# Lambda Networking (Default VPC, Lambda in VPC & RDS Proxy) – Beginner Friendly (AWS SAA-C03)

This topic is **very common** in the AWS SAA exam because many questions ask:

> **"Why can't my Lambda connect to my RDS?"**
> 

Let's understand it from the beginning.

---

# Part 1 — Lambda by Default

When you create a Lambda,

AWS runs it inside

> **AWS's own managed VPC**
> 

**Not your VPC.**

Think of it like this:

```
AWS Cloud

├── AWS VPC (Managed by AWS)
│      │
│      └── Lambda (Default)
│
└── Your VPC
       ├── RDS
       ├── EC2
       └── ElastiCache
```

Notice

Lambda and your RDS are in **different networks**.

---

# Real-Life Analogy

Imagine

AWS owns an apartment.

You own another apartment.

```
AWS Apartment

↓

Lambda

-------------------

Your Apartment

↓

Database
```

Your Lambda **cannot simply walk into your apartment**.

They are separated.

---

# What Can Default Lambda Access?

Default Lambda can easily access

✅ Public AWS services

Example

```
Lambda

↓

S3

SNS

SQS

DynamoDB

API Gateway
```

These services are accessible without placing Lambda in your VPC.

---

# What Can't It Access?

Suppose your database is

```
Private RDS
```

inside your VPC.

Default Lambda cannot reach it.

Also cannot directly reach:

- Private Amazon RDS
- Private Amazon ElastiCache
- Internal Load Balancer (Internal ALB/NLB)
- Private EC2 instances

---

# Why?

Because your database has

```
Private IP
```

Only devices inside your VPC can access it.

Default Lambda is **outside your VPC**.

---

# Exam Tip

If the question says

> Lambda cannot connect to private RDS
> 

Think immediately

✅ Lambda must be placed inside the VPC.

---

# Part 2 — Lambda Inside a VPC

Now you tell AWS

"I want this Lambda to run inside **my VPC**."

AWS asks for

- VPC ID
- Subnets
- Security Groups

---

## Example

```
Your VPC

├── Private Subnet A
├── Private Subnet B
└── RDS
```

Lambda configuration

```
VPC

↓

Subnet A

Subnet B

↓

Security Group
```

AWS now connects Lambda to your network.

---

# Elastic Network Interface (ENI)

This is an important exam term.

When Lambda joins your VPC,

AWS creates an

> **Elastic Network Interface (ENI)**
> 

Think of an ENI as a **network cable**.

```
Lambda

↓

ENI

↓

Your VPC

↓

RDS
```

Without the ENI,

Lambda has no connection to your VPC.

---

# Real-Life Analogy

Imagine Lambda is a visitor.

ENI is the visitor's entry pass.

Without the pass,

it cannot enter your office.

---

# Lambda in VPC Architecture

```
Internet

↓

API Gateway

↓

Lambda

↓

ENI

↓

Private Subnet

↓

RDS
```

Now Lambda can access the database.

---

# Important Note

Putting Lambda inside your VPC **does not automatically give it internet access**.

If the Lambda needs to call:

- External APIs
- Public websites
- Public AWS endpoints (without VPC endpoints)

your VPC must provide a path (commonly through a **NAT Gateway** for private subnets).

This is a common exam scenario.

---

# Part 3 — Lambda with RDS

Imagine your website suddenly becomes popular.

```
10,000 Users
```

Every request calls Lambda.

Each Lambda opens

one database connection.

```
10,000 Lambdas

↓

10,000 Database Connections
```

Your database cannot handle that many simultaneous connections.

---

# Problem

Database connection exhaustion.

The database spends too much time managing connections instead of serving queries.

---

# Solution

Use

# Amazon RDS Proxy

---

# What is RDS Proxy?

Think of it as a receptionist.

Without receptionist

```
Customer

↓

Manager
```

Everyone directly talks to the manager.

Chaos.

---

With receptionist

```
Customer

↓

Receptionist

↓

Manager
```

Much better.

---

# Lambda + RDS Proxy

Instead of every Lambda talking directly to the database,

they talk to the proxy.

```
Lambda

↓

RDS Proxy

↓

Database
```

The proxy manages a pool of reusable database connections.

---

# Connection Pooling

Suppose

1000 Lambdas

need the database.

Without proxy

```
1000 Lambdas

↓

1000 Connections
```

With RDS Proxy

```
1000 Lambdas

↓

RDS Proxy

↓

50 Shared Connections

↓

Database
```

Huge improvement.

---

# Benefits of RDS Proxy

### 1. Better Scalability ⭐⭐⭐

Reuses database connections instead of opening new ones for every Lambda invocation.

---

### 2. Faster Failover ⭐⭐

Suppose

Primary database crashes.

RDS Proxy preserves client connectivity as much as possible and reconnects more efficiently to the new database instance.

AWS documentation states it can significantly reduce failover time.

---

### 3. Better Security ⭐⭐

Instead of storing

```
Username

Password
```

inside Lambda,

use

```
Secrets Manager
```

and IAM authentication where supported.

Safer.

---

# Can RDS Proxy Be Public?

No.

This is an important exam point.

RDS Proxy is

```
Private Only
```

It lives inside your VPC.

---

Therefore

Lambda **must also be inside the same VPC (or have network connectivity to it)**.

Exam shortcut:

> **Private RDS Proxy → Lambda in VPC**
> 

---

# Complete Architecture

```
User

↓

API Gateway

↓

Lambda (Inside VPC)

↓

ENI

↓

RDS Proxy

↓

Amazon RDS
```

This is the recommended architecture for Lambda accessing relational databases.

---

# Default Lambda vs Lambda in VPC

| Default Lambda | Lambda in VPC |
| --- | --- |
| Runs in an AWS-managed VPC | Runs inside your VPC |
| Cannot directly access private RDS | Can access private RDS |
| Can easily access public AWS services | Can access private resources in your VPC |
| No ENI in your VPC | AWS creates ENIs in your selected subnets |

---

# Direct RDS vs RDS Proxy

| Direct Connection | RDS Proxy |
| --- | --- |
| Every Lambda opens its own DB connection | Connections are pooled and reused |
| Can exhaust database connections | Scales much better |
| Slower failover handling | Faster failover handling |
| Credentials often managed directly by the app | Integrates with IAM and Secrets Manager |

---

# Lambda Networking Flow

### Default

```
Lambda

↓

AWS Managed Network

↓

S3
```

---

### Private Database

```
Lambda

↓

ENI

↓

Private VPC

↓

RDS
```

---

### Best Practice

```
Lambda

↓

ENI

↓

RDS Proxy

↓

RDS
```

---

# AWS SAA-C03 Cheat Sheet

| Topic | Remember |
| --- | --- |
| **Default Lambda** | Runs outside your VPC in an AWS-managed VPC |
| **Private RDS** | Not reachable from a default Lambda |
| **Lambda in VPC** | Requires VPC ID, subnets, and security groups |
| **ENI** | Network interface AWS creates so Lambda can communicate inside your VPC |
| **Internet Access** | A Lambda in a private subnet needs a NAT Gateway (or appropriate VPC endpoints for AWS services) to reach the public internet |
| **RDS Proxy** | Pools database connections for better scalability and reliability |
| **RDS Proxy Accessibility** | Private only; typically used from Lambdas running in the VPC |
| **Secrets Manager** | Securely stores database credentials for use with RDS Proxy |

---

# Easy Memory Trick

Imagine:

- 🏠 **Your VPC** = Your house
- 👤 **Lambda** = A visitor
- 🎫 **ENI** = The visitor's access pass
- 🧑‍💼 **RDS Proxy** = The receptionist
- 🗄️ **RDS** = The manager in the office

Without the pass (ENI), the visitor can't enter your house.

Without the receptionist (RDS Proxy), every visitor crowds directly around the manager.

With the receptionist, visitors are organized and the manager (database) can work much more efficiently.
