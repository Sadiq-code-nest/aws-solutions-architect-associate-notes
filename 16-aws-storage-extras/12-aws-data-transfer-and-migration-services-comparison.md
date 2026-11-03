# AWS Data Transfer & Migration Services Comparison

If you're preparing for AWS certification or a DevOps interview, it's important to understand where **AWS DataSync** fits among AWS data migration and transfer services.

# AWS Data Transfer & Migration Services Comparison

| Service | Main Purpose | Source → Destination | Transfer Method | Best For |
| --- | --- | --- | --- | --- |
| AWS DataSync | Online data migration | On-prem ↔ AWS, AWS ↔ AWS | Agent-based | Large file transfers |
| AWS Transfer Family | FTP/SFTP access | Users ↔ S3/EFS | FTP/SFTP/FTPS | Legacy applications |
| AWS Storage Gateway | Hybrid storage | On-prem ↔ AWS | Cached storage | Hybrid environments |
| AWS Snowball Edge | Offline migration | On-prem → AWS | Physical device | Petabytes, slow internet |
| AWS Snowmobile | Massive offline migration | Data Center → AWS | Truck | Exabyte migrations |
| AWS Database Migration Service (AWS DMS) | Database migration | DB → DB | Continuous replication | Database migrations |
| AWS Application Migration Service (MGN) | Server migration | Physical/VM → EC2 | Block replication | Lift-and-shift migration |
| AWS Backup | Backup management | AWS Resources | Scheduled backups | Disaster recovery |
| **AWS Data Transfer Terminal** | Offline migration | On-prem ↔ AWS | You bring to AWS |  slow internet |

---

# 1. AWS DataSync

## Purpose

Move **files** quickly and securely.

### Supports

- On-prem NAS
- NFS
- SMB
- HDFS
- Object Storage
- Amazon S3
- Amazon EFS
- Amazon FSx
- AWS ↔ AWS transfers

### Features

- Incremental sync
- Compression
- Encryption
- Verification
- Scheduling
- Bandwidth throttling
- Parallel transfers
- Preserves permissions, timestamps, ownership

### Example

Company has **100 TB** of files on a Windows file server.

Need to move to Amazon EFS.

✅ Use **DataSync**

---

# 2. AWS Transfer Family

## Purpose

Provides **FTP, FTPS, SFTP** endpoints.

Instead of migrating data, it lets users upload/download files using traditional protocols.

### Example

A bank has customers uploading files via SFTP.

Instead of managing Linux FTP servers:

Customer

↓

SFTP

↓

Transfer Family

↓

Amazon S3

---

## Difference

DataSync moves data.

Transfer Family lets users access AWS storage using FTP/SFTP.

---

# 3. AWS Storage Gateway

Purpose:

Hybrid storage.

Acts like local storage but stores data in AWS.

### Types

- File Gateway
- Volume Gateway
- Tape Gateway

### Example

Hospital stores medical images.

Doctors access files locally.

Gateway uploads data automatically to S3.

---

Difference

Storage Gateway = Continuous hybrid storage.

DataSync = One-time or scheduled migration.

---

# 4. AWS Snowball Edge

Purpose

Transfer huge datasets physically.

Instead of Internet:

AWS sends appliance

↓

Copy data

↓

Ship back

↓

AWS uploads to S3

---

When?

- 100 TB
- 500 TB
- Poor Internet

---

Difference

| DataSync | Snowball |
| --- | --- |
| Online | Offline |
| Internet | Physical appliance |
| Continuous | One-time |
| Good network required | No network required |

---

# 5. AWS Snowmobile

Purpose

Transfer Exabytes.

Literally a truck.

Example

Netflix

Government

Satellite company

Need 50 PB+

AWS sends truck.

Very rare.

---

# 6. AWS Database Migration Service (DMS)

Purpose

Move databases.

Supports

- Oracle
- SQL Server
- PostgreSQL
- MySQL
- Aurora
- DynamoDB

Example

MySQL

↓

Aurora MySQL

Minimal downtime.

Difference

DataSync = Files

DMS = Databases

---

# 7. AWS Application Migration Service (MGN)

Purpose

Move entire servers.

Migrates

- OS
- Applications
- Settings
- Disks

Example

VMware

↓

Amazon EC2

Almost no redesign.

Difference

DataSync copies files only.

MGN copies entire machines.

---

# 8. AWS Backup

Purpose

Centralized backup.

Supports

- EC2
- EFS
- RDS
- DynamoDB
- EBS
- FSx
- Storage Gateway

Difference

Backup protects data.

DataSync migrates data.

---

## 9. Data Transfer Terminal

- Images
    
    
    
    
    
    

A **Data Transfer Terminal** is a secure AWS facility where you can physically bring your own storage devices and transfer data to AWS.

Think:

```
Your Data Center
      │
      │ Put data on your storage device
      ▼
Your storage device
      │
      │ physically transport
      ▼
AWS Data Transfer Terminal
      │
      ▼
AWS
```

### When would you use it?

Suppose you have:

```
500 TB of data
```

You already have your own storage hardware and don't want to wait for a network transfer.

You physically take the storage device to an AWS Data Transfer Terminal.

**Key idea:**

> **You bring the storage to AWS.**
> 

# Simple Memory Trick

| Need | Use |
| --- | --- |
| Move files | **DataSync** |
| FTP/SFTP server | **Transfer Family** |
| Hybrid storage | **Storage Gateway** |
| No internet | **Snowball** |
| Huge offline migration | **Snowmobile** |
| Move databases | **DMS** |
| Move servers | **MGN** |
| Backup AWS resources | **AWS Backup** |
| **You bring the** Huge  **storage to AWS.** | AWS Data Transfer Terminal |

---

# Decision Flow

```
Need to move data?
│
├── Files?
│     │
│     ├── Good Internet?
│     │      └── DataSync
│     │
│     └── Poor Internet?
│            ├── < 80 PB → Snowball Edge
│            |── Exabytes → Snowmobile
|            |-- Huge (80TB), but your bring to aws ->AWS Data Transfer Terminal
│
├── Database?
│      └── DMS
│
├── Entire Server?
│      └── MGN
│
├── Continuous Hybrid Storage?
│      └── Storage Gateway
│
├── Need FTP/SFTP access?
│      └── Transfer Family
│
└── Need Backup?
       └── AWS Backup
```

# AWS Exam Tips (Very Common)

| Scenario | Correct Service |
| --- | --- |
| Migrate millions of files from an on-premises NAS to Amazon EFS | **DataSync** |
| Customers upload files via SFTP into Amazon S3 | **Transfer Family** |
| Keep local file shares while storing data in AWS | **Storage Gateway (File Gateway)** |
| Transfer 500 TB with limited internet bandwidth | **Snowball Edge** |
| Transfer multiple petabytes/exabytes | **Snowmobile** |
| Migrate an Oracle database to Amazon Aurora with minimal downtime | **DMS** |
| Lift-and-shift a VMware server to Amazon EC2 | **MGN** |
| Centrally manage backups for EBS, RDS, and EFS | **AWS Backup** |

## Quick Rule to Remember

- **DataSync** = **Files**
- **Transfer Family** = **FTP/SFTP**
- **Storage Gateway** = **Hybrid Storage**
- **Snowball/Snowmobile** = **Offline Data Transfer**
- **DMS** = **Databases**
- **MGN** = **Servers**
- **AWS Backup** = **Backups & Recovery**

This mapping is one of the most frequently tested topics in AWS Solutions Architect and SysOps certification exams.
