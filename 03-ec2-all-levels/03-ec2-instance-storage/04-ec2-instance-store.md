# EC2 Instance Store — AWS SAA-C03

### Simple definition

> **EC2 Instance Store is very fast local disk storage physically attached to the EC2 host.**
> 

Unlike EBS, it is **local to the physical EC2 server**, so it can provide very high I/O performance.

```
EC2 Host
┌─────────────────────┐
│ EC2 Instance         │
│                     │
│ Instance Store SSD   │ ← Very fast local disk
└─────────────────────┘
```

### Why use it?

Use **Instance Store** when you need:

- Very high **IOPS**
- Very low-latency local storage
- Temporary data

Typical use cases:

- **Cache**
- **Buffer**
- **Scratch space**
- Temporary processing data

Example:

```
Application
    ↓
Instance Store
    ↓
Temporary processing files
```

If the data is lost, the application can recreate it.

---

### ⚠️ Biggest disadvantage: Ephemeral

Instance Store data is **not persistent**.

If the instance is stopped/terminated or the underlying hardware fails, the data can be lost.

```
EC2 Running
   ↓
Instance Store
   ↓
Data
   ↓
Hardware failure ❌
   ↓
Data lost
```

Therefore:

> **Do NOT use Instance Store for important persistent data.**
> 

---

### Instance Store vs EBS

|  | **Instance Store** | **EBS** |
| --- | --- | --- |
| Location | Local to EC2 host | Network-attached |
| Performance | ⭐ Very high I/O | Good/high depending on type |
| Persistent? | ❌ No | ✅ Yes |
| Best for | Cache, scratch, temporary data | Databases, OS, persistent data |
| Backup | **You manage it** | EBS Snapshots |

---

### 🧠 Exam clue

If the question says:

> **"Very high IOPS / very low latency local storage"**
> 

and the data is:

> **"temporary, cache, scratch, buffer"**
> 

👉 **EC2 Instance Store**

If it says:

> **"Persistent data / must survive stop"**
> 

👉 **EBS**

### 🔑 Memory Trick

> **Instance Store = FAST but TEMPORARY**
> 
> 
> **EBS = PERSISTENT block storage**
>
