# VPC Peering

Absolutely. Let’s make **VPC Peering** crystal clear for SAA-C03, focusing on **why it exists, how traffic flows, what you must configure, limitations, and exam traps**.

# VPC Peering — SAA-C03 Exam Notes

## 1. What is VPC Peering?

**VPC Peering = private networking connection between two VPCs.**

It allows resources in **VPC-A** to communicate privately with resources in **VPC-B** using the AWS private network.

Think:

> **“Two separate VPCs, but I want them to communicate privately as if they are part of the same network.”**
> 

Traffic does **not** need to go through the public Internet.

### Simple example

Suppose you have:

```
VPC-A — Production
10.0.0.0/16

        VPC Peering

VPC-B — Shared Services
20.0.0.0/16
```

An EC2 instance:

```
10.0.1.10
```

can privately communicate with:

```
20.0.1.10
```

through the VPC peering connection.

---

# 2. Real-World Scenario

Imagine a company has two VPCs:

```
                    AWS Region
┌──────────────────────────────────────────────┐
│                                              │
│   VPC-A                         VPC-B        │
│   Production                   Database      │
│                                              │
│  EC2 App ───────── VPC Peering ─────── RDS   │
│  10.0.1.10                       20.0.1.10   │
│                                              │
└──────────────────────────────────────────────┘
```

The application in VPC-A needs to access a database/service in VPC-B.

Instead of:

```
EC2 → Internet → Internet → Database
```

you can use:

```
EC2 → VPC Peering → Database
```

It's **private AWS networking**.

---

# 3. The Most Important Requirement: CIDRs MUST NOT Overlap

This is one of the biggest SAA-C03 exam points.

### ❌ Not allowed

```
VPC-A: 10.0.0.0/16
VPC-B: 10.0.0.0/16
```

The address ranges overlap.

You **cannot create VPC peering** between them.

### ✅ Allowed

```
VPC-A: 10.0.0.0/16

VPC-B: 20.0.0.0/16
```

No overlap → peering can be established.

### Exam keyword

> **VPC Peering → non-overlapping CIDRs**
> 

---

# 4. VPC Peering Does NOT Automatically Configure Routing

This is another major exam trap.

Creating the peering connection alone is **not enough**.

You need to update the **route tables**.

Example:

```
VPC-A
10.0.0.0/16
       |
       | Peering
       |
VPC-B
20.0.0.0/16
```

Suppose EC2-A:

```
10.0.1.10
```

needs to communicate with EC2-B:

```
20.0.1.10
```

The route table in VPC-A needs something like:

```
Destination       Target
10.0.0.0/16       local
20.0.0.0/16       pcx-xxxx
```

And VPC-B needs the reverse:

```
Destination       Target
20.0.0.0/16       local
10.0.0.0/16       pcx-xxxx
```

`pcx-xxxx` = VPC Peering Connection.

### Very important

> **Peering connection + Route Table routes = actual communication**
> 

---

# 5. Traffic Flow

Let's visualize the complete architecture:

```
                VPC-A
             10.0.0.0/16
        ┌─────────────────────┐
        │                     │
        │   EC2-A             │
        │   10.0.1.10         │
        │      │              │
        │      ▼              │
        │   Route Table       │
        │   20.0.0.0/16       │
        │      │              │
        └──────┼──────────────┘
               │
               │ VPC Peering
               │
               ▼
        ┌─────────────────────┐
        │   Route Table       │
        │   10.0.0.0/16       │
        │      │              │
        │      ▼              │
        │   EC2-B             │
        │   20.0.1.10         │
        │                     │
        │        VPC-B         │
        │     20.0.0.0/16     │
        └─────────────────────┘
```

There is **no Internet Gateway required for the peering traffic**.

---

# 6. VPC Peering Is NOT Transitive

🔥 **EXTREMELY IMPORTANT FOR SAA-C03**

Suppose:

```
VPC-A
   |
   | Peering
   |
VPC-B
   |
   | Peering
   |
VPC-C
```

You might think:

```
A → B → C
```

means A can automatically communicate with C.

### ❌ No!

VPC Peering is **not transitive**.

If A needs to communicate with C:

```
VPC-A ───────── VPC-C
       Peering
```

You need a **separate peering connection**.

So:

```
A ↔ B
B ↔ C
```

does NOT automatically mean:

```
A ↔ C
```

---

## Why?

Think of each peering connection as a **direct private bridge**.

```
A ===== B
```

The bridge connects A and B.

It doesn't make B a router for A's traffic to C.

---

# 7. VPC Peering Mesh Problem

Imagine you have:

```
VPC-A
VPC-B
VPC-C
VPC-D
VPC-E
```

and every VPC needs to communicate with every other VPC.

You need many individual peering connections:

```
       A
     / | \
    /  |  \
   B---|---C
    \  |  /
     \ | /
       D
       |
       E
```

As the number of VPCs grows, managing all those peering connections becomes complicated.

### Exam clue

If the question says:

> "Hundreds/thousands of VPCs need to communicate"
> 

or

> "Centralized connectivity between many VPCs"
> 

start thinking:

**Transit Gateway**, not individual VPC Peering.

---

# 8. VPC Peering vs Transit Gateway

| Feature | VPC Peering | Transit Gateway |
| --- | --- | --- |
| Connect VPCs | ✅ | ✅ |
| Private communication | ✅ | ✅ |
| Transitive | ❌ No | ✅ Yes |
| Best for | Few VPCs | Many VPCs |
| Hub-and-spoke | ❌ | ✅ |
| Centralized routing | ❌ | ✅ |
| Complexity with many VPCs | High | Lower |
| Cross-region | ✅ | ✅ |
| Cross-account | ✅ | ✅ |
| Uses AWS private network | ✅ | ✅ |

### Memory trick

> **Peering = Pair-to-Pair**
> 

> **Transit Gateway = Central Hub**
> 

---

# 9. Cross-Account VPC Peering

VPC Peering doesn't have to be between VPCs belonging to the same AWS account.

You can have:

```
Account A
   |
   | VPC Peering
   |
Account B
```

Example:

```
Company Account
     │
     │ Peering
     │
Security Account
```

Useful when different AWS accounts need private communication.

---

# 10. Cross-Region VPC Peering

VPC Peering can also connect VPCs in **different AWS Regions**.

Example:

```
AWS Region: us-east-1
VPC-A
10.0.0.0/16
      │
      │ VPC Peering
      │
      ▼
AWS Region: eu-west-1
VPC-B
20.0.0.0/16
```

The communication remains on the AWS network rather than requiring the public Internet.

### Exam clue

> **Two VPCs in different Regions need private connectivity**
> 

→ **Inter-Region VPC Peering** can be an answer.

---

# 11. Security Groups and VPC Peering

An important feature:

You can reference a **Security Group from a peered VPC** in a Security Group rule.

For example:

```
VPC-A                         VPC-B

EC2-A                         EC2-B
SG-A                          SG-B

        VPC Peering
             │
             ▼

SG-B can allow traffic
from SG-A
```

Instead of allowing an IP/CIDR, you can use the Security Group as the source **when supported by the peering context**.

### Exam clue

If you see:

> "Allow traffic from instances associated with a specific Security Group in a peered VPC"
> 

think:

**Security Group reference over VPC Peering**

---

# 12. Security Groups Still Matter

VPC Peering doesn't bypass Security Groups.

Suppose:

```
EC2-A
10.0.1.10
   │
   │ VPC Peering
   ▼
EC2-B
20.0.1.10
```

For communication to work, you need:

### VPC-A route table

```
20.0.0.0/16 → Peering Connection
```

### VPC-B route table

```
10.0.0.0/16 → Peering Connection
```

### Security Group

The destination EC2's SG must permit the required traffic.

### NACL

If you have restrictive NACLs, they must also allow the traffic.

So don't think:

> "Peering automatically allows everything."
> 

It doesn't.

---

# 13. Does VPC Peering Need an Internet Gateway?

### ❌ No

For private communication:

```
EC2-A
   ↓
Route Table
   ↓
VPC Peering
   ↓
Route Table
   ↓
EC2-B
```

No:

```
Internet Gateway
NAT Gateway
Public IP
Internet
```

is required for the peering path.

---

# 14. VPC Peering vs Internet Gateway

|  | VPC Peering | Internet Gateway |
| --- | --- | --- |
| Purpose | VPC ↔ VPC | VPC ↔ Internet |
| Private AWS networking | ✅ | ❌ Internet access |
| Requires public IP? | Not for peering traffic | Usually required for IPv4 Internet access |
| Requires route | ✅ | ✅ |
| CIDR overlap | ❌ Not allowed | Not relevant |
| Transitive | ❌ | N/A |
| Cross-account | ✅ | N/A |
| Cross-region | ✅ | N/A |

---

# 15. VPC Peering vs NAT Gateway

This is another common confusion.

### NAT Gateway

Purpose:

```
Private EC2 → Internet
```

Example:

```
Private EC2
     ↓
NAT Gateway
     ↓
Internet Gateway
     ↓
Internet
```

Used when private instances need:

- OS updates
- Download packages
- Access external APIs
- Internet connectivity

---

### VPC Peering

Purpose:

```
VPC-A → VPC-B
```

Example:

```
EC2-A
  ↓
VPC Peering
  ↓
EC2-B
```

Used for **private communication between VPCs**.

### Easy memory

> **NAT = Private subnet → Internet**
> 

> **Peering = VPC → VPC**
> 

---

# 16. What Does "Behave as if They Were in the Same Network" Actually Mean?

Be careful with this wording.

It **doesn't mean** AWS literally merges the two VPCs.

They remain separate:

```
VPC-A                         VPC-B
10.0.0.0/16                   20.0.0.0/16
```

But they can communicate privately through the peering connection.

So think:

> **Separate networks + private connection**
> 

not:

> **One giant VPC**
> 

---

# 17. Complete Example

Imagine an organization has:

### VPC-A — Application

```
CIDR: 10.0.0.0/16

EC2:
10.0.1.10
```

### VPC-B — Database

```
CIDR: 20.0.0.0/16

Database:
20.0.1.10
```

Requirement:

> EC2 in VPC-A must privately access the database in VPC-B.
> 

### Solution

Create:

```
VPC-A
10.0.0.0/16
       │
       │ VPC Peering
       │
VPC-B
20.0.0.0/16
```

Then configure:

**VPC-A route table:**

```
20.0.0.0/16 → VPC Peering
```

**VPC-B route table:**

```
10.0.0.0/16 → VPC Peering
```

**Database Security Group:**

```
Allow required DB port
Source = VPC-A CIDR or appropriate SG
```

Now:

```
EC2
10.0.1.10
   │
   │ private AWS network
   ▼
VPC Peering
   │
   ▼
Database
20.0.1.10
```

---

# 18. What If There Are 3 VPCs?

Suppose:

```
VPC-A       VPC-B       VPC-C
10.0/16     20.0/16     30.0/16
```

You create:

```
A ↔ B
B ↔ C
```

Can A access C?

### ❌ No.

You need:

```
A ↔ C
```

Then:

```
       VPC-A
      /     \
     /       \
 VPC-B ───── VPC-C
```

This is the **non-transitive** nature of VPC Peering.

---

# 19. VPC Peering Exam Decision Tree

When you see a question, think in this order:

### Step 1 — Is it VPC → VPC?

If yes:

→ Consider **VPC Peering** or **Transit Gateway**

### Step 2 — How many VPCs?

**Few VPCs / simple direct connection**

→ **VPC Peering**

**Many VPCs / centralized connectivity**

→ **Transit Gateway**

### Step 3 — Do CIDRs overlap?

If:

```
10.0.0.0/16
10.0.0.0/16
```

→ ❌ VPC Peering won't work.

### Step 4 — Does the question require transitive routing?

```
A → B → C
```

and A must reach C:

→ ❌ VPC Peering

→ Think **Transit Gateway**

### Step 5 — Did they mention route tables?

Remember:

> **Peering connection alone isn't enough.**
> 

You need routes in the relevant route tables.

---

# 20. SAA-C03 Exam Traps 🔥

| Exam statement | Correct thinking |
| --- | --- |
| Two VPCs need private communication | **VPC Peering** |
| VPCs have overlapping CIDRs | ❌ Peering won't work |
| VPC peering is transitive | ❌ False |
| A ↔ B and B ↔ C means A ↔ C | ❌ False |
| Need to update route tables | ✅ Yes |
| Need Internet Gateway for peering | ❌ No |
| Need NAT Gateway for peering | ❌ No |
| Cross-account peering | ✅ Supported |
| Cross-region peering | ✅ Supported |
| Many VPCs need centralized connectivity | **Transit Gateway** |
| SG reference from peered VPC | ✅ Supported in supported peering scenarios |
| Peering automatically allows traffic | ❌ No; SG/NACL/routing still matter |
| VPC CIDRs overlap | ❌ Peering cannot be established |

---

# 🧠 Final Memory Picture

Remember just this:

```
          VPC PEERING

     VPC-A                  VPC-B
  10.0.0.0/16            20.0.0.0/16
      │                       │
      │                       │
   Route                    Route
   Table                    Table
      │                       │
      └─────── PCX ───────────┘
              │
        Private AWS Network
```

And remember the **5 rules**:

> **1. VPC ↔ VPC**
> 

> **2. CIDRs cannot overlap**
> 

> **3. NOT transitive**
> 

> **4. Update route tables**
> 

> **5. Many VPCs → consider Transit Gateway**
> 

### One-line SAA-C03 takeaway

**VPC Peering provides private, direct connectivity between two non-overlapping VPCs; it is non-transitive, requires route-table updates, and is best for simple VPC-to-VPC connectivity.**
