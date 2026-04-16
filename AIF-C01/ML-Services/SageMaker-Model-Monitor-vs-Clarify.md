# SageMaker Model Monitor vs Clarify

## 1. Service/Topic Overview
- **Purpose**: Understand the distinct roles and overlap between SageMaker Model Monitor (production monitoring) and SageMaker Clarify (bias detection & explainability), and when to use each
- **Service Level**: Regional
- **Key Use Cases**:
  1. Monitoring deployed model data quality and accuracy drift (Model Monitor)
  2. Detecting bias in training data and model predictions (Clarify)
  3. Explaining model predictions with feature attributions (Clarify)
  4. Monitoring bias drift and feature attribution drift in production (Clarify integrated WITH Model Monitor)
  5. Triggering model retraining based on quality degradation (Model Monitor)
- **Exam Weight**: HIGH
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — model quality metrics
  - Domain 4: Guidelines for Responsible AI (14%) — bias, fairness, explainability
  - Domain 5: Security, Compliance, and Governance for AI Solutions (14%) — monitoring, governance
- **Task Statements**:
  - Task 1.3: Describe the ML development lifecycle (monitoring phase)
  - Task 4.1: Explain responsible AI considerations
  - Task 4.2: Recognize the importance of transparent and explainable models

---

## 2. Exam Keyword Mapping

### Trigger Words → Model Monitor
- "Data drift", "data quality", "schema violations"
- "Model accuracy degradation", "model quality"
- "Production monitoring", "endpoint monitoring"
- "Baseline constraints", "statistical drift"
- "Retraining trigger", "CloudWatch alerts"

### Trigger Words → Clarify
- "Bias detection", "fairness", "explainability"
- "SHAP values", "feature importance", "feature attribution"
- "Disparate impact", "demographic parity"
- "Pre-training bias", "post-training bias"
- "Why did the model predict..."

### Trigger Words → Both Together
- "Bias drift monitoring in production"
- "Feature attribution drift"
- "NDCG score", "production fairness monitoring"

### Anti-patterns
- ❌ "Debug training job" → **SageMaker Debugger** (not Model Monitor or Clarify)
- ❌ "Content filtering for GenAI" → **Bedrock Guardrails**
- ❌ "Document model metadata" → **SageMaker Model Cards**
- ❌ "Evaluate foundation model toxicity" → **SageMaker Clarify FMEval** (not Model Monitor)

---

## 3. Core Concepts

### The Key Relationship
```
┌─────────────────────────────────────────────────────────────────┐
│                    SageMaker Model Monitor                       │
│                  (Production Monitoring Framework)                │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────────┐ │
│  │ Data Quality  │  │ Model Quality │  │ Bias Drift +           │ │
│  │ Monitor       │  │ Monitor       │  │ Feature Attribution    │ │
│  │               │  │               │  │ Drift Monitor          │ │
│  │ (Model Monitor│  │ (Model Monitor│  │                        │ │
│  │  own container│  │  own container│  │ (Powered by CLARIFY)   │ │
│  └──────────────┘  └──────────────┘  └────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                    SageMaker Clarify                              │
│              (Bias Detection & Explainability)                    │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────────┐ │
│  │ Pre-training  │  │ Post-training │  │ Explainability         │ │
│  │ Bias Metrics  │  │ Bias Metrics  │  │ (SHAP / PDPs)          │ │
│  │ (data only)   │  │ (data+model)  │  │                        │ │
│  └──────────────┘  └──────────────┘  └────────────────────────┘ │
│                                                                  │
│  ┌──────────────┐  ┌──────────────────────────────────────────┐ │
│  │ FM Evaluation │  │ Production: Bias Drift + Feature         │ │
│  │ (FMEval)      │  │ Attribution Drift (via Model Monitor)    │ │
│  └──────────────┘  └──────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

### Critical Insight for Exam
> **Model Monitor is the scheduling/monitoring FRAMEWORK. Clarify is the bias/explainability ENGINE.**
> For bias drift and feature attribution drift monitoring in production, Clarify runs INSIDE Model Monitor's scheduling framework. They are NOT competing services — they are complementary.

### 4 Types of Model Monitor Monitoring

| # | Monitor Type | Powered By | What It Detects |
|---|-------------|-----------|-----------------|
| 1 | **Data Quality** | Model Monitor (Deequ) | Statistical drift in input data (schema, distribution changes) |
| 2 | **Model Quality** | Model Monitor | Accuracy/performance metric degradation (needs ground truth) |
| 3 | **Bias Drift** | **Clarify** (via Model Monitor) | Changes in bias metrics over time |
| 4 | **Feature Attribution Drift** | **Clarify** (via Model Monitor) | Changes in feature importance rankings (NDCG) |

---

## 4. Key Features & Components

### 4.1 Model Monitor — Data Quality Monitoring

**What it does**: Detects when production input data drifts from the training data baseline.

| Aspect | Details |
|--------|---------|
| **Baseline** | Computed using Deequ (open-source, Apache Spark) |
| **What it checks** | Schema (data types, missing values), statistical properties (mean, std, min, max, distribution) |
| **Input** | Captured inference requests (via Data Capture) |
| **Output** | `statistics.json` + `constraint_violations.json` |
| **Alerts** | CloudWatch metrics and alarms |
| **Ground truth needed?** | ❌ No |

**Data Quality Metrics Tracked**:
- Completeness (missing values)
- Data type consistency
- Numerical distribution (mean, std, min, max, quantiles)
- Categorical distribution (unique values, frequency)
- Schema violations (unexpected columns, type changes)

### 4.2 Model Monitor — Model Quality Monitoring

**What it does**: Tracks model performance metrics over time by comparing predictions to ground truth labels.

| Problem Type | Metrics Tracked |
|-------------|----------------|
| **Binary Classification** | Accuracy, Precision, Recall, F1, F0.5, F2, AUC, TPR, FPR, TNR, FNR, Confusion Matrix |
| **Multiclass Classification** | Accuracy, Weighted Precision, Weighted Recall, Weighted F1/F0.5/F2, Confusion Matrix |
| **Regression** | MAE, MSE, RMSE, R² |

> **Exam Tip**: Model Quality monitoring **requires ground truth labels**. You must merge ground truth with captured predictions. This is a key differentiator — Data Quality monitoring does NOT need ground truth.

### 4.3 Clarify — Bias Detection (Pre-training & Post-training)

| Phase | Metrics Count | Requires Model? | Key Metrics |
|-------|--------------|-----------------|-------------|
| **Pre-training** | 8 metrics | ❌ No (data only) | CI, DPL, KL, JS, LP, TVD, KS, CDD |
| **Post-training** | 13 metrics | ✅ Yes (needs predictions) | DPPL, DI, FT, AD, RD, TE, GE |

### 4.4 Clarify — Explainability

| Feature | Description |
|---------|-------------|
| **SHAP Values** | Feature importance per prediction (local) and overall (global) |
| **Partial Dependence Plots** | Marginal effect of a feature on predictions |
| **Asymmetric Shapley** | For features with causal/ordered relationships |
| **Online Explainability** | Real-time SHAP values during inference |

### 4.5 Clarify via Model Monitor — Production Bias & Attribution Drift

| Monitor Type | Metric Used | Alert Threshold | What It Detects |
|-------------|------------|-----------------|-----------------|
| **Bias Drift** | Clarify bias metrics (DI, DPPL, etc.) | User-defined | Fairness metrics shifting from baseline |
| **Feature Attribution Drift** | NDCG (Normalized Discounted Cumulative Gain) | < 0.90 | Feature importance rankings changing |

---

## 5. Exam Focus Areas

### The Master Comparison Table

| Dimension | Model Monitor (Data Quality) | Model Monitor (Model Quality) | Clarify (Bias) | Clarify (Explainability) |
|-----------|------------------------------|-------------------------------|-----------------|--------------------------|
| **Primary Question** | "Has the data changed?" | "Has the model gotten worse?" | "Is the model fair?" | "Why did the model predict this?" |
| **When Used** | Production only | Production only | Pre-training, Post-training, Production | Post-training, Production |
| **Needs Model?** | ❌ | ✅ | Pre: ❌ / Post: ✅ | ✅ |
| **Needs Ground Truth?** | ❌ | ✅ | ❌ | ❌ |
| **Key Output** | Statistics + violations | Performance metrics | Bias metrics + reports | SHAP values + PDPs |
| **Baseline** | Deequ statistics | Performance metrics | Bias metric values | SHAP baseline |
| **CloudWatch** | ✅ | ✅ | ✅ (via Model Monitor) | ✅ (via Model Monitor) |
| **Scheduling** | Model Monitor | Model Monitor | Model Monitor (production) | Model Monitor (production) |
| **GenAI Support** | ❌ | ❌ | ✅ (FMEval) | ❌ |

### Common Exam Traps

| Trap | Correct Answer |
|------|---------------|
| "Monitor model accuracy in production" | **Model Monitor (Model Quality)** — NOT Clarify |
| "Detect data drift in production" | **Model Monitor (Data Quality)** — NOT Clarify |
| "Detect bias in training data" | **Clarify (Pre-training)** — NOT Model Monitor |
| "Explain why model made a prediction" | **Clarify (SHAP)** — NOT Model Monitor |
| "Monitor bias drift in production" | **Clarify integrated WITH Model Monitor** — both are involved |
| "Feature importance changed in production" | **Clarify (Feature Attribution Drift) via Model Monitor** |
| "Model predictions differ across demographics" | **Clarify (Post-training bias)** — NOT Model Monitor |
| "Input data schema changed" | **Model Monitor (Data Quality)** — NOT Clarify |

### Decision Tree
```
What do you need to monitor/detect?
│
├── Data has changed (schema, distribution)?
│   └── Model Monitor → Data Quality Monitor
│
├── Model accuracy/performance degraded?
│   └── Model Monitor → Model Quality Monitor (needs ground truth)
│
├── Is the model fair/unbiased?
│   ├── Before training? → Clarify Pre-training Bias (data only)
│   ├── After training? → Clarify Post-training Bias (needs model)
│   └── In production? → Clarify Bias Drift via Model Monitor
│
├── Why did the model make this prediction?
│   ├── Batch analysis? → Clarify SHAP (processing job)
│   └── Real-time? → Clarify Online Explainability
│
├── Feature importance rankings changed?
│   └── Clarify Feature Attribution Drift via Model Monitor (NDCG)
│
└── Evaluate foundation model quality?
    └── Clarify FMEval (accuracy, toxicity, robustness)
```

### Gotchas
- ⚠️ Model Monitor has **4 monitoring types**, but only 2 are "native" (Data Quality, Model Quality). The other 2 (Bias Drift, Feature Attribution Drift) are **powered by Clarify**
- ⚠️ Model Quality monitoring **requires ground truth labels** — this is often the tricky part in production (delayed labels)
- ⚠️ Clarify can run **independently** (as a processing job) OR **within Model Monitor** (as a scheduled monitor)
- ⚠️ Data Quality uses **Deequ** (Apache Spark library) for baseline computation — NOT Clarify
- ⚠️ Model Monitor works on **tabular data only** — it can monitor outputs of image/text models but not the inputs
- ⚠️ Model Monitor does **NOT support multi-model endpoints**
- ⚠️ Data Capture stops at **high disk usage** (keep below 75%)

---

## 6. Best Practices

### Security
- Use **IAM roles** with least-privilege for monitoring jobs
- Enable **KMS encryption** for captured data in S3
- Use **VPC endpoints** for S3 and CloudWatch when running in custom VPC
- Restrict access to bias reports (contain demographic information)

### Cost Optimization
- Schedule monitoring at appropriate intervals (hourly for critical, daily for standard)
- Use **spot instances** for Clarify processing jobs
- Sample data for baseline computation on large datasets
- Data Capture stops at high disk usage — monitor disk utilization

### Performance
- Keep disk utilization below 75% to ensure continuous data capture
- Use existing endpoints instead of shadow endpoints for repeated Clarify analysis
- Optimize SHAP sample count for faster computation

### Operations
- Integrate both into **SageMaker Pipelines** for automated MLOps
- Set **CloudWatch alarms** on all 4 monitoring types
- Store all reports in S3 with versioning for audit trails
- Use **Model Cards** to document monitoring results for governance

---

## 7. Hands-On Labs

### Quick Setup — Enable Data Capture + Data Quality Monitor
```python
from sagemaker.model_monitor import (
    DefaultModelMonitor,
    DataCaptureConfig,
    CronExpressionGenerator
)

# Step 1: Enable Data Capture on endpoint
data_capture_config = DataCaptureConfig(
    enable_capture=True,
    sampling_percentage=100,
    destination_s3_uri="s3://<bucket>/data-capture"
)

# Step 2: Create Data Quality Monitor
data_quality_monitor = DefaultModelMonitor(
    role="<iam-role-arn>",
    instance_count=1,
    instance_type="ml.m5.xlarge"
)

# Step 3: Create baseline
data_quality_monitor.suggest_baseline(
    baseline_dataset="s3://<bucket>/training-data/baseline.csv",
    dataset_format=DatasetFormat.csv(header=True)
)

# Step 4: Schedule monitoring
data_quality_monitor.create_monitoring_schedule(
    monitor_schedule_name="data-quality-schedule",
    endpoint_input="<endpoint-name>",
    schedule_cron_expression=CronExpressionGenerator.hourly(),
    statistics=data_quality_monitor.baseline_statistics(),
    constraints=data_quality_monitor.suggested_constraints()
)
```

### Setup — Model Quality Monitor
```python
from sagemaker.model_monitor import ModelQualityMonitor

model_quality_monitor = ModelQualityMonitor(
    role="<iam-role-arn>",
    instance_count=1,
    instance_type="ml.m5.xlarge"
)

# Requires ground truth to be merged with predictions
model_quality_monitor.suggest_baseline(
    baseline_dataset="s3://<bucket>/baseline-with-ground-truth.csv",
    dataset_format=DatasetFormat.csv(header=True),
    problem_type="BinaryClassification",
    inference_attribute="prediction",
    ground_truth_attribute="label"
)
```

### Setup — Bias Drift Monitor (Clarify via Model Monitor)
```python
from sagemaker.model_monitor import ClarifyModelMonitor
from sagemaker.clarify import BiasConfig, DataConfig, ModelConfig

clarify_monitor = ClarifyModelMonitor(
    role="<iam-role-arn>",
    instance_count=1,
    instance_type="ml.m5.xlarge"
)

# Create bias baseline using Clarify
clarify_monitor.suggest_baseline(
    data_config=DataConfig(...),
    bias_config=BiasConfig(
        label_values_or_threshold=[1],
        facet_name="gender",
        facet_values_or_threshold=[0]
    ),
    model_config=ModelConfig(...)
)

# Schedule bias drift monitoring
clarify_monitor.create_monitoring_schedule(
    monitor_schedule_name="bias-drift-schedule",
    endpoint_input="<endpoint-name>",
    schedule_cron_expression=CronExpressionGenerator.daily()
)
```

### CLI — List All Monitoring Schedules
```bash
aws sagemaker list-monitoring-schedules \
    --sort-by CreationTime \
    --sort-order Descending
```

---

## 8. Sample Questions

### Question 1
**A company has deployed an ML model to a SageMaker real-time endpoint. They notice the model's accuracy has been declining over the past month. They want to set up automated monitoring to detect when the model's performance drops below acceptable thresholds. Which solution should they implement?**

A) SageMaker Clarify pre-training bias analysis
B) SageMaker Model Monitor with model quality monitoring
C) SageMaker Clarify with SHAP values
D) SageMaker Debugger with built-in rules

**Correct Answer: B**

**Explanation**: Model Monitor's model quality monitoring tracks performance metrics (accuracy, precision, recall, F1, AUC for classification; MAE, MSE, RMSE, R² for regression) against a baseline and alerts when they degrade. This requires merging ground truth labels with captured predictions.
- A is wrong: Pre-training bias analysis checks data fairness before training, not production accuracy.
- C is wrong: SHAP values explain predictions but don't monitor accuracy drift.
- D is wrong: Debugger analyzes training jobs, not production endpoints.

---

### Question 2
**A financial services company wants to ensure their loan approval model doesn't develop bias against certain demographic groups over time in production. Which combination of SageMaker services should they use?**

A) SageMaker Model Monitor data quality monitoring only
B) SageMaker Clarify bias drift monitoring integrated with Model Monitor
C) SageMaker Debugger with custom rules
D) SageMaker Model Monitor model quality monitoring only

**Correct Answer: B**

**Explanation**: For monitoring bias drift in production, SageMaker Clarify's bias detection capabilities are integrated with Model Monitor's scheduling framework. Clarify computes bias metrics (like Disparate Impact, DPPL) on a schedule and compares them to a baseline, alerting via CloudWatch when bias metrics drift.
- A is wrong: Data quality monitoring detects statistical drift in input data, not bias.
- C is wrong: Debugger is for training job analysis, not production bias monitoring.
- D is wrong: Model quality monitoring tracks accuracy metrics, not fairness/bias metrics.

---

### Question 3
**A data science team is setting up comprehensive monitoring for their production ML model. They want to detect: (1) changes in input data distribution, (2) accuracy degradation, and (3) fairness drift. How many distinct Model Monitor monitoring types do they need to configure?**

A) 1 — Model Monitor handles all three automatically
B) 2 — Data quality for (1) and model quality for (2) and (3)
C) 3 — Data quality for (1), model quality for (2), and bias drift (Clarify) for (3)
D) 3 — But they need separate services: Model Monitor for (1) and (2), Clarify for (3)

**Correct Answer: C**

**Explanation**: Model Monitor provides 4 monitoring types. For this scenario, they need 3: (1) Data Quality Monitor for input data distribution drift, (2) Model Quality Monitor for accuracy degradation (requires ground truth), and (3) Bias Drift Monitor (powered by Clarify, scheduled through Model Monitor) for fairness drift. All three are configured within the Model Monitor framework, but the bias drift monitor uses Clarify under the hood.
- A is wrong: Each monitoring type must be configured separately.
- B is wrong: Model quality tracks accuracy, not fairness/bias.
- D is wrong: While Clarify powers the bias drift monitor, it's configured and scheduled through Model Monitor — they're not separate.

---

> **Exam Tip**: The #1 thing to remember: Model Monitor is the **framework** (scheduling, data capture, alerting). Clarify is the **engine** for bias and explainability. In production, Clarify runs INSIDE Model Monitor for bias drift and feature attribution drift. For data quality and model quality, Model Monitor uses its own containers. When the exam says "monitor bias in production" → think **both** Clarify AND Model Monitor working together.

---

## 9. Citations
- [SageMaker Model Monitor Overview](https://docs.aws.amazon.com/sagemaker/latest/dg/model-monitor.html)
- [SageMaker Model Monitor - MLOps](https://docs.aws.amazon.com/sagemaker/latest/dg/model-monitor-mlops.html)
- [Data Quality Monitoring](https://docs.aws.amazon.com/sagemaker/latest/dg/model-monitor-data-quality.html)
- [Model Quality Metrics](https://docs.aws.amazon.com/sagemaker/latest/dg/model-monitor-model-quality-metrics.html)
- [Bias Drift for Models in Production](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-model-monitor-bias-drift.html)
- [Feature Attribution Drift](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-model-monitor-feature-attribution-drift.html)
- [Create a SHAP Baseline for Production](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-model-monitor-shap-baseline.html)
