# EC2 Placement Groups — SAA-C03 Exam Note

**Placement Group = a way to control where EC2 instances are physically placed relative to each other.**

There are **3 strategies**:

| Strategy | Main goal | Physical placement | AZ scope | Best for | **Exam trigger** |
| --- | --- | --- | --- | --- | --- |
| **Cluster** | **Low latency + high network throughput** | Instances are grouped closely together | **Single AZ** | Big Data jobs, HPC, tightly coupled applications | **“Low latency”**, **“high network throughput”**, **“10 Gbps between instances”**, **“complete job quickly”** |
| **Spread** | **Maximum isolation / HA** | Each instance is placed on separate underlying hardware | Can span **multiple AZs** | Critical applications where instance failure must be isolated | **“Different physical hardware”**, **“isolate failures”**, **“maximize HA”** |
| **Partition** | **Failure isolation for large distributed systems** | Instances are divided into partitions using different racks | Can span **multiple AZs** | **Kafka, Cassandra, HDFS, HBase** | **“Partitions”**, **“racks”**, **“Kafka/Cassandra/Hadoop”**, **“hundreds of instances”** |

---

## 1. Cluster Placement Group

### What?

EC2 instances are placed **close together inside a single Availability Zone**.

The goal is to give instances:

- very low network latency
- high network throughput

```
          SINGLE AZ
┌─────────────────────────────┐
│                             │
│   EC2 ── EC2 ── EC2         │
│     \    |    /             │
│       EC2                    │
│                             │
│   Cluster Placement Group   │
└─────────────────────────────┘
```

With Enhanced Networking, instances in a cluster placement group can achieve very high network performance.

### Scenario

A company runs a **Hadoop/Big Data processing job** where hundreds of machines continuously exchange large amounts of data and the job needs to finish as quickly as possible.

→ **Cluster Placement Group**

### Exam trigger

**“Low latency between EC2 instances”**

**“High network throughput”**

**“Big Data job needs to finish quickly”**

→ **CLUSTER**

### Memory

> **Cluster = Close together = fast communication**
> 

### Major disadvantage

The cluster is within **one AZ**.

If that AZ has a major failure:

```
AZ failure
   ↓
Cluster instances affected
```

So don't choose Cluster when the primary requirement is maximum fault isolation across AZs.

### Trap

> **“High availability across AZs” → NOT Cluster**
> 

---

# 2. Spread Placement Group

### What?

Spread placement puts instances on **different underlying physical hardware**.

The purpose is to prevent one hardware failure from taking down multiple instances.

```
AZ-A
─────────────────────
Hardware 1 → EC2
Hardware 2 → EC2
Hardware 3 → EC2

AZ-B
─────────────────────
Hardware 4 → EC2
Hardware 5 → EC2
```

Each instance is isolated from the others as much as possible.

### Scenario

A financial application has several critical servers. Losing one physical host should affect **only one EC2 instance**, not several instances simultaneously.

→ **Spread Placement Group**

### Exam trigger

**“Critical applications”**

**“Each instance must be isolated”**

**“Different underlying hardware”**

**“Reduce simultaneous hardware failure”**

→ **SPREAD**

### Memory

> **Spread = Spread instances apart**
> 

### Important limitation

A spread placement group supports a maximum of **7 running instances per AZ per placement group** under the commonly tested SAA-C03 model.

So if the question says:

> **“Hundreds of EC2 instances”**
> 

Spread is usually **not** the answer.

### Advantage

A spread placement group can span **multiple AZs**, providing additional failure isolation.

### Trap

Don't confuse:

**Spread → individual instance isolation**

with:

**Partition → groups of instances isolated by partitions/racks**

---

# 3. Partition Placement Group ⭐⭐⭐⭐⭐

### What?

Partition placement groups divide EC2 instances into **logical partitions**.

Each partition uses a **different set of racks**.

```
                 Partition Placement Group

AZ
┌─────────────────────────────────────────┐
│                                         │
│ Partition 1       Partition 2           │
│ ┌───────────┐     ┌───────────┐         │
│ │ EC2 EC2   │     │ EC2 EC2   │         │
│ │ EC2 EC2   │     │ EC2 EC2   │         │
│ └───────────┘     └───────────┘         │
│                                         │
│              Partition 3                │
│             ┌───────────┐               │
│             │ EC2 EC2   │               │
│             │ EC2 EC2   │               │
│             └───────────┘               │
│                                         │
│ Different partitions use different racks│
└─────────────────────────────────────────┘
```

If one partition has a hardware/rack failure, other partitions can continue operating.

### Scenario

You're running **Apache Kafka** with many brokers and want brokers distributed across separate hardware/rack groups so that a rack failure doesn't affect the entire cluster.

→ **Partition Placement Group**

### Exam triggers

**“Kafka”**

**“Cassandra”**

**“HDFS / HBase”**

**“Hundreds of EC2 instances”**

**“Different racks”**

**“Partition-level failure isolation”**

→ **PARTITION**

### Memory

> **Partition = Big distributed systems**
> 

Think:

**P → Partition → Kafka**

---

# Cluster vs Spread vs Partition — The Most Important Table

|  | 🔵 **Cluster** | 🟢 **Spread** | 🟠 **Partition** |
| --- | --- | --- | --- |
| Primary goal | **Performance** | **Isolation** | **Distributed-system isolation** |
| Instances | Close together | Far apart | Grouped into partitions |
| Hardware | Close placement | Separate hardware | Separate racks per partition |
| AZ | **Single AZ** | Can span AZs | Can span AZs |
| Scale | Depends on instance/AZ capacity | **7 instances/AZ/group** | **100s of instances** |
| Failure model | AZ failure can affect group | Individual hardware failure affects fewer instances | Partition/rack failure affects one partition |
| Network focus | **Very high throughput/low latency** | Isolation | Distributed workload |
| Typical workloads | HPC, Big Data | Critical isolated applications | Kafka, Cassandra, HDFS, HBase |
| Keyword | **FAST** | **ISOLATED** | **PARTITIONED** |

---

# 🔥 How to Choose in an Exam

### Question says:

> **“Low latency and high network throughput”**
> 

→ **Cluster**

---

### Question says:

> **“Instances must be isolated from each other”**
> 

→ **Spread**

---

### Question says:

> **“Kafka brokers distributed across racks”**
> 

→ **Partition**

---

### Question says:

> **“Hundreds of EC2 instances”**
> 

→ **Partition**

---

### Question says:

> **“Critical instances should not share the same underlying hardware”**
> 

→ **Spread**

---

### Question says:

> **“Big Data processing needs to complete quickly”**
> 

→ **Cluster**

---

# 🧠 Perfect Memory Trick

Remember:

> **CLUSTER = CLOSE**
> 

> **SPREAD = SEPARATE**
> 

> **PARTITION = RACKS**
> 

Or even shorter:

```
CLUSTER
   ↓
FAST

SPREAD
   ↓
ISOLATION

PARTITION
   ↓
DISTRIBUTED SYSTEMS
```

### One-line exam rule

> **FAST communication → Cluster | Maximum instance isolation → Spread | Large distributed systems/rack isolation → Partition**
>
