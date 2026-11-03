# AWS SAA-C03 — Scalability & High Availability

## 1. Core Concept

| Concept | Simple Meaning | How it works | Main Goal | Example |
| --- | --- | --- | --- | --- |
| **Scalability** | Ability to handle increasing/decreasing workload | Add more resources or bigger resources | Handle changing load | Website gets 10× more users |
| **Vertical Scaling** | **Scale UP/DOWN** | Increase/decrease the size of one resource | Handle more workload with a bigger resource | EC2 `t2.micro` → larger instance |
| **Horizontal Scaling** | **Scale OUT/IN** | Add/remove multiple resources | Handle workload using multiple instances | 2 EC2 → 10 EC2 |
| **High Availability (HA)** | Keep application available despite failure | Run resources across multiple AZs | Survive an AZ/data-center failure | EC2 instances in AZ-1 + AZ-2 |
| **Elasticity** | Automatically adjust capacity based on demand | Automatically scale out/in | Match capacity to current demand | ASG adds EC2 during traffic spike |

### The easiest way to remember

> **Vertical = Bigger machine**
> 
> 
> **Horizontal = More machines**
> 
> **High Availability = Multiple AZs**
> 

---

# 2. Vertical vs Horizontal Scaling

| Feature | Vertical Scaling | Horizontal Scaling |
| --- | --- | --- |
| Also called | **Scale Up / Scale Down** | **Scale Out / Scale In** |
| What changes? | Size/power of one instance | Number of instances |
| Example | `t3.small → t3.large` | `2 EC2 → 6 EC2` |
| Multiple servers required? | ❌ No | ✅ Yes |
| Distributed architecture? | Usually ❌ | Usually ✅ |
| Maximum limit | Hardware/instance size limit | Can scale to many instances |
| Complexity | Simpler | More complex |
| Availability improvement | ❌ Not necessarily | ✅ Can improve HA when spread across AZs |
| Common use | Databases, single-instance workloads | Web/app servers |
| AWS examples | RDS instance class change, ElastiCache node size | EC2 + ASG + Load Balancer |
| Main limitation | Eventually you hit the largest instance size | Application must support distributed operation |

### Example

**Vertical:**

```
Before:
EC2 t3.small
   ↓ Scale UP
EC2 t3.large
```

**Horizontal:**

```
Before:
       EC2

   ↓ Scale OUT

EC2   EC2   EC2   EC2
```

---

# 3. High Availability

| Item | High Availability |
| --- | --- |
| Meaning | Application remains available even when part of infrastructure fails |
| Main technique | Deploy across **multiple Availability Zones** |
| Minimum typical architecture | Resources in **2+ AZs** |
| Goal | Survive AZ/data-center failure |
| Does HA automatically mean scalability? | ❌ No |
| Does horizontal scaling help HA? | ✅ Yes, when instances are distributed across AZs |
| Example | EC2 in AZ-1 + EC2 in AZ-2 behind ALB |
| Database example | RDS Multi-AZ |
| AWS design principle | Avoid a single point of failure |

### Important distinction

**Scalability ≠ High Availability**

For example:

```
1 huge EC2 instance
```

This may have **high capacity**, but it is **not highly available**.

If that instance fails → application goes down.

Instead:

```
             ALB
            /   \
         AZ-1   AZ-2
          EC2    EC2
```

Now one EC2/AZ can fail while the other continues serving traffic.

---

# 4. Active vs Passive High Availability

| Type | Meaning | Example | What happens during failure? |
| --- | --- | --- | --- |
| **Active HA** | Multiple resources actively serve traffic | EC2 instances across AZs + ALB | Other instances continue serving |
| **Passive HA** | Standby resource waits for failure | RDS Multi-AZ | Standby takes over |

### Exam example

**RDS Multi-AZ**

```
Primary DB ─────── Standby DB
   AZ-1               AZ-2
      ↓ failure
   Standby becomes Primary
```

This is primarily **High Availability**, not read scaling.

> **RDS Multi-AZ ≠ Read Scaling**
> 

---

# 5. EC2: Vertical vs Horizontal vs HA

| Requirement | AWS Solution |
| --- | --- |
| Need a more powerful EC2 | **Vertical scaling** → change instance type |
| Need more EC2 instances | **Horizontal scaling** → ASG |
| Need automatic scaling | **Auto Scaling Group** |
| Need distribute traffic | **Elastic Load Balancer** |
| Need survive AZ failure | EC2 instances across **multiple AZs** |
| Need automatic replacement of unhealthy instances | **ASG** |
| Need both scaling + HA | **Multi-AZ ASG + Load Balancer** |

### Ideal EC2 architecture

```
                    Users
                      |
                     ALB
                  /       \
               AZ-1       AZ-2
              EC2 EC2    EC2 EC2
                \          /
                 Auto Scaling
                    Group
```

This provides:

- **Horizontal scalability** → more EC2
- **Elasticity** → automatically add/remove EC2
- **High availability** → multiple AZs
- **Load distribution** → ALB
- **Automatic replacement** → ASG

---

# 6. Scalability vs Elasticity

| Concept | Meaning | Example |
| --- | --- | --- |
| **Scalability** | Ability to handle increased workload | System can grow from 2 → 20 EC2 |
| **Elasticity** | Automatically adjust capacity according to demand | ASG automatically adds/removes EC2 |
| **Manual scaling** | Administrator changes capacity | You manually increase EC2 count |
| **Automatic scaling** | AWS adjusts capacity automatically | CloudWatch + ASG |

### Easy memory

> **Scalability = Can grow**
> 
> 
> **Elasticity = Automatically grows/shrinks**
> 

---

# 7. Scaling Direction

| Action | Meaning | Example |
| --- | --- | --- |
| **Scale Up** | Increase resource size | `t3.small → t3.large` |
| **Scale Down** | Decrease resource size | `t3.large → t3.small` |
| **Scale Out** | Increase number of resources | `2 EC2 → 5 EC2` |
| **Scale In** | Decrease number of resources | `5 EC2 → 2 EC2` |

### Memory trick

**UP/DOWN = size**

**OUT/IN = number**

---

# 8. Common AWS Services and Their Scaling/HA Role

| AWS Service | Vertical | Horizontal | HA | Important Point |
| --- | --- | --- | --- | --- |
| **EC2** | ✅ | ✅ | ✅ | ASG enables automatic horizontal scaling |
| **RDS** | ✅ | Limited/specialized | ✅ | Multi-AZ for HA; Read Replicas for read scaling |
| **Aurora** | ✅ | ✅ | ✅ | Read Replicas/Aurora Replicas for read scaling |
| **DynamoDB** | Managed | ✅ | ✅ | Automatically distributed |
| **ElastiCache** | ✅ | ✅ | ✅ | Depends on Redis/Valkey/Memcached architecture |
| **Lambda** | N/A | Automatic | ✅ | Automatically scales concurrent executions |
| **S3** | Managed | Automatic | ✅ | Massive automatic scalability |
| **ALB** | Managed | Automatic | ✅ | Designed to scale across AZs |
| **ASG** | — | ✅ | ✅ | Core EC2 scaling mechanism |

---

# 9. RDS: Important Exam Distinction

This is a **very common SAA-C03 trap**.

| Requirement | RDS Feature |
| --- | --- |
| Database needs AZ-level failure protection | **RDS Multi-AZ** |
| Need read performance | **Read Replica** |
| Need additional read capacity | **Read Replicas** |
| Need automatic failover | **Multi-AZ** |
| Need disaster/HA standby | **Multi-AZ** |
| Need horizontal read scaling | **Read Replica** |
| Need bigger database machine | **Vertical scaling / instance class change** |

### Remember

```
Multi-AZ
= HA / Failover

Read Replica
= Read Scaling
```

---

# 10. High Availability vs Fault Tolerance

| Concept | High Availability | Fault Tolerance |
| --- | --- | --- |
| Goal | Minimize downtime | Continue operating despite failure |
| Architecture | Usually redundant resources | Highly redundant/duplicate resources |
| Failure handling | May involve failover/recovery | Designed to continue with little/no interruption |
| Cost | Usually lower | Usually higher |
| Example | RDS Multi-AZ | Specialized fault-tolerant architecture |

For SAA-C03, don't automatically treat **HA = zero downtime**. HA means designing to **reduce downtime and survive failures**.

---

# 11. Most Important Exam Scenarios

| Scenario | Correct Concept/Solution |
| --- | --- |
| Website traffic suddenly increases | **Horizontal scaling / ASG** |
| Need bigger EC2 because application requires more CPU/RAM | **Vertical scaling** |
| Need application available if AZ-1 fails | **Multi-AZ architecture** |
| Need automatic EC2 replacement after failure | **ASG** |
| Need automatically add EC2 when CPU increases | **ASG + CloudWatch scaling policy** |
| Need distribute traffic among EC2 | **ALB/NLB** |
| Need web application to scale automatically | **ALB + ASG** |
| Need database failover | **RDS Multi-AZ** |
| Need database read performance | **RDS Read Replica** |
| Need application to handle unpredictable traffic | **Auto Scaling / elasticity** |
| Need maximum availability | Deploy across **multiple AZs** |
| One EC2 is overloaded | **Scale vertically or horizontally**, depending on architecture |
| Traffic varies during the day | **Dynamic scaling** |
| Traffic pattern is predictable | **Scheduled scaling** |
| Future workload can be forecast from historical patterns | **Predictive scaling** |

---

# 12. Final Master Table — SAA-C03 Revision

| Concept | One-Line Meaning | Direction | AWS Example | Main Purpose | Exam Keyword |
| --- | --- | --- | --- | --- | --- |
| **Vertical Scaling** | Make one machine bigger | ↑ Size | EC2 larger instance | More CPU/RAM | **Scale Up** |
| **Vertical Scale Down** | Make one machine smaller | ↓ Size | EC2 smaller instance | Reduce capacity/cost | **Scale Down** |
| **Horizontal Scaling** | Add more machines | ↑ Number | ASG | Handle more traffic | **Scale Out** |
| **Horizontal Scale In** | Remove machines | ↓ Number | ASG | Reduce unused capacity | **Scale In** |
| **Scalability** | System can handle greater load | Both | EC2/ASG | Handle growth | **Can handle more** |
| **Elasticity** | Automatically adjust capacity | Both | ASG | Match demand | **Automatic** |
| **High Availability** | Survive infrastructure failure | Multi-AZ | ALB + ASG across AZs | Minimize downtime | **Multiple AZs** |
| **RDS Multi-AZ** | Standby database in another AZ | HA | RDS | Automatic failover | **HA, not read scaling** |
| **RDS Read Replica** | Additional DB copies for reads | Horizontal read scaling | RDS | Increase read capacity | **Read scaling** |
| **ASG** | Automatically manages EC2 fleet | Horizontal | EC2 ASG | Scale + replace | **Min/Desired/Max** |
| **ALB** | Distributes application traffic | Horizontal architecture | ALB + EC2 | Load distribution | **Traffic distribution** |
| **Multi-AZ ASG** | EC2 fleet across AZs | Horizontal + HA | ASG + ALB | Scale + availability | **Best EC2 HA pattern** |

## ⭐ One-minute memory map

```
SCALABILITY
│
├── Vertical
│     └── Bigger machine
│         └── Scale UP / DOWN
│
└── Horizontal
      └── More machines
          └── Scale OUT / IN
                 │
                 └── ASG
                       │
                       ├── Automatic scaling
                       ├── Min / Desired / Max
                       ├── Replace unhealthy EC2
                       └── Multi-AZ → High Availability

HIGH AVAILABILITY
│
└── Multiple AZs
      │
      ├── EC2 + ASG + ALB
      └── RDS Multi-AZ
```

**Golden exam rule:**

**“Bigger server” → Vertical scaling.**

**“More servers” → Horizontal scaling.**

**“Automatically add/remove servers” → Elasticity/ASG.**

**“Survive AZ failure” → High Availability/Multi-AZ.**

**“Database read scaling” → Read Replica.**

**“Database failover/HA” → Multi-AZ.**
