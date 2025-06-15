## 🌐 CORE CONCEPTS

In this section, we’ll break down the fundamental concepts of Kubernetes. You’ll learn how containerized applications are deployed, updated, and scaled. We'll also explore the architecture of Kubernetes and its key components.

---

## 🚢 1. Containerization

![Containerization Layers](/images/containerization.png)

### 🔍 What is Containerization?

Containerization is a lightweight form of virtualization that packages applications and their dependencies into isolated units called **containers**. These containers can run consistently across different environments — from local development machines to production servers in the cloud.

---

## ☸️ 2. What is Kubernetes?

![Kubernetes Logo](/Images/kuberneteslogo.png)

**Kubernetes** is an open-source container orchestration platform that helps you:
- Deploy and manage containerized applications
- Automatically scale and restart failed containers
- Run workloads in various environments:
  - Local development machines
  - On-premise data centers
  - Public or private cloud

---

## 🏗️ 3. Kubernetes Architecture

![Kubernetes Architecture Diagram](/Images/Architecture.png)

Kubernetes architecture is divided into two main parts:
- **Master (Control Plane) Node**
- **Worker Nodes**

### 🧠 Master Node Components

1. **API Server**  
   Acts as the **communication hub** for the cluster. It exposes Kubernetes APIs and is the entry point for all administrative commands via `kubectl`.

2. **Scheduler**  
   Determines which node is best suited to run a newly created pod, based on resource availability and workload distribution.

3. **Controller Manager**  
   Monitors and ensures the desired state of various resources (nodes, replicas, etc.) is maintained.

4. **etcd**  
   A distributed **key-value store** that stores all cluster data — essentially the "brain" of Kubernetes.

> 💡 The **control plane** manages the overall health and coordination of the cluster. It decides *what* needs to happen and *where*, then delegates the work to the worker nodes.

---

### ⚙️ Worker Node Components

- **Kubelet**  
  An agent that runs on each worker node. It communicates with the API server and ensures containers (pods) are running as expected.

- **Kube-proxy**  
  Manages networking on the node. It handles routing of traffic coming into the cluster and facilitates communication between pods.

> 🧩 Each worker node runs the necessary services to support the running containers and reports back to the control plane.
