# Organizations vs. RAM

### Organizations

> "These accounts belong to my company and I want to centrally manage them."
> 

### RAM

> "Now I want to share supported resources between those accounts."
> 

Together:

```
        AWS Organizations
                │
       Manage the accounts
                │
       ┌────────┼────────┐
       │        │        │
      Dev      Prod    Security
       │        │        │
       └────────┼────────┘
                │
               RAM
                │
      Share supported resources
```
