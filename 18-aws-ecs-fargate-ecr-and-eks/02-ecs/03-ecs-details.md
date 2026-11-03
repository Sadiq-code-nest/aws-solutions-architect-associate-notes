# Amazon ECS (Elastic Container Service) – Complete Beginner Notes (AWS SAA-C03)

If Docker is **"how to package an application"**, then **Amazon ECS** is **"how to run and manage many Docker containers on AWS."**

Think of it this way:

- **Docker** = Packs and runs one container.
- **Amazon ECS** = Manages thousands of containers automatically.

---

# 1. What is Amazon ECS?

**Amazon ECS (Elastic Container Service)** is AWS's **container orchestration service**.

It helps you:

- Run Docker containers
- Scale them
- Monitor them
- Restart failed containers
- Load balance traffic

without manually managing every container.

---

## Real-Life Example

Imagine Netflix.

Thousands of users are watching movies.

Netflix may have hundreds of containers running.

Instead of manually starting every container,

AWS ECS does it automatically.

```
Users
   │
   ▼
Application
   │
   ▼
Amazon ECS
   │
 ┌─┼────────────┐
 ▼ ▼            ▼
Container 1   Container 2   Container 3
```

---

# 2. Important ECS Components

Before learning ECS, understand these terms.

---

## ECS Cluster

A Cluster is simply a **group of machines** where containers run.

Think of it as an apartment building.

```
Apartment Building (Cluster)

Apartment 1

Apartment 2

Apartment 3
```

Containers live inside the apartments.

---

## ECS Task

A Task is **one running copy** of your application.

Example

Docker Image

↓

Run

↓

Task

If you start 5 copies,

```
Task 1

Task 2

Task 3

Task 4

Task 5
```

---

## Task Definition

Think of it as a **recipe**.

It tells ECS:

- Which Docker image?
- CPU?
- RAM?
- Environment Variables?
- Ports?
- IAM Role?
- Volumes?

Example

```
Image : nginx

CPU : 1 vCPU

RAM : 512 MB

Port : 80
```

ECS follows this recipe whenever it starts a task.

# ECS Service vs ECS Task

This is one of the most important concepts for this question.

## ECS Task

A **task** is an actual running instance of your container workload.

Example:

```
ECS Task
 └── Container
      └── Batch processing
```

It starts → does work → finishes.

---

## ECS Service

An ECS **service** manages a desired number of running tasks.

For example:

```
ECS Service
Desired count = 3

       ↓

Task 1
Task 2
Task 3
```

The service tries to maintain that number.

---

# 3. ECS Launch Types

This is one of the most common exam topics.

There are two launch types.

```
Amazon ECS

├── EC2 Launch Type
└── Fargate Launch Type
```

---

# ECS EC2 Launch Type

With EC2 Launch Type,

**you manage the EC2 servers**.

AWS only manages the containers.

Architecture

```
EC2 Instance

↓

Docker

↓

ECS Agent

↓

Your Containers
```

---

## What You Manage

You create:

- EC2 instances
- Security Groups
- Auto Scaling
- AMIs
- Patching
- Instance sizing

AWS manages:

- Container scheduling
- Container restart
- ECS control plane

---

### Example

Suppose you launch

```
3 EC2 Instances
```

Each instance runs

```
Docker

ECS Agent
```

Then ECS places containers on them.

```
EC2 #1

Container A

Container B

--------------

EC2 #2

Container C

--------------

EC2 #3

Container D
```

---

# ECS Agent

Very important for the exam.

Every EC2 instance **must run the ECS Agent**.

The ECS Agent:

- Registers the EC2 instance with the ECS Cluster.
- Receives instructions from ECS.
- Starts and stops containers.
- Reports container health.

Without the ECS Agent,

ECS cannot control the instance.

---

# Fargate Launch Type

Fargate is much simpler.

There are **no EC2 instances to manage**.

AWS manages everything.

You only provide:

- Docker image
- CPU
- RAM

AWS runs the container.

Architecture

```
Docker Image

↓

Amazon ECS

↓

AWS Fargate

↓

Running Container
```

You never log into a server.

---

## Why Fargate is Called Serverless

Because:

- No EC2
- No patching
- No scaling servers
- No operating system management

You simply say:

```
Run 5 containers

CPU = 2 vCPU

RAM = 4 GB
```

AWS handles the rest.

---

# EC2 vs Fargate

Imagine opening a restaurant.

## EC2

You buy the building.

```
Buy building

Hire staff

Repair building

Clean building
```

You own the infrastructure.

---

## Fargate

You rent a fully managed kitchen.

```
Cook food

Serve customers
```

Someone else manages the kitchen.

That's Fargate.

---

# Scaling

## EC2 Launch Type

Need more containers?

Maybe your EC2 instances are full.

You must launch more EC2 instances.

```
EC2

↓

More EC2

↓

More Containers
```

---

## Fargate

Need more containers?

Just increase the task count.

```
Task 2

↓

Task 10

↓

Task 100
```

AWS automatically provisions the required compute.

---

# ECS IAM Roles

There are **two different IAM roles**, and exam questions often test the difference.

---

## 1. EC2 Instance Profile (EC2 Launch Type only)

This IAM role is attached to the **EC2 instance**.

It is used by the **ECS Agent**, not your application.

Typical permissions include:

- Pull Docker images from Amazon ECR.
- Send logs to CloudWatch Logs.
- Read secrets from Secrets Manager or Parameter Store.
- Communicate with the ECS service.

Think:

> **Instance Profile = Permissions for the EC2 machine and ECS Agent.**
> 

---

## 2. ECS Task Role

Each container can have its own IAM role.

Example

Container A

Needs

```
Amazon S3
```

Container B

Needs

```
DynamoDB
```

Instead of giving both containers all permissions,

assign different Task Roles.

```
Task A

↓

S3 Access

-------------

Task B

↓

DynamoDB Access
```

This follows the **principle of least privilege**.

Think:

> **Task Role = Permissions for the application inside the container.**
> 

---

# ECS + Load Balancer

Users access your application through a load balancer.

```
Users

↓

Load Balancer

↓

ECS Tasks
```

---

## Application Load Balancer (ALB)

Recommended for most applications.

Supports:

- HTTP
- HTTPS
- Path-based routing
- Host-based routing

Example

```
/api

↓

Container A

---------

/images

↓

Container B
```

ALB is the default choice for web applications.

---

## Network Load Balancer (NLB)

Use when you need:

- Very high performance
- Very high throughput
- TCP/UDP traffic
- Integration with AWS PrivateLink

---

## Classic Load Balancer

Still supported,

but AWS generally recommends using ALB or NLB instead.

Classic Load Balancer is **not supported with Fargate**.

---

# ECS + Amazon EFS

Containers are usually **ephemeral**.

If a container is deleted,

its local files disappear.

Example

```
Container

↓

photo.jpg
```

Container stops.

```
photo.jpg

Lost
```

---

## Solution: Amazon EFS

Mount a shared EFS file system.

```
Container A

      │

Container B

      │

Container C

      ▼

Amazon EFS
```

All containers share the same files.

---

## Multi-AZ

Even if containers run in different Availability Zones,

they can all access the same EFS file system.

```
AZ-A

Container

      │

AZ-B

Container

      │

AZ-C

Container

      ▼

Amazon EFS
```

Perfect for shared uploads or persistent application data.

---

# Why Not Amazon S3?

This is a common exam point.

Amazon S3 is **object storage**, not a file system.

You **cannot mount S3** as a native file system in ECS.

If your application expects files and directories, use **Amazon EFS**.

---

# Typical ECS Architecture

```
Developer

↓

Build Docker Image

↓

Amazon ECR

↓

Amazon ECS

↓

AWS Fargate

↓

Application Load Balancer

↓

Users
```

---

# EC2 vs Fargate (Exam Comparison)

| Feature | ECS EC2 | ECS Fargate |
| --- | --- | --- |
| Manage EC2 instances | ✅ Yes | ❌ No |
| Manage OS patches | ✅ Yes | ❌ No |
| Docker installed by you | ✅ Yes | ❌ No |
| ECS Agent required | ✅ Yes | ❌ No (AWS manages it) |
| Serverless | ❌ No | ✅ Yes |
| Pay for | EC2 instances | CPU and RAM used by tasks |
| Best for | More control and customization | Simplicity and less operational overhead |

---

# ECS IAM Roles

| Role | Used By | Purpose |
| --- | --- | --- |
| **EC2 Instance Profile** | EC2 instance / ECS Agent | Pull images from ECR, send logs to CloudWatch, communicate with ECS, retrieve secrets |
| **ECS Task Role** | Containerized application | Access AWS services like S3, DynamoDB, SQS, etc. |

---

# ECS Load Balancer Comparison

| Load Balancer | Use Case |
| --- | --- |
| **Application Load Balancer (ALB)** | Best for HTTP/HTTPS web applications (most common choice) |
| **Network Load Balancer (NLB)** | High-performance TCP/UDP workloads and PrivateLink |
| **Classic Load Balancer (CLB)** | Legacy only; not recommended for new deployments |

---

# EFS with ECS

| Storage | Can ECS Mount It? | Best For |
| --- | --- | --- |
| **Amazon EFS** | ✅ Yes | Shared, persistent file storage across tasks and AZs |
| **Amazon S3** | ❌ No (not as a file system) | Object storage accessed via APIs |

---

# AWS SAA-C03 Exam Memory Tricks

### 🚛 ECS

**Container Manager**

Runs and manages Docker containers.

---

### 🖥️ EC2 Launch Type

**You manage the servers.**

AWS manages the containers.

---

### ☁️ Fargate

**Serverless containers.**

No EC2 instances to manage.

---

### 👮 EC2 Instance Profile

Permissions for the **EC2 instance and ECS Agent**.

---

### 👤 ECS Task Role

Permissions for the **application inside the container**.

---

### ⚖️ ALB

Default choice for **HTTP/HTTPS applications**.

---

### 📂 EFS

Shared, persistent file storage that multiple ECS tasks can mount—even across Availability Zones.

## Quick Exam Decision Guide

- **Need full control over EC2 instances?** → **ECS EC2 Launch Type**
- **Don't want to manage servers?** → **ECS Fargate**
- **Need shared persistent storage for containers?** → **Amazon EFS**
- **Need to pull Docker images privately in AWS?** → **Amazon ECR**
- **Need HTTP/HTTPS routing to containers?** → **Application Load Balancer**
- **Need application-specific AWS permissions?** → **ECS Task Role**
- **Need ECS Agent permissions on EC2?** → **EC2 Instance Profile**
