# AWS Audit Manager — SAA-C03 Exam Note

## 1. What is AWS Audit Manager?

**AWS Audit Manager** is a service that **automatically collects evidence from your AWS environment** to help you **continuously audit and demonstrate compliance** with regulatory standards and internal policies.

### Simple meaning

> **Audit Manager = Collects evidence from YOUR AWS environment for audits.**
> 

Think:

**Artifact → AWS's compliance documents**

**Audit Manager → Your AWS environment's compliance evidence**

---

## 2. What problem does it solve?

Normally, preparing for an audit can involve manually collecting evidence such as:

- CloudTrail activity
- AWS Config configuration data
- IAM information
- Security settings
- Resource configurations
- Other AWS service activity

This can take a lot of time.

**Audit Manager automates much of this evidence collection and organizes it for auditing.**

### Without Audit Manager

```
Auditor asks:
"Show me evidence that your AWS environment follows this control."

        ↓

Engineer manually searches
CloudTrail + Config + IAM + other sources

        ↓

Collect evidence

        ↓

Organize evidence

        ↓

Prepare audit report
```

### With Audit Manager

```
AWS Environment
      │
      ├── CloudTrail
      ├── AWS Config
      ├── IAM
      ├── Other AWS services
      │
      ▼
AWS Audit Manager
      │
      ▼
Automatically collect & organize evidence
      │
      ▼
Audit / Compliance assessment
```

---

# 3. What can AWS Audit Manager do?

### ① Automate evidence collection

Audit Manager collects evidence from your AWS environment based on defined controls.

For example:

> "Are security-related API activities being logged?"
> 

Audit Manager can collect relevant evidence from AWS services such as CloudTrail.

---

### ② Create assessments

You create an **assessment** based on the compliance framework you need to evaluate.

An assessment defines things such as:

- What compliance framework you're assessing
- Which AWS accounts are involved
- Which controls are evaluated
- Who is responsible for the controls

---

### ③ Use prebuilt frameworks

Audit Manager provides frameworks that can help with common compliance requirements.

Examples include frameworks related to:

- **PCI DSS**
- **SOC**
- **HIPAA**
- **GDPR**
- **ISO**
- CIS-related controls

You can also create **custom frameworks** for your organization's requirements.

---

### ④ Collect evidence continuously

Instead of waiting until audit time, Audit Manager can continuously gather evidence.

This makes it easier to answer:

> "Can you demonstrate that your AWS environment has been following this control?"
> 

---

### ⑤ Organize evidence for auditors

Audit Manager organizes collected evidence around:

**Framework → Controls → Evidence**

This makes the audit process easier to manage.

---

# 4. Important concepts

### Framework

A **framework** defines the compliance requirements you're evaluating.

Example:

```
PCI DSS Framework
       │
       ├── Control 1
       ├── Control 2
       ├── Control 3
       └── Control 4
```

---

### Control

A **control** represents a specific compliance requirement.

Example:

> "Ensure appropriate logging is enabled."
> 

Audit Manager checks/collects evidence related to that requirement.

---

### Evidence

**Evidence = information collected from your AWS environment that supports an audit/control.**

Examples:

- CloudTrail activity
- Configuration information
- AWS resource information
- User activity
- Security-related data

---

### Assessment

An **assessment** is the audit activity where you evaluate your AWS environment against a framework.

Simple mental model:

```
Framework
    ↓
Controls
    ↓
Assessment
    ↓
Evidence collection
    ↓
Audit-ready information
```

---

# 5. Real-world scenario

### 🏦 Banking company

Imagine a bank runs its application on AWS.

The bank must demonstrate compliance with **PCI DSS**.

The auditor asks:

> "Show evidence that your AWS environment follows the required security controls."
> 

Without Audit Manager:

```
Engineer
   ↓
Search CloudTrail
   ↓
Check AWS Config
   ↓
Check IAM
   ↓
Collect screenshots/logs/configuration
   ↓
Organize evidence
   ↓
Give to auditor
```

This is time-consuming.

With **AWS Audit Manager**:

```
AWS Environment
       ↓
Audit Manager
       ↓
PCI DSS framework
       ↓
Controls
       ↓
Evidence collected
       ↓
Organized audit evidence
```

The organization can use the collected evidence to support its compliance audit.

---

# 6. AWS Audit Manager vs AWS Artifact

This is **VERY IMPORTANT for SAA-C03**.

| Feature | AWS Artifact | AWS Audit Manager |
| --- | --- | --- |
| Main purpose | AWS compliance documentation | Audit your AWS environment |
| Whose compliance? | **AWS's** | **Your organization's** |
| Provides AWS compliance reports? | ✅ Yes | ❌ Not its main purpose |
| Collects evidence from your AWS environment? | ❌ | ✅ |
| Helps prepare for audits? | Provides AWS evidence | Collects/organizes your evidence |
| Example | Download AWS SOC report | Collect evidence that your environment follows controls |
| Memory | 📄 **AWS paperwork** | 🔍 **Your audit evidence** |

### Exam shortcut

> **"Download AWS compliance reports" → Artifact**
> 

> **"Collect evidence from my AWS environment for an audit" → Audit Manager**
> 

---

# 7. AWS Audit Manager vs AWS Config

Another common confusion.

| Feature | AWS Audit Manager | AWS Config |
| --- | --- | --- |
| Main purpose | Audit/compliance evidence | Resource configuration & compliance |
| Focus | **Audit process** | **Resource configuration** |
| Checks resource configuration? | Can use evidence from services | ✅ Core purpose |
| Configuration history | ❌ | ✅ |
| Compliance rules | Framework/control-oriented | Config Rules |
| Example | Prepare evidence for PCI DSS audit | Check whether S3 buckets are publicly accessible |

### Example

Suppose the requirement is:

> **"S3 buckets must not be publicly accessible."**
> 

**AWS Config** can evaluate the resource configuration against a rule.

**Audit Manager** can help collect and organize evidence relevant to an audit framework/control.

Think:

> **Config = "Is my resource configured correctly?"**
> 

> **Audit Manager = "Can I demonstrate compliance during an audit?"**
> 

---

# 8. Audit Manager vs Security Hub

| Feature | Audit Manager | Security Hub |
| --- | --- | --- |
| Main purpose | Compliance audit evidence | Centralized security findings |
| Focus | Audit/compliance | Security posture |
| Collects security findings? | Not its primary purpose | ✅ |
| Aggregates findings | ❌ | ✅ |
| Compliance frameworks | ✅ | ✅ Security standards |
| Example | Prepare audit evidence | Find and manage security issues |

### Memory

**Security Hub**

> 🛡️ "What's wrong with my security?"
> 

**Audit Manager**

> 📋 "Can I demonstrate compliance for my audit?"
> 

---

# 9. Audit Manager vs Trusted Advisor

| Service | Think |
| --- | --- |
| **Audit Manager** | 📋 Audit evidence |
| **Trusted Advisor** | 💡 Best-practice recommendations |

Example:

**Trusted Advisor:**

> "Your security group allows unrestricted access."
> 

**Audit Manager:**

> "Here is evidence relevant to your organization's audit control."
> 

---

# 10. Audit Manager vs AWS Artifact vs Config — Remember This

```
                COMPLIANCE
                    │
       ┌────────────┼────────────┐
       │            │            │
    Artifact    Audit Manager   Config
       │            │            │
       ▼            ▼            ▼
   AWS's own     YOUR AWS      Resource
   compliance    audit         configuration
   documents     evidence      compliance
```

### One-line memory trick

> **Artifact = AWS's proof** 📄
> 
> 
> **Audit Manager = Your proof** 📋
> 
> **Config = Your configuration check** 🔧
> 

---

# 11. SAA-C03 Scenario Patterns

### Scenario 1

> A company needs to download AWS compliance reports and certifications for its auditor.
> 

✅ **AWS Artifact**

---

### Scenario 2

> A company needs to continuously collect evidence from its AWS environment to demonstrate compliance with a regulatory framework.
> 

✅ **AWS Audit Manager**

---

### Scenario 3

> A company wants to determine whether its AWS resources comply with specific configuration rules.
> 

✅ **AWS Config**

---

### Scenario 4

> A company wants a centralized place to view security findings from multiple AWS security services.
> 

✅ **AWS Security Hub**

---

### Scenario 5

> A company wants recommendations about cost, security, performance, and fault tolerance.
> 

✅ **AWS Trusted Advisor**

---

# 🧠 SAA-C03 Final Takeaway

**AWS Audit Manager = automated audit evidence collection and organization.**

Remember:

> **"Audit Manager doesn't primarily secure your AWS environment — it helps you prove that your AWS environment meets audit/compliance requirements."**
> 

### 🔥 Exam keywords

If you see:

**"audit evidence"**

**"automatically collect evidence"**

**"compliance assessment"**

**"regulatory framework"**

**"prepare for an audit"**

**"PCI DSS / SOC / HIPAA / GDPR"**

**"continuously collect evidence"**

👉 **Think AWS Audit Manager.**
