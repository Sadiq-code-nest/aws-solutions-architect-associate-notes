# AWS Direct Connect (DX) — SAA-C03 Exam Notes

## 1. What is Direct Connect?

**AWS Direct Connect (DX)** provides a **dedicated private network connection** between your **on-premises/data center network and AWS**.

Think:

```
Your Company / Data Center
        |
        | Dedicated private connection
        |
AWS Direct Connect Location
        |
        |
      AWS
        |
       VPC
        |
   Private EC2
```

### Simple real-world example

Imagine your company has:

```
Corporate Data Center
        |
   10 Gbps private link
        |
       AWS
        |
       VPC
        |
      EC2
```

Instead of sending traffic through:

```
Data Center → Internet → AWS
```

you have a dedicated connection:

```
Data Center → Direct Connect → AWS
```

### Memory trick

> **DX = Dedicated eXpress private road to AWS**
> 

---

# 2. Why would you use Direct Connect?

The major SAA-C03 clues are:

| Requirement | Think |
| --- | --- |
| Dedicated/private connection | **Direct Connect** |
| Large amount of data | **Direct Connect** |
| Consistent network performance | **Direct Connect** |
| High bandwidth | **Direct Connect** |
| Hybrid cloud | **Direct Connect** |
| Real-time data feeds | **Direct Connect** |
| Avoid Internet for AWS connectivity | **Direct Connect** |
| Long-term connectivity between on-prem and AWS | **Direct Connect** |

### Example

A financial company has:

- 50 TB of data
- on-premises database
- applications running in AWS
- continuous data transfer
- requirement for predictable network performance

**Answer → Direct Connect**

---

# 3. Direct Connect vs Internet

### Normal Internet connection

```
Corporate DC
     |
     ↓
  Internet
     |
     ↓
   AWS
```

The Internet is shared infrastructure.

### Direct Connect

```
Corporate DC
     |
     ↓
Direct Connect
     |
     ↓
AWS
```

The connection is **dedicated/private**.

### Important!

**Direct Connect does NOT automatically encrypt your traffic.**

This is a very important exam point.

```
Direct Connect
      ↓
Private connection
      ❌ Not encrypted by default
```

If you need encryption:

```
Direct Connect
      +
Site-to-Site VPN
      ↓
IPsec encryption
```

### Exam keyword

> **Private ≠ encrypted**
> 

---

# 4. Direct Connect architecture

The important components are:

```
Corporate Data Center
        |
Customer Router / Firewall
        |
        |
AWS Direct Connect
        |
Direct Connect Location
        |
        |
       AWS
        |
Virtual Private Gateway
        |
VPC
        |
Private Subnet
        |
EC2
```

### Components to remember

| Component | What it means |
| --- | --- |
| Customer network | Your on-premises network |
| Customer router | Your networking equipment |
| Direct Connect Location | Physical AWS/partner facility |
| Direct Connect connection | Dedicated physical connection |
| Virtual Private Gateway (VGW) | AWS-side gateway attached to VPC |
| VPC | Your AWS private network |
| EC2 | Your AWS workload |

---

# 5. What is a Direct Connect Location?

Your data center doesn't simply plug a cable directly into your VPC.

There is a **Direct Connect location** where the physical connectivity is established.

Think:

```
Your Data Center
      ↓
Customer Router
      ↓
Direct Connect Location
      ↓
AWS Network
      ↓
VGW
      ↓
VPC
```

### Exam perspective

You don't need to memorize the physical details.

Just remember:

> **Direct Connect Location = physical place where your dedicated connection connects into AWS.**
> 

---

# 6. Virtual Private Gateway (VGW)

You have already learned VGW with Site-to-Site VPN.

The same concept is relevant here.

For a traditional DX-to-VPC setup:

```
On-Premises
     |
Direct Connect
     |
    VGW
     |
    VPC
```

**VGW = AWS-side gateway attached to your VPC.**

### Memory trick

> **VGW = VPC's gateway toward your external/on-prem network**
> 

---

# 7. Can Direct Connect access S3?

**Yes.**

Direct Connect can provide connectivity to:

### Private AWS resources

```
EC2
RDS
VPC resources
```

### AWS public services

For example:

```
S3
```

The exact configuration uses different virtual interfaces.

---

# 8. Virtual Interfaces — important for exam

You will commonly see:

### Private Virtual Interface (Private VIF)

Used to access:

```
VPC
Private resources
```

Example:

```
On-Premises
     |
Direct Connect
     |
Private VIF
     |
VGW
     |
VPC
     |
Private EC2
```

### Public Virtual Interface (Public VIF)

Used to access AWS **public services**.

For example:

```
On-Premises
     |
Direct Connect
     |
Public VIF
     |
AWS public services
     |
S3
```

### Easy memory

> **Private VIF → VPC/private resources**
> 

> **Public VIF → AWS public services**
> 

---

# 9. Direct Connect Gateway (DXGW)

This is a **very important distinction**.

Suppose you have:

```
Corporate Data Center
       |
Direct Connect
       |
     AWS
```

And you want connectivity to VPCs in **multiple AWS Regions**.

Instead of creating separate Direct Connect connections for every VPC/Region, you can use:

**Direct Connect Gateway**

Architecture:

```
                   VPC - us-east-1
                         |
                         |
Corporate DC → Direct Connect → DX Gateway
                         |
                         |
                   VPC - us-west-2
```

### Memory trick

> **Direct Connect Gateway = one DX connection → multiple VPCs/Regions**
> 

---

# 10. Direct Connect vs Direct Connect Gateway

| Feature | Direct Connect | Direct Connect Gateway |
| --- | --- | --- |
| What is it? | Dedicated network connection | Gateway for connecting DX to multiple VPCs |
| Main purpose | Connect on-premises to AWS | Extend DX connectivity across VPCs/Regions |
| Physical connection? | Yes | No |
| Connects to VPC? | Through appropriate gateway/VIF | Helps connect to multiple VPCs |
| Multi-region use | Possible with additional architecture | **Key use case** |
| Memory | **Private dedicated road** | **Gateway connecting many AWS destinations** |

### Exam clue

> **"Connect on-premises to VPCs in multiple AWS Regions"**
> 

Think:

**Direct Connect Gateway**

---

# 11. Direct Connect Connection Types

There are two major types you should know:

### Dedicated Connection

A physical Ethernet connection dedicated to your customer.

```
Customer
   |
Dedicated physical connection
   |
AWS Direct Connect
```

### Hosted Connection

Provided through an **AWS Direct Connect Partner**.

```
Customer
   |
Partner
   |
AWS Direct Connect
```

For SAA-C03, don't spend too much time memorizing exact bandwidth ranges. The more important concept is:

> **Dedicated = physical connection dedicated to you**
> 

> **Hosted = provided through a Direct Connect Partner**
> 

---

# 12. Direct Connect bandwidth

The key exam concept isn't usually the exact number.

Think:

> **Need high bandwidth for large data transfer → Direct Connect**
> 

Example:

```
Company
  |
  | Massive data transfer
  | 10s/100s of TB
  ↓
Direct Connect
  ↓
AWS
```

---

# 13. Direct Connect is NOT automatically encrypted

This is one of the most important exam traps.

### Direct Connect alone

```
On-Prem
   |
Direct Connect
   |
 AWS
```

✅ Private

❌ Not encrypted by default

### DX + VPN

```
On-Prem
   |
Direct Connect
   +
Site-to-Site VPN
   |
 AWS
```

✅ Private connectivity

✅ IPsec encryption

### Exam clue

If the question says:

> "Dedicated private connection but encryption is required"
> 

Think:

**Direct Connect + Site-to-Site VPN**

---

# 14. Direct Connect + Site-to-Site VPN

This is a **very common architecture**.

Imagine:

```
                    PRIMARY
Corporate DC ───── Direct Connect ───── AWS
      |
      |
      └──────────── BACKUP
              Site-to-Site VPN
                    |
                 Internet
                    |
                   AWS
```

### Why?

Direct Connect is reliable, but physical connections can fail.

So:

```
DX works
   ↓
Use DX
```

If DX fails:

```
DX failure
   ↓
Site-to-Site VPN
   ↓
Continue connectivity
```

### Exam memory trick

> **DX = primary private road**
> 

> **VPN = backup road**
> 

---

# 15. DX + VPN for encryption

There is another reason to combine them.

```
Direct Connect
      +
VPN
```

Gives:

```
Dedicated/private connectivity
          +
       Encryption
```

So if the question says:

> "The company requires a dedicated connection AND encrypted traffic."
> 

Think:

### **Direct Connect + Site-to-Site VPN**

---

# 16. Direct Connect Resiliency

For critical workloads, don't rely on only one physical connection.

### Basic

```
Corporate DC
     |
     |
     DX
     |
    AWS
```

Problem:

❌ Single point of failure

---

### Better

```
             Direct Connect #1
Corporate ─────────────────── AWS
   DC
             Direct Connect #2
         ───────────────────── AWS
```

Use separate connections / locations for higher resiliency.

---

### Maximum resilience

```
                    DX #1
                 Location #1
                    |
Corporate DC ──────┤
                    |
                    DX #2
                 Location #2
                    |
                   AWS
```

The key principle:

> **Separate connections terminating on separate devices and locations provide greater resiliency.**
> 

---

# 17. Direct Connect vs Site-to-Site VPN

This is extremely important for SAA-C03.

| Feature | Direct Connect | Site-to-Site VPN |
| --- | --- | --- |
| Connection | Dedicated private connection | Encrypted VPN tunnel |
| Uses Internet? | No, dedicated connection | **Yes** |
| Encryption | ❌ Not by default | ✅ IPsec |
| Setup time | Long | Fast |
| Cost | Generally more expensive | Generally cheaper |
| Bandwidth | High | Lower than DX in typical designs |
| Performance | More consistent/predictable | Dependent on Internet |
| Best for | Large/consistent workloads | Quick secure connectivity |
| Hybrid cloud | ✅ | ✅ |
| Backup for DX | — | **Excellent** |
| Encryption + dedicated connectivity | DX + VPN | Already encrypted |
| Exam keyword | **Dedicated / consistent / high bandwidth** | **Fast / encrypted / Internet VPN** |

---

# 18. The easiest decision method for the exam

When you see an on-premises → AWS question, ask:

### Question 1: Do they need a dedicated private connection?

**YES → Direct Connect**

```
On-Prem → DX → AWS
```

---

### Question 2: Do they need encrypted connectivity quickly?

**YES → Site-to-Site VPN**

```
On-Prem → Internet → VPN → AWS
```

---

### Question 3: Do they need dedicated + encrypted?

**YES → DX + Site-to-Site VPN**

```
On-Prem
   |
   ├── DX ─────────── Primary
   |
   └── VPN ────────── Encryption/Backup
```

---

### Question 4: Do they need VPC connectivity across multiple AWS Regions?

**YES → Direct Connect Gateway**

```
                    VPC Region A
                   /
On-Prem → DX → DXGW
                   \
                    VPC Region B
```

---

# 19. SAA-C03 Scenario Examples

### Scenario 1

> A company needs a **dedicated private connection** from its corporate data center to AWS with consistent network performance.
> 

**Answer: Direct Connect**

Keyword:

**Dedicated + consistent**

---

### Scenario 2

> A company needs to establish an encrypted connection between its on-premises data center and AWS quickly.
> 

**Answer: Site-to-Site VPN**

Keyword:

**Encrypted + quick**

---

### Scenario 3

> A company has a Direct Connect connection and wants an encrypted connection over the same private connectivity.
> 

**Answer: Direct Connect + Site-to-Site VPN**

Keyword:

**DX + encryption**

---

### Scenario 4

> A company wants connectivity from one on-premises data center to VPCs in multiple AWS Regions.
> 

**Answer: Direct Connect Gateway**

Keyword:

**Multiple Regions/VPCs**

---

### Scenario 5

> A company transfers huge datasets continuously between its data center and AWS and needs predictable network performance.
> 

**Answer: Direct Connect**

Keyword:

**Large data + predictable performance**

---

### Scenario 6

> A company wants a backup connection if its Direct Connect connection fails.
> 

**Answer: Site-to-Site VPN**

Architecture:

```
             Primary
On-Prem ─────── DX ───── AWS
    |
    └──────── VPN ────── AWS
                 Backup
```

---

# 20. Don't confuse these three

This is probably the most useful exam table:

| Service | Think |
| --- | --- |
| **Site-to-Site VPN** | 🔐 Fast encrypted tunnel over Internet |
| **Direct Connect** | 🚀 Dedicated private connection |
| **Direct Connect Gateway** | 🌎 Connect DX to VPCs across Regions/accounts architecture |

### One-line memory

> **VPN = Secure tunnel**
> 

> **DX = Dedicated road**
> 

> **DX Gateway = Connect that road to many AWS VPCs**
> 

---

# 🧠 Final SAA-C03 Cheat Sheet

```
ON-PREMISES → AWS
        │
        ├── Fast + encrypted
        │       ↓
        │   Site-to-Site VPN
        │
        ├── Dedicated + high bandwidth
        │       ↓
        │   Direct Connect
        │
        ├── Dedicated + encrypted
        │       ↓
        │   Direct Connect + VPN
        │
        ├── Multiple AWS Regions/VPCs
        │       ↓
        │   Direct Connect Gateway
        │
        └── DX failure backup
                ↓
           Site-to-Site VPN
```

### ⭐ 6 things to memorize before SAA-C03

1. **DX = dedicated private connection**
2. **VPN = encrypted IPsec tunnel over Internet**
3. **DX itself is NOT encrypted**
4. **DX + VPN = dedicated + encrypted**
5. **DX Gateway = connect DX to multiple VPCs/Regions**
6. **VPN is commonly used as a backup for Direct Connect**

That decision tree is much more valuable for the exam than memorizing the physical Direct Connect architecture.
