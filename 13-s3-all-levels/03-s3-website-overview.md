# Amazon S3 Static Website Hosting — SAA-C03

- IMAGES
    
    
    
    
    
    

## 1. What is S3 Static Website Hosting?

Amazon S3 can host a **static website** directly from an S3 bucket.

**Static website** means the website consists mainly of files such as:

- HTML
- CSS
- JavaScript
- Images
- Videos
- Other static assets

Example:

```
User Browser
     │
     │ HTTP
     ▼
┌───────────────────┐
│   S3 Bucket       │
│                   │
│ index.html        │
│ style.css         │
│ app.js            │
│ logo.png          │
└───────────────────┘
```

The S3 bucket serves those files to users over the Internet.

---

# 2. Real-World Example

Imagine you build a simple portfolio website:

```
myportfolio/
├── index.html
├── about.html
├── style.css
├── script.js
└── images/
    └── profile.png
```

Upload everything to:

```
s3://my-portfolio-website/
```

Then enable **Static Website Hosting**.

Users can access the website using the S3 website endpoint.

---

# 3. S3 Website Endpoint

The website endpoint depends on the AWS Region.

Conceptually:

```
http://bucket-name.s3-website-<region>.amazonaws.com
```

For example:

```
http://my-portfolio.s3-website-us-east-1.amazonaws.com
```

⚠️ **For SAA-C03, remember the concept rather than memorizing the exact hostname format.**

---

# 4. What does S3 actually do?

S3 is simply serving the website's static files.

```
                   Internet
                      │
                      ▼
                 S3 Website
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
       HTML          CSS        JavaScript
```

S3 doesn't run server-side application code.

### S3 is good for:

✅ HTML

✅ CSS

✅ JavaScript

✅ Images

✅ Videos

✅ Static assets

### S3 is NOT designed to directly run:

❌ PHP

❌ Python backend

❌ Node.js backend

❌ Java application

❌ Database-driven server-side logic

For dynamic backend functionality, you would typically use services such as **Lambda, API Gateway, EC2**, etc.

---

# 5. Important: Bucket Policy

This is one of the biggest SAA-C03 exam points.

If users need to access the website publicly, the objects need appropriate public-read access.

A bucket policy can allow:

```
Principal: *
Action: s3:GetObject
Resource: bucket objects
Effect: Allow
```

Conceptually:

```
Internet User
      │
      │ GET /index.html
      ▼
┌──────────────────┐
│ S3 Bucket        │
│                  │
│ Bucket Policy    │
│ Allow GetObject  │
│ to public users  │
└──────────────────┘
```

---

# 6. What if you get `403 Forbidden`?

A very common exam/troubleshooting clue:

```
Website
   ↓
403 Forbidden
```

Check:

### 1️⃣ Bucket Policy

Does it allow the required public `s3:GetObject` access?

### 2️⃣ Block Public Access

If **S3 Block Public Access** prevents public access, your public website configuration may not work.

### 3️⃣ Object permissions

Make sure the requested objects can actually be read.

### 🧠 Exam memory

> **S3 Website + 403 → Think permissions / Bucket Policy / Block Public Access.**
> 

---

# 7. Static Website vs Normal S3 Object URL

Don't confuse these two.

|  | S3 Object URL | S3 Website Endpoint |
| --- | --- | --- |
| Purpose | Access S3 objects | Host a website |
| Website behavior | ❌ | ✅ |
| Static website hosting | Not required | Required |
| Public access | Depends on permissions | Usually needs public access |
| Common use | Download/read object | Serve website |
| HTTPS | S3 supports HTTPS object endpoints | **S3 website endpoints are HTTP-only** |

🔥 **Important SAA-C03 clue:**

If the requirement says:

> **“Static website must be accessible over HTTPS.”**
> 

Don't simply choose the S3 website endpoint.

A common architecture is:

```
                  HTTPS
                    │
                    ▼
             ┌──────────────┐
             │ CloudFront   │
             │ Distribution │
             └──────┬───────┘
                    │
                    ▼
             ┌──────────────┐
             │ S3 Bucket    │
             │ Static Site  │
             └──────────────┘
```

**CloudFront** provides HTTPS, caching, global edge delivery, and lower latency.

---

# 8. S3 + CloudFront — Production Architecture

For a real production website, you will often see:

```
                 User
                  │
               HTTPS
                  │
                  ▼
        ┌──────────────────┐
        │   CloudFront     │
        │  Edge Locations  │
        └────────┬─────────┘
                 │
                 ▼
        ┌──────────────────┐
        │       S3         │
        │ Static Website   │
        └──────────────────┘
```

### Why CloudFront?

- 🌍 Global distribution
- ⚡ Lower latency
- 🔒 HTTPS
- 💾 Caching
- 🛡️ Can integrate with AWS WAF
- 📉 Reduces repeated requests reaching S3

---

# 9. S3 Static Website vs Dynamic Website

| Requirement | Best approach |
| --- | --- |
| Simple HTML website | **S3** |
| Static React/Vue/Angular frontend | **S3 + CloudFront** |
| Need HTTPS + global delivery | **CloudFront + S3** |
| Need backend API | **API Gateway + Lambda** or other compute |
| Need database | Add appropriate database service |
| Server-side application | EC2 / ECS / Lambda etc. |

---

# 10. SAA-C03 Scenario

### Question

> A company wants to host a simple static corporate website containing HTML, CSS, JavaScript, and images. The website should be highly scalable and require minimal administration.
> 

### Think:

```
Static website
       ↓
Amazon S3
```

If the question additionally says:

> **“Users around the world need low-latency HTTPS access.”**
> 

Think:

```
CloudFront
    ↓
S3
```

---

## 🔥 SAA-C03 Exam Takeaway

> **S3 can host static websites. Public website access requires appropriate S3 permissions, and a 403 commonly points to access configuration. For production HTTPS/global caching, think CloudFront + S3.**
> 

### 🧠 Memory Trick

**S3 = Static Storage → Static Website**

**S3 + CloudFront = Fast + HTTPS + Global Static Website**
