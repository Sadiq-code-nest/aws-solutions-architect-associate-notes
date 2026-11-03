# AWS Cost Explorer — SAA-C03 Exam Notes

## 1. What is Cost Explorer?

**AWS Cost Explorer** is a tool that helps you **visualize, analyze, and understand AWS spending and usage over time**.

Think of it as:

> **"Where is my AWS money going?"**
> 

It lets you investigate costs by things like:

- AWS service
- Account
- Region
- Usage type
- Time period
- Resource-level details where supported
- Monthly or hourly granularity

---

## 2. Simple real-world example

Suppose your company receives an AWS bill of **$1,500/month**.

You want to know:

```
Why is AWS costing $1,500?
```

Cost Explorer can break it down:

```
AWS Monthly Cost
│
├── EC2          → $600
├── RDS          → $300
├── NAT Gateway  → $250
├── S3           → $100
├── CloudWatch   → $150
└── Other        → $100
```

Now you immediately know:

> **EC2 and NAT Gateway are major cost contributors.**
> 

---

# 3. Cost Explorer — What can you analyze?

| Capability | Cost Explorer |
| --- | --- |
| View AWS costs | ✅ |
| View AWS usage | ✅ |
| Analyze historical spending | ✅ |
| Group by AWS service | ✅ |
| Filter costs | ✅ |
| Monthly analysis | ✅ |
| Hourly analysis | ✅ |
| Account-level analysis | ✅ |
| Resource-level granularity | ✅ Where supported |
| Create custom reports | ✅ |
| Forecast future usage | ✅ |
| Savings Plan recommendations | ✅ |

---

# 4. Monthly Cost Analysis

One of the most common uses is seeing:

> **How much am I spending on each AWS service every month?**
> 

Example:

```
                 Cost Explorer

January
│
├── EC2        $400
├── RDS        $200
├── S3         $50
└── Other      $100

February
│
├── EC2        $500
├── RDS        $220
├── S3         $60
└── Other      $110
```

You can identify **cost trends** over time.

### Exam clue

> "Analyze AWS spending by service over several months."
> 

👉 **Cost Explorer**

---

# 5. Hourly & Resource-Level Analysis

Cost Explorer can provide more granular analysis.

For example:

> "Which resources are generating the highest costs?"
> 

You can use filters and grouping to investigate the spending.

```
EC2
│
├── Instance A → $120
├── Instance B → $350
├── Instance C → $80
└── Instance D → $450
```

This helps you identify expensive resources.

---

# 6. Cost Explorer vs AWS Cost & Usage Report

These two are easy to confuse.

|  | **Cost Explorer** | **Cost & Usage Report (CUR)** |
| --- | --- | --- |
| Main purpose | Analyze/visualize costs | Detailed billing dataset |
| Ready-made visualization | ✅ | ❌ |
| Interactive analysis | ✅ | Usually external tools |
| Detailed raw billing data | Less focused | ✅ |
| Custom reports | ✅ | Can build externally |
| Best for quick investigation | ✅ | ❌ |
| Best for detailed billing analytics | Good | **Excellent** |

### Memory

> **Cost Explorer = Explore the bill**
> 
> 
> **CUR = Detailed billing data**
> 

---

# 7. Savings Plans

Cost Explorer can also help you determine an appropriate **Savings Plan**.

Suppose your EC2 usage is consistently high:

```
EC2 usage
████████████████████
████████████████████
████████████████████
```

Instead of paying entirely on-demand, you may benefit from a **Savings Plan** commitment.

Cost Explorer can analyze your historical usage and provide recommendations.

### Exam clue

> "Analyze historical usage to determine an optimal Savings Plan."
> 

👉 **Cost Explorer**

---

# 8. Forecasting

Cost Explorer can **forecast future usage/costs based on historical usage patterns**.

Example:

```
Past                         Future
                             forecast
│                               │
│  $500 ──$550──$600──$650 ────$700
│                    ───────────────►
└────────────────────────────────────
```

According to the material you're studying, forecasting can extend **up to 18 months** based on previous usage.

### Exam clue

> "Predict/forecast future AWS costs based on historical usage."
> 

👉 **Cost Explorer**

---

# 9. Cost Explorer vs Budgets vs Cost Anomaly Detection

This is **very important for SAA-C03**.

| Feature | Cost Explorer | AWS Budgets | Cost Anomaly Detection |
| --- | --- | --- | --- |
| View/analyze costs | ✅ | Limited | Limited |
| Visualize spending | ✅ | ❌ | ❌ |
| Historical cost analysis | ✅ | Limited | ✅ |
| Forecast | ✅ | ✅ | ❌ |
| Set spending threshold | ❌ Primary purpose | ✅ | ❌ |
| Alert when budget exceeded | ❌ Primary purpose | ✅ | ❌ |
| Detect unusual spending | ❌ | ❌ | ✅ |
| Savings Plan recommendations | ✅ | ❌ | ❌ |

### 🧠 Easy memory

**Cost Explorer**

> **"Where did my money go?"**
> 

**AWS Budgets**

> **"Don't let me spend more than X."**
> 

**Cost Anomaly Detection**

> **"Why did my spending suddenly become weird?"**
> 

---

# 10. Real exam scenario

### Scenario

A company wants to:

- Analyze its AWS spending by service
- Compare costs month-to-month
- Examine hourly usage
- Create custom cost reports
- Forecast future usage
- Find an appropriate Savings Plan

### Answer:

✅ **AWS Cost Explorer**

Because the primary requirement is **analyzing and understanding AWS costs and usage**.

---

# 🎯 SAA-C03 Exam Keywords

When you see:

- **Visualize AWS costs**
- **Analyze AWS spending**
- **Understand usage**
- **Cost by AWS service**
- **Monthly cost**
- **Hourly cost**
- **Historical usage**
- **Custom cost reports**
- **Forecast future usage**
- **Savings Plan recommendation**

👉 Think **AWS Cost Explorer**.

---

## 🧠 One-line memory trick

> **Cost Explorer = "EXPLORE where my AWS MONEY went and where it's going."**
> 

### Final exam takeaway

> **AWS Cost Explorer analyzes and visualizes AWS costs and usage over time, supports filtering/grouping and custom reports, provides granular cost analysis, Savings Plan recommendations, and usage forecasting.**
>
