# Types of Inferencing - Batch, Real-Time, and More

## 1. Service/Topic Overview
- **Purpose**: Understanding different inference patterns for deploying ML models and when to use each approach
- **Service Level**: Applies to SageMaker, Bedrock, and AI services across all regions
- **Key Use Cases**: 
  - Selecting appropriate inference method based on latency requirements
  - Optimizing costs for different workload patterns
  - Designing scalable ML architectures
  - Choosing between real-time, batch, and asynchronous inference
- **Exam Weight**: HIGH - Critical for deployment decisions (Domains 1, 3, 5)
- **Exam Domain**: 
  - Domain 1: Fundamentals of AI and ML (20%)
  - Domain 3: Applications of Foundation Models (28%)
  - Domain 5: Security, Compliance, and Governance (14%)
- **Task Statements**: 
  - Task 1.3: Describe the ML development lifecycle
  - Task 3.2: Choose appropriate model inference parameters
  - Task 3.3: Troubleshoot and optimize foundation model applications

## 2. Exam Keyword Mapping

### Trigger Words
- **Real-Time**: "immediate", "milliseconds", "low latency", "synchronous", "interactive", "live"
- **Batch**: "large datasets", "overnight", "scheduled", "non-urgent", "cost-effective", "bulk processing"
- **Asynchronous**: "variable payload", "queue", "long processing", "minutes", "unpredictable workload"
- **Serverless**: "intermittent", "pay-per-use", "no infrastructure", "sporadic traffic"

### Scenario Indicators
- "Fraud detection during transaction" → Real-time
- "Process millions of records overnight" → Batch
- "Analyze uploaded documents (large files)" → Asynchronous
- "Occasional predictions, unpredictable traffic" → Serverless
- "Chatbot responses" → Real-time (Bedrock on-demand)
- "Monthly customer segmentation" → Batch
- "Deploy fine-tuned Bedrock model" → Provisioned Throughput (required)
- "Guaranteed throughput for production FM" → Provisioned Throughput
- "Bulk generate summaries with Bedrock" → Bedrock Batch Inference
- "Handle traffic spikes beyond region capacity" → Cross-Region Inference

### Anti-patterns
- Don't use real-time for large batch processing (expensive)
- Don't use batch for interactive applications (too slow)
- Don't use serverless for consistent high-volume traffic (cold starts)
- Don't use asynchronous for sub-second latency requirements

## 3. Core Concepts

### Inference Lifecycle
```
Trained Model → Deployment → Inference Endpoint → Predictions → Monitoring
```

### Inference Decision Tree
```
Need predictions?
├─ Immediate response required? (< 1 second)
│  ├─ YES → Real-Time Inference
│  │  ├─ Consistent traffic? → Provisioned endpoint
│  │  └─ Sporadic traffic? → Serverless endpoint
│  └─ NO → Can wait minutes/hours?
│     ├─ Large dataset, scheduled? → Batch Inference
│     └─ Variable size, queue-based? → Asynchronous Inference
```

### Key Metrics by Type
- **Latency**: Response time (milliseconds to hours)
- **Throughput**: Requests per second or records per hour
- **Cost**: Per-request vs. per-hour vs. per-instance
- **Scalability**: Auto-scaling capabilities
- **Payload Size**: Maximum input/output size

## 4. Key Features & Components

### 1. Real-Time Inference (Synchronous)

#### Definition
Immediate predictions with low latency (milliseconds to seconds), synchronous request-response pattern.

#### Characteristics
- **Latency**: < 1 second (typically 10-500ms)
- **Pattern**: Synchronous - client waits for response
- **Payload Size**: Up to **25 MB** (SageMaker)
- **Processing Time**: 60 seconds (regular), **8 minutes** (streaming responses)
- **Use Case**: Interactive applications requiring immediate results
- **Cost Model**: Pay for provisioned capacity (instance hours)
- **Scaling**: Auto-scaling based on traffic

#### AWS Implementations

**SageMaker Real-Time Endpoints**
- Persistent HTTPS endpoint
- Multiple instance types (CPU, GPU, Inferentia)
- Auto-scaling with target tracking
- Multi-model endpoints (cost optimization)
- Multi-container endpoints

**Amazon Bedrock (On-Demand)**
- Pay-per-request pricing
- No infrastructure management
- Immediate availability
- Automatic scaling

**AI Services (Rekognition, Comprehend, etc.)**
- Fully managed APIs
- Pay-per-request
- Real-time analysis

#### When to Use
✅ Fraud detection during transactions  
✅ Chatbots and conversational AI  
✅ Real-time recommendations  
✅ Interactive image/video analysis  
✅ Live sentiment analysis  
✅ Instant credit decisions  
✅ Real-time language translation  

#### When NOT to Use
❌ Processing millions of records  
❌ Non-urgent batch jobs  
❌ Highly variable, unpredictable traffic (consider serverless)  
❌ Very large payloads (> 25 MB)  
❌ Long processing times (> 60 seconds for regular, > 8 min for streaming)  

#### Cost Considerations
- Charged per instance-hour (even if idle)
- More expensive than batch for large volumes
- Use auto-scaling to optimize costs
- Consider multi-model endpoints for multiple models

#### Example Architecture
```
User Request → API Gateway → Lambda → SageMaker Endpoint → Response
                                    ↓
                              CloudWatch Metrics
```

---

### 2. Batch Inference (Offline)

#### Definition
Process large datasets offline without persistent endpoints, optimized for throughput over latency.

#### Characteristics
- **Latency**: Minutes to hours (not real-time)
- **Pattern**: Asynchronous - submit job, retrieve results later
- **Payload Size**: Unlimited (entire datasets)
- **Use Case**: Bulk processing, scheduled jobs, non-urgent predictions
- **Cost Model**: Pay only during processing (no idle costs)
- **Scaling**: Processes multiple records in parallel

#### AWS Implementations

**SageMaker Batch Transform**
- No persistent endpoint needed
- Processes S3 data → outputs to S3
- Automatic instance provisioning and termination
- Supports data parallelism
- Managed Spot instance support (70% cost savings)

**SageMaker Processing Jobs**
- Custom batch inference logic
- Flexible data processing
- Integration with ML pipelines

#### When to Use
✅ Monthly customer churn predictions  
✅ Overnight product recommendations  
✅ Periodic image classification (millions of images)  
✅ End-of-day fraud analysis  
✅ Scheduled sentiment analysis of reviews  
✅ Bulk document processing  
✅ Data preprocessing for analytics  

#### When NOT to Use
❌ Interactive applications  
❌ Real-time decision making  
❌ Immediate results required  
❌ Continuous streaming data  
❌ User-facing applications  

#### Cost Considerations
- Most cost-effective for large volumes
- No idle costs (pay only during processing)
- Use Managed Spot for up to 70% savings
- Charged per instance-hour during job execution

#### Example Architecture
```
S3 Input Data → Batch Transform Job → S3 Output Results
                       ↓
                 CloudWatch Logs
                       ↓
                 SNS Notification (completion)
```

---

### 3. Asynchronous Inference

#### Definition
Queue-based inference for requests with variable processing times and payload sizes, with near-real-time results.

#### Characteristics
- **Latency**: Seconds to minutes (near-real-time)
- **Pattern**: Queue-based - submit request, poll for results
- **Payload Size**: Large (up to 1 GB)
- **Use Case**: Variable workloads, large payloads, unpredictable traffic
- **Cost Model**: Pay for processing time + optional auto-scaling
- **Scaling**: Scales to zero when idle, scales up with queue depth

#### AWS Implementations

**SageMaker Asynchronous Inference**
- Request queuing with SQS
- Auto-scales based on queue depth
- Scales to zero (no idle costs)
- Large payload support (up to 1 GB)
- SNS notifications on completion
- Optional result caching

#### When to Use
✅ Document analysis (large PDFs)  
✅ Video processing and analysis  
✅ Large image batch processing  
✅ Audio transcription (long files)  
✅ Unpredictable traffic patterns  
✅ Variable processing times  
✅ Cost optimization with intermittent workloads  

#### When NOT to Use
❌ Sub-second latency requirements  
❌ Synchronous response needed  
❌ Small, consistent payloads (use real-time)  
❌ Scheduled bulk processing (use batch)  

#### Cost Considerations
- Scales to zero (no idle costs)
- Pay for processing time only
- More cost-effective than real-time for sporadic workloads
- Queue and notification costs (minimal)

#### Example Architecture
```
Client → API Gateway → Lambda → SageMaker Async Endpoint
                                        ↓
                                    SQS Queue
                                        ↓
                                  Processing
                                        ↓
                                   S3 Results
                                        ↓
                                SNS Notification → Client
```

---

### 4. Serverless Inference

#### Definition
On-demand inference that automatically scales from zero, ideal for intermittent or unpredictable traffic.

#### Characteristics
- **Latency**: Seconds (includes cold start)
- **Pattern**: On-demand, scales from zero
- **Payload Size**: Up to **4 MB**
- **Processing Time**: Up to 60 seconds
- **Use Case**: Intermittent traffic, development/testing, cost optimization
- **Cost Model**: Pay per request (no idle costs)
- **Scaling**: Automatic, scales to zero

#### AWS Implementations

**SageMaker Serverless Inference**
- No instance management
- Automatic scaling (0 to thousands)
- Cold start latency (10-30 seconds first request)
- Warm pool for subsequent requests
- Memory-based pricing (1-6 GB)

**Amazon Bedrock (On-Demand)**
- Fully serverless foundation models
- Pay-per-token pricing
- No cold starts
- Automatic scaling

**AWS Lambda with Models**
- Custom inference logic
- 15-minute timeout
- 10 GB memory max
- Container image support

#### When to Use
✅ Development and testing  
✅ Infrequent predictions (few per hour)  
✅ Unpredictable traffic patterns  
✅ Proof-of-concepts  
✅ Cost-sensitive applications with low volume  
✅ Internal tools with sporadic use  

#### When NOT to Use
❌ Consistent high-volume traffic  
❌ Strict latency requirements (< 1 second)  
❌ Cold start intolerance  
❌ Payloads > 4 MB  
❌ Processing times > 60 seconds  

#### Cost Considerations
- Most cost-effective for low, intermittent traffic
- No idle costs
- More expensive per request than provisioned for high volume
- Break-even point typically at consistent traffic

#### Example Architecture
```
Sporadic Requests → API Gateway → SageMaker Serverless Endpoint
                                         ↓
                                  Auto-scales 0-N
                                         ↓
                                     Response
```

---

### 5. Bedrock Inference Modes

Amazon Bedrock offers its own set of inference modes that are distinct from SageMaker:

#### 5a. On-Demand Inference (Bedrock)
- **How It Works**: Pay-per-token pricing, no upfront commitment
- **Pricing**: Charged per input token + per output token
- **Scaling**: Automatic, fully managed
- **Cold Starts**: None
- **Best For**: Variable workloads, getting started, development/testing
- **Exam Tip**: Default mode for Bedrock — simplest to use, no capacity planning needed

#### 5b. Provisioned Throughput (Bedrock)
- **How It Works**: Purchase dedicated capacity measured in **Model Units (MUs)** for guaranteed throughput
- **Pricing**: Billed hourly based on model, number of MUs, and commitment duration
- **Commitment Terms**:
  - **No commitment** — Delete anytime, highest hourly rate
  - **1 month** — Cannot delete until term ends, discounted rate
  - **6 months** — Cannot delete until term ends, most discounted rate
- **Model Units (MUs)**: Each MU specifies:
  - Number of input tokens processed per minute across all requests
  - Number of output tokens generated per minute across all requests
- **Required For**: Custom models (fine-tuned or continued pre-training) — you MUST purchase Provisioned Throughput to use a custom model
- **Best For**: Production workloads with predictable high traffic, custom models, guaranteed performance
- **Exam Tip**: If a question mentions "custom model on Bedrock" + "inference", Provisioned Throughput is required

> 📖 Source: [Provisioned Throughput in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/prov-throughput.html)

#### 5c. Batch Inference (Bedrock)
- **How It Works**: Submit large datasets (JSONL files in S3) for offline processing via CreateModelInvocationJob API
- **Input**: S3 bucket with JSONL files (supports InvokeModel or Converse API format)
- **Output**: Results written to S3
- **Requires**: IAM service role with S3 permissions, KMS encryption supported
- **Best For**: Bulk processing of prompts, non-urgent large-scale generation, cost optimization for high volume
- **Exam Tip**: Bedrock DOES support batch inference — don't assume it's real-time only

> 📖 Source: [Create a batch inference job - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/batch-inference-create.html)

#### 5d. Cross-Region Inference (Bedrock)
- **How It Works**: Routes inference requests across multiple AWS Regions to maximize throughput and manage traffic bursts
- **Types**:
  - **Geographic** — Routes within a geographic boundary (e.g., US-only regions)
  - **Global** — Routes across all supported regions for maximum throughput
- **Best For**: High-traffic applications, handling traffic spikes, improving availability
- **Exam Tip**: Use cross-region inference when you need to handle burst traffic beyond a single region's capacity

> 📖 Source: [Cross-Region inference - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html)

#### Bedrock Inference Mode Decision Tree
```
Using Amazon Bedrock?
├─ Custom/fine-tuned model? → Provisioned Throughput (REQUIRED)
├─ Base model?
│  ├─ Variable/low traffic? → On-Demand (pay-per-token)
│  ├─ Consistent high traffic, need guaranteed performance? → Provisioned Throughput
│  ├─ Bulk offline processing? → Batch Inference
│  └─ Traffic spikes beyond single region? → Cross-Region Inference
```

---

### 6. Streaming Inference (Real-Time Streaming)

#### Definition
Continuous processing of streaming data with low latency, often for time-series or event-driven predictions.

#### Characteristics
- **Latency**: Milliseconds (per event)
- **Pattern**: Continuous stream processing
- **Payload Size**: Small individual events
- **Use Case**: IoT, clickstream, real-time analytics
- **Cost Model**: Combination of streaming service + inference costs
- **Scaling**: Handles high-throughput streams

#### AWS Implementations

**Kinesis + SageMaker/Lambda**
- Kinesis Data Streams for ingestion
- Lambda or Kinesis Analytics for inference
- Real-time processing pipeline

**Kinesis + Bedrock**
- Stream processing with foundation models
- Real-time text analysis

#### When to Use
✅ IoT sensor data analysis  
✅ Real-time clickstream analysis  
✅ Continuous anomaly detection  
✅ Live video analytics  
✅ Real-time log analysis  
✅ Financial market predictions  

#### When NOT to Use
❌ Batch processing requirements  
❌ Non-streaming data  
❌ Simple request-response patterns  

#### Example Architecture
```
IoT Devices → Kinesis Data Streams → Lambda → SageMaker Endpoint
                                              ↓
                                        CloudWatch
                                              ↓
                                      Kinesis Firehose → S3
```

---

### 7. Edge Inference (On-Device)

#### Definition
Run inference on edge devices (IoT, mobile, embedded systems) without cloud connectivity.

#### Characteristics
- **Latency**: Milliseconds (local processing)
- **Pattern**: On-device, offline capable
- **Payload Size**: Varies by device
- **Use Case**: Offline operation, privacy, low latency
- **Cost Model**: Device compute costs (no cloud inference costs)
- **Scaling**: Per-device deployment

#### AWS Implementations

**SageMaker Edge Manager**
- Deploy models to edge devices
- Model optimization for edge
- Fleet management
- Monitoring and updates

**AWS IoT Greengrass**
- Local inference on IoT devices
- Offline operation
- Model updates over-the-air

**SageMaker Neo**
- Model compilation for edge devices
- Optimization for ARM, Intel, NVIDIA

#### When to Use
✅ Offline operation required  
✅ Privacy-sensitive data (local processing)  
✅ Ultra-low latency needs  
✅ Limited/expensive connectivity  
✅ Autonomous vehicles  
✅ Industrial IoT  
✅ Mobile applications  

#### When NOT to Use
❌ Large models (device constraints)  
❌ Frequent model updates needed  
❌ Centralized data collection required  
❌ Complex models requiring GPUs  

---

## 5. Exam Focus Areas

### Comprehensive Comparison Table

| Aspect | Real-Time | Batch | Asynchronous | Serverless | Streaming | Edge |
|--------|-----------|-------|--------------|------------|-----------|------|
| **Latency** | < 1 sec | Hours | Seconds-Minutes | Seconds | Milliseconds | Milliseconds |
| **Pattern** | Sync | Async | Queue | On-demand | Continuous | Local |
| **Payload** | ≤ 25 MB | Unlimited | ≤ 1 GB | ≤ 4 MB | Small events | Varies |
| **Idle Cost** | Yes | No | No | No | Yes (stream) | No |
| **Scaling** | Auto-scale | Job-based | Queue-based | Zero-scale | Stream-based | Per-device |
| **Best For** | Interactive | Bulk | Variable | Intermittent | Continuous | Offline |
| **SageMaker** | Endpoints | Transform | Async Endpoint | Serverless | + Kinesis | Edge Manager |
| **Bedrock** | On-Demand / Provisioned | Batch Inference | N/A | On-Demand | + Kinesis | N/A |

### Bedrock-Specific Inference Comparison

| Aspect | On-Demand | Provisioned Throughput | Batch Inference | Cross-Region |
|--------|-----------|----------------------|-----------------|---------------|
| **Pricing** | Per-token | Hourly (MU-based) | Per-token (discounted) | Per-token |
| **Commitment** | None | None / 1 mo / 6 mo | None | None |
| **Custom Models** | ❌ | ✅ Required | ❌ | ❌ |
| **Guaranteed Throughput** | ❌ | ✅ | ❌ | ❌ |
| **Latency** | Low | Low (dedicated) | Hours | Low |
| **Best For** | Variable traffic | Production, custom models | Bulk processing | Burst traffic |

### Decision Matrix for Exam Questions

```
Question Scenario → Choose This Inference Type
────────────────────────────────────────────────────────────
"Fraud detection during checkout"              → Real-Time
"Chatbot conversation"                         → Real-Time (Bedrock)
"Process 10M images overnight"                 → Batch Transform
"Monthly customer segmentation"                → Batch Transform
"Analyze uploaded documents (100 MB PDFs)"     → Asynchronous
"Unpredictable traffic, cost-sensitive"        → Asynchronous/Serverless
"Development/testing environment"              → Serverless
"Few predictions per day"                      → Serverless
"IoT sensor data, continuous"                  → Streaming
"Autonomous vehicle decisions"                 → Edge
"Offline mobile app"                           → Edge
"Cost optimization, non-urgent"                → Batch
"Variable processing time, large files"        → Asynchronous
"Custom fine-tuned model on Bedrock"           → Provisioned Throughput
"Guaranteed throughput for FM"                 → Provisioned Throughput
"Bulk prompt processing on Bedrock"            → Bedrock Batch Inference
"Handle traffic spikes across regions"         → Cross-Region Inference
```

### Common Question Types

**Latency-Based Selection**
- "Immediate response required" → Real-Time
- "Results needed within hours" → Batch
- "Near real-time, seconds acceptable" → Asynchronous

**Cost Optimization**
- "Most cost-effective for millions of records" → Batch
- "Minimize idle costs, sporadic traffic" → Serverless or Asynchronous
- "Consistent traffic, optimize cost" → Real-Time with auto-scaling

**Workload Pattern**
- "Scheduled nightly job" → Batch
- "Interactive user application" → Real-Time
- "Unpredictable, variable workload" → Asynchronous or Serverless
- "Continuous data stream" → Streaming

### Gotchas

1. **Serverless ≠ Always Cheaper**
   - Serverless is cheaper for LOW, intermittent traffic
   - Real-time provisioned is cheaper for consistent high traffic
   - Calculate break-even point based on request volume

2. **Asynchronous ≠ Batch**
   - Asynchronous: Near real-time, queue-based, variable payloads
   - Batch: Scheduled, bulk processing, cost-optimized
   - Don't confuse the two

3. **Real-Time Endpoint Idle Costs**
   - Charged even when not processing requests
   - Use auto-scaling to scale down during low traffic
   - Consider serverless for truly sporadic workloads

4. **Cold Start Latency**
   - Serverless inference has cold start (10-30 seconds)
   - Not suitable for strict latency requirements
   - Bedrock on-demand has no cold starts

5. **Payload Size Limits**
   - Real-Time: **25 MB** (SageMaker)
   - Serverless: **4 MB** (SageMaker)
   - Asynchronous: **1 GB** (SageMaker)
   - Batch: Unlimited (processes from S3)
   - Choose based on data size

6. **Bedrock Custom Models Require Provisioned Throughput**
   - You CANNOT use on-demand inference for fine-tuned or continued pre-trained models
   - Must purchase Provisioned Throughput (with MUs) to invoke custom models
   - This is a common exam distractor

7. **Bedrock Batch Inference Exists**
   - Bedrock supports batch inference via CreateModelInvocationJob
   - Input/output via S3 (JSONL format)
   - Don't assume Bedrock is real-time only

## 6. Best Practices

### Selecting Inference Type

**Real-Time Inference**
- Use auto-scaling (target tracking on invocations)
- Implement multi-model endpoints for cost savings
- Monitor latency with CloudWatch
- Use Application Auto Scaling for dynamic capacity
- Consider multi-AZ deployment for high availability

**Batch Inference**
- Use Managed Spot instances (70% savings)
- Optimize batch size for throughput
- Use data parallelism for large datasets
- Schedule during off-peak hours
- Monitor with CloudWatch and SNS notifications

**Asynchronous Inference**
- Configure appropriate queue timeout
- Set up SNS notifications for completion
- Use S3 for large input/output data
- Monitor queue depth for scaling
- Implement result caching for repeated requests

**Serverless Inference**
- Start with development/testing
- Monitor cold start frequency
- Configure appropriate memory allocation
- Use for proof-of-concepts
- Transition to provisioned if traffic increases

### Cost Optimization Strategies

1. **Right-Size Instances**: Choose appropriate instance types
2. **Auto-Scaling**: Scale based on actual demand
3. **Managed Spot**: Use for batch workloads (70% savings)
4. **Multi-Model Endpoints**: Share infrastructure across models
5. **Serverless for Low Volume**: Eliminate idle costs
6. **Batch for Bulk**: Most cost-effective for large datasets

### Security Best Practices

- **Encryption**: Enable at-rest (S3, EBS) and in-transit (TLS)
- **IAM**: Least privilege access for endpoints
- **VPC**: Deploy endpoints in private subnets
- **Monitoring**: CloudWatch Logs and CloudTrail
- **Data Privacy**: Use VPC endpoints for private connectivity

## 7. Hands-On Labs

### Lab 1: Real-Time Inference with SageMaker

```python
import boto3
import sagemaker
from sagemaker.predictor import Predictor

# Deploy model to real-time endpoint
predictor = model.deploy(
    initial_instance_count=1,
    instance_type='ml.m5.xlarge',
    endpoint_name='realtime-fraud-detection'
)

# Configure auto-scaling
client = boto3.client('application-autoscaling')

# Register scalable target
client.register_scalable_target(
    ServiceNamespace='sagemaker',
    ResourceId=f'endpoint/realtime-fraud-detection/variant/AllTraffic',
    ScalableDimension='sagemaker:variant:DesiredInstanceCount',
    MinCapacity=1,
    MaxCapacity=10
)

# Create scaling policy
client.put_scaling_policy(
    PolicyName='InvocationsPerInstance',
    ServiceNamespace='sagemaker',
    ResourceId=f'endpoint/realtime-fraud-detection/variant/AllTraffic',
    ScalableDimension='sagemaker:variant:DesiredInstanceCount',
    PolicyType='TargetTrackingScaling',
    TargetTrackingScalingPolicyConfiguration={
        'TargetValue': 1000.0,  # Target 1000 invocations per instance
        'PredefinedMetricSpecification': {
            'PredefinedMetricType': 'SageMakerVariantInvocationsPerInstance'
        }
    }
)

# Make real-time prediction
response = predictor.predict(data)
```

### Lab 2: Batch Transform

```python
from sagemaker.transformer import Transformer

# Create transformer
transformer = Transformer(
    model_name='my-model',
    instance_count=2,
    instance_type='ml.m5.xlarge',
    output_path='s3://bucket/batch-output/',
    use_spot_instances=True,  # 70% cost savings
    max_wait_time=3600
)

# Run batch transform
transformer.transform(
    data='s3://bucket/batch-input/',
    content_type='text/csv',
    split_type='Line',
    join_source='Input'  # Include input with output
)

# Wait for completion
transformer.wait()
```

### Lab 3: Asynchronous Inference

```python
from sagemaker.async_inference import AsyncInferenceConfig
from sagemaker.model import Model

# Configure async inference
async_config = AsyncInferenceConfig(
    output_path='s3://bucket/async-output/',
    max_concurrent_invocations_per_instance=10,
    notification_config={
        'SuccessTopic': 'arn:aws:sns:region:account:success-topic',
        'ErrorTopic': 'arn:aws:sns:region:account:error-topic'
    }
)

# Deploy async endpoint
async_predictor = model.deploy(
    initial_instance_count=1,
    instance_type='ml.m5.xlarge',
    endpoint_name='async-document-analysis',
    async_inference_config=async_config
)

# Submit async request
response = async_predictor.predict_async(
    data=large_document,
    input_path='s3://bucket/input/doc.pdf'
)

# Get output location
output_location = response.output_path
```

### Lab 4: Serverless Inference

```python
from sagemaker.serverless import ServerlessInferenceConfig

# Configure serverless
serverless_config = ServerlessInferenceConfig(
    memory_size_in_mb=4096,  # 1-6 GB
    max_concurrency=20       # Max concurrent invocations
)

# Deploy serverless endpoint
serverless_predictor = model.deploy(
    serverless_inference_config=serverless_config,
    endpoint_name='serverless-classifier'
)

# Make prediction (same API as real-time)
response = serverless_predictor.predict(data)
```

### Lab 5: Bedrock Real-Time Inference

```python
import boto3
import json

bedrock = boto3.client('bedrock-runtime')

# Real-time inference with Bedrock (on-demand)
response = bedrock.invoke_model(
    modelId='anthropic.claude-3-sonnet-20240229-v1:0',
    body=json.dumps({
        "anthropic_version": "bedrock-2023-05-31",
        "max_tokens": 1000,
        "messages": [
            {"role": "user", "content": "Analyze this customer review for sentiment"}
        ]
    })
)

result = json.loads(response['body'].read())
# Immediate response, pay-per-request, no infrastructure
```

## 8. Sample Questions

### Question 1: Real-Time vs Batch

**Scenario**: An e-commerce company needs to generate personalized product recommendations. They have two requirements:
1. Show recommendations on the website when customers browse (5 million daily visitors)
2. Send weekly email campaigns with recommendations (50 million customers)

**Question**: Which inference approach should they use for each requirement?

A) Real-time for both  
B) Batch for both  
C) Real-time for website, Batch for emails  
D) Batch for website, Real-time for emails

**Correct Answer**: C) Real-time for website, Batch for emails

**Explanation**:
- **Why C is correct**: Website browsing requires immediate recommendations (real-time inference). Weekly emails are scheduled, non-urgent, and process large volumes (batch inference is cost-effective).
- **Why A is wrong**: Using real-time for 50 million email recommendations would be extremely expensive with idle costs. Batch is more cost-effective for scheduled bulk processing.
- **Why B is wrong**: Website visitors need immediate recommendations. Batch processing takes hours and wouldn't provide real-time user experience.
- **Why D is wrong**: Backwards - website needs immediate response (real-time), emails can wait (batch).

**Exam Tip**: Interactive/immediate = Real-time. Scheduled/bulk = Batch.

---

### Question 2: Asynchronous Inference

**Scenario**: A legal firm needs to analyze uploaded contracts (PDF files ranging from 10 MB to 500 MB). Processing time varies from 30 seconds to 10 minutes. Upload frequency is unpredictable (0-50 per hour).

**Question**: Which inference type is most appropriate?

A) Real-time inference with auto-scaling  
B) Batch transform with scheduled jobs  
C) Asynchronous inference  
D) Serverless inference

**Correct Answer**: C) Asynchronous inference

**Explanation**:
- **Why C is correct**: Large payloads (up to 500 MB), variable processing times (30s-10min), and unpredictable traffic are perfect for asynchronous inference. It handles large files, queues requests, scales to zero when idle, and provides near-real-time results.
- **Why A is wrong**: Real-time has 6 MB payload limit and 60-second timeout. These files are too large and processing too long.
- **Why B is wrong**: Batch is for scheduled jobs, not on-demand uploads. Users upload contracts unpredictably, not on a schedule.
- **Why D is wrong**: Serverless has 6 MB payload limit and isn't optimized for long processing times (10 minutes).

**Exam Tip**: Large payloads + variable processing time + unpredictable traffic = Asynchronous inference.

---

### Question 3: Serverless vs Provisioned

**Scenario**: A startup is developing an ML-powered feature for their mobile app. They expect 10-50 prediction requests per day during development, but anticipate 10,000+ requests per hour after launch.

**Question**: What is the most cost-effective deployment strategy?

A) Use real-time provisioned endpoints from the start  
B) Use serverless inference for development, switch to provisioned after launch  
C) Use batch transform for all predictions  
D) Use asynchronous inference throughout

**Correct Answer**: B) Use serverless inference for development, switch to provisioned after launch

**Explanation**:
- **Why B is correct**: Serverless is ideal for low-volume development (no idle costs for 10-50 requests/day). After launch with consistent high traffic (10,000+/hour), provisioned endpoints become more cost-effective. This strategy optimizes costs for both phases.
- **Why A is wrong**: Provisioned endpoints charge for idle time. During development with only 10-50 requests/day, you'd pay for 24/7 capacity that's rarely used.
- **Why C is wrong**: Batch is for scheduled bulk processing, not interactive mobile app predictions that need immediate results.
- **Why D is wrong**: Asynchronous is for large payloads and variable processing. Mobile app predictions likely need quick synchronous responses.

**Exam Tip**: Low intermittent traffic = Serverless. High consistent traffic = Provisioned. Transition as traffic grows.

---

### Question 4: Edge Inference

**Scenario**: An agricultural company is deploying IoT devices to monitor crop health in remote farms with limited internet connectivity. Devices need to analyze images locally and make immediate decisions about irrigation.

**Question**: Which inference approach should they use?

A) Real-time inference with SageMaker endpoints  
B) Batch transform with scheduled uploads  
C) Edge inference with SageMaker Edge Manager  
D) Asynchronous inference with queuing

**Correct Answer**: C) Edge inference with SageMaker Edge Manager

**Explanation**:
- **Why C is correct**: Limited connectivity and need for immediate local decisions require edge inference. SageMaker Edge Manager deploys models to IoT devices for offline operation with ultra-low latency.
- **Why A is wrong**: Real-time endpoints require internet connectivity. Remote farms have limited connectivity, making cloud-based inference unreliable.
- **Why B is wrong**: Batch requires uploading data to cloud and waiting for results. Immediate irrigation decisions can't wait for scheduled processing.
- **Why D is wrong**: Asynchronous still requires internet connectivity to submit requests and retrieve results. Doesn't solve the connectivity problem.

**Exam Tip**: Limited/no connectivity + local processing + IoT = Edge inference.

---

**Exam Tip**: Match inference type to latency requirements, workload patterns, and cost constraints. Real-time (interactive), Batch (scheduled bulk), Asynchronous (variable/large), Serverless (intermittent), Streaming (continuous), Edge (offline). For Bedrock: On-Demand (default), Provisioned Throughput (custom models + guaranteed capacity), Batch Inference (bulk offline), Cross-Region (burst traffic).
