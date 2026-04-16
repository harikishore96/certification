# Amazon Inspector

> **Source**: [What is Amazon Inspector?](https://docs.aws.amazon.com/inspector/latest/user/what-is-inspector.html)

---

## 1. Service/Topic Overview

- **Purpose**: Automated vulnerability management service that continuously scans AWS workloads (EC2, ECR container images, Lambda functions) for software vulnerabilities and unintended network exposure
- **Service Level**: Regional
- **Key Use Cases**:
  - Continuous vulnerability scanning of EC2 instances, container images, and Lambda functions
  - Software composition analysis (SCA) for package dependencies
  - Network reachability analysis for EC2 instances
  - Centralized vulnerability management across multi-account AWS Organizations
  - Compliance and security posture assessment for AI/ML workloads
- **Exam Weight**: Medium
- **Exam Domain**: Domain 5 — Security, Compliance, and Governance for AI Solutions (14%)
- **Task Statements**:
  - Task 5.1: Identify AWS services and features for securing AI systems
  - Task 5.2: Recognize governance and compliance requirements for AI systems

---

## 2. Exam Keyword Mapping

- **Trigger Words**: "vulnerability scanning", "CVE", "software vulnerabilities", "network exposure", "security scanning", "container image scanning", "patch management", "code vulnerabilities"
- **Scenario Indicators**:
  - "Automatically scan EC2 instances for vulnerabilities" → Inspector
  - "Scan container images in ECR for security issues" → Inspector
  - "Identify software vulnerabilities in Lambda functions" → Inspector
  - "Continuous vulnerability assessment" → Inspector
  - "CVSS risk scoring" → Inspector
- **Anti-patterns**:
  - Compliance reporting/audit evidence → AWS Audit Manager
  - API activity logging → CloudTrail
  - Sensitive data discovery (PII in S3) → Amazon Macie
  - Configuration compliance checks → AWS Config
  - Centralized security findings aggregation → Security Hub
  - Infrastructure-as-code security scanning → not Inspector

---

## 3. Core Concepts

### Architectural Patterns
- **Agentless + Agent-based**: Uses AWS Systems Manager (SSM) agent for deep EC2 scanning; agentless for ECR and Lambda
- **Event-driven scanning**: Automatically rescans when new packages are installed, patches applied, or new CVEs published
- **Centralized management**: Delegated administrator via AWS Organizations for multi-account environments

### What Inspector Scans

| Resource Type | What It Scans | Scan Type |
|---|---|---|
| **Amazon EC2** | OS packages, software vulnerabilities, network reachability | Continuous |
| **Amazon ECR** | Container image OS packages and programming language packages | On push + continuous |
| **AWS Lambda** | Function code dependencies and application code vulnerabilities | Continuous |

### Data Flow
```
Resources (EC2/ECR/Lambda) → Amazon Inspector (auto-discovery + scanning)
    → Findings (with CVSS risk scores)
        → EventBridge (automation)
        → Security Hub (aggregation)
        → S3 (report export)
```

### Integration Points
- **AWS Security Hub**: Publishes findings for centralized security posture view
- **Amazon EventBridge**: Publishes finding events for automated remediation workflows
- **AWS Organizations**: Multi-account management via delegated administrator
- **Amazon ECR**: Native integration for container image scanning
- **AWS Systems Manager (SSM)**: Agent-based scanning for EC2 instances
- **Amazon S3**: Export findings reports (CSV/JSON)
- **AWS KMS**: Encryption of exported reports and code snippets in findings

---

## 4. Key Features & Components

### Exam-Tested Features
- **Continuous scanning**: No manual scheduling needed — automatically discovers and scans eligible resources
- **Amazon Inspector Risk Score**: Context-aware CVSS score adjusted for your environment (e.g., lowers score if no internet-facing network path exists)
- **Finding types**:
  - **Package vulnerability**: CVEs in OS and programming language packages
  - **Code vulnerability**: Security issues in Lambda function code
  - **Network reachability**: Unintended network exposure on EC2
- **Suppression rules**: Filter out unwanted findings from views
- **Findings reports**: Export in CSV or JSON format to S3

### Configuration Options
- Enable/disable scanning per resource type (EC2, ECR, Lambda)
- Configure ECR scan frequency (on push, continuous)
- Set up delegated administrator for Organizations
- Create suppression rules and filters

### Key Integrations for AI/ML Context
- Scan **SageMaker notebook instances** (EC2-based) for OS vulnerabilities
- Scan **container images** used in SageMaker training/inference jobs (via ECR)
- Scan **Lambda functions** used in AI/ML inference pipelines
- Feed findings into **Security Hub** for holistic AI workload security posture

---

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "Which service automatically scans EC2 instances and container images for software vulnerabilities?" → Amazon Inspector
- **Comparison**: Inspector vs. Macie vs. GuardDuty vs. Config
- **Integration**: How Inspector works with Security Hub and EventBridge
- **AI/ML Security**: Securing ML infrastructure (containers, compute, Lambda)

### Gotchas
- Inspector scans for **software vulnerabilities** (CVEs) — NOT for sensitive data (that's Macie)
- Inspector does **NOT** scan S3 buckets — it scans EC2, ECR, and Lambda
- Inspector is **NOT** the same as GuardDuty — GuardDuty detects threats/anomalous activity; Inspector finds vulnerabilities
- Inspector **automatically** rescans — no need to schedule assessments
- Inspector Risk Score ≠ NVD CVSS score — it's contextually adjusted

### Comparison Points

| Feature | Amazon Inspector | Amazon Macie | Amazon GuardDuty | AWS Config |
|---|---|---|---|---|
| **Primary Focus** | Vulnerability scanning | Sensitive data discovery | Threat detection | Configuration compliance |
| **What It Scans** | EC2, ECR, Lambda | S3 buckets | VPC Flow Logs, DNS, CloudTrail | AWS resource configurations |
| **Finding Type** | CVEs, network exposure | PII, sensitive data | Threats, anomalies | Config rule violations |
| **Scanning Style** | Continuous, automated | Scheduled/on-demand | Continuous monitoring | Continuous/periodic |
| **AI/ML Relevance** | Scan ML infrastructure | Find PII in training data | Detect unauthorized access | Ensure compliant configs |

### Decision Trees
- ✅ **Use Inspector when**: You need to find software vulnerabilities in compute resources (EC2, containers, Lambda)
- ✅ **Use Inspector when**: You need continuous, automated vulnerability assessment with risk scoring
- ✅ **Use Inspector when**: You need to scan container images used in ML training/inference
- ❌ **Don't use Inspector when**: You need to find sensitive data in S3 → use **Macie**
- ❌ **Don't use Inspector when**: You need to detect active threats or anomalous behavior → use **GuardDuty**
- ❌ **Don't use Inspector when**: You need to check resource configuration compliance → use **AWS Config**
- ❌ **Don't use Inspector when**: You need audit evidence and compliance reports → use **AWS Audit Manager**

---

## 6. Best Practices

### Security
- Enable Inspector across all accounts via AWS Organizations
- Integrate with Security Hub for centralized findings view
- Use EventBridge rules to automate remediation (e.g., trigger Lambda to patch)
- Encrypt exported findings reports with AWS KMS customer-managed keys
- Ensure SSM Agent is installed and running on EC2 instances for full scanning coverage

### Cost Optimization
- Inspector pricing is based on number of resources scanned (EC2 instances, container images, Lambda functions)
- Use suppression rules to reduce noise from low-priority findings
- Focus scanning on production and internet-facing workloads first
- 15-day free trial available for new accounts

### Performance
- Minimal performance impact on scanned resources
- Agentless scanning for ECR and Lambda — no compute overhead
- SSM-based scanning for EC2 — lightweight agent

### Operations
- Monitor scan coverage via Inspector dashboard
- Export findings regularly to S3 for historical tracking
- Set up EventBridge notifications for critical/high severity findings
- Review and remediate findings based on Inspector Risk Score priority

---

## 7. Hands-On Labs

### Quick Setup
1. Open Amazon Inspector console
2. Click **Get Started** → **Enable Inspector**
3. Inspector automatically discovers and begins scanning eligible resources

### CLI Commands
```bash
# Enable Inspector for your account
aws inspector2 enable --resource-types EC2 ECR LAMBDA

# List findings
aws inspector2 list-findings

# List findings filtered by severity
aws inspector2 list-findings --filter-criteria '{"severity": [{"comparison": "EQUALS", "value": "CRITICAL"}]}'

# Get scan coverage
aws inspector2 list-coverage

# Export findings report to S3
aws inspector2 create-findings-report \
    --report-format CSV \
    --s3-destination bucketName=my-bucket,keyPrefix=inspector-reports,kmsKeyArn=arn:aws:kms:us-east-1:123456789012:key/my-key
```

### Practical Exercise
1. Launch an EC2 instance with an outdated AMI
2. Enable Inspector → observe automatic scanning
3. Review findings in the Inspector console dashboard
4. Push a container image to ECR → observe automatic vulnerability scan
5. Check findings in Security Hub for aggregated view

---

## 8. Sample Questions

### Question 1
**A company is deploying ML models using Amazon SageMaker with custom Docker containers stored in Amazon ECR. The security team wants to automatically identify software vulnerabilities in these container images before they are used in production. Which AWS service should they use?**

A) Amazon GuardDuty
B) Amazon Macie
C) Amazon Inspector
D) AWS Config

**Correct Answer: C) Amazon Inspector**

- ✅ **C**: Amazon Inspector automatically scans container images in Amazon ECR for known software vulnerabilities (CVEs) in OS packages and programming language dependencies
- ❌ **A**: GuardDuty detects active threats and anomalous activity, not software vulnerabilities in container images
- ❌ **B**: Macie discovers sensitive data (PII) in S3 buckets, not container vulnerabilities
- ❌ **D**: AWS Config checks resource configuration compliance, not software vulnerabilities

---

### Question 2
**An AI team wants to receive automated notifications whenever a critical vulnerability is found in their EC2-based SageMaker notebook instances. Which combination of services should they use?**

A) Amazon Inspector + Amazon SNS directly
B) Amazon Inspector + Amazon EventBridge + Amazon SNS
C) AWS Config + Amazon CloudWatch + Amazon SNS
D) Amazon GuardDuty + AWS Lambda + Amazon SES

**Correct Answer: B) Amazon Inspector + Amazon EventBridge + Amazon SNS**

- ✅ **B**: Inspector publishes findings to EventBridge as events. EventBridge rules can filter for critical severity and route to SNS for notifications
- ❌ **A**: Inspector doesn't publish directly to SNS — it uses EventBridge as the event bus
- ❌ **C**: AWS Config checks configuration compliance, not software vulnerabilities
- ❌ **D**: GuardDuty detects threats, not software vulnerabilities in instances

---

### Question 3
**A data science team needs to ensure their AWS infrastructure is secure. They need to: (1) scan EC2 instances for software vulnerabilities, (2) detect sensitive data in S3 training datasets, and (3) monitor for unauthorized API calls. Which combination of services addresses ALL three requirements?**

A) Amazon Inspector, Amazon Macie, Amazon GuardDuty
B) Amazon Inspector, AWS Config, AWS CloudTrail
C) Amazon GuardDuty, Amazon Macie, AWS Config
D) Amazon Inspector, Amazon Macie, AWS Config

**Correct Answer: A) Amazon Inspector, Amazon Macie, Amazon GuardDuty**

- ✅ **A**: Inspector scans EC2 for vulnerabilities, Macie discovers sensitive data in S3, GuardDuty monitors for unauthorized/anomalous API activity
- ❌ **B**: Config checks configuration compliance, not sensitive data; CloudTrail logs API calls but doesn't detect unauthorized patterns
- ❌ **C**: GuardDuty doesn't scan for software vulnerabilities; Config doesn't detect sensitive data
- ❌ **D**: Config doesn't detect sensitive data in S3 or monitor for unauthorized API calls

---

**Exam Tip**: For AIF-C01, remember the "security service trifecta" — **Inspector** (vulnerability scanning), **Macie** (sensitive data), **GuardDuty** (threat detection). Know which service handles which security concern, especially in the context of securing AI/ML workloads and data pipelines.
