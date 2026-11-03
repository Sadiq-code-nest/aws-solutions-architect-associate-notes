# Why Encryption? (AWS SAA-C03 Complete Understanding)

This is one of the **most important security concepts** in AWS. Many AWS services (S3, EBS, RDS, EFS, DynamoDB, Secrets Manager, etc.) use one or more of these encryption methods.

The easiest way to remember is:

> **Data can be protected while it is travelling, while it is stored, or before it even leaves your computer.**
> 

There are **3 types**:

1. **Encryption in Transit (In Flight)** → Protect data while moving.
2. **Server-Side Encryption (At Rest)** → Protect data while stored.
3. **Client-Side Encryption** → Only the client can read the data.

---

# Imagine Sending a Secret Letter

Suppose you want to send your friend a confidential letter.

There are three ways to protect it.

### Option 1

Put the letter inside a locked delivery box.

People carrying it cannot read it.

➡️ **Encryption in Transit**

---

### Option 2

Your friend receives it and immediately locks it inside a safe.

➡️ **Server-Side Encryption**

---

### Option 3

Before sending, you encrypt the letter yourself with your own secret code.

Even your friend stores only encrypted text until someone with the correct key decrypts it.

➡️ **Client-Side Encryption**

---

# Overview Diagram

```
                 Your Data

                    │

     ┌──────────────┼───────────────┐

     ▼              ▼               ▼

 In Transit      At Rest      Before Sending

(TLS/HTTPS)   (Server-side)   (Client-side)

     │              │               │

Protect while   Protect while   Server never
moving          stored          sees plaintext
```

---

# 1. Encryption in Transit (In Flight)

## What is it?

Data is encrypted **while travelling over the network**.

Think:

```
Computer  ─────────► Internet ─────────► AWS
```

Without encryption, anyone intercepting the traffic can read it.

With encryption, intercepted data looks like random characters.

---

# Example Without Encryption

Suppose you log into a website.

```
Username: john

Password: MyPassword123
```

Someone on public Wi-Fi captures the network traffic.

They immediately see:

```
Username: john

Password: MyPassword123
```

Your password is stolen.

---

# With TLS Encryption

Now the browser encrypts everything before sending.

Instead of this:

```
Username: john

Password: MyPassword123
```

The hacker sees:

```
F82K#@91AJD8K2H9S8A...
```

Completely unreadable.

---

# Diagram

```
                Internet

Client ------------------------ AWS

Username: john
Password: 123456

        │
        ▼

TLS Encryption

        │

XXXXXXXXXXXXXXXXXXXXXXXX

        │

TLS Decryption

        ▼

AWS receives

Username: john
Password: 123456
```

Notice:

Only the client and AWS can read the data.

---

# What is TLS?

TLS stands for:

> **Transport Layer Security**
> 

Older name:

SSL (Secure Sockets Layer)

Today, AWS uses **TLS**.

---

# HTTPS

Whenever you see:

```
https://
```

TLS is being used.

Example:

```
https://amazon.com
```

instead of

```
http://amazon.com
```

---

# What is a TLS Certificate?

Think of it as a digital ID card proving the server is genuine.

Example:

Your browser checks:

```
Amazon Certificate

↓

Issued by trusted authority

↓

Valid

↓

Secure Connection
```

Now your browser trusts Amazon.

---

# What Attack Does TLS Prevent?

A favourite exam topic.

TLS prevents:

> **Man-In-The-Middle (MITM) Attack**
> 

---

## MITM Without TLS

```
Client

↓

Hacker

↓

AWS
```

Hacker reads everything.

---

## MITM With TLS

```
Client

↓

Encrypted Data

↓

Hacker

↓

??????????

↓

AWS
```

Hacker cannot understand the data.

---

# Real-Life Example ⭐⭐⭐⭐⭐

You're using airport Wi-Fi.

You log into your online bank.

Because the website uses HTTPS,

even if someone captures the network packets,

they cannot read:

- Password
- Account number
- Transactions

---

# AWS Examples

Uses TLS:

- Amazon S3
- RDS
- DynamoDB
- API Gateway
- CloudFront
- Load Balancers

Basically almost every AWS service.

---

# 2. Server-Side Encryption (Encryption at Rest)

Now the data has already reached AWS.

Question:

How should AWS store it?

Without encryption:

```
Hard Disk

↓

Photo.jpg
```

Anyone accessing the storage could read it.

---

# With Server-Side Encryption

AWS encrypts it before writing to disk.

```
Upload File

↓

AWS Receives File

↓

Encrypt

↓

Store Encrypted
```

---

# Diagram

```
Client

↓

Upload

↓

AWS

↓

Encrypt

↓

Encrypted Storage

↓

Later

↓

Decrypt

↓

Download
```

---

# What Does AWS Do?

When you upload a file to S3:

AWS automatically:

```
Receive

↓

Encrypt

↓

Store

↓

Decrypt when authorised user downloads
```

You don't normally see this happening.

---

# Real-Life Example ⭐⭐⭐⭐⭐

Imagine a bank vault.

Customers give jewellery.

Bank immediately locks it inside the vault.

When the customer returns,

the bank unlocks it.

Exactly how Server-Side Encryption works.

---

# Example with Amazon S3

You upload:

```
Salary.xlsx
```

AWS stores:

```
ASD8123JSH823JKD...
```

Not the original file.

---

# Who Owns the Encryption Key?

Depends on the encryption type.

Examples:

- SSE-S3 → AWS manages keys.
- SSE-KMS → AWS KMS manages keys.
- SSE-C → Customer provides keys.

---

# AWS Services Supporting Encryption at Rest

Examples:

- S3
- EBS
- EFS
- RDS
- Aurora
- DynamoDB
- FSx

---

# Real Example ⭐⭐⭐⭐⭐

Hospital stores patient records.

If someone steals the physical hard disk,

they cannot read patient data because it is encrypted.

---

# 3. Client-Side Encryption

This is the strongest form.

Here,

AWS never sees your original data.

---

# How It Works

Instead of AWS encrypting it...

Your computer encrypts it first.

```
Laptop

↓

Encrypt

↓

Upload

↓

AWS stores encrypted data only
```

AWS never knows the original content.

---

# Diagram

```
Client

↓

Encrypt

↓

Encrypted File

↓

AWS Storage

↓

Download

↓

Client Decrypt

↓

Original File
```

Notice:

AWS never decrypts it.

---

# Real-Life Example ⭐⭐⭐⭐⭐

Suppose you're sending your diary to cloud storage.

You first lock it with your own secret padlock.

AWS stores only the locked version.

Even AWS employees cannot read it.

Only you have the key.

---

# Example

Original:

```
My Secret Password
```

Client encrypts.

Uploads:

```
JKS98HSDKJ892KDJ...
```

AWS stores only:

```
JKS98HSDKJ892KDJ...
```

AWS never sees:

```
My Secret Password
```

---

# What is Envelope Encryption?

Sometimes you'll hear this term.

Instead of encrypting a huge file directly with a master key:

AWS:

1. Creates a **Data Key**
2. Encrypts the file using the Data Key
3. Encrypts the Data Key using a **Master Key (KMS Key)**

---

# Diagram

```
Master Key (KMS)

        │

Encrypts

        │

Data Key

        │

Encrypts

        │

Large File
```

Think of it like:

A big safe (master key) protecting a smaller key, and that smaller key locks the actual file.

This is faster and more secure than using the master key directly for every byte of data.

---

# Complete Comparison

| Feature | In Transit | Server-Side | Client-Side |
| --- | --- | --- | --- |
| Data moving over network | ✅ | ❌ | ❌ |
| Data stored on server | ❌ | ✅ | ✅ (already encrypted) |
| Server can read data | ✅ | ✅ | ❌ |
| Uses TLS | ✅ | ❌ | ❌ |
| Uses encryption keys | TLS certificates | AWS/customer-managed keys | Client-managed keys |
| Strongest privacy | Medium | High | Very High |

---

# Real-Life Comparison

Imagine sending a gold necklace.

### Encryption in Transit

You put it inside a locked delivery truck.

People on the road cannot steal it.

---

### Server-Side Encryption

The jewellery shop receives it and locks it in a safe.

---

### Client-Side Encryption

You lock the jewellery inside your own safe before shipping it.

The jewellery shop stores your locked safe.

Only you know the combination.

---

# Which One Can AWS Read?

| Type | AWS Can Read? |
| --- | --- |
| In Transit | ✅ Yes (after TLS is terminated) |
| Server-Side | ✅ Yes (it decrypts the data when needed) |
| Client-Side | ❌ No (AWS never has your decryption key) |

---

# AWS SAA-C03 Exam Tips

### If the question says:

> **Protect data while travelling over the Internet**
> 

✅ **TLS / HTTPS (Encryption in Transit)**

---

> **Protect stored data on S3, EBS, or RDS**
> 

✅ **Server-Side Encryption (Encryption at Rest)**

---

> **AWS must never be able to read the data**
> 

✅ **Client-Side Encryption**

---

> **Prevent Man-In-The-Middle (MITM) attacks**
> 

✅ **TLS / HTTPS**

---

> **Encrypt data before uploading to AWS**
> 

✅ **Client-Side Encryption**

---

# Easy Memory Tricks ⭐⭐⭐⭐⭐

### Encryption in Transit

> **Protects data while moving.**
> 
> 
> **Keyword:** HTTPS / TLS
> 

---

### Server-Side Encryption

> **AWS encrypts after receiving the data and decrypts it before returning it.**
> 
> 
> **Keyword:** Data at Rest
> 

---

### Client-Side Encryption

> **You encrypt before uploading. AWS only stores ciphertext and cannot decrypt it.**
> 
> 
> **Keyword:** AWS cannot read your plaintext
> 

---

# Final Memory Diagram

```
                    YOUR DATA

                       │

      ┌────────────────┼─────────────────┐

      ▼                ▼                 ▼

  In Transit      Server-Side      Client-Side

 (TLS/HTTPS)     (AWS Encrypts)   (You Encrypt)

      │                │                 │

 Protect while     Protect while    AWS never sees

 moving            stored           plaintext

      │                │                 │

      ▼                ▼                 ▼

 Internet        AWS Storage       AWS Stores Only
                                     Ciphertext
```

## One-line Summary

- **Encryption in Transit** protects data **while it is travelling** (TLS/HTTPS).
- **Server-Side Encryption** protects data **while it is stored** in AWS.
- **Client-Side Encryption** protects data **before it leaves your device**, so **only the client can decrypt it**.
