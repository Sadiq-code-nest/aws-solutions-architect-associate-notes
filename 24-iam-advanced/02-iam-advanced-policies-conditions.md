# 🔐 IAM Conditions — Clear, Simple + Exam Mastery (SAA-C03)

---

# 🧩 1. BIG PICTURE (Understand First)

👉 **IAM Conditions = Extra rules that decide WHEN a policy applies**

- Not just **Allow/Deny**
- But **Allow/Deny IF something is true**

---

## 🧠 Core Idea

> IAM Policy = **Who + What + Resource + Condition**
> 

---

# 🔍 2. How Conditions Work

👉 Example logic:

```
Allow EC2 actions
ONLY IF request comes from specific IP
```

---

# 🌐 3. `aws:SourceIp` (VERY IMPORTANT)

- images
    
    
    
    

---

## 🧠 What it does:

👉 Restricts API calls based on **client IP address**

---

## ✅ Example Use Case:

- Allow access only from office IP
- Block unknown locations

---

## ✅ Example Policy

```json
{
  "Effect": "Deny",
  "Action": "*",
  "Resource": "*",
  "Condition": {
    "NotIpAddress": {
      "aws:SourceIp": ["192.0.2.0/24", "203.0.113.0/24"]
    }
  }
}
```

---

## 🧠 Exam Trick

> “Restrict access by IP” → **aws:SourceIp**
> 

---

# 🌍 4. `aws:RequestedRegion`

---

## 🧠 What it does:

👉 Restricts **which AWS region** API calls can be made to

---

## ✅ Example Use Case:

- Allow only EU regions
- Prevent usage in other regions

---

## ✅ Example Policy

```json
{
  "Effect": "Deny",
  "Action": ["ec2:*", "rds:*", "dynamodb:*"],
  "Resource": "*",
  "Condition": {
    "StringEquals": {
      "aws:RequestedRegion": ["eu-central-1", "eu-west-1"]
    }
  }
}
```

---

## 🧠 Exam Trick

> “Restrict region usage” → **aws:RequestedRegion**
> 

---

# 🏷️ 5. `ec2:ResourceTag` (Tag-Based Access)

---

## 🧠 What it does:

👉 Controls access based on **resource tags**

---

## ✅ Example Use Case:

- Only manage EC2 instances with tag `Project=DataAnalytics`

---

## ✅ Example Policy

```json
{
  "Effect": "Allow",
  "Action": ["ec2:StartInstances", "ec2:StopInstances"],
  "Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*",
  "Condition": {
    "StringEquals": {
      "ec2:ResourceTag/Project": "DataAnalytics"
    }
  }
}
```

---

## 🧠 Exam Trick

> “Access based on tags” → **ResourceTag**
> 

---

# 🔐 6. `aws:MultiFactorAuthPresent` (MFA Enforcement)

---

## 🧠 What it does:

👉 Forces user to use **MFA (Multi-Factor Authentication)**

---

## ✅ Example Use Case:

- Deny dangerous actions without MFA

---

## ✅ Example Policy

```json
{
  "Effect": "Deny",
  "Action": ["ec2:StopInstances", "ec2:TerminateInstances"],
  "Resource": "*",
  "Condition": {
    "BoolIfExists": {
      "aws:MultiFactorAuthPresent": "false"
    }
  }
}
```

---

## 🧠 Exam Trick

> “Force MFA for critical actions” → **aws:MultiFactorAuthPresent**
> 

---

# 🪣 7. IAM for S3 (VERY IMPORTANT)

---

## 🔑 Two Levels:

---

## 📁 Bucket-Level Permission

- `s3:ListBucket`
- Resource:

```
arn:aws:s3:::my-bucket
```

---

## 📄 Object-Level Permission

- `s3:GetObject`, `PutObject`, `DeleteObject`
- Resource:

```
arn:aws:s3:::my-bucket/*
```

---

## ✅ Example Policy

```json
{
  "Effect": "Allow",
  "Action": ["s3:ListBucket"],
  "Resource": "arn:aws:s3:::test"
},
{
  "Effect": "Allow",
  "Action": ["s3:GetObject", "s3:PutObject", "s3:DeleteObject"],
  "Resource": "arn:aws:s3:::test/*"
}
```

---

## 🧠 Exam Trick

> Bucket = no `/*`
> 
> 
> Object = must use `/*`
> 

---

# 🏢 8. `aws:PrincipalOrgID` (ORG-Level Security)

- images
    
    
    
    

---

## 🧠 What it does:

👉 Restricts access to **only accounts inside your AWS organisation.**

---

## ✅ Use Case:

- Block external accounts
- Allow only internal org access

---

## ✅ Example Policy

```json
{
  "Effect": "Allow",
  "Action": ["s3:GetObject", "s3:PutObject"],
  "Resource": "arn:aws:s3:::2022-financial-data/*",
  "Condition": {
    "StringEquals": {
      "aws:PrincipalOrgID": "o-yyyyyyyyyy"
    }
  }
}
```

---

## 🧠 Exam Trick

> “Only allow accounts in my org” → **aws:PrincipalOrgID**
> 

---

# ⚡ 9. 30-Second Memory Trick

| Condition | Meaning |
| --- | --- |
| aws:SourceIp | Restrict by IP |
| aws:RequestedRegion | Restrict region |
| ResourceTag | Tag-based access |
| MFA | Force MFA |
| PrincipalOrgID | Org-level restriction |

---

# 📌 10. AWS SAA-C03 Cheat Sheet

- Conditions = fine-grained control
- IP restriction → SourceIp
- Region restriction → RequestedRegion
- Tags → ResourceTag
- MFA → MultiFactorAuthPresent
- Org restriction → PrincipalOrgID

---

# 🚨 11. Exam Rules (CRITICAL)

| If Question Says… | Answer |
| --- | --- |
| Restrict by IP | aws:SourceIp |
| Restrict region | aws:RequestedRegion |
| Enforce MFA | aws:MultiFactorAuthPresent |
| Tag-based access | ResourceTag |
| Org-only access | aws:PrincipalOrgID |

---

# ⚠️ 12. Common Traps

| Trap | Wrong | Correct |
| --- | --- | --- |
| S3 object access | bucket ARN ❌ | use `/*` |
| MFA enforcement | IAM only ❌ | use condition |
| Org restriction | IAM role ❌ | PrincipalOrgID |
| Region restriction | SCP only ❌ | can use IAM condition |

---

# 🧪 13. Mini Practice

---

### ✅ Q1

Allow only office IP

👉 aws:SourceIp

---

### ✅ Q2

Allow only EU region

👉 aws:RequestedRegion

---

### ✅ Q3

Allow only tagged EC2

👉 ResourceTag

---

### ✅ Q4

Force MFA for delete

👉 MultiFactorAuthPresent

---

### ✅ Q5

Restrict to org accounts

👉 PrincipalOrgID

---

# 🚀 FINAL ULTRA-RECAP

> 🔐 IAM Conditions = **Control WHEN access is allowed**
> 
- IP
- Region
- Tags
- MFA
- Organization

---

If you want next:

👉 **IAM Policies vs SCP vs Permission Boundaries (VERY IMPORTANT)**

👉 **Hard IAM scenario questions (exam level)**

👉 **Real-world IAM architecture patterns**

Just tell me 🔥
