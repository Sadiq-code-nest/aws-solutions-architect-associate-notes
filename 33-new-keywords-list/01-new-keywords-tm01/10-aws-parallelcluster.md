# AWS ParallelCluster

## What is AWS ParallelCluster?

AWS ParallelCluster is used to create and manage **HPC — High Performance Computing clusters** on AWS.

For example:

```
Research workload
       │
       ▼
ParallelCluster
       │
 ┌─────┼─────┐
 │     │     │
EC2   EC2   EC2
 │     │     │
 └─────┼─────┘
       │
   HPC workload
```

Typical use cases:

- Scientific computing
- Engineering simulations
- Research
- Computational workloads
- Batch/HPC workloads

It has **nothing to do with consolidating AWS accounts**.

So:

> **ParallelCluster → HPC**
> 

not:

> **ParallelCluster → AWS account management**
>
