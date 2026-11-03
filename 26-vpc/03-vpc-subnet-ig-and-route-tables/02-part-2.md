# 18. Public Subnet Route Table

Public subnet's route table:

| Destination | Target |
| --- | --- |
| `10.0.0.0/16` | local |
| `0.0.0.0/0` | Internet Gateway |

Therefore:

```
Public Subnet
       │
       ▼
Route Table
       │
       ├── VPC traffic → local
       │
       └── Internet traffic → IGW
```

---

# 19. Private Subnet Route Table

Private subnet could have:

| Destination | Target |
| --- | --- |
| `10.0.0.0/16` | local |
| `0.0.0.0/0` | NAT Gateway |

Then:

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

This allows **outbound Internet access** without giving the EC2 instance a public IP.

For SAA-C03:

> **Private EC2 → Internet → NAT Gateway**
> 

is a very important pattern.

---

# 20. IGW vs NAT Gateway

Don't confuse them.

| Feature | Internet Gateway | NAT Gateway |
| --- | --- | --- |
| Purpose | Internet connectivity for VPC resources | Outbound Internet access from private resources |
| Used by | Public resources | Private resources |
| Public IP required for EC2? | Normally yes for direct IPv4 Internet connectivity | No |
| Inbound Internet to private EC2 | ❌ | ❌ |
| Outbound Internet from private EC2 | Not directly | ✅ |
| Managed by AWS | ✅ | ✅ |
| Highly available | AWS-managed, horizontally scaled | Deploy per AZ for AZ-level resiliency |
| Typical location | VPC attachment | Public subnet |

### Memory:

```
Public EC2:
EC2 → IGW → Internet

Private EC2:
EC2 → NAT Gateway → IGW → Internet
```

---

# 21. One VPC ↔ One IGW

For the exam:

```
VPC A
  │
  └── IGW A

VPC B
  │
  └── IGW B
```

An Internet Gateway is attached to a VPC.

You don't attach one IGW to multiple VPCs simultaneously.

---

# 22. What Does "Public IPv4 Address" Actually Do?

Suppose:

```
EC2 private IP = 10.0.1.10
Public IPv4 = 3.20.x.x
```

The private IP is used within the VPC.

The public IP provides Internet-routable addressing.

So:

```
Internet
   │
   ▼
Public IP
   │
   ▼
IGW
   │
   ▼
Private IP
   │
   ▼
EC2
```

The Internet Gateway performs the necessary address translation between the instance's private address and its public IPv4 address.

---

# 23. Security Group Is Also Required

Here's a common exam trap.

Suppose you have:

```
EC2
Public IP
Public subnet
IGW
0.0.0.0/0 → IGW
```

But the Security Group says:

```
Inbound:
No HTTP
No HTTPS
No SSH
```

Can users access the EC2?

❌ No.

The network path exists, but the Security Group blocks the traffic.

### Think in layers:

```
Internet
   ↓
IGW
   ↓
Route Table
   ↓
Subnet
   ↓
Security Group
   ↓
EC2
```

---

# 24. Full Internet Access Checklist

If the question asks:

> "Why can't my EC2 instance access the Internet?"
> 

Check:

### 1. Is it in the right subnet?

Public subnet for direct Internet access.

### 2. Does the route table have:

```
0.0.0.0/0 → IGW
```

### 3. Does the instance have a public IPv4 address?

For direct IPv4 Internet access, yes.

### 4. Does the Security Group permit the traffic?

For outbound Internet access, check outbound rules.

### 5. Does the Network ACL allow the traffic?

NACLs are stateless, so check both directions as appropriate.

---

# 25. VPC → Subnet → Route Table → IGW Relationship

This is the **core mental model**.

```
                    AWS REGION
                        │
                        ▼
                      VPC
                  10.0.0.0/16
                        │
             ┌──────────┴──────────┐
             ▼                     ▼
       Public Subnet         Private Subnet
       10.0.1.0/24           10.0.2.0/24
             │                     │
             ▼                     ▼
        Route Table            Route Table
             │                     │
       0.0.0.0/0 → IGW       0.0.0.0/0 → NAT
             │                     │
             ▼                     ▼
            IGW                NAT Gateway
             │                     │
             └──────────┬──────────┘
                        ▼
                     Internet
```

---

# 26. How AWS Decides Where Traffic Goes

Suppose the route table is:

| Destination | Target |
| --- | --- |
| `10.0.0.0/16` | local |
| `0.0.0.0/0` | IGW |

EC2 sends traffic to:

```
10.0.5.20
```

AWS asks:

> Does `10.0.5.20` belong to `10.0.0.0/16`?
> 

Yes.

Therefore:

```
EC2 → local
```

Now EC2 sends to:

```
8.8.8.8
```

Does it belong to:

```
10.0.0.0/16?
```

No.

Then:

```
0.0.0.0/0 → IGW
```

So:

```
EC2 → IGW → Internet
```

---

# 27. Most Important CIDR Numbers for SAA-C03

| CIDR | Total IPv4 addresses | AWS usable in subnet |
| --- | --- | --- |
| `/16` | 65,536 | 65,531 |
| `/20` | 4,096 | 4,091 |
| `/24` | 256 | 251 |
| `/25` | 128 | 123 |
| `/26` | 64 | 59 |
| `/27` | 32 | 27 |
| `/28` | 16 | 11 |
| `/32` | 1 | Not a normal subnet size |

Remember:

```
Subnet usable IPv4 = Total - 5
```

---

# 28. SAA-C03 Exam Traps

### ❌ Trap 1

> "Attaching an Internet Gateway automatically makes the VPC public."
> 

Wrong.

You need the route:

```
0.0.0.0/0 → IGW
```

---

### ❌ Trap 2

> "A subnet is public because it has a public IP range."
> 

Wrong.

A subnet is considered public because its route table has a route to an IGW.

---

### ❌ Trap 3

> "All 256 IPs in a /24 subnet can be assigned to EC2."
> 

Wrong.

```
256 - 5 = 251
```

---

### ❌ Trap 4

> "A subnet can span multiple AZs."
> 

Wrong.

```
1 subnet → 1 AZ
```

---

### ❌ Trap 5

> "Private EC2 requires a public IPv4 address to download updates."
> 

Not necessarily.

Use:

```
Private EC2
   ↓
NAT Gateway
   ↓
IGW
   ↓
Internet
```

---

### ❌ Trap 6

> "NAT Gateway allows Internet users to initiate connections to private EC2."
> 

Wrong.

NAT Gateway is primarily for **outbound connections from private resources**.

---

### ❌ Trap 7

> "IGW is the same as a NAT Gateway."
> 

Wrong.

```
IGW = Internet connectivity for public resources

NAT Gateway = outbound Internet connectivity for private resources
```

---

# 29. Easy Real-World Scenario

Imagine you're running an online shopping application.

Your network:

```
VPC
10.0.0.0/16
```

You create:

```
Public Subnet:
10.0.1.0/24

Private Subnet:
10.0.2.0/24
```

### Internet Gateway

Attach:

```
VPC
 ↓
IGW
```

### Public route table

```
10.0.0.0/16 → local
0.0.0.0/0 → IGW
```

Put your ALB there.

### Private route table

```
10.0.0.0/16 → local
0.0.0.0/0 → NAT Gateway
```

Put application EC2 instances there.

### Database subnet

Keep RDS private.

Final architecture:

```
                         USERS
                           │
                           ▼
                       INTERNET
                           │
                           ▼
                         IGW
                           │
                           ▼
                  ┌─────────────────┐
                  │   PUBLIC SUBNET │
                  │                 │
                  │       ALB       │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ PRIVATE SUBNET  │
                  │                 │
                  │ EC2 EC2 EC2     │
                  └────────┬────────┘
                           │
                           ▼
                  ┌─────────────────┐
                  │ PRIVATE SUBNET  │
                  │                 │
                  │      RDS        │
                  └─────────────────┘
```

For EC2 to download OS updates:

```
EC2
 ↓
NAT Gateway
 ↓
IGW
 ↓
Internet
```

But Internet users **cannot directly initiate a connection** to those private EC2 instances through the NAT Gateway.

---

# 🧠 Final SAA-C03 Mental Model

Memorize this flow:

```
VPC
│
├── CIDR
│     └── Defines overall IP range
│
├── Subnet
│     └── Smaller range inside VPC
│     └── Lives in ONE AZ
│
├── Route Table
│     └── Decides where traffic goes
│
├── Internet Gateway
│     └── VPC ↔ Internet path
│
└── Security Group
      └── Controls allowed traffic
```

And the **most important public/private distinction**:

```
PUBLIC SUBNET

Route Table
0.0.0.0/0 → IGW
             │
             ▼
          Internet
```

```
PRIVATE SUBNET

Route Table
0.0.0.0/0 → NAT Gateway
                  │
                  ▼
                 IGW
                  │
                  ▼
              Internet
```

### 🔥 Exam memory tricks

**VPC:**

> **Big private AWS network**
> 

**Subnet:**

> **Smaller network inside VPC + one AZ**
> 

**Route Table:**

> **Traffic directions**
> 

**IGW:**

> **Internet door**
> 

**NAT Gateway:**

> **Private subnet's outbound Internet door**
> 

**`0.0.0.0/0`:**

> **Anywhere / Internet**
> 

**Subnet IP calculation:**

> **Total IPs − 5 = usable IPv4**
> 

**Public subnet:**

> **Route to IGW**
> 

**Private subnet:**

> **No direct route to IGW**
> 

**One sentence for the exam:**

> **VPC defines the network, subnets divide it by AZ, route tables decide where traffic goes, IGW provides the Internet path, and NAT Gateway lets private resources reach the Internet without making them directly Internet-accessible.**
>
