# T-Bears CLI

T-Bears fournit une interface en ligne de commande pour interagir avec le réseau d'ICON. Elle implémente toutes les APIs JSON-RPC v3.

Pour un guide détaillé de son utilisation, merci de regarder le [tutorial de T-Bears](https://github.com/icon-project/t-bears/blob/master/README.md). 
Pour interagir avec le réseau d'ICON, vous n'avez pas besoin de `start` le service T-Bears.
A la place, utilisez l'option `-u` pour pointer avec le réseau d'ICON, ou mettez à jour la valeur "uri" de votre fichier de configuration tbears_cli_config.json.
Les commandes en ligne de commande listées ci-dessous sont disponibles après une installation de T-Bears.

```console
$ tbears [-h] [-d] commande ...
```

| commande | description |
|-------|-------|
| keystore | Créer un fichier keystore. Génère une pair de clés privée et publique à l'aide de la bibliothèque secp256k1. |
| deploy | Deploie le SCORE. |
| scoreapi | Demande la liste des APIs qui sont exposées par le SCORE donné. |
| call | Requête icx_call en utilisant le fichier json donné en entrée utilisateur. |
| sendtx | Requête icx_sendTransaction en utilisant le fichier json donné en entrée utilisateur. |
| txresult | Récupère le résultat d'une transaction à l'aide du hash d'une transaction. |
| txbyhash | Récupère les informations d'une transaction à l'aide du hash d'une transaction. |
| transfer | Transfère des ICX. |
| balance | Récupère le solde d'une addresse donnée en loop. |
| totalsupply | Demande la réserve total d'ICX en loop. |
| lastblock | Récupère les informations du dernier bloc. |
| blockbyheight | Récupère les informations d'un bloc grâce à la hauteur du bloc. |
| blockbyhash | Récupère les informations d'un bloc grâce au hash du bloc. |

## Se familiariser avec T-Bears
Nous installerons T-Bears en utilisant Docker, et on enverra quelques requêtes au testnet.

- Installez docker. [[Débuter avec Docker](https://www.docker.com/get-started)]
- Installez et lancez T-Bears avec Docker.
La commande ci-dessous téléchargera l'image et lancera le container.
A l'exécution, les fichiers de configuration et le keystore du compte de test seront générés.
Notez que vous ne devriez pas utiliser le keystore généré pour signer vos transactions sur les autres réseaux,
utilisez seulement le fichier `keystore_test1` sur l'environnement local de T-Bears.

```bash
$ docker run -it --name tbears-container -p 9000:9000 iconloop/tbears
Unable to find image 'iconloop/tbears:latest' locally
latest: Pulling from iconloop/tbears
124c757242f8: Pull complete 
2ebc019eb4e2: Pull complete 
dac0825f7ffb: Pull complete 
82b0bb65d1bf: Pull complete 
ef3b655c7f88: Pull complete 
abf2199d9d18: Pull complete 
dd59e2bf156a: Pull complete 
02a056d8159d: Pull complete 
Digest: sha256:cbec58fc5ae717934b510794d54bab6095de03eac013cc89a34ab1a8899e1813
Status: Downloaded newer image for iconloop/tbears:latest
 * Starting RabbitMQ Messaging Server rabbitmq-server                    [ OK ] 
Made tbears_cli_config.json, tbears_server_config.json, keystore_test1 successfully
Started tbears service successfully
root@b65c6a4cccf8:/tbears#
```

Nous allons maintenant interagir avec le testnet.
[[Testnet Information](icon_network-fr.md#testnet-for-dapps)].
Vous pouvez passer le point d'accès de l'API du noeud avec l'option -u pour faire en sorte que T-Bears communique avec le bon noeud.
Pour des raisons de simplicité, nous invoquerons des méthodes qui ne requiert pas de keystore.
- Exemple de requête 1 : Récupérer le block de génèse du testnet. Vous pouvez vérifier l'ID du réseau dans le message répondu.
Faîtes attention à ce que la hauteur du bloc soit en hexadécimal.

```bash
root@b65c6a4cccf8:/tbears# tbears blockbyheight -u https://bicon.net.solidwallet.io/api/v3 0x0
block info : {
    "jsonrpc": "2.0",
    "result": {
        "version": "0.1a",
        "prev_block_hash": "",
        "merkle_tree_root_hash": "831da0a095133e4a0dfd7b6527e8d1851a474dfface9748ec2fe2c6464d345ed",
        "time_stamp": 0,
        "confirmed_transaction_list": [
            {
                "message": "{X}iconNet",
                "accounts": [
                    {
                        "balance": "0x2961fff8ca4a62327800000",
                        "name": "god",
                        "address": "hx5a05b58a25a1e5ea0f1d5715e1f655dffc1fb30a"
                    },
                    {
                        "balance": "0x0",
                        "name": "treasury",
                        "address": "hx1000000000000000000000000000000000000000"
                    },
                    {
                        "balance": "0x2961fff8ca4a62327800000",
                        "name": "test_1",
                        "address": "hx6e1dd0d4432620778b54b2bbc21ac3df961adf89"
                    }
                ],
                "nid": "0x3"
            }
        ],
        "block_hash": "974e2a8fbefad82b30af0ebfd9939314a53cd7ce3c54b19079b59501122987fe",
        "height": 0,
        "peer_id": "",
        "signature": ""
    },
    "id": 1
}

```

- Exemple de requête 2 : Requêter les APIs du [SCORE de Gouvernance](https://github.com/icon-project/governance/blob/master/README.md).

```bash
root@b65c6a4cccf8:/tbears# tbears scoreapi -u https://bicon.net.solidwallet.io/api/v3 cx0000000000000000000000000000000000000001
SCORE API: [
    {
        "type": "function",
        "name": "acceptScore",
        "inputs": [
            {
                "name": "txHash",
                "type": "bytes"
            }
        ],
        "outputs": []
    },
    {
        "type": "function",
        "name": "addAuditor",
        "inputs": [
            {
                "name": "address",
                "type": "Address"
            }
        ],
        "outputs": []
    },
    ...
```

- Exemple de requête 3 : Vérifions le coût courant des steps pour chaque opération. 

```bash
root@b65c6a4cccf8:/tbears# cat stepcost.json 
{
    "jsonrpc": "2.0",
    "id": 1,
    "method": "icx_call",
    "params": {
        "to": "cx0000000000000000000000000000000000000001",
        "dataType": "call",
        "data": {
            "method": "getStepCosts"
        }
    }
}
root@b65c6a4cccf8:/tbears# tbears call -u https://bicon.net.solidwallet.io/api/v3 stepcost.json
response : {
    "jsonrpc": "2.0",
    "result": {
        "default": "0x186a0",
        "contractCall": "0x61a8",
        "contractCreate": "0x3b9aca00",
        "contractUpdate": "0x5f5e1000",
        "contractDestruct": "-0x11170",
        "contractSet": "0x7530",
        "get": "0x0",
        "set": "0x140",
        "replace": "0x50",
        "delete": "-0xf0",
        "input": "0xc8",
        "eventLog": "0x64",
        "apiCall": "0x0"
    },
    "id": 1
}
```

---
[Document de référence](https://github.com/icon-project/icon-project.github.io/tree/b876874deb842cd062059f813ad5918d95d16053)