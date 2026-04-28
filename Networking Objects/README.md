# 🌐 Kubernetes Networking Objects

## 📖 Overview

In Kubernetes, **Networking Objects** control how applications communicate inside and outside the cluster

They manage:

- Pod-to-Pod communication
- Internal service access
- External traffic routing
- Security between services

---

# 📦 1. Service

A **Service** provides a stable network endpoint for a set of Pods.

Since Pods are temporary and their IPs change, Services provide:

- Stable IP address
- DNS name
- Load balancing

---

## 🔹 Types of Services

### 🟦 ClusterIP (default)

- Internal access only
- Used for communication inside the cluster

### 🟨 NodePort

- Exposes service on each node’s IP
- Accessible externally via a fixed port

### 🌍 LoadBalancer

- Exposes service to the internet
- Common in cloud environments

---

## 🧭 Service Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

## 🌍 Ingress

- Routes external HTTP/HTTPS traffic
- Uses domain/path rules
- Central entry point

---

## 🔐 NetworkPolicy

- Controls traffic between Pods
- Acts like a firewall

---

## Summary

| Object        | Purpose                  |
| ------------- | ------------------------ |
| Service       | Internal/external access |
| Ingress       | External routing         |
| NetworkPolicy | Security rules           |

## Ingress VS Gateway

- Ingress → Simple, widely used, but limited
- Gateway API → Modern, flexible, and scalable

| Feature          | Ingress 🌍           | Gateway API 🚀       |
| ---------------- | -------------------- | -------------------- |
| Maturity         | Stable & widely used | Newer standard       |
| Flexibility      | Limited              | Highly flexible      |
| Protocol support | HTTP/HTTPS only      | HTTP, TCP, UDP, gRPC |
| Role separation  | Weak                 | Strong               |
| Extensibility    | Controller-specific  | Kubernetes-native    |
| Complexity       | Simple               | More advanced        |
