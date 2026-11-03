# Amazon S3 Server Access Logging — Exam Night Revision

## 1. What is it? (Plain and simple)

S3 Server Access Logging records detailed information about requests made to an S3 bucket.

Think of it as a security camera for your S3 bucket.

Example: A company wants to know who downloaded `salary.xlsx`, when it happened, which IP address made the request, and whether the request succeeded.

## 2. Architecture & How it works

User / Application

GET salary.xlsx

Source S3 Bucket

Stores the actual files

S3 Server Access Logging

Records request details

Destination S3 Bucket

Stores log files for later analysis

How it works: Enable logging on the source bucket, configure a destination bucket, and S3 delivers log records there.

Exam traps:

- Delivery is best effort, not real-time or guaranteed complete.
- The destination bucket should be in the same AWS account and Region.
- Avoid using the same bucket as source and destination; it can create a logging loop.
- The destination bucket cannot have Requester Pays or S3 Object Lock enabled.

## 3. What information does a server access log contain?

| Information | Example |
| --- | --- |
| Requester / identity | Who made the request, if identifiable |
| Source IP address | `203.0.113.10` |
| Request time | When the request happened |
| Operation | `REST.GET.OBJECT` |
| Object key | `salary.xlsx` |
| HTTP status | `200` success, `403` forbidden, `404` not found |
| Error code | `AccessDenied` |
| Bytes sent / object size | Amount of data transferred |
| Request duration | Total time and turnaround time |
| Referrer / user agent | Referring page and client information |

Scenario: Unexpected downloads are detected. Check the logs for request times, IP addresses, object names, requesters, and download activity.

Note: Some fields may be unavailable, and a requester is not always identifiable by a human name.

## 4. Server Access Logging vs CloudTrail vs Event Notifications vs Requester Pays

| Feature | Main purpose | Exam trigger phrase |
| --- | --- | --- |
| S3 Server Access Logging | Detailed records of bucket requests | Request IP, object key, HTTP status, referrer, request duration |
| AWS CloudTrail | Records AWS API activity and the identity/context of API calls | Audit who performed AWS API actions; track S3 object-level API activity using data events |
| S3 Event Notifications | Triggers actions when specified events occur | Automatically invoke Lambda, send to SQS/SNS, or EventBridge for processing |
| Requester Pays | Makes the requester pay eligible request and data-transfer costs | Share data while charging the requester rather than the bucket owner |

### Example scenarios

- “Find the IP that downloaded a file and the request time.” → Server Access Logging
- “Audit which IAM identity called `PutObject`.” → CloudTrail; enable S3 data events for object-level activity
- “Run Lambda whenever an image is uploaded.” → S3 Event Notifications
- “Let external users download datasets and pay eligible access costs themselves.” → Requester Pays

## 5. Memory trick

“Logs – Trails – Triggers – Transfer cost”

- Logs = detailed S3 request records
- Trails = CloudTrail API audit history
- Triggers = Event Notifications
- Transfer cost = Requester Pays

## 6. Final confusion-buster table

| If the question says… | Choose |
| --- | --- |
| Detailed request logs, IP, referrer, latency | Server Access Logging |
| IAM identity, API history, audit trail | CloudTrail |
| React automatically to object uploads/deletions | Event Notifications |
| Requester should pay eligible access costs | Requester Pays |
| Need immediate event-driven processing | Event Notifications, not access logs |
| Need object-level API audit events | CloudTrail data events |
| Need logs delivered to S3 for later analysis | Server Access Logging |

Last-minute exam rule: Server Access Logging tells you about bucket requests; CloudTrail audits AWS API activity; Event Notifications trigger work; Requester Pays changes who pays eligible access costs.
