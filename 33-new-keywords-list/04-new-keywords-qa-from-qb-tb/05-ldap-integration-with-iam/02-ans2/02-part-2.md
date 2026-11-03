# 7. Clue-by-Clue Post-Mortem

### "on-premises MySQL..." — **Not present here**

No database issue is involved.

The question is entirely about **identity integration**.

---

### "Lightweight Directory Access Protocol (LDAP) directory service"

🔥 Trigger:

```
Existing corporate directory
       ↓
External identity
       ↓
Federation
```

This eliminates the idea of simply creating independent IAM users.

---

### "from the on-premises data center to the AWS VPC"

The identity system lives **outside AWS**.

Therefore AWS needs a mechanism to trust/authenticate an external identity source.

Think:

```
On-premises
     ↓
Federation
     ↓
AWS
```

---

### "using IAM"

This tells you AWS authorization still ultimately involves:

```
IAM Roles
IAM Policies
```

But IAM itself isn't an LDAP directory.

---

### "identity store ... is not compatible with SAML"

🔥🔥🔥 **This is the decisive clue.**

Normally:

```
Identity Provider
       ↓ SAML
      AWS
```

But:

```
No SAML
   ↓
Need another federation mechanism
   ↓
Custom Identity Broker
   ↓
STS
```

This strongly confirms **Option B**.

---

### "most valid approach"

This means we should choose the architecture that actually bridges the incompatible systems, rather than a workaround such as credential synchronization.

---

# 8. Why the Tempting Wrong Answers Are Tempting

## Trap #1 — IAM Identity Center

This is probably the most tempting.

A rushed reader sees:

> "Identity store + AWS access"
> 

and immediately thinks:

> **IAM Identity Center!**
> 

That's reasonable in many modern AWS questions.

But the question deliberately gives you:

> **"not compatible with SAML."**
> 

The exam is testing whether you know the **legacy/custom federation pattern**.

So:

```
Supported identity integration
        ↓
IAM Identity Center / federation
```

versus:

```
Non-SAML legacy identity
        ↓
Custom Identity Broker
        ↓
STS
```

---

## Trap #2 — IAM credentials + LDAP synchronization

This sounds practical:

> "Whenever LDAP credentials change, update the IAM credentials."
> 

But it's actually a terrible federation design.

You're creating **two credential systems** and trying to keep them synchronized.

The better model is:

```
LDAP
 ↓
Authenticate once
 ↓
STS
 ↓
Temporary AWS credentials
```

rather than:

```
LDAP
 ↓
Copy/synchronize credentials
 ↓
IAM long-term credentials
```

---

# 9. Final Answer Recap + One-Line Memory Trick

## ✅ Correct Answer: Option B

> **Develop an on-premises custom identity broker application and use STS to issue short-lived AWS credentials.**
> 

### The architecture

```
          ON-PREMISES
┌──────────────────────────┐
│ LDAP Directory            │
│ Users / Groups            │
└────────────┬─────────────┘
             │
             │ Authenticate
             ▼
┌──────────────────────────┐
│ Custom Identity Broker   │
└────────────┬─────────────┘
             │
             │ Request temporary credentials
             ▼
┌──────────────────────────┐
│ AWS STS                  │
└────────────┬─────────────┘
             │
             │ Temporary credentials
             ▼
┌──────────────────────────┐
│ IAM Role                 │
└────────────┬─────────────┘
             │
             ▼
       AWS Resources
```

### 🧠 Memory Tricks

> **"LDAP + No SAML → Custom Identity Broker + STS."**
> 

And:

> **"Federation = temporary credentials, not synchronized IAM passwords."**
> 

---

# 10. Bonus: Related Exam Traps to Watch For

### Scenario 1

**Corporate IdP supports SAML and employees need AWS access.**

Think:

```
SAML Federation
```

—not custom LDAP broker.

---

### Scenario 2

**Company wants centralized workforce access across many AWS accounts.**

Think:

```
IAM Identity Center
```

—not necessarily a custom identity broker.

---

### Scenario 3

**Application running on EC2 needs access to S3 without storing credentials.**

Think:

```
IAM Role attached to EC2
```

—not STS manually implemented by the application.

---

### Scenario 4

**Application needs temporary AWS credentials to access resources.**

Think:

```
AWS STS
```

---

### Scenario 5

**Question asks which service defines what actions a user/role can perform.**

Think:

```
IAM Policy
```

Remember:

```
Authentication → "WHO are you?"
Authorization  → "WHAT can you do?"
```

---

# 🔥 FINAL FLASHCARD — LDAP FEDERATION DECISION TABLE

| If you see this phrase/clue in a question... | Think of this service/feature |
| --- | --- |
| **LDAP + AWS federation** | Identity federation |
| **LDAP doesn't support SAML** | **Custom Identity Broker** |
| **Custom broker needs AWS credentials** | **STS** |
| **Temporary/short-lived AWS credentials** | **STS** |
| **Federated user permissions** | **IAM Role** |
| **Allow/Deny AWS actions** | **IAM Policy** |
| **SAML-compatible corporate IdP** | **SAML Federation** |
| **Centralized workforce access across AWS accounts** | **IAM Identity Center** |
| **Synchronize LDAP passwords to IAM credentials** | ❌ Avoid |
| **IAM policy references LDAP user IDs** | ❌ Not authentication |
| **LDAP → Broker → STS → AWS** | ✅ **Correct pattern** |

## 🎯 10-second SAA decision tree

```
External corporate identity?
          │
         YES
          │
          ▼
Does it support SAML?
      │           │
     YES          NO
      │            │
      ▼            ▼
 SAML Federation  Custom Identity
                  Broker
                     │
                     ▼
                    STS
                     │
                     ▼
                 IAM Role
                     │
                     ▼
               AWS Resources
```

**For this question:**

```
LDAP
 ↓
NO SAML
 ↓
Custom Identity Broker
 ↓
STS
 ↓
Short-lived credentials
 ↓
IAM Role
 ↓
AWS
 ↓
✅ OPTION B
```
