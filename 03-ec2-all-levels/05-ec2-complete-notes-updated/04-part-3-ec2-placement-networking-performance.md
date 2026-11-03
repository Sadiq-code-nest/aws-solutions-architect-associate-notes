# EC2 — PART 3: PLACEMENT GROUPS + NETWORKING PERFORMANCE

This part is about **where EC2 instances are physically placed** and **how they communicate at very high speed / very low latency**.

The core exam idea is:

> **Placement Groups control physical placement. ENA/EFA control networking performance.**
> 

---

# 1. PLACEMENT GROUPS

A **Placement Group** is a logical grouping that lets you influence how EC2 instances are placed on the underlying AWS infrastructure.

There are **3 placement strategies** you must know:

| Placement Group | Main Goal | Think |
| --- | --- | --- |
| **Cluster** | Maximum network performance | 🚀 Together |
| **Spread** | Maximum hardware isolation | 🛡️ Apart |
| **Partition** | Isolation by partitions/racks | 📦 Groups |

The fastest memory trick:

> **Cluster = Close**
> 

> **Spread = Separate**
> 

> **Partition = Separate in Groups**
> 

---

# 2. CLUSTER PLACEMENT GROUP

## What is it?

A **Cluster Placement Group** places instances close together within a **single Availability Zone**.

The goal is:

- very low network latency
- very high network throughput
- high-speed communication between instances

Conceptually:

```
                 ONE AZ
┌─────────────────────────────────────┐
│                                     │
│   EC2 ─── EC2 ─── EC2 ─── EC2      │
│      \      |       /               │
│       \     |      /                │
│        Very close together          │
│                                     │
└─────────────────────────────────────┘
```

---

## Why use Cluster?

When instances communicate with each other **constantly and heavily**.

Typical workloads:

- HPC
- scientific computing
- tightly coupled applications
- high-performance analytics
- distributed computing requiring very low latency

---

## Exam trigger phrases

If you see:

> **low network latency**
> 

> **high network throughput between EC2 instances**
> 

> **HPC**
> 

> **tightly coupled workloads**
> 

> **instances need to communicate extremely quickly**
> 

→ **Cluster Placement Group**

---

# 3. CLUSTER — BIG EXAM LIMITATION

Cluster Placement Groups are **AZ-specific**.

You cannot create one cluster placement group spanning multiple AZs.

Example:

```
Cluster Placement Group

        us-east-1a
             │
      ┌──────┴──────┐
      EC2 EC2 EC2 EC2
```

Not:

```
us-east-1a ─── EC2
              │
              ❌
              │
us-east-1b ─── EC2
```

---

## Why?

The whole point is putting instances **physically close together**.

Cross-AZ placement would defeat that purpose.

---

# 4. CLUSTER FAILURE IMPLICATION

This is an important trade-off.

Because the instances are concentrated within one AZ:

> **An AZ failure can affect the entire cluster workload.**
> 

Therefore:

### Cluster optimizes:

**Performance**

not

**Maximum availability**

---

## Exam trap

Question:

> An HPC application requires extremely low network latency between EC2 instances.
> 

→ Cluster.

But if the question says:

> The application must survive an AZ failure.
> 

Cluster alone is not the answer.

You need an architecture designed for multi-AZ resilience.

---

# 5. SPREAD PLACEMENT GROUP

Spread is basically the opposite philosophy.

Instead of putting instances close together:

> **Spread deliberately separates instances across distinct underlying hardware.**
> 

Conceptually:

```
AZ
│
├── Hardware A → EC2
│
├── Hardware B → EC2
│
├── Hardware C → EC2
│
└── Hardware D → EC2
```

The purpose is:

> **Reduce the chance that a single hardware failure affects multiple instances.**
> 

---

# 6. WHY USE SPREAD?

Use Spread when:

- each instance is critical
- hardware failure must affect as few instances as possible
- you want strong instance isolation
- you have a small number of important instances

Typical scenario:

```
EC2 #1 → critical controller
EC2 #2 → critical controller
EC2 #3 → critical controller
```

You don't want all three sitting on the same underlying hardware.

---

## Exam trigger

> **critical instances**
> 

> **hardware failure isolation**
> 

> **each instance should be on separate hardware**
> 

> **minimize correlated failures**
> 

→ **Spread Placement Group**

---

# 7. SPREAD AND AZs

Spread placement groups can span **multiple AZs**.

That makes Spread fundamentally different from Cluster.

Example:

```
             Spread PG

       AZ-a              AZ-b
        │                  │
     EC2 A              EC2 C
        │                  │
     EC2 B              EC2 D
```

Instances are distributed across distinct underlying hardware.

---

# 8. SPREAD — IMPORTANT LIMITATION

Spread placement groups are intended for a **small number of critical instances**.

For SAA-C03, remember the classic course constraint:

> **Maximum 7 running instances per AZ per spread placement group.**
> 

This limitation is frequently useful for eliminating answers.

---

# 9. PARTITION PLACEMENT GROUP

Partition placement groups are the middle ground.

They divide instances into **logical partitions**.

Each partition corresponds to a separate set of underlying hardware/racks.

Conceptually:

```
                 ONE AZ

┌─────────────────────────────────────┐
│ Partition 1                         │
│ EC2 EC2 EC2                         │
├─────────────────────────────────────┤
│ Partition 2                         │
│ EC2 EC2 EC2                         │
├─────────────────────────────────────┤
│ Partition 3                         │
│ EC2 EC2 EC2                         │
└─────────────────────────────────────┘
```

A hardware failure affecting one partition should primarily affect the instances in that partition.

---

# 10. WHY PARTITION?

Partition is designed for **large distributed systems** that need awareness of failure boundaries.

Classic examples:

- Apache Kafka
- Apache Cassandra
- Hadoop / HDFS
- HBase

These systems distribute data across multiple nodes.

You don't necessarily need every instance completely isolated from every other instance.

Instead:

> **You want groups of instances isolated from other groups.**
> 

---

# 11. PARTITION FAILURE MODEL

Imagine:

```
Partition 1 → EC2 EC2 EC2
Partition 2 → EC2 EC2 EC2
Partition 3 → EC2 EC2 EC2
```

If hardware associated with Partition 2 fails:

```
Partition 1 → ✅
Partition 2 → ❌
Partition 3 → ✅
```

The failure is contained primarily within that partition.

---

# 12. PARTITION PLACEMENT GROUP AND AZs

Partition placement groups can span multiple AZs.

Within each AZ, the instances are organized into partitions.

This gives distributed systems more control over failure isolation than simply placing everything together.

---

# 13. CLUSTER vs SPREAD vs PARTITION

This is **one of the highest-value EC2 comparison tables**.

| Feature | Cluster | Spread | Partition |
| --- | --- | --- | --- |
| Primary goal | Performance | Isolation | Distributed-system isolation |
| Physical placement | Close together | Separate hardware | Separate partitions |
| Network latency | **Lowest** | Normal | Normal |
| Network throughput | **Highest** | Normal | High/normal |
| Hardware isolation | Low | **Highest** | High |
| AZ scope | **Single AZ** | Can span AZs | Can span AZs |
| Best for | HPC | Critical instances | Kafka/Cassandra/HDFS |
| Failure impact | Potentially entire AZ/cluster | Minimized per instance | Usually partition-level |
| Typical scale | High-performance cluster | Small number | Large distributed systems |
| Key word | **Performance** | **Isolation** | **Partitions** |

---

# 14. THE EASIEST MEMORY TRICK

Imagine a classroom.

### Cluster

Everyone sits together:

```
👤👤👤👤
```

→ Easy to communicate.

**PERFORMANCE**

---

### Spread

Everyone sits at different tables:

```
👤     👤     👤     👤
```

→ One table failure doesn't affect everyone.

**ISOLATION**

---

### Partition

Students sit in separate groups:

```
👤👤 | 👤👤 | 👤👤
```

→ Failure of one group doesn't take down every group.

**DISTRIBUTED SYSTEMS**

---

# 15. HPC SCENARIOS

HPC = **High Performance Computing**.

Think:

- scientific simulations
- weather modeling
- computational fluid dynamics
- molecular modeling
- financial simulations
- engineering calculations

These workloads may have hundreds/thousands of compute operations that communicate frequently.

---

## Scenario

> A company runs computational fluid dynamics simulations. EC2 instances exchange large amounts of data and require extremely low network latency.
> 

### Correct:

**Cluster Placement Group**

Because:

```
HPC
+
High network throughput
+
Low latency
=
CLUSTER
```

---

# 16. HPC ACROSS MULTIPLE AZs

This is a classic trick.

Suppose:

> An HPC workload currently spans multiple AZs and has poor network performance. The company wants to minimize latency between compute nodes.
> 

You should recognize:

**Cluster Placement Group requires a single AZ.**

Therefore:

> Put the tightly coupled HPC instances in the same AZ and use a Cluster Placement Group.
> 

---

## Important distinction

Multi-AZ is generally excellent for:

**availability**

But HPC communication may prioritize:

**latency**

You must identify what the question is optimizing.

---

# 17. KAFKA SCENARIOS

Kafka is a classic **Partition Placement Group** scenario.

Why?

Kafka distributes data across brokers and partitions.

You want infrastructure failure isolation so that a hardware problem doesn't take down every broker.

Conceptually:

```
Partition 1 → Kafka brokers
Partition 2 → Kafka brokers
Partition 3 → Kafka brokers
```

Therefore:

> **Kafka + failure isolation → Partition Placement Group**
> 

---

# 18. CASSANDRA SCENARIOS

Cassandra is another classic Partition Placement Group workload.

Cassandra distributes data across nodes.

You want the nodes distributed across different infrastructure failure domains.

Therefore:

> **Cassandra + large distributed cluster + rack/hardware awareness**
> 

→ **Partition Placement Group**

---

# 19. HDFS SCENARIOS

Hadoop/HDFS is also a classic.

HDFS distributes large datasets across many nodes.

If several nodes share the same underlying failure domain, one infrastructure failure could affect a large amount of data.

Partition placement provides additional failure isolation.

Therefore:

> **HDFS + distributed storage cluster → Partition**
> 

---

# 20. WHY NOT CLUSTER FOR KAFKA?

This is a common exam trap.

Kafka needs:

- distributed brokers
- fault isolation
- multiple failure domains

Cluster puts instances together for performance.

Partition separates them into failure domains.

Therefore:

> **Kafka → Partition**
> 

not automatically Cluster.

---

# 21. WHY NOT SPREAD FOR KAFKA?

Spread gives strong isolation, but it is designed around **individual instance separation** and has a small-instance limitation.

Large distributed systems such as Kafka may have many instances.

Partition is designed specifically for:

> **large distributed workloads requiring partition-level isolation.**
> 

So:

```
Few critical instances
→ Spread

Many distributed instances
→ Partition
```

---

# 22. AZ FAILURE IMPLICATIONS

You need to understand the difference between:

### Cluster

```
ONE AZ
   ↓
AZ failure
   ↓
Potentially entire cluster affected
```

### Spread

Can span AZs:

```
AZ-A → EC2
AZ-B → EC2
AZ-C → EC2
```

This can reduce correlated failures.

### Partition

Can also span AZs and creates multiple failure partitions.

---

## Exam decision

If the question emphasizes:

> **AZ failure resilience**
> 

→ Don't blindly choose Cluster.

If it emphasizes:

> **lowest possible latency**
> 

→ Cluster.

If it emphasizes:

> **hardware failure isolation**
> 

→ Spread.

If it emphasizes:

> **large distributed application + partition/rack awareness**
> 

→ Partition.

---

# 23. ENHANCED NETWORKING

Now we're moving from:

**Where are instances placed?**

to:

**How fast can they communicate?**

Enhanced Networking provides higher network performance using modern virtualization/network interfaces.

Benefits include:

- higher bandwidth
- lower latency
- lower CPU overhead
- improved packet-per-second performance

---

# 24. ENA

**ENA = Elastic Network Adapter**

ENA is the primary modern mechanism behind EC2 Enhanced Networking.

Think:

> **ENA = high-performance virtual network adapter**
> 

Normal networking:

```
EC2
 ↓
standard networking
```

Enhanced Networking:

```
EC2
 ↓
ENA
 ↓
high-performance network
```

---

# 25. WHEN IS ENA IMPORTANT?

When the application needs:

- high network bandwidth
- high packets per second
- lower network latency
- demanding network workloads

Examples:

- HPC networking
- high-performance web services
- network-intensive applications
- distributed computing

---

## Exam trigger

> **high network throughput**
> 

> **high packet-per-second performance**
> 

> **low network latency**
> 

→ **Enhanced Networking / ENA**

---

# 26. EFA

**EFA = Elastic Fabric Adapter**

EFA is designed for **high-performance computing and tightly coupled workloads**.

Think:

> **ENA = fast general networking**
> 

> **EFA = HPC super-networking**
> 

EFA provides very low-latency communication and is designed for workloads where compute nodes communicate heavily.

---

# 27. EFA USE CASES

Typical:

- HPC
- machine learning training
- tightly coupled distributed workloads
- scientific computing

For example:

```
EC2 ───── EFA ───── EC2
 │                    │
 └──── very low ──────┘
       latency
```

---

# 28. ENA vs EFA

|  | ENA | EFA |
| --- | --- | --- |
| Full name | Elastic Network Adapter | Elastic Fabric Adapter |
| Purpose | Enhanced general networking | HPC / tightly coupled workloads |
| High bandwidth | ✅ | ✅ |
| Low latency | ✅ | **Extremely important** |
| HPC | ✅ | **Excellent** |
| ML training | Possible | Excellent |
| General EC2 networking | **Yes** | More specialized |
| Memory | **Fast network adapter** | **HPC fabric** |

### Memory trick

> **ENA = Enhanced Network**
> 

> **EFA = Extreme/Elastic Fabric for HPC**
> 

---

# 29. ENHANCED NETWORKING vs EBS-OPTIMIZED

This distinction is extremely important because exam questions intentionally mix them.

## Enhanced Networking

Improves:

```
EC2 ↔ EC2
EC2 ↔ network
```

Think:

> **NETWORK**
> 

---

## EBS-optimized

Improves:

```
EC2 ↔ EBS
```

Think:

> **STORAGE**
> 

---

### Comparison

| Requirement | Correct |
| --- | --- |
| EC2 needs higher network bandwidth | **Enhanced Networking / ENA** |
| EC2 needs lower network latency | **Enhanced Networking / ENA** |
| HPC nodes communicate rapidly | **ENA/EFA + appropriate placement** |
| EC2 needs dedicated EBS bandwidth | **EBS-optimized** |
| EBS storage throughput is bottleneck | **EBS-optimized** |
| Upload to S3 is slow | **Don't automatically choose EBS-optimized** |

---

# 30. HIGH NETWORK THROUGHPUT SCENARIOS

Consider:

> An application transfers enormous amounts of data between EC2 instances.
> 

The question says:

**high network throughput**

not:

**high disk throughput**.

Therefore:

→ **Enhanced Networking / ENA**

If the instances also need extremely low latency:

→ Consider **Cluster Placement Group**.

If the workload is HPC/tightly coupled:

→ **EFA** may be appropriate.

---

# 31. LOW-LATENCY SCENARIOS

There are actually **two different latency questions** you must distinguish.

### Network latency

```
EC2 ↔ EC2
```

→ Enhanced Networking / ENA/EFA

And potentially:

→ Cluster Placement Group

---

### Storage latency

```
EC2 ↔ EBS
```

→ Appropriate EBS volume, such as io2 for demanding low-latency/high-IOPS workloads.

And potentially:

→ EBS-optimized

---

## Exam trap

Question:

> EC2 instances need very low latency communication with each other.
> 

→ **Cluster + Enhanced Networking/EFA as appropriate**

Question:

> EC2 needs very low latency disk I/O.
> 

→ **EBS/io2 + EBS-optimized as appropriate**

Don't mix the two.

---

# 32. THE FULL HPC STACK

This is worth remembering.

A serious HPC architecture may combine:

```
             HPC Cluster
                  │
        Cluster Placement Group
                  │
        ┌─────────┴─────────┐
        │                   │
       EC2                 EC2
        │                   │
       EFA                 EFA
        │                   │
        └──── low-latency ──┘
             networking
```

So don't think:

> "HPC = one service."
> 

Instead:

**HPC → Cluster Placement Group + Enhanced Networking/EFA**

depending on the exact question.

---

# 33. COMMON EXAM SCENARIOS

## Scenario A

> EC2 instances need maximum network throughput and minimum latency.
> 

### Answer:

**Cluster Placement Group**

Potentially with **Enhanced Networking**.

### Trigger:

**MAXIMUM EC2-TO-EC2 PERFORMANCE**

---

# Scenario B

> A company has several mission-critical EC2 instances. A hardware failure should affect as few instances as possible.
> 

### Answer:

**Spread Placement Group**

### Trigger:

**HARDWARE ISOLATION**

---

# Scenario C

> A company runs a large Kafka cluster and wants infrastructure failure isolation.
> 

### Answer:

**Partition Placement Group**

### Trigger:

**KAFKA + LARGE DISTRIBUTED CLUSTER**

---

# Scenario D

> Cassandra nodes should be distributed across separate failure domains.
> 

### Answer:

**Partition Placement Group**

---

# Scenario E

> HDFS cluster should reduce the impact of a rack-level infrastructure failure.
> 

### Answer:

**Partition Placement Group**

---

# Scenario F

> CFD simulation requires extremely low latency communication among EC2 instances.
> 

### Answer:

**Cluster Placement Group**

Potentially **EFA** for supported HPC architecture.

---

# Scenario G

> EC2 instances need higher network bandwidth and packet processing.
> 

### Answer:

**Enhanced Networking / ENA**

---

# Scenario H

> HPC workload requires extremely low-latency node-to-node communication.
> 

### Answer:

**EFA**

Potentially combined with:

**Cluster Placement Group**

---

# Scenario I

> EC2 instances have poor EBS performance and require dedicated bandwidth between EC2 and EBS.
> 

### Answer:

**EBS-optimized**

Not ENA.

---

# Scenario J

> EC2 instances have poor network performance when communicating with each other.
> 

### Answer:

**Enhanced Networking**

Not EBS-optimized.

---

# 🔥 MASTER PLACEMENT GROUP TABLE

| Trigger phrase | Correct pick |
| --- | --- |
| **Maximum network performance** | **Cluster** |
| **Lowest EC2-to-EC2 latency** | **Cluster** |
| **HPC** | **Cluster** |
| **Tightly coupled compute** | **Cluster** |
| **Critical instances isolated from hardware failure** | **Spread** |
| **Each instance on separate hardware** | **Spread** |
| **Small number of critical instances** | **Spread** |
| **Kafka** | **Partition** |
| **Cassandra** | **Partition** |
| **HDFS** | **Partition** |
| **HBase** | **Partition** |
| **Large distributed system** | **Partition** |
| **Rack/partition failure isolation** | **Partition** |
| **High network bandwidth** | **ENA / Enhanced Networking** |
| **High packet-per-second performance** | **ENA** |
| **HPC fabric / ultra-low latency** | **EFA** |
| **EC2 ↔ EBS bandwidth** | **EBS-optimized** |

---

# 🧠 FINAL CONFUSION-BUSTER

When you see a placement-group question, ask:

### Question 1

**Do they want instances CLOSE?**

→ **Cluster**

### Question 2

**Do they want instances SEPARATE?**

→ **Spread**

### Question 3

**Do they want LARGE DISTRIBUTED GROUPS separated into failure partitions?**

→ **Partition**

Then ask:

### Question 4

**Is the problem network performance?**

→ **ENA / Enhanced Networking**

### Question 5

**Is it HPC/tightly coupled and ultra-low latency?**

→ **EFA**

### Question 6

**Is the problem EC2 ↔ EBS storage bandwidth?**

→ **EBS-optimized**

---

# 🚨 15-SECOND EXAM MEMORY

```

```

### The ultimate rule

> **Cluster = Performance.**
> 
> 
> **Spread = Individual isolation.**
> 
> **Partition = Distributed-system isolation.**
> 
> **ENA = Network performance.**
> 
> **EFA = HPC network performance.**
> 
> **EBS-optimized = Storage performance.**
> 

**EC2 Part 3 complete.**
