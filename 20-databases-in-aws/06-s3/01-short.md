# Short

Amazon S3 - Summary

· S3 is a ... key / value store for objects
. Great for bigger objects, not so great for many small objects
. Serverless, scales infinitely, max object size is 5 TB, versioning capability
. Tiers: S3 Standard, S3 Infrequent Access, S3 Intelligent, S3 Glacier + lifecycle policy
· Features: Versioning, Encryption, Replication, MFA-Delete, Access Logs ...
. Security: IAM, Bucket Policies, ACL, Access Points, Object Lambda, CORS, Object/Vault Lock
· Encryption: SSE-S3, SSE-KMS, SSE-C, client-side, TLS in transit, default encryption
. Batch operations on objects using S3 Batch, listing files using S3 Inventory
. Performance: Multi-part upload, S3 Transfer Acceleration, S3 Select
. Automation: S3 Event Notifications (SNS, SQS, Lambda, EventBridge)
. Use Cases: static files, key value store for big files, website hosting
