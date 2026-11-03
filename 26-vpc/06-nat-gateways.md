# NAT Gateway — SAA-C03 Complete Exam Notes

NAT Gateway is one of the most important VPC concepts for SAA-C03 because questions often test **private-subnet internet access, routing, AZ failure, security groups, and NAT Gateway vs NAT Instance**.

---

## 1. What is a NAT Gateway?

**NAT Gateway = AWS-managed Network Address Translation service.**

Its main purpose:

> Allow resources in a **private subnet** to initiate connections to the **internet**, without giving those resources public IPv4 addresses.
> 

Typical architecture:

```
Private EC2
     │
     ▼
NAT Gateway
     │
     ▼
Internet Gateway
     │
     ▼
Internet
```

### Real-world example

Imagine an EC2 application server in a private subnet:

```
Private EC2
10.0.2.10
```

It needs to:

- download OS updates
- download packages
- call an external API
- access a public website

But you don't want to give it a public IP.

So:

```
EC2 Private Subnet
       ↓
NAT Gateway
       ↓
Internet Gateway
       ↓
Internet
```

The EC2 remains **private**, while it can still make **outbound connections**.

---

# 2. NAT Gateway must be in a Public Subnet

For a **public NAT Gateway**, the NAT Gateway is deployed in a **public subnet**.

That public subnet's route table contains:

```
0.0.0.0/0 → Internet Gateway
```

The private subnet's route table contains:

```
0.0.0.0/0 → NAT Gateway
```

So:

```
                 INTERNET
                     ▲
                     │
              Internet Gateway
                     ▲
                     │
              Public Subnet
                     │
                NAT Gateway
                     ▲
                     │
              Private Subnet
                     │
                 EC2 Instance
```

### Important distinction

The private EC2 does **not** connect directly to the IGW.

❌ Incorrect:

```
Private EC2 → IGW → Internet
```

✅ Correct:

```
Private EC2 → NAT Gateway → IGW → Internet
```

---

# 3. Why does NAT Gateway need an Elastic IP?

A public NAT Gateway uses an **Elastic IP address**.

Example:

```
Private EC2
10.0.2.10
     ↓
NAT Gateway
EIP: 54.x.x.x
     ↓
Internet
```

When the EC2 accesses an internet service, the external service sees the NAT Gateway's public IP rather than the EC2's private IP.

### Exam clue

If you see:

> Private EC2 needs outbound internet access and must use a fixed public IPv4 address.
> 

Think:

**NAT Gateway + Elastic IP**

---

# 4. NAT Gateway is outbound-focused

NAT Gateway allows:

```
Private EC2 → Internet
```

It does **not** make the private EC2 publicly accessible.

For example:

```
Private EC2
     │
     ├──→ Google        ✅
     ├──→ Public API    ✅
     ├──→ Package repo  ✅
     │
     ←── Internet → EC2  ❌
```

This is one of the most important concepts.

> **NAT Gateway = private resources can initiate outbound internet connections.**
> 

---

# 5. Route tables are extremely important

A NAT Gateway doesn't automatically provide internet access.

You need correct routing.

### Public subnet route table

```
Destination       Target

10.0.0.0/16       local
0.0.0.0/0         Internet Gateway
```

### Private subnet route table

```
Destination       Target

10.0.0.0/16       local
0.0.0.0/0         NAT Gateway
```

Therefore:

```
Private EC2
    │
    │ 0.0.0.0/0
    ▼
NAT Gateway
    │
    │ 0.0.0.0/0
    ▼
Internet Gateway
    │
    ▼
Internet
```

### Exam trap 🚨

If the question says:

> Private subnet EC2 cannot access the internet.
> 

Check the private subnet route table.

Does it have:

```
0.0.0.0/0 → NAT Gateway
```

If not, that's likely the problem.

---

# 6. NAT Gateway vs Security Groups

A very important difference from NAT Instance:

### NAT Gateway

❌ No Security Group

You **don't attach a Security Group** to a NAT Gateway.

Traffic control is instead handled through things such as:

- Security Groups on the EC2 instances
- Network ACLs
- Route tables

### NAT Instance

✅ Has Security Group

Because it's an EC2 instance.

---

# 7. NAT Gateway cannot be used by EC2 in the same subnet

For SAA-C03, remember this rule from your notes:

> A NAT Gateway cannot be used by an EC2 instance in the **same subnet**.
> 

The intended architecture is:

```
Private Subnet A
       │
       ▼
NAT Gateway
       │
Public Subnet
```

Think:

> **NAT Gateway is designed to sit in a public subnet and serve private resources from other subnet(s).**
> 

---

# 8. NAT Gateway is AZ-specific

This is extremely important for exam questions.

A NAT Gateway is created in a **specific Availability Zone**.

For example:

```
Region
│
├── AZ-A
│   ├── Public Subnet
│   │     └── NAT Gateway A
│   │
│   └── Private Subnet A
│         └── EC2 A
│
└── AZ-B
    ├── Public Subnet
    │     └── NAT Gateway B
    │
    └── Private Subnet B
          └── EC2 B
```

For high availability:

```
Private AZ-A → NAT Gateway A

Private AZ-B → NAT Gateway B
```

---

# 9. Why do we need multiple NAT Gateways?

Suppose you have:

```
AZ-A                         AZ-B

Private EC2                  Private EC2
     │                            │
     ▼                            │
NAT Gateway A                     │
                                  │
                              NAT Gateway B
```

Now AZ-A goes down.

```
AZ-A ❌

NAT Gateway A ❌
EC2 A ❌
```

AZ-B continues working:

```
AZ-B ✅
NAT Gateway B ✅
EC2 B ✅
```

That's why for a highly available multi-AZ architecture, you normally use **one NAT Gateway per AZ**.

---

# 10. Very important exam trap: Don't rely on cross-AZ NAT

Imagine:

```
AZ-A                         AZ-B

Private EC2                  NAT Gateway
     │                            │
     └───────────────►────────────┘
```

This can technically be used, but it is generally **not the recommended highly available design**.

Why?

### Problem 1 — AZ dependency

If AZ-B has a failure:

```
AZ-B NAT Gateway ❌
```

AZ-A's private instances may lose outbound internet access.

### Problem 2 — Cross-AZ data transfer costs

Traffic crossing AZ boundaries can result in additional data transfer costs.

### Better design

```
AZ-A                         AZ-B

Private EC2                  Private EC2
     │                            │
     ▼                            ▼
NAT GW A                      NAT GW B
```

### Exam clue

> "Highly available NAT architecture across multiple AZs"
> 

Answer:

**Create a NAT Gateway in each AZ and route each private subnet to its local NAT Gateway.**

---

# 11. NAT Gateway is AWS managed

You don't manage:

- Operating system
- Patching
- NAT software
- Failover scripts
- EC2 instance sizing

AWS manages the NAT Gateway infrastructure.

That's a major advantage over NAT Instance.

---

# 12. Bandwidth

Your notes mention:

> **5 Gbps initial capacity, automatically scales up to 100 Gbps.**
> 

For exam purposes, the key idea is:

> **NAT Gateway provides high, automatically scalable bandwidth.**
> 

Don't make the 5 → 100 Gbps number the main thing you memorize. The conceptual distinction is much more useful.

---

# 13. NAT Gateway has a cost

NAT Gateway isn't free.

You pay for:

- NAT Gateway hourly usage
- Data processing through the NAT Gateway

Therefore, if an architecture unnecessarily routes huge amounts of traffic through NAT Gateway, it can become expensive.

### Exam scenario

Suppose private EC2 instances are accessing an AWS service such as S3.

If the architecture can use an appropriate **VPC endpoint**, that can avoid sending the traffic through NAT Gateway and potentially reduce NAT processing costs.

So:

```
Private EC2 → NAT Gateway → Internet
```

isn't automatically the best architecture for every AWS service.

---

# 14. NAT Gateway vs NAT Instance

This comparison is highly exam-relevant.

| Feature | NAT Gateway | NAT Instance |
| --- | --- | --- |
| Type | AWS managed service | EC2 instance |
| Management | AWS | You |
| Public subnet | ✅ | ✅ |
| Elastic IP | ✅ | ✅ |
| Private subnet internet access | ✅ | ✅ |
| Security Group | ❌ | ✅ |
| Source/Destination Check | Not applicable | **Must disable** |
| High availability | Resilient within AZ | Must build yourself |
| Multi-AZ HA | NAT Gateway per AZ | Multiple instances + failover |
| Scaling | AWS managed | Depends on EC2 instance |
| Bandwidth | Automatically scales | Depends on instance type |
| OS management | None | You manage |
| Patching | AWS | You |
| Failover | AWS-managed within AZ | You need to configure |
| Cost | Hourly + data processing | EC2 + network costs |
| Modern recommendation | ✅ Preferred | ⚠️ Legacy |
| Bastion Host | ❌ | Can technically be used, but not its primary purpose |

### Memory trick

> **NAT Gateway = AWS manages it.**
> 

> **NAT Instance = I manage the EC2.**
> 

---

# 15. NAT Gateway vs Internet Gateway

Don't confuse them.

|  | Internet Gateway | NAT Gateway |
| --- | --- | --- |
| Purpose | Internet connectivity for VPC resources with public addressing/routing | Outbound internet access for private resources |
| AWS managed | ✅ | ✅ |
| Public subnet | Used with public routing | Normally deployed there |
| Private EC2 uses it directly? | ❌ | ❌ |
| Requires route table | ✅ | ✅ |
| Elastic IP | No | Public NAT Gateway uses EIP |
| Security Group | No | No |

### Easy mental model

**IGW = door to the internet**

**NAT Gateway = translator for private resources**

---

# 16. Public vs Private architecture

## Public EC2

```
Internet
    │
    ▼
Internet Gateway
    │
    ▼
Public Subnet
    │
    ▼
EC2 with Public IP
```

## Private EC2

```
Private EC2
    │
    ▼
NAT Gateway
    │
    ▼
Internet Gateway
    │
    ▼
Internet
```

---

# 17. Regional NAT Gateway — important distinction

Your notes mention **Regional NAT Gateway (RNAT)**.

This is a newer NAT Gateway capability that changes the traditional architecture.

Traditional model:

```
AZ-A → NAT Gateway A
AZ-B → NAT Gateway B
AZ-C → NAT Gateway C
```

A **Regional NAT Gateway** is designed to be associated with the VPC rather than being tied to only one AZ in the same way as the traditional zonal NAT Gateway model.

Conceptually:

```
                Regional NAT Gateway
                   /      |      \
                  /       |       \
               AZ-A      AZ-B     AZ-C
              Private   Private   Private
```

The important exam concept is:

> **Regional NAT Gateway can simplify multi-AZ NAT deployment because you don't need a separate NAT Gateway in every AZ.**
> 

Your notes also highlight:

- It has its own route tables.
- It can serve resources across AZs.
- It can automatically expand to new AZs.
- You don't need public subnets specifically to host the Regional NAT Gateway.

### Don't mix these two concepts

**Traditional NAT Gateway:**

```
AZ-specific
↓
For HA → NAT Gateway per AZ
```

**Regional NAT Gateway:**

```
VPC-level
↓
Designed to simplify multi-AZ deployment
```

For SAA-C03, pay attention to **which architecture the question is describing**.

---

# 18. One complete scenario

Suppose your company has:

```
AWS Region
│
├── AZ-A
│   ├── Public Subnet
│   │    └── NAT Gateway A
│   │
│   └── Private Subnet
│        └── EC2 App A
│
└── AZ-B
    ├── Public Subnet
    │    └── NAT Gateway B
    │
    └── Private Subnet
         └── EC2 App B
```

Route tables:

### Private Subnet A

```
0.0.0.0/0 → NAT Gateway A
```

### Private Subnet B

```
0.0.0.0/0 → NAT Gateway B
```

### Public Subnets

```
0.0.0.0/0 → Internet Gateway
```

Traffic:

```
EC2 A
  ↓
NAT GW A
  ↓
IGW
  ↓
Internet
```

and:

```
EC2 B
  ↓
NAT GW B
  ↓
IGW
  ↓
Internet
```

This is a strong **multi-AZ, highly available architecture**.

---

# 🧠 SAA-C03 Memory Map

Memorize this:

```
PRIVATE EC2
     │
     │ 0.0.0.0/0
     ▼
NAT GATEWAY
     │
     ▼
INTERNET GATEWAY
     │
     ▼
INTERNET
```

And:

### NAT Gateway = **P-N-E-M**

**P** → Public subnet

**N** → NAT for private resources

**E** → Elastic IP

**M** → Managed by AWS

For HA:

> **One NAT Gateway per AZ** — traditional design.
> 

For security:

> **No Security Group on NAT Gateway.**
> 

For routing:

> **Private subnet → NAT Gateway; NAT Gateway's subnet → IGW.**
> 

For modern architecture:

> **NAT Gateway > NAT Instance**
> 

### 🔥 One-line exam takeaway

> **NAT Gateway is an AWS-managed, highly scalable NAT service that allows private-subnet resources to initiate outbound internet connections without public IPs; it normally sits in a public subnet, uses an Elastic IP, requires proper route tables and an IGW, has no Security Group, and for traditional multi-AZ HA you deploy one per AZ.**
>
