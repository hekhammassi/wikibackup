---
title: Docker-Swarm
description: 
published: true
date: 2025-01-30T09:13:19.879Z
tags: 
editor: markdown
dateCreated: 2025-01-29T12:40:40.859Z
---

## Documentation : Préparer un Serveur pour l'Exécution de Docker Swarm

Ce document fournit un guide étape par étape pour préparer un serveur à exécuter Docker Swarm. Docker Swarm est un outil d'orchestration de conteneurs qui vous permet de gérer plusieurs conteneurs Docker en tant que cluster. Avant de configurer Docker Swarm, assurez-vous que votre serveur est correctement configuré.

---

## Étape 1 : Mettre à Jour le Serveur
Avant d'installer Docker, assurez-vous que votre serveur est à jour :
```bash
sudo apt update && sudo apt upgrade -y
```

---

## Étape 2 : Installer Docker
Docker Swarm nécessite l'installation de Docker Engine sur le serveur.

### Pour Ubuntu/Debian :
```bash
sudo apt install docker.io -y
```

---

## Étape 3 : Installer Docker Compose
```bash
sudo apt install docker-compose -y
```

---

## Étape 4 : Initialiser Docker Swarm
Une fois Docker installé et configuré, initialisez Docker Swarm.

### Initialiser le Swarm
```bash
sudo docker swarm init --advertise-addr <IP_SERVEUR>
```
Remplacez `<IP_SERVEUR>` par l'adresse IP de votre serveur.

### Vérifier l'état du Swarm
```bash
sudo docker node ls
```

### Sauvegarder le token de connexion
La sortie de la commande `docker swarm init` inclura un token permettant d'ajouter des nœuds workers. Sauvegardez ce token pour une utilisation ultérieure.

---

## Étape 5 : Ajouter des Nœuds Workers (Optionnel)
Si vous souhaitez ajouter d'autres serveurs au Swarm, suivez ces étapes sur chaque nœud worker :

1. **Installer Docker** (répétez l'Étape 2).
2. **Rejoindre le Swarm** en utilisant le jeton de l'Étape 4 :

```bash
sudo docker swarm join --token <TOKEN> <IP_MANAGER>:2377
```
Remplacez `<TOKEN>` par le token de connexion et `<IP_MANAGER>` par l'adresse IP du gestionnaire de Swarm.

---

## Étape 6 : Surveiller et Gérer le Swarm
Utilisez les commandes suivantes pour surveiller et gérer votre Docker Swarm :

### Lister les nœuds
```bash
sudo docker node ls
```

### Lister les services
```bash
sudo docker service ls
```

### Inspecter un service
```bash
sudo docker service inspect <NOM_SERVICE>
```

### Mettre à l'échelle un service
```bash
sudo docker service scale <NOM_SERVICE>=<NOMBRE_REPLICAS>
```

---
## Documentation : Préparer un Serveur pour l'Exécution de Docker Swarm

Ce document fournit un guide étape par étape pour préparer un serveur à exécuter Docker Swarm. Docker Swarm est un outil d'orchestration de conteneurs qui vous permet de gérer plusieurs conteneurs Docker en tant que cluster. Avant de configurer Docker Swarm, assurez-vous que votre serveur est correctement configuré.

---

## Étape 1 : Mettre à Jour le Serveur
Avant d'installer Docker, assurez-vous que votre serveur est à jour :
```bash
sudo apt update && sudo apt upgrade -y
```

---

## Étape 2 : Installer Docker
Docker Swarm nécessite l'installation de Docker Engine sur le serveur.

### Pour Ubuntu/Debian :
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

---

## Étape 3 : Installer Docker Compose

######  https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04
---

## Étape 4 : Initialiser Docker Swarm
Une fois Docker installé et configuré, initialisez Docker Swarm.

### Initialiser le Swarm
```bash
sudo docker swarm init --advertise-addr <IP_SERVEUR>
```
Remplacez `<IP_SERVEUR>` par l'adresse IP de votre serveur.

### Vérifier l'état du Swarm
```bash
sudo docker node ls
```

### Sauvegarder le jeton de connexion
La sortie de la commande `docker swarm init` inclura un jeton permettant d'ajouter des nœuds workers. Sauvegardez ce jeton pour une utilisation ultérieure.

---

## Étape 5 : Ajouter des Nœuds Workers (Optionnel)
Si vous souhaitez ajouter d'autres serveurs au Swarm, suivez ces étapes sur chaque nœud worker :

1. **Installer Docker** (répétez l'Étape 2).
2. **Rejoindre le Swarm** en utilisant le jeton de l'Étape 4 :

```bash
sudo docker swarm join --token <TOKEN> <IP_MANAGER>:2377
```
Remplacez `<TOKEN>` par le jeton de connexion et `<IP_MANAGER>` par l'adresse IP du gestionnaire de Swarm.

---

## Étape 6 : Déployer des Services
Une fois le Swarm configuré, vous pouvez déployer des services avec Docker Compose ou directement via les commandes Docker.
```bash
sudo docker service create --name web --replicas 3 -p 80:80 nginx
```

---

## Étape 7 : Surveiller et Gérer le Swarm
Utilisez les commandes suivantes pour surveiller et gérer votre Docker Swarm :

### Lister les nœuds
```bash
sudo docker node ls
```

### Lister les services
```bash
sudo docker service ls
```

### Inspecter un service
```bash
sudo docker service inspect <NOM_SERVICE>
```

---
## Étape 8 : Automatiser le deploiement du stack avec un pipeline

1. **Préparer le docker compose**
2. **Ajouter le clé privé dans un variable CI/CD au niveau gitlab**
3. **Ajouter le stage de deploiement**
```bash
 script:
    - apk update && apk add  openssh-client
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY_SERVER"
    - echo "$SSH_PRIVATE_KEY_SERVER" | tr -d '\r' | ssh-add - > /dev/null
    - ssh -o StrictHostKeyChecking=no USER@<IP_SERVEUR> "sudo sed -i 's|^IMAGE=.*|IMAGE=$REGISTRY_PATH/$CI_PROJECT_NAME-$CI_COMMIT_BRANCH:$CI_COMMIT_SHORT_SHA-$CI_PIPELINE_ID|' /opt/angular-nodejs-mysql/.env"
    - ssh -o StrictHostKeyChecking=no USERn@<IP_SERVEUR> "cd /opt/angular-nodejs-mysql && sudo docker compose config | yq e 'del(.name) | (.services[].ports[].published) |= tonumber' - >/tmp/docker-compose-subst.yaml "
    - ssh -o StrictHostKeyChecking=no USER@<IP_SERVEUR> "sudo docker stack deploy -c /tmp/docker-compose-subst.yaml demo-angular-$CI_COMMIT_BRANCH"
 

```
Avec ces étapes, votre serveur est prêt à exécuter Docker Swarm et à orchestrer des conteneurs efficacement.

