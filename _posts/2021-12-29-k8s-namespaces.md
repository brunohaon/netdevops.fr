---
title:  "K8S - Les Namespaces"
categories: "Kubernetes"
---

## K8S - Les Namespaces

Dans Kubernetes, il est possible de créer des « clusters » virtuels au sein d’un cluster réel. Cela permet d’organiser les différentes ressources Kube dans des ensembles cohérents.

Les Namespaces ne segmentent pas de façon sécurisée les ressources entre elles, ils sont utilisés principalement dans des environnements multi utilisateurs/groupes avec des limitations via des quota d’utilisation des ressources.

Ils fournissent un modèle d’isolation lié aux noms des ressources. Ces derniers doivent être uniques dans un namespace, mais pas dans l’ensemble des namespaces. Les namespaces ne peuvent pas être imbriqués les uns dans les autres et chaque ressource Kubernetes ne peut se trouver que dans un seul namespace.

### Affichage des Namespaces

kubectl get namespace

Par défaut Kubernetes possède un Namespace Default qui est utilisé si vous ne spécifiez pas de Namespace à la suite d’une commande avec le suffixe -ns ou –namespace.

Il y a trois autres Namespaces par défaut:

- kube-node-lease pour les objets de bail associés à chaque nœud, ce qui améliore les performances des pulsations du nœud à mesure que le cluster évolue
- kube-public pour les utilisateurs sans authentification
- kube-system pour les objets créés par Kubernetes lui-même

### Création des Namespaces
Un Namespace peut être créé dans sa plus simple forme avec la commande `kubectl create namespace <nom-namespace>`:

kubectl create namespace graph

Bien entendu, un Namespace peut être créé via un fichier YAML:

apiVersion: v1
kind: Namespace
metadata:
  name: graph

Puis appliqué avec la commande :

kubectl apply -f graph.yaml

### DNS entre les Namespaces
Dans Kubernetes, les ressources de type pods sont liées à des services pour pouvoir échanger des informations entre elles. Lorsqu’un Pod doit communiquer avec un service dans le même Namespace, le DNS permet de résoudre le nom du service(<nom-service>) avec son adresse IP.

Exemple, le Pod grafana doit accéder au Pod influxdb pour effectuer des requêtes, il crée une connexion vers le service influxdb en utilisant son nom: « influxdb »si ils sont dans le même Namespace.

Lorsque les ressources doivent communiquer entre elles depuis des Namespaces différents, la résolution DNS se fait sur le nom complet du service cible: `<nom-service>.<nom-namespace>.svc.cluster.local`.


Pour notre exemple, le pod grafana qui est dans le namespace graph, crée une connexion vers le service influxdb qui est dans le namespace db: influxdb.db.svc.cluster.local.