# AWS SAA-C03 — Amazon EC2 MASTER NOTES

## Part 1 — Foundations, Instance Types, AMI, User Data & Lifecycle

> **Exam mindset:** EC2 questions are usually not asking "What is EC2?"
> 
> 
> They are asking:
> 
> **"What kind of workload is this, and which EC2 capability matches it?"**
> 

---

# 1. What Is Amazon EC2?

Amazon EC2 = **Elastic Compute Cloud**.

It gives you virtual servers called **instances**.

Instead of buying a physical server:

```
Physical Server
      ↓
Buy hardware
      ↓
Install OS
      ↓
Configure software
      ↓
Run application
```

AWS lets you launch:

```
EC2 Instance
      ↓
Choose CPU
Choose RAM
Choose OS
Choose storage
Choose networking
      ↓
Run application
```

## Why does EC2 exist?

When you need **server-level control**.

You control things such as:

- Operating system
- Installed software
- CPU/memory capacity
- Networking
- Attached storage
- Security configuration
- Startup scripts

---

## Exam trigger

If the question says:

> "The company needs full control over the operating system and server configuration."
> 

Think:

**EC2**

---

# 2. EC2 Instance = Virtual Machine

An EC2 instance is essentially a virtual server.

Example:

```
Application
    ↓
EC2
 ┌──────────────┐
 │ Linux        │
 │ 4 vCPU       │
 │ 16 GiB RAM   │
 │ EBS          │
 └──────────────┘
```

You choose the instance configuration according to the workload.

---

# 3. EC2 Instance Families

This is one of the most important SAA-C03 EC2 topics.

AWS gives you different instance families because workloads have different resource requirements.

Think:

```
CPU-heavy       → Compute Optimized
Balanced        → General Purpose
RAM-heavy       → Memory Optimized
Disk-heavy      → Storage Optimized
GPU/FPGA        → Accelerated Computing
```

---

# 4. General Purpose — M / T

## M family

Examples:

```
M5
M6i
M7i
```

Balanced:

```
CPU
RAM
Network
```

### Use when

The workload doesn't have an extreme requirement for one resource.

Typical:

- Web servers
- Application servers
- Backend applications
- Enterprise applications
- Development environments

### Trigger

> "Balanced compute, memory and networking."
> 

→ **M = General Purpose**

### Memory trick

**M = Middle / Mixed**

---

## Example

A company runs a normal web application:

```
ALB
 ↓
EC2 M7i
 ↓
RDS
```

Nothing is particularly CPU-heavy or memory-heavy.

M is a natural choice.

---

# 5. T Family — Burstable General Purpose

Examples:

```
T3
T3a
T4g
```

T instances are also General Purpose, but they are **burstable**.

This means:

```
Normal workload
     ↓
Low baseline CPU
     ↓
CPU credits accumulate
     ↓
Traffic spike
     ↓
Use credits
     ↓
Temporarily high CPU
```

---

## Why does burstable performance exist?

Some workloads are usually quiet but occasionally become busy.

Example:

```
Normal:
CPU 10%
CPU 15%
CPU 12%

Sudden spike:
CPU 80%
CPU 90%
```

You don't want to pay for a permanently powerful CPU just to handle occasional spikes.

---

## Typical scenarios

- Small websites
- Development/test servers
- Small applications
- Low-to-moderate workloads with occasional bursts

---

## Exam trigger

If the question says:

> "CPU usage is generally low but occasionally spikes."
> 

Think:

**T-series**

---

## Memory trick

> **T = Temporary CPU Turbo**
> 

---

## Important distinction

Don't choose T-series simply because the workload is "small."

The important clue is:

**BURSTY CPU usage.**

---

# 6. Compute Optimized — C

Examples:

```
C5
C6i
C7i
```

Designed for:

**high CPU performance.**

CPU is the bottleneck.

---

## Typical workloads

- Batch processing
- Scientific modeling
- High-performance web servers
- Video encoding
- Distributed analytics
- CPU-intensive workloads
- HPC computation

---

## Scenario

A company runs a mathematical simulation requiring very high CPU utilization.

```
CPU ████████████████████
RAM █████
```

→ Compute Optimized.

---

## Exam trigger

Words such as:

- **CPU-intensive**
- **compute-intensive**
- **high-performance computing**
- **batch computation**
- **scientific calculations**
- **video encoding**

→ **C family**

---

## Memory trick

> **C = Compute**
> 

Easy.

---

# 7. Memory Optimized — R / X

Memory-optimized instances are designed for workloads that need large amounts of RAM.

Examples:

```
R5
R6i
R7i
X1
X2
```

Think:

```
CPU █████
RAM ████████████████████
```

---

## Typical workloads

- Large databases
- In-memory databases
- Big data processing
- Real-time analytics
- Caching
- Large enterprise applications

---

# 8. R Family

R is commonly associated with:

> **Memory-intensive applications**
> 

Examples:

- In-memory caching
- Database workloads
- Analytics
- Large applications

---

## Exam trigger

If the question says:

> "The application requires a large amount of memory."
> 

→ **R**

---

## Memory trick

> **R = RAM**
> 

---

# 9. X Family

X instances are designed for **very large memory requirements**.

Think:

```
X = eXtreme memory
```

Used for:

- Very large in-memory databases
- SAP workloads
- Enterprise applications
- Extremely memory-intensive workloads

---

## Exam distinction

If the question merely says:

> "Memory optimized"
> 

→ R is often sufficient.

If it emphasizes:

> "Massive memory / very large in-memory enterprise workload"
> 

→ consider **X**.

---

# 10. Storage Optimized — D / H / I

Storage optimized instances are designed around **local storage and high disk performance**.

This is where many SAA candidates confuse:

```
Storage Optimized
vs
EBS
vs
Instance Store
```

The instance family and the storage technology are related, but they are not the same concept.

---

# 11. D Family

D family = storage optimized with large local HDD capacity.

Typical:

- Data warehouses
- Distributed file systems
- Big data
- Large sequential workloads

Think:

> **D = Dense storage**
> 

---

# 12. H Family

H family is storage optimized and designed around **high disk throughput**.

Typical workloads:

- Big data
- Data processing
- High-throughput storage workloads

---

# 13. I Family

I family focuses on:

- High random I/O
- High IOPS
- Low latency
- High sequential read/write performance

Typical:

- NoSQL databases
- Databases
- Data analytics
- High-performance storage workloads

---

## Very important exam distinction

Question:

> "High sequential read/write access to very large datasets on local storage."
> 

This is strongly pointing toward:

**Storage Optimized EC2**

Not Memory Optimized.

Not General Purpose.

Not Compute Optimized.

---

## Memory trick

```
C → CPU
R → RAM
D/H/I → DISK
```

---

# 14. Accelerated Computing — G / P / F

Accelerated computing uses specialized hardware.

```
CPU
+
GPU / FPGA / accelerator
```

---

# 15. G Family — GPU

G = GPU-focused workloads.

Typical:

- Graphics
- 3D visualization
- Gaming
- Video processing
- Machine learning inference
- Graphics rendering

---

## Exam trigger

> "Graphics-intensive"
> 

→ **G**

---

## Memory trick

> **G = Graphics**
> 

---

# 16. P Family — GPU for Compute / ML

P instances use powerful GPUs for computational workloads.

Typical:

- Machine learning
- Deep learning
- High-performance GPU computing
- Scientific computing

---

## Difference from G

Think:

```
G → Graphics / visualization
P → Powerful GPU computation
```

---

# 17. F Family — FPGA

F instances provide **FPGA** acceleration.

FPGA = Field Programmable Gate Array.

The hardware can be configured for specialized computation.

Typical:

- Financial modeling
- Genomics
- Scientific computation
- Custom hardware acceleration

---

## Exam trigger

If the question explicitly says:

> **FPGA**
> 

→ **F family**

---

## Memory trick

> **F = FPGA**
> 

---

# 18. EC2 Instance Family Master Table

| Family | Category | Main resource | Typical workload | Exam trigger |
| --- | --- | --- | --- | --- |
| **M** | General Purpose | Balanced | Web/app servers | Balanced |
| **T** | General Purpose | Burstable CPU | Small/spiky workloads | **CPU bursts** |
| **C** | Compute Optimized | CPU | Batch/HPC/computation | **CPU-intensive** |
| **R** | Memory Optimized | RAM | Databases/cache | **Memory-intensive** |
| **X** | Memory Optimized | Huge RAM | Large in-memory enterprise apps | **Massive memory** |
| **D** | Storage Optimized | Dense local storage | Large datasets | **Dense storage** |
| **H** | Storage Optimized | Disk throughput | High disk throughput | **High throughput** |
| **I** | Storage Optimized | IOPS/low latency | DB/random I/O | **High IOPS** |
| **G** | Accelerated | GPU | Graphics | **Graphics** |
| **P** | Accelerated | GPU | ML/HPC | **GPU computation** |
| **F** | Accelerated | FPGA | Specialized acceleration | **FPGA** |

---

# 19. The 5-Second Instance Selection Trick

When an exam question asks:

> "Which instance type should be selected?"
> 

Ask:

### Question 1

Is CPU the bottleneck?

→ **C**

### Question 2

Is RAM the bottleneck?

→ **R/X**

### Question 3

Is local disk the bottleneck?

→ **D/H/I**

### Question 4

Is GPU needed?

→ **G/P**

### Question 5

Is FPGA explicitly required?

→ **F**

### Question 6

Is workload balanced?

→ **M**

### Question 7

Is CPU usually low but occasionally spikes?

→ **T**

---

# 20. AMI — Amazon Machine Image

AMI = **template used to launch EC2 instances**.

Think of an AMI as a prepared server blueprint.

Example:

```
AMI
│
├── Operating System
├── Installed software
├── Configuration
├── Application dependencies
└── Storage configuration
```

Then:

```
AMI
 ↓
Launch
 ↓
EC2 Instance
```

---

# 21. Why Do AMIs Exist?

Imagine you configure one server:

```
Linux
+
Nginx
+
Application
+
Libraries
+
Configuration
```

You don't want to manually configure 100 servers.

Create an AMI.

Then:

```
AMI
 ↓
EC2 #1
EC2 #2
EC2 #3
...
EC2 #100
```

Every instance starts from the same baseline.

---

# 22. Golden Image

A **Golden Image** is a standardized, approved machine image.

Example:

A company defines:

```
Corporate Linux
+
Security patches
+
Monitoring agent
+
Antivirus
+
Application dependencies
```

They create an approved AMI.

That is their:

> **Golden Image**
> 

---

## Exam trigger

If the question says:

- standardized server image
- preconfigured image
- identical EC2 instances
- approved company image
- reusable server configuration

→ **AMI / Golden Image**

---

# 23. AMI + Auto Scaling

This is extremely important.

Suppose:

```
ALB
 ↓
Auto Scaling Group
 ↓
EC2 instances
```

The ASG needs to know:

> "What should each new EC2 instance look like?"
> 

The launch template specifies the configuration and can reference an AMI.

```
Launch Template
      │
      ├── AMI
      ├── Instance Type
      ├── Security Group
      ├── IAM Role
      ├── User Data
      └── Storage
```

Then:

```
ASG
 ↓
Launch Template
 ↓
AMI
 ↓
New EC2
```

---

# 24. AMI vs User Data

This is a major exam trap.

### AMI

Provides the **base image**.

```
OS
+
Installed software
+
Preconfigured environment
```

### User Data

Runs a **startup/bootstrap script**.

```
Launch EC2
     ↓
Run startup commands
     ↓
Install/configure/start software
```

---

## Memory trick

> **AMI = WHAT the server is**
> 
> 
> **User Data = WHAT the server does when it starts**
> 

---

# 25. User Data

User Data is a startup script supplied when launching an EC2 instance.

Example:

```bash
#!/bin/bash
yum update -y
yum install -y nginx
systemctl start nginx
```

When the instance starts, the script can configure it automatically.

---

# 26. Why User Data?

Without User Data:

```
Launch EC2
 ↓
SSH into server
 ↓
Install software
 ↓
Configure software
 ↓
Start application
```

With User Data:

```
Launch EC2
 ↓
Bootstrap automatically
 ↓
Application ready
```

---

# 27. Exam Trigger — User Data

If the question says:

> "Automatically configure the EC2 instance when it launches."
> 

→ **User Data**

If it says:

> "Install software during instance startup."
> 

→ **User Data**

If it says:

> "Retrieve instance metadata during startup."
> 

→ **User Data + Instance Metadata**

---

# 28. User Data Runs Where?

The script executes **inside the EC2 instance**.

It can:

- Install packages
- Configure applications
- Start services
- Download files
- Retrieve metadata
- Configure the OS

---

# 29. User Data vs AMI — Scenario

### Scenario A

100 instances must have:

```
Ubuntu
Nginx
Application dependencies
Monitoring agent
```

Use:

**Golden AMI**

because you want a reusable standardized image.

### Scenario B

Every newly launched instance needs to:

```
Download today's configuration
Register itself with service discovery
Start application
```

Use:

**User Data**

---

# 30. Instance Metadata

EC2 instances can access information about themselves through the **Instance Metadata Service (IMDS)**.

Examples:

- Instance ID
- Private IP
- Public IP where applicable
- Instance type
- IAM role credentials
- Availability Zone
- Region
- Network information

---

# 31. Why Metadata Matters

Suppose a startup script needs:

```
Instance private IP
Instance public IP
Availability Zone
Instance ID
```

You don't hard-code these values.

The instance can query its metadata.

---

# 32. IMDSv2

Modern AWS security guidance favors:

**IMDSv2**

It uses a session-oriented approach with a token.

Conceptually:

```
Request token
     ↓
Use token
     ↓
Request metadata
```

This helps protect against certain SSRF-style attacks.

---

## Exam clue

If a question says:

> "Secure access to EC2 instance metadata."
> 

Think:

**IMDSv2**

---

# 33. EC2 Public IP vs Private IP

Every EC2 instance in a VPC has a private IP.

Example:

```
10.0.1.25
```

A public IPv4 address may also be associated depending on subnet/configuration.

Example:

```
Public IP: 54.x.x.x
Private IP: 10.0.1.25
```

---

# 34. Private IP

Used for communication inside the VPC/private networking environment.

Example:

```
EC2
10.0.1.10
   ↓
RDS
10.0.2.20
```

The communication uses private networking.

---

# 35. Elastic IP

An Elastic IP is a **static public IPv4 address** allocated to your AWS account.

Useful when an application needs a persistent public IPv4 address.

---

## Exam trap

Do not automatically use Elastic IP for high availability.

For scalable architectures, AWS often expects:

```
Route 53
+
ALB/NLB
+
Auto Scaling
```

rather than manually assigning an Elastic IP to one EC2 instance.

---

# 36. EC2 Lifecycle

The major states:

```
Pending
   ↓
Running
   ↓
Stopping
   ↓
Stopped
   ↓
Starting
   ↓
Running

Running
   ↓
Terminating
   ↓
Terminated
```

---

# 37. Stop

When you stop an EBS-backed EC2 instance:

- Compute billing stops
- EBS storage continues to incur charges
- Instance can later be started again
- Private IPv4 address can change
- EBS volumes persist according to their settings

---

## Exam trigger

> "Stop the instance to reduce compute cost during off-hours."
> 

→ **Stop**

---

# 38. Terminate

Terminate means:

> Permanently delete the EC2 instance.
> 

The instance itself is gone.

EBS behavior depends on:

**DeleteOnTermination**

---

# 39. Reboot

Reboot is similar to restarting the operating system.

The instance remains running as the same instance.

Use when:

> "The operating system needs to restart."
> 

Not:

> "We want to eliminate compute charges."
> 

---

# 40. Stop vs Reboot vs Terminate

| Action | Instance remains? | Compute charge? | Typical use |
| --- | --- | --- | --- |
| **Reboot** | Yes | Continues | Restart OS |
| **Stop** | Yes | Stops | Temporary shutdown |
| **Terminate** | No | Stops | Delete instance |

---

# 41. Delete on Termination

Each EBS attachment can have a:

**DeleteOnTermination**

setting.

For many EC2 configurations:

```
Root EBS
→ deleted when EC2 terminates
```

while additional EBS volumes may be configured to persist.

---

## Exam clue

> "Terminate EC2 but preserve the data volume."
> 

→ Disable **DeleteOnTermination** for that volume.

---

# 42. AMI + EBS Relationship

An AMI can reference snapshots of EBS-backed volumes.

Conceptually:

```
EBS Volume
    ↓
Snapshot
    ↓
AMI
    ↓
Launch
    ↓
New EBS Volume
    ↓
EC2
```

This is why an AMI can reproduce an EC2 environment.

---

# 43. AMI Across Regions

An AMI is regional.

If you need to launch from it in another AWS Region, you can **copy the AMI** to that Region.

Example:

```
us-east-1
   │
   │ Copy AMI
   ↓
eu-west-1
```

Then launch instances in the destination Region.

---

# 44. Why Copy an AMI?

Typical scenario:

> "The company has standardized its EC2 image in us-east-1 and wants to deploy the same application in Europe."
> 

→ **Copy the AMI to the European Region.**

---

# 45. AMI Exam Traps

### Trap 1

> "Need identical EC2 configurations."
> 

Wrong answer:

Manually configure every server.

Better:

**AMI / Golden Image**

---

### Trap 2

> "Need to execute commands when the instance launches."
> 

Don't choose AMI alone.

→ **User Data**

---

### Trap 3

> "Need the same AMI in another Region."
> 

Don't assume AMIs automatically become available everywhere.

→ **Copy the AMI to the target Region.**

---

### Trap 4

> "Need persistent application data."
> 

Don't use AMI as the primary runtime data store.

Use appropriate persistent storage such as:

**EBS / EFS / S3 / database**

depending on the workload.

---

# 46. EC2 Foundation Exam Decision Tree

When you see an EC2 question, mentally run:

```
             EC2 question
                  │
                  ▼
        What is the bottleneck?
                  │
      ┌───────────┼───────────┐
      ▼           ▼           ▼
     CPU          RAM         Disk
      │            │            │
      C          R / X       D/H/I
      │
      ▼
   GPU/FPGA?
      │
   G/P/F
```

If none:

```
Balanced → M
Bursty CPU → T
```

---

# 47. EC2 Ultra-Fast Memory Table

| Question wording | Think |
| --- | --- |
| **CPU intensive** | C |
| **Compute intensive** | C |
| **HPC computation** | C |
| **Memory intensive** | R |
| **Huge in-memory database** | X |
| **High disk throughput** | H |
| **Large local storage** | D |
| **High random I/O / IOPS** | I |
| **Graphics** | G |
| **GPU computing / ML** | P |
| **FPGA** | F |
| **Balanced workload** | M |
| **Occasional CPU spikes** | T |
| **Standardized server image** | AMI |
| **Configure at startup** | User Data |
| **Secure metadata access** | IMDSv2 |
| **Persistent static public IPv4** | Elastic IP |
| **Temporarily stop compute** | Stop |
| **Restart OS** | Reboot |
| **Delete server** | Terminate |

---

# 48. SAA-C03 EC2 Mental Model

Do not memorize EC2 as hundreds of isolated facts.

Use this model:

```
                    EC2
                     │
       ┌─────────────┼─────────────┐
       │             │             │
    COMPUTE       STORAGE       NETWORK
       │             │             │
    C / M / T     EBS / IS      ENA / EFA
    R / X         snapshots
    D/H/I
    G/P/F
       │
       └─────────────┬─────────────┘
                     │
                 DEPLOYMENT
                     │
              AMI + User Data
                     │
                Launch Template
                     │
                    ASG
```

And separately:

```
              EC2 COST
                 │
     ┌───────────┼─────────────┐
     │           │             │
 On-Demand    Reserved/SP    Spot
     │
     └── Capacity Reservation
```

This architecture will make the remaining EC2 topics much easier.

---

# 49. HIGH-VALUE EXAM CONNECTIONS

### Connection 1

**AMI + Launch Template + ASG**

means:

> "Automatically launch identical preconfigured EC2 instances."
> 

---

### Connection 2

**T-series + CPU credits**

means:

> "Workload is normally low but periodically bursts."
> 

---

### Connection 3

**C-series + HPC**

means:

> "CPU is the bottleneck."
> 

---

### Connection 4

**I-series + database + high IOPS**

means:

> "Storage I/O is the bottleneck."
> 

---

### Connection 5

**D/H + huge sequential datasets**

means:

> "Storage throughput/capacity is the bottleneck."
> 

---

### Connection 6

**G/P + GPU**

means:

> "CPU alone isn't enough; specialized accelerator needed."
> 

---

### Connection 7

**F + FPGA**

means:

> "Reconfigurable hardware acceleration."
> 

---

# 50. EC2 Part 1 — Final Exam Hacks

### 🔥 Hack #1

**"CPU intensive" → C**

Don't overthink.

### 🔥 Hack #2

**"RAM intensive" → R/X**

### 🔥 Hack #3

**"High IOPS" → I**

### 🔥 Hack #4

**"High disk throughput / huge local data" → Storage Optimized**

### 🔥 Hack #5

**"Graphics" → G**

### 🔥 Hack #6

**"FPGA" → F**

### 🔥 Hack #7

**"Bursty CPU" → T**

### 🔥 Hack #8

**"Identical preconfigured servers" → AMI**

### 🔥 Hack #9

**"Run script at launch" → User Data**

### 🔥 Hack #10

**"Secure metadata" → IMDSv2**

### 🔥 Hack #11

**"Persistent public IPv4" → Elastic IP**

### 🔥 Hack #12

**"Stop temporarily" ≠ Terminate**

Stop preserves the instance.

Terminate deletes it.

---

# PART 1 — MASTER CHEAT TABLE

| Concept | Trigger phrase | Pick |
| --- | --- | --- |
| Balanced workload | **General purpose / balanced** | M |
| Bursty CPU | **Occasional CPU spikes** | T |
| CPU-heavy | **Compute intensive / HPC** | C |
| RAM-heavy | **Memory intensive** | R |
| Huge RAM | **Massive in-memory workload** | X |
| Dense local storage | **Very large local datasets** | D |
| Disk throughput | **High sequential disk throughput** | H |
| High IOPS | **Low latency / random I/O / high IOPS** | I |
| Graphics | **Graphics-intensive** | G |
| GPU compute | **Machine learning / GPU computing** | P |
| FPGA | **Reconfigurable FPGA** | F |
| Standard server image | **Golden image / identical servers** | AMI |
| Startup automation | **Configure during launch** | User Data |
| Secure metadata | **Metadata security / SSRF protection** | IMDSv2 |
| Temporary shutdown | **Reduce compute cost during off-hours** | Stop |
| Restart OS | **Restart instance** | Reboot |
| Delete server | **Permanently remove instance** | Terminate |
| Persistent public IPv4 | **Static public IP** | Elastic IP |
| Cross-region deployment | **Use same AMI in another Region** | Copy AMI |

---

## NEXT

**EC2 Part 1 of the Master Series is complete.**

The next part is **EC2 Part 2 — Storage**, starting with:

> **Instance Store vs EBS-backed instances → EBS fundamentals → root volumes → Delete on Termination → snapshots → encryption → gp2/gp3 → io1/io2 → st1/sc1 → IOPS vs throughput → EBS-optimized → Multi-Attach → Elastic Volumes → DLM.**
> 

I will **not move to Placement Groups, Spot, Reserved Instances, or another AWS service yet**. We stay inside EC2 and finish it in the roadmap order above.
