# NAT Instances

Absolutely. For **SAA-C03**, treat NAT Instance as a **legacy concept** you should recognize in questions, while knowing that **NAT Gateway is normally the preferred modern solution**.

# NAT Instance — SAA-C03 Exam Notes

## 1. What is a NAT Instance?

**NAT = Network Address Translation**

A **NAT Instance** is an EC2 instance configured to allow resources in a **private subnet** to access the **public internet**, while preventing the internet from directly initiating connections to those private resources.

### Simple idea

> **Private EC2 → NAT Instance → Internet**
> 

The private EC2 doesn't need a public IP.

---

# 2. Why do we need NAT?

Imagine this architecture:

```
                    INTERNET
                        │
                        ▼
                 Internet Gateway
                        │
                ┌───────┴────────┐
                │  Public Subnet  │
                │                 │
                │  NAT Instance   │
                │  Public/EIP     │
                └───────┬─────────┘
                        │
                  Private Route
                        │
                ┌───────▼────────┐
                │ Private Subnet  │
                │                 │
                │  EC2 Instance   │
                │  10.0.0.20      │
                └─────────────────┘
```

The private EC2 might need to:

- Download software updates
- Access external APIs
- Download packages
- Reach public AWS endpoints

But you **don't want to give the EC2 a public IPv4 address**.

So:

```
Private EC2
    ↓
NAT Instance
    ↓
Internet Gateway
    ↓
Internet
```

---

# 3. Real-world example

Suppose you have an application server:

```
EC2
Private IP: 10.0.0.20
```

It's running in a private subnet.

It needs to download a package from:

```
example.com
```

But it has **no public IP**.

The private subnet's route table says:

```
0.0.0.0/0 → NAT Instance
```

The NAT Instance is in the public subnet.

The NAT Instance has:

```
Private IP: 10.0.0.10
Elastic IP: 12.34.56.78
```

Traffic:

```
10.0.0.20
     ↓
10.0.0.10
NAT Instance
     ↓
12.34.56.78
     ↓
Internet
```

The external website sees:

```
12.34.56.78
```

It doesn't see the private EC2's:

```
10.0.0.20
```

That's the **Network Address Translation** part.

---

# 4. NAT Instance must be in a PUBLIC subnet

This is a very important exam point.

```
NAT Instance
     ↓
PUBLIC SUBNET
```

Why?

Because the NAT Instance itself needs to reach the internet.

The public subnet needs a route:

```
0.0.0.0/0 → Internet Gateway
```

And the NAT Instance needs a **public IPv4 address / Elastic IP**.

### Exam clue

If the question says:

> "NAT Instance is deployed in a private subnet"
> 

🚨 **Wrong architecture.**

A NAT Instance should be in a **public subnet**.

---

# 5. What must be configured?

For a NAT Instance to work, remember these **5 things**:

### ① NAT Instance in Public Subnet

```
Public Subnet
      ↓
NAT Instance
```

### ② Elastic IP

Attach an **Elastic IP** to the NAT Instance.

```
NAT Instance
Private IP → 10.0.0.10
Public/EIP → 12.34.56.78
```

### ③ Disable Source/Destination Check

This is a **very important SAA exam clue**.

Normally, EC2 expects:

> "Traffic coming to this instance should be for this instance."
> 

But a NAT Instance forwards traffic **on behalf of other instances**.

Therefore:

> **Disable Source/Destination Check on the NAT Instance.**
> 

### ④ Private subnet route table

Private subnet:

```
Destination       Target

10.0.0.0/16       local
0.0.0.0/0         NAT Instance
```

The key is:

```
0.0.0.0/0 → NAT Instance
```

### ⑤ Security Groups

The NAT Instance needs appropriate security group rules.

For example:

```
Private EC2
     ↓
HTTP/HTTPS
     ↓
NAT Instance
     ↓
Internet
```

---

# 6. How traffic actually flows

Let's say:

```
Private EC2:
10.0.0.20

NAT Instance:
10.0.0.10
EIP: 12.34.56.78

Internet server:
50.60.4.10
```

The private EC2 sends:

```
Source:      10.0.0.20
Destination: 50.60.4.10
```

NAT Instance translates the source:

```
Source:      12.34.56.78
Destination: 50.60.4.10
```

The internet server responds to:

```
12.34.56.78
```

The NAT Instance translates the response back:

```
Destination: 10.0.0.20
```

So the private EC2 can communicate with the internet.

---

# 7. Important: NAT does NOT mean inbound internet access

This is a common exam trap.

NAT Instance is primarily for:

> **Private → Internet**
> 

Not:

> **Internet → Private EC2**
> 

For example:

```
Private EC2 → Google/API/package repository
       ✅

Internet → Private EC2
       ❌
```

The private instance doesn't become publicly reachable just because it uses NAT.

---

# 8. NAT Instance vs NAT Gateway

This is much more important for SAA-C03.

| Feature | NAT Instance | NAT Gateway |
| --- | --- | --- |
| Type | EC2 instance | AWS managed service |
| Managed by | You | AWS |
| Location | Public subnet | Public subnet |
| Private subnet can access internet | ✅ | ✅ |
| Elastic IP | Required | Required for public NAT Gateway |
| Source/Destination Check | **Disable** | Not applicable |
| High availability | ❌ Not by default | Better design per AZ |
| Scaling | You manage | AWS managed scaling |
| Maintenance | You manage OS/software | AWS manages |
| Security Group | **Yes** | **No SG** |
| Network ACL | Yes | Yes |
| Performance | Depends on EC2 instance size | AWS managed |
| Preferred today | ❌ Legacy | ✅ Usually preferred |
| Exam keyword | Legacy / custom control | Scalable / managed / highly available |

### Easy memory

> **NAT Instance = "I manage the server."**
> 

> **NAT Gateway = "AWS manages the NAT."**
> 

---

# 9. Why NAT Gateway is usually better

Suppose you have:

```
AZ-A
Private Subnet
     ↓
NAT Gateway A

AZ-B
Private Subnet
     ↓
NAT Gateway B
```

This gives a more resilient architecture.

If NAT Gateway A has an issue:

```
AZ-A affected
AZ-B still works
```

For highly available applications, AWS commonly recommends deploying NAT Gateways across AZs and routing each private subnet to the NAT Gateway in its own AZ.

---

# 10. NAT Instance High Availability

A single NAT Instance is a **single point of failure**.

Bad:

```
Private Subnets
      ↓
 Single NAT Instance
      ↓
   Internet
```

If NAT Instance dies:

```
Private EC2
    X
Internet
```

You could make it more resilient with:

```
AZ-A                         AZ-B

NAT Instance A              NAT Instance B
      ↑                            ↑
Private Subnet A             Private Subnet B
```

and use automation/Auto Scaling appropriately.

But this adds operational complexity.

That's one major reason **NAT Gateway is preferred**.

---

# 11. NAT Instance vs Bastion Host

Don't confuse these two.

|  | NAT Instance | Bastion Host |
| --- | --- | --- |
| Main purpose | Private EC2 → Internet | Admin → Private EC2 |
| Direction | Outbound | Administrative inbound |
| Example | Download updates | SSH into private EC2 |
| Public subnet | ✅ | ✅ |
| EIP | Usually required | Usually required |
| Source/Destination check | Disable | Normally not |
| Replaces NAT Gateway? | Legacy NAT solution | ❌ |

### Memory trick

**NAT = OUT**

**Bastion = IN**

```
NAT:
Private EC2 → Internet

Bastion:
Admin → Private EC2
```

---

# 12. NAT Instance vs Internet Gateway

Another common confusion.

### Internet Gateway

Allows a VPC resource with appropriate public addressing/routing to communicate with the internet.

```
EC2 with public IP
      ↓
Internet Gateway
      ↓
Internet
```

### NAT Instance

Allows private resources to reach the internet **without giving those private resources public IPs**.

```
Private EC2
     ↓
NAT Instance
     ↓
Internet Gateway
     ↓
Internet
```

### Memory

> **IGW = Internet door of the VPC**
> 

> **NAT = Private subnet's outbound translator**
> 

---

# 13. The complete exam architecture

```
                         INTERNET
                            │
                            ▼
                   ┌─────────────────┐
                   │ Internet Gateway│
                   └────────┬────────┘
                            │
                     Public Subnet
                            │
                    ┌───────▼────────┐
                    │  NAT Instance  │
                    │                │
                    │ Private IP     │
                    │ 10.0.0.10      │
                    │                │
                    │ Elastic IP      │
                    │ 12.34.56.78    │
                    └───────┬────────┘
                            │
                     Private Subnet
                            │
                    ┌───────▼────────┐
                    │  Private EC2   │
                    │ 10.0.0.20      │
                    └────────────────┘
```

Private route table:

```
0.0.0.0/0 → NAT Instance
```

Public route table:

```
0.0.0.0/0 → Internet Gateway
```

NAT Instance:

```
✅ Public subnet
✅ Elastic IP
✅ Disable Source/Destination Check
✅ Security Group
```

---

# 14. SAA-C03 exam traps 🚨

### Trap 1

> Private EC2 needs internet access without public IP.
> 

Think:

**NAT Gateway / NAT Instance**

---

### Trap 2

> NAT Instance can't forward traffic.
> 

Check:

**Source/Destination Check must be disabled.**

---

### Trap 3

> NAT Instance has no public IP.
> 

Give it:

**Elastic IP**

---

### Trap 4

> NAT Instance is deployed in a private subnet.
> 

❌ Wrong.

It belongs in:

**Public subnet**

---

### Trap 5

> Private subnet route table points directly to IGW.
> 

Usually ❌ wrong for private outbound internet access.

Use:

```
Private subnet
     ↓
NAT Gateway / NAT Instance
     ↓
IGW
     ↓
Internet
```

---

### Trap 6

> Need a highly available managed solution for private subnet internet access.
> 

Think:

**NAT Gateway**

Not NAT Instance.

---

### Trap 7

> Need SSH access from administrator to private EC2.
> 

Think:

**Bastion Host** (or modern AWS Systems Manager approaches), **not NAT**.

---

# 🧠 Final memory trick

Remember:

> **PRIVATE → NAT → IGW → INTERNET**
> 

And for NAT Instance:

**P-E-S-R**

- **P** = Public subnet
- **E** = Elastic IP
- **S** = Source/Destination check **disabled**
- **R** = Private subnet **Route → NAT Instance**

### One-line exam takeaway

> **NAT Instance is a legacy EC2-based NAT solution placed in a public subnet with an Elastic IP, disabled Source/Destination Check, and a private-subnet default route pointing to it; for modern scalable/highly available designs, prefer NAT Gateway.**
>
