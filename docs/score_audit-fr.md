# Audit de SCORE

Pour chaque SCORE soumis pour le déploiement sur le réseau principal (*mainnet*) d'ICON, nous procédons à un audit de sécurité afin de vérifier qu'ils ne perturbent pas le *mainnet*. Le processus de l'audit peut prendre plusieurs jours. Avant de planifier de construire votre DApp, merci de prendre le temps de regarder ces lignes de conduite.

## Lignes de conduite

Le code doit être déterministe puisqu'il sera exécuté sur plusieurs nœuds. Vous devriez éviter tout fonctionnement logique qui dépend sur des entrées non déterministes, tels que l'heure, les nombres aléatoires, ou des sources de données externes.

- N'utilisez pas l'horloge temporelle. Utilisez les numéros de blocs à la place. Si vous souhaitez vraiment utiliser une information relative au temps, utilisez l'horodatage des blocs, ou l'horodatage des transactions.
- N'utilisez pas le module Python `random`. N'utilisez pas le hash de la transaction ou la hauteur du bloc comme seed afin de générer des nombres aléatoires.
- N'effectuez pas d'appel réseau qui récupère des données venant de sources externes dont l'issue ne peut être vérifiée et qui pourrait changer au fil du temps.

Pour passer un audit d'ICON, nous vous recommandons de procéder à la liste suivante :
- N'importez AUCUN paquet système. Importez SEULEMENT “iconservice” et les fichiers de votre propre implémentation placés dans le même dossier.
- Ne faîtes PAS d'opération qui dure longtemps à l'intérieur du SCORE. Le temps de génération moyen d'un bloc devrait être de 2 secondes pour ICON, votre transaction ne devrait pas interrompre la génération de bloc. Pas de bloc avec zéro transaction pour l'instant.

## Processus de l'audit

- Vous soumettez le déploiement (“deploy”) de votre SCORE au *mainnet* d'ICON depuis la ligne de commande de tbears.
- Votre SCORE passe dans l'état "en attente" si la transaction de déploiement s'est effectuée avec succès.
- Vous pouvez requêter l'état de votre SCORE en envoyant un appel d'API à une addresse spéciale en utilisant les SDKs (Java, Python) ou sur ICONex.
  - Addresse : cx0000000000000000000000000000000000000001
  - API : getScoreStatus
  - Paramètre : addresse du SCORE. Vous pouvez récupérer cette addresse dans le résultat de la commande "tbears txresult" en utilisant la transaction du déploiement.
- Une fois que l'auditeur accepte votre SCORE, il passe en état “actif”.
- S'il est rejeté, il passe en état “rejeté”. La réponse du message de "getScoreStatus" retournera le hash de la transaction de l'audit, et si vous requêtez la transaction de l'audit, vous saurez pourquoi il a été refusé. Vous devrez alors déployer une nouvelle fois après avoir résolu les problèmes.

## Notes

Ce processus d'audit devrait être temporaire puisque le réseau d'ICON courant n'en est encore qu'à ses balbutiements en tant que réseau public. Nous confirmons que nous n'avons pas l'intention d'avoir un contrôle sur les DApps. Notre première priorité est de maintenir le réseau d'ICON aussi stable que possible, et de minimiser tout impact négatif, s'il y en a, de nos partenaires qui se servent d'ICON.

---
[Document de référence](https://github.com/icon-project/icon-project.github.io/tree/3c4d77ced348bc5ea801eb61f55b5ac79e805ebd)