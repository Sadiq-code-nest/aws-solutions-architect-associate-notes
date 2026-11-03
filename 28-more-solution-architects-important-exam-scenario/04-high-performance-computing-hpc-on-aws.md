# High Performance Computing (HPC) on AWS

Absolutely. For **SAA-C03**, think of HPC as a combination of **fast data transfer + powerful compute + ultra-fast networking + high-performance storage + job orchestration**.

# AWS High Performance Computing (HPC) — SAA-C03 Exam Note

## 1. What is HPC?

**High Performance Computing (HPC)** means using many powerful computing resources together to solve **very large/complex problems quickly**.

Instead of:

> One server → takes 10 days
> 

HPC:

> 100 powerful servers working together → potentially finish much faster.
> 

### Common HPC workloads

- Genomics
- Computational chemistry
- Weather forecasting
- Financial risk modeling
- Machine learning / deep learning
- Autonomous driving
- Scientific simulations
- Engineering simulations

### Simple real-world example

Imagine a company needs to simulate **millions of weather conditions**.

Instead of one EC2 instance calculating everything sequentially:

```
                    HPC Cluster
                        │
        ┌───────────────┼────────────────┐
        ↓               ↓                ↓
      EC2 #1          EC2 #2           EC2 #3
   calculations    calculations     calculations
        │               │                │
        └───────────────┼────────────────┘
                        ↓
                    Final Result
```

**Memory trick:**

> **HPC = Lots of powerful computers working together on a big problem.**
> 

---

# 2. The 5 Pieces of AWS HPC

For the exam, remember:

| HPC Requirement | AWS Services |
| --- | --- |
| **Move data** | Direct Connect, Snowball, Snowmobile, DataSync |
| **Compute** | EC2, GPU instances, Spot Instances |
| **Fast networking** | Cluster Placement Group, Enhanced Networking, EFA |
| **Fast storage** | EBS, Instance Store, S3, EFS, FSx for Lustre |
| **Job management** | AWS Batch, ParallelCluster |

### Easy memory trick

> **HPC = Move → Compute → Connect → Store → Automate**
> 

---

# 3. Data Management & Transfer

Before HPC can process data, you may need to bring **huge amounts of data into AWS**.

## AWS Direct Connect

Provides a **dedicated private network connection** between your on-premises environment and AWS.

```
Corporate Data Center
        │
        │ Dedicated connection
        ↓
AWS Direct Connect
        │
        ↓
       AWS
```

### Use when:

You have **continuous large data transfers** and need:

- High bandwidth
- Consistent network performance
- Private connectivity

### HPC example

A research company continuously sends **TBs of genomic data** from its data center to AWS.

**Think:**

> **DX = continuous high-speed highway to AWS**
> 

---

## AWS Snowball

Physical AWS device used to transfer **large amounts of data offline**.

```
On-Premises
    │
    │ Copy TBs/PBs
    ↓
Snowball
    │
    │ Ship physically
    ↓
AWS
```

### Use when:

Internet transfer would take **weeks/months**.

**Think:**

> **Snowball = physically move the data**
> 

---

## AWS Snowmobile

For **extremely huge datasets**, such as **petabytes/exabytes**.

Think:

> **Snowball = truck/device**
> 

> **Snowmobile = literal giant data-moving vehicle**
> 

---

## AWS DataSync

Managed service for moving large amounts of data between:

- On-premises ↔ S3
- On-premises ↔ EFS
- On-premises ↔ FSx

### Think:

> **DataSync = automated high-speed file transfer**
> 

---

# 4. Compute

## Amazon EC2

HPC commonly uses powerful EC2 instances.

Choose specialized instances depending on workload:

| Workload | EC2 choice |
| --- | --- |
| CPU-heavy | Compute optimized |
| GPU-heavy | GPU instances |
| Memory-heavy | Memory optimized |
| Cost-sensitive | Spot Instances |

### Example

A machine-learning simulation requires massive GPU processing.

Use:

> **GPU-optimized EC2**
> 

---

# 5. Spot Instances for HPC

HPC jobs are often **batch workloads**.

If a job can tolerate interruption, Spot Instances can dramatically reduce cost.

```
HPC Job
   │
   ├── EC2 Spot #1
   ├── EC2 Spot #2
   ├── EC2 Spot #3
   └── EC2 Spot #4
```

### Exam clue

If the question says:

> "HPC workload is fault tolerant / can be interrupted / wants lowest cost"
> 

Think:

**Spot Instances**

### Memory trick

> **HPC + flexible workload + cheapest compute → Spot**
> 

---

# 6. EC2 Cluster Placement Group

This is **very important for SAA-C03**.

A **Cluster Placement Group** places EC2 instances physically close together within an AZ.

```
Same AZ

┌─────────────────────────────┐
│ EC2 │ EC2 │ EC2 │ EC2      │
│     Very close together      │
│      High bandwidth         │
│       Low latency           │
└─────────────────────────────┘
```

### Why?

HPC applications frequently require **very fast communication between EC2 instances**.

### Exam clue

If you see:

- HPC
- Low latency
- High network throughput
- Tightly coupled workloads
- EC2 instances communicating frequently

Think:

> **Cluster Placement Group**
> 

### Memory trick

> **Cluster = EC2s close together**
> 

---

# 7. Enhanced Networking

Enhanced Networking provides:

- Higher bandwidth
- Higher packets per second
- Lower latency

It uses **SR-IOV**.

Two technologies you'll see:

### ENA

**Elastic Network Adapter**

Provides high-performance networking, with modern EC2 instances supporting very high bandwidth.

### Intel 82599 VF

Older/legacy option.

For the exam, remember:

> **ENA = modern high-performance networking**
> 

---

# 8. Elastic Fabric Adapter (EFA)

This is one of the **most important HPC-specific concepts**.

EFA is basically an enhancement for very demanding HPC workloads.

```
EC2 ───── EFA ───── EC2
 │                   │
 └──── EFA ──────────┘
```

It provides:

- Very low latency
- High-performance inter-node communication
- Designed for HPC
- Linux workloads
- Supports MPI
- Helps tightly coupled workloads

### Why is EFA special?

Normal networking involves more of the operating system networking stack.

EFA can **bypass much of the underlying OS networking stack**, reducing communication overhead.

### Exam clue

If you see:

> **HPC + tightly coupled + low latency + MPI**
> 

Think:

# **EFA**

### Memory trick

> **EFA = Extremely Fast communication between EC2 nodes**
> 

---

# 9. EFA vs ENA

| Feature | ENA | EFA |
| --- | --- | --- |
| Purpose | General high-performance networking | HPC |
| Low latency | Yes | **Extremely low latency** |
| HPC | Can help | **Designed for HPC** |
| Inter-node communication | Good | **Excellent** |
| MPI | Not the key feature | **Yes** |
| OS bypass | No | **Yes / reduces OS networking overhead** |
| Exam keyword | High bandwidth / PPS | **HPC / MPI / tightly coupled** |

### Exam shortcut

> **High-performance networking → ENA**
> 

> **HPC + MPI + tightly coupled → EFA**
> 

---

# 10. Storage for HPC

Different workloads need different storage.

| Storage | Best for HPC |
| --- | --- |
| EBS | Persistent block storage |
| Instance Store | Extremely fast temporary local storage |
| S3 | Huge object/data repository |
| EFS | Shared Linux file system |
| FSx for Lustre | **HPC-optimized distributed file system** |

---

# 11. EBS

Amazon EBS provides **persistent block storage** attached to EC2.

Useful when you need:

- Persistent storage
- High IOPS
- Block storage

For HPC, high-performance EBS options such as **io2 Block Express** can provide very high IOPS.

### Think:

> **EBS = persistent high-performance disk**
> 

---

# 12. Instance Store

Instance Store is physically attached to the host running the EC2 instance.

Advantages:

- Extremely low latency
- Very high IOPS
- Very fast

But:

> **Instance Store is temporary/ephemeral.**
> 

If the EC2 instance/underlying host is stopped/terminated in ways that lose the instance store, the data can be lost.

### Exam clue

> "Need extremely fast temporary storage"
> 

Think:

**Instance Store**

### Memory trick

> **Instance Store = Speed over durability**
> 

---

# 13. Amazon S3

S3 is an **object storage service**.

Excellent for:

- Massive datasets
- Data lakes
- Input/output datasets
- Long-term storage

But S3 is **not a traditional file system**.

### Think:

> **S3 = huge data warehouse for objects**
> 

---

# 14. Amazon EFS

EFS is a managed **shared file system**.

Multiple EC2 instances can access the same files.

```
           EFS
        /   |   \
       ↓    ↓    ↓
     EC2   EC2   EC2
```

### Use when:

Multiple Linux EC2 instances need shared files.

### Think:

> **EFS = shared file system**
> 

---

# 15. Amazon FSx for Lustre ⭐

This is **extremely important for HPC questions**.

FSx for Lustre is a **high-performance distributed file system designed for compute-intensive workloads**.

Excellent for:

- HPC
- Machine learning
- Scientific workloads
- High-performance computing
- Large-scale processing

It can integrate with **S3**.

```
                S3
                 │
                 ↓
          FSx for Lustre
                 │
       ┌─────────┼─────────┐
       ↓         ↓         ↓
     EC2       EC2       EC2
```

### Exam clue

If you see:

> HPC + distributed file system + millions of IOPS + high throughput
> 

Think:

# **FSx for Lustre**

### Memory trick

> **Lustre = HPC storage**
> 

---

# 16. Storage Comparison

| Feature | EBS | Instance Store | EFS | S3 | FSx for Lustre |
| --- | --- | --- | --- | --- | --- |
| Type | Block | Local block | File | Object | Distributed file |
| Persistent | ✅ | ❌ | ✅ | ✅ | ✅ |
| Shared by many EC2 | Limited/specific configurations | ❌ | ✅ | ✅ | ✅ |
| Very high performance | ✅ | **⭐⭐⭐** | Good | High throughput | **⭐⭐⭐** |
| HPC specialized | No | Useful | Sometimes | Data repository | **Yes** |
| Best memory | Persistent disk | Fast temporary disk | Shared filesystem | Object storage | **HPC filesystem** |

---

# 17. AWS Batch ⭐

AWS Batch is used to **run batch computing jobs**.

Instead of manually:

```
Create EC2
↓
Configure EC2
↓
Run job
↓
Monitor
↓
Terminate EC2
```

AWS Batch can manage much of this process.

```
Jobs
 │
 ↓
AWS Batch
 │
 ├── EC2
 ├── EC2
 ├── EC2
 └── EC2
```

It can:

- Queue jobs
- Schedule jobs
- Provision compute resources
- Run jobs
- Scale compute resources according to workload

### HPC-specific capability

AWS Batch supports **multi-node parallel jobs**.

One job can span multiple EC2 instances.

### Exam clue

> "Run thousands of batch jobs"
> 

or

> "Automatically provision compute resources for batch jobs"
> 

Think:

**AWS Batch**

---

# 18. AWS ParallelCluster

AWS ParallelCluster is an **open-source cluster management tool** for HPC.

It helps automatically create HPC environments.

It can configure:

- VPC
- Subnets
- EC2 instances
- Cluster configuration
- EFA

### Think:

> **ParallelCluster = Build my HPC cluster for me**
> 

---

# AWS Batch vs ParallelCluster

|  | AWS Batch | AWS ParallelCluster |
| --- | --- | --- |
| Main purpose | Run/manage batch jobs | Build/manage HPC clusters |
| Job scheduling | ⭐⭐⭐ | Not the primary purpose |
| Automatically provisions compute | ✅ | ✅ |
| HPC cluster creation | Not primary | **⭐** |
| Multi-node jobs | ✅ | ✅ |
| EFA | Can be used in suitable setups | **Strong HPC use case** |
| Memory trick | **Run jobs** | **Build cluster** |

### Exam shortcut

**"Schedule/execute batch jobs" → AWS Batch**

**"Deploy/configure an HPC cluster" → ParallelCluster**

---

# 19. Complete HPC Architecture

Here's the big picture:

```
                 ON-PREMISES
                     │
                     │
          ┌──────────┴──────────┐
          │                     │
   Direct Connect          Snowball
   Continuous data       Huge migration
          │                     │
          └──────────┬──────────┘
                     ↓
                    AWS
                     │
                ┌────┴────┐
                │   S3    │
                │ Dataset │
                └────┬────┘
                     │
                     ↓
              FSx for Lustre
              High-performance
              shared filesystem
                     │
          ┌──────────┼──────────┐
          ↓          ↓          ↓
        EC2        EC2        EC2
       Compute    Compute    Compute
          │          │          │
          └────── EFA ──────────┘
               Low latency
             HPC communication
                     │
                     ↓
                AWS Batch
              Job scheduling
```

This architecture captures the **main HPC exam concepts**.

---

# 20. HPC Service Selection — Exam Decision Table

| Question asks for... | Choose |
| --- | --- |
| Dedicated private high-bandwidth connection | **Direct Connect** |
| Move huge data physically | **Snowball** |
| Extremely massive physical data migration | **Snowmobile** |
| Managed large-scale data transfer | **DataSync** |
| Powerful general compute | **EC2** |
| GPU-intensive processing | **GPU EC2** |
| Cheapest interruptible compute | **Spot Instances** |
| EC2s close together | **Cluster Placement Group** |
| High-performance network | **Enhanced Networking / ENA** |
| HPC + MPI + ultra-low latency | **EFA** |
| Persistent block storage | **EBS** |
| Fast temporary local storage | **Instance Store** |
| Object storage | **S3** |
| Shared file system | **EFS** |
| HPC distributed filesystem | **FSx for Lustre** |
| Run/schedule batch jobs | **AWS Batch** |
| Build HPC clusters | **AWS ParallelCluster** |

---

# 21. Most Important SAA-C03 Exam Clues ⭐

Memorize these associations:

| Exam wording | Your brain should say |
| --- | --- |
| **HPC workload** | EC2 + Cluster Placement Group |
| **Tightly coupled workloads** | **EFA** |
| **MPI** | **EFA** |
| **Low-latency inter-node communication** | **EFA** |
| **EC2 instances need to be physically close** | **Cluster Placement Group** |
| **Millions of IOPS distributed filesystem** | **FSx for Lustre** |
| **Shared Linux filesystem** | EFS |
| **Temporary ultra-fast storage** | Instance Store |
| **Persistent block storage** | EBS |
| **Huge object dataset** | S3 |
| **Automatically run batch jobs** | AWS Batch |
| **Create/manage HPC cluster** | ParallelCluster |
| **Continuous huge on-prem → AWS transfer** | Direct Connect |
| **Internet transfer would take months** | Snowball/Snowmobile |
| **Lowest-cost interruptible compute** | Spot Instances |

---

# 🧠 Final HPC Memory Map

Don't memorize 20 separate services. Think in **5 questions**:

```
                    HPC
                     │
       ┌─────────────┼──────────────┐
       ↓             ↓              ↓
     MOVE          COMPUTE       CONNECT
       │             │              │
    DX/Snow       EC2/Spot      Cluster PG
    DataSync      GPU            ENA/EFA
       │
       ↓
     STORE
       │
 ┌─────┼─────────────┐
 ↓     ↓             ↓
S3    EBS       FSx for Lustre
             ⭐ HPC storage
       │
       ↓
    AUTOMATE
       │
 ┌─────┴──────┐
 ↓            ↓
Batch    ParallelCluster
Jobs      HPC Cluster
```

### ⭐ The 7 things I'd prioritize for SAA-C03

1. **EFA → HPC + MPI + tightly coupled + low latency**
2. **FSx for Lustre → HPC high-performance distributed filesystem**
3. **Cluster Placement Group → EC2s physically close**
4. **AWS Batch → run/schedule batch jobs**
5. **ParallelCluster → create/manage HPC clusters**
6. **Spot → cheap, interruptible HPC compute**
7. **Direct Connect / Snowball → get huge datasets into AWS**

**One-line exam takeaway:**

> **HPC = powerful EC2 + Cluster Placement Group + EFA + FSx for Lustre + Batch/ParallelCluster, with DX/DataSync/Snowball for moving massive datasets.**
>
