---
title: Exertis
description: 
published: true
date: 2024-08-06T13:18:47.873Z
tags: 
editor: markdown
dateCreated: 2022-12-14T09:26:20.872Z
---

# Exertis

## Environnement

### Kubernetes

Le client dispose de 3 envrionnements:

- Staging
- Prod
- Dev

Il n'y a que 2 clusters:
- Staging et Dev sont ensemble
- Prod est tout seul

### DNS

- CloudFlare: exertis-portal.fr


## Keycloak

> **Keycloak prod se trouve dans le cluster staging** car il lui faut au moins 2 noeuds. Il n'a pas été migré pour des question de simplicité.
{.is-warning}


- Le Pod Keycloak possède 2 containers:
  - Un container avec le Keycloak basique et un montage vers une PVC (theme)
  - Un container avec un simple montage vers le même PVC que précédemment pour pouvoir modifier les fichiers directement (**Le container Keycloak n'a pas pas accès root donc on ne peut pas modifier les fichiers directements)** ==> D'où la création d'un container supplémentaire pour les thèmes.
  

Les thèmes sont dans access-portal backend: https://gitlab.exertis.fr/exertis-france/access-portal/access-portal-back/-/tree/staging/docker/keycloak/exertis/login

Pour mettre à jour les themes il suffit de faire un git clone dans tmp et ensuite un rsync:

  `rsync -Wazv --delete /tmp/....  /opt/theme/... `

- Une federation est faite depuis leur AD. Une synchro automatique s'effctue toutes les 5 min.

### MFA

https://github.com/mesutpiskin/keycloak-2fa-email-authenticator

Cloner le repo puis faire:

  `mvn pavckage`

Puis sur Keycloak:

After you’ve deployed the JAR profile to the /deployments or /provider directory, you’ll need to establish and setup a Keycloak authentication flow in your realm and utilize it in a binding to use the keycloak-2fa-email-authenticator.jar

  * Configure Realm SMTP email settings in Realm/Realm Settings/Email.
  
  * Create (clone) a new flow for browser-based authentication and modify it to meet your requirements. Include an execution for “browser-email-otp-flow”
  
  * Configure the “browser-email-otp-flow” execution phase using the parameters that are most appropriate for your needs.
  
  * Set the newly established flow to “Browser Flow” in your realm’s admin console’s Authentication / Bindings tab.
  
  * Add a condition for "Collaborators"

![screenshot_2024-08-06_at_15-13-57_keycloak_administration_ui.png](/screenshot_2024-08-06_at_15-13-57_keycloak_administration_ui.png)

## Load-balancer / Ingress controller / Ingress

### Annotations /health
- Pour chaque Helm Charts des Ingress controller il faut ajouter l'annontation suivante:

``` 
  controller:
    ....
    service:
      annotations:
        service.beta.kubernetes.io/azure-load-balancer-health-probe-request-path: /healthz
``` 
Les load balancer d'Azure check sur le "/health" pour verifier que la connexion est OK. Par défault le load balancer créer des check sur "/". Il faut donc informer l'ingress controller d'envoyer l'information au load balancer. C'est pour cela qu'on rajoute cette annotation.

### Port de type TCP (Redis)

- Par défaut un ingress controller créer un load balancer sur les port 80 et 443. il arrive parfois qu'on ai besoin d'un autre service. Par exemple Redis. Pour cela il suffit d'ajouter à la fin Helm Charts:

```
tcp:
  "63791": common-prod/redis-common:6379
udp: {}
``` 

### Restiction d'IP sur un ingress


- Pour restreinte une IP particulière via un ingress il suffit d'ajouter dans le fichier YAML:

`    nginx.ingress.kubernetes.io/whitelist-source-range: "193.95.54.1/32,196.203.237.52/32,146.59.250.247"`

- Modifier dans le service associé:

`externalTrafficPolicy: Cluster`

par:

`externalTrafficPolicy: Local` 


## Application

### Common

Les applications qui sont communes à plusieurs autres application ont été mise dans le repertoire "Common" dans ArgoCD. C'est tout ce qui est utilisées par plusieurs applications en meme temps. (Exemple: PMA, Redis)

### Redis

- Il y a 2 Redis:
  - 1 Redis commun à toute l'application (celui dont le port 63791 est externe)
  - 1 Redis specifique à access-portal
  

### PMA

Pour deployer PHPMyadmin sur un ingress il faut ajouter la variable d'environnement `PMA_ABSOLUTE_URI` dans le deployment avec exactement la même URI que celle de l'ingress


### Redirect www

Pour faire une redirection sur ingress:

`nginx.ingress.kubernetes.io/configuration-snippet: return 302 https://access.exertis-portal.fr/;`


Pour que cela fonctionne il faut activer les snippet-annoations (configMap):

`allow-snippet-annotations: true`

### Package registry

Certains paquets ont été créer par les developpeurs. Ils ont été mis sur le registry Gitlab.

Ils sont dans: https://gitlab.exertis.fr/exertis-france/common/template/-/packages

### Connexion Azure via CLI

  `az login --tenant 5b7cb417-ee00-4876-ab2b-81fab4a0c984`