# Partie 2. HelloWorld sur le testnet

Dans cette partie 2, nous déployerons le SCORE HelloWorld sur le testnet. Vous apprendrez comment configurer vos outils pour interagir avec le testnet.

## Configurer ICONex pour se connecter au testnet

Si vous n'avez pas installé le portefeuille d'ICON sous Chrome, voici le [lien](https://chrome.google.com/webstore/detail/iconex/flpiciilemghbmfalicajoolhkkenfel) du magasin en ligne de Chrome.

Le portefeuille Chrome d'ICON est connecté au mainnet. Vous pouvez passer d'un réseau à l'autre en changeant sa configuration.
- Ouvrez les outils de développement de Chrome en appuyant sur F12, puis allez dans l'onglet **Application**. Dans la section **Storage**, déroulez **Local Storage**.
- Ajoutez une nouvelle pair clé/valeur, **isDev/true**, en cliquant dans la ligne vide en bas de la table.
- Rechargez votre portefeuille, et vous verrez un menu en bas. Cliquez sur le bouton **ICX (SERVER)** afin d'ouvrir une liste déroulante des réseaux disponibles. Sélectionnez le réseau **YEOUIDO**, qui est le testnet pour les développeurs de DApp.

Veuillez vous référer au [guide du Réseau d'ICON](icon_network-fr.md) pour de plus amples informations.

## Créez un compte dans ICONex

Créez un compte sur ICONex, et téléchargez le fichier de keystore. Un guide complet pour créer un fichier de keystore est situé [ici](wallet-fr.md#create-an-account).

Nous allons utiliser ce fichier de keystore sur le testnet. Le fichier keystore téléchargé depuis ICONex ressemblera à quelque chose similaire à `UTC--2018-10-06T06_00_02.195Z--hxbac99ffea54749ca1c86ab4e6bfe0b630bf7a7a0`. Comme vous avez pu remarqué, la partie à la fin du nom du fichier est votre adresse, `hxbac99ffea54749ca1c86ab4e6bfe0b630bf7a7a0` dans cet exemple. Renommons ce fichier `keyfile_test2` pour qu'il soit plus humainement lisible.

## Obtenir des ICX de test

Vous avez besoin d'ICX pour déployer et invoquer un SCORE sur le testnet, car chaque transaction sur le testnet génère des frais de transaction. Pour recevoir des ICX sur le testnet, merci d'envoyer un email à `testicx@icon.foundation` avec les informations suivantes.
- URL du nœud testnet
- Addresse de réception des ICX de testnet. Il s'agit d'une chaîne de caractères débutant par `hx`.

Vérifiez votre solde depuis la ligne de commande, ou sur ICONex.

```console
# tbears -u https://bicon.net.solidwallet.io/api/v3 balance [compte]
```

## Frais de transaction

[Les frais de transactions détaillés](step-fr.md).  

- `stepLimit` dans le message de la requête de transaction.

  Chaque message de requête de transaction dispose d'un champ `stepLimit`. Cette valeur ne peut excéder la "step limite maximum" définie dans le SCORE de Gouvernance.

  ```
  {
      "jsonrpc": "2.0",
      "method": "icx_sendTransaction",
      "id": 1234,
      "params": {
          "version": "0x3",
          "from": "hxe7af5fcfd8dfc67530a01a0e403882687528dfcb",
          "to": "cx9a4c4229ab2cbd61a5cc051fbbb6ee7e3e3adfac",
          "stepLimit": "0x123450",
           ...
  ```

- `stepUsed` dans le résultat de la transaction

  Vous avez peut être déjà remarqué que chaque transaction retournait un champ `stepUsed`. C'est en fait le montant de step consommé par la transaction. C'est une bonne habitude de regarder dans T-Bears le montant de step utilisé par chaque transaction. Sur T-Bears, le step est effectivement calculé mais le prix du step est mis à zéro, donc les frais de transaction seront toujours de zéro. T-Bears ne retournera une erreur `Out of step` seulement si l'utilisation de step dépasse la valeur du `stepLimit` définit dans votre requête de transaction.

  ```
  {
      "jsonrpc": "2.0",
      "result": {
          "txHash": "0xc40cbbf2b89cd1e2890....",
          "blockHeight": "0x3158",
          "blockHash": "0x9e0c1385128bf0d4257....",
           ...
          "stepUsed": "0x4d361d0",
           ...
  ```

- Erreur `Out of step` dans le résultat de la transaction

  Lorsque vous recevez une erreur `Out of step`, augmentez le `stepLimit` de votre message de requête. La valeur doit être plus grande que celle de `stepUsed` (step utilisé).

  ```
  {
      "jsonrpc": "2.0", 
      "result": {
          "txHash": "0x9f512fe4b431d8780d75c29ad307f3....", 
          ...
          "stepUsed": "0x4d361d0",
          "status": "0x0", 
          "failure": {
              "code": "0x7d64", 
              "message": "Out of step: contractSet"
          }
          ...
  ```


## Configurer T-Bears afin de voir le testnet

Nous allons modifier le fichier de configuration par défaut de la CLI, "tbears_cli_config.json", pour le testnet. Dans ce fichier de configuration, nous modifierons les champs `uri`, `nid`, et `stepLimit`.
Chaque commande utilisée dans la CLI consultera le champ `uri` du fichier de configuration si l'option `-u` n'est pas utilisée. La command `deploy` utilise les champs `nid` et `stepLimit` dans ce fichier.

```
{
    "uri": "https://bicon.net.solidwallet.io/api/v3",
    "nid": "0x3",
    "keyStore": null,
    "from": "hxe7af5fcfd8dfc67530a01a0e403882687528dfcb",
    "to": "cx0000000000000000000000000000000000000000",
    "stepLimit": "0x5000000",
    ...
}
```

## Déployer un contrat HelloWorld sur le testnet (T-Bears CLI)

Contrairement à l'environnement émulé de T-Bears, sur le testnet, une signature valide est requise afin d'envoyer une transaction. Par conséquent, chaque requête de transaction aura besoin d'un fichier de keystore pour la signer.


La commande `tbears deploy` accompagnée de l'option `-k [keystore_file]` installera votre SCORE sur le testnet. Les informations du réseau ciblé (uri, nid) sont lues depuis le fichier de configuration par défaut. N'oubliez pas de récupérer l'adresse du SCORE depuis le résultat de la transaction.

```console
root@07dfee84208e:/tbears# tbears deploy hello_world -k keystore_test2
root@07dfee84208e:/tbears# tbears txresult [txhash]
```

## Exécuter le contrat HelloWorld (T-Bears CLI, Python)

#### T-Bears CLI

La même commande invoquera la méthode `hello` sur le testnet. Les fonctions lecture-seule n'ont pas besoin d'un fichier de keystore. L'`uri` du testnet est lue depuis le fichier de configuration par défaut. Vérifiez seulement que vous avez mis à jour correctement les valeurs `to` et `from` dans le message de requête "call.json".

```console
root@07dfee84208e:/tbears# tbears call call.json

root@07dfee84208e:/tbears# cat call.json 
{
    "jsonrpc": "2.0",
    "method": "icx_call",
    "id": 1,
    "params": {
        "from": "hxbac99ffea54749ca1c86ab4e6bfe0b630bf7a7a0",  <-- Addresse test2 
        "to": "cx9a4c4229ab2cbd61a5cc051fbbb6ee7e3e3adfac",  <-- Addresse SCORE sur le testnet 
        "dataType": "call", 
        "data": {
            "method": "hello" 
        }
    }
}
```


#### Python SDK

Nous allons créer un fichier "hello.py" qui invoquera la méthode `hello` du contrat sur le testnet. Utilisez l'adresse du SCORE et le mot de passe du keystore dans votre code.

```python
from iconsdk.icon_service import IconService
from iconsdk.providers.http_provider import HTTPProvider
from iconsdk.wallet.wallet import KeyWallet
from iconsdk.builder.call_builder import CallBuilder

node_uri = "https://bicon.net.solidwallet.io/api/v3"
network_id = 3
hello_world_address = "__SCORE_ADDRESS__"
keystore_path = "./keystore_test2"
keystore_pw = "__PASSWORD__"

wallet = KeyWallet.load(keystore_path, keystore_pw)
tester_addr = wallet.get_address()
icon_service = IconService(HTTPProvider(node_uri))

call = CallBuilder().from_(tester_addr)\
                    .to(hello_world_address)\
                    .method("hello")\
                    .build()

result = icon_service.call(call)
print(result)
```

Puisque vous n'avez pas besoin de signer les requêtes aux fonctions lecture-seule, il n'est pas nécessaire de créer une instance de portefeuille depuis un fichier de keystore.

Vous pouvez maintenant lancer le code. 

```console
$ python3 test.py
Hello, hxbac99ffea54749ca1c86ab4e6bfe0b630bf7a7a0. My name is HelloWorld
```

---
[Document de référence](https://github.com/icon-project/icon-project.github.io/tree/3c4d77ced348bc5ea801eb61f55b5ac79e805ebd)
