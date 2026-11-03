# EC2 Instance Families — SAA-C03 Master Table

Absolutely. For **SAA-C03**, the easiest way is to map the **instance family letter → workload → trigger phrase**.

One important correction for your reference question: **Storage Optimized (I/D/H families) is the intended family for very large datasets on local storage with high sequential/random I/O**. The question wording you provided says “high sequential read/write access to very large data sets on local storage,” which points strongly to **Storage Optimized**, not Compute Optimized.

## EC2 Instance Families — SAA-C03 Master Table

| Family | Category | Main purpose | When to choose it | **Exam trigger phrases** | Tiny example | Memory shortcut |
| --- | --- | --- | --- | --- | --- | --- |
| **C** | **Compute Optimized** | High-performance CPU workloads | When CPU is the bottleneck | **CPU-intensive**, **compute-intensive**, **high-performance computing**, batch processing | Video encoding or scientific calculations | **C = Compute** |
| **D** | **Storage Optimized** | High disk throughput with local HDD storage | Large datasets requiring high-throughput local storage | **High disk throughput**, **large sequential I/O**, HDD-based local storage | Log/data processing requiring lots of local disk throughput | **D = Dense storage** |
| **G** | **Accelerated Computing** | GPU for graphics/general GPU workloads | Graphics, visualization, ML inference, GPU applications | **Graphics-intensive**, **GPU**, 3D rendering | 3D visualization application | **G = GPU / Graphics** |
| **H** | **Storage Optimized** | High-capacity HDD-based local storage | Very large datasets needing high sequential disk throughput | **HDD**, **high disk throughput**, **large sequential reads/writes** | Big-data workload scanning huge local datasets | **H = HDD** |
| **I** | **Storage Optimized** | High IOPS + low-latency local storage | Random I/O and high-performance databases | **Low latency**, **high random I/O**, **high IOPS**, **sequential read throughput** | NoSQL/database workload requiring extremely fast local SSD | **I = IOPS** |
| **M** | **General Purpose** | Balanced CPU, memory, networking | Normal applications with balanced resource requirements | **General purpose**, **balanced compute/memory**, web servers | Typical business application server | **M = Mixed / balanced** |
| **P** | **Accelerated Computing** | Powerful GPU compute | GPU-heavy scientific/ML workloads | **GPU compute**, **machine learning**, **deep learning**, **HPC** | Training a deep-learning model | **P = Powerful GPU** |
| **F** | **Accelerated Computing** | FPGA acceleration | Highly specialized/reconfigurable hardware acceleration | **FPGA**, **reconfigurable hardware**, custom acceleration | Financial risk calculation acceleration | **F = FPGA** |
| **R** | **Memory Optimized** | Large RAM | Applications that need lots of memory | **Memory-intensive**, **large in-memory dataset**, **in-memory cache** | Large in-memory database | **R = RAM** |
| **T** | **General Purpose** | Burstable CPU performance | Workloads normally use low CPU but occasionally spike | **Burstable**, **CPU credits**, **variable CPU usage**, **T2/T3/T4g** | Small web server with occasional traffic spikes | **T = Turbo when needed** |
| **X** | **Memory Optimized** | Extremely large memory | Enterprise in-memory applications and huge databases | **Huge memory**, **enterprise in-memory**, **very large database**, **SAP HANA** | Large SAP HANA database | **X = eXtreme memory** |

---

# The Most Important Exam Decision

Think of EC2 families as this:

| If the question emphasizes... | Pick |
| --- | --- |
| **CPU / compute power** | **C – Compute Optimized** |
| **Balanced CPU + memory** | **M – General Purpose** |
| **Burstable CPU / CPU credits** | **T – Burstable General Purpose** |
| **RAM / memory-intensive** | **R – Memory Optimized** |
| **Extreme/huge memory / SAP HANA** | **X – Memory Optimized** |
| **HDD + huge sequential throughput** | **D/H – Storage Optimized** |
| **High IOPS + low latency local storage** | **I – Storage Optimized** |
| **GPU / graphics** | **G** |
| **GPU compute / ML training** | **P** |
| **FPGA / reconfigurable hardware** | **F** |

---

# Your Reference Question

> Workloads require **high, sequential read and write access to very large data sets on local storage**.
> 

The key words are:

**HIGH + SEQUENTIAL + VERY LARGE DATASETS + LOCAL STORAGE**

That means the workload is primarily **storage/I/O focused**, not CPU-focused.

### Correct family: **Storage Optimized**

The important distinction:

- **C** → CPU is the bottleneck
- **R/X** → RAM is the bottleneck
- **I/D/H** → **local storage/I/O is the bottleneck**
- **M** → balanced resources

### Exam shortcut

> **“Very large dataset + local storage + high sequential I/O” → STORAGE OPTIMIZED**
> 

---

# C vs I — Very Common Confusion

|  | **C – Compute Optimized** | **I – Storage Optimized** |
| --- | --- | --- |
| Bottleneck | CPU | Storage/I/O |
| Main strength | High CPU performance | High IOPS / low-latency storage |
| Typical workload | Batch processing, HPC, scientific calculations | High-performance databases, NoSQL, real-time I/O |
| Trigger | **Compute-intensive** | **High IOPS / low latency / random I/O** |

### Easy rule

**CPU problem → C**

**Disk/I/O problem → I**

---

# I vs D/H — Another Important Confusion

|  | **I** | **D/H** |
| --- | --- | --- |
| Storage | Local SSD/NVMe-oriented | Local HDD-oriented |
| Best for | **High IOPS + low latency** | **High sequential throughput + huge capacity** |
| I/O pattern | Random + sequential | Primarily large sequential workloads |
| Trigger | **IOPS, low latency, random I/O** | **HDD, sequential throughput, very large datasets** |

### Brain shortcut

> **I = IOPS**
> 

> **H = HDD**
> 

> **D = Dense storage**
> 

So:

**“Millions of random reads/writes with very low latency” → I**

**“Huge dataset streamed sequentially from HDD” → D/H**

---

# M vs T

|  | **M – General Purpose** | **T – Burstable** |
| --- | --- | --- |
| Performance | Consistent/balanced | Baseline + CPU bursts |
| CPU credits | No | **Yes** |
| Best for | Normal balanced applications | Workloads with occasional CPU spikes |
| Trigger | **General purpose / balanced** | **Burstable / CPU credits / unpredictable occasional CPU spikes** |

### Example

A normal application server requiring consistent CPU and memory:

→ **M**

A small website normally using 10% CPU but occasionally jumping to 80%:

→ **T**

---

# R vs X

|  | **R** | **X** |
| --- | --- | --- |
| Category | Memory Optimized | Memory Optimized |
| Focus | Large-memory workloads | **Extreme memory requirements** |
| Trigger | **Memory-intensive** | **Huge memory / enterprise in-memory database / SAP HANA** |
| Example | In-memory analytics | Massive SAP HANA deployment |

### Shortcut

> **R = RAM-heavy**
> 

> **X = eXtreme RAM**
> 

---

# GPU Families

| Family | What makes it special | **Exam trigger** |
| --- | --- | --- |
| **G** | General GPU / graphics | **Graphics, visualization, 3D rendering** |
| **P** | Powerful GPU compute | **Machine learning, deep learning, GPU computing** |
| **F** | FPGA | **FPGA, custom/reconfigurable acceleration** |

### Remember:

**G → Graphics**

**P → Powerful GPU**

**F → FPGA**

---

# 🔥 Final EC2 Family Exam Cheat Sheet

| **Question says...** | **Think immediately...** |
| --- | --- |
| **CPU-intensive** | 🧠 **C** |
| **Compute-intensive** | **C** |
| **HPC calculations** | **C** |
| **General-purpose application** | **M** |
| **Balanced compute + memory** | **M** |
| **Burstable CPU** | **T** |
| **CPU credits** | **T** |
| **Memory-intensive** | **R** |
| **Large in-memory application** | **R** |
| **Huge memory / SAP HANA** | **X** |
| **High disk throughput** | **D/H** |
| **HDD-based local storage** | **H/D** |
| **Very large datasets + sequential I/O** | **D/H** |
| **High IOPS** | **I** |
| **Low-latency random I/O** | **I** |
| **Graphics-intensive** | **G** |
| **GPU machine learning** | **P** |
| **FPGA** | **F** |

## 🧠 10-Second Memory Map

**C → CPU**

**M → Mixed/Balanced**

**T → Turbo/Burst**

**R → RAM**

**X → eXtreme RAM**

**I → IOPS**

**D/H → Dense/HDD Storage**

**G → Graphics**

**P → Powerful GPU**

**F → FPGA**

### Most important SAA-C03 rule

> **Identify the bottleneck first: CPU → C, Memory → R/X, Storage/I/O → I/D/H, GPU → G/P, FPGA → F, balanced → M, burstable → T.**
>
