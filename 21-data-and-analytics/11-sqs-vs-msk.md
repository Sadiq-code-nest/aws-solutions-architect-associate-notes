# SQS Vs MSK

This is actually one of the **most confusing topics** in AWS SAA because **MSK, SQS, SNS, and Kinesis all deal with messages/events**, but they solve different problems.

The easiest way to separate them is by understanding **their purpose**, not just their features.

---

# First, Think About Real Life

Imagine a restaurant.

## Amazon SQS = Waiting Line

Customers take a ticket and wait.

```
Customer

    │

    ▼

   Queue

    │

    ▼

Chef prepares order

    │

    ▼

Remove from queue
```

Each order is processed **once**, then removed.

---

## Amazon MSK = Live News Channel

Every event is broadcast.

```
Kitchen

↓

Order Created

↓

MSK

↓

Billing

↓

Analytics

↓

Dashboard

↓

Recommendation System
```

The event stays in Kafka.

Many applications can read it.

---

# What is the Purpose?

| Service | Purpose |
| --- | --- |
| SQS | Queue tasks/jobs |
| MSK | Stream events continuously |

This is the biggest difference.

---

# Easy Definition

## Amazon SQS

> A managed message queue.
> 

Stores messages until a consumer processes them.

Then the message disappears.

---

## Amazon MSK

> A managed event streaming platform (Apache Kafka).
> 

Stores events for replay and allows many consumers.

---

# Visual Difference

## SQS

```
Producer

↓

Queue

↓

Consumer

↓

Delete Message
```

One message

↓

One consumer

↓

Finished

---

## MSK

```
Producer

↓

Topic

↓

Consumer A

Consumer B

Consumer C
```

Same event

↓

Many consumers

↓

Event remains stored

---

# Real Example

Suppose a customer buys a laptop.

---

## Using SQS

Customer Order

↓

Queue

↓

Order Processor

↓

Delete

Done.

Only one service processes it.

---

## Using MSK

Customer Order

↓

Kafka Topic

↓

Billing

↓

Inventory

↓

Analytics

↓

Fraud Detection

↓

Email Service

Everyone receives the same event.

---

# Message Lifetime

## SQS

```
Message arrives

↓

Consumer reads

↓

Deleted
```

Gone forever.

---

## MSK

```
Message arrives

↓

Consumer A reads

↓

Consumer B reads

↓

Consumer C reads

↓

Still stored
```

Kafka keeps it.

---

# Multiple Consumers

## SQS

Suppose:

```
Queue

↓

Worker 1

Worker 2

Worker 3
```

Only ONE worker processes each message.

Example:

```
Message 1 → Worker 1

Message 2 → Worker 2

Message 3 → Worker 3
```

Messages are distributed for load balancing.

---

## MSK

One event:

```
Order Created
```

Can be read by

```
Billing

Analytics

Inventory

Fraud Detection

Shipping
```

All of them.

---

# Replay

## SQS

Can you replay yesterday's messages?

❌ No

Already deleted.

---

## MSK

Can you replay yesterday's events?

✅ Yes

Kafka stores them.

---

# Ordering

## SQS

Standard Queue

❌ Best effort ordering

FIFO Queue

✅ Ordering guaranteed

---

## MSK

Ordering is guaranteed **within each partition**.

---

# Storage

## SQS

Temporary message storage.

---

## MSK

Long-term event storage.

Stored on EBS.

---

# Throughput

## SQS

Very high.

Perfect for background jobs.

---

## MSK

Very high.

Designed for continuous streaming.

---

# Typical Use Cases

## Amazon SQS

- Order processing
- Image processing
- Email queue
- Background jobs
- Payment processing
- Worker queue

---

## Amazon MSK

- Clickstream
- IoT
- Website events
- Log streaming
- Fraud detection
- Real-time analytics
- Financial trading

---

# Architecture Comparison

## Amazon SQS

```
Web App

↓

SQS Queue

↓

EC2

or

Lambda

↓

Process Job

↓

Delete Message
```

---

## Amazon MSK

```
Website

↓

MSK Topic

↓

Billing

Analytics

Fraud

Inventory

Email

↓

Event remains stored
```

---

# Feature Comparison

| Feature | Amazon SQS | Amazon MSK |
| --- | --- | --- |
| Type | Message Queue | Event Streaming Platform |
| Technology | AWS Native | Apache Kafka |
| Main Purpose | Queue tasks | Stream events |
| Consumers | One consumer per message | Many consumers can read the same event |
| Replay | ❌ No | ✅ Yes |
| Storage | Temporary | Long-term (EBS) |
| Delete After Read | ✅ Yes | ❌ No |
| Ordering | FIFO only | Per partition |
| Best For | Background processing | Real-time streaming |

---

# Exam Scenarios

### Scenario 1

> "Process uploaded images one by one."
> 

✅ **Amazon SQS**

---

### Scenario 2

> "Multiple applications need to process every order event."
> 

✅ **Amazon MSK**

---

### Scenario 3

> "Replay yesterday's events for analytics."
> 

✅ **Amazon MSK**

---

### Scenario 4

> "Decouple two applications using a queue."
> 

✅ **Amazon SQS**

---

### Scenario 5

> "Existing Apache Kafka application."
> 

✅ **Amazon MSK**

---

# Memory Trick

### 📬 Amazon SQS = Post Office Queue

A package arrives.

One employee delivers it.

Done.

---

### 📺 Amazon MSK = TV News Channel

One news event is broadcast.

Millions of viewers watch the same event.

The broadcast is also recorded, so people can watch it later.

---

# One-Line Summary (AWS SAA)

| Service | Remember |
| --- | --- |
| **Amazon SQS** | **Queue jobs/tasks. One message → one consumer → message deleted.** |
| **Amazon MSK** | **Stream events. One event → many consumers → event retained for replay.** |

## 🎯 Quick Decision Rule

Ask yourself:

- **Do I want one worker to process a task and then discard it?** → **SQS**
- **Do I want multiple systems to consume the same event now or later?** → **MSK**
