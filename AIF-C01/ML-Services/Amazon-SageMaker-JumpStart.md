# Amazon SageMaker JumpStart

## 1. Service/Topic Overview
- **Purpose**: A machine learning hub within SageMaker that provides pretrained, open-source models, solution templates, and example notebooks to accelerate ML development
- **Service Level**: Regional
- **Key Use Cases**:
  - Quickly deploy pretrained foundation models (LLMs, image generation, etc.) without building from scratch
  - Fine-tune open-source models on domain-specific data
  - Explore and evaluate foundation models before committing to production
  - Launch end-to-end ML solution templates for common use cases
  - Prototype generative AI applications with one-click deployment
- **Exam Weight**: HIGH
- **Exam Domain**:
  - Domain 2: Fundamentals of Generative AI (24%)
  - Domain 3: Applications of Foundation Models (28%)
- **Task Statements**:
  - 2.1 Explain the basic concepts of generative AI
  - 3.1 Describe design considerations for applications that use foundation models
  - 3.3 Describe the training and fine-tuning process for foundation models

## 2. Exam Keyword Mapping
- **Trigger Words**: "pretrained model", "open-source model", "model hub", "one-click deploy", "JumpStart", "foundation model catalog", "model zoo", "quick start ML", "pre-built model"
- **Scenario Indicators**:
  - "Team wants to quickly deploy an open-source LLM on their own infrastructure"
  - "Need to fine-tune a publicly available foundation model with custom data"
  - "Want to explore and compare multiple foundation models before choosing one"
  - "Need a pretrained model deployed to a SageMaker endpoint"
  - "Developer wants to use Llama, Falcon, or Mistral models on AWS"
- **Anti-patterns**:
  - ❌ Need a fully managed API to call foundation models → **Amazon Bedrock**
  - ❌ Need no-code ML model building → **SageMaker Canvas**
  - ❌ Need AutoML for tabular data → **SageMaker Autopilot**
  - ❌ Need proprietary models (Claude, Titan) via API without managing infrastructure → **Amazon Bedrock**
  - ❌ Need to build a model completely from scratch with custom architecture → **SageMaker Training**

## 3. Core Concepts

### Architectural Patterns
```
┌─────────────────────────────────────────────────────┐
│              SageMaker JumpStart                     │
│                                                     │
│  ┌──────────────┐  ┌──────────────┐  ┌───────────┐ │
│  │  Foundation   │  │  Solution    │  │  Example  │ │
│  │  Models       │  │  Templates   │  │ Notebooks │ │
│  └──────┬───────┘  └──────────────┘  └───────────┘ │
│         │                                           │
│    ┌────┴────┐                                      │
│    │         │                                      │
│  Deploy  Fine-tune                                  │
│    │         │                                      │
│    ▼         ▼                                      │
│ SageMaker  SageMaker                                │
│ Endpoint   Training Job                             │
└─────────────────────────────────────────────────────┘
```

### Three Pillars of JumpStart
| Pillar | Description |
|--------|-------------|
| **Foundation Models** | 400+ pretrained models (LLMs, vision, multimodal) from providers like Meta, Hugging Face, Stability AI, Mistral |
| **Solution Templates** | End-to-end ML solutions that set up infrastructure for common use cases (e.g., demand forecasting, fraud detection) |
| **Example Notebooks** | Executable Jupyter notebooks demonstrating SageMaker features and ML workflows |

### Model Types Available
| Category | Examples | Use Cases |
|----------|----------|-----------|
| **Text Generation** | Llama 2/3, Falcon, Mistral, Mixtral, Flan-T5, GPT-J, BLOOM | Content writing, Q&A, summarization, code generation |
| **Image Generation** | Stable Diffusion | Text-to-image, image editing |
| **Embedding** | Various embedding models | Semantic search, RAG |
| **Vision** | Image classification, object detection models | Computer vision tasks |
| **Tabular** | XGBoost, LightGBM, CatBoost | Classification, regression |

### Model Sources
- **Publicly Available (Open-Source)**: Free to deploy, identified by model ID, deployed via JumpStartModel class
- **Proprietary (Third-Party)**: Require AWS Marketplace subscription, deployed via ModelPackage class, may have additional licensing costs

### Data Flow
```
Model Selection (Studio UI / Python SDK)
    │
    ├── Deploy ──→ Model downloaded ──→ SageMaker Endpoint (Real-time inference)
    │
    ├── Fine-tune ──→ Training data (S3) ──→ Training Job ──→ Fine-tuned Model ──→ Deploy
    │
    └── Evaluate ──→ Evaluation dataset ──→ Metrics (accuracy, toxicity, robustness)
```

### Integration Points
- **SageMaker Studio**: Primary UI for browsing, deploying, and fine-tuning models
- **SageMaker Python SDK**: Programmatic access via JumpStartModel, JumpStartEstimator classes
- **Amazon S3**: Training data storage for fine-tuning
- **SageMaker Endpoints**: Model hosting for inference
- **SageMaker Pipelines**: Integrate JumpStart models into MLOps workflows
- **SageMaker Model Registry**: Register fine-tuned models for versioning
- **AWS Marketplace**: Source for proprietary/third-party models
- **Private/Curated Hubs**: Organization-controlled model catalogs for governance

## 4. Key Features & Components

### Exam-Tested Features

#### One-Click Deployment
- Deploy pretrained models directly to SageMaker endpoints from Studio UI
- No ML expertise required for basic deployment
- Automatic instance type selection based on model requirements

#### Foundation Model Fine-Tuning
Two approaches:
1. **Domain Adaptation Fine-Tuning**: Train on domain-specific unlabeled data (e.g., medical, legal, financial text)
2. **Instruction-Based Fine-Tuning**: Train on prompt-response pairs for task-specific behavior

#### Fine-Tuning Hyperparameters
| Parameter | Description |
|-----------|-------------|
| `epoch` | Number of passes through the training dataset |
| `learning_rate` | Rate at which model weights are updated |
| `instruction_tuned` | Whether to use instruction-based fine-tuning (`True`/`False`) |
| `per_device_train_batch_size` | Batch size per GPU for training |
| `lora_r` | LoRA rank — scaling factor for low-rank weight updates |
| `lora_alpha` | LoRA alpha — generally 2-4x the `lora_r` value |
| `lora_dropout` | Dropout for LoRA layers (0 to 1) |
| `int8_quantization` | Load model with 8-bit precision to reduce memory |
| `enable_fsdp` | Enable Fully Sharded Data Parallelism for large models |

#### LoRA (Low-Rank Adaptation)
- Efficient fine-tuning technique that adds small trainable matrices instead of updating all model weights
- Significantly reduces compute and memory requirements
- Key parameters: `lora_r`, `lora_alpha`, `lora_dropout`

#### Model Evaluation
- Evaluate foundation models directly in Studio
- Metrics: accuracy, toxicity, semantic robustness
- Supports automatic and human-in-the-loop evaluation

#### Private/Curated Hubs
- Organizations can create curated model catalogs
- Control which models are available to teams
- Enforce governance and compliance policies

### Python SDK Classes
| Class | Purpose |
|-------|---------|
| `JumpStartModel` | Deploy publicly available models |
| `JumpStartEstimator` | Fine-tune publicly available models |
| `ModelPackage` | Deploy proprietary models (AWS Marketplace) |

### SDK Example — Deploy a Model
```python
from sagemaker.jumpstart.model import JumpStartModel

model = JumpStartModel(model_id="meta-textgeneration-llama-2-7b")
predictor = model.deploy()

response = predictor.predict({
    "inputs": "What is machine learning?",
    "parameters": {"max_new_tokens": 256, "temperature": 0.7}
})
```

### SDK Example — Fine-Tune a Model
```python
from sagemaker.jumpstart.estimator import JumpStartEstimator

estimator = JumpStartEstimator(
    model_id="meta-textgeneration-llama-2-7b",
    hyperparameters={
        "epoch": "3",
        "learning_rate": "2e-5",
        "instruction_tuned": "True",
        "lora_r": "8"
    }
)
estimator.fit({"training": "s3://my-bucket/training-data/"})
```

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "A company wants to deploy an open-source LLM on their own AWS infrastructure with full control over the endpoint" → **SageMaker JumpStart**
- **Scenario**: "A team needs to fine-tune Llama 2 on proprietary company data" → **SageMaker JumpStart fine-tuning**
- **Comparison**: "When to use JumpStart vs. Bedrock for foundation models?"
- **Feature**: "What customization options does JumpStart provide?"

### 🔥 Gotchas
- JumpStart deploys models to **SageMaker endpoints** (you manage infrastructure) — Bedrock provides **serverless API access** (fully managed)
- Not all JumpStart models support fine-tuning — check the supported models list
- Proprietary models require **AWS Marketplace subscription** before deployment
- Some models require **EULA acceptance** before use
- JumpStart models run on **your AWS account's compute** — you pay for the instances
- Fine-tuning changes model weights; prompt engineering does NOT

### Comparison Points

#### JumpStart vs. Bedrock (Critical for Exam!)
| Feature | SageMaker JumpStart | Amazon Bedrock |
|---------|-------------------|----------------|
| **Model Access** | Open-source + some proprietary | Proprietary + select open-source |
| **Infrastructure** | You manage SageMaker endpoints | Fully managed, serverless |
| **Customization** | Full fine-tuning with LoRA, FSDP | Fine-tuning, continued pre-training |
| **Model Control** | Full control over instance type, scaling | AWS manages infrastructure |
| **Pricing** | Pay for compute instances | Pay per token / provisioned throughput |
| **RAG** | Build your own RAG pipeline | Built-in Knowledge Bases |
| **Guardrails** | Build your own | Built-in Guardrails service |
| **Best For** | ML engineers who want full control | Developers who want simplicity |
| **Expertise Required** | Higher (ML engineering) | Lower (API-level) |

#### JumpStart vs. Hugging Face on SageMaker
| Feature | JumpStart | Hugging Face DLC |
|---------|-----------|-----------------|
| **Setup** | One-click / model ID | Manual container config |
| **Model Selection** | Curated catalog | Full Hugging Face Hub |
| **Fine-tuning** | Built-in hyperparameter support | Full flexibility |
| **Best For** | Quick start, less config | Advanced customization |

### Decision Trees
```
Need to use a foundation model on AWS?
├── Want fully managed API, no infrastructure? → Amazon Bedrock
├── Want to deploy open-source models on your own endpoints?
│   ├── Want one-click deploy or built-in fine-tuning? → SageMaker JumpStart
│   └── Want maximum flexibility with custom containers? → SageMaker + Hugging Face DLC
├── Want no-code ML? → SageMaker Canvas (uses JumpStart models)
└── Need to train a model from scratch? → SageMaker Training
```

## 6. Best Practices

### Security
- Models deploy within your VPC — configure VPC endpoints for private access
- Use IAM roles to control who can deploy/fine-tune models
- Enable encryption at rest (KMS) for model artifacts and training data in S3
- Review EULA and license terms for third-party models before production use
- Use Private/Curated Hubs to restrict model access within organizations

### Cost Optimization
- Choose appropriate instance types — JumpStart suggests defaults but you can override
- Use **SageMaker Inference Recommender** to find optimal instance for cost/performance
- Delete endpoints when not in use (endpoints incur charges while running)
- Use **LoRA fine-tuning** instead of full fine-tuning to reduce compute costs
- Use `int8_quantization` to run larger models on smaller instances
- Consider **Spot Instances** for fine-tuning training jobs

### Performance
- Use GPU instances (ml.g5, ml.p4d) for foundation model inference
- Enable `enable_fsdp` (Fully Sharded Data Parallelism) for training large models across multiple GPUs
- Tune `per_device_train_batch_size` based on available GPU memory
- Use quantization (`int8_quantization`) to reduce memory footprint

### Operations
- Monitor endpoints with **Amazon CloudWatch** (invocations, latency, errors)
- Use **SageMaker Model Monitor** to detect data/model drift
- Register fine-tuned models in **SageMaker Model Registry** for versioning
- Track fine-tuning experiments with **SageMaker Experiments**

## 7. Hands-On Labs

### Quick Setup — Deploy a Model via Studio
1. Open SageMaker Studio → Navigate to **Models** in left panel
2. Browse or search for a model (e.g., "Llama 2 7B")
3. Click the model card → Review model details
4. Click **Deploy** → Select instance type → Deploy
5. Test the endpoint with sample prompts

### Quick Setup — Deploy via Python SDK
```python
from sagemaker.jumpstart.model import JumpStartModel

# Deploy Flan-T5 XL
model = JumpStartModel(model_id="huggingface-text2text-flan-t5-xl")
predictor = model.deploy()

# Invoke
response = predictor.predict({"inputs": "Summarize: Amazon SageMaker JumpStart provides..."})
print(response)

# Clean up
predictor.delete_endpoint()
```

### CLI — List Available Models
```bash
aws sagemaker list-model-packages \
    --model-package-group-name "jumpstart-models" \
    --region us-east-1
```

### Practical Exercise — Fine-Tune and Deploy
1. Prepare training data in JSONL format (prompt-response pairs)
2. Upload to S3
3. Use JumpStartEstimator to launch fine-tuning job
4. Deploy the fine-tuned model
5. Compare outputs between base and fine-tuned model

## 8. Sample Questions

### Question 1
A machine learning team wants to deploy an open-source large language model on AWS. They need full control over the hosting infrastructure, including the ability to choose instance types and configure auto-scaling. They also want to fine-tune the model on their proprietary dataset. Which AWS service should they use?

A) Amazon Bedrock
B) Amazon SageMaker JumpStart
C) Amazon Comprehend
D) Amazon Lex

**Correct Answer: B**

**Explanation**: SageMaker JumpStart provides a catalog of open-source pretrained models that can be deployed to SageMaker endpoints, giving the team full control over infrastructure (instance types, auto-scaling). JumpStart also supports fine-tuning with custom data. Amazon Bedrock (A) provides serverless API access without infrastructure control. Amazon Comprehend (C) is for NLP tasks like sentiment analysis, not deploying custom LLMs. Amazon Lex (D) is for building conversational chatbots.

---

### Question 2
A developer is using SageMaker JumpStart to fine-tune a large language model but is running into GPU memory limitations. Which technique should they use to reduce memory requirements while still fine-tuning the model effectively?

A) Increase the learning rate
B) Use Low-Rank Adaptation (LoRA)
C) Increase the batch size
D) Use a larger instance type with more GPUs

**Correct Answer: B**

**Explanation**: LoRA (Low-Rank Adaptation) is an efficient fine-tuning technique that adds small trainable matrices instead of updating all model weights, significantly reducing memory and compute requirements. JumpStart supports LoRA via `lora_r`, `lora_alpha`, and `lora_dropout` hyperparameters. Increasing the learning rate (A) doesn't reduce memory. Increasing batch size (C) would increase memory usage. Using a larger instance (D) is a brute-force approach that increases cost rather than optimizing the technique.

---

### Question 3
A company wants to use foundation models on AWS. They need a serverless, fully managed API to access models like Claude and Amazon Titan without managing any infrastructure. Which service should they choose?

A) Amazon SageMaker JumpStart
B) Amazon SageMaker Canvas
C) Amazon Bedrock
D) Amazon SageMaker Autopilot

**Correct Answer: C**

**Explanation**: Amazon Bedrock provides a fully managed, serverless API to access foundation models (including Claude, Titan, Llama) without managing infrastructure. SageMaker JumpStart (A) requires deploying models to SageMaker endpoints that you manage. SageMaker Canvas (B) is a no-code ML tool for building models. SageMaker Autopilot (D) is for AutoML on tabular data. The key differentiator is "serverless" and "no infrastructure management" → Bedrock.

---

**Exam Tip**: The most common exam question pattern around JumpStart is distinguishing it from Bedrock. Remember: JumpStart = you manage the infrastructure (SageMaker endpoints), Bedrock = fully managed serverless API. If the question mentions "open-source models" + "full control" + "own endpoints" → JumpStart. If it mentions "serverless" + "API access" + "no infrastructure" → Bedrock.

---

## Sources
- [SageMaker JumpStart Pretrained Models](https://docs.aws.amazon.com/sagemaker/latest/dg/studio-jumpstart.html)
- [SageMaker JumpStart Foundation Models](https://docs.aws.amazon.com/sagemaker/latest/dg/jumpstart-foundation-models.html)
- [Foundation Model Customization](https://docs.aws.amazon.com/sagemaker/latest/dg/jumpstart-foundation-models-customize.html)
- [Foundation Models Fine-Tuning Hyperparameters](https://docs.aws.amazon.com/sagemaker/latest/dg/jumpstart-foundation-models-fine-tuning.html)
- [Use Foundation Models with the SageMaker Python SDK](https://docs.aws.amazon.com/sagemaker/latest/dg/jumpstart-foundation-models-use-python-sdk.html)
