# Amazon S3 Glacier Vault Lock vs S3 Object Lock (SAA-C03) — Complete Crystal Clear Notes

This is a **very important compliance topic** in the AWS SAA exam.

Students often confuse:

- **S3 Glacier Vault Lock**
- **S3 Object Lock**

Both use the **WORM model**, but they protect **different things**.

---

# Step 1: First Understand WORM

WORM means:

> **Write Once, Read Many**
> 

Once data is written,

✔ Read it many times.

❌ Cannot modify it.

❌ Cannot delete it.

---

## Real-Life Example

Imagine writing a legal contract on paper.

After both parties sign it,

```
Contract

↓

Signed

↓

Locked forever
```

You can:

✅ Read

✅ Copy

✅ Print

But you cannot:

❌ Erase

❌ Modify

❌ Replace

That is WORM.

---

# Why WORM Exists?

Many industries require it by law.

Examples:

- Banks
- Hospitals
- Government
- Stock Exchanges
- Medical Records
- Tax Records

Reason:

Nobody should secretly modify historical records.

---

# Example

Suppose a bank stores

```
Transaction History
```

If someone changes

```
$10,000

↓

$100
```

Huge fraud.

WORM prevents this.

---

# Now Let's Learn Both Services

---

# 1. S3 Glacier Vault Lock

This works only with:

```
Amazon S3 Glacier Vault
```

(Not regular S3 buckets.)

---

## What does it lock?

It locks the **Vault Policy**.

Not individual objects.

---

Imagine:

```
Glacier Vault

↓

Contains

1000 Objects
```

The Vault has a policy like:

```
Nobody can delete objects

Retention = 7 years
```

Normally,

an administrator could change that policy.

With Vault Lock,

the policy becomes permanently locked.

---

# Diagram

```
Glacier Vault

↓

Vault Policy

↓

LOCKED

↓

Cannot Change

Cannot Delete

Cannot Edit
```

---

# Example

Company policy:

```
Keep backup

10 years
```

Administrator tries:

```
Change policy

↓

Retention = 2 years
```

Result

❌ Not allowed.

Vault Lock prevents it forever.

---

# Why?

Compliance.

Government regulations.

Financial laws.

Medical records.

---

# Key Point

Vault Lock protects

✅ The policy

NOT

❌ Individual objects.

---

# Real-Life Analogy

Imagine a company safe.

Inside:

```
1000 documents
```

The safe has one rule:

```
Nobody opens it before 2035.
```

Now weld the lock permanently.

Nobody can change that rule.

That is Glacier Vault Lock.

---

# SAA Exam Keywords

- Glacier
- Compliance
- Vault Policy
- Permanent Policy
- WORM

Think:

✅ Glacier Vault Lock

---

# 2. S3 Object Lock

This works on

Regular

```
S3 Buckets
```

Not Glacier Vaults.

---

Object Lock protects

Individual object versions.

Example

Bucket

```
employee-records
```

Contains

```
employee.pdf
```

Version

```
Version 1
```

Object Lock

↓

Locks Version 1

Nobody can delete it.

---

Diagram

```
S3 Bucket

↓

employee.pdf

↓

Version 1

↓

Object Lock

↓

Cannot Delete

Cannot Modify
```

---

# Important Requirement

Object Lock requires:

✅ Versioning Enabled

Without Versioning

❌ Cannot use Object Lock.

---

# Why?

Because Object Lock protects

Object Versions.

No versions

↓

Nothing to lock.

---

# Retention Modes

Object Lock has TWO modes.

---

# 1. Compliance Mode

This is the strongest protection.

Nobody can delete.

Nobody can modify.

Even

```
AWS Root User
```

cannot remove it.

---

Diagram

```
Root User

↓

Delete Object

↓

Denied
```

---

Can retention period be shortened?

❌ No.

Can lock be removed?

❌ No.

Can overwrite?

❌ No.

---

Example

Government stores

```
Passport Records
```

Retention

```
10 Years
```

Even AWS Account Owner

Cannot delete.

---

# Exam Keywords

- Strict
- Compliance
- Immutable
- Root cannot delete

Answer

✅ Compliance Mode

---

# 2. Governance Mode

Much more flexible.

Most users

↓

Cannot delete.

BUT

Some administrators

with special IAM permissions

CAN.

---

Diagram

```
Normal User

↓

Delete

↓

Denied

-----------------

Admin

↓

Special Permission

↓

Delete

↓

Allowed
```

---

Example

Company stores

```
Invoices
```

Most employees

↓

Cannot delete.

Security team

↓

Can delete if necessary.

---

Exam Keywords

- Administrator override
- IAM Permission
- Governance

Think

✅ Governance Mode

---

# Compliance vs Governance

| Feature | Compliance | Governance |
| --- | --- | --- |
| Normal users delete? | ❌ No | ❌ No |
| Admin delete? | ❌ No | ✅ Yes (with permission) |
| Root user delete? | ❌ No | ✅ Possible (with appropriate bypass permission) |
| Retention shortened? | ❌ No | ✅ Possible by authorized users |
| Highest protection? | ✅ Yes | No |

---

# Retention Period

Object Lock works for

A specific amount of time.

Example

```
Retention

5 Years
```

Timeline

```
2025

↓

Upload

↓

Locked

↓

2028

Still Locked

↓

2030

Still Locked

↓

2031

Expired

↓

Delete Allowed
```

---

Can you extend?

✅ Yes.

Can you shorten?

Compliance

❌ No.

Governance

✅ If authorized.

---

# Legal Hold

Very important.

Many students confuse this.

Legal Hold

does NOT use time.

---

Retention Period

```
Lock

5 Years
```

Automatically ends.

---

Legal Hold

```
Lock

Forever
```

Until someone removes it.

---

Diagram

```
Object

↓

Legal Hold

↓

Locked Forever

↓

Admin Removes Hold

↓

Now Delete Possible
```

---

# Example

A company is involved in a lawsuit.

Court says

```
Do not delete any emails.
```

Nobody knows how long the case will last.

Maybe

```
2 years

or

10 years
```

Instead of setting

```
Retention = 10 years
```

Use

```
Legal Hold
```

When lawsuit finishes

↓

Remove Legal Hold.

---

# Legal Hold Permission

Need IAM permission

```
s3:PutObjectLegalHold
```

Without it,

Cannot place/remove Legal Hold.

---

# Real Company Scenarios

---

## Scenario 1

Hospital

Stores

```
Patient Records
```

Law requires

Keep

7 years.

Nobody

Even admin

Should delete.

Answer

✅ Object Lock

Compliance Mode

---

## Scenario 2

Company

Stores invoices.

Security manager

Can override.

Employees

Cannot.

Answer

✅ Governance Mode

---

## Scenario 3

Government

Archives

Tax Records

Policy must never change.

Answer

✅ Glacier Vault Lock

---

## Scenario 4

Court Investigation

Evidence

Must never be deleted

Until case ends.

Unknown duration.

Answer

✅ Legal Hold

---

## Scenario 5

Backup Archive

Need

10-year retention

No changes.

Answer

✅ Compliance Mode

---

# Glacier Vault Lock vs Object Lock

| Feature | Glacier Vault Lock | S3 Object Lock |
| --- | --- | --- |
| Works On | Glacier Vault | Regular S3 Bucket |
| Protects | Vault Policy | Individual Object Versions |
| Requires Versioning | ❌ No | ✅ Yes |
| WORM | ✅ Yes | ✅ Yes |
| Retention | Policy-based | Object-based |
| Best For | Compliance policies | Individual file protection |

---

# Compliance Mode vs Governance Mode vs Legal Hold

| Feature | Compliance | Governance | Legal Hold |
| --- | --- | --- | --- |
| Uses Retention Time | ✅ Yes | ✅ Yes | ❌ No |
| Ends Automatically | ✅ Yes | ✅ Yes | ❌ No |
| Admin Override | ❌ No | ✅ Yes | Can be removed by users with `s3:PutObjectLegalHold` permission |
| Root Override | ❌ No | ✅ Possible with appropriate permissions | Depends on permissions to remove the hold |
| Best For | Regulations | Internal company control | Lawsuits / investigations |

---

# Decision Tree (Easy Exam Trick)

```
Need WORM?

        │

        ▼

Protect Glacier Vault Policy?

        │

YES ─────────► Glacier Vault Lock

        │

NO

        │

Protect Individual S3 Objects?

        │

YES

        │

Need strongest protection?

        │

YES ─────────► Compliance Mode

        │

NO

        │

Admins should override?

        │

YES ─────────► Governance Mode

        │

NO

        │

Unknown retention time?

        │

YES ─────────► Legal Hold
```

---

# SAA-C03 Cheat Sheet

| Requirement | Solution |
| --- | --- |
| Lock Glacier vault policy forever | ✅ Glacier Vault Lock |
| Lock specific S3 object version | ✅ S3 Object Lock |
| Highest protection (even root cannot delete) | ✅ Compliance Mode |
| Admin can override | ✅ Governance Mode |
| Protect object for a fixed period | ✅ Retention Period |
| Protect object indefinitely until manually removed | ✅ Legal Hold |
| Required for Object Lock | ✅ Versioning enabled |

---

# 🎯 Ultimate Memory Trick

Imagine a **bank vault** and **documents inside it**.

## Glacier Vault Lock

You **weld the vault's rulebook shut**.

- The **vault policy** (the rules about retention and deletion) can never be changed.
- It protects the **entire vault's policy**, not individual files.

**Think:** *"Lock the rules."*

---

## S3 Object Lock

You place a **tamper-proof seal on each document**.

- Each **object version** is protected.
- The document cannot be modified or deleted until its protection ends (or according to its mode).

**Think:** *"Lock the document."*

A simple memory phrase for the exam:

> **Vault Lock = Locks the policy.**
> 
> 
> **Object Lock = Locks the object version.**
>
