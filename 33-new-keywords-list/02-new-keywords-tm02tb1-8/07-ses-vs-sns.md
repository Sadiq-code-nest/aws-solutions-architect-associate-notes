# Amazon SES vs SNS — Key Difference

### 📧 Amazon SES — Email Service

**SES = Simple Email Service**

Used when your application needs to **send or receive emails**.

Example:

```
E-commerce App
     ↓
Amazon SES
     ↓
📧 customer@example.com
"Your order has shipped"
```

**Think:**

> **SES = Email**
> 

Use for:

- Transactional emails
- Verification emails
- Password reset emails
- Marketing emails

---

### 📢 Amazon SNS — Notification/Messaging Service

**SNS = Simple Notification Service**

Used to **send notifications/messages to multiple subscribers or applications**.

Example:

```
Order System
     ↓
    SNS
   / | \
  /  |  \
SQS Lambda Email
```

SNS can deliver messages to endpoints such as **SQS, Lambda, HTTP/S, mobile push, and email/SMS**.

**Think:**

> **SNS = Publish/Subscribe notifications**
> 

---

## 🔥 Major Difference

|  | **SES** | **SNS** |
| --- | --- | --- |
| Main purpose | 📧 Email | 📢 Notifications/messaging |
| Focus | Email delivery | Pub/Sub messaging |
| Typical use | Send customer emails | Notify multiple systems |
| Example | "Your order shipped" email | "Order created" event |
| Can integrate with | Email workflows | SQS, Lambda, HTTP/S, mobile, etc. |

### 🧠 SAA-C03 shortcut

> **Need to send an email → SES**
> 

> **Need to broadcast/publish a message to multiple subscribers → SNS**
> 

**Easy memory:**

**SES = Send Email Service** 📧

**SNS = Send Notification Service** 📢
