# 🔐 SSH into an EC2 Instance — SAA-C03 Notes

## 1. What is SSH?

**SSH (Secure Shell)** lets you connect to a remote Linux server and control it through a command line.

For EC2:

```
Your Computer
     │
     │ SSH / TCP 22
     ▼
EC2 Instance
```

Think:

> **SSH = remote login to a Linux server securely.**
> 

---

## 2. What do you need to SSH into EC2?

Typically:

1. EC2 instance is running
2. Instance has a **public IPv4 address** or reachable private IP
3. Security Group allows **TCP port 22**
4. You have the correct **private key (`.pem`)**
5. Correct EC2 username

Example:

```
Internet
   │
   │ TCP 22
   ▼
[Security Group]
   │
   ▼
[EC2 Linux]
```

Security Group:

```
Inbound:
SSH
TCP 22
Source: Your IP
```

**Best practice:** restrict port 22 to your own IP rather than `0.0.0.0/0`.

---

# 3. Linux / macOS — OpenSSH

Linux and macOS normally have an SSH client built in.

Typical command:

```bash
ssh -i my-key.pem ec2-user@<EC2-Public-IP>
```

Example:

```bash
ssh -i my-key.pem ec2-user@54.123.45.67
```

Breakdown:

```
ssh
│
├── -i my-key.pem
│      ↓
│   Private key
│
├── ec2-user
│      ↓
│   Login username
│
└── 54.123.45.67
       ↓
    EC2 public IP
```

---

# 4. What is the `.pem` file?

When launching an EC2 instance, you can create/select a **key pair**.

You receive the private key, commonly:

```
my-key.pem
```

The EC2 instance has the corresponding public key.

Conceptually:

```
Your computer
   │
   │ Private key (.pem)
   ▼
EC2
   │
   │ Corresponding public key
   ▼
Authentication
```

The private key should be kept **secure** and should not be shared.

---

# 5. Why Port 22?

SSH uses:

```
TCP 22
```

So your Security Group needs an inbound rule such as:

```
Type: SSH
Protocol: TCP
Port: 22
Source: Your IP
```

### Exam clue

> **"SSH into Linux EC2"**
> 

👉 **Port 22**

---

# 6. Linux/macOS SSH Config

If you frequently connect to EC2 instances, you can configure:

```
~/.ssh/config
```

Then instead of remembering the full command:

```bash
ssh -i my-key.pem ec2-user@54.123.45.67
```

you can define a host and connect simply with:

```bash
ssh myserver
```

Concept:

```
~/.ssh/config
      ↓
Connection settings
      ↓
ssh myserver
      ↓
EC2
```

This is mainly a **convenience/SSH configuration concept**, not an AWS service.

---

# 7. Windows — PuTTY

Windows users can use an SSH client such as **PuTTY**.

The basic idea is the same:

```
Windows Computer
       │
       │ SSH / TCP 22
       ▼
   EC2 Linux
```

You configure:

- EC2 public IP
- Port `22`
- SSH username
- Private key

One important detail: PuTTY commonly uses its own key format, so a `.pem` key may need to be converted to a **`.ppk`** using PuTTYgen.

---

# 8. ⭐ EC2 Instance Connect

AWS also provides **EC2 Instance Connect**.

It lets you connect to an EC2 instance **from the AWS Management Console/browser** without manually providing your downloaded `.pem` key.

Conceptually:

```
AWS Console / Browser
        │
        │ EC2 Instance Connect
        ▼
      EC2
```

### What is the "magic"?

AWS temporarily pushes a **temporary public key** to the EC2 instance for authentication.

```
Browser
   │
   │ Temporary key
   ▼
EC2
   │
   ▼
SSH connection
```

The temporary key is not your permanent downloaded private key.

---

# 9. EC2 Instance Connect Still Needs Networking

A common exam trap:

> **EC2 Instance Connect does NOT bypass Security Group rules.**
> 

If connecting through SSH, **port 22 still needs to be reachable**.

```
EC2 Instance Connect
        │
        ▼
Security Group
        │
   TCP 22 allowed?
      ↙     ↘
    YES      NO
     ↓        ↓
   Connect   ❌
```

---

# 10. Quick Comparison

| Method | Where? | Key handling | Main idea |
| --- | --- | --- | --- |
| **OpenSSH** | Linux/macOS | `.pem` private key | SSH from terminal |
| **PuTTY** | Windows | Usually `.ppk` | SSH from Windows |
| **EC2 Instance Connect** | AWS Console/browser | Temporary key | Browser-based EC2 access |

---

# 🧠 SAA-C03 Memory Trick

> **SSH = TCP 22 + Linux remote login**
> 

> **`.pem` = private key for SSH**
> 

> **EC2 Instance Connect = browser-based SSH using a temporary key**
> 

And remember:

**EC2 Instance Connect does not magically open port 22 — the network/security requirements still matter.**
