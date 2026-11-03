# Site-to-Site VPN, Virtual Private Gateway & Customer Gateway

Absolutely — here is a **clear AWS SAA-C03 exam-focused note** for **Site-to-Site VPN and Direct Connect**, with the important differences and decision-making points.

# AWS Site-to-Site VPN

## 1. What is Site-to-Site VPN?

**AWS Site-to-Site VPN** creates a secure connection between:

**On-premises network ↔ AWS VPC**

It uses the **public Internet**, but the traffic is protected using **IPsec encryption**.

### Simple example

Your company has:

```
Company Data Center
      │
      │ Encrypted VPN over Internet
      ▼
AWS Site-to-Site VPN
      │
      ▼
AWS VPC
      │
      ▼
Private EC2
```

**Think:**

> 🏢 Office → 🔐 VPN → ☁️ AWS VPC
> 

---

# 2. Important Components

A Site-to-Site VPN has two major sides.

### AWS side → Virtual Private Gateway (VGW)

**VGW = VPN concentrator on the AWS side**

It is:

- Created in AWS
- Attached to the VPC
- Used to establish the VPN connection
- Can have a customized **ASN (Autonomous System Number)**

```
AWS
┌───────────────────────────┐
│ VPC                       │
│                           │
│   Private EC2             │
│       │                   │
│       ▼                   │
│      VGW                  │
└───────┬───────────────────┘
        │
        │ VPN
        ▼
```

### Customer side → Customer Gateway (CGW)

**CGW = software application or physical device on the customer's/on-premises side**

For example:

```
Company Data Center
      │
      ▼
Customer Gateway
      │
      │ Internet
      ▼
AWS VGW
```

### Easy memory

| Component | Location |
| --- | --- |
| **Customer Gateway (CGW)** | On-premises |
| **Virtual Private Gateway (VGW)** | AWS |
| **VPN connection** | Between them |

> **CGW = Customer side**
> 
> 
> **VGW = AWS/VPC side**
> 

---

# 3. Customer Gateway IP Address

The Customer Gateway generally needs a **public, Internet-routable IP address**.

If your customer gateway is behind a NAT device that supports **NAT Traversal (NAT-T)**, you use the **public IP address of the NAT device**.

---

# 4. Route Propagation — VERY IMPORTANT

Creating a VPN connection alone doesn't automatically make your subnet route traffic through the VPN.

You need to configure routing.

For a VGW-based Site-to-Site VPN:

> **Enable Route Propagation in the route table associated with your subnets.**
> 

Example:

```
On-Premises Network
10.10.0.0/16
      │
      │ VPN
      ▼
     VGW
      │
      ▼
AWS VPC
10.0.0.0/16
```

The route table needs to know:

```
10.10.0.0/16 → VGW
```

### ⭐ Exam clue

If a question says:

> "VPN is established but EC2 cannot communicate with on-premises"
> 

Check:

**Route propagation / route tables**

---

# 5. Security Groups Still Apply

Even when VPN connectivity works, your EC2 Security Group must allow the required traffic.

For example, if you want to ping EC2:

```
On-premises
      │
      │ ICMP
      ▼
    EC2
```

The EC2 Security Group needs an appropriate **inbound ICMP rule**.

So remember:

> **VPN provides connectivity. Security Groups still control traffic.**
> 

---

# 6. AWS VPN CloudHub

**VPN CloudHub** allows communication between **multiple sites** using multiple VPN connections.

Example:

```
       Office A
          │
         VPN
          │
          ▼
         VGW
        /   \
     VPN     VPN
      /       \
Office B     Office C
```

This is basically a **hub-and-spoke model**.

### Use case

Your company has:

- Bangladesh office
- Singapore office
- UK office

and wants the offices to communicate using VPN connections through AWS.

### Key points

- Uses multiple VPN connections
- Uses the same VGW
- Hub-and-spoke architecture
- Uses dynamic routing
- Goes over the **public Internet**
- Lower-cost networking option

---

# 7. Site-to-Site VPN — Exam Summary

Remember:

```
On-Premises
    │
Customer Gateway
    │
    │ IPsec VPN
    │ Public Internet
    ▼
Virtual Private Gateway
    │
    ▼
VPC
    │
Private EC2
```

### ⭐ Key exam facts

- Secure connection between **on-premises ↔ AWS VPC**
- Uses **public Internet**
- Traffic is encrypted using **IPsec**
- **CGW = customer side**
- **VGW = AWS side**
- Need appropriate **route table configuration**
- Route propagation can be enabled
- Security Groups still apply
- Faster to deploy than Direct Connect
- Generally cheaper than Direct Connect
- Can be used as a **backup for Direct Connect**

---

# AWS Direct Connect

## 1. What is Direct Connect?

**AWS Direct Connect (DX)** provides a **dedicated private network connection** from your on-premises network to AWS.

Instead of:

```
Office
   │
   │ Public Internet
   │
   ▼
AWS
```

you have:

```
Company Data Center
        │
        │ Dedicated private connection
        │
        ▼
AWS Direct Connect
        │
        ▼
AWS
```

### Think:

> **VPN = Internet connection + encryption**
> 

> **Direct Connect = Dedicated private connection**
> 

---

# 2. Why use Direct Connect?

Direct Connect is useful when you need:

### Higher bandwidth

For example:

- Large datasets
- Data transfer
- High-volume workloads

### Consistent network performance

Useful for:

- Real-time applications
- Large enterprise workloads
- Hybrid cloud

### Hybrid environments

```
On-Premises
     │
     │ Direct Connect
     ▼
    AWS
```

---

# 3. Direct Connect + Virtual Private Gateway

You generally use a **Virtual Private Gateway** to connect Direct Connect to a VPC.

```
On-Premises
     │
     │
Direct Connect
     │
     ▼
Direct Connect Location
     │
     ▼
   AWS
     │
    VGW
     │
     ▼
    VPC
     │
     ▼
   EC2
```

---

# 4. Access Public + Private AWS Resources

A Direct Connect connection can provide access to:

### Private AWS resources

Example:

```
On-premises → Direct Connect → VPC → EC2
```

### Public AWS resources

Example:

```
On-premises → Direct Connect → S3
```

So don't assume Direct Connect is only for private EC2.

---

# 5. Direct Connect Gateway

Suppose you have:

```
On-Premises
     │
Direct Connect
     │
     ▼
Direct Connect Gateway
    /       \
   /         \
VPC Region A  VPC Region B
```

A **Direct Connect Gateway** helps you connect a Direct Connect connection to **VPCs in multiple AWS Regions**.

### ⭐ Exam clue

> "Connect on-premises to VPCs in multiple AWS Regions"
> 

Think:

**Direct Connect Gateway**

---

# 6. Direct Connect Connection Types

## Dedicated Connection

Physical Ethernet port dedicated to one customer.

Available capacities include:

- 1 Gbps
- 10 Gbps
- 100 Gbps

The request is made to AWS and completed through AWS Direct Connect infrastructure/partners as applicable.

---

## Hosted Connection

Provided through an AWS Direct Connect Partner.

Can offer capacities such as:

- 50 Mbps
- 500 Mbps
- Up to 10 Gbps

Capacity options depend on the Direct Connect Partner.

### Easy comparison

|  | Dedicated | Hosted |
| --- | --- | --- |
| Physical port | Dedicated to customer | Partner-hosted |
| Provider | AWS/Direct Connect setup | Partner |
| Capacity | Higher options | Flexible options |
| Best for | Large enterprise | Flexible connectivity |

---

# 7. Direct Connect Encryption — VERY IMPORTANT

This is a common SAA exam trap.

### ❌ Direct Connect by itself does NOT encrypt your data.

But:

> Direct Connect traffic travels over a **private connection**.
> 

So:

**Private ≠ Encrypted**

---

# 8. Direct Connect + VPN

If you want:

**Private connection + encryption**

you can combine:

**AWS Direct Connect + Site-to-Site VPN**

```
On-Premises
     │
     ├──── Direct Connect ────► AWS
     │
     └──── VPN over DX ───────► AWS
              🔐
           IPsec encryption
```

This gives you an additional encryption layer.

### ⭐ Exam clue

If the question says:

> "Need Direct Connect but also require encrypted traffic"
> 

Think:

**Direct Connect + Site-to-Site VPN**

---

# 9. Direct Connect Resiliency

You shouldn't depend on only one Direct Connect connection for critical production workloads.

You can have:

```
             ┌── Direct Connect #1 ──► AWS
On-Premises ─┤
             └── Direct Connect #2 ──► AWS
```

or use:

```
Primary:
Direct Connect

Backup:
Site-to-Site VPN
```

### Common architecture

```
                ┌── Direct Connect ──► AWS
On-Premises ────┤
                └── Site-to-Site VPN ► AWS
                         Backup
```

This gives you redundancy if Direct Connect fails.

---

# Site-to-Site VPN vs Direct Connect

This is **VERY important for SAA-C03**.

| Feature | Site-to-Site VPN | Direct Connect |
| --- | --- | --- |
| Connection | Public Internet | Dedicated private connection |
| Encryption | ✅ IPsec | ❌ Not encrypted by default |
| Speed | Lower | Higher |
| Performance | Less predictable | More consistent |
| Setup | Faster | Takes longer |
| Cost | Generally lower | Generally higher |
| Best for | Quick secure connectivity | Enterprise/hybrid workloads |
| Backup | Can back up DX | Can be primary |
| Multiple VPC Regions | Transit Gateway / other architecture may be used | Direct Connect Gateway |
| AWS side | VGW | VGW / Direct Connect Gateway |
| Customer side | CGW | Customer/partner connection |

---

# 🧠 SAA-C03 Decision Guide

### Question says:

**"Need quick connectivity between on-premises and AWS"**

➡️ **Site-to-Site VPN**

---

### "Need encrypted connection over the Internet"

➡️ **Site-to-Site VPN**

---

### "Need high bandwidth"

➡️ **Direct Connect**

---

### "Need consistent network performance"

➡️ **Direct Connect**

---

### "Need dedicated private connection"

➡️ **Direct Connect**

---

### "Need encryption + Direct Connect"

➡️ **Direct Connect + Site-to-Site VPN**

---

### "Need backup connectivity for Direct Connect"

➡️ **Site-to-Site VPN**

---

### "Need to connect multiple VPCs across Regions through Direct Connect"

➡️ **Direct Connect Gateway**

---

### "Need multiple offices/sites to communicate through AWS using VPN"

➡️ **VPN CloudHub**

---

# 🔥 Final 30-Second Revision

```
SITE-TO-SITE VPN
━━━━━━━━━━━━━━━━━━━━━━
On-Premises
    │
   CGW
    │
    │ IPsec / Public Internet
    ▼
   VGW
    │
   VPC
    │
   EC2

CGW = Customer side
VGW = AWS side
Fast to deploy
Lower cost
Encrypted
Uses Internet
```

```
DIRECT CONNECT
━━━━━━━━━━━━━━━━━━━━━━
On-Premises
    │
    │ Dedicated Private Connection
    ▼
Direct Connect
    │
    ▼
   VGW
    │
   VPC
    │
   EC2

High bandwidth
Consistent performance
Private connection
NOT encrypted by default
Longer setup
Higher cost
```

### ⭐ Most important memory trick

> **VPN = Fast + Internet + Encrypted**
> 

> **Direct Connect = Dedicated + Private + High bandwidth**
> 

> **DX + VPN = Dedicated private connectivity + encryption**
> 

> **CGW = Customer side**
> 

> **VGW = AWS side**
> 

> **DX Gateway = Multiple VPCs/Regions**
> 

> **VPN CloudHub = Multiple sites communicate through AWS**
>
