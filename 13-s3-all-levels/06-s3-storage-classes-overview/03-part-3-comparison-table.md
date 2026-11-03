# Amazon S3 Storage Classes — MASTER COMPARISON TABLE

This is the **single master table** I’d use for **AWS SAA-C03 revisions**. It includes the current S3 storage classes, access pattern, AZ design, availability, retrieval behaviour, minimum storage duration, object-size considerations, cost behavior, and the exam scenario to recognise.

AWS currently lists **8 primary S3 storage classes** for general S3 workloads: Standard, Intelligent-Tiering, Express One Zone, Standard-IA, One Zone-IA, Glacier Instant Retrieval, Glacier Flexible Retrieval, and Glacier Deep Archive. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html?utm_source=chatgpt.com))

## 🏆 MASTER TABLE

| Feature | **S3 Standard** | **S3 Intelligent-Tiering** | **S3 Express One Zone** | **S3 Standard-IA** | **S3 One Zone-IA** | **S3 Glacier Instant Retrieval** | **S3 Glacier Flexible Retrieval** | **S3 Glacier Deep Archive** |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Main purpose** | General-purpose | Unknown/changing access | Very high-performance | Infrequent access | Infrequent + recreatable | Archive + instant access | Archive + occasional retrieval | Very long-term archive |
| **Access frequency** | Frequently | Unknown / unpredictable | Frequently / latency-sensitive | About monthly | About monthly | About quarterly | About 1–2 times/year | Less than once/year |
| **Access latency** | Milliseconds | Milliseconds* | **Single-digit ms** | Milliseconds | Milliseconds | **Milliseconds** | Minutes–hours | Hours |
| **Real-time GET?** | ✅ Yes | ✅ Yes, depending on tier | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes | ❌ Restore first | ❌ Restore first |
| **Availability Zones** | ≥ 3 | ≥ 3 | **1 AZ** | ≥ 3 | **1 AZ** | ≥ 3 | ≥ 3 | ≥ 3 |
| **AZ failure resilient?** | ✅ | ✅ | ❌ | ✅ | ❌ | ✅ | ✅ | ✅ |
| **Designed durability** | **99.999999999% (11 9s)** | **99.999999999%** | **99.999999999%** | **99.999999999%** | **99.999999999%** | **99.999999999%** | **99.999999999%** | **99.999999999%** |
| **Designed availability** | **99.99%** | **99.9%** | **99.95%** | **99.9%** | **99.5%** | **99.9%** | **99.99% after restore** | **99.99% after restore** |
| **Minimum storage duration** | None | None | None | **30 days** | **30 days** | **90 days** | **90 days** | **180 days** |
| **Minimum billable object size** | None | None† | None | **128 KB** | **128 KB** | **128 KB** | Archive metadata considerations | Archive metadata considerations |
| **Retrieval fee** | No | **No retrieval fee** | Pricing differs | **Yes** | **Yes** | **Yes** | **Yes** | **Yes** |
| **Storage price** | Higher | Automatic optimization | High-performance pricing | Lower than Standard | Lower than Standard-IA | Lower than IA classes | Very low | **Lowest archive storage** |
| **Best for** | Normal production data | Unknown access pattern | Low-latency/high-request workloads | Long-lived infrequently accessed data | Re-creatable infrequent data | Rarely accessed but instant retrieval required | Archive where minutes/hours are acceptable | Compliance/archive for years |
| **Typical exam keyword** | "Frequently accessed" | "Unknown/changing access" | "Single AZ + very low latency" | "Infrequent + rapid access + resilient" | "Infrequent + can recreate" | "Archive + milliseconds" | "Archive + minutes/hours" | "Long-term archive + lowest cost" |
- Intelligent-Tiering has automatic low-latency access tiers plus optional archive tiers with asynchronous retrieval.

† Objects smaller than 128 KB in Intelligent-Tiering are not monitored for automatic tiering and remain in the Frequent Access tier. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html?utm_source=chatgpt.com))

---

# 🔥 THE EASIEST WAY TO UNDERSTAND ALL 8

Think of S3 classes as a **spectrum**:

```
                 FREQUENTLY ACCESSED
                         │
                         ▼
              ┌─────────────────────┐
              │    S3 STANDARD      │
              └─────────────────────┘
                         │
               Unknown / changing?
                         │
                         ▼
           ┌─────────────────────────┐
           │ S3 INTELLIGENT-TIERING  │
           └─────────────────────────┘
                         │
              Infrequently accessed
                    /            \
                   /              \
                  ▼                ▼
        Need AZ resilience?    Can recreate?
              YES                   YES
               │                    │
               ▼                    ▼
        STANDARD-IA             ONE ZONE-IA
               │
               │
       Archive but instant?
               │
               ▼
       GLACIER INSTANT
          RETRIEVAL
               │
       Archive, delay OK?
               │
               ▼
      GLACIER FLEXIBLE
          RETRIEVAL
               │
      Very rarely accessed?
               │
               ▼
       GLACIER DEEP
           ARCHIVE
```

And separately:

```
Need extremely low latency
+ single AZ is acceptable
             │
             ▼
      S3 EXPRESS ONE ZONE
```

---

# 1️⃣ S3 STANDARD

### Think:

> **Normal S3 — frequently accessed production data.**
> 

| Property | S3 Standard |
| --- | --- |
| Access | Frequent |
| Latency | Milliseconds |
| AZs | ≥ 3 |
| Availability | 99.99% |
| Durability | 99.999999999% |
| Minimum duration | None |
| Retrieval fee | No |
| Use case | General-purpose |

### Example

A website stores:

```
profile pictures
product images
CSS/JS files
application assets
```

Users access these files constantly.

→ **S3 Standard**

### SAA-C03 keyword

> **Frequently accessed + high availability + millisecond access**
> 

→ **S3 Standard**

---

# 2️⃣ S3 INTELLIGENT-TIERING ⭐⭐⭐

### Think:

> **"I don't know how often users will access this data."**
> 

Instead of manually predicting the access pattern, S3 automatically moves objects between access tiers based on usage. It has a small monitoring/automation charge, but **no retrieval charges**. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/?utm_source=chatgpt.com))

### Automatic tiers

```
              S3 Intelligent-Tiering
                       │
              ┌────────┴────────┐
              ▼                 ▼
        Frequent Access    Infrequent Access
              │                 │
              └────────┬────────┘
                       ▼
              Archive Instant Access
                       │
                       ▼
                Archive Access
                       │
                       ▼
              Deep Archive Access
```

The archive tiers are optional.

### Example

You have millions of user-generated files.

You don't know whether a particular file will be accessed tomorrow or six months from now.

→ **S3 Intelligent-Tiering**

### SAA-C03 keyword

> **Unknown / unpredictable / changing access pattern**
> 

→ **Intelligent-Tiering**

### Major advantage

**No retrieval fees** for Intelligent-Tiering, while automatic tiering optimizes storage cost. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html?utm_source=chatgpt.com))

---

# 3️⃣ S3 EXPRESS ONE ZONE ⭐⭐⭐

This one is easy to confuse with One Zone-IA.

### Think:

> **"I need extremely fast S3 and can keep it in one AZ."**
> 

| Property | Express One Zone |
| --- | --- |
| AZ | **1** |
| Latency | **Single-digit milliseconds** |
| Availability | 99.95% |
| Durability | 99.999999999% |
| Minimum duration | None |
| Main purpose | High-performance workloads |

AWS describes it as a high-performance, single-AZ storage class designed for latency-sensitive workloads, with access speeds up to 10× S3 Standard in suitable workloads. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html?utm_source=chatgpt.com))

### Example

An ML workload repeatedly accesses huge datasets and needs very low latency.

```
EC2 / SageMaker
       │
       │ same AZ
       ▼
S3 Express One Zone
```

### SAA-C03 keyword

> **High performance + single AZ + single-digit millisecond latency**
> 

→ **S3 Express One Zone**

---

# 4️⃣ S3 STANDARD-IA

IA = **Infrequent Access**

### Think:

> **"I don't access it often, but when I do, I need it immediately."**
> 

| Property | Standard-IA |
| --- | --- |
| Access | About monthly |
| Retrieval | Milliseconds |
| AZs | ≥ 3 |
| Availability | 99.9% |
| Minimum duration | 30 days |
| Minimum billable size | 128 KB |
| Retrieval fee | Yes |

### Example

A company stores backup data that is rarely accessed but must be immediately available during a disaster.

→ **S3 Standard-IA**

AWS recommends Standard-IA for the primary or only copy of long-lived data that can't easily be recreated. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html?utm_source=chatgpt.com))

### SAA-C03 keyword

> **Infrequent + immediate retrieval + must survive AZ failure**
> 

→ **Standard-IA**

---

# 5️⃣ S3 ONE ZONE-IA

### Think:

> **"Infrequent data + I can recreate it + I accept one AZ."**
> 

| Property | One Zone-IA |
| --- | --- |
| AZ | **1** |
| Access | About monthly |
| Retrieval | Milliseconds |
| Availability | 99.5% |
| Durability | 99.999999999% |
| Minimum duration | 30 days |
| Minimum billable size | 128 KB |
| Retrieval fee | Yes |
| AZ disaster resilience | ❌ |

### Example

You have a backup that already exists elsewhere:

```
Primary data
     │
     ├── Original
     │
     └── Secondary copy
             ↓
        S3 One Zone-IA
```

If the AZ is lost, you can recreate the data from the original source.

### SAA-C03 keyword

> **Infrequent + can recreate + lower cost + one AZ acceptable**
> 

→ **S3 One Zone-IA**

AWS specifically recommends it for recreatable data and certain replica/secondary-copy scenarios. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html?utm_source=chatgpt.com))

---

# 6️⃣ S3 GLACIER INSTANT RETRIEVAL ⭐⭐⭐

### Think:

> **"Archive it cheaply, but I still need millisecond access."**
> 

| Property | Glacier Instant Retrieval |
| --- | --- |
| Access | About quarterly |
| Retrieval | **Milliseconds** |
| AZs | ≥ 3 |
| Availability | 99.9% |
| Minimum duration | 90 days |
| Minimum billable size | 128 KB |
| Retrieval fee | Yes |
| Restore required? | ❌ No |

### Example

Medical images are rarely accessed, but when a patient appointment occurs, doctors need them immediately.

→ **Glacier Instant Retrieval**

AWS specifically positions this class for long-lived data accessed around once per quarter while requiring millisecond retrieval. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/glacier-storage-classes.html?utm_source=chatgpt.com))

### SAA-C03 keyword

> **Archive + rarely accessed + milliseconds**
> 

→ **Glacier Instant Retrieval**

---

# 7️⃣ S3 GLACIER FLEXIBLE RETRIEVAL ⭐⭐⭐

Formerly called **S3 Glacier**.

### Think:

> **"Archive + I don't need immediate access."**
> 

| Property | Glacier Flexible Retrieval |
| --- | --- |
| Access | About 1–2 times/year |
| AZs | ≥ 3 |
| Minimum duration | 90 days |
| Retrieval fee | Yes |
| Immediate GET | ❌ |
| Restore required | ✅ |
| Expedited | 1–5 minutes |
| Standard | 3–5 hours |
| Bulk | 5–12 hours |

AWS's current retrieval guidance gives these typical ranges. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects-retrieval-options.html?utm_source=chatgpt.com))

### Example

A company keeps old backup data that might need to be recovered once or twice per year.

They can wait several hours.

→ **Glacier Flexible Retrieval**

### SAA-C03 keyword

> **Archive + minutes/hours acceptable + occasional retrieval**
> 

→ **Glacier Flexible Retrieval**

---

# 8️⃣ S3 GLACIER DEEP ARCHIVE ⭐⭐⭐

### Think:

> **"Store it for years and almost never access it."**
> 

| Property | Deep Archive |
| --- | --- |
| Access | Less than once/year |
| AZs | ≥ 3 |
| Minimum duration | **180 days** |
| Retrieval fee | Yes |
| Immediate GET | ❌ |
| Restore required | ✅ |
| Standard retrieval | Up to ~12 hours |
| Bulk retrieval | Up to ~48 hours |
| Storage cost | **Lowest among S3 archive classes** |

AWS recommends it for long-term archive data that is very rarely accessed. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/glacier-storage-classes.html?utm_source=chatgpt.com))

### Example

A financial company must retain records for many years for regulatory requirements.

They almost never retrieve them.

→ **S3 Glacier Deep Archive**

### SAA-C03 keyword

> **Years + compliance + extremely rare access + lowest storage cost**
> 

→ **Glacier Deep Archive**

---

# 🧠 THE MOST IMPORTANT COMPARISON

## Standard vs Standard-IA vs One Zone-IA

|  | Standard | Standard-IA | One Zone-IA |
| --- | --- | --- | --- |
| Frequent access | ✅ | ❌ | ❌ |
| Infrequent access | ❌ | ✅ | ✅ |
| Millisecond access | ✅ | ✅ | ✅ |
| ≥3 AZs | ✅ | ✅ | ❌ |
| Single AZ | ❌ | ❌ | ✅ |
| AZ failure resilient | ✅ | ✅ | ❌ |
| Retrieval charge | ❌ | ✅ | ✅ |
| Minimum duration | None | 30 days | 30 days |
| Can recreate data? | Not required | Not required | **Should be** |
| Main decision | Performance | Resilience | Cost + recreatable |

---

# 🧠 GLACIER COMPARISON

|  | Glacier Instant Retrieval | Glacier Flexible Retrieval | Glacier Deep Archive |
| --- | --- | --- | --- |
| Archive | ✅ | ✅ | ✅ |
| Real-time GET | **✅** | ❌ | ❌ |
| Restore required | ❌ | ✅ | ✅ |
| Retrieval | **Milliseconds** | Minutes → hours | Hours |
| Typical access | Quarterly | 1–2×/year | <1×/year |
| Min duration | 90 days | 90 days | **180 days** |
| Retrieval cost | Yes | Yes | Yes |
| Cheapest storage | No | Very low | **Lowest** |
| Best for | Archive + instant access | Archive + flexible retrieval | Long-term preservation |

---

# ⚡ THE TWO "ONE ZONE" CLASSES

This is a **very important distinction**:

|  | S3 One Zone-IA | S3 Express One Zone |
| --- | --- | --- |
| AZs | 1 | 1 |
| Main objective | **Lower storage cost** | **High performance** |
| Access | Infrequent | Frequent / latency-sensitive |
| Latency | ms | **Single-digit ms** |
| Availability | 99.5% | **99.95%** |
| Minimum duration | 30 days | None |
| Recreatable data | ✅ Recommended | Not the defining requirement |
| Typical use | Secondary/recreatable data | ML/HPC/analytics/latency-sensitive |

### Memory:

> **One Zone-IA = CHEAP + INFREQUENT**
> 

> **Express One Zone = FAST + FREQUENT**
> 

---

# 💰 COST LOGIC — DON'T MEMORIZE PRICES

AWS pricing varies by Region, so for SAA-C03 it's much more useful to understand the **cost model** than memorize dollar amounts.

```
                   STORAGE COST
                        ↓
                 Standard
                        ↓
              Standard-IA / One Zone-IA
                        ↓
              Glacier Instant Retrieval
                        ↓
              Glacier Flexible Retrieval
                        ↓
              Glacier Deep Archive
```

But:

> **Cheaper storage does NOT necessarily mean cheaper overall.**
> 

Because some classes have:

- Retrieval charges
- Minimum storage duration
- Minimum billable object size
- Early deletion/transition charges

For example, Standard-IA and One Zone-IA have 30-day minimum storage duration and 128-KB minimum billable object size. Glacier Instant Retrieval has a 90-day minimum and 128-KB minimum billable size. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html?utm_source=chatgpt.com))

---

# ⏱️ RETRIEVAL SPEED — MASTER VIEW

```
FASTEST
  │
  ├── S3 Standard
  ├── Intelligent-Tiering low-latency tiers
  ├── Express One Zone
  ├── Standard-IA
  ├── One Zone-IA
  └── Glacier Instant Retrieval
           │
           │ milliseconds
           ▼
     Glacier Flexible Retrieval
           │
           │ minutes → hours
           ▼
      Glacier Deep Archive
           │
           │ hours
           ▼
SLOWEST
```

**Critical distinction:**

```
Glacier Instant Retrieval
        ↓
GET immediately ✅

Glacier Flexible Retrieval
        ↓
Restore first ❗

Glacier Deep Archive
        ↓
Restore first ❗
```

AWS confirms that Flexible Retrieval and Deep Archive require restoration before access; Instant Retrieval provides real-time access. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects-retrieval-options.html?utm_source=chatgpt.com))

---

# 🗄️ MINIMUM STORAGE DURATION

| Storage Class | Minimum |
| --- | --- |
| S3 Standard | **None** |
| Intelligent-Tiering | **None** |
| Express One Zone | **None** |
| Standard-IA | **30 days** |
| One Zone-IA | **30 days** |
| Glacier Instant Retrieval | **90 days** |
| Glacier Flexible Retrieval | **90 days** |
| Glacier Deep Archive | **180 days** |

### Exam trap ⭐

If you transition/delete an object before its minimum storage duration, you can incur charges for the remaining minimum period. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/glacier-storage-classes.html?utm_source=chatgpt.com))

---

# 📦 MINIMUM OBJECT SIZE

| Storage Class | Minimum billable object size |
| --- | --- |
| Standard | None |
| Intelligent-Tiering | Special handling for <128 KB |
| Express One Zone | None |
| Standard-IA | **128 KB** |
| One Zone-IA | **128 KB** |
| Glacier Instant Retrieval | **128 KB** |
| Glacier Flexible Retrieval | Archive metadata applies |
| Glacier Deep Archive | Archive metadata applies |

For Glacier Flexible Retrieval and Deep Archive, AWS charges additional metadata associated with archived objects; the official documentation specifies 40 KB of additional metadata per object, split between Glacier and S3 Standard rates. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html?utm_source=chatgpt.com))

---

# 🧠 SAA-C03 SCENARIO DECISION TABLE

| Scenario says... | Choose |
| --- | --- |
| Frequently accessed production data | 🟢 **S3 Standard** |
| General-purpose S3 storage | 🟢 **S3 Standard** |
| Access pattern unknown | 🟢 **Intelligent-Tiering** |
| Access pattern changes unpredictably | 🟢 **Intelligent-Tiering** |
| Automatically optimize storage cost based on access | 🟢 **Intelligent-Tiering** |
| Very low latency + single AZ | 🟢 **S3 Express One Zone** |
| Hundreds of thousands of requests/sec + low latency | 🟢 **S3 Express One Zone** |
| Infrequently accessed but needs instant access | 🟢 **Standard-IA** |
| Infrequently accessed + must survive AZ failure | 🟢 **Standard-IA** |
| Secondary backup that can be recreated | 🟢 **One Zone-IA** |
| Infrequent + one AZ acceptable | 🟢 **One Zone-IA** |
| Archive but milliseconds retrieval required | 🟢 **Glacier Instant Retrieval** |
| Archive + access once/twice a year | 🟢 **Glacier Flexible Retrieval** |
| Archive + can wait hours | 🟢 **Glacier Flexible Retrieval** |
| Long-term compliance archive | 🟢 **Glacier Deep Archive** |
| Data accessed less than once/year | 🟢 **Glacier Deep Archive** |
| Lowest S3 archive storage cost | 🟢 **Glacier Deep Archive** |

---

# 🔥 SAA-C03 "KEYWORD → ANSWER" CHEAT SHEET

```
"Frequently accessed"
       ↓
S3 STANDARD
```

```
"Unknown access pattern"
"Unpredictable access"
"Changing access pattern"
       ↓
INTELLIGENT-TIERING
```

```
"Single AZ"
"High performance"
"Single-digit millisecond"
"Very high request rate"
       ↓
S3 EXPRESS ONE ZONE
```

```
"Infrequent"
"Millisecond retrieval"
"Need AZ resilience"
       ↓
STANDARD-IA
```

```
"Infrequent"
"Can recreate"
"One AZ acceptable"
"Secondary copy"
       ↓
ONE ZONE-IA
```

```
"Archive"
"Rarely accessed"
"Milliseconds"
       ↓
GLACIER INSTANT RETRIEVAL
```

```
"Archive"
"Occasional retrieval"
"Minutes to hours acceptable"
       ↓
GLACIER FLEXIBLE RETRIEVAL
```

```
"Years"
"Compliance"
"Very rarely accessed"
"Lowest archive cost"
       ↓
GLACIER DEEP ARCHIVE
```

---

# 🏆 FINAL MASTER MEMORY MAP

```
                         AMAZON S3
                            │
             ┌──────────────┼──────────────┐
             │              │              │
         FREQUENT        INFREQUENT       ARCHIVE
             │              │              │
             ▼              ▼              ▼
       ┌──────────┐    ┌──────────┐   ┌───────────────┐
       │ Standard │    │ Standard │   │ Glacier       │
       │          │    │    IA    │   │ Instant       │
       └──────────┘    └──────────┘   │ Retrieval     │
             │              │         └───────────────┘
             │              │                 │
             │              ▼                 ▼
             │        ┌───────────┐    Glacier Flexible
             │        │ One Zone  │       Retrieval
             │        │    IA     │            │
             │        └───────────┘            ▼
             │                           Deep Archive
             │
             ▼
       Unknown pattern
             │
             ▼
      Intelligent-Tiering

      SPECIAL PERFORMANCE
             │
             ▼
      Express One Zone
```

### The **8-line memory trick**

> **Standard** → Frequently accessed
> 
> 
> **Intelligent-Tiering** → Don't know access pattern
> 
> **Express One Zone** → Extremely fast + one AZ
> 
> **Standard-IA** → Infrequent + AZ resilient
> 
> **One Zone-IA** → Infrequent + recreatable
> 
> **Glacier Instant** → Archive + milliseconds
> 
> **Glacier Flexible** → Archive + minutes/hours
> 
> **Deep Archive** → Archive + years + lowest storage cost
> 

**One final exam rule:** don't choose a class based only on "cheapest." Always check **access frequency + retrieval speed + AZ resilience + minimum duration + retrieval fees**. That combination is what SAA-C03 scenarios are testing. ([AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html?utm_source=chatgpt.com))

[AWS S3 Storage Classes — official comparison](https://aws.amazon.com/s3/storage-classes/?loc=3&nc=sn&utm_source=chatgpt.com)
