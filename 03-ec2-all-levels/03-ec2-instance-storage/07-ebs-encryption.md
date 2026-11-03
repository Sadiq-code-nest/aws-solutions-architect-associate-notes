# EBS Encryption — SAA-C03 Exam-Centric Notes

The core idea is very simple:

> **EBS encryption protects your EBS data automatically using AWS KMS keys.**
> 

You don't manually encrypt/decrypt the data when using an encrypted EBS volume.

---

## 1. What happens when EBS is encrypted?

An encrypted EBS volume provides:

| Protection | What it means |
| --- | --- |
| **Data at rest** | Data stored on the EBS volume is encrypted |
| **Data in transit** | Data moving between EC2 and the EBS volume is encrypted |
| **Snapshots** | Snapshots of encrypted volumes are encrypted |
| **New volumes** | Volumes created from encrypted snapshots are encrypted |
| **Application experience** | Encryption/decryption is transparent |

Think:

```
EC2
 │
 │ 🔐 encrypted data in transit
 ▼
EBS Volume
 │
 │ 🔐 encrypted at rest
 ▼
Encrypted Snapshot
 │
 ▼
Encrypted EBS Volume
```

---

# 2. EBS Encryption Uses AWS KMS

EBS encryption leverages:

> **AWS Key Management Service (KMS)**
> 

with **AES-256** encryption.

You don't normally need to write encryption/decryption code.

```
EC2
 │
 ▼
EBS
 │
 ▼
AWS KMS key
 │
 ▼
Encryption handled automatically
```

### Exam clue

If you see:

> **"Encrypt EBS volumes using AWS-managed/customer-managed encryption keys"**
> 

→ Think **EBS + KMS**

---

# 3. Data at Rest

This means the data **stored inside the EBS volume** is encrypted.

Example:

```
EC2
  │
  ▼
Encrypted EBS
  │
  └── customer-data.db 🔐
```

Even though your application sees normal data, the underlying EBS storage is encrypted.

---

# 4. Data in Transit

The data moving between:

```
EC2 instance
      ↕
EBS volume
```

is also encrypted for encrypted EBS volumes.

This is important because EBS is **network-attached storage**.

### Memory

> **Encrypted EBS = at rest + in transit**
> 

---

# 5. Snapshots of Encrypted EBS Volumes

If the original EBS volume is encrypted:

```
Encrypted EBS
      ↓
Encrypted Snapshot
```

The snapshot is automatically encrypted.

You don't create an unencrypted snapshot from an encrypted volume.

### Exam trigger

> **"Take a snapshot of an encrypted EBS volume"**
> 

→ **The snapshot is encrypted.**

---

# 6. Volumes Created From Encrypted Snapshots

Suppose:

```
Encrypted EBS
      ↓
Encrypted Snapshot
      ↓
New EBS Volume
```

The newly created EBS volume is also encrypted.

So:

> **Encrypted snapshot → encrypted EBS volume**
> 

---

# 7. Can You Encrypt an Existing Unencrypted EBS Volume?

Yes.

But you don't simply switch the existing volume from unencrypted to encrypted directly in the traditional workflow.

The standard process is:

```
Existing Unencrypted EBS
          ↓
       Snapshot
          ↓
   Copy Snapshot
   + Encryption
          ↓
 Encrypted Snapshot
          ↓
Create EBS Volume
          ↓
Encrypted EBS
          ↓
Attach to EC2
```

### Example

You currently have:

```
EC2
 │
 └── 100 GB unencrypted EBS
```

You need encryption.

Do:

**1. Create snapshot**

```
EBS → Snapshot
```

**2. Copy the snapshot and enable encryption**

```
Unencrypted Snapshot
        ↓
Copy + Encrypt
        ↓
Encrypted Snapshot
```

**3. Create a new EBS volume**

```
Encrypted Snapshot
        ↓
Encrypted EBS
```

**4. Attach the new encrypted volume**

```
EC2
 │
 └── Encrypted EBS
```

---

# 8. Why "Copy Snapshot" Is Important ⭐⭐⭐⭐⭐

This is a classic exam concept.

AWS allows you to:

> **Copy an unencrypted snapshot and encrypt the copy.**
> 

So if you see:

> **"An existing EBS volume is unencrypted. How can the architect encrypt it?"**
> 

Think:

> **Snapshot → Copy snapshot with encryption → New encrypted volume**
> 

---

# 9. Encryption Has Minimal Performance Impact

EBS encryption is designed to have:

> **Minimal impact on latency**
> 

And encryption/decryption is handled transparently.

Therefore, you generally don't need to modify your application to encrypt/decrypt the data.

---

# 🔥 EBS Encryption — Master Table

| Requirement / Question wording | Think |
| --- | --- |
| Encrypt EBS data at rest | **EBS encryption** |
| Encrypt data moving EC2 ↔ EBS | **Encrypted EBS** |
| Encrypt EBS snapshots | **Encrypted EBS snapshot** |
| Create encrypted volume from encrypted snapshot | **Automatically encrypted** |
| Encryption key management | **AWS KMS** |
| Encryption algorithm mentioned | **AES-256** |
| Application needs no encryption/decryption code | **Transparent EBS encryption** |
| Existing EBS is unencrypted | **Snapshot → Copy + Encrypt → New EBS** |
| Snapshot of encrypted EBS | **Encrypted snapshot** |
| Volume created from encrypted snapshot | **Encrypted volume** |
| Encrypt an unencrypted snapshot | **Copy snapshot and enable encryption** |
| Performance concern | **Minimal latency impact** |

---

# 🚨 Common SAA-C03 Traps

### Trap 1: "Modify the existing unencrypted volume's data directly"

Don't choose an answer suggesting you manually encrypt the data inside the volume.

Use the snapshot/copy workflow when the question asks how to convert an existing unencrypted EBS volume.

---

### Trap 2: "Encrypted EBS snapshot becomes unencrypted"

❌ No.

```
Encrypted EBS
      ↓
Encrypted Snapshot
```

---

### Trap 3: "Only data at rest is encrypted"

❌ Incomplete.

Encrypted EBS also provides encryption for **data in transit between the EC2 instance and the EBS volume**.

---

### Trap 4: "Application must decrypt the EBS data"

❌ No.

EBS encryption/decryption is **transparent** to the application.

---

### Trap 5: "Use S3 encryption"

If the question specifically concerns:

> **EC2 + EBS volume encryption**
> 

→ Think **EBS encryption + KMS**, not S3 encryption.

---

# 🧠 Final 20-Second Memory

```
              EBS ENCRYPTION
                    │
          ┌─────────┼─────────┐
          ↓         ↓         ↓
       At Rest   In Transit  Snapshot
          │         │         │
          └─────────┼─────────┘
                    ↓
                   KMS
                AES-256
```

And memorize this conversion:

```
UNENCRYPTED EBS
      ↓
   SNAPSHOT
      ↓
COPY + ENCRYPT
      ↓
ENCRYPTED SNAPSHOT
      ↓
NEW EBS VOLUME
      ↓
ATTACH TO EC2
```

### ⭐ Exam brain shortcut

> **"EBS + encryption" → KMS**
> 

> **"Encrypted EBS" → at rest + EC2↔EBS in transit**
> 

> **"Encrypted EBS → snapshot" → encrypted snapshot**
> 

> **"Unencrypted EBS → encrypted" → Snapshot → Copy + Encrypt → New EBS**
> 

> **"No application changes required" → transparent encryption**
>
