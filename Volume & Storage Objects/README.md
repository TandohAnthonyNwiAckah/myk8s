# 💾 Kubernetes Volume & Storage Objects

## 📖 Overview

In Kubernetes, Pods are **ephemeral** (temporary). When a Pod is deleted, its data is lost.

To solve this, Kubernetes provides **Volume & Storage Objects** to persist and manage data in **Kubernetes**.

---

# 📦 Why Storage is Important

- Pods can restart or be replaced
- Containers lose data on restart
- Applications like databases require persistent storage

> Storage ensures data survives beyond Pod lifecycle.

---

# 📁 1. Volume

A **Volume** is storage attached to a Pod.

## 🔹 Characteristics

- Exists only as long as the Pod runs
- Can be shared between containers in the same Pod
- Data is lost when Pod is deleted

---

## 🧪 Example (emptyDir)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-example
```

# Kubernetes — Volume & Storage Objects

## Overview

Kubernetes Pods are ephemeral: container filesystems do not persist when Pods are deleted or recreated. To provide durable or shared storage, Kubernetes uses Volumes, PersistentVolumes (PV), PersistentVolumeClaims (PVC), and StorageClasses.

This README summarizes core concepts, access modes, reclaim policies, and provides minimal examples you can apply with `kubectl`.

---

## Core Concepts

- **Volume**: pod-scoped storage that lives as long as the Pod (examples: `emptyDir`, `hostPath`, `configMap`). Useful for sharing data between containers in the same Pod or for ephemeral storage.

- **PersistentVolume (PV)**: cluster resource representing actual storage (disk, NFS, cloud disk). PVs are independent of Pods and managed by admins or provisioners.

- **PersistentVolumeClaim (PVC)**: user's request for storage. PVCs bind to PVs (static or dynamic provisioning) and are referenced by Pods.

- **StorageClass**: defines how storage is provisioned (provisioner, parameters, reclaimPolicy). PVCs can request a StorageClass for dynamic provisioning.

---

## Access Modes

- `ReadWriteOnce` (RWO): mounted read-write by a single node.
- `ReadOnlyMany` (ROX): mounted read-only by many nodes.
- `ReadWriteMany` (RWX): mounted read-write by many nodes.

Choose the mode based on whether you need single-writer or multi-writer semantics.

---

## Reclaim Policies

- `Retain`: keep the underlying storage after the PV is released (manual cleanup required).

- `Delete`: delete the underlying storage when the PV is released.

- `Recycle`: deprecated.

---

## Minimal Examples

1. Ephemeral Pod volume (`emptyDir`)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: emptydir-example
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - name: data
          mountPath: /usr/share/nginx/html
  volumes:
    - name: data
      emptyDir: {}
```

2. Static PersistentVolume (hostPath for local testing)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-local
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /tmp/pv-local
  persistentVolumeReclaimPolicy: Retain
```

3. PersistentVolumeClaim

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-example
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

4. StorageClass (example for cloud provisioner)

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
reclaimPolicy: Delete
```

---

## Common Commands

- Apply manifests: `kubectl apply -f <file>.yaml`

- List PVs/PVCs: `kubectl get pv`, `kubectl get pvc`

- Describe binding: `kubectl describe pvc <name>`

---

## When to use what

- Use `emptyDir` for ephemeral, intra-pod sharing or scratch space.

- Use PV + PVC for persistent data (databases, file services, backups).

- Use `StorageClass` when you want dynamic provisioning (cloud volumes, CSI drivers).

---


> Key takeaway: use PVs and PVCs to decouple storage lifecycle from Pods and ensure data durability.
