# Table-comparison-1

I checked the current AWS database lineup so the table doesn't miss the major managed database choices. AWS currently groups relational, key-value, in-memory, document, wide-column, graph, and time-series databases, with Redshift for analytics/data warehousing. ([AWS Documentation](https://docs.aws.amazon.com/databases-on-aws-how-to-choose/?utm_source=chatgpt.com))

## AWS Database Services — Master SAA-C03 Comparison

**Read this from left → right:**

**What data do I have? → What workload? → Which database?**

| Priority | AWS Database | Type / Data Model | What is it? | Best Use Case | Key SAA-C03 Clues | Easy Example | Memory Trick |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 🔥 **1** | **Amazon RDS** | Relational / SQL | Managed relational database service supporting MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Db2 | Traditional applications needing SQL, joins, transactions | **SQL + relational + familiar DB engines + Multi-AZ** | Company's ERP stores customers, orders and payments in related tables | **RDS = Regular Relational DB** |
| 🔥 **2** | **Amazon Aurora** | Relational / SQL | AWS cloud-optimized relational database compatible with MySQL/PostgreSQL | High-performance, highly available relational applications | **MySQL/PostgreSQL + high performance + 6 copies across 3 AZs + read replicas + Global Database** | Large e-commerce website needs highly available transactional DB | **Aurora = Supercharged RDS** |
| 🔥 **3** | **Amazon DynamoDB** | NoSQL / Key-value + Document | Serverless, fully managed NoSQL database | Massive scale with predictable, very low-latency access patterns | **NoSQL + key-value + single-digit ms + serverless + huge scale** | Shopping cart: `UserID → CartItems` | **DynamoDB = Key → Value at scale** |
| 🔥 **4** | **Amazon ElastiCache** | In-memory / Redis or Memcached | Managed cache that keeps frequently accessed data in memory | Reduce DB load and improve application response time | **Cache + microsecond latency + Redis/Memcached + session storage** | Website repeatedly reads the same product information → cache it | **ElastiCache = Cache it** |
| 🔥 **5** | **Amazon Redshift** | Relational / OLAP Data Warehouse | Managed cloud data warehouse for large-scale analytics | BI, reporting, analytics over huge datasets | **OLAP + analytics + data warehouse + SQL + columnar** | Company analyzes 5 years of sales data across billions of rows | **Redshift = Read/Analyze Huge Data** |
| 🔥 **6** | **Amazon DocumentDB** | NoSQL / Document | Managed document database with MongoDB compatibility | JSON/document-oriented applications | **MongoDB-compatible + JSON documents + flexible schema** | Product catalog where each product has different attributes | **DocumentDB = Documents** |
| 🔥 **7** | **Amazon Neptune** | Graph | Managed graph database | Highly connected relationships | **Social network + fraud detection + recommendations + relationships** | Find connections between people, accounts and suspicious transactions | **Neptune = Network of relationships** |
| 🔥 **8** | **Amazon Keyspaces** | NoSQL / Wide-column | Managed Apache Cassandra-compatible database | High-throughput Cassandra workloads | **Cassandra + wide-column + huge scale + low latency** | IoT application receives millions of device events | **Keyspaces = Cassandra** |
| 🔥 **9** | **Amazon MemoryDB** | In-memory / Redis-compatible | Durable in-memory database | Primary database requiring extremely fast reads plus durability | **Redis-compatible + durable + Multi-AZ + microsecond reads** | Microservices need an ultra-fast primary database that must persist data | **MemoryDB = Memory + Database durability** |
| ⭐ **10** | **Amazon Timestream** | Time-series | Managed database optimized for time-stamped data | IoT, metrics, monitoring, telemetry | **Time + timestamps + IoT + metrics** | Store CPU usage every second from thousands of servers | **Timestream = Time → Stream** |
| ⭐ **11** | **Amazon Aurora Serverless v2** | Relational / SQL | Aurora configuration that automatically adjusts compute capacity according to workload | Unpredictable/spiky database workloads | **Aurora + unpredictable traffic + automatic capacity scaling** | Normal traffic is low but a shopping sale creates huge DB demand | **Serverless = DB capacity follows demand** |
| ⭐ **12** | **Amazon Aurora Global Database** | Relational / SQL | Aurora architecture for cross-Region database replication | Global applications + disaster recovery + low-latency regional reads | **Cross-Region + Global Database + DR + read locally** | US application has users in Europe and Asia | **Aurora Global = One DB across Regions** |
| ⭐ **13** | **Amazon RDS Proxy** | DB connection management | Fully managed database proxy between applications and RDS/Aurora | Connection pooling and protecting DB from connection overload | **Connection pooling + Lambda + too many connections + RDS/Aurora** | Thousands of Lambda functions open DB connections → Proxy manages them | **Proxy = Manages Connections** |
| ⭐ **14** | **Amazon Aurora DSQL** | Distributed SQL / Relational | Serverless distributed SQL database designed for highly available, multi-Region applications | Globally distributed applications needing SQL | **Distributed SQL + active-active + multi-Region + serverless** | Global application needs writes in multiple Regions | **DSQL = Distributed SQL** |
| ⭐ **15** | **Amazon ElastiCache Serverless** | In-memory cache | Serverless ElastiCache that automatically scales cache capacity | Variable cache workloads without managing cluster capacity | **Serverless cache + automatic scaling + Redis/Valkey/Memcached context** | Traffic changes dramatically throughout the day and cache capacity should adapt | **Serverless Cache = Cache scales itself** |

AWS describes RDS and Aurora as relational choices; DynamoDB as key-value, ElastiCache/MemoryDB as in-memory; DocumentDB as document, Keyspaces as wide-column; Neptune as graph; and Timestream as time-series. ([AWS Documentation](https://docs.aws.amazon.com/databases-on-aws-how-to-choose/?utm_source=chatgpt.com))

---

# 🧠 The Most Important Decision Table

This is the part I'd **memorise conceptually**, not the individual service descriptions:

| If the question says... | Think... | Choose |
| --- | --- | --- |
| **SQL, tables, rows, columns, joins** | Relational | **RDS / Aurora** |
| SQL + **traditional DB engine** | Managed traditional relational | **RDS** |
| SQL + **high performance / high availability** | Cloud-optimized relational | **Aurora** |
| **MySQL/PostgreSQL compatible + higher performance** | Aurora | **Aurora** |
| **Unpredictable DB traffic + automatic scaling** | Serverless relational | **Aurora Serverless v2** |
| **Cross-Region Aurora + global application** | Global DB | **Aurora Global Database** |
| **Key-value / NoSQL + massive scale** | DynamoDB | **DynamoDB** |
| **Known access patterns + single-digit ms** | DynamoDB | **DynamoDB** |
| **Shopping cart / session / user preferences** | Key-value | **DynamoDB** |
| **JSON documents + MongoDB compatibility** | Document DB | **DocumentDB** |
| **Relationships / connected data** | Graph | **Neptune** |
| **Fraud detection relationships** | Graph | **Neptune** |
| **Social network relationships** | Graph | **Neptune** |
| **Cassandra-compatible** | Wide-column | **Keyspaces** |
| **IoT / metrics / timestamps** | Time-series | **Timestream** |
| **Cache frequently accessed data** | In-memory cache | **ElastiCache** |
| **Reduce DB load / improve read performance** | Cache | **ElastiCache** |
| **Redis + cache** | In-memory cache | **ElastiCache** |
| **Redis + durable primary database** | Durable in-memory DB | **MemoryDB** |
| **Microsecond reads + persistence** | Memory database | **MemoryDB** |
| **Huge analytics / BI / data warehouse** | OLAP | **Redshift** |
| **Complex analytical queries over huge datasets** | Data warehouse | **Redshift** |
| **Too many DB connections from Lambda** | Connection pooling | **RDS Proxy** |
| **Globally distributed SQL + active-active** | Distributed SQL | **Aurora DSQL** |

---

# 🔥 RDS vs Aurora vs DynamoDB — The Big 3

|  | **RDS** | **Aurora** | **DynamoDB** |
| --- | --- | --- | --- |
| Database type | Relational | Relational | NoSQL |
| SQL | ✅ | ✅ | ❌ Traditional SQL |
| Tables | ✅ | ✅ | ❌ Traditional relational tables |
| Joins | ✅ | ✅ | ❌ |
| Transactions | ✅ | ✅ | ✅ |
| Schema | Structured | Structured | Flexible |
| Scaling model | Instance-based | Cloud-optimized | Serverless/distributed |
| Best for | Traditional SQL apps | High-performance SQL apps | Massive-scale NoSQL |
| Typical latency | ms | ms | Single-digit ms |
| Horizontal scale | Read replicas etc. | Excellent read scaling | Excellent |
| Multi-AZ | ✅ | Built into architecture | ✅ |
| Cross-Region | Read replicas / features | **Global Database** | **Global Tables** |
| Example | ERP | E-commerce DB | Shopping cart |
| Main clue | **SQL + familiar engines** | **SQL + performance/HA** | **NoSQL + massive scale** |

AWS specifically distinguishes Aurora as the cloud-native relational option and RDS as the broader managed relational-engine choice. DynamoDB is optimized around known access patterns and consistent single-digit-millisecond performance at scale. ([Amazon Web Services, Inc.](https://aws.amazon.com/compare/aurora-and-rds/?utm_source=chatgpt.com))

---

# 🔥 ElastiCache vs MemoryDB — VERY Important

|  | **ElastiCache** | **MemoryDB** |
| --- | --- | --- |
| Main purpose | **Cache** | **Primary database** |
| Data persistence | Usually treated as ephemeral/cache data | **Durable** |
| Main goal | Reduce DB load | Fast durable database |
| Technology | Redis/Valkey/Memcached options | Redis-compatible |
| Performance | Extremely fast | Extremely fast |
| Example | Cache product information | Store application state as primary DB |
| Exam keyword | **Caching** | **Durable + in-memory** |

AWS explicitly describes ElastiCache as a caching layer, while MemoryDB is intended as a durable in-memory database that can act as a primary database. ([AWS Documentation](https://docs.aws.amazon.com/databases-on-aws-how-to-choose/?utm_source=chatgpt.com))

### Memory trick:

> **ElastiCache = "I already have a DB; make it faster."**
> 
> 
> **MemoryDB = "I want the database itself to be extremely fast."**
> 

---

# 🔥 DocumentDB vs DynamoDB vs Neptune

|  | **DynamoDB** | **DocumentDB** | **Neptune** |
| --- | --- | --- | --- |
| Model | Key-value + document | Document | Graph |
| Data | Items/attributes | JSON-like documents | Nodes + relationships |
| Best for | Massive-scale access patterns | MongoDB/document apps | Highly connected data |
| Example | Shopping cart | Product catalog | Social network |
| Key clue | **Key-value** | **MongoDB / JSON** | **Relationships** |
| Memory | **Key → Value** | **Document** | **Connections** |

AWS's database-selection guidance makes this distinction directly: DynamoDB is key-value, DocumentDB is document-oriented, and Neptune is optimized for graph relationships. ([AWS Documentation](https://docs.aws.amazon.com/databases-on-aws-how-to-choose/?utm_source=chatgpt.com))

---

# 🔥 RDS/Aurora vs Redshift

This is another **very common conceptual trap**.

|  | **RDS / Aurora** | **Redshift** |
| --- | --- | --- |
| Workload | **OLTP** | **OLAP** |
| Purpose | Application transactions | Analytics |
| Queries | Transactions | Large analytical queries |
| Example | Create order | Analyze 10 years of sales |
| Data volume | Normal application DB | Massive datasets |
| Typical users | Application | Data analysts / BI |
| Think | **Run the business** | **Analyze the business** |

AWS explicitly distinguishes RDS as transactional/OLTP and Redshift as analytical/data-warehouse/OLAP. ([Amazon Web Services, Inc.](https://aws.amazon.com/compare/?utm_source=chatgpt.com))

### Memory trick:

> **RDS/Aurora → Run the application**
> 
> 
> **Redshift → Read/analyze the data**
> 

---

# 🎯 The Ultimate SAA-C03 Database Decision Tree

```
                What kind of data?
                       │
        ┌──────────────┴──────────────┐
        │                             │
    RELATIONAL                     NoSQL
     / SQL                           │
        │                ┌────────────┼──────────────┐
        │                │            │              │
   RDS / Aurora       Key-Value    Document       Graph
        │                │            │              │
        │            DynamoDB     DocumentDB       Neptune
        │
        ├── Traditional DB engines?
        │       → RDS
        │
        ├── High performance / HA?
        │       → Aurora
        │
        ├── Unpredictable DB workload?
        │       → Aurora Serverless v2
        │
        └── Global Aurora application?
                → Aurora Global Database

Other specialized needs:

Cache → ElastiCache
Durable in-memory DB → MemoryDB
Cassandra → Keyspaces
Time-series → Timestream
Analytics / Data Warehouse → Redshift
Too many DB connections → RDS Proxy
Distributed SQL / active-active → Aurora DSQL
```

## 🧠 One-Line Memory Map

> **SQL → RDS/Aurora**
> 
> 
> **Key-Value → DynamoDB**
> 
> **MongoDB/JSON → DocumentDB**
> 
> **Relationships → Neptune**
> 
> **Cassandra → Keyspaces**
> 
> **Time → Timestream**
> 
> **Cache → ElastiCache**
> 
> **Durable Memory → MemoryDB**
> 
> **Analytics → Redshift**
> 
> **DB Connections → RDS Proxy**
> 

**For SAA-C03, focus especially on:** **RDS vs Aurora vs Aurora Serverless v2, DynamoDB, ElastiCache vs MemoryDB, DocumentDB, Neptune, Keyspaces, Redshift, RDS Proxy, and Aurora Global Database.** These are the distinctions most worth being able to recognise from scenario wording.
