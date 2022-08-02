---
title:  "Ansible – vars-prompt – Partie 1"
categories: "Outils"
---

## Ansible – vars-prompt – Partie 1

Lorsque l’on travaille avec Ansible, on doit constamment modifier des fichiers de variables au format YAML. Les habitués des systèmes Linux ne trouvent pas cela problématique. Cependant, pour certaines personnes moins concernées par l’automatisation, la gestion des fichiers de variables devient vite complexe.
Bien souvent, seules quelques variables doivent être modifiées avant de jouer un Playbook, ainsi il pourrait être intéressant d’avoir un mode interactif qui questionne l’administrateur pour entrer les variables du Playbook.
La fonctionnalité vars-prompt est justement faite pour ça.

Cas d’usage
Pour illustrer cette fonctionnalité, nous allons utilisé un cas d’usage habituel : l’ajout d’un VLAN sur un ensemble de Switches.
Nous allons créer un Playbook qui va générer un bout de configuration pour créer un nouveau VLAN puis qui le poussera sur un ensemble de commutateurs.

Uage sans vars-prompt
Sans la fonctionnalité vars-prompt, il faudrait mettre dans un des fichiers de variables, la liste des VLAN devant être créés, avec l’ensemble des attributs d’un VLAN (ID, NOM, IP, etc…).

### Arborescence
```bash
Working_dir
|_group_vars
  |_access.yml
|_host_vars
|_roles
  |_add_vlans
    |_configs
    |_tasks
      |_main.yml
    |_templates
      |_add_vlans.j2
|_ansible.cfg
|_add_vlans.yml
|_hosts
```

### Contenu des fichiers

```yaml
#Working_dir/group_vars/access.yml
---

provider:
  host: "{{ ansible_host }}"
  username: "admin"
  password: "admin"
  timeout: 120

vlans:
- { vid: 10, name: MGMT }
- { vid: 20, name: USERS }
- { vid: 30, name: VOICE }

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

#jinja2: lstrip_blocks: "true"
{% for vlan in vlans %}
vlan {{ vlan['vid'] }}
 name {{ vlan['name'] }}
{% endfor %}
```

### Fonctionnement
Pour lancer le Playbook, il suffit de taper la commande :
ansible-playbook add_vlans.yml
Ce playbook indique à Ansible d’agir sur les commutateurs du groupe access dans le fichier Working_dir/hosts avec les tâches du rôle add_vlans c’est à dire celles qui sont incluses dans le fichier Working_dir/roles/add_vlans/main.yml et qui effectuent les actions suivantes:

Génèrer un fichier Working_dir/roles/add_vlans/configs/Nom_du_Switch.cfg à partir du template Working_dir/roles/add_vlans/templates/add_vlans.j2 avec les variables de la liste « vlans » contenus dans le fichier Working_dir/group_vars/access.yml
Pousser le contenu du fichier sur l’équipement concerné pour chaque commutateur inclus dans le groupe access du fichier hosts
Lorsque l’on veut ajouter une liste de vlans, il faut rajouter les informations nécessaires dans le fichier Working_dir/group_vars/access.yml.

Pour l’intégration d’un projet cette méthode doit être employée car elle permet de créer l’ensemble des VLAN avec une seul requête.
Pour des tâches quotidiennes, cependant, il peut être intéressant de posséder un Playbook pour ajouter uniquement un VLAN, avec la possibilité de changer rapidement l’ID et le nom du VLAN.
C’est dans ce dernier cas que la fonctionnalité vars-prompt sera adaptée.

Suite : Ansible – vars-prompt – Partie 2 (lien vers 2eme article Ansible)

