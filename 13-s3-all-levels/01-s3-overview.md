# Amazon S3 — SAA-C03 Clear Revision Note

## 1. What is Amazon S3?

**Amazon S3 (Simple Storage Service)** is AWS's highly scalable **object storage service**.

### Simple meaning

> **S3 = Store files/objects in AWS using buckets.**
> 

You can store things such as:

- Images
- Videos
- PDFs
- Backups
- Logs
- Application files
- Data-lake data
- Static website files
- Database backups

Think of S3 as a **massive cloud storage system** where you put files into **buckets**.

---

# 2. What problem does S3 solve?

Suppose your application generates thousands of images every day.

Instead of storing them directly on an EC2 instance:

```
Users
   ↓
Application
   ↓
EC2
   ↓
Local disk ❌
```

You can store them in S3:

```
Users
   ↓
Application
   ↓
EC2
   │
   └──────────→ S3 Bucket
                    │
              ┌─────┼─────┐
              │     │     │
            img1  img2  img3
```

Now storage is:

- Highly scalable
- Durable
- Independent from EC2
- Accessible by many AWS services
- Suitable for backups and archives

---

# 3. Major S3 Use Cases

| Use case | Example |
| --- | --- |
| **Backup & Storage** | Store application backups |
| **Disaster Recovery** | Store backups that can be restored after failure |
| **Archive** | Store years of historical records |
| **Data Lake** | Store raw data for analytics |
| **Static Website** | Host HTML/CSS/JS files |
| **Media Hosting** | Store images, videos, audio |
| **Software Delivery** | Store application packages/installers |
| **Hybrid Cloud** | On-premises applications store files in S3 |
| **Big Data Analytics** | Store large datasets for Athena/EMR/Redshift Spectrum |
| **Application Integration** | AWS services use S3 as an event/data integration point |

---

# 4. S3 Core Structure

S3 uses:

```
AWS Account
     │
     └── Bucket
          │
          ├── Object
          ├── Object
          ├── Object
          └── Object
```

### Important terms

| Term | Meaning |
| --- | --- |
| **S3** | Object storage service |
| **Bucket** | Container for objects |
| **Object** | Actual file/data stored in S3 |
| **Key** | Full name/path of an object |
| **Prefix** | Beginning portion of an object's key |
| **Metadata** | Information describing an object |
| **Tags** | Key-value labels attached to objects |
| **Version ID** | Identifies a specific version of an object when versioning is enabled |

---

# 5. S3 Bucket

A **bucket** is a container where objects are stored.

Example:

```
Bucket:
my-company-production-data

Objects:
    invoices/2026/january/invoice001.pdf
    invoices/2026/january/invoice002.pdf
    images/logo.png
    backups/database.sql
```

### Important SAA-C03 facts

- Bucket names must be **globally unique**.
- Bucket names are unique across AWS.
- A bucket is created in a specific **AWS Region**.
- S3 is often described as a global service, but **the bucket itself belongs to a Region**.

### Memory trick

> **Bucket = Container**
> 
> 
> **Object = File**
> 

---

# 6. S3 Bucket Naming

Remember the major exam rules:

- Generally **3–63 characters**
- Lowercase letters
- Numbers
- Hyphens
- No uppercase letters
- No underscores
- Must not look like an IP address
- Must start with a lowercase letter or number
- Must be globally unique

### Example

✅

```
my-company-backups-2026
```

❌

```
My_Company_Backups
```

---

# 7. S3 Objects

An **object is the actual data/file stored inside an S3 bucket.**

Example:

```
Bucket:
company-data

Object:
reports/2026/january/report.pdf
```

The object consists of things such as:

```
Object
 ├── Data
 ├── Key
 ├── Metadata
 ├── Tags
 └── Version ID (if versioning enabled)
```

---

# 8. S3 Object Key

This is **VERY important for SAA-C03.**

S3 doesn't really have traditional directories/folders.

For example:

```
s3://company-data/reports/2026/january/report.pdf
```

The **full key** is:

```
reports/2026/january/report.pdf
```

### Prefix + object name

```
reports/2026/january/
          ↓
       Prefix

report.pdf
    ↓
Object name
```

Therefore:

> **Key = Prefix + Object name**
> 

---

## S3 doesn't actually have folders

The S3 console makes it look like:

```
company-data
 └── reports
      └── 2026
           └── january
                └── report.pdf
```

But internally, S3 is essentially storing a key:

```
reports/2026/january/report.pdf
```

The `/` is simply part of the object's key.

### Exam takeaway

> **S3 = flat object storage, not a traditional hierarchical filesystem.**
> 

---

# 9. S3 Object Metadata

Metadata is information about an object.

Example:

```
Object:
photo.jpg

Metadata:
Content-Type = image/jpeg
Size = 5 MB
```

There are broadly:

- System metadata
- User-defined metadata

Metadata helps applications understand/manage objects.

---

# 10. S3 Object Tags

Objects can have **tags** as key-value pairs.

Example:

```
Environment = Production
Department = Finance
DataType = Sensitive
```

Tags can be useful for:

- Lifecycle management
- Organization
- Access-control-related policies
- Cost/resource management

### Memory trick

> **Metadata = Information about the object**
> 
> 
> **Tags = Labels attached to the object**
> 

---

# 11. S3 Version ID

If **S3 Versioning** is enabled, multiple versions of the same object can exist.

Example:

```
report.pdf
   │
   ├── Version A
   ├── Version B
   └── Version C
```

If someone accidentally overwrites the file:

```
Old report
     ↓
New report
```

Versioning can allow you to recover the previous version.

### Exam connection

If the question says:

> **Protect against accidental deletion or overwriting**
> 

Think:

**S3 Versioning**

And if stronger protection against deletion is required, consider:

**Versioning + MFA Delete / Object Lock**, depending on the exact scenario.

---

# 12. S3 Security

S3 security can be thought of in two major categories:

```
              S3 Security
                   │
          ┌────────┴────────┐
          │                 │
     User-Based        Resource-Based
          │                 │
     IAM Policies      Bucket Policies
                       Object ACLs
                       Bucket ACLs
```

---

# 13. IAM Policies — User-Based Access

IAM policies control **what an IAM principal is allowed to do**.

Example:

```
Developer IAM User
       ↓
IAM Policy
       ↓
Allow:
s3:GetObject
       ↓
S3 Bucket
```

Example requirement:

> Developer should be able to download objects from a specific S3 bucket.
> 

You can grant the required S3 permissions using an IAM policy.

---

# 14. Bucket Policies

A **Bucket Policy** is a **resource-based policy** attached to an S3 bucket.

It can control who can access the bucket and what they can do.

Example:

```
Company Account
      │
      ▼
S3 Bucket
      ▲
      │
Bucket Policy
      │
      ▼
Another AWS Account
```

### Important use case

**Cross-account access**

For example:

> Account A owns an S3 bucket, but Account B needs permission to access it.
> 

A bucket policy can be used to grant access to the other account.

---

# 15. Object ACL & Bucket ACL

S3 also has **Access Control Lists (ACLs)**.

They can provide more granular access control.

However:

> **S3 ACLs are generally not the first choice for modern S3 access control.**
> 

S3 Object Ownership can be configured to disable ACLs in many scenarios.

### Exam approach

If AWS asks for straightforward S3 access control:

**Think IAM Policy / Bucket Policy first.**

---

# 16. IAM Policy vs Bucket Policy

| Feature | IAM Policy | Bucket Policy |
| --- | --- | --- |
| Type | Identity-based | Resource-based |
| Attached to | IAM identity | S3 bucket |
| Controls access | ✅ | ✅ |
| Cross-account access | Possible with appropriate setup | ⭐ Common use case |
| Example | Allow user to GetObject | Allow another account to access bucket |

### Memory

> **IAM Policy → "What can this user do?"**
> 

> **Bucket Policy → "Who can access this bucket?"**
> 

---

# 17. Important S3 Permission Logic

A principal can access an S3 resource when permission is appropriately granted **and there is no explicit Deny**.

The most important exam rule:

> **Explicit DENY overrides ALLOW.**
> 

Think:

```
ALLOW
  +
No explicit DENY
  =
Access can be allowed
```

But:

```
ALLOW
  +
EXPLICIT DENY
  =
❌ DENIED
```

### Exam keyword

If you see:

> "Even though the IAM policy allows access..."
> 

Look for an **explicit Deny** somewhere.

---

# 18. S3 Encryption

S3 supports encryption of objects at rest.

Think:

```
Application
    ↓
S3
    ↓
Encrypted Object 🔐
```

Common S3 encryption options include:

| Option | Simple meaning |
| --- | --- |
| **SSE-S3** | S3 manages encryption keys |
| **SSE-KMS** | AWS KMS manages encryption keys |
| **DSSE-KMS** | Dual-layer server-side encryption using KMS |
| **SSE-C** | Customer provides encryption key |
| **Client-side encryption** | Application encrypts data before sending it to S3 |

For SAA-C03, the major distinction to recognize is:

> **SSE-S3 → S3-managed keys**
> 

> **SSE-KMS → KMS-managed keys + KMS capabilities**
> 

---

# 19. Simple S3 Architecture Example

Imagine an e-commerce company.

Users upload product images.

```
                    Internet
                       │
                       ▼
                  Application
                       │
                       ▼
                     S3
               Product Images
                       │
        ┌──────────────┼──────────────┐
        │              │              │
     image1.jpg    image2.jpg    image3.jpg
```

Instead of putting images on EC2's local disk, S3 provides scalable object storage.

---

# 20. S3 Backup Scenario

A company wants to store database backups.

```
RDS
 │
 │ Backup/export
 ▼
S3 Bucket
 │
 ├── backup-01
 ├── backup-02
 └── backup-03
```

If the company later needs disaster recovery:

```
S3
 ↓
Restore
 ↓
Database
```

---

# 21. S3 Data Lake Scenario

A company receives millions of records every day.

```
Applications
     │
     ▼
    S3
 ┌───────────────┐
 │ Raw data      │
 │ Logs          │
 │ CSV           │
 │ JSON          │
 │ Parquet       │
 └───────────────┘
     │
     ├── Athena
     ├── EMR
     ├── Glue
     └── Redshift
```

S3 becomes the central storage layer for analytics.

### Exam clue

> **"Centralized data lake" → S3**
> 

---

# 22. S3 Static Website Scenario

Suppose you have:

```
index.html
style.css
app.js
logo.png
```

These can be stored in S3 and used to host a static website.

Common architecture:

```
User
  ↓
CloudFront
  ↓
S3
  ↓
Static Website Files
```

For modern architectures, **CloudFront + S3** is a common pattern for secure, low-latency content delivery.

---

# 23. S3 — What You Should Remember for SAA-C03

### Core

> **S3 = highly scalable object storage**
> 

### Structure

```
S3
 ↓
Bucket
 ↓
Object
 ↓
Key
```

### Important concepts

| Concept | Remember |
| --- | --- |
| Bucket | Container for objects |
| Bucket Region | Bucket is created in a specific Region |
| Bucket name | Globally unique |
| Object | Actual stored file/data |
| Key | Full object name/path |
| Prefix | Beginning of key |
| Folder | Not a true filesystem directory |
| Metadata | Information about object |
| Tags | Key-value labels |
| Versioning | Protects against overwrite/deletion |
| IAM Policy | Identity-based access |
| Bucket Policy | Resource-based access |
| ACL | Legacy/additional access-control mechanism |
| Encryption | Protects data at rest |

---

## 🧠 Final SAA-C03 Memory Map

```
                    AMAZON S3
                       │
       ┌───────────────┼────────────────┐
       │               │                │
     Storage         Security        Management
       │               │                │
   Bucket           IAM Policy      Versioning
   Object           Bucket Policy   Lifecycle
   Key              ACL             Replication
   Prefix           Encryption      Storage Classes
   Metadata
   Tags
```

### 🔥 Exam keywords

**"Object storage"** → S3

**"Highly scalable storage"** → S3

**"Backup / archive"** → S3

**"Data lake"** → S3

**"Static website files"** → S3

**"Globally unique bucket name"** → S3 bucket

**"Full path/name of object"** → S3 Object Key

**"Protect against accidental overwrite/deletion"** → S3 Versioning

**"Cross-account bucket access"** → S3 Bucket Policy

**"User-specific S3 permissions"** → IAM Policy

**"AWS compliance documents"** → Artifact, **not S3**

**"Object storage, not filesystem"** → S3

> **Best mental model:**
> 
> 
> **S3 = Bucket → Objects → Keys + Security + Versioning + Encryption.**
>
