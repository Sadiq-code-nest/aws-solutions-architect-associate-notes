# EFS vs FSx vs S3 vs EBS

When learning AWS storage, think of it like this:

| Service | Think of it as |
| --- | --- |
| **EBS** | A hard disk attached to **one EC2 server** |
| **EFS** | A shared network drive that **many Linux EC2s** can use together |
| **FSx** | Specialized managed file systems (Windows, Lustre, NetApp, OpenZFS) |
| **S3** | An object storage bucket for files, backups, images, videos, logs, etc. |

---

# Complete Comparison Table

| Feature | Amazon EBS | Amazon EFS | Amazon FSx | Amazon S3 |
| --- | --- | --- | --- | --- |
| Storage Type | Block Storage | File Storage | File Storage | Object Storage |
| Looks Like | Hard Disk (SSD/HDD) | Shared Network Folder | Specialized File Server | Cloud Bucket |
| Attach To | One EC2 | Many EC2s | Many Clients | Any AWS service |
| Sharing | ❌ No | ✅ Yes | ✅ Yes | Yes (through API) |
| OS Support | Linux & Windows | Linux only (NFS) | Depends on FSx type | All |
| Protocol | Block Device | NFS | SMB, NFS, Lustre, etc. | HTTPS REST API |
| Mount Required | Yes | Yes | Yes | No |
| Multiple EC2 Access | No | Yes | Yes | Yes |
| Automatically Scales | No | Yes | Yes | Unlimited |
| Maximum Size | User defines | Petabytes | Petabytes | Virtually Unlimited |
| Performance | Very High | High | Extremely High | High throughput |
| Latency | Lowest | Low | Very Low | Higher |
| Persistence | Yes | Yes | Yes | Yes |
| Availability Zone | Single AZ | Regional | Depends | Regional |
| Backup Support | Snapshots | AWS Backup | AWS Backup | Versioning |
| Encryption | Yes | Yes | Yes | Yes |
| Typical Cost | $$ | $$$ | $$$$ | Cheapest |

---

# 1. Amazon EBS (Elastic Block Store)

### Imagine

A laptop's internal SSD.

Only **one computer** uses it.

```
EC2
 │
 ├── 500GB EBS
```

Characteristics

- Block Storage
- Attached to ONE EC2
- Cannot share between multiple EC2 instances
- Very low latency
- Excellent for databases
- Boot volume for EC2
- Snapshot support
- Persistent after EC2 reboot

Best Use Cases

- OS Drive
- MySQL
- PostgreSQL
- MongoDB
- SQL Server
- Application installation

Example

```
EC2 Web Server

C:
D:

These drives are EBS.
```

---

# 2. Amazon EFS (Elastic File System)

Imagine a shared office drive.

Everyone in the office accesses the same folder.

```
          EFS

      /    |    \
    EC2   EC2   EC2
```

Characteristics

- File Storage
- NFS protocol
- Multiple EC2 simultaneously
- Auto grows
- Auto shrinks
- Regional
- Highly available

Best Use Cases

- Shared website files
- WordPress uploads
- Shared configuration
- Home directories
- ML datasets

Example

Suppose 10 web servers.

```
EC2-1
EC2-2
EC2-3
EC2-4

      ↓

      EFS

/images
/videos
/css
/uploads
```

Every server sees the same files.

---

# 3. Amazon FSx

FSx means AWS manages a specialized file system for you.

There are different versions.

| FSx Type | Best For |
| --- | --- |
| FSx for Windows | Windows File Server |
| FSx for Lustre | HPC, AI, ML |
| FSx for NetApp ONTAP | Enterprise NAS |
| FSx for OpenZFS | Linux workloads |

---

## FSx for Windows

Supports:

- SMB
- NTFS
- Active Directory
- Windows permissions

Perfect for

```
Windows Server

Shared Folder

\\company\shared
```

---

## FSx for Lustre

Built for supercomputers.

Used by

- AI
- ML
- Video rendering
- Genomics
- Scientific computing

Can deliver hundreds of GB/s throughput.

---

## FSx for NetApp ONTAP

Enterprise storage supporting:

- Snapshots
- Cloning
- Replication
- Deduplication
- Compression

Used by large companies migrating existing NetApp environments to AWS.

- Details NetApp
    
    Amazon FSx for NetApp ONTAP is a **managed AWS file storage service based on NetApp ONTAP**.
    
    Think of it as:
    
    > **A highly capable managed file storage system that supports multiple storage protocols, including NFS, SMB, and iSCSI.**
    > 
    
    Instead of you buying physical NetApp storage:
    
    ```
    Your data center
    
    Server
       │
       ▼
    Physical NetApp storage
       │
       ├── disks
       ├── controllers
       ├── networking
       ├── replication
       └── administration
    ```
    
    AWS gives you:
    
    ```
    AWS
     │
     ▼
    Amazon FSx for NetApp ONTAP
     │
     ├── Storage
     ├── ONTAP capabilities
     ├── Snapshots
     ├── Replication
     ├── NFS
     ├── SMB
     └── iSCSI
    ```
    
    AWS manages much of the underlying infrastructure for you.
    
    ---
    
    # 2. Why is ONTAP special?
    
    The important thing for SAA is that ONTAP can support **multiple ways of accessing storage**.
    
    You can have:
    
    ```
                       FSx for NetApp ONTAP
                                │
                 ┌──────────────┼──────────────┐
                 │              │              │
                 ▼              ▼              ▼
                NFS            SMB           iSCSI
                 │              │              │
                 ▼              ▼              ▼
               Linux         Windows        EC2/Linux/
                                             Windows
    ```
    
    So one FSx for ONTAP environment can support different workloads.
    
    ---
    
    # 3. Before NFS vs SMB vs iSCSI: Understand "file" vs "block"
    
    This is the **most important concept**.
    
    There are two fundamentally different ways applications can access storage.
    
    ## File storage
    
    You see files and folders:
    
    ```
    /shared
       │
       ├── report.pdf
       ├── photo.jpg
       ├── invoices/
       │      ├── 001.pdf
       │      └── 002.pdf
       └── users/
    ```
    
    The client says:
    
    > "Give me `/shared/report.pdf`."
    > 
    
    That's **file-level access**.
    
    NFS and SMB are file protocols.
    
    ---
    
    ## Block storage
    
    The client sees a block device:
    
    ```
    /dev/sdb
    ```
    
    or conceptually:
    
    ```
    Disk
    ┌──────────────────────────┐
    │ block 1                  │
    │ block 2                  │
    │ block 3                  │
    │ block 4                  │
    │ ...                      │
    └──────────────────────────┘
    ```
    
    The storage system doesn't primarily say:
    
    > "Here's `report.pdf`."
    > 
    
    Instead, the operating system sees something like a disk and manages the filesystem itself.
    
    That's **block-level storage**.
    
    iSCSI is used for this.
    
- **NFS, SMB, and iSCSI.**
    
    # Before NFS vs SMB vs iSCSI: Understand "file" vs "block"
    
    This is the **most important concept**.
    
    There are two fundamentally different ways applications can access storage.
    
    ## File storage
    
    You see files and folders:
    
    ```
    /shared
       │
       ├── report.pdf
       ├── photo.jpg
       ├── invoices/
       │      ├── 001.pdf
       │      └── 002.pdf
       └── users/
    ```
    
    The client says:
    
    > "Give me `/shared/report.pdf`."
    > 
    
    That's **file-level access**.
    
    NFS and SMB are file protocols.
    
    ---
    
    ## Block storage
    
    The client sees a block device:
    
    ```
    /dev/sdb
    ```
    
    or conceptually:
    
    ```
    Disk
    ┌──────────────────────────┐
    │ block 1                  │
    │ block 2                  │
    │ block 3                  │
    │ block 4                  │
    │ ...                      │
    └──────────────────────────┘
    ```
    
    The storage system doesn't primarily say:
    
    > "Here's `report.pdf`."
    > 
    
    Instead, the operating system sees something like a disk and manages the filesystem itself.
    
    That's **block-level storage**.
    
    iSCSI is used for this.
    
    ---
    
    # 4. NFS
    
    ## NFS = Network File System
    
    NFS is a **file-sharing protocol**, traditionally associated with Linux/Unix environments.
    
    Imagine you have:
    
    ```
    EC2 Linux Server
           │
           │ NFS
           ▼
    FSx for ONTAP
           │
           ▼
    /shared
    ```
    
    The EC2 server mounts the remote filesystem:
    
    ```
    /mnt/shared
    ```
    
    Then your application can do:
    
    ```
    /mnt/shared/report.txt
    /mnt/shared/images/photo.jpg
    /mnt/shared/logs/app.log
    ```
    
    The files physically live on FSx, but your Linux server accesses them over the network.
    
    ---
    
    # 5. Real NFS example
    
    Suppose you have 10 Linux web servers:
    
    ```
                 FSx for ONTAP
                       │
                    NFS share
                       │
           ┌───────────┼───────────┐
           │           │           │
           ▼           ▼           ▼
         EC2-1       EC2-2       EC2-3
         Linux       Linux       Linux
    ```
    
    All servers need access to:
    
    ```
    /shared/uploads
    ```
    
    Instead of storing uploads separately:
    
    ```
    EC2-1 → /uploads
    EC2-2 → /uploads
    EC2-3 → /uploads
    ```
    
    you use shared file storage:
    
    ```
                  FSx ONTAP
                      │
                 /uploads
                      │
            ┌─────────┼─────────┐
            ▼         ▼         ▼
          EC2-1     EC2-2     EC2-3
    ```
    
    All three servers can access the same files.
    
    ### Typical clue
    
    If the question says:
    
    > "Linux servers need a shared filesystem."
    > 
    
    Think:
    
    **NFS + FSx for ONTAP**
    
    ---
    
    # 6. SMB
    
    ## SMB = Server Message Block
    
    SMB is also a **file-sharing protocol**, but it is strongly associated with **Windows**.
    
    Imagine:
    
    ```
    Windows EC2
         │
         │ SMB
         ▼
    FSx for ONTAP
         │
         ▼
    \\fileserver\shared
    ```
    
    A Windows user might see:
    
    ```
    \\company-files\documents
    ```
    
    and inside:
    
    ```
    \\company-files\documents
        │
        ├── report.docx
        ├── salary.xlsx
        └── presentation.pptx
    ```
    
    Again, this is **file-level access**.
    
    ---
    
    # 7. Real SMB example
    
    Suppose a company has:
    
    ```
    100 Windows employees
    ```
    
    They need a shared folder:
    
    ```
    \\company-files\finance
    ```
    
    You could use FSx for ONTAP with SMB.
    
    Conceptually:
    
    ```
    Windows PCs
        │
        │ SMB
        ▼
    FSx for ONTAP
        │
        ▼
    Finance share
    ```
    
    Employees see:
    
    ```
    Finance
     ├── 2026-budget.xlsx
     ├── invoices
     └── reports
    ```
    
    This is exactly the type of file-sharing scenario SMB is designed for.
    
    ---
    
    # 8. NFS vs SMB
    
    Both are **file protocols**.
    
    That's the first thing to memorize.
    
    ```
                     FILE STORAGE
                          │
                  ┌───────┴───────┐
                  │               │
                 NFS             SMB
                  │               │
               Linux            Windows
    ```
    
    ### NFS
    
    Think:
    
    > **Linux/Unix → shared files**
    > 
    
    Example:
    
    ```
    Linux EC2
       │
       │ NFS
       ▼
    FSx ONTAP
       │
     /shared
    ```
    
    ### SMB
    
    Think:
    
    > **Windows → shared files**
    > 
    
    Example:
    
    ```
    Windows Server
          │
          │ SMB
          ▼
    FSx ONTAP
          │
     \\server\share
    ```
    
    ---
    
    # 9. Now iSCSI — this is where things change
    
    ## iSCSI = Internet Small Computer Systems Interface
    
    iSCSI is a protocol for accessing **block storage over IP networks**.
    
    That's the critical phrase:
    
    > **Block storage over a network.**
    > 
    
    Instead of:
    
    ```
    Server
       │
       │ "Give me /shared/file.txt"
       ▼
    File storage
    ```
    
    you get:
    
    ```
    Server
       │
       │ iSCSI
       ▼
    Block storage
    ```
    
    The operating system sees something similar to a disk.
    
    ---
    
    # 10. Imagine a physical hard drive
    
    If you install a physical disk into your server:
    
    ```
    Server
      │
      └── /dev/sdb
    ```
    
    The OS can create a filesystem:
    
    ```
    /dev/sdb
       ↓
    ext4 / XFS / NTFS
       ↓
    files and directories
    ```
    
    With iSCSI, the disk can be **remote**:
    
    ```
    Server
       │
       │ IP network
       │ iSCSI
       ▼
    Storage system
    ```
    
    But the server can treat the remote storage like a block device.
    
    That's the magic.
    
    ---
    
    # 11. iSCSI example
    
    Suppose you have:
    
    ```
    Linux EC2
    ```
    
    and need a 1 TB block device.
    
    FSx for ONTAP can provide storage through iSCSI.
    
    Conceptually:
    
    ```
                  EC2 Linux
                      │
                      │ iSCSI
                      ▼
              FSx for ONTAP
                      │
                      ▼
                 1 TB LUN
    ```
    
    The EC2 instance sees something like:
    
    ```
    /dev/sdb
    ```
    
    Then **the EC2 operating system** can format it:
    
    ```
    /dev/sdb
        ↓
    XFS
        ↓
    /data
    ```
    
    Now:
    
    ```
    /data
     ├── database files
     ├── application files
     └── other data
    ```
    
    The key distinction is:
    
    **The client/server owns the filesystem.**
    
    ---
    
    # 12. NFS vs iSCSI — the most important difference
    
    Imagine you have storage containing:
    
    ```
    report.pdf
    photo.jpg
    database.db
    ```
    
    ### NFS
    
    The storage system understands that you're requesting files.
    
    ```
    Linux
      │
      │ "Give me /shared/report.pdf"
      ▼
    FSx ONTAP
      │
      ▼
    report.pdf
    ```
    
    ### iSCSI
    
    The client sees a block device:
    
    ```
    Linux
      │
      │ "Give me blocks"
      ▼
    FSx ONTAP
      │
      ▼
    LUN
      │
      ▼
    Linux filesystem
      │
      ▼
    report.pdf
    ```
    
    So:
    
    ```
    NFS
    Client → Files/Folders → Network storage
    
    iSCSI
    Client → Block device → Network storage
    ```
    
    ---
    
    # 13. SMB vs iSCSI
    
    This distinction is also important.
    
    ### SMB
    
    ```
    Windows
       │
       │ SMB
       ▼
    FSx
       │
       ▼
    \\server\share
    ```
    
    Windows sees:
    
    ```
    Folders
    Files
    Permissions
    ```
    
    ### iSCSI
    
    ```
    Windows
       │
       │ iSCSI
       ▼
    FSx
       │
       ▼
    Block device / LUN
    ```
    
    Windows sees something like a disk.
    
    It can then create/use a filesystem on that block device.
    
    ---
    
    # 14. A very easy analogy
    
    Imagine a warehouse.
    
    ## NFS
    
    You say:
    
    > "Give me this particular box."
    > 
    
    ```
    Client
      │
      │ "Give me report.pdf"
      ▼
    Warehouse
    ```
    
    The storage system manages the files.
    
    ---
    
    ## SMB
    
    Same concept:
    
    > "Give me this particular file from this shared folder."
    > 
    
    ```
    Windows
       │
       │ "Give me \\server\finance\report.xlsx"
       ▼
    Warehouse
    ```
    
    Still file-level.
    
    ---
    
    ## iSCSI
    
    You say:
    
    > "Give me this entire storage area."
    > 
    
    ```
    Client
      │
      │ "Give me this block device"
      ▼
    Warehouse
    ```
    
    Then **you** organize the storage:
    
    ```
    Block device
        ↓
    Filesystem
        ↓
    Directories
        ↓
    Files
    ```
    
    ---
    
    # 15. The exam-friendly comparison
    
    | Feature | NFS | SMB | iSCSI |
    | --- | --- | --- | --- |
    | Storage type | **File** | **File** | **Block** |
    | Common OS | Linux/Unix | Windows | Linux/Windows |
    | Client sees | Files/folders | Files/folders | Disk/block device |
    | Shared file access | ✅ | ✅ | ❌ typically not the goal |
    | Filesystem managed by | Storage system | Storage system | **Client/host** |
    | Typical use | Linux shared filesystem | Windows shared filesystem | Databases, applications needing block storage |
    | Example | `/mnt/shared` | `\\server\share` | `/dev/sdb` |
    | FSx ONTAP support | ✅ | ✅ | ✅ |
    
    ---
    
    # 16. One scenario using all three
    
    Imagine a company has an application:
    
    ```
                      Application
                          │
              ┌───────────┼────────────┐
              │           │            │
              ▼           ▼            ▼
           Linux        Windows      Database
           servers      users        server
              │           │            │
             NFS          SMB         iSCSI
              │           │            │
              └───────────┼────────────┘
                          ▼
                 FSx for NetApp ONTAP
    ```
    
    ### Linux application servers
    
    Need:
    
    ```
    /shared/images
    ```
    
    Use:
    
    **NFS**
    
    ```
    Linux → NFS → FSx ONTAP
    ```
    
    ### Windows employees
    
    Need:
    
    ```
    \\company\documents
    ```
    
    Use:
    
    **SMB**
    
    ```
    Windows → SMB → FSx ONTAP
    ```
    
    ### Database server
    
    Needs a block device:
    
    ```
    /dev/sdb
    ```
    
    Use:
    
    **iSCSI**
    
    ```
    Database server → iSCSI → FSx ONTAP
    ```
    
    One FSx for ONTAP system can therefore support different workloads through different protocols.
    
    ---
    
    # 17. Why would you choose iSCSI for a database?
    
    Suppose you're running a database application.
    
    The database expects to manage its own storage layout and filesystem behavior.
    
    You could have:
    
    ```
    Database server
           │
           ▼
       /dev/sdb
           │
           ▼
        XFS/ext4
           │
           ▼
     Database files
    ```
    
    The database server has control over the block device.
    
    This is fundamentally different from:
    
    ```
    Database server
           │
           ▼
    NFS share
           │
           ▼
    /shared/database
    ```
    
    Whether NFS is appropriate depends on the database and workload, but when the requirement specifically says **block storage**, iSCSI is the protocol clue.
    
    ---
    
    # 18. A critical exam distinction: "shared storage"
    
    Be careful when you see:
    
    > "Multiple servers need access to shared storage."
    > 
    
    Don't immediately choose NFS/SMB.
    
    Ask:
    
    ### Do they need shared FILES?
    
    ```
    Server A ─┐
    Server B ─┼──> shared files
    Server C ─┘
    ```
    
    Then:
    
    **NFS / SMB**
    
    ---
    
    ### Do they need BLOCK storage?
    
    ```
    Server
       │
       ▼
    Block device
    ```
    
    Then:
    
    **iSCSI**
    
    ---
    
    # 19. FSx for ONTAP vs EFS vs FSx Windows File Server
    
    This is useful for AWS exams.
    
    ### Amazon EFS
    
    Think:
    
    > **Managed shared Linux file system**
    > 
    
    ```
    Linux EC2
       │
       │ NFS
       ▼
    EFS
    ```
    
    ### FSx for Windows File Server
    
    Think:
    
    > **Managed Windows file storage**
    > 
    
    ```
    Windows
       │
       │ SMB
       ▼
    FSx for Windows
    ```
    
    ### FSx for NetApp ONTAP
    
    Think:
    
    > **Advanced managed NetApp storage supporting multiple protocols and enterprise features**
    > 
    
    ```
                 FSx ONTAP
                 /   |   \
                /    |    \
              NFS   SMB  iSCSI
    ```
    
    This is why FSx for ONTAP appears frequently in more advanced storage questions.
    
    ---
    
    # 20. Why would AWS give me FSx ONTAP if EFS and FSx Windows already exist?
    
    Because ONTAP is aimed at workloads needing **NetApp ONTAP capabilities and flexibility**.
    
    For example, you may need:
    
    - NFS
    - SMB
    - iSCSI
    - Snapshots
    - Data replication
    - Multi-protocol access
    - Enterprise storage features
    - Existing NetApp workloads migrated to AWS
    
    So you might have:
    
    ```
    Existing enterprise environment
    
    Linux ──NFS──┐
                 │
    Windows ─SMB─┼──> NetApp ONTAP
                 │
    Apps ─iSCSI──┘
    ```
    
    And you want to migrate that architecture to AWS.
    
    FSx for ONTAP can be a natural fit.
    
    ---
    
    # 21. SAA-C03 scenario examples
    
    ### Scenario A
    
    > A company has Linux EC2 instances that need a shared filesystem.
    > 
    
    Think:
    
    ```
    Linux
      ↓
    NFS
      ↓
    FSx / EFS
    ```
    
    ---
    
    ### Scenario B
    
    > A company has Windows servers that require a shared file system with Windows-compatible file sharing.
    > 
    
    Think:
    
    ```
    Windows
       ↓
    SMB
       ↓
    FSx for Windows / FSx ONTAP
    ```
    
    ---
    
    ### Scenario C
    
    > A workload requires block-level storage accessible over the network.
    > 
    
    Think:
    
    ```
    Server
       ↓
    iSCSI
       ↓
    FSx for ONTAP
    ```
    
    ---
    
    ### Scenario D
    
    > The company is migrating an existing NetApp environment to AWS and needs NFS, SMB, and iSCSI support.
    > 
    
    Think:
    
    ```
    🔥 FSx for NetApp ONTAP
    ```
    
    ---
    
    # 22. The ultimate decision tree
    
    When you see a storage question:
    
    ### Step 1 — What does the application need?
    
    **Files and folders?**
    
    ```
    YES
     ↓
    File storage
    ```
    
    Then ask:
    
    **Linux?**
    
    ```
    YES → NFS
    ```
    
    **Windows?**
    
    ```
    YES → SMB
    ```
    
    ---
    
    ### Step 2 — Does the application need a disk/block device?
    
    ```
    YES
     ↓
    Block storage
     ↓
    iSCSI
    ```
    
    ---
    
    # 🧠 Memorize this
    
    ```
                        FSx for NetApp ONTAP
                                 │
                  ┌──────────────┼──────────────┐
                  │              │              │
                  ▼              ▼              ▼
                 NFS            SMB           iSCSI
                  │              │              │
                  ▼              ▼              ▼
               FILES           FILES          BLOCKS
                  │              │              │
               Linux          Windows       Linux/Windows
    ```
    
    Or even shorter:
    
    > 🐧 **NFS → Linux → Files**
    > 
    
    > 🪟 **SMB → Windows → Files**
    > 
    
    > 💽 **iSCSI → Block storage → Disk**
    > 
    
    > 🗄️ **FSx for NetApp ONTAP → Managed enterprise storage that can support NFS + SMB + iSCSI**
    > 
    
    ### One SAA exam trick
    
    If the question says:
    
    **"Shared files between Linux servers"** → **NFS**
    
    **"Shared files between Windows servers/users"** → **SMB**
    
    **"Block storage over the network / server sees a disk/LUN"** → **iSCSI**
    
    **"Existing NetApp / ONTAP workload + NFS/SMB/iSCSI / enterprise storage features"** → **FSx for NetApp ONTAP**
    
- In short
    
    
    | Topic | Simply means | Think |
    | --- | --- | --- |
    | **FSx for NetApp ONTAP** | Managed AWS **enterprise file storage** supporting NFS, SMB, iSCSI | 🗄️ **Storage system** |
    | **NFS** | File-sharing protocol mainly for **Linux/Unix** | 🐧 **Linux → Files** |
    | **SMB** | File-sharing protocol mainly for **Windows** | 🪟 **Windows → Files** |
    | **iSCSI** | Protocol for **block storage over a network** | 💽 **Disk/Blocks** |
    
    ### Remember this:
    
    ```
    FSx for NetApp ONTAP
            │
       ┌────┼────┐
       ↓    ↓    ↓
      NFS  SMB  iSCSI
       ↓    ↓    ↓
     Files Files Blocks
       ↓    ↓    ↓
    Linux Windows Disk
    ```
    
    **SAA-C03 shortcut:**
    
    - 🐧 **Linux shared files → NFS**
    - 🪟 **Windows shared files → SMB**
    - 💽 **Block storage / LUN → iSCSI**
    - 🗄️ **Need NetApp ONTAP capabilities → FSx for NetApp ONTAP**
    

---

## FSx for OpenZFS

Provides:

- ZFS snapshots
- Checksums
- Compression
- Fast Linux file serving

---

# 4. Amazon S3

Imagine Google Drive, but for applications.

```
Bucket

photo.jpg

movie.mp4

backup.zip

logs.txt
```

Characteristics

- Object Storage
- Unlimited scale
- No mounting required (unless using special tools)
- Access via HTTP/HTTPS APIs
- Extremely durable (11 nines: 99.999999999%)
- Lifecycle management
- Versioning
- Static website hosting

Best Use Cases

- Images
- Videos
- Documents
- Backups
- Logs
- Data lakes
- Static websites

---

# Storage Structure Comparison

## EBS

```
EC2

└── Disk

C:
D:
```

Looks like a local disk.

---

## EFS

```
EC2-1

EC2-2

EC2-3

      ↓

 Shared Folder
```

Looks like a shared network drive.

---

## FSx

```
Windows PCs

     ↓

Windows File Server
```

Or a specialized managed file system depending on the FSx variant.

---

## S3

```
Bucket

├── img1.jpg
├── img2.jpg
├── backup.zip
├── logs.txt
```

Objects inside a bucket.

---

# Performance Ranking

| Service | Speed |
| --- | --- |
| EBS | ⭐⭐⭐⭐⭐ Fastest for a single EC2 |
| FSx for Lustre | ⭐⭐⭐⭐⭐ Extremely high throughput |
| EFS | ⭐⭐⭐⭐ |
| S3 | ⭐⭐⭐ (higher latency than block/file storage) |

---

# Cost Ranking

Cheapest → Expensive

---

S3

---

EBS

---

EFS

---

FSx (varies by type, often the most expensive)

---

---

# When Should You Use Each?

| Scenario | Best Choice | Why |
| --- | --- | --- |
| EC2 Boot Volume | EBS | Acts like a local disk |
| MySQL Database | EBS | Lowest latency block storage |
| Shared Website Files | EFS | Multiple EC2s access the same files |
| Windows Shared Drive | FSx for Windows | SMB, NTFS, Active Directory integration |
| AI/ML Training | FSx for Lustre | Extremely high-performance parallel file system |
| Enterprise NAS Migration | FSx for NetApp ONTAP | Advanced enterprise storage features |
| Backups | S3 | Durable and low cost |
| Static Website | S3 | Built for serving static content |
| Images & Videos | S3 | Massive scalability and object storage |
| Shared Linux Home Directories | EFS | Concurrent access from many Linux instances |

---

# Easy Way to Remember

| If you need... | Choose |
| --- | --- |
| A hard drive for one EC2 | **EBS** |
| One folder shared by many Linux EC2s | **EFS** |
| A managed Windows or specialized enterprise/HPC file system | **FSx** |
| A bucket to store unlimited files, backups, images, videos, and logs | **S3** |

### One-line memory trick

- **EBS = One EC2, One Disk**
- **EFS = Many EC2s, One Shared Folder**
- **FSx = Specialized Managed File System**
- **S3 = Unlimited Cloud Object Storage**
