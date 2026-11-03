# Amazon S3 Event Notifications — SAA-C03 Notes

## 1. What is S3 Event Notification?

**S3 Event Notifications** allow Amazon S3 to automatically notify another AWS service when something happens to an object in a bucket.

Instead of continuously checking the bucket:

```
❌ Application repeatedly asks:
"Did a new file arrive?"
"Did someone delete a file?"
```

S3 can automatically send an event:

```
S3 Bucket
    │
    │ Object event
    ▼
Notification
    │
    ├── Lambda
    ├── SQS
    └── SNS
```

### Simple idea

> **Something happens in S3 → S3 sends an event → another service reacts.**
> 

---

# 2. Common S3 Events

S3 can generate events for different object operations.

| Event | Meaning |
| --- | --- |
| `s3:ObjectCreated:*` | Object created |
| `s3:ObjectCreated:Put` | Object uploaded using PUT |
| `s3:ObjectCreated:Post` | Object uploaded using POST |
| `s3:ObjectCreated:Copy` | Object copied |
| `s3:ObjectCreated:CompleteMultipartUpload` | Multipart upload completed |
| `s3:ObjectRemoved:*` | Object deleted |
| `s3:ObjectRestore:*` | Archived object restored |
| `s3:Replication:*` | Replication-related event |

For SAA-C03, the big ones to remember are:

**Created → Removed → Restored → Replicated**

---

# 3. Basic Architecture

```
                    Amazon S3
                  ┌─────────────┐
                  │   Bucket    │
                  │             │
                  │ image.jpg   │
                  └──────┬──────┘
                         │
                   ObjectCreated
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
           Lambda       SQS        SNS
              │          │          │
              ▼          ▼          ▼
         Process image  Queue     Notify
```

---

# 4. Most Important Example — Image Thumbnail

Suppose users upload images to:

```
s3://my-photo-bucket/uploads/
```

You want to automatically generate thumbnails.

### Architecture

```
User
 │
 │ Upload image.jpg
 ▼
┌──────────────┐
│  S3 Bucket   │
│ /uploads/    │
└──────┬───────┘
       │
       │ ObjectCreated
       ▼
┌──────────────┐
│ AWS Lambda   │
└──────┬───────┘
       │
       │ Resize image
       ▼
┌──────────────┐
│ S3 /thumbnails│
└──────────────┘
```

### Flow

1. User uploads `image.jpg`
2. S3 detects `ObjectCreated`
3. S3 sends event to Lambda
4. Lambda reads the image
5. Lambda generates thumbnail
6. Thumbnail is stored in S3

### Exam keyword

> **"When an object is uploaded to S3, automatically process it."**
> 

Think:

**S3 Event Notification → Lambda**

---

# 5. Object Name Filtering ⭐

You don't necessarily need to trigger your application for every object.

You can filter based on:

- Prefix
- Suffix

Example:

```
uploads/
├── photo.jpg
├── photo.png
├── video.mp4
└── document.pdf
```

You only want Lambda to process images.

Configure:

```
Prefix: uploads/
Suffix: .jpg
```

Then:

```
photo.jpg
   ↓
Lambda triggered ✅

video.mp4
   ↓
No notification ❌

document.pdf
   ↓
No notification ❌
```

### SAA-C03 clue

> "Trigger Lambda only when `.jpg` files are uploaded."
> 

→ **S3 Event Notification with suffix filtering**

---

# 6. Destinations for S3 Event Notifications

Native S3 Event Notifications can send events to:

| Destination | Typical use |
| --- | --- |
| **Lambda** | Process the object immediately |
| **SQS** | Queue work reliably |
| **SNS** | Fan out notifications |
| **EventBridge** | Advanced event routing/filtering |

For the classic S3 Event Notification mechanism, remember:

```
S3
├── Lambda
├── SQS
└── SNS
```

**EventBridge is the more advanced event-routing option.**

---

# 7. S3 → SQS

Use **SQS** when you want asynchronous processing and buffering.

```
S3
 │
 │ ObjectCreated
 ▼
SQS Queue
 │
 │ Poll
 ▼
Worker / Lambda
```

### Example

A company receives thousands of files per minute.

Instead of processing everything immediately:

```
S3
 │
 ▼
SQS
 │
 ├── File 1
 ├── File 2
 ├── File 3
 ├── File 4
 └── ...
 │
 ▼
Consumers
```

SQS provides a buffer between S3 and the processing application.

### Think:

> **S3 → SQS = "Put the work in a queue."**
> 

---

# 8. S3 → SNS

SNS is useful when **multiple systems need to know about the event**.

```
                 S3
                  │
             ObjectCreated
                  │
                  ▼
                 SNS
              /    |    \
             /     |     \
            ▼      ▼      ▼
          SQS   Lambda   Email
```

Example:

When a new document is uploaded:

- Processing system receives it
- Audit system receives it
- Notification system sends an alert

### Think:

> **SNS = Fan-out notification**
> 

---

# 9. S3 Event Notification vs EventBridge ⭐⭐⭐

This is important for SAA-C03.

## Basic S3 Event Notifications

Good for straightforward S3 → destination integration.

```
S3
 │
 ├── Lambda
 ├── SQS
 └── SNS
```

You can filter using object name prefixes/suffixes.

---

## EventBridge

For more advanced event-driven architectures:

```
S3
 │
 │ Events
 ▼
EventBridge
 │
 ├── Lambda
 ├── SQS
 ├── SNS
 ├── Step Functions
 ├── Kinesis
 └── Many other targets
```

EventBridge provides richer event filtering and routing.

---

# 10. EventBridge Advanced Filtering ⭐

With EventBridge, you can filter based on event details such as:

- Object name
- Object size
- Metadata/event fields
- Event type
- Other event attributes

Conceptually:

```
S3
 │
 │ ObjectCreated
 ▼
EventBridge
 │
 ├── Rule 1 → .jpg → Lambda
 │
 ├── Rule 2 → large files → Step Functions
 │
 └── Rule 3 → specific event → SQS
```

This is much more flexible than simple prefix/suffix filtering.

---

# 11. EventBridge — Multiple Destinations

Suppose your requirement is:

> When an S3 object is created, start a workflow and also send the event to analytics.
> 

Architecture:

```
                       S3
                        │
                   ObjectCreated
                        │
                        ▼
                  EventBridge
                    /       \
                   /         \
                  ▼           ▼
          Step Functions   Kinesis
```

You can route events to many AWS services.

---

# 12. EventBridge — Archive & Replay ⭐

EventBridge provides capabilities beyond simple notification.

### Archive

You can archive events.

```
S3
 │
 ▼
EventBridge
 │
 ├── Target
 │
 └── Archive
```

### Replay

Later, you can replay archived events.

Useful if:

- A consumer had a problem
- You need to reprocess historical events
- You want to test a new event consumer

### Exam clue

> "Need to archive and replay events."
> 

Think:

**Amazon EventBridge**

---

# 13. EventBridge — Reliable Event Routing

EventBridge is useful when you need a centralized event bus.

Example:

```
S3
 │
 ▼
EventBridge
 │
 ├── Rule: image uploaded
 │      └── Lambda
 │
 ├── Rule: large object
 │      └── Step Functions
 │
 └── Rule: archive event
        └── Archive
```

---

# 14. IAM Permissions ⭐⭐⭐

This is a common troubleshooting/exam area.

S3 needs permission to invoke or publish to the destination.

There are different resource policies depending on the destination.

### S3 → Lambda

Lambda needs a **resource-based policy** allowing S3 to invoke it.

```
S3
 │
 │ Invoke
 ▼
Lambda
 │
 └── Lambda Resource Policy
      allows S3
```

---

### S3 → SQS

The SQS queue needs an appropriate **resource policy** allowing S3 to send messages.

```
S3
 │
 │ SendMessage
 ▼
SQS
 │
 └── SQS Resource Policy
      allows S3
```

---

### Key exam idea

If an S3 notification isn't working, don't only inspect the event configuration.

Check:

```
S3 Event Configuration
        +
Destination permissions
```

---

# 15. Event Notification Delivery Timing

S3 Event Notifications are designed for near-real-time notification.

Typically:

> **Events are delivered within seconds, but can sometimes take a minute or longer.**
> 

Therefore, don't treat S3 notifications as a strict real-time transaction mechanism.

---

# 16. Multiple Event Notifications

You can configure multiple notifications for the same bucket.

Example:

```
                     S3 Bucket
                         │
              ┌──────────┼──────────┐
              │          │          │
              ▼          ▼          ▼
           .jpg       .pdf       ObjectRemoved
              │          │          │
              ▼          ▼          ▼
           Lambda      SQS         SNS
```

So one bucket can have multiple event notification configurations.

---

# 17. S3 Event Notifications vs EventBridge

| Feature | S3 Event Notifications | EventBridge |
| --- | --- | --- |
| S3 events | ✅ | ✅ |
| Lambda | ✅ | ✅ |
| SQS | ✅ | ✅ |
| SNS | ✅ | ✅ |
| Advanced event filtering | Limited | ✅ |
| Prefix/suffix filtering | ✅ | Can filter event fields |
| Many AWS service targets | Limited | ✅ |
| Event archive | ❌ | ✅ |
| Event replay | ❌ | ✅ |
| Complex event routing | Limited | ✅ |
| Best for simple S3 triggers | ⭐⭐⭐ | ⭐⭐ |
| Best for complex event-driven architecture | ⭐ | ⭐⭐⭐ |

---

# 18. S3 Event Notifications vs SNS vs SQS

Don't confuse these.

| Service | Main purpose |
| --- | --- |
| **S3 Event Notification** | Detect an event in S3 |
| **SNS** | Push/fan-out notification |
| **SQS** | Queue/buffer messages |
| **Lambda** | Execute code in response |
| **EventBridge** | Route/filter events intelligently |

A common architecture can combine them:

```
                  S3
                   │
             ObjectCreated
                   │
                   ▼
              EventBridge
               /       \
              ▼         ▼
          Lambda       SQS
```

---

# 19. Scenario-Based SAA-C03 Examples

### Scenario 1

> Whenever a customer uploads a `.jpg` image to S3, automatically generate a thumbnail.
> 

**Answer:**

```
S3 Event Notification
        ↓
Lambda
```

Use a `.jpg` suffix filter.

---

### Scenario 2

> Every time a file is uploaded to S3, multiple applications need to receive a notification.
> 

**Think:**

```
S3 → SNS → Multiple subscribers
```

**SNS = fan-out**

---

### Scenario 3

> S3 receives thousands of objects and processing should happen asynchronously without losing work during temporary processing spikes.
> 

**Think:**

```
S3 → SQS → Consumer
```

**SQS = buffering + asynchronous processing**

---

### Scenario 4

> An organization needs advanced filtering based on object metadata and size, and wants to route events to multiple AWS services.
> 

**Answer:**

```
S3 → EventBridge → Rules → Targets
```

---

### Scenario 5

> The company needs to archive events and replay them later.
> 

**Answer:**

**Amazon EventBridge**

---

### Scenario 6

> S3 successfully detects an object creation event, but Lambda isn't being invoked.
> 

Check:

```
S3 Event Notification
       +
Lambda resource-based policy
```

---

# 20. SAA-C03 Decision Tree 🧠

```
S3 object event occurs
        │
        ▼
What do you need?
        │
        ├── Run code?
        │      ↓
        │    Lambda
        │
        ├── Queue/buffer work?
        │      ↓
        │     SQS
        │
        ├── Notify/fan-out?
        │      ↓
        │     SNS
        │
        └── Complex filtering/routing?
               ↓
           EventBridge
```

### One-line memory

> **S3 Event Notification = "Something happened in my bucket; react to it."**
> 

And:

> **Lambda = process**
> 
> 
> **SQS = queue**
> 
> **SNS = fan-out**
> 
> **EventBridge = advanced routing/filtering + archive/replay**
>
