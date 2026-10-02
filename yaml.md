Below are the **most basic Kubernetes YAML codes** for practice.

### 1. Pod YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod

spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
```

```bash
kubectl apply -f pod.yaml
kubectl get pods
kubectl delete -f pod.yaml
```

### 2. Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp

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

```bash
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods
```

### 3. Service — ClusterIP

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myservice

spec:
  type: ClusterIP

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
```

### 4. Service — NodePort

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myservice

spec:
  type: NodePort

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```

### 5. Service — LoadBalancer

Useful with **AKS / EKS / GKE**:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myservice

spec:
  type: LoadBalancer

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
```

### 6. Namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

### 7. ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfig

data:
  APP_ENV: production
  APP_PORT: "8080"
```

### 8. Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret

type: Opaque

stringData:
  username: admin
  password: password123
```

For learning, remember this basic structure:

```yaml
apiVersion:
kind:
metadata:
spec:
```

And the most important relationship:

```text
Deployment
   |
   | creates/manages
   v
Pods
   |
   | selected using labels
   v
Service
   |
   v
Users / Applications
```

For your **first K8s practice**, focus mainly on **Pod → Deployment → Service → ConfigMap → Secret**.
