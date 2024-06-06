---
title: Postes Linux
description: 
published: true
date: 2024-06-06T09:21:46.041Z
tags: 
editor: markdown
dateCreated: 2023-07-17T09:05:46.868Z
---

# Configuration des postes

## Oneshot

```
curl -s -H 'Authorization:Basic Zm9uZGF0aXZlOkZvbmRhdGl2ZVRlbXBsYXRlczIwMjM=' https://dev.fondative.com/init-debian.sh | bash

```

## Verrouillage

- Cryptage du /home pour tous les laptop
- Verouillage des écrans au bout de 3 minutes
- Mise à jour automatique avec unattended-upgrades

```
sudo apt install libpam-cracklib

sudo cp /etc/pam.d/common-password /etc/pam.d/common-password.bak

sudo nano /etc/pam.d/common-password

```
```
password required pam_cracklib.so try_first_pass retry=7 minlen=10 lcredit=1 ucredit=1 dcredit=1 ocredit=1 reject_username

```

-    try_first_pass retry=N - Maximum number of retries to change password. N indicates the number. The default for this parameter is 1.
-    minlen=N - The minimum acceptable size for the new password (plus one if credits are not disabled which is the default). In addition to the number of characters in the new password, credit (of +1 in length) is given for each different kind of character (other, upper, lower and digit). The default value is 9.
-    lcredit=N - Define the maximum credit for containing lowercase letters in the password. Default value is 1.
-    ucredit=N - Define the maximum number for containing uppercase letters in the password. Default value is 1.
-    dcredit=N - Define the maximum credit for containing digits in the password. Default value is 1.
-    ocredit=N - Define the maximum credit for containing other characters in the password. Default value is 1.
-    difok=N - Define number of characters that must be different from the previous password.
-    reject_username - Restrict the users to use their name as password.


# Outils de base

```
apt install terminator git vim make zsh sublime
```

# Installation des applications sur les postes Linux

## Chrome

```
sudo sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list' 

wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add - 
```

```
sudo apt update
sudo apt install google-chrome-stable
```

## Brave

```
sudo apt install curl

sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main"|sudo tee /etc/apt/sources.list.d/brave-browser-release.list

sudo apt update

sudo apt install brave-browser
```

## Slack

```
sudo snap install slack
```

## VScode

```
sudo apt install wget gpg apt-transport-https
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
rm -f packages.microsoft.gpg
```

```
sudo apt update
sudo apt install code
```
## Docker 
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg |sudo apt-key add -


sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

apt-cache policy docker-ce

sudo apt install docker-ce

sudo usermod -aG docker $USER

### Docker Compose


mkdir -p ~/.docker/cli-plugins/

curl -SL https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose

chmod +x ~/.docker/cli-plugins/docker-compose
```


## NextCloud 

```
sudo apt install nextcloud-desktop
```
![nextcloud.png](/nextcloud.png)

![nextcloud2.png](/nextcloud2.png)



## Mise à jour automatiques

```
sudo apt install unattended-upgrades
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Decommenter la ligne:
```
  "{distro_id}:${distro_codename}-updates";
```

# Optionnel

## Teams

```
sudo snap install teams-for-linux
```

## Mysql Workbench

```
sudo snap install mysql-workbench-community
```

## PHPStorm

```
sudo snap install mysql-workbench-community
```