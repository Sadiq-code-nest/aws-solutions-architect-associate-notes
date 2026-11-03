# AWS Shield

## 1. Meaning

**AWS Shield is an AWS service that protects applications from DDoS (Distributed Denial of Service) attacks.**

Think:

> **Shield = Protection against DDoS attacks**
> 

---

# 2. What is DDoS?

**DDoS = Distributed Denial of Service**

Imagine you have an online shopping website:

```
                  Your Website
                       │
                       ▼
                     ALB
                       │
                       ▼
                      EC2
```

Normally, 1,000 customers might visit your website.

But suddenly, thousands or millions of malicious requests are sent toward your application:

```
Attacker 1 ───────┐
Attacker 2 ───────┤
Attacker 3 ───────┤
Attacker 4 ───────┤
Attacker 5 ───────┤
     ...          ├──────► Your Website
Millions of       │
requests          │
```

The huge amount of traffic can consume resources and make the application unavailable to normal users.

That's a **DDoS attack**.

---

# 3. Real Example ⭐⭐⭐⭐⭐

Imagine you run:

```
shop.example.com
```

Normally:

```
100 customers
      ↓
CloudFront / ALB
      ↓
Application
      ↓
Everything works ✅
```

Suddenly, attackers send enormous amounts of traffic:

```
1 million+
malicious requests
       ↓
       ↓
       ↓
AWS application
```

Your infrastructure may become overloaded.

AWS Shield is designed to help protect AWS applications against these DDoS attacks.

Think of Shield as a **security guard standing in front of your AWS application**, filtering/mitigating DDoS traffic before it causes serious disruption.

---

# 4. AWS Shield Has TWO Levels ⭐⭐⭐⭐⭐

There are two important versions:

```
AWS Shield
   │
   ├── Shield Standard
   │
   └── Shield Advanced
```

The easiest exam distinction:

> **Shield Standard = automatic/basic DDoS protection**
> 

> **Shield Advanced = stronger/advanced DDoS protection + additional features**
> 

---

# 5. AWS Shield Standard ⭐⭐⭐⭐⭐

Shield Standard is:

- Free
- Automatically enabled for AWS customers
- Provides protection against common DDoS attacks
- Primarily protects against common network/transport-layer attacks

Examples include:

- SYN floods
- UDP floods
- Reflection attacks
- Other common Layer 3/Layer 4 attacks

### Easy example

Imagine an attacker sends huge numbers of UDP packets:

```
Attacker
   │
   ├── UDP
   ├── UDP
   ├── UDP
   ├── UDP
   └── UDP
        ↓
     AWS Shield
        ↓
   DDoS mitigation
        ↓
    Application
```

Shield Standard provides automatic protection against common attacks like this.

---

# 6. AWS Shield Advanced ⭐⭐⭐⭐⭐

Shield Advanced is the **more powerful DDoS protection option**.

It provides advanced protection for resources such as:

- Amazon EC2
- Elastic Load Balancing (ELB)
- Amazon CloudFront
- AWS Global Accelerator
- Amazon Route 53

Think:

> **Standard = automatic baseline protection**
> 

> **Advanced = more sophisticated DDoS protection + extra capabilities**
> 

---

# 7. Shield Standard vs Shield Advanced

| Feature | Shield Standard | Shield Advanced |
| --- | --- | --- |
| DDoS protection | ✅ | ✅ |
| Automatically available | ✅ | ❌ Optional |
| Cost | Free | Paid |
| Common DDoS attacks | ✅ | ✅ |
| More sophisticated attacks | Basic protection | ✅ Advanced protection |
| AWS DDoS Response Team | ❌ | ✅ |
| DDoS cost protection | ❌ | ✅ |
| Advanced application-layer mitigation | ❌ | ✅ |

### Exam shortcut:

> **Free + automatic → Shield Standard**
> 

> **Advanced protection + DRT + cost protection → Shield Advanced**
> 

---

# 8. Shield Advanced — AWS DDoS Response Team ⭐⭐⭐⭐⭐

Shield Advanced provides access to the:

> **AWS DDoS Response Team (DRT)**
> 

This gives you specialised AWS assistance when dealing with significant DDoS events.

Think:

```
Major DDoS attack
       │
       ▼
Shield Advanced
       │
       ▼
AWS DDoS Response Team
       │
       ▼
Help with mitigation
```

### Exam keyword:

> **DRT → Shield Advanced**
> 

---

# 9. Shield Advanced — DDoS Cost Protection ⭐⭐⭐⭐⭐

One interesting feature is protection against certain **DDoS-related cost increases**.

Imagine your application normally receives:

```
10,000 requests/hour
```

A DDoS attack causes traffic to suddenly increase:

```
10,000
   ↓
100,000
   ↓
1,000,000+
```

Your AWS resource usage could potentially increase during the attack.

Shield Advanced provides **DDoS cost protection** that can help protect eligible AWS resource costs from certain usage spikes caused by DDoS attacks.

### Exam keyword:

> **DDoS attack → unexpected AWS usage/cost → Shield Advanced**
> 

---

# 10. Shield Advanced + AWS WAF ⭐⭐⭐⭐⭐

This is one of the most important concepts.

Shield Advanced can provide **automatic application-layer DDoS mitigation**.

It can automatically create, evaluate, and deploy **AWS WAF rules** to help mitigate Layer 7 attacks.

Conceptually:

```
                 DDoS Attack
                      │
                      ▼
               Shield Advanced
                      │
                      │ detects Layer 7 attack
                      ▼
                  AWS WAF
                      │
                WAF Rule created
                      │
                      ▼
                  Bad traffic
                    blocked
```

So:

> **Shield Advanced + WAF = DDoS protection + application-layer filtering**
> 

---

# 11. Layer 3/4 vs Layer 7 ⭐⭐⭐⭐⭐

This is useful for SAA questions.

### Layer 3/4

Examples:

```
SYN Flood
UDP Flood
Reflection Attack
```

Think:

> **Network/transport-level DDoS**
> 

Shield provides protection here.

### Layer 7

This is application/web traffic:

```
HTTP
HTTPS
Web requests
```

For example:

```
Attacker
   │
   │ Millions of HTTP requests
   ▼
Your Web Application
```

Shield Advanced can work with AWS WAF to automatically help mitigate application-layer DDoS attacks.

---

# 12. Shield vs WAF ⭐⭐⭐⭐⭐

Don't confuse these two.

| AWS Shield | AWS WAF |
| --- | --- |
| DDoS protection | Web application firewall |
| Protects against DDoS attacks | Filters HTTP/HTTPS requests |
| Network/transport DDoS protection | Application-layer rules |
| Shield Standard/Advanced | Web ACL + rules |
| Advanced includes DRT | WAF rules can block SQL injection, XSS, etc. |

### Easy memory:

> 🛡️ **Shield = DDoS**
> 

> 🔥 **WAF = Web request filtering**
> 

---

# 13. Shield + WAF Example

Suppose your application is:

```
Internet
    │
    ▼
CloudFront
    │
    ▼
AWS WAF
    │
    ▼
ALB
    │
    ▼
EC2
```

An attacker launches a DDoS attack.

Shield helps with DDoS protection.

WAF can inspect web requests and apply application-layer rules.

```
                   Internet
                      │
                      ▼
                AWS Shield
               DDoS Protection
                      │
                      ▼
                  CloudFront
                      │
                      ▼
                   AWS WAF
                Web ACL Rules
                      │
                      ▼
                     ALB
                      │
                      ▼
                     EC2
```

---

# 14. Shield Standard vs Advanced — Real Scenario

### Scenario A

> "A company wants basic DDoS protection without paying extra."
> 

Answer:

> ✅ **AWS Shield Standard**
> 

Why?

It is automatically available and provides baseline DDoS protection.

---

### Scenario B

> "A company needs advanced DDoS protection and access to AWS DDoS Response Team."
> 

Answer:

> 🔥 **AWS Shield Advanced**
> 

Keyword:

> **DDoS Response Team → Shield Advanced**
> 

---

### Scenario C

> "A company wants protection against DDoS-related usage spikes and unexpected costs."
> 

Answer:

> 🔥 **Shield Advanced**
> 

Keyword:

> **DDoS cost protection → Shield Advanced**
> 

---

### Scenario D

> "A company needs automatic mitigation of sophisticated Layer 7 DDoS attacks."
> 

Answer:

> 🔥 **Shield Advanced + AWS WAF**
> 

---

# 15. Shield Advanced Protected Resources

Remember these important AWS services:

```
Shield Advanced
      │
      ├── EC2
      ├── ELB
      ├── CloudFront
      ├── Global Accelerator
      └── Route 53
```

If an exam question says:

> "Protect CloudFront/Route 53/Global Accelerator from sophisticated DDoS attacks"
> 

Think:

> **Shield Advanced**
> 

---

# 16. Shield vs Security Group

Another common confusion:

### Security Group

Controls network traffic such as:

```
TCP 443
TCP 80
TCP 22
```

Example:

```
Allow HTTPS
Allow SSH
```

### Shield

Protects against:

```
DDoS attacks
```

So:

> **Security Group → Network access control**
> 

> **Shield → DDoS protection**
> 

---

# 17. Shield vs AWS WAF vs Security Group

| Need | Service |
| --- | --- |
| DDoS protection | 🛡️ AWS Shield |
| SQL Injection | 🔥 AWS WAF |
| XSS | 🔥 AWS WAF |
| Block country | 🔥 AWS WAF |
| Rate-based HTTP rules | 🔥 AWS WAF |
| Allow TCP 443 | 🔐 Security Group |
| Allow TCP 22 | 🔐 Security Group |

---

# 18. Important Pricing Exam Note

Shield Standard:

> **Free**
> 

Shield Advanced:

> **Paid optional service**
> 

For the SAA exam, the most important thing isn't memorizing the exact price.

Remember:

> **Standard = Free**
> 

> **Advanced = Paid**
> 

---

# 19. SAA-C03 Exam Recognition ⭐⭐⭐⭐⭐

**"Protect against common DDoS attacks automatically at no additional cost."**

→ ✅ **Shield Standard**

**"Need advanced DDoS protection."**

→ 🔥 **Shield Advanced**

**"Need AWS DDoS Response Team (DRT)."**

→ 🔥 **Shield Advanced**

**"Need protection against DDoS-related usage/cost spikes."**

→ 🔥 **Shield Advanced**

**"Need automatic Layer 7 DDoS mitigation using WAF rules."**

→ 🔥 **Shield Advanced + AWS WAF**

**"Protect CloudFront against sophisticated DDoS attacks."**

→ 🔥 **Shield Advanced**

**"Protect Route 53 against advanced DDoS attacks."**

→ 🔥 **Shield Advanced**

---

# Final Memory Trick 🧠

```
             AWS SHIELD
                 │
          "DDoS Protection"
                 │
        ┌────────┴────────┐
        │                 │
     STANDARD          ADVANCED
        │                 │
     Free             Paid
     Automatic        Advanced
     Common DDoS      Sophisticated DDoS
        │                 │
        │              DRT Access
        │              Cost Protection
        │              Layer 7 mitigation
        │                 │
        └────────┬────────┘
                 │
             AWS WAF
          Layer 7 filtering
```

### 🧠 One sentence to remember

> **Shield protects against DDoS; Standard is free and automatic, while Advanced adds stronger DDoS protection, DRT support, cost protection, and automatic Layer 7 mitigation with WAF.**
>
