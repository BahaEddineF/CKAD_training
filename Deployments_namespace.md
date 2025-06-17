# Kubernetes Deployments & Namespaces Guide


---

## 1. What is a Deployment?
<div align="center">
  <img src="/Images/deplou.png" alt="Deployment Diagram" width="300">
  </div>


A Deployment manages the rollout and updates of applications in Kubernetes, ensuring they run reliably with features like scaling and rollback.

### Key Features:
- **Rolling Updates**: Gradually replace Pod instances
- **Rollback**: Revert to previous versions
- **Scaling**: Adjust replica counts dynamically
- **Self-Healing**: Replace failed Pods automatically

**Example Deployment:**
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
        image: nginx:1.21
        ports:
        - containerPort: 80
```

---

## 2. What is a Namespace?
<div align="center">
  <img src="/Images/namespace.png" alt="Namespace Structure" width="300">
</div>
Namespaces in Kubernetes are used to divide a cluster into virtual environments, helping organize resources for teams or projects. Common namespaces include kube-system, default, and kube-public.
Namespaces provide isolation, resource limits, policies, and their own DNS scope

### Common Namespaces:
- **default**: User-created resources go here by default
- **kube-system**: System processes (master/node components)

### Namespace Benefits:
- Resource isolation
- DNS name scoping (`<service>.<namespace>.svc.cluster.local`)

**Example Namespace Creation:**
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
```

---

## 3. Essential Commands

### Deployment Operations
```bash
# Create from YAML
kubectl apply -f deployment.yaml

# Update container image
kubectl set image deployment/nginx-deployment nginx=nginx:1.22

# Scale deployment
kubectl scale deployment nginx-deployment --replicas=5

# Check rollout status
kubectl rollout status deployment/nginx-deployment

# Rollback to previous version
kubectl rollout undo deployment/nginx-deployment
```

### Namespace Operations
```bash
# Create namespace
kubectl create namespace staging

# View all namespaces
kubectl get namespaces

# Work within a namespace (-n flag)
kubectl get pods -n production

# View all resources in namespace
kubectl get all -n production

# Delete namespace (cascading deletion)
kubectl delete namespace staging
```

---

## 4. Practical Examples

### Deployment with Namespace
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: web-container
        image: nginx:1.21
```

### Quick Command Reference
| Task                  | Command                                    |
|-----------------------|---------------------------------------------|
| Create namespace      | `kubectl create ns <name>`                  |
| Get deployments       | `kubectl get deploy -n <namespace>`         |
| Delete deployment     | `kubectl delete deploy <name> -n <ns>`      |
| Edit deployment live  | `kubectl edit deploy/<name>`                |
| View namespace events | `kubectl get events -n <namespace>`         |
