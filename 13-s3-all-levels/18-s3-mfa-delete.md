# Amazon S3 — MFA Delete

## What is MFA Delete?

**S3 MFA Delete** adds an extra security layer to an S3 bucket by requiring **MFA authentication** for certain sensitive S3 operations.

It is mainly designed to protect **versioned S3 objects from permanent deletion** and prevent someone from disabling versioning.

### Simple idea

```
Without MFA Delete

User
  │
  │ Delete / Versioning change
  ▼
S3 Bucket
  │
  ▼
Operation happens

With MFA Delete

User
  │
  │ Sensitive operation
  ▼
S3 Bucket
  │
  ├── MFA required? ── YES ──► Verify MFA
  │                              │
  │                           Correct?
  │                           /      \
  │                         YES       NO
  │                          │         │
  ▼                          ▼         ▼
Operation allowed          ALLOW     DENY
```

---

## What problem does MFA Delete solve?

Imagine you have an important production S3 bucket:

```
s3://company-production-data
```

Versioning is enabled.

A compromised user tries to permanently delete an important object version.

With MFA Delete enabled:

```
Attacker
   │
   │ Permanent delete
   ▼
S3
   │
   │ MFA required
   ▼
❌ Cannot complete without MFA
```

So MFA Delete provides protection against **accidental or malicious permanent deletion**.

---

# Operations Requiring MFA

| Operation | MFA required? |
| --- | --- |
| Permanently delete an object version | ✅ Yes |
| Suspend versioning | ✅ Yes |
| Enable versioning | ❌ No |
| List deleted/object versions | ❌ No |
| Normal read/access | ❌ No |

### ⭐ Exam focus

Remember these two:

> **Permanent version deletion → MFA required**
> 

> **Suspend Versioning → MFA required**
> 

---

# MFA Delete + Versioning

MFA Delete **requires S3 Versioning to be enabled**.

Think:

```
S3 Bucket
   │
   └── Versioning
          │
          └── MFA Delete
```

You cannot use MFA Delete as an independent feature on a bucket without versioning.

---

# Why Versioning + MFA Delete Is Powerful

Consider:

```
                    S3 Bucket
                        │
                  Versioning ON
                        │
                  MFA Delete ON
                        │
          ┌─────────────┴──────────────┐
          │                            │
      Accidental                   Malicious
       delete                       delete
          │                            │
          ▼                            ▼
   Old version remains          MFA required
          │                            │
          └─────────────┬──────────────┘
                        ▼
                  Better recovery
```

### Example

You have:

```
report.pdf

Version 1
Version 2
Version 3
```

Someone accidentally deletes Version 3.

With versioning, the previous versions remain available.

MFA Delete adds another protection layer against **permanent deletion of those versions**.

---

# Who Can Enable/Disable MFA Delete?

This is an important SAA-C03 fact:

> **Only the bucket owner / root account can enable or disable MFA Delete.**
> 

### Exam trap

Don't confuse:

```
IAM User
IAM Role
Root Account
```

For enabling/disabling **MFA Delete**:

✅ **Root account / bucket owner**

---

# MFA Delete vs S3 Versioning

| Feature | S3 Versioning | MFA Delete |
| --- | --- | --- |
| Protects against accidental deletion | ✅ | ✅ Additional protection |
| Keeps previous object versions | ✅ | ❌ |
| Requires MFA | ❌ | ✅ For specific operations |
| Permanently deleting versions requires MFA | ❌ | ✅ |
| Suspending versioning requires MFA | ❌ | ✅ |
| Can be enabled without Versioning | ✅ | ❌ |
| Root/bucket owner required to enable | ❌ | ✅ MFA Delete specifically |
| Main purpose | Recover previous versions | Protect sensitive version-management operations |

---

# MFA Delete vs S3 Object Lock

These are easy to confuse in exam questions.

|  | **MFA Delete** | **S3 Object Lock** |
| --- | --- | --- |
| Main purpose | Protect version deletion/versioning changes | Prevent object deletion/modification |
| Uses MFA | ✅ | ❌ |
| Works with Versioning | Required | Required |
| Compliance/WORM | ❌ | ✅ |
| Retention period | ❌ | ✅ |
| Legal hold | ❌ | ✅ |
| Strong protection against intentional deletion | Good | **Very strong** |
| Typical use | Extra security for versioned bucket | Compliance / immutable data |

### Shortcut

> **MFA Delete → "Need MFA to perform sensitive version operations."**
> 

> **Object Lock → "You cannot delete/change the object during retention."**
> 

---

# SAA-C03 Scenario Examples

### Scenario 1

> A company wants additional protection against permanently deleting versions of critical S3 objects.
> 

**Answer:**

✅ Enable **S3 Versioning**

✅ Enable **MFA Delete**

---

### Scenario 2

> A user accidentally deletes `database-backup.tar.gz`. The company wants to recover the previous copy.
> 

**Answer:**

✅ **S3 Versioning**

MFA Delete isn't necessary for the basic recovery requirement.

---

### Scenario 3

> Security requires MFA before anyone can permanently delete a specific version.
> 

**Answer:**

✅ **S3 MFA Delete**

---

### Scenario 4

> An administrator wants to suspend versioning on a bucket protected by MFA Delete.
> 

**Answer:**

🔐 **MFA is required.**

---

### Scenario 5

> A company must ensure financial records cannot be deleted or modified for 7 years for regulatory compliance.
> 

Think:

✅ **S3 Object Lock**

Not MFA Delete.

---

# 🧠 Master Exam Cheat Sheet

```
S3 Versioning
      │
      ├── Recover previous versions
      │
      └── Protect against accidental overwrites/deletes
                │
                ▼
          MFA Delete
                │
                ├── Permanently delete version → MFA
                │
                └── Suspend Versioning → MFA

MFA NOT required for:
   ├── Enable Versioning
   └── List versions

MFA Delete:
   ├── Requires Versioning
   └── Root account / bucket owner enables/disables
```

### 🔥 One-line memory

> **Versioning saves the old version; MFA Delete protects the ability to permanently delete it.**
>
