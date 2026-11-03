# 21. Bedrock Guardrails

**Guardrails protect generative-AI applications from undesirable inputs and outputs.**

They can be used to control:

- harmful content
- denied topics
- sensitive information
- PII
- unwanted words/content
- certain image content

Guardrails can evaluate both **user inputs and model responses**.

---

# 22. Guardrails example

Suppose you're building a banking chatbot.

You don't want it to provide certain types of inappropriate financial advice.

Architecture:

```
User
 ↓
Guardrail
 ↓
Bedrock Model
 ↓
Guardrail
 ↓
Response
```

If the input violates your configured policy, it can be blocked.

If the model generates an unacceptable response, that can also be filtered or blocked.

---

# 23. Guardrails + PII

Suppose the user enters:

> "My phone number is XXXXX."
> 

A Guardrail can be configured to help detect and protect sensitive information.

### Exam clue

If you see:

> "Prevent sensitive information from appearing in GenAI input/output."
> 

Think:

**Amazon Bedrock Guardrails**

---

# 24. Guardrails aren't just for Agents

Guardrails can be applied to:

- model inference
- Agents
- Knowledge Bases
- Flows

---

# 25. Bedrock Flows

Bedrock also provides **Flows** for visually/structurally connecting generative-AI components.

Think:

```
Input
 ↓
Prompt
 ↓
Model
 ↓
Knowledge Base
 ↓
Output
```

Instead of manually building every orchestration step yourself.

For SAA-C03, know the concept, but **don't prioritize it over Knowledge Bases, Agents, and Guardrails**.

---

# 26. Prompt Management

Prompt management helps create and manage reusable prompts.

Instead of hardcoding:

```
"Summarize this document in 5 bullets..."
```

throughout an application, prompts can be managed centrally and reused.

### Think

**Prompt Management = reusable/managed prompts**

---

# 27. Provisioned Throughput

This is important when the application requires **predictable model capacity/throughput**.

Normally:

```
Application
 ↓
Bedrock
 ↓
Model
```

With Provisioned Throughput:

```
Application
 ↓
Bedrock
 ↓
Reserved/provisioned model throughput
```

You purchase capacity for a model at a fixed hourly cost, based on the throughput/model units and commitment.

### Scenario

A company has a production AI application that requires:

> "Predictable, consistent model throughput."
> 

Think:

**Bedrock Provisioned Throughput**

---

# 28. On-demand vs Provisioned Throughput

|  | On-Demand | Provisioned Throughput |
| --- | --- | --- |
| Capacity | Use available model capacity | Purchased capacity |
| Cost model | Pay based on usage | Hourly |
| Predictability | Lower | Higher |
| Best for | Variable workloads | Predictable/high-volume workloads |
| Commitment | Generally no capacity commitment | Can involve commitment |
| Exam clue | Normal usage | Need predictable throughput |

---

# 29. Model Customization vs RAG

This is a **very important exam comparison**.

| Requirement | Better concept |
| --- | --- |
| Give model access to private company documents | **RAG / Knowledge Base** |
| Data changes frequently | **RAG** |
| Need answers based on current documents | **RAG** |
| Change/adapt model behavior using training data | **Model customization** |
| Need model to perform application actions | **Agents** |
| Block harmful content/PII | **Guardrails** |
| Predictable model throughput | **Provisioned Throughput** |

### Memory trick

```
KNOW → Knowledge Base
DO   → Agent
SAFE → Guardrails
CHANGE MODEL → Customization
CAPACITY → Provisioned Throughput
```

---

# 30. Bedrock Security

For SAA, security is important.

Think about:

### IAM

IAM controls:

> **Who can use Bedrock resources/APIs?**
> 

Example:

```
Developer
   ↓
IAM permissions
   ↓
Bedrock
```

---

### Encryption

Bedrock supports encryption for data using AWS security mechanisms, including AWS KMS integration where applicable.

Think:

> **Sensitive AI application → encryption + KMS + IAM**
> 

---

### Private connectivity

When designing enterprise architectures, AWS private networking/security mechanisms can be relevant depending on the Bedrock feature and supported configuration.

Don't assume every Bedrock component automatically works through every VPC networking mechanism; always check the specific feature's supported networking model.

---

# 31. Data Privacy — Important Concept

A common concern:

> "Will the company's private prompts/data simply become public training data?"
> 

For exam purposes, remember that Bedrock is designed as an enterprise managed service with AWS security/privacy controls, and customers retain control over their data.

### Scenario

A bank wants GenAI but is concerned about handling confidential data.

Think:

**Amazon Bedrock + IAM + encryption + Guardrails + appropriate data-access controls**

---

# 32. Bedrock + Lambda

A very common architecture:

```
User
 ↓
API Gateway
 ↓
Lambda
 ↓
Amazon Bedrock
 ↓
Foundation Model
 ↓
Response
```

### Use case

Build a serverless AI chatbot.

### Exam clue

If the question says:

> "Build a serverless generative-AI API."
> 

Possible architecture:

**API Gateway + Lambda + Bedrock**

---

# 33. Bedrock + S3

Another common pattern:

```
S3
 │
 │ company documents
 ↓
Knowledge Base
 ↓
Foundation Model
 ↓
AI chatbot
```

### Example

S3 contains:

```
product-manual.pdf
employee-policy.pdf
technical-guide.pdf
```

Bedrock Knowledge Base retrieves relevant information to answer user questions.

---

# 34. Bedrock + DynamoDB

For a conversational application:

```
User
 ↓
API Gateway
 ↓
Lambda
 ├── DynamoDB → application data
 └── Bedrock → AI response
```

Example:

> "What is the status of my order?"
> 

Lambda can retrieve order information from DynamoDB and use Bedrock to generate a natural-language response.

---

# 35. Bedrock + CloudWatch

For production workloads, monitoring and logging become important.

Typical architecture:

```
Application
 ↓
Bedrock
 ↓
Model
 ↓
CloudWatch / logging / monitoring
```

For SAA, the key concept is:

**Bedrock is part of an AWS application architecture, not an isolated AI service.**

---

# 36. Bedrock + IAM

Example:

```
Developer
   ↓
IAM Role
   ↓
Lambda
   ↓
Bedrock
```

The Lambda execution role needs appropriate permissions to invoke the relevant Bedrock APIs/models.

### Exam clue

> "Lambda must invoke a foundation model."
> 

Think:

**IAM permissions + Bedrock Runtime API**

---

# 37. Bedrock vs Amazon Q

Don't confuse these.

|  | Amazon Bedrock | Amazon Q |
| --- | --- | --- |
| Main purpose | Build your own GenAI applications | AWS-managed generative AI assistant |
| Developer platform | **Yes** | Less central |
| Foundation models | Provides access to FMs | Uses AWS-managed GenAI capabilities |
| Custom applications | Excellent | More focused on assistant experiences |
| Example | Build your own customer chatbot | Enterprise assistant / developer assistant |

### Memory

**Bedrock = BUILD AI apps**

**Q = USE an AI assistant**

---

# 38. Bedrock vs Amazon Lex

Another important distinction.

|  | Bedrock | Lex |
| --- | --- | --- |
| Main purpose | Generative AI | Conversational interfaces/chatbots |
| Technology focus | Foundation models / GenAI | Intent/slot-based conversational AI + supported capabilities |
| Flexible text generation | Strong | Not the main focus |
| Example | AI knowledge assistant | "Book my appointment" bot |
| Exam keyword | Foundation model | Intent / slot / chatbot |

### Memory

**Lex = conversation interface**

**Bedrock = foundation-model GenAI**

---

# 39. Bedrock vs Comprehend

|  | Bedrock | Comprehend |
| --- | --- | --- |
| Main focus | Generative AI | NLP analysis |
| Generate new text | Yes | Not primary purpose |
| Sentiment analysis | Can build applications around models | **Comprehend** |
| Entity extraction | Possible through GenAI | **Comprehend** |
| Summarization | **Bedrock** | Not primary |
| Exam clue | Foundation model | Sentiment/entities/classification |

---

# 40. Bedrock vs Rekognition

Easy:

**Rekognition = analyze images/video**

**Bedrock = generative AI applications**

Example:

```
"Detect faces in this image."
→ Rekognition

"Describe this image and write a caption."
→ Suitable Bedrock multimodal model
```

---

# 41. Bedrock vs Textract

**Textract = extract text/data from documents**

Example:

```
PDF invoice
 ↓
Textract
 ↓
Extract:
Invoice number
Price
Date
Customer
```

Then:

```
Textract
 ↓
Bedrock
 ↓
"Summarize this invoice."
```

They can work together.

---

# 42. Bedrock vs Transcribe

**Transcribe = Speech → Text**

Example:

```
Audio
 ↓
Transcribe
 ↓
Text
 ↓
Bedrock
 ↓
Summary
```

This is a very realistic AWS architecture.
