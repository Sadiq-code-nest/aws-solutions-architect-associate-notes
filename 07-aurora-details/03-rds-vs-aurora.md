# RDS vs Aurora

For AWS SAA, this is the **high-value comparison table** you should memorise.

# RDS vs Aurora (Exam Cheat Sheet)

| Feature | RDS | Aurora |
| --- | --- | --- |
| Database Type | Managed relational DB service | AWS-built cloud-native relational DB |
| Engines | MySQL, PostgreSQL, MariaDB, Oracle, SQL Server | MySQL-compatible, PostgreSQL-compatible |
| Performance | Standard | Up to several times faster than standard MySQL/PostgreSQL |
| Storage Architecture | Storage attached to DB instance | Shared distributed storage |
| Storage Copies | Depends on setup | 6 copies across 3 AZs |
| Storage Scaling | Manual/limited resizing | Auto grows up to 128 TB |
| Read Replication | Asynchronous | Shared storage, very low lag |
| Read Replicas | Up to a limited number depending on engine | Up to 15 Aurora Replicas |
| Replica Lag | Can be noticeable | Typically much lower |
| Failover Speed | Slower | Faster |
| High Availability | Multi-AZ option | Built-in architecture |
| Multi-AZ | Separate standby instance | Native design across 3 AZs |
| Read Scaling | Read Replicas | Reader Endpoint + Aurora Replicas |
| Load Balancing Reads | Application handles it | Reader Endpoint handles it automatically |
| Writer Endpoint | No special endpoint concept | Dedicated Writer Endpoint |
| Reader Endpoint | No | Yes |
| Auto Scaling Replicas | No | Yes (Aurora Auto Scaling) |
| Serverless Option | No native RDS serverless | Aurora Serverless |
| Global Database | Limited approaches | Aurora Global Database |
| Backup | Automated backups | Continuous backups to S3 |
| Recovery Time | Good | Faster |
| Cost | Lower | Higher |
| Best Use Case | General applications | Mission-critical, high-performance applications |

---

# Quick Exam Decision Tree

| Requirement in Question | Answer |
| --- | --- |
| Cheapest managed relational database | RDS |
| Need MySQL/PostgreSQL but much faster | Aurora |
| Need automatic read load balancing | Aurora Reader Endpoint |
| Need up to 15 read replicas | Aurora |
| Need global low-latency reads | Aurora Global Database |
| Need serverless relational database | Aurora Serverless |
| Need highest availability | Aurora |
| Need fastest failover | Aurora |
| Need simple database at lower cost | RDS |
| Need read scaling only | RDS Read Replica or Aurora Replicas |
| Need disaster recovery within region | RDS Multi-AZ or Aurora |

---

# The Most Tested Exam Keywords

### If you see:

```
6 copies across 3 AZs
```

👉 Aurora

---

```
Shared Storage Volume
```

👉 Aurora

---

```
Reader Endpoint
```

👉 Aurora

---

```
Writer Endpoint
```

👉 Aurora

---

```
Aurora Auto Scaling
```

👉 Aurora

---

```
Global Database
```

👉 Aurora

---

```
Serverless Relational Database
```

👉 Aurora Serverless

---

```
Standby Instance
```

👉 RDS Multi-AZ

---

```
Asynchronous Read Replica
```

👉 RDS Read Replica

---

# 30-Second Memory Trick

| Think... | Service |
| --- | --- |
| Cheap & simple | RDS |
| Fast & scalable | Aurora |
| DR / Failover | Multi-AZ |
| Read Scaling | Read Replica |
| Global Reads | Aurora Global Database |
| Automatic Read Load Balancing | Aurora Reader Endpoint |
| Serverless Database | Aurora Serverless |

**One-line summary:**

**RDS = Traditional managed database.**

**Aurora = AWS redesigned database with shared storage, faster failover, better scaling, reader**
