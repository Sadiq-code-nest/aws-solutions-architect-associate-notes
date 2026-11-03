# Transferring Large Amounts of Data into AWS — SAA-C03

This is a **very common AWS architecture decision**:

**How should you transfer a huge amount of data from on-premises to AWS?**

The main choices are:

1. **Internet / Site-to-Site VPN**
2. **AWS Direct Connect**
3. **AWS Snowball**
4. **For ongoing transfers → VPN/DX + DMS/DataSync**

---

# 1. Example: 200 TB of Data

Suppose:

- Data = **200 TB**
- Internet connection = **100 Mbps**

We need to move **200 TB into AWS**.

---

## Option 1 — Internet / Site-to-Site VPN

### Advantage

✅ **Immediate to set up**

You don't need to wait for dedicated networking infrastructure.

### Disadvantage

❌ Very slow for huge amounts of data.

Given:

```
200 TB × 1000 GB/TB × 1000 MB/GB × 8 Mb/MB
------------------------------------------------
                  100 Mbps
```

≈ **16,000,000 seconds**

≈ **185 days**

So transferring 200 TB over a 100 Mbps connection could take roughly:

> **185 days**
> 

### 🧠 Exam clue

> **Small/moderate amount of data + Internet available + need to start immediately**
> 

➡️ Internet / VPN can make sense.

> **Huge amount of data + slow Internet**
> 

➡️ Consider **Snowball**.

---

# 2. Option 2 — AWS Direct Connect

Suppose we have:

**1 Gbps Direct Connect**

The theoretical transfer time:

```
200 TB × 1000 GB/TB × 8 Gb/GB
--------------------------------
            1 Gbps
```

≈ **1,600,000 seconds**

≈ **18.5 days**

So:

> **200 TB over 1 Gbps ≈ 18.5 days**
> 

### Advantage

✅ Dedicated private network connection

✅ Much higher and more consistent bandwidth

### Disadvantage

❌ **Long initial setup**

The one-time setup can take **over a month**, depending on the connection/provider.

So if you need to transfer the data **right now**, DX may not be the best solution for the initial transfer.

---

# 3. Option 3 — AWS Snowball

For a huge one-time transfer, **AWS Snowball** can be much faster.

For 200 TB:

> You may use **2–3 Snowballs in parallel**.
> 

The end-to-end transfer can take around:

> **~1 week**
> 

The basic workflow:

```
On-Premises
    │
    │ Copy data
    ↓
Snowball devices
    │
    │ Ship to AWS
    ↓
AWS
    │
    ↓
Data imported
```

### Why Snowball?

Because you aren't limited by your relatively slow Internet connection.

Instead:

```
Network transfer
      ❌

Physical data transfer
      ✅
```

### 🧠 Exam clue

> **Very large amount of data + limited network bandwidth + one-time migration**
> 

➡️ **AWS Snowball**

---

# 4. Snowball + DMS

Snowball doesn't necessarily mean you have to stop using online replication.

You can combine **Snowball + AWS DMS**.

Example:

```
              Initial bulk data
On-Premises ────────────────→ Snowball
                                  │
                                  ↓
                                 AWS

After initial transfer:

On-Premises ────────────────→ AWS DMS
                               (CDC)
                                  │
                                  ↓
                           Ongoing changes
```

### Why?

Snowball handles the **large initial dataset**.

DMS can then handle **ongoing database changes**.

This helps reduce migration downtime.

---

# 5. Ongoing Data Transfer

If you're not doing a one-time migration and instead need **continuous/ongoing data transfer**, consider:

### Site-to-Site VPN + DMS/DataSync

or

### Direct Connect + DMS/DataSync

```
On-Premises
     │
     ├── Site-to-Site VPN ──┐
     │                      │
     └── Direct Connect ────┤
                            ↓
                     DMS / DataSync
                            ↓
                           AWS
```

---

# 🔥 DMS vs DataSync

This distinction is important.

### AWS DMS

Think:

> **Database → Database**
> 

Used for:

- Database migration
- Database replication
- Continuous replication / CDC

Example:

```
On-Premises MySQL
       ↓
      DMS
       ↓
Amazon RDS
```

---

### AWS DataSync

Think:

> **Files → Files / Storage**
> 

Used for transferring/synchronizing large amounts of file data between on-premises storage and AWS storage services.

Example:

```
On-Premises File System
        ↓
     DataSync
        ↓
Amazon S3 / EFS / FSx
```

---

# 🎯 SAA-C03 Decision Table

| Situation | Best choice |
| --- | --- |
| Small amount of data | Internet |
| Need immediate setup | Internet / VPN |
| Huge data + slow Internet | **Snowball** |
| 200 TB one-time transfer | **Snowball** |
| Dedicated high-bandwidth private connection | **Direct Connect** |
| Ongoing database replication | **DMS** |
| Ongoing file/storage transfer | **DataSync** |
| Initial huge DB migration + ongoing changes | **Snowball + DMS** |
| Ongoing transfer over private connection | **DX + DMS/DataSync** |
| Ongoing transfer over VPN | **VPN + DMS/DataSync** |

---

# 🧠 Exam Memory Trick

### **Internet**

> Easy + immediate, but potentially **very slow**
> 

### **Direct Connect**

> **Fast + private + consistent**, but takes time to establish
> 

### **Snowball**

> **Huge one-time data transfer**
> 

### **DMS**

> **Database migration/replication**
> 

### **DataSync**

> **File/storage transfer**
> 

### ⭐ The easiest way to remember:

**Huge one-time data → Snowball**

**Database → DMS**

**Files → DataSync**

**Long-term private network → Direct Connect**

**Quick/simple transfer → Internet/VPN**
