# Comment écrire un SCORE
Ce document explique les critères de l'audit d'ICON and suggère des pratiques d'implémentation sécurisées d'un SCORE.
La liste des choses à vérifier lors de l'audit est constituée de 2 niveaux de sévérité, Critique et Avertissement.
Les résultats de l'audit seront rendus avec la mention Pass/Fail/NA (Admis/Echec/Non Annoncé) pour chaque élément Critique, et Pass/Warning/NA pour chaque élément Avertissement.
Si le moindre élément Critique est déterminé comme étant "Fail", le déploiement du SCORE sera rejeté.

Ci-dessous se trouve la liste des choses à vérifier groupées par sévérité.
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


## Délai d'attente
Une fonction d'un SCORE doit retourner aussi vite que possible. La blockchain n'est pas faite pour des opérations qui durent longtemps.
Par exemple, si vous implémentez un airdrop pour beaucoup d'utilisateurs, n'itérez pas sur tous les utilisateurs dans une seule fonction. Gérez plutôt chaque airdrop un par un ou partiellement.

```python

# Mauvais
@external
def airDropToken(self, _value: int, _data: bytes = None) -> bool:
  for target in self._very_large_targets:
    self._transfer(self.msg.sender, target, _value, _data)

# Bon
@external
def airDropToken(self, _to: Address, _value: int, _data: bytes = None) -> bool:
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
  // faire quelque chose

# Bien
i = 0
while i < 10:
  // faire quelque chose
  i+= 1
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
Si les nœuds ne peuvent atteindre un consensus, toutes les transactions du bloc seront perdues.
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
def transfer(self, _to: Address, _value: int, _data: bytes = None) -> bool:
    self._balances[self.msg.sender] -= _value
    self._balances[_to] += _value
    self.Transfer(self.msg.sender, _to, _value, _data)
    return True
```

## Eventlog sans Transfert de Token
Ne déclenchez pas d'Eventlog lorsqu'il n'y a pas de transfert de token.
```python
# Mauvais
@eventlog(indexed=3)
def Transfer(self, _from: Address, _to: Address, _value: int, _data: bytes):
    pass

@external
def transfer(self, _to: Address, _value: int, _data: bytes = None) -> bool:
    // Pas de transfert de token
    self.Transfer(self.msg.sender, _to, _value, _data)
    return True
```

## Eventlog ICXTransfer
L'Eventlog "ICXTransfer" est réservé pour des transferts d'ICX. N'implémentez pas d'Eventlog avec le même nom.
```python
# Mauvais
@eventlog(indexed=3)
def ICXTransfer(self, _from: Address, _to: Address, _value: int):
```

## Vérification de Paramètre de Fonction Externe
Si une fonction SCORE est appelée depuis un EOA avec les mauvais types pour les paramètres ou un paramètre manquant, le service d'ICON retournera de lui même une erreur.
Les développeurs n'ont pas besoin de vérifier délibérément les paramètres d'entrée à l'intérieur d'une fonction.

## Vérification de Paramètre de Fonction Interne
Si un SCORE appelle d'autres fonctions faisant parties de leur propre SCORE ou d'un autre SCORE, vérifiez toujours que les types sont corrects et que les paramètres requis sont présents.
Les valeurs des paramètres doivent être dans une portée valide.
Il n'y a pas de taille limite pour les `str` et les `int` en Python, cependant, le message de la transaction ne doit pas excéder 512Ko.

```python
# Mauvais
def myTransfer(_value: int) -> bool:
    ...
myTransfer("1000")

# Bien
def myTransfer(_value: int) -> bool:
    ...
myTransfer(1000)

# Mauvais
def myTransfer(_value: int, _extra: str) -> bool:
    ...
myTransfer(1000)

# Bien
def myTransfer(_value: int, _extra: str) -> bool:
    ...
myTransfer(1000, 'abc')
```

## Arbitrarité prévisible
Certaines applications comme les lotteries ont besoin d'arbitrarité. A cause de la nature de la blockchain, l'implémentation d'une telle logique doit être effectuée avec beaucoup d'attention.
La sortie d'un générateur de nombre pseudo aléatoire peut être prévisible si la graine aléatoire (*seed*) est révélée.
```python
# Mauvais
# La hauteur d'un bloc est prévisible.
won = block.height % 2 == 0
```

---
[Document de référence](https://github.com/icon-project/icon-project.github.io/blob/261dd6572654f461faf1ee886ffb791ee8475346/docs/audit_checklist.md)