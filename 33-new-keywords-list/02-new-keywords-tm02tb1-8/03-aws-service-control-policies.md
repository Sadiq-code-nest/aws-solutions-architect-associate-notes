# AWS Service Control Policies

### What about SCP?

**Service Control Policies (SCPs)** are used to establish maximum permission boundaries for accounts/OUs in an AWS Organization.

For example:

```
SCP
 ↓
Deny deleting CloudTrail
 ↓
Accounts/OUs
```

SCPs answer:

> **“What actions can accounts in my organization be allowed to perform?”**
> 

They do **not** answer:

> **“How do I consolidate my accounts?”**
> 

And they aren't required merely because the company wants centralized login.

### Exam lesson

> **SCP = permission guardrail, not account consolidation or user authentication.**
>
