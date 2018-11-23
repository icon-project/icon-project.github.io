# Introduction

Lorsqu'un utilisateur envoie une transaction, ils ont besoin de signer les données avec leur propre clé privée. Il y a deux étapes majeures qui jouent dans le processus de signature d'une transaction : la sérialisation des données de la transaction d'origine, et la génération de la signature avec la clé privée de l'utilisateur. Ce document décrit le processus de génération de la signature digitale des données d'une transaction. 

- [Serialiser les données d'une transaction](#Serialize-transaction-data)
  - [Précondition](#Precondition)
    - [Types autorisés en JSON](#Allowed-types-in-JSON)
  - [Serialiser](#Serialize)
    - [Type String](#String-type)
    - [Type Dictionnaire](#Dictionary-type)
    - [Type Tableau](#Array-type)
    - [Type Null](#Null-type)
  - [Exemple](#example)
    - [Envoi normal d'une transaction](#Normal-send-transaction)
    - [Invocation d'une API de SCORE](#SCORE-API-Invoke)
- [Création d'une signature de transaction](#Create-transaction-signature)
  - [Données requises](#Required-data)
    - [Clé privée](#Private-key)
    - [Hash de transaction](#Transaction-hash)
  - [Créer une signature](#Create-signature)
  - [Exemple](#example-1)

# Serialiser les données d'une transaction

Avant de signer des données, les données ont besoin d'être sérialisées en bytes
Cette section décrit comment sérialiser les données d'une transaction.
Ce document ne décrira pas comment générer les données d'une transaction elle-même. La spécification du message d'une transaction est définie dans [l'API JSON-RPC v3](https://github.com/icon-project/icon-rpc-server/blob/master/docs/icon-json-rpc-v3.md). 

## Précondition

Les données de transaction sont dans le format JSON avec quelques restrictions.

### Types autorisés en JSON

| Type        | Description                                                          |
| ----------- | -------------------------------------------------------------------- |
| String      | String normale sans le caractère U+0000 (NULL). Exemple : "Value"    |
| Dictionaire | Paires de clé-valeurs. Exemple: {"key1": "value1", "key1": "value2"} |
| Tableau     | Séries de valeurs. Exemple: ["value1", "value2"]                     |
| Null        | null                                                                 |

## Serialiser

ICON suit la spécification du protocole JSON-RPC v2.0. Une requête de transaction signée ressemble à cela: 

```json
{
    "jsonrpc": "2.0",
    "method": "icx_sendTransaction",
    "id": 1234,
    "params": {
        "version": "0x3",
        "from": "hxbe258ceb872e08851f1f59694dac2558708ece11",
        "to": "hx5bfdb090f43a808005ffc27c25b213145e80b7cd",
        "value": "0xde0b6b3a7640000",
        "stepLimit": "0x12345",
        "timestamp": "0x563a6cf330136",
        "nonce": "0x1",
        "signature": "VAia7YZ2Ji6igKWzjR2YsGa2m53nKPrfK7uXYW78QLE+ATehAVZPC40szvAiA6NEU5gCYB4c4qaQzqDh2ugcHgA="
    }
}
```

Les données d'une transaction sont sérialisées en concaténant la clé, les paires de valeurs dans `params` avec `.` comme délimiteur. Notre but final est de générer la `signature` des données d'une transaction, de ce fait, le champ `signature` montré dans l'exemple ci-dessus ne fait pas partie des données à être sérialisées. Afin de compléter le processus de sérialisation, il faut ajouter le nom de la méthode, "icx_sendTransaction" à la chaîne sérialisée en tant que préfixe.

```
icx_sendTransaction.<key1>.<value1>....<keyN>.<valueN>
```

Soyez sûr de vérifier que tous les caractères spécieux dans la string soit encodé en UTF-8.

### Type String

Appliquez l'encodage UTF-8 à votre texte. Les caractères listés ci-dessous doivent être échappés avec `\`. La chaîne de caractères ne doit avoir aucun caractère null, U+0000.

| Nom  | Anti-slash (REVERSE SOLIDUS) | Point (FULL STOP) | Accolade ouvrante | Accolade fermante | Crochet ouvrant | Crochet fermant |
| ----- | --------------------------- | ------------------ | ------------------ | ------------------- | ------------------- | -------------------- |
| Forme | *\\*                        | **.**              | **{**              | **}**               | **[**               | **]**                |
| UTF-8 | 0x5C                        | 0x2E               | 0x7B               | 0x7D                | 0x5B                | 0x5D                 |

### Type Dictionnaire

Le contenu d'un dictionnaire doit être entre les caractères `{` et `}`, et les paires clé/valeur doivent être séparées par `.`. Chaque clé dans un dictionnaire doivent être de type string, ainsi, les mêmes règles d'encodage s'appliquent. L'ordre des clés dans les données sérialisées suivent l'ordre naturel d'une comparaison octet par octet d'une chaîne UTF-8 (pareil que l'ordre en Unicode).

```
{<key1>.<value1>.<key2>.<value2>}
```

Exemple:

```json
{
       "version": "0x3",
       "from": "hxbe258ceb872e08851f1f59694dac2558708ece11",
       "to": "cxb0776ee37f5b45bfaea8cff1d8232fbb6122ec32",
       "value": "0xde0b6b3a7640000",
       "stepLimit": "0x12345",
       "timestamp": "0x563a6cf330136"
}
```

est sérialisé en

``` 
{from.hxbe258ceb872e08851f1f59694dac2558708ece11.stepLimit.0x12345.timestamp.0x563a6cf330136.to.cxb0776ee37f5b45bfaea8cff1d8232fbb6122ec32.value.0xde0b6b3a7640000.version.0x3}
```

### Type tableau

Le contenu d'un tableau est contenu entre `[` et `]`. Toutes les valeurs sont séparées par un `.`.

``` 
[<value1>.<value2>.<value3>]
```

### Type Null

Null sera représenté par un zéro échappé.

```
\0
```

## Exemple

### Transfert d'ICX

Requête JSON originale :

```json
{
    "jsonrpc": "2.0",
    "method": "icx_sendTransaction",
    "id": 1234,
    "params": {
        "version": "0x3",
        "from": "hxbe258ceb872e08851f1f59694dac2558708ece11",
        "to": "hx5bfdb090f43a808005ffc27c25b213145e80b7cd",
        "value": "0xde0b6b3a7640000",
        "stepLimit": "0x12345",
        "timestamp": "0x563a6cf330136",
        "nonce": "0x1"
    }
}
```

Paramètres sérialisés :

```
icx_sendTransaction.from.hxbe258ceb872e08851f1f59694dac2558708ece11.nonce.0x1.stepLimit.0x12345.timestamp.0x563a6cf330136.to.hx5bfdb090f43a808005ffc27c25b213145e80b7cd.value.0xde0b6b3a7640000.version.0x3
```

### Invocation d'une API de SCORE

Requête JSON d'origine :

```json

{
    "jsonrpc": "2.0",
    "method": "icx_sendTransaction",
    "id": 1234,
    "params": {
        "version": "0x3",
        "from": "hxbe258ceb872e08851f1f59694dac2558708ece11",
        "to": "cxb0776ee37f5b45bfaea8cff1d8232fbb6122ec32",
        "stepLimit": "0x12345",
        "timestamp": "0x563a6cf330136",
        "nonce": "0x1",
        "dataType": "call",
        "data": {
            "method": "transfer",
            "params": {
                "to": "hxab2d8215eab14bc6bdd8bfb2c8151257032ecd8b",
                "value": "0x1"
            }
        }
    }
}
```

Paramètres sérialisés :

```
icx_sendTransaction.data.{method.transfer.params.{to.hxab2d8215eab14bc6bdd8bfb2c8151257032ecd8b.value.0x1}}.dataType.call.from.hxbe258ceb872e08851f1f59694dac2558708ece11.nonce.0x1.stepLimit.0x12345.timestamp.0x563a6cf330136.to.cxb0776ee37f5b45bfaea8cff1d8232fbb6122ec32.version.0x3
 ```

# Création d'une signature de transaction

Cette section décrit comment créer une signature valide pour une transaction en utilisant la clé privée associée avec une adresse spécifique à un wallet.

## Données requises

### Clé privée

La clé privée de l'adresse `from` depuis laquelle la transaction a été faite.

### Hash de transaction

Hash de 32 octets (256-bit) qui a été créé en hashant les données de transaction sérialisés en utilisant SHA3_256.

## Créer une signature

La première étape est de créer une signature sérialisée. En utilisant la librairie `secp256k1`, crééez une signature ECDSA du hash de la transaction. Cela permet de s'assurer que le possesseur de la clé privée est à l'origine de la transaction, et que le message de la transaction reçue par le destinataire n'est pas compromis. Le résultat en sortie devrait être une sinature séralisée de 64 octets (R, S) avec un octet d'ID de recouvrement (V). 

La dernière étape est d'encoder la signature générée en une string base 64.

## Exemple 

Ci-dessous se trouve un exemple de création de signature en Python.

Voici un exemple de requête de transaction. Nous allons créer une signature pour cette transaction.

```json
{
    "jsonrpc": "2.0",
    "method": "icx_sendTransaction",
    "id": 1234,
    "params": {
        "version": "0x3",
        "from": "hxbe258ceb872e08851f1f59694dac2558708ece11",
        "to": "cxb0776ee37f5b45bfaea8cff1d8232fbb6122ec32",
        "value": "0xde0b6b3a7640000",
        "stepLimit": "0x12345",
        "timestamp": "0x563a6cf330136"
    }
}
```

Les données de la transaction sérialisées : 

```
icx_sendTransaction.from.hxbe258ceb872e08851f1f59694dac2558708ece11.stepLimit.0x12345.timestamp.0x563a6cf330136.to.cxb0776ee37f5b45bfaea8cff1d8232fbb6122ec32.value.0xde0b6b3a7640000.version.0x3
```

Crééons le hash des données de la transaction et générons la signature de la transaction.

```python
import base64
import hashlib
import secp256k1

serialized_transaction = "icx_sendTransaction.from.hxbe258ceb872e08851f1f59694dac2558708ece11.stepLimit.0x12345.timestamp.0x563a6cf330136.to.cxb0776ee37f5b45bfaea8cff1d8232fbb6122ec32.value.0xde0b6b3a7640000.version.0x3"

# transaction_hash
# result: b'\xc4\xa3\xa8\xae\xb5uH\x90\\\xfd\x9a1a\x9b\xe0\x05W\xf6\x03\x9a9\xac\xb8\xc5o\xce\x14\xcak\xae\x1f\x08'
msg_hash = hashlib.sha3_256(serialized_transaction.encode()).digest()

# Prépare la clé privée (cette clé privée n'existe que pour ce test)
private_key = b'\x870\x91*\xef\xedB\xac\x05\x8f\xd3\xf6\xfdvu8\x11\x04\xd49\xb3\xe1\x1f\x17\x1fTR\xd4\xf9\x19mL'

# Créé un objet représentant la clé privée
private_key_object = secp256k1.PrivateKey(private_key)

# Créé une signature ECDSA
recoverable_signature = private_key_object.ecdsa_sign_recoverable(msg_hash, raw=True)

# Convertit le résultat de ecdsa_sign_recoverable en un tuple composé de 64 octets et d'un integer denominé comme le recovery id.
signature, recovery_id = private_key_object.ecdsa_recoverable_serialize(recoverable_signature)
recoverable_sig = bytes(bytearray(signature) + recovery_id.to_bytes(1, 'big'))

# encode en base64
transaction_signature = base64.b64encode(recoverable_sig) 

# transaction_signature: b'a5fs7KC8Qw3Rpgyhx2b02WG7jghqdRT58dznUVb8qV12QhWx0zXi0YnIAmHHL2NF55ULn1RaEwrzQq2Fiq5W8wA='
```

---
[Document de référence](https://github.com/icon-project/icon-project.github.io/tree/25c1ad06172e2a58d06da35efbfab85c030d28d2)