# Question

#### **2. Question**

**Category: CSAA – Design High-Performing Architectures**

A Solutions Architect is implementing a new High-Performance Computing (HPC) system in AWS that involves orchestrating several Amazon Elastic Container Service (Amazon ECS) tasks with an Amazon EC2 launch type that is part of an ECS cluster. The ECS cluster is configured with an ECS Capacity Provider to dynamically manage and scale the EC2 instances based on ECS task demand, ensuring sufficient compute resources are always available for high-throughput workloads.

Users around the globe will frequently access the system, and it is expected that hundreds of ECS tasks will run most of the time. These tasks require high-frequency read and write operations to a shared storage system. The output data of each ECS task is around 10 MB, and although obsolete data is eventually archived and deleted, the total storage size will not exceed 10 TB.

Which of the following is the MOST suitable solution that the Architect should recommend?

- Launch an Amazon DynamoDB table with DynamoDB Accelerator (DAX) and DynamoDB Streams enabled. Configure the table to be accessible by all ECS cluster instances. Set the DynamoDB table as the container mount point in the ECS task definition of the ECS cluster.
- Launch an Amazon Elastic File System (Amazon EFS) file system with Bursting Throughput mode and set the performance mode to `General Purpose`. Configure the EFS file system as the container mount point in the ECS task definition of the ECS cluster.
- Set up an SMB file share by creating an Amazon FSx for Windows File Server. Set the file share as the container mount point in the ECS task definition of the ECS cluster.
- Launch an Amazon Elastic File System (Amazon EFS) with Provisioned Throughput mode and set the performance mode to `Max I/O`. Configure the EFS file system as the container mount point in the ECS task definition of the ECS cluster.
