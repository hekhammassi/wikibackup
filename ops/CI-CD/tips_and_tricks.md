---
title: Tips And Tricks
description: 
published: true
date: 2025-03-10T12:54:53.428Z
tags: 
editor: markdown
dateCreated: 2021-03-08T13:46:08.216Z
---

# Trucs & Astuces

Vous trouverez ici toutes les pépites qu'on trouve difficilement 8-)

## Utiliser un registry privée avec un executor Docker dans un runner Gitlab 

  * Encoder le login et mot de passe du registry:

  `echo -n "my_username:my_password" | base64`

Output:

  `bXlfdXNlcm5hbWU6bXlfcGFzc3dvcmQ=`


  * Créer une variable: DOCKER_AUTH_CONFIG dans le projet Gitlab et y intégré le contenu suivant:
```
 {
      "auths": {
          "registry.example.com:5000": {
              "auth": "bXlfdXNlcm5hbWU6bXlfcGFzc3dvcmQ="
          }
      }
  }
```
## Créer un "Helm chart" sur Gitlab 

```
  export HELM_EXPERIMENTAL_OCI=1
```
```
  helm registry login -u hregistry gitlab.fondative.com:5050
```  
>   password: `jjbeaCp7qwTSsqtFwzET`
{.is-info}

```  
  helm chart save my-nginx gitlab.fondative.com:5050/ci/helm-charts/my-nginx-helm:0.0.1
```
```
  helm chart push gitlab.fondative.com:5050/ci/helm-charts/my-nginx-helm:0.0.1
```
```
  helm chart list
```
```
  helm chart pull gitlab.fondative.com:5050/ci/helm-charts/my-nginx-helm:0.0.1
```  
## Supprimer tous les pods d'un Namespace de Kubernetes

## Step 1
```
kubectl -n <NAMESPACE> delete replicasets,deployments,jobs,service,pods,statefulsets --all --cascade=true --grace-period=0 --force
```
## Step 2

  * Run the following command to view the namespaces that are stuck in the Terminating state:

`kubectl get namespaces`

  * Select a terminating namespace and view the contents of the namespace to find out the finalizer. Run the following command:

`kubectl get namespace <terminating-namespace> -o yaml`

  * Your YAML contents might resemble the following output:

```
apiVersion: v1
 kind: Namespace
 metadata:
   creationTimestamp: 2018-11-19T18:48:30Z
   deletionTimestamp: 2018-11-19T18:59:36Z
   name: <terminating-namespace>
   resourceVersion: "1385077"
   selfLink: /api/v1/namespaces/<terminating-namespace>
   uid: b50c9ea4-ec2b-11e8-a0be-fa163eeb47a5
 spec:
   finalizers:
   - kubernetes
 status:
   phase: Terminating
```

  * Run the following command to create a temporary JSON file:   

 `kubectl get namespace <terminating-namespace> -o json >tmp.json `

  * Edit your tmp.json file. Remove the kubernetes value from the finalizers field and save the file.
  
Your tmp.json file might resemble the following output:
```
     {
      "apiVersion": "v1",
      "kind": "Namespace",
      "metadata": {
          "creationTimestamp": "2018-11-19T18:48:30Z",
          "deletionTimestamp": "2018-11-19T18:59:36Z",
          "name": "<terminating-namespace>",
          "resourceVersion": "1385077",
          "selfLink": "/api/v1/namespaces/<terminating-namespace>",
          "uid": "b50c9ea4-ec2b-11e8-a0be-fa163eeb47a5"
      },
      "spec": {
         "finalizers": 
      },
      "status": {
          "phase": "Terminating"
      }
  }
``` 

  * To set a temporary proxy IP and port, run the following command. Be sure to keep your terminal window open until you delete the stuck namespace:

`kubectl proxy`

Your proxy IP and port might resemble the following output:

`Starting to serve on 127.0.0.1:8001`

  * From a new terminal window, make an API call with your temporary proxy IP and port:
```
curl -k -H "Content-Type: application/json" -X PUT --data-binary @tmp.json http://127.0.0.1:8001/api/v1/namespaces/<terminating-namespace>/finalize
```
  * Verify that the terminating namespace is removed, run the following command:

`kubectl get namespaces`

# Copy content volume to volume Docker
```
docker volume create --name <new_volume>
docker run --rm -it -v <old_volume>:/from -v <new_volume>:/to alpine ash -c "cd /from ; cp -av . /to"
docker volume rm <old_volume>
```
# Amazon AWS Account
## Set credentials AWS:
```
aws configure set aws_access_key_id <yourAccessKey>
aws configure set aws_secret_access_key <yourSecretKey>
```
## For More information:
`aws sts get-caller-identity`
## Get credentials AWS:
```
aws configure get aws_access_key_id
aws configure get aws_secret_access_key
```

# Forcer un cronjob sur Kubernetes

`kubectl create job --from=cronjob/<name of cronjob> <name of job> -n namespace`

# Create Pod in Kubernetes

`kubectl run <name of pod> --image=<name of image> -n namespace`
`kubectl create -f file.yml`

# Change AccessMode PV Volumes K3S

`kubectl get pv -n namespace`
`kubectl edit pv <name of pv> -n namespace`

Et passez au mode d'accès RWX :
```
accessModes:
  - ReadWriteMany
```  
# Create a bucket

`aws s3 mb s3://bkp-manewco-prod`

# Delete the contents of a bucket

`aws s3 rm s3://bkp-manewco-prod --recursive`

# Delete Bucket

`aws s3api delete-bucket --bucket bkp-manewco-prod`

# Create Default VPC
 
 `aws ec2 create-default-vpc`

# Add Helm Package In GitLab
```
helm repo add --username ********* --password ************* demo https://gitlab.fondative.com/api/v4/projects/<project-id>/packages/helm/master
helm repo update
helm package demo-fmu
helm plugin install https://github.com/emanuelflp/helm-push.git
helm cm-push --username ******** --password ********** demo-fmu-0.3.7.tgz demo
```

# Add Files in Container Volumes

 ```
export PODNAME=$(kubectl get pods -l app=frontend -n ${CI_COMMIT_REF_NAME} -o json | jq -r '.items[].metadata.name')
kubectl cp ./public/images ${CI_COMMIT_REF_NAME}/${PODNAME}:/var/www/html/public/
```

# Install Crictl Nodes kubernetes

```
VERSION="v1.22.0" && \
  wget https://github.com/kubernetes-sigs/cri-tools/releases/download/$VERSION/crictl-$VERSION-linux-amd64.tar.gz && \
  sudo tar zxvf crictl-$VERSION-linux-amd64.tar.gz -C /usr/local/bin && \
  rm -f crictl-$VERSION-linux-amd64.tar.gz
```  

# Force to delete NS Kubernetes


```
NS=`kubectl get ns |grep Terminating | awk 'NR==1 {print $1}'` && kubectl get namespace "$NS" -o json   | tr -d "\n" | sed "s/\"finalizers\": \[[^]]\+\]/\"finalizers\": []/"   | kubectl replace --raw /api/v1/namespaces/$NS/finalize -f -
```

# Prune Gilab images

`gitlab-ctl registry-garbage-collect -m`

# Delete all Terminating pods

`kubectl get pods --all-namespaces | awk '/Terminating|Pending/ {print "kubectl delete pod " $2 " -n " $1 " --grace-period=0 --force"}' | sh
`