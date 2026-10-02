Here are the **most important Kubernetes diagrams** for learning, teaching, and interview revision.

### 1. Kubernetes Architecture

```text
                    KUBERNETES CLUSTER
┌──────────────────────────────────────────────────────┐
│                 CONTROL PLANE                        │
│                                                      │
│  ┌──────────────┐      ┌────────────────────────┐   │
│  │ API Server   │◄────►│        etcd            │   │
│  └──────┬───────┘      │ Cluster State / Data   │   │
│         │              └────────────────────────┘   │
│         │                                            │
│  ┌──────▼───────┐       ┌──────────────────────┐   │
│  │  Scheduler   │       │ Controller Manager   │   │
│  └──────────────┘       └──────────────────────┘   │
└──────────────────────┬───────────────────────────────┘
                       │
              ─────────▼─────────
                 WORKER NODES
              ───────────────────

┌──────────────────────┐    ┌──────────────────────┐
│ Worker Node 1        │    │ Worker Node 2        │
│                      │    │                      │
│  kubelet             │    │  kubelet             │
│  kube-proxy          │    │  kube-proxy          │
│  Container Runtime   │    │  Container Runtime   │
│                      │    │                      │
│ ┌──────────────────┐ │    │ ┌──────────────────┐ │
│ │ POD              │ │    │ │ POD              │ │
│ │ ┌────┐  ┌────┐   │ │    │ │ ┌────┐  ┌────┐   │ │
│ │ │ C1 │  │ C2 │   │ │    │ │ │ C1 │  │ C2 │   │ │
│ │ └────┘  └────┘   │ │    │ │ └────┘  └────┘   │ │
│ └──────────────────┘ │    │ └──────────────────┘ │
└──────────────────────┘    └──────────────────────┘
```

### 2. Container → Pod → Node → Cluster

```text
Kubernetes Cluster
│
├── Node 1
│   ├── Pod 1
│   │   ├── Container
│   │   └── Container
│   │
│   └── Pod 2
│       └── Container
│
└── Node 2
    ├── Pod 3
    │   └── Container
    │
    └── Pod 4
        └── Container
```

**Remember:**

```text
Container < Pod < Node < Cluster
```

### 3. Deployment → ReplicaSet → Pods

```text
        Deployment
            │
            ▼
       ReplicaSet
            │
      ┌─────┼─────┐
      ▼     ▼     ▼
    Pod 1  Pod 2  Pod 3
      │      │      │
      ▼      ▼      ▼
 Container Container Container
```

Example:

```yaml
replicas: 3
```

means:

```text
Deployment
    │
ReplicaSet
    │
 ┌──┼──┐
 ▼  ▼  ▼
Pod Pod Pod
```

### 4. Service Load Balancing

```text
                  User
                    │
                    ▼
              ┌───────────┐
              │  Service  │
              └─────┬─────┘
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
       ┌─────┐   ┌─────┐   ┌─────┐
       │Pod 1│   │Pod 2│   │Pod 3│
       └─────┘   └─────┘   └─────┘
```

Service provides a **stable IP/DNS** and distributes traffic to matching Pods.

### 5. Ingress Architecture

```text
             Internet
                 │
                 ▼
        ┌─────────────────┐
        │ Ingress / LB    │
        └────────┬────────┘
                 │
        ┌────────┴────────┐
        │                 │
   /frontend            /api
        │                 │
        ▼                 ▼
 Service-Frontend     Service-API
        │                 │
     ┌──┴──┐           ┌──┴──┐
     ▼     ▼           ▼     ▼
    Pod   Pod         Pod   Pod
```

### 6. ConfigMap and Secret

```text
      ┌─────────────┐
      │  ConfigMap  │
      │ App Config  │
      └──────┬──────┘
             │
             ▼
         ┌───────┐
         │  POD  │
         │  App  │
         └───────┘
             ▲
             │
      ┌──────┴──────┐
      │   Secret    │
      │ Password    │
      │ API Key     │
      └─────────────┘
```

### 7. Persistent Storage

```text
       POD
        │
        ▼
┌────────────────┐
│ PVC            │
│ Persistent     │
│ Volume Claim   │
└───────┬────────┘
        │ requests
        ▼
┌────────────────┐
│ PV             │
│ Persistent     │
│ Volume         │
└───────┬────────┘
        │
        ▼
┌────────────────┐
│ Storage        │
│ Disk / EBS /   │
│ Azure Disk     │
└────────────────┘
```

Remember:

```text
Pod → PVC → PV → Storage
```

### 8. Complete Application Flow

```text
                     USER
                       │
                       ▼
                  Internet
                       │
                       ▼
               Load Balancer
                       │
                       ▼
                    Ingress
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        Frontend Service     Backend Service
             │                   │
        ┌────┴────┐         ┌────┴────┐
        ▼         ▼         ▼         ▼
      Pod-1     Pod-2     Pod-1     Pod-2
                                  │
                                  ▼
                                 PVC
                                  │
                                  ▼
                              Database
```

### Kubernetes diagram cheat sheet

```text
Cluster
  ↓
Node
  ↓
Pod
  ↓
Container


Deployment
  ↓
ReplicaSet
  ↓
Pods


User
  ↓
LoadBalancer / Ingress
  ↓
Service
  ↓
Pods


Pod
 ↓
PVC
 ↓
PV
 ↓
Storage
```

For **AKS**, the high-level architecture becomes:

```text
                 Microsoft Azure
                       │
               ┌───────▼────────┐
               │ AKS Control    │
               │ Plane          │
               │ Managed Azure  │
               └───────┬────────┘
                       │
              ┌────────┴────────┐
              ▼                 ▼
         Worker Node       Worker Node
           Azure VM          Azure VM
              │                 │
          ┌───┴───┐         ┌───┴───┐
          ▼       ▼         ▼       ▼
         Pod     Pod       Pod     Pod
```

**Interview formula:**  
**Deployment manages ReplicaSets → ReplicaSets manage Pods → Pods run Containers → Services expose Pods → Ingress routes external HTTP/HTTPS traffic.**

## Kubernetes Architecture — Short & Easy

Kubernetes architecture has **2 main parts**:

```text
                KUBERNETES CLUSTER
┌──────────────────────────────────────────────┐
│                                              │
│        CONTROL PLANE (Master)                │
│                                              │
│  ┌─────────────┐  ┌───────────────┐          │
│  │ API Server  │  │   Scheduler   │          │
│  └──────┬──────┘  └───────────────┘          │
│         │                                    │
│  ┌──────▼──────┐  ┌───────────────┐          │
│  │    etcd     │  │  Controller   │          │
│  │  Database   │  │   Manager     │          │
│  └─────────────┘  └───────────────┘          │
│                                              │
├──────────────────────────────────────────────┤
│              WORKER NODES                    │
│                                              │
│  ┌──────────── Node 1 ──────────────┐        │
│  │ kubelet                          │        │
│  │ kube-proxy                       │        │
│  │ Container Runtime               │        │
│  │                                 │        │
│  │  Pod              Pod           │        │
│  │ ┌─────────┐      ┌─────────┐    │        │
│  │ │Container│      │Container│    │        │
│  │ └─────────┘      └─────────┘    │        │
│  └─────────────────────────────────┘        │
│                                              │
│  ┌──────────── Node 2 ──────────────┐        │
│  │ kubelet | kube-proxy | Runtime   │        │
│  │     Pod            Pod           │        │
│  └─────────────────────────────────┘        │
└──────────────────────────────────────────────┘
```

### 1. Control Plane = Brain

| Component | Simple Purpose |
|---|---|
| **API Server** | Entry point for Kubernetes requests |
| **etcd** | Stores cluster configuration/state |
| **Scheduler** | Decides which Node runs a Pod |
| **Controller Manager** | Maintains desired cluster state |

### 2. Worker Node = Does the Work

| Component | Simple Purpose |
|---|---|
| **kubelet** | Manages Pods on the Node |
| **kube-proxy** | Handles Service/network traffic |
| **Container Runtime** | Runs containers, commonly containerd |
| **Pod** | Smallest deployable Kubernetes unit |
| **Container** | Application runs inside the Pod |

### Remember the hierarchy

```text
Cluster
  │
  ├── Control Plane → Manages everything
  │
  └── Worker Nodes → Run applications
          │
          └── Pods
               │
               └── Containers
```

### What happens when you deploy?

```text
kubectl apply
     ↓
API Server
     ↓
etcd stores desired state
     ↓
Scheduler selects Node
     ↓
kubelet creates Pod
     ↓
Container Runtime starts Container
     ↓
Application Running
```

**Easy memory:**  
**Control Plane = Brain → Node = Machine → Pod = Wrapper → Container = Application**.
