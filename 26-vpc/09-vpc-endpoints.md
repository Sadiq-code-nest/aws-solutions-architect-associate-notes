# AWS VPC Endpoints — AWS PrivateLink

### SAA-C03 Exam Notes + Easy Real-World Understanding

This is a **very important VPC topic for SAA-C03**, especially questions involving:

- Private EC2 → S3/DynamoDB
- Private EC2 → AWS services
- "No Internet access"
- "Avoid NAT Gateway cost"
- "Access AWS services privately"
- Gateway Endpoint vs Interface Endpoint

---

# 1. First: What problem does a VPC Endpoint solve?

Normally, an AWS service such as S3, DynamoDB, SNS, or SQS is accessed through an AWS public endpoint.

Imagine:

```
Private EC2
     |
     | ❌ No direct Internet access
     |
    NAT
     |
    IGW
     |
  Internet
     |
   AWS Service
```

This works, but you need a **NAT Gateway + Internet Gateway**, and NAT Gateway costs money.

A VPC Endpoint provides a private path:

```
Private EC2
     |
     | Private AWS network
     |
 VPC Endpoint
     |
 AWS Service
```

### Core idea

> **VPC Endpoint = Private connectivity from your VPC to supported AWS services without requiring Internet Gateway/NAT Gateway.**
> 

---

# 2. The most important exam distinction

There are **two major types** you need to know:

|  | Gateway Endpoint | Interface Endpoint |
| --- | --- | --- |
| Powered by | VPC Gateway Endpoint | AWS PrivateLink |
| Uses | Route table | ENI |
| Private IP / ENI | ❌ No | ✅ Yes |
| Security Group | ❌ No | ✅ Yes |
| Supported services | **S3 + DynamoDB** | **Most AWS services** |
| Cost | **Free** | Hourly + data processing |
| Route table modification | ✅ Yes | Usually DNS/ENI-based routing |
| Typical exam choice | S3/DynamoDB | SNS, SQS, KMS, Secrets Manager, etc. |

### 🚨 Memorize this first

> **S3 / DynamoDB → Gateway Endpoint**
> 

> **Almost everything else → Interface Endpoint**
> 

---

# 3. Gateway Endpoint

Gateway Endpoint is specifically for:

- **Amazon S3**
- **Amazon DynamoDB**

It provides private connectivity without going through:

- Internet Gateway
- NAT Gateway
- Public Internet

### Architecture

```
                 AWS Region
┌──────────────────────────────────────────┐
│                                          │
│  VPC                                     │
│                                          │
│  Private Subnet                          │
│  ┌──────────────┐                        │
│  │ Private EC2  │                        │
│  └──────┬───────┘                        │
│         │                                 │
│         ▼                                 │
│   Route Table                             │
│         │                                 │
│         ▼                                 │
│  Gateway Endpoint                         │
│         │                                 │
└─────────┼────────────────────────────────┘
          │
          ▼
     Amazon S3
     or DynamoDB
```

### Important

A Gateway Endpoint is **not an ENI**.

Therefore:

- No private IP to configure
- No Security Group attached
- Route table is used

---

# 4. Example — Private EC2 needs S3

Suppose you have:

```
Private EC2
10.0.2.10
```

It needs to download files from S3.

The private subnet has **no NAT Gateway** and **no Internet Gateway**.

You could do:

### ❌ Expensive approach

```
EC2
 ↓
NAT Gateway
 ↓
Internet Gateway
 ↓
AWS network
 ↓
S3
```

But you can instead use:

### ✅ Better solution

```
EC2
 ↓
Route Table
 ↓
S3 Gateway Endpoint
 ↓
S3
```

### Exam answer

If the question says:

> "Private EC2 instances need access to S3 without Internet connectivity and with minimal cost."
> 

Think:

**S3 Gateway VPC Endpoint.**

---

# 5. Example — Private Lambda → DynamoDB

Imagine:

```
Lambda
   |
   | inside VPC
   |
Private Subnet
   |
   ▼
DynamoDB
```

Lambda needs to access DynamoDB.

You have two possibilities.

### Option 1 — NAT

```
Lambda
 ↓
Private Subnet
 ↓
NAT Gateway
 ↓
Internet Gateway
 ↓
DynamoDB
```

This works, but costs money.

### Option 2 — Gateway Endpoint ⭐

```
Lambda
 ↓
Private Subnet
 ↓
DynamoDB Gateway Endpoint
 ↓
DynamoDB
```

This is generally the better answer when the question emphasizes:

- private connectivity
- no Internet
- cost optimization
- DynamoDB

---

# 6. Interface Endpoint

An Interface Endpoint uses **AWS PrivateLink**.

It creates an **Elastic Network Interface (ENI)** inside your subnet.

That ENI gets a **private IP address**.

```
Private EC2
     |
     ▼
┌──────────────────┐
│ Interface        │
│ Endpoint         │
│                  │
│ ENI              │
│ Private IP       │
└────────┬─────────┘
         |
         ▼
   AWS Service
```

Because it uses an ENI, you can associate a **Security Group** with the endpoint.

---

# 7. What services use Interface Endpoints?

Interface Endpoints support **most AWS services**.

Examples include services such as:

- SNS
- SQS
- KMS
- Secrets Manager
- CloudWatch
- Systems Manager
- API Gateway
- ECR
- etc.

So if you see:

> Private EC2 → SNS
> 

Think:

**Interface Endpoint / AWS PrivateLink**

Not Gateway Endpoint.

---

# 8. Example — Private EC2 → SNS

Suppose your application is running on a private EC2 instance.

It needs to publish notifications to SNS.

You don't want:

- Internet access
- NAT Gateway

Architecture:

```
                  AWS Region
┌───────────────────────────────────┐
│ VPC                               │
│                                   │
│ Private Subnet                    │
│                                   │
│ ┌────────────┐                    │
│ │ EC2         │                   │
│ └─────┬──────┘                    │
│       │                            │
│       ▼                            │
│ Interface Endpoint                │
│ (ENI + Private IP)                │
│       │                            │
└───────┼───────────────────────────┘
        │
        ▼
       SNS
```

Because SNS is **not S3/DynamoDB**, you use:

> **Interface Endpoint**
> 

---

# 9. Why Security Groups matter for Interface Endpoints

Because an Interface Endpoint is an **ENI**, you can attach a Security Group.

Example:

```
Private EC2
SG: App-SG
     |
     | TCP 443
     ▼
Interface Endpoint
SG: Endpoint-SG
     |
     ▼
AWS Service
```

You might configure:

```
Endpoint-SG inbound:

HTTPS / TCP 443
Source: App-SG
```

Meaning:

> Only EC2 instances belonging to `App-SG` can connect to the endpoint over HTTPS.
> 

### Exam clue

If the question says:

> "Configure security group rules for private access to an AWS service"
> 

Think:

**Interface Endpoint**

---

# 10. Gateway vs Interface — the easiest way to remember

### Gateway Endpoint

Think:

> **"Gateway = S3/DynamoDB"**
> 

```
S3
DynamoDB
   ↑
Gateway Endpoint
```

### Interface Endpoint

Think:

> **"Interface = ENI + PrivateLink + most services"**
> 

```
SNS
SQS
KMS
Secrets Manager
etc.
   ↑
Interface Endpoint
```

---

# 11. Gateway Endpoint vs NAT Gateway

This is an extremely common exam comparison.

|  | Gateway Endpoint | NAT Gateway |
| --- | --- | --- |
| Purpose | Private access to S3/DynamoDB | Private subnet → Internet |
| Internet required | ❌ | Via IGW |
| NAT required | ❌ | It's the NAT |
| S3 access | ✅ | ✅ |
| DynamoDB access | ✅ | ✅ |
| General Internet access | ❌ | ✅ |
| Cost | **Free** | 💰 Paid |
| Security Group | ❌ | NAT GW itself doesn't use SG |
| Route table | ✅ | ✅ |
| Best for | S3/DynamoDB private access | Internet access from private subnet |

### Important distinction

A Gateway Endpoint **doesn't replace a NAT Gateway for general Internet access.**

For example:

```
Private EC2 → S3
```

Gateway Endpoint works.

But:

```
Private EC2 → google.com
```

Gateway Endpoint does **not** work.

You need NAT Gateway or another appropriate Internet egress architecture.

---

# 12. Interface Endpoint vs NAT Gateway

Suppose private EC2 needs access to **SNS**.

### NAT approach

```
Private EC2
   ↓
NAT Gateway
   ↓
Internet Gateway
   ↓
Internet/AWS public endpoint
   ↓
SNS
```

### Interface Endpoint

```
Private EC2
   ↓
Interface Endpoint
   ↓
AWS PrivateLink
   ↓
SNS
```

If the requirement says:

> "Access the AWS service privately without Internet connectivity."
> 

Choose:

**Interface VPC Endpoint.**

---

# 13. What does "AWS PrivateLink" mean?

This can be confusing.

Think of it like:

> **PrivateLink = the underlying private connectivity technology used by Interface Endpoints.**
> 

So:

```
Interface Endpoint
       ↓
   PrivateLink
       ↓
AWS Service
```

For SAA-C03, if you see:

> "Use AWS PrivateLink to privately access an AWS service"
> 

Think:

**Interface VPC Endpoint.**

---

# 14. VPC Endpoint and DNS

Interface Endpoints commonly work with **Private DNS**.

For example, your application might normally use:

```
sns.<region>.amazonaws.com
```

With Private DNS enabled, the DNS name can resolve to the endpoint's **private IP addresses** from inside your VPC.

So your application doesn't necessarily need to be rewritten to use a special endpoint URL.

### Exam clue

If you create an Interface Endpoint but connectivity isn't working, check:

1. **DNS resolution**
2. **Security Group**
3. **Network ACL**
4. **Route/network configuration**

Your provided notes correctly highlight DNS settings and route/network configuration as troubleshooting areas.

---

# 15. Gateway Endpoint and Route Tables

Gateway Endpoints work through route tables.

Conceptually:

```
Private EC2
    ↓
Private Subnet Route Table
    ↓
S3 Gateway Endpoint
    ↓
S3
```

The endpoint is associated with route tables.

Therefore, if an instance in one subnet can access S3 but another can't, check:

> **Are both subnet route tables associated with the Gateway Endpoint?**
> 

---

# 16. Endpoint Policies

This is another useful SAA-C03 concept.

VPC Endpoints can have **endpoint policies** that control which AWS resources/actions can be accessed through the endpoint.

For example:

```
Private EC2
    ↓
S3 Gateway Endpoint
    ↓
Endpoint Policy
    ↓
Only approved S3 bucket
```

So you can restrict access further.

Think:

> **IAM controls who can perform actions; endpoint policies can restrict access through the endpoint.**
> 

---

# 17. The big architecture comparison

### Scenario A — Private EC2 → Internet

```
Private EC2
     ↓
Route Table
     ↓
NAT Gateway
     ↓
Internet Gateway
     ↓
Internet
```

Use **NAT Gateway**.

---

### Scenario B — Private EC2 → S3

```
Private EC2
     ↓
Route Table
     ↓
Gateway Endpoint
     ↓
S3
```

Use **Gateway Endpoint**.

---

### Scenario C — Private EC2 → DynamoDB

```
Private EC2
     ↓
Route Table
     ↓
Gateway Endpoint
     ↓
DynamoDB
```

Use **Gateway Endpoint**.

---

### Scenario D — Private EC2 → SNS

```
Private EC2
     ↓
Interface Endpoint
     ↓
PrivateLink
     ↓
SNS
```

Use **Interface Endpoint**.

---

### Scenario E — Private EC2 → SQS

```
Private EC2
     ↓
Interface Endpoint
     ↓
PrivateLink
     ↓
SQS
```

Use **Interface Endpoint**.

---

# 18. On-premises → AWS Service

This is where Interface Endpoints become especially interesting.

Suppose:

```
On-Premises
     |
     | Direct Connect / Site-to-Site VPN
     |
     ▼
    VPC
     |
     ▼
Interface Endpoint
     |
     ▼
AWS Service
```

An Interface Endpoint can provide private connectivity to supported AWS services through your VPC.

### Exam clue

If you see:

> "On-premises servers need private access to an AWS service without traversing the public Internet."
> 

Think:

**VPN/DX → VPC → Interface Endpoint / PrivateLink**

---

# 19. S3: Gateway vs Interface Endpoint

This is a little more advanced.

S3 can be accessed using either:

- **Gateway Endpoint**
- **Interface Endpoint**

### Gateway Endpoint

```
VPC
 ↓
Gateway Endpoint
 ↓
S3
```

Advantages:

- Free
- Simple
- Common choice for resources inside VPC

### Interface Endpoint

```
VPC
 ↓
Interface Endpoint
 ↓
PrivateLink
 ↓
S3
```

Useful when you need capabilities associated with Interface Endpoints, such as private connectivity from environments outside the VPC through supported network connectivity.

### Exam shortcut

If the question simply says:

> Private EC2 needs access to S3 at the lowest cost.
> 

Answer:

**S3 Gateway Endpoint.**

---

# 20. One major trap: VPC Endpoint ≠ Internet Gateway

Don't confuse them.

### Internet Gateway

```
VPC → IGW → Internet
```

Provides Internet connectivity.

### VPC Endpoint

```
VPC → Endpoint → Supported AWS Service
```

Provides private access to supported AWS services.

---

# 21. One major trap: VPC Endpoint ≠ NAT Gateway

NAT Gateway:

> **Private subnet → Internet**
> 

VPC Endpoint:

> **VPC → Supported AWS service privately**
> 

Remember:

```
Need Internet?
       ↓
   NAT Gateway

Need S3/DynamoDB?
       ↓
 Gateway Endpoint

Need SNS/SQS/KMS/etc. privately?
       ↓
 Interface Endpoint
```

---

# 22. SAA-C03 Decision Tree ⭐

When you see a question about private access:

```
Private resource needs connectivity
             │
             ▼
       What destination?
             │
      ┌──────┴─────────┐
      │                │
 S3 / DynamoDB      Other AWS service
      │                │
      ▼                ▼
Gateway Endpoint   Interface Endpoint
      │                │
      │             PrivateLink
      │                │
    FREE           Paid
```

If the destination is **general Internet**:

```
Private resource
      ↓
NAT Gateway
      ↓
Internet Gateway
      ↓
Internet
```

---

# 23. Exam Keyword → Answer Map

| Question keyword/clue | Think |
| --- | --- |
| Private EC2 → S3 | **Gateway Endpoint** |
| Private EC2 → DynamoDB | **Gateway Endpoint** |
| Lowest cost S3 private access | **Gateway Endpoint** |
| Lowest cost DynamoDB private access | **Gateway Endpoint** |
| No Internet Gateway/NAT required for S3 | **Gateway Endpoint** |
| Private EC2 → SNS | **Interface Endpoint** |
| Private EC2 → SQS | **Interface Endpoint** |
| Private EC2 → KMS | **Interface Endpoint** |
| Private EC2 → Secrets Manager | **Interface Endpoint** |
| Most AWS services | **Interface Endpoint** |
| AWS PrivateLink | **Interface Endpoint** |
| ENI / private IP | **Interface Endpoint** |
| Security Group on endpoint | **Interface Endpoint** |
| Private subnet → Internet | **NAT Gateway** |
| On-premises → AWS service privately | Often **Interface Endpoint + VPN/DX** |
| No public Internet access | **VPC Endpoint** may be appropriate |
| Free endpoint | **Gateway Endpoint** |

---

# 24. Real-world scenario to lock it in

Imagine a company has:

```
                   VPC
        ┌───────────────────────────┐
        │                           │
        │     Private Subnet        │
        │                           │
        │      EC2 Application      │
        │             │             │
        └─────────────┼─────────────┘
                      │
          ┌───────────┼───────────┐
          │           │           │
          ▼           ▼           ▼
       S3          DynamoDB      SNS
        │             │           │
        ▼             ▼           ▼
     Gateway       Gateway     Interface
     Endpoint      Endpoint    Endpoint
                                  │
                              PrivateLink
```

The application needs three things:

- Store files → **S3 Gateway Endpoint**
- Read/write NoSQL data → **DynamoDB Gateway Endpoint**
- Send notifications → **SNS Interface Endpoint**

No NAT Gateway is needed **for these three AWS-service connections**.

---

# 25. ⚡ Final SAA-C03 Cheat Sheet

### VPC Endpoint

> Private connectivity between your VPC and supported AWS services.
> 

### Gateway Endpoint

> **S3 + DynamoDB**
> 
- Free
- Route table
- No ENI
- No Security Group
- Private access

### Interface Endpoint

> **Most AWS services**
> 
- AWS PrivateLink
- ENI
- Private IP
- Security Group
- Hourly + data processing charges

### NAT Gateway

> **Private subnet → Internet**
> 
- AWS managed
- Requires IGW
- Paid
- Doesn't provide private endpoint connectivity to AWS services in the same way VPC endpoints do

### 🧠 The ultimate memory trick

> **"S3 & DynamoDB = Gateway. Everything else = Interface."**
> 

And:

> **"Private Internet = NAT; Private AWS Service = VPC Endpoint."**
> 

### SAA-C03 exam takeaway

**If a private EC2/Lambda needs S3 or DynamoDB → choose a Gateway Endpoint. If it needs another supported AWS service such as SNS/SQS/KMS/Secrets Manager → choose an Interface Endpoint (PrivateLink). If it needs general Internet access → NAT Gateway.**
