# Amazon Mechanical Turk (MTurk)

## 1. Service/Topic Overview
- **Purpose**: Crowdsourcing marketplace that connects requesters with a global, on-demand human workforce to complete microtasks requiring human intelligence
- **Service Level**: Global
- **Key Use Cases**:
  - Data labeling and annotation for ML training datasets
  - Image classification, tagging, and moderation
  - Data verification, cleanup, and deduplication
  - Survey data collection and behavioral studies
  - Content moderation and sentiment analysis
- **Exam Weight**: Low-Medium
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — data preparation, labeling
  - Domain 3: Applications of Foundation Models (28%) — training data quality
  - Domain 4: Guidelines for Responsible AI (14%) — human-in-the-loop review
- **Task Statements**: Understanding workforce options for data labeling, human-in-the-loop workflows, and data quality management

## 2. Exam Keyword Mapping
- **Trigger Words**: "crowdsourcing", "human workers", "data labeling", "microtasks", "human intelligence tasks", "annotation", "public workforce", "human-in-the-loop"
- **Scenario Indicators**:
  - "Need to label thousands of images quickly using a large workforce"
  - "Require human judgment for subjective data categorization"
  - "Want to use a public workforce for non-sensitive data labeling"
  - "Need to collect multiple human opinions on the same data point"
- **Anti-patterns**:
  - ❌ Data contains PII, PHI, or confidential information → Use private or vendor workforce
  - ❌ Need automated labeling without humans → Use SageMaker Autopilot or automated data labeling
  - ❌ Need expert-level domain annotation → Use private workforce or vendor workforce
  - ❌ Video frame or 3D point cloud labeling → MTurk not supported, use private/vendor workforce
  - ❌ HIPAA-eligible workloads → Cannot use MTurk with Amazon A2I

## 3. Core Concepts

### Architectural Patterns
```
Requester → Creates HITs → MTurk Marketplace → Workers Complete Tasks → Results Returned
                                    ↕
                          SageMaker Ground Truth (integration)
                          Amazon Augmented AI / A2I (integration)
```

### Key Terminology
| Term | Definition |
|------|-----------|
| **Requester** | Company/person that posts tasks (HITs) for workers to perform |
| **Worker** | Person who completes the tasks in their web browser |
| **HIT (Human Intelligence Task)** | Single, self-contained microtask (e.g., "identify the color of the car") |
| **Assignment** | Created when a worker accepts a HIT; belongs exclusively to that worker |
| **Reward** | Money paid to workers for satisfactory work |
| **Bonus** | Additional payment for high-quality performance |
| **Qualification** | Attributes specifying which workers are eligible (location, past performance, etc.) |
| **Microtask** | Small, atomic task completable in a web browser |
| **HIT Type** | Group of HITs sharing the same attributes (title, description, reward) |

### Marketplaces
| Marketplace | Purpose |
|-------------|---------|
| **Production** | Real tasks with real monetary transactions |
| **Sandbox** | Development/testing environment — no money changes hands |

### Data Flow
```
Input Data (S3) → Define HIT → Publish to Marketplace → Workers Accept & Complete
    → Collect Assignments → Review/Approve/Reject → Pay Workers → Use Labeled Data
```

### Integration Points
| AWS Service | Integration |
|-------------|-------------|
| **SageMaker Ground Truth** | Primary workforce option for data labeling jobs |
| **Amazon Augmented AI (A2I)** | Human review workforce for AI predictions |
| **Amazon S3** | Input data storage (requires CORS configuration for images) |
| **IAM** | Access control for requester operations |

## 4. Key Features & Components

### Exam-Tested Features
- **Multiple Assignments per HIT**: Collect multiple worker responses for quality control and consensus
- **Annotation Consolidation**: Ground Truth combines multiple worker labels to improve quality
- **Worker Qualifications**: System-generated (location, approval rate) or custom qualifications
  - **Master Workers**: Pre-qualified workers with demonstrated high-quality performance
  - **Approval Rate**: System qualification based on historical acceptance rate
- **Auto-Approval Threshold**: Automatic approval of assignments after a set time period
- **Worker Blocks**: Ability to prevent specific workers from accepting your HITs

### Workforce Options (SageMaker Ground Truth Context)
| Workforce | Best For | PII Allowed? |
|-----------|----------|-------------|
| **Amazon Mechanical Turk** | Large-scale, non-sensitive labeling | ❌ No |
| **Private Workforce** | Sensitive/confidential data, domain expertise | ✅ Yes |
| **Vendor Workforce** | Specialized labeling, managed quality | ✅ Yes |

### Configuration Options
- **Reward Amount**: Price per task paid to workers
- **Assignment Duration**: Time workers have to complete a task
- **HIT Lifetime/Expiration**: How long a task remains available
- **Number of Workers per Object**: How many workers label each data item
- **Adult Content Declaration**: Flag for restricting worker access
- **PII-Free Declaration**: Required when using MTurk (jobs fail without it)

### API/SDK Highlights
- `CreateHIT` / `CreateHITType` — Create tasks
- `CreateHITWithHITType` — Create HIT using existing HIT Type for consistency
- `ApproveAssignment` / `RejectAssignment` — Review worker submissions
- `SendBonus` — Award additional payment
- `CreateQualificationType` — Define custom worker qualifications
- `ListAssignmentsForHIT` — Retrieve worker results

## 5. Exam Focus Areas

### Common Question Types
- **Scenario-based**: "Which workforce should you use for labeling non-sensitive image data at scale?" → MTurk
- **Feature comparison**: Differences between MTurk, private, and vendor workforces
- **Best practice**: When to use/not use MTurk based on data sensitivity
- **Integration**: How MTurk works with SageMaker Ground Truth and Amazon A2I

### Gotchas ⚠️
1. **PII Restriction**: MTurk **cannot** be used with data containing PII, PHI, or confidential information — jobs will **fail** if PII flag is not set
2. **CORS Requirement**: S3 buckets with image input data **must** have CORS configuration or HITs will fail
3. **HIPAA**: Cannot use MTurk with A2I for HIPAA-eligible service workloads (Textract, Rekognition)
4. **Video/3D Not Supported**: MTurk is not supported for Ground Truth video frame or 3D point cloud labeling jobs
5. **Billing**: MTurk workforce billing is included in Ground Truth/A2I billing — no separate MTurk account needed
6. **Worker Blocks**: Overuse of blocks can cause workers to avoid your tasks; prefer custom qualifications for skill-based filtering

### Comparison Points

#### MTurk vs. SageMaker Ground Truth Plus
| Feature | MTurk (via Ground Truth) | Ground Truth Plus |
|---------|-------------------------|-------------------|
| Workforce | Public crowd | Expert managed workforce |
| Setup | Self-service | Turn-key, AWS-managed |
| Quality | Requester manages | AWS manages quality control |
| Cost | Pay per task | Up to 40% cost reduction |
| PII | Not allowed | Supported |

#### MTurk vs. Amazon A2I
| Feature | MTurk (standalone) | MTurk via A2I |
|---------|-------------------|---------------|
| Purpose | General crowdsourcing | Human review of ML predictions |
| Trigger | Manual HIT creation | Automated based on confidence thresholds |
| Integration | Direct API | Built into SageMaker workflow |

### Decision Trees
```
Need human data labeling?
├── Data contains PII/PHI/confidential info?
│   ├── YES → Use Private or Vendor Workforce
│   └── NO → Is it video frame or 3D point cloud?
│       ├── YES → Use Private or Vendor Workforce
│       └── NO → Need fastest turnaround at scale?
│           ├── YES → ✅ Amazon Mechanical Turk
│           └── NO → Need expert-managed quality?
│               ├── YES → Ground Truth Plus or Vendor
│               └── NO → ✅ Amazon Mechanical Turk
```

## 6. Best Practices

### Security
- **Never share** PII, PHI, or confidential information with MTurk workforce
- Declare `FreeOfPersonallyIdentifiableInformation` flag — required for MTurk jobs
- Use `FreeOfAdultContent` flag when applicable
- Use IAM roles for Ground Truth labeling job permissions
- Configure S3 bucket policies to restrict access to input/output data

### Cost Optimization
- Set appropriate reward amounts based on task complexity and completion time
- Use **automated data labeling** (Ground Truth) to reduce the number of items requiring human labeling
- Start with small batches to validate task design before scaling
- Use annotation consolidation to balance quality vs. number of workers per object
- Maintain consistent HIT Type attributes to avoid creating unnecessary HIT groups

### Performance
- Design efficient task interfaces to reduce worker time and improve accuracy
- Use clear, non-technical instructions (design for non-expert audience)
- Include optional feedback fields for worker insights
- Test HITs in sandbox before production deployment
- Specify links to open in new tabs (`target="_blank"`) to preserve worker interface

### Operations
- Use sandbox marketplace for development and testing
- Start with small HIT batches, then scale after validating results
- Monitor approval/rejection rates to maintain workforce quality
- Use qualifications over blocks for skill-based worker filtering
- Provide clear rejection reasons to help workers improve

## 7. Hands-On Labs

### Quick Setup (via SageMaker Ground Truth Console)
1. Open SageMaker Console → Ground Truth → Labeling Jobs
2. Create labeling job → specify S3 input/output locations
3. Select task type (e.g., Image Classification)
4. Choose **Amazon Mechanical Turk** as workforce
5. Set price per task, number of workers per object
6. Confirm data is free of PII → Create job

### CLI Example — Create Labeling Job with MTurk
```bash
aws sagemaker create-labeling-job \
  --labeling-job-name my-labeling-job \
  --label-attribute-name category \
  --input-config '{
    "DataSource": {
      "S3DataSource": {
        "ManifestS3Uri": "s3://my-bucket/manifests/input.manifest"
      }
    },
    "DataAttributes": {
      "ContentClassifiers": ["FreeOfPersonallyIdentifiableInformation", "FreeOfAdultContent"]
    }
  }' \
  --output-config '{
    "S3OutputPath": "s3://my-bucket/output/"
  }' \
  --role-arn arn:aws:iam::<account-id>:role/SageMakerRole \
  --human-task-config '{
    "WorkteamArn": "arn:aws:sagemaker:us-east-1:394669845002:workteam/public-crowd/default",
    "UiConfig": {
      "UiTemplateS3Uri": "s3://my-bucket/template.html"
    },
    "PreHumanTaskLambdaArn": "arn:aws:lambda:us-east-1:432418664414:function:PRE-ImageMultiClass",
    "TaskTitle": "Classify Images",
    "TaskDescription": "Select the correct category for each image",
    "NumberOfHumanWorkersPerDataObject": 3,
    "TaskTimeLimitInSeconds": 300,
    "MaxConcurrentTaskCount": 1000,
    "AnnotationConsolidationConfig": {
      "AnnotationConsolidationLambdaArn": "arn:aws:lambda:us-east-1:432418664414:function:ACS-ImageMultiClass"
    }
  }'
```

### Practical Exercises
1. **Image Classification**: Create a Ground Truth labeling job using MTurk to classify vehicle images by type
2. **Sentiment Labeling**: Use MTurk to label customer review text as positive/negative/neutral for training a Comprehend custom model
3. **A2I Human Review**: Set up an A2I workflow with MTurk to review low-confidence Textract document extractions (non-PII data only)

## 8. Sample Questions

### Question 1
A company wants to build a training dataset by labeling 50,000 product images. The images do not contain any sensitive information. They need the labeling completed as quickly as possible. Which workforce option should they use with Amazon SageMaker Ground Truth?

A) Amazon Mechanical Turk workforce
B) Private workforce using company employees
C) AWS Marketplace vendor workforce
D) SageMaker Autopilot

**Correct Answer: A**
- ✅ A: MTurk provides the largest workforce, available 24/7 globally, offering the fastest turnaround. Data is non-sensitive, so MTurk is appropriate.
- ❌ B: Private workforce is slower and better suited for sensitive/confidential data.
- ❌ C: Vendor workforce is more expensive and better for specialized tasks requiring managed quality.
- ❌ D: Autopilot is for automated model building, not data labeling.

---

### Question 2
A healthcare company needs to label medical records for an ML model. The data contains protected health information (PHI). They want to use Amazon SageMaker Ground Truth. Which workforce option is appropriate?

A) Amazon Mechanical Turk workforce
B) Private workforce within the organization
C) Amazon Mechanical Turk with Master Workers qualification
D) Any workforce with encryption enabled

**Correct Answer: B**
- ✅ B: Private workforce is the correct choice for PHI/PII data. The company controls who has access.
- ❌ A: MTurk explicitly prohibits sharing PHI, PII, or confidential information. The labeling job will fail.
- ❌ C: Even Master Workers on MTurk cannot access PHI — the restriction applies to the entire MTurk workforce.
- ❌ D: Encryption does not change the workforce restriction. MTurk workers would still see the data.

---

### Question 3
A data scientist creates a SageMaker Ground Truth labeling job using Amazon Mechanical Turk. The job uses images stored in Amazon S3 but the HITs are failing. What is the most likely cause?

A) The S3 bucket does not have server-side encryption enabled
B) The S3 bucket is missing a CORS configuration
C) The IAM role does not have MTurk permissions
D) The images exceed the maximum file size for MTurk

**Correct Answer: B**
- ✅ B: AWS documentation explicitly states that S3 buckets containing image input data must have a CORS configuration, or HITs will fail.
- ❌ A: Encryption is a best practice but not the cause of HIT failures.
- ❌ C: IAM role permissions affect the labeling job creation, not individual HIT failures.
- ❌ D: File size limits exist but are not the most common cause of this specific failure pattern.

---

**Exam Tip**: The AIF-C01 exam frequently tests your understanding of **when to use MTurk vs. private/vendor workforces**. The key differentiator is **data sensitivity** — if the question mentions PII, PHI, confidential data, or HIPAA, MTurk is always the wrong answer. For non-sensitive data at scale, MTurk is the fastest and most cost-effective option.
