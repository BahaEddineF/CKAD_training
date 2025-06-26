# Container Logging

## Overview
- **Log container:** Logs are records of events or actions that happen within a system, application, or container.
- Used to debug, monitor, and trace behavior of apps running in containers.

---

## Visual Overview

<div align="center">
  <img src="/Images/logs.png" alt="Container Logs" width="500">
</div>

---

## YAML Configuration (`log-container.yml`)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: log-collector
spec:
  containers:
    - name: app-container
      image: busybox
      command: ["sh", "-c", "while true; do echo App Container Log; sleep 5; done"]
    - name: log-container
      image: busybox
      command: ["sh", "-c", "while true; do echo Log Container Log; sleep 5; done"]
```

---

## Commands

```bash
# Apply the configuration
kubectl apply -f log-container.yml

# View logs of a specific container
kubectl logs log-collector -c app-container
kubectl logs log-collector -c log-container

# (Optional) Follow logs in real-time
kubectl logs -f log-collector -c app-container
```

---

## Notes
- Use `-c` to specify the container inside the pod.
- Use `-f` to follow the log stream in real time.
- Logging helps with debugging, monitoring, and understanding container behavior.

---

# Monitor and Debug Applications

## Overview
- **Monitor and debug applications:** means tracking their performance, health, and usage to ensure they’re running smoothly.

---

## Commands

```bash
# Show resource usage (CPU/Memory) for each node
kubectl top node

# Show resource usage (CPU/Memory) for each pod
kubectl top pod
```

---

## Visual Help

<div align="center">
  <img src="/Images/debugapp.png" alt="Debug Monitoring" width="500">
</div>
