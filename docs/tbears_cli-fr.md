# T-Bears CLI

T-Bears fournit une interface en ligne de commande pour interagir avec le réseau d'ICON. Elle implémente toutes les APIs JSON-RPC v3.

Pour un guide détaillé de son utilisation, merci de regarder le [tutorial de T-Bears](https://github.com/icon-project/t-bears/blob/master/README.md). 
Pour interagir avec le réseau d'ICON, vous n'avez pas besoin de `start` le service T-Bears.
A la place, utilisez l'option `-u` pour pointer avec le réseau d'ICON, ou mettez à jour la valeur "uri" de votre fichier de configuration tbears_cli_config.json.
Ci-dessous est listé les commandes en ligne de commande qui sont disponibles après une installation de T-Bears.

Bien que tous les clients des SDKs exposent les mêmes fonctions, une des commandes particulière de T-Bears est `deploy`.
Si vous voulez déployer votre propre SCORE, vous devez toujours le faire depuis T-Bears.

```console
$ tbears [-h] [-d] commande ...
```

| commande | description |
|-------|-------|
| keystore | Créer un fichier keystore. Génère une pair de clés privée et publique à l'aide de la bibliothèque secp256k1. |
| deploy | Deploie le SCORE. |
| scoreapi | Demande la liste des APIs qui sont exposées par le SCORE donné. |
| call | Requête icx_call en utilisant le fichier json donné en entrée utiliseur. |
| sendtx | Requête icx_sendTransaction en utilisant le fichier json donné en entrée utiliseur. |
| txresult | Récupère le résultat d'une transaction à l'aide du hash d'une transaction. |
| txbyhash | Récupère les informations d'une transaction à l'aide du hash d'une transaction. |
| transfer | Transfère des ICX. |
| balance | Récupère le solde d'une addresse donnée en loop. |
| totalsupply | Demande la réserve total d'ICX en loop. |
| lastblock | Récupère les informations du dernier bloc. |
| blockbyheight | Récupère les informations d'un bloc grâce à la hauteur du bloc. |
| blockbyhash | Récupère les informations d'un bloc grâce au hash du bloc. |

---
[Document de référence](https://github.com/icon-project/icon-project.github.io/blob/861ef5bc09c75a367150b40d89def84c57c0ccc9/docs/tbears_cli.md)