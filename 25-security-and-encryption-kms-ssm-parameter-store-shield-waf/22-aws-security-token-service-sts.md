# AWS Security Token Service (STS)

AWS STS provides **temporary security credentials**, so employees don't need permanent IAM credentials.

AWS **Security Token Service (STS)** issues **temporary security credentials** consisting of an access key ID, secret access key, and session token. These credentials can be obtained by **assuming an IAM role**, making STS the direct service for granting temporary access to the production account.

The key requirement is:

> **Temporary + IAM role + short-lived security credentials**
> 

That points directly to **AWS STS**.
