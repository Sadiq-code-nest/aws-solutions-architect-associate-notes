# Aurora Parallel Query

## Aurora Parallel Query — SAA-C03

**Definition:**

**Aurora Parallel Query** is a feature of **Amazon Aurora** that lets Aurora **push the heavy data-processing part of a SQL query down to the underlying storage layer**, so multiple storage nodes can process the data **in parallel**.

👉 **Main goal:** Speed up **large, analytical queries** without requiring you to scale up the database instance.

### Simple idea

Without Parallel Query:

**Application → Aurora DB instance → DB processes lots of data → Storage**

With Parallel Query:

**Application → Aurora DB instance → Storage nodes process data in parallel → Results returned**

So the **DB instance does less CPU-intensive work**.

---

### When to use it

Use Aurora Parallel Query when:

- You have **large tables**
- Queries perform **full/large table scans**
- You run **analytical/reporting queries** on Aurora
- Queries involve large amounts of data filtering/aggregation
- You want to improve query performance **without increasing DB instance size**

### Example Scenario

> A company stores **10 TB of customer transaction data** in Aurora. Every night, it runs analytical queries such as:
> 
> 
> `SELECT customer_id, SUM(amount) FROM transactions WHERE transaction_date > ... GROUP BY customer_id;`
> 
> The queries scan millions/billions of rows and consume significant database CPU.
> 

✅ **Aurora Parallel Query** can push the scan/filtering work to Aurora's distributed storage layer, allowing the work to happen in parallel.

---

### SAA-C03 Exam Shortcut

| If question says... | Think |
| --- | --- |
| Large Aurora tables | Parallel Query |
| Heavy analytical queries | Parallel Query |
| Large table scans | Parallel Query |
| Reduce DB instance CPU during analytical queries | Parallel Query |
| Push query processing to storage | **Aurora Parallel Query** |
| Need read scalability for SELECT queries | **Aurora Read Replicas** |
| Need caching of frequently accessed data | **ElastiCache** |

### ⚠️ Important Trap

**Aurora Parallel Query ≠ Read Replica**

- **Parallel Query** → makes **individual heavy queries** faster by parallelizing processing.
- **Read Replica** → handles **more read traffic / concurrent queries** by distributing reads across replicas.

**Memory trick:**

> **Parallel Query = one big query → split the work.**
> 
> 
> **Read Replica = many read requests → split the traffic.**
>
