# AWS SAA-C03 – Choosing the Right Database (Compact Exam Notes)

## Step 1: Ask These Questions

Before choosing a database, ask:

- 📖 **Read-heavy or Write-heavy?**
- 📈 **Will traffic scale or fluctuate?**
- 💾 **How much data? How long to store it?**
- ⚡ **Low latency required?**
- 👥 **How many concurrent users?**
- 🗂️ **Structured (SQL) or Flexible (NoSQL)?**
- 🔗 **Need JOINs or relationships?**
- 📊 **Need analytics/reporting?**
- 🔍 **Need full-text search?**
- 💰 **Need cloud-native to reduce licensing costs? (Aurora)**

---

# Database Selection Cheat Sheet

| Requirement | AWS Service |
| --- | --- |
| **Relational SQL (OLTP), JOINs, Transactions** | **Amazon RDS / Amazon Aurora** |
| **High-speed NoSQL, key-value, JSON, serverless** | **Amazon DynamoDB** |
| **In-memory cache** | **Amazon ElastiCache (Redis/Memcached)** |
| **Document database (MongoDB compatible)** | **Amazon DocumentDB** |
| **Graph relationships (social networks, recommendations)** | **Amazon Neptune** |
| **Apache Cassandra workloads** | **Amazon Keyspaces** |
| **Large object storage (images, videos, files)** | **Amazon S3** |
| **Archive & long-term backup** | **Amazon S3 Glacier** |
| **Data warehouse / Business Intelligence (OLAP)** | **Amazon Redshift** |
| **SQL queries directly on S3** | **Amazon Athena** |
| **Big Data processing (Spark, Hadoop)** | **Amazon EMR** |
| **Full-text search & log analytics** | **Amazon OpenSearch Service** |
| **Immutable ledger / audit trail** | **Amazon QLDB (Quantum Ledger Database)** |
| **Time-series data (IoT, metrics, monitoring)** | **Amazon Timestream** |

---

# Database Categories

## 1. Relational Database (SQL / OLTP)

**Services:**

- Amazon RDS
- Amazon Aurora

**Best for:**

- Structured data
- Transactions
- JOIN operations
- Banking
- E-commerce
- ERP systems

**Remember:** **SQL + JOINs = RDS/Aurora**

---

## 2. NoSQL Databases

### Amazon DynamoDB

- JSON-like data
- Key-Value
- Massive scale
- Serverless

**Best for:** Mobile apps, gaming, shopping carts

---

### Amazon ElastiCache

- Redis / Memcached
- In-memory cache

**Best for:** Speeding up applications

---

### Amazon DocumentDB

- MongoDB compatible

**Best for:** Document-based applications

---

### Amazon Neptune

- Graph database

**Best for:**

- Social networks
- Friend recommendations
- Fraud detection
- Network relationships

---

### Amazon Keyspaces

- Apache Cassandra compatible

**Best for:** Cassandra workloads without managing servers

---

## 3. Object Storage

### Amazon S3

Store:

- Images
- Videos
- Documents
- Backups
- Static website files

---

### Amazon Glacier

Store:

- Long-term archives
- Compliance data
- Rarely accessed backups

---

## 4. Data Warehouse / Analytics (OLAP)

### Amazon Redshift

- SQL analytics
- BI reporting
- Dashboards

**Best for:** Business Intelligence

---

### Amazon Athena

- Run SQL directly on S3
- No servers

**Best for:** Ad-hoc queries on S3 data

---

### Amazon EMR

- Hadoop
- Spark
- Big Data processing

---

## 5. Search

### Amazon OpenSearch Service

- Full-text search
- JSON documents
- Log analytics

**Best for:**

- Website search
- Log analysis
- Kibana/OpenSearch Dashboards

---

## 6. Ledger Database

### Amazon QLDB

Immutable transaction history

**Best for:**

- Financial records
- Audit logs
- Compliance

---

## 7. Time-Series Database

### Amazon Timestream

Stores data over time

Examples:

- IoT sensors
- CPU metrics
- Temperature readings
- Monitoring dashboards

---

# Easy Memory Tricks

| Service | Memory Trick |
| --- | --- |
| **RDS / Aurora** | SQL + JOINs |
| **DynamoDB** | JSON + NoSQL + Serverless |
| **ElastiCache** | Cache = Fast Memory |
| **DocumentDB** | MongoDB |
| **Neptune** | Graphs & Relationships |
| **Keyspaces** | Cassandra |
| **S3** | Files & Objects |
| **Glacier** | Archives |
| **Redshift** | Analytics & BI |
| **Athena** | SQL on S3 |
| **EMR** | Big Data |
| **OpenSearch** | Search Engine |
| **QLDB** | Ledger / Audit |
| **Timestream** | Time-Series |

---

# AWS SAA-C03 Exam Decision Tree

**Need SQL + JOINs?** → **RDS / Aurora**

**Need NoSQL + Massive Scale?** → **DynamoDB**

**Need Caching?** → **ElastiCache**

**Need MongoDB?** → **DocumentDB**

**Need Graph Relationships?** → **Neptune**

**Need Cassandra?** → **Keyspaces**

**Need File Storage?** → **S3**

**Need Long-Term Archive?** → **Glacier**

**Need Business Analytics?** → **Redshift**

**Need SQL Queries on S3?** → **Athena**

**Need Big Data Processing?** → **EMR**

**Need Search Engine?** → **OpenSearch**

**Need Immutable Ledger?** → **QLDB**

**Need Time-Series Data?** → **Timestream**

---

# ⭐ Final 30-Second Exam Summary

| Requirement | Choose |
| --- | --- |
| SQL + Transactions + JOINs | **RDS / Aurora** |
| NoSQL + Serverless | **DynamoDB** |
| Cache | **ElastiCache** |
| MongoDB | **DocumentDB** |
| Graph Data | **Neptune** |
| Cassandra | **Keyspaces** |
| File Storage | **S3** |
| Archive | **Glacier** |
| Analytics / BI | **Redshift** |
| SQL on S3 | **Athena** |
| Big Data | **EMR** |
| Search | **OpenSearch** |
| Ledger | **QLDB** |
| Time-Series | **Timestream** |
