# RDS Event Subscription

## Amazon RDS Event Subscription — Quick Exam Note

### What is it?

**RDS Event Subscription** is a mechanism that lets you **receive notifications when something happens to an RDS resource**.

Think:

> **“Tell me when something happens to my RDS database/service.”**
> 

It is mainly about **RDS/Aurora service or infrastructure events**, not individual rows inside your database.

---

### Simple example

You have:

```
Amazon RDS / Aurora
       ↓
Something happens
       ↓
RDS Event
       ↓
Event Subscription
       ↓
SNS
       ↓
Notification
```

For example:

- DB instance started
- DB instance stopped
- Failover occurred
- Backup completed
- Maintenance event
- Database instance failure

You can subscribe to specific event categories and receive notifications, commonly through **Amazon SNS**.

---

### What it DOES NOT mean

This is the important exam distinction:

RDS Event Subscription does **not** mean:

> "Tell me whenever a row in my database table changes."
> 

For example:

```
DELETE FROM cars WHERE car_id = 101;
```

That is a **database data change**, not an RDS service event.

So don't confuse:

| RDS Event Subscription | Database Data Change |
| --- | --- |
| RDS/Aurora service events | Data inside tables changes |
| Failover | Row inserted |
| Backup completed | Row updated |
| Maintenance | Row deleted |
| Instance failure | Record changed |

### 🧠 Memory Trick

> **RDS Event Subscription = “What happened to my RDS?”**
> 

Not:

> **“What happened inside my database table?”**
> 

### Typical architecture

```
RDS / Aurora
     ↓
RDS Event
     ↓
Event Subscription
     ↓
SNS
     ↓
Email / Lambda / SQS / etc.
```

**SAA-C03 takeaway:**

When you see **“RDS event,” “failover,” “backup,” "maintenance," or “instance event,”** think **RDS Event Subscription**.
