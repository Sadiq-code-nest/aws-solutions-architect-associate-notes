# Amazon EMR (Elastic MapReduce) – Beginner Friendly Complete Notes (AWS SAA-C03)

Before understanding **Amazon EMR**, you first need to understand **Hadoop**.

---

# Step 1: What is Big Data?

Imagine Facebook.

Every second users generate:

- Photos 📷
- Videos 🎥
- Likes 👍
- Comments 💬
- Messages ✉️

Or Amazon:

- Millions of orders
- Customer clicks
- Search history
- Payments

This is called **Big Data**.

Characteristics:

- Huge size (TBs → PBs)
- Generated very quickly
- Too large for one computer

---

# Problem

Suppose you have:

```
100 TB of data
```

Can one laptop process it?

❌ No.

Even one powerful server may take days.

So instead, we split the work across **many computers**.

---

# What is Hadoop?

**Hadoop** is an **open-source Big Data framework** that processes huge datasets by distributing them across many machines.

Think of Hadoop as:

> **A team of workers instead of one worker.**
> 

---

# Real Example

Imagine you have **1000 books** to count words.

### One Person

```
1000 Books

↓

One Person

↓

5 Days
```

Very slow.

---

### 100 People

```
1000 Books

↓

100 People

↓

Each reads 10 books

↓

Combine results

↓

30 Minutes
```

Much faster.

This is exactly how Hadoop works.

---

# How Hadoop Works

Suppose you have:

```
100 GB File
```

Instead of processing it on one machine,

Hadoop splits it.

```
100 GB

↓

25 GB

25 GB

25 GB

25 GB

↓

Server 1

Server 2

Server 3

Server 4

↓

Combine Results
```

This is called **Distributed Processing**.

---

# Two Main Parts of Hadoop

## 1. HDFS (Storage)

HDFS = Hadoop Distributed File System

Instead of storing one big file on one server,

it stores pieces across many servers.

Example:

```
Movie.mp4 (100 GB)

↓

Block 1 → Server A

Block 2 → Server B

Block 3 → Server C

Block 4 → Server D
```

If one server fails,

the file is still available because Hadoop keeps copies.

---

## 2. MapReduce (Processing)

MapReduce is Hadoop's processing engine.

Think:

### MAP

Everyone works separately.

```
Server A

↓

Count words
```

```
Server B

↓

Count words
```

```
Server C

↓

Count words
```

---

### REDUCE

Combine everything.

```
Server A Result

↓

Server B Result

↓

Server C Result

↓

Final Answer
```

Hence the name:

> **Map → Reduce**
> 

---

# Why Hadoop?

Without Hadoop:

```
Huge Data

↓

One Server

↓

Very Slow
```

With Hadoop:

```
Huge Data

↓

100 Servers

↓

Parallel Processing

↓

Very Fast
```

---

# What is Amazon EMR?

EMR stands for:

> **Elastic MapReduce**
> 

Amazon EMR is a **managed Hadoop and Big Data service**.

AWS installs and manages Hadoop for you.

You don't install:

- Hadoop
- Spark
- HBase
- Flink
- Presto

AWS does everything.

---

# Why Use EMR?

Installing Hadoop manually requires:

- Many EC2 instances
- Networking
- Storage
- Configuration
- Monitoring

Very difficult.

EMR automates everything.

---

Instead of this:

```
Launch 50 EC2

↓

Install Hadoop

↓

Configure Cluster

↓

Networking

↓

Storage

↓

Monitoring
```

You simply click:

```
Create EMR Cluster
```

Done.

---

# EMR Architecture

```
Big Data

↓

Amazon S3

↓

Amazon EMR

↓

Apache Spark

↓

Processed Data

↓

Redshift
```

Very common AWS architecture.

---

# EMR Can Launch Hundreds of EC2 Instances

Example:

```
EMR Cluster

↓

200 EC2 Instances

↓

Process 500 TB Data
```

AWS creates them automatically.

---

# EMR Comes With Big Data Tools

EMR already includes:

### Apache Spark ⭐⭐⭐⭐⭐

Most common processing engine today.

Very fast.

Used for:

- ETL
- Machine Learning
- Analytics

---

### Hadoop

Traditional Big Data framework.

---

### Presto

SQL queries on Big Data.

---

### Flink

Real-time stream processing.

---

### HBase

NoSQL database for Hadoop.

---

# Auto Scaling

If workload increases:

```
20 EC2

↓

50 EC2

↓

100 EC2
```

Automatically.

When workload decreases,

EMR removes instances.

---

# Spot Instance Support

To reduce cost,

EMR can use Spot Instances.

Perfect for temporary jobs.

---

# EMR Use Cases

## 1. Big Data Analytics ⭐⭐⭐⭐⭐

Analyze:

- Sales
- Website clicks
- Customer behavior

---

## 2. Machine Learning

Train models on huge datasets.

---

## 3. ETL

Extract

↓

Transform

↓

Load

large datasets.

---

## 4. Log Processing

Analyze TBs of logs.

---

## 5. Web Indexing

Search engines process billions of web pages.

---

# Amazon EMR Node Types ⭐⭐⭐⭐⭐

EMR clusters have three node types.

---

## 1. Master Node

Think of it as the **Manager**.

Responsibilities:

- Manage cluster
- Monitor health
- Schedule work
- Coordinate nodes

Usually:

One master node.

---

Diagram:

```
           Master Node

          (Manager)

          /     |     \

        Core  Core   Core
```

---

## 2. Core Node

Think of them as **Workers**.

Responsibilities:

- Process data
- Store data (HDFS)

They perform most of the work.

Usually long-running.

---

## 3. Task Node

Optional.

Responsibilities:

- Process tasks only
- Does NOT store data

Usually Spot Instances.

Easy to add or remove.

---

## Complete Architecture

```
              Master Node

             (Coordinator)

             /     |      \

      Core      Core      Core

     (Store + Compute)

        \         |        /

        Task   Task   Task

       (Compute Only)
```

---

# Purchasing Options

## On-Demand

Most reliable.

Never interrupted.

Best for:

Production workloads.

---

## Reserved

Commit for 1 year or more.

Lower cost.

Best for:

Long-running clusters.

---

## Spot

Very cheap.

Can be terminated anytime.

Best for:

Temporary workloads.

Task Nodes usually use Spot.

---

# Long Running vs Transient Cluster

## Long Running

Cluster stays active.

Example:

```
EMR

↓

24/7

↓

Daily Analytics
```

---

## Transient Cluster

Temporary.

Example:

```
Create Cluster

↓

Run Job

↓

Delete Cluster
```

Pay only while it runs.

---

# EMR vs Redshift

| Amazon EMR | Amazon Redshift |
| --- | --- |
| Big Data Processing | Data Warehouse |
| Spark, Hadoop | SQL Analytics |
| ETL | Reporting |
| Batch Processing | BI |

---

# EMR vs Athena

| EMR | Athena |
| --- | --- |
| Big Data Framework | SQL Query Service |
| Spark/Hadoop | SQL only |
| Complex processing | Simple SQL on S3 |

---

# EMR vs Glue

| EMR | Glue |
| --- | --- |
| Hadoop/Spark Cluster | Serverless ETL |
| More control | Easier management |
| Long-running jobs | ETL pipelines |

---

# Easy Memory Tricks

Imagine moving **1,000,000 boxes**.

One worker:

❌ Impossible.

100 workers:

✅ Easy.

Hadoop = Many workers.

EMR = AWS automatically hires and manages those workers for you.

---

# AWS SAA-C03 Cheat Sheet

| Feature | Remember |
| --- | --- |
| EMR | Elastic MapReduce |
| Purpose | Big Data Processing |
| Based On | Hadoop |
| Processing | Distributed |
| Cluster | Hundreds of EC2 instances |
| Tools | Spark, Hadoop, HBase, Flink, Presto |
| Scaling | Auto Scaling |
| Spot Support | Yes |
| Storage | Usually Amazon S3 and HDFS |
| Best Use Cases | ETL, Analytics, ML, Big Data |

---

# Node Types Cheat Sheet

| Node | Responsibility |
| --- | --- |
| **Master** | Manage Cluster |
| **Core** | Store Data + Process Data |
| **Task** | Process Only (No Storage) |

---

# 30-Second Exam Memory Trick

- 🐘 **EMR = Elastic MapReduce**
- 📊 **Big Data Processing**
- 🏗️ **Managed Hadoop Cluster**
- ⚡ **Apache Spark included**
- ☁️ **Runs on EC2**
- 📈 **Auto Scaling**
- 💰 **Supports Spot Instances**
- 👨‍💼 **Master = Manager**
- 💾 **Core = Store + Compute**
- 🏃 **Task = Compute Only**
- 📦 **Use for ETL, ML, Big Data Analytics**

## 🎯 Exam Rules (Very Important)

- If the question mentions **processing petabytes of data**, **Apache Hadoop**, **Apache Spark**, **distributed batch processing**, or **Big Data analytics**, choose **Amazon EMR**.
- If it asks for **SQL analytics on a data warehouse**, choose **Amazon Redshift**.
- If it asks for **serverless SQL queries directly on S3**, choose **Amazon Athena**.
- If it asks for **serverless ETL**, think **AWS Glue** rather than EMR.
