---
title:  "TextFSM Ovverview"
---

## TextFSM Ovverview : Parse et formate les sorties de CLI

### Définition
TextFSM est un module Python qui implémente une machine à états basée sur des Templates pour l’analyse de texte semi-formaté. Développé à l’origine pour permettre un accès programmatique aux informations fournies par la sortie des périphériques pilotés par la CLI, tels que les routeurs et les commutateurs réseau, il peut toutefois être utilisé pour toute sortie textuelle de ce type.

Le moteur prend deux entrées – un fichier de Template et une entrée de texte (telles que les réponses aux commandes à partir de la CLI d’un périphérique) et renvoie une liste d’enregistrements contenant les données analysées à partir du texte.

Un fichier Template est nécessaire pour chaque entrée de texte à structure unique. Certains exemples sont fournis avec le code et les utilisateurs sont encouragés à développer le leur.

En développant un pool de fichiers Templates, les scripts peuvent appeler textFSM pour analyser des informations utiles provenant de diverses sources. Il est également possible d’utiliser différents Templates sur les mêmes données, afin de créer différentes tables (ou vues).

TextFSM a été développé par Google sous licence Apache 2.0.

### Usage
TextFSM est un outil utile pour automatiser le formatage des données en sortie de commandes « Show ».

Lorsque l’on veut, par exemple, aller chercher des données de fonctionnalités précises sur un réseau en production, il est possible de faire un script qui exécute une ou plusieurs commandes « show » sur plusieurs équipements du réseau simultanément.

Le résultat de ces commandes peut être stocké dans des fichiers textes ou dans des variables.

Ces fichiers ou variables seront ensuite parsés par TextFSM grâce à un Template prédéfini (avec des expressions régulières).

 Le script retournera une liste de valeurs formatées qui pourra être utilisée avec d’autres outils/scripts pour leurs interprétations.

### Exemple
Je suis administrateur d’un réseau dont le protocole de routage est BGP.
Je souhaite connaitre tous les voisins BGP de chaque routeurs que j’ai dans mon réseau.
Je vais créer un Template TextFSM permettant d’identifier les valeurs pertinentes que je pourrais trouver dans la sortie d’une commande « show », disons « show ip bgp summary ».

Ensuite, je vais créer un script Pyhton qui va se connecter à la liste de routeurs concernés et qui exécutera une commande « show ip bgp summary ».

Mon script stockera la sortie brut (au format texte) de la commande dans un fichier ou une variable pour chaque routeur, nommé avec le nom du routeur (Hostname).

Le même script utilisera, ensuite, TextFSM avec le fichier spécifique du routeur et le Template créé pour retourner une liste formatée des données du routeur interrogé.
En corrélant toutes les listes des routeurs, je vais faire en sorte que mon script puisse avoir une sortie intéressante de l’ensemble des routeurs et de leurs voisins BGP.

Exemple pour une sortie « show ip bgp summary » sur un routeur:

BGP router identifier 192.0.2.70, local AS number 65550
BGP table version is 9, main routing table version 9
4 network entries using 468 bytes of memory
4 path entries using 208 bytes of memory
3/2 BGP path/bestpath attribute entries using 420 bytes of memory
1 BGP AS-PATH entries using 24 bytes of memory
1 BGP community entries using 24 bytes of memory
0 BGP route-map cache entries using 0 bytes of memory
0 BGP filter-list cache entries using 0 bytes of memory
BGP using 1144 total bytes of memory
BGP activity 12/4 prefixes, 12/4 paths, scan interval 5 secs

`Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd`
`192.0.2.77      4 65551    6965    1766        9    0    0  5w4d           1`
`192.0.2.78      4 65552    6965    1766        9    0    0  5w4d          10`

Avec le Template suivant :

`# Permet de representer les titres des colones (RouterID, LocalAS,...)`
`Value Filldown RouterID (\S+)`
`Value Filldown LocalAS (\d+)`
`Value RemoteAS (\d+)`
`Value Required RemoteIP (\d+(\.\d+){3})`
`Value Uptime (\d+\S+)`
`Value Received_V4 (\d+)`
`Value Status (\D.*)`

`Start`
  `^BGP router identifier ${RouterID}, local AS number ${LocalAS}`
  `^${RemoteIP}\s+\d+\s+${RemoteAS}(\s+\S+){5}\s+${Uptime}\s+${Received_V4} -> Record`
  `^${RemoteIP}\s+\d+\s+${RemoteAS}(\s+\S+){5}\s+${Uptime}\s+${Status} -> Record`

`# Last record is already recorded then skip doing so here.`
`EOF`

Donnera une représentation tabulaire comme suit:

['RouterID', 'LocalAS', 'RemoteAS', 'RemoteIP', 'Uptime', 'Received_V4', 'Status']
['192.0.2.70', '65550', '65551', '192.0.2.77', '5w4d', '1', '1']
['192.0.2.70', '65550', '65552', '192.0.2.78', '5w4d', '10', '10']

Plus de détails bientôt dans un article plus poussé. En attendant, de nombreux Templates sont déjà disponible sur la page GitHub de [Networktocode](https://github.com/networktocode/ntc-templates/tree/master/ntc_templates/templates).