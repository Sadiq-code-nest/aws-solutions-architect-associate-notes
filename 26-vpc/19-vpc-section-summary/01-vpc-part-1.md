# VPC Part: 1

Below is a **complete VPC SAA-C03 revision note** designed so that someone who reads it from top to bottom gets the **whole VPC picture**, understands how the components connect, and can distinguish similar services in exam questions.

# AWS VPC — Complete SAA-C03 Summary & Exam Revision Note

## 0. The Big Picture First

Think of an AWS VPC as **your own private network inside AWS**.

A typical architecture looks like this:

```
                         INTERNET
                             │
                    ┌────────▼────────┐
                    │ Internet Gateway│
                    └────────┬────────┘
                             │
                 ┌───────────┴───────────┐
                 │         VPC            │
                 │     10.0.0.0/16       │
                 │                       │
        ┌────────▼────────┐    ┌────────▼────────┐
        │ Public Subnet A │    │ Private Subnet A│
        │ 10.0.1.0/24     │    │ 10.0.2.0/24     │
        │                 │    │                 │
        │ ALB / Bastion   │    │ App EC2         │
        │ Public EC2      │    │                 │
        └────────┬────────┘    └────────┬────────┘
                 │                      │
                 │                      ▼
                 │                 NAT Gateway
                 │                      │
                 └──────────────────────┘
                                        │
                                     Internet
```

The key idea:

> **VPC = network → Subnet = section of network → Route Table = traffic direction → IGW/NAT/Endpoint = gateways to somewhere else → SG/NACL = traffic security**
> 

---

# 1. VPC — Virtual Private Cloud

### Simple meaning

A **VPC is your logically isolated network inside AWS**.

You decide:

- IP address ranges
- Subnets
- Routing
- Internet connectivity
- Network security

### Example

Suppose your company creates:

```
VPC
CIDR: 10.0.0.0/16
```

That gives the VPC a private IP range.

You can divide it into:

```
10.0.1.0/24 → Public subnet
10.0.2.0/24 → Private subnet
10.0.3.0/24 → Database subnet
```

### Exam idea

> **VPC defines the overall network boundary.**
> 

---

# 2. CIDR — Your IP Address Range

CIDR defines an IP range.

Example:

```
10.0.0.0/16
```

The `/16` means the first 16 bits are fixed.

For exam purposes:

| CIDR | Total IPv4 addresses |
| --- | --- |
| `/32` | 1 |
| `/30` | 4 |
| `/29` | 8 |
| `/28` | 16 |
| `/27` | 32 |
| `/26` | 64 |
| `/24` | 256 |
| `/20` | 4,096 |
| `/16` | 65,536 |
| `/8` | 16,777,216 |

### Important VPC CIDR limits

Typical SAA-C03 knowledge:

- VPC IPv4 CIDR can be between `/16` and `/28`
- You can add multiple CIDR blocks to a VPC
- CIDRs should **not overlap** with networks that need to communicate with your VPC

### Private IPv4 ranges

| Range | CIDR |
| --- | --- |
| `10.0.0.0 – 10.255.255.255` | `10.0.0.0/8` |
| `172.16.0.0 – 172.31.255.255` | `172.16.0.0/12` |
| `192.168.0.0 – 192.168.255.255` | `192.168.0.0/16` |

---

# 3. Subnet

### Simple meaning

A **subnet is a smaller network inside your VPC**.

Most importantly:

> **A subnet belongs to exactly one Availability Zone.**
> 

Example:

```
Region: us-east-1

VPC: 10.0.0.0/16

        ┌───────────────┐
        │ us-east-1a    │
        │ Public Subnet │
        │ 10.0.1.0/24   │
        └───────────────┘

        ┌───────────────┐
        │ us-east-1b    │
        │ Private Subnet│
        │ 10.0.2.0/24   │
        └───────────────┘
```

### Public vs Private Subnet

A subnet isn't inherently public or private.

Its **route table determines whether it has a route to an Internet Gateway**.

| Subnet | Typical route | Meaning |
| --- | --- | --- |
| Public | `0.0.0.0/0 → IGW` | Internet reachable |
| Private IPv4 | `0.0.0.0/0 → NAT Gateway` | Outbound Internet |
| Private IPv6 | `::/0 → Egress-only IGW` | Outbound IPv6 Internet |
| Isolated | No Internet route | No Internet access |

### ⭐ Exam trick

> **Public subnet = route to IGW**
> 

Not:

> "It has a public IP, therefore it's public."
> 

---

# 4. AWS Reserves 5 IP Addresses

This is an extremely important exam calculation.

Suppose:

```
10.0.1.0/24
```

There are:

```
256 total IPs
```

AWS reserves 5.

So usable:

```
256 - 5 = 251
```

AWS reserves:

```
10.0.1.0     → Network address
10.0.1.1     → VPC router
10.0.1.2     → Amazon DNS
10.0.1.3     → Future use
10.0.1.255   → Broadcast address
```

AWS doesn't support IPv4 broadcast, but the last address is still reserved.

### Common exam question

Need **29 usable IPv4 addresses**.

`/27`:

```
32 - 5 = 27 ❌
```

`/26`:

```
64 - 5 = 59 ✅
```

Therefore:

> **Use `/26`.**
> 

---

# 5. Route Tables

This is one of the **most important VPC concepts**.

### Simple meaning

A route table tells AWS:

> **"Where should this traffic go?"**
> 

Example:

```
Destination       Target
--------------------------------
10.0.0.0/16       local
0.0.0.0/0         igw-xxxx
```

Meaning:

```
10.0.0.0/16 → stay inside VPC

Everything else → Internet Gateway
```

---

## Route table examples

### Public subnet

```
Destination       Target
--------------------------------
10.0.0.0/16       local
0.0.0.0/0         Internet Gateway
```

### Private IPv4 subnet

```
Destination       Target
--------------------------------
10.0.0.0/16       local
0.0.0.0/0         NAT Gateway
```

### Private IPv6 subnet

```
Destination       Target
--------------------------------
10.0.0.0/16       local
2001:db8::/56     local
::/0              Egress-only IGW
```

### VPC Peering

```
10.0.0.0/16       local
172.16.0.0/16     VPC Peering Connection
```

### Gateway Endpoint

```
S3 prefix list → Gateway Endpoint
```

---

# 6. Internet Gateway — IGW

### What is it?

An **Internet Gateway** provides Internet connectivity between your VPC and the Internet.

It supports:

- IPv4
- IPv6

### Important

Creating an IGW alone **does not make a subnet public**.

You need:

```
EC2
 ↓
Subnet
 ↓
Route Table
 ↓
IGW
 ↓
Internet
```

And the EC2 needs appropriate public addressing for direct Internet communication.

### Important properties

- Attached at VPC level
- One VPC can have one attached IGW
- Highly available and horizontally scalable
- AWS managed
- Supports IPv4 and IPv6

---

# 7. Public EC2 Internet Architecture

```
                  INTERNET
                      │
                      ▼
              Internet Gateway
                      │
                      ▼
              Public Route Table
                0.0.0.0/0 → IGW
                      │
                      ▼
                Public Subnet
                      │
                      ▼
                  EC2 Instance
```

For an IPv4 EC2 to directly communicate with the Internet, it needs a public IPv4 address or Elastic IP as appropriate.

---

# 8. NAT Gateway

### Problem

Private EC2 needs to access the Internet:

```
Private EC2
    ↓
Download updates
    ↓
Internet
```

But you don't want Internet users initiating connections to that EC2.

Use:

> **NAT Gateway**
> 

### Architecture

```
Private EC2
    │
    ▼
Private Route Table
0.0.0.0/0 → NAT Gateway
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

### NAT Gateway is:

- AWS managed
- For IPv4
- Outbound Internet access for private resources
- Deployed in a subnet/AZ
- Requires an Internet Gateway for Internet access
- Uses public connectivity through its public IPv4/EIP
- No Security Group attached to the NAT Gateway itself

### Important HA concept

A NAT Gateway is resilient within its AZ.

For multi-AZ architecture:

```
AZ-A                       AZ-B

Private EC2                Private EC2
    ↓                          ↓
NAT Gateway A              NAT Gateway B
```

Avoid depending on one AZ's NAT Gateway for another AZ's workloads when designing for AZ failure.

---

# 9. NAT Instance

Older approach.

```
Private EC2
     ↓
NAT Instance
     ↓
IGW
     ↓
Internet
```

Important exam clues:

| Requirement | NAT Instance |
| --- | --- |
| EC2-based | ✅ |
| Managed by AWS | ❌ |
| Disable source/destination check | ✅ |
| Public subnet | ✅ |
| Elastic IP | Typically required |
| Security Group | Required |
| Maintenance | You manage |
| Scaling | You manage |
| Modern preferred choice | NAT Gateway |

### Exam clue

If the question explicitly says:

> **"NAT instance"**
> 

Remember:

> **Disable Source/Destination Check.**
> 

---

# 10. NAT Gateway vs NAT Instance

| Feature | NAT Gateway | NAT Instance |
| --- | --- | --- |
| Managed | AWS | You |
| Type | Managed service | EC2 instance |
| Source/destination check | N/A | Must disable |
| Scaling | AWS-managed | You manage |
| HA | Create per-AZ for resilient architecture | You build it |
| Security Group | No | Yes |
| Maintenance | AWS | You |
| Exam preference | Usually preferred | Legacy |

---

# 11. Egress-only Internet Gateway

This is basically the **IPv6 equivalent of the NAT Gateway's outbound-only purpose**, but **not NAT**.

### Used for:

> Private IPv6 resources needing outbound Internet access without allowing new inbound connections from the Internet.
> 

```
Private IPv6 EC2
       ↓
Egress-only IGW
       ↓
Internet
```

### Memory

> **IPv4 → NAT Gateway**
> 
> 
> **IPv6 → Egress-only IGW**
> 

---

# 12. Bastion Host

### Problem

You have private EC2:

```
Private EC2
No public IP
```

You need SSH access for administration.

Solution:

> **Bastion Host**
> 

A Bastion Host is an EC2 instance in a public subnet used as a controlled jump point to private instances.

```
Your Computer
      │
      │ SSH :22
      ▼
Bastion Host
Public Subnet
      │
      │ SSH :22
      ▼
Private EC2
```

### Security Groups

Bastion SG:

```
Inbound:
SSH :22
Source = trusted corporate/home CIDR
```

Private EC2 SG:

```
Inbound:
SSH :22
Source = Bastion SG
```

### Exam memory

> **Bastion = Jump box**
> 

---

# 13.1 ENI (Elastic Network Interface) — AWS VPC

An **ENI is a virtual network card** for an AWS resource.

Think of it like a **network adapter/NIC** that connects an EC2 instance to the VPC.

- NIC
    
    A network adapter, or Network Interface Card (NIC), is **a hardware component that connects a computer to a wired or wireless network**
    

Simple example

```
VPC
 │
Subnet
 │
 └── ENI
      ├── Private IP: 10.0.1.10
      ├── Security Group
      └── MAC address
           │
           ▼
        EC2 Instance
```

The ENI provides the **network connectivity** for the EC2 instance.

### What can an ENI have?

An ENI can have:

- **Private IP address**
- **Security Groups**
- **MAC address**
- Optional **Elastic IP / public IP association**

### Why is it useful?

You can **attach/detach an ENI** to supported resources. For example, you could move a secondary ENI from one EC2 instance to another while keeping its private IP and security-group configuration.

### 🧠 SAA-C03 shortcut

> **ENI = Virtual network card inside a VPC**
> 

And remember:

**EC2 → ENI → Private IP → VPC network**

### Don't confuse:

- **ENI** → network connectivity
- **EBS** → block storage
- **EFS** → shared file storage

**One-line memory:**

> **ENI = the "network card" that gives an AWS resource access to the VPC.**
> 

# 13.2 Security Groups — SG

Security Groups act as a **virtual firewall at the ENI ( Elastic Network Interface )/instance level**.

### Characteristics

- Stateful
- Allow rules only
- No explicit deny rules
- All rules evaluated
- Associated with network interfaces/resources
- Return traffic automatically allowed

Example:

```
Internet
   ↓
SG: allow TCP 443
   ↓
EC2
```

If EC2 sends a response:

```
EC2
 ↓
Internet
```

The return traffic is automatically allowed because SG is stateful.

---

# 14. NACL — Network Access Control List

NACL is a firewall at the **subnet level**.

### Characteristics

- Stateless
- Supports ALLOW and DENY
- Rules evaluated in order
- Lowest rule number has highest priority
- First matching rule wins
- Applies automatically to resources in the subnet

Example:

```
NACL Rule #100
DENY 203.0.113.10/32
```

This can block that IP at the subnet boundary.

---

# 15. Security Group vs NACL

| Feature | Security Group | NACL |
| --- | --- | --- |
| Level | ENI/Instance | Subnet |
| Stateful | ✅ | ❌ |
| Stateless | ❌ | ✅ |
| Allow | ✅ | ✅ |
| Deny | ❌ | ✅ |
| Rule evaluation | All applicable rules | Lowest number → first match |
| Return traffic | Automatically allowed | Must explicitly allow |
| Applies automatically | No, associated to resource | Yes, subnet association |
| Great for blocking specific IP | Less suitable | ✅ |

### Ultimate memory

> **SG = Stateful + Server/ENI**
> 

> **NACL = Network + Numbered + Stateless**
> 
- **Details NACL with SG**
    
    # NACL — Network Access Control List
    
    A **Network ACL (NACL)** is a **security filter for an entire subnet** in an AWS VPC.
    
    Think:
    
    > **NACL = security gate at the entrance of a subnet.**
    > 
    
    ```
    Internet
       ↓
    [NACL]
       ↓
    Subnet
     ├── EC2
     ├── EC2
     └── EC2
    ```
    
    It controls which **inbound and outbound traffic** is allowed to/from the subnet.
    
    ---
    
    ## 🔐 NACL Example
    
    Suppose your subnet contains web servers:
    
    ```
    Subnet: 10.0.1.0/24
            ↓
          NACL
            ↓
       Web Servers
    ```
    
    You could configure:
    
    ```
    Inbound:
    80  → ALLOW
    443 → ALLOW
    22  → DENY
    ```
    
    Meaning:
    
    - HTTP → ✅
    - HTTPS → ✅
    - SSH → ❌
    
    And you can separately configure outbound rules.
    
    ---
    
    # ⭐ NACL vs Security Group
    
    This is **very important for SAA-C03**.
    
    | Feature | Security Group (SG) | NACL |
    | --- | --- | --- |
    | Applied to | **ENI / instance** | **Subnet** |
    | State | **Stateful** | **Stateless** |
    | Rules | Allow only | **Allow + Deny** |
    | Return traffic | Automatically allowed | Must explicitly allow |
    | Rule evaluation | All applicable rules | **Lowest numbered rule first** |
    | Main purpose | Resource-level security | Subnet-level traffic control |
    
    ---
    
    # 1. Stateful vs Stateless — MOST IMPORTANT
    
    ### Security Group = Stateful
    
    Suppose:
    
    ```
    Client → EC2 : 443
    ```
    
    SG allows HTTPS.
    
    The response:
    
    ```
    EC2 → Client
    ```
    
    is automatically allowed because SG is **stateful**.
    
    Think:
    
    > **SG remembers the connection.**
    > 
    
    ---
    
    ### NACL = Stateless
    
    Suppose NACL allows:
    
    ```
    Client → EC2 : 443
    ```
    
    You also need to allow the **return traffic** in the outbound NACL rules.
    
    ```
    Inbound NACL
    Client → EC2
         ✅
    
    Outbound NACL
    EC2 → Client
         ✅
    ```
    
    Think:
    
    > **NACL does NOT remember the connection.**
    > 
    
    ---
    
    # 2. NACL Can Explicitly DENY
    
    This is a huge exam clue.
    
    ### Security Group
    
    ```
    ALLOW 443
    ```
    
    SG doesn't have explicit **DENY** rules.
    
    ### NACL
    
    Can have:
    
    ```
    100 → ALLOW 443
    110 → DENY 443
    ```
    
    Therefore:
    
    > **Need to explicitly block a specific IP → NACL**
    > 
    
    ---
    
    # 3. NACL Applies to the Whole Subnet
    
    Suppose:
    
    ```
    Subnet A
       │
       ├── EC2-1
       ├── EC2-2
       └── EC2-3
    ```
    
    One NACL is associated with the subnet.
    
    Its rules affect traffic entering/leaving that subnet.
    
    Whereas SG can be attached to individual resources/ENIs:
    
    ```
    EC2-1 → SG-A
    EC2-2 → SG-B
    EC2-3 → SG-A
    ```
    
    ---
    
    # 🏢 Real-World Example
    
    Imagine an office building.
    
    ### NACL = Building entrance security
    
    ```
                  Building
                     │
              [NACL Security Gate]
                     │
           ┌─────────┼─────────┐
           ▼         ▼         ▼
         Team A    Team B    Team C
    ```
    
    The building gate can say:
    
    > 🚫 Block everyone from this particular IP address.
    > 
    
    ### Security Group = Individual office door
    
    ```
    Building
       ↓
    Office door (SG)
       ↓
    Server
    ```
    
    The server's SG says:
    
    > "Only allow HTTPS traffic to me."
    > 
    
    So:
    
    > **NACL protects the subnet.**
    > 
    
    > **SG protects the resource/ENI.**
    > 
    
    ---
    
    # 🔥 SAA-C03 Exam Clues
    
    | Question says... | Think... |
    | --- | --- |
    | **Subnet-level security** | NACL |
    | **Instance/resource-level security** | Security Group |
    | **Stateless** | NACL |
    | **Stateful** | Security Group |
    | **Explicit DENY** | NACL |
    | **Allow + deny rules** | NACL |
    | **Only allow rules** | Security Group |
    | **Return traffic automatically allowed** | Security Group |
    | **Must allow return traffic separately** | NACL |
    | **Rule number / lowest number first** | NACL |
    | **Block a specific IP at subnet level** | NACL |
    
    ---
    
    ## 🧠 Super-short memory trick
    
    > **SG = Stateful + Server**
    > 
    
    > **NACL = Network + Stateless + Allow/Deny**
    > 
    
    Or simply:
    
    **SG → Resource**
    
    **NACL → Subnet**
    
    That's the core distinction you need for SAA-C03.
