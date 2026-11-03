# Blocking an IP Address in AWS — SAA-C03

This is a **very important architecture decision** because AWS gives you several ways to block traffic, but **each operates at a different layer**.

The biggest exam question is usually:

> **"Where should I block this IP?"**
> 

Think about **where the traffic enters your architecture** and **what kind of filtering you need**.

---

# 1. First Understand the Layers

```
Internet
   │
   ▼
┌───────────────┐
│   CloudFront  │  ← Edge / global
└───────┬───────┘
        │
        ▼
┌───────────────┐
│     AWS WAF   │  ← Web/application filtering
└───────┬───────┘
        │
        ▼
┌───────────────┐
│     ALB       │  ← Layer 7
└───────┬───────┘
        │
        ▼
┌───────────────┐
│     NACL      │  ← Subnet-level IP filtering
└───────┬───────┘
        │
        ▼
┌───────────────┐
│ Security Group│  ← Instance / ENI level
└───────┬───────┘
        │
        ▼
      EC2
```

**But:** this is a conceptual view, not a literal requirement that every request passes through all these components.

---

# 2. Security Group — Can I Block One Specific IP?

### Important:

**Security Groups support ALLOW rules only.**

They do **not** support explicit DENY rules.

For example:

```
Security Group

Inbound:
ALLOW TCP 443 from 0.0.0.0/0
```

This means:

> Allow everyone.
> 

But you cannot write:

```
DENY 203.0.113.50
```

inside a Security Group.

### Exam clue

If the question says:

> "Block a specific malicious IP address"
> 

Don't choose **Security Group** if an explicit deny is required.

---

# 3. NACL — Block a Specific IP

NACLs support:

- ✅ ALLOW
- ❌ DENY

Therefore, NACLs are useful for **blocking a specific IP at the subnet level**.

Example:

```
NACL

Rule #100
DENY
203.0.113.50/32

Rule #200
ALLOW
0.0.0.0/0
```

Because rule #100 comes first:

```
203.0.113.50
       │
       ▼
NACL #100
       │
     DENY ❌
```

Other IPs:

```
Other users
    │
    ▼
NACL #100 → doesn't match
    │
    ▼
NACL #200 → ALLOW ✅
```

### SAA-C03 keyword

> **"Explicitly DENY a specific IP at subnet level"**
> 

→ **NACL**

---

# 4. But There Is an Important NACL Problem

NACLs are **stateless**.

So if you allow inbound traffic, you must also consider the return traffic.

For example:

```
Client
203.0.113.50
     │
     │ TCP 443
     ▼
   EC2
     │
     │ response
     ▼
Client
```

NACL rules must account for both directions, including **ephemeral ports** where applicable.

That's why NACLs are powerful for IP blocking but can require careful rule management.

---

# 5. EC2 With Firewall Software

Another approach:

```
Internet
    │
    ▼
Public EC2
    │
    ▼
Firewall Software
    │
    ▼
Application
```

You could install and manage firewall software on the EC2 instance.

It can potentially block:

```
203.0.113.50 → DROP ❌
```

### Why isn't this usually the best SAA answer?

Because you must manage:

- OS
- firewall software
- rules
- patches
- availability
- scaling

AWS-managed services are generally preferable when they satisfy the requirement.

---

# 6. ALB + Security Group

Now let's introduce an **Application Load Balancer**.

```
Internet
    │
    ▼
   ALB
    │
    ▼
 EC2 Instances
Private Subnet
```

Security groups:

```
Client
  │
  ▼
ALB Security Group
  │
  ▼
EC2 Security Group
```

A good architecture is:

### ALB Security Group

```
Inbound:
TCP 443
Source: Internet
```

### EC2 Security Group

```
Inbound:
TCP application port
Source: ALB Security Group
```

This is a major SAA-C03 pattern.

> **EC2 should generally accept traffic from the ALB Security Group rather than directly from the Internet.**
> 

---

# 7. Can ALB Security Group Block One IP?

No.

Again:

**Security Groups = ALLOW only**

So this doesn't work:

```
ALB Security Group

ALLOW 0.0.0.0/0
DENY 203.0.113.50 ❌
```

For explicit IP blocking, consider:

**NACL or AWS WAF**, depending on the requirement.

---

# 8. ALB + WAF — Better for Web Traffic

This is one of the most important patterns.

```
                 Internet
                    │
                    ▼
              ┌──────────┐
              │ AWS WAF  │
              └────┬─────┘
                   │
                   ▼
              ┌──────────┐
              │   ALB    │
              └────┬─────┘
                   │
                   ▼
              ┌──────────┐
              │   EC2    │
              └──────────┘
```

AWS WAF can inspect HTTP/HTTPS requests.

You can create rules based on things such as:

- IP addresses
- HTTP headers
- URI paths
- Query strings
- Request patterns
- Rate-based conditions
- Managed rules

---

# 9. Blocking an IP with WAF

Suppose:

```
Attacker IP:
203.0.113.50
```

Create a WAF rule:

```
IF source IP = 203.0.113.50
THEN BLOCK
```

Traffic:

```
203.0.113.50
       │
       ▼
    AWS WAF
       │
       X
    BLOCKED
```

Legitimate user:

```
198.51.100.10
       │
       ▼
    AWS WAF
       │
       ▼
      ALB
       │
       ▼
      EC2
```

### Why WAF is often better than NACL for web applications

WAF is designed specifically for **HTTP/HTTPS application-layer filtering**.

NACL operates at the **subnet/network level**.

---

# 10. NACL vs WAF for IP Blocking

| Feature | NACL | AWS WAF |
| --- | --- | --- |
| Level | Subnet | Web/application |
| ALLOW | ✅ | ✅ |
| DENY/BLOCK | ✅ | ✅ |
| IP blocking | ✅ | ✅ |
| HTTP/HTTPS inspection | ❌ | ✅ |
| URI filtering | ❌ | ✅ |
| HTTP headers | ❌ | ✅ |
| Query string filtering | ❌ | ✅ |
| Stateful | ❌ | Managed web filtering |
| Best for | Network/subnet filtering | Web application protection |

### Memory trick

> **NACL = Network-level blocking**
> 

> **WAF = Web-level blocking**
> 

---

# 11. ALB + WAF — Real-World Scenario

Imagine an online shopping website:

```
Users
  │
  ▼
AWS WAF
  │
  │ malicious request → BLOCK ❌
  │
  ▼
ALB
  │
  ▼
EC2 Auto Scaling
  │
  ▼
Application
```

You discover an attacker repeatedly sending malicious requests from:

```
203.0.113.50
```

You create an IP set in WAF:

```
Blocked IPs:
203.0.113.50
```

WAF blocks the requests before they reach the application.

### Exam clue

> "Protect an ALB-based web application from malicious IP addresses"
> 

→ **AWS WAF**

---

# 12. NLB + Security Group

A Network Load Balancer operates at **Layer 4**.

Architecture:

```
Internet
    │
    ▼
   NLB
    │
    ▼
 EC2
```

Modern NLBs can have security groups, but remember the bigger distinction:

**NLB = network/transport-level load balancing**

**ALB = application-level HTTP/HTTPS load balancing**

For web filtering requirements such as:

> Block requests based on URI, HTTP headers, SQL injection patterns, etc.
> 

Think:

**WAF + ALB/CloudFront**, not NLB.

---

# 13. CloudFront + WAF

Now imagine your users are worldwide.

```
             Users Worldwide
                    │
                    ▼
             ┌────────────┐
             │ CloudFront │
             └─────┬──────┘
                   │
                   ▼
              ┌─────────┐
              │   WAF   │
              └────┬────┘
                   │
                   ▼
                  ALB
                   │
                   ▼
                  EC2
```

This gives you:

**CloudFront**

→ Global edge delivery / caching

**WAF**

→ Web request filtering

**ALB**

→ Application load balancing

**EC2**

→ Application compute

---

# 14. Why CloudFront + WAF Is Powerful

Suppose your application has:

```
1,000,000 users
```

and malicious traffic is coming from:

```
203.0.113.50
```

You can block the IP at the web edge using WAF associated with CloudFront.

Conceptually:

```
Attacker
   │
   ▼
CloudFront Edge
   │
   ▼
AWS WAF
   │
   X
BLOCKED
```

The malicious request doesn't need to travel all the way to your origin infrastructure.

---

# 15. CloudFront Geo Restriction

Don't confuse **IP blocking** with **Geo Restriction**.

Suppose your application should only be available in:

```
Bangladesh
India
Singapore
```

CloudFront can use **geographic restrictions** to restrict content based on country.

Example:

```
Bangladesh → ✅
India      → ✅
USA        → ❌
Canada     → ❌
```

### Memory trick

> **Specific IP → WAF**
> 

> **Entire country → CloudFront Geo Restriction**
> 

---

# 16. The Complete Comparison

| Method | Level | Can DENY? | Best Use |
| --- | --- | --- | --- |
| **Security Group** | EC2/ENI | ❌ | Allow trusted traffic |
| **NACL** | Subnet | ✅ | Block IP/network at subnet |
| **EC2 Firewall** | OS/instance | ✅ | Custom host-level firewall |
| **AWS WAF** | Web/application | ✅ | Block malicious HTTP/HTTPS requests |
| **CloudFront Geo Restriction** | Global/edge | ✅ | Restrict countries |
| **Network Firewall** | VPC/network | ✅ | Sophisticated VPC-wide network inspection |

---

# 17. Most Important Exam Decision Table

| Question wording | Best answer |
| --- | --- |
| "Security Group should block an IP" | ❌ SG can't explicitly DENY |
| "Block a specific IP at subnet level" | **NACL** |
| "Block malicious HTTP requests" | **AWS WAF** |
| "Block IP addresses for an ALB web application" | **AWS WAF** |
| "Block SQL injection/XSS" | **AWS WAF** |
| "Filter based on HTTP headers/URI" | **AWS WAF** |
| "Block a country" | **CloudFront Geo Restriction** |
| "Protect entire VPC with sophisticated network filtering" | **AWS Network Firewall** |
| "Host-level/custom firewall rules" | **EC2 firewall software** |
| "Allow traffic to EC2 only from ALB" | **EC2 SG referencing ALB SG** |

---

# 18. Architecture You Should Remember

For a production web application, a common architecture is:

```
                         INTERNET
                            │
                            ▼
                    ┌───────────────┐
                    │  CloudFront   │
                    │ CDN / Edge    │
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │   AWS WAF     │
                    │ IP / Web rules│
                    └───────┬───────┘
                            │
                            ▼
                  ┌──────────────────┐
                  │       ALB        │
                  │  Public Subnet   │
                  └────────┬─────────┘
                           │
                           ▼
              ┌─────────────────────────┐
              │    EC2 Auto Scaling     │
              │    Private Subnets      │
              └───────────┬─────────────┘
                          │
                          ▼
                       Database
```

Security:

```
CloudFront
    ↓
   WAF
    ↓
   ALB SG
    ↓
   EC2 SG
    ↓
  Database SG
```

The **EC2 Security Group should trust the ALB Security Group**, rather than opening the EC2 instances directly to the Internet.

---

# 19. SAA-C03 "Which One?" Cheat Sheet 🧠

```
                    NEED TO BLOCK?
                         │
          ┌──────────────┼───────────────┐
          │              │               │
      Network/IP       Web Request     Country
          │              │               │
          ▼              ▼               ▼
         NACL           WAF        CloudFront
                                      Geo
                                   Restriction

             NEED VPC-WIDE INSPECTION?
                         │
                         ▼
                  AWS Network
                    Firewall

             NEED TO ALLOW ONLY?
                         │
                         ▼
                  Security Group
```

### 🔥 Final memory trick

> **SG → ALLOW**
> 

> **NACL → DENY IP**
> 

> **WAF → BLOCK WEB**
> 

> **CloudFront → GLOBAL / COUNTRY**
> 

> **Network Firewall → WHOLE VPC**
> 

And the single most important distinction:

**"Block this IP from reaching my subnet" → NACL**

**"Block this IP from accessing my web application" → WAF**

**"Block users from a particular country" → CloudFront Geo Restriction**
