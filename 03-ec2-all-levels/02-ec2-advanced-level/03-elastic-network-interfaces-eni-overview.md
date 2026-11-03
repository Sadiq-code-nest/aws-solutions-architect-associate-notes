# Elastic Network Interface (ENI) — AWS SAA-C03

### Simple definition

> **ENI is a virtual network card for an EC2 instance.**
> 

Just like a physical computer has a network card (NIC), an EC2 instance uses an **ENI** to connect to a VPC.

```
VPC
 │
 └── EC2
      │
      └── ENI
           ├── Private IP
           ├── Public IP / Elastic IP
           ├── Security Groups
           └── MAC Address
```

### What can an ENI have?

An ENI can have:

- **Primary private IPv4**
- **Secondary private IPv4 addresses**
- **One public IPv4**
- **Elastic IP addresses**
- **Security Groups**
- **MAC address**

### ⭐ Important: ENI is AZ-specific

An ENI belongs to a **specific Availability Zone**.

```
us-east-1a
   │
   └── ENI
        │
        └── EC2
```

You **cannot move that ENI directly to an EC2 instance in another AZ**.

### Why is a separate ENI useful?

You can create an ENI separately and attach/detach it from EC2 instances.

Example:

```
        ENI
     10.0.1.50
        │
        ▼
   EC2 Instance A
```

If Instance A fails:

```
        ENI
     10.0.1.50
        │
        ▼
   EC2 Instance B
```

The ENI moves with its network identity, so the application can continue using the same private IP/security-group configuration.

### 🧠 Exam memory

> **ENI = Virtual Network Card**
> 

> **ENI contains IP + Security Groups + MAC**
> 

> **ENI is tied to an AZ**
> 

> **ENI can be detached and attached to another EC2 in the same AZ**
>
