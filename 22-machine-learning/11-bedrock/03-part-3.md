# 43. Complete Bedrock Architecture

Here's a useful architecture to remember for SAA:

```
                       USER
                         │
                         ▼
                  CloudFront / App
                         │
                         ▼
                    API Gateway
                         │
                         ▼
                       Lambda
                         │
             ┌───────────┼───────────┐
             │           │           │
             ▼           ▼           ▼
          Bedrock     DynamoDB       S3
             │
      ┌──────┼──────────────┐
      │      │              │
      ▼      ▼              ▼
     FM   Guardrails   Knowledge Base
                              │
                              ▼
                             RAG
                              │
                              ▼
                         Private Data
```

This is a **serverless GenAI application pattern**.

---

# 44. Bedrock Agent Architecture

```
                         User
                           │
                           ▼
                    Bedrock Agent
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
       Knowledge Base              Action Group
              │                         │
              ▼                         ▼
         Private Data                 Lambda
                                        │
                                        ▼
                                Company API / DB
```

### Remember:

**Knowledge Base → retrieve information**

**Action Group → perform action**

---

# 45. Bedrock RAG Architecture

```
                 Company Documents
                        │
                        ▼
                       S3
                        │
                        ▼
               Bedrock Knowledge Base
                        │
                  Embeddings /
                 Vector Retrieval
                        │
                        ▼
                  Relevant Context
                        │
                        ▼
                  Foundation Model
                        │
                        ▼
                       Answer
```

### Example

User:

> "What is our company's remote-work policy?"
> 

Knowledge Base retrieves relevant policy content.

Bedrock generates the answer using that context.

---

# 46. Bedrock Guardrails Architecture

```
User
 │
 ▼
Guardrail
 │
 ├── Block harmful input
 ├── Filter denied topics
 ├── Detect sensitive information
 │
 ▼
Foundation Model
 │
 ▼
Guardrail
 │
 ├── Filter unsafe response
 └── Mask sensitive output
 │
 ▼
User
```

---

# 47. The Most Important Bedrock Decision Table

| Question asks for... | Think |
| --- | --- |
| Build GenAI application | **Amazon Bedrock** |
| Access different foundation models | **Bedrock Model Catalog** |
| Generate text/images | **Foundation Model inference** |
| Use private company data | **Knowledge Base** |
| RAG | **Knowledge Base** |
| Semantic retrieval | **Embeddings / Knowledge Base** |
| AI should perform actions | **Bedrock Agent** |
| Agent calls APIs | **Action Group + Lambda/API** |
| Prevent harmful content | **Guardrails** |
| Protect PII | **Guardrails** |
| Compare models | **Model Evaluation** |
| Adapt model using training data | **Model customization** |
| Predictable/high throughput | **Provisioned Throughput** |
| Build custom ML models | **SageMaker AI** |
| AI assistant for users | **Amazon Q** |
| Intent/slot conversational bot | **Amazon Lex** |
| Sentiment/entity analysis | **Amazon Comprehend** |
| Image/video analysis | **Amazon Rekognition** |
| Extract text from documents | **Amazon Textract** |
| Speech → text | **Amazon Transcribe** |

---

# 48. The "Which Bedrock Feature?" Exam Trick

When you see a scenario, ask:

### Question 1 — Does it need GenAI?

```
YES
 ↓
Bedrock
```

### Question 2 — Does it need private knowledge?

```
YES
 ↓
Knowledge Base / RAG
```

### Question 3 — Does AI need to DO something?

```
YES
 ↓
Agent
 ↓
Action Group
 ↓
Lambda/API
```

### Question 4 — Does it need safety filtering?

```
YES
 ↓
Guardrails
```

### Question 5 — Does it need predictable throughput?

```
YES
 ↓
Provisioned Throughput
```

### Question 6 — Does the company need to change the model itself?

```
YES
 ↓
Model customization
```

---

# 49. Bedrock vs RAG vs Agent vs Guardrail

This is probably the **single most useful table for your exam revision**:

| Feature | Purpose | Example |
| --- | --- | --- |
| **Bedrock** | Build GenAI apps | Customer AI chatbot |
| **Foundation Model** | Generate AI output | Answer user's question |
| **Knowledge Base** | Retrieve private information | Search company policies |
| **RAG** | Ground model response in retrieved data | Answer using latest company documents |
| **Agent** | Reason/orchestrate tasks | Cancel an order |
| **Action Group** | Define actions Agent can invoke | `CancelOrder()` |
| **Lambda** | Execute backend logic | Call order API |
| **Guardrails** | Control unsafe/sensitive input/output | Block PII/harmful content |
| **Model Evaluation** | Assess model quality | Compare Model A vs Model B |
| **Customization** | Adapt model with training data | Specialized model behavior |
| **Provisioned Throughput** | Predictable model capacity | High-volume production application |

---

# 50. SAA-C03 Scenario Examples

### Scenario 1

> A company wants to build a customer-facing generative AI chatbot without managing ML infrastructure.
> 

**Answer → Amazon Bedrock**

---

### Scenario 2

> The chatbot needs to answer questions using thousands of internal PDF documents stored in Amazon S3.
> 

**Answer → Bedrock Knowledge Base / RAG**

---

### Scenario 3

> The AI assistant needs to retrieve an order and cancel it through the company's API.
> 

**Answer → Bedrock Agent + Action Group + Lambda/API**

---

### Scenario 4

> The company wants to prevent the AI from returning personally identifiable information.
> 

**Answer → Bedrock Guardrails**

---

### Scenario 5

> The company needs predictable model throughput for a production workload.
> 

**Answer → Bedrock Provisioned Throughput**

---

### Scenario 6

> The company wants to compare several foundation models to determine which performs best for its application.
> 

**Answer → Bedrock Model Evaluation**

---

### Scenario 7

> The company wants to use frequently changing internal information without retraining the foundation model every time the information changes.
> 

**Answer → Knowledge Base / RAG**

---

### Scenario 8

> A data-science team wants to train and deploy a custom machine-learning model with extensive control over the ML lifecycle.
> 

**Answer → SageMaker AI**, not Bedrock.

---

# 51. Common Exam Traps

### Trap 1

**"Private company data" doesn't automatically mean fine-tuning.**

If the objective is:

> "Answer questions using company documents."
> 

Think:

**RAG / Knowledge Base**

---

### Trap 2

**Agent ≠ Knowledge Base**

Knowledge Base:

> "Find information."
> 

Agent:

> "Perform tasks."
> 

---

### Trap 3

**Guardrails ≠ model training**

Guardrails:

> "Control what goes in/out."
> 

They don't train the model.

---

### Trap 4

**Bedrock ≠ SageMaker**

```
GenAI application
→ Bedrock

Train/customize/deploy ML
→ SageMaker AI
```

---

### Trap 5

**Bedrock ≠ Amazon Q**

```
Build your own GenAI application
→ Bedrock

Use AWS's managed AI assistant experience
→ Amazon Q
```

---

# 52. One Real-World Example — Put Everything Together

Imagine **CloudShop**, an online store.

CloudShop wants an AI support assistant.

### Requirement 1

Customers ask questions naturally.

→ **Amazon Bedrock**

### Requirement 2

Answers must use CloudShop's product manuals.

→ **Knowledge Base + RAG**

### Requirement 3

Product manuals are stored in S3.

→ **S3 → Knowledge Base**

### Requirement 4

Customer asks:

> "Cancel my order."
> 

AI must actually cancel the order.

→ **Bedrock Agent**

### Requirement 5

Agent needs to call the cancellation API.

→ **Action Group + Lambda/API**

### Requirement 6

CloudShop doesn't want sensitive information returned.

→ **Bedrock Guardrails**

### Architecture

```
                        Customer
                           │
                           ▼
                    CloudShop Website
                           │
                           ▼
                     API Gateway
                           │
                           ▼
                         Lambda
                           │
                           ▼
                   Amazon Bedrock
                           │
                ┌──────────┼───────────┐
                │          │           │
                ▼          ▼           ▼
             Agent     Guardrails   Knowledge
                                      Base
                                        │
                                        ▼
                                       S3
                                  Product Manuals

                Agent
                  │
                  ▼
             Action Group
                  │
                  ▼
               Lambda
                  │
                  ▼
            Order API / DB
```

Now you understand **why each component exists**, instead of memorizing isolated definitions.

---

# 53. Final SAA-C03 Memory Map

Memorize this:

```
                 AMAZON BEDROCK
                       │
       ┌───────────────┼────────────────┐
       │               │                │
       ▼               ▼                ▼
   Foundation      Knowledge          Agents
     Models          Base               │
       │               │                ▼
       │               ▼           Action Groups
       │              RAG                │
       │               │                ▼
       │               ▼             Lambda/API
       │          Private Data
       │
       ├──── Guardrails → SAFETY
       │
       ├──── Evaluation → COMPARE
       │
       ├──── Customization → ADAPT MODEL
       │
       └──── Provisioned Throughput → PREDICTABLE CAPACITY
```

## 🧠 The ultimate memory trick

> **BEDROCK = BUILD**
> 

> **KNOWLEDGE BASE = KNOW**
> 

> **RAG = RETRIEVE**
> 

> **AGENT = DO**
> 

> **ACTION GROUP = HOW TO DO**
> 

> **GUARDRAIL = SAFE**
> 

> **SAGEMAKER = TRAIN/CUSTOMIZE ML**
> 

> **PROVISIONED THROUGHPUT = PREDICTABLE CAPACITY**
> 

### SAA-C03 exam takeaway

**Amazon Bedrock is the managed AWS platform for building generative-AI applications with foundation models. Use Knowledge Bases/RAG when the model needs private or frequently changing information, Agents when the AI must perform actions through APIs, Guardrails for safety and sensitive-data controls, model evaluation to compare performance, customization to adapt a model, and Provisioned Throughput when predictable model capacity is required.**

For your Tutorial Dojo preparation, **prioritize Knowledge Bases/RAG, Agents, Guardrails, Bedrock vs SageMaker, and Bedrock architecture patterns first**. Those are the concepts most useful for solving scenario questions rather than simply recognizing the service name
