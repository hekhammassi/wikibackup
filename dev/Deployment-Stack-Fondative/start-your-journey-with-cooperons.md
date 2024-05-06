---
title: start-your-journey-with-cooperons
description: 
published: true
date: 2024-05-06T14:50:46.209Z
tags: 
editor: markdown
dateCreated: 2023-08-03T21:50:43.385Z
---

# Guide de Préparation de l'Environnement de Développement
Ce guide détaillé vous accompagnera dans la mise en place d'un environnement de développement optimal pour vos projets. Suivez ces étapes pour configurer votre environnement en toute simplicité.

## Étape 1 : Clonage des Répertoires

**NB: il faut ajouter ton utlisateur dans le projet stack pour que tu puisse cloner le repo
**
   Créez un nouveau répertoire dédié à votre projet et clonez les dépôts contenant le code source requis .
```
  mkdir /home/user/cooperons
  cd /home/user/cooperons
  git clone https://git.cooperons.com/cooperons/cooperons-api.git
  git clone https://git.cooperons.com/cooperons/cooperons-2.git
  git clone https://git.cooperons.com/cooperons/cooperons-transform.git
  git clone https://git.cooperons.com/stack/cooperons.git
  
```

## Étape 2 : Personnalisation du Fichier .env

  Déplacer le fichier docker-compose.yaml et .env cloné (repertoire cooperons) vers le repertoire principale et modifiez les valeurs en fonction de vos préférences et de votre configuration.
  ```
  mv cooperons/.env .
  mv cooperons/docker-compose.yml .
  
```

## Étape 3 : Intégration des Fichiers .env et environment.dev.ts  dans le Code

  Ajoutez les valeurs du votre fichier **.env** dans **cooperons-api** et **environment.dev.ts** sous le path **cooperons-2/src/environments**
Il est nécessaire d'établir une connexion entre le backend et la base de données en associant les variables correspondantes dans le fichier .env :
```
DATABASE_URL=postgres://stack:stack@db_container_name:5432/cooperons?server_version=12
DATABASE_USERNAME=stack
DATABASE_PASSWORD=stack
DATABASE_HOST=db_container_name
DATABASE_PORT=5432
DATABASE_NAME=cooperons
```

## Étape 4 : Authentification au Registre de Fondative

   Effectuez l'authentification requise au registre de Fondative en suivant les instructions fournies.
```
 sudo docker login https://registry.fondative.com/fondative
  
```

>   URL: https://registry.fondative.com/fondative
>   Username: **fdt_registry**
>   Password: 3ks45Tq1D_f?
>{.is-success}

## Étape 5 : Lancement du Stack cooperons

  Dans le répertoire contenant le fichier docker-compose.yaml, exécutez cette commande pour démarrer le stack en mode détaché.
  ```
sudo docker-compose up -d
```

## Étape 6 : Importer la Base de Données

  N'oubliez pas d'importer la base de données par la commande suivante:
`psql -U <database_username> -h <ip_address> --port 5432 <database_name> < <database_path>`

Pour identfier l'adresse IP du host vous pouvez éxecuter la commande 
`sudo docker inspect <ID CONTAINER DB> | grep IP `

Exemple: 
`psql -U stack -h 192.168.1.56 --port 5432 cooperons</home/user/cooperons.sql`


Félicitations ! Vous avez maintenant préparé avec succès votre environnement de développement en suivant ces étapes simples et organisées. Vous êtes prêt à plonger dans le développement de votre projet dans un environnement optimisé.
Profitez-en :) !