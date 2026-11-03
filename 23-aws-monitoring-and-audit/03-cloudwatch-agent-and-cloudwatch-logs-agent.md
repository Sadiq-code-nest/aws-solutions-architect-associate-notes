# CloudWatch Agent & CloudWatch Logs Agent

## 🖥️ CloudWatch Logs for EC2 — Deep Understanding for SAA-C03

---

## 1. Default Behavior (VERY IMPORTANT)

### ❗ Key Fact

> **EC2 does NOT send logs to CloudWatch by default**
> 

---

### 🧠 What this means

- Your EC2 instance generates logs (e.g., `/var/log/syslog`, app logs)
- BUT ❌ they stay on the instance unless you configure something

👉 To send logs → you MUST install an **agent**

---

## 2. Visual Concept (How Logs Flow from EC2)

---

## 3. Solution: CloudWatch Agent

---

### ✅ You need to:

1. Install **CloudWatch Agent** on EC2
2. Configure which logs to send
3. Attach correct IAM role

---

### 🔐 IAM Permissions

👉 EC2 instances must have a role with the following:

- `CloudWatchLogsFullAccess` (or minimal required permissions)

---

## 4. Two Types of Agents

---

## 🔹 1. CloudWatch Logs Agent (OLD)

- Legacy agent
- Only sends:
    - Logs → CloudWatch Logs

❌ Cannot collect metrics like RAM

---

## 🔹 2. CloudWatch Unified Agent (IMPORTANT)

> Modern, recommended agent
> 

---

### ✅ Capabilities

- Send **logs** → CloudWatch Logs
- Send **custom metrics** → CloudWatch Metrics

---

### ⭐ Key Advantage

👉 Collects **system-level metrics NOT available by default**

---

## 5. Unified Agent — Metrics You Can Collect

---

### 🧠 Why this matters

Default EC2 metrics:

- CPU
- Network
- Disk (basic)

❌ Missing:

- RAM
- Processes
- Detailed system stats

👉 Unified Agent solves this

---

### 📊 Metrics Collected

---

### 🔹 CPU Metrics

- active, idle, system, user, steal

---

### 🔹 Memory (RAM)

- free
- used
- cached
- total

---

### 🔹 Disk Metrics

- free / used space
- I/O operations
- read/write bytes

---

### 🔹 Network (Netstat)

- TCP/UDP connections
- packets
- bytes

---

### 🔹 Processes

- running
- sleeping
- blocked
- total

---

### 🔹 Swap Space

- used / free

---

## 6. Configuration (Important Detail)

---

### ⚙️ Centralized Configuration

👉 Use:

- **SSM Parameter Store**

---

### 🧠 Benefit

- Manage config centrally
- Apply to multiple servers
- Easier scaling

---

## 7. Works Beyond EC2

---

### 🌍 Also works on:

- On-premise servers
- Hybrid environments

👉 Useful for:

- Centralized logging
- Hybrid cloud setups

---

## 8. SAA-C03 Exam Perspective

---

### 🎯 What AWS Tests

- Knowing EC2 does NOT send logs by default
- Knowing you need an **agent**
- Knowing **Unified Agent vs old agent**

---

## 🔑 Keywords to Watch

| Keyword | Answer |
| --- | --- |
| EC2 logs not appearing | Install agent |
| Need RAM metrics | Unified Agent |
| Collect logs from EC2 | CloudWatch Agent |
| Hybrid / on-prem logs | Unified Agent |

---

## 9. Common Exam Traps

---

### ⚠️ Trap 1: “Why are logs missing?”

👉 Scenario:

- EC2 running fine
- No logs in CloudWatch

✅ Answer:

> Install CloudWatch Agent
> 

---

### ⚠️ Trap 2: “Need RAM metrics”

👉 Default EC2 metrics do NOT include RAM

✅ Answer:

> Use **Unified Agent**
> 

---

### ⚠️ Trap 3: Old vs Unified Agent

| Feature | Logs Agent | Unified Agent |
| --- | --- | --- |
| Logs | ✅ Yes | ✅ Yes |
| Metrics | ❌ No | ✅ Yes |
| Recommended | ❌ No | ✅ YES |

---

## 10. Mini Practice Questions

---

### ✅ Q1

EC2 instance logs are not visible in CloudWatch Logs. What should you do?

**Answer:** Install CloudWatch Agent

---

### ✅ Q2

You need to monitor RAM usage on EC2.

**Answer:** Use Unified Agent

---

### ✅ Q3

You want to collect logs from on-prem servers into AWS.

**Answer:** Use Unified Agent

---

### ✅ Q4

Which agent should you use for both logs and metrics?

**Answer:** Unified Agent

---

## 11. Final Revision Notes

---

### ⚡ 30-Second Summary

- EC2 does NOT send logs by default
- Must install **CloudWatch Agent**
- **Unified Agent** = modern + best choice
- Unified Agent collects:
    - Logs
    - RAM, processes, disk, etc

---

### 🧠 Memory Shortcut

👉 Think:

> “No agent = no logs from EC2”
> 

---

### 🚀 Pro Tip (Exam Gold)

If question says:

- “Missing EC2 logs” → install agent
- “Need RAM metrics” → Unified Agent
- “Old vs new agent” → choose Unified Agent

---

If you want next:

👉 **CloudWatch Alarms (VERY HIGH EXAM WEIGHT)**

👉 or **End-to-end monitoring architecture (Logs + Metrics + Alarms)**

Just tell me 🔥
