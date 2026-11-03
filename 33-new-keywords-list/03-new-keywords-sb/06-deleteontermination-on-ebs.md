# DeleteOnTermination on EBS

To preserve an EBS volume when its EC2 instance terminates, set `DeleteOnTermination` to `False`; Termination Protection protects the instance, not its EBS data.

- ScenarioQ
    
    A Solutions Architect is designing a highly available environment for an application. She plans to host the application on EC2 instances within an Auto Scaling Group. One of the conditions requires data stored on root EBS volumes to be preserved if an instance terminates.
    
    What should be done to satisfy the requirement?
    
    - **Set the value of `DeleteOnTermination` attribute of the EBS volumes to `False`.**
    - **Use AWS DataSync to replicate root volume data to Amazon S3.**
    - Configure ASG to suspend the health check process for each EC2 instance.
    - Enable the Termination Protection option for all EC2 instances.

An EC2 instance is terminated and the company wants its root EBS volume to remain available.

✅ **Set `DeleteOnTermination = False`.**

## **“Instance dies, EBS survives → DeleteOnTermination = False.”**

And don't confuse:

> **Termination Protection protects EC2.**
> 

> **DeleteOnTermination protects EBS.**
> 

### Core relationship

```
ASG
 │
 ▼
EC2 Instance
 │
 ▼
Root EBS Volume
 │
 └── DeleteOnTermination
          │
          ├── True  → Delete EBS
          └── False → Preserve EBS
```

---

#
