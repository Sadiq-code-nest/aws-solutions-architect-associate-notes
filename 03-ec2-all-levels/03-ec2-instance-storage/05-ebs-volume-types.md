# Amazon EBS Volume Types — SAA-C03 Exam Revision Note

The easiest way to understand EBS volume types is to compare **3 things**:

> **Size (GiB) | IOPS | Throughput**
> 

And first remember the big split:

| Type | Technology | Main idea |
| --- | --- | --- |
| **gp2 / gp3** | SSD | General-purpose balance of cost + performance |
| **io1 / io2 Block Express** | SSD | High, consistent IOPS for critical workloads |
| **st1** | HDD | High sequential throughput at low cost |
| **sc1** | HDD | Lowest-cost HDD for infrequently accessed data |

### 🚨 Boot-volume rule

**Only `gp2/gp3` and `io1/io2` can be used as EBS boot/root volumes.**

❌ `st1` cannot be a boot volume

❌ `sc1` cannot be a boot volume

---

# 1. gp3 — General Purpose SSD ⭐⭐⭐⭐⭐

### What is it?

`gp3` is the modern general-purpose SSD choice for most workloads.

It gives you a balance between:

- cost
- low latency
- IOPS
- throughput

### Key numbers

**Size:** 1 GiB – 16 TiB

**Baseline:**

- **3,000 IOPS**
- **125 MiB/s throughput**

You can increase:

- IOPS → up to **16,000**
- Throughput → up to **1,000 MiB/s**

Most importantly:

> **IOPS and throughput can be configured independently of volume size.**
> 

### Scenario

A web application needs a normal persistent SSD disk for its EC2 instances without requiring extremely high database IOPS.

→ **gp3**

### Exam trigger

**"General-purpose SSD"**

**"Cost-effective + low latency"**

**"Boot volume"**

**"Development/test"**

**"Virtual desktop"**

→ **gp3**

### Memory

> **gp3 = General Purpose + 3,000 baseline**
> 

---

# 2. gp2 — General Purpose SSD

`gp2` is also general-purpose SSD, but its performance model is different from gp3.

### Key concept

With gp2:

> **IOPS is linked to volume size.**
> 

The relationship is approximately:

> **3 IOPS per GiB**
> 

So:

```
100 GiB
 ↓
300 IOPS baseline

1,000 GiB
 ↓
3,000 IOPS

5,334 GiB
 ↓
~16,000 IOPS
```

Maximum IOPS:

**16,000 IOPS**

Small gp2 volumes can **burst up to 3,000 IOPS**.

### Scenario

You have an older application already using gp2 and its storage/performance requirements are modest.

→ **gp2**

For a **new** general-purpose workload, **gp3** is generally the preferred choice when appropriate.

### Exam trigger

**"3 IOPS per GiB"**

**"IOPS linked to size"**

**"Small volume can burst to 3,000 IOPS"**

→ **gp2**

### Memory

> **gp2 = Performance grows with GB**
> 

---

# 3. gp2 vs gp3 ⭐⭐⭐⭐⭐

|  | **gp2** | **gp3** |
| --- | --- | --- |
| SSD | ✅ | ✅ |
| General purpose | ✅ | ✅ |
| Maximum IOPS | **16,000** | **16,000** |
| Baseline IOPS | Depends on size | **3,000** |
| IOPS tied to size? | **Yes** | **No** |
| Throughput tied to size? | More coupled | **Can configure independently** |
| Typical choice for new workload | Older generation | **Preferred general-purpose choice** |
| Boot volume | ✅ | ✅ |

### Exam shortcut

> **"3 IOPS/GiB" → gp2**
> 

> **"3,000 baseline + independently configure IOPS/throughput" → gp3**
> 

---

# 4. io1 — Provisioned IOPS SSD ⭐⭐⭐⭐⭐

### What is it?

`io1` is for applications that need **sustained, predictable high IOPS**.

Instead of relying primarily on volume size to determine performance, you explicitly provision the required IOPS.

### Best for

- Critical business applications
- High-performance databases
- Storage-sensitive applications
- Workloads requiring consistent I/O performance
- Applications requiring **more than 16,000 IOPS**

### Key numbers

**Size:** 4 GiB – 16 TiB

Maximum PIOPS:

- **64,000 IOPS** on supported Nitro-based EC2 instances
- **32,000 IOPS** on other instances

You can increase IOPS independently from storage size.

### Scenario

A critical database requires sustained, predictable high I/O performance.

→ **io1**

### Exam trigger

**"Provisioned IOPS"**

**"Sustained IOPS"**

**"Consistent storage performance"**

**"Critical database"**

**"More than 16,000 IOPS"**

→ **io1/io2**

### Memory

> **io = IOPS**
> 

---

# 5. io2 Block Express ⭐⭐⭐⭐⭐

### What is it?

`io2 Block Express` is designed for **very high-performance, mission-critical workloads**.

It provides:

- extremely high IOPS
- very low latency
- high throughput
- predictable performance

### Key numbers

**Size:** 4 GiB – 64 TiB

Maximum:

> **256,000 IOPS**
> 

IOPS-to-size ratio:

> **1,000 IOPS per GiB**
> 

Latency:

> **Sub-millisecond**
> 

It also supports:

> **EBS Multi-Attach**
> 

### Scenario

A mission-critical database needs **256,000 IOPS with sub-millisecond latency**.

→ **io2 Block Express**

### Exam trigger

**"256,000 IOPS"**

**"Sub-millisecond latency"**

**"Mission-critical database"**

**"Extremely high IOPS"**

→ **io2 Block Express**

### Memory

> **io2 = extreme IOPS + mission critical**
> 

---

# 6. io1 vs io2 Block Express

|  | **io1** | **io2 Block Express** |
| --- | --- | --- |
| Technology | SSD | SSD |
| Category | Provisioned IOPS | Provisioned IOPS |
| Purpose | High sustained IOPS | **Very high mission-critical IOPS** |
| Max IOPS | 64,000 on supported Nitro | **256,000** |
| Max size | 16 TiB | **64 TiB** |
| Latency | Low | **Sub-millisecond** |
| Multi-Attach | Supported | **Supported** |
| Typical clue | **High sustained IOPS** | **Extreme performance / 256K IOPS** |

---

# 7. st1 — Throughput Optimized HDD ⭐⭐⭐⭐⭐

### What is it?

`st1` is an **HDD** volume designed for:

> **frequently accessed, throughput-intensive workloads**
> 

The important word is:

# THROUGHPUT

It is not designed for high random IOPS.

### Key numbers

**Size:** 125 GiB – 16 TiB

Maximum:

- **500 MiB/s throughput**
- **500 IOPS**

### Good use cases

- Big Data
- Data warehouses
- Log processing
- Large sequential read/write workloads

### Scenario

A company processes huge log files and needs high **sequential throughput** rather than high random IOPS.

→ **st1**

### Exam trigger

**"High sequential read/write"**

**"Throughput-intensive"**

**"Big Data"**

**"Data warehouse"**

**"Log processing"**

→ **st1**

### Memory

> **st1 = Streaming Throughput**
> 

Think:

```
Huge files
   ↓
Sequential I/O
   ↓
ST1
```

---

# 8. sc1 — Cold HDD ⭐⭐⭐⭐⭐

### What is it?

`sc1` is an HDD volume optimized for:

> **infrequently accessed data where lowest cost is important**
> 

### Key numbers

**Size:** 125 GiB – 16 TiB

Maximum:

- **250 MiB/s throughput**
- **250 IOPS**

### Scenario

A company stores old datasets that are accessed occasionally and wants the **lowest-cost EBS HDD** option.

→ **sc1**

### Exam trigger

**"Infrequently accessed"**

**"Lowest cost"**

**"Cold data on EBS"**

→ **sc1**

### Memory

> **sc1 = Cold + Cheapest HDD**
> 

---

# 9. st1 vs sc1

This is an easy exam comparison.

|  | **st1** | **sc1** |
| --- | --- | --- |
| Type | HDD | HDD |
| Main purpose | **Throughput-intensive** | **Infrequently accessed** |
| Access pattern | Frequently accessed | Less frequently accessed |
| Max throughput | **500 MiB/s** | 250 MiB/s |
| Max IOPS | **500** | 250 |
| Cost | Low | **Lowest-cost EBS HDD** |
| Typical workloads | Big Data, data warehouse, logs | Cold/infrequently accessed data |
| Boot volume | ❌ | ❌ |

### Exam shortcut

> **HIGH THROUGHPUT → st1**
> 

> **LOWEST COST + INFREQUENT ACCESS → sc1**
> 

---

# 🔥 All EBS Volume Types — Master Table

| Volume | Technology | Max IOPS | Max Throughput | Main purpose | **Exam trigger** | Boot? |
| --- | --- | --- | --- | --- | --- | --- |
| **gp3** | SSD | **16K** | **1,000 MiB/s** | General-purpose SSD | **General purpose / cost-effective / low latency** | ✅ |
| **gp2** | SSD | **16K** | — | General-purpose SSD, IOPS linked to size | **3 IOPS/GiB / burst 3K** | ✅ |
| **io1** | SSD | **64K** on supported Nitro | — | Provisioned IOPS | **Sustained / predictable IOPS / critical DB** | ✅ |
| **io2 Block Express** | SSD | **256K** | — | Extreme-performance mission-critical workloads | **256K / sub-ms / Multi-Attach** | ✅ |
| **st1** | HDD | **500** | **500 MiB/s** | Throughput-intensive | **Sequential / Big Data / logs / warehouse** | ❌ |
| **sc1** | HDD | **250** | **250 MiB/s** | Infrequently accessed, lowest-cost HDD | **Cold / infrequent / lowest cost** | ❌ |

---

# 🧠 The Ultimate EBS Decision Tree

When you see an EBS question, ask:

```
                    What does the workload need?
                              │
             ┌────────────────┴────────────────┐
             │                                 │
         SSD needed?                       HDD okay?
             │                                 │
       ┌─────┴─────┐                    ┌──────┴──────┐
       │           │                    │             │
   General      High IOPS          High throughput   Infrequent
   purpose      required              required        access
       │           │                    │             │
      gp3       io1/io2               st1            sc1
       │           │
       │       Extreme?
       │           │
       │     256K IOPS /
       │     sub-ms latency
       │           ↓
       │        io2 Block Express
       │
       └── Older gp2 / 3 IOPS per GiB
```

---

# 🚨 Exam Trigger → Answer

| If the question says... | Think... |
| --- | --- |
| **"General-purpose SSD"** | **gp3** |
| **"Cost-effective + low latency"** | **gp3** |
| **"Boot volume"** | **gp3/gp2/io1/io2** |
| **"3 IOPS per GiB"** | **gp2** |
| **"Small volume bursts to 3,000 IOPS"** | **gp2** |
| **"IOPS and throughput independently configurable"** | **gp3** |
| **"Provisioned IOPS"** | **io1/io2** |
| **"Sustained IOPS"** | **io1/io2** |
| **"Critical database"** | **io1/io2** |
| **"More than 16,000 IOPS"** | **io1/io2** |
| **"256,000 IOPS"** | **io2 Block Express** |
| **"Sub-millisecond latency"** | **io2 Block Express** |
| **"Multi-Attach"** | **io1/io2** |
| **"High sequential throughput"** | **st1** |
| **"Big Data"** | **st1** |
| **"Data warehouse"** | **st1** |
| **"Log processing"** | **st1** |
| **"Infrequently accessed EBS data"** | **sc1** |
| **"Lowest-cost HDD"** | **sc1** |
| **"Cold data"** | **sc1** |
| **"Cannot be a boot volume"** | **st1/sc1** |

---

## ⚡ 10-Second Brain Shortcut

Memorize this:

> **GP = General Purpose**
> 

> **IO = IOPS**
> 

> **ST = Throughput / Streaming**
> 

> **SC = Cold / Cheapest**
> 

```
gp3 → NORMAL

gp2 → 3 IOPS/GiB

io1 → HIGH IOPS

io2 → EXTREME IOPS

st1 → HIGH THROUGHPUT

sc1 → CHEAP + COLD
```

### Your reference question

> **"High, sequential read and write access to very large data sets on local storage"**
> 

The important keywords are **large datasets + sequential access + throughput**, so the EBS family member to recognize is **st1 (Throughput Optimized HDD)**. If the question instead says **high IOPS / random I/O / database latency**, move toward **io1/io2**.
