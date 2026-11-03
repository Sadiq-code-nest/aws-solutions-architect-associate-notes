# AWS Database Services — Master Exam Comparison

| Feature | **RDS** | **Aurora** | **Aurora Serverless v2** | **DynamoDB** | **ElastiCache** | **MemoryDB** | **DocumentDB** | **Neptune** | **Keyspaces** | **Timestream** | **Redshift** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Database Type** | Relational | Relational | Relational | NoSQL | In-memory cache | In-memory database | Document | Graph | Wide-column | Time-series | Data warehouse |
| **Database Model** | SQL / tables | SQL / tables | SQL / tables | Key-value + Document | Key-value cache | Key-value | JSON documents | Graph / relationships | Wide-column | Time-series | Relational / analytical |
| **Main Purpose** | Managed traditional SQL database | High-performance cloud relational DB | Automatically scalable Aurora | Massive-scale NoSQL | Speed up applications | Fast + durable primary DB | MongoDB-compatible document DB | Connected-data relationships | Cassandra-compatible workloads | Time-based data | Large-scale analytics |
| **SQL Support** | ✅ | ✅ | ✅ | ❌ Traditional SQL | ❌ | ❌ Traditional SQL | Document queries | Graph query languages | CQL | SQL-like querying | ✅ |
| **Joins** | ✅ | ✅ | ✅ | ❌ Traditional relational joins | ❌ | ❌ | ❌ Traditional joins | Relationship traversal | ❌ | ❌ | ✅ |
| **Transactions** | ✅ | ✅ | ✅ | ✅ | ❌ Main purpose isn't transactions | ✅ | ✅ | ✅ | Limited/model-dependent | Not primary focus | ✅ |
| **Schema** | Structured | Structured | Structured | Flexible | Flexible | Flexible | Flexible | Graph structure | Wide-column | Time-based | Structured |
| **Typical Workload** | OLTP | OLTP | Variable OLTP | High-scale OLTP | Caching | Primary DB | Document applications | Relationship-heavy | High-throughput NoSQL | IoT / metrics | OLAP |
| **Scalability** | Instance-based | Excellent | Automatic compute scaling | Massive automatic scaling | Horizontal/automatic depending on configuration | Scales for high throughput | Scalable | Scalable | Massive scale | Automatic/serverless | Massive analytical scale |
| **Compute Scaling** | Resize instance | Resize/add replicas | **Automatically adjusts capacity** | Serverless/on-demand or provisioned | Cache capacity scaling | Cluster scaling | Managed | Managed | Serverless/provisioned options | Serverless | Managed warehouse |
| **Storage Scaling** | Depends on engine/configuration | **Auto-growing distributed storage** | Aurora storage | Automatically managed | Memory/cache capacity | Durable storage architecture | Managed | Managed | Managed | Managed | Managed |
| **High Availability** | Multi-AZ | **Built-in distributed architecture** | Aurora architecture | Built-in | Multi-AZ options | Multi-AZ | Multi-AZ | Multi-AZ | Multi-AZ | Managed | Managed |
| **Storage Architecture** | DB instance + EBS-backed storage | **Distributed shared storage** | Aurora distributed storage | Distributed | In-memory | Distributed durable memory | Distributed | Distributed | Distributed | Distributed | Distributed |
| **Storage Copies** | Depends on setup | **6 copies across 3 AZs** | Same Aurora architecture | Multiple replicas internally | Cluster replicas | Multiple copies | Managed replication | Managed replication | Replication | Managed | Distributed |
| **Read Replicas** | ✅ | **Aurora Replicas** | ✅ | Not called read replicas | Cache nodes | Replicas | Replicas | Replicas | Replication | N/A | Not the main model |
| **Read Scaling** | Read Replicas | **Up to 15 Aurora Replicas** | Aurora Replicas | **Massive horizontal scale** | Cache scaling | Read replicas | Replicas | Read replicas | Horizontal scaling | N/A | Analytics parallelism |
| **Read Endpoint** | No Aurora-style Reader Endpoint | **Reader Endpoint** | **Reader Endpoint** | API-based access | Cache endpoint | Cluster endpoint | Cluster endpoints | Cluster endpoints | Cassandra endpoints | API/query | SQL endpoint |
| **Writer Endpoint** | DB endpoint | **Writer Endpoint** | **Writer Endpoint** | API | Cache endpoint | Primary/cluster endpoint | Cluster endpoint | Cluster endpoint | Cassandra endpoint | API | SQL endpoint |
| **Read Load Balancing** | Usually application/DNS approach | **Reader Endpoint** | **Reader Endpoint** | Distributed service | Cache cluster | Service-managed | Service-managed | Service-managed | Service-managed | N/A | Query engine |
| **Failover** | Multi-AZ standby | **Very fast Aurora failover** | Aurora failover | Built-in | Replica failover | Automatic failover | Managed | Managed | Managed | Managed | Managed |
| **Multi-AZ** | Optional Multi-AZ | **Native architecture** | Native Aurora | Built-in | Supported | Supported | Supported | Supported | Supported | Managed | Managed |
| **Global / Cross-Region** | Read Replicas / other solutions | **Aurora Global Database** | Aurora capabilities | **Global Tables** | Global datastore options | Multi-Region capabilities | Global replication options | Global databases | Multi-Region replication | Multi-Region writes/read | Cross-Region capabilities |
| **Best for Global Apps** | Possible | ⭐ Excellent | ⭐ Excellent | ⭐ Excellent | ⭐ Excellent for caching | ⭐ Excellent for fast global state | Possible | Possible | Possible | Possible | Analytics |
| **Automatic DB Capacity Scaling** | ❌ | ❌ Standard Aurora | **✅ Core feature** | **✅** | Depending on configuration | Managed scaling | Managed | Managed | Serverless option | **✅** | Managed |
| **Unpredictable Traffic** | Not ideal | Good | ⭐ **Excellent** | ⭐ **Excellent** | ⭐ Excellent for cache | Good | Good | Good | Good | Good | Not primary use |
| **Sudden Traffic Spikes** | May need scaling | Handles well with replicas/scaling | ⭐ **Automatically scales** | ⭐ **Massive scale** | ⭐ Excellent | Excellent | Good | Good | Excellent | Excellent | Designed for analytics |
| **Serverless** | ❌ | Aurora Serverless available | **✅** | **✅** | Serverless options available | Managed but not the same serverless model | Managed | Managed | **Serverless option** | **Serverless** | Serverless option |
| **Performance Focus** | General relational | **High-performance relational** | Variable workload | **Low-latency massive scale** | **Ultra-low-latency cache** | **Ultra-low-latency durable DB** | Document workloads | Graph traversal | High-throughput Cassandra | Time-series ingestion/query | Massive analytics |
| **Latency Goal** | ms | Low ms | Low ms | **Single-digit ms** | **Microseconds** | **Microseconds** | Low ms | Low ms | Low ms | Low ms | Analytics rather than transactional latency |
| **Typical Data** | Customers, orders, payments | Orders, payments, transactions | Same as Aurora | Users, carts, sessions, profiles | Frequently accessed data | Persistent application state | Product/customer documents | People, accounts, relationships | IoT/device events | Sensor readings/metrics | Sales/analytics data |
| **Example** | Banking application | Large e-commerce DB | Ticketing site with unpredictable traffic | Shopping cart | Cache product details | Gaming state | Product catalog | Fraud network | Millions of IoT events | Server metrics | 10-year sales analysis |
| **Data Relationships** | ⭐ Excellent | ⭐ Excellent | ⭐ Excellent | Limited | Limited | Limited | Limited | ⭐ **Excellent** | Limited | Limited | ⭐ Excellent |
| **Flexible Schema** | ❌ | ❌ | ❌ | ⭐ | ⭐ | ⭐ | ⭐ **Excellent** | Graph model | Structured wide-column | Time-series | ❌ |
| **MongoDB Compatible** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ⭐ **Yes** | ❌ | ❌ | ❌ | ❌ |
| **Cassandra Compatible** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ⭐ **Yes** | ❌ | ❌ |
| **Redis Compatible** | ❌ | ❌ | ❌ | ❌ | Redis/Valkey options | ⭐ **Redis-compatible** | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Graph Database** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ⭐ **Yes** | ❌ | ❌ | ❌ |
| **Time-Series Database** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ⭐ **Yes** | ❌ |
| **Data Warehouse** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ⭐ **Yes** |
| **Caching** | ❌ | ❌ | ❌ | ❌ | ⭐ **Primary purpose** | Not primarily a cache | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Durable In-Memory Primary DB** | ❌ | ❌ | ❌ | ❌ | ❌ | ⭐ **Yes** | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Connection Pooling** | RDS Proxy | RDS Proxy | RDS Proxy | Not applicable | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Backup** | Automated backups + snapshots | Continuous backup to S3 + snapshots | Aurora backups | PITR + backups | Cache-oriented | Durable database backups | Automated backups | Backups | Managed backups | Managed | Snapshots |
| **Disaster Recovery** | Multi-AZ / snapshots / replicas | Global Database | Aurora DR options | Global Tables | Replication options | Multi-Region options | Replication | Global options | Replication | Multi-Region options | Snapshots/replication |
| **Best Exam Keyword** | **Traditional SQL** | **High-performance SQL** | **Unpredictable DB load** | **NoSQL / huge scale** | **Cache** | **Durable in-memory** | **MongoDB / documents** | **Relationships** | **Cassandra** | **IoT / timestamps** | **Analytics / OLAP** |
| **Memory Trick** | **Regular SQL DB** | **Super Aurora** | **Aurora scales itself** | **Key → Value at scale** | **Make DB faster** | **Fast DB that remembers** | **Documents** | **Connections between things** | **Cassandra** | **Time → Stream** | **Analyze huge data** |

---

# 🔥 More AWS Database-Related Services

These aren't all standalone database engines, but **you absolutely need them for SAA-C03 database questions**:

| Service | What it does | When you see this in a question | Memory Trick |
| --- | --- | --- | --- |
| **RDS Proxy** | Connection pooling and managed DB connections | Lambda creates **too many connections** to RDS/Aurora | **Proxy = Connections** |
| **Aurora Serverless v2** | Automatically adjusts Aurora compute capacity | **Unpredictable/spiky database workload** | **DB capacity follows demand** |
| **Aurora Global Database** | Cross-Region Aurora replication | **Global app + DR + low-latency regional reads** | **Aurora across Regions** |
| **DynamoDB Global Tables** | Multi-Region DynamoDB replication | **Global NoSQL + active-active** | **DynamoDB everywhere** |
| **DynamoDB Accelerator (DAX)** | In-memory cache specifically for DynamoDB | Need **microsecond reads from DynamoDB** | **DynamoDB + Cache = DAX** |
| **Amazon Redshift Serverless** | Serverless analytics warehouse | Analytics without managing warehouse capacity | **Redshift without servers** |
| **Amazon Neptune Analytics** | Analytics over graph data | Graph relationship analysis | **Analyze relationships** |
| **AWS Database Migration Service (DMS)** | Migrates/replicates databases | **Database migration + minimal downtime + CDC** | **DMS = Database Move Service** |
| **AWS Schema Conversion Tool (SCT)** | Converts schema between different DB engines | **Oracle → Aurora PostgreSQL / SQL Server → MySQL** | **SCT = Schema Conversion** |

---

# 🧠 The SAA-C03 Database "Which One?" Cheat Sheet

This is the part I'd memorize most:

| Question says... | 🚀 Think |
| --- | --- |
| **SQL + MySQL/PostgreSQL/Oracle/SQL Server** | **RDS** |
| **SQL + high performance + high availability** | **Aurora** |
| **Aurora + unpredictable traffic + automatic scaling** | **Aurora Serverless v2** |
| **Aurora + global application + cross-Region** | **Aurora Global Database** |
| **NoSQL + key-value** | **DynamoDB** |
| **NoSQL + massive scale + single-digit ms** | **DynamoDB** |
| **DynamoDB + microsecond reads** | **DAX** |
| **DynamoDB + multi-Region active-active** | **Global Tables** |
| **MongoDB-compatible / JSON documents** | **DocumentDB** |
| **Highly connected data / relationships** | **Neptune** |
| **Cassandra-compatible** | **Keyspaces** |
| **IoT / sensor / metrics / timestamp data** | **Timestream** |
| **Frequently accessed data / reduce DB load** | **ElastiCache** |
| **Redis-compatible + durable primary DB** | **MemoryDB** |
| **Huge analytics / BI / OLAP / data warehouse** | **Redshift** |
| **Lambda + too many database connections** | **RDS Proxy** |
| **Oracle → Aurora PostgreSQL** | **DMS + SCT** |
| **Same engine migration, e.g. PostgreSQL → RDS PostgreSQL** | **DMS** |
| **Database migration with ongoing replication / CDC** | **DMS** |

### 🔑 The ultimate mental map

```
                 DATABASE DECISION
                        │
        ┌───────────────┴────────────────┐
        │                                │
      SQL                              NoSQL
        │                                │
   ┌────┴────┐              ┌────────────┼─────────────┐
   │         │              │            │             │
 RDS      Aurora        Key-Value    Document        Graph
   │         │              │            │             │
Traditional  │          DynamoDB    DocumentDB       Neptune
SQL          │
             ├── Variable load
             │      → Aurora Serverless v2
             │
             └── Global
                    → Aurora Global Database

Other special cases:

Cache                         → ElastiCache
Durable in-memory DB          → MemoryDB
Cassandra                     → Keyspaces
Time-series / IoT             → Timestream
Analytics / Data Warehouse    → Redshift
DynamoDB microsecond cache    → DAX
DB connection pooling         → RDS Proxy
Database migration            → DMS
Different DB engines          → DMS + SCT
```

**One sentence to remember the whole thing:**

> **RDS/Aurora = SQL, DynamoDB = Key-Value, DocumentDB = Documents, Neptune = Relationships, Keyspaces = Cassandra, Timestream = Time, ElastiCache = Cache, MemoryDB = Durable Memory, Redshift = Analytics.**
>
