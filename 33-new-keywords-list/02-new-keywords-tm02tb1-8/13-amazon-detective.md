# Amazon Detective — AWS SAA-C03

**Amazon Detective** is a security service that **automatically analyzes AWS data to investigate and find the root cause of suspicious activity or security incidents**.

Think:

> 🔎 **Detective = Investigate "What happened, how did it happen, and what is affected?"**
> 

### Simple example

Suppose an IAM user suddenly performs unusual API calls:

```
IAM User
   ↓
Unusual activity 🚨
   ↓
Amazon Detective
   ↓
Analyzes activity/data
   ↓
Finds relationships & patterns
   ↓
Helps investigate the incident
```

Detective can help security teams understand relationships between things like:

- Users
- IP addresses
- AWS resources
- API activity
- Security findings

### 🔥 Don't confuse it with GuardDuty

| Service | Purpose |
| --- | --- |
| **GuardDuty** | 🚨 **Detect** suspicious/threat activity |
| **Detective** | 🔎 **Investigate** and understand the security incident |

Easy example:

> **GuardDuty:** "Something suspicious is happening!" 🚨
> 
> 
> **Detective:** "Let's investigate what happened and how everything is connected." 🔎
> 

### SAA-C03 shortcut

If the question says:

> **"Investigate a security finding / analyze suspicious activity / determine root cause"**
> 

👉 **Amazon Detective**
