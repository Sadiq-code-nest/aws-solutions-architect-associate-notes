# PART 2 — What Can CloudFront Use as an Origin?

Origin means

> **The original source of the files.**
> 

CloudFront itself does NOT permanently store your files.

It retrieves them from an Origin.

---

There are two types.

# 1. S3 Bucket

Most common.

```
S3

↓

CloudFront

↓

Users
```

Best for

- Images
- Videos
- CSS
- JS
- Downloads

---

# 2. Custom Origin

Any HTTP server.

Examples

- ALB
- EC2
- On-premises Web Server
- Static Website
- Any HTTP backend

Diagram

```
Users

↓

CloudFront

↓

ALB

↓

EC2
```

---

# S3 Origin vs S3 Website

Students confuse this.

Normal S3 Bucket

```
Private Bucket
```

Use

OAC

---

Static Website Hosting

```
Public Website Endpoint
```

Can also be Origin.

---

# OAI vs OAC

Old method

```
Origin Access Identity
```

New method

```
Origin Access Control
```

Exam answer today

✅ OAC

---

# What does OAC do?

Suppose bucket is private.

Without OAC

CloudFront

↓

Cannot access bucket.

---

With OAC

```
CloudFront

↓

Authenticated

↓

Allowed

↓

Private Bucket
```

Users still cannot access S3 directly.

Only CloudFront can.

---

# Architecture

```
Users

↓

CloudFront

↓

OAC

↓

Private S3 Bucket
```

---

# Why Better?

Without OAC

Some people may accidentally make S3 public.

With OAC

Bucket remains private.

Much safer.

---

# PART 3 — CloudFront as Upload (Ingress)

Most people think

CloudFront only downloads.

Actually,

CloudFront can also receive uploads.

Example

```
User

↓

CloudFront

↓

S3
```

Useful when uploads come from around the world.

---

# PART 4 — CloudFront vs S3 Cross Region Replication

These are very different.

Students confuse them.

---

## CloudFront

Stores

Temporary Cache

Example

```
USA Bucket

↓

Europe Edge

↓

Temporary Copy
```

TTL expires

↓

Copy removed or refreshed.

---

## Cross Region Replication

Actually copies

Entire Object

to another Region.

```
USA Bucket

↓

Replication

↓

London Bucket

↓

Permanent Copy
```

---

# Diagram

CloudFront

```
USA Bucket

↓

Europe Cache

↓

Temporary
```

CRR

```
USA Bucket

↓

London Bucket

↓

Permanent
```

---

# Comparison

| CloudFront | CRR |
| --- | --- |
| Cache | Replication |
| Temporary | Permanent |
| Global Edge | Selected Regions |
| TTL | No TTL |
| Faster Downloads | Disaster Recovery & Compliance |

---

# Exam Scenario

Need

Global image delivery

↓

CloudFront

Need

Permanent copy in Europe

↓

CRR

---

# PART 5 — Geo Restriction

Sometimes

Company says

> Only USA users can watch this movie.
> 

CloudFront supports

Geo Restriction.

---

Allowlist

```
USA

Canada

UK
```

Only these countries allowed.

---

Blocklist

```
Russia

North Korea
```

Blocked.

---

AWS uses

Geo-IP Database

to determine the country.

---

# Example

Netflix

Movie licensed only in Japan.

CloudFront

↓

Only Japan allowed.

Everyone else

↓

403 Forbidden.

---

# PART 6 — CloudFront Pricing

Not every Edge costs the same.

Example

USA

↓

Cheap

Australia

↓

More expensive

South America

↓

Expensive

---

AWS offers

Price Classes.

---

# Price Class 100

Only cheapest locations.

Lowest cost.

Lower coverage.

---

# Price Class 200

Most locations.

Except expensive ones.

---

# Price Class All

Every Edge worldwide.

Best performance.

Highest cost.

---

# PART 7 — Cache Invalidation

Very important exam topic.

Suppose

CloudFront cached

```
logo.png
```

TTL

```
24 Hours
```

Now

You replace logo.

CloudFront still serves

Old logo.

Why?

Because TTL hasn't expired.

---

Solution

Invalidate Cache.

---

Diagram

```
Old Logo

↓

Cached

↓

Replace Logo

↓

Still Old

↓

Invalidate

↓

CloudFront Deletes Cache

↓

Fetches New Logo
```

---

Invalidate

```
/images/*
```

or

```
*
```

---

# Exam Trick

Need immediate update?

↓

CloudFront Invalidation.

---

# CloudFront Request Flow (Memory Diagram)

```
User

↓

Nearest CloudFront Edge

↓

Cache Hit?

      │

 YES────────────► Return Immediately

      │

 NO

      │

      ▼

Origin (S3 / ALB / EC2)

↓

Store in Edge Cache

↓

Return to User
```

---

# CloudFront + OAC Architecture

```
Users

      │

      ▼

CloudFront

      │

Origin Access Control (OAC)

      │

Private S3 Bucket

(No direct public access)
```

---

# Global Accelerator Flow

```
Global User

↓

Nearest AWS Edge

↓

AWS Global Network

↓

Healthy ALB / EC2

↓

Application
```

---

# SAA-C03 Ultimate Cheat Sheet

| Requirement | AWS Service |
| --- | --- |
| Deliver static content globally | ✅ CloudFront |
| Cache content at edge locations | ✅ CloudFront |
| Keep S3 private while serving through CDN | ✅ CloudFront + OAC |
| Update cached content immediately | ✅ CloudFront Invalidation |
| Restrict content by country | ✅ CloudFront Geo Restriction |
| Reduce CloudFront cost | ✅ Price Class 100/200 |
| Permanently replicate S3 data to another region | ✅ S3 Cross-Region Replication |
| Improve global TCP/UDP application performance | ✅ Global Accelerator |
| Automatic regional failover | ✅ Global Accelerator Health Checks |
| Fixed IPs for firewall allowlists | ✅ Global Accelerator |

---

# 🎯 Final Memory Trick (Never Confuse These)

Imagine you own a **global pizza business**:

- **CloudFront** = You **cook one pizza** at the main kitchen (origin) and place **temporary hot copies** at nearby delivery hubs (edge locations). Customers get pizza quickly because it's already nearby. This is **caching**.
- **S3 Cross-Region Replication (CRR)** = You build **another full kitchen** in another country. Every new pizza recipe is permanently copied there. This is **replication**, not caching.
- **Global Accelerator** = You **don't move the pizza**. Instead, you build **faster highways** so customers reach the original kitchen more quickly using AWS's global network. This is **network acceleration**, not caching.

Remember this exam rule:

- **CloudFront = Cache Content**
- **CRR = Copy Data**
- **Global Accelerator = Accelerate Network Traffic**
