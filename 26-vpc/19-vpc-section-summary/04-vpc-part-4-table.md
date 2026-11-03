# 37. VPC Networking Master Diagram

This is the picture you should have in your head for SAA-C03:

```
                              INTERNET
                         ▲              ▲
                         │              │
                       IGW              │
                         │              │
              ┌──────────┴──────────────┴───────┐
              │              VPC                 │
              │           10.0.0.0/16            │
              │                                  │
              │  PUBLIC SUBNET                   │
              │  ┌──────────────────────────┐    │
              │  │ Bastion / ALB / Public EC2│   │
              │  └─────────────┬────────────┘    │
              │                │                 │
              │              Route               │
              │                │                 │
              │  PRIVATE SUBNET│                 │
              │  ┌─────────────▼────────────┐    │
              │  │ Private EC2 / Application│    │
              │  └─────────────┬────────────┘    │
              │                │                 │
              │         NAT Gateway              │
              │                │                 │
              │                └──────► IGW      │
              │                                  │
              │  IPv6 Private EC2                │
              │          │                       │
              │          ▼                       │
              │   Egress-only IGW ─────► Internet
              │                                  │
              │  Gateway Endpoint ─────► S3      │
              │                                  │
              │  Interface Endpoint ──► AWS Svc  │
              │                                  │
              └──────────────────────────────────┘
```

Outside the VPC:

```
Corporate DC
     │
     ├── Site-to-Site VPN ──► VGW ──► VPC
     │
     └── Direct Connect ────► DXGW ──► VPCs

VPC A ── VPC Peering ── VPC B

VPCs ───────────────► Transit Gateway ◄──── VPN/DX

Provider VPC
     │
     NLB
     │
 PrivateLink
     │
Consumer VPC
```

---

# 38. VPC Component Comparison — Master Table

| Component | Main Purpose | Operates At | Internet? | Key Exam Keyword |
| --- | --- | --- | --- | --- |
| **VPC** | Isolated network | Region/network | Not by itself | Network boundary |
| **Subnet** | Divide VPC | AZ | Depends on route | One AZ |
| **Route Table** | Direct traffic | Subnet association | Depends on route | Where traffic goes |
| **IGW** | Internet connectivity | VPC | ✅ | Public Internet |
| **NAT Gateway** | Private IPv4 outbound Internet | Subnet/AZ | Outbound | IPv4 private |
| **NAT Instance** | Legacy NAT | EC2 | Outbound | Disable S/D check |
| **Egress-only IGW** | Private IPv6 outbound Internet | VPC | Outbound | IPv6 |
| **Bastion Host** | SSH into private EC2 | EC2 | Admin access | Jump box |
| **Security Group** | Resource firewall | ENI/instance | N/A | Stateful |
| **NACL** | Subnet firewall | Subnet | N/A | Stateless |
| **VPC Peering** | Connect VPCs | VPC-to-VPC | Private | Non-transitive |
| **VPC Endpoint** | Private AWS service access | VPC | Avoid public path | S3/DDB |
| **VPC Flow Logs** | Observe network traffic | VPC/Subnet/ENI | N/A | ACCEPT/REJECT |
| **Site-to-Site VPN** | Connect on-prem ↔ AWS | Network | Uses Internet | IPsec |
| **VGW** | AWS VPN endpoint | VPC | VPN | AWS side |
| **CGW** | On-prem VPN endpoint representation | Customer side | VPN | Customer side |
| **VPN CloudHub** | Connect multiple sites | VPN hub | Internet | Hub-and-spoke |
| **Direct Connect** | Dedicated private connectivity | Physical connection | Private | Consistent |
| **DX Gateway** | Connect DX to multiple VPCs/Regions | AWS networking | Private | Multi-Region |
| **PrivateLink** | Private service exposure | Service | Private | NLB + endpoint |
| **Transit Gateway** | Central network hub | Regional | Private | Transitive |
| **Traffic Mirroring** | Copy traffic for inspection | ENI | N/A | Security analysis |

---

# 39. The Most Important "Which One?" Table

When the exam describes this...

| Exam scenario | Think |
| --- | --- |
| Create isolated network in AWS | **VPC** |
| Divide VPC network | **Subnet** |
| Subnet belongs to a specific AZ | **Subnet** |
| Decide where packets go | **Route Table** |
| Public Internet access | **Internet Gateway** |
| Private IPv4 → Internet | **NAT Gateway** |
| Private IPv6 → Internet | **Egress-only IGW** |
| SSH into private EC2 | **Bastion Host** |
| Firewall on EC2/ENI | **Security Group** |
| Firewall on subnet | **NACL** |
| Stateful firewall | **Security Group** |
| Stateless firewall | **NACL** |
| Block a specific IP at subnet level | **NACL** |
| Two VPCs privately communicate | **VPC Peering** |
| Many VPCs need central connectivity | **Transit Gateway** |
| Private EC2 → S3 | **Gateway Endpoint** |
| Private EC2 → most AWS services | **Interface Endpoint** |
| Private service shared with another VPC | **PrivateLink** |
| Observe accepted/rejected network traffic | **VPC Flow Logs** |
| On-prem → AWS quickly over encrypted Internet | **Site-to-Site VPN** |
| Dedicated private on-prem → AWS | **Direct Connect** |
| Multiple VPCs/Regions through DX | **Direct Connect Gateway** |
| VPN backup for DX | **Site-to-Site VPN** |
| Multiple offices connected through VPN hub | **VPN CloudHub** |
| Copy traffic for security inspection | **Traffic Mirroring** |

---

# 40. The Three Most Important Network Paths

## A. Public IPv4 EC2

```
EC2
 ↓
Public Subnet
 ↓
Route Table
 ↓
IGW
 ↓
Internet
```

---

## B. Private IPv4 EC2

```
EC2
 ↓
Private Subnet
 ↓
Route Table
 ↓
NAT Gateway
 ↓
IGW
 ↓
Internet
```

---

## C. Private IPv6 EC2

```
EC2
 ↓
Private Subnet
 ↓
Route Table
 ↓
Egress-only IGW
 ↓
Internet
```

Memorize these three paths.

---

# 41. AWS Networking Decision Tree

```
I need connectivity...
          │
          ├── Internet?
          │      │
          │      ├── Public IPv4/IPv6
          │      │       → Internet Gateway
          │      │
          │      ├── Private IPv4 outbound
          │      │       → NAT Gateway
          │      │
          │      └── Private IPv6 outbound
          │              → Egress-only IGW
          │
          ├── AWS Service privately?
          │      │
          │      ├── S3/DynamoDB
          │      │       → Gateway Endpoint
          │      │
          │      └── Most other services
          │              → Interface Endpoint
          │
          ├── VPC ↔ VPC?
          │      │
          │      ├── Small/simple
          │      │       → VPC Peering
          │      │
          │      └── Many VPCs
          │              → Transit Gateway
          │
          ├── On-prem ↔ AWS?
          │      │
          │      ├── Quick + encrypted
          │      │       → Site-to-Site VPN
          │      │
          │      └── Dedicated + consistent
          │              → Direct Connect
          │
          └── Private service → customer VPC?
                 │
                 └── PrivateLink
```

---

# 42. Most Important SAA-C03 Traps

### Trap #1

> "Private subnet needs Internet access."
> 

Don't automatically choose IGW.

For IPv4:

**NAT Gateway**

---

### Trap #2

> "Private IPv6 instance needs outbound Internet."
> 

**Egress-only Internet Gateway**

---

### Trap #3

> "Internet Gateway is attached to subnet."
> 

❌ Wrong.

IGW is attached to the:

> **VPC**
> 

Route tables determine which subnets use it.

---

### Trap #4

> "Security Group supports DENY."
> 

❌ Wrong.

SG:

> **Allow only**
> 

NACL:

> **Allow + Deny**
> 

---

### Trap #5

> "NACL automatically permits response traffic."
> 

❌ Wrong.

NACL is:

> **Stateless**
> 

Think:

> **Ephemeral ports**
> 

---

### Trap #6

> "VPC Peering allows A → B → C."
> 

❌ Wrong.

VPC Peering is:

> **Non-transitive**
> 

---

### Trap #7

> "Direct Connect encrypts traffic."
> 

❌ Not by default.

DX is:

> **Private, but not encrypted by default.**
> 

For encryption:

> **DX + VPN**
> 

---

### Trap #8

> "Need S3 access from private EC2."
> 

Don't automatically choose NAT Gateway.

Consider:

> **S3 Gateway Endpoint**
> 

---

### Trap #9

> "Many VPCs and VPN/DX connections."
> 

Think:

> **Transit Gateway**
> 

---

### Trap #10

> "Private service exposed to customers without VPC peering."
> 

Think:

> **PrivateLink**
> 

---

# 43. Final Memory Map

If you remember only this before the exam:

```
                         AWS VPC
                            │
          ┌─────────────────┼─────────────────┐
          │                 │                 │
       SUBNET            ROUTING           SECURITY
          │                 │                 │
       One AZ          Route Table       SG + NACL
                            │
          ┌─────────────────┼───────────────────┐
          │                 │                   │
         IGW              NAT GW             EIGW
          │                 │                   │
      Public IPv4/IPv6   Private IPv4      Private IPv6
        Internet          Internet           Internet

AWS SERVICE ACCESS
        │
        ├── S3/DynamoDB → Gateway Endpoint
        │
        └── Most services → Interface Endpoint

VPC ↔ VPC
        │
        ├── Small/simple → VPC Peering
        │
        └── Large-scale → Transit Gateway

ON-PREMISES ↔ AWS
        │
        ├── Fast + encrypted → Site-to-Site VPN
        │
        └── Dedicated + consistent → Direct Connect

SERVICE SHARING
        │
        └── PrivateLink → NLB + Interface Endpoint

NETWORK VISIBILITY
        │
        ├── VPC Flow Logs → ACCEPT/REJECT analysis
        │
        └── Traffic Mirroring → Copy packets for inspection
```

## 🧠 The ultimate SAA-C03 one-liners

> **VPC = Your AWS network**
> 

> **Subnet = A section of the VPC in one AZ**
> 

> **Route Table = Where should traffic go?**
> 

> **IGW = Internet for public resources**
> 

> **NAT Gateway = Private IPv4 → Internet**
> 

> **Egress-only IGW = Private IPv6 → Internet**
> 

> **Bastion = SSH jump box**
> 

> **SG = Stateful instance/ENI firewall**
> 

> **NACL = Stateless subnet firewall**
> 

> **Peering = Direct VPC-to-VPC, non-transitive**
> 

> **Endpoint = Private access to AWS services**
> 

> **Flow Logs = See ACCEPT/REJECT network traffic**
> 

> **VPN = Encrypted connection over Internet**
> 

> **Direct Connect = Dedicated private connection**
> 

> **PrivateLink = Privately consume a service**
> 

> **Transit Gateway = Central hub for many networks**
> 

> **Traffic Mirroring = Copy traffic for inspection**
> 

### ⭐ The most important mental model

**Don't memorize VPC components separately. Think about the traffic path:**

**Where is the resource? → What IP version? → Where does the route table send traffic? → Which gateway/service handles it? → Does SG/NACL permit it?**

If you can answer those five questions, a very large portion of **SAA-C03 VPC scenario questions** becomes much easier.
