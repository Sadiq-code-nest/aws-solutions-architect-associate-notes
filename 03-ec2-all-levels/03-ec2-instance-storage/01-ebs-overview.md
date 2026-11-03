# Amazon EBS Volume — SAA-C03 Exam Note

## 1. What is an EBS Volume?

**Amazon EBS (Elastic Block Store)** provides **block-level storage** for EC2 instances.

Think:

> **EBS = a network-attached hard drive for EC2**
> 

```
EC2 Instance
     │
     │ Network
     ▼
EBS Volume
```

Unlike temporary instance storage, EBS data can **persist after the EC2 instance is terminated**, depending on the volume's **Delete on Termination** setting.

### Why does EBS exist?

EC2 instances need persistent storage for things like:

- Operating-system/root disks
- Databases
- Application files
- Logs
- Persistent application data

---

# 2. EBS Is Network Storage

EBS is **not a physical disk inside the EC2 server**.

```
Physical server
┌─────────────────┐
│      EC2        │
│                 │
└─────────────────┘
        │
      Network
        │
        ▼
┌─────────────────┐
│   EBS Volume    │
└─────────────────┘
```

Because communication happens over the network, there can be **some network latency**.

### Exam clue

If the question says:

> **"Persistent block storage for EC2"**
> 

→ **EBS**

If it says:

> **"Network-attached block storage"**
> 

→ **EBS**

---

# 3. EBS Is Persistent

Suppose:

```
EC2
 │
 └── EBS 100 GB
```

You terminate EC2:

```
EC2 ❌
 │
 └── EBS 100 GB ✅
```

The EBS volume can remain and be attached to another EC2 instance.

This is one of the major differences between **EBS and Instance Store**.

---

# 4. EBS Is AZ-Specific ⭐⭐⭐⭐⭐

An EBS volume is associated with **one Availability Zone**.

For example:

```
us-east-1a
   │
   └── EBS Volume
```

You **cannot directly attach** that volume to an EC2 instance in:

```
us-east-1b
```

### To move EBS between AZs

Use:

```
EBS Volume
     ↓
Snapshot
     ↓
Create volume in another AZ
     ↓
Attach to EC2
```

### Exam trigger

> **"Move EBS volume to another AZ"**
> 

→ **Create snapshot → create new EBS volume in target AZ**

### Memory

> **EBS = AZ locked**
> 

---

# 5. Can One EBS Volume Attach to Multiple EC2 Instances?

For the normal SAA-C03 scenario:

> **One EBS volume → one EC2 instance**
> 

However, there is an important exception:

### EBS Multi-Attach

Certain **io1/io2** EBS volumes support **Multi-Attach**, allowing a volume to be attached to multiple instances **within the same AZ** when supported/configured.

But this is an advanced feature and requires applications/filesystems designed for concurrent access.

So remember:

> **Normal EBS → one instance**
> 

> **Multi-Attach → supported io1/io2 → multiple instances in same AZ**
> 

---

# 6. EBS Has Provisioned Capacity

When creating an EBS volume, you specify characteristics such as:

```
Size = 100 GB
IOPS = 5,000
```

You are charged for the **provisioned capacity** according to the volume type and pricing model.

For example:

```
EBS
100 GB
+
Provisioned performance
```

You can generally **increase the volume size over time** using Elastic Volumes.

---

# 7. EBS Volume Can Be Resized

Suppose you initially create:

```
EBS = 100 GB
```

Later:

```
100 GB → 500 GB
```

You can increase the volume without replacing it in many cases.

### Important

Increasing the EBS volume at the AWS level doesn't automatically mean the operating system's filesystem immediately uses the new space.

You may also need to extend the partition/filesystem inside the EC2 instance.

### Exam clue

> **"Increase EBS capacity without detaching/replacing the volume"**
> 

→ **Elastic Volumes**

---

# 8. EBS Detach and Reattach

One useful property:

```
EC2-A
  │
  └── EBS
       ↓ detach

       ↓ attach

EC2-B
  │
  └── EBS
```

You can detach an EBS volume and attach it to another compatible EC2 instance in the **same AZ**.

### Scenario

An EC2 instance fails, but the application data is stored on EBS.

You can attach the EBS volume to another instance and recover the data.

---

# 9. EBS Delete on Termination

This is **very important for exams**.

Every EBS volume attached to an EC2 instance has a:

> **Delete on Termination** attribute
> 

It controls whether the volume is deleted when the EC2 instance terminates.

### Default behavior

| EBS volume | Default behavior |
| --- | --- |
| **Root volume** | Deleted when EC2 terminates |
| **Additional EBS volume** | Preserved |

So:

```
EC2 terminated
      │
      ├── Root EBS → ❌ deleted by default
      │
      └── Data EBS → ✅ remains by default
```

### Example

EC2 has:

```
/dev/root → 20 GB
/dev/data → 100 GB
```

EC2 terminates.

By default:

```
20 GB root  → deleted
100 GB data → preserved
```

---

# 10. Why Would You Disable Delete on Termination?

Suppose an application stores important data on the root volume and you want that volume preserved after EC2 termination.

Set:

> **Delete on Termination = false**
> 

Then:

```
EC2 terminates
      ↓
Root EBS remains
      ↓
Can be preserved/reused
```

### Exam clue

> **"Preserve the EBS volume after EC2 termination"**
> 

→ **Disable Delete on Termination**

---

# 11. EBS vs Instance Store — Core Difference

This distinction is extremely important.

|  | **EBS** | **Instance Store** |
| --- | --- | --- |
| Type | Network block storage | Local physical storage |
| Persistent? | **Yes** | **No** |
| Survives EC2 termination? | Can | **No** |
| Detach/reattach | Yes | No |
| AZ-bound | Yes | Tied to instance |
| Performance | High | Very high / low latency |
| Best for | Persistent data | Temporary/cache/scratch data |
| Snapshot | **Yes** | No |
| Billing | Provisioned storage | Included with supported instance |

### Memory

> **EBS = Elastic + Persistent**
> 

> **Instance Store = Temporary + Local**
> 

---

# 12. EBS vs Instance Store — Scenario

### Scenario A

> A database needs persistent storage even when the EC2 instance is stopped or terminated.
> 

→ **EBS**

### Scenario B

> A distributed application needs extremely fast temporary scratch space and can recreate the data.
> 

→ **Instance Store**

---

# 13. EBS vs S3

Another common confusion:

|  | EBS | S3 |
| --- | --- | --- |
| Storage model | **Block** | **Object** |
| Attached to EC2? | Yes | No, accessed through API |
| Filesystem-like access | Yes | No traditional block filesystem |
| AZ | Specific AZ | Regional service |
| Typical use | OS/database/application disk | Objects, backups, static files |
| Example | EC2 `/data` disk | `photos/company/logo.png` |

### Exam clue

**"Block storage attached to EC2" → EBS**

**"Object storage / files accessed via HTTP/API" → S3**

---

# 14. EBS Snapshots

A snapshot is a **point-in-time backup of an EBS volume**.

```
EBS Volume
    │
    ▼
Snapshot
    │
    ├── Backup
    └── Create new EBS volume
```

Snapshots are stored in AWS-managed storage and are used to back up/restore EBS data.

### Important use case

To move an EBS volume to another AZ:

```
EBS in AZ-A
    ↓
Snapshot
    ↓
Create EBS in AZ-B
```

### Exam clue

> **"Copy/move EBS data to another AZ"**
> 

→ **Snapshot**

---

# 15. EBS Encryption

EBS supports encryption at rest.

Encrypted EBS protects:

- data
- snapshots
- data in transit between EC2 and EBS in supported configurations

AWS KMS keys are used for EBS encryption.

### Exam clue

> **"Encrypt EBS volumes"**
> 

→ **EBS encryption using KMS**

---

# 16. EBS Volume Types

For SAA-C03, understand the major categories:

```
SSD
│
├── gp3 / gp2 → General Purpose
│
└── io2 / io1 → Provisioned IOPS

HDD
│
├── st1 → Throughput Optimized
└── sc1 → Cold HDD
```

### Fast decision

| Requirement | Think |
| --- | --- |
| General-purpose SSD | **gp3** |
| High IOPS / demanding database | **io2** |
| High sequential throughput | **st1** |
| Lowest-cost HDD | **sc1** |

---

# 17. Elastic Volumes

**Elastic Volumes** allows you to modify supported EBS volume attributes while the volume is in use.

Common changes include:

- Increase size
- Change volume type
- Adjust performance characteristics

Example:

```
gp3
500 GB
3,000 IOPS

       ↓

Increase

       ↓

gp3
1 TB
6,000 IOPS
```

### Exam clue

> **"Resize EBS without stopping the application"**
> 

→ **Elastic Volumes**

---

# 18. EBS Data Lifecycle Manager (DLM)

**Amazon Data Lifecycle Manager (DLM)** automates the creation and deletion of EBS snapshots and related lifecycle management.

Example:

```
Every day
   ↓
Create EBS snapshot

Keep 7 snapshots
   ↓
Delete older snapshots
```

### Scenario

A company wants automatic daily backups of EBS volumes and wants old snapshots automatically deleted.

→ **DLM**

### Exam clue

**"Automate EBS snapshots + retention" → DLM**

---

# 19. EBS Multi-Attach

**Multi-Attach** allows certain EBS volumes to be attached to multiple EC2 instances simultaneously.

The key exam points:

- Mainly associated with **io1/io2**
- Instances must be in the **same AZ**
- Applications/filesystems must support concurrent access
- It is not a general-purpose shared filesystem

### Don't confuse with EFS

If the question says:

> **"Multiple EC2 instances need shared filesystem access"**
> 

Usually think:

→ **EFS**

If it specifically describes:

> **"Multiple EC2 instances attach to the same EBS volume"**
> 

→ **EBS Multi-Attach**

---

# 🔥 EBS Master Scenario Table

| Question requirement | Correct concept |
| --- | --- |
| Persistent block storage for EC2 | **EBS** |
| Network-attached storage | **EBS** |
| Survive EC2 termination | **EBS** |
| Move EBS to another AZ | **Snapshot → new EBS in target AZ** |
| Increase EBS size | **Elastic Volumes** |
| Preserve volume after EC2 termination | **Disable Delete on Termination** |
| Automatically create/delete EBS snapshots | **DLM** |
| High IOPS database | **io2/io1** |
| General-purpose SSD | **gp3/gp2** |
| High sequential throughput | **st1** |
| Lowest-cost HDD storage | **sc1** |
| Multiple EC2 instances share one supported EBS volume | **Multi-Attach** |
| Temporary ultra-fast local storage | **Instance Store** |
| Object storage | **S3** |

---

# 🧠 EBS Exam Traps

### Trap 1 — EBS is regional

❌ "An EBS volume can be directly attached across AZs."

✅ **EBS is AZ-specific.**

---

### Trap 2 — Root volume deletion

❌ "All EBS volumes are deleted when EC2 terminates."

✅ Root is **deleted by default**; additional EBS volumes are **not deleted by default**.

---

### Trap 3 — Snapshot isn't an EBS volume

A snapshot is a **backup/copy mechanism**, not the same thing as an attached EBS volume.

```
EBS → Snapshot → New EBS
```

---

### Trap 4 — EBS isn't shared storage by default

Normal EBS:

> **one volume → one EC2 instance**
> 

Multi-Attach is the special exception for supported volume types/configurations.

---

### Trap 5 — EBS vs Instance Store

If the requirement says:

**"data must survive instance termination"**

→ **EBS**

If:

**"temporary / scratch / cache / highest local performance"**

→ **Instance Store**

---

# 🚨 15-Second EBS Brain Map

```
                    EBS
                     │
        ┌────────────┼────────────┐
        │            │            │
    Persistent    AZ-specific   Network
        │
        ├── Snapshot
        │
        ├── Elastic Volumes
        │
        ├── Encryption/KMS
        │
        ├── DLM
        │
        └── Multi-Attach
```

### Final exam memory

> **EBS = persistent block storage attached to EC2**
> 

> **EBS is locked to an AZ**
> 

> **Need another AZ → Snapshot → create new volume**
> 

> **EC2 termination → root deleted by default, extra EBS preserved by default**
> 

> **Need automatic EBS snapshot lifecycle → DLM**
> 

> **Need multiple instances on one supported EBS volume → Multi-Attach**
> 

> **Need temporary local high-speed storage → Instance Store**
> 

> **Need general-purpose SSD → gp3**
> 

> **Need high IOPS → io2/io1**
> 

> **Need high sequential throughput → st1**
> 

> **Need object storage → S3**
>
