# Service Control Policy (SCP)

Sets maximum available permissions and restricts AWS services/actions for accounts/OUs

Then **SCPs** act like guardrails:

```
Organization
     ↓
SCP
     ↓
"These services/actions are allowed"
"These services/actions are prohibited"
     ↓
Member Accounts
```

### Important distinction

An **SCP does not grant permissions to users**.

Instead:

> **SCP = maximum permission boundary/guardrail for accounts**
> 

IAM policies are still needed to actually grant users/roles permissions.

### Understand the role of SCP vs IAM

This is a very important SAA-C03 concept.

```
SCP
 ↓
Maximum permissions / guardrail
```

while:

```
IAM Policy
 ↓
Actually grants permissions
```

For an action to succeed:

```
SCP allows it
      +
IAM allows it
      ↓
Action can be performed
```

If the SCP explicitly denies it:

```
SCP
 ↓
DENY
 ↓
❌ IAM cannot override it
```

Therefore, SCPs are excellent for enforcing **organization-wide security guardrails**.
