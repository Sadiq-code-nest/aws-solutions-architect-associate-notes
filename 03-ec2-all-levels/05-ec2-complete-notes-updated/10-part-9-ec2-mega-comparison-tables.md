# Part 9 — EC2 Mega Comparison Tables 🔥

This is the **final EC2 comparison layer** for SAA-C03.

Focus on the **trigger words in bold**—these are what should drive your answer.

---

## 1. On-Demand vs RI vs Savings Plan vs Spot

|  | **On-Demand** | **Reserved Instance** | **Savings Plan** | **Spot** |
| --- | --- | --- | --- | --- |
| Core idea | Pay as you go | Commit to EC2 usage | Commit to $/hour usage | Buy unused EC2 capacity cheaply |
| Best for | **Unpredictable / short-term** | **Predictable long-term** | **Predictable long-term + flexibility** | **Interruptible workloads** |
| Commitment | None | 1 or 3 years | 1 or 3 years | None |
| Discount | Lowest of these four | High | High | **Up to ~90%** |
| Interruption | No | No | No | **Yes** |
| Capacity guarantee | No | **No** | No | No |
| Best clue | **"No commitment"** | **"1/3-year EC2 commitment"** | **"Flexible commitment"** | **"Batch / fault tolerant / interruptible"** |

### Exam shortcut

> **No commitment → On-Demand**
> 
> 
> **Long-term EC2 → RI/SP**
> 
> **Flexible long-term → Savings Plan**
> 
> **Can be interrupted → Spot**
> 

---

# 2. Standard RI vs Convertible RI

|  | **Standard RI** | **Convertible RI** |
| --- | --- | --- |
| Discount | **Higher** | Lower than Standard |
| Flexibility | Lower | **Higher** |
| Change instance attributes | Limited | **Can exchange** for another configuration |
| Best for | Stable workload | Long-term workload where requirements may change |
| Exam clue | **"Highest discount"** | **"Need flexibility to change instance type/family"** |

### Memory

> **Standard = savings**
> 
> 
> **Convertible = flexibility**
> 

### Trap

Don't choose Convertible simply because it is long-term.

If the configuration is stable and maximum discount is desired:

→ **Standard RI**

---

# 3. Compute Savings Plan vs EC2 Instance Savings Plan

|  | **Compute Savings Plan** | **EC2 Instance Savings Plan** |
| --- | --- | --- |
| Flexibility | **Highest** | Lower |
| Applies across | EC2 + Lambda + Fargate | **EC2 instance family in a region** |
| Change instance family | More flexible | Generally committed to family/region |
| Change OS/tenancy | Flexible | More constrained |
| Best clue | **"Maximum flexibility"** | **"Committed to an EC2 family"** |

### Memory

> **Compute SP = compute is flexible**
> 
> 
> **EC2 SP = EC2 family commitment**
> 

---

# 4. Dedicated Host vs Dedicated Instance

|  | **Dedicated Host** | **Dedicated Instance** |
| --- | --- | --- |
| Physical server dedicated? | ✅ | ✅ |
| Visibility into physical host | **Yes** | No |
| Socket/core visibility | **Yes** | No |
| BYOL scenarios | **Excellent** | More limited |
| Best clue | **"Existing license based on sockets/cores"** | **"Dedicated hardware/tenancy"** |

### Exam trigger

> **BYOL + physical sockets/cores → Dedicated Host**
> 

> **Dedicated hardware without host-level control → Dedicated Instance**
> 

---

# 5. Capacity Reservation vs RI

|  | **Capacity Reservation** | **Reserved Instance** |
| --- | --- | --- |
| Main purpose | **Guarantee capacity** | **Reduce cost** |
| Specific AZ | **Yes** | No capacity guarantee |
| Commitment | No 1/3-year requirement | Usually 1/3 years |
| Billing discount | No inherent discount | **Yes** |
| Can combine with Savings Plan/RI? | **Yes, billing/discount mechanisms can be used separately** | N/A |
| Trigger | **"Ensure capacity in AZ"** | **"Lower predictable EC2 cost"** |

### ⭐ Critical exam distinction

> **Capacity Reservation = capacity**
> 

> **RI = discount**
> 

---

# 6. EBS vs Instance Store

|  | **EBS** | **Instance Store** |
| --- | --- | --- |
| Type | Network block storage | **Physical local storage** |
| Persistence | **Persistent** | **Ephemeral** |
| Survives stop? | ✅ | ❌ |
| Survives instance termination? | Usually, depending on DeleteOnTermination | ❌ |
| Snapshot | ✅ | ❌ |
| Detachable | ✅ | ❌ |
| AZ-bound | **Yes** | Instance-local |
| Best for | OS, database, persistent application data | **Temporary scratch/cache** |

### Memory

> **EBS = External + persistent**
> 

> **Instance Store = Inside + temporary**
> 

### Trap

High performance does **not automatically mean Instance Store**.

If persistence is required:

→ **EBS**

---

# 7. gp2 vs gp3

|  | **gp2** | **gp3** |
| --- | --- | --- |
| Type | General-purpose SSD | General-purpose SSD |
| IOPS relationship | **Linked to volume size** | **Independent of size** |
| Baseline IOPS | 3 IOPS/GiB | **3,000 IOPS** |
| Baseline throughput | Varies | **125 MiB/s** |
| Max IOPS | 16,000 | **16,000** |
| Max throughput | Lower | **1,000 MiB/s** |
| Best clue | Existing gp2 / size-driven performance | **"Independently configure IOPS/throughput"** |

### ⭐ Memory

> **gp2 → size determines performance**
> 

> **gp3 → performance can be tuned separately**
> 

---

# 8. io1 vs io2

|  | **io1** | **io2** |
| --- | --- | --- |
| Type | Provisioned IOPS SSD | Provisioned IOPS SSD |
| Use | Mission-critical I/O | **Mission-critical I/O** |
| Reliability | High | **Higher durability** |
| Max IOPS | Up to **64,000** on supported Nitro instances | Up to **256,000** with Block Express |
| Multi-Attach | Supported | **Supported** |
| Latency | Low | **Sub-millisecond** with Block Express |
| Best modern choice | Older PIOPS workloads | **High-end PIOPS** |

### Trigger

> **Very high sustained IOPS → io2/io1**
> 

> **Extreme IOPS / Block Express → io2**
> 

### Memory

> **io = IOPS**
> 

---

# 9. st1 vs sc1

|  | **st1** | **sc1** |
| --- | --- | --- |
| Type | HDD | HDD |
| Purpose | **Throughput-intensive** | **Cold/infrequently accessed** |
| Cost | Low | **Lowest-cost HDD** |
| Best workload | Big Data, logs, data warehouses | Cold sequential data |
| Trigger | **"High sequential throughput"** | **"Lowest cost + infrequent access"** |
| Boot volume | ❌ | ❌ |

### Memory

> **st1 = throughput**
> 

> **sc1 = cold**
> 

---

# 10. Cluster vs Spread vs Partition

|  | **Cluster** | **Spread** | **Partition** |
| --- | --- | --- | --- |
| Main goal | **Low latency / high network throughput** | **Hardware isolation** | **Distributed-system failure isolation** |
| Placement | Close together | Separate hardware | Separate partitions/racks |
| AZ scope | Single AZ | Can span AZs | Can span AZs |
| Scale | Limited | Small number | **100s of instances** |
| Classic workloads | HPC | Critical isolated instances | **Kafka, Cassandra, HDFS** |
| Failure benefit | Weakest | **Strongest isolation** | Separates failure domains |

### Memory

> **Cluster = CLOSE**
> 

> **Spread = SEPARATE**
> 

> **Partition = DISTRIBUTE**
> 

---

# 11. User Data vs AMI

|  | **User Data** | **AMI** |
| --- | --- | --- |
| Purpose | **Bootstrap/configure at launch** | **Reusable machine image** |
| When | Launch/boot | Before launch |
| Contains | Scripts/commands | OS + installed software + configuration |
| Dynamic setup | **Excellent** | Less suitable |
| Standardized server | Can help | **Excellent** |
| ASG usage | Launch Template can reference User Data | Launch Template references AMI |

### Exam clue

> **"Run a script when instance launches" → User Data**
> 

> **"Launch identical preconfigured instances" → AMI**
> 

### Memory

> **AMI = baked**
> 

> **User Data = boot**
> 

---

# 12. Stop vs Hibernate vs Terminate

|  | **Stop** | **Hibernate** | **Terminate** |
| --- | --- | --- | --- |
| Instance removed? | No | No | **Yes** |
| EBS root survives? | Usually | Usually | Depends on DeleteOnTermination |
| RAM preserved? | ❌ | **✅** | ❌ |
| Restart possible? | ✅ | **Resume state** | ❌ |
| Compute billing while stopped | ❌ | ❌ | ❌ |
| Useful for | Normal temporary shutdown | **Fast resume with memory state** | Permanent deletion |
| Trigger | **"Stop/start"** | **"Preserve RAM"** | **"Delete instance"** |

### Memory

> **Stop = shutdown**
> 

> **Hibernate = sleep**
> 

> **Terminate = destroy**
> 

---

# 13. T-Series vs Other EC2 Families

|  | **T-series** | **C-series** | **M-series** | **R/X-series** | **I/D/H-series** | **P/G/F-series** |
| --- | --- | --- | --- | --- | --- | --- |
| Main purpose | **Burstable** | Compute | General purpose | Memory | Storage | Accelerated |
| CPU pattern | **Occasional spikes** | Sustained CPU | Balanced | Memory-heavy | Storage-heavy | GPU/FPGA |
| Example | Web server with occasional bursts | Batch calculation | Typical application server | In-memory DB | Big sequential/local storage | ML/graphics |
| Trigger | **"CPU credits / burst"** | **"CPU intensive"** | **"Balanced"** | **"Large memory"** | **"High local storage"** | **"GPU/FPGA"** |

### ⭐ Exam shortcut

> **Burst → T**
> 
> 
> **Compute → C**
> 
> **General → M**
> 
> **Memory → R/X**
> 
> **Storage → I/D/H**
> 
> **GPU → P/G**
> 
> **FPGA → F**
> 

---

# 14. Compute vs Memory vs Storage vs Accelerated

| Workload | Pick | Trigger phrase | Example |
| --- | --- | --- | --- |
| CPU-intensive | **Compute Optimized** | **"CPU-heavy"** | Video encoding |
| Balanced | **General Purpose** | **"balanced compute/memory"** | Web application |
| Memory-intensive | **Memory Optimized** | **"large in-memory dataset"** | In-memory database |
| Local storage-intensive | **Storage Optimized** | **"high sequential I/O on local storage"** | Big-data processing |
| GPU | **Accelerated Computing** | **"GPU / ML / graphics"** | ML training |
| FPGA | **Accelerated Computing** | **"FPGA"** | Specialized acceleration |

### Important

The question you gave earlier:

> **"high, sequential read and write access to very large data sets on local storage"**
> 

→ **Storage Optimized**

Not Compute Optimized.

The word **local storage** is the decisive clue.

---

# 15. Enhanced Networking vs EBS-Optimized

|  | **Enhanced Networking** | **EBS-Optimized** |
| --- | --- | --- |
| Optimizes | **Network traffic** | **EBS traffic** |
| Focus | Network bandwidth/PPS/latency | Dedicated EBS bandwidth |
| Technology | **ENA / EFA** | Dedicated EBS network bandwidth |
| Trigger | **"high network throughput"** | **"high EBS throughput / EBS I/O"** |
| Typical workload | HPC/network-intensive application | Database using high-performance EBS |
| Can coexist? | **Yes** | **Yes** |

### Memory

> **Network → Enhanced Networking**
> 

> **Disk → EBS-optimized**
> 

---

# 16. Launch Template vs AMI

These are **not replacements for each other**.

|  | **Launch Template** | **AMI** |
| --- | --- | --- |
| Purpose | **How to launch EC2** | **What the machine contains** |
| Contains | Instance type, AMI ID, SG, key pair, User Data, network config, etc. | OS/software/configuration/image |
| ASG | **Core ASG launch configuration** | Referenced by Launch Template |
| Versioning | **Yes** | Image versions |
| Reusable | Yes | Yes |
| Trigger | **"Launch configuration/settings"** | **"Preconfigured image"** |

### Mental model

```
AMI
 ↓
"What is installed?"
       +
Launch Template
 ↓
"How should I launch it?"
       ↓
EC2
```

### Exam trap

If question says:

> "New AMI needs to be used by an Auto Scaling Group."
> 

You normally update the **Launch Template** to reference the new AMI.

---

# 17. Instance Profile vs IAM Role

|  | **IAM Role** | **Instance Profile** |
| --- | --- | --- |
| Purpose | Defines permissions | **Container for role attached to EC2** |
| Used by | AWS services/users/workloads | **EC2** |
| Contains | Permissions/trust relationship | IAM role |
| Directly attached to EC2? | Technically via instance profile | **Yes** |
| Typical exam wording | **"EC2 needs permission to S3"** | **"Attach role to EC2"** |

### Important exam concept

You normally do **not** put access keys into the EC2 application.

Instead:

```
EC2
 ↓
Instance Profile
 ↓
IAM Role
 ↓
Temporary Credentials
 ↓
S3 / DynamoDB / etc.
```

### Memory

> **Role = permissions**
> 

> **Instance Profile = role wrapper for EC2**
> 

---

# 🔥 FINAL EC2 MEGA SCAN

| If the question says… | Pick |
| --- | --- |
| **Interruptible / fault-tolerant / batch** | **Spot** |
| **Predictable long-term** | **RI / Savings Plan** |
| **Maximum flexibility for long-term compute commitment** | **Compute Savings Plan** |
| **Highest RI discount + stable configuration** | **Standard RI** |
| **Change RI configuration later** | **Convertible RI** |
| **Guarantee EC2 capacity in a specific AZ** | **Capacity Reservation** |
| **Physical sockets/cores / BYOL** | **Dedicated Host** |
| **Dedicated hardware tenancy** | **Dedicated Instance** |
| **HPC + low latency** | **Cluster** |
| **Maximum hardware isolation** | **Spread** |
| **Kafka / Cassandra / HDFS** | **Partition** |
| **Persistent block storage** | **EBS** |
| **Temporary ultra-fast local storage** | **Instance Store** |
| **Independent IOPS + throughput** | **gp3** |
| **Very high sustained IOPS** | **io2/io1** |
| **Sequential HDD throughput** | **st1** |
| **Lowest-cost cold HDD** | **sc1** |
| **CPU-intensive** | **Compute Optimized / C** |
| **Large memory / in-memory** | **Memory Optimized / R/X** |
| **Large local storage** | **Storage Optimized / I/D/H** |
| **GPU** | **P/G** |
| **FPGA** | **F** |
| **Occasional CPU spikes** | **T-series** |
| **Preserve RAM during shutdown** | **Hibernate** |
| **Run configuration script at launch** | **User Data** |
| **Reusable preconfigured machine** | **AMI** |
| **High network bandwidth/PPS** | **Enhanced Networking** |
| **High EBS I/O bandwidth** | **EBS-optimized** |
| **EC2 needs AWS permissions** | **IAM Role via Instance Profile** |

## 🧠 The 17-word EC2 memory sentence

> **Spot interrupts. RI saves. Capacity reserves. Host licenses. Cluster closes. Spread isolates. Partition distributes. C computes. R remembers. I stores. T bursts. EBS persists. Store disappears. gp3 separates. io maximizes. User Data boots. AMI bakes.**
> 

That sentence is worth memorizing before the exam.
