# Route 53 Health Checks — SAA-C03 Complete Notes

This is one of the most important Route 53 topics because it connects directly to:

- Failover Routing
- Disaster Recovery (DR)
- High Availability
- Multi-Region Architectures

---

# First: Why Do We Need Health Checks?

Imagine:

```
www.company.com
       ↓
EC2 Server
```

The server crashes.

Without health checks:

```
User
 ↓
DNS returns server IP
 ↓
Server dead
 ↓
Website down
```

Route 53 doesn't know the server died.

---

With Health Checks:

```
Health Check
      ↓
Server unhealthy
      ↓
Route53 detects failure
      ↓
DNS returns backup server
```

This is called:

# Automated DNS Failover

---

# Important Exam Statement

## Route 53 DOES NOT MONITOR TRAFFIC

Route 53 monitors:

```
Endpoint Health
```

and decides:

```
Should I return this DNS record?
```

---

# Three Types of Health Checks

Route 53 supports:

```
1. Endpoint Health Check

2. Calculated Health Check

3. CloudWatch Alarm Health Check
```

Let's understand each.

---

# Type 1: Endpoint Health Check

Most common.

Route53 directly checks:

```
Website
Server
Load Balancer
Public API
Public IP
```

Example:

```
www.company.com
      ↓
ALB
      ↓
EC2
```

Health check continuously tests:

```
https://www.company.com/health
```

---

# How Does It Work Internally?

AWS has about 15+ health checker locations worldwide.

Example:

```
US
Brazil
Ireland
Singapore
Tokyo
Sydney
...
```

Each checker independently tests your endpoint.

```
Checker #1
     ↓
Healthy

Checker #2
     ↓
Healthy

Checker #3
     ↓
Healthy

Checker #4
     ↓
Unhealthy
```

---

# Why Many Checkers?

Suppose:

```
Tokyo checker loses internet
```

That doesn't mean:

```
Your website is down
```

Maybe only Tokyo has a problem.

Therefore AWS uses many checkers globally.

---

# Health Check Flow

```
Health Checker
      ↓
HTTP Request
      ↓
Endpoint
      ↓
Response
      ↓
Healthy/Unhealthy
```

---

# Supported Protocols

Route53 supports:

```
HTTP
HTTPS
TCP
```

---

## HTTP

Checks:

```
http://website.com
```

---

## HTTPS

Checks:

```
https://website.com
```

---

## TCP

Only checks:

```
Can I open the TCP connection?
```

Example:

```
TCP 443
TCP 3306
TCP 5432
```

Useful for databases or custom services.

---

# Health Check Interval

Default:

```
30 seconds
```

Meaning:

Every 30 seconds health checkers test the endpoint.

---

Can be:

```
10 seconds
```

More frequent.

Higher cost.

---

# Healthy / Unhealthy Threshold

Default:

```
3 consecutive successes
```

to become healthy.

And:

```
3 consecutive failures
```

to become unhealthy.

---

Example

Server temporarily glitches:

```
Fail
Pass
Pass
```

AWS won't immediately mark it unhealthy.

This prevents false alarms.

---

# 18% Rule (Exam Favorite)

Many students memorize this without understanding.

Suppose:

```
15 health checkers
```

AWS considers endpoint healthy if:

```
More than 18%
```

report healthy.

---

Example

15 checkers.

Healthy reports:

```
12 healthy
3 unhealthy
```

Result:

```
Healthy
```

---

Example

15 checkers.

Healthy reports:

```
2 healthy
13 unhealthy
```

Result:

```
Unhealthy
```

---

Why?

Because AWS trusts the majority of global observations.

---

# HTTP Status Codes

Health check passes only if:

```
2xx
or
3xx
```

---

Healthy:

```
200 OK
201 Created
301 Redirect
302 Redirect
```

---

Unhealthy:

```
404
500
503
```

---

Example

```
GET /health
```

returns:

```
200 OK
```

Healthy.

---

Returns:

```
500 Internal Server Error
```

Unhealthy.

---

# String Matching

Very important.

Health checks can inspect:

```
First 5120 bytes
```

of the response.

Example:

Server returns:

```html
Status: OK
```

Health Check expects:

```
OK
```

Found:

```
Healthy
```

---

Server returns:

```html
Database Error
```

Expected string not found.

```
Unhealthy
```

---

Real-world example:

Your web server is alive.

But database is dead.

Without string matching:

```
Health Check = Healthy
```

Wrong.

---

With string matching:

```
Page says DB Error
```

Health Check fails.

Much more accurate.

---

# Security Group / Firewall Requirement

Health Checkers are outside your VPC.

Important exam point.

AWS Health Checkers are on the public internet.

```
AWS Checker
      ↓
Public Internet
      ↓
Your Endpoint
```

Your firewall/security group must allow:

```
Route53 Health Checker IP ranges
```

Otherwise:

```
Health Check
     ↓
Blocked
     ↓
Unhealthy
```

---

# Why Private Resources Don't Work

Suppose:

```
Private Subnet
      ↓
Private EC2
```

No public IP.

Health checker tries:

```
Internet
     ↓
Private EC2
```

Cannot reach it.

---

Result:

```
Health Check fails
```

---

# Exam Rule

Endpoint Health Checks only work for:

```
Public Resources
```

---

# Type 2: Calculated Health Checks

This is Health Check of Health Checks.

---

Example

You have:

```
EC2 #1
EC2 #2
EC2 #3
```

Health checks:

```
HC1
HC2
HC3
```

Then:

```
Parent Health Check
```

monitors:

```
HC1
HC2
HC3
```

---

Visual

```
Parent HC
     ↓
--------------
|    |      |
HC1 HC2   HC3
```

---

# Why Use It?

Suppose:

```
3 web servers
```

You want website healthy if:

```
At least 2 survive
```

---

Rule:

```
2 of 3 must pass
```

---

Example

Results:

```
HC1 = Healthy
HC2 = Healthy
HC3 = Failed
```

Parent:

```
Healthy
```

---

Example

```
HC1 = Failed
HC2 = Failed
HC3 = Healthy
```

Parent:

```
Unhealthy
```

---

# Logical Operations

Supports:

```
AND
OR
NOT
```

---

AND Example

```
Web healthy
AND
Database healthy
```

Both required.

---

OR Example

```
US Region healthy
OR
EU Region healthy
```

One is enough.

---

NOT Example

```
NOT maintenance-mode
```

Useful during upgrades.

---

# Maintenance Example

Suppose:

```
Server1
Server2
Server3
```

You intentionally shut down:

```
Server1
```

Without calculated checks:

```
Health Check fails
Failover triggered
```

Bad.

---

Calculated check can say:

```
Need only 2 of 3 healthy
```

No failover.

Maintenance succeeds.

---

# Type 3: CloudWatch Alarm Health Checks

This is the most important advanced concept.

---

Problem:

Route53 cannot reach:

```
Private EC2
Private ALB
Private RDS
On-Prem Server
```

because health checkers are outside VPC.

---

Solution

Use:

```
CloudWatch Metric
      ↓
CloudWatch Alarm
      ↓
Route53 Health Check
```

---

Example

Private RDS.

Health checker cannot access:

```
RDS
```

---

But CloudWatch knows:

```
CPU
Connections
Storage
Replication
```

---

Create Alarm:

```
DatabaseDownAlarm
```

Then:

```
Route53 Health Check
       ↓
Monitors Alarm
```

---

If alarm enters:

```
ALARM
```

Route53 marks endpoint:

```
Unhealthy
```

---

# Real AWS Example

Private RDS Database:

```
RDS
 ↓
CloudWatch Metric
 ↓
Alarm
 ↓
Route53 Health Check
 ↓
DNS Failover
```

---

# Exam Scenarios

### Public Website

Use:

```
Endpoint Health Check
```

---

### Multi-server dependency

Use:

```
Calculated Health Check
```

---

### Private EC2

Use:

```
CloudWatch Alarm Health Check
```

---

### Private RDS

Use:

```
CloudWatch Alarm Health Check
```

---

### On-premises server behind firewall

Use:

```
CloudWatch Alarm Health Check
```

---

# Final Exam Cheat Sheet

| Feature | Endpoint HC | Calculated HC | CloudWatch HC |
| --- | --- | --- | --- |
| Public Resources | ✅ | Uses child checks | Indirect |
| Private Resources | ❌ | ❌ | ✅ |
| HTTP/HTTPS/TCP | ✅ | ❌ | ❌ |
| AND/OR Logic | ❌ | ✅ | ❌ |
| Monitor Alarms | ❌ | ❌ | ✅ |
| Used in Failover Routing | ✅ | ✅ | ✅ |

# 30-Second Exam Memory

**Endpoint Health Check** = "Route53 directly checks the server."

**Calculated Health Check** = "Health check of multiple health checks."

**CloudWatch Health Check** = "Route53 checks a CloudWatch alarm instead of the actual server, which allows monitoring private resources."
