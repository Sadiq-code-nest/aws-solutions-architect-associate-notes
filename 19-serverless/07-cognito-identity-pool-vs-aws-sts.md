# Cognito Identity Pool vs. AWS STS?

This is an **excellent SAA-C03 question**, because Cognito Identity Pools and STS are closely related.

The simplest answer:

> **STS is the AWS service that actually issues temporary credentials.**
> 
> 
> **Cognito Identity Pool is a higher-level service that decides how application users get those credentials and which IAM role they should receive.**
> 

Think:

```
Cognito Identity Pool
        ↓
      STS
        ↓
Temporary AWS Credentials
```

## 1. What does AWS STS do?

AWS Security Token Service (STS) is fundamentally a **temporary credential issuer**.

It can provide:

```
Access Key ID
Secret Access Key
Session Token
Expiration
```

For example:

```
Application
    ↓
STS
    ↓
Temporary credentials
    ↓
S3
```

STS doesn't primarily care whether the caller is a mobile-app user, employee, workload, etc.

Its job is essentially:

> **"Here are temporary credentials that let you assume this IAM role."**
> 

---

# 2. What does Cognito Identity Pool do?

Identity Pool is designed specifically around **application identities**.

For example:

```
Mobile App
    ↓
User logs in with
Cognito / Google / Facebook / etc.
    ↓
Cognito Identity Pool
    ↓
Determine IAM role
    ↓
Temporary AWS credentials
```

It handles things such as:

- Application user identity
- Authentication providers
- Authenticated vs unauthenticated users
- Mapping identities to IAM roles
- Obtaining temporary AWS credentials

Behind the scenes, Cognito uses AWS's temporary-credential mechanisms.

---

# 3. Think of STS as the "credential factory"

A useful analogy:

### STS = Bank

It issues temporary access cards.

### Identity Pool = Reception/security desk

It says:

> "This person has authenticated through Google/Cognito. Give them the **Customer** access card."
> 

So:

```
                 Cognito Identity Pool
                         │
                  "Who is this user?"
                         │
                  "Which role?"
                         │
                         ▼
                        STS
                         │
                  "Here are temporary
                   credentials."
                         │
                         ▼
                        S3
```

---

# 4. The biggest difference

|  | Cognito Identity Pool | AWS STS |
| --- | --- | --- |
| Primary purpose | Give application users AWS access | Issue temporary AWS credentials |
| Designed for | Mobile/web application users | AWS identities/workloads/users/federation |
| Determines IAM role for app identities | ✅ | Not its primary purpose |
| Temporary credentials | ✅ Provides them | ✅ Issues them |
| Supports unauthenticated app users | ✅ | ❌ Not directly as an "anonymous app user" feature |
| Supports Cognito User Pool identities | ✅ | Can work with federation/role assumption mechanisms, but not as a Cognito user directory |
| Direct AWS service access | ✅ Enables it | ✅ Credentials can enable it |
| User directory | ❌ | ❌ |
| Authentication | Uses external/auth providers | Not an application user directory |

---

# 5. Example: Mobile photo application

Suppose:

```
PhotoApp
```

User:

```
Sadiq
```

logs in.

### Using Cognito Identity Pool

```
Sadiq
 ↓
Cognito User Pool
 ↓
JWT
 ↓
Cognito Identity Pool
 ↓
IAM Role
 ↓
Temporary credentials
 ↓
S3
```

The Identity Pool handles the **application-user-to-IAM-role** part.

---

# 6. What if I use STS directly?

You can use STS directly when you already have an appropriate identity/federation mechanism and want to obtain temporary credentials.

For example, an enterprise has:

```
Corporate Active Directory
          ↓
       SAML 2.0
          ↓
         STS
          ↓
Temporary AWS credentials
          ↓
AWS Console / AWS APIs
```

This is a classic **AWS STS federation** scenario.

The corporate user authenticates with the company's identity provider, and STS exchanges that federation information for temporary AWS credentials associated with an IAM role.

---

# 7. This is where SAA questions try to confuse you

Imagine the question says:

> A company has a mobile application. Users authenticate using Facebook. After authentication, users need to upload files directly to Amazon S3. The company does not want to embed AWS credentials in the application.
> 

### Answer:

**Cognito Identity Pool**

Why?

Because this is an **application user → AWS service** scenario.

```
Facebook
   ↓
Cognito Identity Pool
   ↓
Temporary AWS credentials
   ↓
S3
```

---

Now imagine:

> A company has an existing corporate identity provider using SAML 2.0. Employees need temporary credentials to access AWS resources.
> 

Think:

**STS federation / AssumeRoleWithSAML**

```
Corporate IdP
     ↓
SAML
     ↓
AWS STS
     ↓
Temporary credentials
     ↓
AWS
```

---

# 8. Another important STS scenario: AssumeRole

Suppose you have:

```
AWS Account A
      ↓
Developer
      ↓
AssumeRole
      ↓
AWS Account B
      ↓
ProductionRole
```

STS can issue temporary credentials for that role.

```
Developer
    ↓
sts:AssumeRole
    ↓
STS
    ↓
Temporary credentials
    ↓
Production AWS resources
```

This has nothing to do with Cognito.

It's an **AWS-to-AWS / IAM role assumption** scenario.

---

# 9. So why does Cognito exist if STS already exists?

Because Cognito solves a **higher-level application identity problem**.

Imagine you're building:

```
Netflix-like mobile app
```

You could theoretically build all the logic yourself:

```
Google login
     ↓
Verify identity
     ↓
Determine user
     ↓
Determine IAM role
     ↓
Call STS
     ↓
Get temporary credentials
     ↓
Refresh credentials
     ↓
Handle expiration
     ↓
Access S3
```

That's a lot of work.

Cognito Identity Pools package much of this application-user federation and AWS credential-access flow for you.

So:

> **STS is the underlying temporary credential service. Cognito Identity Pool is an application-focused identity federation layer that uses temporary AWS credentials to give users controlled access to AWS resources.**
> 

---

# 10. One diagram to remember forever

```
                 APPLICATION USER
                        │
                        │ Login
                        ▼
              ┌───────────────────┐
              │   Identity / IdP  │
              │ Cognito User Pool │
              │ Google / Facebook│
              │ SAML / OIDC       │
              └─────────┬─────────┘
                        │
                        │ Identity
                        ▼
              ┌───────────────────┐
              │ Cognito Identity  │
              │       Pool        │
              └─────────┬─────────┘
                        │
                        │ IAM Role
                        ▼
              ┌───────────────────┐
              │       AWS STS     │
              │ Temporary Creds   │
              └─────────┬─────────┘
                        │
                        │
              ┌─────────┼──────────┐
              ▼         ▼          ▼
             S3      DynamoDB   AppSync
```

**Important:** Don't interpret this as "Identity Pool is just another name for STS." They're different services with different responsibilities.

### 🧠 SAA-C03 cheat code

**STS → "Give me temporary AWS credentials."**

**Cognito Identity Pool → "My application user needs temporary AWS credentials to access AWS resources."**

**Cognito User Pool → "I need to authenticate/manage my application's users."**

That's the distinction I'd use in an exam.
