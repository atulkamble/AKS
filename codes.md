Here’s a **basic AKS command + YAML template cheat sheet** you can use for practice and teaching.

### 1. Create AKS Cluster

```bash
# Login
az login

# Create Resource Group
az group create \
  --name myRG \
  --location eastus

# Create AKS Cluster
az aks create \
  --resource-group myRG \
  --name myAKS \
  --node-count 2 \
  --generate-ssh-keys

# Connect kubectl to AKS
az aks get-credentials \
  --resource-group myRG \
  --name myAKS

# Verify
kubectl get nodes
kubectl cluster-info
```

### 2. Basic Pod Commands

```bash
# Create Pod
kubectl run nginx --image=nginx

# List Pods
kubectl get pods

# More details
kubectl get pods -o wide

# Pod details
kubectl describe pod nginx

# Pod logs
kubectl logs nginx

# Enter container
kubectl exec -it nginx -- /bin/bash

# Delete Pod
kubectl delete pod nginx
```

### 3. Pod YAML Template

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

```bash
kubectl apply -f pod.yaml
kubectl get pods
kubectl delete -f pod.yaml
```

### 4. Deployment YAML Template

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

```bash
kubectl apply -f deployment.yaml

kubectl get deployments
kubectl get pods
kubectl get replicasets
```

### 5. Service — LoadBalancer

For AKS, this is a common way to expose an application publicly:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service

spec:
  type: LoadBalancer

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
```

```bash
kubectl apply -f service.yaml

kubectl get services
kubectl get svc
```

Wait for:

```text
NAME            TYPE           EXTERNAL-IP
nginx-service   LoadBalancer   20.x.x.x
```

Then test:

```text
http://EXTERNAL-IP
```

### 6. Deployment + Service in One File

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: webapp

  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
        - name: webapp
          image: nginx
          ports:
            - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: LoadBalancer
  selector:
    app: webapp
  ports:
    - port: 80
      targetPort: 80
```

Run:

```bash
kubectl apply -f app.yaml

kubectl get all
kubectl get svc
```

### 7. Scaling

```bash
# Scale Deployment
kubectl scale deployment nginx-deployment --replicas=5

kubectl get pods

# Autoscaling
kubectl autoscale deployment nginx-deployment \
  --cpu-percent=50 \
  --min=2 \
  --max=10

kubectl get hpa
```

### 8. Update / Rollout

```bash
# Change image
kubectl set image deployment/nginx-deployment \
  nginx=nginx:1.27

# Check rollout
kubectl rollout status deployment/nginx-deployment

# History
kubectl rollout history deployment/nginx-deployment

# Rollback
kubectl rollout undo deployment/nginx-deployment
```

### 9. Namespace

```bash
kubectl create namespace dev

kubectl get namespaces

kubectl run nginx \
  --image=nginx \
  -n dev

kubectl get pods -n dev

kubectl delete namespace dev
```

### 10. ConfigMap

```bash
kubectl create configmap app-config \
  --from-literal=ENV=production

kubectl get configmap

kubectl describe configmap app-config
```

Template:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config

data:
  ENV: production
  APP_NAME: cloudnautic
```

### 11. Secret

```bash
kubectl create secret generic db-secret \
  --from-literal=username=admin \
  --from-literal=password=Password123

kubectl get secrets

kubectl describe secret db-secret
```

### 12. AKS Node Commands

```bash
# List node pools
az aks nodepool list \
  --resource-group myRG \
  --cluster-name myAKS \
  -o table

# Scale node pool
az aks nodepool scale \
  --resource-group myRG \
  --cluster-name myAKS \
  --name nodepool1 \
  --node-count 3

kubectl get nodes
```

### 13. Troubleshooting Commands

```bash
kubectl get all

kubectl get pods -A

kubectl get pods -o wide

kubectl describe pod POD-NAME

kubectl logs POD-NAME

kubectl get events

kubectl get events --sort-by=.metadata.creationTimestamp

kubectl top nodes

kubectl top pods
```

### AKS Flow to Remember

```text
Azure
  |
Resource Group
  |
AKS Cluster
  |
Node Pool
  |
Nodes
  |
Pods
  |
Containers
  |
Service
  |
Load Balancer
  |
Users
```

**Most important templates to learn first:** `Pod → Deployment → Service → ConfigMap → Secret → HPA → Ingress`.
