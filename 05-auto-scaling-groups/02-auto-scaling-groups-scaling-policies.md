# Auto Scaling Groups - Scaling Policies

Absolutely. Here is the **ASG Scaling Policies + Cooldown + Metrics + a final Master Table** designed specifically for **AWS SAA-C03 revision**.

# AWS Auto Scaling Groups — Scaling Policies

The easiest way to understand ASG scaling is:

```
                     APPLICATION
                          │
                          ▼
                     CloudWatch
                     Metrics/Data
                          │
                          ▼
                 Scaling Policy
                          │
              ┌───────────┴───────────┐
              ▼                       ▼
          Scale OUT                Scale IN
         Add EC2                   Remove EC2
              │                       │
              └───────────┬───────────┘
                          ▼
                         ASG
```

**Scaling Policy = the decision-making rule.**

---

# 1. Dynamic Scaling

Dynamic scaling means:

> **ASG automatically changes the number of EC2 instances based on current workload/metrics.**
> 

Main dynamic scaling approaches:

1. Target Tracking
2. Simple Scaling
3. Step Scaling

---

# 2. Target Tracking Scaling ⭐

### The simplest dynamic scaling approach

You tell ASG:

> "Keep this metric around this target."
> 

Example:

```
Target CPU = 40%
```

ASG continuously tries to maintain:

```
Average CPU ≈ 40%
```

### Example

```
Current:

EC2    EC2    EC2
 │      │      │
 └──────┴──────┘
      CPU = 75%
          │
          ▼
      ASG scales OUT
          │
          ▼
EC2 EC2 EC2 EC2 EC2
          │
          ▼
      CPU ≈ 40%
```

If workload falls:

```
CPU = 15%
   │
   ▼
ASG scales IN
   │
   ▼
Remove unnecessary EC2
```

### Key exam clue

> "Keep average CPU utilization at 40%."
> 

**Answer → Target Tracking Scaling**

---

# 3. Simple Scaling

Simple scaling works with a **CloudWatch alarm**.

Example:

```
CloudWatch Alarm

CPU > 70%
     │
     ▼
Add 2 instances
```

Another alarm:

```
CPU < 30%
     │
     ▼
Remove 1 instance
```

Conceptually:

```
              CloudWatch
                  │
          ┌───────┴────────┐
          ▼                ▼
       CPU > 70%        CPU < 30%
          │                │
          ▼                ▼
       +2 EC2            -1 EC2
```

### Important

Simple scaling generally waits for the current scaling activity/cooldown to finish before responding to another alarm.

For more responsive multi-step reactions, **Step Scaling** is generally preferred.

---

# 4. Step Scaling ⭐

Step scaling is more flexible.

Instead of saying:

> "CPU > 70% → always add 2"
> 

you can define different actions for different levels.

Example:

| CPU | Scaling action |
| --- | --- |
| < 30% | Remove 1 |
| 30–70% | No change |
| 70–80% | Add 2 |
| 80–90% | Add 3 |
| > 90% | Add 5 |

Visual:

```
CPU
 │
 │  >90% ───────────────► +5 EC2
 │
 │ 80-90% ──────────────► +3 EC2
 │
 │ 70-80% ──────────────► +2 EC2
 │
 │ 30-70% ──────────────► No change
 │
 │ <30% ────────────────► -1 EC2
 │
 └──────────────────────────────
```

### Exam clue

> "Different scaling actions depending on how much the metric exceeds the threshold."
> 

**Answer → Step Scaling**

---

# 5. Target Tracking vs Step Scaling vs Simple Scaling

| Feature | Target Tracking | Simple Scaling | Step Scaling |
| --- | --- | --- | --- |
| Main idea | Maintain target | Alarm triggers fixed action | Alarm triggers different actions |
| Example | CPU ≈ 40% | CPU >70% → +2 | CPU 70% → +2, 90% → +5 |
| Setup | ⭐ Very simple | Simple | More configuration |
| Uses CloudWatch | Yes | Yes | Yes |
| Different scaling amounts | Automatically adjusts | Usually fixed action | ✅ Yes |
| Best for | Maintaining a metric target | Basic alarm-driven scaling | More granular control |
| SAA keyword | "Maintain 40%" | "Alarm → add/remove X" | "Different steps based on severity" |

### Memory trick

> **Target = maintain a target**
> 
> 
> **Simple = one simple reaction**
> 
> **Step = different steps**
> 

---

# 6. Scheduled Scaling

Scheduled scaling is used when you **already know when demand will change**.

Example:

A company knows traffic increases every Friday evening.

```
Friday 5 PM
     │
     ▼
Increase minimum capacity to 10
     │
     ▼
Prepare for traffic
```

Example:

```
Normal:
Min = 2
Desired = 3

Friday 5 PM:
Min = 10
Desired = 10
```

After the event:

```
Friday 11 PM:
Min = 2
Desired = 3
```

### Best scenario

> "Traffic increases every weekday from 9 AM to 5 PM."
> 

Use:

**Scheduled Scaling**

---

# 7. Scheduled vs Dynamic Scaling

| Situation | Best choice |
| --- | --- |
| Traffic unexpectedly increases | Dynamic scaling |
| CPU needs to stay around 40% | Target Tracking |
| CPU >70% → add 2 | Simple/Step Scaling |
| Different actions at 70%, 80%, 90% | Step Scaling |
| Traffic predictably increases every Friday at 5 PM | Scheduled Scaling |
| AWS predicts future demand from historical patterns | Predictive Scaling |

---

# 8. Predictive Scaling ⭐

Predictive scaling looks at **historical workload patterns** and forecasts future demand.

Think:

```
Historical Load
      │
      ▼
Analyze patterns
      │
      ▼
Generate forecast
      │
      ▼
Predict future demand
      │
      ▼
Schedule scaling ahead
```

Example:

Historical data:

```
Every Monday:

08:00 → Low
09:00 → Medium
10:00 → High
11:00 → Very High
```

AWS can predict:

```
Tomorrow:

09:00 → Medium
10:00 → High
11:00 → Very High
```

So ASG can prepare capacity **before the traffic arrives**.

### Exam clue

> "Forecast workload using historical data and scale ahead of demand."
> 

**Answer → Predictive Scaling**

---

# 9. Predictive vs Scheduled Scaling

This distinction is important.

| Scheduled Scaling | Predictive Scaling |
| --- | --- |
| You explicitly define schedule | AWS forecasts demand |
| Based on known timing | Based on historical patterns |
| "Every Friday at 5 PM" | "AWS predicts traffic will increase at 10 AM" |
| Manual schedule | Forecast-driven |
| No forecasting required | Uses historical load forecasting |

### Memory trick

**Scheduled = YOU know the future**

**Predictive = AWS predicts the future**

---

# 10. Good Metrics for ASG Scaling

Choosing the correct metric is extremely important.

## CPUUtilization

Measures average CPU utilization across your instances.

Example:

```
EC2-A → 80%
EC2-B → 75%
EC2-C → 85%

Average ≈ 80%
```

If CPU is consistently high:

```
CPU ↑
 ↓
Scale OUT
```

Good when your application is **CPU-bound**.

---

# 11. RequestCountPerTarget ⭐

This is especially useful with an **Application Load Balancer**.

Instead of asking:

> "How much CPU is being used?"
> 

you can ask:

> "How many requests is each EC2 instance receiving?"
> 

Example:

```
              ALB
               │
       ┌───────┼───────┐
       ▼       ▼       ▼
      EC2     EC2     EC2
      100     100     100
    requests requests requests
```

Target:

```
RequestCountPerTarget = 100
```

If requests increase:

```
100 → 200 → 300
```

ASG can add more EC2 instances.

After scaling:

```
             ALB
              │
     ┌────────┼────────┐
     ▼        ▼        ▼
    EC2      EC2      EC2
     │        │        │
    100      100      100
```

### Why is this useful?

Some applications aren't CPU-heavy.

For example:

```
CPU = 20%
Requests = extremely high
```

CPU-based scaling might incorrectly think everything is fine.

**RequestCountPerTarget** can be a better metric.

---

# 12. Network In / Network Out

Useful when your application is **network-bound**.

Example:

```
Network Out
    ↑
    │
Very high
    │
    ▼
Scale OUT
```

Good for applications where network throughput is the main bottleneck.

---

# 13. Custom CloudWatch Metrics

You aren't limited to built-in metrics.

You can publish your own metric to CloudWatch.

Example:

```
Application
    │
    │ custom metric
    ▼
CloudWatch

"OrdersWaiting = 5000"
    │
    ▼
ASG Scaling Policy
    │
    ▼
Launch more EC2
```

Potential custom metrics:

- Orders waiting
- Queue depth
- Active users
- Jobs waiting
- Requests per second
- Application-specific workload

### Exam clue

> "Scale based on a custom application metric."
> 

Think:

**CloudWatch Custom Metric + ASG Scaling Policy**

---

# 14. Scaling Cooldown ⭐

This is an important SAA-C03 concept.

After ASG performs a scaling activity, it may enter a **cooldown period**.

The purpose:

> Give the system time to stabilize before performing additional scaling actions.
> 

Your notes mention a **default 300 seconds** cooldown for ASG.

Conceptually:

```
Scale OUT
   │
   ▼
Launch EC2
   │
   ▼
Cooldown
   │
   │ Wait for metrics to stabilize
   ▼
Evaluate again
```

---

# 15. Why Cooldown Exists

Imagine:

```
CPU = 90%
```

ASG launches 2 EC2 instances.

Immediately after launching:

```
CPU still = 90%
```

Why?

Because:

- New EC2 may still be starting
- Application may still be installing
- User Data may still be executing
- New instance may not yet be serving traffic
- Metrics may not have stabilized

If ASG immediately launches more instances:

```
3 EC2
 ↓
5 EC2
 ↓
8 EC2
 ↓
12 EC2
```

You might over-scale unnecessarily.

Cooldown gives the new instances time to become useful.

---

# 16. Cooldown Architecture

```
                   CPU = 80%
                       │
                       ▼
                 CloudWatch
                       │
                       ▼
                Scaling Policy
                       │
                       ▼
                 ASG Scale OUT
                       │
                       ▼
                 Launch EC2
                       │
                       ▼
              ┌────────────────┐
              │    COOLDOWN    │
              │                │
              │ Let metrics    │
              │ stabilize      │
              └───────┬────────┘
                      │
                      ▼
                Evaluate again
```

### Exam idea

> Cooldown prevents ASG from reacting too quickly to temporary metric changes after a scaling activity.
> 

---

# 17. Ready-to-Use AMI and Cooldown

Your notes mention an important optimization.

If your new EC2 requires a lot of setup:

```
Launch EC2
   ↓
Install OS packages
   ↓
Install application
   ↓
Download dependencies
   ↓
Configure application
   ↓
Start application
   ↓
Ready
```

It takes time.

Instead, use a **pre-baked AMI**:

```
AMI
 │
 ├── OS
 ├── Dependencies
 ├── Application
 └── Configuration
        │
        ▼
     Launch EC2
        │
        ▼
   Almost ready
```

### Benefit

New EC2 becomes useful faster.

That can reduce the time required for the system to respond to scaling events.

---

# 18. ASG Scaling — Complete Flow

```
                   USER TRAFFIC
                        │
                        ▼
                       ALB
                        │
                        ▼
                  EC2 Instances
                        │
            ┌───────────┴──────────┐
            │                      │
       CPU / Requests          Network /
       / Custom Metric         Custom Metric
            │                      │
            └───────────┬──────────┘
                        ▼
                    CloudWatch
                        │
                        ▼
                 Scaling Policy
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
 Target Tracking    Step Scaling    Scheduled/
                                   Predictive
        │               │                │
        └───────────────┼────────────────┘
                        ▼
                       ASG
                        │
                ┌───────┴───────┐
                ▼               ▼
             Scale OUT       Scale IN
                │               │
              +EC2             -EC2
                │
                ▼
             Cooldown
                │
                ▼
         Metrics stabilize
```

---

# ⭐ ASG MASTER TABLE — SAA-C03

This is the **one-table revision sheet** you can use before the exam.

| ASG Concept | What it does | Trigger / Input | Example | Best exam clue |
| --- | --- | --- | --- | --- |
| **Auto Scaling Group** | Automatically manages EC2 count | Scaling policies, health checks, schedules | 3 EC2 → 6 EC2 | "Automatically add/remove EC2" |
| **Minimum Capacity** | Lowest desired capacity ASG should maintain | ASG configuration | Min = 2 | "Never have fewer than 2" |
| **Desired Capacity** | Current target number of instances | ASG configuration/scaling | Desired = 3 | "Currently want 3" |
| **Maximum Capacity** | Highest number ASG can scale to | ASG configuration | Max = 10 | "Never exceed 10" |
| **Scale Out** | Adds EC2 instances | Increased demand | 3 → 6 | "Traffic increased" |
| **Scale In** | Removes EC2 instances | Reduced demand | 6 → 3 | "Traffic decreased" |
| **Target Tracking** ⭐ | Maintains metric near target | Metric target | CPU ≈ 40% | **"Keep/maintain around X%"** |
| **Simple Scaling** | Performs a scaling action after an alarm | CloudWatch alarm | CPU >70% → +2 | "Alarm → fixed action" |
| **Step Scaling** ⭐ | Performs different scaling actions depending on alarm severity | CloudWatch alarm + steps | CPU 70% +2, 90% +5 | **"Different levels/actions"** |
| **Scheduled Scaling** ⭐ | Scales at a known time | User-defined schedule | Friday 5 PM → 10 EC2 | **"Predictable schedule"** |
| **Predictive Scaling** ⭐ | Forecasts future load and scales ahead | Historical workload | Predict tomorrow's traffic spike | **"Forecast/predict future demand"** |
| **CPUUtilization** | Scales based on average CPU | CloudWatch | CPU >70% | CPU-bound application |
| **RequestCountPerTarget** ⭐ | Scales based on requests per target | ALB metric | 500 requests/EC2 | **Request-heavy application** |
| **Network In/Out** | Scales based on network throughput | CloudWatch | Network Out very high | Network-bound workload |
| **Custom Metric** | Scales based on application-specific data | CloudWatch custom metric | Orders waiting >5000 | "Custom application metric" |
| **CloudWatch Alarm** | Detects metric threshold conditions | CloudWatch metric | CPU >70% | "Trigger scaling" |
| **Scaling Policy** | Defines how ASG reacts | Metric/schedule | Add 2 instances | "How should ASG scale?" |
| **Cooldown** ⭐ | Gives instances time to stabilize after scaling | Scaling activity | Wait before another scaling action | **"Prevent rapid repeated scaling"** |
| **Launch Template** ⭐ | Defines how new EC2 instances are created | AMI, type, SG, IAM role, etc. | Launch `t3.micro` with AMI | **"Configuration for new EC2"** |
| **User Data** | Configures instance during launch | Launch Template | Install/start application | "Configure new instance automatically" |
| **Health Check** | Detects unhealthy instances | EC2/ELB health | Replace unhealthy EC2 | **"Replace unhealthy instance"** |
| **Multiple AZs** ⭐ | Improves availability | ASG subnet/AZ configuration | EC2 in AZ-1 + AZ-2 | **"High availability"** |
| **ALB + ASG** ⭐ | Distributes traffic + manages capacity | ALB + ASG | ALB → EC2 fleet | **"Scalable web application"** |
| **Pre-baked AMI** | Reduces instance startup/configuration time | AMI | App already installed | "Scale faster / ready quickly" |

---

# 🔥 Scaling Policies — One-Table Comparison

| Policy | Core question | How it works | Example | Remember |
| --- | --- | --- | --- | --- |
| **Target Tracking** ⭐ | "What target should I maintain?" | ASG automatically adjusts capacity to maintain target | Keep CPU ≈ 40% | **Maintain target** |
| **Simple Scaling** | "What should I do after this alarm?" | Alarm triggers a scaling action | CPU >70% → +2 | **Simple reaction** |
| **Step Scaling** ⭐ | "How severe is the problem?" | Different scaling actions for different alarm levels | 70% → +2, 90% → +5 | **Multiple steps** |
| **Scheduled Scaling** ⭐ | "When do I already know traffic will change?" | Scaling happens at predefined times | Friday 5 PM → 10 EC2 | **Known schedule** |
| **Predictive Scaling** ⭐ | "What will demand probably be?" | Uses historical patterns to forecast and scale ahead | Predict Monday morning spike | **AWS forecast** |

---

# 🧠 Final SAA-C03 Decision Tree

When you see an ASG question, ask:

```
                    ASG QUESTION
                         │
                         ▼
              "When/how should it scale?"
                         │
       ┌─────────────────┼──────────────────┐
       │                 │                  │
       ▼                 ▼                  ▼
  Maintain target    Known schedule    Future forecast
       │                 │                  │
       ▼                 ▼                  ▼
Target Tracking    Scheduled Scaling  Predictive Scaling
       │
       │
       └──────────────┐
                      ▼
               CloudWatch alarm?
                      │
                 ┌────┴────┐
                 ▼         ▼
             One action   Different
             / simple     levels
                 │         │
                 ▼         ▼
              Simple     Step
              Scaling    Scaling
```

### 🔑 The 5 lines to memorize

> **Target Tracking → Maintain a target**
> 

> **Simple Scaling → Alarm triggers a simple scaling action**
> 

> **Step Scaling → Different alarm levels → different scaling amounts**
> 

> **Scheduled Scaling → Known future schedule**
> 

> **Predictive Scaling → Historical data → forecast → scale ahead**
> 

And:

> **Cooldown → Give newly launched/remaining instances time to stabilize before reacting to another scaling event.**
>
