# Model Customization (Foundation Models)

## 1. Topic Overview
- **Purpose**: The spectrum of techniques used to adapt foundation models (FMs) to specific use cases — from zero-cost prompt engineering to full custom model training — and knowing when to apply each
- **Service Level**: Regional (Bedrock customization jobs run in specific regions)
- **Key Use Cases**:
  - Improving FM accuracy for domain-specific tasks
  - Adapting model behavior, tone, and output format
  - Reducing inference cost while maintaining quality
  - Teaching models domain-specific vocabulary and knowledge
  - Aligning model outputs with human preferences
- **Exam Weight**: HIGH — This is one of the most heavily tested topics
- **Exam Domain**:
  - Domain 2: Fundamentals of Generative AI (24%)
  - Domain 3: Applications of Foundation Models (28%) — **Primary**
  - Domain 5: Security, Compliance, and Governance (14%)
- **Task Statements**:
  - Task 3.1: Explain the cost tradeoffs of various approaches to FM customization (pre-training, fine-tuning, in-context learning, RAG)
  - Task 3.3: Describe the key elements of training an FM (pre-training, fine-tuning, continuous pre-training, distillation)
  - Task 3.3: Define methods for fine-tuning an FM (instruction tuning, domain adaptation, transfer learning, continuous pre-training)
  - Task 3.3: Describe how to prepare data to fine-tune an FM (data curation, governance, size, labeling, representativeness, RLHF)

## 2. Exam Keyword Mapping
- **Trigger Words**: "model customization", "fine-tuning", "continued pre-training", "distillation", "reinforcement fine-tuning", "domain adaptation", "transfer learning", "RLHF", "LoRA", "PEFT", "labeled data", "unlabeled data", "instruction tuning", "teacher-student"
- **Scenario Indicators**:
  - "Model not performing well for specific domain" → Customization needed
  - "Need to reduce cost while maintaining quality" → Distillation
  - "Need domain-specific vocabulary" → Continued pre-training
  - "Need specific output format/style" → Supervised fine-tuning
  - "Need to align with human preferences" → Reinforcement fine-tuning / RLHF
  - "Lack labeled data but can define success criteria" → Reinforcement fine-tuning
- **Anti-patterns**:
  - ❌ Don't jump to fine-tuning — always try prompt engineering and RAG first
  - ❌ Don't use fine-tuning for frequently changing information — use RAG
  - ❌ Don't use continued pre-training when you need task-specific behavior — use fine-tuning
  - ❌ Don't build a custom FM when existing FMs can be customized

## 3. Core Concepts

### The Model Customization Hierarchy ⭐⭐⭐
AWS recommends a **progressive approach** — start simple, escalate only when needed:

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

> **Exam Tip** 🎯: This hierarchy is **heavily tested**. The correct answer almost always follows this order. If a question says "what should you try first?" → prompt engineering. If prompt engineering isn't working → RAG. Only then → fine-tuning.

**Source**: [GENOPS05-BP01 Learn when to customize models](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/genops05-bp01.html) — AWS Well-Architected Generative AI Lens

### When to Choose Each Technique

| Technique | Modifies Weights? | Data Required | Cost | Best For |
|-----------|:-:|---|---|---|
| **Prompt Engineering** | ❌ | None | Free | Quick adjustments, formatting, few-shot examples |
| **RAG** | ❌ | Knowledge base docs | Low-Medium | Up-to-date info, factual grounding, citations |
| **Supervised Fine-Tuning** | ✅ | Labeled input-output pairs | Medium-High | Style, tone, task specialization |
| **Reinforcement Fine-Tuning** | ✅ | Prompts + reward functions | Medium-High | Alignment, measurable quality criteria |
| **Distillation** | ✅ | Prompts (teacher generates responses) | Medium | Smaller/cheaper model with similar quality |
| **Continued Pre-Training** | ✅ | Large unlabeled corpus | High | Domain vocabulary, terminology |
| **Custom FM** | ✅ | Massive dataset | Very High | Unique requirements, full control |

## 4. Key Features & Components

### 4.1 Supervised Fine-Tuning (SFT) ⭐
The most common customization method. Provide **labeled input-output pairs** to teach the model specific task behavior.

- **Data**: Curated dataset of prompt-completion pairs (labeled)
- **Result**: Model learns to produce outputs matching your examples; model parameters are adjusted
- **Use Cases**: Customer service responses, clinical Q&A, specific formatting, code generation style
- **AWS Service**: Amazon Bedrock, SageMaker JumpStart

**Supported Models for Fine-Tuning in Bedrock**:
| Provider | Models |
|----------|--------|
| Amazon | Nova 2 Lite, Nova Canvas, Nova Lite, Nova Micro, Nova Pro |
| Anthropic | Claude 3 Haiku |
| Meta | Llama 3.1 (8B, 70B), Llama 3.2 (1B, 3B, 11B, 90B), Llama 3.3 70B |
| Amazon | Titan Image Generator v2, Titan Multimodal Embeddings G1 |

**Data Format (JSONL)**:
```json
{"prompt": "Summarize this medical report:", "completion": "Patient presents with..."}
{"prompt": "Translate to formal tone:", "completion": "We would like to inform you..."}
```

**Source**: [Customize a model with fine-tuning in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/custom-model-fine-tuning.html)

### 4.2 Reinforcement Fine-Tuning (RFT) ⭐ NEW
A feedback-driven approach that teaches models what constitutes a "good" response through **reward functions** instead of labeled datasets.

- **Data**: Training prompts + reward functions (no labeled output pairs needed)
- **How It Works**:
  1. Model receives prompts and generates **multiple responses per prompt**
  2. Reward functions **score** each response
  3. Bedrock trains the model using **Group Relative Policy Optimization (GRPO)**
  4. Training loop continues until data is exhausted or you stop at a checkpoint
- **Result**: Model optimized for the metric that matters to you

**When to Use RFT**:
- ✅ You can define **clear, measurable success criteria** for response quality
- ✅ Mathematical problem-solving, code generation (rule-based graders)
- ✅ Scientific reasoning, structured data analysis
- ✅ Tasks with **multiple valid solutions** where some are clearly better
- ✅ Collecting high-quality labeled examples is **expensive or impractical**
- ✅ Applications requiring iterative improvement or adherence to complex business rules

**Reward Functions**:
- Defined using **AWS Lambda** (custom code) or **model-as-a-judge** grader
- Built-in templates for quick setup
- Must execute within seconds, not minutes

**Supported Models for RFT**:
| Provider | Model | Region |
|----------|-------|--------|
| Amazon | Nova 2 Lite | us-east-1 |
| OpenAI | gpt-oss-20B | us-west-2 |
| Qwen | Qwen3 32B | us-west-2 |

**Key Difference from SFT**:
| Aspect | Supervised Fine-Tuning | Reinforcement Fine-Tuning |
|--------|----------------------|--------------------------|
| Data | Labeled input-output pairs | Prompts + reward functions |
| Learning | Learns from examples | Learns from feedback scores |
| Best When | You know the ideal output | You can measure output quality |
| Algorithm | Standard supervised learning | GRPO (policy optimization) |

**Best Practices**:
- Start with **100-200 examples**, scale gradually
- If baseline rewards are **0%** → use SFT first to establish basic capabilities
- If baseline rewards are **>95%** → RFT may be unnecessary
- Watch for overfitting: training rewards ↑ while validation rewards ↓

**Source**: [Customize a model with reinforcement fine-tuning in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/reinforcement-fine-tuning.html)

### 4.3 Model Distillation ⭐
Transfers knowledge from a larger **teacher** model to a smaller **student** model.

- **Data**: Prompts in `.jsonl` files OR invocation logs from production
- **How It Works**: Teacher generates responses → Bedrock applies data synthesis → Student is fine-tuned on teacher responses
- **Result**: Smaller, faster, cheaper model that retains much of the teacher's capability
- **Key Benefit**: Up to **similar quality** at **lower cost and latency**

**Two Data Preparation Options**:
1. **Provide your own prompts** — Bedrock generates teacher responses + applies data synthesis
2. **Use invocation logs** — Reuse existing teacher responses from CloudWatch logs in S3
   - Prompts only → Bedrock re-generates responses (data synthesis applied)
   - Prompt-response pairs → Uses existing responses directly (faster, cheaper, no data synthesis)

**Supported Teacher → Student Pairings**:
| Teacher | Student(s) |
|---------|-----------|
| Nova Premier | Nova Pro, Nova Lite, Nova Micro |
| Nova Pro | Nova Lite, Nova Micro |
| Claude 3.5 Sonnet | Claude 3 Haiku |
| Llama 3.1 405B | Llama 3.1 8B/70B, Llama 3.2 1B, Llama 3.3 70B |
| Llama 3.1 70B | Llama 3.1 8B, Llama 3.2 1B/3B |

> ⚠️ Teacher must always be **larger** than student. Cross-provider pairings are **not supported**.

**Source**: [Customize a model with distillation in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-distillation.html)

### 4.4 Continued Pre-Training (CPT)
Extends the pre-training phase by exposing the model to additional **unlabeled text** from specific domains.

- **Data**: Large corpus of **unlabeled** domain-specific text (tens of billions of tokens ideal)
- **Result**: Model acquires deeper domain knowledge, learns terminology and writing patterns
- **Use Cases**: Legal documents, medical literature, technical documentation, proprietary business content
- **Key Difference from Fine-Tuning**: Uses **unlabeled** data, focuses on **domain knowledge** rather than task behavior
- **Important**: After CPT, the model typically needs **additional instruction tuning** to use the newly acquired knowledge

**Supported Models for CPT**:
- Nova 1.0 (Micro, Lite, Pro)
- Nova 2.0 (Lite)

```
Pre-trained FM → CPT (domain knowledge) → Fine-Tuning (task behavior) → Deployment
                  Unlabeled domain text     Labeled input-output pairs
```

**Source**: [Continued pre-training (CPT) - Amazon Nova](https://docs.aws.amazon.com/nova/latest/nova2-userguide/nova-cpt.html)

### 4.5 Instruction Tuning
- Fine-tuning with **instruction-formatted data** to improve the model's ability to follow diverse instructions
- Data: Instructions paired with desired outputs
- Example: `"Summarize the following text in 3 bullet points: [text]"` → `[3 bullet summary]`

### 4.6 RLHF (Reinforcement Learning from Human Feedback)
- Aligns model behavior with **human preferences** using a reward model
- Process: Generate responses → Humans rank them → Train reward model → Optimize FM with RL (PPO)
- Used by: ChatGPT, Claude (during their training)
- AWS Context: SageMaker Ground Truth Plus generates preference data for RLHF

### 4.7 Transfer Learning
- Using a pre-trained model as a starting point and adapting it to a new task
- Fine-tuning IS a form of transfer learning
- Reduces training time and data requirements compared to training from scratch

## 5. Exam Focus Areas

### The Master Comparison Table ⭐⭐⭐

| Technique | Data Type | Modifies Weights | Cost | Time | AWS Service |
|-----------|-----------|:-:|---|---|---|
| Prompt Engineering | None | ❌ | Free | Minutes | Bedrock Console/API |
| RAG | Knowledge docs | ❌ | Low | Hours | Bedrock Knowledge Bases |
| Supervised Fine-Tuning | Labeled pairs | ✅ | Medium | Hours | Bedrock, SageMaker |
| Reinforcement Fine-Tuning | Prompts + rewards | ✅ | Medium | Hours | Bedrock |
| Distillation | Prompts (teacher responds) | ✅ | Medium | Hours | Bedrock |
| Continued Pre-Training | Unlabeled corpus | ✅ | High | Days | Bedrock, SageMaker |
| Custom FM | Massive dataset | ✅ | Very High | Weeks | SageMaker HyperPod |

### Common Question Patterns

| Question Pattern | Answer |
|-----------------|--------|
| "What to try first when model isn't performing well?" | Prompt engineering |
| "Model needs up-to-date factual information" | RAG |
| "Model needs specific output format/style with labeled examples" | Supervised fine-tuning |
| "Can define measurable success criteria but lack labeled outputs" | Reinforcement fine-tuning |
| "Need cheaper/faster model with similar quality" | Distillation |
| "Need model to understand medical/legal terminology from large text corpus" | Continued pre-training |
| "Need to align model with human preferences" | RLHF / Reinforcement fine-tuning |
| "Transfer knowledge from large model to small model" | Distillation |
| "Fine-tune with limited compute resources" | LoRA / PEFT |
| "No-code fine-tuning for business users" | SageMaker Canvas |

### Gotchas ⚠️
1. **Fine-tuning ≠ RAG**: Fine-tuning changes model **behavior/style**; RAG provides **factual knowledge**. They serve different purposes and can be combined.
2. **Labeled vs. Unlabeled**: Fine-tuning uses **labeled** data; continued pre-training uses **unlabeled** data. This is a common exam trap.
3. **Provisioned Throughput**: All custom models in Bedrock (fine-tuned, distilled) require **Provisioned Throughput** for inference — not on-demand pricing.
4. **Distillation costs**: Teacher model inference charges are incurred during distillation (billed at on-demand rates).
5. **RFT prerequisites**: If baseline rewards are 0%, use SFT first before attempting reinforcement fine-tuning.
6. **CPT needs follow-up**: After continued pre-training, the model typically needs additional instruction tuning to be useful.
7. **Cross-provider distillation**: Not supported — teacher and student must be from the same provider family.
8. **Fine-tuning doesn't add real-time knowledge**: It bakes in behavior at training time. For current information, use RAG.
9. **Data never shared**: Custom models in Bedrock are private — your data is never used to train other models.

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

## 6. Best Practices

### Security
- Custom models in Bedrock are **private** — data never leaves AWS's secure environment
- Use IAM service roles with least-privilege access
- Encrypt training data in S3 (SSE-S3 or SSE-KMS)
- Configure VPC for customization jobs for network isolation
- Use CloudTrail for audit logging of customization operations

### Cost Optimization
- **Start with prompt engineering** — it's free
- **Use RAG before fine-tuning** — cheaper and handles dynamic data
- **Use LoRA/PEFT** for fine-tuning when compute is limited (~0.1-1% of parameters, near full accuracy)
- **Use distillation** to create smaller models for high-volume inference
- **Enable early stopping** in fine-tuning to prevent overfitting and save cost
- Bedrock charges: tokens processed × epochs (training) + per-model-per-month (storage)

### Performance
- **Combine RAG + fine-tuning** for best results (fine-tune for style, RAG for knowledge)
- **Quality > Quantity** for training data — 1,000 high-quality examples often beats 10,000 noisy ones
- **Start with default hyperparameters** in Bedrock — AWS optimizes them
- **Monitor training curves** — watch for overfitting (validation loss increasing)
- **Evaluate thoroughly** — use both automated metrics (ROUGE, BLEU, BERTScore) and human evaluation

### Operations
- Use Amazon Bedrock Model Evaluation to assess performance improvements after customization
- Version your models — track which data and hyperparameters produced each model
- Use SageMaker Model Monitor for ongoing assessment of deployed models
- Regularly update and retrain models with fresh data

## 7. Hands-On Labs

### Quick Setup: Fine-Tuning in Bedrock Console
1. Navigate to **Amazon Bedrock** → **Custom models** → **Customize model**
2. Select customization type: Fine-tuning / Continued pre-training / Distillation
3. Select base model
4. Point to training data in S3 (`.jsonl` format)
5. Configure hyperparameters (or use defaults)
6. Create IAM service role
7. Submit job → Monitor in console
8. Purchase **Provisioned Throughput** for inference

### CLI — Create Fine-Tuning Job
```bash
aws bedrock create-model-customization-job \
  --job-name my-fine-tuning-job \
  --custom-model-name my-custom-model \
  --role-arn arn:aws:iam::<account-id>:role/BedrockCustomizationRole \
  --base-model-identifier amazon.nova-lite-v1:0:300k \
  --customization-type FINE_TUNING \
  --training-data-config '{"s3Uri": "s3://my-bucket/training-data/train.jsonl"}' \
  --output-data-config '{"s3Uri": "s3://my-bucket/output/"}' \
  --hyper-parameters '{"epochCount": "3", "learningRate": "0.00001", "batchSize": "8"}'
```

### CLI — Create Distillation Job
```bash
aws bedrock create-model-customization-job \
  --job-name my-distillation-job \
  --custom-model-name my-distilled-model \
  --role-arn arn:aws:iam::<account-id>:role/BedrockDistillationRole \
  --base-model-identifier anthropic.claude-3-haiku-20240307-v1:0:200k \
  --customization-type DISTILLATION \
  --training-data-config '{"s3Uri": "s3://my-bucket/training-data/"}' \
  --output-data-config '{"s3Uri": "s3://my-bucket/output/"}'
```

## 8. Sample Questions

### Question 1
A company's foundation model performs well on general tasks but struggles with industry-specific legal terminology. They have a large collection of legal documents (contracts, case law, regulations) but no labeled input-output pairs. Which customization technique is most appropriate?

A) Supervised fine-tuning with the legal documents  
B) Continued pre-training on the legal document corpus  
C) Model distillation using a larger legal-trained model  
D) Reinforcement fine-tuning with legal accuracy reward functions  

**✅ Answer: B**  
Continued pre-training uses **unlabeled** domain-specific text to teach the model domain vocabulary and terminology. The company has a large corpus of legal documents (unlabeled), making CPT the ideal choice. Option A requires labeled input-output pairs, which they don't have. Option C requires a pre-existing legal-trained teacher model. Option D requires reward functions but doesn't address the fundamental vocabulary gap.

---

### Question 2
A startup wants to deploy an AI assistant that provides accurate, up-to-date product information (updated weekly) while responding in a friendly, brand-specific tone. Which combination of techniques is most appropriate?

A) Fine-tuning only — train on product data and brand examples  
B) RAG only — retrieve product information from a knowledge base  
C) RAG for product information + fine-tuning for brand tone and style  
D) Continued pre-training on product catalogs + distillation for cost  

**✅ Answer: C**  
RAG handles frequently changing factual information (product data updated weekly) because the knowledge base can be updated without retraining. Fine-tuning teaches consistent brand tone and style. Combining both gives the best of both worlds. Option A can't handle weekly data changes. Option B doesn't customize tone. Option D is overkill and doesn't address the dynamic data requirement.

---

### Question 3
A company uses a large foundation model on Amazon Bedrock for code review. They can programmatically verify code correctness but find it expensive to create labeled examples of ideal code reviews. They want to improve the model's code review quality. Which customization approach should they use?

A) Supervised fine-tuning with manually created code review examples  
B) Reinforcement fine-tuning with a Lambda-based reward function that verifies code correctness  
C) Model distillation to a smaller model  
D) Continued pre-training on code repositories  

**✅ Answer: B**  
Reinforcement fine-tuning is ideal when you can define **measurable success criteria** (code correctness can be verified programmatically) but collecting labeled examples is expensive. The Lambda reward function evaluates response quality, and the model learns iteratively. Option A requires expensive labeled data they want to avoid. Option C reduces cost but doesn't improve quality. Option D teaches code vocabulary but not review behavior.

---

**Exam Tip** 🎯: For AIF-C01, master the customization hierarchy (prompt engineering → RAG → fine-tuning → CPT → custom FM). Know the **data requirements** for each technique (labeled vs. unlabeled vs. none). Understand that Bedrock now offers **four** customization methods: supervised fine-tuning, reinforcement fine-tuning, distillation, and continued pre-training. The exam tests your ability to **choose the right technique** for a given scenario.

---

**Related Notes**:
- [Fine-Tuning LLMs](./Fine-Tuning-LLMs.md) — Deep dive into fine-tuning techniques, PEFT/LoRA, hyperparameters
- [Model Distillation](./Model-Distillation.md) — Deep dive into teacher-student distillation, supported pairings
- [Amazon Bedrock](../Generative-AI-Services/Amazon-Bedrock.md) — Full Bedrock service notes
- [Hyperparameters](./Hyperparameters.md) — Training hyperparameters reference

**Citations**:
- [Customize your model to improve its performance - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/custom-models.html)
- [Customize a model with fine-tuning in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/custom-model-fine-tuning.html)
- [Customize a model with reinforcement fine-tuning in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/reinforcement-fine-tuning.html)
- [Customize a model with distillation in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-distillation.html)
- [Continued pre-training (CPT) - Amazon Nova](https://docs.aws.amazon.com/nova/latest/nova2-userguide/nova-cpt.html)
- [GENOPS05-BP01 Learn when to customize models - Generative AI Lens](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/genops05-bp01.html)
- [Foundation model customization - Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/jumpstart-foundation-models-customize.html)
- [AIF-C01 Domain 3: Applications of Foundation Models](https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain3.html)
