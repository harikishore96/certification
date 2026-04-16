# Amazon SageMaker Pipelines

## 1. Service/Topic Overview
- **Purpose**: ML workflow orchestration service that enables you to define, automate, and manage end-to-end ML workflows as a series of interconnected steps in a directed acyclic graph (DAG)
- **Service Level**: Regional
- **Key Use Cases**:
  - Automating end-to-end ML workflows (data processing → training → evaluation → deployment)
  - Implementing CI/CD for ML models (MLOps)
  - Enforcing quality gates with conditional logic before model registration
  - Running bias/drift checks as part of automated pipelines
  - Reproducible, auditable ML experiments
- **Exam Weight**: Medium-High
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — ML development lifecycle
  - Domain 3: Applications of Foundation Models (28%) — model deployment strategies
  - Domain 5: Security, Compliance, and Governance (14%) — MLOps governance
- **Task Statements**: Task 1.3 (ML Development Lifecycle), Task 3.3 (Training and fine-tuning)

## 2. Exam Keyword Mapping
- **Trigger Words**: "automate ML workflow", "orchestrate ML pipeline", "CI/CD for ML", "MLOps", "DAG", "pipeline steps", "model approval", "conditional deployment", "reproducible ML"
- **Scenario Indicators**:
  - "Automate the process from data preparation to model deployment"
  - "Only deploy the model if accuracy exceeds a threshold"
  - "Run bias checks before registering a model"
  - "Orchestrate training, evaluation, and deployment"
- **Anti-patterns**:
  - Simple one-off training jobs → use SageMaker Training directly
  - General-purpose workflow orchestration (non-ML) → use AWS Step Functions
  - Data pipeline ETL only → use AWS Glue

## 3. Core Concepts

### What is SageMaker Pipelines?
SageMaker Pipelines is a purpose-built CI/CD service for ML. It defines workflows as a **directed acyclic graph (DAG)** in JSON format. Each node in the DAG is a **step** that performs a specific ML task.

### Architectural Pattern
```
Pipeline Definition (JSON/Python SDK)
    │
    ▼
┌─────────────┐    ┌─────────────┐    ┌──────────────┐    ┌──────────────┐
│ Processing   │───▶│  Training   │───▶│  Evaluation  │───▶│  Condition   │
│ Step         │    │  Step       │    │  Step        │    │  Step        │
└─────────────┘    └─────────────┘    └──────────────┘    └──┬───────┬───┘
                                                        true │       │ false
                                                    ┌────────▼┐  ┌───▼──────┐
                                                    │ Register │  │  Fail    │
                                                    │ Model    │  │  Step    │
                                                    └──────────┘  └──────────┘
```

### Key Components
- **Pipeline**: The top-level object containing steps, parameters, and metadata
- **Steps**: Individual units of work (processing, training, etc.)
- **Parameters**: Variables with defaults that can be overridden per execution (String, Integer, Float, Boolean)
- **Properties**: Step outputs that can be referenced by downstream steps (resolved at runtime)
- **Conditions**: Boolean expressions that control branching logic
- **Execution Variables**: Runtime variables like `PIPELINE_EXECUTION_ID`
- **Property Files**: JSON files produced by steps that can be queried using JsonGet
- **Step Caching**: Reuse results from previous runs if inputs haven't changed

### Integration Points
- **S3**: Input/output data storage for all steps
- **SageMaker Model Registry**: Model package registration and versioning
- **SageMaker Experiments**: Automatic experiment tracking (pipeline = experiment, execution = trial)
- **SageMaker Clarify**: Bias and explainability checks via ClarifyCheck step
- **SageMaker Model Monitor**: Quality baselines via QualityCheck step
- **Amazon SQS**: Callback step integration for custom processes
- **AWS Lambda**: Lightweight compute via Lambda step
- **Amazon EMR**: Big data processing via EMR step
- **Amazon EventBridge**: Event-driven pipeline triggers
- **CloudWatch**: Monitoring and logging

## 4. Pipeline Step Types (Complete Reference)

SageMaker Pipelines supports **18 step types**:

### Data Preparation Steps

| Step | SDK Class | Purpose |
|------|-----------|---------|
| **Processing** | `ProcessingStep` | Data preprocessing, feature engineering, model evaluation |
| **EMR** | `EMRStep` | Big data processing on Amazon EMR clusters |

- **ProcessingStep**: Runs a SageMaker Processing job. Supports SKLearn, PySpark, or custom containers. Used for data prep AND model evaluation.
- **EMRStep**: Runs jobs on existing or new EMR clusters. Supports EMR managed and EMR Serverless modes.

### Model Training Steps

| Step | SDK Class | Purpose |
|------|-----------|---------|
| **Training** | `TrainingStep` | Train a model using an estimator |
| **Tuning** | `TuningStep` | Hyperparameter optimization (HPO) — runs multiple training jobs |
| **AutoML** | `AutoMLStep` | Automated model training via SageMaker Autopilot (ensembling mode only) |

- **TrainingStep**: Requires an estimator + training/validation data inputs. Outputs model artifacts to S3.
- **TuningStep**: Wraps `HyperparameterTuner`. Use `get_top_model_s3_uri(top_k=0)` to get the best model. Up to top 50 models ranked by objective.
- **AutoMLStep**: Uses Autopilot. Use `get_best_auto_ml_model()` to retrieve the best candidate.

### Model Management Steps

| Step | SDK Class | Purpose |
|------|-----------|---------|
| **Model** | `ModelStep` | Create or register a model (recommended) |
| **Create Model** | `CreateModelStep` | Create a SageMaker model (legacy) |
| **Register Model** | `RegisterModel` | Register model to Model Registry (legacy) |

- **ModelStep** (recommended since SDK v2.90.0): Unified step for both creating and registering models. Replaces CreateModelStep and RegisterModel.
- **CreateModelStep** (legacy): Creates a SageMaker model from training artifacts.
- **RegisterModel** (legacy): Registers a model package in the SageMaker Model Registry.

### Model Deployment Steps

| Step | SDK Class | Purpose |
|------|-----------|---------|
| **Deploy Model (Endpoint)** | Pipeline Designer only | Deploy model to a real-time endpoint |
| **Transform** | `TransformStep` | Batch inference on entire datasets |

- **Deploy Model (Endpoint)**: Available in Pipeline Designer drag-and-drop UI. Creates endpoint for real-time inference.
- **TransformStep**: Runs batch transform jobs. Requires a Transformer and input data.

### Quality & Compliance Steps

| Step | SDK Class | Purpose |
|------|-----------|---------|
| **ClarifyCheck** | `ClarifyCheckStep` | Bias analysis and model explainability drift checks |
| **QualityCheck** | `QualityCheckStep` | Data quality and model quality drift checks |

- **ClarifyCheckStep**: Runs one check type per step instance:
  - `DataBiasCheckConfig` — check data for bias
  - `ModelBiasCheckConfig` — check model predictions for bias
  - `ModelExplainabilityCheckConfig` — check feature attribution drift
  - Uses SageMaker Clarify prebuilt container
  - Key flags: `skip_check` and `register_new_baseline`

- **QualityCheckStep**: Runs one check type per step instance:
  - `DataQualityCheckConfig` — statistical properties of input data
  - `ModelQualityCheckConfig` — model prediction accuracy
  - Uses SageMaker Model Monitor prebuilt container
  - Key flags: `skip_check` and `register_new_baseline`

Both check steps can pull baselines from the Model Registry via `model_package_group_name`.

### Control Flow Steps

| Step | SDK Class | Purpose |
|------|-----------|---------|
| **Condition** | `ConditionStep` | Branch pipeline based on conditions (if/else) |
| **Fail** | `FailStep` | Stop pipeline execution with error message |

- **ConditionStep**: Evaluates conditions against step properties. Supports: `ConditionEquals`, `ConditionGreaterThan`, `ConditionLessThanOrEqualTo`, etc. Uses `JsonGet` to extract values from property files.
  - ⚠️ No nested condition steps allowed
  - ⚠️ Cannot use identical steps in both if/else branches
- **FailStep**: Terminates pipeline with custom error message. Always the last step. Cannot be retried. Cannot be in `DependsOn` list.

### Integration Steps

| Step | SDK Class | Purpose |
|------|-----------|---------|
| **Callback** | `CallbackStep` | Integrate external processes via Amazon SQS |
| **Lambda** | `LambdaStep` | Run AWS Lambda functions |
| **Notebook Job** | `NotebookJobStep` | Run Jupyter notebooks non-interactively |
| **Execute Code** | Pipeline Designer | Run custom Python code (functions, scripts, notebooks) |

- **CallbackStep**: Sends message to SQS queue → external process runs → calls `SendPipelineExecutionStepSuccess` or `SendPipelineExecutionStepFailure` with token. Pipeline waits for response.
- **LambdaStep**: Invokes existing or creates new Lambda function. Max timeout: 10 minutes. Pipeline can't stop while Lambda is running.
- **NotebookJobStep**: Runs SageMaker Notebook Jobs as pipeline steps.
- **Execute Code**: Drag-and-drop UI step. Uploads Python function/script/notebook to S3, runs via SageMaker training job.

### Custom Steps

| Step | Decorator | Purpose |
|------|-----------|---------|
| **@step** | `@step` decorator | Convert local Python functions to pipeline steps |

- **@step decorator**: Annotate any Python function to convert it into a pipeline step. Lift-and-shift local ML code into pipelines.

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "You need to automate an ML workflow that processes data, trains a model, and only deploys if accuracy > 90%" → ProcessingStep → TrainingStep → ProcessingStep (eval) → ConditionStep → RegisterModel
- **Feature comparison**: "What step type would you use to check for data bias?" → ClarifyCheckStep with DataBiasCheckConfig
- **Best practice**: "How to integrate a third-party approval process?" → CallbackStep with SQS
- **MLOps**: "How to implement CI/CD for ML models?" → SageMaker Pipelines + Model Registry + ConditionStep

### Gotchas
- ⚠️ **ConditionStep** cannot be nested — no condition inside another condition
- ⚠️ **ClarifyCheck** and **QualityCheck** each run only ONE check type per step instance
- ⚠️ **CallbackStep** — pipeline doesn't stop while callback is running; must call Success/Failure API
- ⚠️ **LambdaStep** — max 10 min timeout; Lambda continues running even if step fails
- ⚠️ **FailStep** — cannot be retried, cannot be in DependsOn list
- ⚠️ **ModelStep** is the recommended replacement for both CreateModelStep and RegisterModel (since SDK v2.90.0)
- ⚠️ **Step caching** — reuses previous results if cache key attributes haven't changed (within timeout period)
- ⚠️ A step can only appear once — either in the pipeline's step list OR in a condition's if/else list, not both

### Comparison Points

| Feature | SageMaker Pipelines | AWS Step Functions |
|---------|--------------------|--------------------|
| **Purpose** | ML-specific workflow orchestration | General-purpose workflow orchestration |
| **ML Integration** | Native (training, processing, model registry) | Via SageMaker API calls |
| **Experiment Tracking** | Automatic (SageMaker Experiments) | Manual |
| **Step Caching** | Built-in | Not built-in |
| **Visual Designer** | Pipeline Designer in Studio | Workflow Studio |
| **Best For** | End-to-end ML workflows | Complex multi-service orchestration |

### Decision Tree
- ✅ Use SageMaker Pipelines when:
  - Building end-to-end ML workflows
  - Need ML-specific steps (training, tuning, model registration)
  - Want automatic experiment tracking
  - Need step caching for cost optimization
  - Implementing MLOps with model approval workflows
- ❌ Don't use when:
  - Orchestrating non-ML workflows
  - Need complex branching beyond simple if/else
  - Integrating many non-AWS services (use Step Functions)

## 6. Best Practices

### Security
- Pipeline execution role needs permissions for all step types used
- Use IAM roles with least privilege for each step
- Encrypt data at rest (S3, EBS volumes) and in transit
- EMR steps require `AmazonSageMakerPipelinesIntegrations` managed policy

### Cost Optimization
- **Step caching**: Enable to avoid re-running unchanged steps (saves compute costs)
- **Managed Spot Training**: Use in TrainingStep for up to 90% savings
- **Right-size instances**: Use appropriate instance types per step
- **Pipeline parameters**: Parameterize instance types to easily adjust

### Performance
- Use `PipelineSession` (not regular `Session`) for deferred execution
- Parallelize independent steps (Pipelines auto-detects dependencies)
- Use `DependsOn` for custom step ordering when needed

### Operations
- View pipeline DAG and execution history in SageMaker Studio
- Track lineage across pipeline executions
- Use `ExecutionVariables.PIPELINE_EXECUTION_ID` for unique output paths
- Configure retry policies for transient failures

## 7. Typical Pipeline Flow (Exam Pattern)

```
1. ProcessingStep (data preprocessing)
       │
2. TrainingStep (model training)
       │
3. ProcessingStep (model evaluation)
       │
4. ClarifyCheckStep (bias check) ──── optional
       │
5. QualityCheckStep (data quality) ── optional
       │
6. ConditionStep (accuracy > threshold?)
      │              │
    true           false
      │              │
7. ModelStep      FailStep
   (register)    (stop pipeline)
      │
8. TransformStep or Deploy
   (batch or real-time)
```

## 8. Sample Questions

### Question 1
A data science team wants to automate their ML workflow so that a model is only registered in the Model Registry if the evaluation MSE is below 6.0. Which combination of SageMaker Pipeline steps should they use?

A) TrainingStep → TransformStep → RegisterModel
B) ProcessingStep → TrainingStep → ProcessingStep → ConditionStep → RegisterModel
C) TrainingStep → ClarifyCheckStep → RegisterModel
D) ProcessingStep → TrainingStep → LambdaStep → RegisterModel

**Answer: B**
- ✅ B is correct: ProcessingStep for data prep, TrainingStep for training, ProcessingStep for evaluation (produces metrics JSON), ConditionStep to check MSE < 6.0, then RegisterModel only if condition is true.
- ❌ A: No evaluation or conditional logic.
- ❌ C: ClarifyCheck is for bias/explainability, not model accuracy evaluation.
- ❌ D: LambdaStep could work but is not the standard pattern; ConditionStep is purpose-built for this.

### Question 2
A company needs to integrate a manual approval process into their SageMaker Pipeline before deploying a model to production. Which step type should they use?

A) ConditionStep
B) LambdaStep
C) CallbackStep
D) FailStep

**Answer: C**
- ✅ C is correct: CallbackStep sends a message to an SQS queue and waits for an external process (like a human approval) to call SendPipelineExecutionStepSuccess or SendPipelineExecutionStepFailure.
- ❌ A: ConditionStep evaluates automated conditions, not manual approvals.
- ❌ B: LambdaStep runs automated code, not manual approval workflows.
- ❌ D: FailStep stops the pipeline, doesn't enable approval.

### Question 3
A team wants to check for data bias AND model quality drift as part of their automated ML pipeline. How should they configure the pipeline?

A) Use a single ClarifyCheckStep with both DataBiasCheckConfig and ModelQualityCheckConfig
B) Use a ClarifyCheckStep with DataBiasCheckConfig and a separate QualityCheckStep with ModelQualityCheckConfig
C) Use a single QualityCheckStep with both configurations
D) Use a ProcessingStep with a custom bias detection script

**Answer: B**
- ✅ B is correct: ClarifyCheckStep handles bias checks (one type per instance), QualityCheckStep handles quality checks (one type per instance). You need separate steps for different check types.
- ❌ A: ClarifyCheckStep can only run ONE check type per instance, and ModelQualityCheckConfig belongs to QualityCheckStep.
- ❌ C: QualityCheckStep doesn't support bias checks.
- ❌ D: While possible, this doesn't leverage the built-in check steps that integrate with Model Monitor baselines.

---

**Exam Tip**: Focus on knowing WHICH step type to use for each scenario. The exam tests your ability to select the right step type (Processing vs Training vs Condition vs Callback vs ClarifyCheck vs QualityCheck) based on the use case described. Remember that ClarifyCheck = bias/explainability and QualityCheck = data/model quality — they are NOT interchangeable.

---

**Citations**:
- [Add a step - Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/build-and-manage-steps-types.html)
- [Define a pipeline - Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/define-pipeline.html)
- [Pipeline parameters - Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/build-and-manage-parameters.html)
- [Default cache key attributes by pipeline step type - Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/pipelines-default-keys.html)
