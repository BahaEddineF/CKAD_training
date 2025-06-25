# Kubernetes Advanced Scheduling Guide


## 1. ServiceAccounts

<div align="center">
  <img src="/Images/serviceAccount.png" alt="ServiceAccount Flow" width="500">
</div>

**Definition:** An identity used by a Pod to interact with the Kubernetes API securely. It allows the Pod to perform specific actions within the cluster based on assigned permissions.

### Example Configuration:
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: prometheus-sa
```

### Using ServiceAccount in Pod:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: monitoring-pod
spec:
  serviceAccountName: prometheus-sa
  containers:
  - name: prometheus
    image: prom/prometheus
```

### Key Commands:
```bash
kubectl create serviceaccount my-sa
kubectl get serviceaccounts
```

---

## 2. Resource Requirements & Limits

<div align="center">
  <img src="/images/resources.png" alt="Resource Allocation" width="500">
</div>

**Definition:** Define the minimum and maximum amount of CPU and memory a container needs to run.

### Example:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-demo
spec:
  containers:
  - name: nginx-container
    image: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

**Units:**
- CPU: `1000m` = 1 core  
- Memory: `Mi` (mebibytes), `Gi` (gibibytes)

---

## 3. Taints & Tolerations

<div align="center">
  <img src="/images/exemple taint.png" alt="Taints Mechanism" width="600">
</div>

### Taints (Node Side)

Rules applied to nodes to repel Pods from being scheduled there unless the Pods explicitly tolerate the taint. Taints are commonly used to:

- Reserve nodes for specific workloads  
- Prevent scheduling on nodes with limitations or special conditions  

- **Key:** A unique identifier for the taint  
- **Value:** (Optional) Additional context or information  
- **Effect:** Defines the behavior when the taint is not tolerated (e.g., `NoSchedule`, `PreferNoSchedule`, `NoExecute`)

```bash
kubectl taint nodes node1 key=value:NoSchedule
```

---

### Tolerations (Pod Side)

Rules applied to Pods, allowing them to schedule on nodes with matching taints. Ensures that certain Pods can run on specific nodes despite taints.

```yaml
tolerations:
- key: "key"
  operator: "Equal"
  value: "value"
  effect: "NoSchedule"
```

**Effects:**
- `NoSchedule`: Prevent scheduling  
- `PreferNoSchedule`: Prefer not to schedule  
- `NoExecute`: Evict existing pods  

---

<div align="center">
  <img src="/images/nodetaint.png" alt="Node Selection" width="500">
</div>

---

## 4. Node Selector
<div align="center">
  <img src="/images/nodeselctor.png" alt="Node Selection" width="500">
</div>


### Node Selector:
The Node Selector in Kubernetes is a simple way to ensure that a Pod runs on a specific type of Node

**Simple Node Selection:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  nodeSelector:
    size: large
```

**Label Nodes:**
```bash
kubectl label nodes <node-name> <key>=<value> 
```

---

## 5. Node Affinity

### Node Affinity :
is a Kubernetes feature that ensures pods are scheduled on nodes that match specific labels


**Advanced Example:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: zone
            operator: In
            values:
            - east
```

**Affinity Types:**
- `required...`: Hard requirement  
- `preferred...`: Soft preference

- **requiredDuringSchedulingIgnored DuringExecution** Pods must run on Nodes matching the rules; rules apply only during scheduling  
- **preferredDuringSchedulingIgnored DuringExecution:** Pods prefer Nodes matching the rules but can run elsewhere if necessary; rules apply only during scheduling.
- **requiredDuringSchedulingRequired DuringExecution**  Not implemented yet; rules would apply during both scheduling and execution

---

## Comparison Table

| Feature            | Use Case                     | Granularity             |
|--------------------|-------------------------------|--------------------------|
| Node Selector      | Simple label matching         | Basic                    |
| Node Affinity      | Complex scheduling rules      | Advanced                 |
| Taints/Tolerations | Node repelling mechanism      | Specialized workloads    |
| Resources          | Container resource management | Performance control      |
