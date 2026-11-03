# AWS SAM — AWS SAA-C03 Complete Concept

## 1. What is AWS SAM?

**AWS SAM (Serverless Application Model)** is an **open-source framework from AWS** used to **build, define, package, deploy, and manage serverless applications**.

The easiest way to remember:

> **AWS SAM = simpler way to define and deploy serverless applications using Infrastructure as Code.**
> 

Instead of manually creating:

```
API Gateway
     ↓
Lambda
     ↓
DynamoDB
     ↓
IAM Role
     ↓
CloudWatch
```

you can describe the whole serverless application in a **SAM template**.

```
SAM Template
     ↓
AWS SAM
     ↓
CloudFormation
     ↓
AWS Resources
```

---

# 2. What does SAM actually do?

SAM does **not replace Lambda, API Gateway, DynamoDB, or CloudFormation**.

Instead, it makes working with serverless resources easier.

| Service | Role |
| --- | --- |
| **AWS SAM** | Define/build/deploy serverless applications |
| **AWS CloudFormation** | Actually provisions/manages the infrastructure |
| **AWS Lambda** | Runs your code |
| **API Gateway** | Provides HTTP/API endpoint |
| **DynamoDB** | Serverless database |
| **S3** | Object storage / deployment artifacts |
| **CloudWatch** | Logs and monitoring |

### Important exam concept

> **SAM is an abstraction/tool on top of CloudFormation.**
> 

---

# 3. Why do we need SAM?

Imagine you manually create a serverless application.

You might need to configure:

```
Lambda function
IAM execution role
API Gateway
API route
DynamoDB table
Lambda permissions
CloudWatch Logs
S3 deployment artifacts
```

That can become complicated.

With SAM:

```
sam-template.yaml
       ↓
   SAM CLI
       ↓
CloudFormation
       ↓
All resources deployed
```

You define the application declaratively.

---

# 4. SAM uses YAML/JSON templates

A SAM application normally has a file such as:

```
template.yaml
```

Inside it, you define resources.

For example:

```yaml
AWSTemplateFormatVersion: '2010-09-09'

Transform: AWS::Serverless-2016-10-31

Resources:

  HelloFunction:
    Type: AWS::Serverless::Function
    Properties:
      Runtime: python3.12
      Handler: app.lambda_handler
      CodeUri: hello/
```

The important part is:

```yaml
Transform: AWS::Serverless-2016-10-31
```

This tells CloudFormation:

> "Interpret this template using AWS SAM."
> 

---

# 5. SAM + Lambda

This is the most common relationship.

You define:

```yaml
Type: AWS::Serverless::Function
```

SAM then handles much of the underlying Lambda configuration.

Example:

```yaml
Resources:

  HelloFunction:
    Type: AWS::Serverless::Function
    Properties:
      Runtime: python3.12
      Handler: app.lambda_handler
      CodeUri: hello/
```

Conceptually:

```
SAM Template
     │
     ▼
AWS::Serverless::Function
     │
     ▼
AWS Lambda Function
```

---

# 6. SAM + API Gateway

SAM can also create an API Gateway API connected to Lambda.

Example:

```yaml
Resources:

  HelloFunction:
    Type: AWS::Serverless::Function
    Properties:
      Runtime: python3.12
      Handler: app.lambda_handler
      CodeUri: hello/
      Events:

        HelloApi:
          Type: Api
          Properties:
            Path: /hello
            Method: get
```

Now the architecture becomes:

```
Client
   │
   │ GET /hello
   ▼
API Gateway
   │
   ▼
Lambda
   │
   ▼
Response
```

SAM automatically creates the necessary API Gateway integration and Lambda permission.

---

# 7. Real-life example

Imagine an online bookstore.

The application has:

```
GET /books
POST /books
GET /books/{id}
```

You could create:

```
                 API Gateway
                /     |      \
               /      |       \
          GET books  POST    GET /{id}
              \        |       /
               \       |      /
                  Lambda
                     │
                     ▼
                 DynamoDB
```

Instead of manually configuring every component, SAM can define the serverless architecture in one template.

---

# 8. SAM + DynamoDB

SAM can define DynamoDB resources too.

Example:

```yaml
Resources:

  BooksTable:
    Type: AWS::Serverless::SimpleTable
    Properties:
      PrimaryKey:
        Name: id
        Type: String
```

Then your Lambda can interact with the table.

Architecture:

```
Client
  ↓
API Gateway
  ↓
Lambda
  ↓
DynamoDB
```

SAM defines the infrastructure.

Lambda contains the application logic.

---

# 9. Complete simple SAM architecture

A typical application might look like:

```
                    Internet
                       │
                       ▼
                 API Gateway
                       │
                       ▼
                    Lambda
                       │
                       ▼
                   DynamoDB

                       +

                  CloudWatch
                     Logs
```

And SAM sits on the **deployment/infrastructure-definition side**:

```
                 template.yaml
                       │
                       ▼
                      SAM
                       │
                       ▼
                CloudFormation
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
     API Gateway    Lambda       DynamoDB
```

---

# 10. SAM CLI

**AWS SAM CLI** is the command-line tool used to develop and deploy SAM applications.

Common commands:

| Command | Purpose |
| --- | --- |
| `sam init` | Create a new SAM application |
| `sam build` | Build the application |
| `sam local` | Run/test functions locally |
| `sam deploy` | Deploy application |
| `sam package` | Package application artifacts |
| `sam validate` | Validate SAM template |
| `sam logs` | Retrieve Lambda logs |

For SAA-C03, you don't need to memorize every command.

The important concept is:

> **SAM CLI helps developers build, test, package, and deploy serverless applications.**
> 

---

# 11. `sam init`

You can start a new project:

```bash
sam init
```

SAM creates a project structure such as:

```
my-serverless-app/
│
├── template.yaml
│
├── hello/
│   ├── app.py
│   └── requirements.txt
│
└── events/
```

Then you implement your Lambda code.

---

# 12. `sam build`

When you run:

```bash
sam build
```

SAM prepares the application for deployment.

Conceptually:

```
Source Code
    ↓
sam build
    ↓
Build artifacts
    ↓
Ready for deployment
```

---

# 13. `sam deploy`

Then:

```bash
sam deploy
```

SAM deploys the application using CloudFormation.

Conceptually:

```
template.yaml
      +
Lambda code
      ↓
   SAM CLI
      ↓
CloudFormation
      ↓
AWS resources
```

---

# 14. SAM Local Testing

One of SAM's major benefits is **local development/testing**.

For example:

```bash
sam local start-api
```

You can run your API locally before deploying it.

Conceptually:

```
Developer Laptop

API request
     ↓
SAM Local
     ↓
Lambda function locally
```

This helps developers test serverless applications without deploying every small change to AWS.

---

# 15. SAM Local Invoke

You can also invoke a Lambda locally.

```bash
sam local invoke
```

Conceptually:

```
Test Event
    ↓
SAM CLI
    ↓
Lambda code running locally
```

Useful for development and debugging.

---

# 16. SAM + Event Sources

SAM isn't limited to API Gateway.

A Lambda function can be triggered by many AWS services.

For example:

### S3 → Lambda

```
User
 ↓
S3 Upload
 ↓
S3 Event
 ↓
Lambda
```

SAM can define that relationship.

---

### SQS → Lambda

```
Application
     ↓
    SQS
     ↓
   Lambda
```

SAM can define the Lambda/SQS event source mapping.

---

### EventBridge → Lambda

```
AWS Event
     ↓
EventBridge
     ↓
Lambda
```

SAM can define the EventBridge rule and Lambda integration.

---

### SNS → Lambda

```
SNS
 │
 ├── Lambda
 ├── SQS
 └── Email
```

SAM can define the Lambda subscription.

---

# 17. Common SAM event sources

| Event source | Typical architecture |
| --- | --- |
| API Gateway | API → Lambda |
| S3 | S3 event → Lambda |
| SQS | Queue → Lambda |
| SNS | SNS → Lambda |
| EventBridge | Event → Lambda |
| CloudWatch/EventBridge schedule | Schedule → Lambda |
| Kinesis | Stream → Lambda |
| DynamoDB Streams | Stream → Lambda |

---

# 18. SAM vs CloudFormation

This is another important exam distinction.

|  | **AWS SAM** | **CloudFormation** |
| --- | --- | --- |
| Purpose | Simplify serverless application deployment | General AWS infrastructure as code |
| Scope | Primarily serverless applications | Almost all AWS infrastructure |
| Template | YAML/JSON | YAML/JSON |
| Deployment engine | CloudFormation | CloudFormation |
| Serverless abstractions | ✅ | Can define resources directly |
| Lambda | ✅ Easy abstraction | ✅ |
| API Gateway | ✅ Easy integration | ✅ |
| DynamoDB | ✅ | ✅ |
| EC2 | Not its main focus | ✅ |
| VPC | Not its main focus | ✅ |
| Complexity | Simpler for serverless | More general/flexible |

### Memory:

> **CloudFormation = general infrastructure IaC**
> 

> **SAM = serverless-focused abstraction built on CloudFormation**
> 

---

# 19. SAM vs AWS CDK

Another potential confusion.

|  | **SAM** | **AWS CDK** |
| --- | --- | --- |
| Main idea | Serverless IaC framework | Infrastructure defined using programming languages |
| Typical format | YAML/JSON | TypeScript, Python, Java, C#, etc. |
| Built on | CloudFormation | CloudFormation |
| Serverless focus | Strong | General AWS infrastructure |
| Programming language required | ❌ | ✅ |
| Best for | Simple serverless definitions | Complex infrastructure using reusable code |

Example:

```
SAM:
template.yaml
     ↓
CloudFormation
```

CDK:

```
Python / TypeScript
       ↓
CDK
       ↓
CloudFormation
```

---

# 20. SAM vs Serverless Framework

Don't confuse AWS SAM with the **Serverless Framework**.

|  | AWS SAM | Serverless Framework |
| --- | --- | --- |
| Provider | AWS | Third-party/open-source ecosystem |
| AWS integration | Native AWS | Multi-cloud capable |
| CloudFormation | ✅ | Commonly uses CloudFormation on AWS |
| AWS serverless focus | ✅ | ✅ |
| AWS-managed framework | ✅ | ❌ |

For SAA-C03, **SAM** is the AWS-native serverless IaC tool you should recognize.

---

# 21. SAM and CloudFormation relationship

This is worth remembering exactly:

```
                SAM Template
                template.yaml
                     │
                     ▼
              SAM Transformation
                     │
                     ▼
               CloudFormation
                     │
        ┌────────────┼─────────────┐
        ▼            ▼             ▼
      Lambda      API Gateway    DynamoDB
```

SAM transforms its simplified resources into underlying CloudFormation resources.

For example:

```yaml
Type: AWS::Serverless::Function
```

is a SAM abstraction that ultimately results in the appropriate CloudFormation resources.

---

# 22. SAM Application Example

Suppose you build an image-processing application.

User uploads:

```
photo.jpg
```

to S3.

You want Lambda to create a thumbnail.

Architecture:

```
                  User
                    │
                    ▼
                  S3
             image uploaded
                    │
                    ▼
                  Lambda
                    │
                    ▼
             Thumbnail created
                    │
                    ▼
                  S3
```

SAM can define the Lambda and its S3 event trigger.

Conceptually:

```yaml
Resources:

  ThumbnailFunction:
    Type: AWS::Serverless::Function

    Properties:
      Runtime: python3.12
      Handler: app.lambda_handler
      CodeUri: thumbnail/

      Events:
        S3Upload:
          Type: S3
          Properties:
            Bucket: !Ref ImageBucket
            Events: s3:ObjectCreated:*

  ImageBucket:
    Type: AWS::S3::Bucket
```

Now the infrastructure is reproducible.

You can deploy the same architecture into:

```
Development
     ↓
Testing
     ↓
Production
```

---

# 23. Why SAM is useful in real projects

Without IaC:

```
Create resource manually
       ↓
Configure
       ↓
Click around console
       ↓
Repeat in another environment
       ↓
Configuration differences ❌
```

With SAM:

```
template.yaml
       ↓
Deploy
       ↓
Dev
       ↓
Same template
       ↓
Production
```

Benefits:

- Repeatable deployments
- Version-controlled infrastructure
- Easier CI/CD
- Local testing
- Less manual configuration
- Easier serverless application management

---

# 24. SAM + CI/CD

SAM works well with CI/CD pipelines.

Example:

```
Developer
    ↓
GitHub
    ↓
CodePipeline
    ↓
SAM Build
    ↓
SAM Deploy
    ↓
CloudFormation
    ↓
Lambda / API Gateway / DynamoDB
```

So a company can automatically deploy a serverless application whenever code is committed.

---

# 25. What SAM is NOT

Very important.

### SAM is NOT:

❌ A compute service

❌ A replacement for Lambda

❌ An API service

❌ A database

❌ A monitoring service

❌ A runtime environment

❌ A replacement for CloudFormation

Instead:

> **SAM is a framework/tool for defining and deploying serverless applications.**
> 

---

# 26. SAA-C03 Scenario Recognition

### Scenario 1

> Developers want to define Lambda, API Gateway, and DynamoDB infrastructure in a simple template and deploy the entire serverless application repeatedly.
> 

✅ **AWS SAM**

---

### Scenario 2

> Developers want to test Lambda functions and API Gateway locally before deploying them.
> 

✅ **AWS SAM CLI**

---

### Scenario 3

> A company needs Infrastructure as Code for VPCs, EC2, RDS, IAM, and many other AWS resources.
> 

✅ **CloudFormation** is the broader/general solution.

---

### Scenario 4

> Developers want to define AWS infrastructure using Python or TypeScript constructs.
> 

✅ **AWS CDK**

---

### Scenario 5

> A Lambda function should execute whenever an object is uploaded to S3.
> 

You can implement it with SAM:

```
S3
 ↓
Lambda
```

---

# ⭐ Final SAA-C03 Master Table

| Concept | Remember |
| --- | --- |
| **AWS SAM** | Serverless Application Model |
| Main purpose | Build/deploy/manage serverless applications |
| Infrastructure as Code? | ✅ |
| Template | `template.yaml` |
| Deployment engine | **CloudFormation** |
| Lambda | ✅ Strong integration |
| API Gateway | ✅ Strong integration |
| DynamoDB | ✅ |
| S3 | ✅ |
| SQS | ✅ |
| SNS | ✅ |
| EventBridge | ✅ |
| Local Lambda testing | ✅ SAM CLI |
| Local API testing | ✅ SAM CLI |
| CI/CD | ✅ |
| `sam init` | Create project |
| `sam build` | Build application |
| `sam deploy` | Deploy application |
| `sam local` | Test locally |
| `AWS::Serverless::Function` | SAM Lambda abstraction |
| Main advantage | Simplifies serverless IaC |
| SAM vs CloudFormation | SAM is serverless-focused; CloudFormation is general-purpose |
| SAM vs CDK | SAM uses declarative templates; CDK uses programming languages |
| SAM replaces Lambda? | ❌ |
| SAM replaces CloudFormation? | ❌ |
| SAM is an AWS service that runs your code? | ❌ |

## 🔥 One-minute exam memory

```
AWS SAM
   │
   │ Serverless IaC
   ▼
CloudFormation
   │
   ├── Lambda
   ├── API Gateway
   ├── DynamoDB
   ├── S3
   ├── SQS
   ├── SNS
   └── EventBridge
```

**If the SAA-C03 question says *“simplify development/deployment of a serverless application using Infrastructure as Code”* → think AWS SAM.**

**If it says *“general Infrastructure as Code for AWS resources”* → think CloudFormation.**

**If it says *“define infrastructure using Python/TypeScript/Java/etc.”* → think AWS CDK.**
