# AWS Transit Gateway — SAA-C03 Exam Notes

## 1. What is AWS Transit Gateway?

**AWS Transit Gateway (TGW)** is a **regional network transit hub** that allows you to connect **thousands of VPCs and on-premises networks** through a central gateway.

Instead of creating many individual connections between VPCs:

```
Without Transit Gateway

VPC A ───── VPC B
 │  \       /  │
 │   \     /   │
 │    \   /    │
 VPC C ───── VPC D
```

You can use:

```
With Transit Gateway

          VPC A
            │
          VPC B
            │
VPC C ─ Transit Gateway ─ VPC D
            │
      On-Premises
```

### 🧠 Easy understanding

> **Transit Gateway = Central network hub for connecting many VPCs and on-premises networks.**
> 

---

# 2. Why do we need Transit Gateway?

Imagine a large company has:

- 100 VPCs
- Multiple AWS accounts
- Multiple offices/data centers
- Multiple VPN connections
- Direct Connect

Connecting everything individually becomes complicated.

Transit Gateway simplifies this:

```
                  VPC 1
                    │
                  VPC 2
                    │
VPC 3 ─────── Transit Gateway ─────── VPC 4
                    │
                    │
              Direct Connect
                    │
              On-Premises
```

### ⭐ Exam clue

> **"Connect thousands of VPCs and on-premises networks"**
> 

➡️ **Transit Gateway**

---

# 3. Transit Gateway is Regional

Transit Gateway is a **Regional resource**.

It can connect VPCs within a Region.

For example:

```
AWS Region: us-east-1

VPC A ─┐
VPC B ─┼── Transit Gateway
VPC C ─┘
```

But Transit Gateway can also work across Regions using **Transit Gateway peering**.

---

# 4. Transit Gateway Peering Across Regions

You can peer Transit Gateways in different AWS Regions.

```
us-east-1                         eu-west-1

VPC A ─┐                         ┌─ VPC D
VPC B ─┼─ TGW ───────────── TGW ─┼─ VPC E
VPC C ─┘    TGW Peering          └─ VPC F
```

### ⭐ Exam clue

> **"Connect VPCs in different Regions using Transit Gateway"**
> 

➡️ **Peer the Transit Gateways**

---

# 5. Sharing Transit Gateway Across AWS Accounts

You can share a Transit Gateway across AWS accounts using:

**AWS Resource Access Manager (AWS RAM)**

Example:

```
Management Account
        │
        ▼
   Transit Gateway
        │
   ┌────┼────┐
   ▼    ▼    ▼
Account A  Account B  Account C
   │          │          │
  VPC        VPC        VPC
```

### ⭐ Remember

> **AWS RAM = Share resources across AWS accounts**
> 

---

# 6. Transit Gateway Route Tables

Transit Gateway uses **Transit Gateway Route Tables** to determine where traffic should go.

Think of it as:

> **"Where should this packet go next?"**
> 

Example:

```
VPC A
10.1.0.0/16
     │
     ▼
Transit Gateway
     │
     ▼
TGW Route Table

10.2.0.0/16 → VPC B
10.3.0.0/16 → VPC C
10.10.0.0/16 → VPN
```

So:

```
VPC A → TGW → Route Table → VPC B
```

### ⭐ Exam clue

> **"Control which VPC can communicate with which other VPC"**
> 

➡️ **Transit Gateway Route Tables**

---

# 7. Transit Gateway + VPN

Transit Gateway can connect with **Site-to-Site VPN**.

```
On-Premises
     │
 Customer Gateway
     │
     │ VPN
     ▼
Transit Gateway
   │    │    │
  VPC  VPC  VPC
```

This is particularly useful when you have **many VPCs**.

Instead of creating a separate VPN connection to every VPC:

```
❌ On-Premises → VPN → VPC A
❌ On-Premises → VPN → VPC B
❌ On-Premises → VPN → VPC C
```

you can have:

```
✅ On-Premises
       │
      VPN
       │
       ▼
Transit Gateway
   │    │    │
 VPC A VPC B VPC C
```

---

# 8. Transit Gateway + Direct Connect

Transit Gateway also works with **Direct Connect Gateway**.

Example:

```
On-Premises
     │
     │ Direct Connect
     ▼
Direct Connect Gateway
     │
     ▼
Transit Gateway
   │   │   │
 VPC A VPC B VPC C
```

This allows your on-premises network to connect to multiple VPCs through a centralized architecture.

### ⭐ Exam chain

**On-premises → Direct Connect → Direct Connect Gateway → Transit Gateway → VPCs**

---

# 9. Transit Gateway + ECMP

## What is ECMP?

**ECMP = Equal-Cost Multi-Path**

It allows traffic to use **multiple equal-cost network paths** instead of relying on only one path.

Imagine:

```
                 VPN #1
                /
On-Premises ─── TGW
                \
                 VPN #2
```

If both paths have equal cost, traffic can be distributed across them.

---

# 10. Why use ECMP with Site-to-Site VPN?

Suppose you have one VPN:

```
On-Premises
      │
     VPN
      │
      ▼
     TGW
```

You have limited bandwidth.

You can create multiple VPN connections:

```
                ┌── VPN #1 ──┐
On-Premises ────┤            ├── TGW
                └── VPN #2 ──┘
```

With **ECMP**, traffic can be distributed over multiple equal-cost paths.

### Result:

> **Increase aggregate VPN bandwidth / throughput**
> 

### ⭐ Exam clue

> "Multiple Site-to-Site VPN connections to increase bandwidth"
> 

➡️ **Transit Gateway + ECMP**

---

# 11. Transit Gateway + ECMP Example

Imagine:

```
VPN #1 = 1.25 Gbps
VPN #2 = 1.25 Gbps
```

With ECMP, you can potentially use both paths:

```
Total aggregate capacity ≈ 2.5 Gbps
```

The exact throughput depends on the architecture and AWS limits, but the **exam concept** is:

> **Multiple equal-cost VPN paths → ECMP → higher aggregate throughput**
> 

---

# 12. Transit Gateway + Direct Connect Across Accounts

Transit Gateway can also be used in architectures where a **Direct Connect Gateway** is shared across multiple AWS accounts.

For example:

```
                 On-Premises
                      │
                Direct Connect
                      │
                      ▼
             Direct Connect Gateway
                      │
                Transit Gateway
                /      |       \
               /       |        \
          Account A Account B Account C
             VPC       VPC       VPC
```

This can simplify connectivity in a **multi-account AWS environment**.

---

# 13. Transit Gateway vs VPC Peering

This is important for SAA-C03.

### VPC Peering

```
VPC A ───────── VPC B
```

If you have many VPCs, connections become difficult to manage.

### Transit Gateway

```
          VPC A
            │
          VPC B
            │
VPC C ──── TGW ──── VPC D
            │
        On-Premises
```

### Easy memory

> **VPC Peering = direct connection**
> 

> **Transit Gateway = central hub**
> 

---

# 14. Transit Gateway — Important Exam Concepts

| Requirement | Think of |
| --- | --- |
| Connect many VPCs | **Transit Gateway** |
| Connect VPCs + on-premises | **Transit Gateway** |
| Central network hub | **Transit Gateway** |
| Cross-region TGW connectivity | **TGW Peering** |
| Share TGW across accounts | **AWS RAM** |
| Control traffic paths | **TGW Route Tables** |
| Connect VPN to many VPCs | **Transit Gateway** |
| Connect Direct Connect to many VPCs | **Direct Connect Gateway + TGW** |
| Multiple equal-cost VPN paths | **ECMP** |
| Increase VPN aggregate throughput | **ECMP** |

---

# VPC Traffic Mirroring

## 15. What is VPC Traffic Mirroring?

**VPC Traffic Mirroring** allows you to **capture and inspect network traffic** from your VPC.

You can send a copy of network traffic to a **security appliance** for analysis.

Think:

```
EC2 / ENI
   │
   │ Original traffic
   ▼
Application

   │
   │ Copy of traffic
   ▼
Security Appliance
```

It is useful for:

- Threat monitoring
- Security analysis
- Network troubleshooting
- Content inspection

---

# 16. How Traffic Mirroring Works

Traffic Mirroring can capture traffic from:

### Source

An **Elastic Network Interface (ENI)**.

or supported sources such as network interfaces associated with workloads.

### Target

The mirrored traffic can be sent to a target such as:

**Network Load Balancer**

which can forward traffic to security appliances.

Conceptually:

```
          EC2 / ENI
             │
       ┌─────┴─────┐
       │           │
 Original       Mirrored
 Traffic         Traffic
       │           │
       ▼           ▼
 Application   NLB / Security
               Appliance
```

---

# 17. Same VPC or Different VPC

The source and target can be in:

### Same VPC

```
EC2/ENI ───► Security Appliance
```

### Different VPCs

For example, using **VPC Peering**:

```
VPC A                     VPC B

EC2 ───── Traffic Mirror ─────► Security Appliance
```

---

# 18. Capture All Packets or Selected Traffic

Traffic Mirroring can be configured to capture:

- All packets
- Only packets matching your filtering requirements

You can also use packet truncation to capture only a portion of packets when appropriate.

---

# 🧠 VPC Traffic Mirroring — Exam Memory

> **Traffic Mirroring = Copy network traffic for inspection**
> 

### Typical use case:

```
EC2
 │
 ├── Normal traffic → Application
 │
 └── Copy → Security Appliance
                 │
                 ▼
        Threat Monitoring
        Content Inspection
        Troubleshooting
```

### ⭐ Exam clue

> "Security team wants to inspect/capture packets from an EC2 instance without interrupting the original traffic."
> 

➡️ **VPC Traffic Mirroring**

---

# 🔥 Final SAA-C03 Revision

### Transit Gateway

**Many VPCs + many networks → central hub**

```
VPCs
 │
 ├── VPC
 ├── VPC
 ├── VPC
 │
 ▼
Transit Gateway
 │
 ├── VPN
 ├── Direct Connect Gateway
 └── Other TGWs
```

### ECMP

**Multiple equal-cost VPN paths → distribute traffic → increase aggregate bandwidth**

### TGW Peering

**TGW Region A ↔ TGW Region B**

### AWS RAM

**Share Transit Gateway across AWS accounts**

### TGW Route Tables

**Control which networks can communicate**

### VPC Traffic Mirroring

**Copy packets → security appliance → inspect/troubleshoot**

## 🎯 One-line memory

> **Transit Gateway = "Connect everything through one central hub."**
> 

> **ECMP = "Use multiple equal-cost VPN paths."**
> 

> **TGW Peering = "Connect TGWs across Regions."**
> 

> **RAM = "Share TGW across accounts."**
> 

> **Traffic Mirroring = "Copy packets for inspection."**
>
