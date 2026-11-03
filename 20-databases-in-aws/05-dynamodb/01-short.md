# Short

Amazon DynamoDB - Summary

. AWS proprietary technology, managed serverless NoSQL database, millisecond latency
. Capacity modes: provisioned capacity with optional auto-scaling or on-demand capacity
· Can replace ElastiCache as a key/value store (storing session data for example, using TTL feature)
. Highly Available, Multi AZ by default, Read and Writes are decoupled, transaction capability
. DAX cluster for read cache, microsecond read latency
. Security, authentication and authorization is done through IAM
. Event Processing: DynamoDB Streams to integrate with AWS Lambda, or Kinesis Data Streams
· Global Table feature: active-active setup
. Automated backups up to 35 days with PITR (restore to new table), or on-demand backups
. Export to S3 without using RCU within the PITR window, import from S3 without using WCU
. Great to rapidly evolve schemas

· Use Case: Serverless applications development (small documents 100s KB), distributed serverless cache
