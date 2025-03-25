---
title: Implémentation de Kyverno pour Kubernetes
description: 
published: true
date: 2025-03-25T17:21:13.263Z
tags: 
editor: markdown
dateCreated: 2025-03-25T16:43:15.570Z
---

# Introduction

Ce document a pour objectif de détailler la stratégie d'implémentation de Kyverno dans nos clusters Kubernetes, en mettant en avant les avantages attendus pour notre besoin, les bonnes pratiques à adopter ainsi que la stratégie de mise en place.

### Qu'est-ce que Kyverno ?

Kyverno est un moteur de politiques open-source qui s'intègre nativement dans Kubernetes.

Il permet de créer, gérer et appliquer des politiques pour contrôler les ressources Kubernetes. Ces politiques peuvent être utilisées pour garantir les meilleures pratiques, la sécurité et la conformité opérationnelle au sein du cluster.

Kyverno fonctionne avec des CRDs (Custom Resource Definitions), ce qui permet de gérer les politiques de manière native dans Kubernetes.

# Pourquoi implémenter Kyverno ?  

Kyverno nous permet de définir et d'appliquer automatiquement des politiques de sécurité au sein de notre infrastructure Kubernetes. À mesure que notre environnement grandit en taille et en complexité, garantir la conformité à nos exigences de sécurité devient un défi croissant.  

## 1. Sécurité Renforcée et Conformité
Grâce à Kyverno, nous pouvons établir des règles couvrant des aspects essentiels tels que :  

- **Empêcher** le déploiement d'images de conteneurs non sécurisées  
- **Garantir** que nos workloads respectent les exigences de conformité définies  
- **Gérer** les politiques réseau pour renforcer la sécurité  
- **Appliquer** des règles de chiffrement et de protection des données sensibles  

En intégrant Kyverno à notre cluster Kubernetes, nous automatisons l'application de ces politiques, réduisant ainsi le risque d'erreurs humaines et de potentielles failles de sécurité.

## 2. Gouvernance et Meilleures Pratiques  

Kyverno facilite la formalisation et l’application des meilleures pratiques de déploiement au sein de nos clusters Kubernetes.  

Par exemple, il permet de :  

- **Garantir** que toutes les ressources Kubernetes (Pods, Services, Deployments, etc.) respectent des critères de bonnes pratiques, notamment en matière de configuration reproductible et de gestion optimisée des ressources.  
- **Appliquer** des restrictions sur la configuration des ressources, comme l’imposition de limites CPU/Mémoire, afin de prévenir les défaillances liées à une allocation inefficace des ressources.


# A quoi sert ? Exemples de Cas d'utilisation avec Kyverno.

## 1. Contrôleur TTL-after-finished pour nettoyage automatique des jobs

Le contrôleur `TTL-after-finished` permet de nettoyer automatiquement les jobs lorsque le champ `.spec.ttlSecondsAfterFinished` est spécifié.

La politique suivante ajoute ce champ `.spec.ttlSecondsAfterFinished` à un job qui n’a pas de `ownerReference`, si ce dernier n'est pas déjà défini :

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-ttl-jobs
  annotations:
    policies.kyverno.io/title: Ajouter TTL aux Jobs
    policies.kyverno.io/category: Autre
    policies.kyverno.io/severity: Moyen
    policies.kyverno.io/subject: Job
    kyverno.io/kyverno-version: 1.11.1
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.23"
spec:
  rules:
    - name: add-ttlSecondsAfterFinished
      match:
        any:
        - resources:
            kinds:
              - Job
      preconditions:
        any:
          - key: "{{ request.object.metadata.ownerReferences || `[]` }}"
            operator: Equals
            value: []
      mutate:
        patchStrategicMerge:
          spec:
            +(ttlSecondsAfterFinished): 900

```
Cette politique permet d'ajouter un TTL (temps de vie après la fin) de 900 secondes (15 minutes) aux jobs qui n'ont pas de ownerReference.


### 2. Interdire l'utilisation du tag "latest" pour les images Docker

Le tag `latest` est mutable et peut entraîner des erreurs imprévues si l'image change. Une bonne pratique consiste à utiliser un tag immuable qui correspond à une version spécifique de l'image. Cette politique empêche l'utilisation du tag `latest` dans les Pods.

#### Politique pour interdire l'utilisation du tag "latest" :
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: disallow-latest-tag
  annotations:
    policies.kyverno.io/title: Disallow Latest Tag
    policies.kyverno.io/category: Best Practices
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: Pod    
spec:
  validationFailureAction: Enforce
  background: true
  rules:
  - name: require-image-tag
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      message: "Un tag d'image est requis."
      pattern:
        spec:
          containers:
          - image: "*:*"
  - name: validate-image-tag
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      message: "L'utilisation d'un tag d'image mutable, par exemple 'latest', n'est pas autorisée."
      pattern:
        spec:
          containers:
          - image: "!*:latest"
```

### 3. Exiger des Limites et des Requêtes de Ressources pour les Pods

Une bonne pratique en Kubernetes est de définir des limites et des requêtes pour les ressources (CPU et mémoire) des Pods, afin d’éviter une utilisation excessive des ressources et d’assurer la stabilité du cluster.

#### Politique pour exiger des limites et des requêtes de ressources :
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-resource-limits
  annotations:
    policies.kyverno.io/title: Exiger des Limites et des Requêtes de Ressources
    policies.kyverno.io/category: Best Practices
    policies.kyverno.io/severity: élevé
    policies.kyverno.io/subject: Pod
spec:
  validationFailureAction: Enforce
  rules:
    - name: require-limits-and-requests
      match:
        any:
        - resources:
            kinds:
              - Pod
      validate:
        message: "Les limites et les requêtes de ressources (CPU, mémoire) sont requises pour chaque conteneur."
        pattern:
          spec:
            containers:
              - resources:
                  requests:
                    cpu: "*"
                    memory: "*"
                  limits:
                    cpu: "*"
                    memory: "*"
```

### 4. Interdire les Pods Privileges (Running as Root)

Pour des raisons de sécurité, il est déconseillé de faire tourner des Pods en tant qu'utilisateur root. Cette politique empêche les Pods de s'exécuter avec des privilèges root.

#### Politique pour interdire les Pods exécutés en tant qu'utilisateur root :
```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: disallow-root-user
  annotations:
    policies.kyverno.io/title: Interdire l'Utilisateur Root
    policies.kyverno.io/category: Sécurité
    policies.kyverno.io/severity: élevé
    policies.kyverno.io/subject: Pod
spec:
  validationFailureAction: Enforce
  rules:
    - name: disallow-root-user
      match:
        any:
        - resources:
            kinds:
              - Pod
      validate:
        message: "Les Pods ne doivent pas être exécutés en tant qu'utilisateur root."
        pattern:
          spec:
            securityContext:
              runAsUser: "!0"
```

# Kyverno dans notre Tool Chain CI/CD fondative

Kyverno peut être intégré aux pipelines CI/CD et à ArgoCD de plusieurs manières :

* Validation avant déploiement : Le CLI de Kyverno peut être utilisé dans les pipelines CI/CD (GitLab CI, GitHub Actions, Jenkins, etc.) pour vérifier que les manifests Kubernetes respectent les bonnes pratiques avant leur déploiement.

* Contrôle d’admission : Kyverno agit comme un webhook validateur dans Kubernetes, bloquant les déploiements qui ne respectent pas les politiques définies. Cela empêche l’introduction de configurations non conformes dans le cluster.

* Analyse post-déploiement : Kyverno peut surveiller en continu les ressources Kubernetes en production et signaler les violations de politique. Cela permet de détecter toute dérive ou mauvaise configuration après le déploiement.

* Intégration avec ArgoCD : Kyverno peut imposer des règles aux applications déployées via ArgoCD. Si une application ne respecte pas les politiques, ArgoCD peut bloquer son déploiement ou générer des alertes, garantissant ainsi la conformité dans un workflow GitOps.

