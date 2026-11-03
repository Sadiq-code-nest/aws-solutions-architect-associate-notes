# Understanding CIDR — IPv4

CIDR looks complicated at first, but for **AWS SAA-C03**, you mainly need to understand **IP ranges, `/32`, `/24`, `/16`, `/8`, and `/0`**.

Think of CIDR as:

> **CIDR = a short way of saying "which IP addresses are included in this range?"**
> 

---

# 1. What is CIDR?

**CIDR = Classless Inter-Domain Routing.**

In AWS, CIDR is used everywhere you define an IP range, including:

- VPC CIDR
- Subnets
- Security Groups
- Network ACLs
- Route tables
- VPC networking

Example Security Group rule:

```
Type       Protocol   Port    Source
SSH        TCP        22      203.0.113.5/32
```

This means:

> Allow SSH from **one specific IP address**.
> 

---

# 2. The Two Parts of CIDR ⭐

A CIDR block has:

```
192.168.0.0/24
^^^^^^^^^^^ ^^
Base IP     Prefix
```

### Part 1 — Base IP

```
192.168.0.0
```

This represents the starting/network address.

### Part 2 — Prefix `/24`

```
/24
```

This tells you **how many bits are fixed as the network portion**.

The remaining bits can change to create the IP range.

---

# 3. The Most Important Rule 🧠

IPv4 has **32 bits**.

So:

```
Total IPs = 2^(32 - CIDR number)
```

For example:

### `/32`

```
2^(32-32)
= 2^0
= 1 IP
```

So:

```
192.168.1.10/32
```

means:

> **Exactly one IP address.**
> 

---

### `/24`

```
2^(32-24)
= 2^8
= 256 IPs
```

So:

```
192.168.0.0/24
```

means:

```
192.168.0.0
       ↓
192.168.0.255
```

**256 total IP addresses.**

---

### `/16`

```
2^(32-16)
= 2^16
= 65,536 IPs
```

```
192.168.0.0/16
```

means:

```
192.168.0.0
       ↓
192.168.255.255
```

---

### `/0`

```
2^(32-0)
= 2^32
```

This covers:

> **All IPv4 addresses**
> 

```
0.0.0.0/0
```

🔥 **Very important in AWS.**

---

# 4. The CIDR Sizes You Should Memorize ⭐⭐⭐⭐⭐

| CIDR | Total IPv4 addresses | Easy meaning |
| --- | --- | --- |
| `/32` | 1 | One IP |
| `/31` | 2 | Two IPs |
| `/30` | 4 | Four IPs |
| `/29` | 8 | Eight IPs |
| `/28` | 16 | Sixteen IPs |
| `/27` | 32 | Thirty-two IPs |
| `/26` | 64 | Sixty-four IPs |
| `/25` | 128 | 128 IPs |
| `/24` | 256 | One full last-octet range |
| `/16` | 65,536 | Large range |
| `/8` | 16,777,216 | Huge range |
| `/0` | 4,294,967,296 | All IPv4 addresses |

### 🧠 Easy pattern

Every time the prefix decreases by **1**, the number of IP addresses **doubles**.

```
/32 → 1
/31 → 2
/30 → 4
/29 → 8
/28 → 16
/27 → 32
/26 → 64
/25 → 128
/24 → 256
```

---

# 5. Understanding `/24` Visually ⭐⭐⭐⭐⭐

Take:

```
192.168.0.0/24
```

An IPv4 address has four sections:

```
192 . 168 . 0 . 0
```

With `/24`:

```
192 . 168 . 0 | 0
^^^^^^^^^^^^^   ^
  Fixed        Can change
```

The final section can range from:

```
0 → 255
```

Therefore:

```
192.168.0.0
192.168.0.1
192.168.0.2
...
192.168.0.255
```

That's:

> **256 addresses**
> 

---

# 6. Understanding `/16`

Now:

```
192.168.0.0/16
```

Visually:

```
192 . 168 | 0 . 0
^^^^^^^^^^   ^^^^^
  Fixed     Can change
```

The last two sections can change:

```
192.168.0.0
      ↓
192.168.255.255
```

So:

> **65,536 addresses**
> 

---

# 7. Understanding `/8`

```
10.0.0.0/8
```

Visually:

```
10 | 0 . 0 . 0
^^   ^^^^^^^^^
Fixed  Can change
```

Therefore:

```
10.0.0.0
   ↓
10.255.255.255
```

This gives:

> **16,777,216 addresses**
> 

This is why you commonly see:

```
10.0.0.0/8
```

for very large private networks.

---

# 8. Understanding `/0` ⭐⭐⭐⭐⭐

```
0.0.0.0/0
```

Nothing is fixed.

```
0 . 0 . 0 . 0
^   ^   ^   ^
All bits can change
```

So it means:

> **Any IPv4 address.**
> 

### AWS example

A route table:

```
Destination      Target
0.0.0.0/0        Internet Gateway
```

means:

> Any IPv4 destination that doesn't have a more specific route → send it toward the Internet Gateway.
> 

Security Group:

```
Inbound
TCP 443
Source: 0.0.0.0/0
```

means:

> Allow HTTPS traffic from **any IPv4 address**.
> 

---

# 9. `/32` vs `/0` ⭐⭐⭐⭐⭐

These are opposites and extremely important.

### `/32`

```
203.0.113.50/32
```

Means:

> **Only this one IP.**
> 

### `/0`

```
0.0.0.0/0
```

Means:

> **Every IPv4 address.**
> 

So:

```
/32 → Most restrictive range
/0  → Largest possible IPv4 range
```

---

# 10. Real Security Group Example

Imagine your company has a public Bastion Host.

Your company's public IP is:

```
203.0.113.50
```

You want SSH access only from your company's network.

You could use:

```
SSH
TCP
22
203.0.113.50/32
```

Meaning:

> Only that one IP can connect.
> 

But if you use:

```
0.0.0.0/0
```

you're saying:

> **Any IPv4 address can attempt SSH.**
> 

🔥 For SAA questions, **restricted CIDR is generally the safer choice** when the requirement says only a specific network should access something.

---

# 11. CIDR + Security Groups

Suppose:

```
Security Group

Inbound:
HTTP   TCP 80
Source: 0.0.0.0/0
```

Meaning:

> Anyone on the internet can access HTTP.
> 

Another:

```
SSH   TCP 22
Source: 203.0.113.50/32
```

Meaning:

> Only one specific IP can access SSH.
> 

So CIDR controls:

> **Which IP addresses are allowed.**
> 

---

# 12. CIDR + VPC

When creating an AWS VPC, you choose a CIDR.

Example:

```
VPC
10.0.0.0/16
```

This gives the VPC a large address range:

```
10.0.0.0
      ↓
10.0.255.255
```

You can then divide that into smaller subnets.

For example:

```
VPC
10.0.0.0/16
      │
      ├── Public Subnet
      │   10.0.1.0/24
      │
      ├── Private Subnet
      │   10.0.2.0/24
      │
      └── Private Subnet
          10.0.3.0/24
```

Think:

> **VPC = big neighborhood**
> 

> **Subnet = smaller street inside the neighborhood**
> 

---

# 13. CIDR + Subnetting ⭐⭐⭐⭐⭐

Suppose you have:

```
VPC: 10.0.0.0/16
```

You need multiple subnets.

You can create:

```
10.0.1.0/24
10.0.2.0/24
10.0.3.0/24
10.0.4.0/24
```

Each `/24` contains:

```
256 total IPv4 addresses
```

AWS reserves **5 IP addresses in every subnet**, so an AWS `/24` subnet provides:

```
256 - 5 = 251 usable IPv4 addresses
```

This is an important AWS-specific detail.

---

# 14. Public vs Private IPv4

IANA reserves these ranges for **private IPv4 networks**.

### 10.0.0.0/8

```
10.0.0.0
   ↓
10.255.255.255
```

Very large private range.

---

### 172.16.0.0/12

```
172.16.0.0
   ↓
172.31.255.255
```

Another private range.

⚠️ Be careful:

Not all `172.x.x.x` addresses are private.

Only:

```
172.16.0.0 → 172.31.255.255
```

are in this private block.

---

### 192.168.0.0/16

```
192.168.0.0
   ↓
192.168.255.255
```

Commonly seen in home networks.

For example:

```
192.168.1.1
```

is typically a home router's private IP.

---

# 15. Private IP Ranges — Memorize These ⭐⭐⭐⭐⭐

```
10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```

### 🧠 Memory:

> **10 — 172.16–31 — 192.168**
> 

Everything outside these specific private ranges is generally publicly routable IPv4 space, subject to other special-use/reserved ranges.

---

# 16. Quick Exercises

### Question 1

```
192.168.0.0/24
```

Range?

✅

```
192.168.0.0
→
192.168.0.255
```

**256 addresses**

---

### Question 2

```
192.168.0.0/16
```

Range?

✅

```
192.168.0.0
→
192.168.255.255
```

**65,536 addresses**

---

### Question 3

```
134.56.78.123/32
```

Range?

✅

```
134.56.78.123
```

**Exactly 1 IP**

---

### Question 4

```
0.0.0.0/0
```

Range?

✅

**All IPv4 addresses**

---

### Question 5

```
10.0.0.0/8
```

Range?

✅

```
10.0.0.0
→
10.255.255.255
```

**16,777,216 addresses**

---

# 🧠 SAA-C03 Cheat Sheet

```
CIDR
│
├── /32 → 1 IP
│
├── /24 → 256 IPs
│
├── /16 → 65,536 IPs
│
├── /8  → 16,777,216 IPs
│
└── /0  → ALL IPv4 addresses
```

### Private IPv4:

```
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

### Most important formula:

> **Number of IPv4 addresses = 2^(32 − prefix)**
> 

### And the simplest mental model:

> **The bigger the `/number`, the smaller the IP range.**
> 

```
/32 → █ 1 IP
/24 → █████ 256 IPs
/16 → ███████████ 65K IPs
/8  → huge range
/0  → EVERYTHING
```

For SAA-C03, if you can instantly recognize **`/32 = one IP`**, **`/24 = 256`**, **`/16 = 65,536`**, and **`/0 = all IPv4`**, you'll be able to solve a large portion of CIDR questions without doing complicated calculations.
