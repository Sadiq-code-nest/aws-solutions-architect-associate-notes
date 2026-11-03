# Amazon S3 Replication — CRR & SRR

### SAA-C03 Exam Notes

S3 Replication automatically copies objects from one S3 bucket to another **asynchronously**.

The two important types are:

- **CRR — Cross-Region Replication**
- **SRR — Same-Region Replication**

---

## 1. Core Concept

Imagine:

```
                    Asynchronous Replication
                           ───────────────►

Source S3 Bucket                         Destination S3 Bucket
us-east-1                                us-west-2
┌──────────────┐                         ┌──────────────┐
│ image.jpg    │ ──────────────────────► │ image.jpg    │
│ report.pdf   │                         │ report.pdf   │
└──────────────┘                         └──────────────┘
```

The source object is uploaded first.

S3 then replicates it to the destination **asynchronously**.

> **Replication is NOT synchronous.**
> 

---

# 2. CRR vs SRR

| Feature | CRR | SRR |
| --- | --- | --- |
| Full Name | Cross-Region Replication | Same-Region Replication |
| Source & Destination | Different AWS Regions | Same AWS Region |
| Example | `us-east-1 → us-west-2` | `us-east-1 → us-east-1` |
| Main purpose | DR, compliance, geographic access | Log aggregation, environment separation |
| Lower-latency access in another region | ✅ | ❌ |
| Cross-account | ✅ | ✅ |
| Asynchronous | ✅ | ✅ |
| Versioning required | ✅ Source + destination | ✅ Source + destination |
| IAM permissions required | ✅ | ✅ |

### 🧠 Memory Trick

**CRR = Cross Region → Disaster Recovery / Compliance**

**SRR = Same Region → Same-region data organization**

---

# 3. Versioning is REQUIRED

This is one of the biggest SAA-C03 exam clues.

Both buckets need Versioning:

```
Source Bucket                    Destination Bucket
Versioning ON                    Versioning ON
     │                                 ▲
     │                                 │
     └─────── Replication ─────────────┘
```

If a question says:

> "Configure S3 replication between two buckets"
> 

Immediately think:

**Versioning ON on both buckets.**

---

# 4. Cross-Region Replication (CRR)

CRR copies objects between **different AWS Regions**.

Example:

```
Customer
   │
   ▼
S3 Bucket
us-east-1
   │
   │ CRR
   │
   ▼
S3 Bucket
us-west-2
```

### Why would you use CRR?

### ① Disaster Recovery

Your primary bucket is:

```
us-east-1
```

You maintain a replica in:

```
us-west-2
```

If the application needs data from another region, you have a replicated copy.

---

### ② Lower Latency

Suppose your users are distributed globally:

```
USA Users
   │
   ▼
us-east-1 S3

European Users
   │
   ▼
eu-west-1 S3
```

CRR can help place data closer to users in another region.

---

### ③ Compliance

A company might require data to be replicated to another geographic region for organizational or regulatory requirements.

---

### ④ Cross-Account Replication

Source and destination buckets can belong to **different AWS accounts**.

```
Account A                         Account B

S3 Bucket                         S3 Bucket
   │                                  ▲
   └──────── CRR ─────────────────────┘
```

Useful for:

- Security isolation
- Centralized backup account
- Separate production/DR accounts

---

# 5. Same-Region Replication (SRR)

SRR replicates objects between buckets in the **same AWS Region**.

Example:

```
AWS Region: us-east-1

Production Bucket
       │
       │ SRR
       ▼
Test / Logging Bucket
```

### Common use cases

### Log Aggregation

Multiple applications may generate logs:

```
Application A ─┐
Application B ─┼──► S3 Buckets
Application C ─┘
                    │
                    │ SRR
                    ▼
              Central S3 Bucket
```

You can centralize data within the same region.

---

### Production → Test

```
Production S3
      │
      │ SRR
      ▼
Test S3
```

Useful when you need a replicated copy for testing or development.

---

# 6. Replication is Asynchronous

This is extremely important.

```
Application
    │
    ▼
Source S3
    │
    │  asynchronous
    │
    └──────────────► Destination S3
```

Don't assume:

> Upload → instantly available at destination.
> 

There can be replication delay.

### Exam clue

If the question says:

> "Objects must be replicated automatically to another bucket."
> 

Think:

**S3 Replication**

If it says:

> "Replication must happen immediately/synchronously."
> 

Be careful — S3 replication is **asynchronous**.

---

# 7. What happens after enabling replication?

This is a common exam trap.

Suppose:

```
Day 1
Bucket has:

A
B
C
```

You enable replication on Day 2.

Then upload:

```
D
```

Normally:

```
A ❌
B ❌
C ❌
D ✅ → replicated
```

> **By default, only new objects are replicated after replication is configured.**
> 

---

# 8. What about existing objects?

You can use:

## S3 Batch Replication

It can replicate:

- Existing objects
- Objects that previously failed replication

Example:

```
Existing Bucket

A
B
C
D

       │
       │ S3 Batch Replication
       ▼

Destination Bucket

A
B
C
D
```

### 🧠 Exam clue

> "Replicate existing objects after replication has already been configured."
> 

Think:

**S3 Batch Replication**

---

# 9. DELETE Operations

This is a particularly important exam concept.

There are two important cases.

### Case 1 — Delete Marker

You can optionally configure S3 to replicate **delete markers**.

```
Source

file.txt
   │
DELETE
   ▼
Delete Marker
   │
   │ optional replication
   ▼
Destination
Delete Marker
```

So:

> **Delete markers can optionally be replicated.**
> 

---

### Case 2 — Delete using Version ID

Suppose someone specifically deletes:

```
file.txt
Version ID = xyz123
```

That deletion is **not replicated**.

Why?

To help prevent malicious or accidental deletion from propagating to the replica.

```
Source                           Destination

Version 1                        Version 1
Version 2                        Version 2

Delete Version 2
     │
     X ───────────────────────►  Version 2 remains
```

### 🔥 Exam memory

> **Delete marker → Can replicate**
> 

> **Delete specific version → NOT replicated**
> 

---

# 10. No Replication Chaining

This is another excellent SAA-C03 trap.

Suppose:

```
Bucket A
   │
   │ Replication
   ▼
Bucket B
   │
   │ Replication
   ▼
Bucket C
```

You might think:

```
A → B → C
```

But S3 does **not** automatically chain replication this way.

If an object is created in **Bucket A**:

```
A ─────► B
         ✕
         C
```

It does **not automatically go from A → B → C**.

### 🧠 Memory Trick

> **S3 Replication doesn't relay.**
> 

---

# 11. IAM Permissions

S3 needs the proper permissions to perform replication.

Conceptually:

```
Source S3
    │
    ▼
IAM Role
    │
    ▼
S3 Replication
    │
    ▼
Destination S3
```

You need appropriate permissions for the replication process.

### Exam clue

If replication isn't working and the question mentions permissions:

> **Check the IAM role / S3 replication permissions.**
> 

---

# 12. Cross-Account Replication

CRR and SRR can work across AWS accounts.

Example:

```
Account A — Production

S3 Production
      │
      │ Replication
      ▼
Account B — Backup

S3 Backup
```

This is particularly useful for **security isolation**.

For example:

> Production account should not be the only place where backups exist.
> 

---

# 13. Complete Architecture Example

### Scenario

A company operates a production application in `us-east-1`.

Requirements:

- Store user-uploaded files in S3.
- Keep a copy in another AWS Region.
- Protect against regional failure.
- Production and backup should use separate AWS accounts.

### Architecture

```
                 Production Account
                    us-east-1
                       │
                       ▼
                ┌─────────────┐
                │ S3 Bucket   │
                │ Versioning  │
                │ ON          │
                └──────┬──────┘
                       │
                       │ CRR
                       │
                       ▼
                ┌─────────────┐
                │ S3 Bucket   │
                │ Versioning  │
                │ ON          │
                └─────────────┘
                    us-west-2
                  Backup Account
```

### Why?

- **CRR** → different regions
- **Versioning** → required
- **Cross-account** → supported
- **Asynchronous** → replication isn't immediate
- **DR** → replica exists in another region

---

# 14. CRR vs SRR — Scenario Recognition

| Question scenario | Answer |
| --- | --- |
| Replicate data to another AWS Region | **CRR** |
| Disaster recovery in another region | **CRR** |
| Improve access latency in another region | **CRR** |
| Geographic compliance requirement | **CRR** |
| Replicate between two accounts in different regions | **CRR** |
| Aggregate logs within same region | **SRR** |
| Production bucket → test bucket in same region | **SRR** |
| Replicate between accounts in same region | **SRR** |
| Existing objects need replication | **S3 Batch Replication** |
| New objects after replication is configured | **S3 Replication** |
| Delete marker replication | **Optional** |
| Specific version deletion replication | **No** |
| A → B → C automatic replication | ❌ **No chaining** |

---

# 🔥 SAA-C03 Final Cheat Sheet

| Concept | Exam Answer |
| --- | --- |
| CRR | Different AWS Regions |
| SRR | Same AWS Region |
| Replication speed | **Asynchronous** |
| Versioning | **Required on source + destination** |
| Cross-account | ✅ Supported |
| Existing objects | **S3 Batch Replication** |
| New objects | Automatically replicated according to replication rule |
| Delete marker | Can optionally replicate |
| Delete specific version ID | ❌ Not replicated |
| Replication chaining | ❌ Not supported |
| CRR major use cases | **DR, compliance, lower latency** |
| SRR major use cases | **Log aggregation, production → test** |
| Permissions | Appropriate IAM/S3 replication permissions required |

## 🧠 Ultimate Memory Trick

> **CRR = Cross Region → DR / Compliance / Latency**
> 

> **SRR = Same Region → Logs / Production-Test**
> 

And remember this **5-point exam formula**:

**Versioning → Async → New Objects → Batch for Old → No Chaining**
