---
title: Kubernetes Security
description: 
published: true
date: 2025-01-30T10:05:39.636Z
tags: 
editor: markdown
dateCreated: 2025-01-30T10:05:39.635Z
---

# Renforcement de la sécurité de notre cluster Kubernetes

## Introduction

> Afin d'assurer une sécurité optimale de notre environnement Kubernetes, nous allons mettre en œuvre une stratégie combinant les outils Kubescape, Kyverno et Falco. Chacun de ces outils apporte une dimension complémentaire à notre dispositif de sécurité, permettant ainsi de détecter et de prévenir les vulnérabilités.


## Kubescape : Le gardien de la conformité

> Kubescape est un scanner de sécurité open-source conçu spécifiquement pour les clusters Kubernetes. Il permet d'évaluer la conformité de nos clusters par rapport à de bonnes pratiques et à des standards de sécurité reconnus (CIS Benchmarks, NSA, etc.).
{.is-info}


- On va tout d'abord faire l'évaluation de la posture actuelle de conformité de nos clusters en lançant une analyse en profondeur avec Kubescape pour identifier les écarts par rapport aux meilleures pratiques.
- On va également identifier, par le biais de cette analyse, les vulnérabilités communes telles que les configurations à risque, les secrets exposés, les autorisations excessives et d'autres vulnérabilités courantes.

- Finalement, on procède à la correction de ces failles, à la remédiation des vulnérabilités et à l'adaptation aux bonnes pratiques communes.



---

##  Kyverno : Le régulateur des politiques

> Kyverno est un outil de politique d'admission pour Kubernetes. 
> Il permet de définir et d'appliquer des règles personnalisées afin de contrôler les ressources qui sont créées ou modifiées dans nos clusters.
{.is-info}


Actions possibles: ce qu'on pourrait faire avec Kyverno

* On va appliquer les politiques de sécurité et imposer des règles strictes sur les noms, les étiquettes, les annotations, les autorisations et les ressources autorisées. On commence tout d'abord par les politiques et règles communes, puis on procède à l'ajout de règles personnalisées.

- On va aussi valider les manifestes en vérifiant que nos fichiers YAML correspondent aux politiques définies avant leur déploiement.


---


## Falco : Le chasseur de menaces en temps réel

> Falco est un système de détection des intrusions léger et rapide pour les conteneurs et les environnements cloud-native. 
> Il surveille les événements système en temps réel pour détecter les comportements anormaux et les attaques potentielles
{.is-info}


- On va mettre en place et configurer Falco afin de détecter des activités suspectes (une large gamme d'attaques) telles que les injections de code, les mouvements latéraux, les accès non autorisés et les manipulations de fichiers.

- On va aussi générer des alertes en temps réel lorsque des événements suspects sont détectés, permettant une réponse rapide aux incidents.

- Il y a aussi la possibilité d'intégrer Falco avec des outils SIEM afin d'avoir une détection plus approfondie ~(à voir)~.
