# Amazon VPC IP Address Manager

Amazon VPC IPAM helps organizations **plan, allocate, monitor, and manage IP address space**.

For example:

```
10.0.0.0/16
     ↓
 ┌───┴────┐
 ↓        ↓
AZ-A     AZ-B
10.0.1   10.0.2
```

IPAM helps manage that address space.

### Why it seems tempting

The architecture involves:

- VPC
- Multiple AZs
- EC2 instances
- Networking

But IPAM has nothing to do with distributing application requests.

```
IPAM
 ↓
"Which IP ranges should I use?"
```
