# AWS SAA-C03 Notes: IAM Role Switching

[AWS Identity and Access Management (IAM) Deep Dive | by Abhishek_Jirage | Medium](https://images.openai.com/static-rsc-4/EymdTUGqGkGDSgyy7arH04pm4hfk_xPUOAVLbS9tyc--5wv2NFRMxViUbGx9fJNG8sIVlZ6xxDyOND0KIR3uOs3-RJkamN9FoNK6PVehZ3c3uP13zyyv9rFSaPXtYyyIURtRD0pWp7CyTYc4V3FzqCe4h04LvpusNgObjagaqcc?purpose=inline)

# IAM Role Switching

Identity and Access Management · Cross-account access · Least privilege

## 1. What is IAM role switching?

IAM role switching allows a user to switch from their current identity to an IAM role, gaining the permissions associated with that role without needing a separate IAM user in every AWS account.

It is especially useful when a company has multiple AWS accounts and employees need different levels of access in each account.

### Real-world example

A company has three AWS accounts:

- Development account: Developers deploy and test applications.
- Production account: Only authorized engineers can manage production resources.
- Security account: Security engineers investigate security findings.

One engineer uses their normal company identity to sign in and switches roles depending on the task.

User signs in

Uses their existing identity

Switch role

Dev role

Deploy and test

Prod role

Restricted access

Security role

Investigate findings

Each role provides its own set of permissions.

## 2. How does IAM role switching work?

The key mechanism is AWS Security Token Service (AWS STS), which provides temporary security credentials when a principal assumes a role.

1. The user signs in.
    
    The user authenticates through an authorized identity, such as an IAM user or a federated identity.
    
2. The user chooses a target role.
    
    For example, the user selects `ProductionReadOnlyRole` in another AWS account.
    
3. AWS checks permissions and trust.
    
    The caller must be allowed to assume the role, and the target role's trust policy must trust the caller or their account/identity as configured.
    
4. STS issues temporary credentials.
    
    The user receives temporary credentials associated with the assumed role.
    
5. The user acts with the role's permissions.
    
    The user can perform permitted actions until the session expires or ends.
    

### Important distinction

- IAM role switching: The user-facing capability for changing to an authorized role.
- AWS STS `AssumeRole`: The API mechanism that provides temporary credentials for the role session.
- IAM role trust policy: Defines who can assume the role.
- Role permissions policy: Defines what the role can do.

## 3. What needs to be configured?

For cross-account role switching, remember these two essential permission checks.

| Configuration | Purpose |
| --- | --- |
| Source identity's permissions | Allows the user or source role to call `sts:AssumeRole` on the target role |
| Target role's trust policy | Allows the source principal to assume the target role |
| Target role's permissions | Defines which actions the user can perform after switching |
| Optional MFA / conditions | Adds stronger authentication or restrictions where required |

Example:

Account A contains the user's original identity. Account B contains a role called `ProductionReadOnlyRole`.

The target role's trust policy can allow an authorized principal from Account A to assume it. The role's permissions policy can then grant read-only access to selected production resources.

The exact policy must be configured according to the company's security requirements.

## 4. Benefits of IAM role switching

- One identity, multiple accounts: Avoids creating and maintaining a separate IAM user in every account.
- Temporary credentials: Reduces reliance on long-lived credentials for role access.
- Least privilege: Users can switch into roles with only the permissions needed for each task.
- Easy account navigation: Users can switch between authorized roles in the AWS Management Console.
- Centralized access management: Access can be managed through roles and trust relationships.

## 5. IAM role switching vs. other AWS services

| Option | Main purpose | When to recognize it |
| --- | --- | --- |
| IAM role switching | Switch into an authorized role | User needs to move between roles/accounts easily |
| AWS STS | Issue temporary credentials | Programmatic role assumption and temporary access |
| IAM users per account | Separate long-term identities | Usually unnecessary when role-based access is appropriate |
| AWS Organizations | Manage multiple accounts centrally | Account governance, organizational units and consolidated management |
| Service Control Policies (SCPs) | Set maximum available permissions for accounts/OUs | Restrict what identities in member accounts can do; does not grant permissions itself |
| IAM Identity Center | Centrally manage workforce access to multiple AWS accounts | Employees need single sign-on and centrally assigned account/role access |

Exam nuance: IAM role switching is the direct answer when the question emphasizes users easily switching between specified roles. IAM Identity Center is also an important solution when the scenario explicitly emphasizes centralized workforce SSO across many AWS accounts.

## 6. Exam trigger phrases

| If the question says… | Think… |
| --- | --- |
| “Quickly switch between AWS accounts” | IAM role switching |
| “Assume a role in another account” | IAM role + trust policy + `sts:AssumeRole` |
| “Temporary security credentials” | AWS STS |
| “Avoid creating IAM users in every account” | Role-based access |
| “Centrally manage employee access across many AWS accounts with SSO” | IAM Identity Center |
| “Apply permission guardrails across organizational units” | AWS Organizations + SCPs |

## 7. Your reference question — answer and reasoning

Correct answer: Use the IAM role-switching feature to allow users to switch between roles within and between AWS accounts.

Why the other options are weaker:

- Create IAM users for each account: Adds unnecessary identity and credential management overhead.
- Use STS to generate credentials for each user and role: STS is the underlying credential mechanism, but the question asks for an easy role-switching strategy.
- Use Organizations and SCPs: Useful for account management and permission guardrails, but SCPs do not provide the user-facing role-switching capability.

## 8. Memory hooks

Role switching = Change your hat

A developer puts on the “Dev hat” to deploy code and the “Production ReadOnly hat” to inspect production.

STS = Temporary keys

STS supplies temporary credentials for the assumed role.

Trust = Who? Permissions = What?

Trust policy answers who may assume the role; the permissions policy answers what the role can do.

Final exam takeaway: If users must securely and easily switch between roles across AWS accounts, choose IAM role switching. Understand that cross-account access relies on role trust, permission to assume the role, and temporary credentials issued through STS.
