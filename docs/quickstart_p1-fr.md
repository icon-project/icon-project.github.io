# Partie 1. HelloWorld sur un environnement émulé local

## Installer T-Bears (Docker)

- Installez Docker [[Démarrer avec Docker](https://www.docker.com/get-started)]

- Installez T-Bears et exécutez le container.

La commande ci-dessous téléchargera une image Docker de T-Bears, créera un container, démarrera le container, et attachera votre stdin/stderr au container.

```console
$ docker run -it --name local-tbears -p 9000:9000 iconloop/tbears
 * Starting RabbitMQ Messaging Server rabbitmq-server                    [ OK ]
Made tbears_cli_config.json, tbears_server_config.json, keystore_test1 successfully
Started tbears service successfully
root@c5b81f9874ee:/tbears#
```

Quitter et stopper le container.

```console
root@07dfee84208e:/tbears# exit
```

Lister les containers disponibles.

```console
$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS                    NAMES
c5b81f9874ee        iconloop/tbears     "entry.sh"          6 minutes ago       Up 6 minutes               0.0.0.0:9000->9000/tcp   local-tbears
```

Démarrer et s'attacher à un container, ou s'attacher à un container déjà démarré.

```console
$ docker container start -a local-tbears
root@07dfee84208e:/tbears#

$ docker container attach local-tbears
root@07dfee84208e:/tbears#
```

## Compte de test

Le fichier keystore de test, `keystore_test`, vous est fourni pour simplifier le processus. Attention de ne pas utiliser ce fichier de keystore afin de signer vos transactions sur un autre réseau autre que T-Bears, car son mot de passe est ouvert à tout le monde publiquement.

- Addresse : hxe7af5fcfd8dfc67530a01a0e403882687528dfcb
- Mot de passe : test1_Account
- Solde ICX : 0x2961fff8ca4a62327800000

## Vérifier le solde de votre compte

Lançons une commande `tbears balance` afin de vérifier votre solde.

```console
root@07dfee84208e:/tbears# tbears balance hxe7af5fcfd8dfc67530a01a0e403882687528dfcb
balance in hex: 0x2961fff8ca4a62327800000
balance in decimal: 800460000000000000000000000
```


## Créer un contrat HelloWorld et le déployer

La commande `tbears init` initialisera un projet. 

```console
root@07dfee84208e:/tbears# tbears init hello_world HelloWorld
Initialized tbears successfully

root@07dfee84208e:/tbears# ls hello_world
hello_world.py  __init__.py  package.json

```

Le fichier `hello_world.py` contient l'implémentation d'un modèle SCORE complètement fonctionnel.

```python
from iconservice import *

class HelloWorld(IconScoreBase):

    def __init__(self, db: IconScoreDatabase) -> None:
        super().__init__(db)

    def on_install(self) -> None:
        super().on_install()

    def on_update(self) -> None:
        super().on_update()

    @external(readonly=True)
    def hello(self) -> str:
        return "Hello"
```

Déployons ce contrat sans y apporter de modification et récupérons l'adresse du contrat. Pour chaque transaction, vous devez vérifier que le résultat de l'exécution en utilisant son txhash. Le résultat de la transaction contient l'adresse du SCORE si le déploiement s'est bien déroulé.

```console
root@07dfee84208e:/tbears# tbears deploy hello_world
Send deploy request successfully.
If you want to check SCORE deployed successfully, execute txresult command
transaction hash: 0xc40cbbf2b89cd1e2890132145e6d86ad61835edaca0bcc3a4c34b5cb22b8be28

root@07dfee84208e:/tbears# tbears txresult 0xc40cbbf2b89cd1e2890132145e6d86ad61835edaca0bcc3a4c34b5cb22b8be28
Transaction result: {
    "jsonrpc": "2.0",
    "result": {
        "txHash": "0xc40cbbf2b89cd1e2890132145e6d86ad61835edaca0bcc3a4c34b5cb22b8be28",
        "blockHeight": "0x3158",
        "blockHash": "0x9e0c1385128bf0d425773f9f9130d683d327a058a9c8dc0a6c4df71bb98195e1",
        "txIndex": "0x0",
        "to": "cx3176b5d6cae66a1abbc3ca9070423a5c708834a9",
        "scoreAddress": "cx3176b5d6cae66a1abbc3ca9070423a5c708834a9", <-- SCORE address
        "stepUsed": "0x4d361d0",
        "stepPrice": "0x0",
        "cumulativeStepUsed": "0x4d361d0",
        "eventLogs": [],
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "status": "0x1"
    },
    "id": 1
}
```

Le SCORE a été déployé avec succès. Nous allons invoquer la méthode `hello` depuis la ligne de commande pour regarder le résultat. Le fichier `call.json` contient le message de la requête. `to` est l'adresse du SCORE, `from` est l'adresse de l'émetteur du message. N'oubliez pas de fournir votre propre adresse du SCORE, celle que vous obtenez avec le déploiement (`tbears deploy`). Pour une documentation complète du formatage des messages JSON, veuillez vous référer à la [spécification ICON JSON-RPC API v3](https://github.com/icon-project/icon-rpc-server/blob/master/docs/icon-json-rpc-v3.md).

```console
root@07dfee84208e:/tbears# tbears call call.json
response : {
    "jsonrpc": "2.0",
    "result": "Hello",
    "id": 1
}
root@07dfee84208e:/tbears# cat call.json 
{
    "jsonrpc": "2.0",
    "method": "icx_call",
    "id": 1,
    "params": {
        "from": "hxe7af5fcfd8dfc67530a01a0e403882687528dfcb",
        "to": "cx3176b5d6cae66a1abbc3ca9070423a5c708834a9",
        "dataType": "call", 
        "data": {
            "method": "hello" 
        }
    }
}
```

## Modifier le contrat HelloWorld pour qu'il vous réponde

Cela serait plus agréable d'avoir un SCORE HelloWorld plus accueillant. Donnons lui un nom, et faisons en sorte qu'il nous salue de manière plus sympathique. Une nouvelle méthode sera ajoutée, et la méthode `hello` sera modifiée.

- `name` est une fonction à lecture seule, et retourne le nom du SCORE.

- La méthode `hello` est légèrement modifiée afin de vous reconnaître. `msg` est une propriété intégrée dans le système d'ICON qui retient des information sur l'émetteur du message, et le montant d'ICX que l'émetteur essaye de transférer. Ils peuvent être respectivement référencé par `msg.sender` et `msg.value`.

```python
    @external(readonly=True)
    def name(self) -> str:
        return "HelloWorld"
    
    @external(readonly=True)
    def hello(self) -> str:
        return f'Hello, {self.msg.sender}. My name is {self.name()}'
```

Nous aurons besoin de déployer le SCORE à nouveau afin que les changements prennent effet. La commande `deploy` avec les options `-m update` et `-o [addresse SCORE]` mettront à jour le SCORE. La fonctionnalité de mise à jour est unique à ICON. L'adresse du SCORE n'est pas changée après la mise à jour.

```console
root@07dfee84208e:/tbears# tbears deploy -m update -o cx3176b5d6cae66a1abbc3ca9070423a5c708834a9 hello_world
Send deploy request successfully.
If you want to check SCORE deployed successfully, execute txresult command
transaction hash: 0xc412dc9c6685701c8837eddea091283244303d322aa1fba36bc0782e1b483763
...
```

Invoquez la méthode `hello` avec la même requête, et observez les changements.

```console
root@07dfee84208e:/tbears# tbears call call.json
response : {
    "jsonrpc": "2.0",
    "result": "Hello, hxe7af5fcfd8dfc67530a01a0e403882687528dfcb. My name is HelloWorld",
    "id": 1
}
```

---
[Document de référence](https://github.com/icon-project/icon-project.github.io/tree/bdca96e297edfcd204b8d44aae32ecc52a27a932)
