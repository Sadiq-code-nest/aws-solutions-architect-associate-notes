# Elasticache

Amazon ElastiCache - Summary
. Managed Redis / Memcached (similar offering as RDS, but for caches)
. In-memory data store, sub-millisecond latency
· Select an ElastiCache instance type (e.g., cache.m6g.large)
. Support for Clustering (Redis) and Multi AZ, Read Replicas (sharding)
. Security through IAM, Security Groups, KMS, Redis Auth
. Backup / Snapshot / Point in time restore feature
. Managed and Scheduled maintenance
. Requires some application code changes to be leveraged

. Use Case: Key/Value store, Frequent reads, less writes, cache results for DB
queries, store session data for websites, cannot use SQL.
