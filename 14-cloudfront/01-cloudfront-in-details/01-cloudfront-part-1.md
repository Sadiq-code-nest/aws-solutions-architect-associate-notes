# CloudFront Part 1

This topic is one of the **most important topics in AWS SAA-C03**. It combines **CloudFront + CDN + Global Accelerator + Caching + Edge Locations + OAC + Geo Restriction + Cache Invalidation + Pricing**.

The key to understanding it is:

> **First understand the problem AWS is trying to solve. Then every service becomes easy.**
> 

---

# PART 1 — Why do we need CloudFront?

## Imagine this architecture

You have one web server.

```
             USA (Ohio)

           EC2 / ALB / S3

                │

        ┌───────┼────────┐

   User-USA  User-Europe User-India
```

Everyone downloads images from one server.

---

## Problem

European users must travel thousands of kilometers.

Example

```
Europe

↓

Internet

↓

USA

↓

Receive image

↓

Back to Europe
```

Maybe

```
350 ms
```

---

Australian user

```
Australia

↓

Internet

↓

USA

↓

Back
```

Latency

```
600 ms
```

Very slow.

---

# Solution

Instead of making everyone go to USA,

AWS places copies of your files around the world.

These are called

# Edge Locations

Example

```
USA

Europe

India

Japan

Australia

Brazil

South Africa
```

Now

European user

↓

Downloads from Europe.

Indian user

↓

Downloads from India.

Much faster.

---

# This is CloudFront.

CloudFront is

> **AWS's Content Delivery Network (CDN).**
> 

---

# What is a CDN?

CDN =

Content Delivery Network

Instead of storing files in one place,

copies are cached globally.

---

## Diagram

Without CDN

```
          USA Server

              │

     ┌────────┼────────┐

 Europe     India   Australia
```

Everyone goes to USA.

---

With CloudFront

```
            USA Origin

                │

      ┌─────────┼─────────┐

 Europe Edge  India Edge Australia Edge

      │            │            │

   Europe User  India User  Australia User
```

Huge improvement.

---

# CloudFront Workflow

Suppose

Origin

```
S3 Bucket
```

contains

```
logo.png
```

---

European user requests

```
logo.png
```

CloudFront checks

European Edge.

No copy.

↓

Downloads once from S3.

↓

Stores locally.

↓

Returns image.

---

Second European user

↓

Edge already has it.

↓

Returns immediately.

No S3 request.

---

Diagram

```
User

↓

Europe Edge

↓

Cache Miss

↓

S3 Bucket

↓

Store Cache

↓

Return

----------------

Second User

↓

Europe Edge

↓

Cache Hit

↓

Return Immediately
```

---

# Cache Hit vs Cache Miss

## Cache Miss

Object not in Edge.

```
User

↓

Edge

↓

Origin

↓

User
```

Slow.

---

## Cache Hit

Object already exists.

```
User

↓

Edge

↓

User
```

Very fast.

---

# What is TTL?

TTL

=

Time To Live

How long should CloudFront keep a cached copy?

Suppose

TTL

```
24 Hours
```

Timeline

```
9 AM

↓

CloudFront downloads image

↓

Stores

↓

9 PM

Still cached

↓

Next Day

9 AM

TTL expires

↓

Fetch new version
```

---

# Why CloudFront Improves Performance

Because

Instead of

```
India → USA
```

traffic becomes

```
India → Mumbai Edge
```

Latency becomes much smaller.

---

# CloudFront Benefits

✅ Faster loading

✅ Lower latency

✅ Less traffic to origin

✅ Lower origin cost

✅ Better user experience
