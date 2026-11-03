# AWS SAA-C03 Post-Mortem — Question 13

## 1. Restate the Scenario in Plain English

### What is the company trying to do?

A media company delivers images through **Amazon CloudFront**.

They have two versions of an image:

```
photo.jpg
photo.webp
```

They want CloudFront to make the decision automatically:

- If the user's browser/device supports **WebP** → return WebP.
- Otherwise → return JPEG.
- They also want to add a **custom response header** for tracking.
- They want the solution with **minimum operational overhead**.

So the core problem is:

```
                    User
                     │
                     │ User-Agent
                     ▼
              ┌──────────────┐
              │  CloudFront  │
              └──────┬───────┘
                     │
             Does browser
             support WebP?
               /          \
             YES           NO
              │             │
              ▼             ▼
          image.webp    image.jpg
```

And the response should additionally contain something like:

```
X-Image-Format: webp
```

or:

```
X-Image-Format: jpeg
```

---

### Actors

| Actor / Component | Role |
| --- | --- |
| User/device | Requests an image |
| User-Agent header | Provides information about the requesting browser/device |
| CloudFront | Global CDN delivering the image |
| WebP image | Preferred format for compatible clients |
| JPEG image | Fallback format |
| Custom response header | Tracking information |
| Lambda@Edge | Logic executed close to CloudFront viewers |

---

### Current architecture

```
User
 │
 │ GET /image
 │ User-Agent: Browser XYZ
 ▼
CloudFront
 │
 ▼
Origin
 │
 ├── image.webp
 └── image.jpg
```

### Desired architecture

```
                       User
                         │
                         │ User-Agent
                         ▼
                ┌─────────────────┐
                │   CloudFront    │
                │                 │
                │ Lambda@Edge     │
                │     logic       │
                └────────┬────────┘
                         │
                ┌────────┴────────┐
                │                 │
          Supports WebP?      Doesn't?
                │                 │
                ▼                 ▼
          image.webp         image.jpg
                │                 │
                └────────┬────────┘
                         ▼
                       User
                  + custom header
```

---

# 2. Identify the Hidden Sub-Problems

This question actually has **two independent requirements**.

| Symptom | Underlying Category | Why |
| --- | --- | --- |
| Different image format depending on `User-Agent` | **Edge request processing / dynamic content selection** | Need logic that examines the incoming request |
| WebP for supported browsers | **Viewer-request logic / URI or origin selection** | Need to choose the WebP object |
| JPEG for unsupported browsers | **Fallback routing/rewriting** | Need an alternative object |
| Add custom response header | **Edge response manipulation** | Need to modify the HTTP response |
| Minimize operational overhead | **Serverless edge computing** | Avoid managing EC2 servers or multiple distributions |

### The key conceptual split

There are actually two phases:

```
REQUEST
   │
   │ User-Agent
   ▼
Choose WebP or JPEG
   │
   ▼
Fetch appropriate object
   │
   ▼
RESPONSE
   │
   ▼
Add custom header
```

That is why **edge compute** is the right category.

---

# 3. Explain Every Option in Plain Language

---

## Option A

> Generate a CloudFront response headers policy. Utilize the policy to deliver the suitable image format according to the User-Agent HTTP header in the incoming request.
> 

### What is this trying to say?

It says:

> "Use a CloudFront Response Headers Policy to inspect the browser's User-Agent and decide whether to return WebP or JPEG."
> 

Architecture:

```
User
 │
 │ User-Agent
 ▼
CloudFront
 │
 ▼
Response Headers Policy
 │
 ├── WebP?
 └── JPEG?
```

### Verdict: ❌ Incorrect

The **Response Headers Policy** can modify/add/remove HTTP response headers.

It does **not execute arbitrary logic to choose which image object should be served based on an incoming request header**.

Think:

```
Response Headers Policy
        ↓
"What headers should I put in the response?"
```

Not:

```
Response Headers Policy
        ↓
"Which object should I fetch?"
```

### What it CAN do

For example:

```
Response:
HTTP/2 200
Content-Type: image/webp
X-Tracking-ID: abc123
Cache-Control: ...
```

The policy can help configure headers.

### What it CANNOT do

```
User-Agent
    │
    ├── WebP browser → image.webp
    └── old browser  → image.jpg
```

That requires **request-processing logic**.

### Why this is a tempting answer

The question explicitly says:

> "add a custom header"
> 

and the option says:

> "response headers policy"
> 

That part sounds perfect.

But the question has **two requirements**, not one:

```
1. Choose image format       → ❌ Response Headers Policy can't do this
2. Add custom response header → ✅ Response Headers Policy can help
```

Because it cannot satisfy #1, the whole option fails.

---

# Option B

> Create multiple CloudFront distributions, each serving a specific image format. Route incoming requests based on the User-Agent header to the respective distribution using Amazon Route 53.
> 

### What is this saying?

Create:

```
Distribution #1 → WebP
Distribution #2 → JPEG
```

Then use Route 53 to decide which CloudFront distribution receives the request.

Architecture:

```
                       User
                         │
                    User-Agent
                         │
                         ▼
                    Route 53
                    /       \
                   /         \
                  ▼           ▼
          CloudFront #1   CloudFront #2
             WebP            JPEG
```

### Verdict: ❌ Incorrect

This is **far too complicated**.

The requirement is a simple request-level decision based on:

```
User-Agent
```

Creating multiple CloudFront distributions introduces unnecessary:

- infrastructure
- configuration
- management
- deployment complexity
- operational overhead

And there's another important problem:

### Route 53 isn't the right tool for this kind of HTTP-header decision

Route 53 routing policies primarily route DNS queries based on things such as:

- latency
- geography
- health
- weighted percentages
- IP information

It is not an HTTP application-layer router that normally examines the browser's `User-Agent` header and sends the request to different CloudFront distributions.

### Better architecture

Instead of:

```
User
 ↓
Route 53
 ↓
CloudFront A/B
```

do:

```
User
 ↓
CloudFront
 ↓
Lambda@Edge
 ↓
WebP/JPEG
```

### Why it's particularly bad for this question

The question explicitly says:

> **"minimizing operational overhead"**
> 

Multiple distributions do the opposite.

---

# Option C

> Implement an image conversion service on Amazon EC2 instances and integrate it with CloudFront. Use AWS Lambda functions to modify the response headers and serve the appropriate format based on the User-Agent header.
> 

### What is this saying?

Build your own image-processing infrastructure:

```
User
 │
 ▼
CloudFront
 │
 ▼
EC2 image conversion service
 │
 ├── WebP
 └── JPEG
```

And use Lambda for response manipulation.

### Verdict: ❌ Incorrect

Technically, you could build something like this.

But it violates the most important architectural preference:

> **minimize operational overhead**
> 

Now you have to manage:

```
EC2
├── instances
├── AMIs
├── patching
├── Auto Scaling
├── monitoring
├── security groups
└── image conversion software
```

That's unnecessary for a simple edge-routing/transformation requirement.

### Compare

Option C:

```
CloudFront
    │
    ▼
EC2
    │
    ▼
Image processing
```

Option D:

```
CloudFront
    │
    ▼
Lambda@Edge
    │
    ▼
WebP/JPEG
```

The second is much more serverless and operationally lightweight.

### Another issue

The option says:

> "AWS Lambda functions"
> 

This is ambiguous because **standard Lambda** and **Lambda@Edge** are not the same deployment model.

For logic that must execute at CloudFront edge locations as part of the CloudFront request/response lifecycle, **Lambda@Edge** is the relevant feature.

---

# Option D — Correct

> Configure CloudFront behaviors to handle different image formats based on the User-Agent header. Use Lambda@Edge functions to modify the response headers and serve the appropriate format.
> 

### What is this saying?

Use CloudFront's edge-processing capability to inspect the request and choose the appropriate image.

Architecture:

```
                     User
                       │
                       │ User-Agent
                       ▼
              ┌─────────────────┐
              │   CloudFront    │
              │                 │
              │  Lambda@Edge    │
              └────────┬────────┘
                       │
                ┌──────┴──────┐
                │             │
             WebP?           No
                │             │
                ▼             ▼
          image.webp      image.jpg
                │             │
                └──────┬──────┘
                       ▼
                     User
                       │
               + custom header
```

### Verdict: ✅ Correct

It directly addresses both requirements.

### Requirement #1

> Serve WebP to compatible user agents.
> 

Lambda@Edge can inspect request information such as:

```
User-Agent
```

and use that information to determine which resource should be requested.

Conceptually:

```
User-Agent
    │
    ├── Supports WebP → /images/cat.webp
    │
    └── Doesn't → /images/cat.jpg
```

### Requirement #2

> Add custom response header.
> 

Lambda@Edge can modify the response at the appropriate CloudFront event.

Conceptually:

```
Origin
  │
  ▼
CloudFront
  │
  ▼
Lambda@Edge
  │
  └── Add X-Tracking-Header
  │
  ▼
User
```

### Requirement #3

> Minimize operational overhead.
> 

Lambda@Edge is serverless.

No:

```
EC2
patching
OS management
Auto Scaling groups
```

are required.

### Therefore

```
Dynamic request decision
        +
Response modification
        +
Global edge execution
        +
No servers
        ↓
   Lambda@Edge
```

---

# 4. Deep-Dive Every AWS Concept Touched by the Question

---

## 4.1 Amazon CloudFront

### Definition

Amazon CloudFront is AWS's CDN. It caches and delivers content through globally distributed edge locations, reducing latency for users.

```
                 Origin
                   │
                   ▼
             ┌───────────┐
             │ CloudFront│
             │   CDN     │
             └─────┬─────┘
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
      User A     User B     User C
```

### Example

Without CloudFront:

```
Bangladesh User ───────────────► US Origin
```

With CloudFront:

```
Bangladesh User
      │
      ▼
Nearby CloudFront Edge
      │
      ▼
Origin only when needed
```

### CloudFront vs S3

| CloudFront | S3 |
| --- | --- |
| CDN | Object storage |
| Caches/delivers content | Stores objects |
| Global edge locations | Regional buckets |
| Reduces latency | Durable storage |

### Exam trigger

> **"Global users + static content + lower latency" → CloudFront**
> 

---

# 4.2 CloudFront Behavior

### Definition

A CloudFront behavior defines how CloudFront handles requests matching a particular path pattern.

Example:

```
/images/*
    ↓
CloudFront Behavior
    ↓
Image origin
```

Another:

```
/api/*
    ↓
API origin
```

### Architecture

```
                 CloudFront
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       /images/*   /video/*   /api/*
          │          │          │
          ▼          ▼          ▼
       S3 image    S3 video    API
```

### Example

```
/images/*
```

could use an image-specific origin and caching policy.

### CloudFront behavior vs Route 53 routing

| CloudFront Behavior | Route 53 Routing |
| --- | --- |
| HTTP request handling | DNS-level routing |
| Path patterns | DNS queries |
| Headers/cookies/query strings | DNS routing policies |
| CDN behavior | Determines endpoint/IP response |

### Exam trigger

> **"Different URL paths need different CloudFront behavior/origins" → CloudFront behaviors**
> 

---

# 4.3 CloudFront Response Headers Policy

### Definition

A Response Headers Policy allows CloudFront to add, remove, or configure HTTP response headers without requiring application code.

Example:

```
CloudFront
    │
    ▼
Response Headers Policy
    │
    ├── Cache-Control
    ├── CORS headers
    ├── Security headers
    └── Custom headers
```

### Example

Add:

```
X-Company-ID: Media123
```

to every response.

### Critical limitation

It doesn't perform arbitrary request-based application logic.

So:

```
User-Agent → choose image
```

is **not its job**.

### Response Headers Policy vs Lambda@Edge

| Response Headers Policy | Lambda@Edge |
| --- | --- |
| Declarative header configuration | Programmable edge logic |
| Add/remove/modify headers | Can inspect/process requests/responses |
| Simple | More flexible |
| Less operational complexity | More powerful |
| No arbitrary application logic | Custom code |

### Exam trigger

> **"Add/modify HTTP response headers without code" → Response Headers Policy**
> 

---

# 4.4 User-Agent HTTP Header

### Definition

The `User-Agent` request header identifies information about the client software making the HTTP request.

Simplified:

```
GET /image HTTP/1.1
Host: example.com
User-Agent: Browser XYZ
```

An application/edge function can inspect it.

### Example

Conceptually:

```
User-Agent
    │
    ├── Browser supports WebP
    │       ↓
    │     WebP
    │
    └── Browser doesn't
            ↓
          JPEG
```

### User-Agent vs Accept header

This is an important real-world distinction.

| User-Agent | Accept |
| --- | --- |
| Identifies client/browser | Declares acceptable content formats |
| Browser/device information | Content negotiation |
| Example: Chrome/Firefox | Example: image/webp |
| Can be used for compatibility logic | Often better for format negotiation |

**Exam note:** This question explicitly says to use `User-Agent`, so follow the scenario.

### Exam trigger

> **"Based on browser/client information" → inspect request headers such as User-Agent**
>
