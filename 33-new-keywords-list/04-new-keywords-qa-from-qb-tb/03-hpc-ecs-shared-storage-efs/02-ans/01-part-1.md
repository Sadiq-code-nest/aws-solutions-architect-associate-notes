# AWS SAA-C03 Post-Mortem — HPC + ECS Shared Storage

## 1. Restate the Scenario in Plain English

A company is building a **High-Performance Computing (HPC)** workload using ECS.

The important part is that **hundreds of ECS tasks run concurrently**, and those tasks need to access the **same storage system** with lots of reads and writes.

### Actors

| Actor | Role |
| --- | --- |
| Global users | Frequently access the application |
| ECS cluster | Runs the containerized workloads |
| ECS tasks | Hundreds of concurrent HPC/image-processing jobs |
| ECS Capacity Provider | Automatically manages EC2 capacity for ECS tasks |
| Shared storage | Must be accessible concurrently by many ECS tasks |
| Output data | ~10 MB per task |
| Total storage | ≤ 10 TB |

The key requirement is **not simply "store data."**

It's:

> **Hundreds of ECS tasks → shared filesystem → frequent high-frequency reads/writes.**
> 

### Current architecture

```
                    Global Users
                         │
                         ▼
                 ┌───────────────┐
                 │  ECS Cluster  │
                 │  EC2 Launch   │
                 └───────┬───────┘
                         │
              ECS Capacity Provider
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
       Task 1          Task 2         Task 3
          │              │              │
          └──────────────┼──────────────┘
                         │
                    ??? Storage
```

The missing piece is a storage service that supports:

- **shared access**
- **many concurrent ECS tasks**
- **high throughput**
- **high-frequency reads/writes**
- **Linux/container-friendly filesystem access**
- approximately **10 TB or less**

### Desired architecture

```
                    ECS Cluster
                         │
       ┌─────────────────┼─────────────────┐
       ▼                 ▼                 ▼
    ECS Task           ECS Task          ECS Task
       │                 │                 │
       └─────────────────┼─────────────────┘
                         │
                         ▼
                    Amazon EFS
                         │
             Shared filesystem
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
           Files       Files       Files
```

---

# 2. Identify the Hidden Sub-Problems

There are several clues, but they boil down to one major architectural decision: **which shared filesystem is appropriate?**

| Symptom / Requirement | Underlying Category | Why |
| --- | --- | --- |
| Hundreds of ECS tasks | **Highly concurrent shared storage** | Many compute workers need simultaneous access |
| Tasks need shared storage | **Network file system** | Local EC2/EBS storage isn't naturally shared across many instances |
| High-frequency reads/writes | **High-throughput filesystem** | Storage must handle concurrent I/O |
| ECS EC2 launch type | **ECS-compatible network filesystem** | EFS can be mounted into ECS tasks |
| ≤ 10 TB | **EFS is suitable** | EFS scales automatically without fixed volume sizing |
| HPC workload | **High-performance filesystem mode** | Need to consider EFS performance mode |
| Global users | **Globally accessible application**, but not necessarily multi-Region storage | Don't automatically interpret "global users" as Global Tables or multi-Region storage |
| 10 MB output/task | **Moderate individual file size** | Doesn't justify a database or block storage architecture |
| Shared files | **Filesystem semantics** | DynamoDB is not a filesystem |

### The biggest clue

> **"hundreds of ECS tasks" + "high-frequency read and write operations" + "shared storage system"**
> 

Think:

```
Many compute workers
        +
Shared files
        +
Concurrent I/O
        ↓
   Network filesystem
        ↓
       EFS
```

Now the real question becomes:

> **EFS General Purpose or EFS Max I/O?**
> 

That is where the exam's performance-mode clue matters.

---

# 3. Explain Every Option

---

## Option A — DynamoDB + DAX + Streams

> Launch a DynamoDB table with DAX and DynamoDB Streams and use it as the container mount point.
> 

### What is it trying to say?

Use DynamoDB as if it were a shared filesystem for the ECS tasks.

```
              ECS Tasks
          ┌─────┼─────┐
          ▼     ▼     ▼
        Task  Task  Task
          │     │     │
          └─────┼─────┘
                ▼
           DynamoDB
             + DAX
             + Streams
```

### Verdict: ❌ Incorrect

This fundamentally misunderstands DynamoDB.

DynamoDB is a **NoSQL database**, not a filesystem.

You cannot do:

```
DynamoDB
   ↓
ECS container mount point
```

in the same way you mount EFS.

A container mount point expects a filesystem.

For example:

```
/app/data
    │
    ▼
EFS filesystem
```

DynamoDB instead works through API operations:

```
GetItem
PutItem
UpdateItem
Query
Scan
```

### Why DAX doesn't fix it

DAX is a DynamoDB cache.

```
Application
    │
    ▼
   DAX
    │
    ▼
DynamoDB
```

It doesn't transform DynamoDB into a filesystem.

### Why DynamoDB Streams doesn't fix it

Streams capture item-level changes:

```
DynamoDB
    │
    ▼
DynamoDB Streams
    │
    ▼
Lambda / consumer
```

They are useful for event-driven processing, replication patterns, auditing, etc.

They do **not** provide shared filesystem storage.

### Exam takeaway

> **If the question says "mount," "filesystem," "shared files," or "container mount point," DynamoDB should immediately become suspicious.**
> 

---

# Option B — EFS Bursting + General Purpose

> Launch EFS with Bursting Throughput and General Purpose performance mode and mount it into the ECS tasks.
> 

### What is it trying to say?

Use EFS as the shared filesystem:

```
              ECS Cluster
       ┌────────┼────────┐
       ▼        ▼        ▼
     Task 1   Task 2   Task 3
       │        │        │
       └────────┼────────┘
                ▼
          Amazon EFS
       General Purpose
       Bursting Throughput
```

### Verdict: ⚠️ Partially valid, but not best

This is much closer.

**EFS itself is absolutely the correct family of service.**

The problem is the **performance mode**.

The scenario explicitly emphasizes:

> **hundreds of ECS tasks**
> 

and:

> **high-frequency read and write operations**
> 

This is the sort of workload where **Max I/O** is intended to provide higher aggregate throughput and support highly parallel workloads.

### General Purpose

EFS General Purpose provides lower latency and is the default/recommended performance mode for most workloads.

But the question specifically describes a highly parallel HPC workload.

### Bursting Throughput

Bursting Throughput provides throughput that scales based on the amount of data stored in the filesystem.

That's potentially important here because the filesystem is only around:

```
≤ 10 TB
```

But the bigger clue is **highly parallel I/O from hundreds of tasks**.

For the exam's intended answer, **Max I/O + Provisioned Throughput** is the stronger match.

---

# Option C — FSx for Windows File Server

> Create an SMB file share using Amazon FSx for Windows File Server and mount it into ECS.
> 

### What is it trying to say?

Create a Windows-oriented shared filesystem:

```
              ECS Tasks
          ┌─────┼─────┐
          ▼     ▼     ▼
        Task  Task  Task
          │     │     │
          └─────┼─────┘
                ▼
        FSx for Windows
          SMB File Share
```

### Verdict: ❌ Incorrect

FSx for Windows File Server is excellent when you specifically need:

- Windows file systems
- SMB
- Windows applications
- Active Directory integration
- Windows-native file sharing

But this question describes an **ECS HPC workload**, without any Windows/SMB requirement.

The more natural shared filesystem for Linux-based ECS workloads is EFS.

### The exam clue

If you see:

> **SMB + Windows + Active Directory**
> 

think:

```
FSx for Windows File Server
```

If you see:

> **Linux + NFS + shared filesystem + ECS**
> 

think:

```
EFS
```

---

# Option D — EFS Provisioned Throughput + Max I/O

> Launch EFS with Provisioned Throughput and Max I/O performance mode and mount it into the ECS tasks.
> 

### What is it trying to say?

Use EFS specifically configured for a highly parallel workload.

```
                     ECS Cluster
                         │
        ┌────────────────┼────────────────┐
        ▼                ▼                ▼
      Task 1           Task 2           Task 3
        │                │                │
        ├────────────────┼────────────────┤
        │        Shared filesystem        │
        └────────────────┼────────────────┘
                         ▼
                 Amazon EFS
                ┌─────────────┐
                │   Max I/O   │
                │  Provisioned│
                │  Throughput │
                └─────────────┘
```

### Verdict: ✅ Correct

This is the intended answer.

### Why?

The question deliberately gives us:

> **hundreds of ECS tasks**
> 

and:

> **high-frequency read and write operations**
> 

This points toward a filesystem designed for **highly parallel access**.

And:

> **shared storage system**
> 

immediately points toward EFS rather than DynamoDB.

### Why Provisioned Throughput?

Provisioned Throughput lets you specify the throughput independently of the amount of data stored.

This is useful when:

```
Small/moderate dataset
        +
Very high I/O requirements
```

For example:

```
Storage = 2 TB
but
Throughput requirement = very high
```

You don't want your throughput capability to be constrained simply because the dataset isn't enormous.

That's an important clue in this question.

# 4. Deep-Dive Every AWS Concept Touched

---

## 4.1 Amazon ECS

### Definition

Amazon ECS is a managed container orchestration service that runs and manages Docker/container workloads.

```
ECS Cluster
    │
    ├── Task 1
    ├── Task 2
    ├── Task 3
    └── Task N
```

Here, the question uses the **EC2 launch type**.

```
ECS
 │
 └── EC2 launch type
         │
         ▼
   EC2 instances
         │
         ▼
    ECS tasks
```

### Example

An image-processing system could run:

```
100 ECS tasks
     │
     ├── process image A
     ├── process image B
     ├── process image C
     └── ...
```

### ECS vs Lambda

| ECS | Lambda |
| --- | --- |
| Container workloads | Functions |
| Long-running workloads possible | Event/function execution |
| More control over runtime | More abstracted |
| Excellent for containerized HPC/batch workloads | Excellent for event-driven workloads |

### Exam trigger

> **"Containers + task definitions + cluster + service" → ECS**
> 

---

# 4.2 ECS EC2 Launch Type

### Definition

With ECS EC2 launch type, you manage the underlying EC2 instances that provide compute capacity for ECS tasks.

```
ECS Cluster
    │
    ▼
EC2 instances
 ┌──────┬──────┬──────┐
 │      │      │      │
Task   Task   Task   Task
```

### Example

Suppose:

```
20 EC2 instances
↓
200 ECS tasks
```

The tasks consume CPU/memory from those instances.

### EC2 launch type vs Fargate

| EC2 launch type | Fargate |
| --- | --- |
| You manage EC2 capacity | AWS manages underlying servers |
| More infrastructure control | Less operational overhead |
| Can use custom capacity strategy | Serverless containers |
| Useful for specialized workloads | Great for simpler container deployment |

### Exam trigger

> **"ECS tasks running on EC2 instances" → ECS EC2 launch type**
> 

---

# 4.3 ECS Capacity Providers

### Definition

ECS Capacity Providers connect ECS task placement to the underlying compute capacity and can help automatically manage capacity according to task demand.

```
ECS Tasks
   │
   ▼
Capacity Provider
   │
   ▼
EC2 Capacity
   │
   ├── Scale out
   └── Scale in
```

### Example

Traffic/job demand increases:

```
50 tasks
  ↓
100 tasks
  ↓
200 tasks
```

The capacity provider strategy can work with Auto Scaling to make additional EC2 capacity available.

### Capacity Provider vs Auto Scaling Group

| Capacity Provider | Auto Scaling Group |
| --- | --- |
| ECS-aware capacity management | EC2 capacity management |
| Works with ECS task demand | Scales EC2 instances |
| Connects ECS with compute capacity | Provides actual EC2 fleet |
| ECS concept | EC2 scaling concept |

### Exam trigger

> **"ECS dynamically manages EC2 capacity based on task demand" → Capacity Provider**
> 

---

# 4.4 Amazon EFS

### Definition

Amazon EFS is a managed, elastic NFS filesystem that can be mounted concurrently by multiple compute resources. It automatically grows and shrinks as files are added or removed.

```
             EFS
              │
      ┌───────┼────────┐
      ▼       ▼        ▼
    ECS 1   ECS 2    ECS 3
      │       │        │
      └───────┼────────┘
              │
         Shared files
```

This is exactly what the question needs.

### Worked example

Imagine 200 image-processing tasks:

```
Task 1 ─┐
Task 2 ─┤
Task 3 ─┤
...     ├──→ EFS
Task 200┘
```

Each task can access shared files.

### EFS vs EBS

| EFS | EBS |
| --- | --- |
| File storage | Block storage |
| Shared across many instances | Normally attached to one EC2 instance at a time |
| NFS | Block device |
| Elastic storage | Fixed volume size |
| Excellent shared filesystem | Excellent EC2 disk |
| Multiple clients | Primarily single EC2 attachment |

### Exam trigger

> **"Multiple EC2/ECS instances need simultaneous access to the same filesystem" → EFS**
> 

---

# 4.5 EFS Performance Modes

EFS has performance modes that determine how the filesystem is optimized for workload characteristics.

The important exam distinction here is:

```
General Purpose
        vs
Max I/O
```

### General Purpose

Designed for most workloads and provides lower per-operation latency.

```
General Purpose
      ↓
Lower latency
      ↓
Most common workloads
```

### Max I/O

Designed for workloads requiring high levels of aggregate throughput and concurrency.

```
Max I/O
   ↓
Many clients
   +
Highly parallel workload
   +
High aggregate throughput
```

### Comparison

| General Purpose | Max I/O |
| --- | --- |
| Lower latency | Optimized for higher aggregate throughput |
| Default choice | Highly parallel workloads |
| Most workloads | HPC/high concurrency scenarios |
| Lower operation latency | Potentially higher latency |
| Good general workload | Many concurrent clients |

### Exam trigger

> **"Hundreds/thousands of clients + HPC + highly parallel filesystem operations" → Max I/O**
> 

---

# 4.6 EFS Throughput Modes

The question also tests **throughput mode**.

Two important choices here are:

```
Bursting
Provisioned
```

### Bursting Throughput

Throughput is related to the amount of data stored in the filesystem, with the ability to burst above baseline under the EFS throughput model.

Conceptually:

```
More stored data
      ↓
More baseline throughput
```

### Provisioned Throughput

You specify the throughput independently from the amount of data stored.

```
Small dataset
      +
High throughput requirement
      ↓
Provisioned Throughput
```

That's particularly useful when you have:

```
10 TB or less
+
very intensive I/O
```

### Comparison

| Bursting Throughput | Provisioned Throughput |
| --- | --- |
| Throughput linked to storage size/baseline model | Throughput specified independently |
| Can burst | Predictable configured throughput |
| Good when storage size and throughput naturally align | Good when throughput demand is high relative to storage size |
| Simpler/cost-effective for many workloads | Useful for throughput-intensive workloads |

### Exam trigger

> **"Small dataset but consistently high throughput requirement" → Provisioned Throughput**
> 

---

# 4.7 FSx for Windows File Server

### Definition

Amazon FSx for Windows File Server provides fully managed Windows file systems accessible through the SMB protocol.

```
Windows Applications
        │
        ▼
   SMB protocol
        │
        ▼
FSx for Windows
```

### Example

A company has:

```
Windows Server
Active Directory
SMB file shares
Windows applications
```

FSx for Windows is a natural fit.

### FSx Windows vs EFS

| EFS | FSx for Windows |
| --- | --- |
| NFS | SMB |
| Linux-oriented | Windows-oriented |
| Shared file system | Shared Windows file system |
| Excellent AWS/Linux workloads | Excellent Windows/SMB workloads |
| ECS Linux common choice | Windows file workloads |

### Exam trigger

> **"Windows + SMB + Active Directory" → FSx for Windows File Server**
>
