---
title:  "Ansible Overview !"
---

## Ansible Overview

Dans le domaine de l’automatisation, Ansible est l’un des outils les plus populaires.

Ansible est un outils Open Source qui appartient à RedHat. Une version commerciale appelée Tower est aussi disponible. Depuis quelques temps, Tower existe aussi en version non commerciale sous le nom AWX.

A l’origine ce produit était destiné à l’automatisation des serveurs Linux et les ingénieurs réseaux l’ont vite adopté pour plusieurs raisons que nous allons détailler dans cet article.

Avantages
Tout d’abord, Ansible est un outils développé en Python et ne nécessite pas d’agent sur l’équipement à piloter. Il utilise des bibliothèques Python qui permettent de gérer les connexions en mode SSH aussi bien qu’en mode REST natif (pour les équipements qui possèdent une API Restful) depuis l’ordinateur qui exécute Ansible.

Ensuite, Ansible, bien que développé en Python, possède son propre Domain Specific Language (DSL). Cela facilite sa prise en main pour des administrateurs qui ne sont pas trop familiers avec le développement.
Ainsi les boucles FOR et les Conditions IF sont implicitement mis en oeuvre dans les tâches d’Ansible (Playbooks) avec des mots clés tels que With_Items et When.

Fonctionnement
Globalement, Ansible est une application qui lance des « Playbooks », constitués de « Tasks » et de « Vars » sur un « Inventory » de périphériques depuis un OS Linux avec une arborescence de répertoires hiérarchisés.

L’Inventory est un fichier « hosts » qui liste les périphériques à piloter avec la possibilité de les regrouper. On peut configurer des variables d’environnement pour chaque périphérique ou groupe de périphériques.

Les Playbooks sont des fichiers au format YAML qui listent essentiellement : les équipements concernés dans l’inventaire, les tâches (Tasks) à effectuer et les variables (Vars) à utiliser.

La gestion des variables est très bien faite. Un système d’héritage permet à Ansible de trouver les variables utilisées par une tâche, dans plusieurs emplacements, avec une priorité définie.
Par exemple, une tâche qui est lancée par un Playbook, cherchera en premier les variables (vars) configurées directement dans le Playbook.
Si elle ne les trouve pas, elle cherchera dans les variables spécifiques à l’Host traité (dans un fichier dédié à l’Host, se trouvant dans le répertoire Host_vars).
Sans succès, elle cherchera dans les variables d’un groupe (dans un fichier dédié à un groupe, situé dans le répertoire Group_Vars).
Finalement, on peut créé un fichier all.yml, contenant toutes les variables qui sont communes à tous les périphériques.

Les tâches (Tasks) représentent une action à effectuée sous forme de « module » d’Ansible. Ces modules font parti du code d’Ansible et sont de plus en plus nombreux avec l’évolution des versions d’Ansible. On peut développer ces modules en python lorsqu’ils n’existent pas directement dans Ansible. La documentation d’Ansible détaille les modules ainsi que leur usage.

Les composants des playbooks peuvent être décomposés à l’intérieur d’un « Role », sous forme de répertoires portant leurs noms (Tasks, Vars, etc…).

Ansible est un outils incontournable pour l’automatisation des réseaux. Vous trouverez de nombreux articles, à son sujet, sur ce blog. Ils vous permettront d’apprendre rapidement son usage.