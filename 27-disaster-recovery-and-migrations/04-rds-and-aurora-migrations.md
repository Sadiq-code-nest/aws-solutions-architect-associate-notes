# RDS & Aurora MySQL / PostgreSQL Migrations — SAA-C03 Notes

This topic is mainly about **how to migrate an existing MySQL/PostgreSQL database to Amazon Aurora**.

---

## 1. RDS MySQL → Aurora MySQL

There are **2 main options**.

### Option 1 — DB Snapshot

```
RDS MySQL
    │
    │ Create DB Snapshot
    ▼
DB Snapshot
    │
    │ Restore
    ▼
Aurora MySQL
```

You can take a **DB snapshot from RDS MySQL** and restore it as an **Aurora MySQL DB cluster**.

### 🧠 Exam clue

> **RDS MySQL → Aurora MySQL using snapshot**
> 

✅ **Create DB Snapshot → Restore as Aurora MySQL**

---

## 2. RDS MySQL → Aurora MySQL — Read Replica

Another option is to create an **Aurora Read Replica** from your existing RDS MySQL database.

```
RDS MySQL
     │
     │ Replication
     ▼
Aurora MySQL Read Replica
     │
     │ Replication Lag = 0
     ▼
Promote
     │
     ▼
Aurora MySQL DB Cluster
```

### Process

1. Create Aurora Read Replica from RDS MySQL.
2. Let replication happen.
3. Wait until **replication lag = 0**.
4. Promote the Aurora Read Replica.
5. It becomes its own Aurora DB cluster.

### Important

This approach can:

- Take time
- Cost money

---

# 3. External MySQL → Aurora MySQL

Here, the source MySQL database isn't necessarily running on Amazon RDS.

For example:

```
On-Premises MySQL
        ↓
     Aurora MySQL
```

There are several approaches.

---

## Option 1 — Percona XtraBackup + S3

```
External MySQL
      │
      │ Percona XtraBackup
      ▼
Backup File
      │
      ▼
Amazon S3
      │
      │ Import
      ▼
Aurora MySQL
```

### Process

1. Use **Percona XtraBackup** to create a backup.
2. Store the backup in **Amazon S3**.
3. Create an Aurora MySQL database from the S3 backup.

### 🧠 Exam clue

> **External MySQL → Aurora + backup files + S3**
> 

✅ **Percona XtraBackup → S3 → Aurora MySQL**

---

# 4. External MySQL → Aurora MySQL — mysqldump

You can also use the **mysqldump** utility.

```
External MySQL
      │
      │ mysqldump
      ▼
SQL Dump
      │
      ▼
Aurora MySQL
```

### Important

The **mysqldump method is slower than the S3 method**.

So remember:

**S3 backup method → faster**

**mysqldump → slower**

---

# 5. External MySQL → Aurora — DMS

If both databases are **up and running**, you can use **AWS DMS**.

```
External MySQL
      │
      │ AWS DMS
      ▼
Aurora MySQL
```

This is particularly useful when you want:

- Continuous replication
- Minimal downtime
- Source database to remain available

### 🧠 Exam clue

> Both source and target databases are running + continuous migration
> 

✅ **AWS DMS**

---

# 6. RDS PostgreSQL → Aurora PostgreSQL

There are also **2 main options**.

### Option 1 — DB Snapshot

```
RDS PostgreSQL
      │
      │ DB Snapshot
      ▼
Snapshot
      │
      │ Restore
      ▼
Aurora PostgreSQL
```

Create a DB snapshot from RDS PostgreSQL and restore it as an **Aurora PostgreSQL DB cluster**.

---

## 7. RDS PostgreSQL → Aurora PostgreSQL — Read Replica

Another option:

```
RDS PostgreSQL
      │
      │ Replication
      ▼
Aurora PostgreSQL Read Replica
      │
      │ Replication Lag = 0
      ▼
Promote
      │
      ▼
Aurora PostgreSQL DB Cluster
```

### Process

1. Create Aurora Read Replica.
2. Wait for replication.
3. Wait until **replication lag = 0**.
4. Promote it.
5. It becomes its own Aurora DB cluster.

Again, this can take time and cost money.

---

# 8. External PostgreSQL → Aurora PostgreSQL

For an external PostgreSQL database:

```
External PostgreSQL
        │
        │ Backup
        ▼
       S3
        │
        │ Import
        ▼
Aurora PostgreSQL
```

### Process

1. Create a backup of the PostgreSQL database.
2. Put the backup in **Amazon S3**.
3. Import it using the **`aws_s3` Aurora extension**.

---

# 9. External PostgreSQL → Aurora — DMS

If both databases are already running:

```
External PostgreSQL
        │
        │ AWS DMS
        ▼
Aurora PostgreSQL
```

Use **AWS DMS** when you need ongoing replication / migration with minimal downtime.

---

# 🔥 MySQL vs PostgreSQL — Easy Comparison

| Source | Target | Main Options |
| --- | --- | --- |
| RDS MySQL | Aurora MySQL | Snapshot OR Aurora Read Replica |
| External MySQL | Aurora MySQL | Percona XtraBackup + S3 OR `mysqldump` OR DMS |
| RDS PostgreSQL | Aurora PostgreSQL | Snapshot OR Aurora Read Replica |
| External PostgreSQL | Aurora PostgreSQL | Backup + S3 + `aws_s3` extension OR DMS |

---

# 🎯 SAA-C03 Exam Decision Tree

### RDS MySQL → Aurora MySQL

**Snapshot available?**

➡️ **Snapshot → Aurora**

**Want replication and then promotion?**

➡️ **Aurora Read Replica → wait for lag = 0 → Promote**

---

### External MySQL → Aurora MySQL

**Have a backup file?**

➡️ **Percona XtraBackup → S3 → Aurora**

**Want a simpler dump-based migration?**

➡️ **mysqldump → Aurora**

**Both databases are running and need continuous migration?**

➡️ **DMS**

---

### RDS PostgreSQL → Aurora PostgreSQL

**Snapshot approach?**

➡️ **Snapshot → Aurora**

**Replication approach?**

➡️ **Aurora Read Replica → lag = 0 → Promote**

---

### External PostgreSQL → Aurora PostgreSQL

**Backup available?**

➡️ **S3 → `aws_s3` extension → Aurora**

**Both DBs running?**

➡️ **DMS**

---

## 🧠 Final Memory Trick

```
RDS → Aurora
│
├── Snapshot
└── Aurora Read Replica
       ↓
   Lag = 0
       ↓
    Promote

External MySQL → Aurora
│
├── Percona XtraBackup → S3
├── mysqldump
└── DMS

External PostgreSQL → Aurora
│
├── Backup → S3 → aws_s3 extension
└── DMS
```

### ⭐ Most important exam association

**MySQL + backup → Percona XtraBackup + S3**

**PostgreSQL + backup → S3 + `aws_s3` extension**

**Both DBs running + continuous migration → AWS DMS**

**RDS → Aurora + snapshot → Restore snapshot**

**Read Replica → wait for replication lag = 0 → Promote**
