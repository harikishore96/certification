# Confusion Matrix

## 1. Topic Overview
- **Purpose**: A tabular visualization that summarizes the performance of a classification model by comparing predicted labels against actual labels across all possible outcome combinations
- **Service Level**: N/A (Conceptual topic; visualized in SageMaker Autopilot, Canvas, Clarify, Debugger, and Model Monitor)
- **Key Use Cases**:
  - Evaluating binary classification models (fraud detection, spam filtering, medical diagnosis)
  - Evaluating multiclass classification models (image classification, sentiment analysis)
  - Identifying specific types of errors a model makes (false positives vs. false negatives)
  - Deriving all classification metrics (Precision, Recall, F1, Accuracy, Specificity)
  - Tuning classification thresholds based on business cost of different error types
- **Exam Weight**: HIGH
- **Exam Domain**: Domain 1: Fundamentals of AI and ML (20%)
- **Task Statements**:
  - Task 1.3 — Describe the ML development lifecycle (model performance metrics: accuracy, AUC, F1 score)
  - Task 3.4 — Describe methods to evaluate foundation model performance

---

## 2. Exam Keyword Mapping

### Trigger Words
- "confusion matrix", "classification performance", "prediction outcomes"
- "true positive", "true negative", "false positive", "false negative"
- "Type I error", "Type II error"
- "misclassification", "error analysis", "prediction errors"

### Scenario Indicators
- "Evaluate a classification model's performance" → **Build/interpret a confusion matrix**
- "Understand where the model is making mistakes" → **Analyze confusion matrix quadrants**
- "Cost of different types of errors" → **Compare FP vs. FN from confusion matrix**
- "Model predicts fraud/spam/disease" → **Binary confusion matrix analysis**
- "Classify images into multiple categories" → **Multiclass confusion matrix (N×N)**

### Anti-patterns
- Don't use confusion matrix for regression problems — it's classification only
- Don't confuse with correlation matrix (statistics) or transformation matrix (linear algebra)
- Don't rely solely on the diagonal — off-diagonal cells reveal the most important insights

---

## 3. Core Concepts

### Binary Confusion Matrix (2×2)

```
                        PREDICTED
                   Positive    Negative
              ┌────────────┬────────────┐
   Positive   │     TP     │     FN     │
ACTUAL        ├────────────┼────────────┤
   Negative   │     FP     │     TN     │
              └────────────┴────────────┘
```

### The Four Quadrants

| Quadrant | Full Name | Meaning | Also Called |
|----------|-----------|---------|-------------|
| **TP** | True Positive | Model said YES, actually YES | Hit, Correct Detection |
| **TN** | True Negative | Model said NO, actually NO | Correct Rejection |
| **FP** | False Positive | Model said YES, actually NO | **Type I Error**, False Alarm |
| **FN** | False Negative | Model said NO, actually YES | **Type II Error**, Miss |

> **Memory Aid**: The first word (True/False) tells you if the model was RIGHT or WRONG. The second word (Positive/Negative) tells you WHAT the model predicted.

### Real-World Examples

| Scenario | TP | FP (Type I) | FN (Type II) | TN |
|----------|----|----|----|----|
| **Fraud Detection** | Fraud caught | Legit flagged as fraud | Fraud missed | Legit passed |
| **Spam Filter** | Spam caught | Good email in spam | Spam in inbox | Good email in inbox |
| **Cancer Screening** | Cancer detected | Healthy told they have cancer | Cancer missed | Healthy confirmed healthy |
| **Airport Security** | Threat detected | Innocent flagged | Threat missed | Safe passenger cleared |

### Multiclass Confusion Matrix (N×N)

For N classes, the matrix is N×N. Diagonal = correct predictions. Off-diagonal = misclassifications.

```
Example: 3-class sentiment (Positive, Neutral, Negative)

                    PREDICTED
              Positive  Neutral  Negative
   Positive  [  45       3        2     ]   ← 45 correct, 5 misclassified
ACTUAL Neutral  [   2      38        5     ]   ← 38 correct, 7 misclassified
   Negative  [   1       4       40     ]   ← 40 correct, 5 misclassified
```

- **Diagonal cells**: Correct predictions (TP for each class)
- **Off-diagonal cells**: Misclassifications — shows WHICH classes get confused with each other
- **Row sum**: Total actual instances of that class
- **Column sum**: Total predicted instances of that class

### Deriving All Classification Metrics from the Confusion Matrix

Every classification metric comes from the confusion matrix:

| Metric | Formula | What It Answers |
|--------|---------|-----------------|
| **Accuracy** | (TP + TN) / (TP + TN + FP + FN) | Overall, how often is the model correct? |
| **Precision** | TP / (TP + FP) | When model says positive, how often is it right? |
| **Recall** (Sensitivity) | TP / (TP + FN) | Of all actual positives, how many did we catch? |
| **Specificity** | TN / (TN + FP) | Of all actual negatives, how many did we correctly reject? |
| **F1 Score** | 2 × (Precision × Recall) / (Precision + Recall) | Balanced measure of Precision and Recall |
| **False Positive Rate** | FP / (FP + TN) = 1 − Specificity | How often do we falsely alarm? |
| **False Negative Rate** | FN / (FN + TP) = 1 − Recall | How often do we miss? |

### Worked Example

A fraud detection model evaluates 1,000 transactions (20 actual fraud, 980 legitimate):

```
                    PREDICTED
                 Fraud    Legit
ACTUAL  Fraud  [  15        5   ]   ← 20 actual fraud
        Legit  [  10      970   ]   ← 980 actual legit
```

| Metric | Calculation | Value |
|--------|-------------|-------|
| **Accuracy** | (15 + 970) / 1000 | 98.5% |
| **Precision** | 15 / (15 + 10) | 60% |
| **Recall** | 15 / (15 + 5) | 75% |
| **Specificity** | 970 / (970 + 10) | 99% |
| **F1 Score** | 2 × (0.60 × 0.75) / (0.60 + 0.75) | 66.7% |

> **Key Insight**: Accuracy is 98.5% but Precision is only 60% — the model looks great overall but 40% of its fraud alerts are false alarms. This is why the confusion matrix matters more than accuracy alone.

### Multiclass Averaging Methods

When computing Precision/Recall/F1 for multiclass problems, AWS services use these averaging methods:

| Method | How It Works | When to Use |
|--------|-------------|-------------|
| **Micro** | Aggregate TP, FP, FN across all classes, then compute | When all instances matter equally |
| **Macro** | Compute metric per class, then average (unweighted) | When all classes matter equally regardless of size |
| **Weighted** | Compute metric per class, then weighted average by class size | When class imbalance exists and larger classes should count more |

> **AWS Reference**: SageMaker Debugger XGBoost reports provide micro, macro, and weighted metrics on precision, recall, and F1-score. SageMaker Autopilot uses F1macro for multiclass classification by default.
> 
> Source: [SageMaker Debugger XGBoost Report](https://docs.aws.amazon.com/sagemaker/latest/dg/debugger-training-xgboost-report-walkthrough.html)

---

## 4. Key Features & Components

### AWS Services That Use Confusion Matrices

| Service | How It Uses Confusion Matrix |
|---------|------------------------------|
| **SageMaker Autopilot** | Auto-generates confusion matrix for classification experiments; default metric: F1 (binary), F1macro (multiclass) |
| **SageMaker Canvas** | Visual confusion matrix in the model analysis tab; shows per-class Precision, Recall, F1 |
| **SageMaker Debugger** | XGBoost training reports include confusion matrix visualization with evaluation metrics |
| **SageMaker Model Monitor** | Computes confusion matrix-derived metrics (TP, TN, FP, FN, Precision, Recall, F1, AUC) for production endpoints |
| **SageMaker Clarify** | Uses confusion matrix metrics to detect post-training bias (e.g., difference in recall across demographic groups) |

> Source: [SageMaker Canvas Metrics Reference](https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-metrics.html)

### SageMaker Canvas Classification Metrics (from Confusion Matrix)

**2-category prediction**: Accuracy, AUC, BalancedAccuracy, F1, LogLoss, Precision, Recall

**3+ category prediction**: Accuracy, BalancedAccuracy, F1macro, LogLoss, PrecisionMacro, RecallMacro

### Balanced Accuracy (Important for Imbalanced Data)
- **Formula**: 0.5 × ((TP/P) + (TN/N))
- Normalizes by class size — gives equal weight to each class
- Better than standard accuracy when classes are imbalanced (e.g., 1% fraud)

### Classification Threshold and the Confusion Matrix
- Most classifiers output a probability (0.0 to 1.0)
- The **threshold** determines the cutoff for positive vs. negative prediction
- Changing the threshold shifts values between quadrants:
  - **Lower threshold** → More positives predicted → TP↑, FP↑, FN↓, TN↓ → Higher Recall, Lower Precision
  - **Higher threshold** → Fewer positives predicted → TP↓, FP↓, FN↑, TN↑ → Higher Precision, Lower Recall
- The ROC curve plots this trade-off across all thresholds

---

## 5. Exam Focus Areas

### Common Question Types

- **"What does a confusion matrix show?"** → Comparison of predicted vs. actual labels for classification
- **"Which quadrant represents missed fraud?"** → False Negative (FN)
- **"Model has high accuracy but low recall — what's happening?"** → Many false negatives; model misses actual positives (likely imbalanced data)
- **"How to reduce false positives?"** → Increase classification threshold (improves Precision)
- **"How to reduce false negatives?"** → Decrease classification threshold (improves Recall)
- **"Which AWS service visualizes a confusion matrix?"** → SageMaker Canvas, SageMaker Debugger, SageMaker Autopilot

### Gotchas
- **Accuracy trap**: A model can have 99% accuracy and still be useless if the dataset is imbalanced — always check the confusion matrix
- **FP ≠ FN cost**: In most real-world scenarios, false positives and false negatives have very different business costs — the confusion matrix helps quantify this
- **Type I vs. Type II**: Type I = FP (false alarm), Type II = FN (miss) — exam may use either terminology
- **Confusion matrix is for classification ONLY** — never for regression
- **Multiclass**: For N classes, the matrix is N×N, not 2×2
- **Threshold is not shown**: The confusion matrix shows results at ONE specific threshold — different thresholds produce different matrices

### Comparison: Confusion Matrix vs. Other Evaluation Tools

| Tool | What It Shows | When to Use |
|------|--------------|-------------|
| **Confusion Matrix** | Exact counts of TP, TN, FP, FN at one threshold | Detailed error analysis, understanding specific mistakes |
| **ROC Curve** | TPR vs. FPR across all thresholds | Comparing models, threshold-independent evaluation |
| **Precision-Recall Curve** | Precision vs. Recall across thresholds | Imbalanced datasets where positive class is rare |
| **AUC Score** | Single number summarizing ROC curve | Quick model comparison |

### Decision Tree: Reading a Confusion Matrix

```
Look at the confusion matrix →
├── High TP + High TN (diagonal) → Model is performing well overall
├── High FP → Model has too many false alarms
│   └── Action: Increase threshold OR optimize for Precision
├── High FN → Model is missing actual positives
│   └── Action: Decrease threshold OR optimize for Recall
├── High FP + High FN → Model is confused, needs retraining
│   └── Action: More/better training data, feature engineering, different algorithm
└── Imbalanced matrix (one class dominates) → Check for data imbalance
    └── Action: Use BalancedAccuracy, F1, or AUC instead of Accuracy
```

---

## 6. Best Practices

### Security
- Ensure evaluation datasets don't contain PII — use anonymized or synthetic data
- Restrict access to confusion matrix results via IAM (may reveal model weaknesses)
- Encrypt evaluation outputs at rest (S3 SSE-KMS) and in transit (TLS)

### Cost Optimization
- Use SageMaker Autopilot to automatically generate confusion matrices during model selection — no manual computation needed
- Use SageMaker Canvas for visual confusion matrix analysis without writing code
- Schedule Model Monitor evaluations (not continuous) to reduce compute costs

### Performance
- Always analyze the confusion matrix alongside accuracy — never rely on accuracy alone
- For imbalanced datasets, focus on Recall, Precision, F1, or BalancedAccuracy
- Use the confusion matrix to identify which classes are most confused — target those with additional training data
- Adjust classification threshold based on business requirements (cost of FP vs. FN)

### Operations
- Establish baseline confusion matrix metrics before deployment
- Monitor confusion matrix metrics in production with SageMaker Model Monitor
- Set CloudWatch alarms when FP or FN rates exceed acceptable thresholds
- Track confusion matrix changes over time to detect model drift

---

## 7. Hands-On Labs

### Quick Setup: View Confusion Matrix in SageMaker Canvas
1. Open SageMaker Console → Canvas
2. Import a classification dataset (e.g., fraud detection CSV)
3. Select target column → Build model
4. After training, click "Analyze" → View confusion matrix visualization
5. Review per-class Precision, Recall, and F1 scores

### Python: Build and Visualize a Confusion Matrix
```python
from sklearn.metrics import confusion_matrix, ConfusionMatrixDisplay
import matplotlib.pyplot as plt

y_true = [1, 0, 1, 1, 0, 1, 0, 0, 1, 0]
y_pred = [1, 0, 1, 0, 0, 1, 1, 0, 1, 0]

cm = confusion_matrix(y_true, y_pred)
print(cm)
# [[3, 1],   ← TN=3, FP=1
#  [1, 5]]   ← FN=1, TP=5  (sklearn format: row=actual, col=predicted, 0 first)

# Note: sklearn orders labels [0, 1] so:
# Row 0 = Actual Negative, Row 1 = Actual Positive
# Col 0 = Predicted Negative, Col 1 = Predicted Positive

disp = ConfusionMatrixDisplay(cm, display_labels=["Negative", "Positive"])
disp.plot(cmap="Blues")
plt.title("Confusion Matrix")
plt.show()
```

### CLI: Evaluate Model with SageMaker Model Monitor
```bash
aws sagemaker create-model-quality-job-definition \
    --job-definition-name fraud-model-quality \
    --model-quality-app-specification '{
        "ImageUri": "156813124566.dkr.ecr.us-east-1.amazonaws.com/sagemaker-model-monitor-analyzer",
        "ProblemType": "BinaryClassification"
    }' \
    --model-quality-baseline-config '{
        "BaseliningJobName": "fraud-baseline-job"
    }' \
    --job-resources '{
        "ClusterConfig": {
            "InstanceCount": 1,
            "InstanceType": "ml.m5.xlarge",
            "VolumeSizeInGB": 20
        }
    }'
```

---

## 8. Sample Questions

### Question 1
**A company builds a model to detect fraudulent credit card transactions. The confusion matrix shows: TP=80, FP=20, FN=40, TN=860. The company is most concerned about missing fraudulent transactions. Which metric should they focus on improving?**

A) Accuracy  
B) Precision  
C) Recall  
D) Specificity  

**Correct Answer: C) Recall**

- ✅ **C)** Recall = TP / (TP + FN) = 80 / (80 + 40) = 66.7%. Missing fraud = False Negatives. Improving Recall means reducing FN, catching more actual fraud.
- ❌ A) Accuracy = (80 + 860) / 1000 = 94% — looks great but hides the 40 missed fraud cases
- ❌ B) Precision = 80 / (80 + 20) = 80% — measures false alarms, not missed fraud
- ❌ D) Specificity = 860 / (860 + 20) = 97.7% — measures correct rejection of legitimate transactions

---

### Question 2
**A data scientist examines a confusion matrix for a binary classification model and notices a very high number in the False Positive (FP) cell. What does this indicate?**

A) The model is missing many actual positive cases  
B) The model is incorrectly predicting many negative cases as positive  
C) The model has high recall  
D) The model has high accuracy  

**Correct Answer: B) The model is incorrectly predicting many negative cases as positive**

- ✅ **B)** False Positive means the model predicted Positive, but the actual label was Negative — the model is generating too many false alarms.
- ❌ A) Missing actual positives = False Negatives (FN), not FP
- ❌ C) High FP actually hurts Precision, not Recall. High Recall means low FN.
- ❌ D) High FP reduces accuracy (more incorrect predictions)

---

### Question 3
**A machine learning team is evaluating a 3-class image classification model (Cat, Dog, Bird). The confusion matrix shows that the model frequently misclassifies Cat images as Dog. Which action would MOST likely improve the model's performance?**

A) Increase the number of Bird training images  
B) Add more Cat and Dog training images that highlight distinguishing features  
C) Remove the Bird class from the model  
D) Switch to a regression model  

**Correct Answer: B) Add more Cat and Dog training images that highlight distinguishing features**

- ✅ **B)** The confusion matrix reveals the specific misclassification pattern (Cat→Dog). Adding more diverse training examples of both classes helps the model learn distinguishing features.
- ❌ A) Adding Bird images doesn't address the Cat/Dog confusion
- ❌ C) Removing Bird doesn't fix the Cat/Dog misclassification
- ❌ D) Image classification is a classification problem, not regression — switching model type is incorrect

---

**Exam Tip**: The confusion matrix is the FOUNDATION of all classification metrics. If you understand the four quadrants (TP, TN, FP, FN), you can derive every classification metric. Remember: **FP = Type I Error (false alarm)**, **FN = Type II Error (miss)**. The exam tests whether you know which quadrant to minimize based on business requirements — fraud detection prioritizes reducing FN (Recall), while spam filtering prioritizes reducing FP (Precision).

---

**See Also**:
- [ML Evaluation Metrics](ML-Evaluation-Metrics.md) — Full coverage of all classification and regression metrics
- [FM Evaluation Metrics](../Fundamentals/FM-Evaluation-Metrics.md) — Foundation model-specific metrics (ROUGE, BLEU, BERTScore)
- [Amazon SageMaker Clarify](../ML-Services/Amazon-SageMaker-Clarify.md) — Bias detection using confusion matrix metrics
