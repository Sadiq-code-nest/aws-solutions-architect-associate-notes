# Lightweight Directory Access Protocol (LDAP)

## LDAP

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
