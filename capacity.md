## Kubernetes — Pod, Node & Container Limits

The most important point is: **there is no single universal fixed number of containers per Pod or Pods per Node in Kubernetes.** Practical limits depend on cluster configuration, resources, networking, and—on managed services such as AKS—provider settings.

### Relationship

```text
Kubernetes Cluster
│
├── Node 1
│   ├── Pod 1
│   │   ├── Container 1
│   │   └── Container 2
│   │
│   ├── Pod 2
│   │   └── Container 1
│   │
│   └── Pod 3
│       └── Container 1
│
└── Node 2
    ├── Pod 4
    └── Pod 5
```

### Limits to remember

| Level | Limit / Key Point |
|---|---|
| Containers per Pod | **No fixed Kubernetes numeric limit**; normally 1 main app container, sometimes sidecars |
| Pods per Node | Configurable; controlled by kubelet/managed-service settings |
| Nodes per cluster | Depends on Kubernetes/provider/version/configuration |
| CPU per Pod | Controlled using `requests` and `limits` |
| Memory per Pod | Controlled using `requests` and `limits` |

### AKS: Pod limit per Node

For **AKS**, don't memorize one number as applying to every cluster. The maximum Pods per node is affected by the **networking mode/plugin, node pool configuration, and `maxPods` setting**.

Check your actual AKS node pool configuration:

```bash
az aks nodepool list \
  --resource-group myRG \
  --cluster-name myAKS \
  -o table
```

Check Kubernetes capacity:

```bash
kubectl describe node
```

Look for:

```text
Capacity:
  cpu:
  memory:
  pods:

Allocatable:
  cpu:
  memory:
  pods:
```

### Container Resource Limits

Example:

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
REQUEST
   ↓
Minimum resource considered for scheduling

LIMIT
   ↓
Maximum resource constraint

CPU limit exceeded
   ↓
CPU throttling

Memory limit exceeded
   ↓
Possible OOMKilled
```

### Easy Interview/Exam Memory

```text
Cluster
  ↓
Nodes
  ↓
Pods
  ↓
Containers
```

**Node = Machine**  
**Pod = Kubernetes workload unit**  
**Container = Application process/runtime unit**

And:

```text
1 Cluster → Many Nodes
1 Node    → Many Pods
1 Pod     → One or more Containers
```

For production sizing, always check the **specific Kubernetes/AKS version, VM size, networking mode, IP capacity, `maxPods`, CPU, and memory** rather than relying on a single memorized maximum.
