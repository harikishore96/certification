# Amazon SageMaker Clarify

## 1. Service/Topic Overview
- **Purpose**: Detect bias in data and ML models, and explain model predictions using feature attributions
- **Service Level**: Regional
- **Key Use Cases**:
  1. Detect pre-training bias in raw datasets before model training
  2. Detect post-training bias in model predictions
  3. Explain model predictions using SHAP (Shapley) values
  4. Monitor deployed models for bias drift and feature attribution drift
  5. Generate governance/compliance reports for regulators
- **Exam Weight**: HIGH
- **Exam Domain**:
  - Domain 4: Guidelines for Responsible AI (14%) — primary
  - Domain 1: Fundamentals of AI and ML (20%) — evaluation metrics
  - Domain 5: Security, Compliance, and Governance for AI Solutions (14%) — audit/reporting
- **Task Statements**:
  - Task 4.1: Explain responsible AI considerations for ML solutions
  - Task 4.2: Recognize the importance of transparent and explainable models
  - Task 4.3: Identify methods to detect and mitigate bias

---

## 2. Exam Keyword Mapping

### Trigger Words
- "Bias detection", "fairness", "explainability"
- "Feature importance", "feature attribution", "SHAP values"
- "Disparate impact", "demographic parity"
- "Pre-training bias", "post-training bias"
- "Why did the model predict...", "explain predictions"
- "Partial dependence plots (PDPs)"
- "Bias drift monitoring"

### Scenario Indicators
- "A financial company wants to ensure loan approval model is fair across demographics"
- "Identify which features most influenced a prediction"
- "Detect bias in training data before model training"
- "Monitor production model for fairness drift"
- "Regulatory compliance requires model explainability"
- "Understand why a model denied a loan application"

### Anti-patterns (When NOT to Use)
- ❌ Model quality/accuracy monitoring → **SageMaker Model Monitor**
- ❌ Debugging training jobs (vanishing gradients, overfitting) → **SageMaker Debugger**
- ❌ Content filtering for generative AI → **Bedrock Guardrails**
- ❌ Documenting model metadata → **SageMaker Model Cards** (uses Clarify results, but doesn't detect bias)
- ❌ Data quality monitoring → **SageMaker Model Monitor (Data Quality)**
- ❌ AutoML model building → **SageMaker Autopilot** (integrates Clarify, but Autopilot builds models)

---

## 3. Core Concepts

### Architectural Patterns
```
┌─────────────────────────────────────────────────────────┐
│                  SageMaker Clarify                       │
│                                                         │
│  ┌──────────────┐   ┌──────────────┐   ┌─────────────┐ │
│  │ Pre-training  │   │ Post-training │   │ Explainab-  │ │
│  │ Bias Metrics  │   │ Bias Metrics  │   │ ility       │ │
│  │ (data only)   │   │ (data+model)  │   │ (SHAP/PDPs) │ │
│  └──────┬───────┘   └──────┬───────┘   └──────┬──────┘ │
│         │                  │                   │        │
│         ▼                  ▼                   ▼        │
│  ┌─────────────────────────────────────────────────────┐│
│  │           Processing Job (Clarify Container)        ││
│  └─────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────┘
         │                  │                   │
         ▼                  ▼                   ▼
    ┌──────────┐     ┌──────────┐        ┌──────────┐
    │ S3 Input │     │ Model    │        │ S3 Output│
    │ Dataset  │     │ Endpoint │        │ Reports  │
    └──────────┘     └──────────┘        └──────────┘
```

### Key Terminology
| Term | Definition |
|------|-----------|
| **Facet** | Column/feature with respect to which bias is measured (e.g., age, gender) |
| **Facet Value** | Specific attribute value that may be favored or disfavored |
| **Positive Label** | Favorable outcome (e.g., loan approved) |
| **Negative Label** | Unfavorable outcome (e.g., loan denied) |
| **Favored Facet (a)** | Demographic group that bias favors |
| **Disfavored Facet (d)** | Demographic group that bias disfavors |
| **Observed Label (y)** | Actual ground truth label in the dataset |
| **Predicted Label (y')** | Label predicted by the model |

### Data Flow
```
Input (S3)          →  Clarify Processing Job  →  Output (S3)
- Dataset (CSV/     →  1. Validate inputs       →  - JSON bias metrics
  JSON Lines)       →  2. Create shadow endpoint →  - Visual report (HTML)
- Analysis config   →  3. Get model predictions  →  - Feature attributions
  (JSON)            →  4. Compute metrics        →  - Partial dependence plots
- Model (optional)  →  5. Delete shadow endpoint →  - Local explanations
```

### Integration Points
| Service | Integration |
|---------|------------|
| **S3** | Input datasets and output reports |
| **SageMaker Endpoints** | Get model predictions for post-training analysis |
| **SageMaker Pipelines** | Integrate bias checks as pipeline steps |
| **SageMaker Model Monitor** | Monitor bias drift in production |
| **SageMaker Studio** | Visualize bias reports in the IDE |
| **SageMaker Autopilot** | Automatic Clarify integration for AutoML |
| **CloudWatch** | Publish bias metrics for alerting |
| **SageMaker Model Cards** | Document bias analysis results |

---

## 4. Key Features & Components

### 4.1 Pre-training Bias Metrics (8 Metrics)
Detect bias in raw data **before** training — model-agnostic (no model needed).

| Metric | Abbreviation | What It Measures | Range |
|--------|-------------|------------------|-------|
| Class Imbalance | CI | Imbalance in number of members between facets | [-1, +1] |
| Difference in Proportions of Labels | DPL | Imbalance of positive outcomes between facets | [-1, +1] |
| Kullback-Leibler Divergence | KL | Entropy-based divergence between outcome distributions | [0, +∞) |
| Jensen-Shannon Divergence | JS | Symmetric entropy-based divergence between distributions | [0, +∞) |
| Lp-norm | LP | p-norm difference between demographic distributions | [0, +∞) |
| Total Variation Distance | TVD | Half of L1-norm difference between distributions | [0, +∞) |
| Kolmogorov-Smirnov | KS | Maximum divergence between outcome distributions | [0, +1] |
| Conditional Demographic Disparity | CDD | Disparity of outcomes between facets by subgroups | [-1, +1] |

> **Exam Tip**: Pre-training metrics only need the **dataset** — no model required. They are model-agnostic.

### 4.2 Post-training Bias Metrics (13 Metrics)
Detect bias **after** training — requires model predictions.

| Metric | Abbreviation | What It Measures |
|--------|-------------|------------------|
| Difference in Positive Proportions in Predicted Labels | DPPL | Difference in proportion of positive predictions between facets |
| Disparate Impact | DI | Ratio of positive prediction proportions between facets |
| Conditional Demographic Disparity in Predicted Labels | CDDPL | Disparity of predicted labels between facets by subgroups |
| Counterfactual Fliptest | FT | Whether similar members of different facets get different predictions |
| Accuracy Difference | AD | Difference in prediction accuracy between facets |
| Recall Difference | RD | Difference in recall between facets |
| Difference in Conditional Acceptance | DCAcc | Difference in observed vs. predicted positive outcomes across facets |
| Difference in Acceptance Rates | DAR | Difference in ratios of observed positives to predicted positives |
| Specificity Difference | SD | Difference in specificity between facets |
| Difference in Conditional Rejection | DCR | Difference in observed vs. predicted negative outcomes across facets |
| Difference in Rejection Rates | DRR | Difference in ratios of observed negatives to predicted negatives |
| Treatment Equality | TE | Difference in ratio of false positives to false negatives |
| Generalized Entropy | GE | Inequality in benefits assigned by model predictions |

> **Exam Tip**: Post-training metrics require **both** the dataset and **model predictions**. They can detect bias introduced by the algorithm or hyperparameter choices.

### 4.3 Model Explainability

#### SHAP (SHapley Additive exPlanations)
- Based on **Shapley values** from cooperative game theory
- Assigns each feature an **importance value** for a particular prediction
- **Model-agnostic** — works with any ML model
- Supports: tabular data, computer vision (CV), natural language processing (NLP)
- Two types:
  - **Global explanations**: Overall feature importance across the dataset
  - **Local explanations**: Per-instance feature importance for individual predictions

#### Partial Dependence Plots (PDPs)
- Show the **marginal effect** of a feature on the predicted outcome
- Help understand how changing one feature value affects predictions
- Useful for understanding non-linear relationships

#### Asymmetric Shapley Values
- Extension of SHAP for features with causal or ordered relationships
- Accounts for feature dependencies and ordering

### 4.4 Production Monitoring
- **Bias Drift Monitoring**: Detect when bias metrics shift from baseline over time
- **Feature Attribution Drift**: Detect when feature importance changes in production
- Integrates with **SageMaker Model Monitor** for scheduled monitoring
- Publishes metrics to **CloudWatch** for alerting
- Uses **Normalized Discounted Cumulative Gain (NDCG)** to compare feature attribution rankings between training data and live data
  - NDCG range: [0, 1] — where 1 = no drift (rankings identical)
  - **Alert threshold**: NDCG < 0.90 automatically raises an alert
  - Sensitive to both ranking order changes AND raw attribution scores

#### Feature Attribution Drift Example
| Feature | Attribution (Training) | Attribution (Live) |
|---------|----------------------|--------------------|
| SAT Score | 0.70 | 0.10 |
| GPA | 0.50 | 0.20 |
| Class Rank | 0.05 | 0.70 |

> In this example, the ranking completely reversed → NDCG = 0.69 → alert triggered (below 0.90)

### 4.5 Online Explainability
- Real-time explanations from a SageMaker endpoint
- Get per-instance SHAP values during inference
- Useful for customer-facing applications requiring instant explanations

### 4.6 Foundation Model Evaluation (FMEval)
SageMaker Clarify also provides **foundation model evaluation** capabilities for LLMs and generative AI models.

#### Evaluation Methods
| Method | Description | Tool |
|--------|-------------|------|
| **Automatic Evaluation** | Benchmark-based metrics scored programmatically | Studio or `fmeval` library |
| **Human Evaluation** | Human workers manually evaluate subjective dimensions | Studio only |

#### Supported Task Types
| Task Type | Description |
|-----------|-------------|
| **Open-ended Generation** | Natural human responses without pre-defined structure |
| **Text Summarization** | Concise summaries retaining key information |
| **Question Answering** | Relevant and accurate responses to prompts |
| **Classification** | Assigning categories/labels/scores to text |
| **Custom** | User-defined evaluation dimensions |

#### Evaluation Dimensions
| Dimension | What It Measures |
|-----------|------------------|
| **Accuracy** | How well model output matches target output (exact match, F1, recall, precision) |
| **Toxicity** | Harmful, offensive, or inappropriate content in responses |
| **Semantic Robustness** | Consistency of responses when prompts are slightly perturbed |
| **Custom Dimensions** | User-defined criteria for specific use cases |

#### Accuracy Metrics for FM Evaluation
| Metric | Description |
|--------|-------------|
| `exact_match_score` | 1 if output exactly matches target, else 0 |
| `quasi_exact_match_score` | Relaxed exact match (ignores whitespace/punctuation) |
| `recall_over_words` | Proportion of target words found in model output |
| `precision_over_words` | Proportion of model output words found in target (measures verbosity) |
| `f1_score` | Geometric average of precision and recall |

#### Human Evaluation
- Compare responses from **up to 2 JumpStart models** side-by-side
- Can also include responses from **models outside AWS**
- Requires **custom prompt dataset** stored in S3
- Define custom evaluation criteria (helpfulness, style, coherence)
- Create **work teams** in Studio to participate in evaluation
- Best for **subjective dimensions** that automated metrics can't capture

#### Inference Parameters (Configurable in FM Eval)
| Parameter | Effect |
|-----------|--------|
| **Temperature** | Controls randomness — lower = more deterministic |
| **Top P** | Controls word selection pool — lower = more deterministic |
| **Max New Tokens** | Controls response length |

#### Prompt Templates
- Clarify **automatically augments prompts** to match model-specific formats (e.g., `[INST]<<SYS>>` for Llama)
- Can toggle off automatic templating and provide **custom prompt templates**
- Same prompt template applies to all datasets within the same evaluation dimension

> **Exam Tip**: FMEval in SageMaker Clarify evaluates foundation models for accuracy, toxicity, and robustness. For subjective evaluation (helpfulness, style), use **human evaluation**. The `fmeval` library allows custom evaluation outside Studio.

---

## 5. Exam Focus Areas

### Common Question Types

#### Scenario-based Selection
> "A company needs to ensure their loan approval model doesn't discriminate based on gender..."
> → **SageMaker Clarify** (bias detection)

#### Feature Comparison
> "Which service explains WHY a model made a specific prediction?"
> → **SageMaker Clarify** (SHAP values / feature attributions)

#### Troubleshooting
> "A model shows different approval rates for different demographics in production..."
> → **SageMaker Clarify + Model Monitor** (bias drift monitoring)

#### Best Practice
> "What should a company do BEFORE training to check for data bias?"
> → Run **pre-training bias metrics** with SageMaker Clarify

### Gotchas
- ⚠️ **Clarify ≠ Model Monitor**: Clarify detects bias; Model Monitor monitors data quality/model quality. However, Clarify integrates WITH Model Monitor for bias drift monitoring
- ⚠️ **Pre-training metrics are model-agnostic** — they don't need a model, only data
- ⚠️ **Post-training metrics need model predictions** — they require an endpoint or model
- ⚠️ **Not all bias metrics can be satisfied simultaneously** — choosing the right metric depends on the use case and fairness definition
- ⚠️ **Shadow endpoints**: Clarify creates temporary (ephemeral) endpoints for analysis if you provide a model name instead of an existing endpoint
- ⚠️ **Clarify generates reports, not fixes** — it identifies bias but doesn't automatically mitigate it (mitigation is a separate step)

### Comparison Points

| Feature | SageMaker Clarify | SageMaker Debugger | SageMaker Model Monitor |
|---------|-------------------|-------------------|------------------------|
| **Purpose** | Bias detection + explainability + FM evaluation | Debug training jobs | Monitor production models |
| **When** | Pre/post-training + production + FM eval | During training | Production |
| **Detects** | Bias, feature importance, toxicity, accuracy | Vanishing gradients, overfitting | Data drift, model quality |
| **Output** | Bias reports, SHAP values, FM eval reports | Debug rules, tensors | Alerts, violations |
| **Model needed?** | Only for post-training & FM eval | Yes (during training) | Yes (deployed) |
| **GenAI support?** | Yes (FMEval) | No | No |

### Decision Tree
```
Need to understand fairness/bias?
├── YES → SageMaker Clarify
│   ├── Before training? → Pre-training bias metrics
│   ├── After training? → Post-training bias metrics
│   ├── In production? → Clarify + Model Monitor (bias drift)
│   └── Need explanations? → SHAP values / PDPs
│
├── Need to evaluate a foundation model? → SageMaker Clarify (FMEval)
│   ├── Automated metrics (accuracy, toxicity)? → Automatic evaluation
│   └── Subjective quality (helpfulness, style)? → Human evaluation
│
├── Need to debug training? → SageMaker Debugger
├── Need to monitor data quality? → Model Monitor (Data Quality)
├── Need to document model? → Model Cards (can include Clarify results)
└── Need content filtering for GenAI? → Bedrock Guardrails
```

---

## 6. Best Practices

### Security
- Use **IAM roles** with least-privilege access for Clarify processing jobs
- Encrypt data at rest in S3 using **KMS**
- Use **VPC configuration** for processing jobs to keep data in private network
- Restrict access to bias reports (may contain sensitive demographic information)

### Cost Optimization
- Use **spot instances** for Clarify processing jobs when possible
- Run pre-training analysis on a **sample** of data first to validate configuration
- Shadow endpoints are automatically deleted after analysis — no lingering costs
- Schedule bias monitoring at appropriate intervals (not too frequent)

### Performance
- Use **parallel processing** for large datasets
- Optimize the number of SHAP samples for faster computation
- Use existing endpoints instead of shadow endpoints for repeated analysis
- Consider **online explainability** for real-time use cases vs. batch processing

### Operations
- Integrate Clarify into **SageMaker Pipelines** for automated bias checks
- Set **CloudWatch alarms** on bias metrics for production monitoring
- Store bias reports in S3 with versioning for audit trails
- Use **Model Cards** to document Clarify findings for governance

---

## 7. Hands-On Labs

### Quick Setup — Pre-training Bias Analysis
```python
import boto3
from sagemaker import Session
from sagemaker.clarify import (
    SageMakerClarifyProcessor,
    DataConfig,
    BiasConfig
)

session = Session()
clarify_processor = SageMakerClarifyProcessor(
    role="<iam-role-arn>",
    instance_count=1,
    instance_type="ml.m5.xlarge",
    sagemaker_session=session
)

# Configure data
data_config = DataConfig(
    s3_data_input_path="s3://<bucket>/input/data.csv",
    s3_output_path="s3://<bucket>/output/bias-report",
    label="loan_approved",
    headers=["age", "income", "gender", "loan_approved"],
    dataset_type="text/csv"
)

# Configure bias detection
bias_config = BiasConfig(
    label_values_or_threshold=[1],  # positive outcome
    facet_name="gender",
    facet_values_or_threshold=[0]   # disfavored facet value
)

# Run pre-training bias analysis
clarify_processor.run_pre_training_bias(
    data_config=data_config,
    data_bias_config=bias_config
)
```

### Post-training Bias + Explainability
```python
from sagemaker.clarify import ModelConfig, SHAPConfig

model_config = ModelConfig(
    model_name="<model-name>",
    instance_type="ml.m5.xlarge",
    instance_count=1
)

shap_config = SHAPConfig(
    baseline=[["35", "50000", "1"]],  # baseline for SHAP
    num_samples=100
)

# Run post-training bias + explainability
clarify_processor.run_post_training_bias(
    data_config=data_config,
    data_bias_config=bias_config,
    model_config=model_config
)

clarify_processor.run_explainability(
    data_config=data_config,
    model_config=model_config,
    explainability_config=shap_config
)
```

### CLI — List Processing Jobs
```bash
aws sagemaker list-processing-jobs \
    --name-contains "clarify" \
    --sort-by CreationTime \
    --sort-order Descending
```

---

## 8. Sample Questions

### Question 1
**A financial company is developing an ML model that predicts loan eligibility for customers. The company wants a solution that can identify biases in both training data and model predictions. Which AWS service will meet these requirements?**

A) Amazon SageMaker Model Monitor
B) Amazon SageMaker Clarify
C) Amazon SageMaker Debugger
D) Amazon Bedrock Guardrails

**Correct Answer: B**

**Explanation**: SageMaker Clarify is purpose-built for detecting bias in both pre-training data (using model-agnostic metrics like CI, DPL, KL) and post-training model predictions (using metrics like DI, DPPL, AD). It also provides feature attributions via SHAP values to explain predictions.
- A is wrong: Model Monitor monitors data quality and model quality drift in production, not bias detection specifically.
- C is wrong: Debugger analyzes training job issues like vanishing gradients and overfitting.
- D is wrong: Bedrock Guardrails provides content filtering for generative AI, not traditional ML bias detection.

---

### Question 2
**A data science team wants to understand which features most influenced their fraud detection model's predictions. They need both global feature importance and per-instance explanations. Which approach should they use?**

A) Use SageMaker Debugger to capture tensor data during training
B) Use SageMaker Clarify with SHAP values for feature attributions
C) Use SageMaker Model Monitor to track feature drift
D) Use SageMaker Autopilot to automatically select features

**Correct Answer: B**

**Explanation**: SageMaker Clarify uses SHAP (SHapley Additive exPlanations) values based on cooperative game theory to provide both global feature importance (across the dataset) and local explanations (per-instance). This is exactly what the team needs.
- A is wrong: Debugger captures training tensors for debugging, not for explaining predictions.
- C is wrong: Model Monitor tracks feature drift, not feature importance.
- D is wrong: Autopilot automates model building, not model explanation.

---

### Question 3
**A healthcare company needs to check their patient readmission dataset for demographic bias BEFORE training any model. They want to know if the dataset has imbalanced representation across age groups. Which SageMaker Clarify capability should they use?**

A) Post-training bias metrics with Disparate Impact (DI)
B) SHAP values for feature attribution
C) Pre-training bias metrics with Class Imbalance (CI)
D) Partial Dependence Plots (PDPs)

**Correct Answer: C**

**Explanation**: Pre-training bias metrics are model-agnostic and analyze raw data before training. The Class Imbalance (CI) metric specifically measures the imbalance in the number of members between different facet values (e.g., age groups), which is exactly what the company needs.
- A is wrong: Post-training metrics require a trained model, but the company wants to check BEFORE training.
- B is wrong: SHAP values explain model predictions — they require a trained model.
- D is wrong: PDPs show marginal effects of features on predictions — they also require a trained model.

---

---

### Question 4
**A company wants to evaluate a foundation model deployed on SageMaker JumpStart for toxicity and accuracy before using it in production. They want automated scoring without human reviewers. Which approach should they use?**

A) Use SageMaker Model Monitor to track model quality metrics
B) Use SageMaker Clarify automatic model evaluation with built-in datasets
C) Use Amazon Bedrock Guardrails to filter toxic content
D) Use SageMaker Debugger to analyze model outputs

**Correct Answer: B**

**Explanation**: SageMaker Clarify's FMEval capability supports automatic model evaluation jobs that score foundation model responses for accuracy, toxicity, and semantic robustness using benchmark-based metrics. This can be done via Studio or the `fmeval` library without human reviewers.
- A is wrong: Model Monitor tracks data/model quality drift in production, not pre-deployment FM evaluation.
- C is wrong: Bedrock Guardrails filters content at inference time but doesn't evaluate model quality.
- D is wrong: Debugger analyzes training job issues, not foundation model output quality.

---

### Question 5
**A data science team notices that the feature importance rankings of their deployed model have changed significantly compared to training. The NDCG score dropped to 0.65. What does this indicate and what triggered the alert?**

A) The model accuracy has dropped below acceptable levels
B) Feature attribution drift has been detected because NDCG fell below the 0.90 threshold
C) The training data has become corrupted
D) The model needs to be retrained with more epochs

**Correct Answer: B**

**Explanation**: SageMaker Clarify uses NDCG (Normalized Discounted Cumulative Gain) to compare feature attribution rankings between training data and live data. An NDCG of 1.0 means rankings are identical. Clarify automatically raises an alert when NDCG drops below 0.90. An NDCG of 0.65 indicates significant feature attribution drift.
- A is wrong: NDCG measures feature ranking drift, not model accuracy directly.
- C is wrong: The drift is in live inference data feature attributions, not training data corruption.
- D is wrong: While retraining may be needed, the NDCG score specifically indicates feature attribution drift, not an epoch-related issue.

---

> **Exam Tip**: When you see keywords like "bias," "fairness," "explainability," "feature importance," or "SHAP" → think **SageMaker Clarify**. Remember the key distinction: pre-training metrics need only data, post-training metrics need data + model predictions. For foundation model evaluation (toxicity, accuracy, robustness) → also think **SageMaker Clarify (FMEval)**.

---

## 9. Citations
- [Pre-training Data Bias](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-detect-data-bias.html)
- [Feature Attributions that Use Shapley Values](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-shapley-values.html)
- [What are Foundation Model Evaluations?](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-foundation-model-evaluate-whatis.html)
- [Model Evaluation Results](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-foundation-model-reports.html)
- [Feature Attribution Drift for Models in Production](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-model-monitor-feature-attribution-drift.html)
- [SHAP Baselines for Explainability](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-feature-attribute-shap-baselines.html)
