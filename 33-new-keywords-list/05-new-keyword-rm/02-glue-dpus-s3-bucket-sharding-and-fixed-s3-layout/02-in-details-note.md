# AWS SAA-C03 Notes: Glue DPUs, S3 Bucket Sharding & Fixed S3 Layout

These three concepts relate to cost efficiency, processing performance, and organizing data in AWS analytics workloads.

## 1. AWS Glue Data Processing Unit (DPU)

Simple meaning: A DPU is a unit of processing capacity used by AWS Glue to run data integration and ETL jobs.

AWS Glue ETL jobs use compute resources to read, transform, and write data. The number of DPUs (or the worker configuration, depending on the job type) affects available processing capacity and cost.

Example: CSV → Parquet

CSV files in Amazon S3

AWS Glue ETL job

DPUs / worker capacity perform the transformation

Parquet files in S3 → queried by Athena

### When should you use more or less processing capacity?

| Dataset / workload | Approach |
| --- | --- |
| Small datasets | Avoid allocating excessive compute |
| Large datasets | Provide enough workers/capacity for the workload |
| Highly variable dataset sizes | Use appropriate scaling or capacity configuration rather than blindly fixing one size |
| Repeated incremental processing | Consider job bookmarks as well |

Exam trap: A static number of DPUs for every job may waste money on small datasets or provide insufficient capacity for large datasets.

Memory trick: DPU = Glue processing power. More capacity can help, but the right amount depends on the workload.

## 2. S3 Bucket Sharding

Simple meaning: Bucket sharding means splitting data across multiple S3 buckets instead of storing everything in one bucket, typically to distribute workloads or separate datasets.

Example:

One analytics application

Bucket A

Small datasets

Bucket B

Large datasets

### When might you use it?

- Separating data by workload, tenant, environment, or access policy.
- Keeping different datasets independently managed.
- Designing around specific operational requirements or service quotas.

### Important SAA-C03 clarification

Bucket sharding is not normally required to improve S3 request performance. Amazon S3 automatically scales to high request rates. If request patterns create performance issues, object-key design and workload patterns may matter, but creating extra buckets simply because a dataset is small is not a general best practice.

Exam trap: Do not assume that small datasets must be stored in separate buckets. Bucket separation should have a clear architectural reason.

Memory trick: Sharding = split across buckets.

## 3. Fixed-layout S3 bucket structure

Simple meaning: A fixed-layout structure means following a consistent folder-like object-key naming convention for data, regardless of dataset size.

S3 is an object store, not a traditional filesystem. Its “folders” are generally prefixes in object keys.

Example:

```
s3://company-analytics/
    raw/
        sales/
        customers/
    processed/
        sales/
        customers/
    curated/
        sales/
        customers/
```

A more specific layout might include dates:

```
s3://company-analytics/processed/sales/year=2026/month=10/day=18/
```

### Why use a consistent layout?

- Makes data easier to discover and manage.
- Supports predictable ETL input and output paths.
- Helps Athena query partitioned datasets efficiently when partitioning is designed appropriately.
- Simplifies lifecycle policies and access controls when data categories need different treatment.

### When should you use it?

Use a consistent layout when you have many datasets, multiple processing stages, repeatable ETL jobs, or Athena queries that benefit from partitioned data.

Important: A fixed layout does not mean every dataset must have identical numbers of files or partitions. It means the naming and organization convention remains consistent.

Memory trick: Fixed layout = same organizational rules, not same dataset size.

## Quick comparison for the exam

| Concept | Main purpose | Typical scenario |
| --- | --- | --- |
| Glue DPUs | Supply ETL compute capacity | CSV-to-Parquet transformations with varying workloads |
| S3 bucket sharding | Separate/distribute data across buckets | Workload, tenant, policy, or operational separation |
| Fixed S3 layout | Standardize object-key organization | Many datasets processed and queried consistently |

## Apply it to your reference question

The question emphasizes datasets ranging from a few hundred to several million rows and asks for maximum efficiency and flexibility.

- Static DPUs for every transformation: Not ideal because small and large jobs have different capacity needs.
- S3 Transfer Acceleration: Intended to accelerate eligible transfers over long distances; it does not optimize the ETL transformation itself.
- Glue job bookmarks: Track previously processed data to help avoid reprocessing old data in supported incremental ETL workflows.
- Bucket sharding for small datasets: Not automatically necessary.
- Fixed S3 layout for all dataset sizes: A consistent structure can simplify ETL and analytics management.

The key distinction is that compute sizing handles workload variability, while a fixed layout handles organizational consistency. Job bookmarks solve a different problem: incremental processing.

One caution: based on the wording alone, fixed layout is clearly a reasonable choice, but the question’s exact intended second answer depends on its source’s assumptions about job bookmarks and dataset processing. Bookmarks are most valuable when jobs run incrementally over changing data; they do not automatically improve every full-batch transformation.
