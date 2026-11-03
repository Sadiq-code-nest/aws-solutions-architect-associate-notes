# EC2 Instance Store — Simple Understanding

**Instance Store** is **temporary storage physically attached to the EC2 host machine**.

Think:

```
EC2 Instance
   │
   └── Instance Store
       (very fast temporary disk)
```

## Why do we need it?

Its main purpose is to provide **very fast temporary storage** for data that **doesn't need to survive** if the EC2 instance is stopped, terminated, or the underlying host fails.

### Example

Suppose you're processing huge temporary files:

```
EC2
 │
 ├── Application
 │
 └── Instance Store
       ├── temp-file-1
       ├── temp-file-2
       └── cache
```

Your application can process these files very quickly.

If the instance is terminated:

```
EC2 terminated
      ↓
Instance Store data ❌ deleted
```

That's okay because the data was only temporary.

---

## Common use cases

### 1. Temporary processing

Example:

> Video processing application creates large temporary files while converting videos.
> 

```
S3 → EC2 → Instance Store → processed video → S3
```

The temporary files don't need permanent storage.

### 2. Cache

Example:

> Application caches frequently used data locally for very fast access.
> 

If the cache disappears, the application can rebuild it.

### 3. Scratch space

Example:

> Big-data/scientific workloads need very fast temporary working space.
> 

---

# Instance Store vs EBS

|  | Instance Store | EBS |
| --- | --- | --- |
| Storage | Physically attached to host | Network-attached |
| Speed | Very fast | Fast |
| Persistent? | ❌ No | ✅ Yes |
| Survives instance stop? | ❌ No | ✅ |
| Survives instance termination? | ❌ No | Usually ✅ |
| Best for | Temporary data/cache | Persistent data |
| Backup needed? | If data matters | Snapshots available |

### 🧠 SAA-C03 shortcut

> **Instance Store = Fast + Temporary + Non-persistent**
> 

> **EBS = Persistent + Durable block storage**
> 

### Exam clue

If the question says:

> **"temporary files", "cache", "scratch data", "very high-speed local storage"**
> 

👉 Think **EC2 Instance Store**.

If it says:

> **"data must survive instance termination"**
> 

👉 **Don't use Instance Store** → think **EBS / EFS / S3**, depending on the workload.
