# EBS Snapshots — AWS SAA-C03 Notes

## 1. What is an EBS Snapshot?

> **EBS Snapshot = backup of an EBS volume at a specific point in time.**
> 

Think of it like taking a **photo/backup of your disk**.

```
EC2
 │
 ▼
EBS Volume (50 GB)
 │
 │ Take Snapshot
 ▼
EBS Snapshot
```

You can later use the snapshot to **create a new EBS volume**.

```
Snapshot
   │
   │ Restore
   ▼
New EBS Volume
```

---

## 2. Do I need to detach the EBS volume?

**No.**

You can create a snapshot while the volume is attached and in use.

However, for the most consistent backup, especially for active applications/databases, it is generally recommended to **pause writes / quiesce the application** when appropriate.

---

## 3. Snapshot across AZ / Region

Snapshots are useful for moving/recreating EBS data.

Example:

```
us-east-1a
EBS Volume
    │
    ▼
Snapshot
    │
    ▼
us-east-1b
New EBS Volume
```

You can also **copy snapshots to another AWS Region**.

```
us-east-1
Snapshot
    │
    │ Copy
    ▼
us-west-2
Snapshot
    │
    ▼
New EBS Volume
```

### 🧠 Exam clue

> **"Backup EBS volume" → EBS Snapshot**
> 

> **"Restore EBS volume in another AZ" → Snapshot → New EBS volume**
> 

> **"Copy EBS backup to another Region" → Copy Snapshot**
> 

---

# 4. EBS Snapshot Archive

Sometimes you don't need to access old snapshots frequently.

You can move a snapshot to the **EBS Snapshot Archive**.

### Main idea:

> **Archive = much cheaper storage for snapshots you rarely need.**
> 

Your notes specify approximately **75% cheaper** than the standard snapshot tier.

```
Normal Snapshot
      │
      │ Archive
      ▼
Snapshot Archive
      │
      └── Much cheaper
```

### Important trade-off

Restoring an archived snapshot takes approximately:

> **24–72 hours**
> 

So it's **not for frequently accessed backups**.

### Example

A company keeps monthly backups for compliance for 10 years:

```
Recent backup
     ↓
Normal Snapshot

Old backup
     ↓
Snapshot Archive
     ↓
Cheap long-term storage
```

### Exam clue

> **"Long-term retention + rarely accessed + lowest storage cost"**
> 

👉 **EBS Snapshot Archive**

---

# 5. EBS Snapshot Recycle Bin

The **Recycle Bin** protects snapshots from accidental deletion.

You create a retention rule:

```
Delete Snapshot
      ↓
Recycle Bin
      ↓
Retained for X days
      ↓
Can recover if needed
```

You can configure retention from:

> **1 day → 1 year**
> 

### Example

An administrator accidentally deletes an important EBS snapshot.

Without Recycle Bin:

```
Delete → Gone ❌
```

With Recycle Bin:

```
Delete
  ↓
Recycle Bin
  ↓
Recover ✅
```

### 🧠 Exam clue

> **"Accidental snapshot deletion"**
> 

👉 **EBS Snapshot Recycle Bin**

---

# 6. Fast Snapshot Restore (FSR)

Normally, when you create an EBS volume from a snapshot, the data may need to be initialized as it is accessed for the first time.

This can cause **initial I/O latency**.

**Fast Snapshot Restore (FSR)** forces the snapshot to be fully initialized so that the restored EBS volume can provide **full performance immediately**.

```
Without FSR:

Snapshot
   ↓
New EBS
   ↓
First access → initialization → possible latency

With FSR:

Snapshot
   ↓
Pre-initialized EBS
   ↓
Immediate full performance
```

### Trade-off

> **FSR costs extra money.**
> 

So use it when **very fast access to newly restored volumes** is important.

### Example

A company has a critical application and needs to rapidly create EBS volumes from a snapshot during disaster recovery.

Without FSR:

```
Restore
  ↓
Initial I/O
  ↓
Latency
```

With FSR:

```
Restore
  ↓
Full performance immediately
```

---

# ⭐ EBS Snapshot Features — Exam Cheat Sheet

| Feature | Purpose | Key clue |
| --- | --- | --- |
| **EBS Snapshot** | Backup EBS volume | Point-in-time backup |
| **Snapshot Copy** | Move/copy snapshot to another Region | Cross-Region |
| **Snapshot Archive** | Cheaper long-term snapshot storage | Rarely accessed / 24–72h restore |
| **Recycle Bin** | Recover accidentally deleted snapshots | Accidental deletion |
| **Fast Snapshot Restore (FSR)** | Avoid initial I/O latency | Immediate full performance |

## 🧠 One Memory Trick

> **Snapshot = Backup**
> 
> 
> **Archive = Cheap old backup**
> 
> **Recycle Bin = Recover deleted backup**
> 
> **FSR = Fast restored volume**
> 

**SAA-C03 focus:** Know the **purpose and trade-off** of each feature rather than memorizing implementation details.
