# AWS Systems Manager OpsCenter — SAA-C03 Notes

## 1. What is OpsCenter?

AWS Systems Manager OpsCenter helps operations teams centrally view, investigate, and resolve operational issues across AWS resources.

When an AWS resource has a problem, OpsCenter creates or collects information into an operational work item called an OpsItem.

Think of it as a central to-do list for AWS operational problems.

AWS resource has a problem

Example: EC2 instance CPU utilization is too high

OpsCenter creates or collects an OpsItem

Related information, severity, and resource details

Operations team investigates and resolves

Uses Systems Manager tools and related AWS services

## 2. Real-world SAA-C03 scenario examples

### Scenario 1: EC2 instance has high CPU utilization

A company runs a web application on EC2. CPU utilization becomes unusually high, affecting application performance.

Solution: Use Amazon CloudWatch to detect the high CPU utilization and create an OpsItem in Systems Manager OpsCenter.

OpsCenter gives the operations team a central place to investigate the issue, view related information, and track resolution.

Exam trigger: Centralized investigation and resolution of operational issues → Systems Manager OpsCenter.

### Scenario 2: Multiple AWS resources have operational issues

A company operates hundreds of AWS resources. Its operations team struggles to track issues across EC2, databases, and other services.

Solution: Use OpsCenter to centralize operational issues into OpsItems, making it easier to prioritize, investigate, and track them.

Exam trigger: Centralized operational issue management → OpsCenter.

### Scenario 3: Automatically respond to operational problems

A company wants to detect an operational problem and help its team resolve it with less manual work.

Solution: Integrate CloudWatch alarms or supported AWS event sources with OpsCenter to create OpsItems. Use Systems Manager Automation runbooks where appropriate to automate remediation.

Exam trigger: Operational issue tracking plus automated remediation → OpsCenter + Systems Manager Automation.

## 3. OpsCenter vs. similar AWS services

| Service | What it does | Key exam phrase |
| --- | --- | --- |
| Systems Manager OpsCenter | Centralizes operational issues | Investigate and resolve issues |
| Systems Manager Automation | Automates operational tasks using runbooks | Automatically execute remediation steps |
| Amazon CloudWatch | Collects metrics, logs, and alarms | Monitor CPU, memory-related metrics, alarms |
| AWS Systems Manager Fleet Manager | Helps manage and administer server fleets | Manage servers at scale |
| AWS Control Tower | Governs a multi-account AWS environment | Account Factory, landing zone, guardrails |
| AWS Security Hub | Aggregates security findings | Central security posture management |

## 4. Quick exam practice

Question: A company manages hundreds of EC2 instances. When operational issues occur, the operations team must manually investigate multiple dashboards and track incidents separately. The company wants a centralized solution to organize operational issues and simplify investigation. Which AWS service should it use?

A. AWS Control Tower

B. AWS Systems Manager OpsCenter

C. AWS Resource Access Manager (RAM)

D. AWS Config Aggregator

Last-minute memory trick: OpsCenter = Ops problems; Automation = automated actions; CloudWatch = monitoring; Control Tower = multi-account governance.
