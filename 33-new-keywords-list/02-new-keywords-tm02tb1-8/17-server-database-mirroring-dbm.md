# SQL Server Database Mirroring (DBM)

**Database Mirroring (DBM)** is a **SQL Server technology that keeps a second copy of a database synchronized with the primary database**.

In simple words:

> **Primary database → continuously keeps a mirror/standby database updated → if primary fails, the mirror can take over.**
> 

### Simple example

```
                SQL Server
                   │
          ┌────────┴────────┐
          ▼                 ▼
     Primary DB        Mirror DB
      (AZ-1)             (AZ-2)
         │                   ▲
         └── changes ────────┘
```

If the primary database fails:

```
Primary DB ❌
     ↓
Mirror DB becomes
the new primary
     ↓
Application continues
```

## In Amazon RDS

For **RDS for SQL Server Multi-AZ**, AWS uses **SQL Server Database Mirroring (DBM)** to maintain the standby database and provide **automatic failover**.

So:

```
RDS SQL Server Multi-AZ
        ↓
Primary DB
        ↓
Database Mirroring
        ↓
Standby DB in another AZ
        ↓
Primary fails → RDS automatically fails over
```

### 🧠 SAA-C03 Exam Memory

| Database Engine | RDS Multi-AZ technology |
| --- | --- |
| MySQL | AWS failover technology |
| PostgreSQL | AWS failover technology |
| MariaDB | AWS failover technology |
| Oracle | AWS failover technology |
| **SQL Server** | **SQL Server Database Mirroring (DBM)** |

**Key phrase:**

> **RDS SQL Server + Multi-AZ → Database Mirroring (DBM) → High Availability + Automatic Failover**
>
