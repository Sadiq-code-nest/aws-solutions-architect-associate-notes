# NACL & Security Groups

Absolutely. For **SAA-C03**, the easiest way to understand Security Groups vs NACLs is to visualize the traffic path first, then learn the rules.

# 🔐 Security Groups & NACLs — SAA-C03 Complete Concept

## 1. First: Where do they sit?

Think of an AWS VPC like an apartment building:

- **NACL = Security guard at the subnet entrance**
- **Security Group = Security guard at the EC2 instance door**

### Simple architecture

```
                         INTERNET
                            │
                            │
                       Internet Gateway
                            │
                            ▼
                    ┌─────────────────┐
                    │      VPC        │
                    │                 │
                    │   NACL          │
                    │  SUBNET LEVEL   │
                    │       │         │
                    │       ▼         │
                    │  Security       │
                    │  Group          │
                    │ INSTANCE LEVEL  │
                    │       │         │
                    │       ▼         │
                    │      EC2        │
                    └─────────────────┘
```

Traffic entering an EC2 instance conceptually passes through:

**Internet → IGW → NACL → Security Group → EC2**

For outbound traffic:

**EC2 → Security Group → NACL → IGW → Internet**

---

# 2. Security Group — Instance-Level Firewall

A **Security Group (SG)** controls network traffic **to and from an EC2 instance or network interface**.

Think:

> 🏠 **Security Group = Firewall attached to the EC2 instance**
> 

Example:

```
Internet
   │
   │ TCP 443
   ▼
┌──────────────┐
│     NACL     │
│   Subnet     │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Security     │
│ Group        │
│              │
│ ALLOW 443    │
└──────┬───────┘
       │
       ▼
    EC2 Web
    Server
```

If the SG says:

```
Inbound:
TCP 443
Source: 0.0.0.0/0
ALLOW
```

then HTTPS traffic can reach the EC2 instance **provided the NACL and routing also allow it**.

---

# 3. Security Group — Most Important Properties

| Property | Security Group |
| --- | --- |
| Level | **Instance / ENI level** |
| Rules | **ALLOW only** |
| DENY rules | ❌ No |
| Stateful? | ✅ Yes |
| Return traffic | Automatically allowed |
| Rule evaluation | All applicable rules evaluated |
| Applied automatically? | No, attach to EC2/ENI |
| Common use | Protect EC2 instances |

### 🧠 Memory Trick

> **SG = Stateful + Server**
> 

Both start with **S**.

---

# 4. What does Stateful mean?

This is **VERY important for the exam**.

Suppose your EC2 allows:

```
Inbound:
TCP 443
ALLOW
```

A user sends:

```
Client
10.1.1.10:50000
       │
       │ → TCP 443
       ▼
      EC2
```

The EC2 sends the response:

```
EC2
TCP 443
       │
       │ → Client's port 50000
       ▼
Client
```

You **don't need a separate outbound rule specifically allowing the response**.

Why?

Because SG is **stateful**.

```
                 SECURITY GROUP
                       │
Client ───────► EC2
       Request
                       │
Client ◄─────── EC2
       Response

       ✅ Return traffic automatically allowed
```

### Exam phrase

If you see:

> **"Return traffic is automatically allowed"**
> 

Think:

### 👉 Security Group

---

# 5. Security Group only supports ALLOW

Security Groups don't have explicit DENY rules.

Example:

```
Inbound

TCP 443 → ALLOW
TCP 22  → ALLOW
TCP 3306 → ❌ No rule
```

Port 3306 is effectively blocked because there is no matching allow rule.

You cannot write:

```
DENY 1.2.3.4
```

inside an SG.

For explicit **DENY**, think:

### 👉 NACL

---

# 6. NACL — Network Access Control List

A **Network ACL (NACL)** controls traffic **at the subnet level**.

Think:

> 🚪 **NACL = Security gate around the entire subnet**
> 

```
                     VPC
                      │
              ┌───────┴───────┐
              │    NACL       │
              │               │
              │    SUBNET     │
              │               │
              │   ┌───────┐   │
              │   │  SG   │   │
              │   └───┬───┘   │
              │       │       │
              │      EC2      │
              └───────────────┘
```

The NACL applies to **all resources/interfaces in that subnet**.

---

# 7. NACL — Most Important Properties

| Property | NACL |
| --- | --- |
| Level | **Subnet level** |
| Rules | ✅ ALLOW |
| DENY | ✅ DENY |
| Stateful? | ❌ Stateless |
| Return traffic | Must be explicitly allowed |
| Rule evaluation | **Lowest number first** |
| First matching rule | Wins |
| Automatically applies | Yes, to subnet |
| Common use | Subnet-level filtering / blocking IPs |

### 🧠 Memory Trick

> **NACL = Network + Numbered + Not Stateful**
> 

---

# 8. NACL Rules — Number Matters!

Suppose:

```
Rule #100
ALLOW 10.0.0.10/32

Rule #200
DENY 10.0.0.10/32
```

Which one wins?

### ✅ Rule #100

Because NACL evaluates rules from **lowest number to highest number**.

```
#100 → ALLOW ✅
#200 → DENY ❌

       ↓

FIRST MATCH WINS
```

Therefore:

> **Lower rule number = higher priority**
> 

### 🧠 Memory Trick

> **NACL = Number → First Match**
> 

---

# 9. NACL has explicit DENY

This is one of the most useful differences.

Suppose you want to block:

```
203.0.113.50
```

You can create:

```
Rule #100
DENY
203.0.113.50/32
```

The entire subnet is protected from that IP.

This makes NACL useful for:

> **Blocking a specific malicious IP at the subnet level.**
> 

---

# 10. NACL is Stateless — VERY IMPORTANT

This is probably the biggest exam trap.

Suppose:

```
Client
11.22.33.44
Source Port: 50105

        │
        │ HTTPS
        │ Destination Port 443
        ▼

Web Server
55.66.77.88
Port 443
```

The request is:

```
Client: 50105
      ↓
Server: 443
```

The response is:

```
Server: 443
      ↓
Client: 50105
```

The client's **50105** is an ephemeral port.

Because NACL is **stateless**, you must allow both directions.

---

# 11. Ephemeral Ports

A client usually chooses a temporary source port.

For example:

```
Client
IP: 11.22.33.44
Port: 50105
        │
        │ HTTPS
        ▼
Server
IP: 55.66.77.88
Port: 443
```

Response:

```
Server
Port: 443
        │
        ▼
Client
Port: 50105
```

So NACL needs rules allowing the return traffic.

Typical exam ranges you'll see include:

```
1024–65535
```

or Linux-style ephemeral ranges such as:

```
32768–60999
```

The exact range depends on the operating system and scenario.

### Exam clue

If the question says:

> "NACL is blocking return traffic"
> 

or

> "Need to allow ephemeral ports"
> 

Think:

### 👉 NACL is stateless.

---

# 12. SG vs NACL — The Big Picture

```
                    INTERNET
                       │
                       ▼
              ┌────────────────┐
              │      NACL      │
              │  SUBNET LEVEL  │
              │                │
              │ ALLOW / DENY   │
              │   STATELESS    │
              └───────┬────────┘
                      │
                      ▼
              ┌────────────────┐
              │ Security Group │
              │ INSTANCE LEVEL │
              │                │
              │     ALLOW      │
              │    STATEFUL    │
              └───────┬────────┘
                      │
                      ▼
                    EC2
```

---

# 13. Head-to-Head Comparison

| Feature | Security Group | NACL |
| --- | --- | --- |
| Works at | **Instance / ENI** | **Subnet** |
| Firewall around | EC2/network interface | Entire subnet |
| Allow rules | ✅ | ✅ |
| Deny rules | ❌ | ✅ |
| Stateful | ✅ | ❌ |
| Return traffic | Automatically allowed | Must explicitly allow |
| Rule priority | All applicable rules evaluated | Lowest number first |
| First match wins | ❌ | ✅ |
| Automatically applied | No | Yes, based on subnet association |
| Multiple possible | Multiple SGs can attach | One NACL associated with a subnet |
| Best for | Instance-level security | Subnet-level filtering |
| Block specific IP | Not with explicit DENY | ✅ Excellent use case |
| Ephemeral ports | Usually not separately needed for return traffic | Often required |
| Memory | **SG = Stateful Server** | **NACL = Numbered Network Gate** |

---

# 14. Real AWS Architecture Example

Imagine you're building a 3-tier application:

```
                     INTERNET
                         │
                         ▼
                ┌─────────────────┐
                │ Internet Gateway│
                └────────┬────────┘
                         │
                ┌────────▼────────┐
                │   Web NACL      │
                │  Subnet-level   │
                └────────┬────────┘
                         │
                  Public Subnet
                         │
                  ┌──────▼──────┐
                  │     ALB     │
                  │     SG      │
                  └──────┬──────┘
                         │
                         ▼
                ┌─────────────────┐
                │ Application     │
                │ Private Subnet  │
                │                 │
                │ NACL            │
                │      │          │
                │      ▼          │
                │    EC2 + SG     │
                └────────┬────────┘
                         │
                         │ TCP 3306
                         ▼
                ┌─────────────────┐
                │ Database Subnet │
                │                 │
                │ NACL            │
                │      │          │
                │      ▼          │
                │    RDS + SG     │
                └─────────────────┘
```

### Application Security Group

```
Inbound:
TCP 443
Source = ALB-SG
```

Meaning:

> Only traffic coming from the ALB's security group can reach the application servers on the required port.
> 

### Database Security Group

```
Inbound:
TCP 3306
Source = App-SG
```

Meaning:

> Only the application servers can access the database.
> 

This is a very common AWS architecture pattern.

---

# 15. Important: Security Group Can Reference Another SG

This is a **very common SAA-C03 question**.

Instead of:

```
DB-SG:

TCP 3306
Source: 10.0.2.0/24
```

you can use:

```
DB-SG:

TCP 3306
Source: APP-SG
```

Architecture:

```
        Application EC2
             │
          APP-SG
             │
             │ TCP 3306
             ▼
          DB-SG
             │
             ▼
          RDS
```

This is better than hardcoding IP addresses because instances can change IP addresses.

### 🧠 Exam clue

> "Allow database access only from application servers"
> 

Think:

### **Database SG → Source = Application SG**

---

# 16. NACL Doesn't Work Like SG References

Don't confuse the two.

NACL rules generally work with:

```
CIDR
IP address
protocol
port
```

Example:

```
ALLOW TCP 443
Source: 10.0.1.0/24
```

Think:

> **SG → Can reference another SG**
> 

> **NACL → Uses IP/CIDR-based rules**
> 

---

# 17. Default NACL

When you create a VPC, there's a **default NACL**.

For IPv4, it essentially allows all inbound and outbound traffic.

Conceptually:

```
Inbound:
ALLOW 0.0.0.0/0

Outbound:
ALLOW 0.0.0.0/0
```

The default NACL behaves as an open subnet-level firewall.

### Important

AWS recommends:

> **Don't modify the default NACL.**
> 

Instead, create a custom NACL when you actually need custom subnet-level filtering.

---

# 18. Custom NACL

A newly created custom NACL denies traffic by default.

```
Internet
   │
   ▼
Custom NACL
   │
   ├── No ALLOW rule
   │
   ▼
❌ Traffic blocked
```

You need to create appropriate inbound and outbound rules.

---

# 19. NACL Association

A subnet is associated with **one NACL at a time**.

Example:

```
VPC
│
├── Public Subnet A ──► Web-NACL
│
├── Public Subnet B ──► Web-NACL
│
├── Private Subnet A ─► App-NACL
│
└── Private Subnet B ─► App-NACL
```

You can associate the same NACL with multiple subnets.

---

# 20. The Classic Exam Trap

Question:

> A company wants to block a specific malicious IP address from accessing all EC2 instances in a subnet. Which solution should be used?
> 

### Answer:

**NACL**

Why?

Because:

```
NACL
 ↓
Subnet level
 ↓
All resources in subnet
 ↓
Explicit DENY
 ↓
Perfect for blocking an IP
```

---

# 21. Another Classic Exam Trap

Question:

> An EC2 instance allows inbound HTTPS traffic through its Security Group. The administrator does not configure an outbound rule specifically for the response. Will the response be allowed?
> 

### Answer:

**Yes — because Security Groups are stateful.**

---

# 22. Another Classic Trap

Question:

> An administrator configured an NACL to allow inbound TCP 443 but the client doesn't receive the response. What is likely missing?
> 

Think:

```
NACL
   ↓
STATELESS
   ↓
Return traffic
   ↓
Ephemeral port
```

Therefore, the outbound NACL rule for the return traffic may be missing.

---

# 23. How to Think During the Exam

When you see **Security Group**, immediately think:

```
EC2 / ENI
     ↓
Instance-level
     ↓
ALLOW only
     ↓
STATEFUL
     ↓
Return traffic automatic
     ↓
Can reference another SG
```

When you see **NACL**, immediately think:

```
SUBNET
   ↓
Subnet-level
   ↓
ALLOW + DENY
   ↓
STATELESS
   ↓
Return traffic needs rule
   ↓
Numbered rules
   ↓
Lowest number first
   ↓
First match wins
   ↓
Good for blocking IPs
```

---

# 🧠 Ultimate SAA-C03 Memory Trick

### **SG = "Security Guard at the Server"**

**S**erver → **S**tateful → **S**G

> **ALLOW only + Stateful + Instance level**
> 

### **NACL = "Network Gate"**

**N**etwork → **N**umbered → **N**on-stateful

> **ALLOW/DENY + Stateless + Subnet level + First match wins**
> 

---

## ⭐ 30-Second Exam Revision

| If question says... | Think... |
| --- | --- |
| EC2-level firewall | **Security Group** |
| Subnet-level firewall | **NACL** |
| Stateful | **Security Group** |
| Stateless | **NACL** |
| Explicit DENY | **NACL** |
| Allow only | **Security Group** |
| Block specific IP | **NACL** |
| Return traffic automatically allowed | **Security Group** |
| Need ephemeral port rules | **NACL** |
| Lowest rule number wins | **NACL** |
| First matching rule wins | **NACL** |
| All SG rules evaluated | **Security Group** |
| Reference another security group | **Security Group** |
| Protect all resources in subnet | **NACL** |
| Instance/ENI level | **Security Group** |
| Subnet level | **NACL** |

### The one sentence to memorize:

> **Security Group protects the instance and is stateful; NACL protects the subnet and is stateless.**
> 

That single distinction answers a **huge number of SAA-C03 SG/NACL questions**.
