# AWS CloudWatch — Complete DevOps / SRE Study Notes

## CORE

### Ingestion

1. **What it is** — CloudWatch ingestion is the process of collecting metrics, logs, events, and telemetry from AWS resources and applications into CloudWatch.
2. **What it does** — Centralizes operational data so teams can monitor infrastructure and applications without maintaining separate monitoring systems.
3. **Scenario** — An SRE wants EC2 OS metrics and application logs in CloudWatch for troubleshooting production incidents.
4. **Example** — Install the CloudWatch Agent to collect memory: `mem_used_percent`; send application logs such as `/var/log/app.log` to CloudWatch Logs.

### Dashboards

1. **What it is** — CloudWatch Dashboards are customizable visual views of metrics, logs, alarms, and operational data.
2. **What it does** — Gives engineers a single screen for monitoring application and infrastructure health.
3. **Scenario** — During an outage, the on-call engineer needs CPU, ALB errors, Lambda duration, and database connections together.
4. **Example** — Create a dashboard containing `EC2 CPUUtilization`, `ALB HTTPCode_ELB_5XX_Count`, and `AWS/Lambda Duration`.

### Alarms

1. **What it is** — CloudWatch Alarms continuously evaluate metrics or metric math against defined thresholds.
2. **What it does** — Automatically detects abnormal conditions and can trigger actions such as SNS notifications or Auto Scaling.
3. **Scenario** — A production API should alert when HTTP 5xx errors remain high for five minutes.
4. **Example** — Alarm when `HTTPCode_Target_5XX_Count > 100` for `5 consecutive 1-minute periods`, sending an SNS notification.

---

## AI OPERATIONS

### Overview

1. **What it is** — CloudWatch AI Operations applies AI/ML-assisted analysis to operational telemetry and incidents.
2. **What it does** — Helps engineers detect issues, correlate signals, identify likely causes, and reduce troubleshooting time.
3. **Scenario** — A service suddenly becomes slow and the SRE wants CloudWatch to correlate latency, errors, logs, and infrastructure changes.
4. **Example** — Use CloudWatch investigations to analyze an alarm/incident and surface related metrics, logs, traces, and probable root causes.

### Investigations

1. **What it is** — CloudWatch Investigations is an AI-assisted incident investigation capability for analyzing operational problems.
2. **What it does** — Automatically examines telemetry and relationships between resources to accelerate root-cause analysis.
3. **Scenario** — An API alarm fires and the SRE needs to determine whether the cause is a deployment, downstream service, database, or infrastructure problem.
4. **Example** — Start an investigation from an alarm and review correlated signals and suggested root causes instead of manually searching dozens of dashboards.

### Configuration

1. **What it is** — CloudWatch AI Operations configuration controls the telemetry, permissions, and operational context available to AI-assisted analysis.
2. **What it does** — Ensures investigations have appropriate CloudWatch data and access to produce useful operational insights.
3. **Scenario** — A company enables AI-assisted investigations for production accounts while controlling which telemetry and resources are accessible.
4. **Example** — Configure required IAM permissions and CloudWatch telemetry so an investigation can analyze relevant logs, metrics, traces, alarms, and resource relationships.

---

## GENAI OBSERVABILITY

### Model Invocations

1. **What it is** — CloudWatch provides observability for generative-AI model invocation activity, including Amazon Bedrock workloads.
2. **What it does** — Helps monitor invocation volume, latency, errors, token usage, and model-related operational behavior.
3. **Scenario** — An application using an LLM becomes slower and the team needs to determine whether model latency or application code is responsible.
4. **Example** — Monitor Bedrock invocation metrics such as invocation count, latency, errors, and token consumption.

### Bedrock AgentCore

1. **What it is** — CloudWatch observability for Amazon Bedrock AgentCore provides operational visibility into AI agents and their runtime activity.
2. **What it does** — Helps teams monitor agent executions, latency, errors, traces, and interactions with tools/models.
3. **Scenario** — An AI agent intermittently fails while calling tools, and the team needs to identify where the execution breaks.
4. **Example** — Use CloudWatch telemetry/traces to inspect an AgentCore execution and identify latency or failures across model and tool calls.

### Coding Agent Insights

1. **What it is** — Coding Agent Insights provides CloudWatch observability for coding-agent workloads and their operational behavior.
2. **What it does** — Helps engineering teams understand coding-agent activity, performance, failures, and resource usage.
3. **Scenario** — A company runs AI coding agents in its development pipeline and wants to identify slow or failing agent executions.
4. **Example** — Use CloudWatch insights to investigate coding-agent executions and correlate agent activity with logs, metrics, and traces.

---

## APPLICATION SIGNALS (APM)

### Overview

1. **What it is** — CloudWatch Application Signals is an APM capability that automatically collects application performance signals from supported applications.
2. **What it does** — Provides service-centric visibility into availability, latency, errors, dependencies, and SLO performance.
3. **Scenario** — An SRE manages dozens of microservices and wants application health without manually instrumenting every dashboard.
4. **Example** — Enable Application Signals and monitor automatically discovered services using latency, availability, and dependency telemetry.

### Services

1. **What it is** — Application Signals represents applications as monitored services with key performance indicators.
2. **What it does** — Lets teams evaluate individual microservices based on latency, errors, availability, and dependencies.
3. **Scenario** — The `checkout` service has increased latency while other services remain healthy.
4. **Example** — Open Application Signals → Services → select `checkout` and inspect request latency, error rate, dependencies, and SLO status.

### Application Map

1. **What it is** — Application Map is a visual representation of applications, services, dependencies, and their health.
2. **What it does** — Makes distributed-system relationships and problematic components easier to identify.
3. **Scenario** — A customer-facing API fails and the team needs to quickly determine which downstream service is causing the problem.
4. **Example** — Open the Application Map and follow `ALB → API service → payment service → database` to locate an unhealthy dependency.

### Transaction Search

1. **What it is** — Transaction Search lets engineers search individual application transactions and traces.
2. **What it does** — Enables detailed investigation of specific requests instead of relying only on aggregated metrics.
3. **Scenario** — Users report that one checkout request takes 12 seconds while average latency looks normal.
4. **Example** — Search transactions for the checkout service, filter by high latency, and inspect the corresponding trace/request details.

### Service Level Objectives (SLO)

1. **What it is** — An SLO defines a measurable reliability target for a service.
2. **What it does** — Converts observability data into reliability objectives such as availability and latency targets.
3. **Scenario** — A payment API must achieve 99.9% availability and maintain acceptable latency.
4. **Example** — Define an SLO such as `Availability >= 99.9%` and monitor its compliance and error-budget consumption.

---

## SYNTHETICS / RUM / TRACES

### Synthetics Canaries

1. **What it is** — CloudWatch Synthetics Canaries are automated scripts that periodically test application endpoints or user workflows.
2. **What it does** — Detects failures before real users report them.
3. **Scenario** — An e-commerce team wants to continuously verify that login and checkout work from outside the application.
4. **Example** — Create a canary that runs every five minutes and performs `GET /login` followed by a synthetic checkout workflow; alarm on failures.

### RUM (Real User Monitoring)

1. **What it is** — CloudWatch RUM collects performance and user-experience telemetry from real users' web browsers.
2. **What it does** — Shows actual client-side latency, errors, page performance, and user experience.
3. **Scenario** — Synthetic tests are healthy, but users in one region report slow page loads.
4. **Example** — Enable RUM for a web application and analyze browser page-load duration, JavaScript errors, and user geography.

### Traces

1. **What it is** — CloudWatch traces provide distributed request tracing across application components.
2. **What it does** — Shows how a request travels through services and where latency/errors occur.
3. **Scenario** — An API request takes 5 seconds and the team needs to determine which microservice consumes most of the time.
4. **Example** — Trace `POST /checkout` and inspect segments such as API Gateway → Lambda → DynamoDB.

### Trace Map

1. **What it is** — Trace Map visualizes relationships between traced services and resources.
2. **What it does** — Helps identify dependencies, bottlenecks, and unhealthy components in distributed applications.
3. **Scenario** — A microservice architecture has dozens of dependencies and an incident affects only one request path.
4. **Example** — Open Trace Map and follow `Service A → Service B → DynamoDB` to identify where errors or latency concentrate.

### Application Insights

1. **What it is** — CloudWatch Application Insights automatically discovers application components and monitors relevant telemetry for supported applications.
2. **What it does** — Simplifies monitoring by identifying metrics, logs, and alarms relevant to application components.
3. **Scenario** — A .NET application running on EC2 has many components and the team doesn't want to manually configure every monitoring signal.
4. **Example** — Enable Application Insights for the application and let it configure monitoring for components such as EC2, IIS, SQL Server, and application logs.

---

## INFRASTRUCTURE MONITORING

### Container Insights

1. **What it is** — Container Insights collects metrics and logs from containerized environments such as Amazon ECS and Amazon EKS.
2. **What it does** — Provides visibility into cluster, node, pod/task, and container resource utilization and performance.
3. **Scenario** — An EKS service becomes slow and the SRE needs to determine whether pods are CPU- or memory-constrained.
4. **Example** — Enable Container Insights and inspect pod CPU/memory utilization, restart counts, and cluster resource metrics.

### Database Insights

1. **What it is** — CloudWatch Database Insights provides monitoring and performance visibility for supported AWS databases.
2. **What it does** — Helps identify database load, waits, resource pressure, and problematic queries.
3. **Scenario** — An RDS database has high latency and the team needs to determine whether CPU, connections, locks, or SQL workload is responsible.
4. **Example** — Open Database Insights for an RDS instance and analyze DB load, wait events, and top SQL statements.

### Lambda Insights

1. **What it is** — Lambda Insights provides enhanced runtime-level performance metrics and diagnostics for Lambda functions.
2. **What it does** — Helps identify memory, CPU, cold-start, invocation, and runtime performance problems.
3. **Scenario** — A Lambda function has intermittent high latency and standard Lambda metrics aren't detailed enough.
4. **Example** — Enable Lambda Insights and inspect enhanced telemetry such as memory utilization, CPU time, and initialization duration.

### SageMaker Insights

1. **What it is** — SageMaker Insights provides observability for Amazon SageMaker workloads and ML resources.
2. **What it does** — Helps monitor machine-learning infrastructure and workload performance.
3. **Scenario** — An ML inference endpoint experiences increased latency and the team needs deeper resource-level visibility.
4. **Example** — Use CloudWatch/SageMaker monitoring to examine endpoint invocation latency, errors, instance utilization, and related telemetry.

### EC2 Resource Health

1. **What it is** — EC2 Resource Health provides visibility into the health status of EC2 resources and related infrastructure conditions.
2. **What it does** — Helps determine whether an EC2 problem is caused by the instance, underlying AWS infrastructure, or another resource condition.
3. **Scenario** — A production EC2 instance becomes unreachable and the SRE needs to determine whether AWS infrastructure is reporting a problem.
4. **Example** — Check EC2 Resource Health for instance/system status and investigate reported resource-level health events.

---

## LOGS

### Log Management

1. **What it is** — CloudWatch Logs provides centralized collection, storage, retention, and management of application and infrastructure logs.
2. **What it does** — Gives teams one place to store and control operational logs from AWS services and applications.
3. **Scenario** — An application runs across 50 EC2 instances and engineers need centralized logs instead of SSHing into individual servers.
4. **Example** — Configure the CloudWatch Agent to send `/var/log/app.log` to `/aws/myapp/production`.

### Log Analytics (Logs Insights)

1. **What it is** — CloudWatch Logs Insights is an interactive query engine for searching and analyzing CloudWatch Logs.
2. **What it does** — Quickly finds errors, patterns, latency problems, and operational events across large log sets.
3. **Scenario** — An API returns HTTP 500 errors and the team wants to identify affected endpoints.
4. **Example:**

```
fields @timestamp, @message
| filter @message like /500/
| stats count() by bin(5m)
```

### Log Anomalies

1. **What it is** — CloudWatch Logs anomaly detection identifies unusual patterns in log data.
2. **What it does** — Helps detect abnormal log behavior without requiring engineers to define every possible error pattern manually.
3. **Scenario** — A service suddenly begins generating an unfamiliar error at 3 AM.
4. **Example** — Enable anomaly detection for a log group and investigate newly detected abnormal log patterns.

---

## METRICS

### Query Studio

1. **What it is** — CloudWatch Query/Metric Query Studio provides an interactive environment for building and analyzing CloudWatch metric queries.
2. **What it does** — Makes complex metric exploration and visualization easier than manually navigating individual metrics.
3. **Scenario** — An SRE needs to compare CPU utilization across multiple EC2 instances and time periods.
4. **Example** — Build a query for `AWS/EC2` → `CPUUtilization`, group by `InstanceId`, and visualize the result.

### Classic metrics

1. **What it is** — CloudWatch Metrics are time-series measurements collected from AWS resources and applications.
2. **What it does** — Provides the fundamental quantitative signals used for dashboards, alarms, queries, and automated actions.
3. **Scenario** — An operations team needs to monitor whether an EC2 instance is overloaded.
4. **Example** — Monitor `AWS/EC2 → CPUUtilization` and create an alarm when average CPU exceeds `80%`.

### Explorer

1. **What it is** — CloudWatch Metrics Explorer is a dynamic interface for exploring metrics across resources using dimensions and tags.
2. **What it does** — Makes it easier to discover and compare metrics across large fleets without manually selecting every resource.
3. **Scenario** — A company has 500 EC2 instances tagged `Environment=Production` and wants fleet-wide CPU visibility.
4. **Example** — Use Metrics Explorer to filter EC2 resources by `Environment=Production` and graph `CPUUtilization`.

### Streams (Metric Streams)

1. **What it is** — CloudWatch Metric Streams continuously streams CloudWatch metrics to a destination such as Amazon Data Firehose.
2. **What it does** — Enables near-real-time delivery of metrics to external monitoring, analytics, or data-platform systems.
3. **Scenario** — An enterprise wants centralized observability across AWS accounts in an external monitoring platform.
4. **Example** — Configure a Metric Stream → Kinesis Data Firehose → external observability platform.

---

## NETWORK MONITORING

### Flow monitors (Network Flow Monitor)

1. **What it is** — Network Flow Monitor provides visibility into network flows between AWS resources.
2. **What it does** — Helps identify network performance problems such as latency, packet loss, and connectivity issues.
3. **Scenario** — An application intermittently becomes slow and the team suspects network degradation between EC2 instances.
4. **Example** — Create a flow monitor for traffic between application EC2 instances and inspect flow-level performance telemetry.

### Internet monitors (Internet Monitor)

1. **What it is** — Amazon CloudWatch Internet Monitor observes internet connectivity and performance between AWS applications and end users.
2. **What it does** — Identifies internet-related availability and performance problems affecting users and AWS resources.
3. **Scenario** — Customers in one geographic region experience slow access to an application hosted in AWS.
4. **Example** — Configure Internet Monitor for an application resource and inspect client-location performance and internet health events.

### Synthetic monitors (Network Synthetic Monitor)

1. **What it is** — Network Synthetic Monitor uses synthetic network tests to proactively measure connectivity and network performance.
2. **What it does** — Detects network problems before application users necessarily experience them.
3. **Scenario** — A company needs continuous testing between its corporate network and AWS workloads.
4. **Example** — Configure synthetic network probes between network locations and monitor latency, packet loss, and connectivity.

---

## SETUP

### Getting Started

1. **What it is** — CloudWatch Getting Started provides guided setup paths for beginning monitoring of AWS resources and applications.
2. **What it does** — Helps teams quickly enable common metrics, logs, dashboards, alarms, and observability features.
3. **Scenario** — A new DevOps team launches its first production AWS workload and needs baseline monitoring.
4. **Example** — Open CloudWatch → Getting Started and configure recommended monitoring for EC2, Lambda, logs, and alarms.

### What's new

1. **What it is** — CloudWatch's What's New information highlights newly released monitoring and observability capabilities.
2. **What it does** — Keeps engineers aware of new CloudWatch features, integrations, and service capabilities.
3. **Scenario** — An SRE team periodically reviews AWS monitoring improvements to replace older manual monitoring approaches.
4. **Example** — Review recent CloudWatch feature announcements before updating the team's observability architecture.

### Settings

1. **What it is** — CloudWatch Settings contains account- and feature-level configuration options for CloudWatch capabilities.
2. **What it does** — Allows teams to configure operational behavior such as log retention, cross-account observability, and related CloudWatch settings.
3. **Scenario** — A company centralizes observability across multiple AWS accounts and needs appropriate CloudWatch configuration.
4. **Example** — Configure CloudWatch cross-account observability and manage relevant account-level monitoring settings.

---

# AWS CloudWatch — One-Look Master Table

| Category | Item | Remember it as |
| --- | --- | --- |
| Core | Ingestion | **Collect telemetry** |
| Core | Dashboards | **Visualize health** |
| Core | Alarms | **Detect + act** |
| AI Operations | Overview | **AI-assisted operations** |
| AI Operations | Investigations | **AI root-cause analysis** |
| AI Operations | Configuration | **Configure AI Ops** |
| GenAI | Model Invocations | **Monitor AI calls** |
| GenAI | Bedrock AgentCore | **Observe AI agents** |
| GenAI | Coding Agent Insights | **Observe coding agents** |
| APM | Overview | **Application performance** |
| APM | Services | **Service health** |
| APM | Application Map | **Service dependencies** |
| APM | Transaction Search | **Find individual requests** |
| APM | SLO | **Reliability target** |
| Synthetics/RUM/Traces | Synthetics Canaries | **Test proactively** |
| Synthetics/RUM/Traces | RUM | **Real-user experience** |
| Synthetics/RUM/Traces | Traces | **Follow one request** |
| Synthetics/RUM/Traces | Trace Map | **Visualize request paths** |
| Synthetics/RUM/Traces | Application Insights | **Auto application monitoring** |
| Infrastructure | Container Insights | **ECS/EKS observability** |
| Infrastructure | Database Insights | **Database performance** |
| Infrastructure | Lambda Insights | **Lambda runtime performance** |
| Infrastructure | SageMaker Insights | **ML observability** |
| Infrastructure | EC2 Resource Health | **EC2 health** |
| Logs | Log Management | **Store/manage logs** |
| Logs | Logs Insights | **Query logs** |
| Logs | Log Anomalies | **Detect unusual logs** |
| Metrics | Query Studio | **Build metric queries** |
| Metrics | Classic Metrics | **Time-series measurements** |
| Metrics | Explorer | **Explore fleet metrics** |
| Metrics | Metric Streams | **Stream metrics externally** |
| Network | Network Flow Monitor | **AWS flow performance** |
| Network | Internet Monitor | **End-user internet performance** |
| Network | Network Synthetic Monitor | **Synthetic network tests** |
| Setup | Getting Started | **Initial setup** |
| Setup | What's New | **New capabilities** |
| Setup | Settings | **CloudWatch configuration** |

### 🧠 The SAA-C03 mental model

Think of CloudWatch as:

```
                    CLOUDWATCH
                         │
       ┌─────────────────┼──────────────────┐
       │                 │                  │
    COLLECT            ANALYZE            ACT
       │                 │                  │
   Logs/Metrics       Insights           Alarms
   Traces/RUM        Dashboards           SNS
   Synthetics        APM/SLO          Auto Scaling
       │
       ▼
   UNDERSTAND
       │
 ┌─────┼─────────┬──────────┐
 │     │         │          │
APM  Logs   Infrastructure  Network
 │     │         │          │
Services Logs   EC2/EKS     Flow
SLO    Insights Lambda       Internet
Traces Anomalies DB          Synthetic
```

**Exam shortcut:**

**Metric = "how much?" · Log = "what happened?" · Trace = "where did the request go?" · Synthetics = "does it work?" · RUM = "what does the real user experience?" · Alarm = "when should AWS/people react?" · Dashboard = "show me the health."**
