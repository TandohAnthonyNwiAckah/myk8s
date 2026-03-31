## Overview

This short guide shows how to create a simple Kubernetes Pod using a YAML file named pod.yaml.

## Prerequisites

- A running Kubernetes cluster (Minikube, kind, or cloud provider)
- `kubectl` installed and configured
- Verify cluster access:

```bash
kubectl get nodes
```

## Pod configuration (pod.yaml)

Create a file named `pod.yaml` with the following contents:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx:1.20
      ports:
        - containerPort: 80
```

Notes:
- Use `kubectl apply -f pod.yaml` for declarative management (recommended).
- Use `kubectl create -f pod.yaml` for a one-time create.

## Create the Pod

Apply the manifest:

```bash
kubectl apply -f pod.yaml
```

## Verify the Pod

Check Pod status:

```bash
kubectl get pods
```

You should see output similar to:

```
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          10s
```

## Delete the Pod

Remove the Pod with:

```bash
kubectl delete -f pod.yaml
```

## Troubleshooting

- If the Pod doesn't start, inspect logs and events:

```bash
kubectl describe pod nginx
kubectl logs pod/nginx
```






