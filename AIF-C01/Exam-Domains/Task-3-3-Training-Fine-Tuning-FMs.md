# Task Statement 3.3: Describe the Training and Fine-Tuning Process for FMs

## 1. Topic Overview
- **Purpose**: Understand the full lifecycle of how foundation models are built (pre-training) and customized (fine-tuning, continued pre-training, distillation) — including data preparation, methods, and AWS services
- **Service Level**: Regional (Bedrock customization jobs run in specific AWS regions)
- **Key Use Cases**:
  - Adapting a pre-trained FM to a specific domain or task
  - Teaching a model domain-specific vocabulary from unlabeled text
  - Creating smaller, cost-efficient models via distillation
  - Aligning model outputs with human preferences via RLHF
  - Preparing and curating high-quality training datasets
- **Exam Weight**: HIGH
- **Exam Domain**: Domain 3 — Applications of Foundation Models (28%)
- **Task Statements** (Official AIF-C01 Objectives):
  - **3.3.1**: Describe the key elements of training an FM (pre-training, fine-tuning, continuous pre-training, distillation)
  - **3.3.2**: Define methods for fine-tuning an FM (instruction tuning, domain adaptation, transfer learning, continuous pre-training)
  - **3.3.3**: Describe how to prepare data to fine-tune an FM (data curation, governance, size, labeling, representativeness, RLHF)

## 2. Exam Keyword Mapping
- **Trigger Words**: "pre-training", "fine-tuning", "continued pre-training", "continuous pre-training", "distillation", "instruction tuning", "domain adaptation", "transfer learning", "RLHF", "reinforcement fine-tuning", "labeled data", "unlabeled data", "data curation", "data governance", "LoRA", "PEFT", "teacher-student"
- **Scenario Indicators**:
  - "Model needs domain-specific vocabulary from large text corpus" → Continued pre-training
  - "Model needs specific output format/style with labeled examples" → Supervised fine-tuning
  - "Need cheaper/faster model with similar quality" → Distillation
  - "Can define measurable success criteria but lack labeled outputs" → Reinforcement fine-tuning
  - "Need to align model with human preferences" → RLHF / Reinforcement fine-tuning
  - "What should you try first?" → Prompt engineering (always)
- **Anti-patterns**:
  - ❌ Don't jump to fine-tuning — always try prompt engineering and RAG first
  - ❌ Don't use fine-tuning for frequently changing information — use RAG
  - ❌ Don't use continued pre-training when you need task-specific behavior — use fine-tuning
  - ❌ Don't confuse fine-tuning (labeled data) with continued pre-training (unlabeled data)

---

## 3. Objective 3.3.1 — Key Elements of Training an FM

### 3.1 Pre-Training (Building the Foundation)
The initial, most expensive phase where a model learns general language understanding from massive datasets.

| Aspect | Details |
|--------|---------|
| **What** | Train a model from scratch on massive unlabeled text corpora (internet text, books, code) |
| **Data** | Trillions of tokens of unlabeled text |
| **Objective** | Learn general language patterns, grammar, facts, reasoning |
| **Cost** | Extremely high (millions of dollars, weeks/months of GPU time) |
| **Who does it** | Model providers (Amazon, Anthropic, Meta, etc.) — NOT typical customers |
| **Result** | A foundation model (FM) with broad general knowledge |
| **AWS Service** | SageMaker HyperPod (if building custom FM from scratch) |

**Key Exam Point**: Pre-training is done by model providers. As a customer, you typically start with a pre-trained FM and customize it.

### 3.2 Fine-Tuning (Supervised Fine-Tuning / SFT) ⭐
The most common customization method. Provide labeled input-output pairs to teach the model specific task behavior.

| Aspect | Details |
|--------|---------|
| **What** | Further train a pre-trained FM on labeled input-output pairs |
| **Data** | Curated dataset of prompt-completion pairs (labeled) |
| **Objective** | Teach specific task behavior, style, tone, format |
| **Cost** | Medium-High |
| **Result** | Model parameters adjusted; improved performance for target tasks |
| **AWS Services** | Amazon Bedrock, SageMaker JumpStart, SageMaker Canvas (no-code) |

**Data Format (JSONL for Bedrock)**:
```json
{"prompt": "Summarize this medical report:", "completion": "Patient presents with..."}
{"prompt": "Translate to formal tone:", "completion": "We would like to inform you..."}
```

**Conversational Format (Converse API for Claude, Llama 3.2+)**:
```json
{"system": [{"text": "You are a helpful assistant"}], "messages": [{"role": "user", "content": [{"text": "What is AWS?"}]}, {"role": "assistant", "content": [{"text": "AWS is..."}]}]}
```

### 3.3 Continued Pre-Training (CPT) / Continuous Pre-Training ⭐
Extends the pre-training phase with domain-specific unlabeled text.

| Aspect | Details |
|--------|---------|
| **What** | Further train a pre-trained FM on large amounts of unlabeled domain-specific text |
| **Data** | Large corpus of unlabeled text (tens of billions of tokens ideal) |
| **Objective** | Teach domain vocabulary, terminology, writing patterns |
| **Cost** | High (more data, longer training than fine-tuning) |
| **Result** | Model acquires deeper domain knowledge |
| **AWS Service** | Amazon Bedrock (Nova 1.0 Micro/Lite/Pro, Nova 2.0 Lite) |
| **Important** | After CPT, model typically needs additional instruction tuning to use the new knowledge |

```
Pre-trained FM → CPT (domain knowledge) → Fine-Tuning (task behavior) → Deployment
                  Unlabeled domain text     Labeled input-output pairs
```

> **Source**: [Continued pre-training (CPT) - Amazon Nova](https://docs.aws.amazon.com/nova/latest/nova2-userguide/nova-cpt.html)

### 3.4 Distillation (Teacher → Student) ⭐
Transfers knowledge from a larger "teacher" model to a smaller "student" model.

| Aspect | Details |
|--------|---------|
| **What** | Teacher generates responses → Bedrock applies data synthesis → Student is fine-tuned on teacher responses |
| **Data** | Prompts in `.jsonl` files OR invocation logs from production |
| **Objective** | Create a smaller, faster, cheaper model with similar quality |
| **Cost** | Medium (teacher inference charges + student training) |
| **Result** | Smaller model retaining much of the teacher's capability |
| **AWS Service** | Amazon Bedrock |

**Supported Teacher → Student Pairings**:
| Teacher | Student(s) |
|---------|-----------|
| Nova Premier | Nova Pro, Nova Lite, Nova Micro |
| Nova Pro | Nova Lite, Nova Micro |
| Claude 3.5 Sonnet | Claude 3 Haiku |
| Llama 3.1 405B | Llama 3.1 8B/70B, Llama 3.2 1B, Llama 3.3 70B |
| Llama 3.1 70B | Llama 3.1 8B, Llama 3.2 1B/3B |

> ⚠️ Teacher must always be **larger** than student. Cross-provider pairings are **not supported**.

> **Source**: [Customize a model with distillation in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-distillation.html)

### Master Comparison Table ⭐⭐⭐

| Element | Data Type | Modifies Weights | Cost | Time | Purpose |
|---------|-----------|:-:|---|---|---|
| **Pre-Training** | Massive unlabeled corpus | ✅ (from scratch) | Very High | Weeks-Months | Build general knowledge |
| **Fine-Tuning (SFT)** | Labeled input-output pairs | ✅ | Medium | Hours | Task-specific behavior |
| **Continued Pre-Training** | Large unlabeled domain text | ✅ | High | Days | Domain vocabulary/knowledge |
| **Distillation** | Prompts (teacher responds) | ✅ | Medium | Hours | Smaller/cheaper model |

---

## 4. Objective 3.3.2 — Methods for Fine-Tuning an FM

### 4.1 Instruction Tuning
- **What**: Fine-tuning with instruction-formatted data to improve the model's ability to follow diverse instructions
- **Data**: Instructions paired with desired outputs
- **Example**: `"Summarize the following text in 3 bullet points: [text]"` → `[3 bullet summary]`
- **Result**: Model becomes better at following varied instructions across tasks
- **Key Point**: This is how most modern chat models (Claude, GPT, Llama-Instruct) are created from base models

### 4.2 Domain Adaptation
- **What**: Adapting a general-purpose FM to perform well in a specific domain
- **Two approaches**:
  1. **Continued Pre-Training (CPT)**: Feed unlabeled domain text to teach vocabulary and patterns
  2. **Domain-Specific Fine-Tuning**: Use labeled domain examples to teach task behavior
- **Common pattern**: CPT first (domain knowledge) → then fine-tuning (task behavior)
- **Examples**: Medical AI, legal document analysis, financial report generation

### 4.3 Transfer Learning
- **What**: Using a pre-trained model as a starting point and adapting it to a new task
- **Key Insight**: Fine-tuning IS a form of transfer learning — the model transfers its general knowledge to a specific task
- **Benefits**: Reduces training time and data requirements compared to training from scratch
- **Exam Point**: When the exam mentions "transfer learning" in the context of FMs, it refers to the concept of leveraging pre-trained knowledge for new tasks

### 4.4 Reinforcement Fine-Tuning (RFT) — NEW in Bedrock
- **What**: Feedback-driven approach using reward functions instead of labeled datasets
- **Data**: Training prompts + reward functions (no labeled output pairs needed)
- **How**: Model generates multiple responses → Reward functions score them → Model optimized via GRPO (Group Relative Policy Optimization)
- **When to use**: You can define measurable success criteria but collecting labeled examples is expensive
- **Reward Functions**: Defined using AWS Lambda (custom code) or model-as-a-judge grader
- **Supported Models**: Nova 2 Lite, gpt-oss-20B, Qwen3 32B

| Aspect | Supervised Fine-Tuning | Reinforcement Fine-Tuning |
|--------|----------------------|-----------------------------|
| Data | Labeled input-output pairs | Prompts + reward functions |
| Learning | Learns from examples | Learns from feedback scores |
| Best When | You know the ideal output | You can measure output quality |
| Algorithm | Standard supervised learning | GRPO (policy optimization) |

> **Source**: [Customize a model with reinforcement fine-tuning in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/reinforcement-fine-tuning.html)

### 4.5 Parameter-Efficient Fine-Tuning (PEFT) / LoRA
- **What**: Freezes original model weights and adds small trainable adapter matrices
- **Benefit**: 10-100x fewer trainable parameters, much lower GPU memory and cost
- **LoRA (Low-Rank Adaptation)**: Most common PEFT method — decomposes weight updates into low-rank matrices
- **QLoRA**: Combines LoRA with 4-bit quantization for even lower memory
- **Exam Tip**: If exam mentions "efficient fine-tuning" or "low resource fine-tuning" → LoRA/PEFT

### Methods Summary

| Method | Data Required | What It Teaches | AWS Service |
|--------|-------------|-----------------|-------------|
| **Instruction Tuning** | Instruction-output pairs | Follow diverse instructions | Bedrock, SageMaker |
| **Domain Adaptation (CPT)** | Unlabeled domain text | Domain vocabulary/patterns | Bedrock |
| **Domain Adaptation (SFT)** | Labeled domain examples | Domain task behavior | Bedrock, SageMaker |
| **Transfer Learning** | Task-specific data | New task from pre-trained base | SageMaker JumpStart |
| **Reinforcement Fine-Tuning** | Prompts + reward functions | Optimize for measurable criteria | Bedrock |
| **LoRA/PEFT** | Same as SFT (labeled pairs) | Same as SFT, but efficiently | SageMaker JumpStart |

---

## 5. Objective 3.3.3 — Preparing Data to Fine-Tune an FM

### 5.1 Data Curation
- **Quality > Quantity**: 1,000 high-quality examples often beats 10,000 noisy ones
- **Consistency**: Uniform formatting across all examples
- **Diversity**: Representative of real-world use cases and edge cases
- **Relevance**: Examples should match the target task closely
- **Deduplication**: Remove duplicate or near-duplicate examples
- **Format**: `.jsonl` files (JSON Lines) — each line is one training example

### 5.2 Data Governance
- **PII handling**: Remove or mask personally identifiable information unless necessary and properly handled
- **Data privacy**: Training data in Bedrock is private — never shared with model providers or used to train other models
- **Compliance**: Ensure training data complies with regulatory requirements (HIPAA, GDPR, etc.)
- **Access control**: Use IAM roles with least-privilege access to S3 training data
- **Encryption**: Encrypt training data at rest (SSE-S3 or SSE-KMS) and in transit
- **Audit**: Use CloudTrail for logging customization operations
- **Data lineage**: Track data sources and transformations

### 5.3 Data Size
- **Minimum**: Varies by model (e.g., Bedrock Canvas requires 100+ pairs; Claude 3 Haiku requires 32+)
- **Maximum**: Up to 10,000 records for most Bedrock models (adjustable via Service Quotas)
- **Token limits**: Vary by model (e.g., 4,096 tokens for Titan, 16,000 for Llama 3.2, 32,000 for Claude 3 Haiku)
- **File size**: Training dataset up to 1 GB (10 GB for Claude 3 Haiku)
- **More data** generally improves quality, but with diminishing returns
- **Fewer epochs** needed with larger datasets; more epochs with smaller datasets (but risk overfitting)

### 5.4 Data Labeling
- **Supervised fine-tuning**: Requires labeled input-output pairs (prompt → completion)
- **Continued pre-training**: Does NOT require labels — uses raw unlabeled text
- **Reinforcement fine-tuning**: Requires prompts + reward functions (not traditional labels)
- **Distillation**: Requires prompts only — teacher model generates the "labels"
- **Labeling tools**: Amazon SageMaker Ground Truth, SageMaker Ground Truth Plus (managed labeling workforce)
- **Human annotators**: For RLHF preference data (ranking model responses)

### 5.5 Data Representativeness
- **Balanced**: Training data should represent all categories/topics proportionally
- **Bias awareness**: Unrepresentative data leads to biased model outputs
- **Edge cases**: Include examples of unusual or difficult inputs
- **Domain coverage**: Ensure all relevant sub-domains are represented
- **Demographic diversity**: For models serving diverse populations, ensure training data reflects that diversity

### 5.6 RLHF (Reinforcement Learning from Human Feedback)
A multi-step process to align model behavior with human preferences:

```
Step 1: Generate multiple responses to the same prompt
Step 2: Human annotators rank responses (preferred vs. not preferred)
Step 3: Train a reward model on human preference data
Step 4: Use reinforcement learning (PPO) to optimize the FM against the reward model
```

| Aspect | Details |
|--------|---------|
| **Purpose** | Align model with human values, safety, helpfulness |
| **Data** | Human preference rankings (response A > response B) |
| **Algorithm** | PPO (Proximal Policy Optimization) with reward model |
| **Alternative** | DPO (Direct Preference Optimization) — simpler, no separate reward model |
| **AWS Tools** | SageMaker Ground Truth Plus for preference data collection |
| **Used By** | ChatGPT, Claude (during their training) |

### Data Preparation Checklist ✅

| Requirement | Fine-Tuning (SFT) | Continued Pre-Training | Distillation | Reinforcement FT |
|-------------|:-:|:-:|:-:|:-:|
| Labeled input-output pairs | ✅ Required | ❌ Not needed | ❌ Optional | ❌ Not needed |
| Unlabeled domain text | ❌ | ✅ Required | ❌ | ❌ |
| Prompts only | ❌ | ❌ | ✅ Required | ✅ Required |
| Reward functions | ❌ | ❌ | ❌ | ✅ Required |
| JSONL format | ✅ | ✅ | ✅ | ✅ |
| Stored in S3 | ✅ | ✅ | ✅ | ✅ |
| IAM role | ✅ | ✅ | ✅ | ✅ |

---

## 6. AWS Services for FM Training & Fine-Tuning

### The Customization Hierarchy ⭐⭐⭐
AWS recommends a progressive approach — start simple, escalate only when needed:

```
┌──────────────────────────────────────────────────────────────────────────┐
│  CUSTOMIZATION HIERARCHY (Least → Most Resource-Intensive)              │
│                                                                          │
│  1. PROMPT ENGINEERING        Cost: Free    | Data: None                │
│     ↓ Not sufficient?         Time: Minutes | Modifies Weights: No     │
│                                                                          │
│  2. RAG                       Cost: Low     | Data: Knowledge docs      │
│     ↓ Not sufficient?         Time: Hours   | Modifies Weights: No     │
│                                                                          │
│  3. FINE-TUNING (SFT)         Cost: Medium  | Data: Labeled pairs       │
│     ↓ Not sufficient?         Time: Hours   | Modifies Weights: Yes    │
│                                                                          │
│  4. CONTINUED PRE-TRAINING    Cost: High    | Data: Unlabeled corpus    │
│     ↓ Not sufficient?         Time: Days    | Modifies Weights: Yes    │
│                                                                          │
│  5. CUSTOM FM (from scratch)  Cost: Highest | Data: Massive dataset     │
│                               Time: Weeks   | Modifies Weights: Yes    │
└──────────────────────────────────────────────────────────────────────────┘
```

> **Source**: [GENOPS05-BP01 Learn when to customize models](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/genops05-bp01.html)

### AWS Service Comparison

| Service | Code Required? | Control Level | Best For |
|---------|:-:|---|---|
| **Amazon Bedrock** | Minimal (console/API) | Managed | SFT, RFT, Distillation, CPT — quick, managed experience |
| **SageMaker JumpStart** | Some (SDK) | Medium | More control, wider model selection, LoRA/PEFT support |
| **SageMaker Canvas** | None (visual) | Low | Business users, no-code fine-tuning |
| **SageMaker HyperPod** | Yes (advanced) | Full | Custom FM training from scratch at scale |

### Bedrock Customization: Key Details
- **Pricing**: Tokens processed × epochs (training) + per-model-per-month (storage)
- **Deployment**: Custom models require **Provisioned Throughput** for inference (not on-demand)
  - Exception: Nova custom models support **on-demand inference** deployment
- **Privacy**: Custom models are private — your data is never shared with model providers
- **Hyperparameters**: epochCount, learningRate, batchSize, learningRateWarmupSteps, earlyStoppingThreshold, earlyStoppingPatience
- **AWS Recommendation**: Start with default hyperparameters — they're optimized across tasks

> **Source**: [Customize your model to improve its performance - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/custom-models.html)

---

## 7. Exam Focus Areas

### Common Question Patterns

| Question Pattern | Answer |
|-----------------|--------|
| "What to try first when model isn't performing well?" | Prompt engineering |
| "Model needs up-to-date factual information" | RAG |
| "Model needs specific output format/style with labeled examples" | Supervised fine-tuning |
| "Need model to understand medical/legal terminology from large text corpus" | Continued pre-training |
| "Need cheaper/faster model with similar quality" | Distillation |
| "Can define measurable success criteria but lack labeled outputs" | Reinforcement fine-tuning |
| "Need to align model with human preferences" | RLHF / Reinforcement fine-tuning |
| "Transfer knowledge from large model to small model" | Distillation |
| "Fine-tune with limited compute resources" | LoRA / PEFT |
| "No-code fine-tuning for business users" | SageMaker Canvas |
| "What data format for Bedrock fine-tuning?" | JSONL stored in S3 |
| "Labeled vs. unlabeled data?" | Fine-tuning = labeled; CPT = unlabeled |

### Gotchas ⚠️
1. **Labeled vs. Unlabeled**: Fine-tuning uses **labeled** data; continued pre-training uses **unlabeled** data — this is a common exam trap
2. **Fine-tuning ≠ RAG**: Fine-tuning changes model **behavior/style**; RAG provides **factual knowledge**. They serve different purposes and can be combined
3. **Provisioned Throughput**: Custom models in Bedrock require **Provisioned Throughput** for inference (except Nova custom models which support on-demand)
4. **CPT needs follow-up**: After continued pre-training, the model typically needs additional **instruction tuning** to be useful
5. **Cross-provider distillation**: NOT supported — teacher and student must be from the same provider family
6. **Fine-tuning doesn't add real-time knowledge**: It bakes in behavior at training time. For current information, use RAG
7. **Data never shared**: Custom models in Bedrock are private — your data is never used to train other models
8. **RFT prerequisites**: If baseline rewards are 0%, use SFT first before attempting reinforcement fine-tuning
9. **Transfer learning**: Fine-tuning IS a form of transfer learning — leveraging pre-trained knowledge for new tasks
10. **Prompt tuning ≠ prompt engineering**: Prompt tuning learns continuous vectors (not human-readable text); prompt engineering writes text prompts

### Decision Tree
```
Model not meeting requirements?
│
├─ Try PROMPT ENGINEERING first (free, fast)
│   ├─ Few-shot examples
│   ├─ Chain-of-thought prompting
│   └─ System prompts
│
├─ Need factual/up-to-date/proprietary knowledge?
│   └─ RAG (Bedrock Knowledge Bases)
│
├─ Need domain-specific vocabulary from large text corpus?
│   └─ CONTINUED PRE-TRAINING (unlabeled data)
│       └─ Then fine-tune for specific tasks
│
├─ Need specific task behavior/style?
│   ├─ Have labeled input-output pairs?
│   │   └─ SUPERVISED FINE-TUNING
│   │       ├─ Limited compute? → LoRA / PEFT
│   │       └─ No-code? → SageMaker Canvas
│   └─ Can define measurable success criteria?
│       └─ REINFORCEMENT FINE-TUNING
│
├─ Need smaller/cheaper model with similar quality?
│   └─ DISTILLATION (teacher → student)
│
├─ Need human preference alignment?
│   └─ RLHF or REINFORCEMENT FINE-TUNING
│
└─ None of the above work?
    └─ BUILD CUSTOM FM (SageMaker HyperPod)
```

---

## 8. Sample Questions

### Question 1
A company has a large collection of proprietary legal documents (contracts, case law, regulations) but no labeled input-output pairs. They want their foundation model to understand legal terminology. Which customization technique is most appropriate?

A) Supervised fine-tuning with the legal documents  
B) Continued pre-training on the legal document corpus  
C) Model distillation using a larger legal-trained model  
D) Reinforcement fine-tuning with legal accuracy reward functions  

**✅ Answer: B**  
Continued pre-training uses **unlabeled** domain-specific text to teach the model domain vocabulary and terminology. The company has a large corpus of legal documents (unlabeled), making CPT the ideal choice. Option A requires labeled input-output pairs, which they don't have. Option C requires a pre-existing legal-trained teacher model. Option D requires reward functions but doesn't address the fundamental vocabulary gap.

---

### Question 2
A data science team fine-tuned a foundation model on Amazon Bedrock. The model performs well in testing, but they cannot invoke it using on-demand pricing. What is the most likely issue?

A) The fine-tuned model needs to be deployed to a SageMaker endpoint first  
B) Custom models in Bedrock require Provisioned Throughput for inference  
C) The team needs to enable the model in the Bedrock model access page  
D) Fine-tuned models can only be accessed via the AWS CLI, not the console  

**✅ Answer: B**  
Custom models created through fine-tuning in Amazon Bedrock require **Provisioned Throughput** to be purchased before they can be used for inference. On-demand pricing is not available for most custom models (with the exception of Nova custom models). Option A is incorrect because Bedrock custom models don't need SageMaker endpoints. Options C and D are incorrect as they describe unrelated issues.

---

### Question 3
A company wants to improve their AI assistant's code review quality. They can programmatically verify code correctness but find it expensive to create labeled examples of ideal code reviews. Which approach should they use?

A) Supervised fine-tuning with manually created code review examples  
B) Reinforcement fine-tuning with a Lambda-based reward function that verifies code correctness  
C) Continued pre-training on code repositories  
D) Model distillation to a smaller model  

**✅ Answer: B**  
Reinforcement fine-tuning is ideal when you can define **measurable success criteria** (code correctness can be verified programmatically) but collecting labeled examples is expensive. The Lambda reward function evaluates response quality, and the model learns iteratively. Option A requires expensive labeled data they want to avoid. Option C teaches code vocabulary but not review behavior. Option D reduces cost but doesn't improve quality.

---

**Exam Tip** 🎯: Task 3.3 tests three things: (1) knowing the key training elements and how they differ (pre-training vs. fine-tuning vs. CPT vs. distillation), (2) understanding fine-tuning methods and when to apply each, and (3) data preparation requirements — especially the labeled vs. unlabeled distinction and RLHF. The customization hierarchy (prompt engineering → RAG → fine-tuning → CPT → custom FM) is critical.

---

**Related Notes**:
- [Fine-Tuning LLMs](../Fundamentals/Fine-Tuning-LLMs.md) — Deep dive into fine-tuning techniques, PEFT/LoRA, hyperparameters
- [Model Customization](../Fundamentals/Model-Customization.md) — Comprehensive customization methods comparison
- [Model Distillation](../Fundamentals/Model-Distillation.md) — Teacher-student distillation details
- [Amazon Bedrock](../Generative-AI-Services/Amazon-Bedrock.md) — Full Bedrock service notes
- [Hyperparameters](../Fundamentals/Hyperparameters.md) — Training hyperparameters reference
- [Amazon SageMaker Ground Truth](../ML-Services/Amazon-SageMaker-Ground-Truth.md) — Data labeling for RLHF

**Citations**:
- [AIF-C01 Domain 3: Applications of Foundation Models](https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain3.html)
- [Customize your model to improve its performance - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/custom-models.html)
- [Prepare data for fine-tuning your models - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-customization-prepare.html)
- [Customize a model with reinforcement fine-tuning in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/reinforcement-fine-tuning.html)
- [Customize a model with distillation in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-distillation.html)
- [Continued pre-training (CPT) - Amazon Nova](https://docs.aws.amazon.com/nova/latest/nova2-userguide/nova-cpt.html)
- [GENOPS05-BP01 Learn when to customize models - Generative AI Lens](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/genops05-bp01.html)
