---
title:  "YAML Overview !"
---

## YAML Overview !

Le format de données YAML (Yet Another Markup Languge) permet de formater des données de façon structurée, avec une lisibilité très efficace pour les humains.
Beaucoup plus lisible par exemple que le XML, et aussi efficace que le format CSV.

On trouve souvent le format YAML dans les fichiers de configuration de certains logiciels, tels qu’Ansible.

Les fichiers YAML commencent en général par " --- " , une indentation doit ensuite être respecté pour assurer la structure de données.

On retrouve une structure « clé » : « valeur » sur une seule ligne :

```yaml
---
dns_server: 10.1.1.1
domain_name: netdevops.fr
```

Lorsqu’un ensemble de paramètres représente un élément d’une liste, un tiret (-) représente le début de chaque élément :

```yaml
vlans:
    - id: 1
      name: native
    - id: 2
      name: data
    - id: 3
      name: voice
```

il est possible d’utiliser le format [JSON](#) pour représenter un dictionnaire :

```yaml
vlans:
    - { id: 1, name: native }
    - { id: 2, name: data }
    - { id: 3, name: voice }
```

Il est possible de représenter des hiérarchies plus complexes avec des indentations :

```yaml
#vrfs
- GUEST:
      rd: '65100:1'       
      rt_both: '65100:1'
      member_interfaces:
          - vlan100
          - vlan150
          - GigabitEthernet1/0/1 
```

```yaml
- DMZ:
      rd: '65100:100'
      rt_both: '65100:100'
      member_interfaces:
          - vlan200
          - vlan250
          - GigabitEthernet1/0/10
```

