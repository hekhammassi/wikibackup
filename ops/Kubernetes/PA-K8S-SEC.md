---
title: Plan d'action pour securiser notre cluster kubernetes
description: 
published: true
date: 2025-03-26T11:17:31.891Z
tags: 
editor: markdown
dateCreated: 2025-03-26T11:17:31.891Z
---

# **Stratégie d'implémentation : Sécurisation du Cluster Kubernetes**

L'approche sera divisée en deux phases : **contrôles de sécurité pré-déploiement** et **contrôles de sécurité post-déploiement**.

## **1. Contrôles de sécurité pré-déploiement**
### **Intégration CI/CD**
- Intégrer **Kubescape** et **Kyverno CLI** dans notre pipeline CI/CD afin de :
  - Scanner les images générées pour détecter d’éventuelles vulnérabilités.
  - Analyser les **Helm charts** et les fichiers de déploiement afin d’identifier les non-conformités et les failles de sécurité.

## **2. Contrôles de sécurité post-déploiement**
- **Déploiement de Kubescape** via son **Operator Kubernetes** en utilisant Helm.
- **Intégration avec une interface graphique** afin d’obtenir une visualisation des analyses de sécurité, tester la fonctionnalité **Live Check** et explorer d'autres fonctionnalités avancées.
- **Correction des vulnérabilités critiques** identifiées dans le cluster.

## **3. Mise en place et configuration de Kyverno**
- **Installation et configuration** de Kyverno pour renforcer la gouvernance et la conformité du cluster.
- **Identification et mise en œuvre des politiques de sécurité essentielles**, notamment :
  - Restrictions sur l’utilisation de l’utilisateur root.
  - Contraintes sur les balises (tags) des images.
  - Définition des limites de ressources.
...
- **Exploration d'autres politiques pertinentes**  pour améliorer la sécurité globale du cluster.
