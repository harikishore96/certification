# Amazon SageMaker Assets

## 1. Service/Topic Overview
- **Purpose**: Organizational components within SageMaker that help manage, version, and track ML artifacts including models, datasets, experiments, and pipelines
- **Service Level**: Regional
- **Key Use Cases**: 
  - Model versioning and lineage tracking
  - Dataset management and versioning
  - Experiment tracking and comparison
  - Pipeline orchestration and automation
  - Model registry for deployment governance
- **Exam Weight**: High
- **Exam Domain**: 
  - Domain 3: Applications of Foundation Models (28%)
  - Domain 5: Security, Compliance, and Governance for AI Solutions (14%)
- **Task Statements**: 
  - Task 3.2: Select foundation models for specific use cases
  - Task 5.1: Explain methods to secure AI systems
  - Task 5.3: Recognize governance and compliance regulations for AI systems

## 2. Exam Keyword Mapping
- **Trigger Words**: 
  - "Model versioning", "model registry", "model lineage"
  - "Experiment tracking", "trial comparison"
  - "Dataset versioning", "feature store"
  - "Pipeline automation", "MLOps workflow"
  - "Model governance", "approval workflow"
  - "Artifact tracking", "metadata management"
- **Scenario Indicators**: 
  - "Need to track multiple model versions"
  - "Compare different training experiments"
  - "Automate ML workflow from data to deployment"
  - "Maintain audit trail for model changes"
  - "Manage approval process before production deployment"
  - "Track data lineage and model provenance"
- **Anti-patterns**: 
  - Simple one-time model training (use SageMaker Training Jobs directly)
  - Real-time data streaming (use Kinesis)
  - Data warehousing (use Redshift)
  - Simple file storage (use S3)

## 3. Core Concepts

### Architectural Patterns
```
Data Sources → Feature Store → Training Jobs → Model Registry → Endpoints
                     ↓              ↓              ↓
                Experiments    Pipelines      Model Monitor
                     ↓              ↓              ↓
                 Lineage ← → Model Cards → Governance
```

### Service Limits
- Model Registry: 1000 model packages per model group
- Experiments: 5000 trials per experiment
- Pipelines: 500 pipeline executions (concurrent)
- Feature Store: 100 feature groups per account (default)
- Model Cards: 1000 model cards per account

### Data Flow
1. **Input**: Raw data, training code, hyperparameters
2. **Processing**: Feature engineering, training, evaluation
3. **Output**: Versioned models, metrics, artifacts, metadata

### Integration Points
- **S3**: Artifact storage (models, datasets, logs)
- **ECR**: Container image registry for custom algorithms
- **CloudWatch**: Monitoring and logging
- **EventBridge**: Event-driven automation
- **IAM**: Access control and permissions
- **Step Functions**: Workflow orchestration (alternative to Pipelines)
- **Lambda**: Custom processing and automation

## 4. Key Features & Components

### Exam-Tested Features

#### **Model Registry**
- **Purpose**: Centralized repository for model versioning and deployment governance
- **Key Capabilities**:
  - Model versioning with automatic lineage tracking
  - Approval workflows (PendingManualApproval → Approved → Rejected)
  - Model metadata and documentation
  - Cross-account model sharing
  - Integration with CI/CD pipelines
- **Exam Focus**: When to approve models, version management, deployment governance

#### **Experiments**
- **Purpose**: Track, organize, and compare ML training runs
- **Components**:
  - **Experiment**: Top-level container for related trials
  - **Trial**: Individual training run with specific parameters
  - **Trial Component**: Specific stage (preprocessing, training, evaluation)
- **Metrics Tracked**: Loss, accuracy, precision, recall, custom metrics
- **Exam Focus**: Comparing model performance, hyperparameter tuning results

#### **Pipelines**
- **Purpose**: Automate and orchestrate end-to-end ML workflows
- **Key Features**:
  - DAG-based workflow definition
  - Built-in steps: Processing, Training, Transform, Model, Register, Condition
  - Parameterization for reusability
  - Caching for cost optimization
  - Integration with Model Registry
- **Exam Focus**: MLOps automation, workflow orchestration, cost optimization

#### **Feature Store**
- **Purpose**: Centralized repository for ML features with online and offline access
- **Components**:
  - **Feature Group**: Collection of related features
  - **Online Store**: Low-latency access for real-time inference (DynamoDB-backed)
  - **Offline Store**: Historical data for training (S3-backed)
  - **Feature Definitions**: Schema and metadata
- **Key Capabilities**:
  - Feature versioning and time-travel queries
  - Automatic data ingestion and updates
  - Feature discovery and reuse
  - Point-in-time correct queries
- **Exam Focus**: Online vs offline store, feature reuse, data consistency

#### **Model Cards**
- **Purpose**: Document model details for transparency and governance
- **Sections**:
  - Model details and intended use
  - Training details and data
  - Evaluation results and metrics
  - Considerations (ethical, fairness, limitations)
  - Additional information
- **Exam Focus**: Responsible AI, model documentation, compliance

#### **Lineage Tracking**
- **Purpose**: Automatic tracking of relationships between ML artifacts
- **Tracked Entities**:
  - Datasets → Training Jobs → Models → Endpoints
  - Experiments → Trials → Model Versions
  - Pipeline Executions → Artifacts
- **Query Capabilities**: Forward and backward lineage traversal
- **Exam Focus**: Audit trails, reproducibility, compliance

### Configuration Options
- **Model Registry Approval Status**: Approved, Rejected, PendingManualApproval
- **Pipeline Execution Mode**: Immediate, Scheduled, Event-driven
- **Feature Store Ingestion**: Streaming (real-time), Batch
- **Experiment Tracking**: Automatic (SDK) or Manual (API)

### API/SDK Highlights
```python
# Model Registry
sagemaker_client.create_model_package_group()
sagemaker_client.create_model_package()
sagemaker_client.update_model_package(ModelApprovalStatus='Approved')

# Experiments
smexperiments.experiment.Experiment.create()
smexperiments.trial.Trial.create()
smexperiments.tracker.Tracker.log_metric()

# Pipelines
from sagemaker.workflow.pipeline import Pipeline
pipeline.upsert(role_arn=role)
pipeline.start()

# Feature Store
feature_group.create()
feature_group.ingest()
feature_group.get_record()
```

### Integrations
- **SageMaker Studio**: Visual interface for all assets
- **SageMaker Projects**: MLOps templates with CI/CD
- **SageMaker Model Monitor**: Continuous model quality monitoring
- **AWS CodePipeline**: CI/CD integration
- **AWS Glue**: Data preparation and ETL
- **Amazon Athena**: Query offline feature store

## 5. Exam Focus Areas

### Common Question Types

#### **Scenario-based Selection**
- "A company needs to track which dataset version was used to train each model" → **Lineage Tracking**
- "Data scientists need to compare 50 different hyperparameter combinations" → **Experiments**
- "Automate retraining when new data arrives in S3" → **Pipelines with EventBridge**
- "Require manual approval before deploying models to production" → **Model Registry with approval workflow**
- "Share features between training and real-time inference" → **Feature Store**

#### **Feature Comparison**
| Asset | Primary Purpose | Key Differentiator |
|-------|----------------|-------------------|
| Model Registry | Model versioning & governance | Approval workflows |
| Experiments | Training run comparison | Metrics tracking |
| Pipelines | Workflow automation | End-to-end orchestration |
| Feature Store | Feature management | Online + Offline stores |
| Model Cards | Documentation | Responsible AI focus |
| Lineage | Audit trail | Automatic relationship tracking |

#### **Troubleshooting Questions**
- **Issue**: Models deployed without proper testing
  - **Solution**: Implement Model Registry approval workflow
- **Issue**: Cannot reproduce model results
  - **Solution**: Use Experiments and Lineage Tracking
- **Issue**: Manual ML workflow is error-prone
  - **Solution**: Automate with SageMaker Pipelines
- **Issue**: Training and inference feature mismatch
  - **Solution**: Use Feature Store for consistency

#### **Best Practice Questions**
- Always version models in Model Registry before production deployment
- Use Experiments to track all training runs for comparison
- Implement Pipelines for repeatable, automated workflows
- Document models with Model Cards for governance
- Use Feature Store to prevent training-serving skew

### Gotchas
- **Model Registry**: Models must be registered before deployment governance applies
- **Experiments**: Metrics must be explicitly logged; not automatic
- **Pipelines**: Caching can cause unexpected behavior if not understood
- **Feature Store**: Online store has additional costs; use only when needed
- **Lineage**: Only tracks SageMaker-managed resources, not external systems
- **Model Cards**: Not automatically generated; requires manual documentation

### Comparison Points

#### **SageMaker Pipelines vs AWS Step Functions**
- **Pipelines**: ML-specific, integrated with SageMaker, built-in caching, simpler for ML workflows
- **Step Functions**: General-purpose, broader AWS integration, more complex orchestration

#### **Feature Store Online vs Offline**
- **Online Store**: Real-time inference, low latency (<10ms), DynamoDB-backed, higher cost
- **Offline Store**: Training/batch, S3-backed, lower cost, supports time-travel queries

#### **Experiments vs CloudWatch Metrics**
- **Experiments**: ML-specific, trial comparison, hyperparameter tracking, integrated with Studio
- **CloudWatch**: General monitoring, infrastructure metrics, alerting

### Decision Trees

#### **When to Use Model Registry?**
```
Need model versioning? → YES → Need approval workflow? → YES → Model Registry
                       ↓                                ↓
                      NO                               NO
                       ↓                                ↓
                  Use S3 directly              Use automated deployment
```

#### **When to Use Feature Store?**
```
Need feature reuse? → YES → Real-time inference? → YES → Online + Offline Store
                    ↓                             ↓
                   NO                            NO
                    ↓                             ↓
              Use S3/Glue                   Offline Store only
```

## 6. Best Practices

### Security
- **IAM Policies**: 
  - Separate roles for data scientists (read/write experiments) and ML engineers (deploy models)
  - Restrict Model Registry approval permissions to authorized personnel
  - Use resource-based policies for cross-account model sharing
- **Encryption**: 
  - Enable encryption at rest for Feature Store (KMS)
  - Encrypt S3 buckets storing model artifacts
  - Use VPC endpoints for private connectivity
- **Network Isolation**: 
  - Deploy Feature Store in VPC for private access
  - Use VPC endpoints for SageMaker API calls
- **Data Protection**: 
  - Implement data lineage tracking for compliance
  - Use Model Cards to document data sources and privacy considerations
  - Enable CloudTrail logging for audit trails

### Cost Optimization
- **Model Registry**: Free; only pay for underlying S3 storage
- **Experiments**: Free; minimal CloudWatch Logs costs
- **Pipelines**: 
  - Enable caching to avoid redundant processing
  - Use Spot instances for training steps
  - Parameterize pipelines for reusability
- **Feature Store**: 
  - Use offline store only if real-time access not needed
  - Set appropriate TTL for online store records
  - Monitor read/write capacity units
- **General**: 
  - Delete unused model versions
  - Archive old experiment data to S3 Glacier
  - Use lifecycle policies for S3 artifacts

### Performance
- **Feature Store**: 
  - Batch ingestion for large datasets
  - Use appropriate partition keys for query performance
  - Pre-compute features to reduce inference latency
- **Pipelines**: 
  - Parallelize independent steps
  - Use caching for unchanged steps
  - Optimize instance types per step
- **Model Registry**: 
  - Use model package groups for logical organization
  - Implement automated testing before approval
- **Experiments**: 
  - Log metrics at appropriate intervals (not every step)
  - Use trial components for granular tracking

### Operations
- **Monitoring**: 
  - CloudWatch metrics for pipeline execution status
  - EventBridge rules for pipeline failures
  - Model Registry approval status tracking
  - Feature Store ingestion monitoring
- **Logging**: 
  - Enable CloudTrail for API call auditing
  - CloudWatch Logs for pipeline execution details
  - Experiment metrics for model performance
- **Maintenance**: 
  - Regular cleanup of old model versions
  - Archive completed experiments
  - Update Model Cards with new findings
  - Review and update pipeline definitions
- **Troubleshooting**: 
  - Use Lineage to trace issues back to source
  - Review pipeline execution logs in Studio
  - Check IAM permissions for access issues
  - Validate feature definitions in Feature Store

## 7. Hands-On Labs

### Quick Setup

#### **Create Model Registry**
```python
import boto3
sagemaker = boto3.client('sagemaker')

# Create model package group
response = sagemaker.create_model_package_group(
    ModelPackageGroupName='my-model-group',
    ModelPackageGroupDescription='Image classification models'
)

# Register a model
model_package = sagemaker.create_model_package(
    ModelPackageGroupName='my-model-group',
    ModelPackageDescription='Version 1.0',
    InferenceSpecification={
        'Containers': [{
            'Image': 'account.dkr.ecr.region.amazonaws.com/my-model:latest',
            'ModelDataUrl': 's3://bucket/model.tar.gz'
        }],
        'SupportedContentTypes': ['application/json'],
        'SupportedResponseMIMETypes': ['application/json']
    },
    ModelApprovalStatus='PendingManualApproval'
)
```

#### **Create Experiment and Track Metrics**
```python
from sagemaker.experiments import Run

with Run(
    experiment_name='image-classification-exp',
    run_name='run-001',
    sagemaker_session=sagemaker_session
) as run:
    # Log parameters
    run.log_parameter('learning_rate', 0.01)
    run.log_parameter('batch_size', 32)
    
    # Training code here...
    
    # Log metrics
    run.log_metric('train_loss', 0.25)
    run.log_metric('val_accuracy', 0.92)
```

#### **Create Simple Pipeline**
```python
from sagemaker.workflow.pipeline import Pipeline
from sagemaker.workflow.steps import TrainingStep
from sagemaker.estimator import Estimator

# Define training step
estimator = Estimator(
    image_uri='training-image',
    role=role,
    instance_count=1,
    instance_type='ml.m5.xlarge'
)

training_step = TrainingStep(
    name='TrainModel',
    estimator=estimator,
    inputs={'training': 's3://bucket/data'}
)

# Create pipeline
pipeline = Pipeline(
    name='my-ml-pipeline',
    steps=[training_step]
)

pipeline.upsert(role_arn=role)
pipeline.start()
```

### Practical Exercises

#### **Exercise 1: Model Versioning Workflow**
1. Create a model package group in Model Registry
2. Register two model versions with different metrics
3. Set first version to "Approved"
4. Query lineage to see training job used
5. Deploy approved model to endpoint

#### **Exercise 2: Experiment Comparison**
1. Create an experiment for hyperparameter tuning
2. Run 5 trials with different learning rates
3. Log metrics (loss, accuracy) for each trial
4. Use SageMaker Studio to compare trials visually
5. Identify best performing configuration

#### **Exercise 3: Feature Store Setup**
1. Create feature group with online and offline stores
2. Ingest sample data (batch)
3. Query online store for real-time access
4. Query offline store using Athena
5. Verify point-in-time correctness

### CLI Commands

```bash
# List model package groups
aws sagemaker list-model-package-groups

# Approve model for deployment
aws sagemaker update-model-package \
    --model-package-arn arn:aws:sagemaker:region:account:model-package/group/version \
    --model-approval-status Approved

# List experiments
aws sagemaker list-experiments

# Describe pipeline execution
aws sagemaker describe-pipeline-execution \
    --pipeline-execution-arn arn:aws:sagemaker:region:account:pipeline/name/execution/id

# List feature groups
aws sagemaker list-feature-groups

# Get feature store record
aws sagemaker-featurestore-runtime get-record \
    --feature-group-name my-feature-group \
    --record-identifier-value-as-string "12345"
```

### Console Walkthrough

#### **Model Registry (SageMaker Studio)**
1. Open SageMaker Studio
2. Navigate to "Models" → "Model Registry"
3. Click "Create model group"
4. Register model by selecting training job or providing S3 location
5. Review model details and set approval status
6. View lineage graph to see connected artifacts

#### **Experiments (SageMaker Studio)**
1. In Studio, go to "Experiments and trials"
2. Select experiment to view all trials
3. Click "Compare trials" to see side-by-side metrics
4. Use charts to visualize metric trends
5. Export comparison results

#### **Pipelines (SageMaker Console)**
1. Go to SageMaker Console → "Pipelines"
2. Click "Create pipeline"
3. Define pipeline using Python SDK or Studio visual editor
4. Execute pipeline and monitor progress
5. View execution details and logs for each step

## 8. Sample Questions

### Question 1
A machine learning team is training multiple versions of an image classification model with different architectures and hyperparameters. They need to compare the performance of all training runs and identify the best model for production deployment. The solution should provide a centralized view of all metrics and enable easy comparison.

Which SageMaker feature should they use?

A) SageMaker Model Registry to store and compare model versions  
B) SageMaker Experiments to track and compare training trials  
C) SageMaker Pipelines to automate model training  
D) CloudWatch Logs to collect training metrics  

**Correct Answer: B**

**Explanation**: 
- **B is correct**: SageMaker Experiments is specifically designed for tracking, organizing, and comparing ML training runs. It allows data scientists to log parameters, metrics, and artifacts for each trial, then compare them side-by-side in SageMaker Studio to identify the best performing model.

- **A is incorrect**: Model Registry is for versioning and governance of trained models, not for comparing training runs. It's used after you've identified the best model.

- **C is incorrect**: Pipelines automate ML workflows but don't provide comparison capabilities for training runs. Pipelines can use Experiments for tracking, but Experiments is the direct answer.

- **D is incorrect**: CloudWatch Logs can collect metrics but doesn't provide ML-specific comparison features or a centralized view optimized for model evaluation.

---

### Question 2
A financial services company must maintain a complete audit trail showing which dataset version was used to train each model deployed to production. They also need to track which data preprocessing steps were applied and which training job produced each model. This is required for regulatory compliance.

Which SageMaker capability provides this functionality?

A) SageMaker Model Cards for documenting model details  
B) SageMaker Lineage Tracking for automatic artifact relationships  
C) SageMaker Experiments for tracking training runs  
D) CloudTrail for logging API calls  

**Correct Answer: B**

**Explanation**: 
- **B is correct**: SageMaker Lineage Tracking automatically captures relationships between ML artifacts (datasets → processing jobs → training jobs → models → endpoints). It provides forward and backward lineage traversal, creating a complete audit trail for compliance purposes.

- **A is incorrect**: Model Cards document model details manually but don't automatically track relationships between artifacts or provide queryable lineage.

- **C is incorrect**: Experiments track training runs and metrics but don't automatically capture the full lineage from raw data through deployment.

- **D is incorrect**: CloudTrail logs API calls but doesn't establish relationships between ML artifacts or provide ML-specific lineage tracking.

---

### Question 3
A data science team needs to ensure that the same features are used consistently between model training and real-time inference to prevent training-serving skew. The features should be accessible with low latency (<10ms) for real-time predictions and also available for batch training jobs. The solution should support feature versioning and point-in-time correctness.

Which SageMaker feature should they implement?

A) SageMaker Feature Store with both online and offline stores  
B) Amazon S3 with separate buckets for training and inference data  
C) Amazon DynamoDB for real-time access and S3 for training  
D) SageMaker Model Registry to version feature definitions  

**Correct Answer: A**

**Explanation**: 
- **A is correct**: SageMaker Feature Store is specifically designed to solve training-serving skew by providing a centralized feature repository. The online store (DynamoDB-backed) provides low-latency access for real-time inference, while the offline store (S3-backed) supports batch training. It includes feature versioning and point-in-time correct queries.

- **B is incorrect**: Using separate S3 buckets doesn't prevent training-serving skew and doesn't provide low-latency access for real-time inference. It also lacks built-in versioning and consistency guarantees.

- **C is incorrect**: While this approach could work, it requires manual implementation and doesn't provide the built-in consistency, versioning, and point-in-time correctness that Feature Store offers.

- **D is incorrect**: Model Registry versions models, not features. It doesn't provide feature storage or access capabilities.

---

## Exam Tip

**Focus on the "WHY" for each asset**:
- **Model Registry** → Governance and approval workflows
- **Experiments** → Compare training runs and hyperparameters
- **Pipelines** → Automate end-to-end ML workflows
- **Feature Store** → Prevent training-serving skew with consistent features
- **Model Cards** → Document models for responsible AI and compliance
- **Lineage** → Audit trails and reproducibility

The AIF-C01 exam tests your ability to select the right asset for specific MLOps scenarios. Understand when each asset adds value versus when simpler solutions (like S3 or CloudWatch) are sufficient. Pay special attention to questions about model governance, experiment tracking, and feature consistency—these are high-frequency topics.
