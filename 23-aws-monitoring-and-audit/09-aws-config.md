# 🧠 AWS Config — Full Concept (Simple + Practical)

---

# 🌍 1. BIG IDEA (Understand This First)

👉 AWS Config =

**Track + Audit + Check Compliance of your AWS resources over time**

---

## 🎯 Core Concept

> **Config = “What changed + Is it compliant?”**
> 

---

## 🧠 Simple Meaning

- CloudTrail → *Who did what*
- Config → *What changed & is it allowed*

---

# 🔄 2. What AWS Config Actually Does

- IMAGES
    
    
    
    
    
    
    

## ✅ 1. Records configuration

- Tracks resource settings
- Example:
    - Security Group rules
    - S3 bucket permissions

---

## ✅ 2. Tracks changes over time

- Keeps **history (timeline)**

---

## ✅ 3. Checks compliance (using rules)

---

## 🧠 Real Example

👉 Yesterday:

- S3 bucket = private ✅

👉 Today:

- Someone made it public. ❌

➡️ Config:

- Detects change
- Marks **NON-COMPLIANT**

---

# 📌 3. Types of Questions Config Answers

- Is SSH (port 22) open to world? ❗
- Is S3 bucket public? ❗
- Who changed my ALB config?
- When did this change happen?

---

# ⚙️ 4. Config Rules (VERY IMPORTANT)

## 🔹 Two types

### ✅ 1. AWS Managed Rules

- Prebuilt (70+)
- Examples:
    - No public S3
    - No open SSH

---

### ✅ 2. Custom Rules

- You define logic
- Uses:
    - AWS Lambda

---

## 🧠 Example

👉 Rule:

> “All EC2 must be t2.micro”
> 

➡️ If not → NON-COMPLIANT

---

# ⏱️ 5. When Rules Are Triggered

## ✅ 1. On change

- When resource is modified

## ✅ 2. Periodic

- Every X hours (e.g., 2 hours)

---

## 🧠 Example

- Change SG → rule runs immediately
- Or check every 2 hours

---

# 🚨 6. IMPORTANT LIMITATION (EXAM TRAP)

> ❌ Config does NOT block actions
> 

---

## ❗ Meaning

- Someone CAN:
    - Open port 22
- Config will:
    - Detect it
    - Mark NON-COMPLIANT

---

## 🧠 Easy Memory

> “Config = Police report, NOT police”
> 

---

# 🔧 7. Remediation (AUTO FIX)

- IMAGES
    
    
    
    
    
    
    

## ✅ How to fix non-compliance automatically

Uses:

- AWS Systems Manager (SSM Automation)

---

## 🧠 Example

👉 Rule:

- IAM keys older than 90 days ❌

➡️ Config detects

➡️ Triggers SSM document

➡️ Deactivates key

---

## 🔁 Retry Feature

- Can retry fix (e.g., 5 times)

---

# 🔔 8. Notifications (VERY IMPORTANT)

## 🔹 Option 1

- Config → Amazon SNS

---

## 🔹 Option 2

- Config → Amazon EventBridge → SNS / Lambda

---

## 🧠 Example

- SG becomes non-compliant
    
    → EventBridge
    
    → SNS
    
    → Email / Slack alert
    

---

# 🌐 9. Scope (IMPORTANT)

- Works **per region**
- But can:
    - Aggregate multiple regions/accounts

---

# 💾 10. Storage + Analysis

- Stores history in S3
- Can analyze using:
    - Amazon Athena

---

# ⚖️ 11. Config vs CloudTrail (EXAM FAVORITE)

| Feature | Config | CloudTrail |
| --- | --- | --- |
| Tracks changes | ✅ | ❌ |
| Tracks API calls | ❌ | ✅ |
| Compliance check | ✅ | ❌ |
| History timeline | ✅ | ❌ |

---

## 🧠 Easy Memory

> CloudTrail = “WHO did it”
> 
> 
> Config = “WHAT changed”
> 

---

# ⚡ 12. 30-Second Memory Trick

> 🧠 Config = Compliance
> 
> 
> 📜 Timeline = History
> 
> ⚠️ Detect only (no block)
> 
> 🔧 Fix via SSM
> 

---

# 🚨 13. Exam Rules (VERY IMPORTANT)

| If question says… | Answer |
| --- | --- |
| “Check compliance” | Config |
| “Track config changes” | Config |
| “Prevent action” | IAM ❗ |
| “Auto fix non-compliant” | Config + SSM |
| “API logging” | CloudTrail |

---

# ⚠️ 14. Common Traps

| Scenario | Wrong | Correct |
| --- | --- | --- |
| Block SSH | Config ❌ | IAM/Security Group ✅ |
| Detect S3 public | CloudTrail ❌ | Config ✅ |
| Fix automatically | Config alone ❌ | Config + SSM ✅ |

---

# 🧪 15. Practice (Exam Style)

---

### ❓ Q1

Check if S3 bucket is public

✅ AWS Config

---

### ❓ Q2

Track security group changes over time

✅ AWS Config

---

### ❓ Q3

Prevent public access

❌ Config

✅ IAM / Bucket Policy

---

### ❓ Q4

Auto-disable old IAM keys

✅ Config + SSM

---

# 🚀 FINAL RECAP (ULTRA SIMPLE)

> **Config = Track + Check + Alert + Fix (optional)**
> 

---

## 🔥 One Line Memory

> “Detect → Evaluate → Alert → Remediate”
> 

---

## 🚀 If you want next (HIGH VALUE)

I can explain:

- Config vs Security Hub vs GuardDuty 🔥
- Real architecture (Config + EventBridge + Lambda)
- 20 tricky exam MCQs

Just tell me 👍
