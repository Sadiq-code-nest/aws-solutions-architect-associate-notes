# Async Replication vs sync Replication

Most students memorise:

- Multi-AZ = Synchronous
- Read Replica = Asynchronous

But they don't understand **why**.

Let's fix that.

---

# Imagine a bank transfer.

You transfer $100.

Question:

**When should the bank tell you "Transaction Successful"?**

There are two possibilities.

---

# 1. Synchronous Replication

## Meaning

Primary database waits for standby database to confirm the write.

Only then success is returned.

### Flow

```
Application
     │
     ▼
Primary DB
     │
     ├── Write Data
     │
     ▼
Standby DB
     │
     └── Confirmation
             │
             ▼
      Success Returned
```

---

## Example

You place an order.

```sql
INSERT INTO Orders
```

### Step 1

Primary receives write.

```
Order #101
```

---

### Step 2

Primary sends same write to standby.

```
Primary ---> Standby
```

---

### Step 3

Standby says:

```
Saved successfully
```

---

### Step 4

Now primary tells application:

```
Transaction Successful
```

---

# Why is it Safe?

Because both databases already contain the data.

```
Primary = Order #101
Standby = Order #101
```

Even if primary dies immediately:

```
Primary ❌
```

Standby still has:

```
Order #101
```

No data loss.

---

# Downside

Primary must wait.

```
Write
 ↓
Wait
 ↓
Wait
 ↓
Standby confirms
 ↓
Success
```

Slower.

---

# AWS Example

## RDS Multi-AZ

```
Primary
    │
    ▼
Standby
```

Uses:

```
Synchronous Replication
```

Purpose:

```
High Availability
Disaster Recovery
```

---

# 2. Asynchronous Replication

## Meaning

Primary does NOT wait for replica.

It immediately returns success.

Replica is updated later.

---

### Flow

```
Application
     │
     ▼
Primary DB
     │
     ▼
Success Returned Immediately

Later...

Primary
     │
     ▼
Replica Updated
```

---

# Example

User places order.

```sql
INSERT INTO Orders
```

---

### Step 1

Primary saves order.

```
Order #101
```

---

### Step 2

Immediately returns:

```
Success
```

to application.

---

### Step 3

A little later:

```
Primary ---> Replica
```

copies data.

Maybe:

```
100 ms later
1 second later
5 seconds later
```

depends on load.

---

# Problem

Imagine this happens:

```
Order saved on Primary
```

But before replica gets update:

```
Primary crashes ❌
```

Now:

```
Replica never received Order #101
```

Data may be lost.

---

This is called:

# Replication Lag

Replica is behind primary.

Example:

```
Primary
Orders = 1000

Replica
Orders = 998
```

Replica is lagging by 2 records.

---

# Why Use Async Then?

Because it's FAST.

Primary doesn't wait.

```
Write
 ↓
Success
```

No delay.

Much higher performance.

---

# AWS Example

## RDS Read Replica

```
Primary
    │
    ▼
Read Replica
```

Uses:

```
Asynchronous Replication
```

Purpose:

```
Read Scaling
```

Not disaster recovery.

---

# Aurora's Special Case

Aurora readers are different.

Traditional RDS:

```
Primary Storage
       │
       ▼
Replica Storage
```

Data must be copied.

---

Aurora:

```
Writer
Reader1
Reader2
Reader3
      │
      ▼
Shared Storage
```

All instances read the same storage.

This greatly reduces replication lag.

That's why Aurora failover is much faster than standard RDS.

---

# Exam Trick

Question says:

> No data loss allowed.
> 

Think:

✅ Synchronous

✅ Multi-AZ

---

Question says:

> Scale read traffic.
> 

Think:

✅ Asynchronous

✅ Read Replica

---

Question says:

> Need DR and automatic failover.
> 

Think:

✅ Multi-AZ

✅ Synchronous replication

---

Question says:

> Heavy reporting queries causing DB load.
> 

Think:

✅ Read Replica

✅ Asynchronous replication

---

# Easy Memory Table

| Feature | Synchronous | Asynchronous |
| --- | --- | --- |
| Wait for replica? | Yes | No |
| Faster writes? | No | Yes |
| Data loss risk? | Very low | Possible |
| Replication lag? | No | Yes |
| AWS Service | Multi-AZ | Read Replica |
| Purpose | HA / DR | Read Scaling |
| Automatic failover | Yes | Usually No |

### One-Line Memory Trick

**Synchronous = "Save everywhere first, then say success."**

**Asynchronous = "Say success first, copy later."**

That single sentence is enough to answer most AWS SAA questions on sync vs async replication.
