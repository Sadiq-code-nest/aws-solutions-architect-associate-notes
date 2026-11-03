# Amazon S3 Versioning — SAA-C03

## 1. What is S3 Versioning?

**S3 Versioning** keeps multiple versions of the same object inside an S3 bucket.

Normally, if you upload:

```
website.zip
```

and later upload another `website.zip`, the old object is overwritten.

With **Versioning enabled**, S3 keeps both versions.

```
S3 Bucket
│
└── website.zip
      ├── Version 1
      ├── Version 2
      └── Version 3
```

So you can **restore an older version** instead of permanently losing it.

---

## 2. Why do we need Versioning?

The biggest SAA-C03 use cases are:

### Accidental overwrite

```
Version 1
   ↓
Important report.pdf

Developer uploads wrong report.pdf
   ↓
Version 2
```

Without versioning:

❌ Original may be lost.

With versioning:

✅ Version 1 remains available.

---

### Accidental deletion

```
S3
│
├── report.pdf
│    ├── Version 1
│    └── Version 2
│
└── User deletes report.pdf
```

With versioning, S3 doesn't simply destroy the previous version.

A **delete marker** is created.

```
report.pdf
   │
   ├── Delete Marker ← current version
   ├── Version 2
   └── Version 1
```

You can remove the delete marker to make the previous object version accessible again.

---

# 3. Versioning is enabled at the BUCKET level

This is important:

> **Versioning is a bucket-level setting.**
> 

You don't enable versioning individually for one object.

```
S3 Bucket
   │
   ├── Versioning: ENABLED
   │
   ├── file1.txt → versions
   ├── file2.jpg → versions
   └── backup.zip → versions
```

---

# 4. Same Key → New Version

Suppose you upload:

```
s3://my-bucket/config.json
```

### First upload

```
config.json
Version ID: abc123
```

### Second upload using the SAME key

```
config.json
Version ID: xyz456
```

### Third upload

```
config.json
Version ID: pqr789
```

S3 now has:

```
config.json
│
├── Version pqr789 ← latest
├── Version xyz456
└── Version abc123
```

### 🧠 Memory Trick

> **Same Key + New Upload = New Version**
> 

---

# 5. Real-World Example

Imagine a production application stores:

```
s3://company-config/app-config.json
```

The file initially contains:

```
database = production-db-1
```

Someone accidentally changes it to:

```
database = test-db
```

With S3 Versioning:

```
Version 1 → production-db-1
Version 2 → test-db
```

You can restore **Version 1**.

### Without Versioning

The previous content could be lost through an overwrite.

---

# 6. Versioning + Accidental Delete

This is a VERY important SAA-C03 concept.

Suppose:

```
report.pdf
├── Version 1
└── Version 2 ← current
```

User deletes:

```
DELETE report.pdf
```

S3 creates:

```
Delete Marker
Version 2
Version 1
```

The object appears deleted to normal requests, but the previous versions still exist.

Therefore:

> **Versioning protects against accidental deletion and overwriting.**
> 

---

# 7. What is a Delete Marker?

A **delete marker** is essentially a special version that indicates the object has been deleted.

Example:

```
Before:

report.pdf
 ├── V2
 └── V1

After DELETE:

report.pdf
 ├── Delete Marker ← current
 ├── V2
 └── V1
```

If you remove the delete marker:

```
report.pdf
 ├── V2 ← becomes current
 └── V1
```

The object becomes accessible again.

---

# 8. What happens if versioning is enabled AFTER files already exist?

Suppose:

```
Day 1
Versioning = OFF

photo.jpg uploaded
```

Then:

```
Day 2
Versioning = ON
```

The existing object doesn't suddenly receive a normal version ID.

Its version ID is:

> **`null`**
> 

So:

```
photo.jpg
Version ID = null
```

Future versions will receive actual version IDs.

### 🧠 Exam clue

> **Object existed before versioning was enabled → Version ID = `null`**
> 

---

# 9. What happens if you suspend versioning?

You can s**uspend** versioning.

But:

> **Suspending Versioning does NOT delete existing versions.**
> 

Example:

```
Versioning enabled

V1
V2
V3
```

Suspend versioning:

```
Versioning suspended

V1  ← still exists
V2  ← still exists
V3  ← still exists
```

Existing versions remain.

---

# 10. Versioning States

Think of three situations:

| State | Meaning |
| --- | --- |
| **Unversioned** | Versioning hasn't been enabled |
| **Enabled** | New versions are created |
| **Suspended** | New versioning behavior stops, but old versions remain |

---

# 11. Versioning ≠ Backup

Very important distinction.

**Versioning is not the same as a traditional backup.**

Versioning protects against:

- Accidental overwrite
- Accidental deletion
- Need to roll back to previous versions

But versions still live in the same S3 bucket/environment.

For stronger protection, combine it with things like:

```
S3 Versioning
      +
S3 Replication
      +
S3 Object Lock
      +
S3 Lifecycle
```

depending on the requirement.

---

# 12. Versioning + Lifecycle

Versions can consume storage.

Imagine:

```
file.zip
├── V1 → 10 GB
├── V2 → 10 GB
├── V3 → 10 GB
├── V4 → 10 GB
└── V5 → 10 GB
```

You may have **50 GB** of stored versions.

You can use **S3 Lifecycle rules** to manage older versions.

For example:

```
Current version
       ↓
Keep normally

Noncurrent versions
       ↓
After 30 days → transition
       ↓
After 90 days → delete
```

This can reduce storage costs.

---

# 13. Versioning + MFA Delete

For additional protection against accidental or malicious deletion, S3 supports **MFA Delete**.

Conceptually:

```
Delete version
      ↓
Requires MFA
      ↓
Extra protection
```

### Exam clue

> **“Require MFA before permanently deleting an S3 object version.”**
> 

Think:

**S3 Versioning + MFA Delete**

### 🔐 S3 MFA Delete — Exam Note

**MFA Delete** adds an extra security layer to an **S3 bucket with versioning enabled**. It requires **MFA authentication** for sensitive versioning/deletion operations, helping protect objects from **permanent accidental or malicious deletion**.

**Why is it needed?**

- 🛡️ Protects **object versions from permanent deletion**
- 🛡️ Helps prevent someone from **disabling S3 Versioning**
- 🔄 Works together with **S3 Versioning** to protect against accidental deletion/overwrite
- Especially useful for **critical production data**

**How to enable it?**

- ❌ **AWS Management Console:** Cannot enable MFA Delete
- ✅ **AWS CLI:** Yes
- ✅ **S3 API:** Yes
- ⚠️ **Root account credentials are required to enable/disable MFA Delete**

**Basic process:**

```
1. Enable S3 Versioning
          ↓
2. Configure MFA Delete
          ↓
3. Provide MFA device ARN + MFA code
          ↓
4. MFA Delete becomes Enabled
          ↓
5. Sensitive delete/versioning operations
   require MFA
```

**CLI example:**

```bash
aws s3api put-bucket-versioning \
  --bucket YOUR-BUCKET \
  --versioning-configuration Status=Enabled,MFADelete=Enabled \
  --mfa "MFA_DEVICE_ARN MFA_CODE"
```

Example:

```
--mfa "arn:aws:iam::123456789012:mfa/root-account 123456"
```

### 🧠 SAA-C03 Shortcut

> **Versioning = keeps old versions**
> 
> 
> **MFA Delete = protects permanent deletion/versioning changes**
> 
> **MFA Delete = CLI/API, NOT Console**
> 
> **Enable MFA Delete = Root account required**
> 

**Exam scenario:**

If the question asks how to protect S3 data from **accidental deletion AND overwriting**, think:

**✅ Versioning + MFA Delete**

# 14. Versioning + Replication

Versioning is also important for **S3 Replication**.

For S3 replication scenarios, remember:

> **Versioning must be enabled on the relevant source and destination buckets.**
> 

Example:

```
Account A                         Account B

S3 Bucket                        S3 Bucket
Versioning ON                    Versioning ON
     │                                ▲
     │        Replication             │
     └───────────────────────────────►
```

This can be used for:

- Disaster recovery
- Cross-account replication
- Cross-region replication

---

# 🔥 SAA-C03 Scenario Examples

### Scenario 1 — Accidental overwrite

> A developer accidentally overwrites a production configuration file in S3. The company needs the ability to restore the previous file.
> 

✅ **S3 Versioning**

---

### Scenario 2 — Accidental deletion

> An employee accidentally deletes important S3 objects and the company needs to restore them.
> 

✅ **S3 Versioning**

---

### Scenario 3 — Rollback

> An application deployment uploads a bad version of a file. The administrator needs to quickly revert to the previous version.
> 

✅ **S3 Versioning**

---

### Scenario 4 — Existing object before Versioning

> A file was uploaded before S3 Versioning was enabled. What version ID does the existing object have?
> 

✅ **`null`**

---

### Scenario 5 — Suspend Versioning

> Versioning is suspended. What happens to existing object versions?
> 

✅ **Existing versions remain.**

---

# 🧠 S3 Versioning — Quick Revision Table

| Exam concept | Remember |
| --- | --- |
| Enabled at | **Bucket level** |
| Same key uploaded again | **New version** |
| Protect overwrite | ✅ |
| Protect accidental delete | ✅ |
| Rollback | ✅ |
| Delete with Versioning | **Delete marker** |
| Existing object before Versioning | Version ID **`null`** |
| Suspend Versioning | **Old versions remain** |
| Storage cost | Multiple versions consume storage |
| Manage old versions | **Lifecycle Rules** |
| Extra deletion protection | **MFA Delete** |
| Replication | Versioning required |
| Versioning = backup? | ❌ Not exactly |

### 🔑 Ultimate memory trick

> **S3 Versioning = “Don't lose the past.”**
> 

**Overwrite → Previous version stays**

**Delete → Delete marker**

**Rollback → Restore old version**

**Old object before versioning → `null`**

**Suspend → Old versions stay**
