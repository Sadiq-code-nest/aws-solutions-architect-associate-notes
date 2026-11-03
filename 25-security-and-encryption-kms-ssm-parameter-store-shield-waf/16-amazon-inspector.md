# Amazon Inspector

## 1. Meaning

**Amazon Inspector is an automated vulnerability-scanning service that checks your EC2 instances, container images, and Lambda functions for security weaknesses.**

Think:

> **Inspector = "Find vulnerabilities in my workloads." 🔍**
> 

---

## 2. Definition

Amazon Inspector continuously scans supported workloads for things such as:

- **Software/package vulnerabilities** using a database of known **CVEs**
- **Network reachability issues** for EC2
- Vulnerabilities in **container images stored in Amazon ECR**
- Vulnerabilities in **Lambda functions and their package dependencies**

Inspector gives vulnerabilities a **risk score** so you can prioritise the most dangerous findings first.

It can also send findings to:

```
Amazon Inspector
       ↓
Security Hub
       ↓
EventBridge
       ↓
Lambda / other targets
```

---

# 3. What Does the Inspector Evaluate? ⭐⭐⭐⭐⭐

For SAA-C03, remember these **three**:

```
┌──────────────────────────────┐
│       Amazon Inspector       │
├──────────────────────────────┤
│  1. EC2 Instances             │
│  2. ECR Container Images      │
│  3. Lambda Functions          │
└──────────────────────────────┘
```

### EC2

Inspector can find:

- Known software/package vulnerabilities
- Network reachability problems

### ECR

When container images are pushed to Amazon ECR, Inspector can scan them for vulnerabilities.

### Lambda

The inspector can identify vulnerabilities in:

- Function code
- Package dependencies

### 🧠 Exam memory:

> **Inspector = EC2 + ECR + Lambda**
> 

---

# 4. Real Example — EC2 ⭐⭐⭐⭐⭐

Imagine you have an EC2 server:

```
EC2
 │
 ├── Linux OS
 ├── nginx
 ├── Python
 └── Other packages
```

One of the installed packages has a **known CVE vulnerability**.

You may not realise it.

inspector continuously scans the environment:

```
EC2
 ↓
Amazon Inspector
 ↓
Finds vulnerable package 🚨
 ↓
Risk score
 ↓
Security team fixes it
```

So instead of manually checking every EC2 server for vulnerabilities, Inspector continuously assesses them.

---

# 5. Real Example — Container Image ⭐⭐⭐⭐⭐

Suppose your DevOps pipeline builds a Docker image:

```
Developer
    ↓
Docker build
    ↓
Container Image
    ↓
Amazon ECR
```

The image contains a package with a known vulnerability.

Inspector scans the image:

```
ECR Image
    ↓
Amazon Inspector
    ↓
Known CVE found 🚨
    ↓
Finding + Risk Score
```

This is useful because you can discover vulnerabilities **before running that container in production**.

### 🧠 Remember:

> **ECR + vulnerability scanning → Inspector**
> 

---

# 6. Real Example — Lambda ⭐⭐⭐⭐⭐

Suppose your Lambda function uses a third-party package:

```
Lambda
 │
 ├── Your code
 └── Third-party dependency
          ↓
     Vulnerable package
```

Inspector can identify vulnerabilities in the function's code/package dependencies.

```
Lambda
   ↓
Inspector
   ↓
Vulnerable dependency 🚨
   ↓
Finding
```

So Inspector isn't only for servers.

It also checks **Lambda functions**.

---

# 7. Network Reachability ⭐⭐⭐⭐⭐

For EC2, Inspector can also analyze **network reachability**.

Imagine:

```
Internet
   │
   ▼
Security Group
   │
   ▼
EC2
```

Your EC2 accidentally becomes reachable from an unintended network path.

Inspector can identify network reachability issues.

Think:

> **"Can an attacker potentially reach this EC2 because of my network configuration?"**
> 

That's an important Inspector capability.

---

# 8. Inspector + Security Hub

Inspector findings can integrate with **AWS Security Hub**.

Example:

```
EC2
 ↓
Inspector
 ↓
Vulnerability found
 ↓
Security Hub
 ↓
Central security dashboard
```

This is useful when an organization wants to collect security findings from multiple AWS security services in one place.

---

# 9. Inspector + EventBridge

Inspector findings can also be sent to **Amazon EventBridge**.

Example:

```
Inspector
   ↓
Critical vulnerability found
   ↓
EventBridge
   ↓
Lambda
   ↓
Automated response
```

So you can build automated workflows around vulnerability findings.

---

# 10. Inspector vs GuardDuty ⭐⭐⭐⭐⭐

This is **very important for SAA-C03**.

### Amazon Inspector

Asks:

> **"Does my workload have a vulnerability?"**
> 

Example:

```
EC2
 ↓
Old vulnerable package
 ↓
Inspector 🚨
```

### Amazon GuardDuty

Asks:

> **"Is something suspicious or malicious happening?"**
> 

Example:

```
EC2
 ↓
Suspicious network activity
 ↓
GuardDuty 🚨
```

### 🧠 Easy memory:

> **Inspector → Vulnerability**
> 

> **GuardDuty → Threat**
> 

---

# 11. Inspector vs Macie

Another common confusion:

### Inspector

Checks:

> **EC2 / ECR / Lambda vulnerabilities**
> 

### Macie

Checks:

> **Sensitive data in Amazon S3**
> 

Example:

```
EC2 vulnerable?
→ Inspector

Suspicious AWS activity?
→ GuardDuty

Sensitive data in S3?
→ Macie
```

---

# 12. Continuous Scanning ⭐⭐⭐⭐⭐

Inspector is designed for **continuous vulnerability assessment**.

You don't simply run one scan and forget about it.

For example:

```
Monday
EC2 → No critical vulnerability ✅

Tuesday
New vulnerable package appears
        ↓
Inspector detects it 🚨

Wednesday
Package updated
        ↓
Inspector reassesses
```

This makes Inspector useful for continuously changing environments.

---

# 13. Risk Score

Inspector associates vulnerabilities with a **risk score**.

Imagine you have:

```
100 findings
      ↓
Inspector
      ↓
Critical → 9.8 🚨
High     → 8.1
Medium   → 5.2
Low      → 2.1
```

Your security team can prioritize the highest-risk vulnerabilities first.

### 🧠 Remember:

> **Inspector = Find vulnerability + prioritize it with risk score**
> 

---

# 14. SAA-C03 Exam Recognition ⭐⭐⭐⭐⭐

If you see:

> **"Continuously scan EC2 for software vulnerabilities."**
> 

✅ **Amazon Inspector**

---

> **"Scan container images in Amazon ECR for known vulnerabilities."**
> 

✅ **Amazon Inspector**

---

> **"Scan Lambda functions for vulnerabilities in dependencies."**
> 

✅ **Amazon Inspector**

---

> **"Use a database of known CVEs to identify vulnerable packages."**
> 

✅ **Amazon Inspector**

---

> **"Analyze network reachability of EC2 instances."**
> 

✅ **Amazon Inspector**

---

> **"Automatically assess workloads for vulnerabilities."**
> 

✅ **Amazon Inspector**

---

> **"Send Inspector findings to Security Hub."**
> 

✅ **Inspector → Security Hub**

---

> **"Trigger an automated action when Inspector finds a vulnerability."**
> 

✅ **Inspector → EventBridge → Lambda**

---

# 🧠 Final Memory Trick

Think of Inspector as a **security inspector visiting your AWS workloads**:

```
                AMAZON INSPECTOR 🔍
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
         EC2          ECR         Lambda
          │            │            │
      Packages      Images      Dependencies
          │            │            │
          └────────────┼────────────┘
                       ▼
                Known CVEs
                       +
              Network Reachability
                       ↓
                  Risk Score
                       ↓
                    Finding
```

### ⭐ One-line exam memory:

> **Amazon Inspector = Continuous vulnerability scanning for EC2, ECR container images, and Lambda.**
> 

And keep this distinction locked in:

```
Inspector  → Vulnerabilities 🔍
GuardDuty  → Threats 🚨
Macie      → Sensitive S3 data 🔐
```
