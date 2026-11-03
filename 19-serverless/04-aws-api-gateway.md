# AWS API Gateway (AWS SAA-C03) – Beginner Friendly Complete Notes

Imagine you build a **restaurant**.

Customers should **not** walk directly into the kitchen.

Instead,

They first go to the **reception counter**.

The receptionist:

- Takes the order
- Checks payment
- Verifies customer
- Controls the queue
- Sends the order to the kitchen

API Gateway works exactly like this.

```
Customer

↓

API Gateway

↓

Lambda

↓

Database
```

---

# What is API Gateway?

Amazon API Gateway is a **fully managed service** that allows clients (websites, mobile apps, or other applications) to securely call your backend services such as Lambda, EC2, ECS, or other AWS services.

Think of it as:

> **The front door of your backend application.**
> 

---

# Why Do We Need API Gateway?

Suppose you have a Lambda function.

Without API Gateway:

```
User

↓

Lambda
```

How does the user call Lambda?

- No URL
- No authentication
- No request validation
- No throttling
- No version control

It becomes difficult to expose Lambda safely over the internet.

---

API Gateway solves this.

```
User

↓

API Gateway

↓

Lambda
```

Now AWS provides:

- URL endpoint
- Authentication
- Security
- Rate limiting
- Logging
- Monitoring
- API versioning
- Request validation

---

# Real-Life Analogy

Imagine a bank.

Without a receptionist,

everyone enters the manager's office directly.

Chaos!

```
Customers

↓

Manager
```

With a receptionist,

```
Customers

↓

Reception Desk

↓

Manager
```

Much more organized.

API Gateway is the **reception desk**.

---

# Problems API Gateway Solves

## 1. Exposes Backend Services

Without API Gateway:

```
Lambda

(No public API)
```

With API Gateway:

```
Internet

↓

https://api.example.com

↓

Lambda
```

Now anyone with permission can use your API.

---

## 2. Authentication & Authorization

Suppose only logged-in users should access your API.

API Gateway checks identity before forwarding the request.

```
User

↓

API Gateway

↓

Check Login

↓

Lambda
```

Lambda doesn't need to perform this check every time.

---

## 3. Request Throttling

Suppose a hacker sends

```
100,000 Requests/Second
```

Without protection,

Lambda could become overwhelmed.

API Gateway can limit requests.

Example:

```
Maximum

100 Requests/Second
```

Extra requests are rejected.

This protects your backend.

---

## 4. API Versioning

Suppose you improve your API.

Old:

```
/v1/users
```

New:

```
/v2/users
```

Both versions can exist simultaneously.

Old mobile apps continue working.

---

## 5. Multiple Environments

Usually applications have:

```
Development

Testing

Production
```

API Gateway can expose separate endpoints for each stage.

```
dev.api.com

test.api.com

api.com
```

---

## 6. Request Validation

Suppose your API expects:

```
Name

Age

Email
```

A user sends only

```
Name
```

API Gateway rejects the request before Lambda runs.

This saves compute time and money.

---

## 7. Response Transformation

Backend returns:

```
{
"id":1,
"name":"John"
}
```

API Gateway can transform the response into another format before sending it to the client.

---

## 8. API Caching

Suppose 10,000 users request:

```
GET /products
```

The product list rarely changes.

Without cache:

```
Every Request

↓

Lambda

↓

Database
```

Very expensive.

With cache:

```
First Request

↓

Lambda

↓

Cache

↓

Future Requests

↓

Cache
```

Much faster and cheaper.

---

# Building a Serverless API

This is the most common architecture in AWS.

```
User

↓

API Gateway

↓

Lambda

↓

DynamoDB
```

No servers to manage.

AWS manages everything.

This is why it's called **Serverless**.

---

# API Gateway Integrations

API Gateway can connect to different backend services.

---

## 1. Lambda Integration ⭐⭐⭐⭐⭐

Most common.

```
User

↓

API Gateway

↓

Lambda
```

Use when:

- Serverless applications
- REST APIs
- Mobile backends

---

## 2. HTTP Integration

Instead of Lambda,

API Gateway can call an existing HTTP server.

```
User

↓

API Gateway

↓

Application Load Balancer

↓

EC2
```

or

```
User

↓

API Gateway

↓

On-Premises HTTP Server
```

Why use API Gateway?

Even if you already have a web server, you still get:

- Authentication
- API Keys
- Throttling
- Caching
- Monitoring

---

## 3. AWS Service Integration

API Gateway can call AWS services directly.

Example:

```
User

↓

API Gateway

↓

SQS
```

No Lambda needed.

Other examples:

- Step Functions
- EventBridge
- Kinesis Data Streams
- SNS

This reduces cost and simplifies the architecture.

---

# Example: Kinesis Integration

```
Application

↓

API Gateway

↓

Kinesis Data Streams
```

API Gateway receives data and directly places it into the stream.

---

# API Gateway Endpoint Types

AWS provides three endpoint types.

---

## 1. Edge-Optimized (Default)

Best for users around the world.

```
Japan User

↓

CloudFront Edge

↓

API Gateway (US-East)
```

CloudFront improves latency.

Remember:

The API itself still runs in **one AWS Region**.

---

## Use Case

Global public APIs.

Example:

- International website
- Mobile application
- Worldwide customers

---

## 2. Regional

Only intended for users in the same AWS Region.

```
Bangladesh User

↓

API Gateway (Singapore)
```

No CloudFront is added automatically.

If needed,

you can place CloudFront in front of it yourself for more control.

---

## Use Case

Applications serving users mainly in one region.

---

## 3. Private

Not accessible from the internet.

Only resources inside your VPC can access it.

```
EC2

↓

VPC Endpoint (ENI)

↓

Private API Gateway
```

Perfect for internal company APIs.

---

# Easy Memory Trick

### Edge

```
Worldwide
```

---

### Regional

```
One Region
```

---

### Private

```
Inside VPC Only
```

---

# API Gateway Security

Several authentication methods are available.

---

## IAM Authentication

Used for AWS users or applications.

Example:

```
Developer

↓

AWS Credentials

↓

API Gateway
```

Common for internal applications.

---

## Amazon Cognito

For external users.

Example:

```
Mobile User

↓

Login

↓

Cognito

↓

API Gateway
```

Ideal for:

- Mobile apps
- Customer portals
- Websites

---

## Lambda Authorizer (Custom Authorizer)

You write your own authentication logic.

Example:

```
User

↓

API Gateway

↓

Lambda Authorizer

↓

Allow or Deny
```

Useful for custom tokens or proprietary authentication systems.

---

# Custom Domain Names

Instead of:

```
https://abc123.execute-api.amazonaws.com
```

Use:

```
https://api.example.com
```

Looks professional.

---

# HTTPS Certificates

API Gateway uses

**AWS Certificate Manager (ACM)**

to manage SSL/TLS certificates.

Exam point:

- **Edge-Optimized API** → Certificate must be in **us-east-1**.
- **Regional API** → Certificate must be in the **same Region** as the API Gateway.

---

# Route 53

After creating a custom domain,

create a DNS record.

Usually:

- CNAME
- Alias (A record)

Example:

```
api.example.com

↓

API Gateway
```

---

# Complete Architecture

```
Internet Users

↓

API Gateway

↓

Lambda

↓

DynamoDB
```

---

Another example:

```
Users

↓

API Gateway

↓

SQS
```

No Lambda required.

---

Another:

```
Users

↓

API Gateway

↓

Application Load Balancer

↓

EC2
```

---

# AWS SAA-C03 Cheat Sheet

| Topic | Remember |
| --- | --- |
| **API Gateway** | Front door for backend services and APIs |
| **Main Purpose** | Securely expose backend services to clients |
| **Common Integration** | Lambda |
| **Other Integrations** | HTTP endpoints, SQS, SNS, Step Functions, Kinesis, EventBridge, and other AWS services |
| **Serverless API** | API Gateway → Lambda → DynamoDB |
| **Authentication** | IAM, Cognito, Lambda Authorizer |
| **Versioning** | `/v1`, `/v2` APIs |
| **Stages** | Development, Test, Production |
| **Throttling** | Protects backend from excessive requests |
| **Caching** | Reduces backend load and improves performance |
| **Edge-Optimized** | Best for global users; uses CloudFront edge locations |
| **Regional** | Best for users in one region |
| **Private** | Accessible only from within a VPC through an Interface VPC Endpoint |
| **HTTPS** | Uses AWS Certificate Manager (ACM) |
| **Custom Domain** | Requires Route 53 DNS configuration (or another DNS provider) |

---

# Easy Memory Tricks

- 🚪 **API Gateway** = Reception desk / front door of your application.
- 🔐 **Authentication** = Security guard checking who can enter.
- 🚦 **Throttling** = Traffic police controlling request flow.
- 💾 **Caching** = Waiter remembers the last order instead of asking the kitchen again.
- 🌍 **Edge-Optimized** = Global customers.
- 📍 **Regional** = Local customers.
- 🏢 **Private** = Employees inside the company network only.

---

## Exam Decision Tree

When you see a question, think like this:

- **Need to expose a Lambda as a REST API?** → **API Gateway + Lambda**
- **Need authentication, API keys, throttling, or caching?** → **API Gateway**
- **Global public API?** → **Edge-Optimized endpoint**
- **Users mostly in one AWS Region?** → **Regional endpoint**
- **Internal API accessible only from a VPC?** → **Private endpoint**
- **Need to call an AWS service like SQS or Step Functions directly?** → **API Gateway AWS Service Integration**
