# VPC Part: 5 - Connection

Yes — **this is the right way to understand VPC**. Instead of thinking "what does this service do?", think:

> **"What is connected to what, and what is on each side?"**
> 

One important correction first: **a Route Table is not a physical network device that traffic passes through.** It is a set of routing rules **associated with a subnet**. The subnet uses those rules to decide the next hop.

---

# 1. VPC

```
                 ┌──────────────────┐
                 │       VPC        │
                 │   10.0.0.0/16   │
                 └──────────────────┘
```

Think:

```
VPC
 │
 ├── Subnet
 ├── Route Table
 ├── Internet Gateway
 ├── NAT Gateway
 ├── VPC Endpoint
 ├── Transit Gateway attachment
 └── other resources
```

The VPC is basically the **container/network boundary**.

---

# 2. Subnet

A subnet lives **inside the VPC**.

```
              VPC
               │
               │ contains
               ▼
        ┌───────────────┐
        │    Subnet     │
        │ 10.0.1.0/24   │
        └───────────────┘
```

And EC2 lives inside the subnet:

```
VPC
 │
 └── Subnet
      │
      ├── EC2
      ├── EC2
      └── EC2
```

---

# 3. Route Table ⭐

This one is slightly different.

A Route Table is **associated with a subnet**.

```
             VPC
              │
              ▼
           Subnet
              │
              │ associated with
              ▼
        ┌───────────────┐
        │  Route Table  │
        └───────────────┘
```

The route table itself doesn't "connect" two things like a cable.

Instead, it contains instructions:

```
Route Table

Destination          Target
──────────────────────────────
10.0.0.0/16          local
0.0.0.0/0            igw-xxxx
10.1.0.0/16          tgw-xxxx
```

Meaning:

```
Traffic destination
       │
       ▼
Route Table
       │
       ├── 10.0.0.0/16 ──► local
       │
       ├── 0.0.0.0/0 ────► IGW
       │
       └── 10.1.0.0/16 ──► Transit Gateway
```

So remember:

> **Route Table → is associated with Subnet.**
> 

And:

> **Route Table → tells traffic which target to use.**
> 

---

# 4. Internet Gateway — IGW

Your example is exactly the right mental model.

```
          INTERNET
             │
             │
             ▼
      ┌──────────────┐
      │     IGW      │
      └──────┬───────┘
             │
             │ attached to
             ▼
            VPC
```

So:

```
Internet ←──→ IGW ←──→ VPC
```

More accurately:

```
Internet
    │
    │
    ▼
   IGW
    │
    │ VPC attachment
    ▼
   VPC
```

The IGW is **attached to the VPC**, not to a subnet.

Then the route table points toward it:

```
EC2
 │
 ▼
Subnet
 │
 ▼
Route Table
 │
 │ 0.0.0.0/0 → IGW
 ▼
 IGW
 │
 ▼
Internet
```

---

# 5. NAT Gateway

NAT Gateway has a very important placement rule.

It sits in a **public subnet**.

```
                 VPC
                  │
          ┌───────┴────────┐
          │                │
          ▼                ▼
    Public Subnet     Private Subnet
          │                │
          │                │
     NAT Gateway           EC2
          │                │
          │                │
          └───────┐        │
                  │        │
                  ▼        │
                 IGW ◄─────┘
                  │
                  ▼
               Internet
```

But the actual routing is:

```
Private EC2
     │
     ▼
Private Route Table
     │
     │ 0.0.0.0/0 → NAT Gateway
     ▼
NAT Gateway
     │
     ▼
Public Subnet Route Table
     │
     │ 0.0.0.0/0 → IGW
     ▼
IGW
     │
     ▼
Internet
```

So NAT's two sides are conceptually:

```
Private side                    Public side

Private EC2
    │
    ▼
Private subnet
    │
    ▼
NAT Gateway
    │
    ▼
Public subnet
    │
    ▼
IGW
    │
    ▼
Internet
```

### Key point

**NAT Gateway is not directly connected to the Internet.**

It reaches the Internet **through the IGW**.

---

# 6. VPC Endpoint

This is another special one.

Instead of:

```
Private EC2
    │
    ▼
NAT
    │
    ▼
IGW
    │
    ▼
Internet
    │
    ▼
S3
```

you can have:

```
Private EC2
    │
    ▼
Private Route Table
    │
    ▼
VPC Endpoint
    │
    ▼
AWS Service
     (e.g. S3)
```

So:

```
EC2 ←→ Subnet ←→ Route Table
                    │
                    ▼
              VPC Endpoint
                    │
                    ▼
               AWS Service
```

### Gateway Endpoint

For example:

```
Private Subnet
      │
      ▼
Route Table
      │
      │ S3 prefix → Endpoint
      ▼
Gateway Endpoint
      │
      ▼
S3
```

The **Gateway Endpoint is associated with route tables**.

---

# 7. Interface Endpoint

Interface Endpoint works differently.

It creates an **ENI inside your subnet**.

```
                 VPC
                  │
                  ▼
             Private Subnet
                  │
          ┌───────┴────────┐
          │                │
          ▼                ▼
         EC2       Endpoint ENI
                       │
                       │
                       ▼
                  AWS Service
```

So think:

```
EC2
 │
 ▼
Endpoint ENI
 │
 ▼
AWS Service
```

The Endpoint ENI is also protected using a **Security Group**.

---

# 8. Transit Gateway

Transit Gateway is like a **central router/interchange**.

```
             VPC A
               │
               │ TGW attachment
               ▼
        ┌────────────────┐
        │ Transit Gateway│
        └───────┬────────┘
                │
                │ TGW attachment
                ▼
             VPC B
```

With multiple VPCs:

```
                VPC A
                  │
                  │
                  ▼
             ┌─────────┐
             │   TGW   │
             └─────────┘
              ▲   ▲   ▲
              │   │   │
             VPC B VPC C VPN
```

But again, the **VPC route table must send traffic to TGW**:

```
EC2
 │
 ▼
Subnet
 │
 ▼
Route Table
 │
 │ 10.1.0.0/16 → TGW
 ▼
Transit Gateway
 │
 ▼
VPC B
```

---

# 9. Bastion Host

Bastion Host is simply an **EC2 instance placed in a public subnet**.

```
Internet
    │
    ▼
   IGW
    │
    ▼
Public Subnet
    │
    ▼
Bastion EC2
    │
    │ SSH
    ▼
Private Subnet
    │
    ▼
Private EC2
```

The important connection is:

```
Internet
   ↓
IGW
   ↓
Public Subnet
   ↓
Bastion
   ↓
Private EC2
```

The Bastion isn't a special VPC networking component. It's just an EC2 used as a **jump host**.

---

# 10. Security Group

Security Group attaches to the **ENI/network interface of a resource**.

Think:

```
Subnet
  │
  ▼
EC2
 │
 └──── Security Group
```

Not:

```
❌ Subnet
    │
    ▼
 Security Group
```

Instead:

```
             EC2
              │
              ▼
       ┌──────────────┐
       │Security Group│
       └──────────────┘
```

Conceptually:

```
Traffic
   │
   ▼
Security Group
   │
   ▼
EC2
```

And SG is **stateful**.

---

# 11. NACL

NACL is associated with a **subnet**.

```
              VPC
               │
               ▼
             Subnet
               │
               ▼
             NACL
```

Think of it as guarding the **entrance/exit of the subnet**:

```
                    NACL
                     │
                     ▼
          ┌─────────────────────┐
          │       SUBNET        │
          │                     │
Internet ─► NACL → EC2          │
          │                     │
          └─────────────────────┘
```

So:

```
NACL → Subnet level
SG   → ENI/Instance level
```

---

# 12. VPC Flow Logs

Flow Logs are not really "in the traffic path."

They're more like a monitoring camera.

```
                  Traffic
                     │
          ┌──────────┼──────────┐
          │          │          │
          ▼          ▼          ▼
         EC2       Subnet      ENI
          │          │          │
          └──────────┼──────────┘
                     │
                     ▼
                Flow Logs
                     │
              ┌──────┴──────┐
              ▼             ▼
        CloudWatch          S3
```

So:

```
❌ Traffic → Flow Logs → EC2
```

No.

Instead:

```
Traffic ───────────────► EC2
    │
    │ recorded/observed
    ▼
Flow Logs
```

---

# 🔥 Now put all connections into ONE picture

This is the diagram I'd memorize:

```
                              INTERNET
                                  │
                                  │
                                  ▼
                           ┌────────────┐
                           │    IGW     │
                           └─────┬──────┘
                                 │
                         attached to VPC
                                 │
                                 ▼
┌───────────────────────────────────────────────────────────────┐
│                             VPC                               │
│                                                               │
│                                                               │
│  ┌──────────────────────┐       ┌──────────────────────────┐ │
│  │    PUBLIC SUBNET     │       │     PRIVATE SUBNET       │ │
│  │                      │       │                          │ │
│  │  Route Table         │       │  Route Table              │ │
│  │      │               │       │      │                   │ │
│  │      │ 0/0 → IGW     │       │      │ 0/0 → NAT          │ │
│  │      │               │       │      │                   │ │
│  │      ▼               │       │      ▼                   │ │
│  │  Bastion EC2         │       │   Application EC2        │ │
│  │      │               │       │      │                   │ │
│  │      │               │       │      │                   │ │
│  │  NAT Gateway         │◄──────┼──────┘                   │ │
│  │      │               │       │                          │ │
│  └──────┼───────────────┘       └──────────────────────────┘ │
│         │                                                     │
│         │                                                     │
│         ▼                                                     │
│        IGW ───────────────────────────────► INTERNET          │
│                                                               │
│                                                               │
│        PRIVATE EC2                                             │
│             │                                                 │
│             │ route to endpoint                               │
│             ▼                                                 │
│      ┌──────────────┐                                         │
│      │ VPC Endpoint │──────────────────────► S3 / AWS Service │
│      └──────────────┘                                         │
│                                                               │
│                                                               │
│      PRIVATE EC2                                             │
│          │                                                    │
│          ▼                                                    │
│     Route Table                                              │
│          │                                                    │
│          │ 10.1.0.0/16 → TGW                                 │
│          ▼                                                    │
│   ┌──────────────────┐                                       │
│   │ Transit Gateway  │──────────────► VPC B                   │
│   └──────────────────┘                                       │
│                                                               │
│                                                               │
│   NACL ─────────► associated with SUBNET                      │
│                                                               │
│   SG ───────────► attached to EC2/ENI                         │
│                                                               │
│   Flow Logs ────► observe traffic → CloudWatch/S3             │
│                                                               │
└───────────────────────────────────────────────────────────────┘
```

# 🧠 The connection cheat sheet

This is probably the **most useful table for you**:

| Component | Connected/Attached to | Other side / Purpose |
| --- | --- | --- |
| **VPC** | Contains subnets/resources | Network boundary |
| **Subnet** | Inside VPC | Contains EC2, NAT, ENIs, etc. |
| **Route Table** | **Associated with Subnet** | Points traffic to targets |
| **IGW** | **Attached to VPC** | VPC ↔ Internet |
| **NAT Gateway** | **Lives in Public Subnet** | Private subnet → IGW → Internet |
| **VPC Gateway Endpoint** | **Route Table** | Private subnet → AWS service such as S3 |
| **VPC Interface Endpoint** | **ENI inside Subnet** | Private subnet → AWS service |
| **Transit Gateway** | VPC via **TGW attachment** | VPC ↔ VPC / VPN / Direct Connect |
| **Bastion Host** | EC2 in **Public Subnet** | Admin access → Private EC2 |
| **Security Group** | **ENI / EC2** | Instance-level stateful firewall |
| **NACL** | **Subnet** | Subnet-level stateless firewall |
| **VPC Flow Logs** | VPC/subnet/ENI traffic observation | Logs → CloudWatch/S3 |

## ⭐ One sentence to lock it into your brain

```
VPC
 │
 ├── contains → Subnet
 │                │
 │                ├── associated with → Route Table
 │                │                         │
 │                │                         ├──→ IGW
 │                │                         ├──→ NAT
 │                │                         ├──→ VPC Endpoint
 │                │                         └──→ Transit Gateway
 │                │
 │                ├── protected by → NACL
 │                │
 │                └── contains → EC2
 │                                  │
 │                                  └── protected by → Security Group
 │
 ├── attached to → IGW
 │
 └── connected via → Transit Gateway
```

**The key distinction:**

**Route Table is the "decision maker"; it is not a pipe.** It says *"for this destination, send traffic to this target."* Then the target—IGW, NAT Gateway, Endpoint, TGW, etc.—actually provides the next networking path.
