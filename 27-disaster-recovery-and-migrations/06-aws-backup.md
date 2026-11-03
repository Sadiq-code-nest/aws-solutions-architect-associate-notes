# AWS Backup — SAA-C03 Notes

## What is AWS Backup?

**AWS Backup** is a **fully managed service** that lets you **centrally manage and automate backups across AWS services**.

Instead of creating custom backup scripts and manually managing backups, AWS Backup provides a centralized backup system.

```
                    AWS Backup
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
       EC2             S3             RDS
        ↓              ↓              ↓
      EBS           Aurora        DynamoDB
        ↓                             ↓
       EFS                    DocumentDB / Neptune
```

### 🧠 Exam shortcut

> **Centralized + automated backup across multiple AWS services**
> 

➡️ **AWS Backup**

---

# Supported AWS Services

AWS Backup supports backups for:

- **Amazon EC2 / Amazon EBS**
- **Amazon S3**
- **Amazon RDS** — all DB engines
- **Amazon Aurora**
- **Amazon DynamoDB**
- **Amazon DocumentDB**
- **Amazon Neptune**
- **Amazon EFS**
- **Amazon FSx**
    - FSx for Lustre
    - FSx for Windows File Server
- **AWS Storage Gateway**
    - Volume Gateway

---

# AWS Backup — Backup Features

### 1. Cross-Region Backups

You can copy backups to another AWS Region.

```
Region A
   │
   │ Backup
   ↓
AWS Backup
   │
   │ Cross-Region Copy
   ↓
Region B
```

### Why?

Useful for:

- Disaster recovery
- Regional failure protection
- Business continuity

---

### 2. Cross-Account Backups

You can copy backups between AWS accounts.

```
Account A
   │
   │ Backup
   ↓
Account B
   │
Backup Vault
```

Useful for protecting backups from problems in the original account.

---

# Point-in-Time Recovery (PITR)

AWS Backup supports **Point-in-Time Recovery (PITR)** for supported AWS services.

Instead of restoring only one specific backup, PITR can allow you to restore data to a particular point in time.

### 🧠 Exam clue

> **Restore database/data to a specific point in time**
> 

➡️ Think **PITR**

---

# On-Demand vs Scheduled Backups

AWS Backup supports both:

### On-Demand Backup

You manually start the backup.

```
User
 ↓
Start Backup
 ↓
AWS Backup
```

### Scheduled Backup

AWS Backup automatically runs according to your backup plan.

```
Backup Plan
     ↓
Schedule
     ↓
Automatic Backup
```

---

# Backup Plans

You create backup policies called **Backup Plans**.

A Backup Plan defines **when and how long your backups should be kept**.

It can specify:

### Backup Frequency

Examples:

- Every 12 hours
- Daily
- Weekly
- Monthly
- Cron expression

---

### Backup Window

Defines **when the backup should run**.

Example:

```
Backup Window
02:00 AM → 05:00 AM
```

---

### Transition to Cold Storage

You can configure when backups should move to **cold storage**.

Possible periods include:

- Never
- Days
- Weeks
- Months
- Years

---

### Retention Period

Defines **how long the backup should be retained**.

Examples:

- Always
- Days
- Weeks
- Months
- Years

---

# Tag-Based Backup Policies

AWS Backup can use **tags** to determine which resources should be backed up.

Example:

```
EC2 Instance
Tag:
Backup = Daily
```

AWS Backup can use that tag to automatically apply the appropriate backup policy.

### 🧠 Exam clue

> **Automatically backup resources based on tags**
> 

➡️ **AWS Backup + tag-based backup policy**

---

# 🔐 AWS Backup Vault Lock

This is **very important for the exam**.

**AWS Backup Vault Lock** provides a **WORM (Write Once Read Many)** state for backups stored in an AWS Backup Vault.

```
AWS Backup
     │
     ↓
Backup Vault
     │
     ↓
Vault Lock
     │
     ↓
WORM protection
```

## What does WORM mean?

**Write Once Read Many**

Once backups are protected, they cannot simply be modified or deleted according to the Vault Lock configuration.

---

# Why use Backup Vault Lock?

It provides an additional layer of protection against:

### Accidental deletion

Someone accidentally deletes important backups.

### Malicious deletion

An attacker or compromised account attempts to delete backups.

### Retention manipulation

Someone attempts to:

- Shorten the retention period
- Alter the retention period
- Delete protected backups

Vault Lock helps prevent these actions.

---

# 🚨 Important Exam Point

When **AWS Backup Vault Lock** is enabled and configured:

> **Even the root user cannot delete the protected backups.**
> 

This makes it particularly useful for protecting backups against **ransomware and malicious/accidental deletion**.

---

# 🔥 AWS Backup vs Backup Vault Lock

| Feature | AWS Backup | Backup Vault Lock |
| --- | --- | --- |
| Centralized backups | ✅ | — |
| Automated backups | ✅ | — |
| Scheduled backups | ✅ | — |
| Cross-Region backups | ✅ | — |
| Cross-account backups | ✅ | — |
| PITR for supported services | ✅ | — |
| Tag-based backup policies | ✅ | — |
| WORM protection | — | ✅ |
| Protect against backup deletion | — | ✅ |
| Protect retention settings | — | ✅ |
| Root user unable to delete protected backups | — | ✅ |

---

# 🎯 SAA-C03 Exam Quick Revision

### **AWS Backup**

> **Centralized and automated backup service for multiple AWS services.**
> 

### **Backup Plan**

> Defines **schedule + backup window + cold storage transition + retention**.
> 

### **Cross-Region**

> Protect backups against **Regional disaster**.
> 

### **Cross-Account**

> Store backups in another AWS account for additional isolation/protection.
> 

### **PITR**

> Restore supported resources to a **specific point in time**.
> 

### **Tag-Based Backup**

> Automatically apply backup policies to resources based on **tags**.
> 

### **Backup Vault Lock**

> **WORM protection** for backups.
> 

### 🚨 Memorize this:

**AWS Backup = Manage & automate backups**

**Backup Plan = When + how long**

**Cross-Region = Disaster recovery**

**Cross-Account = Account isolation**

**PITR = Restore to a point in time**

**Vault Lock = Can't easily delete/alter protected backups**
