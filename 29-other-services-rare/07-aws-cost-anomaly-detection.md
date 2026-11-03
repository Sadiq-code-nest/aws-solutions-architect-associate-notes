# AWS Cost Anomaly Detection — SAA-C03 Exam Notes

## 1. What is it?

**AWS Cost Anomaly Detection** is an AWS cost-management service that uses **machine learning (ML)** to continuously monitor AWS spending and detect **unusual or unexpected cost increases**.

Think:

> **"AWS spending suddenly became abnormal — tell me!"**
> 

Unlike a simple fixed threshold, it **learns your historical spending patterns** and detects anomalies based on what is unusual for your environment.

---

## 2. Simple real-world example

Normally your company spends:

```
EC2 monthly cost

$500
$520
$510
$530
$515
```

Suddenly:

```
$1,400  🚨
```

You didn't define:

> "Alert me when cost > $1,000."
> 

You don't need to.

Cost Anomaly Detection learns the normal pattern and can identify the unusual increase.

```
Normal spending
      ↓
$500 → $520 → $510 → $530
                         \
                          \
                         $1,400 🚨
                          ↑
                   Unusual anomaly
```

It can then provide an **anomaly report and root-cause analysis**.

---

# 3. How it works

```
AWS Cost & Usage
       ↓
Cost Anomaly Detection
       ↓
ML learns historical spending
       ↓
Detect unusual spending
       ↓
Identify possible root cause
       ↓
Alert
       ↓
SNS
```

---

# 4. What can it monitor?

You can create cost monitors for:

| Monitor | Example |
| --- | --- |
| AWS Services | EC2 suddenly becomes expensive |
| Member Accounts | One account has abnormal spending |
| Cost Allocation Tags | `Environment=Production` spending spikes |
| Cost Categories | "Production workloads" suddenly cost more |

---

# 5. Important difference: Threshold vs ML

This is a **major exam distinction**.

### AWS Budgets

You define a threshold:

```
Budget = $1,000

Cost reaches $1,000
        ↓
🚨 Alert
```

### Cost Anomaly Detection

You don't need to define a fixed threshold.

```
Historical spending
        ↓
ML learns normal pattern
        ↓
Unexpected deviation
        ↓
🚨 Anomaly detected
```

### 🧠 Memory

> **Budgets = "I set the limit."**
> 
> 
> **Anomaly Detection = "AWS learns what's unusual."**
> 

---

# 6. Continuous cost increase

It's not only a **one-time spike**.

It can also detect **continuous unexpected increases**.

Example:

```
Normal:

$500 → $510 → $520 → $515

Unexpected continuous increase:

$500 → $600 → $700 → $800 → $900
       ↑
   suspicious trend
```

This is useful when a configuration mistake gradually increases spending.

---

# 7. Root-Cause Analysis

Cost Anomaly Detection doesn't simply say:

> 🚨 "Your bill increased."
> 

It can provide **root-cause analysis** to help investigate the anomaly.

For example:

```
🚨 Cost Anomaly

Unexpected increase: +$420

Possible contributors:
    ↓
EC2
    ↓
us-east-1
    ↓
Production workload
    ↓
Specific cost allocation dimension
```

This helps you understand **what caused the unusual spending**.

---

# 8. Notifications

When an anomaly is detected, you can receive notifications.

### Individual alerts

```
Anomaly detected
       ↓
Notification
```

### Daily / Weekly summaries

Instead of receiving an alert for every event, you can receive a summary.

SNS can be used for notifications.

```
Cost Anomaly Detection
          ↓
         SNS
          ↓
Email / notification
```

---

# 9. Cost Anomaly Detection vs Cost Explorer vs Budgets

🔥 **Know this table for SAA-C03.**

| Feature | Cost Explorer | Cost Anomaly Detection | AWS Budgets |
| --- | --- | --- | --- |
| Main purpose | Analyze costs | Detect unusual spending | Control spending against planned budget |
| Uses ML | ❌ | ✅ | ❌ |
| Learns historical spending | For analysis | **Yes, for anomaly detection** | ❌ |
| Fixed threshold required | ❌ | ❌ | **Usually yes** |
| Detect unexpected spike | ❌ Primary purpose | **✅** | Only if threshold crossed |
| Detect unusual continuous increase | ❌ | **✅** | ❌ |
| Root-cause analysis | ❌ Primary purpose | **✅** | ❌ |
| Cost visualization | **✅** | ❌ Primary purpose | Limited |
| Forecasting | **✅** | ❌ | **✅** |
| Savings Plan recommendations | **✅** | ❌ | ❌ |
| SNS notifications | ❌ Primary purpose | **✅** | **✅** |
| Service/account monitoring | Analysis | **✅** | Budget-based |

---

# 🎯 SAA-C03 Exam Scenarios

### Scenario 1

> A company wants AWS to automatically detect unusual spending patterns using machine learning without manually defining spending thresholds.
> 

✅ **AWS Cost Anomaly Detection**

---

### Scenario 2

> A company normally spends $5,000/month, but suddenly its AWS spending becomes abnormal. It wants to automatically detect the unexpected increase.
> 

✅ **AWS Cost Anomaly Detection**

---

### Scenario 3

> A company wants to receive an SNS notification when AWS detects unusual spending.
> 

✅ **AWS Cost Anomaly Detection + SNS**

---

### Scenario 4

> A company wants to understand which AWS service or account caused an unexpected cost increase.
> 

✅ **AWS Cost Anomaly Detection**

Because it provides **root-cause analysis** for detected anomalies.

---

### Scenario 5

> A company wants to be notified when its monthly AWS spending exceeds $10,000.
> 

Think:

✅ **AWS Budgets**

Not necessarily Anomaly Detection.

---

### Scenario 6

> A company wants to visualize and analyze its AWS costs by service and month.
> 

Think:

✅ **Cost Explorer**

---

# 🔑 Exam Keywords

If the question says:

- **Machine learning**
- **Unusual spending**
- **Unexpected cost**
- **Cost spike**
- **Abnormal spending**
- **No predefined threshold**
- **Historical spending patterns**
- **Continuous cost increase**
- **Root-cause analysis**
- **Anomaly alert**
- **SNS notification**

👉 **AWS Cost Anomaly Detection**

---

# 🧠 Memory Trick

### **"Anomaly = Abnormal"**

> **Cost Anomaly Detection = ML watches your normal spending and screams when something looks ABNORMAL. 🚨**
> 

Or remember:

```
Cost Explorer
   ↓
"WHERE did my money go?"

AWS Budgets
   ↓
"STOP me when I cross my limit!"

Cost Anomaly Detection
   ↓
"THIS spending looks WEIRD!"
```

### 📌 SAA-C03 Excel Takeaway

> **AWS Cost Anomaly Detection uses ML to learn historical spending patterns and automatically detect unusual one-time spikes or continuous cost increases without requiring fixed thresholds. It can monitor services, accounts, tags, and cost categories, provide root-cause analysis, and send alerts through SNS.**
>
