# 🚀 Aurora Custom Endpoints (Very Important for Exam)

## 🔹 First: What is an Endpoint in Aurora?

In Amazon Aurora, you don’t connect directly to instances.

Instead, you connect using **DNS endpoints**, which route traffic to DB instances inside the cluster.

Types:

- **Cluster endpoint** → Writer (read/write)
- **Reader endpoint** → All replicas (read-only load balancing)
- **Instance endpoint** → Single DB instance
- **Custom endpoint** → YOU define subset of instances

---

# 🎯 What is a Custom Endpoint?

A **Custom Endpoint** lets you create a **logical group of selected Aurora DB instances** inside a cluster and connect to them using one DNS name.

👉 You control which replicas handle specific workloads.

---

## 🧠 Simple Definition (Exam-ready)

> A custom endpoint in Aurora is a user-defined endpoint that routes connections only to a specific subset of DB instances in an Aurora cluster.
> 

---

# ⚙️ Why Custom Endpoints are Needed?

Normally:

- Reader endpoint → sends queries to ALL replicas

But what if:

- Some replicas are **bigger (high-memory)**
- Some are used for **analytics**
- Some are for **reporting only**

👉 You don’t want all replicas to receive heavy analytical queries.

So you use custom endpoints.

---

# 🧩 Real-world Example (Very Important)

Imagine this Aurora cluster:

| Instance | Type | Purpose |
| --- | --- | --- |
| DB1 | writer | OLTP (transactions) |
| DB2 | replica (small) | normal read traffic |
| DB3 | replica (large) | analytics |
| DB4 | replica (large) | reporting |

---

## Without Custom Endpoint:

- Reader endpoint → DB2 + DB3 + DB4 (all mixed traffic)
- Analytics may overload small instances

---

## With Custom Endpoint:

You create:

### 🔵 analytics-endpoint

→ DB3 + DB4 only

### 🔵 reporting-endpoint

→ DB4 only

### 🔵 app-read-endpoint

→ DB2 only

Now:

- Analytics queries go only to powerful instances
- Normal app traffic stays isolated
- Reporting is separated

---

# 🔁 How It Works Internally

1. You create a custom endpoint and select instances
2. Aurora stores the mapping:
    - Static list OR
    - Dynamic rules (include/exclude instances)
3. When client connects:
    - DNS resolves to one of the selected instances
    - Aurora load balances across ONLY those instances

👉 It still uses **DNS-based routing**, not a physical load balancer.

---

# 🔄 Important Behavior

## 1. Reader endpoint becomes less important

Once custom endpoints are used:

- You usually stop using reader endpoint for workload routing
- Because custom endpoints give more control

---

## 2. Failover handling

- If an instance in the custom endpoint fails
- Aurora automatically routes to another instance in that custom group (if available)

---

## 3. New instances behavior

You can choose:

- ❌ Do NOT include new instances automatically (static group)
- ✅ Include new instances automatically (dynamic group)

---

# 🆚 Custom vs Reader Endpoint (Exam Favorite)

| Feature | Reader Endpoint | Custom Endpoint |
| --- | --- | --- |
| Control | No control | Full control |
| Instance selection | All replicas | Selected replicas |
| Use case | General read scaling | Workload isolation |
| Flexibility | Low | High |
| Routing | Auto load balance | Group-based load balance |

---

# 🧪 Key Exam Point

👉 Custom endpoints are used for:

- Workload isolation
- Performance tuning
- Running analytics/reporting separately
- Routing to specific instance types

---

# 💡 One-line Memory Trick

> Reader endpoint = “use all replicas”
> 
> 
> Custom endpoint = “choose your replicas”
> 

---

# 🧠 When NOT to use Custom Endpoint

Don’t use it when:

- You just need simple read scaling
- You don’t care which replica runs queries

---

# 🏁 Final Summary (Exam-ready)

Aurora Custom Endpoint allows you to:

- Define a subset of DB instances in a cluster
- Route traffic only to those selected instances
- Separate workloads like analytics, reporting, and app reads
- Improve performance and resource isolation
- Replace need for multiple application-level routing logic
