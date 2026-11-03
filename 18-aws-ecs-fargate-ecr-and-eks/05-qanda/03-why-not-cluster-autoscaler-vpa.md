# WHY not Cluster Autoscaler + VPA?

> **HPA creates/removes Pods; Karpenter creates/removes the EC2 capacity needed to run those Pods; VPA makes individual Pods bigger/smaller; Cluster Autoscaler also scales nodes but is less aligned with the “least operational overhead” clue.**
> 

### One sentence to lock it in:

**✅ Option 4 — Metrics Server + HPA**

**✅ Option 1 — Karpenter**

### ⭐ Final answer

| Option | What it scales | Correct? | Why |
| --- | --- | --- | --- |
| **1. Karpenter** | **Nodes / infrastructure** | ✅ | Dynamic node provisioning + low operational overhead |
| **2. CloudWatch Alarms** | Monitoring/triggering | ❌ | Not the appropriate EKS autoscaling layer here |
| **3. Cluster Autoscaler** | **Nodes** | ❌ | Can scale nodes, but more node-group/ASG configuration; Karpenter better matches **least operational overhead** |
| **4. Metrics Server + HPA** | **Pods** | ✅ | Scales application horizontally according to demand |
| **5. Metrics Server + VPA** | **Pod resources** | ❌ | Vertical resource adjustment, not horizontal scale-out/in |

## So for Q21:

---

→ **Cluster Autoscaler**

### **"Automatically adjust number of nodes using existing ASGs/node groups"**

→ **Karpenter**

### **"Automatically provision/scale nodes with minimum operational overhead"**

→ **VPA**

### **"Adjust CPU/memory of Pods"**

→ **HPA**

### **"Scale application Pods"**

When you see:

# 🚨 Exam Decision Rule

---

```

```

Then:

```

```

Memorize this **logic**, not just the answer:

# 🧠 The BEST mental model for SAA-C03

---

### **Karpenter**

Dojo wants:

> **LEAST operational overhead**
> 

Because the question explicitly says:

```

```

But:

It **can absolutely scale nodes**.

```

```

Cluster Autoscaler:

This is the subtle one.

# Why not Cluster Autoscaler?

---

**VPA ❌**

So:

For this exam scenario, **horizontal scaling = more/fewer Pods**.

But the requirement is **scale in and out**.

```

```

Because VPA would do:

# Why not VPA?

---

**Option 1 ✅**

Therefore:

```

```

Then:

---

**Option 4 ✅**

Therefore:

### **HPA**

Who does that?

```

```

What does that normally mean?

Traffic increases.

> **"website experiences a surge in traffic around the holidays"**
> 

The question says:

# 🎯 Now apply this to the question

---

**VPA → more CPU/memory per Pod**

In Kubernetes:

```

```

You don't want more restaurants. You want the existing restaurant to become bigger.

### Vertical scaling

---

**HPA → more Pods**

In Kubernetes:

**Add more copies.**

```

```

You need more capacity because customers increased.

### Horizontal scaling

Imagine you own a restaurant.

# 🔥 The key difference: Horizontal vs Vertical

---

That's **vertical scaling**.

> **"This Pod needs more CPU/memory."**
> 

It's basically saying:

```

```

For example:

Instead, VPA changes the **resources allocated/requested by an individual Pod**.

**VPA does NOT primarily increase the number of Pods.**

This one is easier to eliminate.

> Install Metrics Server + activate Vertical Pod Autoscaler.
> 

# ❌ Option 5 — Vertical Pod Autoscaler

---

👉 **Karpenter is preferred.**

### **"LEAST operational overhead"**

Therefore, when the exam adds:

> **Karpenter = "Give me the compute capacity my Pods actually need."**
> 

> **Cluster Autoscaler = "Scale my predefined node groups."**
> 

So:

```

```

Karpenter is more dynamic:

You generally need to configure/manage those node groups and their instance types.

```

```

### Cluster Autoscaler generally works around existing Auto Scaling Groups/node groups:

And this is where **Karpenter beats Cluster Autoscaler** in the intended Dojo scenario.

> **"with the LEAST amount of operational overhead"**
> 

### Because the question asks:

So why isn't it the answer?

```

```

Cluster Autoscaler does:

That's why this option is confusing.

**YES. Absolutely.**

### Is this technically capable of scaling nodes?

> **Enable Cluster Autoscaler for EKS to automatically manage the number of nodes based on the resource needs of the Pods.**
> 

## ❌ Option 3 — Cluster Autoscaler

# Now your two options

---

```

```

> "Do I have enough EC2 machines to run those Pods?"
> 

### ② Node scaling → Karpenter / Cluster Autoscaler

```

```

> "How many copies of my application do I need?"
> 

### ① Pod scaling → HPA

So there are **two different things that can scale**:

```

```

An EKS application has:

## 🧠 First: Understand the 2 layers

Yes. The easiest way to understand this question is to **separate EKS autoscaling into 2 layers**.
