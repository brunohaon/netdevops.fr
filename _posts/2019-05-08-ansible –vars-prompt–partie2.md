---
title:  "Ansible – vars-prompt – Partie 2"
categories: Outils
---

## Ansible – vars-prompt – Partie 2

Dans la première partie de cet article (Ansible – vars-prompt – Partie 1), nous avons vue comment se comporte un Playbook sans la fonctionnalité vars-prompt.
Dans la partie 2, nous allons faire le même Playbook mais cette fois en utilisant la fonctionnalité pour créer un Playbook d’exploitation plutôt qu’un Playbook d’intégration.

### Usage avec vars-prompt
Avec la fonctionnalité vars-prompt, les variables du Playbook seront demandées par Ansible sous forme de prompts que nous allons configurer.
Ainsi, il n’est pas nécessaire d’alimenter un fichier de variables, on dans une utilisation en mode exploitation.

#### Arborescence
L’arborescence reste la même que celle vue précédemment dans la partie 1.

#### Contenu des fichiers

```yaml
#Working_dir/group_vars/access.yml
---

provider:
  host: "{{ ansible_host }}"
  username: "admin"
  password: "admin"
  timeout: 120

--------------------------------------------------
#Working_dir/hosts

[access]
ACCESS1
ACCESS2

--------------------------------------------------
#Working_dir/add_vlans.yml
---

- hosts: access
  connection: local
  gather_facts: no

  vars_prompt:
    - name: "vid"
      prompt: "Inserez l'ID du VLAN a ajouter"
      private: no
    - name: "name"
      prompt: "Inserez le nom du VLAN a ajouter"
      private: no

  roles:
    - role: add_vlans

--------------------------------------------------

#Working_dir/roles/add_vlans/tasks/main.yml
----

- name: GENERATE CONFIG TO ADD VLANS
  template:
    src: add_vlans.j2
    dest: "roles/add_vlans/configs/{{inventory_hostname}}.cfg"

- name: PUSH CONFIG TO SWITCHES
  ios_config:
    provider: "{{ provider }}"
    src: "roles/add_vlans/configs/{{inventory_hostname}}.cfg"

--------------------------------------------------

#Working_dir/roles/add_vlans/templates/add_vlans.j2

vlan {{ vid }}
 name {{ name }}
```

### Fonctionnement
Lorsque le playbook est lancé avec la commande ansible-playbook add_vlans.yml, un premier prompt apparaît « Inserez l’ID du VLAN a ajouter » (attention : Ansible rajoute automatiquement « : » à la fin du prompt), vous laissant la main pour taper une valeur.
Après avoir valider la première question, un deuxième prompt vous demandera le nom du VLAN.
Les deux valeurs saisies sont stockées avec les noms définis dans la section vars-prompt du Playbook (ici « vid » et « name » respectivement), afin de pouvoir les utiliser dans les tâches du rôle add_vlans appelées par le Playbook.
Note : il est important de constater que les vars-prompt sont définis dans le fichier add_vlans.yml, directement à la racine du répertoire principal utilisé (ici Working_dir).

### Paramètres avancés
Le paramètre « private » permet, s’il est activé (private : yes), de masquer les valeurs entrées à la main. Dans le cas d’un Password par exemple ou de n’importe quelle valeur confidentielle.
Attention : par défaut le paramètre « private » est activé. Pour voir ce que vous tapez, il faut donc le désactiver.

Le paramètre « default » permet d’attribuer une valeur par défaut à la variable du prompt. Cette valeur par défaut sera stockée si aucune entrée manuelle est faite.
exemple:

```yaml
vars_prompt:
     - name: "vid"
       prompt: "Inserez l'ID du VLAN a ajouter"
       default: "1"
       private: no 
```

Il est aussi possible de chiffrer les valeurs entrées avec le paramètre « encrypt« , mais cela nécessite d’installer Passlib qui est une librairie Python pour « Hasher » du contenu.
Plus d’information sur la documentation d’Ansible.

### Conclusion
La fonctionnalité vars-prompt peut être très utile dans le cas de l’exploitation d’un réseau avec des tâches répétitives sur un nombre conséquent d’équipements.
L’usage décrit par Ansible est la confidentialité de certaines données sensibles qui n’ont rien à faire dans un fichier de variable (en texte clair). Il existe pour cet usage un outils appelé ansible-vault (un article est en préparation).
Pour un usage plus global, on préférera les fichiers de variable, car on peut plus facilement faire du déploiement massif, ou encore l’usage de solution dites Source of Truth qui permettent de stocker, dans une base de données, l’ensemble des informations utiles pour l’automatisation tels que Netbox (Digital Ocean) et dans ce cas il faudra utiliser un plugin netbox pour faire interagir Ansible et Netbox. Un article sera consacré à ce mode d’utilisation.

Retour : Ansible – vars-prompt – Partie 1
