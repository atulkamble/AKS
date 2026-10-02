## Kubernetes + AKS Commands Cheat Sheet

### 1. Kubernetes — Cluster Information

```bash
kubectl version --client
kubectl cluster-info
kubectl get nodes
kubectl get nodes -o wide

kubectl get namespaces
kubectl get all
kubectl get all -A
```

### 2. Pods

```bash
# List pods
kubectl get pods
kubectl get pods -o wide
kubectl get pods -A

# Create pod
kubectl run nginx --image=nginx

# Pod details
kubectl describe pod nginx

# Logs
kubectl logs nginx
kubectl logs -f nginx

# Enter container
kubectl exec -it nginx -- /bin/bash

# Delete pod
kubectl delete pod nginx
```

### 3. Deployments

```bash
# Create deployment
kubectl create deployment nginx --image=nginx

# List
kubectl get deployments

# Details
kubectl describe deployment nginx

# Scale
kubectl scale deployment nginx --replicas=3

# Update image
kubectl set image deployment/nginx nginx=nginx:1.27

# Rollout status
kubectl rollout status deployment/nginx

# History
kubectl rollout history deployment/nginx

# Rollback
kubectl rollout undo deployment/nginx

# Delete
kubectl delete deployment nginx
```

### 4. Services

```bash
kubectl get services
kubectl get svc

# ClusterIP
kubectl expose deployment nginx \
  --type=ClusterIP \
  --port=80

# NodePort
kubectl expose deployment nginx \
  --type=NodePort \
  --port=80

# LoadBalancer - commonly used with AKS
kubectl expose deployment nginx \
  --type=LoadBalancer \
  --port=80 \
  --target-port=80

kubectl get svc
```

Flow:

```text
Internet
   |
Azure Load Balancer
   |
Kubernetes Service
   |
Deployment
   |
Pods
   |
Containers
```

### 5. YAML Commands

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

kubectl get -f deployment.yaml

kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

Useful validation:

```bash
kubectl apply -f deployment.yaml --dry-run=client

kubectl diff -f deployment.yaml
```

### 6. Namespace

```bash
kubectl get namespaces

kubectl create namespace dev

kubectl get pods -n dev

kubectl apply -f deployment.yaml -n dev

kubectl delete namespace dev
```

### 7. ConfigMap & Secret

```bash
# ConfigMap
kubectl create configmap app-config \
  --from-literal=ENV=production

kubectl get configmap
kubectl describe configmap app-config

# Secret
kubectl create secret generic db-secret \
  --from-literal=username=admin \
  --from-literal=password=Password123

kubectl get secrets
kubectl describe secret db-secret
```

### 8. Troubleshooting Commands

```bash
kubectl get pods
kubectl get pods -o wide

kubectl describe pod <pod-name>

kubectl logs <pod-name>

kubectl logs -f <pod-name>

kubectl get events

kubectl get events --sort-by=.metadata.creationTimestamp

kubectl exec -it <pod-name> -- /bin/sh

kubectl top nodes
kubectl top pods
```

Remember:

```text
Pending            → Scheduling / resource / PVC problem
ImagePullBackOff   → Cannot download container image
CrashLoopBackOff   → Container repeatedly crashes
OOMKilled          → Container exceeded memory limit
Running            → Pod is running
```

---

# AKS — Azure Kubernetes Service

### 9. Azure Login

```bash
az login

az account show

az account list -o table

az account set --subscription "<subscription-id>"
```

### 10. Create Resource Group

```bash
az group create \
  --name myRG \
  --location centralindia
```

### 11. Create AKS Cluster

Simple lab cluster:

```bash
az aks create \
  --resource-group myRG \
  --name myAKSCluster \
  --node-count 2 \
  --generate-ssh-keys
```

Check cluster:

```bash
az aks list -o table

az aks show \
  --resource-group myRG \
  --name myAKSCluster
```

### 12. Connect kubectl to AKS

```bash
az aks get-credentials \
  --resource-group myRG \
  --name myAKSCluster
```

Verify:

```bash
kubectl cluster-info

kubectl get nodes

kubectl get nodes -o wide
```

Architecture:

```text
Azure
 |
 +-- Resource Group
      |
      +-- AKS Cluster
           |
           +-- Control Plane (Azure Managed)
           |
           +-- Node Pool
                 |
                 +-- Node 1
                 |    +-- Pod
                 |    +-- Pod
                 |
                 +-- Node 2
                      +-- Pod
                      +-- Pod
```

### 13. Deploy Application on AKS

```bash
kubectl create deployment webapp --image=nginx

kubectl get deployments

kubectl get pods
```

Expose:

```bash
kubectl expose deployment webapp \
  --type=LoadBalancer \
  --port=80
```

Check:

```bash
kubectl get svc

kubectl get svc -w
```

Look for:

```text
EXTERNAL-IP
```

Then test:

```text
http://<EXTERNAL-IP>
```

### 14. Scale Pods

```bash
kubectl scale deployment webapp --replicas=5

kubectl get pods
```

```text
Deployment
   |
   +-- Pod 1
   +-- Pod 2
   +-- Pod 3
   +-- Pod 4
   +-- Pod 5
```

### 15. Scale AKS Nodes

```bash
az aks scale \
  --resource-group myRG \
  --name myAKSCluster \
  --node-count 3
```

Check:

```bash
kubectl get nodes
```

**Remember:**

```text
kubectl scale → Pods
az aks scale  → Nodes
```

### 16. AKS Node Pools

```bash
az aks nodepool list \
  --resource-group myRG \
  --cluster-name myAKSCluster \
  -o table
```

Add node pool:

```bash
az aks nodepool add \
  --resource-group myRG \
  --cluster-name myAKSCluster \
  --name apppool \
  --node-count 2
```

Scale node pool:

```bash
az aks nodepool scale \
  --resource-group myRG \
  --cluster-name myAKSCluster \
  --name apppool \
  --node-count 3
```

Delete:

```bash
az aks nodepool delete \
  --resource-group myRG \
  --cluster-name myAKSCluster \
  --name apppool
```

### 17. AKS Autoscaling

Enable cluster autoscaler:

```bash
az aks update \
  --resource-group myRG \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Pod autoscaling:

```bash
kubectl autoscale deployment webapp \
  --cpu-percent=50 \
  --min=2 \
  --max=10
```

Check:

```bash
kubectl get hpa
```

**Difference:**

```text
HPA                → Automatically scales Pods
Cluster Autoscaler → Automatically scales Nodes
```

### 18. Important Commands to Memorize

```bash
kubectl get nodes
kubectl get pods
kubectl get pods -o wide
kubectl get deployments
kubectl get svc
kubectl get all

kubectl describe pod <pod>
kubectl logs <pod>
kubectl exec -it <pod> -- /bin/bash

kubectl apply -f file.yaml
kubectl delete -f file.yaml

kubectl scale deployment <name> --replicas=3

kubectl rollout status deployment/<name>
kubectl rollout history deployment/<name>
kubectl rollout undo deployment/<name>

az aks create
az aks list
az aks show
az aks get-credentials
az aks scale
az aks nodepool list
```

### Quick Memory Map

```text
kubectl get       → View
kubectl describe  → Details / Troubleshoot
kubectl logs      → Application logs
kubectl exec      → Enter container
kubectl apply     → Create / Update
kubectl delete    → Delete
kubectl scale     → Scale Pods
kubectl rollout   → Deployment updates/rollback

az aks create           → Create AKS
az aks get-credentials  → Connect kubectl
az aks scale            → Scale nodes
az aks nodepool         → Manage node pools
```

**Core hierarchy:**

```text
AKS Cluster
    ↓
Node Pool
    ↓
Node
    ↓
Pod
    ↓
Container
```
