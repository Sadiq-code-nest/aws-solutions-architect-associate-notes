# On-Demand Capacity Reservations — AWS SAA-C03

## 1. What is an On-Demand Capacity Reservation?

**On-Demand Capacity Reservation = reserve EC2 compute capacity in a specific Availability Zone so that EC2 capacity is guaranteed when you need it.**

Think:

> **“I don't want to reserve a discount. I want to reserve the actual EC2 capacity.”**
> 

It is mainly about **capacity availability**, not cost savings.

You create a capacity reservation specifying things such as:

- Availability Zone
- Instance type
- Number of instances
- Platform/OS
- Tenancy

AWS then reserves that EC2 capacity for you.

---

## 2. The most important distinction

| Feature | Main purpose |
| --- | --- |
| **On-Demand Capacity Reservation** | **Guarantee EC2 capacity** |
| **Reserved Instance** | **Get a billing discount for predictable usage** |
| **Savings Plans** | **Get a billing discount for committed usage** |
| **Spot Instance** | **Get very cheap compute but capacity is interruptible** |
| **Dedicated Host** | **Get an entire physical server for licensing/compliance** |

### Exam memory

> **Capacity Reservation → Capacity**
> 
> 
> **Reserved Instance → Discount**
> 

This distinction is extremely important for SAA-C03.

---

# 3. Why do we need it?

Imagine you have a critical application that must run at **10 PM every night**.

At 10 PM, you launch:

```
20 × EC2 instances
```

But AWS may not necessarily have 20 instances of that exact type available in your required AZ at that exact moment.

Without a Capacity Reservation:

```
10 PM
  ↓
Launch 20 EC2
  ↓
Some capacity unavailable
  ↓
Application may not start completely ❌
```

With a Capacity Reservation:

```
Before 10 PM
  ↓
Reserve 20 EC2 capacity
  ↓
10 PM
  ↓
Launch EC2
  ↓
Capacity guaranteed ✅
```

---

# 4. Very important: Capacity Reservation does NOT automatically mean cheaper

This is a common exam trap.

A Capacity Reservation primarily provides:

**Capacity assurance**

It does **not** inherently provide the discounted pricing associated with Reserved Instances.

For example:

```
Capacity Reservation
        ↓
Guarantees capacity
        ↓
You pay On-Demand rates
```

So don't think:

> "Reserve = discount"
> 

Instead:

> **Capacity Reservation = availability guarantee**
> 

---

# 5. Your financial-bank scenario

Your scenario says:

- Critical financial calculations
- Every night
- 10 PM → 3 AM
- Must not be delayed/interrupted
- Moving to AWS
- Needs capacity in a **specific Availability Zone**
- Workload is predictable

The key phrase is:

> **"reserve compute capacity in a specific Availability Zone"**
> 

That points directly toward:

### **On-Demand Capacity Reservation**

Conceptually:

```
                    AWS
                     │
             Capacity Reservation
                     │
              AZ: us-east-1a
                     │
          ┌──────────┼──────────┐
          ↓          ↓          ↓
        EC2        EC2        EC2
          │          │          │
          └──── Financial ──────┘
                workload
```

The important requirement is **guaranteed capacity**, not merely a discount.

---

# 6. But the workload runs only 10 PM–3 AM. What about that?

This is where the scenario becomes interesting.

Capacity Reservations can be configured with a **specific time window / recurring schedule**, depending on the Capacity Reservation configuration available for the workload.

Conceptually:

```
Every day

10 PM ───────────────── 3 AM
      Capacity needed
          ↓
     EC2 capacity
       RESERVED
```

This is useful for workloads where:

- compute is needed at predictable times
- capacity must be available
- the workload is critical
- you don't want to risk EC2 capacity shortages

---

# 7. Capacity Reservation vs Reserved Instance

This is probably the **#1 confusion**.

|  | **Capacity Reservation** | **Reserved Instance** |
| --- | --- | --- |
| Primary purpose | Guarantee capacity | Reduce EC2 cost |
| Guarantees capacity? | ✅ Yes | ❌ Not by itself |
| Specific AZ | ✅ Yes | Depends on RI type |
| Billing discount | ❌ Not the primary benefit | ✅ Yes |
| Best for | Critical capacity requirement | Predictable long-term workload |
| Example | "I must get 20 EC2 instances in AZ-A" | "I will run this EC2 workload for 1–3 years" |
| Exam keyword | **capacity must always be available** | **reduce cost for predictable usage** |

### Easy memory:

> **RI = “Give me a cheaper price.”**
> 

> **Capacity Reservation = “Give me the capacity.”**
> 

---

# 8. Capacity Reservation vs Regional Reserved Instance

Your question specifically contains this distractor:

> "Use Regional Reserved Instances to reserve capacity on a specific Availability Zone..."
> 

That's the trap.

A **Regional Reserved Instance** provides pricing benefits across the Region, but it does **not reserve physical EC2 capacity in a specific AZ**.

So:

```
Regional RI
     ↓
Billing discount
     ↓
Not a specific-AZ capacity guarantee
```

Whereas:

```
Capacity Reservation
     ↓
Specific AZ
     ↓
Actual EC2 capacity reserved
```

---

# 9. Capacity Reservation vs Dedicated Host

Another distractor.

### Dedicated Host

You get:

```
Dedicated physical EC2 server
```

Useful when you need:

- Dedicated physical hardware
- Certain licensing requirements
- Compliance requirements
- Bring-your-own-license scenarios

Example:

> A company has Oracle software licensed based on physical CPU sockets and needs a dedicated physical server.
> 

That's a **Dedicated Host** problem.

It is **not** simply an "I need EC2 capacity available at 10 PM" problem.

---

# 10. Capacity Reservation vs Spot

Very different.

### Spot

```
Cheap
  ↓
AWS can reclaim capacity
  ↓
Workload can be interrupted
```

### Capacity Reservation

```
Pay On-Demand pricing
  ↓
Capacity reserved
  ↓
Good for critical workloads
```

Therefore:

> **Mission-critical + cannot tolerate interruption → Capacity Reservation**
> 

> **Flexible + interruption-tolerant + cost-sensitive → Spot**
> 

---

# 11. Capacity Reservation vs On-Demand

Normal On-Demand:

```
Need instance
    ↓
Launch
    ↓
AWS attempts to provide capacity
```

Capacity Reservation:

```
Before workload
       ↓
Reserve capacity
       ↓
Workload starts
       ↓
Capacity is available
```

Both use On-Demand-style pricing, but the key difference is **capacity assurance**.

---

# 12. Typical scenarios

### Scenario 1 — Financial processing ⭐⭐⭐⭐⭐

A bank runs a critical batch process every night.

```
10 PM → 3 AM
```

It requires:

```
20 × specific EC2 instance
Specific AZ
```

The job cannot be delayed.

➡️ **On-Demand Capacity Reservation**

---

### Scenario 2 — Healthcare application

A hospital must launch a specific number of EC2 instances during a predictable emergency-processing window.

Capacity availability is critical.

➡️ **Capacity Reservation**

---

### Scenario 3 — Disaster recovery

You need to ensure that enough EC2 capacity is available in a particular AZ for a critical DR workload.

➡️ **Capacity Reservation can be appropriate**

---

### Scenario 4 — Long-running predictable workload

A company knows:

> "We will run these EC2 instances continuously for the next 3 years."
> 

The primary goal is:

> **Reduce cost.**
> 

➡️ **Reserved Instance / Savings Plan**

Not Capacity Reservation alone.

---

### Scenario 5 — Flexible batch processing

A company processes millions of images, but jobs can be interrupted and restarted.

Primary goal:

> **Lowest possible compute cost.**
> 

➡️ **Spot Instances**

---

# 13. SAA-C03 keyword map

| If question says... | Think... |
| --- | --- |
| **Guarantee EC2 capacity** | 🟢 Capacity Reservation |
| **Specific Availability Zone capacity** | 🟢 Capacity Reservation |
| **Capacity must always be available** | 🟢 Capacity Reservation |
| **Critical workload cannot be interrupted because capacity may be unavailable** | 🟢 Capacity Reservation |
| **Predictable workload + reduce cost** | 🔵 Reserved Instance / Savings Plans |
| **Long-term commitment + discount** | 🔵 Reserved Instance / Savings Plans |
| **Cheapest EC2 + can tolerate interruption** | 🟡 Spot |
| **Physical server** | 🟣 Dedicated Host |
| **BYOL based on physical cores/sockets** | 🟣 Dedicated Host |

---

# 14. One master picture

```
                    EC2 Pricing / Capacity Choices
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
       Capacity            Discount            Cheapest
       Guarantee           needed               compute
          │                   │                   │
          ▼                   ▼                   ▼
 Capacity Reservation    RI / Savings Plan       Spot
          │
          │
          ▼
 Specific AZ
 Actual EC2
 capacity reserved
```

## ⭐ Exam-night memory

> **On-Demand Capacity Reservation = "I need the EC2 capacity to be there."**
> 

> **Reserved Instance = "I know I'll use EC2 for a long time and want a discount."**
> 

> **Spot = "I want the cheapest compute and can tolerate interruption."**
> 

> **Dedicated Host = "I need the physical server."**
> 

### For your exact bank question

**Critical + scheduled workload + specific AZ + capacity must be guaranteed → On-Demand Capacity Reservation.**
