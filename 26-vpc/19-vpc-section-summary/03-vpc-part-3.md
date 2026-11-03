# 30. PrivateLink / VPC Endpoint Services

This is slightly different from normal VPC endpoints.

Suppose **Company A** provides a service:

```
Provider VPC
    │
    │
Network Load Balancer
    │
PrivateLink
    │
Consumer VPC
```

The consumer can access the provider's service privately.

### Important components

Provider side:

> **Network Load Balancer**
> 

Consumer side:

> **Interface VPC Endpoint**
> 

### Big advantage

You don't need:

- VPC Peering
- Public Internet
- NAT Gateway
- Complex routing between the VPCs

### Memory

> **PrivateLink = privately consume someone else's service**
> 

---

# 31. VPC Peering vs PrivateLink

|  | VPC Peering | PrivateLink |
| --- | --- | --- |
| Purpose | VPC-to-VPC connectivity | Service-to-consumer connectivity |
| Access | Broad network connectivity | Specific service |
| Provider NLB required | ❌ | ✅ |
| Consumer Interface Endpoint | ❌ | ✅ |
| Transitive | ❌ | Service-based |
| CIDR overlap | Must not overlap | Can be useful when overlapping CIDRs make peering unsuitable |
| Great for | VPC networking | SaaS/service sharing |

### Memory

> **Peering = connect networks**
> 

> **PrivateLink = expose a service**
> 

---

# 32. Transit Gateway

VPC Peering becomes difficult at scale.

Imagine:

```
VPC A ── VPC B
 │ ╲      ╱ │
 │  ╲    ╱  │
VPC C ── VPC D
```

Lots of connections.

Instead:

```
            VPC A
              │
            VPC B
              │
        ┌─────▼─────┐
        │  Transit  │
        │  Gateway  │
        └─────┬─────┘
          │    │    │
        VPC C VPN   DX
```

Transit Gateway provides a **central network hub**.

### Supports connectivity involving:

- VPCs
- Site-to-Site VPN
- Direct Connect Gateway
- Other Transit Gateways through peering

### Important

> **Transit Gateway = hub for large-scale network connectivity**
> 

---

# 33. VPC Peering vs Transit Gateway

|  | VPC Peering | Transit Gateway |
| --- | --- | --- |
| Architecture | Point-to-point | Central hub |
| Scale | Good for smaller networks | Large-scale |
| Transitive routing | ❌ | ✅ |
| VPCs | Connect directly | Connect through TGW |
| VPN | Not the primary purpose | ✅ |
| DX | Not the primary purpose | ✅ via DX Gateway |
| Management | Many individual connections | Centralized |

### Memory

> **Few VPCs → Peering**
> 

> **Many VPCs + VPN/DX → Transit Gateway**
> 

---

# 34. Traffic Mirroring

Traffic Mirroring allows you to **copy network traffic from an ENI** for inspection.

```
EC2
 │
 │ Original traffic
 ▼
Application

     └──── copy ────►
              │
              ▼
        Network Load Balancer
              │
              ▼
       Security Appliances
```

### Used for

- Threat detection
- Security inspection
- Network troubleshooting
- Traffic analysis

### Important

Source:

> **ENI**
> 

Target:

> **ENI or Network Load Balancer**
> 

---

# 35. IPv6

IPv4:

```
32-bit
~4.3 billion addresses
```

IPv6:

```
128-bit
Huge address space
```

Example:

```
2001:db8:1234:5678::1
```

### AWS VPC

IPv4 cannot be removed from a VPC.

You can enable:

> **Dual-stack**
> 

Meaning:

```
IPv4 + IPv6
```

---

# 36.1 IPv6 Internet Access

| Requirement | Solution |
| --- | --- |
| Public IPv4 | IGW |
| Public IPv6 | IGW |
| Private IPv4 outbound Internet | NAT Gateway |
| Private IPv6 outbound Internet | Egress-only IGW |

### Remember

```
IPv4 private → NAT Gateway

IPv6 private → Egress-only IGW
```

---

# 36.2 Dual-Stack VPC

### Definition

A dual-stack architecture supports **both IPv4 and IPv6**.

```
                 VPC
                  │
          ┌───────┴───────┐
          │               │
         IPv4            IPv6
          │               │
          └───────┬───────┘
                  ▼
                EC2
```

A resource can have both IPv4 and IPv6 connectivity when configured appropriately.

### Example

```
EC2
 ├── Private IPv4: 10.0.1.10
 └── IPv6: 2001:db8::10
```

### Dual-stack vs IPv6-only

| Dual-stack | IPv6-only |
| --- | --- |
| IPv4 + IPv6 | IPv6 |
| Better compatibility | Avoids IPv4 consumption |
| Useful during migration | Useful for new IPv6-native workloads |
| More flexibility | Requires IPv6-compatible dependencies |

### Exam trigger

> **"Both IPv4 and IPv6" → Dual-stack.**
>
