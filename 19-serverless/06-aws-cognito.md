# Amazon Cognito (AWS SAA-C03) – Beginner Friendly Complete Notes

Imagine you build a mobile app called **PhotoShare**.

Before users can upload photos, they must:

- Create an account
- Login
- Reset password if forgotten
- Verify email
- Use Google Login if they want

Can you build all of this yourself?

Yes...

But it's difficult, time-consuming, and security-sensitive.

AWS provides **Amazon Cognito** to handle user authentication and identity.

---

# What is Amazon Cognito?

Amazon Cognito is a fully managed service that handles

- User Sign-up
- User Sign-in
- User Authentication
- User Identity
- Access to AWS Resources

Think of Cognito as

> **AWS's Login & Identity Management Service for your application users.**
> 

---

# Why Do We Need Cognito?

Suppose your app has

```
500,000 users
```

Each user needs

- Username
- Password
- Password Reset
- Email Verification
- MFA
- Google Login

Building all this yourself is difficult.

Instead

```
Users

↓

Amazon Cognito

↓

Your Application
```

AWS manages everything.

---

# Real-Life Analogy

Imagine a university.

Before entering,

students go to

```
Registration Office
```

The office

- Creates student accounts
- Verifies identity
- Issues ID cards
- Resets passwords

Cognito is that registration office.

---

# Two Main Parts of Cognito

AWS Cognito has

```
1. User Pools

2. Identity Pools
```

Students confuse these.

Let's make them simple.

---

# Cognito User Pools (CUP)

Think of User Pool as

> **A User Database**
> 

It stores users.

Example

```
John

Password

Email

Phone
```

---

User signs up

↓

User Pool stores information

↓

User logs in

↓

User Pool verifies credentials

Simple.

---

## What Does User Pool Do?

### User Registration

```
Sign Up

↓

Store User
```

---

### Login

```
Username

Password

↓

Login Success
```

---

### Password Reset

Forgot password?

AWS sends

```
Reset Email
```

Automatically.

---

### Email Verification

```
User Registers

↓

Verification Email

↓

Click Link

↓

Verified
```

---

### Phone Verification

Same idea.

AWS sends

```
SMS Code
```

---

### Multi-Factor Authentication (MFA)

Login requires

```
Password

+

OTP
```

Much more secure.

---

### Social Login

Instead of creating a new account,

users can login with

- Google
- Facebook
- Apple
- Amazon
- SAML

Example

```
Google Login

↓

Cognito

↓

Your App
```

---

# User Pool Summary

Think

```
User Database

+

Authentication
```

---

# User Pool Integrations

User Pool works well with

### API Gateway

```
User

↓

Login

↓

Cognito

↓

API Gateway

↓

Lambda
```

---

### Application Load Balancer (ALB)

```
User

↓

ALB

↓

Cognito Login

↓

EC2
```

No need to write your own login page.

---

# Cognito Identity Pool

This is completely different.

User Pool answers

> **Who is the user?**
> 

Identity Pool answers

> **What AWS resources can the user access?**
> 

---

Think

User logged in successfully.

Now they want to upload a file directly to S3.

Without Identity Pool

User has

```
No AWS Credentials
```

Cannot upload.

---

Identity Pool gives

```
Temporary AWS Credentials
```

Now

```
User

↓

Identity Pool

↓

Temporary AWS Credentials

↓

S3
```

Works.

---

# Real-Life Analogy

User Pool

↓

Issues

```
Student ID Card
```

Identity Pool

↓

Issues

```
Library Access Card
```

The ID proves who you are.

The library card lets you use the library.

---

# Where Can Identity Pool Get Users?

Identity Pool doesn't create users.

It accepts users from

- Cognito User Pool
- Google
- Facebook
- Apple
- Amazon
- SAML
- OpenID Connect

Example

```
Google

↓

Identity Pool

↓

AWS Credentials
```

---

# Temporary AWS Credentials

Identity Pool gives

```
Access Key

Secret Key

Session Token
```

Temporary only.

Safer than permanent IAM users.

---

# Why Temporary?

Imagine giving every mobile app user

Administrator credentials.

Terrible idea.

Instead

AWS creates temporary credentials

that expire automatically.

---

# IAM Roles

Identity Pool assigns IAM Roles.

Example

Authenticated users

↓

```
Can Upload

Can Download
```

---

Guest users

↓

```
Read Only
```

Different permissions.

---

# Fine-Grained Access

Suppose

John logs in.

He should only access

```
john/profile.jpg
```

Not

```
alice/profile.jpg
```

Identity Pool can customize IAM permissions.

---

# Row-Level Security (DynamoDB)

Very common exam topic.

Suppose table

```
Orders
```

contains

```
John

Alice

Bob
```

John logs in.

He should only see

```
John's Orders
```

Not

Alice's.

Identity Pool + IAM policies make this possible.

This is called

> **Row-Level Security**
> 

Each user can access only their own data.

---

# User Pool vs Identity Pool

This is the favorite exam comparison.

| User Pool | Identity Pool |
| --- | --- |
| Stores users | Gives AWS credentials |
| Authentication | Authorization to AWS resources |
| Sign Up / Login | Access S3, DynamoDB, etc. |
| Password Reset | IAM Roles |
| Email Verification | Temporary AWS Credentials |
| MFA | Fine-grained AWS permissions |

---

# Easy Memory Trick

User Pool

↓

```
Who are you?
```

Identity Pool

↓

```
What can you access?
```

---

# Complete Architecture

```
User

↓

User Pool

↓

Login Success

↓

Identity Pool

↓

Temporary Credentials

↓

S3

↓

DynamoDB
```

---

# Example

Mobile Photo App

User logs in.

↓

Identity Pool provides credentials.

↓

User uploads photo directly.

```
Phone

↓

Cognito

↓

Temporary Credentials

↓

Amazon S3
```

No backend server required.

---

# Another Example

Online Shopping

```
Customer

↓

Login

↓

User Pool

↓

Identity Pool

↓

DynamoDB
```

Customer only sees

their own orders.

---

# Cognito vs IAM

Students confuse these.

| Cognito | IAM |
| --- | --- |
| App users | AWS users |
| Customers | Developers/Admins |
| Millions of mobile users | Employees |
| Login system | AWS account management |

---

Example

AWS Employee

↓

Uses IAM

---

Customer using your mobile app

↓

Uses Cognito

---

# AWS SAA-C03 Cheat Sheet

| Topic | Remember |
| --- | --- |
| **Amazon Cognito** | User authentication and identity service |
| **User Pool** | User database + login system |
| **Identity Pool** | Temporary AWS credentials |
| **User Pool Features** | Sign-up, login, password reset, email/SMS verification, MFA, social login |
| **Identity Pool** | Uses IAM roles to control access to AWS services |
| **Temporary Credentials** | Access Key, Secret Key, Session Token |
| **Integrations** | API Gateway, ALB, S3, DynamoDB, Lambda |
| **Social Login** | Google, Facebook, Apple, Amazon, SAML, OIDC |
| **Row-Level Security** | Users access only their own DynamoDB items |
| **Cognito vs IAM** | Cognito = application users; IAM = AWS account users |

---

# Easy Memory Tricks

- 👤 **User Pool** = User database + login system.
- 🎫 **Identity Pool** = Temporary AWS access pass.
- 🔑 **User Pool** = "Who are you?"
- 🛡️ **Identity Pool** = "What are you allowed to access?"
- 📱 **Cognito** = For your application's users.
- 👨‍💻 **IAM** = For AWS administrators and developers.

---

# Exam Decision Tree

When you see a Cognito question, ask yourself:

### 🔹 Do I need users to **sign up or log in**?

➡️ **Cognito User Pool**

Examples:

- Register account
- Login
- Password reset
- Email verification
- MFA
- Google/Facebook login

---

### 🔹 Do authenticated users need to **access AWS resources directly**?

➡️ **Cognito Identity Pool**

Examples:

- Upload files to S3
- Read/write DynamoDB
- Get temporary AWS credentials
- Apply IAM permissions
- Row-level security

---

## 30-Second Exam Memory Trick

Think of a shopping mall:

- 📝 **User Pool** = The customer registration desk. It creates accounts and verifies who the customer is.
- 🎟️ **Identity Pool** = The access pass desk. After verifying the customer, it gives a temporary pass that lets them enter specific stores (AWS services) based on their permissions.

**User Pool = Authentication (Who are you?)**

**Identity Pool = Authorization (What AWS resources can you use?)**
