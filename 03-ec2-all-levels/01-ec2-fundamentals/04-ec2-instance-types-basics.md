# EC2 Instance Types — AWS SAA-C03 Detailed Notes

The main idea is:

> **EC2 instance types are different combinations of CPU, memory, networking, and storage performance, optimized for different workloads.**
> 

You should **not memorize every EC2 instance name** for SAA-C03. Instead, learn how to recognize the **instance family from the workload**.

---

# 1. EC2 Instance Naming Convention

Example:

```
m5.2xlarge
│ │ │
│ │ └── Size
│ └──── Generation
└────── Instance family
```

### `m`

The **instance family/class**.

`m` = **General Purpose**

### `5`

The **generation**.

Higher/newer generations generally provide newer hardware and better price/performance.

### `2xlarge`

The **instance size**.

Larger size generally means more:

- vCPUs
- Memory
- Network capacity
- EBS bandwidth

So:

```
m5.large
m5.xlarge
m5.2xlarge
m5.4xlarge
```

are the same general-purpose family but progressively larger sizes.

---

# 2. The 5 Main EC2 Instance Families You Should Know

For SAA-C03, focus heavily on these categories:

```
                 EC2 Instance Types
                        │
       ┌────────────────┼────────────────┐
       │                │                │
       ▼                ▼                ▼
 General Purpose   Compute Optimized   Memory Optimized
       │
       │
       └───────────────┬─────────────────
                       │
                       ▼
                 Storage Optimized
```

And also know **Accelerated Computing** for GPU/ML-type questions.

---

# 3. 🟢 General Purpose

### Main idea

General Purpose instances provide a **balance of**:

- Compute
- Memory
- Networking

Think:

> **"I don't have a special requirement. I need a balanced EC2."**
> 

### Common examples

- Web servers
- Application servers
- Code repositories
- Small/medium databases
- Development/test environments

Example:

```
Internet
   ↓
ALB
   ↓
EC2 General Purpose
   ↓
Application
```

If the application doesn't require unusually high CPU or unusually high memory, General Purpose is often a good starting point.

### Common families

You may see:

```
t3
t4g
m5
m6i
m7i
```

Don't memorize every generation. Understand the family.

---

# 4. ⚡ T Family — Burstable General Purpose

This is especially important for SAA-C03.

Examples:

```
t2.micro
t3.micro
t3.small
t4g.micro
```

T instances are **burstable**.

They are useful when CPU usage is normally low/moderate but **occasionally needs to spike**.

### Example

A small website:

```
Normal:
CPU ███░░░░░░

Traffic spike:
CPU ██████████
```

T instances can handle temporary CPU bursts using **CPU credits**.

### Exam clue

If you see:

> **"Bursty workload"**
> 

> **"CPU usage usually low but occasionally spikes"**
> 

👉 Think **T family**

---

# 5. 🔴 Compute Optimized

Examples:

```
c5
c6i
c7i
```

The `c` stands for **Compute**.

These instances are optimized for workloads that need **high CPU performance**.

Think:

> **CPU-heavy workload → Compute Optimized**
> 

### Common use cases

- Batch processing
- Media transcoding
- High-performance web servers
- High Performance Computing (HPC)
- Scientific modeling
- Machine learning workloads that are CPU-focused
- Dedicated gaming servers

### Example

Suppose you're processing thousands of videos:

```
Videos
  ↓
EC2
  ↓
CPU-heavy transcoding
```

You need lots of CPU rather than huge amounts of RAM.

👉 **Compute Optimized**

---

# 6. 🧠 Memory Optimized

Examples:

```
r5
r6i
r7i
```

The `r` family is commonly associated with **Memory Optimized** workloads.

Think:

> **Large dataset needs to be processed in RAM → Memory Optimized**
> 

### Common use cases

- High-performance databases
- In-memory databases
- Distributed caches
- Real-time big-data processing
- Large relational/non-relational databases

### Example

Suppose you have an in-memory application:

```
Large dataset
     ↓
RAM
     ↓
Very fast processing
```

You might choose a memory-optimized instance.

### Exam clue

Words like:

> **"large amount of memory"**
> 

> **"in-memory processing"**
> 

> **"large dataset in memory"**
> 

> **"in-memory database/cache"**
> 

👉 **Memory Optimized**

---

# 7. 💾 Storage Optimized

Examples include:

```
i3
i4i
d3
h1
```

These are designed for workloads requiring **high local storage performance**.

Think:

> **Lots of local data + high-speed local disk I/O → Storage Optimized**
> 

### Common use cases

- High-frequency OLTP
- Relational databases
- NoSQL databases
- Data warehousing
- Distributed file systems
- Local caching

### Example

```
EC2
 │
 └── Local NVMe SSD
          ↓
     Huge data
          ↓
   Very fast I/O
```

Some storage-optimized instances provide **local NVMe SSD instance storage**.

### Exam clue

If you see:

> **"very high local disk I/O"**
> 

> **"high sequential read/write"**
> 

> **"large datasets on local storage"**
> 

👉 Think **Storage Optimized**

---

# 8. 🚀 Accelerated Computing

This is another important category.

These instances use specialized hardware such as:

- GPUs
- AWS Inferentia
- AWS Trainium
- Other accelerators

Think:

> **Specialized hardware → Accelerated Computing**
> 

### Common use cases

- Machine learning training
- Machine learning inference
- GPU workloads
- Video processing
- Graphics
- Scientific computing

Example:

```
ML workload
    ↓
GPU / ML accelerator
    ↓
EC2 Accelerated Computing
```

---

# 9. Your Provided Examples

Let's decode the examples from your notes.

### `t2.micro`

```
t2.micro
│ │
│ └── micro = small size
└──── t2 = burstable general purpose
```

Your notes:

```
vCPU: 1
Memory: 1 GiB
Storage: EBS-only
```

It's suitable for small workloads and learning/testing.

---

### `t2.xlarge`

```
t2.xlarge
```

Still **T family**, so still burstable general purpose, but significantly larger than `t2.micro`.

```
vCPU: 4
Memory: 16 GiB
```

---

### `c5d.4xlarge`

Break it down:

```
c5d.4xlarge
│││
││└── Size
│└─── Generation
└──── Compute Optimized
```

`c` → Compute Optimized

`d` → includes **local instance storage**

So this is:

> **Compute Optimized + local storage**
> 

Example:

```
CPU-intensive workload
        +
Need fast local storage
        ↓
     c5d
```

---

### `r5.16xlarge`

```
r5.16xlarge
```

`r` → Memory Optimized

`16xlarge` → very large size

Your notes show:

```
64 vCPU
512 GiB RAM
```

This would make sense for a workload requiring a **large amount of memory**.

---

### `m5.8xlarge`

```
m5.8xlarge
```

`m` → General Purpose

`8xlarge` → large instance size

Your notes show:

```
32 vCPU
128 GiB RAM
```

Good for a workload needing a **balanced combination of CPU and memory**.

---

# 10. How to Choose an EC2 Instance

Don't start by memorizing instance names.

Start with the workload.

```
                 What does my workload need?
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
      CPU?               RAM?              Storage?
        │                  │                  │
        ▼                  ▼                  ▼
 Compute Optimized   Memory Optimized   Storage Optimized
```

If there isn't a strong specialization:

```
        ↓
General Purpose
```

If CPU usage is **bursty**:

```
        ↓
T family
```

If GPU/ML accelerator is required:

```
        ↓
Accelerated Computing
```

---

# 11. Instance Size Matters Too

Within a family, you have different sizes:

```
m5.large
   ↓
m5.xlarge
   ↓
m5.2xlarge
   ↓
m5.4xlarge
   ↓
m5.8xlarge
```

Generally:

> **Larger instance → more CPU + memory + networking capacity**
> 

But larger isn't automatically better.

AWS exam questions usually want you to choose an instance that provides the **required capacity without unnecessary cost**.

---

# 12. EBS-Only vs Instance Storage

Your table includes a very important distinction.

### EBS-only

```
EC2
 ↓
EBS
```

Persistent block storage.

Example:

```
m5.8xlarge
EBS Only
```

### Instance Store

```
EC2
 ↓
Local Instance Store
```

Fast but **ephemeral**.

Example:

```
c5d.4xlarge
1 × 400 NVMe SSD
```

The `d` variants commonly indicate **local instance storage**.

### Exam shortcut

> **Need persistent storage → EBS**
> 

> **Need very fast temporary local storage → Instance Store**
> 

---

# 13. EBS Bandwidth

Your table also shows:

```
EBS Bandwidth (Mbps)
```

This represents how much bandwidth the instance can provide for communication with EBS volumes.

For example:

```
EC2
  │
  │ EBS bandwidth
  ▼
EBS Volume
```

A larger/more capable instance generally provides higher EBS bandwidth.

This matters for applications doing lots of **EBS I/O**.

---

# 14. Network Performance

You may see:

```
Low
Moderate
Up to 10 Gbps
20 Gbps
```

This describes the instance's networking capability.

For example:

```
EC2 A
  │
  │ High network traffic
  ▼
EC2 B
```

If your workload requires very high network throughput, choose an appropriate instance size/family with sufficient networking capability.

---

# 15. SAA-C03 Scenario Recognition

### Scenario 1

> A web application requires a balanced amount of CPU and memory.
> 

👉 **General Purpose**

---

### Scenario 2

> A workload normally uses little CPU but occasionally has large CPU spikes.
> 

👉 **T family / Burstable**

---

### Scenario 3

> A company needs to perform CPU-intensive batch processing.
> 

👉 **Compute Optimized**

---

### Scenario 4

> A database needs a very large amount of memory for in-memory processing.
> 

👉 **Memory Optimized**

---

### Scenario 5

> An application performs very high local disk I/O using large datasets.
> 

👉 **Storage Optimized**

---

### Scenario 6

> A machine-learning application requires GPU acceleration.
> 

👉 **Accelerated Computing**

---

## The table as text

| Instance Family | Category | Key Characteristic |
| --- | --- | --- |
| **C** | Compute Optimized | Cost-effective high performance at a low price per compute ratio |
| **D** | Storage Optimized | High disk throughput |
| **G** | Accelerated Computing | Graphics-intensive GPU instances |
| **H** | Storage Optimized | HDD-based local storage for high disk throughput |
| **I** | Storage Optimized | High storage, low latency, high random I/O performance, high sequential read throughput, and high IOPS |
| **M** | General Purpose | Fixed performance |
| **P** | Accelerated Computing | General purpose GPU instances |
| **F** | Accelerated Computing | Reconfigurable FPGA instances |
| **R** | Memory Optimized | Memory-intensive applications |
| **T** | General Purpose | Burstable performance instances |
| **X** | Memory Optimized | Large-scale, enterprise-class, in-memory applications, and high-performance databases |

---

## 

# Learning purpose

The first letter of an EC2 instance type (like `c5.large` or `t3.micro`) tells you its **family**, and the family tells you what workload it is built for. The SAA exam often describes a workload and asks you to pick the right instance type, so you need to link **letter → purpose**.

### Group them into 5 categories

| Category | Letters | Use it when... |
| --- | --- | --- |
| **General Purpose** | **M, T** | Balanced CPU, memory and network. Web servers, small databases, dev/test |
| **Compute Optimized** | **C** | CPU-heavy work: batch processing, video encoding, gaming servers, scientific modeling |
| **Memory Optimized** | **R, X** | Large data held in RAM: in-memory databases, caches, big-data analytics |
| **Storage Optimized** | **D, H, I** | Heavy disk I/O: data warehouses, NoSQL databases, distributed file systems |
| **Accelerated Computing** | **G, P, F** | GPUs or FPGAs: machine learning, graphics rendering, specialized hardware |

### Quick difference to remember

- **M vs T:** M gives **fixed** (steady) performance. T is **burstable**, meaning it has a low baseline and can spike using CPU credits. T is cheap for light, spiky workloads.
- **R vs X:** Both are memory-heavy. **X** is for the most extreme cases (enterprise in-memory databases like SAP HANA).
- **D, H, I:** **I** is the fast one (SSD/NVMe, high IOPS, low latency). **H** uses HDD for throughput. **D** is dense storage for high disk throughput.
- **G vs P:** **G** is graphics-focused (rendering, streaming). **P** is general-purpose GPU compute (machine learning training).
- **F:** FPGA, hardware you can reprogram for custom acceleration.

### Memory tricks

| Letter | Mnemonic |
| --- | --- |
| **C** | **C**ompute / **C**PU |
| **R** | **R**AM |
| **X** | e**X**treme memory |
| **I** | **I**OPS |
| **D** | **D**ense storage |
| **H** | **H**DD |
| **G** | **G**raphics / **G**PU |
| **P** | **P**arallel GPU (ML) |
| **F** | **F**PGA |
| **T** | **T**iny / burs**T**able |
| **M** | **M**ain / balanced |

### Exam examples

- "In-memory database, very large RAM" → **R or X**
- "High IOPS, low-latency NoSQL database" → **I**
- "Machine learning training on GPUs" → **P**
- "Video encoding, high-performance web servers" → **C**
- "Small website with occasional traffic spikes, lowest cost" → **T**

**Note:** This table is a simplified summary. AWS has added newer families since (for example **Z**, **U**, **Inf/Trn** for ML inference/training, and **Hpc**), and the exact descriptions change over time. For the exam, the five categories and the main letters above are what matter most.

- 

# 

## Instance family → scenario cheat sheet

### General Purpose (M, T)

| Family | Use it when... | Example scenarios |
| --- | --- | --- |
| **M** | You need steady, balanced CPU/memory/network all the time | Application servers, backend servers, mid-size databases, enterprise apps, caching fleets |
| **T** | Workload is mostly idle with occasional spikes, and you want the lowest cost | Dev/test environments, small websites, microservices, low-traffic blogs, code repositories |

**T trap:** If the question says *sustained high CPU*, T is wrong. It runs out of CPU credits and gets throttled. Choose **M** or **C**.

### Compute Optimized (C)

| Family | Use it when... | Example scenarios |
| --- | --- | --- |
| **C** | CPU is the bottleneck | Batch processing, video encoding/transcoding, high-performance web servers, scientific modeling, gaming servers, ad serving, machine learning inference |

**Keywords:** "CPU-intensive", "high-performance computing", "batch jobs".

### Memory Optimized (R, X)

| Family | Use it when... | Example scenarios |
| --- | --- | --- |
| **R** | Large datasets must sit in RAM | In-memory caches (Redis/Memcached on EC2), real-time big-data analytics, open-source relational databases with large working sets |
| **X** | Extreme RAM, enterprise scale | SAP HANA, Microsoft SQL Server in-memory, large in-memory enterprise databases, Apache Spark with huge datasets |

**Keywords:** "in-memory database", "memory-intensive", "large RAM". Pick **X** when the question stresses *very large / enterprise / SAP HANA*.

### Storage Optimized (D, H, I)

| Family | Use it when... | Example scenarios |
| --- | --- | --- |
| **I** | You need very fast, low-latency random disk access (SSD/NVMe, high IOPS) | NoSQL databases (Cassandra, MongoDB), OLTP, transactional workloads, search engines like Elasticsearch, data warehousing |
| **D** | You need dense storage with high sequential throughput at low cost per TB | Distributed file systems, Hadoop/MapReduce, data warehouses, log processing |
| **H** | You need HDD-based local storage with high disk throughput | MapReduce, distributed file systems, big-data workloads on HDFS |

**Keywords:** "high IOPS", "low latency disk", "sequential read/write", "data warehouse".

### Accelerated Computing (G, P, F)

| Family | Use it when... | Example scenarios |
| --- | --- | --- |
| **G** | Graphics-heavy work on GPU | Video rendering, game streaming, 3D visualization, remote graphics workstations, ML inference |
| **P** | General-purpose GPU compute | Machine learning training, deep learning, HPC, computational finance, seismic analysis, drug discovery |
| **F** | You need custom, reprogrammable hardware (FPGA) | Genomics, real-time video processing, financial analytics, custom hardware acceleration |

**Keywords:** "GPU", "machine learning training", "graphics", "FPGA", "custom hardware".

---

## Quick decision flow

Ask yourself **"What is the bottleneck?"**

| Bottleneck / keyword in the question | Pick |
| --- | --- |
| Nothing special, balanced workload | **M** |
| Light load with spikes, cheapest | **T** |
| CPU | **C** |
| RAM | **R** (or **X** if extreme) |
| Disk IOPS / latency | **I** |
| Disk throughput, big sequential data | **D / H** |
| GPU / ML / graphics | **G / P** |
| FPGA / custom hardware | **F** |

## Exam examples

1. "Hosting a Cassandra NoSQL database that needs very high IOPS" → **I**
2. "Running SAP HANA in-memory database" → **X**
3. "Transcoding thousands of videos" → **C**
4. "Training a deep learning model" → **P**
5. "Dev/test server, used a few hours a day" → **T**
6. "Hadoop cluster processing huge sequential datasets" → **D or H**
7. "Caching layer with large datasets in RAM" → **R**
8. "Game streaming / 3D rendering" → **G**

**Note:** Real-world descriptions vary slightly by AWS documentation version, but this mapping matches what the SAA exam expects.

# 🧠 Final SAA-C03 Cheat Sheet

| Workload clue | Think |
| --- | --- |
| Balanced CPU + memory | **General Purpose** |
| Bursty CPU | **T family** |
| CPU-intensive | **Compute Optimized (C)** |
| Huge memory / in-memory | **Memory Optimized (R)** |
| High local disk I/O | **Storage Optimized (I/D)** |
| GPU / ML accelerator | **Accelerated Computing (P/G/Inf/Trn etc.)** |
| Persistent block storage | **EBS** |
| Fast temporary local storage | **Instance Store** |

### 🔥 The easiest memory trick

> **T = Temporary CPU bursts**
> 
> 
> **M = Mixed/balanced**
> 
> **C = Compute/CPU**
> 
> **R = RAM**
> 
> **I/D = Intensive storage**
> 
> **P/G = GPU**
> 
> **EBS = Persistent**
> 
> **Instance Store = Ephemeral**
> 

For SAA-C03, the **most important skill is recognizing the workload requirement**, not memorizing every current instance-generation number.
