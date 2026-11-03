# ANS

## 1. ✅ Correct Answer

**An Internet-routable IP address (static) of the customer gateway's external interface for the on-premises network.**

The key requirement for an AWS **Site-to-Site VPN** is that AWS must know **where the customer's on-premises VPN device is located on the Internet**.

That is provided by the **static, Internet-routable public IP address of the Customer Gateway device**.

### Basic flow

```
On-Premises / SAP HANA
        │
        │
        ▼
Customer Gateway
(Your VPN device)
        │
        │ Public static IP
        │
   🌐 Internet
        │
        ▼
Virtual Private Gateway
        │
        ▼
      VPC
        │
        ▼
   AWS Workload
```

---

# 2. 🧠 Core Concept — Very Simple

The question is basically asking:

> **"AWS wants to create a VPN tunnel to my on-premises network. What information does AWS need about my on-premises VPN device?"**
> 

Answer:

> **Its static public IP address.**
> 

Think of it like calling someone:

```
AWS VPN
   │
   │ "Where should I connect?"
   ▼
Customer Gateway
   │
   └── Public static IP = "My Internet address"
```

AWS needs this address to establish the VPN tunnel.

---

# 3. ✅ Why the Correct Answer Is Correct

For a traditional AWS Site-to-Site VPN, you configure two important sides:

```
AWS side                         Customer side
────────                         ─────────────
Virtual Private Gateway   ←→     Customer Gateway
                                (VPN device)
```

The **Customer Gateway (CGW)** represents the physical/logical VPN device on your on-premises side.

AWS needs the CGW's:

> **Static, Internet-routable public IP address**
> 

because the VPN tunnel is established over the Internet.

### Requirement checklist

| Requirement | Correct answer |
| --- | --- |
| Identify on-premises VPN endpoint | ✅ |
| Reach it over the Internet | ✅ |
| Must be publicly routable | ✅ |
| Should be static | ✅ |
| Required to establish VPN tunnel | ✅ |

---

# 4. ❌ Why Every Other Option Is Wrong

### ❌ Option 1 — EIP to the Virtual Private Gateway

This is backwards.

The **Virtual Private Gateway (VGW)** is on the **AWS side**:

```
On-Premises                  AWS
   │                          │
Customer Gateway ─── VPN ──► VGW
```

The question asks what needs to be configured **outside the VPC** for the connection to succeed.

You don't assign an EIP to the VGW as the requirement described here.

The important public IP is the **Customer Gateway's external interface**.

---

### ❌ Option 3 — Dedicated NAT instance

A NAT instance is used for:

```
Private subnet
      │
      ▼
NAT
      │
      ▼
Internet
```

Its purpose is allowing **private AWS resources to initiate Internet-bound connections**.

It is not required to establish an AWS Site-to-Site VPN.

```
NAT ≠ VPN endpoint
```

---

### ❌ Option 4 — Main route table → NAT instance

Same problem.

A route such as:

```
0.0.0.0/0
     ↓
NAT Instance
```

is about **Internet access from private subnets**.

Site-to-Site VPN uses a VPN connection between:

```
Customer Gateway
       ↕
VPN
       ↕
Virtual Private Gateway
```

You don't need a NAT instance for this.

---

# 5. 🎯 AWS Service / Topic Being Tested

**Domain:** Networking & Content Delivery

**Main Topic:** Hybrid Connectivity

**Subtopic:** AWS Site-to-Site VPN / Customer Gateway

### Important components

```
On-Premises
    │
    ▼
Customer Gateway (CGW)
    │
    │ Internet
    ▼
Site-to-Site VPN
    │
    ▼
Virtual Private Gateway (VGW)
    │
    ▼
VPC
```

---

# 6. 🔑 Important Exam Keywords / Clues

| Keyword | Think |
| --- | --- |
| **Site-to-Site VPN** | VPN between on-prem and VPC |
| **on-premises network** | Customer Gateway |
| **Customer Gateway** | Your-side VPN device |
| **external interface** | Public IP |
| **Internet-routable IP** | Public IP |
| **static IP** | Required endpoint address |
| **Virtual Private Gateway** | AWS-side VPN endpoint |
| **NAT instance** | ❌ Not VPN |

### 🚨 Exam trigger

> **Site-to-Site VPN + Customer Gateway → static public IP of the customer gateway**
> 

---

# 7. 🏢 Easy Real-World Example

Your insurance company's SAP HANA database is sitting in the company's data center:

```
Company Data Center
┌─────────────────────┐
│ SAP HANA            │
│      ↓              │
│ VPN Router          │
│ Public IP:         │
│ 203.x.x.x           │
└─────────┬───────────┘
          │
       Internet
          │
          ▼
┌─────────────────────┐
│ AWS                 │
│ Virtual Private     │
│ Gateway             │
│       ↓             │
│ VPC                 │
└─────────────────────┘
```

AWS needs to know:

> **"Which public IP should I establish the VPN tunnel with?"**
> 

Answer:

**203.x.x.x — the static public IP of the customer's VPN device.**

---

# 8. 🧠 Short Memory Trick

> **CGW = Customer's VPN door → AWS needs the door's public address.**
> 

Or simply:

**VPN → Customer Gateway → Static Public IP**

---

# 9. 🔄 Similar Questions AWS Could Ask

### Variation 1

> What AWS component represents the VPN device on the customer/on-premises side?
> 

**Answer:** Customer Gateway.

### Variation 2

> What AWS component terminates the VPN on the AWS side for a VPC?
> 

**Answer:** Virtual Private Gateway.

### Variation 3

> What IP address is required for the Customer Gateway?
> 

**Answer:** Static, Internet-routable public IP of the customer VPN device.

### Variation 4

> A private EC2 instance needs outbound Internet access.
> 

**Answer:** That's where you think about **NAT Gateway**, not Site-to-Site VPN.

### ⭐ Important distinction

```
Customer Gateway
       ↓
Your on-premises VPN device

Virtual Private Gateway
       ↓
AWS-side VPN endpoint

NAT Gateway
       ↓
Private subnet → Internet

Site-to-Site VPN
       ↓
On-premises network ↔ AWS VPC
```

---

# 10. 📝 SAA-C03 Exam Takeaway

> **For an AWS Site-to-Site VPN, the Customer Gateway must have a static, Internet-routable public IP address so AWS can establish the VPN tunnel to the on-premises VPN device.**
> 

---

# 🔍 Services & Concepts in This Question

| Service / Feature | What it is | What it does | Remember |
| --- | --- | --- | --- |
| **AWS Site-to-Site VPN** | Managed VPN service | Securely connects on-premises networks to AWS | On-prem ↔ AWS |
| **Customer Gateway** | AWS representation of customer's VPN device | Defines the on-premises VPN endpoint | Customer side |
| **Customer Gateway public IP** | Static public IP of VPN device | Allows AWS to locate/reach the VPN endpoint | **Static public IP** |
| **Virtual Private Gateway** | AWS-side VPN gateway | Terminates VPN connections for a VPC | AWS side |
| **NAT Instance** | EC2-based NAT | Provides Internet access to private resources | Not VPN |
| **Route Table** | VPC traffic-routing rules | Determines where packets go | Controls VPC routing |
| **VPC** | AWS virtual network | Hosts subnets and workloads | AWS network |
| **SAP HANA** | Enterprise database platform | Runs ERP/database workloads | On-prem workload here |

---

## 📊 Excel Row

[Q# not provided] Networking & Content Delivery Hybrid Connectivity - Site-to-Site VPN Customer Gateway Site-to-Site VPN, Customer Gateway, Virtual Private Gateway, VPC, NAT Instance, Route Tables Weak Customer Gateway is the on-prem VPN endpoint and needs a static Internet-routable public IP; NAT is for private subnet Internet access, not VPN Customer Gateway → Static Public IP
