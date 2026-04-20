## 📘 Introduction to Kubernetes (K8s)

Kubernetes (often abbreviated as **K8s**) is an open-source platform designed to automate the deployment, scaling, and management of containerized applications.

Originally developed by Google and now maintained by the Cloud Native Computing Foundation (CNCF), Kubernetes helps you run applications reliably across clusters of machines—whether on your local machine, on-premises, or in the cloud.

## ❓ Why Kubernetes?

Modern applications are often built using containers (e.g., Docker). While containers make applications portable, managing them at scale can become complex. Kubernetes solves this problem by providing:

- **Automated Deployment & Scaling**
- **Self-Healing**
- **Service Discovery & Load Balancing**
- **Storage Orchestration**
- **Configuration Management**


## 🧱 Core Concepts

- **Cluster**: A set of nodes (machines) that run containerized applications.
- **Node**: A worker machine in Kubernetes.
- **Pod**: The smallest deployable unit, containing one or more containers.
- **Deployment**: Manages how applications are deployed and updated.
- **Service**: Exposes your application to network traffic.



## ⚙️ Getting Started

### ✅ Prerequisites

Ensure you have the following installed:

- [Docker](https://www.docker.com/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Minikube](https://minikube.sigs.k8s.io/docs/) or any Kubernetes cluster

---

### 🚀 Start a Local Cluster with minikube

```bash
minikube start
```

### Check Minikube Status

```bash
minikube status
```

### View Cluster Information

```bash
kubectl cluster-info
```

### List Cluster Nodes:

```bash
kubectl get nodes
```

### View Running Pods :

```bash
kubectl get pods
```


### Generate Deployment (dry-run):

```bash
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > deployment.yaml
```