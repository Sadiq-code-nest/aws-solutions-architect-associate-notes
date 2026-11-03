# IRSA — IAM Roles for Service Accounts

### Definition

**IRSA** allows Kubernetes Pods running in EKS to obtain AWS permissions through a Kubernetes ServiceAccount rather than giving permissions to the entire EC2 node.

```
Pod
 |
 | Kubernetes ServiceAccount
 v
IRSA
 |
 v
IAM Role
 |
 v
AWS Service
(S3 / DynamoDB / SQS...)
```

### Example

An application Pod needs to read S3:

```
EKS Pod
  |
  v
ServiceAccount
  |
  v
IAM Role
  |
  v
S3
```

The question mentions IRSA, but **IRSA isn't the mechanism that performs autoscaling**.

### Common confusion

| IRSA | EC2 Instance Profile |
| --- | --- |
| IAM permissions at Pod/service-account level | IAM permissions associated with EC2 instance |
| Fine-grained Pod identity | Node-level identity |
| Kubernetes workload identity | EC2 workload identity |

### Exam trigger

> **"EKS Pod needs AWS permissions without granting them to the entire node" → IRSA**
> 

---

#
