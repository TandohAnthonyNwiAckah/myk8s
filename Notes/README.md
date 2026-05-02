# Kubernetes & Cloud-Native Notes

Short, practical reference covering common Kubernetes, GitOps, and service-mesh concepts.

## Table of contents
- [Purpose](#purpose)
- [Quick Concepts](#quick-concepts)
- [Kubernetes Cheatsheet](#kubernetes-cheatsheet)
- [Observability & Networking](#observability--networking)
- [GitOps & Operators](#gitops--operators)
- [Examples & Commands](#examples--commands)
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


- ✅ A Service of type LoadBalancer exposes a Service externally, while an Ingress provides more advanced routing (like host/path-based rules), but still relies on Services as the backend—not Pods directly.
A LoadBalancer Service gives you an external IP, but does not handle TLS termination or routing rules by itself.
=======
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

- ✅ A RoleBinding is namespace-scoped, meaning it grants permissions only within a specific namespace.
If you want to grant permissions across all namespaces (cluster-wide), you would use a ClusterRoleBinding, not a RoleBinding.

- Roles/ClusterRoles define permissions, Bindings assign them.

- ClusterRoleBinding → ONLY works with ClusterRole

- A RoleBinding can use a ClusterRole, but it does NOT make it cluster-wide.

- ✅ A Headless Service in Kubernetes is a Service that does not get a ClusterIP and therefore does not provide load balancing or a single virtual IP.
Instead, it gives you direct access to the individual Pod IPs.
A Headless Service = DNS-based service discovery without load balancing.
Inside Kubernetes, service discovery is DNS-based, not IP-based.


- ✅ The Prometheus server is the core component responsible for:
Service discovery (finding targets like Pods, Services, Nodes via Kubernetes APIs).
Scraping metrics from those targets at defined intervals (via scrape_configs)
- Node Exporter → exposes node-level metrics.
- kube-state-metrics → exposes Kubernetes object state metrics.


- ✅ To get a Guaranteed QoS class in Kubernetes, the rule is strict:
Every container in the Pod must have CPU and memory requests set, and those requests must be exactly equal to their limits.

- ✅ An API Gateway sits at the edge of a microservices system and acts as a reverse proxy that routes client requests to the appropriate services. It centralizes concerns like authentication, rate limiting, routing, and aggregation—making it easier for clients to interact with a complex backend.


- ✅ Kubernetes Secrets storage are base64-encoded by default,

- Metrics → Prometheus

- Dashboards/Visualization → Grafana

- Tracing (request flow) → Jaeger

- Logging → Fluentd

- Networking / Service Mesh → Envoy


- ✅ CAP Theorem.

- C = Consistency (all nodes see same data)
- A = Availability (every request gets a response)
- P = Partition Tolerance (system works despite network splits)

- > Partition tolerance is mandatory in distributed systems

- So you choose:
- CP (Consistency + Partition tolerance) → e.g., strong DBs
- AP (Availability + Partition tolerance) → e.g., DNS, Cassandra

- > You cannot have all 3 simultaneously

- ResourceQuota sets overall limits per namespace.

- LimitRange sets per-Pod/container defaults and limits, not totals.

- The kubelet runs on each node and ensures containers are started and stay healthy.

- ✔️ kubectl describe pod <pod-name>
This is the most useful for lifecycle diagnostics—it shows events, conditions, scheduling issues, container states, and errors.

- runC is the native runtime for Open Container Initiative (OCI) compliant.
