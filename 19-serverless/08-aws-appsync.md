# AWS AppSync — Complete SAA-C03 Note

## Images

## 1. What is AWS AppSync?

**AWS AppSync is a fully managed service for building APIs, especially GraphQL APIs, that connect applications to data sources and real-time events.** It can expose data from one or more backends through a single API endpoint and supports real-time updates through WebSockets. ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/what-is-appsync.html?utm_source=chatgpt.com))

The easiest way to remember it:

> **AppSync = API layer + GraphQL + real-time data + multiple data sources**
> 

Instead of a mobile/web application talking directly to several databases/services:

```
Mobile App
   │
   ├──── DynamoDB
   ├──── Lambda
   ├──── Aurora
   └──── HTTP backend
```

you can put AppSync in front:

```
                 Mobile / Web App
                        │
                        ▼
                   AWS AppSync
                  GraphQL API
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
      DynamoDB       Lambda         Aurora
```

This is particularly useful when a frontend needs **different pieces of data from different backend systems**.

---

# 2. Why does AppSync exist?

Imagine a mobile application displaying a user's profile.

The screen needs:

- User information
- Orders
- Product information
- Recommendations

Without GraphQL, the application might need multiple API calls:

```
Mobile App
   │
   ├── GET /user
   ├── GET /orders
   ├── GET /products
   └── GET /recommendations
```

With AppSync/GraphQL:

```
Mobile App
     │
     │ One GraphQL request
     ▼
  AppSync
     │
 ┌───┼────┬──────────┐
 ▼   ▼    ▼          ▼
User Orders Products Recommendations
```

The client can request the fields it needs.

### Brain shortcut

> **Many data sources + one API + flexible data retrieval → AppSync**
> 

---

# 3. What is GraphQL?

This is the most important prerequisite.

**GraphQL is an API query language and runtime where the client specifies the data it wants.**

Compare:

### REST

```
GET /users/123
```

might return:

```json
{
  "id": "123",
  "name": "Rahim",
  "email": "rahim@example.com",
  "address": "...",
  "phone": "...",
  "orders": [...]
}
```

The application might only need:

```
name
email
```

GraphQL lets the client request exactly those fields.

```graphql
query {
  user(id: "123") {
    name
    email
  }
}
```

The response contains those requested fields.

---

# 4. GraphQL's Three Main Operations ⭐⭐⭐⭐⭐

This is extremely important for SAA-C03 recognition.

| Operation | Meaning | Think |
| --- | --- | --- |
| **Query** | Read data | GET |
| **Mutation** | Change data | POST/PUT/DELETE |
| **Subscription** | Receive real-time updates | WebSocket/live updates |

### Memory

> **Query = READ**
> 

> **Mutation = CHANGE**
> 

> **Subscription = LISTEN**
> 

---

# 5. Query

A Query retrieves data.

Example:

```graphql
query {
  getUser(id: "123") {
    name
    email
  }
}
```

Think:

```
Application
    │
    ▼
 AppSync
    │
    ▼
DynamoDB
    │
    ▼
Result
```

### Scenario

A mobile banking application wants to retrieve the current account balance.

**Trigger:**

> "Retrieve/query/read data"
> 

→ **GraphQL Query**

---

# 6. Mutation

Mutation changes data.

Example:

```graphql
mutation {
  castVote(songId: "song123") {
    success
  }
}
```

The mutation could trigger:

```
AppSync
   │
   ▼
Lambda / DynamoDB
   │
   ▼
Database updated
```

### Scenario

A singing competition application allows users to submit votes.

```
Mobile App
    │
    ▼
 AppSync Mutation
    │
    ▼
 DynamoDB
    │
    ▼
Vote stored
```

### Exam clue

> **"Create/update/delete data through GraphQL"**
> 

→ **Mutation**

---

# 7. Subscription ⭐⭐⭐⭐⭐

This is one of the biggest reasons AppSync appears in AWS exam questions.

A subscription lets clients receive **real-time updates**.

AppSync maintains a WebSocket connection for GraphQL subscriptions. ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/aws-appsync-real-time-data.html?utm_source=chatgpt.com))

Example:

```
                 DynamoDB
                    │
                    │ change
                    ▼
                 AppSync
                    │
            WebSocket connections
             ┌──────┼──────┐
             ▼      ▼      ▼
           User A User B User C
```

Suppose a football application displays a live score.

Instead of:

```
App → ask every 5 seconds
App → ask every 5 seconds
App → ask every 5 seconds
```

the application subscribes:

```
App
 │
 │ Subscribe
 ▼
AppSync
 │
 │ WebSocket
 ▼
Live updates
```

When the backend produces an update, subscribed clients receive it.

### Exam clue

**"REAL-TIME"**

**"LIVE UPDATES"**

**"PUSH updates to mobile clients"**

**"Avoid polling"**

→ **AppSync subscription**

---

# 8. Polling vs AppSync Subscription

| Polling | AppSync Subscription |
| --- | --- |
| Client repeatedly asks for updates | Server pushes updates |
| Repeated requests | Persistent WebSocket connection |
| Can create unnecessary traffic | Efficient for live updates |
| Client controls polling interval | AppSync manages real-time connection |
| Not truly real-time | Real-time |

AWS specifically describes subscriptions as an alternative to repeatedly polling for changes. ([AWS Documentation](https://docs.aws.amazon.com/en_en/appsync/latest/devguide/graphql-types.html?utm_source=chatgpt.com))

### Brain shortcut

> **"Keep asking?" → Polling**
> 

> **"Tell me when it changes" → Subscription**
> 

---

# 9. AppSync End-to-End Architecture

A common architecture:

```
                  Mobile Application
                         │
                         ▼
                    AWS AppSync
                         │
                GraphQL API endpoint
                         │
       ┌─────────────────┼─────────────────┐
       │                 │                 │
       ▼                 ▼                 ▼
   DynamoDB           Lambda            Aurora
       │                 │                 │
       └─────────────────┼─────────────────┘
                         │
                         ▼
                    Data returned
                         │
                         ▼
                    Mobile App
```

For real-time:

```
Database / Mutation
       │
       ▼
    AppSync
       │
       │ WebSocket
       ▼
Subscribed clients
```

---

# 10. AppSync Data Sources

AppSync can connect the GraphQL API to backend data sources.

Important SAA-C03 concepts include:

- **Amazon DynamoDB**
- **AWS Lambda**
- **Amazon OpenSearch Service**
- **Amazon Aurora/RDS in supported AppSync integration patterns**
- HTTP endpoints
- Other supported AWS integrations

The key idea is:

> **AppSync is the API layer; the actual data can live elsewhere.**
> 

---

# 11. AppSync + DynamoDB ⭐⭐⭐⭐⭐

This is probably the most important SAA-C03 combination.

```
Mobile App
    │
    ▼
 AppSync
    │
    ▼
DynamoDB
```

Why is this combination powerful?

DynamoDB provides:

- Massive scalability
- Serverless NoSQL database
- High availability
- Flexible schema
- On-demand/provisioned capacity

AppSync provides:

- GraphQL API
- Flexible queries
- Real-time subscriptions
- Authorization

---

## Reference Scenario — Singing Competition

Millions of people vote.

```
Millions of mobile users
          │
          ▼
       AppSync
          │
     Mutation: Vote
          │
          ▼
      DynamoDB
```

Ranking:

```
Mobile App
    │
    ▼
AppSync Query
    │
    ▼
DynamoDB
    │
    ▼
Current ranking
```

Real-time ranking:

```
Vote submitted
     │
     ▼
DynamoDB updated
     │
     ▼
AppSync subscription
     │
     ▼
Connected clients
     │
     ▼
Updated ranking
```

### Why the reference question points toward this architecture

The scenario combines:

**Millions of users**

- 

**Highly scalable database**

- 

**Highly available**

- 

**Real-time ranking**

- 

**Frequent schema changes**

Those requirements map very naturally to:

> **DynamoDB + AppSync**
> 

You asked not to focus on solving the question, so treat that only as a concept-mapping example.

---

# 12. Why DynamoDB + AppSync is such a strong combination

Think of the responsibilities separately:

```
                 Application
                     │
                     ▼
                 AppSync
            API + GraphQL
            + Real-time
                     │
                     ▼
                DynamoDB
          Scalable NoSQL storage
```

### AppSync solves:

**"How does my application communicate with backend data?"**

### DynamoDB solves:

**"Where do I store massive-scale application data?"**

Together:

> **AppSync = API communication**
> 

> **DynamoDB = data storage**
> 

---

# 13. AppSync Schema

GraphQL APIs use a **schema** to define available operations and data types.

Example:

```graphql
type Song {
    id: ID!
    name: String!
    votes: Int
}

type Query {
    getSong(id: ID!): Song
}

type Mutation {
    castVote(songId: ID!): Song
}

type Subscription {
    onVote: Song
}
```

This tells the client:

```
What data exists?
What can I query?
What can I modify?
What can I subscribe to?
```

### Exam clue

> **"GraphQL schema defines API structure/types/operations"**
> 

→ AppSync/GraphQL schema

---

# 14. Frequent Schema Changes

This is an important distinction from traditional relational database thinking.

GraphQL has a defined schema describing the API.

For example:

### Version 1

```
Song
 ├── id
 ├── name
 └── votes
```

Later:

```
Song
 ├── id
 ├── name
 ├── votes
 ├── artist
 └── category
```

The API schema can evolve without requiring the client to retrieve every field.

This flexibility is one reason GraphQL is attractive for applications whose frontend requirements evolve frequently.

---

# 15. Resolver ⭐⭐⭐⭐⭐

A **resolver** tells AppSync how to obtain or manipulate the data for a GraphQL field.

Think:

> **Resolver = bridge between GraphQL field and backend**
> 

Example:

```
GraphQL Query
     │
     ▼
 getSong()
     │
     ▼
  Resolver
     │
     ▼
 DynamoDB
```

Suppose:

```graphql
getSong(id: "123")
```

The resolver determines how to retrieve that song from DynamoDB.

---

# 16. Resolver Types

Conceptually, AppSync can use:

### Unit resolver

One resolver directly interacts with a data source.

```
GraphQL
   ↓
Resolver
   ↓
DynamoDB
```

### Pipeline resolver

Multiple functions/resolver steps execute in sequence.

```
GraphQL
   ↓
Function 1
   ↓
Function 2
   ↓
Function 3
   ↓
Data source
```

Useful when a request needs multiple processing steps.

### Exam clue

> **"Multiple processing steps in one GraphQL request"**
> 

→ **Pipeline resolver**

---

# 17. AppSync Functions

AppSync Functions allow reusable business logic to be executed as part of resolver processing.

Example:

```
Request
  ↓
Validate user
  ↓
Check permissions
  ↓
Fetch DynamoDB data
  ↓
Transform result
  ↓
Response
```

This can be organized using pipeline resolvers.

---

# 18. JavaScript Resolvers

Modern AppSync supports JavaScript-based resolver logic.

You can use JavaScript/TypeScript-oriented tooling for application logic in AppSync rather than putting every small operation into a Lambda function. AWS documents JavaScript and TypeScript support as part of AppSync's capabilities. ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/what-is-appsync.html?utm_source=chatgpt.com))

### Important exam idea

If the question needs simple request/response transformation or resolver logic, **you don't automatically need Lambda**.

---

# 19. AppSync + Lambda

Another common architecture:

```
Mobile App
    │
    ▼
 AppSync
    │
    ▼
 Lambda
    │
    ▼
External API / Database / Business logic
```

Why use Lambda?

When the application requires custom processing.

### Example

A loan application sends:

```
customerId
loanAmount
income
```

AppSync mutation:

```
Submit Loan
     ↓
 AppSync
     ↓
 Lambda
     ↓
Complex validation
     ↓
Database
```

---

# 20. AppSync + HTTP Backend

AppSync can also act as a GraphQL layer in front of HTTP-based backends.

```
Mobile
  ↓
AppSync
  ↓
HTTP API
  ↓
Backend
```

Useful when you already have existing APIs and want a GraphQL interface for clients.

---

# 21. AppSync + Aurora/RDS

For relational workloads, AppSync can expose relational data through supported integrations.

Conceptually:

```
Application
    ↓
AppSync GraphQL
    ↓
Relational backend
    ↓
Aurora / RDS
```

Use relational databases when you actually need:

- SQL
- relational relationships
- joins
- strong relational transactions

Don't choose DynamoDB merely because AppSync is being used.

---

# 22. AppSync Authentication & Authorization ⭐⭐⭐⭐⭐

AppSync supports multiple authorization mechanisms, including:

- **API Key**
- **AWS IAM**
- **Amazon Cognito User Pools**
- **OpenID Connect**
- **AWS Lambda authorization**

You can also configure additional authorization modes. ([AWS Documentation](https://docs.aws.amazon.com/en_en/appsync/latest/devguide/security-authz.html?utm_source=chatgpt.com))

This is highly testable.

---

# 23. AppSync + Cognito User Pools

Common mobile application architecture:

```
Mobile App
    │
    ▼
Cognito User Pool
    │
    │ authenticate user
    ▼
AppSync
    │
    ▼
Backend
```

Cognito User Pool handles:

> **Who is the user?**
> 

AppSync controls:

> **What can that authenticated user access?**
> 

---

# 24. AppSync + IAM

AppSync can use IAM authorization.

The request is authenticated using AWS Signature Version 4 and IAM permissions can control access. ([AWS Documentation](https://docs.aws.amazon.com/en_en/appsync/latest/devguide/security-authz.html?utm_source=chatgpt.com))

Example:

```
EC2 / IAM Role
      │
      ▼
 AppSync
      │
 IAM policy allows
 appsync:GraphQL
```

### Exam clue

> **"AWS service/application using IAM credentials to call AppSync"**
> 

→ **IAM authorization**

---

# 25. AppSync + API Key

API keys provide a simple authorization mechanism.

Useful for scenarios such as:

- Public APIs
- Simple unauthenticated access
- Development/testing

But don't treat API keys as the strongest authentication mechanism.

### Exam clue

> **"Simple public API / temporary API access"**
> 

→ API Key

---

# 26. Lambda Authorization

You can use a Lambda authorizer when authorization logic is custom.

```
Client
   ↓
AppSync
   ↓
Lambda Authorizer
   ↓
Allow / Deny
```

Example:

```
if customer belongs to organization
    → allow

otherwise
    → deny
```

### Exam clue

> **"Custom authorization logic"**
> 

→ **Lambda authorization**

---

# 27. OpenID Connect

AppSync can also integrate with an **OIDC-compliant identity provider**. ([AWS Documentation](https://docs.aws.amazon.com/en_en/appsync/latest/devguide/security-authz.html?utm_source=chatgpt.com))

Think:

> **Existing external identity provider + OIDC → AppSync OIDC authorization**
> 

---

# 28. Multiple Authorization Modes

An AppSync API can support additional authorization modes.

For example:

```
Primary: Cognito
Additional: IAM
```

This can allow different types of clients to access the same API with different authorization mechanisms.

### Example

```
Mobile users
    ↓
Cognito

Internal AWS service
    ↓
IAM

             ↓
          AppSync
```

---

# 29. Fine-Grained Authorization

AppSync isn't limited to:

> "User can access the entire API."
> 

You can implement more granular authorization.

Example:

```
User A
  ↓
Can read own profile

User B
  ↓
Can read own profile

Admin
  ↓
Can read all profiles
```

This can be implemented using authorization directives and resolver logic.

AWS AppSync supports fine-grained access control for GraphQL operations, including subscription-related authorization. ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/security-authorization-use-cases.html?utm_source=chatgpt.com))

---

# 30. AppSync Subscriptions + Authorization

Suppose you have a private chat application.

```
User A
   │
   ▼
Subscription: Room123
```

Only authorized users should receive messages for Room123.

AppSync can apply authorization controls to subscription access.

### Scenario

> **"Users should receive real-time updates only for resources they are authorized to access."**
> 

Think:

**AppSync subscription + authorization**

---

# 31. AppSync Real-Time Architecture

Classic GraphQL subscription model:

```
                  AppSync
                     │
              WebSocket layer
            ┌────────┼────────┐
            ▼        ▼        ▼
          User A   User B   User C
```

One client makes a mutation:

```
User A
  │
  ▼
Mutation
  │
  ▼
AppSync
  │
  ▼
Data changed
  │
  ├────→ User A
  ├────→ User B
  └────→ User C
```

The subscribed clients receive updates.

---

# 32. AppSync Events — Newer Real-Time Capability

There is an important modern AppSync concept:

**AWS AppSync Events.**

AWS introduced AppSync Events for building real-time **Pub/Sub APIs over WebSockets**, allowing event data to be broadcast to potentially millions of subscribers without managing WebSocket infrastructure yourself. ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/aws-appsync-real-time-data.html?utm_source=chatgpt.com))

This is slightly different from traditional GraphQL subscriptions.

### Think:

```
Traditional AppSync GraphQL

GraphQL
 ├── Query
 ├── Mutation
 └── Subscription
```

versus:

```
AppSync Events

Publisher
    ↓
AppSync Events
    ↓
WebSocket channels
    ↓
Many subscribers
```

### Exam brain shortcut

> **GraphQL real-time data → Subscription**
> 

> **Real-time Pub/Sub over WebSockets → AppSync Events**
> 

For SAA-C03 material, don't confuse AppSync's GraphQL subscription capability with generic messaging services such as SNS/SQS.

---

# 33. AppSync vs API Gateway ⭐⭐⭐⭐⭐

This is a major confusion point.

|  | AppSync | API Gateway |
| --- | --- | --- |
| Main API model | **GraphQL** | REST / HTTP / WebSocket |
| Client requests exact fields | **Yes** | Typically endpoint-defined |
| Real-time GraphQL subscriptions | **Yes** | Different WebSocket API model |
| Multiple data sources behind one API | **Strong use case** | Possible through integrations |
| GraphQL | **Core feature** | Not its primary model |
| Mobile apps with flexible data requirements | **Excellent fit** | Also possible |
| GraphQL subscriptions | **Built-in** | Not the same model |

### Exam trigger

**"GraphQL"**

**"Query exactly the fields needed"**

**"real-time GraphQL subscriptions"**

→ **AppSync**

### Brain shortcut

> **API Gateway = API gateway**
> 

> **AppSync = GraphQL API**
> 

---

# 34. AppSync vs DynamoDB

These are not competing services.

```
AppSync
   ↓
API layer

DynamoDB
   ↓
Database
```

### Exam trap

If an answer says:

> "Use AppSync to store the data"
> 

That's conceptually wrong.

AppSync **exposes/manages API access** to data sources.

DynamoDB **stores the data**.

---

# 35. AppSync vs Cognito

Also not direct competitors.

```
Cognito
   ↓
Identity / authentication

AppSync
   ↓
GraphQL API
```

They are often used together.

### Example

```
User
 ↓
Cognito
 ↓
Authenticated
 ↓
AppSync
 ↓
DynamoDB
```

---

# 36. AppSync vs SNS

This distinction matters.

| AppSync | SNS |
| --- | --- |
| GraphQL API | Pub/sub messaging |
| Application data access | Message/event distribution |
| Queries/mutations/subscriptions | Topics/subscriptions |
| Mobile/web application backend | Fan-out notifications |
| GraphQL real-time updates | Notification/event delivery |

### Exam clue

> **"GraphQL API for mobile application"** → AppSync
> 

> **"Publish one message to many subscribers"** → SNS
> 

---

# 37. AppSync vs SQS

| AppSync | SQS |
| --- | --- |
| API/data access | Message queue |
| Client ↔ API | Producer → queue → consumer |
| GraphQL | Queue-based messaging |
| Real-time subscriptions | Asynchronous processing |
| Flexible data queries | Decoupling |

### Brain shortcut

> **Need API → AppSync**
> 

> **Need buffer/queue → SQS**
> 

---

# 38. AppSync vs EventBridge

| AppSync | EventBridge |
| --- | --- |
| API/data access | Event bus |
| GraphQL | Event routing |
| Queries/mutations/subscriptions | Rules/events/targets |
| Application frontend backend interface | Application integration |
| Real-time client data | Event-driven architecture |

### Trigger:

**"Route events between AWS services/SaaS applications"**

→ **EventBridge**

**"GraphQL API"**

→ **AppSync**

---

# 39. AppSync Caching

AppSync supports server-side caching to improve response latency and reduce repeated backend data access. AWS describes optional caching as part of AppSync's capabilities. ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/what-is-appsync.html?utm_source=chatgpt.com))

Conceptually:

```
Client
  ↓
AppSync
  ↓
Cache
  │
  ├── HIT → return quickly
  │
  └── MISS → backend
```

Useful when:

- Data is requested frequently
- Data doesn't change constantly
- Backend calls are expensive

### Exam clue

> **"Reduce repeated backend queries / improve API response latency"**
> 

→ AppSync caching may be appropriate.

---

# 40. AppSync + CloudWatch

AppSync integrates with AWS observability tooling.

You can monitor API behavior and use logging/metrics.

```
AppSync
   │
   └── CloudWatch
          ├── Metrics
          └── Logs
```

For deeper distributed tracing, AppSync also integrates with **AWS X-Ray**. AWS documents built-in monitoring, logging, and tracing capabilities. ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/what-is-appsync.html?utm_source=chatgpt.com))

---

# 41. AppSync + AWS WAF

AppSync can be protected using AWS WAF.

Conceptually:

```
Internet
   │
   ▼
AWS WAF
   │
   ▼
AppSync
   │
   ▼
Backend
```

WAF is useful for filtering malicious web/API requests.

### Trigger

> **"Protect API from common web exploits / rate-based attacks"**
> 

→ **AWS WAF**

Not AppSync itself.

---

# 42. Private AppSync APIs

AppSync supports **Private APIs**, which can restrict API access to traffic originating from configured VPC endpoints. ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/merged-api.html?utm_source=chatgpt.com))

Think:

```
Private VPC
   │
VPC Endpoint
   │
   ▼
Private AppSync API
```

Useful for internal applications where the API should not be publicly reachable.

---

# 43. AppSync Merged APIs

This is more advanced but useful to recognize.

Suppose different teams own different GraphQL APIs:

```
Team A → Customer API
Team B → Order API
Team C → Product API
```

Instead of forcing clients to communicate with three APIs:

```
Client
 ├── Customer API
 ├── Order API
 └── Product API
```

AppSync Merged APIs can combine multiple AppSync source APIs into a single merged API. ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/merged-api.html?utm_source=chatgpt.com))

```
Customer API ──┐
Order API ─────┼──→ Merged AppSync API
Product API ───┘
                     │
                     ▼
                   Client
```

### Exam clue

> **"Multiple AppSync APIs owned independently by different teams but exposed through one unified GraphQL endpoint"**
> 

→ **AppSync Merged API**

---

# 44. Important Merged API Idea

Merged APIs use a **build-time schema composition** approach.

The source APIs remain independently managed, while their schemas/resources can be combined into the merged API. ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/merged-api.html?utm_source=chatgpt.com))

For SAA-C03 recognition, remember only:

> **Multiple independent GraphQL APIs → one unified AppSync endpoint**
> 

---

# 45. AppSync Security — Big Picture

```
                 Client
                   │
          Authentication
                   │
        ┌──────────┼───────────┐
        ▼          ▼           ▼
     Cognito      IAM       OIDC/API Key
        │          │           │
        └──────────┼───────────┘
                   ▼
                AppSync
                   │
            Authorization
                   │
                   ▼
             Data Sources
```

Authorization options include API key, Lambda, IAM, OIDC, and Cognito User Pools. ([AWS Documentation](https://docs.aws.amazon.com/en_en/appsync/latest/devguide/security-authz.html?utm_source=chatgpt.com))

---

# 46. Complete AppSync Concept Map

```
                         AWS APPSYNC
                              │
              ┌───────────────┼────────────────┐
              │               │                │
              ▼               ▼                ▼
           GraphQL         Real-time         Security
              │               │                │
       ┌──────┼──────┐        │        ┌───────┼────────┐
       ▼      ▼      ▼        ▼        ▼       ▼        ▼
     Query Mutation Subscription     Cognito   IAM     OIDC
       │      │        │
       │      │        └── WebSocket
       │      │
       │      └──────── Change data
       │
       └────────────── Read data

                              │
                              ▼
                         Data Sources
                    ┌────────┼─────────┐
                    ▼        ▼         ▼
                DynamoDB   Lambda    Aurora

                              │
                              ▼
                         CloudWatch
                         X-Ray / WAF
```

---

# 47. AppSync — Scenario Recognition Table

| If question says... | Think |
| --- | --- |
| **GraphQL API** | **AppSync** |
| **Mobile application + GraphQL** | **AppSync** |
| **Web/mobile clients need flexible data queries** | **AppSync** |
| **One API accesses multiple data sources** | **AppSync** |
| **Real-time application updates** | **AppSync Subscription** |
| **Live data without polling** | **AppSync Subscription** |
| **WebSocket real-time Pub/Sub** | **AppSync Events** |
| **Millions of subscribers + real-time events** | **AppSync Events** |
| **Store massive scalable NoSQL application data** | **DynamoDB** |
| **GraphQL + scalable NoSQL** | **AppSync + DynamoDB** |
| **Custom backend/business logic** | **AppSync + Lambda** |
| **User authentication + AppSync** | **Cognito + AppSync** |
| **AWS service authentication to AppSync** | **IAM authorization** |
| **Custom authorization logic** | **Lambda authorization** |
| **External OIDC identity provider** | **OIDC authorization** |
| **Simple API-key access** | **API key authorization** |
| **Multiple AppSync APIs → one API** | **Merged APIs** |
| **Reduce repeated backend calls / latency** | **AppSync caching** |
| **Protect API from web attacks** | **AWS WAF + AppSync** |
| **Private API accessible through VPC endpoints** | **Private AppSync API** |

---

# ⭐ 48. The Reference Question — Concept Map

Your practice question contains several important clues.

### "Millions of users"

Think:

```
Need highly scalable backend
```

→ **DynamoDB** is a strong fit for massively scalable NoSQL workloads.

### "Votes"

Think:

```
Write operation
```

→ AppSync **Mutation**

### "Real-time ranking"

Think:

```
Live updates
```

→ AppSync **Subscription**

### "Frequent schema changes"

Think:

```
Flexible API/data model
```

→ GraphQL/AppSync + appropriately designed backend.

So the architecture conceptually becomes:

```
Millions of Users
       │
       ▼
   AWS AppSync
       │
       ├──── Mutation ────► DynamoDB
       │
       └──── Subscription ◄── changes
                                  │
                                  ▼
                          Live ranking updates
```

That architecture is the key mental picture you want to retain.

---

# 49. Most Important AppSync vs Related Services

| Requirement | Pick |
| --- | --- |
| **GraphQL API** | **AppSync** |
| **REST/HTTP API** | **API Gateway** |
| **Queue messages** | **SQS** |
| **Fan-out notifications** | **SNS** |
| **Event routing** | **EventBridge** |
| **Workflow orchestration** | **Step Functions** |
| **Scalable NoSQL database** | **DynamoDB** |
| **Relational SQL database** | **Aurora/RDS** |
| **Authentication/user directory** | **Cognito** |
| **Web attack filtering** | **WAF** |
| **Real-time GraphQL updates** | **AppSync Subscription** |
| **Real-time Pub/Sub WebSocket events** | **AppSync Events** |

---

# 50. Classic SAA-C03 Wrong-Answer Traps

### ❌ AppSync vs DynamoDB

Don't choose AppSync **instead of** DynamoDB when the question asks for database storage.

Think:

> **AppSync = API**
> 

> **DynamoDB = database**
> 

---

### ❌ AppSync vs Cognito

Cognito is not the GraphQL API.

```
Cognito → identity
AppSync → API
```

They can work together.

---

### ❌ AppSync vs SNS

SNS is for **pub/sub messaging and fan-out**.

AppSync is for **GraphQL APIs and real-time application data**.

---

### ❌ AppSync vs SQS

SQS is for **asynchronous message buffering/decoupling**.

AppSync is an **API layer**.

---

### ❌ AppSync vs API Gateway

The biggest clue:

> **GRAPHQL → APPSYNC**
> 

Don't choose API Gateway just because both expose APIs.

---

### ❌ Subscription vs Query

A subscription is not simply another way to perform a normal database query.

Think:

```
Query       → "Give me data."
Mutation     → "Change data."
Subscription → "Tell me when data changes."
```

---

# 🧠 FINAL APPSYNC BRAIN SHORTCUT

Memorize this:

> **APPSYNC = GRAPHQL + REAL-TIME + MULTIPLE DATA SOURCES**
> 

Then remember:

```
QUERY
  ↓
READ

MUTATION
  ↓
WRITE / CHANGE

SUBSCRIPTION
  ↓
REAL-TIME UPDATE
```

And:

```
AppSync
   │
   ├── DynamoDB → scalable NoSQL
   ├── Lambda   → custom logic
   ├── Aurora   → relational SQL
   ├── HTTP     → existing backend
   │
   ├── Cognito  → user authentication
   ├── IAM      → AWS authorization
   ├── WAF      → web protection
   └── CloudWatch/X-Ray → monitoring
```

### 🔥 10-second exam rule

| Keyword | Immediately think |
| --- | --- |
| **GRAPHQL** | 🟢 **AppSync** |
| **REAL-TIME** + GraphQL | 🟢 **AppSync Subscription** |
| **LIVE UPDATES** | 🟢 **Subscription** |
| **MOBILE + GRAPHQL** | 🟢 **AppSync** |
| **ONE API + MULTIPLE DATA SOURCES** | 🟢 **AppSync** |
| **MILLIONS + NoSQL + scalable** | 🟢 **DynamoDB** |
| **GRAPHQL + MILLIONS + REAL-TIME** | 🟢 **AppSync + DynamoDB** |
| **AUTHENTICATION** | 🟢 **Cognito** |
| **CUSTOM AUTHORIZATION** | 🟢 **Lambda authorizer** |
| **QUEUE/BUFFER** | 🟢 **SQS** |
| **FAN-OUT** | 🟢 **SNS** |
| **EVENT ROUTING** | 🟢 **EventBridge** |
| **WORKFLOW** | 🟢 **Step Functions** |

**One sentence to lock into memory:**

> **AppSync gives your mobile/web application a managed GraphQL API that can read/write multiple backend data sources and push real-time updates to clients.** ([AWS Documentation](https://docs.aws.amazon.com/appsync/latest/devguide/what-is-appsync.html?utm_source=chatgpt.com))
>
