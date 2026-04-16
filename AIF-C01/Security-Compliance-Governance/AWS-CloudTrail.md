# AWS CloudTrail

## 1. Service/Topic Overview
- **Purpose**: Records and logs all API calls and account activity across your AWS environment, providing a complete audit trail for governance, compliance, and security monitoring
- **Service Level**: Regional (trails can be configured for all regions; organization trails span all accounts)
- **Key Use Cases**:
  - Auditing API calls to AI/ML services (Bedrock, SageMaker)
  - Compliance evidence for AI governance and regulatory requirements
  - Security monitoring — detecting unauthorized access to foundation models
  - Incident investigation — tracing who invoked a model, when, and from where
  - Operational troubleshooting — diagnosing failed API calls
- **Exam Weight**: Medium
- **Exam Domain**: Domain 5 — Security, Compliance, and Governance for AI Solutions (14%)
- **Task Statements**:
  - Task 5.1: Identify AWS services and features for securing AI solutions
  - Task 5.2: Recognize governance and compliance requirements for AI systems

## 2. Exam Keyword Mapping
- **Trigger Words**: "audit trail", "API logging", "who made the call", "track API activity", "compliance logging", "governance", "traceability", "account activity", "log management events"
- **Scenario Indicators**:
  - "A company needs to track who invoked a Bedrock model" → CloudTrail
  - "Audit all SageMaker API calls for compliance" → CloudTrail
  - "Detect unauthorized access to AI services" → CloudTrail + GuardDuty
  - "Maintain traceability for generative AI workloads" → CloudTrail
  - "Log model invocation requests for regulatory review" → CloudTrail (management events) + Bedrock Model Invocation Logging (request/response content)
- **Anti-patterns**:
  - Need to log **model input/output content** (prompts and responses) → Use **Bedrock Model Invocation Logging** (CloudTrail logs the API call metadata, NOT the prompt/response body)
  - Need **real-time performance metrics** (latency, token count, errors) → Use **Amazon CloudWatch**
  - Need to **monitor model drift or data quality** → Use **SageMaker Model Monitor**
  - Need to **evaluate model fairness/bias** → Use **SageMaker Clarify**

## 3. Core Concepts

### Four Event Types
| Event Type | Description | Logged by Default? | AI/ML Example |
|---|---|---|---|
| **Management Events** | Control plane operations (create, configure, delete resources) | ✅ Yes | `CreateAgent`, `CreateGuardrail`, `InvokeModel`, `CreateTrainingJob` |
| **Data Events** | Data plane operations (high-volume resource operations) | ❌ No (must enable) | `InvokeAgent`, `Retrieve`, `RetrieveAndGenerate`, `InvokeFlow` |
| **Insights Events** | Unusual API activity patterns (anomaly detection) | ❌ No (must enable) | Spike in `InvokeModel` calls indicating potential misuse |
| **Network Activity Events** | VPC endpoint activity | ❌ No (must enable) | API calls made through VPC endpoints |

### Key Components
- **Event History**: Free, 90-day searchable history of management events in the console
- **Trails**: Deliver events to S3 (and optionally CloudWatch Logs/SNS) for long-term storage; can be single-region or all-regions
- **Organization Trails**: Centralized logging across all accounts in AWS Organizations
- **CloudTrail Lake**: Managed data lake for querying events using SQL; integrates with Athena and Audit Manager

### Data Flow
```
API Call (Bedrock/SageMaker/etc.)
    → CloudTrail captures event (JSON log)
        → Event History (90 days, management events)
        → Trail → S3 Bucket (long-term storage)
                → CloudWatch Logs (real-time alerts)
                → SNS (notifications)
        → CloudTrail Lake (SQL queries)
        → EventBridge (automated responses)
```

### Integration Points
| Service | Integration |
|---|---|
| **Amazon S3** | Log file storage destination |
| **Amazon CloudWatch Logs** | Real-time monitoring and alerting on API activity |
| **Amazon EventBridge** | Trigger automated responses to CloudTrail events (e.g., alert on `DeleteGuardrail`) |
| **AWS Organizations** | Organization-wide trails across all member accounts |
| **Amazon Athena** | SQL queries on CloudTrail logs stored in S3 |
| **AWS Audit Manager** | CloudTrail Lake event data stores for compliance evidence |
| **Amazon GuardDuty** | Analyzes CloudTrail logs to detect suspicious AI/ML API activity |
| **AWS Config** | Tracks configuration changes (complementary to CloudTrail's API logging) |

## 4. Key Features & Components

### CloudTrail + Amazon Bedrock (Exam-Critical)
- **Management events logged by default**: `InvokeModel`, `InvokeModelWithResponseStream`, `Converse`, `ConverseStream`, `CreateAgent`, `CreateGuardrail`, `CreateKnowledgeBase`, `StopModelCustomizationJob`
- **Data events (must enable)**: `InvokeAgent`, `InvokeInlineAgent`, `Retrieve`, `RetrieveAndGenerate`, `InvokeFlow`
- **Bedrock data event resource types**: `AWS::Bedrock::AgentAlias`, `AWS::Bedrock::KnowledgeBase`, `AWS::Bedrock::FlowAlias`, `AWS::Bedrock::Guardrail`, `AWS::Bedrock::Model`
- **GuardDuty integration**: Automatically analyzes CloudTrail management events to detect suspicious Bedrock activity (e.g., removing guardrails from unusual location, changing training data S3 bucket)

> ⚠️ **Exam Gotcha**: CloudTrail logs **API call metadata** (who, when, what API, from where). It does NOT log the actual **prompt text or model response content**. For that, use **Bedrock Model Invocation Logging** which stores full request/response payloads to S3 or CloudWatch Logs.

### CloudTrail + Amazon SageMaker
- All SageMaker API operations are logged as management events by default
- Examples: `CreateTrainingJob`, `CreateEndpoint`, `CreateModel`, `CreatePipeline`, `UpdateEndpoint`
- CloudTrail logs include IAM Identity Center user mapping for auditing

### CloudTrail Lake
- Managed event data store with SQL query capability
- Retention: configurable (up to 7 years for paid, 90 days for free tier)
- Integrates with AWS Audit Manager for compliance evidence aggregation
- Supports querying across multiple fields (user, time, region, event name)

### CloudTrail Insights
- Detects unusual API call patterns (anomalies in call volume or error rates)
- Example: Sudden spike in `InvokeModel` calls could indicate compromised credentials or misuse
- Generates Insights events that can trigger EventBridge rules

### Log File Integrity
- CloudTrail can validate log file integrity using SHA-256 hashing
- Ensures logs haven't been tampered with — critical for compliance audits
- Log files are encrypted with SSE-S3 by default; can use SSE-KMS for additional control

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "How to audit who accessed a foundation model?" → CloudTrail
- **Scenario**: "How to detect unauthorized Bedrock API usage?" → CloudTrail + GuardDuty
- **Scenario**: "How to maintain compliance evidence for AI workloads?" → CloudTrail + Audit Manager
- **Comparison**: CloudTrail vs CloudWatch vs Bedrock Model Invocation Logging
- **Best practice**: "How to ensure traceability for generative AI services?" → CloudTrail trails + model invocation logging

### Gotchas
- CloudTrail is **enabled by default** on every AWS account (Event History = 90 days of management events for free)
- Creating a **trail** is needed for long-term storage, data events, and cross-region logging
- **Data events cost extra** and are NOT logged by default — must explicitly enable for Bedrock agents, knowledge bases, etc.
- CloudTrail logs the **API call** but NOT the **content** of model invocations (prompts/responses)
- **Event History** only shows management events for the last 90 days — not data events

### Comparison Points

| Feature | CloudTrail | CloudWatch | Bedrock Model Invocation Logging |
|---|---|---|---|
| **What it logs** | API call metadata (who, when, what, from where) | Metrics, logs, alarms (performance data) | Full prompt/response content + metadata |
| **Purpose** | Audit & compliance | Monitoring & alerting | AI-specific content auditing |
| **Default** | Management events ON | Must configure | Must enable |
| **Storage** | S3, CloudTrail Lake | CloudWatch Logs | S3, CloudWatch Logs |
| **AI/ML Focus** | Who called InvokeModel? | How many tokens? What latency? | What was the actual prompt and response? |

### Decision Tree
```
Need to know WHO called an AI API and WHEN?
  → CloudTrail ✅

Need to see the actual PROMPT and RESPONSE content?
  → Bedrock Model Invocation Logging ✅

Need real-time METRICS (latency, errors, token count)?
  → CloudWatch ✅

Need to detect SUSPICIOUS activity on AI services?
  → CloudTrail + GuardDuty ✅

Need COMPLIANCE EVIDENCE for auditors?
  → CloudTrail + Audit Manager ✅

Need to AUTOMATE responses to API events?
  → CloudTrail + EventBridge ✅
```

## 6. Best Practices

### Security
- Enable **multi-region trails** to capture activity across all regions
- Use **SSE-KMS** encryption for CloudTrail log files in S3
- Enable **log file validation** to detect tampering
- Restrict access to CloudTrail logs using S3 bucket policies and IAM
- Use **organization trails** for centralized logging across accounts

### Cost Optimization
- **First copy of management events** is free per region per trail
- Data events and additional copies of management events incur charges
- Use **CloudTrail Lake** selectively — queries incur costs based on data scanned
- Archive older logs to S3 Glacier for cost savings

### Operations
- Integrate with **CloudWatch Logs** for real-time alerting on critical API calls
- Set up **EventBridge rules** to automate responses (e.g., alert when `DeleteGuardrail` is called)
- Use **Athena** for ad-hoc SQL queries on historical CloudTrail logs in S3
- Regularly review **Insights events** for anomalous API patterns

## 7. Hands-On Labs

### Quick Setup: Enable CloudTrail for Bedrock Monitoring
1. Open CloudTrail console → **Trails** → **Create trail**
2. Name the trail, select S3 bucket for log storage
3. Enable **SSE-KMS encryption** and **log file validation**
4. Under **Events**, ensure **Management events** are enabled (Read + Write)
5. Optionally enable **Data events** → Select **Bedrock agent alias** and **Bedrock knowledge base**

### CLI: View Recent Bedrock Events
```bash
# Look up recent Bedrock InvokeModel events
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=EventSource,AttributeValue=bedrock.amazonaws.com \
  --max-results 10

# Create a trail for all Bedrock data events
aws cloudtrail put-event-selectors --trail-name my-ai-trail \
  --advanced-event-selectors '[
    {
      "Name": "Log Bedrock agent data events",
      "FieldSelectors": [
        { "Field": "eventCategory", "Equals": ["Data"] },
        { "Field": "resources.type", "Equals": ["AWS::Bedrock::AgentAlias"] }
      ]
    }
  ]'
```

### Sample CloudTrail Log Entry (Bedrock InvokeModel)
```json
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "IAMUser",
        "arn": "arn:aws:iam::111122223333:user/<user>",
        "accountId": "111122223333"
    },
    "eventTime": "2023-10-11T21:58:59Z",
    "eventSource": "bedrock.amazonaws.com",
    "eventName": "InvokeModel",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "192.0.2.0",
    "requestParameters": {
        "modelId": "anthropic.claude-3-sonnet-20240229-v1:0"
    },
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "eventCategory": "Management"
}
```

## 8. Sample Questions

### Question 1
A company uses Amazon Bedrock to power a customer-facing chatbot. The security team needs to determine which IAM users invoked foundation models during a specific time period for a compliance audit. Which AWS service should they use?

A) Amazon CloudWatch Logs
B) AWS CloudTrail
C) Amazon Bedrock Model Invocation Logging
D) AWS Config

**Correct Answer: B**

CloudTrail records all API calls including `InvokeModel` as management events by default. Each log entry includes the IAM identity (userIdentity), timestamp (eventTime), source IP, and the model ID. This is exactly what's needed for a compliance audit of who accessed what and when.
- A is wrong: CloudWatch provides metrics and operational logs, not API call audit trails with identity information.
- C is wrong: Model Invocation Logging captures the actual prompt/response content, not the identity-focused audit trail needed here.
- D is wrong: AWS Config tracks resource configuration changes, not API call history.

---

### Question 2
A machine learning team wants to be automatically notified whenever someone deletes a Bedrock Guardrail in their AWS account. Which combination of services should they use?

A) AWS CloudTrail + Amazon SNS
B) AWS CloudTrail + Amazon EventBridge + Amazon SNS
C) Amazon CloudWatch + Amazon SNS
D) AWS Config + AWS Lambda

**Correct Answer: B**

CloudTrail logs the `DeleteGuardrail` API call as a management event. EventBridge can create a rule that matches this specific CloudTrail event and triggers an SNS notification. This is the recommended pattern for automated responses to specific API calls.
- A is wrong: While CloudTrail can send notifications to SNS when new log files are delivered, it cannot filter for specific API calls — EventBridge provides the event filtering capability.
- C is wrong: CloudWatch monitors metrics and logs, not API call events for specific actions like deleting a guardrail.
- D is wrong: AWS Config tracks configuration state changes but is not the primary tool for real-time API call detection and notification.

---

### Question 3
A data scientist notices that CloudTrail logs show `InvokeModel` calls to Amazon Bedrock but the logs do not contain the actual prompts sent to the model. What should they enable to capture the full request and response content?

A) CloudTrail data events for Bedrock
B) Amazon Bedrock Model Invocation Logging
C) CloudWatch detailed monitoring
D) CloudTrail Insights

**Correct Answer: B**

Amazon Bedrock Model Invocation Logging is specifically designed to capture the full request (prompt) and response content from model invocations. CloudTrail only logs API call metadata (who, when, what API, from where) — it intentionally does NOT include the prompt/response body.
- A is wrong: Enabling data events would capture additional Bedrock API calls (like InvokeAgent), but still would not include the prompt/response content.
- C is wrong: CloudWatch detailed monitoring provides performance metrics, not model invocation content.
- D is wrong: CloudTrail Insights detects unusual API call patterns (anomalies), not request/response content.

---

**Exam Tip**: Remember the distinction: CloudTrail = "WHO did WHAT and WHEN" (API audit trail). Bedrock Model Invocation Logging = "WHAT was the actual prompt and response" (content logging). CloudWatch = "HOW is the service performing" (metrics and alarms). These three services are complementary for comprehensive AI/ML monitoring.

---

## Citations
- [CloudTrail Concepts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-concepts.html)
- [Monitor Amazon Bedrock API calls using CloudTrail](https://docs.aws.amazon.com/bedrock/latest/userguide/logging-using-cloudtrail.html)
- [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html)
- [GENSEC01-BP04: Implement Access Monitoring to Generative AI Services](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/gensec01-bp04.html)
