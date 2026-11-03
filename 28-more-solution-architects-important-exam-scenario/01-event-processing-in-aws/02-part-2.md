# 20. EventBridge Real Example

Imagine your company has:

```
EC2
S3
DynamoDB
CloudTrail
Custom applications
```

You want:

> Whenever a specific production resource changes, notify the security team.
> 

Architecture:

```
AWS Services
     │
     ▼
EventBridge
     │
     │ Rule
     ▼
    SNS
     │
     ▼
Security Team
```

---

# 21. EventBridge Advanced Filtering

Suppose S3 generates:

```
ObjectCreated
ObjectRemoved
ObjectRestored
```

You only care about:

```
ObjectCreated
AND
file size > 10 MB
AND
prefix = /uploads/
```

EventBridge rules can perform sophisticated event filtering.

### Memory trick

> **EventBridge = Event traffic controller.**
> 

It doesn't necessarily do the work. It **decides where the event should go**.

---

# 22. EventBridge Archive & Replay

This is an important differentiator.

```
Events
  │
  ▼
EventBridge
  │
  ├── Target
  │
  └── Archive
         │
         ▼
       Replay
```

### Scenario

You had a bug in your Lambda application.

Some events happened while the application was broken.

Instead of losing those events, EventBridge can use **archived events for replay**.

### Exam clue

> "Replay past events"
> 

👉 **EventBridge**

---

# 23. EventBridge + CloudTrail

CloudTrail records AWS API activity.

Example:

```
User
 │
 ▼
DeleteTable API
 │
 ▼
DynamoDB
```

CloudTrail records:

```
DeleteTable
```

EventBridge can detect that event:

```
CloudTrail
    │
    ▼
EventBridge
    │
    ▼
SNS
    │
    ▼
Security Team
```

### Scenario

> Alert administrators whenever someone deletes a DynamoDB table.
> 

Use:

**CloudTrail + EventBridge + SNS**

---

# 24. EventBridge vs CloudTrail

Don't confuse them.

| Service | Role |
| --- | --- |
| **CloudTrail** | Records AWS API activity |
| **EventBridge** | Reacts to/routs events |
| **SNS** | Sends notification |
| **Lambda** | Executes code |

Architecture:

```
Someone calls API
       │
       ▼
   CloudTrail
       │
       ▼
  EventBridge
       │
       ▼
      SNS
       │
       ▼
     Alert
```

### Memory trick

> **CloudTrail = Who did what?**
> 
> 
> **EventBridge = What should happen because of it?**
> 

---

# 25. API Gateway

Amazon API Gateway is a managed service for creating and exposing APIs.

```
Client
   │
   ▼
API Gateway
   │
   ▼
Lambda
   │
   ▼
DynamoDB
```

It acts as the **front door** for APIs.

### Example

Mobile application:

```
Mobile App
    │
    │ HTTPS
    ▼
API Gateway
    │
    ▼
Lambda
    │
    ▼
DynamoDB
```

The mobile application doesn't directly access DynamoDB.

---

# 26. API Gateway + Lambda

Very common serverless architecture:

```
Client
  │
  ▼
API Gateway
  │
  ▼
Lambda
  │
  ▼
DynamoDB
```

Think:

> **API Gateway = front door**
> 
> 
> **Lambda = backend worker**
> 

---

# 27. API Gateway + Kinesis

Your notes mention:

```
Client
  │
  ▼
API Gateway
  │
  ▼
Kinesis Data Streams
  │
  ▼
Kinesis Data Firehose
  │
  ▼
S3
```

### Example

IoT devices send telemetry:

```
Device 1 ─┐
Device 2 ─┼──► API Gateway
Device 3 ─┘          │
                     ▼
              Kinesis Data Streams
                     │
                     ▼
             Kinesis Data Firehose
                     │
                     ▼
                     S3
```

---

# 28. Kinesis Data Streams vs Firehose

This is highly testable.

| Feature | Kinesis Data Streams | Kinesis Data Firehose |
| --- | --- | --- |
| Purpose | Real-time streaming | Deliver streaming data |
| Processing | Custom consumers | Managed delivery |
| Real-time | ⭐ Very low latency | Near real-time buffering |
| Data processing | More control | Less control |
| Scaling | You manage shards / capacity mode | Fully managed |
| Destination | Consumers can process data | S3, Redshift, OpenSearch, etc. |
| Use case | Custom real-time processing | Load streaming data into storage/analytics |

### Memory trick

> **Streams = Streaming + processing**
> 
> 
> **Firehose = Deliver data somewhere**
> 

---

# 29. The Complete Architecture

A very realistic architecture could look like:

```
                         Users
                           │
                           ▼
                     API Gateway
                           │
                           ▼
                        Lambda
                           │
                           ▼
                          SNS
                    ┌──────┼───────┐
                    ▼      ▼       ▼
                   SQS    SQS     SQS
                    │      │       │
                    ▼      ▼       ▼
                 Lambda Lambda   Lambda
                    │      │       │
                    ▼      ▼       ▼
                   DB    S3     Other App
```

Meanwhile:

```
S3
 │
 │ ObjectCreated
 ▼
EventBridge
 │
 ├── Lambda
 ├── SQS
 ├── SNS
 └── Step Functions
```

And for streaming:

```
IoT / Applications
       │
       ▼
API Gateway
       │
       ▼
Kinesis Data Streams
       │
       ▼
Kinesis Firehose
       │
       ▼
S3 / Redshift / OpenSearch
```

---

# 30. SQS vs SNS vs EventBridge vs Lambda

This is probably the **most useful comparison for your exam**.

| If the question says... | Think |
| --- | --- |
| Queue messages | **SQS** |
| Decouple applications | **SQS** |
| Buffer traffic spikes | **SQS** |
| Process asynchronously | **SQS** |
| Retry failed messages | **SQS** |
| Dead-letter queue | **SQS** |
| Preserve ordering | **SQS FIFO** |
| One message to many subscribers | **SNS** |
| Fan-out | **SNS** |
| Publish/subscribe | **SNS** |
| Event routing | **EventBridge** |
| Advanced event filtering | **EventBridge** |
| Archive/replay events | **EventBridge** |
| React to AWS events | **EventBridge** |
| Run code in response to event | **Lambda** |
| API front door | **API Gateway** |
| Real-time streaming | **Kinesis Data Streams** |
| Deliver streaming data to S3/Redshift/OpenSearch | **Kinesis Firehose** |

---

# 31. The Most Important Decision Tree

When you see an event-driven question, think:

```
                    What do I need?
                         │
          ┌──────────────┼──────────────┐
          │              │              │
        Queue          Fan-out       Event Routing
          │              │              │
          ▼              ▼              ▼
         SQS            SNS        EventBridge
          │
          │
     Need ordering?
       /       \
     YES        NO
      │          │
      ▼          ▼
   SQS FIFO   Standard SQS
```

Then:

```
Need to execute code?
        │
        ▼
      Lambda
```

```
Need API endpoint?
        │
        ▼
    API Gateway
```

```
Need real-time stream?
        │
        ▼
 Kinesis Data Streams
```

```
Need to deliver stream
to S3 / Redshift / OpenSearch?
        │
        ▼
 Kinesis Firehose
```

---

# 32. SQS + SNS + EventBridge — Easy Mental Model

Imagine a restaurant.

### SQS = Waiting line

```
Customers
   ↓
Queue
   ↓
Workers serve them
```

**Work waits until somebody processes it.**

---

### SNS = Announcement speaker

```
Announcement
     ↓
 ┌───┼───┐
 ↓   ↓   ↓
Team Team Team
```

**One message reaches many subscribers.**

---

### EventBridge = Traffic controller

```
Many events
     ↓
EventBridge
     ↓
"Where should each event go?"
```

**It examines events and routes them according to rules.**

---

### Lambda = Worker

```
Event
  ↓
Lambda
  ↓
Do something
```

**It executes code without managing servers.**

---

# 33. S3 Event-Driven Architecture

### Simple

```
S3
 │
 │ ObjectCreated
 ▼
Lambda
```

Use when:

> "When an object is uploaded, execute Lambda."
> 

---

### Fan-out

```
S3
 │
 ▼
SNS
 ├── SQS → Thumbnail
 ├── SQS → Virus Scan
 └── SQS → Metadata
```

Use when:

> "When an object is uploaded, multiple independent systems need to process it."
> 

---

### Advanced

```
S3
 │
 ▼
EventBridge
 │
 ├── Rule 1 → Lambda
 ├── Rule 2 → SQS
 ├── Rule 3 → SNS
 └── Rule 4 → Step Functions
```

Use when:

> "We need advanced filtering, multiple destinations, archive/replay, or complex event routing."
> 

---

# 34. Important Exam Traps ⚠️

### Trap 1

> "Application must not lose messages if consumer is temporarily unavailable."
> 

❌ SNS alone

✅ **SQS**

Because SQS provides persistent queueing/buffering.

---

### Trap 2

> "Send the same event to several independent applications."
> 

❌ Just SQS

✅ **SNS fan-out**, often **SNS → multiple SQS queues**

---

### Trap 3

> "Messages must be processed in order."
> 

❌ Standard SQS

✅ **SQS FIFO**

---

### Trap 4

> "Need advanced filtering of AWS events."
> 

❌ Basic S3 Event Notification

✅ **EventBridge**

---

### Trap 5

> "Need to replay historical events."
> 

❌ SQS

❌ SNS

✅ **EventBridge Archive + Replay**

---

### Trap 6

> "Someone calls DeleteTable and security team should immediately receive an alert."
> 

Think:

```
CloudTrail
   ↓
EventBridge
   ↓
SNS
   ↓
Alert
```

---

### Trap 7

> "Real-time streaming data needs custom consumers."
> 

✅ **Kinesis Data Streams**

Not Firehose.

---

### Trap 8

> "Stream data needs to be delivered to S3 with minimal management."
> 

✅ **Kinesis Data Firehose**

---

# 35. Final SAA-C03 Memory Table

| Service | One-line meaning | Memory |
| --- | --- | --- |
| **SQS** | Managed message queue | **Wait** |
| **SQS FIFO** | Ordered/deduplicated queue | **Order** |
| **DLQ** | Holds repeatedly failed messages | **Failed parking lot** |
| **SNS** | Pub/sub notification service | **Broadcast** |
| **SNS + SQS** | Fan-out to independent workers | **Broadcast + Buffer** |
| **Lambda** | Serverless code execution | **Worker** |
| **EventBridge** | Event routing with rules | **Traffic controller** |
| **S3 Events** | S3 announces object events | **S3 → action** |
| **API Gateway** | Managed API front door | **Front door** |
| **Kinesis Streams** | Real-time streaming data | **Process streams** |
| **Kinesis Firehose** | Managed streaming delivery | **Deliver streams** |

## 🔥 The one-minute revision

Remember this:

```
SQS        → Queue / Buffer / Decouple
SQS FIFO   → Ordering
DLQ        → Failed messages

SNS        → Fan-out / Broadcast
SNS + SQS  → One event → many independent workers

Lambda     → Execute code

EventBridge
           → Event routing
           → Advanced filtering
           → Archive + Replay
           → AWS events

S3 Events  → Simple S3 event trigger

API Gateway
           → API front door

Kinesis Streams
           → Real-time stream processing

Firehose   → Deliver streams to destinations
```

### 🧠 Master memory trick

> **SQS = WAIT**
> 
> 
> **SNS = TELL EVERYONE**
> 
> **EventBridge = DECIDE WHERE IT GOES**
> 
> **Lambda = DO THE WORK**
> 
> **API Gateway = LET USERS IN**
> 
> **Kinesis Streams = PROCESS THE STREAM**
> 
> **Firehose = DELIVER THE STREAM**
> 

For SAA-C03, if you can identify those **seven roles from the wording of a scenario**, a large portion of event-driven architecture questions becomes much easier.
