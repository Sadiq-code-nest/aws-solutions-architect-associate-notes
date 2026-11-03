# Understand Concept

## 1. First: What problem are we solving?

Imagine your company has its own physical network:

```
🏢 Company Network
10.0.0.0/16
       |
       |
   Internet
       |
       |
☁️ AWS VPC
172.16.0.0/16
```

Your AWS VPC and your company's network are **two separate networks**.

By default:

```
Company Network  ❌──────────❌  AWS VPC
```

An EC2 instance inside AWS cannot simply say:

> "Hey, let me access the company's database."
> 

Why?

Because AWS doesn't automatically know:

- Where your company network is
- How to reach it
- Whether your company allows the traffic
- How to securely communicate with it

So we need a **VPN connection**.

---

# 2. What does Site-to-Site VPN actually mean?

Think of it as creating a **secure tunnel between two networks**:

```
🏢 Your Network                         ☁️ AWS

Company LAN                             VPC
10.0.0.0/16                            172.16.0.0/16

     |                                      |
     |                                      |
Customer Gateway                        Virtual
     |                                   Private Gateway
     |                                      |
     +========== 🔐 IPsec VPN =============+
                    Tunnel
```

Now:

```
EC2
172.16.1.10
   |
   | "I want 10.0.0.50"
   ↓
VPN Tunnel 🔐
   ↓
Company Network
10.0.0.50
```

That's the basic idea.

---

# 3. Now understand each component

This is where AWS exam questions become easier.

## A. Customer Gateway (CGW)

**Customer Gateway = your side.**

It represents the VPN device/software on **your company's network**.

For example:

```
🏢 Company

        Customer Gateway
              ↓
       [ VPN Router ]
              |
        Company LAN
```

The actual device could be:

- Cisco router
- Fortinet firewall
- Palo Alto firewall
- StrongSwan software
- etc.

AWS needs information about this device.

So in AWS you create a **Customer Gateway resource**.

### Important distinction

There are two meanings that can confuse you:

**Customer Gateway device**

→ Actual physical/software VPN device at your company.

**Customer Gateway resource**

→ AWS configuration describing that device.

---

# 4. Why does AWS need a static public IP?

This is extremely important for SAA.

Your company's VPN device needs to be reachable from the Internet.

For example:

```
Company VPN Router

Private IP:
10.0.0.1

Public IP:
203.0.113.10
       ↑
   Internet-routable
```

AWS needs to know:

> "Where is your VPN device?"
> 

So you configure:

```
Customer Gateway
      ↓
Static Public IP
203.0.113.10
```

Therefore:

### Exam keyword

> **Customer Gateway requires an Internet-routable static IP address.**
> 

If the question says:

> "What needs to be configured outside the VPC?"
> 

The answer is often:

**A static, Internet-routable IP address for the customer gateway.**

---

# 5. Virtual Private Gateway (VGW)

Now let's move to the AWS side.

AWS needs its own VPN endpoint.

That's the:

**Virtual Private Gateway (VGW)**

Think:

```
🏢 Your side                    ☁️ AWS side

Customer Gateway  ← 🔐 →  Virtual Private Gateway
      CGW                         VGW
```

VGW is attached to your VPC:

```
             VPC
       172.16.0.0/16
              |
              |
        Virtual Private
          Gateway
             VGW
```

So:

**CGW = your side**

**VGW = AWS side**

---

# 6. VPN Connection

Now connect them:

```
🏢 Company                         AWS

Customer Gateway                  VGW
      |                              |
      |                              |
      +========= IPsec VPN =========+
                 🔐
              VPN Tunnel
```

AWS calls this the **VPN connection**.

It is an **IPsec-based encrypted connection** between:

```
Your network ↔ AWS VPC
```

---

# 7. But here's an important point

Creating the VPN tunnel doesn't automatically make traffic flow.

You also need **routing**.

Imagine EC2 wants:

```
172.16.1.10
      ↓
10.0.0.50
```

AWS needs to know:

> "For 10.0.0.0/16, where should I send the packet?"
> 

That's the job of the **route table**.

---

# 8. Route Table

Suppose your VPC route table contains:

```
Destination       Target
--------------------------------
172.16.0.0/16     local
10.0.0.0/16       VGW
```

This means:

```
EC2
 |
 | Destination = 10.0.0.50
 |
 ↓
Route Table
 |
 | 10.0.0.0/16 → VGW
 ↓
VGW
 |
 ↓
🔐 VPN Tunnel
 |
 ↓
CGW
 |
 ↓
Company Network
```

So the route table tells AWS:

> "Traffic going to my company network should go through the VPN gateway."
> 

---

# 9. Security Groups

Then you need to allow the traffic at the EC2 level.

Example:

```
EC2 Security Group

Inbound:
Source: 10.0.0.0/16
Port: 443
Allow
```

So:

```
Company Server
10.0.0.50
      |
      | HTTPS
      ↓
🔐 VPN
      ↓
VGW
      ↓
VPC Route Table
      ↓
EC2
      |
      ↓
Security Group
      |
      ↓
Allow TCP 443
```

---

# 10. NACL can also matter

Remember:

**Security Group = stateful**

**Network ACL = stateless**

If you're using restrictive NACLs, you need to allow both directions appropriately.

But for understanding the basic VPN architecture, focus first on:

```
Customer Gateway
       ↓
VPN Tunnel
       ↓
Virtual Private Gateway
       ↓
Route Table
       ↓
Security Group
       ↓
EC2
```

---

# 11. The complete picture

Here's the architecture you should have in your head:

```
              YOUR COMPANY
          🏢 On-Premises Network
             10.0.0.0/16
                   |
                   |
            ┌──────────────┐
            │   Customer   │
            │   Gateway    │
            │    (CGW)     │
            └──────┬───────┘
                   |
                   | Public Internet
                   |
             🔐 IPsec VPN
                Tunnel
                   |
                   |
            ┌──────┴───────┐
            │   Virtual    │
            │   Private    │
            │   Gateway    │
            │    (VGW)     │
            └──────┬───────┘
                   |
                AWS VPC
             172.16.0.0/16
                   |
              Route Table
                   |
             10.0.0.0/16
                 → VGW
                   |
                  EC2
            172.16.1.10
```

---

# 12. Who is responsible for what?

This is a **very useful SAA exam mental model**:

| Component | Where? | Purpose |
| --- | --- | --- |
| **Customer Gateway device** | Your network | Your VPN router/firewall |
| **Customer Gateway resource** | AWS | Tells AWS about your device |
| **Static public IP** | Your network | Internet-reachable address of CGW |
| **Virtual Private Gateway** | AWS | AWS-side VPN endpoint |
| **VPN Connection** | Between them | Encrypted IPsec tunnel |
| **Route Table** | AWS VPC | Sends on-prem traffic to VGW |
| **Security Group** | EC2 | Allows/blocks traffic |
| **NACL** | Subnet | Stateless network filtering |

---

# 13. Why does the question say "outside the VPC"?

This is probably the most important part of the paragraph you posted.

Suppose the exam asks:

> What needs to be configured **outside of the VPC** for an AWS Virtual Private Network connection to succeed?
> 

They're trying to make you think about the **customer side**.

AWS side:

```
VPC
 ↓
VGW
 ↓
VPN
```

Customer side:

```
Your Network
 ↓
Customer Gateway
 ↓
STATIC PUBLIC IP
```

The critical requirement on the customer side is:

> **An Internet-routable static IP address for the customer gateway.**
> 

So if you see:

❌ EIP to Virtual Private Gateway

❌ Private IP of customer router

❌ Elastic IP on EC2

✅ **Static public/Internet-routable IP of the customer gateway**

→ **Choose the static public IP.**

---

# 14. One more important correction

Don't think:

> "VPN = Internet connection."
> 

That's not quite right.

The Internet is the **transport path**.

The VPN provides the **encrypted tunnel** over that path.

Conceptually:

```
Company
   |
   | encrypted IPsec traffic
   ↓
Internet
   |
   ↓
AWS
```

Think of it like:

**Internet = road**

**VPN = secure armored tunnel running over the road**

---

## 🧠 SAA-C03 shortcut

When you see:

> **On-premises ↔ AWS VPC**
> 

immediately think:

```
Customer Gateway
        ↕
     IPsec VPN
        ↕
Virtual Private Gateway
        ↓
   Route Table
        ↓
      VPC
```

And when you see:

> **"What must be configured on the customer side/outside the VPC?"**
> 

think:

**CGW → static Internet-routable public IP.**

That's the core concept behind the paragraph you posted.
