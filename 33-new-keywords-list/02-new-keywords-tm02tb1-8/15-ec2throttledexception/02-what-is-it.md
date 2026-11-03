# What is it

## EC2ThrottledException — Very Simple

**`EC2ThrottledException`** means:

> **Lambda is trying to create/use networking resources in your VPC, but AWS is throttling the request because a VPC/ENI/IP limit has been reached.**
> 

When a Lambda function is connected to a VPC, AWS manages **Elastic Network Interfaces (ENIs)** and IP addresses for the Lambda networking.

### Simple picture

```
Lambda
  ↓
VPC
  ↓
Subnet
  ↓
ENI + Private IP
```

If your Lambda suddenly has **massive traffic**:

```
Millions of Lambda invocations
          ↓
Need more networking capacity
          ↓
ENIs / IP addresses
          ↓
❌ Quota or IP capacity reached
          ↓
EC2ThrottledException
```

---

## Your question: why these TWO?

### ✅ 1. Lambda exceeds VPC quota for ENIs or available IP addresses

Exactly matches the problem.

```
Lambda
 ↓
Need ENI/IP
 ↓
VPC limit reached ❌
 ↓
EC2ThrottledException
```

### ✅ 2. Lambda is in a subnet with limited IP capacity

Your VPC is:

```
10.31.0.0/27
```

A `/27` subnet/network is **small**, so it provides a limited number of IP addresses.

If massive Lambda concurrency needs many IP addresses:

```
Available IPs
████████████████  ← exhausted
                  ↓
Lambda needs more ❌
                  ↓
EC2ThrottledException
```

---

## Why the others are wrong

### ❌ No NAT Gateway

NAT Gateway is needed when private VPC resources need to **initiate internet-bound connections**.

It isn't required simply because Lambda is connected to VPC/EFS.

```
Lambda → EFS
```

doesn't require NAT.

### ❌ IAM permissions

IAM controls **authorization**:

```
"Is Lambda allowed to do X?"
```

It doesn't cause this particular ENI/IP capacity throttling problem.

### ❌ Security Group outbound rules

Security groups control **network traffic**.

A blocked outbound connection would cause a **connection/network access problem**, not typically `EC2ThrottledException`.

---

# 🧠 Easy Memory Trick

> **EC2ThrottledException in VPC Lambda → Think ENI/IP capacity.**
> 

Especially when you see:

**High Lambda concurrency + small subnet + VPC + EC2ThrottledException**

👉 **Check ENI quotas and available subnet IP addresses.**
