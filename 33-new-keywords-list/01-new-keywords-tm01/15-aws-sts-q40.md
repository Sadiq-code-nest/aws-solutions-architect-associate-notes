# What is STS actually?

AWS STS = **AWS Security Token Service**.

STS provides temporary AWS credentials, such as:

```
Access Key ID
Secret Access Key
Session Token
```

These temporary credentials are used for AWS API access.

For example:

```
Application
   ↓
STS
   ↓
Temporary AWS credentials
   ↓
AWS API
```

 

# 15. Important distinction: STS credentials vs RDS auth token

This is worth remembering.

### STS temporary credentials

Used primarily to authenticate/authorize requests to **AWS services/APIs**.

Example:

```
Temporary AWS credentials
        ↓
S3 API
DynamoDB API
EC2 API
etc.
```

### RDS IAM authentication token

Used to authenticate a database connection to an RDS database configured for IAM DB authentication.

```
IAM credentials
       ↓
RDS auth token
       ↓
RDS database connection
```

Therefore don't see:

> "temporary token"
> 

and automatically answer STS.

The **resource/service being accessed matters**.
