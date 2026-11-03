# AWS SAA-C03 — Elastic Load Balancing (ELB)

## 1. What is Load Balancing?

A **Load Balancer** receives client requests and distributes them across multiple backend servers.

```
                    Users
                      |
                      ↓
                Load Balancer
               /      |      \
              ↓       ↓       ↓
            EC2      EC2      EC2
```

Instead of users directly accessing individual EC2 instances, they access the **Load Balancer's DNS name**.

---

# 2. Why Do We Need a Load Balancer?

| Benefit | What it means | Example |
| --- | --- | --- |
| **Distribute traffic** | Spread requests across multiple servers | 10,000 requests → 5 EC2 |
| **Single access point** | Users use one DNS endpoint | `app.example.com` |
| **High Availability** | Traffic can continue if one server/AZ fails | EC2 in AZ-1 + AZ-2 |
| **Health Checks** | Stops sending traffic to unhealthy instances | `/health` returns failure |
| **SSL/TLS termination** | LB handles HTTPS encryption/decryption | Client HTTPS → ALB → HTTP EC2 |
| **Stickiness** | Keep a user connected to the same backend | Session-based applications |
| **Auto Scaling integration** | Automatically send traffic to new ASG instances | ASG launches EC2 → LB registers it |
| **Security separation** | Public LB can communicate with private EC2 | Internet → ALB → private EC2 |

---

# 3. Elastic Load Balancer (ELB)

**ELB = AWS managed load balancing service.**

AWS manages:

- Infrastructure
- Maintenance
- Patching/upgrades
- Scaling
- High availability

You mainly configure:

- Listener
- Port/protocol
- Target groups
- Health checks
- Routing rules
- Security groups where applicable

### Exam point

> **ELB itself is managed by AWS. You don't manually maintain the load-balancer servers.**
> 

---

# 4. AWS Load Balancer Types — Master Table

| Load Balancer | Layer | Main Protocols | Best For | Key Feature | SAA-C03 Keyword |
| --- | --- | --- | --- | --- | --- |
| **Application Load Balancer (ALB)** | **Layer 7** | HTTP, HTTPS, WebSocket | Web applications / APIs | Content-based routing | **HTTP/HTTPS** |
| **Network Load Balancer (NLB)** | **Layer 4** | TCP, TLS, UDP | Ultra-high performance / TCP/UDP apps | Very high performance, static IP support | **TCP/UDP** |
| **Gateway Load Balancer (GWLB)** | **Layer 3** | IP packets | Security appliances/firewalls | Transparent traffic inspection | **Security appliances** |
| **Classic Load Balancer (CLB)** | Older generation | HTTP, HTTPS, TCP, SSL | Legacy applications | Old generation | **Avoid for new designs** |

### Easy memory

> **ALB → Application → HTTP/HTTPS**
> 
> 
> **NLB → Network → TCP/UDP/TLS**
> 
> **GWLB → Gateway → Security appliances**
> 
> **CLB → Classic → Legacy**
> 

---

# 5. ALB — Application Load Balancer

ALB operates at **Layer 7 (Application Layer)**.

It understands HTTP/HTTPS requests.

```
Client
   |
 HTTPS
   ↓
  ALB
   |
   ├── /api     → API servers
   ├── /images  → Image servers
   └── /admin   → Admin servers
```

### ALB can route based on:

- Hostname
- URL path
- HTTP headers
- HTTP methods
- Query strings
- Source IP

### Example

```
example.com/api/*
       ↓
      ALB
       ↓
 API Target Group

example.com/images/*
       ↓
      ALB
       ↓
 Image Target Group
```

### ALB supports

- HTTP
- HTTPS
- WebSocket
- Path-based routing
- Host-based routing
- HTTP redirects
- Fixed responses
- Authentication integrations
- SSL/TLS termination
- WebSocket
- WAF integration

---

# 6. NLB — Network Load Balancer

NLB operates at **Layer 4**.

It handles network-level connections rather than understanding HTTP application content.

### Supports

- TCP
- TLS
- UDP

```
Client
   |
 TCP/UDP
   ↓
 NLB
   |
   ├── EC2
   ├── EC2
   └── EC2
```

### Choose NLB when you need:

| Requirement | NLB |
| --- | --- |
| TCP application | ✅ |
| UDP application | ✅ |
| TLS traffic | ✅ |
| Very high performance | ✅ |
| Very low latency | ✅ |
| Static IP | ✅ |
| Preserve source IP | ✅ |
| HTTP path-based routing | ❌ → use ALB |

### Exam clue

> **"TCP/UDP traffic" → NLB**
> 

> **"Static IP for load balancer" → NLB**
> 

---

# 7. GWLB — Gateway Load Balancer

GWLB is designed for **network/security appliances**.

It operates at **Layer 3**.

```
                 Internet
                    |
                    ↓
                  GWLB
                    |
          Security Appliances
          /       |        \
      Firewall  IDS/IPS   Inspection
                    |
                    ↓
                 Application
```

### Use cases

- Firewalls
- Intrusion Detection Systems
- Intrusion Prevention Systems
- Deep packet inspection
- Network traffic inspection
- Third-party security appliances

### Important

GWLB uses **GENEVE** protocol on port **6081**.

> **Exam clue: "centralized security appliance / firewall inspection" → GWLB**
> 

---

# 8. CLB — Classic Load Balancer

CLB is the **old generation**.

| CLB | Status |
| --- | --- |
| Introduced | 2009 |
| Generation | v1 |
| Modern replacement | ALB / NLB |
| Recommended for new applications? | ❌ |
| Existing legacy application? | Possible |

For SAA-C03:

> If the question doesn't specifically require CLB, prefer **ALB/NLB/GWLB** based on the requirement.
> 

---

# 9. Load Balancer Health Checks

Health checks allow the load balancer to determine whether a backend target is healthy.

Example:

```
ALB
 |
 | HTTP :4567
 | GET /health
 ↓
EC2
 |
 └── HTTP 200 OK
       ↓
     Healthy
```

If the target fails the health check:

```
ALB
 |
 ├── EC2-A → Healthy ✅ → receives traffic
 |
 └── EC2-B → Unhealthy ❌ → no traffic
```

### Health check contains

| Parameter | Example |
| --- | --- |
| Protocol | HTTP |
| Port | 4567 |
| Path | `/health` |
| Healthy response | HTTP 200 |
| Frequency | Configurable |
| Thresholds | Configurable |

### Important exam concept

> **Unhealthy EC2 → Load Balancer stops sending new traffic to it.**
> 

---

# 10. Load Balancer + Auto Scaling Group

This is one of the most important SAA architectures.

```
                 Users
                   |
                   ↓
                  ALB
                   |
          ┌────────┴────────┐
          ↓                 ↓
        AZ-1               AZ-2
      EC2 EC2             EC2 EC2
          └────────┬────────┘
                   ↓
                  ASG
```

### What each service does

| Service | Responsibility |
| --- | --- |
| **ALB** | Distributes requests |
| **ASG** | Adds/removes/replaces EC2 |
| **CloudWatch** | Provides metrics/alarms |
| **EC2** | Runs application |

### Example

Traffic increases:

```
2 EC2
 ↓
ASG detects increased demand
 ↓
4 EC2
 ↓
ALB automatically sends traffic to new instances
```

Traffic decreases:

```
4 EC2
 ↓
Demand decreases
 ↓
ASG
 ↓
2 EC2
```

---

# 11. Load Balancer Security Groups

A very important SAA security pattern:

```
Internet
   |
   ↓
ALB
[ALB Security Group]
   |
   ↓
EC2
[EC2 Security Group]
```

### Recommended configuration

**ALB Security Group**

```
Inbound:
HTTP  80   ← Internet
HTTPS 443  ← Internet
```

**EC2 Security Group**

```
Inbound:
HTTP 80 ← ALB Security Group
```

### Why?

Don't allow:

```
Internet → EC2
```

Instead:

```
Internet → ALB → EC2
```

This keeps EC2 instances private and ensures only the load balancer can reach them.

---

# 12. Public vs Internal Load Balancer

| Type | Meaning | Example |
| --- | --- | --- |
| **Internet-facing** | Has public connectivity | Internet → ALB → EC2 |
| **Internal** | Only accessible privately | ALB → private application |
| **Public ALB** | Public web application | Website |
| **Internal ALB** | Internal services/microservices | Frontend → backend |

### Example

```
Internet
   |
Public ALB
   |
Private EC2
```

For internal application:

```
Frontend
   |
Internal ALB
   |
Backend EC2
```

---

# 13. SSL/TLS Termination

A load balancer can handle HTTPS encryption.

```
User
 HTTPS
   ↓
 ALB
 TLS termination
   ↓
 HTTP
   ↓
 EC2
```

Instead of configuring SSL certificates individually on every EC2 instance, you can install the certificate on the load balancer using **AWS Certificate Manager (ACM)**.

### Exam clue

> "Centralize HTTPS/SSL certificate management" → **ALB/NLB + ACM**
> 

---

# 14. Load Balancer Stickiness

Normally:

```
User
 ↓
ALB
 ├── EC2-A
 ├── EC2-B
 └── EC2-C
```

Requests may go to different instances.

With **stickiness**:

```
User A
 ↓
ALB
 ↓
EC2-A

User A's future requests
 ↓
EC2-A
```

Useful when an application maintains session information locally.

### But remember

For highly scalable applications, it's generally better to make the application **stateless** and store session state externally, such as in a database or cache.

---

# 15. ALB vs NLB vs GWLB — Master Comparison

| Feature | **ALB** | **NLB** | **GWLB** |
| --- | --- | --- | --- |
| Layer | L7 | L4 | L3 |
| HTTP | ✅ | Can handle TCP-based HTTP but doesn't provide ALB-style HTTP routing | ❌ |
| HTTPS | ✅ | ✅ TLS | ❌ |
| TCP | ❌ as an application routing protocol | ✅ | IP traffic |
| UDP | ❌ | ✅ | IP traffic |
| WebSocket | ✅ | ❌ ALB is the usual choice | ❌ |
| Path-based routing | ✅ | ❌ | ❌ |
| Host-based routing | ✅ | ❌ | ❌ |
| Static IP | ❌ | ✅ | — |
| Very high performance | Good | **Excellent** | High |
| Security appliances | ❌ | ❌ | **✅** |
| Firewall inspection | ❌ | ❌ | **✅** |
| WAF integration | **✅** | Limited/architecture-dependent | ❌ |
| Main purpose | Web/API traffic | Network traffic | Security appliance traffic |
| SAA keyword | **HTTP/HTTPS** | **TCP/UDP/static IP** | **Firewall/inspection** |

---

# 16. Load Balancer Type — Scenario Table

| Scenario | Best Choice |
| --- | --- |
| Website using HTTP/HTTPS | **ALB** |
| Path-based routing `/api` and `/images` | **ALB** |
| Host-based routing `api.example.com` vs `www.example.com` | **ALB** |
| WebSocket application | **ALB** |
| TCP application | **NLB** |
| UDP application | **NLB** |
| Need static IP addresses | **NLB** |
| Ultra-low latency/high-performance network traffic | **NLB** |
| TLS/TCP application | **NLB** |
| Deploy third-party firewalls | **GWLB** |
| Intrusion detection/prevention | **GWLB** |
| Deep packet inspection | **GWLB** |
| Legacy application specifically requiring classic LB | **CLB** |
| Public web application with private EC2 | **Internet-facing ALB + private EC2** |
| Internal microservice routing | **Internal ALB** |

---

# 17. Load Balancer + Security Architecture

### Recommended public web architecture

```
                    Internet
                       |
                       ↓
                  CloudFront
                       |
                     AWS WAF
                       |
                       ↓
               Internet-facing ALB
               [ALB Security Group]
                       |
                ┌──────┴──────┐
                ↓             ↓
             AZ-1           AZ-2
             EC2             EC2
             [Private SG]    [Private SG]
                ↑             ↑
                └──── ASG ────┘
```

Each layer has a purpose:

| Component | Purpose |
| --- | --- |
| CloudFront | Global caching/edge delivery |
| WAF | HTTP request filtering |
| ALB | HTTP/HTTPS load balancing |
| ASG | EC2 scaling/replacement |
| EC2 | Application |
| Multiple AZs | High availability |

---

# 18. Common SAA-C03 Confusions

| If question says... | Think... |
| --- | --- |
| **HTTP/HTTPS web application** | ALB |
| **Path-based routing** | ALB |
| **Host-based routing** | ALB |
| **WebSocket** | ALB |
| **TCP** | NLB |
| **UDP** | NLB |
| **Static IP for LB** | NLB |
| **Very high performance / low latency** | NLB |
| **Security appliance** | GWLB |
| **Firewall inspection** | GWLB |
| **Deep packet inspection** | GWLB |
| **Health check failed** | LB stops sending traffic to target |
| **HTTPS termination** | ALB/NLB + ACM |
| **Public users → private EC2** | Internet-facing ALB + private EC2 |
| **Only LB should access EC2** | EC2 SG allows LB SG |
| **Automatically add EC2 during traffic spike** | ASG |
| **Distribute traffic across AZs** | Multi-AZ Load Balancer |
| **Old/legacy load balancer** | CLB |

---

# ⭐ Final Exam Memory

```
                LOAD BALANCERS
                      |
        ┌─────────────┼─────────────┐
        ↓             ↓             ↓
       ALB           NLB           GWLB
       L7             L4            L3
       HTTP          TCP            IP
       HTTPS         UDP
       WebSocket     TLS
        |             |              |
        ↓             ↓              ↓
    Web/API       Network apps   Security
    routing       High perf      appliances
    /api          Static IP       Firewall
    /images                       IDS/IPS
```

### The 3 rules to memorize:

> **ALB = HTTP/HTTPS + intelligent application routing**
> 

> **NLB = TCP/UDP/TLS + high performance + static IP**
> 

> **GWLB = security appliances + traffic inspection**
> 

And for architecture:

> **ALB + ASG + Multi-AZ EC2 = classic highly available, scalable web application architecture.**
>
