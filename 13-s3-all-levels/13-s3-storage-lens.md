# Amazon S3 Storage Lens (SAA-C03) — Complete Crystal Clear Notes

This is a **monitoring and analytics service** for Amazon S3.

Think of it as:

> **CloudWatch for your S3 storage usage.**
> 

It helps answer questions like:

- Which bucket is growing the fastest?
- Which buckets have old objects?
- Which buckets aren't using Lifecycle rules?
- Which buckets cost the most?
- Which buckets should move data to cheaper storage?

---

# 1. What is S3 Storage Lens?

Imagine your company has:

```
AWS Account

├── Bucket A (Photos)
├── Bucket B (Logs)
├── Bucket C (Backups)
├── Bucket D (Videos)
├── Bucket E (Reports)
```

Thousands or millions of objects exist.

Your manager asks:

> "Which bucket is wasting the most money?"
> 

Without Storage Lens:

❌ You manually inspect every bucket.

With Storage Lens:

```
AWS analyzes everything

↓

Dashboard

↓

Charts

↓

Recommendations
```

Very easy.

---

# Simple Definition

> **S3 Storage Lens analyzes your S3 storage across buckets and provides dashboards, metrics, and recommendations to optimize storage, security, and cost.**
> 

It does **not** modify your data.

It only **analyzes** and **reports**.

---

# 2. How Storage Lens Works

```
S3 Buckets

↓

Storage Lens

↓

Collect Metrics

↓

Analyze

↓

Dashboard

↓

Recommendations
```

Storage Lens continuously collects information about your S3 buckets.

---

# Real-Life Example

Imagine you own 20 warehouses.

Instead of visiting each warehouse daily,

your assistant gives you a report:

```
Warehouse A

80% Full

Warehouse B

Very Empty

Warehouse C

Lots of Old Items
```

Now you know where to save money.

That's Storage Lens.

---

# 3. Default Dashboard

When you enable Storage Lens,

AWS automatically creates a dashboard.

It shows information for:

```
Entire AWS Account
```

or even

```
Entire AWS Organization
```

if using AWS Organizations.

---

Dashboard Example

```
S3 Storage Lens Dashboard

--------------------------------

Total Storage

120 TB

Total Objects

900 Million

Current Versions

700 Million

Noncurrent Versions

200 Million

Encrypted Objects

98%

Lifecycle Enabled

80%

Delete Markers

1 Million
```

Everything is visualized with charts and graphs.

---

# 4. Storage Lens Metrics

AWS groups metrics into several categories.

---

# A. Summary Metrics

These provide an overall view.

Examples:

- Total Storage (GB/TB)
- Number of Buckets
- Number of Objects

Example:

```
Storage

50 TB

Objects

400 Million
```

---

# B. Cost Optimization Metrics

These help reduce storage costs.

Examples:

- Objects without Lifecycle rules
- Incomplete Multipart Uploads
- Old objects in Standard storage
- Objects that should move to cheaper storage

Example:

```
Bucket A

10 TB

Not accessed for 2 years

↓

Move to Glacier
```

Storage Lens highlights this opportunity.

---

# C. Data Protection Metrics

These focus on protecting your data.

Examples:

- Versioning enabled?
- Replication enabled?
- Delete markers
- Noncurrent object versions

Example:

```
Bucket A

Versioning

Disabled

⚠ Risk
```

---

# D. Access Management Metrics

These relate to permissions and ownership.

Examples:

- Publicly accessible objects
- Object ownership
- Bucket policies

Example:

```
Bucket

Public

⚠ Security Warning
```

---

# E. Event Metrics (Advanced)

These show how objects are used.

Examples:

- Upload activity
- Download activity
- Delete requests

Useful for understanding application behavior.

---

# F. Activity Metrics (Advanced)

Shows request activity like:

- GET requests
- PUT requests
- DELETE requests

Example:

```
Yesterday

GET Requests

5 Million

PUT Requests

100,000
```

Useful for performance and usage analysis.

---

# 5. Important Metrics for the Exam

Think of these as the "must-know" metrics.

| Metric | Meaning |
| --- | --- |
| Storage Bytes | Total storage used |
| Object Count | Number of objects |
| Current Versions | Latest versions of objects |
| Noncurrent Versions | Older versions (with versioning) |
| Delete Markers | Logical deletes created by versioning |
| Encrypted Objects | Objects using encryption |
| Lifecycle Rules | Objects managed by lifecycle |
| Multipart Uploads | Incomplete uploads consuming storage |

---

# Example

Bucket contains:

```
Photos

↓

10 Million Objects

↓

Storage Lens

↓

Storage

5 TB

Encrypted

100%

Lifecycle

No

Recommendation

Move old objects to Glacier
```

---

# 6. Free vs Paid (Advanced Tier)

Storage Lens has two levels.

---

## Free Tier

Included with every AWS account.

Provides:

- Dashboard
- Summary metrics
- Cost optimization metrics
- Data protection metrics
- Basic recommendations

Good enough for many exam questions.

---

## Paid Tier (Advanced Metrics)

Provides everything in the free tier plus:

### Activity Metrics

Example:

```
GET Requests

PUT Requests

DELETE Requests
```

---

### Advanced Recommendations

More detailed optimization suggestions.

---

### CloudWatch Publishing

Advanced metrics can be published to:

Amazon CloudWatch

Then you can:

- Create alarms
- Build dashboards
- Monitor trends

---

### Prefix-Level Metrics

Instead of only bucket-level insights,

you can analyze specific prefixes.

Example:

```
Bucket

photos/

videos/

logs/
```

See which folder uses the most storage or receives the most requests.

---

### Frequent Updates

Advanced metrics are updated more frequently than the free metrics.

---

# Free vs Paid Comparison

| Feature | Free | Paid (Advanced) |
| --- | --- | --- |
| Dashboard | ✅ | ✅ |
| Storage Metrics | ✅ | ✅ |
| Object Count | ✅ | ✅ |
| Cost Optimization | ✅ | ✅ |
| Data Protection | ✅ | ✅ |
| Activity Metrics (GET/PUT/Delete) | ❌ | ✅ |
| Prefix-Level Metrics | ❌ | ✅ |
| CloudWatch Integration | ❌ | ✅ |
| Advanced Recommendations | ❌ | ✅ |

---

# 7. How Storage Lens Helps

Suppose your bucket contains:

```
100 TB
```

Storage Lens finds:

```
70 TB

Not accessed

for

2 years
```

Recommendation:

```
Lifecycle Rule

↓

Glacier Deep Archive
```

Company saves thousands of dollars.

---

Another example:

Storage Lens reports:

```
Bucket

Versioning

Disabled
```

Recommendation:

Enable Versioning for better data protection.

---

# 8. Storage Lens vs S3 Analytics

Students often confuse these two.

| S3 Storage Lens | S3 Storage Class Analysis |
| --- | --- |
| Monitors entire S3 environment | Analyzes storage class transitions |
| Security metrics | ❌ |
| Cost metrics | ✅ |
| Lifecycle insights | ✅ |
| Object access analysis | Limited |
| Dashboard | ✅ |

**Remember:**

- **Storage Lens** = Overall visibility into S3 usage, cost, and protection.
- **Storage Class Analysis** = Helps decide **when to transition objects** between storage classes.

---

# Architecture Diagram

```
          S3 Buckets
      ┌────────┴────────┐
      │                 │
   Photos            Backups
      │                 │
      └────────┬────────┘
               │
               ▼
        S3 Storage Lens
               │
      Collect Metrics
               │
               ▼
         Dashboard
               │
     Recommendations
```

---

# Real Company Example

A company has:

```
20 Buckets
500 Million Files
```

Storage Lens dashboard shows:

```
Bucket A

40 TB

No Lifecycle

⚠

Bucket B

Versioning Disabled

⚠

Bucket C

98% Encrypted

✅
```

The storage administrator can immediately identify where improvements are needed.

---

# SAA-C03 Exam Cheat Sheet

| Topic | Key Point |
| --- | --- |
| Purpose | Analyze and optimize S3 storage |
| Scope | Account-wide or organization-wide |
| Default Dashboard | Yes |
| Shows | Storage, object count, lifecycle, encryption, versioning, delete markers |
| Free Tier | Basic metrics and recommendations |
| Paid Tier | Activity metrics, prefix-level metrics, CloudWatch integration |
| Modifies Objects? | ❌ No, it only analyzes and reports |

---

# 🎯 Ultimate Memory Trick

Imagine **Storage Lens** as a **doctor performing a full health check on your S3 environment**.

It tells you:

- 💰 Where you're wasting money (cost optimization)
- 🔒 Where your data isn't protected (versioning, encryption)
- 📦 How much storage you're using
- 📈 Which buckets are growing
- ⚡ (Advanced tier) How your buckets are being accessed (GET, PUT, DELETE activity)

It **doesn't fix anything automatically**—it gives you the information needed to make better decisions.

---

# 30-Second SAA-C03 Summary

- **Amazon S3 Storage Lens** is an analytics service that provides visibility into S3 usage across buckets, accounts, or organizations.
- The **default dashboard** displays storage usage, object counts, encryption status, lifecycle adoption, versioning, delete markers, and more.
- **Free metrics** focus on storage, cost optimization, and data protection.
- **Advanced (paid) metrics** add request activity (GET/PUT/DELETE), prefix-level analysis, CloudWatch publishing, and richer recommendations.
- It is used to **optimize cost, improve security, and monitor storage trends**, not to move or modify objects automatically.
