# AWS Artifact

> **Source**: [AWS Artifact Documentation](https://docs.aws.amazon.com/artifact/latest/ug/what-is-aws-artifact.html)

---

## 1. Service/Topic Overview

- **Purpose**: AWS Artifact provides on-demand access to AWS security and compliance documents (audit reports, certifications) and enables management of legal agreements with AWS
- **Service Level**: Global
- **Pricing**: **Free** — no charge for downloading reports or managing agreements
- **Key Use Cases**:
  1. Downloading AWS compliance reports (SOC, ISO, PCI DSS) for auditors
  2. Accepting and managing legal agreements (e.g., HIPAA BAA)
  3. Providing audit artifacts to regulators as evidence of AWS compliance
  4. Accessing third-party ISV compliance documents from AWS Marketplace
  5. Managing organization-wide agreements via AWS Organizations
- **Exam Weight**: Medium
- **Exam Domain**: Domain 5 — Security, Compliance, and Governance for AI Solutions (14%)
- **Task Statements**: Task 5.3 — Understand compliance and governance requirements for AI solutions

---

## 2. Exam Keyword Mapping

- **Trigger Words**: "compliance reports", "audit reports", "SOC reports", "ISO certification", "PCI DSS", "download compliance documents", "BAA", "HIPAA agreement", "NDA with AWS", "third-party audit"
- **Scenario Indicators**:
  - "Company needs to provide AWS compliance documentation to auditors" → **AWS Artifact Reports**
  - "Organization must accept a HIPAA BAA with AWS" → **AWS Artifact Agreements**
  - "Need to download SOC 2 report for regulatory review" → **AWS Artifact**
  - "Verify AWS meets specific compliance standards" → **AWS Artifact**
- **Anti-patterns**:
  - ❌ Need to assess YOUR OWN compliance posture → **AWS Audit Manager**
  - ❌ Need to continuously monitor compliance of AWS resources → **AWS Config**
  - ❌ Need to detect security vulnerabilities in workloads → **Amazon Inspector**
  - ❌ Need to aggregate security findings → **AWS Security Hub**
  - ❌ Need to track API calls for auditing → **AWS CloudTrail**

---

## 3. Core Concepts

### Two Main Components

| Component | Purpose | Examples |
|-----------|---------|----------|
| **Artifact Reports** | Download AWS compliance/security documents | SOC 1/2/3, ISO 27001, PCI DSS, FedRAMP |
| **Artifact Agreements** | Review, accept, and manage legal agreements with AWS | HIPAA BAA, NDA, Data Processing Addendum |

### Architectural Patterns
- **Self-service compliance portal** — no need to contact AWS support for compliance documents
- **Centralized agreement management** — manage agreements at account or organization level
- **Audit evidence pipeline** — download → review → submit to auditors/regulators

### Data Flow
```
User → AWS Artifact Console → Select Report/Agreement → Download/Accept → Share with Auditors
```

### Integration Points
- **AWS Organizations** — accept agreements on behalf of all member accounts
- **AWS IAM** — control access to reports and agreements with fine-grained policies
- **IAM Condition Keys** — restrict access to specific report categories/series
- **AWS Marketplace Vendor Insights** — access ISV compliance documents

### Shared Responsibility Model
- ⚠️ AWS Artifact provides evidence of **AWS's** compliance (their side of shared responsibility)
- Customers are still responsible for developing/obtaining documents that demonstrate **their own** compliance

---

## 4. Key Features & Components

### Artifact Reports
- **On-demand download** of AWS security and compliance documents
- Report types include:
  - **SOC Reports** (SOC 1, SOC 2, SOC 3) — System and Organization Controls
  - **ISO Certifications** (ISO 27001, 27017, 27018, 9001) — International standards
  - **PCI DSS** — Payment Card Industry Data Security Standard
  - **FedRAMP** — Federal Risk and Authorization Management Program
  - **HIPAA** — Health Insurance Portability and Accountability Act
  - **CSA STAR** — Cloud Security Alliance
- Third-party ISV reports via AWS Marketplace

### Artifact Agreements
- **Account-level agreements** — apply to a single AWS account
- **Organization-level agreements** — apply to ALL accounts in AWS Organizations
  - All existing and subsequent member accounts automatically covered
- **Agreement lifecycle**: Accept → Review → Terminate → Re-activate
- **Common agreements**:
  - **HIPAA BAA** (Business Associate Addendum) — required for processing PHI
  - **NDA** (Non-Disclosure Agreement)
  - **Data Processing Addendum**
- **Offline agreements** — view status of agreements accepted outside the console

### Notifications
- Subscribe to notifications about new or updated AWS Artifact documents

---

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "A company needs to provide AWS SOC 2 reports to their auditors" → AWS Artifact
- **Scenario**: "An organization processing healthcare data needs a HIPAA BAA" → AWS Artifact Agreements
- **Comparison**: AWS Artifact vs. AWS Audit Manager vs. AWS Config

### Gotchas
- ⚠️ AWS Artifact shows **AWS's compliance**, NOT your compliance — use **AWS Audit Manager** to assess your own
- ⚠️ Organization agreements cover **all** member accounts automatically (existing + future)
- ⚠️ Terminated agreements can be **re-activated** — they are not permanently deleted
- ⚠️ AWS Artifact is **free** — no cost for any reports or agreements

### Critical Comparison: AWS Artifact vs. AWS Audit Manager

| Aspect | AWS Artifact | AWS Audit Manager |
|--------|-------------|-------------------|
| **Focus** | AWS's compliance documents | YOUR compliance assessment |
| **Output** | Downloadable reports/certifications | Automated audit evidence collection |
| **Question** | "Download SOC report" | "Assess our compliance posture" |
| **Scope** | AWS infrastructure compliance | Customer workload compliance |
| **Cost** | Free | Pay per resource assessment |

### Decision Tree
- ✅ **Use AWS Artifact when**: You need to download AWS compliance reports, accept agreements (BAA/NDA), or provide AWS audit evidence to regulators
- ❌ **Do NOT use when**: You need to assess your own compliance, monitor resource configurations, or detect security vulnerabilities

---

## 6. Best Practices

### Security
- Use **IAM policies** to restrict who can access compliance reports
- Use **IAM condition keys** to limit access to specific report categories/series
- Only share reports with authorized personnel (auditors, compliance teams)
- Review agreements carefully before accepting

### Operations
- **Subscribe to notifications** to stay informed about new/updated documents
- Use **organization-level agreements** to simplify compliance across multiple accounts
- Regularly review accepted agreements to ensure they still meet requirements
- Maintain a record of which reports have been shared with auditors

---

## 7. Hands-On Labs

### Quick Setup
1. Sign in to the [AWS Artifact Console](https://console.aws.amazon.com/artifact/)
2. Navigate to **Reports** to browse available compliance documents
3. Navigate to **Agreements** to view and manage legal agreements

### Practical Exercises
1. **Download a SOC Report**: Navigate to Reports → Search for SOC → Download
2. **Accept a BAA**: Navigate to Agreements → Select BAA → Review terms → Accept
3. **Configure Notifications**: Subscribe to receive updates on new documents

### CLI Commands
```bash
# List available reports
aws artifact list-reports

# Get a specific report
aws artifact get-report-metadata --report-id <report-id>

# List customer agreements
aws artifact list-customer-agreements
```

---

## 8. Sample Questions

### Question 1
**A healthcare company using AWS needs to ensure they have a legally binding agreement with AWS to process protected health information (PHI). Which AWS service should they use?**

A) AWS Config  
B) AWS Audit Manager  
C) AWS Artifact  
D) Amazon Macie  

**Correct Answer: C**

**Explanation**: AWS Artifact allows organizations to review and accept agreements such as the HIPAA Business Associate Addendum (BAA), which is required for processing PHI on AWS. Organization-level BAAs can cover all member accounts automatically.
- A is wrong: AWS Config monitors resource configurations, not legal agreements
- B is wrong: Audit Manager assesses your compliance posture, not manage AWS agreements
- D is wrong: Macie discovers sensitive data but doesn't manage compliance agreements

---

### Question 2
**An auditor requests evidence that AWS infrastructure meets SOC 2 compliance standards. How should the company obtain this documentation?**

A) Contact AWS Support to request the report  
B) Download the SOC 2 report from AWS Artifact  
C) Run an assessment in AWS Audit Manager  
D) Check AWS Config compliance rules  

**Correct Answer: B**

**Explanation**: AWS Artifact provides on-demand, self-service access to AWS compliance reports including SOC 1/2/3, ISO certifications, and PCI DSS reports. No need to contact AWS Support.
- A is wrong: AWS Artifact provides self-service access; no need to contact support
- C is wrong: Audit Manager assesses YOUR compliance, not AWS's compliance
- D is wrong: Config monitors your resource configurations against rules

---

### Question 3
**A company wants to ensure that all AWS accounts in their organization are covered by a HIPAA BAA. What is the MOST efficient approach?**

A) Accept a BAA individually for each AWS account in AWS Artifact  
B) Accept an organization-level BAA in AWS Artifact using AWS Organizations  
C) Create an AWS Config rule to enforce BAA compliance  
D) Use AWS Audit Manager to generate BAA reports  

**Correct Answer: B**

**Explanation**: AWS Artifact supports organization-level agreements through AWS Organizations. When you accept a BAA at the organization level, all existing and subsequent member accounts are automatically covered — no need to accept individually per account.
- A is wrong: While possible, this is inefficient and doesn't auto-cover new accounts
- C is wrong: AWS Config cannot manage legal agreements
- D is wrong: Audit Manager doesn't manage agreements

---

**Exam Tip**: Remember the key distinction — AWS Artifact = **AWS's compliance evidence** (reports + agreements). AWS Audit Manager = **YOUR compliance assessment**. If the question asks about downloading compliance reports or accepting agreements like BAA, the answer is always AWS Artifact.
