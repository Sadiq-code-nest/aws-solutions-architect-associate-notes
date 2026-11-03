# AMI Sharing Process (Encrypted with AWS KMS) — Complete AWS SAA-C03 Understanding

One of the most confusing EC2 topics is **sharing an encrypted AMI between AWS accounts**.

The easiest way to remember is:

> **An encrypted AMI cannot be shared by itself. You must also share the KMS key used to encrypt it.**
> 

Think of an encrypted AMI as **a locked car**, and the KMS key as **the car key**.

You can give someone the car, but without the key, they can't drive it.

---

# First, what is an AMI?

An **Amazon Machine Image (AMI)** is a template used to launch EC2 instances.

It contains:

- Operating System
- Installed software
- Configuration
- EBS Snapshot(s)

Example:

```
Amazon Linux

+

Nginx

+

Java

+

Application

↓

AMI
```

Whenever you launch an EC2 instance, AWS creates it from the AMI.

---

# The Problem

Suppose:

**Account A** creates an AMI.

The AMI's EBS snapshot is encrypted using **KMS Key A**.

```
Account A

↓

AMI

↓

Encrypted EBS Snapshot

↓

KMS Key A
```

Now Account A wants to share this AMI with **Account B**.

Can Account B launch it immediately?

❌ No.

Because the AMI is encrypted.

Account B doesn't have permission to use KMS Key A.

---

# Real-Life Example ⭐⭐⭐⭐⭐

Imagine your friend gives you:

- A locked laptop
- The charger

But it doesn't give you the password.

Can you use it?

No.

The laptop is the **AMI**.

The password is the **KMS Key**.

Without both,

the AMI is useless.

---

# Complete Sharing Process

There are **5 steps**.

---

# Step 1 — Create an Encrypted AMI

Suppose Account A creates an AMI.

```
EC2 Instance

↓

Create AMI

↓

Encrypted Snapshot

↓

KMS Key A
```

Everything belongs to Account A.

---

# Step 2 — Share the AMI

An AMI is private by default.

Account A must give launch permission.

AWS calls this:

**Launch Permission**

Example:

```
AMI

↓

Modify Image Attribute

↓

Add Account B
```

Now Account B can **see** the AMI.

But...

Can it launch it?

❌ Still No.

Because the snapshot is encrypted.

---

# Important Point

Many students stop here.

They think:

"I shared the AMI."

Done.

❌ Wrong.

The encryption key is still private.

---

# Step 3 — Share the KMS Key ⭐⭐⭐⭐⭐

The EBS snapshot inside the AMI was encrypted using:

```
KMS Key A
```

Account B must also be allowed to use that key.

This is done by updating the **KMS key policy**.

Example:

```
KMS Key A

↓

Allow

↓

Account B
```

Now Account B can decrypt the snapshot.

---

# Architecture

```
Account A

AMI

↓

Encrypted Snapshot

↓

KMS Key A

↓

Key Policy

↓

Allow Account B
```

Without this permission,

Launching the AMI fails.

---

# Step 4 — IAM Permissions in Target Account

Even if the KMS Key Policy allows access,

The IAM user or IAM role in account B also needs permissions.

Usually:

```
kms:DescribeKey

kms:Decrypt

kms:ReEncrypt*

kms:CreateGrant
```

Let's understand why.

---

## DescribeKey

Allows AWS to read information about the KMS key.

Think:

"Does this key exist?"

---

## Decrypt

Allows Account B to decrypt the encrypted snapshot.

Without this,

the EC2 instance cannot boot.

---

## ReEncrypt*

Allows AWS to encrypt data using another KMS key.

Very useful if Account B wants its own encryption key.

---

## Create Grant

Allows AWS services (like EC2 and EBS) to temporarily use the KMS key during the launch process.

---

# Step 5 — Launch the EC2 Instance

Now Account B launches the instance.

AWS performs:

```
Read Snapshot

↓

Decrypt using Key A

↓

Create EBS Volume

↓

Launch EC2
```

The instance starts successfully.

---

# Optional Step — Re-encrypt Using Account B's Own KMS Key ⭐⭐⭐⭐⭐

Many companies don't want to continue using another account's KMS key.

Instead,

They launch the instance and choose:

```
KMS Key B
```

AWS performs:

```
Decrypt

↓

Key A

↓

Re-Encrypt

↓

Key B
```

Now the new EBS volume belongs completely to Account B.

---

# Real-Life Example ⭐⭐⭐⭐⭐

Imagine a software company.

Development Team

↓

Account A

Creates a secure Ubuntu AMI.

Production Team

↓

Account B

Needs to launch servers using that AMI.

Instead of keeping the Development Team's encryption forever,

Production launches the instance and re-encrypts the EBS volume using its own p**roduction KMS key**.

Now Production fully owns its encrypted storage.

---

# Complete Architecture

```
                    Account A

             EC2 Instance

                  │

          Create Encrypted AMI

                  │

          EBS Snapshot

                  │

              KMS Key A

                  │

      Share AMI + Share KMS Key

                  │

────────────────────────────────────────

                    Account B

            View Shared AMI

                  │

       IAM Permissions Granted

                  │

        Decrypt using Key A

                  │

(Optional)

Re-Encrypt using Key B

                  │

Launch EC2 Instance
```

---

# Why Can't AWS Just Share the Snapshot?

Because encryption belongs to KMS.

The snapshot is useless without permission to use the encryption key.

AWS protects encrypted data by requiring both:

- Access to the AMI
- Access to the KMS Key

---

# Common Mistake ⭐⭐⭐⭐⭐

Many students think:

"I shared the AMI, so anyone can launch it."

❌ Wrong.

If the snapshot uses KMS encryption,

you **must** also share the KMS key.

---

# AMI Sharing Checklist

✅ Share the AMI (Launch Permission)

↓

✅ Share the KMS Key

↓

✅ Update the KMS Key Policy

↓

✅ Grant IAM permissions

↓

✅ Launch EC2

↓

(Optional)

Re-encrypt using the destination account's own KMS key

---

# Required IAM Permissions

| Permission | Why Needed |
| --- | --- |
| `kms:DescribeKey` | Read key information |
| `kms:Decrypt` | Decrypt the source snapshot |
| `kms:ReEncrypt*` | Re-encrypt using another KMS key |
| `kms:CreateGrant` | Allow AWS services to use the KMS key during launch |

---

# Easy Memory Tricks ⭐⭐⭐⭐⭐

### Encrypted AMI

> **Encrypted AMI = Locked Machine**
> 

---

### KMS Key

> **KMS Key = The Key That Unlocks the AMI**
> 

---

### Launch Permission

> **Lets another account SEE and launch the AMI.**
> 

---

### KMS Key Policy

> **Lets another account USE the encryption key.**
> 

---

### Re-Encryption

> **Decrypt with the source key → Encrypt with the target account's key.**
> 

---

# AWS SAA-C03 Exam Tips

If the question says...

- **"Share an encrypted AMI across AWS accounts."**
    - ✅ Share both the AMI and the KMS key.
- **"AMI launches fail with KMS access errors."**
    - ✅ Check the KMS Key Policy and IAM permissions.
- **"Target account wants to own the encryption."**
    - ✅ Re-encrypt the EBS volume using a KMS key in the target account.
- **"Cross-account encrypted AMI."**
    - ✅ Remember: **AMI sharing alone is not enough.**

---

# Final Memory Diagram

```
                  Account A

             EC2 Instance

                  │

             Create AMI

                  │

         Encrypted Snapshot

                  │

              KMS Key A

          ┌────────┴────────┐

          │                 │

   Share AMI         Share KMS Key

          │                 │

          └────────┬────────┘

                   ▼

                Account B

        IAM Permissions

                   │

         Decrypt with Key A

                   │

(Optional)

Re-Encrypt with Key B

                   │

          Launch EC2 Instance
```

## One-line Summary

**To share an encrypted AMI across AWS accounts, you must share both the AMI (Launch Permission) and the KMS key used to encrypt its EBS snapshot. The target account also needs the appropriate IAM permissions, and it can optionally re-encrypt the new EBS volumes using its own KMS key.**
