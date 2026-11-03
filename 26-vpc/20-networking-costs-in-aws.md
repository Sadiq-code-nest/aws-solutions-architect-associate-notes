# AWS Networking Costs — SAA-C03 Exam Revision Note

The main idea is simple:

> **AWS generally charges for data leaving AWS, crossing Regions, and using certain managed networking components.**
> 
> 
> For exam questions, think: **“Can I keep the traffic private, local, and inside AWS?”**
> 

---

## 1. The 3 Traffic Directions You Must Know

| Traffic | Meaning | Typical Cost |
| --- | --- | --- |
| **Ingress** | Internet → AWS | Usually **free** |
| **Egress** | AWS → Internet | Usually **charged** |
| **Inter-AZ** | AZ-A → AZ-B | Usually **charged** |
| **Inter-Region** | Region A → Region B | **Charged** |
| Same AZ, private communication | Resource → resource in same AZ | Often **lowest/free depending on service/path** |

### Easy memory trick

**IN = usually FREE**

**OUT = usually COST**

Think of AWS as a building:

- Bringing things **into** the building → usually free
- Taking things **out** → AWS charges
- Moving things between **different buildings/AZs/Regions** → may cost

---

# 2. Same AZ vs Different AZ

Suppose you have:

```
AWS Region
│
├── AZ-A
│   ├── EC2
│   └── RDS
│
└── AZ-B
    └── EC2
```

If EC2-A communicates with RDS/EC2 in another AZ, **inter-AZ data transfer charges can apply**.

### Exam thinking

| Architecture | Cost perspective | Availability |
| --- | --- | --- |
| Resources in same AZ | Usually cheaper | Lower resilience |
| Resources across AZs | More networking cost possible | Better HA |
| Resources across Regions | More expensive | Disaster recovery / global architecture |

### Important trade-off

Don't blindly choose the cheapest architecture.

If the question asks for:

> **High availability / fault tolerance**
> 

You generally want **multiple AZs**, even if inter-AZ transfer costs exist.

If it asks:

> **Minimize data transfer costs**
> 

Then keeping frequently communicating resources **in the same AZ** can help.

---

# 3. Private IP vs Public IP

When resources communicate inside AWS, **private IP communication is generally preferable**.

```
EC2 A
Private IP
   │
   │ Private AWS network
   ▼
EC2 B
Private IP
```

Instead of unnecessarily routing through public IPs:

```
EC2 A
   │
   ▼
Public networking
   │
   ▼
EC2 B
```

### Exam shortcut

> **Private IP = generally better for internal AWS communication**
> 

It can provide both **cost and performance benefits**, depending on the architecture.

---

# 4. Inter-AZ Cost — Classic Exam Scenario

Imagine:

```
AZ-A                         AZ-B

EC2 ──────── traffic ──────── RDS
```

If your application continuously sends large amounts of data between AZs, the transfer can become expensive.

### Example

An application server processes **huge amounts of data** with a database.

If possible:

```
AZ-A

EC2
 │
 │ heavy traffic
 ▼
Database
```

can reduce cross-AZ data transfer compared with:

```
AZ-A                    AZ-B

EC2 ───────────────────► Database
       huge traffic
```

But remember:

> **Don't sacrifice high availability merely to save transfer costs.**
> 

For production architectures, Multi-AZ is often important.

---

# 5. Egress vs Ingress

This is one of the easiest things to remember.

### Ingress

```
Internet
   │
   │
   ▼
AWS
```

**Incoming traffic**

Usually free.

### Egress

```
AWS
 │
 │
 ▼
Internet
```

**Outgoing traffic**

Usually charged.

### Memory trick

**Egress = Exit**

So:

> **Egress → traffic Exiting AWS → $$$**
> 

---

# 6. Keep Traffic Inside AWS

A common AWS architecture principle:

> **Keep large amounts of data close to where it is processed.**
> 

Bad design:

```
AWS Database
     │
     ▼
Internet
     │
     ▼
On-prem Application
```

if unnecessary.

Better:

```
AWS
│
├── Application
│
└── Database
```

The less unnecessary traffic leaves AWS, the lower the potential egress cost.

---

# 7. Example: Database Query

Suppose:

```
Application
    │
    │ Query
    ▼
Database
    │
    │ 100 MB result
    ▼
Application
```

If the application is close to the database, you avoid unnecessary external data transfer.

### Better architecture

```
AWS Cloud

Application
     │
     │ internal traffic
     ▼
Database
```

### Poor architecture

```
AWS Database
      │
      ▼
Corporate DC
      │
      ▼
Application
```

when the application could instead run within AWS.

---

# 8. S3 Data Transfer Costs

For exam purposes, remember these relationships rather than memorizing every exact price.

| S3 Traffic | Exam idea |
| --- | --- |
| Internet → S3 | **Ingress generally free** |
| S3 → Internet | **Egress charged** |
| S3 → CloudFront | Very favorable transfer pricing |
| S3 → another Region | Cross-Region transfer charges |
| S3 Transfer Acceleration | Faster uploads/downloads + **additional cost** |

---

# 9. S3 → Internet vs S3 → CloudFront

This is important.

### Direct S3 → Internet

```
User
   ▲
   │
Internet
   │
   ▼
S3
```

The user downloads directly from S3.

Potentially:

**S3 data transfer out → $$$**

---

### S3 + CloudFront

```
             ┌── User
             │
S3 ─────── CloudFront
             │
             └── User
```

CloudFront caches content at **Edge Locations**.

Benefits:

- Lower latency
- Reduced repeated requests to S3
- Better user experience
- Can reduce S3-related request/data-transfer costs
- CloudFront is designed for delivery to Internet users

### Exam clue

If the question says:

> **Many users repeatedly download the same S3 objects**
> 

Think:

**S3 + CloudFront**

---

# 10. CloudFront Caching Example

Suppose you have:

```
movie.mp4 = 1 GB
```

10,000 users request the same file.

Without caching:

```
10,000 users
     │
     ▼
    S3
```

With CloudFront:

```
             ┌── User
             ├── User
S3 → CloudFront ├── User
             ├── User
             └── User
```

CloudFront can serve cached content from edge locations rather than repeatedly retrieving it from S3.

### Memory trick

> **CloudFront = Cache close to customers**
> 

---

# 11. S3 Transfer Acceleration

S3 Transfer Acceleration is for **faster data transfer to/from S3 using CloudFront edge locations**.

Conceptually:

```
User
  │
  ▼
Nearest Edge Location
  │
  │ AWS optimized network
  ▼
S3
```

Instead of:

```
User ───────────────► S3
      long distance
```

### When should you think about it?

If the question says:

- Users are geographically far from S3
- Large files
- Upload/download performance is poor
- Need faster transfer to S3
- Willing to pay extra

Think:

> **S3 Transfer Acceleration**
> 

### Memory trick

**Acceleration = faster S3 transfer**

---

# 12. S3 Cross-Region Replication

Suppose:

```
us-east-1                    eu-west-1

S3 Bucket ──────────────────► S3 Bucket
          Cross-Region
          Replication
```

Objects are replicated between Regions.

This is useful for:

- Disaster recovery
- Compliance
- Lower-latency access for users in another Region
- Regional redundancy

But:

> **Cross-Region data transfer has associated charges.**
> 

### Exam clue

If you see:

> **S3 → another AWS Region**
> 

Think:

**Cross-Region transfer cost**

---

# 13. NAT Gateway vs Gateway VPC Endpoint

This is **VERY important for SAA-C03**.

Suppose a private EC2 instance needs to access S3.

### Option A — NAT Gateway

```
Private EC2
    │
    ▼
NAT Gateway
    │
    ▼
Internet Gateway
    │
    ▼
Internet
    │
    ▼
S3
```

You pay for:

- NAT Gateway hourly usage
- Data processed by NAT Gateway
- Potential additional data transfer costs

---

### Option B — S3 Gateway Endpoint

```
Private EC2
    │
    ▼
S3 Gateway Endpoint
    │
    ▼
Amazon S3
```

No NAT Gateway is required.

**Gateway VPC Endpoints for S3 and DynamoDB have no hourly endpoint charge.**

### Exam shortcut

> **Private EC2 → S3/DynamoDB → Gateway Endpoint**
> 

Usually the preferred cost-efficient answer.

---

# 14. NAT Gateway vs S3 Gateway Endpoint

| Feature | NAT Gateway | S3 Gateway Endpoint |
| --- | --- | --- |
| Main purpose | Private subnet → Internet/AWS public endpoints | Private VPC → S3 |
| S3 access | ✅ | ✅ |
| DynamoDB access | ✅ | ✅ |
| General Internet access | ✅ | ❌ |
| Requires IGW | Yes, through NAT architecture | ❌ |
| Hourly charge | ✅ | **No** |
| Data processing charge | ✅ | **No endpoint processing charge** |
| Security Group attached | No | No |
| Route table configuration | Yes | Yes |
| Best for private EC2 → S3 | Usually unnecessary | ⭐ **Excellent** |

### Memory trick

**S3/DynamoDB = Gateway Endpoint**

**Internet = NAT Gateway**

---

# 15. NAT Gateway vs Interface Endpoint

Suppose private EC2 needs to access **Amazon SNS**.

SNS isn't one of the services supported by Gateway Endpoints.

You could use:

```
Private EC2
    │
    ▼
NAT Gateway
    │
    ▼
Internet
    │
    ▼
SNS
```

Or use an **Interface VPC Endpoint / AWS PrivateLink**:

```
Private EC2
    │
    ▼
Interface Endpoint
(ENI/private IP)
    │
    ▼
SNS
```

Interface endpoints have **hourly and data-processing charges**, but they keep traffic private.

---

# 16. Important VPC Endpoint Cost Table

| Requirement | Best exam answer |
| --- | --- |
| Private EC2 → S3 | **S3 Gateway Endpoint** |
| Private EC2 → DynamoDB | **DynamoDB Gateway Endpoint** |
| Private EC2 → SNS | **Interface Endpoint** |
| Private EC2 → SQS | **Interface Endpoint** |
| Private EC2 → Systems Manager services | **Interface Endpoints**, where supported |
| Private EC2 → General Internet | **NAT Gateway** |
| Need no Internet/NAT for S3 | **Gateway Endpoint** |

---

# 17. PrivateLink and Cost

Interface VPC Endpoints are powered by **AWS PrivateLink**.

Architecture:

```
Private EC2
     │
     ▼
Interface Endpoint
     │
     ▼
AWS Service
```

The endpoint creates an **ENI with private IP addresses** in your subnet.

You pay for:

- Endpoint hourly usage
- Data processed

### Exam clue

> "Access AWS service privately without Internet/NAT"
> 

Think:

**VPC Endpoint / PrivateLink**

---

# 18. Direct Connect and Cost

Direct Connect provides a **dedicated private network connection** between your on-premises environment and AWS.

```
Corporate DC
      │
      │ Dedicated connection
      ▼
AWS Direct Connect
      │
      ▼
AWS
```

Useful when you need:

- Large data transfer
- Consistent network performance
- Hybrid cloud
- Long-term connectivity

But remember:

> Direct Connect isn't simply "free networking." There are connection/port and data transfer-related costs depending on the architecture.
> 

---

# 19. Direct Connect vs Internet/VPN

|  | Site-to-Site VPN | Direct Connect |
| --- | --- | --- |
| Connection | Public Internet | Dedicated/private connection |
| Setup | Fast | Usually longer |
| Cost | Generally cheaper to start | More expensive |
| Bandwidth | Internet-dependent | Higher/consistent options |
| Predictability | Lower | Higher |
| Encryption | IPsec | **Not encrypted by default** |
| Encryption option | Built-in IPsec | DX + VPN |
| Best use | Quick hybrid connectivity / backup | Large, consistent, long-term transfer |

### Exam clue

**Large amount of data + consistent bandwidth + hybrid**

→ **Direct Connect**

**Fast + inexpensive setup + encrypted connection**

→ **Site-to-Site VPN**

---

# 20. Direct Connect + VPN

Direct Connect itself does **not provide encryption by default**.

You can combine:

```
Corporate DC
      │
      ├──────── Direct Connect ────────► AWS
      │
      └──────── VPN/IPsec ─────────────► AWS
```

This can provide:

- Dedicated/private connectivity through DX
- IPsec encryption through VPN

### Exam memory

> **DX = private, not automatically encrypted**
> 
> 
> **DX + VPN = private + encrypted**
> 

---

# 21. S3 Cost Cheat Sheet

| Architecture | Main idea |
| --- | --- |
| Internet → S3 | Ingress generally free |
| S3 → Internet | Egress charge |
| S3 → CloudFront | Favorable transfer path |
| CloudFront → User | CloudFront data transfer charge |
| S3 → another Region | Cross-Region transfer charge |
| S3 Transfer Acceleration | Faster + additional cost |
| S3 → EC2 through Gateway Endpoint | Cost-efficient private access |
| S3 → EC2 through NAT Gateway | Adds NAT Gateway processing/usage costs |

---

# 22. Cost Optimization Decision Tree

For SAA-C03, use this mental decision tree:

```
Where is the traffic going?
          │
          ├── INTO AWS?
          │      └── Usually FREE
          │
          ├── OUT OF AWS?
          │      └── Think EGRESS $$$
          │
          ├── Between AZs?
          │      └── Think INTER-AZ COST
          │
          ├── Between Regions?
          │      └── Think CROSS-REGION COST
          │
          ├── Private EC2 → S3?
          │      └── Gateway Endpoint ⭐
          │
          ├── Private EC2 → DynamoDB?
          │      └── Gateway Endpoint ⭐
          │
          ├── Private EC2 → AWS service
          │   that doesn't support Gateway Endpoint?
          │      └── Interface Endpoint
          │
          ├── Private EC2 → Internet?
          │      └── NAT Gateway
          │
          └── Many users → same S3 content?
                 └── CloudFront
```

---

# 23. Most Important SAA-C03 Exam Traps

### Trap 1 — "Private EC2 needs S3"

❌ NAT Gateway isn't automatically the best answer.

✅ **S3 Gateway VPC Endpoint**

---

### Trap 2 — "Private EC2 needs DynamoDB"

❌ NAT Gateway

✅ **DynamoDB Gateway VPC Endpoint**

---

### Trap 3 — "Private EC2 needs SNS"

Gateway Endpoint doesn't support SNS.

✅ **Interface VPC Endpoint**

---

### Trap 4 — "Need Internet access from private subnet"

✅ **NAT Gateway**

Architecture:

```
Private EC2
   ↓
NAT Gateway
   ↓
Internet Gateway
   ↓
Internet
```

---

### Trap 5 — "DX is encrypted"

❌ Not by default.

✅ **Direct Connect is private but not encrypted by default.**

For encryption:

**Direct Connect + Site-to-Site VPN**

---

### Trap 6 — "Large S3 downloads from worldwide users"

Think:

✅ **CloudFront**

because caching content near users can improve latency and reduce repeated origin requests.

---

### Trap 7 — "Large data transfer from on-premises"

Think:

**Direct Connect / Snow Family / DataSync**, depending on the exact requirement.

For a **one-time massive migration**, Snow Family may be more appropriate.

For **ongoing network connectivity**, Direct Connect or VPN may be appropriate.

---

# 24. Final SAA-C03 Cost Memory Table

| If question says... | Think... |
| --- | --- |
| Traffic coming into AWS | **Ingress → usually free** |
| AWS → Internet | **Egress → $$$** |
| Different AZ | **Inter-AZ transfer cost** |
| Different Region | **Cross-Region cost** |
| Private IP communication | **Prefer private networking when appropriate** |
| Private EC2 → S3 | ⭐ **Gateway Endpoint** |
| Private EC2 → DynamoDB | ⭐ **Gateway Endpoint** |
| Private EC2 → SNS/SQS/etc. | **Interface Endpoint** |
| Private EC2 → Internet | **NAT Gateway** |
| Worldwide users → S3 content | **CloudFront** |
| Faster S3 upload over long distances | **S3 Transfer Acceleration** |
| S3 → another Region | **Cross-Region Replication + transfer cost** |
| Huge on-prem → AWS one-time transfer | **Snow Family** |
| Ongoing hybrid connectivity | **Direct Connect / VPN** |
| Dedicated + consistent network | **Direct Connect** |
| Fast setup + encrypted | **Site-to-Site VPN** |
| DX + encryption | **DX + Site-to-Site VPN** |

## 🧠 One-minute revision

> **IN = usually free. OUT = costs.**
> 
> 
> **Cross-AZ = cost. Cross-Region = cost.**
> 
> **S3/DynamoDB private access = Gateway Endpoint.**
> 
> **Other AWS services privately = Interface Endpoint.**
> 
> **Private subnet → Internet = NAT Gateway.**
> 
> **S3 content → many users = CloudFront.**
> 
> **Far-away S3 transfer speed = Transfer Acceleration.**
> 
> **Dedicated hybrid network = Direct Connect.**
> 
> **DX isn't encrypted by default → add VPN if encryption is required.**
>
