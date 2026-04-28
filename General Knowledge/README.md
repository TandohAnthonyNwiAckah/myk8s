## General Knowledge

- A service mesh’s core role is to manage service-to-service communication, which primarily includes traffic routing, service discovery, and load balancing.

- The Horizontal Pod Autoscaler (HPA) in Kubernetes can automatically scale the number of pods in a Deployment or ReplicaSet based on CPU utilization, memory utilization, or even custom metrics (such as application-specific metrics provided through a metrics server or external monitoring system).

- A NetworkPolicy = rules that control Pod-to-Pod and Pod-to-network traffic using labels, IPs, and ports.

- A StorageClass in Kubernetes defines how storage should be provisioned (e.g., which provisioner to use, parameters like disk type, replication, etc.). When a PVC references a StorageClass, Kubernetes can automatically create a matching PersistentVolume instead of requiring one to be pre-created.

- When a container is terminated with an OOMKilled status in Kubernetes, it means the process was killed by the Out-Of-Memory (OOM) killer because it used more memory than its defined limit. Kubernetes enforces memory limits strictly, so exceeding them results in the container being terminated and restarted.

- kube-proxy runs on each node and implements the Kubernetes Service abstraction by maintaining network rules (typically via iptables or IPVS). These rules enable traffic routing from inside or outside the cluster to the appropriate Pods.