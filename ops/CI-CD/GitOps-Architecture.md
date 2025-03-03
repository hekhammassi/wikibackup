---
title: GitOps Architecture
description: 
published: true
date: 2025-03-03T01:03:47.895Z
tags: 
editor: markdown
dateCreated: 2025-03-02T23:13:05.323Z
---

# CI / CD Folder Structure


Continuous Integration Structure (CI)

```
// The Dockerfiles used to build the application docker images
 ├── Dockerfiles
    ├── nginx-react  // an example of an nginx-react app to build
        ├── default.conf
        └── Dockerfile
    ├── php8.2-fpm   // an example of an php-fpm app to build
        ├── consumer-data-portal.conf
        ├── consumer-pricing.conf
        ├── Dockerfile
        ├── entrypoint.sh
        ├── php.ini
        ├── README.md
        └── www.conf
    ├── reverse-proxy // an example of a reverse proxy app to build
        ├── default.conf
        └── Dockerfile
├── .gitlab-ci-back-nodejs.yml   // Continuous integration files containing the pipeline stages
├── .gitlab-ci-back-php8.2.yml
├── .gitlab-ci-back.yml
├── .gitlab-ci-front.yml
├── .gitlab-ci-web.yml

```

Continuous deployment Structure (CD)
```
├── applications
    ├── manifests
				├── Cluster-Name1
            ├── develop
                ├── main.yaml
                ├── manifest-app-1.yaml
                ├── manifest-app-2.yaml
                ├── manifest-app-3.yaml
            ├── preprod
                ├── main.yaml
                ├── manifest-app-1.yaml
                ├── manifest-app-2.yaml
                ├── manifest-app-3.yaml   
            ├── production
                ├── main.yaml
                ├── manifest-app-1.yaml
                ├── manifest-app-2.yaml
                ├── manifest-app-3.yaml
            ├── sandbox
                ├── main.yaml
                ├── manifest-app-1.yaml
                ├── manifest-app-2.yaml
                ├── manifest-app-3.yaml
            ├── staging
                ├── main.yaml
                ├── manifest-app-1.yaml
                ├── manifest-app-2.yaml
                ├── manifest-app-3.yaml

        ├── Cluster-Name2 (An example of the ApplicationSet deployment Model)
            ├── develop
                 ├── applications.yaml
                 ├── deployment_manifest.yaml
                 ├── main.yaml
            ├──production
                 ├── applications.yaml
                 ├── deployment_manifest.yaml
                 ├── main.yaml
            ├──staging
                 ├── applications.yaml
                 ├── deployment_manifest.yaml
                 └── main.yaml

    ├── ressources
        ├── Cluster-Name1
             ├── app-1
                 ├── Chart.yaml
                 ├── templates  // an example of deployment files that an application usually contains
                     ├── backend-deployment.yaml
                     ├── backend-service.yaml
                     ├── consumer-deployment.yaml
                     ├── cron-deployement.yaml
                     ├── frontend-deployment.yaml
                     ├── frontend-service.yaml
                     ├── ingress.yaml
                     ├── issuer.yaml
                     ├── reverse-proxy-deployment.yaml
                     ├── reverse-proxy-service.yaml
                     └── secret-regcred.yaml
                 ├── values-develop.yaml
                 ├── values-main.yaml
                 ├── values-production.yaml
                 ├── values-sandbox.yaml
                 └── values-staging.yaml
             ├── app-2
                 ├── Chart.yaml
                 ├── templates
                     ├── backend-deployment.yaml
                     ├── backend-service.yaml
                     ├── consumer-deployment.yaml
                     ├── cron-deployement.yaml
                     ├── frontend-deployment.yaml
                     ├── frontend-service.yaml
                     ├── ingress.yaml
                     ├── issuer.yaml
                     ├── reverse-proxy-deployment.yaml
                     ├── reverse-proxy-service.yaml
                     └── secret-regcred.yaml
                 ├── values-develop.yaml
                 ├── values-main.yaml
                 ├── values-production.yaml
                 ├── values-sandbox.yaml
                 └── values-staging.yaml
             ├── db-secrets
                 └── db-secrets.yaml
             ├── common    // deployments that are common across all environments 
                 ├── Chart.yaml
                 ├── templates
                     ├── common-deployment1.yaml
                     ├── common-deployment2.yaml
                     ├── common-ingress.yaml
                     ├── common-issuer.yaml
                     ├── common-phpmyadmin-deployment.yaml
                     ├── common-phpmyadmin-service.yaml
                     ├── common-redis-deployment.yaml
                     └── common-redis-service.yaml
                 ├── values-develop.yaml
                 ├── values-main.yaml
                 ├── values-production.yaml
                 ├── values-sandbox.yaml
                 └── values-staging.yaml

        ├── Cluster-Name2
            ├── Chart.yaml
            ├── templates
                ├── crons
                    ├── app-name
                    │   └── cron-name1.yaml
                    └── app-name2
                        └── cron-name2.yaml
                ├── envs
                    ├── app-name
                    │   ├── env-app-name-develop-configmap.yaml
                    │   ├── env-app-name-master-configmap.yaml
                    │   └── env-app-name-staging-configmap.yaml
                    ├── hitech
                    │   ├── env-app-name-2-develop-configmap.yaml
                    │   ├── env-app-name-2-master-configmap.yaml
                    │   └── env-app-name-2-staging-configmap.yaml
                ├── web-deployment.yaml
                ├── web-service.yaml
                ├── ingress.yaml
                ├── issuer.yaml
                ├── phpmyadmin-deployment.yaml
                ├── phpmyadmin-service.yaml
                ├── reverse-configmap.yaml
                ├── secret-regcred.yaml
            ├── values
                ├── app1
                    ├── values-app1-develop.yaml
                    ├── values-app1-master.yaml
                    └── values-app1-staging.yaml
                ├── app2
                    ├── values-app2-develop.yaml
                    ├── values-app2-master.yaml
                    └── values-app2-staging.yaml
```
