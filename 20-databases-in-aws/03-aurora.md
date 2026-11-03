# Aurora

Amazon Aurora - Summary

. Compatible API for PostgreSQL / MySQL, separation of storage and compute
· Storage: data is stored in 6 replicas, across 3 AZ - highly available, self-healing, auto-scaling
. Compute: Cluster of DB Instance across multiple AZ, auto-scaling of Read Replicas
. Cluster: Custom endpoints for writer and reader DB instances
. Same security / monitoring / maintenance features as RDS
. Know the backup & restore options for Aurora
· Aurora Serverless - for unpredictable / intermittent workloads, no capacity planning
. Aurora Global: up to 16 DB Read Instances in each region, < I second storage replication
· Aurora Machine Learning: perform ML using SageMaker & Comprehend on Aurora
. Aurora Database Cloning: new cluster from existing one, faster than restoring a snapshot
. Use case: same as RDS, but with less maintenance / more flexibility / more performance / more features
