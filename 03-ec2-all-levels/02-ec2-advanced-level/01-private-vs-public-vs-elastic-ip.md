# Private IP vs Public IP — AWS SAA-C03

## 1. 🌐 What is an IP address?

An **IP address** identifies a device/interface on a network.

For IPv4:

```
192.168.1.10
```

IPv4 format:

```
A.B.C.D
```

Each part is `0–255`.

For SAA-C03, focus mainly on understanding **private vs public IPv4**.

---

# 2. 🌍 Public IP

A **Public IP** is an IP address that can be used to communicate over the **Internet**.

```
Your Computer
     │
     │ Internet
     ▼
Public IP
     │
     ▼
Internet
```

### Example

```
EC2 Public IP
54.123.45.67
```

This address can be reached from the Internet **if the networking and security rules allow it**.

### Key points

- Used for Internet communication
- Must be globally unique
- Can change when an EC2 instance is stopped and started
- Can be associated with an Elastic IP

---

# 3. 🔒 Private IP

A **Private IP** is used for communication **inside a private network**, such as a VPC.

Example:

```
EC2 A
10.0.1.10
   │
   │ Private network
   ▼
EC2 B
10.0.1.20
```

The important idea:

> **Private IPs are not directly reachable from the public Internet.**
> 

Different private networks can reuse the same private IP ranges.

Example:

```
Company A VPC        Company B VPC
10.0.1.10             10.0.1.10
     │                     │
     └── Different networks ──┘
```

That's completely fine because they are separate private networks.

---

# 4. 🏠 Common Private IPv4 Ranges

These are the main private IPv4 ranges you should recognize:

```
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

For example:

```
10.0.1.25
172.16.5.20
192.168.1.10
```

are private IP addresses.

### 🧠 Exam clue

If you see:

> `10.x.x.x`
> 
> 
> `172.16.x.x – 172.31.x.x`
> 
> `192.168.x.x`
> 

👉 Think **Private IP**

---

# 5. 🔄 How a Private EC2 Reaches the Internet

A private EC2 cannot directly communicate with the Internet using its private IP.

Typically:

```
Private EC2
10.0.1.10
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

The **NAT Gateway** allows instances in a private subnet to initiate connections to the Internet without making the instances directly publicly reachable.

---

# 6. 🟢 EC2 Usually Has Both

An EC2 instance can have:

```
EC2
│
├── Private IP
│     ↓
│   Internal AWS/VPC communication
│
└── Public IP
      ↓
    Internet communication
```

Example:

```
EC2
Private IP: 10.0.1.15
Public IP: 54.123.45.67
```

### When EC2 talks to another EC2 in the same VPC

Use the **private IP**:

```
EC2-A
10.0.1.10
   │
   │ private communication
   ▼
EC2-B
10.0.1.20
```

This avoids unnecessarily going through the public Internet.

---

# 7. 💻 SSH Example

Suppose your EC2 has:

```
Private IP: 10.0.1.10
Public IP: 54.123.45.67
```

You're sitting at home on your computer.

You generally **cannot SSH directly to `10.0.1.10`**, because your home network isn't directly connected to that VPC.

You can SSH through the EC2's reachable public address:

```bash
ssh -i my-key.pem ec2-user@54.123.45.67
```

Flow:

```
Your Computer
     │
     │ Internet
     ▼
Public IP
54.123.45.67
     │
     ▼
EC2
Private IP
10.0.1.10
```

The Security Group must also allow **TCP 22** from your source IP.

---

# 8. ⭐ Elastic IP

A normal EC2 public IPv4 address can change when the instance is stopped and started.

If you need a **static public IPv4 address**, you can use an **Elastic IP**.

```
Elastic IP
54.x.x.x
    │
    ▼
EC2-A
```

If EC2-A fails, you can remap the Elastic IP to another EC2:

```
Before:

Elastic IP → EC2-A ❌

After:

Elastic IP → EC2-B ✅
```

This can help quickly redirect traffic to another instance.

---

# 9. ⚠️ Important: Don't Overuse Elastic IP

For SAA-C03, remember that AWS generally encourages using DNS and load balancers instead of manually managing public IPs when possible.

For example:

```
Better architecture:

Users
  ↓
DNS
  ↓
Load Balancer
  ↓
EC2 instances
```

Rather than:

```
Users
  ↓
Elastic IP
  ↓
One EC2
```

---

# 10. Public vs Private — Quick Comparison

|  | **Public IP** | **Private IP** |
| --- | --- | --- |
| Internet reachable? | ✅ Potentially | ❌ Not directly |
| Used inside VPC? | Can be, but private IP is normally used for internal communication | ✅ Yes |
| Globally unique? | ✅ | ❌ Not globally |
| Example | `54.123.45.67` | `10.0.1.10` |
| Changes after EC2 stop/start? | Normal public IP can change | Private IP normally remains associated |
| Static option | **Elastic IP** | Private IP assignment |

---

## 🧠 The easiest way to remember

> **Public IP = "My address on the Internet"**
> 

> **Private IP = "My address inside my private network"**
> 

And for AWS:

```
Internet
   │
   ▼
Public IP
   │
   ▼
EC2
   │
   ▼
Private IP
   │
   ▼
Other AWS resources
```

### 🎯 SAA-C03 Exam Takeaway

> **Public IP is for Internet-facing communication; private IP is for internal VPC communication, and an Elastic IP provides a persistent public IPv4 address.**
>
