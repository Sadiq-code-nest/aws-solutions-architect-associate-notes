# Kubernetes Metrics Server

### Definition

Metrics Server collects resource usage metrics from Kubernetes nodes and Pods and makes them available through the Kubernetes API for mechanisms such as HPA.

```
Nodes / Pods
     |
     | CPU / Memory metrics
     v
Metrics Server
     |
     v
Kubernetes API
     |
     v
HPA
```

### Example

```
Pod CPU = 90%
      |
      v
Metrics Server
      |
      v
HPA sees high utilization
      |
      v
Increase replicas
```

### Common confusion

Metrics Server **does not itself perform autoscaling**.

Think:

```
Metrics Server = measurement
HPA             = decision/action
```

### Exam trigger

> **"HPA needs CPU/memory resource metrics" → Metrics Server**
>
