# Part: 1

## 1. First: What exactly is EBS?

**Amazon Elastic Block Store (EBS)** is **persistent block-level storage designed for EC2**.

Think:

```
                    EC2 Instance
                         │
                ┌────────┴────────┐
                │                 │
             EBS Volume       Instance Store
             Persistent         Temporary
             Storage            Storage
                │                 │
          Data survives      Data lost when
          EC2 stop*          instance terminates
```

- Whether data survives **termination** depends on the EBS volume's **DeleteOnTermination** setting. By default, the root volume is generally deleted when the instance is terminated, while additional EBS volumes are generally retained—but the setting can be changed.

### The key distinction

| Storage | Type | Persistent? | Typical use |
| --- | --- | --- | --- |
| **EBS** | Block | ✅ | EC2 OS, database, applications |
| **Instance Store** | Block | ❌ | Temporary/high-speed scratch data |
| **S3** | Object | ✅ | Files, backups, data lake, static objects |
| **EFS** | File | ✅ | Shared file system |
| **FSx** | File | ✅ | Specialized file systems |

### SAA-C03 mental model

> **EC2 + persistent block storage → EBS**
> 

---

# 2. The EBS Volume Types You Need to Know

For SAA-C03, organize them into just **two families**:

```
                       EBS
                        │
             ┌──────────┴──────────┐
             │                     │
             ▼                     ▼
           SSD                    HDD
             │                     │
       ┌─────┴─────┐          ┌────┴────┐
       │           │          │         │
      gp3         io2        st1       sc1
      gp2         io1
```

And there is also:

```
Magnetic
(previous-generation)
```

The **most important decision** is:

> **Do I need IOPS or throughput?**
> 

---

# 3. MASTER TABLE — One-Time Seeing Should Give You the Idea

| EBS Type | Family | Think | Best For | SAA-C03 Clue |
| --- | --- | --- | --- | --- |
| 🟢 **gp3** | SSD | **General Purpose** | Most workloads | General purpose, boot, web/app servers |
| 🟢 **gp2** | SSD | General Purpose | Older/default general workloads | Legacy/general-purpose SSD |
| 🔴 **io2** | SSD | **High IOPS** | Critical databases | High IOPS, low latency, mission-critical DB |
| 🔴 **io1** | SSD | Provisioned IOPS | I/O-intensive workloads | High IOPS, older provisioned-IOPS choice |
| 🟠 **st1** | HDD | **Throughput** | Sequential workloads | Big data, logs, data warehouses |
| ⚪ **sc1** | HDD | **Cold** | Infrequently accessed data | Lowest-cost HDD, infrequent access |
| 🟤 **Magnetic** | HDD | **Legacy** | Previous-generation workloads | Previous generation / legacy |

### Memorize this:

> **gp3 = General**
> 
> 
> **io2 = IOPS**
> 
> **st1 = Throughput**
> 
> **sc1 = Cold**
> 
> **Magnetic = Legacy**
> 

If you remember only this, you can solve a huge percentage of EBS questions.

---

# 4. 🟢 GP3 — General Purpose SSD

## What is gp3?

**gp3 is the default "safe choice" for general-purpose workloads.**

Think:

```
Normal application
       ↓
    gp3 SSD
       ↓
Balanced price + performance
```

### Typical workloads

- EC2 boot volumes
- Web servers
- Application servers
- Development/test
- Small/medium databases
- General-purpose applications

### Exam clue

If the question says:

> "General purpose"
> 

> "Most workloads"
> 

> "Boot volume"
> 

> "Web/application server"
> 

> "Need SSD but no extreme IOPS requirement"
> 

Think:

# → GP3

---

# 5. Why GP3 is Better Than GP2 for Many New Deployments

Both are General Purpose SSD.

But **gp3 is the newer generation**.

A very important conceptual difference:

### GP2

Performance is tied more closely to volume size.

```
Larger volume
     ↓
More baseline IOPS
```

### GP3

You can independently provision:

```
Storage capacity
       +
IOPS
       +
Throughput
```

So:

```
gp3
 ├── Storage
 ├── IOPS
 └── Throughput
      ↑
 independently configurable
```

### SAA-C03 clue

If a question says:

> "Need to independently configure storage, IOPS and throughput"
> 

Think:

# → GP3

---

# 6. 🔴 IO2 — Provisioned IOPS SSD

This is one of the **most important EBS types for SAA-C03**.

Think:

> **IO2 = I/O intensive**
> 

```
                 Database
                    │
          Very high I/O requirement
                    │
                   io2
```

Use io2 when the application needs:

- High IOPS
- Consistent performance
- Low latency
- Mission-critical database storage
- I/O-intensive workloads

### Typical examples

```
Large relational DB
       ↓
     io2

Critical transaction system
       ↓
     io2

High-performance database
       ↓
     io2
```

### Exam clues

Look for:

> **High IOPS**
> 

> **Provisioned IOPS**
> 

> **Consistent low latency**
> 

> **Mission-critical database**
> 

> **I/O intensive**
> 

> **High-performance relational database**
> 

→ **IO2**

---

# 7. IO1 vs IO2

Both are:

> **Provisioned IOPS SSD**
> 

```
io1
 │
 └── Provisioned IOPS

io2
 │
 └── Provisioned IOPS
```

For modern AWS designs, **io2 is generally preferred** when you need provisioned IOPS with higher durability/availability characteristics.

For SAA-C03 questions:

| Requirement | Think |
| --- | --- |
| General-purpose SSD | **gp3** |
| High IOPS | **io2** |
| Mission-critical DB | **io2** |
| Provisioned IOPS | **io2/io1** |

If the question gives you a choice between **gp3 and io2** and emphasizes **high IOPS / critical DB**, choose **io2**.

---

# 8. 🟠 ST1 — Throughput Optimized HDD

This is where many candidates make mistakes.

ST1 is **not primarily about IOPS**.

It's about:

# **THROUGHPUT**

Think:

```
Huge amount of sequential data
             ↓
           st1
             ↓
       High throughput
```

### Typical workloads

- Big data
- Data warehouses
- Log processing
- ETL workloads
- Large sequential workloads
- Streaming workloads

### Example

Suppose you have:

```
100 TB of log data

Application reads:
1 → 2 → 3 → 4 → 5 → 6 → ...
```

This is largely **sequential access**.

Think:

# → ST1

---

# 9. ST1 vs IO2 — VERY IMPORTANT

This distinction is extremely common in exam questions.

### IO2

Think:

> **I/O operations**
> 

```
Random / intensive I/O
        ↓
      IO2
```

### ST1

Think:

> **Amount of data transferred**
> 

```
Large sequential data
        ↓
       ST1
```

### Quick comparison

| Requirement | Answer |
| --- | --- |
| High IOPS | 🔴 **io2** |
| Low latency | 🔴 **io2** |
| Database | 🔴 **io2** |
| Sequential workload | 🟠 **st1** |
| High throughput | 🟠 **st1** |
| Big data | 🟠 **st1** |
| Log processing | 🟠 **st1** |

### Memory trick

> **IO2 = "I/O"**
> 

> **ST1 = "Stream Throughput"**
> 

---

# 10. ⚪ SC1 — Cold HDD

SC1 is the **cold storage option within EBS HDD volumes**.

Think:

```
Infrequent access
       +
Lowest-cost HDD
       ↓
      sc1
```

Use it when:

- Data is accessed infrequently
- You need large capacity
- Performance requirements are low
- Cost is more important than performance

### Examples

```
Old datasets
Historical data
Rarely accessed files
Large cold datasets
        ↓
       sc1
```

### Exam clue

If you see:

> "Infrequently accessed"
> 

> "Lowest-cost HDD"
> 

> "Cold data"
> 

> "Cost-sensitive"
> 

Think:

# → SC1

---

# 11. ST1 vs SC1 — Another VERY Important Comparison

|  | **ST1** | **SC1** |
| --- | --- | --- |
| Type | HDD | HDD |
| Main purpose | High throughput | Low-cost cold storage |
| Access | Frequently processed | Infrequent |
| Performance | Higher | Lower |
| Cost | More | Less |
| Big data | ✅ | Sometimes |
| Sequential workload | ✅ | Possible |
| Infrequent access | ❌ Main clue | ✅ Main clue |

### Easy memory

> **ST1 = Speed/Throughput**
> 

> **SC1 = Slow/Cold**
> 

---

# 12. 🟤 Magnetic — Previous Generation

This is the one in your reference question.

**Magnetic** is a **previous-generation EBS volume type**.

Think:

```
Magnetic
   ↓
Old EBS
   ↓
Legacy workloads
```

It is important mainly because **older SAA questions may mention it**.

Don't confuse it with modern EBS recommendations.

### Your reference question

It says:

> "Magnetic volumes provide the lowest cost per gigabyte ... ideal for workloads where data is accessed infrequently."
> 

This reflects the **older EBS volume landscape/question wording**.

For modern exam preparation, the more important association is:

```
Infrequent access + lowest-cost HDD
                 ↓
                SC1
```

And:

```
Previous-generation EBS
                 ↓
             Magnetic
```

### Exam strategy

If you see **Magnetic**:

> Think **legacy / previous generation**.
> 

If you see:

> **"Infrequently accessed + lowest-cost HDD"**
> 

Think:

# **SC1**

---

# 13. The Most Important EBS Decision Tree

When you see an EBS question, run this in your head:

```
                 EBS requirement
                       │
                       ▼
             Do I need SSD performance?
                  /           \
                YES            NO
                 │              │
                 ▼              ▼
          General or       HDD workload
          high IOPS?           │
             /   \             │
           /       \            │
       General    High          │
          │       IOPS          │
          ▼        ▼            │
        GP3       IO2           │
                                │
                         ┌──────┴──────┐
                         │             │
                    High throughput  Infrequent
                         │             │
                         ▼             ▼
                        ST1           SC1
```

That's the **core EBS decision tree**.

---

# 14. EBS MASTER TABLE — Exam Version

Save this table mentally:

| If question says... | Choose |
| --- | --- |
| General purpose | 🟢 **gp3** |
| Boot volume | 🟢 **gp3** |
| Web server | 🟢 **gp3** |
| Application server | 🟢 **gp3** |
| Most workloads | 🟢 **gp3** |
| Balanced price/performance | 🟢 **gp3** |
| High IOPS | 🔴 **io2** |
| Provisioned IOPS | 🔴 **io2/io1** |
| Low latency | 🔴 **io2** |
| I/O-intensive database | 🔴 **io2** |
| Mission-critical database | 🔴 **io2** |
| High throughput | 🟠 **st1** |
| Sequential workload | 🟠 **st1** |
| Big data | 🟠 **st1** |
| Data warehouse | 🟠 **st1** |
| Log processing | 🟠 **st1** |
| Infrequent access | ⚪ **sc1** |
| Cold data | ⚪ **sc1** |
| Lowest-cost HDD | ⚪ **sc1** |
| Previous-generation | 🟤 **Magnetic** |
| Legacy EBS | 🟤 **Magnetic** |

---
