# 🚀 Kubernetes Deployment

## 📖 Overview

A **Deployment** is a workload object used to manage **stateless applications**.

It ensures that a specified number of identical Pods are running and automatically handles:

- Scaling
- Self-healing
- Rolling updates
- Rollbacks

---

## ⚙️ Why Use a Deployment?

Instead of creating Pods directly, a Deployment provides:

- ✅ Automated Pod management
- ✅ High availability
- ✅ Easy scaling
- ✅ Zero-downtime updates

---

## 📦 How It Works

A Deployment manages Pods through a **ReplicaSet**, which ensures the desired number of Pods are always running.

## 🚀 Create Deployment Automatically

You can generate a Deployment using kubectl:

```bash
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > deployment.yaml
```

Then apply it:

```bash
kubectl apply -f deployment.yaml
```

Check:

```bash
kubectl get pods
```

## Test self-healing:

Delete a Pod:

```bash
kubectl delete pod <pod-name>
```

👉 Kubernetes will automatically recreate it.

## 🔄 Scaling a Deployment

```bash
kubectl scale deployment nginx --replicas=5
```

## 🔁 Rolling Updates

```bash
kubectl set image deployment/nginx nginx=nginx:1.25
```

Rollback if needed:

```bash
kubectl rollout undo deployment/nginx
```
