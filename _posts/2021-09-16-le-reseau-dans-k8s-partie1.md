---
title:  "Le Réseau dans K8S - partie 1"
---

## K8S - Le Réseau dans K8S - partie 1

Les ingénieurs réseaux sont habitués à travailler avec des appliances physiques pour assurer les différentes fonctions liées à l’interconnexion des périphériques.
Les containers tournent sur des hôtes Linux, il est important de comprendre comment fonctionne le réseaux au sein des hôtes, en mode intra cluster mais aussi inter-cluster.
Avec cet article, je vais essayer d’expliquer ces mécanismes dans un environnement Kubernetes en m’appuyant sur de nombreuses sources, particulièrement sur l’article de Kevin Sookocheff: [« A Guide to the Kubernetes Networking Model »](https://sookocheff.com/post/kubernetes/understanding-kubernetes-networking-model/) que je vous recommande de lire.
La partie 1 présente les éléments de base de Kubernetes et des composants réseaux qui assurent la création des containers sous Linux.

### Les composants de Kubernetes (K8s)
Retournons dans un premier temps sur les concepts de Kubernetes.

Kubernetes est installé sur un Node possédant un OS Linux.
Les Nodes possèdent une adresse IP accessible depuis le réseau physique. C’est l’adresse qui a été attribuée lors de l’installation de l’OS. Nous l’appellerons HOST-IP pour plus de facilité.

![Interface Physique HOST IP]({{site.baseurl}}/assets/img/Diapositive1.jpeg)

Des ressources sont installées sur le Node, telles que des Workloads, des Services, des Volumes, etc…

La plus petite entité possédant une IP dans K8s est le POD. Nous appellerons l’adresse IP associée POD-IP.
Le Pod peut être composé de plusieurs containers, partageant l’IP POD-IP, en utilisant des ports UDP/TCP différents pour exploiter le Multiplexing.

![POD IP](assets/img/Diapositive2.jpeg)

Dans le monde des containers, à l’inverse de nos infrastructures legacy, le workload n’est pas forcément fait pour rester UP and Running 24/24 – 7/7.
Avec le déploiement continue, la durée de vie d’un container peut s’avérer être très éphémère.
Chaque fois qu’un Pod est mis à jour, ou redémarré, une nouvelle POD-IP est affectée au nouveau Pod.
Le DNS interne de K8s doit s’appuyer sur une IP durable pour permettre une résolution fonctionnelle du nom du service.

Les Pods utilisent donc une couche d’abstraction : les Services.
Les services représentent une VIP (nous l’appellerons SVC-IP) et sont des Load Balancers qui distribuent le trafic vers les POD-IP des pods concernés (grâce à des labels).

![Service IP](assets/img/Diapositive3.jpeg)

### Les grandes règles de la mise en réseau sous K8s
Chaque Pod possède une adresse IP qui est partagée entre tous les containers qu’il possède.
Tous les Pods peuvent communiquer avec tous les autres Pods sans utiliser la fonction NAT (Network Address Translation).
Tous les Nodes peuvent communiquer avec tous les Pods sans utiliser NAT.
L’IP d’un Pod est unique de son propre point de vue comme du point de vue des autres Pods.
Il existe 4 types de communication :

Container-to-Container
Pod-to-Pod
Pod-to-Service
Externe-to-Service

### Communication Container to Container

#### Les Namespaces
Avant de se lancer dans l’explication des communications au sein de l’hôte Linux, il est important d’expliquer ce qu’est un Namespace.
Pour ceux qui souhaitent en connaitre plus sur les Namespaces, je vous conseille de lire l’article de Jérémy Rosen ["Namespaces : La brique de base des conteneurs"](https://linuxembedded.fr/2020/11/namespaces-la-brique-de-base-des-conteneurs).

Un container n’existe pas au niveau du Kernel Linux et il est représenté par deux éléments principaux, les cgroups et les namespaces.
Les cgroups permettent de limiter les ressources (CPU, mémoire, réseaux, etc…) qu’un process pourra utiliser. Les namespaces permettent de définir ce que les process peuvent voir, assurant ainsi une segmentation des ressources.
Les namespaces Linux sont des visions d’une ressource unique du noyau, par exemple le Hostname est une ressource unique pour l’ensemble des processus qui tournent sur un même Hôte. Si un process modifie le Hostname, seuls les process qui tournent dans le même namespace seront conscient de cette modification.

Il existe plusieurs types de linux Namespaces, celui qui nous intéresse ici est le Network Namespace (NNS dans les schémas).

Un Network Namespace possède sa propre table de routage, ses propres interfaces et une configuration de politique iptable dédiée. Seuls les process associés à ce Network namespace seront conscients de ces éléments.
Pour les gars réseau comme moi, cela s’apparente à une VRF.


Un Network Namespace existe par défaut, le Root Network Namespace et est utilisé par l’OS de façon globale.
Pour continuer avec les comparaisons dans le réseau « physique », cela peut nous faire penser à la GRT (Global Routing Table) sur un routeur.

![Linux Root Network Namespace](assets/img/Diapositive4.jpeg)

La création d’un nouveau Network Namespace ajoute une instance spécifique, segmentée du Root Network Namespace, avec une interface Loopback, une table de routage et une table iptable.

![Pod Network Namespace](assets/img/Diapositive5.jpeg)

#### Continuons avec les communications réseau dans Kubernetes.

Communication Containers vers Containers au sein du même Pod
Un Pod Kubernetes est un ensemble de containers qui partagent un Network Namespace commun.
Ces containers partagent donc la même POD-IP.
Il est possible d’utiliser l’adresse Localhost comme destination pour les communications entre les containers du même Pod avec un port de transport différent pour chaque container.

![Communication Container to Container dans un Pod](assets/img/Diapositive6.jpeg)

### Communication Pod à Pod
Le Pod contient une IP réelle (POD-IP) et l’utilise pour pouvoir communiquer avec un autre Pod, qu’il soit sur le même Node, ou sur un Node différent dans le même cluster.
Cependant les adresses IP des Pods n’appartiennent pas au même Network Namespace.
Linux fournit une ressource, appelée Linux Virtual Ethernet Device ou veth, qui se compose d’une paire d’interfaces virtuelles. Ces deux interfaces virtuelles permettent de réaliser une connexion directe entre deux Network Namespaces (sous forme de Tunnel).
Cette veth met en relation le Network Namespace du Pod et le Root Network Namespace.

![Veth interfaces](assets/img/Diapositive7.jpeg)

Ok, tout va bien, un Pod A peut accéder au Root Network Namespace, et un Pod B peut y accéder également. Mais comment se fait la communication transitive entre le Pod A et le Pod B ?
C’est ici qu’intervient le bridge. L’hôte Linux peut créer un Bridge entre les deux interfaces d’extrémité des veth coté Root Network Namespace correspondantes aux deux Pods.

![Communication Pod to Pod via Linux Bridge dans le Root Network Namespace]({{site.baseurl}}/assets/img/Diapositive8.jpeg)

La continuité de niveau 2 est établie entre les deux Pods par l’intermédiaire d’un Bridge qui se situe dans le Root Network Namespace. La suite nous est plus familière, le Bridge Linux se comporte comme un Switch Ethernet classique, en inspectant les entêtes de niveau 2 et en transmettant ou pas, les trames vers l’adresse MAC de destination.

Dans le prochain article, j’expliquerai les communications Service-to-Pod et Exterieur-to-services.
