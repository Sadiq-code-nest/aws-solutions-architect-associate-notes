# Cluster Autoscaler

### Definition

Cluster Autoscaler automatically adjusts the number of nodes in a Kubernetes cluster based on whether Pods can be scheduled on the existing nodes.

```
Pending Pod
    |
    v
Cluster Autoscaler
    |
    v
Increase node group
    |
    v
New node
    |
    v
Pod scheduled
```

When capacity is no longer needed:

```
Unused nodes
     |
     v
Cluster Autoscaler
     |
     v
Scale node group down
```

### Example

```
Node Group:
min = 2
max = 10

Demand increases
      ↓
2 → 3 → 4 → 5 nodes

Demand decreases
      ↓
5 → 4 → 3 → 2 nodes
```

### Exam trigger

> **"Automatically adjust EKS node count based on unschedulable Pods" → Cluster Autoscaler**
>
