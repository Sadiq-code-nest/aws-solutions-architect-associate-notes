# CloudWatch Alarms

## 🚨 CloudWatch Alarms — Complete Guide (SAA-C03 Ready)

---

## 1. What is a CloudWatch Alarm?

👉 A **CloudWatch Alarm** monitors a **metric** and triggers an action when a threshold is crossed.

---

### 🧠 Example

- Monitor: **CPUUtilization**
- Condition: CPU > 80% for 5 minutes
- Action: Send notification or scale EC2

---

## 2. Alarm States (VERY IMPORTANT)

---

### 🔁 3 Possible States

| State | Meaning |
| --- | --- |
| ✅ **OK** | Everything is normal |
| ⚠️ **INSUFFICIENT_DATA** | Not enough data yet |
| 🚨 **ALARM** | Threshold breached |

---

### 🧠 Exam Tip

- New alarm → starts in **INSUFFICIENT_DATA**
- No metrics → stays in that state

---

## 3. Key Configuration Concepts

---

### ⏱️ Period

- Time window to evaluate metric
- Example: 60 seconds

---

### 📊 Evaluation Options

- Average
- Sum
- Minimum / Maximum
- Percentiles (p90, p95, etc.)

---

### ⚡ High-Resolution Metrics

- 10 sec
- 30 sec
- or multiples of 60 sec

👉 Used for **faster reaction time**

---

## 4. Alarm Actions (Targets)

---

## 🎯 What Can an Alarm Do?

---

### 🔹 1. EC2 Actions

- Stop instance
- Terminate instance
- Reboot instance
- **Recover instance** (VERY IMPORTANT)

---

### 🔹 2. Auto Scaling

👉 Automatically:

- Add instances
- Remove instances

---

### 🔹 3. SNS Notifications

👉 Send alerts to:

- Email
- SMS
- Lambda
- HTTP endpoints

---

## 5. Composite Alarms (High Exam Value)

---

### 🧠 Problem

Too many alarms = **noise**

---

### ✅ Solution: Composite Alarms

- Combine multiple alarms
- Use logic:
    - **AND**
    - **OR**

---

### 🧠 Example

Trigger alarm only if:

- CPU > 80% **AND**
- Network > high threshold

👉 Reduces false alerts

---

## 6. EC2 Instance Recovery (CRITICAL TOPIC)

---

## 🔍 Status Checks

---

### 3 Types:

| Check | What it monitors |
| --- | --- |
| Instance Status | OS / VM |
| System Status | Physical hardware |
| EBS Status | Attached volumes |

---

## 🔄 Recovery Action

👉 If s**ystem status fails**:

- CloudWatch Alarm can trigger **EC2 recovery.**

---

### ✅ What is preserved?

- Private IP
- Public IP
- Elastic IP
- Metadata
- Placement group

👉 The instance is moved to **healthy hardware**

---

### 🧠 Exam Trap

| Scenario | Answer |
| --- | --- |
| Hardware failure | Recover instance |
| App crash | Reboot instance |

---

## 7. Logs → Alarm Integration

---

### 🔄 Flow

---

### Steps:

1. Logs go to CloudWatch Logs
2. Create **Metric Filter**
3. Create Alarm on that metric
4. Trigger action (SNS, etc.)

---

### 🧠 Example

- Count number of `"ERROR"` logs
- If > 10 → trigger alarm

---

## 8. Testing Alarms (Important Trick)

---

### 🧪 CLI Command

```bash
aws cloudwatch set-alarm-state \
--alarm-name "myalarm" \
--state-value ALARM \
--state-reason "testing purposes"
```

---

### 🧠 Why useful?

- Test notifications without waiting
- Validate SNS / Auto Scaling

---

## 9. Summary Table

---

| Feature | Description |
| --- | --- |
| Alarm | Monitors metric + triggers action |
| States | OK / ALARM / INSUFFICIENT_DATA |
| Period | Evaluation window |
| Targets | EC2 / Auto Scaling / SNS |
| Composite | Combine multiple alarms |
| Recovery | Fix hardware failure |
| Logs Integration | Metric Filter → Alarm |

---

## 10. Common Exam Scenarios

---

### ⚠️ Scenario 1

**"You want to scale EC2 when CPU increases"**

✅ Answer:

→ CloudWatch Alarm + Auto Scaling

---

### ⚠️ Scenario 2

**"Too many alerts, want smarter alerting"**

✅ Answer:

→ Composite Alarms

---

### ⚠️ Scenario 3

**"Underlying hardware failure"**

✅ Answer:

→ EC2 Recovery

---

### ⚠️ Scenario 4

**"Trigger alert based on logs"**

✅ Answer:

→ Metric Filter + Alarm

---

## 11. Final Memory Trick

---

### 🧠 Think:

> **Metric → Alarm → Action**
> 

---

### 🔥 Ultra-Short Recap

- Alarm monitors metrics
- 3 states: OK / ALARM / INSUFFICIENT_DATA
- Actions:
    - EC2
    - Auto Scaling
    - SNS
- Composite alarms reduce noise
- Recovery fixes hardware issues

---

If you want next:

👉 **CloudWatch Dashboards (visualization)**

👉 **Full monitoring architecture (Logs + Metrics + Alarms + Events)**

👉 **Exam cheat sheet (1-page summary)**

Just tell me 👍
