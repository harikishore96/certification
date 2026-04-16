# CloudWatch Model Invocation Logging (Amazon Bedrock)

## 1. Service/Topic Overview
- **Purpose**: Enables monitoring of all Amazon Bedrock model invocation requests, responses, and metadata using CloudWatch Logs and Amazon S3
- **Service Level**: Regional (logs must be in the same account and Region)
- **Key Use Cases**:
  - Auditing all foundation model API calls (inputs/outputs)
  - Tracking token usage and costs per model
  - Debugging model invocation errors and latency
  - Compliance logging for AI/ML workloads
  - Real-time monitoring of generative AI application performance
- **Exam Weight**: HIGH
- **Exam Domain**:
  - Domain 5: Security, Compliance, and Governance for AI Solutions (14%)
  - Domain 3: Applications of Foundation Models (28%) — monitoring deployed FM applications
- **Task Statements**:
  - Task 5.1: Methods to secure AI systems
  - Task 5.2: Governance and compliance for AI solutions
  - Task 3.1: Design considerations for FM applications (observability)

## 2. Exam Keyword Mapping
- **Trigger Words**: "monitor model invocations", "log Bedrock requests", "track token usage", "audit FM calls", "invocation logging", "model observability", "CloudWatch Bedrock", "generative AI monitoring"
- **Scenario Indicators**:
  - "Company needs to audit all prompts and responses sent to foundation models"
  - "Track which models are being used and how many tokens are consumed"
  - "Debug why a Bedrock model invocation is failing or slow"
  - "Compliance requires logging all AI model interactions"
  - "Monitor latency and throttling of FM invocations"
- **Anti-patterns**:
  - ❌ CloudTrail alone — CloudTrail logs API calls (who called what) but NOT the request/response content
  - ❌ SageMaker Model Monitor — for monitoring data drift on SageMaker endpoints, not Bedrock
  - ❌ Amazon Bedrock Guardrails — for content filtering, not logging

## 3. Core Concepts

### What Gets Logged
Model invocation logging captures:
- **Full request data** (prompts, parameters)
- **Full response data** (model outputs)
- **Metadata** (model ID, timestamps, token counts, latency)

### Logged API Operations
| API | Type |
|-----|------|
| `Converse` | Synchronous multi-turn |
| `ConverseStream` | Streaming multi-turn |
| `InvokeModel` | Synchronous single-turn |
| `InvokeModelWithResponseStream` | Streaming single-turn |

### Logging Destinations
| Destination | What It Handles | Query Method |
|-------------|----------------|--------------|
| **CloudWatch Logs** | JSON log events with metadata + input/output up to **100 KB** | CloudWatch Logs Insights |
| **Amazon S3** | Gzipped JSON batches + large data/binary (>100 KB) | Athena, S3 Select, Glue ETL |
| **Both** | Full coverage — CW Logs for real-time, S3 for large data | Both methods |

> ⚠️ **Key Exam Point**: Large input/output data (>100 KB) and binary image outputs are ONLY stored in S3, even if CloudWatch Logs is configured.

### Data Flow
```
Bedrock API Call → Model Invocation → Logging Service
                                         ├── CloudWatch Logs (JSON events, ≤100 KB)
                                         │     └── CloudWatch Logs Insights (query)
                                         │     └── CloudWatch Dashboards (visualize)
                                         └── Amazon S3 (gzipped JSON + large data)
                                               └── Athena / Glue / OpenSearch (analyze)
```

### Modality Options
When enabling logging, you select which modalities to log:
- **Text** — text-based model invocations
- **Image** — image input/output models
- **Embedding** — embedding model invocations
- **Video** — video model invocations

> Data is logged for ALL models that support the selected modalities.

## 4. Key Features & Components

### Amazon Bedrock Runtime Metrics (CloudWatch Namespace: `AWS/Bedrock`)

| Metric | Unit | Description |
|--------|------|-------------|
| `Invocations` | SampleCount | Successful requests to Converse/InvokeModel APIs |
| `InvocationLatency` | Milliseconds | Time from request sent to last token received |
| `InvocationClientErrors` | SampleCount | Client-side errors (4xx) |
| `InvocationServerErrors` | SampleCount | Server-side errors (5xx) |
| `InvocationThrottles` | SampleCount | Throttled requests (NOT counted as Invocations or Errors) |
| `InputTokenCount` | SampleCount | Tokens in the input |
| `OutputTokenCount` | SampleCount | Tokens in the output |
| `OutputImageCount` | SampleCount | Images in output (image generation models) |
| `TimeToFirstToken` | Milliseconds | Time to first token (streaming APIs only) |
| `EstimatedTPMQuotaUsage` | SampleCount | Estimated Tokens Per Minute quota consumption |
| `LegacyModelInvocations` | SampleCount | Invocations using legacy models |

### Log Delivery Metrics (Namespace: `AWS/Bedrock`)
- `ModelInvocationLogsCloudWatchDeliverySuccess` / `Failure`
- `ModelInvocationLogsS3DeliverySuccess` / `Failure`
- `ModelInvocationLargeDataS3DeliverySuccess` / `Failure`

### Metric Dimensions
- `ModelId` — filter metrics by specific model
- `ModelId + ImageSize + BucketedStepSize` — for OutputImageCount

### CloudWatch GenAI Observability Dashboard
Pre-configured dashboards showing:
- Invocation count
- Invocation latency
- Token counts by model (input vs output)
- Daily token counts by ModelID
- Requests grouped by input token ranges
- Invocation throttles
- Invocation error count

### Configuration APIs
| API | Purpose |
|-----|---------|
| `PutModelInvocationLoggingConfiguration` | Enable/configure logging |
| `GetModelInvocationLoggingConfiguration` | Retrieve current config |
| `DeleteModelInvocationLoggingConfiguration` | Disable logging |

### Default State
- **Model invocation logging is DISABLED by default**
- Must be explicitly enabled via Console or API
- Once enabled, logs are stored until the logging configuration is deleted

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "A company needs to track all prompts sent to Bedrock models for compliance" → Enable model invocation logging to CloudWatch Logs and/or S3
- **Scenario**: "Monitor token usage across models to optimize costs" → Use CloudWatch metrics (InputTokenCount, OutputTokenCount) under AWS/Bedrock namespace
- **Scenario**: "Debug high latency in Bedrock responses" → Check InvocationLatency and TimeToFirstToken metrics
- **Feature comparison**: CloudTrail vs Model Invocation Logging vs Guardrails

### Gotchas
- 🚨 **Disabled by default** — you must explicitly enable it
- 🚨 **Same Region only** — destination S3 bucket and CloudWatch log group must be in the same Region
- 🚨 **Same account only** — cross-account logging destinations are NOT supported
- 🚨 **100 KB limit for CloudWatch Logs** — larger payloads go to S3 only
- 🚨 **Throttled requests** are NOT counted as Invocations or Errors in metrics
- 🚨 **Image/document data** from Converse API is only logged to S3 (not CloudWatch Logs)

### Comparison Points

| Feature | Model Invocation Logging | AWS CloudTrail | Bedrock Guardrails |
|---------|------------------------|----------------|-------------------|
| **What it logs** | Full request/response content + metadata | API call metadata (who, when, what API) | Content policy violations |
| **Purpose** | Observability & debugging | Audit trail & compliance | Content safety & filtering |
| **Captures prompts/responses** | ✅ Yes | ❌ No | Only flagged content |
| **Token counts** | ✅ Yes | ❌ No | ❌ No |
| **Latency metrics** | ✅ Yes | ❌ No | ❌ No |
| **Default state** | Disabled | Enabled (management events) | Must create guardrail |

### Decision Tree
- ✅ **Use Model Invocation Logging** when you need to see actual prompts, responses, token usage, or debug model behavior
- ✅ **Use CloudTrail** when you need to audit WHO made API calls and WHEN
- ✅ **Use CloudWatch Metrics** when you need real-time dashboards for latency, errors, throttles
- ✅ **Use S3 destination** when you need to store large payloads, run Athena queries, or archive logs
- ✅ **Use CloudWatch Logs destination** when you need real-time querying with Logs Insights
- ❌ **Don't confuse** with SageMaker Model Monitor (monitors data/model drift, not Bedrock invocations)

## 6. Best Practices

### Security
- Use **IAM roles** with least-privilege for Bedrock to write to CloudWatch Logs and S3
- Apply **confused deputy protection** with `aws:SourceAccount` and `aws:SourceArn` conditions
- Enable **SSE-KMS encryption** on S3 buckets storing invocation logs
- Enable **CloudWatch Logs encryption** with KMS for sensitive prompt/response data
- Use **CloudWatch Logs data protection** to mask sensitive data (PII) in logs
- Disable S3 bucket ACLs (use bucket policies only)

### Cost Optimization
- Select only the **modalities you need** (don't log embeddings if you only use text)
- Use **S3 lifecycle policies** to transition old logs to cheaper storage tiers
- Set **CloudWatch Logs retention periods** to avoid indefinite storage costs
- Use **CloudWatch Logs Insights** for targeted queries instead of scanning all logs

### Performance
- Use **CloudWatch Alarms** on InvocationThrottles to detect quota limits
- Monitor **TimeToFirstToken** for streaming applications
- Track **InvocationLatency** to identify slow models or regions
- Use **inference profiles** alongside logging to track usage by application

### Operations
- Set up **CloudWatch Alarms** for:
  - High error rates (InvocationClientErrors, InvocationServerErrors)
  - Throttling spikes (InvocationThrottles)
  - Log delivery failures (ModelInvocationLogsCloudWatchDeliveryFailure)
- Use **CloudWatch Logs Insights** queries to analyze invocation patterns
- Stream logs to **OpenSearch** for advanced analytics
- Use **EventBridge** to trigger actions based on S3 log delivery

## 7. Hands-On Labs

### Quick Setup (Console)
1. Open Amazon Bedrock console → **Settings**
2. Enable **Model invocation logging**
3. Select modalities: Text, Image, Embedding, Video
4. Choose destination: CloudWatch Logs / S3 / Both
5. Configure log group name and IAM role
6. Save settings

### CLI — Enable Logging
```bash
aws bedrock put-model-invocation-logging-configuration \
  --logging-config '{
    "cloudWatchConfig": {
      "logGroupName": "/aws/bedrock/modelinvocations",
      "roleArn": "arn:aws:iam::<account-id>:role/BedrockLoggingRole",
      "largeDataDeliveryS3Config": {
        "bucketName": "my-bedrock-logs",
        "keyPrefix": "large-data"
      }
    },
    "s3Config": {
      "bucketName": "my-bedrock-logs",
      "keyPrefix": "invocation-logs"
    },
    "textDataDeliveryEnabled": true,
    "imageDataDeliveryEnabled": true,
    "embeddingDataDeliveryEnabled": false
  }'
```

### CLI — Check Logging Configuration
```bash
aws bedrock get-model-invocation-logging-configuration
```

### CloudWatch Logs Insights — Sample Query
```sql
fields @timestamp, modelId, inputTokenCount, outputTokenCount
| filter modelId like /anthropic/
| stats sum(inputTokenCount) as totalInput, sum(outputTokenCount) as totalOutput by modelId
| sort totalOutput desc
```

## 8. Sample Questions

### Question 1
A company uses Amazon Bedrock to power a customer-facing chatbot. The compliance team requires that all prompts and model responses be logged for audit purposes. Which approach should the solutions architect recommend?

A) Enable AWS CloudTrail logging for the Bedrock service
B) Enable model invocation logging in Amazon Bedrock with CloudWatch Logs and S3 as destinations
C) Configure Amazon Bedrock Guardrails to log all interactions
D) Use Amazon SageMaker Model Monitor to track model inputs and outputs

**Correct Answer: B**

**Explanation**: Model invocation logging captures the full request data (prompts), response data (model outputs), and metadata for all Bedrock API calls. It supports CloudWatch Logs and S3 as destinations.
- A is wrong: CloudTrail logs API call metadata (who called what API) but does NOT capture the actual prompt/response content.
- C is wrong: Guardrails filter content for safety but are not designed for comprehensive audit logging of all interactions.
- D is wrong: SageMaker Model Monitor monitors data drift on SageMaker endpoints, not Amazon Bedrock invocations.

---

### Question 2
A data scientist notices that their Amazon Bedrock application is experiencing intermittent slowdowns. Which CloudWatch metric should they monitor to identify latency issues with streaming responses?

A) `Invocations`
B) `InvocationThrottles`
C) `TimeToFirstToken`
D) `InputTokenCount`

**Correct Answer: C**

**Explanation**: `TimeToFirstToken` measures the time from when a request is sent to when the first token is received, specifically for streaming API operations (ConverseStream, InvokeModelWithResponseStream). This is the most relevant metric for diagnosing latency in streaming responses.
- A is wrong: Invocations counts successful requests, not latency.
- B is wrong: InvocationThrottles counts throttled requests, which could cause slowdowns but doesn't measure latency directly.
- D is wrong: InputTokenCount measures token volume, not response time.

---

### Question 3
An organization enables Amazon Bedrock model invocation logging with CloudWatch Logs as the destination. Some log entries appear to be missing the full model response content. What is the most likely cause?

A) The IAM role does not have sufficient permissions
B) The model response exceeded 100 KB and was not delivered to CloudWatch Logs
C) CloudWatch Logs does not support logging model responses
D) Model invocation logging only captures input data, not output data

**Correct Answer: B**

**Explanation**: CloudWatch Logs supports input and output JSON bodies up to 100 KB in size. Binary data or JSON bodies larger than 100 KB are only delivered to Amazon S3. If no S3 destination is configured for large data delivery, the full response content will not be available.
- A is wrong: Permission issues would cause delivery failures, not partial content.
- C is wrong: CloudWatch Logs does support response logging (up to 100 KB).
- D is wrong: Model invocation logging captures both input and output data.

---

**Exam Tip**: Remember the three monitoring pillars for Bedrock: (1) **CloudTrail** for API audit trail (who/when), (2) **Model Invocation Logging** for content visibility (what was sent/received), and (3) **CloudWatch Metrics** for performance monitoring (how fast/how many). The exam loves testing the distinction between these three.

---

## Citations
- [Monitor model invocation using CloudWatch Logs and Amazon S3 - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-invocation-logging.html)
- [Model Invocations - Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/model-invocations.html)
- [Monitoring the performance of Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/monitoring.html)
