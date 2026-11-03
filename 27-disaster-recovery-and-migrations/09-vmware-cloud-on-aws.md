# VMware Cloud on AWS — SAA-C03 Notes

## What is VMware Cloud on AWS?

**VMware Cloud on AWS** allows customers who already use **VMware vSphere** in their on-premises data center to **extend or migrate their VMware environment to AWS** while continuing to use familiar VMware software.

### 🧠 Simple idea

> **"I already use VMware on-premises. I want AWS capacity but don't want to completely change my VMware environment."**
> 

➡️ **VMware Cloud on AWS**

---

## How it works

```
Customer Data Center
        │
        │
   On-Premises
     vCenter
        │
   vSphere-based
    Environment
        │
        │ Extend / Migrate
        ↓
      AWS Cloud
        │
 VMware Cloud on AWS
        │
 ┌──────┼──────────────┐
 ↓      ↓              ↓
vSphere vSAN          NSX
        │
        ↓
   Amazon EC2
        │
        ├── Amazon S3
        ├── Amazon FSx
        ├── Amazon RDS
        └── Amazon Redshift
```

VMware Cloud on AWS uses VMware technologies such as:

- **VMware vSphere**
- **VMware vSAN**
- **VMware NSX**

and runs on **AWS infrastructure**.

---

# Main Use Cases

## 1. Migrate VMware Workloads to AWS

If an organization has applications running on **VMware vSphere** on-premises:

```
On-Premises VMware
        ↓
VMware Cloud on AWS
```

They can migrate those workloads to AWS while continuing to use the VMware environment.

### 🧠 Exam clue

> **Existing VMware vSphere workloads + migrate to AWS**
> 

➡️ **VMware Cloud on AWS**

---

# 2. Extend the Data Center to AWS

A company might have:

```
On-Premises Data Center
        │
   VMware vSphere
        │
        │
        └──────────────┐
                       ↓
                 VMware Cloud
                    on AWS
```

This lets the organization **extend its existing VMware environment into AWS**.

### Why?

They can increase capacity without immediately rebuilding applications using native AWS architectures.

---

# 3. Hybrid Cloud

VMware Cloud on AWS supports VMware workloads across:

- Private cloud
- Public cloud
- Hybrid cloud

So an organization can have VMware workloads both **on-premises and in AWS**.

---

# 4. Disaster Recovery

VMware Cloud on AWS can also be used as part of a **disaster recovery strategy**.

For example:

```
Primary
On-Premises VMware
       │
       │ Replication / DR
       ↓
AWS
VMware Cloud on AWS
       │
       ↓
Failover
```

If the on-premises environment has a disaster, workloads can be recovered in AWS.

---

# VMware Cloud on AWS + AWS Services

A major benefit is that VMware workloads can interact with AWS services.

Examples:

- **Amazon S3**
- **Amazon FSx**
- **Amazon RDS**
- **Amazon Redshift**
- **Amazon EC2**

This gives customers access to AWS capabilities while continuing to operate VMware workloads.

---

# Direct Connect

**AWS Direct Connect** can be used to provide connectivity between the customer's on-premises environment and AWS.

```
Customer Data Center
        │
   VMware vSphere
        │
        │
 Direct Connect
        │
        ↓
VMware Cloud on AWS
```

This is useful when you need a more consistent private network connection between on-premises VMware and AWS.

---

# 🔥 VMware Cloud on AWS vs Native AWS

|  | VMware Cloud on AWS | Native AWS |
| --- | --- | --- |
| Existing VMware environment | ✅ Excellent fit | Migration/rearchitecture may be needed |
| vSphere | ✅ | ❌ |
| vSAN | ✅ | ❌ |
| NSX | ✅ | ❌ |
| Migrate VMware workloads | ✅ | Possible, but may require different migration approach |
| Extend VMware environment | ✅ | ❌ |
| Hybrid VMware environment | ✅ | ❌ |
| VMware-based DR | ✅ | ❌ |
| AWS services integration | ✅ | ✅ |

---

# 🎯 SAA-C03 Exam Clues

### Question:

> A company already has a large **VMware vSphere environment** and wants to move/extend it to AWS without abandoning VMware technologies.
> 

➡️ **VMware Cloud on AWS**

---

### Question:

> A company wants to **extend its on-premises VMware data center capacity into AWS**.
> 

➡️ **VMware Cloud on AWS**

---

### Question:

> A company wants a **disaster recovery environment for its VMware workloads in AWS**.
> 

➡️ **VMware Cloud on AWS**

---

### Question:

> A company wants to keep using **vSphere, vSAN, and NSX** while benefiting from AWS infrastructure and services.
> 

➡️ **VMware Cloud on AWS**

---

## 🧠 One-Line Memory

> **VMware Cloud on AWS = Bring/extend your VMware vSphere environment to AWS while continuing to use VMware technologies.**
>
