# EC2 Detailed Monitoring

### Definition

Detailed Monitoring provides EC2 metrics at a more frequent interval than basic monitoring.

It is useful when you need more granular monitoring of standard EC2 metrics.

```
EC2
 |
 v
Detailed Monitoring
 |
 v
More frequent CloudWatch metrics
```

### Example

If an ASG needs more frequent **CPU** data for scaling, detailed monitoring may be useful.

### Critical distinction

Detailed Monitoring:

```
Does NOT = OS memory monitoring
```

CloudWatch Agent:

```
DOES = OS-level memory monitoring
```

### Common confusion

| Detailed Monitoring | CloudWatch Agent |
| --- | --- |
| More frequent EC2 metrics | Collects additional OS/application metrics |
| AWS-managed standard metrics | Agent runs inside instance |
| Does not provide memory utilization automatically | Can collect memory utilization |
| Useful for faster metric visibility | Needed for custom OS metrics |

### Exam trigger

> **"Need standard EC2 metrics more frequently" → Detailed Monitoring**
>
