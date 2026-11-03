# AWS Elastic Disaster Recovery (DRS) + AWS DMS- Database Migration Service  + SCT — SAA-C03 Exam Notes

## 1. AWS Elastic Disaster Recovery (DRS)

**AWS Elastic Disaster Recovery (DRS)** was formerly called **CloudEndure Disaster Recovery**.

### What does DRS do?

It helps you **recover physical, virtual, or cloud-based servers into AWS** during a disaster.

Typical use cases:

- Critical databases
- Oracle
- MySQL
- SQL Server
- Enterprise applications such as SAP
- Protection against ransomware attacks
- Disaster recovery from on-premises or another cloud

### 🧠 Simple definition

> **AWS DRS = Continuously replicate servers to AWS so they can be quickly recovered when disaster happens.**
> 

---

# 2. How AWS DRS Works

The source environment can be:

```
Corporate Data Center
        OR
     Any Cloud
        OR
   Physical / Virtual Servers
```

You install the **AWS Replication Agent** on the source servers.

```
Source Server
     │
     │ AWS Replication Agent
     │
     │ Continuous block-level replication
     │        (seconds)
     ▼
AWS DRS
     │
     ▼
Staging Area
     │
     ├── Low-cost EC2
     └── EBS Volumes
```

The source environment continues running normally.

---

# 3. Continuous Block-Level Replication

DRS performs **continuous block-level replication**.

This means changes to disk blocks are continuously replicated to AWS.

```
Source Server
      │
      │ Changed disk blocks
      │
      ▼
AWS Staging Area
```

The replication happens continuously, often within seconds.

### Why is this useful?

When disaster happens, AWS has a relatively recent copy of the server's data.

---

# 4. Failover

If the source environment fails:

```
On-Premise ❌
     │
     │ Disaster
     ▼
AWS DRS
     │
     ▼
Target EC2 + EBS
     │
     ▼
Production Application
```

The recovery environment can be launched into AWS.

### Important

**Failover can happen in minutes**, rather than rebuilding servers from scratch.

---

# 5. Failback

After the original environment is restored, you can perform **failback**.

```
Normal:

On-Premise → AWS DRS

Disaster:

On-Premise ❌
      ↓
AWS → Production

After recovery:

AWS → On-Premise
     ↑
   Failback
```

### 🧠 Exam memory

**DRS = Replicate → Failover → Recover → Failback**

---

# 6. DRS Architecture — Easy View

```
                 SOURCE
       Corporate Data Center
              / Any Cloud
                   │
                   │
          AWS Replication Agent
                   │
                   │ Continuous
                   │ block replication
                   ▼
             AWS DRS
                   │
                   ▼
          Staging Environment
          ┌────────┴────────┐
          │                 │
       EC2 instances     EBS volumes
       (low-cost)        (replicated)
          │
          │ Disaster
          ▼
      Failover
          │
          ▼
   Target EC2 + EBS
       Production
```

---

# ⭐ DRS Exam Clues

If you see:

> **"Recover physical, virtual, or cloud-based servers into AWS"**
> 

➡️ **AWS Elastic Disaster Recovery**

If you see:

> **"Continuous block-level replication"**
> 

➡️ **AWS DRS**

If you see:

> **"CloudEndure Disaster Recovery"**
> 

➡️ **AWS Elastic Disaster Recovery**

---

# 7. AWS Database Migration Service (AWS DMS)

**AWS Database Migration Service (DMS)** is used to **migrate databases to AWS**.

[**AWS Database Migration Service (AWS DMS)**](https://aws.amazon.com/dms/)  **moves data securely and quickly from an on-premises database to the AWS cloud with near-zero downtime**

### Simple definition

> **AWS DMS = Move/replicate database data from a source to a target.**
> 

It supports:

- Database migrations
- Continuous replication
- Minimal downtime migrations
- Homogeneous migrations
- Heterogeneous migrations

---

# 8. Source Database Remains Available

One major advantage of DMS:

> **The source database remains available during migration.**
> 

For example:

```
Production DB
     │
     │ continues serving users
     │
     ├──────────────→ Users
     │
     │ Replication
     ▼
    DMS
     │
     ▼
Target DB
```

This allows you to migrate without taking the source database completely offline.

---

# 9. AWS DMS Architecture

You need a **DMS replication instance**.

The replication instance performs the migration/replication tasks.

```
Source Database
      │
      ▼
┌──────────────────┐
│ DMS Replication  │
│ Instance         │
└────────┬─────────┘
         │
         ▼
   Target Database
```

### ⭐ Exam clue

> **"Create an EC2 instance to perform the replication tasks"**
> 

In the provided notes, this refers to the **DMS replication instance** concept.

More precisely, DMS provisions/manages the replication instance for the migration task rather than requiring you to manually create a normal EC2 instance.

---

# 10. Homogeneous Migration

**Same database engine → same database engine**

Example:

```
Oracle
  ↓
Oracle
```

or:

```
PostgreSQL
    ↓
RDS PostgreSQL
```

You generally **do not need AWS SCT** when the database engine remains the same.

### Example

```
On-Premise PostgreSQL
        ↓
       DMS
        ↓
Amazon RDS PostgreSQL
```

Even though you're moving from on-premises to RDS, the engine is still **PostgreSQL**.

---

# 11. Heterogeneous Migration

**Different database engines**

Example:

```
SQL Server
    ↓
Aurora PostgreSQL
```

or:

```
Oracle
   ↓
Aurora
```

Here, the database schema may need to be converted.

That's where **AWS Schema Conversion Tool (SCT)** becomes important.

---

# 12. AWS DMS + SCT

For a heterogeneous migration:

```
Source DB
Oracle / SQL Server
      │
      │ AWS SCT
      ▼
Converted Schema
      │
      ▼
     DMS
      │
      ▼
Target DB
Aurora / PostgreSQL / MySQL
```

### Easy way to remember

**SCT → Converts the schema**

**DMS → Moves/replicates the data**

---

# 13. AWS SCT — Schema Conversion Tool

AWS SCT converts a database schema from one database engine to another.

### Example OLTP

```
SQL Server
    │
    │ SCT
    ▼
MySQL / PostgreSQL / Aurora
```

### Example OLAP

```
Teradata / Oracle
       │
       │ SCT
       ▼
    Redshift
```

---

# 14. Do I Need SCT?

### Same engine

```
PostgreSQL
     ↓
RDS PostgreSQL
```

❌ Usually **no SCT**

Use:

**DMS**

---

### Different engine

```
Oracle
   ↓
Aurora PostgreSQL
```

✅ **SCT + DMS**

```
SCT → Convert schema
DMS → Migrate data
```

---

# 15. DMS Continuous Data Replication

DMS supports **Continuous Data Replication using CDC**.

**CDC = Change Data Capture**

After the initial migration, DMS can continue replicating changes from the source database.

```
Source DB
   │
   ├── Initial data ────────→ Target
   │
   └── New changes (CDC) ──→ Target
```

This is useful when you want minimal downtime.

### Example

```
Production Database
        │
        │ Users continue
        │ using database
        ▼
       DMS
        │
        │ CDC
        ▼
New AWS Database
```

Once the target is synchronized, you can switch the application to the new database.

---

# 16. DMS Sources

DMS supports many source systems.

### On-Premises / EC2 databases

- Oracle
- Microsoft SQL Server
- MySQL
- MariaDB
- PostgreSQL
- MongoDB
- SAP
- DB2

### Azure

- Azure SQL Database

### AWS

- Amazon RDS
- Aurora
- Amazon S3
- DocumentDB

---

# 17. DMS Targets

DMS supports targets such as:

- On-Premises databases
- EC2-hosted databases
- Amazon RDS
- Amazon Redshift
- DynamoDB
- Amazon S3
- OpenSearch Service
- Kinesis Data Streams
- Apache Kafka
- DocumentDB
- Amazon Neptune
- Redis
- Babelfish

### 🧠 Exam idea

DMS isn't limited to:

> **Database → Database**
> 

It can also move data into services such as:

```
Database
   ↓
S3
```

or:

```
Database
   ↓
Redshift
```

---

# 18. DMS Multi-AZ

DMS can be configured for **Multi-AZ**.

When Multi-AZ is enabled:

```
AWS Region
│
├── AZ-A
│     └── DMS Replication Instance
│
└── AZ-B
      └── Standby Replica
```

The standby replica is maintained in a different Availability Zone.

### Benefits

- Data redundancy
- High availability
- Improved resilience
- Helps avoid I/O freezes
- Helps minimize latency spikes

---

# 19. DMS Multi-AZ — Exam Scenario

### Question

> A company is migrating a production database using AWS DMS. They need the DMS replication infrastructure to be highly available in case an Availability Zone fails.
> 

➡️ **Enable Multi-AZ for the DMS replication instance.**

---

# 🔥 DRS vs DMS vs SCT

This is **very important for SAA-C03**.

| Service | Main purpose | Think |
| --- | --- | --- |
| **AWS DRS** | Disaster recovery of servers | **Recover servers** |
| **AWS DMS** | Database/data migration and replication | **Move database data** |
| **AWS SCT** | Convert database schemas | **Convert database structure** |

### Super-simple memory:

```
DRS
↓
Servers → AWS
"DISASTER RECOVERY"

DMS
↓
Database → Database / AWS data services
"DATABASE MIGRATION"

SCT
↓
Oracle → PostgreSQL
SQL Server → Aurora
"SCHEMA CONVERSION"
```

---

# 🎯 Most Important Exam Scenarios

### ① Physical server → AWS DR

> Company has physical servers on-premises and wants continuous replication into AWS for disaster recovery.
> 

✅ **AWS Elastic Disaster Recovery (DRS)**

---

### ② On-premise database → RDS

> Company wants to migrate its production PostgreSQL database to Amazon RDS PostgreSQL while keeping the source database available.
> 

✅ **AWS DMS**

---

### ③ Oracle → Aurora PostgreSQL

> Company wants to migrate Oracle to Aurora PostgreSQL and needs to convert the database schema.
> 

✅ **AWS SCT + DMS**

```
SCT → Schema conversion
DMS → Data migration
```

---

### ④ Same database engine

> On-premises PostgreSQL → RDS PostgreSQL
> 

✅ **DMS**

❌ SCT generally not needed.

---

### ⑤ Minimal downtime database migration

> Source database must remain available and changes must continuously replicate to the target.
> 

✅ **AWS DMS + CDC**

---

### ⑥ DMS high availability

> DMS migration must survive an Availability Zone failure.
> 

✅ **DMS Multi-AZ**

---

# 🧠 Final 30-Second Revision

```
AWS ELASTIC DISASTER RECOVERY (DRS)
│
├── Formerly CloudEndure Disaster Recovery
├── Physical / Virtual / Cloud servers
├── Continuous block-level replication
├── AWS Replication Agent
├── Staging environment
├── Failover → AWS
└── Failback → Original environment

AWS DMS
│
├── Database migration
├── Source remains available
├── Homogeneous migration
├── Heterogeneous migration
├── CDC = continuous changes
├── Multi-AZ available
└── DMS Replication Instance

AWS SCT
│
├── Schema conversion
├── Different DB engines
├── Oracle → PostgreSQL
├── SQL Server → Aurora
└── Teradata / Oracle → Redshift
```

### ⭐ Memorize these three lines

> **DRS = Recover servers**
> 

> **DMS = Migrate/replicate data**
> 

> **SCT = Convert database schema**
> 

And the most important combination:

**Different DB engine → SCT + DMS**

**Same DB engine → DMS only**
