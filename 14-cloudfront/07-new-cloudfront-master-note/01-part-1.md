# ☁️ Amazon CloudFront — MASTER NOTE for AWS SAA-C03

This is a **complete exam-revision note** built around the topics you listed. I’ve also added a few important CloudFront concepts that commonly appear in SAA-C03 questions.

The goal is:

> **Read this one note → understand what CloudFront does → recognize the scenario → choose the correct feature.**
> 

---

# 1. What is Amazon CloudFront?

**Amazon CloudFront = AWS Content Delivery Network (CDN).**

It delivers content to users from AWS **edge locations close to the users**, instead of making every user request travel all the way to the origin.

### Simple picture

```
User in Bangladesh
       ↓
CloudFront Edge Location
       ↓
   Cache Hit?
    ↙      ↘
   YES      NO
    ↓        ↓
Response   Origin
           ↓
      S3 / ALB / EC2 /
      API Gateway / etc.
```

### Main benefits

| Benefit | Meaning |
| --- | --- |
| ⚡ Low latency | Content served from a location closer to the user |
| 🚀 Better performance | Faster delivery of static/dynamic content |
| 📉 Lower origin load | Cached content doesn't repeatedly hit origin |
| 🌍 Global delivery | Users around the world access content through CloudFront |
| 🔐 Security | Integrates with WAF, Shield, OAC, signed URLs/cookies, HTTPS |
| 💰 Potential cost reduction | Less traffic reaches the origin |
| 📦 Content caching | Frequently requested content is cached at edge locations |

---

# 2. The Core CloudFront Architecture

Remember these **5 words**:

> **Viewer → Distribution → Edge → Cache → Origin**
> 

Example:

```
                 CloudFront
              Global Distribution
                     │
        ┌────────────┼────────────┐
        ↓            ↓            ↓
    Edge/POP      Edge/POP      Edge/POP
    Bangladesh    Singapore     USA
        │            │            │
        └────────────┼────────────┘
                     ↓
                   Origin
              ┌──────┼───────┐
              ↓      ↓       ↓
             S3     ALB     API
```

### Important terms

| Term | Simple meaning |
| --- | --- |
| **Viewer** | Client/user requesting content |
| **Distribution** | Main CloudFront configuration |
| **Edge Location / POP** | Location where CloudFront serves/caches content |
| **Cache** | Temporary copy of content at edge |
| **Origin** | Original source of content |
| **Cache Hit** | Object found in CloudFront cache |
| **Cache Miss** | Object not available/valid in cache → request goes to origin |
| **TTL** | How long an object can remain cached |
| **Cache Key** | Determines which requests are considered the same cached object |
| **Behavior** | Rules for requests matching a path pattern |
| **Origin Group** | Primary + secondary origin for failover |

---

# 3. What Can Be a CloudFront Origin?

CloudFront can work with different origins.

| Origin | Example | Typical use |
| --- | --- | --- |
| **S3 bucket** | `mybucket.s3.amazonaws.com` | Static files, images, videos |
| **S3 website endpoint** | S3 static website | Static website with S3 website-hosting behavior |
| **ALB** | Application Load Balancer | Web applications |
| **NLB** | Network Load Balancer | TCP/HTTP applications; current CloudFront supports NLB origins |
| **EC2** | EC2 web server | Custom application |
| **API Gateway** | REST/HTTP API | APIs |
| **MediaStore / MediaPackage** | Media services | Media delivery |
| **Custom HTTP origin** | Your public web server | Non-AWS origin |
| **VPC origin** | Private ALB/NLB/EC2 | Private applications in VPC |

AWS currently supports **internet-facing NLBs as CloudFront origins**, and CloudFront **VPC origins** can use private ALBs, NLBs, or EC2 instances.

### 🚨 Exam correction

If you see:

> "NLB cannot be used as CloudFront origin"
> 

That is **not generally correct**.

NLB can be a CloudFront origin.

For **private VPC applications**, CloudFront VPC origins support:

- ALB
- NLB
- EC2

But **Gateway Load Balancer cannot be a VPC origin**.

---

# 4. CloudFront Distribution

A **distribution** is basically the configuration of your CloudFront CDN.

It defines things like:

- Origins
- Cache behaviors
- TLS certificate
- Allowed HTTP methods
- Cache policies
- Origin request policies
- WAF
- Geo restrictions
- Logging
- Compression
- Signed URL/cookie requirements

Example:

```
CloudFront Distribution
│
├── Origin → S3
├── Origin → ALB
├── Behavior /images/*
├── Behavior /api/*
├── HTTPS
├── WAF
├── Cache Policy
└── Logging
```

---

# 5. CloudFront URL

CloudFront gives you a domain such as:

```
https://d123example.cloudfront.net
```

You can also use your own domain:

```
https://www.example.com
```

using:

```
Route 53
     ↓
CloudFront
```

---

# 6. CloudFront Edge Locations

CloudFront has globally distributed **Points of Presence (POPs)/edge locations**.

Suppose:

```
User → Bangladesh
```

CloudFront attempts to serve content from an appropriate nearby edge location.

If cached:

```
User
 ↓
Edge Cache
 ↓
Response
```

No origin request is necessary.

If not cached:

```
User
 ↓
Edge
 ↓
Origin
 ↓
Edge Cache
 ↓
User
```

The returned object can then be cached according to the caching configuration.

---

# 7. Cache Hit vs Cache Miss

This is one of the most important CloudFront concepts.

### Cache Hit

```
User
 ↓
CloudFront Edge
 ↓
Object exists in cache
 ↓
Return object
```

Benefits:

- Faster
- Lower origin load
- Potentially lower origin transfer costs

### Cache Miss

```
User
 ↓
CloudFront Edge
 ↓
Object NOT available/valid
 ↓
Origin
 ↓
CloudFront
 ↓
User
```

---

# 8. Why Does CloudFront Sometimes Contact the Origin?

Even though you have CloudFront caching, origin requests can still happen.

Common reasons:

- First request to an edge location
- Object expired
- TTL is very short
- Cache behavior doesn't allow effective caching
- Request has a different cache key
- Object was invalidated
- Dynamic/non-cacheable content

CloudFront uses TTL settings and origin `Cache-Control`/`Expires` headers to determine how long content remains cached.

---

# 9. TTL — Time To Live

TTL controls **how long CloudFront keeps an object in cache**.

There are three important values:

| TTL | Meaning |
| --- | --- |
| **Minimum TTL** | Minimum time object remains cached |
| **Default TTL** | Used when origin doesn't provide caching headers |
| **Maximum TTL** | Maximum allowed caching duration |

### Example

Suppose:

```
Default TTL = 1 day
```

User requests:

```
logo.png
```

CloudFront gets it from S3 and caches it.

For subsequent requests during its valid cache lifetime:

```
User → CloudFront
```

instead of:

```
User → CloudFront → S3
```

### Important

Origin can send:

```
Cache-Control: max-age=3600
```

CloudFront uses that together with its TTL limits.

AWS recommends using **cache policies** to configure TTL and cache-key behavior.

---

# 10. Cache-Control

Origin can tell CloudFront how long content should be cached.

Example:

```
Cache-Control: max-age=86400
```

Means approximately:

> Cache for 86,400 seconds = 24 hours.
> 

### Good scenario

Static CSS:

```
style.css
```

doesn't change frequently.

Use a longer cache lifetime.

### Bad scenario

Bank account balance:

```
/account/balance
```

You generally don't want stale cached data.

Use appropriate non-cacheable/dynamic behavior.

---

# 11. Cache Key ⭐⭐⭐⭐⭐

The **cache key** determines whether two requests refer to the same cached object.

CloudFront can consider:

- URL path
- Query strings
- HTTP headers
- Cookies

through cache policy configuration.

### Example

Suppose:

```
/image.jpg?size=small
/image.jpg?size=large
```

If query string is included in the cache key:

```
/image.jpg?size=small
        ≠
/image.jpg?size=large
```

They can have separate cached responses.

---

# 12. Cache Policy vs Origin Request Policy ⭐⭐⭐⭐⭐

Very important.

| Feature | Cache Policy | Origin Request Policy |
| --- | --- | --- |
| Controls cache key | ✅ Yes | ❌ No |
| Controls what CloudFront forwards to origin | Partly related | ✅ Main purpose |
| Headers | Can include in cache key | Can forward |
| Cookies | Can include in cache key | Can forward |
| Query strings | Can include in cache key | Can forward |
| Main purpose | **Caching** | **Origin request** |

### Simple memory trick

> **Cache Policy = What makes two requests different in cache?**
> 

> **Origin Request Policy = What should I send to my origin?**
> 

---

# 13. Cache Behaviors

A CloudFront distribution can have multiple **cache behaviors**.

Example:

```
CloudFront
│
├── /images/*  → S3
├── /api/*     → ALB
└── /*         → S3
```

This allows different rules for different URL paths.

### Example

```
example.com/images/cat.jpg
```

→ S3

```
example.com/api/orders
```

→ ALB

You can configure different:

- Origins
- Cache policies
- Allowed methods
- Viewer protocol policies
- Functions
- Lambda@Edge
- Security settings

per behavior.

---

# 14. Multiple Origins

A single distribution can have multiple origins.

Example:

```
CloudFront
│
├── S3 → Static content
│
└── ALB → Dynamic application
```

Then use cache behaviors:

```
/images/* → S3

/api/* → ALB
```

---

# 15. Origin Groups ⭐

Origin Groups provide **origin failover**.

Example:

```
CloudFront
    │
    ▼
Primary Origin
   S3-A
    │
   FAIL
    ↓
Secondary Origin
   S3-B
```

You configure:

```
Primary
+
Secondary
```

If the primary returns certain configured failure responses, CloudFront can try the secondary.

### Scenario

A company has:

```
Primary origin → ALB
Backup origin → S3 static emergency page
```

If ALB fails:

```
CloudFront → S3 backup
```

Users can still receive content.

---

# 16. CloudFront + S3 ⭐⭐⭐⭐⭐

One of the most common SAA-C03 architectures:

```
Users
  ↓
CloudFront
  ↓
S3
```

### Why?

- Global delivery
- Edge caching
- Low latency
- Reduced S3 request load
- S3 provides durable storage
- CloudFront provides CDN

---

# 17. S3 REST Endpoint vs S3 Website Endpoint

This distinction is extremely important.

### S3 REST endpoint

```
CloudFront
    ↓
S3 bucket
```

You can use:

**OAC — Origin Access Control**

to prevent direct public access to S3.

### S3 website endpoint

```
CloudFront
    ↓
S3 Website Endpoint
```

This is treated as a **custom origin**.

OAC/OAI cannot be used with an S3 website endpoint. AWS states that if an S3 bucket is configured as a website endpoint, CloudFront must use it as a custom origin.

### Exam memory

> **Private S3 + CloudFront → OAC**
> 

> **S3 Website Endpoint → Custom Origin**
> 

---

# 18. Origin Access Control — OAC ⭐⭐⭐⭐⭐

OAC protects an S3 origin.

Without OAC:

```
User
 ├──→ CloudFront
 └──→ S3 directly ❌
```

With OAC:

```
User
   ↓
CloudFront
   ↓
OAC authentication
   ↓
Private S3
```

The S3 bucket policy allows the CloudFront service principal to access the bucket on behalf of the specific distribution.

### Exam clue

> "Users must access S3 objects only through CloudFront."
> 

Answer:

**CloudFront + OAC + private S3 bucket**

---

# 19. OAC vs OAI

|  | OAC | OAI |
| --- | --- | --- |
| Full name | Origin Access Control | Origin Access Identity |
| Status | ✅ Recommended | ⚠️ Legacy |
| S3 security | ✅ | ✅ |
| SSE-KMS | ✅ | ❌ |
| Dynamic S3 requests | ✅ | Limited |
| New AWS Regions | ✅ | Limitations |
| Recommended for new designs | ✅ | ❌ |

AWS recommends **OAC instead of legacy OAI**.

---

# 20. CloudFront + SSE-KMS S3

If S3 objects use:

```
SSE-KMS
```

and CloudFront OAC accesses them, the KMS key policy must allow the CloudFront service principal appropriate KMS permissions.

Conceptually:

```
User
 ↓
CloudFront
 ↓
OAC
 ↓
S3
 ↓
KMS
 ↓
Decrypt object
```

---

# 21. Signed URLs ⭐⭐⭐⭐⭐

Signed URLs provide **private access to specific CloudFront content**.

Example:

```
https://example.com/movie.mp4
```

Instead of giving everyone access, your application creates a signed URL:

```
https://example.com/movie.mp4
?Expires=...
&Signature=...
```

CloudFront validates the signature.

### Scenario

A customer purchases:

```
premium-video.mp4
```

Give them a signed URL valid for:

```
30 minutes
```

After 30 minutes:

❌ URL no longer works.

---

# 22. Signed Cookies ⭐⭐⭐⭐⭐

Signed cookies are useful when a user needs access to **multiple files**.

### Signed URL

Best when:

> "Give access to this particular file."
> 

### Signed Cookie

Best when:

> "Give this user access to many protected files."
> 

Example:

```
Premium user
   ↓
Signed Cookie
   ↓
CloudFront
   ↓
/video1.mp4
/video2.mp4
/video3.mp4
```

AWS supports expiration and optional IP restrictions in signed URLs/cookies.

---

# 23. Signed URL vs Signed Cookie

|  | Signed URL | Signed Cookie |
| --- | --- | --- |
| Best for | Individual file | Multiple files |
| URL changes | Yes | No |
| Example | Download PDF | Streaming video website |
| User gets | Special URL | Authentication cookie |
| Exam clue | "specific file" | "multiple files" |
