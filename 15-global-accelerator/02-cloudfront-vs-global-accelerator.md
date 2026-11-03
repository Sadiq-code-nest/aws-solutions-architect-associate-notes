# CloudFront Vs Global Accelerator

This is one of the most confusing AWS topics. The easiest way to understand it is this:

> **CloudFront = Delivers Content Faster**
> 
> 
> **Global Accelerator = Delivers Traffic Faster**
> 

Everything else becomes easy once you remember these two lines.

---

# First Understand the Problem

Suppose your application is hosted in **Singapore (ap-southeast-1)**.

Users access it from:

- Bangladesh
- USA
- Germany
- Australia

Without any AWS optimization:

```
User
   │
Internet (random ISP routes)
   │
Singapore
```

The request travels through many ISPs.

Problems:

- Slow
- Packet loss
- Congestion
- High latency

AWS has two different services to solve two different problems.

---

# CloudFront

CloudFront is a CDN (Content Delivery Network).

Purpose:

> Cache content close to users.
> 

Instead of downloading images from Singapore every time, users download them from a nearby AWS Edge Location.

Example:

Website:

```
www.shop.com
```

Contains:

- logo.png
- CSS
- JS
- Product images

User in Bangladesh

Without CloudFront

```
Bangladesh
      │
Internet
      │
Singapore S3
```

Every image comes from Singapore.

Slow.

With CloudFront

```
Bangladesh User
       │
Dhaka Edge Location
       │
(Cache Hit)
```

Image comes from nearby edge.

Very fast.

---

## CloudFront stores

- Images
- Videos
- CSS
- JS
- HTML (optional)
- Downloads

Basically

Static content.

---

# CloudFront Request Flow

Let's see step by step.

Website:

```
www.netflix.com
```

Image:

```
movie.jpg
```

Origin:

```
S3 Bucket
```

---

## First Request

```
User
   │
Nearest Edge Location
   │
Cache Miss
   │
S3 Bucket
   │
Returns Image
   │
Edge caches image
   │
User gets image
```

---

## Second User

Another person requests same image.

```
User
   │
Nearest Edge
   │
Cache Hit
   │
Image Returned
```

No request goes to S3.

Very fast.

---

## Cache Hit

```
Edge already has image.

No origin call.

Fast.
```

---

## Cache Miss

```
Edge doesn't have image.

Fetch from origin.

Store.

Return.
```

---

# CloudFront Architecture

```
          User

            │

   Nearest Edge Location

      Cache Hit?

      Yes ↓
      Return

      No ↓

   Origin Server

    S3
    EC2
    ALB
    API Gateway

      │

Edge stores copy

      │

Return User
```

---

---

# What is Global Accelerator?

Global Accelerator is NOT a cache.

It NEVER stores files.

Instead it improves

Routing.

---

Imagine roads.

Without Global Accelerator

```
Dhaka

   │

Many ISP Roads

   │

Singapore
```

Traffic jam.

---

With Global Accelerator

```
Dhaka

     │

Nearest AWS Edge

     │

AWS Private Global Network

     │

Singapore
```

AWS carries traffic inside its own backbone.

Much faster.

---

# Global Accelerator Request Flow

```
User

   │

Nearest AWS Edge

   │

AWS Global Backbone

   │

Region

   │

ALB

   │

EC2
```

Notice

No cache.

Just routing.

---

# Why Faster?

Instead of

```
ISP A

↓

ISP B

↓

ISP C

↓

ISP D

↓

AWS
```

Traffic becomes

```
AWS Edge

↓

AWS Fiber Network

↓

Destination
```

AWS backbone is optimized.

Less latency.

---

# What Can Global Accelerator Point To?

Usually

- ALB
- NLB
- EC2
- Elastic IP

---

# Example

Gaming Server

Singapore

Players

- Bangladesh
- India
- USA

Need

Lowest latency

No caching

Global Accelerator is perfect.

---

# Example

SSH

```
Laptop

↓

Global Accelerator

↓

EC2
```

No cache.

Just optimized route.

---

# Another Example

API

```
api.company.com

↓

Global Accelerator

↓

ALB

↓

EC2
```

Requests are dynamic.

Cannot cache.

Use Global Accelerator.

---

# Unicast vs Anycast

This is the key concept behind Global Accelerator.

---

# Unicast

One IP belongs to one server.

```
Client

↓

1.1.1.1

↓

Singapore
```

Everybody goes to the same IP.

---

Example

```
Server

IP

203.0.113.1
```

All users connect there.

---

# Anycast

One IP

Multiple locations.

```
Same IP

54.x.x.x

Exists in:

Singapore

Tokyo

London

Virginia
```

User automatically reaches nearest AWS Edge.

```
Bangladesh User

↓

Nearest AWS Edge

↓

AWS Network

↓

Destination
```

User doesn't know.

Same IP.

Different nearest edge.

---

# Visual

Unicast

```
      Client

         │

    One Server

       One IP
```

---

Anycast

```
           Same IP

       Edge A

Client ───►

       Edge B

Client ───►

       Edge C

Client ───►
```

Nearest one receives request.

---

# Global Accelerator Uses Anycast

AWS gives

Two static Anycast IPs.

Every AWS Edge advertises those IPs.

Users automatically reach nearest edge.

---

# CloudFront vs Global Accelerator

| CloudFront | Global Accelerator |
| --- | --- |
| CDN | Traffic optimizer |
| Caches data | No cache |
| Static content | Dynamic traffic |
| Uses Edge Cache | Uses AWS Backbone |
| Images | APIs |
| Videos | Games |
| CSS | TCP/UDP apps |
| JS | ALB/NLB |
| S3 | EC2 |

---

# Biggest Difference

CloudFront

```
Can answer request itself.
```

Example

```
Need logo.png

↓

Edge already has it

↓

Done.
```

Origin not contacted.

---

Global Accelerator

```
Cannot answer anything.

Always forwards.
```

```
User

↓

Edge

↓

Backend
```

Always.

---

# Example Scenario 1

Company Website

Contains

- Images
- CSS
- Videos

Need

Fast loading.

Solution

```
CloudFront

↓

S3
```

---

# Example Scenario 2

Bank API

```
Transfer Money

POST /transfer
```

Cannot cache.

Need secure routing.

Solution

```
Global Accelerator

↓

ALB

↓

EC2
```

---

# Example Scenario 3

Online Game

Players

Worldwide

Need

Very low latency.

Solution

```
Global Accelerator

↓

Game Servers
```

---

# Example Scenario 4

Netflix

Need

Video streaming

Solution

```
CloudFront

↓

Edge Cache

↓

S3
```

---

# Example Scenario 5

Amazon Shopping

Static

```
Images

CSS

JS
```

↓

CloudFront

Dynamic

```
Checkout

Login

Payment

Inventory API
```

↓

Global Accelerator

---

# Can They Work Together?

**Yes, and many large applications use both.**

Example:

```
User

      │

   DNS

      │

────────────────────────

Static Request

/images/logo.png

↓

CloudFront

↓

Edge Cache

↓

S3

────────────────────────

Dynamic Request

/api/payment

↓

Global Accelerator

↓

ALB

↓

EC2
```

Static files use CloudFront.

Dynamic APIs use Global Accelerator.

---

# Easy Memory Trick

Imagine **a food delivery app**.

### CloudFront 📦

Like **keeping food in a nearby local warehouse**.

```
Customer

↓

Nearby Warehouse

↓

Food delivered
```

The warehouse stores copies of popular items.

This is **caching**.

---

### Global Accelerator 🚗

Like **using a VIP express highway** to reach the restaurant.

```
Customer

↓

Express Highway

↓

Restaurant
```

Nothing is stored on the highway.

It simply gets you there faster.

---

# Final Exam Shortcut

| If you need... | Use |
| --- | --- |
| Cache images | CloudFront |
| Cache videos | CloudFront |
| Cache CSS/JS | CloudFront |
| Speed up downloads | CloudFront |
| Speed up API requests | Global Accelerator |
| Speed up gaming traffic | Global Accelerator |
| Speed up TCP/UDP applications | Global Accelerator |
| Static website | CloudFront |
| Dynamic application | Global Accelerator |
| Static Anycast IPs | Global Accelerator |
| AWS global private network routing | Global Accelerator |

## One-line summary

- **CloudFront = "Store a copy near the user."**
- **Global Accelerator = "Route the user to AWS over the fastest global network."**

If you remember just those two ideas, you'll be able to answer most AWS certification and interview questions about these services.
