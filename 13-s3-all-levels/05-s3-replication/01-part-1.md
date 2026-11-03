# Amazon S3 Replication — Crystal Clear Exam Notes (SAA-C03)

Amazon S3 replication is used to automatically copy objects from one bucket to another.

Amazon S3 supports replication for:

- Backup (disaster recovery)
- Cross-region availability
- Compliance
- Data locality

---

# 1. What is S3 Replication?

## Simple meaning:

> Automatically copy objects from one S3 bucket to another bucket.
> 

Example:

```
Bucket A (Source)  →  Bucket B (Destination)
```

When you upload a file:

```
photo.jpg → Bucket A → automatically copied to Bucket B
```

---

# 2. IMPORTANT RULE: Only NEW objects are replicated

This is very important for exams.

## What happens after enabling replication?

### Scenario:

You enable replication today.

Then:

```
Upload file1.jpg
Upload file2.jpg
```

✔ These will be replicated

---

### BUT:

```
Old files already in bucket → NOT replicated
```

Example:

Before replication:

```
bucket A:
  old1.jpg
  old2.jpg
```

After enabling replication:

```
old1.jpg ❌ NOT copied
old2.jpg ❌ NOT copied
new1.jpg ✔ copied
```

---

## Why?

Because replication starts only from the moment you enable it.

---

# 3. S3 Batch Replication (for existing objects)

If you want old data also copied:

You use:

> **S3 Batch Replication**
> 

---

## What it does:

- Copies existing objects
- Copies objects that failed earlier replication

---

## Example:

```
Bucket A has 1 million old files
```

After enabling replication:

- Only new files replicate

Then you run Batch Replication:

✔ All old files are copied too

---

## Exam keyword:

> "Replicate existing objects after enabling replication"
> 

✔ Answer = Batch Replication

---

# 4. DELETE Operations in Replication

This is a tricky exam topic.

There are TWO types of delete behavior.

---

# Case 1: Delete Marker (soft delete)

If versioning is enabled:

```
delete file.jpg
```

AWS does NOT remove file.

It adds:

```
DELETE MARKER
```

---

## Can delete markers be replicated?

✔ YES (optional setting)

You can choose:

```
Replicate delete markers → ON/OFF
```

---

### Example:

Bucket A:

```
file.jpg → delete marker added
```

If enabled:

```
Bucket B also gets delete marker
```

So both buckets look deleted.

---

# Case 2: Versioned DELETE (dangerous delete)

Example:

```
Delete file with version ID
```

This is a **permanent delete of a specific version**.

---

## Important Rule:

❌ NOT replicated

---

## Why?

To prevent:

```
Malicious user deleting data in source → destroying backup in destination
```

So AWS protects your backup.

---

## Exam line:

> Versioned deletes are NOT replicated for safety.
> 

---

# 5. No Replication Chaining (VERY IMPORTANT)

This is a common exam trap.

---

## Example:

```
Bucket 1 → Bucket 2 → Bucket 3
```

Replication enabled:

- Bucket 1 → Bucket 2 ✔
- Bucket 2 → Bucket 3 ✔

---

## What students THINK:

```
Bucket 1 → Bucket 3 (indirectly)
```

❌ WRONG

---

## What ACTUALLY happens:

```
Bucket 1 → Bucket 2 ✔
Bucket 1 → Bucket 3 ❌ NOT automatic
```

---

## Why?

AWS does NOT chain replication to avoid:

- loops
- duplication storms
- uncontrolled data propagation

---

# 6. Full Visual Diagram

## Normal replication:

```
   Upload
     │
     ▼
Bucket A (Source)
     │
     ▼
Replication Rule
     │
     ▼
Bucket B (Destination)
```

---

## Batch replication:

```
Old Objects → Batch Job → Bucket B
New Objects → Auto Replication → Bucket B
```

---

## Delete behavior:

```
Delete Marker → (optional replication) → Bucket B
Versioned Delete → NOT replicated
```

---

## No chaining:

```
Bucket A → Bucket B → Bucket C

BUT:

A does NOT automatically go to C
```

---

# 7. Real-Life Analogy (Very Important for Memory)

## Normal replication:

```
Main warehouse → Backup warehouse
```

Every new product is copied.

---

## Batch replication:

```
Old warehouse stock → manually copied in bulk
```

---

## Delete marker:

```
Item marked "removed" → optionally mirrored
```

---

## Versioned delete:

```
Destroyed item → NOT copied (to protect backup)
```

---

## No chaining:

```
Warehouse A → Warehouse B → Warehouse C

But A does NOT directly supply C
```

---

# 8. Exam Cheat Sheet

## Must remember:

### 1. Only new objects replicate

✔ After enabling replication

---

### 2. Old objects need Batch Replication

✔ optional but required for existing data

---

### 3. Delete markers can replicate

✔ optional setting

---

### 4. Versioned deletes do NOT replicate

✔ for safety

---

### 5. No replication chaining

✔ A → B → C does NOT mean A → C

---

# 9. 10-Second Exam Summary

> S3 Replication copies only new objects automatically. Existing objects need Batch Replication. Delete markers can be replicated, but versioned deletes are not. Replication does not chain between buckets.
>
