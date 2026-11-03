# Lambda5 (RDS & Aurora)

These two topics are often confused in the exam because **both involve RDS and Lambda**, but they serve **completely different purposes**.

---

# 1. Invoking Lambda from RDS & Aurora

## First Understand the Idea

Normally, we think:

```
Application

↓

Lambda

↓

RDS
```

The application calls Lambda, and Lambda accesses the database.

---

But AWS also allows the opposite.

The **database itself** can call a Lambda function.

```
RDS

↓

Lambda
```

Think of it as:

> **Database → Lambda**
> 

instead of

> **Lambda → Database**
> 

---

# Why Would the Database Call Lambda?

Imagine your database detects something important.

Example:

A new order is inserted.

Instead of waiting for your application,

the database immediately calls Lambda.

Lambda can then

- Send an email
- Notify another system
- Call an API
- Process the new data
- Update another database

---

## Real Example

Customer places an order.

```
Customer

↓

Application

↓

RDS
```

New row inserted:

```
Order #500
```

Database immediately calls Lambda.

```
RDS

↓

Lambda

↓

Send Confirmation Email
```

Very useful.

---

# Another Example

Hospital Database

Patient result added.

```
New Blood Report

↓

RDS

↓

Lambda

↓

Notify Doctor
```

The application doesn't need to check continuously.

---

# How Does It Work?

```
Insert Data

↓

RDS

↓

Invoke Lambda

↓

Lambda Processes Data

↓

Done
```

---

# Which Databases Support It?

Not every RDS engine.

AWS SAA usually expects you to remember:

✅ RDS PostgreSQL

✅ Aurora MySQL

---

# Networking Requirement

Suppose

Database wants to call Lambda.

```
RDS

↓

Lambda
```

It must be able to reach Lambda.

Possible networking options include:

- Public internet (for applicable configurations)
- NAT Gateway
- VPC Endpoints

Think:

The database needs **network connectivity** to the Lambda service.

---

# Permissions

Database cannot invoke Lambda automatically.

AWS requires permissions.

Two important pieces:

### IAM Policy

Allows the database to invoke Lambda.

---

### Lambda Resource Policy

Allows Lambda to accept requests from the database.

Think

```
RDS

↓

Permission

↓

Lambda
```

No permission → no invocation.

---

# Exam Tip

If question says

> Database automatically triggers business logic
> 

Think

✅ RDS invokes Lambda

---

# Memory Trick

Normally

```
Lambda

↓

Database
```

Here

```
Database

↓

Lambda
```

Simply reversed.

---

# 2. RDS Event Notifications

This is completely different.

---

## What Are Event Notifications?

These are notifications about

the **database service itself**,

NOT

the data stored inside it.

---

Think

Database administration.

---

Examples

```
Database Started

Database Stopped

Database Created

Backup Finished

Snapshot Created

Failover Occurred

Proxy Created
```

These are infrastructure events.

---

## NOT Data Changes

Suppose

Customer places an order.

```
INSERT INTO Orders
```

RDS Event Notifications

❌ Do NOT tell you this.

---

Suppose

Someone deletes a row.

```
DELETE FROM Users
```

Still

❌ No notification.

---

RDS Event Notifications care about

the database itself.

---

# Easy Analogy

Imagine your car.

---

Car Event

```
Engine Started
```

AWS tells you.

---

Car Event

```
Fuel Empty
```

AWS tells you.

---

But

Passenger changes seat.

AWS doesn't care.

---

Same for databases.

---

# Architecture

```
RDS

↓

Database Started

↓

SNS

↓

Email
```

Simple.

---

Or

```
RDS

↓

EventBridge

↓

Lambda
```

---

# What Can Be Monitored?

Examples

- DB Instance
- DB Snapshot
- DB Parameter Group
- DB Security Group
- RDS Proxy
- Custom Engine Version

---

# Delivery Time

Near real time.

Usually

Up to

```
5 Minutes
```

---

# SNS Integration

Suppose database stops.

```
RDS

↓

SNS

↓

Email

↓

Administrator
```

---

# EventBridge Integration

Instead of email,

trigger automation.

```
RDS

↓

EventBridge

↓

Lambda

↓

Restart Service
```

---

# Difference Between These Two Topics

This is what AWS loves asking.

| Invoking Lambda from RDS | RDS Event Notifications |
| --- | --- |
| Database calls Lambda | RDS sends notifications about DB events |
| Triggered by database logic or functions | Triggered by infrastructure/service events |
| Can process actual application data | Does **not** include row-level data changes |
| Used for business logic | Used for monitoring and administration |

---

# Easy Story

Imagine a bank.

---

## RDS Invokes Lambda

Customer deposits

```
$1000
```

Database immediately calls Lambda.

Lambda sends

```
SMS

"Deposit Successful"
```

Business logic.

---

## RDS Event Notification

Database crashes.

AWS sends

```
Database Failed

↓

SNS

↓

Administrator
```

Infrastructure notification.

---

# Visual Summary

## RDS → Lambda

```
New Data

↓

RDS

↓

Lambda

↓

Business Logic
```

---

## RDS Event Notification

```
Database Restarted

↓

SNS

↓

Admin Email
```

---

# AWS SAA Cheat Sheet

| Topic | Remember |
| --- | --- |
| **RDS → Lambda** | Database invokes Lambda to process application data or business logic |
| **Supported Engines** | RDS for PostgreSQL and Aurora MySQL (as noted in your course) |
| **Requirements** | Network connectivity + IAM permissions + Lambda resource-based policy |
| **RDS Event Notifications** | Database service events only |
| **Contains Data Changes?** | ❌ No |
| **Examples** | Database started, stopped, snapshot created, failover, proxy events |
| **Destinations** | SNS or EventBridge |

---

# 30-Second Exam Memory Trick

When you see **RDS** in a question, ask yourself:

### 📊 Is AWS talking about the **data** inside the database?

Examples:

- New order inserted
- New user created
- New patient record

➡️ Think **RDS invokes Lambda** (business logic on the data).

---

### ⚙️ Or is AWS talking about the **database service itself**?

Examples:

- Database created
- Database stopped
- Snapshot completed
- Failover occurred

➡️ Think **RDS Event Notifications** (monitoring/administration).

This simple distinction answers most SAA questions involving these two features.
