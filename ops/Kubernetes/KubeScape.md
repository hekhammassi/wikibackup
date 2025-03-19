---
title: KubeScape Operations
description: 
published: true
date: 2025-03-19T07:39:25.997Z
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
#### Quels **standards de sécurité** allons-nous suivre ? (OWASP, CIS Benchmarks...)  
#### Quels sont les **risques spécifiques** à nos clusters et à nos workloads ?  
#### Quel est notre **objectif principal** avec Kubescape ? (Audit, conformité, surveillance continue...)  

## 3. Définir une Stratégie de Sécurité  
####  Quelles sont les **priorités en matière de contrôles de sécurité** à implémenter ? (RBAC, Network Policies, vulnérabilités des images...)  
####  Jusqu’à quel point notre **politique de sécurité** doit-elle être stricte tout en préservant la productivité ?  
####  Définirons-nous des **critères d’acceptation côté développement** pour garantir la sécurité des applications déployées sur nos clusters ?  

## 4. Implémentation et Intégration de Kubescape  
####  Où allons-nous **intégrer Kubescape** ?  
  - **Dans notre workflow CI/CD** pour sécuriser les déploiements en continu ?  
  - **Dans nos Helm Charts** pour s’assurer que nos applications respectent les bonnes pratiques de sécurité ?  

## 5. Exploitation des Résultats et Prise de Décision  
- Comment allons-nous **gérer les vulnérabilités identifiées** ? (Priorisation, correction, suivi...)  
- Comment allons-nous **sensibiliser et former les équipes** à l’interprétation et à l’exploitation des analyses de sécurité ?





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

