# Network Load Balancer (NLB)

## Network Load Balancer (NLB) — Target Groups

An **NLB target group** defines **where the NLB sends incoming traffic**.

| Target Type | Supported? | Important Point | Example |
| --- | --- | --- | --- |
| **EC2 Instances** | ✅ | NLB sends traffic directly to EC2 instances | TCP traffic → EC2 |
| **IP Addresses** | ✅ | IPs **must be private IP addresses** | On-premises/private workloads |
| **Application Load Balancer** | ✅ | NLB can forward traffic to an ALB | NLB → ALB → EC2 |
| Lambda | ❌ | Not supported as an NLB target | — |

### 

### One-line translation

> **Need a static public IP for a load balancer → Network Load Balancer + Elastic IP.**
> 

Think:

```
Static IP requirement
        ↓
Elastic IP
        ↓
Network Load Balancer
```

NLB Target Group Health Checks

NLB supports health checks using:

- **TCP**
- **HTTP**
- **HTTPS**

Example:

```
Client
   ↓
Network Load Balancer
   ↓
Target Group
   ↓
EC2 Instance
```

The NLB continuously checks whether the target is healthy. If a target fails the health check, the NLB stops sending traffic to it.

### ⭐ SAA-C03 Exam Memory

> **NLB → EC2, Private IP, or ALB**
> 

And remember:

> **NLB = Layer 4 → TCP / TLS / UDP**
> 

**Common scenario:** You need very high-performance TCP traffic and want to route it to EC2 instances → **NLB + EC2 target group**.
