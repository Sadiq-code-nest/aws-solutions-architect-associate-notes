# AWS Certificate Manager (ACM) — AWS SAA-C03

## 1. Meaning

**AWS Certificate Manager (ACM) is a service that provisions, manages, and deploys TLS certificates for HTTPS and other encrypted connections.**

Think:

> **ACM = AWS's certificate manager for HTTPS.**
> 

---

# 2. What Problem Does ACM Solve?

Suppose you have a website:

```
http://myshop.com
```

Users want:

```
https://myshop.com
```

HTTPS requires a **TLS certificate**.

Instead of manually buying, installing, renewing, and managing certificates, ACM handles the certificate lifecycle.

```
                 AWS Certificate Manager
                          │
                   TLS Certificate
                          │
                          ▼
                    Load Balancer
                          │
                          ▼
                     HTTPS Website
```

---

# 3. Real Example ⭐⭐⭐⭐⭐

Imagine you have an online shopping application:

```
Customer
    │
    │ HTTPS
    ▼
Application Load Balancer
    │
    ▼
EC2 Auto Scaling Group
    │
    ▼
Shopping Application
```

You want customers to safely access:

```
https://shop.example.com
```

You request a certificate from **ACM** for:

```
shop.example.com
```

ACM manages the certificate.

You attach it to the **Application Load Balancer**.

Now:

```
Customer
   │
   │ HTTPS
   ▼
ALB
   │
   │ HTTP
   ▼
EC2
```

The ALB handles the TLS connection.

---

# 4. In-Flight Encryption

ACM certificates are commonly used for **TLS encryption in transit**.

Without HTTPS:

```
Client ────────────────> Server

Password
Credit Card
Personal Data

❌ Can potentially be intercepted
```

With HTTPS:

```
Client
  │
  │ 🔒 TLS / HTTPS
  ▼
Server

Encrypted Data
```

This protects data while it travels between the client and server.

---

# 5. Public and Private Certificates

ACM supports:

### Public TLS Certificates

Used for publicly accessible websites.

Example:

```
https://www.example.com
```

### Private TLS Certificates

Used for internal applications and private networks.

Example:

```
https://internal.company.local
```

---

# 6. Public ACM Certificates Are Free ⭐⭐⭐⭐⭐

AWS provides **public TLS certificates through ACM at no additional charge**.

The important exam idea is:

> **ACM public certificate = free**
> 

---

# 7. Automatic Renewal ⭐⭐⭐⭐⭐

This is one of the most important ACM features.

TLS certificates expire.

Without automation:

```
Certificate expires

↓

Website HTTPS breaks ❌
```

ACM can automatically renew **ACM-issued public certificates**.

```
Certificate
    │
    │ Approaching expiration
    ▼
ACM
    │
    ▼
Automatically renew
    │
    ▼
Continue HTTPS
```

ACM-generated certificates are automatically renewed **60 days before expiration**, assuming the required validation and service integration remain valid.

---

# 8. Where Can ACM Certificates Be Used?

Important integrations include:

### Elastic Load Balancing

- Application Load Balancer
- Network Load Balancer
- Classic Load Balancer

### CloudFront

For HTTPS on CloudFront distributions.

### API Gateway

For custom domains using HTTPS.

Think:

> **ACM → ALB / NLB / CLB / CloudFront / API Gateway**
> 

---

# 9. Important: ACM + EC2 ⭐⭐⭐⭐⭐

This is a common exam trap.

You generally **cannot export an ACM-managed public certificate's private key for installation directly on an EC2 instance**.

So don't think:

```
ACM
 ↓
Download certificate
 ↓
EC2
```

Instead, use ACM with supported AWS integrations such as:

```
ACM
 ↓
ALB
 ↓
EC2
```

The load balancer handles TLS.

---

# 10. ACM + ALB — Very Common Architecture

A very common setup is:

```
                    Internet
                       │
                    HTTPS
                       │
                       ▼
              Application Load
                  Balancer
                       │
                TLS Certificate
                       ▲
                       │
                      ACM
                       │
                       ▼
                EC2 Auto Scaling
                   Group
```

The user connects using HTTPS.

The ALB uses the ACM certificate.

The ALB can then forward the request to EC2 instances.

---

# 11. HTTP → HTTPS Redirect ⭐⭐⭐⭐⭐

You can configure the ALB to redirect HTTP requests to HTTPS.

Example:

User enters:

```
http://example.com
```

ALB responds:

```
Redirect → https://example.com
```

Then the user connects securely.

Architecture:

```
              User
               │
               │ HTTP
               ▼
              ALB
               │
          Redirect
               │
               ▼
              HTTPS
               │
               ▼
          Application
```

This is a very common real-world configuration.

---

# 12. Requesting a Public Certificate

When requesting a public ACM certificate, you specify the domain names.

Example:

```
example.com
www.example.com
api.example.com
```

You can also use a wildcard:

```
*.example.com
```

---

# 13. Wildcard Certificate ⭐⭐⭐⭐⭐

A wildcard:

```
*.example.com
```

can cover subdomains such as:

```
www.example.com
api.example.com
shop.example.com
```

But it doesn't cover arbitrary deeper levels such as:

```
test.api.example.com
```

For SAA-C03, remember:

> **`*.example.com` = subdomains of example.com**
> 

---

# 14. Certificate Validation

When you request a public certificate, ACM needs to verify that you control the domain.

You can use:

```
ACM Certificate Validation

        │
        ├── DNS Validation
        │
        └── Email Validation
```

---

# 15. DNS Validation ⭐⭐⭐⭐⭐

DNS validation is generally preferred for automation.

ACM gives you a **CNAME record**.

You add it to your DNS configuration, commonly using Route 53.

Example:

```
ACM

↓

Give CNAME record

↓

Route 53

↓

DNS Validation

↓

Certificate Issued
```

Once AWS verifies the record, ACM issues the certificate.

---

# Why DNS Validation Is Better for Automation

Suppose your certificate needs renewal.

With DNS validation, ACM can continue verifying domain ownership automatically as long as the validation record remains in place.

Think:

> **DNS validation = automation-friendly**
> 

---

# 16. Email Validation

ACM can also send validation emails to domain contacts.

```
ACM

↓

Validation Email

↓

Domain Owner

↓

Click / Approve

↓

Certificate Issued
```

This requires someone to respond to the email.

Therefore:

> **DNS validation is generally preferred for automation.**
> 

---

# 17. ACM Public Certificate Request Flow

```
1. Request Certificate

        ↓

2. Enter Domain
   example.com
   *.example.com

        ↓

3. Choose Validation

   DNS or Email

        ↓

4. AWS Verifies Domain

        ↓

5. Certificate Issued

        ↓

6. Attach Certificate

   ALB / CloudFront / API Gateway

        ↓

7. Automatic Renewal
```

---

# 18. Importing Your Own Certificate

You don't have to create the certificate through ACM.

You can create or obtain a certificate elsewhere and **import it into ACM**.

Example:

```
Third-Party Certificate Authority

            ↓

       Certificate

            ↓

       Import into ACM

            ↓

      Use with supported
      AWS services
```

---

# 19. Important Difference: Imported Certificate

This is a major exam distinction.

If you **import** a certificate into ACM:

❌ ACM does not automatically renew it.

You need to obtain a replacement certificate before expiration and import it again.

So:

```
ACM-issued certificate
       ↓
Automatic renewal ✅

Imported certificate
       ↓
Automatic renewal ❌
```

---

# 20. Expiration Events for Imported Certificates

ACM can help you monitor imported certificates.

It sends **expiration events through Amazon EventBridge**, starting **45 days before expiration by default**.

Example:

```
Imported Certificate

       ↓
45 days before expiration

       ↓
EventBridge Event

       ↓
Notification / Automation
```

The number of days can be configured.

---

# 21. AWS Config + ACM

AWS Config provides a managed rule:

```
acm-certificate-expiration-check
```

It can check whether certificates are approaching expiration based on the configured number of days.

Example:

```
AWS Config

       ↓

acm-certificate-expiration-check

       ↓

Certificate nearing expiration?

       ↓

NON_COMPLIANT
```

This is useful for compliance monitoring.

---

# 22. ACM + API Gateway ⭐⭐⭐⭐⭐

ACM can be used with API Gateway when you want a custom HTTPS domain.

For example:

```
Instead of:

https://abc123.execute-api.amazonaws.com

Use:

https://api.example.com
```

You create a custom domain in API Gateway and associate a TLS certificate.

---

# 23. API Gateway Edge-Optimized Endpoint

For an **Edge-Optimized API Gateway endpoint**:

```
Global User
     │
     ▼
CloudFront Edge Location
     │
     ▼
API Gateway
     │
     ▼
API
```

The API Gateway endpoint itself exists in **one AWS Region**, but requests are routed through CloudFront edge locations to improve latency for global clients.

### Important Certificate Rule ⭐⭐⭐⭐⭐

For an **Edge-Optimized API Gateway custom domain**, the ACM certificate must be in:

> **US East (N. Virginia) — `us-east-1`**
> 

Remember:

> **API Gateway Edge-Optimized → ACM certificate in `us-east-1`**
> 

---

# 24. API Gateway Regional Endpoint

For a **Regional API Gateway endpoint**:

```
Client
   │
   ▼
API Gateway
   │
   ▼
Regional API
```

This is useful when clients are primarily in the same Region.

The TLS certificate must be in the **same AWS Region as the API Gateway endpoint**.

---

# 25. Regional + CloudFront

You can also manually put CloudFront in front of a Regional API Gateway endpoint.

This gives you more control over CloudFront configuration and caching.

```
Global Users
     │
     ▼
CloudFront
     │
     ▼
Regional API Gateway
     │
     ▼
Application
```

This is different from simply choosing API Gateway's built-in **Edge-Optimized** endpoint.

---

# 26. API Gateway Endpoint Comparison

|  | Edge-Optimized | Regional |
| --- | --- | --- |
| Best for | Global clients | Clients near the Region |
| CloudFront | Automatically involved | Can manually add CloudFront |
| API Gateway | One Region | One Region |
| ACM certificate | `us-east-1` | Same Region as API |
| Main idea | Global latency improvement | Regional control |

---

# 27. ACM + CloudFront ⭐⭐⭐⭐⭐

CloudFront supports HTTPS using ACM certificates.

Architecture:

```
User
 │
 │ HTTPS
 ▼
CloudFront
 │
 │
 ▼
ALB / S3 / Origin
```

For CloudFront, the ACM certificate must be in:

> **US East (N. Virginia) — `us-east-1`**
> 

This is a very common SAA-C03 question.

---

# 28. The Important Region Rule

Memorize this:

```
CloudFront
      │
      ▼
ACM Certificate
      │
      ▼
us-east-1
```

And:

```
API Gateway Edge-Optimized
      │
      ▼
ACM Certificate
      │
      ▼
us-east-1
```

But:

```
API Gateway Regional
      │
      ▼
ACM Certificate
      │
      ▼
Same Region as API
```

---

# 29. ACM Certificate Architecture

```
                       AWS Certificate Manager
                                │
             ┌──────────────────┼──────────────────┐
             │                  │                  │
             ▼                  ▼                  ▼
           ALB              CloudFront        API Gateway
             │                  │                  │
             ▼                  ▼                  ▼
          HTTPS              HTTPS             HTTPS
             │                  │                  │
             ▼                  ▼                  ▼
           EC2                Origin              API
```

---

# 30. ACM vs IAM Certificate Store

For SAA-C03, remember the modern approach:

> **Use ACM to manage TLS certificates for supported AWS services.**
> 

Don't think:

> "I need to manually install certificates everywhere."
> 

AWS services such as ALB, CloudFront, and API Gateway integrate directly with ACM.

---

# Easy Memory Tricks ⭐⭐⭐⭐⭐

### ACM

> **ACM = HTTPS Certificate Manager**
> 

### TLS

> **TLS = Encryption in transit**
> 

### DNS Validation

> **DNS = Best for automation**
> 

### Public ACM Certificate

> **Free + automatic renewal**
> 

### Imported Certificate

> **No automatic renewal**
> 

### ALB

> **ACM certificate → ALB → HTTPS**
> 

### CloudFront

> **ACM certificate → `us-east-1`**
> 

### API Gateway Edge-Optimized

> **ACM certificate → `us-east-1`**
> 

### API Gateway Regional

> **ACM certificate → Same Region as API**
> 

### EC2

> **Don't directly install/export ACM-managed public certificate private keys onto EC2**
> 

---

# AWS SAA-C03 Exam Recognition

If you see:

**"Enable HTTPS on an ALB."**

→ ✅ **ACM certificate**

**"Automatically renew a public TLS certificate."**

→ ✅ **Use an ACM-issued certificate**

**"Certificate created outside ACM."**

→ ⚠️ **Import into ACM, but renewal must be managed manually**

**"Validate domain automatically."**

→ ⭐ **DNS validation**

**"Global CloudFront HTTPS certificate."**

→ ⭐ **ACM certificate in `us-east-1`**

**"API Gateway Edge-Optimized custom domain."**

→ ⭐ **ACM certificate in `us-east-1`**

**"API Gateway Regional custom domain."**

→ ⭐ **ACM certificate in the same Region as API Gateway**

**"Redirect HTTP users to HTTPS."**

→ ✅ **Configure HTTP → HTTPS redirect on the ALB**

**"Monitor imported certificates approaching expiration."**

→ ✅ **EventBridge expiration events / AWS Config**

---

# Final Memory Diagram

```
                         ACM
                          │
                    TLS Certificate
                          │
          ┌───────────────┼────────────────┐
          │               │                │
          ▼               ▼                ▼
         ALB          CloudFront       API Gateway
          │               │                │
          ▼               ▼                ▼
        HTTPS           HTTPS            HTTPS
          │               │                │
          ▼               ▼                ▼
         EC2             Origin           API

Important Region Rule:

CloudFront
     → ACM certificate in us-east-1

API Gateway Edge-Optimized
     → ACM certificate in us-east-1

API Gateway Regional
     → ACM certificate in same Region as API
```

## One-Line Summary

**AWS Certificate Manager provisions and manages TLS certificates for HTTPS, supports DNS/email validation, automatically renews ACM-issued public certificates, integrates with ALB/CloudFront/API Gateway, and requires special attention to certificate Region rules for CloudFront and API Gateway Edge-Optimized endpoints.**
