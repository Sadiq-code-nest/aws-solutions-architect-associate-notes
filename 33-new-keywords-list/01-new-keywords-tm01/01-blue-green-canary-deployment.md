# Blue-green canary deployment

The easiest way to understand **Blue-Green vs Canary deployment** is to focus on **how much traffic is moved and how gradually**.

## 🔵🟢 Blue-Green Deployment

### Core idea

You maintain **two complete environments**:

- 🔵 **Blue** = current/old version
- 🟢 **Green** = new version

Initially:

```
Users
  │
  ▼
Load Balancer
  │
  ▼
🔵 BLUE
v1.0
100% traffic
```

You deploy the new version separately:

```
Users
  │
  ▼
Load Balancer
  │
  ├── 🔵 Blue → v1.0
  │
  └── 🟢 Green → v2.0
```

You test Green.

Once you're confident:

```
Before:

Users ──100%──> 🔵 Blue v1.0

Switch:

Users ──100%──> 🟢 Green v2.0
```

If something goes wrong:

```
Users ──100%──> 🔵 Blue v1.0
```

You simply switch traffic back.

### 🛒 Real-world example

Imagine you have an e-commerce website:

```
shop.example.com
```

Current production:

```
🔵 Blue
Application v1
100% users
```

Your developers create:

```
🟢 Green
Application v2
```

You deploy v2 to Green and test:

- Login
- Product search
- Shopping cart
- Checkout
- Payment
- Database connectivity

Everything looks good.

Then the load balancer changes:

```
100% → Green
```

Now customers are using v2.

If checkout suddenly breaks:

```
Green ❌
     ↓
Switch traffic back
     ↓
Blue ✅
```

### ⭐ Key characteristic

**Traffic switches from one environment to another.**

Think:

> **Blue-Green = "Build the new environment, then switch."**
> 

---

# 🐤 Canary Deployment

Canary is different.

Instead of immediately moving **100% traffic**, you send a **small percentage of real users** to the new version first.

Example:

```
Users
  │
  ▼
Load Balancer
  │
  ├── 🟢 v2 → 5%
  │
  └── 🔵 v1 → 95%
```

You monitor:

- Error rate
- Latency
- CPU/memory
- Application logs
- Conversion/checkout failures
- CloudWatch metrics

If everything looks good:

```
5% → 10% → 25% → 50% → 100%
```

Eventually:

```
Users
  │
  ▼
🟢 v2
100%
```

---

## 🛒 Real-world Canary Example

Suppose an online store has **1 million users**.

You release a new payment service.

You don't want to risk sending all 1 million users to the new version.

So:

### Stage 1

```
v1 → 99%
v2 → 1%
```

You monitor the new version.

Suppose:

```
v1 error rate = 0.5%
v2 error rate = 0.6%
```

Looks acceptable.

### Stage 2

```
v1 → 90%
v2 → 10%
```

Still healthy.

### Stage 3

```
v1 → 75%
v2 → 25%
```

Still healthy.

### Stage 4

```
v1 → 50%
v2 → 50%
```

Still healthy.

### Final

```
v1 → 0%
v2 → 100%
```

The deployment is complete.

---

# 🚨 What if Canary fails?

Suppose at 25% traffic you discover:

```
v1 → 75%
v2 → 25%

v2 error rate = 8% ❌
```

Stop the rollout.

```
Remove v2 from traffic

v1 → 100%
v2 → 0%
```

Only the small percentage of users exposed to v2 experienced the problem.

That's the **major advantage of Canary**.

---

# 🔵🟢 vs 🐤 Canary — The Most Important Difference

| Feature | 🔵🟢 Blue-Green | 🐤 Canary |
| --- | --- | --- |
| Environments | Two environments | Usually old + new version |
| New version | Fully deployed before switch | Gradually exposed |
| Initial traffic to new version | Usually **0%** | Usually **small %** |
| Traffic movement | **Big switch** | **Gradual shift** |
| Example | 100% Blue → 100% Green | 1% → 5% → 10% → 50% → 100% |
| Risk | Low, but larger blast radius during switch | **Very low blast radius** |
| Rollback | Very fast traffic switch | Stop rollout / route traffic back |
| Main purpose | Fast release + easy rollback | Safely validate with real traffic |
| Infrastructure cost | Usually higher | Can be lower, depending on implementation |
| Best for | Major releases where fast rollback matters | High-risk changes where gradual validation matters |

---

# 🧠 Think About It Like This

Imagine you're opening a **new restaurant**.

### 🔵🟢 Blue-Green

You have two restaurants:

```
🔵 OLD RESTAURANT
     ↓
Currently serving everyone

🟢 NEW RESTAURANT
     ↓
Fully prepared and tested
```

Then you tell everyone:

> "From today, everybody goes to the new restaurant."
> 

That's **Blue-Green**.

---

### 🐤 Canary

You have a new restaurant and say:

> "Let's send only 1% of our customers there first."
> 

```
99% → Old restaurant
 1% → New restaurant
```

If customers are happy:

```
90% → Old
10% → New
```

Then:

```
50% → Old
50% → New
```

Finally:

```
0% → Old
100% → New
```

That's **Canary**.

---

# ☁️ AWS Example

This distinction is **very important for SAA-C03**.

### Blue-Green with AWS

You could have:

```
Route 53 / ALB
       │
       ├──── 🔵 EC2/ECS v1
       │
       └──── 🟢 EC2/ECS v2
```

After testing v2, change routing so that:

```
100% → Green
```

If v2 fails:

```
100% → Blue
```

AWS services that can participate in blue-green strategies include **Elastic Beanstalk**, **CodeDeploy**, **ECS**, and others depending on architecture.

---

### Canary with AWS

For example, API Gateway:

```
API Gateway
     │
     ├── v1 → 95%
     │
     └── v2 → 5%
```

Then gradually:

```
v1       v2
95%      5%
 ↓
90%     10%
 ↓
75%     25%
 ↓
50%     50%
 ↓
0%     100%
```

You monitor the application at each stage.

---

# ⚠️ Very Important Exam Trap

Don't confuse:

> **Blue-Green = 50/50 traffic**
> 

Not necessarily.

Blue-Green is fundamentally about **two separate environments and switching traffic between them**.

The classic model is:

```
Blue = 100%
Green = 0%

       ↓ SWITCH

Blue = 0%
Green = 100%
```

Canary is fundamentally about:

> **Gradually exposing users to the new version.**
> 

```
1%
 ↓
5%
 ↓
10%
 ↓
25%
 ↓
50%
 ↓
100%
```

---

# 🎯 SAA-C03 Exam Keywords

### If you see:

**"Deploy new version alongside existing version"**

→ 🔵🟢 **Blue-Green**

---

**"Switch traffic to new environment"**

→ 🔵🟢 **Blue-Green**

---

**"Instant rollback by switching traffic back"**

→ 🔵🟢 **Blue-Green**

---

**"Small percentage of users"**

→ 🐤 **Canary**

---

**"Gradually shift traffic"**

→ 🐤 **Canary**

---

**"Monitor new version before full rollout"**

→ 🐤 **Canary**

---

**"Minimize blast radius"**

→ 🐤 **Canary**

---

## 🧠 One-Line Memory Trick

> 🔵🟢 **Blue-Green = Switch environments.**
> 
> 
> 🐤 **Canary = Slowly increase traffic.**
> 

And one more useful distinction:

```
BLUE-GREEN
"Is the NEW environment ready?"
             ↓
       Test → Switch

CANARY
"Does the NEW version work well with REAL users?"
             ↓
    Small traffic → Monitor → Increase
```

**For SAA-C03, if the question emphasizes *fast rollback / two environments / traffic switch*, think Blue-Green. If it emphasizes *small percentage / gradual traffic shifting / minimizing blast radius*, think Canary.**
