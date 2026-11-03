# AWS Glue (AWS SAA-C03) – Beginner Friendly Complete Notes

Imagine your company stores data in different places:

- Customer data → Amazon RDS
- Sales data → CSV files in S3
- Logs → JSON files
- IoT data → Kinesis

Now your manager says:

> "Combine everything into one clean format so our analysts can use it."
> 

This is exactly what **AWS Glue** does.

---

# What is AWS Glue?

AWS Glue is a **fully managed, serverless ETL (Extract, Transform, Load)** service.

Think of it as:

> **A data cleaning and data moving service.**
> 

---

# What is ETL?

This is one of the most important concepts.

ETL =

- **E = Extract**
- **T = Transform**
- **L = Load**

---

## Step 1 — Extract

Collect data from different sources.

Example:

```
Amazon RDS
CSV Files
JSON Files
S3
DynamoDB
```

↓

Glue reads all of them.

---

## Step 2 — Transform

Clean and modify the data.

Examples:

Before

```
Name      Age

John      NULL

Alice     25
```

After

```
Name      Age

John      18

Alice     25
```

Or convert:

- CSV → Parquet
- JSON → CSV
- Remove duplicates
- Filter records
- Merge datasets

---

## Step 3 — Load

Store the cleaned data somewhere.

Example:

```
Amazon Redshift

Amazon S3

Data Warehouse
```

---

# ETL Architecture ⭐⭐⭐⭐⭐

```
Amazon RDS

CSV Files

JSON Files

S3 Bucket

↓

AWS Glue

↓

Clean Data

↓

Amazon Redshift

↓

QuickSight Dashboard
```

Very common AWS architecture.

---

# Why Use Glue?

Without Glue:

```
Download Files

↓

Python Script

↓

Clean Data

↓

Upload Again
```

Manual work.

---

With Glue:

```
Data

↓

Glue

↓

Automatically Cleaned
```

No server management.

---

# Serverless

Glue is completely serverless.

No EC2.

No Hadoop cluster.

No Spark cluster management.

AWS manages everything.

---

# Common Use Cases

✅ Data Cleaning

✅ ETL

✅ Data Migration

✅ Data Warehousing

✅ Data Analytics

---

# Glue Converts Data to Parquet ⭐⭐⭐⭐⭐

Very common exam question.

Suppose your data is stored as:

```
sales.csv
```

Athena can query it,

but CSV is slower and more expensive.

Glue converts:

```
sales.csv

↓

Glue

↓

sales.parquet
```

Benefits:

- Faster Athena queries
- Lower Athena cost
- Better compression

---

### Remember

Glue is commonly used before:

- Athena
- Redshift
- QuickSight

---

# Glue Data Catalog ⭐⭐⭐⭐⭐

Imagine a library.

Without a catalog:

Finding books is difficult.

---

Glue Data Catalog is exactly that.

It stores metadata about datasets.

It does NOT store the actual data.

---

Example

```
S3 Bucket

↓

customer.csv

sales.parquet

orders.json

↓

Glue Data Catalog

↓

Table Names

Column Names

Data Types

Locations
```

Athena uses the Data Catalog to know:

- Table names
- Columns
- Data location

---

### Easy Memory

**Data Catalog = Database Dictionary**

---

# Glue Job Bookmarks ⭐⭐⭐⭐

Suppose yesterday Glue processed:

```
100 Files
```

Today:

```
105 Files
```

Without Job Bookmarks,

Glue processes all 105 files again.

Waste of time.

---

With Job Bookmarks:

```
Yesterday

100 Files

↓

Today

Only New 5 Files
```

It remembers where it stopped.

---

### Easy Memory

Bookmark =

> Continue from where you left off.
> 

---

# Glue Elastic Views ⭐⭐⭐

Imagine data exists in:

- DynamoDB
- RDS
- Redshift

Normally,

you'd write custom code to combine them.

Glue Elastic Views lets you combine data using SQL.

Example:

```
RDS

↓

Glue Elastic Views

↓

DynamoDB

↓

Virtual Table
```

AWS keeps the data synchronized automatically.

---

### Think

Like a SQL View,

but across multiple databases.

---

# Glue DataBrew ⭐⭐⭐⭐

Suppose your Excel file contains:

```
JOHN

john

John

JHON
```

Messy.

DataBrew lets you clean data **without writing code**.

Examples:

- Remove duplicates
- Fix spelling
- Standardize dates
- Replace NULL values

GUI-based.

Perfect for data analysts.

---

# Glue Studio ⭐⭐⭐⭐

Instead of writing ETL code,

Glue Studio provides a graphical interface.

Example:

```
Drag

↓

Drop

↓

Create ETL Pipeline
```

Very beginner friendly.

---

# Glue Streaming ETL ⭐⭐⭐⭐

Normally,

Glue processes batch data.

Streaming ETL processes data continuously.

Example:

```
Kinesis Data Streams

↓

Glue Streaming ETL

↓

Redshift
```

Real-time processing.

---

Supported Sources:

- Kinesis Data Streams
- Apache Kafka
- Amazon MSK

---

# Complete Glue Architecture ⭐⭐⭐⭐⭐

```
          Amazon RDS

          Amazon S3

          DynamoDB

        Kinesis Streams

                │

                ▼

            AWS Glue

       Extract + Transform

                │

                ▼

       Glue Data Catalog

                │

                ▼

      Amazon Redshift

      Amazon Athena

      Amazon QuickSight
```

---

# Glue vs Athena

| Glue | Athena |
| --- | --- |
| ETL | SQL Query |
| Cleans data | Reads data |
| Converts formats | Queries S3 |

---

# Glue vs EMR

| Glue | EMR |
| --- | --- |
| Serverless ETL | Hadoop Cluster |
| Fully Managed | EC2 Cluster |
| Easy | More Control |

---

# Glue vs DataBrew

| Glue | DataBrew |
| --- | --- |
| ETL Pipelines | Visual Data Cleaning |
| Developer | Data Analyst |

---

# Easy Memory Tricks

Imagine washing clothes.

Dirty clothes:

```
CSV

JSON

Excel
```

↓

Washing Machine (Glue)

↓

Clean Clothes

↓

Wardrobe (Redshift)

Glue is simply the washing machine for your data.

---

# AWS SAA-C03 Cheat Sheet

| Feature | Remember |
| --- | --- |
| Service | ETL |
| Meaning | Extract, Transform, Load |
| Type | Fully Managed, Serverless |
| Main Use | Data Cleaning & Transformation |
| Data Catalog | Stores Metadata |
| Job Bookmarks | Avoid reprocessing old data |
| Elastic Views | SQL across multiple databases |
| DataBrew | No-code data cleaning |
| Glue Studio | GUI for ETL |
| Streaming ETL | Kinesis, Kafka, MSK |
| Common Output | Redshift, Athena, S3 |

---

# 30-Second Exam Memory Trick

- 🧹 **Glue = Data Cleaning**
- 📦 **ETL = Extract → Transform → Load**
- ☁️ **Serverless**
- 📚 **Data Catalog = Metadata**
- 🔖 **Job Bookmark = Process only new data**
- 📄 **CSV → Parquet**
- ⚡ **Improves Athena performance**
- 🖱️ **Glue Studio = GUI**
- 🧪 **DataBrew = No-code cleaning**
- 🌊 **Streaming ETL = Kinesis / Kafka**

---

# 🎯 Exam Rules (Very Important)

Choose **AWS Glue** when the question mentions:

- **ETL (Extract, Transform, Load)**
- **Cleaning or transforming data**
- **Converting CSV/JSON to Parquet or ORC**
- **Preparing data for Athena or Redshift**
- **Metadata catalog for datasets (Glue Data Catalog)**
- **Avoid reprocessing previously processed data (Job Bookmarks)**
- **No-code data preparation (DataBrew)**
- **Visual ETL designer (Glue Studio)**
- **Real-time ETL from Kinesis or Kafka (Glue Streaming ETL)**

### ⭐ Most Common SAA Architecture

```
Amazon S3 / RDS
        │
        ▼
     AWS Glue
 (Clean & Transform)
        │
        ▼
   Amazon Redshift
        │
        ▼
 Amazon QuickSight
```

**Remember the flow:**

**Raw Data → Glue (ETL) → Redshift/Athena → QuickSight Dashboard**.
