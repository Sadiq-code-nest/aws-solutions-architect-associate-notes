# AWS SAA-C03 Notes: Server Name Indication (SNI)

## What is SNI?

SNI (Server Name Indication) is a TLS/SSL extension that allows a client (browser) to tell the server which hostname it wants to connect to during the TLS handshake.

This enables a single Load Balancer or web server to host multiple HTTPS websites with different SSL certificates.

---

## Why Was SNI Needed?

### Problem Before SNI

Suppose one ALB hosts:

- shop.com
- blog.com
- api.company.com

Each domain has its own SSL certificate.

When a browser connects, the server must send a certificate immediately.

But how does the server know which certificate to send?

Without SNI, it does not know whether the user wants:

- shop.com
- blog.com
- api.company.com

Result:

- One IP address could effectively use only one SSL certificate.
- Companies often needed multiple servers or load balancers.

---

## How SNI Solves the Problem

Browser sends:

"I want shop.com"

during the TLS handshake.

The ALB reads the hostname and chooses:

- shop.com certificate

The HTTPS connection is then established successfully.

---

## Simple Example

Domains:

- shop.com
- blog.com
- customer.com

Certificates:

- Cert A → shop.com
- Cert B → blog.com
- Cert C → customer.com

Flow:

Browser → "I want blog.com"

ALB → "Use Blog Certificate"

HTTPS connection established.

---

## Real AWS Example

One ALB hosts:

- customer1.com
- customer2.com
- customer3.com

Each customer provides its own SSL certificate.

Instead of creating:

- ALB-1
- ALB-2
- ALB-3

AWS can use:

- One ALB
- Multiple certificates
- SNI

This reduces cost and complexity.

---

## SNI vs Wildcard Certificate

### Wildcard Certificate

Certificate:

- .example.com

Covers:

- api.example.com
- home.example.com
- blog.example.com

One certificate handles all subdomains.

Example:

api.example.com

home.example.com

about.example.com

Usually no need for multiple certificates.

---

### SNI

Used when multiple certificates exist.

Example:

- shop.com
- blog.net
- customer.org

Each domain has its own certificate.

SNI allows one ALB to choose the correct certificate.

---

## AWS Services Supporting SNI

Supported:

- Application Load Balancer (ALB)
- Network Load Balancer (NLB)
- CloudFront

Not Supported:

- Classic Load Balancer (CLB)

---

## Common Exam Keywords

If the question mentions:

- Multiple HTTPS websites
- Multiple SSL certificates
- One Load Balancer
- Minimize cost
- Different domains

Think:

✅ SNI

---

## Common Exam Trap

Question:

A company hosts:

- api.example.com
- blog.example.com
- shop.example.com

All are under the same domain.

Best solution?

Answer:

✅ Wildcard Certificate (*.example.com)

Not necessarily SNI.

---

## Common Exam Scenario

Question:

A company hosts:

- shop.com
- blog.com
- customer.com

Each requires its own SSL certificate.

The company wants one ALB.

Answer:

✅ Use SNI on ALB.

---

## Memory Trick

Wildcard Certificate:

Same family

example.com

api.example.com

blog.example.com

→ Use *.example.com

SNI:

Different families

shop.com

blog.net

customer.org

→ Use multiple certificates + SNI

---

## One-Line Exam Answer

Server Name Indication (SNI) is a TLS extension that allows a client to specify the target hostname during the SSL/TLS handshake, enabling a single ALB or server to present the correct SSL certificate for multiple HTTPS domains hosted on the same load balancer.
