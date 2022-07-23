---
layout: post
title:  "Jinja2 Overview !"
---

## Jinja2 Overview !

Jinja2 est un langage de « Templating » pour Python, moderne et facile à utiliser.
Il est basé sur le modèle de Templating de Django (Framework python pour créer des applications Web).

A l’origine Jinja était utilisé pour créer des pages HTML à partir d’un modèle pré formaté dans lequel on injecte des données sous forme de variables. On appelle ce modèle un Template.

Pour les administrateurs réseaux, cet outils est très important, voir le plus important car il permet de faire des templates de configuration avec des variables.

J’entends déjà les anciens (dont je fais parti) dirent, « on a pas besoin de ça, on utilisait déjà des scripts PERL ou même des éditeurs de texte pour faire cela avec du « Rechercher/Remplacer »…. »
OUI, vous avez raison, ce n’est qu’un autre outils pour le faire.
En revanche, en raison de l’utilisation, de plus en plus fréquente, de cet outil par les administrateurs réseau, permet de le rendre de plus en plus puissant notamment grâce à la présence de filtres adaptés à notre métier. Nous y reviendrons plus tard…

Fonctionnement
Jinja2 est donc un langage, il permet de créer des templates avec des variables mais aussi avec des conditions (type IF/THEN/ELSE) et des boucles (type FOR).

Règles de base
Voici les règles de base (très bonne documentation en ligne sur le site de Jinja2 ici) :

Premièrement, un Template Jinja est simplement un fichier texte. Il ne nécessite pas d’avoir une extension spécifique (.txt, .xml, etc…), mais dans un soucis d’organisation, une bonne pratique peut être de mettre une extension .j2.

Un template contient des variables et/ou des expressions, qui seront remplacés avec des valeurs lorsqu’un template est « rendu ». Des tags contrôlent la logique du template. La syntaxe du template est largement inspirée par Django et Python.

Ci-dessous, un exemple minimaliste dont nous expliquerons les détails plus loin.

`{# creation des vlans #}`
`{% for vlanid in vlans %}`
    `vlan {{ vlanid }}`
`{% endfor %}`

Le langage possède les basiques suivants :

- `{% … %}` pour créer des Statements
- `{{ … }}` pour déclarer des expressions/variables à afficher dans le rendu
- `{# … #}` pour les commentaires qui ne seront pas affichés dans le rendu

Dans l’exemple ci-dessus, le petit template Jinja2 possède un commentaire suivi d’une boucle FOR qui permet de générer la création de VLAN pour chaque VLAN ID trouvé dans un objet python créé dans l’application utilisant Jinja2.
Dans l’exemple ci-dessous, on utilise une liste :

`vlans =  [12, 13, 14]`

Les filtres
Vous pouvez formater les variables grâce à des filtres existants dans l’application utilisant le templating Jinja2. Pour appliquer un filtre, il suffit de mettre un Pipe (« | ») après la variable suivit du filtre à appliquer.
Dans l’exemple ci-dessous, on définit une valeur par défaut pour l’ID de VLAN Natif, ainsi si le template ne trouve aucune variable, il utilisera la valeur par défaut.

`vlan {{ id_native_vlan|défaut ('99') }}`

On peut utiliser Jinja2 directement comme Bibliothèque Python en l’installant avec PIP:On peut utiliser Jinja2 directement comme Bibliothèque Python en l’installant avec PIP:

`pip install Jinja2`

Puis en l’utilisant comme dans l’exemple suivant:

`from jinja2 import Template` 
`template = Template('{% for vlan in vlans %}vlan {{ vlan }} {% endfor %}')`
`template.render(vlans=[1, 2, 3, 4, 5, 6])` 
`u'vlan 1 vlan 2 vlan 3 vlan 4 vlan 5 vlan 6'`

Ansible utilise Jinja2 comme langage de Templating, il intègre des filtres très intéressants pour la pratique du NetDevOps tels que "ipaddress" …