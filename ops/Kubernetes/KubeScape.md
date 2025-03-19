---
title: KubeScape Operations
description: 
published: true
date: 2025-03-19T10:13:28.521Z
tags: 
editor: markdown
dateCreated: 2025-03-18T13:53:52.745Z
---

# Mise en Place de Kubescape pour la Sécurité de nos Clusters Kubernetes

## 1. Découverte de l’outil  
- Comment pouvons-nous tirer parti de **Kubescape** pour améliorer la sécurité de nos clusters ?


####  **Kubescape Key Features**  

- **Manifest File Scanning**  
  Analysez les erreurs de configuration dès l'étape de soumission du fichier manifeste.

- **IDE & CI/CD Integration**  
  -- VSCode, Lens  
  -- GitLab CI/CD, GitHub

- **Cluster Scanning**  
  Analysez les clusters Kubernetes à la recherche de vulnérabilités, d'erreurs de configuration et de problèmes de sécurité...

- **Security Frameworks Supported**  
  -- NSA-CISA Kubernetes Hardening Guidance  
  -- MITRE ATT&CK  
  -- SOC 2, CIS Benchmarks  
  - **N'inclut pas OWASP**  

- **YAML & Helm Chart Validation**  
  Aucun cluster actif requis pour la validation.

- **Kubernetes Hardening**  
  -- Identification et correction des erreurs de configuration/vulnérabilités  
  -- Analyses manuelles, récurrentes ou déclenchées par des événements



#### Quel est le **niveau minimal de bonnes pratiques** que nous devons mettre en place ?  

## 2. Comprendre l’Environnement et les Objectifs  
####  Quel est le **niveau actuel de posture de sécurité** de nos clusters Kubernetes ?
apres un premier scan
NSA Score: 61.12%
MITRE Score: 72.92%
Plusieurs contrôles échoués, notamment sur les limites CPU/mémoire et des misconfigurations.

#### Quels **standards de sécurité** allons-nous suivre ? (OWASP, CIS Benchmarks...)
NSA, MITRE, CIS AKS sont les cadres utilisés dans le scan.
#### Quels sont les **risques spécifiques** à nos clusters et à nos workloads ?
Absence de limites CPU/mémoire -> risque de surcharge et déni de service
Permissions excessives (RBAC)

Des utilisateurs ou des services pourraient avoir des privilèges administratifs inutiles -> Risque d’escalade de privilèges et d'accès non autorisé.

Absence de Network Policies -> Pods peuvent communiquer librement entre eux + Risque de lateral movement en cas de compromission d’un pod.

Utilisation d’images non signées ou vulnérables -> Risque d’exécution d’images contenant des failles connues.
 
Volumes montés avec accès en écriture -> Un conteneur compromis peut modifier des données persistantes = Risque de corruption des données.

Absence de Pod Security Context -> Conteneurs peuvent s'exécuter avec des privilèges root ce qui augmente la surface d’attaque en cas de compromission.

Absence de contrôle d’entrée/sortie (Ingress/Egress) -> Les applications peuvent communiquer avec des services externes non autorisés.


#### Quel est notre **objectif principal** avec Kubescape ? (Audit, conformité, surveillance continue...)  

## 3. Définir une Stratégie de Sécurité  
####  Quelles sont les **priorités en matière de contrôles de sécurité** à implémenter ? (RBAC, Network Policies, vulnérabilités des images...)
- Appliquer RBAC strict.
- Implémenter des Network Policies.
- Corriger les failles des images détectées.

####  Jusqu’à quel point notre **politique de sécurité** doit-elle être stricte tout en préservant la productivité ?  
####  Définirons-nous des **critères d’acceptation côté développement** pour garantir la sécurité des applications déployées sur nos clusters ?  

## 4. Implémentation et Intégration de Kubescape  
####  Où allons-nous **intégrer Kubescape** ?
- Dans le cluster en tant qu’Operator pour un scanning continu.
- Dans le CI/CD pour bloquer les déploiements non conformes.
- Dans Helm Charts pour garantir le respect des standards dès la conception.

## 5. Exploitation des Résultats et Prise de Décision  
#### Comment allons-nous **gérer les vulnérabilités identifiées** ? (Priorisation, correction, suivi...)
Priorisation des corrections en fonction de la sévérité.
Correction des erreurs critiques en priorité.
Sensibilisation des équipes sur les bonnes pratiques et l'interprétation des rapports.




# Implementation et tests:

On peut l'installer comme un package linux
mais on peut aussi 
- VS Code Extension: Scan YAML files while writing them
- Lens Extension
- k8s operator: https://github.com/kubescape/helm-charts?tab=readme-ov-file

Overview du resultat
![kubescape_1.png](/kubescape_1.png)
![kubescape_2.png](/kubescape_2.png)

on peut chosir le format du resultat
[results.pdf](/files/results.pdf)

on peut faire le scan par namespace
[results_argocd.pdf](/files/results_argocd.pdf)

