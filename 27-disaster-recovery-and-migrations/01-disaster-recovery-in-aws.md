# AWS Disaster Recovery (DR) — SAA-C03 Exam Notes

## 1. What is a Disaster?

A **disaster** is any event that negatively impacts a company's:

- Business continuity
- Operations
- Data
- Finances

Examples:

- Data center failure
- Hardware failure
- Network outage
- Cyberattack
- Natural disaster
- Application failure
- Regional AWS outage

### Disaster Recovery (DR)

**Disaster Recovery** is the process of **preparing for, responding to, and recovering from a disaster**.

The main goal is:

> **Recover the application and data within an acceptable amount of time and with an acceptable amount of data loss.**
> 

---

# 2. Disaster Recovery Scenarios

AWS DR can be designed for different environments:

### On-Premise → On-Premise

Traditional disaster recovery.

```
Primary Data Center
       ↓
Backup Data Center
```

❌ Usually expensive because you need to maintain another physical environment.

---

### On-Premise → AWS Cloud

Hybrid disaster recovery.

```
On-Premise
    ↓
   AWS
```

AWS can act as your DR environment.

✅ Can reduce the cost of maintaining a second physical data center.

---

### AWS Region A → AWS Region B

Cloud-to-cloud disaster recovery.

```
AWS Region A
     ↓
AWS Region B
```

Useful for protecting against a **regional disaster**.

---

# 3. RPO — Recovery Point Objective

**RPO = How much data can you afford to lose?**

It is measured in **time**.

Example:

```
Last backup
    ↓
10:00 AM
    │
    │ 30 minutes of data
    ▼
10:30 AM
Disaster
```

If your RPO is **30 minutes**, you are accepting the possibility of losing up to approximately **30 minutes of data**.

### 🧠 Easy memory

> **RPO = Data loss**
> 

Think:

**"How far back can I go?"**

---

# 4. RTO — Recovery Time Objective

**RTO = How quickly must the system be restored?**

It is also measured in **time**.

Example:

```
10:00 AM
Disaster
   ↓
11:00 AM
Application restored
```

RTO = **1 hour**

### 🧠 Easy memory

> **RTO = Downtime**
> 

Think:

**"How long can I be down?"**

---

# ⭐ RPO vs RTO

|  | RPO | RTO |
| --- | --- | --- |
| Full name | Recovery Point Objective | Recovery Time Objective |
| Measures | Data loss | Downtime |
| Question | How much data can we lose? | How quickly must we recover? |
| Think | **Data** | **Time to recover** |
| Lower is generally | Better but more expensive | Better but more expensive |

### Exam shortcut

> **RPO → Point in time → Data loss**
> 

> **RTO → Time to recover → Downtime**
> 

---

# 5. Disaster Recovery Strategies

The major strategies are:

```
                    Faster RTO
                       ↑
                       │
Backup & Restore → Pilot Light → Warm Standby → Multi-Site
     ↑                                               ↑
    Slow                                          Fast
    Cheap                                         Expensive
```

From **slowest/cheapest** to **fastest/most expensive**:

1. Backup & Restore
2. Pilot Light
3. Warm Standby
4. Multi-Site / Hot Site

---

# 6. Backup and Restore

### Concept

Data is regularly backed up to AWS.

During a disaster:

```
On-Premise
    │
    │ Backups
    ▼
AWS Cloud
    │
    │ Disaster
    ▼
Restore infrastructure + data
```

Possible AWS services:

- Amazon S3
- S3 IA
- S3 Glacier
- EBS Snapshots
- RDS Snapshots
- RDS Automated Backups
- AMIs
- AWS Storage Gateway
- AWS Snowball

### Example

```
Corporate Data Center
        │
        ├── Storage Gateway
        │
        ├── Snowball
        │
        ▼
       S3
        │
        ▼
    S3 Glacier
```

EC2:

```
EC2
 ↓
Scheduled EBS Snapshots
```

RDS:

```
RDS
 ↓
Automated Backups / Snapshots
```

### During disaster

You need to:

1. Create infrastructure
2. Restore data
3. Start applications
4. Configure networking
5. Redirect traffic

Therefore:

**RTO = High**

**RPO = High**

### 🧠 Exam clue

> **"Cheapest DR strategy" / "Backups only"**
> 

➡️ **Backup & Restore**

---

# 7. Pilot Light

A **small version of the application is always running in AWS**.

The critical/core components remain active.

```
                 AWS
                  │
          ┌───────┴───────┐
          │               │
     Core System       Database
       RUNNING           RUNNING
          │
       Pilot Light
```

The remaining infrastructure is created/scaled when disaster occurs.

### Why is it faster than Backup & Restore?

Because critical systems are **already running**.

You don't start completely from zero.

### Example

Your production environment:

```
100 EC2 instances
```

Pilot Light:

```
Core systems running
Minimal compute
Database replicated
```

After disaster:

```
Scale up
    ↓
Production capacity
```

### 🧠 Exam clue

> **"Critical core is always running"**
> 

➡️ **Pilot Light**

---

# 8. Warm Standby

With **Warm Standby**, the **entire system is already running**, but at a **minimum/low capacity**.

```
Production
100 EC2
   │
   │ Replication
   ▼
DR Region
10 EC2
(minimum size)
```

The application is already operational.

During disaster:

```
Disaster
   ↓
Scale up
   ↓
Production capacity
```

### Example Architecture

```
                 Route 53
                    │
                    │ failover
                    ▼
              ┌───────────┐
              │    ELB    │
              └─────┬─────┘
                    │
            EC2 Auto Scaling
             (minimum size)
                    │
                  App
                    │
             Data Replication
                    │
                    ▼
              RDS Standby
```

### Why faster than Pilot Light?

Because **the full application stack is already running**.

You mainly need to:

- Scale it
- Redirect traffic

### 🧠 Exam clue

> **"Full system running at minimum capacity"**
> 

➡️ **Warm Standby**

---

# 9. Multi-Site / Hot Site

This provides the **lowest RTO** and therefore the fastest recovery.

The complete production environment is running in multiple locations.

```
               Route 53
              /         \
             ↓           ↓
        Region A      Region B
        ACTIVE        ACTIVE
           │             │
          ELB           ELB
           │             │
        EC2 ASG        EC2 ASG
       Production     Production
           │             │
           └──────┬──────┘
                  │
             Replication
```

Both environments operate at **production scale**.

### During disaster

```
Region A ❌
    ↓
Traffic → Region B
```

Very little needs to be created because Region B is already running.

### Advantages

- Very low RTO
- Very fast recovery
- Excellent availability

### Disadvantage

💰 **Very expensive**

You are essentially running production infrastructure in multiple locations.

### 🧠 Exam clue

> **"Very low RTO" + "full production environment running"**
> 

➡️ **Multi-Site / Hot Site**

---

# ⭐ 10. DR Strategy Comparison

| Strategy | Environment | RTO | Cost | Key idea |
| --- | --- | --- | --- | --- |
| **Backup & Restore** | Not running | Highest | $ | Restore everything |
| **Pilot Light** | Core running | Lower | $$ | Critical core always running |
| **Warm Standby** | Full system, minimum capacity | Low | $$$ | Scale up |
| **Multi-Site / Hot Site** | Full production scale | Lowest | $$$$ | Already running |

### Memorize this:

```
Backup & Restore
      ↓
Pilot Light
      ↓
Warm Standby
      ↓
Multi-Site
```

**RTO gets faster → Cost gets higher**

---

# 11. Backup Best Practices

Use different AWS backup mechanisms depending on the workload.

### EBS

Use:

- EBS Snapshots

```
EBS Volume
    ↓
Snapshot
```

### RDS

Use:

- Automated Backups
- Snapshots

### S3

Use:

- S3
- S3 IA
- S3 Glacier
- Lifecycle Policies
- Cross-Region Replication

### On-Premise → AWS

Use:

- AWS Storage Gateway
- AWS Snowball

---

# 12. High Availability During DR

Use highly available architectures to reduce downtime.

### Route 53

Use Route 53 to redirect traffic between regions.

```
              Route 53
              /       \
             ↓         ↓
         Region A   Region B
          ACTIVE     DR
```

If Region A fails:

```
Route 53
    ↓
Region B
```

---

### RDS Multi-AZ

Provides high availability within a region.

### ElastiCache Multi-AZ

Provides high availability for caching.

### EFS

Regional storage designed for availability across Availability Zones.

### S3

Highly durable object storage.

---

# 13. Site-to-Site VPN as DR

Suppose your primary connection is:

```
On-Premise
    │
    │ Direct Connect
    ▼
   AWS
```

You can use Site-to-Site VPN as a backup:

```
                Direct Connect
On-Premise ───────────────────→ AWS
     │
     │
     └──── Site-to-Site VPN ──→ AWS
              Backup
```

### 🧠 Exam clue

> **"Direct Connect failure + need backup connectivity"**
> 

➡️ **Site-to-Site VPN**

---

# 14. Replication

Replication reduces the amount of data you lose after a disaster.

Examples:

### RDS Cross-Region Replication

```
Region A
RDS Primary
    │
    │ Replication
    ▼
Region B
RDS Replica
```

### Aurora Global Database

```
Region A
Aurora Primary
      │
      ├────────→ Region B
      ├────────→ Region C
      └────────→ Region D
```

Useful for:

- Multi-region applications
- Disaster recovery
- Lower recovery time
- Global applications

---

# 15. Database Replication from On-Premise to AWS

You can also replicate an on-premise database to AWS.

For example:

```
On-Premise DB
      │
      │ Replication
      ▼
AWS RDS
```

This helps maintain a current copy of your database in AWS for DR.

---

# 16. Automation

A strong DR architecture shouldn't depend entirely on humans manually rebuilding everything.

### CloudFormation

Use CloudFormation to recreate infrastructure.

```
CloudFormation Template
          ↓
New AWS Environment
          ↓
VPC + EC2 + ALB + RDS + etc.
```

### Elastic Beanstalk

Can help recreate application environments.

### CloudWatch + Automation

CloudWatch alarms can trigger automated actions such as recovery/reboot actions where supported.

### Lambda

Lambda can be used for custom DR automation.

Example:

```
CloudWatch Event
      ↓
Lambda
      ↓
Perform DR action
```

---

# 17. Chaos Engineering

A mature DR strategy should also test whether systems actually recover.

A famous example is Netflix's **Simian Army**, which was designed to deliberately cause failures in infrastructure to test resilience.

The basic concept:

> **Don't just assume your DR system works — test failure scenarios.**
> 

For AWS exam purposes, understand the concept rather than memorizing the specific implementation.

---

# 🔥 SAA-C03 Scenario Examples

### Scenario 1

> Company wants the cheapest DR solution. Data can be restored after a disaster.
> 

**Answer:** Backup & Restore

---

### Scenario 2

> Critical database and core infrastructure are always running, but the rest of the application is created during disaster recovery.
> 

**Answer:** Pilot Light

---

### Scenario 3

> The entire application is running in another region but at minimum capacity. During disaster, it scales to production capacity.
> 

**Answer:** Warm Standby

---

### Scenario 4

> Company requires extremely low RTO and is willing to pay for a complete duplicate production environment.
> 

**Answer:** Multi-Site / Hot Site

---

### Scenario 5

> Company wants to protect against an AWS Region failure.
> 

**Think:**

```
Cross-Region DR
```

Potential solutions:

- Cross-Region replication
- Aurora Global Database
- RDS Cross-Region Replica
- S3 Cross-Region Replication
- Route 53 failover
- Multi-region architecture

---

# 🧠 Final Exam Cheat Sheet

```
DISASTER RECOVERY
│
├── RPO
│     └── How much DATA can I lose?
│
├── RTO
│     └── How much DOWNTIME can I tolerate?
│
├── Backup & Restore
│     └── Cheapest / Slowest
│
├── Pilot Light
│     └── Critical core is running
│
├── Warm Standby
│     └── Full system running at minimum capacity
│
└── Multi-Site / Hot Site
      └── Full production running
      └── Fastest / Most expensive
```

### ⭐ The one line to memorize

**RPO = Data loss**

**RTO = Downtime**

**Backup → Pilot Light → Warm Standby → Multi-Site**

**As RTO gets lower → DR cost generally gets higher.**
