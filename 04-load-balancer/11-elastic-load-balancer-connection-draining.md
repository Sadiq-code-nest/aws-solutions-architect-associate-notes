# Connection Draining / Deregistration Delay — AWS SAA-C03

**Connection Draining** is the **CLB** terminology.

**Deregistration Delay** is the terminology used by **ALB & NLB**.

The concept is the same:

> When an EC2 target is being removed or becomes unhealthy, the Load Balancer **stops sending new requests to it but allows existing/in-flight requests to finish**.
> 

---

## How It Works

Suppose the Load Balancer has 3 EC2 instances:

```
             Load Balancer
            /      |      \
          EC2-A  EC2-B  EC2-C
```

EC2-B is being removed.

Instead of immediately terminating traffic:

```
EC2-B → DRAINING
```

The Load Balancer:

```
❌ New requests → EC2-B

✅ Existing requests → EC2-B continue
```

Meanwhile:

```
New requests
     ↓
Load Balancer
   ↙       ↘
EC2-A     EC2-C
```

Once existing requests finish, EC2-B can safely be removed.

---

# Example

Imagine a user uploads a large 2-GB file.

```
User
 ↓
Load Balancer
 ↓
EC2-B
```

During the upload, EC2-B is deregistered.

Without draining:

```
EC2-B terminated
      ↓
❌ Upload interrupted
```

With deregistration delay:

```
EC2-B → DRAINING
      ↓
Existing upload continues
      ↓
Upload completes ✅
      ↓
EC2-B removed
```

---

# Timing

| Setting | Value |
| --- | --- |
| Minimum | **1 second** |
| Maximum | **3600 seconds (1 hour)** |
| Default | **300 seconds (5 minutes)** |
| Disable | Set to **0** |

---

# What Happens During Draining?

| Request | Behavior |
| --- | --- |
| Existing/in-flight request | ✅ Allowed to finish |
| New request | ❌ Not sent to draining target |
| New connections | Sent to healthy targets |
| After delay expires | Target is deregistered |

---

# CLB vs ALB vs NLB

| Load Balancer | Feature Name | Default |
| --- | --- | --- |
| **Classic Load Balancer (CLB)** | **Connection Draining** | 300 sec |
| **Application Load Balancer (ALB)** | **Deregistration Delay** | 300 sec |
| **Network Load Balancer (NLB)** | **Deregistration Delay** | 300 sec |

### 🧠 Exam memory

> **CLB = Connection Draining**
> 
> 
> **ALB/NLB = Deregistration Delay**
> 

Same basic idea.

---

# When Should You Use a Low Value?

If your requests are very short:

```
Request duration = 1–2 seconds
```

You don't need to wait several minutes.

You can use a shorter deregistration delay.

### Example

```
Short API requests
↓
1–10 seconds may be sufficient
```

But for:

```
Long file uploads
Long-running HTTP requests
Long transactions
```

you may need a longer delay.

---

# 🧠 SAA-C03 Exam Clues

| Question clue | Answer |
| --- | --- |
| EC2 being removed from Load Balancer | **Deregistration Delay / Connection Draining** |
| Stop new requests but finish existing requests | ⭐ **Connection Draining** |
| ALB terminology | **Deregistration Delay** |
| NLB terminology | **Deregistration Delay** |
| CLB terminology | **Connection Draining** |
| Default timeout | **300 seconds** |
| Maximum | **3600 seconds** |
| Disable feature | **Set to 0** |
| Long-running requests must finish before termination | **Increase deregistration delay** |
| Short requests | **Use a lower delay** |

### 🔥 One-line memory

> **Drain = "No new requests, finish the old ones."**
> 

    ============= OLD =============
