# Amazon S3 Lifecycle Policy (SAA-C03) – Complete Crystal-Clear Notes

This is one of the highest-scoring SAA-C03 topics because AWS asks many scenario-based questions.

---

# 1. What is an S3 Lifecycle Policy?

Think of an S3 lifecycle policy as an **automatic storage manager**.

Instead of you manually moving or deleting files, AWS does it automatically based on rules you define.

Example:

```
Today → Upload file
30 days later → Move to cheaper storage
180 days later → Move to Glacier
365 days later → Delete
```

AWS performs these actions automatically.

---

# Real-Life Analogy

Imagine your home.

Today you buy a document.

```
Desk Drawer
```

You use it every day.

After 6 months:

```
Move it to a cabinet.
```

After 5 years:

```
Move it to the basement.
```

After 10 years:

```
Throw it away.
```

You don't manually remember these dates.

You tell your assistant once.

The assistant automatically performs them.

👉 **S3 Lifecycle = That assistant.**

---

# 2. Why Do We Need Lifecycle Policies?

Because storage costs money.

Example:

You upload:

```
Vacation.jpg
```

Day 1:

User opens it frequently.

So it belongs in:

```
S3 Standard
```

After one year:

Nobody opens it.

Keeping it in Standard is expensive.

Instead:

```
Move it to Glacier
```

Much cheaper.

---

# 3. How Lifecycle Works

Imagine a bucket:

```
my-photos
```

Lifecycle Rule:

```
After 30 days
↓

Standard IA

After 180 days
↓

Glacier Flexible Retrieval

After 365 days
↓

Delete
```

---

Visual

```
Upload
   │
   ▼
S3 Standard
   │
30 Days
   ▼
Standard-IA
   │
180 Days
   ▼
Glacier
   │
365 Days
   ▼
Deleted
```

AWS automatically checks object age and performs the transitions.

---

# Two Main Lifecycle Actions

Lifecycle rules perform only two types of actions.

## 1. Transition

Move an object to another storage class.

Example:

```
Standard
    ↓
Standard-IA
    ↓
Glacier
    ↓
Deep Archive
```

The object still exists.

Only its storage class changes.

---

## 2. Expiration

Delete the object permanently.

Example

```
After 365 days

↓

Delete Object
```

---

# Lifecycle Does NOT Copy Objects

Many students confuse this.

Lifecycle:

```
Bucket A

↓

Move to Glacier
```

Same bucket.

Same object.

Different storage class.

NOT

```
Bucket A

↓

Bucket B
```

That is Replication.

---

# Scenario 1 (Profile Pictures)

Let's understand AWS's example.

---

Suppose users upload:

```
profile.jpg
```

to S3.

Your application creates:

```
thumbnail.jpg
```

---

Architecture

```
User
   │
Uploads
   ▼
profile.jpg
   │
Application
   ▼
thumbnail.jpg
```

Now we have:

```
Source Image

Thumbnail
```

---

Question says:

> Thumbnails can be recreated anytime.
> 

Meaning:

If deleted,

Application simply generates them again.

No problem.

---

But source images:

```
Original Photos
```

cannot be lost.

---

Requirement 1

Original image:

Need immediate access

for

```
60 Days
```

After that,

waiting

```
6 Hours
```

is acceptable.

---

Which storage class allows hours of retrieval?

Answer:

```
Glacier Flexible Retrieval
```

Lifecycle:

```
Day 0

↓

Standard

↓

Day 60

↓

Glacier Flexible Retrieval
```

---

Requirement 2

Thumbnail:

Need only

```
60 Days
```

After that,

Delete.

Because it can be recreated.

Lifecycle:

```
Day 0

↓

One Zone IA

↓

Day 60

↓

Delete
```

---

Visual

```
Original Photo

Standard
    │
60 Days
    ▼
Glacier

---------------------

Thumbnail

One Zone IA
      │
60 Days
      ▼
Delete
```

---

Why One Zone IA?

Because thumbnails are not important.

If one Availability Zone fails,

Application recreates them.

No need for expensive storage.

---

# Scenario 2 (Deleted Objects)

This scenario requires understanding versioning.

---

Suppose:

```
report.pdf
```

exists.

User deletes it.

Without Versioning:

```
Deleted

Gone Forever
```

Impossible to recover.

---

With Versioning

Delete actually means:

```
Delete Marker
```

Object still exists underneath.

Visual

```
Version 3

Delete Marker

Version 2

Version 1
```

Latest version becomes:

Delete Marker.

Older versions still exist.

---

Requirement

Recover immediately

for

```
30 Days
```

---

Best storage:

```
Standard IA
```

because retrieval is immediate.

Lifecycle:

```
Noncurrent Versions

↓

Standard IA
```

---

Requirement

After 30 days,

recovery may take

```
48 Hours
```

Now cheaper storage is acceptable.

Lifecycle:

```
Noncurrent Version

↓

Standard IA

↓

Glacier Deep Archive
```

---

Visual

```
Current Object

↓

Deleted

↓

Delete Marker

↓

Old Version

↓

30 Days

↓

Standard IA

↓

365 Days

↓

Glacier Deep Archive
```

---

Important Exam Point

Lifecycle can target:

```
Current Version

OR

Noncurrent Version
```

This appears frequently in SAA questions.

---

# What Are Noncurrent Versions?

Suppose versioning is enabled.

Upload:

```
Resume.pdf
```

Version:

```
V1
```

Edit:

```
Resume.pdf
```

New version:

```
V2
```

Now:

```
Current Version

↓

V2

Noncurrent

↓

V1
```

Lifecycle can move only V1 to Glacier, while V2 stays in Standard.

---

# S3 Storage Class Analysis

Students often ask:

> "How do I know when to move objects?"
> 

AWS answers with:

**S3 Storage Class Analysis.**

---

It analyses object access patterns.

Example report:

```
Object Age

10 Days

Frequently Accessed

----------------

Object Age

120 Days

Rarely Accessed
```

AWS recommends:

```
Move after 90 days.
```

---

Important

Supports:

```
Standard

Standard IA
```

Does NOT analyse:

```
One Zone IA

Glacier

Deep Archive
```

---

Report

Generated:

```
Daily
```

First report appears after:

```
24-48 Hours
```

---

Visual

```
Bucket
   │
Storage Analysis
   │
CSV Report
   │
Recommendation
   │
Lifecycle Rule
```

---

# Real Company Example

Company stores CCTV videos.

Week 1:

Videos watched frequently.

```
Standard
```

After 30 days:

Almost never viewed.

```
Standard IA
```

After one year:

Needed only for legal reasons.

```
Deep Archive
```

After seven years:

Delete.

Lifecycle:

```
Upload
   │
30 Days
   ▼
Standard IA
   │
365 Days
   ▼
Deep Archive
   │
7 Years
   ▼
Delete
```

---

# Exam Comparison

| Action | Lifecycle Does? |
| --- | --- |
| Move Standard → Glacier | ✅ |
| Delete Objects | ✅ |
| Delete Old Versions | ✅ |
| Move Old Versions | ✅ |
| Copy to another bucket | ❌ |
| Replicate to another Region | ❌ |

---

# Memory Trick

Imagine your clothes.

```
New Clothes

↓

Bedroom Closet

↓

After 6 months

↓

Storage Room

↓

After 5 years

↓

Throw Away
```

You don't buy new clothes.

You don't copy them.

You simply move them to cheaper storage and eventually discard them.

That's exactly what an **S3 Lifecycle Policy** does.

---

# 30-Second SAA-C03 Summary

- **Lifecycle Policy** = Automatically transition objects between S3 storage classes or expire (delete) them based on age.
- **Transition** = Move to a cheaper storage class while keeping the object.
- **Expiration** = Permanently delete the object.
- **Current versions** and **noncurrent versions** (with versioning enabled) can have separate lifecycle rules.
- **S3 Storage Class Analysis** helps determine the best time to transition objects by analyzing access patterns, making it a great first step before creating lifecycle rules.
