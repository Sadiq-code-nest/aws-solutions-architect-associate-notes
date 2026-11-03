# Amazon Athena (AWS SAA-C03) – Beginner Friendly Complete Notes

Imagine your company stores **10 TB of log files** in Amazon S3.

Example:

```
S3 Bucket

├── logs/
├── sales.csv
├── cloudtrail/
├── vpc-flow-logs/
└── customers.parquet
```

Now your manager asks:

> "How many users logged in yesterday?"
> 

Do you need to download the files?

❌ No.

Do you need to create a database?

❌ No.

Instead, simply write an SQL query.

That's exactly what **Amazon Athena** does.

---

# What is Amazon Athena?

Amazon Athena is a **serverless SQL query service** used to analyze data stored directly in **Amazon S3**.

Think of it as:

> **"SQL for files stored in S3."**
> 

---

# Why Do We Need Athena?

Suppose your application stores logs in S3.

Without Athena:

```
Download Files

↓

Open Excel

↓

Write Scripts

↓

Analyze Data
```

Slow and inefficient.

With Athena:

```sql
SELECT *
FROM logs
WHERE status = 500;
```

Done.

No servers.

No databases.

---

# Serverless

Athena is fully serverless.

You don't manage:

- Servers
- Databases
- Clusters

AWS runs everything automatically.

---

# Uses Standard SQL

Athena supports standard SQL.

Example:

```sql
SELECT name
FROM employees
WHERE salary > 50000;
```

If you know SQL,

you already know Athena.

---

# Reads Data Directly from S3

Athena does **not** store your data.

It simply queries files already stored in Amazon S3.

Example:

```
Amazon S3

↓

CSV Files

↓

Athena SQL Query

↓

Results
```

---

# Supported File Formats

Athena can query many formats:

- CSV
- JSON
- Parquet ⭐
- ORC ⭐
- Avro

No need to convert to a database first.

---

# Pricing

Athena charges based on **how much data it scans**.

Current pricing (commonly tested):

> **$5 per TB scanned**
> 

Example:

You scan:

```
100 GB
```

You pay only for 100 GB.

---

## Exam Tip

Athena pricing depends on:

✅ Data scanned

NOT:

- Query time
- Number of users
- Number of queries

---

# Common Use Cases

## 1. Business Intelligence

Example:

```
Sales Reports

↓

Athena

↓

Dashboard
```

---

## 2. Reporting

Monthly reports.

Sales reports.

Financial reports.

---

## 3. Analyze Logs ⭐⭐⭐⭐⭐

Athena is commonly used to analyze:

- CloudTrail Logs
- VPC Flow Logs
- ELB Access Logs
- ALB Logs
- Application Logs

Instead of downloading them.

---

## 4. Ad-hoc Queries

Need a quick answer?

Just write SQL.

---

# Athena + QuickSight

Very common exam architecture.

```
S3

↓

Athena

↓

QuickSight

↓

Dashboard
```

Athena queries the data.

QuickSight creates charts and reports.

---

# Performance Improvement ⭐⭐⭐⭐⭐

These questions appear frequently in the SAA exam.

---

## 1. Use Columnar Formats

Instead of CSV,

store data as:

- Parquet ⭐
- ORC ⭐

Why?

Athena reads only the required columns.

Less data scanned.

Lower cost.

Much faster.

---

### Example

CSV:

```
Name | Age | Salary | City
```

If you only need Salary,

CSV still reads everything.

---

Parquet:

Athena reads only:

```
Salary
```

Huge performance improvement.

---

## 2. Use AWS Glue

Suppose your files are CSV.

AWS Glue can convert them to:

- Parquet
- ORC

This reduces Athena costs significantly.

---

## 3. Compress Files

Supported compression:

- gzip
- Snappy
- zstd
- lz4
- bzip2

Smaller files

↓

Less data scanned

↓

Lower cost

---

## 4. Partition Data ⭐⭐⭐⭐⭐

Instead of:

```
sales.csv
```

Store data like:

```
sales/

year=2025/

month=07/

day=20/
```

Example:

```
s3://sales-data/

year=2025/

month=07/

day=20/
```

Now query:

```sql
SELECT *
FROM sales
WHERE year=2025
```

Athena reads only that folder.

Much faster.

Much cheaper.

---

## 5. Use Large Files

Instead of:

```
100,000 tiny files
```

Use:

```
Large files (>128 MB)
```

Benefits:

- Less overhead
- Better performance

---

# Athena Federated Query ⭐⭐⭐⭐

Normally,

Athena queries only S3.

Federated Query allows Athena to query:

- RDS
- DynamoDB
- CloudWatch Logs
- Redshift
- On-premises databases
- Custom data sources

using SQL.

---

## How Does It Work?

Athena uses:

```
Athena

↓

AWS Lambda

↓

Connector

↓

Data Source
```

AWS provides Lambda connectors.

---

Example:

```
Athena

↓

Lambda Connector

↓

DynamoDB
```

Run SQL against DynamoDB.

---

Or:

```
Athena

↓

Lambda

↓

RDS
```

Run SQL without moving the data.

---

## Query Results

Regardless of the source,

Athena stores the query results in:

```
Amazon S3
```

---

# Athena vs Redshift

| Amazon Athena | Amazon Redshift |
| --- | --- |
| Serverless | Data Warehouse |
| Queries files in S3 | Stores data internally |
| Pay per TB scanned | Pay for cluster |
| Ad-hoc analytics | Large BI workloads |

---

# Athena vs RDS

| Athena | RDS |
| --- | --- |
| Query files | Store relational data |
| Serverless | Managed SQL database |
| Reads S3 | Stores application data |

---

# Easy Memory Tricks

Imagine S3 is a huge library.

Athena doesn't move the books.

It simply walks into the library,

reads the pages you ask for,

and gives you the answer.

---

# AWS SAA-C03 Cheat Sheet

| Feature | Remember |
| --- | --- |
| **Service** | Serverless SQL Query Service |
| **Data Source** | Amazon S3 |
| **Language** | Standard SQL (Presto engine) |
| **Pricing** | $5 per TB scanned |
| **Formats** | CSV, JSON, Parquet, ORC, Avro |
| **Performance** | Parquet, ORC, Compression, Partitioning |
| **Reports** | Works with QuickSight |
| **Federated Query** | Query RDS, DynamoDB, CloudWatch via Lambda |
| **Results Stored In** | Amazon S3 |

---

# 30-Second Exam Memory Trick

- 🔍 **Athena = SQL on S3**
- ☁️ **Serverless**
- 📝 **Uses Standard SQL**
- 💰 **Pay per TB scanned**
- 📂 **Reads CSV, JSON, Parquet, ORC**
- 🚀 **Parquet + ORC = Faster & Cheaper**
- 🗂️ **Partition Data = Faster Queries**
- 🗜️ **Compression = Lower Cost**
- 📊 **QuickSight + Athena = Dashboards**
- 🔄 **Federated Query = Query RDS/DynamoDB using Lambda**
- 📦 **Results saved to S3**

## 🎯 Exam Rule

If the question says:

- **Analyze files stored in S3**
- **Run SQL without managing servers**
- **Analyze CloudTrail, VPC Flow Logs, or ELB logs**
- **Pay per TB scanned**
- **Business intelligence on S3 data**

➡️ The correct answer is **Amazon Athena**.

### ⭐ Most Common SAA Question

**"You have log files in S3 and want to analyze them using SQL without managing any infrastructure."**

✅ **Answer: Amazon Athena**
