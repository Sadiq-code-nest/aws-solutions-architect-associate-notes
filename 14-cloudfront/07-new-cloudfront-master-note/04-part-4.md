# 58. Master CloudFront Features Table

| CloudFront Topic | What it does | Simple example | Exam clue |
| --- | --- | --- | --- |
| **CloudFront** | Global CDN | Users access images from nearby edge | Global low latency |
| **Distribution** | Main CloudFront configuration | One CDN configuration | Create CloudFront |
| **Edge Location** | Serves cached content close to users | Bangladesh user → nearby POP | Low latency |
| **Origin** | Original content source | S3 / ALB / EC2 | Where content comes from |
| **Cache** | Stores frequently requested content | `logo.png` | Reduce origin requests |
| **Cache Hit** | Object found in cache | Edge returns image | Fast |
| **Cache Miss** | Object isn't available/valid | Edge fetches S3 | Origin request |
| **Cache Key** | Identifies cached object | URL + selected query/header/cookie | Different response based on request |
| **Cache Policy** | Controls cache key + TTL | Cache based on query string | Caching |
| **Origin Request Policy** | Controls data sent to origin | Forward `User-Agent` | Origin needs header |
| **Cache Behavior** | Rules for path | `/api/* → ALB` | Path-based routing |
| **TTL** | How long object stays cached | 1 hour | Cache duration |
| **Cache-Control** | Origin tells caching behavior | `max-age=86400` | Browser/CloudFront caching |
| **Invalidation** | Remove cached objects | `/index.html` | Fresh content immediately |
| **Versioned URL** | Creates new cache key | `app-v2.js` | Avoid invalidation |
| **Origin Group** | Primary/backup origins | ALB → S3 fallback | Origin failover |
| **Origin Shield** | Extra centralized cache layer | Edge → Shield → origin | Reduce origin load |
| **OAC** | Secure S3 origin | Private S3 accessible through CF | Block direct S3 access |
| **OAI** | Legacy S3 origin access | Old CloudFront/S3 setup | Legacy |
| **Signed URL** | Private access to individual resource | Download PDF for 10 min | One file |
| **Signed Cookie** | Private access to multiple resources | Premium video library | Multiple files |
| **Field-Level Encryption** | Encrypt selected request fields | Credit card field | Sensitive fields |
| **HTTPS** | Encrypt viewer connection | HTTPS website | Secure transit |
| **SNI** | Multiple certificates/domains | `a.com`, `b.com` | Multiple HTTPS domains |
| **Viewer Protocol Policy** | Controls viewer HTTP/HTTPS | Redirect HTTP → HTTPS | Force HTTPS |
| **Origin Protocol Policy** | Controls CF → origin protocol | HTTPS to ALB | Origin connection |
| **Response Headers Policy** | Adds/modifies response headers | CORS/HSTS/custom header | Response headers |
| **CloudFront Functions** | Lightweight edge logic | URL rewrite | Simple edge logic |
| **Lambda@Edge** | Advanced edge logic | Header manipulation/personalization | Complex edge logic |
| **WAF** | Web request filtering | SQL injection/rate limit | Web attacks |
| **Shield** | DDoS protection | Volumetric DDoS | DDoS |
| **Geo Restriction** | Restrict content by country | USA only | Geographic content restriction |
| **Price Class** | Limit edge locations for cost | Reduce edge coverage | Cost optimization |
| **Compression** | Compress responses | Gzip/Brotli | Reduce transfer size |
| **CORS** | Cross-origin browser access | Website A → API B | Cross-origin |
| **Standard Logs** | Request logging | Store access records | Analysis/audit |
| **Real-Time Logs** | Near-real-time request data | Security monitoring | Real-time analysis |
| **CloudWatch** | Monitoring/metrics | Error alarm | Monitoring |
| **Continuous Deployment** | Safely test distribution changes | 5% traffic → staging | Test before production |
| **VPC Origins** | Private VPC origins | CF → private ALB | Keep origin private |

---

# 59. Master Scenario Table ⭐⭐⭐⭐⭐

This is the **exam-night table**.

| Scenario in question | Think of |
| --- | --- |
| Users around world need lower latency | **CloudFront** |
| Static website globally | **S3 + CloudFront** |
| Images/videos need fast global delivery | **CloudFront** |
| Reduce origin server load | **CloudFront caching** |
| S3 should not be directly accessible | **CloudFront OAC** |
| S3 bucket is private but content should be public through CloudFront | **OAC** |
| Need private individual download | **Signed URL** |
| User needs many protected files | **Signed Cookies** |
| Encrypt credit-card/sensitive POST fields | **Field-Level Encryption** |
| Force HTTP users to HTTPS | **Viewer Protocol Policy** |
| Multiple HTTPS domains/certificates | **SNI** |
| HTTPS certificate management | **ACM + CloudFront** |
| Different URL paths → different origins | **Cache Behaviors** |
| `/images/* → S3`, `/api/* → ALB` | **Multiple Origins + Behaviors** |
| Primary origin + backup origin | **Origin Group** |
| Reduce origin load across many edge locations | **Origin Shield** |
| Cached old file must be removed immediately | **Invalidation** |
| Frequently deploy static assets | **Versioned object names** |
| Add CORS/security/custom response headers | **Response Headers Policy** |
| Select image based on browser/request logic | **Edge function/custom logic + correct cache key** |
| Simple URL rewrite/redirect | **CloudFront Functions** |
| Complex processing at edge | **Lambda@Edge** |
| Block SQL injection/XSS | **AWS WAF** |
| Rate-limit suspicious web requests | **AWS WAF rate-based rule** |
| DDoS protection | **AWS Shield** |
| Restrict content by country | **CloudFront Geo Restriction** |
| Reduce CloudFront cost by limiting edge coverage | **Price Class** |
| Private ALB/NLB/EC2 in VPC + CloudFront | **VPC Origins** |
| Monitor CloudFront errors/requests | **CloudWatch** |
| Need request logs | **CloudFront Standard/Real-Time Logs** |
| Safely test new CloudFront configuration | **Continuous Deployment / Staging Distribution** |
| Cache based on query string | **Cache Policy** |
| Forward header/cookie/query string to origin | **Origin Request Policy** |
| Origin sends `Cache-Control` | **TTL + cache headers determine caching** |

---

# 60. 🚨 The Most Important Confusions

## CloudFront vs S3

| S3 | CloudFront |
| --- | --- |
| Object storage | CDN |
| Stores original files | Caches/delivers files |
| Durable storage | Fast global delivery |
| Bucket/object | Distribution/cache |
| Origin can be S3 | S3 can be origin |

### Memory

> **S3 = Store**
> 

> **CloudFront = Deliver**
> 

---

## CloudFront vs S3 Website Hosting

```
S3
 ↓
Static website hosting
```

means S3 itself serves the website.

```
S3
 ↓
CloudFront
 ↓
Users
```

adds CDN/global caching.

---

# 61. OAC vs Signed URL

This is a VERY common confusion.

| OAC | Signed URL |
| --- | --- |
| Protects **origin** | Protects **viewer access** |
| CloudFront → S3 authentication | User → CloudFront authorization |
| Prevents direct S3 access | Controls who can access content |
| Usually with private S3 | Useful for private CloudFront content |

Think:

```
OAC
↓
"Who can access my S3 origin?"
```

```
Signed URL
↓
"Which users can access my CloudFront content?"
```

You can use both together.

---

# 62. WAF vs Shield

| WAF | Shield |
| --- | --- |
| Web application firewall | DDoS protection |
| HTTP/HTTPS request filtering | DDoS attack protection |
| SQL injection | Volumetric/network DDoS |
| XSS | DDoS |
| IP/rate rules | Shield Standard/Advanced |

### Memory

> **WAF = Bad web requests**
> 

> **Shield = DDoS**
> 

---

# 63. CloudFront Functions vs Lambda@Edge

| Question wording | Likely answer |
| --- | --- |
| Simple redirect | CloudFront Functions |
| Simple URL rewrite | CloudFront Functions |
| Simple header manipulation | CloudFront Functions |
| Lightweight edge logic | CloudFront Functions |
| Origin request processing | Lambda@Edge |
| Origin response processing | Lambda@Edge |
| Complex edge customization | Lambda@Edge |
| Advanced personalization | Lambda@Edge |

---

# 64. Signed URL vs Signed Cookie

```
ONE FILE
   ↓
Signed URL

MANY FILES
   ↓
Signed Cookie
```

Example:

```
Download invoice.pdf
→ Signed URL
```

```
Netflix-style premium content
→ Signed Cookie
```

---

# 65. Cache Policy vs Response Headers Policy

Very important because both are called "policies."

|  | Cache Policy | Response Headers Policy |
| --- | --- | --- |
| Main purpose | Caching | HTTP response headers |
| Cache key | ✅ | ❌ |
| TTL | ✅ | ❌ |
| CORS headers | ❌ Main purpose | ✅ |
| Security headers | ❌ | ✅ |
| Custom response headers | ❌ | ✅ |

### Memory

> **Cache Policy = Cache**
> 

> **Response Headers Policy = Headers**
> 

---

# 66. Cache Policy vs Origin Request Policy

```
Cache Policy
     ↓
"What makes this request a different cached object?"
```

```
Origin Request Policy
     ↓
"What information should I send to my origin?"
```

Example:

```
User-Agent
```

If your origin needs it:

→ Origin Request Policy can forward it.

If different User-Agent values produce **different cached responses**, you also need to consider the cache key.

---

# 67. The User-Agent / WebP Example — Understand the Architecture

Suppose:

```
Browser A
supports WebP

Browser B
doesn't support WebP
```

Request:

```
Browser
   ↓
CloudFront
   ↓
inspect request characteristics
   ↓
choose appropriate content
```

Potential logic:

```
WebP supported?
     │
 ┌───┴───┐
YES      NO
 ↓        ↓
WebP     JPEG
```

But there's an important caching consideration:

```
Browser A → WebP
Browser B → JPEG
```

If CloudFront treats both requests as the **same cache key**, it could cache one response and incorrectly serve it to the other client.

Therefore:

> **Whenever request information changes the response, think about the cache key.**
> 

And remember:

> **Response Headers Policy adds/modifies headers; it does not itself perform image-format conversion.**
> 

---

# 68. Complete CloudFront Request Flow

For exam questions, visualize this:

```
                    USER
                      │
                      │ HTTPS
                      ▼
              ┌───────────────┐
              │   CloudFront  │
              │ Distribution  │
              └───────┬───────┘
                      │
             WAF / Shield
                      │
                      ▼
               Cache Behavior
                      │
                Cache Lookup
                 /         \
               HIT         MISS
                │            │
                │            ▼
                │      Origin Request
                │            │
                │            ▼
                │         ORIGIN
                │      ┌─────┼─────┐
                │      │     │     │
                │     S3    ALB    API
                │
                ▼
          Cached Response
                │
                ▼
             USER
```

---

# 69. CloudFront + S3 Private Content — Perfect Architecture

For a private S3-backed website:

```
                    Internet
                       │
                       ▼
                  CloudFront
                       │
                      OAC
                       │
                       ▼
                 Private S3
                       │
                     Objects
```

The S3 bucket policy permits the CloudFront service principal to retrieve objects for the specific distribution.

### Exam wording

> "Prevent users from bypassing CloudFront and accessing S3 directly."
> 

### Think:

**OAC**

---

# 70. CloudFront + ALB + ASG — Classic Architecture

```
Users
  ↓
CloudFront
  ↓
ALB
  ↓
Auto Scaling Group
  ↓
EC2 EC2 EC2
```

Use when:

- Global users
- Web application
- Need caching
- Need load balancing
- Need EC2 scaling
- Need WAF/HTTPS

---

# 71. CloudFront + Private Application

```
Internet
   ↓
CloudFront
   ↓
VPC Origin
   ↓
Private ALB
   ↓
Private EC2
```

This is particularly useful when:

> **The origin must not be publicly exposed.**
> 

AWS supports private ALB, NLB, and EC2 as CloudFront VPC origins.

---

# 72. CloudFront + WAF + Shield

For security:

```
             Internet
                 ↓
              Shield
                 ↓
             CloudFront
                 ↓
                WAF
                 ↓
               ALB
                 ↓
                EC2
```

Conceptually:

```
Shield → DDoS
WAF    → Web attacks
CloudFront → Global edge/CDN
ALB    → Load balancing
EC2    → Application
```

---

# 73. CloudFront + Multiple Origins

Example:

```
                    CloudFront
                        │
              ┌─────────┴─────────┐
              │                   │
         /images/*             /api/*
              │                   │
              ▼                   ▼
             S3                  ALB
              │                   │
         Static files          Application
```

This is an extremely useful architecture pattern.

---

# 74. CloudFront Security Master Table

| Security feature | Protects what? | Example |
| --- | --- | --- |
| **HTTPS/TLS** | Data in transit | Encrypt viewer connection |
| **OAC** | S3 origin | Prevent direct S3 access |
| **Signed URL** | Individual private content | Private PDF |
| **Signed Cookie** | Multiple private files | Premium video |
| **Field-Level Encryption** | Sensitive request fields | Credit-card field |
| **WAF** | Web requests | SQL injection/XSS |
| **Shield** | DDoS attacks | DDoS mitigation |
| **Geo Restriction** | Geographic content access | USA-only content |
| **VPC Origins** | Origin exposure | Private ALB/EC2 |
| **ACM certificate** | HTTPS identity | `www.example.com` |

---

# 75. CloudFront Performance Master Table

| Feature | Performance benefit |
| --- | --- |
| **Edge caching** | Serve content close to users |
| **Cache Policy** | Improve cache hit ratio |
| **Long TTL** | Fewer origin requests |
| **Compression** | Reduce response size |
| **Origin Shield** | Reduce origin requests |
| **Multiple origins** | Route traffic appropriately |
| **CloudFront + S3** | Excellent static-content delivery |
| **CloudFront + ALB** | Accelerate global web applications |
| **Price Class** | Cost optimization, not performance improvement |
| **Invalidation** | Freshness, not performance |

---

# 76. ⭐ FINAL EXAM-NIGHT MASTER TABLE

If you remember only **one table**, remember this:

| If the question says... | Your brain should immediately say... |
| --- | --- |
| Global users + low latency | **CloudFront** |
| CDN | **CloudFront** |
| Static files globally | **S3 + CloudFront** |
| Reduce origin load | **Caching** |
| Object remains cached for X time | **TTL** |
| Origin says `Cache-Control: max-age` | **Caching/TTL** |
| Old cached content needs immediate removal | **Invalidation** |
| Frequent deployment of static assets | **Versioned filenames** |
| `/images/* → S3`, `/api/* → ALB` | **Cache Behaviors + Multiple Origins** |
| Primary + backup origin | **Origin Group** |
| Many edge locations hitting origin | **Origin Shield** |
| Private S3 + CloudFront | **OAC** |
| Stop direct S3 access | **OAC + S3 bucket policy** |
| Legacy S3 CloudFront access | **OAI** |
| One private file | **Signed URL** |
| Many private files | **Signed Cookie** |
| Encrypt selected sensitive POST fields | **Field-Level Encryption** |
| Force HTTPS | **Viewer Protocol Policy** |
| CloudFront → origin HTTPS | **Origin Protocol Policy** |
| Multiple SSL certificates/domains | **SNI** |
| SSL certificate | **ACM** |
| Add CORS/security/custom response headers | **Response Headers Policy** |
| Inspect/manipulate request at edge | **CloudFront Functions / Lambda@Edge** |
| Simple edge logic | **CloudFront Functions** |
| Complex edge logic/origin events | **Lambda@Edge** |
| SQL injection | **WAF** |
| XSS | **WAF** |
| Rate limiting | **WAF rate-based rule** |
| DDoS | **Shield** |
| Country-based content restriction | **CloudFront Geo Restriction** |
| Reduce CloudFront cost by limiting edge locations | **Price Class** |
| Compress content | **CloudFront compression** |
| Cross-origin browser request | **CORS** |
| Monitor requests/errors | **CloudWatch** |
| Detailed request logging | **CloudFront Logs** |
| Test CloudFront config before production | **Continuous Deployment / Staging** |
| Private ALB/NLB/EC2 behind CloudFront | **VPC Origins** |
| User-Agent affects returned content | **Cache key + edge/application logic** |
| Add a custom response header | **Response Headers Policy** |
| Convert JPEG → WebP | **Application/edge logic, not Response Headers Policy** |

---

# 🧠 10 CloudFront Memory Rules

```
1. CloudFront = CDN
```

```
2. S3 = Store
   CloudFront = Deliver
```

```
3. Cache Hit = Edge serves it
   Cache Miss = Origin is contacted
```

```
4. TTL = How long it stays cached
```

```
5. OAC = Protect S3 origin
```

```
6. Signed URL = One/specific private resource
   Signed Cookie = Many private resources
```

```
7. WAF = Web attacks
   Shield = DDoS
```

```
8. Functions = Simple edge logic
   Lambda@Edge = Advanced edge logic
```

```
9. Origin Group = Failover
   Origin Shield = Reduce origin load
```

```
10. Cache Policy = Cache key + TTL
    Origin Request Policy = What goes to origin
```

### One final mental picture

```
                         USERS
                           │
                           ▼
                    ┌─────────────┐
                    │ CloudFront  │
                    │    CDN      │
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
             WAF         Shield       HTTPS
              │
              ▼
        ┌──────────────┐
        │ Cache Lookup │
        └──────┬───────┘
            HIT│ MISS
               │
               ▼
       ┌──────────────────┐
       │ Cache Behaviors  │
       └────────┬─────────┘
                │
       ┌────────┼───────────┐
       ▼        ▼           ▼
      S3       ALB       API Gateway
       │        │           │
       │       EC2        Lambda
       │
       └─────── OAC ────────┘

        Supporting Features
        ───────────────────
        TTL
        Cache Policy
        Origin Request Policy
        Origin Shield
        Origin Groups
        Signed URLs
        Signed Cookies
        Field-Level Encryption
        CloudFront Functions
        Lambda@Edge
        Geo Restriction
        Response Headers Policy
        Compression
        Logging
        CloudWatch
        VPC Origins
        Continuous Deployment
```

**Current AWS documentation confirms the key modern distinctions used above, including OAC over legacy OAI, VPC origins, NLB origins, cache policies/TTL behavior, private-content controls, and CloudFront continuous deployment**
