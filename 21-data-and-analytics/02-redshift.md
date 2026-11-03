# Amazon Redshift (AWS SAA-C03) – Beginner Friendly Complete Notes

Imagine your company has **10 years of sales data**.

Your CEO asks:

- 📈 Total sales this year?
- 🏆 Best-selling products?
- 🌍 Sales by country?
- 📊 Monthly revenue trend?

This involves analyzing **millions or billions of rows**.

A normal database (RDS) isn't designed for these large analytical queries.

This is where **Amazon Redshift** comes in.

---

# What is Amazon Redshift?

Amazon Redshift is a **fully managed Data Warehouse** service for **analytics (OLAP)**.

Think of it as:

> **A giant SQL database optimized for analyzing huge amounts of data.**
> 

---

# What is a Data Warehouse?

A Data Warehouse stores **historical data** for reporting and business intelligence (BI).

Example:

```
Sales Data
2020
2021
2022
2023
2024
2025
```

Managers use it to answer business questions, not to run day-to-day transactions.

---

# OLTP vs OLAP ⭐⭐⭐⭐⭐

This is one of the most important exam concepts.

| OLTP | OLAP |
| --- | --- |
| Online Transaction Processing | Online Analytical Processing |
| Daily business operations | Data analysis & reporting |
| Many small transactions | Large analytical queries |
| RDS, Aurora | Redshift |
| Insert, Update, Delete | SUM, AVG, GROUP BY |

### Example

**OLTP (RDS):**

```sql
INSERT INTO Orders ...
```

A customer places an order.

---

**OLAP (Redshift):**

```sql
SELECT SUM(sales)
FROM Orders
GROUP BY country;
```

Analyze millions of orders.

---

## 📝 Exam Tip

- **Transactions → RDS**
- **Analytics → Redshift**

---

# PostgreSQL Based

Redshift is based on PostgreSQL.

However,

❌ It is **NOT** a normal PostgreSQL database.

It has been modified for analytics.

---

# Extremely Fast

AWS states Redshift can provide **up to 10× better performance** than traditional data warehouses.

Why?

Because it uses:

- Columnar Storage
- Parallel Processing
- Massive distributed computing

---

# Columnar Storage ⭐⭐⭐⭐⭐

This is heavily tested.

---

## Normal Database (Row Storage)

Stores data like:

```
Name | Age | Salary | City

John | 25 | 5000 | Dhaka

Alice| 28 | 6000 | London
```

Everything in one row.

If you need only Salary,

the database still reads every column.

---

## Redshift (Column Storage)

Stores columns separately.

```
Name

John

Alice
```

```
Salary

5000

6000
```

Now if you need Salary,

Redshift reads only the Salary column.

Much faster.

---

# Parallel Query Engine ⭐⭐⭐⭐

Redshift splits a query across many servers.

Example:

```
100 Million Rows

↓

Node 1 → 25M

Node 2 → 25M

Node 3 → 25M

Node 4 → 25M
```

All nodes work simultaneously.

This makes analytics much faster.

---

# Deployment Modes

## 1. Provisioned Cluster

You choose:

- Instance type
- Number of nodes
- Storage

Best for predictable workloads.

You can purchase **Reserved Instances** to save money.

---

## 2. Serverless

No cluster management.

AWS automatically manages:

- Compute
- Scaling
- Capacity

Pay only for what you use.

---

# SQL Interface

Redshift supports SQL.

Example:

```sql
SELECT SUM(sales)
FROM Orders
GROUP BY country;
```

Easy for SQL developers.

---

# BI Tool Integration

Redshift integrates with:

- Amazon QuickSight
- Tableau
- Power BI
- Looker

Typical architecture:

```
Redshift

↓

QuickSight

↓

Dashboard
```

---

# Redshift vs Athena ⭐⭐⭐⭐⭐

Very common exam question.

| Redshift | Athena |
| --- | --- |
| Data Warehouse | Query Service |
| Stores data | Reads files from S3 |
| Faster for joins & aggregations | Good for ad-hoc queries |
| Long-running analytics | Analyze S3 directly |
| Cluster or Serverless | Fully Serverless |

### Example

**Data already stored in S3?**

➡️ Use Athena.

---

**Huge BI dashboard with complex joins?**

➡️ Use Redshift.

---

# Redshift Cluster Architecture

A Redshift cluster has two types of nodes.

---

## 1. Leader Node

Responsible for:

- Receiving SQL queries
- Planning execution
- Combining results

Think of it as the **manager**.

---

## 2. Compute Nodes

Responsible for:

- Processing data
- Running queries
- Returning results

Think of them as the **workers**.

Example:

```
User

↓

Leader Node

↓

Compute Node 1

Compute Node 2

Compute Node 3

↓

Leader combines results

↓

User
```

---

# Snapshots (Backups)

Redshift creates **Snapshots**.

Snapshots are:

- Point-in-time backups
- Stored internally in Amazon S3
- Incremental (only changed blocks are saved)

---

## Automated Snapshots

Created:

- Every 8 hours
- Every 5 GB of changes
- Or on a custom schedule

Retention can be configured.

---

## Manual Snapshots

Created manually.

Remain until you delete them.

Useful before upgrades or migrations.

---

## Disaster Recovery

Redshift can automatically copy snapshots to another AWS Region.

This provides **cross-region disaster recovery**.

---

# Multi-AZ

Some Redshift clusters support **Multi-AZ** deployment.

Provides:

- High Availability
- Automatic failover

---

# Loading Data into Redshift

Large batch inserts perform much better than many small inserts.

AWS recommends bulk loading.

Common methods:

- **COPY** command from Amazon S3 ⭐
- Amazon Kinesis Data Firehose
- JDBC/ODBC
- EC2 applications

---

## COPY Command ⭐⭐⭐⭐⭐

The fastest and most common method.

```
Amazon S3

↓

COPY Command

↓

Redshift
```

Very common exam question.

---

# Redshift Spectrum ⭐⭐⭐⭐⭐

Suppose:

You have 100 TB of data in S3.

You don't want to import it into Redshift.

Instead,

Redshift Spectrum lets you query S3 directly.

Example:

```
Redshift

↓

Spectrum

↓

Amazon S3
```

No need to load the files first.

---

### Important

You **must have a Redshift cluster** to use Redshift Spectrum.

Spectrum is **not** a standalone service.

---

# Common Use Cases

## 1. Business Intelligence ⭐⭐⭐⭐⭐

Company dashboards.

Sales reports.

Revenue analysis.

---

## 2. Data Warehouse

Store years of historical business data.

---

## 3. Reporting

Generate monthly and yearly reports.

---

## 4. Analytics

Analyze:

- Customer behavior
- Sales
- Marketing
- Financial data

---

## 5. Large SQL Queries

Millions or billions of rows.

Complex joins.

Aggregations.

---

# Redshift vs RDS

| Redshift | RDS |
| --- | --- |
| OLAP | OLTP |
| Analytics | Transactions |
| Data Warehouse | Relational Database |
| Historical analysis | Day-to-day operations |

---

# Redshift vs Athena

| Redshift | Athena |
| --- | --- |
| Stores data | Reads S3 |
| Better joins | Simple SQL on S3 |
| BI dashboards | Ad-hoc analysis |
| Cluster/Serverless | Serverless only |

---

# Easy Memory Tricks

Imagine a company CEO.

They don't care about one order.

They want:

- Monthly revenue
- Best-selling products
- Sales by country

That's exactly what Redshift is built for.

Think:

**Analytics → Redshift**

---

# AWS SAA-C03 Cheat Sheet

| Feature | Remember |
| --- | --- |
| **Service Type** | Data Warehouse |
| **Purpose** | OLAP (Analytics) |
| **Database Base** | PostgreSQL (modified) |
| **Storage** | Columnar |
| **Performance** | Parallel Query Engine |
| **Deployment** | Provisioned or Serverless |
| **Backups** | Incremental Snapshots in S3 |
| **DR** | Cross-Region Snapshot Copy |
| **Loading Data** | COPY command from S3 |
| **Query S3** | Redshift Spectrum |
| **BI Integration** | QuickSight, Tableau |

---

# 30-Second Exam Memory Trick

- 🏢 **Redshift = Data Warehouse**
- 📊 **OLAP = Analytics**
- 📝 **Uses SQL**
- 📦 **Columnar Storage**
- ⚡ **Parallel Processing**
- ☁️ **Provisioned or Serverless**
- 💾 **Snapshots stored in S3**
- 🔄 **Incremental Backups**
- 📥 **COPY from S3 = Fastest Data Load**
- 🌐 **Spectrum = Query S3 without importing**
- 📈 **Best for BI, Reports, Dashboards**

## 🎯 Exam Rule

If the question mentions:

- **Data Warehouse**
- **Business Intelligence (BI)**
- **Analytics**
- **OLAP**
- **Historical reporting**
- **Large SQL aggregations**
- **QuickSight dashboards**
- **Columnar storage**
- **Redshift Spectrum**
- **COPY command from S3**

➡️ The correct answer is **Amazon Redshift**.
