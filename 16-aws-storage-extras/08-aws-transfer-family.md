# AWS Transfer Family — Complete SAA-C03 Notes (Crystal Clear)

These two services look similar because **both move data**.

The easiest way to remember them is:

> **AWS Transfer Family = People upload/download files using FTP/SFTP.**
> 

---

# PART 1 — AWS Transfer Family

## What is AWS Transfer Family?

Suppose your company has customers who upload files using

- FTP
- FTPS
- SFTP

AWS S3 **doesn't support FTP directly.**

AWS Transfer Family acts like a **FTP server**.

It accepts FTP connections and stores files in S3 or EFS.

---

## Simple Architecture

```
           Customer

         FileZilla

              │

         FTP / SFTP

              │

              ▼

     AWS Transfer Family

              │

      Authentication

              │

              ▼

     Amazon S3 / Amazon EFS
```

The customer thinks

"I uploaded to an FTP server."

Actually

AWS stores it inside S3.

---

# Real Life Example

Imagine

A supplier uploads invoices every day.

They already use

```
FileZilla
```

You don't want them to change software.

Instead,

give them an

SFTP endpoint.

They continue using FileZilla.

Files automatically go into S3.

---

# Supported Protocols

AWS supports three protocols.

| Protocol | Full Name | Secure? | Port |
| --- | --- | --- | --- |
| FTP | File Transfer Protocol | ❌ No | 21 |
| FTPS | FTP over SSL/TLS | ✅ Yes | 21 |
| SFTP | SSH File Transfer Protocol | ✅ Yes | 22 |

---

## FTP

Old protocol.

No encryption.

Data travels in plain text.

Rarely used today.

---

## FTPS

FTP

- SSL

Encrypts traffic.

---

## SFTP

Uses SSH.

Most common.

Very secure.

AWS exam usually prefers

**SFTP**.

---

# Storage Options

Transfer Family can store files into

```
Amazon S3

or

Amazon EFS
```

---

## Example 1

Customer uploads

```
invoice.pdf
```

↓

Transfer Family

↓

Amazon S3

---

## Example 2

Developer uploads

```
project.zip
```

↓

Transfer Family

↓

Amazon EFS

---

# Authentication

Supports many authentication systems.

```
Microsoft AD

LDAP

Okta

Amazon Cognito

Custom Identity Provider
```

Meaning

users can login using existing company accounts.

---

# Availability

AWS manages

- Multi-AZ
- Scaling
- Security
- Failover

You don't manage servers.

---

# Pricing

You pay for

- Endpoint
- Data transferred

No EC2 needed.

---

# Use Cases

### Example 1

Partner uploads reports daily.

↓

Use

Transfer Family.

---

### Example 2

Customers upload documents.

↓

Transfer Family.

---

### Example 3

Bank receives payment files from another bank using SFTP.

↓

Transfer Family.

---

# Architecture

```
Partner

      │

SFTP

      │

AWS Transfer Family

      │

Authentication

      │

Amazon S3
```

---
