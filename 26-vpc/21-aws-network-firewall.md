# AWS Network Protection — SAA-C03 Complete Exam Note

## 1. The Big Picture

AWS gives you multiple layers of network protection:

```
                         INTERNET
                             │
                    ┌────────▼────────┐
                    │   AWS Shield    │
                    │ DDoS Protection │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │      AWS WAF    │
                    │ HTTP/HTTPS      │
                    │ Layer 7         │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │ VPC / Subnets   │
                    │                 │
                    │ SG → EC2        │
                    │ NACL → Subnet   │
                    │                 │
                    │ Network Firewall │
                    │ Whole VPC       │
                    └──────────────────┘
```

Think of them as **different security layers**:

| Service | Protects | Main Purpose |
| --- | --- | --- |
| **Security Group** | EC2/ENI | Instance-level firewall |
| **NACL** | Subnet | Subnet-level firewall |
| **AWS WAF** | Web applications | HTTP/HTTPS request filtering |
| **AWS Shield** | AWS applications/resources | DDoS protection |
| **AWS Network Firewall** | Entire VPC/network traffic | Advanced network inspection |
| **Firewall Manager** | Multiple AWS accounts/resources | Centrally manage security rules |

---

# 2. Security Group

### Simple meaning

A **Security Group (SG)** is a virtual firewall attached to an EC2 instance/ENI.

It controls:

```
Internet
   │
   ▼
Security Group
   │
   ▼
EC2
```

### Key characteristics

- Works at **instance/ENI level**
- **Stateful**
- Supports **ALLOW rules only**
- Return traffic is automatically allowed
- Rules are evaluated together
- No explicit DENY rule

### Example

You have a web server:

```
Internet
   │
   │ TCP 443
   ▼
┌─────────────┐
│ Security    │
│ Group       │
│ ALLOW 443   │
└──────┬──────┘
       ▼
     EC2
```

You allow HTTPS 443.

When the EC2 sends a response, you **don't need another outbound rule specifically for the response** because SG is stateful.

### Exam clue

> "Instance-level firewall"
> 

→ **Security Group**

---

# 3. Network ACL

A **Network Access Control List (NACL)** is a firewall applied at the **subnet level**.

```
                 VPC
                  │
        ┌─────────▼─────────┐
        │      Subnet       │
        │                   │
        │      NACL         │
        │        │          │
        │    ┌───▼───┐      │
        │    │ EC2   │      │
        │    └───────┘      │
        └───────────────────┘
```

### Key characteristics

- Works at **subnet level**
- **Stateless**
- Supports **ALLOW and DENY**
- Rules evaluated from **lowest number → highest**
- **First matching rule wins**
- Automatically applies to all resources in the subnet
- Return traffic must be explicitly permitted

### Exam clue

> "Block a specific IP address at subnet level"
> 

→ **NACL**

---

# 4. Security Group vs NACL

| Feature | Security Group | NACL |
| --- | --- | --- |
| Level | EC2/ENI | Subnet |
| Stateful? | ✅ Yes | ❌ No |
| Allow rules | ✅ | ✅ |
| Deny rules | ❌ | ✅ |
| Rule evaluation | All rules | Lowest number first |
| First match wins? | ❌ | ✅ |
| Return traffic | Automatically allowed | Must explicitly allow |
| Applies automatically to subnet? | ❌ | ✅ |
| Best for | Instance protection | Subnet/IP blocking |
| Typical clue | "EC2 firewall" | "Block IP/subnet traffic" |

### Memory trick

**SG = Stateful + Server**

**NACL = Network + Numbered + No state**

---

# 5. AWS WAF

**AWS WAF = Web Application Firewall**

WAF protects **web applications** from malicious HTTP/HTTPS requests.

Think:

```
User
 │
 ▼
CloudFront / ALB / API Gateway
 │
 ▼
   WAF
 │
 ├── Allow
 ├── Block
 └── Count
 │
 ▼
Application
```

### What can WAF protect against?

Examples:

- SQL injection
- Cross-site scripting (XSS)
- Malicious HTTP requests
- IP-based filtering
- Bad bots
- Request patterns
- Geographic restrictions
- Rate-based attacks

### Exam clue

If the question says:

> "Protect a web application from SQL injection"
> 

→ **AWS WAF**

If it says:

> "Block requests from a specific IP"
> 

→ **AWS WAF** can do this for web requests.

---

# 6. AWS Shield

AWS Shield provides **DDoS protection**.

There are two levels:

|  | Shield Standard | Shield Advanced |
| --- | --- | --- |
| Cost | Included | Paid |
| DDoS protection | ✅ | ✅ Advanced |
| Protection level | Standard | Advanced |
| Best for | Basic protection | Critical applications |
| Visibility/response | Basic | More advanced |

### Memory trick

**Shield = Protect against DDoS**

Don't confuse:

```
WAF    → malicious WEB requests
Shield → DDoS attacks
```

---

# 7. AWS Network Firewall

This is the major concept from your note.

### Simple meaning

**AWS Network Firewall is a managed, advanced network firewall that protects traffic throughout a VPC.**

Unlike an SG, which is primarily associated with an ENI/instance, Network Firewall can inspect traffic flowing through your **VPC network architecture**.

---

# 8. What Can Network Firewall Protect?

It can inspect traffic such as:

```
                    INTERNET
                       │
                       ▼
                AWS Network Firewall
                       │
                       ▼
                      VPC
                   /       \
                  /         \
              EC2           EC2
```

It can also inspect traffic involving:

### Internet

```
Internet
   ↓
Network Firewall
   ↓
VPC
```

### VPC → Internet

```
VPC
 ↓
Network Firewall
 ↓
Internet
```

### VPC → VPC

```
VPC-A
  │
  ▼
Network Firewall
  │
  ▼
VPC-B
```

### Direct Connect

```
Corporate DC
      │
      ▼
Direct Connect
      │
      ▼
Network Firewall
      │
      ▼
     VPC
```

### Site-to-Site VPN

```
On-Premises
     │
     ▼
Site-to-Site VPN
     │
     ▼
Network Firewall
     │
     ▼
    VPC
```

---

# 9. Layer 3 → Layer 7 Protection

This is an important distinction.

Network Firewall can inspect traffic from:

**Layer 3 → Layer 7**

Meaning it can inspect things such as:

- IP addresses
- Ports
- Protocols
- Network traffic patterns
- Application-level patterns
- Domains
- More sophisticated traffic rules

### Example

Suppose your company wants:

> "Allow employees to access only `mycompany.com` and our approved software repository, while blocking other outbound traffic."
> 

This is much more sophisticated than simply saying:

```
Allow TCP 443
```

Network Firewall can use **stateful domain list rules** and other advanced inspection capabilities.

---

# 10. Network Firewall Fine-Grained Controls

Network Firewall supports sophisticated rules.

### IP + Port

Example:

```
Block:
10.0.5.10 → TCP → 22
```

### Protocol

Example:

> Block SMB traffic leaving the VPC.
> 

### Domain filtering

Example:

```
ALLOW:
*.mycompany.com
*.approved-repo.com

BLOCK:
everything else
```

### Regex / pattern matching

Can perform more advanced pattern matching.

### Traffic actions

Rules can:

- **Allow**
- **Drop**
- **Alert**

---

# 11. Intrusion Prevention

Network Firewall provides **active flow inspection** and intrusion-prevention capabilities.

Think:

```
Traffic
   │
   ▼
Network Firewall
   │
   ├── Normal → ALLOW
   │
   ├── Suspicious → ALERT
   │
   └── Malicious → DROP
```

This is why Network Firewall is much more advanced than a simple SG/NACL.

---

# 12. AWS Network Firewall + Gateway Load Balancer

An important architecture concept:

AWS Network Firewall internally uses **AWS Gateway Load Balancer technology**.

For SAA-C03, remember:

> **Network Firewall = AWS-managed advanced VPC firewall**
> 

> **Gateway Load Balancer = commonly used to deploy/scale third-party virtual network appliances**
> 

Don't confuse the two.

---

# 13. Network Firewall vs Security Group vs NACL

| Feature | Security Group | NACL | Network Firewall |
| --- | --- | --- | --- |
| Scope | EC2/ENI | Subnet | VPC/network traffic |
| Stateful | ✅ | ❌ | ✅ Stateful inspection capabilities |
| Allow | ✅ | ✅ | ✅ |
| Deny | ❌ | ✅ | ✅ |
| IP filtering | ✅ | ✅ | ✅ |
| Port filtering | ✅ | ✅ | ✅ |
| Advanced inspection | Limited | Limited | ✅ |
| Domain rules | ❌ | ❌ | ✅ |
| Regex/pattern matching | ❌ | ❌ | ✅ |
| Intrusion prevention | ❌ | ❌ | ✅ |
| HTTP application filtering | ❌ | ❌ | Can inspect advanced traffic |
| Best use | EC2 firewall | Subnet/IP firewall | Advanced VPC firewall |

### Memory trick

```
SG       → Server
NACL     → Network/Subnet
WAF      → Web
Shield   → DDoS
Network Firewall → Whole network
```

---

# 14. AWS Firewall Manager

Now imagine you have:

```
AWS Organization
│
├── Account A
│   └── VPC
│
├── Account B
│   └── VPC
│
├── Account C
│   └── VPC
│
└── Account D
    └── VPC
```

You don't want to manually configure security policies in every account.

That's where **AWS Firewall Manager** comes in.

### Simple meaning

> Centrally manage firewall/security policies across multiple AWS accounts and resources.
> 

It can centrally manage policies involving services such as:

- AWS WAF
- AWS Shield Advanced
- AWS Network Firewall
- Security Groups
- Amazon Route 53 Resolver DNS Firewall

### Exam clue

> "Centralized security management across multiple AWS accounts"
> 

→ **AWS Firewall Manager**

---

# 15. Network Protection Service Selection

This is probably the **most important exam table**.

| Question says... | Think |
| --- | --- |
| Protect EC2 instance | **Security Group** |
| Instance-level firewall | **Security Group** |
| Stateful firewall | **Security Group** |
| Block specific IP at subnet level | **NACL** |
| Need explicit DENY rules | **NACL** |
| Stateless firewall | **NACL** |
| Protect website from SQL injection | **AWS WAF** |
| Filter HTTP/HTTPS requests | **AWS WAF** |
| Rate-limit web requests | **AWS WAF** |
| Protect against DDoS | **AWS Shield** |
| Advanced DDoS protection | **Shield Advanced** |
| Advanced firewall for entire VPC | **AWS Network Firewall** |
| Inspect VPC traffic | **AWS Network Firewall** |
| Domain-based outbound filtering | **AWS Network Firewall** |
| Intrusion prevention | **AWS Network Firewall** |
| Central security policies across accounts | **AWS Firewall Manager** |

---

# 16. Real-World Architecture Example

Imagine an online banking application.

```
                         INTERNET
                            │
                            ▼
                     ┌─────────────┐
                     │   Shield    │
                     │ DDoS        │
                     └──────┬──────┘
                            │
                            ▼
                     ┌─────────────┐
                     │     WAF     │
                     │ SQLi / XSS  │
                     │ Rate limits │
                     └──────┬──────┘
                            │
                            ▼
                    Application Load
                       Balancer
                            │
                            ▼
                  ┌──────────────────┐
                  │       VPC        │
                  │                  │
                  │ Network Firewall │
                  │ Advanced traffic│
                  │ inspection       │
                  │                  │
                  │   ┌──────────┐   │
                  │   │   NACL   │   │
                  │   │  Subnet  │   │
                  │   └────┬─────┘   │
                  │        │         │
                  │   ┌────▼─────┐   │
                  │   │    SG    │   │
                  │   │   EC2    │   │
                  │   └──────────┘   │
                  └──────────────────┘
```

Each layer has a different responsibility:

**Shield**

→ "Is someone trying to overwhelm my application with DDoS?"

**WAF**

→ "Is this HTTP request malicious?"

**Network Firewall**

→ "Is this network traffic allowed to travel through my VPC?"

**NACL**

→ "Should this subnet accept this IP/port traffic?"

**Security Group**

→ "Should this EC2 instance accept this connection?"

**Firewall Manager**

→ "How do I enforce these security policies across many AWS accounts?"

---

# 17. Network Firewall + Firewall Manager

For a large organization:

```
                    AWS Organization
                           │
                   Firewall Manager
                           │
        ┌──────────────────┼──────────────────┐
        ▼                  ▼                  ▼
     Account A          Account B          Account C
        │                  │                  │
        ▼                  ▼                  ▼
      VPC-A              VPC-B              VPC-C
        │                  │                  │
 Network Firewall    Network Firewall    Network Firewall
```

Instead of manually creating and maintaining firewall policies in every account, **Firewall Manager centrally manages them**.

### Exam clue

**"Across multiple accounts" + "centralized firewall/security policies"**

→ **Firewall Manager**

---

# 18. Network Firewall Logging

Network Firewall can send rule-match logs to:

- **Amazon S3**
- **CloudWatch Logs**
- **Kinesis Data Firehose**

Example:

```
Network Firewall
       │
       ├── S3
       ├── CloudWatch Logs
       └── Kinesis Data Firehose
```

You can then analyze these logs for:

- Threats
- Blocked traffic
- Allowed traffic
- Suspicious activity
- Security investigations

---

# 19. Network Firewall vs WAF — VERY IMPORTANT

These two are easy to confuse.

|  | Network Firewall | WAF |
| --- | --- | --- |
| Main target | Network/VPC traffic | Web requests |
| Layer | L3-L7 | Primarily L7 |
| Protects | VPC/network | Web applications |
| SQL Injection | Can inspect advanced traffic, but **WAF is the usual exam answer** | ✅ Primary choice |
| XSS | ❌ Not the primary choice | ✅ |
| Domain filtering | ✅ | Different purpose |
| IP/port filtering | ✅ | Can filter web requests by IP |
| Intrusion prevention | ✅ | ❌ |
| HTTP request filtering | Not its primary role | ✅ |
| VPC-wide advanced inspection | ✅ | ❌ |
| Typical exam wording | "VPC traffic", "network inspection" | "web application", "HTTP request" |

### Easy distinction

> **WAF protects the WEBSITE.**
> 

> **Network Firewall protects the NETWORK.**
> 

---

# 20. Network Firewall vs Shield

|  | Network Firewall | Shield |
| --- | --- | --- |
| Main purpose | Network traffic inspection | DDoS protection |
| Protects against | Network threats/malicious traffic | DDoS attacks |
| VPC traffic inspection | ✅ | ❌ |
| DDoS protection | Not its primary purpose | ✅ |
| Domain filtering | ✅ | ❌ |
| Intrusion prevention | ✅ | ❌ |
| Think | Firewall | DDoS shield |

### Memory

**Shield = DDoS**

---

# 21. The SAA-C03 Decision Tree

When you see a network-security question, think:

```
              What needs protection?
                       │
          ┌────────────┼────────────┐
          │            │            │
        EC2          Subnet        Web App
          │            │            │
          ▼            ▼            ▼
         SG           NACL         WAF

                       │
                       ▼
                  DDoS attack?
                       │
                       ▼
                    Shield

                       │
                       ▼
              Entire VPC / network
               advanced inspection?
                       │
                       ▼
               Network Firewall

                       │
                       ▼
             Multiple AWS accounts?
                       │
                       ▼
               Firewall Manager
```

---

# 22. Exam Traps

### Trap 1

> "Block a specific IP address."
> 

Don't automatically choose WAF.

Ask **where?**

- Web request → **WAF**
- Subnet/network level → **NACL**
- EC2 instance → **Security Group cannot explicitly DENY**

---

### Trap 2

> "DDoS attack"
> 

→ **Shield**

Not Network Firewall.

---

### Trap 3

> "SQL injection"
> 

→ **WAF**

Because SQL injection is a **web application attack**.

---

### Trap 4

> "Advanced traffic inspection across the VPC"
> 

→ **Network Firewall**

---

### Trap 5

> "Centralize firewall policies across AWS accounts"
> 

→ **Firewall Manager**

---

# 23. One-Minute Revision

| Service | Remember this |
| --- | --- |
| **Security Group** | 🖥️ EC2 firewall |
| **NACL** | 🏠 Subnet firewall |
| **WAF** | 🌐 Web application firewall |
| **Shield** | 🛡️ DDoS protection |
| **Network Firewall** | 🔥 Advanced VPC/network firewall |
| **Firewall Manager** | 🏢 Centralized security across accounts |

## The ultimate memory line

> **SG protects the Server → NACL protects the Subnet → WAF protects the Website → Shield protects from DDoS → Network Firewall protects the Network → Firewall Manager manages them across accounts.**
> 

That single line is worth memorizing for **SAA-C03**.
