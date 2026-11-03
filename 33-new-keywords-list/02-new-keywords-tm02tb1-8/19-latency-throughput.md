# Latency Throughput

Absolutely. These four terms become easy if you separate **two different ideas**:

- **Latency = How long one request takes to get a response**
- **Throughput = How much data/work can be handled in a given time**

---

## 1. 🐌 High Latency

**High latency = each request takes a long time to get a response.**

Example:

You click **"Buy Now"**:

```
You → Request → Server
              ⏳ 3 seconds
You ← Response
```

The request eventually works, but it takes a long time.

### Real-world example

A video call has high latency:

> You say "Hello" → the other person hears it 2 seconds later.
> 

That's **high latency**.

**AWS example:**

An application server is very far from its users, so requests take longer to reach the server.

---

# 2. 📉 Low Throughput

**Low throughput = the system can handle only a small amount of data/work per second.**

Example:

```
Server
 ↓
Can process only 10 requests/second
```

If 1,000 users send requests:

```
1000 requests
     ↓
Server handles slowly
     ↓
Requests pile up
```

### Real-world example

Imagine a **one-lane road**:

```
🚗 🚗 🚗 🚗 🚗
──────────────→
     1 lane
```

Cars can move, but only a limited number can pass per minute.

That's **low throughput**.

---

# 3. ⚡ Low Latency

**Low latency = each request gets a response very quickly.**

Example:

```
You → Request → Server
              ⚡ 20 ms
You ← Response
```

The individual request is very fast.

### Real-world example

You press a light switch:

```
Press switch → 💡 ON
```

Almost immediately.

That's **low latency**.

**AWS example:**

CloudFront caches content close to users, reducing the time required to retrieve that content.

---

# 4. 📈 High Throughput

**High throughput = the system can handle a large amount of data/work per second.**

Example:

```
Server
 ↓
Can process 10,000 requests/second
```

Or:

```
100 MB/second
1 GB/second
10,000 transactions/second
```

### Real-world example

Imagine a **10-lane highway**:

```
🚗 🚗 🚗 🚗 🚗 🚗 🚗
══════════════════════
       many lanes
```

A huge number of cars can pass through every minute.

That's **high throughput**.

---

# 🔥 The MOST Important Difference

Imagine a restaurant.

### Latency = How long YOU wait for your food

```
Order → ⏳ → Food
          5 min
```

### Throughput = How MANY customers the restaurant can serve

```
🍔 🍔 🍔 🍔 🍔
       ↓
100 customers/hour
```

So:

> **Latency = speed of one operation**
> 

> **Throughput = amount of operations/data handled over time**
> 

---

# Put all 4 together

|  | Low | High |
| --- | --- | --- |
| **Latency** | ⚡ Fast response | 🐌 Slow response |
| **Throughput** | 📉 Handles little | 📈 Handles a lot |

### Example

**Low latency + high throughput:**

```
⚡ Each request is fast
+
📈 System handles many requests
```

This is generally what you want for a high-performance system.

---

## 🧠 AWS Exam Memory Trick

Remember:

> **Latency = WAIT TIME** ⏳
> 
> 
> **Throughput = AMOUNT/TIME** 📦
> 

And:

> **Low latency → fast response**
> 

> **High latency → slow response**
> 

> **Low throughput → small amount handled**
> 

> **High throughput → large amount handled**
> 

### One important point

**Latency and throughput are not the same thing.**

A system can have:

```
Low latency + Low throughput
```

or

```
High latency + High throughput
```

For example, a system might process **huge amounts of data** (high throughput) but take a long time to start/finish each individual job (high latency).
