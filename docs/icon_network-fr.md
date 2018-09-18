# Réseau d'ICON

Lorsque vous basculez entre les différents réseaux, veillez à être sûr d'utiliser le bon identifiant de réseau (*network id*).
Si l'identifiant de réseau ne correspond pas, les transactions échoueront.

## Devnet privé sur AWS 
- TBA

## Testnet pour les DApps

Veuillez noter que le testnet courant peut être réinitialisé à zéro à tout moment, et vous pouvez être confronté à des indisponibilités imprévues.
Nous travaillons sur un environnement de test final plus stable accompagné de plusieurs réseaux pour faire des tests.

|              |                |
|--------------|----------------|
| Nom          | Yeouido (여의도) |
| Noeud         | https://bicon.net.solidwallet.io |
| Point d'accès de l'API | https://bicon.net.solidwallet.io/api/v3 |
| Network ID (nid) | 3 |
| Tracker         | https://bicon.tracker.solidwallet.io |
| Frais de transaction | Activé |
| Audit de SCORE     | Désactivé |

Pour recevoir des ICX sur le testnet, merci d'envoyer un email à `testicx@icon.foundation` avec les informations suivantes.
- URL du nœud testnet
- Addresse de réception des ICX de testnet. Il s'agit d'une chaîne de caractères débutant par `hx`.

## Testnet pour les échanges
Le réseau Euljiro est exclusivement ouvert pour les développeurs des échanges.

|              |                |
|--------------|----------------|
| Nom         | Euljiro (을지로) |
| Noeud         | https://test-ctz.solidwallet.io |
| Point d'accès de l'API | https://test-ctz.solidwallet.io/api/v3 |
| Network ID (nid)| 2 |
| Tracker         | https://trackerdev.icon.foundation |
| Frais de transaction | Activé  |
| Audit de SCORE     | Désactivé |

Pour recevoir des ICX de test, merci de contacter votre gérant de compte.

## Mainnet

|              |                |
|--------------|----------------|
| Nom         | ICON Mainnet   |
| Noeud         | https://ctz.solidwallet.io |
| Point d'accès de l'API | https://ctz.solidwallet.io/api/v3 |
| Network ID (nid)| 1 |
| Tracker         | https://tracker.icon.foundation |
| Frais de transaction | Activé  |
| Audit de SCORE     | Activé  |

Avant de soumettre votre SCORE au réseau principal d'ICON, vous devez le tester entièrement à l'aide de T-Bears et du testnet.
Notez que l'audit du SCORE n'est pas activé sur le testnet. Veuillez être sûr que vous comprenez la politique d'audit d'ICON et de suivre les règles.
# [Audit de SCORE](https://icon-project.github.io/docs/score_audit-fr.html)
  - [Liste de choses à vérifier](https://icon-project.github.io/docs/audit_checklist-fr.html)
  - [Guide de déploiement](https://icon-project.github.io/docs/score_deploy_guide-fr.html)

Si vous avez la moindre question à propos du processus de l'audit, merci d'envoyer à mail à `audit@icon.foundation`.

---
[Document de référence](https://github.com/icon-project/icon-project.github.io/blob/d7ea24967b67b76c2b65ce9c77f9b5135699ab69/icon_network.md)
