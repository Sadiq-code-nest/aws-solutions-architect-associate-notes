# PART 8 — Global Accelerator

Students confuse this with CloudFront.

They solve different problems.

---

Suppose

Your website

Uses

ALB.

Global users connect.

Without Global Accelerator

Traffic

↓

Internet

↓

Many routers

↓

ALB

Slow.

---

Global Accelerator

Gives

Two Anycast IPs.

Traffic

↓

Nearest AWS Edge

↓

AWS Private Network

↓

Destination

Much faster.

---

# Diagram

Without GA

```
India

↓

Internet

↓

USA ALB
```

With GA

```
India

↓

Mumbai Edge

↓

AWS Backbone

↓

USA ALB
```

---

# Why Faster?

AWS Network

is faster than

Public Internet.

---

# Unicast vs Anycast

## Unicast

One IP

↓

One Server.

---

## Anycast

Same IP

Exists everywhere.

AWS automatically routes you

to nearest Edge.

---

Real Life

Pizza

One shop.

↓

Travel far.

---

Anycast

Same pizza chain

Every city.

↓

Go nearest shop.

---

# Features

Supports

- ALB
- NLB
- EC2
- Elastic IP

Supports

TCP

UDP

---

Health Checks

If

US Region fails

↓

Automatically routes

to Europe.

Usually

less than one minute.

---

Great for

Disaster Recovery.

---

Only

Two Static IPs

Need firewall whitelist.

Very useful.

---

# CloudFront vs Global Accelerator

| CloudFront | Global Accelerator |
| --- | --- |
| CDN | Network Accelerator |
| Caches Content | No Cache |
| Best for Images, CSS, JS, Videos | Best for Applications |
| HTTP/HTTPS | TCP & UDP |
| Returns Cached Files | Forwards Traffic |
| Origin = S3, ALB, EC2 | ALB, NLB, EC2, Elastic IP |
| Reduces Origin Load | Doesn't cache |
| Uses TTL | No TTL |
| Static Content | Dynamic Applications |

---

# Real-World Scenarios

### Scenario 1

Website has images.

Users worldwide.

Answer

✅ CloudFront

---

### Scenario 2

Private S3 bucket.

Need secure CDN.

Answer

✅ CloudFront + OAC

---

### Scenario 3

Website logo updated immediately.

Answer

✅ Cache Invalidation

---

### Scenario 4

Movie only available in Canada.

Answer

✅ Geo Restriction (Allowlist)

---

### Scenario 5

Gaming server over UDP with global users.

Answer

✅ Global Accelerator

---

### Scenario 6

Global banking application using ALB.

Need low latency and automatic regional failover.

Answer

✅ Global Accelerator

---

### Scenario 7

Need a permanent copy of data in another AWS Region.

Answer

✅ S3 Cross-Region Replication (CRR), **not** CloudFront.

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

---
