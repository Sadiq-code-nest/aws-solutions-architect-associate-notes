# Application Load Balancer (ALB) — v2

**Application Load Balancer (ALB)** is an AWS managed **Layer 7 (Application Layer)** load balancer designed mainly for **HTTP/HTTPS applications**.

### Core idea

> **ALB understands HTTP requests and can make routing decisions based on the content of the request.**
> 

For example:

```
User
  ↓
ALB
  ├── /users  → User Service
  ├── /orders → Order Service
  └── /admin  → Admin Service
```

This makes ALB especially useful for **web applications, microservices, containers, and APIs**.

---

# 1. ALB Layer & Protocols

| Feature | ALB |
| --- | --- |
| Generation | **v2 / modern** |
| OSI Layer | **Layer 7** |
| Main protocols | HTTP, HTTPS |
| WebSocket | ✅ Supported |
| HTTP/2 | ✅ Supported |
| SSL/TLS termination | ✅ |
| Path-based routing | ✅ |
| Host-based routing | ✅ |
| Query-string routing | ✅ |
| Header-based routing | ✅ |
| Redirects | ✅ |
| Target Groups | ✅ |
| Health Checks | ✅ |
| Fixed DNS hostname | ✅ |

### Exam memory

> **ALB = Layer 7 = HTTP/HTTPS = smart/content-based routing**
> 

---

# 2. How ALB Works

Basic architecture:

```
                Internet
                   │
                   ▼
             Application
           Load Balancer
                   │
          ┌────────┴────────┐
          ▼                 ▼
    Target Group 1     Target Group 2
          │                 │
     EC2 / ECS          EC2 / ECS
```

The ALB receives the HTTP request and decides **which Target Group should receive it**.

---

# 3. Target Groups

A **Target Group** is a logical group of backend targets that receive traffic from the ALB.

ALB can send traffic to:

| Target type | Supported? | Example |
| --- | --- | --- |
| EC2 instances | ✅ | Web servers |
| ECS tasks | ✅ | Containerized applications |
| Lambda functions | ✅ | Serverless backend |
| Private IP addresses | ✅ | On-premises/private servers |

### Important

> **Health checks are configured at the Target Group level.**
> 

Example:

```
ALB
 │
 ├── Target Group: Users
 │      ├── EC2-1
 │      └── EC2-2
 │
 └── Target Group: Orders
        ├── EC2-3
        └── EC2-4
```

The ALB can have **multiple listeners/rules and multiple target groups**.

---

# 4. Path-Based Routing ⭐⭐⭐⭐⭐

ALB can route traffic based on the **URL path**.

Example:

```
example.com/users
        ↓
   Users Target Group

example.com/orders
        ↓
   Orders Target Group

example.com/products
        ↓
   Products Target Group
```

### Scenario

A company has a microservices application:

- User service
- Order service
- Product service

Instead of creating separate load balancers:

```
ALB
 ├── /users     → User service
 ├── /orders    → Order service
 └── /products  → Product service
```

### Exam clue

> **Different URL paths → different applications → ALB**
> 

---

# 5. Host-Based Routing ⭐⭐⭐⭐⭐

ALB can route based on the **hostname/domain**.

Example:

```
users.example.com
        ↓
Users Target Group

orders.example.com
        ↓
Orders Target Group
```

Or:

```
api.example.com
        ↓
API servers

admin.example.com
        ↓
Admin servers
```

### Exam clue

> **Different hostnames/subdomains → ALB host-based routing**
> 

---

# 6. Query String Routing

ALB can also route based on **query parameters**.

Example:

```
example.com/app?platform=mobile
```

→ Mobile Target Group

```
example.com/app?platform=desktop
```

→ Desktop Target Group

Architecture:

```
                    ALB
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
platform=mobile          platform=desktop
          │                     │
          ▼                     ▼
 Mobile Target Group     Desktop Target Group
```

---

# 7. Header-Based Routing

ALB can inspect HTTP headers and route traffic accordingly.

Example:

```
Header:
X-App-Version: v2
```

→ Version 2 application

While:

```
X-App-Version: v1
```

→ Version 1 application

Useful for:

- Application version routing
- Testing
- Canary deployments
- Advanced application routing

---

# 8. Redirects

ALB can perform redirects.

Most common example:

```
HTTP
 ↓
ALB
 ↓
HTTPS
```

Example:

```
http://example.com
        ↓
ALB
        ↓
https://example.com
```

This means you don't necessarily need your application servers to implement the HTTP → HTTPS redirect themselves.

---

# 9. ALB + ECS ⭐⭐⭐⭐⭐

ALB is particularly useful with **Amazon ECS**.

Example:

```
Users
  ↓
ALB
  ↓
ECS Service
  ├── Task 1
  ├── Task 2
  └── Task 3
```

ALB supports **dynamic port mapping**, which is important for containerized applications.

For example:

```
EC2 Host
 ├── Container A → Port 32768
 ├── Container B → Port 32769
 └── Container C → Port 32770
```

ALB can automatically route traffic to the appropriate container/port.

### Exam clue

> **Containers + HTTP/HTTPS + dynamic port mapping → ALB**
> 

---

# 10. ALB + Microservices

This is one of the most important use cases.

Imagine:

```
                   ALB
                    │
       ┌────────────┼─────────────┐
       ▼            ▼             ▼
    /users       /orders       /payments
       │            │             │
       ▼            ▼             ▼
 User Service   Order Service  Payment Service
```

One ALB can expose multiple services.

### Why?

Because ALB understands **HTTP paths, hosts, headers, and query strings**.

---

# 11. ALB Health Checks ⭐⭐⭐⭐⭐

ALB checks whether targets are healthy before sending traffic.

Example:

```
Protocol: HTTP
Port: 4567
Path: /health
```

The target might return:

```
HTTP 200 OK
```

→ Healthy ✅

If it returns an error:

```
HTTP 500
```

→ Unhealthy ❌

ALB stops sending traffic to unhealthy targets.

```
ALB
 │
 ├── EC2-1 → Healthy ✅ → Traffic
 ├── EC2-2 → Healthy ✅ → Traffic
 └── EC2-3 → Unhealthy ❌ → No traffic
```

---

# 12. ALB + Auto Scaling Group

ALB works very well with ASG.

```
             Users
                ↓
               ALB
                ↓
          Target Group
                ↓
        Auto Scaling Group
       ┌────────┼────────┐
       ▼        ▼        ▼
      EC2      EC2      EC2
```

When ASG launches a new instance:

```
New EC2
   ↓
Automatically registered
   ↓
Target Group
   ↓
ALB sends traffic
```

When an instance is terminated, it is removed from the target group.

---

# 13. Client IP Address — Important ⭐⭐⭐⭐

Because ALB terminates the client connection:

```
Client
  │
  │ Client IP = 12.34.56.78
  ▼
 ALB
  │
  │
  ▼
 EC2
```

The backend application does **not necessarily see the original client IP as the network source IP**.

Instead, ALB provides the original client IP through HTTP headers.

### Important headers

| Header | Purpose |
| --- | --- |
| `X-Forwarded-For` | Original client IP |
| `X-Forwarded-Port` | Original client port |
| `X-Forwarded-Proto` | Original protocol, e.g. HTTP/HTTPS |

Example:

```
X-Forwarded-For: 12.34.56.78
X-Forwarded-Proto: https
X-Forwarded-Port: 443
```

### Exam clue

> **Need original client IP behind ALB → `X-Forwarded-For`**
> 

---

# 14. Connection Termination

ALB terminates the client connection:

```
Client
   │ HTTPS
   ▼
  ALB
   │ HTTP/HTTPS
   ▼
 EC2
```

This allows ALB to perform tasks such as:

- TLS termination
- HTTP routing
- Request inspection
- Redirects

---

# 15. ALB Security Groups ⭐⭐⭐⭐⭐

Recommended architecture:

```
Internet
   │
   ▼
ALB Security Group
   │
   ▼
EC2 Security Group
```

### ALB Security Group

Allow:

```
HTTP 80
HTTPS 443
from Internet
```

### EC2 Security Group

Allow application traffic **only from the ALB Security Group**.

Example:

```
ALB SG
  ↓
EC2 SG

EC2 SG:
HTTP 80 → Source = ALB Security Group
```

This prevents users from directly accessing the EC2 instances.

---

# 16. ALB + Lambda

ALB can invoke a Lambda function as a target.

```
Client
  ↓
ALB
  ↓
Lambda
```

The HTTP request is converted into an event that Lambda receives.

Useful when:

- You want HTTP access to serverless functions
- You don't need API Gateway features
- You already use ALB for your application architecture

---

# 17. ALB + Private IP Targets

ALB can route traffic to **private IP addresses**.

This can be useful for:

```
ALB
 ↓
Private IP
 ↓
On-premises application
```

provided the required network connectivity exists.

---

# 18. ALB vs Classic Load Balancer

| Feature | CLB | ALB |
| --- | --- | --- |
| Generation | v1 | **v2** |
| Status | Legacy | **Recommended** |
| Layer | L4/L7 | **L7** |
| HTTP | ✅ | ✅ |
| HTTPS | ✅ | ✅ |
| WebSocket | ❌/limited legacy | ✅ |
| Path routing | ❌ | ✅ |
| Host routing | ❌ | ✅ |
| Query-string routing | ❌ | ✅ |
| Header routing | ❌ | ✅ |
| Lambda target | ❌ | ✅ |
| ECS integration | Limited | **Excellent** |
| Microservices | Poor fit | **Excellent** |
| Dynamic port mapping | ❌ | ✅ |

---

# 19. ALB vs NLB — Very Important ⭐⭐⭐⭐⭐

| Feature | ALB | NLB |
| --- | --- | --- |
| Layer | **L7** | **L4** |
| Main protocols | HTTP/HTTPS | TCP/UDP/TLS |
| Understands HTTP | ✅ | ❌ |
| Path-based routing | ✅ | ❌ |
| Host-based routing | ✅ | ❌ |
| Query-string routing | ✅ | ❌ |
| WebSocket | ✅ | ✅ |
| Static IP | ❌* | ✅ |
| Ultra-high performance | Good | **Excellent** |
| Very low latency | Good | **Excellent** |
| TCP applications | ❌ | ✅ |
| UDP applications | ❌ | ✅ |
| Microservices HTTP | **Excellent** | Possible |
| TLS termination | ✅ | ✅ |
- ALB uses AWS-managed IPs that can change; use **NLB** when static IP addresses are a key requirement.

---

# 20. When Should You Choose ALB?

### Choose ALB when you need:

```
HTTP / HTTPS
       +
Smart routing
```

Typical scenarios:

| Scenario | ALB? |
| --- | --- |
| HTTP web application | ✅ |
| HTTPS website | ✅ |
| Microservices | ✅ |
| ECS containers | ✅ |
| Path-based routing | ✅ |
| Host-based routing | ✅ |
| Query-string routing | ✅ |
| Header-based routing | ✅ |
| HTTP → HTTPS redirect | ✅ |
| Lambda HTTP target | ✅ |
| WebSocket | ✅ |

---

# 21. SAA-C03 Exam Decision Rule ⭐⭐⭐⭐⭐

Remember this:

```
HTTP/HTTPS
   +
Content-based routing
   ↓
  ALB
```

```
TCP / UDP
   +
High performance / static IP
   ↓
  NLB
```

```
Security appliance / inspection
   ↓
  GWLB
```

```
Old/legacy
   ↓
  CLB
```

### One-line memory

> **ALB = Layer 7 HTTP/HTTPS load balancer that routes requests intelligently using paths, hosts, headers, and query strings, making it ideal for modern web apps, microservices, ECS, and containers.**
>
