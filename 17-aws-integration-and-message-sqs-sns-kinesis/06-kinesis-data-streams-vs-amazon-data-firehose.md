# Kinesis Data Streams vs Amazon Data Firehose

| Feature | Kinesis Data Streams | Amazon Data Firehose |
| --- | --- | --- |
| Purpose | Capture and process real-time streaming data | Deliver streaming data to destinations |
| Managed? | Partially (you manage shards unless using On-Demand) | Fully managed, serverless |
| Replay data? | ✅ Yes (retention up to 365 days) | ❌ No |
| Retention | Up to 365 days | No long-term retention |
| Consumer required? | ✅ Yes, applications read the stream | ❌ No consumer needed for delivery |
| Scaling | Provisioned or On-Demand | Automatic |
| Data processing | Your applications process records | Optional Lambda transformation before delivery |
| Delivery | Consumers decide what to do | Automatically delivers to configured destinations |
| Latency | Real-time (low latency) | Near real-time (buffers before delivery) |
| Best for | Real-time analytics, fraud detection, dashboards, replay | Log delivery, backups, loading data into S3, Redshift, OpenSearch |

---

# Easy Exam Trick

Imagine a **river**.

## Kinesis Data Streams = The River 🌊

The water (events) keeps flowing.

You can stand by the river and collect water whenever you want.

If you miss some water, you can often go back (within the retention period) and replay the events.

```
Producer
    │
    ▼
~~~~~~~~~~~~~~
 Kinesis Stream
~~~~~~~~~~~~~~
     ▲
     │
 Consumers
```

---

## Amazon Data Firehose = The Delivery Truck 🚚

The truck picks up packages and delivers them automatically.

You don't decide how it drives.

You simply tell it the destination.

```
Logs

↓

Firehose 🚚

↓

S3
```

---

# AWS SAA-C03 Memory Tips

When you see these phrases in an exam question, think of the matching service:

| Exam clue | Choose |
| --- | --- |
| "Replay data later" | **Kinesis Data Streams** |
| "Multiple applications process the same live stream" | **Kinesis Data Streams** |
| "Need data retention for days or months" | **Kinesis Data Streams** |
| "Automatically deliver logs to S3 or Redshift" | **Amazon Data Firehose** |
| "Fully managed, serverless streaming delivery" | **Amazon Data Firehose** |
| "Convert JSON to Parquet before storing" | **Amazon Data Firehose** |
| "Compress streaming data before storage" | **Amazon Data Firehose** |

### One-line memory trick

- **Kinesis Data Streams = Capture, keep, replay, and process streaming data.**
- **Amazon Data Firehose = Automatically transform (optionally) and deliver streaming data to storage or analytics destinations.**
