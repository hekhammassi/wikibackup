---
title: Plan d'action pour securiser notre cluster kubernetes
description: 
published: true
date: 2025-04-02T15:06:12.536Z
tags: 
editor: markdown
dateCreated: 2025-03-26T11:17:31.891Z
---

# **Stratégie d'implémentation : Sécurisation du Cluster Kubernetes**

L'approche sera divisée en deux phases : **contrôles de sécurité pré-déploiement: PRE-SECURITY** et **contrôles de sécurité post-déploiement: POST-SECURITY**.

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



# Implementation

## Kubescape CI/CD integration

### Integration CI

#### Scan des image docker

Dans notre stage de build d'image Docker, nous effectuons une analyse de sécurité avec Kubescape. Cette analyse permet d'échouer le pipeline selon:

- Le severity-threshold(CRITICAL, HIGH, MEDIUM ...)

```
docker-build-docker-image:
  image: docker:stable
  stage: docker-build-backend
  services:
    - docker:dind
  before_script:
    - apk add curl gcompat bash   #Installation des paquets nécessaires~~
    - curl -s https://raw.githubusercontent.com/kubescape/kubescape/master/install.sh | bash  # Installation de Kubescape
  script:
    - docker build -t "$REGISTRY_PATH/$CI_PROJECT_NAME-app-$CI_COMMIT_BRANCH:$CI_COMMIT_SHORT_SHA" .
    - kubescape scan image "$REGISTRY_PATH/$CI_PROJECT_NAME-app-$CI_COMMIT_BRANCH:$CI_COMMIT_SHORT_SHA" --severity-threshold high  # Échec du pipeline si des vulnérabilités CRITICAL sont détectées
```
> Exemple: https://gitlab.fondative.com/si/applications/tt-planning/-/jobs/28491#L233
> ou bien si:
{.is-danger}


- Le test coverage est inférieur à un seuil défini (80% dans ce cas).
```
docker-build-docker-image:
  image: docker:stable
  stage: docker-build-backend
  services:
    - docker:dind
  before_script:
    - apk add curl gcompat bash   #Installation des paquets nécessaires~~
    - curl -s https://raw.githubusercontent.com/kubescape/kubescape/master/install.sh | bash  # Installation de Kubescape
  script:
    - docker build -t "$REGISTRY_PATH/$CI_PROJECT_NAME-app-$CI_COMMIT_BRANCH:$CI_COMMIT_SHORT_SHA" .
    - kubescape scan image "$REGISTRY_PATH/$CI_PROJECT_NAME-app-$CI_COMMIT_BRANCH:$CI_COMMIT_SHORT_SHA" --compliance-threshold 80  # Échec du pipeline si la couverture des tests est inférieure à 80%
    
```

> Exemple: https://gitlab.fondative.com/si/applications/tt-planning/-/jobs/28492#L124
> {.is-success}



#### Scan du Dockerfile
Kubescape ne peut pas analyser directement un Dockerfile. Il est conçu pour scanner des images Docker, des clusters Kubernetes, ou des manifestes YAML

Trivy est la Solutions alternatives pour analyser un Dockerfile

#### Scan des yaml files
```
repo-update:
  stage: helm-repo-update
  image: debian:bullseye-slim
  variables:
    GIT_STRATEGY: none
  environment:
    name: $CI_COMMIT_BRANCH
  before_script:
    - apt update -yqq
    - apt install wget curl git -yqq
    - wget https://github.com/mikefarah/yq/releases/download/v4.20.2/yq_linux_amd64 -O /usr/bin/yq && chmod +x /usr/bin/yq
    - curl -s https://raw.githubusercontent.com/kubescape/kubescape/master/install.sh | bash
  script:
    - git clone https://$USERNAME_GIT:$PASSWORD_GIT@gitlab.fondative.com/si/infrastructure/cd-config.git
    - cd cd-config
    - kubescape scan ttplanning/templates/*.yaml # scan des fichier yaml
```
> Exemple : https://gitlab.fondative.com/si/applications/tt-planning/-/jobs/28504#L448
> 