# Cross-Zone Load Balancing — AWS SAA-C03

**Cross-Zone Load Balancing** controls **how a load balancer distributes traffic across Availability Zones (AZs).**

The key idea:

> **With Cross-Zone enabled, each load balancer node can send traffic to targets in ALL enabled AZs.**
> 

---

## 1. Without Cross-Zone Load Balancing

Each Load Balancer node sends traffic **only to targets in its own AZ**.

Example:

```
              ALB / NLB
             /         \
           AZ-1        AZ-2
            │            │
          Node          Node
            │            │
        ┌───┴───┐    ┌───┴───┐
        EC2     EC2  EC2
```

Suppose:

| AZ | EC2 Instances |
| --- | --- |
| AZ-1 | 2 |
| AZ-2 | 2 |

Traffic is distributed within each AZ.

---

# 2. With Cross-Zone Load Balancing

Now each LB node can distribute traffic across **all registered targets in all AZs**.

```
                 Load Balancer
                /             \
              AZ-1           AZ-2
               │               │
               └──────┬────────┘
                      │
             All registered targets
               │    │    │    │
              EC2  EC2  EC2  EC2
```

Example:

| AZ | EC2 Instances |
| --- | --- |
| AZ-1 | 2 |
| AZ-2 | 4 |

With cross-zone enabled, traffic can be distributed across **all 6 instances**, rather than each LB node being restricted to its local AZ.

---

# Master Table — Cross-Zone Load Balancing

| Load Balancer | Default | Can Enable/Disable? | Cross-AZ Data Transfer Charge |
| --- | --- | --- | --- |
| **Application Load Balancer (ALB)** | ✅ Enabled | ✅ Can disable at Target Group level | **No charge** |
| **Network Load Balancer (NLB)** | ❌ Disabled | ✅ Can enable | **Yes, charges apply** |
| **Gateway Load Balancer (GWLB)** | ❌ Disabled | ✅ Can enable | **Yes, charges apply** |
| **Classic Load Balancer (CLB)** | ❌ Disabled | ✅ Can enable | **No charge** |

### ⭐ Exam memory

```
ALB  → ON by default → FREE
NLB  → OFF by default → $
GWLB → OFF by default → $
CLB  → OFF by default → FREE
```

---

# Why Is Cross-Zone Useful?

Consider an unbalanced deployment:

```
AZ-1 → 2 EC2
AZ-2 → 6 EC2
```

Without cross-zone:

```
AZ-1 LB Node → only 2 EC2
AZ-2 LB Node → only 6 EC2
```

The 2 instances in AZ-1 could receive proportionally more traffic.

With cross-zone:

```
AZ-1 LB Node ──┐
               ├──► all 8 EC2 instances
AZ-2 LB Node ──┘
```

Traffic can be distributed across all targets.

---

# Cross-Zone vs Normal Load Balancing

|  | **Without Cross-Zone** | **With Cross-Zone** |
| --- | --- | --- |
| Traffic scope | Targets in same AZ | Targets across all enabled AZs |
| Can use targets in another AZ? | ❌ | ✅ |
| Helps uneven AZ distribution? | ❌ | ✅ |
| Inter-AZ traffic | Lower | Potentially higher |
| Cross-AZ data charges | Depends on LB | Depends on LB type |

---

# 🧠 SAA-C03 Scenario

### Scenario

You have:

```
AZ-1 → 2 EC2
AZ-2 → 8 EC2
```

Traffic is uneven because each LB node sends requests only to targets in its own AZ.

### Solution

> **Enable Cross-Zone Load Balancing.**
> 

Now the LB can distribute requests across targets in both AZs.

---

# ⚠️ Important Exam Trap

Don't confuse **Cross-Zone Load Balancing** with **Multi-AZ**.

### Multi-AZ

Means:

> Run resources across multiple Availability Zones for **high availability**.
> 

### Cross-Zone Load Balancing

Means:

> Allow a load balancer node to distribute traffic to targets across **multiple Availability Zones**.
> 

You can have Multi-AZ without necessarily having cross-zone balancing enabled.

---

# 🔥 Final Memory Trick

> **Cross-Zone = "Don't stay inside my AZ; use targets across AZs."**
> 

And memorize:

**ALB → Enabled + Free**

**NLB → Disabled + Paid if enabled**

**GWLB → Disabled + Paid if enabled**

**CLB → Disabled + Free if enabled**
