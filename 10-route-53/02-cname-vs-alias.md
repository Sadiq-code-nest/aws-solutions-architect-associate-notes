# Route 53: CNAME vs Alias Record (SAA-C03 Exam Notes)

## 1. What is a Root Domain (Zone Apex)?

A **root domain** (also called **zone apex**) is the main domain name itself.

Example:

```
example.com        ← Root Domain / Zone Apex

www.example.com    ← Subdomain
api.example.com    ← Subdomain
blog.example.com   ← Subdomain
```

Visualize it like:

```
example.com
├── www.example.com
├── api.example.com
├── blog.example.com
└── mail.example.com
```

The top level (`example.com`) is called the **Zone Apex**.

---

## 2. Why is the Root Domain Special?

Every DNS zone must contain mandatory records:

```
example.com
├── SOA
└── NS
```

### SOA (Start of Authority)

Contains zone configuration information.

### NS (Name Server)

Tells the internet which DNS servers are authoritative for the domain.

Because these records already exist at the root domain, DNS standards impose special rules on it.

---

# 3. What is a CNAME Record?

**CNAME = Canonical Name**

A CNAME maps one hostname to another hostname.

Example:

```
www.example.com
        ↓
my-alb-123.us-east-1.elb.amazonaws.com
```

Route 53 Record:

```
www.example.com
CNAME
my-alb-123.us-east-1.elb.amazonaws.com
```

---

## DNS Resolution Flow

User visits:

```
www.example.com
```

DNS Process:

```
www.example.com
       ↓
CNAME says:
       ↓
my-alb-123.us-east-1.elb.amazonaws.com
       ↓
DNS lookup again
       ↓
ALB IP Address
```

The DNS resolver performs **another lookup** to get the final IP.

---

## AWS Example

Suppose your ALB DNS name is:

```
my-shop-alb-123.us-east-1.elb.amazonaws.com
```

You can create:

```
www.example.com
CNAME
my-shop-alb-123.us-east-1.elb.amazonaws.com
```

Result:

```
www.example.com
       ↓
ALB
```

Works perfectly.

---

# 4. Why Can't CNAME Be Used at the Root Domain?

Suppose you try:

```
example.com
CNAME
my-alb.amazonaws.com
```

DNS standards say:

> If a record is a CNAME, no other records may exist with that name.
> 

But root domains already contain:

```
example.com
├── SOA
└── NS
```

Now we'd have:

```
example.com
├── SOA
├── NS
└── CNAME
```

This violates DNS rules.

Therefore:

```
example.com CNAME my-alb.amazonaws.com
```

❌ Invalid

---

# 5. AWS Solution: Alias Record

AWS created **Alias Records** in Route 53.

Alias behaves like:

```
A Record + AWS Intelligence
```

Instead of storing a fixed IP, Route 53 automatically tracks AWS resources.

---

## Example

You have an ALB:

```
my-shop-alb-123.us-east-1.elb.amazonaws.com
```

Create:

```
example.com
ALIAS
Application Load Balancer
```

Now:

```
example.com
       ↓
Route 53
       ↓
ALB
       ↓
Current ALB IPs
```

---

# 6. Why Alias is Perfect for ALB

Remember:

ALB IPs change frequently.

Example:

Today:

```
52.10.1.1
52.10.1.2
```

Tomorrow:

```
54.22.3.4
54.22.3.5
```

AWS manages these changes automatically.

With Alias:

```
example.com
      ↓
ALB
```

Route 53 always knows the current ALB IPs.

No manual updates needed.

---

# 7. CNAME vs Alias DNS Flow

## CNAME Flow

```
User
 ↓
www.example.com
 ↓
CNAME
 ↓
my-alb.amazonaws.com
 ↓
Another DNS lookup
 ↓
ALB IP
```

Two steps.

---

## Alias Flow

```
User
 ↓
example.com
 ↓
Route 53
 ↓
ALB
 ↓
Returns ALB IP directly
```

One logical DNS resolution.

---

# 8. Real AWS Architecture

```
Internet Users
        │
        ▼
example.com
(Route 53 Alias)
        │
        ▼
Application Load Balancer
(ALB)
        │
        ▼
EC2 Instances
```

Root domain uses Alias.

---

For a subdomain:

```
Internet Users
        │
        ▼
www.example.com
(CNAME)
        │
        ▼
ALB DNS Name
        │
        ▼
ALB
        │
        ▼
EC2 Instances
```

---

# 9. AWS Resources Supported by Alias

Common exam examples:

- Elastic Load Balancing (ALB/NLB)
- Amazon CloudFront
- Amazon S3 Static Website
- AWS Global Accelerator
- Another Route 53 record

---

# 10. SAA-C03 Exam Rules

### Scenario 1

Question:

> Point example.com to an ALB.
> 

Answer:

✅ Alias Record

Reason:

```
example.com
```

is the root domain (zone apex).

CNAME cannot be used.

---

### Scenario 2

Question:

> Point [www.example.com](http://www.example.com/) to an ALB.
> 

Answer:

✅ CNAME or Alias

AWS generally prefers Alias, but CNAME is valid.

---

### Scenario 3

Question:

> Point example.com to CloudFront.
> 

Answer:

✅ Alias Record

---

### Scenario 4

Question:

> Point example.com to an S3 Static Website.
> 

Answer:

✅ Alias Record

---

# Ultimate Memory Trick

### CNAME

```
Nickname → Another DNS Name
```

Example:

```
www.example.com
      ↓
my-alb.amazonaws.com
```

Works only when not at the root domain.

---

### Alias

```
AWS Smart DNS Shortcut
```

Example:

```
example.com
      ↓
ALB
```

Works at the root domain and automatically tracks AWS resources.

---

# Exam One-Liner

**CNAME = Maps one hostname to another hostname, but cannot be used at the Zone Apex (root domain).**

**Alias = Route 53-specific record that can point the Zone Apex to AWS resources such as ALB, CloudFront, and S3 while automatically handling changing IP addresses.** 🚀
