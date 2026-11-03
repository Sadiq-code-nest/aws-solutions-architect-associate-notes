# 4. What is cardinality?

This is essential for this question.

### Cardinality = number of distinct values.

---

## High cardinality

Many different values.

Example:

```
UserID:

U001
U002
U003
U004
U005
...
U999999
```

That's **high cardinality**.

There are lots of different partition-key values.

---

## Low cardinality

Only a few different values.

Example:

```
Country:

Bangladesh
India
USA
UK
Canada
```

Only a small number of possible values.

That's **low cardinality**.

---

# 5. Why high cardinality is good

Imagine the partition key is:

```
Country
```

There are millions of users but only:

```
Bangladesh
India
USA
UK
Canada
...
```

Suppose 50% of your users are from Bangladesh.

Then many requests could concentrate around:

```
Partition Key = Bangladesh
```

You can get a hot partition.

Conceptually:

```
Country = Bangladesh
        ↓
Huge number of items/requests
        ↓
One partition-key value gets heavy traffic
        ↓
🔥 Hot partition
```

---

# 6. High-cardinality solution

Instead use something like:

```
UserID
```

Example:

```
U001
U002
U003
U004
U005
...
```

Now requests can be spread across many partition-key values.

Conceptually:

```
User 001 → Partition A
User 002 → Partition B
User 003 → Partition C
User 004 → Partition D
User 005 → Partition A
...
```

This gives DynamoDB more opportunities to distribute the workload.

---

# 7. What is a hot partition?

A **hot partition** occurs when a disproportionate amount of traffic is directed to a particular partition.

Imagine:

```
Partition 1 → ███████████████████ 🔥
Partition 2 → █
Partition 3 → █
Partition 4 → ██
```

Partition 1 is receiving much more traffic.

That's inefficient.

You want:

```
Partition 1 → █████
Partition 2 → █████
Partition 3 → █████
Partition 4 → █████
```

---

# 8. Why hot partitions matter

DynamoDB is designed to distribute workload across partitions.

If your partition-key design causes traffic to concentrate on a small number of partition-key values, you can get:

- Uneven capacity utilization
- Hot partitions
- Throttling
- Reduced performance
- Inefficient use of provisioned throughput

That's exactly the problem described in the question.

# 9. What is WCU?

The question mentions:

> **Write Capacity Units (WCU)**
> 

You should know this for SAA.

### WCU = Write Capacity Unit

For provisioned-capacity DynamoDB:

One WCU generally provides capacity for:

> **one write per second for an item up to 1 KB.**
> 

For larger items, more WCUs are required.

Example conceptually:

```
1 KB write/sec → 1 WCU
2 KB write/sec → 2 WCU
```

So if traffic is unevenly distributed, some partitions may consume their available write capacity much faster than others.

- **Question**
    
    Question
    A Docker application, which is running on an Amazon ECS cluster behind a load balancer, is heavily using Amazon DynamoDB. The application requires improved database performance by distributing the workload evenly and utilizing the provisioned throughput efficiently. Currently, the table’s write capacity units (WCU) are unevenly consumed due to key distribution.
    
    Which of the following should be implemented for the DynamoDB table?
    
    Reduce the number of partition keys in the DynamoDB table.
    Avoid using a primary key that combines both a partition key and a sort key.
    Use partition keys with high-cardinality attributes, which have a large number of distinct values for each item.
    Use partition keys with low-cardinality attributes, which have a small number of distinct values for each item.
    
- Ans
    - Part: 1
        
        # Question 37 — DynamoDB Partition Key Design & Hot Partitions
        
        ### ✅ Correct answer: **3**
        
        > **Use partition keys with high-cardinality attributes, which have a large number of distinct values for each item.**
        > 
        
        This question is fundamentally about **DynamoDB partition-key design**, specifically:
        
        - How DynamoDB distributes data
        - How DynamoDB distributes WCU/RCU
        - **Hot partitions**
        - **High-cardinality partition keys**
        - Avoiding uneven workload distribution
        
        The ECS + Docker + Load Balancer information is mostly **background noise**. The real topic is DynamoDB.
        
        ---
        
        # 1. Understand the scenario first
        
        The application looks like:
        
        ```
        Users
          ↓
        Load Balancer
          ↓
        ECS
          ↓
        Docker application
          ↓
        DynamoDB
        ```
        
        The application is heavily using DynamoDB.
        
        The problem is:
        
        > **WCU are unevenly consumed due to key distribution.**
        > 
        
        In simple words:
        
        ### Some DynamoDB partitions are getting a LOT of traffic.
        
        while:
        
        ### Other partitions are barely being used.
        
        Conceptually:
        
        ```
        DynamoDB
        │
        ├── Partition A → ████████████████████  VERY BUSY 🔥
        ├── Partition B → ██
        ├── Partition C → █
        └── Partition D → ███
        ```
        
        This is an **uneven workload distribution** problem.
        
        The goal is:
        
        ```
        Before:
        A → ████████████████████
        B → ██
        C → █
        D → ███
        
        After:
        A → █████
        B → █████
        C → █████
        D → █████
        ```
        
        We want DynamoDB to distribute requests more evenly.
        
        ---
        
        # 2. The key phrase: "key distribution"
        
        The question says:
        
        > **"WCU are unevenly consumed due to key distribution."**
        > 
        
        This is your biggest clue.
        
        Think:
        
        > **DynamoDB partition key determines where an item is stored.**
        > 
        
        Therefore:
        
        > **Bad partition-key distribution → uneven traffic → hot partitions.**
        > 
        
        The solution is to choose a partition key with **high cardinality** and good distribution.
        
        ---
        
        # 3. What is a DynamoDB partition key?
        
        A DynamoDB table's primary key can be:
        
        ### Option A — Simple primary key
        
        Only a partition key:
        
        ```
        Partition Key
        ```
        
        Example:
        
        ```
        UserID
        ```
        
        ---
        
        ### Option B — Composite primary key
        
        Partition key + sort key:
        
        ```
        Partition Key + Sort Key
        ```
        
        Example:
        
        ```
        UserID + OrderDate
        ```
        
        The important thing:
        
        > **The partition key is what DynamoDB uses to distribute items across partitions.**
        > 
        
        The sort key is mainly used to organize/query related items within the same partition-key value.
        
        ---
        
        # 4. What is cardinality?
        
        This is essential for this question.
        
        ### Cardinality = number of distinct values.
        
        ---
        
        ## High cardinality
        
        Many different values.
        
        Example:
        
        ```
        UserID:
        
        U001
        U002
        U003
        U004
        U005
        ...
        U999999
        ```
        
        That's **high cardinality**.
        
        There are lots of different partition-key values.
        
        ---
        
        ## Low cardinality
        
        Only a few different values.
        
        Example:
        
        ```
        Country:
        
        Bangladesh
        India
        USA
        UK
        Canada
        ```
        
        Only a small number of possible values.
        
        That's **low cardinality**.
        
        ---
        
        # 5. Why high cardinality is good
        
        Imagine the partition key is:
        
        ```
        Country
        ```
        
        There are millions of users but only:
        
        ```
        Bangladesh
        India
        USA
        UK
        Canada
        ...
        ```
        
        Suppose 50% of your users are from Bangladesh.
        
        Then many requests could concentrate around:
        
        ```
        Partition Key = Bangladesh
        ```
        
        You can get a hot partition.
        
        Conceptually:
        
        ```
        Country = Bangladesh
                ↓
        Huge number of items/requests
                ↓
        One partition-key value gets heavy traffic
                ↓
        🔥 Hot partition
        ```
        
        ---
        
        # 6. High-cardinality solution
        
        Instead use something like:
        
        ```
        UserID
        ```
        
        Example:
        
        ```
        U001
        U002
        U003
        U004
        U005
        ...
        ```
        
        Now requests can be spread across many partition-key values.
        
        Conceptually:
        
        ```
        User 001 → Partition A
        User 002 → Partition B
        User 003 → Partition C
        User 004 → Partition D
        User 005 → Partition A
        ...
        ```
        
        This gives DynamoDB more opportunities to distribute the workload.
        
        ---
        
        # 7. What is a hot partition?
        
        A **hot partition** occurs when a disproportionate amount of traffic is directed to a particular partition.
        
        Imagine:
        
        ```
        Partition 1 → ███████████████████ 🔥
        Partition 2 → █
        Partition 3 → █
        Partition 4 → ██
        ```
        
        Partition 1 is receiving much more traffic.
        
        That's inefficient.
        
        You want:
        
        ```
        Partition 1 → █████
        Partition 2 → █████
        Partition 3 → █████
        Partition 4 → █████
        ```
        
        ---
        
        # 8. Why hot partitions matter
        
        DynamoDB is designed to distribute workload across partitions.
        
        If your partition-key design causes traffic to concentrate on a small number of partition-key values, you can get:
        
        - Uneven capacity utilization
        - Hot partitions
        - Throttling
        - Reduced performance
        - Inefficient use of provisioned throughput
        
        That's exactly the problem described in the question.
        
        ---
        
        # 9. Option 1 — Reduce the number of partition keys ❌
        
        > **Reduce the number of partition keys in the DynamoDB table.**
        > 
        
        Wrong.
        
        This would generally make the distribution problem **worse**.
        
        Suppose you have:
        
        ```
        100,000 distinct partition-key values
        ```
        
        You reduce that to:
        
        ```
        10 partition-key values
        ```
        
        Now more traffic is concentrated among fewer values.
        
        That increases the possibility of hot partitions.
        
        ### Exam logic:
        
        ```
        Fewer distinct values
               ↓
        Less distribution
               ↓
        More concentration
               ↓
        🔥 Hot partitions
        ```
        
        Therefore:
        
        ❌ Wrong.
        
        ---
        
        # 10. Option 2 — Avoid a composite primary key ❌
        
        > **Avoid using a primary key that combines both a partition key and a sort key.**
        > 
        
        Wrong.
        
        This is a distractor.
        
        A composite primary key:
        
        ```
        Partition Key
        +
        Sort Key
        ```
        
        is perfectly valid in DynamoDB.
        
        For example:
        
        ```
        UserID       = U123
        OrderDate    = 2026-08-16
        ```
        
        The composite primary key is not inherently the cause of uneven WCU distribution.
        
        The critical factor here is:
        
        > **How well the partition key distributes workload.**
        > 
        
        You don't solve hot partitions simply by removing the sort key.
        
        ---
        
        # 11. Very important: Partition key vs Sort key
        
        Don't confuse these.
        
        Suppose:
        
        ```
        PK = UserID
        SK = OrderDate
        ```
        
        You might have:
        
        ```
        UserID    OrderDate
        ------    ----------
        U001      2026-01-01
        U001      2026-02-01
        U001      2026-03-01
        
        U002      2026-01-01
        U002      2026-02-01
        ```
        
        DynamoDB groups items with the same partition-key value.
        
        So:
        
        ```
        UserID = U001
        ```
        
        identifies a logical partition-key value.
        
        The sort key helps organize/query multiple items associated with that partition key.
        
        ### Therefore:
        
        > **Sort key does not replace the need for a good partition key.**
        > 
        
        ---
        
        # 12. Option 3 — High-cardinality partition key ✅
        
        > **Use partition keys with high-cardinality attributes, which have a large number of distinct values for each item.**
        > 
        
        Correct.
        
        This is exactly what the question wants.
        
        ### Why?
        
        High cardinality means:
        
        ```
        Many distinct values
                ↓
        Better distribution of items
                ↓
        Better distribution of requests
                ↓
        Better utilization of throughput
                ↓
        Reduced hot-partition risk
        ```
        
        ---
        
        # 13. Option 4 — Low-cardinality partition key ❌
        
        > **Use partition keys with low-cardinality attributes, which have a small number of distinct values for each item.**
        > 
        
        Wrong.
        
        Low cardinality means fewer distinct partition-key values.
        
        Example:
        
        ```
        Gender:
        Male
        Female
        Other
        ```
        
        or:
        
        ```
        Status:
        Pending
        Completed
        Cancelled
        ```
        
        If you use such attributes as partition keys for a large, high-traffic table, requests may concentrate heavily on a few values.
        
        For example:
        
        ```
        Status = Completed
                ↓
        Millions of requests/items
                ↓
        Hot partition risk
        ```
        
        Therefore:
        
        ❌ Wrong.
        
        ---
        
        # 14. The four options visually
        
        | Option | Cardinality | Effect | Answer |
        | --- | --- | --- | --- |
        | Reduce partition keys | Lower | More concentration | ❌ |
        | Avoid PK + SK | Unrelated | Doesn't solve distribution | ❌ |
        | **High-cardinality PK** | **High** | **Better distribution** | ✅ |
        | Low-cardinality PK | Low | Concentration/hot partitions | ❌ |
        
        ---
        
        # 
        
    - Part: 2
        
        # 15. Easy real-world example
        
        Imagine a restaurant has 100 cashiers.
        
        ### Bad design
        
        Customers are assigned to only 3 cashiers based on:
        
        ```
        City:
        Dhaka
        Chittagong
        Sylhet
        ```
        
        Most customers are from Dhaka.
        
        Result:
        
        ```
        Cashier 1 → ████████████████████ 🔥
        Cashier 2 → ██
        Cashier 3 → █
        ```
        
        Terrible distribution.
        
        ---
        
        ### Better design
        
        Assign customers using unique customer IDs:
        
        ```
        Customer 10001
        Customer 10002
        Customer 10003
        Customer 10004
        ...
        ```
        
        Now workload can be distributed much more evenly.
        
        That's the idea behind:
        
        > **High-cardinality partition keys.**
        > 
        
        ---
        
        # 16. Another DynamoDB example
        
        Suppose you're building an online store.
        
        ### Bad partition key
        
        ```
        Partition Key = ProductCategory
        ```
        
        Possible values:
        
        ```
        Electronics
        Clothing
        Books
        Furniture
        ```
        
        Only a few values.
        
        If Electronics gets most traffic:
        
        ```
        Electronics → 🔥🔥🔥🔥🔥
        Clothing    → 🔥
        Books       → 🔥
        Furniture   → 🔥
        ```
        
        Not ideal.
        
        ---
        
        ### Better partition key
        
        Use:
        
        ```
        ProductID
        ```
        
        Example:
        
        ```
        P001
        P002
        P003
        P004
        P005
        ...
        ```
        
        Many distinct values.
        
        Much better potential distribution.
        
        ---
        
        # 17. But there is an important nuance
        
        Don't memorize:
        
        > "Always choose the highest-cardinality attribute."
        > 
        
        That's too simplistic.
        
        The real goal is:
        
        > **Choose a partition key that distributes both data and workload effectively.**
        > 
        
        You want:
        
        ### High cardinality
        
        **AND**
        
        ### Even access patterns.
        
        For example, suppose:
        
        ```
        UserID
        ```
        
        has 10 million unique users.
        
        That's high cardinality.
        
        But if:
        
        ```
        UserID = VIP_USER
        ```
        
        receives 90% of all requests, you could still have a hot key.
        
        So the best partition key considers:
        
        - Cardinality
        - Access frequency
        - Data distribution
        - Query patterns
        
        ---
        
        # 18. DynamoDB partition key design — deeper understanding
        
        DynamoDB uses the partition-key value to determine the physical partition where an item is stored.
        
        Conceptually:
        
        ```
        Partition Key
              ↓
        Hash function
              ↓
        Partition placement
        ```
        
        You don't manually choose:
        
        > "Put this item into Partition 7."
        > 
        
        DynamoDB handles the physical partitioning.
        
        Your job is to choose a good logical partition key.
        
        ---
        
        # 19. What does "hash" mean here?
        
        Conceptually:
        
        ```
        UserID = U123
             ↓
        Hash function
             ↓
        Hash value
             ↓
        DynamoDB partition
        ```
        
        Another:
        
        ```
        UserID = U456
             ↓
        Hash function
             ↓
        Different hash value
             ↓
        Potentially different partition
        ```
        
        This helps DynamoDB distribute data.
        
        That's why high-cardinality partition keys are useful.
        
        ---
        
        # 20. What is WCU?
        
        The question mentions:
        
        > **Write Capacity Units (WCU)**
        > 
        
        You should know this for SAA.
        
        ### WCU = Write Capacity Unit
        
        For provisioned-capacity DynamoDB:
        
        One WCU generally provides capacity for:
        
        > **one write per second for an item up to 1 KB.**
        > 
        
        For larger items, more WCUs are required.
        
        Example conceptually:
        
        ```
        1 KB write/sec → 1 WCU
        2 KB write/sec → 2 WCU
        ```
        
        So if traffic is unevenly distributed, some partitions may consume their available write capacity much faster than others.
        
        ---
        
        # 21. RCU vs WCU
        
        Remember:
        
        | Capacity | Used for |
        | --- | --- |
        | **WCU** | Writes |
        | **RCU** | Reads |
        
        The question specifically talks about:
        
        > **WCU unevenly consumed**
        > 
        
        Therefore, focus on **write distribution**.
        
        ---
        
        # 22. Provisioned throughput
        
        DynamoDB can operate using:
        
        ### Provisioned capacity
        
        You specify:
        
        ```
        RCU = 100
        WCU = 200
        ```
        
        DynamoDB provides that configured capacity, subject to its scaling/configuration behavior.
        
        ---
        
        ### On-demand capacity
        
        DynamoDB automatically manages capacity based on workload.
        
        This is often useful for:
        
        - Unpredictable traffic
        - Variable workloads
        - Applications where you don't want to manage capacity manually
        
        But:
        
        > **On-demand capacity does not mean a bad partition key suddenly becomes a good partition key.**
        > 
        
        Good key design remains important.
        
        ---
        
        # 23. Very important: On-demand doesn't eliminate hot keys
        
        Suppose:
        
        ```
        Partition Key = "Dhaka"
        ```
        
        and 95% of requests use:
        
        ```
        Dhaka
        ```
        
        Switching to on-demand doesn't magically make the access pattern perfectly distributed.
        
        You still need good DynamoDB data modeling.
        
        ---
        
        # 24. What does "provisioned throughput efficiently" mean?
        
        Imagine you have:
        
        ```
        Total WCU = 400
        ```
        
        Ideally, workload is spread across the available partitions.
        
        But instead:
        
        ```
        Partition A → 350 WCU 🔥
        Partition B → 20 WCU
        Partition C → 15 WCU
        Partition D → 15 WCU
        ```
        
        Although the table has 400 WCU overall, one area is being hammered.
        
        The goal is to design the key so workload is distributed more effectively.
        
        ---
        
        # 25. What is a hot key?
        
        A **hot key** is a partition-key value that receives disproportionately high traffic.
        
        Example:
        
        ```
        PK = ProductID
        
        Product A → 80% of requests 🔥
        Product B → 5%
        Product C → 5%
        Product D → 5%
        Product E → 5%
        ```
        
        Product A becomes a hot key.
        
        This can cause a hot partition or concentrated workload.
        
        ---
        
        # 26. High cardinality vs high traffic
        
        These are different concepts.
        
        ### Cardinality
        
        How many distinct values exist.
        
        ```
        UserID
        → 10 million values
        ```
        
        High cardinality.
        
        ### Frequency
        
        How often a particular value is accessed.
        
        ```
        UserID = U001
        → 1 million requests
        ```
        
        High frequency.
        
        You ideally want:
        
        > **Many distinct values + reasonably distributed access.**
        > 
        
        ---
        
        # 27. What about ECS and Load Balancer?
        
        The question starts with:
        
        > "A Docker application, which is running on an Amazon ECS cluster behind a load balancer..."
        > 
        
        Don't get distracted.
        
        The architecture is:
        
        ```
        Load Balancer
              ↓
        ECS
              ↓
        DynamoDB
        ```
        
        The ECS/Load Balancer portion tells you:
        
        > This is a scalable application.
        > 
        
        But the actual question is:
        
        > **How should the DynamoDB table be designed to distribute workload?**
        > 
        
        None of the options involve:
        
        - ECS
        - Docker
        - Load Balancer
        
        So the answer must be about DynamoDB key design.
        
        ---
        
        # 28. Why a Load Balancer doesn't solve this problem
        
        You might think:
        
        > "Traffic is uneven, so use a Load Balancer."
        > 
        
        But the Load Balancer distributes requests across application servers/tasks.
        
        It doesn't fix DynamoDB's partition-key distribution.
        
        You could have:
        
        ```
        Load Balancer
           ↓
        ECS Tasks
           ↓
        DynamoDB
        ```
        
        with perfectly balanced ECS traffic while DynamoDB still has:
        
        ```
        DynamoDB
        Partition A 🔥🔥🔥🔥
        Partition B
        Partition C
        Partition D
        ```
        
        Different layer.
        
        ---
        
        # 29. SAA layer-by-layer thinking
        
        This is a very useful exam technique.
        
        ```
                     User Traffic
                          ↓
                  Load Balancer
                          ↓
                      ECS
                          ↓
                     DynamoDB
        ```
        
        If the question says:
        
        > ECS tasks are overloaded
        > 
        
        Think:
        
        **ECS scaling**
        
        If it says:
        
        > Load is not distributed across instances
        > 
        
        Think:
        
        **Load Balancer**
        
        If it says:
        
        > DynamoDB WCU/RCU unevenly consumed
        > 
        
        Think:
        
        ### **DynamoDB partition-key design**
        
        This prevents you from choosing the wrong service.
        
        ---
        
        # 30. The concept of "scatter" vs "concentrate"
        
        Think of DynamoDB partition keys as distributing balls into buckets.
        
        ### Low cardinality
        
        ```
        1000 balls
        
        Bucket A → 700
        Bucket B → 200
        Bucket C → 100
        ```
        
        Concentrated.
        
        ### High cardinality
        
        ```
        1000 balls
        
        Bucket A → ~250
        Bucket B → ~250
        Bucket C → ~250
        Bucket D → ~250
        ```
        
        Better distribution.
        
        That's the core idea.
        
        ---
        
        # 31. What if the partition key is sequential?
        
        This is another useful DynamoDB concept.
        
        Suppose you use:
        
        ```
        Timestamp
        ```
        
        and writes continuously arrive at the newest timestamp.
        
        You could create a concentrated access pattern depending on how the key is designed and accessed.
        
        DynamoDB partition-key design should consider **write/read distribution**, not merely uniqueness.
        
        ---
        
        # 32. What is a composite primary key?
        
        Because option 2 mentions it, make sure you understand it.
        
        ### Simple primary key
        
        ```
        PK = UserID
        ```
        
        Example:
        
        ```
        UserID = U001
        ```
        
        Unique partition-key value identifies the item.
        
        ---
        
        ### Composite primary key
        
        ```
        PK = UserID
        SK = OrderID
        ```
        
        Example:
        
        ```
        U001 | O100
        U001 | O101
        U001 | O102
        
        U002 | O200
        U002 | O201
        ```
        
        This lets multiple related items share the same partition key.
        
        It's extremely useful for one-to-many data models.
        
        ---
        
        # 33. Does composite key cause hot partitions?
        
        ### Not inherently.
        
        The critical point is:
        
        > **The partition-key value determines the grouping/distribution.**
        > 
        
        If you use:
        
        ```
        PK = UserID
        SK = OrderID
        ```
        
        and users are reasonably distributed, this can work very well.
        
        But if you use:
        
        ```
        PK = Country
        SK = OrderID
        ```
        
        and one country receives most traffic, you may have a problematic access pattern.
        
        So don't think:
        
        > Composite key = bad.
        > 
        
        That's false.
        
        ---
        
        # 34. What would "good" DynamoDB key design look like?
        
        For an online store:
        
        ```
        PK = CustomerID
        SK = OrderID
        ```
        
        Example:
        
        ```
        C001 | O001
        C001 | O002
        C001 | O003
        
        C002 | O004
        C002 | O005
        ```
        
        This gives you:
        
        - High-cardinality customer IDs
        - Efficient customer-specific queries
        - Related orders grouped together
        - Good potential distribution across customers
        
        ---
        
        # 35. What would "bad" design look like?
        
        ```
        PK = OrderStatus
        SK = OrderID
        ```
        
        Values:
        
        ```
        Pending
        Completed
        Cancelled
        ```
        
        Now millions of orders might be concentrated into only a few partition-key values.
        
        Especially if most orders are:
        
        ```
        Completed
        ```
        
        You can create an uneven workload.
        
        ---
        
        # 36. Exam keywords from this question
        
        When you see:
        
        ### "unevenly distributed workload"
        
        → Think **partition key**
        
        ### "hot partition"
        
        → Think **better partition-key distribution**
        
        ### "hot key"
        
        → Think **frequently accessed partition-key value**
        
        ### "high cardinality"
        
        → Think **many distinct partition-key values**
        
        ### "low cardinality"
        
        → Think **few distinct partition-key values**
        
        ### "WCU unevenly consumed"
        
        → Think **write distribution / hot partitions**
        
        ### "RCU unevenly consumed"
        
        → Think **read distribution / hot partitions**
        
        ---
        
        # 37. Final decision tree
        
        ```
        DynamoDB performance problem
                  │
                  ▼
        Uneven read/write workload?
                  │
                 YES
                  │
                  ▼
        Check partition key
                  │
                  ▼
        Does it have good cardinality?
               /             \
             YES              NO
              │                │
              │                ▼
              │          Consider better
              │          partition key
              │
              ▼
        Check access pattern
              │
              ▼
        Avoid hot keys
        ```
        
        ---
        
        # 38. Final option-by-option post-mortem
        
        ### ❌ Option 1
        
        **Reduce number of partition keys**
        
        This reduces distribution and can increase concentration.
        
        > Fewer distinct values → greater hot-partition risk.
        > 
        
        ---
        
        ### ❌ Option 2
        
        **Avoid partition key + sort key**
        
        Composite keys are completely valid and useful.
        
        > Sort key isn't the problem.
        > 
        
        ---
        
        ### ✅ Option 3
        
        **Use high-cardinality partition keys**
        
        Many distinct values allow DynamoDB to distribute data/workload more effectively.
        
        > **Correct.**
        > 
        
        ---
        
        ### ❌ Option 4
        
        **Use low-cardinality partition keys**
        
        Few values can concentrate traffic.
        
        > Low cardinality → higher hot-partition risk.
        > 
        
        ---
        
        # 🧠 Final SAA-C03 memory
        
        ### **DynamoDB partition-key rule**
        
        > **Choose a partition key with high cardinality and an evenly distributed access pattern.**
        > 
        
        Think:
        
        ```
        High cardinality
              +
        Even access
              ↓
        Good distribution
              ↓
        Better throughput utilization
              ↓
        Fewer hot partitions
        ```
        
        And remember:
        
        > **The ECS, Docker, and Load Balancer details are distractions here. The question is about DynamoDB partition-key design.**
        > 
        
        ### 🎯 Correct answer: **Option 3 — Use a high-cardinality partition key.**
