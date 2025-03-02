---
title: FreeScout
description: 
published: true
date: 2025-03-02T01:27:53.724Z
tags: 
editor: markdown
dateCreated: 2025-02-28T13:02:59.669Z
---

# Installation et Mise en Place de FreeScout

Deux méthodes sont possibles pour installer FreeScout :

## 1.1 Installation via Script Automatique
Installation interactive via un script automatisé, en saisissant les données demandées (email, nom de domaine, etc.) au fur et à mesure. 
```
sudo apt install wget
wget https://raw.githubusercontent.com/freescout-help-desk/freescout/dist/tools/install.sh
chmod u+x install.sh
sudo ./install.sh
```

## 1.2 Installation via Docker-Compose

Paramétrer les variables d’environnement avec les valeurs souhaitées (nom de domaine, email admin, etc.).
```yaml
version: '2'

services:
  
  freescout-app:
    image: tiredofit/freescout
    container_name: freescout-app
    links:
    - freescout-db
    volumes:
    ### If you want to perform customizations to the source and have access to it, then uncomment this line - This includes modules
    #- ./data:/www/html
    ### Or, if you just want to use Stock Freescout and hold onto persistent files like cache and session use this, one or the other.
    - ./data:/data
    ### If you want to just keep the original source and add additional modules uncomment this line
    #- ./modules:/www/html/Modules
    - ./logs/:/www/logs
    environment:
    - VIRTUAL_HOST=support-test.fondative.com
    - VIRTUAL_NETWORK=nginx-proxy
    - VIRTUAL_PORT=80
    - LETSENCRYPT_HOST=support-test.fondative.com
    - LETSENCRYPT_EMAIL=hek@fondative.com

    - CONTAINER_NAME=freescout-app
    
    - DB_HOST=freescout-db
    - DB_NAME=freescout
    - DB_USER=freescout
    - DB_PASS=freescout

    - SITE_URL=https://support-test.fondative.com
    - ADMIN_EMAIL=hek@fondative.com
    - ADMIN_PASS=freescout
    - ENABLE_SSL_PROXY=TRUE
    - DISPLAY_ERRORS=FALSE
    - TIMEZONE=Paris/France
    networks:
      - proxy-tier
    restart: always

  freescout-db:
    image: tiredofit/mariadb
    container_name: freescout-db
    volumes:
      - ./db:/var/lib/mysql
    environment:
      - ROOT_PASS=password
      - DB_NAME=freescout
      - DB_USER=freescout
      - DB_PASS=freescout

      - CONTAINER_NAME=freescout-db
    networks:
      - proxy-tier
    restart: always

  freescout-db-backup:
    container_name: freescout-db-backup
    image: tiredofit/db-backup
    links:
     - freescout-db
    volumes:
      - ./dbbackup:/backup
    environment:
      - CONTAINER_NAME=freescout-db-backup
      - DB_HOST=freescout-db
      - DB_TYPE=mariadb
      - DB_NAME=freescout
      - DB_USER=freescout
      - DB_PASS=freescout
      - DB01_BACKUP_INTERVAL=1440
      - DB01_BACKUP_BEGIN=0000
      - DB_CLEANUP_TIME=8640
      - COMPRESSION=BZ
      - MD5=TRUE
    networks:
      - proxy-tier
    restart: always


networks:
  proxy-tier:
    external:
      name: nginx-proxy
```



# Configuration de FreeScout

## Création de la Boîte Mail (Mailbox)

Ajouter une nouvelle boîte mail dans FreeScout.
![0.png](/0.png)
![1.png](/1.png)

## Configurer l’adresse email sur laquelle les tickets seront reçus.
![2.png](/2.png)


## Configuration du SMTP (Envoi de Mails)
Paramétrer l’envoi des emails via SMTP Infomaniak dans les paramètres du mailbox.
![3.png](/3.png)

## Configuration du IMAP (Reception de Mails)

Paramétrer la reception des emails via IMAP Infomaniak dans les paramètres du mailbox.
![4.png](/4.png)

## Configurer également le SMTP dans System Settings
![5.png](/5.png)


