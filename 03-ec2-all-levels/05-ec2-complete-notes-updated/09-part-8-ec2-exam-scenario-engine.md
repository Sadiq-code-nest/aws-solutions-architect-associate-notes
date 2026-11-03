# EC2 Part 8 — Exam Scenario Engine

This is the **decision-making layer** for EC2.

The goal is not to memorize service definitions. The goal is:

> **Read the scenario → identify the trigger phrase → immediately map it to the correct EC2 feature.**
> 

For the SAA-C03 exam, this is often faster and more reliable than trying to reason from every answer choice.

---

# 1. EC2 Master Trigger Engine ⭐⭐⭐⭐⭐

| Scenario / Trigger phrase | Correct pick | Why |
| --- | --- | --- |
| **Interruptible batch workload** | **Spot Instances** | Cheapest compute when interruption is acceptable |
| **Steady predictable workload** | **Reserved Instances / Savings Plans** | Long-term commitment lowers cost |
| **Critical workload requiring capacity in a specific AZ** | **On-Demand Capacity Reservation** | Guarantees capacity in that AZ |
| **Existing software license tied to physical sockets/cores** | **Dedicated Host** | Physical host visibility/control for BYOL |
| **Physical tenancy isolation** | **Dedicated Instance / Dedicated Host** | Dedicated hardware |
| **Low-latency HPC** | **Cluster Placement Group** | Closely places instances for high bandwidth/low latency |
| **Maximum hardware isolation** | **Spread Placement Group** | Instances placed on separate underlying hardware |
| **Kafka / Cassandra / HDFS** | **Partition Placement Group** | Separates distributed nodes into partitions |
| **High sequential local storage** | **Storage Optimized** | High local storage performance |
| **CPU-heavy workload** | **Compute Optimized** | High CPU capability |
| **Memory-heavy workload** | **Memory Optimized** | Large RAM capacity |
| **GPU workload** | **Accelerated Computing** | GPU hardware |
| **Occasional CPU spikes** | **T-series** | Burstable performance |
| **Very fast restart while preserving RAM state** | **Hibernation** | Saves RAM state to root EBS |
| **Persistent block storage** | **EBS** | Persistent network-attached block storage |
| **Temporary ultra-fast local storage** | **Instance Store** | Local ephemeral storage |
| **Independent IOPS/throughput control** | **gp3** | IOPS and throughput configurable independently of volume size |
| **Very high sustained IOPS** | **io2 / io1** | Provisioned IOPS SSD |
| **Large sequential throughput** | **st1** | Throughput-optimized HDD |
| **Lowest-cost infrequent HDD** | **sc1** | Cold HDD |
| **Extreme network performance** | **Enhanced Networking** | ENA/high packet-per-second/bandwidth |
| **Automatic EC2 configuration at launch** | **User Data** | Bootstrapping |
| **Reusable preconfigured server** | **AMI** | Captures OS/software/configuration |

---

# 2. Pricing Decision Engine ⭐⭐⭐⭐⭐

This is one of the first things you should recognize.

```
Can the workload tolerate interruption?
              │
             YES
              │
              ▼
            SPOT
```

Example:

> Batch video processing can be interrupted and restarted.
> 

→ **Spot**

---

If interruption is **not acceptable**:

```
Is workload predictable/long-term?
              │
       ┌──────┴──────┐
      YES            NO
       │              │
       ▼              ▼
RI / Savings       On-Demand
Plan
```

### Trigger mapping

> **"Flexible, short-term, unpredictable"**
> 

→ **On-Demand**

> **"Predictable for 1–3 years"**
> 

→ **RI / Savings Plan**

> **"Interruptible / fault-tolerant / batch"**
> 

→ **Spot**

---

# 3. RI vs Savings Plan

If the question says:

> **"Long-term commitment but wants flexibility across EC2 usage."**
> 

Think:

**Savings Plan**

Especially:

> **Compute Savings Plan**
> 

when flexibility across compute services/configurations matters.

If the workload is stable and you want a more specific EC2 commitment:

> **EC2 Instance Savings Plan**
> 

may fit.

If the question explicitly emphasizes:

> **maximum RI discount / fixed configuration**
> 

→ **Standard Reserved Instance**

If it emphasizes:

> **ability to change instance attributes**
> 

→ **Convertible RI**

---

# 4. Capacity Reservation Engine ⭐⭐⭐⭐⭐

This is a classic trap.

Question:

> A company must ensure that EC2 capacity is available in a specific Availability Zone for a critical workload.
> 

Answer:

**On-Demand Capacity Reservation**

Why?

Because:

```
Capacity Reservation
        ↓
Guarantees capacity
        ↓
Specific AZ
```

It is **not primarily a discount mechanism**.

### Memory:

> **RI/Savings Plan = price**
> 
> 
> **Capacity Reservation = capacity**
> 

---

# 5. Dedicated Infrastructure Engine

Question:

> Existing commercial software license is based on physical CPU sockets/cores.
> 

→ **Dedicated Host**

Why?

You need visibility/control over the physical host.

---

Question:

> Instances must run on hardware dedicated to the AWS account, but there is no requirement for host-level visibility.
> 

→ **Dedicated Instance**

---

### Fast distinction

```
BYOL / socket / core
        ↓
Dedicated Host
```

```
Dedicated hardware only
        ↓
Dedicated Instance
```

---

# 6. Placement Group Engine ⭐⭐⭐⭐⭐

This is one of the most valuable scenario mappings.

## Cluster

Trigger phrases:

- HPC
- low latency
- maximum network throughput
- tightly coupled workloads
- scientific computing
- instances need to communicate extremely quickly

→ **Cluster Placement Group**

```
EC2 EC2 EC2 EC2
│   │   │   │
└── closely placed ──┘
```

### Memory

> **Cluster = close together**
> 

---

# 7. Spread

Trigger:

> **"Maximum hardware isolation"**
> 

→ **Spread Placement Group**

Instances are placed on separate underlying hardware.

```
Hardware 1 → EC2
Hardware 2 → EC2
Hardware 3 → EC2
```

Useful for a **small number of critical instances** where correlated hardware failure is the concern.

### Memory

> **Spread = spread apart**
> 

---

# 8. Partition

Trigger phrases:

- Kafka
- Cassandra
- HDFS
- HBase
- large distributed systems
- rack awareness
- separate failure domains

→ **Partition Placement Group**

Conceptually:

```
Partition 1
EC2 EC2 EC2

Partition 2
EC2 EC2 EC2

Partition 3
EC2 EC2 EC2
```

The distributed application can place replicas across partitions.

### Memory

> **Partition = distributed systems**
> 

---

# 9. Placement Group 15-Second Rule

When you see:

```
HPC / low latency
```

→ **Cluster**

When you see:

```
hardware isolation
```

→ **Spread**

When you see:

```
Kafka / Cassandra / HDFS
```

→ **Partition**

### One-line:

> **Cluster = close | Spread = separate | Partition = distributed**
> 

---

# 10. Storage Engine ⭐⭐⭐⭐⭐

The first question:

> **Does the data need to survive the instance lifecycle?**
> 

### Yes

→ **EBS**

### No

→ **Instance Store**

---

# 11. EBS Trigger

Look for:

- persistent block storage
- boot volume
- database disk
- survive stop
- detachable storage
- snapshot
- persistent application data

→ **EBS**

Example:

> EC2 instance stores application data that must survive a stop/start.
> 

→ **EBS**

---

# 12. Instance Store Trigger

Look for:

- temporary
- ephemeral
- scratch data
- cache
- temporary processing
- ultra-low-latency local storage
- data can be recreated

→ **Instance Store**

Example:

> A batch application needs extremely fast temporary scratch space and can recreate the data.
> 

→ **Instance Store**

---

# 13. EBS vs Instance Store Trap

Never choose Instance Store simply because the question says:

> **"high performance."**
> 

Ask:

> **Does the data need persistence?**
> 

```
High performance + persistent
→ EBS appropriate type

High performance + temporary
→ Instance Store
```

---

# 14. EBS Volume Engine ⭐⭐⭐⭐⭐

Once you've decided **EBS**, identify the workload.

```
General purpose
     ↓
   gp3
```

```
Very high sustained IOPS
     ↓
 io2 / io1
```

```
Large sequential throughput
     ↓
   st1
```

```
Infrequent / lowest-cost HDD
     ↓
   sc1
```

---

# 15. gp3 Trigger

Look for:

> **"IOPS and throughput should be configured independently of volume size."**
> 

→ **gp3**

This is one of the strongest gp3 clues.

Compare:

```
gp2
→ performance tied more closely to volume size
```

versus:

```
gp3
→ independently configure IOPS and throughput
```

### Memory

> **gp3 = decouple performance from size**
> 

---

# 16. io1 / io2 Trigger

Look for:

- very high IOPS
- sustained I/O performance
- latency-sensitive database
- provisioned IOPS

→ **io1/io2**

For modern choices, **io2** is generally the stronger high-end option when the requirements fit.

### Memory

> **io = IOPS**
> 

---

# 17. st1 Trigger

Look for:

- large sequential workloads
- high throughput
- HDD
- big data processing
- log processing
- data warehouse-type sequential access

→ **st1**

### Memory

> **st1 = sequential throughput**
> 

---

# 18. sc1 Trigger

Look for:

- infrequently accessed
- cold data
- lowest-cost HDD
- sequential access
- data doesn't need frequent access

→ **sc1**

### Memory

> **sc1 = cold storage**
> 

---

# 19. Storage Decision Tree

```
                  EBS?
                   │
                  YES
                   │
          What is the workload?
                   │
      ┌────────────┼─────────────┐
      │            │             │
 General       High IOPS      Sequential
 purpose        database       throughput
      │            │             │
     gp3         io1/io2        st1
                                  │
                            Infrequent?
                                  │
                                 sc1
```

More precisely, **st1 vs sc1** is the HDD decision:

```
Frequently accessed / throughput-heavy
→ st1

Cold / infrequent / lowest-cost
→ sc1
```

---

# 20. CPU Engine ⭐⭐⭐⭐⭐

Question:

> Application performs intensive CPU calculations continuously.
> 

→ **Compute Optimized**

Think:

```
CPU ████████████████
RAM ████
```

### Trigger phrases

- CPU-intensive
- compute-intensive
- high-performance computing on CPU
- batch CPU processing
- scientific calculations

→ **C-series**

---

# 21. Memory Engine

Question:

> Database requires very large RAM and keeps a huge working set in memory.
> 

→ **Memory Optimized**

Think:

```
CPU ████
RAM █████████████████
```

### Trigger phrases

- in-memory database
- large working dataset
- memory-intensive
- high RAM requirement

→ **R/X/etc.**

---

# 22. GPU / Accelerated Computing Engine

Question:

> Machine learning training requires GPUs.
> 

→ **Accelerated Computing**

Common SAA clue:

> **GPU**
> 

→ Think **P/G family**

For high-performance GPU/ML/HPC workloads:

> **P-series**
> 

For graphics/visualization-oriented GPU workloads:

> **G-series**
> 

---

# 23. FPGA

If the question specifically says:

> **FPGA acceleration**
> 

→ **F-series**

Memory:

> **F = FPGA**
> 

---

# 24. T-Series Engine ⭐⭐⭐⭐⭐

This is a common trap.

Question:

> Application normally uses low CPU but periodically experiences CPU spikes.
> 

→ **T-series**

```
Normal:
20%
25%
30%

Spike:
80%
90%
```

The important phrase is:

> **occasional**
> 

---

But:

> Application continuously consumes 90% CPU.
> 

→ **Compute Optimized**

```
Occasional CPU burst
→ T

Continuous CPU demand
→ C
```

This distinction is extremely exam-important.

---

# 25. Hibernation Engine

Question:

> Instance needs to restart quickly while preserving its in-memory state.
> 

→ **Hibernate**

Because:

```
Stop
→ EBS survives
→ RAM lost

Hibernate
→ EBS survives
→ RAM saved
```

### Trigger phrases

- preserve RAM
- resume previous state
- fast restart
- long application initialization
- expensive in-memory initialization

→ **Hibernation**

---

# 26. Hibernation Trap

Don't choose Hibernate merely because:

> "The instance needs to restart."
> 

Ask:

> **Does RAM/application state need to be preserved?**
> 

If no:

→ **Stop/start**

If yes:

→ **Hibernate**

---

# 27. Enhanced Networking Engine ⭐⭐⭐⭐⭐

Trigger phrases:

- extreme network performance
- high packets per second
- low network latency
- high bandwidth between EC2 instances
- network-intensive application

→ **Enhanced Networking**

Modern EC2 generally uses:

> **ENA**
> 

for enhanced networking.

---

# 28. Enhanced Networking vs Cluster Placement

These can appear together.

### Cluster Placement Group

Controls:

> **physical placement**
> 

### Enhanced Networking

Controls:

> **network interface/network performance**
> 

For an HPC architecture, you may use both:

```
Cluster Placement Group
        +
Enhanced Networking / EFA
        ↓
High-performance EC2 cluster
```

### Memory

> **Placement = where**
> 
> 
> **Networking = how fast network communication works**
> 

---

# 29. User Data Engine ⭐⭐⭐⭐⭐

Trigger:

> **"Automatically install/configure software when EC2 launches."**
> 

→ **User Data**

Example:

```
EC2 launches
   ↓
User Data
   ↓
Install nginx
   ↓
Configure nginx
   ↓
Start service
```

---

# 30. AMI Engine ⭐⭐⭐⭐⭐

Trigger:

> **"Reusable preconfigured server image."**
> 

→ **AMI**

Example:

```
AMI
├── OS
├── Application
├── Libraries
└── Configuration
```

Launch:

```
AMI
 ↓
EC2
```

### Memory:

> **AMI = baked image**
> 

---

# 31. AMI vs User Data — Critical Exam Comparison

| Requirement | Pick |
| --- | --- |
| Reusable preconfigured server | **AMI** |
| Install/configure at launch | **User Data** |
| Application already installed | **AMI** |
| Dynamic boot-time setup | **User Data** |
| Standardized base image | **AMI** |
| Environment-specific initialization | **User Data** |

### Best architecture

```
AMI
+
Small User Data
```

Think:

> **Bake stable things; bootstrap dynamic things.**
> 

---

# 32. Full EC2 Scenario Engine

Now combine everything.

```
                    EC2 Scenario
                         │
                         ▼
                  What is the clue?
                         │
      ┌──────────────────┼───────────────────┐
      │                  │                   │
   Pricing            Placement           Workload
      │                  │                   │
      ▼                  ▼                   ▼
   Spot/RI/          Cluster/Spread/     CPU/RAM/
   SP/Capacity       Partition            Storage/GPU
      │                                      │
      │                                      ▼
      │                               Instance Family
      │
      └─────────────────────────────────────────────
```

Then storage:

```
Persistent?
   │
 ┌─┴─┐
YES  NO
 │    │
EBS  Instance
     Store
```

If EBS:

```
General → gp3
IOPS → io2/io1
Sequential → st1
Cold HDD → sc1
```

---

# 33. Scenario Walkthrough #1

### Question

A company runs a batch video-processing workload. Jobs can be interrupted and restarted. The company wants to minimize compute cost.

### Trigger words

> **batch**
> 

> **interruptible**
> 

> **restartable**
> 

> **minimize cost**
> 

### Answer

**Spot Instances**

Why?

```
Interruptible
+
Fault tolerant
+
Cost sensitive
=
Spot
```

---

# 34. Scenario Walkthrough #2

### Question

A company operates a database continuously for several years with predictable usage and wants to reduce EC2 costs.

### Trigger words

> **continuous**
> 

> **predictable**
> 

> **long-term**
> 

### Answer

**Reserved Instances / Savings Plan**

Not:

- Spot
- On-Demand
- Capacity Reservation

---

# 35. Scenario Walkthrough #3

### Question

A financial application must always have EC2 capacity available in `us-east-1a`.

### Trigger:

> **capacity**
> 

> **specific AZ**
> 

### Answer

**On-Demand Capacity Reservation**

The question isn't primarily about discount.

It's about:

> **capacity availability**
> 

---

# 36. Scenario Walkthrough #4

### Question

A company owns software licensed based on physical CPU sockets and wants to migrate it to EC2.

### Trigger:

> **physical CPU sockets**
> 

### Answer

**Dedicated Host**

---

# 37. Scenario Walkthrough #5

### Question

A scientific application has tightly coupled EC2 nodes that require extremely low network latency.

### Trigger:

> **tightly coupled**
> 

> **low latency**
> 

### Answer:

**Cluster Placement Group**

Potentially combined with:

> **Enhanced Networking / EFA**
> 

depending on the exact question.

---

# 38. Scenario Walkthrough #6

### Question

A company runs Kafka across many EC2 instances and wants to minimize correlated hardware failures.

### Trigger:

> **Kafka**
> 

### Answer:

**Partition Placement Group**

Why?

Kafka is a distributed system where separating brokers across partitions can reduce correlated failure impact.

---

# 39. Scenario Walkthrough #7

### Question

A web application needs temporary high-speed scratch storage. The data can be recreated if the instance fails.

### Trigger:

> **temporary**
> 

> **high-speed local**
> 

> **recreatable**
> 

### Answer:

**Instance Store**

Not EBS.

---

# 40. Scenario Walkthrough #8

### Question

A database requires configurable high IOPS and predictable storage performance.

### Trigger:

> **high IOPS**
> 

> **database**
> 

### Answer:

**io2/io1**

---

# 41. Scenario Walkthrough #9

### Question

An application normally consumes 20% CPU but occasionally requires 80% CPU for several minutes.

### Trigger:

> **normally low**
> 

> **occasionally high**
> 

### Answer:

**T-series**

---

# 42. Scenario Walkthrough #10

### Question

An EC2 instance needs to automatically install an application whenever it is launched by an Auto Scaling Group.

### Trigger:

> **automatically configure at launch**
> 

### Answer:

**User Data in the Launch Template**

Architecture:

```
Launch Template
      │
      ├── AMI
      └── User Data
             │
             ▼
            EC2
```

---

# 43. Scenario Walkthrough #11

### Question

A company has standardized a fully configured application server and wants to launch identical instances repeatedly.

### Trigger:

> **standardized**
> 

> **fully configured**
> 

> **reusable**
> 

### Answer:

**AMI**

Potential architecture:

```
AMI
 ↓
Launch Template
 ↓
ASG
```

---

# 44. Scenario Walkthrough #12

### Question

A workload requires very high sequential throughput on local HDD storage.

### Trigger:

> **sequential**
> 

> **throughput**
> 

> **HDD**
> 

### Answer:

**st1**

---

# 45. Scenario Walkthrough #13

### Question

A workload needs the lowest-cost EBS HDD storage and accesses data infrequently.

### Trigger:

> **lowest cost**
> 

> **infrequent**
> 

> **HDD**
> 

### Answer:

**sc1**

---

# 46. Scenario Walkthrough #14

### Question

An application requires EBS performance that can be configured independently from volume capacity.

### Trigger:

> **independent IOPS/throughput**
> 

### Answer:

**gp3**

---

# 47. Scenario Walkthrough #15

### Question

A workload requires extreme network bandwidth and very high packets per second between EC2 instances.

### Trigger:

> **high bandwidth**
> 

> **high PPS**
> 

### Answer:

**Enhanced Networking**

If the scenario additionally says:

> tightly coupled HPC
> 

then consider:

**Cluster Placement Group + Enhanced Networking/EFA**

---

# 48. The "Don't Get Distracted" Rule ⭐⭐⭐⭐⭐

AWS exam questions often include several true statements.

Example:

> A batch application runs on EC2, requires very high CPU, can tolerate interruption, and needs the lowest possible cost.
> 

You might see:

- C-series
- T-series
- Spot
- Reserved Instance

Which clue is answering the actual question?

If the question asks:

> **"Which purchasing option minimizes cost?"**
> 

→ **Spot**

If it asks:

> **"Which instance type is best for CPU-intensive processing?"**
> 

→ **C-series**

Same workload, **different question → different answer**.

This is extremely important.

---

# 49. Trigger Phrase Priority

When multiple clues appear, identify **what the question is asking you to optimize**.

### Example

```
CPU-intensive
+
interruptible
+
lowest cost
```

Could involve:

```
C-series
+
Spot
```

The final answer depends on the question.

> **Instance type** → C
> 
> 
> **Purchasing model** → Spot
> 

---

# 50. Another Important Example

```
HPC
+
low latency
+
high network bandwidth
```

Possible answers:

- Enhanced Networking
- Cluster Placement Group
- EFA

Use the exact clue:

| Clue | Pick |
| --- | --- |
| Physical proximity | **Cluster Placement Group** |
| High network bandwidth/PPS | **Enhanced Networking / ENA** |
| HPC ultra-low-latency fabric | **EFA** |

Often the correct architecture combines them.

---

# 51. Scenario Engine — Pricing vs Instance Type

Never mix these categories.

### Purchasing decision

```
On-Demand
Reserved
Savings Plan
Spot
Capacity Reservation
```

### Instance type decision

```
T
M
C
R/X
I/D/H
P/G/F
```

### Placement decision

```
Cluster
Spread
Partition
```

A question can require **one from each category**.

Example:

> Fault-tolerant CPU-heavy batch workload requiring high performance.
> 

Potential architecture:

```
C-series
+
Spot
+
appropriate ASG/placement
```

---

# 52. EC2 Master Decision Matrix ⭐⭐⭐⭐⭐

| Category | Trigger | Answer |
| --- | --- | --- |
| **Purchase** | Interruptible | **Spot** |
| **Purchase** | Predictable long-term | **RI / Savings Plan** |
| **Purchase** | Guaranteed AZ capacity | **Capacity Reservation** |
| **Tenancy** | Physical socket/core licensing | **Dedicated Host** |
| **Tenancy** | Dedicated hardware | **Dedicated Instance/Host** |
| **Placement** | HPC / low latency | **Cluster** |
| **Placement** | Maximum hardware isolation | **Spread** |
| **Placement** | Kafka/Cassandra/HDFS | **Partition** |
| **CPU** | Continuous CPU-heavy | **C** |
| **Memory** | Large RAM | **R/X** |
| **Storage** | Local high-performance storage | **I/D/H** |
| **GPU** | GPU/ML | **P/G** |
| **FPGA** | FPGA | **F** |
| **CPU pattern** | Occasional spikes | **T** |
| **Restart** | Preserve RAM | **Hibernate** |
| **Storage** | Persistent block | **EBS** |
| **Storage** | Temporary local | **Instance Store** |
| **EBS** | Independent performance controls | **gp3** |
| **EBS** | Very high IOPS | **io2/io1** |
| **EBS** | Sequential throughput | **st1** |
| **EBS** | Cold/infrequent HDD | **sc1** |
| **Network** | Extreme bandwidth/PPS | **Enhanced Networking** |
| **Bootstrapping** | Configure at launch | **User Data** |
| **Image** | Reusable preconfigured server | **AMI** |

---

# 53. Final 60-Second EC2 Exam Scan 🔥

If you only have one minute before answering an EC2 question, run this:

```
INTERRUPTIBLE?
→ SPOT

PREDICTABLE LONG TERM?
→ RI / SAVINGS PLAN

SPECIFIC AZ CAPACITY?
→ CAPACITY RESERVATION

SOCKET/CORE LICENSE?
→ DEDICATED HOST

HPC / LOW LATENCY?
→ CLUSTER

MAX HARDWARE ISOLATION?
→ SPREAD

KAFKA / CASSANDRA / HDFS?
→ PARTITION

CPU HEAVY?
→ C

MEMORY HEAVY?
→ MEMORY OPTIMIZED

GPU?
→ ACCELERATED COMPUTING

CPU SPIKES?
→ T

PRESERVE RAM?
→ HIBERNATE

PERSISTENT BLOCK?
→ EBS

TEMPORARY LOCAL?
→ INSTANCE STORE

INDEPENDENT IOPS/THROUGHPUT?
→ GP3

HIGH IOPS?
→ IO2/IO1

SEQUENTIAL HDD?
→ ST1

COLD HDD?
→ SC1

EXTREME NETWORK?
→ ENHANCED NETWORKING

CONFIGURE AT BOOT?
→ USER DATA

REUSABLE SERVER IMAGE?
→ AMI
```

# The Ultimate EC2 Mental Model

Think of every EC2 question as asking you to choose along **five independent axes**:

```
                 EC2 WORKLOAD
                      │
       ┌──────────────┼───────────────┐
       │              │               │
       ▼              ▼               ▼
   HOW PAY?       WHAT CPU/RAM?   WHERE/HOW?
       │              │               │
 Spot/RI/SP       T/M/C/R/X       Placement
 On-Demand        GPU/FPGA        Networking
 Capacity
       │
       └──────────────┬────────────────┘
                      │
                      ▼
                 STORAGE
                      │
               EBS / Instance Store
                      │
                      ▼
                 BOOT/IMAGE
                      │
                  AMI + User Data
```

### The exam-winning rule:

> **Don't ask "Which AWS service do I remember?" Ask "What exact requirement is the question trying to solve?"**
> 

**EC2 Part 8 complete.**
