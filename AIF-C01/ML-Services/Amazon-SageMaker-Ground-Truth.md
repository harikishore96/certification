# Amazon SageMaker Ground Truth

## 1. Service/Topic Overview
- **Purpose**: Managed data labeling service that uses human workers and optional machine learning (active learning) to create high-quality labeled training datasets
- **Service Level**: Regional
- **Key Use Cases**:
  - Labeling images for object detection, classification, and segmentation
  - Annotating text for NLP tasks (classification, named entity recognition)
  - Labeling video frames for object detection and tracking
  - Annotating 3D point clouds for autonomous driving / robotics
  - Creating training datasets for custom ML models
- **Exam Weight**: Medium
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — ML development lifecycle, data preparation
  - Domain 3: Applications of Foundation Models (28%) — training data quality
  - Domain 5: Security, Compliance, and Governance (14%) — PII handling, workforce management
- **Task Statements**:
  - 1.3: Describe the ML development lifecycle (data labeling is a critical step)
  - 3.3: Describe the training and fine-tuning process for foundation models

> **Citation**: [Amazon SageMaker Ground Truth Documentation](https://docs.aws.amazon.com/sagemaker/latest/dg/sms.html)

---

## 2. Exam Keyword Mapping

- **Trigger Words**: "data labeling", "annotation", "labeled dataset", "training data", "human labelers", "active learning", "labeling workforce", "bounding box", "image classification labels"
- **Scenario Indicators**:
  - "Need to create labeled training data at scale" → Ground Truth
  - "Reduce cost of data labeling" → Ground Truth automated labeling (active learning)
  - "Use human workers to label data" → Ground Truth
  - "Combine human and machine labeling" → Ground Truth automated data labeling
  - "Label images/text/video for ML training" → Ground Truth
- **Anti-patterns**:
  - Need to **review ML model predictions** (not raw data labeling) → Amazon Augmented AI (A2I)
  - Need to **extract text from documents** → Amazon Textract
  - Need to **detect objects in images** (inference, not labeling) → Amazon Rekognition
  - Need **no-code ML model building** → SageMaker Canvas

---

## 3. Core Concepts

### Architectural Patterns
```
Raw Data (S3) → Input Manifest File → Ground Truth Labeling Job → Workers Label Data → Output Manifest (S3) → ML Training
                                              ↕
                                    Active Learning Model
                              (auto-labels high-confidence data)
```

### Key Components
- **Labeling Job**: The core unit — defines data source, task type, workforce, and instructions
- **Input Manifest File**: JSON Lines file in S3 pointing to data objects to be labeled
- **Output Manifest File**: JSON Lines file containing labels, confidence scores, and metadata
- **Worker Task Template**: HTML-based UI presented to workers for annotation
- **Annotation Consolidation**: Combines labels from multiple workers into a single high-quality label

### Three Workforce Types

| Workforce | Description | Use Case | PII Allowed? |
|-----------|-------------|----------|--------------|
| **Amazon Mechanical Turk** | 500,000+ independent contractors worldwide | General-purpose, non-sensitive data | ❌ No PII |
| **Private Workforce** | Your own employees/contractors (via Amazon Cognito or OIDC) | Sensitive/confidential data, domain expertise | ✅ Yes |
| **Vendor Workforce** | Third-party companies from AWS Marketplace | Specialized labeling expertise | ✅ Yes (per vendor agreement) |

> ⚠️ **Exam Tip**: Mechanical Turk **cannot** be used with PII data, video frame jobs, or 3D point cloud jobs. Use private or vendor workforce instead.

### Data Flow
```
Input: S3 bucket (images, text, video, point clouds) + Input manifest file
  ↓
Processing: Labeling job → Workers annotate → (Optional) Active learning auto-labels
  ↓
Output: S3 bucket → Output manifest file (augmented with labels)
```

### Integration Points
- **Amazon S3**: Input/output data storage
- **Amazon SageMaker**: Use labeled data to train models
- **Amazon SQS**: Queue management for streaming labeling jobs
- **Amazon SNS**: Notifications for streaming job output
- **AWS Lambda**: Pre-annotation and post-annotation processing in custom workflows
- **Amazon Cognito / OIDC**: Private workforce authentication
- **Amazon CloudWatch**: Labeling job logs under `/aws/sagemaker/LabelingJobs`

---

## 4. Key Features & Components

### Built-in Task Types

| Category | Task Types |
|----------|-----------|
| **Images** | Bounding box, Image classification (single/multi-label), Semantic segmentation, Label verification/adjustment |
| **Text** | Text classification (single/multi-label), Named entity recognition (NER) |
| **Video** | Video classification, Video frame object detection, Video frame object tracking |
| **3D Point Clouds** | Object detection, Object tracking, Semantic segmentation |

### Automated Data Labeling (Active Learning) 🔑
- Uses **active learning** — ML technique that identifies which data should be labeled by humans vs. auto-labeled
- Reduces cost by up to **70%** compared to fully human labeling
- **How it works**:
  1. Random sample sent to human workers
  2. Human-labeled data trains/validates an internal model
  3. Model runs inference on remaining unlabeled data with confidence scores
  4. High-confidence data → auto-labeled
  5. Low-confidence data → sent to human workers
  6. Repeat until dataset is fully labeled
- **Minimum**: 1,250 objects (recommended: 5,000+)
- **Supported task types for auto-labeling**:
  - Image classification (single label)
  - Semantic segmentation
  - Object detection (bounding box)
  - Text classification (single label)
- **Accuracy targets**:
  - Image/text classification: ≥ **95%** accuracy
  - Bounding box: mean IoU ≥ **0.6**
  - Semantic segmentation: mean IoU ≥ **0.7**

> ⚠️ **Exam Tip**: Automated labeling is NOT supported for streaming labeling jobs, custom task types, multi-label classification, video frames, or 3D point clouds.

### Streaming Labeling Jobs
- Perpetually running labeling jobs for real-time data labeling
- Send new data objects continuously via **Amazon SNS** or **S3**
- Uses **Amazon SQS** queue for managing labeling requests
- Auto-stops after **10 days** of inactivity
- Only supported via SageMaker API (not console)

### Custom Labeling Workflows
- Build custom worker UIs using **Crowd HTML Elements**
- Use **AWS Lambda** functions for:
  - **Pre-annotation**: Transform input data before presenting to workers
  - **Post-annotation**: Process/consolidate worker responses

### Label Verification & Adjustment
- Create follow-up jobs to verify or adjust labels from previous labeling jobs
- Supported for image, video frame, and 3D point cloud tasks

---

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "Company needs to label 100,000 images for training. How to reduce cost?" → Enable automated data labeling
- **Scenario**: "Data contains PII and needs labeling" → Use private workforce (NOT Mechanical Turk)
- **Scenario**: "Need continuous labeling as new data arrives" → Streaming labeling job
- **Feature comparison**: Ground Truth vs. A2I (labeling raw data vs. reviewing model predictions)

### Gotchas ⚠️
- Automated labeling requires **minimum 1,250 objects** (5,000+ recommended)
- Automated labeling only works with **4 specific built-in task types** (single-label image classification, semantic segmentation, bounding box, single-label text classification)
- Mechanical Turk **cannot** handle PII, video frames, or 3D point clouds
- Streaming jobs are **API-only** (no console support)
- Streaming jobs auto-expire after **10 days** idle
- Automated labeling incurs **additional SageMaker training and inference costs**

### Comparison Points

| Feature | Ground Truth | Amazon A2I | Mechanical Turk (standalone) |
|---------|-------------|------------|------------------------------|
| **Purpose** | Label raw training data | Review ML model predictions | General crowdsourcing |
| **ML Integration** | Active learning for auto-labeling | Human review loops for inference | None built-in |
| **Task Types** | Built-in + custom labeling UIs | Custom review UIs | Fully custom HITs |
| **Workforce** | MTurk, Private, Vendor | MTurk, Private, Vendor | MTurk only |
| **When to use** | Building training datasets | Validating model outputs | Non-AWS crowdsourcing tasks |

### Decision Trees
- ✅ **Use Ground Truth when**: You need labeled training data for ML models
- ✅ **Use Ground Truth automated labeling when**: Large dataset (5,000+), supported task type, want to reduce cost
- ✅ **Use streaming labeling when**: Continuous data flow needs real-time labeling
- ❌ **Don't use Ground Truth when**: You need to review/validate model predictions → Use A2I
- ❌ **Don't use Mechanical Turk when**: Data contains PII or requires video/3D annotation

---

## 6. Best Practices

### Security
- Use **private workforce** for sensitive, confidential, or PII data
- Run labeling jobs in **VPC mode** for network isolation
- Use **IAM roles** to control access to S3 data and labeling resources
- Mechanical Turk requires declaring data is **free of PII** via `FreeOfPersonallyIdentifiableInformation` flag

### Cost Optimization
- Enable **automated data labeling** to reduce human labeling costs by up to 70%
- Use **annotation consolidation** (multiple workers per object) only when quality demands it
- Set appropriate **task timeouts** to avoid paying for abandoned tasks
- Use larger datasets with automated labeling for better cost efficiency

### Performance
- Provide **clear, detailed worker instructions** with examples to improve label quality
- Use **annotation consolidation** (3-5 workers per object) for complex tasks
- Start with a **small pilot** labeling job to validate instructions and quality
- Use **label verification/adjustment** jobs to improve quality iteratively

### Operations
- Monitor labeling jobs via **CloudWatch** logs (`/aws/sagemaker/LabelingJobs`)
- Use **output manifest** files to track labeling progress and quality
- For streaming jobs, monitor **SQS queue depth** to control data flow

---

## 7. Hands-On Labs

### Quick Setup
1. Upload data to S3
2. Open SageMaker Console → Ground Truth → Labeling Jobs → Create
3. Specify S3 input/output paths
4. Select task type (e.g., Image Classification)
5. Choose workforce (Mechanical Turk for non-sensitive data)
6. Configure worker instructions
7. (Optional) Enable automated data labeling
8. Create labeling job

### CLI Example — Create Labeling Job
```bash
aws sagemaker create-labeling-job \
    --labeling-job-name my-labeling-job \
    --label-attribute-name category \
    --input-config '{
        "DataSource": {
            "S3DataSource": {
                "ManifestS3Uri": "s3://my-bucket/manifests/input.manifest"
            }
        }
    }' \
    --output-config '{
        "S3OutputPath": "s3://my-bucket/output/"
    }' \
    --role-arn arn:aws:iam::123456789012:role/SageMakerRole \
    --human-task-config '{
        "WorkteamArn": "arn:aws:sagemaker:us-east-1:394669845002:workteam/public-crowd/default",
        "UiConfig": {
            "UiTemplateS3Uri": "s3://my-bucket/template.html"
        },
        "PreHumanTaskLambdaArn": "arn:aws:lambda:us-east-1:123456789012:function:pre-label",
        "TaskTitle": "Classify Images",
        "TaskDescription": "Classify each image into a category",
        "NumberOfHumanWorkersPerDataObject": 3,
        "TaskTimeLimitInSeconds": 300,
        "AnnotationConsolidationConfig": {
            "AnnotationConsolidationLambdaArn": "arn:aws:lambda:us-east-1:123456789012:function:consolidate"
        }
    }'
```

### Python (boto3) — Create Labeling Job
```python
import boto3

sagemaker = boto3.client('sagemaker')

response = sagemaker.create_labeling_job(
    LabelingJobName='my-image-classification-job',
    LabelAttributeName='category',
    InputConfig={
        'DataSource': {
            'S3DataSource': {
                'ManifestS3Uri': 's3://my-bucket/manifests/input.manifest'
            }
        }
    },
    OutputConfig={
        'S3OutputPath': 's3://my-bucket/output/'
    },
    RoleArn='arn:aws:iam::123456789012:role/SageMakerRole',
    HumanTaskConfig={
        'WorkteamArn': 'arn:aws:sagemaker:us-east-1:394669845002:workteam/public-crowd/default',
        'UiConfig': {
            'UiTemplateS3Uri': 's3://my-bucket/template.html'
        },
        'PreHumanTaskLambdaArn': 'arn:aws:lambda:us-east-1:123456789012:function:pre-label',
        'TaskTitle': 'Classify Images',
        'TaskDescription': 'Classify each image into a category',
        'NumberOfHumanWorkersPerDataObject': 3,
        'TaskTimeLimitInSeconds': 300,
        'AnnotationConsolidationConfig': {
            'AnnotationConsolidationLambdaArn': 'arn:aws:lambda:us-east-1:123456789012:function:consolidate'
        }
    }
)
```

---

## 8. Sample Questions

### Question 1
A company has 50,000 product images that need to be classified into 10 categories to train a custom ML model. They want to minimize labeling costs. The data is not sensitive. Which approach should they use?

A) Use Amazon Rekognition to automatically classify all images
B) Use SageMaker Ground Truth with automated data labeling enabled and Amazon Mechanical Turk workforce
C) Hire a team of internal annotators and use SageMaker Canvas
D) Use Amazon Comprehend to classify the images

**Answer: B** ✅

Ground Truth with automated data labeling (active learning) is ideal for large datasets (50,000 images exceeds the 5,000 recommended minimum). It combines human labeling with ML-based auto-labeling to reduce costs by up to 70%. Since the data is not sensitive, Mechanical Turk is appropriate.
- A is wrong: Rekognition performs inference on images, it doesn't create labeled training datasets.
- C is wrong: SageMaker Canvas is for no-code ML model building, not data labeling.
- D is wrong: Comprehend is for text analysis, not image classification.

---

### Question 2
A healthcare company needs to label medical images containing patient information for training a diagnostic ML model. Which Ground Truth workforce option should they use?

A) Amazon Mechanical Turk
B) AWS Marketplace vendor workforce
C) Private workforce using Amazon Cognito
D) Any workforce type — Ground Truth encrypts all data automatically

**Answer: C** ✅

Medical images with patient information contain PII/PHI. Mechanical Turk explicitly prohibits PII data. A private workforce (employees/contractors authenticated via Amazon Cognito or OIDC) ensures data stays within the organization and complies with healthcare data regulations.
- A is wrong: Mechanical Turk cannot be used with PII or protected health information.
- B is wrong: While vendor workforces can handle PII per agreement, a private workforce is the most secure option for PHI.
- D is wrong: Encryption alone doesn't address the PII restriction on Mechanical Turk.

---

### Question 3
A data science team wants to continuously label new sensor data as it arrives from IoT devices. They need labels in near real-time. Which Ground Truth feature should they use?

A) Automated data labeling with active learning
B) Batch labeling job with a private workforce
C) Streaming labeling job
D) Amazon A2I human review workflow

**Answer: C** ✅

Streaming labeling jobs allow perpetually running labeling jobs where new data objects are sent to workers in real time. They use Amazon SQS for queue management and remain active until manually stopped or idle for 10 days.
- A is wrong: Automated labeling reduces cost but doesn't support real-time/continuous data ingestion.
- B is wrong: Batch jobs process a fixed dataset, not continuous streams.
- D is wrong: A2I is for reviewing model predictions, not labeling raw training data.

---

**Exam Tip**: Remember the three workforce types and their PII restrictions. Know that automated labeling uses **active learning**, requires **1,250+ objects** (5,000+ recommended), and only supports **4 built-in task types**. Understand the difference between Ground Truth (labeling raw data) and A2I (reviewing model predictions).

> **Sources**:
> - [SageMaker Ground Truth Overview](https://docs.aws.amazon.com/sagemaker/latest/dg/sms.html)
> - [Automate Data Labeling](https://docs.aws.amazon.com/sagemaker/latest/dg/sms-automated-labeling.html)
> - [Built-in Task Types](https://docs.aws.amazon.com/sagemaker/latest/dg/sms-task-types.html)
> - [Streaming Labeling Jobs](https://docs.aws.amazon.com/sagemaker/latest/dg/sms-streaming-labeling-job.html)
> - [Mechanical Turk Workforce](https://docs.aws.amazon.com/sagemaker/latest/dg/sms-workforce-management-public.html)
