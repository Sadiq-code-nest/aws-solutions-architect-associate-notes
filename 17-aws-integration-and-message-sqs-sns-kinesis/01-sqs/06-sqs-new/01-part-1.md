# Amazon SQS — COMPLETE SAA-C03 SPECIAL NOTE

**Amazon SQS (Simple Queue Service)** is a **managed message queue** used mainly to **decouple applications**.

The core idea is extremely simple:

> **Producer sends a message → SQS stores it → Consumer processes it → Consumer deletes it.**
> 

# 1. What Problem Does SQS Solve?

Imagine you have:

```
Users
  ↓
Web Application
  ↓
Database
```

Suddenly, 100,000 users send requests at once.

Your database may not be able to process all requests immediately.

Without SQS:

```
100,000 requests
       ↓
    Database
       ↓
   OVERLOAD ❌
```

With SQS:

```
Users
  ↓
Application
  ↓
SQS Queue
  ↓
Workers
  ↓
Database
```

SQS acts as a **buffer**.

The application can quickly put messages into SQS, and workers process them at their own speed.

### Easy analogy

Think of a restaurant:

```
Customers → Order Counter → Queue → Kitchen
```

The order counter doesn't need to wait for the kitchen to finish one order.

The queue separates the **customer-facing system** from the **processing system**.

---

# 2. Basic SQS Architecture

| Component | Meaning |
| --- | --- |
| **Producer** | Application that sends messages |
| **SQS Queue** | Stores messages temporarily |
| **Consumer** | Application that receives/processes messages |
| **Message** | Data/task stored in the queue |

Example:

```
Producer
(Web App)
    │
    │ SendMessage
    ▼
┌───────────────┐
│   SQS Queue   │
│               │
│ Order #101    │
│ Order #102    │
│ Order #103    │
└───────────────┘
    │
    │ ReceiveMessage
    ▼
Consumer
(EC2/Lambda)
    │
    ▼
Process Order
    │
    ▼
DeleteMessage
```

---

# 3. Producer

A **producer** creates and sends messages to SQS.

Examples:

- Web application
- EC2 application
- Lambda
- Mobile/backend application
- ECS application
- Another AWS service

The producer commonly uses the AWS SDK/API:

```
SendMessage
```

Example message:

```
{
  "orderId":"ORD-1001",
  "customerId":"C-500",
  "amount":250
}
```

SQS stores the message until a consumer processes it and deletes it.

---

# 4. Consumer

A consumer retrieves messages from SQS.

Possible consumers:

```
EC2
Lambda
ECS
On-premises application
Other applications
```

Typical flow:

```
ReceiveMessage
       ↓
Process message
       ↓
Successful?
       ↓
DeleteMessage
```

Important:

> **Receiving a message does NOT delete it.**
> 

The consumer must delete it after successful processing.

---

# 5. Standard Queue vs FIFO Queue

This is one of the **most important SAA-C03 distinctions**.

| Feature | Standard Queue | FIFO Queue |
| --- | --- | --- |
| Main purpose | High-scale messaging | Strict ordering + duplicate protection |
| Ordering | Best effort | **Strict ordering** within message group |
| Delivery | **At least once** | Designed for exactly-once processing |
| Duplicate messages | Possible | Duplicate prevention via deduplication |
| Throughput | Very high / nearly unlimited API throughput | Lower by default, but high-throughput FIFO is available |
| Message Group ID | Not required | **Required** |
| Queue name | Normal name | Must end in `.fifo` |
| Best for | Huge volume, order not important | Financial transactions, orders, sequential processing |

### Exam shortcut

> **Need maximum throughput → Standard**
> 

> **Need ordering / no duplicates → FIFO**
> 

---

# 6. Standard Queue — Important Characteristics

### Standard SQS

```
Producer
   ↓
Standard SQS
   ↓
Many Consumers
```

Characteristics:

- Very high / nearly unlimited throughput
- Unlimited number of messages in the queue
- Default message retention = **4 days**
- Maximum retention = **14 days**
- Maximum message size = **1 MiB**
- At-least-once delivery
- Best-effort ordering

### Scenario

An e-commerce website receives:

```
500,000 image-processing requests
```

Order doesn't matter.

You want maximum throughput.

**Standard SQS is appropriate.**

---

# 7. FIFO Queue

FIFO = **First In, First Out**

Example:

```
Message 1
Message 2
Message 3
Message 4
```

Consumer receives:

```
1 → 2 → 3 → 4
```

This is important when **order matters**.

Example:

```
Create Account
      ↓
Update Account
      ↓
Delete Account
```

You don't want:

```
Delete
 ↓
Create
```

because that would produce an incorrect state.

---

# 8. Message Group ID — FIFO

This is a very important FIFO concept.

Suppose you have:

```
Customer A:
A1
A2
A3

Customer B:
B1
B2
B3
```

You can use:

```
MessageGroupId = CustomerA
MessageGroupId = CustomerB
```

Messages **within the same group** are processed in order.

```
Customer A

A1 → A2 → A3
```

But different groups can be processed in parallel:

```
Customer A → A1 → A2 → A3
                         ↘
Customer B → B1 → B2 → B3
```

### Exam clue

> **FIFO + MessageGroupId = ordered processing per group.**
> 

Having many message groups can also improve FIFO parallelism/high throughput.

---

# 9. FIFO Deduplication

FIFO supports deduplication.

Example:

Producer sends:

```
Order #100
```

Then because of a network retry:

```
Order #100
```

again.

Deduplication can prevent the duplicate from being processed as a separate message.

You can use:

### Explicit Deduplication ID

```
MessageDeduplicationId = ORDER100
```

or:

### Content-Based Deduplication

SQS generates the deduplication ID from the message body.

---

# 10. SQS Message Size

Maximum SQS message size:

> **1 MiB**
> 

If you need to handle a payload larger than that, a common architecture is:

```
Large File
   ↓
Amazon S3
   ↓
SQS
   ↓
Reference to S3 object
   ↓
Consumer
```

The SQS message contains something like:

```
s3://my-bucket/orders/order1001.json
```

rather than the entire large file.

AWS also provides Extended Client Libraries that can store large payloads in S3 and place a reference in SQS.

---

# 11. Message Retention

SQS doesn't store messages forever.

| Setting | Value |
| --- | --- |
| Default retention | **4 days** |
| Maximum | **14 days** |
| Minimum | **60 seconds** |

### Example

Message arrives:

```
Monday 10 AM
```

If retention is 4 days and nobody processes it:

```
Friday 10 AM → message expires
```

### Exam clue

If the requirement says:

> "Keep messages for several months."
> 

SQS alone is **not** the appropriate long-term storage solution.

---

# 12. Visibility Timeout ⭐⭐⭐⭐⭐

One of the most important SQS concepts.

When a consumer receives a message:

```
SQS
 ↓
Consumer receives Message A
```

SQS temporarily makes that message **invisible** to other consumers.

This is called:

> **Visibility Timeout**
> 

Default:

> **30 seconds**
> 

Maximum:

> **12 hours**
> 

---

## Example

Suppose:

```
Visibility Timeout = 30 seconds
```

Consumer receives:

```
Order #100
```

For 30 seconds:

```
Order #100 → invisible
```

The consumer processes it and deletes it:

```
Process
   ↓
DeleteMessage
   ↓
Done ✅
```

---

# 13. What If Processing Takes Too Long?

Suppose:

```
Visibility Timeout = 30 seconds
Processing time = 2 minutes
```

After 30 seconds:

```
Message becomes visible again
```

Another consumer may receive it.

You could get:

```
Consumer A → Order #100
Consumer B → Order #100
```

Therefore, the same message can be processed again.

### Solution

Use:

```
ChangeMessageVisibility
```

to extend the visibility timeout.

### Exam clue

> **Message is being processed twice → check Visibility Timeout.**
> 

---

# 14. Visibility Timeout — Too Low vs Too High

| Visibility Timeout | Problem |
| --- | --- |
| Too low | Message may become visible while still processing → duplicate processing |
| Too high | If consumer crashes, message waits longer before becoming available again |

So choose a timeout appropriate for the processing time.

AWS recommends making it longer than the SDK read timeout.

---

# 15. Short Polling vs Long Polling

When a consumer asks:

```
"Do you have any messages?"
```

SQS can respond immediately.

This is **short polling**.

Or it can wait for messages to arrive.

This is **long polling**.

---

# 16. Short Polling

```
Consumer → SQS
          ↓
       No message
          ↓
      Empty response
```

Consumer repeatedly asks:

```
Any message?
Any message?
Any message?
Any message?
```

This can create unnecessary API calls.

---

# 17. Long Polling ⭐⭐⭐⭐

With long polling:

```
Consumer
   ↓
SQS
   ↓
"Wait for a message"
```

SQS can wait up to:

> **20 seconds**
> 

If a message arrives during that period, SQS returns it.

### Benefits

- Fewer empty responses
- Fewer API calls
- Lower cost
- Better efficiency
- Lower unnecessary latency

### Exam clue

> **Reduce empty ReceiveMessage calls → Long Polling**
> 

> **20 seconds → maximum long-polling wait**
> 

---

# 18. Delay Queue

SQS can delay a newly sent message before it becomes visible.

Maximum:

> **15 minutes**
> 

Example:

```
Producer
   ↓
Send message
   ↓
SQS
   ↓
Wait 5 minutes
   ↓
Message becomes available
   ↓
Consumer
```

Useful when processing should intentionally be delayed.

---

# 19. SQS as a Buffer ⭐⭐⭐⭐⭐

This is one of the most common SAA architectures.

```
                SQS
                 │
                 ▼
Users → Web App → Queue → Workers → Database
```

Suppose:

```
Incoming requests = 10,000/sec
Database processing = 3,000/sec
```

Without SQS:

```
10,000/sec → Database
             ❌ overload
```

With SQS:

```
10,000/sec
    ↓
  SQS
    ↓
3,000/sec
    ↓
Database
```

The queue absorbs the temporary traffic spike.

---

# 20. SQS + Auto Scaling Group ⭐⭐⭐⭐⭐

SQS can be used with an ASG.

Example:

```
                 SQS
                  │
        Queue contains
         100,000 messages
                  │
                  ▼
            EC2 Instances
            EC2 EC2 EC2
                  │
                  ▼
             Processing
```

CloudWatch monitors queue metrics.

For example:

```
ApproximateNumberOfMessages
```

If queue length becomes very high:

```
CloudWatch Alarm
       ↓
ASG Scale Out
       ↓
More EC2 consumers
       ↓
Process messages faster
```

When the queue becomes smaller:

```
Scale In
```

---

# 21. SQS + Lambda

SQS can trigger Lambda through an **event source mapping**.

Architecture:

```
Producer
   ↓
SQS
   ↓
Lambda
   ↓
Process message
```

Lambda automatically polls the SQS queue and invokes your function with batches of messages.

### Scenario

An online store receives orders:

```
Web App
   ↓
SQS
   ↓
Lambda
   ↓
Update DynamoDB
```

This removes the need to maintain EC2 worker servers.

---

# 22. SQS + EC2

Another common architecture:

```
Application
    ↓
SQS
    ↓
EC2 workers
    ↓
Database
```

EC2 instances poll the queue.

If workload increases:

```
More messages
     ↓
ASG
     ↓
More EC2 workers
```

---

# 23. SQS Security

SQS supports encryption and access control.

### Encryption in transit

SQS API calls use:

```
HTTPS
```

### Encryption at rest

Messages can be encrypted using:

```
AWS KMS
```

### Access control

You can use:

```
IAM Policies
```

and

```
SQS Access Policies
```

SQS resource policies are particularly useful for allowing other AWS services or accounts to access a queue.

---

# 24. IAM Policy vs SQS Access Policy

Think:

### IAM Policy

Controls:

> **What can this IAM principal do?**
> 

Example:

```
EC2 role
   ↓
sqs:ReceiveMessage
sqs:DeleteMessage
```

### SQS Resource Policy

Controls:

> **Who can access this queue?**
> 

Example:

```
Account A
   ↓
SQS Queue in Account B
```

Queue policy can allow Account A to send messages.

---

# 25. Dead-Letter Queue — DLQ ⭐⭐⭐⭐⭐

A **Dead-Letter Queue** stores messages that repeatedly fail processing.

Example:

```
Main Queue
    ↓
Consumer
    ↓
Processing fails
    ↓
Retry
    ↓
Fails
    ↓
Retry
    ↓
Fails
    ↓
DLQ
```

Instead of endlessly retrying a bad message, move it to a DLQ for investigation.

---

# 26. Redrive Policy

You can configure:

> **Maximum number of receives**
> 

Example:

```
maxReceiveCount = 5
```

Meaning:

```
Receive
 ↓
Fail
 ↓
Receive
 ↓
Fail
 ↓
Receive
 ↓
Fail
 ↓
...
 ↓
5 attempts
 ↓
DLQ
```

This is extremely useful for troubleshooting poison messages.

---

# 27. SQS DLQ Scenario

Suppose your order-processing application receives:

```
Order #100
```

But the order contains corrupted data.

Every attempt fails.

Without DLQ:

```
Order #100
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
...
```

With DLQ:

```
Main SQS
   ↓
Consumer
   ↓
5 failures
   ↓
DLQ
   ↓
Developer investigates
```

### Exam clue

> **Messages repeatedly fail → Dead-Letter Queue**
> 

---

# 28. Standard SQS Delivery Model

Standard SQS uses:

> **At-least-once delivery**
> 

This means duplicates **can happen**.

Example:

```
Message A
   ↓
Consumer
   ↓
Processing
```

The message may occasionally be delivered again.

Therefore applications using Standard SQS should ideally be:

> **Idempotent**
> 

---

# 29. What Is Idempotency?

An operation is idempotent when performing it multiple times produces the same final result as performing it once.

Example:

Bad design:

```
Charge credit card $100
```

If the same message is processed twice:

```
$100
+
$100
=
$200 ❌
```

Better design:

```
Transaction ID = TX123
```

The application checks whether:

```
TX123 already processed?
```

If yes:

```
Ignore duplicate
```

This protects against duplicate SQS deliveries.

---

# 30. FIFO and Ordering

Suppose an application sends:

```
1 → 2 → 3 → 4
```

FIFO ensures the ordering is preserved within the relevant message group.

Example financial transaction:

```
Deposit $100
Deposit $50
Withdraw $30
```

Correct:

```
100 → +50 → -30
```

You don't want operations randomly reordered.

### Best choice:

**FIFO SQS**
