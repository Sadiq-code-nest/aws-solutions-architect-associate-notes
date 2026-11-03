# Amazon Route 53 Routing Policies — Complete Master Table

### AWS SAA-C03 Exam Revision

The easiest way to understand Route 53 routing policies is:

> **Route 53 decides WHICH resource/endpoint should receive the DNS request.**
> 

It does **not** directly route the actual application traffic.

---

## 🏆 MASTER TABLE — ALL ROUTE 53 ROUTING POLICIES

| Routing Policy | Main Purpose | How Route 53 Chooses | Typical Scenario | Example | SAA-C03 Exam Clue |
| --- | --- | --- | --- | --- | --- |
| **Simple** | Basic DNS routing | Returns the resource/value | One application, one endpoint | `www.example.com → ALB` | **"Single resource / basic DNS"** |
| **Weighted** | Control traffic percentage | Based on assigned **weights** | Send 90% to old version, 10% to new version | v1 = 90, v2 = 10 | **"Percentage / traffic split / canary"** |
| **Latency-based** | Send users to region with lowest latency | AWS measures network latency | Global application deployed in US, Europe, Asia | User → nearest/lowest-latency region | **"Lowest latency / best-performing AWS Region"** |
| **Failover** | Active-passive DR | Primary unless unhealthy → secondary | Primary website + backup DR website | Primary ALB → DR S3/ALB | **"Primary/secondary / disaster recovery"** |
| **Geolocation** | Route based on user's geographic location | Country/continent/state | European users → EU server | Germany → Frankfurt | **"Users from a specific country/continent"** |
| **Geoproximity** | Route based on geographic proximity + optional bias | Geographic location of resources/users | Shift more traffic toward one region | Bias US region to receive more traffic | **"Geographic distance + bias"** |
| **IP-based** | Route based on client's IP address | Client IP → predefined CIDR mapping | Corporate users → private/optimized endpoint | `10.0.0.0/8 → Endpoint A` | **"Source IP / CIDR"** |
| **Multivalue Answer** | Return multiple healthy IPs | Up to multiple healthy records | Basic DNS-level availability | Return 3 healthy web server IPs | **"Multiple healthy resources / simple DNS failover"** |

---

- <u>Details</u>
    
    # 1. Simple Routing
    
    ### Idea
    
    The simplest policy.
    
    ```
    User
     ↓
    Route 53
     ↓
    Application
    ```
    
    Example:
    
    ```
    www.example.com
           ↓
         ALB
    ```
    
    Route 53 returns the configured record.
    
    ### Use when
    
    - You have a single resource.
    - No sophisticated routing requirement.
    - Basic DNS resolution.
    
    ### Example
    
    A company has:
    
    ```
    www.company.com → ALB
    ```
    
    Use **Simple Routing**.
    
    ### 🧠 Exam clue
    
    > **One resource / simple DNS routing**
    > 
    
    ---
    
    # 2. Weighted Routing ⭐⭐⭐⭐⭐
    
    ### Idea
    
    Split traffic according to **weights**.
    
    Example:
    
    | Endpoint | Weight |
    | --- | --- |
    | Version 1 | 90 |
    | Version 2 | 10 |
    
    Approximately:
    
    ```
    100 requests
    │
    ├── 90 → Version 1
    └── 10 → Version 2
    ```
    
    ### Perfect for
    
    - Canary deployments
    - Testing a new application version
    - Gradual migration
    - A/B-style traffic distribution
    
    ### Example
    
    You have:
    
    ```
    Old application → 90%
    New application → 10%
    ```
    
    If the new version works well, gradually change:
    
    ```
    90/10
     ↓
    70/30
     ↓
    50/50
     ↓
    0/100
    ```
    
    ### 🧠 Exam clue
    
    > **"Send 10% of traffic to the new application."**
    > 
    
    ➡️ **Weighted Routing**
    
    ---
    
    # 3. Latency-Based Routing ⭐⭐⭐⭐⭐
    
    ### Idea
    
    Route the user to the AWS Region that provides the **lowest network latency**.
    
    ```
                     Route 53
                  /     |      \
                 ↓      ↓       ↓
               USA    Europe   Asia
    ```
    
    A user in Asia may be directed to:
    
    ```
    Asia Region
    ```
    
    while a European user may be directed to:
    
    ```
    Europe Region
    ```
    
    ### Important
    
    It is based on **measured latency**, not simply geographical distance.
    
    ### Example
    
    Your application runs in:
    
    ```
    us-east-1
    eu-west-1
    ap-southeast-1
    ```
    
    Route 53 sends each user to the Region that provides the lowest latency.
    
    ### 🧠 Exam clue
    
    > **"Lowest latency"**
    > 
    
    > **"Best-performing Region for the user"**
    > 
    
    ➡️ **Latency-Based Routing**
    
    ---
    
    # 4. Failover Routing ⭐⭐⭐⭐⭐
    
    ### Idea
    
    Create an **active-passive** architecture.
    
    ```
                 Route 53
                    │
              Health Check
                    │
            ┌───────┴───────┐
            ↓               ↓
         PRIMARY          SECONDARY
         Active              DR
    ```
    
    Normally:
    
    ```
    User → Primary
    ```
    
    If primary becomes unhealthy:
    
    ```
    User → Secondary
    ```
    
    ### Example
    
    Primary:
    
    ```
    ALB in us-east-1
    ```
    
    Secondary:
    
    ```
    ALB in us-west-2
    ```
    
    If the primary fails:
    
    ```
    Route 53
       ↓
    Secondary
    ```
    
    ### Perfect for
    
    - Disaster Recovery
    - Active-passive architecture
    - Backup website
    
    ### 🧠 Exam clue
    
    > **"Primary and secondary"**
    > 
    
    > **"DR site"**
    > 
    
    > **"Route traffic to backup if primary fails"**
    > 
    
    ➡️ **Failover Routing**
    
    ---
    
    # 5. Geolocation Routing ⭐⭐⭐⭐⭐
    
    ### Idea
    
    Route users based on **where they are geographically located**.
    
    You can route based on:
    
    - Country
    - Continent
    - US state
    
    Example:
    
    ```
    US users
       ↓
    US application
    
    European users
       ↓
    EU application
    
    Asian users
       ↓
    Asia application
    ```
    
    ### Example
    
    A company wants:
    
    ```
    Germany → Frankfurt
    UK → London
    US → Virginia
    ```
    
    Use **Geolocation Routing**.
    
    ### Important distinction
    
    Geolocation answers:
    
    > **"Where is this user located?"**
    > 
    
    ### 🧠 Exam clue
    
    > **"Users from Germany should go to..."**
    > 
    
    > **"European users should access..."**
    > 
    
    ➡️ **Geolocation**
    
    ---
    
    # 6. Geoproximity Routing ⭐⭐⭐⭐
    
    ### Idea
    
    Route traffic based on the **geographic location of users and resources**.
    
    The special feature is:
    
    ### **Bias**
    
    You can increase or decrease the amount of traffic directed toward a location.
    
    Example:
    
    ```
    US Region
    Bias = +50
    ```
    
    Increasing the bias makes that location's routing area larger.
    
    ```
                        US
                 ← larger area →
    ```
    
    ### Why use it?
    
    Useful when you want to:
    
    - Shift traffic toward a particular Region
    - Gradually migrate traffic
    - Control geographic traffic distribution
    
    ### Important distinction
    
    | Geolocation | Geoproximity |
    | --- | --- |
    | Based on user's geographic location | Based on geographic proximity |
    | Country/continent/state | Resource/user geographic locations |
    | No traffic bias concept | **Supports bias** |
    | "Germany → Germany endpoint" | "Send more traffic toward this region" |
    
    ### 🧠 Exam clue
    
    > **"Geographic proximity" + "bias"**
    > 
    
    ➡️ **Geoproximity**
    
    ---
    
    # 7. IP-Based Routing
    
    ### Idea
    
    Route users based on their **source IP address**.
    
    You create mappings such as:
    
    ```
    Client CIDR
          ↓
    Endpoint
    ```
    
    Example:
    
    | Client IP range | Destination |
    | --- | --- |
    | `10.0.0.0/8` | Endpoint A |
    | `203.0.113.0/24` | Endpoint B |
    
    ### Scenario
    
    A company has different customers coming from known IP ranges and wants each IP range to use a particular endpoint.
    
    ### 🧠 Exam clue
    
    > **"Source IP address"**
    > 
    
    > **"CIDR-based routing"**
    > 
    
    ➡️ **IP-Based Routing**
    
    ---
    
    # 8. Multivalue Answer Routing ⭐⭐⭐⭐
    
    ### Idea
    
    Route 53 can return **multiple healthy records** in response to a DNS query.
    
    Example:
    
    ```
    Route 53
       │
       ├── EC2-A ✅
       ├── EC2-B ✅
       └── EC2-C ✅
    ```
    
    Route 53 may return multiple healthy IP addresses.
    
    The client can then connect to one of them.
    
    ### Important
    
    Multivalue Answer Routing is **not a replacement for an ELB**.
    
    It provides a basic DNS-level distribution/availability mechanism.
    
    ### Example
    
    You have three web servers:
    
    ```
    10.0.1.10
    10.0.2.10
    10.0.3.10
    ```
    
    Route 53 returns multiple healthy IP addresses.
    
    If one becomes unhealthy, Route 53 can exclude it when health checks are configured.
    
    ### 🧠 Exam clue
    
    > **"Return multiple healthy resources"**
    > 
    
    > **"Multiple IP addresses in DNS response"**
    > 
    
    ➡️ **Multivalue Answer**
    
    ---
    

# 🔥 ROUTE 53 POLICIES — HEAD-TO-HEAD

| Policy | Think This Word | Decision Based On | Main Use |
| --- | --- | --- | --- |
| **Simple** | Basic | Nothing special | One resource |
| **Weighted** | **%** | Weight | Canary / traffic split |
| **Latency** | **Fastest** | Network latency | Global performance |
| **Failover** | **DR** | Health | Primary/backup |
| **Geolocation** | **Where** | User location | Country/continent routing |
| **Geoproximity** | **Distance + Bias** | Geographic proximity | Shift geographic traffic |
| **IP-based** | **IP** | Client CIDR | IP-specific routing |
| **Multivalue** | **Multiple** | Healthy resources | Multiple healthy IPs |

---

# 🚨 MOST IMPORTANT CONFUSIONS

## Weighted vs Latency

| Weighted | Latency |
| --- | --- |
| You control the percentage | AWS determines based on latency |
| 90/10, 70/30 etc. | Lowest latency |
| Canary deployment | Global application performance |
| **Traffic percentage** | **Network performance** |

### Memory

> **Weighted = "How much?"**
> 

> **Latency = "Which is fastest?"**
> 

---

## Geolocation vs Geoproximity

| Geolocation | Geoproximity |
| --- | --- |
| User's location | Geographic proximity |
| Country/continent/state | Geographic distance |
| "French users → France" | "Closer to this resource" |
| No bias | **Bias available** |

### Memory

> **Geolocation = WHERE the user is**
> 

> **Geoproximity = HOW CLOSE + BIAS**
> 

---

## Failover vs Multivalue

| Failover | Multivalue |
| --- | --- |
| Primary + secondary | Multiple healthy resources |
| Active-passive | Multiple possible endpoints |
| Disaster Recovery | DNS-level availability |
| One primary normally receives traffic | Multiple healthy records returned |

### Memory

> **Failover = "If primary dies, use backup."**
> 

> **Multivalue = "Here are several healthy choices."**
> 

---

# 🏆 FINAL EXAM-NIGHT MASTER TABLE

| If the question says... | Choose |
| --- | --- |
| **One website → one endpoint** | 🟢 Simple |
| **Send 10% traffic to new version** | 🟢 Weighted |
| **Canary deployment** | 🟢 Weighted |
| **Gradually migrate traffic** | 🟢 Weighted |
| **Lowest latency Region** | 🔵 Latency |
| **Global application, users should use fastest Region** | 🔵 Latency |
| **Primary + DR/backup** | 🔴 Failover |
| **Route to secondary when primary fails** | 🔴 Failover |
| **Users from different countries** | 🟣 Geolocation |
| **Country/continent/state-based routing** | 🟣 Geolocation |
| **Geographic proximity + bias** | 🟠 Geoproximity |
| **Shift traffic toward a geographic resource** | 🟠 Geoproximity |
| **Client source IP / CIDR** | 🟡 IP-based |
| **Return multiple healthy IP addresses** | 🟢 Multivalue |
| **DNS-level basic availability with multiple resources** | 🟢 Multivalue |

---

# 🧠 8-Word Memory Trick

Remember the policies in this order:

> **Simple → Weighted → Latency → Failover → Geolocation → Geoproximity → IP → Multivalue**
> 

And associate:

```
Simple       = BASIC
Weighted     = %
Latency      = FASTEST
Failover     = DR
Geolocation  = WHERE
Geoproximity = DISTANCE + BIAS
IP-based     = IP
Multivalue   = MULTIPLE
```

### ⭐ If you remember only this:

**% → Weighted**

**Fastest → Latency**

**Primary/DR → Failover**

**Country → Geolocation**

**Distance + Bias → Geoproximity**

**Source IP → IP-based**

**Multiple healthy IPs → Multivalue**

**Basic → Simple**
