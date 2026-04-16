# AWS Config

## 1. Service/Topic Overview
- **Purpose**: Continuously records and evaluates AWS resource configurations for compliance, governance, and change management
- **Service Level**: Regional (with multi-account, multi-region aggregation)
- **Key Use Cases**:
  - Track configuration changes to AI/ML resources (SageMaker, Bedrock)
  - Evaluate compliance of ML infrastructure against security baselines
  - Audit resource configurations for regulatory compliance
  - Detect resource misconfigurations and auto-remediate
  - Maintain historical configuration records for troubleshooting
- **Exam Weight**: Medium
- **Exam Domain**: Domain 5: Security, Compliance, and Governance for AI Solutions (14%)
- **Task Statements**: Task 5.3 — Understand governance and compliance regulations for AI systems

## 2. Exam Keyword Mapping
- **Trigger Words**: "configuration compliance", "resource tracking", "configuration history", "compliance rules", "conformance pack", "configuration drift", "noncompliant resources", "governance baseline"
- **Scenario Indicators**:
  - "Ensure SageMaker notebooks don't have direct internet access" → AWS Config rule
  - "Continuously evaluate resource configurations against policies" → AWS Config
  - "Track configuration changes over time" → AWS Config
  - "Enforce security baselines for AI/ML workloads" → AWS Config conformance packs
  - "Detect when a resource violates a compliance rule" → AWS Config rules
- **Anti-patterns**:
  - Need to track API calls/who made changes → **AWS CloudTrail** (not Config)
  - Need centralized security findings dashboard → **AWS Security Hub** (aggregates Config findings)
  - Need vulnerability scanning → **Amazon Inspector**
  - Need compliance reports/certifications → **AWS Artifact**

## 3. Core Concepts

### How AWS Config Works
```
AWS Resources → Configuration Recorder → Configuration Items (CIs)
                                              ↓
                                    AWS Config Rules (evaluate)
                                              ↓
                              Compliant / Noncompliant → SNS Notification
                                              ↓
                                    Remediation Actions (auto-fix)
                                              ↓
                              Configuration History → S3 Bucket
```

### Key Components
| Component | Description |
|-----------|-------------|
| **Configuration Recorder** | Records resource configurations; must be enabled to start tracking |
| **Configuration Item (CI)** | Point-in-time snapshot of a resource's attributes, relationships, and configuration |
| **Config Rules** | Desired configuration settings; evaluates CIs for compliance (managed or custom) |
| **Conformance Packs** | Collection of Config rules deployed as a single entity (e.g., AI/ML security baseline) |
| **Aggregator** | Collects Config data across multiple accounts and regions into one view |
| **Remediation Actions** | Auto-fix noncompliant resources using SSM Automation documents |
| **Configuration Snapshot** | Complete configuration of all recorded resources at a point in time |
| **Configuration History** | Collection of CIs for a resource over time (delivered to S3 every 6 hours) |

### Service Requirements
- **S3 Bucket** — stores configuration snapshots and history files (JSON format)
- **SNS Topic** — sends compliance change notifications
- **IAM Role** — grants Config permissions to access resources, S3, and SNS

### Integration Points
| Service | Integration |
|---------|-------------|
| **AWS Security Hub** | Aggregates Config compliance findings into centralized dashboard |
| **AWS CloudTrail** | Config tracks *what* changed; CloudTrail tracks *who* changed it |
| **AWS Systems Manager** | Remediation actions use SSM Automation runbooks |
| **Amazon S3** | Stores configuration history and snapshots |
| **Amazon SNS** | Delivers compliance change notifications |
| **AWS Lambda** | Powers custom Config rule evaluation logic |
| **AWS Organizations** | Multi-account Config aggregation and conformance pack deployment |

## 4. Key Features & Components

### Config Rules (Exam-Critical)
| Rule Type | Description | Trigger |
|-----------|-------------|---------|
| **AWS Managed Rules** | Pre-built rules maintained by AWS (300+) | Configuration change or periodic |
| **Custom Lambda Rules** | Custom evaluation logic in Lambda functions | Configuration change or periodic |
| **Custom Policy Rules** | Written in CloudFormation Guard (no Lambda needed) | Configuration change |

### AI/ML-Specific Config Rules
| Rule | What It Checks |
|------|---------------|
| `sagemaker-notebook-no-direct-internet-access` | SageMaker notebooks don't have direct internet access |
| `sagemaker-notebook-instance-kms-key-configured` | SageMaker notebooks use KMS encryption |
| `sagemaker-notebook-instance-root-access-check` | Root access is disabled on SageMaker notebooks |
| `sagemaker-endpoint-configuration-kms-key-configured` | SageMaker endpoints use KMS encryption |
| `sagemaker-notebook-instance-inside-vpc` | SageMaker notebooks are deployed in a VPC |
| `sagemaker-featuregroup-description` | SageMaker FeatureGroups have descriptions |

### AI/ML Conformance Packs
AWS Config provides pre-built conformance packs specifically for AI/ML governance:

1. **Amazon Bedrock Security and Governance Best Practices** — Baseline security configurations for Bedrock AI workloads (generative AI, agentic AI)
2. **Amazon SageMaker AI Security and Governance Best Practices** — Baseline security for SageMaker ML workloads
3. **Self-Hosted AI/ML Security & Governance Best Practices** — Security baseline for self-hosted AI/ML on AWS compute/storage/networking
4. **AI/ML Security & Governance Supporting Infrastructure Best Practices** — Companion pack for underlying infrastructure

### Evaluation Triggers
| Trigger | When |
|---------|------|
| **Configuration change** | When a recorded resource is created, changed, or deleted |
| **Periodic** | At a frequency you specify (e.g., every 1, 3, 6, 12, or 24 hours) |

### Multi-Account Aggregation
- **Aggregator** collects data from multiple accounts and regions
- Provides centralized compliance view across an organization
- Works with AWS Organizations for automatic account discovery

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "How to ensure all SageMaker notebooks are encrypted?" → AWS Config managed rule
- **Scenario**: "How to continuously monitor AI/ML resource compliance?" → AWS Config rules + conformance packs
- **Comparison**: "What tracks configuration changes vs. API calls?" → Config (configuration) vs. CloudTrail (API calls)
- **Best Practice**: "How to enforce security baselines across AI workloads?" → AWS Config conformance packs

### Gotchas
- AWS Config records **what** the configuration is; CloudTrail records **who** made the change
- Config is **regional** — you must enable it in each region (or use aggregators for cross-region view)
- Config rules evaluate compliance but do NOT prevent changes (they detect and report; use SCPs to prevent)
- Configuration history files are delivered to S3 every **6 hours** (not real-time)
- Config must be **enabled** (configuration recorder started) before it can track anything

### Comparison Points

| Feature | AWS Config | AWS CloudTrail | AWS Security Hub |
|---------|-----------|---------------|-----------------|
| **Primary Purpose** | Resource configuration compliance | API activity logging | Centralized security findings |
| **Tracks** | What resources look like (configuration state) | Who did what (API calls) | Aggregated findings from multiple services |
| **Rules/Checks** | Config Rules & Conformance Packs | N/A | Security standards & controls |
| **Remediation** | Auto-remediation via SSM | N/A | N/A (delegates to source) |
| **AI/ML Focus** | SageMaker/Bedrock conformance packs | Logs Bedrock InvokeModel calls | Aggregates Config + Inspector findings |

### Decision Tree
- ✅ **Use AWS Config when**: You need to continuously evaluate resource configurations against compliance rules
- ✅ **Use AWS Config when**: You need configuration history and change tracking for AI/ML resources
- ✅ **Use AWS Config when**: You need to enforce security baselines with conformance packs for Bedrock/SageMaker
- ✅ **Use AWS Config when**: You need auto-remediation of noncompliant resources
- ❌ **Don't use Config when**: You need to track who made API calls → Use **CloudTrail**
- ❌ **Don't use Config when**: You need a centralized security dashboard → Use **Security Hub**
- ❌ **Don't use Config when**: You need to prevent actions → Use **IAM/SCPs**

## 6. Best Practices

### Security
- Enable Config in **all regions** to detect unauthorized resource creation
- Use **conformance packs** for AI/ML workloads (Bedrock, SageMaker, self-hosted)
- Encrypt configuration snapshots in S3 with **KMS**
- Restrict access to Config using **IAM policies**
- Integrate with **Security Hub** for centralized compliance visibility

### Cost Optimization
- Record only the **resource types you need** (don't record all by default if unnecessary)
- Use **periodic evaluation** instead of change-triggered for non-critical rules
- Manage S3 lifecycle policies to archive/delete old configuration history

### Operations
- Use **aggregators** for multi-account, multi-region visibility
- Set up **SNS notifications** for compliance changes
- Enable **auto-remediation** for critical noncompliant resources
- Use **advanced queries** (SQL-like) to query current resource configurations

## 7. Hands-On Labs

### Quick Setup (Console)
1. Navigate to **AWS Config** → **Get started**
2. Choose resource types to record (or record all)
3. Specify S3 bucket for configuration history
4. Optionally configure SNS topic for notifications
5. Choose Config rules to enable

### CLI — Check SageMaker Notebook Compliance
```bash
# Enable a managed rule for SageMaker notebook internet access
aws configservice put-config-rule --config-rule '{
  "ConfigRuleName": "sagemaker-no-direct-internet",
  "Source": {
    "Owner": "AWS",
    "SourceIdentifier": "SAGEMAKER_NOTEBOOK_NO_DIRECT_INTERNET_ACCESS"
  }
}'

# Check compliance status
aws configservice get-compliance-details-by-config-rule \
  --config-rule-name sagemaker-no-direct-internet

# Get configuration history for a resource
aws configservice get-resource-config-history \
  --resource-type AWS::SageMaker::NotebookInstance \
  --resource-id my-notebook
```

### Deploy AI/ML Conformance Pack
```bash
# Deploy Bedrock security conformance pack
aws configservice put-conformance-pack \
  --conformance-pack-name "bedrock-security-baseline" \
  --template-s3-uri "s3://my-bucket/Amazon-Bedrock-Security-and-Governance-Best-Practices.yaml"
```

## 8. Sample Questions

### Question 1
A company uses Amazon SageMaker to train ML models. The security team needs to ensure that all SageMaker notebook instances are encrypted with KMS keys and do not have direct internet access. Which AWS service should they use to continuously monitor and enforce these requirements?

A) AWS CloudTrail
B) AWS Config
C) Amazon Inspector
D) AWS Trusted Advisor

**Correct Answer: B**
- **B is correct**: AWS Config rules can continuously evaluate SageMaker notebook configurations against desired settings (KMS encryption, no direct internet access) and flag noncompliant resources.
- A is wrong: CloudTrail tracks API calls, not resource configuration compliance.
- C is wrong: Inspector scans for software vulnerabilities, not configuration compliance.
- D is wrong: Trusted Advisor provides general best practice recommendations, not continuous configuration compliance monitoring.

---

### Question 2
An organization wants to establish a security baseline for all their Amazon Bedrock generative AI workloads across multiple AWS accounts. Which AWS Config feature should they use?

A) Config Rules
B) Configuration Recorder
C) Conformance Packs
D) Advanced Queries

**Correct Answer: C**
- **C is correct**: AWS Config conformance packs (specifically the "Amazon Bedrock Security and Governance Best Practices" pack) provide a collection of pre-built rules that can be deployed as a single entity across multiple accounts to establish a security baseline.
- A is wrong: Individual Config rules would work but don't provide a pre-packaged baseline for Bedrock.
- B is wrong: Configuration Recorder records configurations but doesn't evaluate compliance.
- D is wrong: Advanced Queries let you query current configurations but don't enforce baselines.

---

### Question 3
A data scientist notices that a SageMaker endpoint configuration was changed, causing model inference failures. They need to view the previous configuration to troubleshoot. Which service provides this historical configuration data?

A) AWS CloudTrail
B) Amazon CloudWatch
C) AWS Config
D) AWS X-Ray

**Correct Answer: C**
- **C is correct**: AWS Config maintains configuration history for recorded resources, allowing you to view previous configurations and compare changes over time.
- A is wrong: CloudTrail shows who made the API call but not the full resource configuration state.
- B is wrong: CloudWatch monitors metrics and logs, not resource configurations.
- D is wrong: X-Ray traces application requests, not resource configurations.

---

## Citations
- [What Is AWS Config?](https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html)
- [How AWS Config Works](https://docs.aws.amazon.com/config/latest/developerguide/how-does-config-work.html)
- [Amazon Bedrock Security and Governance Best Practices](https://docs.aws.amazon.com/config/latest/developerguide/amazon-bedrock-security-and-governance-best-practices.html)
- [Amazon SageMaker AI Security and Governance Best Practices](https://docs.aws.amazon.com/config/latest/developerguide/amazon-sagemaker-ai-security-and-governance-best-practices.html)
- [Self-Hosted AI/ML Security & Governance Best Practices](https://docs.aws.amazon.com/config/latest/developerguide/self-hosted-AI-ML-security-governance-best-practices.html)
