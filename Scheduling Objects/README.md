# Kubernetes Scheduling Objects

This guide covers the core Kubernetes scheduling objects and concepts used to control how Pods are assigned to Nodes.

---

## 🧠 Overview

The Kubernetes scheduler (kube-scheduler) determines which node a Pod runs on based on constraints and preferences defined in the Pod spec.

Scheduling controls help answer:
- Where *can* a Pod run?
- Where *should* it run?
- Where *should it avoid* running?

---

## 🔑 Scheduling Objects

### 1. NodeSelector

**Simple node selection using labels (hard requirement).**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  nodeSelector:
    disktype: ssd
```

- Matches node labels exactly
- Easy to use
- Not flexible (no logic like OR/NOT)   



### 2. Node Affinity

More expressive version of NodeSelector with support for rules.


#### a) Required (Hard Constraint)

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: disktype
              operator: In
              values:
                - ssd
```                

- Pod must match rules to be scheduled



#### b) Preferred (Soft Constraint)

```yaml
affinity:
  nodeAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
            - key: disktype
              operator: In
              values:
                - ssd

```  

- Scheduler tries to match, but not required



### 3. Taints and Tolerations

Nodes repel Pods unless Pods tolerate them.

Taint (applied to node):

> kubectl taint nodes node1 key=value:NoSchedule


Toleration (in Pod):

```yaml
tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"

``` 

- Prevents unwanted Pods from being scheduled
- Common in dedicated or special-purpose nodes    



### 4. Pod Affinity and Anti-Affinity

Controls scheduling based on other Pods.


#### Pod Affinity (co-locate Pods)

```yaml
affinity:
  podAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchLabels:
            app: backend
        topologyKey: "kubernetes.io/hostname"

``` 

- Places Pods near other matching Pods



#### Pod Anti-Affinity (separate Pods)

```yaml
affinity:
  podAntiAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchLabels:
            app: frontend
        topologyKey: "kubernetes.io/hostname"

``` 

- Spreads Pods across nodes



### 5. Topology Spread Constraints

Ensures Pods are evenly distributed across nodes/zones.


```yaml
topologySpreadConstraints:
  - maxSkew: 1
    topologyKey: topology.kubernetes.io/zone
    whenUnsatisfiable: DoNotSchedule
    labelSelector:
      matchLabels:
        app: my-app
```         

- Helps improve availability and fault tolerance







🎯 Key Takeaways
- NodeSelector = simplest way to constrain scheduling

- Node Affinity = flexible and powerful

- Taints = nodes reject Pods unless tolerated

- Pod Affinity = group Pods together

- Pod Anti-Affinity = spread Pods apart

- Topology Spread = balance Pods across infrastructure

