# AWS Snow Family (Snowball, Snowball Edge) — Complete SAA-C03 Notes

This is a very common **AWS SAA-C03 exam topic**.

The biggest mistake students make is trying to memorize Snowball features without understanding **why AWS created it**.

Let's learn it from the beginning.

---

# Part 1 — The Problem AWS is Solving

Suppose your company has

```
1 Petabyte (PB) of data
```

stored in your data center.

Now you want to move everything into AWS S3.

---

## Option 1 — Upload over Internet

Suppose your Internet speed is

```
100 Mbps
```

Time required

```
1 PB

↓

About 3 Years
```

Even with

```
1 Gbps
```

it still takes

```
124 Days
```

Still too slow.

---

Even

```
10 Gbps
```

takes

```
12 Days
```

Many companies cannot even get a dedicated 10 Gbps connection.

---

### Problems with Internet Transfer

❌ Slow

❌ Expensive

❌ Internet interruptions

❌ Shared bandwidth

❌ Business users become slow

❌ Transfer may fail

---

# AWS Solution

Instead of

> **Sending data over the network...**
> 

AWS says

> **Ship the storage device itself!**
> 

This is AWS Snowball.

---

# Real-Life Analogy

Imagine moving **500 TB of movies** to another city.

Option 1

Upload over Internet.

May take months.

---

Option 2

Copy everything onto a hard drive.

Ship it by courier.

Done.

AWS Snowball works exactly like this.

---

# What is AWS Snowball?

> **Snowball is a secure physical device that AWS ships to you so you can transfer massive amounts of data offline.**
> 

Instead of

```
Data

↓

Internet

↓

AWS
```

we do

```
Data

↓

Snowball Device

↓

Courier

↓

AWS

↓

S3
```

---

# Complete Workflow

### Step 1

Request Snowball from AWS.

```
AWS

↓

Ships Snowball

↓

Your Office
```

---

### Step 2

Connect Snowball to your network.

```
Server

↓

LAN

↓

Snowball
```

Copy data.

---

### Step 3

When finished

Return the device.

```
Your Office

↓

Courier

↓

AWS
```

---

### Step 4

AWS copies the data

into

- Amazon S3
- EBS
- Glacier (depending on the job)

---

# Diagram

```
          Company Data Center

          NAS / Servers

               │

         Copy Data

               │

        AWS Snowball

               │

      Ship Device Back

               │

               ▼

             AWS

               │

          Amazon S3
```

---

# Why Snowball?

Instead of

```
Internet Transfer
```

We use

```
Physical Shipping
```

Sometimes shipping is much faster than networking.

AWS even jokes about this:

> **Never underestimate the bandwidth of a truck full of hard drives.**
> 

---

# When Should You Use Snowball?

AWS recommendation:

> **If transferring data over the network would take more than about one week, use Snowball.**
> 

---

# Example

Suppose

Company owns

```
400 TB
```

Internet

```
100 Mbps
```

Time

Months.

Answer

✅ Snowball.

---

# Snowball Security

Many people worry

"What if someone steals the device?"

AWS designed Snowball to be highly secure.

Features include:

- Tamper-resistant hardware
- Data encrypted (256-bit encryption)
- Secure shipping
- Device cannot be accessed without authorization
- Trusted Platform Module (TPM) protection

So even if someone steals it,

they cannot read your data.

---

# Snowball vs Internet

| Internet | Snowball |
| --- | --- |
| Online transfer | Offline transfer |
| Slow for PB-scale data | Very fast for PB-scale migration |
| Can fail if Internet drops | Independent of Internet speed |
| Uses bandwidth | No WAN bandwidth required |
| Good for small data | Best for huge datasets |

---

# Part 2 — What is Edge Computing?

Now comes another confusing topic.

Students think

Snowball

=

Data Migration only.

Actually

Snowball Edge

can also **run applications**.

---

First understand

## What is the Edge?

Normally

Applications run

inside AWS.

Example

```
Truck

↓

Internet

↓

AWS

↓

Process Data
```

Works fine

only if Internet exists.

---

Now imagine

A truck

driving in a desert.

No Internet.

---

Or

```
Oil Rig

Middle of Ocean
```

No Internet.

---

Or

```
Mining Station

Underground
```

No Internet.

---

How will data be processed?

Cannot send it to AWS.

---

AWS Solution

Bring AWS

to the device.

This is

## Edge Computing

---

# Definition

> **Edge Computing means processing data where it is created instead of sending it to the cloud first.**
> 

---

# Architecture

Without Edge Computing

```
Camera

↓

Internet

↓

AWS

↓

Processing
```

---

With Edge Computing

```
Camera

↓

Snowball Edge

↓

Processing

↓

(Optional)

Later upload to AWS
```

---

# Real-Life Example

Imagine a delivery truck.

Truck has

- Cameras
- GPS
- Temperature sensors

Every second

they generate

```
500 GB
```

of data.

Internet

No signal.

---

Snowball Edge

inside truck

processes data immediately.

No Internet required.

---

Later

when truck returns

upload results

to AWS.

---

# Snowball Edge

Snowball Edge is

**Snowball + Computing Power**

It can

- Store data
- Run EC2
- Run Lambda
- Process data locally

---

# Diagram

```
Truck

Camera

Sensor

GPS

      │

      ▼

Snowball Edge

EC2

Lambda

AI

      │

(Optional)

Upload later

↓

AWS
```

---

# Types of Snowball Edge

There are two major types for the exam.

---

## 1. Compute Optimized

Focus

More CPU & RAM.

Less storage.

Best when computation is the priority.

Example

- AI inference
- Image processing
- Video transcoding
- Running applications

---

## 2. Storage Optimized

Focus

More storage.

Enough compute.

Best when storing huge amounts of data.

Example

- Video backup
- Scientific datasets
- Media archives

---

# Comparison

| Compute Optimized | Storage Optimized |
| --- | --- |
| More CPU | More Disk |
| Less Storage | Huge Storage |
| Run applications | Store data |
| Machine Learning | Data Migration |

---

# What Can Snowball Edge Run?

### EC2 Instances

Example

```
Factory

↓

Snowball Edge

↓

EC2

↓

Analyze sensor data
```

---

### Lambda Functions

Example

Every image

↓

Lambda

↓

Detect defects

↓

Save result

---

# Common Use Cases

## 1. Preprocessing Data

Instead of uploading

```
10 TB
```

upload only

```
500 GB
```

after cleaning.

Example

```
Camera

↓

Raw Video

↓

Snowball

↓

Compress

↓

Upload
```

---

## 2. Machine Learning

Suppose

Factory camera

captures products.

Snowball Edge

runs AI model.

Immediately

```
Good

Bad

Broken
```

No Internet required.

---

## 3. Media Transcoding

Original

```
4K Video
```

Convert

↓

1080p

↓

Upload later.

---

## 4. IoT

Thousands of sensors.

Snowball Edge

collects

processes

stores

uploads later.

---

# Snowball vs Snowball Edge

| Snowball | Snowball Edge |
| --- | --- |
| Data Migration | Data Migration + Compute |
| Storage Device | Storage + EC2 + Lambda |
| Offline Transfer | Offline Transfer + Edge Computing |
| No compute | Compute available |

---

# Complete Architecture

```
          Factory

      Cameras

      Sensors

      Machines

           │

           ▼

     Snowball Edge

  ┌──────────────────┐

  │ Storage          │

  │ EC2              │

  │ Lambda           │

  │ Machine Learning │

  └──────────────────┘

           │

     Later upload

           ▼

         Amazon S3
```

---

# SAA-C03 Exam Scenarios

### Scenario 1

**Need to migrate 700 TB of data from an on-premises data center to AWS. The Internet connection is only 100 Mbps.**

✅ **Answer:** AWS Snowball

---

### Scenario 2

**An oil platform in the ocean has no reliable Internet but must process sensor data locally.**

✅ **Answer:** Snowball Edge (Compute Optimized)

---

### Scenario 3

**A mining station needs to run ML inference where there is no network connectivity.**

✅ **Answer:** Snowball Edge + EC2/Lambda

---

### Scenario 4

**A media company needs to transfer petabytes of archived videos into Amazon S3.**

✅ **Answer:** Snowball

---

### Scenario 5

**A truck collects camera footage all day and uploads data only when it returns to headquarters.**

✅ **Answer:** Snowball Edge

---

# Snowball vs Snowball Edge vs Direct Internet

| Feature | Internet | Snowball | Snowball Edge |
| --- | --- | --- | --- |
| Transfer Method | Network | Physical device | Physical device |
| Best for | Small data | Large migration | Large migration + local processing |
| Internet Required | Yes | No | No |
| Runs EC2 | ❌ | ❌ | ✅ |
| Runs Lambda | ❌ | ❌ | ✅ |
| Edge Computing | ❌ | ❌ | ✅ |
| Petabyte Scale | Poor | Excellent | Excellent |

---

# 🎯 Ultimate Memory Trick

Imagine you're filming a wildlife documentary in a remote jungle:

- **Internet Upload** = Sending every video clip over a slow satellite connection—painfully slow.
- **Snowball** = Putting all the footage onto a rugged encrypted storage box and shipping it back to headquarters.
- **Snowball Edge** = The same rugged box **plus a small computer**. It can analyze footage, run AI to detect animals, compress videos, or filter useless recordings **before** they are shipped.

**Remember for the exam:**

- 📦 **Snowball = Move Large Data**
- 🖥️ **Snowball Edge = Move Large Data + Process Data at the Edge**
- 🌐 **Edge Computing = Process data where it's created, not in the cloud**
