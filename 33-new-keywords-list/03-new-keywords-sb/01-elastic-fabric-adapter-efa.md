# Elastic Fabric Adapter (EFA)

## Elastic Fabric Adapter (EFA)

**EFA** is a **high-performance network interface for EC2** designed for applications that need **very fast, low-latency communication between EC2 instances**.

Think:

> **EFA = Super-fast network connection for HPC and distributed workloads.**
> 

### Example

A scientific simulation runs across many EC2 instances:

```
EC2 ─── EFA ─── EC2
 │               │
 └──── EFA ──────┘
```

The instances need to exchange huge amounts of data very quickly. EFA provides **low-latency, high-throughput networking** for this communication.

### Common use cases

- **High Performance Computing (HPC)**
- Machine learning / distributed training
- Scientific simulations
- Tightly coupled distributed applications

### 🧠 SAA-C03 shortcut

> **HPC + EC2 + very low-latency/high-performance network communication → EFA**
> 

**Important:** EFA is **not** storage and not a load balancer. It is a specialized **network interface** for high-performance workloads.
