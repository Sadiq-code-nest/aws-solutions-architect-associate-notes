# Bastion Host

## 1. Meaning

A **Bastion Host** is a specially secured EC2 instance in a **public subnet** that lets administrators securely connect to EC2 instances inside **private subnets**.

Think:

> **Bastion Host = "Jump machine to reach private servers." 🦘**
> 

---

## 2. Definition

Normally, a private EC2 instance has **no direct internet access for incoming SSH connections**.

So you create a Bastion Host:

```
Internet
   │
   │ SSH :22
   ▼
┌─────────────────────┐
│ Public Subnet       │
│                     │
│ Bastion Host        │
└─────────┬───────────┘
          │
          │ SSH :22
          ▼
┌─────────────────────┐
│ Private Subnet      │
│                     │
│ Private EC2         │
└─────────────────────┘
```

The Bastion Host itself has a **public IP** and is placed in a **public subnet**.

The private EC2 remains private.

---

# 3. Real Example ⭐⭐⭐⭐⭐

Imagine your company has a private EC2 server running a database/application:

```
Private EC2
Private IP: 10.0.2.50
```

For security, you don't want this server directly reachable from the internet.

But you're the administrator and need to SSH into it.

You create:

```
Internet
   ↓
Bastion Host
Public IP: 54.x.x.x
   ↓
Private EC2
10.0.2.50
```

You first connect to the Bastion Host:

```
Your Computer
     ↓ SSH :22
Bastion Host
```

Then from the Bastion Host:

```
Bastion Host
     ↓ SSH :22
Private EC2
```

The private EC2 **doesn't need a public IP**.

---

# 4. Security Groups ⭐⭐⭐⭐⭐

This is the **important SAA-C03 part**.

### Bastion Host Security Group

Allow SSH **only from trusted IP addresses**.

For example:

```
Bastion SG

Inbound:
SSH :22
Source:
Your company's public CIDR
203.0.113.0/24
```

❌ Don't normally allow:

```
0.0.0.0/0
```

for SSH because that means anyone on the internet can attempt to connect.

---

### Private EC2 Security Group

The private EC2 should allow SSH **from the Bastion Host**, not from the entire internet.

A clean setup is:

```
Bastion-SG
    ↓
Private-EC2-SG
```

Private EC2 Security Group:

```
Inbound:
SSH :22
Source:
Bastion-SG
```

This is better than manually entering the Bastion's IP.

---

# 5. Why Use Security Group Reference? ⭐⭐⭐⭐⭐

Imagine:

```
Bastion Host
10.0.1.10
```

You could say:

```
Private EC2 SG
Allow SSH from 10.0.1.10
```

But if the Bastion's private IP changes, you have to update the rule.

Instead:

```
Private EC2 SG
Allow SSH
Source = Bastion-SG
```

Now AWS understands:

> "Allow SSH from resources associated with Bastion-SG."
> 

### 🧠 Exam trick:

> **Private EC2 SG → Source = Bastion Host Security Group**
> 

---

# 6. What Makes the Bastion Host "Public"?

A Bastion Host needs to be reachable from your trusted administrator network.

Therefore:

```
Bastion Host
    ↓
Public Subnet
    ↓
Internet Gateway
    ↓
Internet
```

It typically has a **public IPv4 address or Elastic IP**.

The private EC2 doesn't need one:

```
Public Subnet
     │
     └── Bastion Host
          Public IP ✅

Private Subnet
     │
     └── Private EC2
          Public IP ❌
```

---

# 7. Bastion Host vs NAT Gateway ⭐⭐⭐⭐⭐

Very common confusion.

### Bastion Host

Used for:

> **Incoming administrative access**
> 

```
Admin
 ↓
Bastion
 ↓
Private EC2
```

### NAT Gateway

Used for:

> **Outgoing internet access from private resources**
> 

```
Private EC2
 ↓
NAT Gateway
 ↓
Internet
```

So:

> **Bastion = Admin → Private EC2**
> 

> **NAT Gateway = Private EC2 → Internet**
> 

🔥 This distinction is highly useful for SAA-C03.

---

# 8. Bastion Host vs SSM Session Manager

Modern AWS architectures often use **AWS Systems Manager Session Manager** instead of exposing SSH through a Bastion Host.

With Session Manager:

```
Administrator
      ↓
Systems Manager
      ↓
Private EC2
```

You can manage instances without requiring inbound SSH access from the internet.

But if an SAA question specifically says:

> "Administrators need SSH access to private EC2 instances through a public entry point."
> 

Think:

✅ **Bastion Host**

---

# 🧠 Final SAA-C03 Memory Trick

Remember this picture:

```
                 INTERNET
                    │
             SSH :22 from
            trusted CIDR only
                    │
                    ▼
          ┌──────────────────┐
          │  PUBLIC SUBNET   │
          │                  │
          │  Bastion Host    │
          │   Bastion-SG     │
          └────────┬─────────┘
                   │
                 SSH :22
                   │
                   ▼
          ┌──────────────────┐
          │ PRIVATE SUBNET   │
          │                  │
          │   Private EC2    │
          │    Private-SG    │
          │                  │
          │ SSH source =     │
          │   Bastion-SG     │
          └──────────────────┘
```

### ⭐ Exam keywords

**"SSH into private EC2"** → Bastion Host

**"Jump server"** → Bastion Host

**"Public subnet → private subnet"** → Bastion Host

**"Allow SSH only from corporate CIDR"** → Bastion SG

**"Private EC2 allows SSH from Bastion SG"** → Correct SG design

**"Private EC2 needs outbound internet"** → NAT Gateway, **not Bastion**
