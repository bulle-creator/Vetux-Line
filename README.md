# Vetux-Line

## Contexte

La société de service dans laquelle vous travaillez a comme client l’entreprise VETUX-LINE, createur de ligne de vêtements.

La société VETUX-LINE reçoit de la part de ses partenaires, tous les mois, 2 fichiers clients au format CSV.

Afin d’exploiter ces fichiers partenaires, VETUX-LINE souhaite disposer d’un outil (une application) lui permettant de fusionner ces 2 fichiers en un fichier unique.

La société nous communique des exemples de fichiers reçus. Ces fichiers sont : french-client.csv (~3000 clients) et german-client.csv (~2000 clients). Ces fichiers ont même structure (même type et nombre de colonnes).

Les fichiers reçus contiennent plus d’information que nécessaire. Le fichier résultant de la fusion sera composé d’un sous-ensemble des colonnes existantes (appelé projection) et une sélection de lignes sera effectuée (sélection des personnes majeures uniquement, suppression de doublons…​). La demande du client est détaillée plus loin dans ce document.

Dans un second temps (seconde partie de la mission), le service R&D de la société VETUX-LINE souhaite obtenir ces données sous la forme d’une base de données relationnelle.
