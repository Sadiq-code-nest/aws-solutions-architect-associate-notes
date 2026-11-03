# 7. Clue-by-Clue Post-Mortem

### "GraphQL API"

Think:

```
Application/API layer
```

No major impact on the answer.

---

### "Amazon EKS cluster"

Think:

```
Kubernetes workload
```

The application is running inside a VPC environment.

---

### "AWS Fargate launch type"

Think:

```
Containers without managing worker servers
```

Still not the main decision.

---

### "deployed using AWS SAM"

This is mostly background information.

Don't assume SAM is the solution just because it appears.

---

### "connected to an Amazon DynamoDB table"

🔥 Now pay attention.

We need:

```
Private DynamoDB connectivity
```

---

### "DAX as its data store"

Important correction mentally:

DAX is a **cache in front of DynamoDB**, not a replacement database in the normal architectural sense.

```
Application
    ↓
   DAX
    ↓
DynamoDB
```

---

### "Both resources are hosted in the us-east-1 region"

This tells you the resources are Regional and establishes the DynamoDB regional endpoint context.

It doesn't imply multi-Region replication is required.

---

### "IAM authenticator for Kubernetes"

This is about:

```
Authentication + RBAC
```

Not networking.

Don't get distracted.

---

### "improve network security"

🔥 Think:

```
Private connectivity
+
VPC endpoint
```

---

### "preventing database calls from traversing the public internet"

🔥🔥 Strongest networking clue.

Immediately think:

```
VPC
 ↓
VPC Endpoint
 ↓
DynamoDB
```

And because the service is DynamoDB:

```
DynamoDB
 ↓
Gateway Endpoint
```

This strongly favors **A/D** over B/C.

---

### "automated cross-account backup"

🔥🔥 Strongest backup clue.

Think:

```
AWS Backup
   ↓
Cross-account copy
```

This eliminates:

- B → Timestream
- C → PITR
- A → native on-demand backup as described

Now we're left with:

# D

---

### "long-term retention"

Think:

```
Backup management
+
Retention
+
Separate account
```

Again, AWS Backup is a strong fit.

---

### "for disaster recovery"

A separate account gives additional isolation.

Conceptually:

```
Production Account
      │
      ▼
   AWS Backup
      │
      ▼
DR/Backup Account
```

If the production account has a major problem, the backup isn't sitting exclusively there.

---

# 8. Why the Tempting Wrong Answers Are Tempting

## Trap #1 — Option C: PITR

This is the **most tempting**.

The question says:

> "backup"
> 

and Option C says:

> **Point-in-Time Recovery**
> 

A rushed reader thinks:

> "Backup + recovery = PITR."
> 

But the actual requirement is more specific:

> **"cross-account backup."**
> 

PITR answers:

> **"When do I want to restore the database to?"**
> 

AWS Backup answers:

> **"How do I centrally manage/copy/retain backups, including cross-account copies?"**
> 

### Memory:

```
PITR
= WHEN do I restore?

AWS Backup
= WHERE/HOW do I manage and copy backups?
```

---

## Trap #2 — Option B: Interface Endpoint

Interface endpoints are extremely common in AWS questions.

You may learn:

> "Private AWS service access = Interface Endpoint"
> 

and automatically choose it.

But SAA has a critical exception:

```
S3 + DynamoDB
       ↓
Gateway Endpoint
```

while:

```
Most other AWS services
       ↓
Interface Endpoint
```

Then B adds **Network Firewall**, making it sound more secure.

But:

> More security components ≠ better answer.
> 

The question asks for a solution to a specific requirement.

If a gateway endpoint solves the private connectivity requirement directly, adding Network Firewall is unnecessary complexity.

---

# 9. Final Answer Recap + One-Line Memory Trick

# ✅ Correct Answer: D

> **Create a DynamoDB gateway endpoint and associate it with the appropriate route table. Use AWS Backup to automatically copy the on-demand DynamoDB backups to another AWS account for disaster recovery.**
> 

### Final architecture

```
                     AWS VPC
                       │
                 ┌─────▼─────┐
                 │ EKS/Fargate│
                 └─────┬─────┘
                       │
                      DAX
                       │
                       ▼
                Route Table
                       │
                       ▼
          DynamoDB Gateway Endpoint
                       │
                       ▼
                   DynamoDB
                       │
                       │
                       ▼
                  AWS Backup
                       │
                       │ cross-account copy
                       ▼
              ┌─────────────────┐
              │ Backup Account  │
              │ Backup Vault    │
              └─────────────────┘
```

## 🧠 Memory Tricks

> **"DynamoDB/S3 + private VPC access → Gateway Endpoint."**
> 

> **"Cross-account backup → AWS Backup. Restore to a specific time → PITR."**
> 

And the big one:

> **GSI is for querying. PITR is for time-based recovery. AWS Backup is for backup management/copying. Gateway Endpoint is for private DynamoDB access.**
> 

---

# 10. Bonus: Related Exam Traps to Watch For

### 1. "Private EC2 access to Secrets Manager"

Don't automatically choose Gateway Endpoint.

Think:

```
Secrets Manager
      ↓
Interface Endpoint
```

because Secrets Manager uses an interface endpoint.

---

### 2. "Restore DynamoDB after accidental deletion to yesterday at 3 PM"

Now the answer changes:

```
DynamoDB
   ↓
PITR
```

because the clue is a **specific point in time**.

---

### 3. "Need to inspect and block malicious outbound traffic"

Now **Network Firewall** becomes relevant:

```
VPC
 ↓
Network Firewall
 ↓
Internet/AWS destinations
```

The requirement is traffic inspection, not simply private DynamoDB access.

---

### 4. "Need to query DynamoDB using Email instead of UserID"

Now think:

```
DynamoDB
   ↓
GSI
```

Not Global Tables, PITR, or AWS Backup.

---

### 5. "Need a cache to accelerate DynamoDB reads"

Now:

```
Application
    ↓
DAX
    ↓
DynamoDB
```

The question is about **database read latency**, not network privacy.

---

# 🔥 FINAL FLASHCARD — MASTER COMPARISON

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **Private DynamoDB from VPC** | **DynamoDB Gateway Endpoint** |
| **Private S3 from VPC** | **S3 Gateway Endpoint** |
| **S3/DynamoDB + VPC endpoint** | **Gateway Endpoint** |
| **PrivateLink / ENI / private IP** | **Interface Endpoint** |
| **Cross-account backup** | **AWS Backup** |
| **Backup vault / backup policy / retention** | **AWS Backup** |
| **Restore DynamoDB to 3:15 PM yesterday** | **PITR** |
| **Point-in-time recovery** | **DynamoDB PITR** |
| **Time-series / IoT data** | **Timestream** |
| **Inspect/block network traffic** | **Network Firewall** |
| **Subnet-level + stateless firewall** | **NACL** |
| **Kubernetes** | **EKS** |
| **Serverless container compute** | **Fargate** |
| **Serverless application IaC** | **SAM** |
| **DynamoDB caching** | **DAX** |
| **IAM authentication into Kubernetes** | **EKS IAM authentication/RBAC** |
| **DynamoDB alternative query key** | **GSI** |
| **DynamoDB across Regions** | **Global Tables** |
| **Private DynamoDB + cross-account backup** | **Gateway Endpoint + AWS Backup** |
| **Specific recovery time** | **PITR** |
| **Backup copied to another account** | **AWS Backup** |
| **Avoid NAT/Internet Gateway for DynamoDB** | **DynamoDB Gateway Endpoint** |

## 🚀 The 5-second SAA decision for this question

```
DynamoDB
   │
   ├── Need PRIVATE VPC access?
   │        ↓
   │   Gateway Endpoint
   │
   └── Need CROSS-ACCOUNT BACKUP?
            ↓
        AWS Backup
```

**Therefore: Option D.**
