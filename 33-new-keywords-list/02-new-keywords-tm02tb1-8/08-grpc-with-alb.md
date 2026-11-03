# gRPC with ALB

## gRPC — Short AWS SAA-C03 Understanding

**gRPC (Remote Procedure Call)** is a communication protocol that allows **one application/service to call a function on another service as if it were a local function**.

Think:

> **gRPC = Service-to-service communication, commonly used in microservices.**
> 

### Simple example

Suppose you have:

```
Order Service
      │
      │ gRPC: GetCustomer()
      ▼
Customer Service
```

The Order Service can call:

```
GetCustomer(customer_id)
```

and the Customer Service processes it and returns the result.

Instead of manually building HTTP/JSON APIs, gRPC commonly uses **Protocol Buffers (Protobuf)** to define services and messages.

---

# How does gRPC connect with an ALB?

**Application Load Balancer (ALB) supports gRPC traffic.**

You can have:

```
gRPC Client
     │
     │ HTTP/2 + gRPC
     ▼
    ALB
     │
     ├──────────┐
     ▼          ▼
gRPC Server  gRPC Server
 EC2          EC2
```

The ALB acts as the **load balancer** in front of your gRPC services.

### What does ALB do?

A client sends a gRPC request:

```
Client
   ↓
ALB
   ↓
Healthy gRPC server
```

If you have multiple servers:

```
             ALB
          /    |    \
         ↓     ↓     ↓
       EC2-1 EC2-2 EC2-3
```

ALB distributes requests among the healthy targets.

---

## Example scenario

Imagine an online shopping application:

```
Frontend
   ↓
Order Service
   ↓ gRPC
ALB
   ↓
Customer Service
 ┌──────┬──────┐
 EC2    EC2    EC2
```

The Order Service needs customer information.

It sends a gRPC request through the ALB:

```
GetCustomer(12345)
```

ALB routes the request to a healthy Customer Service instance.

---

## Why use gRPC + ALB?

gRPC is particularly useful for:

- **Microservices communication**
- High-performance service-to-service communication
- Efficient binary serialization
- HTTP/2-based communication
- Strongly typed APIs

ALB gives you:

- Load balancing
- Health checks
- High availability
- Routing to multiple gRPC servers

---

### 🧠 SAA-C03 shortcut

If you see:

> **"gRPC application needs a load balancer"**
> 

Think:

# ✅ Application Load Balancer (ALB)

Because **ALB supports HTTP/2 and gRPC**, making it suitable for routing gRPC requests to backend services.

**Memory trick:**

> **gRPC = how services communicate**
> 
> 
> **ALB = distributes those gRPC requests to healthy services**
>
