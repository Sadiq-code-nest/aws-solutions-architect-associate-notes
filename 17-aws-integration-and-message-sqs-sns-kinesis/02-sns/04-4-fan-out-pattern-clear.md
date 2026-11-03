# 4 - Fan-out pattern clear

The **Fan-Out Pattern** is one of the most important messaging patterns in AWS. Once you understand it, many AWS architectures become much easier.

Let's start from **zero knowledge**.

---

# Imagine Real Life

Suppose you are a teacher.

You have **100 students**.

Today you want to announce:

> "Tomorrow's class is cancelled."
> 

### Option 1 (Without Fan-Out)

You call every student one by one.

```
Teacher
   │
   ├── Student 1
   ├── Student 2
   ├── Student 3
   ├── ...
   └── Student 100
```

That's a lot of work.

---

### Option 2 (With Fan-Out)

Instead, you post the message in the class WhatsApp group.

```
Teacher
      │
      ▼
 WhatsApp Group
      │
 ┌────┼────┐
 ▼    ▼    ▼
S1   S2   S3
...
S100
```

Now everyone gets the same message automatically.

This is exactly what **Amazon SNS** does.

---

# What is Fan-Out?

Fan-Out means

> **One message becomes many copies of that message.**
> 

One sender

↓

One SNS Topic

↓

Many receivers

Each receiver gets its own copy.

---

# Why SQS Alone Cannot Do This

Imagine you have only **one SQS Queue**.

```
Producer
     │
     ▼
   SQS Queue
     │
 ┌───┴───┐
 │       │
App A  App B
```

You may think:

> Both App A and App B will receive the message.
> 

❌ Wrong.

Only **one consumer** receives a message.

Example

Queue contains

```
Order #100
```

App A reads it.

```
Queue

(empty)
```

Now App B receives nothing.

Because SQS is designed for **work distribution**, not broadcasting.

Think of a pizza.

```
One pizza

Person A eats it.

Person B can't eat it anymore.
```

---

# Why SNS Exists

SNS is built for broadcasting.

Suppose one order is created.

```
Order #100
```

Three systems need this information.

- Billing Service
- Email Service
- Analytics Service

If you send directly

```
Application
     │
     ├── Billing
     ├── Email
     └── Analytics
```

The application must know every destination.

Whenever a new service is added...

```
SMS Service
```

You must modify the application.

Not good.

---

Instead

```
Application
      │
      ▼
   SNS Topic
      │
 ┌────┼─────┐
 ▼    ▼     ▼
Billing
Email
Analytics
```

The application only knows SNS.

SNS handles everything else.

---

# But Why Add SQS?

SNS sends messages immediately.

Imagine Email Service is down.

```
SNS
 │
 ▼
Email Service ❌
```

The message may not be processed.

Instead, place an SQS queue in front.

```
SNS
 │
 ├────────► Billing Queue
 │
 ├────────► Email Queue
 │
 └────────► Analytics Queue
```

Each service reads from its own queue.

---

# Complete Fan-Out Architecture

```
                Producer
                    │
                    ▼
               SNS Topic
        ┌────────┼─────────┐
        ▼        ▼         ▼
 Billing Q   Email Q   Analytics Q
     │          │          │
     ▼          ▼          ▼
 Billing     Email      Analytics
 Service     Service     Service
```

Notice something.

Every queue receives the **same message**.

SNS creates copies.

Example message

```
Order ID: 100
Customer: John
Amount: $50
```

Billing Queue gets

```
Order ID:100
```

Email Queue gets

```
Order ID:100
```

Analytics Queue gets

```
Order ID:100
```

Three identical copies.

---

# Why Separate Queues?

Suppose

Billing is fast.

```
2 seconds
```

Email is slow.

```
30 seconds
```

Analytics takes

```
5 minutes
```

Each service works independently.

```
Billing Queue
    ↓
 Empty

Email Queue
    ↓
Waiting...

Analytics Queue
    ↓
Waiting...
```

No service blocks another.

---

# Real AWS Example

Imagine an e-commerce website.

Customer buys an iPhone.

```
Order Created
```

What needs to happen?

1. Save order
2. Send confirmation email
3. Reduce inventory
4. Generate invoice
5. Update dashboard
6. Notify warehouse
7. Award loyalty points

Instead of doing everything in one application...

```
Order Service
      │
      ▼
   SNS Topic
      │
 ┌────┼───────────────┬─────────────┬────────────┐
 ▼    ▼               ▼             ▼            ▼
Email Inventory Invoice Dashboard Warehouse Loyalty
Queue     Queue      Queue    Queue      Queue     Queue
```

Every team owns its own queue.

Nobody affects anyone else.

---

# Another AWS Example (S3 Events)

Suppose someone uploads a photo.

```
dog.jpg
```

Many actions are needed.

- Create thumbnail
- Scan for viruses
- Store metadata
- Run AI detection

Flow

```
S3 Bucket
      │
      ▼
   SNS Topic
      │
 ┌────┼─────┬─────────┐
 ▼    ▼     ▼         ▼
Thumbnail Virus AI Metadata
 Queue    Queue Queue Queue
```

Each service receives the upload event.

---

# Why Not Multiple Consumers on One Queue?

Suppose

```
Queue

Image Uploaded
```

Consumer A reads it.

Queue becomes

```
Empty
```

Consumer B

```
Nothing
```

Consumer C

```
Nothing
```

So one queue cannot broadcast.

SNS solves this.

---

# Visual Comparison

## Only SQS

```
Producer
    │
    ▼
 SQS Queue
    │
 ┌──┴──┐
 ▼     ▼
A      B

Only ONE gets message.
```

---

## SNS + SQS (Fan-Out)

```
Producer
      │
      ▼
   SNS Topic
      │
 ┌────┼────┐
 ▼    ▼    ▼
Q1   Q2   Q3
 │    │    │
 ▼    ▼    ▼
A    B    C

Everyone gets a copy.
```

---

# Advantages

✅ One message reaches many systems.

✅ Producer doesn't know who the consumers are.

✅ Each consumer has its own queue.

✅ Services work independently.

✅ One slow service doesn't affect others.

✅ Easy to add new services later.

---

# Easy Trick for AWS Exam

Remember this simple sentence:

> **SQS = One worker gets one task.**
> 

Think of a queue at a bank.

One customer → One cashier.

---

> **SNS = Loudspeaker announcement.**
> 

Everyone hears the same announcement.

---

> **SNS + SQS = Loudspeaker + Personal Mailbox.**
> 

SNS announces the message.

Each SQS queue gets its own copy and stores it until its consumer is ready.

---

## Final Summary

| Service | Think of it as | Who gets the message? |
| --- | --- | --- |
| **SQS** | A task queue | Only **one** consumer receives each message. |
| **SNS** | A loudspeaker or WhatsApp announcement | **All subscribers** receive the message. |
| **SNS + SQS (Fan-Out)** | A loudspeaker that delivers a copy into everyone's personal mailbox | Every subscribed SQS queue gets its own copy, and each service processes it independently. |

**Rule to remember for interviews and the AWS SAA exam:**

- Use **SQS** when **one job should be processed by one worker**.
- Use **SNS** when **one event should notify many systems**.
- Use **SNS + SQS Fan-Out** when **many independent services need to reliably receive and process the same event**.
