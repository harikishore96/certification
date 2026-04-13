# ML Regression

## 1. Topic Overview
- **Purpose**: Regression is a supervised learning technique that predicts continuous numerical values from input features
- **Category**: ML Fundamentals — Supervised Learning
- **Key Use Cases**:
  - Predicting housing prices, stock prices, revenue forecasting
  - Estimating customer lifetime value
  - Demand forecasting and inventory planning
  - Predicting temperature, energy consumption, or any continuous variable
  - Healthcare cost estimation
- **Exam Weight**: High
- **Exam Domain**: Domain 1: Fundamentals of AI and ML (20%)
- **Task Statements**: Task 1.1 — Explain basic AI concepts and terminologies; Task 1.2 — Identify practical use cases for AI/ML

## 2. Exam Keyword Mapping
- **Trigger Words**: "predict a number", "continuous value", "estimate", "forecast", "how much", "how many", "numerical output", "price prediction", "demand prediction"
- **Scenario Indicators**:
  - "Predict the selling price of a house" → Regression
  - "Estimate the number of units to be sold" → Regression
  - "Forecast next quarter's revenue" → Regression
  - "Predict customer spend amount" → Regression
- **Anti-patterns**:
  - Predicting categories (yes/no, spam/not spam) → Classification, NOT regression
  - Grouping similar items → Clustering (unsupervised), NOT regression
  - Predicting a label or class → Classification
  - Time-series with seasonality patterns → May use specialized forecasting (DeepAR, Amazon Forecast) rather than basic regression

## 3. Core Concepts

### What is Regression?
- A **supervised learning** method where the model learns a mapping from input features (X) to a **continuous numerical target** (Y)
- The model learns from labeled training data (input-output pairs) and generalizes to unseen data
- Output is always a **real number** (not a class or category)

### Regression vs Classification

| Aspect | Regression | Classification |
|--------|-----------|----------------|
| **Output** | Continuous number (e.g., 254,013) | Discrete class/label (e.g., "spam") |
| **Target** | Real-valued (price, temperature) | Categorical (yes/no, A/B/C) |
| **Loss Functions** | MSE, MAE, RMSE | Cross-entropy, Hinge loss |
| **Metrics** | RMSE, MAE, R², MAPE | Accuracy, Precision, Recall, F1, AUC-ROC |
| **Example** | "Predict house price: $350,000" | "Is this email spam? Yes" |

### Types of Regression

| Type | Description | Use Case |
|------|-------------|----------|
| **Linear Regression** | Models linear relationship between features and target (y = wx + b) | Simple relationships, baseline models |
| **Polynomial Regression** | Extends linear regression with polynomial terms | Non-linear relationships |
| **Ridge Regression (L2)** | Linear regression + L2 regularization penalty | Prevents overfitting, many features |
| **Lasso Regression (L1)** | Linear regression + L1 regularization penalty | Feature selection, sparse models |
| **Elastic Net** | Combines L1 + L2 regularization | Best of both Ridge and Lasso |
| **Decision Tree Regression** | Tree-based splits to predict values | Non-linear, interpretable models |
| **Gradient Boosted Trees (XGBoost)** | Ensemble of weak learners (trees) combined iteratively | High accuracy on tabular data |

### Data Flow
```
Input Features (X)     →  Regression Model  →  Continuous Prediction (ŷ)
[sq_ft, bedrooms,          [trained weights      [predicted price:
 location, age]             and biases]            $254,013]
```

### Key Terminology
- **Features (X)**: Input variables used for prediction (independent variables)
- **Target (Y)**: The continuous value being predicted (dependent variable)
- **Prediction (ŷ)**: The model's estimated output
- **Residual**: Difference between actual and predicted value (y - ŷ)
- **Coefficients/Weights**: Learned parameters that define the relationship
- **Intercept/Bias**: The baseline value when all features are zero
- **Loss Function**: Mathematical function measuring prediction error (e.g., MSE)
- **Regularization**: Technique to prevent overfitting (L1/Lasso, L2/Ridge)

## 4. Key Features & Components

### Regression Evaluation Metrics (⭐ HIGH EXAM RELEVANCE)

| Metric | Formula | Interpretation | When to Use |
|--------|---------|----------------|-------------|
| **MAE** (Mean Absolute Error) | avg(\|y - ŷ\|) | Average magnitude of errors; treats all errors equally | When all errors matter equally; robust to outliers |
| **MSE** (Mean Squared Error) | avg((y - ŷ)²) | Average squared errors; penalizes large errors more | When large errors are particularly undesirable |
| **RMSE** (Root Mean Squared Error) | √MSE | Same units as target; penalizes large errors | **Most common metric**; AWS default for regression |
| **MAPE** (Mean Absolute Percentage Error) | avg(\|y - ŷ\| / \|y\|) × 100 | Error as percentage; scale-independent | Comparing across different scales |
| **R²** (R-Squared / Coefficient of Determination) | 1 - (SS_res / SS_tot) | Proportion of variance explained (0 to 1) | Understanding model fit quality |

#### Metric Deep Dive

**RMSE (Root Mean Squared Error)** — AWS Primary Metric
- Industry standard for regression evaluation
- **Lower RMSE = better model** (perfect model has RMSE = 0)
- Same units as the target variable (e.g., dollars for price prediction)
- Penalizes large errors more heavily than MAE
- AWS SageMaker uses RMSE as the default regression metric
- **Baseline RMSE**: RMSE of a model that always predicts the mean — your model must beat this

**MAE (Mean Absolute Error)**
- Simpler to interpret: "on average, predictions are off by X units"
- More robust to outliers than RMSE
- Does not penalize large errors disproportionately

**MSE (Mean Squared Error)**
- Squared units make it harder to interpret directly
- Commonly used as the **loss function** during training (not just evaluation)
- Differentiable everywhere — good for gradient-based optimization

**MAPE (Mean Absolute Percentage Error)**
- Expressed as a percentage — easy to communicate to stakeholders
- Scale-independent: can compare models across different datasets
- ⚠️ Undefined when actual value is zero

**R² (R-Squared)**
- Ranges from 0 to 1 (can be negative for very poor models)
- R² = 1.0 → perfect fit; R² = 0.0 → model is no better than predicting the mean
- **Does NOT indicate if the model is biased** — only measures variance explained
- Adding more features always increases R² (use Adjusted R² to account for this)

### Residual Analysis
- **Residual** = Actual Value - Predicted Value (y - ŷ)
- Positive residual → model **underestimates** (actual > predicted)
- Negative residual → model **overestimates** (actual < predicted)
- **Ideal**: Residuals form a **zero-centered bell curve** (random errors, no systematic bias)
- **Problem**: Non-zero-centered residuals indicate the model is missing patterns → add more features or use a more complex model

### AWS Services for Regression

| Service | Regression Capability | Expertise Level |
|---------|----------------------|-----------------|
| **SageMaker Linear Learner** | Built-in linear/logistic regression algorithm | Medium |
| **SageMaker XGBoost** | Gradient boosted trees for regression | Medium |
| **SageMaker Autopilot** | AutoML — automatically selects best regression model | Low |
| **SageMaker Canvas** | No-code regression model building | None |
| **SageMaker JumpStart** | Pre-trained models and regression templates | Low-Medium |
| **Amazon Forecast** | Specialized time-series regression/forecasting | Low |
| **Amazon Redshift ML** | SQL-based regression using SageMaker under the hood | Low |

### SageMaker Linear Learner for Regression
- Set `predictor_type='regressor'` for regression tasks
- Supports continuous objectives: **MSE, absolute error, cross-entropy loss**
- Input formats: CSV, RecordIO-wrapped Protobuf
- Three data channels: **train** (required), **validation** (optional), **test** (optional)
- Supports CPU and GPU instances (P2, P3, G4dn, G5)
- Output `score` is the predicted numerical value
- Supports **Managed Spot Training** with checkpoints for cost savings

### SageMaker XGBoost for Regression
- Implements **gradient boosted trees** — ensemble of weak regression trees
- Each new tree predicts the **residuals** (errors) of previous trees
- Uses **regularized objective function** = loss function + penalty for model complexity
- Iterative training: gradient descent minimizes loss when adding new models
- Excellent for **tabular data** with mixed feature types
- Supports both regression and classification

### SageMaker Autopilot Regression Metrics
Autopilot automatically evaluates regression candidates using:
- **MAE**, **MSE**, **RMSE**, **R²**
- Uses **k-fold cross-validation** to reduce overfitting
- Ensembling mode: stacks multiple models for optimal performance

## 5. Exam Focus Areas

### Common Question Types
- **"Which metric should you use?"** → Know when to pick RMSE vs MAE vs R² vs MAPE
- **"What type of ML problem is this?"** → Continuous output = Regression
- **"Which algorithm for regression?"** → Linear Learner (simple), XGBoost (complex/tabular)
- **"How to evaluate a regression model?"** → RMSE (default), MAE, R²
- **"What does a low/high RMSE mean?"** → Low = better predictions

### Gotchas ⚠️
- **Regression output can exceed training data range** — predictions are NOT bounded to the min/max of training labels
- **R² can be negative** — when the model is worse than simply predicting the mean
- **RMSE ≠ MAE** — RMSE penalizes large errors more; if outliers matter, use RMSE
- **MSE is in squared units** — not directly interpretable; use RMSE instead
- **MAPE fails when actual = 0** — division by zero
- **Adding features always increases R²** — use Adjusted R² for fair comparison
- **Regression ≠ Forecasting** — time-series forecasting is a specialized form; Amazon Forecast or DeepAR may be better than generic regression for temporal data
- **Linear Learner can do BOTH regression and classification** — set `predictor_type` accordingly

### Comparison Points

| Scenario | Choose This |
|----------|-------------|
| Simple linear relationship | **Linear Learner** |
| Complex tabular data, non-linear | **XGBoost** |
| No-code, business analyst | **SageMaker Canvas** |
| AutoML, best model automatically | **SageMaker Autopilot** |
| Time-series forecasting | **Amazon Forecast** or **DeepAR** |
| SQL-based predictions | **Amazon Redshift ML** |

### Decision Tree: Regression Metric Selection
```
What matters most?
├── All errors equally important? → MAE
├── Large errors are costly? → RMSE (or MSE)
├── Need percentage-based error? → MAPE
├── Need to understand variance explained? → R²
└── AWS default / general purpose? → RMSE ✅
```

## 6. Best Practices

### Data Preparation
- **Handle missing values** — impute or remove before training
- **Feature scaling** — normalize/standardize features for linear models (less critical for tree-based)
- **Outlier treatment** — outliers heavily impact MSE/RMSE; consider robust methods or MAE
- **Feature engineering** — create meaningful derived features to improve model performance
- **Train/validation/test split** — always hold out data for unbiased evaluation

### Model Selection
- Start with **Linear Learner** as a baseline (fast, interpretable)
- Move to **XGBoost** for better accuracy on complex data
- Use **Autopilot** when unsure — it tries multiple algorithms automatically
- Monitor **residuals** — non-random patterns indicate model deficiency

### Security
- Encrypt training data at rest (S3 SSE, KMS) and in transit (TLS)
- Use VPC endpoints for SageMaker to keep traffic private
- IAM roles with least-privilege access for training jobs

### Cost Optimization
- Use **Managed Spot Training** (up to 90% savings) for Linear Learner and XGBoost
- Right-size instances — start with smaller instances and scale up
- Use **SageMaker Autopilot** to avoid manual hyperparameter tuning costs
- Stop training early if validation loss plateaus (early stopping)

### Performance
- **Regularization** (L1/L2) to prevent overfitting
- **Cross-validation** to get reliable performance estimates
- **Hyperparameter tuning** — learning rate, number of rounds (XGBoost), regularization strength
- Monitor for **data drift** — use SageMaker Model Monitor to detect when input data distribution changes

## 7. Hands-On Labs

### Quick Setup: Linear Learner Regression
```python
import sagemaker
from sagemaker import LinearLearner

# Create estimator
linear = LinearLearner(
    role=sagemaker.get_execution_role(),
    instance_count=1,
    instance_type='ml.m5.large',
    predictor_type='regressor',  # Key: set to regressor
    num_models=32,               # Explore 32 models in parallel
    loss='squared_loss'          # MSE loss for regression
)

# Set training data
linear.fit({'train': s3_train_data, 'validation': s3_val_data})
```

### Quick Setup: XGBoost Regression
```python
from sagemaker.xgboost import XGBoost

xgb = XGBoost(
    role=sagemaker.get_execution_role(),
    instance_count=1,
    instance_type='ml.m5.large',
    framework_version='1.7-1',
    hyperparameters={
        'objective': 'reg:squarederror',  # Regression objective
        'num_round': 100,
        'max_depth': 5,
        'eta': 0.2
    }
)

xgb.fit({'train': s3_train_data, 'validation': s3_val_data})
```

### CLI: Create Autopilot Regression Job
```bash
aws sagemaker create-auto-ml-job \
    --auto-ml-job-name housing-price-regression \
    --input-data-config '[{
        "DataSource": {"S3DataSource": {"S3Uri": "s3://bucket/train.csv"}},
        "TargetAttributeName": "price"
    }]' \
    --output-data-config '{"S3OutputPath": "s3://bucket/output/"}' \
    --problem-type Regression \
    --auto-ml-job-objective '{"MetricName": "RMSE"}' \
    --role-arn arn:aws:iam::123456789012:role/SageMakerRole
```

## 8. Sample Questions

### Question 1
A retail company wants to predict the **dollar amount** each customer will spend in the next quarter based on their purchase history, demographics, and browsing behavior. Which type of ML problem is this?

A. Binary classification
B. Multi-class classification
C. Regression
D. Clustering

**Correct Answer: C**
- The target is a **continuous numerical value** (dollar amount) → Regression
- A: Binary classification predicts two classes (yes/no), not a number
- B: Multi-class classification predicts one of several categories, not a continuous value
- D: Clustering groups similar items without labels (unsupervised), not predicting a value

---

### Question 2
A data scientist trained a regression model to predict housing prices. The model has an RMSE of 45,000 and an R² of 0.85. The baseline RMSE (predicting the mean) is 120,000. Which statement is correct?

A. The model is performing poorly because RMSE is high
B. The model explains 85% of the variance and significantly outperforms the baseline
C. The R² of 0.85 means the model is 85% accurate
D. The model should be discarded because RMSE should be close to zero

**Correct Answer: B**
- R² = 0.85 means 85% of the **variance** in housing prices is explained by the model
- RMSE of 45,000 is much lower than the baseline of 120,000 → model is useful
- A: RMSE must be compared to the baseline and the scale of the target, not judged in isolation
- C: R² measures variance explained, NOT accuracy percentage
- D: RMSE = 0 is only for a perfect model; 45,000 vs 120,000 baseline shows strong improvement

---

### Question 3
A team needs to build a regression model on tabular data with mixed feature types (numerical and categorical). They want the highest accuracy possible without manual feature engineering. Which SageMaker approach is MOST appropriate?

A. SageMaker Linear Learner with default settings
B. SageMaker XGBoost algorithm
C. SageMaker Autopilot
D. Amazon Comprehend

**Correct Answer: C**
- **SageMaker Autopilot** automatically tries multiple algorithms (including XGBoost and Linear Learner), performs feature engineering, and selects the best model
- A: Linear Learner is a good baseline but may not achieve highest accuracy on complex data
- B: XGBoost is powerful but requires manual hyperparameter tuning for optimal results
- D: Amazon Comprehend is an NLP service, not for tabular regression

---

**Exam Tip**: When you see "predict a number" or "continuous value" in a question, it's regression. When you see "predict a category" or "classify", it's classification. For metric questions, remember RMSE is the AWS default for regression, and lower is always better.
