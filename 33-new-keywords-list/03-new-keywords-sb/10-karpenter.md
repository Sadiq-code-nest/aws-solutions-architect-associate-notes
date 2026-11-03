# Karpenter

### Definition

Karpenter is a Kubernetes node provisioning/scaling solution that automatically provisions compute capacity when Pods cannot be scheduled because existing nodes don't have sufficient capacity.

```
Pods
 |
 | Pending / unschedulable
 v
Karpenter
 |
 | Select appropriate capacity
 v
AWS compute
 |
 v
New Node
 |
 v
Pod scheduled
```

It can also consolidate/remove unnecessary capacity when demand falls.

### Example

Holiday traffic:

```
20 Pods
   ↓
Existing nodes full
   ↓
Some Pods Pending
   ↓
Karpenter
   ↓
Provision additional nodes
   ↓
Pods scheduled
```

### Karpenter vs Cluster Autoscaler

| Karpenter | Cluster Autoscaler |
| --- | --- |
| Provisions nodes based on workload requirements | Adjusts node-group size |
| Flexible instance selection | Primarily scales configured node groups |
| Can optimize/consolidate capacity | More traditional node-group model |
| Strong fit for dynamic EKS capacity | Proven Kubernetes autoscaling solution |

### Exam trigger

> **"Pending Pods need suitable new nodes automatically" → Karpenter**
> 

---

#
