# BERTScore vs RMSE vs MAPE — Evaluation Metrics Comparison

## 1. Topic Overview
- **Purpose**: Understand the fundamental differences between three evaluation metrics used in completely different ML contexts — text generation (BERTScore), regression error measurement (RMSE), and percentage-based regression error (MAPE)
- **Exam Weight**: HIGH
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — RMSE, MAPE
  - Domain 3: Applications of Foundation Models (28%) — BERTScore
- **Task Statements**:
  - Task 1.3 — Describe the ML development lifecycle (model performance metrics)
  - Task 3.4 — Describe methods to evaluate foundation model performance

---

## 2. Exam Keyword Mapping

### Trigger Words
- "evaluate summarization quality" → **BERTScore**
- "semantic similarity of generated text" → **BERTScore**
- "regression prediction error" → **RMSE**
- "penalize large errors" → **RMSE**
- "percentage error across different scales" → **MAPE**
- "scale-independent error" → **MAPE**

### Scenario Indicators
- "Compare generated summary to reference summary" → **BERTScore**
- "Predict house prices, measure error in dollars" → **RMSE**
- "Compare forecast accuracy across products with different price ranges" → **MAPE**

### Anti-patterns
- Don't use BERTScore for regression tasks — it's for text generation only
- Don't use RMSE/MAPE for text generation tasks — they measure numerical error
- Don't confuse BERTScore (semantic embeddings) with BLEU/ROUGE (word overlap)

---

## 3. Core Comparison

### At a Glance

| Aspect | BERTScore | RMSE | MAPE |
|--------|-----------|------|------|
| **Full Name** | Bidirectional Encoder Representations from Transformers Score | Root Mean Squared Error | Mean Absolute Percentage Error |
| **Domain** | NLP / Generative AI (text generation) | Regression / Forecasting (numerical prediction) | Regression / Forecasting (numerical prediction) |
| **What It Measures** | Semantic similarity between generated and reference text | Magnitude of prediction errors (in original units) | Prediction errors as a percentage of actual values |
| **How It Works** | Compares contextual BERT embeddings of tokens | √(average of squared differences between actual and predicted) | Average of \|actual - predicted\| / \|actual\| × 100% |
| **Score Range** | 0 to 1 | 0 to ∞ | 0% to ∞ |
| **Good Score** | **HIGH** (closer to 1 = better) | **LOW** (closer to 0 = better) | **LOW** (closer to 0% = better) |
| **Units** | Unitless (similarity score) | Same units as target variable (e.g., dollars) | Percentage (%) |
| **Scale Dependent?** | N/A (text) | ✅ Yes — can't compare across different datasets | ❌ No — percentage-based, can compare across scales |
| **Sensitive to Outliers?** | No | ✅ Yes — squared errors amplify outliers | No — uses absolute values |
| **AWS Service** | Amazon Bedrock Model Evaluation, SageMaker FMEval | SageMaker Autopilot, Canvas, Model Monitor, Forecast | SageMaker Canvas, Amazon Forecast |

---

## 4. Deep Dive: Each Metric

### BERTScore — Semantic Text Similarity

**What makes it unique**: Uses pre-trained BERT model embeddings to capture **meaning**, not just word overlap.

```
Reference:  "The cat sat on the mat"
Generated:  "A feline rested on the rug"

ROUGE-1:    Low (few exact word matches)
BERTScore:  High (semantically similar meaning)
```

- Computes cosine similarity between contextual token embeddings
- Produces three scores: **Precision**, **Recall**, and **F1** (F1 is most commonly reported)
- Captures paraphrasing, synonyms, and meaning preservation
- Used in **Amazon Bedrock automated evaluation** for **text summarization accuracy**
- Built-in dataset: **Gigaword** (news headline summarization)

**When to use BERTScore**:
- ✅ Evaluating text summarization quality
- ✅ Measuring semantic similarity of generated text vs reference
- ✅ When different wording should still count as correct
- ❌ NOT for regression, classification, or numerical predictions

**AWS Context**:
- Amazon Bedrock uses BERTScore as the **accuracy metric for text summarization** evaluation
- Robustness for summarization = (Delta BERTScore / BERTScore) × 100
- Available in SageMaker FMEval library

> **Exam Tip**: BERTScore is the ONLY common FM metric that captures **semantic meaning** using embeddings. ROUGE and BLEU only measure word overlap.

---

### RMSE — Root Mean Squared Error

**What makes it unique**: Penalizes **large errors disproportionately** due to squaring, making it sensitive to outliers.

```
Actual:     [100, 200, 300]
Predicted:  [110, 190, 250]
Errors:     [10,  10,  50]

MAE  = (10 + 10 + 50) / 3 = 23.3
RMSE = √((100 + 100 + 2500) / 3) = √900 = 30.0
                                     ↑ RMSE > MAE because of the large error (50)
```

- **Formula**: √(Σ(actual - predicted)² / n)
- Same units as the target variable → easy to interpret
- **AWS default regression metric** (SageMaker Autopilot, Canvas)
- RMSE ≥ MAE always; large gap = outlier errors present
- Cannot compare RMSE across datasets with different scales

**When to use RMSE**:
- ✅ General-purpose regression evaluation (AWS default)
- ✅ When large errors are particularly costly (e.g., financial predictions)
- ✅ When you need error in the same units as the target
- ❌ NOT for comparing models across different scales (use MAPE or R²)
- ❌ NOT for text generation tasks

**AWS Context**:
- SageMaker Autopilot default regression metric
- SageMaker Canvas displays RMSE for numeric prediction and time-series forecasts
- SageMaker Model Monitor tracks RMSE for production model quality
- Amazon Forecast uses RMSE as one of its evaluation metrics

> **Exam Tip**: RMSE is the **go-to regression metric on AWS**. Lower = better. If RMSE >> MAE, your model has outlier prediction errors.

---

### MAPE — Mean Absolute Percentage Error

**What makes it unique**: Expresses error as a **percentage**, making it **scale-independent** and easy to communicate to business stakeholders.

```
Actual:     [100, 200, 1000]
Predicted:  [110, 190, 950]

Errors:     |100-110|/100 = 10%
            |200-190|/200 = 5%
            |1000-950|/1000 = 5%

MAPE = (10% + 5% + 5%) / 3 = 6.67%
→ "On average, predictions are off by 6.67%"
```

- **Formula**: (1/n) × Σ(|actual - predicted| / |actual|) × 100%
- Scale-independent → can compare across different datasets and products
- Easy to explain: "predictions are off by X% on average"
- ⚠️ **Undefined when actual value = 0** (division by zero)
- ⚠️ **Asymmetric**: penalizes over-predictions less than under-predictions

**When to use MAPE**:
- ✅ Comparing forecast accuracy across products with different price ranges
- ✅ Communicating model performance to non-technical stakeholders
- ✅ When relative error matters more than absolute error
- ❌ NOT when actual values can be zero or near-zero
- ❌ NOT for text generation tasks

**AWS Context**:
- SageMaker Canvas displays MAPE for numeric prediction: "prediction is +/- {MAPE}% from actual"
- Amazon Forecast uses MAPE as a time-series evaluation metric
- Useful for demand forecasting where products have vastly different volumes

> **Exam Tip**: MAPE is the metric to choose when the question mentions **"compare across different scales"** or **"percentage error"**. Watch out for the zero-value trap.

---

## 5. Exam Focus Areas

### Decision Tree: Which Metric?

```
What are you evaluating?
│
├── TEXT GENERATION (summarization, translation, Q&A)
│   ├── Need semantic similarity? → BERTScore
│   ├── Need word overlap (summarization)? → ROUGE
│   └── Need word overlap (translation)? → BLEU
│
└── NUMERICAL PREDICTION (regression, forecasting)
    ├── Need error in same units as target? → RMSE
    ├── Need to penalize large errors? → RMSE
    ├── Need scale-independent comparison? → MAPE
    ├── Need percentage-based error? → MAPE
    ├── Need variance explained? → R²
    └── Need outlier-robust error? → MAE
```

### Common Exam Traps

| Trap | Why It's Wrong | Correct Answer |
|------|---------------|----------------|
| Using RMSE to evaluate summarization | RMSE is for numerical predictions, not text | Use ROUGE or BERTScore |
| Using BERTScore for regression | BERTScore is for text similarity only | Use RMSE or MAPE |
| Using MAPE when actuals contain zeros | MAPE is undefined when actual = 0 | Use MAE or RMSE |
| Comparing RMSE across different scales | RMSE is scale-dependent | Use MAPE or R² |
| Saying BERTScore measures word overlap | BERTScore uses embeddings for semantic meaning | ROUGE/BLEU measure word overlap |
| Thinking high RMSE is good | Lower RMSE = better (unlike BERTScore where higher = better) | RMSE: LOW is good; BERTScore: HIGH is good |

### Score Direction Cheat Sheet

| Metric | ↑ Higher = Better | ↓ Lower = Better |
|--------|-------------------|-------------------|
| **BERTScore** | ✅ | |
| **RMSE** | | ✅ |
| **MAPE** | | ✅ |
| **ROUGE** | ✅ | |
| **BLEU** | ✅ | |
| **R²** | ✅ | |
| **MAE** | | ✅ |
| **Toxicity** | | ✅ |
| **Robustness (Bedrock)** | | ✅ |

---

## 6. Sample Questions

### Question 1
**A company uses a foundation model to generate text summaries. They want a metric that evaluates whether the generated summary preserves the meaning of the original text, even if different words are used. Which metric should they use?**

A) RMSE
B) MAPE
C) ROUGE-1
D) BERTScore

**Correct Answer: D) BERTScore**

- ✅ **D) BERTScore** — Uses contextual BERT embeddings to measure semantic similarity, capturing meaning even with different wording (paraphrasing)
- ❌ A) RMSE — Measures numerical prediction error for regression tasks, not text quality
- ❌ B) MAPE — Measures percentage-based numerical prediction error, not text quality
- ❌ C) ROUGE-1 — Measures unigram word overlap only; doesn't capture semantic meaning when different words are used

---

### Question 2
**A retail company predicts demand for 500 products ranging from $1 items to $10,000 items. They need a single metric to compare forecast accuracy across all products fairly. Which metric is MOST appropriate?**

A) RMSE
B) MAE
C) MAPE
D) BERTScore

**Correct Answer: C) MAPE**

- ✅ **C) MAPE** — Scale-independent percentage-based metric; a 10% error is equally meaningful whether the product costs $1 or $10,000
- ❌ A) RMSE — Scale-dependent; an RMSE of $50 means very different things for a $1 item vs a $10,000 item
- ❌ B) MAE — Also scale-dependent; same problem as RMSE
- ❌ D) BERTScore — Used for text generation evaluation, not numerical forecasting

---

### Question 3
**A data scientist is evaluating a regression model that predicts housing prices. They want a metric that penalizes large prediction errors more heavily and reports the error in the same units as the target (dollars). Which metric should they choose?**

A) MAPE
B) R²
C) RMSE
D) BERTScore

**Correct Answer: C) RMSE**

- ✅ **C) RMSE** — Reports error in the same units as the target (dollars), and the squaring operation penalizes large errors disproportionately
- ❌ A) MAPE — Reports error as a percentage, not in dollars; doesn't penalize large errors more
- ❌ B) R² — Measures proportion of variance explained (unitless ratio), not error in dollars
- ❌ D) BERTScore — Used for text generation evaluation, not regression

---

**Exam Tip**: The AIF-C01 exam tests whether you can match the right metric to the right task type. Remember: **BERTScore = text (semantic)**, **RMSE = numbers (penalizes outliers)**, **MAPE = numbers (percentage, scale-free)**. These three metrics are NEVER interchangeable.

---

## Citations
- [SageMaker Canvas Metrics Reference — Numeric Prediction & Time Series](https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-metrics.html)
- [Amazon Bedrock Model Evaluation — Built-in Metrics](https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation-built-in-metrics.html)
- [Generative AI Lens — Collect Performance Metrics](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/genperf01-bp02.html)
- [AWS Well-Architected ML Lens — Define Evaluation Metrics](https://docs.aws.amazon.com/wellarchitected/latest/machine-learning-lens/mlperf02-bp01.html)

---

**See Also**:
- [FM Evaluation Metrics](FM-Evaluation-Metrics.md) — Full coverage of ROUGE, BLEU, BERTScore, METEOR, and Bedrock evaluation
- [ML Evaluation Metrics](../ML-Fundamentals/ML-Evaluation-Metrics.md) — Full coverage of classification and regression metrics
- [ML Regression](../ML-Fundamentals/ML-Regression.md) — Regression concepts, algorithms, and metrics deep dive
