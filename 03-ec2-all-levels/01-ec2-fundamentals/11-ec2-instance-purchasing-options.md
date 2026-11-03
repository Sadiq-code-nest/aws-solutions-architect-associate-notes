# EC2 Instance Purchasing Options

> **On-Demand = Pay normally**
> 
> 
> **Reserved = Commit to save**
> 
> **Convertible RI = Commit + change configuration**
> 
> **Savings Plan = Commit to usage/spend**
> 
> **Spot = Cheapest + can disappear**
> 
> **Dedicated Host = Entire physical server**
> 
> **Dedicated Instance = Dedicated hardware for account**
> 
> **Capacity Reservation = Guarantee capacity in an AZ**
> 

## ⭐ SAA-C03 Master Memory Trick

---

```

```

### Dedicated Host vs Dedicated Instance

---

You can also combine certain billing discounts with Capacity Reservations.

```

```

### Reserved Instance vs Capacity Reservation

---

```

```

### Reserved Instance vs Savings Plan

---

```

```

### Spot vs On-Demand

# 🚨 Important SAA-C03 Distinctions

---

```

```

or:

```

```

Then ask:

```

```

When you see an EC2 pricing question, think:

# 🎯 SAA-C03 Decision Tree

---

**Capacity guarantee, not discount.**

> "Reserve this room for me in this particular hotel location. I'll pay even if I don't stay."
> 

### 🟠 Capacity Reservation

---

**Entire physical server.**

> "I want the entire hotel building for my company."
> 

### 🏢 Dedicated Host

---

**Cheap + interruptible.**

> "I'll take an empty room for a very cheap price, but you can kick me out if the hotel needs it."
> 

### 🔴 Spot

---

**Commit to usage/spend rather than a specific configuration.**

> "I'll spend $10/hour at this hotel for 3 years, but I want flexibility in which eligible room I use."
> 

### 🟣 Savings Plan

---

**Long commitment + flexibility.**

> "I'll stay for 1–3 years, but I may want to change my room type later."
> 

### 🔄 Convertible Reserved

---

**Long commitment.**

> "I'll stay here for 1–3 years. Give me a discount."
> 

### 🟡 Reserved

---

**No commitment.**

> "I'll stay tonight. I'll pay the normal price."
> 

### 🟢 On-Demand

Imagine AWS EC2 is a hotel.

# 🧠 The Hotel Analogy

---

| Option | Commitment | Discount | Can be interrupted? | Main purpose |
| --- | --- | --- | --- | --- |
| **On-Demand** | None | ❌ | No | Flexible/unpredictable |
| **Reserved Instance** | 1/3 years | ✅ | No | Steady-state workload |
| **Convertible RI** | 1/3 years | ✅ | No | Steady + configuration flexibility |
| **Savings Plan** | 1/3 years | ✅ | No | Commit to compute usage |
| **Spot** | None | 💰💰💰 | **YES** | Fault-tolerant workloads |
| **Dedicated Host** | On-Demand / 1/3 years | Possible | No | Dedicated physical server / BYOL |
| **Dedicated Instance** | No RI-style commitment required | — | No | Hardware dedicated to account |
| **Capacity Reservation** | None | ❌ | No | Guarantee capacity in specific AZ |

# 🔥 Most Important Comparison

---

👉 **Capacity Reservation**

> **"Guarantee EC2 capacity in a specific AZ"**
> 

### 🧠 Exam clue

```

```

Suppose you have a critical application that **must launch in a specific AZ**, and you're worried that EC2 capacity might not be available when you need it.

### Why use it?

You pay the **On-Demand rate** whether you are actually running the instances or not.

### Capacity Reservation ≠ discount

The important thing:

```

```

Example:

> **Reserve EC2 capacity in a specific Availability Zone.**
> 

Capacity Reservation means:

### What?

This one is commonly confused with Reserved Instances.

# 8. 🟠 Capacity Reservations

---

👉 **Dedicated Instance**

> **"Hardware dedicated to my account, but no need to control physical host"**
> 

### 🧠 Exam clue

> Hardware is dedicated to your account, but you don't control the physical host placement.
> 

**Dedicated Instance**

> You control/book the physical server.
> 

**Dedicated Host**

### Key difference

You may have multiple instances on dedicated hardware, and you don't control exactly where those instances are placed.

```

```

However, you don't get the same level of physical host control as with Dedicated Hosts.

Your EC2 instances run on **hardware dedicated to your AWS account**.

### What?

# 7. 🔵 Dedicated Instances

---

👉 **Dedicated Host**

> **"Physical server visibility/control"**
> 

👉 **Dedicated Host**

> **"Existing software license based on physical cores/sockets"**
> 

### 🧠 Exam clue

Also useful for certain **compliance/regulatory requirements**.

Dedicated Hosts can help with those licensing requirements.

- 
- 
- 

Some software licenses depend on:

### **BYOL — Bring Your Own License**

Most importantly:

### Why use it?

You don't share that physical host with other AWS customers.

```

```

AWS gives you an **entire physical server dedicated to your use**.

### What?

# 6. 🏢 Dedicated Hosts

---

👉 **Spot**

> **"Lowest possible EC2 cost"**
> 

> **"Flexible start/end time"**
> 

> **"Can tolerate interruption"**
> 

### 🧠 Exam clue

❌ Workload that must never be interrupted

❌ Critical database

### Bad workloads

- 
- 
- 
- 
- 
- 

### Good workloads

```

```

You need to process 1 million images:

### Example

So you should only use Spot for workloads that can tolerate interruption.

> ⚠️ **AWS can interrupt the instance when the capacity is needed back.**
> 

But:

AWS has unused EC2 capacity and offers it at a **very large discount**.

### What?

# 5. 🔴 Spot Instances

---

👉 **Savings Plans**

> **"Commit to $X/hour for 1 or 3 years"**
> 

### Exam clue

> **Savings Plan = commit to spending/usage**
> 

> **Reserved Instance = reserve a configuration**
> 

### 🧠 Simple idea

Depending on the Savings Plan type, there is also flexibility around instance family/region.

- 
- 
- 

Savings Plans can provide flexibility across things such as:

### Why is it flexible?

```

```

Example:

You commit to a **certain amount of compute usage per hour** for **1 or 3 years**.

### What?

This is one of the most important options to distinguish from Reserved Instances.

# 4. 🟣 Savings Plans

---

👉 **Convertible RI**

> **Long-term commitment + need flexibility to change instance attributes**
> 

### 🧠 Exam clue

> You want a long-term discount but aren't sure exactly which instance configuration you'll need later.
> 

### Best for

- 
- 
- 
- 

You can change things such as:

```

```

For example:

You still make a **long-term commitment**, but you have more flexibility to change the instance configuration.

### Main idea

This is a special type of Reserved Instance.

# 3. 🔄 Convertible Reserved Instance

---

👉 **Reserved Instance**

> **Predictable + steady workload + 1/3 year commitment**
> 

### 🧠 Exam clue

> **More upfront payment → greater discount**
> 

Generally:

- 
- 
- 

### Payment options

```

```

Example:

**Steady-state workloads** that run continuously.

### Best for

in exchange for a significant discount compared with On-Demand.

**1 year or 3 years**

You commit to using a specific EC2 configuration for:

### What?

# 2. 🟡 Reserved Instances (RI)

---

👉 **On-Demand**

> **No commitment + unpredictable/short-term workload**
> 

### 🧠 Exam clue

```

```

A company launches a new application and doesn't know its future traffic:

### Example

- 
- 
- 
- 

### Best for

```

```

Pay for EC2 **as you use it**, with **no long-term commitment**.

### What?

## 1. 🟢 On-Demand Instances

---

1. 
2. 

The easiest way to understand EC2 purchasing options is to ask **two questions**:

# EC2 Purchasing Options — AWS SAA-C03
