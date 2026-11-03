# What is SAML 2.0?

## SAML = Security Assertion Markup Language

**SAML 2.0** is an open standard used to exchange **authentication and authorization information** between an identity provider and a service provider.

Don't let the name scare you.

Think of SAML simply as:

> **A standardized way for one system to tell another system: "This user has successfully authenticated, and here is what they're allowed to access."**
> 

---

# 3. Very simple SAML example

Imagine you work for a company.

You already logged into:

```
Corporate Active Directory
```

Now you want to access AWS.

Instead of AWS asking:

```
AWS username?
AWS password?
```

AWS trusts your company's identity system.

The flow becomes:

```
You
 ↓
Corporate login
 ↓
Active Directory
 ↓
AD FS
 ↓
SAML assertion
 ↓
AWS
 ↓
IAM Role
 ↓
AWS Console/resources
```

You don't need a separate AWS password.

That's the key idea.

---

# 4. What is a SAML Assertion?

This is another important term.

A **SAML assertion** is essentially a digitally signed message containing information about the authenticated user.

Conceptually:

```
AD FS
  │
  │ "I authenticated this user."
  │
  │ "This user is SoftwareArchitect."
  │
  ▼
SAML Assertion
  │
  ▼
AWS
```

AWS trusts the identity provider and uses the information in the assertion to determine what AWS role the user can assume.

---

# 5. Don't think of SAML as the authentication system itself

This distinction is useful.

SAML is primarily the **protocol/standard for exchanging identity information**.

The actual authentication might happen in:

```
Active Directory
```

AD FS then participates in federation and generates the SAML response/assertion.

So:

```
Active Directory
       ↓
Authentication
       ↓
AD FS
       ↓
SAML
       ↓
AWS
```
