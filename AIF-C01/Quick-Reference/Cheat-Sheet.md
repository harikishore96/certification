# AIF-C01 Cheat Sheet

> One-page-per-domain quick reference for the AWS Certified AI Practitioner exam.

---

## Domain 1: Fundamentals of AI and ML (20%)

### AI Hierarchy
```
Artificial Intelligence (broadest)
  └── Machine Learning (learns from data)
        └── Deep Learning (neural networks with many layers)
              └── Generative AI (creates new content)
```

### Learning Paradigms at a Glance

| Paradigm | Data | Goal | Example |
|---|---|---|---|
| Supervised | Labeled | Predict known output | Spam detection, price prediction |
| Unsupervised | Unlabeled | Find hidden patterns | Customer segmentation, anomaly detection |
| Semi-supervised | Small labeled + large unlabeled | Improve with unlabeled data | Medical image classification |
| Self-supervised | Unlabeled (auto-labels) | Learn representations | BERT (masked words), GPT (next token) |
| Reinforcement | Rewards/penalties | Maximize cumulative reward | Game AI, robotics |
| RLHF | Human preferences | Align with human values | ChatGPT alignment, Claude training |

### Supervised Learning Tasks

| Task | Output | Metric | Example |
|---|---|---|---|
| Binary Classification | 0 or 1 | Accuracy, F1, AUC-ROC | Fraud: yes/no |
| Multi-class Classification | One of N classes | F1 (macro/micro), Accuracy | Image: cat/dog/bird |
| Regression | Continuous number | RMSE, MAE, R² | House price |

### Evaluation Metrics Cheat Sheet

| Metric | Formula | Use When |
|---|---|---|
| **Precision** | TP / (TP + FP) | False positives are costly (spam filter) |
| **Recall** | TP / (TP + FN) | False negatives are costly (cancer detection) |
| **F1 Score** | 2 × (P × R) / (P + R) | Need balance of precision & recall |
| **Accuracy** | (TP + TN) / Total | Balanced classes only |
| **AUC-ROC** | Area under ROC curve | Compare classifiers, threshold-independent |
| **RMSE** | √(mean squared error) | Regression, penalize large errors |
| **MAE** | Mean absolute error | Regression, robust to outliers |
| **R²** | 1 - (SS_res / SS_tot) | Regression, proportion of variance explained |
| **BLEU** | n-gram overlap | Machine translation quality |
| **ROUGE** | Recall of n-grams | Text summarization quality |
| **BERTScore** | Semantic similarity | Text generation quality |
| **Perplexity** | Exp of avg log-likelihood | Language model quality (lower = better) |

### Model Problems

| Problem | Symptom | Fix |
|---|---|---|
| **Overfitting** | High train accuracy, low test accuracy | More data, regularization, dropout, early stopping |
| **Underfitting** | Low train AND test accuracy | More complex model, more features, train longer |
| **High Bias** | Underfitting | Increase model complexity |
| **High Variance** | Overfitting | Regularization, more data, simpler model |

### Key Hyperparameters

| Hyperparameter | Effect of Increasing | Effect of Decreasing |
|---|---|---|
| **Learning Rate** | Faster training, may overshoot | Slower training, more precise |
| **Batch Size** | Faster, less noisy, more memory | Slower, more noisy, better generalization |
| **Epochs** | Better learning, risk overfitting | Underfitting |
| **Regularization (L1/L2)** | Simpler model, prevent overfitting | More complex model |
| **Dropout Rate** | More regularization | Less regularization |

---

## Domain 2: Fundamentals of Generative AI (24%)

### Foundation Model Landscape

| Provider | Models in Bedrock | Strengths |
|---|---|---|
| **Anthropic** | Claude 3.5 Sonnet, Claude 3 Haiku/Opus | Reasoning, safety, long context |
| **Amazon** | Titan Text/Embeddings/Image, Nova | Cost-effective, multimodal |
| **Meta** | Llama 3 | Open-weight, customizable |
| **Cohere** | Command R/R+, Embed | Enterprise search, RAG |
| **Stability AI** | Stable Diffusion | Image generation |
| **Mistral** | Mistral, Mixtral | Efficient, multilingual |

### Transformer Architecture Key Concepts
```
Input → Tokenization → Embedding + Positional Encoding → Self-Attention → Feed-Forward → Output
```
- **Self-Attention**: Each token attends to all other tokens (captures relationships)
- **Multi-Head Attention**: Multiple attention patterns in parallel
- **Positional Encoding**: Injects sequence order information
- **Encoder**: Processes input (BERT-style, good for understanding)
- **Decoder**: Generates output (GPT-style, good for generation)
- **Encoder-Decoder**: Both (T5-style, good for translation)

### Prompt Engineering Techniques

| Technique | Description | Best For |
|---|---|---|
| **Zero-shot** | No examples, just instruction | Simple tasks, capable models |
| **Few-shot** | 2-5 examples before the task | Format guidance, specific patterns |
| **Chain-of-thought** | "Think step by step" | Math, logic, complex reasoning |
| **System prompt** | Define persona/rules/constraints | Consistent behavior, safety |
| **Role prompting** | "You are a [expert]..." | Domain-specific responses |
| **Output formatting** | "Respond in JSON/table/list" | Structured outputs |

### Inference Parameters

| Parameter | Low Value | High Value |
|---|---|---|
| **Temperature** | Deterministic, factual | Creative, diverse |
| **Top-P** | Focused, fewer choices | Diverse, more choices |
| **Top-K** | Consider fewer tokens | Consider more tokens |
| **Max Tokens** | Shorter responses | Longer responses |
| **Stop Sequences** | N/A | Control where output ends |

### Model Customization Spectrum
```
Prompt Engineering → RAG → Fine-tuning → Continued Pre-training → Train from Scratch
←── Less effort/cost                                    More effort/cost ──→
←── Less customization                              More customization ──→
```

### GenAI Challenges & Mitigations

| Challenge | Description | Mitigation |
|---|---|---|
| **Hallucinations** | Generates false information | RAG, low temperature, grounding, guardrails |
| **Toxicity** | Harmful/offensive content | Guardrails, content filters, RLHF |
| **Bias** | Unfair outputs for groups | Diverse training data, bias testing, guardrails |
| **Prompt injection** | Malicious input overrides | Input validation, guardrails, prompt design |
| **Data leakage** | Exposes training data | Differential privacy, access controls |
| **Cost** | Token-based pricing adds up | Caching, smaller models, batch processing |

---

## Domain 3: Applications of Foundation Models (28%)

### AWS AI/ML Service Selection Decision Tree

```
What do you need?
│
├── Generate text/images/code? ──→ Amazon Bedrock
│     ├── Need RAG? ──→ Bedrock Knowledge Bases
│     ├── Need autonomous tasks? ──→ Bedrock Agents
│     └── Need content safety? ──→ Bedrock Guardrails
│
├── Train custom ML model? ──→ Amazon SageMaker
│     ├── No-code? ──→ SageMaker Canvas
│     ├── AutoML? ──→ SageMaker Autopilot
│     ├── Pre-trained models? ──→ SageMaker JumpStart
│     └── Bias detection? ──→ SageMaker Clarify
│
├── Analyze images/video? ──→ Amazon Rekognition
├── Analyze text? ──→ Amazon Comprehend
├── Extract document data? ──→ Amazon Textract
├── Speech → Text? ──→ Amazon Transcribe
├── Text → Speech? ──→ Amazon Polly
├── Translate languages? ──→ Amazon Translate
├── Build chatbot? ──→ Amazon Lex (structured) / Bedrock (generative)
├── Enterprise search? ──→ Amazon Kendra
├── Recommendations? ──→ Amazon Personalize
├── Forecast time-series? ──→ Amazon Forecast
├── Detect fraud? ──→ Amazon Fraud Detector
└── Human review of ML? ──→ Amazon A2I
```

### RAG Architecture
```
User Query
    │
    ▼
┌─────────────┐     ┌──────────────────┐     ┌─────────────┐
│  Embedding   │────▶│  Vector Database  │────▶│  Retrieved   │
│  Model       │     │  (OpenSearch,     │     │  Context     │
└─────────────┘     │   Pinecone, etc.) │     └──────┬──────┘
                     └──────────────────┘            │
                                                      ▼
                                              ┌──────────────┐
                              User Query ───▶ │  Foundation   │ ──▶ Response
                                              │  Model        │
                                              └──────────────┘
```

### Bedrock Features Quick Reference

| Feature | Purpose | Key Detail |
|---|---|---|
| **Model Access** | Use FMs via API | Pay-per-token (on-demand) |
| **Knowledge Bases** | Managed RAG | Auto-chunk, embed, store, retrieve |
| **Agents** | Autonomous task execution | Connect to APIs, Lambda, KBs |
| **Guardrails** | Content safety | Filters, PII redaction, denied topics |
| **Fine-tuning** | Customize model behavior | Labeled data, task-specific |
| **Continued Pre-training** | Domain adaptation | Unlabeled domain data |
| **Model Evaluation** | Compare models | Automatic + human evaluation |
| **Provisioned Throughput** | Reserved capacity | Guaranteed performance |

### SageMaker Components Quick Reference

| Component | Purpose | User Level |
|---|---|---|
| **Studio** | Unified ML IDE | Data scientists |
| **Canvas** | No-code ML | Business analysts |
| **Autopilot** | AutoML | Developers |
| **JumpStart** | Pre-trained model hub | All levels |
| **Clarify** | Bias & explainability | ML engineers |
| **Model Monitor** | Production monitoring | MLOps engineers |
| **Pipelines** | ML workflow automation | MLOps engineers |
| **Model Registry** | Model versioning | MLOps engineers |
| **Feature Store** | Feature management | Data engineers |
| **Data Wrangler** | Data preparation | Data scientists |
| **Model Cards** | Model documentation | Governance teams |

### AI Services — Input/Output Quick Map

| Service | Input | Output |
|---|---|---|
| **Rekognition** | Image, Video | Labels, faces, text, moderation |
| **Comprehend** | Text | Sentiment, entities, key phrases, PII |
| **Textract** | Document image/PDF | Text, tables, forms, key-value pairs |
| **Transcribe** | Audio | Text transcript |
| **Polly** | Text | Audio (speech) |
| **Translate** | Text | Translated text |
| **Lex** | Text/Voice | Intent, slots, response |
| **Kendra** | Natural language query | Ranked documents, answers |
| **Personalize** | User interaction data | Recommendations |
| **Forecast** | Time-series data | Future predictions |
| **Fraud Detector** | Transaction data | Fraud score |

---

## Domain 4: Guidelines for Responsible AI (14%)

### AWS Responsible AI Pillars

| Pillar | Description | AWS Tool |
|---|---|---|
| **Fairness** | No discrimination across groups | SageMaker Clarify (bias metrics) |
| **Explainability** | Understand model decisions | SageMaker Clarify (SHAP values) |
| **Privacy** | Protect personal data | Bedrock Guardrails (PII redaction), Comprehend (PII detection) |
| **Safety** | Prevent harmful outputs | Bedrock Guardrails (content filters) |
| **Transparency** | Document model behavior | SageMaker Model Cards, AWS AI Service Cards |
| **Governance** | Oversight and accountability | SageMaker Model Registry, CloudTrail |
| **Robustness** | Reliable under varied conditions | SageMaker Model Monitor (drift detection) |

### Bias Detection Timeline

```
Data Collection → Pre-training Bias Detection → Model Training → Post-training Bias Detection → Deployment → Monitoring
                  (SageMaker Clarify)                            (SageMaker Clarify)                  (Model Monitor)
```

### Key Bias Metrics (SageMaker Clarify)

| Metric | Type | Measures |
|---|---|---|
| **Class Imbalance (CI)** | Pre-training | Imbalance in class representation |
| **Difference in Proportions of Labels (DPL)** | Pre-training | Label distribution across groups |
| **Disparate Impact (DI)** | Post-training | Ratio of positive outcomes across groups |
| **Demographic Parity Difference** | Post-training | Difference in positive prediction rates |

### Bedrock Guardrails Configuration

| Filter Type | What It Does | Example |
|---|---|---|
| **Content Filters** | Block harmful categories | Hate, violence, sexual, insults, misconduct |
| **Denied Topics** | Block specific subjects | Competitor info, investment advice |
| **Word Filters** | Block specific words/phrases | Profanity, brand names |
| **Sensitive Info Filters** | Detect/redact PII | SSN, email, phone, credit card |
| **Contextual Grounding** | Check factual accuracy | Hallucination detection |

### PII Detection Across AWS Services

| Service | PII Capability |
|---|---|
| **Comprehend** | Detect & redact PII in text (names, SSN, addresses, etc.) |
| **Bedrock Guardrails** | Redact PII in FM inputs/outputs |
| **Textract** | Detect PII in scanned documents |
| **Transcribe** | Redact PII from audio transcriptions |
| **Macie** | Discover PII in S3 buckets |

---

## Domain 5: Security, Compliance, and Governance (14%)

### Shared Responsibility Model for AI

| AWS Manages | Customer Manages |
|---|---|
| Physical infrastructure | Data classification & protection |
| Service availability | IAM policies & access control |
| Base model security | Prompt design & safety |
| Network infrastructure | Content filtering (Guardrails) |
| Patch management | Compliance requirements |
| Encryption capabilities | Encryption key management |
| | Responsible AI implementation |
| | Monitoring & auditing |

### Data Protection

| Layer | Mechanism | AWS Service |
|---|---|---|
| **At Rest** | AES-256 encryption | AWS KMS (CMK or AWS-managed) |
| **In Transit** | TLS 1.2+ | Built into all AWS AI services |
| **In Use** | VPC isolation, private endpoints | VPC, PrivateLink |
| **Access** | Least privilege | IAM policies, resource policies |
| **Audit** | API logging | CloudTrail, CloudWatch |

### Key Security Services for AI Workloads

| Service | Role |
|---|---|
| **IAM** | Identity and access management, least privilege |
| **KMS** | Encryption key management |
| **VPC** | Network isolation |
| **PrivateLink** | Private connectivity to AI services |
| **CloudTrail** | API audit logging |
| **CloudWatch** | Monitoring, alarms, dashboards |
| **AWS Config** | Configuration compliance |
| **Organizations + SCPs** | Organizational guardrails |
| **Macie** | S3 data discovery and PII detection |
| **GuardDuty** | Threat detection |

### Governance Checklist for AI Solutions

- [ ] IAM roles follow least privilege principle
- [ ] Data encrypted at rest (KMS) and in transit (TLS)
- [ ] VPC endpoints for private connectivity
- [ ] CloudTrail enabled for all AI service API calls
- [ ] Model Cards document model purpose, limitations, bias
- [ ] Guardrails configured for content safety
- [ ] Model Monitor tracks drift and performance
- [ ] AI services opt-out policy applied (if required)
- [ ] Compliance requirements mapped (HIPAA, GDPR, etc.)
- [ ] Human review workflows (A2I) for high-risk decisions

### AI Services Opt-Out Policy
```
AWS Organizations → Policies → AI services opt-out policies
→ Prevents AWS from using your content to improve AI services
→ Applied at organization, OU, or account level
→ Supports: Comprehend, Lex, Polly, Rekognition, Textract, Transcribe, Translate, etc.
```

---

## Quick Formulas & Numbers

| Item | Value |
|---|---|
| Bedrock max context (Claude 3.5 Sonnet) | 200K tokens |
| SageMaker real-time endpoint timeout | 60 seconds |
| SageMaker async inference max payload | 1 GB |
| Comprehend max document size (sync) | 5,000 bytes (UTF-8) |
| Rekognition max image size (API) | 5 MB |
| Textract max pages (sync) | 1 page |
| Textract max pages (async) | 3,000 pages |
| Transcribe max audio length | 4 hours (batch) |
| Translate max text (sync) | 10,000 bytes |

---

## Exam Day Quick Tips

1. **"Fully managed" + "no ML expertise"** → AI Services (Rekognition, Comprehend, etc.) or Bedrock
2. **"Custom model" + "full control"** → SageMaker
3. **"Foundation model" + "API access"** → Bedrock
4. **"Reduce hallucinations"** → RAG (Bedrock Knowledge Bases)
5. **"Detect bias"** → SageMaker Clarify
6. **"Content safety"** → Bedrock Guardrails
7. **"Human review"** → Amazon A2I
8. **"No-code ML"** → SageMaker Canvas
9. **"Enterprise search"** → Amazon Kendra
10. **"Recommendations"** → Amazon Personalize
11. **"Document extraction"** → Amazon Textract (NOT Rekognition)
12. **"Model documentation"** → SageMaker Model Cards
13. **"Monitor production model"** → SageMaker Model Monitor
14. **"Encrypt data"** → AWS KMS
15. **"Audit API calls"** → AWS CloudTrail

---

**Sources**:
- [Amazon Bedrock Foundation Models](https://docs.aws.amazon.com/bedrock/latest/userguide/foundation-models-reference.html)
- [SageMaker Clarify - Bias Detection & Explainability](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-configure-processing-jobs.html)
- [AWS AI Services Overview](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/machine-learning.html)
- [AWS AI Services Opt-Out Policy](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_ai-opt-out_all.html)
