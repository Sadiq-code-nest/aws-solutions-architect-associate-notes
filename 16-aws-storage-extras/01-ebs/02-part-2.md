# 15. EBS Multi-Attach — VERY IMPORTANT

Another concept that appears in SAA questions is:

# **EBS Multi-Attach**

It allows a supported EBS volume to be attached to **multiple EC2 instances simultaneously**.

Conceptually:

```
             EBS Volume
              /      \
             /        \
          EC2-A      EC2-B
```

But there is a huge exam trap:

# Multi-Attach ≠ Multi-AZ

EBS volumes are associated with a **single Availability Zone**.

So:

```
AZ-A
 ├── EC2-A
 ├── EC2-B
 └── EBS
       ↑
   Multi-Attach
```

Not:

```
AZ-A              AZ-B
 │                  │
EC2-A              EC2-B
  \                /
       Same EBS ❌
```

### Therefore:

> **Multi-Attach allows multiple EC2 instances in the same AZ to access a supported EBS volume.**
> 

It does **not** make EBS multi-AZ.

---

# 16. EBS Multi-AZ Question Trap

Your reference question contains this statement:

> "gp3 volumes with multi-attach enabled ... designed for applications requiring multi-AZ resiliency."
> 

❌ **Wrong.**

There are two separate problems.

### Problem 1: Multi-Attach

Multi-Attach is not a Multi-AZ solution.

### Problem 2: Multi-AZ resiliency

For Multi-AZ database resilience, think:

```
RDS Multi-AZ
Aurora
Cross-AZ architecture
```

Not:

```
EBS Multi-Attach
```

### Exam rule

> **EBS = AZ-scoped**
> 

> **Multi-Attach = multiple instances in same AZ**
> 

---

# 17. EBS vs Instance Store

This is another favorite SAA-C03 comparison.

| Feature | EBS | Instance Store |
| --- | --- | --- |
| Block storage | ✅ | ✅ |
| Persistent | ✅ | ❌ |
| Survives instance termination | Usually configurable | ❌ |
| Attached to EC2 | ✅ | ✅ |
| Use for database persistence | ✅ | ❌ generally |
| Temporary/scratch data | Possible | ✅ Excellent |
| Data survives stop/termination | Depends on settings | ❌ termination |
| Network-attached | Yes | Physically attached to host |

### Exam clue

Question says:

> "Data must persist after EC2 instance termination."
> 

Think:

# → EBS

Question says:

> "Temporary scratch space"
> 

Think:

# → Instance Store

---

# 18. EBS vs S3 — Don't Mix Them Up

This is fundamental.

### EBS

```
EC2
 │
 └── EBS
      ↓
   Block storage
```

Used for:

- OS
- Filesystem
- Database storage
- Application data

### S3

```
Application
     │
     ▼
    S3
     ↓
Object storage
```

Used for:

- Backups
- Images
- Videos
- Documents
- Data lakes
- Static files

### Exam clue

> "Block-level storage for EC2"
> 

→ **EBS**

> "Object storage"
> 

→ **S3**

---

# 19. EBS vs EFS

Another common SAA comparison.

### EBS

Usually:

```
EC2 ─── EBS
```

Block storage.

### EFS

```
       EFS
      / | \
     /  |  \
   EC2 EC2 EC2
```

Shared file storage.

### Exam clue

> "One EC2 needs persistent block storage"
> 

→ **EBS**

> "Multiple EC2 instances need shared Linux file storage"
> 

→ **EFS**

---

# 20. The "IOPS vs Throughput" Concept

This is probably the **single most important conceptual distinction** for EBS.

## IOPS

**Input/Output Operations Per Second**

Think:

> "How many I/O operations can I perform?"
> 

Example:

```
Database
1000s of small random reads/writes
        ↓
       IO2
```

---

## Throughput

Think:

> "How much data can I transfer per second?"
> 

Example:

```
Large sequential files
████████████████████████
        ↓
      ST1
```

### Remember

```
IOPS       → IO2
Throughput → ST1
```

---

# 21. Random vs Sequential I/O

Another exam clue.

### Random I/O

```
Block 100
Block 5
Block 700
Block 32
Block 999
```

Lots of individual I/O operations.

Think:

# → IO2

Typical:

- Databases
- Transaction systems

---

### Sequential I/O

```
Block 1
Block 2
Block 3
Block 4
Block 5
Block 6
...
```

Large streams of data.

Think:

# → ST1

Typical:

- Big data
- Log processing
- Data warehouses

---

# 22. SSD vs HDD — Easy Decision

```
Need high performance?
        │
        ▼
       SSD
     /     \
    /       \
General    High IOPS
   │          │
  gp3        io2
```

If performance isn't the main requirement:

```
        HDD
       /   \
      /     \
Throughput  Cold
   │          │
  st1        sc1
```

---

# 23. Scenario-Based SAA Questions

## Scenario 1

A company runs a normal web application on EC2. It needs persistent storage for the application and OS.

### Answer:

**gp3**

Why?

> General-purpose workload.
> 

---

## Scenario 2

A financial application runs a mission-critical relational database requiring extremely high and consistent IOPS.

### Answer:

**io2**

Clues:

> Mission-critical + database + high IOPS.
> 

---

## Scenario 3

A company processes huge amounts of sequential log data.

### Answer:

**st1**

Clues:

> Large + sequential + throughput.
> 

---

## Scenario 4

A company stores several TB of data that is rarely accessed. Cost is the primary concern.

### Answer:

**sc1**

Clues:

> Infrequent + low cost + HDD.
> 

---

## Scenario 5

An old application specifically requires a previous-generation EBS volume.

### Answer:

**Magnetic**

Clue:

> Previous generation / legacy.
> 

---

# 24. Scenario 6 — The Trap

A database requires extremely high IOPS. Which EBS type?

A. gp3

B. sc1

C. st1

D. io2

### Answer:

**D — io2**

Because:

```
High IOPS
   ↓
 IO2
```

---

# 25. Scenario 7 — Another Trap

A company needs high throughput for a large sequential data-processing workload.

A. io2

B. gp3

C. st1

D. sc1

### Answer:

**C — st1**

Because:

```
Sequential + throughput
          ↓
         st1
```

---

# 26. Scenario 8

A company needs inexpensive EBS storage for data that is rarely accessed.

### Answer:

**sc1**

Don't automatically choose Magnetic.

Modern mental model:

> **Cold + infrequent + cheap HDD → sc1**
> 

---

# 27. Scenario 9

An application requires persistent block-level storage attached to an EC2 instance.

### Answer:

**EBS**

Not:

- S3 ❌ — object storage
- EFS ❌ — shared file system
- Instance Store ❌ — ephemeral

---

# 28. Scenario 10 — Multi-Attach Trap

Two EC2 instances need access to the same EBS volume.

What feature?

### Answer:

**EBS Multi-Attach**, where supported.

But remember:

> Same Availability Zone.
> 

---

# 29. Your Reference Question — Final Analysis

Let's classify every answer.

### A. gp3 + Multi-Attach + Multi-AZ resiliency

❌ **Wrong**

Because Multi-Attach does not provide Multi-AZ resiliency.

---

### B. Magnetic = lowest-cost + infrequent access

⚠️ **Historically/reference-question answer**

The question is based on older EBS terminology.

For **current SAA-C03 thinking**, use:

> **sc1 = infrequently accessed, lowest-cost HDD**
> 

Treat Magnetic as:

> **previous-generation / legacy**
> 

---

### C. Spot volumes

❌ **Wrong**

There is no EBS volume type called "Spot."

Don't confuse:

```
Spot Instance ≠ EBS volume type
```

---

### D. Provisioned IOPS = consistent low latency + I/O intensive DB

✅ **Correct**

Think:

```
High IOPS
Low latency
Database
     ↓
   IO2/IO1
```

---

### E. SR-IOV volumes

❌ **Wrong**

SR-IOV is a virtualization/networking technology, **not an EBS volume type**.

---

# 30. The Ultimate EBS Memory Map

If you remember only one diagram, remember this:

```
                         EBS
                          │
             ┌────────────┴─────────────┐
             │                          │
            SSD                        HDD
             │                          │
       ┌─────┴─────┐              ┌─────┴─────┐
       │           │              │           │
     GP3          IO2            ST1         SC1
       │           │              │           │
   GENERAL       HIGH IOPS     HIGH        COLD
   PURPOSE       DATABASE      THROUGHPUT   DATA
       │           │              │           │
    Boot        DB/critical     Big data    Infrequent
    Web         Low latency     Logs        Low cost
    App         I/O intensive   Sequential
```

And:

```
MAGNETIC
    ↓
LEGACY / PREVIOUS GENERATION
```

---

# 🧠 31. ONE-LOOK MASTER TABLE

This is the table I'd memorize before the exam:

| 🔑 Question clue | 🎯 EBS answer | 🧠 Mental word |
| --- | --- | --- |
| General purpose | **gp3** | GENERAL |
| Boot volume | **gp3** | GENERAL |
| Web/app server | **gp3** | GENERAL |
| Most workloads | **gp3** | GENERAL |
| Need configurable IOPS/throughput | **gp3** | FLEXIBLE |
| High IOPS | **io2** | IOPS |
| Low latency DB | **io2** | DATABASE |
| Mission-critical DB | **io2** | CRITICAL |
| I/O intensive | **io2** | I/O |
| Provisioned IOPS | **io2/io1** | IOPS |
| High throughput | **st1** | THROUGHPUT |
| Sequential access | **st1** | SEQUENTIAL |
| Big data | **st1** | BIG DATA |
| Log processing | **st1** | STREAM |
| Data warehouse | **st1** | THROUGHPUT |
| Infrequent access | **sc1** | COLD |
| Lowest-cost HDD | **sc1** | CHEAP |
| Cold data | **sc1** | COLD |
| Previous generation | **Magnetic** | LEGACY |
| Multiple EC2 same EBS | **Multi-Attach** | SAME AZ |
| Multi-AZ resilience | **Not EBS Multi-Attach** | AZ |
| Temporary storage | **Instance Store** | EPHEMERAL |
| Object storage | **S3** | OBJECT |
| Shared file system | **EFS** | FILE |

---

# 🚨 32. Exam Clue → Answer in 3 Seconds

When reading an SAA question, train your brain like this:

```
"GENERAL"
    ↓
   GP3

"HIGH IOPS"
    ↓
   IO2

"LOW LATENCY DATABASE"
    ↓
   IO2

"SEQUENTIAL"
    ↓
   ST1

"HIGH THROUGHPUT"
    ↓
   ST1

"INFREQUENT"
    ↓
   SC1

"LOWEST-COST HDD"
    ↓
   SC1

"LEGACY / PREVIOUS GENERATION"
    ↓
 MAGNETIC

"TEMPORARY"
    ↓
 INSTANCE STORE

"OBJECT"
    ↓
 S3

"SHARED FILE"
    ↓
 EFS
```

## ⭐ Final SAA-C03 Rule

Don't memorize EBS as seven random names. Memorize the **five personalities**:

> 🟢 **GP3 = I need a normal SSD.**
> 
> 
> 🔴 **IO2 = I need serious IOPS.**
> 
> 🟠 **ST1 = I need serious throughput.**
> 
> ⚪ **SC1 = I have cold/infrequent data and want cheap HDD storage.**
> 
> 🟤 **Magnetic = old/legacy.**
> 

And the three storage comparisons:

> **EBS = persistent block**
> 
> 
> **EFS = shared file**
> 
> **S3 = object**
> 

That mental model is much more useful for SAA-C03 than memorizing raw performance numbers.
