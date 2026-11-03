# 4.5 Lambda@Edge

### Definition

Lambda@Edge allows Lambda functions to run at CloudFront edge locations in response to CloudFront request/response events.

Conceptually:

```
User
 │
 ▼
CloudFront Edge
 │
 ├── Lambda@Edge
 │
 ▼
Origin
```

It allows you to customize CloudFront behavior without managing servers.

### CloudFront request/response lifecycle

Simplified:

```
                 Viewer Request
                       │
                       ▼
                ┌─────────────┐
                │ CloudFront  │
                └──────┬──────┘
                       │
                 Origin Request
                       │
                       ▼
                    Origin
                       │
                 Origin Response
                       │
                       ▼
                 Viewer Response
                       │
                       ▼
                     User
```

Lambda@Edge can execute at these lifecycle stages.

### Example

A viewer requests:

```
/images/cat
```

Lambda@Edge examines:

```
User-Agent
```

and can modify the request to:

```
/images/cat.webp
```

for compatible clients.

### Lambda@Edge vs CloudFront Functions

This is **very important for SAA-C03**.

| Lambda@Edge | CloudFront Functions |
| --- | --- |
| More powerful | Lightweight |
| JavaScript/Node.js/Python support depending on runtime capabilities | JavaScript |
| Viewer request/response and origin request/response events | Viewer request/response events |
| More complex logic | Very lightweight transformations |
| Can access more AWS/Lambda capabilities | Restricted execution environment |
| Higher complexity | Extremely low latency |
| More appropriate for richer edge logic | Best for simple transformations |

### Critical exam distinction

If the question says:

> **"simple URL rewrite / header manipulation at viewer request/response"**
> 

CloudFront Functions may be the better fit.

If it needs:

> **more sophisticated edge processing / origin request or response processing**
> 

think Lambda@Edge.

For **this question's answer choices**, Lambda@Edge is the intended solution.

### Exam trigger

> **"Run custom Lambda logic at CloudFront edge locations" → Lambda@Edge**
> 

---

# 4.6 AWS Lambda

### Definition

AWS Lambda is a serverless compute service that runs code without requiring you to manage servers.

```
Event
  │
  ▼
Lambda
  │
  ▼
Result
```

### Example

```
S3 Object Created
       │
       ▼
     Lambda
       │
       ▼
Process image
```

### Standard Lambda vs Lambda@Edge

| Lambda | Lambda@Edge |
| --- | --- |
| Runs in AWS Lambda infrastructure/region | Runs as part of CloudFront edge processing |
| Event-driven compute | CDN edge customization |
| General serverless applications | CloudFront request/response processing |
| Regional architecture | Global edge architecture |

### Exam trigger

> **"Serverless code execution" → Lambda**
> 

> **"Serverless code executing as part of CloudFront edge request/response" → Lambda@Edge**
> 

---

# 4.7 Amazon Route 53

### Definition

Route 53 is AWS's highly available DNS service. It can route DNS queries using policies such as:

- Simple
- Weighted
- Latency
- Failover
- Geolocation
- Geoproximity
- IP-based

### Architecture

```
User
 │
 ▼
Route 53
 │
 ├── Endpoint A
 ├── Endpoint B
 └── Endpoint C
```

### Example

If users should be directed to the AWS region with the lowest network latency:

```
User
 ↓
Route 53
 ↓
Latency-based routing
```

### Route 53 vs CloudFront

| Route 53 | CloudFront |
| --- | --- |
| DNS | CDN |
| Resolves domain names | Delivers/caches content |
| DNS routing policies | HTTP content behavior |
| Before connection | Handles HTTP delivery |

### Exam trigger

> **"DNS routing based on geography/latency/health/weight" → Route 53**
> 

---

# 4.8 Amazon EC2

### Definition

EC2 provides virtual servers that you manage in AWS.

```
User
 │
 ▼
EC2
 │
 └── Application
```

You are responsible for much more infrastructure management than with Lambda:

- OS
- patching
- capacity
- scaling
- instance configuration
- security hardening

### EC2 vs Lambda

| EC2 | Lambda |
| --- | --- |
| Virtual server | Serverless functions |
| You manage OS | AWS manages infrastructure |
| Long-running workloads | Event/function execution |
| More operational overhead | Lower operational overhead |
| Manual/Auto Scaling | Automatic service-managed scaling |

### Exam trigger

> **"Avoid server management / serverless" → Lambda**
> 

---

# 4.9 WebP

### Definition

WebP is a modern image format designed to provide efficient image compression, often producing smaller files than JPEG/PNG at comparable visual quality.

Conceptually:

```
JPEG
  │
  │ larger
  ▼
Network
  │
  ▼
Browser

WebP
  │
  │ often smaller
  ▼
Network
  │
  ▼
Browser
```

### Example

For a website with millions of image requests:

```
JPEG = 200 KB
WebP = 120 KB
```

WebP can reduce bandwidth usage when supported.

### WebP vs JPEG

| WebP | JPEG |
| --- | --- |
| Modern image format | Older, widely supported format |
| Often smaller | Often larger |
| Good for web delivery | Broad compatibility |
| Browser support required | Very broad support |

### Exam trigger

> **"Modern optimized image format for web" → WebP**
> 

---

# 4.10 JPEG

### Definition

JPEG is a widely supported lossy image format commonly used for photographs.

Its main advantage in this scenario is:

> **broad compatibility**
> 

Therefore:

```
WebP supported?
     │
 YES ──► WebP
 NO  ──► JPEG
```

### Exam trigger

> **"Fallback image format for broad compatibility" → JPEG**
> 

---

# 4.11 CloudFront Cache Behavior and Headers

There's an important hidden issue here.

If CloudFront serves different objects based on a request header such as:

```
User-Agent
```

then the caching strategy must account for the fact that the response can vary based on that request characteristic.

Conceptually:

```
User-Agent A ──► image.webp
User-Agent B ──► image.jpg
```

If the cache doesn't distinguish appropriately, one cached representation could potentially be served to the wrong request.

### Exam takeaway

When a question says:

> **"response varies based on request header/cookie/query string"**
> 

think about:

```
Cache key
```

and whether CloudFront needs to consider that request attribute.

This question's main answer is still **Lambda@Edge**, but the caching implication is an important secondary concept.

---

# 5. Master Comparison Table

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| Global delivery of static content | **CloudFront** |
| CDN / edge caching | **CloudFront** |
| Different URL paths need different behavior | **CloudFront Behaviors** |
| Add/remove/modify response headers without code | **CloudFront Response Headers Policy** |
| Execute custom logic at CloudFront edge | **Lambda@Edge** |
| Very lightweight viewer request/response manipulation | **CloudFront Functions** |
| Inspect `User-Agent` and make a custom request decision | **Edge compute — Lambda@Edge/CloudFront Functions depending on complexity** |
| Add custom response header with simple static configuration | **Response Headers Policy** |
| Dynamic response modification | **Lambda@Edge** |
| DNS-based routing | **Route 53** |
| Route based on latency/geography/weight/health | **Route 53 routing policies** |
| Server-based image conversion | **EC2** |
| Avoid managing servers | **Lambda / serverless** |
| Modern compressed web image | **WebP** |
| Broadly compatible image fallback | **JPEG** |
| Global edge request processing | **Lambda@Edge** |
| Simple edge rewrite/header logic | **CloudFront Functions** |
| Different cached content based on request characteristics | **CloudFront cache key / cache policy** |

---

# 6. Side-by-Side Option Analysis Table

| Option | Underlying Idea | Good? | One-line reason |
| --- | --- | --- | --- |
| **A** | Response Headers Policy chooses image format | ❌ | Response Headers Policy manages response headers; it doesn't perform the required dynamic object selection |
| **B** | Multiple CloudFront distributions + Route 53 | ❌ | Excessive architecture and Route 53 isn't an HTTP User-Agent router |
| **C** | EC2 image conversion + Lambda | ❌ | Works in principle but introduces unnecessary servers and operational overhead |
| **D** | CloudFront + Lambda@Edge | ✅ | Performs edge logic based on the request and can modify the response while avoiding server management |
