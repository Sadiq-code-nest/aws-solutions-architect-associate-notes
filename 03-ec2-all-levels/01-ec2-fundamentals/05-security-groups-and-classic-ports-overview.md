# 🔐 AWS Security Groups — SAA-C03 Notes

## 1. What is a Security Group?

A **Security Group (SG)** is a **virtual firewall attached to an EC2 instance's network interface (ENI)**.

Its job is to control:

- **Inbound traffic** → who can connect **to** your EC2
- **Outbound traffic** → where your EC2 can connect **to**

Think:

> **Security Group = Firewall for your EC2 network connection**
> 

```
Internet
   ↓
[ Security Group ]
   ↓
[ EC2 Instance ]
```

---

## 2. What does a Security Group control?

Security Groups control traffic based on:

- **Port**
- **Protocol**
- **Source/Destination IP**
- **Another Security Group**

Example:

```
Inbound Rule:
TCP
Port: 443
Source: 0.0.0.0/0
```

Meaning:

> Allow HTTPS traffic from anywhere to this EC2 instance.
> 

---

# 3. Inbound vs Outbound

### Inbound

Traffic **coming into** EC2.

```
User
  ↓
Internet
  ↓
SG
  ↓
EC2
```

Example:

```
Port 22 → SSH
Port 80 → HTTP
Port 443 → HTTPS
```

### Outbound

Traffic **leaving** EC2.

```
EC2
 ↓
SG
 ↓
Internet / Other AWS resource
```

Example:

```
EC2 → Database
EC2 → External API
EC2 → Internet
```

---

# 4. Default Behavior ⭐

A newly created Security Group:

```
Inbound  → ❌ Deny everything
Outbound → ✅ Allow everything
```

So you must explicitly create **inbound allow rules**.

Example:

```
Inbound
22  → Allow my IP
443 → Allow everyone

Everything else → ❌
```

---

# 5. Security Groups Are Stateful ⭐⭐⭐

This is one of the **most important SAA-C03 concepts**.

Suppose your SG allows:

```
Client
  ↓
TCP 443
  ↓
EC2
```

The response from EC2 is automatically allowed:

```
Client → EC2     ✅
EC2 → Client     ✅
```

You don't need to create a separate inbound rule for the response.

Why?

> **Security Groups are stateful — they remember the connection.**
> 

### Easy memory:

**SG = Stateful**

---

# 6. Security Group Can Reference Another Security Group

This is extremely useful for **EC2 → EC2** communication.

Example:

```
                    SG-Web
                       ↓
Internet → Web Server
                       ↓
                  SG-Database
                       ↓
                  Database EC2
```

Instead of allowing the database to accept traffic from a specific IP, you can say:

```
Database SG:
Allow TCP 3306
Source = SG-Web
```

Meaning:

> Any EC2 instance associated with `SG-Web` can connect to the database on port 3306.
> 

### Why is this better?

If the web servers change IP addresses:

```
Web Server 1 → 10.0.1.10
Web Server 2 → 10.0.1.20
Web Server 3 → 10.0.1.30
```

You don't need to update the database SG with all those IPs.

The SG reference handles it.

---

# 7. Multiple Security Groups

An EC2 instance can have **multiple Security Groups**.

```
             EC2
              │
       ┌──────┴──────┐
       ↓             ↓
    SG-Web         SG-SSH
```

The rules from the attached Security Groups are evaluated together.

If **any applicable rule allows the traffic**, the traffic is allowed.

---

# 8. Security Group Exists Outside the EC2 OS

The Security Group filters traffic **before it reaches the instance**.

```
Internet
   ↓
Security Group
   ↓
EC2 Network Interface
   ↓
EC2 OS
```

If SG blocks the traffic:

```
Internet
   ↓
SG ❌
   ↓
EC2 never receives it
```

---

# 9. Very Important: Timeout vs Connection Refused

This is a useful troubleshooting clue.

### ❌ Timeout

Usually indicates something is **blocking the network traffic**.

```
Client
  ↓
SG ❌
  ↓
⏳ Timeout
```

Check:

- Security Group
- NACL
- Route table
- Network connectivity

### ❌ Connection Refused

The request reached the destination, but the application isn't accepting the connection.

For example:

```
Client
 ↓
EC2
 ↓
Application
 ↓
❌ Nothing listening on port 8080
```

Think:

> **Timeout → network/security problem**
> 

> **Connection refused → application/port listening problem**
> 

---

# 10. Important Ports for SAA-C03

| Port | Protocol/Service | Typical use |
| --- | --- | --- |
| **22** | SSH | Linux administration |
| **21** | FTP | File transfer |
| **22** | SFTP | Secure file transfer |
| **80** | HTTP | Web traffic |
| **443** | HTTPS | Secure web traffic |
| **3389** | RDP | Windows administration |

### Easy memory

```
22   → SSH
80   → HTTP
443  → HTTPS
3389 → Windows RDP
```

---

# 11. Simple Real-World Example

Suppose you have:

```
Internet
   ↓
Web Server
   ↓
Application Server
   ↓
Database
```

You could design Security Groups like this:

```
SG-Web
  ↓
Allow 443 from Internet

SG-App
  ↓
Allow 8080 from SG-Web

SG-DB
  ↓
Allow 3306 from SG-App
```

So:

```
Internet
   ↓ HTTPS 443
[ SG-Web ]
   ↓
Web Server
   ↓ TCP 8080
[ SG-App ]
   ↓
App Server
   ↓ TCP 3306
[ SG-DB ]
   ↓
Database
```

This follows **least privilege**:

> Only the required traffic is allowed between application layers.
> 

---

# 12. Security Group vs NACL

You should know this comparison very well:

| Feature | Security Group | NACL |
| --- | --- | --- |
| Applied to | **ENI / EC2 resource** | **Subnet** |
| Stateful? | ✅ **Yes** | ❌ **No** |
| Rules | Allow | Allow + Deny |
| Return traffic | Automatically allowed | Must explicitly allow |
| IP-based rules | ✅ | ✅ |
| SG reference | ✅ | ❌ |
| Explicit DENY | ❌ | ✅ |
| Main purpose | Resource-level firewall | Subnet-level firewall |

### 🧠 Memory

> **Security Group → Instance/ENI**
> 

> **NACL → Subnet**
> 

> **SG → Stateful**
> 

> **NACL → Stateless**
> 

---

# 🎯 SAA-C03 Exam Takeaways

### If you see:

**"Control access to an EC2 instance"**

👉 Security Group

**"Allow HTTPS from the Internet"**

👉 Security Group rule: `TCP 443`

**"Allow one EC2 tier to communicate with another"**

👉 Reference the **source Security Group**

**"Stateless firewall"**

👉 NACL

**"Explicit deny a specific IP"**

👉 NACL

**"Return traffic automatically allowed"**

👉 Security Group

**"All inbound traffic blocked by default"**

👉 New Security Group

---

## 🔑 One-Line Memory Trick

> **Security Group = Stateful virtual firewall attached to an EC2 network interface that controls inbound/outbound traffic using allow rules.**
>
