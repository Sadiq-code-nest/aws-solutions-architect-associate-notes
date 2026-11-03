# Organizations vs Control Tower

### AWS Organizations

Focus:

> **Manage accounts**
> 

```
Organization
 ├── Account
 ├── Account
 └── Account
```

Features:

- Accounts
- OUs
- SCP
- Consolidated billing

---

### Control Tower

Focus:

> **Set up and govern a well-architected multi-account environment**
> 

It uses/works with AWS Organizations and other AWS services to establish governance.

Think:

```
Organizations
      ↓
Account structure
      +
Control Tower
      ↓
Governed landing zone
```
