# VPC Flow Logs — AWS SAA-C03 Complete Exam Notes

VPC Flow Logs are **very important for SAA-C03 troubleshooting questions**, especially when the question gives you **ACCEPT/REJECT**, **Security Groups**, **NACLs**, ports, IP addresses, or asks where traffic is being blocked.

The key idea:

> **VPC Flow Logs = network traffic metadata that tells you who communicated with whom, on which port, and whether AWS accepted or rejected the traffic.**
> 

---

# 1. What are VPC Flow Logs?

VPC Flow Logs capture information about **IP traffic flowing to and from network interfaces**.

They are mainly used for:

- 🔍 Troubleshooting connectivity
- 🔐 Security investigation
- 📊 Traffic analysis
- 🚨 Detecting suspicious/malicious traffic
- 🛠️ Finding whether traffic is being rejected by **Security Groups or NACLs**

Think of them as a **network CCTV/logbook**.

They don't capture the actual application data/payload.

### Example

Suppose:

```
Your Laptop
     |
     | TCP 443
     ↓
EC2
10.0.1.10
```

Flow Logs can tell you something like:

```
Source IP     : 203.0.113.10
Destination   : 10.0.1.10
Source Port   : 50120
Dest Port     : 443
Protocol      : TCP
Action        : ACCEPT
```

But they **do not show the HTTP request body, password, webpage contents, etc.**

---

# 2. Where can you create Flow Logs?

Flow Logs can be created at different scopes:

### VPC Flow Logs

Capture traffic for interfaces within the VPC.

```
VPC
├── Subnet A
│    ├── EC2
│    └── EC2
│
└── Subnet B
     ├── EC2
     └── EC2
```

### Subnet Flow Logs

Capture traffic associated with network interfaces in a specific subnet.

### ENI Flow Logs

Capture traffic for a specific **Elastic Network Interface**.

---

# 3. Easy way to understand the three levels

```
VPC
│
├── Subnet A
│    │
│    ├── ENI-1 → EC2
│    ├── ENI-2 → EC2
│    └── ENI-3 → EC2
│
└── Subnet B
     │
     └── ENI-4 → EC2
```

You can think:

| Flow Log type | Scope |
| --- | --- |
| VPC | Broadest |
| Subnet | One subnet |
| ENI | Specific network interface |

### Exam clue

If the question says:

> "Monitor traffic for a particular EC2 network interface"
> 

Think:

**ENI Flow Logs**

---

# 4. What can Flow Logs capture?

Flow Logs capture **metadata about network traffic**, such as:

- Source IP
- Destination IP
- Source port
- Destination port
- Protocol
- Packets
- Bytes
- Start/end time
- ACCEPT / REJECT
- Log status

They are excellent for answering:

> **Who is talking to whom? On what port? How much traffic? Was it accepted or rejected?**
> 

---

# 5. Where can Flow Logs send data?

VPC Flow Logs can publish to:

### 1. CloudWatch Logs

```
VPC Flow Logs
      ↓
CloudWatch Logs
      ↓
CloudWatch Logs Insights
```

Useful for:

- Searching logs
- Filtering traffic
- Creating metrics
- Alarms

---

### 2. Amazon S3

```
VPC Flow Logs
      ↓
S3
      ↓
Athena
      ↓
Query / Analyze
```

Good for:

- Long-term storage
- Large-scale analysis
- Historical traffic analysis

---

### 3. Amazon Data Firehose

```
VPC Flow Logs
      ↓
Data Firehose
      ↓
Supported destination
```

Useful when you need streaming delivery into supported destinations.

---

# 6. What AWS resources can have Flow Logs?

A very important exam point:

Flow Logs aren't limited to EC2.

They can capture traffic involving network interfaces associated with services such as:

- EC2
- Elastic Load Balancing
- RDS
- ElastiCache
- Redshift
- NAT Gateway
- Transit Gateway
- VPC Endpoint
- etc.

So if an exam question says:

> "Investigate network traffic involving an AWS-managed network interface."
> 

Think:

**VPC Flow Logs**

---

# 7. Flow Log example

A simplified Flow Log record could look like:

```
Account
        ↓
Interface ID
        ↓
Source IP
        ↓
Destination IP
        ↓
Source Port
        ↓
Destination Port
        ↓
Protocol
        ↓
Packets / Bytes
        ↓
Time
        ↓
Action
```

For example:

```
eni-123456

Source IP       172.31.16.139
Destination IP  172.31.16.21

Source Port     20641
Destination     22

Protocol        TCP
Packets         22
Bytes           4242

Action          ACCEPT
```

Interpretation:

> An internal host at `172.31.16.139` attempted TCP communication with `172.31.16.21` on **port 22 (SSH)** and the traffic was accepted.
> 

---

# 8. The fields you REALLY need for SAA-C03

Don't memorize every field.

Focus on these:

| Field | What it tells you |
| --- | --- |
| `srcaddr` | Who initiated traffic |
| `dstaddr` | Destination |
| `srcport` | Source port |
| `dstport` | Destination service port |
| `protocol` | TCP/UDP/etc. |
| `packets` | Number of packets |
| `bytes` | Amount of traffic |
| `action` | ACCEPT or REJECT |
| `start/end` | When traffic occurred |
| `interface-id` | Which ENI was involved |

---

# 9. The MOST important field: `ACTION`

For exam questions:

> **Look at ACTION first.**
> 

It tells you:

```
ACCEPT
   ↓
Traffic was accepted

REJECT
   ↓
Traffic was rejected
```

This becomes extremely useful when troubleshooting **Security Groups vs NACLs**.

---

# 10. Security Group vs NACL — remember this first

Before troubleshooting Flow Logs, remember:

### Security Group

```
Instance level
     ↓
STATEFUL
     ↓
Allow only
```

### NACL

```
Subnet level
     ↓
STATELESS
     ↓
Allow + Deny
     ↓
Rules evaluated in order
```

The biggest difference:

> **Security Group = Stateful**
> 

> **NACL = Stateless**
> 

---

# 11. Why stateful vs stateless matters

Suppose your client connects to a web server:

```
Client
IP: 1.2.3.4
Port: 50000
     |
     | TCP → 443
     ↓
Web Server
IP: 10.0.1.10
Port: 443
```

The server responds:

```
Web Server
10.0.1.10:443
     |
     | TCP → 50000
     ↓
Client
1.2.3.4:50000
```

The client uses an **ephemeral port** such as:

```
50000
```

---

# 12. Security Group behavior

Security Groups are stateful.

If the inbound request is allowed:

```
Client
   ↓
443
   ↓
EC2

SG: ALLOW
```

The response is automatically allowed by the Security Group.

You don't need to explicitly create a separate outbound rule just to permit the response **because the SG tracks the connection state**—assuming the SG's outbound rules otherwise permit the traffic.

### Memory trick

> **SG remembers the conversation.**
> 

---

# 13. NACL behavior

NACLs are stateless.

They don't remember the connection.

Therefore:

```
Request
Client → Server
```

and

```
Response
Server → Client
```

must each be allowed by the appropriate NACL rules.

That's why ephemeral ports are important.

---

# 14. Classic NACL + ephemeral port problem

Suppose:

```
Client
1.2.3.4:50105
       |
       | TCP → 443
       ↓
Web Server
10.0.1.10:443
```

Response:

```
Web Server
10.0.1.10:443
       |
       | TCP → 50105
       ↓
Client
1.2.3.4:50105
```

Your NACL may need to allow:

```
Inbound:
TCP 443
```

and for the return traffic:

```
Outbound:
TCP 1024-65535
```

The exact ephemeral port range depends on the client OS and traffic pattern, so don't blindly memorize `1024-65535` as a universal OS range.

### Exam idea

> **NACL + return traffic = think ephemeral ports.**
>
