# Amazon SageMaker

## 1. Service/Topic Overview
- **Purpose**: Fully managed machine learning service to build, train, and deploy ML models at scale
- **Service Level**: Regional
- **Key Use Cases**: 
  - End-to-end ML model development and deployment
  - Training custom ML models with managed infrastructure
  - Hosting ML models for real-time and batch inference
  - AutoML for automated model building (SageMaker Autopilot)
  - MLOps and model monitoring in production
- **Exam Weight**: High
- **Exam Domain**: 
  - Domain 1: Fundamentals of AI and ML (20%)
  - Domain 3: Applications of Foundation Models (28%)
  - Domain 5: Security, Compliance, and Governance for AI Solutions (14%)
- **Task Statements**: 
  - Task 1.1: Explain basic AI concepts and terminologies
  - Task 1.3: Describe the ML development lifecycle
  - Task 3.4: Select appropriate model deployment methods
  - Task 5.1: Explain methods to secure AI systems

## 2. Exam Keyword Mapping
- **Trigger Words**: 
  - "Custom ML model training"
  - "End-to-end ML pipeline"
  - "Model deployment and hosting"
  - "Jupyter notebooks for ML"
  - "Managed training infrastructure"
  - "AutoML" or "automated model building"
  - "Model monitoring and drift detection"
  - "MLOps workflow"
- **Scenario Indicators**: 
  - "Need to train a custom model with large datasets"
  - "Deploy models for real-time predictions"
  - "Automate the entire ML lifecycle"
  - "Monitor model performance in production"
  - "Scale training across multiple instances"
  - "Build models without deep ML expertise" (Autopilot)
- **Anti-patterns**: 
  - Pre-built AI services for common tasks → Use Rekognition, Comprehend, etc.
  - Only need inference with foundation models → Use Amazon Bedrock
  - Simple data analysis without ML → Use Amazon Athena or QuickSight
  - Real-time streaming analytics → Use Kinesis Data Analytics

## 3. Core Concepts
- **Architectural Patterns**: 
  - **Data Preparation**: S3 → SageMaker Processing → Cleaned Data
  - **Training**: Training Data → SageMaker Training Job → Model Artifacts (S3)
  - **Deployment**: Model → SageMaker Endpoint → Real-time Inference
  - **Batch**: Model → SageMaker Batch Transform → Predictions (S3)
- **Service Limits**: 
  - Default: 20 instances per training job
  - Endpoint instances: Varies by instance type
  - Concurrent training jobs: 100 (default)
  - Model size: Up to 2 GB for real-time endpoints (uncompressed)
- **Data Flow**: 
  - Input: Training data from S3, EFS, or FSx for Lustre
  - Processing: Distributed training across multiple instances
  - Output: Model artifacts stored in S3, metrics in CloudWatch
- **Integration Points**: 
  - **S3**: Primary data storage for datasets and model artifacts
  - **IAM**: Role-based access control for SageMaker resources
  - **CloudWatch**: Monitoring, logging, and metrics
  - **ECR**: Custom container images for training and inference
  - **Step Functions**: Orchestrate ML workflows
  - **Lambda**: Trigger inference or preprocessing tasks
  - **Bedrock**: Fine-tune foundation models using SageMaker

## 4. Key Features & Components

### SageMaker Studio
- Integrated development environment (IDE) for ML
- Jupyter notebooks with managed compute
- Visual workflow builder
- Experiment tracking and model registry

### SageMaker Training
- **Built-in Algorithms**: XGBoost, Linear Learner, K-Means, etc.
- **Distributed Training**: Data parallelism and model parallelism
- **Spot Training**: Use EC2 Spot instances for cost savings (up to 90%)
- **Managed Spot Training**: Automatic checkpoint and resume
- **Hyperparameter Tuning**: Automatic optimization of model parameters

### SageMaker Autopilot
- AutoML capability for automated model building
- Automatically explores different algorithms and hyperparameters
- Generates explainable models with feature importance
- No ML expertise required

### SageMaker Inference
- **Real-time Endpoints**: Low-latency predictions (milliseconds)
- **Batch Transform**: Process large datasets asynchronously
- **Serverless Inference**: Auto-scaling without managing instances
- **Asynchronous Inference**: Long-running inference tasks (up to 15 min)
- **Multi-Model Endpoints**: Host multiple models on single endpoint

### SageMaker Model Monitor
- Detect data drift and model quality degradation
- Continuous monitoring of predictions
- Automated alerts when performance degrades
- Baseline comparison for data quality

### SageMaker Feature Store
- Centralized repository for ML features
- Online store (low-latency) and offline store (training)
- Feature versioning and lineage tracking
- Consistent features across training and inference

### SageMaker Pipelines
- CI/CD for ML workflows (MLOps)
- Automate and orchestrate ML steps
- Version control for pipelines
- Integration with model registry

### SageMaker Ground Truth
- Data labeling service for training datasets
- Human labeling workforce (public, private, vendor)
- Active learning to reduce labeling costs
- Built-in labeling workflows for images, text, video

### SageMaker Neo
- Optimize models for edge devices and cloud inference
- Compile models once, run anywhere
- Up to 2x performance improvement
- Supports TensorFlow, PyTorch, MXNet, ONNX

### SageMaker Clarify
- Detect bias in ML models and datasets
- Explain model predictions (SHAP values)
- Feature importance analysis
- Pre-training and post-training bias metrics

### SageMaker Debugger
- Real-time monitoring during training
- Automatic detection of training issues
- Profile resource utilization (CPU, GPU, memory)
- Built-in rules for common problems

## 5. Exam Focus Areas

### Common Question Types
1. **Scenario-based selection**: "Which SageMaker feature should you use to..."
2. **Cost optimization**: "How to reduce training costs in SageMaker?"
3. **Deployment options**: "Real-time vs. batch vs. serverless inference"
4. **MLOps workflows**: "Automate model retraining and deployment"
5. **Responsible AI**: "Detect bias and explain predictions"

### Gotchas
- **Training data location**: Must be in S3 (or EFS/FSx for Lustre)
- **Model artifacts**: Automatically saved to S3 after training
- **Endpoint costs**: Charged per hour, even with no traffic (use serverless for variable workloads)
- **Spot training**: Not suitable for jobs that can't tolerate interruptions without checkpointing
- **Instance types**: Training and inference can use different instance types
- **VPC configuration**: Optional but recommended for security

### Comparison Points
| Feature | SageMaker | Bedrock | Pre-built AI Services |
|---------|-----------|---------|----------------------|
| **Use Case** | Custom ML models | Foundation model inference/fine-tuning | Common AI tasks (vision, NLP) |
| **ML Expertise** | Required (except Autopilot) | Minimal | None |
| **Training** | Full control | Fine-tuning only | No training |
| **Deployment** | Managed endpoints | API-based | API-based |
| **Cost Model** | Instance-based | Token-based | API call-based |

### Decision Trees
**When to use SageMaker:**
- ✅ Need custom ML model with proprietary data
- ✅ Require full control over training process
- ✅ Building end-to-end ML pipelines
- ✅ Need to deploy models for real-time inference
- ✅ Require MLOps automation

**When NOT to use SageMaker:**
- ❌ Pre-built AI service exists (Rekognition, Comprehend)
- ❌ Only need foundation model inference → Use Bedrock
- ❌ No ML expertise and simple use case → Use pre-built services
- ❌ One-time analysis → Use SageMaker Canvas or Autopilot

## 6. Best Practices

### Security
- **IAM Roles**: Use least privilege for SageMaker execution roles
- **VPC Configuration**: Deploy in VPC for network isolation
- **Encryption**: 
  - At rest: S3 encryption for data and model artifacts
  - In transit: TLS for API calls and endpoints
  - KMS integration for customer-managed keys
- **Network Isolation**: Enable network isolation for training and inference
- **PrivateLink**: Access SageMaker API via VPC endpoints
- **Data Protection**: Use SageMaker's built-in data encryption

### Cost Optimization
- **Spot Instances**: Use managed spot training for up to 90% savings
- **Right-sizing**: Choose appropriate instance types (don't over-provision)
- **Serverless Inference**: For variable or unpredictable traffic
- **Multi-Model Endpoints**: Host multiple models on single endpoint
- **Automatic Scaling**: Configure auto-scaling for endpoints
- **Batch Transform**: Use for non-real-time predictions (cheaper than endpoints)
- **SageMaker Savings Plans**: Commit to usage for discounts
- **Delete Unused Resources**: Stop notebook instances, delete unused endpoints

### Performance
- **Distributed Training**: Use for large datasets and models
- **Pipe Mode**: Stream data from S3 instead of downloading (faster, cheaper)
- **Instance Types**: 
  - Training: ml.p3/p4 for GPU, ml.c5 for CPU
  - Inference: ml.inf1 for cost-effective inference
- **Model Optimization**: Use SageMaker Neo for edge deployment
- **Caching**: Enable caching in SageMaker Pipelines
- **Feature Store**: Reduce feature computation overhead

### Operations
- **Monitoring**: 
  - CloudWatch metrics for endpoint latency and invocations
  - Model Monitor for data drift detection
  - Debugger for training issues
- **Logging**: Enable CloudWatch Logs for training and inference
- **Versioning**: Use Model Registry for version control
- **Tagging**: Tag resources for cost allocation and organization
- **Automation**: Use SageMaker Pipelines for CI/CD

## 7. Hands-On Labs

### Quick Setup
```bash
# Install SageMaker SDK
pip install sagemaker boto3

# Create S3 bucket for SageMaker
aws s3 mb s3://my-sagemaker-bucket-<account-id>

# Create IAM role for SageMaker (via console or CloudFormation)
```

### Practical Exercises

#### Exercise 1: Train a Model with Built-in Algorithm
```python
import sagemaker
from sagemaker import get_execution_role
from sagemaker.estimator import Estimator

# Setup
role = get_execution_role()
sess = sagemaker.Session()
bucket = sess.default_bucket()

# Use built-in XGBoost algorithm
container = sagemaker.image_uris.retrieve('xgboost', sess.boto_region_name, '1.5-1')

# Configure training job
xgb = Estimator(
    container,
    role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    output_path=f's3://{bucket}/output',
    sagemaker_session=sess
)

# Set hyperparameters
xgb.set_hyperparameters(
    objective='binary:logistic',
    num_round=100,
    max_depth=5
)

# Train model
xgb.fit({'train': 's3://my-bucket/train-data'})
```

#### Exercise 2: Deploy Model for Real-time Inference
```python
# Deploy trained model
predictor = xgb.deploy(
    initial_instance_count=1,
    instance_type='ml.m5.large',
    endpoint_name='my-xgboost-endpoint'
)

# Make prediction
result = predictor.predict(test_data)

# Clean up
predictor.delete_endpoint()
```

#### Exercise 3: Use SageMaker Autopilot
```python
from sagemaker.automl.automl import AutoML

# Configure Autopilot
automl = AutoML(
    role=role,
    target_attribute_name='target',
    output_path=f's3://{bucket}/autopilot-output',
    max_candidates=10
)

# Start AutoML job
automl.fit(
    inputs='s3://my-bucket/training-data.csv',
    job_name='my-autopilot-job'
)

# Deploy best model
predictor = automl.deploy(
    initial_instance_count=1,
    instance_type='ml.m5.large'
)
```

### CLI Commands
```bash
# List training jobs
aws sagemaker list-training-jobs

# Describe training job
aws sagemaker describe-training-job --training-job-name my-training-job

# List endpoints
aws sagemaker list-endpoints

# Create endpoint
aws sagemaker create-endpoint \
    --endpoint-name my-endpoint \
    --endpoint-config-name my-endpoint-config

# Delete endpoint
aws sagemaker delete-endpoint --endpoint-name my-endpoint

# List models
aws sagemaker list-models

# Create model
aws sagemaker create-model \
    --model-name my-model \
    --primary-container Image=<image-uri>,ModelDataUrl=s3://bucket/model.tar.gz \
    --execution-role-arn arn:aws:iam::123456789012:role/SageMakerRole
```

### Console Walkthrough
1. **SageMaker Studio**: Navigate to SageMaker → Studio → Open Studio
2. **Create Notebook**: Studio → File → New → Notebook
3. **Training Job**: SageMaker → Training → Training jobs → Create training job
4. **Deploy Model**: SageMaker → Inference → Models → Create model → Create endpoint
5. **Monitor**: SageMaker → Inference → Endpoints → Select endpoint → View metrics

## 8. Sample Questions

### Question 1
A data scientist needs to train a custom image classification model using a large dataset of 500,000 images stored in Amazon S3. The training process is expected to take several hours, and the company wants to minimize costs while ensuring the training job can recover from interruptions.

Which SageMaker feature should be used?

A) SageMaker Serverless Inference with automatic scaling  
B) SageMaker Managed Spot Training with checkpointing enabled  
C) SageMaker Real-time Endpoints with auto-scaling  
D) SageMaker Batch Transform with on-demand instances  

**Correct Answer: B**

**Explanation:**
- **B is correct**: Managed Spot Training uses EC2 Spot instances (up to 90% cost savings) and automatically handles interruptions by saving checkpoints and resuming training. Perfect for long-running training jobs where cost optimization is important.
- **A is incorrect**: Serverless Inference is for model deployment/inference, not training.
- **C is incorrect**: Real-time Endpoints are for hosting models for inference, not training.
- **D is incorrect**: Batch Transform is for running batch inference on trained models, not for training models.

### Question 2
A machine learning team has deployed multiple models for different business units. Each model receives unpredictable traffic patterns with long periods of inactivity followed by sudden bursts. The team wants to optimize costs while maintaining low latency during active periods.

What is the MOST cost-effective deployment strategy?

A) Deploy each model on a separate real-time endpoint with auto-scaling  
B) Use SageMaker Serverless Inference for all models  
C) Deploy all models on a single Multi-Model Endpoint  
D) Use SageMaker Batch Transform with scheduled jobs  

**Correct Answer: B**

**Explanation:**
- **B is correct**: Serverless Inference automatically scales to zero during inactivity (no charges) and scales up during traffic bursts. Ideal for unpredictable, intermittent traffic patterns. Pay only for compute time used.
- **A is incorrect**: Real-time endpoints charge per hour even during inactivity. Auto-scaling helps but doesn't eliminate costs during idle periods.
- **C is incorrect**: Multi-Model Endpoints reduce costs when hosting many models, but still charge for running instances even during inactivity. Better for consistent traffic across models.
- **D is incorrect**: Batch Transform is for asynchronous batch processing, not suitable for real-time inference requirements.

### Question 3
A company is building an ML model to predict customer churn. The data science team wants to ensure the model is fair and unbiased across different demographic groups. They also need to explain individual predictions to business stakeholders for regulatory compliance.

Which SageMaker feature should they use?

A) SageMaker Debugger to monitor training metrics  
B) SageMaker Model Monitor to detect data drift  
C) SageMaker Clarify to detect bias and explain predictions  
D) SageMaker Autopilot to automatically build explainable models  

**Correct Answer: C**

**Explanation:**
- **C is correct**: SageMaker Clarify is specifically designed for responsible AI. It detects bias in datasets and models (pre-training and post-training) and provides explainability through SHAP values and feature importance. Perfect for fairness and regulatory compliance requirements.
- **A is incorrect**: Debugger monitors training jobs for issues like vanishing gradients or overfitting, but doesn't address bias detection or explainability.
- **B is incorrect**: Model Monitor detects data drift and model quality degradation in production, but doesn't provide bias detection or prediction explanations.
- **D is incorrect**: While Autopilot can generate explainable models, it's primarily for automated model building. Clarify is the dedicated tool for bias detection and explainability across any model.

---

**Exam Tip**: SageMaker is AWS's comprehensive ML platform. Focus on understanding the different components (Training, Inference, Autopilot, Clarify, Model Monitor) and when to use each. Know the difference between real-time endpoints (always running, low latency), serverless inference (auto-scales to zero), and batch transform (asynchronous processing). For the AIF-C01 exam, emphasize responsible AI features (Clarify for bias/explainability) and cost optimization strategies (Spot Training, Serverless Inference, Multi-Model Endpoints).
