# 4.8 Amazon CloudFront

### Definition

CloudFront is AWS's CDN. It caches and delivers content from edge locations closer to users, reducing latency for content such as static files and suitable HTTP responses.

```
User
 │
 ▼
CloudFront Edge
 │
 ├── Cache HIT → User
 │
 └── Cache MISS
         │
         ▼
       Origin
```

### Worked example

For game assets:

```
Mobile User
     │
     ▼
CloudFront
     │
     ▼
S3
     │
     ▼
game-image.png
```

CloudFront is excellent for:

- images
- JavaScript
- CSS
- videos
- downloadable game assets
- suitable API responses

### CloudFront vs API Gateway caching

| CloudFront | API Gateway cache |
| --- | --- |
| CDN / edge caching | API-layer caching |
| Global edge locations | Regional API service |
| Excellent for static content | API responses |
| Reduces geographic latency | Reduces backend API calls |
| Works with many origin types | API Gateway integration |

### Exam trigger

> **"Global content delivery / edge locations / static content" → CloudFront**
> 

---

# 4.9 Amazon ElastiCache

### Definition

ElastiCache is a managed in-memory caching service supporting engines such as Redis/Valkey and Memcached. It provides a general-purpose caching layer for applications.

```
Application
    │
    ▼
ElastiCache
    │
    ▼
Database
```

### Worked example

An application frequently needs:

```
session information
leaderboard data
frequently accessed objects
computed results
```

Instead of repeatedly querying the database:

```
Application
     │
     ▼
ElastiCache
     │
     ├── Hit → return
     │
     └── Miss → Database
```

### ElastiCache vs DAX

The key exam distinction:

```
DynamoDB-specific caching
       ↓
      DAX
```

versus:

```
General application caching
       ↓
   ElastiCache
```

### Exam trigger

> **"Redis/Memcached / general-purpose application cache" → ElastiCache**
> 

---

# 4.10 AWS CLI

### Definition

AWS CLI is a command-line interface that lets you create and manage AWS resources using commands.

```
Developer
   │
   ▼
AWS CLI
   │
   ▼
AWS APIs
   │
   ▼
DynamoDB
```

### Worked example

A developer can create a DynamoDB table through CLI commands rather than the console.

### Important exam point

The fact that the DynamoDB table was created using CLI **doesn't affect the architecture decision**.

You can still configure:

- Auto Scaling
- DAX
- Global Tables
- backups
- other DynamoDB features

afterward.

### Exam trigger

> **"Command-line AWS resource management" → AWS CLI**
> 

---

# 5. Master Comparison Table

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **DynamoDB read acceleration** | **DAX** |
| **DynamoDB cache** | **DAX** |
| **Microsecond DynamoDB cached reads** | **DAX** |
| **Provisioned RCU/WCU automatically adjusts** | **DynamoDB Auto Scaling** |
| **Variable traffic + provisioned capacity** | **DynamoDB Auto Scaling** |
| **No capacity planning / unpredictable DynamoDB traffic** | **DynamoDB On-Demand** |
| **API response caching** | **API Gateway Cache** |
| **API Gateway + Lambda** | **API Gateway + Lambda** |
| **Global edge caching** | **CloudFront** |
| **Static images/files globally** | **CloudFront** |
| **General-purpose Redis/Memcached cache** | **ElastiCache** |
| **Multi-Region DynamoDB** | **Global Tables** |
| **Global users needing regional database access** | **DynamoDB Global Tables** |
| **Serverless function** | **Lambda** |
| **Massive scalable NoSQL database** | **DynamoDB** |
| **AWS resources created from terminal** | **AWS CLI** |
| **DynamoDB-specific cache** | **DAX, not ElastiCache** |
| **API-level cache** | **API Gateway Cache** |
| **Database-level cache for DynamoDB** | **DAX** |
| **Multi-Region replication** | **Global Tables** |
| **Millions of DynamoDB reads + low latency** | **DAX + appropriate DynamoDB capacity strategy** |

---

# 6. Side-by-Side Option Analysis Table

| Option | Underlying Idea | Good? | One-line reason |
| --- | --- | --- | --- |
| **A** | DAX + DynamoDB Auto Scaling | ✅ | Directly attacks huge read volume and variable capacity demand |
| **B** | CloudFront → DynamoDB + ElastiCache on client | ❌ | DynamoDB isn't used as a normal CloudFront origin, and ElastiCache isn't a client-device cache |
| **C** | DAX + assume Auto Scaling is automatic | ❌ | DAX is correct, but Auto Scaling is not simply enabled by default |
| **D** | API Gateway cache + Global Tables | ⚠️ | Both can be useful, but Global Tables add multi-Region complexity without a stated requirement, and DAX is the more direct DynamoDB read-acceleration solution |

---

# 7. Clue-by-Clue Post-Mortem

Let's dissect the question.

---

### "A company currently has an Augment Reality (AR) mobile game"

This establishes the application type.

Think:

```
Mobile application
      ↓
Potentially huge concurrent traffic
```

It doesn't itself determine the answer.

---

### "serverless backend"

🔥 Important architectural clue.

Think:

```
API Gateway
     ↓
Lambda
     ↓
DynamoDB
```

The question expects managed/serverless services rather than manually managed servers.

---

### "DynamoDB table"

🔥 Important.

The database is already DynamoDB.

Therefore the likely optimization services include:

```
DynamoDB
 ├── DAX
 ├── Auto Scaling
 ├── On-Demand
 └── Global Tables
```

---

### "launched using the AWS CLI"

Mostly irrelevant.

Don't get distracted by it.

```
CLI-created DynamoDB
       =
Still just DynamoDB
```

---

### "store all the user data and information gathered from the players"

This indicates the database is central to application operation.

```
Player
  ↓
Application
  ↓
DynamoDB
```

---

### "Lambda function to pull the data from DynamoDB"

🔥 Important.

Current flow:

```
Mobile
  ↓
Lambda
  ↓
DynamoDB
```

This gives us an opportunity for a caching layer:

```
Mobile
  ↓
Lambda
  ↓
DAX
  ↓
DynamoDB
```

---

### "millions of users each day"

🔥🔥 Very important.

This screams:

> **Massive scale + potentially massive read volume.**
> 

Think:

```
Millions of requests
       ↓
Caching
       ↓
DAX
```

---

### "to read and store data"

Notice that both **reads and writes** matter.

DAX primarily helps with the **read side**.

DynamoDB capacity/scaling handles the broader database workload, including writes.

```
READ
 ↓
DAX
 ↓
DynamoDB

WRITE
 ↓
DynamoDB
```

---

### "improve its overall performance"

Think:

```
Performance
   ↓
Caching
   ↓
DAX
```

---

### "make it more scalable"

Think:

```
Scalability
   ↓
DynamoDB capacity scaling
```

---

### "while keeping the costs low"

🔥🔥 This is the deciding phrase.

Don't simply say:

> "Provision huge DynamoDB capacity."
> 

That can become expensive.

Instead:

```
Variable traffic
     ↓
Auto Scaling
     ↓
Don't permanently over-provision
```

And:

```
Repeated reads
     ↓
DAX
     ↓
Fewer DynamoDB reads
```

Therefore:

```
Performance → DAX
Scalability → Auto Scaling
Cost → avoid unnecessary over-provisioning
```

That combination strongly points to **A**.

---

# 8. Why the Tempting Wrong Answers Are Tempting

## Trap #1 — Option C

This is probably the most dangerous distractor.

It says:

> "Since Auto Scaling is enabled by default..."
> 

Everything after that sounds excellent:

```
DAX
+
Auto Scaling
=
Exactly what we want!
```

A rushed test-taker may select it immediately.

But AWS exam questions often hide the decisive mistake inside an otherwise good architecture.

The false statement is:

> **DynamoDB Auto Scaling is enabled by default.**
> 

That's not correct for provisioned capacity.

### The distinction

```
DynamoDB
    │
    ├── On-Demand
    │      ↓
    │   automatically handles capacity
    │
    └── Provisioned
           ↓
       Auto Scaling
           ↓
       must be configured
```

So:

**A:** explicitly enables Auto Scaling → ✅

**C:** assumes it's enabled automatically → ❌

---

## Trap #2 — Option D

This one sounds sophisticated:

```
API Gateway
+
Lambda
+
Caching
+
Global Tables
```

That's a lot of AWS services, so it can feel like a more "enterprise" architecture.

But SAA questions are not:

> "Which option contains the most AWS services?"
> 

They're asking:

> **"Which architecture directly solves the stated problem with the least unnecessary complexity/cost?"**
> 

Global Tables are excellent when you need:

```
Multi-Region
+
Global users
+
Regional resilience/latency
```

But none of those requirements are explicitly stated.

For this question:

```
Huge DynamoDB read volume
        ↓
       DAX
```

is much more directly relevant.

---

# 9. Final Answer Recap + One-Line Memory Trick

## ✅ Correct Answer: Option A

> **Enable DynamoDB Accelerator (DAX) and ensure that Auto Scaling is enabled and increase the maximum provisioned read and write capacity.**
> 

### Why?

```
Millions of users
       │
       ▼
Huge read volume
       │
       ▼
      DAX
       │
       ▼
Reduce DynamoDB read pressure
```

And:

```
Variable traffic
       │
       ▼
DynamoDB Auto Scaling
       │
       ├── Scale up
       └── Scale down
```

This improves:

- ⚡ Read performance
- 📈 Scalability
- 💰 Cost efficiency

### 🧠 Memory Trick #1

> **"DynamoDB + massive reads + microseconds → DAX."**
> 

### 🧠 Memory Trick #2

> **"Provisioned DynamoDB + variable traffic → Auto Scaling."**
> 

And an important trap:

> **"DAX is not Auto Scaling. Auto Scaling must be configured."**
> 

---

# 10. Bonus: Related Exam Traps to Watch For

### Scenario 1 — Unpredictable traffic with no capacity planning

If the question says:

> "Traffic is highly unpredictable and the company wants to avoid capacity planning."
> 

Think:

```
DynamoDB
   ↓
On-Demand Capacity
```

Not necessarily Auto Scaling.

---

### Scenario 2 — Users are spread around the world and need low-latency writes

If the question says:

> "Users are distributed across multiple continents and need local database access."
> 

Think:

```
DynamoDB Global Tables
```

because the requirement is **multi-Region**.

---

### Scenario 3 — Need to cache API responses

If the question says:

> "Repeated API requests return the same response and the company wants to reduce backend invocations."
> 

Think:

```
API Gateway
     ↓
Caching
```

rather than DAX.

---

### Scenario 4 — General Redis/Memcached application caching

If the question says:

> "Cache sessions, leaderboards, computed results, and arbitrary application objects."
> 

Think:

```
ElastiCache
```

rather than DAX.

---

### Scenario 5 — Static game assets need global delivery

If the question says:

> "Millions of users globally download game images, JavaScript, videos, or other static assets."
> 

Think:

```
User
 ↓
CloudFront
 ↓
S3
```

Not DAX.

---

# 🔥 FINAL FLASHCARD — MASTER COMPARISON

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **DynamoDB + massive repeated reads** | **DAX** |
| **DynamoDB + microsecond cached reads** | **DAX** |
| **DynamoDB-specific caching** | **DAX** |
| **Provisioned RCU/WCU + automatically adjust capacity** | **DynamoDB Auto Scaling** |
| **Unpredictable DynamoDB traffic + no capacity planning** | **DynamoDB On-Demand** |
| **API response caching** | **API Gateway Cache** |
| **API Gateway + Lambda** | **API Gateway + Lambda** |
| **Global static content** | **CloudFront** |
| **Images/videos/game assets globally** | **CloudFront** |
| **Redis/Memcached/general application cache** | **ElastiCache** |
| **DynamoDB across multiple Regions** | **Global Tables** |
| **Global users + local database access** | **Global Tables** |
| **Serverless backend function** | **Lambda** |
| **Massively scalable NoSQL** | **DynamoDB** |
| **AWS resources managed from command line** | **AWS CLI** |
| **DynamoDB cache** | **DAX** |
| **General-purpose cache** | **ElastiCache** |
| **API-level cache** | **API Gateway Cache** |
| **CDN/edge cache** | **CloudFront** |
| **Multi-Region replication** | **Global Tables** |
| **Millions of DynamoDB reads + low latency + cost concern** | **DAX + appropriate capacity scaling** |

## 🧠 5-second exam decision

```
Question says:
"Millions of users"
       │
       ▼
"Read DynamoDB heavily"
       │
       ▼
"Improve performance"
       │
       ▼
      DAX
       │
       +
       │
"Need scalable capacity"
       │
       ▼
DynamoDB Auto Scaling
```

### Final answer:

**✅ Option A**

The key distinction to lock into memory is:

> **DAX = make DynamoDB reads faster.**
> 
> 
> **Auto Scaling = dynamically adjust provisioned DynamoDB capacity.**
> 
> **Global Tables = replicate DynamoDB across Regions.**
> 
> **API Gateway Cache = cache API responses.**
> 
> **ElastiCache = general-purpose application cache.**
>
