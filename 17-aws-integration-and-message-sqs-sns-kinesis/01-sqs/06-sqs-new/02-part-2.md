# 31. FIFO Throughput — Current Exam Awareness

Older study material often says:

```
300 messages/sec
3,000 messages/sec with batching
```

Those are the traditional/default FIFO limits.

AWS now also supports **high-throughput FIFO**, which can significantly increase throughput depending on Region/configuration. AWS documents high-throughput FIFO as using message-group-level throughput and recommends multiple message groups for parallelism.

For the SAA exam, don't memorize only the old number. Focus on the architectural distinction:

> **FIFO = ordering + deduplication**
> 

> **Standard = very high throughput + best-effort ordering**
> 

---

# 32. SQS vs SNS

This is a **very common exam comparison**.

|  | **SQS** | **SNS** |
| --- | --- | --- |
| Type | Queue | Pub/Sub topic |
| Main idea | Store messages for consumers | Push/fan-out messages to subscribers |
| Consumer model | Consumer polls/receives | SNS pushes to subscribers |
| Message persistence | Yes, until processed/retention expires | Not primarily a message queue |
| Multiple consumers | Workers compete for messages | Multiple subscribers receive the message |
| Buffering | ⭐ Excellent | Not its primary purpose |
| Fan-out | Not primary purpose | ⭐ Excellent |
| Ordering | FIFO available | FIFO topics available |
| Common use | Decouple worker systems | Send one event to many systems |

### Remember

```
SQS = Queue
SNS = Publish/Subscribe
```

---

# 33. SNS → SQS Fan-Out

Very important architecture:

```
                    SNS Topic
                  /     |      \
                 ↓      ↓       ↓
               SQS    SQS     Lambda
                ↓
             Consumer
```

One message:

```
Order Created
```

can be delivered to multiple SQS queues.

Example:

```
SNS
 │
 ├── SQS → Billing
 │
 ├── SQS → Shipping
 │
 └── SQS → Analytics
```

Each system gets its own copy.

### Exam clue

> **One event → many independent consumers → SNS + SQS fan-out**
> 

---

# 34. SQS vs Kinesis

Another useful distinction:

|  | SQS | Kinesis Data Streams |
| --- | --- | --- |
| Main purpose | Message queue / decoupling | Streaming data |
| Consumer model | Messages consumed/deleted | Records remain for retention period |
| Replay | Not typical queue behavior | ⭐ Replay available |
| Ordering | FIFO queue | Ordering within shard |
| Typical use | Background jobs | Real-time streaming/analytics |
| Example | Process orders | Process clickstream |

### Remember

> **Task/job → SQS**
> 

> **Streaming data → Kinesis**
> 

---

# 35. SQS vs EventBridge

|  | SQS | EventBridge |
| --- | --- | --- |
| Main role | Queue/buffer | Event bus |
| Store work until consumer processes it | ⭐ Yes | Not its primary role |
| Decouple applications | ⭐ Yes | ⭐ Yes |
| Event routing/filtering | Limited | ⭐ Excellent |
| Multiple event targets | Not primary | ⭐ |
| Replay/archive | Not typical | ⭐ |
| Use case | Worker queue | Event-driven architecture |

---

# 36. SQS Standard — Perfect Scenario

### Scenario

A video-processing company receives thousands of uploads.

Each upload creates a processing job.

Order doesn't matter.

Architecture:

```
Upload
  ↓
Application
  ↓
SQS Standard
  ↓
EC2/Lambda workers
  ↓
Process video
```

Why Standard?

- Very high throughput
- Decoupling
- Buffering
- Order isn't important

---

# 37. SQS FIFO — Perfect Scenario

### Scenario

A banking application processes transactions.

Order is critical:

```
Transaction 1
Transaction 2
Transaction 3
```

Architecture:

```
Application
    ↓
SQS FIFO
    ↓
Transaction Processor
    ↓
Database
```

Why FIFO?

- Ordering
- Duplicate protection
- Message groups for ordered streams

---

# 38. SQS Buffer — Perfect Scenario

### Scenario

Your application receives:

```
10,000 requests/sec
```

Database can handle:

```
2,000 requests/sec
```

Use:

```
Application
    ↓
SQS
    ↓
Workers
    ↓
Database
```

SQS absorbs the burst.

---

# 39. SQS + ASG — Perfect Scenario

### Scenario

Your queue normally has:

```
1,000 messages
```

but sometimes reaches:

```
100,000 messages
```

Use:

```
SQS
 ↓
CloudWatch
 ↓
Alarm
 ↓
ASG
 ↓
More EC2 workers
```

Queue length becomes a scaling signal.

---

# 40. SQS + Lambda — Perfect Scenario

### Scenario

You don't want to manage EC2 worker servers.

```
Application
    ↓
SQS
    ↓
Lambda
    ↓
DynamoDB
```

Lambda processes queued messages.

---

# 41. SQS + DLQ — Perfect Scenario

### Scenario

Some messages contain invalid data and repeatedly fail.

```
Main Queue
    ↓
Consumer
    ↓
Repeated failure
    ↓
DLQ
```

Developers can investigate the failed messages without blocking the main processing pipeline.

---

# 42. SQS Configuration Master Table

| Feature | Default | Maximum / Important Point | Why You Care |
| --- | --- | --- | --- |
| Message retention | **4 days** | **14 days** | How long unprocessed messages remain |
| Visibility timeout | **30 sec** | **12 hours** | Prevents another consumer from seeing a message temporarily |
| Long polling | 0 sec = short polling | **20 sec** | Reduce empty ReceiveMessage calls |
| Message delay | 0 sec | **15 min** | Delay message delivery |
| Message size | 1 KiB–1 MiB | **1 MiB** | Large payloads may need S3 |
| ReceiveMessage | — | Up to **10 messages** | Batch processing |
| Standard throughput | Very high | Nearly unlimited API throughput | High-volume workloads |
| FIFO ordering | — | Strict within message group | Order-sensitive processing |
| FIFO queue name | — | Must end `.fifo` | Identifies FIFO queue |
| FIFO MessageGroupId | Required | No quota on number of groups | Parallel ordered streams |
| DLQ | Optional | Configure redrive policy | Failed/poison messages |
| Encryption in transit | HTTPS | — | Protect data in transit |
| Encryption at rest | Optional/configurable | KMS / SQS-managed options | Protect stored messages |

---

# 43. The MOST Important SQS Concepts

| If question says... | Think... |
| --- | --- |
| **Decouple application tiers** | SQS |
| **Buffer requests** | SQS |
| **Huge number of messages** | Standard SQS |
| **Order must be preserved** | FIFO |
| **Duplicate messages must be prevented** | FIFO |
| **Messages for same customer must remain ordered** | FIFO + MessageGroupId |
| **Consumer processing takes longer than expected** | Visibility Timeout |
| **Message processed twice** | Visibility Timeout / at-least-once delivery |
| **Reduce empty polling requests** | Long Polling |
| **Wait up to 20 seconds for messages** | Long Polling |
| **Repeatedly failing messages** | Dead-Letter Queue |
| **Scale EC2 workers based on backlog** | CloudWatch + ASG |
| **Serverless queue consumer** | SQS + Lambda |
| **One event → many systems** | SNS + SQS |
| **Streaming + replay** | Kinesis |
| **Event routing/filtering** | EventBridge |
| **Large payload > 1 MiB** | S3 + SQS reference |
| **Encrypt messages at rest** | KMS |
| **Control API access** | IAM |
| **Cross-account queue access** | SQS Resource Policy |

---

# 44. MASTER SQS DECISION TABLE ⭐⭐⭐⭐⭐

| Requirement | Best SQS Concept | Example |
| --- | --- | --- |
| Separate frontend from backend | **SQS** | Web app → SQS → workers |
| Handle sudden traffic spikes | **SQS as buffer** | 50K requests arrive, workers process gradually |
| Process massive volume | **Standard Queue** | Image/video processing |
| Strict message ordering | **FIFO Queue** | Financial transactions |
| Avoid duplicate processing | **FIFO + deduplication** | Payment/order processing |
| Order per customer but parallelize different customers | **FIFO + MessageGroupId** | Customer transactions |
| Worker needs more processing time | **Change Visibility Timeout** | 10-minute job |
| Reduce polling/API calls | **Long Polling** | Worker waits for messages |
| Failed messages need investigation | **DLQ** | Invalid order message |
| Increase EC2 processing capacity | **ASG based on queue length** | Queue backlog grows |
| No EC2 management | **Lambda consumer** | SQS → Lambda |
| Send same event to many systems | **SNS → SQS fan-out** | Billing + Shipping + Analytics |
| Data stream with replay | **Kinesis** | Clickstream |
| Route events based on rules | **EventBridge** | Event → different targets |
| Store very large payload | **S3 + SQS reference** | 500-MB file |
| Secure API access | **IAM** | EC2 role can ReceiveMessage |
| Cross-account access | **SQS Resource Policy** | Account A sends to Account B queue |
| Encrypt stored messages | **KMS / SQS encryption** | Sensitive financial messages |

---

# 45. SQS Flow — Memorize This

### Standard Queue

```
Producer
   │
   │ SendMessage
   ▼
┌────────────────────┐
│   SQS Standard     │
│                    │
│ Message 1          │
│ Message 2          │
│ Message 3          │
└────────────────────┘
   │
   │ ReceiveMessage
   ▼
Consumer
   │
   │ Process
   ▼
DeleteMessage
```

### Failed processing

```
SQS
 ↓
Consumer
 ↓
FAIL
 ↓
Visibility Timeout
 ↓
Retry
 ↓
FAIL
 ↓
MaxReceiveCount reached
 ↓
DLQ
```

### Scaling

```
SQS
 ↓
Queue Length
 ↓
CloudWatch
 ↓
Alarm
 ↓
ASG
 ↓
More Consumers
```

---

# 46. Final SAA-C03 Memory Map 🧠

```
                         AMAZON SQS
                             │
             ┌───────────────┴───────────────┐
             │                               │
        STANDARD                          FIFO
             │                               │
      High throughput                  Ordering
      Best-effort order                Deduplication
      At-least-once                    MessageGroupId
             │                               │
             └───────────────┬───────────────┘
                             │
                      COMMON FEATURES
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
   Visibility            Long Polling            DLQ
    Timeout                                      │
        │                                        │
   Prevent duplicate                       Failed messages
   simultaneous processing                 investigation
        │
        └───────────────┐
                        │
                   SQS BUFFER
                        │
              ┌─────────┴─────────┐
              │                   │
             ASG                Lambda
              │                   │
        Scale workers       Serverless worker
```

## 🔥 One-Minute Exam Revision

> **SQS = queue + decoupling + buffering**
> 

> **Standard = high throughput, best-effort ordering, at-least-once**
> 

> **FIFO = strict ordering + deduplication**
> 

> **MessageGroupId = ordering/parallelism within FIFO**
> 

> **Visibility Timeout = temporarily hide a received message**
> 

> **ChangeMessageVisibility = give the consumer more processing time**
> 

> **Long Polling = wait for messages, reduce empty API calls**
> 

> **DLQ = repeatedly failed messages**
> 

> **SQS + ASG = scale workers based on queue backlog**
> 

> **SQS + Lambda = serverless message processing**
> 

> **SNS + SQS = fan-out to multiple independent consumers**
> 

> **S3 + SQS = handle payloads larger than SQS's 1 MiB message limit**
> 

> **Kinesis = streaming/replay**
> 

> **EventBridge = event routing**
> 

These are the core SQS patterns that repeatedly matter in **AWS SAA-C03 scenario questions**.
