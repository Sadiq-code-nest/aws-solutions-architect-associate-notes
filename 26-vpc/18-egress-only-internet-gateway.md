# Egress-only Internet Gateway (EIGW) — AWS SAA-C03 Complete Notes

This is a **favorite AWS SAA-C03 exam topic** because many students confuse it with a NAT Gateway.

The easiest way to remember it is:

> **NAT Gateway protects private IPv4 instances.**
> 
> 
> **Egress-only Internet Gateway protects private IPv6 instances.**
> 

---

# 1. First understand the problem

Let's say you have a private EC2.

```
Private EC2
```

The instance needs to:

- Download software updates
- Connect to AWS APIs
- Connect to external websites

But...

You DON'T want people on the Internet to connect back to it.

For IPv4 we solve this with:

```
Private EC2
      │
      ▼
NAT Gateway
      │
      ▼
Internet
```

Now imagine the same thing with IPv6.

You **cannot** use NAT Gateway.

Why?

Because IPv6 already has globally unique addresses.

AWS therefore created:

> **Egress-only Internet Gateway**
> 

---

# 2. What is an Egress-only Internet Gateway?

### Definition

An **Egress-only Internet Gateway (EIGW)** is a VPC component that allows **outbound IPv6 Internet access only**, while preventing **unsolicited inbound IPv6 connections** from the Internet.

Think:

> **Exit only. No entry.**
> 

---

# 3. Meaning of "Egress"

Networking terms:

```
Ingress = Incoming traffic

Egress = Outgoing traffic
```

Example

```
EC2 ---> Internet

This is Egress
```

Internet trying to connect:

```
Internet ---> EC2

This is Ingress
```

EIGW allows:

```
EC2 ---> Internet
```

But blocks:

```
Internet ---> EC2
```

---

# 4. Simple architecture

```
                   Internet
                      ▲
                      │
               Response allowed
                      │
                +----------------+
                | Egress-only IGW|
                +----------------+
                      ▲
                      │
             Outbound IPv6 only
                      ▲
                      │
             Private IPv6 EC2
```

Notice:

The connection always starts from EC2.

---

# 5. Real-world example

Imagine a company has:

```
Private EC2

Runs:
✔ Backend API
✔ Database Client
✔ Update Service
```

The server needs to:

- Download Linux updates
- Call external payment API
- Download packages

But should NEVER be reachable directly from the Internet.

Solution:

```
Private EC2
      │
      ▼
Egress-only Internet Gateway
      │
      ▼
Internet
```

Exactly what AWS designed EIGW for.

---

# 6. Connection flow

### Step 1

Private EC2 starts connection

```
Private EC2

↓

www.google.com
```

Allowed.

---

### Step 2

Google replies

```
Google

↓

Private EC2
```

Allowed.

Because it's a response.

---

### Step 3

Random hacker

```
Internet

↓

Private EC2
```

Blocked.

The Internet cannot initiate connections.

---

# 7. Visualize the traffic

Allowed

```
Private EC2
      │
      ▼
Internet

Internet
      │
      ▼
Private EC2

(Response only)
```

Blocked

```
Internet
      │
      ▼
Private EC2

(New connection)

❌ Blocked
```

---

# 8. NAT Gateway vs Egress-only Internet Gateway

This is one of the most common exam comparisons.

| Feature | NAT Gateway | Egress-only Internet Gateway |
| --- | --- | --- |
| Supports | IPv4 | IPv6 |
| Address Translation | Yes (NAT) | No |
| Outbound Internet | ✅ | ✅ |
| Internet can initiate connection | ❌ | ❌ |
| Used in Private Subnet | ✅ | ✅ |
| Requires Route Table | Yes | Yes |
| Requires Internet Gateway | Yes | Yes |
| AWS Managed | Yes | Yes |

---

# 9. Why doesn't IPv6 need NAT?

With IPv4:

Private IP

```
10.0.1.5
```

Cannot exist on the Internet.

Need NAT.

```
10.0.1.5

↓

54.20.30.40

↓

Internet
```

---

With IPv6:

```
2001:db8:abcd::123
```

Already globally unique.

No translation needed.

So NAT is unnecessary.

AWS simply controls who can start the connection.

---

# 10. Public subnet routing

Suppose:

```
Public EC2
```

Needs to receive traffic from Internet.

Route table

```
Destination

0.0.0.0/0

Target

IGW
```

For IPv6

```
Destination

::/0

Target

IGW
```

Diagram

```
Internet

↓

Internet Gateway

↓

Public EC2
```

Public EC2 can communicate both ways.

---

# 11. Private subnet routing

Private subnet

Needs outbound only.

IPv4

```
0.0.0.0/0

↓

NAT Gateway
```

IPv6

```
::/0

↓

Egress-only Internet Gateway
```

Diagram

```
Private EC2

↓

Egress-only IGW

↓

Internet
```

---

# 12. Understanding the Route Table

Private subnet

```
Destination         Target

10.0.0.0/16         local

2001:db8::/56       local

0.0.0.0/0           NAT Gateway

::/0                Egress-only IGW
```

Meaning

Local IPv4

```
10.0.0.0/16

↓

Stay inside VPC
```

Local IPv6

```
2001:db8::/56

↓

Stay inside VPC
```

Internet IPv4

```
0.0.0.0/0

↓

NAT Gateway
```

Internet IPv6

```
::/0

↓

Egress-only Internet Gateway
```

---

# 13. Public subnet route table

```
Destination

10.0.0.0/16

↓

local

2001:db8::/56

↓

local

0.0.0.0/0

↓

IGW

::/0

↓

IGW
```

Notice

Public subnet uses:

```
Internet Gateway

for BOTH

IPv4

AND

IPv6
```

---

# 14. Complete architecture

```
                    Internet
                  /          \
               IPv4          IPv6
                 │             │
                 ▼             ▼
          +-------------------------+
          |   Internet Gateway      |
          +-------------------------+
                 │
        -----------------------
        │                     │
        ▼                     ▼
 Public Subnet          Private Subnet

 Public EC2             Private EC2
 IPv4 + IPv6            IPv4 + IPv6

        │                     │
        │                     ▼
        │             Egress-only IGW
        │                     │
        └──────────────► Internet
```

---

# 15. Common SAA-C03 scenarios

### Scenario 1

Company has:

- Private IPv6 instances
- Need outbound Internet
- No inbound Internet

Answer

✅ **Egress-only Internet Gateway**

---

### Scenario 2

Private subnet

Need outbound Internet for IPv4.

Answer

✅ NAT Gateway

---

### Scenario 3

Public web server

Users access from Internet.

Answer

✅ Internet Gateway

---

### Scenario 4

Need IPv6 outbound only.

Answer

✅ Egress-only Internet Gateway

---

# 16. Common exam traps

### Trap 1

Question

> Use NAT Gateway for IPv6.
> 

Wrong.

NAT Gateway is for IPv4.

---

### Trap 2

Question

> Private IPv6 instance needs Internet updates.
> 

Answer

Egress-only Internet Gateway.

---

### Trap 3

Question

> IPv6 route to Internet
> 

Correct route

```
::/0

↓

Internet Gateway

(public subnet)

or

Egress-only IGW

(private subnet)
```

---

### Trap 4

Question

> IPv6 addresses need translation.
> 

Wrong.

IPv6 does not require NAT.

---

### Trap 5

Question

> Egress-only Internet Gateway allows Internet users to SSH into the instance.
> 

Wrong.

It blocks new inbound connections initiated from the Internet.

---

# 17. Decision tree (Very Important)

```
Need Internet?

        │
        ▼

Is it IPv4?

     Yes
      │
      ▼

Public subnet?

Yes → Internet Gateway

No → NAT Gateway

-----------------------

Is it IPv6?

      Yes
       │

Public subnet?

Yes → Internet Gateway

No → Egress-only Internet Gateway
```

---

# 🧠 Ultimate Memory Tricks

### Trick 1

```
IPv4

Private

↓

NAT Gateway
```

---

### Trick 2

```
IPv6

Private

↓

Egress-only Internet Gateway
```

---

### Trick 3

```
Public Subnet

↓

Internet Gateway

(for IPv4 AND IPv6)
```

---

### Trick 4

Remember the word:

**Egress = Exit**

So:

```
Exit

Allowed

Entry

Blocked
```

---

# 🚀 SAA-C03 One-Page Cheat Sheet

| Requirement | AWS Service |
| --- | --- |
| Public IPv4 Internet | Internet Gateway |
| Public IPv6 Internet | Internet Gateway |
| Private IPv4 → Internet | NAT Gateway |
| Private IPv6 → Internet | Egress-only Internet Gateway |
| IPv4 address translation | NAT Gateway |
| IPv6 address translation | Not required |
| IPv6 outbound-only access | Egress-only Internet Gateway |
| Prevent Internet from initiating IPv6 connections | Egress-only Internet Gateway |
| Route for all IPv4 Internet traffic | `0.0.0.0/0` |
| Route for all IPv6 Internet traffic | `::/0` |

## ⭐ Final Exam Takeaway

If the exam says:

- **Private IPv4 instance needs Internet access** → **NAT Gateway**
- **Private IPv6 instance needs outbound-only Internet access** → **Egress-only Internet Gateway**
- **Public IPv4 or IPv6 instance** → **Internet Gateway**

This distinction is tested frequently in SAA-C03 scenario-based questions.
