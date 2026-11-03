# Gateway Load Balancer (GWLB)

**Gateway Load Balancer (GWLB)** is designed to **deploy, scale, and manage third-party or custom network security appliances**.

### Core idea ⭐⭐⭐⭐⭐

> **GWLB = Load balancer for network/security appliances**
> 

Examples:

- Firewalls
- Intrusion Detection Systems (IDS)
- Intrusion Prevention Systems (IPS)
- Deep Packet Inspection (DPI)
- Traffic inspection appliances
- Payload manipulation appliances

---

## How GWLB works

```
Users / Traffic
       │
       ▼
 Gateway Load Balancer
       │
       ▼
 ┌─────┼─────┐
 ▼     ▼     ▼
FW-1  FW-2  FW-3
(Security Appliances)
       │
       ▼
 Application
```

GWLB distributes traffic across multiple security appliances.

If traffic volume increases, you can scale the appliance fleet.

---

## GWLB has two major functions

| Function | What it does |
| --- | --- |
| **Transparent Network Gateway** | Provides a single entry/exit point for traffic going through the appliances |
| **Load Balancer** | Distributes traffic across multiple security appliances |

So think:

> **Gateway + Load Balancer = GWLB**
> 

---

## Layer & Protocol

| Feature | GWLB |
| --- | --- |
| OSI Layer | **Layer 3 — Network Layer** |
| Works with | **IP packets** |
| Encapsulation protocol | **GENEVE** |
| GENEVE port | **UDP 6081** |
| Main purpose | Security/network appliances |

### ⭐ Exam memory

> **GWLB → Layer 3 → GENEVE → UDP 6081**
> 

---

## Example: Firewall Inspection

Suppose a company wants **all VPC traffic inspected by third-party firewalls**.

```
                  Internet
                     │
                     ▼
                 GWLB
                     │
             ┌───────┼───────┐
             ▼       ▼       ▼
           FW-1    FW-2    FW-3
             │       │       │
             └───────┼───────┘
                     │
                     ▼
               Application
```

Instead of manually sending traffic to each firewall, GWLB distributes the traffic across the firewall fleet.

---

## GWLB Target Group

The targets are typically **network virtual appliances**.

```
GWLB
  │
  ▼
Target Group
  │
  ├── Firewall appliance 1
  ├── Firewall appliance 2
  └── Firewall appliance 3
```

The appliances can be provided by **third-party vendors** or custom-built.

---

# GWLB vs ALB vs NLB

| Feature | **ALB** | **NLB** | **GWLB** |
| --- | --- | --- | --- |
| Layer | L7 | L4 | **L3** |
| Main purpose | HTTP applications | TCP/UDP high-performance traffic | **Security appliances** |
| HTTP routing | ✅ | ❌ | ❌ |
| Path-based routing | ✅ | ❌ | ❌ |
| TCP | ❌ as primary model | ✅ | IP packets |
| UDP | ❌ | ✅ | IP packets |
| Firewall appliances | ❌ | ❌ | **✅** |
| IDS/IPS | ❌ | ❌ | **✅** |
| Deep packet inspection | ❌ | ❌ | **✅** |
| GENEVE | ❌ | ❌ | **✅ UDP 6081** |

---

## SAA-C03 Decision Rule ⭐⭐⭐⭐⭐

When the question says:

**"Deploy third-party firewalls"**

→ **GWLB**

**"Intrusion detection/prevention"**

→ **GWLB**

**"Deep packet inspection"**

→ **GWLB**

**"Security appliances need to inspect all traffic"**

→ **GWLB**

**"Distribute traffic across a fleet of network virtual appliances"**

→ **GWLB**

### Final memory

> 🟢 **ALB → HTTP/HTTPS applications**
> 
> 
> 🔵 **NLB → TCP/UDP, high performance**
> 
> 🟠 **GWLB → Firewalls/security appliances**
> 
> ⚪ **CLB → Legacy/old generation**
>
