---
title: Backup Borg
description: 
published: true
date: 2024-08-31T07:59:34.189Z
tags: 
editor: markdown
dateCreated: 2024-01-15T16:31:01.989Z
---


# Backup Database


on a préparé un script Bash automatisé effectue une sauvegarde de bases de données PostgreSQL ou MySQL et utilise BorgBackup pour compresser et stocker les sauvegardes dans un service de stockage distant backblaze ou S3 en utilisant rclone. 

URL : https://gitlab.fondative.com/si/backups.git

### Objectif
Le script vise à automatiser le processus de sauvegarde des bases de données PostgreSQL ou MySQL, utilisant BorgBackup pour compresser et stocker les sauvegardes dans un service de stockage distant via rclone.

### Configuration
Le script utilise des variables d'environnement pour configurer les paramètres de la base de données, du système de stockage distant et d'autres options.

- **DB_TYPE**: Type de base de données (postgres ou mysql).
- **DB_HOST**: Adresse de l'hôte de la base de données.
- **DB_USER**: Nom d'utilisateur de la base de données.
- **DB_PASSWORD**: Mot de passe de la base de données.
- **PORT**: Port de connexion à la base de données.
- **BUCKET_TYPE**: Type de stockage distant ( s3,b2).
- **BUCKET_NAME**: Nom du compartiment ou du répertoire dans le stockage distant.
### Déroulement du Script
1. **Création du Nom de Sauvegarde**
Génère un nom de sauvegarde unique basé sur la date et le nom de l'espace de noms.

2. **Configuration BorgBackup**
Définit les options de BorgBackup, y compris les statistiques et la compression.

3. **Création des Répertoires de Sauvegarde**
Crée les répertoires de sauvegarde local et métadonnées Borg.

4. **Sauvegarde de la Base de Données**
Effectue la sauvegarde en fonction du type de base de données (PostgreSQL ou MySQL).
Envoie un e-mail en cas d'échec de la sauvegarde.

5. **Configuration Rclone**
Vérifie l'existence de compartiments/répertoires distants via Rclone.
Initialise BorgBackup si le compartiment/répertoire n'existe pas.

6. **Création de la Sauvegarde BorgBackup**
Crée une nouvelle sauvegarde BorgBackup avec le nom généré.
Envoie un e-mail en cas d'échec de la création de la sauvegarde.

7. **Gestion des Sauvegardes**
Effectue une prune sur les sauvegardes Borg pour maintenir une politique de rétention.
Synchronise les métadonnées Borg avec le stockage distant via Rclone.
Envoie un e-mail en cas d'échec de la synchronisation.

**Notifications par E-mail**
Le script peut envoyer des notifications par e-mail en cas d'échec lors de la sauvegarde, de la création BorgBackup ou de la synchronisation avec le stockage distant.

### **Remarques**
Le script utilise les commandes pg_dump pour PostgreSQL et mysqldump pour MySQL pour effectuer les sauvegardes.
La configuration de l'e-mail utilise la commande nc pour se connecter au serveur SMTP et envoyer des e-mails.
### Dépendances
BorgBackup
Rclone
PostgreSQL Client (pour les sauvegardes PostgreSQL)
MySQL Client (pour les sauvegardes MySQL)
netcat (nc)
### Exécution

##### **Environnement Kubernetes (CronJob)**
Le script de sauvegarde peut être planifié en tant que CronJob dans un cluster Kubernetes. Le CronJob ci-dessous exécute le script de sauvegarde chaque jour à minuit.

**Remarques:**

Assurez-vous que les variables d'environnement nécessaires sont correctement configurées dans le CronJob.
Les volumes sont montés pour accéder aux configurations (rclone, backups, borg).

#### Environnement Hôte (Docker avec Cron)

Le script peut également être exécuté sur un environnement hôte en utilisant un conteneur Docker planifié par Cron. La commande Docker ci-dessous crée un conteneur qui exécute le script chaque jour à minuit.


> docker run -d \
>   --name backup \
>   -p 6000:6000 \
>   --restart always \
>   --env-file .env \
>   --network debian_default\
>   -v /opt/backups/db-backup/host-env/backups:/backups \
>   -v /opt/backups/db-backup/host-env/rclone:/root/.config/rclone \
>   -v /opt/backups/db-backup/host-env/borg:/borg \
>   registry.fondative.com/fondative/backup:latest \
>   /bin/sh -c ". /backups/backup.sh"

**Remarques:**

Assurez-vous que le fichier .env contient les variables d'environnement nécessaires pour connecter au base de données.
Les volumes sont montés pour accéder aux sauvegardes, aux configurations rclone et borg.
Le conteneur est configuré pour être relancé automatiquement et expose le port 6000 (ajustez selon les besoins).

**Automatisation avec Cron sur l'Hôte**
> Automatisez la création du conteneur en ajoutant une tâche cron pour exécuter la commande ci-dessus à minuit avec la commande crontab -e 
{.is-warning}



> **0 0 * * * docker run --rm --env-file /chemin/vers/.env -v /opt/backups/db-backup/host-env/backups:/backups -v /opt/backups/db-backup/host-env/rclone:/root/.config/rclone -v /opt/backups/db-backup/host-env/borg:/borg registry.fondative.com/fondative/backup:latest /bin/sh -c ". /backups/backup.sh"**
{.is-info}




# Backup Files


on a préparé un script Bash automatisé effectue une sauvegarde  de fichiers en utilisant BorgBackup et Rclone pour stocker les sauvegardes dans un service de stockage distant en utilisant rclone

URL : https://gitlab.fondative.com/si/backups.git

### Objectif

Ce script Bash vise à automatiser la sauvegarde de fichiers en utilisant BorgBackup et Rclone pour stocker les sauvegardes dans un service de stockage distant.

### Configuration
Le script utilise des variables pour configurer les paramètres de la sauvegarde, du stockage distant, et d'autres options.

- **BORG_META_DIR**: Répertoire des métadonnées BorgBackup.
- **BUCKET_TYPE**: Type de stockage distant (e.g., s3, swift).
- **BUCKET_NAME**: Nom du compartiment ou du répertoire dans le stockage distant.
- **FILE**: Liste des fichiers à sauvegarder.
- **BORG_OPTIONS**: Options supplémentaires pour BorgBackup.
### Déroulement du Script

1. **Création du Répertoire des Métadonnées**
Crée le répertoire des métadonnées BorgBackup.

2. **Configuration E-mail**
Configure les paramètres nécessaires pour l'envoi d'e-mails.

3. **Recherche des Anciens Buckets**
Utilise Rclone pour lister les sauvegardes existantes.

4. **Initialisation ou Clonage des Buckets**
Initialise un nouveau bucket BorgBackup s'il n'existe pas.
Clone les fichiers existants si le bucket existe déjà.

5. **Sauvegarde des Fichiers**
Itère sur la liste des fichiers à sauvegarder.
Crée un nouveau Backup  pour chaque fichier.
Envoie un e-mail en cas d'échec de la sauvegarde.

6. **Gestion des Sauvegardes**
Effectue une prune sur les sauvegardes Borg pour maintenir une politique de rétention.
Synchronise les métadonnées Borg avec le stockage distant via Rclone.
Envoie un e-mail en cas d'échec de la synchronisation.

**Notifications par E-mail**
Le script peut envoyer des notifications par e-mail en cas d'échec lors de la sauvegarde, de la création BorgBackup ou de la synchronisation avec le stockage distant.

### Dépendances
BorgBackup
Rclone
netcat (nc)
### Exécution

##### **Environnement Kubernetes (CronJob)**
Le script de sauvegarde peut être planifié en tant que CronJob dans un cluster Kubernetes. Le CronJob  exécute le script de sauvegarde chaque jour à minuit.

**Remarques:**

Assurez-vous que les variables d'environnement nécessaires sont correctement configurées dans le CronJob.
Les volumes sont montés pour accéder aux configurations (rclone, backups, borg).

#### Environnement Hôte (Docker avec Cron)

Le script peut également être exécuté sur un environnement hôte en utilisant un conteneur Docker planifié par Cron. La commande Docker ci-dessous crée un conteneur qui exécute le script chaque jour à minuit.


> docker run -d \
>   --name backup \
>   -p 6000:6000 \
>   --restart always \
>   --env-file .env \
>   --network debian_default\
>   -v /opt/backups/db-backup/host-env/backups:/backups \
>   -v /opt/backups/db-backup/host-env/rclone:/root/.config/rclone \
>   -v /opt/backups/db-backup/host-env/borg:/borg \
>   registry.fondative.com/fondative/backup:latest \
>   /bin/sh -c ". /backups/backup.sh"

**Remarques:**

Assurez-vous que le fichier .env contient les variables d'environnement nécessaires.
Les volumes sont montés pour accéder aux sauvegardes, aux configurations rclone et borg.
Le conteneur est configuré pour être relancé automatiquement et expose le port 6000 (ajustez selon les besoins).

**Automatisation avec Cron sur l'Hôte**
> Automatisez la création du conteneur en ajoutant une tâche cron pour exécuter la commande ci-dessus à minuit avec la commande crontab -e 
{.is-warning}



> **0 0 * * * docker run --rm --env-file /chemin/vers/.env -v /opt/backups/db-backup/host-env/backups:/backups -v /opt/backups/db-backup/host-env/rclone:/root/.config/rclone -v /opt/backups/db-backup/host-env/borg:/borg registry.fondative.com/fondative/backup:latest /bin/sh -c ". /backups/backup.sh"**
{.is-info}





# Notification


On utilise pour les notification une fonction qui permet de se connecter au serveur mail distant et envoyer la  partie du serveur en question et non pas de l'instance locale.


La fonction ci-dessous définit les variables nécessaires à l'envoi du mail ensuite faire la connexion au serveur mail distant à partir duquel on souhaite envoyer la notification(et non pas à partir de l'instance de qui va subir le backup).

```
        send_email() {
         local SUBJECT=$1
         local BODY=$2
         local TO="ops@fondative.com"
         local SMTP_SERVER="email.fondative.com"
         local SMTP_PORT="587"
         local FROM="no-reply@fondative.com"
         local SMTP_USER="no-reply@fondative.com"
         local SMTP_PASSWORD="HKIn2dUpfl6HojVxpKV0sJqEfReNiMzN"


         # Connect to the SMTP server using netcat (nc)
         (echo "EHLO example.com";
         sleep 1;
         echo "AUTH PLAIN";
         sleep 1;
         echo -n "$SMTP_USER" | base64
         #sleep 1
         echo -n "$SMTP_PASSWORD" | base64
         sleep 1
         echo "MAIL FROM: <$FROM>"
         sleep 1
         echo "RCPT TO: <$TO>"
         sleep 1
         echo "DATA"
         sleep 1
         echo -e "Subject: $SUBJECT\nFrom: $FROM\nTo: $TO\n";
         echo "$BODY"
         echo ".";
         sleep 1;
          echo "QUIT") | nc $SMTP_SERVER $SMTP_PORT
         }

```

Il y en a 3 phases de notifications qui figurent dans le script
- Notification relatives au dump de la base de donnée 
- Notification lors des actions de récupération/transfert de rclone
- Notification lors des actions de backup/transfert à l'aide de l'outil de backup Borg


### Notification du backup de la base base de donnee
Dans cette partie, Selon le type de la base de donnée un backup est effectué par pg_dump/mysql_dump.
Si l'opération est erronée, un mail de notification indicative  est envoyé.

```
        case $DB_TYPE in
          postgres)
            export PGPASSWORD=$DB_PASSWORD; pg_dump -h $DB_HOST -U $DB_USER -p $PORT $DB > $BACKUP_DIR/dump-$(date +%Y-%m-%d).sql
            if [ $? -eq 0 ]
            then
               echo "Dump created successfully"
               SUCCESS=1
            else
               echo "Dump failed"
               SUBJECT="Error $NAMESPACE_NAME: PostgreSQL Dump Failed"
               BODY="The PostgreSQL dump command failed for $NAMESPACE_NAME - $DB_HOST."
               send_email "$SUBJECT" "$BODY"
               echo "Type de base de données non pris en charge : postgresql"
               exit 1
            fi
            ;;
          mysql)
            mysqldump -h $DB_HOST -u $DB_USER -p$DB_PASSWORD $DB > $BACKUP_DIR/dump-$(date +%Y-%m-%d).sql
            if [ $? -eq 0 ]
            then 
               echo "Dump created successfully"
               SUCCESS=1
             else
               echo "MYSQL Dump failed"
               SUBJECT="Error: MYSQL Dump Failed"
               BODY="The MYSQL dump command failed for $NAMESPACE_NAME - $DB_HOST."
               send_email "$SUBJECT" "$BODY"
               echo "Type de base de données non pris en charge : postgresql"
               exit 1
            fi
            ;;
          *)
            echo "Type de base de données non pris en charge : $DB_TYPE"
            exit 1
            ;;
```

### Notification du Rclone 

En cas ou l'opération Rclone de lecture à partir de la destination de backup est erronée, un mail de notification est également envoyé indiquant en plus le message d'erreur résultant.
```
            echo "Searching for older buckets..."
            output=$(rclone lsf $BUCKET_TYPE: 2>&1)
            if [ $? -eq 1 ]
            then
               echo "Rclone backups listing operation failed"
               SUBJECT="Error $NAMESPACE_NAME : Rclone backups listing operation Failed"
               BODY="The Rclone lsf command failed $NAMESPACE_NAME - $DB_HOST : $output"
               send_email "$SUBJECT" "$BODY"
               exit 1

```

### Notification de l'operation de l'outil Borg

Un backup est lancé à l'aide de l'outil borg, si l'opération est erronée, un mail de notification contenant le message d'erreur retourné est envoyé.
```
           echo "Backup Started..."
           error_message_borg=$(borg create ${BORG_OPTIONS} $BORG_META_DIR::${BACKUP_NAME} $BACKUP_DIR  2>&1)
           if [ $? -eq 0 ]
           then
               echo "Sending backup on $BUCKET_TYPE"
               borg prune --keep-within 7d --keep-weekly 1 --keep-monthly 1 --keep-yearly 1  $BORG_META_DIR
               rclone sync $BORG_META_DIR $BUCKET_TYPE:$BUCKET_NAME/Database
               if [ $? -eq 0 ]
               then
                   echo "Backup synced on $BUCKET_TYPE successfully"

               else
                   echo "Backup not synced on $BUCKET_TYPE"
                   exit 1
               fi     
           else 
               echo "Backup failed!" 
               SUBJECT="Error $NAMESPACE_NAME : Backup failed !"
               BODY=" Backup failed $NAMESPACE_NAME - $DB_HOST:$error_message_borg"
               send_email "$SUBJECT" "$BODY"       
               exit 1
           fi
```

# Restore

- List archives:

`sh /restore/restore.sh`

- List archives content:

`borg list  /backups/borg-meta::site-fondative-v2-staging-DB-2024-08-26-00:00:02`

```
drwxr-xr-x root   root          0 Mon, 2024-08-26 00:00:02 backups/data
-rw-r--r-- root   root   509956922 Mon, 2024-08-26 00:00:36 backups/data/dump-2024-08-26.sql
```

- Restore:

`borg extract -v --list /backups/borg-meta::site-fondative-v2-staging-DB-2024-08-26-00:00:02`


# Troubleshooting


En cas d'échec du script avec le message "Object with key XXXXXXX not found in repository" et lors de l'exécution de la commande "borg check --show-rc", signalant que le bloc de métadonnées de l'archive avec l'ID XXXXX est manquant, il est nécessaire d'utiliser la commande
``` "borg check --show-rc --repair" ``` 
pour remédier à la situation. Ensuite, il est recommandé de relancer une synchronisation vers le référentiel en utilisant l'outil rclone.
``` rclone sync $BORG_META_DIR $BUCKET_TYPE:$BUCKET_NAME/Database ```
