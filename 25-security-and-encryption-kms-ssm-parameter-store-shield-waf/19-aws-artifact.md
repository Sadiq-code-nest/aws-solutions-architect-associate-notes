# AWS Artifact — SAA-C03 Short Exam Note

### What is AWS Artifact?

**AWS Artifact** is a **self-service portal for downloading AWS security, compliance, and legal documents**.

Think:

> **Artifact = AWS's compliance-document library**
> 

It helps you prove that AWS meets certain **security, privacy, and compliance requirements**.

---

### What problem does it solve?

Organizations often need documents to answer questions like:

- Is AWS compliant with **SOC, PCI DSS, ISO**, etc.?
- Can I provide compliance evidence to my auditor?
- What AWS legal agreements apply to my organization?
- Can I download AWS compliance reports?

Instead of contacting AWS Support, you can access eligible documents directly through **AWS Artifact**.

---

### What can you get from Artifact?

| Category | What you get |
| --- | --- |
| **Compliance reports** | AWS audit/compliance reports |
| **Certifications** | Evidence of AWS compliance certifications |
| **Security documents** | Security and compliance-related documentation |
| **Agreements** | AWS legal agreements |
| **Compliance evidence** | Documents useful for auditors and regulators |

Two important areas to remember:

**1. Artifact Reports**

- Download AWS compliance reports and certifications.
- Examples: **SOC reports, PCI reports, ISO certifications**.

**2. Artifact Agreements**

- Review and accept certain AWS agreements.
- Useful when an organization has specific regulatory/legal requirements.

---

## Real-world scenario

Imagine a **bank** is moving its application to AWS.

The bank's auditor asks:

> "Show me evidence that the cloud provider meets the required security and compliance standards."
> 

The architect can go to:

**AWS Console → AWS Artifact → Reports**

and obtain the relevant AWS compliance documentation.

The architect **doesn't configure security controls using Artifact**.

Artifact simply provides the **official AWS compliance evidence/documents**.

---

## 🧠 SAA-C03 Exam Clues

If the question says:

- **Compliance documentation**
- **Audit reports**
- **AWS certifications**
- **SOC reports**
- **PCI DSS**
- **ISO**
- **Legal agreements**
- **Need evidence for auditors**
- **Download AWS compliance reports**

👉 **Think AWS Artifact**

### Memory Trick

> 📄 **Artifact = AWS's compliance paperwork**
> 

Or simply:

> **"Need AWS compliance documents? → Artifact."**
> 

### Don't confuse it

| Service | Main purpose |
| --- | --- |
| **AWS Artifact** | 📄 Compliance reports & agreements |
| **AWS Audit Manager** | 🔍 Collect evidence to audit **your AWS environment** |
| **AWS Trusted Advisor** | 💡 AWS account best-practice recommendations |
| **AWS Security Hub** | 🛡️ Centralize security findings |
| **AWS Config** | 📋 Check resource configuration/compliance |

**Most important distinction for SAA-C03:**

> **Artifact → AWS's compliance documents**
> 
> 
> **Audit Manager → evidence about your own AWS environment**
>
