# 51. CloudFront Origin Failover

Use origin failover when availability is important.

Example:

```
CloudFront
    │
    ├── Primary → ALB
    │
    └── Secondary → S3
```

If primary fails according to configured failover conditions:

```
Primary ❌
    ↓
Secondary ✅
```

---

# 52. CloudFront + ALB

Very common architecture:

```
Users
 ↓
CloudFront
 ↓
ALB
 ↓
Auto Scaling Group
 ↓
EC2
```

Benefits:

- CloudFront caching
- Global delivery
- WAF integration
- HTTPS
- ALB load balancing
- EC2 scaling

### Scenario

A global e-commerce application:

```
Static:
images/css/js
```

and dynamic:

```
/api/orders
```

CloudFront can cache static content while forwarding API requests to ALB.

---

# 53. CloudFront + API Gateway

Architecture:

```
Users
 ↓
CloudFront
 ↓
API Gateway
 ↓
Lambda
```

Useful when:

- Global API users
- Need edge delivery
- Need CloudFront security/caching/customization
- API Gateway is the origin

---

# 54. CloudFront + Lambda

CloudFront can work with Lambda-based architectures.

Example:

```
User
 ↓
CloudFront
 ↓
API Gateway
 ↓
Lambda
 ↓
DynamoDB
```

CloudFront is particularly useful for caching appropriate API responses and providing global edge access.

---

# 55. VPC Origins ⭐⭐⭐⭐

A newer important concept.

CloudFront can access applications in **private VPC subnets** using VPC origins.

Supported:

```
Private ALB
Private NLB
EC2 instance
```

Architecture:

```
Internet
   ↓
CloudFront
   ↓
VPC Origin
   ↓
Private ALB
   ↓
Private EC2
```

This allows CloudFront to be the public entry point while the application remains private.

### Scenario

Company says:

> "Our application servers must NOT have public internet access, but users must access the application through CloudFront."
> 

Think:

**CloudFront VPC origins**

---

# 56. CloudFront Continuous Deployment ⭐⭐⭐

Useful when you want to safely test CloudFront configuration changes.

Architecture:

```
Production
Distribution
      │
      ├── Primary
      │
      └── Staging
```

You can send a subset of production traffic to the staging distribution and test the new configuration before fully promoting it.

### Scenario

You changed:

- Cache policy
- Origin
- WAF
- Function
- Behavior

Instead of immediately affecting everyone:

```
95% → Production
5%  → Staging
```

Test → monitor → promote.

---

# 57. CloudFront + Origin Request / Response

Understand the request lifecycle:

```
                 CloudFront
                     │
Viewer Request ──────┤
                     │
                 Cache Lookup
                  /       \
              Hit          Miss
               ↓             ↓
           Response      Origin Request
                              ↓
                           Origin
                              ↓
                       Origin Response
                              ↓
                         Cache Store
                              ↓
                       Viewer Response
```

This lifecycle helps understand:

- CloudFront Functions
- Lambda@Edge
- Caching
- Headers
- Authentication
- Origin failover
