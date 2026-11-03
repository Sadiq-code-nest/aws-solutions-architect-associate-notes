# Amazon Data Firehose

Firehose is much simpler.

Think of it as a **delivery truck**.

```
Incoming Data

↓

Firehose

↓

Destination
```

You don't process the data yourself.

Firehose automatically delivers it.

---

## Example

Website logs

```
Millions of logs
```

Need to store them in S3.

```
Logs

↓

Firehose

↓

S3 Bucket
```

Done.

No servers.

No consumers.

No shard management.

---

# Firehose Supports

Destinations like:

- Amazon S3
- Amazon Redshift
- Amazon OpenSearch Service
- Splunk
- Datadog
- MongoDB
- HTTP endpoints

---

# Buffering

Suppose

One log arrives.

Firehose doesn't send immediately.

Instead it waits.

```
Log

Log

Log

Log

Log
```

Then sends together.

This is called **Buffering**.

Buffering can be based on:

- Time
- Size

That's why Firehose is **Near Real-Time**, not instant.

---

# Data Transformation

Suppose data arrives as CSV.

```
ID,Name

1,John
```

You want JSON.

Lambda converts it.

```
{
 "ID":1,
 "Name":"John"
}
```

Firehose supports this.

---

# Compression

Without compression

```
100 GB
```

With gzip

```
20 GB
```

Lower storage cost.

---

# Format Conversion

Firehose can convert

```
JSON

↓

Parquet
```

Parquet is optimized for analytics (such as querying with Athena).

---

# Real Architecture

```
Application

      │

      ▼

Kinesis Data Streams

      │

      ▼

Amazon Data Firehose

      │

      ▼

Amazon S3
```

Streams handles ingestion and replay.

Firehose handles delivery.
