# Amazon S3 (AWS SAA-C03) – Exam Notes with Easy Explanations

## 1. S3 is an Object Storage (Key-Value Store)

S3 stores **objects (files)** inside **buckets**.

Each object has:

- **Key** = File name/path (unique identifier)
- **Value** = Actual file content

Example:

```
Bucket: my-photos

Key: vacation/beach.jpg
Value: Image File
```

**📝 Exam Tip:**

S3 = **Object Storage**, **not a file system** and **not a database**.

---

## 2. Best for Large Objects

S3 is designed for:

- Images
- Videos
- PDFs
- Backups
- Documents
- Log files

Not ideal for:

- Millions of tiny files with very frequent updates.

**📝 Exam Tip:**

Large files → **S3**

---

## 3. Serverless & Infinite Scaling

AWS manages everything:

- No servers
- No storage management
- Automatic scaling

Maximum object size:

- **5 TB**

For files larger than **100 MB**, use **Multipart Upload** (recommended).

**📝 Exam Tip:**

**Max object size = 5 TB**

---

## 4. Versioning

Every time a file is overwritten,

S3 keeps the old version.

Example:

```
Resume.pdf (v1)

↓

Upload New Version

↓

Resume.pdf (v2)
```

Benefits:

- Recover deleted files
- Restore previous versions
- Protection from accidental overwrites

**📝 Exam Tip:**

Versioning protects against accidental deletion and overwrite.

---

## 5. Storage Classes (Tiers)

### S3 Standard

- Frequently accessed data
- Highest availability

Example:

Website images

---

### S3 Standard-IA (Infrequent Access)

- Rarely accessed
- Lower storage cost
- Retrieval fee

Example:

Monthly reports

---

### S3 One Zone-IA

- Stored in one AZ only
- Cheaper
- No Multi-AZ durability

Example:

Re-creatable backup data

---

### S3 Intelligent-Tiering

AWS automatically moves objects between tiers based on usage.

Best for:

Unknown access patterns

---

### S3 Glacier Instant Retrieval

Archive data that still needs millisecond access.

---

### S3 Glacier Flexible Retrieval

Long-term archive.

Retrieval:

Minutes to hours.

---

### S3 Glacier Deep Archive

Cheapest storage.

Retrieval:

Hours (up to 12+ hours).

Best for:

Compliance and long-term backups.

---

## 6. Lifecycle Policies

Automatically move or delete objects.

Example:

```
30 Days

↓

Move to Standard-IA

↓

90 Days

↓

Move to Glacier

↓

365 Days

↓

Delete
```

**📝 Exam Tip:**

Lifecycle = Automatic cost optimization.

---

## 7. Security

### IAM Policies

Control user permissions.

---

### Bucket Policies

Grant access to:

- Public users
- Other AWS accounts
- Services

---

### ACL (Access Control List)

Legacy permission system.

AWS recommends using IAM and Bucket Policies instead.

---

### Access Points

Create different access rules for different applications or teams.

---

### Object Lambda

Modify an object before it is returned.

Example:

Resize an image before download.

---

### CORS

Allow websites from another domain to access your S3 bucket.

Example:

Frontend → API → S3

---

### Object Lock / Vault Lock

Prevent objects from being deleted or modified.

Useful for:

- Compliance
- Financial records
- Legal archives

---

## 8. Encryption

### SSE-S3

AWS manages encryption keys.

Simplest option.

---

### SSE-KMS

Uses AWS KMS.

Supports:

- Audit logs
- Fine-grained permissions

---

### SSE-C

Customer provides encryption key.

AWS never stores the key.

---

### Client-Side Encryption

Encrypt the file before uploading.

AWS never sees the unencrypted data.

---

### TLS (HTTPS)

Protects data while traveling across the network.

**📝 Exam Tip:**

- **At Rest** → SSE-S3 / SSE-KMS / SSE-C
- **In Transit** → HTTPS (TLS)

---

## 9. Replication

Automatically copy objects.

### CRR (Cross-Region Replication)

Copies to another AWS Region.

Use:

Disaster Recovery

---

### SRR (Same-Region Replication)

Copies within the same Region.

Use:

Compliance

Log aggregation

**📝 Exam Tip:**

Replication requires **Versioning enabled**.

---

## 10. MFA Delete

Deleting important files requires:

- Password
- MFA code

Prevents accidental deletion.

---

## 11. Access Logs

Record:

- Who accessed files
- When
- From where

Useful for:

Auditing and troubleshooting.

---

## 12. S3 Batch Operations

Perform bulk actions on millions of objects.

Examples:

- Copy
- Delete
- Encrypt
- Tag

---

## 13. S3 Inventory

Generate reports listing objects in a bucket.

Useful for:

- Auditing
- Compliance
- Large bucket management

---

## 14. Performance Features

### Multipart Upload

Upload large files in pieces.

Benefits:

- Faster
- Resume failed uploads

Recommended for files >100 MB (required for >5 GB).

---

### S3 Transfer Acceleration

Uploads through the nearest CloudFront Edge Location.

Best for:

Users uploading from different countries.

---

### S3 Select

Retrieve only part of an object instead of downloading the whole file.

Example:

Read one column from a CSV.

Saves bandwidth and improves performance.

---

## 15. Automation

### S3 Event Notifications

Trigger actions when objects are:

- Uploaded
- Deleted
- Restored

Can send events to:

- SNS
- SQS
- Lambda

---

### EventBridge

Provides advanced event routing and filtering.

Useful for:

Complex event-driven architectures.

---

## 16. Common Use Cases

Use Amazon S3 for:

- Static website hosting
- Image and video storage
- Application assets
- Backups
- Log storage
- Data lakes
- Big file storage
- Disaster recovery
- Software downloads

---

# AWS SAA-C03 Cheat Sheet

| Feature | Remember |
| --- | --- |
| **Storage Type** | Object Storage (Key-Value) |
| **Max Object Size** | **5 TB** |
| **Serverless** | Yes |
| **Versioning** | Recover old versions |
| **Storage Classes** | Standard, IA, Intelligent-Tiering, Glacier |
| **Lifecycle Policy** | Auto move/delete objects |
| **Replication** | CRR & SRR (requires Versioning) |
| **Encryption** | SSE-S3, SSE-KMS, SSE-C, Client-Side |
| **Security** | IAM, Bucket Policies, ACL, Access Points |
| **Performance** | Multipart Upload, Transfer Acceleration, S3 Select |
| **Automation** | Event Notifications, EventBridge |
| **Batch Processing** | S3 Batch Operations |
| **Reporting** | S3 Inventory |

---

# 30-Second Exam Memory Trick

- 🗂️ **S3 = Object Storage**
- 📦 **Max Object = 5 TB**
- ♾️ **Serverless & Infinite Scaling**
- 📄 **Versioning = Recover old files**
- 💰 **Lifecycle = Save storage cost**
- 🌍 **CRR = Cross-Region Replication**
- 🏠 **SRR = Same-Region Replication**
- 🔐 **SSE-KMS = KMS-managed encryption**
- 🚀 **Multipart Upload = Large files**
- 🌎 **Transfer Acceleration = Faster global uploads**
- ⚡ **S3 Select = Read part of a file**
- 🔔 **Event Notifications = Trigger SNS, SQS, Lambda**
- 🌐 **Static Website Hosting = Common S3 use case**
