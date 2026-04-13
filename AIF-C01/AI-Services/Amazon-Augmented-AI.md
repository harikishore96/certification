# Amazon Augmented AI (Amazon A2I)

## 1. Service/Topic Overview
- **Purpose**: Amazon A2I enables human review of ML predictions by providing managed workflows that route low-confidence or sampled predictions to human reviewers
- **Service Level**: Regional (resources must be in the same AWS Region as the integrated service)
- **Key Use Cases**:
  - Human review of low-confidence ML predictions
  - Content moderation review (images flagged by Rekognition)
  - Document extraction review (key-value pairs from Textract)
  - Auditing ML model predictions via random sampling
  - Custom human review loops for any ML workflow
- **Exam Weight**: Medium
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — human-in-the-loop concepts
  - Domain 3: Applications of Foundation Models (28%) — integrating human review into ML pipelines
  - Domain 4: Guidelines for Responsible AI (14%) — human oversight, quality assurance
- **Task Statements**:
  - Understand when and why to incorporate human review into ML workflows
  - Identify which AWS services integrate with A2I
  - Recognize human-in-the-loop as a responsible AI practice

## 2. Exam Keyword Mapping
- **Trigger Words**: "human review", "human-in-the-loop", "low-confidence predictions", "human oversight", "review workflow", "manual review of ML output", "audit ML predictions", "confidence threshold review"
- **Scenario Indicators**:
  - "A model returns low-confidence predictions that need human verification"
  - "Regulatory requirements mandate human review of automated decisions"
  - "Need to randomly sample and audit ML predictions"
  - "Content moderation requires human escalation for borderline cases"
  - "Document extraction results need human validation for critical fields"
- **Anti-patterns**:
  - ❌ Don't confuse with SageMaker Ground Truth (Ground Truth = data labeling; A2I = reviewing ML predictions)
  - ❌ Don't confuse with Amazon Mechanical Turk directly (MTurk is a workforce option within A2I, not the workflow service itself)
  - ❌ Not for training data creation — use SageMaker Ground Truth instead
  - ❌ Not for model retraining — A2I provides review output, but retraining is a separate step

## 3. Core Concepts

### Architectural Patterns
```
ML Model Prediction → Confidence Check → Below Threshold? → A2I Human Loop → Human Review → Output to S3
                                          ↓ (Above Threshold)
                                     Direct to Application
```

### Core Components (5 Key Components)

| Component | Description |
|-----------|-------------|
| **Task Types** | Built-in (Textract, Rekognition) or Custom task types |
| **Human Review Workflow (Flow Definition)** | Specifies work team, worker UI template, activation conditions, and output S3 bucket |
| **Work Team** | Group of human reviewers — Amazon Mechanical Turk, vendor-managed, or private workforce |
| **Worker Task Template** | Defines the human task UI — displays data and provides interactive review tools |
| **Human Loop** | A single human review job; created automatically (built-in) or via API (custom) |

### Task Types

| Type | Trigger | How Loop Starts |
|------|---------|-----------------|
| **Built-in: Amazon Textract** | `AnalyzeDocument` API — key-value pair extraction | Automatically when activation conditions are met |
| **Built-in: Amazon Rekognition** | `DetectModerationLabels` API — image moderation | Automatically when activation conditions are met |
| **Custom** | Any ML workflow (Comprehend, Transcribe, Translate, SageMaker endpoints, etc.) | Manually via `StartHumanLoop` API |

### Activation Conditions (Built-in Task Types Only)
- **Confidence Threshold**: Route to human review when ML confidence is below/within a specified range
- **Random Sampling**: Send a percentage of predictions for human review regardless of confidence
- ⚠️ Custom task types do NOT support activation conditions — you must define your own logic and call `StartHumanLoop`

### Data Flow
```
Input (S3) → AWS AI Service (Textract/Rekognition/Custom) → Prediction
  → Activation Conditions Evaluated
    → If met: Human Loop Created → Worker Task UI → Human Review → Output (S3)
    → If not met: Prediction passed directly to application
```

### Output Data Path
```
s3://output-bucket/flow-definition-name/YYYY/MM/DD/hh/mm/ss/human-loop-name/output.json
```

Output includes:
- `humanAnswers` — worker responses
- `inputContent` — original AI service request and response
- `humanTaskActivationConditionResults` — why the human loop was triggered
- Worker metadata (for private workforce — includes Cognito identity)

### Integration Points

| AWS Service | Integration Type | Use Case |
|-------------|-----------------|----------|
| **Amazon Textract** | Built-in | Review key-value pairs from document extraction |
| **Amazon Rekognition** | Built-in | Review content moderation labels |
| **Amazon Comprehend** | Custom | Review sentiment analysis, entity detection |
| **Amazon Transcribe** | Custom | Review speech-to-text transcriptions |
| **Amazon Translate** | Custom | Review low-confidence translations |
| **Amazon SageMaker** | Custom | Review real-time endpoint inferences |
| **Amazon S3** | Input/Output | Store input data and human review results |
| **Amazon CloudWatch Events / EventBridge** | Monitoring | Notifications when human review is complete |
| **Amazon Cognito** | Workforce Auth | Authenticate private workforce workers |

## 4. Key Features & Components

### Exam-Tested Features
- **Built-in Workflows**: Pre-configured for Textract and Rekognition — no custom code needed for activation conditions
- **Custom Workflows**: Integrate with ANY ML model or AWS AI service via `StartHumanLoop` API
- **Flexible Workforce Options**:
  - **Amazon Mechanical Turk** — public crowdsourced workforce
  - **Vendor-managed** — third-party workforce providers from AWS Marketplace
  - **Private workforce** — your own employees/contractors (via Amazon Cognito or OIDC)
- **Multiple Reviewers**: Configure multiple workers per object to increase review accuracy
- **Worker Task Templates**: Customizable UI for human reviewers with instructions and interactive tools
- **Confidence-Based Routing**: Set thresholds to only route uncertain predictions
- **Random Sampling**: Audit a percentage of all predictions for quality assurance

### Configuration Options
- **Flow Definition**: Created via SageMaker console or `CreateFlowDefinition` API
- **Human Loop Config**: Passed as parameter to built-in service API calls (e.g., `DetectModerationLabels`)
- **Activation Conditions**: JSON-based rules for confidence thresholds or sampling percentages
- **Worker Count**: Number of workers assigned per review task (configurable)

### Key API Operations

| API | Service | Purpose |
|-----|---------|---------|
| `CreateFlowDefinition` | SageMaker API | Create a human review workflow |
| `StartHumanLoop` | A2I Runtime API | Start a human loop (custom task types) |
| `StopHumanLoop` | A2I Runtime API | Stop an in-progress human loop |
| `DescribeHumanLoop` | A2I Runtime API | Get status of a human loop |
| `ListHumanLoops` | A2I Runtime API | List all human loops for a flow definition |
| `DeleteHumanLoop` | A2I Runtime API | Delete a human loop |

### Managed Policies

| Policy | Scope |
|--------|-------|
| `AmazonAugmentedAIFullAccess` | Full access to A2I operations only |
| `AmazonAugmentedAIIntegratedAPIAccess` | A2I + Textract + Rekognition API access |

## 5. Exam Focus Areas

### Common Question Types
- **Scenario-based**: "A company needs human review when document extraction confidence is below 90%. Which service should they use?" → Amazon A2I with Textract
- **Feature comparison**: "What is the difference between SageMaker Ground Truth and Amazon A2I?" → Ground Truth = labeling training data; A2I = reviewing ML predictions
- **Best practice**: "How can a company ensure responsible AI by auditing ML predictions?" → Amazon A2I with random sampling
- **Integration**: "Which services have built-in A2I integration?" → Textract and Rekognition only

### Gotchas
- ⚠️ **Only 2 built-in task types**: Textract and Rekognition. Everything else (Comprehend, Transcribe, Translate, SageMaker) requires custom task type
- ⚠️ **Custom task types have NO automatic activation conditions** — you must implement your own logic to call `StartHumanLoop`
- ⚠️ **Regional service**: A2I resources must be in the same AWS Region as the integrated service
- ⚠️ **CORS required**: S3 buckets with image input data must have CORS headers configured
- ⚠️ **A2I is part of SageMaker**: Managed through the SageMaker console and APIs
- ⚠️ **Private workforce must be pre-created** in the same Region before creating a flow definition

### Comparison Points

| Feature | Amazon A2I | SageMaker Ground Truth |
|---------|-----------|----------------------|
| **Purpose** | Review ML predictions | Label training data |
| **When Used** | Post-inference (production) | Pre-training (data preparation) |
| **Input** | ML model predictions | Raw unlabeled data |
| **Output** | Reviewed/corrected predictions | Labeled training dataset |
| **Trigger** | Confidence thresholds or sampling | Manual job creation |

| Feature | Amazon A2I | Amazon Mechanical Turk |
|---------|-----------|----------------------|
| **Level** | Managed workflow service | Crowdsourcing marketplace |
| **Relationship** | A2I can USE MTurk as a workforce | MTurk provides the workers |
| **Automation** | Automatic routing based on conditions | Manual task creation |
| **ML Integration** | Native AWS AI service integration | General-purpose tasks |

### Decision Trees
- ✅ **Use A2I when**: You need human review of ML predictions in production
- ✅ **Use A2I when**: Regulatory/compliance requires human oversight of automated decisions
- ✅ **Use A2I when**: You want to audit ML model quality via random sampling
- ✅ **Use A2I when**: Content moderation needs human escalation for borderline cases
- ❌ **Don't use A2I when**: You need to label training data → Use SageMaker Ground Truth
- ❌ **Don't use A2I when**: You need to retrain a model → Use SageMaker Training
- ❌ **Don't use A2I when**: You need real-time automated content filtering → Use Rekognition directly

## 6. Best Practices

### Security
- Use **IAM roles** with least-privilege access for flow definitions
- Attach `AmazonAugmentedAIFullAccess` or `AmazonAugmentedAIIntegratedAPIAccess` managed policies
- Flow definition IAM role needs: `s3:GetObject` (input bucket) + `s3:PutObject` (output bucket) + SageMaker trust policy
- Use **AWS KMS** encrypted S3 buckets for sensitive input/output data
- Configure **CORS headers** on S3 buckets containing image input data
- Use **private workforce** (not MTurk) for sensitive/confidential data
- Set `ContentClassifiers` (e.g., `FreeOfPersonallyIdentifiableInformation`) when using public workforce

### Cost Optimization
- **Pricing**: Pay per human review object — no upfront costs
- Use **confidence thresholds** to only route truly uncertain predictions (avoid unnecessary human reviews)
- Optimize **sampling percentages** — start low and increase based on quality needs
- Use **private workforce** to control costs vs. MTurk per-task pricing
- Monitor human loop volumes with CloudWatch to identify cost trends

### Performance
- Set appropriate **confidence thresholds** — too low = missed errors; too high = excessive human reviews
- Use **multiple reviewers per object** for critical decisions to increase accuracy
- Provide **clear worker instructions** in task templates to reduce review time and errors
- Monitor **human-model agreement rates** to calibrate thresholds over time

### Operations
- Use **Amazon EventBridge / CloudWatch Events** to get notified when human reviews complete
- Monitor human loops with `ListHumanLoops` and `DescribeHumanLoop` APIs
- Store output in organized S3 paths for downstream processing
- Use A2I output data to **incrementally retrain models** and improve future predictions
- Implement **feedback loops**: A2I output → retrain model → improved predictions → fewer human reviews

## 7. Hands-On Labs

### Quick Setup
1. Open SageMaker console → Augmented AI → Human review workflows
2. Create a flow definition with task type, work team, and worker template
3. Integrate with Textract or Rekognition API call by passing the flow definition ARN

### Practical Exercises

**Exercise 1: Built-in Textract Integration**
1. Create a flow definition for Textract key-value pair extraction
2. Set confidence threshold to 90%
3. Call `AnalyzeDocument` with `HumanLoopConfig`
4. Review the human loop output in S3

**Exercise 2: Built-in Rekognition Integration**
1. Create a flow definition for image moderation
2. Set activation condition: confidence < 98% for "Suggestive" label
3. Call `DetectModerationLabels` with `HumanLoopConfig`
4. Monitor human loop status via EventBridge

**Exercise 3: Custom Task Type**
1. Create a custom worker task template
2. Create a flow definition with custom task type
3. Call `StartHumanLoop` from your application when confidence is low
4. Process human review results from S3

### CLI Commands

```bash
# Create a flow definition
aws sagemaker create-flow-definition \
  --flow-definition-name my-review-workflow \
  --role-arn arn:aws:iam::123456789012:role/A2IRole \
  --human-loop-config '{
    "WorkteamArn": "arn:aws:sagemaker:us-east-1:123456789012:workteam/private-crowd/my-team",
    "HumanTaskUiArn": "arn:aws:sagemaker:us-east-1:123456789012:human-task-ui/my-template",
    "TaskCount": 1,
    "TaskTitle": "Review Document",
    "TaskDescription": "Review extracted key-value pairs"
  }' \
  --output-config '{"S3OutputPath": "s3://my-output-bucket/a2i-output"}'

# Start a human loop (custom task type)
aws sagemaker-a2i-runtime start-human-loop \
  --human-loop-name my-loop-001 \
  --flow-definition-arn arn:aws:sagemaker:us-east-1:123456789012:flow-definition/my-review-workflow \
  --human-loop-input '{"InputContent": "{\"text\": \"Review this prediction\"}"}'

# Check human loop status
aws sagemaker-a2i-runtime describe-human-loop \
  --human-loop-name my-loop-001

# List human loops
aws sagemaker-a2i-runtime list-human-loops \
  --flow-definition-arn arn:aws:sagemaker:us-east-1:123456789012:flow-definition/my-review-workflow
```

### Python SDK Example

```python
import boto3

# Create A2I Runtime client
a2i_runtime = boto3.client('sagemaker-a2i-runtime')

# Start a human loop (custom task type)
response = a2i_runtime.start_human_loop(
    HumanLoopName='review-loop-001',
    FlowDefinitionArn='arn:aws:sagemaker:us-east-1:123456789012:flow-definition/my-workflow',
    HumanLoopInput={
        'InputContent': '{"prediction": "negative", "confidence": 0.62, "text": "Sample text"}'
    }
)

# Check status
status = a2i_runtime.describe_human_loop(
    HumanLoopName='review-loop-001'
)
print(f"Status: {status['HumanLoopStatus']}")
```

## 8. Sample Questions

### Question 1
**A healthcare company uses Amazon Textract to extract patient information from scanned forms. Due to regulatory requirements, any extraction with confidence below 95% must be reviewed by a human. Which solution meets this requirement with the LEAST operational overhead?**

A) Build a custom application that monitors Textract confidence scores and sends low-confidence results to a review queue  
B) Use Amazon A2I with the built-in Amazon Textract task type and set a confidence threshold of 95%  
C) Use Amazon SageMaker Ground Truth to label all extracted documents  
D) Use Amazon Mechanical Turk directly to review all Textract outputs  

**Correct Answer: B**
- ✅ B is correct: A2I has a built-in Textract integration that automatically creates human loops when confidence is below the threshold — minimal operational overhead
- ❌ A is wrong: Building a custom application adds significant operational overhead
- ❌ C is wrong: Ground Truth is for labeling training data, not reviewing predictions
- ❌ D is wrong: MTurk alone doesn't provide automated confidence-based routing

---

### Question 2
**A company wants to add human review to their custom sentiment analysis model deployed on a SageMaker endpoint. When the model's confidence is below 70%, predictions should be sent for human review. How should they configure Amazon A2I?**

A) Use the built-in Amazon Comprehend task type with a 70% confidence threshold  
B) Create a custom task type, implement confidence checking logic in the application, and call `StartHumanLoop` when confidence is below 70%  
C) Use the built-in SageMaker task type with activation conditions set to 70%  
D) Configure Amazon A2I activation conditions directly on the SageMaker endpoint  

**Correct Answer: B**
- ✅ B is correct: Custom SageMaker models require the custom task type. Activation conditions are NOT available for custom types — you must implement the confidence check yourself and call `StartHumanLoop`
- ❌ A is wrong: There is no built-in Comprehend task type — only Textract and Rekognition are built-in
- ❌ C is wrong: There is no built-in SageMaker task type
- ❌ D is wrong: A2I activation conditions cannot be configured directly on SageMaker endpoints

---

### Question 3
**A media company uses Amazon Rekognition for content moderation. They want to audit 10% of all images randomly, regardless of confidence scores, AND review all images where the confidence for inappropriate content is between 50-90%. Which Amazon A2I feature supports this?**

A) Create two separate flow definitions — one for sampling and one for confidence thresholds  
B) Use a single flow definition with both `Sampling` and `ModerationLabelConfidenceCheck` activation conditions  
C) Use a custom task type to implement both conditions  
D) This is not possible with Amazon A2I  

**Correct Answer: B**
- ✅ B is correct: A2I's built-in Rekognition integration supports both `Sampling` (random percentage) and `ModerationLabelConfidenceCheck` (confidence range) activation conditions in a single flow definition
- ❌ A is wrong: You don't need separate flow definitions — both conditions can be in one
- ❌ C is wrong: The built-in Rekognition task type already supports this natively
- ❌ D is wrong: This is a core feature of A2I

---

**Exam Tip**: Remember that Amazon A2I is the go-to answer whenever a question mentions "human review of ML predictions" or "human-in-the-loop" for production ML systems. The key distinction is: A2I reviews predictions (post-inference), while Ground Truth labels data (pre-training). Also remember only Textract and Rekognition have built-in task types — everything else is custom.
