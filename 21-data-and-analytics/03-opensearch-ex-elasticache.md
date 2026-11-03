# Amazon OpenSearch Service (AWS SAA-C03) – Beginner Friendly Notes

Imagine you own an **online shopping website**.

A customer searches:

> **"iphone"**
> 

Your database contains:

```
iPhone 16 Pro

Apple iPhone Charger

iPhone Case

iPhone Screen Protector
```

The customer expects all related products instantly.

A normal database (RDS/DynamoDB) is **not optimized for searching text** like Google.

This is why we use **Amazon OpenSearch**.

---

# What is Amazon OpenSearch?

Amazon OpenSearch is a **managed search and analytics engine**.

Think of it as:

> **Google Search for your own data.**
> 

It lets users search:

- Products
- Logs
- Documents
- Websites
- Emails
- Error messages

very quickly.

---

# Why Not Use DynamoDB?

Suppose your DynamoDB table contains:

| ProductID | Product Name |
| --- | --- |
| 101 | Apple iPhone 16 |
| 102 | Samsung S25 |
| 103 | Apple Watch |

DynamoDB works best when you know the **Primary Key**.

Example:

```
ProductID = 101
```

Very fast.

But what if the user searches:

```
iphone
```

or

```
apple phone
```

or

```
iph
```

DynamoDB is not built for these searches.

OpenSearch is.

---

# Partial Search

Example:

Database:

```
Apple iPhone 16 Pro
```

User types:

```
iph
```

OpenSearch still finds:

```
Apple iPhone 16 Pro
```

Google-like searching.

---

# Search Any Field

Suppose:

```
Product

Name

Description

Category

Brand

Reviews
```

OpenSearch can search **all fields simultaneously**.

---

# OpenSearch Complements a Database

OpenSearch is **NOT** usually your main database.

Normally:

```
Application

↓

RDS / DynamoDB

↓

OpenSearch
```

The database stores data.

OpenSearch provides **fast searching**.

---

# Deployment Modes

### 1. Managed Cluster

You manage:

- Cluster size
- Nodes
- Scaling

AWS manages the infrastructure.

---

### 2. Serverless

No clusters to manage.

AWS automatically scales.

---

# SQL Support

OpenSearch is **not primarily SQL-based**.

It uses its own search API.

SQL can be enabled using a plugin.

---

# Security

Supports:

- IAM
- Cognito
- KMS Encryption
- TLS Encryption

---

# OpenSearch Dashboards

Think of it as Grafana for OpenSearch.

It provides:

- Graphs
- Charts
- Dashboards
- Log Analysis

Example:

```
Error Rate

CPU

Requests

Top Errors
```

---

# Common Use Cases

✅ Website Search

✅ Product Search

✅ Log Analytics

✅ Monitoring

✅ Security Analytics

✅ Full Text Search

---

# Pattern 1 — CloudWatch Logs → OpenSearch ⭐⭐⭐⭐⭐

This is one of the most common architectures in AWS.

---

## Problem

Your application generates logs.

Example:

```
Server

↓

Application Logs

↓

CloudWatch Logs
```

Thousands of logs every second.

Searching CloudWatch directly becomes difficult.

---

## Solution

Send logs to OpenSearch.

### Diagram

```
           Application

                │

                ▼

        CloudWatch Logs

                │

      Subscription Filter

                │

                ▼

      Amazon OpenSearch

                │

                ▼

     OpenSearch Dashboards

                │

                ▼

 Search Errors / Analytics
```

---

### Example

Suppose logs contain:

```
Error 404

Error 500

Database Timeout

Login Failed
```

In OpenSearch Dashboard,

you search:

```
timeout
```

Immediately all timeout logs appear.

---

### Real Example

You type:

```
payment failed
```

OpenSearch instantly shows:

- Time
- User
- Server
- Error

---

### Exam Memory

> **CloudWatch Logs → OpenSearch = Search and visualize logs**
> 

---

# Pattern 2 — Kinesis → OpenSearch ⭐⭐⭐⭐⭐

Very common exam architecture.

---

## Problem

Millions of events arrive every second.

Example:

```
Website Clicks

IoT Devices

Application Events

Transactions
```

Need real-time search.

---

## Architecture

### Option 1

Kinesis Data Streams

```
Users

↓

Kinesis Data Streams

↓

Lambda / Consumer

↓

OpenSearch

↓

Dashboard
```

Here,

you write your own consumer.

More control.

---

### Option 2

Kinesis Data Firehose ⭐⭐⭐⭐⭐

Much easier.

```
Users

↓

Kinesis Data Firehose

↓

Amazon OpenSearch

↓

Dashboard
```

No Lambda required.

Firehose automatically delivers data.

---

## Why Firehose?

Firehose:

- Buffers data
- Retries automatically
- Delivers continuously
- No infrastructure

Perfect for log analytics.

---

# Streams vs Firehose for OpenSearch

## Kinesis Data Streams

```
Applications

↓

Data Streams

↓

Custom Consumer

↓

OpenSearch
```

You manage the consumer.

Greater flexibility.

---

## Kinesis Data Firehose

```
Applications

↓

Firehose

↓

OpenSearch
```

AWS manages delivery.

Very easy.

---

# Real Example

Suppose Netflix receives:

```
User Login

Movie Search

Play Video

Pause Video

Like Movie
```

Millions every second.

Firehose continuously sends events to OpenSearch.

Managers search:

```
movie=Batman
```

See analytics instantly.

---

# DynamoDB vs OpenSearch

| DynamoDB | OpenSearch |
| --- | --- |
| Key-Value NoSQL | Search Engine |
| Primary Key lookup | Full-text search |
| Exact search | Partial search |
| Stores data | Searches data |

---

# Athena vs OpenSearch

| Athena | OpenSearch |
| --- | --- |
| SQL queries on S3 | Real-time search |
| Reporting | Search |
| Analytics | Log analysis |

---

# Easy Memory Tricks

Imagine Google.

Google doesn't know your Product ID.

It searches:

- Name
- Description
- Text
- Keywords

That's exactly what OpenSearch does.

Think:

> **Google Search for AWS Data**
> 

---

# AWS SAA-C03 Cheat Sheet

| Feature | Remember |
| --- | --- |
| Service | Search Engine |
| Best For | Full-text Search |
| Search | Any field |
| Partial Match | Yes |
| Modes | Managed & Serverless |
| Dashboard | OpenSearch Dashboards |
| Security | IAM, Cognito, KMS, TLS |
| Common Inputs | CloudWatch Logs, Firehose, IoT |
| SQL | Plugin only |
| Common Pair | DynamoDB / RDS + OpenSearch |

---

# Architecture Cheat Sheet

### CloudWatch Logs

```
Application
      │
      ▼
CloudWatch Logs
      │
      ▼
OpenSearch
      │
      ▼
Dashboards
```

👉 Best for **log searching and visualization**.

---

### Kinesis Data Firehose

```
Application
      │
      ▼
Kinesis Firehose
      │
      ▼
OpenSearch
      │
      ▼
Dashboards
```

👉 Best for **real-time log ingestion with no custom code**.

---

### Kinesis Data Streams

```
Application
      │
      ▼
Kinesis Data Streams
      │
      ▼
Lambda / Consumer
      │
      ▼
OpenSearch
```

👉 Best when you need **custom processing before indexing**.

---

# 🎯 Exam Rules (Very Important)

Choose **Amazon OpenSearch** when the question mentions:

- 🔍 Full-text search
- 🔍 Search by keywords
- 🔍 Partial word matching (e.g., searching `"iph"` finds `"iPhone"`)
- 📊 Log analytics and dashboards
- 📈 CloudWatch Logs visualization
- ⚡ Real-time search
- 🌐 Product or website search

### Remember these integration patterns:

- **CloudWatch Logs → OpenSearch** = Search and visualize application/system logs.
- **Kinesis Data Firehose → OpenSearch** = Managed real-time data ingestion with no custom consumer.
- **Kinesis Data Streams → Lambda → OpenSearch** = Real-time ingestion with custom processing logic before indexing.
