 ## AKS — Create and Delete Commands

### 1. Login to Azure
```bash
az login
```

### 2. Create Resource Group
```bash
az group create \
  --name myRG \
  --location eastus
```

### 3. Create AKS Cluster
```bash
az aks create \
  --resource-group myRG \
  --name myAKSCluster \
  --node-count 2 \
  --generate-ssh-keys
```

### 4. Connect to AKS
```bash
az aks get-credentials \
  --resource-group myRG \
  --name myAKSCluster
```

### 5. Verify
```bash
kubectl get nodes
kubectl get pods -A
```

### 6. Delete Only AKS Cluster
```bash
az aks delete \
  --resource-group myRG \
  --name myAKSCluster \
  --yes
```

### 7. Delete Complete Resource Group
This deletes the **AKS cluster and all resources inside `myRG`**.

```bash
az group delete \
  --name myRG \
  --yes \
  --no-wait
```

**Easy flow to remember:**

```text
az login
   ↓
Create Resource Group
   ↓
Create AKS
   ↓
Get Credentials
   ↓
kubectl get nodes
   ↓
Delete AKS / Resource Group
```
