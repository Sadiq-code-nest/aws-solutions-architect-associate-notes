# AWS X-Ray

**X-Ray** helps you **trace requests as they travel through your distributed application**.

Example:

```
User
 ↓
API Gateway
 ↓
Lambda
 ↓
DynamoDB
```

X-Ray can show:

> "The request spent 20ms in API Gateway, 500ms in Lambda, and 2 seconds waiting on DynamoDB."
> 

**Think:**

👉 **X-Ray = Trace a request across services**

**Exam clue:** Distributed application, microservices, request tracing, latency troubleshooting.
