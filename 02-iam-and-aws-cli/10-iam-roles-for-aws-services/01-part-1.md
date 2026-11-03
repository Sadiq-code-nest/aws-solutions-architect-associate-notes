# IAM Roles — AWS SAA-C03 Master Note

IAM Roles are one of the **most important AWS SAA-C03 concepts** because AWS services, EC2 instances, Lambda functions, and users often need to access other AWS resources.

The easiest way to understand IAM is:

> **User = Who is making the request**
> 
> 
> **Group = Collection of users**
> 
> **Policy = What actions are allowed/denied**
> 
> **Role = An identity that can be assumed temporarily**
> 

---

# 1. What is an IAM Role?

An **IAM Role** is an AWS identity that contains permissions but **does not have permanent credentials/passwords associated with it**.

Instead, a trusted user or AWS service **assumes the role** and receives temporary security credentials.

### Simple idea

```
IAM Role
   │
   ├── Permissions
   │      ├── s3:GetObject
   │      ├── s3:PutObject
   │      └── dynamodb:GetItem
   │
   └── Can be assumed by
          ├── EC2
          ├── Lambda
          ├── IAM User
          ├── Another AWS Account
          └── Other AWS services
```

---

# 2. Why Do We Need IAM Roles?

Imagine an EC2 instance needs to read files from S3.

### Bad approach ❌

Put an IAM user's:

```
Access Key ID
Secret Access Key
```

inside the EC2 application.

```
EC2
 ↓
Hard-coded AWS credentials
 ↓
S3
```

Problems:

- Credentials can leak.
- Credentials need rotation.
- Difficult to manage.
- Security risk.

### Correct approach ✅

Attach an IAM Role to EC2.

```
EC2
 ↓
IAM Role
 ↓
Temporary credentials
 ↓
S3
```

No access keys need to be stored inside the application.

---

# 3. IAM Role = Temporary Access

When a principal assumes a role, AWS Security Token Service (**STS**) provides temporary credentials.

Conceptually:

```
Principal
   ↓
AssumeRole
   ↓
STS
   ↓
Temporary Credentials
   ↓
AWS Resource
```

Temporary credentials consist of:

```
Access Key ID
Secret Access Key
Session Token
```

These credentials automatically expire.

---

# 4. IAM Role Has TWO Important Policies

This is extremely important for the exam.

An IAM Role generally involves:

### 1. Trust Policy

Answers:

> **WHO can assume this role?**
> 

### 2. Permissions Policy

Answers:

> **WHAT can the role do after it is assumed?**
> 

---

## Example

Suppose EC2 needs to read S3.

### Trust Policy

```
Who can assume role?

EC2
```

### Permissions Policy

```
What can role do?

s3:GetObject
```

So:

```
             IAM Role
                │
       ┌────────┴────────┐
       ↓                 ↓
 Trust Policy      Permissions Policy
       │                 │
 "EC2 can assume"   "Read S3 objects"
```

### Memory trick ⭐

> **Trust = WHO**
> 
> 
> **Permissions = WHAT**
> 

---

# 5. Example IAM Role for EC2

Suppose:

```
EC2
 ↓
Needs to read
 ↓
s3://company-reports/
```

Create:

```
EC2-S3-ReadRole
```

Attach:

```
AmazonS3ReadOnlyAccess
```

Trust relationship:

```
ec2.amazonaws.com
```

Architecture:

```
                  IAM Role
              EC2-S3-ReadRole
                     │
          ┌──────────┴──────────┐
          │                     │
     Trust Policy         Permissions
          │                     │
        EC2              s3:GetObject
          │                     │
          └──────────┬──────────┘
                     ↓
                    S3
```

---

# 6. IAM Role for EC2

This is one of the most common SAA questions.

An EC2 instance can have an **IAM instance profile**, which provides the role to applications running on the instance.

Conceptually:

```
EC2 Instance
     │
     ↓
Instance Profile
     │
     ↓
IAM Role
     │
     ↓
Permissions
     │
     ↓
AWS Services
```

### Example

An EC2 application needs:

```
S3 → GetObject
DynamoDB → PutItem
CloudWatch → PutMetricData
```

Instead of creating access keys:

```
EC2
 ↓
IAM Role
 ↓
S3
DynamoDB
CloudWatch
```

---

# 7. IAM Role for Lambda

Lambda functions can also have execution roles.

Example:

```
API Gateway
      ↓
Lambda
      ↓
IAM Execution Role
      ↓
DynamoDB
```

Suppose Lambda needs to read DynamoDB.

The Lambda execution role has:

```
dynamodb:GetItem
dynamodb:Query
```

Trust policy allows:

```
lambda.amazonaws.com
```

### Exam clue

If you see:

> "Lambda needs permission to access S3/DynamoDB/CloudWatch"
> 

Think:

> **Lambda Execution Role**
> 

---

# 8. IAM Role for Other AWS Services

Many AWS services use IAM roles.

Examples:

| Service | Typical Role |
| --- | --- |
| EC2 | IAM Role / Instance Profile |
| Lambda | Execution Role |
| ECS | Task Role / Execution Role |
| Step Functions | IAM Role |
| Glue | IAM Role |
| CodeBuild | Service Role |
| CodePipeline | Service Role |
| SageMaker | Execution Role |
| EventBridge | IAM Role when needed |
| Systems Manager | IAM-related service roles |
| CloudFormation | Service Role |

The exact role mechanism varies by service, but the exam concept is:

> **AWS service needs to access another AWS resource → usually give the service an IAM Role.**
> 

---

# 9. IAM User vs IAM Role

This distinction is critical.

| IAM User | IAM Role |
| --- | --- |
| Represents a long-term identity | Represents an assumable identity |
| Can have password | No permanent password |
| Can have access keys | Provides temporary credentials when assumed |
| Usually associated with a person/application identity | Usually used by AWS services, workloads, or temporary access |
| Long-term credentials possible | Temporary credentials |
| Example: Alice | Example: EC2-S3-Role |

### Example

Employee Alice:

```
Alice
 ↓
IAM User
```

EC2 application:

```
EC2
 ↓
IAM Role
```

---

# 10. IAM Group

An IAM Group is simply a **collection of IAM users**.

It makes permission management easier.

Example:

```
Developers Group
│
├── Alice
├── Bob
└── Charlie
```

Attach one policy:

```
Allow:
s3:GetObject
s3:PutObject
```

All developers inherit those permissions.

---

# 11. IAM User vs Group vs Role vs Policy

This is the **master distinction** you should memorize.

| Concept | What is it? | Main purpose | Example |
| --- | --- | --- | --- |
| **IAM User** | Individual identity | Represents a person/application requiring long-term identity | Alice |
| **IAM Group** | Collection of users | Manage permissions for multiple users | Developers |
| **IAM Policy** | Permission document | Defines allowed/denied actions | Allow `s3:GetObject` |
| **IAM Role** | Assumable identity | Temporary access for users/services/workloads | EC2-S3-Role |

### One-line memory

> **User = WHO**
> 
> 
> **Group = WHO together**
> 
> **Policy = WHAT**
> 
> **Role = temporary WHO + WHAT**
> 

---

# 12. IAM Policy

An IAM Policy is a JSON document defining permissions.

Example:

```
{
  "Version":"2012-10-17",
  "Statement": [
    {
      "Effect":"Allow",
      "Action":"s3:GetObject",
      "Resource":"arn:aws:s3:::company-data/*"
    }
  ]
}
```

It says:

> Allow the principal to read objects from the company-data bucket.
> 

---

# 13. Identity-Based Policy

Attached to an identity:

```
IAM User
IAM Group
IAM Role
```

Example:

```
IAM Role
   ↓
IAM Policy
   ↓
Allow s3:GetObject
```

This is called an **identity-based policy**.

---

# 14. Resource-Based Policy

A resource can also have its own policy.

Examples:

- S3 Bucket Policy
- SQS Queue Policy
- SNS Topic Policy
- KMS Key Policy
- Lambda Resource Policy

Example:

```
S3 Bucket
   ↓
Bucket Policy
   ↓
Allow another AWS account
```

This is particularly important for **cross-account access**.

---

# 15. IAM Role + Cross-Account Access

One of the most important IAM Role use cases.

Suppose:

```
Account A
Production

        ↓

Account B
Security
```

Account A has an IAM Role:

```
ProductionAuditRole
```

Account B user assumes the role.

```
Account B User
      ↓
AssumeRole
      ↓
Account A Role
      ↓
Temporary Credentials
      ↓
Account A Resources
```

This is much better than creating permanent credentials for the other account.

---

# 16. Cross-Account Role Scenario

A company has:

```
Account A = Production
Account B = Security
```

Security team needs to inspect Production resources.

Instead of creating a user in Production:

```
❌ Permanent credentials
```

Create:

```
Production-Audit-Role
```

Trust policy:

```
Account B
```

Permissions:

```
ReadOnlyAccess
```

Then:

```
Security User
      ↓
AssumeRole
      ↓
Production-Audit-Role
      ↓
Temporary Credentials
      ↓
Production Resources
```

### SAA exam clue ⭐⭐⭐⭐⭐

If the question says:

> "Allow users from another AWS account to access resources temporarily"
> 

Think:

> **Cross-account IAM Role + STS AssumeRole**
>
