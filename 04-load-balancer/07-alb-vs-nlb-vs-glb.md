# ALB vs NLB vs GWLB — AWS SAA-C03 Master Comparison

> **Fastest exam rule:**
> 
> 
> **ALB = HTTP/HTTPS applications** | **NLB = TCP/UDP/TLS high-performance networking** | **GWLB = security/network appliances**
> 

| Feature | 🟢 **ALB** | 🔵 **NLB** | 🟠 **GWLB** |
| --- | --- | --- | --- |
| **Full Name** | Application Load Balancer | Network Load Balancer | Gateway Load Balancer |
| **Main Purpose** | Load balance **web applications** | Load balance **network traffic** | Load balance **security appliances** |
| **OSI Layer** ⭐ | **Layer 7** | **Layer 4** | **Layer 3** |
| **Understands** | HTTP requests | TCP/UDP/TLS connections | IP packets |
| **Main Protocols** | HTTP, HTTPS | TCP, TLS, UDP | IP + GENEVE |
| **GENEVE** | ❌ | ❌ | ✅ |
| **GENEVE Port** | — | — | **UDP 6081** ⭐ |
| Path-based routing | ✅ | ❌ | ❌ |
| Host-based routing | ✅ | ❌ | ❌ |
| Query-string routing | ✅ | ❌ | ❌ |
| Header-based routing | ✅ | ❌ | ❌ |
| HTTP → HTTPS redirect | ✅ | ❌ | ❌ |
| WebSocket | ✅ | ✅ | Not the primary purpose |
| Static IP addresses | ❌ | **✅** | ❌ |
| Elastic IP support | ❌ | **✅** | ❌ |
| Very high performance | Good | **Excellent** | Designed for appliance traffic |
| Ultra-low latency | Good | **Excellent** | For inspection/appliance architecture |
| Security appliance support | ❌ | ❌ | **✅** |
| Firewall | ❌ | ❌ | **✅** |
| IDS/IPS | ❌ | ❌ | **✅** |
| Deep Packet Inspection | ❌ | ❌ | **✅** |
| Traffic inspection | ❌ | ❌ | **✅** |
| Third-party appliances | ❌ | ❌ | **✅** |
| Microservices | **✅ Excellent** | ✅ Possible | ❌ |
| ECS/containers | **✅ Excellent** | ✅ | Not primary use |
| Lambda target | **✅** | ❌ | ❌ |
| ALB as target | ❌ | **✅** | ❌ |
| EC2 targets | ✅ | ✅ | Appliances |
| IP targets | ✅ Private IP | ✅ Private IP | Appliance targets |
| Health checks | HTTP/HTTPS | TCP/HTTP/HTTPS | Appliance health checks |
| Typical architecture | Web app | Network application | Security inspection |
| **Exam keyword** | **HTTP/HTTPS** | **TCP/UDP + high performance** | **Firewall/IDS/IPS** |

---

# Scenario-Based Comparison

| Scenario | Best Choice | Why? | 🔑 Exam Clue |
| --- | --- | --- | --- |
| Public website using HTTPS | 🟢 **ALB** | Understands HTTP/HTTPS | **Web application** |
| `/users` and `/orders` need different backend services | 🟢 **ALB** | Path-based routing | **Path-based routing** |
| `api.example.com` and `admin.example.com` need different targets | 🟢 **ALB** | Host-based routing | **Hostname routing** |
| Route based on HTTP headers | 🟢 **ALB** | Layer 7 inspection | **Header-based routing** |
| Route based on query string | 🟢 **ALB** | Understands HTTP requests | **Query string** |
| Microservices architecture | 🟢 **ALB** | Application-level routing | **Microservices** |
| ECS containers with dynamic ports | 🟢 **ALB** | Dynamic port mapping | **ECS + HTTP** |
| Need HTTP → HTTPS redirect | 🟢 **ALB** | Supports redirects | **Redirect** |
| Application uses TCP | 🔵 **NLB** | Layer 4 load balancing | **TCP** |
| Application uses UDP | 🔵 **NLB** | Supports UDP | **UDP** |
| Need static IP addresses **/whitelisted** IP/**Elastic IP** | 🔵 **NLB** | Supports static IP/EIP | **Static IP** |
| Extremely high-performance network traffic | 🔵 **NLB** | Layer 4, high throughput | **High performance** |
| Very low network latency | 🔵 **NLB** | Layer 4 networking | **Low latency** |
| Need TLS pass-through / TLS traffic handling | 🔵 **NLB** | Supports TLS | **TLS** |
| Deploy third-party firewalls | 🟠 **GWLB** | Designed for security appliances | **Firewall** |
| IDS/IPS inspection | 🟠 **GWLB** | Appliance fleet management | **IDS/IPS** |
| Deep packet inspection | 🟠 **GWLB** | Network traffic inspection | **DPI** |
| Distribute traffic among security appliances | 🟠 **GWLB** | Load balances appliances | **Security appliance fleet** |
| Need a transparent gateway for inspection | 🟠 **GWLB** | Gateway + load balancer | **Transparent inspection** |
| Security appliances communicate using GENEVE | 🟠 **GWLB** | Native GENEVE support | **GENEVE 6081** |

---

# Architecture: ALB

```
                 Users
                   │
                   ▼
              ┌─────────┐
              │   ALB   │
              └────┬────┘
                   │
          HTTP routing rules
       ┌───────────┼───────────┐
       ▼           ▼           ▼
    /users       /orders    /products
       │           │           │
       ▼           ▼           ▼
   Target       Target       Target
   Group        Group        Group
```

### Think:

> **"I need to understand the HTTP request." → ALB**
> 

---

# Architecture: NLB

```
                Client
                   │
             TCP / UDP / TLS
                   │
                   ▼
              ┌─────────┐
              │   NLB   │
              └────┬────┘
                   │
          Layer 4 load balancing
       ┌───────────┼───────────┐
       ▼           ▼           ▼
      EC2         EC2         EC2
```

### Think:

> **"I don't care about HTTP content; I need fast network-level traffic handling." → NLB**
> 

---

# Architecture: GWLB

```
                 Traffic
                    │
                    ▼
              ┌─────────┐
              │   GWLB  │
              └────┬────┘
                   │
              GENEVE / 6081
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
      FW-1       FW-2       FW-3
        │          │          │
        └──────────┼──────────┘
                   │
                   ▼
              Application
```

### Think:

> **"I need to inspect/protect traffic using virtual security appliances." → GWLB**
> 

---

# ⭐ The 3-Second Exam Decision

| Question contains... | Choose |
| --- | --- |
| **HTTP / HTTPS** | 🟢 **ALB** |
| **Path-based routing** | 🟢 **ALB** |
| **Host-based routing** | 🟢 **ALB** |
| **Query string / HTTP header** | 🟢 **ALB** |
| **Microservices / ECS** | 🟢 **ALB** |
| **TCP** | 🔵 **NLB** |
| **UDP** | 🔵 **NLB** |
| **TLS** | 🔵 **NLB** |
| **Static IP / Elastic IP** | 🔵 **NLB** |
| **High throughput / ultra-low latency network traffic** | 🔵 **NLB** |
| **Firewall** | 🟠 **GWLB** |
| **IDS / IPS** | 🟠 **GWLB** |
| **Deep Packet Inspection** | 🟠 **GWLB** |
| **Third-party security appliance** | 🟠 **GWLB** |
| **GENEVE** | 🟠 **GWLB** |
| **UDP 6081** | 🟠 **GWLB** |
| **Transparent network gateway** | 🟠 **GWLB** |

---

# 🧠 Final Memory Formula

```
ALB
 ↓
Application
 ↓
HTTP / HTTPS
 ↓
Layer 7
 ↓
SMART ROUTING
```

```
NLB
 ↓
Network
 ↓
TCP / UDP / TLS
 ↓
Layer 4
 ↓
FAST + STATIC IP
```

```
GWLB
 ↓
Security
 ↓
IP Packets
 ↓
Layer 3
 ↓
FIREWALL / IDS / IPS
 ↓
GENEVE UDP 6081
```

### One-line SAA-C03 memory

> 🟢 **ALB = Application traffic**
> 
> 
> 🔵 **NLB = Network traffic**
> 
> 🟠 **GWLB = Security appliance traffic**
>
