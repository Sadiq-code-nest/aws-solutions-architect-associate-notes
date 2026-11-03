# Full picture

Yes — let's understand the **whole packet journey**, because once you see the journey, the correct two answers become almost obvious.

## 1. First understand the architecture

```
                    INTERNET
                       │
                       │ HTTPS :443
                       ↓
                Elastic IP
                       │
                       ↓
              ┌─────────────────┐
              │  EC2 Instance   │
              │  Public Subnet  │
              │                 │
              │  App :443       │
              └─────────────────┘
                 ↑           ↑
              NACL         Security
            (stateless)      Group
```

There are **two firewalls** involved:

```
Internet
   ↓
NACL
   ↓
Security Group
   ↓
EC2
```

Both must allow the traffic.

---

# 2. What does the user want?

The requirement is:

> Anyone on the internet should be able to access the application using HTTPS.
> 

HTTPS means:

```
TCP port 443
```

And:

```
any source = 0.0.0.0/0
```

So the initial request is:

```
Internet client
      │
      │ Source: client's ephemeral port
      │ Destination: 443
      ↓
EC2
```

For example:

```
Client: 50,000
       ↓
EC2: 443
```

---

# 3. First firewall: NACL

The question says:

> NACL is configured to block **all inbound and outbound traffic**.
> 

So currently:

```
NACL

Inbound  → DENY ALL ❌
Outbound → DENY ALL ❌
```

Therefore, even if the Security Group allows HTTPS, the traffic still cannot enter.

We need to change the NACL.

---

# 4. Allow the incoming HTTPS request

We need:

```
Internet
   │
   │ TCP
   │ Destination = 443
   ↓
NACL
```

So NACL needs:

```
INBOUND:
TCP 443
Source: 0.0.0.0/0
ALLOW
```

This allows:

```
Anyone → EC2:443
```

Good.

But we're **not finished**.

---

# 5. Think about the response

The EC2 application receives:

```
Client:50000
       ↓
EC2:443
```

The application sends a response:

```
EC2:443
       ↓
Client:50000
```

Notice something very important:

### Request

```
Client → EC2
50000 → 443
```

### Response

```
EC2 → Client
443 → 50000
```

The response's destination is the client's **ephemeral port**.

That's why NACL needs an outbound ephemeral-port rule.

For this question:

```
OUTBOUND:
TCP 32768–65535
Destination: 0.0.0.0/0
ALLOW
```

So the NACL becomes:

```
NACL

INBOUND
TCP 443
0.0.0.0/0
ALLOW ✅

OUTBOUND
TCP 32768–65535
0.0.0.0/0
ALLOW ✅
```

---

# 6. Now the Security Group

The EC2 uses the **default Security Group**.

The question doesn't say that the default SG already allows inbound HTTPS.

So we need to add:

```
INBOUND
TCP 443
Source: 0.0.0.0/0
ALLOW
```

Now the complete path is:

```
                 INTERNET
                    │
                    │ Client:50000
                    │ → EC2:443
                    ↓
              ┌───────────┐
              │   NACL    │
              │ Inbound   │
              │ TCP 443 ✅│
              └─────┬─────┘
                    ↓
              ┌───────────┐
              │    SG     │
              │ TCP 443 ✅│
              └─────┬─────┘
                    ↓
              ┌───────────┐
              │    EC2    │
              │    :443   │
              └───────────┘
```

Then the response:

```
              EC2:443
                 │
                 │ → Client:50000
                 ↓
              ┌───────────┐
              │    NACL   │
              │ Ephemeral │
              │ 32768-65535
              │    ✅     │
              └─────┬─────┘
                    ↓
                 INTERNET
```

---

# 7. Therefore, the TWO correct answers

### ✅ Answer 1

> **In the Security Group, add a new rule to allow TCP connection on port 443 from source `0.0.0.0/0`**
> 

Because the application needs to accept HTTPS:

```
Internet → SG → EC2:443
```

---

### ✅ Answer 2

> **In the Network ACL, update the rule to allow inbound TCP connection on port 443 from source `0.0.0.0/0` and outbound TCP connection on port `32768 - 65535` to destination `0.0.0.0/0`**
> 

Because NACL is **stateless**, it needs rules for **both directions**.

---

# 8. Why is the first NACL option WRONG?

This option says:

> Allow inbound **and outbound TCP port 443**
> 

It sounds reasonable, but look at the response:

```
EC2:443 → Client:50000
```

The outbound destination port is:

```
50000
```

**NOT 443.**

Therefore:

```
Outbound TCP 443 ❌
```

doesn't properly allow the return traffic.

You need:

```
Outbound TCP ephemeral ports
32768–65535 ✅
```

---

# 9. Why is the SG outbound option WRONG?

This one:

> Security Group, allow TCP 443 to destination `0.0.0.0/0`
> 

It's wrong because the requirement is about **incoming traffic**.

We need:

```
SG INBOUND
TCP 443
Source: 0.0.0.0/0
```

Also, Security Groups are **stateful**, so when the inbound connection is allowed, the response traffic is automatically allowed.

You don't need to manually allow the ephemeral return port in the SG.

---

# 🧠 The BIG exam concept

Memorize this flow:

```
             REQUEST
Client:EPHEMERAL ──────→ EC2:443
                         ↑
                    NACL INBOUND
                    TCP 443
                         +
                    SG INBOUND
                    TCP 443

             RESPONSE
Client:EPHEMERAL ←────── EC2:443
        ↑
   NACL OUTBOUND
   TCP EPHEMERAL
```

### Security Group

```
STATEFUL
    ↓
Allow 443 inbound
    ↓
Response automatically allowed ✅
```

### NACL

```
STATELESS
    ↓
Must explicitly allow BOTH directions
    ↓
Inbound 443
+
Outbound ephemeral ports
```

## 🔥 SAA-C03 shortcut

When you see:

> **NACL + HTTPS + return traffic**
> 

Think:

```
INBOUND  → 443
OUTBOUND → EPHEMERAL
```

And when you see:

> **Security Group + HTTPS**
> 

Think:

```
INBOUND → 443
Response → automatically allowed
```

That's the entire trick behind this question.
