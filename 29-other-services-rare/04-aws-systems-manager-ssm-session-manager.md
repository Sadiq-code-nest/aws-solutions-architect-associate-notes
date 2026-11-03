# AWS Systems Manager — Session Manager

**Amazon SSM Session Manager** lets you securely access the command line of **EC2 instances and supported on-premises servers** without opening SSH/RDP access to the internet.

### The core idea

Normally:

```
User
  │
  │ SSH :22 + SSH Key
  ▼
EC2
```

With **Session Manager**:

```
User
  │
  │ IAM permissions
  ▼
SSM Session Manager
  │
  │ SSM Agent
  ▼
EC2 Instance
```

**No inbound port 22 is required.**

---

## 1. What problem does it solve?

Suppose you have an EC2 instance in a **private subnet**.

You want to troubleshoot it, but:

- You don't want to expose SSH port 22.
- You don't want to maintain SSH keys.
- You don't want to create a bastion host.
- You want to control access using IAM.
- You want to record session activity.

**Session Manager solves this.**

---

## 2. How it works

```
                    AWS Cloud
                       │
                SSM Session Manager
                       │
                IAM Authorization
                       │
                       ▼
              ┌─────────────────┐
              │   EC2 Instance  │
              │                 │
              │   SSM Agent     │
              └─────────────────┘
```

The EC2 instance needs the **SSM Agent** and appropriate IAM permissions through an **IAM instance role**.

The user also needs IAM permissions to start sessions.

---

## 3. Why is it more secure?

Traditional SSH:

```
Internet
   │
   │ TCP 22
   ▼
EC2
```

This often requires:

- Public IP
- Port 22
- SSH key
- Security Group rule
- Potentially a bastion host

Session Manager:

```
User
 │
 │ IAM
 ▼
SSM
 │
 ▼
EC2
```

So you can keep the instance **without inbound SSH access**.

### Exam phrase

> **No port 22 → No SSH key → No bastion host**
> 

Think **Session Manager**.

---

## 4. Session Manager vs Bastion Host

| Feature | Bastion Host | SSM Session Manager |
| --- | --- | --- |
| SSH required | ✅ | ❌ |
| Port 22 required | Usually ✅ | ❌ |
| SSH keys | Usually ✅ | ❌ |
| Bastion EC2 required | ✅ | ❌ |
| IAM-based access | Not primary | ✅ |
| Private EC2 access | ✅ | ✅ |
| Session logging | Must configure separately | ✅ S3 / CloudWatch Logs |
| Security | Good if configured properly | Generally stronger/no inbound SSH |
| AWS exam preference | Sometimes | **Preferred when requirements fit** |

---

## 5. Session logging

Session Manager can send session logs to:

```
SSM Session Manager
       │
       ├──────────► Amazon S3
       │
       └──────────► CloudWatch Logs
```

This is useful for **auditing and compliance**.

Example:

> A company wants to know exactly what commands administrators execute on production EC2 instances.
> 

**Session Manager + session logging** is a strong solution.

---

## 6. Real-world example

You have:

```
VPC
│
├── Public Subnet
│
└── Private Subnet
       │
       └── EC2
           Private IP only
           No public IP
           Port 22 CLOSED
```

An administrator needs to troubleshoot the EC2.

Instead of:

```
❌ Create Bastion
❌ Give Bastion public IP
❌ Open SSH :22
❌ Manage SSH keys
```

Use:

```
Administrator
      │
      ▼
SSM Session Manager
      │
      ▼
Private EC2
```

The administrator gets a shell session without directly exposing SSH.

---

## 7. Important requirements

For the exam, remember the basic prerequisites:

**EC2 side:**

- SSM Agent installed/running
- Appropriate IAM instance role/permissions
- Network connectivity to required Systems Manager endpoints

**User side:**

- IAM permissions to start a Session Manager session

---

## 8. SSM Session Manager vs SSM Run Command

Don't confuse these:

| Feature | Session Manager | Run Command |
| --- | --- | --- |
| Interactive shell | ✅ | ❌ |
| Execute commands remotely | ✅ | ✅ |
| Continuous interactive session | ✅ | ❌ |
| Good for troubleshooting | ✅ | Sometimes |
| Execute same command across many instances | Not its primary purpose | ✅ |

### Memory trick

> **Session Manager = "I want to log into the machine."**
> 
> 
> **Run Command = "I want AWS to execute this command for me."**
> 

---

# 🧠 SAA-C03 Exam Clues

If the question says:

- **Access EC2 without SSH**
- **No port 22**
- **No SSH keys**
- **No bastion host**
- **Private EC2 instance**
- **IAM-based access**
- **Secure shell**
- **Audit/log administrator sessions**
- **Send session logs to S3/CloudWatch**

👉 **Think: AWS Systems Manager Session Manager**

### One-line exam takeaway

> **SSM Session Manager provides secure, IAM-controlled shell access to EC2/on-premises servers without SSH, port 22, SSH keys, or bastion hosts, with optional session logging to S3 or CloudWatch Logs.**
> 

**Memory:**

### `SSM Session Manager = Shell without SSH`
