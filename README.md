# Kubernetes + AKS — Short Learning Notes

## 1. Kubernetes (K8s)

**Kubernetes = Container Orchestration Platform**

Used to automatically **deploy, manage, scale, load balance, and recover containers**.

### Why Kubernetes?

```text
Without Kubernetes
Containers
   ↓
Manual Deployment
Manual Scaling
Manual Recovery
Manual Load Balancing

With Kubernetes
Containers
   ↓
Kubernetes
   ↓
Deployment + Scaling
Load Balancing
Self-Healing
Rolling Updates
```

### Key Benefits

- Container orchestration
- Auto scaling
- Self-healing
- Load balancing
- Service discovery
- Rolling updates & rollback
- Storage management
- Configuration & secrets

---

# 2. Kubernetes Architecture

```text
                 KUBERNETES CLUSTER
                         |
          +--------------+--------------+
          |                             |
     CONTROL PLANE                 WORKER NODES
          |                             |
    +-----+------+              +-------+-------+
    |     |      |              |       |       |
   API   etcd Scheduler       Kubelet Runtime kube-proxy
 Server        Controller        |
                                 |
                           +-----+-----+
                           |           |
                          Pod         Pod
                           |           |
                       Container   Container
```

### Control Plane

| Component | Purpose |
|---|---|
| API Server | Entry point to Kubernetes |
| etcd | Stores cluster state |
| Scheduler | Selects node for Pod |
| Controller Manager | Maintains desired state |

### Worker Node

| Component | Purpose |
|---|---|
| Kubelet | Manages Pods on node |
| Container Runtime | Runs containers |
| kube-proxy | Service/network traffic |

---

# 3. Kubernetes Object Architecture

```text
Deployment
    ↓
ReplicaSet
    ↓
   Pods
 ┌──┼──┐
 P1 P2 P3
    ↑
 Service
    ↑
  Users
```

Remember:

```text
Deployment → manages application
ReplicaSet → maintains replicas
Pod        → runs containers
Service    → provides network access
```

---

# 4. Pod

**Smallest deployable unit in Kubernetes.**

```text
Pod
 └── Container
```

Create:

```bash
kubectl run nginx --image=nginx
```

Check:

```bash
kubectl get pods
```

Delete:

```bash
kubectl delete pod nginx
```

---

# 5. Deployment

Manages Pods and application versions.

```text
Deployment
    ↓
ReplicaSet
    ↓
Pod Pod Pod
```

Create:

```bash
kubectl create deployment nginx --image=nginx
```

Scale:

```bash
kubectl scale deployment nginx --replicas=3
```

Check:

```bash
kubectl get deployments
kubectl get pods
```

---

# 6. Service

Provides stable network access to Pods.

```text
        Users
          ↓
       Service
          ↓
    +-----+-----+
    ↓     ↓     ↓
   Pod   Pod   Pod
```

### Service Types

| Type | Use |
|---|---|
| ClusterIP | Internal access |
| NodePort | Access using Node IP + Port |
| LoadBalancer | Public/cloud access |
| ExternalName | Maps to external DNS |

Example:

```bash
kubectl expose deployment nginx \
--type=LoadBalancer \
--port=80
```

---

# 7. Namespace

Logical separation inside a cluster.

```text
Kubernetes Cluster
      |
 +----+-----+------+
 |          |      |
DEV        TEST   PROD
```

Commands:

```bash
kubectl get ns
kubectl create ns dev
kubectl get pods -n dev
```

---

# 8. ConfigMap vs Secret

```text
Application
    |
 +--+---------+
 |            |
ConfigMap    Secret
 |            |
Config       Password
URL          Token
Port         Key
```

| ConfigMap | Secret |
|---|---|
| Non-sensitive data | Sensitive data |
| URL | Password |
| Port | Token |
| App settings | Credentials |

---

# 9. Kubernetes Storage

```text
Pod
 ↓
PVC
 ↓
PV / StorageClass
 ↓
Storage
```

Remember:

```text
PV  = Persistent Volume
PVC = Persistent Volume Claim
```

PVC requests persistent storage for a workload.

---

# 10. Ingress

Routes HTTP/HTTPS traffic to Services.

```text
                Internet
                   ↓
                Ingress
                   |
           +-------+-------+
           |               |
        /shop             /api
           ↓               ↓
      Shop Service     API Service
           ↓               ↓
         Pods             Pods
```

---

# 11. Kubernetes Scaling

### HPA

**Horizontal Pod Autoscaler**

```text
Traffic ↑
   ↓
CPU/Memory/Metric ↑
   ↓
HPA
   ↓
Pods ↑
```

Example:

```bash
kubectl autoscale deployment nginx \
--min=2 \
--max=10 \
--cpu-percent=50
```

Remember:

```text
HPA → Scales Pods

Node Autoscaling
    → Scales Nodes
```

---

# 12. Self-Healing

```text
Desired Pods = 3

Pod1   Pod2   Pod3
        X
        ↓
    Pod Failed
        ↓
   Kubernetes
        ↓
 Creates New Pod
        ↓
Pod1   Pod3   Pod4
```

Kubernetes continuously tries to maintain the **desired state**.

---

# 13. Kubernetes YAML

Basic structure:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx

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
          image: nginx
          ports:
            - containerPort: 80
```

Run:

```bash
kubectl apply -f deployment.yaml
```

---

# 14. Most Important kubectl Commands

```bash
kubectl get nodes

kubectl get pods

kubectl get pods -o wide

kubectl get deployments

kubectl get svc

kubectl get all

kubectl get all -A

kubectl describe pod <pod>

kubectl logs <pod>

kubectl exec -it <pod> -- /bin/sh

kubectl apply -f app.yaml

kubectl delete -f app.yaml
```

---

# 15. Deployment Update

```text
Version 1
   ↓
Deployment
   ↓
V1  V1  V1

Rolling Update

V2  V1  V1
V2  V2  V1
V2  V2  V2
```

Update:

```bash
kubectl set image deployment/nginx \
nginx=nginx:1.29
```

Status:

```bash
kubectl rollout status deployment/nginx
```

Rollback:

```bash
kubectl rollout undo deployment/nginx
```

---

# 16. Important Workload Types

| Object | Use |
|---|---|
| Deployment | Stateless application |
| StatefulSet | Stateful application |
| DaemonSet | Pod on each/selected node |
| Job | Run once |
| CronJob | Scheduled job |

Example:

```text
Web App        → Deployment
Database       → StatefulSet
Monitoring     → DaemonSet
DB Migration   → Job
Daily Backup   → CronJob
```

---

# 17. What is AKS?

**AKS = Azure Kubernetes Service**

It is Microsoft's managed Kubernetes service.

```text
                  MICROSOFT AZURE
                        |
                   AKS Cluster
                        |
          +-------------+-------------+
          |                           |
 Managed Control Plane           Node Pools
                                      |
                              +-------+-------+
                              |       |       |
                            Node1   Node2   Node3
                              |       |       |
                            Pods    Pods    Pods
```

### Azure manages

```text
Control Plane
API Server
etcd
Control-plane availability
Control-plane operations
```

### You manage/configure

```text
Applications
Deployments
Pods
Services
Node Pools
Scaling
Networking
Storage
Security configuration
```

---

# 18. Why AKS?

Use AKS when you want Kubernetes on Azure without operating the Kubernetes control plane yourself.

Benefits:

- Managed Kubernetes
- Azure integration
- Scaling
- Load balancing
- Microsoft Entra ID integration
- Azure Monitor integration
- ACR integration
- Azure networking
- Managed identity
- Azure storage integration

---

# 19. Kubernetes vs AKS

| Kubernetes | AKS |
|---|---|
| Container orchestration platform | Azure managed Kubernetes |
| Can run anywhere | Azure |
| Can be self-managed | Managed service |
| Control plane may be your responsibility | Azure manages AKS control plane |
| Generic cloud-neutral platform | Azure integrations |

Remember:

```text
Kubernetes = Technology

AKS = Kubernetes managed by Azure
```

---

# 20. AKS Deployment Flow

```text
Developer
    ↓
Source Code
    ↓
Docker Build
    ↓
Container Image
    ↓
Azure Container Registry
    ↓
AKS
    ↓
Deployment
    ↓
Pods
    ↓
Service
    ↓
Load Balancer
    ↓
User
```

---

# 21. Create AKS

Login:

```bash
az login
```

Resource Group:

```bash
az group create \
--name myRG \
--location eastus
```

Create cluster:

```bash
az aks create \
--resource-group myRG \
--name myAKS \
--node-count 2 \
--generate-ssh-keys
```

Connect:

```bash
az aks get-credentials \
--resource-group myRG \
--name myAKS
```

Verify:

```bash
kubectl get nodes
```

---

# 22. Deploy Application to AKS

```bash
kubectl create deployment nginx --image=nginx
```

Scale:

```bash
kubectl scale deployment nginx --replicas=3
```

Expose:

```bash
kubectl expose deployment nginx \
--type=LoadBalancer \
--port=80
```

Check:

```bash
kubectl get pods
kubectl get svc
```

Architecture:

```text
Internet
   ↓
Public IP
   ↓
Azure Load Balancer
   ↓
Kubernetes Service
   ↓
+-----+-----+-----+
|     |     |     |
Pod1 Pod2  Pod3
```

---

# 23. AKS + ACR Architecture

```text
Developer
   ↓
Docker Image
   ↓
Azure Container Registry
   ↓
AKS Cluster
   ↓
Deployment
   ↓
Pods
```

Common production flow:

```text
GitHub / Azure Repos
         ↓
    CI/CD Pipeline
         ↓
       Build
         ↓
       Test
         ↓
   Docker Image
         ↓
        ACR
         ↓
        AKS
         ↓
   Deployment
         ↓
       Pods
         ↓
      Service
         ↓
Ingress / Load Balancer
         ↓
       Users
```

---

# 24. Troubleshooting — Remember These

```bash
kubectl get pods
```

↓

```bash
kubectl describe pod <pod>
```

↓

```bash
kubectl logs <pod>
```

↓

```bash
kubectl get events
```

Common errors:

```text
Pending
   → Scheduling/resources/storage issue

ImagePullBackOff
   → Cannot pull image

CrashLoopBackOff
   → Container repeatedly crashes

OOMKilled
   → Memory limit exceeded
```

---

# 25. Final Revision Diagram

```text
                 KUBERNETES
                     |
       +-------------+-------------+
       |                           |
 CONTROL PLANE                 WORKER NODE
       |                           |
 API Server                     Kubelet
 etcd                           Runtime
 Scheduler                      Networking
 Controllers                       |
                                   |
                              Deployment
                                   ↓
                              ReplicaSet
                                   ↓
                             +-----+-----+
                             |     |     |
                            Pod   Pod   Pod
                             \     |     /
                              \    |    /
                                Service
                                   ↓
                           Ingress / LB
                                   ↓
                                  User
```

### 10 concepts to remember first

```text
1. Cluster     → Complete Kubernetes environment
2. Node        → Machine running workloads
3. Pod         → Smallest deployable unit
4. Deployment  → Manages application/Pods
5. ReplicaSet  → Maintains Pod count
6. Service     → Stable network access
7. Ingress     → HTTP/HTTPS routing
8. ConfigMap   → Non-sensitive configuration
9. Secret      → Sensitive configuration
10. AKS        → Managed Kubernetes on Azure
```

**Best learning order:** `Docker → Kubernetes Architecture → Pod → Deployment → ReplicaSet → Service → Namespace → ConfigMap/Secret → Storage → Ingress → Scaling → AKS → ACR + AKS → CI/CD`.
