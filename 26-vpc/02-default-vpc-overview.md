# Default VPC

## 1. Meaning

A **Default VPC is a ready-made VPC that AWS automatically creates for you in a new AWS account in each AWS Region.**

Think:

> **Default VPC = AWS's "ready-to-use network" for launching EC2 quickly.**
> 

---

## 2. Definition

When you create an AWS account, AWS provides a **default VPC** in each Region.

It is already configured with the basic networking components needed to launch EC2 instances and access the internet.

Typically, the Default VPC includes:

```
Default VPC
│
├── Subnets
├── Route Tables
├── Internet Gateway
└── Internet connectivity
```

If you launch an EC2 instance **without choosing a specific subnet**, AWS can place it in the **default VPC**.

---

# 3. Real Example ⭐⭐⭐⭐⭐

Imagine you just created an AWS account and immediately want to test an EC2 instance.

You go to:

```
EC2
 ↓
Launch Instance
 ↓
Don't manually select a VPC/subnet
 ↓
Launch
```

Where does AWS put it?

👉 **Default VPC**

You don't have to manually create:

```
VPC
Internet Gateway
Route Table
Subnet
```

because AWS already created the basic networking environment.

The EC2 instance can have a **public IPv4 address**, allowing internet connectivity when the relevant network configuration permits it.

Think:

> **"I just opened AWS and want to launch my first EC2." → Default VPC makes this easy.**
> 

---

# 4. Internet Connectivity ⭐⭐⭐⭐⭐

A Default VPC is designed to provide internet connectivity.

The basic flow looks like:

```
                 Internet
                    ▲
                    │
             Internet Gateway
                    ▲
                    │
             Route Table
                    ▲
                    │
              Public Subnet
                    ▲
                    │
                   EC2
```

So an EC2 instance launched in the Default VPC can communicate with the internet when its networking/security configuration allows it.

---

# 5. Public IPv4 Address ⭐⭐⭐⭐⭐

By default, EC2 instances launched into a **default subnet of the Default VPC** are configured to receive a **public IPv4 address**.

Example:

```
EC2
Private IP:
10.0.1.25

Public IPv4:
54.x.x.x
```

This allows the instance to communicate with the internet through the VPC's Internet Gateway.

### 🧠 Exam clue:

> **"Launch EC2 in the default VPC and it automatically receives a public IPv4 address."**
> 

Think:

✅ **Default VPC / default subnet behavior**

---

# 6. Public + Private DNS Names

An EC2 instance can have DNS hostnames associated with its IP addresses.

For example, you may see something conceptually like:

```
Private DNS
ip-10-0-1-25.region.compute.internal
```

and a public DNS name associated with its public IPv4 address.

The exact DNS naming format can vary, so for the exam, focus on the concept:

> **EC2 in a Default VPC can have both public and private DNS names when it has the corresponding public/private addressing.**
> 

---

# 7. Default VPC vs Custom VPC ⭐⭐⭐⭐⭐

### Default VPC

AWS creates the basic environment for you.

```
AWS
 ↓
Default VPC
 ↓
Ready to use
 ↓
Launch EC2
```

### Custom VPC

You design the network yourself.

```
You
 ↓
Create VPC
 ↓
Choose CIDR
 ↓
Create subnets
 ↓
Configure route tables
 ↓
Internet/NAT gateways
 ↓
Launch resources
```

### 🧠 Simple memory:

> **Default VPC = AWS sets up the basic network**
> 

> **Custom VPC = You design the network**
> 

---

# 8. Important Exam Trap ⚠️

Don't assume:

> **"Every EC2 instance always gets a public IP."**
> 

❌ Wrong.

The statement is specifically about the **default VPC/default subnet behavior**.

In a custom VPC, you control whether a subnet automatically assigns public IPv4 addresses.

For example:

```
Custom VPC
│
├── Public Subnet
│     └── EC2 → Public IP possible
│
└── Private Subnet
      └── EC2 → No public IP
```

---

# 🧠 SAA-C03 Memory Trick

Remember:

> **Default VPC = "AWS gives me a network so I can start immediately."**
> 

Key points:

```
Default VPC
│
├── AWS creates it
├── Ready to use
├── Internet connectivity
├── Default subnets
├── EC2 can receive public IPv4
└── EC2 can have public/private DNS names
```

### ⭐ Exam keywords

**"New AWS account"** → Default VPC

**"No subnet specified when launching EC2"** → Default VPC

**"Ready-to-use VPC"** → Default VPC

**"EC2 automatically gets public IPv4 in default subnet"** → Default VPC behavior

**"Want full control over network design"** → Create a **custom VPC**
