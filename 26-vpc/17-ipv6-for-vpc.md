# AWS IPv6 in VPC — SAA-C03 Exam Notes

The most important idea is:

> **AWS VPC is always IPv4 + optionally IPv6. IPv6 can be added, but IPv4 cannot be removed.**
> 

---

## 1. What is IPv6?

**IPv6 = Internet Protocol version 6**, the successor to IPv4.

IPv4 has a limited address space:

> **~4.3 billion addresses**
> 

IPv6 has an enormously larger address space:

> **2¹²⁸ ≈ 3.4 × 10³⁸ addresses**
> 

So IPv6 solves the problem of **IPv4 address exhaustion**.

### Simple analogy

Think of IPv4 as a parking lot with a limited number of spaces.

IPv6 is like creating a parking system so enormous that running out of spaces is practically not a concern.

---

# 2. IPv4 vs IPv6

| Feature | IPv4 | IPv6 |
| --- | --- | --- |
| Address size | 32-bit | 128-bit |
| Address count | ~4.3 billion | ~3.4 × 10³⁸ |
| Example | `10.0.0.5` | `2001:db8::1234` |
| Format | Decimal | Hexadecimal |
| Private address ranges | ✅ Yes | ❌ No traditional private range |
| AWS VPC support | Required | Optional |
| Can disable in VPC? | ❌ No | N/A |
| AWS IPv6 addresses | Can be private/public | Public & Internet-routable |
| Typical AWS use | Primary addressing | Additional addressing / IPv6 connectivity |

---

# 3. What does an IPv6 address look like?

IPv4:

```
10.0.0.5
```

IPv6:

```
2001:db8:3333:4444:5555:6666:7777:8888
```

IPv6 contains **8 groups**, separated by `:`.

Each group contains hexadecimal characters:

```
0 1 2 3 4 5 6 7 8 9 a b c d e f
```

Each group can contain up to **4 hexadecimal digits**.

Example:

```
2001:db8:3333:4444:5555:6666:7777:8888
```

---

# 4. IPv6 `::` shorthand

IPv6 addresses can be shortened.

`::` represents one or more consecutive groups of zeros.

For example:

```
2001:db8:0000:0000:0000:0000:0000:0000
```

Can become:

```
2001:db8::
```

Another example:

```
0000:0000:0000:0000:0000:0000:1234:5678
```

becomes:

```
::1234:5678
```

And:

```
2001:db8:0000:0000:0000:0000:1234:5678
```

becomes:

```
2001:db8::1234:5678
```

### Memory trick

> **IPv6 = hexadecimal + colons + `::` compresses zeros**
> 

---

# 5. IPv6 in AWS VPC

This is **VERY important for SAA-C03**.

You cannot create an IPv6-only VPC in the normal AWS VPC model described here.

IPv4 is always present.

```
VPC
├── IPv4 CIDR       ← Required
└── IPv6 CIDR       ← Optional
```

So you can enable IPv6 **in addition to IPv4**.

This is called:

> **Dual-stack**
> 

---

# 6. What is Dual-Stack?

Dual-stack means your resources can communicate using **both IPv4 and IPv6**.

For example:

```
                VPC
        ┌───────────────┐
        │               │
        │ IPv4          │
        │ 10.0.0.0/16   │
        │               │
        │ IPv6          │
        │ 2001:db8::/56 │
        │               │
        └───────┬───────┘
                │
               EC2
          ┌─────┴─────┐
          │           │
       IPv4          IPv6
      10.0.0.5    2001:db8::...
          │           │
          └─────┬─────┘
                │
                ▼
          Internet Gateway
                │
                ▼
             Internet
```

### Exam keyword

If you see:

> **"Both IPv4 and IPv6"**
> 

Think:

**Dual-stack**

---

# 7. IPv6 addresses in AWS are public

This is one of the most important differences.

With IPv4, AWS has private IPv4 addresses such as:

```
10.0.0.5
172.16.0.10
192.168.1.20
```

IPv6 doesn't have the same concept of AWS private IPv4 ranges.

For SAA-C03, remember:

> **AWS IPv6 addresses are globally unique and Internet-routable.**
> 

But that does **NOT** mean the resource is automatically accessible from the Internet.

Security controls still matter.

For example:

```
Internet
   │
   ▼
IPv6 EC2
   │
   ├── Security Group
   └── NACL
```

If the Security Group doesn't allow inbound traffic, the Internet still can't connect.

---

# 8. IPv6 and Internet Gateway

An **Internet Gateway (IGW)** supports both:

```
IPv4
IPv6
```

So an EC2 instance can communicate with the Internet through the IGW using either protocol.

Example:

```
                    Internet
                  /          \
                 /            \
              IPv4            IPv6
               │                │
               └──────┬─────────┘
                      │
               Internet Gateway
                      │
                     VPC
                      │
                     EC2
```

---

# 9. IPv6 subnet example

Suppose you create:

```
VPC
IPv4: 10.0.0.0/16
IPv6: 2001:db8:1234:5678::/56
```

You can have subnets with IPv4 and IPv6 addressing.

Example:

```
VPC
IPv4: 10.0.0.0/16
IPv6: 2001:db8:1234:5678::/56

        │
        ├── Public Subnet
        │      IPv4: 10.0.1.0/24
        │      IPv6: 2001:db8:1234:5678::/64
        │
        └── Private Subnet
               IPv4: 10.0.2.0/24
               IPv6: 2001:db8:1234:5678:1::/64
```

The exact CIDRs can vary; the important exam concept is **IPv4 + IPv6 can coexist**.

---

# 10. Very important: IPv6 does NOT use NAT Gateway for Internet access

This is a common SAA-C03 trap.

With IPv4:

```
Private EC2
    │
    ▼
NAT Gateway
    │
    ▼
Internet Gateway
    │
    ▼
Internet
```

With IPv6, the resource already has a globally routable IPv6 address.

For IPv6 outbound-only Internet access, AWS provides:

> **Egress-only Internet Gateway**
> 

Architecture:

```
Private IPv6 EC2
       │
       ▼
Egress-only Internet Gateway
       │
       ▼
   Internet
```

---

# 11. Egress-Only Internet Gateway

Think of it as:

> **IPv6 equivalent of NAT Gateway for outbound-only Internet access**
> 

But don't say they are technically identical.

Its purpose is:

### Allowed

```
EC2 → Internet
```

### Not allowed as an unsolicited inbound connection

```
Internet → EC2
```

So it's useful when IPv6 resources need to **initiate outbound connections** but shouldn't be directly reachable from the Internet.

### Memory trick

> **Egress = Exit**
> 

So:

> **Egress-only IGW = IPv6 can EXIT, Internet can't initiate inbound connections.**
> 

---

# 12. IPv4 NAT vs IPv6 Egress-only IGW

|  | NAT Gateway | Egress-only IGW |
| --- | --- | --- |
| Used for | IPv4 | IPv6 |
| Primary purpose | Outbound Internet | Outbound Internet |
| Translates addresses? | ✅ NAT | ❌ |
| Inbound Internet connection | ❌ | ❌ |
| Requires IGW? | Yes | It's an Internet Gateway-related VPC component |
| Typical scenario | Private IPv4 subnet needs Internet | Private IPv6 subnet needs outbound Internet |
| Cost | Charged | No NAT processing charge |

### Exam shortcut

```
IPv4 private subnet + Internet
        ↓
    NAT Gateway

IPv6 + outbound Internet only
        ↓
Egress-only Internet Gateway
```

---

# 13. IPv4 exhaustion — very important scenario

Suppose your VPC is:

```
10.0.0.0/16
```

And your subnet is:

```
10.0.0.0/24
```

AWS reserves 5 IPv4 addresses.

Therefore:

```
256 total IPv4
- 5 AWS reserved
----------------
251 usable
```

If your subnet runs out of IPv4 addresses, you may not be able to launch another EC2 instance.

### Important point

Having IPv6 addresses available does **not** solve the problem if the resource still requires an IPv4 address.

Remember:

> **IPv4 cannot be disabled.**
> 

So if an EC2 launch fails because the subnet has no available IPv4 addresses, adding IPv6 does not magically fix the IPv4 shortage.

---

# 14. What should you do when IPv4 runs out?

The course concept you should remember:

> **Add another IPv4 CIDR to the subnet/VPC where appropriate, or create/use another subnet with sufficient IPv4 space.**
> 

For example:

```
VPC
10.0.0.0/16

Subnet A
10.0.1.0/24
       ↓
IPv4 addresses exhausted

Solution:
Plan additional IPv4 address space / subnet
```

Don't confuse:

> **IPv6 has huge address space**
> 

with:

> **IPv6 replaces the required IPv4 addressing in the VPC**
> 

It doesn't.

---

# 15. Real-world scenario

Imagine a company runs an application:

```
                  Internet
                      │
                      ▼
                Internet Gateway
                      │
          ┌───────────┴───────────┐
          │                       │
        IPv4                    IPv6
          │                       │
          ▼                       ▼
       EC2 App                  EC2 App
     10.0.0.5             2001:db8::1234
```

The company wants to start supporting IPv6 users but doesn't want to redesign the entire application.

They enable IPv6 in their VPC and operate in **dual-stack mode**.

Now:

```
IPv4 users ───────► IPv4
                     │
                     ▼
                    App

IPv6 users ───────► IPv6
                     │
                     ▼
                    App
```

This is one of the main reasons dual-stack is useful.

---

# 16. SAA-C03 traps you should know

### ❌ Trap 1

> "IPv6 can replace IPv4 in an AWS VPC."
> 

**Wrong.**

IPv4 cannot be disabled.

---

### ❌ Trap 2

> "IPv6 addresses in AWS are private by default."
> 

**Wrong.**

AWS IPv6 addresses are globally unique/Internet-routable.

Security Groups and NACLs still control access.

---

### ❌ Trap 3

> "Use NAT Gateway for IPv6 Internet access."
> 

Generally **wrong** for IPv6.

For IPv6 outbound-only connectivity:

**Egress-only Internet Gateway**

---

### ❌ Trap 4

> "If IPv4 addresses run out, just enable IPv6."
> 

**Wrong.**

The VPC/subnet still requires IPv4 addressing.

---

### ❌ Trap 5

> "Internet Gateway only supports IPv4."
> 

**Wrong.**

An IGW supports **IPv4 and IPv6**.

---

# 🧠 Final SAA-C03 memory sheet

| Exam clue | Answer |
| --- | --- |
| IPv4 address exhaustion | **IPv6** |
| 128-bit addressing | **IPv6** |
| `2001:db8::...` | **IPv6** |
| Hexadecimal + `:` | **IPv6** |
| IPv4 cannot be disabled | **Always keep IPv4** |
| IPv4 + IPv6 together | **Dual-stack** |
| IPv6 Internet connectivity | **Internet Gateway** |
| IPv6 outbound-only Internet | **Egress-only Internet Gateway** |
| IPv4 private subnet → Internet | **NAT Gateway** |
| IPv6 has private AWS address ranges like `10.0.0.0/8` | **No** |
| IPv4 subnet runs out of addresses | **Need more IPv4 capacity** |
| IPv6 addresses automatically mean unrestricted access | **No — SG/NACL still apply** |

### 🔥 One-line exam takeaway

> **AWS VPC always requires IPv4; IPv6 is optional and enables dual-stack networking. IPv6 addresses are globally routable, and for IPv6 outbound-only Internet access, use an Egress-only Internet Gateway—not a NAT Gateway.**
>
