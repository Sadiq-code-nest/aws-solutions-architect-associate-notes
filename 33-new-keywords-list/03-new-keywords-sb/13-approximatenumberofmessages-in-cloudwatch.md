# ApproximateNumberOfMessages in CloudWatch

### `ApproximateNumberOfMessages`—SQS Metric

It tells you **approximately how many messages are currently waiting in an SQS queue to be processed**.

Think of it as:

> **“How many requests are waiting in the queue?”**
> 

### Scenario

Suppose your application has:

```
Users → SQS → EC2 Workers
```

At 10 AM:

- SQS has **100 messages**
- EC2 instances are processing them quickly

`ApproximateNumberOfMessages = 100`

Later, during a traffic spike:

- **5,000 messages** arrive
- EC2 workers can process them slower than they arrive

Now:

`ApproximateNumberOfMessages ≈ 5,000`

This indicates a **large backlog**, so the Auto Scaling Group can launch more EC2 instances to process messages faster.

### 🧠 SAA-C03 shortcut

**`ApproximateNumberOfMessages` = messages waiting in SQS → backlog → useful for scaling EC2 consumers.**

- ScenarioQ
    
    A manufacturing company has EC2 instances running in AWS. The EC2 instances are configured with Auto Scaling. There are a lot of requests being lost because of too much load on the servers. The Auto Scaling is launching new EC2 instances to take the load accordingly yet, there are still some requests that are being lost.
    
    Which of the following is the MOST suitable solution that you should implement to avoid losing recently submitted requests?
    
    - Set up Amazon Aurora Serverless for on-demand, auto-scaling configuration of your EC2 Instances and also enable Amazon Aurora Parallel Query feature for faster analytical queries over your current data.
    - Replace the Auto Scaling group with a cluster placement group to achieve a low-latency network performance necessary for tightly-coupled node-to-node communication.
    - Use larger instances for your application with an attached Elastic Fabric Adapter (EFA).
    - **Use an Amazon SQS queue to decouple the application components and scale-out the EC2 instances based upon the `ApproximateNumberOfMessages` metric in Amazon CloudWatch.**

Why `ApproximateNumberOfMessages`?

Suppose:

```
SQS queue = 1,500 messages

EC2 instances = 10
```

Then roughly:

```
1,500 ÷ 10 = 150 messages per instance
```

So each instance effectively has a backlog of about **150 messages**.

If your acceptable backlog is only **100 messages per instance**, then:

```
Current backlog per instance = 150
Target = 100

150 > 100
      ↓
Scale OUT
      ↓
Launch more EC2 instances
```

As more EC2 instances process the queue, the backlog decreases.
