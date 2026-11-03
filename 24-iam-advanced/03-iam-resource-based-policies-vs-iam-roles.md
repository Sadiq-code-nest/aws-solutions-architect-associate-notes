# IAM Roles vs Resource-Based Policies

This is another **very common SAA-C03 topic**, especially for **cross-account access**.

---

# First Understand the Problem

Suppose you have **two AWS accounts**:

```
Account A (Developer)

Account B (Storage)
```

The developer in Account A needs to upload a file into an S3 bucket in Account B.

How can this be done?

AWS provides **two methods**:

1. IAM Role
2. Resource-Based Policy

---

# Method 1 — IAM Role

Think of it like **borrowing another person's ID card**.

You temporarily become someone else.

Architecture:

```
User (Account A)

      │

Assume IAM Role

      │

      ▼

Temporary Role

(Account B)

      │

      ▼

S3 Bucket
```

When you assume the role:

Your original permissions disappear temporarily.

You now use the role's permissions.

---

## Real-Life Example ⭐⭐⭐⭐⭐

Suppose you're a contractor.

You arrive at a company.

Your own company ID card cannot open the office.

The company gives you a temporary visitor badge.

While using that badge:

- You access only permitted rooms.
- Your personal badge is not used.

That's exactly how **IAM Role** works.

---

# Method 2 — Resource-Based Policy

Instead of changing who you are...

The resource says:

> "I trust this user."
> 

Example:

```
S3 Bucket Policy

↓

Allow

User from Account A
```

Architecture:

```
User (Account A)

↓

S3 Bucket Policy

↓

S3 Bucket

(Account B)
```

The user keeps their own permissions.

The bucket simply allows them.

---

# Real-Life Example ⭐⭐⭐⭐⭐

Imagine your neighbour says:

> "You may enter my garage."
> 

You don't become your neighbour.

You remain yourself.

The garage simply allows you.

That's a **resource-based policy**.

---

# Key Difference

## IAM Role

```
You become

↓

The Role
```

Original permissions are replaced temporarily.

---

## Resource-Based Policy

```
You remain yourself

↓

Resource trusts you
```

No permission switch happens.

---

# Example From the Exam

User in Account A must:

1. Read DynamoDB table in Account A.
2. Upload data to S3 bucket in Account B.

If Account B uses an **S3 Bucket Policy**:

The user can:

- Read DynamoDB (their own IAM permissions)
- Write to S3 (bucket trusts them)

They **don't need to give up** their DynamoDB permissions.

If they instead assume a role in Account B:

They temporarily use the role's permissions.

If the role doesn't allow DynamoDB access,

They lose it during that session.

---

# Which AWS Services Support Resource-Based Policies?

Common exam services:

- Amazon S3
- Amazon SNS
- Amazon SQS
- AWS Lambda
- API Gateway
- EventBridge (some targets)

---

# EventBridge Security

When an EventBridge rule triggers another service, it must have permission.

There are **two possibilities**:

### 1. Target Supports Resource-Based Policies

Examples:

- Lambda
- SNS
- SQS
- API Gateway
- S3

The target resource simply trusts EventBridge.

```
EventBridge Rule

↓

Lambda Resource Policy

↓

Lambda Executes
```

---

### 2. Target Does NOT Support Resource Policies

Examples:

- Kinesis Data Streams
- EC2 Auto Scaling
- ECS Tasks
- Systems Manager Run Command

EventBridge assumes an IAM Role.

```
EventBridge Rule

↓

IAM Role

↓

Kinesis Stream
```

---

# Easy Rule to Remember ⭐⭐⭐⭐⭐

```
If target supports Resource Policy

↓

Use Resource-Based Policy

----------------------------

If target does NOT support Resource Policy

↓

Use IAM Role
```

---

# Quick Comparison Table

| IAM Role | Resource-Based Policy |
| --- | --- |
| Identity assumes a role | Resource trusts the identity |
| Original permissions are replaced temporarily | Original permissions stay active |
| Uses STS temporary credentials | No role assumption required |
| Good for cross-account access to many services | Good for S3, SNS, SQS, Lambda, API Gateway |
| Example: EventBridge → Kinesis | Example: EventBridge → Lambda |
