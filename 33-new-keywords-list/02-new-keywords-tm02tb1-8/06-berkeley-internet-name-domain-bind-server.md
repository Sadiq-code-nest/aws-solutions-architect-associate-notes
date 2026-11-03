# Berkeley Internet Name Domain (BIND) Server

## Berkeley Internet Name Domain (BIND) Server

**BIND** is a popular **DNS (Domain Name System) server software** used to translate domain names into IP addresses.

Think:

> **BIND = Software that runs a DNS server.**
> 

### Simple example

A user enters:

```
www.example.com
```

BIND DNS server finds:

```
www.example.com
      ↓
192.0.2.10
```

Then the user's browser connects to that IP.

### AWS SAA-C03 context

You might have an **on-premises BIND DNS server** and want AWS resources to resolve names between your on-premises network and AWS.

AWS services commonly involved include:

- **Route 53 Resolver**
- **Route 53 Private Hosted Zones**
- **Resolver inbound/outbound endpoints**

### 🧠 Exam shortcut

> **BIND = DNS server software, commonly used on Linux/Unix systems.**
>
