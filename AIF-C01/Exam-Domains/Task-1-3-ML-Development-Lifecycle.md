# Task 1.3: ML Development Lifecycle

## Task Overview
- **Domain**: Domain 1 - Fundamentals of AI and ML
- **Weight**: Medium-High
- **Exam Focus**: Understanding the complete end-to-end ML workflow from data to production

## Learning Objectives
- Understand each phase of the ML lifecycle
- Identify activities in each phase
- Recognize AWS services for each stage
- Understand iterative nature of ML development
- Know when to retrain and update models

## ML Development Lifecycle Phases

```
1. Business Problem Definition
   ↓
2. Data Collection & Preparation
   ↓
3. Feature Engineering
   ↓
4. Model Training
   ↓
5. Model Evaluation
   ↓
6. Model Deployment
   ↓
7. Monitoring & Maintenance
   ↓
8. Iteration (back to step 2 or 3)
```

---

## Phase 1: Business Problem Definition

### Objective
Define the business problem and determine if ML is appropriate.

### Key Activities
- **Define Business Goal**: What problem are we solving?
- **Define Success Metrics**: How do we measure success?
- **Assess ML Suitability**: Is ML the right approach?
- **Identify Constraints**: Budget, time, resources, compliance

### Questions to Answer
- What decision needs to be made?
- What data is available?
- What is the expected ROI?
- Are there regulatory requirements?
- What is acceptable accuracy?

### ML Problem Types
- **Classification**: Categorize into classes (spam/not spam)
- **Regression**: Predict numerical values (price, demand)
- **Clustering**: Group similar items (customer segments)
- **Anomaly Detection**: Find outliers (fraud, defects)
- **Forecasting**: Predict future values (sales, demand)
- **Recommendation**: Suggest items (products, content)

### AWS Services
- Amazon QuickSight (business analytics)
- AWS Well-Architected Tool (architecture review)

### Example
```
Business Problem: Reduce customer churn
ML Problem Type: Binary classification (churn/no churn)
Success Metric: Reduce churn by 20%
Data Available: Customer demographics, usage history, support tickets
Constraints: Must explain predictions (regulated industry)
```

---

## Phase 2: Data Collection & Preparation

### Objective
Gather, clean, and prepare data for model training.

### Key Activities

**2.1 Data Collection**
- Identify data sources
- Extract data from systems
- Combine multiple sources
- Store in centralized location

**Data Sources**:
- Databases (RDS, DynamoDB)
- Data warehouses (Redshift)
- Data lakes (S3)
- Streaming data (Kinesis)
- APIs and external sources
- Log files (CloudWatch)

**2.2 Data Exploration (EDA)**
- Understand data structure
- Identify patterns and relationships
- Detect anomalies
- Visualize distributions

**2.3 Data Cleaning**
- **Handle Missing Values**:
  - Remove rows/columns
  - Imputation (mean, median, mode)
  - Forward/backward fill (time-series)
  
- **Handle Outliers**:
  - Remove extreme values
  - Cap/floor values
  - Transform (log, sqrt)
  
- **Fix Inconsistencies**:
  - Standardize formats
  - Correct errors
  - Resolve duplicates

**2.4 Data Transformation**
- **Normalization**: Scale to [0, 1]
- **Standardization**: Mean=0, StdDev=1
- **Encoding Categorical Variables**:
  - One-hot encoding (nominal)
  - Label encoding (ordinal)
  - Target encoding
  
- **Handle Imbalanced Data**:
  - Oversampling minority class (SMOTE)
  - Undersampling majority class
  - Class weights

**2.5 Data Splitting**
- **Training Set** (70-80%): Train model
- **Validation Set** (10-15%): Tune hyperparameters
- **Test Set** (10-15%): Final evaluation

**Important**: 
- Time-series: Use time-based splits (no shuffling)
- Stratified splits: Maintain class distribution

### AWS Services
- **S3**: Data storage
- **Glue**: ETL and data catalog
- **Athena**: Query data in S3
- **SageMaker Data Wrangler**: Visual data preparation
- **SageMaker Processing**: Custom data processing
- **SageMaker Ground Truth**: Data labeling

### Example Code
```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

# Load data
df = pd.read_csv('s3://bucket/data.csv')

# Handle missing values
df['age'].fillna(df['age'].median(), inplace=True)

# Encode categorical variables
df = pd.get_dummies(df, columns=['category'])

# Split features and target
X = df.drop('target', axis=1)
y = df['target']

# Split data
X_train, X_temp, y_train, y_temp = train_test_split(
    X, y, test_size=0.3, random_state=42, stratify=y
)
X_val, X_test, y_val, y_test = train_test_split(
    X_temp, y_temp, test_size=0.5, random_state=42, stratify=y_temp
)

# Normalize features
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_val_scaled = scaler.transform(X_val)
X_test_scaled = scaler.transform(X_test)
```

---

## Phase 3: Feature Engineering

### Objective
Create and select features that improve model performance.

### Key Activities

**3.1 Feature Creation**
- **Domain Knowledge**: Create features based on expertise
- **Interaction Features**: Combine existing features
- **Polynomial Features**: x², x³, etc.
- **Aggregations**: Sum, mean, count, etc.
- **Time-based Features**: Day of week, month, season

**3.2 Feature Selection**
- **Remove Low Variance**: Features with little variation
- **Remove Correlated Features**: Highly correlated pairs
- **Importance-based**: Keep most important features
- **Recursive Feature Elimination**: Iteratively remove features

**3.3 Dimensionality Reduction**
- **PCA**: Principal Component Analysis
- **t-SNE**: Visualization
- **Autoencoders**: Neural network-based

### AWS Services
- **SageMaker Data Wrangler**: Visual feature engineering
- **SageMaker Feature Store**: Centralized feature repository
- **SageMaker Processing**: Custom feature engineering

### Example
```python
# Create interaction features
df['income_per_age'] = df['income'] / df['age']

# Create time-based features
df['day_of_week'] = df['timestamp'].dt.dayofweek
df['is_weekend'] = df['day_of_week'].isin([5, 6]).astype(int)

# Feature selection based on importance
from sklearn.ensemble import RandomForestClassifier

rf = RandomForestClassifier()
rf.fit(X_train, y_train)

# Get feature importance
importance = pd.DataFrame({
    'feature': X_train.columns,
    'importance': rf.feature_importances_
}).sort_values('importance', ascending=False)

# Keep top features
top_features = importance.head(20)['feature'].tolist()
X_train_selected = X_train[top_features]
```

---

## Phase 4: Model Training

### Objective
Train ML models on prepared data.

### Key Activities

**4.1 Algorithm Selection**
- Choose based on problem type and data characteristics
- Consider interpretability requirements
- Evaluate computational constraints

**Common Algorithms**:
- **Classification**: Logistic Regression, Random Forest, XGBoost, Neural Networks
- **Regression**: Linear Regression, XGBoost, Neural Networks
- **Clustering**: K-Means, DBSCAN
- **Anomaly Detection**: Isolation Forest, Random Cut Forest

**4.2 Hyperparameter Tuning**
- **Hyperparameters**: Set before training (learning rate, depth, etc.)
- **Parameters**: Learned during training (weights, biases)

**Tuning Methods**:
- **Grid Search**: Try all combinations
- **Random Search**: Random sampling
- **Bayesian Optimization**: Smart search
- **Automated**: SageMaker Automatic Model Tuning

**4.3 Training Process**
- Initialize model with hyperparameters
- Feed training data in batches/epochs
- Model adjusts parameters to minimize loss
- Validate on validation set
- Iterate until convergence

**4.4 Prevent Overfitting**
- **Regularization**: L1 (Lasso), L2 (Ridge)
- **Dropout**: Randomly disable neurons
- **Early Stopping**: Stop when validation performance degrades
- **Cross-Validation**: K-fold validation
- **More Data**: Collect additional training data

### AWS Services
- **SageMaker Training Jobs**: Managed training
- **SageMaker Built-in Algorithms**: Pre-built algorithms
- **SageMaker Automatic Model Tuning**: Hyperparameter optimization
- **SageMaker Experiments**: Track training runs
- **SageMaker Debugger**: Debug training issues

### Example Code
```python
import sagemaker
from sagemaker import image_uris

# Configure training job
container = image_uris.retrieve('xgboost', region, '1.5-1')

xgb = sagemaker.estimator.Estimator(
    container,
    role=role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    output_path='s3://bucket/output/',
    hyperparameters={
        'objective': 'binary:logistic',
        'num_round': 100,
        'max_depth': 5,
        'eta': 0.2,
        'subsample': 0.8,
        'colsample_bytree': 0.8
    }
)

# Train model
xgb.fit({
    'train': 's3://bucket/train/',
    'validation': 's3://bucket/validation/'
})

# Hyperparameter tuning
from sagemaker.tuner import HyperparameterTuner, IntegerParameter, ContinuousParameter

hyperparameter_ranges = {
    'max_depth': IntegerParameter(3, 10),
    'eta': ContinuousParameter(0.01, 0.3),
    'subsample': ContinuousParameter(0.5, 1.0)
}

tuner = HyperparameterTuner(
    xgb,
    objective_metric_name='validation:auc',
    hyperparameter_ranges=hyperparameter_ranges,
    max_jobs=20,
    max_parallel_jobs=3
)

tuner.fit({'train': train_data, 'validation': val_data})
```

---

## Phase 5: Model Evaluation

### Objective
Assess model performance and determine if it meets business requirements.

### Key Activities

**5.1 Select Evaluation Metrics**

**Classification Metrics**:
- **Accuracy**: Overall correctness
- **Precision**: True positives / Predicted positives
- **Recall**: True positives / Actual positives
- **F1 Score**: Harmonic mean of precision and recall
- **AUC-ROC**: Area under ROC curve
- **Confusion Matrix**: Detailed breakdown

**Regression Metrics**:
- **MAE**: Mean Absolute Error
- **RMSE**: Root Mean Squared Error
- **R²**: Coefficient of determination
- **MAPE**: Mean Absolute Percentage Error

**5.2 Evaluate on Test Set**
- Use held-out test data (never seen during training)
- Calculate metrics
- Compare to baseline and business requirements

**5.3 Error Analysis**
- Identify where model fails
- Analyze misclassifications
- Look for patterns in errors
- Identify data quality issues

**5.4 Model Comparison**
- Compare multiple models
- Consider accuracy vs. interpretability
- Evaluate computational costs
- Assess deployment complexity

**5.5 Business Validation**
- Does model meet business goals?
- Is accuracy sufficient?
- Are false positives/negatives acceptable?
- What is the ROI?

### AWS Services
- **SageMaker Model Evaluation**: Built-in evaluation
- **SageMaker Clarify**: Bias detection, explainability
- **SageMaker Experiments**: Compare models

### Example Code
```python
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score, confusion_matrix

# Make predictions on test set
y_pred = model.predict(X_test)

# Calculate metrics
accuracy = accuracy_score(y_test, y_pred)
precision = precision_score(y_test, y_pred)
recall = recall_score(y_test, y_pred)
f1 = f1_score(y_test, y_pred)

print(f"Accuracy: {accuracy:.3f}")
print(f"Precision: {precision:.3f}")
print(f"Recall: {recall:.3f}")
print(f"F1 Score: {f1:.3f}")

# Confusion matrix
cm = confusion_matrix(y_test, y_pred)
print("Confusion Matrix:")
print(cm)

# Business validation
if accuracy >= 0.85 and recall >= 0.80:
    print("Model meets business requirements")
else:
    print("Model needs improvement")
```

---

## Phase 6: Model Deployment

### Objective
Deploy model to production for inference.

### Key Activities

**6.1 Model Registration**
- Store model artifacts
- Version models
- Document model metadata
- Set approval status

**6.2 Select Deployment Strategy**

**Real-Time Inference**:
- Low latency (< 1 second)
- Synchronous requests
- Persistent endpoint
- **Use**: Interactive applications

**Batch Inference**:
- Process large datasets
- Asynchronous
- No persistent endpoint
- **Use**: Scheduled jobs

**Asynchronous Inference**:
- Queue-based
- Large payloads
- Variable processing time
- **Use**: Document processing

**Serverless Inference**:
- Auto-scales to zero
- Pay-per-request
- Cold start latency
- **Use**: Intermittent traffic

**Edge Inference**:
- On-device inference
- Offline capable
- Low latency
- **Use**: IoT, mobile

**6.3 Deployment Patterns**

**Blue/Green Deployment**:
- Deploy new version alongside old
- Switch traffic when validated
- Easy rollback

**Canary Deployment**:
- Route small % of traffic to new version
- Gradually increase if successful
- Monitor metrics

**A/B Testing**:
- Split traffic between versions
- Compare performance
- Choose winner

**6.4 Infrastructure Setup**
- Configure compute resources
- Set up auto-scaling
- Configure security (VPC, IAM)
- Set up monitoring

### AWS Services
- **SageMaker Model Registry**: Model versioning
- **SageMaker Endpoints**: Real-time inference
- **SageMaker Batch Transform**: Batch inference
- **SageMaker Serverless**: Serverless inference
- **SageMaker Edge Manager**: Edge deployment
- **SageMaker Pipelines**: Automated deployment

### Example Code
```python
# Register model
model_package = model.register(
    content_types=["text/csv"],
    response_types=["text/csv"],
    inference_instances=["ml.m5.xlarge"],
    transform_instances=["ml.m5.xlarge"],
    model_package_group_name="fraud-detection-models",
    approval_status="PendingManualApproval"
)

# Deploy real-time endpoint
predictor = model.deploy(
    initial_instance_count=2,
    instance_type='ml.m5.xlarge',
    endpoint_name='fraud-detection-prod'
)

# Configure auto-scaling
client = boto3.client('application-autoscaling')

client.register_scalable_target(
    ServiceNamespace='sagemaker',
    ResourceId=f'endpoint/fraud-detection-prod/variant/AllTraffic',
    ScalableDimension='sagemaker:variant:DesiredInstanceCount',
    MinCapacity=2,
    MaxCapacity=10
)

client.put_scaling_policy(
    PolicyName='InvocationsPerInstance',
    ServiceNamespace='sagemaker',
    ResourceId=f'endpoint/fraud-detection-prod/variant/AllTraffic',
    ScalableDimension='sagemaker:variant:DesiredInstanceCount',
    PolicyType='TargetTrackingScaling',
    TargetTrackingScalingPolicyConfiguration={
        'TargetValue': 1000.0,
        'PredefinedMetricSpecification': {
            'PredefinedMetricType': 'SageMakerVariantInvocationsPerInstance'
        }
    }
)
```

---

## Phase 7: Monitoring & Maintenance

### Objective
Monitor model performance and maintain quality over time.

### Key Activities

**7.1 Performance Monitoring**
- Track inference latency
- Monitor throughput
- Track error rates
- Monitor resource utilization

**7.2 Model Quality Monitoring**
- **Data Drift**: Input data distribution changes
- **Concept Drift**: Relationship between features and target changes
- **Model Drift**: Model performance degrades
- **Prediction Drift**: Output distribution changes

**7.3 Data Quality Monitoring**
- Missing values
- Outliers
- Schema changes
- Data type violations

**7.4 Bias Monitoring**
- Monitor fairness metrics over time
- Detect bias drift
- Alert on violations

**7.5 Explainability Monitoring**
- Track feature importance changes
- Monitor SHAP value distributions
- Detect explanation drift

**7.6 Business Metrics**
- Track business KPIs
- Measure ROI
- Monitor user satisfaction
- Track task completion rates

**7.7 Alerting**
- Set up CloudWatch alarms
- Define thresholds
- Configure notifications (SNS)
- Escalation procedures

### AWS Services
- **SageMaker Model Monitor**: Automated monitoring
- **CloudWatch**: Metrics and alarms
- **CloudWatch Logs**: Log analysis
- **EventBridge**: Event-driven automation
- **SNS**: Notifications

### Example Code
```python
from sagemaker.model_monitor import DefaultModelMonitor, DataCaptureConfig

# Enable data capture
data_capture_config = DataCaptureConfig(
    enable_capture=True,
    sampling_percentage=100,
    destination_s3_uri='s3://bucket/data-capture'
)

predictor = model.deploy(
    initial_instance_count=1,
    instance_type='ml.m5.xlarge',
    data_capture_config=data_capture_config
)

# Create baseline
baseline_job = DefaultModelMonitor.suggest_baseline(
    baseline_dataset='s3://bucket/baseline/data.csv',
    dataset_format={'csv': {'header': True}},
    output_s3_uri='s3://bucket/baseline-results',
    instance_type='ml.m5.xlarge'
)

# Create monitoring schedule
monitor = DefaultModelMonitor(
    role=role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    max_runtime_in_seconds=3600
)

monitor.create_monitoring_schedule(
    monitor_schedule_name='daily-monitoring',
    endpoint_input=predictor.endpoint_name,
    output_s3_uri='s3://bucket/monitoring-results',
    statistics=baseline_job.baseline_statistics(),
    constraints=baseline_job.suggested_constraints(),
    schedule_cron_expression='cron(0 0 * * ? *)',  # Daily at midnight
    enable_cloudwatch_metrics=True
)
```

---

## Phase 8: Iteration & Retraining

### Objective
Continuously improve model based on new data and feedback.

### When to Retrain

**Scheduled Retraining**:
- Regular intervals (weekly, monthly)
- Incorporate new data
- Adapt to seasonal changes

**Triggered Retraining**:
- Performance drops below threshold
- Data drift detected
- Concept drift detected
- Business requirements change

**Event-Driven Retraining**:
- New data batch available
- Significant event occurs
- User feedback accumulated

### Retraining Process
1. Collect new data
2. Combine with historical data (or use only new)
3. Repeat data preparation and feature engineering
4. Retrain model with updated data
5. Evaluate new model
6. Deploy if better than current model
7. Monitor performance

### Continuous Learning
- Online learning (update with each new sample)
- Mini-batch updates
- Active learning (label most informative samples)

### AWS Services
- **SageMaker Pipelines**: Automated retraining workflows
- **EventBridge**: Trigger retraining on events
- **Step Functions**: Orchestrate retraining process
- **Lambda**: Trigger functions

### Example Code
```python
from sagemaker.workflow.pipeline import Pipeline
from sagemaker.workflow.steps import TrainingStep, ProcessingStep
from sagemaker.workflow.conditions import ConditionGreaterThanOrEqualTo
from sagemaker.workflow.condition_step import ConditionStep

# Define retraining pipeline
processing_step = ProcessingStep(
    name="DataPreprocessing",
    processor=processor,
    inputs=[...],
    outputs=[...]
)

training_step = TrainingStep(
    name="ModelTraining",
    estimator=estimator,
    inputs={...}
)

evaluation_step = ProcessingStep(
    name="ModelEvaluation",
    processor=evaluation_processor,
    inputs=[...],
    outputs=[...]
)

# Conditional deployment
condition = ConditionGreaterThanOrEqualTo(
    left=evaluation_metric,
    right=0.85  # Minimum accuracy threshold
)

condition_step = ConditionStep(
    name="CheckAccuracy",
    conditions=[condition],
    if_steps=[deployment_step],
    else_steps=[]
)

# Create pipeline
pipeline = Pipeline(
    name="RetrainingPipeline",
    steps=[processing_step, training_step, evaluation_step, condition_step]
)

pipeline.upsert(role_arn=role)

# Schedule pipeline execution
# Via EventBridge rule (daily, weekly, etc.)
```

---

## Well-Architected ML Lifecycle Components

The AWS Well-Architected Machine Learning Lens defines cross-cutting components that span multiple lifecycle phases:

### Feature Store (Online & Offline)
- **Purpose**: Centralized repository for reusable, consistent features across teams and projects
- **Online Store**: Low-latency retrieval for real-time inference
- **Offline Store**: Historical feature values for training and batch scoring
- **Benefit**: Eliminates duplication and need to rerun feature engineering code
- **AWS Service**: SageMaker Feature Store

### Model Registry
- **Purpose**: Repository for storing ML model artifacts + metadata (data, code, model)
- **Capabilities**: Version control, lineage tracking, approval workflows
- **Tracks**: Model versions, training data references, hyperparameters, evaluation metrics
- **AWS Service**: SageMaker Model Registry

### Lineage Tracker
- **Purpose**: Enables reproducible ML experiences — recreate any point-in-time environment
- **Tracks**:
  - **System Architecture**: Infrastructure as Code (IaC) to address environment drift
  - **Data**: Metadata, values, and features
  - **Model**: Algorithm, features, parameters, and hyperparameters
  - **Code**: Implementation, modeling, and pipeline code
- **AWS Service**: SageMaker ML Lineage Tracking

### Feedback Loops
- **Performance Feedback Loop**: Informs data preparation based on model evaluation during development
- **Model Drift Feedback Loop**: Informs data preparation based on model evaluation during production deployment
- **Key Insight**: Feedback loops are the key enablers for monitoring and continuous improvement

### Alarm Manager & Scheduler
- **Alarm Manager**: Receives alerts from monitoring → publishes notifications → triggers retraining pipeline
- **Scheduler**: Initiates model retraining at business-defined intervals
- **AWS Services**: CloudWatch Alarms, EventBridge, SNS

---

## MLOps: Automating the Lifecycle

### MLOps Principles
- **Automation**: Automate repetitive tasks
- **Versioning**: Track data, code, models
- **Reproducibility**: Recreate results
- **Monitoring**: Continuous observation
- **Collaboration**: Team coordination

### MLOps Maturity Levels

| Level | Description | Characteristics |
|-------|-------------|----------------|
| **Level 0** | Manual | Manual data prep, training, deployment. No CI/CD. Notebook-driven. |
| **Level 1** | ML Pipeline Automation | Automated training pipeline, continuous training (CT), experiment tracking. Manual deployment. |
| **Level 2** | CI/CD Pipeline Automation | Full CI/CD/CT. Automated testing, deployment, monitoring. Feature store. Model registry. |

**Exam Tip**: Know the progression — most organizations start at Level 0 and mature toward Level 2. The exam tests understanding of what each level provides.

### MLOps Components

**1. Version Control**
- Code (Git)
- Data (S3 versioning, DVC)
- Models (Model Registry)
- Experiments (SageMaker Experiments)

**2. CI/CD/CT for ML**
- **CI**: Automated testing of code and data
- **CD**: Automated deployment of models and pipelines
- **CT**: Continuous Training — automatically retrain when data or code changes
- Source data is a first-class input alongside source code
- Pipelines must version ML models, inputs, and outputs for traceability

**3. Infrastructure as Code**
- CloudFormation
- CDK
- Terraform

**4. Orchestration**
- SageMaker Pipelines
- Step Functions
- Airflow

### AWS MLOps Services
- **SageMaker Pipelines**: End-to-end workflows
- **SageMaker Model Registry**: Model versioning
- **SageMaker Projects**: MLOps templates
- **SageMaker Feature Store**: Feature management
- **SageMaker Experiments**: Experiment tracking
- **SageMaker ML Lineage Tracking**: Artifact lineage
- **CodePipeline**: CI/CD
- **CloudFormation**: Infrastructure as code

### MLOps Benefits (per AWS)
- **Productivity**: Self-service environments with curated datasets
- **Repeatability**: Automated, repeatable training → evaluation → deployment
- **Reliability**: CI/CD for quality and consistency
- **Auditability**: Versioned inputs/outputs demonstrate exactly how model was built
- **Data & Model Quality**: Enforce policies against bias, track data statistics and model quality over time

---

## Exam Focus Areas

### Common Question Types

**1. Phase Identification**
- "Which phase involves splitting data?" → Data Preparation
- "When do you tune hyperparameters?" → Model Training
- "When do you detect data drift?" → Monitoring

**2. Activity Sequencing**
- Correct order of lifecycle phases
- Dependencies between activities

**3. Tool Selection**
- "Which service for data labeling?" → Ground Truth
- "Which service for monitoring drift?" → Model Monitor
- "Which service for hyperparameter tuning?" → Automatic Model Tuning

**4. Best Practices**
- When to retrain models
- How to prevent overfitting
- How to handle imbalanced data

### Key Concepts to Remember

**Data Splitting**:
- Training: 70-80%
- Validation: 10-15%
- Test: 10-15%
- Time-series: Use time-based splits

**Overfitting vs Underfitting**:
- Overfitting: Good training, poor test → Regularization, more data
- Underfitting: Poor training and test → More complex model, more features

**Retraining Triggers**:
- Scheduled (regular intervals)
- Performance degradation
- Data/concept drift
- New data available

**Deployment Strategies**:
- Blue/Green: Full switch
- Canary: Gradual rollout
- A/B: Compare versions

### AWS Service Mapping

| Phase | AWS Service |
|-------|-------------|
| Data Collection | S3, Glue, Kinesis |
| Data Preparation | Data Wrangler, Processing |
| Data Labeling | Ground Truth |
| Feature Engineering | Feature Store, Data Wrangler |
| Model Training | Training Jobs, Built-in Algorithms |
| Hyperparameter Tuning | Automatic Model Tuning |
| Model Evaluation | Clarify, Experiments |
| Model Registry | Model Registry |
| Deployment | Endpoints, Batch Transform |
| Monitoring | Model Monitor, CloudWatch |
| Orchestration | Pipelines, Step Functions |

---

## Sample Questions

### Question 1: Data Splitting

**Question**: A data scientist is training a model to predict customer churn. They have 10,000 historical customer records. What is the recommended approach for splitting the data?

A) Use all 10,000 records for training  
B) Split 80% training, 20% test  
C) Split 70% training, 15% validation, 15% test  
D) Use cross-validation only

**Correct Answer**: C

**Explanation**: Need three sets: training (learn), validation (tune hyperparameters), test (final evaluation). 70-15-15 split is standard. Option A has no evaluation data. Option B lacks validation set for hyperparameter tuning. Option D doesn't provide independent test set.

---

### Question 2: Monitoring

**Question**: A deployed fraud detection model's accuracy has dropped from 95% to 80% over 3 months. What is the most likely cause and solution?

A) Overfitting - collect more training data  
B) Data drift - retrain model with recent data  
C) Underfitting - use more complex model  
D) Poor feature engineering - create new features

**Correct Answer**: B

**Explanation**: Performance degradation over time indicates data drift (input data distribution changed) or concept drift (fraud patterns evolved). Solution is to retrain with recent data. Overfitting would show poor performance from the start. Underfitting wouldn't explain time-based degradation.

---

### Question 3: AWS Service Selection

**Question**: Which AWS service should be used to automatically detect when a deployed model's predictions start deviating from the baseline?

A) SageMaker Experiments  
B) SageMaker Debugger  
C) SageMaker Model Monitor  
D) SageMaker Clarify

**Correct Answer**: C

**Explanation**: SageMaker Model Monitor continuously monitors deployed models for data drift, model drift, and prediction drift. Experiments tracks training runs. Debugger helps during training. Clarify detects bias and provides explainability.

---

## Drift Types Summary

| Drift Type | Definition | Detection | Impact |
|-----------|-----------|-----------|--------|
| **Data Drift** | Input data distribution changes vs. training data | Compare inference data statistics to training baseline | Model predictions become unreliable |
| **Concept Drift** | Relationship between features and target changes | Monitor model quality metrics over time | Model learns wrong patterns |
| **Model Drift** | Model performance degrades over time | Track accuracy, precision, recall trends | Business KPIs impacted |
| **Feature Attribution Drift** | Feature importance/contribution changes | Monitor SHAP values over time | Model explanations become invalid |
| **Bias Drift** | Fairness metrics change over time | Monitor bias metrics per demographic group | Responsible AI violations |

**Exam Tip**: Data drift and concept drift are the most commonly tested. Data drift = input changes. Concept drift = the "rules" change (e.g., fraud patterns evolve).

---

## Study Tips

1. **Understand the complete flow**: Don't just memorize phases, understand why each is necessary
2. **Know AWS services for each phase**: Match services to lifecycle stages
3. **Practice with hands-on**: Use SageMaker to go through the full lifecycle
4. **Focus on monitoring**: Drift detection and retraining are commonly tested
5. **Understand iteration**: ML is iterative, not linear
6. **Know when to retrain**: Scheduled vs. triggered retraining
7. **Master data splitting**: Especially for time-series data
8. **Feature Store**: Know online (real-time) vs. offline (batch/training) stores
9. **Lineage Tracking**: Understand it enables reproducibility across the entire lifecycle
10. **MLOps Maturity**: Know the 3 levels (Manual → Pipeline Automation → CI/CD Automation)

---

## Citations
- [ML Lifecycle Architecture Diagram - AWS Well-Architected ML Lens](https://docs.aws.amazon.com/wellarchitected/latest/machine-learning-lens/architecture-diagram.html)
- [Monitoring - AWS Well-Architected ML Lens](https://docs.aws.amazon.com/wellarchitected/latest/machine-learning-lens/monitoring.html)
- [Why Should You Use MLOps? - Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/sagemaker-projects-why.html)
- [Implement MLOps - Amazon SageMaker AI](https://docs.aws.amazon.com/sagemaker/latest/dg/mlops.html)
