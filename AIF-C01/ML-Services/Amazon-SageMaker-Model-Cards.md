# Amazon SageMaker Model Cards

## 1. Service/Topic Overview
- **Purpose**: Document ML model details, intended use, performance metrics, and responsible AI considerations throughout the model lifecycle
- **Service Level**: Regional (part of SageMaker)
- **Key Use Cases**: 
  - Model governance and documentation
  - Regulatory compliance and audit trails
  - Responsible AI transparency and accountability
  - Model risk management and approval workflows
  - Sharing model information across teams
- **Exam Weight**: Medium
- **Exam Domain**: 
  - Domain 4: Guidelines for Responsible AI (14%)
  - Domain 5: Security, Compliance, and Governance for AI Solutions (14%)
- **Task Statements**: 
  - Task 4.1: Explain the development of AI systems that are responsible
  - Task 5.3: Recognize governance and compliance regulations for AI systems

## 2. Exam Keyword Mapping
- **Trigger Words**: 
  - "Model documentation"
  - "Model governance"
  - "Regulatory compliance for ML models"
  - "Model transparency"
  - "Audit trail for models"
  - "Model risk management"
  - "Responsible AI documentation"
  - "Model lineage and metadata"
- **Scenario Indicators**: 
  - "Need to document model performance and bias metrics"
  - "Regulatory requirements for model transparency"
  - "Track model information throughout lifecycle"
  - "Share model details with stakeholders"
  - "Maintain audit trail for compliance"
  - "Document intended use and limitations"
- **Anti-patterns**: 
  - Only need to detect bias → Use SageMaker Clarify
  - Need to monitor production models → Use SageMaker Model Monitor
  - Version control for models → Use SageMaker Model Registry
  - Experiment tracking → Use SageMaker Experiments

## 3. Core Concepts
- **Architectural Patterns**: 
  - **Creation**: Model Training → Evaluation → Model Card Creation → Model Registry
  - **Governance**: Model Card → Review/Approval → Deployment → Ongoing Updates
  - **Compliance**: Model Card → Audit Trail → Regulatory Reporting
- **Service Limits**: 
  - Model card size: Up to 100 KB
  - Model card versions: Unlimited
  - Export formats: PDF, JSON
- **Data Flow**: 
  - Input: Model metadata, training data info, evaluation metrics, bias reports
  - Processing: Structured documentation with versioning
  - Output: Exportable model cards (PDF/JSON), searchable metadata
- **Integration Points**: 
  - **SageMaker Model Registry**: Link model cards to registered models
  - **SageMaker Clarify**: Import bias and explainability reports
  - **SageMaker Experiments**: Pull training metrics and parameters
  - **S3**: Store exported model cards
  - **IAM**: Control access to model cards
  - **CloudTrail**: Audit model card changes

## 4. Key Features & Components

### Model Card Sections
1. **Model Overview**
   - Model name, version, and owner
   - Model type and algorithm
   - Problem type (classification, regression, etc.)
   - Intended use and business purpose

2. **Intended Uses**
   - Primary use cases
   - Out-of-scope use cases
   - Target users and audience
   - Limitations and constraints

3. **Training Details**
   - Training data description
   - Data preprocessing steps
   - Training algorithm and hyperparameters
   - Training infrastructure and duration
   - Training metrics and convergence

4. **Evaluation Results**
   - Test dataset description
   - Performance metrics (accuracy, precision, recall, F1, etc.)
   - Evaluation methodology
   - Comparison with baseline models

5. **Bias Analysis**
   - Fairness metrics across demographic groups
   - Pre-training and post-training bias
   - Mitigation strategies applied
   - Integration with SageMaker Clarify reports

6. **Explainability**
   - Feature importance
   - Model interpretability methods
   - SHAP values or other explainability techniques

7. **Additional Information**
   - Ethical considerations
   - Caveats and recommendations
   - Model maintenance and update schedule
   - Contact information

### Key Capabilities
- **Versioning**: Track changes to model cards over time
- **Export**: Generate PDF or JSON exports for sharing
- **Templates**: Use predefined templates for consistency
- **Automation**: Programmatically create and update via SDK/API
- **Search**: Find models based on card metadata
- **Access Control**: IAM-based permissions for viewing/editing

## 5. Exam Focus Areas

### Common Question Types
1. **Governance scenarios**: "How to document models for compliance?"
2. **Responsible AI**: "Track bias metrics and model fairness"
3. **Transparency**: "Share model information with stakeholders"
4. **Audit requirements**: "Maintain model documentation trail"

### Gotchas
- **Not a monitoring tool**: Model Cards document static information; use Model Monitor for live monitoring
- **Manual updates**: Cards don't auto-update with model changes (must update programmatically or manually)
- **Separate from Model Registry**: Model Cards complement but don't replace Model Registry
- **Documentation focus**: Primarily for governance, not operational monitoring

### Comparison Points
| Feature | Model Cards | Model Registry | Model Monitor | Clarify |
|---------|-------------|----------------|---------------|---------|
| **Purpose** | Documentation & governance | Version control | Production monitoring | Bias detection & explainability |
| **When** | Throughout lifecycle | Deployment time | Post-deployment | Training & inference |
| **Output** | PDF/JSON reports | Model versions | Drift alerts | Bias reports |
| **Updates** | Manual/programmatic | Automatic versioning | Continuous | On-demand |

### Decision Trees
**When to use Model Cards:**
- ✅ Regulatory compliance requirements
- ✅ Need model transparency and documentation
- ✅ Governance and approval workflows
- ✅ Sharing model details with non-technical stakeholders
- ✅ Audit trail for responsible AI

**When NOT to use Model Cards:**
- ❌ Real-time bias detection → Use Clarify
- ❌ Production monitoring → Use Model Monitor
- ❌ Model versioning only → Use Model Registry
- ❌ Experiment tracking → Use SageMaker Experiments

## 6. Best Practices

### Security
- **IAM Policies**: Restrict who can create, view, and edit model cards
- **Sensitive Data**: Don't include PII or sensitive training data details
- **Access Logging**: Enable CloudTrail for audit trails
- **Encryption**: Model cards encrypted at rest in SageMaker

### Governance
- **Standardized Templates**: Use consistent templates across organization
- **Required Fields**: Define mandatory sections for all model cards
- **Review Process**: Implement approval workflows before deployment
- **Version Control**: Update cards when models are retrained or modified
- **Retention Policy**: Define how long to keep historical model cards

### Documentation Quality
- **Completeness**: Fill all relevant sections thoroughly
- **Clarity**: Write for non-technical stakeholders
- **Accuracy**: Ensure metrics and descriptions are current
- **Limitations**: Clearly document model constraints and risks
- **Updates**: Keep cards current with model changes

### Automation
- **SDK Integration**: Programmatically create cards during training
- **CI/CD Pipeline**: Include model card generation in MLOps workflows
- **Clarify Integration**: Automatically import bias and explainability reports
- **Template Reuse**: Create organization-specific templates

## 7. Hands-On Labs

### Quick Setup
```python
import boto3
from sagemaker.model_card import ModelCard, ModelOverview, IntendedUses, TrainingDetails

# Initialize SageMaker client
sm_client = boto3.client('sagemaker')
```

### Practical Exercises

#### Exercise 1: Create a Model Card Programmatically
```python
from sagemaker.model_card import (
    ModelCard, ModelOverview, IntendedUses, 
    TrainingDetails, EvaluationJob, MetricGroup, Metric
)

# Define model overview
model_overview = ModelOverview(
    model_name="customer-churn-predictor",
    model_version="1.0",
    model_description="XGBoost model to predict customer churn",
    problem_type="Binary Classification",
    algorithm_type="XGBoost"
)

# Define intended uses
intended_uses = IntendedUses(
    purpose_of_model="Predict customers likely to churn for retention campaigns",
    intended_uses="Customer retention, marketing optimization",
    factors_affecting_model_efficiency="Data quality, feature availability",
    risk_rating="Medium",
    explanations_for_risk_rating="Model decisions impact customer experience"
)

# Define training details
training_details = TrainingDetails(
    training_observations="Training completed successfully with 95% accuracy",
    training_job_details={
        "training_arn": "arn:aws:sagemaker:us-east-1:123456789012:training-job/churn-model-2024",
        "training_datasets": ["s3://my-bucket/training-data/"],
        "training_environment": {
            "container_image": ["763104351884.dkr.ecr.us-east-1.amazonaws.com/xgboost:latest"]
        }
    }
)

# Create model card
model_card = ModelCard(
    name="customer-churn-model-card",
    status="Draft",
    model_overview=model_overview,
    intended_uses=intended_uses,
    training_details=training_details
)

# Create in SageMaker
model_card.create()
print(f"Model Card created: {model_card.arn}")
```

#### Exercise 2: Add Evaluation Metrics
```python
# Define evaluation metrics
evaluation_job = EvaluationJob(
    name="churn-model-evaluation",
    evaluation_observation="Model performs well across all metrics",
    metric_groups=[
        MetricGroup(
            name="binary_classification_metrics",
            metric_data=[
                Metric(name="accuracy", type="number", value=0.95),
                Metric(name="precision", type="number", value=0.93),
                Metric(name="recall", type="number", value=0.91),
                Metric(name="f1_score", type="number", value=0.92),
                Metric(name="auc", type="number", value=0.96)
            ]
        )
    ]
)

# Update model card with evaluation
model_card.evaluation_details = [evaluation_job]
model_card.update()
```

#### Exercise 3: Export Model Card
```python
# Export to PDF
response = sm_client.export_model_card(
    ModelCardName='customer-churn-model-card',
    ModelCardVersion=1,
    ExportJobName='churn-model-card-export',
    OutputConfig={
        'S3OutputPath': 's3://my-bucket/model-cards/'
    }
)

print(f"Export job started: {response['ExportJobArn']}")

# Check export status
export_status = sm_client.describe_model_card_export_job(
    ExportJobName='churn-model-card-export'
)
print(f"Status: {export_status['Status']}")
```

### CLI Commands
```bash
# Create model card
aws sagemaker create-model-card \
    --model-card-name my-model-card \
    --model-card-status Draft \
    --content file://model-card-content.json

# List model cards
aws sagemaker list-model-cards

# Describe model card
aws sagemaker describe-model-card \
    --model-card-name my-model-card

# Update model card
aws sagemaker update-model-card \
    --model-card-name my-model-card \
    --model-card-status Approved \
    --content file://updated-content.json

# Export model card
aws sagemaker create-model-card-export-job \
    --model-card-name my-model-card \
    --model-card-version 1 \
    --export-job-name my-export-job \
    --output-config S3OutputPath=s3://my-bucket/exports/

# Delete model card
aws sagemaker delete-model-card \
    --model-card-name my-model-card
```

### Console Walkthrough
1. **Navigate**: SageMaker Console → Governance → Model Cards
2. **Create**: Click "Create model card" → Fill in sections
3. **Add Metrics**: Evaluation section → Add performance metrics
4. **Import Clarify**: Bias analysis → Import from Clarify job
5. **Review**: Preview card → Update status to "Approved"
6. **Export**: Actions → Export to PDF

## 8. Sample Questions

### Question 1
A financial services company is deploying an ML model for loan approval decisions. Regulatory requirements mandate comprehensive documentation of model performance, bias metrics, intended use, and limitations. The documentation must be shareable with auditors and updated when the model is retrained.

Which AWS service should they use?

A) Amazon SageMaker Model Monitor to track model documentation  
B) Amazon SageMaker Model Cards to document model details and governance  
C) Amazon SageMaker Clarify to generate compliance reports  
D) Amazon SageMaker Model Registry to store model metadata  

**Correct Answer: B**

**Explanation:**
- **B is correct**: SageMaker Model Cards are specifically designed for comprehensive model documentation, governance, and compliance. They capture intended use, limitations, performance metrics, bias analysis, and can be exported as PDFs for auditors. Cards can be versioned and updated when models are retrained.
- **A is incorrect**: Model Monitor tracks production model performance and data drift, but doesn't provide comprehensive documentation or governance features.
- **C is incorrect**: Clarify detects bias and provides explainability, but doesn't create comprehensive documentation. Clarify reports can be imported into Model Cards.
- **D is incorrect**: Model Registry manages model versions for deployment but doesn't provide detailed documentation, intended use, or limitations tracking.

### Question 2
A data science team has trained multiple ML models and needs to maintain an audit trail showing model performance, training data characteristics, and bias metrics for each model version. The information must be easily searchable and exportable for compliance reviews.

What is the BEST approach?

A) Store training logs in Amazon CloudWatch Logs  
B) Create SageMaker Model Cards for each model with versioning  
C) Use SageMaker Experiments to track all model metadata  
D) Export SageMaker Clarify reports to Amazon S3  

**Correct Answer: B**

**Explanation:**
- **B is correct**: Model Cards provide structured, versioned documentation that includes all required information (performance, training data, bias metrics). They're searchable, exportable (PDF/JSON), and designed specifically for governance and compliance audit trails.
- **A is incorrect**: CloudWatch Logs capture operational logs but don't provide structured model documentation or easy export for compliance reviews.
- **C is incorrect**: SageMaker Experiments track training runs and metrics but don't provide comprehensive governance documentation, intended use, limitations, or compliance-focused exports.
- **D is incorrect**: While Clarify reports contain bias metrics, they don't capture the full model lifecycle documentation needed for comprehensive audit trails.

### Question 3
An organization wants to ensure all ML models follow responsible AI principles. They need to document intended use cases, out-of-scope applications, fairness metrics, and ethical considerations before models are approved for production deployment.

Which combination of SageMaker features should they use?

A) Model Monitor for fairness tracking and Model Registry for approval  
B) Clarify for bias detection and Model Cards for governance documentation  
C) Autopilot for model building and Debugger for ethical analysis  
D) Feature Store for data governance and Pipelines for approval workflows  

**Correct Answer: B**

**Explanation:**
- **B is correct**: SageMaker Clarify detects bias and provides fairness metrics, while Model Cards document intended uses, limitations, ethical considerations, and governance information. Clarify reports can be imported into Model Cards for comprehensive responsible AI documentation and approval workflows.
- **A is incorrect**: Model Monitor tracks production models but doesn't provide bias detection or ethical documentation. Model Registry manages versions but lacks governance documentation features.
- **C is incorrect**: Autopilot automates model building but doesn't focus on responsible AI documentation. Debugger monitors training issues, not ethical considerations.
- **D is incorrect**: Feature Store manages features, and Pipelines orchestrate workflows, but neither provides responsible AI documentation or ethical consideration tracking.

---

**Exam Tip**: SageMaker Model Cards are the primary tool for ML model governance and compliance documentation. They're distinct from Model Monitor (production monitoring), Model Registry (version control), and Clarify (bias detection). Focus on understanding Model Cards for responsible AI transparency, regulatory compliance, and audit trail scenarios. Remember that Model Cards document static information and must be manually or programmatically updated—they don't automatically track production changes.
