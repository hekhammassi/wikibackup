---
title: Gitlab LogRotate
description: 
published: true
date: 2025-03-24T13:51:10.995Z
tags: 
editor: markdown
dateCreated: 2025-03-24T13:51:10.995Z
---

## Configuration de la Rotation des Logs pour GitLab

### 1. Configurer la Rotation des Logs de GitLab

Si vous avez installé GitLab via **Omnibus**, vous pouvez configurer la rotation des logs dans `/etc/gitlab/gitlab.rb`.

#### **Modifier la Configuration de GitLab**
```bash
sudo nano /etc/gitlab/gitlab.rb
```

#### **Ajouter ou Modifier les Paramètres Suivants**
```ruby
gitlab_rails['logrotate_frequency'] = "daily"  # Rotation des logs quotidienne
gitlab_rails['logrotate_size'] = "100M"       # Taille max du fichier avant rotation
gitlab_rails['logrotate_keep'] = "7"          # Conserver 7 fichiers de logs anciens
gitlab_rails['logrotate_compress'] = true      # Compresser les logs archivés
gitlab_rails['logrotate_method'] = "copytruncate"  # Troncature tout en gardant le fichier ouvert
```

#### **Appliquer les Modifications**
```bash
sudo gitlab-ctl reconfigure
```

#### **Nettoyer Manuellement les Anciens Logs (Optionnel)**
```bash
sudo rm -rf /var/log/gitlab/*.log
```

---

### 2. Configurer la Rotation des Logs de Docker

Si GitLab fonctionne dans un **conteneur Docker**, il faut configurer la rotation des logs de Docker pour éviter qu'ils ne deviennent trop volumineux.

#### **Vérifier le Driver de Logs Actuel**
```bash
docker info | grep "Logging Driver"
```

Si la sortie est `json-file`, suivez les étapes suivantes :

#### **Modifier la Configuration du Daemon Docker**
```bash
sudo nano /etc/docker/daemon.json
```

#### **Ajouter ou Modifier les Paramètres Suivants**
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m",   // Taille max du fichier de log avant rotation
    "max-file": "3"        // Nombre de fichiers de logs à conserver
  }
}
```

#### **Redémarrer Docker pour Appliquer les Modifications**
```bash
sudo systemctl restart docker
```

#### **Nettoyer les Logs Existant (Si Nécessaire)**
```bash
truncate -s 0 /var/lib/docker/containers/*/*-json.log
```