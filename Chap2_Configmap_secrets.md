# Kubernetes ConfigMaps & Secrets Guide

## 1. ConfigMaps

<div align="center">
  <img src="/Images/config.png" alt="ConfigMap Diagram" width="500">
</div>

### What is a ConfigMap?
A **ConfigMap** in Kubernetes is a resource used to store configuration data (like value pairs) separately from application code.

**Use Cases:**
- Environment variables  
- Configuration files  
- Command-line arguments  

### Creating ConfigMaps

#### Imperative Commands:
```bash
# From literal values
kubectl create configmap app-config \
  --from-literal=DB_HOST=db.example.com \
  --from-literal=LOG_LEVEL=debug

# From file
kubectl create configmap app-config \
  --from-file=config.properties
```

#### Declarative YAML:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  key1: value1
  key2: value2
```

### Using ConfigMaps

**As Environment Variables:**
```yaml
env:
- name: DB_HOST
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: DB_HOST
```

**As Volume:**
```yaml
volumes:
- name: config-volume
  configMap:
    name: app-config
```

---

## 2. Secrets

<div align="center">
  <img src="/Images/secret.png" alt="Secret Diagram" width="500">
</div>

### What is a Secret?
A **Secret** stores sensitive data like passwords and tokens in base64-encoded format.

**Use Cases:**
- Database credentials  
- API keys  
- TLS certificates  

### Creating Secrets

#### Imperative Commands:
```bash
# From literals
kubectl create secret generic db-secret \
  --from-literal=DB_USER=admin \
  --from-literal=DB_PASSWORD=secret123

# From file
kubectl create secret generic tls-secret \
  --from-file=tls.crt=server.crt \
  --from-file=tls.key=server.key
# Decode Secret value ==  `echo <encoded-value> | base64 --decode`
```

#### Declarative YAML:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  DB_USER: YWRtaW4=         # base64 encoded 'admin'
  DB_PASSWORD: c2VjcmV0MTIz # base64 encoded 'secret123'
```

### Using Secrets

**As Environment Variables:**
```yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: DB_PASSWORD
```

**As Volume:**
```yaml
volumes:
- name: secret-volume
  secret:
    secretName: db-secret
```
## Key Commands Cheat Sheet

| Task                        | Command                                                                 |
|-----------------------------|-------------------------------------------------------------------------|
| Create ConfigMap from file  | `kubectl create cm <name> --from-file=<path>`                          |
| Create ConfigMap (apply)    | `kubectl apply -f configmap.yaml`                                      |
| Create ConfigMap (by name)  | `kubectl create configmap [name]`                                      |
| Create ConfigMap (literal)  | `kubectl create configmap example-config --from-literal=key1=value1`   |
| Create ConfigMap (file)     | `kubectl create configmap example-config --from-file=example-file.txt` |
| Create Secret (basic)       | `kubectl create secret generic db-secret`                              |
| Create Secret (literals)    | `kubectl create secret generic db-secret --from-literal=DB_HOST=db.example.com --from-literal=DB_USER=admin --from-literal=DB_PASSWORD=secretpassword` |
| Create Secret (file)        | `kubectl create secret generic db-secret --from-file=db-config.txt`    |
| View ConfigMap data         | `kubectl get cm <name> -o yaml`                                        |                              |
| Decode specific field       | `kubectl get secret db-secret -o=jsonpath='{.data.DB_HOST}' | base64 --decode` |
| List all Secrets            | `kubectl get secrets`                                                  |
| Update ConfigMap            | `kubectl edit cm <name>`                                               |

---





## 3. What is a Security Context?
<div align="center">
  <img src="/Images/Contextsecurity.png" alt="Security Context Diagram" width="350">
</div>
A **SecurityContext** defines privilege and access control settings for Pods or containers, including:

- User and group IDs  
- Filesystem permissions  
- Privilege escalation  
- Capabilities  
- SELinux/AppArmor options  

---

## Example Configuration

### Pod-Level Security Context
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
  - name: secure-container
    image: nginx
```

### Container-Level Security Context
```yaml
containers:
- name: secure-container
  image: redis
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    allowPrivilegeEscalation: false
    capabilities:
      add: ["NET_ADMIN"]
      drop: ["ALL"]
    readOnlyRootFilesystem: true
```

---

## Key Security Context Fields

| Field                   | Description                                               |
|------------------------|-----------------------------------------------------------|
| `runAsUser`            | User ID to run the container process                      |
| `runAsGroup`           | Group ID to run the container process                     |                         |
| `allowPrivilegeEscalation` | Prevent privilege escalation attacks (recommended: false) |
---

## Best Practices

### Least Privilege Principle
```yaml
securityContext:
  runAsNonRoot: true
  allowPrivilegeEscalation: false
```

### Filesystem Protection
```yaml
securityContext:
  readOnlyRootFilesystem: true
```

### Capability Management
```yaml
capabilities:
  drop: ["ALL"]
  add: ["NET_BIND_SERVICE"]
```

### Pod Security Standards
- Use `restricted` policy where possible  
- Apply **Pod Security Admission (PSA)**  

---

## Common Commands

```bash
# Check effective user in running container
kubectl exec secure-pod -- whoami

# Verify security context
kubectl get pod secure-pod -o jsonpath='{.spec.securityContext}'
```
