# Amazon FSx Comparison (SAA-C03 Exam Cheat Sheet)

Instead of memorizing, ask yourself this question:

> **"What file system or workload does the application need?"**
> 

The answer immediately tells you which FSx service to use.

| Feature | **FSx for Windows File Server** | **FSx for Lustre** | **FSx for NetApp ONTAP** | **FSx for OpenZFS** |
| --- | --- | --- | --- | --- |
| **Purpose** | Windows shared file server | High Performance Computing (HPC) | Enterprise NAS/File Storage | Managed ZFS file system |
| **Best For** | Windows users sharing files | Machine Learning, AI, HPC | Existing enterprise storage | Existing ZFS workloads |
| **Primary Users** | Windows companies | Data scientists, researchers | Large enterprises | Linux/Unix users |
| **Main Protocol** | SMB | Lustre | SMB, NFS, iSCSI | NFS |
| **Works With** | Windows, Linux | Linux | Windows, Linux, Mac, VMware | Linux, Windows, Mac |
| **Active Directory** | ✅ Yes | ❌ No | ✅ Yes | ❌ No |
| **S3 Integration** | ❌ No | ✅ Excellent | ❌ Limited | ❌ No |
| **Performance** | High | Extremely High | High | Very High |
| **Latency** | Low | Sub-millisecond | Low | < 0.5 ms |
| **Snapshots** | ✅ Yes | Limited | ✅ Advanced | ✅ Yes |
| **Compression** | Basic | ❌ No | ✅ Yes | ✅ Yes |
| **Deduplication** | ❌ No | ❌ No | ✅ Yes | ❌ No |
| **Instant Clone** | ❌ No | ❌ No | ✅ Yes | ✅ Yes |
| **Typical Storage** | Company shared folders | Massive datasets | Enterprise business data | Linux application data |

---

# Easy Way to Choose (Decision Tree)

```
Need Windows shared folder?

        ↓ YES

FSx Windows

----------------------------

Need Machine Learning?

Need HPC?

Need Video Rendering?

Need S3 integration?

        ↓ YES

FSx Lustre

----------------------------

Already using NetApp?

Need SMB + NFS + iSCSI?

Need snapshots + deduplication?

        ↓ YES

FSx NetApp ONTAP

----------------------------

Already using ZFS?

Need NFS with ZFS?

        ↓ YES

FSx OpenZFS
```

---

# Real-World Examples

## 🪟 FSx for Windows File Server

### Company

```
ABC Bank
```

Employees use

```
\\Company\Finance

\\Company\HR

\\Company\Projects
```

They use

- Windows PCs
- Active Directory
- NTFS permissions

Moving to AWS.

They don't want to change anything.

### Best Choice

✅ **FSx for Windows**

---

## 🚀 FSx for Lustre

Company

```
Tesla AI
```

Needs to train

Machine Learning models

using

```
500 TB

of images

stored in S3
```

Need

- very high throughput
- extremely low latency

### Best Choice

✅ **FSx for Lustre**

Because it can read directly from S3 at extremely high speed.

---

## 🏢 FSx for NetApp ONTAP

Company already owns

```
NetApp Storage
```

They use

- SMB
- NFS
- iSCSI

Need

- Snapshots
- Compression
- Deduplication
- Replication

without changing applications.

### Best Choice

✅ **FSx for NetApp ONTAP**

---

## 🐧 FSx for OpenZFS

Company already uses

```
OpenZFS
```

Linux applications depend on ZFS features.

Need

- snapshots
- compression
- fast NFS storage

### Best Choice

✅ **FSx for OpenZFS**

---

# Feature Comparison

| Requirement | Windows | Lustre | NetApp | OpenZFS |
| --- | --- | --- | --- | --- |
| Shared office folders | ⭐⭐⭐⭐⭐ | ❌ | ⭐⭐⭐⭐ | ⭐⭐ |
| Machine Learning | ❌ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐ |
| High Performance Computing | ❌ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐ |
| Video Rendering | ❌ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐ |
| Enterprise NAS | ⭐⭐⭐ | ⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| Windows Environment | ⭐⭐⭐⭐⭐ | ❌ | ⭐⭐⭐⭐ | ⭐ |
| Linux Environment | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Active Directory | ⭐⭐⭐⭐⭐ | ❌ | ⭐⭐⭐⭐ | ❌ |
| Existing NetApp Customers | ❌ | ❌ | ⭐⭐⭐⭐⭐ | ❌ |
| Existing ZFS Customers | ❌ | ❌ | ❌ | ⭐⭐⭐⭐⭐ |
| Read data directly from S3 | ❌ | ⭐⭐⭐⭐⭐ | ❌ | ❌ |

---

# One-Line Memory Trick

| Service | Remember This |
| --- | --- |
| 🪟 **FSx Windows** | **Windows File Server in AWS** |
| 🚀 **FSx Lustre** | **Supercomputer storage for AI, HPC, and S3** |
| 🏢 **FSx NetApp ONTAP** | **Enterprise NAS with advanced storage features** |
| 🐧 **FSx OpenZFS** | **Managed ZFS file system for Linux/Unix workloads** |

---

# SAA-C03 Exam Scenarios

| Exam Question | Correct Answer | Why? |
| --- | --- | --- |
| Company uses Windows shared drives with Active Directory | ✅ FSx for Windows | Native SMB + NTFS + AD integration |
| Train ML models on 500 TB stored in S3 | ✅ FSx for Lustre | Highest throughput and direct S3 integration |
| Enterprise already uses NetApp storage on-premises | ✅ FSx for NetApp ONTAP | Minimal migration changes and enterprise storage features |
| Existing Linux application depends on ZFS | ✅ FSx for OpenZFS | Native ZFS capabilities |
| Need snapshots, deduplication, SMB, NFS, and iSCSI together | ✅ FSx for NetApp ONTAP | Richest enterprise feature set |
| Need temporary ultra-fast storage for rendering or HPC jobs | ✅ FSx for Lustre (Scratch) | Optimized for short-lived, high-speed workloads |

---

# 🎯 30-Second Exam Shortcut

```
Windows users?
      ↓
FSx Windows

Need AI / HPC / S3 integration?
      ↓
FSx Lustre

Already using NetApp?
      ↓
FSx NetApp ONTAP

Already using ZFS?
      ↓
FSx OpenZFS
```

If you remember this decision flow, you'll answer **almost every FSx question on the SAA-C03 exam** without memorizing individual features.
