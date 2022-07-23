---
title:  "JSON Overview !"
---

## JSON Overview !

Le format JSON (JavaScript Object Notation) permet de représenter des données de façon structurée dans un fichier texte.
Pour ceux qui connaissent le langage Python, c’est exactement la même structure que celle des dictionnaires à l’exception qu’un fichier au format JSON est permanent et exportable, alors qu’un dictionnaire Python existe uniquement en mémoire de l’ordinateur qui exécute le code.

Ce format est devenu incontournable pour représenter les données utilisées dans les API REST et dans les échanges de données en général. Il est très lisible et utilise des ensembles de pairs {« clé » : « valeur »} ou des listes ordonnées de valeurs, le tout structuré avec des indentations.

Les valeurs peuvent être de tout type, comme des listes, des entiers, des chaines de caractères, etc…

Exemple :

`{`
        `"vlans": [`
              `{ "id": 1, "name": "native" },`
              `{ "id": 2, "name": "data"},`
              `{ "id": 3, "name": "voice"}`
        `]`
`}`



