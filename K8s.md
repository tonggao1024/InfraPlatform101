# Kubernetes

## Table of Contents
- [Introduction](#introduction)
- [Components](#k8s-components)
- [Basic Architecture](#basic-architecture)
- [Cluster Set-Up](#cluster-setup)
- [Minikube](#minikube)
- [Kubectl](#kubectl)
- [Basic Commands](#basic-commands)

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
- also act as a load balancer because we do replications on Nodes(servers) and they are sharing the Service for the replicated pod.

### Ingress
- route traffic into the cluster by forward the http request to service

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

Resources: CPU|RAM|STORAGE
Nodes:
- 2 Master Nodes -> less resources
- 3 Woker Nodes
Add new Master/Node server:
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

#Metrics

`kubectl top` The kubectl top command returns current CPU and memory usage for a cluster’s pods or nodes, or for a particular pod or node if specified.



