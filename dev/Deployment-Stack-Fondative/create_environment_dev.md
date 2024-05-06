---
title: Create Environment DEV
description: 
published: true
date: 2024-05-06T09:42:42.848Z
tags: 
editor: markdown
dateCreated: 2021-03-08T13:18:57.978Z
---

# Créer son environnement de DEV 

##  Installation 

Pour simplifier la vie à tout le monde, un petit script va tout faire pour vous.

C'est à dire:
>  Installer **Docker**
>  Installer **Portainer**
>  Activer **Docker Swarm**
{.is-info}


Et c'est gratuit :)

Commencez par l'exécution de cette commande pour préparer votre environnement DEV :

`curl -s -H 'Authorization:Basic Zm9uZGF0aXZlOkZvbmRhdGl2ZVRlbXBsYXRlczIwMjM=' https://dev.fondative.com/prepare_dev_environment.sh | bash`
 
<!--- Commencez par installer GIT si ce n'est pas déjà fait: 

  `sudo apt install git`

Placer vous dans le répertoire que vous souhaitez.

> Il est important de garder ce répertoire car il sera souvent mis à jour. Mettez le donc en lieu sûr.
{.is-success}


Cloner le script d'installation:
  
  `git clone https://gitlab.fondative.com/ci/common.git`
  

Donnez les droits d’exécution au script et executez le:
```
  cd common/dev_environment/scripts/
  chmod +x prepare_dev_environment.sh
  ./prepare_dev_environment.sh
```
-->
##  Portainer 

> Rendez vous sur: http://localhost:9025 pour accéder à Portainer.
> {.is-info}


Une fois connecté vous arrivez sur l’interface Web de Portainer où il faut inscrire un nouvel utilisateur choisissez les identifiants que vous souhaitez.

Portainer vous demande ensuite où se situe votre plateforme Docker dans notre cas choisissez local :

![portainer_local.png](/portainer_local.png)

Une fois connecter en local, on va ajouter notre registry pour pouvoir pull les images automatiquement

SETTINGS --> Registries

![registry.png](/registry.png)

<!--
>   URL: https://registry.fondative.com/
>   Username: **fdt_registry**
>   Password: 3ks45Tq1D_f?
>{.is-success}
-->
>   URL: https://gitlab.fondative.com:5050/docker/dockerfiles
>   Username: **registry**
>   Password: rHzonRGoeWByxpUQxusT
>{.is-success}

<!--- (On ajoute aussi l'URL pour les templates:

SETTING -> Settings -> Use external templates

>   URL: http://templates:7000/templates.json
{.is-success}
-->

Enfin créer un répertoire qui accueillera la stack de votre projet et fait un git clone de tout le projets dont vous avez besoin (on en aura besoin pour la suite).

## Exemple:

```
  mkdir /home/mbi/cooperons
  cd /home/mbi/cooperons
  git clone https://git.cooperons.com/cooperons/cooperons-api.git
  git clone https://git.cooperons.com/cooperons/cooperons-2.git
  git clone https://git.cooperons.com/cooperons/cooperons-transform.git
```
```
  ls -l /home/mbi/cooperons
  total 2
  drwxr-xr-x  6 root root 4096 août  19 16:45 cooperons-2
  drwxr-xr-x 14 root root 4096 août  20 13:40 cooperons-api
  drwxr-xr-x 12 root root 4096 août  20 13:46 cooperons-transform
```

Enfin ajouter votre utilisateur au groupe www-data

  `sudo adduser mbi www-data`

##  Lancement de la stack Coopérons :

Allez sur "Add Templates", vous trouverez le template de la stack "Coopérons", cliquez dessus, vous obtiendrez la page suivante:

![stack_cooperons.png](/stack_cooperons.png)

  * **Name:** Mettez le nom que vous souhaitez
  * **Path of API project:** C'est le chemin du projet GIT sur votre PC pour la partie API
  * **Path of Front project:** C'est le chemin du projet GIT sur votre PC pour la partie Angular
  * **Path of Transform project:** C'est le chemin du projet GIT sur votre PC pour la partie Copperons Transform
  * **PostgreSQL database name:** C'est le nom de la base de données
  * **PostgreSQL username:** C'est le nom d'utilisateur du compte PgAdmin
  * **PostgreSQL and pgAdmin password:** C'est le mot de passe du compte PgAdmin et de la base de données


> **Si vous avez déjà une stack qui fonctionne (avec docker-compose par exemple) veuillez à bien l'éteindre --> docker-compose down.** 
{.is-warning}


> **De même si vous avez un serveur PostgreSQL sur votre PC, veuillez à bien l'éteindre également --> service postgresql stop**
{.is-warning}


> Cliquez sur "Deploy the stack"  8-)
{.is-success}


> Le temps de téléchargement des images peut prendre du temps. Environ 10 min en moyenne la première fois
{.is-info}


##  Derniers paramétrages 
###  Angular 

Sur Angular modifier les 2 paramètres suivants sur le ficher src/environment.ts:

> apiBaseUrl: 'http://api.cooperons.lo:8000/v1.0'
> frontBaseUrl: 'http://app.cooperons.lo:3200'
{.is-success}



> Une amélioration aura bientôt lieu pour que cela soit automatique
{.is-info}


### API

Copier votre fichier .env à la racine du projet.


###  BDD 

N'oubliez pas d'importer la base de données :) par la commande suivante:
`psql -U <database_username> -h <ip_address> --port 15432 <database_name> < <database_path>`

Exemple: 

`psql -U cooperons -h 192.168.1.56 --port 15432 cooperons < /home/kais/cooperons.sql`

## Problèmes fréquemment rencontrés 

###  Cas 1: Résolution rapide 


En cas de problème au niveau de la stack, si vous avez besoin de resoudre un problème en urgence sans attendre l'intervention d'une personne de l'équipe Ops le plus rapide à éffectuer est de supprimer la stack et de la recréer.

Il suffit de se rendre dans:

  * "Stacks" --> Selectionner la stack voulue --> Remove

> Cela ne supprime aucune donnée du code, il n'y aura pas besoin de refaire un composer install ni de réinstaller la base de données. Seule la partie système est touchée. En recréant la stack on se retrouve à l'état avant suppression.
{.is-info}


Pour recréer la stack, il suffit de reprendre à partir de : **Lancement de la stack**

### Cas 2: En cas d'échec du cas 1

Si la solution d'au dessus n'a pas fonctionné il faudra tout supprimer et tout refaire. Pour ce faire:

  * Supprimer la stack comme ci dessus (voire même toutes les stacks présentes)
  * Se rendre dans son dépot "**common**" et faire un "**git pull**" pour récupérer les dernières modifications.
  * Si le git pull ne passe pas, effectuer un git stash avant
  * Exécuter la commande suivante pour supprimer tous les containers présents:
```
  docker rm -f $(docker ps -a -q)
  docker volume prune
```
>  Cela supprimera tous les conteneurs sans exception
{.is-danger}


  * Relancer le script de création de la stack:
```
  cd common/dev_environment/scripts/
  chmod +x prepare_dev_environment.sh
  sudo ./prepare_dev_environment.sh
```
  * Reprendre la documentation à partir de **Portainer**



### Cas 3: Les templates n'apparaissent pas

  * Se rendre dans son dépot "**common**" et faire un "**git pull**" pour récupérer les dernières modifications.
  * Si le git pull ne passe pas, effectuer un git stash avant

  * Relancer le script de création de la stack:

```
  cd common/dev_environment/scripts/
  chmod +x prepare_dev_environment.sh
  sudo ./prepare_dev_environment.sh
```

> Cela ne supprimera rien. Le script detectera que protainer est en fonctionnement et mettra à jour simplement les templates
{.is-warning}


### Cas 4: Un des conteneur n'a pas l'air de fonctionner

L'action la plus simple est de:

  * Se rendre dans "Services" --> Cocher le service en question --> Update

La seconde option est de creuser un peu plus le problème en utilisant les commande:

  `docker ps`

Cela permet de lister le conteneurs en fonctionnement

  `docker logs id_du_container`

Pour avoir les derniers logs issues de ce conteneur.

> Si le problème n'est pas clair, vous pouvez demander de l'aide à l'équipe Ops.
{.is-info}


### Cas 5: L'image utilisée n'as pas l'air à jour

L'action la plus simple est de:

  * Se rendre dans "Services" --> Cocher le service en question --> Update + **Pull latest image version**

###  Procédures de mises à jour 

Les mises à jour seront transmises sur slack ainsi que les instuctions  afin que cette mises à jour soient prises en compte.
La procédure sera donc la suivante:
  - Mise à jour de la stack (Equipe Ops)
  - Communication de la mise à jour sur Slack. Cela comprendra:
     - Les éléments qui ont été mis à jour
     - Les instructions a exécuter afin que la mise à jour soient prise en compte

