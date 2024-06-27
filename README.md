
# Kubernetes Introduction and Advanced Setup

Welcome to the comprehensive guide to Kubernetes. This README will walk you through both basic and advanced concepts of Kubernetes, including setting up a cluster, deploying applications, managing resources, and more. This guide assumes you have basic knowledge of Docker and containerization.

## Table of Contents
1. [What is Kubernetes?](#what-is-kubernetes)
2. [Prerequisites](#prerequisites)
3. [Setting up Kubernetes](#setting-up-kubernetes)
4. [Basic Kubernetes Concepts](#basic-kubernetes-concepts)
5. [Deploying an Application](#deploying-an-application)
6. [Managing Resources](#managing-resources)
7. [ConfigMaps and Secrets](#configmaps-and-secrets)
8. [Persistent Storage](#persistent-storage)
9. [Networking](#networking)
10. [Monitoring and Logging](#monitoring-and-logging)
11. [Cleaning Up](#cleaning-up)
12. [Additional Resources](#additional-resources)

## What is Kubernetes?
Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It allows you to manage clusters of nodes (servers) running containerized applications.

## Prerequisites
- Basic understanding of Docker
- Docker installed on your machine
- Kubernetes CLI (`kubectl`) installed
- Minikube (for local Kubernetes cluster) installed

## Setting up Kubernetes

### Step 1: Install Minikube
Minikube is a tool that lets you run Kubernetes locally. Install it by following the instructions on the [Minikube GitHub page](https://github.com/kubernetes/minikube).

### Step 2: Start Minikube
Start a local Kubernetes cluster with Minikube:

```sh
minikube start
```

This command will download the necessary images and start a single-node Kubernetes cluster.

### Step 3: Verify the Cluster
Verify that your cluster is running:

```sh
kubectl cluster-info
```

You should see information about the Kubernetes master and other services running in your cluster.

## Basic Kubernetes Concepts

### Pods
A Pod is the smallest deployable unit in Kubernetes. It represents a single instance of a running process in your cluster. Pods can contain one or more containers.

### Services
A Service is an abstraction that defines a logical set of Pods and a policy to access them. Services enable loose coupling between dependent Pods. There are different types of services: ClusterIP, NodePort, LoadBalancer, and ExternalName.

### Deployments
A Deployment provides declarative updates to applications. It manages the creation and scaling of Pods. Deployments ensure that a specified number of Pods are running at all times.

### Namespaces
Namespaces provide a mechanism to isolate groups of resources within a single cluster. This is useful in multi-tenant environments or for organizing different environments like development, staging, and production.

## Deploying an Application

### Step 1: Create a Deployment
Create a deployment using the following YAML file (`deployment.yaml`):

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: nginx:latest
        ports:
        - containerPort: 80
```

Apply the deployment:

```sh
kubectl apply -f deployment.yaml
```

### Step 2: Expose the Deployment
Expose your deployment as a service:

```sh
kubectl expose deployment myapp-deployment --type=LoadBalancer --port=80
```

### Step 3: Access the Application
Get the URL of your service:

```sh
minikube service myapp-deployment --url
```

Open the URL in your web browser to see your running application.

## Managing Resources

### Scaling the Deployment
Scale your deployment to 5 replicas:

```sh
kubectl scale deployment myapp-deployment --replicas=5
```

### Updating the Deployment
Update the image in your deployment:

```sh
kubectl set image deployment/myapp-deployment myapp=nginx:1.19.0
```

### Viewing Logs
View the logs of a Pod:

```sh
kubectl logs <pod-name>
```

Replace `<pod-name>` with the name of one of your Pods.

### Executing Commands in a Pod
Run a command in a running Pod:

```sh
kubectl exec -it <pod-name> -- /bin/bash
```

### Checking Resource Usage
Check the resource usage of your Pods:

```sh
kubectl top pod
```

### Describing Resources
Get detailed information about a resource:

```sh
kubectl describe pod <pod-name>
```

## ConfigMaps and Secrets

### ConfigMaps
ConfigMaps allow you to decouple configuration artifacts from image content to keep containerized applications portable.

Create a ConfigMap from a file:

```sh
kubectl create configmap myconfig --from-file=config.txt
```

### Secrets
Secrets store and manage sensitive information, such as passwords, OAuth tokens, and SSH keys.

Create a Secret from literals:

```sh
kubectl create secret generic mysecret --from-literal=username=admin --from-literal=password=secret
```

## Persistent Storage

### PersistentVolume (PV) and PersistentVolumeClaim (PVC)
PVs and PVCs provide a way to manage durable storage in a Kubernetes cluster.

Create a PV (`pv.yaml`):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

Apply the PV:

```sh
kubectl apply -f pv.yaml
```

Create a PVC (`pvc.yaml`):

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Apply the PVC:

```sh
kubectl apply -f pvc.yaml
```

## Networking

### Network Policies
Network Policies are used to control the traffic flow between different Pods in your cluster.

Create a Network Policy (`network-policy.yaml`):

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-nginx
spec:
  podSelector:
    matchLabels:
      app: nginx
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: myapp
    ports:
    - protocol: TCP
      port: 80
```

Apply the Network Policy:

```sh
kubectl apply -f network-policy.yaml
```

## Monitoring and Logging

### Prometheus and Grafana
Prometheus is a powerful monitoring system, and Grafana is used for analytics and monitoring dashboards.

#### Step 1: Install Prometheus
Install Prometheus using Helm:

```sh
helm install stable/prometheus --name prometheus --namespace monitoring
```

#### Step 2: Install Grafana
Install Grafana using Helm:

```sh
helm install stable/grafana --name grafana --namespace monitoring
```

### Viewing Logs
View logs of all Pods in a namespace:

```sh
kubectl logs -n <namespace> --all-containers=true --max-log-requests=5
```

## Cleaning Up
To delete all resources created during this guide, use the following commands:

```sh
kubectl delete deployment myapp-deployment
kubectl delete service myapp-deployment
kubectl delete configmap myconfig
kubectl delete secret mysecret
kubectl delete pvc my-pvc
kubectl delete pv my-pv
kubectl delete networkpolicy allow-nginx
```

## Additional Resources
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Kubernetes GitHub Repository](https://github.com/kubernetes/kubernetes)
- [Kubernetes Tutorials by KodeKloud](https://kodekloud.com/p/kubernetes-for-beginners)
- [Udemy Kubernetes Courses](https://www.udemy.com/topic/kubernetes/)

This README provides a basic introduction to Kubernetes and some advanced concepts. For more detailed tutorials and examples, please refer to the official documentation and other resources listed above.
