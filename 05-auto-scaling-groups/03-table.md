# ⭐ ASG MASTER TABLE — SAA-C03

This is the **one-table revision sheet** you can use before the exam.

| ASG Concept | What it does | Trigger / Input | Example | Best exam clue |
| --- | --- | --- | --- | --- |
| **Auto Scaling Group** | Automatically manages EC2 count | Scaling policies, health checks, schedules | 3 EC2 → 6 EC2 | "Automatically add/remove EC2" |
| **Minimum Capacity** | Lowest desired capacity ASG should maintain | ASG configuration | Min = 2 | "Never have fewer than 2" |
| **Desired Capacity** | Current target number of instances | ASG configuration/scaling | Desired = 3 | "Currently want 3" |
| **Maximum Capacity** | Highest number ASG can scale to | ASG configuration | Max = 10 | "Never exceed 10" |
| **Scale Out** | Adds EC2 instances | Increased demand | 3 → 6 | "Traffic increased" |
| **Scale In** | Removes EC2 instances | Reduced demand | 6 → 3 | "Traffic decreased" |
| **Target Tracking** ⭐ | Maintains metric near target | Metric target | CPU ≈ 40% | **"Keep/maintain around X%"** |
| **Simple Scaling** | Performs a scaling action after an alarm | CloudWatch alarm | CPU >70% → +2 | "Alarm → fixed action" |
| **Step Scaling** ⭐ | Performs different scaling actions depending on alarm severity | CloudWatch alarm + steps | CPU 70% +2, 90% +5 | **"Different levels/actions"** |
| **Scheduled Scaling** ⭐ | Scales at a known time | User-defined schedule | Friday 5 PM → 10 EC2 | **"Predictable schedule"** |
| **Predictive Scaling** ⭐ | Forecasts future load and scales ahead | Historical workload | Predict tomorrow's traffic spike | **"Forecast/predict future demand"** |
| **CPUUtilization** | Scales based on average CPU | CloudWatch | CPU >70% | CPU-bound application |
| **RequestCountPerTarget** ⭐ | Scales based on requests per target | ALB metric | 500 requests/EC2 | **Request-heavy application** |
| **Network In/Out** | Scales based on network throughput | CloudWatch | Network Out very high | Network-bound workload |
| **Custom Metric** | Scales based on application-specific data | CloudWatch custom metric | Orders waiting >5000 | "Custom application metric" |
| **CloudWatch Alarm** | Detects metric threshold conditions | CloudWatch metric | CPU >70% | "Trigger scaling" |
| **Scaling Policy** | Defines how ASG reacts | Metric/schedule | Add 2 instances | "How should ASG scale?" |
| **Cooldown** ⭐ | Gives instances time to stabilize after scaling | Scaling activity | Wait before another scaling action | **"Prevent rapid repeated scaling"** |
| **Launch Template** ⭐ | Defines how new EC2 instances are created | AMI, type, SG, IAM role, etc. | Launch `t3.micro` with AMI | **"Configuration for new EC2"** |
| **User Data** | Configures instance during launch | Launch Template | Install/start application | "Configure new instance automatically" |
| **Health Check** | Detects unhealthy instances | EC2/ELB health | Replace unhealthy EC2 | **"Replace unhealthy instance"** |
| **Multiple AZs** ⭐ | Improves availability | ASG subnet/AZ configuration | EC2 in AZ-1 + AZ-2 | **"High availability"** |
| **ALB + ASG** ⭐ | Distributes traffic + manages capacity | ALB + ASG | ALB → EC2 fleet | **"Scalable web application"** |
| **Pre-baked AMI** | Reduces instance startup/configuration time | AMI | App already installed | "Scale faster / ready quickly" |

---

# 🔥 Scaling Policies — One-Table Comparison

| Policy | Core question | How it works | Example | Remember |
| --- | --- | --- | --- | --- |
| **Target Tracking** ⭐ | "What target should I maintain?" | ASG automatically adjusts capacity to maintain target | Keep CPU ≈ 40% | **Maintain target** |
| **Simple Scaling** | "What should I do after this alarm?" | Alarm triggers a scaling action | CPU >70% → +2 | **Simple reaction** |
| **Step Scaling** ⭐ | "How severe is the problem?" | Different scaling actions for different alarm levels | 70% → +2, 90% → +5 | **Multiple steps** |
| **Scheduled Scaling** ⭐ | "When do I already know traffic will change?" | Scaling happens at predefined times | Friday 5 PM → 10 EC2 | **Known schedule** |
| **Predictive Scaling** ⭐ | "What will demand probably be?" | Uses historical patterns to forecast and scale ahead | Predict Monday morning spike | **AWS forecast** |
