# Edge Computing (Simple Explanation)

Think of **Cloud Computing** and **Edge Computing** like this:

- **Cloud Computing** = Process data in AWS data centers (far away)
- **Edge Computing** = Process data near where the data is created

Example:

Imagine you have **10,000 CCTV cameras** in a factory.

Without Edge Computing:

```
Camera
   │
   ▼
Internet
   │
   ▼
AWS Cloud
   │
Analyze Video
   │
   ▼
Result
```

Every video goes to AWS.

Problems:

- Huge bandwidth usage
- Slow response
- Expensive
- Internet dependency

---

With Edge Computing:

```
Camera
   │
   ▼
Local Edge Device
(AWS Snowball Edge / AWS Outposts / IoT Greengrass)
   │
Analyze locally
   │
Only send important results
   ▼
AWS Cloud
```

Instead of sending **100 GB of video**, it may send:

```
Motion detected
Person entered
Fire detected
```

Only a few KB.

---

# Why Edge Computing?

Suppose:

A self-driving car generates

```
4 TB/day
```

Can it upload everything to AWS first?

No.

If internet takes even

```
200 ms
```

the car may already crash.

Instead

```
Camera
↓
Computer inside car
↓
Brake immediately
↓
Later upload logs to AWS
```

This is Edge Computing.

---

# Definition

Edge Computing means:

> Processing, storing, and analyzing data **close to where it is generated**, instead of sending everything to a cloud data center.
> 

---

# Why AWS Created Edge Services?

Many places have

- Slow Internet
- No Internet
- Ships
- Military bases
- Oil rigs
- Remote villages
- Factories
- Hospitals

They still need computing.

AWS created Edge services.

---

# AWS Edge Services

AWS provides:

```
AWS Snowball Edge
AWS Outposts
AWS Local Zones
AWS Wavelength
AWS IoT Greengrass
```

Today we'll focus on **Snowball Edge**.

---

# What is Snowball?

AWS Snowball Edge is a **physical appliance** shipped by AWS.

It looks like a rugged computer.

It has:

- CPU
- RAM
- Storage
- Network ports
- Security chip
- Runs AWS software

Think of it as

> "A small AWS server in a portable box."
> 

---

# Why AWS Built Snowball?

Imagine your company has

```
2 Petabytes
```

of data.

Internet speed:

```
100 Mbps
```

How long?

Around **3 years**.

Need it tomorrow?

Impossible.

AWS says:

```
Don't upload.

We'll send you a device.
```

---

# Snowball Data Migration

Step 1

AWS ships Snowball.

```
AWS
 │
 ▼
Your Office
```

---

Step 2

Connect it.

```
Laptop
Server
NAS
Database

      │
      ▼

 Snowball Edge
```

Copy data at very high speed.

---

Step 3

When finished

```
Lock Device
```

AWS encrypts everything.

---

Step 4

Ship it back.

```
You
 │
 ▼
AWS
```

---

Step 5

AWS copies data into:

- Amazon Simple Storage Service (S3)
- Amazon Elastic Block Store (EBS)
- Glacier
- etc.

---

# This is called

Offline Data Migration

Instead of

```
Internet
```

you use

```
Truck
```

This is jokingly called

> **Never underestimate the bandwidth of a truck full of hard drives.**
> 

---

# Snowball Edge = More Than Storage

Old Snowball

```
Storage only
```

Snowball Edge

```
Storage
+
Compute
```

It contains CPUs and can run applications.

---

# Why Compute?

Suppose

A mining company has

```
1000 cameras
```

No Internet.

Without compute

```
Camera
↓
Store
↓
Wait
↓
Upload later
```

With Snowball Edge

```
Camera
↓
Snowball Edge

AI Detection
Face Detection
Object Detection
Quality Check

↓

Store Results

↓

Later Sync AWS
```

Everything happens locally.

---

# Example

Airport Security

```
Camera
      │
      ▼

Snowball Edge

Person Detection
Weapon Detection
Face Matching

↓

Alert Security

↓

Upload logs later
```

No Internet required.

---

# Snowball Edge Architecture

```
          Cameras
             │
             ▼

      Snowball Edge

 ┌─────────────────────┐
 │ CPU                 │
 │ Memory              │
 │ Storage             │
 │ EC2 Compute         │
 │ Lambda              │
 │ S3 Compatible Store │
 └─────────────────────┘

             │

Internet Available?

      YES             NO
       │               │
       ▼               ▼

Sync AWS        Continue Working
```

---

# Compute Optimized Snowball

More CPU

Best for

- Machine Learning
- AI
- Video Processing
- Analytics

---

# Storage Optimized

More Disk

Best for

- Backup
- Data Migration
- Log Storage
- File Storage

---

# What Can Run Inside Snowball?

AWS lets you run:

- EC2 instances
- Lambda functions
- Containers
- Local S3-compatible storage

Example:

```
Sensor

↓

Lambda

↓

Analyze

↓

Store

↓

Upload Later
```

---

# Security

Snowball is very secure.

- 256-bit encryption
- Tamper-resistant hardware
- Trusted Platform Module (TPM)
- Data erased after import

If someone steals the device,

they cannot read the data without the encryption keys managed in AWS.

---

# Real Example

Oil Rig

```
Middle of Ocean

Internet?
Sometimes No
```

Sensors generate

```
Temperature
Pressure
Video
```

Without Edge

Impossible to upload continuously.

Instead

```
Sensors

↓

Snowball Edge

↓

Analyze

↓

Alert Workers

↓

Upload to AWS later
```

---

# Another Example

Hospital

MRI Scan

```
10 GB each
```

Instead of uploading immediately

```
MRI

↓

Snowball Edge

↓

AI Analysis

↓

Doctor gets result

↓

Upload at night
```

---

# Relationship Between Edge Computing and Snowball

```
Edge Computing

      │

      ▼

Need Local Computing

      │

      ▼

AWS Provides

Snowball Edge
```

So,

**Edge Computing** is the concept, and **Snowball Edge** is one AWS product that implements it.

---

# Cloud vs Edge

| Cloud Computing | Edge Computing |
| --- | --- |
| Processing in AWS Region | Processing near the data source |
| Requires network connectivity | Can work with limited or no connectivity |
| Higher latency | Low latency |
| Centralized resources | Distributed resources |
| Ideal for large-scale analytics and long-term storage | Ideal for real-time decisions |

---

# Edge Computing + Snowball Flow

```
Factory

Sensors
Cameras
Machines

      │

      ▼

Snowball Edge

Local Storage
Local Compute
Lambda
EC2
AI

      │

Internet Available?

      │

      ▼

AWS Cloud

S3
Analytics
Backup
Machine Learning
```

---

# When Should You Use Snowball Edge?

Use it when you have:

- Petabytes of data to migrate to AWS
- Limited or unreliable internet connectivity
- A need for local data processing
- Remote locations (ships, mines, military bases, oil rigs)
- Real-time AI or analytics at the edge
- Backup and disaster recovery in disconnected environments

---

# Easy Way to Remember

- **Edge Computing** = *Process data close to where it's created.*
- **Cloud Computing** = *Process data in AWS data centers.*
- **AWS Snowball Edge** = *A portable AWS server with storage and compute that lets you process data locally and later synchronize with AWS.*
