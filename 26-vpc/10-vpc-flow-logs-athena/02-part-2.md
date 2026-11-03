# 15. How Flow Logs help troubleshoot

Imagine:

```
Internet
   |
   ↓
EC2 Web Server
TCP 443
```

User says:

> "I can't connect to my web server."
> 

You inspect Flow Logs.

You see:

```
Source:      1.2.3.4
Destination: 10.0.1.10
Port:        443
Action:      REJECT
```

Now you know:

> The traffic is being rejected somewhere in the network controls.
> 

Then investigate:

- Security Group
- NACL
- Route configuration
- Other network controls

---

# 16. The important SG/NACL Flow Log logic

This is one of the best SAA-C03 troubleshooting patterns.

### Incoming request

```
Internet
   ↓
NACL inbound
   ↓
Security Group inbound
   ↓
EC2
```

If the inbound request is **REJECTED**, it could be blocked by:

- NACL
- Security Group

So:

> **Inbound REJECT → SG or NACL**
> 

---

### Incoming request accepted, response rejected

```
Client
  ↓
Inbound → ACCEPT
  ↓
EC2
  ↓
Outbound → REJECT
```

Because the Security Group is **stateful**, a properly established response is automatically allowed by the SG's state tracking.

Therefore, a return-path rejection strongly points toward:

> **NACL**
> 

assuming you're analyzing the expected response traffic.

---

# 17. Outgoing request

Suppose:

```
EC2
 ↓
Internet
```

Flow Logs show:

```
Outbound → REJECT
```

Possible causes:

- Security Group
- NACL

But if:

```
Outbound → ACCEPT
Inbound response → REJECT
```

the response rejection strongly suggests:

> **NACL**
> 

because NACLs are stateless.

---

# 18. ⭐ SAA-C03 troubleshooting table

| Flow pattern | What to investigate |
| --- | --- |
| Inbound REJECT | **SG or NACL** |
| Inbound ACCEPT → Outbound REJECT | **NACL** is the key suspect |
| Outbound REJECT | **SG or NACL** |
| Outbound ACCEPT → Inbound REJECT | **NACL** is the key suspect |
| Return traffic not allowed | **NACL / ephemeral ports** |
| SG allows request but response fails | Check **NACL** |
| NACL allows request but SG blocks | Check **SG** |

### Why?

```
SG = STATEFUL
NACL = STATELESS
```

That's the core concept behind these questions.

---

# 19. Architecture: Flow Logs + CloudWatch

A common architecture:

```
                    VPC
                     │
              VPC Flow Logs
                     │
                     ▼
              CloudWatch Logs
                     │
          ┌──────────┴──────────┐
          │                     │
          ▼                     ▼
     Logs Insights         Metric Filter
                                │
                                ▼
                            CloudWatch
                              Alarm
                                │
                                ▼
                               SNS
```

### Example

You want to detect excessive SSH attempts.

Flow Logs:

```
dstport = 22
```

You can analyze the traffic and create monitoring/alerting around suspicious patterns.

---

# 20. Architecture: Flow Logs + S3 + Athena

For large-scale analysis:

```
VPC
 │
 ▼
VPC Flow Logs
 │
 ▼
S3
 │
 ▼
Athena
 │
 ▼
SQL queries
 │
 ▼
Traffic analysis
```

Example question:

> "Which IP addresses generated the most traffic toward port 22?"
> 

Athena can query the Flow Logs stored in S3.

---

# 21. Flow Logs + QuickSight

For dashboards:

```
VPC Flow Logs
       ↓
      S3
       ↓
    Athena
       ↓
  QuickSight
       ↓
Dashboard
```

You could visualize:

- Top source IPs
- Top destination IPs
- Top ports
- Traffic volume
- Rejected traffic
- Traffic patterns

---

# 22. Flow Logs + CloudWatch Contributor Insights

Your notes mention:

```
VPC Flow Logs
      ↓
CloudWatch Logs
      ↓
Contributor Insights
      ↓
Top IP addresses
```

This can help identify top contributors such as:

> "Which IP addresses are generating the most traffic?"
> 

---

# 23. Flow Logs are NOT packet capture

This is a very important distinction.

### VPC Flow Logs

Give you:

```
Source IP
Destination IP
Port
Protocol
Packets
Bytes
ACCEPT/REJECT
```

They don't give you:

```
❌ HTTP body
❌ Password
❌ Actual application payload
❌ Full packet contents
```

### Memory trick

> **Flow Logs = "Who talked to whom?"**
> 

Not:

> "What did they say?"
> 

---

# 24. Flow Logs vs CloudTrail

Another potential exam trap.

|  | VPC Flow Logs | CloudTrail |
| --- | --- | --- |
| Main purpose | Network traffic | AWS API activity |
| Focus | IP/network communication | API calls |
| Example | EC2 → DB port 3306 | Someone called `RunInstances` |
| Source IP | ✅ | Can include caller source info |
| Port/protocol | ✅ | ❌ |
| SG/NACL troubleshooting | ✅ | ❌ |
| API auditing | ❌ | ✅ |

### Memory trick

> **Flow Logs = Network**
> 

> **CloudTrail = API actions**
> 

---

# 25. Flow Logs vs CloudWatch

Also don't confuse these.

### VPC Flow Logs

> Provides network traffic metadata.
> 

### CloudWatch

> Monitoring and observability platform that can **receive/analyze** Flow Logs when configured accordingly.
> 

So:

```
VPC Flow Logs = DATA SOURCE
CloudWatch = MONITORING / LOG ANALYSIS DESTINATION
```

---

# 26. IAM permissions for CloudWatch Logs

If sending Flow Logs to CloudWatch Logs, the relevant IAM role needs permissions to publish the logs.

Common permissions include:

```
logs:CreateLogGroup
logs:CreateLogStream
logs:PutLogEvents
```

Conceptually:

```
VPC Flow Logs
      ↓
IAM Role
      ↓
CloudWatch Logs
```

### Exam clue

If the question says:

> "VPC Flow Logs aren't being delivered to CloudWatch Logs."
> 

Check:

> **IAM role permissions**
> 

---

# 27. A complete real-world troubleshooting scenario

Imagine a 3-tier application:

```
                 Internet
                    │
                    ▼
             Application Tier
              EC2 / ALB
                    │
                    │ TCP 3306
                    ▼
              Database Tier
                 RDS
```

Application server can't connect to RDS.

You check Flow Logs:

```
Source:      10.0.1.20
Destination: 10.0.2.10
Source Port: 40000
Dest Port:   3306
Protocol:    TCP
Action:      REJECT
```

What do you investigate?

### Step 1 — Security Group

Does RDS SG allow:

```
TCP 3306
Source = Application SG
```

### Step 2 — NACL

Does the DB subnet NACL permit inbound TCP 3306?

And because NACL is stateless, does the return path permit the required ephemeral port?

### Step 3 — Routes

Do subnet route tables have the correct local routing?

### Step 4 — Flow Logs

Look at:

```
srcaddr
dstaddr
srcport
dstport
action
```

This gives you the evidence.

---

# 28. Another exam scenario: suspicious traffic

Suppose a company notices:

```
Thousands of requests
       ↓
Port 22
       ↓
EC2 instances
```

Security team wants to identify the source IPs.

Use:

> **VPC Flow Logs**
> 

Then analyze through:

```
CloudWatch Logs Insights
```

or:

```
S3 → Athena
```

depending on the requirements.

---

# 29. What Flow Logs don't do

Don't choose VPC Flow Logs when the requirement is primarily:

### "Which AWS API call caused this?"

→ **CloudTrail**

### "What vulnerabilities exist on EC2?"

→ **Amazon Inspector**

### "Detect suspicious AWS account activity"

→ **GuardDuty**

### "Capture application logs"

→ **CloudWatch Logs / application logging**

### "Capture network packets for deep packet inspection"

Flow Logs are **not packet capture**; you need an appropriate packet-capture/traffic-inspection solution.

---

# 30. 🧠 SAA-C03 Memory Map

Memorize this:

```
                 VPC FLOW LOGS
                       │
           "WHO TALKED TO WHOM?"
                       │
        ┌──────────────┼──────────────┐
        │              │              │
       IP             PORT         ACTION
        │              │              │
    src / dst       src / dst     ACCEPT/REJECT
                                      │
                              Troubleshooting
                                      │
                              ┌───────┴───────┐
                              │               │
                             SG              NACL
                          Stateful        Stateless
                           Allow           Allow/Deny
                                           Order matters
                                           Ephemeral ports
```

---

# 31. 🔥 Final SAA-C03 Cheat Sheet

### VPC Flow Logs

> **Capture network traffic metadata for troubleshooting, security analysis, and traffic monitoring.**
> 

### Can be created for:

- VPC
- Subnet
- ENI

### Can send to:

- **CloudWatch Logs**
- **S3**
- **Kinesis Data Firehose**

### Important fields:

> **Source IP → Destination IP → Source Port → Destination Port → Protocol → ACCEPT/REJECT**
> 

### Most important field:

> **ACTION**
> 

### Security Group:

> **Stateful + Allow only**
> 

### NACL:

> **Stateless + Allow/Deny + Rule order + ephemeral ports**
> 

### Critical troubleshooting clue:

> **If the initial request is ACCEPTED but the return traffic is REJECTED, investigate the NACL**, because Security Groups are stateful.
> 

### Analytics:

```
Flow Logs → CloudWatch Logs → Logs Insights
```

or

```
Flow Logs → S3 → Athena → QuickSight
```

### Memory trick:

> **Flow Logs = "Who talked to whom, on which port, and was it accepted?"**
> 

And for troubleshooting:

> **SG remembers. NACL forgets.**
> 

That one line is extremely useful for SAA-C03 questions involving **Flow Logs + Security Groups + NACLs**.
