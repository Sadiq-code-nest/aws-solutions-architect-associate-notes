# Homogeneous Migration

### Homogeneous

Same database engine:

```
Oracle → Oracle
MySQL → MySQL
PostgreSQL → PostgreSQL
```

Usually requires fewer schema/application changes.

### Heterogeneous

Different database engines:

```
Oracle → Aurora PostgreSQL
Oracle → PostgreSQL
SQL Server → MySQL
```

Often requires:

- AWS Schema Conversion Tool
- Application modifications
- Schema conversion

### Exam shortcut:

> **"Minimize changes" + same database engine → Homogeneous migration**
>
