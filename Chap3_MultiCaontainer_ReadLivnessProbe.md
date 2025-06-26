# Kubernetes Multi-Container Pods & Readiness/Liveness Probes

<div align="center">
  <img src="/Images/multic.png" alt="Multi-Container Pod Diagram" width="450">
</div>

---

## 🧩 What is a Multi-Container Pod?

A Pod configuration where multiple containers work together, sharing the same:
- **Network namespace** (same IP address)
- **Storage volumes**
- **Linux namespace** (PID, IPC, etc.)

### 🔑 Key Characteristics:
- Containers are co-located (always scheduled together)
- Communicate via `localhost`
- Share ephemeral or mounted volumes

---

## 🛠️ Example Configuration

### 🔹 Basic Multi-Container Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: main-container
    image: busybox:1.28
    command: ["sh", "-c", "echo The app is running! && sleep 3600"]
  - name: sidecar-container
    image: nginx
    ports:
    - containerPort: 80
```

---

### 🔹 Init Container Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
  - name: main-app
    image: myapp:1.0
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting; sleep 2; done']
```

---

# 🧪 Readiness and Liveness Probe

<div align="center">
  <img src="/Images/readnessprobe.png" alt="Probe Overview" width="600">
</div>

---

## 🔍 Overview

- **Liveness Probe**: Checks if the container is still running.  
  → If it fails, **Kubernetes restarts** the container.

- **Readiness Probe**: Checks if the container is ready to accept traffic.  
  → If it fails, the **Pod is removed from the Service endpoints**.

---

## 🔧 Probe Types

### 1. `httpGet`
```yaml
readinessProbe:
  httpGet:
    path: /api/ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```

---

### 2. `tcpSocket`
```yaml
readinessProbe:
  tcpSocket:
    port: 3306
```

---

### 3. `exec`
```yaml
readinessProbe:
  exec:
    command:
    - cat
    - /app/ready
```

---

## 📄 Full YAML Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: probe-example
spec:
  containers:
  - name: my-app
    image: my-app-image
    ports:
    - containerPort: 8080
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
    readinessProbe:
      httpGet:
        path: /readyport
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
```

---

## ✅ Summary Table

| Probe Type | Purpose                      | Action on Failure                      |
|------------|------------------------------|----------------------------------------|
| Liveness   | Check if container is alive  | Restart the container                  |
| Readiness  | Check if container is ready  | Remove Pod from service endpoints      |
