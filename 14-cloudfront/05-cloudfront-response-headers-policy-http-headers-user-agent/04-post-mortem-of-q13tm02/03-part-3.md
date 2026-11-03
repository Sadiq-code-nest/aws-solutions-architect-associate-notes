# 7. Clue-by-Clue Post-Mortem

Let's read the original question like an exam solver.

---

### Clue 1

> **"media company"**
> 

Not a technical answer by itself.

But media companies commonly have:

```
images
videos
large global audiences
CDNs
```

So CloudFront makes sense.

---

### Clue 2

> **"images it delivers through Amazon CloudFront"**
> 

Immediate trigger:

```
CloudFront
=
CDN
=
edge delivery
```

We should look for a solution that works **inside/around CloudFront**, rather than building another delivery architecture.

---

### Clue 3

> **"compatible across various user devices"**
> 

Trigger:

```
Content adaptation
```

The response needs to vary depending on the client.

---

### Clue 4

> **"serve images in WebP format to user agents that support it"**
> 

Very important.

We need:

```
Inspect request
      ↓
User-Agent
      ↓
Determine format
```

This is **request-processing logic**, not simply a response-header configuration.

---

### Clue 5

> **"return to JPEG format for those that don’t"**
> 

This confirms that the system needs conditional logic:

```
IF WebP supported
      ↓
   WebP

ELSE
      ↓
   JPEG
```

That eliminates Option A.

A Response Headers Policy doesn't provide this conditional object-selection logic.

---

### Clue 6

> **"add a custom header to the response"**
> 

Now we have a second requirement:

```
Response
   +
Custom header
```

Possible mechanisms:

```
Response Headers Policy
Lambda@Edge
```

But we still need the first requirement.

---

### Clue 7

> **"while minimizing operational overhead"**
> 

This is a huge exam clue.

Eliminate:

```
EC2 infrastructure ❌
Multiple distributions ❌
Complex routing architecture ❌
```

Prefer:

```
Serverless edge compute
```

---

### Clue 8

> **"Configure CloudFront behaviors"**
> 

This keeps the solution inside CloudFront rather than introducing multiple independent delivery systems.

---

### Clue 9

> **"Use Lambda@Edge functions"**
> 

Now we have:

```
CloudFront
+
custom edge logic
```

Exactly what the scenario requires.

---

### Clue 10

> **"modify the response headers"**
> 

Lambda@Edge can participate in CloudFront's request/response lifecycle and modify the response.

---

### Clue 11

> **"serve the appropriate format based on the User-Agent header"**
> 

This is the final confirmation.

```
User-Agent
    ↓
Lambda@Edge
    ↓
WebP/JPEG
```

Therefore:

# ✅ Option D

---

# 8. Why the Tempting Wrong Answers Are Tempting

## Distractor #1 — Option A

This is probably the **most dangerous distractor**.

Why?

Because the question explicitly says:

> "add a custom header"
> 

And Option A says:

> "CloudFront response headers policy"
> 

That's absolutely relevant.

The mistake is solving **only half the question**.

### Rushed thinking

```
Question says "custom header"
        ↓
Response Headers Policy
        ↓
DONE!
```

### Correct thinking

```
Question has TWO requirements

1. Dynamic image format
        ↓
Edge logic

2. Custom response header
        ↓
Response manipulation

Both together
        ↓
Lambda@Edge
```

### Memory trick

> **Headers Policy = configure headers. Lambda@Edge = execute logic.**
> 

---

## Distractor #2 — Option B

This is tempting because Route 53 is a powerful routing service.

But ask:

> **What layer does Route 53 operate at?**
> 

```
Route 53
   ↓
DNS
```

The requirement is:

```
HTTP User-Agent
   ↓
Choose image
```

That's an **HTTP/application-level decision**, not normal DNS routing.

And multiple CloudFront distributions violate:

> **"minimizing operational overhead."**
> 

---

# 9. Final Answer Recap + One-Line Memory Trick

## ✅ Correct Answer: Option D

> **Configure CloudFront behaviors to handle different image formats based on the User-Agent header. Use Lambda@Edge functions to modify the response headers and serve the appropriate format.**
> 

### The architecture

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
                  ┌─────┴─────┐
                  │           │
             WebP supported?  No
                  │           │
                  ▼           ▼
              WebP          JPEG
                  │           │
                  └─────┬─────┘
                        ▼
                  Custom Header
                        │
                        ▼
                       User
```

### 🧠 Memory Trick #1

> **"Need custom logic at CloudFront's edge → Lambda@Edge."**
> 

### 🧠 Memory Trick #2

> **"Response Headers Policy configures headers; Lambda@Edge makes decisions."**
> 

---

# 10. Bonus: Related Exam Traps to Watch For

These look similar but can have different answers.

### Trap 1 — Only adding a static custom header

> "Add `X-Company: ABC` to every CloudFront response."
> 

Think:

```
CloudFront Response Headers Policy
```

You don't need Lambda@Edge.

---

### Trap 2 — Simple URL/header manipulation

> "Rewrite `/old/*` to `/new/*` at the CloudFront viewer request with very lightweight logic."
> 

Think:

```
CloudFront Functions
```

Potentially preferable to Lambda@Edge because it is designed for lightweight, high-scale edge transformations.

---

### Trap 3 — Route users to the nearest AWS region

> "Send European users to Europe and Asian users to Asia."
> 

Think:

```
Route 53
+
Geolocation / latency routing
```

Not Lambda@Edge.

---

### Trap 4 — Dynamically resize/transform images

> "Resize images based on requested dimensions and generate transformed versions."
> 

Depending on the architecture, think:

```
CloudFront
+
Lambda / image processing service
+
S3
```

or an image transformation architecture—not merely a response headers policy.

---

### Trap 5 — Cache different versions based on request information

> "CloudFront should cache different responses depending on a request header/query string/cookie."
> 

Think:

```
CloudFront Cache Policy
+
appropriate cache key
```

Don't forget that **varying the response based on request attributes has caching implications**.

---

# 🔥 FINAL FLASHCARD — MASTER COMPARISON

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **Global delivery of static content** | **CloudFront** |
| **CDN / edge caching** | **CloudFront** |
| **Different URL paths need different behavior** | **CloudFront Behaviors** |
| **Add/remove/modify response headers without code** | **CloudFront Response Headers Policy** |
| **Execute custom logic at CloudFront edge** | **Lambda@Edge** |
| **Very lightweight viewer request/response manipulation** | **CloudFront Functions** |
| **Inspect User-Agent and make a custom request decision** | **Edge compute** |
| **Static custom response header** | **Response Headers Policy** |
| **Dynamic response modification** | **Lambda@Edge** |
| **DNS-based routing** | **Route 53** |
| **Latency/geography/weight/health routing** | **Route 53 routing policies** |
| **Server-based image conversion** | **EC2** |
| **Avoid managing servers** | **Lambda / serverless** |
| **Modern optimized web image format** | **WebP** |
| **Broad compatibility image fallback** | **JPEG** |
| **Global custom request/response processing** | **Lambda@Edge** |
| **Simple edge rewrite/header logic** | **CloudFront Functions** |
| **Different cached content based on request attributes** | **CloudFront Cache Policy / cache key** |
| **"Configure headers, no custom code"** | **Response Headers Policy** |
| **"Inspect request → make decision → modify request/response"** | **Lambda@Edge** |
| **"DNS should decide where request goes"** | **Route 53** |
| **"Minimize operational overhead; don't manage servers"** | **Serverless / edge functions** |

### ⭐ The 3-line SAA mental model

```
Need to CONFIGURE response headers?
        → Response Headers Policy

Need SIMPLE edge manipulation?
        → CloudFront Functions

Need CUSTOM / MORE COMPLEX CloudFront edge logic?
        → Lambda@Edge
```

And for this question:

```
User-Agent
    ↓
Choose WebP/JPEG
    +
Modify response header
    +
Low operational overhead
    ↓
✅ Lambda@Edge
```
