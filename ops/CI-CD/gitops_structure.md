---
title: GitOps - Structure
description: 
published: true
date: 2025-03-04T07:32:14.233Z
tags: 
editor: markdown
dateCreated: 2024-07-10T05:43:45.956Z
---

# GitOps - Structure

```

APPS
├── API - backend
│   └── code source
├── APP - admin
│   └── code source
└── APP - frontend
    └── code source
CI-CD
├── ci-config
│   ├── Dockerfiles
│   │   ├── backup
│   │   │   └── Dockerfile
│   │   ├── .gitkeep
│   │   └── php
│   │       ├── Dockerfile
│   │       └── .gitkeep
│   ├── .env-global.yml
│   ├── .gitlab-ci-back.yml
│   ├── .gitlab-ci-front.yml
│   ├── .gitlab-ci-admin.yml
│   ├── .gitlab-ci.yml
│   └── README.md
├── cd-config
│   ├── applications
│   │   ├── manifests
│   │   │   └── production
│   │   │       ├── api-backend.yaml
│   │   │       ├── main.yaml
│   │   │       ├── app-frontend.yaml
│   │   │       └── app-admin.yaml
│   │   └── resources
│   │       ├── api-backend
│   │       │   ├── Chart.yaml
│   │       │   ├── templates
│   │       │   │   ├── api-backend-deployment.yaml
│   │       │   │   ├── api-backend-service.yaml
│   │       │   │   ├── ingress.yaml
│   │       │   │   ├── issuer.yaml
│   │       │   │   └── reverse-configmap.yaml
│   │       │   └── values.yaml
│   │       ├── app-frontend
│   │       │   ├── Chart.yaml
│   │       │   ├── templates
│   │       │   │   ├── frontend-deployment.yaml
│   │       │   │   ├── frontend-service.yaml
│   │       │   │   ├── ingress.yaml
│   │       │   │   ├── issuer.yaml
│   │       │   │   └── nginx-config-configmap.yaml
│   │       │   └── values.yaml
│   │       └── app-admin
│   │           ├── Chart.yaml
│   │           ├── templates
│   │           │   ├── app-admin-deployment.yaml
│   │           │   ├── app-admin-service.yaml
│   │           │   ├── ingress.yaml
│   │           │   └── issuer.yaml
│   │           └── values.yaml
│   ├── infrastructure
│   │   ├── manifests
│   │   │   └── cluster-prod
│   │   │       └── common
│   │   │           ├── argocd.yaml
│   │   │           ├── backups.yaml
│   │   │           ├── cert-manager.yaml
│   │   │           ├── main.yaml
│   │   │           ├── nginx-controller.yaml
│   │   │           ├── registry-credentials.yaml
│   │   │           ├── sealed-secrets.yaml
│   │   │           └── velero.yaml
│   │   └── resources
│   │       └── cluster-prod
│   │           ├── common
│   │           │   ├── argocd
│   │           │   │   ├── argo-cm-configmap.yaml
│   │           │   │   ├── git-repo-secrets.yaml
│   │           │   │   └── rbac-configmap.yaml
│   │           │   └── backups
│   │           │       ├── Chart.yaml
│   │           │       ├── templates
│   │           │       │   ├── borg-passphrase-secrets-sealed.yaml
│   │           │       │   ├── db-backup-cronjob.yaml
│   │           │       │   ├── db-credentials-secrets.yaml
│   │           │       │   ├── db-script-configmap.yaml
│   │           │       │   ├── rclone-config-secrets-sealed.yaml
│   │           │       │   └── smtp-credentials-secrets.yaml
│   │           │       └── values.yaml
│   │           └── production
│   │               └── registry-credentials
│   │                   └── regcred-secrets-sealed.yaml
└── README.md
```
