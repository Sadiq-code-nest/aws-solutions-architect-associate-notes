# 2 - Common confusion Table

Absolutely — this is a **high-value AWS SAA-C03 confusion table**. The easiest way to separate them is to ask: **“What exactly is this service trying to protect, detect, inspect, discover, aggregate, or prove?”**

### AWS Security Services — Common Confusion Master Table

| Service | **Core Job — Think This** | What does it actually do? | Protects / Works On | Example Scenario | **Exam Keyword** |
| --- | --- | --- | --- | --- | --- |
| **AWS Firewall Manager** | 🏢 **Central management** | Centrally configures and manages security policies across **multiple AWS accounts and VPCs** | WAF, Shield Advanced, Network Firewall, security groups, etc. | Company has 50 AWS accounts and wants the same WAF rules everywhere | **Centralized security across accounts** |
| **Amazon GuardDuty** | 🕵️ **Threat detection** | Continuously analyses AWS data sources and detects **suspicious/malicious activity** | AWS accounts, workloads, identities, network activity | EC2 starts communicating with a known malicious IP → GuardDuty generates a finding | **Threat detection** |
| **Amazon Inspector** | 🔍 **Vulnerability scanning** | Automatically scans supported workloads for **software vulnerabilities and unintended network exposure** | EC2, ECR container images, Lambda | EC2 is running an OS package with a known CVE → Inspector reports vulnerability | **Vulnerability assessment** |
| **Amazon Macie** | 🗂️ **Sensitive data discovery** | Uses ML/pattern matching to discover and classify **sensitive data** in S3 | **S3 data** | Finds credit-card numbers or personally identifiable information in an S3 bucket | **Sensitive data in S3** |
| **AWS Security Hub** | 📊 **Security findings dashboard** | Aggregates and prioritizes security findings from multiple AWS security services and supported partners | AWS security posture/findings | GuardDuty + Inspector + Macie produce findings → Security Hub gives one central view | **Central security findings** |
| **AWS Artifact** | 📜 **Compliance documents** | Provides access to AWS **compliance reports, certifications, agreements and related documents** | AWS compliance documentation | Auditor asks for AWS SOC/ISO compliance documentation → retrieve it from Artifact | **Compliance reports/documents** |
| **AWS Audit Manager** | 📋 **Audit evidence collection** | Automatically collects and organizes evidence to help you **prepare for audits** | Your AWS environment / workloads | Company needs evidence for a compliance audit → Audit Manager continuously gathers relevant evidence | **Audit preparation/evidence** |
| **AWS WAF** | 🛡️ **Web request filtering** | Filters malicious **HTTP/HTTPS requests** using rules | Web applications, ALB, CloudFront, API Gateway, AppSync | Block SQL injection, XSS, specific IPs, countries, bad request patterns | **Layer 7 / web attacks** |
| **AWS Shield** | 🌊 **DDoS protection** | Protects applications against **Distributed Denial-of-Service attacks** | AWS applications/resources | Attacker sends huge volumes of traffic to overwhelm your public application | **DDoS protection** |
| **AWS Network Firewall** | 🧱 **VPC network firewall** | Provides sophisticated **network traffic inspection/filtering** across your VPC | VPC traffic, inbound/outbound, VPC-to-VPC, VPN/DX traffic | Block specific IPs, ports, protocols or domains across VPC traffic | **VPC Layer 3–7 firewall** |

---

## 🧠 The Ultimate Separation Trick

Memorize this chain:

> **WAF = Web requests**
> 
> 
> **Shield = DDoS**
> 
> **Network Firewall = VPC network traffic**
> 
> **GuardDuty = Threats**
> 
> **Inspector = Vulnerabilities**
> 
> **Macie = Sensitive S3 data**
> 
> **Security Hub = Findings dashboard**
> 
> **Firewall Manager = Central management**
> 
> **Artifact = AWS compliance documents**
> 
> **Audit Manager = Your audit evidence**
> 

That's the core distinction.

---

## 🔥 Most Important Confusions

| If the question says... | Think... | NOT... |
| --- | --- | --- |
| **SQL injection / XSS / malicious HTTP request** | **WAF** | Shield |
| **DDoS attack** | **Shield** | WAF |
| **Block IP/port/protocol across VPC traffic** | **Network Firewall** | WAF |
| **Suspicious API activity / compromised credentials / malicious IP** | **GuardDuty** | Inspector |
| **Known CVE / vulnerable package / software vulnerability** | **Inspector** | GuardDuty |
| **Credit card / PII / sensitive information in S3** | **Macie** | Inspector |
| **Combine findings from GuardDuty, Inspector, Macie** | **Security Hub** | Firewall Manager |
| **Apply WAF/Shield/Network Firewall policies across many accounts** | **Firewall Manager** | Security Hub |
| **Download AWS SOC/ISO/compliance reports** | **Artifact** | Audit Manager |
| **Prepare audit evidence from your AWS environment** | **Audit Manager** | Artifact |

---

# GuardDuty vs Inspector vs Macie

This is probably the **most important 3-way distinction**:

|  | **GuardDuty** | **Inspector** | **Macie** |
| --- | --- | --- | --- |
| Main question | **Is something suspicious happening?** | **Is my workload vulnerable?** | **Does my S3 contain sensitive data?** |
| Purpose | Threat detection | Vulnerability management | Data security |
| Looks for | Threats / malicious activity | CVEs / vulnerabilities / exposure | PII / sensitive data |
| Main target | AWS accounts/workloads | EC2, ECR, Lambda | **S3** |
| Example | EC2 communicating with malicious IP | EC2 has vulnerable package | S3 contains credit-card information |
| Remember | **ATTACK** | **WEAKNESS** | **DATA** |

### Easy mental model

```
                AWS Environment
                      │
       ┌──────────────┼──────────────┐
       │              │              │
    GuardDuty      Inspector       Macie
       │              │              │
   "Am I being      "Am I         "Do I have
    attacked?"     vulnerable?"   sensitive data?"
       │              │              │
     THREAT        VULNERABILITY     DATA
```

---

# Security Hub vs Firewall Manager

These two are often confused because both sound like **central security management**.

|  | **Security Hub** | **Firewall Manager** |
| --- | --- | --- |
| Main purpose | **Centralize security findings** | **Centrally manage security policies** |
| Question | "What security problems do I have?" | "How do I enforce security rules everywhere?" |
| Works with | GuardDuty, Inspector, Macie, etc. | WAF, Shield Advanced, Network Firewall, security groups |
| Action | Aggregates/prioritizes findings | Deploys/enforces policies |
| Example | 100 security findings across 20 accounts → one dashboard | Apply the same WAF rule to 20 AWS accounts |
| Keyword | **Findings** | **Policies** |

### Remember:

**Security Hub = See security problems**

**Firewall Manager = Manage security controls**

---

# Artifact vs Audit Manager

Another extremely common confusion:

|  | **AWS Artifact** | **AWS Audit Manager** |
| --- | --- | --- |
| Main purpose | Access **AWS compliance documents** | Collect **your audit evidence** |
| Whose compliance? | **AWS's compliance** | **Your environment's compliance** |
| Example | Get AWS SOC 2 report | Gather evidence showing your resources comply with controls |
| Think | "Show me AWS's certificates/reports" | "Help me prove my environment is compliant" |
| Keyword | **Compliance documents** | **Audit evidence** |

### One-line memory:

> **Artifact = AWS proves AWS's compliance.**
> 
> 
> **Audit Manager = You prepare evidence about your AWS environment.**
> 

---

# WAF vs Shield vs Network Firewall

|  | **WAF** | **Shield** | **Network Firewall** |
| --- | --- | --- | --- |
| Main protection | Web application | DDoS | VPC network |
| Layer focus | **Layer 7** | DDoS protection | **Layer 3–7** |
| Looks at | HTTP/HTTPS requests | Attack traffic / DDoS patterns | Network traffic |
| Blocks | SQL injection, XSS, bad IPs, bad patterns | DDoS attacks | IPs, ports, protocols, domains, network threats |
| Typical resource | CloudFront, ALB, API Gateway | CloudFront, Route 53, ALB, etc. | VPC |
| Think | **Web filter** | **DDoS shield** | **VPC firewall** |

### Exam shortcut

```
SQL Injection / XSS
        ↓
      WAF

DDoS
        ↓
     Shield

VPC traffic / IP / Port / Protocol
        ↓
Network Firewall
```

---

# 🧠 Final 10-Word Memory Map

| Service | Remember |
| --- | --- |
| **Firewall Manager** | **Manage security centrally** |
| **GuardDuty** | **Detect threats** |
| **Inspector** | **Find vulnerabilities** |
| **Macie** | **Find sensitive S3 data** |
| **Security Hub** | **Aggregate security findings** |
| **Artifact** | **AWS compliance documents** |
| **Audit Manager** | **Collect audit evidence** |
| **WAF** | **Filter web requests** |
| **Shield** | **Stop DDoS** |
| **Network Firewall** | **Inspect/filter VPC traffic** |

### ⭐ SAA-C03 exam decision tree

```
                 SECURITY QUESTION
                        │
       ┌────────────────┼─────────────────┐
       │                │                 │
   Web attack?       DDoS?          VPC traffic?
       │                │                 │
      WAF             Shield       Network Firewall
       │
       └──────────────────────────────────┐
                                          │
                              Something suspicious?
                                          │
                                       GuardDuty
                                          │
                              Vulnerable software?
                                          │
                                       Inspector
                                          │
                              Sensitive S3 data?
                                          │
                                         Macie
                                          │
                              Need central findings?
                                          │
                                     Security Hub
                                          │
                              Manage across accounts?
                                          │
                                    Firewall Manager
                                          │
                              Need AWS compliance docs?
                                          │
                                       Artifact
                                          │
                              Need audit evidence?
                                          │
                                    Audit Manager
```

**If you remember only one thing for the exam:**

**GuardDuty = threat, Inspector = vulnerability, Macie = sensitive S3 data, Security Hub = findings, Firewall Manager = policies, Artifact = AWS documents, Audit Manager = your evidence, WAF = web, Shield = DDoS, Network Firewall = VPC traffic.**
