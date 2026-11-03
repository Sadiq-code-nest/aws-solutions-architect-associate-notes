# Instance Scheduler on AWS — SAA-C03 Exam Notes

## 1. What is Instance Scheduler on AWS?

**Instance Scheduler on AWS is a pre-built AWS Solution that automatically starts and stops supported AWS resources according to a schedule to reduce costs.**

### Most important point:

> ⚠️ **Instance Scheduler on AWS is NOT an AWS service. It is an AWS Solution deployed using AWS CloudFormation.**
> 

Think:

```
CloudFormation
      │
      ▼
Instance Scheduler
      │
      ├── Lambda
      ├── DynamoDB
      └── Resource Tags
             │
             ▼
     Start / Stop Resources
```

---

# 2. What problem does it solve?

Suppose a company has development EC2 instances:

```
Monday → Friday
9 AM → 6 PM     → Developers work

6 PM → 9 AM      → Nobody uses them
```

Keeping the instances running 24/7 wastes money.

Instance Scheduler can automatically:

```
9:00 AM
   ↓
START EC2
   ↓
Developers work
   ↓
6:00 PM
   ↓
STOP EC2
   ↓
No unnecessary compute cost
```

### Simple idea:

> **Don't run resources when nobody needs them.**
> 

---

# 3. Supported Resources

For your SAA-C03 exam, remember:

| Resource | Supported? |
| --- | --- |
| **EC2 Instances** | ✅ |
| **EC2 Auto Scaling Groups** | ✅ |
| **RDS Instances** | ✅ |

The key exam idea is **scheduled start/stop to reduce cost**.

---

# 4. How does it work?

The solution uses AWS components behind the scenes.

```
             CloudFormation
                   │
                   ▼
        Instance Scheduler Solution
                   │
        ┌──────────┴──────────┐
        ▼                     ▼
     Lambda               DynamoDB
        │                     │
        │              Schedule information
        │
        ▼
 Resource Tags
        │
        ▼
 ┌───────────────┐
 │ EC2 / RDS /   │
 │ ASG resources │
 └───────────────┘
        │
        ▼
 Start / Stop
```

### Important components

**CloudFormation**

→ Deploys the solution.

**DynamoDB**

→ Stores/manages schedule information.

**Lambda**

→ Performs the automation to start/stop resources.

**Tags**

→ Identify which resources should follow a particular schedule.

---

# 5. Real-World Example

A company has 50 development EC2 instances.

They are needed only:

```
Monday-Friday
08:00 → 18:00
```

Instead of:

```
EC2
24 hours × 7 days
```

Instance Scheduler can do:

```
08:00 AM
   ↓
Start tagged EC2 instances

18:00 PM
   ↓
Stop tagged EC2 instances
```

So the company doesn't pay for unnecessary runtime outside working hours.

---

# 6. Example with Tags

Suppose:

```
EC2 Instance
Name: Dev-Server-01

Tags:
Environment = Dev
Schedule    = BusinessHours
```

The scheduler can recognize the resource based on its tags and apply the appropriate schedule.

```
Tag
Schedule = BusinessHours
       ↓
Instance Scheduler
       ↓
08:00 → START
18:00 → STOP
```

### Memory:

> **Tag → Schedule → Start/Stop**
> 

---

# 7. Cross-Account & Cross-Region

The solution can support scheduling resources across:

- Multiple AWS accounts
- Multiple AWS Regions

Example:

```
AWS Organization
│
├── Account A
│    └── EC2 → Schedule
│
├── Account B
│    └── EC2 → Schedule
│
└── Account C
     └── RDS → Schedule
```

Useful for organizations managing many development/test environments.

---

# 8. Instance Scheduler vs Auto Scaling

Don't confuse these.

|  | Instance Scheduler | EC2 Auto Scaling |
| --- | --- | --- |
| Main purpose | **Scheduled start/stop** | Automatically adjust capacity |
| Main goal | **Cost savings** | Availability + scalability |
| Example | Stop Dev EC2 at 6 PM | Add EC2 when traffic increases |
| Trigger | Time/schedule | Demand/metrics/schedules |
| Typical use | Dev/test environments | Production applications |

### Exam shortcut:

> **"Stop resources outside business hours" → Instance Scheduler**
> 

> **"Add/remove instances based on demand" → Auto Scaling**
> 

---

# 9. Instance Scheduler vs AWS Instance Scheduler Service

This is a potential trick.

### Instance Scheduler on AWS

✅ AWS Solution

✅ Deployed using CloudFormation

✅ Uses Lambda/DynamoDB/tags

✅ Automates scheduled start/stop

It is **not** a standalone AWS managed service like EC2 or RDS.

---

# 🎯 SAA-C03 Exam Scenarios

### Scenario 1

> A company wants to automatically stop development EC2 instances every evening and restart them every morning to reduce costs.
> 

✅ **Instance Scheduler on AWS**

---

### Scenario 2

> A company wants EC2 instances to run only during business hours.
> 

✅ **Instance Scheduler on AWS**

---

### Scenario 3

> A company has development and test resources across multiple AWS accounts and wants centralized scheduled start/stop automation.
> 

✅ **Instance Scheduler on AWS**

---

### Scenario 4

> A company wants EC2 instances to automatically increase from 2 to 10 when CPU utilization increases.
> 

❌ Instance Scheduler

✅ **EC2 Auto Scaling**

---

### Scenario 5

> A company wants a solution deployed using CloudFormation that uses tags and Lambda to automatically start and stop EC2 and RDS resources according to predefined schedules.
> 

✅ **Instance Scheduler on AWS**

---

# 🔑 Exam Keywords

| Question says... | Think... |
| --- | --- |
| Start/stop resources automatically | **Instance Scheduler** |
| Business hours | **Instance Scheduler** |
| Stop at night | **Instance Scheduler** |
| Start in morning | **Instance Scheduler** |
| Reduce EC2 costs | **Instance Scheduler** |
| Development/test environments | **Instance Scheduler** |
| Schedule EC2 instances | **Instance Scheduler** |
| Schedule RDS instances | **Instance Scheduler** |
| CloudFormation solution | **Instance Scheduler** |
| Uses tags | **Instance Scheduler** |
| Lambda + DynamoDB | **Instance Scheduler** |
| Cross-account scheduling | **Instance Scheduler** |
| Scale based on demand | ❌ → **Auto Scaling** |

---

# 🧠 Memory Trick

### **"Office Hours = Instance Scheduler"**

Imagine:

> 🏢 **Office opens → START**
> 
> 
> 🌙 **Office closes → STOP**
> 

So:

**Business hours → Start/Stop → Save money → Instance Scheduler**

And remember the special exam fact:

> **Instance Scheduler = AWS Solution, not AWS service.**
> 

### 📝 SAA-C03 Excel Takeaway

**Topic:** Cost Optimization / Automation

**Core concept:** Instance Scheduler on AWS is a CloudFormation-deployed AWS Solution that automatically starts and stops EC2, EC2 Auto Scaling Groups, and RDS resources according to schedules, using tags and automation components such as Lambda and DynamoDB.

**Exam trigger:**

> **"Automatically start/stop EC2 or RDS based on business hours to reduce costs" → Instance Scheduler on AWS.**
>
