# Amazon S3 Access Points & S3 Object Lambda (SAA-C03) — Complete Crystal Clear Notes

These are **high-scoring AWS SAA-C03 topics**. They seem confusing initially, but once you understand the problem they solve, they become easy.

---

# Part 1 — Why Do We Need S3 Access Points?

## First understand the problem.

Suppose your company has **one S3 bucket**.

```
company-data
```

Inside the bucket:

```
company-data

│

├── finance/

├── sales/

├── analytics/

├── hr/

└── engineering/
```

Different teams need different access.

| Team | Needs |
| --- | --- |
| Finance | Read & Write only finance folder |
| Sales | Read & Write only sales folder |
| Analytics | Read everything |
| HR | Only HR folder |

---

## Without Access Points

Everything is controlled using **one Bucket Policy**.

That single policy becomes huge.

```
Bucket Policy

↓

Finance Rules

↓

Sales Rules

↓

Analytics Rules

↓

HR Rules

↓

Dev Rules

↓

Partner Rules

↓

Vendor Rules

↓

Application Rules

↓

Temporary Rules
```

Eventually:

```
100+
policy statements
```

Very difficult to manage.

---

## Real-Life Analogy

Imagine one office building.

Departments:

```
Finance

Sales

HR

Marketing
```

Without Access Points,

there is **one security guard**.

He memorizes permissions for everyone.

```
John

↓

Finance

↓

Allow

------------

Mike

↓

Sales

↓

Allow

------------

Anna

↓

HR

↓

Allow

------------

Tom

↓

Marketing

↓

Allow
```

Eventually the guard gets confused.

---

AWS solution:

Instead of one guard,

give every department its own security desk.

```
Finance Door

↓

Finance Guard

------------

Sales Door

↓

Sales Guard

------------

HR Door

↓

HR Guard
```

This is exactly what **S3 Access Points** do.

---

# What is an S3 Access Point?

An **Access Point** is simply

> **A separate entry point to the same S3 bucket with its own permissions (policy).**
> 

The bucket remains the same.

Only the way users access it changes.

---

## Architecture

```
                S3 Bucket

             company-data

        ┌────────┼─────────┐

        │        │         │

 Finance AP  Sales AP  Analytics AP

        │        │         │

 Finance   Sales    Analytics Users
```

One bucket.

Many access points.

---

# How It Works

Suppose

Bucket

```
company-data
```

contains

```
finance/

sales/

analytics/
```

Create

### Finance Access Point

Policy:

```
Allow

Read

Write

Only

finance/*
```

---

### Sales Access Point

Policy

```
Allow

Read

Write

Only

sales/*
```

---

### Analytics Access Point

Policy

```
Allow

Read

Entire Bucket
```

Now

Finance users cannot even see Sales.

---

# Diagram

```
Finance Users

      │

Finance Access Point

      │

Policy

finance/*

      │

      ▼

 company-data Bucket

      │

 finance/

 sales/

 analytics/
```

---

Sales

```
Sales Users

      │

Sales Access Point

      │

Policy

sales/*

      ▼

 company-data Bucket
```

---

# Important Exam Point

Each Access Point has

### 1. Its own DNS Name

Instead of

```
bucket.s3.amazonaws.com
```

AWS gives

```
finance-access-point-123.s3-accesspoint.amazonaws.com
```

Every Access Point has its own endpoint.

---

### 2. Its own Policy

Exactly like Bucket Policy.

Example

```
Allow

GetObject

finance/*
```

No effect on Sales.

---

# Does Access Point Replace Bucket Policy?

No.

Both exist.

```
Request

↓

Bucket Policy

↓

Access Point Policy

↓

IAM Policy

↓

Access Granted?
```

AWS evaluates everything together.

If any policy explicitly denies access,

the request is denied.

---

# Access Point Example

Bucket

```
company-data
```

Contains

```
finance/report.pdf

sales/report.pdf
```

Finance Access Point

```
Allow

finance/*
```

Finance user requests

```
finance/report.pdf
```

✅ Allowed

Finance user requests

```
sales/report.pdf
```

❌ Denied

---

# Exam Keywords

Need easier permission management?

Need separate permissions for many teams?

Need different applications accessing same bucket?

Answer

✅ S3 Access Points

---

# Part 2 — VPC Origin Access Point

Now let's make it more secure.

Normally

Users access through Internet.

```
User

↓

Internet

↓

Access Point

↓

S3
```

Sometimes company says

> Nobody from Internet should access.
> 

Only EC2 inside VPC.

---

Solution

## VPC Origin Access Point

Now

```
Internet

↓

Blocked

-----------

EC2 inside VPC

↓

Allowed
```

---

Diagram

```
          VPC

   EC2 Instance

        │

VPC Endpoint

        │

Access Point

(VPC Origin)

        │

S3 Bucket
```

---

# Why Need VPC Endpoint?

Remember

S3 is outside your VPC.

Your EC2 cannot directly use

Private Access Point.

Need

```
VPC Endpoint
```

Think of it as

Private tunnel.

---

Real-life

Without endpoint

```
Office

↓

Public Road

↓

Warehouse
```

With endpoint

```
Office

↓

Private Tunnel

↓

Warehouse
```

Much safer.

---

# Types of Endpoint

Can use

- Gateway Endpoint ✅ (Most common for S3)
- Interface Endpoint

Exam usually expects

Gateway Endpoint.

---

# Required Policies

Three things must allow access.

```
IAM Policy

+

Access Point Policy

+

VPC Endpoint Policy

↓

Access Granted
```

Miss one?

↓

Denied.

---

# Architecture

```
EC2

↓

Gateway Endpoint

↓

Endpoint Policy

↓

Access Point

↓

Access Point Policy

↓

Bucket

↓

Object
```

---

# Exam Scenario

Requirement

Only EC2 inside VPC can access S3.

Internet blocked.

Answer

✅ Access Point

- 

✅ VPC Origin

- 

✅ Gateway Endpoint

---

# Part 3 — S3 Object Lambda

This is one of the coolest S3 features.

---

## Problem

Suppose

Bucket contains

```
customer.json
```

```
{

Name

Email

Phone

Salary

Credit Card

}
```

Different applications need different data.

---

Marketing App

Needs

```
Name

Email
```

Analytics

Needs

Everything except

Credit Card.

---

Mobile App

Needs

Compressed version.

---

Without Object Lambda

Need

```
Original File

↓

Create Copy 1

↓

Create Copy 2

↓

Create Copy 3

↓

Store all copies
```

Waste of storage.

---

AWS Solution

Keep ONE object.

Modify it

ONLY

when someone downloads it.

---

# How It Works

```
User

↓

Object Lambda Access Point

↓

Lambda Function

↓

Original Object

↓

Modified Object

↓

User
```

Original object

Never changes.

---

# Diagram

```
Client

      │

Request Object

      │

      ▼

Object Lambda

Access Point

      │

Lambda Function

      │

Modify Object

      │

Original S3 Object

      │

Return Modified Version
```

---

# Important Concept

Lambda changes the object

**during download**

NOT

Inside the bucket.

Bucket still stores

```
Original File
```

---

# Example 1

Bucket

```
customer.json
```

```
{

Name

Salary

Credit Card

}
```

Analytics requests.

Lambda removes

```
Credit Card
```

Returns

```
{

Name

Salary

}
```

Bucket still contains

Full object.

---

# Example 2

Image Resize

Bucket stores

```
5000 × 5000 image
```

Mobile requests.

Lambda

↓

Resizes

↓

500 × 500

↓

Returns smaller image.

Original unchanged.

---

# Example 3

XML to JSON

Original

```
XML
```

Lambda

↓

Convert

↓

JSON

↓

Return

Original still XML.

---

# Example 4

Watermark

Bucket stores

```
photo.jpg
```

Marketing downloads.

Lambda adds

```
CONFIDENTIAL
```

Returns

Watermarked image.

Original untouched.

---

# Example 5

Customer Loyalty

Suppose

Bucket contains

```
invoice.pdf
```

Database

Contains

```
Customer Points
```

Lambda

Reads invoice

- 

Reads database

↓

Adds

```
Reward Points

1500
```

↓

Returns enriched document.

---

# Why Is It Called Object Lambda?

Because

```
Object

+

Lambda

=

Modify Object Dynamically
```

---

# Architecture

```
             Client

               │

        Request Object

               │

               ▼

     Object Lambda Access Point

               │

        Lambda Function

         ┌───────────────┐

         │               │

         ▼               ▼

    S3 Bucket      DynamoDB/RDS

         │               │

         └───────┬───────┘

                 ▼

         Modified Object

                 ▼

              Client
```

---

# Access Point vs Object Lambda

| Access Point | Object Lambda |
| --- | --- |
| Controls access | Modifies returned object |
| Own policy | Uses Lambda function |
| No data modification | Data transformed before download |
| Security feature | Data transformation feature |

---

# Example Comparison

Without Object Lambda

```
Bucket

↓

customer.json

↓

Everyone receives

Same file
```

---

With Object Lambda

```
Customer A

↓

Lambda

↓

Hide Salary

------------

Customer B

↓

Lambda

↓

Translate Language

------------

Customer C

↓

Lambda

↓

Resize Image
```

Everyone receives a different view of the same object.

---

# Decision Tree

```
Need easier permissions?

↓

YES

↓

S3 Access Point

-----------------------------

Need only VPC access?

↓

YES

↓

VPC Origin Access Point

+

Gateway Endpoint

-----------------------------

Need to modify object before download?

↓

YES

↓

S3 Object Lambda
```

---

# SAA-C03 Cheat Sheet

| Requirement | AWS Feature |
| --- | --- |
| Separate permissions for Finance, Sales, HR on the same bucket | ✅ S3 Access Points |
| Each department needs its own DNS endpoint and policy | ✅ S3 Access Points |
| Restrict bucket access to only resources inside a VPC | ✅ VPC Origin Access Point + Gateway VPC Endpoint |
| Transform an object before returning it to the caller | ✅ S3 Object Lambda |
| Remove sensitive fields before download | ✅ S3 Object Lambda |
| Convert XML to JSON during retrieval | ✅ S3 Object Lambda |
| Resize or watermark images dynamically | ✅ S3 Object Lambda |

---

# 🎯 Ultimate Memory Trick

Think of a **shopping mall**:

- **S3 Bucket** = The mall.
- **S3 Access Points** = Different entrances (Finance entrance, Sales entrance, Analytics entrance). Each entrance has its own security rules.
- **VPC Origin Access Point** = A staff-only entrance that can only be reached through a private employee tunnel (the VPC Endpoint).
- **S3 Object Lambda** = A customization desk at the exit. Before you leave, it can wrap your gift, remove sensitive information, resize an image, or translate a document—without changing the original item stored in the mall.
