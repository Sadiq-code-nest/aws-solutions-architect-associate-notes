# Elastic Beanstalk (SAA-C03) — Complete Crystal Clear Notes

---

# 1. The Problem Developers Face

Imagine you're a developer.

You wrote a Node.js application:

```
app.js
```

All you want is:

> "Run my application on AWS."
> 

But AWS says:

```
Create EC2
Create Security Groups
Create ALB
Create Auto Scaling Group
Configure Health Checks
Configure Scaling Policies
Configure Logs
Configure Monitoring
Configure Deployments
```

Developer's reaction:

```
😫 I just want my code to run!
```

---

# Traditional AWS Deployment

Without Elastic Beanstalk:

```
Developer
    │
    ▼
Create EC2
    │
    ▼
Install App
    │
    ▼
Create ALB
    │
    ▼
Create Auto Scaling Group
    │
    ▼
Configure Scaling
    │
    ▼
Configure Monitoring
    │
    ▼
Deploy Code
```

Lots of work.

---

# Why AWS Created Elastic Beanstalk

AWS realized:

Most web applications look like:

```
Users
  │
  ▼
ALB
  │
  ▼
Auto Scaling Group
  │
  ▼
EC2 Instances
```

Every company repeats this architecture.

So AWS created:

## Elastic Beanstalk

A platform where developers simply upload code.

AWS creates everything else.

---

# What is Elastic Beanstalk?

AWS Elastic Beanstalk is a Platform as a Service (PaaS).

You provide:

```
Application Code
```

AWS manages:

```
EC2
ALB
ASG
Scaling
Monitoring
Health Checks
Deployment
Logging
```

---

# Simple Definition (Exam)

> Elastic Beanstalk is a managed service that automatically provisions and manages infrastructure required to run web applications.
> 

---

# What AWS Creates Behind The Scenes

When you upload your application:

```
My Application.zip
```

Beanstalk automatically creates:

```
Elastic Beanstalk
        │
        ▼
Load Balancer
        │
        ▼
Auto Scaling Group
        │
        ▼
EC2 Instances
```

Optional:

```
RDS
CloudWatch
IAM Roles
Security Groups
```

---

# Important Exam Point

Beanstalk is NOT serverless.

Behind the scenes AWS still creates:

- EC2
- ALB
- ASG

You can see and manage them.

---

# Is Elastic Beanstalk Free?

Beanstalk itself:

```
FREE
```

But you pay for:

```
EC2
ALB
RDS
EBS
CloudWatch
```

that Beanstalk creates.

---

# Who Manages What?

## Developer

Responsible for:

```
Application Code
```

---

## AWS

Responsible for:

```
Infrastructure
Scaling
Monitoring
Load Balancing
Health Checks
Provisioning
```

---

# Core Components of Elastic Beanstalk

There are three important concepts:

```
Application
Application Version
Environment
```

---

# Component 1: Application

Think of Application as:

```
Container of your project
```

Example:

```
Ecommerce-App
```

Inside it:

```
Versions
Environments
Configurations
```

---

Visual:

```
Application
    │
 ┌──┴──┐
 │     │
Version Environment
```

---

# Component 2: Application Version

An application version is:

```
A specific version of your code
```

Example:

```
v1
v2
v3
```

---

Like Git releases:

```
v1.0
v1.1
v2.0
```

Each upload becomes a new Application Version.

---

Example:

```
Shopping App

v1 -> Initial Release
v2 -> Bug Fix
v3 -> New Checkout Feature
```

---

# Component 3: Environment

This is VERY important for exams.

An Environment is:

> Running AWS resources that execute one Application Version.
> 

Example:

```
Prod Environment
       │
       ▼
ALB
ASG
EC2
```

running:

```
Version 3
```

---

Important:

One environment can run:

```
ONE application version
```

at a time.

---

# Multiple Environments

Very common.

```
Application
      │
 ┌────┼─────┐
 │    │     │
Dev Test Prod
```

---

Example:

```
Dev  -> Version 4
Test -> Version 4
Prod -> Version 3
```

Each environment is independent.

---

# Real Company Example

```
Application: Banking App

Environment 1:
Development

Environment 2:
Testing

Environment 3:
Production
```

Same application.

Different environments.

---

# Deployment Flow

```
Developer
    │
Upload v1
    │
    ▼
Application Version v1
    │
Deploy
    ▼
Environment
```

Later:

```
Upload v2
    │
Deploy
    ▼
Environment updated
```

---

# Elastic Beanstalk Environment Tiers

There are TWO tiers.

```
1. Web Server Tier

2. Worker Tier
```

Exam favorite.

---

# 1. Web Server Tier

Used for:

```
Websites
REST APIs
Frontend Apps
```

Receives requests directly from users.

---

Architecture:

```
Users
   │
   ▼
Load Balancer
   │
   ▼
EC2 Instances
```

---

Example

User visits:

```
www.shop.com
```

Request goes:

```
User
 ↓
ALB
 ↓
EC2
 ↓
Response
```

This is Web Server Tier.

---

# Real Example

```
Node.js Website
Spring Boot API
Python Flask App
ASP.NET App
```

All use Web Server Tier.

---

# 2. Worker Tier

Worker Tier does NOT receive user traffic.

Instead it processes background tasks.

---

Example:

User uploads image.

---

Without Worker:

```
User
 ↓
Web Server
 ↓
Resize Image
 ↓
Generate Thumbnail
 ↓
Store Image
 ↓
Return Response
```

User waits.

Slow.

---

With Worker Tier:

```
User
 ↓
Web Server
 ↓
SQS Queue
 ↓
Response Returned Immediately
```

Worker processes later:

```
SQS
 ↓
Worker Instance
 ↓
Resize Image
 ↓
Generate Thumbnail
```

---

Architecture

```
Users
   │
   ▼
Web Tier
   │
   ▼
SQS Queue
   │
   ▼
Worker Tier
```

---

# Easy Memory Trick

## Web Tier

```
Handles HTTP Requests
```

Think:

```
Customer Facing
```

---

## Worker Tier

```
Handles Background Jobs
```

Think:

```
Behind The Scenes
```

---

# Example of Worker Jobs

```
Email Sending
Video Processing
PDF Generation
Thumbnail Creation
Report Generation
Data Import
```

---

# Deployment Modes (Exam Favorite)

Beanstalk supports multiple deployment strategies.

---

## All-at-Once

```
Stop All Servers
Deploy New Version
Start All Servers
```

Fastest.

Has downtime.

---

Visual

```
Old App
 ↓
STOP
 ↓
Deploy
 ↓
New App
```

---

## Rolling

Updates a few instances at a time.

```
Instance 1 Updated
Instance 2 Updated
Instance 3 Updated
```

No complete downtime.

Slower.

---

Visual

```
[Old][Old][Old][Old]

↓

[New][Old][Old][Old]

↓

[New][New][Old][Old]

↓

[New][New][New][New]
```

---

## Rolling with Additional Batch

AWS launches extra instances first.

Safer.

Maintains capacity.

---

Visual

```
4 Existing

+
2 Temporary

Deploy

Remove Old
```

---

## Immutable Deployment

Most important for exams.

AWS launches completely new instances.

```
Old Environment
      +
New Environment
```

---

Visual

```
Old ASG
   │
Running

Create New ASG
   │
Deploy New Version
   │
Health Check Passes
   │
Switch Traffic
```

---

Benefits:

```
Zero Downtime
Easy Rollback
Very Safe
```

Cost:

```
More Expensive
```

because both environments run simultaneously.

---

# SAA-C03 Exam Summary

### Elastic Beanstalk

```
Upload Code
AWS Manages Infrastructure
```

---

### Application

```
Logical container
```

---

### Application Version

```
Specific code release
```

---

### Environment

```
Running AWS resources
```

---

### Web Server Tier

```
Handles user requests
```

Architecture:

```
Users
 ↓
ALB
 ↓
EC2
```

---

### Worker Tier

```
Handles background processing
```

Architecture:

```
Web Tier
 ↓
SQS
 ↓
Worker Tier
```

---

### Deployment Modes

| Mode | Downtime | Risk |
| --- | --- | --- |
| All-at-Once | Yes | High |
| Rolling | No/Minimal | Medium |
| Rolling + Additional Batch | No | Lower |
| Immutable | No | Lowest |

---

# Ultimate Exam Memory

Think:

```
Elastic Beanstalk
=
"I upload code,
AWS builds and manages
ALB + ASG + EC2 for me."
```

And remember:

```
Web Tier = User Requests

Worker Tier = Background Jobs (via SQS)

Immutable Deployment = Safest Deployment Method
```
