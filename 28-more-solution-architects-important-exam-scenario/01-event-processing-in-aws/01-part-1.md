# Part : 1

Here is a **SAA-C03 exam-focused summary** of this whole section. The goal is to make the architecture and the **“which service should I choose?”** logic clear, rather than memorizing isolated facts.

# AWS Event-Driven Architecture — SAA-C03 Complete Revision Note

## 1. The Big Picture

A very common AWS architecture is:

```
User / Application
        │
        ▼
     Producer
        │
        ▼
   ┌─────────┐
   │   SNS   │  ← Fan-out / notification
   └────┬────┘
        │
   ┌────┼──────────────┐
   ▼    ▼              ▼
  SQS  Lambda        SQS
   │                   │
   ▼                   ▼
Worker / App        Worker / App
```

Or:

```
S3
 │
 │ ObjectCreated
 ▼
EventBridge / S3 Event Notification
 │
 ├── Lambda
 ├── SQS
 ├── SNS
 ├── Step Functions
 └── Kinesis
```

### The core idea

| Service | Think of it as |
| --- | --- |
| **Lambda** | Worker that runs code |
| **SQS** | Queue that stores work |
| **SNS** | Broadcast / fan-out |
| **EventBridge** | Event router + filtering |
| **S3 Event Notifications** | S3 tells another service that something happened |
| **API Gateway** | Front door/API for applications |
| **Kinesis Data Streams** | Real-time streaming pipeline |
| **Kinesis Data Firehose** | Deliver streaming data to destinations |

---

# 2. Synchronous vs Asynchronous

This is extremely important for the exam.

### Synchronous

```
Client
  │
  ▼
Application
  │
  ▼
Database
  │
  ▼
Response
  │
  ▼
Client
```

The caller **waits for the response**.

Example:

> User requests account balance → application checks database → application returns balance.
> 

---

### Asynchronous

```
Producer
   │
   ▼
  SQS
   │
   ▼
Consumer
```

The producer **doesn't have to wait** for the consumer to finish.

Example:

> User uploads a video → application puts a job into SQS → user gets an immediate response → worker processes video later.
> 

### Exam clue

If the question says:

- decouple applications
- process later
- absorb traffic spikes
- asynchronous
- retry failed processing
- don't lose messages

👉 **Think SQS.**

---

# 3. Amazon SQS

## What is SQS?

**Amazon Simple Queue Service** is a fully managed message queue used to **decouple applications**.

```
Producer
   │
   │ Message
   ▼
┌──────────────┐
│     SQS      │
│    Queue     │
└──────┬───────┘
       │
       ▼
    Consumer
```

### Real-world example

Imagine an online store.

10,000 customers place orders at once.

Without SQS:

```
10,000 requests
      │
      ▼
Order Processing
      │
   OVERLOAD ❌
```

With SQS:

```
10,000 requests
      │
      ▼
     SQS
      │
      ├── Order 1
      ├── Order 2
      ├── Order 3
      ├── ...
      └── Order 10,000
              │
              ▼
       Worker processes
       at manageable rate
```

The queue **absorbs the traffic spike**.

---

# 4. Why SQS?

SQS provides:

- Decoupling
- Asynchronous processing
- Buffering
- Retry capability
- Scaling
- Fault tolerance

### Memory trick

> **SQS = Store the work and process it later.**
> 

---

# 5. SQS + Lambda

This is a very common SAA-C03 architecture.

```
Producer
   │
   ▼
 SQS Queue
   │
   ▼
 Lambda
   │
   ▼
Database / S3 / etc.
```

Lambda can poll the SQS queue and process messages.

### Example

```
Website
   │
   ▼
SQS
   │
   ▼
Lambda
   │
   ▼
DynamoDB
```

Customer submits an order → SQS stores order → Lambda processes it → DynamoDB stores order information.

### Why?

Because the website doesn't need to wait for the database-processing workflow.

---

# 6. What happens when processing fails?

Suppose:

```
SQS
 │
 ▼
Lambda
 │
 ❌ Failure
```

The message can be retried.

```
Try
 ↓
Fail
 ↓
Retry
 ↓
Fail
 ↓
Retry
 ↓
Fail
 ↓
DLQ
```

---

# 7. Dead-Letter Queue — DLQ

A **Dead-Letter Queue** stores messages that could not be successfully processed after the configured retry attempts.

```
SQS
 │
 ▼
Lambda
 │
 ├── Success → Done ✅
 │
 └── Failure
       │
       ▼
    Retry
       │
       ▼
   Still fails
       │
       ▼
      DLQ
```

### Why DLQ?

Instead of repeatedly processing a bad message forever, isolate it so you can investigate it.

### Example

An order contains malformed data:

```
Order → SQS → Lambda
                │
                ❌
                │
             retries
                │
                ▼
               DLQ
```

### Memory trick

> **DLQ = Failed messages' parking lot.**
> 

---

# 8. SQS FIFO

FIFO = **First-In-First-Out**

```
Message 1
Message 2
Message 3
Message 4

       ↓

Message 1
Message 2
Message 3
Message 4
```

Use FIFO when **ordering matters**.

### Example

Bank transaction:

```
Deposit $100
     ↓
Withdraw $50
     ↓
Withdraw $20
```

You don't want these operations randomly reordered.

### FIFO also helps with deduplication

If the same message is accidentally submitted twice, FIFO queues can provide **deduplication capabilities**.

### Exam clue

If you see:

- strict ordering
- order must be preserved
- exactly-once processing requirement / duplicate prevention
- sequential processing

👉 **Think SQS FIFO.**

---

# 9. Standard SQS vs FIFO

| Feature | Standard SQS | FIFO SQS |
| --- | --- | --- |
| Ordering | Best-effort | Ordered |
| Duplicate messages | Possible | Deduplication supported |
| Throughput | Very high | Lower than Standard, though FIFO supports high-throughput modes |
| Main use | General queue | Ordering-sensitive workloads |
| Exam keyword | Scalability | Ordering |

### Easy memory

> **Standard = scale**
> 
> 
> **FIFO = order**
> 

---

# 10. SNS — Simple Notification Service

SNS is primarily a **publish/subscribe messaging service**.

Think:

> **One message → many subscribers**
> 

```
              ┌── SQS
              │
Producer → SNS├── Lambda
              │
              ├── Email
              │
              └── HTTP endpoint
```

### Example

Your application detects a security incident.

You want:

- Security team notified
- Logging system notified
- Processing system notified
- Another application notified

Instead of sending the event individually:

```
Application
    │
    ▼
   SNS
  / │  \
 ▼  ▼   ▼
SQS Lambda Email
```

That's **fan-out**.

---

# 11. SNS Fan-Out Pattern

Very important for SAA-C03.

Suppose an image is uploaded.

You need:

1. Thumbnail generation
2. Virus scanning
3. Metadata processing

Architecture:

```
                 S3
                  │
                  ▼
                 SNS
              /    |    \
             ▼     ▼     ▼
           SQS    SQS    SQS
            │      │      │
            ▼      ▼      ▼
        Thumbnail Virus  Metadata
        Worker    Scan   Worker
```

### Why SNS + SQS?

SNS broadcasts the event.

SQS independently buffers each application's workload.

### Key advantage

Each consumer gets its **own copy**.

If the virus scanner is temporarily down, it doesn't prevent the thumbnail processor from receiving its messages.

### Memory trick

> **SNS = Send to Many**
> 
> 
> **SQS = Save for Later**
> 

---

# 12. SNS vs SQS

| Feature | SNS | SQS |
| --- | --- | --- |
| Main purpose | Publish/Subscribe | Queue |
| Pattern | One-to-many | One-to-one / worker queue |
| Stores messages | Not primarily a queue | Yes |
| Consumers | Multiple subscribers | Consumers pull/process messages |
| Fan-out | ⭐ Excellent | Not its main purpose |
| Decoupling | Yes | ⭐ Excellent |
| Buffering | No | ⭐ Yes |
| Retry workload | Usually use SQS subscriber | ⭐ Yes |
| Example | Send notification to 10 systems | Process orders asynchronously |

### Exam shortcut

**"One message to multiple systems"**

👉 SNS

**"Store messages until worker processes them"**

👉 SQS

**"One message → multiple independent queues"**

👉 SNS + SQS Fan-Out

---

# 13. Lambda + SNS

```
SNS
 │
 ▼
Lambda
 │
 ▼
Application
```

SNS publishes an event and Lambda receives it.

Example:

```
Order Created
     │
     ▼
    SNS
     │
     ▼
  Lambda
     │
     ▼
Send confirmation
```

---

# 14. Lambda + SQS

```
Application
    │
    ▼
   SQS
    │
    ▼
 Lambda
    │
    ▼
Database
```

Use this when you need:

- buffering
- asynchronous processing
- retries
- decoupling
- handling traffic spikes

---

# 15. SNS + SQS + Lambda

This is an extremely useful architecture to recognize.

```
                    SNS
                     │
            ┌────────┴────────┐
            ▼                 ▼
          SQS A             SQS B
            │                 │
            ▼                 ▼
         Lambda A          Lambda B
            │                 │
            ▼                 ▼
        Database A        Database B
```

### Scenario

An order is created.

The event needs to go to:

- Inventory system
- Billing system
- Shipping system

Use:

```
Order Service
     │
     ▼
    SNS
   / | \
  ▼  ▼  ▼
 SQS SQS SQS
  │   │   │
  ▼   ▼   ▼
Inv Bill Ship
```

This is the classic **Fan-Out Pattern**.

---

# 16. S3 Event Notifications

Amazon S3 can generate events when something happens to an object.

Examples:

- `ObjectCreated`
- `ObjectRemoved`
- `ObjectRestore`
- Replication events

Architecture:

```
User
 │
 ▼
S3 Bucket
 │
 │ ObjectCreated
 ▼
Lambda
```

### Example

User uploads:

```
photo.jpg
```

S3 event:

```
ObjectCreated
```

Triggers Lambda:

```
S3
 │
 ▼
Lambda
 │
 ▼
Generate thumbnail
```

---

# 17. S3 Event Filtering

You can filter events.

For example:

```
S3 Bucket
 │
 ├── *.jpg → Lambda
 ├── *.png → Lambda
 └── *.pdf → SQS
```

Or use prefixes.

Example:

```
/uploads/images/
```

Only objects matching the filter trigger the target.

---

# 18. S3 Event Notifications vs EventBridge

This distinction is important.

| Feature | S3 Event Notifications | EventBridge |
| --- | --- | --- |
| Main purpose | S3 events | General event routing |
| Filtering | Basic filtering | ⭐ Advanced filtering |
| Multiple AWS services | Limited compared with EventBridge | ⭐ Excellent |
| Archive events | No | Yes |
| Replay events | No | Yes |
| Complex event rules | Limited | ⭐ Yes |
| Many AWS services | No | ⭐ Yes |
| Best for | Simple S3 event → target | Complex event-driven architecture |

### Exam shortcut

**Simple S3 upload → Lambda**

👉 S3 Event Notification

**Complex event filtering / many destinations / archive & replay**

👉 EventBridge

---

# 19. Amazon EventBridge

Think:

> **EventBridge = intelligent event router**
> 

```
Event Sources
      │
      ▼
 ┌─────────────┐
 │ EventBridge │
 └──────┬──────┘
        │
   Rules / Filters
        │
 ┌──────┼─────────┬──────────┐
 ▼      ▼         ▼          ▼
Lambda  SQS       SNS     Step Functions
```

It receives events and routes them based on rules.
