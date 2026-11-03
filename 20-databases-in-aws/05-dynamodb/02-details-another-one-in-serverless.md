# Amazon DynamoDB (AWS SAA-C03) – Exam Notes with Easy Explanations

## 1. Managed Serverless NoSQL Database

- **Managed:** AWS handles servers, patching, backups, and scaling.
- **Serverless:** No infrastructure to manage.
- **NoSQL:** Stores data as **key-value** or **JSON-like documents** (not SQL tables).
- **Performance:** Single-digit **millisecond latency** (very fast).

**📝 Exam Tip:**

If the question says **serverless + NoSQL + massive scale + low latency**, think **DynamoDB**.

---

## 2. Capacity Modes

### Provisioned Capacity

- You choose the number of **Read Capacity Units (RCUs)** and **Write Capacity Units (WCUs)**.
- Cheaper for **predictable workloads**.
- Can enable **Auto Scaling**.

### On-Demand Capacity

- AWS automatically scales reads and writes.
- No capacity planning.
- Better for **unpredictable traffic**.
- Slightly more expensive.

**📝 Exam Tip:**

- **Predictable traffic → Provisioned**
- **Unpredictable traffic → On-Demand**

---

## 3. Can Replace ElastiCache (Simple Key-Value Store)

DynamoDB can store:

- User sessions
- Shopping carts
- Login tokens

Using **TTL (Time To Live)**, expired items are automatically deleted.

Example:

- User logs in.
- Session expires after 30 minutes.
- DynamoDB deletes it automatically.

**📝 Exam Tip:**

**TTL = Automatically remove expired data.**

---

## 4. Highly Available (Multi-AZ)

DynamoDB automatically stores data across **multiple Availability Zones**.

Benefits:

- High Availability
- Fault Tolerance
- No manual replication

**📝 Exam Tip:**

**Multi-AZ is built in**—you don't need to configure it.

---

## 5. Read and Write Scaling are Independent

You can scale:

- Reads only
- Writes only

No need to scale both together.

**📝 Exam Tip:**

Useful when your application has **more reads than writes** (or vice versa).

---

## 6. Supports ACID Transactions

DynamoDB supports **transactions** across multiple items.

Example:

- Deduct money from Account A.
- Add money to Account B.

Either:

- ✅ Both succeed.
- ❌ Both fail.

Never a partial update.

**📝 Exam Tip:**

DynamoDB supports **ACID transactions**, despite being NoSQL.

---

## 7. DAX (DynamoDB Accelerator)

**DAX = In-memory cache for DynamoDB.**

Purpose:

- Makes **read operations much faster**.
- Reduces latency from **milliseconds → microseconds**.

Example:

- Product catalog
- Gaming leaderboard
- Frequently accessed data

**📝 Exam Tip:**

**Need extremely fast reads? → Use DAX.**

---

## 8. Security with IAM

Access to DynamoDB is controlled using:

- IAM Users
- IAM Roles
- IAM Policies

No database usernames/passwords are required for AWS services.

**📝 Exam Tip:**

**Authorization = IAM**

---

## 9. DynamoDB Streams

Streams capture every change in a table:

- Insert
- Update
- Delete

Example:

- New user registers.
- Stream triggers Lambda.
- Lambda sends a welcome email.

Streams can integrate with:

- AWS Lambda
- Kinesis Data Streams

**📝 Exam Tip:**

**Need real-time processing after table changes? → DynamoDB Streams.**

---

## 10. Global Tables

Replicate data across **multiple AWS Regions**.

Example:

```
Singapore  ←→  London  ←→  Virginia
```

All regions can:

- Read
- Write

This is called **Active-Active Replication**.

Benefits:

- Low latency worldwide
- Disaster Recovery
- Global applications

**📝 Exam Tip:**

**Global Tables = Multi-Region Active-Active.**

---

## 11. Automated Backups (PITR)

**Point-in-Time Recovery (PITR)**

- Keeps backups for **up to 35 days**.
- Restore to **any second** within that period.
- Restores into a **new table**.

Example:

- Someone deletes data at 2:30 PM.
- Restore table to 2:29 PM.

**📝 Exam Tip:**

**PITR = Recover to any point in the last 35 days.**

---

## 12. On-Demand Backup

Create a manual full backup anytime.

Features:

- Long-term storage.
- Doesn't affect performance.
- Restores to a **new table**.

**📝 Exam Tip:**

Use for **long-term backup**.

---

## 13. Export to S3

Export table data directly to Amazon S3.

Benefits:

- Doesn't consume **Read Capacity Units (RCUs)**.
- Used for:
    - Analytics
    - Data Lake
    - Archiving

Requires **PITR enabled**.

**📝 Exam Tip:**

**Export → No RCUs consumed.**

---

## 14. Import from S3

Import:

- CSV
- DynamoDB JSON
- Amazon Ion

Creates a **new DynamoDB table**.

Benefits:

- Doesn't consume **Write Capacity Units (WCUs)**.

**📝 Exam Tip:**

**Import → No WCUs consumed.**

---

## 15. Flexible Schema

Each item can have different attributes.

Example:

```
User 1
Name
Email

User 2
Name
Phone
Address
```

No need to define every column in advance.

**📝 Exam Tip:**

DynamoDB is **schema-flexible**, unlike relational databases.

---

# Common Use Cases

Use DynamoDB for:

- Serverless applications
- Mobile apps
- Web applications
- Shopping carts
- User profiles
- Session storage
- Gaming leaderboards
- IoT data
- Event-driven applications
- High-scale key-value storage

---

# AWS SAA-C03 Cheat Sheet

| Feature | Remember |
| --- | --- |
| **Database Type** | Managed Serverless NoSQL |
| **Performance** | Single-digit millisecond latency |
| **Capacity Modes** | Provisioned or On-Demand |
| **High Availability** | Multi-AZ by default |
| **Transactions** | ACID supported |
| **Cache** | DAX (microsecond reads) |
| **Security** | IAM |
| **Events** | DynamoDB Streams → Lambda/Kinesis |
| **Global Replication** | Global Tables (Active-Active) |
| **TTL** | Auto-delete expired items |
| **Backup** | PITR (35 days) + On-Demand |
| **Export** | To S3 (No RCUs) |
| **Import** | From S3 (No WCUs) |
| **Schema** | Flexible (NoSQL) |

---

# 30-Second Exam Memory Trick

- 🚀 **Serverless NoSQL** → DynamoDB
- ⚡ **Fastest Reads** → DAX
- 🔄 **Table Changes** → DynamoDB Streams
- 🌍 **Multi-Region** → Global Tables
- 🗑️ **Auto Delete** → TTL
- ⏪ **Recover Last 35 Days** → PITR
- 📤 **Export to S3** → No RCUs
- 📥 **Import from S3** → No WCUs
- 🔐 **Permissions** → IAM
- 📈 **Predictable Traffic** → Provisioned
- 🌊 **Unpredictable Traffic** → On-Demand
