# AWS SAA-C03: Resource Policies

## 1. What are Resource Policies?

Resource policies are permissions attached directly to an AWS resource that specify who can access that resource and what actions they can perform.

They can allow access to:

- Another AWS account (cross-account access).
- Specific IAM users or roles.
- AWS services or other supported principals.

Simple example: Your company owns an S3 bucket. A partner company needs to read certain files. Instead of sharing your credentials, you add a bucket policy that allows the partner's AWS account to read those files.

## 2. How does it work?

Your AWS account — Account A

Owns the S3 bucket

Resource policy allows a specific principal

S3 bucket policy

Allow only the intended account or role

External AWS account — Account B

Authorized principal accesses permitted objects

The resource policy defines the permitted access. The external account must use an appropriate identity or role to make the request, and other applicable security controls still matter.

## 3. Real-world scenario

Imagine a streaming company stores music files and analytics reports in Amazon S3. A partner company needs to read reports from one specific folder.

Example bucket policy concept:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::222233334444:root"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::streaming-reports/partner/*"
    }
  ]
}
```

Here:

- `Principal`: identifies the external AWS account.
- `Action`: allows reading objects (`s3:GetObject`).
- `Resource`: limits access to objects under `partner/`.
- `Effect: Allow`: grants the specified permission, subject to other applicable policies and controls.

The account ID is illustrative. In practice, you should authorize the intended role or account and keep the policy as restrictive as possible.

## 4. Resource policy vs. identity-based policy

| Feature | Resource-based policy | Identity-based policy |
| --- | --- | --- |
| Attached to | AWS resource | IAM user, group, or role |
| Answers | Who can access this resource? | What can this identity access? |
| Common example | S3 bucket policy | IAM role policy |
| Cross-account access | Can directly grant access to external principals for supported resources | Usually requires permissions on both the caller side and resource side |

Important: Cross-account access is not automatically enabled just because one side grants permission. The precise evaluation depends on the service, principal, and applicable policies.

## 5. Common AWS resource policies

| AWS service | Resource policy example |
| --- | --- |
| Amazon S3 | Bucket policy |
| AWS Lambda | Lambda function resource policy |
| Amazon SQS | Queue access policy |
| Amazon SNS | Topic access policy |
| AWS KMS | Key policy |
| Amazon ECR | Repository policy |
| Amazon API Gateway | API resource policy |

## 6. When should you use a resource policy?

Use one when a question says:

- “Share a resource with another AWS account.”
- “Allow a specific external account to access an S3 bucket.”
- “Grant a service permission to invoke a Lambda function.”
- “Control who can access an SQS queue or SNS topic.”
- “Restrict access to a particular resource or set of objects.”

## 7. Exam confusion-buster

| Option | Correct? | Why |
| --- | --- | --- |
| Create an IAM user with full access and share credentials | ❌ | Unsafe credential sharing and excessive permissions |
| Use resource policies for a specific AWS account | ✅ | Grants controlled access to designated resources |
| Share access keys | ❌ | Credentials should never be shared this way |
| Enable VPC peering | ❌ | Provides network connectivity, not resource-level IAM authorization |

Answer to your reference question: Option 2 — Use Resource Policies to allow a specific AWS account access to the designated resources.

### Memory trick

Identity policy = permissions attached to the identity.

Resource policy = permissions attached to the resource.

For SAA-C03, when the key phrase is “share a resource with another AWS account securely,” think of a resource-based policy, such as an S3 bucket policy.
