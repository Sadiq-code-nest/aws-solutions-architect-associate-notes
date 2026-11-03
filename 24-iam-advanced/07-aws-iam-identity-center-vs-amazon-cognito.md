# AWS IAM Identity Center vs Amazon Cognito — Major Difference

The **easiest way to remember**:

> **IAM Identity Center = People inside your organization accessing AWS/accounts/apps.**
> 
> 
> **Cognito = Customers/users of your application logging into the application.**
> 

| Feature | **AWS IAM Identity Center** | **Amazon Cognito** |
| --- | --- | --- |
| **Main purpose** | Workforce identity / employee access | Customer identity / application user authentication |
| **Who uses it?** | Employees, administrators, developers, workforce users | Customers, mobile/web app users, external users |
| **Main question it solves** | **“Which AWS resources/apps can this employee access?”** | **“Who is this user of my application?”** |
| **AWS account access** | ✅ **Yes — primary purpose** | ❌ No |
| **AWS Management Console login** | ✅ Yes | ❌ No |
| **AWS CLI access** | ✅ Yes, through permission sets/roles | ❌ Not its purpose |
| **AWS services authorization** | ✅ Uses IAM roles/permission sets | ❌ Doesn't grant AWS IAM permissions to users |
| **Application login** | Can provide access to supported applications | ✅ **Primary purpose** |
| **User registration/signup** | ❌ Not designed for customer signup | ✅ Yes |
| **User login/logout** | For workforce access | ✅ Yes |
| **Social login** | Not the primary use case | ✅ Google, Apple, Facebook, etc. through federation options |
| **Username/password authentication** | Uses configured identity source | ✅ Yes |
| **MFA** | ✅ Yes | ✅ Yes |
| **SSO** | ✅ **SSO across AWS accounts and supported applications** | Can provide authentication/identity federation for applications |
| **Identity federation** | ✅ Microsoft AD, external IdP, etc. | ✅ External identity providers/social providers |
| **User directory** | Uses IAM Identity Center directory or external identity source | **Cognito User Pool** |
| **Groups** | ✅ Used to organize workforce permissions | ✅ Used to organize application users |
| **OAuth 2.0 / OIDC** | Not the main concept to memorize for AWS access | ✅ Important for application authentication |
| **JWT tokens** | Not the primary exam concept | ✅ Commonly used by Cognito for app authentication |
| **Application API authorization** | ❌ Not its primary role | ✅ Can integrate with API Gateway and applications |
| **Typical integration** | AWS Organizations + IAM roles + permission sets | Web/mobile app + API Gateway/Lambda/backend |
| **Typical users** | Employee / contractor / administrator | Customer / student / shopper / app user |
| **SAA-C03 keyword** | **Workforce access / AWS accounts / SSO** | **Web/mobile application users / customer authentication** |

---

# 1. IAM Identity Center — Think "Employee → AWS"

Imagine a company has:

```
Employee
   ↓
IAM Identity Center
   ↓
Choose AWS Account
   ↓
Choose Permission Set
   ↓
IAM Role
   ↓
AWS Resources
```

Example:

A company has:

- AWS Production account
- AWS Development account
- AWS Security account

An employee logs into **IAM Identity Center**.

They might see:

```
AWS Accounts

Production
   → ReadOnly

Development
   → AdministratorAccess

Security
   → SecurityAudit
```

The employee doesn't need separate IAM users/passwords in every AWS account.

### Key concept

**Permission Set** defines what access the workforce user receives in an AWS account.

So:

> **IAM Identity Center = centralized workforce access to AWS accounts and applications.**
> 

---

# 2. Cognito — Think "Customer → Application"

Imagine you build an online shopping application.

```
Customer
   ↓
Cognito
   ↓
Login / Signup
   ↓
Authentication
   ↓
Application
   ↓
API / Backend / Database
```

A customer creates an account:

```
Email: john@example.com
Password: ********
```

Cognito authenticates the customer.

The application can then determine:

```
User = John
User ID = 12345
Role = Customer
```

Cognito is designed to handle **application users**, not AWS account administrators.

---

# The Biggest Exam Difference

### If the question says:

> "Employees need single sign-on to multiple AWS accounts."
> 

Think:

**IAM Identity Center**

### If the question says:

> "Customers need to sign up and log in to a web/mobile application."
> 

Think:

**Amazon Cognito**

---

# Very Common SAA-C03 Scenarios

| Scenario | Choose |
| --- | --- |
| Employees need SSO to multiple AWS accounts | **IAM Identity Center** |
| Administrators need centralized access to AWS accounts | **IAM Identity Center** |
| Company uses Microsoft Active Directory for workforce AWS access | **IAM Identity Center** |
| Developers need access to AWS through centralized SSO | **IAM Identity Center** |
| Employees need access to supported SaaS/business applications | **IAM Identity Center** |
| Customers need to register for an application | **Cognito** |
| Customers need username/password login | **Cognito** |
| Mobile application needs user authentication | **Cognito** |
| Web application needs customer authentication | **Cognito** |
| Customers should login with Google/Facebook/Apple | **Cognito** |
| API Gateway needs authenticated application users | **Cognito** |
| Application needs OAuth/OIDC-based customer authentication | **Cognito** |

---

# Cognito's Two Important Components

Don't confuse these:

| Cognito Component | Purpose |
| --- | --- |
| **Cognito User Pools** | **Authentication** — users sign up/sign in and receive tokens |
| **Cognito Identity Pools** | **Temporary AWS credentials** — allows authenticated/guest users to access AWS resources |

### Memory trick

> **User Pool = Who are you?**
> 

> **Identity Pool = What AWS resources can you temporarily access?**
> 

For example:

```
Customer
   ↓
Cognito User Pool
   ↓
Authenticated
   ↓
Cognito Identity Pool
   ↓
Temporary AWS credentials
   ↓
S3 / other AWS resources
```

---

# Don't Mix IAM Identity Center With Cognito Identity Pools

This is a particularly important distinction.

|  | IAM Identity Center | Cognito Identity Pool |
| --- | --- | --- |
| Main audience | Workforce | Application users |
| AWS access | ✅ | ✅ |
| Typical purpose | Employee AWS access | Give app users temporary AWS credentials |
| Example | Developer accesses EC2 console | Mobile app user uploads directly to S3 |
| Central AWS account SSO | ✅ | ❌ |
| Customer authentication | ❌ | Works with Cognito/user identity federation |
| Permission mechanism | Permission Sets → IAM Roles | IAM Roles / temporary credentials |

---

# One Simple Picture to Remember

```
                 AWS ORGANIZATION
                       │
                       ▼
             IAM Identity Center
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Account A    Account B    Account C
          │            │            │
       IAM Role     IAM Role     IAM Role
          │            │            │
       AWS Resources / Services

       👆 EMPLOYEES / WORKFORCE
```

Versus:

```
                 YOUR APPLICATION
                       │
                 ┌─────▼─────┐
                 │  Cognito  │
                 └─────┬─────┘
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
          Signup     Login     Social Login
             │         │         │
             └─────────┼─────────┘
                       ▼
                  Application
                       │
                 API / Backend
                       │
                  AWS Services

                  👆 CUSTOMERS
```

## ⭐ Exam-night memory

> **IAM Identity Center → Workforce → AWS accounts → Permission Sets → IAM Roles**
> 

> **Cognito → Customers → Web/Mobile apps → Signup/Login → User Pools**
> 

> **Cognito Identity Pool → Application users → Temporary AWS credentials**
> 

If you remember those **three lines**, you can distinguish most IAM Identity Center vs Cognito questions in SAA-C03.
