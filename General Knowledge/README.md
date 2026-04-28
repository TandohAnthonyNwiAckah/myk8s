## General Knowledge

- A service mesh’s core role is to manage service-to-service communication, which primarily includes traffic routing, service discovery, and load balancing.

- A service mesh like Istio focuses on traffic management, security, and observability, not scaling workloads.
It does handle advanced load balancing, retries, circuit breaking, mTLS, and intelligent routing.

- The Horizontal Pod Autoscaler (HPA) in Kubernetes can automatically scale the number of pods in a Deployment or ReplicaSet based on CPU utilization, memory utilization, or even custom metrics (such as application-specific metrics provided through a metrics server or external monitoring system).

- A NetworkPolicy = rules that control Pod-to-Pod and Pod-to-network traffic using labels, IPs, and ports.

- The Container Network Interface (CNI) is a Kubernetes standard for networking plugins.
- > CNI = rules/interface
- > Plugins = execution/ implementation

- A StorageClass in Kubernetes defines how storage should be provisioned (e.g., which provisioner to use, parameters like disk type, replication, etc.). When a PVC references a StorageClass, Kubernetes can automatically create a matching PersistentVolume instead of requiring one to be pre-created.

- When a container is terminated with an OOMKilled status in Kubernetes, it means the process was killed by the Out-Of-Memory (OOM) killer because it used more memory than its defined limit. Kubernetes enforces memory limits strictly, so exceeding them results in the container being terminated and restarted.

- kube-proxy runs on each node and implements the Kubernetes Service abstraction by maintaining network rules (typically via iptables or IPVS). These rules enable traffic routing from inside or outside the cluster to the appropriate Pods.

- A Service of type LoadBalancer exposes a Service externally, while an Ingress provides more advanced routing (like host/path-based rules), but still relies on Services as the backend—not Pods directly.

- In Kubernetes, DNS resolution for Services inside the cluster is handled by CoreDNS. When a Pod tries to resolve something like my-service.my-namespace.svc.cluster.local, the request goes to the cluster DNS service (CoreDNS), which returns the appropriate ClusterIP.

- In Domain-Driven Design (DDD), a bounded context defines clear boundaries where a specific domain model applies. Aligning microservices with these bounded contexts is considered a best practice.

- CPU throttling in Kubernetes is enforced only when a CPU limit is defined. Without a limit, the container can use as much CPU as the node allows.

- Cluster Autoscaler: scales nodes

- Vertical Pod Autoscaler (VPA): adjusts resource requests/limits, not replica count.

- Horizontal Pod Autoscaler (HPA): scales based on CPU/memory or custom metrics

- Kubernetes Event-Driven Autoscaling (KEDA): event-driven autoscaling.



- A Helm chart is basically a packaged set of Kubernetes resources that you can install, reuse, and share. Think of it like this: instead of manually writing and applying a bunch of YAML files for deployments, services, configs, etc., you bundle everything into one reusable package called a chart.

- Helm = package manager (like apt, npm)

- Helm chart = installable package (like a .deb or npm package)

- In Kubernetes, Operators extend the API by introducing new object types, and this is done through Custom Resource Definitions (CRDs). A CRD lets you define your own resource (kind, API version, schema), which Kubernetes then treats like built-in objects. The Operator watches these custom resources and acts on them.

- ✅ Rate Limiting – helps prevent overload by controlling incoming traffic
- ✅ Circuit Breaker – stops repeated calls to failing services and allows recovery
- ✅ Bulkhead – isolates components so failures don’t spread across the system

- ✅ Load Leveling – smooths traffic spikes (often via queues), improving stability under stress