---
title:  "Le filtre 'ip address' de Jinja 2 sous Ansible"
---

## Le filtre 'ip address' de Jinja 2 sous Ansible

Le module le plus utilisé, sous Ansible, pour la génération des configurations est de loin celui de templating nommé « template ».
Il permet de créer des fichiers de configurations à partir de templates au format Jinja2.

Une des forces du langage Jinja2 est la possibilité d’utiliser des filtres pour formater ou vérifier des variables au moment du rendu du template.
Ansible possède ses propres filtres, on peut en trouver une liste ici.
Celui qui nous intéresse grandement est nommé « ipaddr ».
Voici un résumé de la documentation officielle d’Ansible.

ipaddr() est un filtre Jinja2 conçu pour fournir une interface vers le Package Python « netaddr » à l’intérieur d’Ansible. Il peut opérer sur des ‘strings’ ou sur des listes d’éléments, il peut tester des données variées pour vérifier la validité d’adresses IP, et il peut manipuler les données entrantes pour en extraire des informations requises.
ipaddr() fonctionne aussi bien avec les adresses IPv4 qu’IPv6 sous des formes variées. D’autres fonctions additionnelles sont aussi disponibles pour manipuler les adresses MAC et les Subnets IP.

Pour utiliser ce filtre dans Ansible, il est nécessaire d’installer la librairie Python « netaddr » sur la machine qui utilise Ansible (pas nécessaire de l’installer sur l’hôte distant).
Vous pouvez installer librairie avec un manager de Package d’un système ou en utilisant « pip »:

`pip install netaddr`

### Tests basiques

ipaddr() est conçu pour retourner la valeur entrée si une requête est True, et False si une requête est False. Ainsi, il est facile de l’utiliser dans des filtres chainés. Pour utiliser le filtre, il suffit de mettre une valeur String suivi du filtre:

`{{ '192.0.2.0' | ipaddr }}`

On peut utiliser une variable à la place d’un String:

`{{ ip_address_1 | ipaddr }}`

Voici quelques résultats du filtre sur différentes valeurs:

`'192.168.0.1'       192.168.0.1`
`'192.168.32.0/24'   192.168.32.0/24`
`'fe80::100/10'      False`
`45443646733         False`
`'523454/24'         0.7.252.190/24`

### Filtrage de listes

Lorsqu’une liste contenant des informations doit être filtrée pour extraire certaines valeurs valides, il est possible d’utiliser ipaddr() avec une requête particulière entre les parenthèses.

`# Exemple de liste de valeurs`
`test_list = ['192.24.2.1', 'host.fqdn', '::1', '192.168.32.0/24', 'fe80::100/10', True, '', '42540766412265424405338506004571095040/64']`

`# {{ test_list | ipaddr }}`
`['192.24.2.1', '::1', '192.168.32.0/24', 'fe80::100/10', '2001:db8:32c:faad::/64']`
 
`# {{ test_list | ipv4 }}`
`['192.24.2.1', '192.168.32.0/24']`

 `# {{ test_list | ipv6 }}`
`['::1', 'fe80::100/10', '2001:db8:32c:faad::/64']`

### Usages utiles
Voici quelques exemples utiles, toujours basés sur des valeurs d’entrée incluses dans la liste suivante:

`test_list = ['192.24.2.1', 'host.fqdn', '::1', '192.168.32.0/24', 'fe80::100/10', True, '', '42540766412265424405338506004571095040/64']`

Vérifier si des adresses IP ou des ranges de réseau sont Privés ou Publics :

`# {{ test_list | ipaddr('public') }}`
`['192.24.2.1', '2001:db8:32c:faad::/64']`

`# {{ test_list | ipaddr('private') }}`
`['192.168.32.0/24', 'fe80::100/10']`

Possibilité de vérifier quelles valeurs sont dans des ranges:

`# {{ test_list | ipaddr('net') }}`
`['192.168.32.0/24', '2001:db8:32c:faad::/64']`

On peut savoir combien d’adresses sont incluses dans un certain range:

`# {{ test_list | ipaddr('net') | ipaddr('size') }}`
`[256, 18446744073709551616L]`

En spécifiant un range de réseau dans une requête, il est possible de vérifier si une valeur donnée se trouve dans ce range:

`# {{ test_list | ipaddr('192.0.0.0/8') }}`
`['192.24.2.1', '192.168.32.0/24']`

Il est possible d’extraire les informations de Subnet et de Prefix à partir d’une valeur ‘Host/Prefix’:

`host_prefix = ['2001:db8:deaf:be11::ef3/64', '192.0.2.48/24', '127.0.0.1', '192.168.0.0/16']`

`# {{ host_prefix | ipaddr('host/prefix') | ipaddr('subnet') }}`
`['2001:db8:deaf:be11::/64', '192.0.2.0/24']` 

`# {{ host_prefix | ipaddr('host/prefix') | ipaddr('prefix') }}`
`[64, 24]`

Conversion des masques de sous réseau en notation CIDR:

`net_mask = '192.168.0.0/255.255.255.0'`
`# {{ net_mask | ipaddr('net') }}`
`'192.168.0.0/24'`

Vous pouvez trouver d’autres exemples dans la documentation d’Ansible [ici](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters_ipaddr.html).


