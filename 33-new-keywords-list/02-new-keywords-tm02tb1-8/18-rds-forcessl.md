# rds.forcessl

Force all connections to your DB instance to use SSL by setting `rds.force_ssl` to true. Once done, reboot your DB instance.

### Definition

`rds.force_ssl` is an RDS for SQL Server configuration parameter that can require client connections to use SSL/TLS.

```
Client
  │
  ├── SSL/TLS ───────► RDS ✅
  │
  └── Plaintext ─────► RDS ❌
```

### Worked example

You have:

```
EC2
 ↓
RDS SQL Server
```

Set:

```
rds.force_ssl = 1
```

Then RDS requires SSL connections.

### `rds.force_ssl` vs application SSL configuration

| `rds.force_ssl` | Application SSL configuration |
| --- | --- |
| Server-side enforcement | Client-side configuration |
| Forces encrypted connection | Tells client to use TLS |
| Prevents accidental plaintext connections | Establishes trusted/encrypted connection |
| RDS configuration | Application/driver configuration |

### Exam trigger

> **"Force all RDS connections to use SSL" → `rds.force_ssl`.**
>
