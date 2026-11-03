# Serverless – Complete Beginner Notes (AWS SAA-C03)

Many beginners misunderstand **Serverless**.

They think:

> **"Serverless means there are no servers."**
> 

❌ That's **wrong**.

There **are servers**.

You just **don't manage them**.

---

# What is Serverless?

Serverless means:

> **AWS manages the servers for you.**
> 

You only focus on:

- Writing code
- Deploying your application
- Using the service

AWS handles:

- Servers
- Operating System
- Patching
- Scaling
- High Availability
- Capacity Planning

---

# Traditional Way

Imagine opening a restaurant.

You must

```
Buy Building

↓

Install Electricity

↓

Hire Cleaners

↓

Repair Equipment

↓

Cook Food
```

Lots of work.

---

# Serverless Way

Instead,

you rent a fully managed kitchen.

You only cook.

Someone else manages everything else.

That's Serverless.

---

# Another Simple Analogy

Imagine electricity.

Do you own a power plant?

No.

You simply

```
Turn ON Switch

↓

Use Electricity
```

You don't care:

- How electricity is generated
- Which machines produce it
- Which workers maintain it

Serverless is exactly like that.

---

# Traditional Server Hosting

Suppose you create a website.

Without serverless

```
Create EC2

↓

Install Linux

↓

Install Docker

↓

Configure Nginx

↓

Patch OS

↓

Monitor CPU

↓

Scale Servers

↓

Deploy App
```

You manage everything.

---

# Serverless Hosting

```
Write Code

↓

Deploy

↓

Done
```

AWS manages the infrastructure.

---

# Important Exam Definition

Serverless

≠

"No Servers"

Instead

Serverless

=

> **No Server Management**
> 

Servers still exist.

AWS owns them.

AWS manages them.

---

# Why Serverless?

Imagine

Today

```
10 Users
```

Tomorrow

```
100,000 Users
```

Without serverless

You must

- Launch more servers
- Configure Load Balancer
- Configure Auto Scaling

---

With serverless

AWS automatically scales.

You don't touch anything.

---

# What Do Developers Do?

Developers simply

```
Write Code

↓

Upload

↓

Done
```

No server configuration.

---

# Serverless = FaaS

Originally,

Serverless mostly meant

**FaaS**

(Function as a Service)

Example

AWS Lambda

You upload

one function.

AWS runs it whenever needed.

---

# Example

Instead of running

an entire server,

you upload

```python
def send_email():
    ...
```

AWS runs it.

When finished,

it stops.

No running server to manage.

---

# AWS Lambda

The most famous serverless service.

Example

Someone uploads a photo.

```
Photo Uploaded

↓

AWS Lambda

↓

Create Thumbnail
```

No EC2.

No Docker.

No Kubernetes.

---

# Why Lambda is Serverless

You don't choose

- Server size
- CPU
- RAM (beyond function configuration choices)
- Operating System
- Scaling

AWS handles everything.

---

# Serverless Services in AWS

AWS has many serverless services.

Not only Lambda.

---

## AWS Lambda

Runs code.

---

## DynamoDB

Serverless NoSQL database.

You don't create database servers.

AWS handles everything.

---

## Amazon S3

Store files.

No storage server to manage.

Just upload objects.

---

## Amazon SNS

Send notifications.

No messaging server.

AWS manages it.

---

## Amazon SQS

Message queue.

No queue server.

AWS manages it.

---

## Amazon Kinesis Data Firehose

Streaming delivery service.

No infrastructure.

Just configure destinations.

---

## Amazon Aurora Serverless

Database automatically starts,

stops,

and scales.

No database server management.

---

## AWS Cognito

Authentication service.

AWS manages user authentication infrastructure.

---

## AWS API Gateway

Create REST APIs.

No web server management.

AWS hosts the API.

---

## AWS Step Functions

Coordinate workflows.

AWS manages execution.

---

## AWS Fargate

Runs containers.

No EC2 instances.

Still serverless.

---

# Which AWS Services Are NOT Serverless?

Example

Amazon EC2

```
Launch Server

↓

Choose Instance Type

↓

Patch OS

↓

Scale
```

You manage it.

Not serverless.

---

Amazon ECS (EC2 Launch Type)

You manage EC2 instances.

Not serverless.

---

Amazon EKS (Managed Nodes)

Still EC2 underneath.

Not fully serverless.

---

# Traditional vs Serverless

## Traditional

```
Developer

↓

EC2

↓

OS

↓

Application
```

---

## Serverless

```
Developer

↓

Code

↓

AWS

↓

Application Runs
```

---

# Example Comparison

Suppose you need to resize uploaded images.

---

## Traditional

```
Upload

↓

EC2

↓

Python

↓

Resize Image
```

Need to maintain EC2.

---

## Serverless

```
Upload

↓

AWS Lambda

↓

Resize Image
```

No server management.

---

# Benefits

✅ No server management

✅ Automatic scaling

✅ High availability

✅ Pay only when used (many services)

✅ Faster development

---

# Disadvantages

Some services (like Lambda) may have:

- Execution time limits
- Cold starts
- Less control over the underlying infrastructure

---

# Common AWS Serverless Services

| Service | What It Does |
| --- | --- |
| **AWS Lambda** | Runs code without managing servers |
| **Amazon DynamoDB** | Serverless NoSQL database |
| **Amazon S3** | Serverless object storage |
| **Amazon API Gateway** | Hosts REST/HTTP/WebSocket APIs |
| **Amazon SNS** | Serverless publish/subscribe messaging |
| **Amazon SQS** | Serverless message queue |
| **Amazon Kinesis Data Firehose** | Serverless streaming data delivery |
| **Amazon Aurora Serverless** | Auto-scaling relational database |
| **AWS Cognito** | Managed authentication and user identities |
| **AWS Step Functions** | Serverless workflow orchestration |
| **AWS Fargate** | Serverless compute for containers |

---

# Serverless vs Traditional

| Traditional | Serverless |
| --- | --- |
| You manage servers | AWS manages servers |
| Manual scaling | Automatic scaling |
| OS patching | AWS patches infrastructure |
| Capacity planning | AWS handles capacity |
| More operational work | Less operational work |
| Example: EC2 | Example: Lambda |

---

# EC2 vs Lambda

| Amazon EC2 | AWS Lambda |
| --- | --- |
| You manage servers | AWS manages servers |
| Long-running applications | Event-driven functions |
| Choose instance type | Upload code |
| Pay for running instances | Pay for executions and execution time |
| Full OS control | No OS management |

---

# ECS EC2 vs ECS Fargate

| ECS EC2 | ECS Fargate |
| --- | --- |
| Manage EC2 instances | No EC2 management |
| Not serverless | Serverless |
| More control | Less operational overhead |

---

# AWS SAA-C03 Memory Tricks

### 🖥️ EC2

**You manage the server.**

---

### ⚡ Lambda

**You upload code. AWS runs it.**

---

### ☁️ Serverless

**Servers exist—but AWS manages them.**

---

### 📦 Fargate

**Run containers without managing EC2 instances.**

---

### 🗄️ DynamoDB

**Serverless database.**

---

### 📂 S3

**Serverless object storage.**

---

# One-Line Exam Summary

> **Serverless means AWS manages the underlying infrastructure (servers, scaling, patching, and availability), while you focus on your code or data. It does *not* mean there are no servers.**
> 

## Quick Exam Clues

If the question says:

- **"No infrastructure management"** → Think **Serverless**
- **"Only upload code"** → **AWS Lambda**
- **"Run containers without EC2"** → **AWS Fargate**
- **"Fully managed NoSQL database"** → **Amazon DynamoDB**
- **"Automatically scale without provisioning servers"** → **Serverless services**
