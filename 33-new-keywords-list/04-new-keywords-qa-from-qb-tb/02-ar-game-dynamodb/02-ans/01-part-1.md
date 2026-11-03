# Part: 1

## 1. Restate the Scenario in Plain English

We have a very popular **AR mobile game** with a serverless backend.

Millions of players use the game every day. The application repeatedly needs to:

- **Read player information** from DynamoDB.
- **Write player information** to DynamoDB.
- Handle very large and potentially changing traffic.
- Keep response times fast.
- Scale automatically.
- Avoid paying for unnecessarily large always-on capacity.

### Actors

| Actor / Service | What it does |
| --- | --- |
| Mobile game users | Millions of players generating reads/writes |
| Lambda | Retrieves data from DynamoDB |
| DynamoDB | Stores player/user data |
| API Gateway | Potential API front door/cache |
| DAX | Optional in-memory cache for DynamoDB |
| DynamoDB Auto Scaling | Adjusts provisioned capacity based on utilization |
| Global Tables | Replicates DynamoDB data across AWS Regions |

### Current architecture

```
Millions of Mobile Players
          │
          ▼
     Serverless API
          │
          ▼
       Lambda
          │
          ▼
      DynamoDB
     ┌─────────┐
     │ User    │
     │ Data    │
     └─────────┘
```

The problem:

```
Millions of users
       │
       ├──── Read ────┐
       │              │
       ├──── Write ───┤
       │              ▼
       │          DynamoDB
       │              ▲
       └──── Read ────┘
```

There may be huge numbers of repeated reads for the same player/game data.

### Desired architecture

The goal is essentially:

```
Millions of Players
        │
        ▼
     Lambda
        │
        ▼
      DAX
   ┌────┴─────┐
   │           │
Cache Hit    Cache Miss
   │           │
   ▼           ▼
 Return     DynamoDB
 quickly
```

while DynamoDB capacity automatically adjusts:

```
Traffic
  │
  ▼
DynamoDB
  │
  ▼
Auto Scaling
  │
  ├── Scale up when demand rises
  └── Scale down when demand falls
```

---

# 2. Identify the Hidden Sub-Problems

There are **two major problems** hidden in the question.

| Symptom | Underlying Category | Why |
| --- | --- | --- |
| Millions of users repeatedly reading DynamoDB data | **Caching / read acceleration** | Frequently requested data should ideally be served from memory instead of repeatedly hitting DynamoDB |
| Traffic can vary significantly | **Automatic capacity scaling** | DynamoDB should increase/decrease provisioned capacity according to demand |
| Need low cost | **Avoid permanent over-provisioning** | Don't simply provision huge RCU/WCU all the time |
| Need overall scalability | **DynamoDB scaling + caching** | Reduce database pressure while allowing capacity to adapt |
| Serverless architecture | **Managed AWS services** | Prefer managed scaling/caching over custom infrastructure |

### The key mental model

```
HIGH READ LOAD
     │
     ▼
Need fewer DynamoDB reads
     │
     ▼
   DAX
```

and:

```
VARIABLE LOAD
     │
     ▼
Need capacity to adapt
     │
     ▼
DynamoDB Auto Scaling
```

That combination points strongly toward **Option A**.

However, we need to carefully examine the other options because several contain partially valid AWS technologies.

---

# 3. Explain Every Option in Plain Language

---

## Option A

> Enable DynamoDB Accelerator (DAX) and ensure that Auto Scaling is enabled and increase the maximum provisioned read and write capacity.
> 

### What is this option trying to say?

Use **DAX to cache frequently accessed DynamoDB data**, while allowing DynamoDB provisioned capacity to automatically scale when traffic changes.

```
             Mobile Users
                  │
                  ▼
               Lambda
                  │
                  ▼
                 DAX
             ┌────┴────┐
             │         │
         Cache Hit   Cache Miss
             │         │
             ▼         ▼
          Response   DynamoDB
                       │
                       ▼
                  Auto Scaling
```

### Verdict: ✅ Correct

This is the strongest answer for the stated requirements.

### Why?

The application has:

> **millions of users each day to read and store data**
> 

DAX is specifically designed to accelerate DynamoDB workloads.

Instead of:

```
User → Lambda → DynamoDB
```

every time, frequently accessed data can be:

```
User → Lambda → DAX → Response
```

This reduces pressure on DynamoDB and provides much faster reads.

### Auto Scaling

If the application uses provisioned capacity, DynamoDB Auto Scaling can dynamically adjust capacity based on utilization.

```
Low traffic
   ↓
Lower provisioned capacity
   ↓
Lower cost

High traffic
   ↓
Higher provisioned capacity
   ↓
Handle more requests
```

### Important nuance: "increase the maximum"

The wording isn't the most elegant part of this answer.

Increasing the maximum capacity provides Auto Scaling enough headroom for traffic spikes.

But remember:

> **Setting a high maximum doesn't mean DynamoDB immediately charges you for that maximum.**
> 

The scaling mechanism adjusts actual provisioned capacity within the configured range.

So:

```
Minimum = 100
Maximum = 10,000

Current traffic → 500
Actual provisioned capacity ≈ appropriate level
```

not:

```
Maximum = 10,000
       ↓
Always pay for 10,000
```

### Why this is cost-effective

DAX reduces repeated DynamoDB reads, while Auto Scaling prevents you from having to permanently provision enormous capacity.

---

# Option B

> Configure CloudFront with DynamoDB as the origin; cache frequently accessed data on the client device using ElastiCache.
> 

### What is this trying to say?

It proposes:

```
Mobile User
    │
    ▼
CloudFront
    │
    ▼
DynamoDB
```

and somehow:

```
Mobile Device
     │
     ▼
ElastiCache
```

### Verdict: ❌ Incorrect

There are multiple problems.

---

### Problem 1 — CloudFront isn't used this way with DynamoDB

CloudFront is primarily a **content delivery network**.

Typical architecture:

```
User
 │
 ▼
CloudFront
 │
 ├── S3
 ├── ALB
 ├── API Gateway
 └── custom HTTP origin
```

You don't simply configure DynamoDB as a standard CloudFront origin for arbitrary DynamoDB API/data caching.

---

### Problem 2 — ElastiCache isn't a client-device cache

This is another major trap.

ElastiCache is a server-side AWS caching service:

```
Application
    │
    ▼
ElastiCache
    │
    ▼
Database
```

not:

```
Mobile Device
    │
    ▼
ElastiCache
```

The cache runs in AWS infrastructure, generally within a VPC.

### ElastiCache vs DAX

For DynamoDB-specific caching:

```
DynamoDB
    ↑
   DAX
```

is the natural AWS service.

ElastiCache is a general-purpose caching system:

```
Application
    │
    ▼
Redis/Memcached
    │
    ▼
Database/application data
```

---

# Option C

> Since Auto Scaling is enabled by default, the provisioned read and write capacity will adjust automatically. Also enable DAX to improve the performance from milliseconds to microseconds.
> 

### What is this trying to say?

```
Application
    │
    ▼
   DAX
    │
    ▼
DynamoDB
    │
    ▼
Auto Scaling
```

The idea is very similar to Option A.

### Verdict: ❌ Incorrect

**But this is a very tempting distractor.**

The problem is the statement:

> **"Since Auto Scaling is enabled by default"**
> 

That is false.

DynamoDB **does not automatically enable provisioned-capacity Auto Scaling simply because you create a DynamoDB table**.

You need to configure Auto Scaling for the relevant provisioned capacity.

### Important exam distinction

Don't memorize:

> DynamoDB = Auto Scaling by default.
> 

Instead:

```
DynamoDB capacity modes
        │
        ├── On-demand
        │      ↓
        │  automatically handles capacity
        │
        └── Provisioned
               ↓
        Auto Scaling can be configured
```

So Option C contains a good idea:

> DAX improves DynamoDB read performance.
> 

But it attaches that idea to a false claim about Auto Scaling being enabled by default.

Therefore the entire answer is incorrect.

### DAX performance

DAX is designed to provide **microsecond-level response times for cached reads**, compared with the typical millisecond-scale latency of DynamoDB operations.

The important point isn't to memorize an exact number.

Think:

```
DynamoDB
   ↓
milliseconds

DAX cache hit
   ↓
microseconds
```

---

# Option D

> Use API Gateway in conjunction with Lambda and turn on the caching on frequently accessed data and enable DynamoDB global replication.
> 

### What is this trying to say?

It proposes:

```
Mobile Users
     │
     ▼
API Gateway
     │
     ▼
API Gateway Cache
     │
     ▼
Lambda
     │
     ▼
DynamoDB
```

and:

```
DynamoDB Region A
       │
       │ Global Tables
       ▼
DynamoDB Region B
       │
       ▼
DynamoDB Region C
```

### Verdict: ⚠️ Partially valid but not best

There are useful concepts here, but they don't fit the question as well as Option A.

---

### API Gateway caching

API Gateway can cache responses for configured APIs.

For repeated requests:

```
Client
  │
  ▼
API Gateway
  │
  ├── Cache hit → return cached response
  │
  └── Cache miss
          │
          ▼
       Lambda
          │
          ▼
      DynamoDB
```

That can absolutely improve performance and reduce backend requests.

So this portion is technically useful.

---

### But why isn't it the best answer?

The question specifically describes:

> **DynamoDB storing huge amounts of player data with millions of users reading and writing.**
> 

DAX is purpose-built for accelerating DynamoDB access.

Also, API Gateway caching is tied to API requests and cache configuration, whereas DAX is directly integrated with DynamoDB access patterns.

---

### Global Tables

DynamoDB Global Tables replicate DynamoDB tables across multiple AWS Regions.

That's useful when you need:

- multi-Region applications
- users geographically distributed around the world
- regional resilience
- local reads/writes
- low latency across Regions

Architecture:

```
                  Global Table
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
      us-east-1    eu-west-1   ap-southeast-1
       DynamoDB     DynamoDB     DynamoDB
```

But the question doesn't say:

> "Users are distributed globally and need low-latency regional access."
> 

Nor does it say:

> "The application must survive a Region failure."
> 

Therefore Global Tables introduce additional complexity/cost without directly addressing the primary problem.

---

# 4. Deep-Dive Every AWS Concept Touched by the Question

---

## 4.1 DynamoDB

### Definition

Amazon DynamoDB is a fully managed NoSQL database designed for very high scalability, low latency, and massive request volumes. It supports key-value and document data models.

```
Application
    │
    ├── PutItem
    ├── GetItem
    ├── UpdateItem
    └── Query
          │
          ▼
      DynamoDB
```

### Worked example

A game stores:

```
PlayerID = 12345
Level = 47
Coins = 9200
Weapons = [...]
```

The application can retrieve the player's information using the partition key.

### DynamoDB vs RDS

| DynamoDB | RDS |
| --- | --- |
| NoSQL | Relational |
| Key-value/document | Tables/rows/SQL |
| Massive horizontal scalability | Traditional relational architecture |
| Serverless/managed | Managed DB servers |
| Great for predictable access patterns | Great for relational queries/transactions |

### Exam trigger

> **"Massive scale + NoSQL + key-value/document + very low latency" → DynamoDB**
> 

---

# 4.2 DynamoDB Accelerator (DAX)

### Definition

DAX is a fully managed, in-memory cache designed specifically for DynamoDB. It sits between your application and DynamoDB and can return cached data much faster than querying DynamoDB directly.

```
Application
     │
     ▼
    DAX
   ┌───┴────┐
   │        │
Hit       Miss
   │        │
   ▼        ▼
Return   DynamoDB
             │
             ▼
          DAX cache
```

### Worked example

Suppose millions of players repeatedly request:

```
Game configuration
Player profile
Leaderboard-related data
Frequently accessed items
```

Instead of every read reaching DynamoDB:

```
1,000,000 reads
       │
       ▼
      DAX
       │
       ├── 950,000 cache hits
       │
       └── 50,000 DynamoDB reads
```

This can dramatically reduce database read pressure.

### DAX vs ElastiCache

| DAX | ElastiCache |
| --- | --- |
| Specifically designed for DynamoDB | General-purpose cache |
| DynamoDB API compatible | Redis/Memcached |
| Easy DynamoDB caching | More application-level cache logic |
| Excellent for DynamoDB read acceleration | Useful for broader caching requirements |
| Microsecond-level cached reads | Depends on workload/configuration |

### Exam trigger

> **"Accelerate DynamoDB reads / microsecond reads / DynamoDB cache" → DAX**
> 

---

# 4.3 DynamoDB Auto Scaling

### Definition

DynamoDB Auto Scaling automatically adjusts **provisioned read/write capacity** within configured minimum and maximum values based on utilization.

```
             Traffic
                │
        ┌───────┴───────┐
        ▼               ▼
     Low load         High load
        │               │
        ▼               ▼
     Scale down       Scale up
        │               │
        └───────┬───────┘
                ▼
       Provisioned capacity
```

### Worked example

Suppose:

```
Minimum RCU = 100
Maximum RCU = 10,000
```

Traffic increases:

```
100 → 500 → 2,000 → 8,000
```

Auto Scaling can increase provisioned capacity within the configured limits.

When demand drops, it can scale down.

### Auto Scaling vs On-Demand Capacity

| Provisioned + Auto Scaling | On-Demand |
| --- | --- |
| Set min/max capacity | No capacity planning |
| Capacity scales within configured range | Automatically handles changing traffic |
| Useful for predictable workloads | Excellent for unpredictable workloads |
| Can be cost-efficient at sustained usage | Pay-per-request |
| Requires configuration | Simpler operationally |

### Exam trigger

> **"Provisioned RCU/WCU should automatically increase/decrease" → DynamoDB Auto Scaling**
> 

---

# 4.4 API Gateway

### Definition

Amazon API Gateway is a managed service for creating, publishing, securing, monitoring, and managing APIs. It commonly sits in front of Lambda in serverless architectures.

```
Mobile App
    │
    ▼
API Gateway
    │
    ▼
Lambda
    │
    ▼
DynamoDB
```

### Worked example

```
GET /player/123
       │
       ▼
API Gateway
       │
       ▼
Lambda
       │
       ▼
DynamoDB
```

### API Gateway vs Application Load Balancer

| API Gateway | ALB |
| --- | --- |
| API management service | Layer 7 load balancer |
| Excellent serverless integration | Excellent HTTP load balancing |
| API authentication/throttling/caching | Routes requests to targets |
| Lambda integration | EC2/ECS/EKS/IP/Lambda targets |
| API-oriented | Application traffic distribution |

### Exam trigger

> **"Managed API + Lambda + throttling/auth/API management" → API Gateway**
> 

---

# 4.5 API Gateway Caching

### Definition

API Gateway caching stores responses from backend integrations so repeated requests can sometimes be served without invoking Lambda or querying the database again.

```
Client
  │
  ▼
API Gateway
  │
  ├── Cache HIT ─────→ Response
  │
  └── Cache MISS
          │
          ▼
        Lambda
          │
          ▼
      DynamoDB
```

### Worked example

If 100,000 users request the same relatively static game configuration:

```
100,000 requests
       │
       ▼
API Gateway cache
       │
       ├── 99,000 hits
       └── 1,000 backend requests
```

This reduces Lambda/database traffic.

### API Gateway Cache vs DAX

| API Gateway Cache | DAX |
| --- | --- |
| Caches API responses | Caches DynamoDB data |
| Sits at API layer | Sits at database access layer |
| Useful for repeated API requests | Useful for DynamoDB read acceleration |
| HTTP/API-oriented | DynamoDB-oriented |
| Doesn't directly replace DynamoDB caching | Specifically built for DynamoDB |

### Exam trigger

> **"Cache API responses before reaching Lambda/backend" → API Gateway caching**
> 

---

# 4.6 DynamoDB Global Tables

### Definition

DynamoDB Global Tables provide multi-Region replication of DynamoDB tables, allowing applications to use replicas in multiple AWS Regions.

```
             Global Table
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
   Region A    Region B    Region C
   DynamoDB    DynamoDB    DynamoDB
```

### Worked example

A global game has players in:

```
USA → us-east-1
Europe → eu-west-1
Asia → ap-southeast-1
```

Users can access a nearby regional DynamoDB replica.

### Global Tables vs DAX

| Global Tables | DAX |
| --- | --- |
| Multi-Region replication | In-memory caching |
| Geographic availability/latency | Read acceleration |
| Replicates database data | Caches database data |
| Useful for global applications | Useful for high-read DynamoDB workloads |

### Exam trigger

> **"Multi-Region DynamoDB / global users / regional resilience" → Global Tables**
> 

---

# 4.7 AWS Lambda

### Definition

Lambda is AWS's serverless compute service that executes code in response to events or requests without requiring you to manage servers.

```
API Gateway
     │
     ▼
   Lambda
     │
     ▼
 DynamoDB
```

### Worked example

```
GET /player/123
       ↓
API Gateway
       ↓
Lambda
       ↓
DynamoDB
       ↓
Player data
```

### Lambda vs Fargate

| Lambda | Fargate |
| --- | --- |
| Functions | Containers |
| Event/request driven | Long-running/container workloads |
| No server management | No server management |
| Pay per invocation/runtime | Pay for allocated container resources |
| Short/variable execution | Containerized applications |

### Exam trigger

> **"Serverless function / event-driven execution / no servers" → Lambda**
>
