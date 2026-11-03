# Part: 1

Absolutely. I’ll keep the **S3 storage-class section exam-focused**, but make the decision logic clear enough that you can identify the correct class from a scenario rather than memorizing names.

# Amazon S3 Storage Classes — SAA-C03 Complete Exam Note

## 1. First Understand the Big Picture

S3 storage classes are basically different **pricing + access-pattern choices** for the same type of object storage.

Think:

> **How often will I access the data? → How quickly must I retrieve it? → Can I tolerate one AZ failure? → How long will I keep it?**
> 

That leads you to the correct class.

| Requirement | Think About |
| --- | --- |
| Frequently accessed | **S3 Standard** |
| Infrequently accessed + rapid retrieval | **S3 Standard-IA** |
| Infrequently accessed + data can be recreated | **S3 One Zone-IA** |
| Unknown/changing access pattern | **S3 Intelligent-Tiering** |
| Archive but need milliseconds | **S3 Glacier Instant Retrieval** |
| Archive, retrieval can take minutes/hours | **S3 Glacier Flexible Retrieval** |
| Very long-term archive | **S3 Glacier Deep Archive** |
| Extremely low latency + huge request rate in one AZ | **S3 Express One Zone** |

---

# 2. Durability vs Availability — VERY IMPORTANT

These two are **not the same thing**.

### Durability

> **Will AWS lose my object?**
> 

S3 provides extremely high durability: **99.999999999% (11 9's)** for objects across S3 storage classes.

### Availability

> **Can I access my object right now?**
> 

Availability varies by storage class.

### Easy example

Imagine you have a photo stored in S3.

- **Durability:** Will the photo disappear? → Extremely unlikely.
- **Availability:** Can I access the photo immediately? → Depends on the storage class.

So:

> **Durability = data safety**
> 
> 
> **Availability = accessibility**
> 

### Exam trap

Don't confuse:

**S3 One Zone-IA**

with

> "Low durability."
> 

❌ Wrong.

It still provides **11 9's durability**, but the data is stored in **one AZ**, so losing that AZ can result in data loss.

---

# 3. S3 Standard

### Think:

> **Frequently accessed production data**
> 

| Feature | S3 Standard |
| --- | --- |
| Access pattern | Frequent |
| Retrieval | Immediate |
| Availability | **99.99%** |
| AZs | Multiple AZs |
| Durability | **99.999999999%** |
| Retrieval fee | No additional retrieval fee |
| Minimum storage duration | None |
| Best for | General-purpose storage |

### Examples

- Website images
- Videos
- Frequently accessed documents
- Application assets
- Big data
- Content distribution
- Mobile applications

### Scenario

> A gaming application stores user profile images that are accessed thousands of times every day.
> 

**Answer → S3 Standard**

Because the data is frequently accessed.

---

# 4. S3 Standard-IA

**IA = Infrequent Access**

Think:

> **Rarely accessed, but when needed → retrieve immediately**
> 

| Feature | Standard-IA |
| --- | --- |
| Access | Infrequent |
| Retrieval | Milliseconds |
| Availability | **99.9%** |
| AZs | Multiple AZs |
| Durability | **11 9's** |
| Retrieval fee | **Yes** |
| Minimum storage duration | **30 days** |
| Best for | Backups, DR |

### Example

A company creates a backup every night.

Normally:

> Nobody accesses the backup.
> 

But when disaster happens:

> They need the backup immediately.
> 

**S3 Standard-IA** is a good choice.

### Exam clue

> "Infrequently accessed but must be immediately available"
> 

→ **S3 Standard-IA**

---

# 5. S3 One Zone-IA

Think:

> **Infrequent + cheap + data can be recreated**
> 

| Feature | One Zone-IA |
| --- | --- |
| Access | Infrequent |
| Retrieval | Milliseconds |
| Availability | **99.5%** |
| AZs | **ONE AZ** |
| Durability | 11 9's while stored |
| Retrieval fee | Yes |
| Minimum duration | **30 days** |
| Cost | Cheaper than Standard-IA |
| Best for | Re-creatable data |

### Key difference

**Standard-IA**

> Multiple AZs
> 

**One Zone-IA**

> One AZ
> 

### Scenario

You have thumbnails generated from original images.

If thumbnails are deleted:

> You can regenerate them from the originals.
> 

Therefore:

**S3 One Zone-IA**

### Exam clue

> "Data can be recreated"
> 

> "Secondary copy"
> 

> "Lower-cost storage"
> 

> "Infrequently accessed"
> 

→ **One Zone-IA**

---

# 6. S3 Intelligent-Tiering

This is one of the most important exam concepts.

Think:

> **"I don't know how frequently the data will be accessed."**
> 

Instead of manually deciding the storage class, S3 automatically moves objects between access tiers based on access patterns.

| Feature | Intelligent-Tiering |
| --- | --- |
| Access pattern | Unknown / changing |
| Automatic movement | ✅ |
| Retrieval fees | **No retrieval charges** |
| Monitoring fee | Small monthly fee |
| Availability | 99.9% |
| Durability | 11 9's |
| Best for | Unpredictable access |

### Example

You upload 10 TB of data.

Some objects:

- accessed every day
- some once every few months
- some rarely
- some suddenly become popular

You don't know the future access pattern.

Use:

**S3 Intelligent-Tiering**

---

## Intelligent-Tiering Concept

Basic automatic tiers:

```
             S3 Intelligent-Tiering
                      │
                      ▼
             Frequent Access
                      │
             No access for 30 days
                      ▼
             Infrequent Access
                      │
             No access for 90 days
                      ▼
          Archive Instant Access
```

There are also optional:

```
Archive Access
       ↓
Deep Archive Access
```

These deeper archive tiers can be configured for longer periods.

### Exam clue

> "Unknown access pattern"
> 

> "Changing access pattern"
> 

> "Automatically optimize storage cost"
> 

> "No retrieval fees"
> 

→ **S3 Intelligent-Tiering**

---

# 7. S3 Glacier Instant Retrieval

Think:

> **Archive data + millisecond retrieval**
> 

This is where many exam questions try to confuse you.

| Feature | Glacier Instant Retrieval |
| --- | --- |
| Purpose | Archive |
| Retrieval | **Milliseconds** |
| Access | Very infrequent |
| Minimum duration | **90 days** |
| Retrieval fee | Yes |
| Best for | Data accessed approximately quarterly |

### Scenario

A company stores medical images.

They are rarely accessed, but when doctors need them:

> They need them immediately.
> 

Use:

**S3 Glacier Instant Retrieval**

### Exam clue

> Archive
> 
- 

> Millisecond retrieval
> 

→ **Glacier Instant Retrieval**

---

# 8. S3 Glacier Flexible Retrieval

Think:

> **Archive + retrieval can take minutes/hours**
> 

| Retrieval option | Approximate time |
| --- | --- |
| Expedited | **1–5 minutes** |
| Standard | **3–5 hours** |
| Bulk | **5–12 hours** |
| Minimum storage duration | **90 days** |

### Scenario

A company stores old financial records.

They rarely need them.

If they do:

> Waiting several hours is acceptable.
> 

Use:

**S3 Glacier Flexible Retrieval**

### Exam clue

> Archive
> 

> Retrieval doesn't need to be immediate
> 

> Hours are acceptable
> 

→ **Glacier Flexible Retrieval**

---

# 9. S3 Glacier Deep Archive

Think:

> **"I almost never need this data."**
> 

Designed for very long-term archival.

| Feature | Glacier Deep Archive |
| --- | --- |
| Access | Extremely rare |
| Storage cost | Very low |
| Standard retrieval | ~12 hours |
| Bulk retrieval | ~48 hours |
| Minimum duration | **180 days** |
| Retrieval fee | Yes |
| Best for | Long-term archive |

### Example

A bank must retain records for many years for regulatory purposes.

The data is almost never accessed.

→ **S3 Glacier Deep Archive**

### Exam clue

> 7–10+ years retention
> 

> Regulatory compliance
> 

> Rarely accessed
> 

> Lowest storage cost
> 

→ **Glacier Deep Archive**

---

# 10. S3 Express One Zone

This is different from the traditional storage classes.

Think:

> **Ultra-high performance + single AZ**
> 

| Feature | S3 Express One Zone |
| --- | --- |
| AZ | **Single AZ** |
| Latency | Single-digit milliseconds |
| Performance | Very high request rate |
| Availability | **99.95%** |
| Durability | **11 9's** |
| Bucket type | **Directory bucket** |
| Use case | Performance-sensitive workloads |

### Typical use cases

- AI/ML training
- High-performance computing
- Financial modeling
- Media processing
- Data-intensive applications
- Latency-sensitive applications

### Important concept

You can place compute and S3 Express One Zone storage in the **same AZ** to minimize latency.

### Exam clue

> Extremely high request rate
> 

> Single-digit millisecond latency
> 

> AI/ML
> 

> HPC
> 

> Single AZ
> 

→ **S3 Express One Zone**
