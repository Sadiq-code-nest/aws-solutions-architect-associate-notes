# Amazon DocumentDB — AWS SAA-C03 Exam Notes

## 1. What is Amazon DocumentDB? (Simple understanding)

Amazon DocumentDB is a fully managed, MongoDB-compatible document database service on AWS.

Think of it as a database for storing information in flexible, JSON-like documents instead of traditional rows and columns.

Example: Online shopping application

A product can have different attributes without forcing every product into the same table structure.

```
{
  "product": "Laptop",
  "price": 1200,
  "features": ["16GB RAM", "512GB SSD"],
  "warranty": {
    "years": 2
  }
}
```

Documents can contain nested objects and arrays, making them useful for flexible application data.

### Remember the relationship

- Amazon Aurora → Relational database compatible with MySQL and PostgreSQL.
- Amazon DocumentDB → Document database compatible with MongoDB workloads.
- Amazon DynamoDB → Serverless key-value and document NoSQL database.

Important exam nuance: DocumentDB is MongoDB-compatible, not the MongoDB product itself. Compatibility is not necessarily identical across every MongoDB feature or version.

## 2. Architecture — how it works

Application

Uses a MongoDB-compatible driver

Amazon DocumentDB Cluster

Cluster endpoint and database instances

Writer

Handles writes

Replicas

Read scaling and failover support

Shared cluster storage

Storage grows automatically as data increases

Basic flow: The application connects through a compatible driver, sends database queries, and reads or writes documents. Replicas help scale reads and provide failover capacity.

## 3. Core features to remember

| Feature | Exam-relevant fact |
| --- | --- |
| Fully managed | AWS manages infrastructure, patching, backups, and monitoring |
| MongoDB compatibility | Supports MongoDB-compatible drivers and APIs; not identical to every MongoDB feature |
| Flexible documents | Stores nested JSON-like data, arrays, and objects |
| High availability | Data is replicated across 3 AZs; replicas can support failover |
| Read scaling | Add up to 15 read replicas for instance-based clusters |
| Automatic storage growth | Storage grows in 10 GiB increments |
| Massive scale | Elastic Clusters support workloads with millions of reads and writes per second |
| Backups | Automated backups and point-in-time recovery |
| Security | Runs in a VPC; supports encryption, TLS, IAM integration, and monitoring |
| Migration | AWS Database Migration Service (DMS) can migrate MongoDB workloads |

These are the main service characteristics to recognize. Exact limits can vary by cluster type and engine version.

Amazon Web Services, Inc.

+2

## 4. Which database should you choose?

| Requirement in the question | Best fit |
| --- | --- |
| Existing MongoDB application; flexible document data | DocumentDB |
| SQL, joins, relationships; MySQL/PostgreSQL compatibility | Aurora |
| Serverless key-value/document access at massive scale | DynamoDB |
| Managed relational database using standard database engines | Amazon RDS |
| Data warehouse for large-scale analytical SQL | Amazon Redshift |
| Search engine for full-text search and log analytics | Amazon OpenSearch Service |

Main confusion-buster: DocumentDB is a strong choice when MongoDB compatibility matters. DynamoDB is usually the stronger fit for serverless, key-value access patterns at very large scale.

AWS

+1

## 5. Exam trigger phrases + scenarios

| If the question says… | Think… |
| --- | --- |
| “Migrate an existing MongoDB application to AWS” | DocumentDB; consider AWS DMS |
| “Flexible JSON documents with nested fields” | DocumentDB |
| “Read-heavy MongoDB workload” | Add DocumentDB read replicas |
| “Database must tolerate an instance failure” | Multi-AZ replication and replica failover |
| “Storage grows unpredictably” | DocumentDB automatic storage scaling |
| “Millions of reads and writes per second” | Consider DocumentDB Elastic Clusters |
| “Reduce database administration” | Fully managed DocumentDB |
| “Protect data at rest and in transit” | KMS encryption and TLS |
| “Recover to a specific point in time” | Automated backups and point-in-time recovery |
| “Global users need low-latency reads” | Consider DocumentDB Global Clusters |

Amazon Web Services, Inc.

+2

## 6. Important exam traps

- Read replicas are not extra independent databases. They share the cluster storage and can support read scaling and failover.
- Automatic storage scaling does not mean unlimited compute scaling. Instance-based clusters still require compute sizing; replicas help scale reads.
- DocumentDB is MongoDB-compatible, not MongoDB itself. Check compatibility requirements if a question mentions a specific MongoDB feature.
- Three AZs provide resilience, not automatic global disaster recovery. For cross-Region needs, consider Global Clusters.
- DMS migrates data; it is not the database engine.
- Millions of requests per second is associated with Elastic Clusters, not a promise that every DocumentDB configuration reaches that throughput.

## 7. Memory hooks

DOC

DocumentDB = Documents

Flexible, nested JSON-like data; MongoDB-compatible applications.

3 AZ

Three = resilience

Multi-AZ storage replication helps protect against failures.

15

Replicas = read scaling

Instance-based clusters support up to 15 read replicas.

10 GiB

Storage grows automatically

No need to pre-provision future storage capacity.

## 8. Final 30-second revision sheet

- What? Fully managed, MongoDB-compatible document database.
- Data model? Flexible JSON-like documents.
- Availability? Replicated across 3 AZs.
- Read performance? Up to 15 read replicas for instance-based clusters.
- Storage? Automatic growth in 10 GiB increments.
- Extreme scale? Elastic Clusters can support millions of reads and writes per second.
- Migration? AWS DMS.
- Security? VPC, KMS encryption, TLS, IAM integration.
- Recovery? Automated backups and point-in-time recovery.
- Aurora vs DocumentDB vs DynamoDB? Relational SQL vs MongoDB-compatible documents vs serverless key-value/document access.

One-line memory trick: MongoDB app → DocumentDB; SQL and joins → Aurora; serverless key-value at massive scale → DynamoDB.

For current technical details, use the official [Amazon DocumentDB overview](https://docs.aws.amazon.com/documentdb/latest/devguide/what-is.html?utm_source=chatgpt.com).

mongoDB.
