# AWS IAM Identity Center (Successor to AWS Single Sign-On)

This is a **very important AWS SAA-C03 topic**, especially when the exam mentions:

- Multiple AWS accounts
- AWS Organizations
- One login
- SSO (Single Sign-On)
- Active Directory (AD)
- Okta
- Microsoft 365
- Salesforce

The easiest way to remember it is:

> **IAM Identity Center = One username and password to access everything.**
> 

---

# The Problem Before IAM Identity Center

Imagine a company has:

- 15 AWS Accounts
- Salesforce
- Microsoft 365
- GitHub Enterprise
- EC2 Windows Servers

Without Identity Center...

Employee John needs:

```
AWS Dev Account
Username: john-dev

AWS Prod Account
Username: john-prod

Microsoft 365
Username: john365

Salesforce
Username: john-sales

EC2 Windows
Username: john-win
```

John has:

❌ Multiple usernames

❌ Multiple passwords

❌ Multiple logins

Very frustrating.

---

# IAM Identity Center Solves This

Instead of remembering five accounts...

John logs in once.

```
Username

john@company.com

Password

********
```

Done.

Now AWS automatically lets him access everything he's allowed to use.

---

# Architecture

```
                John

                  │
                  │ Login Once
                  ▼

      AWS IAM Identity Center

      (Single Sign-On)

      │
 ┌────┼───────────────┬──────────┐
 │    │               │          │
 ▼    ▼               ▼          ▼

AWS Account       AWS Account   AWS Account

 Dev                Test          Prod

 │
 ▼

Salesforce

 │
 ▼

Microsoft 365

 │
 ▼

EC2 Windows Login
```

One login.

Many applications.

---

# What is Single Sign-On (SSO)?

Single Sign-On means:

> **Login once → Access many systems without logging in again.**
> 

Example:

You login to Gmail.

Now you can also open:

- Google Drive
- Google Docs
- Google Calendar
- YouTube

Without entering your password again.

That is SSO.

AWS Identity Center does the same.

---

# Real-Life Example ⭐⭐⭐⭐⭐

Imagine you work at Amazon.

You arrive in the morning.

You login:

```
john@amazon.com
```

After that you can immediately open:

- AWS Dev Account
- AWS Production Account
- Microsoft Teams
- Salesforce
- Internal Dashboard
- EC2 Windows Desktop

No additional logins.

---

# What Can IAM Identity Center Access?

## 1. AWS Accounts

Example:

```
AWS Organization

↓

Development

↓

Testing

↓

Production
```

One login accesses all three.

---

## 2. Business Applications

Examples:

- Salesforce
- Microsoft 365
- Box
- Dropbox
- Slack

---

## 3. SAML Applications

Any application supporting:

```
SAML 2.0
```

can use Identity Center.

---

## 4. EC2 Windows

Users can login directly into Windows EC2 instances.

---

# Identity Providers (IdP)

Where do user accounts come from?

IAM Identity Center supports two choices.

---

## Option 1

Built-in Identity Store

AWS stores:

```
Users

Groups

Passwords
```

Everything stays inside AWS.

Good for small companies.

---

## Option 2

External Identity Provider

Large companies already have employee accounts.

Example:

```
Active Directory

Okta

OneLogin

Azure AD
```

Instead of creating users again,

AWS connects to them.

Architecture:

```
Employees

↓

Active Directory

↓

IAM Identity Center

↓

AWS Accounts
```

---

# Login Flow ⭐⭐⭐⭐⭐

Let's follow John.

---

### Step 1

John opens AWS Portal.

```
portal.company.com
```

---

### Step 2

Identity Center asks:

```
Username

Password
```

---

### Step 3

Identity is verified.

Either by:

- Built-in Identity Store

OR

- Active Directory

---

### Step 4

John sees:

```
Available Accounts

Development

Testing

Production
```

---

### Step 5

John clicks

```
Development
```

AWS automatically creates temporary credentials.

No IAM user is created.

---

### Step 6

John now uses the AWS Console.

```
John

↓

IAM Identity Center

↓

Assume IAM Role

↓

Development Account
```

---

# Wait...

Did John become an IAM User?

No.

This is extremely important.

Identity Center uses:

```
AssumeRole
```

behind the scenes.

It creates:

Temporary Credentials

using STS.

Exactly like this:

```
User

↓

IAM Identity Center

↓

STS

↓

Temporary Credentials

↓

IAM Role

↓

AWS Account
```

No permanent IAM User needed.

---

# Permission Sets

This is one of the most tested topics.

What is a Permission Set?

Think:

> **Permission Set = Template of IAM permissions.**
> 

Instead of creating IAM roles manually in every account,

create one Permission Set.

Example:

```
Developer Permission Set

↓

EC2

S3

CloudWatch
```

Assign it to John.

Done.

---

# Architecture

```
Permission Set

↓

Developer

↓

EC2

S3

Lambda

↓

Assigned

↓

John

↓

Development Account
```

AWS automatically creates the IAM role behind the scenes.

---

# Real-Life Example ⭐⭐⭐⭐⭐

Company has:

100 developers.

Without Permission Sets:

Need to manually create IAM Roles

100 times.

With Permission Sets:

```
Developer Permission Set

↓

Assign

↓

100 Developers
```

AWS handles everything.

Huge time saver.

---

# Multi-Account Permissions

Suppose company has:

```
Development

Testing

Production
```

John should have:

Developer access

to all three.

Instead of configuring three accounts separately:

Create one Permission Set.

Assign:

```
John

↓

Development

Testing

Production
```

Done.

---

# Another Example

Manager Sarah.

Needs

Read-only

across every account.

Create

```
ReadOnly Permission Set
```

Assign Sarah.

AWS creates proper IAM roles in every account automatically.

---

# Attribute-Based Access Control (ABAC)

Normally,

permissions depend on:

```
Username
```

ABAC says

Permissions depend on

```
User Attributes
```

Example attributes:

```
Department

Country

Cost Center

Job Title
```

---

# Example ⭐⭐⭐⭐⭐

Suppose company has:

Employees

```
Department

Finance

Engineering

HR
```

Rule:

```
If Department = Finance

↓

Allow Finance Resources
```

John

```
Department

Engineering
```

Cannot access Finance.

---

Another example:

User:

```
Country

UK
```

Automatically gets

UK resources.

No manual IAM policy needed.

---

# Architecture

```
User

↓

Attributes

Department = Finance

↓

IAM Identity Center

↓

Permission

↓

Finance AWS Account
```

---

# Real-Life Example

Company has

5,000 employees.

Instead of manually assigning permissions:

Just update:

```
Department
```

When employee moves from

Engineering

to

Finance,

permissions change automatically.

Very powerful.

---

# Why Use IAM Identity Center?

Without it:

```
100 AWS Accounts

↓

100 IAM Users

↓

100 Passwords

↓

Nightmare
```

With Identity Center:

```
One Login

↓

Temporary Credentials

↓

Permission Sets

↓

AWS Accounts
```

Simple.

---

# IAM Identity Center vs IAM

This is a favourite exam comparison.

| IAM | IAM Identity Center |
| --- | --- |
| Users inside one AWS account | One login across many AWS accounts |
| Permanent IAM Users | Temporary credentials via STS |
| No built-in SSO | Built for Single Sign-On |
| Account-level identity management | Organisation-wide identity management |

---

# IAM Identity Center vs AssumeRole

| AssumeRole | IAM Identity Center |
| --- | --- |
| Manual cross-account access | Central SSO solution |
| One role at a time | Access many accounts easily |
| STS credentials | Also uses STS internally |

Think:

> **Identity Center uses AssumeRole behind the scenes.**
> 

---

# Complete Flow

```
                    Employee

                        │

                        ▼

          IAM Identity Center Login

                        │

          Username + Password

                        │

                        ▼

      Built-in Store / Active Directory

                        │

                        ▼

               Permission Set

                        │

                        ▼

              Assume IAM Role

                        │

                        ▼

         Temporary STS Credentials

                        │

                        ▼

             AWS Account Access
```

---

# Common Exam Scenarios

### Scenario 1

> A company has 50 AWS accounts and wants employees to log in once and access all authorised accounts.
> 

✅ **AWS IAM Identity Center**

---

### Scenario 2

> Users already exist in Microsoft Active Directory and should use those same credentials for AWS.
> 

✅ **IAM Identity Center + Active Directory**

---

### Scenario 3

> Developers need the same permissions across Development, Test and Production accounts.
> 

✅ **Permission Sets**

---

### Scenario 4

> A user's department changes from Engineering to Finance, and their permissions should automatically change.
> 

✅ **ABAC (Attribute-Based Access Control)**

---

# Memory Tricks ⭐⭐⭐⭐⭐

### IAM Identity Center

> **One Login → Many AWS Accounts**
> 

---

### Permission Set

> **One IAM policy template → Applied to many AWS accounts**
> 

---

### Identity Provider

> **Where users come from (Active Directory, Okta, Built-in store)**
> 

---

### ABAC

> **Permissions based on user attributes, not usernames**
> 

---

### STS

> **Identity Center never creates permanent IAM users in member accounts—it uses temporary credentials by assuming IAM roles.**
> 

---

# Final Summary

```
One Login
      │
      ▼
IAM Identity Center
      │
      ├── AWS Accounts
      ├── Salesforce
      ├── Microsoft 365
      ├── EC2 Windows
      └── SAML Apps
      │
      ▼
Permission Sets
      │
      ▼
Assume IAM Role
      │
      ▼
Temporary STS Credentials
      │
      ▼
Access Granted
```

## 🧠 SAA-C03 Exam Keywords

- **Single Sign-On (SSO)** → IAM Identity Center
- **Multiple AWS accounts** → IAM Identity Center
- **AWS Organizations integration** → IAM Identity Center
- **Permission templates across accounts** → Permission Sets
- **Existing corporate identities (AD/Okta)** → External Identity Provider
- **Permissions based on department, title, or cost centre** → ABAC
- **Temporary credentials** → STS + AssumeRole behind the scenes
