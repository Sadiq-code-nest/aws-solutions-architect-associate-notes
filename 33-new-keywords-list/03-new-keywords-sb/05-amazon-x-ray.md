# Amazon X-Ray

## Amazon X-Ray — SAA-C03

**Definition:**

**Amazon X-Ray** is a **distributed tracing service** that helps you **trace and troubleshoot requests as they travel through multiple AWS services and application components**.

👉 **Main goal:** Find **where a request is slow, failing, or causing errors** in a distributed application.

### Simple idea

Imagine a request going through:

**User → API Gateway → Lambda → DynamoDB → SNS → another Lambda**

Without X-Ray, it can be difficult to know **which component caused the problem**.

With X-Ray:

**User Request → API Gateway → Lambda → DynamoDB**

↓

**Trace + timing + errors**

You can see **how long each component took** and where the failure occurred.

---

### When to use it

Use **Amazon X-Ray** when:

- You have a **distributed application**
- Multiple AWS services are involved
- You need to **trace requests end-to-end**
- You need to identify **latency bottlenecks**
- You need to troubleshoot **application errors**
- You want to understand dependencies between services
- You're running **microservices/serverless applications**

### Example Scenario

> A company has a serverless application using **API Gateway → Lambda → DynamoDB**. Users report that requests are sometimes very slow.
> 
> 
> The Solutions Architect needs to determine **which service is causing the latency**.
> 

✅ **Use Amazon X-Ray** to trace requests across API Gateway, Lambda, and DynamoDB and identify the slow component.

---

### What X-Ray helps you see

X-Ray can help identify:

- ⏱️ **Latency** — Which service is taking too long?
- ❌ **Errors** — Where is the request failing?
- 🔗 **Service dependencies** — Which services communicate with each other?
- 📊 **Request flow** — How does a request travel through the application?

---

### SAA-C03 Exam Shortcut

| Question says... | Think |
| --- | --- |
| Trace requests across multiple services | **X-Ray** |
| Find latency bottleneck | **X-Ray** |
| Troubleshoot distributed application | **X-Ray** |
| Trace API Gateway → Lambda → DynamoDB | **X-Ray** |
| Identify where an application request failed | **X-Ray** |
| Visualize service dependencies | **X-Ray** |
| Monitor AWS resource metrics | **CloudWatch** |
| Collect API/activity audit logs | **CloudTrail** |

### ⚠️ Important Trap

**X-Ray ≠ CloudWatch ≠ CloudTrail**

- **X-Ray** → **Trace a request** through your application 🔗
- **CloudWatch** → **Monitor metrics, logs, alarms** 📊
- **CloudTrail** → **Record AWS API/user activity** 📝

### Memory Trick

> **X-Ray = “Show me the path of this request and where it hurts.”** 🔍
> 

**Typical SAA-C03 trigger:**

> *“The application consists of multiple microservices/serverless components, and the team needs to identify the source of latency or errors.”*
> 

➡️ **Amazon X-Ray**
