# PART 2 — AWS DataSync

**AWS DataSync = Systems automatically copy large amounts of data between storage locations.**

DataSync is NOT an FTP server.

It copies storage.

Think

"Storage Migration."

---

## Example

Company has

```
500 TB

NAS Storage
```

Need to move

everything

to AWS.

Uploading manually

takes forever.

Use

DataSync.

---

# Architecture

```
On-Premises NAS

      │

 DataSync Agent

      │

Encrypted Transfer

      │

AWS DataSync

      │

Amazon S3
```

---

# Why Agent?

AWS

cannot directly access

your local storage.

Agent

reads files

and sends them.

---

# Agent Needed?

| Source | Agent? |
| --- | --- |
| On-Premises | ✅ Yes |
| Other Cloud | ✅ Yes |
| AWS → AWS | ❌ No |

---

## Example

Copy

```
Local NAS

↓

Amazon EFS
```

Need

Agent.

---

## Example

Copy

```
S3

↓

FSx
```

No Agent.

Both are already inside AWS.

---

# What Can DataSync Copy?

It supports

```
NFS

SMB

HDFS

S3

Amazon EFS

Amazon FSx

Amazon S3

Other Clouds
```

Almost every storage.

---

# Destinations

Can copy into

- Amazon S3
- Amazon EFS
- FSx Windows
- FSx Lustre
- FSx NetApp
- FSx OpenZFS

---

# Scheduling

Tasks can run

```
Hourly

Daily

Weekly
```

Example

Every night

copy

new files

to S3.

---

# Metadata

DataSync

keeps

permissions.

Example

Original File

```
Owner

Permissions

Timestamp
```

After copying

everything remains the same.

Huge exam point.

---

# Speed

One agent

can transfer

up to

10 Gbps.

Very fast.

---

# Example

Hospital

200 TB

Medical Images.

Need migration.

↓

Install

DataSync Agent.

↓

Copy

to S3.

↓

Permissions preserved.

---

# Architecture

```
Hospital NAS

      │

DataSync Agent

      │

AWS DataSync

      │

Amazon S3
```

---

# Example 2

Move

```
Amazon EFS

↓

Amazon FSx
```

Both AWS.

No agent.

---

# Example 3

Daily Backup

```
On-Premises

↓

S3 Glacier
```

Schedule

Every Midnight.

---

# AWS Transfer Family vs DataSync

This is one of the favorite exam questions.

| Feature | AWS Transfer Family | AWS DataSync |
| --- | --- | --- |
| Purpose | File Transfer Service | Data Migration/Synchronization |
| Who Uses It? | Humans/Partners | Systems/Administrators |
| Main Job | Upload & Download Files | Copy Storage |
| Uses FTP? | ✅ Yes | ❌ No |
| Uses SFTP? | ✅ Yes | ❌ No |
| Uses FTPS? | ✅ Yes | ❌ No |
| Supports S3 | ✅ | ✅ |
| Supports EFS | ✅ | ✅ |
| Supports FSx | ❌ | ✅ |
| Scheduling | ❌ | ✅ Hourly/Daily/Weekly |
| Preserves Metadata | Basic | ✅ Full metadata & permissions |
| Needs Agent | ❌ | Only for On-Premises/Other Cloud |
| Multi-AZ Managed | ✅ | ✅ |

---

# Decision Tree

```
Need FTP?

Need SFTP?

Need FTPS?

        ↓

AWS Transfer Family

-------------------------

Need migration?

Need synchronization?

Need scheduled copy?

Need copy 100 TB?

        ↓

AWS DataSync
```

---

# Real Exam Scenarios

## Scenario 1

**A company wants business partners to upload invoices using SFTP directly into Amazon S3.**

✅ **Answer:** AWS Transfer Family

**Why?** It provides an SFTP endpoint that stores files in S3.

---

## Scenario 2

**An organization needs to migrate 500 TB of data from an on-premises NAS to Amazon S3 while preserving permissions and metadata.**

✅ **Answer:** AWS DataSync

**Why?** It's built for high-speed storage migration and metadata preservation.

---

## Scenario 3

**A company wants to copy new files from Amazon EFS to Amazon FSx every night automatically.**

✅ **Answer:** AWS DataSync

**Why?** It supports scheduled synchronization between AWS storage services.

---

## Scenario 4

**A customer insists on using FileZilla with SFTP to upload files into AWS without changing their workflow.**

✅ **Answer:** AWS Transfer Family

**Why?** It exposes an SFTP server backed by S3 or EFS.

---

## Scenario 5

**An enterprise needs to move data from an on-premises SMB file server to Amazon FSx for Windows File Server.**

✅ **Answer:** AWS DataSync

**Why?** It supports SMB as a source and FSx as a destination while preserving ACLs and permissions.

---

# Transfer Family vs Storage Gateway vs DataSync

Another common SAA-C03 comparison:

| Service | Think of It As | Best Use Case |
| --- | --- | --- |
| **AWS Transfer Family** | An FTP/SFTP server in AWS | External users or partners upload/download files to S3/EFS |
| **AWS Storage Gateway** | A bridge between on-premises applications and AWS storage | Hybrid storage, local caching, backups, disaster recovery |
| **AWS DataSync** | A high-speed moving truck | Bulk migration and scheduled synchronization of storage |

---

# 🎯 30-Second Memory Trick

Imagine a company:

- 👤 **A supplier uploads files using SFTP every day** → **AWS Transfer Family**
- 🚚 **The IT team migrates 300 TB from a local NAS to S3** → **AWS DataSync**
- 🌉 **The office wants to keep local file access while storing data in AWS** → **AWS Storage Gateway**

### Final Exam Shortcut

```
Need FTP / SFTP / FTPS?
        ↓
AWS Transfer Family

Need Hybrid Storage?
        ↓
Storage Gateway

Need Bulk Data Migration or Scheduled Sync?
        ↓
AWS DataSync
```

This simple decision flow is enough to correctly identify these three services in almost every AWS SAA-C03 exam scenario.
