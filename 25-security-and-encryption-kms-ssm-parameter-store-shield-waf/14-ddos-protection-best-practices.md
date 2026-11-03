# AWS Best Practices for DDoS Resiliency

The easiest way to understand this topic is:

> **Don't let attackers reach your actual application directly. Put AWS protection layers in front of it, filter bad traffic, absorb sudden traffic increases, and keep your real resources hidden.**
> 

Think of the architecture like this:

```
                    Internet
                       │
             ┌─────────┴─────────┐
             │                   │
        Normal Users          Attackers
             │                   │
             └─────────┬─────────┘
                       ▼
                 CloudFront
                 Global Accelerator
                       │
                       ▼
                    AWS WAF
               Filter bad requests
                       │
                       ▼
                      ALB
                       │
                Auto Scaling
                 EC2 Instances
```

And **Route 53** handles the DNS side.

---

# 1. Edge Location Mitigation ⭐⭐⭐⭐⭐

AWS recommends using services at the **AWS edge** to help absorb and mitigate attacks before traffic reaches your application.

The three important services here are:

```
CloudFront
Global Accelerator
Route 53
```

Think:

> **Move the protection closer to the user/attacker.**
> 

Instead of:

```
Internet
   │
   ▼
Your EC2
```

Use:

```
Internet
   │
   ▼
AWS Edge
   │
   ▼
Your Application
```

This reduces the amount of unwanted traffic reaching your backend.

---

# 2. CloudFront ⭐⭐⭐⭐⭐

**Amazon CloudFront** is a CDN that delivers content from AWS edge locations.

It can also help protect your application from common DDoS attacks, such as:

- SYN floods
- UDP reflection attacks
- Other common network-level attacks

### Real Example

Suppose you have a website:

```
www.example.com
       │
       ▼
   CloudFront
       │
       ▼
      ALB
       │
       ▼
      EC2
```

An attacker sends huge amounts of traffic.

Instead of exposing your EC2 directly, CloudFront is sitting at the edge.

CloudFront can also cache content:

```
User → CloudFront Edge → Cached content
```

The request may never need to reach your EC2.

### 🧠 Remember:

> **CloudFront = CDN + edge protection + caching**
> 

---

# 3. Global Accelerator ⭐⭐⭐⭐⭐

**AWS Global Accelerator** gives users access to your application through AWS's global edge network.

It can work with **AWS Shield** for DDoS protection.

### Real Example

Suppose your application is running in:

```
us-east-1
     │
     ▼
    ALB
     │
     ▼
    EC2
```

Users around the world connect through Global Accelerator:

```
Bangladesh ──┐
USA ─────────┤
Japan ───────┼──► Global Accelerator
UK ──────────┘          │
                        ▼
                       ALB
                        │
                       EC2
```

Global Accelerator uses AWS's network/edge infrastructure to get traffic toward your application.

### When is it useful instead of CloudFront?

If your backend isn't suitable for CloudFront—for example, applications requiring more general TCP/UDP networking—Global Accelerator can be a better fit.

### 🧠 Remember:

> **Global Accelerator = Global edge access + static anycast IPs + Shield integration**
> 

---

# 4. Route 53 ⭐⭐⭐⭐⭐

Route 53 is AWS's DNS service.

It provides:

> **Domain Name Resolution at the edge**
> 

Example:

```
User
  │
  │ www.example.com
  ▼
Route 53
  │
  ▼
Finds where the application is
  │
  ▼
CloudFront / ALB / other endpoint
```

Route 53 also has DDoS protection mechanisms.

### 🧠 Remember:

> **Route 53 = DNS + edge-level resilience**
> 

---

# 5. Protect EC2 Behind AWS Services ⭐⭐⭐⭐⭐

A major best practice is:

> **Don't expose your EC2 instances directly to the Internet if you don't need to.**
> 

Bad architecture:

```
Internet
    │
    ▼
Public EC2
```

Better:

```
Internet
    │
    ▼
CloudFront / Global Accelerator
    │
    ▼
ALB
    │
    ▼
EC2
```

You can also use:

- CloudFront
- Global Accelerator
- Route 53
- Elastic Load Balancing
- AWS WAF
- Shield

to reduce the attack surface and handle traffic before it reaches EC2.

---

# 6. EC2 Auto Scaling ⭐⭐⭐⭐⭐

DDoS attacks can produce huge traffic spikes.

Auto Scaling helps your application handle increased legitimate or malicious traffic by launching additional EC2 instances when demand increases.

Example:

Normally:

```
Traffic
  ↓
2 EC2 instances
```

Traffic suddenly increases:

```
Huge traffic
     ↓
Auto Scaling
     ↓
2 → 5 → 10 EC2
```

This helps your application remain available when traffic suddenly increases.

### Important!

Auto Scaling **doesn't stop DDoS attacks by itself**.

It helps your infrastructure handle increased load.

For DDoS protection, combine it with services such as:

> **CloudFront + WAF + Shield**
> 

### 🧠 Remember:

> **Auto Scaling = Handle traffic spikes**
> 

> **Shield/WAF = Defend against attacks**
> 

---

# 7. Elastic Load Balancing ⭐⭐⭐⭐⭐

ELB distributes incoming traffic across multiple EC2 instances.

Instead of:

```
100,000 requests
       │
       ▼
    EC2 #1 😵
```

you can have:

```
                  ALB
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
      EC2 #1     EC2 #2     EC2 #3
```

The traffic is distributed across instances.

ELB also scales as traffic increases.

### 🧠 Remember:

> **ELB = Spread traffic across multiple instances**
> 

---

# 8. Application Layer Defense ⭐⭐⭐⭐⭐

This is where **AWS WAF** becomes very important.

A DDoS attack isn't always just huge network traffic.

Attackers can send malicious **HTTP/HTTPS requests**.

For example:

```
Attacker
   │
   │ Malicious HTTP requests
   ▼
AWS WAF
   │
   ├── Bad request → BLOCK ❌
   │
   └── Good request → ALLOW ✅
   │
   ▼
Application
```

WAF can help detect/filter things such as:

- SQL injection
- Cross-site scripting (XSS)
- Bad IP addresses
- Geographic restrictions
- Excessive request rates

---

# 9. CloudFront + WAF ⭐⭐⭐⭐⭐

CloudFront can cache static content at edge locations.

Example:

```
100 users
     │
     ▼
CloudFront
     │
     ├── 80 requests → Cached content
     │
     └── 20 requests → Origin
```

So your origin doesn't need to process every request.

Add WAF:

```
User
 │
 ▼
CloudFront
 │
 ▼
AWS WAF
 │
 ├── Bad → BLOCK ❌
 │
 └── Good → ALLOW ✅
 │
 ▼
ALB
 │
 ▼
EC2
```

This gives you:

> **Caching + edge delivery + web request filtering**
> 

---

# 10. AWS WAF Rate-Based Rules ⭐⭐⭐⭐⭐

A rate-based rule can help protect against situations where someone sends an unusually large number of requests.

Example:

```
Normal user:
100 requests
      ↓
ALLOW ✅

Attacker:
100,000 requests
      ↓
WAF rate rule
      ↓
BLOCK / MITIGATE 🚫
```

This is especially useful for application-layer attacks.

### 🧠 Remember:

> **WAF rate-based rule = Too many web requests → control/block them**
> 

---

# 11. Attack Surface Reduction ⭐⭐⭐⭐⭐

Another major DDoS best practice:

> **Reduce what attackers can directly reach.**
> 

Don't expose every resource to the Internet.

Bad:

```
Internet
   │
   ├── EC2
   ├── Database
   ├── Lambda
   ├── Internal service
   └── Admin service
```

Better:

```
Internet
   │
   ▼
CloudFront / ALB / API Gateway
   │
   ▼
Private resources
   │
   ├── EC2
   ├── Lambda
   └── Database
```

The fewer publicly accessible resources you have, the smaller your attack surface.

---

# 12. Security Groups and Network ACLs ⭐⭐⭐⭐⭐

You can use **Security Groups** and **Network ACLs** to control network traffic.

### Security Group

Works at the instance/ENI level.

Example:

```
EC2 Security Group

Allow:
TCP 443 → HTTPS
TCP 22  → Admin access
```

### Network ACL

Works at the subnet level.

You can use them to control allowed/blocked network traffic at the subnet boundary.

### 🧠 Remember:

> **Security Group = Instance/ENI traffic control**
> 

> **NACL = Subnet-level traffic control**
> 

---

# 13. Hide Your EC2 Instances ⭐⭐⭐⭐⭐

One of the most useful architecture patterns is:

```
                    Internet
                       │
                       ▼
                  CloudFront
                       │
                       ▼
                     ALB
                       │
                       ▼
              Private EC2 instances
```

The EC2 instances don't need public IP addresses.

This is much better than:

```
Internet
    │
    ▼
Public EC2
```

Now attackers cannot simply target the EC2's public IP.

### 🧠 Exam clue:

> **"Hide EC2 instances from the Internet" → Put them behind an ALB/CloudFront and use private subnets.**
> 

---

# 14. API Gateway as Attack-Surface Reduction

For APIs, you can put:

```
Internet
    │
    ▼
API Gateway
    │
    ▼
Lambda / backend
```

Instead of exposing backend resources directly.

API Gateway provides a controlled API entry point.

You can combine it with other security mechanisms such as WAF.

---

# 15. Complete DDoS-Resilient Architecture ⭐⭐⭐⭐⭐

A strong architecture could look like:

```
                       INTERNET
                           │
               ┌───────────┴───────────┐
               │                       │
             Users                  Attackers
               │                       │
               └───────────┬───────────┘
                           ▼
                     Route 53
                      DNS
                           │
                           ▼
                      CloudFront
                    Edge + Cache
                           │
                    AWS Shield
                  DDoS Protection
                           │
                       AWS WAF
                 Web Filtering Rules
                           │
                           ▼
                          ALB
                    Load Balancing
                           │
                 ┌─────────┴─────────┐
                 ▼                   ▼
             Private EC2         Private EC2
                 │                   │
                 └─────────┬─────────┘
                           ▼
                        Database
```

And:

```
EC2
 │
 └── Auto Scaling → Handle traffic spikes

Security Groups
 │
 └── Control instance traffic

Network ACLs
 │
 └── Control subnet traffic
```

---

# 16. The 4 Main DDoS Best-Practice Categories

For the exam, organize everything into **four buckets**:

### 🟦 1. Edge Location Mitigation

```
CloudFront
Global Accelerator
Route 53
```

Think:

> **Stop/absorb traffic closer to the edge.**
> 

---

### 🟩 2. Infrastructure Layer Defense

```
Shield
Global Accelerator
CloudFront
ELB
Auto Scaling
```

Think:

> **Absorb and handle traffic before it overwhelms your application.**
> 

---

### 🟨 3. Application Layer Defense

```
AWS WAF
CloudFront
Shield Advanced
```

Think:

> **Detect and filter malicious HTTP/HTTPS requests.**
> 

---

### 🟥 4. Attack Surface Reduction

```
Security Groups
Network ACLs
Private Subnets
ALB
CloudFront
API Gateway
```

Think:

> **Don't expose your backend directly.**
> 

---

# 🧠 SAA-C03 Quick Recognition Table

| Exam wording | Think |
| --- | --- |
| DDoS protection | **AWS Shield** |
| Free/basic DDoS protection | **Shield Standard** |
| Advanced DDoS protection | **Shield Advanced** |
| AWS DDoS Response Team | **Shield Advanced** |
| Web application attacks | **AWS WAF** |
| SQL injection / XSS | **AWS WAF** |
| Rate-based HTTP protection | **AWS WAF** |
| Cache content at edge | **CloudFront** |
| Global application access | **Global Accelerator** |
| Static anycast IPs + global access | **Global Accelerator** |
| DNS | **Route 53** |
| Spread traffic across EC2 | **ELB** |
| Handle sudden traffic spikes | **Auto Scaling** |
| Hide EC2 from Internet | **Private Subnet + ALB/CloudFront** |
| Control EC2 network traffic | **Security Group** |
| Control subnet traffic | **Network ACL** |
| Central security policies across accounts | **Firewall Manager** |

---

## 🔥 Final Memory Trick

Remember the flow:

> **Route 53 → CloudFront/Global Accelerator → Shield → WAF → ALB → Auto Scaling → Private EC2**
> 

And remember what each one does:

```
Route 53
   ↓
"Where should I go?"              → DNS

CloudFront
   ↓
"Can I serve it at the edge?"     → CDN/Cache

Global Accelerator
   ↓
"How can I reach my app globally?" → Global network

Shield
   ↓
"Is this a DDoS attack?"          → DDoS protection

WAF
   ↓
"Is this web request malicious?"  → Web filtering

ALB
   ↓
"Which server should handle it?"  → Load balancing

Auto Scaling
   ↓
"Do I need more servers?"         → Scale

Private EC2
   ↓
"Keep backend hidden."            → Reduce attack surface
```

### ⭐ The biggest exam idea

> **DDoS resiliency is not one service. You build layers: use edge services to absorb traffic, Shield for DDoS protection, WAF for application-layer filtering, ELB/Auto Scaling for resilience, and private resources + security controls to reduce the attack surface.**
>
