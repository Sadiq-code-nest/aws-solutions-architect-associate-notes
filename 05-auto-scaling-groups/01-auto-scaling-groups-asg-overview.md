# AWS Auto Scaling Group (ASG) — Complete SAA-C03 Notes

## 1. What is an Auto Scaling Group?

An **Auto Scaling Group (ASG)** automatically manages the number of EC2 instances running for your application.

Think of it as an **automatic EC2 workforce manager**:

```
                    Users
                      │
                      ▼
               ┌─────────────┐
               │     ALB     │
               └──────┬──────┘
                      │
             ┌────────▼────────┐
             │ Auto Scaling    │
             │     Group       │
             └─────┬─────┬─────┘
                   │     │
              ┌────▼─┐ ┌─▼────┐
              │ EC2  │ │ EC2  │
              └──────┘ └──────┘

              Normal traffic
                   │
                   ▼
              Need more?
                   │
                   ▼
                + EC2
                   │
                   ▼
             Scale OUT 📈

             Low traffic
                   │
                   ▼
              Need fewer?
                   │
                   ▼
                - EC2
                   │
                   ▼
              Scale IN 📉
```

### Core purpose

ASG helps you:

- **Scale out** → add EC2 instances when demand increases
- **Scale in** → remove EC2 instances when demand decreases
- Maintain **minimum capacity**
- Maintain **maximum capacity**
- Maintain a **desired capacity**
- Replace unhealthy/terminated instances
- Automatically register new instances with a load balancer

**Important:** The ASG itself has no additional charge. You pay for the AWS resources it launches, such as EC2 instances, EBS, load balancers, etc.

---

# 2. The 3 Most Important ASG Numbers

Suppose:

```
Minimum = 2
Desired = 3
Maximum = 6
```

| Setting | Meaning |
| --- | --- |
| **Minimum capacity** | ASG should normally never go below this number |
| **Desired capacity** | Number of instances ASG currently wants |
| **Maximum capacity** | ASG cannot scale beyond this number |

### Example

```
Minimum = 2
Desired = 3
Maximum = 6
```

Normal:

```
EC2  EC2  EC2
 ↑    ↑    ↑
      3 instances
```

Heavy traffic:

```
EC2 EC2 EC2 EC2 EC2 EC2
<--------- 6 ----------->
        Scale OUT
```

Low traffic:

```
EC2 EC2
<--2-->
 Scale IN
```

### SAA-C03 memory

> **MIN = floor**
> 
> 
> **DESIRED = current target**
> 
> **MAX = ceiling**
> 

---

# 3. Why Do We Need ASG?

Without ASG:

```
Traffic ↑

You manually:
1. Notice high traffic
2. Launch EC2
3. Configure EC2
4. Add it to load balancer
5. Repeat...
```

With ASG:

```
Traffic ↑
    │
    ▼
CloudWatch metric/alarm
    │
    ▼
ASG scaling policy
    │
    ▼
Launch EC2 automatically
    │
    ▼
Register with ALB
```

So ASG provides:

**Automation + elasticity + availability**

---

# 4. ASG + Load Balancer

This is one of the **most important architectures** for SAA-C03.

```
                     Users
                       │
                       ▼
                ┌────────────┐
                │    ALB     │
                └─────┬──────┘
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
     ┌─────┐       ┌─────┐       ┌─────┐
     │ EC2 │       │ EC2 │       │ EC2 │
     └─────┘       └─────┘       └─────┘
          ←──── Auto Scaling Group ────→
```

When ASG launches a new EC2:

```
ASG
 │
 ├── Launch EC2
 │
 ├── Configure using Launch Template
 │
 └── Register EC2 with ALB
```

When ASG terminates an EC2:

```
ASG
 │
 └── Remove EC2 from ALB
```

### Why is this useful?

The ALB distributes traffic only to healthy registered instances.

So:

**ALB = distributes traffic**

**ASG = manages number of instances**

Together:

> **ALB + ASG = scalable and highly available EC2 application**
> 

---

# 5. ASG Automatically Replaces Unhealthy Instances

ASG isn't only about scaling.

It can also maintain the desired number of healthy instances.

Example:

```
Desired capacity = 3

EC2-A ✅
EC2-B ❌ unhealthy
EC2-C ✅
```

ASG detects the unhealthy/terminated instance.

```
EC2-A ✅
EC2-B ❌ → terminate/replace
EC2-C ✅
             │
             ▼
          Launch EC2
             │
             ▼
         EC2-D ✅
```

Result:

```
EC2-A
EC2-C
EC2-D
```

Still:

```
Desired = 3
```

### Exam concept

If the question says:

> "Automatically replace unhealthy EC2 instances"
> 

Think:

**Auto Scaling Group**

---

# 6. ASG Launch Template

ASG needs instructions for **how to create new EC2 instances**.

This is provided by a **Launch Template**.

```
              Launch Template
                    │
        ┌───────────┼────────────┐
        ▼           ▼            ▼
       AMI      Instance Type   SG
        │           │            │
        └───────────┼────────────┘
                    ▼
               EC2 Instance
```

A Launch Template can define:

| Component | Example |
| --- | --- |
| AMI | Amazon Linux |
| Instance type | `t3.micro` |
| User Data | Install application |
| EBS volumes | 20 GB gp3 |
| Security Group | Web-SG |
| SSH key pair | `my-key` |
| IAM role | EC2 instance role |
| Network | VPC/subnets |
| Load Balancer | Target group |
| Instance configuration | Other EC2 settings |

### Important

**Launch Configurations are legacy/deprecated.**

For modern AWS architecture:

> **Use Launch Templates.**
> 

---

# 7. ASG + CloudWatch

ASG can use **CloudWatch metrics** to determine when to scale.

Example:

```
EC2 instances
     │
     ▼
CloudWatch
     │
     │ CPU > 70%
     ▼
Scaling Policy
     │
     ▼
ASG
     │
     ▼
Launch more EC2
```

For example:

```
Average CPU = 85%

          ↓

CloudWatch Alarm

          ↓

Scale-out policy

          ↓

ASG

2 EC2 → 4 EC2
```

When demand decreases:

```
Average CPU = 20%

          ↓

CloudWatch Alarm

          ↓

Scale-in policy

          ↓

ASG

4 EC2 → 2 EC2
```

---

# 8. ASG Scaling Policies

There are several important scaling approaches.

| Scaling type | Idea | Example |
| --- | --- | --- |
| **Manual scaling** | Change desired capacity manually | 2 → 5 |
| **Dynamic scaling** | Automatically respond to metrics | CPU > 70% |
| **Target tracking** | Maintain a target metric | Keep CPU around 50% |
| **Step scaling** | Scale different amounts based on alarm severity | CPU 70% → +1, CPU 90% → +3 |
| **Scheduled scaling** | Scale at known times | Every weekday at 8 AM |
| **Predictive scaling** | Forecast demand and scale ahead | Expected traffic spike |

---

# 9. Target Tracking Scaling

Very common in exams.

Suppose:

```
Target CPU = 50%
```

ASG continuously tries to maintain approximately:

```
CPU ≈ 50%
```

Example:

```
Traffic ↑
   │
   ▼
CPU = 75%
   │
   ▼
ASG adds instances
   │
   ▼
CPU decreases
   │
   ▼
CPU ≈ 50%
```

### Exam keyword

> "Maintain average CPU utilization at approximately 50%"
> 

Think:

**Target Tracking Scaling Policy**

---

# 10. Step Scaling

Step scaling allows different scaling actions depending on how severe the situation is.

Example:

| CPU | Action |
| --- | --- |
| 60–70% | +1 instance |
| 70–80% | +2 instances |
| >80% | +3 instances |

Architecture:

```
CPU
 │
 ├── 65% → +1 EC2
 │
 ├── 75% → +2 EC2
 │
 └── 90% → +3 EC2
```

Useful when you want more aggressive scaling during larger traffic spikes.

---

# 11. Scheduled Scaling

If you know traffic patterns ahead of time, you don't need to wait for CloudWatch metrics.

Example: university application.

```
7:00 AM
Traffic begins increasing

8:00 AM
Students start using system

12:00 PM
High traffic

6:00 PM
Traffic decreases
```

You can schedule:

```
Before 8 AM:
2 EC2 → 6 EC2

After 6 PM:
6 EC2 → 2 EC2
```

### Exam keyword

> "Known predictable traffic pattern"
> 

Think:

**Scheduled Scaling**

---

# 12. Predictive Scaling

Predictive scaling uses historical usage patterns to forecast future demand.

Example:

```
Past data

Monday:
8 AM → high traffic
9 AM → very high traffic
10 AM → high traffic

AWS predicts:
Tomorrow 8 AM → traffic will increase
```

ASG can scale **before** demand arrives.

### Difference

| Scheduled | Predictive |
| --- | --- |
| You define the schedule | AWS predicts demand |
| Known predictable event | Historical patterns |
| "Every day at 8 AM" | "AWS expects traffic to increase" |

---

# 13. ASG Across Multiple Availability Zones

For high availability, don't put every instance in one AZ.

Bad:

```
VPC

AZ-1
┌─────────────┐
│ EC2 EC2 EC2 │
└─────────────┘

AZ-2
└── No EC2 ──┘
```

If AZ-1 fails:

```
💥 AZ-1 failure

All EC2 unavailable
```

Better:

```
                    ALB
                     │
             ┌───────┴───────┐
             │               │
            AZ-1            AZ-2
             │               │
          EC2 EC2          EC2 EC2
             └──── ASG ──────┘
```

If AZ-1 fails:

```
AZ-1 ❌

AZ-2
EC2 ✅
EC2 ✅
```

ASG can launch replacement instances in the surviving AZ(s), subject to the configured subnet/AZ capacity.

### SAA-C03 keyword

> **"Highly available EC2 application"**
> 

Think:

**ASG + multiple AZs + ALB**

---

# 14. ASG Health Checks

ASG can use health checks to determine whether instances are healthy.

Common health-check types include:

- **EC2 health checks**
- **ELB health checks**

Example:

```
ALB
 │
 ├── EC2-A → Healthy ✅
 ├── EC2-B → Healthy ✅
 └── EC2-C → Unhealthy ❌
                    │
                    ▼
                  ASG
                    │
                    ▼
             Replace instance
```

### Important distinction

An EC2 instance can be:

```
EC2 itself = technically running
Application = broken
```

An ALB health check can detect application-level problems that a basic EC2 instance health check may not.

---

# 15. Complete Real-World Example

Imagine an online shopping website.

Normal traffic:

```
              Customers
                  │
                  ▼
                 ALB
                  │
          ┌───────┼───────┐
          ▼       ▼       ▼
         EC2     EC2     EC2
          └────── ASG ────┘

Minimum = 2
Desired = 3
Maximum = 10
```

### Black Friday begins

Traffic increases:

```
Users ↑↑↑↑
   │
   ▼
ALB
   │
   ▼
CPU increases
   │
   ▼
CloudWatch
   │
   ▼
ASG scaling policy
   │
   ▼
3 EC2 → 6 EC2
```

Traffic gets even higher:

```
6 EC2 → 10 EC2
```

But:

```
Maximum = 10
```

So ASG cannot launch more than 10.

### After Black Friday

Traffic decreases:

```
10 EC2
   │
   ▼
CPU decreases
   │
   ▼
ASG scales in
   │
   ▼
10 → 4 EC2
```

The company saves money because unnecessary EC2 instances aren't kept running.

---

# 16. ASG vs Load Balancer

Very important distinction.

| Feature | Auto Scaling Group | Load Balancer |
| --- | --- | --- |
| Main job | Manage number of EC2 instances | Distribute traffic |
| Adds EC2? | ✅ | ❌ |
| Removes EC2? | ✅ | ❌ |
| Replaces unhealthy EC2? | ✅ | ❌ |
| Distributes requests? | ❌ | ✅ |
| Health checks? | ✅ | ✅ |
| Works across AZs? | ✅ | ✅ |
| Scaling based on CPU/metrics? | ✅ | ❌ |

### Memory trick

> **ASG = How many servers?**
> 
> 
> **ALB = Which server gets the request?**
> 

---

# 17. ASG vs EC2

| EC2 | ASG |
| --- | --- |
| One virtual server | Group of EC2 instances |
| You manage lifecycle manually unless automated otherwise | Automatically manages instance count |
| Doesn't inherently scale based on demand | Can automatically scale |
| Doesn't automatically maintain desired capacity | Maintains desired capacity |
| Individual compute resource | EC2 management/orchestration mechanism |

---

# 18. ASG vs ECS Service Auto Scaling

Don't confuse these.

| ASG | ECS Service Auto Scaling |
| --- | --- |
| Scales EC2 instances | Scales ECS tasks |
| Infrastructure-level scaling | Application/container-level scaling |
| Adds/removes EC2 | Adds/removes containers/tasks |
| Used with EC2 capacity | Used for ECS services |

For example:

```
ECS on EC2

ECS Service Auto Scaling
        │
        ▼
  Number of Tasks
        │
        ▼
  4 tasks → 10 tasks
```

The underlying EC2 capacity may separately be managed by an ASG.

---

# 19. ASG + User Data

When ASG launches a new instance, you often don't want to manually configure it.

Use **EC2 User Data** in the Launch Template.

Example:

```
ASG launches EC2
       │
       ▼
User Data runs
       │
       ├── Install Nginx
       ├── Download application
       ├── Install dependencies
       └── Start application
```

So every newly launched instance can automatically become application-ready.

---

# 20. ASG Lifecycle — Simple Picture

```
             ASG
              │
              ▼
       Launch Template
              │
              ▼
         Launch EC2
              │
              ▼
       User Data runs
              │
              ▼
       Register with ALB
              │
              ▼
         Serve traffic
              │
       ┌──────┴──────┐
       │             │
    Healthy       Unhealthy
       │             │
       ▼             ▼
 Continue       Replace EC2
```

---

# 21. SAA-C03 Scenario Recognition Table

| Scenario wording | Think |
| --- | --- |
| Traffic suddenly increases | **Scale Out** |
| Traffic decreases | **Scale In** |
| Maintain minimum/maximum EC2 count | **ASG** |
| Automatically replace terminated EC2 | **ASG** |
| Automatically replace unhealthy instances | **ASG + health checks** |
| Distribute requests across EC2 | **ALB** |
| Highly available EC2 | **ASG across multiple AZs** |
| Keep CPU around 50% | **Target Tracking** |
| Different scaling amounts based on CPU severity | **Step Scaling** |
| Traffic predictable every morning | **Scheduled Scaling** |
| Forecast future demand | **Predictive Scaling** |
| Automatically configure newly launched EC2 | **Launch Template + User Data** |
| Automatically add new instances to LB | **ASG + Load Balancer** |
| Modern replacement for Launch Configuration | **Launch Template** |
| ASG itself costs money? | **No — underlying resources cost money** |

---

# 22. The Master Architecture to Remember

For SAA-C03, visualize this:

```
                         Internet
                            │
                            ▼
                    ┌──────────────┐
                    │     ALB      │
                    │ Health Check │
                    └──────┬───────┘
                           │
            ┌──────────────┴──────────────┐
            │       Auto Scaling Group    │
            │                             │
       ┌────▼────┐                   ┌────▼────┐
       │   AZ-1  │                   │   AZ-2  │
       │         │                   │         │
       │ EC2 ✅  │                   │ EC2 ✅  │
       │ EC2 ✅  │                   │ EC2 ✅  │
       └─────────┘                   └─────────┘
            ▲                             ▲
            │                             │
            └──────── ASG manages ────────┘
                          │
                          ▼
                     CloudWatch
                          │
              ┌───────────┴──────────┐
              │                      │
         Traffic ↑               Traffic ↓
              │                      │
              ▼                      ▼
         Scale OUT               Scale IN
```

### The complete logic

**User traffic changes → CloudWatch observes metrics → Scaling Policy decides → ASG changes EC2 count → ALB distributes traffic → ASG maintains healthy capacity.**

---

## ⭐ Final Exam Memory

If you remember only this:

> **ASG manages EC2 capacity.**
> 
> 
> **ALB distributes traffic.**
> 
> **CloudWatch provides metrics/alarms.**
> 
> **Scaling policies decide when/how much to scale.**
> 
> **Launch Template tells ASG how to create EC2.**
> 
> **Multiple AZs provide high availability.**
> 

And the core relationship:

```
             CloudWatch
                 │
                 ▼
         Scaling Policy
                 │
                 ▼
              ASG
          ↙     ↓      ↘
       EC2     EC2     EC2
          \     │     /
             ALB
              │
              ▼
             Users
```

**SAA-C03 mental model:**

**"How many EC2?" → ASG**

**"Which EC2 receives traffic?" → ALB**

**"When should we scale?" → Scaling Policy + CloudWatch**

**"How should new EC2 be configured?" → Launch Template**

**"How do I survive an AZ failure?" → ASG across multiple AZs**
