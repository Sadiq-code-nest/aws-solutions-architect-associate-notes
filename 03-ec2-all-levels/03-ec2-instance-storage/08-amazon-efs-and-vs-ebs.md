# Amazon EFS — Complete SAA-C03 Exam Note

## 1. What is Amazon EFS?

**Amazon EFS (Elastic File System)** is a **managed, elastic NFS file system** that multiple EC2 instances can mount at the same time.

Think:

> **EFS = one shared network folder for many Linux servers.**
> 

Unlike EBS, you don't normally attach EFS as a block disk to one EC2 instance. Instead, EC2 instances **mount the same file system** over the network.

### Simple picture

```
             Amazon EFS
          Shared File System
                 │
       ┌─────────┼─────────┐
       │         │         │
      EC2       EC2       EC2
      AZ-A      AZ-B      AZ-C
       │         │         │
       └──── All can access ────┘
```

### Why does EFS exist?

Suppose you have 100 Linux web servers.

They all need access to:

```
/var/www/uploads/
```

With EBS, each server would have its own separate volume.

With EFS:

```
100 EC2 instances
       ↓
   One EFS
       ↓
 shared files
```

All servers can access the same files.

---

# 2. EFS Core Characteristics

| Feature | EFS |
| --- | --- |
| Storage type | **File storage** |
| Protocol | **NFSv4.1** |
| Access | Multiple EC2 instances simultaneously |
| OS | Primarily **Linux/POSIX** workloads |
| Scope | Regional |
| AZ support | Can be accessed across multiple AZs |
| Scaling | Automatically scales |
| Capacity planning | No manual capacity provisioning |
| Pricing | Pay for storage used |
| Encryption at rest | AWS KMS |
| Network security | Security Groups |
| Common use | Shared files |
| Typical protocol clue | **NFS** |

### 🧠 Memory trick

> **EFS = Elastic File Sharing**
> 

The **F** in EFS → **File**.

---

# 3. EFS vs EBS — Most Important Comparison

This is one of the most common SAA-C03 confusion points.

|  | **EBS** | **EFS** |
| --- | --- | --- |
| Storage type | Block | File |
| Typical use | Disk for one EC2 | Shared file system |
| Multiple EC2? | Generally no; **io1/io2 Multi-Attach** is an exception | **Yes** |
| Across AZs | No, volume is AZ-specific | **Yes** |
| Protocol | Block device | **NFS** |
| Scaling | Resize volume | **Automatically scales** |
| Best for | OS disk, database, applications | Shared files, web content |
| Windows | Yes | Not the standard choice; EFS is for Linux |
| Performance model | Size/IOPS/throughput depending on volume | Performance/throughput modes |
| Cost | Generally cheaper | Generally more expensive |
| Capacity planning | Provision capacity | **No capacity planning** |

### Exam trigger

**"Multiple EC2 instances need simultaneous access to the same files"**

→ **EFS**

**"Linux instances across multiple AZs need a shared file system"**

→ **EFS**

**"Boot volume / database disk attached to EC2"**

→ **EBS**

---

# 4. EFS Architecture

EFS is a **regional file system**.

You create the EFS file system, then use **mount targets** inside your VPC.

```
                 AWS Region
                     │
             ┌────── EFS ──────┐
             │                  │
           Mount              Mount
           Target             Target
             │                  │
            AZ-A               AZ-B
             │                  │
           EC2                 EC2
```

For production, you normally create mount targets in the AZs where your EC2 instances run.

### Exam clue

> **"EC2 instances in multiple Availability Zones access the same file system."**
> 

Think:

**EFS**

---

# 5. EFS Mount Targets

A **mount target** provides the network endpoint through which EC2 instances access EFS from a particular AZ.

The EC2 instance connects to the EFS mount target using NFS.

Example:

```
EC2 in AZ-A
    ↓
EFS Mount Target in AZ-A
    ↓
EFS

EC2 in AZ-B
    ↓
EFS Mount Target in AZ-B
    ↓
EFS
```

### Important exam idea

Don't confuse:

**EFS file system**

with

**EFS mount target**

The file system is the shared storage.

The mount target provides network access to that file system from the VPC/AZ.

---

# 6. EFS Security

EFS uses **network security controls**.

The major exam concept:

> **Security Groups control access to EFS mount targets.**
> 

For example:

```
EC2 Security Group
       │
       │ TCP 2049
       ▼
EFS Mount Target
```

### NFS port

**TCP 2049**

This is a very useful exam number to remember.

### 🧠 Memory trick

> **NFS → 2049**
> 

If the question says:

> "EC2 cannot mount EFS"
> 

Check:

- VPC connectivity
- Mount target
- Security Group
- **TCP 2049**
- IAM/EFS authorization where applicable

---

# 7. EFS Encryption

EFS supports encryption **at rest** using AWS KMS.

You can configure encryption when creating the file system.

```
Application
    ↓
EFS
    ↓
Encrypted storage
    ↓
KMS
```

You can also use encryption **in transit** when mounting EFS using TLS.

### Remember

| Protection | EFS |
| --- | --- |
| Encryption at rest | **KMS** |
| Encryption in transit | **TLS** |

### Exam clue

> **"Sensitive shared files must be encrypted at rest."**
> 

→ EFS encryption with **KMS**

---

# 8. EFS Performance Modes

There are two important performance modes:

### General Purpose

Default choice.

Designed for:

- latency-sensitive applications
- web serving
- CMS
- WordPress
- general file workloads

Think:

> **General Purpose = low latency**
> 

---

### Max I/O

Designed for:

- highly parallel workloads
- big data
- media processing
- workloads involving many concurrent clients

Tradeoff:

> Higher latency in exchange for greater scalability/parallelism.
> 

### 🧠 Memory trick

**General Purpose → latency**

**Max I/O → parallel workloads**

---

# 9. EFS Throughput Modes

This is different from **Performance Mode**.

This distinction is important.

### Performance Mode

Controls characteristics such as latency and parallelism.

### Throughput Mode

Controls **how throughput is provided**.

The major modes to know:

- **Bursting**
- **Provisioned**
- **Elastic**

---

## Bursting Throughput

Throughput is related to the amount of data stored and can burst above the baseline.

Good for workloads where:

> workload occasionally needs more throughput.
> 

---

## Provisioned Throughput

You explicitly provision the desired throughput independently of the amount of storage.

Example:

```
EFS storage = 1 TB

Provisioned throughput = 1 GiB/s
```

Useful when the application needs predictable throughput regardless of how much data is stored.

---

## Elastic Throughput ⭐

EFS automatically adjusts throughput based on workload demand.

Excellent for:

> **unpredictable workloads**
> 

Think:

```
Low traffic
   ↓
Lower throughput

Traffic spikes
   ↓
Higher throughput
```

### 🧠 Exam shortcut

| Requirement | Think |
| --- | --- |
| Normal workload | Bursting |
| Need predetermined throughput | Provisioned |
| **Unpredictable workload** | **Elastic** |

---

# 10. EFS Storage Classes

EFS has different storage tiers based on access frequency.

The basic idea:

```
Frequently accessed
        ↓
EFS Standard
        ↓
Less frequently accessed
        ↓
EFS-IA
        ↓
Rarely accessed
        ↓
Archive
```

---

## EFS Standard

For frequently accessed files.

Example:

```
Active website files
```

---

## EFS Infrequent Access — EFS-IA

For files that aren't accessed frequently.

Storage cost is lower, but retrieving/accessing files has an additional access charge.

Example:

```
Old customer reports
occasionally accessed
```

---

## EFS Archive

For very rarely accessed files.

Useful when files may only be accessed a few times per year.

### 🧠 Memory trick

> **Standard = active**
> 

> **IA = occasionally needed**
> 

> **Archive = almost never needed**
> 

---

# 11. EFS Lifecycle Management

EFS can automatically move files between storage classes based on how long they haven't been accessed.

Example:

```
File uploaded
      ↓
EFS Standard
      ↓
Not accessed for X days
      ↓
EFS IA
      ↓
Very rarely accessed
      ↓
Archive
```

This is useful for reducing storage costs.

### Exam trigger

> **"Automatically move infrequently accessed EFS files to a cheaper storage tier."**
> 

→ **EFS lifecycle management**

---

# 12. EFS One Zone

Standard EFS provides multi-AZ availability.

But EFS also has **One Zone** storage.

```
Standard

AZ-A ──┐
AZ-B ──┼── EFS
AZ-C ──┘
```

One Zone:

```
             EFS
              │
             AZ-A
```

### Why use One Zone?

When:

- workload doesn't require multi-AZ storage
- cost optimization is important
- data can tolerate AZ-level failure
- development/test or suitable backup data

### One Zone-IA

There is also an IA tier for One Zone.

### Exam clue

> **"Lowest-cost EFS storage and data can tolerate AZ failure."**
> 

→ **EFS One Zone**

---

# 13. EFS Availability vs EBS Availability

Very important.

### EBS

An EBS volume belongs to **one AZ**.

```
AZ-A
 │
 EBS
 │
EC2
```

To move it to another AZ:

```
EBS
 ↓
Snapshot
 ↓
Restore in another AZ
 ↓
New EBS volume
```

### EFS

EFS is designed as a **regional shared file system** and can be mounted by EC2 instances across AZs.

```
AZ-A ──┐
AZ-B ──┼── EFS
AZ-C ──┘
```

### Exam shortcut

> **EBS = AZ-bound**
> 

> **EFS = regional shared file system**
> 

---

# 14. EFS Use Cases

## Use Case 1 — WordPress

Multiple web servers need the same website files.

```
ALB
 │
 ├── EC2
 ├── EC2
 └── EC2
       │
       ▼
      EFS
```

All instances access:

```
/wp-content/
```

Perfect EFS scenario.

---

## Use Case 2 — Shared application files

Several Linux servers need access to:

```
/config/
/uploads/
/documents/
```

→ EFS

---

## Use Case 3 — Content Management

A CMS runs on multiple EC2 instances and all instances need the same media files.

→ EFS

---

## Use Case 4 — Data sharing

Multiple Linux EC2 instances need to read/write a common directory.

→ EFS

---

## Use Case 5 — Multi-AZ application

Application servers are distributed across:

```
AZ-A
AZ-B
AZ-C
```

but need shared file storage.

→ EFS

---

# 15. EFS vs Instance Store

Another important distinction.

|  | EFS | Instance Store |
| --- | --- | --- |
| Type | Network file system | Local block storage |
| Shared | Yes | No |
| Persistent after instance termination | Yes | **No** |
| Multi-AZ | Yes | No |
| Performance | Network-based | Very fast local storage |
| Best for | Shared persistent files | Temporary/high-speed local data |

### Exam clue

> **"Data can be lost when EC2 terminates but extremely high local disk performance is needed."**
> 

→ **Instance Store**

> **"Shared persistent files across many EC2 instances."**
> 

→ **EFS**

---

# 16. EFS vs S3

Don't confuse these.

|  | **EFS** | **S3** |
| --- | --- | --- |
| Storage model | File system | Object storage |
| Protocol | NFS | HTTP/API |
| Mount as file system | Yes | Not normally like EFS |
| Shared by Linux EC2 | Yes | Via APIs/tools |
| Directory/file semantics | Yes | Object/key semantics |
| Best for | Shared POSIX files | Objects, backups, static assets |
| Automatic scaling | Yes | Yes |
| Typical use | WordPress shared filesystem | Images, backups, data lake |

### Exam clue

**"Need POSIX file system / NFS / mount / shared directory"**

→ **EFS**

**"Need object storage / massive durable storage / static files"**

→ **S3**

---

# 17. EFS Throughput vs Performance Mode — Don't Mix Them

This is a classic confusion.

### Performance Mode asks:

> **How does EFS behave under different access patterns?**
> 
- General Purpose
- Max I/O

### Throughput Mode asks:

> **How much throughput should EFS provide and how should it scale?**
> 
- Bursting
- Provisioned
- Elastic

### 🧠 Super memory trick

> **Performance = latency/parallelism**
> 

> **Throughput = MB/s capacity**
> 

---

# 18. EFS Access Across AZs

Suppose:

```
        ALB
         │
   ┌─────┼─────┐
   ▼     ▼     ▼
 EC2   EC2   EC2
 AZ-A  AZ-B  AZ-C
   │     │     │
   └─────┼─────┘
         ▼
        EFS
```

All application servers can access the same files.

This is one of the strongest EFS scenarios in SAA-C03.

---

# 19. EFS and Auto Scaling

EFS works very well with Auto Scaling.

Imagine:

```
Normal:
3 EC2 instances

Traffic spike:
10 EC2 instances
```

Every new instance can mount the same EFS.

You don't need to copy files manually between instances.

### Exam trigger

> **"Auto Scaling web servers require a shared filesystem."**
> 

→ **EFS**

---

# 20. EFS and Containers

EFS can also be used with container workloads such as ECS/EKS where shared persistent file storage is required.

Example:

```
ECS Tasks
 ├── Task 1
 ├── Task 2
 └── Task 3
       │
       ▼
      EFS
```

Useful when multiple tasks need shared files.

---

# 21. EFS Backup

EFS can be backed up using **AWS Backup**.

This is useful when the requirement is:

> protect the EFS file system and recover data later.
> 

Don't confuse this with:

> **EFS itself being the backup service.**
> 

EFS = storage.

AWS Backup = backup management.

---

# 22. EFS Access Control

There are multiple layers you may encounter.

### Network layer

Security Groups control traffic to EFS.

NFS:

```
TCP 2049
```

### File-system authorization

EFS supports IAM-based authorization for clients using appropriate EFS features.

### File permissions

Because EFS is POSIX-based, Linux permissions also matter.

Think:

```
Security Group
      ↓
Can network connection reach EFS?

IAM authorization
      ↓
Is the client authorized?

POSIX permissions
      ↓
Can this user/process access the file?
```

---

# 23. EFS POSIX File System

EFS behaves like a normal Linux file system.

Applications can work with:

```
/read
/write
/create
/delete
```

using normal file-system APIs.

This is why EFS is excellent for applications that expect a **shared POSIX filesystem**.

### Exam trigger

> **"Application requires a POSIX-compatible shared filesystem."**
> 

→ **EFS**

---

# 24. EFS and Windows

For SAA-C03, remember:

> **EFS is primarily for Linux/POSIX workloads.**
> 

If the question specifically says:

> **Windows file shares / SMB / Active Directory**
> 

Think:

→ **Amazon FSx for Windows File Server**

Not EFS.

---

# 25. EFS vs FSx for Windows

| Requirement | Correct choice |
| --- | --- |
| Linux shared NFS filesystem | **EFS** |
| Windows SMB filesystem | **FSx for Windows File Server** |
| Active Directory integration | **FSx for Windows** |
| POSIX/Linux semantics | **EFS** |

### 🧠 Exam hack

> **Linux → EFS**
> 

> **Windows + SMB → FSx for Windows**
> 

---

# 26. EFS Cost Optimization

EFS can become expensive for data that is rarely accessed.

Instead of keeping everything in Standard:

```
All files → EFS Standard
```

you can use lifecycle management:

```
Active
 ↓
Standard

Inactive
 ↓
IA

Rarely accessed
 ↓
Archive
```

### Exam clue

> **"Reduce EFS storage cost for infrequently accessed files."**
> 

→ Lifecycle management + IA/Archive tiers.

---

# 27. EFS Exam Scenario Recognition

| Question wording | Think |
| --- | --- |
| **Multiple EC2 instances access same files** | EFS |
| **Shared filesystem** | EFS |
| **Linux shared storage** | EFS |
| **NFS** | EFS |
| **POSIX filesystem** | EFS |
| **Across multiple AZs** | EFS |
| **Auto Scaling servers need common files** | EFS |
| **WordPress shared uploads** | EFS |
| **Content management shared files** | EFS |
| **Automatically scales filesystem** | EFS |
| **No capacity planning** | EFS |
| **Infrequently accessed EFS data** | EFS-IA |
| **Rarely accessed EFS data** | EFS Archive |
| **Lowest-cost EFS + AZ failure acceptable** | EFS One Zone |
| **Unpredictable throughput** | EFS Elastic Throughput |
| **Low-latency EFS workload** | General Purpose Performance |
| **Highly parallel workload** | Max I/O |
| **Windows + SMB** | FSx for Windows |
| **Single EC2 disk** | EBS |
| **Very fast temporary local disk** | Instance Store |
| **Object storage** | S3 |

---

# 28. EFS Master Comparison Table

| Feature | **EFS Standard** | **EFS One Zone** | **EFS-IA** | **EFS Archive** |
| --- | --- | --- | --- | --- |
| Access pattern | Frequent | Frequent | Infrequent | Rare |
| AZ design | Multi-AZ | Single AZ | Multi-AZ | Depends on underlying EFS configuration |
| Storage cost | Higher | Lower | Lower | Lowest tier |
| Retrieval/access cost | Normal | Normal | Access charge | Access charge |
| Best for | Production active files | Cost-sensitive workloads | Infrequent files | Rarely accessed files |
| Typical clue | **Production shared files** | **Lowest-cost EFS + AZ tolerance** | **Infrequent access** | **Rarely accessed** |

---

# 29. EFS Master Performance Table

| Category | Option | Best scenario | Exam trigger |
| --- | --- | --- | --- |
| Performance | **General Purpose** | Latency-sensitive application | **LOW LATENCY** |
| Performance | **Max I/O** | Highly parallel workloads | **HIGH PARALLELISM** |
| Throughput | **Bursting** | Normal workloads with occasional bursts | **BURST** |
| Throughput | **Provisioned** | Known/predictable throughput requirement | **SPECIFIC THROUGHPUT** |
| Throughput | **Elastic** | Unpredictable workload | **UNPREDICTABLE** |

---

# 30. EFS vs EBS vs Instance Store — Final Brain Table

| Requirement | Pick |
| --- | --- |
| **Shared files across many EC2** | 🟢 **EFS** |
| **Linux NFS filesystem** | 🟢 **EFS** |
| **POSIX filesystem** | 🟢 **EFS** |
| **Single EC2 persistent disk** | 🔵 **EBS** |
| **EC2 boot volume** | 🔵 **EBS** |
| **Database block storage** | 🔵 **EBS** |
| **Extremely fast temporary local storage** | 🟠 **Instance Store** |
| **Data must survive EC2 termination** | 🔵 **EBS / EFS** |
| **Data can disappear when EC2 terminates** | 🟠 **Instance Store** |
| **Object storage** | 🟣 **S3** |
| **Windows + SMB** | 🟡 **FSx for Windows** |

---

# 🔥 Final EFS Exam Hacks

### Hack 1

> **"Many EC2 instances + same files" → EFS**
> 

### Hack 2

> **"NFS" → EFS**
> 

### Hack 3

> **"POSIX" → EFS**
> 

### Hack 4

> **"Linux shared filesystem across AZs" → EFS**
> 

### Hack 5

> **"Windows + SMB + AD" → FSx for Windows**
> 

### Hack 6

> **"One EC2 + block storage" → EBS**
> 

### Hack 7

> **"Temporary + ultra-fast local storage" → Instance Store**
> 

### Hack 8

> **"EFS low latency" → General Purpose**
> 

### Hack 9

> **"EFS highly parallel" → Max I/O**
> 

### Hack 10

> **"EFS unpredictable throughput" → Elastic Throughput**
> 

### Hack 11

> **"Automatically move inactive files to cheaper tier" → EFS Lifecycle Management**
> 

### Hack 12

> **"NFS cannot mount" → Check Security Group TCP 2049**
> 

---

## ⚡ 30-Second Pre-Exam Memory

```
                    STORAGE
                       │
       ┌───────────────┼────────────────┐
       │               │                │
      S3              EBS              EFS
   OBJECT            BLOCK             FILE
       │               │                │
       │          EC2 disk        MANY EC2s
       │          ONE AZ          SHARED FILES
       │               │                │
       │          Persistent          NFS
       │                            Linux/POSIX
       │
       └─────────────────────────────────────

EFS:
Multi-AZ shared filesystem
        ↓
General Purpose = LOW LATENCY
Max I/O = HIGH PARALLELISM

Bursting = normal + burst
Provisioned = predictable throughput
Elastic = UNPREDICTABLE throughput

Standard = frequent
IA = infrequent
Archive = rare

Linux + NFS → EFS
Windows + SMB → FSx
Single EC2 disk → EBS
Temporary local high-speed → Instance Store
```

**Ultimate mnemonic:**

> 🟢 **EFS = Elastic + File + Shared**
> 
> 
> 🔵 **EBS = Block + EC2 + AZ-bound**
> 
> 🟠 **Instance Store = Local + Temporary + Fast**
> 
> 🟣 **S3 = Object + Massive + Durable**
>
