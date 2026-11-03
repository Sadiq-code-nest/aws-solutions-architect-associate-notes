# VPC Traffic Mirroring — SAA-C03 Exam Notes

## 1. What is VPC Traffic Mirroring?

**VPC Traffic Mirroring** allows you to **copy network traffic from an ENI** and send that copy to a **security/monitoring appliance** for inspection.

Think of it as:

> **Traffic Mirroring = CCTV camera for network traffic**
> 

It **copies** traffic. It does **not** normally sit in the traffic path.

```
                    Normal traffic
Source EC2 ───────────────────────────► Destination

     │
     │ COPY of traffic
     ▼
Traffic Mirroring
     │
     ▼
Security Appliance
```

The original application traffic continues normally.

---

# 2. Why do we need Traffic Mirroring?

Imagine you have an EC2 instance running your application:

```
Internet / Users
       │
       ▼
     EC2
```

You want to inspect:

- suspicious traffic
- possible attacks
- unusual packets
- network problems
- application/network behavior

But you don't want to modify the application itself.

Traffic Mirroring can copy the packets:

```
                 ┌──────────────► Normal destination
                 │
EC2 / ENI ───────┤
                 │
                 └──────────────► Security appliance
                                  │
                                  ├─ Threat detection
                                  ├─ Packet inspection
                                  └─ Troubleshooting
```

---

# 3. The 3 important components

For SAA-C03, understand these three:

### Source

The **ENI (Elastic Network Interface)** whose traffic you want to capture.

```
EC2
 │
 └── ENI ← SOURCE
```

### Traffic Mirror Session

The configuration that says:

> "Copy traffic from this source and send it there."
> 

### Target

Where the mirrored traffic is sent.

Supported targets include:

- **ENI**
- **Network Load Balancer (NLB)**

So remember:

```
SOURCE
  ↓
ENI
  ↓
Traffic Mirroring
  ↓
TARGET
  ↓
ENI or NLB
```

---

# 4. Real-world security architecture

Suppose your company has several application servers:

```
                 Users
                   │
                   ▼
             Application
               EC2-A
               EC2-B
                   │
                   │
            Normal traffic
                   │
                   ▼
              Database
```

The security team wants to inspect traffic.

You can create:

```
                 Users
                   │
                   ▼
              EC2-A / ENI
                   │
             ┌─────┴─────┐
             │           │
             ▼           ▼
        Application    Traffic
        continues      Mirror
                         │
                         ▼
                   Network Load
                     Balancer
                         │
                         ▼
                  Auto Scaling
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
             IDS        IPS       Analyzer
```

The security appliances can inspect the copied traffic.

---

# 5. Why Network Load Balancer?

Suppose you have several security appliances:

```
             Traffic Mirror
                    │
                    ▼
               NLB
             /  |   \
            /   |    \
           ▼    ▼     ▼
         IDS   IPS   Analyzer
```

The **NLB can distribute the mirrored traffic** across your security appliances.

This is useful when you need to scale the inspection infrastructure.

---

# 6. What traffic can be mirrored?

Traffic Mirroring can capture:

### Inbound traffic

```
User → EC2
```

### Outbound traffic

```
EC2 → User
```

So remember:

> **Traffic Mirroring can inspect inbound AND outbound traffic.**
> 

You can also configure filters so that you don't necessarily mirror everything.

---

# 7. Traffic Mirror Filter

You may not want to capture every packet.

For example:

```
EC2
 │
 ├── HTTP → Mirror
 ├── HTTPS → Mirror
 ├── SSH → Don't mirror
 └── DNS → Don't mirror
```

You can use a **Traffic Mirror Filter** to control what traffic gets mirrored.

### Exam clue

If the question says:

> "Capture only specific traffic"
> 

Think:

**Traffic Mirror Filter**

---

# 8. Packet truncation

Traffic Mirroring can optionally **truncate packets**.

Why?

Large packets contain lots of data that might not be necessary for your analysis.

You might only need part of the packet for:

- security inspection
- traffic analysis
- troubleshooting

Think:

> **Truncate = send only part of the packet**
> 

---

# 9. Can source and target be in different VPCs?

**Yes**, this is an important exam point.

They can be:

### Same VPC

```
VPC A

Source ENI
    │
    ▼
Mirror
    │
    ▼
Target ENI
```

### Different VPCs through VPC Peering

```
VPC A                         VPC B

Source ENI                    Target ENI
    │                            ▲
    └──── Traffic Mirror ────────┘
             VPC Peering
```

So:

> **Traffic Mirroring can send mirrored traffic to a target in another VPC using VPC Peering.**
> 

---

# 10. Traffic Mirroring vs VPC Flow Logs

This is an important distinction for SAA-C03.

| Feature | VPC Flow Logs | Traffic Mirroring |
| --- | --- | --- |
| What does it capture? | **Metadata about traffic** | **Actual network packets** |
| Packet contents? | ❌ No | ✅ Yes |
| Deep packet inspection | ❌ | ✅ |
| Security appliances | Not its primary purpose | ✅ |
| Troubleshooting | ✅ | ✅ |
| Threat monitoring | Limited metadata | ✅ |
| Storage/log analysis | CloudWatch/S3/Firehose | Sent to inspection target |
| Think | **Who talked to whom?** | **What is inside the traffic?** |

### Very important memory trick

> **Flow Logs = traffic metadata**
> 

> **Traffic Mirroring = copy the actual traffic**
> 

For example, Flow Logs might tell you:

```
10.0.1.10 → 10.0.2.20
Port: 443
Action: ACCEPT
Bytes: 5000
```

Traffic Mirroring is designed to provide the **packets themselves** for inspection.

---

# 11. Traffic Mirroring vs VPC Flow Logs — exam scenario

### Question A

> "The security team wants to know which IP addresses are communicating and whether traffic is being accepted or rejected."
> 

Think:

**VPC Flow Logs**

---

### Question B

> "The security team needs to perform deep packet inspection on traffic from EC2 instances."
> 

Think:

**VPC Traffic Mirroring**

---

### Question C

> "A company needs to send copies of network traffic to IDS/IPS appliances."
> 

Think:

**VPC Traffic Mirroring**

---

# 12. Traffic Mirroring vs NACL/Security Group

Don't confuse their purposes.

```
Security Group
      ↓
ALLOW / BLOCK traffic

NACL
      ↓
ALLOW / BLOCK traffic at subnet level

Traffic Mirroring
      ↓
COPY traffic for inspection
```

### Memory

> **SG/NACL = control traffic**
> 

> **Traffic Mirroring = inspect/copy traffic**
> 

---

# 13. Traffic Mirroring is NOT a firewall

This is an important conceptual point.

Traffic Mirroring itself doesn't mean:

> "Block this malicious packet."
> 

Instead:

```
Traffic
   │
   ├────────────► Application
   │
   └─ COPY ─────► Security appliance
                         │
                         ▼
                    Analyze traffic
```

The security appliance can then perform the inspection/detection function.

---

# 14. Common SAA-C03 scenarios

### Scenario 1 — Threat monitoring

> Security team wants to inspect EC2 network traffic for malicious activity using security appliances.
> 

✅ **VPC Traffic Mirroring**

---

### Scenario 2 — IDS/IPS

> Company wants to send copies of EC2 traffic to an IDS/IPS system.
> 

✅ **VPC Traffic Mirroring**

---

### Scenario 3 — Deep packet inspection

> Network engineers need to inspect the contents of packets to troubleshoot an application.
> 

✅ **VPC Traffic Mirroring**

---

### Scenario 4 — Only specific traffic

> Security team wants to mirror only traffic matching certain protocols/ports.
> 

✅ **Traffic Mirror Filter**

---

### Scenario 5 — Scalable security appliances

> Mirrored traffic must be distributed across multiple security appliances.
> 

✅ **Traffic Mirroring → Network Load Balancer → Security Appliances**

---

# 15. The architecture you should visualize

For the exam, remember this:

```
                    VPC
 ┌─────────────────────────────────────────┐
 │                                         │
 │     Source ENI A                       │
 │          │                              │
 │          │                             │
 │          ├──── Normal traffic ────────►│
 │          │                              │
 │          │                              │
 │          └──── Traffic Mirror           │
 │                    │                    │
 │                    ▼                    │
 │             Mirror Filter               │
 │                    │                    │
 │                    ▼                    │
 │              Network Load Balancer      │
 │                    │                    │
 │              ┌─────┼─────┐              │
 │              ▼     ▼     ▼              │
 │             IDS   IPS  Analyzer         │
 │                                         │
 └─────────────────────────────────────────┘
```

---

# 🧠 SAA-C03 Memory Tricks

### Main concept

> **Traffic Mirroring = COPY → INSPECT**
> 

### Source

> **Source = ENI**
> 

### Target

> **Target = ENI or NLB**
> 

### Purpose

> **Security + Monitoring + Troubleshooting**
> 

### Filter

> **Filter = Mirror only the traffic you care about**
> 

### Flow Logs comparison

> **Flow Logs = metadata**
> 

> **Traffic Mirroring = packets**
> 

---

## ⭐ Final exam cheat sheet

| If the question says... | Think... |
| --- | --- |
| Capture network traffic | **Traffic Mirroring** |
| Inspect packets | **Traffic Mirroring** |
| Deep packet inspection | **Traffic Mirroring** |
| IDS/IPS | **Traffic Mirroring** |
| Security appliance | **Traffic Mirroring** |
| Source | **ENI** |
| Target | **ENI or NLB** |
| Distribute traffic to appliances | **NLB** |
| Mirror only selected traffic | **Traffic Mirror Filter** |
| Capture actual packets | **Traffic Mirroring** |
| IP/port/bytes/ACCEPT/REJECT metadata | **VPC Flow Logs** |
| Block/allow traffic | **Security Group / NACL** |

### One sentence to remember for SAA-C03:

> **VPC Traffic Mirroring copies traffic from an ENI to an ENI or NLB so security appliances can inspect it for threat monitoring, content inspection, or troubleshooting.**
>
