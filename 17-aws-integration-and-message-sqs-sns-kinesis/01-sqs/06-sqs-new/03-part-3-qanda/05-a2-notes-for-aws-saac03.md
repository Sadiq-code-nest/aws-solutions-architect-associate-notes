# 📌 SQS Priority-Based Processing Architecture — SAA-C03 Notes

This question introduces an important **SQS design pattern**: when different types of customers/tasks have different priorities, use **multiple SQS queues** and let consumers process the high-priority queue first.

---

# 1. Core Problem: Different Processing Priorities

Imagine an application receives:

- 🟢 **Free users** → normal processing
- 🔴 **Premium users** → faster processing

If everything goes into one SQS queue:

```
Free ──────┐
Free ──────┤
Premium ───┤
Free ──────┤
Premium ───┘
      ↓
   One SQS Queue
      ↓
   EC2 Workers
```

There is no built-in **"premium messages always go first"** priority mechanism in a standard SQS queue.

So we redesign it.

---

# 2. Priority Queue Pattern ⭐⭐⭐

### Short definition

> **Priority Queue Pattern = Use separate queues for different priority levels and have consumers process the higher-priority queue first.**
> 

For this question:

```
                ┌──→ Premium Queue ──→ EC2 Workers
                │
Incoming → Dispatcher
                │
                └──→ Free Queue ─────→ EC2 Workers
```

The EC2 workers:

1. Check **Premium Queue**
2. If messages exist → process them
3. If Premium Queue is empty → process Free Queue

### 🧠 Exam shortcut

> **Different priority customers → Multiple SQS queues**
> 

---

# 3. Order Dispatcher

### Short definition

> **Order Dispatcher = A component that receives incoming requests and routes them to the appropriate queue/processing path.**
> 

In the diagram:

```
             Amazon EC2
           Web Application
                  │
                  ↓
        Order Dispatcher Queue
                  │
                  ↓
          Order Dispatcher
             /         \
            /           \
           ↓             ↓
     Premium Queue     Free Queue
```

The **Order Dispatcher** decides where an order should go.

For example:

```
Customer Request
       │
       ↓
Is customer Premium?
       │
   ┌───┴────┐
  YES       NO
   ↓         ↓
Premium     Free
 Queue      Queue
```

### Example

A premium customer uploads a photo:

```
Upload
  ↓
Order Dispatcher
  ↓
Premium Queue
```

A free customer uploads a photo:

```
Upload
  ↓
Order Dispatcher
  ↓
Free Queue
```

### Where to use?

Use a dispatcher when you need to **route requests based on characteristics**, such as:

- Customer type
- Priority
- Order type
- Region
- Workload type
- Processing requirement

---

# 4. Order Dispatcher Queue

### Short definition

> **Order Dispatcher Queue = An initial SQS queue that temporarily holds incoming orders before a dispatcher routes them to the appropriate processing queue.**
> 

In the provided architecture:

```
Website
   ↓
Order Dispatcher Queue
   ↓
Order Dispatcher
   ↓
 ┌──────────────┐
 ↓              ↓
Premium       Free
Queue         Queue
```

### Why have this extra queue?

It provides **decoupling** between:

```
Web application
      ↕
Order Dispatcher
```

The website doesn't need the dispatcher to be immediately available.

The request can safely wait in SQS.

### Example

Suppose 10,000 users upload photos at once.

```
Website
   ↓
SQS
   ↓
Dispatcher
```

The website can continue accepting requests even if the dispatcher is temporarily busy.

---

# 5. Priority Order Queue

### Short definition

A **priority order queue** is simply a queue designated for **higher-priority work**.

⚠️ Important:

> **"Priority Order Queue" is not a special AWS SQS queue type.**
> 

It is just the architectural name given to the queue in the diagram.

For example:

```
Premium Customers
       ↓
Premium / Priority SQS Queue
       ↓
EC2 Workers
```

And:

```
Free Customers
       ↓
Free SQS Queue
       ↓
EC2 Workers
```

The architecture creates priority by **separating the queues**.

---

# 6. How Does the Priority Actually Work?

This is the key part.

Suppose:

```
Premium Queue:
[ P1 ][ P2 ][ P3 ]

Free Queue:
[ F1 ][ F2 ][ F3 ][ F4 ][ F5 ]
```

EC2 workers first poll:

```
Premium Queue
```

If premium messages exist:

```
P1 → Process
P2 → Process
P3 → Process
```

Only when Premium is empty do they process:

```
F1 → Process
F2 → Process
...
```

Conceptually:

```
          EC2 Worker
              │
              ↓
       Check Premium Queue
              │
        ┌─────┴─────┐
      Has messages? │
        │            │
       YES           NO
        ↓            ↓
    Process       Check Free
     Premium         Queue
```

### ⭐ This is the actual priority mechanism.

---

# 7. Why Not Put Everything in One SQS Queue?

Suppose:

```
Single Queue

[P1][F1][F2][F3][P2][F4][P3]
```

The system cannot simply say:

> "Take P1 and P2 first because they're premium."
> 

SQS isn't a general-purpose priority queue where you assign an arbitrary priority value to each message and expect SQS to reorder delivery accordingly.

Therefore:

❌ One queue + "premium priority"

Instead:

✅ Separate queues.

```
Premium → Premium Queue
Free    → Free Queue
```

---

# 8. EC2 Worker

### Short definition

> **EC2 Worker = An EC2 instance that consumes messages from SQS and performs the actual processing task.**
> 

In this question, the EC2 workers:

> Generate the video montage.
> 

```
SQS
 ↓
EC2 Worker
 ↓
Download Photo
 ↓
Generate Video
 ↓
Complete
```

The worker doesn't need to know where the request originally came from.

It simply receives work from its queue.

---

# 9. Auto Scaling

The diagram shows **Auto Scaling** around the EC2 workers.

### Short definition

> **EC2 Auto Scaling automatically increases or decreases the number of EC2 instances according to workload.**
> 

Example:

```
Normal workload

SQS
 ↓
EC2
EC2
```

High workload:

```
Huge SQS backlog
       ↓
   Auto Scaling
       ↓
EC2 EC2 EC2 EC2 EC2 EC2
```

When the workload decreases:

```
Low SQS backlog
      ↓
Auto Scaling
      ↓
EC2
EC2
```

### Common SAA-C03 clue

> **SQS queue depth/backlog increases → scale EC2 workers**
> 

A common metric is:

**ApproximateNumberOfMessagesVisible**

---

# 10. Why SQS + EC2 Is Decoupled

Without SQS:

```
Web Server
    ↓
Video Processing Server
```

If video processing becomes overloaded, the web application can also suffer.

With SQS:

```
Web Server
    ↓
   SQS
    ↓
EC2 Workers
```

The web application and video processing system are now **decoupled**.

The queue acts as a buffer.

```
              SQS
        ┌─────────────┐
        │  BUFFER     │
        │ P1 P2 F1 F2 │
        └─────────────┘
             ↓
         EC2 Workers
```

---

# 11. Complete Architecture from Your Diagram

The whole architecture can be understood like this:

```
                         WEB APPLICATION
                              │
                              ↓
                  ┌────────────────────┐
                  │ Order Dispatcher    │
                  │      Queue          │
                  └─────────┬──────────┘
                            ↓
                    Order Dispatcher
                       /          \
                      /            \
                     ↓              ↓
             Premium Queue       Free Queue
            "High Priority"     "Normal Priority"
                  │                  │
                  ↓                  ↓
            EC2 Auto Scaling    EC2 Auto Scaling
             Worker Fleet        Worker Fleet
                  │                  │
                  ↓                  ↓
             Video Processing   Video Processing
```

The important flow is:

```
Request
   ↓
Dispatcher
   ↓
Classify customer
   ↓
 ┌─────────────┬─────────────┐
 ↓                           ↓
Premium                    Free
 ↓                           ↓
Priority Queue             Normal Queue
 ↓                           ↓
EC2 Workers                EC2 Workers
```

---

# 12. Example: Food Delivery

This pattern isn't limited to photo/video processing.

Imagine a food delivery application.

### Regular orders

```
Regular Customer
      ↓
Regular Queue
      ↓
Restaurant Workers
```

### VIP customer

```
VIP Customer
      ↓
Priority Queue
      ↓
Restaurant Workers
```

Workers always check:

```
Priority Queue
      ↓
If empty
      ↓
Regular Queue
```

This ensures VIP orders get processed first.

---

# 13. Example: Customer Support

Suppose:

- Free customers → normal support
- Enterprise customers → priority support

```
                   Support Request
                         ↓
                    Dispatcher
                    /         \
                   ↓           ↓
             Enterprise      Free
                Queue         Queue
                   ↓           ↓
              Workers       Workers
```

Workers prioritize Enterprise queue.

---

# 14. Example: Image/Video Processing

Your question's exact use case:

```
Premium User
     ↓
Premium SQS
     ↓
EC2 Video Worker
     ↓
Fast processing
```

```
Free User
     ↓
Free SQS
     ↓
EC2 Video Worker
     ↓
Normal processing
```

This is the best example to remember for the exam.

---

# 15. Alternative: Kinesis ❌

Kinesis is primarily for:

> **Real-time streaming data**
> 

Examples:

- Clickstream
- IoT data
- Logs
- Telemetry
- Real-time analytics

```
Producers
    ↓
Kinesis
    ↓
Stream Consumers
```

It isn't the natural choice for:

> **Premium vs Free task priority**
> 

So:

**Priority-based task processing → SQS**

not Kinesis.

---

# 16. Alternative: S3 ❌

S3 is primarily:

> **Object storage**
> 

You can absolutely store uploaded photos in S3:

```
User
 ↓
S3
 ↓
Photo
```

But S3 doesn't provide the required **priority task processing mechanism**.

You would still need something like:

```
S3
 ↓
SQS
 ↓
Workers
```

So S3 alone doesn't solve the priority requirement.

---

# 17. Important SAA-C03 Pattern: Multiple Queues

Whenever you see something like:

> "Different customers require different processing priority."
> 

Think:

```
                 Dispatcher
                 /        \
                ↓          ↓
          High Priority   Low Priority
             Queue          Queue
                \            /
                 \          /
                  ↓        ↓
                  Workers
```

### Examples

| Requirement | Pattern |
| --- | --- |
| Premium vs Free | Multiple SQS queues |
| VIP vs Regular | Multiple SQS queues |
| Critical vs Normal jobs | Multiple SQS queues |
| High vs Low priority tasks | Multiple SQS queues |
| Different processing classes | Multiple SQS queues |

---

# 🧠 SAA-C03 Quick Notes

### **Order Dispatcher**

> Component that **routes incoming requests** to the appropriate queue.
> 

### **Order Dispatcher Queue**

> Initial SQS queue that **buffers incoming requests** before dispatching.
> 

### **Priority Queue**

> A **dedicated queue for high-priority work**. Not a special SQS feature.
> 

### **Multiple SQS Queues**

> Used to create **priority-based processing**.
> 

### **EC2 Worker**

> Consumes SQS messages and performs the actual processing.
> 

### **Auto Scaling**

> Adds/removes EC2 workers based on workload, often using **SQS queue depth** as a scaling signal.
> 

### **SQS**

> Provides **asynchronous communication, buffering, and decoupling**.
> 

---

## ⭐ The Exam Mental Model

When you see:

**"Premium users must be processed before free users."**

Immediately think:

```
             Dispatcher
             /        \
            ↓          ↓
       PREMIUM        FREE
       SQS Queue      SQS Queue
           ↓             ↓
       EC2 Workers    EC2 Workers
```

**Do NOT think:**

> "I'll put a priority number on each SQS message."
> 

Instead:

> **Separate the workloads into separate SQS queues, and have consumers prioritize the high-priority queue.**
>
