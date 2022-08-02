---
title:  "Nornir Overview !"
categories: "Librairies_python"
---

## Nornir Overview !

Nornir est un Framework d’automatisation sous forme de librairie Python. Il permet d’utiliser nativement le langage Python pour effectuer les tâches de configuration des équipements actifs. Il se distingue, ainsi, d’autres outils comme Ansible qui possèdent un Domain Specific Language (DSL), c’est à dire un langage spécifique au dessus de Python.

Avantages
Nornir permet une meilleure intégration des scripts d’automatisation Python dans des projets plus globaux.
Par exemple lors de la construction d’une application d’orchestration d’infrastructure sous Python avec un framework tel que Flask ou Django.
En effet, pour piloter plusieurs périphériques de constructeurs différents, il peut être compliqué d’intégrer des outils comme Ansible dans le code Python.
Il faudrait utiliser des outils supplémentaires tels que AWX (version gratuite d’Ansible Tower fournissant une API REST).

Avec Nornir, il suffit de créer des scripts python qui pourront être intégrés via des classes ou des fonctions dans l’application globale.

Fonctionnement
Nornir fournit un mécanisme de distribution des tâches sur un inventaire de périphériques. Le tout sous forme de fichiers Python 3.6 (ou supérieur).

Trois fichiers avec le format YAML composent l’inventaire :

Hosts.yml : contient les informations (variables) spécifiques aux hôtes à configurer
Groups.yml : contient les informations (variables) plus globales concernant les groupes
Defaults.yml : contient les informations (variables) concernant tous les groupes
il existe un héritage de données qui permet de récupérer des variables de groupes et de les appliquer sur des hôtes membres de ces groupes.

Des tâches seront effectuées sur des parties de l’inventaire (hosts, groups, tous) ou sur certains équipements sélectionnés grâce à des filtres plus ou moins avancés.

Nornir fournit certaines tâches, pour les actions de configuration les plus courantes. Il est aussi possible de construire directement vos propres tâches via des scripts python.

Vous pouvez trouver un bon tutoriel, permettant de se familiariser avec Nornir, sur sa page de documentation [ici](https://nornir.readthedocs.io/en/stable/index.html).