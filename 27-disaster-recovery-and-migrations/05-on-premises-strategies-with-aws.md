# On-Premises Strategies with AWS

## On-Premises Strategy with AWS — SAA-C03

This topic is about **how AWS helps you migrate, replicate, discover, and recover on-premises workloads**.

### 1. Amazon Linux 2 as a VM

You can download **Amazon Linux 2 AMI as a VM image** (`.iso` format) and run it on:

- VMware
- KVM
- VirtualBox (Oracle VM)
- Microsoft Hyper-V

This allows you to run Amazon Linux in your **on-premises virtualization environment**.

---

## 2. VM Import / Export

**VM Import/Export** allows you to move existing virtual machines between your on-premises environment and AWS.

### On-Premises → AWS

```
On-Premises VM
      ↓
VM Import
      ↓
Amazon EC2
```

### AWS → On-Premises

```
Amazon EC2
    ↓
VM Export
    ↓
On-Premises VM
```

### Main use cases

- Migrate existing applications into **EC2**
- Create a **DR repository strategy** for on-premises VMs
- Export VMs from EC2 back to on-premises

### 🧠 Exam clue

> **Move a VM image between on-premises and EC2**
> 

➡️ **VM Import/Export**

---

# 3. AWS Application Discovery Service

Used when you first need to **understand your existing on-premises environment before migration**.

It gathers information about your on-premises servers, such as:

- Server utilization
- Server information
- Application dependencies
- Dependency mappings

```
On-Premises Servers
        ↓
Application Discovery Service
        ↓
Server information
Utilization
Dependencies
        ↓
Migration planning
```

### 🧠 Think:

> **"What do I have on-premises, and what depends on what?"**
> 

➡️ **AWS Application Discovery Service**

---

# 4. AWS Migration Hub

**AWS Migration Hub** helps you **track migration progress** across your applications and servers.

A simple way to remember the relationship:

```
Application Discovery Service
        ↓
Discover servers + dependencies
        ↓
AWS Migration Hub
        ↓
Track migration
```

### 🧠 Exam distinction

**Discovery Service** → **Discover / collect information**

**Migration Hub** → **Track migration**

---

# 5. AWS Database Migration Service (DMS)

**AWS DMS** is specifically for **database migration and replication**.

It can replicate:

```
On-Premises → AWS
AWS → AWS
AWS → On-Premises
```

It supports various database technologies, including:

- Oracle
- MySQL
- DynamoDB
- And others

### Example

```
On-Premises Oracle
        │
        │ AWS DMS
        ▼
Amazon RDS / AWS Target
```

### 🧠 Exam clue

> **Database migration / continuous database replication**
> 

➡️ **AWS DMS**

---

# 6. AWS Server Migration Service (SMS)

AWS Server Migration Service was designed for **incremental replication of live on-premises servers to AWS**.

```
On-Premises Live Server
        │
        │ Incremental replication
        ▼
       AWS
```

### 🧠 Think:

> **Server migration → AWS SMS**
> 

⚠️ **Exam note:** AWS Server Migration Service (SMS) is a **legacy service** and has been replaced by **AWS Application Migration Service (MGN)** for many server-migration scenarios. For current AWS exam preparation, know **AWS MGN** as the modern service.

---

# 🔥 SAA-C03 Quick Comparison

| Requirement | AWS Service |
| --- | --- |
| Run Amazon Linux as VM on-premises | Amazon Linux VM image |
| Import VM into EC2 | **VM Import/Export** |
| Export EC2 VM back on-premises | **VM Import/Export** |
| Discover on-premises servers | **Application Discovery Service** |
| Find server dependencies | **Application Discovery Service** |
| Track migration progress | **Migration Hub** |
| Migrate databases | **AWS DMS** |
| Replicate DB On-Prem → AWS | **AWS DMS** |
| Replicate DB AWS → AWS | **AWS DMS** |
| Replicate DB AWS → On-Prem | **AWS DMS** |
| Incrementally migrate live servers | **AWS Application Migration Service (MGN)** |

## 🧠 One-Line Memory Trick

**VM → VM Import/Export**

**Discover → Application Discovery Service**

**Track → Migration Hub**

**Database → DMS**

**Server → MGN**
