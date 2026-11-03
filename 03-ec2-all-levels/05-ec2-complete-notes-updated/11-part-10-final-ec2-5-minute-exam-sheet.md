# Part 10 — EC2 Final 5-Minute Exam Sheet

This is the **last-minute EC2 decision engine**.

Read the **Question wording** → identify the **hidden requirement** → jump to the **feature**.

| # | **Question wording / trigger phrase** | **Hidden requirement** | **EC2 feature / answer** | **Why** | **Common wrong choices** |
| --- | --- | --- | --- | --- | --- |
| 1 | **“Can be interrupted”**, non-critical, batch | Workload tolerates interruption | **Spot Instance** | Massive discount for flexible workloads | On-Demand, RI |
| 2 | **“Non-production” + “batch processing” + “interruptible”** | Lowest possible compute cost | **Spot** | Best fit for fault-tolerant workloads | Reserved, Capacity Reservation |
| 3 | **“Steady workload”**, predictable usage | Long-term cost optimization | **Reserved Instance / Savings Plan** | Discount for committed usage | Spot |
| 4 | **“1–3 years” + predictable EC2 usage** | Commitment-based discount | **RI / Savings Plan** | Lower cost than On-Demand | Spot |
| 5 | **“Flexible across instance types/regions/services”** | Need commitment flexibility | **Compute Savings Plan** | Most flexible Savings Plan | EC2 Instance Savings Plan |
| 6 | **“Specific EC2 instance family/region” + commitment** | Less flexibility but deeper EC2-specific optimization | **EC2 Instance Savings Plan** | Discount tied to EC2 instance usage | Compute Savings Plan |
| 7 | **“Specific instance type + AZ + must have capacity”** | Guaranteed capacity at a particular AZ | **On-Demand Capacity Reservation** | Reserves capacity in that AZ | RI, Spot |
| 8 | **“Critical workload” + “capacity must be available in this AZ”** | Capacity assurance, not necessarily discount | **Capacity Reservation** | Guarantees On-Demand capacity | Spot |
| 9 | **“Predictable recurring capacity” + cost savings** | Discount rather than simply capacity guarantee | **RI / Savings Plan** | Capacity Reservation itself is primarily availability assurance | Capacity Reservation |
| 10 | **“Existing software license” + “physical sockets/cores”** | Need visibility/control over physical host | **Dedicated Host** | Host-level visibility supports BYOL scenarios | Dedicated Instance |
| 11 | **“Dedicated physical server” / physical tenancy isolation** | Hardware isolation | **Dedicated Host / Dedicated Instance** | Both provide dedicated hardware tenancy | Shared tenancy |
| 12 | **“Control over physical server”** | Host-level placement/license management | **Dedicated Host** | You get a dedicated physical EC2 host | Dedicated Instance |
| 13 | **“Dedicated hardware” but no host-level control required** | Dedicated tenancy | **Dedicated Instance** | Runs on hardware dedicated to your account | Dedicated Host |
| 14 | **“Very low network latency” + HPC** | Instances need to communicate extremely quickly | **Cluster Placement Group** | Places instances close together in one AZ | Spread, Partition |
| 15 | **“HPC” + “high network throughput” + “low latency”** | Network-intensive compute | **Cluster Placement Group + Enhanced Networking** | Physical proximity + high-speed networking | Spread Placement Group |
| 16 | **“Maximum hardware isolation”** | Minimize correlated hardware failure | **Spread Placement Group** | Instances placed on separate underlying hardware | Cluster |
| 17 | **“Critical instances should not share hardware”** | Failure isolation | **Spread Placement Group** | Reduces simultaneous hardware failure | Partition |
| 18 | **“Kafka” / “Cassandra” / “HDFS”** | Distributed system needs rack-level failure isolation | **Partition Placement Group** | Instances distributed across partitions/racks | Cluster |
| 19 | **“Hundreds of EC2 instances” + distributed workload** | Need scalable hardware/rack distribution | **Partition Placement Group** | Designed for large distributed applications | Spread |
| 20 | **“Partition failure should not affect other partitions”** | Failure-domain isolation | **Partition Placement Group** | Partitions use different racks | Cluster |
| 21 | **“High sequential read/write” + “very large datasets” + “local storage”** | Extremely high local disk throughput | **Storage Optimized EC2** | Optimized for local NVMe/HDD storage workloads | Compute Optimized, Memory Optimized |
| 22 | **“CPU-intensive” / “compute-heavy”** | High CPU-to-memory ratio | **Compute Optimized (C)** | Designed for compute-bound workloads | Memory Optimized |
| 23 | **“Large in-memory dataset” / “memory-intensive”** | High RAM requirement | **Memory Optimized (R/X)** | Large memory capacity | Compute Optimized |
| 24 | **“GPU” / graphics rendering / ML inference** | Parallel GPU processing | **Accelerated Computing (G/P)** | GPU acceleration | Compute Optimized |
| 25 | **“FPGA” / reconfigurable hardware** | Hardware acceleration with programmable logic | **F instances** | FPGA-based acceleration | G/P |
| 26 | **“Occasional CPU spikes”** | Baseline CPU + temporary bursts | **T-series** | Uses CPU credits | C/M |
| 27 | **“Web server has low normal CPU but occasional spikes”** | Burstable compute | **T-series** | Credits absorb temporary CPU demand | Compute Optimized |
| 28 | **“CPU usage consistently high”** | Sustained compute | **C-series**, not T-series | T-series is for burstable workloads | T-series |
| 29 | **“Very fast restart” + “preserve memory state”** | Resume OS/application state | **EC2 Hibernate** | RAM state is saved to EBS root volume | Stop |
| 30 | **“Stop during off-hours and resume quickly”** | Avoid rebuilding application state | **Hibernate** | Preserves RAM state | Terminate |
| 31 | **“Do not need RAM state” + cost saving** | Shut down instance but retain EBS | **Stop** | EBS persists, instance compute charges stop | Hibernate |
| 32 | **“Instance no longer needed”** | Permanently remove compute resource | **Terminate** | Instance is deleted | Stop |
| 33 | **“Persistent block storage”** | Data must survive instance termination | **EBS** | Network-attached persistent block storage | Instance Store |
| 34 | **“Temporary data” / cache / scratch space** | Data can be lost | **Instance Store** | Very fast local storage but ephemeral | EBS |
| 35 | **“Data must survive EC2 termination”** | Persistence | **EBS** | EBS is persistent | Instance Store |
| 36 | **“Highest IOPS” / “mission-critical database”** | Sustained high I/O performance | **io1/io2** | Provisioned IOPS SSD | gp2/gp3 |
| 37 | **“More than 16,000 IOPS”** | Beyond general-purpose SSD range | **io1/io2** | Designed for very high IOPS | gp2/gp3 |
| 38 | **“Independent IOPS and throughput”** | Tune performance separately from size | **gp3** | IOPS and throughput configurable independently | gp2 |
| 39 | **“General-purpose SSD” + cost-effective** | Balanced storage performance | **gp3** usually preferred | Better independent performance controls | io2 |
| 40 | **“IOPS linked to volume size”** | Older general-purpose SSD behavior | **gp2** | IOPS scale with GiB | gp3 |
| 41 | **“Large sequential throughput” + Big Data/log processing** | Throughput > random IOPS | **st1** | Throughput-optimized HDD | io2 |
| 42 | **“Lowest-cost HDD” + infrequently accessed** | Minimize storage cost | **sc1** | Cold HDD | st1 |
| 43 | **“Boot volume”** | Root OS requires bootable EBS | **gp2/gp3 or io1/io2** | HDD st1/sc1 cannot be boot volumes | st1/sc1 |
| 44 | **“10,000 IOPS database”** | Provisioned IOPS | **io1/io2** | Sustained predictable IOPS | st1/sc1 |
| 45 | **“500 MB/s sequential throughput”** | Throughput-intensive workload | **st1** | Optimized for sequential throughput | sc1 |
| 46 | **“Minimal latency + extremely high IOPS”** | High-performance SSD | **io2 Block Express** | Designed for very high-performance workloads | st1 |
| 47 | **“High network throughput”** | Improve packet/network performance | **Enhanced Networking** | Uses SR-IOV / ENA for higher network performance | EBS-optimized |
| 48 | **“HPC networking” / “very low network latency”** | High-performance inter-instance networking | **EFA** | Specialized HPC networking | Standard networking |
| 49 | **“ENA”** | Modern AWS high-performance network adapter | **Enhanced Networking** | Provides higher bandwidth/lower latency | EBS optimization |
| 50 | **“EBS I/O bottleneck”** | Dedicated bandwidth for EBS traffic | **EBS-optimized instance** | Dedicated EBS bandwidth | Enhanced Networking |
| 51 | **“Network bottleneck”** | EC2-to-network performance | **Enhanced Networking** | Optimizes network I/O | EBS-optimized |
| 52 | **“EBS performance + network performance”** | Both resources need optimization | **Enhanced Networking + EBS-optimized** where supported | They solve different bottlenecks | Treating them as the same feature |
| 53 | **“Configure EC2 automatically when launched”** | Initialization/bootstrap | **User Data** | Script executes during instance launch | AMI |
| 54 | **“Install packages/configure software automatically at boot”** | Automated initialization | **User Data** | Bootstrap script | Manual SSH |
| 55 | **“Reusable preconfigured server image”** | Capture OS + installed software/config | **AMI** | Template for launching instances | User Data |
| 56 | **“Same application configuration repeatedly”** | Standardized machine image | **AMI / Golden Image** | Reusable baseline | Reinstall manually |
| 57 | **“Automatically add/remove EC2 instances based on load”** | Elastic fleet size | **Auto Scaling Group** | Maintains desired capacity | Manually launching EC2 |
| 58 | **“CPU utilization target = 50%”** | Maintain a metric around target | **Target Tracking Scaling** | ASG automatically adjusts capacity | Simple Scaling |
| 59 | **“CPU > 70%, add 2 instances”** | Different response based on thresholds | **Step Scaling** | Multiple adjustment steps | Target Tracking |
| 60 | **“Scale out at 8 AM, scale in at 8 PM”** | Known time-based traffic | **Scheduled Scaling** | Capacity changes according to schedule | Predictive Scaling |
| 61 | **“Predictable recurring traffic pattern”** | Forecast future demand | **Predictive Scaling** | Uses historical patterns | Simple Scaling |
| 62 | **“One alarm → add/remove fixed number”** | Basic threshold reaction | **Simple Scaling** | One scaling adjustment + cooldown | Step Scaling |
| 63 | **“New AMI should be used for future ASG instances”** | Change launch configuration | **Launch Template** | ASG uses template to launch instances | Changing existing EC2 manually |
| 64 | **“Launch configuration”** | Legacy EC2 launch definition | **Launch Configuration** | Older mechanism; Launch Templates preferred | Treating it as current best practice |
| 65 | **“ASG behind ALB”** | Distribute application traffic + replace unhealthy instances | **ALB + Target Group + ASG** | ALB routes; ASG scales/replaces | NLB alone |
| 66 | **“Spot instances in Auto Scaling”** | Cost optimization + scalable fleet | **ASG Mixed Instances Policy** | Mix On-Demand and Spot | Only On-Demand |
| 67 | **“Spot interruption risk in ASG”** | Maintain capacity despite interruption | **Capacity Rebalancing** | Helps proactively replace at-risk Spot instances | Waiting for termination |
| 68 | **“Update AMI for running ASG fleet”** | Roll out new instance configuration | **Instance Refresh** | Replaces instances progressively | Manually terminate all instances |
| 69 | **“Need persistent storage when instance stops/terminates”** | Keep data | **EBS** | Persistent storage | Instance Store |
| 70 | **“Move EBS volume to another AZ”** | EBS is AZ-scoped | **Snapshot → create new EBS volume in target AZ** | EBS volume cannot directly attach across AZs | Directly attaching across AZ |
| 71 | **“Backup EBS volume”** | Point-in-time block-storage backup | **EBS Snapshot** | Incremental snapshot mechanism | AMI unless entire instance is needed |
| 72 | **“Encrypt existing unencrypted EBS”** | Convert storage to encrypted | **Snapshot → copy/encrypt snapshot → create encrypted volume** | Encryption can be introduced through snapshot copy | Encrypting existing volume in place |
| 73 | **“Encrypted EBS snapshot”** | Backup remains protected | **Encrypted snapshot** | Snapshots of encrypted volumes are encrypted | Unencrypted snapshot |
| 74 | **“Share one EBS volume between multiple EC2 instances”** | Concurrent attachment | **EBS Multi-Attach with io1/io2 where supported** | Allows multiple supported instances to attach same volume | gp3, st1 |
| 75 | **“Resize EBS without detaching”** | Online storage modification | **Elastic Volumes** | Modify size/type/IOPS without traditional detach workflow | Recreate instance |
| 76 | **“Automatically manage EBS snapshots”** | Scheduled lifecycle/retention | **Data Lifecycle Manager (DLM)** | Automates snapshot creation/retention | Manual snapshots |
| 77 | **“Need EC2 instance access to AWS services without hardcoded credentials”** | Temporary AWS permissions | **IAM Role attached through Instance Profile** | Credentials are supplied dynamically | Access keys in code |
| 78 | **“EC2 needs S3 access”** | Workload identity | **IAM Role** | Avoids embedded long-lived access keys | IAM User credentials |
| 79 | **“Access AWS APIs from EC2 securely”** | Identity-based authorization | **IAM Role + Instance Profile** | Temporary credentials | Hardcoded access key/secret |
| 80 | **“Connect to EC2 without opening port 22”** | Secure management without SSH exposure | **Systems Manager Session Manager** | No inbound SSH required | Bastion Host |
| 81 | **“Private EC2 instance needs administrative access”** | Management without public IP | **SSM Session Manager** | Works without public SSH exposure when prerequisites/connectivity are configured | Public IP + SSH |
| 82 | **“Traditional SSH jump server”** | Centralized SSH entry point | **Bastion Host** | SSH traffic goes through hardened jump host | Session Manager |
| 83 | **“Metadata service security”** | Protect instance metadata credentials | **IMDSv2** | Token-based metadata access | IMDSv1 |
| 84 | **“Prevent SSRF attacks against EC2 metadata”** | Harden metadata access | **IMDSv2** | Requires session token | IMDSv1 |
| 85 | **“Encrypt EBS at rest”** | Storage encryption | **EBS Encryption + KMS** | Encrypts volume/snapshots transparently | Security Group |
| 86 | **“EC2 access to another AWS service”** | Identity authorization | **IAM Role** | Controls API permissions | Security Group |
| 87 | **“Control which IP/port can reach EC2”** | Network traffic filtering | **Security Group / NACL** | Network-layer controls | IAM |
| 88 | **“Only this application can call S3”** | API authorization | **IAM** | Identity-based authorization | Security Group |
| 89 | **“Application must survive Spot interruption”** | Stateless/fault-tolerant architecture | **Spot + ASG / queue-based architecture** | Replace interrupted instances | Single Spot instance |
| 90 | **“Queue workers can disappear and another worker continues”** | Workload is interruption-tolerant | **Spot Instances** | Ideal for disposable workers | Dedicated Host |
| 91 | **“Video transcoding workers poll a queue”** | Jobs can be retried by another worker | **Spot Instances** | Classic interruption-tolerant workload | On-Demand |
| 92 | **“Batch processing / data analysis / rendering”** | Flexible completion time | **Spot** | Excellent cost reduction | Capacity Reservation |
| 93 | **“Database must remain highly available”** | Don't rely on disposable compute | **On-Demand/RI/SP + HA architecture** | Spot interruption is unsuitable for primary DB workloads | Spot as primary DB |
| 94 | **“Guaranteed EC2 capacity but no long-term commitment”** | Capacity reservation | **Capacity Reservation** | Reserves capacity while active | RI |
| 95 | **“Discount + guaranteed capacity”** | Both cost commitment and capacity assurance | **RI/Savings Plan + Capacity Reservation**, when requirements support both | These solve different problems | Assuming RI itself always guarantees AZ capacity |
| 96 | **“Need capacity only at a recurring time”** | Planned capacity requirement | **Scheduled Capacity Reservation / appropriate reservation strategy** | Match capacity planning to known demand | Permanent over-provisioning |
| 97 | **“Need cheapest possible EC2”** | Cost is primary requirement and workload can tolerate interruption | **Spot** | Up to very large discounts vs On-Demand | Dedicated Host |
| 98 | **“Need predictable price and flexible commitment”** | Long-term discount | **Savings Plan** | Flexible commitment model | Spot |
| 99 | **“Need exact physical host for licensing”** | Physical host affinity | **Dedicated Host** | Host visibility | Dedicated Instance |
| 100 | **“Need dedicated tenancy only”** | Hardware isolation | **Dedicated Instance** | No need for host-level management | Dedicated Host |

---

# 🔥 The Ultimate EC2 Decision Tree

When you see an EC2 question, **don't start by thinking about the service name**.

Ask these questions in order:

### 1️⃣ Is the workload interruptible?

> **YES → SPOT**
> 

```
Batch
Data analysis
Video transcoding
Non-production
Flexible workload
Can restart
Can tolerate interruption
        ↓
      SPOT
```

---

### 2️⃣ Is the workload predictable?

> **YES → RI / SAVINGS PLAN**
> 

```
Steady
Predictable
1–3 year commitment
Long-running
        ↓
RI / Savings Plan
```

**Need flexibility across instance types/services?**

→ **Compute Savings Plan**

**Mostly committed to a specific EC2 instance family/region?**

→ **EC2 Instance Savings Plan**

---

### 3️⃣ Do you need guaranteed capacity in a specific AZ?

> **CAPACITY RESERVATION**
> 

```
"must have capacity"
"specific AZ"
"critical workload"
        ↓
Capacity Reservation
```

⚠️ **Capacity Reservation ≠ discount mechanism.**

That's one of the biggest exam traps.

---

### 4️⃣ Is physical hardware important?

> **Dedicated Host / Dedicated Instance**
> 

```
Physical host visibility
BYOL
socket/core licensing
        ↓
Dedicated Host
```

```
Dedicated tenancy
hardware isolation
        ↓
Dedicated Instance
```

### Memory:

> **HOST = “I care about the HOST.”**
> 

> **INSTANCE = “I only care that my INSTANCE is dedicated.”**
> 

---

# 🧠 Placement Group 3-Second Rule

| Question says | Pick |
| --- | --- |
| **LOW LATENCY / HPC / HIGH NETWORK THROUGHPUT** | 🟢 **Cluster** |
| **MAXIMUM HARDWARE ISOLATION / critical instances separated** | 🔵 **Spread** |
| **KAFKA / CASSANDRA / HDFS / hundreds of instances / racks** | 🟣 **Partition** |

### Brain shortcut

> **Cluster = Close**
> 

> **Spread = Separate**
> 

> **Partition = Distributed racks**
> 

---

# 💾 Storage 5-Second Rule

| Question says | Pick |
| --- | --- |
| **Persistent block storage** | **EBS** |
| **Temporary local ultra-fast storage** | **Instance Store** |
| **General-purpose SSD** | **gp3** |
| **Very high sustained IOPS / database** | **io2/io1** |
| **Large sequential throughput / Big Data** | **st1** |
| **Lowest-cost infrequent HDD** | **sc1** |
| **Boot volume** | **gp2/gp3/io1/io2** |
| **Independent IOPS + throughput** | **gp3** |
| **Multiple EC2 attachments** | **Multi-Attach io1/io2** |

### Brain shortcut

> **GP = General Purpose**
> 

> **IO = IOPS**
> 

> **ST = Throughput**
> 

> **SC = Cheap/Cold**
> 

---

# ⚡ EC2 Performance 5-Second Rule

| Requirement | Pick |
| --- | --- |
| **CPU-heavy** | **C** |
| **Memory-heavy** | **R / X** |
| **GPU** | **G / P** |
| **FPGA** | **F** |
| **Storage-heavy** | **D / I / H** depending on storage characteristics |
| **Occasional CPU spikes** | **T** |
| **Extreme network performance** | **Enhanced Networking / ENA** |
| **HPC ultra-low-latency networking** | **EFA** |
| **Dedicated EBS bandwidth** | **EBS-optimized** |

### Most important distinction

**Enhanced Networking ≠ EBS-optimized**

```
Network bottleneck
       ↓
Enhanced Networking / ENA
```

```
EBS I/O bottleneck
       ↓
EBS-optimized
```

---

# 🚀 AMI vs User Data

| Requirement | Answer |
| --- | --- |
| **Reusable preconfigured server** | **AMI** |
| **Golden image** | **AMI** |
| **OS + installed software already prepared** | **AMI** |
| **Run script when instance launches** | **User Data** |
| **Install packages at boot** | **User Data** |
| **Configure instance automatically at startup** | **User Data** |

### Brain shortcut

> **AMI = WHAT the server IS**
> 

> **User Data = WHAT the server DOES at startup**
> 

---

# 💤 Stop vs Hibernate vs Terminate

| State | EBS | RAM preserved? | Instance exists? | Main use |
| --- | --- | --- | --- | --- |
| **Stop** | ✅ | ❌ | ✅ | Save compute cost |
| **Hibernate** | ✅ | ✅ | ✅ | Resume quickly |
| **Terminate** | Usually root deleted by default | ❌ | ❌ | Permanently remove |

### Trigger

**“Preserve memory state” → Hibernate**

**“Just shut it down” → Stop**

**“Delete it” → Terminate**

---

# 🔐 EC2 Security 5-Second Rule

| Question wording | Pick |
| --- | --- |
| **EC2 calls S3/API securely** | **IAM Role + Instance Profile** |
| **Don't hardcode access keys** | **IAM Role** |
| **Access private EC2 without SSH/22** | **SSM Session Manager** |
| **Traditional SSH jump server** | **Bastion Host** |
| **Protect metadata credentials / SSRF** | **IMDSv2** |
| **Encrypt EBS** | **EBS Encryption + KMS** |
| **Control network ports/IPs** | **Security Group/NACL** |
| **Control AWS API actions** | **IAM** |

---

# 📈 ASG + EC2 5-Second Rule

| Trigger | Answer |
| --- | --- |
| **Automatically add/remove EC2** | **Auto Scaling Group** |
| **Keep CPU around 50%** | **Target Tracking** |
| **CPU > X → add different amounts depending on threshold** | **Step Scaling** |
| **Scale at specific times** | **Scheduled Scaling** |
| **Forecast predictable future demand** | **Predictive Scaling** |
| **Basic alarm → fixed adjustment** | **Simple Scaling** |
| **New AMI/configuration for future instances** | **Launch Template** |
| **Legacy launch definition** | **Launch Configuration** |
| **Gradually replace ASG instances with new AMI** | **Instance Refresh** |
| **Mix Spot + On-Demand** | **Mixed Instances Policy** |
| **Spot interruption protection/rebalancing** | **Capacity Rebalancing** |
| **EC2 fleet behind application load balancer** | **ALB + Target Group + ASG** |

---

# 🏆 EC2 “Keyword → Answer” Master Memory

```
INTERRUPTIBLE
      ↓
    SPOT

PREDICTABLE + LONG TERM
      ↓
RI / SAVINGS PLAN

SPECIFIC AZ + GUARANTEED CAPACITY
      ↓
CAPACITY RESERVATION

PHYSICAL SOCKET / CORE LICENSE
      ↓
DEDICATED HOST

DEDICATED TENANCY
      ↓
DEDICATED INSTANCE

HPC + LOW LATENCY
      ↓
CLUSTER

MAX HARDWARE ISOLATION
      ↓
SPREAD

KAFKA / CASSANDRA / HDFS
      ↓
PARTITION

PERSISTENT BLOCK STORAGE
      ↓
EBS

TEMPORARY LOCAL STORAGE
      ↓
INSTANCE STORE

CPU HEAVY
      ↓
C

MEMORY HEAVY
      ↓
R / X

GPU
      ↓
G / P

FPGA
      ↓
F

OCCASIONAL CPU SPIKES
      ↓
T

HIGH IOPS
      ↓
io1 / io2

INDEPENDENT IOPS + THROUGHPUT
      ↓
gp3

SEQUENTIAL THROUGHPUT
      ↓
st1

LOWEST COST HDD
      ↓
sc1

HIGH NETWORK PERFORMANCE
      ↓
ENA / ENHANCED NETWORKING

HPC NETWORKING
      ↓
EFA

EBS BANDWIDTH
      ↓
EBS-OPTIMIZED

PRESERVE RAM
      ↓
HIBERNATE

AUTOMATIC BOOT CONFIGURATION
      ↓
USER DATA

REUSABLE SERVER IMAGE
      ↓
AMI

EC2 → AWS API
      ↓
IAM ROLE

NO SSH / PRIVATE EC2 MANAGEMENT
      ↓
SSM SESSION MANAGER

METADATA SECURITY
      ↓
IMDSv2
```

## 🚨 Final Exam Trap Set

1. **Spot vs Capacity Reservation**
    
    **Spot = cheap + interruptible.**
    
    **Capacity Reservation = guaranteed capacity.**
    
2. **RI vs Capacity Reservation**
    
    **RI = pricing commitment.**
    
    **Capacity Reservation = capacity guarantee.**
    
3. **Dedicated Host vs Dedicated Instance**
    
    **Host = physical host control/license visibility.**
    
    **Instance = dedicated hardware tenancy.**
    
4. **Cluster vs Spread vs Partition**
    
    **Cluster = close. Spread = isolated. Partition = rack groups.**
    
5. **EBS vs Instance Store**
    
    **EBS = persistent. Instance Store = ephemeral.**
    
6. **gp3 vs io2**
    
    **gp3 = general purpose + independently tune IOPS/throughput.**
    
    **io2 = mission-critical/high IOPS.**
    
7. **Enhanced Networking vs EBS-optimized**
    
    **ENA = network. EBS-optimized = EBS bandwidth.**
    
8. **AMI vs User Data**
    
    **AMI = reusable machine image. User Data = launch-time script.**
    
9. **Stop vs Hibernate**
    
    **Hibernate = RAM preserved.**
    
10. **IAM Role vs Security Group**
    
    **IAM = “WHAT AWS API actions can I perform?”**
    
    **Security Group = “WHAT network traffic can reach me?”**
    
11. **T-series vs Compute Optimized**
    
    **T = bursty CPU. C = sustained CPU-heavy workload.**
    
12. **Partition vs Spread**
    
    **Partition = distributed applications such as Kafka/Cassandra/HDFS.**
    
    **Spread = small number of critical instances requiring maximum separation.**
    
13. **EFA vs ENA**
    
    **ENA = enhanced general networking.**
    
    **EFA = specialized HPC networking / very low latency.**
    
14. **Spot + Queue**
    
    If workers can disappear and another worker can continue the job → **Spot is highly likely**.
    
15. **“Must be available” does NOT automatically mean On-Demand.**
    
    First determine whether the question means **capacity guarantee**, **HA**, or **pricing**. Those lead to different answers.
    

### 🧠 The one mental model to carry into the exam

> **EC2 questions usually hide one of five decisions:**
> 

**PRICE →** Spot / RI / Savings Plan

**CAPACITY →** Capacity Reservation

**HARDWARE →** Dedicated Host / Instance

**PLACEMENT →** Cluster / Spread / Partition

**PERFORMANCE →** C / R / D / I / G / P / F / T + Enhanced Networking / EBS-optimized

If you can identify **which of those five dimensions the question is testing**, the correct EC2 answer usually becomes obvious.
