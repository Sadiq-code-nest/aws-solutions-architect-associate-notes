# Part: 1

## AWS Caching Strategies — SAA-C03 Exam Note

The easiest way to understand AWS caching is:

> **Cache = keep frequently requested data closer to the user/application so you don't repeatedly fetch or compute it.**
> 

Without caching:

**User → Application → Database → Response**

With caching:

**User → Cache → Response**

The cache can exist at different layers depending on **what you're trying to cache**.

---

# 1. The Big Picture

```
                         USERS
                           │
                           ▼
                    ┌─────────────┐
                    │ CloudFront  │  ← Cache content near users
                    │ Edge Cache  │
                    └──────┬──────┘
                           │ Cache miss
                           ▼
                    ┌─────────────┐
                    │ API Gateway │  ← Cache API responses
                    └──────┬──────┘
                           │
                           ▼
                 ┌──────────────────┐
                 │ Application      │
                 │ EC2 / Lambda     │
                 └────────┬─────────┘
                          │
                    ┌─────┴─────┐
                    │           │
                    ▼           ▼
               Redis /      Database
             Memcached         RDS
                    │
                    ▼
                 DAX
             for DynamoDB
```

**Important:** These are not interchangeable caches. Each one solves a different problem.

---

# 2. Cache by Layer — The Most Important Concept

| Layer | AWS Service | What does it cache? | Main purpose |
| --- | --- | --- | --- |
| **User → AWS** | **CloudFront** | Static/dynamic HTTP content | Reduce latency for users |
| **API layer** | **API Gateway Cache** | API responses | Avoid repeatedly invoking backend |
| **Application layer** | **ElastiCache Redis** | Application data | Fast data access |
| **Application layer** | **ElastiCache Memcached** | Temporary cached data | Simple distributed caching |
| **DynamoDB layer** | **DAX** | DynamoDB query/read results | Microsecond-level reads |
| **Database** | RDS/Aurora | DB-specific internal caches | Database performance |

### Memory trick

> **CloudFront = User cache**
> 
> 
> **API Gateway = API cache**
> 
> **Redis/Memcached = Application cache**
> 
> **DAX = DynamoDB cache**
> 

---

# 3. CloudFront — Cache at the Edge

CloudFront is a **CDN (Content Delivery Network)**.

It caches content at AWS **Edge Locations** around the world.

### Example

Your application is hosted in:

**AWS us-east-1**

A user is in Bangladesh.

Without CloudFront:

```
Bangladesh User
      │
      │ Internet
      ▼
US-East-1
Application
```

Every request travels to the US.

With CloudFront:

```
Bangladesh User
      │
      ▼
CloudFront Edge Location
      │
      │ Cache HIT
      ▼
Response
```

The content can be delivered from a location closer to the user.

### What can CloudFront cache?

Examples:

- Images
- CSS
- JavaScript
- Videos
- HTML
- API responses
- Other HTTP content

### Key concept: Cache Hit

If the requested object is already cached:

```
User → CloudFront → CACHE HIT → Response
```

No request needs to reach the origin.

### Cache Miss

```
User → CloudFront → CACHE MISS
                    │
                    ▼
                  Origin
                    │
                    ▼
              CloudFront Cache
                    │
                    ▼
                  User
```

### SAA-C03 clue

If the question says:

> "Reduce latency for users around the world"
> 

Think:

**CloudFront**

---

# 4. CloudFront — Why Caching Helps

Caching gives you three major benefits:

### ① Lower latency

The data is physically closer to the user.

### ② Lower origin load

The application doesn't have to handle every request.

### ③ Lower cost

Fewer requests reach your EC2/Lambda/application/database.

---

# 5. TTL — How Long Does CloudFront Keep Data?

TTL = **Time To Live**

Example:

```
TTL = 1 hour
```

CloudFront can keep the cached object for approximately one hour before needing to revalidate/fetch it according to the cache policy and origin behavior.

### Exam concept

**Longer TTL**

→ Better cache hit ratio

→ Less origin traffic

→ Potentially more stale content

**Shorter TTL**

→ Fresher content

→ More origin requests

→ Potentially lower cache hit ratio

### Memory trick

> **TTL = "How long should I remember this?"**
> 

---

# 6. CloudFront — Cache Key

CloudFront doesn't simply cache "everything."

It determines whether requests are equivalent based on the cache policy/cache key.

For example:

```
GET /products?id=10
GET /products?id=20
```

These may represent different cached objects depending on the cache key configuration.

### Exam clue

If the question talks about:

- Cache behavior
- Cache key
- Query strings
- Headers
- Cookies
- TTL
- Edge locations

Think:

**CloudFront**

---

# 7. API Gateway Cache

API Gateway can cache responses from your backend.

Architecture:

```
Client
   │
   ▼
API Gateway
   │
   ├── Cache HIT → Response
   │
   └── Cache MISS
          │
          ▼
       Lambda / EC2
```

### Example

Suppose you have:

```
GET /products
```

10,000 users request the same product list.

Without API caching:

```
10,000 requests
       ↓
10,000 backend calls
```

With API Gateway caching:

```
First request
    ↓
Backend
    ↓
API Gateway Cache

Next requests
    ↓
Cache
    ↓
Response
```

### Why?

Reduce:

- Backend workload
- Lambda invocations
- Database queries
- Response latency

### Exam clue

If the question specifically says:

> "Cache API responses"
> 

Think:

**API Gateway caching**

---

# 8. ElastiCache — Application-Level Caching

Now we move deeper into the architecture.

Suppose your application repeatedly asks:

```
"What is the user's profile?"
"What are today's products?"
"What is the current session?"
```

Instead of repeatedly querying RDS:

```
EC2 / Lambda
      │
      ▼
    RDS
```

Use:

```
EC2 / Lambda
      │
      ▼
 ElastiCache
      │
      │ Cache miss
      ▼
     RDS
```

The application can retrieve frequently accessed data much faster.

---

# 9. ElastiCache Redis vs Memcached

This is **very important for SAA-C03**.

| Feature | Redis | Memcached |
| --- | --- | --- |
| Type | In-memory data store/cache | In-memory cache |
| Data structures | Rich | Simple key-value |
| Persistence | Supported | No |
| Replication | Supported | No |
| High availability | Supported | Limited/simple architecture |
| Multi-AZ | Supported with appropriate configuration | No native replication |
| Automatic failover | Yes | No |
| Pub/Sub | Yes | No |
| Transactions | Yes | No |
| Backup/restore | Yes | No |
| Best for | Advanced caching, sessions, HA | Simple distributed caching |
| Complexity | More features | Simpler |

### Memory trick

> **Redis = Rich + Reliable**
> 

> **Memcached = Minimal + Memory cache**
> 

---

# 10. Redis — Real Example

Imagine an online shopping website.

Users repeatedly request:

```
Product #100
Product #100
Product #100
Product #100
```

Without cache:

```
Application
    ↓
RDS
    ↓
Product #100
```

Every time.

With Redis:

```
Application
    ↓
Redis
    │
    ├── HIT → Product #100
    │
    └── MISS
          ↓
         RDS
```

After the first request:

```
RDS → Redis → Application
```

Future requests can come directly from Redis.

---

# 11. Redis Is More Than Just Cache

Redis can be useful for:

- Session storage
- Caching
- Leaderboards
- Counters
- Pub/Sub
- Real-time applications
- Frequently accessed data

### SAA-C03 clue

If you see:

> "Session data must be shared between multiple EC2 instances"
> 

Think:

**ElastiCache Redis**

Example:

```
             Load Balancer
             /           \
            ▼             ▼
         EC2-A          EC2-B
            \             /
             ▼           ▼
              Redis
```

Now both EC2 instances can access the same session data.

---

# 12. Memcached — When to Think About It

Memcached is useful when you need:

> **Simple, fast, temporary caching**
> 

Example:

```
EC2
 │
 ▼
Memcached
 │
 └── Cache miss → RDS
```

You don't need:

- Persistence
- Replication
- Advanced data structures
- Pub/Sub
- Complex features

### Exam clue

> "Simple distributed cache"
> 

→ **Memcached**

---

# 13. DAX — DynamoDB Accelerator

This one is extremely important.

**DAX is specifically designed for DynamoDB.**

Architecture:

```
Application
     │
     ▼
    DAX
     │
     │ Cache miss
     ▼
 DynamoDB
```

DAX provides an in-memory cache in front of DynamoDB.

### Why use DAX?

To improve read performance and reduce the number of reads reaching DynamoDB.

### Example

Suppose a gaming application frequently requests:

```
Player #123
```

Without DAX:

```
Application → DynamoDB
Application → DynamoDB
Application → DynamoDB
```

With DAX:

```
Application
     │
     ▼
    DAX
     │
     ├── HIT → Return immediately
     │
     └── MISS → DynamoDB
```

---

# 14. DAX vs ElastiCache

Very common exam comparison.

| Feature | DAX | ElastiCache |
| --- | --- | --- |
| Designed for | DynamoDB | General application data |
| Supported engines | DynamoDB | Redis / Memcached |
| Purpose | Cache DynamoDB reads | Application caching |
| Application changes | Minimal for supported DynamoDB operations | Usually application integration required |
| Best clue | DynamoDB performance | General caching/session/data |
| Typical use | Extremely fast DynamoDB reads | Sessions, cached DB results, application data |

### Memory trick

> **DAX → DynamoDB Accelerator**
> 

The **D** in DAX reminds you:

**DAX → DynamoDB**
