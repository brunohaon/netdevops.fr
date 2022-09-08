---
title:  "K8S - Installation du Lab"
categories: "Kubernetes"
---

## K8S - Installation du Lab

Dans cet article, je vais détailler l’environnement du lab utilisé dans les tutos Kubernetes et comment l’installer sur une VM pour que vous puissiez réaliser les exercices.

### Lab NetDevOps
Le lab NetDevOps tourne sur un environnement Ubuntu 20.04 server LTS avec une distribution Kubernetes MicroK8s.
Pourquoi ce choix ? Juste par simplicité d’installation et de pouvoir rapidement utiliser Kube sans se prendre la tête avec son installation. D’autres distributions peuvent fonctionner comme K3S ou minikube, mais pour que les exercices soient utilisables, je vous conseille d’utiliser les mêmes distributions et versions.

Le lab d’origine ne contient que des briques fonctionnelles pour faire tourner la plateforme, les applications pourront être ajoutées ensuite dans les articles tutos.

### Installation de l’OS
Comme indiqué ci-dessus, le système d’exploitation est Ubuntu 20.04 server LTS. Pour l’installer, je vous laisse trouver la documentation sur le site officiel.

Installez l’OS sur une VM avec 2 CPU, 4 GB de RAM et un disque dur de 50 GB. Pour les exercices avec Istio, il est recommandé de mettre 8 GB de RAM.

N’installez aucune extension, à part OpenSSH server.

Une fois l’installation terminée, faite un Update/Upgrade et rebootez.

```bash
sudo apt update
sudo apt upgrade
reboot
```

### Installation de Microk8s
Pour installer Microk8s il suffit de copier la ligne ci-dessous:

```bash
sudo snap install microk8s –classic
```

Lorsque l’installation est terminée, je vous suggère de rajouter deux alias dans le fichier .bashrc de votre répertoire HOME:

```bash
vim ~/.bashrc
    # sous la ligne  alias l='ls -CF'
alias k='sudo microk8s kubectl'
alias kubectl='sudo microk8s kubectl'
<esc>:wq
```

à partir de maintenant vous pouvez utiliser la commande k à la place de kubectl dans le terminal linux.

### Installation des extensions
MicroK8s est installé et pour que notre lab soit fonctionnel, il faut activer des add ons dans microk8s. Pour cela copiez la ligne ci-dessous:

```bash
sudo microk8s enable dns registry storage ingress
```

Nous expliquerons, plus tard dans chaque article tuto, à quoi servent ces add ons.

### Félicitations
Voilà, votre lab est opérationnel pour suivre tous les tutos proposés sur ce blog.


