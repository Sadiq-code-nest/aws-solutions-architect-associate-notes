# Amazon S3 Replication — Encryption Considerations (AWS SAA-C03)

One of the most confusing S3 topics is **how encryption affects replication**.

The easiest way to remember is:

> **S3 can replicate encrypted objects, but the replication process depends on the type of encryption used.**
> 

---

# First, How Does Normal S3 Replication Work?

Suppose you have two buckets.

```
Source Bucket (Singapore)

↓

Replication

↓

Destination Bucket (Tokyo)
```

When you upload a file to the source bucket,

S3 automatically copies it to the destination bucket.

Simple.

---

# But What Happens If the File Is Encrypted?

Imagine the file:

```
salary.xlsx
```

Instead of storing:

```
salary.xlsx
```

S3 stores:

```
Encrypted salary.xlsx
```

Now S3 must decide:

**Can I copy this encrypted object?**

The answer depends on **how it was encrypted.**

---

# Case 1 — Unencrypted Objects ✅

Suppose your object is:

```
report.pdf
```

No encryption.

Replication is simple.

```
Upload File

↓

Source Bucket

↓

Replication

↓

Destination Bucket
```

Nothing special is required.

---

# Case 2 — SSE-S3 (Amazon Managed Encryption) ✅

Suppose the object uses:

**SSE-S3**

AWS automatically manages the encryption.

```
Upload

↓

S3 encrypts automatically

↓

Replication

↓

Destination Bucket
```

No additional configuration is required.

Replication works automatically.

---

## Real-Life Example ⭐⭐⭐⭐⭐

A company stores employee documents in S3.

They enable **SSE-S3**.

Every night,

S3 replicates all files to another region for disaster recovery.

The company never manages encryption keys.

AWS handles everything automatically.

---

# Case 3 — SSE-C (Customer-Provided Keys) ✅

This one surprises many students.

SSE-C means:

**The customer provides the encryption key with every upload and download request.**

AWS never stores that key permanently.

Example:

```
Client

↓

Uploads File

+

Provides Encryption Key

↓

Amazon S3
```

Even though S3 doesn't own the key,

**S3 Replication still supports SSE-C objects**, provided the replication configuration is set correctly.

---

## Real-Life Example

A hospital has its own encryption system.

Instead of letting AWS manage keys,

the hospital provides its own encryption key whenever a file is uploaded.

Those encrypted files can still be replicated to another bucket.

---

# Case 4 — SSE-KMS ⭐⭐⭐⭐⭐

This is the most important exam topic.

Suppose your source bucket uses:

```
SSE-KMS
```

Now the object is encrypted using a KMS key.

Example:

```
Source Bucket

↓

Encrypted

↓

KMS Key A
```

When S3 copies the object,

Can it simply copy the encrypted bytes?

❌ No.

Because the destination bucket must encrypt the object using its own KMS key.

---

# Why?

Imagine:

Singapore uses:

```
KMS Key A
```

Tokyo uses:

```
KMS Key B
```

These are different keys.

S3 must:

1. Decrypt the object using Key A.
2. Encrypt it again using Key B.
3. Store it in the destination bucket.

---

# Architecture

```
Source Bucket

↓

Encrypted Object

↓

KMS Key A

↓

Decrypt

↓

Replication

↓

Encrypt Again

↓

KMS Key B

↓

Destination Bucket
```

This process happens automatically after you configure the required permissions.

---

# What Must You Configure?

SSE-KMS replication requires additional setup.

### 1. Enable KMS Replication

Replication of SSE-KMS objects is **not automatic**.

You must explicitly enable replication for KMS-encrypted objects.

---

### 2. Specify the Destination KMS Key

S3 needs to know:

> **Which KMS key should encrypt objects in the destination bucket?**
> 

Example:

```
Source

↓

KMS Key A

↓

Destination

↓

KMS Key B
```

---

### 3. Update the Destination KMS Key Policy

The destination KMS key must allow S3 to use it.

Without permission,

S3 cannot encrypt the replicated object.

Think of it like giving S3 permission to lock files with the destination key.

---

### 4. Configure IAM Permissions

The replication IAM role needs permissions for **both** keys.

Specifically:

```
Source KMS Key

↓

kms:Decrypt
```

and

```
Destination KMS Key

↓

kms:Encrypt
```

Without both permissions,

replication fails.

---

# Complete Replication Flow

```
Upload File

↓

Source Bucket

↓

Encrypted with KMS Key A

↓

Replication IAM Role

↓

Decrypt using kms:Decrypt

↓

Copy Object

↓

Encrypt using kms:Encrypt

↓

Destination Bucket

↓

Encrypted with KMS Key B
```

---

# Real-Life Example ⭐⭐⭐⭐⭐

A multinational bank stores customer data in Singapore.

The bucket uses **SSE-KMS** with **Key A**.

For disaster recovery,

The data is replicated to London.

London follows different security policies.

So it uses k**ey B**.

During replication:

- S3 decrypts the object with k**ey A.**
- Transfers it securely
- Encrypts it again with **Key B**
- Stores it in the London bucket

Users in London can now access the data using their local KMS key.

---

# What About KMS Throttling?

Every replicated object requires KMS API calls.

Imagine replicating:

```
10 million files
```

Each file may require:

- Decrypt request
- Encrypt request

That's millions of KMS API calls.

If too many requests happen at once,

KMS may throttle them.

---

# Example

A company uploads:

```
500,000 photos every hour.
```

Replication starts immediately.

S3 sends thousands of requests to KMS.

Eventually,

KMS says:

```
Too many requests.
```

This is called the **KMS Throttling.**

---

# Solution

Request a higher **AWS Service Quotas** limit for KMS API requests.

Think:

More files

↓

More KMS requests

↓

Increase KMS quota if needed.

---

# What About Multi-Region KMS Keys?

Many students think:

> "If I use Multi-Region Keys, S3 will simply copy the encrypted object."
> 

❌ Wrong.

Even if the source and destination buckets use **Multi-Region KMS Keys (MRKs)**,

Amazon S3 **still treats them as independent keys during replication.**

S3 still performs:

```
Decrypt

↓

Encrypt Again
```

---

# Why?

Because S3's replication engine currently doesn't optimize MRKs.

Although the keys have identical key material,

S3 still performs the normal replication workflow.

---

# Diagram

```
Source Bucket

↓

Encrypted

↓

Primary MRK

↓

Decrypt

↓

Replication

↓

Encrypt Again

↓

Replica MRK

↓

Destination Bucket
```

So remember:

**MRKs simplify application encryption, but they do not eliminate re-encryption during S3 replication.**

---

# Encryption Comparison

| Encryption Type | Replication Supported? | Extra Configuration? |
| --- | --- | --- |
| Unencrypted | ✅ Yes | No |
| SSE-S3 | ✅ Yes | No |
| SSE-C | ✅ Yes | Minimal configuration |
| SSE-KMS | ✅ Yes | Yes (enable replication, destination KMS key, IAM permissions, key policy) |
| SSE-KMS with Multi-Region Keys | ✅ Yes | Still decrypts and re-encrypts |

---

# Easy Memory Tricks ⭐⭐⭐⭐⭐

### Unencrypted

> **Copy directly.**
> 

---

### SSE-S3

> **AWS manages everything—replication just works.**
> 

---

### SSE-C

> **Customer provides the key, but replication is still supported.**
> 

---

### SSE-KMS

> **Needs extra setup and KMS permissions.**
> 

---

### IAM Role

> **Decrypt from source key → Encrypt with destination key.**
> 

---

### Multi-Region KMS Keys

> **Same key material, but S3 still decrypts and re-encrypts during replication.**
> 

---

# AWS SAA-C03 Exam Tips

If the question says...

- **"Replicate SSE-KMS encrypted S3 objects."**
    - ✅ Enable KMS replication and specify a destination KMS key.
- **"Replication fails because of missing permissions."**
    - ✅ Check the IAM role and KMS key policies.
- **"Large-scale replication causes KMS throttling."**
    - ✅ Request a higher KMS Service Quota.
- **"Using Multi-Region KMS Keys with S3 Replication."**
    - ✅ S3 still decrypts and re-encrypts the objects.

---

# Final Memory Diagram

```
                    Upload File
                         │
                         ▼
              Source S3 Bucket (SSE-KMS)
                         │
                  KMS Key A (Decrypt)
                         │
                         ▼
                S3 Replication Engine
                         │
                  KMS Key B (Encrypt)
                         │
                         ▼
          Destination S3 Bucket (SSE-KMS)

        (Even with Multi-Region KMS Keys,
         S3 still decrypts and re-encrypts.)
```

## One-line Summary

**S3 Replication supports unencrypted, SSE-S3, SSE-C, and SSE-KMS objects. For SSE-KMS, you must configure a destination KMS key, appropriate IAM permissions, and KMS key policies. Even when using KMS Multi-Region Keys, Amazon S3 still decrypts and re-encrypts replicated objects.**
