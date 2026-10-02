## Kubernetes: On-Premises vs AKS vs EKS vs GKE

All four use **Kubernetes**. The main difference is **who manages the Kubernetes control plane and underlying infrastructure**.

| Feature | Kubernetes On-Premises | AKS | EKS | GKE |
|---|---|---|---|---|
| Provider | Your organization | Microsoft Azure | AWS | Google Cloud |
| Full Name | Self-Managed Kubernetes | Azure Kubernetes Service | Elastic Kubernetes Service | Google Kubernetes Engine |
| Type | Self-managed | Managed Kubernetes | Managed Kubernetes | Managed Kubernetes |
| Control Plane | You manage | Azure manages | AWS manages | Google manages |
| Worker Nodes | You manage | You/Azure | You/AWS | You/Google |
| Hardware | Your servers | Azure VMs | EC2 | Compute Engine |
| Load Balancer | You configure | Azure Load Balancer | ELB/ALB/NLB | Google Cloud Load Balancing |
| Storage | SAN/NAS/Ceph/etc. | Azure Disks/Files | EBS/EFS | Persistent Disk/Filestore |
| Identity | Custom LDAP/OIDC/etc. | Microsoft Entra ID | AWS IAM | Google Cloud IAM |
| Networking | Your network/CNI | Azure VNet | AWS VPC | Google VPC |
| Scaling | Mostly configure yourself | Cluster Autoscaler | Cluster Autoscaler/Karpenter | Cluster Autoscaler/Autopilot |
| Upgrades | You manage | Managed tooling | Managed tooling | Managed tooling |
| Setup Difficulty | High | Lower | Lower | Lower |
| Infrastructure Control | Very High | Medium | Medium | Medium |
| Maintenance | High | Lower | Lower | Lower |
| Cloud Dependency | No | Azure | AWS | Google Cloud |

### Architecture

```text
                 Kubernetes
                      |
        +-------------+-------------+
        |             |             |
    On-Premises      AKS           EKS           GKE
        |             |             |             |
   Your Servers    Azure VMs      EC2 VMs      Compute Engine
        |             |             |             |
      Pods          Pods          Pods          Pods
        |             |             |             |
   Containers     Containers    Containers    Containers
```

### 1. Kubernetes On-Premises

You install and manage Kubernetes on **your own physical servers or VMs**.

```text
Data Center
    |
    +-- Control Plane
    |     +-- API Server
    |     +-- Scheduler
    |     +-- Controller Manager
    |     +-- etcd
    |
    +-- Worker Node 1
    |     +-- Pod
    |         +-- Container
    |
    +-- Worker Node 2
          +-- Pod
              +-- Container
```

You are responsible for almost everything:

```text
Hardware
   ↓
Operating System
   ↓
Container Runtime
   ↓
Kubernetes Installation
   ↓
Control Plane
   ↓
Networking
   ↓
Storage
   ↓
Security
   ↓
Upgrades
   ↓
Applications
```

**Use when:** you need maximum infrastructure control, have data-center/compliance requirements, or cannot depend entirely on public cloud.

---

## 2. AKS — Azure Kubernetes Service

Microsoft Azure provides managed Kubernetes through **AKS**.

```text
                 Azure
                   |
          AKS Control Plane
           Managed by Azure
                   |
             AKS Cluster
          /        |        \
       Node 1    Node 2    Node 3
         |          |        |
       Pods       Pods      Pods
         |
     Containers
```

Common integrations:

```text
AKS
 |
 +-- Azure VNet
 +-- Azure Load Balancer
 +-- Azure Container Registry (ACR)
 +-- Azure Disk / Azure Files
 +-- Microsoft Entra ID
 +-- Azure Monitor
 +-- Key Vault
```

**Use when:** your infrastructure and DevOps environment are primarily Azure-based.

---

## 3. EKS — Amazon Elastic Kubernetes Service

Amazon Web Services provides managed Kubernetes through **EKS**.

```text
                  AWS
                   |
          EKS Control Plane
            Managed by AWS
                   |
              EKS Cluster
          /        |        \
       EC2        EC2       EC2
        |          |         |
      Pods       Pods      Pods
        |
    Containers
```

Common integrations:

```text
EKS
 |
 +-- AWS VPC
 +-- EC2
 +-- ECR
 +-- EBS / EFS
 +-- ELB
 +-- IAM
 +-- CloudWatch
```

**Use when:** your workloads already use AWS services such as EC2, IAM, VPC, ECR, EBS, and CloudWatch.

---

## 4. GKE — Google Kubernetes Engine

Google Cloud provides managed Kubernetes through **GKE**.

```text
              Google Cloud
                   |
          GKE Control Plane
          Managed by Google
                   |
              GKE Cluster
          /        |        \
       Node 1    Node 2    Node 3
         |         |         |
       Pods      Pods      Pods
         |
     Containers
```

Common integrations:

```text
GKE
 |
 +-- Google VPC
 +-- Compute Engine
 +-- Artifact Registry
 +-- Persistent Disk
 +-- Cloud Load Balancing
 +-- IAM
 +-- Cloud Monitoring
```

**Use when:** your workloads and data platform are primarily on Google Cloud.

---

## Most Important Point to Remember

```text
On-Prem Kubernetes
------------------
YOU manage:
Control Plane
Nodes
Networking
Storage
Upgrades
Security
Applications


AKS / EKS / GKE
---------------
CLOUD PROVIDER manages:
Control Plane

YOU + CLOUD SERVICES manage:
Worker Nodes / Node Pools
Networking
Storage
Applications
```

Managed offerings can automate more than this—for example, node provisioning/upgrades or serverless/autopilot-style operation—so the exact responsibility split depends on the mode you choose.

### Easy Memory Trick

```text
K8s = Kubernetes

AKS = Azure  + Kubernetes
EKS = AWS    + Kubernetes
GKE = Google + Kubernetes

On-Prem = You manage Kubernetes infrastructure
Managed  = Cloud provider manages much of Kubernetes infrastructure
```

### Common Kubernetes Commands

The nice part is that your everyday Kubernetes commands remain largely the **same**:

```bash
kubectl get nodes

kubectl get pods

kubectl get deployments

kubectl get services

kubectl describe pod <pod-name>

kubectl logs <pod-name>

kubectl apply -f deployment.yaml

kubectl delete -f deployment.yaml

kubectl scale deployment myapp --replicas=5
```

So the core skill is **Kubernetes**; AKS, EKS, and GKE mainly add their respective cloud networking, identity, storage, security, and operational integrations.
