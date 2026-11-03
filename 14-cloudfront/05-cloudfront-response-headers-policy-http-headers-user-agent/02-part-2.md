# 20. Lambda@Edge ⭐⭐⭐⭐⭐

**Lambda@Edge** allows you to run AWS Lambda functions at CloudFront edge locations.

It is useful when you need **custom logic** during CloudFront request/response processing.

Example:

```
User
 ↓
CloudFront Edge
 ↓
Lambda@Edge
 ↓
Origin
```

or during response processing:

```
Origin
 ↓
CloudFront
 ↓
Lambda@Edge
 ↓
User
```

---

# 21. What Can Lambda@Edge Do?

Lambda@Edge can perform custom processing such as:

- Modify requests
- Modify responses
- Inspect headers
- Rewrite URLs
- Redirect users
- Add/remove headers
- Implement custom routing logic
- Perform authentication/authorization logic
- Modify request attributes

---

# 22. CloudFront Functions vs Lambda@Edge

This is a **very important SAA-C03 comparison**.

| Feature | CloudFront Functions | Lambda@Edge |
| --- | --- | --- |
| Purpose | Lightweight edge logic | More advanced edge logic |
| Runtime | JavaScript | Node.js / Python |
| Execution | CloudFront edge | CloudFront edge |
| Startup | Extremely fast | More capable but heavier |
| Complexity | Simple | More complex |
| Use cases | Header manipulation, URL rewrites, redirects | Advanced request/response processing |
| Network access | No | More capabilities depending on event/runtime |
| External services | Very limited | More capable |
| Best for | Lightweight transformations | Complex processing |

### Memory

> **CloudFront Functions = lightweight/simple edge logic**
> 

> **Lambda@Edge = more powerful/complex edge logic**
> 

---

# 23. CloudFront Request/Response Processing

CloudFront supports different stages where edge logic can run.

Conceptually:

```
                 CloudFront
                    │
        ┌───────────┴───────────┐
        │                       │
    Viewer Request         Origin Request
        │                       │
        ↓                       ↓
      Origin
        │
        ↓
    Origin Response
        │
        ↓
    Viewer Response
        │
        ↓
       User
```

This gives you opportunities to inspect or modify requests/responses.

---

# 24. Why Would Lambda@Edge Be Used for Image Delivery?

Imagine:

```
User requests:
 /images/cat
```

Different clients may need different representations.

Conceptually:

```
                 /images/cat
                      │
                 CloudFront
                      │
              inspect request
                      │
          ┌───────────┴──────────┐
          ↓                      ↓
       WebP                    JPEG
```

Edge logic can inspect request information and determine which resource/representation should be served.

---

# 25. Important: Lambda@Edge Doesn't Automatically Mean Image Conversion

There are two different concepts:

### A. Choose an existing image

```
image.webp
image.jpg
```

Edge logic chooses which one to serve.

### B. Convert the image

```
JPEG
 ↓
Image processing
 ↓
WebP
```

Actual image conversion may require a separate image-processing architecture.

For example:

```
S3
 ↓
Lambda / image service
 ↓
WebP generated
 ↓
S3
 ↓
CloudFront
```

Don't confuse **routing/selecting** an image with **converting** an image.

---

# 26. Custom Response Header for Tracking

Suppose the company wants:

```
X-Image-Delivery: CloudFront
```

added to responses.

The browser receives:

```
HTTP/1.1 200 OK
Content-Type: image/webp
X-Image-Delivery: CloudFront
```

This is exactly the type of requirement that **response header configuration** addresses.

Possible uses:

- Tracking
- Debugging
- Security headers
- CORS
- Application metadata

---

# 27. CloudFront Cache — Very Important

CloudFront caches responses at edge locations.

Suppose:

```
User A → WebP
User B → JPEG
```

but both request:

```
/images/cat
```

CloudFront must be careful about caching.

Otherwise you could get:

```
User A requests WebP
        ↓
CloudFront caches WebP
        ↓
User B requests JPEG
        ↓
CloudFront returns cached WebP
```

That could be incorrect.

---

# 28. Cache Key Concept

CloudFront uses a **cache key** to determine whether two requests should use the same cached object.

Conceptually:

```
Request
  ↓
Cache Key
  ↓
Cached object
```

If some request attribute changes the required response, that attribute may need to be considered appropriately in the caching/request configuration.

For example:

```
/image
User-Agent A → WebP

/image
User-Agent B → JPEG
```

If the response varies based on `User-Agent`, caching must be designed accordingly.

---

# 29. Why User-Agent Creates a Caching Challenge

Imagine:

```
Request 1
User-Agent: Browser A
        ↓
WebP
        ↓
CloudFront Cache
```

Then:

```
Request 2
User-Agent: Browser B
        ↓
JPEG required
```

If CloudFront considers both requests equivalent for caching, the second request could receive the wrong cached representation.

Therefore:

> **When response content varies based on a request header, caching configuration becomes important.**
> 

This is a major real-world CloudFront consideration.

---

# 30. Scenario: Image Format Selection

A company stores:

```
S3
│
├── image.webp
└── image.jpg
```

CloudFront sits in front:

```
User
 ↓
CloudFront
 ↓
S3
```

A modern browser requests:

```
/image
User-Agent: modern browser
```

Application/edge logic can direct the request toward:

```
image.webp
```

An older client can be directed toward:

```
image.jpg
```

The response can also contain:

```
X-Image-Version: optimized
```

---

# 31. CloudFront + S3 Architecture

A common architecture is:

```
                Users
                  │
                  ▼
             CloudFront
                  │
          ┌───────┴────────┐
          │                │
       Edge Logic       Policies
          │                │
          └───────┬────────┘
                  ▼
               S3
          ┌───────┴───────┐
          │               │
      image.webp      image.jpg
```

This provides:

- Global distribution
- Edge caching
- Reduced latency
- Reduced origin requests
- Ability to apply response headers
- Optional edge processing

---

# 32. Response Headers Policy vs Lambda@Edge

| Requirement | Appropriate Concept |
| --- | --- |
| Add a response header | **Response Headers Policy** |
| Add security response headers | **Response Headers Policy** |
| Configure CORS response headers | **Response Headers Policy** |
| Add custom response metadata | **Response Headers Policy** |
| Inspect request headers | **CloudFront Function / Lambda@Edge**, depending on complexity |
| Implement custom request logic | **CloudFront Function / Lambda@Edge** |
| Rewrite URI | **CloudFront Function / Lambda@Edge** |
| Complex edge processing | **Lambda@Edge** |
| Choose different content based on request | **Edge logic + appropriate caching design** |
| Actually convert JPEG → WebP | **Image-processing solution**, not merely Response Headers Policy |

---

# 33. CloudFront Response Headers Policy vs Cache Policy

These two are commonly confused.

|  | **Response Headers Policy** | **Cache Policy** |
| --- | --- | --- |
| Main purpose | Control response headers | Control caching |
| Works primarily with | Response | Request/cache key |
| Add custom headers | ✅ | ❌ |
| Security headers | ✅ | ❌ |
| CORS headers | ✅ | ❌ |
| Determine what contributes to cache key | ❌ | ✅ |
| Control TTL | ❌ | ✅ |
| Query strings in cache key | ❌ | ✅ |
| Headers in cache key | ❌ | ✅ |
| Cookies in cache key | ❌ | ✅ |

### Memory

> **Response Headers Policy = "What headers does the viewer receive?"**
> 

> **Cache Policy = "What makes this response a different cached object?"**
> 

---

# 34. CloudFront Behavior vs Policy

| Concept | Think |
| --- | --- |
| **Distribution** | Overall CloudFront configuration |
| **Behavior** | Rules for a particular path |
| **Cache Policy** | How CloudFront caches |
| **Origin Request Policy** | What CloudFront sends to origin |
| **Response Headers Policy** | What response headers viewer receives |
| **CloudFront Function** | Lightweight edge code |
| **Lambda@Edge** | Advanced edge code |

---

# 35. Origin Request Policy

Another important concept.

CloudFront may receive:

```
User-Agent: Chrome
```

but that doesn't automatically mean the header is forwarded to the origin in every configuration.

An **Origin Request Policy** controls which additional request information CloudFront sends to the origin.

Examples:

- Headers
- Cookies
- Query strings

Conceptually:

```
User
 │
 │ User-Agent
 ▼
CloudFront
 │
 │ Origin Request Policy
 │
 ├── forward selected headers
 ├── forward selected cookies
 └── forward selected query strings
 ▼
Origin
```

---

# 36. Three Policies You Must Separate

| Policy | Main Question |
| --- | --- |
| **Cache Policy** | What makes requests different in the CloudFront cache? |
| **Origin Request Policy** | What request information should be forwarded to origin? |
| **Response Headers Policy** | What headers should be returned to the viewer? |

### 🧠 Easy memory

```
CACHE
"What do I cache separately?"

ORIGIN REQUEST
"What do I send to my origin?"

RESPONSE HEADERS
"What do I send back to my user?"
```

---

# 37. Complete Architecture Example

### Requirement

An image website has:

```
100,000 users
```

Images are stored in S3.

The company wants:

- Global low-latency delivery
- WebP for supported clients
- JPEG fallback
- Custom response header
- Low operational overhead

Architecture concept:

```
                    Users
                      │
                      ▼
                 CloudFront
                      │
          ┌───────────┴───────────┐
          │                       │
      Edge logic          Response Headers
          │                    Policy
          │                       │
          ▼                       │
     Select format                │
          │                       │
       ┌──┴───┐                   │
       ▼      ▼                   │
     WebP    JPEG                 │
       │      │                   │
       └──┬───┘                   │
          ▼                       │
          S3                      │
          │                       │
          └──────────┬────────────┘
                     ▼
                   User
```

---

# 38. Important SAA-C03 Concepts From This Question

| Concept | What you should remember |
| --- | --- |
| **CloudFront** | CDN for low-latency global content delivery |
| **HTTP Request** | Client → server |
| **HTTP Response** | Server → client |
| **HTTP Header** | Metadata about request/response |
| **User-Agent** | Request header describing client/browser information |
| **Accept** | Request header indicating acceptable content/media types |
| **WebP** | Efficient image format |
| **JPEG** | Common image format/fallback |
| **Response Headers Policy** | Configure response headers at CloudFront |
| **CloudFront Behavior** | Controls handling for matching URL paths |
| **Cache Policy** | Controls CloudFront cache key/TTL behavior |
| **Origin Request Policy** | Controls information forwarded to origin |
| **Lambda@Edge** | Advanced custom logic at CloudFront edge |
| **CloudFront Functions** | Lightweight, fast edge logic |
| **Content Negotiation** | Choose representation based on client/request |
| **Cache variation** | Important when different requests need different responses |

---

# 39. Exam Confusion Table

| If you see... | Think... |
| --- | --- |
| **Add security headers** | Response Headers Policy |
| **Add CORS headers** | Response Headers Policy |
| **Add custom response header** | Response Headers Policy |
| **Inspect User-Agent and perform logic** | Edge function |
| **Rewrite URL at edge** | CloudFront Function / Lambda@Edge |
| **Complex edge processing** | Lambda@Edge |
| **Simple lightweight edge manipulation** | CloudFront Functions |
| **Control CloudFront caching** | Cache Policy |
| **Control what goes to origin** | Origin Request Policy |
| **Different content based on request information** | Edge logic + caching considerations |
| **Global static content delivery** | CloudFront |
| **Store images/files** | S3 |
| **Actually transform image format** | Image-processing solution |

---

# 🧠 Final Mental Model

Think of CloudFront as a **smart delivery layer**:

```
                         INTERNET
                            │
                            ▼
                         USER
                            │
                     HTTP REQUEST
                            │
                     User-Agent
                     Accept Header
                            │
                            ▼
                    ┌──────────────┐
                    │  CloudFront  │
                    └──────────────┘
                       │    │    │
             ┌─────────┘    │    └──────────┐
             ▼              ▼               ▼
        Cache Policy   Edge Logic    Response Headers
             │              │               │
             │              │               │
             ▼              ▼               ▼
        Cache decision   Custom logic   Add/modify
                                        response headers
                            │
                            ▼
                          Origin
                         S3 / ALB
                            │
                            ▼
                        RESPONSE
                            │
                    Response Headers
                            │
                            ▼
                          USER
```

### ⭐ The key separation to memorize

> **User-Agent = information coming FROM the client.**
> 

> **Response Headers Policy = controls headers going TO the client.**
> 

> **Cache Policy = controls how CloudFront caches the response.**
> 

> **Origin Request Policy = controls what CloudFront forwards TO the origin.**
> 

> **CloudFront Functions = lightweight edge code.**
> 

> **Lambda@Edge = more advanced edge processing.**
> 

> **CloudFront = global content delivery and caching layer.**
>
