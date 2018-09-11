# Comment contribuer

Merci de votre intérêt pour participer au développement de la documentation du projet ICON. Vous êtes encouragés à créer des demandes de fusion de branches (*pull request*) et de poser des questions (*issues*).

## Processus général

- Forkez le dépôt Github.
- Pour tout travail significatif, merci de bien vouloir créer une branche séparée.
- Vérifiez tout caractère tels que des espaces non nécessaires avec `git diff --check` avant de créer un commit.
- Poussez vos changements sur votre branche distante.
- Créez une demande de fusion de branche.

## Types de contributions de documentation

- Améliorations générales de documents déjà existants
  - Correction typographique, réparation de références ou de liens cassés, correction d'imprécisions ou d'informations dépassées, ou offre de meilleures explications à travers une description plus claire et des exemples additionnels.
- Localisation
  - Merci de bien vouloir créer une *issue* si vous débutez une nouvelle localisation. Ceci est requis afin d'éviter tout travail en double.
  - Un travail partiel peut être accepté. Ces dernières contributions ne requiert pas qu'une *issue* soit créée.
  - L'Anglais est le langage par défaut. Nous acceptons que les contributeurs nous aident à traduire les documents Coréens vers l'Anglais.
  - S'il y a une version disponible en plusieurs langages, l'Anglais est le document official qui reflète les derniers changements. Par conséquent, merci de toujours vous baser sur la version anglaise lorsque vous traduisez un document.
  - Le fichier de la version Anglaise devrait être nommé `[nom du fichier].md`. Pour tout autre langage, merci de le nommer `[nom du fichier]-[langue].md` où `[langue]` est le code à deux lettres définis dans l'ISO-639-1. Par exemple, `README.md` est la version officielle écrite en Anglais, tandis que `README-ja.md` correspond à la version traduite en Japonais.
  - Les documents traduits doivent être stockés à côté dans le même répertoire que le fichier du langage par défaut.
- Ajouter un nouveau document que nous n'avons pas encore couvert.
  - Pour ce type de contribution, merci de créer une *issue* et exposez un aperçu du contenu que vous souhaitez créer. Ceci est requis afin d'aligner nos efforts sur la documentation en cours de rédaction. Lorsque la demande est acceptée, le mainteneur du dépôt vous guidera pour vous indiquer dans quel dossier vous devriez créer vos documents et vos images. Les demandes de fusion de branches sans référence à une *issue* préalablement créée peuvent être rejetées.