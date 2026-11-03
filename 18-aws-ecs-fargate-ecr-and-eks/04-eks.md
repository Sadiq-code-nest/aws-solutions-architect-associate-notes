# Amazon EKS (Elastic Kubernetes Service) – Complete Beginner Notes (AWS SAA-C03)

Before learning **Amazon EKS**, you need one important idea.

> **Docker runs containers.**
> 
> 
> **Kubernetes manages lots of containers.**
> 
> **Amazon EKS is AWS's managed Kubernetes service.**
> 

---

# 1. Why Do We Need Kubernetes?

Imagine you have only **one Docker container**.

```
User
   │
   ▼
Docker Container
```

Easy.

---

Now imagine you're Netflix.

Instead of one container, you have

```
5000 Containers
```

Questions arise:

- Which server should each container run on?
- What if one container crashes?
- How do you update without downtime?
- How do you scale automatically?
- How do containers communicate?

Doing this manually would be nearly impossible.

That's why Kubernetes exists.

---

# 2. What is Kubernetes?

Kubernetes (often called **K8s**) is an **open-source container orchestration platform**.

It automatically:

- Deploys containers
- Scales containers
- Restarts failed containers
- Performs rolling updates
- Balances traffic
- Schedules containers on available machines

Think of it as a **manager for thousands of Docker containers**.

---

# 3. Then What is Amazon EKS?

Amazon EKS

=

**Amazon Elastic Kubernetes Service**

AWS manages the Kubernetes control plane for you.

You don't install or maintain Kubernetes yourself.

AWS handles:

- Kubernetes control plane
- API servers
- High availability
- Updates of the control plane

You focus on your applications.

---

# Real-Life Analogy

Imagine a hotel.

The hotel manager assigns guests to rooms.

If one room becomes unavailable,

the manager automatically moves guests elsewhere.

Kubernetes does the same for containers.

Amazon EKS is AWS providing that hotel manager as a managed service.

---

# 4. ECS vs EKS

Both run Docker containers.

But they are different.

## ECS

AWS's own container platform.

Uses AWS APIs.

Best for AWS-native applications.

---

## EKS

Managed Kubernetes.

Uses Kubernetes APIs.

Works similarly across AWS, Azure, GCP, and on-premises.

---

# Visual

```
Docker Containers

↓

Choose One

↓

Amazon ECS

or

Amazon EKS
```

Both achieve the same goal:

**Run containers.**

But they use different orchestration systems.

---

# 5. Why Would a Company Choose EKS?

Imagine a company already uses Kubernetes on-premises.

```
Office Data Center

↓

Kubernetes
```

Now they move to AWS.

Instead of learning ECS,

they keep using Kubernetes.

```
Office Kubernetes

↓

Amazon EKS
```

Very little changes for the development team.

---

# 6. Cloud Agnostic

This is a common exam point.

Kubernetes is **cloud agnostic**.

Meaning:

The same Kubernetes concepts work on

- AWS
- Azure
- Google Cloud
- On-premises

Example

```
Application

↓

Kubernetes

↓

AWS

or

Azure

or

Google Cloud
```

The Kubernetes API stays largely the same.

---

# 7. EKS Architecture

```
Amazon EKS

↓

Worker Nodes

↓

Docker Containers
```

AWS manages Kubernetes,

while your containers run on worker nodes (or Fargate).

---

# 8. Worker Nodes

Worker Nodes are simply

> Machines where containers actually run.
> 

Think of them as apartment buildings.

Containers are the tenants.

```
Worker Node

↓

Container A

Container B

Container C
```

---

# 9. Three Types of Worker Nodes

Amazon EKS supports three ways to run workloads.

```
Amazon EKS

├── Managed Node Groups
├── Self-Managed Nodes
└── AWS Fargate
```

---

# A. Managed Node Groups ⭐⭐⭐

This is the easiest EC2-based option.

AWS creates and manages the EC2 instances.

You don't manually configure much.

Example

```
Need 5 Nodes

↓

AWS Creates

5 EC2 Instances
```

They automatically join the EKS cluster.

They are backed by an **Auto Scaling Group (ASG)**.

---

### Benefits

- Easier management
- Automatic updates (for the managed node group features)
- Automatic scaling with ASG
- Supports:
    - On-Demand Instances
    - Spot Instances

---

# B. Self-Managed Nodes

You create the EC2 instances yourself.

You:

- Launch EC2
- Install software
- Register them with EKS
- Manage updates
- Manage configuration

Example

```
You

↓

Launch EC2

↓

Install Kubernetes Components

↓

Join Cluster
```

More work,

but more control.

AWS provides an **EKS Optimized AMI** to simplify setup.

---

# C. AWS Fargate ⭐⭐⭐

No EC2 instances.

No worker nodes.

Just containers.

```
Docker Container

↓

AWS Fargate

↓

Running
```

Completely serverless.

AWS manages the underlying infrastructure.

---

# Managed Nodes vs Self-Managed vs Fargate

| Feature | Managed Nodes | Self-Managed | Fargate |
| --- | --- | --- | --- |
| EC2 created by | AWS | You | None |
| Manage OS | Mostly AWS-managed node lifecycle | You | AWS |
| Serverless | ❌ | ❌ | ✅ |
| Simplest | ✅ | ❌ | ✅ (for supported workloads) |

---

# 10. Storage in EKS

Containers are temporary.

If a container stops,

its local files disappear.

Need persistent storage?

Use AWS storage services.

---

# StorageClass

Kubernetes uses a **StorageClass** to describe how persistent storage should be provisioned.

Think of it as saying:

> "When a container needs storage, use this type."
> 

Example

```
Container

↓

StorageClass

↓

Amazon EBS
```

---

# Container Storage Interface (CSI)

CSI stands for

**Container Storage Interface**

It is a standard way for Kubernetes to communicate with storage systems.

Think of CSI as an adapter.

```
Kubernetes

↓

CSI Driver

↓

Amazon EBS
```

The same idea works for EFS and FSx.

---

# Storage Options

## Amazon EBS

Best for

One EC2 instance.

```
EC2

↓

Amazon EBS
```

High-performance block storage.

Not shared across multiple nodes at the same time.

---

## Amazon EFS ⭐⭐⭐

Shared file system.

```
Node A

↓

Node B

↓

Node C

↓

Amazon EFS
```

All nodes can access the same files.

Works with **EKS on Fargate**.

---

## Amazon FSx for Lustre

Designed for

- High Performance Computing (HPC)
- Machine Learning
- Scientific workloads

Very high throughput.

---

## Amazon FSx for NetApp ONTAP

Enterprise shared storage.

Supports advanced file features such as snapshots, cloning, and multi-protocol access.

Used when migrating enterprise storage workloads.

---

# ECS vs EKS

| Feature | Amazon ECS | Amazon EKS |
| --- | --- | --- |
| Full Name | Elastic Container Service | Elastic Kubernetes Service |
| Orchestrator | AWS proprietary | Kubernetes |
| API | AWS API | Kubernetes API |
| Learning Curve | Easier | More complex |
| Cloud Agnostic | ❌ AWS only | ✅ Yes |
| Best For | AWS-native applications | Existing Kubernetes users or multi-cloud strategies |

---

# Node Types

| Node Type | Who Manages It? |
| --- | --- |
| Managed Node Group | AWS creates and manages EC2 worker nodes |
| Self-Managed Nodes | You create and manage EC2 worker nodes |
| AWS Fargate | No worker nodes; AWS manages all compute |

---

# Storage Comparison

| Storage | Use Case |
| --- | --- |
| Amazon EBS | Persistent storage for a single EC2-based workload |
| Amazon EFS | Shared file storage across nodes; works with Fargate |
| Amazon FSx for Lustre | High-performance computing and analytics |
| Amazon FSx for NetApp ONTAP | Enterprise NAS/file storage features |

---

# ECS vs EKS vs Fargate

| Service | What It Does |
| --- | --- |
| Amazon ECS | AWS-managed container orchestration using AWS APIs |
| Amazon EKS | Managed Kubernetes on AWS |
| AWS Fargate | Serverless compute engine for running containers (usable with ECS and EKS) |

Notice:

**Fargate is not an orchestrator.**

It is **compute**.

You can use:

```
Amazon ECS

↓

Fargate
```

or

```
Amazon EKS

↓

Fargate
```

---

# AWS SAA-C03 Memory Tricks

### 📦 Docker

Packages your application into containers.

---

### 🎯 Kubernetes

Manages many containers.

---

### ☁️ Amazon EKS

Managed Kubernetes on AWS.

---

### 🖥️ Managed Node Group

AWS manages the EC2 worker nodes.

---

### 🔧 Self-Managed Nodes

You manage the EC2 worker nodes.

---

### 🚀 AWS Fargate

Serverless compute for containers—no EC2 instances to manage.

---

### 💾 Amazon EBS

Persistent block storage for a single EC2-based workload.

---

### 📂 Amazon EFS

Shared file storage that multiple nodes can access, including EKS workloads on Fargate.

## Quick Exam Decision Guide

- **Company already uses Kubernetes on-premises or in another cloud** → **Amazon EKS**
- **Need the simplest AWS-native container service** → **Amazon ECS**
- **Don't want to manage EC2 worker nodes** → **AWS Fargate**
- **Need shared storage across multiple Kubernetes pods** → **Amazon EFS**
- **Need persistent block storage for one node** → **Amazon EBS**
