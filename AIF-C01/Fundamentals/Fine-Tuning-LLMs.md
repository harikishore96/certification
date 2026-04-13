# Fine-Tuning LLMs

## 1. Topic Overview
- **Purpose**: The process of adapting a pre-trained foundation model to a specific task, domain, or behavior by further training it on a smaller, task-specific dataset — modifying the model's weights to improve performance for your use case
- **Exam Weight**: High
- **Exam Domain**:
  - Domain 2 — Fundamentals of Generative AI (24%)
  - Domain 3 — Applications of Foundation Models (28%)
- **Task Statements**: Task 2.2 (Understand the capabilities and limitations of generative AI for solving business problems), Task 3.2 (Choose effective prompt engineering techniques), Task 3.3 (Describe the training and fine-tuning process for foundation models)

## 2. Exam Keyword Mapping
- **Trigger Words**: "fine-tuning", "model customization", "continued pre-training", "domain adaptation", "transfer learning", "RLHF", "LoRA", "PEFT", "labeled data", "instruction tuning"
- **Scenario Indicators**: Questions about improving model accuracy for specific domains, teaching a model new behavior/style, reducing hallucinations for domain-specific tasks, model not performing well with prompt engineering alone
- **Anti-patterns**: Fine-tuning is NOT always the answer — start with prompt engineering and RAG first (AWS recommended approach)

## 3. Core Concepts

### The Model Customization Hierarchy ⭐
AWS recommends a progressive approach — start simple, escalate only when needed:

```
┌─────────────────────────────────────────────────────────────────────┐
│  1. Prompt Engineering          (Lowest cost, fastest, no training) │
│     ↓ Not sufficient?                                               │
│  2. RAG (Retrieval Augmented    (No training, uses external data)   │
│     Generation)                                                     │
│     ↓ Not sufficient?                                               │
│  3. Fine-Tuning                 (Modifies weights, needs data)     │
│     ↓ Not sufficient?                                               │
│  4. Continued Pre-Training      (Domain adaptation, unlabeled data)│
│     ↓ Not sufficient?                                               │
│  5. Build Custom FM             (Highest cost, full control)       │
└─────────────────────────────────────────────────────────────────────┘
         Increasing: Cost, Complexity, Data Requirements, Time
```

**Exam Tip**: 🎯 This hierarchy is heavily tested. Always try prompt engineering → RAG → fine-tuning in that order.

### Fine-Tuning vs. Other Customization Methods

| Method | Modifies Weights? | Data Required | Cost | Latency Impact | Best For |
|--------|------------------|---------------|------|----------------|----------|
| **Prompt Engineering** | ❌ No | None | Free | None | Quick adjustments, formatting, few-shot examples |
| **RAG** | ❌ No | Knowledge base docs | Low-Medium | Adds retrieval step | Up-to-date info, factual grounding, citations |
| **Fine-Tuning** | ✅ Yes | Labeled input-output pairs | Medium-High | None (baked in) | Style, tone, domain expertise, task specialization |
| **Continued Pre-Training** | ✅ Yes | Large unlabeled corpus | High | None (baked in) | Domain vocabulary, industry-specific language |
| **Custom FM** | ✅ Yes (from scratch) | Massive dataset | Very High | None | Unique requirements, full control |

### When to Fine-Tune (vs. When NOT to)

✅ **Fine-tune when:**
- Model needs to learn domain-specific language (medical, legal, financial)
- You need consistent output style, tone, or format
- Prompt engineering and RAG don't achieve required accuracy
- You have high-quality labeled training data (input-output pairs)
- You need to reduce inference latency (no RAG retrieval step)
- Task requires specialized behavior not achievable through prompting

❌ **Don't fine-tune when:**
- Prompt engineering can solve the problem (try this first!)
- You need up-to-date or frequently changing information (use RAG instead)
- You lack sufficient high-quality labeled data
- Cost and time constraints are tight
- You need citations/source attribution (RAG provides this, fine-tuning doesn't)

## 4. Types of Model Customization

### 4.1 Fine-Tuning (Supervised Fine-Tuning / SFT) ⭐
- **What**: Train the model on labeled input-output pairs to learn specific task behavior
- **Data**: Curated dataset of prompt-completion pairs (labeled)
- **Result**: Model learns to produce outputs matching your examples
- **Use Cases**: Customer service responses, code generation style, medical Q&A, specific formatting

**Data Format Example (JSONL):**
```json
{"prompt": "Summarize this medical report:", "completion": "Patient presents with..."}
{"prompt": "Translate to formal tone:", "completion": "We would like to inform you..."}
```

### 4.2 Continued Pre-Training (Domain Adaptation)
- **What**: Further train the model on large amounts of unlabeled domain-specific text
- **Data**: Large corpus of unlabeled text (no input-output pairs needed)
- **Result**: Model learns domain vocabulary, terminology, and patterns
- **Use Cases**: Teaching the model medical terminology, legal language, financial jargon
- **Key Difference from Fine-Tuning**: Uses unlabeled data, focuses on domain knowledge rather than task behavior
- **Typically followed by**: Fine-tuning for specific tasks

```
Pre-trained FM → Continued Pre-Training (domain knowledge) → Fine-Tuning (task behavior) → Deployment
                  Unlabeled domain text                       Labeled input-output pairs
```

### 4.3 Instruction Tuning
- **What**: Fine-tuning with instruction-formatted data to improve the model's ability to follow instructions
- **Data**: Instructions paired with desired outputs
- **Result**: Model becomes better at following diverse instructions
- **Example**: "Summarize the following text in 3 bullet points: [text]" → [3 bullet summary]

### 4.4 Alignment (RLHF & DPO)

#### RLHF (Reinforcement Learning from Human Feedback)
- **What**: Aligns model behavior with human preferences using a reward model
- **Process**:
  1. Generate multiple responses to the same prompt
  2. Human annotators rank responses (preferred vs. not preferred)
  3. Train a reward model on human preferences
  4. Use reinforcement learning (PPO) to optimize the FM against the reward model
- **Result**: Model produces outputs that align with human values and preferences
- **Use Cases**: Safety, helpfulness, reducing harmful content, brand voice alignment
- **AWS Context**: SageMaker Ground Truth Plus generates preference data for RLHF

#### DPO (Direct Preference Optimization)
- **What**: Simpler alternative to RLHF — directly optimizes from preference pairs without a separate reward model
- **Data**: Pairs of preferred/not-preferred responses
- **Advantage**: Simpler, more stable, lower compute than RLHF
- **AWS Context**: Supported for Amazon Nova models in SageMaker AI

| Alignment Method | Complexity | Reward Model? | Stability | Used By |
|-----------------|-----------|---------------|-----------|---------|
| **RLHF (PPO)** | High | Yes (separate) | Can be unstable | ChatGPT, Claude |
| **DPO** | Medium | No (direct) | More stable | Nova, Llama |

### 4.5 Knowledge Distillation
- **What**: Transfer knowledge from a larger "teacher" model to a smaller "student" model
- **Result**: Smaller, faster, cheaper model that retains much of the teacher's capability
- **Use Cases**: When you need cost-efficient inference, edge deployment, lower latency
- **AWS Context**: Available for Amazon Nova models in SageMaker AI

```
Teacher Model (large, expensive)  →  Knowledge Distillation  →  Student Model (small, fast, cheap)
e.g., Nova Pro                                                    e.g., Nova Micro
```

## 5. Parameter-Efficient Fine-Tuning (PEFT) ⭐

Full fine-tuning updates ALL model parameters — expensive and requires massive compute. PEFT methods update only a small subset.

### 5.1 LoRA (Low-Rank Adaptation)
- **What**: Freezes original model weights and adds small trainable "adapter" matrices
- **How**: Decomposes weight updates into low-rank matrices (much smaller)
- **Benefit**: 
  - ✅ 10-100x fewer trainable parameters
  - ✅ Much lower GPU memory and compute cost
  - ✅ Faster training
  - ✅ Original model weights preserved
- **Key Hyperparameters**: `lora_r` (rank), `lora_alpha` (scaling), `lora_dropout`
- **Exam Tip**: 🎯 Most common PEFT method — if exam mentions "efficient fine-tuning" or "low resource fine-tuning" → LoRA

### 5.2 QLoRA (Quantized LoRA)
- **What**: Combines LoRA with quantization (4-bit) for even lower memory usage
- **Benefit**: Fine-tune large models on a single GPU
- **Trade-off**: Slight accuracy loss from quantization

### 5.3 Prompt Tuning
- **What**: Learns a small set of "soft prompt" vectors prepended to the input
- **Benefit**: Extremely parameter-efficient, model weights completely frozen
- **Difference from prompt engineering**: Prompt tuning learns continuous vectors (not human-readable text)

### 5.4 Prefix Tuning
- **What**: Adds trainable prefix vectors to each transformer layer (not just input)
- **Benefit**: More expressive than prompt tuning

### Comparison

| Method | Parameters Updated | Memory | Speed | Accuracy |
|--------|-------------------|--------|-------|----------|
| **Full Fine-Tuning (FFT)** | All (billions) | Very High | Slow | Highest |
| **LoRA** ⭐ | Small adapters (~0.1-1%) | Low | Fast | Near-FFT |
| **QLoRA** | Small adapters + quantized | Very Low | Fast | Slightly lower |
| **Prompt Tuning** | Soft prompt vectors only | Minimal | Fastest | Lower |
| **Prefix Tuning** | Prefix vectors per layer | Low | Fast | Moderate |

## 6. Fine-Tuning on AWS

### Amazon Bedrock Fine-Tuning
- **Managed service**: No infrastructure to manage
- **Supported customizations**: Fine-tuning + Continued Pre-Training
- **Supported models**: Amazon Titan, Amazon Nova, Anthropic Claude 3, Cohere Command, Meta Llama
- **Data format**: JSONL files stored in S3
- **Creates**: Private copy of the model (your data never shared)
- **Deployment**: Requires Provisioned Throughput for inference
- **Key hyperparameters**: epochCount, learningRate, batchSize, learningRateWarmupSteps, earlyStoppingThreshold

**Bedrock Fine-Tuning Process:**
```
1. Prepare labeled data (JSONL) → Upload to S3
2. Select base model in Bedrock console
3. Configure hyperparameters (or use defaults)
4. Submit customization job
5. Wait for training to complete
6. Purchase Provisioned Throughput
7. Invoke fine-tuned model via API
```

### Amazon SageMaker JumpStart
- **More control**: Choose instance types, training frameworks, hyperparameters
- **Supported models**: Llama, Falcon, Mistral, Flan-T5, Stable Diffusion, and many more
- **PEFT support**: LoRA, QLoRA, int8 quantization, FSDP (Fully Sharded Data Parallelism)
- **Deployment**: Deploy to SageMaker endpoints (real-time, serverless, async)
- **Use when**: Need more control than Bedrock offers, or model not available in Bedrock

### Amazon SageMaker Canvas
- **No-code fine-tuning**: Visual interface for non-ML practitioners
- **Requirements**: Tabular dataset with input/output columns (min 100 text pairs, max 512 chars each)
- **Evaluation**: Shows perplexity and loss scores, compare with base model
- **Use when**: Business users need to fine-tune without coding

### Amazon SageMaker HyperPod
- **Purpose**: Train or fine-tune custom foundation models at scale
- **Use when**: Building custom FMs from scratch or large-scale distributed training
- **Features**: Distributed data/model parallelism, Slurm or EKS orchestration

### Service Comparison

| Service | Code Required? | Control Level | PEFT Support | Best For |
|---------|---------------|--------------|-------------|----------|
| **Bedrock** | Minimal (console/API) | Low (managed) | Limited | Quick fine-tuning, managed experience |
| **SageMaker JumpStart** | Some (SDK) | Medium | LoRA, QLoRA, FSDP | More control, wider model selection |
| **SageMaker Canvas** | None (visual) | Low | No | Business users, no-code |
| **SageMaker HyperPod** | Yes (advanced) | Full | All methods | Custom FM training at scale |

## 7. Fine-Tuning Data Requirements

### Data Quality Checklist
- ✅ High-quality, diverse input-output pairs
- ✅ Representative of real-world use cases
- ✅ Consistent formatting across examples
- ✅ Minimum dataset size varies by model (Bedrock Canvas: 100+ pairs)
- ✅ Balanced across categories/topics
- ✅ Free of PII unless necessary and properly handled
- ✅ Stored in S3 in JSONL format (for Bedrock)

### Data Quantity Guidelines
- **More data** generally improves fine-tuning quality
- **Fewer epochs** needed with larger datasets
- **More epochs** needed with smaller datasets (but risk overfitting)
- Quality > Quantity — 1,000 high-quality examples often beats 10,000 noisy ones

### Evaluation Metrics for Fine-Tuned Models
| Metric | Description |
|--------|-------------|
| **Perplexity** | How well the model predicts the next token (lower = better) |
| **Loss** | Training/validation loss curves (should decrease, then plateau) |
| **BERTScore** | Semantic similarity between generated and reference text |
| **ROUGE** | Overlap between generated and reference text (for summarization) |
| **Human evaluation** | Human judges rate quality, relevance, accuracy |

## 8. Fine-Tuning Hyperparameters (Bedrock)

| Hyperparameter | What It Controls | Impact |
|---------------|-----------------|--------|
| **epochCount** | Passes through entire dataset | More = higher cost, risk of overfitting |
| **learningRate** | Speed of weight updates | Too high = unstable, too low = slow |
| **batchSize** | Samples per weight update | Larger = faster but more memory |
| **learningRateWarmupSteps** | Gradual LR increase at start | Prevents early divergence |
| **earlyStoppingThreshold** | Min improvement to continue | Prevents overfitting |
| **earlyStoppingPatience** | Epochs to wait before stopping | Balances training time vs. quality |

**AWS Recommendation**: Start with defaults — they're optimized across tasks and data sizes.

## 9. Exam Focus Areas

### Common Question Types
- "Model not performing well for domain-specific tasks, what to try first?" → **Prompt engineering, then RAG, then fine-tuning**
- "Need model to learn medical terminology from large unlabeled corpus?" → **Continued pre-training**
- "Need model to follow specific output format with labeled examples?" → **Fine-tuning (SFT)**
- "Need to align model with human preferences?" → **RLHF or DPO**
- "Need to fine-tune with limited compute resources?" → **LoRA / PEFT**
- "Need to fine-tune in Bedrock without managing infrastructure?" → **Bedrock managed fine-tuning**
- "Transfer knowledge from large model to small model?" → **Knowledge distillation**

### Gotchas
- Fine-tuning creates a **private copy** of the model — your data is never shared with the base model provider
- Fine-tuning in Bedrock requires **Provisioned Throughput** for inference (not on-demand)
- Continued pre-training uses **unlabeled** data; fine-tuning uses **labeled** data
- LoRA updates only **~0.1-1%** of parameters but achieves near full fine-tuning accuracy
- RLHF requires a **separate reward model**; DPO does **not**
- More epochs = higher cost in Bedrock (each epoch processes entire dataset)
- Fine-tuning does NOT give the model access to new real-time information (use RAG for that)
- Fine-tuning changes model **behavior/style**; RAG provides **factual knowledge**
- Prompt tuning ≠ prompt engineering (tuning learns vectors, engineering writes text)

### Decision Tree
```
Model not meeting requirements?
│
├── Try prompt engineering first
│   ├── Few-shot examples
│   ├── Chain-of-thought prompting
│   └── System prompts
│
├── Need factual/up-to-date knowledge?
│   └── RAG (Knowledge Bases in Bedrock)
│
├── Need domain-specific vocabulary?
│   └── Continued Pre-Training (unlabeled domain text)
│
├── Need specific task behavior/style?
│   └── Fine-Tuning (SFT with labeled data)
│       ├── Limited compute? → LoRA / QLoRA (PEFT)
│       ├── Full accuracy needed? → Full Fine-Tuning
│       └── No-code? → SageMaker Canvas
│
├── Need human preference alignment?
│   ├── Simple approach → DPO
│   └── Complex approach → RLHF (PPO + reward model)
│
├── Need smaller/faster model?
│   └── Knowledge Distillation
│
└── Need complete custom model?
    └── SageMaker HyperPod (train from scratch)
```

## 10. Best Practices
- **Start with prompt engineering** — it's free and fast; fine-tuning is expensive
- **Combine RAG + fine-tuning** for best results (fine-tune for style, RAG for knowledge)
- **Use high-quality data** — quality matters more than quantity
- **Start with default hyperparameters** in Bedrock — AWS optimizes them
- **Use LoRA/PEFT** when compute is limited — near full fine-tuning accuracy at fraction of cost
- **Monitor training curves** — watch for overfitting (validation loss increasing)
- **Enable early stopping** — prevents overfitting and saves cost
- **Evaluate thoroughly** — use both automated metrics and human evaluation
- **Version your models** — track which data and hyperparameters produced each model

## 11. Sample Questions

### Question 1
A healthcare company wants their AI assistant to understand medical terminology and produce responses in a specific clinical format. They have 5,000 labeled examples of clinical questions and ideal responses, plus a large corpus of unlabeled medical literature. What is the recommended approach?

A) Use prompt engineering with few-shot examples only  
B) Apply continued pre-training on the medical literature, then fine-tune with the labeled examples  
C) Fine-tune the model using only the unlabeled medical literature  
D) Build a custom foundation model from scratch using the medical data  

**Answer: B) Apply continued pre-training on the medical literature, then fine-tune with the labeled examples**  
Continued pre-training on the unlabeled medical corpus teaches the model domain vocabulary and terminology. Fine-tuning with the 5,000 labeled examples then teaches it the specific clinical response format. This two-step approach leverages both data types optimally. Fine-tuning requires labeled data (not unlabeled). Building from scratch is unnecessary and extremely expensive.

---

### Question 2
A company fine-tuned a foundation model in Amazon Bedrock but finds the training cost is too high due to the large model size. They want to reduce fine-tuning costs while maintaining good accuracy. Which technique should they use?

A) Increase the number of epochs to improve convergence  
B) Use LoRA (Low-Rank Adaptation) for parameter-efficient fine-tuning  
C) Switch to continued pre-training instead of fine-tuning  
D) Use a larger batch size to speed up training  

**Answer: B) Use LoRA (Low-Rank Adaptation) for parameter-efficient fine-tuning**  
LoRA freezes the original model weights and only trains small adapter matrices, reducing trainable parameters by 10-100x. This dramatically lowers GPU memory and compute costs while achieving near full fine-tuning accuracy. Increasing epochs increases cost. Continued pre-training serves a different purpose. Larger batch size may speed up training but doesn't reduce the fundamental compute requirement.

---

### Question 3
A company's AI chatbot needs to provide accurate, up-to-date product information that changes weekly. They also want the chatbot to respond in a friendly, brand-specific tone. Which combination of techniques is most appropriate?

A) Fine-tuning only — train on product data and brand examples  
B) RAG only — retrieve product information from a knowledge base  
C) RAG for product information + fine-tuning for brand tone and style  
D) Continued pre-training on product catalogs  

**Answer: C) RAG for product information + fine-tuning for brand tone and style**  
RAG is ideal for frequently changing factual information (product data updated weekly) because the knowledge base can be updated without retraining. Fine-tuning is ideal for consistent behavior like brand tone and style. Combining both gives the best of both worlds. Fine-tuning alone can't handle weekly data changes. RAG alone doesn't customize tone. Continued pre-training is for domain vocabulary, not specific product data.

---

**Exam Tip**: 🎯 For AIF-C01, the customization hierarchy (prompt engineering → RAG → fine-tuning → continued pre-training → custom FM) is critical. Know the difference between fine-tuning (labeled data, task behavior) and continued pre-training (unlabeled data, domain knowledge). Understand that LoRA is the go-to for efficient fine-tuning, and that combining RAG + fine-tuning often produces the best results.
