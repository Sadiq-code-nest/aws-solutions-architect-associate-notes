# Amazon S3 Access Logs (SAA-C03) — Complete Crystal Clear Notes

This is an easy exam topic if you understand **why S3 Access Logs exist**.

The purpose is simple:

> **"Who accessed my S3 bucket? When? What did they do? Was it allowed or denied?"**
> 

---

# 1. What are S3 Access Logs?

S3 Access Logs record **every request made to an S3 bucket**.

Think of it like a **CCTV camera** for your bucket.

Every time someone accesses your bucket, AWS writes a log.

---

## Real-Life Example

Imagine your house has CCTV.

Whenever someone comes:

```
10:00 AM

John entered
```

```
10:05 AM

Delivery person arrived
```

```
10:10 AM

Unknown person tried to enter
```

Even failed attempts are recorded.

S3 Access Logs work exactly the same way.

---

# 2. What Gets Logged?

Suppose your bucket is

```
company-files
```

Users perform:

```
Upload report.pdf
```

↓

Logged.

---

```
Download image.jpg
```

↓

Logged.

---

```
Delete backup.zip
```

↓

Logged.

---

Even this:

```
Access Denied
```

↓

Logged.

---

AWS records requests such as:

- GET Object
- PUT Object
- DELETE Object
- LIST Bucket
- Copy Object
- Access Denied
- Successful Requests

Basically:

> **Every request to S3 is logged.**
> 

---

# 3. How It Works

Suppose

Bucket:

```
company-data
```

Logging Bucket:

```
company-access-logs
```

Flow:

```
User

↓

GET report.pdf

↓

S3 Bucket

↓

S3 creates log entry

↓

Stores log

↓

company-access-logs
```

---

# Diagram

```
                User

                  │

        GET report.pdf

                  │

                  ▼

         S3 Bucket (Company Data)

                  │

         Generate Log Entry

                  │

                  ▼

     S3 Logging Bucket (Logs)
```

---

# 4. Every Request is Logged

Example

Employee downloads

```
salary.xlsx
```

AWS creates log:

```
Time

09:00

User

Alice

Operation

GET

Object

salary.xlsx

Status

200 OK
```

---

Another example

Hacker tries

```
GET payroll.xlsx
```

Permission denied.

AWS still logs:

```
Status

403 Forbidden
```

This is important.

**Authorized AND unauthorized requests are logged.**

---

# 5. Where Are Logs Stored?

AWS stores logs in **another S3 bucket**.

Example

Application Bucket

```
company-data
```

↓

Logs stored in

```
company-data-logs
```

---

Diagram

```
company-data

↓

Logging Enabled

↓

company-data-logs
```

---

# 6. Important Requirement

The logging bucket **must be in the same AWS Region**.

Example

Application Bucket

```
US-East-1
```

Logging Bucket

```
US-East-1
```

✅ Allowed

---

Application Bucket

```
US-East-1
```

Logging Bucket

```
EU-West-1
```

❌ Not allowed

---

# 7. What Can You Do with the Logs?

The logs are just text files.

You can analyze them using:

- Amazon Athena
- Amazon EMR
- Custom applications
- SIEM tools
- Log analysis software

Example:

Manager asks:

> Who downloaded payroll.pdf yesterday?
> 

Athena can answer instantly.

---

# 8. Example Log

A real log contains many fields.

Simplified version:

```
Time

10:00

User

Alice

IP

192.168.1.5

Operation

GET

Object

report.pdf

Status

200
```

Another:

```
Time

10:05

User

Bob

Operation

DELETE

Object

backup.zip

Status

204
```

Another:

```
Time

10:06

Unknown

Operation

GET

Object

salary.xlsx

Status

403
```

Everything is recorded.

---

# 9. Logging Loop (Very Important Exam Topic)

This is the favorite AWS exam question.

Suppose

Bucket:

```
company-data
```

You enable logging.

Where should logs go?

Wrong answer:

```
company-data
```

(the same bucket)

---

Let's see what happens.

User uploads file.

```
User

↓

Upload report.pdf

↓

Bucket
```

S3 creates log.

Where should it store the log?

Same bucket.

So AWS writes:

```
log1.txt
```

But writing

```
log1.txt
```

is itself another PUT request.

That PUT request must also be logged.

AWS creates:

```
log2.txt
```

Writing

```
log2.txt
```

creates another PUT request.

Which generates

```
log3.txt
```

This never ends.

---

# Logging Loop Diagram

```
Bucket

↓

Upload File

↓

Create Log

↓

Store Log

↓

Creates New Log

↓

Store Log

↓

Creates Another Log

↓

Store Log

↓

Again

↓

Again

↓

Again...
```

Your bucket grows continuously.

This is called a **logging loop**.

---

# AWS Warning

Never configure:

```
Logging Bucket

=

Application Bucket
```

Always use a **different bucket**.

---

# Correct Architecture

```
Application Bucket

company-data

↓

Logging Enabled

↓

Logs

↓

Logging Bucket

company-data-access-logs
```

No loop.

---

# 10. Real Company Example

Company has:

```
photos-bucket
```

Requirement:

Track every download.

Solution

Enable S3 Access Logs.

Target bucket:

```
photos-access-logs
```

Now every request is recorded.

Later,

Security team uses Athena to answer:

```
Who downloaded image123.jpg?

When?

From which IP?
```

---

# 11. Access Logs vs CloudTrail

Students confuse these.

| S3 Access Logs | CloudTrail |
| --- | --- |
| Records S3 data access requests to the bucket | Records AWS API activity across AWS services |
| Stored in an S3 bucket | Delivered to S3, CloudWatch Logs, or EventBridge depending on configuration |
| Focuses on bucket/object requests | Covers many AWS services |
| Good for storage access analysis | Good for auditing AWS API calls and account activity |

### Example

User downloads:

```
report.pdf
```

S3 Access Logs:

```
GET report.pdf
```

CloudTrail:

```
GetObject API called by Alice
```

Different tools with different purposes.

---

# 12. Common Exam Scenarios

## Scenario 1

Requirement:

> Audit every access to an S3 bucket.
> 

Answer:

✅ Enable **S3 Server Access Logging**.

---

## Scenario 2

Requirement:

> Analyze who downloaded objects.
> 

Answer:

✅ Enable S3 Access Logs and query them using Athena.

---

## Scenario 3

Requirement:

> Logs should be stored in another region.
> 

Answer:

❌ Wrong.

Logging bucket must be in the **same region**.

---

## Scenario 4

Requirement:

> Store logs in the same bucket.
> 

Answer:

❌ Wrong.

Causes **logging loop**.

---

# Architecture Diagram

```
            Users

              │

        Upload / Download

              │

              ▼

      S3 Bucket (App Data)

              │

      Generate Access Logs

              │

              ▼

     S3 Logging Bucket

              │

      Athena / EMR

              │

      Reports & Auditing
```

---

# SAA-C03 Cheat Sheet

| Topic | Key Point |
| --- | --- |
| Purpose | Audit every request made to an S3 bucket |
| Logs Requests | ✅ Authorized and denied requests |
| Storage | Stored in another S3 bucket |
| Same Region Required | ✅ Yes |
| Analyze With | Athena, EMR, SIEM tools |
| Store in Same Bucket? | ❌ Never |
| Reason | Causes an infinite logging loop |

---

# 🎯 Ultimate Memory Trick

Imagine a **security guard writing every visitor's name in a notebook**.

If the guard stores the notebook **inside the room he's monitoring**, every time he places the notebook back, **that action itself must also be recorded**, creating another notebook entry, then another, and another...

That's exactly why AWS warns:

> **Never use the monitored bucket as the logging bucket.**
> 

Always keep the **building (application bucket)** and the **security office (logging bucket)** separate.

---

# 🔥 Interview / Exam Questions

### Q1. Can Access Logs record failed requests?

✅ **Yes.**

Both **successful and denied** requests are logged.

---

### Q2. Where should S3 Access Logs be stored?

✅ In **another S3 bucket in the same AWS Region**.

---

### Q3. Why shouldn't the source bucket also be the logging bucket?

✅ Because every log written generates another log entry, creating an **infinite logging loop** that causes the bucket to grow continuously.

---

### Q4. Can you use Athena to analyze S3 Access Logs?

✅ **Yes.** Since the logs are stored as files in S3, Athena is commonly used to query and analyze them.
