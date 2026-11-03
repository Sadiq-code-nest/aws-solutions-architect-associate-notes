# NLB problem

NLB has one static IP per AZ and supports assigning Elastic IP (for whitelisting specific IPs).

# 🧠 First, understand the Problem

Normally, when you access an application, you use a DNS name:

```
my-app-alb.amazonaws.com
```

Behind that DNS, AWS may change IP addresses.

For most websites, that's fine.

But some companies don't trust DNS names.

They say:

> "Give me the exact IP address that your application will use."
> 

Why?

Because they want to **whitelist** that IP in their firewall.

---

# Real-Life Example

Imagine your company has an API.

Partner company:

```
ABC Bank
```

needs to call your API.

Their security team says:

> We only allow outbound traffic to approved IP addresses.
> 

They ask:

```
Which IP should we allow?
```

---

# Problem with ALB

ALB provides:

```
myapp-alb.us-east-1.elb.amazonaws.com
```

But AWS can change the underlying IPs.

You don't get a fixed IP.

So the bank cannot safely whitelist it.

---

# Solution: NLB

NLB can provide:

```
52.10.10.10
```

Static IP.

This IP remains fixed.

Now the bank can whitelist:

```
Allow:
52.10.10.10
```

Done.

---

# What Does "One Static IP Per AZ" Mean?

Suppose your NLB is deployed in:

- AZ-A
- AZ-B

Architecture:

```
          NLB

    AZ-A        AZ-B
      |            |
Static IP      Static IP
54.1.1.1      54.1.1.2
```

AWS creates one network interface in each AZ.

Each interface gets its own IP.

So your NLB may have:

```
54.1.1.1
54.1.1.2
```

Both are static.

---

# Why Per AZ?

Remember:

AWS wants High Availability.

If:

```
AZ-A fails
```

Traffic can still enter through:

```
54.1.1.2
```

in AZ-B.

---

# Elastic IP (EIP) Support

This is where NLB becomes even more powerful.

---

## What is an Elastic IP?

An Elastic IP is:

> A permanent public IPv4 address owned by your AWS account.
> 

Example:

```
3.22.44.88
```

You keep it until you release it.

---

# With NLB

You can manually attach Elastic IPs.

Example:

```
AZ-A → 3.22.44.88
AZ-B → 18.99.10.11
```

Now AWS will always use these exact IPs.

---

# Technical Architecture

```
Internet
     |
     |
 NLB
 /   \
EIP   EIP
 |      |
AZ-A  AZ-B
 |      |
EC2    EC2
```

---

# Real Production Scenario #1

### Banking Integration

Your company exposes:

```
https://api.company.com
```

Bank says:

```
Send us the IPs.
```

You deploy:

```
NLB + Elastic IP
```

Give them:

```
3.22.44.88
18.99.10.11
```

They whitelist those IPs.

Connection works.

---

# Real Production Scenario #2

### Corporate Firewall

A customer firewall allows only:

```
Destination:
3.22.44.88
```

If you use ALB:

❌ IP may change

If you use NLB:

✅ Fixed IP

---

# Real Production Scenario #3

### Legacy Systems

Some old enterprise software supports only:

```
IP:PORT
```

Not DNS.

Example:

```
52.10.10.10:443
```

NLB is ideal.

---

# Exam Question Style

Question:

> A company exposes a public application to third-party partners. Partners require fixed IP addresses to whitelist traffic. Which load balancer should be used?
> 

Options:

- ALB
- NLB
- Gateway LB
- Route 53

### Answer:

✅ NLB

Keyword:

> fixed IP / static IP / IP whitelisting
> 

---

# Common Exam Trap

Question says:

```
HTTP application
```

and

```
Need static IP
```

Many students pick ALB because of HTTP.

Wrong.

The important requirement is:

```
Static IP
```

Answer:

✅ NLB

---

# Easy Memory Trick

Think:

### ALB

```
I provide a smart DNS endpoint.
```

---

### NLB

```
I provide fixed network addresses.
```

---

# Interview-Level Understanding

When AWS says:

> "NLB has one static IP per Availability Zone"
> 

it means:

- AWS creates a network endpoint in each AZ.
- Each endpoint receives a fixed IP address.
- You may optionally replace those AWS-assigned IPs with your own Elastic IPs.
- This enables firewall whitelisting, partner integrations, legacy systems, and highly available network architectures.

### Exam Shortcut

If you see:

- Static IP
- Fixed IP
- Elastic IP
- IP Whitelisting
- Partner Firewall
- Legacy TCP Application

👉 Immediately think **Network Load Balancer (NLB)**.
