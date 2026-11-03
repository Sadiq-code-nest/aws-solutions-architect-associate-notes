# SSL/TLS & SNI — AWS SAA-C03 Exam Notes

## 1. SSL/TLS Basics

**SSL/TLS provides encryption in transit (in-flight encryption).**

```
Client
   │
   │ HTTPS / TLS encrypted
   ▼
Load Balancer
   │
   │ HTTP or HTTPS inside VPC
   ▼
EC2
```

- **SSL** = Secure Sockets Layer — older protocol.
- **TLS** = Transport Layer Security — newer and used today.
- People often say **SSL certificate**, but modern certificates are used with TLS.
- Certificates use the **X.509** standard.
- Public certificates are issued by **Certificate Authorities (CAs)**.
- Certificates have an expiration date and must be renewed.

---

# 2. HTTPS Listener on Load Balancer

For HTTPS:

```
Client
   │
   │ HTTPS / TLS
   ▼
ALB / NLB
   │
   │ HTTP or HTTPS
   ▼
Target
```

The Load Balancer can terminate TLS.

### TLS Termination

The Load Balancer decrypts the HTTPS request and forwards the request to the backend.

Example:

```
Client
   │ HTTPS
   ▼
ALB
   │ TLS termination
   │ HTTP
   ▼
EC2
```

This is useful because your EC2 instances don't need to perform the TLS encryption/decryption themselves.

---

# 3. AWS Certificate Manager — ACM

AWS Certificate Manager (**ACM**) can manage SSL/TLS certificates.

You can:

- Request public certificates.
- Import certificates obtained elsewhere.
- Attach certificates to supported AWS services.
- Automate certificate renewal for ACM-issued certificates.

Typical architecture:

```
User
  │
 HTTPS
  ▼
ALB
  │
 ACM Certificate
  │
  ▼
EC2
```

### Exam clue

> **"HTTPS on an ALB + certificate management"**
> 

Think:

**ACM**

---

# 4. Default Certificate

An HTTPS listener requires a **default certificate**.

Example:

```
HTTPS Listener :443

Default Certificate
        │
        ▼
www.example.com
```

You can also configure additional certificates.

This becomes important when one Load Balancer serves **multiple domains**.

---

# 5. SNI—Server Name Indication ⭐⭐⭐⭐⭐

### Problem

Suppose one ALB serves:

```
www.company.com
www.shop.com
www.example.com
```

Each website has a different certificate.

How does the Load Balancer know which certificate to return?

### SNI solves this.

During the TLS handshake, the client tells the server:

> "I want to connect to `www.company.com`."
> 

The Load Balancer selects the matching certificate.

```
                    TLS Handshake
                         │
                         │ Hostname:
                         │ www.company.com
                         ▼
Client ───────────────► ALB
                         │
                  Select matching
                    certificate
                         │
                         ▼
                  Company Certificate
```

---

# 6. SNI — Simple Example

Suppose:

| Domain | Certificate |
| --- | --- |
| `www.amazon-example.com` | Certificate A |
| `www.shop-example.com` | Certificate B |
| `www.api-example.com` | Certificate C |

All three use the same ALB.

```
                  ALB
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
Certificate A  Certificate B  Certificate C
       │           │           │
   Amazon        Shop          API
```

The client sends the hostname during the TLS handshake, allowing ALB/NLB to select the correct certificate.

---

# 7. Which Load Balancers Support SNI?

| Load Balancer | SNI Support | Multiple Certificates |
| --- | --- | --- |
| **Classic Load Balancer (CLB)** | ❌ No | ❌ One certificate |
| **Application Load Balancer (ALB)** | ✅ Yes | ✅ |
| **Network Load Balancer (NLB)** | ✅ Yes | ✅ |
| **Gateway Load Balancer (GWLB)** | Not used for HTTPS listener scenarios | — |

### ⭐ Exam memory

> **SNI → ALB, NLB, CloudFront**
> 

**CLB = old → no SNI**

---

# 8. CLB vs ALB/NLB for Multiple HTTPS Domains

### Classic Load Balancer

Suppose:

```
www.a.com
www.b.com
www.c.com
```

Each requires a different certificate.

CLB supports only one SSL certificate.

You would need separate CLBs.

```
www.a.com ──► CLB 1 ──► Certificate A

www.b.com ──► CLB 2 ──► Certificate B

www.c.com ──► CLB 3 ──► Certificate C
```

### ALB/NLB

One Load Balancer can support multiple certificates using SNI.

```
                 ALB
                  │
        ┌─────────┼─────────┐
        ▼         ▼         ▼
      Cert A    Cert B    Cert C
        │         │         │
      a.com     b.com     c.com
```

---

# 9. SSL/TLS End-to-End Encryption

TLS termination doesn't necessarily mean traffic after the Load Balancer must be HTTP.

You can use:

### Option 1 — TLS termination at ALB

```
Client
  │ HTTPS
  ▼
ALB
  │ HTTP
  ▼
EC2
```

Encryption exists between client and ALB.

### Option 2 — Re-encryption to backend

```
Client
  │ HTTPS
  ▼
ALB
  │ HTTPS
  ▼
EC2
```

This provides encryption both:

```
Client ──HTTPS──► ALB ──HTTPS──► EC2
```

Useful when you require encryption throughout the entire path.

---

# 10. Security Policy

HTTPS listeners can use a **security policy** that determines which TLS protocols and cryptographic algorithms are supported.

This matters when you need to support:

- Older clients
- Modern TLS versions
- Stronger security requirements

### Exam clue

> **"Need to support legacy/older clients"**
> 

Think:

**Choose an appropriate TLS security policy.**

---

# MASTER TABLE — SSL/TLS + SNI

| Concept | What it does | Exam clue |
| --- | --- | --- |
| **SSL** | Older encryption protocol | Historical term |
| **TLS** | Modern encryption protocol | HTTPS encryption |
| **X.509 certificate** | Identifies server/domain and enables TLS | SSL/TLS certificate |
| **ACM** | Manages AWS certificates | Certificate management |
| **HTTPS listener** | Accepts encrypted HTTPS traffic | Port 443 |
| **TLS termination** | LB decrypts client HTTPS traffic | HTTPS → HTTP to EC2 |
| **Re-encryption** | LB forwards traffic using HTTPS | HTTPS → HTTPS |
| **Default certificate** | Certificate used when no more specific certificate matches | Required for HTTPS listener |
| **SNI** | Allows multiple certificates/domains on one LB | Multiple websites, one ALB |
| **ALB + SNI** | Multiple HTTPS certificates | Modern Layer 7 LB |
| **NLB + SNI** | Multiple TLS certificates | Modern Layer 4 LB |
| **CLB** | Legacy LB, one SSL certificate | ❌ No SNI |
| **Security Policy** | Controls supported TLS protocols/ciphers | Legacy client compatibility |

---

# 🧠 SAA-C03 Exam Memory

### If you see:

**"Multiple domains, one Application Load Balancer"**

➡️ **SNI**

---

**"Multiple SSL certificates on ALB"**

➡️ **SNI**

---

**"Multiple SSL certificates on NLB"**

➡️ **SNI**

---

**"Manage SSL/TLS certificates on AWS"**

➡️ **ACM**

---

**"HTTPS encryption between users and Load Balancer"**

➡️ **HTTPS listener + certificate**

---

**"HTTPS from client but HTTP between ALB and EC2"**

➡️ **TLS termination at ALB**

---

**"Encryption must continue from ALB to EC2"**

➡️ **HTTPS/TLS between ALB and targets too**

---

**"Old Classic Load Balancer with multiple certificates"**

➡️ **CLB doesn't support SNI → historically use separate CLBs**

---

### One-line memory

> **ACM manages certificates → HTTPS listener uses certificates → TLS encrypts traffic → SNI lets ALB/NLB serve multiple certificates → CLB is the old exception.**
>
