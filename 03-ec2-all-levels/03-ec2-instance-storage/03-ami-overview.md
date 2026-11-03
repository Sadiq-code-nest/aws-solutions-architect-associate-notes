# 🖥️ AMI — Amazon Machine Image

### Simple definition

> **AMI is a pre-configured template/image that contains everything needed to launch an EC2 instance.**
> 

Think of an AMI as a **golden copy of your EC2 setup**.

```
AMI
├── Operating System
├── Software
├── Configuration
├── Monitoring setup
└── Other required settings
        ↓
   Launch EC2
        ↓
   Ready to use
```

### Why use AMI?

Without an AMI:

```
Launch EC2
   ↓
Install OS/software
   ↓
Configure application
   ↓
Configure monitoring
   ↓
Ready
```

With an AMI:

```
AMI
 ↓
Launch EC2
 ↓
Everything pre-configured
 ↓
Ready faster ⚡
```

---

## 🌍 AMI is Region-specific

An AMI is created for a **specific AWS Region**.

```
us-east-1
   ↓
My AMI
   ↓
Launch EC2
```

If you need the same AMI in another Region, you can **copy the AMI to that Region**.

```
us-east-1
   │
   │ Copy AMI
   ▼
us-west-2
   ↓
Launch EC2
```

---

## 📦 Types of AMIs

| AMI Type | Meaning |
| --- | --- |
| **Public AMI** | Provided publicly, including AWS-provided AMIs |
| **Your own AMI** | You create and maintain it |
| **AWS Marketplace AMI** | AMI provided by third parties, potentially paid |

---

# 🔧 How to Create Your Own AMI

Typical process:

```
1. Launch EC2
       ↓
2. Install software
       ↓
3. Configure application
       ↓
4. Stop EC2
       ↓
5. Create AMI
       ↓
6. AWS creates EBS snapshots
       ↓
7. Launch new EC2 instances from AMI
```

### Why stop the instance first?

Stopping helps ensure the disk data is in a **consistent state** before creating the image.

---

## 🏢 Real Example

Suppose your company has a web server that requires:

```
Ubuntu
+ Nginx
+ Java
+ Application
+ Monitoring agent
+ Configuration
```

Instead of manually setting this up every time:

```
AMI: Company-WebServer-AMI
          ↓
    ┌─────┼─────┐
    ↓     ↓     ↓
   EC2   EC2   EC2
```

Now you can launch multiple **identically configured** EC2 instances quickly.

---

## ⭐ AMI vs EBS Snapshot

This is an important distinction:

**EBS Snapshot**

> Backup of an **EBS volume**.
> 

**AMI**

> Template used to **launch EC2 instances**, containing the required OS/configuration and references to the underlying storage snapshots.
> 

```
EBS Snapshot
    ↓
Backup storage

AMI
    ↓
EC2 launch template/image
```

---

## 🧠 SAA-C03 Memory Trick

> **AMI = "My EC2 setup, packaged and ready to clone."**
> 

### Exam keywords

- **Pre-configured EC2** → AMI
- **Faster EC2 deployment** → AMI
- **Same software/configuration across many instances** → Custom AMI
- **Launch identical servers** → AMI
- **Copy AMI to another Region** → Copy AMI
- **Create AMI from customized EC2** → Stop → Create AMI → EBS snapshots

### One-line takeaway

> **AMI is a pre-packaged EC2 template containing your OS, software, and configuration, allowing you to launch consistently configured EC2 instances quickly.**
>
