# AWS WAF — Web Application Firewall

## 1. Meaning

**AWS WAF is a web application firewall that protects applications from unwanted or malicious HTTP/HTTPS requests.**

Think:

> **WAF = Inspect web requests → Allow good traffic → Block bad traffic.**
> 

---

# 2. What Problem Does WAF Solve?

Imagine you have an online shopping website:

```
Users
  │
  │ HTTP / HTTPS requests
  ▼
AWS WAF
  │
  ├── ✅ Good request → Allow
  │
  └── ❌ Malicious request → Block
  │
  ▼
Application Load Balancer
  │
  ▼
EC2
```

WAF examines incoming web requests before they reach your application.

---

# 3. Real Example ⭐⭐⭐⭐⭐

Suppose your website is:

```
https://shop.example.com
```

Thousands of people visit it.

Someone sends a suspicious request containing a common **SQL injection** pattern.

WAF checks the request:

```
User
  │
  │ Malicious HTTP request
  ▼
AWS WAF
  │
  │ Detects suspicious pattern
  ▼
❌ BLOCK
```

The request never reaches your application.

Normal customer:

```
Customer
  │
  │ Normal request
  ▼
AWS WAF
  │
  │ Looks safe
  ▼
✅ ALLOW
  │
  ▼
ALB
  │
  ▼
Application
```

That's the basic idea of WAF.

---

# 4. Web ACL ⭐⭐⭐⭐⭐

In AWS WAF, you create a:

> **Web ACL (Web Access Control List)**
> 

A Web ACL contains the rules that decide whether requests should be allowed or blocked.

Think:

```
Web ACL
   │
   ├── Rule 1 → Block bad IPs
   ├── Rule 2 → Block SQL injection
   ├── Rule 3 → Block XSS
   ├── Rule 4 → Block certain countries
   └── Rule 5 → Rate limit requests
```

Then attach the Web ACL to your protected AWS resource.

---

# 5. What Can WAF Inspect?

WAF can inspect parts of HTTP/HTTPS requests, such as:

- IP addresses
- HTTP headers
- HTTP body
- URI strings
- Request size
- Geographic location

For example:

```
HTTP Request

IP Address
     ↓
Headers
     ↓
URI
     ↓
Body
```

WAF can use rules against these values.

---

# 6. IP Set ⭐⭐⭐⭐

An **IP Set** is a collection of IP addresses that you can use in WAF rules.

Example:

```
IP Set: BadIPs

1.2.3.4
5.6.7.8
9.10.11.12
```

Then create a rule:

```
IF source IP is in BadIPs
        ↓
      BLOCK
```

Your application doesn't need to process requests from those addresses.

---

# 7. IP Set Limit

A WAF IP Set supports up to:

> **10,000 IP addresses**
> 

If you need more than that, you can use multiple rules/IP sets.

For the exam, remember:

> **IP Set = list of IP addresses used by WAF rules.**
> 

---

# 8. SQL Injection Protection ⭐⭐⭐⭐⭐

WAF can protect against common web attacks, such as:

> **SQL Injection**
> 

Example concept:

```
Attacker
   │
   │ Malicious SQL-like input
   ▼
WAF
   │
   ▼
❌ Block
```

This prevents suspicious requests from reaching your application.

---

# 9. Cross-Site Scripting (XSS)

WAF can also help protect against:

> **Cross-Site Scripting (XSS)**
> 

Conceptually:

```
Attacker
   │
   │ Malicious script in request
   ▼
AWS WAF
   │
   ▼
❌ Block
```

So remember:

> **WAF → SQL Injection + XSS**
> 

---

# 10. Geo-Match ⭐⭐⭐⭐

You can create rules based on the **country from which the request originates**.

Example:

Your business only operates in:

```
Bangladesh
India
Singapore
```

You could create a rule to block requests from countries you don't want to serve.

```
Request
   │
   ▼
WAF
   │
   ├── Allowed Country → ✅ Allow
   │
   └── Blocked Country → ❌ Block
```

This is called:

> **Geo-match**
> 

---

# 11. Size Constraint

WAF can also check the size of request components.

For example:

```
HTTP Request Body

Normal size
   ↓
✅ Allow

Unusually large
   ↓
❌ Block
```

This can help protect applications from requests that violate expected size limits.

---

# 12. Rate-Based Rules ⭐⭐⭐⭐⭐

A very useful WAF feature is:

> **Rate-based rules**
> 

Imagine an attacker sends:

```
10 requests
100 requests
1,000 requests
10,000 requests
```

from the same IP in a short period.

You can configure WAF to detect excessive request rates.

Conceptually:

```
IP 1.2.3.4

Request 1
Request 2
Request 3
...
Request 10,000
       │
       ▼
Rate limit exceeded
       │
       ▼
❌ Block / Rate-limit
```

---

# 13. WAF Rate-Based Rules and DDoS ⭐⭐⭐⭐⭐

Rate-based rules can help mitigate certain **application-layer traffic floods**.

For example:

```
Attacker
  │
  ├── Request
  ├── Request
  ├── Request
  ├── Request
  └── Request
        ↓
      WAF
        ↓
Rate exceeds limit
        ↓
      BLOCK
```

### Important distinction

Don't think:

> **WAF = complete DDoS protection**
> 

AWS has other services specifically designed for DDoS protection, such as **AWS Shield**.

For SAA:

> **WAF → Layer 7 web request protection**
> 

> **Shield → DDoS protection**
> 

---

# 14. WAF Is Layer 7. ⭐⭐⭐⭐⭐

WAF works with:

> **HTTP/HTTPS → Application Layer (Layer 7)**
> 

It understands things like:

```
URL
Headers
HTTP Body
Cookies
IP
Request patterns
```

That's why it can detect things such as SQL injection and XSS.

---

# 15. WAF + Load Balancer

A very common architecture is:

```
                 Internet
                    │
                    ▼
                  AWS WAF
                    │
                 Web ACL
                    │
                    ▼
          Application Load Balancer
                    │
             ┌──────┴──────┐
             ▼             ▼
           EC2           EC2
```

WAF protects the web application before requests reach the ALB targets.

---

# 16. WAF Does NOT Support NLB ⭐⭐⭐⭐⭐

This is an important exam trap.

AWS WAF does **not** directly attach to:

> ❌ **Network Load Balancer (NLB)**
> 

Why?

Because NLB works primarily at **Layer 4**, while WAF operates at the web/application layer.

```
ALB
Layer 7
   ↓
✅ WAF supported

NLB
Layer 4
   ↓
❌ WAF not directly supported
```

---

# 17. WAF + ALB Region Rule ⭐⭐⭐⭐⭐

When WAF protects an **Application Load Balancer**, the Web ACL must be in the **same AWS Region as the ALB**.

Example:

```
ALB
us-east-1
   │
   ▼
Web ACL
us-east-1
```

You cannot have:

```
ALB
us-east-1

Web ACL
eu-west-1

❌
```

Remember:

> **WAF + ALB → Same Region**
> 

---

# 18. WAF + CloudFront ⭐⭐⭐⭐⭐

CloudFront is different.

A Web ACL associated with **CloudFront** is managed as a **global CloudFront resource** rather than as a Regional Web ACL.

For SAA-C03, the important distinction is:

```
WAF + ALB
→ Regional Web ACL
→ Same Region as ALB

WAF + CloudFront
→ CloudFront/global scope
```

---

# 19. Rule Groups

A **rule group** is a reusable collection of WAF rules.

Imagine you have several applications:

```
Application A
Application B
Application C
```

All need the same security rules:

```
SQL Injection
XSS
Bad IPs
Known malicious patterns
```

Instead of creating everything repeatedly:

```
Rule Group
   │
   ├── SQL Injection Rule
   ├── XSS Rule
   └── IP Blocking Rule
```

You can reuse that rule group in different Web ACLs.

Think:

> **Rule Group = reusable bundle of WAF rules.**
> 

---

# 20. Fixed IP + WAF + ALB ⭐⭐⭐⭐⭐

Here's an interesting architecture problem.

Suppose you want:

1. WAF protection
2. Application Load Balancer
3. Fixed/static IP address for users

The problem is:

> **ALB doesn't provide fixed/static IP addresses.**
> 

And:

> **WAF doesn't attach directly to NLB.**
> 

So you can use:

```
Users
   │
   ▼
AWS Global Accelerator
   │
   │ Static Anycast IPs
   ▼
Application Load Balancer
   │
   │ Web ACL
   ▼
AWS WAF
   │
   ▼
EC2
```

Conceptually:

```
                 Fixed IP
                    │
                    ▼
             Global Accelerator
                    │
                    ▼
                   ALB
                    │
                   WAF
                    │
                    ▼
                  EC2
```

### Why?

**Global Accelerator:**

> Gives you static IP addresses and global network routing.
> 

**ALB:**

> Provides Layer 7 load balancing.
> 

**WAF:**

> Protects the web application.
> 

---

# 21. Fixed IP Example ⭐⭐⭐⭐⭐

Imagine a company has a banking application.

A partner says:

> "We need a fixed IP address to whitelist your application."
> 

But the application uses an ALB.

ALB IPs can change.

So:

```
❌ Client
     ↓
   ALB
     ↓
"Which IP should we whitelist?"
```

Instead:

```
Client
  │
  ▼
Global Accelerator
  │
  │ Static IP
  ▼
ALB
  │
  ▼
WAF
  │
  ▼
EC2
```

The partner can whitelist the Global Accelerator static IP.

At the same time, the application still benefits from ALB + WAF.

---

# 22. WAF vs Security Group ⭐⭐⭐⭐⭐

Don't confuse them.

| Security Group | AWS WAF |
| --- | --- |
| Network-level firewall | Web/application firewall |
| Controls IP/port/protocol traffic | Inspects HTTP/HTTPS requests |
| Layer 3/4 concepts | Layer 7 |
| Allow/deny network traffic | SQL injection, XSS, geo, rate rules |
| Used with ENIs/resources | Used with supported web resources |

Easy:

> **Security Group → "Can this network traffic reach me?"**
> 

> **WAF → "Is this HTTP request safe?"**
> 

---

# 23. WAF vs Shield ⭐⭐⭐⭐⭐

| AWS WAF | AWS Shield |
| --- | --- |
| Web application firewall | DDoS protection |
| HTTP/HTTPS request inspection | Protects against DDoS attacks |
| SQL injection | Network/application DDoS |
| XSS | DDoS mitigation |
| IP/geo/rate rules | DDoS protection |

Memory:

> **WAF = Web attacks**
> 

> **Shield = DDoS**
> 

They can also be used together.

---

# 24. WAF Rule Decision Flow

```
                    HTTP / HTTPS Request
                              │
                              ▼
                         AWS WAF
                              │
                          Web ACL
                              │
             ┌────────────────┼────────────────┐
             ▼                ▼                ▼
          IP Rule          SQL/XSS          Geo Rule
             │                │                │
             └────────────────┼────────────────┘
                              │
                    ┌─────────┴─────────┐
                    ▼                   ▼
                 Allowed              Blocked
                    │                   │
                    ▼                   ▼
                  ALB                  ❌
                    │
                    ▼
                 EC2 / App
```

---

# Easy Memory Tricks ⭐⭐⭐⭐⭐

### WAF

> **WAF = Protect web applications.**
> 

### Web ACL

> **Web ACL = Collection of rules deciding Allow/Block.**
> 

### IP Set

> **IP Set = List of IP addresses.**
> 

### SQL Injection

> **WAF protects against SQL injection.**
> 

### XSS

> **WAF protects against XSS.**
> 

### Geo-match

> **Block/allow based on country.**
> 

### Rate-based rule

> **Too many requests → limit/block.**
> 

### Rule Group

> **Reusable bundle of WAF rules.**
> 

### ALB

> **WAF works directly with ALB.**
> 

### NLB

> **WAF does NOT directly attach to NLB.**
> 

### ALB Region

> **Web ACL must be in the same Region as ALB.**
> 

### CloudFront

> **WAF Web ACL uses CloudFront/global scope.**
> 

### Fixed IP + WAF + ALB

> **Global Accelerator → ALB + WAF**
> 

---

# AWS SAA-C03 Exam Recognition

**"Protect an ALB-based web application against SQL injection."**

→ 🔥 **AWS WAF**

**"Block requests from certain countries."**

→ ✅ **WAF Geo-match rule**

**"Block known malicious IP addresses."**

→ ✅ **WAF IP Set**

**"Limit requests from an IP that sends too many requests."**

→ 🔥 **WAF rate-based rule**

**"Protect against XSS."**

→ 🔥 **AWS WAF**

**"Attach WAF directly to an NLB."**

→ ❌ **Not supported**

**"WAF Web ACL for ALB."**

→ ⭐ **Web ACL must be in the same Region as ALB**

**"WAF with CloudFront."**

→ ⭐ **Use CloudFront/global Web ACL scope**

**"Need fixed/static IP + ALB + WAF."**

→ ⭐ **Global Accelerator → ALB → WAF**

**"Need DDoS protection."**

→ ⭐ **AWS Shield**

**"Need network-level port/IP access control."**

→ ⭐ **Security Group / Network Firewall depending on architecture**

---

# Final Memory Diagram

```
                         INTERNET
                            │
                            ▼
                     ┌──────────────┐
                     │   AWS WAF    │
                     │   Web ACL    │
                     └──────┬───────┘
                            │
            ┌───────────────┼────────────────┐
            │               │                │
        IP Set          SQL Injection       XSS
            │               │                │
            └───────────────┼────────────────┘
                            │
                      Rate / Geo Rules
                            │
                            ▼
                 Application Load Balancer
                            │
                      ┌─────┴─────┐
                      ▼           ▼
                     EC2         EC2

For fixed IP:

Users
  │
  ▼
Global Accelerator
  │
  │ Static IP
  ▼
ALB
  │
  ▼
WAF
  │
  ▼
EC2
```

## One-Line Summary

**AWS WAF protects web applications by inspecting HTTP/HTTPS requests using Web ACL rules for IP filtering, SQL injection, XSS, geo-matching, size constraints, and rate limiting; it works directly with ALB and CloudFront, but not NLB, and for ALB the Web ACL must be in the same Region.**
