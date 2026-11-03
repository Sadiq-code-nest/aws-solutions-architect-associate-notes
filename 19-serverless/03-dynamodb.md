# Amazon DynamoDB (AWS SAA-C03) – Complete Beginner Notes

Think of DynamoDB as:

> **A super-fast, serverless NoSQL database built to handle millions of users automatically.**
> 

If MySQL/PostgreSQL is like **Microsoft Excel**, then DynamoDB is like a **huge collection of JSON objects**.

---

# What is DynamoDB?

Amazon DynamoDB is a

- Fully Managed Database
- NoSQL Database
- Serverless
- Highly Available
- Auto Scaling
- Extremely Fast

AWS manages everything.

You never install software.

You never patch servers.

You never manage storage.

---

# Real-Life Example

Suppose Facebook has

```
2 Billion Users
```

Each second

```
Millions of Likes

Millions of Comments

Millions of Messages
```

A normal database struggles.

DynamoDB was built for this kind of scale.

---

# Why DynamoDB?

Traditional database

```
Traffic ↑

↓

Need Bigger Server

↓

Downtime

↓

More Maintenance
```

DynamoDB

```
Traffic ↑

↓

AWS Automatically Scales

↓

No Downtime
```

---

# Main Characteristics

✅ Fully Managed

AWS manages everything.

---

✅ Highly Available

Data is automatically copied across multiple Availability Zones.

If one AZ fails,

your database still works.

---

✅ NoSQL Database

Unlike MySQL,

there are

- No JOINs
- No foreign keys
- No fixed schema

---

✅ Very Fast

Usually

```
Single-digit milliseconds
```

Meaning

around

```
1–9 ms
```

Very fast.

---

# SQL vs NoSQL

## SQL

Imagine an Excel sheet.

```
Student Table

ID

Name

Department
```

Every row must follow the same columns.

---

## NoSQL

Every item can look different.

```
User 1

Name

Age

Email
```

---

```
User 2

Name

Phone

Country

Address
```

---

```
User 3

Name

Skills

Projects

Experience
```

No problem.

---

# DynamoDB Structure

Very simple.

```
Table

↓

Items (Rows)

↓

Attributes (Columns)
```

---

Example

```
Students Table

Item 1

ID

101

Name

John

Age

20
```

---

Item 2

```
ID

102

Name

Alice

Department

ICT

CGPA

3.85
```

Notice

Different attributes.

Perfectly fine.

---

# Primary Key

Every table must have

one Primary Key.

There are two types.

---

## 1. Partition Key

Only one key.

Example

```
StudentID
```

```
1001

1002

1003
```

Unique.

---

## 2. Partition + Sort Key

Two keys.

Example

```
UserID

OrderDate
```

```
User1

2026-01-01
```

```
User1

2026-02-01
```

```
User1

2026-03-01
```

Now one user can have many orders.

---

# Easy Analogy

Student Roll Number

```
Roll = Partition Key
```

Exam Date

```
Exam Date = Sort Key
```

---

# Item Size

Maximum size

```
400 KB
```

One item cannot exceed this.

Exam favorite.

---

# **Secondary Index**

- 1
    
    Absolutely. Let's make **DynamoDB Secondary Indexes** very simple.
    
    # 🧠 First: Why do we need a Secondary Index?
    
    Imagine your DynamoDB table:
    
    | UserID | Name | Age | City |
    | --- | --- | --- | --- |
    | U01 | Rahim | 25 | Dhaka |
    | U02 | Karim | 30 | Chittagong |
    | U03 | Sadiq | 28 | Dhaka |
    
    Suppose your table's primary key is:
    
    ```
    UserID
    ```
    
    So DynamoDB is very good at:
    
    > "Give me user U01."
    > 
    
    ```
    UserID = U01
    ```
    
    But what if you ask:
    
    > "Give me all users from Dhaka."
    > 
    
    Your query is based on **City**, not `UserID`.
    
    That's where a **Secondary Index** comes in.
    
    ---
    
    # 🔑 Think of an index as another way to search
    
    Your main table has:
    
    ```
    Primary way of finding data
            ↓
          UserID
    ```
    
    A secondary index gives you another way:
    
    ```
    Alternative way of finding data
            ↓
           City
    ```
    
    So:
    
    ```
    DynamoDB Table
           │
           ├── Primary Key → UserID
           │
           └── Secondary Index → City
    ```
    
    That's basically it.
    
    ---
    
    # There are TWO types
    
    ## 1️⃣ Global Secondary Index — GSI
    
    Think:
    
    > **"I want a completely different partition key."**
    > 
    
    Example:
    
    Main table:
    
    ```
    PK = UserID
    ```
    
    GSI:
    
    ```
    PK = City
    ```
    
    So:
    
    ```
    Main Table
    UserID → U01, U02, U03
    
    GSI
    City → Dhaka, Chittagong
    ```
    
    The GSI's partition key **can be different** from the table's partition key.
    
    It can also have a different sort key.
    
    ### Example
    
    Main table:
    
    ```
    PK = UserID
    SK = OrderID
    ```
    
    GSI:
    
    ```
    PK = ProductID
    SK = OrderDate
    ```
    
    Completely different access pattern.
    
    ### 🧠 Remember:
    
    > **GSI = Different partition key allowed**
    > 
    
    ---
    
    # 2️⃣ Local Secondary Index — LSI
    
    LSI is different.
    
    It says:
    
    > **"Keep the SAME partition key, but give me another sort key."**
    > 
    
    Example main table:
    
    ```
    PK = UserID
    SK = OrderDate
    ```
    
    LSI:
    
    ```
    PK = UserID       ← SAME
    SK = ProductName  ← DIFFERENT
    ```
    
    So:
    
    ```
    Main Table
    UserID + OrderDate
    
    LSI
    UserID + ProductName
    ```
    
    ### 🧠 Remember:
    
    > **LSI = Same partition key, different sort key**
    > 
    
    ---
    
    # ⭐ GSI vs LSI — easiest way
    
    |  | GSI | LSI |
    | --- | --- | --- |
    | Partition Key | Can be **different** | Must be **same** |
    | Sort Key | Can be different/optional | Must be different |
    | Created | Can be created after table creation | **Must be created when table is created** |
    | Number per table | Up to **20** | Up to **5** |
    | Capacity | Can have separate capacity | Shares base table capacity |
    | Simple memory | **Different PK** | **Same PK** |
    
    ### Exam shortcut:
    
    > **GSI → Different PK**
    > 
    > 
    > **LSI → Same PK**
    > 
    
    This is the #1 thing to remember.
    
    ---
    
    # 🎵 Let's use the music example
    
    Suppose you have:
    
    | Artist | Song | Genre | Album |
    | --- | --- | --- | --- |
    | Adele | Hello | Pop | 25 |
    | Metallica | One | Metal | Justice |
    | Adele | Easy | Pop | 25 |
    
    Your table's primary key might be:
    
    ```
    Artist + Song
    ```
    
    So you can easily ask:
    
    > Give me Adele's song "Hello".
    > 
    
    But suppose you want:
    
    > Give me all songs in the **Pop** genre.
    > 
    
    Your primary key doesn't help.
    
    So create a GSI:
    
    ```
    GSI
    
    Partition Key = Genre
    Sort Key = Album
    ```
    
    Now you can query:
    
    ```
    Genre = Pop
    ```
    
    and get:
    
    ```
    Adele → Hello → 25
    Adele → Easy  → 25
    ```
    
    ---
    
    # 🔄 What does "DynamoDB automatically maintains the index" mean?
    
    This sounds complicated but it's simple.
    
    Suppose you add:
    
    ```
    Artist = Taylor Swift
    Genre = Pop
    Album = Midnights
    ```
    
    to the main table.
    
    DynamoDB automatically updates the GSI too.
    
    You don't manually insert into the index.
    
    ```
              Add item
                 ↓
           Main DynamoDB
                 ↓
          DynamoDB updates
                 ↓
           Secondary Index
    ```
    
    If you update/delete the item:
    
    ```
    Update item
        ↓
    Main table updated
        ↓
    Index automatically updated
    ```
    
    So:
    
    > **You manage the index definition; DynamoDB maintains the index data.**
    > 
    
    ---
    
    # 📦 What is "Projection"?
    
    This is another confusing word.
    
    Suppose your table has:
    
    ```
    UserID
    Name
    Age
    City
    Phone
    Email
    Address
    ```
    
    Your GSI might only need:
    
    ```
    City
    Name
    Age
    ```
    
    You can tell DynamoDB which non-key attributes should be copied into the index.
    
    That's called **projection**.
    
    Think:
    
    > **Projection = Which attributes should be copied into the index?**
    > 
    
    ---
    
    # Three projection options
    
    For SAA, know these:
    
    ### `KEYS_ONLY`
    
    Only key attributes.
    
    ```
    Index:
    City
    UserID
    ```
    
    Smallest index.
    
    ---
    
    ### `INCLUDE`
    
    Keys + specific attributes.
    
    ```
    Index:
    City
    UserID
    Name
    Age
    ```
    
    You choose extra attributes.
    
    ---
    
    ### `ALL`
    
    Copy all attributes.
    
    ```
    Index:
    Everything
    ```
    
    Largest index.
    
    ### Easy memory:
    
    ```
    KEYS_ONLY → Keys
    INCLUDE    → Keys + some
    ALL        → Everything
    ```
    
    ---
    
    # 🎯 One complete example
    
    Imagine an e-commerce table:
    
    ```
    Orders
    -------------------------
    CustomerID     ← PK
    OrderID        ← SK
    Product
    Category
    Price
    OrderDate
    ```
    
    Your normal query:
    
    ```
    CustomerID = C100
    ```
    
    Easy.
    
    But you also need:
    
    > "Find all orders for Category = Laptop."
    > 
    
    Create GSI:
    
    ```
    GSI:
    PK = Category
    SK = OrderDate
    ```
    
    Now:
    
    ```
    Category = Laptop
    ```
    
    can be queried efficiently.
    
    ---
    
    # 🚨 One very important SAA distinction
    
    Don't confuse **Secondary Index** with **Scan**.
    
    Without an appropriate index:
    
    ```
    "Find all Laptop orders"
              ↓
    Scan entire table ❌
    ```
    
    With a suitable GSI:
    
    ```
    "Find all Laptop orders"
              ↓
    GSI
              ↓
    Query efficiently ✅
    ```
    
    So a secondary index is essentially creating another **access path** to your DynamoDB data.
    
    ---
    
    # 🧠 Final mental picture
    
    ```
                     DynamoDB Table
                           │
                  Primary Key
                  UserID + OrderID
                           │
              ┌────────────┴────────────┐
              │                         │
             GSI                       LSI
              │                         │
       Different PK              SAME PK
       Different SK              Different SK
              │                         │
         New access path          New access path
    ```
    
    ## Memorize these 5 lines for SAA-C03
    
    > **Secondary Index = another way to query DynamoDB.**
    > 
    
    > **GSI = partition key can be different.**
    > 
    
    > **LSI = partition key MUST be the same.**
    > 
    
    > **GSI can be created after the table; LSI must be created when the table is created.**
    > 
    
    > **DynamoDB automatically maintains indexes when table items change.**
    > 
    
    If you remember just **GSI = Different PK** and **LSI = Same PK**, you'll already solve a large portion of DynamoDB index questions.
    
- 2
    
    Sure. Let's use **one simple e-commerce example** where the **same DynamoDB table has BOTH an LSI and a GSI**.
    
    # 🛒 Example: Customer Orders
    
    Imagine this DynamoDB table:
    
    ### Main Table
    
    | CustomerID **(PK)** | OrderID **(SK)** | Product | Category | Price | OrderDate |
    | --- | --- | --- | --- | --- | --- |
    | C01 | O01 | iPhone | Mobile | 1000 | Jan 10 |
    | C01 | O02 | MacBook | Laptop | 1500 | Jan 15 |
    | C01 | O03 | AirPods | Audio | 200 | Jan 20 |
    | C02 | O04 | iPhone | Mobile | 1000 | Jan 12 |
    
    The primary key is:
    
    ```
    PK = CustomerID
    SK = OrderID
    ```
    
    So the normal table is very good at:
    
    > "Show me all orders belonging to customer C01."
    > 
    
    ```
    CustomerID = C01
    ```
    
    ---
    
    # 🟢 Now we create an LSI
    
    Suppose we also want:
    
    > "Show me C01's orders sorted by **Price**."
    > 
    
    We create an **LSI**:
    
    ```
    LSI
    PK = CustomerID      ← SAME as main table
    SK = Price           ← DIFFERENT
    ```
    
    So:
    
    ```
    Main Table:
    CustomerID + OrderID
    
    LSI:
    CustomerID + Price
    ```
    
    Now we can query:
    
    ```
    CustomerID = C01
    ```
    
    and sort by:
    
    ```
    Price
    ```
    
    Result:
    
    | CustomerID | Price | Product |
    | --- | --- | --- |
    | C01 | 200 | AirPods |
    | C01 | 1000 | iPhone |
    | C01 | 1500 | MacBook |
    
    ### 🧠 Why is this an LSI?
    
    Because:
    
    > **Same partition key: CustomerID**
    > 
    
    ---
    
    # 🔵 Now create a GSI
    
    Suppose the business also wants:
    
    > "Find all orders by **Category**."
    > 
    
    But `Category` isn't our main partition key.
    
    So create a **GSI**:
    
    ```
    GSI
    PK = Category       ← DIFFERENT
    SK = OrderDate
    ```
    
    Now:
    
    ```
    Main Table:
    CustomerID + OrderID
    
    LSI:
    CustomerID + Price
    
    GSI:
    Category + OrderDate
    ```
    
    We can now query:
    
    ```
    Category = Mobile
    ```
    
    Result:
    
    | Category | OrderDate | CustomerID | Product |
    | --- | --- | --- | --- |
    | Mobile | Jan 10 | C01 | iPhone |
    | Mobile | Jan 12 | C02 | iPhone |
    
    ### 🧠 Why is this a GSI?
    
    Because:
    
    > **Partition key is different: Category**
    > 
    
    ---
    
    # 🎯 Now look at the whole thing
    
    ```
                             DynamoDB
                                │
                         Orders Table
                                │
                  PK = CustomerID
                  SK = OrderID
                                │
                ┌───────────────┴───────────────┐
                │                               │
               LSI                             GSI
                │                               │
         PK = CustomerID                 PK = Category
         SK = Price                      SK = OrderDate
                │                               │
          "Customer's                    "Find orders
           orders by price"               by category"
    ```
    
    ## Three ways to access the SAME data
    
    ### ① Main Table
    
    ```
    CustomerID = C01
    ```
    
    ➡️ Get C01's orders.
    
    ### ② LSI
    
    ```
    CustomerID = C01
    ```
    
    ➡️ Get C01's orders organized by **Price**.
    
    ### ③ GSI
    
    ```
    Category = Mobile
    ```
    
    ➡️ Get orders belonging to **Mobile category**, regardless of customer.
    
    ---
    
    # 🔥 The easiest way to remember
    
    Imagine the table has **three doors**:
    
    ```
                  ORDERS
                    │
          ┌─────────┼─────────┐
          ↓         ↓         ↓
       Main       LSI       GSI
     CustomerID CustomerID Category
     + OrderID  + Price    + OrderDate
    ```
    
    **Main table:** CustomerID + OrderID
    
    **LSI:** Same `CustomerID`, different sort key (`Price`)
    
    **GSI:** Different partition key (`Category`)
    
    ### SAA-C03 shortcut:
    
    > 🟢 **LSI = Same PK, Different SK**
    > 
    > 
    > 🔵 **GSI = Different PK, Different/optional SK**
    > 
    
    One more important thing: **LSI must be created when the table is created**, while **GSI can be added later**.
    

# **Cardinality, WCU & Hot partitions in DynamoDB**

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

Imagine the partition key is

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
        
        # 1. Understand the scenario first.
        
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
        
        # 9. Option 1—Reduce the number of partition keys ❌
        
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
        

# Data Types

## Scalar

Simple values.

```
String

Number

Boolean

Null

Binary
```

---

## Document

Like JSON.

```
Address

{

City

Dhaka

Road

12
}
```

---

## List

```
Skills

Python

AWS

Docker
```

---

## Set

Unique values only.

```
Sports

Football

Cricket

Badminton
```

Duplicates aren't allowed.

---

# Capacity Modes

This is a very important exam topic.

Two modes.

---

# 1. Provisioned Capacity

You tell AWS

"I expect"

```
100 Reads/sec

20 Writes/sec
```

AWS reserves that capacity.

You pay even if you don't use it.

---

Analogy

Booking

100 restaurant seats.

Even if only

10 customers come,

you still pay for all 100 seats.

---

# Auto Scaling

Provisioned mode can automatically adjust

Read Capacity Units (RCUs)

and

Write Capacity Units (WCUs)

based on demand.

---

# 2. On-Demand Mode

No planning.

AWS automatically handles everything.

Traffic

```
100 Users

↓

100,000 Users

↓

5 Users
```

AWS scales automatically.

---

You pay only for actual reads and writes.

---

Analogy

Uber.

Pay only when you ride.

---

# Which Should I Use?

## Provisioned

Predictable traffic.

Example

```
Company HR System
```

Same usage every day.

---

## On-Demand

Unpredictable traffic.

Example

```
Ticket Booking

Black Friday

Flash Sale
```

Huge spikes.

---

# DAX (DynamoDB Accelerator)

Imagine asking the same question repeatedly.

Instead of checking the database every time,

keep the answer in memory.

```
Application

↓

DAX

↓

DynamoDB
```

Memory is much faster than disk.

---

# DAX vs ElastiCache

Students often confuse them.

| DAX | ElastiCache |
| --- | --- |
| Only for DynamoDB | Works with many databases |
| AWS manages cache logic | You manage cache usage |
| API-compatible with DynamoDB | General-purpose Redis/Memcached cache |
| Speeds up DynamoDB reads | Speeds up applications in general |

**Memory trick:**

> **DAX = DynamoDB's personal cache.**
> 

---

# DynamoDB Streams

Imagine CCTV for your table.

Whenever data changes,

AWS records the event.

```
Insert

Update

Delete
```

Stream records

```
Old Value

↓

New Value
```

---

Example

```
User Created

↓

DynamoDB Stream

↓

Lambda

↓

Send Welcome Email
```

Very common architecture.

---

# Stream Characteristics

- Ordered changes
- 24-hour retention
- Real-time processing

---

# DynamoDB Streams vs Kinesis Data Streams

| DynamoDB Streams | Kinesis Data Streams |
| --- | --- |
| Only tracks changes in a DynamoDB table | General-purpose streaming platform |
| 24-hour retention | Up to 1 year retention |
| Commonly consumed by Lambda | Many consumers (Lambda, Analytics, Firehose, Glue, etc.) |

**Memory trick:**

- **DynamoDB Streams** = "What changed in my table?"
- **Kinesis** = "Handle lots of streaming data from anywhere."

---

# Global Tables

Suppose users are in

```
USA

Europe

Asia
```

Instead of one database in America,

AWS creates copies around the world.

```
USA

⇄

Europe

⇄

Asia
```

All regions can read and write.

Very low latency.

---

## GSI again

# **Global Secondary Index**

A **Global Secondary Index (GSI)** is an index on a DynamoDB table.

It helps query data using an alternative key.

For example:

```
Main table:
PK = UserID

GSI:
PK = Email
```

It does **not** mean:

```
Region A DynamoDB
        ↓
Region B DynamoDB
```

For cross-Region replication, we need:

> **DynamoDB Global Tables**
> 

# DynamoDB Global Secondary Index (GSI)

A **Global Secondary Index (GSI)** is an additional index on a DynamoDB table that lets you **query the same data using a different partition key (and optionally sort key)**.

### Simple example

Suppose your main table uses:

```
Table: Orders
Partition Key: CustomerID
Sort Key: OrderID
```

You normally query:

```
CustomerID = C100
```

But now you want to efficiently find orders by:

```
ProductID
```

Create a GSI:

```
Orders Table
│
├── CustomerID  ← Main table PK
├── OrderID      ← Main table SK
├── ProductID
└── Amount

GSI
├── ProductID    ← GSI PK
└── OrderID      ← GSI SK (optional)
```

Now you can query:

```
ProductID = P500
```

without scanning the entire table.

---

### Why "Global"?

**Global** means the index can contain items from **across the entire DynamoDB table**, rather than being restricted to a particular partition/key value.

---

### GSI vs Main Table

```
              DynamoDB Table
             /              \
            /                \
      Main Table             GSI
      CustomerID             ProductID
      OrderID                OrderID
```

The GSI is maintained separately by DynamoDB.

---

### Important SAA-C03 points

- ✅ Lets you query using a **different partition key**
- ✅ Can have a different partition key and sort key from the base table
- ✅ Can be created **after the table is created**
- ✅ GSI can be used for alternative access patterns
- ⚠️ GSI has its **own read/write capacity** when using provisioned capacity
- ⚠️ GSI updates are **eventually consistent**; strong consistency is not supported for GSI reads
- ✅ GSI can project selected attributes or all attributes (`ALL`)

### 🧠 Exam shortcut

> **"I need to query DynamoDB using a different partition key."**
> 

👉 **Global Secondary Index (GSI)**

Example:

```
Main table:  CustomerID → Find customer's orders

GSI:         ProductID  → Find orders containing a product
```

**GSI = another way to query the same DynamoDB data.**

# TTL (Time To Live)

Suppose

OTP expires after

```
5 Minutes
```

Instead of deleting manually,

set

```
TTL = 5 Minutes
```

AWS deletes expired items automatically.

---

Other examples:

- Shopping cart expiry
- Temporary sessions
- Login tokens
- Cache data

---

# Backups

Two types.

---

## 1. Point-in-Time Recovery (PITR)

Think of a time machine.

```
10:00

10:05

10:10

10:15
```

Oops.

Someone deleted data at

```
10:12
```

Restore to

```
10:11
```

Possible.

---

Important points:

- Continuous backups
- Up to **35 days**
- Restores into a **new table**

---

## 2. On-Demand Backup

Manual full backup.

Example

Before deployment.

```
Backup

↓

Deploy

↓

Problem

↓

Restore
```

Also restores into a **new table**.

---

# DynamoDB + S3 Integration

## Export to S3

Suppose you have

```
10 TB
```

of DynamoDB data.

Need analytics.

Export it.

```
DynamoDB

↓

S3

↓

Athena

Glue

EMR
```

Important:

- Requires PITR enabled
- Doesn't consume read capacity
- Great for analytics and auditing

---

## Import from S3

Have data in

- CSV
- DynamoDB JSON
- Amazon Ion

Import it directly.

```
S3

↓

DynamoDB
```

AWS creates a **new table**.

No write capacity is consumed during the import.

---

# Common Architectures

### User Registration

```
User

↓

API Gateway

↓

Lambda

↓

DynamoDB
```

---

### Real-Time Notification

```
DynamoDB

↓

Streams

↓

Lambda

↓

SNS

↓

Email
```

---

### Analytics

```
DynamoDB

↓

Export to S3

↓

Athena
```

---

### Multi-Region App

```
USA

⇄

Global Tables

⇄

Europe

⇄

Asia
```

---

# AWS SAA-C03 Cheat Sheet

| Topic | Remember |
| --- | --- |
| **DynamoDB** | Fully managed NoSQL database |
| **Primary Key** | Partition Key or Partition + Sort Key |
| **Item Limit** | **400 KB** maximum per item |
| **Provisioned Mode** | You plan RCUs/WCUs; can enable Auto Scaling |
| **On-Demand Mode** | AWS scales automatically; pay per request |
| **DAX** | In-memory cache for DynamoDB reads |
| **Streams** | Captures item changes (create/update/delete) for up to 24 hours |
| **Global Tables** | Multi-Region active-active replication |
| **TTL** | Automatically deletes expired items |
| **PITR** | Continuous backup for up to 35 days; restores to a new table |
| **On-Demand Backup** | Manual full backup; restores to a new table |
| **Export to S3** | Requires PITR; no read capacity consumed |
| **Import from S3** | Creates a new table; no write capacity consumed |

---

# Easy Memory Tricks

- 📦 **DynamoDB** → A giant box of flexible JSON-like items.
- 🆔 **Partition Key** → The unique ID that decides where data is stored.
- 📅 **Sort Key** → Lets you store multiple related items in order.
- ⚡ **DAX** → "Turbo mode" for DynamoDB reads.
- 📹 **Streams** → CCTV recording every data change.
- 🌍 **Global Tables** → Copies of your table around the world.
- ⏳ **TTL** → Automatic expiry and cleanup.
- ⏪ **PITR** → Time machine (up to 35 days).
- 📤 **Export to S3** → Move data out for analytics.
- 📥 **Import from S3** → Bulk-load data into a new table.

---

## What the SAA exam focuses on most

If you prioritize your study, remember these are the highest-value DynamoDB topics:

1. **Provisioned vs On-Demand Capacity**
2. **Partition Key vs Partition + Sort Key**
3. **DAX vs ElastiCache**
4. **DynamoDB Streams + Lambda**
5. **Global Tables**
6. **TTL**
7. **PITR vs On-Demand Backups**
8. **Export/Import with S3**

Master these eight concepts and you'll be well prepared for nearly all DynamoDB questions on the AWS SAA-C03 exam.
