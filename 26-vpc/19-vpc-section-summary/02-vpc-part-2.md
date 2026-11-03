# 16. Ephemeral Ports

Because NACL is stateless, you must remember **return traffic**.

Suppose:

```
Client
Source port: 50000
       ↓
Server
Port: 443
```

Response:

```
Server
Source port: 443
       ↓
Client
Destination port: 50000
```

That `50000` is an **ephemeral port**.

Therefore, NACL rules may need to permit the relevant ephemeral port range for return traffic.

### Exam clue

If question says:

> "NACL is blocking return traffic"
> 

Think:

> **Ephemeral ports.**
> 
- More details Ephemeral Non-ephemeral ports
    
    Absolutely. The confusing part is usually **“why does the client suddenly use port 50000?”** Let's build it from zero.
    
    ## 1. First: what is a port?
    
    Think of an IP address as a **building address**, and a port as a **specific door inside that building**.
    
    For example:
    
    ```
    Server IP: 10.0.1.20
    
    10.0.1.20:443  → HTTPS door
    10.0.1.20:80   → HTTP door
    10.0.1.20:22   → SSH door
    ```
    
    So:
    
    **IP = Which machine?**
    
    **Port = Which application/service on that machine?**
    
    ---
    
    # 2. Now imagine your browser connects to HTTPS
    
    You open:
    
    ```
    https://example.com
    ```
    
    The server is listening on:
    
    ```
    Server: 10.0.1.20
    Port: 443
    ```
    
    Your computer needs to connect to it.
    
    Your computer says:
    
    > "I'll use one of my own temporary ports to identify this connection."
    > 
    
    For example:
    
    ```
    Client
    10.0.2.10:50000
           |
           |  HTTPS request
           ↓
    Server
    10.0.1.20:443
    ```
    
    Notice:
    
    ```
    10.0.2.10:50000  →  10.0.1.20:443
       Client              Server
    ```
    
    Here:
    
    - `10.0.2.10` = client IP
    - `50000` = **ephemeral port**
    - `10.0.1.20` = server IP
    - `443` = server's HTTPS port
    
    ---
    
    # 3. What exactly is an ephemeral port?
    
    **Ephemeral = temporary**
    
    Your computer doesn't normally have one permanent port for every outgoing connection.
    
    Instead, the operating system temporarily chooses a port.
    
    For example:
    
    ```
    50000
    50001
    50002
    50003
    ...
    ```
    
    So if you open a website, your computer might say:
    
    > "I'll use port 50000 for this connection."
    > 
    
    Another connection might use:
    
    ```
    Client → Server
    50001 → 443
    ```
    
    Another:
    
    ```
    Client → Server
    50002 → 443
    ```
    
    So you can have:
    
    ```
    10.0.2.10:50000 → 10.0.1.20:443
    10.0.2.10:50001 → 10.0.1.20:443
    10.0.2.10:50002 → 10.0.1.20:443
    ```
    
    All going to the same HTTPS server.
    
    ---
    
    # 4. Now comes the important NACL part
    
    This is where AWS exam questions become tricky.
    
    **NACL = stateless.**
    
    That means the NACL doesn't remember:
    
    > "Oh, this response belongs to a connection that I already allowed."
    > 
    
    It evaluates the traffic according to its rules.
    
    Let's see the request.
    
    ### Request
    
    ```
    Client                         Server
    
    10.0.2.10:50000
           ─────────────────────→
                             10.0.1.20:443
    ```
    
    You might have a NACL rule:
    
    ```
    ALLOW
    Destination Port: 443
    ```
    
    So the request is allowed.
    
    But now the server needs to respond.
    
    ---
    
    # 5. Look carefully at the response
    
    The server responds **back to the client's port 50000**.
    
    ```
    Client                         Server
    
    10.0.2.10:50000
           ←─────────────────────
                             10.0.1.20:443
    ```
    
    From the server's perspective:
    
    ```
    Source:
    10.0.1.20:443
    
    Destination:
    10.0.2.10:50000
    ```
    
    So the response is:
    
    ```
    10.0.1.20:443
            ↓
    10.0.2.10:50000
    ```
    
    **50000 is the important part.**
    
    ---
    
    # 6. Why does the response go to 50000?
    
    Because that's the temporary port the client used for this particular connection.
    
    Imagine this conversation:
    
    ```
    Client: "Hey server, I want HTTPS."
            Source port = 50000
    
    Server: "Sure, here's your response."
            Send it back to port = 50000
    ```
    
    It's basically an **answer address**.
    
    If your client had used port `52341` instead:
    
    ```
    Client
    10.0.2.10:52341
           ↓
    Server
    10.0.1.20:443
    ```
    
    The response would be:
    
    ```
    Server
    10.0.1.20:443
           ↓
    Client
    10.0.2.10:52341
    ```
    
    So **the server doesn't respond to some random port**. It responds to the client's ephemeral source port.
    
    ---
    
    # 7. Why does NACL care?
    
    Because NACL is **stateless**.
    
    Suppose:
    
    ```
    Client → Server
    50000 → 443
    ```
    
    You allow:
    
    ```
    Inbound:
    ALLOW TCP 443
    ```
    
    Great.
    
    But the response is:
    
    ```
    Server → Client
    443 → 50000
    ```
    
    The NACL sees:
    
    > Destination port = 50000
    > 
    
    If your NACL doesn't allow that return traffic, it can be blocked.
    
    Therefore, depending on the traffic direction and subnet, you may need NACL rules allowing the **ephemeral port range** used by clients.
    
    ---
    
    # 8. The easiest way to remember it
    
    Forget the complicated definition for a moment.
    
    Remember this story:
    
    ```
    Client picks a temporary port
                 ↓
            "50000"
                 ↓
    Client: 50000 → Server: 443
                 ↓
    Server responds
                 ↓
    Server: 443 → Client: 50000
    ```
    
    ### Therefore:
    
    > **Ephemeral port = temporary port chosen by the client for an outgoing connection.**
    > 
    
    ---
    
    # 9. NACL vs Security Group — VERY important for SAA
    
    This is where you should connect the concept to the exam.
    
    ### Security Group
    
    Security Group is **stateful**.
    
    If:
    
    ```
    Client → Server:443
    ```
    
    is allowed, the response is automatically allowed because SG remembers the connection state.
    
    You generally don't need a separate inbound rule for the ephemeral return port.
    
    ### NACL
    
    NACL is **stateless**.
    
    It doesn't remember the connection.
    
    So:
    
    ```
    Client:50000 → Server:443
    ```
    
    and
    
    ```
    Server:443 → Client:50000
    ```
    
    are evaluated independently.
    
    That's why **ephemeral ports become important with NACLs**.
    
    ---
    
    ## 🧠 SAA-C03 mental shortcut
    
    When you see:
    
    > **NACL + return traffic + connection failing**
    > 
    
    Immediately think:
    
    ```
    NACL = STATELESS
            ↓
    Request allowed?
            ↓
    What about RESPONSE?
            ↓
    Ephemeral port
    ```
    
    And don't make this mistake:
    
    > ❌ "Ephemeral port is a port on the server."
    > 
    
    Instead:
    
    > ✅ **Ephemeral port is typically a temporary client-side source port for an outgoing connection.**
    > 
    
    One more important point: **50000 is just an example**, not a universally fixed ephemeral port. The OS chooses the actual port from its ephemeral-port range.
    
    Absolutely. The confusing part is usually **“why does the client suddenly use port 50000?”** Let's build it from zero.
    
    ## 1. First: what is a port?
    
    Think of an IP address as a **building address**, and a port as a **specific door inside that building**.
    
    For example:
    
    ```
    Server IP: 10.0.1.20
    
    10.0.1.20:443  → HTTPS door
    10.0.1.20:80   → HTTP door
    10.0.1.20:22   → SSH door
    ```
    
    So:
    
    **IP = Which machine?**
    
    **Port = Which application/service on that machine?**
    
    ---
    
    # 2. Now imagine your browser connects to HTTPS
    
    You open:
    
    ```
    https://example.com
    ```
    
    The server is listening on:
    
    ```
    Server: 10.0.1.20
    Port: 443
    ```
    
    Your computer needs to connect to it.
    
    Your computer says:
    
    > "I'll use one of my own temporary ports to identify this connection."
    > 
    
    For example:
    
    ```
    Client
    10.0.2.10:50000
           |
           |  HTTPS request
           ↓
    Server
    10.0.1.20:443
    ```
    
    Notice:
    
    ```
    10.0.2.10:50000  →  10.0.1.20:443
       Client              Server
    ```
    
    Here:
    
    - `10.0.2.10` = client IP
    - `50000` = **ephemeral port**
    - `10.0.1.20` = server IP
    - `443` = server's HTTPS port
    
    ---
    
    # 3. What exactly is an ephemeral port?
    
    **Ephemeral = temporary**
    
    Your computer doesn't normally have one permanent port for every outgoing connection.
    
    Instead, the operating system temporarily chooses a port.
    
    For example:
    
    ```
    50000
    50001
    50002
    50003
    ...
    ```
    
    So if you open a website, your computer might say:
    
    > "I'll use port 50000 for this connection."
    > 
    
    Another connection might use:
    
    ```
    Client → Server
    50001 → 443
    ```
    
    Another:
    
    ```
    Client → Server
    50002 → 443
    ```
    
    So you can have:
    
    ```
    10.0.2.10:50000 → 10.0.1.20:443
    10.0.2.10:50001 → 10.0.1.20:443
    10.0.2.10:50002 → 10.0.1.20:443
    ```
    
    All going to the same HTTPS server.
    
    ---
    
    # 4. Now comes the important NACL part
    
    This is where AWS exam questions become tricky.
    
    **NACL = stateless.**
    
    That means the NACL doesn't remember:
    
    > "Oh, this response belongs to a connection that I already allowed."
    > 
    
    It evaluates the traffic according to its rules.
    
    Let's see the request.
    
    ### Request
    
    ```
    Client                         Server
    
    10.0.2.10:50000
           ─────────────────────→
                             10.0.1.20:443
    ```
    
    You might have a NACL rule:
    
    ```
    ALLOW
    Destination Port: 443
    ```
    
    So the request is allowed.
    
    But now the server needs to respond.
    
    ---
    
    # 5. Look carefully at the response
    
    The server responds **back to the client's port 50000**.
    
    ```
    Client                         Server
    
    10.0.2.10:50000
           ←─────────────────────
                             10.0.1.20:443
    ```
    
    From the server's perspective:
    
    ```
    Source:
    10.0.1.20:443
    
    Destination:
    10.0.2.10:50000
    ```
    
    So the response is:
    
    ```
    10.0.1.20:443
            ↓
    10.0.2.10:50000
    ```
    
    **50000 is the important part.**
    
    ---
    
    # 6. Why does the response go to 50000?
    
    Because that's the temporary port the client used for this particular connection.
    
    Imagine this conversation:
    
    ```
    Client: "Hey server, I want HTTPS."
            Source port = 50000
    
    Server: "Sure, here's your response."
            Send it back to port = 50000
    ```
    
    It's basically an **answer address**.
    
    If your client had used port `52341` instead:
    
    ```
    Client
    10.0.2.10:52341
           ↓
    Server
    10.0.1.20:443
    ```
    
    The response would be:
    
    ```
    Server
    10.0.1.20:443
           ↓
    Client
    10.0.2.10:52341
    ```
    
    So **the server doesn't respond to some random port**. It responds to the client's ephemeral source port.
    
    ---
    
    # 7. Why does NACL care?
    
    Because NACL is **stateless**.
    
    Suppose:
    
    ```
    Client → Server
    50000 → 443
    ```
    
    You allow:
    
    ```
    Inbound:
    ALLOW TCP 443
    ```
    
    Great.
    
    But the response is:
    
    ```
    Server → Client
    443 → 50000
    ```
    
    The NACL sees:
    
    > Destination port = 50000
    > 
    
    If your NACL doesn't allow that return traffic, it can be blocked.
    
    Therefore, depending on the traffic direction and subnet, you may need NACL rules allowing the **ephemeral port range** used by clients.
    
    ---
    
    # 8. The easiest way to remember it
    
    Forget the complicated definition for a moment.
    
    Remember this story:
    
    ```
    Client picks a temporary port
                 ↓
            "50000"
                 ↓
    Client: 50000 → Server: 443
                 ↓
    Server responds
                 ↓
    Server: 443 → Client: 50000
    ```
    
    ### Therefore:
    
    > **Ephemeral port = temporary port chosen by the client for an outgoing connection.**
    > 
    
    ---
    
    # 9. NACL vs Security Group — VERY important for SAA
    
    This is where you should connect the concept to the exam.
    
    ### Security Group
    
    Security Group is **stateful**.
    
    If:
    
    ```
    Client → Server:443
    ```
    
    is allowed, the response is automatically allowed because SG remembers the connection state.
    
    You generally don't need a separate inbound rule for the ephemeral return port.
    
    ### NACL
    
    NACL is **stateless**.
    
    It doesn't remember the connection.
    
    So:
    
    ```
    Client:50000 → Server:443
    ```
    
    and
    
    ```
    Server:443 → Client:50000
    ```
    
    are evaluated independently.
    
    That's why **ephemeral ports become important with NACLs**.
    
    ---
    
    ## 🧠 SAA-C03 mental shortcut
    
    When you see:
    
    > **NACL + return traffic + connection failing**
    > 
    
    Immediately think:
    
    ```
    NACL = STATELESS
            ↓
    Request allowed?
            ↓
    What about RESPONSE?
            ↓
    Ephemeral port
    ```
    
    And don't make this mistake:
    
    > ❌ "Ephemeral port is a port on the server."
    > 
    
    Instead:
    
    > ✅ **Ephemeral port is typically a temporary client-side source port for an outgoing connection.**
    > 
    
    One more important point: **50000 is just an example**, not a universally fixed ephemeral port. The OS chooses the actual port from its ephemeral-port range.
    
    ### Non-ephemeral ports
    
    These are **fixed/well-known ports used by services/applications**.
    
    Examples:
    
    ```
    Port 80   → HTTP
    Port 443  → HTTPS
    Port 22   → SSH
    Port 53   → DNS
    ```
    
    The server **listens** on these ports waiting for connections.
    
    ### Ephemeral port
    
    A **temporary port chosen by the client** for an outgoing connection.
    
    ```
    Client                         Server
    50000 ───────────────────────→ 443
    (ephemeral)                    (service port)
    ```
    
    Response:
    
    ```
    Client                         Server
    50000 ←─────────────────────── 443
    (ephemeral)                    (service port)
    ```
    
    ### 🧠 Remember
    
    **Server → fixed/service port**
    
    **Client → temporary/ephemeral port**
    
    So:
    
    > `Client:50000 → Server:443`
    > 
    > 
    > **50000 = ephemeral**
    > 
    > **443 = non-ephemeral/service port**
    > 

---

# 17. VPC Peering

VPC Peering lets two VPCs communicate privately over AWS networking.

```
VPC A
10.0.0.0/16
      │
      │ VPC Peering
      │
VPC B
172.16.0.0/16
```

### Requirements

- CIDRs must not overlap
- Route tables must be updated
- Security Groups/NACLs must allow traffic
- Can be cross-account
- Can be cross-region

### Most important:

> **VPC Peering is NOT transitive.**
> 

---

# 18. VPC Peering — Non-transitive

Suppose:

```
VPC A
  │
  │ Peering
  │
VPC B
  │
  │ Peering
  │
VPC C
```

A → B works.

B → C works.

But:

```
A → C ❌
```

You need:

```
A ↔ C
```

another peering connection.

### Memory

> **Peering = 1-to-1 relationship**
> 

---

# 19. VPC Endpoints

### Problem

AWS services such as S3, DynamoDB, Systems Manager, etc. are accessible through AWS endpoints.

You may want a private connection without:

```
Private EC2
   ↓
NAT Gateway
   ↓
IGW
   ↓
Internet
   ↓
AWS Service
```

Instead:

```
Private EC2
     ↓
VPC Endpoint
     ↓
AWS Service
```

### Benefits

- Private connectivity
- Can avoid NAT Gateway for supported AWS services
- No need for public Internet path

---

# 20. VPC Endpoint Types

| Feature | Gateway Endpoint | Interface Endpoint |
| --- | --- | --- |
| Technology | Gateway | AWS PrivateLink |
| Main services | S3, DynamoDB | Most AWS services |
| ENI | ❌ | ✅ |
| Private IP | ❌ | ✅ |
| Security Group | ❌ | ✅ |
| Route table | ✅ | DNS/ENI-based access |
| Pricing | No endpoint hourly/data processing charge | Hourly + data processing |
| Common exam choice | S3/DynamoDB | Most other services |

### Memory

> **Gateway Endpoint → S3 + DynamoDB**
> 

> **Interface Endpoint → Most other AWS services**
> 

---

# 21. Example — Private EC2 → S3

Without endpoint:

```
Private EC2
     ↓
NAT Gateway
     ↓
IGW
     ↓
S3
```

With Gateway Endpoint:

```
Private EC2
     ↓
Gateway Endpoint
     ↓
S3
```

This can eliminate NAT Gateway dependency/cost for that S3 access path.

---

# 22. VPC Flow Logs

VPC Flow Logs capture information about network traffic.

Can be created at:

```
VPC
Subnet
ENI
```

They help answer:

> "Why is this connection being accepted/rejected?"
> 

Flow logs can be delivered to:

- CloudWatch Logs
- Amazon S3
- Kinesis Data Firehose

Can capture traffic associated with various AWS-managed network interfaces/resources too.

---

# 23. Flow Logs — Important Fields

Example:

```
srcaddr
dstaddr
srcport
dstport
protocol
packets
bytes
action
```

Especially remember:

> **ACTION = ACCEPT or REJECT**
> 

Useful for troubleshooting:

```
EC2
 ↓
Connection fails
 ↓
Check VPC Flow Logs
 ↓
ACTION = REJECT
```

Then investigate SG/NACL/routing/etc.

---

# 24. Site-to-Site VPN

Used to connect:

```
On-Premises
     │
     │ Encrypted VPN over Internet
     │
     ▼
AWS VPC
```

Main components:

### Customer Gateway — CGW

Represents the **customer/on-premises side**.

Usually:

```
Customer router/firewall
```

### Virtual Private Gateway — VGW

AWS-side VPN endpoint attached to the VPC.

### Overall:

```
Corporate Network
      │
Customer Gateway
      │
   Internet
      │
Site-to-Site VPN
      │
Virtual Private Gateway
      │
     VPC
```

### Important

> **VPN uses the public Internet but traffic is encrypted.**
> 

---

# 25. AWS VPN CloudHub

Suppose you have multiple offices:

```
Office A
    │
    VPN
    │
    ▼
   VGW
   /  \
 VPN    VPN
 /        \
Office B  Office C
```

This is a **hub-and-spoke VPN architecture**.

Use:

> **AWS VPN CloudHub**
> 

when multiple remote sites need to communicate using VPN connections.

---

# 26. Direct Connect — DX

Direct Connect provides a **dedicated private network connection** between your on-premises network and AWS Direct Connect location.

Unlike VPN:

```
VPN:
On-prem → Internet → AWS
```

Direct Connect:

```
On-prem
   │
Dedicated DX connection
   │
AWS Direct Connect Location
   │
AWS
```

### Why use it?

- More consistent network performance
- High bandwidth
- Large data transfers
- Hybrid cloud
- Predictable connectivity
- Can reduce data transfer costs in suitable architectures

---

# 27. VPN vs Direct Connect

| Feature | Site-to-Site VPN | Direct Connect |
| --- | --- | --- |
| Connection | Public Internet | Dedicated/private connection |
| Encryption | IPsec encrypted | Not encrypted by default |
| Setup | Relatively fast | Usually longer |
| Consistency | Less predictable | More consistent |
| Bandwidth | Lower compared with high-capacity DX options | Very high |
| Cost | Usually cheaper | More expensive |
| Best for | Quick secure connectivity / backup | Enterprise hybrid connectivity |
| Internet involved | Yes | No public Internet path |
| Can combine with other | DX can be combined with VPN | DX + VPN for encryption |

### Important exam clue

> **DX is private but NOT encrypted by default.**
> 

If encryption is required:

```
Direct Connect + Site-to-Site VPN
```

---

# 28. Direct Connect Gateway

Problem:

You have:

```
On-premises
     │
     DX
     │
     ?
   VPC A — Region 1
   VPC B — Region 2
   VPC C — Region 3
```

Use:

> **Direct Connect Gateway**
> 

It allows a Direct Connect connection to reach VPCs across multiple AWS Regions through associated gateways/architectures.

### Memory

> **DX Gateway = one DX connection → multiple VPCs/Regions**
> 

---

# 29. Direct Connect + VPN

For critical workloads:

```
                 ┌── Direct Connect ──► AWS
On-Premises ─────┤
                 └── VPN ─────────────► AWS
```

DX:

> Primary private connection
> 

VPN:

> Backup connection
> 

You can also use DX + VPN to provide encryption over the private DX connectivity architecture
