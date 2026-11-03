# EC2 Part 7 — Security + IAM Integration

This part connects **EC2 networking security, IAM permissions, instance credentials, Systems Manager, metadata, and encryption**.

The biggest exam skill is recognizing that these controls operate at **different layers**:

> **Security Group = network traffic control**
> 
> 
> **IAM Role = AWS API permissions**
> 
> **IMDS = obtain temporary instance credentials/metadata**
> 
> **SSM = manage the instance without traditional SSH**
> 
> **KMS/EBS encryption = protect data**
> 

---

# 1. Security Groups ⭐⭐⭐⭐⭐

A Security Group (SG) acts as a **virtual firewall attached to an EC2 network interface**.

It controls network traffic based on things such as:

- protocol
- port
- source/destination
- security group references
- IP ranges

Example:

```
Internet
   │
   │ TCP 443
   ▼
Security Group
   │
   ▼
EC2
```

If the SG doesn't allow the traffic, the connection is blocked.

---

# 2. Security Groups Are Stateful ⭐⭐⭐⭐⭐

This is one of the most important SAA-C03 facts.

Suppose the client sends:

```
Client
  │
  │ TCP 443
  ▼
EC2
```

The SG allows the inbound request.

The response traffic is automatically allowed because the SG is **stateful**.

You don't need a separate outbound rule specifically permitting the response.

### Memory trick

> **Security Group = STATEFUL**
> 

---

# 3. Security Group Rules Are Allow Rules

Security Groups support:

> **Allow**
> 

They don't have explicit deny rules.

Example:

```
Inbound:
TCP 443 → ALLOW

TCP 22 → no rule
```

Port 22 is therefore not allowed.

### Exam trap

If the question asks for:

> **explicit deny based on IP address**
> 

think about a **Network ACL**, not a Security Group.

---

# 4. Security Groups vs NACL

You should already know this from the VPC material, but it is critical for EC2 security.

| Feature | Security Group | Network ACL |
| --- | --- | --- |
| Attached to | ENI/instance | Subnet |
| Stateful | ✅ | ❌ Stateless |
| Allow rules | ✅ | ✅ |
| Deny rules | ❌ | ✅ |
| Rule evaluation | All applicable rules | Ordered rules |
| Typical use | Instance-level firewall | Subnet-level traffic filter |

### Memory

> **SG = instance firewall**
> 
> 
> **NACL = subnet firewall**
> 

---

# 5. Security Group Referencing ⭐⭐⭐⭐⭐

One of the best EC2 patterns is allowing traffic from another Security Group instead of hard-coding IP addresses.

Example:

```
Internet
   │
   ▼
ALB
SG: ALB-SG
   │
   ▼
EC2
SG: APP-SG
```

APP-SG:

```
Inbound TCP 8080
Source = ALB-SG
```

Meaning:

> EC2 accepts application traffic from instances/interfaces associated with the ALB Security Group.
> 

This is better than:

```
Source = 10.0.2.15
```

because the backend instances can change IP addresses.

---

# 6. Classic 3-Tier Security Group Pattern ⭐⭐⭐⭐⭐

A very common SAA architecture:

```
Internet
   │
   ▼
ALB
[ALB-SG]
   │
   │ 443/8080
   ▼
EC2
[APP-SG]
   │
   │ 3306
   ▼
RDS
[DB-SG]
```

Rules:

### ALB-SG

Allow:

```
443 from Internet
```

### APP-SG

Allow:

```
Application port
from ALB-SG
```

### DB-SG

Allow:

```
3306
from APP-SG
```

Notice the important security principle:

> **Each layer trusts only the layer immediately above it.**
> 

---

# 7. Why Not Open Everything?

Bad:

```
EC2 SG

22  → 0.0.0.0/0
3306 → 0.0.0.0/0
8080 → 0.0.0.0/0
```

This exposes unnecessary services.

Better:

```
SSH
→ restricted administration source

App port
→ ALB-SG

Database
→ APP-SG
```

### Exam trigger

> **"Allow only the application tier to access the database."**
> 

→ **Database Security Group referencing application Security Group**

---

# 8. IAM Roles for EC2 ⭐⭐⭐⭐⭐

An EC2 instance often needs to call AWS APIs.

Example:

```
EC2
  │
  │ S3 API
  ▼
Amazon S3
```

How should the EC2 instance authenticate?

### Best practice:

> **Attach an IAM role to the EC2 instance.**
> 

Example:

```
EC2
 │
 └── IAM Role
       │
       ├── s3:GetObject
       └── s3:PutObject
```

The application can then obtain temporary credentials automatically.

---

# 9. EC2 IAM Role Example

Suppose a web server needs to read:

```
s3://company-assets/*
```

Don't create an IAM user and embed its credentials into the application.

Instead:

```
EC2
 ↓
IAM Role
 ↓
Permission Policy
 ↓
S3
```

Policy conceptually:

```
Allow:
s3:GetObject

Resource:
company-assets/*
```

This follows:

> **Least privilege**
> 

---

# 10. Instance Profile ⭐⭐⭐⭐⭐

This terminology causes confusion.

An **instance profile** is the mechanism used to pass an IAM role to an EC2 instance.

Conceptually:

```
IAM Role
   ↓
Instance Profile
   ↓
EC2
```

The instance profile contains the role that EC2 can use.

### Exam memory

> **IAM Role = permissions/identity**
> 
> 
> **Instance Profile = vehicle that associates the role with EC2**
> 

---

# 11. Why Is This Important?

A question may say:

> "An EC2 instance needs permission to access S3."
> 

Possible answers might include:

- Create IAM user
- Store access key in application
- Create access key and put it in User Data
- Attach IAM role to EC2

Correct:

> **Attach an IAM role to the EC2 instance.**
> 

Under the hood:

```
Role
 ↓
Instance Profile
 ↓
EC2
 ↓
Temporary credentials
 ↓
AWS API
```

---

# 12. Why Access Keys Should NOT Normally Be Embedded ⭐⭐⭐⭐⭐

Suppose a developer does this:

```
EC2
 │
 ├── AWS_ACCESS_KEY_ID
 └── AWS_SECRET_ACCESS_KEY
```

This is generally a bad design.

Why?

### Problem 1 — Credential leakage

Credentials could appear in:

- source code
- configuration files
- AMIs
- logs
- User Data
- Git repositories
- application artifacts

---

### Problem 2 — Long-lived credentials

IAM user access keys are generally long-lived credentials.

If leaked:

```
Attacker
   ↓
Access Key
   ↓
AWS APIs
```

Potentially severe consequences.

---

### Problem 3 — Rotation

You now have to rotate credentials and update applications.

---

### Better architecture

```
EC2
 ↓
IAM Role
 ↓
Instance Profile
 ↓
IMDS
 ↓
Temporary credentials
 ↓
AWS API
```

This eliminates the need for you to manually place long-lived access keys on the server.

---

# 13. User Data Is NOT a Secure Credential Store

Very important exam trap.

Some candidates think:

> "I'll put the AWS access key into User Data."
> 

Bad idea.

User Data is intended for:

> **bootstrapping/configuration**
> 

not:

> **storing long-lived secrets**
> 

Instead use:

- IAM roles
- Secrets Manager
- Systems Manager Parameter Store
- other appropriate secret-management mechanisms

depending on the requirement.

---

# 14. SSM vs SSH ⭐⭐⭐⭐⭐

This is one of the highest-value EC2 administration comparisons.

## SSH

Traditional model:

```
Administrator
     │
     │ TCP 22
     ▼
   EC2
```

Requirements typically include:

- SSH client
- network path
- port 22 reachable
- authentication credentials/key
- suitable security-group/network configuration

---

# 15. Systems Manager Session Manager

Session Manager lets you access an EC2 instance **without needing traditional inbound SSH access**.

Conceptually:

```
Administrator
      │
      ▼
AWS Systems Manager
      │
      ▼
EC2
```

The SSM Agent on the instance establishes the required communication with Systems Manager.

This means you can avoid exposing:

```
TCP 22
```

to the network.

---

# 16. SSM Session Manager Architecture

Typical pattern:

```
                  AWS
                   │
                   ▼
          Systems Manager
                   │
                   ▼
               EC2
             SSM Agent
```

The EC2 instance needs appropriate IAM permissions/instance role and network connectivity to Systems Manager endpoints.

Depending on architecture, this connectivity can use:

- internet/NAT path
- appropriate VPC endpoints

You don't need to expose SSH publicly.

---

# 17. SSM vs SSH Exam Table

| Requirement | SSH | SSM Session Manager |
| --- | --- | --- |
| Port 22 required | Usually ✅ | ❌ |
| SSH key required | Usually ✅ | ❌ |
| Public SSH exposure required | ❌ not necessarily | ❌ |
| Central AWS management | Limited | ✅ |
| IAM-based access | Not the primary mechanism | ✅ |
| Audit integration | Possible | Strong AWS integration |
| Good for private EC2 | Possible with networking | ✅ |
| Avoid bastion host | ❌ traditionally | ✅ |

### Strong trigger

> **"Access private EC2 without opening port 22."**
> 

→ **Systems Manager Session Manager**

---

# 18. Bastion Host ⭐⭐⭐⭐⭐

A Bastion Host is a dedicated server used as an administrative jump point.

Architecture:

```
Administrator
      │
      │ SSH 22
      ▼
Bastion
      │
      │ SSH 22
      ▼
Private EC2
```

The private instances don't need direct Internet SSH access.

---

# 19. Bastion Host Security Pattern

Public subnet:

```
Bastion
Public IP
```

Private subnet:

```
EC2
No public IP
```

Security groups:

```
Bastion-SG
   │
   └── SSH from administrator IP

Private-EC2-SG
   │
   └── SSH from Bastion-SG
```

This is much better than:

```
Private EC2
SSH 22
from 0.0.0.0/0
```

---

# 20. Bastion vs SSM

| Requirement | Bastion | SSM |
| --- | --- | --- |
| Traditional SSH | ✅ | ❌ |
| Requires port 22 to managed EC2 | Usually ✅ | ❌ |
| Requires bastion server | ✅ | ❌ |
| IAM-centric access | Less direct | ✅ |
| Avoid inbound SSH | ❌ | ✅ |
| Operational overhead | Higher | Lower |
| Strong modern choice | Sometimes | Often |

### Exam trigger

> **"Securely administer private EC2 without SSH/bastion."**
> 

→ **SSM Session Manager**

If the question explicitly requires:

> **SSH-based administration**
> 

→ **Bastion Host** may be appropriate.

---

# 21. IMDS — Instance Metadata Service ⭐⭐⭐⭐⭐

IMDS allows an EC2 instance to retrieve information about itself.

Examples include:

- instance ID
- instance type
- local IP
- hostname
- networking information
- IAM role credentials

The service is available from the instance itself.

Conceptually:

```
EC2
 │
 ▼
IMDS
 │
 ├── Instance metadata
 └── IAM role credentials
```

---

# 22. Why Is IMDS Important?

For IAM roles, the application needs temporary AWS credentials.

Conceptually:

```
Application
    │
    ▼
IMDS
    │
    ▼
Temporary credentials
    │
    ▼
S3 / DynamoDB / etc.
```

This is one reason you don't have to hard-code access keys into the application.

---

# 23. IMDSv1 vs IMDSv2 ⭐⭐⭐⭐⭐

There are two major versions:

- IMDSv1
- IMDSv2

### IMDSv1

Uses a simpler request model.

The application can request metadata directly.

---

### IMDSv2

Uses a **session-oriented token mechanism**.

Conceptually:

```
Application
    │
    │ request token
    ▼
IMDSv2
    │
    │ token
    ▼
Metadata request
```

This provides stronger protection against certain classes of metadata credential theft, particularly SSRF-related attacks.

---

# 24. Why IMDSv2 Is Preferred

Suppose an application has an SSRF vulnerability.

An attacker tries:

```
Internet attacker
      ↓
Vulnerable application
      ↓
EC2 metadata
      ↓
IAM credentials
```

That could potentially expose the instance's temporary credentials.

IMDSv2 adds an additional token requirement that makes this attack harder.

### Exam trigger

> **"Protect EC2 metadata from SSRF attacks."**
> 

→ **IMDSv2**

---

# 25. IMDSv2 Memory Trick

> **v2 = token**
> 

That's enough for most SAA questions.

```
IMDSv1
→ direct metadata access

IMDSv2
→ token-based session
```

---

# 26. Important IMDS Trap

IMDS is **not** an AWS Secrets Manager replacement.

It provides:

- instance metadata
- temporary IAM role credentials

It is not designed as your application's general-purpose secret vault.

For application secrets:

```
Secrets Manager
or
Parameter Store
```

may be appropriate.

---

# 27. Encryption ⭐⭐⭐⭐⭐

EC2 security isn't only about network access.

You also need to protect:

> **data at rest**
> 

and:

> **data in transit**
> 

---

# 28. EBS Encryption

EBS volumes can be encrypted.

Conceptually:

```
EC2
 │
 ▼
Encrypted EBS
 │
 ▼
KMS
```

Encryption protects data stored on the EBS volume.

---

# 29. What Does EBS Encryption Protect?

For an encrypted EBS volume, encryption applies to data such as:

- data at rest
- data moving between the instance and EBS storage
- snapshots created from the encrypted volume
- volumes created from encrypted snapshots

For SAA purposes:

> **Encrypted EBS = encryption at rest with KMS integration.**
> 

---

# 30. EBS Encryption and KMS ⭐⭐⭐⭐⭐

AWS KMS can provide the cryptographic keys used for EBS encryption.

Conceptually:

```
                 AWS KMS
                   │
                   ▼
             KMS encryption key
                   │
                   ▼
                EBS Volume
                   │
                   ▼
                  EC2
```

You can use:

- AWS managed KMS keys
- customer managed KMS keys

depending on requirements.

---

# 31. Why Customer Managed KMS Keys?

A customer-managed KMS key can provide greater control over:

- key policies
- permissions
- key lifecycle
- auditing
- organizational separation

Example:

```
Production EBS
      ↓
Customer managed KMS key
      ↓
Restricted key policy
```

This is useful when the organization has specific security/compliance requirements.

---

# 32. EBS Encryption Flow

When an encrypted EBS volume is used:

```
EC2
 │
 ▼
EBS
 │
 ▼
KMS key
 │
 ▼
Encrypted storage
```

You generally don't manually encrypt/decrypt every read/write operation in your application.

AWS handles the encryption infrastructure.

---

# 33. EBS Snapshot + Encryption

An encrypted EBS volume produces encrypted snapshots.

You can also use EBS snapshot workflows to create encrypted volumes from previously unencrypted data.

Conceptually:

```
Unencrypted EBS
      ↓
Snapshot
      ↓
Encrypted copy/workflow
      ↓
Encrypted EBS
```

This is a common migration/encryption pattern.

---

# 34. KMS Permissions Matter

If a question says:

> "EC2/EBS encryption is configured using a customer-managed KMS key, but operations fail because of permissions."
> 

Think:

> **KMS key policy + IAM permissions**
> 

The identity performing the operation must have appropriate permissions, and the KMS key policy must permit the required use where applicable.

---

# 35. Encryption at Rest vs In Transit

Don't mix these.

### At rest

Data stored on:

- EBS
- S3
- RDS
- etc.

Example:

```
Encrypted EBS
```

### In transit

Data moving over the network.

Example:

```
Client
  │
  │ HTTPS/TLS
  ▼
ALB
  │
  │ TLS
  ▼
Application
```

### Memory:

> **At rest = storage**
> 
> 
> **In transit = network**
> 

---

# 36. Network-Level vs Identity-Level Controls ⭐⭐⭐⭐⭐

This is probably the most important conceptual distinction in this part.

Imagine:

```
EC2
```

There are two completely different questions.

### Question 1:

> **"Who can connect to this instance?"**
> 

That's primarily a **network security** question.

Think:

> Security Group
> 

---

### Question 2:

> **"What can this instance do in AWS?"**
> 

That's an **identity/authorization** question.

Think:

> IAM Role
> 

---

# 37. Example — S3

Suppose:

```
EC2 → S3
```

You need two different things.

### Network path

Can EC2 reach S3?

Potentially:

```
VPC Endpoint
NAT
Internet path
```

### Authorization

Can the EC2 application perform:

```
s3:GetObject
```

?

That's:

```
IAM Role
```

So:

```
Network connectivity
       +
IAM authorization
```

are separate.

---

# 38. Security Group vs IAM Role

| Question | Correct control |
| --- | --- |
| Can traffic reach EC2 port 443? | **Security Group** |
| Can EC2 receive TCP 8080 from ALB? | **Security Group** |
| Can EC2 read from S3? | **IAM Role** |
| Can EC2 write to DynamoDB? | **IAM Role** |
| Can EC2 publish to SNS? | **IAM Role** |
| Can user connect to EC2 using SSH? | **Network + authentication** |
| Can EC2 call KMS API? | **IAM/KMS permissions** |
| Can EC2 accept inbound traffic? | **Security Group** |

---

# 39. The "Two Locks" Mental Model ⭐⭐⭐⭐⭐

Think of AWS security as two locks.

### Lock #1 — Network

```
Security Group
```

asks:

> **"Can the connection get here?"**
> 

### Lock #2 — IAM

```
IAM Role
```

asks:

> **"Is this identity allowed to perform this AWS action?"**
> 

Therefore:

```
Network allowed
      ↓
Doesn't automatically mean
      ↓
AWS API authorized
```

And:

```
IAM permission
      ↓
Doesn't automatically create
      ↓
Network connectivity
```

This distinction solves many SAA questions.

---

# 40. Example: EC2 Cannot Access S3

Question:

> An EC2 instance has an IAM role allowing `s3:GetObject`, but it cannot access S3.
> 

Don't immediately blame IAM.

Check:

```
IAM permission
+
Network connectivity
```

Possible issue:

```
Private subnet
+
No NAT
+
No S3 Gateway Endpoint
```

The IAM role may be perfectly correct.

---

# 41. Example: EC2 Can Reach S3 But AccessDenied

Opposite situation.

```
EC2
 ↓
S3 reachable
 ↓
S3
 ↓
AccessDenied
```

Now think:

> **IAM/S3 authorization**
> 

The network path works, but permissions don't.

---

# 42. EC2 Security Architecture ⭐⭐⭐⭐⭐

A strong architecture might look like:

```
                    Internet
                       │
                       ▼
                      ALB
                  [ALB-SG]
                       │
                       ▼
                    EC2 ASG
                  [APP-SG]
                       │
          ┌────────────┼─────────────┐
          │            │             │
          ▼            ▼             ▼
         S3          DynamoDB       KMS
          │            │             │
          └────────────┼─────────────┘
                       │
                    IAM Role
                       │
                    Instance
                    Profile
                       │
                      IMDSv2
```

Administration:

```
Administrator
      │
      ▼
Systems Manager
      │
      ▼
Private EC2
```

Storage:

```
EC2
 │
 ▼
Encrypted EBS
 │
 ▼
KMS
```

This gives you:

- network isolation
- IAM least privilege
- temporary credentials
- secure administration
- encryption at rest
- reduced SSH exposure

---

# 43. Full EC2 Security Flow

When an EC2 application accesses an AWS service:

```
                EC2
                 │
       ┌─────────┴──────────┐
       │                    │
       ▼                    ▼
 Security Group          IAM Role
 "Can traffic pass?"     "Is action allowed?"
       │                    │
       └─────────┬──────────┘
                 ▼
             AWS Service
```

Credentials:

```
IAM Role
   ↓
Instance Profile
   ↓
IMDSv2
   ↓
Temporary credentials
   ↓
Application
```

Storage:

```
EC2
 ↓
Encrypted EBS
 ↓
KMS
```

Administration:

```
Admin
 ↓
SSM Session Manager
 ↓
EC2
```

---

# 44. High-Value Exam Scenarios

## Scenario 1

> EC2 needs to access S3 without storing credentials.
> 

### Correct:

**IAM Role attached through instance profile.**

---

## Scenario 2

> Application must not contain long-lived AWS access keys.
> 

### Correct:

**IAM Role + temporary credentials through IMDS.**

---

## Scenario 3

> Protect metadata credentials against SSRF.
> 

### Correct:

**IMDSv2.**

---

## Scenario 4

> Administrator needs access to private EC2 without opening SSH port 22.
> 

### Correct:

**Systems Manager Session Manager.**

---

## Scenario 5

> Company requires traditional SSH access to private instances.
> 

### Correct pattern:

**Bastion Host** or another secure SSH architecture.

---

## Scenario 6

> Only the application servers should connect to RDS.
> 

### Correct:

**RDS Security Group allows database port from application Security Group.**

---

## Scenario 7

> EC2 needs permission to call KMS.
> 

### Correct:

**IAM permissions + appropriate KMS key policy.**

---

## Scenario 8

> Encrypt EC2 persistent storage at rest.
> 

### Correct:

**EBS encryption using KMS.**

---

## Scenario 9

> EC2 can reach S3 but receives AccessDenied.
> 

### Think:

**IAM/resource policy**, not Security Group.

---

## Scenario 10

> EC2 has permission to access S3 but has no network path to S3.
> 

### Think:

**VPC networking**, such as an appropriate S3 Gateway Endpoint or NAT path depending on architecture.

---

# 45. Biggest EC2 Security Traps ⭐⭐⭐⭐⭐

### Trap 1

> "Give EC2 an access key."
> 

❌ Usually wrong.

Use:

> **IAM Role**
> 

---

### Trap 2

> "Put credentials in User Data."
> 

❌ Bad credential-management practice.

Use:

> **IAM Role / Secrets Manager / Parameter Store**
> 

depending on what you're storing.

---

### Trap 3

> "Security Group allows S3 access."
> 

❌ Security Groups don't grant S3 API permissions.

SG controls network traffic.

IAM controls AWS API authorization.

---

### Trap 4

> "IAM role means EC2 can automatically reach S3."
> 

❌ Not necessarily.

You still need network connectivity.

---

### Trap 5

> "IMDSv2 encrypts EBS."
> 

❌ Completely different functions.

```
IMDSv2 → instance metadata/credentials protection
EBS encryption → storage encryption
```

---

### Trap 6

> "SSM requires opening port 22."
> 

❌ No.

One of its major advantages is avoiding traditional inbound SSH.

---

### Trap 7

> "Bastion is always the best modern solution."
> 

❌ Not necessarily.

If the requirement is:

> secure EC2 administration without SSH
> 

→ **SSM Session Manager** is usually the stronger architecture.

---

# 46. EC2 Security Master Table

| Requirement | Correct answer |
| --- | --- |
| Control inbound/outbound EC2 network traffic | **Security Group** |
| Stateful EC2 firewall | **Security Group** |
| Allow application tier to DB tier | **SG-to-SG rule** |
| Give EC2 S3 permissions | **IAM Role** |
| Give EC2 temporary AWS credentials | **IAM Role + Instance Profile + IMDS** |
| Avoid hard-coded access keys | **IAM Role** |
| Retrieve temporary role credentials | **IMDS** |
| Protect metadata from SSRF | **IMDSv2** |
| Administer private EC2 without SSH | **SSM Session Manager** |
| Traditional SSH jump server | **Bastion Host** |
| Encrypt EBS at rest | **EBS Encryption** |
| Manage EBS encryption keys | **AWS KMS** |
| Control KMS key usage | **KMS key policy + IAM** |
| Can network traffic reach EC2? | **Security Group/NACL/routing** |
| Can EC2 perform an AWS API action? | **IAM** |
| Can EC2 reach S3? | **Network connectivity** |
| Can EC2 read S3 object? | **IAM/resource authorization** |

---

# 47. The 5-Layer EC2 Security Model

Memorize this:

```
1. NETWORK
   Security Group
   ↓
   "Can traffic reach me?"

2. IDENTITY
   IAM Role
   ↓
   "Can I perform this AWS action?"

3. CREDENTIAL DELIVERY
   IMDSv2
   ↓
   "How does EC2 obtain temporary credentials?"

4. ADMINISTRATION
   SSM
   ↓
   "How do humans manage the instance securely?"

5. DATA PROTECTION
   EBS Encryption + KMS
   ↓
   "Is stored data protected?"
```

---

# 48. 30-Second SAA-C03 Security Scan ⭐⭐⭐⭐⭐

```
EC2 needs AWS API access
        ↓
     IAM ROLE
```

```
EC2 needs credentials
        ↓
  INSTANCE PROFILE
        ↓
      IMDSv2
```

```
Avoid hard-coded keys
        ↓
     IAM ROLE
```

```
Protect metadata from SSRF
        ↓
      IMDSv2
```

```
Private EC2 administration
without SSH
        ↓
       SSM
```

```
Traditional SSH jump point
        ↓
     BASTION
```

```
Control network traffic
        ↓
   SECURITY GROUP
```

```
Encrypt EC2 disk
        ↓
   EBS ENCRYPTION
        ↓
       KMS
```

```
"Can it connect?"
        ↓
NETWORK CONTROLS

"Can it perform the AWS action?"
        ↓
IDENTITY/IAM CONTROLS
```

# Final Memory Formula

> **SG = network**
> 
> 
> **IAM Role = permission**
> 
> **Instance Profile = role attached to EC2**
> 
> **IMDSv2 = temporary credentials + metadata protection**
> 
> **SSM = manage EC2 without SSH**
> 
> **Bastion = SSH jump host**
> 
> **EBS Encryption = data at rest**
> 
> **KMS = key management**
> 

**EC2 Part 7 complete.**
