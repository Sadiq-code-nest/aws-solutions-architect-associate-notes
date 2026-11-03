# Lambda7 (Master Table)

Absolutely. For SAA-C03, the easiest way to recognize Lambda is to look at the **trigger → Lambda → action** pattern.

| Scenario / Requirement | Trigger | Lambda does | Why Lambda fits |
| --- | --- | --- | --- |
| 🚀 **Sudden API traffic** | API Gateway | Process API request | Automatically scales for bursts |
| 📸 **User uploads image** | S3 | Resize/thumbnail the image | Event-driven; no server needed |
| 📄 **File uploaded** | S3 | Validate, transform, or process file | Runs only when needed |
| 📧 **Send email after signup** | API Gateway / EventBridge | Trigger email workflow | Short-lived task |
| 🛒 **Order placed** | API Gateway / EventBridge | Process order, update DB | Event-driven processing |
| 🔔 **Send notification** | SNS/EventBridge | Send/process notification | No continuously running server |
| 🗃️ **DynamoDB item changed** | DynamoDB Streams | Process the change | Real-time event processing |
| 📦 **Queue messages** | SQS | Process messages | Automatically handles varying message volume |
| 📊 **Process streaming data** | Kinesis | Process records | Scales with event processing |
| ⏰ **Run something every night** | EventBridge Scheduler | Execute cleanup/report job | No EC2 running 24/7 |
| 🧹 **Delete old resources** | EventBridge | Run cleanup logic | Small periodic workload |
| 💰 **Cost monitoring action** | EventBridge / CloudWatch | Take automated action | Event-driven automation |
| 🔐 **Process authentication request** | API Gateway | Validate/process request | Serverless backend |
| 🌐 **Mobile app backend** | API Gateway | Execute application logic | Good for unpredictable API traffic |
| 🖼️ **Generate image metadata** | S3 | Extract/process metadata | Runs only when files arrive |
| 🔍 **Run security check on uploaded file** | S3 | Inspect/process file | Event-driven |
| 📝 **Process logs** | S3 / EventBridge | Transform/analyze logs | No permanent processing server |
| 🏦 **Bank transaction event** | EventBridge/SQS | Validate/process transaction | Scales with event volume |
| 🌡️ **IoT sensor data** | IoT Core | Process sensor event | Massive unpredictable event volume |
| 🛠️ **Automated AWS operation** | EventBridge | Start/stop/tag resources | Serverless automation |

## The patterns you should memorize

### 1. API → Lambda

```
User
 ↓
API Gateway
 ↓
Lambda
 ↓
DynamoDB / RDS
```

**Think:** Serverless API + unpredictable traffic.

---

### 2. S3 → Lambda

```
File uploaded
      ↓
     S3
      ↓
   Lambda
      ↓
Process file
```

**Think:** "When something is uploaded, do something automatically."

---

### 3. SQS → Lambda

```
Applications
     ↓
    SQS
     ↓
  Lambda
     ↓
Process message
```

**Think:** "Process messages from a queue."

---

### 4. DynamoDB Streams → Lambda

```
DynamoDB change
      ↓
DynamoDB Streams
      ↓
Lambda
      ↓
Take action
```

**Think:** "When database data changes, automatically react."

---

### 5. EventBridge → Lambda

```
Event / Schedule
      ↓
EventBridge
      ↓
Lambda
      ↓
Perform action
```

**Think:** "Run something because an event happened or at a scheduled time."

---

### 6. IoT → Lambda

```
IoT devices
     ↓
IoT Core
     ↓
Lambda
     ↓
Process data
```

**Think:** "Millions of devices generate unpredictable events."

---

# ⭐ SAA-C03 recognition cheat sheet

When the question says:

| Question wording | Your brain should think |
| --- | --- |
| **API with unpredictable traffic** | API Gateway + Lambda |
| **Sudden traffic burst** | Lambda |
| **No server management** | Lambda |
| **Event-driven application** | Lambda |
| **Run code when S3 object arrives** | S3 + Lambda |
| **Process messages from SQS** | SQS + Lambda |
| **React to DynamoDB changes** | DynamoDB Streams + Lambda |
| **Run code on a schedule** | EventBridge + Lambda |
| **Process IoT events** | IoT Core + Lambda |
| **Short-lived computation** | Lambda |
| **Pay only when code runs** | Lambda |
| **Highly variable workload** | Lambda |
| **Need thousands of concurrent executions** | Lambda *(subject to concurrency quotas)* |
| **Need servers running continuously** | EC2/ECS may be better |
| **Need full OS/server control** | EC2 |
| **Long-running container application** | ECS/Fargate |

### The biggest mental shortcut

Whenever you see:

> **"Something happens → automatically run some code"**
> 

your first thought should be:

**Lambda.**

Examples:

```
S3 upload       → Lambda
API request     → Lambda
SQS message     → Lambda
DB change       → Lambda
IoT event       → Lambda
Schedule        → Lambda
SNS event       → Lambda
EventBridge     → Lambda
```

That's the **event-driven/serverless Lambda pattern** you want to recognize in SAA-C03 questions.
