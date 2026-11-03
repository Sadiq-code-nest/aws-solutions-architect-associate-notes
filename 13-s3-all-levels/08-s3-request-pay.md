# Amazon S3 Requester Pays (SAA-C03) — Complete Crystal Clear Notes

This topic is actually very simple once you understand **who pays the AWS bill**.

The exam usually tests **billing responsibility**, not technical complexity.

---

# 1. Normal S3 Bucket (Default Behavior)

Let's say you own an S3 bucket.

```
My Bucket
│
├── photo1.jpg
├── video.mp4
└── dataset.zip
```

Now someone downloads your file.

Question:

> **Who pays AWS?**
> 

Answer:

👉 **The bucket owner pays for almost everything.**

---

## Cost Breakdown

Suppose:

- Storage Cost = $5/month
- Data Download = $20
- GET Requests = $1

Who pays?

```
Bucket Owner

Storage      ✅ Pays
Download     ✅ Pays
Requests     ✅ Pays
```

The person downloading pays **nothing** to AWS.

---

# Diagram

```
            Download

Requester  ----------------->  S3 Bucket

                     AWS Bill

Storage Cost  ─────────► Bucket Owner
Request Cost  ─────────► Bucket Owner
Download Cost ─────────► Bucket Owner
```

Everything goes to the owner.

---

# Real-Life Example

Imagine you own a restaurant.

A customer comes.

You say:

> "Today's food is free."
> 

Who pays?

👉 You.

The customer eats.

You receive the bill.

---

# Problem

Suppose your bucket contains:

```
100 TB Research Dataset
```

Thousands of researchers download it every day.

Example:

```
1000 downloads/day
```

Each download costs AWS money.

The bill becomes:

```
Storage

$50

Download

$20,000
```

Even though other people are using it,

YOU pay.

That isn't fair.

---

# AWS Solution

AWS created:

## Requester Pays Bucket

---

# 2. What is Requester Pays?

Requester Pays changes only one thing:

> The person downloading the data pays for the request and download.
> 

The bucket owner still pays for storing the objects.

---

# Cost Comparison

## Standard Bucket

| Cost | Who Pays? |
| --- | --- |
| Storage | Bucket Owner |
| GET Request | Bucket Owner |
| Download | Bucket Owner |

---

## Requester Pays Bucket

| Cost | Who Pays? |
| --- | --- |
| Storage | Bucket Owner |
| GET Request | Requester |
| Download | Requester |

Notice:

Storage NEVER changes.

---

# Diagram

```
Requester
      │
      │ Download
      ▼
 S3 Requester Pays Bucket
      │
      ▼
AWS Billing

Storage Cost
        │
        ▼
Bucket Owner

Request Cost
        │
        ▼
Requester

Download Cost
        │
        ▼
Requester
```

---

# Important

Bucket owner ALWAYS pays:

```
Storage Cost
```

Because the bucket belongs to them.

Only:

```
Download

GET Requests

Transfer
```

are charged to the requester.

---

# Real-Life Example

Imagine Netflix.

Netflix stores movies.

Storage:

```
Netflix pays.
```

But suppose every customer must pay for internet bandwidth used while streaming.

Now:

Netflix:

```
Pays storage.
```

Customer:

```
Pays for watching.
```

That's the idea behind Requester Pays.

---

# AWS Example

Suppose NASA stores:

```
500 TB Satellite Images
```

in S3.

Researchers worldwide download images.

Without Requester Pays:

```
NASA pays every download.
```

Huge bill.

---

With Requester Pays:

```
NASA pays

Storage

Researchers pay

Downloads
```

Now costs are shared fairly.

---

# Why Does AWS Require Authentication?

The requester must be:

```
Authenticated AWS User
```

Why?

Because AWS needs to know:

> "Whose AWS account should receive the bill?"
> 

Suppose an anonymous internet user downloads:

```
dataset.zip
```

AWS doesn't know:

- Which AWS account owns the request?
- Who should be billed?

Impossible.

Therefore:

Anonymous users are NOT allowed.

---

# Diagram

## Authenticated User

```
AWS Account
      │
      ▼
Download Object
      │
      ▼
AWS charges THAT account.
```

Works.

---

## Anonymous User

```
Unknown Person
      │
      ▼
Download Object
      │
      ▼
Who pays?
```

AWS doesn't know.

Rejected.

---

# What Changes Internally?

Imagine:

Standard bucket:

```
GET Object

↓

Bucket Owner's Bill
```

Requester Pays:

```
GET Object

↓

AWS checks requester identity

↓

Charges Requester's AWS Account
```

Storage remains billed to the owner.

---

# Common Exam Scenario

Question:

> A company shares a 50 TB public dataset with many AWS customers.
> 
> 
> The company wants users to pay the download costs while the company continues paying only for storage.
> 

Answer:

✅ Enable **Requester Pays**.

---

# Another Exam Scenario

Company stores backups.

Nobody except the company downloads them.

Should they enable Requester Pays?

❌ No.

There is no benefit.

---

# What Does NOT Change?

Requester Pays does NOT:

❌ Replicate data

❌ Encrypt data

❌ Change storage class

❌ Improve performance

❌ Improve availability

It ONLY changes:

```
Who pays the request and transfer costs.
```

---

# Standard Bucket vs Requester Pays

## Standard Bucket

```
Requester
     │
Download
     ▼
Bucket

Storage Cost
        ▼
Owner

Download Cost
        ▼
Owner
```

---

## Requester Pays Bucket

```
Requester
      │
Download
      ▼
Bucket

Storage Cost
       ▼
Owner

Download Cost
       ▼
Requester
```

---

# Memory Trick

Imagine you're hosting a party.

## Standard Bucket

You invite everyone.

You buy:

- Food ✅
- Drinks ✅
- Delivery ✅

You pay for everything.

---

## Requester Pays Bucket

You still host the party.

You still rent the hall.

But each guest buys their own food.

You only pay for the venue.

---

# SAA-C03 Exam Cheat Sheet

| Feature | Standard Bucket | Requester Pays Bucket |
| --- | --- | --- |
| Storage Cost | Bucket Owner | Bucket Owner |
| Request Cost | Bucket Owner | Requester |
| Download Cost | Bucket Owner | Requester |
| Anonymous Access | Allowed (if bucket policy permits) | ❌ Not allowed |
| Authenticated AWS User Required | No | ✅ Yes |
| Common Use Case | Normal applications | Large shared datasets |

---

# 🎯 Ultimate Exam Memory

Whenever you see words like:

- **Large public dataset**
- **Research data**
- **Many AWS customers downloading**
- **Owner wants to reduce download costs**
- **Consumers should pay for bandwidth**

Think immediately:

> ✅ **S3 Requester Pays**
> 

### One-line Summary

**S3 Requester Pays changes only the billing model: the bucket owner continues to pay for storage, while authenticated requesters pay for the S3 request charges and data download (transfer) charges.**
