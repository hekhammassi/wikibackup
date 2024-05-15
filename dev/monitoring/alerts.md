---
title: Alerts
description: 
published: true
date: 2024-05-15T10:00:42.871Z
tags: 
editor: markdown
dateCreated: 2024-05-15T08:59:37.716Z
---

# Creation d'une alert Grafana
Cliquez sur Alertes et IRM -> Règles d'alerte -> + Nouvelle règle d'alerte .

Saisissez un nom pour identifier votre règle d'alerte.
![a6.png](/a6.png)
Ajoutez une requête.
![a1.png](/a1.png)
Ajoutez une ou plusieurs expressions : Pour chaque expression, sélectionnez Condition classique pour créer une règle d'alerte unique ou choisissez parmi les options Math , Réduire et Rééchantillonner pour générer une alerte distincte pour chaque série.
![a2.png](/a2.png)
Définissez le comportement d'évaluation des alertes : Utilisez l’évaluation des règles d’alerte pour déterminer la fréquence à laquelle une règle d’alerte doit être évaluée et la rapidité avec laquelle elle doit changer d’état.

![a3.png](/a3.png)
Configurez les labels et les notifications : Configurez qui reçoit une notification lorsqu'une règle d'alerte se déclenche en choisissant Sélectionner le point de contact .
![a4.png](/a4.png)
Ajoutez des annotations : Les annotations ajoutent des métadonnées pour fournir plus d'informations sur l'alerte dans votre message de notification d'alerte. 

`{{ $labels.cluster }} : Job {{ $labels.job}} failed to complete. Removing failed job after investigation should clear this alert.  `
![a5.png](/a5.png)