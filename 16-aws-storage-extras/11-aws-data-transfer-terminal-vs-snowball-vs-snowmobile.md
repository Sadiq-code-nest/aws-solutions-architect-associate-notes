# AWS Data Transfer Terminal Vs Snowball Vs Snowmobile

Yes — these are related to **moving large amounts of data into/out of AWS**, but they solve different problems.

The easiest way to understand them is:

> **Data Transfer Terminal = you bring your storage device to AWS's physical location.**
> 
> 
> **Snowball = AWS sends you a rugged storage appliance.**
> 
> **Snowmobile = AWS brings a giant data truck to you.**
> 

---

## 1. Data Transfer Terminal

- Images
    
    
    
    
    
    

A **Data Transfer Terminal** is a secure AWS facility where you can physically bring your own storage devices and transfer data to AWS.

Think:

```
Your Data Center
      │
      │ Put data on your storage device
      ▼
Your storage device
      │
      │ physically transport
      ▼
AWS Data Transfer Terminal
      │
      ▼
AWS
```

### When would you use it?

Suppose you have:

```
500 TB of data
```

You already have your own storage hardware and don't want to wait for a network transfer.

You physically take the storage device to an AWS Data Transfer Terminal.

**Key idea:**

> **You bring the storage to AWS.**
> 

---

# 2. AWS Snowball

- Images
    
    
    
    
    
    

With **Snowball**, AWS sends you a physical device.

```
AWS
 │
 │ ships Snowball
 ▼
Your Data Center
 │
 │ copy data
 ▼
Snowball
 │
 │ ship back
 ▼
AWS
 │
 ▼
S3
```

So the direction is:

> **AWS brings the device to you.**
> 

### Example

You have:

```
100 TB
```

in your on-premises data center.

Internet transfer would take too long.

You request Snowball.

AWS sends:

```
Snowball
```

You copy:

```
100 TB → Snowball
```

Then ship it back.

AWS loads the data into AWS.

---

# 3. Snowmobile

- Images
    
    
    
    

Now we're talking **massive amounts of data**.

Snowmobile is essentially a **very large-scale data transfer truck/container**.

Think:

```
          🚛 AWS Snowmobile

Your Data Center
       │
       │
       ▼
 ┌───────────────────┐
 │   SNOWMOBILE      │
 │                   │
 │ Massive storage   │
 └───────────────────┘
       │
       │
       ▼
      AWS
```

It's intended for **exabyte-scale data transfers**.

For example:

```
1 EB
```

That's:

```
1,000,000 TB
```

You obviously don't want to move that over a normal internet connection.

---

# 4. The critical comparison

|  | Data Transfer Terminal | Snowball | Snowmobile |
| --- | --- | --- | --- |
| Who provides storage? | **You** | **AWS** | **AWS** |
| Where is it? | You bring device to AWS facility | AWS ships device to you | AWS brings truck to you |
| Typical scale | Large | TB / PB-scale | Extremely large / EB-scale |
| Physical transportation | **You transport device** | Ship appliance | AWS transports truck |
| Best for | Existing storage + physical AWS transfer | Offline data migration | Massive data migration |
| Network required? | No for the bulk transfer | No for bulk transfer | No for bulk transfer |

---

# 5. The easiest mental picture

Imagine you have **100 TB**.

### Option A — Data Transfer Terminal

You say:

> "I already have my storage."
> 

```
YOU
 │
 │ 🚚 bring storage
 ▼
AWS Data Transfer Terminal
```

**You bring the data.**

---

### Option B — Snowball

You say:

> "AWS, send me a device."
> 

```
AWS
 │
 │ 🚚 Snowball
 ▼
YOU
 │
 │ copy data
 ▼
AWS
```

**AWS sends the device.**

---

### Option C — Snowmobile

You say:

> "I have an absolutely enormous amount of data."
> 

AWS basically says:

> "We'll bring the truck."
> 

```
AWS 🚛
     │
     ▼
Your Data Center
     │
     │ massive data
     ▼
AWS
```

---

# 6. How this appears in SAA-C03 questions

### Question says:

> "Customer has existing storage devices and wants to physically transfer data to AWS without sending the devices through normal shipping."
> 

Think:

**Data Transfer Terminal**

---

### Question says:

> "Customer has 80 TB of data and network bandwidth is insufficient. AWS should provide a physical device."
> 

Think:

**Snowball**

---

### Question says:

> "Customer needs to migrate hundreds of petabytes/exabytes of data to AWS."
> 

Think:

**Snowmobile**

---

# 🧠 One-line exam cheat sheet

```
Data Transfer Terminal
        ↓
YOU bring the storage

Snowball
        ↓
AWS sends a BOX/device

Snowmobile
        ↓
AWS sends a TRUCK
```

And connect this to your previous Direct Connect question:

```
DIRECT CONNECT
     ↓
Private network highway
     ↓
Continuous network transfer

DATASYNC
     ↓
Actually transfers files/data

DATA TRANSFER TERMINAL
     ↓
Physically bring your storage

SNOWBALL
     ↓
AWS sends storage appliance

SNOWMOBILE
     ↓
AWS sends massive storage truck
```

**That's the distinction I'd memorize for SAA-C03.**
