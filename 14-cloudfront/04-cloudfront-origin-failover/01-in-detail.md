# CloudFront Origin Failover — AWS SAA-C03 Notes

## 1. What is Origin Failover?

**CloudFront Origin Failover** provides a **backup origin** when your primary origin fails.

Think:

```
User
 ↓
CloudFront
 ↓
Primary Origin
    ❌ fails
 ↓
Secondary Origin
    ✅
```

The goal is mainly:

> **Improve availability and resilience of your CloudFront application.**
> 

---

# 2. What is an Origin Group?

CloudFront implements origin failover using an **Origin Group**.

An origin group contains:

```
Origin Group
│
├── Primary Origin
└── Secondary Origin
```

Example:

```
             CloudFront
                 │
                 ▼
        ┌─────────────────┐
        │  Origin Group   │
        └────────┬────────┘
                 │
          ┌──────┴──────┐
          ▼             ▼
      Primary       Secondary
      S3 Bucket A    S3 Bucket B
```

Normally, CloudFront sends requests to the **primary**.

---

# 3. How does failover happen?

Suppose

```
User
 ↓
CloudFront
 ↓
Primary Origin
 ↓
❌ HTTP 500
```

If that status code is configured as a failover condition:

```
CloudFront
 ↓
Secondary Origin
 ↓
✅ Response
```

So the user can still receive the content.

---

# 4. Which errors can trigger failover?

This is an important SAA-C03 detail.

CloudFront origin failover can be configured for certain **HTTP 4xx/5xx status codes**.

Common examples include:

- **400**
- **403**
- **404**
- **416**
- **500**
- **502**
- **503**
- **504**

You select the status codes that should cause CloudFront to try the secondary origin.

### Exam clue:

> **"504 from primary origin → automatically use backup origin"**
> 

Think:

🔥 **CloudFront Origin Failover**

---

# 5. Simple real-world example

Imagine a news website:

```
CloudFront
    │
    ▼
Primary: S3 Bucket A
    │
    ❌ unavailable
    │
    ▼
Secondary: S3 Bucket B
    │
    ✅
```

Both buckets contain copies of the website's static content.

If S3 Bucket A/origin becomes unavailable and produces a configured failure response, CloudFront can retrieve the object from Bucket B.

---

# 6. Important: Primary is NOT load balanced with Secondary

This is a common misunderstanding.

Origin failover is **not**:

```
Primary ─┐
         ├── Load balance traffic
Secondary┘
```

Instead:

```
Primary
  │
  │ failure
  ▼
Secondary
```

Normally, the secondary is **backup**, not a destination receiving normal traffic.

---

# 7. Origin Failover vs Origin Routing

Don't confuse failover with choosing different origins based on normal routing.

### Origin Failover

```
Primary → fails → Secondary
```

Purpose:

> **Availability**
> 

### Normal origin selection

CloudFront can route requests to different origins based on behaviors/path patterns.

Example:

```
/images/* → S3
/api/*    → ALB
```

Purpose:

> **Route different types of requests to different origins**
> 

---

# 8. Origin Failover vs Route 53 Failover

Another important exam distinction.

### CloudFront Origin Failover

```
User
 ↓
CloudFront
 ↓
Primary origin
 ↓ failure
Secondary origin
```

This happens **inside the CloudFront distribution**.

---

### Route 53 Failover Routing

Route 53 can direct DNS traffic between different endpoints:

```
Route 53
   │
   ├── Primary endpoint
   │
   └── Secondary endpoint
```

This is **DNS-level failover**.

### Shortcut:

> **CloudFront origin failure → Origin Failover**
> 

> **DNS endpoint failure → Route 53 Failover Routing**
> 

---

# 9. Origin Failover vs Multi-Region Deployment

Suppose the question says:

> "Deploy application in multiple AWS Regions and route users to the closest region."
> 

That's a different architecture.

It might involve:

```
Route 53
   │
   ├── us-east-1
   ├── eu-west-1
   └── ap-southeast-1
```

That's useful for:

- Global applications
- Regional redundancy
- Latency optimization
- Disaster recovery

But if the question specifically says:

> **"CloudFront primary origin should automatically switch to a backup origin when it returns 504"**
> 

then:

### ✅ Origin Failover

---

# 10. Origin Group and Cache Behavior

This is an important relationship.

You configure:

```
CloudFront Distribution
        │
        ├── Cache Behavior
        │
        └── Origin Group
              │
              ├── Primary
              └── Secondary
```

The cache behavior determines which origin/origin group handles matching requests.

For example:

```
/images/*
     ↓
Origin Group
     ↓
Primary S3
Secondary S3
```

---

# 11. Does CloudFront always contact the secondary when the primary is slow?

Not simply because it is **slow**.

Failover is based on configured **failure responses/status codes**.

For example:

```
Primary → HTTP 504
                ↓
          Failover condition
                ↓
Secondary
```

So remember:

> **Configured HTTP failure response → failover**
> 

not:

> "Primary is slightly slower → automatically switch."
> 

---

# 12. Does failover improve performance?

Not primarily.

Origin failover is mainly about:

### **High availability**

It prevents users from getting errors when the primary origin fails.

```
Without failover:

Primary ❌
   ↓
CloudFront
   ↓
User gets error ❌

With failover:

Primary ❌
   ↓
CloudFront
   ↓
Secondary ✅
   ↓
User gets content ✅
```

---

# 13. Does origin failover require two origins?

Yes.

You need:

```
Primary Origin
+
Secondary Origin
```

They can be different types of origins, provided the CloudFront configuration supports them.

Examples can include:

```
S3 → S3
S3 → ALB
ALB → S3
Custom origin → Custom origin
```

The key idea is simply:

> **Two origins in one origin group.**
> 

---

# 14. Very important: Failover does not replicate your data

CloudFront Origin Failover itself does **not** copy data from the primary origin to the secondary.

For example:

```
Primary S3
├── image1.jpg
├── image2.jpg
└── image3.jpg

Secondary S3
├── image1.jpg
├── image2.jpg
└── image3.jpg
```

You need to ensure the secondary contains the necessary content.

For S3, you might use:

**S3 Replication**

to keep the backup bucket synchronized.

So:

> **Origin Failover = switches traffic**
> 

> **Replication = keeps data/content available in the backup**
> 

These are separate concepts.

---

# 15. Example with S3 replication

A strong architecture could be:

```
                 CloudFront
                     │
                     ▼
               Origin Group
                 /        \
                /          \
               ▼            ▼
        Primary S3      Secondary S3
        us-east-1       us-west-2
             │               ▲
             └── Replication ┘
```

Normal operation:

```
CloudFront → Primary S3
```

Primary fails:

```
CloudFront → Secondary S3
```

The secondary has the required content because replication keeps it updated.

---

# 16. What happens when the primary comes back?

CloudFront can return to using the **primary origin** after the failure condition is no longer present.

The important mental model is:

```
Normal:
Primary ✅
   ↓
CloudFront → Primary

Failure:
Primary ❌
   ↓
CloudFront → Secondary

Recovery:
Primary ✅
   ↓
CloudFront → Primary
```

---

# 17. Origin Failover vs CloudFront caching

Don't confuse these.

### Caching

```
User
 ↓
CloudFront Cache
 ↓
Cache HIT → content returned
```

This reduces origin requests.

### Origin Failover

```
Cache MISS
 ↓
Primary Origin ❌
 ↓
Secondary Origin
```

So failover becomes relevant when CloudFront needs to retrieve content from an origin and the primary encounters a configured failure.

---

# 18. Origin Failover and 504 — your exam question

Your previous question had:

> "users encounter HTTP 504 errors"
> 

That is a strong clue.

You can create:

```
Origin Group
│
├── Primary Origin
└── Secondary Origin
```

Configure **504** as a failover status code.

Then:

```
CloudFront
     ↓
Primary
     ↓
HTTP 504 ❌
     ↓
Secondary
     ↓
Content ✅
```

Therefore:

### 🔥 504 + CloudFront + backup origin = Origin Failover

---

# 19. What is NOT Origin Failover?

### ❌ Auto Scaling

Adds/removes EC2 instances.

### ❌ Route 53 latency routing

Chooses the region based on latency.

### ❌ S3 Cross-Region Replication

Copies S3 data between buckets.

### ❌ CloudFront caching

Stores content at edge locations.

### ❌ Multi-AZ

Provides availability across Availability Zones.

### ✅ Origin Failover

Switches CloudFront from:

**Primary origin → Secondary origin**

when configured failure conditions occur.

---

# 20. SAA-C03 Keyword Map

| Question clue | Think |
| --- | --- |
| CloudFront + backup origin | **Origin Failover** |
| Primary + secondary origin | **Origin Group** |
| Automatically switch origins | **Origin Failover** |
| Primary returns 504 | **Origin Failover** |
| Improve origin availability | **Origin Failover** |
| CloudFront origin unavailable | **Origin Failover** |
| DNS failover | **Route 53 Failover Routing** |
| Keep backup S3 synchronized | **S3 Replication** |
| Reduce origin requests | **CloudFront caching** |
| Global latency-based routing | **Route 53 latency routing** |

---

# 🧠 Final SAA-C03 Cheat Sheet

Remember this picture:

```
                         USER
                           │
                           ▼
                      CLOUDFRONT
                           │
                           ▼
                     ORIGIN GROUP
                      /         \
                     /           \
                    ▼             ▼
               PRIMARY          SECONDARY
                 ORIGIN            ORIGIN
                    │
                 ❌ 5xx/4xx
                    │
                    ▼
               FAILOVER
                    │
                    ▼
               SECONDARY
                    │
                    ▼
                 CONTENT
```

### The 5 things to remember:

1. **Origin Failover = high availability for CloudFront origins.**
2. **Origin Group = Primary + Secondary.**
3. **Primary is used normally.**
4. **Configured HTTP failure codes can trigger failover.**
5. **Failover switches traffic; it does NOT replicate your data.**

### 🔥 One-line exam rule:

> **"CloudFront + primary origin failure + automatically use backup origin" = CloudFront Origin Failover.**
>
