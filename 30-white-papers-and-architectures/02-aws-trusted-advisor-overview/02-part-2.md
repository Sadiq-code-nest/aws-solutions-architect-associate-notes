# 11. AWS Well-Architected Tool

This is where students commonly confuse the **Framework** and **Tool**.

### Framework

The **knowledge/best-practice guidelines**.

### Tool

The **AWS service/tool that helps you review your actual workload**.

Think:

```
Well-Architected Framework
          ↓
    Best practices
          ↓
Well-Architected Tool
          ↓
Review your workload
          ↓
Identify improvement areas
```

### How it works

You select a workload.

Then answer questions related to the six pillars.

```
Your workload
      ↓
Well-Architected Tool
      ↓
Answer questions
      ↓
6 Pillars
      ↓
Identify risks
      ↓
Recommendations
```

It can provide:

- Questions
- Best-practice guidance
- Recommendations
- Documentation/videos
- Reports
- Identified risks

### Exam scenario

> A company wants to review its application's architecture against AWS best practices and identify areas that need improvement.
> 

### Answer

**AWS Well-Architected Tool**

### Memory trick

> **Framework = book/rules**
> 
> 
> **Tool = review/checklist**
> 

---

# 12. AWS Trusted Advisor

Now the important difference.

### Simple meaning

> **Trusted Advisor analyzes your AWS environment/account and gives recommendations for improving it.**
> 

Think of it as:

> **AWS account health advisor**
> 

It can identify things such as:

```
💰 Cost problems
⚡ Performance problems
🔐 Security problems
🛡️ Fault tolerance problems
📊 Service limit issues
⚙️ Operational Excellence
```

---

# 13. Trusted Advisor — 6 Categories

| Category | Example |
| --- | --- |
| **Cost Optimization** | Underutilized resources |
| **Performance** | Resource/performance issues |
| **Security** | Publicly accessible resources |
| **Fault Tolerance** | Missing redundancy |
| **Service Limits** | Approaching service quotas |
| **Operational Excellence** | Operational best-practice recommendations |

### Memory

**C P S F S O**

> **Cost → Performance → Security → Fault tolerance → Service limits → Operational Excellence**
> 

---

# 14. Trusted Advisor Example

Imagine your AWS account contains:

```
EC2 instance
   ↓
Only 5% CPU utilization
   ↓
Running 24/7
```

Trusted Advisor can identify potential **cost optimization** opportunities.

Another example:

```
EBS Snapshot
     ↓
Publicly accessible
```

Trusted Advisor can flag this as a **security** concern.

Another:

```
AWS service usage
       ↓
Very close to quota
       ↓
Service Limits
```

Trusted Advisor can warn you.

---

# 15. Well-Architected vs Trusted Advisor

This is one of the most useful exam tables.

| Feature | Well-Architected Framework | Well-Architected Tool | Trusted Advisor |
| --- | --- | --- | --- |
| What is it? | Best-practice framework | Architecture review tool | AWS account recommendation service |
| Main purpose | Learn how to architect | Review your workload | Find account/resource issues |
| Focus | Architecture principles | Your workload | AWS environment |
| 6 pillars | ✅ | ✅ | ❌ Not the same six-pillar framework |
| Reviews architecture | Conceptually | ✅ | Not its primary purpose |
| Cost recommendations | Guidelines | Architecture review | ✅ |
| Security recommendations | Guidelines | Architecture review | ✅ |
| Performance recommendations | Guidelines | Architecture review | ✅ |
| Service limits | ❌ | ❌ | ✅ |
| Account-level checks | ❌ | ❌ | ✅ |
| Fault tolerance checks | Guidelines | Architecture review | ✅ |
| Operational Excellence | ✅ | ✅ | ✅ |
| Best mental model | **Rules** | **Architecture review** | **Account health check** |

---

# 16. The Easiest Way to Remember

Imagine you are building a restaurant.

### Well-Architected Framework

The **restaurant construction/design rulebook**.

> "A good restaurant should have safe exits, efficient kitchen design, good security, reasonable operating costs, etc."
> 

### Well-Architected Tool

An **architect comes and reviews your restaurant design**.

> "Your kitchen design has these risks."
> 

### Trusted Advisor

A **business consultant checks your actual restaurant operations**.

> "You're wasting electricity."
> 
> 
> "This equipment is underutilized."
> 
> "Your security process has a problem."
> 
> "You're close to a capacity limit."
> 

---

# 17. SAA-C03 Scenario Recognition

| Question says... | Think |
| --- | --- |
| "Follow AWS architectural best practices" | **Well-Architected Framework** |
| "Review workload against AWS best practices" | **Well-Architected Tool** |
| "Review architecture using six pillars" | **Well-Architected Tool** |
| "Find security vulnerabilities/misconfigurations in AWS account" | **Trusted Advisor** |
| "Identify underutilized resources to reduce costs" | **Trusted Advisor** |
| "Check service quotas/limits" | **Trusted Advisor** |
| "Improve fault tolerance based on account resources" | **Trusted Advisor** |
| "Reduce AWS costs based on current environment" | **Trusted Advisor** |
| "Architecture design principle" | **Well-Architected Framework** |

---

# 18. Don't Confuse These

### ❌ Trusted Advisor ≠ Well-Architected Tool

Trusted Advisor:

```
AWS Account
    ↓
Check environment
    ↓
Recommendations
```

Well-Architected Tool:

```
Your Workload
    ↓
Answer architecture questions
    ↓
6 Pillars
    ↓
Identify architectural risks
```

---

# 19. SAA-C03 Exam Example

### Question

A company wants to evaluate its application architecture using AWS best practices. The company wants to answer questions across the six pillars and receive recommendations for improving its architecture.

**Answer: AWS Well-Architected Tool**

Why?

Because the keywords are:

> **application architecture + six pillars + review + best practices**
> 

---

### Another question

A company wants to identify underutilized EC2 instances, security weaknesses, fault-tolerance issues, and AWS service quota concerns within its AWS account.

**Answer: AWS Trusted Advisor**

Keywords:

> **AWS account + recommendations + cost/security/fault tolerance/service limits**
> 

---

# 20. Final SAA-C03 Cheat Sheet

```
WELL-ARCHITECTED FRAMEWORK
        │
        └── How should I architect?
                │
                ├── Operational Excellence
                ├── Security
                ├── Reliability
                ├── Performance Efficiency
                ├── Cost Optimization
                └── Sustainability

WELL-ARCHITECTED TOOL
        │
        └── Review MY WORKLOAD
                │
                └── Against the 6 pillars

TRUSTED ADVISOR
        │
        └── Check MY AWS ACCOUNT
                │
                ├── Cost
                ├── Performance
                ├── Security
                ├── Fault Tolerance
                ├── Service Limits
                └── Operational Excellence
```

### 🔥 One-line memory trick

> **Framework = "How should I build?"**
> 
> 
> **Tool = "How good is my architecture?"**
> 
> **Trusted Advisor = "What's wrong/opportunistic in my AWS account?"**
> 

### SAA-C03 takeaway

**When you see "six pillars / architecture review" → Well-Architected Tool.**

**When you see "AWS account checks / cost / security / quotas / recommendations" → Trusted Advisor.**

**When you see general AWS architectural principles → Well-Architected Framework.**
