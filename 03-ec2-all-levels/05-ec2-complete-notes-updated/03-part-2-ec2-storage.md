# Part 2 — EC2 Storage

Absolutely. We’ll continue **EC2 — Part 2: Storage** in the exact sequence you provided. I’ll go deep enough for SAA-C03 decision-making, but keep each point exam-oriented.

# EC2 — PART 2: STORAGE

## EBS, Instance Store, Snapshots, Encryption, Volume Types, Performance & Exam Scenarios

---

# 1. EBS-BACKED INSTANCES

## What is it?

An **EBS-backed EC2 instance** uses an Amazon EBS volume as its **root volume**.

Think:

> **EC2 = compute + EBS = persistent disk**
> 

The operating system, applications, and potentially data can live on the EBS root volume.

### Why AWS uses it

Because EBS is **persistent storage**.

If you stop an EBS-backed instance:

```
EC2 STOP
   ↓
Instance shuts down
   ↓
EBS remains
   ↓
START
   ↓
Same root disk/data available
```

If you terminate it, the root EBS volume is **normally deleted by default**, depending on its `DeleteOnTermination` setting.

---

## Exam trigger

If the question says:

> **Persistent storage**
> 

→ Think **EBS**

> **Data must survive instance stop/restart**
> 

→ **EBS**

> **Need to detach disk and attach to another EC2**
> 

→ **EBS**

---

## Tiny example

A web server is stopped every night but must retain its OS and application data.

→ **EBS-backed EC2**

---

## Trap

Don't confuse:

**EBS persistent**

with

**Instance Store ephemeral**

---

# 2. INSTANCE STORE

Instance Store is **physically attached local storage** on the host running your EC2 instance.

Think:

> **Instance Store = local disk inside the EC2 host**
> 

Unlike EBS, it isn't a network-attached volume.

```
              EC2 Host
        ┌─────────────────┐
        │ EC2 Instance    │
        │                 │
        │ Instance Store  │
        │ LOCAL DISK      │
        └─────────────────┘
```

This provides extremely fast local I/O with very low latency.

### The critical characteristic

**Instance Store is ephemeral.**

If the EC2 instance is:

- stopped
- hibernated
- terminated
- or the underlying host fails

you should assume the instance-store data is lost.

---

## Why use Instance Store?

When you need:

- very high-speed temporary data
- scratch space
- temporary buffers
- cache
- temporary processing data

and **you can tolerate losing it**.

---

## Exam trigger

Words like:

> **temporary**
> 

> **ephemeral**
> 

> **scratch data**
> 

> **very low latency local storage**
> 

> **data can be recreated**
> 

→ **Instance Store**

---

## Tiny example

A batch-processing application downloads temporary files, processes them, and can recreate them if lost.

→ Instance Store can be ideal.

---

# 3. EBS vs INSTANCE STORE

This is one of the highest-value EC2 distinctions.

| Feature | EBS | Instance Store |
| --- | --- | --- |
| Storage type | Network block storage | Local physical storage |
| Persistence | Persistent | Ephemeral |
| Survives instance stop | ✅ | ❌ |
| Survives termination | Possible depending on DeleteOnTermination | ❌ |
| Detachable | ✅ | ❌ |
| Move to another AZ | Via snapshot | ❌ |
| Low latency | Good | **Excellent** |
| Use case | Persistent workloads | Temporary/high-speed workloads |
| Backup | Snapshots | Application-level/recreated |
| Billing | Provisioned capacity | Included with supported instance types |

### Memory trick

> **EBS = External + Backup + Survives**
> 

> **Instance Store = Inside + Speed + Sacrifice**
> 

---

# 4. ROOT VOLUME

The **root volume** contains the operating system from which the EC2 instance boots.

For example:

```
EC2
│
├── Root volume
│      └── Windows/Linux OS
│
├── EBS data volume
│      └── Application data
│
└── EBS data volume
       └── Database files
```

The root device might be an EBS volume.

---

## Exam clue

If the question asks:

> "Where is the operating system stored?"
> 

→ **Root volume**

If it asks:

> "What happens to the root volume when the instance terminates?"
> 

→ Check **DeleteOnTermination**.

---

# 5. DELETE-ON-TERMINATION

This setting determines whether an attached EBS volume is automatically deleted when the EC2 instance is terminated.

### Default behavior

For the typical EC2 launch:

| Volume | Default |
| --- | --- |
| Root EBS volume | **Delete** |
| Additional EBS volume | **Keep** |

So:

```
Terminate EC2
      ↓
Root EBS
      ↓
Deleted by default
```

But:

```
Terminate EC2
      ↓
Additional EBS
      ↓
Usually retained
```

---

## Exam scenario

> A company wants to preserve the root volume after terminating an EC2 instance.
> 

→ Set:

**DeleteOnTermination = false**

---

## Major trap

**Stopping** an EC2 instance does NOT normally delete its EBS volume.

The important event is:

> **TERMINATION**
> 

not stop.

---

# 6. EBS ATTACHMENT

EBS volumes are attached to EC2 instances.

Normally:

```
EBS Volume
    ↓
One EC2 instance
```

The volume is associated with a specific **Availability Zone**.

For example:

```
us-east-1a

EC2
 │
 └── EBS
```

You cannot simply attach that volume directly to:

```
us-east-1b
```

---

# 7. AZ LIMITATION

This is extremely important.

### EBS is AZ-scoped.

If:

```
EBS → us-east-1a
```

it can only attach to EC2 instances in:

```
us-east-1a
```

Not:

```
us-east-1b
us-east-1c
```

---

## Exam trigger

> **EBS needs to be attached to an instance in another AZ**
> 

→ **Snapshot → create/restore volume in destination AZ**

---

# 8. MOVING EBS ACROSS AZ

You cannot directly move an EBS volume from one AZ to another.

Use a snapshot.

```
EBS
us-east-1a
   ↓
Snapshot
   ↓
Restore/Create EBS
   ↓
us-east-1b
```

### Exam sequence

**Snapshot → Restore → Destination AZ**

---

## Tiny example

An EC2 instance is moved from `us-east-1a` to `us-east-1b`.

Its EBS data volume must move too.

→ Snapshot the original EBS volume and create a new volume from that snapshot in `us-east-1b`.

---

## Trap

Don't answer:

> "Detach EBS and attach it to the instance in another AZ."
> 

❌ Wrong.

The AZ restriction still applies.

---

# 9. EBS SNAPSHOTS

An EBS snapshot is a **point-in-time backup** of an EBS volume.

Think:

> **EBS = disk**
> 

> **Snapshot = backup of that disk**
> 

---

## Why snapshots matter

Snapshots can be used for:

- backup
- disaster recovery
- creating new EBS volumes
- moving data to another AZ
- copying data across Regions
- creating AMIs
- encryption workflows

---

## Example

```
EBS
100 GB
  ↓
Snapshot
  ↓
Create new EBS
```

The new EBS volume can be created from that snapshot.

---

## Cross-AZ

Snapshots are regional resources, so they allow you to create a volume in another AZ within the Region.

---

## Cross-Region

You can copy an EBS snapshot to another Region.

Example:

```
us-east-1
   │
EBS Snapshot
   │
   ▼
Copy Snapshot
   │
   ▼
eu-west-1
```

Useful for:

- disaster recovery
- regional migration
- compliance
- business continuity

---

# Snapshot exam trap

Don't confuse:

**EBS Snapshot**

with

**AMI**

An EBS snapshot is primarily a **volume backup**.

An AMI is a **machine image used to launch EC2 instances**.

We'll revisit this distinction in Section C, but remember:

> **Snapshot → disk**
> 

> **AMI → machine template**
> 

---

# 10. EBS ENCRYPTION

EBS encryption is a very common SAA-C03 security question.

When you use encrypted EBS:

### Data at rest

Encrypted.

### Data moving between EC2 and EBS

Encrypted.

### Snapshots

Encrypted.

### Volumes created from encrypted snapshots

Encrypted.

AWS handles the encryption/decryption transparently.

You don't manually encrypt every read/write operation.

---

## KMS

EBS encryption uses **AWS KMS**.

Conceptually:

```
EC2
 │
 │ encrypted communication
 ▼
EBS
 │
 └── encrypted at rest
       │
       ▼
     KMS
```

---

## Exam trigger

> **Encrypt EBS volumes**
> 

→ **EBS encryption using KMS**

> **Encrypted EBS snapshot**
> 

→ Snapshot remains encrypted.

> **Create volume from encrypted snapshot**
> 

→ New volume is encrypted.

---

# 11. ENCRYPT AN EXISTING UNENCRYPTED EBS VOLUME

Suppose:

```
Existing EBS
UNENCRYPTED
```

You generally don't simply flip a switch on the existing volume.

Use:

```
Unencrypted EBS
      ↓
Snapshot
      ↓
Copy snapshot
      ↓
Encrypt snapshot
      ↓
Create encrypted EBS
      ↓
Attach new volume
```

### Exam shortcut

> **Old EBS is unencrypted → Snapshot → Copy/Encrypt → New EBS**
> 

---

# 12. EBS VOLUME TYPES

You need to know the six major families:

| Type | Media | Main idea |
| --- | --- | --- |
| **gp2** | SSD | General purpose |
| **gp3** | SSD | General purpose, independently configurable |
| **io1** | SSD | Provisioned IOPS |
| **io2** | SSD | Higher durability/performance PIOPS |
| **st1** | HDD | Throughput-intensive |
| **sc1** | HDD | Cold/infrequent, cheapest HDD |

The easiest decision tree:

```
Need boot volume?
       │
       ▼
   SSD family
       │
   ┌───┴────┐
   │        │
General   High IOPS
   │        │
gp3      io2/io1
```

If it's huge sequential throughput:

```
HDD
│
├── Frequent → st1
└── Infrequent → sc1
```

---

# 13. GP2 vs GP3

This is an important exam comparison.

## gp2

Performance is tied to **volume size**.

More GB → more baseline IOPS.

So:

> **gp2 = capacity controls performance**
> 

---

## gp3

Performance is **decoupled from storage capacity**.

You can independently configure:

- volume size
- IOPS
- throughput

Baseline:

- **3,000 IOPS**
- **125 MiB/s throughput**

and performance can be provisioned higher within gp3 limits.

---

## The exam scenario

> "We need more IOPS but don't want to increase storage capacity."
> 

→ **gp3**

That's a classic trigger.

---

### Memory trick

> **gp2 = Size → Speed**
> 

> **gp3 = Separate Speed**
> 

---

## Example

You have a 500-GB workload but need substantially more IOPS.

With gp2, increasing size may be necessary to increase baseline IOPS.

With gp3, you can increase IOPS independently.

→ **gp3**

---

# 14. IO1 vs IO2

These are **Provisioned IOPS SSD** volumes.

Use them when the application needs:

- sustained high IOPS
- predictable storage performance
- low latency
- database workloads
- mission-critical applications

---

## io1

Provisioned IOPS.

Good for high-performance databases.

---

## io2

The newer/high-end option with:

- higher durability
- higher performance capability
- very high IOPS
- io2 Block Express for demanding workloads

---

## Exam trigger

> **Mission-critical database**
> 

> **Very high IOPS**
> 

> **Predictable I/O performance**
> 

→ **io1/io2**

When the question emphasizes the newest/highest performance option:

→ **io2**

---

## Multi-Attach

A particularly important distinction:

**io1/io2** can support **EBS Multi-Attach** for supported configurations.

This allows one EBS volume to be attached to multiple supported EC2 instances in the same AZ.

```
EC2 ──┐
      ├── EBS io1/io2
EC2 ──┘
```

But don't generalize this to gp3.

---

# 15. ST1 vs SC1

Both are HDD-based EBS volumes.

They are **not boot volumes**.

---

## st1 — Throughput Optimized HDD

Use when:

> **frequently accessed + sequential throughput**
> 

Typical:

- Big Data
- data warehouses
- log processing
- large sequential workloads

Think:

> **ST1 = Speed through Throughput**
> 

---

## sc1 — Cold HDD

Use when:

> **infrequently accessed + lowest storage cost**
> 

Think:

> **SC1 = Slow/Cheap**
> 

---

### Comparison

|  | st1 | sc1 |
| --- | --- | --- |
| HDD | ✅ | ✅ |
| Main goal | Throughput | Lowest cost |
| Access | Frequent | Infrequent |
| Boot | ❌ | ❌ |
| Example | Log processing | Cold datasets |

---

# 16. BOOT-VOLUME RULES

This is a classic exam trap.

Only:

- **gp2**
- **gp3**
- **io1**
- **io2**

can be used as boot/root EBS volumes.

### NOT boot volumes:

- st1 ❌
- sc1 ❌

Why?

Because st1/sc1 are optimized for throughput/cold storage, not operating-system boot workloads.

---

## Memory

> **Boot = SSD**
> 

> **HDD EBS = data, not boot**
> 

---

# 17. IOPS vs THROUGHPUT vs LATENCY

This is where many questions become confusing.

### IOPS

**Input/Output Operations Per Second**

Think:

> **How many individual I/O operations can I perform per second?**
> 

Useful for:

- transactional databases
- many small random reads/writes

---

### Throughput

Amount of data transferred per unit of time.

Think:

> **How much data can I move per second?**
> 

Useful for:

- large sequential reads
- large sequential writes
- video processing
- log processing
- big data

---

### Latency

How long one operation takes.

Think:

> **How quickly does one request respond?**
> 

---

## Example

### Database with many small random operations

Focus:

**IOPS + latency**

→ io2/io1 may be appropriate.

### Large sequential file processing

Focus:

**Throughput**

→ st1 or appropriate high-throughput storage.

---

# 18. EBS-OPTIMIZED INSTANCES

An EBS-optimized EC2 instance provides a dedicated network path/bandwidth for EBS traffic.

Think:

```
Normal EC2

Application
    ↓
Shared networking
    ↓
EBS
```

versus:

```
EBS-Optimized

Application
    ↓
EC2
    ↓
Dedicated EBS bandwidth
    ↓
EBS
```

---

## Why?

To improve:

- EBS throughput
- predictable storage performance
- network contention behavior

---

## Exam trigger

> **High EBS I/O workload**
> 

> **Need dedicated bandwidth between EC2 and EBS**
> 

→ **EBS-optimized instance**

---

## Don't confuse

**EBS-optimized**

≠

**Enhanced Networking**

Enhanced networking improves **EC2 network performance**.

EBS optimization specifically targets **EC2 ↔ EBS I/O**.

---

# 19. EBS MULTI-ATTACH

Normally:

```
EBS
 ↓
EC2
```

With supported Multi-Attach:

```
       EC2
        │
        │
EBS ────┼──── EC2
        │
        │
       EC2
```

Multiple EC2 instances can access the same EBS volume.

### Important restrictions

For SAA-C03, remember:

- Supported with **io1/io2**
- Instances must be in the **same AZ**
- Application must be designed to coordinate concurrent access
- EBS doesn't automatically provide a distributed filesystem

---

## Exam trigger

> **Multiple EC2 instances need simultaneous access to the same block volume**
> 

→ Think **EBS Multi-Attach**

But if the question simply says:

> **Multiple EC2 instances need shared files**
> 

→ Usually think **EFS**, not Multi-Attach.

### Critical distinction

**Multi-Attach = shared block storage**

**EFS = shared file storage**

---

# 20. ELASTIC VOLUMES

EBS Elastic Volumes allow you to modify EBS volume configuration without the traditional process of detaching/recreating the volume.

Depending on volume type, you can modify:

- size
- performance characteristics such as IOPS
- throughput

This is especially important with **gp3** because performance can be independently adjusted.

---

## Exam trigger

> "Increase EBS volume size while the application is running."
> 

→ **Elastic Volumes**

---

### Example

A database's EBS volume is becoming full.

Instead of:

```
Stop
Detach
Create new disk
Copy data
Attach
```

you can increase the EBS volume size using Elastic Volumes.

The filesystem may still need to be expanded inside the operating system.

### Exam trap

Increasing the **EBS volume** does not automatically mean the operating system's filesystem is expanded.

Think:

```
AWS EBS size increased
        ↓
OS sees larger block device
        ↓
Filesystem may need expansion
```

---

# 21. DATA LIFECYCLE MANAGER — DLM

**Amazon Data Lifecycle Manager (DLM)** automates lifecycle management for EBS snapshots and EBS-backed AMIs.

Think:

> **DLM = automatic EBS backup housekeeping**
> 

---

## Why use it?

Instead of manually creating/deleting snapshots:

```
Every day
   ↓
Snapshot
   ↓
Keep 7
   ↓
Delete old ones
```

DLM can automate that lifecycle.

---

## Example

A company wants:

> Daily EBS snapshots and automatic deletion after 30 days.
> 

→ **EBS + DLM**

---

## Exam trigger

> **Automatically create EBS snapshots**
> 

> **Automatically retain/delete EBS snapshots**
> 

→ **Data Lifecycle Manager**

---

## Don't confuse DLM with AWS Backup

DLM is particularly focused on automated lifecycle management of **EBS snapshots/AMI-related resources**.

AWS Backup is the broader centralized backup service across many AWS services.

---

# 22. EXAM SCENARIOS INVOLVING EBS

Now let's turn the concepts into the way SAA-C03 actually tests them.

---

## Scenario 1 — Persistent disk

> EC2 data must survive instance stop/start.
> 

### Pick:

**EBS**

### Trigger:

**PERSISTENT**

---

# Scenario 2 — Temporary high-speed data

> Application creates temporary files that can be regenerated and requires very low-latency local storage.
> 

### Pick:

**Instance Store**

### Trigger:

**TEMPORARY + LOCAL + EPHEMERAL**

---

# Scenario 3 — Move EBS to another AZ

> EBS is in `us-east-1a`, but EC2 is now in `us-east-1b`.
> 

### Pick:

**Snapshot → restore/create volume in 1b**

### Trigger:

**EBS + DIFFERENT AZ**

---

# Scenario 4 — Increase EBS performance without increasing capacity

> Database needs more IOPS but doesn't need more storage.
> 

### Pick:

**gp3**

### Trigger:

**IOPS INDEPENDENT OF SIZE**

---

# Scenario 5 — Mission-critical database

> Database requires sustained, predictable, high IOPS.
> 

### Pick:

**io1/io2**

Prefer **io2** when the question emphasizes newer/higher durability/high-end PIOPS capability.

---

# Scenario 6 — Large sequential workload

> Application performs high sequential reads/writes against large datasets.
> 

Be careful.

If the question is describing **local storage** specifically:

→ Consider **EC2 Storage Optimized instances / Instance Store**.

If it specifically asks for an **EBS volume** optimized for throughput:

→ **st1**

This distinction is extremely important.

---

# Scenario 7 — Infrequent HDD data

> Data is accessed infrequently and the company wants the lowest EBS storage cost.
> 

### Pick:

**sc1**

---

# Scenario 8 — EBS boot volume

> Which EBS volume can be used as an EC2 root volume?
> 

### Pick:

**gp2 / gp3 / io1 / io2**

Not:

**st1/sc1**

---

# Scenario 9 — Encrypt existing EBS

> Existing volume is unencrypted and must become encrypted.
> 

### Pattern:

```
EBS
 ↓
Snapshot
 ↓
Copy + encryption
 ↓
New encrypted EBS
```

---

# Scenario 10 — EBS backup automation

> Automatically create daily EBS snapshots and delete old snapshots.
> 

### Pick:

**DLM**

---

# Scenario 11 — Multiple EC2s share block storage

> Several supported EC2 instances need simultaneous access to the same block volume.
> 

### Pick:

**EBS Multi-Attach**

### Trigger:

**SAME BLOCK DEVICE + MULTIPLE EC2**

But if they need normal shared files:

→ **EFS**

---

# Scenario 12 — EBS performance bottleneck

> EC2 has heavy EBS I/O and requires dedicated bandwidth.
> 

### Pick:

**EBS-optimized EC2**

---

# Scenario 13 — Root disk survives termination

> Company terminates instances but wants to preserve the root EBS data.
> 

### Pick:

Set:

**DeleteOnTermination = false**

---

# Scenario 14 — High IOPS vs high throughput

> Database performs millions of small random operations.
> 

→ **IOPS / io2**

> Big data application sequentially reads enormous files.
> 

→ **Throughput / st1** if using EBS.

---

# 🔥 MASTER EBS DECISION TABLE

| Question wording | Think | Answer |
| --- | --- | --- |
| **Persistent EC2 disk** | Persistent block | **EBS** |
| **Temporary local disk** | Ephemeral | **Instance Store** |
| **Very low latency local storage** | Local disk | **Instance Store** |
| **Move EBS to another AZ** | Snapshot | **Snapshot → new EBS** |
| **Backup EBS** | Point-in-time backup | **Snapshot** |
| **Cross-Region EBS DR** | Copy backup | **Snapshot copy** |
| **Encrypt EBS** | Encryption at rest | **EBS + KMS** |
| **Existing unencrypted EBS → encrypted** | Snapshot conversion | **Copy/encrypt snapshot → new EBS** |
| **General purpose SSD** | Balanced | **gp3** |
| **IOPS independent of capacity** | Decoupled performance | **gp3** |
| **Mission-critical high IOPS** | PIOPS | **io2/io1** |
| **Highest-end PIOPS/durability** | Premium PIOPS | **io2** |
| **Sequential throughput + frequent access** | HDD throughput | **st1** |
| **Infrequent + cheapest HDD** | Cold HDD | **sc1** |
| **Boot volume** | SSD | **gp2/gp3/io1/io2** |
| **Multiple EC2 → same block disk** | Shared block | **Multi-Attach** |
| **Multiple EC2 → shared files** | Shared filesystem | **EFS** |
| **Dedicated EC2 → EBS bandwidth** | Storage path | **EBS-optimized** |
| **Automatically manage EBS snapshots** | Lifecycle | **DLM** |
| **Increase EBS size online** | Dynamic modification | **Elastic Volumes** |

---

# 🧠 EBS MEMORY MAP

Remember this sequence:

```
                    EBS
                     │
        ┌────────────┼────────────┐
        │            │            │
     GENERAL       HIGH IOPS    HDD
        │            │            │
       gp3        io1 / io2     ┌──┴──┐
                                │     │
                               st1   sc1
                                │     │
                           Throughput Cold
```

Then:

```
gp3
 ↓
Size ≠ IOPS
```

```
io2
 ↓
Mission-critical / very high IOPS
```

```
st1
 ↓
Sequential throughput
```

```
sc1
 ↓
Infrequent + cheapest
```

And:

```
Instance Store
 ↓
LOCAL + FAST + EPHEMERAL
```

---

# ⚠️ THE 10 EBS TRAPS I WANT YOU TO REMEMBER

### 1. EBS ≠ Instance Store

**EBS persists. Instance Store is ephemeral.**

### 2. EBS ≠ EFS

**EBS = block**

**EFS = shared file system**

### 3. st1/sc1 cannot boot EC2

They are HDD data volumes.

### 4. EBS is AZ-bound

Different AZ → snapshot first.

### 5. gp2 and gp3 are NOT the same

**gp2:** size influences IOPS.

**gp3:** IOPS/throughput can be configured independently.

### 6. Multi-Attach ≠ EFS

Multi-Attach gives shared **block** access.

### 7. Encryption uses KMS

EBS encryption covers the volume and snapshots, transparently.

### 8. Termination ≠ Stop

Delete-on-termination matters when the instance is **terminated**.

### 9. Increasing EBS size ≠ automatically expanding filesystem

The OS filesystem may need its own expansion.

### 10. "Local storage" is a huge clue

If the question specifically says:

> **local / ephemeral / temporary / very low latency**
> 

→ **Instance Store**

If it says:

> **persistent / detachable / snapshot / backup**
> 

→ **EBS**

---

# 🔥 FINAL 30-SECOND EXAM BRAIN

If you see:

> **PERSISTENT BLOCK STORAGE**
> 

→ **EBS**

> **LOCAL + EPHEMERAL**
> 

→ **Instance Store**

> **GENERAL PURPOSE**
> 

→ **gp3**

> **IOPS INDEPENDENT OF SIZE**
> 

→ **gp3**

> **MISSION-CRITICAL DATABASE + HIGH IOPS**
> 

→ **io2**

> **SEQUENTIAL THROUGHPUT**
> 

→ **st1**

> **INFREQUENT + LOWEST COST HDD**
> 

→ **sc1**

> **BOOT**
> 

→ **gp3 / gp2 / io1 / io2**

> **DIFFERENT AZ**
> 

→ **Snapshot → new EBS**

> **MULTIPLE EC2 + SAME BLOCK**
> 

→ **Multi-Attach**

> **MULTIPLE EC2 + SHARED FILES**
> 

→ **EFS**

> **AUTOMATIC EBS SNAPSHOTS**
> 

→ **DLM**

> **EBS PERFORMANCE PATH**
> 

→ **EBS-optimized**

> **UNENCRYPTED EBS → ENCRYPTED**
> 

→ **Snapshot → copy/encrypt → new volume**

> **PRESERVE ROOT AFTER TERMINATION**
> 

→ **DeleteOnTermination = false**

---

## Part 2 complete

**EC2 Storage — all 21 requested subtopics covered.**

**Next in your EC2 master sequence should be Part 3**, continuing after storage with the next EC2 topic block from your original master list.
