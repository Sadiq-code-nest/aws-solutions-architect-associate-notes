# Glacier Retrieval Tiers

The question mentions three retrieval options:

```
             Glacier Retrieval
                    |
       +------------+------------+
       |            |            |
       v            v            v
   Expedited      Standard      Bulk
       |            |            |
    Minutes        Hours         Hours
       |            |            |
     $$$$           $$            $
```

The exact timing can depend on the storage class/service details, but for SAA-C03 reasoning, remember:

> **Expedited = minutes; Standard = hours; Bulk = longer/cheaper bulk retrieval.**
> 

### Exam trigger

> **"<15 minutes" → Expedited**
> 

---

# 4.5 Expedited Retrieval

### Definition

Expedited Retrieval is Glacier's fastest retrieval option for supported archival data. It is designed to make archived objects available in **minutes**, rather than the hours associated with Standard or Bulk retrieval.

```
Glacier
  |
  v
Expedited Retrieval
  |
  | Fast retrieval
  v
Application
```

### Example

At 2 PM:

```
Auditor requests transaction records
             |
             v
       Expedited Retrieval
             |
             v
      Data retrieved quickly
```

### The important catch

Expedited doesn't by itself mean:

> **"Guaranteed capacity is always available."**
> 

For guaranteed availability, use:

```
Provisioned Retrieval Capacity
            +
Expedited Retrieval
```

### Common confusion

| Expedited | Standard |
| --- | --- |
| Fastest | Normal |
| Minutes | Hours |
| Higher cost | Lower cost |
| For urgent retrieval | For routine retrieval |

### Exam trigger

> **"Urgent Glacier retrieval in minutes" → Expedited Retrieval**
> 

---

# 4.6 Provisioned Retrieval Capacity

### Definition

Provisioned Retrieval Capacity allows you to purchase dedicated capacity for **Expedited Retrieval**, ensuring retrieval capacity is available when you need it.

```
             Purchase capacity
                    |
                    v
        +----------------------+
        | Provisioned Retrieval|
        | Capacity              |
        +----------------------+
                    |
                    v
          Expedited Retrieval
                    |
                    v
             Archived data
```

### Example

Requirement:

```
Guaranteed throughput = 150 MB/s
```

Provision:

```
1 capacity unit
       ↓
150 MB/s expedited retrieval capacity
```

If the question says:

> **"must be available at all times / under all circumstances"**
> 

this is a huge signal.

### Common confusion

| Provisioned Retrieval Capacity | Expedited Retrieval |
| --- | --- |
| Guarantees/allocates retrieval capacity | Retrieval speed/tier |
| Capacity guarantee | Fast retrieval method |
| "Will capacity be available?" | "How fast do I retrieve?" |
| Can be purchased | Selected when initiating retrieval |

### Exam trigger

> **"Guaranteed Expedited Retrieval capacity" or "150 MB/s" → Provisioned Retrieval Capacity**
> 

---

# 4.7 Standard Retrieval

### Definition

Standard Retrieval is the normal retrieval option for Glacier Flexible Retrieval. It provides access in **hours**, making it unsuitable when the requirement is measured in minutes.

```
Glacier
   |
   v
Standard Retrieval
   |
   | Wait hours
   v
Retrieved data
```

### Example

A company performs a monthly archive report and can wait several hours.

```
Monthly report
     ↓
Standard Retrieval
     ↓
Wait is acceptable
```

### Common confusion

| Standard | Expedited |
| --- | --- |
| Routine retrieval | Urgent retrieval |
| Hours | Minutes |
| Lower cost | Higher cost |

### Exam trigger

> **"Can wait hours for archived data" → Standard Retrieval**
> 

---

# 4.8 Bulk Retrieval

### Definition

Bulk Retrieval is intended for retrieving **large amounts of archived data** at a lower cost, but with significantly longer retrieval times.

```
Huge archive
     |
     v
Bulk Retrieval
     |
     v
Large-scale retrieval
     |
     v
Wait longer
```

### Example

A company wants to restore several terabytes of historical data for a planned migration and doesn't care if retrieval takes hours.

**Bulk Retrieval** is appropriate.

### Common confusion

| Bulk | Expedited |
| --- | --- |
| Large-volume retrieval | Fast retrieval |
| Cheapest of the Glacier retrieval options | Most expensive/faster option |
| Hours | Minutes |
| Planned jobs | Urgent jobs |

### Exam trigger

> **"Large volume + cost-sensitive + time isn't critical" → Bulk Retrieval**
>
