# What is Microsoft Active Directory?

You probably already know this, but let's connect it to AWS.

**Microsoft Active Directory (AD)** is commonly used by organizations to centrally manage:

- Users
- Passwords
- Groups
- Computers
- Policies
- Authentication

Example:

```
Company Active Directory

Users:
 ├── Sadiq
 ├── Alice
 ├── Bob
 └── John

Groups:
 ├── Developers
 ├── Software Architects
 ├── Finance
 └── Administrators
```

The company already has all its employees there.

The question wants AWS to trust that existing identity system.

---

# 7. What is Microsoft AD FS?

Now the important part.

## AD FS = Active Directory Federation Services

Microsoft **AD FS** is a Microsoft service that enables **federated identity and SSO**.

In simple terms:

> **AD FS acts as a bridge between your company's Active Directory and external applications/services that trust federated identities.**
> 

For this question:

```
On-premises
────────────────────

Active Directory
       ↓
      AD FS
       ↓
    SAML 2.0
       ↓
────────────────────
AWS
```

AD FS allows your organization's existing AD identities to be used to access external services such as AWS.

---

# 8. AD vs AD FS

This is extremely important.

### Active Directory

Manages your corporate identities.

```
AD
│
├── Users
├── Passwords
├── Groups
└── Computers
```

### AD FS

Provides federation/SSO between AD and external applications.

```
AD
 ↓
AD FS
 ↓
External service
```

So:

> **AD = identity directory**
> 

> **AD FS = federation/SSO bridge**
> 

---

# 9. Real-world analogy

Imagine your company has a security desk.

### Active Directory

The security desk has the employee database:

```
Employee:
Sadiq

Department:
Software Architecture

Employee ID:
12345
```

### AD FS

AD FS is the security officer who tells another building:

> "Yes, Sadiq is a valid employee, and he is a Software Architect."
> 

### SAML

SAML is the standardized document/message used to communicate that information.

### AWS

AWS is the other building.

So:

```
Active Directory
      │
      ▼
     AD FS
      │
      ▼
SAML assertion
      │
      ▼
     AWS
```

---

#
