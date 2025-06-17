# Kubernetes Pods and ReplicaSets

---

## 📦 1. What is a Pod?

<div align="center">
  <img src="/Images/Pod.png" alt="Kubernetes Pod Structure" width="500">
</div>

A **Pod** is the smallest unit in Kubernetes, consisting of one or more containers (like Docker containers) that share storage, a unique network IP, and a set of instructions for running the containers.

### 🔑 Key Features of a Pod:
- **Containers**: One or more application containers working together
- **Shared Storage**: Access to the same storage resources
- **Network**: A unique IP address for the Pod to communicate
- **Specifications**: Instructions defining how containers should operate

---

## 🛠️ 2. Pod Management Commands

### ▶️ Imperative Command

```bash
kubectl run Namepod.... --port=8089 --image=pasc/ano007/sample:1.0 
```

---

### 📄 Declarative Command

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web
spec:
  containers:
  - name: web-app
    image: pasc/ano007:1.0
```

### 📥 Apply the declarative configuration

```bash
kubectl apply -f filename.yml
```

---

## 🔁 3. What is a ReplicaSet?

<div align="center">
  <img src="/Images/Replica.png" alt="ReplicaSet Diagram" width="600">
</div>

A **ReplicaSet** A ReplicaSet ensures the correct number of pod copies are running. It helps maintain high availability and allows easy scaling of the application. If a pod fails, the ReplicaSet creates a new one automatically.

### 🔍 Key Features:
- Maintains pod replicas at desired state
- Automatically replaces failed pods
- Enables horizontal scaling

### 🔄 ReplicaSet vs Replication Controller:
- ReplicaSet supports set-based label selectors
- Newer and recommended over ReplicationController

---

## 🏷️ 4. Selectors and Labels

<div align="center">
  <img src="/Images/selector.png" alt="Label Selector Diagram" width="500">
</div>

### 📘 Example Configuration

#### ✅ ReplicaSet Definition:

```yaml
selector:
  matchLabels:
    tier: front-end
```

#### ✅ Pod Definition:

```yaml
metadata:
  name: myapp-pod
  labels:
    tier: front-end
```

---

### 🧾 Key Commands

| Action               | Command                              |
|----------------------|--------------------------------------|
| Create from file      | `kubectl create -f def.yml`         |
| Get resources         | `kubectl get (pod|rs)`              |
| Delete ReplicaSet     | `kubectl delete rs [name]`          |
| Describe pod          | `kubectl describe pod [name]`       |
| Scale replicas        | `kubectl scale --replicas=3 rs/[name]` |
| Replace config        | `kubectl replace -f def.yml`        |
