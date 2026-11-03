# 3 (Updated with Q53)

Absolutely. For **AWS SAA-C03**, the easiest way to master Storage Gateway is to remember one question:

> **“What storage protocol/environment does the company already use on-premises, and where should the actual data live?”**
> 

## AWS Storage Gateway — Master Table

| Feature | **File Gateway** | **Volume Gateway** | **Tape Gateway** |
| --- | --- | --- | --- |
| **Main purpose** | On-premises **file storage** backed by S3 | On-premises **block storage** backed by AWS | Replace **physical tape backup** with AWS |
| Think of it as | **File server → S3** | **Disk → AWS** | **Tape library → AWS** |
| Protocol | **SMB / NFS** | **iSCSI** | **iSCSI Virtual Tape Library (VTL)** |
| Data ultimately stored in | **Amazon S3** | **Amazon S3** via snapshots | **S3 / S3 Glacier** depending on tier |
| On-premises users see | Files/folders | Block volumes | Virtual tapes |
| Best for | Corporate documents, shared files | Applications requiring block storage | Backup applications using tape |
| Local cache | ✅ Yes | ✅ Yes | ✅ Yes |
| Low-latency access to frequently used data | ✅ | ✅ | ✅ |
| Cloud storage | S3 | S3 snapshots | S3 / Glacier |
| Supports SMB | ✅ | ❌ | ❌ |
| Supports NFS | ✅ | ❌ | ❌ |
| Supports iSCSI | ❌ | ✅ | ✅ |
| Can integrate with Active Directory | ✅ SMB | Depends on application/use | ❌ |
| Typical use | File shares | Backup/DR, application volumes | Tape backup replacement |
| **SAA keyword** | **SMB/NFS + S3** | **iSCSI + block storage** | **Tape/VTL + Glacier** |

---

# 1. File Gateway — **“My company has files”**

### Mental picture

```
On-Premises
Users / Applications
       |
    SMB / NFS
       |
  File Gateway
       |
   Local Cache
       |
       v
   Amazon S3
```

File Gateway provides a **file interface** to Amazon S3.

Your on-premises users continue using familiar:

- **SMB**
- **NFS**

They don't need to directly interact with S3.

### Example

A company has:

```
\\company-file-server\documents
```

Employees access:

```
Reports/
Invoices/
HR/
Projects/
```

The company wants:

- AWS durability
- local cache
- SMB access
- less on-premises storage

➡️ **File Gateway**

### SAA-C03 trigger words

| If question says... | Think |
| --- | --- |
| SMB | **File Gateway** |
| NFS | **File Gateway** |
| File share | **File Gateway** |
| Corporate documents | **File Gateway** |
| Shared files | **File Gateway** |
| S3-backed file storage | **File Gateway** |
| Local cache + S3 | **File Gateway** |

---

# 2. Volume Gateway — **“My application needs a disk”**

Volume Gateway provides **block storage** to on-premises applications through **iSCSI**.

```
On-Premises Application
        |
      iSCSI
        |
  Volume Gateway
        |
   Local Cache
        |
        v
   AWS / Amazon S3
```

The application thinks it is working with a normal **disk/volume**.

### Example

An on-premises application requires:

```
/block-device
```

and expects:

> "I need an iSCSI block volume."
> 

➡️ **Volume Gateway**

### Two important Volume Gateway modes

| Mode | What happens |
| --- | --- |
| **Cached volumes** | Primary data is stored in AWS, frequently accessed data cached locally |
| **Stored volumes** | Primary data remains locally; AWS maintains asynchronous backups/snapshots |

### Easy memory

**Cached = Cloud primary + Local cache**

**Stored = Local primary + Cloud backup**

---

# 3. Tape Gateway — **“My company uses backup tapes”**

Tape Gateway replaces physical tape infrastructure with virtual tapes.

```
Backup Application
       |
      iSCSI
       |
  Tape Gateway
       |
   Virtual Tape
       |
       v
 Amazon S3
       |
       v
 S3 Glacier
```

The existing backup application thinks:

> "I'm writing to a tape."
> 

But AWS stores the backup in the cloud.

### Example

A company has:

- Backup software
- Existing tape-based backup process
- 10-year retention requirement

They don't want to redesign the backup application.

➡️ **Tape Gateway**

### SAA-C03 keywords

| Question says... | Answer direction |
| --- | --- |
| Tape backup | **Tape Gateway** |
| Virtual tape | **Tape Gateway** |
| VTL | **Tape Gateway** |
| Existing tape infrastructure | **Tape Gateway** |
| Backup software expects tapes | **Tape Gateway** |
| Long-term archival | **Tape Gateway + Glacier** |

---

# The MOST IMPORTANT Comparison

| Requirement | Correct choice |
| --- | --- |
| SMB file share → S3 | 🟢 **File Gateway** |
| NFS file share → S3 | 🟢 **File Gateway** |
| iSCSI block storage | 🟡 **Volume Gateway** |
| Local application needs block volume | 🟡 **Volume Gateway** |
| Existing tape backup system | 🔵 **Tape Gateway** |
| Virtual tape library | 🔵 **Tape Gateway** |
| Backup to virtual tape then archive | 🔵 **Tape Gateway** |

---

# Your Question — Exact Analysis

The company requires:

> **SMB protocol**
> 

This is the biggest clue.

It also requires:

- On-premises → AWS
- Local cache
- Low-latency recently accessed files
- Durable AWS storage
- Files immediately accessible
- Long-term archival
- Cost optimization

Therefore:

### ✅ Correct answer: **File Gateway**

```
Corporate Documents
        |
      SMB
        |
        v
   File Gateway
        |
   Local Cache
        |
        v
   Amazon S3
        |
   Lifecycle Policy
        |
        v
   S3 Glacier
```

### Why?

**File Gateway** provides:

**SMB/NFS → local cache → S3**

Then S3 Lifecycle can transition older objects to Glacier storage classes.

---

# Why the Other Answers Are Wrong

| Option | Why wrong |
| --- | --- |
| **Tape Gateway + Glacier** | ❌ Tape Gateway is designed for tape/VTL-based backup, not SMB file access |
| **DataSync → S3** | ❌ DataSync transfers files but does **not provide an SMB file gateway/cache interface** |
| **File Gateway → S3 → Glacier** | ✅ **Correct** |
| **Direct Connect + EBS** | ❌ EBS isn't an on-premises SMB file share and isn't the appropriate storage architecture |

### Exam shortcut

> **SMB/NFS = File Gateway**
> 
> 
> **iSCSI = Volume Gateway**
> 
> **Tape/VTL = Tape Gateway**
> 

---

# Storage Gateway vs DataSync — Common Confusion

|  | **Storage Gateway** | **DataSync** |
| --- | --- | --- |
| Main purpose | Provide ongoing **hybrid storage access** | **Transfer/migrate data** |
| Local cache | ✅ | ❌ |
| SMB interface to AWS | ✅ File Gateway | ❌ |
| NFS interface | ✅ File Gateway | ❌ |
| iSCSI | ✅ Volume/Tape Gateway | ❌ |
| One-time migration | Not primary purpose | ✅ Excellent |
| Recurring file transfer | Possible use case, but not its primary purpose | ✅ |
| Keeps frequently accessed data cached locally | ✅ | ❌ |
| Think | **Storage extension** | **Data mover** |

### Easy distinction

**"Users need to continuously access files from on-premises"**

➡️ **Storage Gateway**

**"We need to transfer 50 TB of existing files to AWS"**

➡️ **DataSync**

---

# Storage Gateway vs Direct Connect

| Requirement | Better choice |
| --- | --- |
| Dedicated private network connection | **Direct Connect** |
| SMB file access backed by S3 | **File Gateway** |
| iSCSI block storage backed by AWS | **Volume Gateway** |
| Existing tape backup system | **Tape Gateway** |
| Move large amounts of data | **DataSync** |
| Hybrid storage with local cache | **Storage Gateway** |

**Important:** Direct Connect is a **network connection**, not a storage service.

---

# Final Exam Memory Map

```
                 AWS STORAGE GATEWAY
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
      FILE GATEWAY   VOLUME GATEWAY  TAPE GATEWAY
          |              |              |
       SMB/NFS          iSCSI          VTL
          |              |              |
          v              v              v
         S3          S3 Snapshots    S3/Glacier
          |
      File access
      + local cache
```

### 🔥 3-second SAA-C03 rule

| Keyword | Service |
| --- | --- |
| **SMB / NFS / file share** | 🟢 **File Gateway** |
| **iSCSI / block storage** | 🟡 **Volume Gateway** |
| **Tape / VTL / backup tape** | 🔵 **Tape Gateway** |
| **Move/migrate files** | 🟣 **DataSync** |
| **Dedicated private network** | 🟠 **Direct Connect** |

**Your provided question is a textbook *File Gateway* question because of the combination of `SMB + local cache + S3 + archival`.**
