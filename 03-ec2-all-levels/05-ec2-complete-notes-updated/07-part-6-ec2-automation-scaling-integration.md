# EC2 Part 6 — Automation + Scaling Integration

This is the part where the individual EC2 concepts become an **automated architecture**.

The core chain to understand is:

> **AMI → Launch Template → Auto Scaling Group → ALB → CloudWatch → Scaling**
> 

And for cheaper/interruptible capacity:

> **AMI → Launch Template → ASG → Mixed Instances → Spot**
> 

---

# 1. Launch Templates ⭐⭐⭐⭐⭐

A **Launch Template** is a reusable blueprint that tells EC2/Auto Scaling:

> **"When you launch an instance, configure it like this."**
> 

It can define things such as:

- AMI
- instance type
- key pair
- security groups
- IAM instance profile
- EBS volumes
- network settings
- user data
- purchasing options
- metadata options
- monitoring
- placement-related settings

Think:

```
Launch Template
      │
      ├── AMI
      ├── Instance type
      ├── Security group
      ├── IAM role
      ├── EBS
      ├── User Data
      └── Other EC2 settings
             │
             ▼
        New EC2 Instance
```

### Exam trigger

> **"Standardized configuration for instances launched automatically"**
> 

→ **Launch Template**

---

# 2. Launch Template vs AMI

This distinction is extremely important.

### AMI

Answers:

> **"What software/image should the instance start with?"**
> 

Example:

```
AMI
├── Linux
├── Nginx
├── Application
└── Dependencies
```

### Launch Template

Answers:

> **"How should EC2 launch and configure the instance?"**
> 

Example:

```
Launch Template
├── AMI
├── Instance type
├── SG
├── IAM role
├── EBS
└── User Data
```

Therefore:

> **AMI = machine image**
> 
> 
> **Launch Template = launch instructions**
> 

---

# 3. Launch Template Versions ⭐⭐⭐⭐⭐

Launch Templates support **versions**.

For example:

```
Launch Template
│
├── Version 1 → AMI v1
├── Version 2 → AMI v2
└── Version 3 → AMI v3
```

An ASG can use a particular Launch Template version.

This becomes very important during application updates.

Example:

```
Old:
LT v1 → AMI v1

New:
LT v2 → AMI v2
```

You can update the ASG to use the new version.

But there's a critical point:

> **Changing the Launch Template does not automatically update existing instances.**
> 

Existing instances were already launched using the old configuration.

This leads directly to **Instance Refresh** later.

---

# 4. Launch Configurations — Legacy

Launch Configurations are the **older mechanism** for defining how ASG instances are launched.

Think:

```
Launch Configuration
        ↓
Legacy
```

Modern AWS architecture generally uses:

> **Launch Templates**
> 

rather than Launch Configurations.

---

# 5. Launch Template vs Launch Configuration

| Feature | Launch Template | Launch Configuration |
| --- | --- | --- |
| Modern | ✅ | ❌ Legacy |
| Recommended for new designs | ✅ | ❌ |
| Versioning | ✅ | ❌ |
| More modern EC2 features | ✅ | Limited |
| Used with ASG | ✅ | ✅ |
| Preferred exam answer for new architecture | **Launch Template** | Usually no |

### Exam trigger

> **"Create a new Auto Scaling architecture"**
> 

→ **Launch Template**

If the question explicitly says:

> **"existing legacy configuration"**
> 

Launch Configuration may appear.

### Memory:

> **Template = modern**
> 
> 
> **Configuration = legacy**
> 

---

# 6. Auto Scaling Groups ⭐⭐⭐⭐⭐

An **Auto Scaling Group (ASG)** manages a fleet of EC2 instances.

It defines:

- minimum capacity
- desired capacity
- maximum capacity
- which Launch Template to use
- which subnets/AZs to use
- scaling policies
- health checks

Example:

```
ASG
Min = 2
Desired = 4
Max = 10
```

Normally:

```
2 ≤ instances ≤ 10
```

with the target normally being:

```
4 instances
```

---

# 7. Minimum / Desired / Maximum

This is extremely important.

Suppose:

```
Min = 2
Desired = 4
Max = 10
```

### Minimum

ASG tries to maintain at least:

```
2
```

instances.

### Desired

ASG aims for:

```
4
```

instances.

### Maximum

ASG cannot scale beyond:

```
10
```

instances through that ASG's normal scaling behavior.

### Memory trick

> **Min = floor**
> 
> 
> **Desired = target**
> 
> **Max = ceiling**
> 

---

# 8. AMI + Launch Template + ASG ⭐⭐⭐⭐⭐

These three are frequently tested together.

Imagine you're running an application.

### Step 1 — Create AMI

```
AMI
│
├── OS
├── Application
├── Dependencies
└── Configuration
```

### Step 2 — Create Launch Template

```
Launch Template
│
├── AMI
├── Instance type
├── SG
├── IAM role
└── User Data
```

### Step 3 — Create ASG

```
ASG
│
├── Min = 2
├── Desired = 4
├── Max = 10
└── Launch Template
```

Result:

```
                 ASG
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
      EC2        EC2        EC2 ...
       │          │
       └────── same AMI ────┘
```

### Exam memory

> **AMI = what**
> 
> 
> **Launch Template = how**
> 
> **ASG = how many**
> 

That's one of the most useful EC2 formulas.

---

# 9. User Data + Bootstrap

User Data is commonly used to perform **initial configuration when an EC2 instance launches**.

For example:

```
Launch EC2
   ↓
User Data executes
   ↓
Install packages
   ↓
Pull configuration
   ↓
Start application
```

Example conceptual workflow:

```
AMI
+
User Data
        ↓
Fully configured EC2
```

---

# 10. AMI vs User Data

Another major exam distinction.

### AMI

Contains the prebuilt environment.

```
OS
Application
Libraries
Dependencies
```

### User Data

Performs launch-time actions.

```
Install/update
Configure
Register
Start service
```

### Memory:

> **AMI = bake it in.**
> 
> 
> **User Data = configure it at boot.**
> 

---

# 11. When Should You Use AMI vs User Data?

Suppose you need 100 instances.

### Approach A — Heavy setup every time

```
Launch
 ↓
Install application
 ↓
Install dependencies
 ↓
Configure
 ↓
Start
```

Every instance spends time bootstrapping.

---

### Approach B — Pre-baked AMI

```
AMI
 ↓
Application already installed
 ↓
Launch
 ↓
Small User Data configuration
```

Usually faster and more predictable.

### Strong architecture pattern

> **Bake stable software into the AMI; use User Data for small dynamic initialization.**
> 

---

# 12. User Data in an ASG

This is where automation becomes powerful.

Suppose ASG scales:

```
4 instances
   ↓
CPU rises
   ↓
ASG launches 4 more
   ↓
8 instances
```

Each newly launched instance can execute User Data.

Therefore:

```
ASG
 ↓
Launch EC2
 ↓
User Data
 ↓
Configure application
 ↓
Ready
```

This allows newly launched instances to become operational without manual intervention.

---

# 13. CloudWatch + ASG ⭐⭐⭐⭐⭐

CloudWatch provides monitoring/metrics.

ASG uses those metrics to make scaling decisions.

Conceptually:

```
EC2
 ↓
Metrics
 ↓
CloudWatch
 ↓
Scaling Policy
 ↓
ASG
 ↓
Launch/Terminate instances
```

Example:

```
Average CPU > 70%
        ↓
CloudWatch metric
        ↓
ASG scaling policy
        ↓
Scale OUT
        ↓
More EC2 instances
```

---

# 14. Scale Out vs Scale In

### Scale out

Add instances.

```
2 → 4 → 6
```

Usually happens when demand increases.

---

### Scale in

Remove instances.

```
6 → 4 → 2
```

Usually happens when demand decreases.

### Memory:

> **Out = add**
> 
> 
> **In = remove**
> 

---

# 15. CloudWatch Does Not Launch Instances by Itself

Important distinction.

CloudWatch:

> **observes**
> 

ASG:

> **changes capacity**
> 

Scaling policy:

> **defines how ASG should respond**
> 

So:

```
CloudWatch
   ↓
"What is happening?"

Scaling Policy
   ↓
"What should we do?"

ASG
   ↓
"Change fleet size."
```

---

# 16. Target Tracking Example

Suppose:

```
Target CPU = 50%
```

Current:

```
CPU = 80%
```

ASG increases capacity.

```
2 instances
 ↓
4 instances
 ↓
Average CPU falls
 ↓
~50%
```

If traffic decreases:

```
CPU = 20%
 ↓
ASG scales in
```

### Exam trigger

> **"Maintain average CPU around X%"**
> 

→ **Target tracking scaling**

---

# 17. ALB + ASG ⭐⭐⭐⭐⭐

This is one of the most common AWS architecture patterns.

The ALB distributes traffic.

The ASG manages EC2 capacity.

```
                Users
                  │
                  ▼
                 ALB
                  │
        ┌─────────┼─────────┐
        ▼         ▼         ▼
       EC2       EC2       EC2
        │         │         │
        └─────────┼─────────┘
                  ▼
                 ASG
```

More precisely:

```
Users
 ↓
ALB
 ↓
Target Group
 ↓
EC2 instances
 ↑
ASG manages them
```

---

# 18. Why ALB + ASG?

Suppose one EC2 becomes unhealthy.

ALB health check:

```
EC2 #2
   ↓
Unhealthy
```

ALB stops sending new traffic to it.

ASG can detect the unhealthy instance and replace it.

```
Unhealthy EC2
      ↓
ASG terminates/replaces
      ↓
New EC2
      ↓
ALB target registration
```

This provides a powerful combination of:

- load balancing
- health checks
- horizontal scaling
- automatic replacement

---

# 19. ALB + ASG — Important Health Check Concept

An ASG can use:

### EC2 status checks

and/or

### ELB health checks

Using ELB health checks allows ASG to understand whether the instance is actually healthy **from the load balancer's perspective**.

Example:

```
EC2 is technically running
BUT
application returns HTTP 500
```

An ALB health check can identify the application as unhealthy.

This is much more useful than merely knowing:

> "The EC2 instance is powered on."
> 

---

# 20. Spot + ASG ⭐⭐⭐⭐⭐

ASGs can use Spot Instances.

This is a major cost-optimization architecture.

Instead of:

```
ASG
 ↓
100% On-Demand
```

you can use:

```
ASG
 ↓
Mixed capacity
 ├── On-Demand
 └── Spot
```

This gives:

> **cost savings + interruption resilience**
> 

---

# 21. Why Use Spot in an ASG?

Spot capacity can disappear.

Therefore you don't want:

```
Critical application
+
100% Spot
+
No fallback
```

Instead:

```
ASG
│
├── On-Demand baseline
│
└── Spot additional capacity
```

If Spot capacity is interrupted:

```
Spot instance
    ↓
Interrupted
    ↓
ASG replaces capacity
    ↓
On-Demand / another Spot capacity
```

depending on the configuration.

---

# 22. Mixed Instances ⭐⭐⭐⭐⭐

A Mixed Instances Policy allows an ASG to use multiple instance types and purchasing options.

For example:

```
ASG
│
├── m7i.large On-Demand
├── m7a.large Spot
├── m6i.large Spot
└── m6a.large Spot
```

Why?

Because relying on a single Spot capacity pool can increase interruption risk.

Multiple instance types give the ASG more options.

---

# 23. Mixed Instances — Exam Trigger

Look for:

> **"Use multiple instance types to improve Spot availability."**
> 

→ **Mixed Instances Policy**

or:

> **"Diversify across instance types/AZs."**
> 

→ Mixed instances + appropriate Spot allocation strategy.

---

# 24. On-Demand Base + Spot Percentage

A common architecture is:

```
ASG desired = 10

On-Demand base = 2
Remaining = Spot
```

Conceptually:

```
2 On-Demand
+
8 Spot
=
10 instances
```

The critical baseline has more reliable capacity.

The burst capacity uses cheaper Spot.

---

# 25. Capacity Rebalancing ⭐⭐⭐⭐⭐

Capacity Rebalancing is designed to help ASGs respond when Spot Instances are at increased risk of interruption.

Think:

```
Spot Instance
      ↓
AWS indicates increased interruption risk
      ↓
ASG proactively launches replacement
      ↓
Replacement becomes healthy
      ↓
At-risk Spot instance terminates
```

This is better than waiting until the Spot instance is actually interrupted.

### Memory:

> **Spot interruption risk → Capacity Rebalancing**
> 

---

# 26. Capacity Rebalancing vs Spot Interruption

Without proactive rebalancing:

```
Spot
 ↓
Interruption
 ↓
Instance disappears
 ↓
ASG replaces
```

With Capacity Rebalancing:

```
Spot
 ↓
Interruption risk increases
 ↓
ASG launches replacement
 ↓
Replacement ready
 ↓
Old Spot removed
```

So:

> **Capacity Rebalancing = proactive Spot replacement.**
> 

---

# 27. Capacity Rebalancing Does NOT Mean "No Spot Interruptions"

This is a trap.

It does **not** guarantee Spot instances won't be interrupted.

It simply helps ASG react **before interruption where possible**.

Therefore:

```
Capacity Rebalancing
≠
Spot guarantee
```

---

# 28. Instance Refresh ⭐⭐⭐⭐⭐

This is one of the most important ASG automation features.

Suppose your ASG currently runs:

```
AMI v1
```

You create:

```
AMI v2
```

and update the Launch Template.

What happens to existing instances?

> **They do not automatically become AMI v2.**
> 

They're still running:

```
AMI v1
```

Instance Refresh allows you to progressively replace existing instances.

---

# 29. Instance Refresh Flow

```
Old:
AMI v1
 ↓
Create AMI v2
 ↓
Launch Template v2
 ↓
Update ASG
 ↓
Instance Refresh
 ↓
Replace old instances
 ↓
New:
AMI v2
```

This is essentially a controlled fleet rollout.

---

# 30. Why Not Just Terminate Everything?

Suppose:

```
100 EC2 instances
```

You terminate all 100 and launch the new version.

That's dangerous.

You could have:

```
100%
capacity lost
```

Instead:

```
Old instances
      ↓
Gradually replace
      ↓
New instances
```

This reduces service disruption.

---

# 31. Instance Refresh and Minimum Healthy Percentage

Instance Refresh can control how aggressively instances are replaced.

Conceptually:

```
100 instances

Keep sufficient healthy capacity
while replacing old instances.
```

You can balance:

> **deployment speed vs availability**
> 

This is important for production systems.

---

# 32. Instance Refresh ≠ Scaling

Another exam trap.

### Scaling

Changes:

```
number of instances
```

### Instance Refresh

Changes:

```
version/configuration of instances
```

Example:

```
Scaling:
10 → 20

Refresh:
AMI v1 → AMI v2
```

### Memory:

> **Scaling changes quantity.**
> 
> 
> **Refresh changes fleet version.**
> 

---

# 33. Scaling Implications ⭐⭐⭐⭐⭐

Scaling sounds simple:

```
Demand ↑
→ instances ↑
```

But there are architectural consequences.

---

## Implication 1 — Application should be stateless

If you scale from:

```
2 → 10
```

the application should not depend on one particular instance's local state.

Bad architecture:

```
EC2 #1
 └── user session stored locally
```

Then user gets sent to:

```
EC2 #2
```

and loses the session.

Better:

```
EC2
 ↓
External state
 ├── ElastiCache
 ├── DynamoDB
 └── RDS
```

The application tier remains replaceable.

---

# 34. Implication 2 — Don't Store Important Data on Instance Store

ASG can terminate instances.

Therefore:

```
Instance Store
+
ASG
=
Data can disappear
```

Persistent data should generally use appropriate external storage such as:

- EBS for instance-attached persistent block storage
- EFS for shared file storage
- S3 for object storage
- database services for structured application data

---

# 35. Implication 3 — New Instances Must Become Ready Automatically

Imagine:

```
ASG launches instance
```

But the application isn't installed.

Then:

```
ALB
 ↓
New EC2
 ↓
Application missing
```

Bad.

Therefore use:

```
Pre-baked AMI
+
User Data
+
Health checks
```

so new instances can automatically become usable.

---

# 36. Implication 4 — ALB Health Checks Matter

ASG shouldn't simply assume:

> "EC2 is running, therefore application is healthy."
> 

Example:

```
EC2 status = healthy
Application = broken
```

Use ALB/ELB health checks where appropriate.

---

# 37. Implication 5 — Multi-AZ

A production ASG should generally span multiple AZs.

Example:

```
             ALB
              │
       ┌──────┼──────┐
       ▼      ▼      ▼
      AZ-A   AZ-B   AZ-C
       │      │      │
      EC2    EC2    EC2
```

If one AZ becomes unavailable:

```
AZ-A ❌
```

the remaining AZs can continue serving traffic, assuming sufficient capacity.

### Exam trigger

> **"Highly available EC2 fleet"**
> 

→ **ASG across multiple AZs + ALB**

---

# 38. Scaling Does Not Automatically Mean High Availability

This is subtle.

Suppose:

```
ASG
all instances in one AZ
```

You have scaling.

But:

```
AZ fails
 ↓
All instances lost
```

Therefore:

> **Scaling ≠ multi-AZ HA**
> 

For HA:

```
ASG
+
Multiple AZs
+
ALB
```

---

# 39. Common Architecture Pattern #1 — Basic HA Web Application ⭐⭐⭐⭐⭐

```
                  Internet
                     │
                     ▼
                Internet-facing
                     ALB
                  /     \
                 /       \
              AZ-A       AZ-B
               │           │
             EC2         EC2
               \           /
                \         /
                    ASG
```

ASG:

```
Min = 2
Desired = 2
Max = 6
```

CloudWatch:

```
CPU / request-based metric
        ↓
Scaling policy
        ↓
ASG
```

### Use when:

> Highly available web application with automatic scaling.
> 

---

# 40. Common Architecture Pattern #2 — AMI + Launch Template + ASG

```
                 AMI
                  │
                  ▼
          Launch Template
                  │
                  ▼
                 ASG
          ┌───────┼───────┐
          ▼       ▼       ▼
         EC2     EC2     EC2
```

### Use when:

> Every newly launched instance needs the same known configuration.
> 

---

# 41. Common Architecture Pattern #3 — ALB + ASG + CloudWatch

```
Users
  │
  ▼
 ALB
  │
  ▼
 ASG → EC2 fleet
  │
  ▼
CloudWatch metrics
  │
  ▼
Scaling policy
  │
  ├── Scale out
  └── Scale in
```

### Use when:

> Traffic changes dynamically.
> 

---

# 42. Common Architecture Pattern #4 — Spot + On-Demand ASG ⭐⭐⭐⭐⭐

```
                  ASG
                   │
          ┌────────┴────────┐
          ▼                 ▼
     On-Demand            Spot
      baseline           capacity
          │                 │
          └────────┬────────┘
                   ▼
                 ALB
```

Actually, traffic normally flows through the ALB to all healthy targets regardless of purchase type.

### Use when:

> Application can tolerate interruptions and cost optimization is important.
> 

---

# 43. Common Architecture Pattern #5 — Mixed Instances + Capacity Rebalancing

```
                    ASG
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
    Instance A    Instance B    Instance C
     Spot          Spot          On-Demand
        │
        ▼
Interruption risk
        │
        ▼
Capacity Rebalancing
        │
        ▼
Launch replacement
```

### Use when:

> Cost optimization + resilience against Spot interruption.
> 

---

# 44. Common Architecture Pattern #6 — Safe AMI Deployment

Suppose:

```
AMI v1 → production
```

New application:

```
AMI v2
```

Architecture:

```
AMI v2
 ↓
Launch Template v2
 ↓
ASG updated
 ↓
Instance Refresh
 ↓
Gradual replacement
 ↓
Production now runs v2
```

This is a very important real-world AWS pattern.

---

# 45. Common Architecture Pattern #7 — AMI + Small User Data

A strong pattern is:

```
                 AMI
       ┌─────────┴─────────┐
       │                   │
Application          Dependencies
already installed    already installed
       │
       ▼
Launch Template
       │
       ▼
User Data
(dynamic configuration)
       │
       ▼
ASG
```

### Why?

You don't want every instance spending 10 minutes reinstalling the entire application.

Instead:

> **Bake stable components into AMI.**
> 

Use User Data for:

- environment-specific configuration
- registration
- fetching secrets/configuration through appropriate services
- starting services
- small initialization tasks

---

# 46. Common Architecture Pattern #8 — Self-Healing Web Tier

```
                    ALB
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
        EC2-A      EC2-B      EC2-C
          │          │          │
          └──────────┼──────────┘
                     │
                    ASG
                     │
              Health checks
                     │
              Unhealthy EC2
                     │
                     ▼
                  Replace
```

This is the essence of a **self-healing stateless web tier**.

---

# 47. Launch Template + ASG + Instance Refresh

Memorize this workflow:

```
Build AMI
   ↓
Create new Launch Template version
   ↓
Point ASG to new version
   ↓
Instance Refresh
   ↓
Gradually replace old instances
```

### Exam trigger

> **"Update all existing ASG instances to a new AMI/configuration."**
> 

→ **Instance Refresh**

Not merely:

> "Update Launch Template."
> 

---

# 48. Launch Template + User Data

Another common question:

> The company wants every newly launched ASG instance to automatically install/configure software.
> 

Answer:

**Launch Template with User Data.**

Flow:

```
ASG
 ↓
Launch Template
 ↓
EC2
 ↓
User Data
 ↓
Bootstrap
```

---

# 49. ASG + ALB Health Checks

Question:

> Instances may be running but the application itself may be unhealthy. The company wants unhealthy application instances automatically replaced.
> 

Architecture:

```
ALB health check
        ↓
ASG
        ↓
Replace unhealthy instance
```

The important clue is:

> **Application-level health**
> 

rather than merely:

> **EC2 instance status**
> 

---

# 50. Spot + ASG

Question:

> A stateless application needs large amounts of compute capacity. It can tolerate interruptions and wants to reduce cost.
> 

Think:

```
ASG
+
Spot
+
Mixed Instances
```

If high resilience is needed:

```
+
On-Demand baseline
+
Capacity Rebalancing
```

---

# 51. The BIG EC2 Automation Chain

This is probably the single most important diagram from Part 6:

```
                     ┌──────────────┐
                     │     AMI      │
                     │ "WHAT"       │
                     └──────┬───────┘
                            │
                            ▼
                 ┌───────────────────┐
                 │ Launch Template   │
                 │ "HOW"             │
                 └─────────┬─────────┘
                           │
                           ▼
                 ┌───────────────────┐
                 │       ASG         │
                 │     "HOW MANY"    │
                 └─────────┬─────────┘
                           │
                  ┌────────┴────────┐
                  ▼                 ▼
                EC2              EC2...
                  │
                  ▼
              ALB Target
                  │
                  ▼
              Application

CloudWatch
    │
    ▼
Scaling Policy
    │
    ▼
   ASG
    │
    ├── Scale OUT
    └── Scale IN
```

For Spot:

```
ASG
 │
 ├── On-Demand
 └── Spot
       │
       ▼
Capacity Rebalancing
```

For deployments:

```
New AMI
  ↓
New LT version
  ↓
Instance Refresh
  ↓
Gradual fleet replacement
```

---

# 52. Master Confusion-Buster ⭐⭐⭐⭐⭐

| Question asks... | Correct concept |
| --- | --- |
| **What software/image should instances start with?** | **AMI** |
| **How should instances be configured at launch?** | **Launch Template** |
| **How many instances should run?** | **ASG** |
| **Legacy ASG launch definition?** | **Launch Configuration** |
| **Modern replacement for Launch Configuration?** | **Launch Template** |
| **Install/configure software during launch?** | **User Data** |
| **Monitor EC2 metrics?** | **CloudWatch** |
| **Automatically change fleet size?** | **ASG + scaling policy** |
| **Distribute HTTP/HTTPS traffic?** | **ALB** |
| **Application health checks + replacement?** | **ALB health checks + ASG** |
| **Use cheap interruptible capacity?** | **Spot + ASG** |
| **Use several EC2 instance types?** | **Mixed Instances Policy** |
| **Reduce Spot interruption risk proactively?** | **Capacity Rebalancing** |
| **Update existing ASG instances to new AMI?** | **Instance Refresh** |
| **Highly available EC2 fleet?** | **ASG across multiple AZs + ALB** |
| **Min / Desired / Max?** | **Floor / target / ceiling** |
| **Scale number of instances?** | **ASG** |
| **Change instance configuration/version?** | **Instance Refresh** |
| **Pre-bake application?** | **AMI** |
| **Dynamic boot-time configuration?** | **User Data** |

---

# 53. 30-Second Exam Scan

Memorize these **four questions**:

### 1. What?

> **AMI**
> 

### 2. How?

> **Launch Template**
> 

### 3. How many?

> **ASG**
> 

### 4. When/how should the number change?

> **CloudWatch metric + Scaling Policy + ASG**
> 

Then:

```
Traffic
   ↓
ALB
   ↓
ASG EC2 fleet
   ↓
CloudWatch
   ↓
Scaling
```

For cost:

```
ASG
 ↓
Mixed Instances
 ↓
Spot + On-Demand
 ↓
Capacity Rebalancing
```

For deployment:

```
New AMI
 ↓
Launch Template version
 ↓
Instance Refresh
 ↓
Existing fleet updated
```

## Final memory sentence

> **AMI tells EC2 WHAT to run; Launch Template tells it HOW to launch; ASG controls HOW MANY; CloudWatch tells ASG WHEN to scale; ALB distributes traffic; Spot reduces cost; Capacity Rebalancing protects Spot capacity; Instance Refresh updates the existing fleet.**
> 

**EC2 Part 6 complete.**
