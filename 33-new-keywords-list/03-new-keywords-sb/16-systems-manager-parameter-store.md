# Systems Manager Parameter Store

### Definition

AWS Systems Manager Parameter Store provides secure, centralized storage for configuration values and parameters.

In this question, it can store the **CloudWatch Agent configuration**.

```
Parameter Store
       |
       | Agent configuration
       v
EC2
 |
 v
CloudWatch Agent
 |
 v
CloudWatch
```

### Example

Store:

```
/cloudwatch/agent/config
```

containing the agent's configuration.

Instances can retrieve the configuration rather than manually maintaining configuration on every server.

### Common confusion

| Parameter Store | Secrets Manager |
| --- | --- |
| Configuration/parameters | Secrets/passwords/credentials |
| Can store configuration centrally | Designed specifically for secrets |
| Integrates with SSM ecosystem | Secret rotation features |

### Exam trigger

> **"Centralized configuration parameters" → Parameter Store**
>
