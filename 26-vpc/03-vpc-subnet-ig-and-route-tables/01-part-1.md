# Part: 1

For **AWS SAA-C03**, you should understand **VPC → CIDR → Subnet → Reserved IPs → Route Table → Internet Gateway** as one connected concept rather than memorizing each separately.

# AWS VPC + Subnet + IGW + Route Table — SAA-C03 Master Note

## 1. What is a VPC?

**VPC = Virtual Private Cloud**

A VPC is your **private network inside AWS**.

Think of AWS as a huge city:

```
AWS Region
│
├── VPC A → 10.0.0.0/16
│   ├── Public Subnet
│   ├── Private Subnet
│   └── Private Subnet
│
└── VPC B → 172.16.0.0/16
    ├── Public Subnet
    └── Private Subnet
```

You decide:

- Which IP range the network uses
- Which subnets exist
- Which resources are public/private
- How traffic is routed
- Whether resources can access the Internet
- How your AWS network connects to on-premises networks

### Simple real-world analogy

Imagine your company rents a large office building.

**VPC = entire office building**

Inside it you create different rooms:

**Subnets = rooms**

You then decide which rooms can access outside:

**Internet Gateway = entrance to the Internet**

And you decide where traffic should go:

**Route Table = directions/signposts**

---

# 2. VPC CIDR — Your Network's IP Range

When creating a VPC, you give it a **CIDR block**.

Example:

```
VPC CIDR = 10.0.0.0/16
```

This means the VPC has:

```
65,536 IPv4 addresses
```

because:

```
2^(32 - 16) = 65,536
```

### SAA-C03 important limits

| Item | Important value |
| --- | --- |
| VPC CIDR minimum | `/28` |
| VPC CIDR maximum | `/16` |
| `/28` addresses | 16 |
| `/16` addresses | 65,536 |
| Default VPC CIDR commonly seen | `172.31.0.0/16` |
| VPC CIDRs per VPC | Up to 5 by default* |
- The exact quota can change and is adjustable in many cases, so for the exam focus on the **/28–/16 CIDR sizing rule** and the fact that VPC CIDRs should not overlap.

---

# 3. Private IPv4 ranges

For a typical AWS VPC IPv4 CIDR, use private address ranges:

| Range | CIDR | Common use |
| --- | --- | --- |
| `10.0.0.0 – 10.255.255.255` | `10.0.0.0/8` | Large networks |
| `172.16.0.0 – 172.31.255.255` | `172.16.0.0/12` | AWS/default VPCs often use this |
| `192.168.0.0 – 192.168.255.255` | `192.168.0.0/16` | Small/home networks |

### Example

Suppose your company already has:

```
Corporate network:
10.0.0.0/16
```

Don't create:

```
AWS VPC:
10.0.0.0/16
```

because they **overlap**.

Instead:

```
Corporate:
10.0.0.0/16

AWS VPC:
10.1.0.0/16
```

Now they don't overlap.

### Why does this matter?

Suppose later you create:

```
Corporate Network
       │
       │ Site-to-Site VPN
       │
       ▼
AWS VPC
```

If both networks use overlapping IP addresses, routing becomes problematic.

### 🧠 Exam clue

> **VPC + VPN/Direct Connect + corporate network → Think about CIDR overlap.**
> 

---

# 4. VPC vs Subnet

This distinction is extremely important.

### VPC

The **overall network**.

Example:

```
VPC
10.0.0.0/16
```

### Subnet

A smaller IP range **inside the VPC**.

Example:

```
VPC = 10.0.0.0/16

Subnet A = 10.0.1.0/24
Subnet B = 10.0.2.0/24
Subnet C = 10.0.3.0/24
```

Think:

```
VPC
10.0.0.0/16
│
├── Subnet A
│   10.0.1.0/24
│
├── Subnet B
│   10.0.2.0/24
│
└── Subnet C
    10.0.3.0/24
```

---

# 5. Subnets belong to ONE Availability Zone

A subnet exists in **one AZ**.

Example:

```
Region: us-east-1

VPC: 10.0.0.0/16
│
├── us-east-1a
│   └── Subnet A: 10.0.1.0/24
│
├── us-east-1b
│   └── Subnet B: 10.0.2.0/24
│
└── us-east-1c
    └── Subnet C: 10.0.3.0/24
```

You cannot have one subnet spanning multiple AZs.

### 🧠 Exam memory

> **VPC = Region-level**
> 
> 
> **Subnet = AZ-level**
> 

This is a very common SAA question.

---

# 6. Public vs Private Subnet

This is another critical concept.

A subnet is considered **public** when its route table has a route to an **Internet Gateway**.

Example:

```
Subnet
   │
   ▼
Route Table
   │
   └── 0.0.0.0/0 → IGW
                         │
                         ▼
                     Internet
```

That makes it a **public subnet**.

A private subnet doesn't have a route directly to an Internet Gateway.

### Important!

There isn't a checkbox saying:

> "Make this subnet public."
> 

Instead, the **route table configuration determines whether the subnet is public or private**.

---

# 7. AWS Reserves 5 IP addresses in Every Subnet

This is **very important for SAA-C03 calculations**.

AWS reserves **5 IPv4 addresses** in every subnet.

They are:

```
1. Network address
2. VPC router
3. DNS
4. Future use
5. Broadcast address
```

AWS doesn't support IPv4 broadcast in a VPC, but the last address is still reserved.

---

# 8. Example — 10.0.0.0/24

A `/24` contains:

```
256 IP addresses
```

Range:

```
10.0.0.0
       ↓
10.0.0.255
```

AWS reserves:

```
10.0.0.0     → Network address
10.0.0.1     → VPC router
10.0.0.2     → Amazon-provided DNS
10.0.0.3     → Future use
10.0.0.255   → Broadcast address
```

Therefore:

```
256 - 5 = 251 usable IPv4 addresses
```

### 🧠 Exam formula

```
Usable IPv4 addresses = Total IP addresses - 5
```

---

# 9. The /27 vs /26 Exam Trick

Suppose the question says:

> You need **29 EC2 instances** in a subnet. What CIDR should you choose?
> 

### `/27`

```
2^(32-27)
= 2^5
= 32 addresses
```

AWS reserves 5:

```
32 - 5 = 27 usable
```

You need:

```
29
```

❌ `/27` isn't enough.

---

### `/26`

```
2^(32-26)
= 2^6
= 64 addresses
```

Usable:

```
64 - 5 = 59
```

You need:

```
29
```

✅ `/26` works.

### 🧠 Memory

> **Need 29 EC2 → /27 fails → /26 works.**
> 

---

# 10. What is an Internet Gateway?

**IGW = Internet Gateway**

An Internet Gateway provides a path between your VPC and the Internet.

Think:

```
Your VPC = Building
IGW = Internet entrance
Internet = Outside world
```

Architecture:

```
                Internet
                    │
                    │
              Internet Gateway
                    │
                    │
             ┌──────┴──────┐
             │     VPC     │
             │             │
             │   Subnet    │
             │     │       │
             │    EC2      │
             └─────────────┘
```

---

# 11. An IGW Alone DOES NOT Make EC2 Public

This is one of the **most important SAA-C03 concepts**.

Many beginners think:

```
EC2 → IGW → Internet
```

automatically works.

❌ No.

You need several pieces working together.

For an EC2 instance to have normal direct Internet connectivity, you generally need:

```
EC2
│
├── Subnet
│
├── Route table
│     └── 0.0.0.0/0 → IGW
│
├── Internet-routable IPv4 address
│
├── Security Group allowing appropriate traffic
│
└── Network ACL permitting traffic
```

---

# 12. The Most Important Piece: Route Table

A **route table tells AWS where network traffic should go.**

Think of it as a GPS/direction table.

Example:

```
Destination       Target

10.0.0.0/16       local
0.0.0.0/0         igw-123456
```

Meaning:

### Destination:

```
10.0.0.0/16
```

Target:

```
local
```

Traffic destined for resources inside the VPC stays within the VPC.

---

### Destination:

```
0.0.0.0/0
```

Target:

```
Internet Gateway
```

This means:

> Anything not belonging to the VPC goes toward the Internet Gateway.
> 

---

# 13. What does 0.0.0.0/0 mean?

It means:

> **All IPv4 destinations**
> 

So:

```
0.0.0.0/0 → IGW
```

means:

> Send Internet-bound IPv4 traffic to the Internet Gateway.
> 

### 🧠 Exam memory

```
0.0.0.0/0 = Internet / everywhere
```

---

# 14. Complete Public Subnet Architecture

Let's create a realistic application.

You have:

```
VPC:
10.0.0.0/16
```

Create two subnets:

```
Public Subnet:
10.0.1.0/24

Private Subnet:
10.0.2.0/24
```

Architecture:

```
                         INTERNET
                             │
                             │
                    Internet Gateway
                             │
                             │
                ┌────────────┴────────────┐
                │        VPC              │
                │      10.0.0.0/16        │
                │                         │
                │  Public Subnet          │
                │  10.0.1.0/24            │
                │        │                │
                │       EC2               │
                │        │                │
                │   Route Table           │
                │   0.0.0.0/0 → IGW       │
                │                         │
                │  Private Subnet         │
                │  10.0.2.0/24            │
                │        │                │
                │   Private EC2            │
                │                         │
                └─────────────────────────┘
```

---

# 15. How Does the Public EC2 Reach the Internet?

Suppose EC2 has:

```
Private IP:
10.0.1.10

Public IPv4:
54.x.x.x
```

It wants to access:

```
8.8.8.8
```

EC2 checks its route table.

It finds:

```
Destination        Target

10.0.0.0/16        local
0.0.0.0/0          IGW
```

8.8.8.8 doesn't belong to:

```
10.0.0.0/16
```

So:

```
EC2
 ↓
0.0.0.0/0
 ↓
Internet Gateway
 ↓
Internet
 ↓
8.8.8.8
```

---

# 16. Why Does the Route Table Need `0.0.0.0/0 → IGW`?

Because the IGW itself doesn't tell the subnet:

> "Send Internet traffic to me."
> 

The route table has to say:

```
0.0.0.0/0 → Internet Gateway
```

Therefore:

### IGW

Provides the **gateway/path**.

### Route table

Provides the **routing decision**.

Think:

> **IGW = Door**
> 
> 
> **Route Table = Direction to the door**
> 

---

# 17. Real Scenario — Web Application

Suppose you're deploying an e-commerce website.

You need:

- Load Balancer accessible from Internet
- EC2 application servers
- Database protected from Internet

Architecture:

```
                         INTERNET
                             │
                             ▼
                    Internet Gateway
                             │
                             ▼
              ┌─────────────────────────┐
              │          VPC             │
              │       10.0.0.0/16        │
              │                          │
              │   PUBLIC SUBNET          │
              │   10.0.1.0/24            │
              │                          │
              │       ALB                │
              │        │                 │
              │        │                 │
              │   PRIVATE SUBNET         │
              │   10.0.2.0/24            │
              │        │                 │
              │       EC2                │
              │        │                 │
              │   PRIVATE SUBNET         │
              │   10.0.3.0/24            │
              │        │                 │
              │       RDS                │
              │                          │
              └──────────────────────────┘
```

Why?

### ALB

Needs Internet access:

```
Public Subnet
```

### EC2

Doesn't need to be directly reachable from Internet:

```
Private Subnet
```

### RDS

Definitely shouldn't normally be directly exposed to Internet:

```
Private Subnet
```

This is a classic AWS architecture.
