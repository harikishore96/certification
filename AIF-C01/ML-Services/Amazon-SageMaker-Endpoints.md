# Amazon SageMaker AI Endpoints

## 1. Service/Topic Overview
- **Purpose**: SageMaker AI Endpoints are fully managed infrastructure for deploying trained ML models to get real-time, asynchronous, serverless, or batch predictions (inferences)
- **Service Level**: Regional
- **Key Use Cases**:
  - Real-time low-latency predictions (chatbots, fraud detection, recommendations)
  - Batch inference on large datasets without persistent endpoints
  - Asynchronous processing of large payloads (video/image processing, document analysis)
  - Cost-effective serverless inference for intermittent traffic
  - A/B testing and shadow testing of model variants in production
- **Exam Weight**: HIGH
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — ML development lifecycle, deployment
  - Domain 3: Applications of Foundation Models (28%) — Model deployment strategies
  - Domain 5: Security, Compliance, and Governance (14%) — Endpoint security, monitoring
- **Task Statements**:
  - Task 1.3: Describe the ML development lifecycle (deployment phase)
  - Task 3.2: Choose effective model deployment strategies
  - Task 5.1: Describe methods to secure AI systems

### Deployment Use Cases (AWS Recommended Approaches)
| Use Case | Recommended Feature | Audience | Environment |
|----------|-------------------|----------|-------------|
| Low-code / no-code deployment | **SageMaker JumpStart** via Studio UI | Citizen data scientists, beginners | SageMaker AI domain |
| Code-based deployment with control | **ModelBuilder** (SageMaker Python SDK) | Experienced data scientists | Python dev environment or SageMaker JupyterLab |
| Deploy at scale in production | **AWS SDK (Boto3) + CloudFormation** | Advanced users, MLOps teams | AWS CLI + IaC/CI-CD tools |

---

## 2. Exam Keyword Mapping

### Trigger Words
| Keyword | Points To |
|---------|-----------|
| "low latency inference", "real-time predictions" | Real-time Endpoint |
| "intermittent traffic", "cold starts acceptable", "pay-per-use" | Serverless Endpoint |
| "large payload", "up to 1GB", "long processing time", "queue requests" | Asynchronous Endpoint |
| "large dataset", "no persistent endpoint", "offline predictions" | Batch Transform |
| "multiple models on one endpoint", "cost-effective hosting", "thousands of models" | Multi-Model Endpoint (MME) |
| "multiple containers", "different frameworks" | Multi-Container Endpoint |
| "compare model versions", "traffic splitting" | Production Variants (A/B Testing) |
| "test without impacting users", "replicate traffic" | Shadow Variants |
| "scale instances based on traffic" | Auto Scaling |
| "scale to zero" | Serverless Endpoint OR Async Endpoint (with auto scaling) |
| "optimize model for hardware", "train once run anywhere", "edge deployment" | SageMaker Neo |
| "simplify deployment", "one-step deployment", "auto container selection" | ModelBuilder |
| "LoRA adapter", "fine-tune at inference", "adapter layers" | Adapter Inference Components |
| "baking period", "auto-rollback", "canary deployment", "linear deployment" | Deployment Guardrails |
| "explicit resource allocation per model", "fine-grained multi-model" | Inference Components |
| "direct invocation", "TargetContainerHostname" | Multi-Container Endpoint (Direct Invocation) |

### Scenario Indicators
- "Company needs to deploy a model that receives sporadic requests with idle periods" → **Serverless Endpoint**
- "Process video files up to 500MB and return results within an hour" → **Asynchronous Endpoint**
- "Run predictions on millions of records stored in S3 nightly" → **Batch Transform**
- "Host 1000+ tenant-specific models cost-effectively" → **Multi-Model Endpoint**
- "Validate a new model version before routing production traffic" → **Shadow Variant**
- "Gradually shift traffic from old model to new model" → **Production Variants (A/B Testing)**
- "Safely update model with automatic rollback if errors increase" → **Deployment Guardrails (Canary/Linear)**
- "Deploy LoRA fine-tuned adapter on top of base foundation model" → **Adapter Inference Components**
- "Optimize model to run on Inferentia chips or edge devices" → **SageMaker Neo**
- "Deploy model quickly without configuring containers or serialization" → **ModelBuilder**
- "Chain preprocessing, inference, and postprocessing in one endpoint" → **Inference Pipeline**

### Anti-patterns
- ❌ Don't use real-time endpoints for large batch jobs → Use Batch Transform
- ❌ Don't use serverless endpoints when you need GPUs → Use Real-time Endpoints
- ❌ Don't use serverless endpoints when you need VPC isolation → Use Real-time Endpoints
- ❌ Don't use batch transform when you need sub-second latency → Use Real-time Endpoints
- ❌ Don't use async endpoints for tiny payloads needing instant response → Use Real-time Endpoints

---

## 3. Core Concepts

### Endpoint Architecture (3-Step Creation)

```
┌──────────────┐     ┌─────────────────────┐     ┌──────────────┐
│  CreateModel │ ──► │CreateEndpointConfig  │ ──► │CreateEndpoint│
│              │     │                      │     │              │
│ - Model      │     │ - Instance type      │     │ - Provisions │
│   artifacts  │     │ - Instance count     │     │   resources  │
│ - Container  │     │ - Variant weights    │     │ - Deploys    │
│   image      │     │ - Data capture       │     │   model(s)   │
│ - IAM role   │     │ - Async/Serverless   │     │ - Returns    │
│              │     │   config             │     │   endpoint   │
└──────────────┘     └─────────────────────┘     │   URL        │
                                                  └──────────────┘
```

### Four Inference Options Comparison

| Feature | Real-Time | Serverless | Asynchronous | Batch Transform |
|---------|-----------|------------|--------------|-----------------|
| **Latency** | Milliseconds | Seconds (cold start) | Minutes | Minutes–Hours |
| **Payload Size** | Up to 6 MB | Up to 4 MB | Up to 1 GB | Up to 100 MB/batch |
| **Processing Time** | Up to 60 sec | Up to 60 sec | Up to 1 hour | No limit |
| **Persistent Endpoint** | ✅ Yes | ✅ Yes (scales to 0) | ✅ Yes (scales to 0) | ❌ No |
| **Scale to Zero** | ❌ No (min 1 instance) | ✅ Yes (on-demand) | ✅ Yes (with auto scaling) | N/A |
| **GPU Support** | ✅ Yes | ❌ No | ✅ Yes | ✅ Yes |
| **VPC Support** | ✅ Yes | ❌ No | ✅ Yes | ✅ Yes |
| **Data Capture** | ✅ Yes | ❌ No | ❌ No | N/A |
| **Model Monitor** | ✅ Yes | ❌ No | ❌ No | N/A |
| **Multi-Model** | ✅ Yes | ❌ No | ❌ No | N/A |
| **Pricing** | Per instance-hour | Per ms of compute + data | Per instance-hour | Per instance-hour |
| **Best For** | Interactive apps | Sporadic traffic | Large payloads | Offline bulk jobs |

### Data Flow by Endpoint Type

**Real-Time:**
```
Client → InvokeEndpoint API → Endpoint → Model Container → Response (sync)
```

**Serverless:**
```
Client → InvokeEndpoint API → [Cold Start if idle] → Compute → Response (sync)
```

**Asynchronous:**
```
Client → Upload payload to S3 → InvokeEndpointAsync API → SQS Queue → 
  Endpoint processes → Output to S3 → (Optional) SNS Notification
```

**Batch Transform:**
```
S3 Input → CreateTransformJob → Compute Instances → S3 Output (.out files)
```

### Inference Components
- A newer abstraction that lets you deploy **multiple models on a single endpoint** with fine-grained resource allocation
- Each inference component specifies its own model, container, and compute resource requirements (CPU, memory, GPU)
- SageMaker AI manages placement and scaling of inference components across endpoint instances
- Supports **adapter inference components** for LoRA fine-tuning (see Section 4.7)
- Enables independent scaling of individual models on shared infrastructure
- Key difference from MME: inference components give you explicit control over resource allocation per model, while MME dynamically manages memory

### Integration Points
- **Amazon S3**: Model artifacts storage, async input/output, batch transform I/O
- **Amazon SNS**: Async inference success/error notifications
- **Amazon SQS**: Internal queue for async inference requests
- **Amazon CloudWatch**: Endpoint metrics, alarms, auto scaling triggers
- **Amazon EventBridge**: Endpoint deployment state change events (for deployment guardrails)
- **AWS Auto Scaling**: Dynamic instance scaling for real-time and async endpoints
- **Application Auto Scaling**: Provisioned Concurrency scaling for serverless endpoints
- **IAM**: Endpoint invocation permissions, execution roles
- **AWS KMS**: Encryption of data at rest and in transit
- **SageMaker Model Monitor**: Data quality, model quality, bias, and feature drift monitoring (real-time only)
- **SageMaker Clarify**: Online explainability for real-time endpoints
- **SageMaker Model Registry**: Register and deploy versioned models to endpoints (including serverless)

---

## 4. Key Features & Components

### 4.1 Real-Time Endpoints

**Hosting Options:**

| Option | Description | Use Case |
|--------|-------------|----------|
| **Single-Model Endpoint** | One model per endpoint | Standard deployment |
| **Multi-Model Endpoint (MME)** | Multiple models share one endpoint, dynamically loaded from S3 | SaaS multi-tenant, 1000s of models |
| **Multi-Container Endpoint** | Up to 15 containers on one endpoint | Different frameworks/models together |
| **Inference Pipeline** | Chain of 2–15 containers in sequence | Pre-processing → Inference → Post-processing |

**Multi-Model Endpoints (MME) — Key Details:**
- Models dynamically loaded/unloaded from memory based on invocation patterns
- Models stored in S3; SageMaker caches them on instance storage
- Supports CPU and GPU instances (GPU uses NVIDIA Triton Inference Server)
- Smart Routing learns traffic patterns for optimal model placement
- Add/remove models by uploading/deleting from S3 — no endpoint update needed
- Cold start latency for infrequently accessed models
- Works with: Auto Scaling, PrivateLink/VPC, Inference Pipelines (only one MME container per pipeline), A/B Testing
- Works best when models are **similar in size and invocation latency** — enables effective time-sharing of memory
- Models with significantly higher TPS or latency requirements should be on **dedicated endpoints**
- **Model lifecycle**: Download from S3 → Cache on instance storage → Load into container memory → Serve; unused models unloaded from memory but remain on storage volume
- Custom containers: Integrate the **Multi Model Server** library for CPU-backed MMEs

**Production Variants (A/B Testing):**
- Multiple model versions behind one endpoint
- Allocate traffic percentage to each variant (e.g., 90% Model A, 10% Model B)
- Each variant can have different instance types and auto scaling policies
- Each request routed to only ONE production variant
- Use `TargetVariant` header to route specific requests to a specific variant

**Shadow Variants (Shadow Testing):**
- Replicate a portion of production traffic to a shadow variant
- Shadow variant responses are logged but NOT returned to the caller
- Compare shadow variant performance against production without user impact
- Ideal for validating new model versions safely

### 4.2 Serverless Endpoints

- **Memory**: 1024 MB to 6144 MB (1–6 GB) in 1024 MB increments
- **Ephemeral Storage**: 5 GB
- **Max Container Image Size**: 10 GB
- **Max Concurrency per Endpoint**: 200
- **Max Serverless Endpoints per Region**: 50
- **Total Concurrency per Account/Region**: 500–1000 (region-dependent)
- **Provisioned Concurrency**: Keeps endpoints warm, eliminates cold starts
  - Integrates with Application Auto Scaling (target metric or schedule-based)
  - Must be ≤ max concurrency assigned to the endpoint
- **Cold Start Monitoring**: Use CloudWatch `OverheadLatency` metric
- **Feature Exclusions**: No GPUs, no VPC, no data capture, no Model Monitor, no multi-model, no inference pipelines, no network isolation, no multiple production variants
- **Container Recommendation**: Create only one worker per container and load only one copy of the model (unlike real-time endpoints which may create a worker per vCPU)
- **Container Reuse**: You can use the same container from a real-time endpoint for serverless (SageMaker escrows a copy encrypted with SageMaker-owned KMS key)
- **Compute Auto-Assignment**: SageMaker auto-assigns vCPUs proportional to the memory size you select
- **MLOps Integration**: Can be integrated with SageMaker Pipelines and Model Registry
- **Available in 21 AWS Regions** (GA)

### 4.3 Asynchronous Endpoints

- **Max Payload**: 1 GB (stored in S3)
- **Max Processing Time**: 1 hour
- **Scale to Zero**: ✅ Supported with auto scaling policies
- **Invocation**: InvokeEndpointAsync API — returns request ID + output S3 location
- **Notifications**: Optional SNS topics for success and error
- **Configuration**: Add `AsyncInferenceConfig` to endpoint configuration
- **Cannot mix**: An async endpoint can ONLY receive async invocations

### 4.4 Batch Transform

- **No persistent endpoint** — compute instances provisioned per job
- **Input/Output**: S3 → S3 (output files have `.out` extension)
- **Splitting**: Set `SplitType=Line` to split input files into mini-batches
- **Max Payload**: 100 MB per batch (`MaxPayloadInMB` must not exceed 100 MB)
- **Constraint**: `MaxConcurrentTransforms × MaxPayloadInMB` must also not exceed 100 MB
- **Parallelism**: `MaxConcurrentTransforms` controls parallel processing
- **File Distribution**: SageMaker partitions S3 objects by key and maps them to instances; if you have 1 input file but multiple instances, only 1 instance processes it (rest idle)
- **Output Assembly**: Set `AssembleWith=Line` to concatenate output records with line delimiter (does NOT merge files — 1 output file per input file)
- **Streaming**: Set `MaxPayloadInMB=0` to stream large data via HTTP chunked encoding (not supported by SageMaker built-in algorithms)
- **CSV Limitation**: Does NOT support CSV input with embedded newline characters
- **Use Cases**: Large dataset inference, data preprocessing, testing model variants, one-time/periodic inference jobs

### 4.5 Auto Scaling

- **Target Tracking Scaling**: Scale based on a target metric value (e.g., `InvocationsPerInstance`)
- **Step Scaling**: Scale based on CloudWatch alarm thresholds
- **Scheduled Scaling**: Scale at specific times (predictable traffic patterns)
- **Scale to Zero**: Supported for async endpoints
- **Key Metric**: `SageMakerVariantInvocationsPerInstance` — most common target metric
- **Cooldown Periods**: Configure scale-in and scale-out cooldown to prevent thrashing

### 4.6 Deployment Strategies (Deployment Guardrails)

Deployment guardrails are fully managed deployment options that only apply to **Real-time** and **Asynchronous** endpoint types.

**Two deployment types:**

**1. Blue/Green Deployments** — Traffic shifts from old fleet (blue) to new fleet (green):

| Traffic Shifting Mode | Description | Control Level |
|----------------------|-------------|---------------|
| **All-at-once** | Shifts ALL traffic to green fleet immediately; monitors during baking period; terminates blue fleet if no alarms trip | Low |
| **Canary** | Shifts small % (canary) to green fleet first; monitors during baking period; if canary succeeds, shifts remaining traffic | Medium |
| **Linear** | Shifts traffic in *n* linearly spaced steps (more granular than canary's 2 steps); each step has a baking period | High |

**2. Rolling Deployments** — Incrementally provisions capacity and shifts traffic in batches:
- You specify batch size (instance count or capacity percentage)
- Instances on new fleet updated with new config; baking period monitored per batch
- Old fleet instances cleaned up after successful baking

**Key Concepts:**
- **Baking Period**: Time window after traffic shift where CloudWatch alarms are monitored
- **Auto-Rollback**: If any pre-specified CloudWatch alarm trips during baking period, SageMaker automatically rolls back to the blue fleet
- **Visibility**: Track deployment progress via DescribeEndpoint API or Amazon EventBridge events
- Deployment guardrails are managed via UpdateEndpoint and CreateEndpoint APIs

### 4.7 Adapter Inference Components (LoRA)

- Deploy **LoRA (Low-Rank Adaptation) adapters** on top of a base foundation model hosted as an inference component
- Only a small part of the foundation model is updated — cost-effective fine-tuning
- Adapter inference components use the **compute resources of the base inference component** (no separate resource allocation)
- Adapter artifacts stored as `tar.gz` in S3
- Invoke adapter directly by specifying `InferenceComponentName` in the invoke_endpoint request
- SageMaker combines the adapter with the base model at inference time to augment the response

### 4.8 SageMaker Neo (Model Optimization)

- **Purpose**: Train once, run anywhere — optimizes models for cloud instances and edge devices
- **How it works**: Compiler converts framework-specific model → framework-agnostic intermediate representation → optimized binary code; Runtime loads and executes compiled model
- **Supported Frameworks**: Gluon, Keras, MXNet, PyTorch, TensorFlow, TensorFlow-Lite, ONNX
- **Target Platforms**: Cloud instances (including AWS Inferentia), edge devices (ARM, Intel, Nvidia, Qualcomm, etc.)
- **Optimization**: Supports FP32, INT8, and FP16 quantization
- **Deployment**: Compiled models deploy to SageMaker endpoints or AWS IoT Greengrass devices
- **Exam Relevance**: Know that Neo reduces inference costs by optimizing models for target hardware

### 4.9 ModelBuilder (Simplified Deployment)

- Python class in SageMaker SDK that converts framework models (XGBoost, PyTorch, etc.) into deployable models in **one step**
- **Auto container selection**: Automatically picks the right container based on model framework
- **Auto serialization/deserialization**: Handles data marshalling via SchemaBuilder
- **Auto dependency capture**: Dynamically captures and packages model dependencies
- **LLM support**: Optional local parameter tuning of serving properties for better performance
- **Supported model servers**: TorchServe, Triton, DJLServing, TGI
- Minimum required inputs: model, sample input/output (SchemaBuilder), IAM role

### 4.10 Multi-Container Endpoint Details

- Up to **15 containers** on a single endpoint
- Two invocation modes:
  - **Sequential (Inference Pipeline)**: Containers process requests in order
  - **Direct Invocation**: Each container accessed individually via `TargetContainerHostname` header
- **IAM Security**: Use `sagemaker:TargetModel` condition key to control which containers a user can invoke
- **Auto Scaling**: Supported for multi-container endpoints
- **Metrics**: Per-container CloudWatch metrics available with direct invocation

---

## 5. Exam Focus Areas

### Common Question Types

**Scenario: "Which inference option should you use?"**
- Low latency, always-on → Real-time
- Sporadic traffic, cost-sensitive → Serverless
- Large payloads, near real-time → Asynchronous
- Bulk offline processing → Batch Transform

**Scenario: "How to reduce inference costs?"**
- Use auto scaling to match demand
- Use serverless endpoints for intermittent traffic
- Use async endpoints that scale to zero
- Use multi-model endpoints to consolidate models
- Use SageMaker Neo to optimize model performance
- Use Managed Spot Training (training, not inference)

**Scenario: "How to safely test a new model version?"**
- Shadow variants → Test without user impact
- Production variants → A/B test with traffic splitting
- Batch transform → Offline comparison with validation dataset

### Gotchas & Tricky Points
- ⚠️ Serverless endpoints do NOT support GPUs, VPC, data capture, or Model Monitor
- ⚠️ You CANNOT convert a real-time endpoint to serverless (only serverless → real-time, one-way)
- ⚠️ Async endpoints can ONLY receive async invocations (not real-time)
- ⚠️ Batch Transform output files match input file names with `.out` extension
- ⚠️ Multi-model endpoints have cold start latency for infrequently used models
- ⚠️ Shadow variant responses are logged but NEVER returned to the caller
- ⚠️ Production variant traffic allocation is by percentage, not by request count
- ⚠️ Serverless Provisioned Concurrency auto scaling is NOT supported on CloudFormation
- ⚠️ Real-time endpoints require minimum 1 instance (cannot scale to zero)
- ⚠️ Async endpoints CAN scale to zero instances
- ⚠️ Deployment guardrails only apply to **Real-time** and **Asynchronous** endpoints (NOT serverless or batch)
- ⚠️ Batch Transform with 1 input file + multiple instances = only 1 instance works, rest idle
- ⚠️ Batch Transform does NOT support CSV with embedded newlines
- ⚠️ MME works best when models are similar in size — high-TPS models should get dedicated endpoints
- ⚠️ Only ONE multi-model enabled container can be included in an inference pipeline
- ⚠️ Inference pipeline models are **immutable** — update by deploying a new one via UpdateEndpoint
- ⚠️ Adapter inference components share the base inference component's compute resources (no separate allocation)
- ⚠️ Serverless endpoints: recommended to create only 1 worker per container (unlike real-time)

### Decision Tree: Choosing an Inference Option

```
Need inference?
├── Need persistent endpoint?
│   ├── YES → Need low latency (<100ms)?
│   │   ├── YES → Need GPU?
│   │   │   ├── YES → Real-Time Endpoint
│   │   │   └── NO → Traffic pattern?
│   │   │       ├── Consistent → Real-Time Endpoint
│   │   │       └── Sporadic → Serverless Endpoint
│   │   └── NO → Payload size?
│   │       ├── > 6 MB → Asynchronous Endpoint
│   │       └── ≤ 6 MB → Processing time?
│   │           ├── > 60 sec → Asynchronous Endpoint
│   │           └── ≤ 60 sec → Real-Time or Serverless
│   └── NO → Batch Transform
```

### Comparison: Multi-Model vs Multi-Container vs Inference Pipeline vs Inference Components

| Aspect | Multi-Model (MME) | Multi-Container | Inference Pipeline | Inference Components |
|--------|-------------|-----------------|-------------------|---------------------|
| **Models** | Many models, same framework | Up to 15 different models | 2–15 containers in sequence | Multiple models with explicit resource allocation |
| **Container** | Shared single container | Separate containers | Chained containers | Separate containers per component |
| **Invocation** | Specify `TargetModel` | `TargetContainerHostname` or pipeline | Sequential processing | Specify `InferenceComponentName` |
| **Use Case** | Multi-tenant SaaS | Different frameworks | Pre/post-processing chains | Fine-grained multi-model hosting |
| **Dynamic Loading** | ✅ Yes (from S3) | ❌ No | ❌ No | ❌ No (pre-allocated) |
| **Resource Control** | Shared (SageMaker manages) | Shared per endpoint | Shared per endpoint | Explicit per component |
| **LoRA Adapters** | ❌ No | ❌ No | ❌ No | ✅ Yes (adapter components) |
| **GPU Support** | ✅ Yes (Triton) | ✅ Yes | ✅ Yes | ✅ Yes |

---

## 6. Best Practices

### Security
- Use **IAM policies** to control who can invoke endpoints (`sagemaker:InvokeEndpoint`)
- Enable **encryption at rest** with AWS KMS for model artifacts and data capture
- Enable **encryption in transit** (HTTPS by default)
- Deploy endpoints in a **VPC** for network isolation (real-time and async only)
- Use **AWS PrivateLink** for private connectivity without internet exposure
- Enable **CloudTrail** for auditing endpoint API calls

### Cost Optimization
- Use **auto scaling** to match instance count to traffic demand
- Use **serverless endpoints** for intermittent/unpredictable traffic (pay-per-use)
- Use **async endpoints with scale-to-zero** for workloads with idle periods
- Use **multi-model endpoints** to consolidate many models onto fewer instances
- Use **batch transform** instead of persistent endpoints for one-time/periodic jobs
- Use **SageMaker Neo** to optimize models for target hardware
- Use **Provisioned Concurrency** (serverless) only when cold starts are unacceptable
- Choose appropriate **instance types** — don't over-provision

### Performance
- Enable **auto scaling** with appropriate target metrics and cooldown periods
- Use **Provisioned Concurrency** on serverless endpoints to eliminate cold starts
- For multi-model endpoints, frequently accessed models stay cached in memory
- Use **inference pipelines** to co-locate pre/post-processing with inference
- Monitor `ModelLatency`, `OverheadLatency`, and `Invocations` CloudWatch metrics
- Load test auto scaling configuration before production deployment

### Operations
- Enable **Data Capture** on real-time endpoints for Model Monitor
- Set up **Model Monitor** schedules for data quality, model quality, bias, and feature drift
- Use **CloudWatch alarms** for endpoint health and auto scaling triggers
- Use **deployment guardrails** (canary/linear) with auto-rollback for safe updates
- Tag endpoints for cost allocation and resource management

---

## 7. Hands-On Labs

### Quick Setup: Deploy with ModelBuilder (Recommended)

```python
from sagemaker.serve.builder.model_builder import ModelBuilder
from sagemaker.serve.builder.schema_builder import SchemaBuilder

model_builder = ModelBuilder(
    model=model,  # Your trained framework model
    schema_builder=SchemaBuilder(
        sample_input="How is the demo going?",
        sample_output="Comment la démo va-t-elle?"
    ),
    role_arn="<execution-role-arn>",
)

# Build and deploy in one step
deployable_model = model_builder.build()
predictor = deployable_model.deploy(
    initial_instance_count=1,
    instance_type="ml.m5.large"
)
```

### Quick Setup: Deploy a Real-Time Endpoint (Python SDK)

```python
import sagemaker
from sagemaker.model import Model

session = sagemaker.Session()
role = sagemaker.get_execution_role()

model = Model(
    image_uri="<container-image-uri>",
    model_data="s3://<bucket>/model.tar.gz",
    role=role,
    sagemaker_session=session
)

predictor = model.deploy(
    initial_instance_count=1,
    instance_type="ml.m5.large",
    endpoint_name="my-realtime-endpoint"
)

response = predictor.predict(data="input data")
```

### Quick Setup: Create a Serverless Endpoint (Boto3)

```python
import boto3

sm = boto3.client("sagemaker")

# Create endpoint config with serverless settings
sm.create_endpoint_config(
    EndpointConfigName="my-serverless-config",
    ProductionVariants=[{
        "VariantName": "AllTraffic",
        "ModelName": "my-model",
        "ServerlessConfig": {
            "MemorySizeInMB": 2048,
            "MaxConcurrency": 10,
            "ProvisionedConcurrency": 2  # Optional: eliminates cold starts
        }
    }]
)

# Create endpoint
sm.create_endpoint(
    EndpointName="my-serverless-endpoint",
    EndpointConfigName="my-serverless-config"
)
```

### Quick Setup: Invoke an Async Endpoint

```python
import boto3

runtime = boto3.client("sagemaker-runtime")

response = runtime.invoke_endpoint_async(
    EndpointName="my-async-endpoint",
    InputLocation="s3://<bucket>/input/large-payload.json",
    ContentType="application/json"
)

output_location = response["OutputLocation"]
inference_id = response["InferenceId"]
```

### CLI: Create a Batch Transform Job

```bash
aws sagemaker create-transform-job \
    --transform-job-name my-batch-job \
    --model-name my-model \
    --transform-input '{
        "DataSource": {
            "S3DataSource": {
                "S3DataType": "S3Prefix",
                "S3Uri": "s3://<bucket>/input/"
            }
        },
        "ContentType": "text/csv",
        "SplitType": "Line"
    }' \
    --transform-output '{
        "S3OutputPath": "s3://<bucket>/output/",
        "AssembleWith": "Line"
    }' \
    --transform-resources '{
        "InstanceType": "ml.m5.xlarge",
        "InstanceCount": 2
    }'
```

---

## 8. Sample Questions

### Question 1
**A company has a fraud detection model that needs to return predictions within 50 milliseconds. The model receives consistent traffic 24/7. Which SageMaker AI inference option should they use?**

A) Serverless Inference  
B) Batch Transform  
C) Real-time Inference  
D) Asynchronous Inference  

**Correct Answer: C**

**Explanation:**
- ✅ **C) Real-time Inference** — Designed for low-latency, interactive workloads. Supports always-on endpoints with auto scaling for consistent traffic.
- ❌ A) Serverless Inference — Has cold start latency that can exceed 50ms, and is designed for sporadic traffic, not 24/7 consistent traffic.
- ❌ B) Batch Transform — No persistent endpoint; designed for offline bulk processing, not real-time predictions.
- ❌ D) Asynchronous Inference — Queues requests and processes them asynchronously; not suitable for sub-second latency requirements.

---

### Question 2
**A SaaS company needs to host 2,000 tenant-specific ML models. Most models are accessed infrequently, but some are accessed frequently. The company wants to minimize hosting costs. Which approach should they use?**

A) Deploy 2,000 separate real-time endpoints  
B) Use a multi-model endpoint  
C) Use 2,000 serverless endpoints  
D) Use batch transform for all models  

**Correct Answer: B**

**Explanation:**
- ✅ **B) Multi-model endpoint** — Designed exactly for this use case. Hosts thousands of models on shared infrastructure, dynamically loads/unloads models based on traffic patterns, and significantly reduces costs compared to individual endpoints.
- ❌ A) 2,000 separate endpoints — Extremely expensive; each endpoint requires at least one instance.
- ❌ C) 2,000 serverless endpoints — Exceeds the limit of 50 serverless endpoints per Region, and would be more expensive than a shared multi-model endpoint.
- ❌ D) Batch transform — No persistent endpoint; cannot serve real-time requests from tenants.

---

### Question 3
**A data science team wants to test a new model version in production without affecting end users. They want to compare the new model's predictions against the current production model. Which SageMaker AI feature should they use?**

A) Production variants with 50/50 traffic split  
B) Shadow variants  
C) Batch transform with a validation dataset  
D) A new separate endpoint  

**Correct Answer: B**

**Explanation:**
- ✅ **B) Shadow variants** — Replicates a portion of production traffic to the shadow (new) model. Shadow responses are logged for comparison but NOT returned to users, ensuring zero impact on end users.
- ❌ A) Production variants with 50/50 split — This WOULD affect end users because 50% of requests would be served by the new model, which hasn't been validated yet.
- ❌ C) Batch transform — Offline testing only; doesn't test with real production traffic patterns.
- ❌ D) Separate endpoint — Doesn't receive the same production traffic for comparison; requires additional routing logic.

---

## Citations

- [Deploy models for inference](https://docs.aws.amazon.com/sagemaker/latest/dg/deploy-model.html) — SageMaker Developer Guide
- [Real-time inference](https://docs.aws.amazon.com/sagemaker/latest/dg/realtime-endpoints.html) — SageMaker Developer Guide
- [Deploy models with Amazon SageMaker Serverless Inference](https://docs.aws.amazon.com/sagemaker/latest/dg/serverless-endpoints.html) — SageMaker Developer Guide
- [Asynchronous inference](https://docs.aws.amazon.com/sagemaker/latest/dg/async-inference.html) — SageMaker Developer Guide
- [Batch transform for inference](https://docs.aws.amazon.com/sagemaker/latest/dg/batch-transform.html) — SageMaker Developer Guide
- [Multi-model endpoints](https://docs.aws.amazon.com/sagemaker/latest/dg/multi-model-endpoints.html) — SageMaker Developer Guide
- [Multi-container endpoints](https://docs.aws.amazon.com/sagemaker/latest/dg/multi-container-endpoints.html) — SageMaker Developer Guide
- [Inference pipelines](https://docs.aws.amazon.com/sagemaker/latest/dg/inference-pipelines.html) — SageMaker Developer Guide
- [Validation of models in production](https://docs.aws.amazon.com/sagemaker/latest/dg/model-validation.html) — SageMaker Developer Guide
- [Deployment guardrails](https://docs.aws.amazon.com/sagemaker/latest/dg/deployment-guardrails.html) — SageMaker Developer Guide
- [Model performance optimization with SageMaker Neo](https://docs.aws.amazon.com/sagemaker/latest/dg/neo.html) — SageMaker Developer Guide
- [Create a model with ModelBuilder](https://docs.aws.amazon.com/sagemaker/latest/dg/how-it-works-modelbuilder-creation.html) — SageMaker Developer Guide
- [Fine-tune models with adapter inference components](https://docs.aws.amazon.com/sagemaker/latest/dg/realtime-endpoints-adapt.html) — SageMaker Developer Guide
- [Automatic scaling of Amazon SageMaker AI models](https://docs.aws.amazon.com/sagemaker/latest/dg/endpoint-auto-scaling.html) — SageMaker Developer Guide

---

**Exam Tip**: The exam loves asking "which inference option" questions. Memorize the key differentiators: payload size limits (4MB serverless, 6MB real-time, 1GB async), scale-to-zero capability (serverless + async only), and GPU support (NOT serverless). When you see "large payload" or "long processing time" → think Async. When you see "sporadic traffic" or "cold starts acceptable" → think Serverless. When you see "thousands of models" → think Multi-Model Endpoint. When you see "safely update model in production" → think Deployment Guardrails (canary/linear with auto-rollback). When you see "optimize model for hardware" → think SageMaker Neo. When you see "simplify deployment" → think ModelBuilder.
