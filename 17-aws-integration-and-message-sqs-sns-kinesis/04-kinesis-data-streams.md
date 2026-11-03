# Kinesis Data Streams

For the **AWS SAA-C03 exam**, don't memorize every bullet point. Instead, understand the **purpose** of each service. Once you know **when to use Kinesis Data Streams vs Firehose**, most exam questions become straightforward.

---

# First, What is Amazon Kinesis?

Think of **Amazon Kinesis** as a **real-time data pipeline**.

Imagine thousands of events happening every second:

- Users clicking on a website
- IoT sensors sending temperature
- Mobile apps sending logs
- Games sending player actions
- Financial transactions

Instead of storing them later, AWS can process them **immediately**.

```
Thousands of Events
        │
        ▼
     Amazon Kinesis
        │
        ▼
 Process / Analyze / Store
```

---

# Example

Imagine YouTube.

Millions of people are watching videos.

Every second, YouTube receives events like:

```
User A watched video

User B liked video

User C paused video

User D searched
```

Millions of these events come every second.

Instead of waiting until midnight,

they process everything **live**.

That's what Kinesis is designed for.

---

# Amazon Kinesis Family

Think of Kinesis as a family.

```
Amazon Kinesis

├── Data Streams
├── Data Firehose
├── Data Analytics
└── Video Streams
```

For the SAA exam, the two most important are:

- **Kinesis Data Streams (KDS)**
- **Amazon Data Firehose**

---

# Amazon Kinesis Data Streams (KDS)

## Simple Idea

Imagine a notebook.

Every new event is written into it.

```
Event 1

Event 2

Event 3

Event 4

Event 5
```

Applications can read from this notebook.

The notebook **keeps data for some time**.

That's Kinesis Data Streams.

---

## Example

An online shopping website.

Every purchase is recorded.

```
Order 1

Order 2

Order 3

Order 4
```

Three different applications want to read them.

```
Fraud Detection

Recommendation Engine

Analytics
```

Each application reads the stream independently.

---

## Important Concept

Unlike SQS,

**Kinesis keeps the data**.

Imagine today's events.

```
9:00

9:01

9:02

9:03
```

A consumer crashes.

One hour later...

It comes back.

It can still read old events because the data remains in the stream (up to the configured retention period).

This is called **Replay (Reprocessing)**.

---

# Data Retention

Kinesis stores records

Default:

```
24 Hours
```

Can increase up to

```
365 Days
```

Example

```
Monday

Tuesday

Wednesday
```

You realize Wednesday that Monday's data had a bug.

No problem.

Replay Monday's events.

---

# Why Replay is Useful

Imagine

```
10 Million Events
```

Analytics application crashes.

Without replay

```
Lost forever.
```

With Kinesis

```
Just read again.
```

This is one of the biggest advantages.

---

# Data Ordering

Suppose one customer makes three actions.

```
Login

Add to Cart

Checkout
```

They should stay in this order.

Kinesis guarantees ordering **within the same Partition Key**.

Example

Customer ID = 123

```
Login

Add Item

Checkout
```

They stay ordered.

For a different customer, events may be processed independently.

---

# Shards (Very Important)

Think of a **Shard** as a lane on a highway.

```
One Lane

Cars
Cars
Cars
```

Too many cars?

Add another lane.

```
Lane 1

Lane 2

Lane 3
```

More lanes = higher throughput.

In Kinesis:

```
Shard

1 MB/s Write

2 MB/s Read
```

---

# Capacity Modes

## 1. Provisioned Mode

You decide how many lanes (shards) to create.

Example

```
4 Shards
```

Traffic increases.

You manually change to

```
8 Shards
```

You pay for the provisioned shards whether they're busy or idle.

---

## 2. On-Demand Mode

AWS automatically adds lanes.

```
Traffic Low

2 Shards

↓

Traffic High

10 Shards

↓

Traffic Low

3 Shards
```

You don't manage capacity.

AWS does.

---

# Producer and Consumer

Producer

```
Writes Data
```

Consumer

```
Reads Data
```

Example

```
Website
    │
    ▼
Producer

    │
    ▼
Kinesis

    │
    ▼
Consumer
```

---

# KPL

Kinesis Producer Library

Helps applications

```
Write Faster

Batch Records

Retry Automatically
```

Think

> **Optimized sender**
> 

---

# KCL

Kinesis Client Library

Helps consumers

```
Read Efficiently

Load Balance

Checkpoint Progress
```

Think

> **Optimized reader**
> 

---

---
