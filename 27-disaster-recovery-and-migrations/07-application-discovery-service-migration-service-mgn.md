# AWS Application Discovery Service + AWS Application Migration Service (MGN)

These two services are easy to confuse in SAA-C03. The simplest distinction is:

> **Discovery Service = Understand what you have.**
> 
> 
> **MGN = Move what you have to AWS.**
> 

---

# 1. AWS Application Discovery Service

### What is it?

AWS Application Discovery Service helps you **plan migration projects by gathering information about your on-premises data centers**.

It collects information that helps you understand:

- What servers you have
- Server utilization
- Server configuration
- Application dependencies
- Network connections between systems

### 🧠 Exam shortcut

> **"I need to discover/understand my on-premises environment before migration."**
> 

➡️ **AWS Application Discovery Service**

---

# 2. What information does it collect?

Two discovery methods are important:

```
                 Application Discovery Service
                           │
                 ┌─────────┴─────────┐
                 ↓                   ↓
          Agentless Discovery   Agent-based Discovery
                 │                   │
       Discovery Connector    Discovery Agent
```

---

## Agentless Discovery

Uses the **AWS Agentless Discovery Connector**.

It collects information such as:

- VM inventory
- VM configuration
- Performance history
- CPU usage
- Memory usage
- Disk usage

### 🧠 Think:

> **Agentless = VM inventory + configuration + performance**
> 

---

## Agent-based Discovery

Uses the **AWS Application Discovery Agent**.

It collects more detailed information such as:

- System configuration
- System performance
- Running processes
- Network connection details between systems

### 🧠 Think:

> **Agent-based = deeper system + process + network information**
> 

---

# 3. Discovery Data → Migration Hub

The resulting discovery data can be viewed within **AWS Migration Hub**.

```
On-Premises Data Center
          │
          ↓
Application Discovery Service
          │
     ┌────┴─────┐
     ↓          ↓
 Agentless   Agent-based
     │          │
     └────┬─────┘
          ↓
   Discovery Data
          ↓
   AWS Migration Hub
```

### Important distinction

**Application Discovery Service**

➡️ Collects information.

**AWS Migration Hub**

➡️ Helps view/track migration information and progress.

---

# 4. AWS Application Migration Service (MGN)

AWS Application Migration Service is the modern AWS solution for **lift-and-shift (rehost) migrations**.

It is the AWS evolution of **CloudEndure Migration** and replaced **AWS Server Migration Service (SMS)**.

### Main purpose

> **Move existing physical, virtual, or cloud-based servers to AWS with minimal changes.**
> 

---

# 5. Lift-and-Shift / Rehost

You don't need to redesign your application.

Instead:

```
Existing Server
      │
      │ Replication
      ↓
AWS Application Migration Service
      │
      ↓
EC2 + EBS
```

The application is migrated so it can run **natively on AWS**.

### Supports

- Physical servers
- Virtual servers
- Cloud-based servers
- Wide range of operating systems
- Various platforms
- Databases

---

# 6. How MGN Works

A simplified flow:

```
Corporate Data Center / Any Cloud
              │
        Existing Server
              │
      AWS Replication Agent
              │
              │ Continuous replication
              ↓
       AWS Staging Area
              │
       Low-cost EC2
        + EBS volumes
              │
              │ Cutover
              ↓
       Production AWS
              │
        Target EC2
        + EBS volumes
```

### Key idea

MGN continuously replicates the source server's data to AWS.

Then, when you're ready, you perform a **cutover** and launch the migrated application in AWS.

---

# 7. Why use MGN?

AWS Application Migration Service provides:

- **Lift-and-shift migration**
- Minimal downtime
- Reduced migration costs
- Continuous replication
- Migration of physical, virtual, and cloud-based servers
- AWS-native target environment using **EC2 + EBS**

---

# 🔥 Application Discovery vs MGN

|  | Application Discovery Service | Application Migration Service (MGN) |
| --- | --- | --- |
| Main purpose | **Discover** | **Migrate** |
| Primary use | Migration planning | Actual server migration |
| Collect server information | ✅ | — |
| Server utilization | ✅ | — |
| Dependency mapping | ✅ | — |
| Agentless discovery | ✅ | — |
| Agent-based discovery | ✅ | — |
| Lift-and-shift | — | ✅ |
| Rehost applications | — | ✅ |
| Continuous replication | — | ✅ |
| Target | Discovery information | EC2 + EBS |
| Migration Hub integration | ✅ | Migration tracking/ecosystem |
| Former technology | — | CloudEndure Migration / replaces SMS |

---

# 🧠 SAA-C03 Exam Memory

### Question says:

**"We need to understand our existing on-premises servers before migration."**

➡️ **Application Discovery Service**

---

**"We need CPU, memory, disk usage and VM inventory without installing agents."**

➡️ **Agentless Discovery / Discovery Connector**

---

**"We need running processes and network connections between systems."**

➡️ **Agent-based Discovery / Discovery Agent**

---

**"We want to track/view migration information."**

➡️ **AWS Migration Hub**

---

**"We need to lift-and-shift existing physical/virtual/cloud servers to AWS."**

➡️ **AWS Application Migration Service (MGN)**

---

**"We need continuous replication with minimal downtime before cutover."**

➡️ **AWS Application Migration Service (MGN)**

### ⭐ One-line revision

> **Discovery = Discover → Migration Hub = Track → MGN = Migrate**
>
