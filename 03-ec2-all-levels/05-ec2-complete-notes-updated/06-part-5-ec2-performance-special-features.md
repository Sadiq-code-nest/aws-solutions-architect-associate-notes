# EC2 Part 5 — Performance / Special Features

This part is about **choosing the right EC2 behavior and instance type**, especially when the exam gives you CPU patterns, restart requirements, hardware-failure scenarios, or workload characteristics.

---

# 1. Burstable T Instances

## What is a T-series instance?

T-series instances are **burstable-performance instances**.

They are designed for workloads that:

- usually use **low/moderate CPU**
- occasionally need **high CPU**
- don't need maximum CPU continuously

Examples:

- small web servers
- development/test servers
- low-traffic applications
- small databases
- microservices
- applications with occasional CPU spikes

### Core idea

Think:

> **T = Typically low CPU, Temporarily high CPU**
> 

A T instance can temporarily go above its normal CPU baseline by using **CPU credits**.

---

# 2. CPU Credits ⭐⭐⭐⭐⭐

CPU credits are the most important T-series concept for SAA-C03.

A CPU credit represents an amount of CPU burst capacity.

Conceptually:

```
Normal CPU usage
      ↓
Earn credits
      ↓
CPU spike
      ↓
Spend credits
```

So:

```
Low CPU usage
     ↓
Credits accumulate
     ↓
Traffic spike
     ↓
Instance bursts above baseline
     ↓
Credits consumed
```

The exact credit earning/spending rate depends on the **instance type and size**.

---

## Example

Imagine a T instance whose baseline is:

```
30% CPU
```

Normally:

```
20%
25%
30%
```

No problem.

Suddenly:

```
80% CPU
```

The instance can temporarily burst above its baseline by spending accumulated CPU credits.

---

# 3. Baseline CPU Performance

This is an important exam distinction.

The **baseline** is the sustainable CPU performance level of the T instance without continuously relying on accumulated burst credits.

Think:

```
                    CPU
                     ↑
100% ─────────────────────
                     │
                     │  ← Burst
                     │
 30% ───────────────────── ← Baseline
                     │
                     │
  0% ─────────────────────
```

The exact baseline varies by **T-series instance type and size**.

Therefore, don't memorize one universal percentage.

### Exam logic

If the workload:

> spends most of its time below a baseline but occasionally spikes
> 

→ **T-series is a good candidate.**

If it:

> requires high CPU continuously
> 

→ **choose a non-burstable compute-optimized/general-purpose instance instead.**

---

# 4. CPU Credit Scenarios

## Scenario A — Normal workload

Application:

```
CPU:
20%
25%
15%
30%
20%
```

This is a good T-series workload.

Why?

Because CPU demand is generally around/below the baseline.

---

## Scenario B — Occasional traffic spike

Normal:

```
20%
25%
```

Traffic spike:

```
85%
90%
```

Then traffic falls:

```
20%
25%
```

Excellent T-series pattern.

The instance can accumulate credits during quieter periods and spend them during spikes.

### Trigger phrase

> **"Occasional CPU bursts"**
> 

→ **T-series**

---

# 5. Scenario C — Constant High CPU ❌

Suppose:

```
90%
92%
88%
95%
91%
93%
```

all day.

This is **not** what T-series is designed for.

The workload continuously needs CPU above the baseline.

### Better choice

Consider:

- Compute optimized (`C`)
- General purpose (`M`)
- another appropriate non-burstable family

depending on the workload.

### Exam trigger

> **"CPU-intensive workload running continuously"**
> 

→ **Do NOT automatically choose T-series.**

---

# 6. Standard vs Unlimited Mode ⭐⭐⭐⭐⭐

T instances can operate in:

- **Standard mode**
- **Unlimited mode**

The distinction is about what happens when the workload needs to burst beyond its available CPU credits.

---

## Standard Mode

The instance can burst using available CPU credits.

Once credits are exhausted:

```
CPU demand ↑
      ↓
Credits ↓
      ↓
Credits exhausted
      ↓
CPU performance returns toward baseline
```

So Standard mode protects you from unlimited additional CPU usage charges.

### Memory trick

> **Standard = credits limit the burst.**
> 

---

# 7. Unlimited Mode

Unlimited mode allows the instance to continue bursting beyond its accumulated CPU credits.

Instead of being forced back toward baseline:

```
Credits exhausted
       ↓
Continue bursting
       ↓
Additional CPU usage
       ↓
Additional charges may apply
```

This is useful when:

> the workload occasionally needs sustained bursts and you prefer performance to remain high rather than being constrained by the credit balance.
> 

But there is a cost consideration.

---

# 8. Standard vs Unlimited — Exam Table

| Feature | Standard | Unlimited |
| --- | --- | --- |
| Uses CPU credits | ✅ | ✅ |
| Can burst | ✅ | ✅ |
| Can continue beyond earned credits | ❌ | ✅ |
| Additional CPU charges possible | No for burst-credit exhaustion | ✅ |
| Good for predictable occasional bursts | ✅ | ✅ |
| Good when performance must continue beyond credits | ❌ | ✅ |
| Main concern | Performance drops toward baseline | Cost |

### Memory trick

> **Standard = Stop at credits.**
> 
> 
> **Unlimited = Spend money to keep bursting.**
> 

---

# 9. Important Exam Trap — Unlimited ≠ Unlimited CPU for Free

This is a classic trap.

"Unlimited" does **not** mean:

> Unlimited CPU with no additional cost.
> 

It means the instance can burst beyond its accumulated credits, potentially resulting in **additional charges**.

So:

```
Unlimited
≠
Unlimited free CPU
```

---

# 10. T-Series Decision Tree

```
Does workload need CPU?
        │
        ▼
Mostly low/moderate CPU?
        │
       YES
        │
        ▼
Occasional CPU spikes?
        │
       YES
        │
        ▼
     T-series
```

But:

```
Constantly high CPU?
        │
       YES
        │
        ▼
Avoid relying on T-series
        │
        ▼
Choose appropriate
non-burstable instance
```

### Exam shortcut

> **Low baseline + occasional spike = T**
> 

---

# 11. Hibernation ⭐⭐⭐⭐⭐

Hibernation is different from a normal **Stop**.

When you hibernate an EC2 instance, AWS saves the contents of the instance's **RAM** to the root EBS volume and then stops the instance.

When it starts again:

```
EBS
 ↓
RAM restored
 ↓
Application resumes
```

Instead of booting the operating system from scratch.

---

# 12. Stop vs Hibernate

This distinction is extremely exam-relevant.

| Feature | Stop | Hibernate |
| --- | --- | --- |
| Instance shuts down | ✅ | ✅ |
| EBS data preserved | ✅ | ✅ |
| RAM contents preserved | ❌ | ✅ |
| RAM saved to EBS | ❌ | ✅ |
| OS/application state preserved | ❌ | ✅ |
| Faster resume | Normal boot | Usually faster resume |
| EBS storage remains | ✅ | ✅ |
| Useful for long-running in-memory state | ❌ | ✅ |

### Memory trick

> **Stop = save disk.**
> 
> 
> **Hibernate = save disk + RAM.**
> 

---

# 13. What Actually Happens During Hibernation?

Imagine:

```
EC2 Instance

RAM
 ├── OS state
 ├── Application state
 ├── In-memory data
 └── Running process state
```

When hibernated:

```
RAM
  ↓
Saved to root EBS volume
  ↓
Instance stops
```

When resumed:

```
Root EBS
  ↓
Restore RAM
  ↓
Continue previous state
```

The goal is to resume the operating environment rather than perform a completely fresh boot.

---

# 14. Hibernation Prerequisites ⭐⭐⭐⭐⭐

This is where exam questions get tricky.

Hibernation isn't available for every EC2 configuration.

Important requirements include:

### 1. Supported instance type

The instance type must support hibernation.

---

### 2. Supported AMI / operating system

The operating system and AMI must support hibernation.

Don't assume every custom AMI automatically qualifies.

---

### 3. EBS-backed root volume

Hibernation requires the instance's memory state to be saved to storage.

Therefore, the root volume needs to support this workflow.

---

### 4. Root EBS volume must be large enough

The root EBS volume must have sufficient space to store the contents of RAM.

Think:

```
RAM = 32 GiB

↓

Root EBS must have enough
free capacity to store RAM state
```

This is an important exam concept.

---

### 5. Encryption requirements

For supported configurations, the root EBS volume used for hibernation must satisfy AWS's hibernation requirements, including appropriate encryption.

For exam purposes, remember:

> **Hibernation is tied closely to the root EBS volume because RAM is persisted there.**
> 

---

# 15. Hibernation Example

Suppose an application has:

```
64 GiB RAM
```

and takes:

```
10 minutes
```

to initialize its large in-memory dataset.

Normal stop/start:

```
Stop
 ↓
Start
 ↓
OS boot
 ↓
Application startup
 ↓
Load dataset
 ↓
Ready
```

Hibernation:

```
Hibernate
 ↓
RAM saved
 ↓
Start
 ↓
RAM restored
 ↓
Resume
```

This can significantly reduce application recovery/startup time.

---

# 16. When Is Hibernation Useful? ⭐⭐⭐⭐⭐

Look for:

### Long application initialization

Example:

> Application takes several minutes to initialize.
> 

→ Hibernation may help.

---

### Large in-memory state

Example:

> Application maintains a large working state in RAM and rebuilding it is expensive.
> 

→ Hibernation.

---

### Dev/test environments

Suppose developers work during business hours:

```
09:00 → Start
18:00 → Hibernate
09:00 → Resume
```

The state can be resumed rather than recreated.

---

### Workloads where fast resume matters

Trigger phrase:

> **"Resume exactly where it left off."**
> 

→ Think **Hibernate**.

---

# 17. When Hibernation Is NOT Appropriate

## Case 1 — Stateless application

If the application can simply restart:

```
Stop
 ↓
Start
 ↓
Application initializes
```

there may be little reason to use hibernation.

---

## Case 2 — Disposable compute

If an instance is part of an Auto Scaling fleet and can be replaced easily:

```
Instance dies
 ↓
ASG launches another
```

Hibernation usually isn't the right HA/scaling strategy.

---

## Case 3 — Instance needs continuous availability

Hibernation deliberately stops compute.

So it is **not** a high-availability mechanism.

> Hibernation saves state; it does not provide HA.
> 

---

## Case 4 — Instance type/AMI doesn't support hibernation

Obvious but frequently tested.

---

# 18. Stop vs Hibernate vs Terminate

| Action | EBS | RAM | Instance |
| --- | --- | --- | --- |
| Reboot | Preserved | Lost/reinitialized | Same instance |
| Stop | Preserved* | Lost | Same instance can restart |
| Hibernate | Preserved | Saved to EBS | Same instance resumes |
| Terminate | Usually deleted if DeleteOnTermination=true | Lost | Permanently removed |
- Subject to the volume's `DeleteOnTermination` configuration.

### Exam memory:

> **Reboot = restart OS**
> 
> 
> **Stop = preserve disk**
> 
> **Hibernate = preserve disk + RAM**
> 
> **Terminate = destroy instance**
> 

---

# 19. EC2 Auto Recovery ⭐⭐⭐⭐⭐

This is a different concept from Auto Scaling.

**EC2 Auto Recovery** can automatically recover an instance when certain underlying **system-level failures** occur.

Think:

```
EC2
 ↓
Underlying AWS system failure
 ↓
Auto Recovery
 ↓
Instance moved/recovered onto healthy infrastructure
```

The important point:

> **The problem is the underlying host/system, not your application.**
> 

---

# 20. Auto Recovery vs Auto Scaling

This is a major exam confusion.

| Feature | EC2 Auto Recovery | Auto Scaling |
| --- | --- | --- |
| Main purpose | Recover failed EC2 infrastructure | Maintain desired fleet capacity |
| Scope | Individual instance | Group of instances |
| Detect infrastructure failure | ✅ | Can respond to health/metrics |
| Launch replacement instance | Not primarily the concept | ✅ |
| Scale out/in | ❌ | ✅ |
| Application architecture | Single instance can remain | Multiple instances usually |
| Goal | Recover | Maintain/adjust capacity |

### Memory trick

> **Auto Recovery = repair/recover the instance.**
> 
> 
> **Auto Scaling = replace/scale instances.**
> 

---

# 21. Auto Recovery Scenario

Suppose you have:

```
EC2
running critical application
```

The underlying AWS hardware/system experiences a failure.

The instance is eligible for recovery.

EC2 Auto Recovery can recover it onto healthy infrastructure while preserving important instance characteristics such as:

- instance ID
- private IP
- Elastic IP
- attached EBS volumes

The exact behavior depends on the failure and supported configuration.

### Trigger phrase

> **"Underlying system failure"**
> 

→ **EC2 Auto Recovery**

---

# 22. Auto Recovery ≠ Application Failure

This is important.

Suppose:

```
Your application crashes
```

Auto Recovery isn't designed to magically restart a broken application.

Similarly:

```
Your code has a bug
```

Auto Recovery won't fix it.

Think:

> **Infrastructure failure → Auto Recovery**
> 

versus:

> **Application/instance health problem → ASG/monitoring/application recovery strategy**
> 

---

# 23. Dedicated Infrastructure Concepts

You've already covered Dedicated Hosts and Dedicated Instances in Part 4, so here we're focusing on **how they affect instance selection**.

### Dedicated Instance

Your instances run on hardware dedicated to your AWS account.

Think:

> **Dedicated hardware, but less host-level control.**
> 

---

### Dedicated Host

You get a dedicated physical server.

Think:

> **I control the physical host.**
> 

Especially important for:

- BYOL
- socket/core-based licensing
- compliance requiring dedicated physical infrastructure
- visibility into host placement

---

### Standard EC2

Normal shared AWS infrastructure.

For most workloads:

> **Standard tenancy is the default choice.**
> 

Don't choose dedicated infrastructure simply because the application is important.

---

# 24. Dedicated Infrastructure Decision

```
Need physical host control?
        │
       YES
        ↓
Dedicated Host
```

```
Need dedicated hardware
but not host-level control?
        │
       YES
        ↓
Dedicated Instance
```

```
No special physical requirement?
        │
       YES
        ↓
Normal EC2
```

### Exam trigger

> **BYOL / socket-based license**
> 

→ **Dedicated Host**

This is one of the strongest SAA-C03 clues.

---

# 25. Instance Type Selection ⭐⭐⭐⭐⭐

This is one of the most important EC2 skills.

Don't memorize instance names randomly.

Instead ask:

> **What is the bottleneck?**
> 

```
CPU?
Memory?
Storage?
Network?
GPU?
FPGA?
```

Then choose the appropriate family.

---

# 26. General Purpose — M / T

## M-series

Balanced:

```
CPU
+
Memory
```

Use when neither CPU nor memory is overwhelmingly dominant.

Examples:

- web servers
- application servers
- backend services
- general workloads

### Trigger

> **"Balanced compute and memory requirements"**
> 

→ **M-series**

---

## T-series

Burstable.

Use:

> **Low average CPU + occasional CPU spikes**
> 

→ **T-series**

---

# 27. Compute Optimized — C

C-series emphasizes CPU.

Think:

```
CPU
████████████
Memory
████
```

Good for:

- compute-heavy applications
- batch processing
- high-performance web servers
- scientific computing
- CPU-based analytics
- gaming servers
- high-performance backend processing

### Trigger

> **"CPU-intensive"**
> 

→ **C-series**

---

# 28. Memory Optimized — R / X / Z / U

Memory-optimized families are for workloads that need large amounts of RAM.

Think:

```
CPU
████
Memory
████████████████
```

Examples:

- in-memory databases
- large caches
- memory-intensive analytics
- real-time big-data processing

### Trigger

> **"Large in-memory dataset"**
> 

→ **Memory optimized**

---

# 29. Storage Optimized — I / D / H

These focus on high-performance local storage or large local storage capacity.

Useful for:

- NoSQL databases
- distributed file systems
- data warehousing
- log processing
- workloads requiring very high local disk I/O

Think:

> **The bottleneck is local storage I/O/capacity.**
> 

---

# 30. Accelerated Computing — P / G / F

These use specialized hardware.

### P-series

GPU-oriented high-performance computing.

Think:

> **ML training / HPC / large GPU workloads**
> 

---

### G-series

GPU-oriented workloads with graphics/visualization emphasis.

Think:

> **Graphics / video / visualization**
> 

---

### F-series

FPGA-based acceleration.

Think:

> **Custom hardware acceleration**
> 

---

# 31. Compute vs Memory vs Storage vs GPU/FPGA

This is the exam decision table you should memorize.

| Requirement | Instance family |
| --- | --- |
| Balanced CPU + RAM | **M** |
| Low CPU + occasional bursts | **T** |
| CPU-intensive | **C** |
| Memory-intensive | **R/X/etc.** |
| Local storage performance/capacity | **I/D/H** |
| GPU / ML / HPC | **P** |
| GPU graphics/visualization | **G** |
| FPGA acceleration | **F** |

### Super-short memory:

> **T = burst**
> 
> 
> **M = balanced**
> 
> **C = compute**
> 
> **R/X = RAM**
> 
> **I/D/H = disk**
> 
> **P/G = GPU**
> 
> **F = FPGA**
> 

---

# 32. Right-Sizing ⭐⭐⭐⭐⭐

Right-sizing means selecting an instance with enough resources for the workload **without unnecessarily paying for unused capacity**.

Bad:

```
Application needs:
2 vCPU
4 GiB RAM

You deploy:
64 vCPU
256 GiB RAM
```

You're paying for resources you don't need.

---

Better:

```
Measure workload
      ↓
CPU utilization
Memory utilization
Network
Storage I/O
      ↓
Choose appropriate size
      ↓
Monitor
      ↓
Adjust
```

---

# 33. Right-Sizing Is Not "Always Choose the Smallest"

Another exam trap.

The goal isn't:

> **minimum instance**
> 

The goal is:

> **appropriate instance for the workload.**
> 

If the workload needs high CPU:

```
small instance
↓
CPU saturated
↓
poor performance
```

That's under-sizing.

If you choose an enormous instance:

```
Huge capacity
↓
Mostly idle
↓
wasted cost
```

That's over-sizing.

---

# 34. Right-Sizing Example

Suppose monitoring shows:

```
CPU: 15%
Memory: 90%
```

Don't automatically choose a larger general-purpose instance.

The problem is primarily:

> **memory**
> 

A memory-optimized instance may be more appropriate.

---

Another example:

```
CPU: 95%
Memory: 30%
```

The bottleneck is:

> **CPU**
> 

Think **C-series** rather than simply increasing everything.

---

Another:

```
CPU: 20%
Memory: 25%
Storage I/O: extremely high
```

The bottleneck is:

> **storage**
> 

Think storage-optimized architecture rather than blindly increasing CPU/RAM.

---

# 35. Right-Sizing vs Auto Scaling

These are related but different.

### Right-sizing

Answers:

> **"What instance size/type should I use?"**
> 

Example:

```
m6i.large
→
m6i.xlarge
```

---

### Auto Scaling

Answers:

> **"How many instances should I run?"**
> 

Example:

```
2 instances
↓
CPU increases
↓
4 instances
```

### Memory trick

> **Right-sizing = size of each instance.**
> 
> 
> **Auto Scaling = number of instances.**
> 

---

# 36. Right-Sizing vs Vertical vs Horizontal Scaling

Another exam distinction:

### Vertical scaling

Make the instance bigger.

```
small
 ↓
large
```

### Horizontal scaling

Add more instances.

```
1
 ↓
3
 ↓
10
```

EC2 Auto Scaling primarily performs:

> **horizontal scaling**
> 

Right-sizing is primarily about:

> **choosing the appropriate individual instance size/type.**
> 

---

# 37. CPU vs Memory Decision Tree

Use this during the exam:

```
What is the dominant resource?
          │
 ┌────────┼─────────┐
 │        │         │
CPU     Memory    Storage
 │        │         │
C       R/X/etc.   I/D/H
```

If balanced:

```
M
```

If bursty:

```
T
```

If specialized acceleration:

```
GPU → P/G
FPGA → F
```

---

# 38. A Very Important Exam Pattern

Question:

> An application normally uses 20% CPU but periodically experiences CPU utilization of 90%. Which instance type is most cost-effective?
> 

Answer pattern:

**T-series**

Why?

```
Low average CPU
+
Occasional burst
=
Burstable instance
```

---

# 39. Another Exam Pattern

> An application continuously performs CPU-intensive calculations and requires high CPU performance throughout the day.
> 

Do **not** choose T merely because it can burst.

Answer pattern:

**Compute optimized C-series.**

Why?

```
Continuous CPU demand
≠
burstable workload
```

---

# 40. Another Exam Pattern

> A database keeps a massive working dataset in memory and requires extremely large RAM capacity.
> 

Answer:

**Memory-optimized instance.**

Trigger:

> **"Large in-memory dataset"**
> 

---

# 41. Another Exam Pattern

> A workload requires hardware acceleration for machine-learning model training.
> 

Think:

**GPU-based accelerated computing → P-series**

---

# 42. Another Exam Pattern

> An organization has software licensed based on physical CPU sockets and wants to use its existing license on AWS.
> 

Think:

**Dedicated Host**

Not:

- Dedicated Instance
- Standard EC2
- Capacity Reservation

The key is:

> **physical host/socket visibility**
> 

---

# 43. Another Exam Pattern — Infrastructure Failure

> A critical EC2 instance must automatically recover from an underlying AWS system failure.
> 

Think:

**EC2 Auto Recovery**

Not:

- Auto Scaling
- Spot
- Hibernation

Because:

```
Infrastructure failure
→ Auto Recovery
```

---

# 44. Another Exam Pattern — Resume Existing Memory State

> A developer needs an EC2 instance to resume quickly with the application state in RAM preserved.
> 

Think:

**Hibernation**

Not Stop.

Because:

```
Stop:
EBS survives
RAM doesn't

Hibernate:
EBS survives
RAM state saved
```

---

# 45. Hibernation vs Auto Recovery vs Auto Scaling

This is an excellent confusion-buster.

| Requirement | Correct feature |
| --- | --- |
| Preserve RAM/application state while stopping | **Hibernation** |
| Recover from underlying system failure | **EC2 Auto Recovery** |
| Add/remove EC2 instances based on demand | **Auto Scaling** |
| Replace unhealthy instances in a fleet | **Auto Scaling** |
| Resume quickly from saved memory state | **Hibernation** |
| Recover infrastructure automatically | **Auto Recovery** |

### Memory:

> **Hibernate = preserve state**
> 
> 
> **Auto Recovery = recover infrastructure**
> 
> **ASG = manage fleet capacity**
> 

---

# 46. T-Series vs C-Series vs M-Series

| Requirement | Pick |
| --- | --- |
| Low CPU most of time + spikes | **T** |
| Balanced CPU + memory | **M** |
| Constant CPU-intensive workload | **C** |

### The trap

Don't see:

> "high CPU"
> 

and immediately select C.

Ask:

> **Is the high CPU continuous or occasional?**
> 

```
Occasional → T
Continuous → C
```

That's a very high-value SAA decision rule.

---

# 47. Dedicated Infrastructure vs Capacity Reservation

Another common confusion:

| Requirement | Pick |
| --- | --- |
| Physical dedicated hardware | Dedicated Instance/Host |
| BYOL/socket licensing | Dedicated Host |
| Guarantee EC2 capacity in a specific AZ | Capacity Reservation |
| Lower long-term EC2 price | RI / Savings Plan |
| Dedicated physical host + licensing visibility | Dedicated Host |

Remember:

> **Capacity Reservation reserves capacity. It does NOT give you a dedicated physical server.**
> 

---

# 48. EC2 Instance Selection — Master Decision Tree

```
                 EC2 workload
                      │
                      ▼
              What is the need?
                      │
      ┌───────────────┼─────────────────┐
      │               │                 │
   CPU pattern      Memory            Storage
      │               │                 │
      ▼               ▼                 ▼
 ┌────┴────┐      Memory-          Storage-
 │         │      optimized        optimized
Burst    Constant
 │         │
 T         C
      │
      ▼
Balanced CPU + RAM
      │
      M
```

Specialized:

```
GPU / ML
   ↓
P/G

FPGA
   ↓
F
```

Infrastructure:

```
Physical host requirement
        ↓
Dedicated Host
```

Failure:

```
Underlying AWS system failure
        ↓
EC2 Auto Recovery
```

State:

```
Preserve RAM across stop
        ↓
Hibernation
```

---

# 49. SAA-C03 Trigger Phrase Master Table ⭐⭐⭐⭐⭐

| Trigger phrase | Correct pick |
| --- | --- |
| **Occasional CPU spikes** | **T-series** |
| **Low average CPU, burst occasionally** | **T-series** |
| **CPU credits** | **T-series** |
| **Continue bursting after credits are exhausted** | **Unlimited mode** |
| **Avoid additional burst charges / stay within credits** | **Standard mode** |
| **Constantly high CPU** | **C-series** |
| **Balanced CPU + memory** | **M-series** |
| **Large in-memory dataset** | **Memory-optimized** |
| **High local disk I/O** | **Storage-optimized** |
| **Machine learning GPU training** | **P-series** |
| **Graphics / visualization GPU** | **G-series** |
| **FPGA acceleration** | **F-series** |
| **Preserve RAM while stopped** | **Hibernation** |
| **Resume previous memory state** | **Hibernation** |
| **Underlying EC2 system failure** | **EC2 Auto Recovery** |
| **Scale number of instances** | **Auto Scaling** |
| **Physical host control** | **Dedicated Host** |
| **BYOL / socket-based licensing** | **Dedicated Host** |
| **Dedicated hardware but no host control requirement** | **Dedicated Instance** |
| **Reserve capacity in specific AZ** | **On-Demand Capacity Reservation** |
| **Choose appropriate instance size** | **Right-sizing** |
| **CPU 95%, RAM 30%** | CPU/compute optimized |
| **CPU 20%, RAM 95%** | Memory optimized |
| **CPU 20%, RAM 20%, storage I/O huge** | Storage optimized |

---

# 50. 30-Second Final Exam Scan

Memorize this:

```
T = BURST
M = BALANCED
C = CPU
R/X = RAM
I/D/H = STORAGE
P/G = GPU
F = FPGA
```

```
Standard T
    ↓
credits exhausted
    ↓
return toward baseline

Unlimited T
    ↓
credits exhausted
    ↓
keep bursting
    ↓
possible extra charges
```

```
STOP
→ Disk survives
→ RAM does not

HIBERNATE
→ Disk survives
→ RAM saved
→ Resume state
```

```
EC2 SYSTEM FAILURE
→ Auto Recovery

FLEET CAPACITY
→ Auto Scaling
```

```
BYOL / SOCKET LICENSE
→ Dedicated Host

CAPACITY IN SPECIFIC AZ
→ Capacity Reservation

LOW CPU + SPIKES
→ T

CONSTANT HIGH CPU
→ C
```

---

# EC2 Part 5 — Final Confusion-Buster

| If the question says... | Think... |
| --- | --- |
| **"CPU spikes occasionally"** | T-series |
| **"CPU is high all day"** | C-series |
| **"CPU credits"** | T-series |
| **"continue bursting beyond credits"** | Unlimited |
| **"preserve RAM"** | Hibernate |
| **"underlying hardware/system failure"** | EC2 Auto Recovery |
| **"maintain desired number of instances"** | Auto Scaling |
| **"physical host / BYOL"** | Dedicated Host |
| **"reserve capacity in AZ"** | Capacity Reservation |
| **"balanced workload"** | M-series |
| **"large memory requirement"** | Memory optimized |
| **"high local storage I/O"** | Storage optimized |
| **"GPU ML training"** | P-series |
| **"graphics/visualization"** | G-series |
| **"FPGA"** | F-series |
| **"reduce wasted EC2 capacity"** | Right-sizing |

## One-line memory formula

> **Burst → T | CPU → C | Balanced → M | RAM → R/X | Disk → I/D/H | GPU → P/G | FPGA → F | RAM preservation → Hibernate | Host failure → Auto Recovery | Fleet scaling → ASG | Physical host → Dedicated Host**
> 

**EC2 Part 5 complete.**
