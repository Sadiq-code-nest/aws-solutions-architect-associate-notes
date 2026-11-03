# What is CloudFront Field-Level Encryption?

Field-Level Encryption protects **sensitive fields in HTTP POST requests**.

For example, suppose a customer submits:

```
Name: Sadiq
Email: sadiq@example.com
CreditCard: 1234...
```

You might want to encrypt only:

```
CreditCard
```

before it travels through the application infrastructure.

Conceptually:

```
User
 ↓
CloudFront
 ↓
Encrypt sensitive field
 ↓
Application
```

It is about:

> **Protecting sensitive data fields**
> 

not:

> **Controlling who can access private media files.**
> 

Therefore:

❌ Wrong.

---

# 21. Field-Level Encryption vs Signed Cookies

Very important:

### Field-Level Encryption

```
Protect sensitive fields
```

Examples:

- Credit card information
- Personal information
- Sensitive form data

### Signed Cookie

```
Control access to private CloudFront content
```

Examples:

- Premium videos
- Paid images
- Private documents
- Subscriber-only media
