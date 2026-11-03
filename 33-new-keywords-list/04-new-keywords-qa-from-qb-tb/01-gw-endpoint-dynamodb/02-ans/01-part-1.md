# AWS SAA-C03 Post-Mortem

## 1. Restate the Scenario in Plain English

We have a GraphQL API running on **Amazon EKS with AWS Fargate**. The API needs to access a **DynamoDB table**, with **DAX** involved for fast database access.

The important requirements are:

1. **DynamoDB traffic must not go through the public internet.**
2. **DynamoDB backups must automatically be copied to another AWS account** for long-term retention / disaster recovery.
3. The solution should use AWS-managed capabilities rather than unnecessary custom infrastructure.

### Current architecture

```
Users
  │
  ▼
GraphQL API
  │
  ▼
EKS + Fargate
  │
  ▼
DAX
  │
  ▼
DynamoDB
  │
  └── Public AWS endpoint/network path
```

The architect needs to improve this:

```
                         AWS VPC
                           │
                    ┌──────▼──────┐
                    │ EKS/Fargate │
                    └──────┬──────┘
                           │
                          DAX
                           │
                           ▼
                   DynamoDB Endpoint
                           │
                    AWS private network
                           │
                      DynamoDB
```

And separately:

```
DynamoDB
    │
    ▼
AWS Backup
    │
    ▼
Another AWS Account
    │
    ▼
Long-term DR backup
```

### Actors and existing services

| Component | Role |
| --- | --- |
| GraphQL API | Application/API |
| EKS | Kubernetes platform |
| Fargate | Serverless compute for EKS pods |
| AWS SAM | Application deployment/IaC framework |
| DAX | In-memory cache for DynamoDB |
| DynamoDB | Primary NoSQL database |
| IAM authenticator | EKS authentication/RBAC integration |
| VPC endpoint | Private connectivity from VPC to AWS service |
| AWS Backup | Centralized backup/copy capability |

Notice something important:

> **EKS, Fargate, SAM, and IAM authenticator are mostly background information here.**
> 

The actual decision revolves around:

```
Private DynamoDB connectivity
+
Cross-account backup
```

---

# 2. Identify the Hidden Sub-Problems

| Symptom / Requirement | Underlying Category | Why |
| --- | --- | --- |
| Database calls shouldn't traverse public internet | **VPC endpoint / private AWS service connectivity** | Keep traffic from the VPC to DynamoDB on AWS's private network |
| DynamoDB is being accessed from VPC workloads | **DynamoDB Gateway Endpoint** | DynamoDB supports VPC gateway endpoints |
| Automatic cross-account backup | **AWS Backup cross-account backup** | AWS Backup can copy supported backups across accounts |
| Long-term retention / DR | **Centralized backup strategy** | A separate account protects backups from failures or account-level issues |
| Need route-table-based connectivity | **Gateway endpoint** | Gateway endpoints are associated with route tables |
| Need to restore DynamoDB to a previous state | **PITR** | Point-in-Time Recovery handles restoration to a selected time, but isn't the same as automated cross-account backup |

### Two keywords to separate mentally

```
PRIVATE DATABASE ACCESS
        ↓
VPC ENDPOINT
        ↓
DynamoDB GATEWAY endpoint
```

and:

```
CROSS-ACCOUNT BACKUP
        ↓
AWS BACKUP
        ↓
Copy backup to another account
```

That combination gives us **Option D**.

---

# 3. Explain Every Option in Plain Language

---

## Option A

> Create a DynamoDB gateway endpoint. Set up a NACL rule that allows outbound traffic to the DynamoDB gateway endpoint. Use built-in on-demand DynamoDB backups for cross-account backup and recovery.
> 

### What is this trying to say?

It correctly starts by creating a private VPC connection to DynamoDB:

```
EKS/Fargate
    │
    ▼
VPC Route Table
    │
    ▼
DynamoDB Gateway Endpoint
    │
    ▼
DynamoDB
```

But then it proposes using native DynamoDB on-demand backups for the **cross-account** requirement.

### Verdict: ❌ Incorrect

### Part 1: Gateway endpoint

This part is good.

DynamoDB supports a **gateway VPC endpoint**.

The route table directs DynamoDB traffic through the endpoint instead of requiring an internet gateway/NAT path.

```
Private Subnet
     │
     ▼
Route Table
     │
     ▼
Gateway Endpoint
     │
     ▼
DynamoDB
```

### Part 2: NACL

The NACL portion is unnecessary/misleading.

A gateway endpoint is associated with a **route table**.

The essential configuration is:

```
Route Table
     │
     ▼
DynamoDB Gateway Endpoint
```

You don't normally solve this by creating a NACL rule targeting the DNS name:

```
dynamodb.us-east-1.amazonaws.com
```

NACLs operate at the subnet level using IP/network rules, not AWS service DNS names as a convenient service-specific target.

### Part 3: On-demand backups

This is the bigger problem.

The requirement says:

> **automated cross-account backup**
> 

The correct AWS service here is:

> **AWS Backup**
> 

The option's wording about built-in DynamoDB on-demand backups does not provide the required automated cross-account backup workflow.

### Mini-topic: DynamoDB on-demand backup

DynamoDB supports native on-demand backups.

Conceptually:

```
DynamoDB
    │
    ▼
On-demand backup
    │
    ▼
Restore later
```

Useful for backup/restore, but don't confuse this with the AWS Backup cross-account workflow being tested here.

---

# Option B

> Create a DynamoDB interface endpoint. Use AWS Network Firewall to control outbound traffic. Integrate DynamoDB with Amazon Timestream for point-in-time recovery in another account.
> 

### What is this trying to say?

```
EKS/Fargate
     │
     ▼
Network Firewall
     │
     ▼
Interface Endpoint
     │
     ▼
DynamoDB
```

And:

```
DynamoDB
    │
    ▼
Timestream
    │
    ▼
Other account
```

### Verdict: ❌ Incorrect

There are multiple problems.

### Problem 1 — Wrong endpoint type

For DynamoDB, the standard VPC endpoint pattern is:

> **Gateway endpoint**
> 

not interface endpoint for the requirement described here.

So:

```
DynamoDB
   ↓
Gateway Endpoint ✅
```

is the expected SAA answer.

---

### Problem 2 — Network Firewall is unnecessary

The requirement isn't:

> "Inspect and filter every packet leaving the VPC."
> 

It is:

> "Prevent DynamoDB traffic from traversing the public internet."
> 

A VPC endpoint directly addresses that requirement.

Don't introduce:

```
Network Firewall
```

when:

```
VPC Endpoint
```

already solves the connectivity problem.

This violates a common SAA principle:

> **Prefer the simplest managed AWS service that directly satisfies the requirement.**
> 

---

### Problem 3 — Timestream is completely unrelated

Amazon Timestream is a time-series database.

It is not:

```
DynamoDB backup
```

and it is not:

```
DynamoDB PITR
```

So this is a strong elimination clue.

---

# Option C

> Create a DynamoDB interface endpoint. Associate the endpoint to the appropriate route table. Enable PITR to restore the DynamoDB table to a particular point in time on the same or a different AWS account.
> 

### What is this trying to say?

```
EKS/Fargate
     │
     ▼
Interface Endpoint
     │
     ▼
DynamoDB
```

and:

```
DynamoDB
     │
     ▼
PITR
     │
     ▼
Restore to selected time
```

### Verdict: ❌ Incorrect

There are two important issues.

### Problem 1 — Wrong endpoint type

Again:

```
DynamoDB
   │
   ▼
Gateway Endpoint ✅
```

is the expected answer.

The option says:

```
DynamoDB
   │
   ▼
Interface Endpoint ❌
```

---

### Problem 2 — PITR ≠ cross-account automated backup

This is an **excellent SAA trap**.

PITR means:

> "Keep continuous backups so I can restore my DynamoDB table to a specific point in time."
> 

For example:

```
Monday 10:00
Monday 11:00
Monday 12:00
Monday 13:00
       │
       ▼
PITR
       │
       ▼
Restore to Monday 11:37
```

But the requirement is:

> **automatically copy backups to another AWS account**
> 

That's a different requirement.

Think:

```
PITR
 ↓
Restore to a time
```

versus:

```
AWS Backup
 ↓
Copy backup
 ↓
Another account
```

### Important exam distinction

Don't see:

> "backup"
> 

and automatically select PITR.

Ask:

> **Do they want time-based recovery or cross-account backup copies?**
> 

Here:

**Cross-account backup → AWS Backup.**

---

# Option D — Correct

> Create a DynamoDB gateway endpoint. Associate the endpoint to the appropriate route table. Use AWS Backup to automatically copy the on-demand DynamoDB backups to another AWS account for disaster recovery.
> 

### What is this proposing?

```
                 VPC
                  │
             EKS/Fargate
                  │
                  ▼
             Route Table
                  │
                  ▼
       DynamoDB Gateway Endpoint
                  │
                  ▼
              DynamoDB
```

And:

```
             DynamoDB
                 │
                 ▼
          AWS Backup
                 │
          Backup policy
                 │
                 ▼
        Secondary AWS Account
                 │
                 ▼
          Backup Vault
```

### Verdict: ✅ Correct

It directly solves both requirements.

### Requirement #1

> Prevent database traffic from traversing the public internet.
> 

Use:

**DynamoDB Gateway VPC Endpoint**

```
VPC
 │
 ▼
Gateway Endpoint
 │
 ▼
DynamoDB
```

No Internet Gateway is required for this DynamoDB path.

---

### Requirement #2

> Automated cross-account backup.
> 

Use:

**AWS Backup**

```
DynamoDB
   │
   ▼
AWS Backup
   │
   ▼
Cross-account copy
   │
   ▼
Backup vault in another account
```

This provides a much stronger DR posture because the backup is separated from the source account.

---

# 4. Deep-Dive Every AWS Concept Touched by the Question

---

## 4.1 VPC Gateway Endpoint

### Definition

A gateway VPC endpoint provides private connectivity from a VPC to supported AWS services without requiring an Internet Gateway or NAT Gateway. **Amazon S3 and DynamoDB** are the classic AWS services associated with gateway endpoints.

```
Private Subnet
      │
      ▼
 Route Table
      │
      │ DynamoDB prefix
      ▼
Gateway Endpoint
      │
      ▼
 DynamoDB
```

### Worked example

Your Fargate workload needs to read DynamoDB:

```
Fargate Pod
    │
    ▼
DynamoDB API request
    │
    ▼
Route Table
    │
    ▼
DynamoDB Gateway Endpoint
    │
    ▼
DynamoDB
```

No NAT Gateway is necessary merely to reach DynamoDB.

### Gateway Endpoint vs Interface Endpoint

| Gateway Endpoint | Interface Endpoint |
| --- | --- |
| Uses route tables | Uses ENIs |
| No hourly endpoint charge | Hourly/data-processing charges generally apply |
| S3 and DynamoDB | Most other AWS services |
| No security group attached to endpoint | Security groups apply to endpoint ENIs |
| Routing-based | Private IP/ENI-based |
| Classic answer for DynamoDB | Not the standard answer here |

### Exam trigger

> **"Private access to S3 or DynamoDB from a VPC" → Gateway VPC Endpoint**
> 

---

# 4.2 DynamoDB Gateway Endpoint

This is simply the gateway endpoint specifically targeting DynamoDB.

```
EKS/Fargate
    │
    ▼
VPC Route Table
    │
    ▼
DynamoDB Gateway Endpoint
    │
    ▼
DynamoDB
```

### Worked example

Suppose:

```
Route table:
10.0.0.0/16 → local
DynamoDB prefix list → vpce-12345
```

When the workload calls DynamoDB, the route directs the traffic through the gateway endpoint.

### DynamoDB Gateway Endpoint vs DynamoDB Public Endpoint

| Gateway endpoint | Public endpoint |
| --- | --- |
| Private VPC connectivity | Public AWS service endpoint |
| No Internet Gateway required for this path | Internet connectivity path may be required |
| Controlled through route table | Public endpoint |
| Preferred for private VPC access | Not ideal when avoiding public network path |

### Exam trigger

> **"Fargate/EC2 in private subnet needs DynamoDB without NAT/Internet" → DynamoDB Gateway Endpoint**
> 

---

# 4.3 VPC Interface Endpoint

### Definition

An interface endpoint uses **AWS PrivateLink** and creates elastic network interfaces (ENIs) with private IP addresses in your subnets.

```
Application
     │
     ▼
Private IP
     │
     ▼
Interface Endpoint ENI
     │
     ▼
AWS Service
```

### Worked example

For a supported service such as Secrets Manager:

```
EC2
 │
 ▼
Secrets Manager Interface Endpoint
 │
 ▼
Secrets Manager
```

No public internet path is required.

### Gateway vs Interface

| Gateway | Interface |
| --- | --- |
| Route table | ENI/private IP |
| S3/DynamoDB | Most AWS services |
| No endpoint ENI | Creates ENIs |
| No SG on endpoint | Security group controls endpoint ENI traffic |
| Typically simpler/cheaper | More broadly applicable |

### Exam trigger

> **"PrivateLink / private ENI / private IP to AWS service" → Interface Endpoint**
> 

But:

> **"S3 or DynamoDB" → Think Gateway Endpoint first.**
> 

---

# 4.4 AWS Backup

### Definition

AWS Backup is a centralized managed service for creating, managing, and restoring backups across supported AWS resources. It can also support backup policies and cross-account backup copies.

```
DynamoDB
    │
    ▼
AWS Backup
    │
    ▼
Backup Vault
    │
    ├───────────────┐
    ▼               ▼
Same account    Other account
```

### Worked example

Account A:

```
Production DynamoDB
       │
       ▼
AWS Backup
       │
       ▼
Backup Vault
```

AWS Backup copies the backup to Account B:

```
Account A                  Account B
─────────                  ─────────
DynamoDB                   Backup Vault
    │                          ▲
    └──── AWS Backup ──────────┘
```

If Account A has a serious incident, the backup remains available in Account B.

### AWS Backup vs DynamoDB PITR

| AWS Backup | DynamoDB PITR |
| --- | --- |
| Backup management service | DynamoDB recovery feature |
| Backup/copy/retention management | Continuous recovery capability |
| Cross-account backup workflows | Restore to selected point in time |
| Centralized backup strategy | Granular temporal recovery |
| Strong DR/account-isolation use case | Accidental deletion/data corruption recovery |

### Exam trigger

> **"Centralized backup / cross-account backup / backup vault / backup policy" → AWS Backup**
>
