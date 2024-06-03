# Kubernetes

## Table of Contents
- [Introduction](#introduction)
- [Components](#k8s-components)
- [Basic Architecture](#basic-architecture)
- [Cluster Set-Up](#cluster-setup)
- [Minikube](#minikube)
- [Kubectl](#kubectl)
- [Basic Commands](#basic-commands)
- [YAML Configuration File](#yaml-configuration-file)
- [Demo](#demo)
- [K8s Namespaces](#k8s-namespaces)
- [Ingress Controller](#ingress-controller)
- [Helm](#helm)
- [Volumes](#volumes)
- [Services](#vservices)

## Introduction

https://www.youtube.com/watch?v=X48VuDVv0do

Kubernetes(K8s) is a container orchestration tool, which offers:
- High Availability or no downtime
- Scalability or high performance
- Disaster recovery -- backup and restore

#### Layers

deployment -> pod -> container

Everything below deployment should be managed by K8s


## K8s Components
### Pod
- Smallest unit of K8s
- Abstraction over contrainer
- Usually 1 application per Pod
- Each Pod gets its own IP address (which can be used to communicate to each other)
- New IP address on re-creation

### Service
- permanent/static IP address, one per pod
- pods communite with each other using service
- lifecycle of Pod and Service are not connected, this means if pod goes down, service can still serve as connection protocal
- external service and internal service avaible, depends on the type of the container
  - external service : type is load balancer
  - internal service: type is default type ClusterIP
- also act as a load balancer because we do replications on Nodes(servers) and they are sharing the Service for the replicated pod.

### Ingress
- route traffic into the cluster by forward the http request to service
- Without Ingress, we have to define external Service to define host:port where the http requrest directly access, but this is not ideal. 
- With Ingress, we add an extra layer, the http reqeust will talk to Ingress, and Ingress can forward the request to internal Service.

### ConfigMap
- external configuration of the application
- pod will connect to ConfigMap to read the configs such as DB_URL
- You can use them as enviromental variables or as a properties file

### Secret
- used to store secret data like DB passwords
- base64 encoded
- You can use them as enviromental variables or as a properties file

### Volumes
- Used for Database backup
- Can be either a storage on local machine, or remote, ouside of K8s cluster(external reference)

### Deployment
- for stateLESS Apps
- When doing Node replication, you defind blue print for your application pods (i.e number of replicas)
- abtraction of pods
- In practice, user will not directly working with pods, instead user work with Deployments
- You can scale up/down number of pods

### StatefulSet
- for stateFUL Apps or Databases deployment, similar as Deployment but also make sure the DB read/write consistency
- for best practice, DB are often hosted outside of K8s cluster

  
## Basic Architecture

### Worker Node
Node is worker machine in K8s cluster
- Each Node has multiple Pods on it
- 3 processes must be installed on every Node
  - Kubelet: Agent that runs on each node in the cluster, responsible for managing pods and containers
  - Kube-proxy: Network proxy that runs on each node, maintaining network rules (forward the request)
  - Container runtime: Software responsible for running containers (e.g., Docker, containerd)
- Woker Nodes do the actual work

### Master 
There are 4 processes run on every master:
- Api Server
   - cluster gateway
   - acts as a gatekeeper for authentication
   - forward valid request to Scheduler
   - Only 1 entry point to the cluster
- Scheduler
   - schedule the request on one of the work nodes based on the resources allocation
   - it just decides which node the pod will be scheduled, the actual schedule work is done by cubelet on the worker Node 
- Controller Manager
   - detect state changes like crashing and pod die.
   - it makes the request to the Scheduler to restart the pod
- etcd
   - cluster brain
   - A key-value store that stores all the cluster data
   - store i.e What resources are avaiable; Did the cluster state change etc.
   - The actual application data is not stored here.

## Cluster Setup 

#### Resources: CPU|RAM|STORAGE
#### Nodes:
- 2 Master Nodes -> less resources
- 3 Woker Nodes


#### Add new Master/Node server:
1. get new bare server
2. install all processes
3. add it to the cluster


## Minikube
For test locally
- creates Virtual Box on your laptop
- Node runs in the Virtual Box
- creates only 1 Node k8s cluster, both master and worker running on this node
- pre-build docker container on the node
- mainly for testing purposes


## Kubectl
- It's a cli to interact with the cluster (talking to API Server on Master)
- It's not only for Minikube cluster, but can be used on any type of K8s cluster

## Basic Commands
### install hyperhit and minikube
`brew update`

`brew install minikube`

`kubectl`

`minikube`

### create minikube cluster
`minikube start --vm-driver=docker`

`kubectl get nodes`

`minikube status`

`kubectl version`

### delete cluster and restart in debug mode
`minikube delete`

`minikube start --vm-driver=hyperkit --v=7 --alsologtostderr`

`minikube status`

### kubectl commands
`kubectl get nodes`

`kubectl get pod`

`kubectl get services`

`kubectl create deployment nginx-depl --image=nginx`

`kubectl get deployment`

`kubectl get replicaset`

`kubectl edit deployment nginx-depl`

### debugging
`kubectl logs {pod-name}`

`kubectl exec -it {pod-name} -- bin/bash`

### create mongo deployment
`kubectl create deployment mongo-depl --image=mongo`

`kubectl logs mongo-depl-{pod-name}`

`kubectl describe pod mongo-depl-{pod-name}`

### delete deployment
`kubectl delete deployment mongo-depl`

`kubectl delete deployment nginx-depl`

### create or edit config file
`vim nginx-deployment.yaml`

`kubectl apply -f nginx-deployment.yaml`

`kubectl get pod`

`kubectl get deployment`

### delete with config
`kubectl delete -f nginx-deployment.yaml`

### Metrics

`kubectl top` 
The kubectl top command returns current CPU and memory usage for a cluster’s pods or nodes, or for a particular pod or node if specified.

## YAML Configuration File

[Sample YAML files](https://gitlab.com/nanuchi/youtube-tutorial-series/-/tree/master/kubernetes-configuration-file-explained)

#### 3 parts of a Kubernetes config file:
- metadata : name, labelsm namespace etc
- specification :
  - replica
  - selector
  - template (which is another configuration file for pod)
    - metadata
      - label (used for selector above) 
    - specification (the blueprint for the pod)
- status : managed by k8s

You can use https://jsonformatter.org/yaml-validator to format your YAML file if needed.

#### Labels & Selectors
- The selector in deployment will use the label defined under the meta data of pods
- The selector in service will also use the same label to indentify which pod it's talking to.

## Demo

[Demo](https://gitlab.com/nanuchi/youtube-tutorial-series/-/tree/master/demo-kubernetes-components)

## K8s Namespaces

#### What is Namespaces
- Organize resources in namespaces
- Each namespace is a virtual cluster inside a cluster
- default namespace is used if you don't specify

#### 4 Namespaces by default when creating a cluster

```
tong ~  $ kubectl get namespace
NAME              STATUS   AGE
default           Active   14h
kube-node-lease   Active   14h
kube-public       Active   14h
kube-system       Active   14h
```

You can define namespace in configuration file under metadata section for each kind.

#### Why use namespace
- Group resources like databases, monitoring
- Seperate team using namespace to avoid app name conflict
- Resource sharing: staging and development can each use a different namespace
- Can set access and resource limit per namespace, means each team can only have access to their own namespace

#### Characteristics of namespace
- can't access resources from one namespace to another namespace, which means each namespace has to define it's own configMap/secret
- Service can be shared across namespace, i.e two namespace can talk to the same DB with it's service.
- some components can't be created with a namespace, which means it's global inside the cluster, such as volumes and nodes.

## Ingress Controller
- evaluate all the rules
- manage redirctions
- entry point to the cluster
- many third-party implementations https://bit.ly/32dfHe3, there is one from K8s called K8s Nginx Ingress Controller
<img width="914" alt="Screenshot 2024-06-02 at 11 49 15 AM" src="https://github.com/tonggao1024/InfraPlatform101/assets/19530394/ec84f838-f656-4a68-bc64-a9e47c7f104d">


## Helm
package manager, manage yaml files

#### Helm Charts
- bundle of yaml files
- can be downloaded and use existing ones (ready to use configuration)
- can be used with Templating Engine, have 1 yaml template and use dynamic values in values.yaml (good for CI/CD pipeline)
- can be used to deploy across different enviroments
- can be used for release management

<img width="914" alt="Screenshot 2024-06-02 at 8 56 56 PM" src="https://github.com/tonggao1024/InfraPlatform101/assets/19530394/da16e946-3b0d-466d-95d3-3a88197322b3">

## Volumes

#### Persistent Volume
- a cluster resource
- created via YAML file
- need actual physical storage, like local dist, cloud-storage, an external plugin to your cluster
- this is an interface to the actual storage, where you have to decide what to use and manage by yourself
- NOT namespaced, it's available to the whole cluster.

#### Persistent Volume Claim  
- used in Pods configuration

<img width="914" alt="Screenshot 2024-06-02 at 9 11 06 PM" src="https://github.com/tonggao1024/InfraPlatform101/assets/19530394/37e32632-7bbd-4b2d-9a1a-6e3116c7a75b">

#### ConfigMap and Secret
- these two are local volumes
- not created via PV and PVC
- managed by K8s

#### Storage Class
- Storage Class provisions PV dynamically when PVC claims it
- requested by PVC (refered in the PVC yaml)
  
<img width="914" alt="Screenshot 2024-06-02 at 9 22 00 PM" src="https://github.com/tonggao1024/InfraPlatform101/assets/19530394/be8d8c5a-b30f-4f7b-9efa-9af613d2e718">

## Services

#### Default type: ClusterIP Services

<img width="914" alt="Screenshot 2024-06-02 at 9 40 55 PM" src="https://github.com/tonggao1024/InfraPlatform101/assets/19530394/a0e428a1-bd54-4097-a76b-2ad35f9d9093">

#### Headless Services

<img width="914" alt="Screenshot 2024-06-02 at 9 45 51 PM" src="https://github.com/tonggao1024/InfraPlatform101/assets/19530394/87b18c70-1c8f-46fb-84dd-67549f5bf7d1">


