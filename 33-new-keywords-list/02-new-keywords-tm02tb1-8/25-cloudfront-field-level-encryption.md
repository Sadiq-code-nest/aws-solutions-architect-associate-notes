# CloudFront Field-Level Encryption

### Definition

CloudFront Field-Level Encryption encrypts sensitive fields in POST/PUT requests before forwarding them to the origin. It is intended to protect specific pieces of submitted data rather than control who can access an object.

```
User
 │
 │ POST request
 ▼
CloudFront
 │
 │ Encrypt selected fields
 ▼
Origin
```

### Worked example

A checkout application sends:

```
{
  "name": "Alice",
  "cardNumber": "..."
}
```

Only `cardNumber` may be encrypted.

### Field-Level Encryption vs CloudFront Signed Cookies

| Field-Level Encryption | Signed Cookie |
| --- | --- |
| Protects sensitive request fields | Controls access to private content |
| Encrypts selected data | Authorizes viewer |
| Primarily request → origin | Viewer → CloudFront authorization |
| Not an access-control mechanism | Access-control mechanism |

### Exam trigger

> **"Encrypt specific sensitive fields before sending to origin" → Field-Level Encryption.**
>
