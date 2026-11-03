# 5. Master Comparison Table

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **Lambda needs private VPC resources** | Lambda VPC configuration |
| **Lambda + EFS** | Lambda VPC |
| **Massive Lambda concurrency inside VPC** | Check subnet IP/ENI capacity |
| **Small subnet CIDR** | Limited IP addresses |
| **No available IP addresses** | Expand/reconfigure subnet capacity |
| **ENI quota exceeded** | VPC/ENI capacity issue |
| **EC2ThrottledException with Lambda VPC** | **ENI/IP/quota problem** |
| **Shared filesystem for Lambda** | EFS |
| **Private subnet → Internet** | NAT Gateway |
| **Private subnet → AWS service privately** | VPC Endpoint |
| **Port/network traffic blocked** | Security Group/NACL |
| **AccessDenied / missing AWS API permission** | IAM execution role |
| **Stateful network firewall** | Security Group |
| **Stateless subnet firewall** | NACL |
| **`/27` subnet** | 32 total, 27 usable IPv4 addresses |
| **Peak-time Lambda failures** | Capacity/scaling bottleneck |
| **High concurrency + small subnet** | **Subnet IP exhaustion risk** |

---

# 6. Side-by-Side Option Analysis

| Option | Underlying Idea | Good? | One-line reason |
| --- | --- | --- | --- |
| **1** | Subnet doesn't have enough IP addresses | ✅ | High Lambda scaling can exhaust the small subnet's available IP capacity |
| **2** | Missing NAT Gateway | ❌ | NAT is needed for private-subnet internet access, not for this VPC capacity problem |
| **3** | ENI/VPC quota or IP capacity exceeded | ✅ | Directly matches Lambda VPC scaling and `EC2ThrottledException` |
| **4** | Security group blocks outbound traffic | ❌ | Would cause connectivity failures, not the described resource-throttling issue |
| **5** | IAM role lacks VPC permissions | ❌ | IAM authorization failures are different from VPC/EC2 resource throttling |

---

# 7. Clue-by-Clue Post-Mortem

### "Amazon VPC"

🔥 Immediately think:

```
Subnets
Route tables
IP addresses
ENIs
Security groups
NACLs
Endpoints
NAT
```

This tells you the problem may be networking-related.

---

### "`10.31.0.0/27`"

🔥 This is intentionally specific.

Calculate:

```
/27
↓
32 total IPv4 addresses
↓
27 usable after AWS reserves 5
```

For a massively scaling Lambda workload, that's a warning sign.

---

### "connected to the on-premises data center"

This establishes hybrid connectivity.

But it isn't the key to the failure.

Don't get distracted by:

```
On-premises
VPN
Direct Connect
```

Nothing in the failure description indicates the on-premises connection is broken.

---

### "Lambda function"

Now think about Lambda's behavior when configured for VPC access.

```
Lambda
  ↓
VPC
  ↓
Subnet
  ↓
Networking resources
```

---

### "process massive amounts ... every minute"

🔥🔥 This tells you there may be **high concurrency/scaling pressure**.

```
High workload
    ↓
More Lambda invocations
    ↓
More VPC networking demand
```

---

### "store the results to EFS"

This explains **why Lambda needs VPC connectivity**.

```
Lambda
   ↓
VPC
   ↓
EFS
```

It also makes the VPC networking configuration intentional rather than accidental.

---

### "connecting the Lambda function to the VPC"

🔥🔥 Now the VPC networking limits become extremely important.

If Lambda weren't VPC-connected, this particular subnet IP capacity issue wouldn't be the same concern.

---

### "increase in invocation errors"

This says Lambda is having trouble invoking/executing at scale.

---

### "`EC2ThrottledException`"

🔥🔥🔥 **Major clue.**

Don't think:

```
"EC2 instance is overloaded"
```

Instead think:

```
Lambda + VPC
      ↓
Lambda needs VPC networking operations
      ↓
EC2 networking APIs/resources
      ↓
Throttling / quota
```

This points strongly toward Option 3.

---

### "at certain times of the day"

🔥 This indicates **peak load**.

```
Normal:
Lambda demand → okay

Peak:
Lambda demand → ↑↑↑
                   ↓
             networking capacity
                   ↓
                exhausted
```

That strongly supports Options 1 and 3.

---

# 8. Why the Tempting Wrong Answers Are Tempting

## 🪤 Trap #1 — "No NAT Gateway"

This is a classic AWS exam association:

> **Lambda in private subnet → NAT Gateway**
> 

That's **not always true**.

Ask:

> **Does Lambda need to access the public internet?**
> 

If yes:

```
Private subnet → NAT Gateway → Internet
```

If Lambda only needs private VPC resources such as EFS:

```
Lambda → EFS
```

NAT isn't required.

So:

> **NAT Gateway solves internet connectivity, not subnet IP exhaustion.**
> 

---

## 🪤 Trap #2 — Security Group

Security groups are another common networking distractor.

A test-taker might see:

> "Lambda connected to VPC"
> 

and immediately think:

> "Maybe the security group blocks traffic."
> 

But the error is:

> **`EC2ThrottledException`**
> 

and the failures occur during peak load.

That is a resource-capacity signal, not a packet-filtering signal.

Remember:

```
Security Group
    ↓
"Is this traffic allowed?"

ENI/IP quota
    ↓
"Do I have enough networking capacity?"
```

---

# 9. Final Answer Recap + Memory Trick

## ✅ Correct Answers

### **Option 1**

> The Lambda function is placed in a VPC subnet with limited IP address capacity.
> 

### **Option 3**

> The Lambda function exceeds the VPC quota for Elastic Network Interfaces (ENIs) or available IP addresses in the subnet.
> 

### Why?

```
VPC CIDR = /27
      ↓
Only 32 total IPs
      ↓
27 usable subnet IPs
      ↓
Massive Lambda workload
      ↓
High concurrency
      ↓
VPC networking demand increases
      ↓
IP / ENI capacity or quota reached
      ↓
EC2ThrottledException
```

### 🧠 Memory Tricks

> **"Lambda + VPC + high concurrency + small subnet = check IPs and ENIs."**
> 

> **"`EC2ThrottledException` in VPC Lambda → think ENI/IP/quota before NAT, SG, or IAM."**
> 

---

# 10. Bonus: Related Exam Traps to Watch For

### 1. "Lambda in a private subnet cannot access the internet"

✅ **NAT Gateway**

```
Lambda → Private Subnet → NAT Gateway → Internet
```

---

### 2. "Lambda cannot connect to EFS because TCP 2049 is blocked"

✅ **Security Group**

```
Lambda SG
   │
   │ TCP 2049
   ▼
EFS Mount Target
```

---

### 3. "Lambda receives AccessDenied when writing to S3"

✅ **IAM execution role**

```
Lambda
 ↓
IAM Role
 ↓
s3:PutObject
 ↓
S3
```

---

### 4. "Lambda needs to access DynamoDB privately without going through the internet"

✅ **DynamoDB VPC Gateway Endpoint**

```
Lambda
  ↓
VPC
  ↓
DynamoDB Gateway Endpoint
  ↓
DynamoDB
```

No NAT Gateway is required for that path.

---

### 5. "Lambda has huge concurrency and the subnet runs out of private IP addresses"

✅ **Increase subnet IP capacity / use appropriately sized subnets**

```
Small subnet
     ↓
IP exhaustion
     ↓
Lambda VPC scaling problem
```

---

# 🔥 FINAL FLASHCARD — LAMBDA + VPC NETWORKING

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **Lambda needs EFS** | Lambda VPC |
| **Lambda needs private RDS/EC2/EFS** | Lambda VPC |
| **Lambda + high concurrency + VPC** | Check IP/ENI capacity |
| **Small subnet CIDR** | IP exhaustion risk |
| **`EC2ThrottledException`** | **ENI/IP/quota problem** |
| **No available subnet IPs** | Subnet capacity |
| **ENI quota exceeded** | VPC/ENI quota |
| **Private subnet needs Internet** | NAT Gateway |
| **Private subnet → AWS service privately** | VPC Endpoint |
| **Port blocked / connection timeout** | Security Group/NACL |
| **AccessDenied** | IAM |
| **Shared filesystem** | EFS |
| **`/27` subnet** | 32 total / 27 usable IPv4 addresses |
| **Peak-time failures** | Capacity/scaling bottleneck |
| **Massive Lambda scaling inside VPC** | **Watch ENIs + subnet IPs** |

## 🎯 The exam decision tree

```
Lambda failure in VPC
        │
        ▼
What does the error/clue suggest?
        │
   ┌────┼──────────────┬──────────────┐
   │    │              │              │
   ▼    ▼              ▼              ▼
Internet  Network      IAM           Capacity
problem   blocked      problem       problem
   │        │            │              │
   ▼        ▼            ▼              ▼
 NAT      SG/NACL      IAM role     ENI/IP quota
 Gateway                              │
                                     ▼
                         EC2ThrottledException
```

### Final answer:

**✅ Option 1 + Option 3**

**Core concept:**

**When Lambda is VPC-connected and scales heavily, a small subnet can run out of private IP addresses or hit ENI/VPC quotas, producing EC2-related throttling errors.**
