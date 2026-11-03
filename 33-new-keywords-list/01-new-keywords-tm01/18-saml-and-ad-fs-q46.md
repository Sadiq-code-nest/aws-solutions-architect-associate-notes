# SAML 2.0 + AD + AD FS + SAML Assertion + AWS — SAA-C03 Notes

This whole topic becomes easy if you understand **who stores the users, who verifies them, and who gives AWS permission information**.

---

# 1. Microsoft Active Directory (AD)

**Microsoft Active Directory** is where a company stores and manages its users and credentials.

For example:

```
Company Active Directory
│
├── Sadiq
├── Rahim
├── Karim
└── Software Architects
```

It stores things such as:

- User accounts
- Passwords
- Groups
- Organizational information
- Authentication information

So when you log in to a company computer:

```
Username: sadiq
Password: ********
       ↓
Active Directory
       ↓
Authentication successful ✅
```

### Think:

> **AD = Where the company's users/credentials live.**
> 

---

# 2. Microsoft AD FS

**AD FS = Active Directory Federation Services**

AD FS is a service that allows your organization's existing identities to be used to access **other applications/services**, including AWS.

Think:

```
Active Directory
       ↓
     AD FS
       ↓
 Other applications
       ↓
      AWS
```

AD itself mainly manages the identities.

AD FS acts as a **federation/identity provider service** that can authenticate those users and communicate their identity to another system.

### Think:

> **AD = User database**
> 

> **AD FS = Bridge that allows those users to authenticate to external services**
> 

---

# 3. Why do we need AD FS for AWS?

Suppose a company already has:

```
On-premises
    │
    ▼
Active Directory
    │
    ├── Alice
    ├── Bob
    └── Charlie
```

The company doesn't want to create separate AWS IAM users:

```
AWS IAM
├── Alice
├── Bob
└── Charlie
```

Because then users would have:

```
Company password
       +
AWS credentials
```

That's inconvenient and creates duplicate identity management.

Instead, the company wants:

```
One company identity
       ↓
Active Directory
       ↓
AD FS
       ↓
AWS
```

So users can use their **existing corporate credentials**.

---

# 4. What is SAML 2.0?

**SAML = Security Assertion Markup Language**

SAML is a standard used to exchange **authentication and authorization information** between systems.

For SAA-C03, the easiest way to think about it is:

> **SAML is the language/protocol that allows an identity provider to tell AWS who the user is and what they are allowed to do.**
> 

---

# 5. The important players

You need to know these four:

```
AD
↓
AD FS
↓
SAML
↓
AWS
```

But technically, there are different roles.

### Active Directory

Stores:

```
User + password
```

### AD FS

Authenticates the user using AD and acts as the **Identity Provider (IdP)**.

### SAML

Carries the authentication/identity information.

### AWS

Acts as the **Service Provider (SP)** in this federation scenario.

---

# 6. What is a SAML Assertion?

This is probably the most confusing term.

A **SAML Assertion** is basically a digitally signed message containing information about the authenticated user.

Think of it as a **trusted statement**.

For example:

```
AD FS says:

"This is Alice.
She successfully authenticated.
She belongs to the SoftwareArchitects group.
She can assume AWS role X."
```

That statement is the:

# SAML Assertion

---

# 7. Simple analogy

Imagine entering a university building.

You show your ID card to security.

Security checks it and gives you a pass:

```
"This person is Sadiq.
He is a student.
He can enter Building A."
```

You don't need to prove everything again at Building A.

The pass acts as trusted information.

Similarly:

```
AD FS
  ↓
SAML Assertion
  ↓
AWS
```

The assertion tells AWS:

> "This user has been authenticated and here's the information AWS needs."
> 

---

# 8. How everything connects

Here's the complete picture:

```
             ON-PREMISES

        ┌──────────────────┐
        │ Active Directory │
        │                  │
        │ Users/passwords  │
        └────────┬─────────┘
                 │
                 ▼
        ┌──────────────────┐
        │      AD FS       │
        │                  │
        │ Identity Provider│
        └────────┬─────────┘
                 │
                 │ SAML Assertion
                 ▼
              AWS
                 │
                 ▼
          IAM Federation
                 │
                 ▼
          Temporary AWS
             credentials
                 │
          ┌──────┴──────┐
          ▼             ▼
        S3            EC2
```

---

# 9. Real login example

Suppose Alice is a Software Architect.

Her company account is:

```
Alice
Password123
```

The credentials are stored in:

```
Active Directory
```

Alice wants to access AWS.

### Step 1 — Alice goes to the company's federation portal

```
Alice
  ↓
Company login
```

### Step 2 — AD FS authenticates her

```
Alice
  ↓
AD FS
  ↓
Active Directory
  ↓
Credentials valid ✅
```

### Step 3 — AD FS creates a SAML Assertion

Something conceptually like:

```
User = Alice
Group = SoftwareArchitects
Authentication = Successful
AWS Role = SoftwareArchitectRole
```

### Step 4 — Assertion is sent to AWS

```
AD FS
  │
  │ SAML Assertion
  ▼
AWS
```

### Step 5 — AWS trusts the assertion

AWS determines which IAM role Alice can assume.

```
Alice
 ↓
SoftwareArchitectRole
 ↓
Temporary AWS credentials
```

### Step 6 — Alice accesses AWS

```
Alice
 ↓
Temporary credentials
 ↓
S3 / EC2 / RDS / etc.
```

---

# 10. Why temporary credentials?

This is another important SAA concept.

Federated users generally don't need permanent IAM users.

Instead:

```
AD credentials
      ↓
SAML federation
      ↓
IAM Role
      ↓
Temporary AWS credentials
```

Temporary credentials are much better suited for federated workforce access.

---

# 11. What is IAM doing here?

IAM provides the AWS-side permissions.

For example:

```
IAM Role:
SoftwareArchitectRole

Permissions:
├── Read S3
├── Describe EC2
└── Read CloudWatch
```

AD FS says:

> "Alice is allowed to use SoftwareArchitectRole."
> 

AWS then provides temporary credentials associated with that role.

So:

```
AD → Authentication
AD FS → Federation
SAML → Communication
IAM Role → AWS permissions
```

This is a **very useful exam mental model**.

---

# 12. AD vs AD FS — don't confuse them

|  | Active Directory | AD FS |
| --- | --- | --- |
| Main purpose | Manage identities/users | Federation |
| Stores users? | ✅ | ❌ primarily |
| Stores passwords? | ✅ | ❌ |
| Authenticates against AD? | — | ✅ |
| Connects identity to external apps? | Not its main role | ✅ |
| Can participate in AWS SAML federation? | Through AD FS | ✅ |

### Remember:

> **AD = Identity store**
> 

> **AD FS = Federation service**
> 

---

# 13. SAML vs SAML Assertion

Don't confuse these either.

### SAML 2.0

The **standard/protocol**.

Think:

> "The rules/language for exchanging identity information."
> 

### SAML Assertion

The **actual message** sent during authentication.

Think:

> "The identity statement generated according to those rules."
> 

```
SAML 2.0
   ↓
defines how information is exchanged

SAML Assertion
   ↓
actual authentication information
```

---

# 14. SAML vs Web Identity Federation

This is **exactly relevant to your question**.

Your question says:

> "SAML 2.0-Based Federation by using a Web Identity Federation."
> 

❌ Wrong.

Why?

**Web Identity Federation** is generally for users authenticated by web/social identity providers such as:

- Amazon Cognito
- Google
- Facebook
- Other OIDC-compatible identity providers

Whereas the company says:

> **"Credentials are stored in Active Directory."**
> 

That points to:

# ✅ SAML 2.0 + AD FS

---

# 15. Why not IAM Users?

Question says:

> Users already have corporate credentials in Active Directory.
> 

Creating IAM users means:

```
Active Directory
   ↓
Corporate account

AWS IAM
   ↓
Separate AWS account
```

Now you're managing identities twice.

Instead:

```
Active Directory
      ↓
     AD FS
      ↓
     SAML
      ↓
 AWS IAM Role
```

Much better for workforce federation.

---

# 16. Why not Amazon VPC?

VPC is for **networking**.

```
VPC
├── Subnets
├── Route tables
├── Internet Gateway
├── NAT Gateway
└── Security architecture
```

It has nothing to do with federated authentication.

So:

❌ VPC

---

# 17. Why not Cognito?

Amazon Cognito is primarily designed for **application users/customer identities**.

For example:

```
E-commerce website
       ↓
Customers
       ↓
Cognito
       ↓
Application
```

Your question is about:

> **Employees / Software Architects using corporate Active Directory credentials to access AWS resources.**
> 

That's a **workforce identity federation** scenario.

Think:

```
Employees
   ↓
Corporate AD
   ↓
AD FS
   ↓
SAML
   ↓
AWS
```

---

# 18. Your question — complete answer

The company has:

```
On-premises resources
        +
AWS resources
```

Users already have:

```
Active Directory credentials
```

They want to use those credentials to access AWS.

Therefore:

```
Active Directory
       ↓
     AD FS
       ↓
   SAML 2.0
       ↓
AWS IAM federation
       ↓
IAM Role
       ↓
Temporary credentials
       ↓
AWS resources
```

### ✅ Correct answer:

> **Set up SAML 2.0-Based Federation by using Microsoft Active Directory Federation Service.**
> 

---

# 🧠 SAA-C03 Final Cheat Sheet

Memorize this chain:

```
👤 Employee
     ↓
🏢 Active Directory
     ↓
🔐 AD FS
     ↓
📜 SAML 2.0
     ↓
📄 SAML Assertion
     ↓
☁️ AWS
     ↓
👤 IAM Role
     ↓
⏳ Temporary Credentials
     ↓
🪣 EC2 / S3 / RDS / etc.
```

And these four definitions:

> **AD** → Stores company users and credentials.
> 

> **AD FS** → Federates those corporate identities to external services.
> 

> **SAML 2.0** → Standard/protocol used for the federation.
> 

> **SAML Assertion** → The trusted authentication/identity message sent from AD FS to AWS.
> 

### 🔥 Exam shortcut

**"Employees + existing Active Directory credentials + AWS access"**

→ **SAML 2.0 + AD FS**

**"Customers + application login"**

→ **Cognito**

**"Network connectivity between on-premises and AWS"**

→ **VPN / Direct Connect / VPC**

**"AWS permissions for federated users"**

→ **IAM Roles**, not IAM Users.
