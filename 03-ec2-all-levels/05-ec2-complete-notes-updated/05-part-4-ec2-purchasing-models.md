# EC2 — PART 4: PURCHASING MODELS

This is a **very high-value SAA-C03 topic** because AWS gives you several ways to answer what sounds like the same question:

> “How should the company run EC2 more cheaply?”
> 

The exam is really testing **three separate dimensions**:

1. **How predictable is the workload?**
2. **Can the workload be interrupted?**
3. **Do you need a pricing commitment, physical host control, or guaranteed capacity?**

---

# 1. ON-DEMAND

## What is it?

**On-Demand** means you pay for compute capacity as you use it, with no long-term commitment.

Think:

> **Maximum flexibility, no commitment.**
> 

```
Launch EC2
   ↓
Use it
   ↓
Pay
   ↓
Stop/terminate
   ↓
No long-term commitment
```

---

## When to choose On-Demand

Use it when:

- workload is unpredictable
- workload is short-term
- you cannot commit to 1 or 3 years
- you're testing a new application
- you need flexibility
- interruption is unacceptable but long-term usage is uncertain

---

## Exam trigger

> **Unpredictable workload**
> 

> **Short-term workload**
> 

> **Cannot commit**
> 

> **Need maximum flexibility**
> 

→ **On-Demand**

---

# 2. RESERVED INSTANCES

Reserved Instances provide a pricing discount in exchange for a **1-year or 3-year commitment** to an EC2 configuration.

Think:

> **Predictable workload + long commitment = RI**
> 

They are primarily a **pricing construct**, not the same thing as guaranteeing physical capacity.

---

## Typical scenario

A company runs:

```
Web application
24 × 7
365 days/year
```

The workload is predictable.

Instead of paying On-Demand indefinitely:

→ Consider **Reserved Instances**.

---

# 3. STANDARD RESERVED INSTANCE

A **Standard RI** provides a larger discount but is less flexible than a Convertible RI.

Think:

> **Standard RI = stronger commitment, better discount**
> 

---

## Best scenario

You know exactly what you need:

```
m7i.large
Linux
us-east-1
24/7
```

and expect the workload to remain stable.

→ **Standard RI**

---

## Exam trigger

> **Stable workload**
> 

> **Known instance configuration**
> 

> **1 or 3 year commitment**
> 

> **Maximum RI discount**
> 

→ **Standard RI**

---

# 4. CONVERTIBLE RESERVED INSTANCE

A Convertible RI gives you more flexibility to change the instance configuration during the reservation term.

Think:

> **Convertible = configuration can change**
> 

For example, your workload starts on one EC2 configuration but later needs a different configuration.

Convertible RIs allow exchanges for other eligible configurations, subject to AWS rules.

---

## Trade-off

You generally get:

**More flexibility**

but

**less discount than Standard RI**

---

## Exam trigger

> **Long-term commitment**
> 
- 

> **Need ability to change instance configuration**
> 

→ **Convertible RI**

---

# 5. STANDARD RI vs CONVERTIBLE RI

|  | Standard RI | Convertible RI |
| --- | --- | --- |
| Commitment | 1/3 years | 1/3 years |
| Discount | Higher | Lower |
| Flexibility | Lower | Higher |
| Change configuration | Limited | **More flexible** |
| Best for | Stable workload | Long-term but evolving workload |

### Memory

> **Standard = Stable**
> 

> **Convertible = Change**
> 

---

# 6. SAVINGS PLANS

Savings Plans are another way to receive discounted compute pricing by committing to a consistent amount of **compute spend per hour** over a 1- or 3-year term.

This is extremely important:

> **Savings Plans are based on a spending commitment, not simply reserving one exact EC2 instance.**
> 

Example:

```
Commit:
$10/hour
for 1 year
```

Your eligible usage receives discounted pricing up to the commitment.

---

# 7. COMPUTE SAVINGS PLANS

Compute Savings Plans provide the **most flexibility** among Savings Plans.

They can apply across:

- EC2
- Lambda
- Fargate

and allow changes such as:

- instance family
- instance size
- Region
- operating system

within the applicable Savings Plan rules.

---

## Exam trigger

> **Want discounted compute but maximum flexibility**
> 

> **May change EC2 instance families**
> 

> **May move Regions**
> 

> **May use Lambda/Fargate**
> 

→ **Compute Savings Plan**

---

# 8. EC2 INSTANCE SAVINGS PLANS

EC2 Instance Savings Plans provide a lower price than On-Demand in exchange for a commitment to a particular **EC2 instance family in a Region**.

They are less flexible than Compute Savings Plans but generally provide a larger discount.

---

## Example

You know your organization will continuously use:

```
m7i family
us-east-1
```

but might change:

```
m7i.large
→
m7i.xlarge
```

An EC2 Instance Savings Plan can be a strong fit.

---

# 9. COMPUTE SP vs EC2 INSTANCE SP

|  | Compute Savings Plan | EC2 Instance Savings Plan |
| --- | --- | --- |
| Flexibility | **Highest** | Lower |
| EC2 | ✅ | ✅ |
| Lambda | ✅ | ❌ |
| Fargate | ✅ | ❌ |
| Instance family | Can change | **Committed family** |
| Region flexibility | More flexible | Region-specific |
| Best for | Changing compute | Stable EC2 family |

### Memory:

> **Compute SP = Compute is flexible**
> 

> **EC2 Instance SP = EC2 family commitment**
> 

---

# 10. SPOT INSTANCES

Spot Instances use spare AWS EC2 capacity at a substantial discount compared with On-Demand.

The key characteristic is:

> **AWS can interrupt the instance.**
> 

Therefore Spot is appropriate only when the workload can tolerate interruption.

---

## Perfect workloads

- batch processing
- data analysis
- image/video processing
- CI workloads
- stateless workers
- distributed jobs
- fault-tolerant workloads

---

## Bad workloads

Avoid using Spot as the sole capacity for:

- critical databases
- workloads that cannot tolerate interruption
- stateful applications without recovery mechanisms

---

## Exam trigger

> **Interruptible**
> 

> **Flexible workload**
> 

> **Batch**
> 

> **Can restart**
> 

> **Can tolerate interruption**
> 

> **Lowest possible EC2 cost**
> 

→ **Spot**

---

# 11. SPOT INTERRUPTION

This is one of the most important Spot concepts.

AWS can reclaim Spot capacity.

Possible reasons include:

- capacity needs
- infrastructure requirements
- Spot capacity availability

The exam's important idea is simply:

> **Spot is NOT guaranteed capacity.**
> 

---

# 12. TWO-MINUTE INTERRUPTION NOTICE

When AWS needs to interrupt a Spot Instance, AWS provides a **two-minute interruption notice** when applicable.

Conceptually:

```
Spot Instance
     ↓
Interruption notice
     ↓
~2 minutes
     ↓
Interruption
```

Your application should use that time to:

- save state
- stop processing
- checkpoint work
- deregister from load balancers
- transfer work to another worker

---

## Exam trigger

> **Can tolerate a two-minute interruption notice**
> 

→ Spot is likely appropriate.

---

# 13. SPOT REQUEST

A Spot request tells EC2 what Spot capacity you want.

Conceptually it specifies things such as:

- desired capacity
- instance configuration
- maximum price/capacity constraints as applicable
- request type
- validity period

The important distinction:

> **Spot request ≠ Spot Instance**
> 

The request is the mechanism asking AWS to maintain/launch Spot capacity.

---

# 14. ONE-TIME vs PERSISTENT SPOT REQUEST

There are two important request concepts.

## One-time request

The request is fulfilled once.

If the Spot Instance is interrupted:

> The request does not automatically recreate it.
> 

Think:

> **One-time = one launch opportunity**
> 

---

## Persistent request

The request remains active.

If the Spot Instance is interrupted:

> EC2 can attempt to launch another Spot Instance to satisfy the request.
> 

Think:

> **Persistent = keep trying**
> 

---

# 15. ONE-TIME vs PERSISTENT

|  | One-time | Persistent |
| --- | --- | --- |
| Request remains active | ❌ | ✅ |
| Instance interrupted | Doesn't recreate from request | Can request replacement |
| Good for | One batch job | Long-running Spot capacity |

---

# 16. CANCELING SPOT REQUESTS

This is a **classic exam trap**.

Suppose:

```
Spot Request
     ↓
Spot Instance
```

You cancel the Spot request.

What happens to the running Spot Instance?

> **Canceling the request does NOT automatically terminate the existing Spot Instance.**
> 

You must separately terminate the instance if you don't want it running.

---

## Exam trigger

> "Cancel Spot Request"
> 

→ Cancel request.

> "Terminate Spot Instance"
> 

→ Terminate instance separately.

---

# 17. TERMINATING SPOT INSTANCES

This distinction is worth memorizing:

```
Cancel Spot Request
        ≠
Terminate Spot Instance
```

If you have a persistent request and terminate an associated Spot Instance while the request remains active, the request may attempt to launch replacement capacity.

Therefore, when shutting down a persistent Spot workload:

> **Cancel the request AND terminate the instances as appropriate.**
> 

---

# 18. SPOT FLEET

Spot Fleet lets you request a collection of Spot capacity across multiple:

- instance types
- AZs
- capacity pools

It can also include **On-Demand capacity** depending on configuration.

Think:

> **Spot Instance = one type of capacity**
> 

> **Spot Fleet = intelligently manage a pool of capacity**
> 

---

## Why use Spot Fleet?

Suppose you need:

```
100 vCPUs
```

You don't care exactly which instance type provides them.

Spot Fleet can use multiple pools to improve the probability of getting capacity.

---

# 19. SPOT FLEET ALLOCATION STRATEGIES

This is a high-value exam area.

You need to recognize:

1. lowestPrice
2. diversified
3. capacityOptimized
4. priceCapacityOptimized

---

# 20. LOWESTPRICE

The Fleet chooses the lowest-price available pool.

Think:

> **Lowest price wins.**
> 

---

## Benefit

Potentially minimizes cost.

## Risk

You may concentrate capacity in a particular pool.

That can increase interruption/capacity risk depending on market conditions.

---

## Trigger

> **Cheapest Spot capacity**
> 

→ **lowestPrice**

---

# 21. DIVERSIFIED

Diversified spreads Spot capacity across multiple pools.

For example:

```
Pool A → 25%
Pool B → 25%
Pool C → 25%
Pool D → 25%
```

Conceptually, if one pool becomes unavailable, the whole fleet isn't dependent on that one pool.

---

## Trigger

> **Spread across multiple Spot pools**
> 

→ **diversified**

---

# 22. CAPACITYOPTIMIZED

Capacity Optimized chooses Spot pools based primarily on **availability/capacity risk**.

Think:

> **Need Spot capacity with lower interruption risk.**
> 

This is particularly useful when:

> Getting capacity is more important than simply getting the absolute lowest price.
> 

---

## Trigger

> **Minimize interruption risk**
> 

> **Choose pools with the best available capacity**
> 

→ **capacityOptimized**

---

# 23. PRICECAPACITYOPTIMIZED

This combines:

**price + capacity availability**

It attempts to select pools that provide a good balance between cost and capacity availability.

Think:

> **Best price/capacity trade-off**
> 

---

## Trigger

> **Need cost efficiency AND reduced interruption risk**
> 

→ **priceCapacityOptimized**

---

# 24. SPOT FLEET STRATEGY MEMORY

```
lowestPrice
     ↓
CHEAPEST

diversified
     ↓
DISTRIBUTE

capacityOptimized
     ↓
CAPACITY

priceCapacityOptimized
     ↓
PRICE + CAPACITY
```

---

# 25. SPOT vs RESERVED vs SAVINGS PLANS vs ON-DEMAND

This is the **master purchasing comparison**.

| Model | Commitment | Interruptible? | Flexibility | Best for |
| --- | --- | --- | --- | --- |
| **On-Demand** | None | ❌ | Highest | Unpredictable workloads |
| **Standard RI** | 1/3 years | ❌ | Lower | Stable workload |
| **Convertible RI** | 1/3 years | ❌ | Higher | Stable but evolving |
| **Compute SP** | 1/3 years | ❌ | **Very high** | Flexible compute |
| **EC2 Instance SP** | 1/3 years | ❌ | Medium | Stable EC2 family |
| **Spot** | No traditional long-term commitment | **✅** | Workload-dependent | Interruptible workloads |

---

# 26. THE PURCHASING DECISION TREE

When you see an EC2 pricing question, ask these questions **in order**.

### Question 1:

## Can the workload be interrupted?

### YES

→ **Spot**

### NO

Continue.

---

### Question 2:

## Is the workload predictable for 1–3 years?

### NO

→ **On-Demand**

### YES

Continue.

---

### Question 3:

## Do you need maximum flexibility across compute types?

### YES

→ **Compute Savings Plan**

### NO

Continue.

---

### Question 4:

## Will you stay with one EC2 family in one Region?

### YES

→ **EC2 Instance Savings Plan**

or appropriate RI depending on the scenario.

---

### Question 5:

## Do you know the exact configuration and want a stronger RI discount?

→ **Standard RI**

---

### Question 6:

## Do you need to change the configuration during the commitment?

→ **Convertible RI**

---

# 27. DEDICATED INSTANCES

A **Dedicated Instance** runs on hardware dedicated to a single AWS customer.

The important concept:

> **Physical hardware is dedicated to your AWS account.**
> 

But you do **not** get control over the exact physical host.

---

# 28. DEDICATED HOSTS

A **Dedicated Host** gives you an entire physical server dedicated to your account.

You can see and control:

- the physical host
- how instances are placed on that host
- host-level capacity

This is much more physical-hardware-oriented than Dedicated Instances.

---

# 29. HOST vs INSTANCE DISTINCTION

This is one of the most important comparisons.

|  | Dedicated Instance | Dedicated Host |
| --- | --- | --- |
| Dedicated physical hardware | ✅ | ✅ |
| Entire physical server dedicated | Not necessarily under your control | **Yes** |
| Control over physical host | Limited | **High** |
| Instance placement control | Lower | **Higher** |
| License-related use cases | Some | **Major use case** |
| Physical server visibility | Lower | **Higher** |

---

# 30. DEDICATED HOST — LICENSE SCENARIOS

Dedicated Hosts are particularly important when you have **server-bound software licenses**.

Example:

> A company has existing software licenses that are licensed based on physical sockets or physical cores.
> 

The company wants to use those licenses in AWS.

A Dedicated Host can provide visibility/control over the physical server characteristics needed for licensing compliance.

---

## Exam trigger

> **Bring Your Own License**
> 

> **BYOL**
> 

> **socket-based licensing**
> 

> **core-based licensing**
> 

> **physical server licensing**
> 

→ **Dedicated Host**

---

# 31. DEDICATED INSTANCE — WHEN?

Think Dedicated Instance when the primary requirement is:

> **Dedicated hardware for your account**
> 

but you don't need the same level of physical host control.

---

## Exam trigger

> **Instances must run on hardware dedicated to this customer**
> 

→ **Dedicated Instances**

If the question adds:

> **Need visibility/control over physical server for licensing**
> 

→ **Dedicated Host**

---

# 32. ON-DEMAND CAPACITY RESERVATION

This is where students often confuse **price commitment** with **capacity reservation**.

An **On-Demand Capacity Reservation** reserves EC2 capacity in a specific Availability Zone.

Think:

> **Capacity Reservation = guarantee capacity**
> 

It is **not primarily a discount mechanism**.

---

## Example

A company must launch:

```
20 EC2 instances
```

in:

```
us-east-1a
```

at a specific time.

They cannot risk AWS being unable to provide capacity.

→ **Capacity Reservation**

---

# 33. CAPACITY RESERVATION vs RESERVED INSTANCE

These sound almost identical.

They are NOT.

### Reserved Instance

Primarily:

> **PRICE DISCOUNT**
> 

### Capacity Reservation

Primarily:

> **CAPACITY GUARANTEE**
> 

---

# 34. MASTER DIFFERENCE

```
Reserved Instance
       ↓
"I want a lower price
for predictable usage."

Capacity Reservation
       ↓
"I need capacity available
when I need it."
```

---

# 35. CAPACITY RESERVATION + SAVINGS PLAN

This is an important advanced scenario.

You can combine:

> **Capacity Reservation**
> 

with

> **Savings Plan**
> 

because they solve different problems.

### Capacity Reservation

Guarantees capacity.

### Savings Plan

Provides discounted pricing for eligible compute usage.

Conceptually:

```
Capacity Reservation
        ↓
Capacity guaranteed

Savings Plan
        ↓
Discounted compute price
```

---

# 36. CAPACITY RESERVATION + RI

Similarly, depending on the specific EC2 configuration and eligibility, capacity reservations and pricing commitments can be used together to address:

- **capacity**
- **pricing**

as separate concerns.

The exam-level principle is:

> **A capacity reservation solves availability of EC2 capacity; a pricing commitment solves cost.**
> 

Do not assume that a Reserved Instance by itself guarantees that capacity in an AZ.

---

# 37. CAPACITY RESERVATION DOES NOT MEAN DISCOUNT

This is a major trap.

Question:

> "The company must guarantee EC2 capacity in a particular AZ."
> 

→ **Capacity Reservation**

Question:

> "The company wants to reduce the price of a predictable EC2 workload."
> 

→ **RI / Savings Plan**

---

# 38. RECURRING CAPACITY REQUIREMENTS

Suppose:

> Every evening at 8 PM, a company needs a large amount of EC2 capacity in a specific AZ.
> 

The requirement is:

**predictable recurring capacity**

You should think carefully about:

- capacity planning
- Capacity Reservations
- Savings Plans/RIs for cost
- Auto Scaling for changing demand

The key exam distinction remains:

> **Pricing commitment ≠ capacity reservation.**
> 

---

# 39. COST + AVAILABILITY DECISION TREE

Here is the most useful way to approach the entire topic.

```
                 EC2 workload
                      │
                      ▼
             Can it be interrupted?
                /            \
              YES             NO
               │               │
             SPOT              │
                               ▼
                    Is usage predictable?
                       /             \
                     NO               YES
                     │                 │
                 ON-DEMAND             ▼
                              Need long-term discount?
                                    │
                                    ▼
                              RI / Savings Plan
```

But there is a **second independent question**:

```
Do you need guaranteed
EC2 capacity in a specific AZ?
          │
       YES│
          ▼
Capacity Reservation
```

So:

> **Pricing and capacity are separate axes.**
> 

---

# 40. THE TWO-AXIS MODEL

This is one of the best ways to eliminate confusing answers.

### Axis 1 — COST

```
On-Demand
     ↓
Reserved / Savings Plan
     ↓
Spot
```

### Axis 2 — CAPACITY

```
Normal EC2 capacity
        ↓
Capacity Reservation
```

You can potentially combine the concepts because:

> **One controls price. The other controls capacity.**
> 

---

# 41. EXAM SCENARIO — UNPREDICTABLE WORKLOAD

> A company has unpredictable traffic and cannot commit to a one-year contract.
> 

### Answer:

**On-Demand**

---

# 42. EXAM SCENARIO — STABLE 24/7

> A company runs an EC2 workload continuously for three years and knows the configuration will remain stable.
> 

### Answer:

**Standard Reserved Instance**

---

# 43. EXAM SCENARIO — STABLE BUT CHANGING

> The company expects long-term usage but may change instance configuration during the commitment.
> 

### Answer:

**Convertible RI**

---

# 44. EXAM SCENARIO — FLEXIBLE COMPUTE

> The company wants a long-term discount but may switch EC2 families, use Lambda, or use Fargate.
> 

### Answer:

**Compute Savings Plan**

---

# 45. EXAM SCENARIO — STABLE EC2 FAMILY

> The company will continuously use the same EC2 instance family in a Region but may change instance size.
> 

### Answer:

**EC2 Instance Savings Plan**

---

# 46. EXAM SCENARIO — INTERRUPTIBLE BATCH

> A video transcoding application processes jobs from a queue. Jobs can restart if an EC2 instance disappears.
> 

### Answer:

**Spot**

This is one of the strongest Spot patterns.

---

# 47. EXAM SCENARIO — SPOT INTERRUPTION

> A Spot Instance receives an interruption warning.
> 

The application should:

- checkpoint state
- save progress
- stop accepting new work
- transfer work where possible

It gets approximately:

**2 minutes**

of warning when AWS provides the interruption notice.

---

# 48. EXAM SCENARIO — CANCEL REQUEST

> An administrator cancels a Spot request but wants the currently running Spot Instance to remain.
> 

Cancelling the request does not itself terminate the running instance.

If the goal is to stop the instance:

→ **Terminate the instance separately.**

---

# 49. EXAM SCENARIO — CHEAPEST SPOT

> The requirement is to select the lowest-priced Spot capacity pool.
> 

### Answer:

**lowestPrice**

---

# 50. EXAM SCENARIO — DISTRIBUTE SPOT

> The company wants to distribute Spot instances across several capacity pools.
> 

### Answer:

**diversified**

---

# 51. EXAM SCENARIO — INTERRUPTION RESILIENCE

> The application needs Spot capacity with the lowest likelihood of interruption.
> 

### Answer:

**capacityOptimized**

---

# 52. EXAM SCENARIO — PRICE + CAPACITY

> The company wants a balance between Spot price and capacity availability.
> 

### Answer:

**priceCapacityOptimized**

---

# 53. EXAM SCENARIO — PHYSICAL LICENSE

> A company owns software licensed per physical CPU socket and wants to use those licenses in AWS.
> 

### Answer:

**Dedicated Host**

---

# 54. EXAM SCENARIO — DEDICATED HARDWARE

> The company's compliance policy requires EC2 instances to run on hardware dedicated to that customer.
> 

### Answer:

**Dedicated Instances**

If the question additionally requires:

> physical host visibility/control for licensing
> 

→ **Dedicated Host**

---

# 55. EXAM SCENARIO — GUARANTEED CAPACITY

> A company must ensure that EC2 capacity is available in `us-east-1a` during a critical event.
> 

### Answer:

**On-Demand Capacity Reservation**

Not merely an RI.

---

# 56. THE BIGGEST PURCHASING TRAPS

## Trap 1

**RI = guaranteed capacity**

❌ Not necessarily.

RI is primarily a **pricing commitment**.

---

## Trap 2

**Capacity Reservation = discount**

❌ No.

Capacity Reservation is about **capacity availability**.

---

## Trap 3

**Spot = cheap On-Demand**

❌ No.

Spot can be **interrupted**.

---

## Trap 4

**Cancel Spot Request = terminate instance**

❌ No.

They are separate actions.

---

## Trap 5

**Standard RI = flexible**

❌ Less flexible than Convertible RI.

---

## Trap 6

**Compute Savings Plan = EC2 only**

❌ It can cover eligible compute usage such as EC2, Lambda, and Fargate.

---

## Trap 7

**EC2 Instance Savings Plan = any EC2 anywhere**

❌ It has a narrower commitment scope than Compute Savings Plans.

---

## Trap 8

**Dedicated Instance = Dedicated Host**

❌ Dedicated Host gives much more physical-host visibility/control.

---

## Trap 9

**Dedicated Host = simply cheaper EC2**

❌ Its major exam value is **physical host control / licensing requirements**.

---

# 🔥 MASTER EC2 PURCHASING TABLE

| Trigger phrase | Correct answer |
| --- | --- |
| **Unpredictable workload** | On-Demand |
| **Short-term workload** | On-Demand |
| **No commitment** | On-Demand |
| **Stable 1–3 year workload** | Reserved Instance |
| **Maximum RI discount / stable config** | Standard RI |
| **Need to change RI configuration** | Convertible RI |
| **Flexible long-term compute commitment** | Compute Savings Plan |
| **EC2 family stable in one Region** | EC2 Instance Savings Plan |
| **Interruptible workload** | Spot |
| **Batch processing** | Spot |
| **Can tolerate interruption** | Spot |
| **Two-minute interruption notice** | Spot |
| **Cheapest Spot pool** | lowestPrice |
| **Spread Spot across pools** | diversified |
| **Best capacity availability** | capacityOptimized |
| **Price + capacity balance** | priceCapacityOptimized |
| **Dedicated customer hardware** | Dedicated Instance |
| **Physical host control** | Dedicated Host |
| **BYOL / socket licensing** | Dedicated Host |
| **Guarantee EC2 capacity in AZ** | Capacity Reservation |
| **Lower EC2 price** | RI / Savings Plan |
| **Guarantee capacity + discount** | Capacity Reservation + appropriate pricing commitment |

---

# 🧠 FINAL MEMORY SYSTEM

Remember these six words:

### **ON-DEMAND**

> **Flexible**
> 

### **RI**

> **Predictable**
> 

### **SAVINGS PLAN**

> **Flexible commitment**
> 

### **SPOT**

> **Interruptible**
> 

### **DEDICATED HOST**

> **Physical**
> 

### **CAPACITY RESERVATION**

> **Guaranteed capacity**
> 

---

# 🚨 30-SECOND SAA-C03 PURCHASING SCAN

```
Can tolerate interruption?
        │
       YES
        ↓
      SPOT
        │
       NO
        ↓
Predictable for years?
   │              │
  NO             YES
   │              │
ON-DEMAND    RI / SAVINGS PLAN
                  │
       ┌──────────┼──────────┐
       │          │          │
   Stable     Changeable   Flexible
    exact      config      compute
       │          │          │
 Standard RI  Convertible  Compute SP
                         │
                    EC2 family stable
                         ↓
                  EC2 Instance SP
```

Then independently ask:

```
Need guaranteed capacity
in a specific AZ?
        │
       YES
        ↓
Capacity Reservation
```

And:

```
Physical host / BYOL?
        │
       YES
        ↓
Dedicated Host
```

### Ultimate exam rule

> **Spot = cheapest + interruptible.**
> 
> 
> **RI = predictable + committed pricing.**
> 
> **Savings Plan = committed pricing + more flexibility.**
> 
> **On-Demand = maximum flexibility + no commitment.**
> 
> **Dedicated Host = physical host control/licensing.**
> 
> **Capacity Reservation = guaranteed AZ capacity, not primarily a discount.**
> 

**EC2 Part 4 complete.**
