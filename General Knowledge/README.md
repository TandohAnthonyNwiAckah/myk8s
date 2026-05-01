# Kubernetes & Cloud-Native Notes

Short, practical reference covering common Kubernetes, GitOps, and service-mesh concepts.

## Table of contents
- [Purpose](#purpose)
- [Quick Concepts](#quick-concepts)
- [Kubernetes Cheatsheet](#kubernetes-cheatsheet)
- [Observability & Networking](#observability--networking)
- [GitOps & Operators](#gitops--operators)
- [Examples & Commands](#examples--commands)
- [Diagram](#diagram)
- [Printable Cheat-sheet](#printable-cheat-sheet)

## Purpose
Capture concise reminders and best-practice notes for Kubernetes, service meshes, autoscaling, and related cloud-native topics.

## Quick Concepts
- Service mesh: manages service-to-service communication (routing, retries, circuit breaking, mTLS, observability).
- HPA (Horizontal Pod Autoscaler): scales replicas by CPU/memory or custom metrics.
- VPA (Vertical Pod Autoscaler): adjusts resource requests/limits for Pods (not replica count).
- Cluster Autoscaler: adds/removes nodes based on scheduling needs.
- CNI (Container Network Interface): plugin model for Kubernetes networking.
- StorageClass + PVC: StorageClass describes provisioning; PVC requests storage which can be dynamically provisioned.

## Kubernetes Cheatsheet
- Pod QoS: `Guaranteed` requires requests == limits for CPU and memory on all containers.
- OOMKilled: container exceeded memory limit and was terminated by the kernel OOM killer.
- CPU throttling: enforced only when a CPU limit is configured.
- `maxUnavailable` / `maxSurge`: control rolling update disruption and extra pods during deploys.

## Observability & Networking
- `kube-proxy`: implements Service networking rules (iptables or IPVS) on each node.
- `CoreDNS`: cluster DNS resolver for services (`*.svc.cluster.local`).
- Prometheus: does service discovery and scrapes targets; use `node_exporter` and `kube-state-metrics` for node/state metrics.
- Headless Service: no ClusterIP; DNS returns Pod IPs for direct discovery.

Patterns for resiliency:
- Rate limiting: protect services from overload.
- Circuit breaker: stop repeated calls to failing services.
- Bulkhead: isolate failures across components.
- Load leveling: use queues to smooth bursts.

## GitOps & Operators
- GitOps model: Git (desired state) → Cluster (reconciler pulls from Git and applies changes). One-way flow.
- Role vs ClusterRole vs Bindings:
  - `Role` (namespace-scoped), `ClusterRole` (cluster-scoped permissions).
  - `RoleBinding` grants a Role/ClusterRole within a namespace.
  - `ClusterRoleBinding` grants a ClusterRole cluster-wide.
- Operators: extend Kubernetes APIs via CRDs and automate lifecycle for custom resources.

## Examples & Commands
Practical, copyable snippets for common tasks.

### Kubectl
```bash
# show pods and their resources
kubectl get pods -o wide

# describe an OOMKilled pod
kubectl describe pod <pod-name> -n <namespace>

# horizontal autoscaler using CPU
kubectl autoscale deployment my-app --cpu-percent=70 --min=2 --max=10

# apply a NetworkPolicy (example file: networkpolicy.yaml)
kubectl apply -f networkpolicy.yaml -n my-namespace

# rollout status while updating a deployment
kubectl rollout status deployment/my-app -n my-namespace
```

NetworkPolicy example (networkpolicy.yaml):
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-from-frontend
spec:
  podSelector:
    matchLabels:
      role: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 8080
```

### Ingress (NGINX) example
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
```

### Istio VirtualService snippet
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: my-virtualservice
spec:
  hosts:
  - my-service
  http:
  - route:
    - destination:
        host: my-service
        subset: v1
      weight: 80
    - destination:
        host: my-service
        subset: v2
      weight: 20
```

### Prometheus scrape config (excerpt)
```yaml
scrape_configs:
  - job_name: 'kubernetes-pods'
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
```

### Helm
```bash
# install a chart with values file
helm install myapp ./chart -f values.yaml

# upgrade
helm upgrade myapp ./chart -f values.yaml

# template output (inspect generated manifests)
helm template myapp ./chart -f values.yaml
```

### Quick troubleshooting commands
```bash
# view logs for a pod
kubectl logs -f pod/my-pod -n my-namespace

# get events to diagnose scheduling/oom issues
kubectl get events -n my-namespace --sort-by='.lastTimestamp'
```

## Diagram
Below is a simple GitOps + service mesh flow; renderers that support Mermaid will show this visually.

```mermaid
flowchart LR
  subgraph DEV
    A[Git repo: manifests & Helm] -->|push| B[Git Branch]
  end
  B --> C[Reconciler (Flux/ArgoCD) in Cluster]
  C --> D[Apply resources]
  D --> E[Services & Deployments]
  E --> F[Service Mesh (Istio/Linkerd)]
  F --> G[Observability: Prometheus / Grafana]
  style A fill:#f9f,stroke:#333,stroke-width:1px
```

## Printable Cheat-sheet
A compact one-page listing to print or pin.

- Deploy
  - `kubectl apply -f <file>`
  - `helm install <name> ./chart -f values.yaml`
- Inspect
  - `kubectl get pods -n <ns> -o wide`
  - `kubectl describe pod <pod>`
  - `kubectl logs -f <pod>`
- Autoscaling
  - `kubectl autoscale deployment my-app --cpu-percent=70 --min=2 --max=10`
- Debugging
  - `kubectl get events -n <ns>`
  - `kubectl top pods` (metrics-server required)
- Networking
  - `kubectl get svc,ingress -n <ns>`
  - Use NetworkPolicy to restrict pod-to-pod traffic

---
