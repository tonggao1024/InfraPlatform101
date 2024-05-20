# Kubernetes

## Table of Contents
- [Introduction](#introduction)
- [Components](#k8s-components)
- [Features](#features)
- [Installation](#installation)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgements](#acknowledgements)

## Introduction

https://www.youtube.com/watch?v=X48VuDVv0do

Kubernetes(K8s) is a container orchestration tool, which offers:
- High Availability or no downtime
- Scalability or high performance
- Disaster recovery -- backup and restore

#### Layers

deployment -> pod -> container

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
- forward the http request to service

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
- When doing Node replication, you defind blue print for your application pods (i.e number of replicas)
- In practice, user will not directly working with pods, instead user work with Deployments
- You can scale up/down number of pods




## Features
Replication of Nodes 
- for each pods (same pod share the same Service)

- 
### Prerequisites
List any prerequisites or dependencies required to run the project.

### Installation
Step-by-step instructions on how to install the project.

