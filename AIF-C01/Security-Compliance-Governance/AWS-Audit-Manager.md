# AWS Audit Manager

## 1. Service/Topic Overview
- **Purpose**: Continuously audit your AWS usage to simplify risk assessment and compliance with regulations and industry standards by automating evidence collection
- **Service Level**: Regional
- **Key Use Cases**:
  - Automated evidence collection for compliance audits (HIPAA, PCI DSS, SOC 2, GDPR)
  - Assessing generative AI implementations against AWS best practices (Bedrock & SageMaker)
  - Multi-account compliance posture reporting via AWS Organizations
  - Generating audit-ready assessment reports with linked evidence
  - Custom framework creation for organization-specific compliance requirements
- **Exam Weight**: Medium
- **Exam Domain**: Domain 5 — Security, Compliance, and Governance for AI Solutions (14%)
- **Task Statements**:
  - Task 5.2: Recognize governance and compliance regulations for AI systems
  - Task 5.3: Identify methods and tools for auditing AI systems

> ⚠️ **Important**: AWS Audit Manager is transitioning to **maintenance mode**. From **April 30, 2026**, new customers will no longer be able to set up the service. Existing customers retain full access. AWS recommends exploring **AWS Config Conformance Packs** as an alternative.

---

## 2. Exam Keyword Mapping

### Trigger Words
- "Audit compliance", "evidence collection", "compliance framework"
- "Assessment report", "audit readiness", "regulatory compliance"
- "Generative AI governance", "AI compliance audit"
- "SOC 2", "HIPAA", "PCI DSS", "GDPR" + audit automation
- "Continuous compliance monitoring"

### Scenario Indicators
- "Automatically collect evidence for compliance audits"
- "Demonstrate compliance with industry standards"
- "Audit generative AI usage on Bedrock/SageMaker"
- "Generate assessment reports for auditors"
- "Track compliance posture across multiple AWS accounts"
- "Prebuilt compliance framework for AI best practices"

### Anti-patterns
- ❌ Real-time threat detection → Use **Amazon GuardDuty**
- ❌ Automated remediation of non-compliant resources → Use **AWS Config Rules** with auto-remediation
- ❌ Security posture scoring → Use **AWS Security Hub**
- ❌ Detecting model bias or fairness issues → Use **SageMaker Clarify**
- ❌ Content filtering for AI outputs → Use **Bedrock Guardrails**
- ❌ Infrastructure compliance checks only → Use **AWS Config Conformance Packs**

---

## 3. Core Concepts

### Architectural Patterns
```
┌─────────────────────────────────────────────────────────┐
│                    AWS Audit Manager                     │
│                                                         │
│  Framework (Standard/Custom)                            │
│    └── Control Sets                                     │
│         └── Controls                                    │
│              └── Evidence Sources                       │
│                   ├── AWS Config Rules                  │
│                   ├── AWS CloudTrail Logs               │
│                   ├── AWS Security Hub Findings         │
│                   ├── AWS API Calls                     │
│                   └── Manual Evidence (uploaded)        │
│                                                         │
│  Assessment                                             │
│    ├── Scope: AWS Accounts + Regions                    │
│    ├── Automated Evidence Collection (continuous)       │
│    └── Assessment Report → S3 Bucket                    │
└─────────────────────────────────────────────────────────┘
```

### Key Terminology
| Term | Definition |
|------|-----------|
| **Assessment** | An ongoing evaluation that automatically collects evidence based on a framework's controls |
| **Framework** | A grouping of controls related to a compliance standard (standard or custom) |
| **Control** | A safeguard or requirement that collects evidence to demonstrate compliance |
| **Evidence** | A record (automated or manual) that demonstrates compliance with a control |
| **Control Set** | A group of related controls within a framework |
| **Assessment Report** | A finalized document summarizing collected evidence, stored in S3 |
| **Audit Owner** | User/role that manages assessments and generates reports |
| **Delegate** | Subject matter expert assigned to review specific control sets |

### Control Types
| Type | Description | Editable? |
|------|-------------|-----------|
| **Common Control** | Cross-standard action (e.g., "Access controls") — supports overlapping compliance obligations | No (use as evidence source in custom controls) |
| **Core Control** | Prescriptive AWS guideline (e.g., "Block public read access in S3") | No (use as evidence source in custom controls) |
| **Standard Control** | Prebuilt control tied to a specific compliance framework | No (can make editable copy) |
| **Custom Control** | User-created control with custom evidence sources | Yes |

### Evidence Sources (Data Sources)
| Source Type | Description |
|-------------|-------------|
| **AWS Config** | Configuration compliance checks via Config Rules |
| **AWS CloudTrail** | API activity logs for audit trails |
| **AWS Security Hub** | CSPM (Cloud Security Posture Management) findings |
| **AWS API Calls** | Direct API calls to target services for snapshots |
| **Manual** | User-uploaded evidence (documents, screenshots, records) |

### Integration Points
- **AWS Organizations** → Multi-account assessments with delegated admin
- **AWS Config** → Automated compliance rule evidence
- **AWS Security Hub** → Security findings as evidence
- **AWS CloudTrail** → API activity audit trails
- **Amazon S3** → Assessment report storage destination
- **AWS KMS** → Encryption of assessment data and reports
- **Amazon Bedrock** → GenAI best practices framework evidence
- **Amazon SageMaker** → ML best practices framework evidence
- **AWS CloudWatch** → Log monitoring for AI/ML services

---

## 4. Key Features & Components

### Exam-Tested Features

#### Pre-built Compliance Frameworks (29+)
Key frameworks for AIF-C01:
| Framework | AI/ML Relevance |
|-----------|----------------|
| **AWS Generative AI Best Practices Framework v2** | ⭐ Directly tests GenAI governance on Bedrock & SageMaker |
| HIPAA Security Rule | Healthcare AI data compliance |
| PCI DSS v4.0 | Financial AI data compliance |
| SOC 2 (SSAE-18) | Service organization controls |
| GDPR 2016 | Data privacy for AI systems |
| NIST SP 800-53 Rev 5 | Federal security controls |
| ISO/IEC 27001:2013 | Information security management |
| AWS Well-Architected Framework WAF v10 | Cloud architecture best practices |

#### AWS Generative AI Best Practices Framework v2 (⭐ Key for AIF-C01)
Evaluates GenAI implementations against **8 principles**:

| # | Principle | What It Assesses |
|---|-----------|-----------------|
| 1 | **Responsible** | Ethical guidelines for AI deployment and usage |
| 2 | **Safe** | Parameters and boundaries to prevent harmful output |
| 3 | **Fair** | Impact on different sub-populations of users |
| 4 | **Sustainable** | Efficiency and sustainable resource usage |
| 5 | **Resilience** | Integrity and availability of AI systems |
| 6 | **Privacy** | Protection of sensitive data from theft/exposure |
| 7 | **Accuracy** | Reliability and robustness of AI systems |
| 8 | **Secure** | Prevention of unauthorized access to AI systems |

> 📝 **Exam Tip**: The GenAI Best Practices Framework v2 was developed in collaboration with AI experts, compliance practitioners, security assurance specialists at AWS, and with input from **Deloitte**.

#### Automated Evidence Collection
- Continuous collection once an assessment is created
- Evidence converted to auditor-friendly format automatically
- Evidence attached to controls in the assessment
- Can stop collection by setting assessment/control to *inactive*

#### Assessment Reports
- Finalized documents generated from assessments
- Link to evidence folders organized by controls
- Stored in a designated S3 bucket
- Audit owner selects which evidence to include

#### Delegation Model
- **Audit Owner**: Creates assessments, reviews evidence, generates reports
- **Delegate**: Reviews specific control sets (subject matter experts)
- Separation of duties via IAM policies

### Configuration Options
- **Delegated Administrator**: Set up via AWS Organizations for multi-account management
- **KMS Encryption**: Custom KMS key for encrypting assessment data
- **Evidence Finder**: Search and filter collected evidence
- **Framework Sharing**: Share custom frameworks across accounts/organizations

---

## 5. Exam Focus Areas

### Common Question Types

#### Scenario-based Selection
> "A company wants to automatically collect evidence that their generative AI application on Amazon Bedrock follows responsible AI practices. Which service should they use?"
> → **AWS Audit Manager** with the AWS Generative AI Best Practices Framework v2

#### Feature Comparison
> "What is the difference between AWS Audit Manager and AWS Config for compliance?"
> - Audit Manager: Collects evidence from multiple sources (Config, CloudTrail, Security Hub, API calls), generates assessment reports
> - AWS Config: Records configuration changes, evaluates compliance rules, but doesn't generate audit reports

#### Best Practice Questions
> "How should a company audit their AI/ML workloads for compliance with multiple standards?"
> → Create assessments using relevant standard frameworks + custom frameworks in Audit Manager

### Gotchas
- ⚠️ Audit Manager **does NOT remediate** non-compliant resources — it only collects evidence
- ⚠️ Audit Manager **does NOT replace** an external auditor — it helps prepare for audits
- ⚠️ Evidence collection is **continuous** once started — must explicitly set to inactive to stop
- ⚠️ The GenAI framework v2 covers both **Bedrock AND SageMaker** (v1 was Bedrock only)
- ⚠️ Audit Manager is entering **maintenance mode** (April 2026) — no new features or frameworks
- ⚠️ Manual evidence must be uploaded by users — not all controls can be fully automated

### Comparison Points

#### AWS Audit Manager vs AWS Config
| Feature | Audit Manager | AWS Config |
|---------|--------------|------------|
| **Purpose** | Audit preparation & evidence collection | Resource configuration tracking & compliance |
| **Evidence Sources** | Config, CloudTrail, Security Hub, API calls, Manual | Configuration Items (CIs) only |
| **Assessment Reports** | ✅ Yes — audit-ready reports | ❌ No equivalent |
| **Compliance Frameworks** | 29+ prebuilt frameworks | Conformance Pack templates |
| **Inconclusive Evidence** | ✅ Can present inconclusive evidence | ❌ All evidence maps to compliant/non-compliant |
| **Remediation** | ❌ No | ✅ Auto-remediation via SSM |
| **Future** | Maintenance mode (April 2026) | Actively developed |

#### AWS Audit Manager vs AWS Security Hub
| Feature | Audit Manager | Security Hub |
|---------|--------------|-------------|
| **Purpose** | Compliance audit evidence | Security posture management |
| **Output** | Assessment reports | Security score & findings |
| **Scope** | Broad compliance (HIPAA, SOC 2, etc.) | Security best practices |
| **AI/ML Focus** | GenAI Best Practices Framework | No AI-specific framework |

### Decision Trees
- ✅ **Use Audit Manager when**: You need to collect and organize evidence for compliance audits, especially for AI/ML workloads
- ✅ **Use Audit Manager when**: You need prebuilt frameworks for standards like HIPAA, PCI DSS, SOC 2
- ✅ **Use Audit Manager when**: You want to audit GenAI implementations on Bedrock/SageMaker
- ❌ **Don't use when**: You need real-time security monitoring → Use **GuardDuty**
- ❌ **Don't use when**: You need automated remediation → Use **AWS Config** with auto-remediation
- ❌ **Don't use when**: You need to detect model bias → Use **SageMaker Clarify**
- ❌ **Don't use when**: You need content filtering → Use **Bedrock Guardrails**

---

## 6. Best Practices

### Security
- Use **AWS KMS** customer-managed keys to encrypt assessment data
- Apply **least privilege IAM policies** for audit owners and delegates
- Use **delegated administrator** in AWS Organizations (avoid using management account directly)
- Enable **CloudTrail** logging for Audit Manager API calls
- Store assessment reports in **encrypted S3 buckets** with versioning enabled

### Cost Optimization
- Only create assessments for frameworks relevant to your compliance needs
- Deactivate assessments that are no longer needed to stop evidence collection
- Use the **Sample Framework** for learning before creating production assessments
- Review and clean up unused custom frameworks and controls

### Performance
- Enable **AWS Config**, **Security Hub**, and **CloudTrail** before creating assessments for maximum evidence coverage
- Use **Evidence Finder** to quickly search and filter collected evidence
- Leverage **framework sharing** to standardize assessments across accounts

### Operations
- Assign **delegates** for control sets requiring domain expertise
- Regularly review the **Audit Manager dashboard** for non-compliant controls grouped by domain
- Schedule periodic assessment report generation aligned with audit cycles
- Monitor evidence collection status via **CloudWatch** and **EventBridge**

---

## 7. Hands-On Labs

### Quick Setup
1. Enable AWS Audit Manager in the AWS Console
2. Configure KMS encryption key and S3 report destination
3. Enable AWS Config and Security Hub (recommended integrations)
4. Create an assessment using the **AWS Generative AI Best Practices Framework v2**

### Practical Exercises

#### Exercise 1: Create a GenAI Compliance Assessment
1. Navigate to **AWS Audit Manager** → **Assessments** → **Create assessment**
2. Select **AWS Generative AI Best Practices Framework v2**
3. Choose the AWS accounts running Bedrock/SageMaker workloads
4. Review the 8 principles and their associated controls
5. Monitor evidence collection over 24-48 hours

#### Exercise 2: Generate an Assessment Report
1. Open an active assessment with collected evidence
2. Review evidence folders organized by controls
3. Select evidence to include in the report
4. Generate the assessment report → verify it appears in S3

#### Exercise 3: Create a Custom Framework
1. Go to **Framework library** → **Create custom framework**
2. Add control sets for your organization's AI governance requirements
3. Use common controls (e.g., "Access controls") as evidence sources
4. Create an assessment from your custom framework

### CLI Commands
```bash
# Enable Audit Manager
aws auditmanager register-account

# List available frameworks
aws auditmanager list-assessment-frameworks --framework-type Standard

# Create an assessment
aws auditmanager create-assessment \
  --name "GenAI-Compliance-Assessment" \
  --framework-id <framework-id> \
  --assessment-reports-destination '{"destinationType":"S3","destination":"s3://my-audit-bucket"}' \
  --scope '{"awsAccounts":[{"id":"<account-id>"}]}'

# Get assessment details
aws auditmanager get-assessment --assessment-id <assessment-id>

# List evidence for a control
aws auditmanager get-evidence-by-evidence-folder \
  --assessment-id <assessment-id> \
  --control-set-id <control-set-id> \
  --evidence-folder-id <folder-id>

# Deregister (disable) Audit Manager
aws auditmanager deregister-account
```

---

## 8. Sample Questions

### Question 1
**A healthcare company is building a generative AI chatbot using Amazon Bedrock to assist patients. The compliance team needs to demonstrate that the AI implementation follows responsible AI practices during their annual audit. Which approach should they use?**

A) Use Amazon SageMaker Clarify to generate bias reports for the auditor  
B) Use AWS Audit Manager with the AWS Generative AI Best Practices Framework v2 to automatically collect compliance evidence  
C) Use Amazon Bedrock Guardrails to create an audit trail of filtered content  
D) Use AWS CloudTrail to manually compile API logs for the auditor  

**Correct Answer: B**

**Explanation**: AWS Audit Manager with the **AWS Generative AI Best Practices Framework v2** is purpose-built for this scenario. It automatically collects evidence from Bedrock (and SageMaker) and evaluates the implementation against 8 principles: Responsible, Safe, Fair, Sustainable, Resilience, Privacy, Accuracy, and Secure. It generates audit-ready assessment reports.
- **A is wrong**: SageMaker Clarify detects bias in ML models but doesn't provide comprehensive audit evidence collection for compliance frameworks
- **C is wrong**: Bedrock Guardrails filter content but don't generate compliance audit reports
- **D is wrong**: Manually compiling CloudTrail logs is inefficient and doesn't map to compliance frameworks

---

### Question 2
**A financial services company needs to audit their AWS environment for PCI DSS compliance AND ensure their SageMaker ML models follow AI governance best practices. What is the MOST efficient approach?**

A) Use AWS Config Conformance Packs for both PCI DSS and AI governance  
B) Create two separate assessments in AWS Audit Manager — one using the PCI DSS framework and another using the AWS Generative AI Best Practices Framework v2  
C) Use AWS Security Hub to monitor both PCI DSS and AI governance controls  
D) Hire an external auditor to manually review all AWS resources  

**Correct Answer: B**

**Explanation**: AWS Audit Manager supports multiple assessments simultaneously. Creating two assessments — one for PCI DSS and one for GenAI Best Practices — allows automated evidence collection for both compliance needs from a single service.
- **A is wrong**: AWS Config Conformance Packs don't have a direct equivalent for the GenAI Best Practices Framework (mapped to "Operational Best Practices for AI and ML" which is less comprehensive)
- **C is wrong**: Security Hub focuses on security posture scoring, not comprehensive audit evidence collection with assessment reports
- **D is wrong**: While external auditors are needed, manually reviewing resources is not efficient when Audit Manager can automate evidence collection

---

### Question 3
**An organization is using AWS Audit Manager to collect evidence for their generative AI workloads. They notice that some controls require manual evidence that cannot be automatically collected. What should they do?**

A) Ignore those controls since they cannot be automated  
B) Switch to AWS Config Conformance Packs which automate all controls  
C) Upload manual evidence (documents, screenshots, records) to the relevant controls in the assessment  
D) Create custom AWS Config rules to automate all evidence collection  

**Correct Answer: C**

**Explanation**: AWS Audit Manager supports both automated and manual evidence. Manual common controls require users to upload their own evidence because they typically involve physical records or events outside the AWS environment (e.g., employee training records, policy documents). Users can upload documents, screenshots, and other records directly to controls.
- **A is wrong**: Ignoring controls would leave gaps in the compliance assessment
- **B is wrong**: Config Conformance Packs collect only Configuration Items — they are actually less comprehensive than Audit Manager for evidence types
- **D is wrong**: Not all evidence can be captured via AWS Config rules (e.g., organizational policies, training records)

---

## Citations
- [AWS Audit Manager Concepts & Terminology](https://docs.aws.amazon.com/audit-manager/latest/userguide/concepts.html)
- [Supported Frameworks in AWS Audit Manager](https://docs.aws.amazon.com/audit-manager/latest/userguide/framework-overviews.html)
- [AWS Generative AI Best Practices Framework v2](https://docs.aws.amazon.com/audit-manager/latest/userguide/aws-generative-ai-best-practices.html)
- [AWS Audit Manager Availability Change](https://docs.aws.amazon.com/audit-manager/latest/userguide/audit-manager-availability-change.html)
- [Enabling Recommended Features for Audit Manager](https://docs.aws.amazon.com/audit-manager/latest/userguide/setup-recommendations.html)

---

**Exam Tip**: For AIF-C01, focus on the **AWS Generative AI Best Practices Framework v2** and its 8 principles (Responsible, Safe, Fair, Sustainable, Resilience, Privacy, Accuracy, Secure). Know that Audit Manager automates evidence collection but does NOT remediate issues or replace external auditors. Understand the difference between Audit Manager (audit evidence) vs. Config (resource compliance) vs. Security Hub (security posture).
