# ML Model Evaluation Metrics

## 1. Topic Overview
- **Purpose**: Metrics used to measure the performance of traditional ML models across classification, regression, and ranking tasks
- **Service Level**: N/A (Conceptual topic; implemented via Amazon SageMaker Autopilot, Model Monitor, Clarify, Canvas)
- **Key Use Cases**:
  - Evaluating binary/multiclass classification models (spam detection, fraud detection)
  - Assessing regression model accuracy (price prediction, demand forecasting)
  - Monitoring model drift in production (SageMaker Model Monitor)
  - Comparing model candidates during AutoML (SageMaker Autopilot)
  - Detecting bias in model predictions (SageMaker Clarify)
- **Exam Weight**: HIGH
- **Exam Domain**: Domain 1: Fundamentals of AI and ML (20%)
- **Task Statements**:
  - Task 1.3 — Describe the ML development lifecycle (model performance metrics: accuracy, AUC, F1 score)
  - Task 1.2 — Select appropriate ML techniques (regression, classification, clustering)

---

## 2. Exam Keyword Mapping

### Trigger Words
- "model accuracy", "evaluate classification", "evaluate regression"
- "confusion matrix", "precision", "recall", "F1 score", "AUC-ROC"
- "false positive", "false negative", "true positive", "true negative"
- "MAE", "RMSE", "R-squared", "mean squared error"
- "model performance", "model quality", "model drift"

### Scenario Indicators
- "Minimize false positives" → **Optimize for Precision**
- "Minimize false negatives" → **Optimize for Recall**
- "Imbalanced dataset classification" → **Use F1 Score or AUC-ROC** (not Accuracy)
- "Predict a continuous value" → **Regression metrics (MAE, RMSE, R²)**
- "Monitor model quality in production" → **SageMaker Model Monitor**
- "Compare AutoML model candidates" → **SageMaker Autopilot metrics**

### Anti-patterns
- Don't use Accuracy alone on imbalanced datasets — misleading results
- Don't confuse ML classification metrics (Precision/Recall/F1) with FM text generation metrics (ROUGE/BLEU/BERTScore)
- Don't use regression metrics (MAE, RMSE) for classification problems or vice versa
- R² can be negative — doesn't mean the model is 0% accurate, means it's worse than predicting the mean

---

## 3. Core Concepts

### The Confusion Matrix (Foundation of Classification Metrics)

The confusion matrix is a 2×2 table (for binary classification) that shows all prediction outcomes:

```
                    Predicted Positive    Predicted Negative
Actual Positive         TP                    FN
Actual Negative         FP                    TN
```

| Term | Meaning | Example (Fraud Detection) |
|------|---------|--------------------------|
| **True Positive (TP)** | Correctly predicted positive | Fraud correctly flagged as fraud |
| **True Negative (TN)** | Correctly predicted negative | Legitimate transaction correctly passed |
| **False Positive (FP)** | Incorrectly predicted positive (Type I Error) | Legitimate transaction wrongly flagged as fraud |
| **False Negative (FN)** | Incorrectly predicted negative (Type II Error) | Fraud missed, passed as legitimate |

> **Exam Tip**: FP = "False alarm" / Type I Error. FN = "Missed detection" / Type II Error.

---

### Classification Metrics

#### Accuracy
- **Formula**: (TP + TN) / (TP + TN + FP + FN)
- **What it measures**: Overall percentage of correct predictions
- **When to use**: Balanced datasets where classes are roughly equal
- **⚠️ Limitation**: Misleading on imbalanced data — a model predicting "not fraud" 100% of the time gets 99% accuracy if only 1% of transactions are fraud

#### Precision
- **Formula**: TP / (TP + FP)
- **What it measures**: Of all positive predictions, how many were actually positive?
- **When to use**: When **false positives are costly**
- **Examples**:
  - Email spam filter — flagging a legitimate email as spam is annoying
  - Medical diagnosis — telling a healthy patient they're sick causes unnecessary stress/treatment
- **Mnemonic**: **P**recision = **P**ositive predictions that are correct

#### Recall (Sensitivity / True Positive Rate)
- **Formula**: TP / (TP + FN)
- **What it measures**: Of all actual positives, how many did the model catch?
- **When to use**: When **false negatives are costly**
- **Examples**:
  - Cancer screening — missing a cancer case (FN) is life-threatening
  - Fraud detection — missing fraud (FN) means financial loss
  - Security threat detection — missing a threat (FN) is dangerous
- **Mnemonic**: **R**ecall = how much of the **R**eal positives did we find?

#### F1 Score
- **Formula**: 2 × (Precision × Recall) / (Precision + Recall)
- **What it measures**: Harmonic mean of Precision and Recall — balances both
- **When to use**: When you need a **single balanced metric** and can't afford to sacrifice either precision or recall
- **Why harmonic mean?**: Penalizes extreme imbalance — if either P or R is very low, F1 drops significantly
- **Score Range**: 0 to 1 (1 = perfect)

#### Specificity (True Negative Rate)
- **Formula**: TN / (TN + FP)
- **What it measures**: Of all actual negatives, how many were correctly identified?
- **Complement of**: False Positive Rate (FPR = 1 - Specificity)

### Precision vs. Recall Trade-off

```
High Threshold (conservative) → Fewer positive predictions
  → Higher Precision, Lower Recall
  → Fewer false positives, more false negatives

Low Threshold (aggressive) → More positive predictions
  → Lower Precision, Higher Recall
  → More false positives, fewer false negatives
```

| Priority | Optimize For | Example |
|----------|-------------|---------|
| Avoid false alarms | **Precision** | Spam filter, legal document review |
| Catch every case | **Recall** | Cancer detection, fraud detection, security |
| Balance both | **F1 Score** | General-purpose classification |

---

### AUC-ROC (Area Under the Receiver Operating Characteristic Curve)

#### ROC Curve
- Plots **True Positive Rate (Recall)** vs **False Positive Rate (1 - Specificity)** at various classification thresholds
- Shows the trade-off between sensitivity and specificity

#### AUC (Area Under the Curve)
- **Score Range**: 0 to 1
- **Interpretation**:
  - **1.0** = Perfect classifier
  - **0.5** = Random guessing (no discrimination ability) — diagonal line
  - **< 0.5** = Worse than random (model is inverted)
- **When to use**: 
  - Comparing models regardless of threshold
  - Imbalanced datasets (better than accuracy)
  - When you need a threshold-independent metric
- **Key advantage**: Evaluates model across ALL possible thresholds, not just one

| AUC Value | Model Quality |
|-----------|--------------|
| 0.9 – 1.0 | Excellent |
| 0.8 – 0.9 | Good |
| 0.7 – 0.8 | Fair |
| 0.5 – 0.7 | Poor |
| 0.5 | No discrimination (random) |

---

### Regression Metrics

#### MAE (Mean Absolute Error)
- **Formula**: Average of |Actual - Predicted|
- **What it measures**: Average magnitude of errors, treating all errors equally
- **Units**: Same as the target variable
- **When to use**: When all errors should be weighted equally; easy to interpret
- **Advantage**: Not sensitive to outliers (compared to MSE/RMSE)

#### MSE (Mean Squared Error)
- **Formula**: Average of (Actual - Predicted)²
- **What it measures**: Average of squared errors
- **Units**: Squared units of target variable
- **When to use**: When large errors should be penalized more heavily
- **Disadvantage**: Hard to interpret due to squared units

#### RMSE (Root Mean Squared Error)
- **Formula**: √MSE
- **What it measures**: Standard deviation of prediction errors
- **Units**: Same as the target variable (easier to interpret than MSE)
- **When to use**: Most commonly used regression metric; penalizes large errors more than MAE
- **Key property**: RMSE ≥ MAE always. If RMSE >> MAE, there are large outlier errors

#### MAPE (Mean Absolute Percentage Error)
- **Formula**: Average of |Actual - Predicted| / |Actual| × 100%
- **What it measures**: Average percentage error
- **When to use**: When you want scale-independent error measurement
- **⚠️ Limitation**: Undefined when actual value = 0; biased toward low predictions

#### R² (R-Squared / Coefficient of Determination)
- **Formula**: 1 - (Sum of Squared Residuals / Total Sum of Squares)
- **What it measures**: Proportion of variance in the target explained by the model
- **Score Range**: -∞ to 1
  - **1.0** = Perfect fit (model explains all variance)
  - **0.0** = Model is no better than predicting the mean
  - **Negative** = Model is worse than predicting the mean
- **When to use**: Understanding how well the model explains the data
- **⚠️ Gotcha**: R² can be negative — this means the model performs worse than a simple mean baseline

### Regression Metrics Comparison

| Metric | Sensitive to Outliers? | Same Units as Target? | Scale Independent? |
|--------|----------------------|----------------------|-------------------|
| **MAE** | No | ✅ Yes | No |
| **MSE** | ✅ Yes (squared) | No (squared units) | No |
| **RMSE** | ✅ Yes | ✅ Yes | No |
| **MAPE** | No | No (percentage) | ✅ Yes |
| **R²** | ✅ Yes | No (ratio) | ✅ Yes |

---

## 4. Key Features & Components

### AWS Services for Model Evaluation

#### SageMaker Autopilot
- Automatically computes metrics for model candidates
- Default optimization metrics:
  - Binary classification → **F1**
  - Multiclass classification → **Accuracy**
  - Regression → **MSE**
- Supports: Accuracy, AUC, F1, F1macro, Precision, PrecisionMacro, Recall, RecallMacro, MAE, MSE, R2, RMSE, LogLoss, BalancedAccuracy
- Uses k-fold cross-validation to reduce overfitting

#### SageMaker Model Monitor
- Monitors model quality metrics in production
- Computes regression metrics: MAE, MSE, RMSE, R²
- Computes binary classification metrics: Confusion matrix, Recall, Precision, Accuracy, AUC, F1, F0.5, F2, TPR, TNR, FPR, FNR
- Computes multiclass metrics: Accuracy, Weighted Recall, Weighted Precision, Weighted F1
- Sends metrics to CloudWatch for alerting
- Detects model drift by comparing against baseline

#### SageMaker Clarify
- Evaluates model for bias using fairness metrics
- Computes feature importance for explainability
- Pre-training bias metrics (data-level) and post-training bias metrics (model-level)

#### SageMaker Canvas
- Visual interface showing advanced metrics
- Confusion matrix visualization for classification
- Residuals and error density charts for regression
- Precision-recall curves

### Cross-Validation
- **Purpose**: Reduce overfitting and get reliable metric estimates
- **k-Fold**: Split data into k parts, train on k-1, validate on 1, rotate
- **Used by**: SageMaker Autopilot (automatic k-fold)
- **Benefit**: Every data point is used for both training and validation

---

## 5. Exam Focus Areas

### Common Question Types
- **"Which metric for imbalanced data?"** → F1 Score or AUC-ROC (NOT Accuracy)
- **"Minimize false negatives?"** → Optimize for Recall
- **"Minimize false positives?"** → Optimize for Precision
- **"Single metric to balance precision and recall?"** → F1 Score
- **"Threshold-independent metric?"** → AUC-ROC
- **"Regression model error in same units?"** → MAE or RMSE
- **"Penalize large errors more?"** → RMSE (or MSE)
- **"How much variance does the model explain?"** → R²
- **"Monitor model quality in production?"** → SageMaker Model Monitor + CloudWatch

### Gotchas
- **Accuracy is misleading on imbalanced data** — this is the #1 exam trap
- **R² can be negative** — doesn't mean 0%, means worse than predicting the mean
- **RMSE ≥ MAE always** — large gap means outlier errors exist
- **AUC = 0.5 means random** — not 50% accuracy, but no discrimination ability
- **F1 uses harmonic mean** (not arithmetic) — penalizes imbalance between P and R
- **Precision and Recall are inversely related** — improving one typically hurts the other
- **MAPE fails when actual = 0** — undefined division

### Comparison: Classification Metrics Decision Guide

```
Balanced dataset?
├── Yes → Accuracy is fine
└── No (imbalanced) → DON'T use Accuracy alone
    ├── False Positives costly? → Precision
    ├── False Negatives costly? → Recall
    ├── Need balance? → F1 Score
    └── Need threshold-independent? → AUC-ROC
```

### Comparison: Regression Metrics Decision Guide

```
Need to evaluate regression model?
├── Simple, interpretable error → MAE
├── Penalize large errors → RMSE
├── Scale-independent comparison → MAPE or R²
├── Variance explained → R²
└── Production monitoring → RMSE + R² (SageMaker Model Monitor)
```

### FM Metrics vs. ML Metrics (Key Distinction)

| Category | Metrics | Used For |
|----------|---------|----------|
| **FM Text Generation** | ROUGE, BLEU, BERTScore | Summarization, translation, text quality |
| **ML Classification** | Accuracy, Precision, Recall, F1, AUC-ROC | Spam, fraud, image classification |
| **ML Regression** | MAE, MSE, RMSE, R², MAPE | Price prediction, forecasting |

> See also: [FM Evaluation Metrics](../Fundamentals/FM-Evaluation-Metrics.md) for foundation model-specific metrics

---

## 6. Best Practices

### Security
- Encrypt evaluation datasets at rest (S3 SSE, KMS) and in transit (TLS)
- Use IAM roles to restrict access to Model Monitor and evaluation results
- Ensure ground truth labels don't contain PII

### Cost Optimization
- Use SageMaker Autopilot to automatically select the best metric and model
- Set up Model Monitor on a schedule (not continuous) to reduce compute costs
- Use CloudWatch alarms to trigger retraining only when drift is detected

### Performance
- Choose the right metric for your business problem — don't default to accuracy
- Use cross-validation for reliable metric estimates on small datasets
- Monitor multiple metrics (not just one) to get a complete picture
- Set metric thresholds aligned with business requirements

### Operations
- Establish baseline metrics before deployment
- Configure Model Monitor to compare production metrics against baseline
- Set CloudWatch alarms on key metrics (e.g., alert if AUC drops below 0.8)
- Automate retraining pipelines when model drift is detected

---

## 7. Hands-On Labs

### Quick Setup: View Autopilot Metrics
1. Open SageMaker Console → Autopilot
2. Create experiment with your dataset
3. Select problem type (classification/regression)
4. Review model leaderboard with computed metrics
5. Compare candidates by F1, AUC, Accuracy, RMSE

### CLI: Create Model Monitor Schedule
```bash
aws sagemaker create-model-quality-job-definition \
    --job-definition-name my-model-quality-job \
    --model-quality-app-specification '{
        "ImageUri": "...",
        "ProblemType": "BinaryClassification"
    }' \
    --model-quality-baseline-config '{
        "BaseliningJobName": "my-baseline-job"
    }'
```

### Python: Compute Metrics with sklearn
```python
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score,
    f1_score, roc_auc_score, confusion_matrix,
    mean_absolute_error, mean_squared_error, r2_score
)
import numpy as np

# Classification
y_true = [1, 0, 1, 1, 0, 1, 0, 0]
y_pred = [1, 0, 1, 0, 0, 1, 1, 0]

print(f"Accuracy:  {accuracy_score(y_true, y_pred):.2f}")
print(f"Precision: {precision_score(y_true, y_pred):.2f}")
print(f"Recall:    {recall_score(y_true, y_pred):.2f}")
print(f"F1 Score:  {f1_score(y_true, y_pred):.2f}")
print(f"AUC-ROC:   {roc_auc_score(y_true, y_pred):.2f}")
print(f"Confusion Matrix:\n{confusion_matrix(y_true, y_pred)}")

# Regression
y_actual = [3.0, 5.0, 2.5, 7.0]
y_predicted = [2.8, 5.2, 2.0, 6.5]

print(f"MAE:  {mean_absolute_error(y_actual, y_predicted):.2f}")
print(f"RMSE: {np.sqrt(mean_squared_error(y_actual, y_predicted)):.2f}")
print(f"R²:   {r2_score(y_actual, y_predicted):.2f}")
```

---

## 8. Sample Questions

### Question 1
**A company is building a fraud detection model. Only 1% of transactions are fraudulent. A data scientist notices the model has 99% accuracy. Which statement is MOST likely true?**

A) The model is performing excellently and is ready for production  
B) The model is likely predicting "not fraud" for almost all transactions  
C) The model has a high F1 score  
D) The model has a high AUC-ROC score  

**Correct Answer: B) The model is likely predicting "not fraud" for almost all transactions**

- ✅ **B)** With 99% of data being "not fraud", a model that always predicts "not fraud" achieves 99% accuracy while catching zero fraud cases. This is the classic imbalanced dataset trap.
- ❌ A) High accuracy on imbalanced data is misleading — the model may have zero recall for fraud
- ❌ C) F1 would be very low (or 0) if the model never predicts fraud, since recall = 0
- ❌ D) AUC-ROC would be ~0.5 (random) if the model has no discrimination ability

---

### Question 2
**A healthcare company is building a cancer screening model. Missing a cancer case (false negative) could be life-threatening. Which metric should the team prioritize?**

A) Precision  
B) Accuracy  
C) Recall  
D) Specificity  

**Correct Answer: C) Recall**

- ✅ **C) Recall** = TP / (TP + FN). Maximizing recall minimizes false negatives, ensuring the model catches as many cancer cases as possible.
- ❌ A) Precision minimizes false positives — important but not the priority when lives are at stake
- ❌ B) Accuracy is misleading if cancer cases are rare (imbalanced data)
- ❌ D) Specificity measures true negative rate — focuses on correctly identifying healthy patients, not catching cancer

---

### Question 3
**A data scientist is evaluating a regression model that predicts house prices. They notice R² = -0.3. What does this indicate?**

A) The model explains 30% of the variance in house prices  
B) The model is 30% accurate  
C) The model performs worse than simply predicting the average house price  
D) The model has a 30% error rate  

**Correct Answer: C) The model performs worse than simply predicting the average house price**

- ✅ **C)** A negative R² means the model's predictions are worse than a baseline that always predicts the mean value. The model needs significant improvement.
- ❌ A) R² = -0.3 does NOT mean 30% variance explained — negative R² means the model is worse than the mean baseline
- ❌ B) R² is not a percentage accuracy measure
- ❌ D) R² does not directly represent error rate

---

**Exam Tip**: The AIF-C01 exam specifically calls out accuracy, AUC, and F1 score in Task 1.3. Remember: **Accuracy fails on imbalanced data** (use F1 or AUC instead), **Recall catches everything** (minimize FN), **Precision avoids false alarms** (minimize FP), and **AUC is threshold-independent**. For regression, know that R² can be negative and RMSE penalizes large errors more than MAE.
