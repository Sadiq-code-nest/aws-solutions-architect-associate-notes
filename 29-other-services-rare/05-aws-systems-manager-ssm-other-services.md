# AWS Systems Manager — Run Command, Patch Manager, Maintenance Windows & Automation

These four SSM features are easy to confuse in SAA-C03. The simplest way is to understand **what action you want to perform**.

## 🧠 First, the big picture

### One-line memory

```
                    AWS Systems Manager
                           │
       ┌───────────────────┼───────────────────┐
       │                   │                   │
       ▼                   ▼                   ▼
  Run Command        Patch Manager        Automation
       │                   │                   │
 "Run this command"  "Patch my servers"  "Perform this
                                            workflow"
                           │
                           ▼
                   Maintenance Windows
                    "WHEN should it run?"
```

> **Run Command = WHAT command?**
> 
> 
> **Patch Manager = PATCH what?**
> 
> **Maintenance Window = WHEN?**
> 
> **Automation = HOW do I automate the whole task?**
> 

---

# 1. Systems Manager — Run Command

## What is it?

**Run Command** allows you to remotely execute commands or SSM documents on one or **many managed instances**, without using SSH.

```
                  SSM Run Command
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
        EC2-1        EC2-2        EC2-3
       command       command       command
```

### Example

You have **100 EC2 instances** and need to execute:

```
sudo systemctl restart nginx
```

Instead of SSHing into 100 servers individually:

```
❌ SSH → EC2-1
❌ SSH → EC2-2
❌ SSH → EC2-3
...
```

Use:

```
SSM Run Command
      │
      ▼
100 EC2 instances
      │
      └── Restart nginx
```

---

## Important features

| Feature | Run Command |
| --- | --- |
| Execute commands remotely | ✅ |
| SSH required | ❌ |
| Run on multiple EC2 | ✅ |
| SSM Agent | ✅ |
| Output in Console | ✅ |
| Output to S3 | ✅ |
| Output to CloudWatch Logs | ✅ |
| SNS notifications | ✅ |
| IAM integration | ✅ |
| CloudTrail | ✅ |
| EventBridge integration | ✅ |

### Exam clue

> **"Execute a command on multiple EC2 instances without SSH."**
> 

👉 **Run Command**

---

# 2. Run Command — Real-world scenario

Suppose you have:

```
Production
│
├── Web-01
├── Web-02
├── Web-03
├── Web-04
└── Web-05
```

You need to check disk usage on all servers.

Run Command can execute:

```
df -h
```

on all selected instances.

The output can go to:

```
             Run Command
                  │
        ┌─────────┼──────────┐
        ▼         ▼          ▼
    Console       S3      CloudWatch
```

---

# 3. Run Command vs Session Manager

This is **very important for SAA-C03**.

|  | **Session Manager** | **Run Command** |
| --- | --- | --- |
| Main purpose | Interactive shell access | Execute commands remotely |
| SSH required | ❌ | ❌ |
| Interactive terminal | ✅ | ❌ |
| Multiple instances | Possible, but not main purpose | ✅ |
| Example | "I need to log into EC2" | "Run this command on 50 EC2s" |
| Memory | **Shell** | **Command** |

### 🧠 Easy trick

> **Session Manager = "Let me enter the server."**
> 
> 
> **Run Command = "Run this on the server."**
> 

---

# 4. Systems Manager — Patch Manager

## What is it?

**Patch Manager** automates patching of managed servers.

It can manage:

- OS updates
- Security patches
- Application patches

Supports managed:

- EC2 instances
- On-premises servers
- Linux
- Windows
- macOS

---

## Real example

You have:

```
500 EC2 instances
```

Security team says:

> "Every server must receive the latest approved security patches."
> 

Manually updating 500 servers would be painful.

Instead:

```
                 SSM Patch Manager
                       │
                       ▼
                Patch baseline
                       │
         ┌─────────────┼─────────────┐
         ▼             ▼             ▼
       EC2-1         EC2-2        EC2-500
         │             │             │
         ▼             ▼             ▼
       Patch          Patch         Patch
```

---

## Patch Manager can also

- Patch on demand
- Patch on a schedule
- Scan for missing patches
- Generate patch compliance information

### Exam clue

> **"Automatically install security patches/OS updates on EC2."**
> 

👉 **Patch Manager**

---

# 5. Patch Manager vs Run Command

| Requirement | Answer |
| --- | --- |
| Run `yum update` manually across instances | **Run Command** |
| Automatically manage approved patches | **Patch Manager** |
| Find missing patches | **Patch Manager** |
| Patch compliance reporting | **Patch Manager** |
| Execute arbitrary script | **Run Command** |

### Memory

> **Run Command = any command**
> 
> 
> **Patch Manager = patching specifically**
> 

---

# 6. Systems Manager — Maintenance Windows

This one is extremely easy once you understand the word **WHEN**.

## What is it?

A **Maintenance Window** defines **when** you are allowed to perform maintenance tasks on your instances.

For example:

> Every Sunday from 2:00 AM–4:00 AM.
> 

During this window you can perform:

- OS patching
- Software installation
- Driver updates
- Other maintenance tasks

---

## Architecture

```
             Maintenance Window
                    │
             Schedule: Sunday
              02:00–04:00
                    │
                    ▼
             Registered EC2
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
     Run Command          Patch Manager
          │                   │
          ▼                   ▼
    Install software       Apply patches
```

A Maintenance Window contains:

1. **Schedule**
2. **Duration**
3. **Registered instances**
4. **Registered tasks**

---

# 7. Real-world scenario

Your production environment is busy during the day.

You don't want to patch servers at 10 AM.

You create:

```
Maintenance Window
Every Sunday
02:00 AM
Duration: 2 hours
```

Then associate:

```
Maintenance Window
       │
       ▼
Production EC2
       │
       ▼
Patch Manager
       │
       ▼
Install security patches
```

### Exam clue

> **"Perform maintenance every Sunday at 2 AM."**
> 

👉 **Maintenance Windows**

### 🧠 Memory trick

> **Maintenance Window = WHEN**
> 

---

# 8. Systems Manager — Automation

Now we move beyond simply executing commands.

## What is Automation?

**SSM Automation** helps automate common operational and deployment tasks involving:

- EC2
- EBS
- AMIs
- Other AWS resources

It uses **Automation Runbooks**.

---

## Example

Suppose you want an automated workflow:

```
1. Stop EC2
       ↓
2. Create EBS snapshot
       ↓
3. Create AMI
       ↓
4. Restart EC2
       ↓
5. Send notification
```

Rather than manually performing each operation:

```
SSM Automation
      │
      ▼
Automation Runbook
      │
      ├── Stop EC2
      ├── Snapshot EBS
      ├── Create AMI
      └── Start EC2
```

---

# 9. What is an Automation Runbook?

Think of a **Runbook as a predefined recipe/instruction manual**.

Example:

```
AWS-RestartEC2Instance
```

The runbook defines:

> "These are the steps AWS Systems Manager should perform."
> 

Runbooks can be:

- Predefined by AWS
- Custom-created

---

# 10. How can Automation be triggered?

SSM Automation can be started by:

```
AWS Console
      │
AWS CLI
      │
AWS SDK
      │
EventBridge
      │
Maintenance Window
      │
AWS Config remediation
      │
      ▼
SSM Automation
```

This is useful in exam scenarios involving **automatic remediation**.

---

# 11. Automation + AWS Config

This is a good SAA-C03 pattern.

Imagine AWS Config detects:

> EC2 instance doesn't comply with a security rule.
> 

You can automatically remediate it:

```
EC2
 │
 ▼
AWS Config
 │
 │ detects non-compliance
 ▼
SSM Automation
 │
 ▼
Automation Runbook
 │
 ▼
Fix the resource
```

### Exam clue

> **"Automatically remediate non-compliant resources detected by AWS Config."**
> 

👉 **AWS Config + SSM Automation**

---

# 12. Automation vs Run Command

This is another important distinction.

|  | **Run Command** | **Automation** |
| --- | --- | --- |
| Main purpose | Execute command/script | Automate workflows |
| Simple command | ✅ | Possible |
| Multiple steps/workflow | Limited | ✅ |
| Runbook | SSM Command document | SSM Automation runbook |
| EC2 | ✅ | ✅ |
| Other AWS resources | Limited | ✅ |
| Automated remediation | Not primary | ✅ |
| Example | Restart nginx | Stop → snapshot → AMI → restart |

### 🧠 Memory

> **Run Command = one operational command**
> 
> 
> **Automation = a workflow/recipe**
> 

---

# 13. The four together

This is the most useful mental model:

```
                 AWS SYSTEMS MANAGER
                         │
       ┌─────────────────┼─────────────────┐
       │                 │                 │
       ▼                 ▼                 ▼
 Run Command       Patch Manager       Automation
       │                 │                 │
   WHAT command?     WHAT patches?     WHAT workflow?
       │                 │                 │
       └─────────────────┼─────────────────┘
                         │
                         ▼
                 Maintenance Window
                         │
                         ▼
                       WHEN?
```

---

# 14. SAA-C03 Decision Table

| Question wording | Choose |
| --- | --- |
| "Execute a command remotely" | **Run Command** |
| "Execute command on multiple EC2 instances" | **Run Command** |
| "No SSH required to execute command" | **Run Command** |
| "Install security patches" | **Patch Manager** |
| "Automatically patch operating systems" | **Patch Manager** |
| "Find missing patches" | **Patch Manager** |
| "Patch compliance report" | **Patch Manager** |
| "Perform maintenance every Sunday" | **Maintenance Window** |
| "Schedule maintenance tasks" | **Maintenance Window** |
| "Define when maintenance occurs" | **Maintenance Window** |
| "Automate multi-step operational tasks" | **Automation** |
| "Create AMI automatically" | **Automation** |
| "Create EBS snapshot automatically" | **Automation** |
| "Restart EC2 automatically using runbook" | **Automation** |
| "AWS Config automatically fixes non-compliance" | **Automation** |
| "EventBridge triggers operational workflow" | **Automation** |

---

# 🧠 Ultimate Memory Trick

Remember **4 W's**:

| SSM Feature | Think |
| --- | --- |
| **Run Command** | **WHAT command?** |
| **Patch Manager** | **WHAT patches?** |
| **Maintenance Window** | **WHEN?** |
| **Automation** | **WHOLE workflow?** |

And don't forget:

> **Session Manager = SHELL**
> 

So your SSM mental map becomes:

```
SSM
│
├── Session Manager → SHELL
│
├── Run Command → COMMAND
│
├── Patch Manager → PATCH
│
├── Maintenance Window → WHEN
│
└── Automation → WORKFLOW
```

### 🎯 SAA-C03 final takeaway

> **Session Manager gives secure shell access without SSH. Run Command executes commands remotely without SSH. Patch Manager automates patching. Maintenance Windows schedule when maintenance occurs. Automation uses runbooks to perform multi-step operational workflows and automated remediation.**
>
