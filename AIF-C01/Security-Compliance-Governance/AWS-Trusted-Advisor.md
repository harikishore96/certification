# AWS Trusted Advisor

## 1. Service/Topic Overview
- **Purpose**: AWS Trusted Advisor inspects your AWS environment and makes recommendations to save money, improve system availability and performance, and help close security gaps — drawing upon best practices learned from serving hundreds of thousands of AWS customers
- **Service Level**: Global
- **Key Use Cases**:
  - Cost optimization (idle resources, underutilized instances, Reserved Instance optimization)
  - Security posture improvement (open ports, IAM usage, MFA on root account)
  - Performance optimization (overutilized resources, service limits approaching)
  - Fault tolerance (backups, Multi-AZ, redundancy checks)
  - Service limit monitoring (approaching AWS quotas)
  - Operational excellence (logging, monitoring configuration)
- **Exam Weight**: Low-Medium
- **Exam Domain**: Domain 5 — Security, Compliance, and Governance for AI Solutions (14%)
- **Task Statements**: Task 5.2 — Recognize AWS services and features for governance of AI systems

## 2. Exam Keyword Mapping
- **Trigger Words**: "best practices", "cost optimization", "security recommendations", "service limits", "automated checks", "environment inspection", "Well-Architected"
- **Scenario Indicators**:
  - "Need automated recommendations for improving AWS environment"
  - "Want to check security best practices across accounts"
  - "Need to monitor service quotas/limits"
  - "Want cost-saving recommendations"
  - "Need organizational-wide compliance visibility"
- **Anti-patterns**:
  - Deep security vulnerability scanning → use Amazon Inspector
  - Compliance audit evidence collection → use AWS Audit Manager
  - Sensitive data discovery → use Amazon Macie
  - Configuration compliance rules → use AWS Config
  - Centralized security findings → use AWS Security Hub

## 3. Core Concepts

### 6 Check Categories (Mnemonic: **C-P-S-F-S-O**)
| Category | Description | Example Checks |
|----------|-------------|----------------|
| **Cost Optimization** | Identify unused/underutilized resources | Idle RDS instances, underutilized EC2, unassociated Elastic IPs |
| **Performance** | Improve speed and responsiveness | Overutilized instances, CloudFront optimization |
| **Security** | Close security gaps | Open security groups, MFA on root, IAM access keys |
| **Fault Tolerance** | Increase resiliency | No backups, single-AZ RDS, EBS snapshots |
| **Service Limits** | Monitor approaching quotas | VPC limits, EC2 instance limits, EBS volume limits |
| **Operational Excellence** | Improve operational practices | Logging configuration, monitoring gaps |

### Access Tiers by Support Plan

| Feature | Basic/Developer | Business Support+ / Enterprise |
|---------|----------------|-------------------------------|
| Service Limits checks | ✅ All | ✅ All |
| Security checks | ⚠️ 6 core checks only | ✅ All |
| Cost Optimization | ❌ | ✅ All |
| Performance | ❌ | ✅ All |
| Fault Tolerance | ⚠️ 1 check only | ✅ All |
| Operational Excellence | ❌ | ✅ All |
| Trusted Advisor API | ❌ | ✅ |
| Auto-refresh | ❌ (manual only) | ✅ |
| CloudWatch Events integration | ❌ | ✅ |
| Organizational view | ❌ | ✅ |

### Basic/Developer Plan — Free Security Checks (6)
1. Amazon EBS Public Snapshots
2. Amazon RDS Public Snapshots
3. Amazon S3 Bucket Permissions
4. MFA on Root Account
5. Security Groups — Specific Ports Unrestricted
6. AWS STS Global Endpoint Usage

### Data Flow
```
AWS Environment → Trusted Advisor Checks → Findings (Green/Yellow/Red) → Recommendations → Action
```

### Integration Points
- **Amazon CloudWatch / EventBridge**: Monitor check status changes, trigger automated remediation
- **AWS Organizations**: Organizational view across all member accounts
- **AWS Config**: Config-powered checks for deeper compliance evaluation
- **AWS Security Hub**: View CSPM (Cloud Security Posture Management) controls
- **AWS Compute Optimizer**: Integration for right-sizing recommendations
- **Amazon SNS**: Weekly notification emails for check summaries

## 4. Key Features & Components

### Trusted Advisor Priority (Enterprise Support)
- Prioritized recommendations from AWS account team
- Contextual recommendations based on your specific environment
- Available only with Enterprise Support plan

### Organizational View
- Aggregate Trusted Advisor results across all AWS accounts in an organization
- Create and download reports
- Requires AWS Organizations integration
- Available with Business Support+ and Enterprise plans

### Check Statuses
| Status | Color | Meaning |
|--------|-------|---------|
| ✅ OK | Green | No issues detected |
| ⚠️ Investigation | Yellow | Possible issue, investigation recommended |
| 🔴 Action | Red | Action recommended immediately |
| ⬜ Excluded | Gray | Items excluded from checks |

### Automated Actions
- Checks auto-refresh weekly (Business+ and above)
- EventBridge rules can trigger Lambda for auto-remediation
- SNS notifications for status changes

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "Which service provides automated best practice recommendations across cost, security, and performance?" → Trusted Advisor
- **Comparison**: Trusted Advisor vs. AWS Config vs. Security Hub vs. Inspector
- **Access**: "Which checks are available on Basic Support plan?" → Service Limits + 6 core Security/Fault Tolerance checks
- **Integration**: "How to get organization-wide visibility?" → Organizational view with AWS Organizations

### Gotchas
- ❗ Basic/Developer plans only get limited checks (Service Limits + 6 security/fault tolerance)
- ❗ Full Trusted Advisor requires Business Support+ or higher
- ❗ Trusted Advisor is NOT a deep security scanner — that's Amazon Inspector
- ❗ Trusted Advisor does NOT enforce rules — it only recommends (AWS Config enforces)
- ❗ Auto-refresh is NOT available on Basic/Developer plans
- ❗ Trusted Advisor Priority is Enterprise Support only

### Comparison Points

| Feature | Trusted Advisor | AWS Config | Security Hub | Inspector |
|---------|----------------|------------|--------------|-----------|
| **Purpose** | Best practice recommendations | Configuration compliance | Centralized security findings | Vulnerability scanning |
| **Scope** | Cost, Security, Performance, Fault Tolerance, Limits, Ops | Resource configuration tracking | Security posture aggregation | Software vulnerabilities, network |
| **Action** | Recommends | Evaluates + remediates | Aggregates + prioritizes | Scans + reports |
| **AI/ML Relevance** | Service limits for ML resources, security checks | Track ML resource config changes | Aggregate ML security findings | Scan ML infrastructure |

### Decision Tree
- ✅ Use Trusted Advisor when: You need broad best-practice recommendations across cost, security, performance, and availability
- ✅ Use Trusted Advisor when: You want to monitor service quotas/limits
- ✅ Use Trusted Advisor when: You need organization-wide compliance overview
- ❌ Don't use when: You need deep vulnerability scanning (use Inspector)
- ❌ Don't use when: You need to enforce configuration rules (use AWS Config)
- ❌ Don't use when: You need to aggregate security findings from multiple tools (use Security Hub)
- ❌ Don't use when: You need to discover sensitive data (use Macie)

## 6. Best Practices

### Security
- Enable MFA on root account (Trusted Advisor checks this)
- Review security check results regularly
- Use IAM policies to control Trusted Advisor console access
- Act on red (action required) security findings immediately

### Cost Optimization
- Review cost optimization checks monthly
- Act on idle resource recommendations
- Use Reserved Instance optimization recommendations
- Monitor underutilized EC2 instances

### Performance
- Monitor service limits proactively to avoid throttling
- Right-size resources based on recommendations
- Use Compute Optimizer integration for detailed sizing

### Operations
- Enable organizational view for multi-account environments
- Set up EventBridge rules for automated responses to check status changes
- Configure weekly notification emails via SNS
- Integrate with AWS Systems Manager for automated remediation

## 7. Hands-On Labs

### Quick Setup
1. Navigate to AWS Trusted Advisor console: `https://console.aws.amazon.com/trustedadvisor`
2. Review dashboard showing all check categories
3. Click into each category to see individual check results

### CLI Commands
```bash
# List all Trusted Advisor recommendations (requires Business+ support)
aws trustedadvisor list-recommendations

# Get specific recommendation details
aws trustedadvisor get-recommendation --recommendation-identifier <id>

# List checks (using Support API)
aws support describe-trusted-advisor-checks --language en
```

### Practical Exercise
1. Open Trusted Advisor console
2. Review Security category — identify any red/yellow findings
3. Check Service Limits — note any approaching quotas
4. Set up EventBridge rule to notify on Trusted Advisor check status changes

## 8. Sample Questions

### Question 1
**A company wants to receive automated recommendations to reduce AWS costs, improve security posture, and optimize performance across their environment. Which AWS service should they use?**

A) AWS Config
B) Amazon Inspector
C) AWS Trusted Advisor
D) AWS Security Hub

**Answer: C) AWS Trusted Advisor**
- ✅ C is correct: Trusted Advisor provides automated best-practice recommendations across cost optimization, security, performance, fault tolerance, service limits, and operational excellence
- ❌ A: AWS Config tracks configuration compliance but doesn't provide cost or performance recommendations
- ❌ B: Amazon Inspector focuses only on vulnerability scanning, not cost or performance
- ❌ D: Security Hub aggregates security findings but doesn't cover cost optimization or performance

---

### Question 2
**A startup using the AWS Basic Support plan wants to check if their S3 buckets have overly permissive access. Can they use AWS Trusted Advisor for this?**

A) No, Trusted Advisor is only available with Enterprise Support
B) Yes, S3 Bucket Permissions is one of the free security checks available on all support plans
C) Yes, but only through the Trusted Advisor API
D) No, they must use Amazon Macie instead

**Answer: B)**
- ✅ B is correct: Amazon S3 Bucket Permissions is one of the 6 core security checks available on Basic and Developer Support plans at no additional cost
- ❌ A: Trusted Advisor is available on all plans, but with limited checks on Basic/Developer
- ❌ C: The Trusted Advisor API requires Business Support+ or higher
- ❌ D: While Macie can discover sensitive data, Trusted Advisor's S3 check is available for free

---

### Question 3
**An enterprise wants to view Trusted Advisor check results across all AWS accounts in their organization. What feature should they enable?**

A) AWS Config Aggregator
B) Trusted Advisor Priority
C) Trusted Advisor Organizational View
D) AWS Security Hub cross-account

**Answer: C) Trusted Advisor Organizational View**
- ✅ C is correct: Organizational View allows aggregating Trusted Advisor results across all accounts in an AWS Organization
- ❌ A: Config Aggregator aggregates AWS Config data, not Trusted Advisor checks
- ❌ B: Trusted Advisor Priority provides prioritized recommendations but is not specifically for cross-account aggregation
- ❌ D: Security Hub cross-account aggregates security findings, not Trusted Advisor checks

---

> **Exam Tip**: Remember the 6 check categories (**C-P-S-F-S-O**: Cost, Performance, Security, Fault tolerance, Service limits, Operational excellence) and that Basic/Developer plans only get Service Limits + 6 core security checks. For the AIF-C01, Trusted Advisor is most relevant for ensuring AI/ML workloads follow security best practices and don't hit service limits.

---

**Citations**:
- [AWS Trusted Advisor User Guide](https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor.html)
- [AWS Trusted Advisor Check Reference](https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor-check-reference.html)
- [Get Started with Trusted Advisor](https://docs.aws.amazon.com/awssupport/latest/user/get-started-with-aws-trusted-advisor.html)
