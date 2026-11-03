# 15. The Complete Caching Architecture

Imagine an e-commerce application.

```
                         USERS
                           │
                           ▼
                    ┌─────────────┐
                    │ CloudFront  │
                    │ Edge Cache  │
                    └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │ API Gateway │
                    │ API Cache   │
                    └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │ Application │
                    │ EC2/Lambda  │
                    └──────┬──────┘
                           │
                     ┌─────┴──────┐
                     │            │
                     ▼            ▼
              ┌───────────┐   ┌───────────┐
              │  Redis    │   │    DAX    │
              │ ElastiCache│   │ DynamoDB │
              └─────┬─────┘   └─────┬─────┘
                    │               │
                    ▼               ▼
                   RDS          DynamoDB
```

Each cache has a different job.

---

# 16. What Problem Are You Trying to Solve?

This is the **best SAA-C03 decision-making approach**.

| Question says... | Think... |
| --- | --- |
| Users are globally distributed | **CloudFront** |
| Reduce latency for static content | **CloudFront** |
| Cache API responses | **API Gateway Cache** |
| Reduce repeated database queries | **ElastiCache** |
| Share session data between EC2 instances | **ElastiCache Redis** |
| Need advanced in-memory data structures | **Redis** |
| Need simple temporary distributed cache | **Memcached** |
| Need to accelerate DynamoDB reads | **DAX** |
| Need DynamoDB-specific caching | **DAX** |
| Need cache at the edge | **CloudFront** |
| Need application-level cache | **ElastiCache** |

---

# 17. Cache Layer Comparison

| Layer | Service | Location | Main Cache | Exam Keyword |
| --- | --- | --- | --- | --- |
| **Edge** | CloudFront | Edge Locations | HTTP/content | Global users / low latency |
| **API** | API Gateway Cache | API Gateway | API responses | Cache API responses |
| **Application** | Redis | In-memory | Application data/session | Advanced cache |
| **Application** | Memcached | In-memory | Simple key/value | Simple cache |
| **DynamoDB** | DAX | In-memory | DynamoDB data | Accelerate DynamoDB |

---

# 18. Caching vs Database

A common misunderstanding:

**Cache does NOT normally replace your database.**

Think:

```
              ┌─────────────┐
              │    Cache    │
              │ Fast / temp │
              └──────┬──────┘
                     │
               Cache Miss
                     │
                     ▼
              ┌─────────────┐
              │  Database   │
              │ Source data │
              └─────────────┘
```

Database = **source of truth**

Cache = **fast copy of frequently used data**

---

# 19. Cache-Aside Pattern

This is a useful architecture pattern to understand.

```
Application
     │
     ▼
Check Cache
     │
 ┌───┴────┐
 │        │
HIT      MISS
 │        │
 ▼        ▼
Return   Database
           │
           ▼
         Cache
           │
           ▼
         Return
```

### Example

User asks:

> "Show product #100."
> 

Application:

**Step 1:** Check Redis.

If present:

```
Redis → Application → User
```

If not:

```
Redis → MISS
       ↓
      RDS
       ↓
      Redis
       ↓
 Application
```

### Memory trick

> **Cache HIT = fast path**
> 
> 
> **Cache MISS = database path**
> 

---

# 20. CloudFront vs ElastiCache

Very important.

|  | CloudFront | ElastiCache |
| --- | --- | --- |
| Layer | Edge/CDN | Application |
| Main purpose | Deliver content closer to users | Speed up application data access |
| Global edge locations | Yes | No |
| Typical content | Images, videos, HTTP responses | Database results, sessions, application data |
| Main benefit | Lower user latency | Lower database/application latency |
| Example | Website images | User session |

### Simple rule

> **Closer to USER → CloudFront**
> 

> **Closer to APPLICATION → ElastiCache**
> 

---

# 21. API Gateway Cache vs CloudFront

|  | CloudFront | API Gateway Cache |
| --- | --- | --- |
| Main purpose | CDN/content delivery | Cache API responses |
| Layer | Edge | API |
| Typical use | Images, videos, web content, APIs | REST API responses |
| Location | Edge Locations | API Gateway |
| Exam clue | Global users / CDN | API response caching |

---

# 22. ElastiCache Redis vs DAX

|  | Redis | DAX |
| --- | --- | --- |
| Works with | General applications | DynamoDB |
| Primary purpose | Application caching | DynamoDB acceleration |
| Sessions | ✅ Excellent | ❌ |
| General cache | ✅ | ❌ |
| DynamoDB-specific | ❌ | ✅ |
| Advanced data structures | ✅ | ❌ |
| Memory trick | **Rich cache** | **DynamoDB Accelerator** |

---

# 23. SAA-C03 Scenario Questions

### Scenario 1

> A company has customers worldwide and wants to reduce latency when delivering images and videos.
> 

**Answer: CloudFront**

Keyword:

**Worldwide + content + latency**

---

### Scenario 2

> An application running on multiple EC2 instances needs shared session data.
> 

**Answer: ElastiCache for Redis**

Why?

```
EC2-A ──┐
        ├── Redis
EC2-B ──┘
```

Keyword:

**Shared sessions**

---

### Scenario 3

> An application performs millions of reads against DynamoDB and requires extremely low-latency access.
> 

**Answer: DAX**

Keyword:

**DynamoDB + caching + very low latency**

---

### Scenario 4

> A REST API receives many repeated requests and the company wants to reduce backend calls.
> 

**Answer: API Gateway caching**

Keyword:

**API response caching**

---

### Scenario 5

> An application needs a simple distributed in-memory cache. Persistence and replication aren't required.
> 

**Answer: Memcached**

Keyword:

**Simple + temporary + distributed cache**

---

### Scenario 6

> An application needs advanced caching capabilities, session storage, replication, and failover.
> 

**Answer: Redis**

Keyword:

**Advanced + HA + replication + sessions**

---

# 24. Exam Decision Tree 🧠

When you see a caching question:

```
             WHAT ARE YOU CACHING?
                     │
        ┌────────────┼─────────────┐
        │            │             │
      Content       API         Database/
        │            │          App Data
        ▼            ▼             │
   CloudFront   API Gateway       │
                                  │
                         ┌────────┴────────┐
                         │                 │
                    DynamoDB?        General App?
                         │                 │
                         ▼                 ▼
                        DAX          ElastiCache
                                           │
                                    ┌──────┴──────┐
                                    │             │
                                  Redis       Memcached
                                    │             │
                             Advanced/HA      Simple cache
```

---

# 25. Final SAA-C03 Cheat Sheet

| Service | Remember This | Typical Scenario |
| --- | --- | --- |
| **CloudFront** | **Cache at the Edge** | Global users need low-latency content |
| **API Gateway Cache** | **Cache API responses** | Reduce repeated backend/API calls |
| **ElastiCache Redis** | **Advanced application cache** | Sessions, HA, replication, rich data structures |
| **ElastiCache Memcached** | **Simple distributed cache** | Temporary/simple caching |
| **DAX** | **DynamoDB Accelerator** | Extremely fast DynamoDB reads |

### 🔥 One-line memory trick

> **CloudFront = User** → **API Gateway = API** → **Redis/Memcached = App** → **DAX = DynamoDB**
> 

And remember the fundamental idea:

> **Cache HIT → serve quickly from cache.**
> 
> 
> **Cache MISS → retrieve from the source, then cache it.**
> 

That's the core caching model you need for **SAA-C03**.
