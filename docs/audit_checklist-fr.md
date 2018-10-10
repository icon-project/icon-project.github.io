# Comment écrire un SCORE
Ce document explique les critères de l'audit d'ICON and suggère des pratiques d'implémentation sécurisées d'un SCORE.
La liste des choses à vérifier lors de l'audit est constituée de 2 niveaux de sévérité, Critique et Avertissement.
Les résultats de l'audit seront rendus avec la mention Pass/Fail/NA (Admis/Echec/Non Annoncé) pour chaque élément Critique, et Pass/Warning/NA pour chaque élément Avertissement.
Si le moindre élément Critique est déterminé comme étant "Fail", le déploiement du SCORE sera rejeté.

Ci-dessous se trouve la liste des choses à vérifier groupées par sévérité.
Nous supposerons que vous avez lu [ceci](https://github.com/icon-project/icon-service/blob/master/docs/dapp_guide.md), et que vous comprenez les bases du développement de SCORE.

## Niveau de sévérité
### Critique
- [Délai d'attente](#timeout)
- [Boucle infinie](#unfinishing-loop)
- [Import de paquet](#package-import)
- [Appel système](#system-call)
- [Aléatoire](#randomness)
- [Appel réseau vers l'extérieur](#outbound-network-call)
- [Conformité avec le standard du Token IRC2](#irc2-token-standard-compliance)
- [Nom des paramètres du Token IRC2](#irc2-token-parameter-name)
- [Eventlog lors d'un Transfert de Token](#eventlog-on-token-transfer)
- [Eventlog sans Transfert de Token](#eventlog-without-token-transfer)
- [Eventlog ICXTransfer](#icxtransfer-eventlog)

### Avertissement
- [Vérification de Paramètre de Fonction Externe](#external-function-parameter-check)
- [Vérification de Paramètre de Fonction Interne](#internal-function-parameter-check)
- [Arbitrarité prévisible](#predictable-arbitrarity)
- [Appels bas niveau non vérifiés](#unchecked-low-level-calls)
- [Super Classe](#super-class)
- [Dépassement de capacité](#underflowoverflow)
- [Coffre fort](#vault)
- [Réentrance](#reentrancy)

# Critique
## Délai d'attente
Une fonction d'un SCORE doit retourner aussi vite que possible. La blockchain n'est pas faite pour des opérations qui durent longtemps.
Par exemple, si vous implémentez un airdrop pour beaucoup d'utilisateurs, n'itérez pas sur tous les utilisateurs dans une seule fonction. Gérez plutôt chaque airdrop un par un ou partiellement.

```python

# Mauvais
@external
def airDropToken(self, _value: int, _data: bytes = None):
  for target in self._very_large_targets:
    self._transfer(self.msg.sender, target, _value, _data)

# Bon
@external
def airDropToken(self, _to: Address, _value: int, _data: bytes = None):
  if self._airdrop_sent_address[_to]:
     self.revert(f"Token was dropped already: {_to}")

  self._airdrop_sent_address[_to] = True
  self._transfer(self.msg.sender, _to, _value, _data)
```

## Boucle infinie
Utilisez les mots clés `for` et `while` avec minutieusement. Soyez sûr que le code atteint toujours une condition de sortie.
Si l'opération à l'intérieur de la boucle consomme un `step`, le programme s'arrêtera à un moment donné.
Cependant, si le block de code à l'intérieur de la boucle ne consomme pas de `step`, par exemple, des appels de fonctions Python natives, alors le programme tournera dans la boucle infiniment.
Le réseau d'ICON achèvera de force la tâche qui ne répond plus, mais cela pourrait quand même dégrader le réseau d'ICON significativement.

```python
# Mauvais
while True:
  // faire quelque chose sans consommer de 'step' ou de condition de sortie

# Bien
i = 0
while i < 10:
  // faire quelque chose
  i += 1
```

## Import de paquet
SCORE doit être exécuté dans un environnement cloisonné.
L'import de paquet est interdit à l'exception de `iconservice` et des fichiers présent dans l'arborescence des fichiers de votre SCORE.

```python
# Mauvais
import os

# Bien
from iconservice import *
from .myclass import *
```

## Appel système
Les appels systèmes sont interdits. SCORE ne peut accéder à toute ressource système.

```python
# Mauvais
import os
os.uname()
```

## Aléatoire
Le résultat de l'exécution d'un SCORE doit être déterministe. Sans cela, les nœuds ne peuvent atteindre un consensus.
Si les nœuds ne peuvent atteindre un consensus à cause d'un résultat non déterministe, toutes les transactions du bloc seront perdues.
Par conséquent, non seulement les fonctions aléatoires, mais toute tentative qui empêcherait la génération d'un bloc par une opération indéterminéee est strictement interdite.

```python
# Mauvais
# Chaque nœud aura un résultat différent
won = datetime.datetime.now() % 2 == 0
```

## Appel réseau vers l'extérieur 
Les appels réseaux vers l'extérieur sont interdits. Le résultat d'un tel appel depuis chaque nœud peut être différent.

```python
# Mauvais
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(host, port)
```

## Conformité avec le standard du Token IRC2
Un token IRC2 conforme doit implémenter toutes les fonctions de la spécification. [Standard du Token IRC2 d'ICON](https://github.com/icon-project/IIPs/blob/master/IIPS/iip-2.md)

```python
# Fonctions IRC2
@external(readonly=True)
def name(self) -> str:

@external(readonly=True)
def symbol(self) -> str:

@external(readonly=True)
def decimals(self) -> int:

@external(readonly=True)
def totalSupply(self) -> int:

@external(readonly=True)
def balanceOf(self, _owner: Address) -> int:

@external
def transfer(self, _to: Address, _value: int, _data: bytes=None):
```

## Nom des paramètres du Token IRC2
Lors de l'implémentation d'un Token IRC2 conforme, gardez le même nom pour les paramètres des fonctions 
tel que défini dans le [Standard du Token IRC2 d'ICON](https://github.com/icon-project/IIPs/blob/master/IIPS/iip-2.md).

```python
# Mauvais
def balanceOf(self, owner: Address) -> int:

# Bien
def balanceOf(self, _owner: Address) -> int:
```

## Eventlog lors d'un Transfert de Token
Les transferts de Token doivent déclencher un log d'évènement (*Eventlog*).
```python
# Bien
@eventlog(indexed=3)
def Transfer(self, _from: Address, _to: Address, _value: int, _data: bytes):
    pass

@external
def transfer(self, _to: Address, _value: int, _data: bytes = None):
    self._balances[self.msg.sender] -= _value
    self._balances[_to] += _value
    self.Transfer(self.msg.sender, _to, _value, _data)
```

## Eventlog sans Transfert de Token
Ne déclenchez pas d'Eventlog lorsqu'il n'y a pas de transfert de token.
```python
# Mauvais
@eventlog(indexed=3)
def Transfer(self, _from: Address, _to: Address, _value: int, _data: bytes):
    pass

@external
def doSomething(self, _to: Address, _value: int):
    // Pas de transfert de token
    self.Transfer(self.msg.sender, _to, _value, None)
```

## Eventlog ICXTransfer
L'Eventlog "ICXTransfer" est réservé pour des transferts d'ICX. N'implémentez pas d'Eventlog avec le même nom.
```python
# Mauvais
@eventlog(indexed=3)
def ICXTransfer(self, _from: Address, _to: Address, _value: int):
```

# Avertissement
## Vérification de Paramètre de Fonction Externe
Si une fonction SCORE est appelée depuis un EOA avec les mauvais types pour les paramètres ou un paramètre manquant, le service d'ICON retournera de lui même une erreur.
Les développeurs n'ont pas besoin de vérifier délibérément les paramètres d'entrée à l'intérieur d'une fonction.

## Vérification de Paramètre de Fonction Interne
Si un SCORE appelle d'autres fonctions faisant parties de leur propre SCORE ou d'un autre SCORE, vérifiez toujours que les types sont corrects et que les paramètres requis sont présents.
Les valeurs des paramètres doivent être dans une portée valide.
Il n'y a pas de taille limite pour les `str` et les `int` en Python, cependant, le message de la transaction ne doit pas excéder 512 Ko.

```python
# Déclaration des fonctions
def myTransfer(_value: int) -> bool:
def myTransfer1(_value: int, _extra: str) -> bool:

# Mauvais
myTransfer("1000")
myTransfer1(1000)

# Bien
myTransfer(1000)
myTransfer1(1000, 'abc')
```


## Arbitrarité prévisible
Certaines applications comme les lotteries ont besoin d'arbitrarité. A cause de la nature de la blockchain, l'implémentation d'une telle logique doit être effectuée avec beaucoup d'attention.
La sortie d'un générateur de nombre pseudo aléatoire peut être prévisible si la graine aléatoire (*seed*) est révélée.
```python
# Mauvais
# La hauteur d'un bloc est prévisible.
won = block.height % 2 == 0
```

## Appels bas niveau non vérifié
Dans le cas où l'on envoie des ICX en appelant une fonction bas niveau telle que 'icx.send', vous devez vérifier le résultat de l'exécution de 'icx.send' et gérer le cas d'erreur proprement. 'icx.send' retourne un booléen en résultat de son exécution, et ne lève pas d'exception en cas d'échec. En contrepartie, 'icx.transfer' lève une exception si la transaction échoue. Si le SCORE ne gère pas l'exception, la transaction sera annulée. [Référence: *An object used to transfer icx coin*]( https://github.com/icon-project/icon-service/blob/master/docs/dapp_guide.md#icx--an-object-used-to-transfer-icx-coin)

```python

# Mauvais
self._refund_icx_amount[_to] += amount
self.icx.send(_to)

# Bien
self._refund_icx_amount[_to] += amount
if not self.icx.send(_to, amount):
    self._refund_icx_amount[_to] -= amount

# Bien
self._refund_icx_amount[_to] += amount
self.icx.transfer(_to, amount)
```

## Super Classe
Dans la classe principale de votre SCORE qui hérite de IconScoreBase, vous devez appeler super().\_\_init\_\_() dans la fonction \_\_init\_\_() afin d'initialiser la database des états. De la même manière, super().on_install() doit être appelé dans la fonction on_install() et super().on_update() doit être appelé dans la fonction on_update().

```python
# Mauvais
class MyClass(IconScoreBase):
    def __init__(self, db: IconScoreDatabase) -> None:
        self._context__name = VarDB('context.name', db, str)
        self._context__cap = VarDB('context.cap', db, int)

    def on_install(self, name: str, cap: str) -> None:
        # Faire quelque chose

    def on_update(self) -> None:
        # Faire quelque chose

# Bien
class MyClass(IconScoreBase):
    def __init__(self, db: IconScoreDatabase) -> None:
        super().__init__(db)
        self._context__name = VarDB('context.name', db, str)
        self._context__cap = VarDB('context.cap', db, int)

    def on_install(self, name: str, cap: str) -> None:
        super().on_install()
        # Faire quelque chose

    def on_update(self) -> None:
        super().on_update()
        # Faire quelque chose
```

## Dépassement de capacité
Lorsque vous effectuez des opérations arithmétiques, il est vraiment important de valider que les opérandes et les résultats sont dans la portée désirée.

```python
# Mauvais
@external
def mintToken(self, _amount: int):
    if not msg.sender == self.owner:
        self.revert('Only owner can mint token')

    # Si _amount est en dessous de zéro, self._balances[self.owner] et self._total_supply peuvent potentiellement devenir négatif
    self._balances[self.owner] = self._balances[self.owner] + _amount
    self._total_supply.set(self._total_supply.get() + _amount)

    self.Transfer(EOA_ZERO, self.owner, _amount, b'mint')

# Bien  
@external
def mintToken(self, _amount: int):
    if not msg.sender == self.owner:
        self.revert('Only owner can mint token')
    if _amount <= 0:
        self.revert('_amount should be greater than 0')

    self._balances[self.owner] = self._balances[self.owner] + _amount
    self._total_supply.set(self._total_supply.get() + _amount)

    self.Transfer(EOA_ZERO, self.owner, _amount, b'mint')
```

## Coffre fort
N'importe qui peut consulter les données qui sont sur le réseau de la blockchain publique. Il est recommandé de ne pas sauvegarder de données personnelles tels que des mots de passe sur le réseau de la blockchain, même s'il est chiffré.

```python
# Mauvais
def changePassword(self, _account: Account, _passwd: str):
    if msg.sender != _account:
        self.revert('Only owner of the account can change password')

    self.passwords[_account] = _passwd
```

## Réentrance
Quand vous envoyez des ICX ou des tokens, gardez à l'esprit que la cible pourrait être un SCORE. Si la fonction de repli (*fallback*) est implémentée de manière malveillante, il est pourrait réentrer dans le SCORE original. Ainsi il pourrait y avoir une boucle non voulue entre les deux SCOREs.

```python
# Mauvais
# Fonction de remboursement dans SCORE1 (assumez que le ratio ICX:token est 1:1)
def refund(self, _to:Address, _amount:int):
    if msg.sender != _to:
        self.revert('Only owner of the account can request refund')
    if token_balances[_to] < _amount:
        self.revert('Not enough balance')

    self.icx.transfer(_to, _amount)
    self.token_balances[_to] -= _amount

# Function fallback malveillante dans SCORE2
@payable
def fallback(self):
    is msg.sender == SCORE1_ADDRESS:
        # Appel du remboursement (refund) à nouveau dans SCORE1
        score1 = self.create_interface_score(SCORE1_ADDRESS, Score1Interface)
        score1.refund(self.msg.sender, bigAmountOfICX)

# Bien
# Fonction de remboursement dans SCORE1
def refund(self, _to:Address, _amount:int):
    if msg.sender != _to:
        self.revert('Only owner of the account can request refund')
    if token_balances[_to] < _amount:
        self.revert('Not enough balance')

    # D'abord décrémenter le solde du compte
    self.balances[_to] -= _amount
    self.icx.transfer(_to, _amount)

# Bien
# Fonction de remboursement dans SCORE1
def refund(self, _to:Address, _amount:int):
    if msg.sender != _to:
        self.revert('Only owner of the account can request refund')
    if token_balances[_to] < _amount:
        self.revert('Not enough balance')

    # block if _to is smart contract
    if _to.is_contract:
        self.revert('ICX can not be transferred to SCORE')

    self.icx.transfer(_to, _amount)
    self.balances[_to] -= _amount
```


---
[Document de référence](https://github.com/icon-project/icon-project.github.io/tree/3c4d77ced348bc5ea801eb61f55b5ac79e805ebd)