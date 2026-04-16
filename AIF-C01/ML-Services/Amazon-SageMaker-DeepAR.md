# Amazon SageMaker DeepAR

## 1. Service/Topic Overview
- **Purpose**: A supervised learning algorithm for forecasting scalar (one-dimensional) time series using recurrent neural networks (RNN), training a single model jointly over many related time series
- **Service Level**: Regional (runs within SageMaker)
- **Key Use Cases**:
  - Demand forecasting across hundreds of products
  - Server load and web traffic prediction
  - Energy consumption forecasting
  - Financial time-series forecasting
  - Supply chain and inventory planning
- **Exam Weight**: Medium
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — ML algorithms and use cases
  - Domain 3: Applications of Foundation Models (28%) — model selection and deployment
- **Task Statements**: Task 1.2 (Practical Use Cases for AI), Task 1.3 (ML Development Lifecycle)

## 2. Exam Keyword Mapping
- **Trigger Words**: "time series", "forecasting", "demand prediction", "probabilistic forecast", "multiple related time series", "seasonal patterns", "autoregressive", "RNN forecasting"
- **Scenario Indicators**:
  - "Hundreds of related time series need forecasting" → DeepAR
  - "Need probabilistic forecasts (quantiles, confidence intervals)" → DeepAR
  - "Forecast demand for many products simultaneously" → DeepAR
  - "What-if scenario analysis with dynamic features" → DeepAR
- **Anti-patterns**:
  - Single time series with few data points → Use ARIMA or ETS instead
  - Non-time-series tabular data → Use XGBoost or Linear Learner
  - Pre-built forecasting without ML expertise → Use Amazon Forecast
  - Need a fully managed no-code forecasting service → Amazon Forecast

## 3. Core Concepts

### How DeepAR Works
- Trains **autoregressive recurrent neural networks** on multiple related time series simultaneously
- Learns a **global model** across all time series (vs. classical methods that fit one model per series)
- Outputs **probabilistic forecasts** — full probability distributions, not just point estimates
- Uses **context window** (past data) to predict a **prediction window** (future values)

### Architecture Pattern
```
Multiple Time Series (JSON Lines / Parquet)
    ↓
[Training] context_length + prediction_length windows sampled randomly
    ↓
RNN (LSTM) with lagged features + derived time features
    ↓
Probabilistic Forecast (quantiles, samples)
```

### Key Differentiator vs. Classical Methods
| Aspect | DeepAR | ARIMA / ETS |
|--------|--------|-------------|
| Model scope | Single model for ALL time series | One model per time series |
| Output | Probabilistic distribution | Point forecast (or simple intervals) |
| Cross-learning | Learns patterns across series | No cross-series learning |
| Sweet spot | Hundreds of related series | Single or few series |
| Missing values | Supported in target | Limited support |

### Data Flow
```
Input (S3):
  JSON Lines or Parquet files
  Fields: start, target, cat (optional), dynamic_feat (optional)
    ↓
Training:
  Random sampling of context + prediction windows
  Auto-derived time features (hour-of-day, day-of-week, etc.)
  Lagged values for seasonality capture
    ↓
Output:
  Probabilistic forecast for next prediction_length steps
  Quantiles, mean, samples from the distribution
```

### Input Data Format (JSON Lines)
```json
{"start": "2024-01-01 00:00:00", "target": [4.3, "NaN", 5.1, ...], "cat": [0, 1], "dynamic_feat": [[1.1, 1.2, 0.5, ...]]}
{"start": "2024-03-15 00:00:00", "target": [1.0, -5.0, ...], "cat": [2, 3], "dynamic_feat": [[1.1, 2.05, ...]]}
```

### Input Fields
| Field | Required | Description |
|-------|----------|-------------|
| `start` | ✅ Yes | Start timestamp (`YYYY-MM-DD HH:MM:SS`, no timezone) |
| `target` | ✅ Yes | Array of values (supports `null` / `NaN` for missing) |
| `cat` | ❌ Optional | Categorical features (0-based integers) for grouping |
| `dynamic_feat` | ❌ Optional | Dynamic features (must be known in the future for what-if scenarios) |

### Auto-Derived Time Features
| Time Series Frequency | Derived Features |
|----------------------|------------------|
| Minute | minute-of-hour, hour-of-day, day-of-week, day-of-month, day-of-year |
| Hour | hour-of-day, day-of-week, day-of-month, day-of-year |
| Day | day-of-week, day-of-month, day-of-year |
| Week | day-of-month, week-of-year |
| Month | month-of-year |

### Integration Points
- **Amazon S3**: Input data storage (JSON Lines, Parquet, gzip)
- **SageMaker Training Jobs**: Model training on CPU or GPU instances
- **SageMaker Endpoints**: Real-time inference (CPU only)
- **SageMaker Autopilot**: DeepAR+ is one of 6 algorithms used in time-series forecasting mode
- **Amazon Forecast**: Managed service that uses DeepAR+ internally

## 4. Key Features & Components

### Exam-Tested Features
- **Probabilistic Forecasting**: Outputs full probability distributions, not just point estimates
- **Joint Training**: Single model trained across all related time series
- **Missing Value Support**: Target time series can contain `null` / `NaN`
- **Categorical Features (`cat`)**: Group time series by category (product type, region, etc.)
- **Dynamic Features (`dynamic_feat`)**: Time-varying features known in the future (promotions, holidays, price changes)
- **What-If Scenarios**: Change dynamic features to see impact on forecast
- **Automatic Lag Features**: Auto-generates lagged values to capture seasonality
- **Auto-Derived Time Features**: Automatically creates hour-of-day, day-of-week, etc.

### Required Hyperparameters
| Parameter | Description | Exam Tip |
|-----------|-------------|----------|
| `context_length` | How far back the model looks (positive integer) | Set ≈ `prediction_length`; lags let model see further back |
| `prediction_length` | Forecast horizon — fixed at training time, cannot change later | Keep ≤ 400; aggregate data for longer horizons |
| `epochs` | Number of training passes (10–1000 typical) | Use with `early_stopping_patience` |
| `time_freq` | Granularity: `M`, `W`, `D`, `H`, `min` (or multiples like `5min`) | Drives which auto-derived features are created |

### Key Optional Hyperparameters
| Parameter | Default | Description |
|-----------|---------|-------------|
| `likelihood` | `student-T` | Noise model: `gaussian`, `beta`, `negative-binomial`, `student-T`, `deterministic-L1` |
| `num_cells` | 40 | RNN hidden layer size (30–100 typical) |
| `num_layers` | 2 | RNN depth (1–4 typical) |
| `learning_rate` | 1e-3 | Training learning rate (1e-4 to 1e-1) |
| `dropout_rate` | 0.1 | Zoneout regularization (< 0.2 typical) |
| `mini_batch_size` | 128 | Batch size (32–512 typical) |
| `embedding_dimension` | 10 | Size of categorical embedding vectors (10–100) |
| `early_stopping_patience` | — | Stop training after N epochs with no improvement |

### Likelihood Selection Guide
| Likelihood | Use When |
|------------|----------|
| `gaussian` | Real-valued continuous data |
| `beta` | Values between 0 and 1 (proportions, percentages) |
| `negative-binomial` | Count data (non-negative integers) |
| `student-T` | Real-valued data with bursty/heavy-tailed patterns (default) |
| `deterministic-L1` | Only need point forecast, no uncertainty |

### Evaluation Metrics
- **RMSE** (Root Mean Square Error): Calculated over test set's last `prediction_length` points
- **Weighted Quantile Loss**: Evaluates accuracy of the forecast distribution at specified quantiles
- **test_quantiles**: Default `[0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9]`

## 5. Exam Focus Areas

### Common Question Types
- **Scenario-based**: "A company has 500 product demand time series and needs probabilistic forecasts" → DeepAR
- **Feature comparison**: DeepAR vs. Amazon Forecast vs. ARIMA
- **Best practice**: How to prepare training/test data for DeepAR
- **Hyperparameter**: What does `prediction_length` control?

### Gotchas
- ⚠️ `prediction_length` is **fixed at training time** — you cannot change it after training
- ⚠️ Keep `prediction_length` ≤ 400 — larger values make model slow and less accurate
- ⚠️ **Inference supports CPU only** — no GPU for inference
- ⚠️ Always provide the **entire time series** (don't truncate) — model uses lags beyond `context_length`
- ⚠️ `dynamic_feat` must be known for the **future** prediction period too
- ⚠️ All `cat` values must appear in training data — can't predict for unseen categories
- ⚠️ Minimum **300 total observations** across all training time series
- ⚠️ If trained with `cat` or `dynamic_feat`, must provide them at inference

### Comparison Points

#### DeepAR vs. Amazon Forecast
| Aspect | DeepAR (SageMaker) | Amazon Forecast |
|--------|-------------------|-----------------|
| Expertise needed | ML knowledge required | No ML expertise needed |
| Customization | Full control over hyperparameters | Automated (uses DeepAR+ internally) |
| Infrastructure | Manage instances yourself | Fully managed |
| Algorithms | DeepAR only | 6 algorithms + ensemble (CNN-QR, DeepAR+, Prophet, NPTS, ARIMA, ETS) |
| Best for | ML practitioners needing fine control | Business users / quick deployment |

#### DeepAR vs. Classical Methods (ARIMA/ETS)
| Aspect | DeepAR | ARIMA / ETS |
|--------|--------|-------------|
| Best when | Hundreds of related time series | Single or few time series |
| Output | Probabilistic distribution | Point forecast |
| Cross-learning | ✅ Yes | ❌ No |
| Cold start | Can forecast new similar series | Cannot |

### Decision Tree
- ✅ **Use DeepAR when**:
  - You have hundreds of related time series
  - You need probabilistic forecasts (quantiles, confidence intervals)
  - You want to run what-if scenarios with dynamic features
  - You need to forecast for new time series similar to training data
  - You have ML expertise and want full hyperparameter control
- ❌ **Don't use DeepAR when**:
  - You have a single time series → ARIMA / ETS
  - You want a fully managed no-code solution → Amazon Forecast
  - Your data is not time series → XGBoost, Linear Learner
  - You need forecasts longer than 400 steps → aggregate to lower frequency first

## 6. Best Practices

### Data Preparation
- **Always provide entire time series** — don't break up or truncate (lags need historical data)
- **Shuffle time series** in the training file (random order)
- **Set `start` field correctly** — algorithm derives internal time features from it
- **Training/test split**: Use full data as test set; remove last `prediction_length` points for training set
- For long forecast horizons, **aggregate to lower frequency** (e.g., `5min` instead of `1min`)

### Security
- Data encrypted at rest in S3 (SSE-S3, SSE-KMS)
- VPC configuration for network isolation
- IAM roles for SageMaker training job access
- CloudTrail for API audit logging

### Cost Optimization
- Start with **CPU instances** (ml.c4.2xlarge or ml.c4.4xlarge)
- Use GPU only for large models (many cells/layers) with large batch sizes (>512)
- Use `early_stopping_patience` to avoid unnecessary training epochs
- Use **Managed Spot Training** for up to 90% cost savings

### Performance
- Start `context_length` ≈ `prediction_length` (lags handle longer lookback)
- Keep `prediction_length` ≤ 400
- More time series = better model (hundreds recommended)
- Minimum 300 total observations across all series

### Instance Recommendations
| Phase | Recommended | Notes |
|-------|-------------|-------|
| Training (start) | ml.c4.2xlarge / ml.c4.4xlarge (CPU) | Default starting point |
| Training (large) | GPU instances, multi-machine | Only for large models + batch size > 512 |
| Inference | CPU instances only | GPU not supported for inference |

## 7. Hands-On Labs

### Quick Setup (Python SDK)
```python
import sagemaker
from sagemaker import image_uris

# Get DeepAR container image
image_uri = image_uris.retrieve("forecasting-deepar", region="us-east-1")

# Configure estimator
estimator = sagemaker.estimator.Estimator(
    image_uri=image_uri,
    role="<sagemaker-execution-role>",
    instance_count=1,
    instance_type="ml.c4.2xlarge",
    output_path="s3://<bucket>/deepar-output"
)

# Set hyperparameters
estimator.set_hyperparameters(
    time_freq="D",
    context_length=30,
    prediction_length=30,
    epochs=100,
    early_stopping_patience=10,
    likelihood="student-T",
    num_cells=40,
    num_layers=2
)

# Train
estimator.fit({"train": "s3://<bucket>/train/", "test": "s3://<bucket>/test/"})
```

### CLI — Create Training Job
```bash
aws sagemaker create-training-job \
    --training-job-name deepar-demo \
    --algorithm-specification TrainingImage=<deepar-image-uri>,TrainingInputMode=File \
    --role-arn arn:aws:iam::<account>:role/<role> \
    --input-data-config '[{"ChannelName":"train","DataSource":{"S3DataSource":{"S3Uri":"s3://<bucket>/train/"}}}]' \
    --output-data-config S3OutputPath=s3://<bucket>/output/ \
    --resource-config InstanceType=ml.c4.2xlarge,InstanceCount=1,VolumeSizeInGB=10 \
    --hyper-parameters '{"time_freq":"D","context_length":"30","prediction_length":"30","epochs":"100"}'
```

## 8. Sample Questions

### Question 1
A retail company has demand data for 1,000 products over the past 3 years and wants to forecast demand for the next 30 days with confidence intervals. Which SageMaker algorithm should they use?

**A)** XGBoost  
**B)** Linear Learner  
**C)** DeepAR  
**D)** Random Cut Forest  

**Correct Answer: C**

**Explanation**: DeepAR is designed for forecasting multiple related time series simultaneously and provides probabilistic forecasts (confidence intervals). With 1,000 related product time series, DeepAR will outperform classical methods by learning cross-series patterns. XGBoost and Linear Learner are not time-series forecasting algorithms. Random Cut Forest is for anomaly detection, not forecasting.

---

### Question 2
A data scientist is training a DeepAR model and wants to forecast 60 days into the future. After training, the business asks for 90-day forecasts. What should the data scientist do?

**A)** Change the `prediction_length` parameter on the deployed endpoint  
**B)** Retrain the model with `prediction_length` set to 90  
**C)** Set `context_length` to 90 and re-run inference  
**D)** Use a longer input time series at inference time  

**Correct Answer: B**

**Explanation**: The `prediction_length` is fixed at training time and cannot be changed after the model is trained. The only way to get 90-day forecasts is to retrain the model with `prediction_length=90`. Changing context_length or input length does not affect the forecast horizon.

---

### Question 3
A company wants to forecast product demand and run "what-if" scenarios to see how promotions affect sales. Which DeepAR feature supports this?

**A)** `cat` — categorical features  
**B)** `dynamic_feat` — dynamic features  
**C)** `context_length` hyperparameter  
**D)** `likelihood` hyperparameter  

**Correct Answer: B**

**Explanation**: `dynamic_feat` allows you to provide time-varying features that are known in the future (like planned promotions). By changing these values, you can run what-if scenarios. `cat` is for static grouping (product type, region). `context_length` controls how far back the model looks. `likelihood` controls the noise model for uncertainty estimation.

---

> **Exam Tip**: DeepAR's key differentiator is training a **single model across many related time series** with **probabilistic output**. If the question mentions "hundreds of time series" + "forecasting" + "confidence intervals" → DeepAR. If it says "fully managed forecasting" → Amazon Forecast.

---

**Sources**:
- [SageMaker DeepAR Algorithm](https://docs.aws.amazon.com/sagemaker/latest/dg/deepar.html)
- [How DeepAR Works](https://docs.aws.amazon.com/sagemaker/latest/dg/deepar_how-it-works.html)
- [DeepAR Hyperparameters](https://docs.aws.amazon.com/sagemaker/latest/dg/deepar_hyperparameters.html)
