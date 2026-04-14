# Epoch

## 1. Topic Overview
- **Purpose**: An epoch is one complete pass through the entire training dataset during model training — it is a key hyperparameter that controls how many times the model sees all training examples
- **Exam Weight**: Medium-High
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — understanding training process
  - Domain 2: Fundamentals of Generative AI (24%) — fine-tuning foundation models
  - Domain 3: Applications of Foundation Models (28%) — Bedrock fine-tuning configuration
- **Task Statements**: Task 1.3 (ML Development Lifecycle), Task 2.2 (Generative AI model training), Task 3.2 (Fine-tuning foundation models)

## 2. Exam Keyword Mapping
- **Trigger Words**: "epoch", "epochCount", "number of passes", "iterations through dataset", "training duration", "overfitting after training too long", "convergence"
- **Scenario Indicators**:
  - "Model performs well on training data but poorly on validation data" → too many epochs (overfitting)
  - "Model hasn't learned patterns yet" → too few epochs (underfitting)
  - "Want to reduce fine-tuning cost in Bedrock" → reduce epoch count
  - "Training loss is still decreasing" → may need more epochs
- **Anti-patterns**:
  - Epoch ≠ iteration/step (an iteration processes one batch; an epoch processes ALL batches)
  - Epoch ≠ batch size (batch size is samples per update; epoch is full dataset passes)
  - More epochs ≠ always better (overfitting + cost increase)

## 3. Core Concepts

### What Is an Epoch?

An **epoch** = one complete forward and backward pass through the **entire** training dataset.

```
Training Dataset: [Sample1, Sample2, Sample3, ..., Sample1000]

Epoch 1: Model sees ALL 1000 samples → updates weights
Epoch 2: Model sees ALL 1000 samples again → refines weights
Epoch 3: Model sees ALL 1000 samples again → further refines weights
...
```

### Epoch vs. Iteration vs. Step

| Term | Definition | Scope |
|------|-----------|-------|
| **Epoch** | One complete pass through the entire training dataset | Full dataset |
| **Iteration (Step)** | One forward + backward pass on a single batch | One batch |
| **Batch** | A subset of training data processed together | Subset of data |

**Relationship Formula**:
```
Iterations per Epoch = Total Training Samples ÷ Batch Size

Example:
  Dataset: 10,000 samples
  Batch size: 100
  Iterations per epoch: 10,000 ÷ 100 = 100 iterations
  3 epochs = 3 × 100 = 300 total iterations
```

### How Epochs Affect Training

```
Loss
  │╲
  │ ╲
  │  ╲         ╭── Validation loss starts increasing (overfitting)
  │   ╲      ╱
  │    ╲   ╱
  │     ╲╱ ← Sweet spot (optimal epoch count)
  │     Training loss keeps decreasing
  │
  └──────────────────────────
        Epochs →

  Zone 1 (Few epochs):    Underfitting — model hasn't learned enough
  Zone 2 (Optimal):       Good generalization — training & validation loss both low
  Zone 3 (Too many):      Overfitting — training loss low, validation loss rising
```

### Epoch and Dataset Size Relationship

| Dataset Size | Recommended Epochs | Reasoning |
|-------------|-------------------|-----------|
| **Large** (100K+ samples) | Fewer (1-3) | Model sees enough variety per epoch to converge quickly |
| **Medium** (10K-100K) | Moderate (3-5) | Balanced exposure needed |
| **Small** (< 10K) | More (5-10+) | Model needs repeated exposure to learn patterns |

> **AWS Guidance**: "Larger datasets require fewer epochs to converge, while smaller datasets require more epochs to converge." — [Amazon Bedrock Custom Model Hyperparameters](https://docs.aws.amazon.com/bedrock/latest/userguide/custom-models-hp.html)

## 4. Key Features & Components

### Epoch in Amazon Bedrock Fine-Tuning

The `epochCount` hyperparameter is a core setting when fine-tuning foundation models in Bedrock. Each epoch processes the entire training dataset once, directly impacting cost.

| Model Family | API Parameter | Min | Max | Default |
|-------------|--------------|-----|-----|---------|
| **Amazon Nova** (Lite, Micro, Pro) | `epochCount` | 1 | 5 | 2 |
| **Amazon Titan Text Premier** | `epochCount` | 1 | 5 | 2 |
| **Amazon Titan Text** (Lite, Express) | `epochCount` | 1 | 10 | 5 |
| **Amazon Titan Multimodal Embeddings** | `epochCount` | 1 | 100 | Must specify (supports `Auto`) |
| **Anthropic Claude 3** | `epochCount` | 1 | 10 | 2 |
| **Cohere Command** | `epochCount` | 1 | 10 | Model-specific |
| **Meta Llama** | `epochCount` | 1 | 10 | Model-specific |

> **Source**: [Amazon Bedrock Custom Model Hyperparameters](https://docs.aws.amazon.com/bedrock/latest/userguide/custom-models-hp.html)

**Cost Impact**: Each epoch processes the entire training dataset → **more epochs = more tokens processed = higher fine-tuning cost**. AWS recommends starting with defaults.

### Epoch in Amazon SageMaker

- **SageMaker Training Jobs**: Epoch count is set as a hyperparameter in the training job configuration
- **SageMaker Autopilot**: `epochCount` determines how many times the model goes through the entire training dataset; large values may increase runtime significantly
- **SageMaker JumpStart**: The `epoch` hyperparameter is the number of passes through the fine-tuning dataset during training (must be integer > 1)
- **SageMaker Automatic Model Tuning (AMT)**: Can include epoch count as a tunable hyperparameter range

> **Source**: [SageMaker Autopilot Hyperparameters](https://docs.aws.amazon.com/sagemaker/latest/dg/autopilot-llms-finetuning-hyperparameters.html), [SageMaker JumpStart Fine-Tuning](https://docs.aws.amazon.com/sagemaker/latest/dg/jumpstart-foundation-models-fine-tuning.html)

### Epoch vs. Steps (Image Models)

Some models (e.g., Amazon Titan Image Generator, Amazon Nova Canvas) use **steps** instead of epochs:

| Concept | Epoch-Based | Step-Based |
|---------|------------|------------|
| **Unit** | Full dataset passes | Individual batch iterations |
| **Used by** | Text models, embedding models | Image generation models |
| **Example** | Nova Micro: 1-5 epochs | Titan Image: 10-40,000 steps |
| **Cost driver** | Epochs × dataset size | Step count directly |

### Early Stopping (Epoch Optimization)

Early stopping automatically halts training when validation performance stops improving, preventing unnecessary epochs.

| Model | Early Stopping Support | Parameters |
|-------|----------------------|------------|
| **Claude 3** (Bedrock) | ✅ Yes | `earlyStoppingThreshold` (default: 0.001), `earlyStoppingPatience` (default: 2 epochs) |
| **Cohere** (Bedrock) | ✅ Yes | `earlyStoppingThreshold`, `earlyStoppingPatience` |
| **SageMaker AMT** | ✅ Yes | Configurable per tuning job |
| **Nova / Titan** (Bedrock) | ❌ No | Must manually set epoch count |

## 5. Exam Focus Areas

### Common Question Types

1. **Definition**: "What is an epoch in ML training?" → One complete pass through the entire training dataset
2. **Overfitting diagnosis**: "Training accuracy is high but validation accuracy is low after many epochs" → Overfitting, reduce epochs or add early stopping
3. **Cost optimization**: "How to reduce Bedrock fine-tuning cost?" → Reduce epoch count (fewer passes = fewer tokens processed)
4. **Epoch vs. iteration**: "What is the difference between an epoch and an iteration?" → Epoch = full dataset; iteration = one batch
5. **Dataset size relationship**: "Small dataset, model not converging" → Increase epochs

### Gotchas
- **Epoch ≠ Iteration**: An epoch covers the ENTIRE dataset; an iteration covers ONE batch. If dataset has 1000 samples and batch size is 100, one epoch = 10 iterations
- **More epochs ≠ better model**: After a point, more epochs cause overfitting and increase cost
- **Epoch count affects cost directly in Bedrock**: Each epoch processes all training tokens — this is a billing factor
- **Some models use steps, not epochs**: Image generation models (Titan Image, Nova Canvas) use `stepCount` instead of `epochCount`
- **`Auto` epoch count**: Titan Multimodal Embeddings supports `Auto` which lets AWS determine optimal epoch count based on dataset size
- **Default of 2 works for most cases**: AWS recommends starting with default epoch count (typically 2) for Bedrock fine-tuning

### Comparison: Epoch vs. Related Hyperparameters

| Hyperparameter | Controls | Increase Effect | Decrease Effect |
|---------------|----------|----------------|-----------------|
| **Epoch** | Dataset passes | More learning, risk overfitting, higher cost | Less learning, risk underfitting, lower cost |
| **Batch Size** | Samples per update | Faster training, more memory, may generalize worse | Slower training, less memory, often better generalization |
| **Learning Rate** | Update step size | Faster convergence, risk instability | Slower convergence, more stable |
| **Warmup Steps** | Gradual LR increase | Smoother start, slower to reach target LR | Faster to reach target LR, risk early instability |

### Decision Tree
```
Model not performing well?
│
├── Training loss still decreasing after all epochs?
│   └── ✅ Increase epochs (model needs more training)
│
├── Training loss low, validation loss increasing?
│   └── ❌ Too many epochs → Reduce epochs or enable early stopping
│
├── Both losses high and not decreasing?
│   └── Epochs alone won't help → Check learning rate, model architecture, data quality
│
└── Fine-tuning in Bedrock and want to minimize cost?
    └── Start with default epochs (2), only increase if model underperforms
```

## 6. Best Practices

### Training
- **Start with defaults**: AWS-recommended defaults (typically 2 for Bedrock) are optimized across tasks
- **Monitor training curves**: Watch training loss AND validation loss per epoch
- **Use early stopping when available**: Prevents wasted compute on unnecessary epochs (Claude 3, Cohere in Bedrock)
- **Larger datasets → fewer epochs**: Large datasets provide enough variety per epoch for convergence

### Cost Optimization
- **Fewer epochs = lower cost**: Each epoch in Bedrock processes the entire training dataset (billed per token)
- **Use `Auto` when available**: Titan Multimodal Embeddings supports automatic epoch determination
- **Don't over-train**: If validation loss plateaus or increases, additional epochs waste money
- **Consider learning rate instead**: Increasing learning rate can sometimes achieve convergence in fewer epochs (but risks instability)

### Security & Operations
- **Log epoch metrics**: Track loss per epoch in CloudWatch for SageMaker training jobs
- **Set job timeouts**: In SageMaker Autopilot, set `MaxAutoMLJobRuntimeInSeconds` to accommodate large epoch counts (10 epochs ≈ up to 72 hours)
- **Version control**: Track epoch count alongside model artifacts for reproducibility

## 7. Hands-On Examples

### Bedrock Fine-Tuning (CLI)
```bash
aws bedrock create-model-customization-job \
  --job-name "my-fine-tune-job" \
  --base-model-identifier "amazon.nova-micro-v1:0" \
  --customization-type "FINE_TUNING" \
  --role-arn "arn:aws:iam::<account-id>:role/BedrockRole" \
  --training-data-config '{"s3Uri": "s3://my-bucket/training-data.jsonl"}' \
  --output-data-config '{"s3Uri": "s3://my-bucket/output/"}' \
  --hyper-parameters '{"epochCount": "3", "learningRate": "0.00001", "learningRateWarmupSteps": "10"}'
```

### SageMaker Training (Python SDK)
```python
from sagemaker.estimator import Estimator

estimator = Estimator(
    image_uri="<training-image>",
    role="<sagemaker-role>",
    instance_count=1,
    instance_type="ml.p3.2xlarge",
    hyperparameters={
        "epochs": 5,
        "batch_size": 32,
        "learning_rate": 0.001
    }
)
estimator.fit({"train": "s3://my-bucket/train", "validation": "s3://my-bucket/val"})
```

### SageMaker JumpStart Fine-Tuning (Python SDK)
```python
from sagemaker.jumpstart.estimator import JumpStartEstimator

estimator = JumpStartEstimator(
    model_id="meta-textgeneration-llama-3-8b",
    hyperparameters={
        "epoch": 3,
        "learning_rate": 2e-5,
        "per_device_train_batch_size": 4
    }
)
estimator.fit({"training": "s3://my-bucket/training-data/"})
```

## 8. Sample Questions

### Question 1
A company is fine-tuning an Amazon Nova Micro model in Amazon Bedrock. The training dataset contains 50,000 examples. After training with the default settings, the model performs well on training data but poorly on new inputs. What should the team do?

A) Increase the epoch count to 5 to give the model more training time  
B) Decrease the epoch count to 1 and evaluate performance  
C) Increase the learning rate to speed up convergence  
D) Double the training dataset size  

**Answer: B) Decrease the epoch count to 1 and evaluate performance**  
The model performs well on training data but poorly on new inputs — this is classic overfitting. The default epoch count for Nova models is 2. Reducing to 1 epoch reduces the model's exposure to training data, which can help generalization. Increasing epochs (A) would worsen overfitting. Increasing learning rate (C) addresses convergence speed, not overfitting. Doubling data (D) could help but is not the most direct hyperparameter adjustment.

---

### Question 2
A machine learning engineer is configuring a fine-tuning job in Amazon Bedrock. They want to minimize cost while still achieving acceptable model quality. Which statement about epochs is correct?

A) Setting epochs to the maximum value ensures the best model quality  
B) Each epoch processes the entire training dataset, so more epochs directly increase cost  
C) Epochs only affect training time, not the cost of the fine-tuning job  
D) The epoch count has no impact on model performance  

**Answer: B) Each epoch processes the entire training dataset, so more epochs directly increase cost**  
In Bedrock fine-tuning, cost is based on tokens processed. Each epoch processes the entire training dataset once, so more epochs = more tokens = higher cost. Maximum epochs (A) risks overfitting and unnecessary cost. Epochs do affect cost (C is wrong). Epoch count significantly impacts model performance (D is wrong).

---

### Question 3
A data scientist has a training dataset of 500 samples and is fine-tuning a model. The model is underfitting after 2 epochs. Which combination of hyperparameter changes is most appropriate?

A) Decrease epochs to 1 and increase batch size  
B) Increase epochs to 5 and slightly increase learning rate  
C) Keep epochs at 2 and add L2 regularization  
D) Decrease learning rate and enable early stopping  

**Answer: B) Increase epochs to 5 and slightly increase learning rate**  
With a small dataset (500 samples) and underfitting, the model needs more exposure to the data. Increasing epochs gives the model more passes through the dataset. A slightly higher learning rate can help the model converge faster. Decreasing epochs (A) worsens underfitting. Adding regularization (C) further constrains the model. Decreasing learning rate (D) slows convergence, and early stopping would halt training even sooner.

---

**Exam Tip**: 🎯 For AIF-C01, remember: (1) An epoch = one full pass through the entire dataset, (2) More epochs = higher Bedrock fine-tuning cost, (3) Too many epochs → overfitting, too few → underfitting, (4) AWS recommends starting with default epoch values, (5) Some image models use "steps" instead of "epochs". Know the relationship between dataset size and optimal epoch count.
