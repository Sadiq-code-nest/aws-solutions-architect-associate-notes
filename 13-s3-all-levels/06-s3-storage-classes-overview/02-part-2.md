# 11. COMPLETE S3 STORAGE CLASS COMPARISON

| Feature | Standard | Standard-IA | One Zone-IA | Intelligent-Tiering | Glacier Instant | Glacier Flexible | Glacier Deep Archive | Express One Zone |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Main purpose | Frequent access | Infrequent | Infrequent + cheaper | Unknown access | Archive + instant | Archive | Long-term archive | High performance |
| Access frequency | Frequent | Infrequent | Infrequent | Unknown/changing | Rare | Very rare | Extremely rare | Depends on workload |
| Retrieval | Immediate | Immediate | Immediate | Immediate for active tiers | Milliseconds | Minutes–hours | Hours | Very low latency |
| AZs | Multiple | Multiple | **1** | Multiple | Multiple | Multiple | Multiple | **1** |
| Durability | 11 9's | 11 9's | 11 9's | 11 9's | 11 9's | 11 9's | 11 9's | 11 9's |
| Availability | **99.99%** | 99.9% | 99.5% | 99.9% | 99.9% | 99.99% | 99.99% | 99.95% |
| Retrieval charge | No | Yes | Yes | **No retrieval fee** | Yes | Yes | Yes | Depends |
| Minimum duration | None | 30 days | 30 days | Tier dependent | 90 days | 90 days | **180 days** | None |
| Cost | Higher | Lower | Lower | Optimized automatically | Low | Very low | **Lowest** | Performance-focused |
| Best clue | Frequent | Infrequent + instant | Re-creatable | Unknown pattern | Archive + instant | Archive + hours | Long-term archive | High performance |

---

# 12. The Most Important Decision Tree

When you see an S3 storage-class question, use this:

```
                 What is the requirement?
                         │
          ┌──────────────┴──────────────┐
          │                             │
      Frequently                    Infrequently
      accessed?                     accessed?
          │                             │
          ▼                             ▼
    S3 Standard                Need automatic optimization?
                                        │
                              ┌─────────┴─────────┐
                              │                   │
                             YES                  NO
                              │                   │
                              ▼                   ▼
                    Intelligent-Tiering    Need instant retrieval?
                                                  │
                                      ┌───────────┴──────────┐
                                      │                      │
                                     YES                     NO
                                      │                      │
                                      ▼                      ▼
                              Standard-IA /            Glacier
                              One Zone-IA              classes
```

For Glacier:

```
                  Archive data
                       │
                       ▼
             Need millisecond access?
                  /           \
                YES            NO
                 │              │
                 ▼              ▼
       Glacier Instant      How long can
       Retrieval            retrieval take?
                              │
                     ┌────────┴────────┐
                     │                 │
                  Minutes/Hours     12–48 hours
                     │                 │
                     ▼                 ▼
              Glacier Flexible    Deep Archive
```

---

# 13. Standard-IA vs One Zone-IA

This is a **classic SAA-C03 comparison**.

| Requirement | Standard-IA | One Zone-IA |
| --- | --- | --- |
| Infrequent access | ✅ | ✅ |
| Fast retrieval | ✅ | ✅ |
| Multiple AZs | ✅ | ❌ |
| Single AZ | ❌ | ✅ |
| Cheaper | ❌ | ✅ |
| Data can be recreated | Not required | **Usually yes** |
| DR primary backup | Better | Usually not |
| Secondary/re-creatable data | Good | **Excellent** |

### Exam logic

> Important backup that must survive an AZ failure?
> 

**Standard-IA**

> Data can easily be recreated and cost should be minimized?
> 

**One Zone-IA**

---

# 14. Glacier Instant vs Flexible vs Deep Archive

| Requirement | Choose |
| --- | --- |
| Archive + milliseconds | **Glacier Instant Retrieval** |
| Archive + 1–5 minutes possible | **Glacier Flexible – Expedited** |
| Archive + 3–5 hours acceptable | **Glacier Flexible – Standard** |
| Archive + 5–12 hours acceptable | **Glacier Flexible – Bulk** |
| Long-term archive + ~12 hours | **Deep Archive – Standard** |
| Long-term archive + ~48 hours | **Deep Archive – Bulk** |

### Easy memory

> **Instant = Instant**
> 

> **Flexible = Flexible waiting**
> 

> **Deep Archive = Deepest/longest storage**
> 

---

# 15. S3 Lifecycle Rules

You don't have to manually move objects between storage classes.

Use:

**S3 Lifecycle Configuration**

Example:

```
Day 0
   ↓
S3 Standard
Frequently accessed

Day 30
   ↓
S3 Standard-IA
Less frequently accessed

Day 90
   ↓
S3 Glacier

Day 365
   ↓
S3 Glacier Deep Archive
Long-term archive
```

Lifecycle rules can be based on things such as:

- Object age
- Prefix
- Object tags
- Current/noncurrent versions

### Exam clue

> "Automatically transition objects to cheaper storage classes based on age"
> 

→ **S3 Lifecycle**

---

# 16. Storage Class vs Lifecycle — Don't Confuse

### Storage Class

Answers:

> **Where/how should this object be stored?**
> 

Example:

**S3 Standard**

### Lifecycle

Answers:

> **When should S3 automatically change the storage class or delete the object?**
> 

Example:

```
0–30 days → Standard
30–90 days → Standard-IA
90+ days → Glacier
365+ days → Delete
```

---

# 17. Real-World Example

Imagine an e-commerce company.

### Product images

Users access them constantly.

→ **S3 Standard**

### Old order backups

Rarely accessed but must be available quickly during recovery.

→ **S3 Standard-IA**

### Regeneratable thumbnails

Rarely accessed and can be recreated.

→ **S3 One Zone-IA**

### User-generated content with unpredictable popularity

You don't know which objects will become popular.

→ **S3 Intelligent-Tiering**

### Historical medical records

Rarely accessed but must be retrieved instantly.

→ **Glacier Instant Retrieval**

### Old company records

May need to be accessed occasionally, and hours of retrieval time are acceptable.

→ **Glacier Flexible Retrieval**

### 10-year regulatory records

Almost never accessed.

→ **Glacier Deep Archive**

### AI training dataset requiring very high performance

Very low latency and extremely high request rate.

→ **S3 Express One Zone**

---

# 18. SAA-C03 Scenario → Answer Mapping

| Scenario wording | Answer |
| --- | --- |
| Frequently accessed | **S3 Standard** |
| General-purpose storage | **S3 Standard** |
| Infrequently accessed + immediate access | **Standard-IA** |
| Backup + rapid recovery | **Standard-IA** |
| Infrequent + data can be recreated | **One Zone-IA** |
| Secondary copy | **One Zone-IA** |
| Unknown access pattern | **Intelligent-Tiering** |
| Changing access pattern | **Intelligent-Tiering** |
| Automatic cost optimization based on access | **Intelligent-Tiering** |
| Archive + milliseconds | **Glacier Instant Retrieval** |
| Archive + minutes/hours | **Glacier Flexible Retrieval** |
| Long-term regulatory archive | **Glacier Deep Archive** |
| Lowest long-term storage cost | **Glacier Deep Archive** |
| Single AZ + very high performance | **S3 Express One Zone** |
| Single-digit millisecond latency | **S3 Express One Zone** |
| AI/ML + high-performance S3 storage | **S3 Express One Zone** |

---

# 🧠 SAA-C03 Memory Map

Don't memorize eight separate names. Remember this:

```
S3
│
├── Frequently accessed
│      └── STANDARD
│
├── Infrequently accessed
│      ├── Need multi-AZ resilience
│      │       └── STANDARD-IA
│      │
│      └── Can recreate / cheapest
│              └── ONE ZONE-IA
│
├── Don't know access pattern
│      └── INTELLIGENT-TIERING
│
├── Archive
│      ├── Milliseconds
│      │       └── GLACIER INSTANT
│      │
│      ├── Minutes / Hours
│      │       └── GLACIER FLEXIBLE
│      │
│      └── Years / almost never accessed
│              └── GLACIER DEEP ARCHIVE
│
└── Ultra-high performance / single AZ
       └── S3 EXPRESS ONE ZONE
```

## 🔥 The 8-second exam rule

When you see an S3 storage-class question, ask these **4 questions in order**:

**1. Frequent?**

→ Standard

**2. Infrequent but immediate?**

→ Standard-IA / One Zone-IA

**3. Unknown access pattern?**

→ Intelligent-Tiering

**4. Archive?**

→ Instant / Flexible / Deep Archive depending on retrieval speed

And remember:

> **One Zone-IA = data can be recreated**
> 
> 
> **Intelligent-Tiering = access pattern unknown**
> 
> **Glacier Instant = archive but milliseconds**
> 
> **Glacier Flexible = archive + wait**
> 
> **Deep Archive = archive for years**
> 
> **Express One Zone = performance first**
> 

That decision logic is much more valuable for **SAA-C03** than memorizing the pricing table.
