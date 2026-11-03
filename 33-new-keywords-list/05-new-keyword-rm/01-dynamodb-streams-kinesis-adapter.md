# DynamoDB Streams Kinesis Adapter

### Simple definition

> **DynamoDB Streams Kinesis Adapter is a library that lets your application read records from DynamoDB Streams using the Amazon Kinesis Client Library (KCL).**
> 

Think:

```
DynamoDB
   │
   │ changes
   ▼
DynamoDB Streams
   │
   │ Kinesis Adapter
   ▼
KCL-based application
   │
   ▼
Process changes
```

It is important to understand that **DynamoDB Streams is not the same thing as Kinesis Data Streams**.

The **Kinesis Adapter acts as a bridge** so applications designed to use KCL can consume DynamoDB Stream records.

---

# 1. What does DynamoDB Streams do?

DynamoDB Streams records changes made to items in a DynamoDB table.

For example:

```
DynamoDB Table

UserID    Balance
101       $500
```

Someone changes the balance:

```
$500 → $400
```

DynamoDB Streams records that change.

Conceptually:

```
DynamoDB Table
     │
     │ INSERT / MODIFY / DELETE
     ▼
DynamoDB Streams
```

The stream can contain information about the changed item, depending on the configured stream view type.

---

# 2. Where does the Kinesis Adapter come in?

Suppose you already have a Java application built using **Kinesis Client Library (KCL)**.

Normally:

```
Kinesis Data Streams
        ↓
       KCL
        ↓
   Your application
```

But you want that application to process **DynamoDB Stream** records.

The DynamoDB Streams Kinesis Adapter provides the compatibility layer:

```
DynamoDB Streams
        ↓
Kinesis Adapter
        ↓
       KCL
        ↓
Your application
```

So the adapter essentially says:

> "Your application knows how to consume streams using KCL; I'll make DynamoDB Streams usable through that model."
> 

---

# 3. Real-world example

Imagine an e-commerce application.

```
DynamoDB
Orders Table
   │
   │ Order updated
   ▼
DynamoDB Streams
   │
   ▼
Kinesis Adapter
   │
   ▼
KCL Application
   │
   ├── Update analytics
   ├── Update search index
   ├── Send notification
   └── Update another system
```

### Example

Customer places an order:

```
Order #123
Status = PENDING
```

Then the application changes it:

```
PENDING → PAID
```

DynamoDB Streams captures:

```
MODIFY
Order #123
Status: PENDING → PAID
```

The Kinesis Adapter allows your KCL-based application to consume this stream record and perform additional processing.

---

# 4. When would you use it?

Use the **DynamoDB Streams Kinesis Adapter** when:

> You need to consume **DynamoDB Streams** using an application/framework based on **Kinesis Client Library (KCL)**.
> 

Typical scenarios:

- Existing KCL-based applications
- Custom stream-processing applications
- Distributed processing of DynamoDB changes
- Applications that need KCL's processing model for DynamoDB Streams

---

# 5. Don't confuse these three

This is the important SAA-C03 part.

| Service | Purpose |
| --- | --- |
| **DynamoDB Streams** | Captures changes to DynamoDB items |
| **Kinesis Data Streams** | General-purpose real-time streaming service |
| **DynamoDB Streams Kinesis Adapter** | Lets KCL-based applications consume DynamoDB Streams |

### Memory trick

> **DynamoDB Streams = What changed?**
> 

> **Kinesis = Stream data**
> 

> **Kinesis Adapter = Bridge DynamoDB Streams to KCL**
> 

---

# 6. DynamoDB Streams vs Kinesis Data Streams

They may look similar because both involve streaming, but they solve different problems.

### DynamoDB Streams

```
DynamoDB
   ↓
Changes
   ↓
DynamoDB Streams
```

It's specifically about **DynamoDB item changes**.

### Kinesis Data Streams

```
Applications
     ↓
Kinesis Data Streams
     ↓
Multiple consumers
```

It's a general-purpose streaming platform.

---

# 🎯 SAA-C03 Exam Shortcut

If the question says:

> **"Capture changes made to DynamoDB items"**
> 

👉 **DynamoDB Streams**

If it says:

> **"Use Kinesis Client Library to process DynamoDB Stream records"**
> 

👉 **DynamoDB Streams Kinesis Adapter**

If it says:

> **"Real-time streaming of application/log/event data"**
> 

👉 **Kinesis Data Streams**

### One-line takeaway

> **DynamoDB Streams Kinesis Adapter = a bridge that allows KCL-based applications to consume and process DynamoDB Streams records.**
>
