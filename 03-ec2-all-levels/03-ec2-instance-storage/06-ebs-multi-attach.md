# EBS Multi-Attach — AWS SAA-C03

### Simple definition

> **EBS Multi-Attach allows one EBS volume to be attached to multiple EC2 instances at the same time.**
> 

It is supported with **io1/io2** volumes.

```
             Same EBS Volume
                  │
        ┌─────────┼─────────┐
        ↓         ↓         ↓
      EC2-A     EC2-B     EC2-C
      Read/     Read/     Read/
      Write     Write     Write
```

### Key requirements

- **io1 / io2 only**
- EC2 instances must be in the **same Availability Zone**
- Up to **16 EC2 instances** can attach to one volume
- Each instance gets **read/write access**
- The application must handle **concurrent writes**
- Requires a **cluster-aware file system**

### Why use it?

Mainly for **clustered applications** that need multiple EC2 instances to access the same high-performance block storage.

Example:

```
EC2-A ─┐
       ├── EBS io2
EC2-B ─┤
       │
EC2-C ─┘
```

If one EC2 fails, another instance can continue accessing the shared volume, helping improve **application availability**.

### ⚠️ Important exam point

You **cannot simply mount the same Multi-Attach EBS volume with normal file systems such as XFS or EXT4** from multiple instances because they aren't designed to safely coordinate concurrent writes.

You need a **cluster-aware file system/application** that can manage shared access.

### Multi-Attach vs EFS

|  | EBS Multi-Attach | EFS |
| --- | --- | --- |
| Type | Block storage | File storage |
| Multiple EC2 | ✅ | ✅ |
| AZ | **Same AZ** | Regional / multi-AZ |
| Performance | Very high | Shared file access |
| Supported EBS type | **io1/io2** | Not applicable |
| Concurrent access | Application/filesystem must manage it | Designed for shared file access |

### 🧠 Memory Trick

> **Multi-Attach = ONE io1/io2 EBS → MULTIPLE EC2 → SAME AZ**
> 

And remember:

> **Multi-Attach is not a general shared-storage solution like EFS; it's for specific clustered applications that can safely coordinate shared block-level access.**
>
