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
