# 4.8 DynamoDB

### Definition

DynamoDB is a managed NoSQL database for key-value and document data, designed for highly scalable database workloads.

```
Application
     │
     ├── GetItem
     ├── PutItem
     └── Query
            │
            ▼
        DynamoDB
```

### Example

```
PlayerID → 12345
Score    → 5000
Level    → 42
```

### DynamoDB vs EFS

| DynamoDB | EFS |
| --- | --- |
| NoSQL database | File system |
| Access through APIs | Access through filesystem/NFS |
| Items/attributes | Files/directories |
| Key-value/document | File/directory hierarchy |
| Not mountable as filesystem | Mountable |
| Database workloads | Shared-file workloads |

### Exam trigger

> **"NoSQL + key-value/document + GetItem/Query/PutItem" → DynamoDB**
> 

---

# 4.9 DAX

### Definition

DynamoDB Accelerator (DAX) is an in-memory cache specifically designed to accelerate DynamoDB reads.

```
Application
    │
    ▼
   DAX
    │
    ▼
DynamoDB
```

### Example

```
Frequently requested item
       ↓
DAX cache
       ↓
Microsecond response
```

### DAX vs EFS

| DAX | EFS |
| --- | --- |
| DynamoDB cache | Filesystem |
| Caches database items | Stores files |
| API/database access | NFS filesystem access |
| Read acceleration | Shared storage |
| Not mountable | Mountable |

### Exam trigger

> **"DynamoDB + microsecond read performance/cache" → DAX**
> 

---

# 4.10 DynamoDB Streams

### Definition

DynamoDB Streams captures item-level changes made to a DynamoDB table and makes those changes available for processing.

```
DynamoDB
   │
   │ item changed
   ▼
DynamoDB Streams
   │
   ▼
Lambda / consumer
```

### Example

```
Player score changes
       ↓
DynamoDB
       ↓
Streams
       ↓
Lambda
       ↓
Update leaderboard
```

### Streams vs filesystem

Streams are about:

```
CHANGE EVENTS
```

not:

```
SHARED FILE STORAGE
```

### Exam trigger

> **"React to DynamoDB item changes" → DynamoDB Streams**
> 

---

# 5. Master Comparison Table

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **Hundreds of ECS tasks need shared files** | **EFS** |
| **Multiple EC2 instances need the same filesystem** | **EFS** |
| **Linux shared filesystem / NFS** | **EFS** |
| **Highly parallel HPC filesystem workload** | **EFS Max I/O** |
| **Small/moderate dataset but high throughput requirement** | **EFS Provisioned Throughput** |
| **General-purpose EFS workload / lower latency** | **EFS General Purpose** |
| **Throughput naturally scales with stored data** | **EFS Bursting Throughput** |
| **Windows + SMB** | **FSx for Windows File Server** |
| **Windows + Active Directory + file shares** | **FSx for Windows** |
| **NoSQL key-value/document database** | **DynamoDB** |
| **DynamoDB microsecond read acceleration** | **DAX** |
| **DynamoDB item changes/events** | **DynamoDB Streams** |
| **Containers + task definitions + cluster** | **ECS** |
| **ECS tasks running on EC2** | **ECS EC2 launch type** |
| **ECS dynamically manages EC2 capacity based on task demand** | **ECS Capacity Provider** |
| **Filesystem mount point in ECS task** | **EFS/other supported filesystem — not DynamoDB** |
| **Many concurrent clients + aggregate filesystem throughput** | **EFS Max I/O** |
| **Throughput independent of filesystem size** | **EFS Provisioned Throughput** |

---

# 6. Side-by-Side Option Analysis Table

| Option | Underlying Idea | Good? | One-line reason |
| --- | --- | --- | --- |
| **A** | DynamoDB + DAX + Streams as mounted storage | ❌ | DynamoDB is a database, not a mountable shared filesystem |
| **B** | EFS + Bursting + General Purpose | ⚠️ | EFS is correct, but the highly parallel HPC workload favors Max I/O and provisioned throughput |
| **C** | FSx for Windows + SMB | ❌ | No Windows/SMB requirement; EFS is the better fit for this shared ECS workload |
| **D** | EFS + Provisioned Throughput + Max I/O | ✅ | Best match for hundreds of concurrent ECS tasks with high-frequency shared I/O |

---

# 7. Clue-by-Clue Post-Mortem

### "High-Performance Computing (HPC) system"

🔥 **Trigger:**

```
HPC
 ↓
High parallelism
 ↓
High aggregate throughput
 ↓
High-performance filesystem
```

This makes **EFS Max I/O** attractive.

---

### "orchestrating several Amazon ECS tasks"

Think:

```
ECS
 ↓
Many containers
```

---

### "EC2 launch type"

This tells you the tasks are running on EC2-backed ECS capacity.

It makes shared network storage particularly relevant.

---

### "ECS Capacity Provider"

This is primarily about **compute capacity**, not storage.

Don't confuse:

```
Capacity Provider → EC2 capacity
```

with:

```
EFS → storage
```

---

### "Users around the globe"

This is a distractor unless the question explicitly asks for multi-Region storage.

Don't immediately jump to:

```
Global users → Global Tables
```

That's a database replication problem, not the stated requirement.

---

### "hundreds of ECS tasks will run most of the time"

🔥🔥 Huge clue.

```
Hundreds of clients
       ↓
Shared storage
       ↓
High concurrency
       ↓
EFS
```

And specifically:

```
High concurrency + HPC
       ↓
Max I/O
```

---

### "high-frequency read and write operations"

🔥🔥 Another major clue.

This is about **storage I/O performance**, not simply storage capacity.

```
Frequent I/O
     ↓
Throughput/performance
     ↓
EFS performance + throughput modes
```

---

### "shared storage system"

🔥🔥 Probably the strongest clue.

The word **shared** is critical.

```
Task 1 ─┐
Task 2 ─┤
Task 3 ─┼──→ SAME FILESYSTEM
Task N ─┘
```

That's exactly the use case for a network filesystem.

---

### "output data of each ECS task is around 10 MB"

This tells us individual objects/files aren't enormous.

It doesn't point toward block storage or a database.

---

### "obsolete data is eventually archived and deleted"

This means storage size is controlled over time.

---

### "total storage size will not exceed 10 TB"

This is an important throughput clue.

You don't necessarily have an enormous amount of stored data, but you can have **very high I/O demand**.

Therefore:

```
Storage size
    ≠
Throughput requirement
```

This is why **Provisioned Throughput** becomes attractive.

---

### Option B vs D

This is the final battle:

```
B:
EFS
+ Bursting
+ General Purpose

D:
EFS
+ Provisioned
+ Max I/O
```

HPC + hundreds of concurrent tasks + high-frequency I/O:

```
              D
              ↓
     Provisioned Throughput
              +
           Max I/O
```

---

# 8. Why the Tempting Wrong Answers Are Tempting

## Trap #1 — Option B

This is the **most tempting** wrong answer.

Why?

Because EFS is absolutely correct:

```
ECS
 ↓
EFS
```

So once someone recognizes EFS, they may stop reading.

But the exam asks:

> **MOST suitable**
> 

The question gives extra performance clues specifically to distinguish EFS configurations.

### The trap

You see:

> "shared storage"
> 

and think:

> "EFS!"
> 

Correct.

But then you must continue:

> "What type of EFS configuration?"
> 

Now:

```
Hundreds of tasks
+
HPC
+
High-frequency I/O
        ↓
Max I/O
```

And:

```
High throughput requirement
relative to storage size
        ↓
Provisioned Throughput
```

Therefore D beats B.

---

## Trap #2 — Option A

This distractor exploits the phrase:

> "high-performance"
> 

DynamoDB + DAX sounds extremely fast.

You might think:

```
DAX = microseconds
HPC = fast
Therefore DAX!
```

❌ Wrong category.

DAX makes **database reads** faster.

The requirement is:

> **shared filesystem storage**
> 

That's completely different.

Compare:

```
DAX:
Application → DAX → DynamoDB
                  ↑
              database items
```

versus:

```
EFS:
Task 1 ─┐
Task 2 ─┤
Task 3 ─┼→ EFS → files/directories
Task N ─┘
```

The word **"mount point"** kills Option A immediately.

---

# 9. Final Answer Recap + One-Line Memory Trick

## ✅ Correct Answer: Option D

> **Launch an Amazon EFS with Provisioned Throughput mode and set the performance mode to Max I/O. Configure the EFS filesystem as the container mount point in the ECS task definition.**
> 

### Why?

```
HPC
 +
Hundreds of ECS tasks
 +
Shared filesystem
 +
High-frequency reads/writes
 +
≤10 TB storage
        │
        ▼
      EFS
        │
        ├── Max I/O
        │
        └── Provisioned Throughput
```

### 🧠 Memory Trick

> **"Many ECS workers + shared files + HPC → EFS Max I/O."**
> 

And:

> **"High throughput despite a relatively limited dataset → EFS Provisioned Throughput."**
> 

### The critical distinction

```
DynamoDB → database
EFS      → shared filesystem
FSx      → specialized filesystem

DAX      → DynamoDB cache
```

---

# 10. Bonus: Related Exam Traps to Watch For

### Trap 1 — "Multiple EC2 instances need a shared filesystem"

Answer:

```
EFS
```

Not EBS.

---

### Trap 2 — "Windows applications need SMB and Active Directory"

Answer:

```
FSx for Windows File Server
```

Not EFS.

---

### Trap 3 — "DynamoDB reads need microsecond latency"

Answer:

```
DAX
```

Not EFS.

---

### Trap 4 — "Small EFS dataset but extremely high throughput requirement"

Answer:

```
EFS Provisioned Throughput
```

because throughput can be provisioned independently of storage size.

---

### Trap 5 — "HPC workload with many thousands of concurrent filesystem clients"

Think:

```
EFS
 +
Max I/O
```

rather than automatically choosing General Purpose.

---

# 🔥 FINAL FLASHCARD — EFS / STORAGE DECISION TABLE

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **Shared filesystem across many ECS/EC2 resources** | **EFS** |
| **Linux + NFS + shared files** | **EFS** |
| **HPC + highly parallel filesystem workload** | **EFS Max I/O** |
| **Hundreds/thousands of concurrent filesystem clients** | **EFS Max I/O** |
| **High throughput but relatively small amount of stored data** | **EFS Provisioned Throughput** |
| **General EFS workload + lower latency** | **EFS General Purpose** |
| **Throughput linked to amount of data stored** | **EFS Bursting Throughput** |
| **Windows + SMB + Active Directory** | **FSx for Windows File Server** |
| **NoSQL database** | **DynamoDB** |
| **DynamoDB read acceleration / microseconds** | **DAX** |
| **DynamoDB item-level change events** | **DynamoDB Streams** |
| **Containers/tasks/clusters** | **ECS** |
| **ECS tasks backed by EC2** | **ECS EC2 launch type** |
| **ECS automatically obtains EC2 capacity based on task demand** | **ECS Capacity Provider** |
| **Container mount point** | **Filesystem such as EFS — NOT DynamoDB** |
| **"Shared storage"** | **Filesystem, usually EFS** |
| **"Shared storage + HPC + high-frequency I/O"** | **EFS Max I/O + appropriate throughput mode** |

## 🎯 10-second SAA decision tree

```
Does the application need a DATABASE?
        │
       YES
        │
        ├── NoSQL → DynamoDB
        │              │
        │              └── Faster reads → DAX
        │
       NO
        │
        ▼
Does it need SHARED FILE STORAGE?
        │
       YES
        │
        ├── Linux/NFS → EFS
        │                  │
        │                  ├── General workload → General Purpose
        │                  │
        │                  └── HPC / massive parallelism
        │                         → Max I/O
        │
        └── Windows/SMB/AD
                   ↓
            FSx for Windows
```

**For this question:**

```
HPC
 ↓
Hundreds of ECS tasks
 ↓
Shared filesystem
 ↓
High-frequency I/O
 ↓
EFS
 ↓
Max I/O
 ↓
Provisioned Throughput
 ↓
✅ OPTION D
```
