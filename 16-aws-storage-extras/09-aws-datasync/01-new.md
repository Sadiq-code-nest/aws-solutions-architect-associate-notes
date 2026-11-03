# AWS DataSync — SAA-C03 Exam Notes

## 1. What is AWS DataSync?

**AWS DataSync** is a managed service for **fast, secure, automated data transfer** between:

- On-premises storage ↔ AWS
- AWS storage ↔ AWS storage
- One AWS storage location ↔ another AWS storage location

Think:

> **DataSync = Fast data mover for files and objects**
> 

It is especially useful when you have **large amounts of existing data** that need to be moved or synchronized.

---

## 2. What problem does DataSync solve?

Suppose a company has:

```
On-Premises Data Center
        │
        │  50 TB of files
        ▼
      AWS
```

Manually copying 50 TB could be:

- Slow
- Difficult to manage
- Difficult to monitor
- Error-prone

DataSync handles the transfer for you.

```
On-Premises Storage
        │
        ▼
   DataSync
        │
        ▼
AWS Storage
```

It provides:

- High-speed transfer
- Encryption during transfer
- Automated transfers
- Scheduling
- Monitoring
- Data integrity validation
- Incremental transfers

---

# 3. Main DataSync Use Cases

| Use Case | Example |
| --- | --- |
| **Migration to AWS** | Move 100 TB from on-premises NAS to Amazon S3 |
| **Data transfer to AWS** | Move files from an on-premises file server to EFS |
| **Backup** | Regularly copy on-premises files to S3 |
| **Hybrid storage** | Synchronize on-premises storage with AWS |
| **Data processing** | Move data into S3 before processing with analytics services |
| **AWS-to-AWS transfer** | Transfer data between supported AWS storage locations |
| **Scheduled synchronization** | Copy only changed files every night |
| **Disaster Recovery** | Keep a copy of important files in AWS |

---

# 4. What Storage Systems Can DataSync Work With?

This is an important SAA-C03 concept.

### On-premises / other locations

DataSync can work with:

- NFS file systems
- SMB file systems
- HDFS
- Object storage
- Some other supported storage systems

### AWS destinations/sources

Common AWS storage services include:

- **Amazon S3**
- **Amazon EFS**
- **Amazon FSx**

For example:

```
On-Premises NFS
       │
       ▼
    DataSync
       │
       ▼
Amazon EFS
```

or:

```
On-Premises SMB
       │
       ▼
    DataSync
       │
       ▼
Amazon S3
```

---

# 5. How DataSync Works

The basic architecture is:

```
Source
  │
  ▼
DataSync
  │
  ▼
Destination
```

For on-premises storage, you normally deploy a **DataSync agent**.

```
On-Premises
┌──────────────────┐
│ File Server/NAS  │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ DataSync Agent   │
└────────┬─────────┘
         │
         │ AWS
         ▼
┌──────────────────┐
│ DataSync Service │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Amazon S3 / EFS  │
│ / FSx            │
└──────────────────┘
```

### Important:

**DataSync Agent** is the component that connects your storage environment to AWS DataSync.

---

# 6. DataSync Agent

The **DataSync agent** is used when DataSync needs to access storage outside AWS.

For example:

```
Company NAS
     │
     ▼
DataSync Agent
     │
     ▼
AWS DataSync
     │
     ▼
S3
```

The agent can be deployed in environments such as:

- VMware
- Hyper-V
- Linux/KVM
- AWS
- Other supported virtual environments

---

# 7. DataSync Location

A **DataSync Location** tells DataSync:

> "Where is my source or destination data?"
> 

Examples:

```
Location 1:
On-premises NFS

Location 2:
Amazon S3
```

Then you create a **DataSync Task** between them.

---

# 8. DataSync Task

A **Task** defines the transfer.

Think:

> **Task = instructions for DataSync about what to copy and where to copy it.**
> 

Example:

```
Source:
On-premises NFS

Destination:
S3 bucket

Task:
Copy /company-data → s3://company-backup
```

You can configure things such as:

- What data to transfer
- Destination
- Scheduling
- Verification
- Whether to overwrite files
- Filtering

---

# 9. DataSync Transfer Process

Example:

A company has:

```
500 GB
```

of files on-premises.

They want them in S3.

```
On-Premises
500 GB
   │
   ▼
DataSync Agent
   │
   ▼
DataSync
   │
   ▼
Amazon S3
500 GB
```

DataSync transfers the data and can verify that the destination matches the source.

---

# 10. Incremental Transfer ⭐

One of the most important concepts.

Suppose:

```
Day 1:
1 TB transferred
```

The next day only:

```
20 GB changed
```

DataSync doesn't need to retransmit everything unnecessarily.

```
Day 1
1 TB
│
▼
S3

Day 2
Only changed/new data
20 GB
│
▼
S3
```

This makes scheduled synchronization much more efficient.

---

# 11. Scheduling

DataSync can run transfers:

### Manually

```
Start Task
   ↓
Transfer
```

### On a schedule

For example:

```
Every night at 11 PM
       ↓
    DataSync
       ↓
Copy changed files
```

This is useful for:

- Backups
- Recurring synchronization
- Disaster recovery

---

# 12. DataSync vs AWS Storage Gateway

This is a **very common SAA-C03 confusion**.

|  | **AWS DataSync** | **AWS Storage Gateway** |
| --- | --- | --- |
| Main purpose | **Transfer/synchronize data** | **Provide hybrid cloud storage access** |
| Think | 🚚 Data mover | 🔌 Storage bridge |
| Typical use | Move 100 TB to S3 | On-prem app accesses AWS-backed storage |
| Ongoing access | Not primarily | Yes |
| Migration | ⭐⭐⭐⭐⭐ | Possible, but not the primary purpose |
| Scheduled transfer | Yes | Not its primary function |
| SMB/NFS access | Source/destination support | File Gateway provides SMB/NFS |
| Local cache | No | Yes, depending on gateway |
| Example | NAS → S3 | On-prem application → File Gateway → S3 |

### Easy memory:

> **DataSync = MOVE data**
> 

> **Storage Gateway = ACCESS AWS storage like local storage**
> 

---

# 13. DataSync vs Direct Connect

Another important exam comparison.

|  | **DataSync** | **Direct Connect** |
| --- | --- | --- |
| Purpose | Data transfer | Dedicated network connection |
| Best for | Moving/synchronizing large datasets | Consistent private connectivity |
| One-time migration | ⭐⭐⭐⭐⭐ | Usually not ideal alone |
| Ongoing hybrid connectivity | Not its main purpose | ⭐⭐⭐⭐⭐ |
| Managed transfer mechanism | Yes | No—it's a network connection |
| Physical dedicated connection | No | Yes |
| Automatically moves files | Yes | No |

### Example

Company wants:

> "Move 80 TB of data to AWS as quickly as possible."
> 

Think:

**DataSync**

Company wants:

> "Our data center must have a persistent private network connection to AWS."
> 

Think:

**Direct Connect**

---

# 14. DataSync vs S3 Transfer Acceleration

Very important distinction.

|  | **DataSync** | **S3 Transfer Acceleration** |
| --- | --- | --- |
| Main purpose | Transfer/synchronize data | Accelerate uploads/downloads to S3 |
| Source | On-premises/file/object storage | Client/application |
| Destination | S3, EFS, FSx, etc. | **S3** |
| Uses AWS edge locations | Not the primary concept | ⭐ Yes |
| Best for | Large-scale storage migration | Long-distance S3 transfers |
| Multipart upload | Supported/useful | Can be used with S3 uploads |
| Example | NAS → S3 | Australia client → S3 bucket in Virginia |

### Memory:

**DataSync**

```
Storage → DataSync → AWS Storage
```

**Transfer Acceleration**

```
User → AWS Edge Location → S3
```

---

# 15. DataSync vs Snowball ⭐⭐⭐⭐⭐

This is another major exam topic.

|  | **DataSync** | **AWS Snowball** |
| --- | --- | --- |
| Transfer method | Network | Physical device |
| Internet/network required | Yes | No for bulk physical transfer |
| Best for | Fast online transfer | Very large datasets when network is too slow |
| Physical shipment | No | Yes |
| Example | 10 TB over good network | 500 TB with insufficient bandwidth |
| Recurring synchronization | ⭐⭐⭐⭐⭐ | Not ideal |
| One-time massive migration | Good | ⭐⭐⭐⭐⭐ when network transfer is impractical |

### Easy rule:

> **Good network + need fast online transfer → DataSync**
> 

> **Network too slow + huge dataset → Snowball / Snow family**
> 

---

# 16. DataSync vs Data Transfer Terminal

For very large one-time migrations, AWS also provides **AWS Data Transfer Terminal**.

Conceptually:

```
Huge Dataset
     │
     ▼
AWS Data Transfer Terminal
     │
     ▼
AWS
```

If the question says:

> "80 TB, current bandwidth would take 2 months, need the fastest/cost-effective one-time transfer"
> 

A physical/terminal-based AWS data transfer option can be more appropriate than waiting for network transfer.

---

# 17. DataSync Security

DataSync provides secure transfer capabilities.

Data is:

- Encrypted in transit
- Transferred using secure connections
- Integrated with AWS security controls

You can control access using **IAM**.

For S3, you also need appropriate:

- IAM permissions
- S3 bucket permissions
- KMS permissions if encryption with KMS is involved

---

# 18. Data Integrity

DataSync can perform verification to ensure the transferred data is correct.

Conceptually:

```
Source File
    │
    ▼
Transfer
    │
    ▼
Destination File
    │
    ▼
Verification
```

This is important for migration and backup scenarios where data accuracy matters.

---

# 19. Filtering

You don't necessarily have to transfer everything.

Example:

Company has:

```
/company
 ├── reports
 ├── photos
 ├── videos
 └── temporary
```

They only want:

```
/reports
```

DataSync can use transfer configuration/filtering to control what gets transferred.

---

# 20. DataSync Monitoring

DataSync integrates with AWS monitoring capabilities, including **CloudWatch**.

You can monitor things such as:

- Transfer status
- Bytes transferred
- Files transferred
- Errors
- Transfer duration

Example:

```
DataSync Task
     │
     ▼
CloudWatch
     │
     ├── Bytes transferred
     ├── Files transferred
     └── Errors
```

---

# 21. DataSync Agents vs DataSync Service

Don't confuse these.

### DataSync Agent

Runs in/near your storage environment and provides access to the source/destination storage.

### DataSync Service

The AWS-managed service that coordinates and performs the transfer.

Think:

```
Your environment
      │
      ▼
 DataSync Agent
      │
      ▼
AWS DataSync Service
      │
      ▼
AWS Storage
```

---

# 22. DataSync with S3

Very common architecture:

```
On-Premises NAS
      │
      ▼
DataSync Agent
      │
      ▼
AWS DataSync
      │
      ▼
Amazon S3
```

Then you can use:

- S3 Lifecycle
- S3 Glacier
- S3 Versioning
- S3 Replication
- S3 Object Lock

for further storage management.

### Example

A company has 200 TB of old documents.

```
On-Premises
      │
      │ DataSync
      ▼
S3 Standard
      │
      │ Lifecycle
      ▼
S3 Glacier
      │
      ▼
Long-term archive
```

---

# 23. DataSync with EFS

Suppose a company is migrating a Linux file system to AWS.

```
On-Premises NFS
      │
      ▼
DataSync
      │
      ▼
Amazon EFS
```

This is useful when applications will eventually run on AWS and need a shared file system.

---

# 24. DataSync with FSx

Example:

A Windows environment has SMB-based file storage.

```
On-Premises SMB
      │
      ▼
DataSync
      │
      ▼
Amazon FSx
```

This can be useful during migration of file-based workloads into AWS.

---

# 25. Typical SAA-C03 Scenarios

### Scenario 1 — 50 TB NAS → S3

> Company wants to migrate 50 TB from on-premises NAS to S3 using the existing network.
> 

**Think: DataSync**

---

### Scenario 2 — Nightly backup

> Company wants to copy changed files from its on-premises file server to S3 every night.
> 

**Think: DataSync**

---

### Scenario 3 — On-prem application needs SMB access to AWS

> Application must continue accessing files using SMB while AWS provides the backend storage.
> 

**Think: Storage Gateway File Gateway**, not DataSync.

---

### Scenario 4 — Persistent private AWS connection

> Company needs a dedicated private connection between its data center and AWS for continuous hybrid workloads.
> 

**Think: Direct Connect**

---

### Scenario 5 — 500 TB with extremely slow network

> Moving hundreds of TB over the existing network would take months.
> 

**Think: Snowball / appropriate physical data transfer solution.**

---

### Scenario 6 — User far from S3 bucket

> A global application uploads objects to S3 and needs faster uploads over long geographic distances.
> 

**Think: S3 Transfer Acceleration.**

---

# 26. Exam Keyword Table ⭐⭐⭐⭐⭐

| Question wording | Think |
| --- | --- |
| **Transfer large amounts of data** | **DataSync** |
| **Migrate on-premises data to AWS** | **DataSync** |
| **Synchronize on-premises storage with AWS** | **DataSync** |
| **Schedule recurring transfers** | **DataSync** |
| **NFS → S3** | **DataSync** |
| **SMB → S3** | **DataSync** |
| **On-premises → EFS** | **DataSync** |
| **On-premises → FSx** | **DataSync** |
| **Existing data needs to be moved quickly** | **DataSync** |
| **Network is too slow for huge one-time migration** | **Snowball / physical transfer option** |
| **Dedicated private network to AWS** | **Direct Connect** |
| **AWS-backed SMB/NFS storage for on-prem apps** | **Storage Gateway** |
| **Faster S3 upload using AWS edge locations** | **S3 Transfer Acceleration** |
| **Recurring file synchronization** | **DataSync** |
| **Transfer between AWS storage locations** | **DataSync** |

---

# 🧠 Final SAA-C03 Memory

Think of the AWS data-transfer family like this:

```
                DATA TRANSFER
                     │
       ┌─────────────┼─────────────┐
       │             │             │
       ▼             ▼             ▼
   DataSync      Snowball      Direct Connect
       │             │             │
       ▼             ▼             ▼
   MOVE DATA     PHYSICAL      NETWORK
   ONLINE        TRANSFER      CONNECTION
```

And:

```
DataSync
   ↓
"Move/sync my storage data"

Storage Gateway
   ↓
"Give my on-prem application AWS-backed storage"

Direct Connect
   ↓
"Give me a dedicated private connection to AWS"

S3 Transfer Acceleration
   ↓
"Make my long-distance S3 transfer faster"

Snowball
   ↓
"My network is too slow for this huge dataset"
```

### ⭐ One-line definition

> **AWS DataSync is a managed, high-speed data transfer and synchronization service used primarily to move large amounts of data between on-premises storage and AWS storage services such as S3, EFS, and FSx, with support for automation, scheduling, monitoring, and data verification.**
>
