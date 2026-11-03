# Amazon S3 CORS (Cross-Origin Resource Sharing) — Complete Crystal Clear Notes (SAA-C03)

This is one of the **most common AWS SAA exam topics** because many AWS services (S3, API Gateway, CloudFront, Lambda) interact with browsers.

The biggest mistake students make is thinking:

> **"CORS is an AWS feature."**
> 

❌ Wrong.

**CORS is a Web Browser security feature.**

AWS simply provides headers that the browser checks.

---

# 1. First Understand "Origin"

Everything starts with **Origin**.

An Origin consists of:

```
Origin = Protocol (Scheme)
       + Domain (Host)
       + Port
```

Example

```
https://www.example.com
```

Means

```
Protocol = https

Host = www.example.com

Port = 443 (default)
```

Together these three create ONE origin.

---

# Visual

```
https://www.example.com:443

│        │            │

│        │            │

HTTPS   DOMAIN      PORT

↓

ONE ORIGIN
```

---

# 2. When Are Two URLs the Same Origin?

All **three parts must match.**

### Example 1

```
https://example.com/app1

https://example.com/app2
```

Protocol:

```
HTTPS
```

Same

Domain:

```
example.com
```

Same

Port:

```
443
```

Same

Result

✅ Same Origin

---

Example 2

```
http://example.com

https://example.com
```

Protocol

```
HTTP

HTTPS
```

Different

Result

❌ Different Origin

---

Example 3

```
https://example.com

https://api.example.com
```

Domain

Different

Result

❌ Different Origin

---

Example 4

```
https://example.com

https://example.com:8080
```

Port

Different

Result

❌ Different Origin

---

# Easy Memory Table

| URL 1 | URL 2 | Same Origin? | Why |
| --- | --- | --- | --- |
| [https://example.com](https://example.com/) | [https://example.com/app](https://example.com/app) | ✅ Yes | Same protocol, host, port |
| [https://example.com](https://example.com/) | [http://example.com](http://example.com/) | ❌ No | Different protocol |
| [https://example.com](https://example.com/) | [https://api.example.com](https://api.example.com/) | ❌ No | Different host |
| [https://example.com](https://example.com/) | [https://example.com:8080](https://example.com:8080/) | ❌ No | Different port |

---

# 3. Why Does CORS Exist?

Imagine you're logged into your bank.

```
https://bank.com
```

Now you open a malicious website.

```
https://hacker.com
```

Without CORS,

the hacker's website could secretly call

```
https://bank.com
```

and read your banking information.

That would be a huge security problem.

Browsers therefore enforce the **Same-Origin Policy (SOP)**.

---

# Same-Origin Policy (SOP)

Default browser rule:

> A webpage can freely access resources only from the **same origin**.
> 

Example

```
Browser

↓

https://example.com

↓

Can call

https://example.com/api

✅ Allowed
```

---

But

```
Browser

↓

https://example.com

↓

Call

https://other.com

❌ Blocked
```

Unless **other.com** explicitly says:

> "I allow example.com."
> 

That permission mechanism is called **CORS**.

---

# What is CORS?

CORS stands for:

**Cross-Origin Resource Sharing**

Definition:

> A browser security mechanism that allows one origin to access resources from another origin **only if the destination server explicitly allows it** using CORS response headers.
> 

Notice:

**The browser enforces CORS.**

Not S3.

Not EC2.

Not the server.

The server simply sends permission headers.

---

# Real-Life Analogy

Imagine two offices.

Office A

```
Google
```

Office B

```
Microsoft
```

An employee from Google wants to enter Microsoft.

Microsoft security asks:

```
Do we allow Google employees?
```

If YES

Door opens.

If NO

Door stays closed.

That's exactly CORS.

---

# How CORS Works

Suppose

Website

```
https://www.example.com
```

needs an image from

```
https://www.other.com
```

---

Step 1

Browser loads

```
example.com
```

---

Step 2

HTML contains

```
<img src="https://www.other.com/image.jpg">
```

Browser wants the image.

---

Step 3

Browser sends request

```
GET image.jpg

Origin:

https://www.example.com
```

Notice the **Origin** header.

---

Step 4

Other server responds

```
Access-Control-Allow-Origin:

https://www.example.com
```

Browser checks:

```
Does my origin match?

YES
```

Browser allows JavaScript to access the response.

---

# If Server Does NOT Allow

Browser sends

```
Origin:

https://example.com
```

Server replies

```
(no CORS header)
```

Browser immediately blocks the response.

Server may actually return the data, but the browser refuses to expose it to the webpage.

---

# Diagram

```
example.com

↓

Browser

↓

GET image

Origin:

example.com

↓

other.com

↓

Access-Control-Allow-Origin:

example.com

↓

Browser

↓

Allowed
```

---

# If Not Allowed

```
Browser

↓

Origin

example.com

↓

other.com

↓

No CORS Header

↓

Browser

↓

Blocked
```

---

# Understanding the Image You Uploaded

Your diagram shows a **Preflight Request**.

Many students get confused here.

There are actually **two types of CORS requests**.

---

# Type 1 — Simple Request

Example:

```
GET image
```

Browser directly sends:

```
GET

Origin:

https://example.com
```

Server replies

```
Access-Control-Allow-Origin:

https://example.com
```

Done.

No extra request.

---

# Type 2 — Preflight Request

If the request is more "dangerous," such as:

- PUT
- DELETE
- PATCH
- Custom headers
- Some non-simple POST requests

The browser first asks for permission.

```
OPTIONS
```

This is called the **Preflight Request**.

---

# Why Preflight?

Imagine deleting a file.

Browser asks:

```
Can I send DELETE?
```

Server replies

```
Allowed
```

Then browser sends

```
DELETE
```

---

# Flow

```
Browser

↓

OPTIONS

"Can I use DELETE?"

↓

Server

↓

YES

↓

Browser

↓

DELETE Request

↓

Server

↓

Success
```

---

# Understanding the Headers in Your Image

### Browser sends

```
OPTIONS /

Host:

www.other.com

Origin:

https://www.example.com
```

Browser is asking:

> Can example.com access you?
> 

---

CORSServer replies

```
Access-Control-Allow-Origin:

https://www.example.com
```

Meaning

```
YES

I trust example.com
```

---

Server also replies

```
Access-Control-Allow-Methods:

GET

PUT

DELETE
```

Meaning

```
I allow these HTTP methods.
```

---

Now browser sends

```
GET

or

DELETE

or

PUT
```

---

# CORS with Amazon S3

This is the favorite AWS exam scenario.

Imagine two buckets.

Bucket 1

```
my-bucket-html
```

Stores

```
index.html
```

Bucket 2

```
my-bucket-assets
```

Stores

```
coffee.jpg
```

---

Browser opens

```
index.html
```

Inside HTML

```html
<img src="coffee.jpg">
```

But

```
coffee.jpg

is

another bucket
```

Different bucket = different origin.

Browser checks CORS.

---

Without CORS

```
Browser

↓

Image Request

↓

S3

↓

Blocked
```

---

With CORS enabled

Bucket Assets

adds

```
Access-Control-Allow-Origin:

http://my-bucket-html.s3-website.us-west-2.amazonaws.com
```

Browser now accepts the response.

---

# AWS S3 CORS Configuration

Example

```json
[
  {
    "AllowedOrigins": [
      "https://www.example.com"
    ],
    "AllowedMethods": [
      "GET"
    ],
    "AllowedHeaders": [
      "*"
    ]
  }
]
```

Meaning:

Only

```
https://www.example.com
```

can request objects.

---

Allow Everyone

```json
AllowedOrigins

*
```

Means

```
All websites
```

can access.

Good for public assets.

Not recommended for sensitive data.

---

# Real Company Scenario 1

Frontend

```
React App

↓

https://app.company.com
```

Backend Images

```
https://images.company.com
```

Different origin.

Need

CORS

---

# Real Company Scenario 2

Frontend

Hosted on S3

```
frontend-bucket
```

Images

Hosted on

```
assets-bucket
```

Need

S3 CORS Configuration.

---

# Real Company Scenario 3

Website

```
https://shop.com
```

Calls API

```
https://api.shop.com
```

Different subdomain = different origin.

Need CORS.

---

# When Do You Need CORS?

✅ Browser calling another origin

Examples:

- S3 Website → Another S3 Bucket
- React → API Gateway
- Angular → Lambda URL
- JavaScript → EC2 API
- Website → CloudFront (different origin)

---

# When Is CORS NOT Needed?

Backend server

↓

Calls another server

Example

```
EC2

↓

API
```

No browser involved.

No CORS.

Similarly:

- Lambda → DynamoDB
- EC2 → S3 (AWS SDK)
- Lambda → SQS

These are server-to-server communications, so CORS does not apply.

---

# Common Exam Scenarios

### Scenario 1

Static website in S3.

Images in another S3 bucket.

Images not loading.

Answer:

✅ Configure **CORS** on the image bucket.

---

### Scenario 2

React app calls API Gateway.

Browser shows

```
CORS Error
```

Answer:

Enable CORS on API Gateway (or backend).

---

### Scenario 3

Java application on EC2 accesses S3.

Need CORS?

❌ No.

Because it's not a browser.

---

### Scenario 4

Website

```
https://example.com
```

calls

```
https://example.com/api
```

Need CORS?

❌ No.

Same origin.

---

# SAA-C03 Exam Cheat Sheet

| Question | Answer |
| --- | --- |
| What is CORS? | Browser security mechanism for cross-origin requests |
| Who enforces CORS? | **The browser** |
| What defines an origin? | Protocol + Host + Port |
| Same origin? | All three must match |
| Different origin? | If protocol, host, or port differs |
| Permission header | `Access-Control-Allow-Origin` |
| Allow all websites | `*` |
| S3 static website accessing another bucket | Configure CORS on the target bucket |
| Server-to-server communication | CORS is **not** required |
| OPTIONS request | Preflight request used before certain cross-origin requests |

---

# 🎯 Ultimate Memory Trick

Think of **CORS as a security guard at the destination server's building**.

- The **browser** is the visitor.
- The **Origin** header is the visitor's ID card ("I came from `https://app.example.com`").
- The destination server responds with **`Access-Control-Allow-Origin`**, which is like a guest list.

If your website is **on the guest list**, the browser lets your JavaScript use the response.

If it's **not on the guest list**, the browser blocks access—even if the server sent back the data.

**Remember this for the exam:**

> **CORS does not stop the request from reaching the server. It stops the browser from allowing JavaScript to access the response unless the server explicitly permits that origin.** This is the key concept that AWS SAA questions often test.
>
