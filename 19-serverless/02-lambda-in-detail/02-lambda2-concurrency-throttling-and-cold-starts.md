# AWS Lambda Concurrency, Throttling & Cold Starts (Beginner Friendly)

This is **one of the most important Lambda topics** for the AWS SAA-C03 exam. Many scenario-based questions revolve around it.

---

# First, What is Concurrency?

Imagine you own a pizza shop.

- 👨‍🍳 **1 Chef** = **1 Lambda execution**
- 🍕 **1 Pizza Order** = **1 Lambda invocation**

If **one customer** comes:

```
1 Customer
    │
    ▼
1 Lambda Execution
```

Easy.

---

Now imagine **100 customers** come **at exactly the same time**.

```
100 Customers
      │
      ▼
100 Lambda Executions
```

This is called 'c**oncurrency'**.

> **Concurrency = Number of Lambda functions running at the same time.**
> 

---

# Example

Suppose each Lambda takes **5 seconds**.

### First second

```
100 Requests

↓

100 Running Lambdas
```

Five seconds later

```
All 100 Finish
```

Concurrency was **100**.

---

# AWS Default Concurrency

By default, each AWS account has about

```
1000 Concurrent Executions
```

Think of it as:

AWS gives you **1000 workers**.

---

Example

```
1000 Requests

↓

1000 Workers

↓

All Running
```

Everything works.

---

# What Happens at Request 1001?

Suppose

```
1000 Lambdas

Already Running
```

Now another request arrives.

```
Request #1001

↓

No Worker Available
```

AWS cannot run it immediately.

This is called

# Throttling

---

# What is Throttling?

**Throttling** means

> AWS temporarily refuses new executions because you've reached the concurrency limit.
> 

Think of a restaurant.

```
1000 Seats

↓

Restaurant Full

↓

New Customer Arrives

↓

Wait Outside
```

Same idea.

---

# Exam Definition

> **Throttle = Lambda cannot execute because concurrency limit has been reached.**
> 

---

# What Happens During Throttling?

It depends on **how Lambda was invoked**.

---

# 1. Synchronous Invocation

Example

```
User

↓

API Gateway

↓

Lambda
```

User waits for a response.

If throttled,

AWS immediately returns

```
HTTP 429

Too Many Requests
```

No automatic retry.

---

# Easy Analogy

Calling a friend.

```
Phone Busy

↓

Call Failed
```

You must call again.

---

# 2. Asynchronous Invocation

Example

```
S3

↓

Lambda
```

S3 doesn't wait.

If Lambda is throttled,

AWS automatically retries.

```
S3

↓

Lambda

↓

Throttled

↓

Retry

↓

Retry Again
```

No action needed from the user.

---

# Retry Behavior

Lambda retries for

```
Up to 6 Hours
```

The retry delay increases over time.

Example

```
1 sec

↓

2 sec

↓

4 sec

↓

8 sec

↓

30 sec

↓

5 min
```

This is called

> **Exponential Backoff**
> 

AWS retries less frequently over time to avoid overwhelming the system.

---

# If It Still Fails?

After retries are exhausted,

you can send the failed event to a

### Dead Letter Queue (DLQ)

Usually

- Amazon SQS
- Amazon SNS

```
Lambda

↓

Retry

↓

Retry

↓

Retry

↓

Still Failed

↓

DLQ
```

Nothing is lost.

---

# Reserved Concurrency ⭐⭐⭐

This topic is commonly tested.

---

Imagine your AWS account has

```
1000 Workers
```

You have two Lambda functions.

```
Payment Lambda

Image Resize Lambda
```

Suddenly,

Image Resize receives

```
1000 Requests
```

It uses **all 1000 workers**.

Now a payment request arrives.

```
Payment Lambda

↓

No Workers Available
```

Payment fails.

---

# Solution

Reserve workers for important functions.

Example

```
Total Workers

1000

↓

Reserve

200

For Payment
```

Now,

even if Image Resize gets

```
10,000 Requests
```

Payment still has

```
200 Reserved Workers
```

Always available.

---

# Think of Reserved Concurrency Like Reserved Parking

```
Parking Lot

1000 Spaces

↓

Reserve

100

VIP Only
```

No one else can use them.

---

# Reserved Concurrency Means

✅ Guarantees a minimum amount of concurrency for one function.

It also acts as a **maximum limit** for that function.

Example

Reserve

```
50
```

That function can never run more than

```
50 Concurrent Executions
```

---

# Provisioned Concurrency ⭐⭐⭐⭐

Now another problem.

Suppose

Nobody has called your Lambda for an hour.

AWS removes the execution environment to save resources.

A new request arrives.

AWS must

```
Create Environment

↓

Load Runtime

↓

Load Libraries

↓

Initialize Code

↓

Run Function
```

This takes extra time.

This is called a

# Cold Start

---

# Cold Start

Imagine starting your car on a cold morning.

First time

```
Start Engine

↓

Warm Up

↓

Drive
```

Slow.

---

Second trip

```
Already Warm

↓

Drive Immediately
```

Fast.

Lambda behaves the same way.

---

# Warm Start

If Lambda recently ran,

AWS reuses the existing execution environment.

```
Reuse Existing Environment

↓

Immediately Execute
```

Much faster.

---

# What Causes Cold Starts?

AWS needs to:

- Start the runtime (Python, Java, .NET, etc.)
- Load your code
- Load dependencies
- Initialize SDKs
- Run code outside the handler (initialization phase)

The larger your application,

the longer the cold start.

---

# Java Cold Starts

Java applications often have

- Large libraries
- Longer startup time

So Java cold starts are usually slower than Python or Node.js.

---

# Provisioned Concurrency

Suppose your website always receives traffic at 9:00 AM.

Instead of waiting for the first user,

AWS prepares Lambda environments **before requests arrive**.

```
Before Traffic

↓

Warm Lambda

↓

Ready

↓

User Arrives

↓

Immediate Response
```

No cold start.

---

# Restaurant Analogy

Normal Lambda

```
Customer Arrives

↓

Chef Comes From Home

↓

Starts Cooking
```

Slow.

---

Provisioned Concurrency

```
Chef Already Inside Kitchen

↓

Customer Arrives

↓

Cooking Starts Immediately
```

Fast.

---

# Can AWS Manage Provisioned Concurrency?

Yes.

Using **Application Auto Scaling**, AWS can:

- Increase provisioned concurrency before busy periods (scheduled scaling)
- Adjust it automatically based on utilization (target tracking)

Example:

```
8:55 AM

↓

Prepare 100 Warm Lambdas

↓

9:00 AM

Traffic Starts
```

---

# Reserved vs Provisioned Concurrency

Many students confuse these.

| Reserved Concurrency | Provisioned Concurrency |
| --- | --- |
| Reserves part of the account's concurrency for a function | Keeps execution environments pre-initialized |
| Prevents other functions from consuming that capacity | Eliminates (or greatly reduces) cold starts |
| Controls concurrency limits | Improves startup latency |
| Doesn't keep functions warm | Keeps functions warm |
| Mainly about **capacity isolation** | Mainly about **performance** |

---

# Can You Use Both?

Yes.

Example

```
Reserved

200

↓

Provisioned

50
```

Meaning:

- Function can use up to **200 concurrent executions**
- **50** execution environments are already warm

---

# Visual Summary

```
Requests

↓

Concurrency

↓

Within Limit?
        │
   Yes  │  No
        │
        ▼
   Run Function
        │
        ▼
     Throttle
```

---

```
Cold Start

↓

Create Environment

↓

Load Runtime

↓

Initialize

↓

Run
```

---

```
Provisioned Concurrency

↓

Environment Already Warm

↓

Run Immediately
```

---

# AWS SAA-C03 Cheat Sheet

| Topic | Remember |
| --- | --- |
| **Concurrency** | Number of Lambda executions running simultaneously |
| **Default Limit** | Around **1000 concurrent executions** per account (can be increased via support request) |
| **Throttling** | Happens when concurrency limit is exceeded |
| **Synchronous Invocation** | Client gets **HTTP 429 Too Many Requests** if throttled |
| **Asynchronous Invocation** | AWS automatically retries with exponential backoff (up to about 6 hours) |
| **DLQ** | Stores events that still fail after retries |
| **Reserved Concurrency** | Reserves (and limits) concurrency for a specific function |
| **Cold Start** | First execution after AWS creates a new execution environment |
| **Warm Start** | Reuses an existing execution environment for lower latency |
| **Provisioned Concurrency** | Pre-warms execution environments to reduce cold starts |

---

# Exam Memory Trick

Think of **three different problems** and their solutions:

| Problem | Solution |
| --- | --- |
| Too many requests at once | **Concurrency** |
| One function steals all available capacity | **Reserved Concurrency** |
| First request is slow | **Provisioned Concurrency** |

If you can answer:

- **"How many functions can run at the same time?"** → Concurrency
- **"How do I guarantee capacity for an important function?"** → Reserved Concurrency
- **"How do I reduce cold-start latency?"** → Provisioned Concurrency

...you've mastered one of the highest-value Lambda topics for the SAA-C03 exam.
