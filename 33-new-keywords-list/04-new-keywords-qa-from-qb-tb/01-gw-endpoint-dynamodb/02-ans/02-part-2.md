# 4.5 DynamoDB Point-in-Time Recovery (PITR)

### Definition

PITR continuously maintains backups that allow you to restore a DynamoDB table to a specific point within the supported recovery window.

```
DynamoDB changes
 │
 ├── 10:00
 ├── 11:00
 ├── 12:00
 └── 13:00
       │
       ▼
      PITR
       │
       ▼
Restore to 11:37
```

### Worked example

Someone accidentally deletes data at:

```
14:35
```

You can restore the table to a point before the accidental change.

### PITR vs On-demand backup

| PITR | On-demand backup |
| --- | --- |
| Continuous recovery capability | Explicit backup |
| Restore to a point in time | Restore from a backup |
| Good for accidental changes | Good for retained snapshots/backups |
| Recovery window | Backup remains according to retention strategy |

### PITR vs Cross-account backup

This is the critical distinction:

```
"Restore to 3:17 PM"
        ↓
       PITR
```

versus:

```
"Keep a copy in another account"
        ↓
    AWS Backup
```

### Exam trigger

> **"Restore DynamoDB to a specific point in time" → PITR**
> 

---

# 4.6 AWS Network Firewall

### Definition

AWS Network Firewall is a managed, stateful network firewall designed to inspect and control network traffic entering/leaving VPCs.

```
Traffic
   │
   ▼
Network Firewall
   │
 ┌─┴─┐
 │   │
Allow Deny
```

### Worked example

You might want to control outbound traffic:

```
Private subnet
      │
      ▼
Network Firewall
      │
      ├── Allowed destination
      │
      └── Blocked destination
```

### Network Firewall vs VPC Endpoint

| Network Firewall | VPC Endpoint |
| --- | --- |
| Traffic inspection/control | Private service connectivity |
| Security filtering | Connectivity |
| Stateful firewall | Routing/private access |
| Can block traffic | Provides path to supported AWS service |
| Higher complexity | Simpler for this requirement |

### Exam trigger

> **"Inspect/filter/control network traffic" → Network Firewall**
> 

Not:

> **"Just privately access DynamoDB."**
> 

---

# 4.7 Network ACL (NACL)

### Definition

A Network ACL is a stateless subnet-level firewall that controls inbound and outbound traffic using network rules.

```
Subnet
 ┌───────────────────┐
 │ NACL               │
 │                    │
 │ Allow/Deny traffic │
 │                    │
 │ EC2/Fargate        │
 └───────────────────┘
```

### Worked example

```
Internet
   │
   ▼
NACL
   │
   ├── Port 443 → allow
   └── Port 22  → deny
```

Because NACLs are **stateless**, return traffic must be explicitly allowed.

### NACL vs Security Group

| NACL | Security Group |
| --- | --- |
| Subnet level | ENI/resource level |
| Stateless | Stateful |
| Allow + deny rules | Allow rules only |
| Applies broadly to subnet | Applies to associated resources |
| Rules evaluated in order | Rules are aggregated |

### Exam trigger

> **"Subnet-level, stateless, allow/deny" → NACL**
> 

---

# 4.8 Amazon Timestream

### Definition

Amazon Timestream is a managed time-series database designed for workloads involving data points indexed by time, such as IoT metrics and operational telemetry.

```
IoT / Sensors
     │
     ▼
Timestream
     │
     ├── timestamp
     ├── temperature
     └── pressure
```

### Worked example

```
10:00 → CPU = 50%
10:01 → CPU = 52%
10:02 → CPU = 55%
```

That's a time-series workload.

It is **not a DynamoDB backup mechanism**.

### Timestream vs DynamoDB

| Timestream | DynamoDB |
| --- | --- |
| Time-series data | NoSQL key-value/document |
| Time-based analytics | General application data |
| IoT/metrics | User/application data |
| Not a backup service | Primary database |

### Exam trigger

> **"Time-series / IoT telemetry / metrics over time" → Timestream**
> 

---

# 4.9 EKS

### Definition

Amazon EKS is AWS's managed Kubernetes service. AWS manages the Kubernetes control plane while you run workloads on Kubernetes infrastructure such as EC2 or Fargate.

```
             EKS
      ┌───────────────┐
      │ Kubernetes    │
      │ Control Plane │
      └───────┬───────┘
              │
              ▼
          Kubernetes
           workloads
```

### Worked example

```
GraphQL API
    │
    ▼
Kubernetes Pod
    │
    ▼
DynamoDB
```

### EKS vs ECS

| EKS | ECS |
| --- | --- |
| Kubernetes | AWS-native container orchestrator |
| Kubernetes APIs/ecosystem | Simpler AWS integration |
| Pods | ECS tasks |
| Kubernetes expertise useful | Generally simpler operational model |

### Exam trigger

> **"Kubernetes / pods / kubectl" → EKS**
> 

---

# 4.10 AWS Fargate

### Definition

Fargate is a serverless compute engine for containers. You don't manage the underlying EC2 servers.

```
EKS
 │
 ▼
Pod
 │
 ▼
Fargate
 │
 └── AWS manages underlying compute
```

### Worked example

Instead of:

```
EKS
 ↓
EC2 worker nodes
 ↓
Pods
```

you can use:

```
EKS
 ↓
Fargate
 ↓
Pods
```

### Fargate vs EC2 launch type

| Fargate | EC2 |
| --- | --- |
| Serverless container compute | You manage EC2 worker nodes |
| No node management | Node patching/capacity |
| Pay for requested resources | Pay for EC2 capacity |
| Simpler operations | More control |

### Exam trigger

> **"Containers without managing servers" → Fargate**
> 

---

# 4.11 AWS SAM

### Definition

AWS SAM (Serverless Application Model) is an IaC/deployment framework that simplifies defining and deploying serverless AWS applications.

```
SAM Template
     │
     ▼
AWS resources
     │
     ├── Lambda
     ├── API Gateway
     └── other resources
```

### Important trap in this question

The question mentions SAM, but that does **not** mean SAM is relevant to the answer.

This is common in SAA questions.

> **Don't force every service mentioned in the scenario into the solution.**
> 

### SAM vs CloudFormation

| SAM | CloudFormation |
| --- | --- |
| Serverless-focused abstraction | General AWS IaC |
| Simplifies serverless definitions | Broader resource support |
| Transforms SAM templates into CloudFormation | Native infrastructure orchestration |

### Exam trigger

> **"Serverless application template/deployment" → SAM**
> 

---

# 4.12 DAX — DynamoDB Accelerator

### Definition

DAX is an in-memory caching service designed specifically for DynamoDB. It can significantly reduce read latency for suitable workloads by serving frequently accessed data from memory.

```
Application
    │
    ▼
   DAX
    │
    ├── Cache hit → return quickly
    │
    └── Cache miss
           │
           ▼
       DynamoDB
```

### Worked example

Suppose:

```
Product ID = 100
```

is requested thousands of times.

Instead of every request going directly to DynamoDB:

```
App → DAX → cached product
```

### DAX vs ElastiCache

| DAX | ElastiCache |
| --- | --- |
| Built specifically for DynamoDB | General-purpose cache |
| DynamoDB API compatible | Redis/Memcached |
| Minimal application changes for suitable workloads | Application typically integrates with cache |
| DynamoDB acceleration | General caching |

### Important point for this question

DAX **doesn't eliminate the need to secure the underlying DynamoDB connectivity**.

The architecture still needs appropriate private networking.

### Exam trigger

> **"DynamoDB-compatible in-memory cache / microsecond reads" → DAX**
> 

---

# 4.13 IAM Authenticator for Kubernetes

### Definition

The AWS IAM authenticator integrates AWS IAM identities with Kubernetes authentication/authorization mechanisms, allowing AWS IAM principals to access an EKS cluster according to configured Kubernetes permissions.

Simplified:

```
AWS IAM User/Role
       │
       ▼
IAM authentication
       │
       ▼
EKS
       │
       ▼
Kubernetes RBAC
```

### Worked example

An administrator assumes an IAM role:

```
IAM Role
   ↓
EKS authentication
   ↓
Kubernetes permissions
   ↓
kubectl access
```

### Important point

This concerns:

> **Who can access the Kubernetes cluster.**
> 

It does **not** provide:

> **private DynamoDB network connectivity.**
> 

That's why this information is largely a distractor in this question.

### Exam trigger

> **"IAM identities accessing EKS/Kubernetes" → IAM/EKS authentication + Kubernetes RBAC**
> 

---

# 5. Master Comparison Table

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **Private access from VPC to DynamoDB** | **DynamoDB Gateway VPC Endpoint** |
| **Private access from VPC to S3** | **S3 Gateway VPC Endpoint** |
| **S3/DynamoDB + VPC endpoint** | **Gateway Endpoint** |
| **PrivateLink / ENI / private IP to AWS service** | **Interface Endpoint** |
| **Cross-account backup** | **AWS Backup** |
| **Centralized backup policies/vaults** | **AWS Backup** |
| **Restore DynamoDB to a specific point in time** | **DynamoDB PITR** |
| **Time-series / IoT metrics** | **Timestream** |
| **Inspect/filter network traffic** | **Network Firewall** |
| **Subnet-level stateless filtering** | **NACL** |
| **Kubernetes** | **EKS** |
| **Containers without managing servers** | **Fargate** |
| **Serverless IaC/deployment** | **AWS SAM** |
| **DynamoDB in-memory cache** | **DAX** |
| **IAM + Kubernetes authentication/RBAC** | **EKS IAM authentication** |
| **DynamoDB query acceleration/cache** | **DAX** |
| **Route-table-based AWS service connectivity** | **Gateway VPC Endpoint** |
| **Separate AWS account for DR backups** | **AWS Backup cross-account copy** |
| **Avoid Internet Gateway/NAT for DynamoDB** | **DynamoDB Gateway Endpoint** |

---

# 6. Side-by-Side Option Analysis Table

| Option | Underlying Idea | Good? | One-line reason |
| --- | --- | --- | --- |
| **A** | Gateway endpoint + NACL + native DynamoDB backup | ❌ | Endpoint is right, but NACL approach is unnecessary and native backup doesn't satisfy the intended automated cross-account backup requirement |
| **B** | Interface endpoint + Network Firewall + Timestream | ❌ | Wrong endpoint pattern and Timestream is not a DynamoDB backup/DR service |
| **C** | Interface endpoint + PITR | ❌ | Wrong endpoint type; PITR provides point-in-time restoration, not the requested cross-account backup-copy solution |
| **D** | Gateway endpoint + route table + AWS Backup cross-account copy | ✅ | Directly satisfies private DynamoDB access and automated cross-account backup |
