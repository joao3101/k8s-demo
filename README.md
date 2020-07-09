# Kubernetes demo

[K8s introduction](https://docs.google.com/presentation/d/1WORoKAQ8fVSDGDADAE04f15uazCo9fgzFISuNhV3ay4/edit?usp=sharing)

## Create a local Kubernetes cluster, e.g. [k3d](https://k3d.io/#installation) (install v1.7.0)

    k3d create --publish 8080:80 --workers 3

## Start the local Kubernetes cluster
     
    k3d start

## Monitor all resources created in default namespace

    watch kubectl get all

## Create a deployment with 3 pods

    kubectl create -f 01-deployment.yaml

## Create a service to load balance across the previously created pods

    kubectl create -f 02-service.yaml
    

## Create a service to load balance across the previously created pods

    kubectl create -f 03-ingress.yaml
    
 
## Access hello service
   
    curl -H "host:echo.com" localhost:8080
Should get the response `You've hit hello-<ID> version:1`

Look at the logs
    
    kubectl logs -f deploy/hello
 
## Scale the hello deployment
   
    kubectl scale deployment hello -- replicas 5

Look at the logs
    
    kubectl logs -f deploy/hello


## Do a rolling upgrade

Edit the deployment directly on k8s
    
    kubectl edit deploy hello
    
Set image to marcoamador/hello:2
    
     curl -H "host:echo.com" localhost:8080
    
The response must be now `You've hit hello-<ID> version:2`

## Create a configmap

    kubectl create -f 04-configmap.yaml
    
## Use the previously created configmap in a deployment

    kubectl create -f 05-deployment-configmap.yaml
    
## Connect to the previously created pod and check env variables

    kubectl exec -it hello-configmap-<ID> bash
    
    # env
    
or
    
    kubectl exec hello-configmap-<ID> env
    
## Create a secret and a deployment that attaches it

    kubectl create -f 06-secret.yaml

    kubectl create -f 07-deployment-secret.yaml

and "see" the secret:
    
    kubectl exec hello-secret-<ID> env | grep SECRET_USERNAME
   
       
## Create a job and a cronjob

    kubectl create -f 08-job.yaml

    kubectl create -f 09-cronjob.yaml
 
Look at the logs
    
    kubectl logs -f countdown-<ID>
    
## Create a daemonset and a service

    kubectl create -f 10-daemonset.yaml

    kubectl create -f 11-service-ds.yaml


# Kubernetes dashboard

## Install dashboard

    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml

## Create admin user

    kubectl apply -f 12-dashboard-admin-user,yaml
    
## Get user token

    kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')

## Access dashboard

    kubectl proxy
    
    open http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/


# Cleanup

## Delete all created resources

    ./delete-all.sh

## Stop local Kubernetes cluster

    k3d stop

## delete local Kubernetes cluster

    k3d delete


