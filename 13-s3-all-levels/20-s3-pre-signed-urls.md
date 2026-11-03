# Amazon S3 Pre-Signed URLs — Complete Crystal Clear Notes (SAA-C03)

This is a **very common SAA-C03 exam topic**.

The biggest confusion is:

> **"If my S3 bucket is private, how can someone download or upload a file without making the bucket public?"**
> 

The answer is:

> **Pre-Signed URL**
> 

---

# 1. What is a Pre-Signed URL?

A Pre-Signed URL is a **temporary secure link** that gives someone permission to access a **specific S3 object**.

Think of it like a **temporary access pass**.

Instead of making the bucket public, you generate a special URL.

Example:

```
Private Bucket

↓

Generate Special URL

↓

Share URL

↓

User can access object

↓

URL expires automatically
```

The bucket remains **private**.

---

# Real-Life Analogy

Imagine your apartment building.

Normally,

```
Door

↓

Locked
```

Nobody can enter.

Instead of giving someone your permanent key,

you create a **temporary visitor pass**.

```
Visitor Pass

Valid

2 Hours

↓

After 2 Hours

Expired
```

Pre-Signed URLs work exactly like this.

---

# 2. Why Do We Need It?

Suppose you have

```
Private S3 Bucket
```

containing

```
salary.pdf
```

Normal user

↓

Cannot access.

```
403 Access Denied
```

You don't want to make the bucket public.

Instead,

you generate a Pre-Signed URL.

```
Private Bucket

↓

Generate URL

↓

Send URL

↓

User Downloads File
```

The bucket is still private.

---

# 3. How It Works

Imagine:

Bucket

```
company-files
```

Object

```
salary.pdf
```

You (the owner) generate

```
Pre-Signed URL

Valid

30 Minutes
```

Flow

```
Owner

↓

Generate URL

↓

https://.....

↓

Send URL

↓

User

↓

Download File

↓

30 Minutes Later

↓

Expired
```

---

# Diagram

```
                Owner

                  │

      Generate Pre-Signed URL

                  │

                  ▼

         Temporary URL

                  │

          Send to User

                  │

                  ▼

            S3 Bucket

           (Still Private)

                  │

          Download File

                  │

          Until Expiration
```

---

# 4. Important Concept

Many students think

> Pre-Signed URL makes the bucket public.
> 

❌ Wrong.

Only **one specific object** becomes accessible.

Example

Bucket

```
Private Bucket
```

Contains

```
salary.pdf

resume.pdf

secret.docx
```

Generate URL only for

```
salary.pdf
```

User can download

✅ salary.pdf

Cannot access

❌ resume.pdf

❌ secret.docx

---

# 5. Who Can Generate It?

AWS allows generation using

- S3 Console
- AWS CLI
- AWS SDK
- Application code

Example

Python

↓

Generate URL

↓

Send to customer

---

# 6. Expiration Time

Every Pre-Signed URL expires.

Different tools have different limits.

## S3 Console

Minimum

```
1 minute
```

Maximum

```
12 hours
```

---

## AWS CLI

Default

```
3600 seconds

=

1 hour
```

Maximum

```
604800 seconds

=

7 Days
```

---

## SDK

Usually you specify

```
ExpiresIn

3600
```

or

```
86400
```

etc.

---

# What Happens After Expiration?

Suppose

```
URL

Valid

30 Minutes
```

Timeline

```
10:00

Generate

↓

10:10

Works

↓

10:20

Works

↓

10:29

Works

↓

10:30

Expired

↓

10:31

Access Denied
```

The object is still in S3.

Only the URL becomes invalid.

---

# 7. Very Important Exam Concept

The user **inherits the permissions** of the person who generated the URL.

This is one of the favorite exam questions.

---

Suppose

Alice has permission

```
GetObject
```

She creates a Pre-Signed URL.

Bob opens it.

Bob is treated like Alice for that object.

---

Diagram

```
Alice

Permission

GET

↓

Generate URL

↓

Bob

↓

Download

(using Alice's permission)
```

---

Suppose

Alice does NOT have Delete permission.

She generates URL.

Can Bob delete?

❌ No.

Because Bob only gets Alice's permissions.

---

# Example 1 — Download Premium Video

Netflix stores videos in

```
Private Bucket
```

Users must login first.

Flow

```
User Login

↓

Application

↓

Generate URL

↓

Return URL

↓

Watch Video
```

After

```
1 Hour
```

URL expires.

Need a new one.

---

# Example 2 — Invoice Download

Bank stores

```
invoice.pdf
```

Private.

Customer clicks

```
Download Invoice
```

Backend generates

```
5-minute URL
```

Customer downloads.

Done.

---

# Example 3 — Temporary File Upload

This is another common exam scenario.

Suppose users upload profile pictures.

Without Pre-Signed URL

Flow

```
User

↓

Application Server

↓

S3
```

The application must handle the file upload.

This increases server load.

---

Using Pre-Signed URL

```
User

↓

Application

↓

Generate Upload URL

↓

User Uploads

↓

Directly to S3
```

Server never receives the file.

Much faster.

---

Diagram

```
User

↓

Request Upload

↓

Backend

↓

Generate PUT URL

↓

User

↓

Upload Directly

↓

Amazon S3
```

---

# GET vs PUT Pre-Signed URLs

There are two common types.

---

## GET URL

Purpose

Download

Flow

```
Generate URL

↓

User

↓

Download Object
```

---

## PUT URL

Purpose

Upload

Flow

```
Generate URL

↓

User

↓

Upload Object
```

---

# Exam Trick

Question

Customer should upload files directly to S3 without AWS credentials.

Answer

✅ Pre-Signed PUT URL

---

Question

Customer should download private files temporarily.

Answer

✅ Pre-Signed GET URL

---

# Security Benefits

Without Pre-Signed URL

```
Bucket

↓

Public

↓

Everyone can download
```

Very dangerous.

---

With Pre-Signed URL

```
Bucket

↓

Private

↓

Only URL Holder

↓

Limited Time
```

Much safer.

---

# Common Real-World Scenarios

## Scenario 1

Company stores

```
Training Videos
```

Private.

Only paid users should watch.

Solution

```
Login

↓

Generate Pre-Signed URL

↓

Watch Video
```

---

## Scenario 2

Hospital

Stores

```
Medical Reports
```

Private.

Doctor clicks

```
Download Report
```

Backend

↓

Generate

```
10 Minute URL
```

Doctor downloads.

---

## Scenario 3

Large File Upload

Company allows users to upload

```
5 GB Video
```

Instead of uploading through EC2,

Generate

PUT Pre-Signed URL.

User uploads directly to S3.

Much cheaper.

---

## Scenario 4

Temporary Contractor

Contractor needs access for

```
2 Hours
```

Generate

```
2 Hour Pre-Signed URL
```

No IAM User needed.

---

# Architecture Diagram

```
            User

              │

      Request Download

              │

              ▼

        Application

              │

Generate Pre-Signed URL

              │

              ▼

      Temporary URL

              │

              ▼

         Amazon S3

      (Private Bucket)

              │

      Download Object
```

---

# Common Exam Questions

## Question 1

Private bucket.

Need temporary download.

Answer

✅ Pre-Signed GET URL

---

## Question 2

Users upload images directly to S3.

Answer

✅ Pre-Signed PUT URL

---

## Question 3

Should the bucket become public?

❌ No.

Bucket remains private.

---

## Question 4

Can anyone use the URL?

✅ Yes, **anyone who has the URL can use it until it expires** (unless additional restrictions such as source IP are applied when generating or validating access).

---

## Question 5

What permissions does the user get?

They inherit the permissions of the URL creator.

---

# Pre-Signed URL vs Public Bucket

| Feature | Public Bucket | Pre-Signed URL |
| --- | --- | --- |
| Bucket Visibility | Public | Private |
| Authentication Required | No | URL contains temporary authorization |
| Expiration | Never | Yes |
| Secure | ❌ Low | ✅ High |
| Specific Object | No | Yes |
| Temporary Access | No | Yes |

---

# SAA-C03 Cheat Sheet

| Requirement | Solution |
| --- | --- |
| Temporary download of private file | ✅ Pre-Signed GET URL |
| Temporary upload to S3 | ✅ Pre-Signed PUT URL |
| Bucket should remain private | ✅ Pre-Signed URL |
| Access expires automatically | ✅ Yes |
| User permissions | Inherits permissions of the URL creator |
| Console expiration | 1 minute – 12 hours |
| CLI maximum expiration | 7 days (604800 seconds) |

---

# Ultimate Memory Trick

Imagine your S3 bucket is a **locked hotel room**.

- **Public Bucket** → The hotel room door is always open. Anyone can enter.
- **Private Bucket** → Only the owner has the key.
- **Pre-Signed URL** → The owner creates a **temporary digital room key** that:
    - Opens **only one specific room (object)**.
    - Works only for a **limited time**.
    - Stops working automatically when it expires.

This is why Pre-Signed URLs are one of the safest ways to share private S3 objects without exposing the entire bucket.
