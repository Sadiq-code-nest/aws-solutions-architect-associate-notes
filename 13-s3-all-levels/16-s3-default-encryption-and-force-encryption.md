# Amazon S3 — Default Encryption vs Bucket Policies

This topic is **very important for AWS SAA-C03**, especially when questions mention **S3 encryption, forcing encryption, bucket policies, and HTTPS/TLS**.

---

## 1. Default Encryption

Amazon S3 **automatically encrypts new objects at rest by default** using **SSE-S3**.

So, normally:

```
User / Application
       │
       │ PUT Object
       ▼
   S3 Bucket
       │
       │ Default Encryption
       ▼
   Encrypted Object
       │
       ▼
     SSE-S3
```

### Key point

You **do not need to create a bucket policy** just to make normal S3 objects encrypted at rest.

### Example

A developer uploads:

```
photo.jpg
```

without specifying an encryption header.

S3 automatically encrypts the object using SSE-S3.

---

# 2. What does "Force Encryption" mean?

Sometimes an organization wants to enforce a **specific encryption requirement**.

For example:

> "Every object uploaded to this bucket must use SSE-KMS."
> 

Simply enabling default encryption is **not the same as enforcing a client's requested encryption method**.

You can use an **S3 bucket policy with an explicit `Deny`** to reject uploads that don't meet the required encryption condition.

### Architecture

```
                    PUT Object
                       │
                       ▼
                 ┌─────────────┐
                 │ S3 Bucket   │
                 │   Policy    │
                 └──────┬──────┘
                        │
                Is encryption
                  acceptable?
                 /            \
               YES             NO
                │               │
                ▼               ▼
             ALLOW            DENY
                │
                ▼
          Encrypted Object
```

---

# 3. Default Encryption vs Force Encryption

| Feature | Default Encryption | Bucket Policy Enforcement |
| --- | --- | --- |
| Purpose | Automatically encrypt objects | Enforce a required encryption condition |
| Required? | S3 automatically encrypts new objects | Optional |
| Automatically encrypts new objects? | ✅ Yes | ❌ Policy itself doesn't encrypt |
| Can reject an upload? | ❌ No | ✅ Yes |
| Can require SSE-KMS? | Can configure default encryption for SSE-KMS | ✅ Can enforce it |
| Uses Bucket Policy? | ❌ Not required | ✅ Yes |
| Main purpose | Encryption at rest by default | Compliance / strict security control |
| Example | "Encrypt everything automatically" | "Reject uploads that don't use SSE-KMS" |

### ⭐ Exam shortcut

> **Default Encryption = S3 encrypts automatically.**
> 

> **Bucket Policy = enforce/reject requests that don't meet your requirement.**
> 

---

# 4. Important: Bucket Policy Is Evaluated Before Default Encryption

This is a common exam concept.

Think:

```
                    PUT Request
                        │
                        ▼
               ┌─────────────────┐
               │ Bucket Policy   │
               │ evaluation      │
               └────────┬────────┘
                        │
                 Allowed?
                 /      \
               NO        YES
               │          │
               ▼          ▼
             DENY    Default Encryption
                          │
                          ▼
                    Encrypted Object
```

### Why does this matter?

Suppose:

```
S3 Default Encryption = ON
```

and a bucket policy says:

> "Deny uploads that don't contain a specific encryption header."
> 

A request that doesn't satisfy the policy can be **denied before S3's default encryption mechanism gets a chance to protect the object**.

Therefore:

> **Default encryption doesn't override an explicit bucket-policy Deny.**
> 

---

# 5. Example — Force SSE-KMS

Imagine a company stores sensitive customer documents:

```
s3://company-sensitive-data/
```

Security requirement:

> Every uploaded object must use SSE-KMS.
> 

Architecture:

```
Developer
    │
    │ PUT Object
    │
    ▼
┌─────────────────────┐
│ S3 Bucket           │
│                     │
│ Bucket Policy       │
│                     │
│ Require SSE-KMS     │
└──────────┬──────────┘
           │
     ┌─────┴─────┐
     │           │
 SSE-KMS       No SSE-KMS
     │           │
     ▼           ▼
  ALLOW        DENY
     │
     ▼
Encrypted S3 Object
```

The policy can check the encryption-related request header and deny requests that don't meet the requirement.

---

# 6. Default Encryption ≠ Encryption in Transit

This distinction is **extremely important**.

There are two different security concerns:

### Encryption at rest

Protects data **stored inside S3**.

Examples:

- SSE-S3
- SSE-KMS
- SSE-C

### Encryption in transit

Protects data **while traveling between the client and S3**.

Use:

```
HTTPS / TLS
```

---

# 7. Force HTTPS / Secure Transport

A bucket policy can also require requests to use **HTTPS**.

AWS provides the condition key:

```
aws:SecureTransport
```

The policy can essentially say:

```
IF aws:SecureTransport == false
        ↓
      DENY
```

Architecture:

```
                 Client
                   │
          ┌────────┴─────────┐
          │                  │
       HTTPS              HTTP
          │                  │
          ▼                  ▼
       ALLOW                DENY
          │
          ▼
     S3 Bucket
```

### ⭐ Remember

```
aws:SecureTransport = false
            ↓
        DENY request
```

This forces clients to use **HTTPS/TLS**.

---

# 8. At Rest vs In Transit

| Requirement | Solution |
| --- | --- |
| Encrypt stored S3 objects | S3 encryption |
| Automatically encrypt new objects | S3 Default Encryption |
| Require SSE-KMS | Bucket Policy + SSE-KMS condition |
| Require HTTPS | Bucket Policy + `aws:SecureTransport` |
| Protect data while traveling | HTTPS/TLS |
| Protect data stored in S3 | SSE-S3 / SSE-KMS / SSE-C |

### Easy memory trick

```
              S3 Security
                  │
       ┌──────────┴──────────┐
       │                     │
    IN TRANSIT            AT REST
       │                     │
    HTTPS/TLS          S3 Encryption
       │                     │
 aws:SecureTransport   SSE-S3
       │               SSE-KMS
       │               SSE-C
       │
 Bucket Policy
```

---

# 9. SSE-S3 vs SSE-KMS vs SSE-C

| Encryption | Key managed by | User manages key? | Typical use |
| --- | --- | --- | --- |
| **SSE-S3** | AWS | ❌ | Standard S3 encryption |
| **SSE-KMS** | AWS KMS | Partially / customer-controlled KMS key | More control, auditing, key policies |
| **SSE-C** | Customer | ✅ | Customer provides encryption key with requests |

For SAA-C03, remember:

### SSE-S3

```
S3
 ↓
AWS manages encryption keys
```

### SSE-KMS

```
S3
 ↓
AWS KMS
 ↓
KMS key
```

Useful when you need **KMS key control, permissions, and auditability**.

### SSE-C

```
Customer
   │
   │ Encryption key
   ▼
   S3
```

Customer provides the encryption key with the request.

---

# 10. Scenario-Based SAA-C03 Examples

### Scenario 1 — "Encrypt all new objects"

> A company wants all newly uploaded S3 objects to be encrypted automatically.
> 

**Answer:**

✅ Enable/configure **S3 Default Encryption**

No bucket policy is necessarily required.

---

### Scenario 2 — "Only SSE-KMS uploads allowed"

> A security team requires every upload to use a specific SSE-KMS encryption requirement. Requests that don't meet the requirement must be rejected.
> 

**Answer:**

✅ **S3 Bucket Policy** with an explicit `Deny` condition for requests that don't use the required encryption.

---

### Scenario 3 — "Block HTTP"

> Security policy requires all communication with S3 to use encrypted connections.
> 

**Answer:**

✅ S3 Bucket Policy

✅ `aws:SecureTransport`

✅ Deny when `aws:SecureTransport = false`

---

### Scenario 4 — "Protect data at rest and in transit"

Requirement:

```
Data stored in S3
       +
Data traveling to S3
```

Use:

```
AT REST
   ↓
SSE-S3 / SSE-KMS

IN TRANSIT
   ↓
HTTPS/TLS
   ↓
Bucket Policy can enforce SecureTransport
```

---

# 11. The Biggest Exam Trap

### Question says:

> "Ensure all objects are encrypted."
> 

Think:

**Default Encryption**

### Question says:

> "Reject/deny uploads that aren't encrypted using SSE-KMS."
> 

Think:

**Bucket Policy enforcement**

### Question says:

> "Prevent users from accessing S3 over HTTP."
> 

Think:

**Bucket Policy + `aws:SecureTransport`**

### Question says:

> "Encrypt data while transmitted to S3."
> 

Think:

**HTTPS/TLS**

### Question says:

> "Encrypt data stored in S3."
> 

Think:

**SSE-S3 / SSE-KMS / SSE-C**

---

# 🧠 Final SAA-C03 Cheat Sheet

| Keyword in question | Think |
| --- | --- |
| **Automatically encrypt new objects** | S3 Default Encryption |
| **Encryption at rest** | SSE-S3 / SSE-KMS / SSE-C |
| **AWS-managed encryption** | SSE-S3 |
| **KMS key / key policy / audit** | SSE-KMS |
| **Customer provides encryption key** | SSE-C |
| **Force encryption** | Bucket Policy + explicit Deny |
| **Reject unencrypted uploads** | Bucket Policy |
| **Force SSE-KMS** | Bucket Policy |
| **Encryption in transit** | HTTPS/TLS |
| **Block HTTP** | `aws:SecureTransport` |
| **SecureTransport = false** | DENY |
| **Default Encryption vs Policy** | Default encryption encrypts; policy can enforce/reject |
| **Explicit Deny** | Overrides an Allow/default behavior |

### One-line memory

> *Default Encryption protects automatically; Bucket Policies enforce rules; HTTPS protects data in transit; SSE- protects data at rest.**
>
