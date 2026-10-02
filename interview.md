# Kubernetes Concepts for Interviews

For Kubernetes interviews, focus on **architecture, workloads, networking, storage, security, scheduling, scaling, and troubleshooting**.



## 1. Kubernetes Architecture

```text
                   Kubernetes Cluster
                          |
          +---------------+---------------+
          |                               |
   CONTROL PLANE                     WORKER NODES
          |                               |
   +------+-------+                +------+-------+
   | API Server   |                | kubelet      |
   | etcd         |                | kube-proxy   |
   | Scheduler    |                | Runtime      |
   | Controller   |                | Pods         |
   +--------------+                +--------------+
```

| Component | Interview point |
|---|---|
| API Server | Entry point for Kubernetes API requests |
| etcd | Key-value database storing cluster state |
| Scheduler | Selects a node for a newly created Pod |
| Controller Manager | Reconciles actual state with desired state |
| kubelet | Node agent; ensures assigned Pods/containers run |
| kube-proxy | Implements Service networking rules on nodes |
| Container Runtime | Runs containers, e.g. containerd |

**Important:** Kubernetes works on the **desired-state model**. You declare what you want; controllers continuously work toward that state.

---

# 2. Core Kubernetes Objects

```text
Cluster
  |
  +-- Node
       |
       +-- Pod
            |
            +-- Container
```

### Pod
Smallest deployable unit in Kubernetes.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
```

One Pod can contain **one or multiple containers**. Containers in the same Pod share the Pod's network namespace and can share volumes.

### Deployment

Used mainly for **stateless applications** and manages ReplicaSets/Pods.

```text
Deployment
    ↓
ReplicaSet
    ↓
Pod Pod Pod
```

```bash
kubectl create deployment web --image=nginx
kubectl scale deployment web --replicas=3
```

### ReplicaSet

Ensures the desired number of Pod replicas are running.

```text
Desired = 3
Actual  = 2

ReplicaSet → creates 1 more Pod
```

Normally, you manage ReplicaSets through a **Deployment**, rather than creating them directly.

### StatefulSet

Used when applications need stable identities or persistent storage patterns.

Common examples:

```text
Databases
Kafka
ZooKeeper
Elasticsearch-style clustered workloads
```

Pod names remain predictable:

```text
mysql-0
mysql-1
mysql-2
```

### DaemonSet

Runs a Pod on each eligible node.

Typical uses:

```text
Monitoring Agent
Logging Agent
Security Agent
```

### Job / CronJob

```text
Job     → Run task to completion
CronJob → Run tasks on a schedule
```

---

# 3. Services

Pods are replaceable, so their IP addresses can change. A **Service** provides a stable network endpoint for a set of Pods.

```text
Users
  ↓
Service
  ↓
+-----+-----+-----+
|Pod 1|Pod 2|Pod 3|
+-----+-----+-----+
```

| Service | Purpose |
|---|---|
| ClusterIP | Internal cluster access; default |
| NodePort | Exposes through a port on nodes |
| LoadBalancer | Requests an external load balancer where supported |
| ExternalName | DNS alias to an external name |

Important interview question:

**Service vs Ingress**

```text
Service
   → exposes/routes traffic to a workload

Ingress
   → HTTP/HTTPS routing rules to Services
```

Example:

```text
example.com/app1 → Service A → Pods
example.com/app2 → Service B → Pods
```

Ingress normally requires an **Ingress Controller**.

---

# 4. ConfigMap vs Secret

| ConfigMap | Secret |
|---|---|
| Non-sensitive configuration | Sensitive configuration |
| URLs | Passwords |
| Environment settings | Tokens/API keys |
| Application configuration | Certificates/credentials |

Important interview point: Kubernetes Secrets are **base64-encoded by default, not inherently encrypted just because they are Secrets**. Production clusters should use appropriate access controls and encryption at rest.

---

# 5. Kubernetes Storage

Important concepts:

```text
Volume
PersistentVolume (PV)
PersistentVolumeClaim (PVC)
StorageClass
```

Flow:

```text
Pod
 ↓
PVC
 ↓
PV
 ↓
Storage
```

### PV

Represents persistent storage available to the cluster.

### PVC

Application's request for persistent storage.

### StorageClass

Defines how storage can be dynamically provisioned.

---

# 6. Namespace

Provides logical isolation/grouping inside a cluster.

```bash
kubectl get namespaces

kubectl create namespace dev

kubectl get pods -n dev
```

Typical structure:

```text
Cluster
 |
 +-- dev
 +-- test
 +-- staging
 +-- production
```

Namespaces help with organization, RBAC, quotas, policies, and resource management.

---

# 7. Labels and Selectors

Labels identify Kubernetes resources.

```yaml
metadata:
  labels:
    app: nginx
    env: production
```

Selectors find matching resources.

```text
Service
   |
selector: app=nginx
   |
   +---- Pod app=nginx
   +---- Pod app=nginx
```

Very important relationship:

**Services find Pods using label selectors.**

---

# 8. Requests and Limits

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "500m"
    memory: "512Mi"
```

Remember:

```text
Request = resource amount considered for scheduling
Limit   = maximum enforced/allowed usage for that container
```

CPU exceeding limit → typically **throttling**.

Memory exceeding limit → container may be terminated with **OOMKilled**.

---

# 9. Liveness vs Readiness vs Startup Probe

| Probe | Purpose |
|---|---|
| Liveness | Should Kubernetes restart this container? |
| Readiness | Should this Pod receive Service traffic? |
| Startup | Has a slow-starting application finished starting? |

Easy memory trick:

```text
Liveness  → Alive?
Readiness → Ready for traffic?
Startup   → Started yet?
```

---

# 10. Scheduling Concepts

The Scheduler determines where Pods run.

Important interview concepts:

```text
Node Selector
Node Affinity
Pod Affinity
Pod Anti-Affinity
Taints
Tolerations
Topology Spread Constraints
```

### Taint & Toleration

Think:

```text
Taint      → Node repels Pods
Toleration → Pod is allowed to tolerate that taint
```

Example:

```bash
kubectl taint nodes node1 environment=production:NoSchedule
```

A matching toleration makes the Pod eligible for that tainted node, but doesn't necessarily force it there.

---

# 11. Scaling

### Manual

```bash
kubectl scale deployment web --replicas=5
```

### HPA

Horizontal Pod Autoscaler changes the number of Pods based on metrics.

```text
Traffic ↑
CPU ↑
   ↓
HPA
   ↓
Pods: 2 → 5
```

### VPA

Adjusts Pod resource requests and, depending on configuration, can drive resource resizing/replacement.

### Cluster Autoscaler

Adjusts the number of worker nodes when Pods cannot be scheduled or nodes are underutilized, subject to configured node groups/pools and constraints.

---

# 12. Rolling Update & Rollback

Deployment supports controlled application updates.

```bash
kubectl set image deployment/web nginx=nginx:1.27

kubectl rollout status deployment/web

kubectl rollout history deployment/web

kubectl rollout undo deployment/web
```

Concept:

```text
Version 1
   ↓
Rolling Update
   ↓
Version 2

Problem?
   ↓
Rollback
   ↓
Previous revision
```

---

# 13. Kubernetes Networking

Remember these basic principles:

```text
Pod → Pod
Pod → Service → Pod
External → Service/Ingress → Pod
```

Each Pod normally receives its own cluster IP from the cluster networking solution.

Important concepts:

```text
CNI
Service
DNS
Ingress
NetworkPolicy
kube-proxy
```

**NetworkPolicy** controls allowed network communication when the networking implementation supports it.

---

# 14. Kubernetes Security

Interview topics:

```text
RBAC
ServiceAccount
Role
ClusterRole
RoleBinding
ClusterRoleBinding
Secrets
NetworkPolicy
SecurityContext
Pod Security Admission
```

RBAC:

```text
Role
  ↓
permissions inside namespace

RoleBinding
  ↓
assigns Role/ClusterRole to subject
```

ClusterRole can define cluster-scoped permissions or reusable permissions across namespaces.

---

# 15. Important Troubleshooting Commands

```bash
kubectl get nodes

kubectl get pods
kubectl get pods -A
kubectl get pods -o wide

kubectl describe pod <pod>

kubectl logs <pod>
kubectl logs <pod> -c <container>

kubectl exec -it <pod> -- /bin/sh

kubectl get deployment
kubectl get svc
kubectl get ingress

kubectl get events --sort-by=.metadata.creationTimestamp

kubectl top pods
kubectl top nodes
```

For interviews, remember this troubleshooting flow:

```text
Application Down
      ↓
kubectl get pods
      ↓
Check Pod Status
      ↓
kubectl describe pod
      ↓
Check Events
      ↓
kubectl logs
      ↓
Check Service/Endpoints
      ↓
Check Network/DNS
      ↓
Check Resources/Nodes
```

---

# 16. Common Pod Errors

| Error | Typical meaning |
|---|---|
| Pending | Scheduler cannot currently place/start Pod |
| ImagePullBackOff | Image cannot be pulled |
| ErrImagePull | Initial image pull failure |
| CrashLoopBackOff | Container repeatedly starts and crashes |
| OOMKilled | Container exceeded available/enforced memory |
| ContainerCreating | Container setup is still occurring |
| Evicted | Pod removed due to node/resource conditions |

---

# 17. Top Interview Questions to Prepare

1. What is Kubernetes and why is it used?
2. Explain Kubernetes architecture.
3. What happens when you run `kubectl apply`?
4. Pod vs Container?
5. Deployment vs ReplicaSet?
6. Deployment vs StatefulSet?
7. Service vs Ingress?
8. ClusterIP vs NodePort vs LoadBalancer?
9. ConfigMap vs Secret?
10. PV vs PVC vs StorageClass?
11. Liveness vs Readiness probe?
12. Requests vs Limits?
13. What is `CrashLoopBackOff`?
14. How do you troubleshoot a Pending Pod?
15. What are taints and tolerations?
16. Node affinity vs Pod affinity?
17. How does HPA work?
18. What is a DaemonSet?
19. What is RBAC?
20. Role vs ClusterRole?
21. What is a ServiceAccount?
22. How does Kubernetes DNS work?
23. How does Service discover Pods?
24. What happens if a worker node fails?
25. How do rolling updates and rollbacks work?
26. What is etcd?
27. What is CNI?
28. What is NetworkPolicy?
29. How do you troubleshoot application connectivity?
30. How do you secure a Kubernetes cluster?

## Interview Cheat Sheet

```text
Pod            = Smallest deployable unit
Node           = Machine running Pods
Cluster        = Control plane + worker nodes

Deployment     = Manages stateless workloads/ReplicaSets
ReplicaSet     = Maintains desired Pod count
StatefulSet    = Stable identity/storage patterns
DaemonSet      = Pod on each eligible node
Job            = Run-to-completion workload
CronJob        = Scheduled Job

Service        = Stable access to Pods
Ingress        = HTTP/HTTPS routing
ConfigMap      = Non-sensitive configuration
Secret         = Sensitive configuration

PV             = Persistent storage resource
PVC            = Storage request
StorageClass   = Storage provisioning class

HPA            = Scale Pods horizontally
VPA            = Adjust Pod resource sizing
Cluster Autoscaler = Scale worker nodes

RBAC           = Authorization
CNI            = Pod networking
CSI            = Storage integration
CRI            = Container runtime interface

Request        = Scheduling/resource guarantee basis
Limit          = Maximum enforced resource usage

Liveness       = Restart?
Readiness      = Receive traffic?
Startup        = Finished starting?

Taint          = Repel Pods
Toleration     = Permit Pod onto tainted node
Affinity       = Placement preference/requirement
```

For a **DevOps / Cloud Architect interview**, give extra attention to **Deployments, Services, Ingress, HPA, RBAC, networking, storage, Helm, EKS/AKS, CI/CD, observability, upgrades, security, and real troubleshooting scenarios**.
