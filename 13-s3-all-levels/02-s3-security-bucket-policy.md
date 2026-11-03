# S3 Security Bucket Policy

## Amazon S3 Bucket Policies — SAA-C03 Exam Note

A **bucket policy** is a **resource-based JSON policy attached directly to an S3 bucket** that controls **who can access which S3 resources and what they can do**.

Think:

> **IAM Policy = “What can this identity do?”**
> 
> 
> **Bucket Policy = “Who can access this bucket/object, and what can they do?”**
> 

---

### 1. What can a Bucket Policy contain?

| Element | Meaning | Example |
| --- | --- | --- |
| **Effect** | Allow or Deny | `Allow` |
| **Principal** | Who gets access | User, account, `*` |
| **Action** | What operation is allowed/denied | `s3:GetObject` |
| **Resource** | Which bucket/object | `arn:aws:s3:::my-bucket/*` |
| **Condition** | Optional condition | Require HTTPS |

Simple mental model:

**Principal → Action → Resource → Condition**

---

## 2. Example: Public S3 Bucket

Suppose you host public images:

```
Internet Users
      │
      │ s3:GetObject
      ▼
┌─────────────────┐
│   S3 Bucket     │
│   images        │
└─────────────────┘
```

A bucket policy can allow anonymous users to read objects.

Conceptually:

```
Principal: *
Action: s3:GetObject
Resource: bucket objects
Effect: Allow
```

### SAA-C03 clue

If the question says:

> **“Make objects publicly readable”**
> 

Think:

**S3 Bucket Policy + public access configuration**

⚠️ But remember **S3 Block Public Access** can prevent public access even when a bucket policy attempts to allow it.

---

# 3. User accessing S3 → IAM Policy

Suppose:

```
IAM User
   │
   │ IAM permissions
   ▼
S3 Bucket
```

The IAM user has a policy allowing:

```
s3:GetObject
```

This is an **identity-based IAM policy**.

### Exam clue

> “Grant an IAM user permission to access S3.”
> 

Think:

**IAM Policy**

---

# 4. EC2 accessing S3 → IAM Role

Don't store AWS access keys inside EC2.

Instead:

```
                 IAM Role
                    │
                    ▼
EC2 Instance ──────────────► S3
```

Attach an **IAM role** to the EC2 instance.

The role grants permissions such as:

```
s3:GetObject
s3:PutObject
```

### Exam clue

> “EC2 needs access to S3 without storing credentials.”
> 

Answer:

**IAM Role**

---

# 5. Cross-Account S3 Access

Example:

```
AWS Account A                    AWS Account B

IAM User ──────────────────────► S3 Bucket
                                  │
                                  │
                            Bucket Policy
                            allows Account A
```

Suppose:

- Company A owns the S3 bucket.
- Company B needs to read objects.
- You want to grant Company B access.

A **bucket policy** can specify the external AWS account/user as the **Principal**.

### Exam clue

> “Allow another AWS account to access my S3 bucket.”
> 

Think:

**S3 Bucket Policy → Cross-account access**

---

# 6. Bucket Policy vs IAM Policy

| Feature | IAM Policy | S3 Bucket Policy |
| --- | --- | --- |
| Type | Identity-based | Resource-based |
| Attached to | IAM user/role/group | S3 bucket |
| Defines Principal? | Usually no | **Yes** |
| Controls S3 access | ✅ | ✅ |
| Cross-account access | Possible | **Very common choice** |
| Public access | Not the normal approach | **Yes** |
| Attached directly to S3 | ❌ | ✅ |

### Memory Trick

**IAM = “I am allowed to…”**

**Bucket Policy = “This bucket allows…”**

---

# 7. Important: Explicit DENY

This is extremely important for SAA-C03.

Suppose:

```
IAM Policy
     ↓
ALLOW s3:GetObject

Bucket Policy
     ↓
DENY s3:GetObject
```

Result:

❌ **Access Denied**

Because:

> **Explicit DENY overrides ALLOW.**
> 

Think:

```
ALLOW + ALLOW  → ✅
ALLOW + no DENY → ✅
ALLOW + DENY   → ❌
```

---

# 8. Bucket Policy to Force Encryption

A bucket policy can also enforce security requirements.

For example, your organization wants uploaded objects to use encryption.

Conceptually:

```
Client
   │
   │ PutObject
   ▼
S3 Bucket
   │
   ├── Encryption requirement satisfied → ✅ Allow
   │
   └── Encryption requirement missing → ❌ Deny
```

A bucket policy can use a **Condition** to deny uploads that don't meet the required encryption condition.

### SAA-C03 clue

> “Prevent users from uploading unencrypted objects.”
> 

Think:

**S3 Bucket Policy + explicit DENY + Condition**

---

# 9. Block Public Access

S3 provides **Block Public Access** settings to help prevent accidental exposure of data.

They can be configured at different levels, including:

- Account
- Bucket
- Access point

Conceptually:

```
                 S3 Block Public Access
                         │
                         ▼
                  ┌──────────────┐
Internet ───────X │  S3 Bucket   │
                  │ Private Data │
                  └──────────────┘
```

### Why does this exist?

To prevent situations like:

> Developer accidentally makes a company's private S3 bucket public.
> 

---

## 10. Bucket Policy vs Block Public Access

| Situation | Think |
| --- | --- |
| Give public read access | Bucket Policy |
| Prevent accidental public access | **Block Public Access** |
| Company data should NEVER be public | **Keep Block Public Access enabled** |
| Allow another AWS account | Bucket Policy |
| EC2 needs S3 access | IAM Role |
| IAM user needs S3 access | IAM Policy |
| Force encryption during upload | Bucket Policy + Condition |

---

# 11. Real SAA-C03 Scenario

### Scenario

A company stores confidential reports in:

```
s3://company-reports
```

A junior developer accidentally changes permissions and exposes the bucket publicly.

The company wants to ensure that the bucket **cannot become publicly accessible**.

### Best concept

**S3 Block Public Access**

Not:

❌ IAM policy alone

❌ EC2 role

❌ NACL

❌ Security Group

### Why?

Because the problem is:

> **Preventing public exposure of S3 data.**
> 

That's exactly what **S3 Block Public Access** is designed to help prevent.

---

# 12. Another Scenario — Cross Account

### Requirement

Account A owns:

```
company-data
```

Account B's application needs:

```
s3:GetObject
```

from that bucket.

Architecture:

```
Account B
┌──────────────┐
│ Application  │
└──────┬───────┘
       │
       │ GetObject
       ▼
Account A
┌────────────────────┐
│ S3 Bucket           │
│                    │
│ Bucket Policy      │
│ Principal: B       │
│ Action: GetObject  │
└────────────────────┘
```

### Exam answer

**S3 Bucket Policy allowing the appropriate cross-account principal.**

---

# 13. S3 Access Decision — Easy Mental Model

When thinking about S3 authorization:

```
             Request
                │
                ▼
       ┌─────────────────┐
       │ IAM Permissions │
       └────────┬────────┘
                │
                ▼
       ┌─────────────────┐
       │ Bucket Policy   │
       └────────┬────────┘
                │
                ▼
       Any Explicit DENY?
             /       \
           YES       NO
            │         │
            ❌        ✅
          Denied    Allowed*
```

- Assuming the request otherwise has the required permissions and isn't blocked by another applicable control.

---

# 🔥 SAA-C03 Quick Revision

| Question wording | Think |
| --- | --- |
| “Allow public access to S3 objects” | **Bucket Policy** |
| “Allow another AWS account” | **Bucket Policy** |
| “EC2 needs S3 access” | **IAM Role** |
| “IAM user needs S3 access” | **IAM Policy** |
| “Prevent accidental public exposure” | **S3 Block Public Access** |
| “Prevent unencrypted uploads” | **Bucket Policy + Condition/DENY** |
| “Explicit DENY exists” | **DENY wins** |
| “Resource-based policy” | **S3 Bucket Policy** |
| “Identity-based policy” | **IAM Policy** |

### 🧠 One-line memory trick

> **IAM tells an identity what it can do; Bucket Policy tells the bucket who it will allow; Block Public Access stops accidental public exposure.**
>
