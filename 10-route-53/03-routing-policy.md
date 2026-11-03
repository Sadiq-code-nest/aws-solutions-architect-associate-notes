# Route 53 Routing Policies — SAA-C03 Complete Exam Notes

---

# First: The Biggest Exam Trap

When AWS says **Routing Policy**, many students think:

```
User
 ↓
Route53
 ↓
Routes HTTP traffic
```

❌ WRONG

Route 53 never routes traffic.

Route 53 only answers DNS questions.

Think:

```
User: "What is the IP of example.com?"
Route53: "Use 10.0.1.5"
```

After that, Route 53 is done.

The browser connects directly to the returned IP.

---

# What Does a Routing Policy Actually Do?

A routing policy determines:

> "Which DNS answer should Route 53 return?"
> 

Example:

```
www.example.com
```

may have:

```
Server A = 1.1.1.1
Server B = 2.2.2.2
Server C = 3.3.3.3
```

When a DNS query arrives:

```
What is www.example.com?
```

The routing policy decides:

```
Return A?
Return B?
Return C?
Return A and B?
```

That's all.

---

# 1. Simple Routing Policy

## Idea

Return a single resource.

Most basic DNS behavior.

---

## Example

```
www.example.com
       ↓
   EC2 Instance
```

Record:

```
www.example.com
A -> 10.0.1.10
```

User asks:

```
What is www.example.com?
```

Route53 returns:

```
10.0.1.10
```

Done.

---

## Real World Example

One restaurant.

Customer asks:

> Which restaurant should I visit?
> 

Answer:

> Restaurant A
> 

No choice.

---

## AWS Example

```
www.example.com
      ↓
Single ALB
```

---

## Exam Tip

Simple = One answer.

---

# 2. Weighted Routing Policy

## Idea

Split users by percentage.

---

Suppose:

```
Server A
Weight = 80

Server B
Weight = 20
```

Total:

```
80 + 20 = 100
```

DNS responses:

```
80% → A
20% → B
```

---

## How Route53 Works Internally

User1 asks DNS:

```
www.example.com
```

Route53 randomly chooses based on weights.

Maybe:

```
A
```

User2:

```
A
```

User3:

```
B
```

User4:

```
A
```

Over thousands of requests:

```
80% A
20% B
```

---

## Real Life Example

Two shops:

```
Shop A gets 80 customers
Shop B gets 20 customers
```

---

## AWS Example

Blue/Green Deployment

Old Version:

```
Weight = 90
```

New Version:

```
Weight = 10
```

Traffic gradually shifted.

```
90 → 80 → 50 → 0
```

---

## Exam Use Case

Canary Deployment

Blue/Green Deployment

Traffic Splitting

A/B Testing

---

# 3. Failover Routing Policy

## Idea

Primary + Backup

Used for Disaster Recovery.

---

Setup:

```
Primary Server
      |
      | Health Check
      |
Backup Server
```

---

## Normal State

Health Check:

```
Primary = Healthy
```

DNS Answer:

```
Primary
```

---

## Failure State

Primary crashes.

The health check fails.

Route 53 now returns:

```
Backup
```

---

## Real Life Example

Main hospital closed.

Patients redirected to backup hospital.

---

## AWS Example

Primary:

```
ALB in us-east-1
```

Backup:

```
ALB in us-west-2
```

---

## Exam Keywords

Disaster Recovery

Active-Passive

Health Checks

Automatic DNS Failover

### **Active-Passive/Active-Active Failover concept**

- ScenarioQ
    
    A company is setting up a cloud architecture for an international money transfer service to be deployed in AWS which will have thousands of users around the globe. The service should be available 24/7 to avoid any business disruption and should be resilient enough to handle the outage of an entire AWS region. To meet this requirement, the Solutions Architect has deployed their AWS resources to multiple AWS Regions. He needs to use Route 53 and configure it to set all of the resources to be available all the time as much as possible. When a resource becomes unavailable, Route 53 should detect that it’s unhealthy and stop including it when responding to queries.
    
    Which of the following is the most fault-tolerant routing configuration that the Solutions Architect should use in this scenario?
    
    - Configure an Active-Passive Failover with Weighted Records.
    - Configure an Active-Passive Failover with Multiple Primary and Secondary Resources.
    - Configure an Active-Active Failover with Weighted routing policy.
    - Configure an Active-Active Failover with One Primary and One Secondary Resource.
- ***In details***
    
    # Active-Passive vs Active-Active Failover
    
    These two concepts are mainly about **how your application uses multiple resources during normal operation and during failure**.
    
    The easiest way to remember:
    
    > **Active = serving traffic now**
    > 
    > 
    > **Passive = waiting as backup**
    > 
    
    ---
    
    # 1. 🔵 Active-Passive Failover
    
    ### What is it?
    
    In **Active-Passive**, one resource is **active and serving traffic**, while another resource is **passive and waiting for failure**.
    
    ```
                     Users
                       |
                       ↓
                  Active Server
                     Region A
                       |
                    ✅ Traffic
    
                  Passive Server
                     Region B
                       |
                    ⏸ Waiting
    ```
    
    ### Normal situation
    
    ```
    Users
      ↓
    Active Resource A ✅
      ↓
    Application
    ```
    
    Resource B is available but **doesn't normally serve traffic**.
    
    ### When Active fails
    
    ```
    Users
      ↓
    Active A ❌
      ↓
    Failover
      ↓
    Passive B
      ↓
    Application continues ✅
    ```
    
    The passive resource becomes active.
    
    ---
    
    ## 🏢 Real-world example
    
    Imagine a bank has two data centers:
    
    ```
    Data Center A → ACTIVE
    Data Center B → PASSIVE
    ```
    
    Normally:
    
    ```
    Customers
        ↓
    Data Center A
    ```
    
    If Data Center A completely fails:
    
    ```
    Data Center A ❌
           ↓
    Customers
           ↓
    Data Center B
           ↓
    Continue service
    ```
    
    ### When is Active-Passive useful?
    
    Use it when:
    
    - You need a **standby/backup**
    - You don't need both resources serving traffic simultaneously
    - You want a simpler disaster-recovery setup
    - The backup infrastructure can remain idle until needed
    
    ### Key idea
    
    > **One works, one waits.**
    > 
    
    ---
    
    # 2. 🟢 Active-Active Failover
    
    ### What is it?
    
    In **Active-Active**, **multiple resources are serving traffic at the same time**.
    
    ```
                     Users
                       |
                       ↓
                  Route 53
                  /       \
                 ↓         ↓
            Region A    Region B
              ACTIVE      ACTIVE
                 ↓         ↓
              Traffic     Traffic
    ```
    
    Both resources are operational and handling requests.
    
    ---
    
    ## Normal situation
    
    For example:
    
    ```
    1000 users
        ↓
    Route 53
       / \
      ↓   ↓
    A     B
    50%   50%
    ```
    
    Both Region A and Region B are serving users.
    
    If Region A fails:
    
    ```
    Region A ❌
        ↓
    Route 53 detects unhealthy
        ↓
    Region A removed from DNS responses
        ↓
    Region B handles traffic
    ```
    
    So you don't have a completely idle backup.
    
    ---
    
    # 🏢 Real-world example
    
    Imagine Netflix-like application infrastructure:
    
    ```
    Users worldwide
           ↓
        Route 53
         /     \
        ↓       ↓
     US Region  Europe Region
       ACTIVE      ACTIVE
        ↓           ↓
      Users       Users
    ```
    
    Both regions serve users normally.
    
    If the US region goes down:
    
    ```
    US Region ❌
         ↓
    Route 53
         ↓
    Europe Region ✅
         ↓
    Users
    ```
    
    ---
    
    # 🔥 Active-Passive vs Active-Active
    
    |  | **Active-Passive** | **Active-Active** |
    | --- | --- | --- |
    | Normal traffic | One resource | Multiple resources |
    | Backup | Passive/standby | Also actively serving |
    | Resource utilization | Backup may be idle | Both are utilized |
    | Failure | Passive becomes active | Remaining active resources handle traffic |
    | Cost | Usually lower | Usually higher |
    | Availability | High | Very high |
    | Good for | DR/standby | Global/high-availability systems |
    
    ---
    
    # 3. Route 53 Connection
    
    This is where your reference question becomes easier to understand.
    
    **Route 53 can perform health checks and routing decisions.**
    
    Imagine:
    
    ```
                     Route 53
                        |
                  Health Check
                        |
              ┌─────────┴─────────┐
              ↓                   ↓
          Region A             Region B
          Resource              Resource
    ```
    
    If Region A becomes unhealthy:
    
    ```
    Region A ❌
        ↓
    Route 53 detects unhealthy
        ↓
    Stops returning Region A
        ↓
    Traffic goes to healthy resource
    ```
    
    This is the basic failover idea.
    
    ---
    
    # 4. Active-Passive with Multiple Resources
    
    Don't think that Active-Passive necessarily means **exactly one active + one passive**.
    
    You can have multiple resources.
    
    For example:
    
    ```
                    Route 53
                       |
            ┌──────────┴──────────┐
            ↓                     ↓
       PRIMARY SET            SECONDARY SET
       ┌────┬────┐             ┌────┬────┐
       A1   A2                  B1   B2
       ✅    ✅                  ⏸    ⏸
    ```
    
    The primary resources serve traffic.
    
    The secondary resources are backups.
    
    If the primary set fails, traffic can move to the secondary set.
    
    ---
    
    # 5. Active-Active with Weighted Routing
    
    A common way to implement Active-Active is **Route 53 Weighted Routing**.
    
    Example:
    
    ```
                     Route 53
                     /       \
                  70%         30%
                   ↓           ↓
              Region A      Region B
               ACTIVE        ACTIVE
    ```
    
    Both are serving traffic.
    
    You can control how much traffic goes to each.
    
    For example:
    
    - Region A → 70%
    - Region B → 30%
    
    If A becomes unhealthy, Route 53 can stop returning the unhealthy endpoint, depending on the health-check configuration.
    
    ---
    
    # 6. Active-Active Is NOT Just "Two Servers"
    
    This is important.
    
    Simply having:
    
    ```
    Server A
    Server B
    ```
    
    doesn't automatically mean Active-Active.
    
    They need to be **actively serving traffic simultaneously**.
    
    ```
    A → traffic ✅
    B → traffic ✅
    ```
    
    That's Active-Active.
    
    If:
    
    ```
    A → traffic ✅
    B → waiting ⏸
    ```
    
    That's Active-Passive.
    
    ---
    
    # 7. Active-Passive Example with Route 53
    
    ```
                         Route 53
                            |
                         Primary
                            ↓
                      Region A
                         ACTIVE
                            |
                         Users
    
                      Region B
                       PASSIVE
    ```
    
    Failure:
    
    ```
    Region A ❌
        ↓
    Route 53 health check
        ↓
    Failover
        ↓
    Region B
        ↓
    ACTIVE
        ↓
    Users
    ```
    
    ---
    
    # 8. Active-Active Example with Route 53
    
    ```
                        Route 53
                       /        \
                      ↓          ↓
                 Region A    Region B
                  ACTIVE       ACTIVE
                     ↓           ↓
                  Users        Users
    ```
    
    Failure:
    
    ```
    Region A ❌
         ↓
    Route 53 Health Check
         ↓
    Remove unhealthy endpoint
         ↓
    Region B ✅
         ↓
    Users
    ```
    
    ---
    
    # 9. When Should You Choose Which?
    
    ### Choose Active-Passive when:
    
    > **"I need a standby backup that takes over when the primary fails."**
    > 
    
    Example:
    
    **Primary database/application → backup DR environment**
    
    ```
    Primary → ACTIVE
    Backup  → PASSIVE
    ```
    
    ---
    
    ### Choose Active-Active when:
    
    > **"I want multiple environments serving users at the same time."**
    > 
    
    Example:
    
    **Global application deployed in multiple AWS Regions**
    
    ```
    US → ACTIVE
    EU → ACTIVE
    Asia → ACTIVE
    ```
    
    Benefits:
    
    - Better resource utilization
    - Can serve users from multiple locations
    - Regional failure can be absorbed by other active regions
    - Can distribute traffic
    
    ---
    
    # 🧠 The Simplest Way to Remember
    
    Imagine **two restaurants**.
    
    ### Active-Passive 🍔
    
    ```
    Restaurant A → OPEN ✅
    Restaurant B → CLOSED, ready to open ⏸
    ```
    
    If A closes:
    
    ```
    A ❌
    ↓
    B opens ✅
    ```
    
    **One works, one waits.**
    
    ---
    
    ### Active-Active 🍔🍔
    
    ```
    Restaurant A → OPEN ✅
    Restaurant B → OPEN ✅
    ```
    
    Both serve customers.
    
    If A closes:
    
    ```
    A ❌
    ↓
    B continues serving ✅
    ```
    
    **Both work simultaneously.**
    
    ---
    
    # ⭐ SAA-C03 Exam Distinction
    
    | If the question says... | Think |
    | --- | --- |
    | **Primary + standby** | Active-Passive |
    | **Primary + backup** | Active-Passive |
    | **Standby takes over after failure** | Active-Passive |
    | **Multiple resources serving traffic simultaneously** | Active-Active |
    | **Multiple AWS Regions serving users** | Active-Active |
    | **Distribute traffic between regions** | Active-Active |
    | **Health checks + remove unhealthy endpoints** | Route 53 |
    | **Weighted traffic between multiple healthy endpoints** | Route 53 Weighted Routing |
    
    ### 🔑 One-line memory:
    
    > **Active-Passive = "1 working + 1 waiting"**
    > 
    > 
    > **Active-Active = "Both working together"**
    > 

---

# 4. Latency-Based Routing

## Idea

Send users to region with lowest latency.

NOT nearest distance.

Lowest network delay.

---

Servers:

```
US-East
Europe
Asia
```

---

User in India:

Latency:

```
Mumbai = 20ms
Virginia = 200ms
London = 180ms
```

Route53 returns:

```
Mumbai
```

---

User in Germany:

```
Frankfurt = 10ms
Virginia = 100ms
```

Returns:

```
Frankfurt
```

---

## Real Life Example

Choosing the nearest food delivery branch.

Not based on kilometers.

Based on fastest delivery time.

---

## AWS Example

```
ALB - us-east-1
ALB - eu-central-1
ALB - ap-south-1
```

Users automatically get lowest latency region.

---

## Exam Keywords

Performance

Global Applications

Best User Experience

Lowest Network Delay

---

# 5. Geolocation Routing

## Idea

Choose based on the user's geographic location.

Country.

State.

Continent.

---

NOT latency.

Only geography.

---

Example

Bangladesh:

```
Show Bengali Website
```

USA:

```
Show English Website
```

Japan:

```
Show Japanese Website
```

---

## Real Life Example

 Netflix showing different content by country.

---

## AWS Example

```
US Users → US ALB

Europe Users → EU ALB

Asia Users → Asia ALB
```

---

## Important

A user in Bangladesh may have:

```
Latency to Singapore = 20ms
Latency to Mumbai = 15ms
```

But Geolocation ignores latency.

It only looks at:

```
Country = Bangladesh
```

---

## Exam Keywords

Content Localization

Country-Specific Content

Legal Restrictions

Regional Websites

---

# 6. Multi-Value Answer Routing

## Idea

Return multiple healthy IPs.

Simple load distribution.

---

Servers:

```
10.0.1.10
10.0.1.11
10.0.1.12
```

Route 53 returns:

```
10.0.1.10
10.0.1.11
10.0.1.12
```

---

Client chooses one.

---

## Health Check Integration

Suppose:

```
10.0.1.11
```

dies.

Health check fails.

Route 53 now returns:

```
10.0.1.10
10.0.1.12
```

Only healthy records.

---

## Real Life Example

Restaurant list:

```
Restaurant A
Restaurant B
Restaurant C
```

If B closes:

```
A
C
```

---

## AWS Example

Several EC2 servers without an ELB.

---

## Exam Tip

Many students confuse this with Load Balancer.

It is NOT a load balancer.

DNS only returns multiple healthy IPs.

---

# 7. Geoproximity Routing (Traffic Flow)

## Idea

Route based on geographic distance.

Can shift traffic using Bias.

---

Example

Regions:

```
US-East
Europe
```

Normally:

```
European users
      ↓
Europe

US users
      ↓
US-East
```

---

## Bias

## **BIAS DETAILS**

# What is Bias in Geoproximity Routing?

First remember:

**Geoproximity Routing = Route 53 chooses a resource based on geographic distance.**

Example:

```
Resource A → Singapore
Resource B → Tokyo
```

Normally:

```
Users near Singapore → Singapore

Users near Tokyo → Tokyo
```

Simple.

---

# What is Bias?

**Bias lets you artificially expand or shrink a resource's geographic influence.**

Think of it as:

> "Pretend this location is bigger or smaller than it really is."
> 

---

# Real-Life Example

Imagine two food delivery zones.

Without bias:

```
Restaurant A covers 10 km
Restaurant B covers 10 km
```

Customers are assigned to whichever restaurant is closer.

---

Now you want Restaurant A to handle more orders.

You increase its bias.

Now:

```
Restaurant A covers 15 km
Restaurant B covers 10 km
```

More customers get assigned to A.

Nothing physically moved.

You just expanded A's influence.

---

# AWS Example

Suppose:

```
ALB 1 → Singapore
ALB 2 → Tokyo
```

Normal routing:

```
Malaysia users → Singapore
Japan users → Tokyo
```

---

Now Tokyo has lots of spare capacity.

You set:

```
Tokyo Bias = +50
```

Result:

```
Some users that previously went to Singapore
may now be routed to Tokyo.
```

Tokyo's "region of influence" becomes larger.

---

# Positive Bias

```
Bias = +50
```

Means:

```
Expand influence
Receive more traffic
```

Visual:

```
Before

[Singapore]------|------[Tokyo]

After Tokyo +50

[Singapore]---|----------[Tokyo]
```

Tokyo attracts more users.

---

# Negative Bias

```
Bias = -50
```

Means:

```
Shrink influence
Receive less traffic
```

Visual:

```
Before

[Singapore]------|------[Tokyo]

After Tokyo -50

[Singapore]----------|--[Tokyo]
```

Tokyo gets fewer users.

---

# Why Use Bias?

## Scenario 1: New Region

You launch a new region:

```
Singapore
Tokyo
Mumbai (new)
```

You want to gradually move users to Mumbai.

Increase Mumbai bias.

---

## Scenario 2: Region Under Heavy Load

Tokyo is overloaded.

Reduce Tokyo bias:

```
Tokyo Bias = -30
```

More users go elsewhere.

---

## Scenario 3: Region Has Extra Capacity

Singapore is underutilized.

Increase:

```
Singapore Bias = +40
```

More users are sent there.

---

# Exam Comparison

Many students confuse these:

| Policy | Decision Based On |
| --- | --- |
| Latency | Fastest network path |
| Geolocation | User country/continent |
| Geoproximity | Geographic distance |
| Geoproximity + Bias | Geographic distance adjusted by AWS |

---

# Easy Exam Memory Trick

Imagine each AWS region has a **magnet**.

- **Positive Bias (+)** → stronger magnet → pulls more users
- **Negative Bias (-)** → weaker magnet → pulls fewer users

The regions don't move.

The users don't move.

Only the "strength" of each region's attraction changes.

That's exactly what **Bias** does in Geoproximity Routing. 🚀

AWS allows expanding or shrinking a region's influence.

Example:

```
Europe Bias +50
```

Europe attracts more nearby users.

Even some users outside Europe may be routed there.

## Real Life Example

Two supermarkets.

One expands delivery coverage.

Now more customers use it.

---

## AWS Example

```
ALB Europe
ALB US
```

Traffic flow adjusts geographic influence.

---

# Ultimate Comparison Table

| Policy | Decision Based On |
| --- | --- |
| Simple | Single record |
| Weighted | Percentage (weights) |
| Failover | Health status |
| Latency | Lowest latency |
| Geolocation | User country/location |
| Multi-Value | Multiple healthy IPs |
| Geoproximity | Geographic distance + bias |

---

# Exam Memory Trick

Imagine AWS asks:

### One server?

✅ Simple

---

### 80% / 20% traffic?

✅ Weighted

---

### Primary + Backup?

✅ Failover

---

### Fastest response?

✅ Latency

---

### Country-specific website?

✅ Geolocation

---

### Return multiple healthy servers?

✅ Multi-Value Answer

---

### Shift users geographically?

✅ Geoproximity

---

# Most Important SAA-C03 Distinction

### Latency Routing

```
Decision = Fastest network path
```

User in Germany may go to:

```
London
```

if latency is lower.

---

### Geolocation Routing

```
Decision = Country/Region
```

German users always follow Germany/Europe rule, regardless of latency.

This distinction appears frequently in AWS certification questions.
