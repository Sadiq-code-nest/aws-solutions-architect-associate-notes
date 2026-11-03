# 24. Field-Level Encryption

CloudFront **Field-Level Encryption** protects specific sensitive fields in HTTP POST requests.

Example:

```
POST /payment

Name: John
Email: john@example.com
CreditCard: 4111...
```

You may encrypt only:

```
CreditCard
```

rather than encrypting the entire request payload.

### Scenario

A web application sends:

```
Customer information
+
Credit card number
```

The company wants only specific sensitive fields encrypted before they reach the origin.

→ **CloudFront Field-Level Encryption**

---

# 25. HTTPS / SSL/TLS

CloudFront supports HTTPS.

There are two separate connections to think about:

```
Viewer
   ↓ HTTPS
CloudFront
   ↓ HTTPS/HTTP
Origin
```

You can configure the protocol between CloudFront and the origin according to the origin protocol settings.

### Viewer Protocol Policy

Common choices:

| Policy | Meaning |
| --- | --- |
| HTTP and HTTPS | Both accepted |
| Redirect HTTP to HTTPS | HTTP users redirected to HTTPS |
| HTTPS only | HTTP requests rejected |

### Exam clue

> "Force users to use HTTPS."
> 

→ **Viewer Protocol Policy = Redirect HTTP to HTTPS** or HTTPS-only depending on requirement.

---

# 26. SSL Certificate with CloudFront

CloudFront can use an SSL/TLS certificate managed through:

**AWS Certificate Manager (ACM)**.

Example:

```
www.company.com
       ↓
CloudFront
       ↓
ACM Certificate
       ↓
HTTPS
```

---

# 27. SNI — Server Name Indication ⭐⭐⭐⭐

SNI allows CloudFront to support multiple HTTPS domains/certificates.

Example:

```
CloudFront
│
├── www.company.com → Certificate A
│
└── www.shop.com    → Certificate B
```

The client indicates the hostname during the TLS handshake.

CloudFront selects the appropriate certificate.

SNI is supported with newer-generation load balancers such as ALB/NLB and CloudFront itself; CLB is the old-generation exception in your course material.

---

# 28. Origin Protocol Policy

This controls:

> **CloudFront → Origin**
> 

Example:

```
Viewer → CloudFront → Origin
```

Viewer may use:

```
HTTPS
```

while CloudFront can potentially communicate with a custom origin using:

```
HTTP
```

depending on configuration.

### Common policies

```
HTTP only
HTTPS only
Match viewer
```

For custom origins:

| Setting | Meaning |
| --- | --- |
| HTTP Only | CloudFront → origin over HTTP |
| HTTPS Only | CloudFront → origin over HTTPS |
| Match Viewer | HTTP viewer → HTTP origin; HTTPS viewer → HTTPS origin |

---

# 29. HTTP Headers ⭐⭐⭐⭐⭐

An HTTP request is not just:

```
GET /image.jpg
```

It can contain headers:

```
GET /image.jpg

Host: example.com
User-Agent: Chrome
Accept: image/webp,image/jpeg
Authorization: ...
```

### HTTP Header

A header is **metadata/information about the request or response**.

Examples:

| Header | Purpose |
| --- | --- |
| `Host` | Requested hostname |
| `User-Agent` | Client/browser/device information |
| `Accept` | Content formats client can accept |
| `Authorization` | Authentication information |
| `Cookie` | Client cookie data |
| `Cache-Control` | Caching instructions |
| `Content-Type` | Type of content |
| `X-Forwarded-For` | Original client IP information |

---

# 30. User-Agent Header ⭐⭐⭐⭐⭐

`User-Agent` tells the server information about the client software/device.

Example:

```
User-Agent: Mozilla/5.0 ... Chrome ...
```

A request might indicate:

```
Chrome
Safari
Firefox
Mobile device
Desktop
```

### Why useful?

An application can make decisions based on it.

Example:

```
User-Agent supports WebP?
       ↓
YES → WebP
NO  → JPEG
```

---

# 31. CloudFront Response Headers Policy

This is an important distinction from your practice question.

A **Response Headers Policy** controls HTTP **response headers** sent from CloudFront to the viewer.

Example:

```
Cache-Control: ...
Strict-Transport-Security: ...
Access-Control-Allow-Origin: *
X-Custom-Tracking: ABC123
```

You can use it to add security/CORS/custom response headers without changing the origin application.

### Common uses

- CORS headers
- Security headers
- Custom headers
- HSTS
- Remove unwanted headers
- Add tracking information

### 🚨 Important exam distinction

**Response Headers Policy does NOT convert JPEG → WebP.**

It controls **headers**, not the response body/content transformation.

---

# 32. User-Agent + Image Format Scenario

Suppose:

```
User → CloudFront
```

Request:

```
User-Agent: Modern browser
Accept: image/webp
```

Your application may decide:

```
WebP supported
      ↓
return WebP
```

Another browser:

```
WebP unsupported
      ↓
return JPEG
```

CloudFront can participate in this type of logic using edge compute/custom logic, but you must carefully configure the **cache key** if the response differs based on request headers. AWS specifically notes that if a request value affects the returned response, that value should be represented appropriately in the cache key.

---

# 33. CloudFront Functions ⭐⭐⭐⭐⭐

**CloudFront Functions** run lightweight JavaScript at CloudFront edge locations.

Good for:

- URL rewriting
- Redirects
- Header manipulation
- Simple authentication/authorization logic
- Request manipulation
- Response manipulation

### Example

```
/user/123
   ↓
CloudFront Function
   ↓
/users/123
```

Very lightweight edge logic.

---

# 34. Lambda@Edge ⭐⭐⭐⭐⭐

Lambda@Edge allows Lambda functions to run closer to CloudFront viewers.

Used for more advanced customization.

Examples:

- Dynamic request manipulation
- Header modification
- Authentication
- A/B testing
- Content personalization
- URL rewriting
- Origin request/response processing

AWS documents Lambda@Edge as a mechanism for customizing CloudFront behavior at the edge.

---

# 35. CloudFront Functions vs Lambda@Edge

| Feature | CloudFront Functions | Lambda@Edge |
| --- | --- | --- |
| Runtime | Lightweight JavaScript | Lambda runtime |
| Complexity | Simple | More advanced |
| Speed | Extremely lightweight | More capable but heavier |
| Viewer request | ✅ | ✅ |
| Viewer response | ✅ | ✅ |
| Origin request | ❌ | ✅ |
| Origin response | ❌ | ✅ |
| Best for | Simple edge logic | Complex edge logic |
| Example | Redirect `/old` → `/new` | Advanced personalization |
| Exam clue | "simple URL/header manipulation" | "complex processing at edge" |

### Easy memory

> **CloudFront Functions = Simple + fast edge logic**
> 

> **Lambda@Edge = More powerful edge logic**
> 

---

# 36. Important Edge Function Events

CloudFront request/response flow:

```
Viewer
  ↓
Viewer Request
  ↓
Cache
  ↓
Origin Request
  ↓
Origin
  ↓
Origin Response
  ↓
Cache
  ↓
Viewer Response
  ↓
Viewer
```

### Lambda@Edge can work at:

1. Viewer Request
2. Viewer Response
3. Origin Request
4. Origin Response

CloudFront Functions primarily operate at viewer request/response stages.

---

# 37. CloudFront + WAF ⭐⭐⭐⭐⭐

**AWS WAF** protects CloudFront applications from web-based attacks.

Architecture:

```
Internet
   ↓
CloudFront
   ↓
AWS WAF
   ↓
Application
```

WAF can inspect requests and use rules for things such as:

- IP addresses
- HTTP headers
- URI
- SQL injection
- XSS
- Rate limiting
- Geographic conditions

### Scenario

A website is receiving:

```
100,000 requests/sec
```

from suspicious sources.

You want to block/rate-limit malicious web requests before they reach the application.

→ **CloudFront + AWS WAF**

---

# 38. CloudFront + Shield ⭐⭐⭐⭐

**AWS Shield** protects against DDoS attacks.

Think:

```
Shield
 ↓
DDoS protection

WAF
 ↓
Web request filtering
```

### Shield Standard

Automatic AWS DDoS protection.

### Shield Advanced

More advanced DDoS protection and additional capabilities/support.

### Exam memory

> **DDoS → Shield**
> 

> **SQL injection / XSS / rate-based web filtering → WAF**
> 

---

# 39. Geo Restriction / Geo Blocking

CloudFront can restrict content by country.

Two concepts:

### Allowlist

```
Allow:
USA
Canada
UK
```

Only those countries can access.

### Blocklist

```
Block:
Country A
Country B
```

### Scenario

A company owns media rights only in:

```
USA + Canada
```

It wants CloudFront to restrict delivery to those countries.

→ **CloudFront geographic restriction**

---

# 40. Geo Restriction vs WAF Geographic Rule

They can both involve countries, but conceptually:

|  | CloudFront Geo Restriction | AWS WAF Geo Rule |
| --- | --- | --- |
| Main purpose | Content access restriction | Web request filtering |
| Scope | CloudFront distribution | WAF Web ACL |
| Example | Only USA can view video | Block requests from country X |
| Exam clue | Content delivery restriction | Security/filtering rule |

---

# 41. Cache Invalidation ⭐⭐⭐⭐⭐

Suppose CloudFront cached:

```
logo.png
```

You update the file in S3.

CloudFront may continue serving the cached version until its cache expires.

You can explicitly invalidate it:

```
Invalidation:
 /logo.png
```

or:

```
/*
```

Then CloudFront fetches fresh content when requested.

### Scenario

You deployed an urgent correction to:

```
index.html
```

but users are still seeing the old version.

→ **CloudFront invalidation**

---

# 42. Better Alternative: Versioned Object Names

Instead of:

```
style.css
```

use:

```
style-v2.css
```

or:

```
style.abc123.css
```

CloudFront sees it as a new object.

### Exam memory

> Frequent deployments → **versioned filenames can reduce reliance on invalidation**
> 

---

# 43. Origin Shield ⭐⭐⭐⭐

Origin Shield adds another centralized caching layer between CloudFront edge locations and your origin.

Conceptually:

```
Users
 ↓
Many Edge Locations
 ↓
Origin Shield
 ↓
Origin
```

Without Origin Shield:

```
Many Edge Locations
       ↓
     Origin
```

With Origin Shield:

```
Many Edge Locations
       ↓
 One centralized cache
       ↓
     Origin
```

### Main purpose

> Reduce origin load and improve cache efficiency, especially when many CloudFront locations request the same content.
> 

---

# 44. CloudFront Compression

CloudFront can compress content for supported requests/content.

Example:

```
Original:
100 KB

Compressed:
20 KB
```

Less data travels over the network.

Benefits:

- Faster delivery
- Lower bandwidth
- Better user experience

Common compression formats include:

```
Gzip
Brotli
```

depending on request/response support.

---

# 45. CloudFront Price Classes

CloudFront has **Price Classes** to control which edge locations are used.

Conceptually:

```
More edge locations
      ↓
Potentially better geographic coverage
      ↓
Potentially higher cost
```

Restricting the price class can reduce cost.

### Exam clue

> "Reduce CloudFront cost by limiting geographic edge locations."
> 

→ **Price Class**

---

# 46. CloudFront Allowed HTTP Methods

You can configure which HTTP methods CloudFront accepts/forwards.

Typical methods:

```
GET
HEAD
OPTIONS
PUT
POST
PATCH
DELETE
```

For a static website, generally:

```
GET + HEAD
```

are enough.

For an API:

```
GET
POST
PUT
PATCH
DELETE
OPTIONS
```

may be required.

---

# 47. CloudFront Caching and HTTP Methods

Usually:

```
GET / HEAD
```

are the main cacheable methods.

Other methods such as:

```
POST
PUT
DELETE
PATCH
```

are generally forwarded rather than cached.

AWS documents CloudFront caching behavior for S3 origins, including that GET/HEAD responses are cached and other methods aren't cached.

---

# 48. CORS with CloudFront

**CORS = Cross-Origin Resource Sharing.**

Example:

```
website-a.com
     ↓
requests
     ↓
api.website-b.com
```

Browser security may restrict cross-origin requests.

CloudFront can work with CORS headers such as:

```
Access-Control-Allow-Origin
Access-Control-Allow-Methods
Access-Control-Allow-Headers
```

A **Response Headers Policy** can be used to add CORS response headers.

---

# 49. CloudFront Logging

Two important logging concepts:

### Standard Access Logs

Records CloudFront requests.

Useful for:

- Traffic analysis
- Troubleshooting
- Auditing
- Access analysis

### Real-Time Logs

More detailed/near-real-time request information.

Useful for:

- Real-time monitoring
- Security analysis
- Troubleshooting
- Detailed request analysis

CloudFront distribution settings support both standard and real-time access logging.

---

# 50. CloudFront Monitoring

CloudFront integrates with:

**Amazon CloudWatch**

You can monitor metrics such as:

- Requests
- Bytes downloaded
- Error rates
- Cache hit rate
- Origin latency
- 4xx/5xx errors

Example:

```
CloudFront
    ↓
CloudWatch
    ↓
Alarm
    ↓
SNS notification
```
