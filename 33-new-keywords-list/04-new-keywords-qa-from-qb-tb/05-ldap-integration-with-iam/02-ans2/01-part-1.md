# AWS SAA-C03 Post-Mortem — LDAP Integration with IAM

## 1. Restate the Scenario in Plain English

The company already has an **LDAP directory on-premises** that stores user identities and credentials.

It wants those users to access AWS resources in an AWS VPC **without creating and manually managing a separate IAM user for every LDAP user**.

The important complication is:

> The existing identity store **does not support SAML**.
> 

So the architect needs another way to connect:

```
On-Premises Data Center
┌──────────────────────┐
│ LDAP Directory       │
│ Users + Credentials  │
└──────────┬───────────┘
           │
           │ Authentication
           ▼
        AWS IAM
           │
           ▼
     AWS Resources
```

The key problem is **identity federation**:

```
LDAP users
   ↓
Authenticate using existing LDAP
   ↓
Obtain temporary AWS credentials
   ↓
Access AWS resources
```

The goal is to avoid permanently copying LDAP credentials into IAM.

---

# 2. Identify the Hidden Sub-Problems

There are really two problems hidden in the question.

| Symptom | Underlying Category | Why |
| --- | --- | --- |
| Existing users are stored in on-premises LDAP | **External identity integration/federation** | AWS needs to trust an external identity source |
| Identity store doesn't support SAML | **Custom identity broker / non-SAML federation** | Standard SAML-based federation isn't available |
| Users need AWS access | **Temporary AWS credentials** | Avoid long-lived IAM credentials |
| LDAP credentials shouldn't become IAM credentials | **STS federation** | STS can issue temporary AWS credentials |
| Minimize credential management | **IAM roles + STS** | Users assume roles rather than maintaining IAM users |

The crucial chain is:

```
LDAP
 ↓
Custom Identity Broker
 ↓
AWS STS
 ↓
Temporary Credentials
 ↓
IAM Role
 ↓
AWS Resources
```

That points strongly toward **Option B**.

---

# 3. Explain Every Option

## Option A — Rotate IAM credentials when LDAP credentials change

### What is it trying to say?

Create IAM credentials for the users and synchronize those credentials whenever LDAP credentials change.

```
LDAP
 │
 │ credential changes
 ▼
Synchronization process
 │
 ▼
IAM User Credentials
 │
 ▼
AWS
```

### Verdict: ❌ Incorrect

This is not a proper federation architecture.

The company already has an external identity system. Creating and synchronizing separate IAM credentials creates unnecessary:

- credential duplication
- credential-management overhead
- security risk
- synchronization complexity

More importantly, **IAM roles are intended to provide temporary access without requiring long-term IAM user credentials**.

The question is asking for integration with LDAP, not credential synchronization.

### Exam takeaway

> **Don't synchronize LDAP passwords with IAM credentials. Federate identities and issue temporary credentials instead.**
> 

---

# Option B — Custom identity broker + STS

### What is it trying to say?

Build an application inside the on-premises environment that authenticates users against LDAP and then asks AWS STS for temporary AWS credentials.

```
┌─────────────────┐
│ LDAP Directory  │
└────────┬────────┘
         │
         ▼
┌──────────────────────┐
│ Custom Identity      │
│ Broker               │
└────────┬─────────────┘
         │
         │ AssumeRole / STS
         ▼
┌──────────────────────┐
│ AWS STS              │
│ Temporary credentials│
└────────┬─────────────┘
         │
         ▼
┌──────────────────────┐
│ IAM Role             │
└────────┬─────────────┘
         │
         ▼
    AWS Resources
```

### Verdict: ✅ Correct

This is the classic solution when:

- the existing identity provider doesn't support SAML
- you need AWS federation
- users are already authenticated by an on-premises directory
- you want temporary AWS credentials

The custom identity broker acts as the bridge between:

```
LDAP world
    ↕
AWS federation world
```

### Why STS?

AWS Security Token Service (STS) can issue **temporary security credentials**.

Conceptually:

```
LDAP authentication
       ↓
Identity broker validates user
       ↓
STS
       ↓
Temporary Access Key
Temporary Secret Key
Temporary Session Token
       ↓
AWS resources
```

This is much safer than handing users permanent IAM access keys.

### Why this option wins

The question explicitly says:

> **"identity store ... is not compatible with SAML."**
> 

That's the major discriminator.

If SAML were supported, standard federation could be used.

But when SAML isn't available:

```
No SAML
  ↓
Custom Identity Broker
  ↓
STS
```

---

# Option C — IAM Identity Center

### What is it trying to say?

Use AWS IAM Identity Center as the central identity-management system and connect it directly to LDAP.

```
LDAP
  │
  ▼
IAM Identity Center
  │
  ▼
AWS Accounts
  │
  ▼
AWS Resources
```

### Verdict: ❌ Incorrect

The problem is the **LDAP integration requirement**.

IAM Identity Center is designed around supported identity sources and federation mechanisms. The question specifically tells us that the existing identity store **isn't SAML-compatible**.

The intended architecture for this classic SAA scenario is therefore the **custom identity broker + STS** pattern.

### Why this is tempting

"IAM Identity Center manages AWS user access" is true.

But:

> **The existence of a service that manages identities does not mean it can directly integrate with every identity store.**
> 

You must check the integration protocol.

### Exam trap

Don't think:

```
Identity problem
     ↓
IAM Identity Center
```

automatically.

Instead ask:

```
What identity protocol does the existing directory support?
```

---

# Option D — IAM policy referencing LDAP identifiers

### What is it trying to say?

Create an IAM policy that somehow directly references LDAP users or identifiers.

```
LDAP User
    │
    ▼
IAM Policy
    │
    ▼
AWS Resource
```

### Verdict: ❌ Incorrect

IAM policies don't authenticate LDAP users.

An IAM policy answers:

> **"What is this AWS principal allowed to do?"**
> 

It doesn't answer:

> **"How do I authenticate this LDAP user?"**
> 

Authentication and authorization are different.

```
Authentication
"Who are you?"
        ↓
LDAP / Identity Provider

Authorization
"What are you allowed to do?"
        ↓
IAM policies / IAM roles
```

LDAP identifiers cannot simply be inserted into an IAM policy to establish federation.

---

# 4. Deep-Dive Every AWS Concept Touched

## 4.1 LDAP

### Definition

LDAP (Lightweight Directory Access Protocol) is a protocol commonly used to access and manage directory information such as users, groups, and credentials in an organizational directory.

```
Application
    │
    │ LDAP query/authentication
    ▼
LDAP Directory
 ┌───────────────┐
 │ Users         │
 │ Groups        │
 │ Credentials   │
 └───────────────┘
```

### Example

A company has:

```
LDAP
├── Alice
├── Bob
├── Charlie
└── Developers
```

The company wants Alice to access AWS without creating:

```
IAM User: Alice
IAM Password: ...
IAM Access Key: ...
```

Instead:

```
Alice
 ↓
LDAP authentication
 ↓
Identity Broker
 ↓
AWS temporary credentials
```

### LDAP vs Active Directory

| LDAP | Active Directory |
| --- | --- |
| Protocol | Directory service/product |
| Used for directory access | Microsoft's directory platform |
| Can be used by many directory systems | Common enterprise identity platform |
| LDAP protocol can interact with AD | AD supports LDAP |

### Exam trigger

> **"On-premises directory + existing users + AWS access" → think identity federation.**
> 

---

# 4.2 SAML

### Definition

SAML (Security Assertion Markup Language) is a standard used to exchange authentication/identity information between an identity provider and a service provider.

Typical AWS federation:

```
User
 │
 ▼
Identity Provider
 │
 │ SAML assertion
 ▼
AWS
 │
 ▼
IAM Role
 │
 ▼
Temporary credentials
```

### Example

An enterprise identity provider supports SAML.

Then users can authenticate through the corporate identity system and federate into AWS.

### SAML vs custom identity broker

| SAML Federation | Custom Identity Broker |
| --- | --- |
| Standard federation protocol | Custom application acts as bridge |
| Identity provider supports SAML | Useful when existing directory doesn't |
| No need to build custom translation layer | Application handles authentication/federation |
| Common enterprise approach | Legacy/non-SAML integration scenario |

### Exam trigger

> **"SAML-compatible identity provider" → SAML federation.**
> 

And:

> **"Identity store doesn't support SAML" → consider custom identity broker + STS.**
> 

---

# 4.3 IAM Roles

### Definition

An IAM role is an AWS identity with permissions that can be **assumed by trusted entities** instead of being permanently associated with a user's long-term credentials.

```
User / Federation
       │
       ▼
   Assume Role
       │
       ▼
 IAM Role
       │
       ▼
 AWS Resources
```

### Example

An LDAP user authenticates through the broker.

The broker allows the user to assume:

```
arn:aws:iam::123456789012:role/Developers
```

The role grants:

```
S3 read
EC2 describe
CloudWatch read
```

### IAM Role vs IAM User

| IAM User | IAM Role |
| --- | --- |
| Long-term identity | Assumable identity |
| Can have long-term credentials | Provides temporary credentials when assumed |
| Common for individual AWS identities/legacy workloads | Excellent for federation and AWS workloads |
| Credential management required | Temporary credentials reduce long-term credential exposure |

### Exam trigger

> **"Temporary credentials / federation / assume role" → IAM Role.**
> 

---

# 4.4 AWS STS

### Definition

AWS Security Token Service (STS) provides temporary security credentials for accessing AWS resources.

```
Identity Broker
      │
      │ Request temporary credentials
      ▼
     STS
      │
      ▼
Temporary credentials
      │
      ▼
   AWS APIs
```

Those credentials generally contain:

```
Access Key ID
Secret Access Key
Session Token
```

### Example

Bob authenticates using LDAP.

```
Bob
 ↓
LDAP
 ↓
Identity Broker
 ↓
STS
 ↓
Temporary AWS credentials
 ↓
S3 / EC2 / DynamoDB
```

The credentials expire rather than remaining valid indefinitely.

### STS vs IAM

| STS | IAM |
| --- | --- |
| Issues temporary credentials | Defines identities and permissions |
| Security Token Service | Identity and Access Management |
| Federation mechanism | Authorization/control |
| Temporary access | Roles/policies/users |

### Exam trigger

> **"Temporary AWS credentials" → STS.**
> 

---

# 4.5 Custom Identity Broker

### Definition

A custom identity broker is an application that sits between an external identity system and AWS, authenticates users against the organization's identity store, and obtains temporary AWS credentials from STS.

```
Corporate Directory
       │
       ▼
Identity Broker
       │
       ▼
     AWS STS
       │
       ▼
Temporary Credentials
       │
       ▼
AWS Resources
```

### Example

LDAP doesn't speak the federation protocol required by the AWS federation mechanism.

So:

```
LDAP
 ↓
Custom Broker
 ↓
STS
 ↓
IAM Role
 ↓
AWS
```

### Custom Broker vs IAM Identity Center

| Custom Identity Broker | IAM Identity Center |
| --- | --- |
| Custom-built integration | Managed AWS identity service |
| Useful for legacy/non-SAML identity stores | Best with supported identity sources |
| Developer/operational effort required | Less infrastructure to manage |
| Directly handles federation logic | Centralized AWS access management |

### Exam trigger

> **"Existing identity store doesn't support SAML" + "federate to AWS" → Custom Identity Broker + STS.**
> 

---

# 4.6 IAM Policies

### Definition

IAM policies are JSON-based permission documents that define which AWS actions a principal can perform on which resources.

Conceptually:

```
Principal
   │
   ▼
IAM Policy
   │
   ├── Allow s3:GetObject
   ├── Allow s3:ListBucket
   └── Deny ...
```

### Example

```
Role
 ↓
Policy
 ↓
Allow:
  s3:GetObject
  s3:ListBucket
```

### IAM Policy vs Authentication

| Authentication | Authorization |
| --- | --- |
| Determines who you are | Determines what you can do |
| LDAP / IdP / federation | IAM policies |
| Identity verification | Permissions |
| "Who?" | "What actions?" |

### Exam trigger

> **"Allow/Deny AWS API actions" → IAM policy.**
> 

---

# 4.7 IAM Identity Center

### Definition

AWS IAM Identity Center provides centralized workforce access to AWS accounts and applications using identities from supported identity sources.

Conceptually:

```
Users
  │
  ▼
IAM Identity Center
  │
  ├── AWS Account A
  ├── AWS Account B
  └── AWS Account C
```

It is particularly useful for centralized workforce access across multiple AWS accounts.

### Why not here?

The question's key constraint is the **non-SAML-compatible identity store**, making the custom broker pattern the intended solution.

### IAM Identity Center vs Custom Broker

| IAM Identity Center | Custom Identity Broker |
| --- | --- |
| Managed AWS service | Custom-developed application |
| Central workforce access | Bridge for unsupported/legacy identity systems |
| Supported identity integrations | Can authenticate against LDAP |
| Lower custom-management burden | More operational responsibility |

### Exam trigger

> **"Centralized workforce access to multiple AWS accounts" → IAM Identity Center.**
> 

---

# 5. Master Comparison Table

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **On-premises LDAP users need AWS access** | Identity federation |
| **Identity provider supports SAML** | SAML federation |
| **Identity store doesn't support SAML** | **Custom Identity Broker** |
| **Need temporary AWS credentials** | **AWS STS** |
| **User assumes an AWS role** | **IAM Role** |
| **External users access AWS without IAM users** | Federation + IAM Roles |
| **Centralized workforce access across AWS accounts** | **IAM Identity Center** |
| **Allow/Deny AWS API operations** | **IAM Policy** |
| **"Who are you?"** | Authentication |
| **"What are you allowed to do?"** | Authorization |
| **Legacy directory needs AWS federation** | Custom Identity Broker |
| **Temporary Access Key + Secret + Session Token** | STS |
| **LDAP credentials synchronized with IAM credentials** | ❌ Bad architecture |

---

# 6. Side-by-Side Option Analysis Table

| Option | Underlying Idea | Good? | One-line reason |
| --- | --- | --- | --- |
| **A** | Synchronize LDAP credentials with IAM credentials | ❌ | Creates unnecessary long-term credential management instead of federation |
| **B** | Custom identity broker authenticates LDAP users and uses STS | ✅ | Correct pattern for a non-SAML identity store requiring temporary AWS credentials |
| **C** | IAM Identity Center directly manages LDAP access | ❌ | Doesn't fit the stated non-SAML LDAP integration requirement |
| **D** | IAM policy references LDAP identifiers | ❌ | IAM policies authorize AWS actions; they don't authenticate LDAP identities |

---

#
