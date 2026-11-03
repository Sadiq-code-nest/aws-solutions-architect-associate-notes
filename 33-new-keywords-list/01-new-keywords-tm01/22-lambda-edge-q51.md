# Lambda@Edge

## What is Lambda@Edge?

Lambda@Edge allows you to run Lambda functions at **CloudFront edge locations** in response to CloudFront events.

Instead of:

```
User
 ↓
CloudFront
 ↓
Origin
 ↓
Authentication
```

you can potentially do:

```
User
 ↓
Nearby CloudFront Edge
 ↓
Lambda@Edge
 ↓
Authentication
```

So the authentication logic executes much closer to the user.
