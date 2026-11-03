# 17. AWS STS and IAM Roles

**AWS Security Token Service (STS)** provides temporary credentials.

Common STS APIs include:

```
AssumeRole
AssumeRoleWithWebIdentity
GetSessionToken
```

For standard IAM role assumption:

```
STS AssumeRole
```

Flow:

```
User / Service
      ↓
AssumeRole
      ↓
AWS STS
      ↓
Temporary Credentials
      ↓
AWS Resources
```

---

# 18. Role Chaining

A principal can assume a role and potentially assume another role.

Example:

```
User
 ↓
Role A
 ↓
Role B
 ↓
AWS Resource
```

This is called **role chaining**.

For SAA-C03, recognize the concept, but you generally don't need to memorize implementation details.

---

# 19. IAM Role for EC2 vs Access Keys

### Bad architecture ❌

```
EC2
 ↓
Hard-coded Access Key
 ↓
S3
```

### Good architecture ✅

```
EC2
 ↓
IAM Role
 ↓
Temporary Credentials
 ↓
S3
```

### Exam clue

If the question says:

> "An application running on EC2 needs access to S3 without storing credentials."
> 

Answer direction:

> **IAM Role attached to EC2**
> 

---

# 20. IAM Role for Lambda vs IAM User

Suppose Lambda needs to write logs to CloudWatch.

Don't create:

```
IAM User
 ↓
Access Key
 ↓
Lambda
```

Use:

```
Lambda
 ↓
Execution Role
 ↓
CloudWatch Logs
```

---

# 21. IAM Role for ECS

ECS introduces an important distinction.

There are commonly **two roles** to understand:

### ECS Task Role

Permissions for:

> **The application/container itself**
> 

Example:

```
Container
 ↓
Task Role
 ↓
S3
DynamoDB
Secrets Manager
```

### ECS Task Execution Role

Permissions required by:

> **ECS/Fargate infrastructure to run the task**
> 

For example:

```
ECS
 ↓
Execution Role
 ↓
Pull image from ECR
Send logs to CloudWatch
Retrieve certain startup configuration
```

### Exam memory ⭐

> **Task Role = application permissions**
> 

> **Task Execution Role = ECS/Fargate startup/infrastructure permissions**
> 

---

# 22. IAM Role for S3 Access

Suppose EC2 needs:

```
S3:GetObject
```

Use:

```
EC2
 ↓
IAM Role
 ↓
IAM Policy
 ↓
S3
```

Do **not** confuse this with an S3 Bucket Policy.

### IAM Role

Controls what the EC2 identity can do.

### Bucket Policy

Controls who the bucket allows/denies.

---

# 23. Role vs Resource-Based Policy

This is a common exam confusion.

Suppose Account A owns an S3 bucket and Account B needs access.

### Option 1 — Bucket Policy

```
Account A S3 Bucket
       ↓
Bucket Policy
       ↓
Allow Account B
```

### Option 2 — Cross-account Role

```
Account B User
       ↓
AssumeRole
       ↓
Account A IAM Role
       ↓
S3
```

Both are possible depending on the requirements.

### Typical clue for Role

If the question emphasizes:

- temporary access
- cross-account role assumption
- centralized permissions
- users assuming a role
- STS

Think:

> **IAM Role**
> 

---

# 24. Service Role

A **service role** is an IAM role that an AWS service assumes to perform actions on your behalf.

Example:

```
AWS CodeBuild
      ↓
Service Role
      ↓
S3 / CloudWatch / ECR
```

The service uses the role's permissions.

---

# 25. Instance Profile

For EC2, you'll encounter **instance profiles**.

Think of it simply as the mechanism that associates an IAM role with an EC2 instance.

```
EC2
 ↓
Instance Profile
 ↓
IAM Role
 ↓
Permissions
```

### Exam memory

> **EC2 + IAM Role → Instance Profile**
> 

You normally don't manually manage the credentials.

---

# 26. IAM Role and Least Privilege

IAM Roles should follow the **principle of least privilege**.

Suppose Lambda only needs:

```
s3:GetObject
```

Don't give:

```
AdministratorAccess
```

Instead:

```
Allow s3:GetObject
```

only on the required bucket/object.

### Bad

```
Lambda
 ↓
AdministratorAccess
```

### Good

```
Lambda
 ↓
Role
 ↓
s3:GetObject
 ↓
specific bucket
```

---

# 27. Role Session

When a role is assumed:

```
User
 ↓
AssumeRole
 ↓
Role Session
 ↓
Temporary Credentials
```

The permissions during that session come from the role and applicable policies.

---

# 28. Role ARN

An IAM Role has an ARN.

Example:

```
arn:aws:iam::123456789012:role/EC2-S3-Role
```

You may see role ARNs in:

- Trust policies
- IAM policies
- Cross-account configurations
- AWS services

---

# 29. Trust Policy — Deep Understanding

Example:

```
{
  "Version":"2012-10-17",
  "Statement": [
    {
      "Effect":"Allow",
      "Principal": {
        "Service":"ec2.amazonaws.com"
      },
      "Action":"sts:AssumeRole"
    }
  ]
}
```

Meaning:

```
Principal = EC2
Action = AssumeRole
```

Therefore:

> EC2 is trusted to assume this role.
> 

---

# 30. Permissions Policy — Deep Understanding

Example:

```
{
  "Version":"2012-10-17",
  "Statement": [
    {
      "Effect":"Allow",
      "Action": ["s3:GetObject"
      ],
      "Resource":"arn:aws:s3:::my-data/*"
    }
  ]
}
```

Meaning:

```
Role
 ↓
Can perform
 ↓
s3:GetObject
 ↓
on my-data objects
```

---

# 31. Trust Policy vs Permissions Policy

|  | Trust Policy | Permissions Policy |
| --- | --- | --- |
| Question | **Who can assume the role?** | **What can the role do?** |
| Attached to | IAM Role | User/Group/Role/etc. |
| Main element | Principal | Action |
| Common action | `sts:AssumeRole` | `s3:GetObject`, `ec2:DescribeInstances` |
| Example | EC2 can assume role | Role can read S3 |

### ⭐ Absolute exam memory

```
TRUST POLICY
     ↓
WHO?

PERMISSIONS POLICY
     ↓
WHAT?
```

---

# 32. IAM Role vs IAM Group

This is probably one of your biggest potential exam confusions.

### Group

Used to organize **IAM users**.

```
Developers Group
├── Alice
├── Bob
└── Charlie
```

Permissions:

```
S3 read/write
```

### Role

Used for **temporary/assumable access**.

```
EC2
 ↓
Role
 ↓
S3
```

### Therefore

> Don't put EC2/Lambda into an IAM Group.
> 

Groups contain **users**, not AWS services.

---

# 33. IAM Role vs IAM Policy

They aren't alternatives.

They work together.

```
IAM Role
    +
IAM Policy
    ↓
Permissions
```

Example:

```
EC2
 ↓
EC2-S3-Role
 ↓
S3ReadPolicy
 ↓
s3:GetObject
```

Think:

> **Role = identity that can be assumed**
> 

> **Policy = permissions attached to that identity**
> 

---

# 34. IAM Role vs Access Key

|  | IAM Role | Access Keys |
| --- | --- | --- |
| Credential type | Temporary | Long-term |
| Expiration | Yes | Normally doesn't automatically expire |
| Best for AWS workloads | ✅ | ❌ |
| EC2 application | ✅ | Avoid |
| Lambda | ✅ | Avoid |
| Cross-account temporary access | ✅ | ❌ |
| Credential rotation burden | Low | Higher |
| Security | Better | Riskier if exposed |

### SAA clue

> "Avoid storing credentials in EC2"
> 

→ **IAM Role**

---

# 35. IAM Role + Secrets Manager

Don't confuse these.

Suppose an application needs a database password.

IAM Role:

```
EC2
 ↓
IAM Role
 ↓
secretsmanager:GetSecretValue
 ↓
Secrets Manager
 ↓
Database password
```

The role gives permission to retrieve the secret.

The role **doesn't store the password**.

---

# 36. IAM Role + Parameter Store

Similarly:

```
Lambda
 ↓
IAM Role
 ↓
ssm:GetParameter
 ↓
Systems Manager Parameter Store
```

Again:

> Role = permission to access the parameter.
> 

---

# 37. IAM Role + KMS

Suppose Lambda needs to decrypt an encrypted object.

```
Lambda
 ↓
IAM Role
 ↓
kms:Decrypt
 ↓
KMS Key
```

But remember:

> **KMS key policies also matter.**
> 

IAM permission alone isn't always the whole authorization story with KMS.

---

# 38. IAM Role + S3 Encryption

Example:

```
EC2
 ↓
IAM Role
 ↓
s3:GetObject
 ↓
S3
 ↓
KMS
```

If SSE-KMS is involved, appropriate KMS permissions/key policy configuration may also be required.

---

# 39. IAM Role for Automation

AWS automation services commonly use roles.

Example:

```
EventBridge
     ↓
IAM Role
     ↓
Lambda / ECS / Step Functions / other target
```

The role allows the service to perform the required action.

---

# 40. IAM Role + EventBridge Scenario

Suppose:

> Every night at 10 PM, EventBridge should start an EC2 instance.
> 

Architecture:

```
EventBridge
    ↓
IAM Role
    ↓
ec2:StartInstances
    ↓
EC2
```

The role allows EventBridge to perform the action.

---

# 41. IAM Role + CloudFormation

CloudFormation can use an IAM service role.

```
CloudFormation
      ↓
Service Role
      ↓
Create/Modify AWS Resources
```

This can be useful when you want CloudFormation to have controlled permissions independent of the user's permissions.

---

# 42. IAM Role + Step Functions

Step Functions can assume a role to call AWS services.

Example:

```
Step Functions
      ↓
IAM Role
      ↓
Lambda
      ↓
DynamoDB
```

The role gives Step Functions the required permissions.

---

# 43. IAM Role + OIDC / Web Identity

Modern applications can obtain temporary AWS credentials using web identity federation.

For example:

```
External Identity Provider
          ↓
OIDC
          ↓
STS
          ↓
IAM Role
          ↓
Temporary Credentials
          ↓
AWS Resources
```

A major example is:

### Kubernetes / EKS

Pods can use **IAM Roles for Service Accounts (IRSA)** or newer EKS Pod Identity mechanisms depending on the setup.

The important SAA concept:

> Workloads can obtain temporary AWS permissions without embedding long-term AWS access keys.
> 

---

# 44. IAM Role + Cognito

Amazon Cognito can also provide temporary AWS credentials through IAM roles for authenticated/appropriate users.

Conceptually:

```
Mobile/Web User
      ↓
Cognito
      ↓
IAM Role
      ↓
Temporary AWS Credentials
      ↓
AWS Resources
```

Example:

A mobile application allows users to upload images directly to S3.

```
Mobile App
     ↓
Cognito Authentication
     ↓
IAM Role
     ↓
Temporary Credentials
     ↓
S3
```

---

# 45. Important: Role Does NOT Automatically Give Access

Creating a role isn't enough.

You need:

```
Trust Policy
+
Permissions Policy
```

Example:

```
Role created
     │
     ├── Trust says Lambda can assume it
     │
     └── Permissions say Lambda can access DynamoDB
```

Both matter.

---

# 46. Common SAA-C03 Scenarios

## Scenario 1 — EC2 → S3

> EC2 needs to read S3 objects securely without storing credentials.
> 

### Think:

**IAM Role for EC2**

```
EC2 → IAM Role → S3
```

---

## Scenario 2 — Lambda → DynamoDB

> Lambda needs to write records to DynamoDB.
> 

### Think:

**Lambda Execution Role**

```
Lambda → IAM Role → DynamoDB
```

---

## Scenario 3 — Users from another account

> Account B users need temporary access to Account A.
> 

### Think:

**Cross-account IAM Role + STS AssumeRole**

```
Account B User
      ↓
AssumeRole
      ↓
Account A Role
```

---

## Scenario 4 — Many developers

> 50 developers need the same permissions.
> 

### Think:

**IAM Group**

```
Developers Group
 ↓
Common Policy
 ↓
50 Users
```

---

## Scenario 5 — Store permissions

> Define exactly what API calls are allowed.
> 

### Think:

**IAM Policy**

---

## Scenario 6 — Lambda needs database password

### Think:

```
Lambda
 ↓
IAM Role
 ↓
Secrets Manager
```

---

## Scenario 7 — EC2 needs SSM Parameter

### Think:

```
EC2
 ↓
IAM Role
 ↓
Parameter Store
```

---

# 47. The BIG IAM Decision Table

| Requirement | Use |
| --- | --- |
| Represent a person | **IAM User** |
| Group several users | **IAM Group** |
| Define permissions | **IAM Policy** |
| EC2 needs AWS access | **IAM Role** |
| Lambda needs AWS access | **IAM Execution Role** |
| ECS application needs AWS access | **ECS Task Role** |
| ECS/Fargate needs startup permissions | **Task Execution Role** |
| AWS service needs to perform actions | **Service Role** |
| Temporary access | **IAM Role + STS** |
| Cross-account temporary access | **Cross-account IAM Role** |
| EC2 role association | **Instance Profile** |
| Store secret | **Secrets Manager** |
| Store configuration/parameters | **Parameter Store** |
| Encrypt/decrypt | **KMS** |
| Define who can assume role | **Trust Policy** |
| Define what role can do | **Permissions Policy** |

---

# 48. Most Important IAM Confusions

| Confusion | Correct distinction |
| --- | --- |
| User vs Role | User is a long-term identity; role is assumable and commonly provides temporary credentials |
| Group vs Role | Group organizes users; role is assumed |
| Role vs Policy | Role is an identity; policy defines permissions |
| Trust Policy vs Permissions Policy | Trust = who can assume; permissions = what it can do |
| IAM Policy vs S3 Bucket Policy | IAM policy belongs to identity; bucket policy belongs to S3 resource |
| Role vs Access Key | Role uses temporary credentials; access keys are long-term credentials |
| Secrets Manager vs IAM Role | Secrets Manager stores secrets; role grants permission to retrieve them |
| Parameter Store vs IAM Role | Parameter Store stores parameters; role grants access |
| KMS vs IAM Role | KMS performs cryptographic operations; role grants permission to use them |
| Instance Profile vs IAM Role | Instance profile associates the role with EC2 |

---

# 49. Exam "Keyword → Answer" Cheat Sheet

| If you see this wording... | Think... |
| --- | --- |
| "EC2 needs access to S3" | **IAM Role** |
| "Lambda needs DynamoDB access" | **Lambda Execution Role** |
| "Avoid storing credentials on EC2" | **IAM Role** |
| "Temporary credentials" | **STS / IAM Role** |
| "Cross-account access" | **IAM Role / AssumeRole** |
| "Users from another account" | **Cross-account Role** |
| "Many users need same permissions" | **IAM Group** |
| "Define allowed API actions" | **IAM Policy** |
| "Who can assume the role?" | **Trust Policy** |
| "What can role do?" | **Permissions Policy** |
| "EC2 IAM role association" | **Instance Profile** |
| "Application needs password" | **Secrets Manager** |
| "Application needs configuration" | **Parameter Store** |
| "Encrypt/decrypt data" | **KMS** |
| "AWS service performs action on your behalf" | **Service Role** |
| "No permanent AWS credentials" | **IAM Role + temporary credentials** |

---

# 50. Final Mental Model 🧠

Don't memorize IAM as dozens of separate things. Remember this picture:

```
                         IAM
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
       USER             GROUP             ROLE
        │                 │                 │
      Person        Collection of       Assumable
                     users              identity
                                          │
                                          ↓
                                  Temporary Credentials
                                          │
                                          ↓
                                     AWS STS
                                          │
                                          ↓
                                   AWS Resources
```

And policies sit around the identities:

```
                     IAM POLICY
                         │
                  "WHAT CAN YOU DO?"
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
        USER           GROUP           ROLE
                                         │
                                         ↓
                                  AWS Resources
```

For a role specifically:

```
                    IAM ROLE
                       │
             ┌─────────┴─────────┐
             ↓                   ↓
       TRUST POLICY       PERMISSIONS POLICY
             │                   │
        "WHO can assume?"    "WHAT can it do?"
             │                   │
             ↓                   ↓
        EC2 / Lambda       S3 / DynamoDB /
        User / Account     KMS / etc.
```

## ⭐ The 5 lines to memorize for SAA-C03

> **IAM User → person/long-term identity**
> 

> **IAM Group → collection of users**
> 

> **IAM Policy → permissions**
> 

> **IAM Role → assumable identity + temporary credentials**
> 

> **Trust Policy = WHO can assume the role; Permissions Policy = WHAT the role can do**
> 

If an AWS workload such as **EC2, Lambda, ECS, or another AWS service needs to access another AWS service, your first thought should usually be: *IAM Role, not hard-coded access keys.***
