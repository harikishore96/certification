# Model Distillation

## 1. Service/Topic Overview
- **Purpose**: Model distillation is a technique that transfers knowledge from a large, capable "teacher" model to a smaller, more efficient "student" model — achieving near-teacher accuracy at lower cost and latency
- **Service Level**: Regional (Bedrock Distillation runs in specific regions)
- **Key Use Cases**:
  - Reducing inference cost while maintaining quality
  - Lowering latency for production applications
  - Creating task-specific smaller models from general-purpose large models
  - Deploying efficient models when you lack sufficient labeled training data
  - Matching advanced model performance with smaller model efficiency
- **Exam Weight**: High
- **Exam Domain**:
  - Domain 2: Fundamentals of Generative AI (24%)
  - Domain 3: Applications of Foundation Models (28%)
- **Task Statements**: Understanding model customization techniques, choosing between fine-tuning vs. distillation vs. continued pre-training, optimizing FM deployment for cost and performance

## 2. Exam Keyword Mapping
- **Trigger Words**: "smaller model", "reduce cost", "lower latency", "teacher-student", "knowledge transfer", "efficient model", "distill", "compress model capabilities"
- **Scenario Indicators**:
  - "Company wants the quality of a large model but at lower cost"
  - "Application has strict latency requirements but needs high accuracy"
  - "Need a custom model but lack sufficient labeled training data"
  - "Want to deploy a smaller model that performs like a larger one"
- **Anti-patterns**:
  - ❌ When you need the full capabilities of the teacher model across all tasks → Use the teacher model directly
  - ❌ When you need to add domain-specific knowledge → Use continued pre-training
  - ❌ When you have abundant labeled data for a specific task → Standard fine-tuning may suffice
  - ❌ When you need real-time context from external sources → Use RAG instead

## 3. Core Concepts

### How Distillation Works
```
┌─────────────────────────────────────────────────────┐
│                DISTILLATION WORKFLOW                 │
│                                                     │
│  ┌──────────┐    Prompts     ┌──────────────────┐   │
│  │  Input   │───────────────►│  Teacher Model   │   │
│  │  Data    │                │  (Large/Capable) │   │
│  │ (.jsonl) │                └────────┬─────────┘   │
│  └──────────┘                         │             │
│                              Generates Responses    │
│                                       │             │
│                              ┌────────▼─────────┐   │
│                              │  Data Synthesis   │   │
│                              │  (Augmentation)   │   │
│                              └────────┬─────────┘   │
│                                       │             │
│                              ┌────────▼─────────┐   │
│                              │  Student Model   │   │
│                              │  (Small/Fast)    │   │
│                              │  Fine-tuned with  │   │
│                              │  teacher responses│   │
│                              └──────────────────┘   │
└─────────────────────────────────────────────────────┘
```

### Teacher-Student Relationship
| Aspect | Teacher Model | Student Model |
|--------|--------------|---------------|
| Size | Significantly larger | Significantly smaller |
| Role | Knowledge source — generates high-quality responses | Learns from teacher's responses |
| Cost | Higher inference cost | Lower inference cost |
| Latency | Higher | Lower |
| Selection | Choose one already trained on tasks similar to your use case | Must be paired with compatible teacher (see supported models table) |

### Data Flow
1. **Input**: Prompts in `.jsonl` files OR invocation logs from production
2. **Processing**: Teacher model generates responses → Bedrock applies proprietary data synthesis techniques → Augmented dataset split into training/validation
3. **Output**: Fine-tuned student model that approximates teacher quality

### Service Limits
- Data synthesis may increase dataset to a maximum of **15,000 prompt-response pairs**
- Request metadata: max **16 key-value pairs**, each key/value max **256 characters**
- Distilled models require **Provisioned Throughput** for inference
- Nova distilled models **cannot be copied** to other Regions

## 4. Key Features & Components

### Amazon Bedrock Model Distillation
Bedrock provides a **fully managed, single-workflow** distillation experience:

1. **Choose teacher & student models** — Select compatible model pairs
2. **Prepare training data** — Two options:
   - **Option 1: Provide your own prompts** — Supply prompts in `.jsonl` files; optionally include labeled prompt-response pairs as "golden examples"
   - **Option 2: Use invocation logs** — Reuse existing teacher responses from CloudWatch invocation logs stored in S3
3. **Create distillation job** — Bedrock automates response generation, data synthesis, and student fine-tuning

### Supported Teacher → Student Model Pairings (Bedrock)

| Provider | Teacher | Student(s) | Region |
|----------|---------|------------|--------|
| Amazon | Nova Pro | Nova Lite, Nova Micro | us-east-1 |
| Amazon | Nova Premier | Nova Lite, Nova Micro, Nova Pro | us-east-1 |
| Anthropic | Claude 3.5 Sonnet (v1/v2) | Claude 3 Haiku | us-west-2 |
| Meta | Llama 3.1 405B | Llama 3.1 8B, Llama 3.1 70B, Llama 3.2 1B, Llama 3.3 70B | us-west-2 |
| Meta | Llama 3.1 70B | Llama 3.1 8B, Llama 3.2 1B, Llama 3.2 3B | us-west-2 |
| Meta | Llama 3.3 70B | Llama 3.1 8B, Llama 3.2 1B, Llama 3.2 3B | us-west-2 |

### Amazon Nova Distillation
Nova distillation uses **SageMaker training jobs** with data augmentation:
- **Teacher models**: Nova Pro, Nova Premier
- **Student models**: Nova Lite, Nova Micro (and Nova Pro when Premier is teacher)
- Generates high-quality synthetic training data from teacher
- Validates augmented data quality before fine-tuning

### Proprietary Data Synthesis Techniques
Bedrock automatically applies techniques to improve distillation quality:
- Generates **similar prompts** for more diverse teacher responses
- Uses **golden examples** (labeled prompt-response pairs) to guide teacher response quality
- Splits augmented dataset into **training and validation** sets

### Invocation Logs Integration
- Use `requestMetadata` in InvokeModel/Converse API calls to tag responses
- Filter logs by metadata when creating distillation jobs (e.g., `"project": "CustomerService"`)
- Boolean operators: AND, OR, NOT (single operator per filter, cannot combine)
- Two modes:
  - **Prompts only** → Bedrock re-generates teacher responses (data synthesis applied)
  - **Prompt-response pairs** → Uses existing responses directly (faster, cheaper, but no data synthesis)

### Security & Data Privacy
- Only **you** can access the final distilled model
- Amazon Bedrock does **not** use your data to train any other model for public use
- Supports IAM service roles, S3 encryption, VPC configuration
- Cross-region inference profiles supported for teacher models (System Inference Profiles only)

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "A company wants to reduce inference costs while maintaining quality" → Model distillation
- **Comparison**: Distillation vs. fine-tuning vs. continued pre-training vs. RAG
- **Architecture**: When to use distillation in an ML pipeline
- **Cost**: Understanding the cost trade-offs (teacher inference charges during distillation vs. lower student inference costs)

### Gotchas
- ⚠️ Distillation **incurs teacher model inference charges** during the process (billed at on-demand rates)
- ⚠️ Student model must be from the **supported pairings table** — you can't pick arbitrary combinations
- ⚠️ Distilled models require **Provisioned Throughput** for inference (not on-demand)
- ⚠️ Nova distilled models **cannot be copied** to other Regions
- ⚠️ When using invocation logs with prompt-response pairs, the teacher model in the distillation job **must match** the model used in the invocation log
- ⚠️ Using invocation log responses directly skips data synthesis → may result in a **less performant** distilled model

### Comparison: Model Customization Techniques

| Technique | What It Does | When to Use | Data Needed |
|-----------|-------------|-------------|-------------|
| **Distillation** | Transfers knowledge from large → small model | Reduce cost/latency while keeping quality | Prompts (teacher generates responses) |
| **Fine-tuning** | Adapts model to specific task with labeled data | Task-specific optimization | Labeled prompt-response pairs |
| **Continued Pre-training** | Extends model's domain knowledge | Domain adaptation (legal, medical, etc.) | Unlabeled domain-specific text |
| **RAG** | Augments model with external knowledge at inference | Real-time access to current/proprietary data | Knowledge base documents |
| **Prompt Engineering** | Optimizes input prompts | Quick improvements, no training needed | None (just better prompts) |

### Decision Tree
```
Need to customize a foundation model?
│
├─ Want smaller/cheaper model with similar quality?
│  └─ ✅ Model Distillation
│
├─ Have labeled data for a specific task?
│  └─ ✅ Fine-tuning
│
├─ Need domain-specific knowledge (e.g., medical, legal)?
│  └─ ✅ Continued Pre-training
│
├─ Need real-time access to current/proprietary data?
│  └─ ✅ RAG
│
└─ Want quick improvements without training?
   └─ ✅ Prompt Engineering
```

## 6. Best Practices

### Security
- Use IAM service roles with least-privilege access to S3 buckets
- Encrypt training/validation data in S3
- Configure VPC for distillation jobs for network isolation
- Use cross-region inference profiles with proper permissions

### Cost Optimization
- Use **invocation logs with prompt-response pairs** to avoid re-generating teacher responses (cheaper)
- Be aware that data synthesis techniques incur additional teacher model inference charges
- After distillation, the student model's lower inference cost offsets the upfront distillation cost
- Choose the smallest student model that meets your quality requirements

### Performance
- Provide **golden examples** (labeled prompt-response pairs) to improve teacher response quality
- Use request metadata to filter invocation logs for **task-specific** training data
- Validate your dataset before running the distillation job
- Choose a teacher model already trained on tasks similar to your use case

### Operations
- Monitor distillation jobs via CloudWatch logs
- Clone distillation jobs to iterate with different settings
- Validate augmented data quality after distillation
- For Claude/Llama models: distillation runs in us-west-2; copy model to other regions as needed

## 7. Hands-On Labs

### Quick Setup (Bedrock Console)
1. Navigate to **Amazon Bedrock** → **Custom models** → **Model distillation**
2. Select teacher model (e.g., Claude 3.5 Sonnet v2)
3. Select student model (e.g., Claude 3 Haiku)
4. Upload training data (`.jsonl`) to S3
5. Configure IAM role and S3 permissions
6. Submit distillation job
7. Purchase Provisioned Throughput for the distilled model

### Dataset Format Example
```jsonl
{"prompt": "Summarize the key benefits of cloud computing for small businesses."}
{"prompt": "Explain the difference between IaaS, PaaS, and SaaS."}
{"prompt": "What security measures should a startup implement when migrating to AWS?"}
```

With golden examples (labeled):
```jsonl
{"prompt": "What is serverless computing?", "completion": "Serverless computing is a cloud execution model where the cloud provider dynamically manages server allocation..."}
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
A company uses Claude 3.5 Sonnet on Amazon Bedrock for customer support summarization. The model performs well, but inference costs are too high for their volume. They want to maintain similar quality while reducing costs. What should they do?

**A)** Switch to prompt engineering to reduce token usage
**B)** Use Amazon Bedrock Model Distillation to create a Claude 3 Haiku student model
**C)** Use continued pre-training to make the model more efficient
**D)** Implement RAG to reduce the number of tokens processed

**✅ Answer: B**
Model distillation transfers the teacher model's (Claude 3.5 Sonnet) knowledge to a smaller student model (Claude 3 Haiku), maintaining similar quality at lower inference cost and latency. Option A may help but won't achieve the same quality-to-cost ratio. Option C adds domain knowledge but doesn't reduce model size. Option D addresses context augmentation, not model efficiency.

---

### Question 2
A data scientist is setting up model distillation in Amazon Bedrock. They already have production invocation logs from their teacher model stored in S3. They want the fastest and most cost-effective distillation approach. Which data preparation option should they choose?

**A)** Provide new prompts in `.jsonl` files for the teacher to generate fresh responses
**B)** Use invocation logs with prompts only, so Bedrock re-generates teacher responses
**C)** Use invocation logs with prompt-response pairs to reuse existing teacher responses
**D)** Manually create labeled prompt-response pairs as golden examples

**✅ Answer: C**
Using invocation logs with prompt-response pairs reuses existing teacher responses, avoiding additional teacher inference charges and making the process faster. However, note that Bedrock's proprietary data synthesis techniques are NOT applied in this mode, which may result in slightly lower quality. Option A and B both incur additional teacher inference costs. Option D requires manual effort and doesn't leverage existing production data.

---

### Question 3
Which of the following is a valid teacher-student model pairing for Amazon Bedrock Model Distillation?

**A)** Teacher: Claude 3 Haiku → Student: Claude 3.5 Sonnet
**B)** Teacher: Llama 3.1 8B → Student: Llama 3.2 1B
**C)** Teacher: Nova Premier → Student: Nova Pro
**D)** Teacher: Claude 3.5 Sonnet → Student: Llama 3.1 8B

**✅ Answer: C**
Nova Premier can distill to Nova Lite, Nova Micro, and Nova Pro. Option A is reversed — the teacher must be larger than the student. Option B uses a small model as teacher (8B), which is not a supported teacher. Option D mixes providers (Anthropic teacher with Meta student), which is not supported.

---

**Exam Tip**: Remember that distillation = "big model teaches small model." The key exam differentiator is: distillation reduces cost/latency by creating a smaller model, while fine-tuning adapts a model to a specific task, and continued pre-training adds domain knowledge. Also remember that distilled models in Bedrock require Provisioned Throughput for inference.

---

**Citations**:
- [Amazon Bedrock Model Distillation](https://docs.aws.amazon.com/bedrock/latest/userguide/model-distillation.html)
- [Prerequisites for Model Distillation](https://docs.aws.amazon.com/bedrock/latest/userguide/prequisites-model-distillation.html)
- [Prepare Training Datasets for Distillation](https://docs.aws.amazon.com/bedrock/latest/userguide/distillation-prepare-datasets.html)
- [Amazon Nova Distillation](https://docs.aws.amazon.com/nova/latest/userguide/nova-distillation.html)
