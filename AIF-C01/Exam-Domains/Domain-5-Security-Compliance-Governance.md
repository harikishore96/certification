# Domain 5: Security, Compliance, and Governance for AI Solutions (14%)

## Domain Overview
- **Weight**: 14% of exam
- **Focus**: Security controls, compliance requirements, governance frameworks for AI/ML
- **Key Topics**: IAM, encryption, VPC, monitoring, compliance, access control, data protection

## Task Statements

### Task 5.1: Implement security controls for AI/ML workloads
**Weight**: Very High  
**Skills**:
- Configure IAM for AI/ML services
- Implement encryption (at rest and in transit)
- Secure network architecture (VPC)
- Protect model artifacts and data
- Implement least privilege access

**Security Layers**:

**1. Identity & Access Management (IAM)**

**IAM Roles**:
- **SageMaker Execution Role**: Access to S3, ECR, CloudWatch
- **Bedrock Invocation Role**: Call foundation models
- **Lambda Execution Role**: Invoke AI services

**IAM Policies**:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "sagemaker:InvokeEndpoint",
      "bedrock:InvokeModel"
    ],
    "Resource": "*",
    "Condition": {
      "IpAddress": {
        "aws:SourceIp": "10.0.0.0/16"
      }
    }
  }]
}
```

**Best Practices**:
- Least privilege principle
- Use roles, not access keys
- Implement MFA for sensitive operations
- Regular access reviews
- Separate dev/prod permissions

**2. Data Encryption**

**At Rest**:
- **S3**: SSE-S3, SSE-KMS, SSE-C
- **EBS**: KMS encryption
- **RDS**: TDE (Transparent Data Encryption)
- **SageMaker**: Encrypted storage volumes

**In Transit**:
- **TLS/SSL**: All API calls
- **VPC Endpoints**: Private connectivity
- **Certificate Management**: ACM

**Key Management**:
- **AWS KMS**: Customer managed keys
- **Key Rotation**: Automatic rotation
- **Key Policies**: Control key usage
- **CloudHSM**: Hardware security modules

**3. Network Security**

**VPC Configuration**:
- **Private Subnets**: Deploy endpoints in private subnets
- **Security Groups**: Control inbound/outbound traffic
- **NACLs**: Network-level access control
- **VPC Endpoints**: Private connectivity to AWS services

**SageMaker Network Isolation**:
```python
# Deploy endpoint in VPC
model.deploy(
    initial_instance_count=1,
    instance_type='ml.m5.xlarge',
    endpoint_name='secure-endpoint',
    vpc_config={
        'SecurityGroupIds': ['sg-12345'],
        'Subnets': ['subnet-12345', 'subnet-67890']
    }
)
```

**Network Isolation**:
- No internet access for training/inference
- All traffic through VPC
- PrivateLink for AWS services

**4. Model & Artifact Security**

**Model Protection**:
- Encrypt model artifacts (S3 with KMS)
- Access control (IAM policies)
- Version control (Model Registry)
- Secure model deployment

**Container Security**:
- Use approved base images
- Scan for vulnerabilities (ECR scanning)
- Sign images
- Regular updates

**5. API Security**

**API Gateway**:
- Authentication (IAM, Cognito)
- Authorization (resource policies)
- Rate limiting (throttling)
- WAF (Web Application Firewall)

**Endpoint Security**:
- Authentication required
- HTTPS only
- IP whitelisting
- Request validation

**AWS Services**:
- **IAM**: Access control
- **KMS**: Encryption
- **VPC**: Network isolation
- **Security Groups**: Firewall rules
- **Secrets Manager**: Credential storage
- **Certificate Manager**: TLS certificates
- **WAF**: API protection

**Study Resources**:
- [Amazon SageMaker](../ML-Services/Amazon-SageMaker.md) - Security section
- [Amazon Bedrock](../Generative-AI-Services/Amazon-Bedrock.md) - Security section

---

### Task 5.2: Implement monitoring and logging for AI/ML systems
**Weight**: High  
**Skills**:
- Configure CloudWatch monitoring
- Implement logging strategies
- Set up alerts and notifications
- Monitor model performance
- Track resource utilization

**Monitoring Layers**:

**1. Infrastructure Monitoring**

**CloudWatch Metrics**:
- **SageMaker Endpoints**:
  - ModelLatency (inference time)
  - Invocations (request count)
  - InvocationErrors (failed requests)
  - CPUUtilization, MemoryUtilization
  - DiskUtilization

- **Bedrock**:
  - Invocations
  - InvocationLatency
  - InvocationClientErrors
  - InvocationServerErrors

**Alarms**:
```python
import boto3

cloudwatch = boto3.client('cloudwatch')

# Create alarm for high latency
cloudwatch.put_metric_alarm(
    AlarmName='HighEndpointLatency',
    MetricName='ModelLatency',
    Namespace='AWS/SageMaker',
    Statistic='Average',
    Period=300,
    EvaluationPeriods=2,
    Threshold=1000,  # 1 second
    ComparisonOperator='GreaterThanThreshold',
    AlarmActions=['arn:aws:sns:region:account:topic']
)
```

**2. Model Performance Monitoring**

**SageMaker Model Monitor**:
- **Data Quality**: Detect data drift
- **Model Quality**: Track accuracy degradation
- **Bias Drift**: Monitor fairness over time
- **Feature Attribution Drift**: Explainability changes

**Monitoring Schedule**:
```python
from sagemaker.model_monitor import DefaultModelMonitor

monitor = DefaultModelMonitor(
    role=role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    max_runtime_in_seconds=3600
)

monitor.create_monitoring_schedule(
    monitor_schedule_name='daily-monitoring',
    endpoint_input=endpoint_name,
    schedule_cron_expression='cron(0 0 * * ? *)'  # Daily
)
```

**3. Logging**

**CloudWatch Logs**:
- Endpoint invocation logs
- Training job logs
- Lambda function logs
- Error logs

**CloudTrail**:
- API call history
- Who did what, when
- Compliance auditing
- Security analysis

**Log Analysis**:
- CloudWatch Insights queries
- Log aggregation
- Anomaly detection
- Security monitoring

**4. Application Monitoring**

**Custom Metrics**:
- Business KPIs
- User satisfaction
- Task completion rates
- Error rates by type

**Distributed Tracing**:
- X-Ray for request tracing
- End-to-end latency
- Bottleneck identification

**AWS Services**:
- **CloudWatch**: Metrics, logs, alarms
- **CloudTrail**: Audit logs
- **X-Ray**: Distributed tracing
- **SageMaker Model Monitor**: ML-specific monitoring
- **EventBridge**: Event-driven automation

**Study Resources**:
- [Amazon SageMaker](../ML-Services/Amazon-SageMaker.md) - Monitoring section

---

### Task 5.3: Ensure compliance with regulations and standards
**Weight**: Medium-High  
**Skills**:
- Understand regulatory requirements
- Implement compliance controls
- Maintain audit trails
- Document compliance measures
- Handle data residency requirements

**Key Regulations**:

**1. GDPR (General Data Protection Regulation)**
- **Scope**: EU personal data
- **Requirements**:
  - Right to explanation (explainability)
  - Right to be forgotten (data deletion)
  - Data minimization
  - Consent management
  - Data portability
- **AWS Compliance**: EU regions, data residency

**2. HIPAA (Health Insurance Portability and Accountability Act)**
- **Scope**: US healthcare data (PHI)
- **Requirements**:
  - Encryption (at rest and in transit)
  - Access controls
  - Audit logs
  - Business Associate Agreement (BAA)
- **AWS Services**: HIPAA-eligible services (SageMaker, Comprehend Medical)

**3. CCPA (California Consumer Privacy Act)**
- **Scope**: California residents' data
- **Requirements**:
  - Disclosure of data collection
  - Right to deletion
  - Opt-out of data sale
- **AWS Compliance**: Data residency, deletion capabilities

**4. SOC 2**
- **Scope**: Service organization controls
- **Requirements**:
  - Security controls
  - Availability
  - Confidentiality
  - Privacy
- **AWS Compliance**: SOC 2 reports available

**5. PCI DSS**
- **Scope**: Payment card data
- **Requirements**:
  - Encryption
  - Access control
  - Network security
  - Monitoring
- **AWS Compliance**: PCI DSS compliant services

**Compliance Implementation**:

**1. Data Residency**:
- Store data in specific regions
- Use regional services
- Restrict cross-region transfers

**2. Data Retention**:
- Define retention policies
- Automated deletion (S3 Lifecycle)
- Backup and archive
- Legal hold capabilities

**3. Audit Trails**:
- CloudTrail (all API calls)
- CloudWatch Logs (application logs)
- S3 access logs
- VPC Flow Logs

**4. Access Controls**:
- IAM policies (least privilege)
- MFA for sensitive operations
- Regular access reviews
- Separation of duties

**5. Documentation**:
- Model Cards (model documentation)
- Data lineage (where data comes from)
- Processing records
- Compliance reports

**AWS Compliance Tools**:
- **AWS Artifact**: Compliance reports
- **AWS Config**: Resource compliance
- **Security Hub**: Compliance standards
- **Audit Manager**: Audit automation
- **Macie**: Data classification

**Study Resources**:
- [Amazon SageMaker](../ML-Services/Amazon-SageMaker.md) - Compliance section

---

## Domain Summary

### Key Focus Areas
1. **Security Controls**: IAM, encryption, VPC, network isolation
2. **Monitoring**: CloudWatch, Model Monitor, CloudTrail
3. **Compliance**: GDPR, HIPAA, CCPA, SOC 2, PCI DSS
4. **Data Protection**: Encryption, access control, privacy
5. **Governance**: Policies, processes, documentation

### Common Question Types
- Configure IAM policies for AI/ML services
- Implement encryption for data and models
- Design secure network architecture
- Set up monitoring and alerting
- Ensure regulatory compliance
- Implement audit trails

### Study Strategy
1. Master IAM for AI/ML services
2. Understand encryption options (KMS)
3. Learn VPC configuration for endpoints
4. Know monitoring tools (CloudWatch, Model Monitor)
5. Understand compliance requirements
6. Practice security best practices

### AWS Services to Know
- **IAM**: Access control
- **KMS**: Encryption
- **VPC**: Network isolation
- **CloudWatch**: Monitoring and logging
- **CloudTrail**: Audit trails
- **SageMaker Model Monitor**: ML monitoring
- **Config**: Compliance tracking
- **Security Hub**: Security posture
- **Secrets Manager**: Credential management

### Exam Tips
- Security and compliance apply to ALL AI/ML implementations
- Always encrypt sensitive data (at rest and in transit)
- Use IAM roles, not access keys
- Deploy in VPC for sensitive workloads
- Enable CloudTrail for audit trails
- Use SageMaker Model Monitor for ML-specific monitoring
- Know compliance requirements (GDPR, HIPAA)
- Implement least privilege access
