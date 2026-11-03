# EC2 Spot Instances & Spot Fleets — SAA-C03

## 1. EC2 Spot Instance — Core Idea

**Spot Instances = use spare AWS EC2 capacity at a large discount, but AWS can interrupt it.**

The trade-off is simple:

> **Cheap compute ↔ interruption risk**
> 

Spot Instances can provide **up to 90% discount compared with On-Demand**.

### When should you use Spot?

Use Spot when the workload:

- can tolerate interruption
- can restart/retry
- is not dependent on one continuously running instance
- is batch-oriented
- is doing data processing
- is performing distributed workloads
- can checkpoint progress

### Typical scenarios

| Scenario | Spot? |
| --- | --- |
| Batch processing | ✅ Excellent |
| Data analysis | ✅ Excellent |
| Big-data processing | ✅ Good |
| Stateless web workers | ✅ Often good |
| CI/CD build workers | ✅ Often good |
| Image/video processing | ✅ Good |
| Fault-tolerant distributed application | ✅ Excellent |
| Critical production database | ❌ Poor choice |
| Single server that must never stop | ❌ Poor choice |
| Long-running critical job with no checkpointing | ❌ Poor choice |

---

# 2. Spot Price

A Spot Instance has a variable price based on AWS capacity and demand.

You can define a **maximum price** you're willing to pay.

Conceptually:

```
Spot price < Your maximum price
        ↓
   Instance runs

Spot price > Your maximum price
        ↓
   AWS can interrupt instance
```

### Example

You specify:

```
Maximum Spot price = $0.05/hour
```

If the applicable Spot price is below that amount, your instance can run.

If AWS interrupts the Spot Instance, you receive a **2-minute interruption notice**.

### Exam clue

If the question says:

> **"Can tolerate interruptions"**
> 

→ **Spot**

If it says:

> **"Lowest possible EC2 cost" + "workload can be interrupted"**
> 

→ **Spot**

---

# 3. Spot Interruption

Spot Instances are **not guaranteed to remain running**.

AWS can reclaim the capacity.

You get approximately:

> **2 minutes warning**
> 

During this period, the application should save/checkpoint work or shut down gracefully.

```
Spot Instance running
       ↓
AWS needs capacity
       ↓
Interruption notice
       ↓
~2 minutes
       ↓
Instance interrupted
```

### Exam clue

**"2-minute interruption notice" → Spot Instance**

### Important

Do not think:

> "I chose a high maximum price, so AWS cannot interrupt me."
> 

❌ Wrong.

The maximum price is **not a guarantee of availability**.

Spot capacity can still be reclaimed.

---

# 4. Spot Instance Request

A Spot request describes what capacity you want.

Important parameters include:

- Maximum price
- Desired number of instances
- Launch specification
- Request type
- Valid-from / valid-until period

Conceptually:

```
Spot Request
   │
   ├── Maximum price
   ├── Desired capacity
   ├── Instance configuration
   ├── One-time / persistent
   └── Validity period
```

---

# 5. One-Time vs Persistent Spot Request

| Type | What happens? | Exam idea |
| --- | --- | --- |
| **One-time** | Request is fulfilled; if instance is interrupted, the request does not automatically create another instance | Simple temporary workload |
| **Persistent** | Request remains active and can attempt to launch another Spot Instance after interruption | Want capacity maintained |

### Shortcut

> **One-time = one attempt**
> 

> **Persistent = keep trying**
> 

---

# 6. Cancelling a Spot Request

This is a **classic exam trap**.

### Cancelling the Spot Request does NOT automatically terminate the running Spot Instance.

You need to:

```
1. Cancel Spot Request
        ↓
2. Terminate associated Spot Instance
```

### Exam clue

> **"Cancel Spot request but instance is still running"**
> 

That's expected behavior.

### Another detail

You can cancel Spot Instance requests that are:

- Open
- Active
- Disabled

---

# 7. Spot Instance vs Spot Request

Don't confuse these:

**Spot Request**

→ asks AWS for Spot capacity.

**Spot Instance**

→ the actual EC2 instance running using Spot pricing.

```
Spot Request
      ↓
AWS finds capacity
      ↓
Spot Instance
```

---

# 8. Spot Fleet

## What is Spot Fleet?

A **Spot Fleet** is a collection of Spot Instances, with the optional ability to include On-Demand Instances.

Its purpose is to help obtain the desired compute capacity from multiple possible pools.

```
                 Spot Fleet
                     │
       ┌─────────────┼─────────────┐
       ↓             ↓             ↓
   Spot Pool 1   Spot Pool 2   Spot Pool 3
       ↓             ↓             ↓
    EC2 type      EC2 type      EC2 type
```

You can define multiple launch pools based on combinations such as:

- Instance type
- Availability Zone
- OS
- Other launch requirements

This gives the fleet multiple places from which to obtain capacity.

---

# 9. Why Use Multiple Spot Pools?

Imagine you need:

```
100 instances
```

Instead of requesting all 100 from one instance type/AZ combination, you can allow:

```
m5.large / AZ-A
m5.large / AZ-B
m5.xlarge / AZ-A
m5.xlarge / AZ-B
```

Now Spot Fleet has more options.

If capacity disappears from one pool, it can use another suitable pool.

### Exam clue

> **"Multiple instance types / Availability Zones / pools"**
> 

→ Think **Spot Fleet**

---

# 10. Spot Fleet Allocation Strategies

This is very exam-relevant.

| Strategy | What it tries to do | Best scenario | Memory |
| --- | --- | --- | --- |
| **lowestPrice** | Choose pools with the lowest Spot price | Cost-focused short workloads | 💰 **Lowest price** |
| **diversified** | Spread instances across pools | Availability / long workloads | 🌐 **Diversify** |
| **capacityOptimized** | Select pools with optimal available capacity | Reduce interruption risk | 🛡️ **Capacity first** |
| **priceCapacityOptimized** | Prefer pools with strong capacity, then choose lower price | Best general choice | ⭐ **Capacity + Price** |

---

# 11. lowestPrice

The fleet chooses the pool with the **lowest price**.

### Best for

Short-lived workloads where cost is the primary concern.

```
Pool A = $0.02
Pool B = $0.03
Pool C = $0.05

        ↓

Choose Pool A
```

### Exam clue

> **"lowest cost / cheapest Spot pool"**
> 

→ **lowestPrice**

### Trap

Cheapest doesn't necessarily mean most available.

---

# 12. diversified

Instances are distributed across multiple pools.

Example:

```
Pool A → 25 instances
Pool B → 25 instances
Pool C → 25 instances
Pool D → 25 instances
```

### Why?

If one Spot pool becomes unavailable, you don't lose your entire fleet.

### Best for

> **Long-running workloads where availability matters**
> 

### Exam clue

**"spread across multiple pools"**

→ **diversified**

### Memory

> **Diversified = Don't put all Spot eggs in one basket.**
> 

---

# 13. capacityOptimized

The fleet chooses the pool that has the **best available Spot capacity** for the requested instances.

### Goal

Reduce the probability of interruption caused by insufficient Spot capacity.

### Exam clue

> **"minimize interruptions"**
> 

> **"choose pool with optimal capacity"**
> 

→ **capacityOptimized**

### Memory

> **Capacity first.**
> 

---

# 14. priceCapacityOptimized ⭐

This is the recommended strategy for most workloads.

It considers:

1. **Available capacity**
2. Then **price**

Conceptually:

```
Find pools with strong capacity
          ↓
Among those pools
          ↓
Choose the lower-priced option
```

### Why?

You don't want:

```
CHEAP but unavailable
```

You want:

```
GOOD CAPACITY
+
GOOD PRICE
```

### Exam clue

> **"best balance between price and availability/capacity"**
> 

→ **priceCapacityOptimized**

### Memory

> **Price + Capacity = priceCapacityOptimized**
> 

---

# 15. Spot Fleet — Target Capacity

You can define a target capacity.

Example:

```
Target capacity = 20 instances
```

The Spot Fleet attempts to maintain the requested capacity within the configured constraints.

It stops launching when the desired capacity is reached or when configured limits prevent additional capacity.

---

# 16. Spot Fleet + On-Demand

Spot Fleet doesn't have to be 100% Spot.

It can combine:

```
Spot Instances
+
On-Demand Instances
```

Example:

```
Required capacity = 20

10 Spot
+
10 On-Demand
```

This can provide a balance between:

- lower cost
- more reliable capacity

### Exam clue

> **"mix Spot and On-Demand capacity"**
> 

→ **Spot Fleet**

---

# 17. Spot Fleet vs Auto Scaling Group

Don't confuse these.

|  | Spot Fleet | EC2 Auto Scaling |
| --- | --- | --- |
| Main goal | Obtain Spot capacity from multiple pools | Maintain desired application capacity |
| Pool selection | Multiple Spot pools | Uses launch configuration/template and ASG policies |
| Scaling policies | Not its main concept | Target tracking, step, scheduled, etc. |
| Typical purpose | Cost-efficient compute fleet | Application elasticity + HA |

For modern architectures, **EC2 Auto Scaling can also use Spot Instances**, so don't assume Spot Fleet is the only way to use Spot.

---

# 18. Spot vs On-Demand — Exam Decision

| Requirement | Pick |
| --- | --- |
| Cannot tolerate interruption | **On-Demand** |
| Short unpredictable workload | **On-Demand** |
| Fault-tolerant batch processing | **Spot** |
| Cheapest possible EC2 compute | **Spot** |
| Critical database | **On-Demand / appropriate commitment model** |
| Workload can checkpoint and retry | **Spot** |
| Need guaranteed capacity independent of Spot interruption | **On-Demand** |

### Brain shortcut

> **Critical → On-Demand**
> 

> **Can disappear → Spot**
> 

---

# 19. Spot vs Reserved Instances

Another common exam confusion:

|  | Spot | Reserved |
| --- | --- | --- |
| Discount | Very large, potentially up to ~90% | Significant commitment discount |
| Interruption | **Yes** | No Spot-style interruption |
| Commitment | No long-term commitment required for Spot itself | Usually 1 or 3-year commitment |
| Best for | Interruptible workloads | Predictable steady workloads |
| Example | Batch processing | Production database running continuously |

### Shortcut

> **Steady + predictable → Reserved/Savings Plan**
> 

> **Flexible + interruptible → Spot**
> 

---

# 20. Spot + Fault-Tolerant Architecture

Spot works particularly well when the application is designed to survive instance loss.

Example:

```
             SQS Queue
                 │
        ┌────────┼────────┐
        ↓        ↓        ↓
      Spot     Spot     Spot
      EC2      EC2      EC2
        │        │        │
        └────────┼────────┘
                 ↓
              Database
```

If one instance disappears:

```
Spot EC2 ❌
    ↓
Another worker processes messages
```

This is why **SQS + Spot + Auto Scaling** can be a powerful cost-optimized architecture.

---

# 21. Spot + Checkpointing

For long-running jobs, checkpointing is extremely useful.

Example:

```
Job processing 1 TB
       ↓
Checkpoint every 10 GB
       ↓
Spot interruption
       ↓
Restart from latest checkpoint
```

Instead of restarting from zero.

### Exam clue

> **"work can be checkpointed/resumed"**
> 

→ Spot becomes a strong choice.

---

# 22. Spot Instance — Common Exam Traps

| Wrong thinking | Correct thinking |
| --- | --- |
| "Spot is cheap, so use it everywhere." | ❌ Only for interruption-tolerant workloads |
| "Maximum price guarantees the instance." | ❌ Spot can still be interrupted |
| "Cancelling Spot request terminates instance." | ❌ Cancel request and terminate instance separately |
| "Spot is good for critical DB." | ❌ Generally inappropriate |
| "Spot has unlimited availability." | ❌ Capacity can disappear |
| "Spot Fleet means only one instance type." | ❌ Fleet can use multiple pools |
| "lowestPrice gives best availability." | ❌ It prioritizes price |
| "diversified gives lowest price." | ❌ It prioritizes distribution |
| "capacityOptimized means cheapest." | ❌ It prioritizes capacity |
| "priceCapacityOptimized means cheapest pool overall." | ❌ It considers capacity first, then price |

---

# 🔥 Spot Exam Trigger Table

| **If question says...** | **Think...** |
| --- | --- |
| **Up to 90% discount** | **Spot** |
| **Spare AWS capacity** | **Spot** |
| **Can tolerate interruptions** | **Spot** |
| **Batch processing** | **Spot** |
| **Data analysis** | **Spot** |
| **Fault tolerant workload** | **Spot** |
| **2-minute interruption notice** | **Spot** |
| **Checkpoint and retry** | **Spot** |
| **Maximum Spot price** | **Spot Request** |
| **Cancel request doesn't terminate instance** | **Cancel request + terminate instance separately** |
| **Multiple instance types/AZs/pools** | **Spot Fleet** |
| **Cheapest pool** | **lowestPrice** |
| **Spread across pools** | **diversified** |
| **Best available capacity** | **capacityOptimized** |
| **Best balance of capacity + price** | **priceCapacityOptimized** |
| **Mix Spot + On-Demand** | **Spot Fleet** |

---

# 🧠 FINAL 30-SECOND MEMORY

```
SPOT
│
├── Cheap → up to 90% discount
│
├── Risk → AWS can interrupt
│
├── Warning → ~2 minutes
│
├── Best for → batch / analysis / fault-tolerant
│
└── Spot Request
      │
      ├── One-time
      └── Persistent

SPOT FLEET
│
├── Multiple pools
│
├── Multiple instance types / AZs
│
├── Optional On-Demand
│
└── Allocation strategy
      │
      ├── lowestPrice
      ├── diversified
      ├── capacityOptimized
      └── priceCapacityOptimized ⭐
```

## ⭐ Exam Master Rule

> **If the workload can be interrupted → SPOT.**
> 
> 
> **If you need multiple Spot capacity pools → SPOT FLEET.**
> 
> **If you want cheapest → lowestPrice.**
> 
> **If you want distribution/availability → diversified.**
> 
> **If you want capacity → capacityOptimized.**
> 
> **If you want capacity + price → priceCapacityOptimized.**
> 
> **If the workload is critical and cannot tolerate interruption → NOT SPOT.**
>
