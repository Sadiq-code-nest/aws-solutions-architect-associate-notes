# EC2 Instance High Availability

Absolutely. The key idea here is: **EC2 itself is not automatically highly available**. If you have one EC2 instance and it fails, you need a mechanism to detect the failure and bring up a replacement.

Below are the three patterns, from **simple → more resilient → resilient with persistent EBS data**.

---

# 1. Highly Available EC2 — Standby Instance + CloudWatch

### Architecture

```
                    AWS Cloud
                       │
                ┌──────▼──────┐
                │ CloudWatch  │
                │ Alarm/Event  │
                └──────┬──────┘
                       │ Detect failure
                       ▼
             ┌────────────────────┐
             │ Standby EC2        │
             │ Instance           │
             └─────────┬──────────┘
                       │
                  Attach EIP
                       │
                       ▼
                 Elastic IP
                       │
                       ▼
              Replacement Server
```

### How it works

You have:

- **Primary EC2**
- **Standby EC2**
- **Elastic IP**
- **CloudWatch**
- Automation such as Lambda/SSM to perform recovery actions

Example:

```
Primary EC2
10.0.1.10
    │
    │ Application running
    ▼
   💥 Failure

CloudWatch detects problem
        ↓
Automation starts standby EC2
        ↓
Attach Elastic IP to standby
        ↓
Users reconnect
```

### Real-world example

Imagine:

```
Web Server
Public IP = 54.x.x.x
```

Users always connect to:

```
54.x.x.x
```

If the original EC2 dies:

```
Old EC2 ❌
      ↓
Standby EC2 starts
      ↓
Elastic IP moved
      ↓
Users still use 54.x.x.x
```

The important point is that **the Elastic IP stays the same**.

### SAA-C03 takeaway

> **CloudWatch detects failure → automation starts replacement → Elastic IP is moved.**
> 

But this approach requires you to manage the recovery logic yourself.

---

# 2. Highly Available EC2 — Auto Scaling Group

This is generally a **better architecture**.

Instead of manually maintaining a standby instance, let an **Auto Scaling Group (ASG)** replace failed instances automatically.

### Architecture

```
                  Auto Scaling Group
              Desired = 1
              Minimum = 1
              Maximum = 1
                    │
          ┌─────────┴─────────┐
          │                   │
      AZ-A                  AZ-B
          │                   │
     ┌────▼────┐        ┌────▼────┐
     │ EC2     │        │         │
     │ Primary │   →    │Replacement
     └─────────┘        │ EC2     │
                        └─────────┘
```

The important part is:

> Configure the ASG across **at least 2 Availability Zones**.
> 

---

## What happens when EC2 fails?

```
              ASG
               │
               ▼
          EC2 Instance
               │
             💥 FAIL
               │
               ▼
       ASG detects unhealthy
               │
               ▼
        Terminates instance
               │
               ▼
       Launches replacement
               │
               ▼
          User Data runs
               │
               ▼
      Configure replacement
```

The ASG handles the replacement automatically.

---

# Why User Data matters

Suppose the replacement EC2 starts completely fresh.

It needs to install/configure your application.

You can use **EC2 User Data**:

```
New EC2
   ↓
User Data
   ↓
Install application
   ↓
Configure application
   ↓
Attach/configure resources
   ↓
Ready
```

For example:

```bash
#!/bin/bash
apt update
apt install nginx -y
systemctl enable nginx
systemctl start nginx
```

So every replacement instance can automatically configure itself.

---

# Elastic IP + ASG

If your architecture requires a specific Elastic IP, the replacement instance can use automation to attach the EIP.

For example:

```
ASG
 │
 │ EC2 failure
 ▼
New EC2
 │
 │ User Data / automation
 ▼
Find EIP
 │
 ▼
Associate EIP
```

The EC2 instance needs appropriate **IAM permissions** to make the required API calls.

---

# Important exam distinction

Don't automatically think:

> "ASG = Elastic IP automatically moves."
> 

That's **not the normal default behavior**.

ASG's core responsibility is:

```
Detect unhealthy instance
        ↓
Replace it
```

If you need custom actions such as attaching a particular EIP, you need additional automation.

---

# 3. ASG + EBS Snapshot

Now imagine your EC2 has important data on an EBS volume.

If the EC2 dies and ASG launches a replacement:

```
Old EC2 ❌
     │
     └── EBS data ❌/not automatically transferred

New EC2
     │
     └── Fresh EBS
```

So we need to preserve the data.

That's where **EBS Snapshots** come in.

---

# Architecture

```
                ASG
                 │
        ┌────────┴────────┐
        │                 │
       AZ-A              AZ-B
        │                 │
     EC2 #1             EC2 #2
        │                 │
      EBS                EBS
        │
        │ Snapshot
        ▼
    EBS Snapshot
        │
        │ EC2 failure
        ▼
   Create new EBS
        │
        ▼
   Attach to replacement EC2
```

---

# Lifecycle Hooks

This is where the architecture gets more interesting.

ASG **Lifecycle Hooks** allow you to perform custom actions when an instance is launching or terminating.

### During termination

```
EC2
 │
 │ Terminating
 ▼
ASG Lifecycle Hook
 │
 ▼
Create EBS Snapshot
 │
 ▼
Snapshot saved
 │
 ▼
Instance terminates
```

### During launch

```
New EC2
   │
   ▼
ASG Launch Lifecycle Hook
   │
   ▼
Find latest EBS Snapshot
   │
   ▼
Create EBS Volume
   │
   ▼
Attach EBS
   │
   ▼
Continue instance startup
```

---

# Complete flow

```
              EC2 #1
                 │
                 │ Failure
                 ▼
        ┌─────────────────┐
        │      ASG         │
        └────────┬────────┘
                 │
                 ▼
        Termination Hook
                 │
                 ▼
          EBS Snapshot
                 │
                 ▼
        Launch Replacement
                 │
                 ▼
         Launch Hook
                 │
                 ▼
      Create EBS from Snapshot
                 │
                 ▼
          Attach EBS
                 │
                 ▼
        New EC2 becomes ready
```

---

# Compare the 3 approaches

| Feature | Standby + CloudWatch | ASG | ASG + EBS Snapshot |
| --- | --- | --- | --- |
| Failure detection | CloudWatch | ASG health checks | ASG health checks |
| Replacement | Custom automation | Automatic | Automatic |
| Multiple AZs | Possible | Recommended | Recommended |
| User Data | Can be used | Very useful | Very useful |
| Elastic IP | Custom automation | Custom automation | Custom automation |
| Preserve EBS data | ❌ Not automatically | ❌ Not automatically | ✅ Snapshot |
| Lifecycle Hooks | Optional | Optional | **Important** |
| Complexity | Medium | Low/Medium | Higher |
| Best for | Simple recovery | Stateless EC2 | EC2 + persistent EBS data |

---

# 🧠 The most important SAA-C03 concept

Think about **stateless vs stateful**.

### Stateless EC2

If your application doesn't depend on local EC2 data:

```
EC2 dies
   ↓
ASG launches another EC2
   ↓
User Data configures it
   ↓
Done
```

This is relatively easy.

---

### Stateful EC2

If important data is stored on EBS:

```
EC2 dies
   ↓
ASG replacement
   ↓
Need the old data
   ↓
EBS Snapshot
   ↓
Create EBS
   ↓
Attach EBS
```

More automation is required.

---

# ⚠️ Exam trap: EBS is AZ-specific

Remember:

> **EBS volume belongs to one Availability Zone.**
> 

So you cannot simply take an EBS volume in:

```
us-east-1a
```

and directly attach it to an EC2 in:

```
us-east-1b
```

You need a snapshot and then create a new volume in the target AZ.

```
EBS
AZ-A
 │
 ▼
Snapshot
 │
 ▼
New EBS
AZ-B
 │
 ▼
Replacement EC2
```

This is an important SAA-C03 pattern.

---

# 🧠 Memory Trick

Remember:

### **CloudWatch → ASG → Snapshot**

**CloudWatch = Detect**

> "Something is wrong."
> 

**ASG = Replace**

> "I'll create another EC2."
> 

**Snapshot = Restore Data**

> "I'll bring the old EBS data back."
> 

So:

> **Detect → Replace → Restore**
> 

---

# 🎯 SAA-C03 Question Recognition

If you see:

> **"Automatically replace a failed EC2 instance"**
> 

Think:

**Auto Scaling Group**

If you see:

> **"Automatically configure the replacement EC2"**
> 

Think:

**User Data / automation**

If you see:

> **"Keep the same public IP"**
> 

Think:

**Elastic IP + automation**

If you see:

> **"Preserve data stored on EBS when the instance is replaced"**
> 

Think:

**EBS Snapshot + create/attach new EBS**

If you see:

> **"Perform custom actions when ASG launches/terminates an instance"**
> 

Think:

**ASG Lifecycle Hooks**

If you see:

> **"Highly available across Availability Zones"**
> 

Think:

**ASG spanning multiple AZs**

---

## One final mental picture

```
                    FAILURE
                       │
                       ▼
                 ┌───────────┐
                 │    ASG    │
                 └─────┬─────┘
                       │
                Replace EC2
                       │
              ┌────────┴────────┐
              │                 │
         Stateless           Stateful
              │                 │
          User Data       EBS Snapshot
              │                 │
              │          Create new EBS
              │                 │
              └────────┬────────┘
                       ▼
                Replacement EC2
                       │
                  Elastic IP
                  if required
```

**Core exam takeaway:**

> **ASG provides automatic EC2 replacement. User Data configures the replacement. EBS Snapshots preserve data when EBS must be recreated, and Lifecycle Hooks allow custom actions during ASG launch/termination.**
>
