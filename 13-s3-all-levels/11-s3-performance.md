# Amazon S3 Performance (SAA-C03) — Complete Crystal Clear Notes

This topic is about **how Amazon S3 handles very high traffic and how you can upload/download files faster.**

There are **4 major concepts**:

1. Baseline Performance (Prefixes)
2. Multipart Upload
3. S3 Transfer Acceleration
4. Byte-Range Fetches

---

# 1. S3 Baseline Performance

## First Question:

Suppose you have an S3 bucket.

```
my-company-data
```

Millions of users start downloading files simultaneously.

Will S3 crash?

❌ No.

S3 is designed to automatically scale.

AWS automatically increases capacity behind the scenes.

---

## AWS says:

For **each prefix**, S3 supports at least:

- **3,500** PUT/COPY/POST/DELETE requests per second
- **5,500** GET/HEAD requests per second

These are **minimum guaranteed rates**, not maximum limits.

---

# What is a Prefix?

This is where many students get confused.

Think of a prefix as:

> **Everything before the object's filename.**
> 

Example:

```
s3://mybucket/images/profile/user1.jpg
```

Object name:

```
user1.jpg
```

Prefix:

```
images/profile/
```

---

More examples:

| Object Key | Prefix |
| --- | --- |
| `photos/2025/img1.jpg` | `photos/2025/` |
| `photos/2026/img2.jpg` | `photos/2026/` |
| `logs/app/log.txt` | `logs/app/` |
| `backup.zip` | `/` (root of bucket) |

---

# Why Prefix Matters

Imagine all files are stored in one prefix.

```
Bucket

photos/

    img1
    img2
    img3
    img4
    ...
```

All users access:

```
photos/
```

That single prefix guarantees:

```
5500 GET/sec
```

---

Now divide files.

```
photos1/
photos2/
photos3/
photos4/
```

Each prefix gets:

```
5500 GET/sec
```

Total:

```
4 × 5500

=

22000 GET/sec
```

Exactly why AWS documentation says:

> Spread traffic across prefixes for higher throughput.
> 

---

# Diagram

One Prefix

```
Users
 │
 ▼

photos/

5500 GET/sec
```

---

Four Prefixes

```
Users

↓

photos1/

5500

↓

photos2/

5500

↓

photos3/

5500

↓

photos4/

5500

Total

22000 GET/sec
```

---

# Important Exam Point

AWS says:

There is **NO LIMIT** on the number of prefixes.

Meaning:

```
photos1/

photos2/

photos3/

...

photos1000/
```

All are allowed.

---

# 2. Multipart Upload

Now suppose you upload a huge file.

Example:

```
20 GB movie
```

Uploading as one file:

```
Computer

↓

20 GB

↓

S3
```

Problems:

- Slow
- If upload fails at 99%, restart from beginning

---

AWS Solution:

Multipart Upload.

---

# How Multipart Upload Works

Instead of:

```
20 GB
```

AWS divides it.

Example:

```
20 GB

↓

Part1

Part2

Part3

Part4
```

Each part uploads simultaneously.

---

Diagram

```
20 GB File

        │

────────┼────────

│   │   │   │

P1  P2  P3  P4

│   │   │   │

────────▼────────

Amazon S3
```

---

# Benefits

## Faster

Parallel uploads.

Instead of:

```
One Road
```

You use:

```
Four Roads
```

Traffic moves faster.

---

## More Reliable

Suppose:

```
Part 3 fails.
```

Only upload:

```
Part 3
```

No need to upload the entire 20 GB again.

---

# AWS Exam Rule

Recommended:

```
Files >100 MB
```

Mandatory:

```
Files >5 GB
```

The SAA exam loves this fact.

---

# Real Example

Upload:

```
50 GB backup
```

AWS splits:

```
10 Parts

↓

Uploads all together

↓

Combines into one object
```

---

# 3. S3 Transfer Acceleration

Imagine your bucket is in Australia.

You live in the USA.

Without Transfer Acceleration:

```
USA

────────────Internet────────────

Australia

S3 Bucket
```

Very long network path.

Slow.

---

AWS Solution:

Transfer Acceleration.

AWS uses its global edge network (same network used by Amazon CloudFront).

---

Flow

```
USA User

↓

Nearest AWS Edge Location

↓

Private AWS Network

↓

Australia S3 Bucket
```

Notice:

Instead of traveling across the public internet all the way to Australia, the data reaches the nearest AWS Edge Location first. From there, AWS carries it over its fast private backbone to the bucket.

---

# Diagram

Without Transfer Acceleration

```
USA

========================

Public Internet

========================

Australia

S3
```

---

With Transfer Acceleration

```
USA

↓

Nearest Edge Location

↓

AWS Private Network

↓

Australia S3
```

Much faster.

---

# Real-Life Analogy

Without acceleration:

You drive from one country to another using public roads.

With acceleration:

You reach the nearest airport, then fly using a dedicated airline network.

The dedicated network is much faster.

---

# Can it work with Multipart Upload?

YES.

You can combine:

```
Multipart Upload

+

Transfer Acceleration
```

Huge files upload very quickly.

---

# 4. Byte-Range Fetches

Normally:

Downloading a file:

```
10 GB File

↓

Download Entire File
```

---

Sometimes you only need a small portion.

Example:

```
Movie

Need first 5 minutes only.
```

No reason to download the whole movie.

---

AWS lets you request:

```
Bytes

0-5000
```

instead of:

```
Entire Object
```

---

# Example

Suppose:

```
100 MB File
```

Need only:

```
First 1 MB
```

Request:

```
Bytes

0 - 999999
```

AWS returns only that portion.

---

# Parallel Downloads

Suppose:

```
8 GB File
```

Instead of:

```
One download
```

Split:

```
Bytes

0-2GB

2-4GB

4-6GB

6-8GB
```

Download all four simultaneously.

---

Diagram

```
8 GB File

↓

Chunk 1

↓

Chunk 2

↓

Chunk 3

↓

Chunk 4

↓

Merged
```

Much faster.

---

# Why Useful?

## Speed

Parallel downloads.

---

## Resilience

Suppose:

```
Chunk 3 fails.
```

Download only:

```
Chunk 3
```

instead of restarting the entire file.

---

## Partial Retrieval

Need:

```
First 100 KB
```

Download:

```
Only first 100 KB
```

Useful for:

- Reading file headers
- Previewing videos
- Inspecting metadata
- Parsing large datasets

---

# Complete Comparison

| Feature | Purpose | Example |
| --- | --- | --- |
| Prefix Performance | Increase request throughput | Spread objects across prefixes |
| Multipart Upload | Faster uploads | Split a 20 GB file into parts |
| Transfer Acceleration | Faster long-distance uploads | USA → Australia bucket |
| Byte-Range Fetch | Faster or partial downloads | Download only part of a 10 GB file |

---

# Real Company Example

A video streaming company stores movies in S3.

```
Customer uploads

↓

Multipart Upload

↓

Transfer Acceleration

↓

S3 Bucket
```

Viewer watches movie:

```
Player

↓

Byte-Range Fetch

↓

Only requested video segments
```

Millions of viewers:

```
Objects spread across prefixes

↓

High request throughput
```

All four performance features work together.

---

# Ultimate Memory Trick

Imagine moving a large package overseas.

- **Prefixes** = Open more checkout counters so more customers can be served at once.
- **Multipart Upload** = Break one huge box into smaller boxes and ship them in parallel.
- **Transfer Acceleration** = Drop the boxes at the nearest airport, then let AWS's private airline deliver them.
- **Byte-Range Fetch** = Ask for only the pages you need from a large book instead of borrowing the whole book.

---

# 🎯 30-Second SAA-C03 Summary

- **Baseline Performance**: Each S3 prefix supports at least **3,500 write** and **5,500 read** requests per second. More prefixes = more throughput.
- **Multipart Upload**: Recommended for files over **100 MB**, required for files over **5 GB**. Upload parts in parallel and retry only failed parts.
- **S3 Transfer Acceleration**: Speeds up uploads from distant locations by routing data through the nearest AWS Edge Location and then across AWS's private network.
- **Byte-Range Fetches**: Request only specific byte ranges of an object to enable parallel downloads, improve resilience, or retrieve just part of a file.
