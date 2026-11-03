# AWS SQS & SNS — MASTER SAA-C03 TABLE

| Topic | What is it? | Key Concept | When / Why Use It? | Easy Scenario | ⭐ Exam Clue |
| --- | --- | --- | --- | --- | --- |
| **Amazon SQS** | Managed message queue | Producers send messages → queue stores them → consumers process them | Decouple application components and absorb traffic spikes | Website receives 10,000 orders, backend processes them gradually through SQS | **Decoupling + buffering = SQS** |
| **SQS Producer** | Application/service that sends messages | Uses `SendMessage` / SDK | Put work into a queue for later processing | Web application sends an order ID to SQS | **Producer → SendMessage** |
| **SQS Consumer** | Application/service that reads messages | Polls SQS → processes message → deletes it | Process queued work asynchronously | EC2/Lambda reads an order and stores it in RDS | **Receive → Process → Delete** |
| **SQS Standard Queue** | Default SQS queue type | Very high/unlimited throughput, best-effort ordering, at-least-once delivery | When maximum scalability matters and exact ordering is not required | Image processing jobs can be processed in any order | **High throughput + duplicates possible + ordering not guaranteed** |
| **SQS FIFO Queue** | Ordered SQS queue | FIFO = First In, First Out | When message order matters | Banking transactions for the same account must be processed in order | **Ordering required → FIFO** |
| **Standard vs FIFO** | Main queue choice | Standard = maximum throughput; FIFO = ordering/deduplication | Choose based on ordering requirements | Standard for image processing; FIFO for financial transactions | **Don't choose FIFO unless ordering/deduplication matters** |
| **MessageGroupId** | FIFO ordering mechanism | Messages with the **same MessageGroupId** are processed in order | Maintain ordering for a particular customer/account/order while allowing parallel processing across groups | `Customer-A` messages stay ordered; `Customer-B` can process independently | ⭐ **FIFO + ordering = MessageGroupId** |
| **FIFO Deduplication** | Prevents duplicate messages | Uses Deduplication ID/content-based deduplication | When duplicate messages must be prevented | Payment request accidentally submitted twice | **FIFO supports exactly-once processing semantics** |
| **Message Retention** | How long SQS keeps a message | Default **4 days**, configurable up to **14 days** | Gives consumers time to process messages | Consumer is temporarily unavailable; messages remain in queue | **SQS retention ≠ Visibility Timeout** |
| **Visibility Timeout** | Period after a consumer receives a message during which it is hidden from other consumers | Default **30 seconds** | Prevent another consumer from processing the same message simultaneously | Consumer receives order and gets 30 seconds to process it | ⭐ **Receive ≠ Delete; message becomes invisible temporarily** |
| **ChangeMessageVisibility** | Extends/reduces visibility timeout for a message | Consumer can request more processing time | Useful for long-running jobs | Processing normally takes 30 sec but one job needs 2 minutes | **Long processing → extend visibility timeout** |
| **Visibility Timeout Too Low** | Message becomes visible before processing finishes | Another consumer may receive it | Causes duplicate processing | Job takes 2 min but timeout is only 30 sec | **Too low → duplicates** |
| **Visibility Timeout Too High** | Failed consumer leaves message invisible for too long | Reprocessing is delayed | Avoid excessively long timeout | Consumer crashes and message stays hidden for hours | **Too high → slow recovery** |
| **Short Polling** | Consumer immediately asks SQS for messages | Returns immediately, even when no messages are available | Simple polling, but can cause more API calls | Application constantly asks "Any message? Any message?" | **More empty API calls + less efficient** |
| **Long Polling** | Consumer waits for messages to arrive | Waits up to **20 seconds** | Reduces empty receives and API calls | Consumer waits for an order instead of repeatedly checking | ⭐ **Prefer Long Polling** |
| **WaitTimeSeconds** | API parameter controlling long polling | Can be configured on ReceiveMessage | Set waiting period for long polling | `WaitTimeSeconds = 20` | **1–20 sec; 20 sec commonly preferred** |
| **Dead-Letter Queue (DLQ)** | Queue that stores messages that repeatedly fail processing | Prevents continuously retrying bad messages | Isolate failed/poison messages for investigation | Invalid payment message fails processing repeatedly | ⭐ **Repeated failures → DLQ** |
| **Redrive Policy** | Defines how messages move to DLQ | Uses `maxReceiveCount` | Send problematic messages to DLQ after a number of receives | After 5 failed receives → move to DLQ | **maxReceiveCount → DLQ** |
| **SQS Redrive / DLQ** | Failure-handling mechanism | Original queue → failed messages → DLQ | Debug failed messages without blocking normal processing | One malformed order should not repeatedly consume worker capacity | **Poison message → DLQ** |
| **SQS → Lambda** | Lambda can consume SQS messages | Lambda polls SQS automatically | Serverless asynchronous processing | SQS receives orders → Lambda processes them | **No need to manually poll SQS from Lambda** |
| **SQS + EC2** | EC2 instances consume queue messages | Multiple workers can process messages in parallel | Scale processing horizontally | ASG launches more EC2 workers when queue gets large | **Queue depth → CloudWatch → ASG scaling** |
| **SQS + Auto Scaling** | Dynamically scale consumers based on queue size | `ApproximateNumberOfMessages` is a useful metric | Handle traffic spikes | Queue suddenly grows from 1,000 → 100,000 messages | ⭐ **Large queue → scale out consumers** |
| **SQS as Buffer** | Queue temporarily stores work | Protects backend from sudden traffic spikes | Prevent database/application overload | 100,000 requests arrive but database can process only 10,000/min | ⭐ **SQS = shock absorber/buffer** |
| **SQS for Decoupling** | Separates application tiers | Producer does not directly depend on consumer being available | Improve resilience and scalability | Frontend → SQS → backend workers | ⭐ **Loose coupling = SQS** |
| **SQS Database Protection** | Queue protects database from traffic bursts | Workers consume at controlled rate | Avoid overwhelming RDS/DynamoDB/Aurora | Website receives huge traffic but DB processes writes gradually | **SQS in front of database writes** |
| **SQS Message Size** | Maximum message payload | **1 MB** per message | Send small messages or references to larger data | Instead of sending a 500-MB file, put the S3 object key in SQS | ⭐ **Large payload → store in S3, send reference through SQS** |
| **SQS Security — In Transit** | API communication is encrypted | HTTPS/TLS | Protect messages while travelling | Producer securely sends message to SQS | **HTTPS** |
| **SQS Security — At Rest** | Messages stored encrypted | SQS server-side encryption using KMS | Protect stored messages | Financial messages require encryption at rest | **KMS** |
| **SQS IAM Policy** | Controls who can call SQS APIs | Identity-based permissions | Control users/roles accessing SQS | EC2 role allowed to receive messages | **IAM controls API access** |
| **SQS Access Policy** | Resource-based policy for queue | Controls who/services can access queue | Cross-account access or allowing AWS services to send messages | SNS topic sends messages to SQS | ⭐ **SNS → SQS often requires SQS queue policy** |
| **SNS** | Managed publish/subscribe notification service | One publisher → many subscribers | Fan-out notifications/events | One order event must reach email, SQS, Lambda and other systems | ⭐ **Pub/Sub + fan-out = SNS** |
| **SNS Topic** | Logical communication channel | Publishers send messages to a topic | Distribute messages to subscribers | `OrderCreated` topic | **Publisher → Topic → Subscribers** |
| **SNS Publisher** | Sends message to SNS topic | Uses `Publish` API | Notify multiple subscribers | Order service publishes `OrderCreated` | **Publisher doesn't need to know individual subscribers** |
| **SNS Subscriber** | Receives SNS messages | Examples: SQS, Lambda, HTTP/S, email, SMS, etc. | React to notifications/events | Lambda receives an SNS notification | **Topic distributes to subscribers** |
| **SNS Fan-Out** | One message delivered to multiple destinations | SNS distributes one published event to many subscribers | Avoid creating separate producer-to-consumer connections | Order event → SQS queue 1 + SQS queue 2 + Lambda | ⭐ **One → many = SNS fan-out** |
| **SNS + SQS Fan-Out** | SNS sends the same event to multiple SQS queues | Each queue independently processes the message | Multiple applications need the same event | Order event → Billing Queue + Shipping Queue + Analytics Queue | ⭐ **SNS topic + multiple SQS queues** |
| **SNS vs SQS** | Different messaging models | SNS = push/pub-sub; SQS = queue/pull | SNS for distribution; SQS for buffering/work processing | SNS announces event; SQS stores work until worker processes it | ⭐ **SNS = distribute, SQS = store/process** |
| **SNS Message Filtering** | Sends only matching messages to a subscriber | Uses **Subscription Filter Policy** | Prevent every subscriber from receiving every message | Billing queue only receives `OrderType=Payment` events | ⭐ **Filter per subscription** |
| **SNS Subscription Filter Policy** | JSON rules attached to a subscription | Filters messages based on message attributes (and supported body filtering) | Route only relevant events to each subscriber | `department=finance` → Finance queue | **Filter happens before delivery to subscriber** |
| **SNS Filter Example** | Subscriber receives only matching events | Example: `{"eventType":["order"]}` | Reduce unnecessary processing | Analytics subscriber receives only order events | **Different subscribers can have different filters** |
| **SNS Message Attributes** | Key/value metadata attached to SNS message | Filters can use attributes | Useful for routing messages | `priority=high`, `type=payment` | **Filter policy can inspect message attributes** |
| **SNS → SQS** | SNS publishes to SQS | Queue must allow SNS to send messages | Durable asynchronous fan-out | SNS publishes → SQS stores → workers process | ⭐ **SNS handles distribution; SQS handles durability/buffering** |
| **SNS → Lambda** | SNS invokes Lambda | Event-driven processing | Automatically execute code when event arrives | New image event → Lambda processes it | **SNS → Lambda = event-driven** |
| **SNS → Email/SMS** | SNS can notify users directly | Push notifications | Alerts/notifications | System outage → SNS → SMS/email | **Human notification** |
| **SNS Security** | Controls access to topics | IAM + SNS topic policies | Secure publishing/subscription | Only application role can publish | **IAM + resource policy** |
| **SNS Encryption** | Encrypt SNS messages at rest | AWS KMS | Protect sensitive notifications | Financial notification data | **KMS encryption** |
| **SNS Standard Topic** | High-throughput pub/sub | At-least-once delivery; ordering not guaranteed | General notifications/events | Application events where ordering isn't critical | **Default SNS choice** |
| **SNS FIFO Topic** | Ordered SNS messaging | Preserves message order and supports deduplication | Ordering is important in pub/sub | Transaction events must arrive in order | ⭐ **Ordering + SNS → FIFO topic** |
| **SNS Standard vs FIFO** | Two SNS topic types | Standard = throughput/flexible; FIFO = ordering/deduplication | Choose FIFO only when ordering matters | Payment sequence must remain ordered | **FIFO = ordering** |
| **SNS Delivery Retry** | SNS retries delivery when supported endpoint temporarily fails | Helps reliable delivery | Subscriber temporarily unavailable | HTTP endpoint is down briefly | **SNS retries delivery** |
| **SNS + DLQ** | Failed SNS deliveries can be sent to DLQ for supported subscriptions | Captures undelivered messages | Troubleshoot failed deliveries | SNS → Lambda/SQS subscriber fails repeatedly | **DLQ helps preserve failed deliveries** |
| **SQS vs SNS + SQS** | One queue vs fan-out architecture | SQS alone = one queue/work stream; SNS + SQS = one event to multiple queues | Use SNS + SQS when several independent systems need the same message | Billing, shipping, analytics all need `OrderCreated` | ⭐ **Multiple independent consumers → SNS + SQS** |
| **SQS Ordering** | Standard has best-effort ordering | FIFO guarantees ordering within MessageGroupId | Financial/order workflows | Payment step 1 must happen before step 2 | **Strict ordering → FIFO** |
| **At-Least-Once Delivery** | Message may occasionally be delivered more than once | Consumer must handle duplicates safely | Standard SQS and many asynchronous systems | Same order message processed twice | ⭐ **Design consumers to be idempotent** |
| **Idempotent Consumer** | Processing the same message multiple times produces the same final result | Use unique transaction/order ID and conditional processing | Important with at-least-once delivery | If `Order123` already processed, don't charge customer again | ⭐ **Duplicate message protection** |
| **Exactly-Once Processing** | Application-level guarantee that work happens once | FIFO deduplication helps with duplicate sends, but business logic should still be designed carefully | Critical workflows | Payment processing | **Don't confuse FIFO deduplication with universal exactly-once business processing** |
| **SQS Queue URL** | Address of an SQS queue | Used by API/SDK operations | Identify target queue | Application uses queue URL to send/receive messages | **SQS uses queue, not topic** |
| **SNS Topic ARN** | Unique identifier of SNS topic | Used when publishing/subscribing | Identify SNS topic | Application publishes to Topic ARN | **SNS uses Topic ARN** |
| **SQS Polling Model** | Consumers retrieve messages | SQS is pull-based | Worker asks queue for work | EC2 calls ReceiveMessage | ⭐ **SQS = pull** |
| **SNS Delivery Model** | SNS pushes notifications to subscribers | Publish/subscribe | Immediate notification/event distribution | SNS sends event to Lambda/SQS endpoint | ⭐ **SNS = push** |
| **SQS Throughput** | Standard SQS supports very high/unlimited throughput | Designed for massive workloads | Large-scale asynchronous processing | Millions of jobs | **Standard SQS → massive throughput** |
| **FIFO Throughput** | FIFO has throughput limits based on configuration | Ordering comes with throughput constraints | When ordering/deduplication is more important than unlimited throughput | Sequential transaction processing | **FIFO trades throughput for ordering guarantees** |

---

# 🔥 THE MOST IMPORTANT SQS/SNS DIFFERENCES

| If the Question Says... | Think... | Choose |
| --- | --- | --- |
| **Decouple two applications** | Loose coupling | **SQS** |
| **Buffer requests / traffic spikes** | Shock absorber | **SQS** |
| **One application sends work to workers** | Queue | **SQS** |
| **Workers should process messages later** | Pull model | **SQS** |
| **Need extremely high throughput** | Standard queue | **SQS Standard** |
| **Need strict message ordering** | FIFO | **SQS FIFO** |
| **Need ordering per customer/account** | Message Group | **MessageGroupId** |
| **Duplicate messages are possible** | At-least-once | **Standard SQS** |
| **Prevent poison messages from retrying forever** | Failure isolation | **DLQ** |
| **Message takes longer than visibility timeout** | Extend timeout | **ChangeMessageVisibility** |
| **Reduce empty polling/API calls** | Wait for messages | **Long Polling** |
| **One event must go to many applications** | Fan-out | **SNS** |
| **One event → many SQS queues** | Pub/Sub + buffering | **SNS + SQS** |
| **Send notifications to Lambda** | Push event | **SNS** |
| **Send SMS/email notifications** | Notification service | **SNS** |
| **Only send certain events to a subscriber** | Filtering | **SNS Subscription Filter Policy** |
| **Need ordering in SNS** | FIFO topic | **SNS FIFO** |
| **Need multiple independent consumers with durable queues** | Fan-out + queue | **SNS → multiple SQS queues** |
| **Consumer should pull messages** | Queue | **SQS** |
| **Service should push notifications** | Pub/Sub | **SNS** |

---

# 🧠 ONE DIAGRAM TO REMEMBER EVERYTHING

```
                         ┌──────────────┐
                         │   Producer   │
                         └──────┬───────┘
                                │
                         SendMessage
                                │
                                ▼
                    ┌─────────────────────┐
                    │    SQS QUEUE        │
                    │                     │
                    │ Standard / FIFO     │
                    │ Visibility Timeout  │
                    │ Long Polling        │
                    │ DLQ                 │
                    └──────────┬──────────┘
                               │
                         ReceiveMessage
                               │
                               ▼
                         ┌───────────┐
                         │ Consumer  │
                         └───────────┘

              PUB/SUB / FAN-OUT ARCHITECTURE

                         ┌────────────┐
                         │ Publisher  │
                         └─────┬──────┘
                               │
                            Publish
                               │
                               ▼
                       ┌──────────────┐
                       │ SNS  TOPIC   │
                       └──────┬───────┘
                              │
             ┌────────────────┼────────────────┐
             │                │                │
             ▼                ▼                ▼
         SQS Queue 1      SQS Queue 2       Lambda
         Billing          Shipping          Analytics
             │                │
             ▼                ▼
          Worker           Worker

       SNS FILTERING

                    SNS Topic
                       │
             ┌─────────┼─────────┐
             │         │         │
          Filter A  Filter B  Filter C
             │         │         │
             ▼         ▼         ▼
          Finance    Shipping  Analytics
```

# ⭐ FINAL EXAM MEMORY

| Concept | Remember This |
| --- | --- |
| **SQS** | **Queue / buffer / decouple / pull** |
| **SNS** | **Topic / notification / fan-out / push** |
| **SQS Standard** | **Huge throughput, at-least-once, best-effort ordering** |
| **SQS FIFO** | **Ordering + deduplication** |
| **MessageGroupId** | **Ordering within a FIFO group** |
| **Visibility Timeout** | **Hide message temporarily after ReceiveMessage** |
| **ChangeMessageVisibility** | **Give consumer more processing time** |
| **Short Polling** | **Ask immediately → more API calls** |
| **Long Polling** | **Wait for message → fewer API calls** |
| **DLQ** | **Failed/poison messages go here** |
| **Redrive Policy** | **Controls when failed messages move to DLQ** |
| **SNS Fan-Out** | **One message → many subscribers** |
| **SNS Filter Policy** | **Subscriber receives only matching messages** |
| **SNS + SQS** | **Fan-out + durable independent processing** |
| **SQS + ASG** | **Queue grows → scale consumers** |
| **At-least-once** | **Duplicates can happen → make consumer idempotent** |
| **SQS Encryption** | **HTTPS in transit + KMS at rest** |
| **Large message** | **Store payload in S3, put reference in SQS** |

### 🎯 The 5-second SAA-C03 rule

**Need to BUFFER work? → SQS**

**Need to DECOUPLE applications? → SQS**

**Need to DISTRIBUTE one event to MANY consumers? → SNS**

**Need ORDERING? → FIFO**

**Need to send only certain events to a subscriber? → SNS Subscription Filter Policy**

**Need failed messages isolated? → DLQ**

**Need fewer polling requests? → Long Polling**

**Need more processing time? → Visibility Timeout / ChangeMessageVisibility**

**Need SNS fan-out + independent durable processing? → SNS → multiple SQS queues**
