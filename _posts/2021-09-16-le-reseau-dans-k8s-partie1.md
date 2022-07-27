---
title:  "Le Réseau dans K8S - partie 1"
---

## K8S - Le Réseau dans K8S - partie 1

Les ingénieurs réseaux sont habitués à travailler avec des appliances physiques pour assurer les différentes fonctions liées à l’interconnexion des périphériques.
Les containers tournent sur des hôtes Linux, il est important de comprendre comment fonctionne le réseaux au sein des hôtes, en mode intra cluster mais aussi inter-cluster.
Avec cet article, je vais essayer d’expliquer ces mécanismes dans un environnement Kubernetes en m’appuyant sur de nombreuses sources, particulièrement sur l’article de Kevin Sookocheff: [« A Guide to the Kubernetes Networking Model »](https://sookocheff.com/post/kubernetes/understanding-kubernetes-networking-model/) que je vous recommande de lire.
La partie 1 présente les éléments de base de Kubernetes et des composants réseaux qui assurent la création des containers sous Linux.