---
title: Mail-Service-`Incident-response
description: 
published: true
date: 2024-12-19T10:52:46.596Z
tags: 
editor: markdown
dateCreated: 2024-12-18T15:43:24.118Z
---

# Mail Server Incident Response

## Saturation du volume exterieur monte sur /var/vmail

1. Arrêter les services utilisant /var/vmail

Pour éviter les conflits, arrêtez tous les services utilisant le volume /var/vmail :

```systemctl stop dovecot postfix```

Ensuite, vérifiez les processus encore actifs sur ce volume :

```lsof +D /var/vmail```

S'il y a des processus actifs, terminez-les :

```kill -9 <PID>```

2. Redimensionner le volume sur OVH à 90 Go:

- Sur l'interface OVH, naviguez vers le serveur mail gérant /var/vmail 
- Rendez-vous sur la page "Volumes" et cliquez sur "Modifier" (voir capture d'écran).
![1.png](/1.png)
- Modifiez la taille en 90 Go (voir capture d'écran).
![2.png](/2.png)
3. Ajuster le volume /var/vmail avec cfdisk

Exécutez la commande suivante pour redimensionner la partition en question(relative a /var/vmail, db1 dans ce cas) :
```sudo cfdisk /dev/sdb1```
![4.png](/4.png)
![5.png](/5.png)
![6.png](/6.png)
4. Redimensionner le système de fichiers
Appliquez la nouvelle taille au système de fichiers :
```resize2fs /dev/sda1```

5. Redémarrer les services
Une fois le redimensionnement terminé, redémarrez les services mail :
```systemctl restart dovecot postfix```

