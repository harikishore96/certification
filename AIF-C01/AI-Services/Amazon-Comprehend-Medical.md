# Amazon Comprehend Medical

## 1. Service/Topic Overview
- **Purpose**: HIPAA-eligible NLP service that uses ML to extract medical information from unstructured clinical text — no ML experience required
- **Service Level**: Regional
- **Key Use Cases**:
  - Extract medical entities from clinical notes (conditions, medications, procedures)
  - Detect and identify Protected Health Information (PHI) in medical documents
  - Link medical entities to standardized ontologies (ICD-10-CM, RxNorm, SNOMED CT)
  - Medical billing and revenue cycle management (computer-assisted coding)
  - Clinical trial matching and pharmacovigilance
- **Exam Weight**: Medium
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — NLP use cases in healthcare
  - Domain 3: Applications of Foundation Models (28%) — pre-built AI service selection
  - Domain 4: Guidelines for Responsible AI (14%) — PHI handling, HIPAA compliance
  - Domain 5: Security, Compliance, and Governance (14%) — HIPAA eligibility, data protection
- **Task Statements**:
  - Task 1.2: Identify practical use cases for AI
  - Task 3.2: Select appropriate AI services for specific use cases
  - Task 4.1: Explain responsible AI practices
  - Task 5.1: Describe methods to ensure data security and compliance

## 2. Exam Keyword Mapping
- **Trigger Words**: "clinical text", "medical records", "physician notes", "discharge summaries", "medical conditions", "medications", "PHI detection", "ICD-10", "RxNorm", "SNOMED CT", "medical coding", "HIPAA-eligible NLP", "medical entities", "ontology linking"
- **Scenario Indicators**:
  - "Extract medical conditions from doctor's notes" → DetectEntitiesV2
  - "Identify PHI in clinical documents" → DetectPHI
  - "Link diagnoses to billing codes" → InferICD10CM
  - "Map medications to standardized drug codes" → InferRxNorm
  - "Structure clinical terms for analytics" → InferSNOMEDCT
  - "Analyze medical text at scale" → Batch APIs (StartEntitiesDetectionV2Job)
  - "HIPAA-compliant text analysis" → Comprehend Medical (not standard Comprehend)
- **Anti-patterns**:
  - ❌ General text sentiment analysis → Use Amazon Comprehend (standard)
  - ❌ Text generation or summarization → Use Amazon Bedrock
  - ❌ Custom NLP tasks beyond entity recognition (e.g., summarization, Q&A) → Use SageMaker or Bedrock
  - ❌ Non-English medical text → Only supports US English (US-EN)
  - ❌ Custom entity definitions that differ from the service's model → Use SageMaker custom model
  - ❌ Image-based medical analysis (X-rays, MRIs) → Use Amazon Rekognition or SageMaker
  - ❌ Speech-to-text for medical dictation → Use Amazon Transcribe Medical first, then Comprehend Medical

## 3. Core Concepts

### Architectural Patterns
```
Clinical Text (physician notes, discharge summaries, test results, case notes)
    ↓
Amazon Comprehend Medical
├── Text Analysis APIs
│   ├── DetectEntitiesV2 → Medical entities (conditions, meds, procedures, anatomy, PHI)
│   └── DetectPHI → Protected Health Information only
│
└── Ontology Linking APIs
    ├── InferICD10CM → Medical conditions → ICD-10-CM codes
    ├── InferRxNorm → Medications → RxNorm concept IDs (RxCUI)
    └── InferSNOMEDCT → Medical concepts → SNOMED CT codes
    ↓
Structured Output (JSON with entities, confidence scores, relationships, ontology codes)
```

### Key Terminology
| Term | Definition |
|------|-----------|
| **Entity** | Textual reference to medical information (e.g., "ibuprofen", "hypertension") |
| **Category** | Generalized grouping of an entity (e.g., MEDICATION, MEDICAL_CONDITION) |
| **Type** | Specific entity type within a category (e.g., GENERIC_NAME, BRAND_NAME) |
| **Attribute** | Information related to an entity (e.g., dosage "200 mg" for a medication) |
| **Trait** | Contextual understanding about an entity (e.g., NEGATION — patient is NOT taking a medication) |
| **Relationship Type** | Connection between an entity and its attributes |
| **Confidence Score** | 0–1 value indicating the model's confidence in the detected entity |
| **Ontology Linking** | Mapping detected entities to standardized medical code systems |
| **PHI** | Protected Health Information — patient-identifiable data under HIPAA |

### Processing Modes
| Mode | APIs | Input | Output | Use Case |
|------|------|-------|--------|----------|
| **Synchronous** | DetectEntitiesV2, DetectPHI, InferICD10CM, InferRxNorm, InferSNOMEDCT | Single document (text string) | Direct API response | Real-time clinical applications |
| **Asynchronous (Batch)** | StartEntitiesDetectionV2Job, StartPHIDetectionJob, StartICD10CMInferenceJob, StartRxNormInferenceJob, StartSNOMEDCTInferenceJob | Documents in S3 (up to 10 GB) | Results written to S3 | Large-scale document processing |

### Data Flow
```
Synchronous:
Clinical Text → API Call → JSON Response (entities + scores + relationships)

Asynchronous (Batch):
S3 Input Bucket → Start Job → Processing → S3 Output Bucket
                                              ├── Output files (.out per input file)
                                              └── Manifest files (summary, success, unprocessed)
```

### Integration Points
| AWS Service | Integration |
|-------------|-------------|
| **Amazon S3** | Input/output storage for batch processing |
| **Amazon Transcribe / Transcribe Medical** | Convert speech to text → feed into Comprehend Medical |
| **AWS Lambda** | Real-time triggers for clinical text analysis |
| **Amazon Kinesis / Firehose** | Stream processing for real-time clinical data |
| **Amazon SageMaker** | Custom medical NLP models when Comprehend Medical doesn't fit |
| **IAM** | Access control for API operations |
| **VPC Endpoints (PrivateLink)** | Private connectivity without internet traversal |
| **CloudTrail** | Audit logging for all API calls |

## 4. Key Features & Components

### Entity Categories Detected (DetectEntitiesV2)

#### MEDICATION
- **Types**: BRAND_NAME, GENERIC_NAME
- **Attributes**: DOSAGE, DURATION, FORM, FREQUENCY, RATE, ROUTE_OR_MODE, STRENGTH
- **Traits**: NEGATION, PAST_HISTORY
- **Example**: "LIPITOR 20 mg once daily" → BRAND_NAME: LIPITOR, DOSAGE: 20 mg, FREQUENCY: once daily

#### MEDICAL_CONDITION
- **Types**: DX_NAME (all medical conditions — present illness, reason for visit, medical history)
- **Attributes**: ACUITY (chronic, acute, sudden), DIRECTION (left, right), SYSTEM_ORGAN_SITE, QUALITY (stage, grade)
- **Traits**: DIAGNOSIS, SIGN, SYMPTOM, NEGATION, HYPOTHETICAL, LOW_CONFIDENCE, PERTAINS_TO_FAMILY
- **Example**: "chronic pain in left leg" → DX_NAME: pain, ACUITY: chronic, DIRECTION: left, SYSTEM_ORGAN_SITE: leg

#### TEST_TREATMENT_PROCEDURE
- **Types**: TEST_NAME, TREATMENT_NAME, PROCEDURE_NAME
- **Attributes**: TEST_VALUE, TEST_UNIT
- **Traits**: FUTURE, HYPOTHETICAL, NEGATION, PAST_HISTORY
- **Example**: "blood pressure 120/80 mmHg" → TEST_NAME: blood pressure, TEST_VALUE: 120/80, TEST_UNIT: mmHg

#### ANATOMY
- **Types**: SYSTEM_ORGAN_SITE
- **Attributes**: DIRECTION
- **Example**: "right upper lobe of the lung" → SYSTEM_ORGAN_SITE: lung, DIRECTION: right upper

#### PROTECTED_HEALTH_INFORMATION
- **Types**: ADDRESS, AGE, EMAIL, ID (SSN, MRN, facility ID, clinical trial number), NAME, PHONE_OR_FAX, PROFESSION
- **Example**: "Mr. Smith, age 63, MRN 12345" → NAME: Mr. Smith, AGE: 63, ID: 12345

#### BEHAVIORAL_ENVIRONMENTAL_SOCIAL
- **Types**: ALCOHOL_CONSUMPTION, ALLERGIES, GENDER, RACE_ETHNICITY, REC_DRUG_USE, TOBACCO_USE
- **Attributes**: AMOUNT, DURATION, FREQUENCY (for alcohol, tobacco, recreational drugs)
- **Traits**: NEGATION, PAST_HISTORY

#### TIME_EXPRESSION
- **Types**: TIME_TO_MEDICATION_NAME, TIME_TO_DX_NAME, TIME_TO_TEST_NAME, TIME_TO_PROCEDURE_NAME, TIME_TO_TREATMENT_NAME
- **Note**: Only returned when associated with a detected entity (not standalone dates)

### Ontology Linking APIs

| API | Knowledge Base | Links To | Version | Use Case |
|-----|---------------|----------|---------|----------|
| **InferICD10CM** | ICD-10-CM | Medical conditions → diagnosis codes | 2021 | Medical billing, coding, claims processing |
| **InferRxNorm** | RxNorm (NLM) | Medications → RxCUI concept IDs | Current | Medication validation, drug interaction checks |
| **InferSNOMEDCT** | SNOMED CT | Medical concepts → SNOMED codes | 2021-03 | Clinical analytics, EHR structuring, reporting |

- Each ontology linking API returns the **top matching codes** ranked by confidence score
- InferSNOMEDCT returns the **top 5 matching concepts** per entity
- All include contextual traits (e.g., negation) alongside the linked codes
- Batch versions available: StartICD10CMInferenceJob, StartRxNormInferenceJob, StartSNOMEDCTInferenceJob

### PHI Detection (DetectPHI)
- Dedicated API specifically for identifying Protected Health Information
- Uses **Safe Harbor guidelines** to search for PHI
- Detects: Names, addresses, ages, dates, email, phone/fax, IDs (SSN, MRN), professions
- Use case: De-identification of clinical text before research or analytics

### Console Analysis
- Analyze clinical text up to **20,000 characters** directly in the console
- Color-coded entity visualization:
  - 🟠 Orange = PHI
  - 🔴 Red = Medication
  - 🟢 Green = Medical Condition
  - 🔵 Blue = Test/Treatment/Procedure
  - 🟣 Purple = Anatomy
  - 🩷 Pink = Time Expression
- View JSON request/response structure for application integration

## 5. Exam Focus Areas

### Common Question Types
- **Scenario-based**: "A hospital needs to extract diagnosis codes from physician notes for billing" → InferICD10CM
- **Service selection**: "Which service analyzes medical text for HIPAA compliance?" → Comprehend Medical
- **Feature comparison**: Comprehend Medical vs. standard Comprehend vs. SageMaker
- **Compliance**: When to use Comprehend Medical for PHI handling

### Gotchas ⚠️
1. **US English Only**: Comprehend Medical only supports English (US-EN) — no other languages
2. **Not a Substitute for Medical Advice**: Results must be reviewed by trained medical professionals in patient care scenarios
3. **Confidence Thresholds**: You must set appropriate confidence thresholds for your use case — high thresholds for high-accuracy scenarios
4. **Separate Service**: Comprehend Medical has its own API endpoint, pricing, and documentation — it's NOT just a feature of standard Comprehend
5. **No Custom Training**: Uses a pre-trained model only — you cannot train custom models (unlike standard Comprehend)
6. **No Sentiment Analysis**: Comprehend Medical does entity extraction and ontology linking only — for sentiment, use standard Comprehend
7. **PHI Handling**: HIPAA-eligible, but connections containing PHI **must** be encrypted (HTTPS/TLS by default)
8. **No Persistent Storage**: Comprehend Medical does not persistently store customer content — no encryption at rest configuration needed within the service
9. **Batch Limit**: Up to 10 GB of documents per batch job
10. **Ontology Versions**: ICD-10-CM uses 2021 version; SNOMED CT uses 2021-03 version — not always the latest

### Comparison Points

#### Comprehend Medical vs. Standard Comprehend
| Feature | Comprehend Medical | Amazon Comprehend |
|---------|-------------------|-------------------|
| **Domain** | Medical/clinical text | General text |
| **Entity Types** | Medical (conditions, meds, anatomy, PHI) | General (person, location, org, date) |
| **Ontology Linking** | ICD-10-CM, RxNorm, SNOMED CT | None |
| **HIPAA Eligible** | ✅ Yes | ❌ No (standard) |
| **Languages** | US English only | 100+ languages |
| **Custom Models** | ❌ No | ✅ Yes (classification, entity recognition) |
| **Sentiment Analysis** | ❌ No | ✅ Yes |
| **PII/PHI Detection** | PHI (medical-specific) | PII (general) |
| **Pricing** | Separate pricing | Separate pricing |

#### Comprehend Medical vs. SageMaker (Custom Medical NLP)
| Feature | Comprehend Medical | SageMaker Custom |
|---------|-------------------|-----------------|
| **Setup** | No training, pre-built APIs | Requires training data and model building |
| **ML Expertise** | None required | Required (or use AutoML) |
| **Customization** | None — fixed entity types | Fully customizable entities and tasks |
| **Tasks** | Entity extraction + ontology linking only | Any NLP task (summarization, Q&A, classification) |
| **Time to Deploy** | Immediate | Days to weeks |
| **Cost** | Pay per API call | Training + inference infrastructure |
| **Best For** | Standard medical entity extraction | Custom entity definitions, non-standard NLP tasks |

#### When to Use Which Ontology API
| Need | API | Knowledge Base |
|------|-----|---------------|
| Map diagnoses to billing codes | InferICD10CM | ICD-10-CM |
| Validate/standardize medication names | InferRxNorm | RxNorm |
| Structure clinical data for analytics/EHR | InferSNOMEDCT | SNOMED CT |

### Decision Trees
```
Need to analyze medical/clinical text?
├── Extract medical entities (conditions, meds, procedures)?
│   ├── Standard entity types sufficient? → ✅ Comprehend Medical (DetectEntitiesV2)
│   └── Need custom entity definitions? → SageMaker custom model
├── Detect PHI for de-identification?
│   └── ✅ Comprehend Medical (DetectPHI)
├── Link to medical billing codes?
│   ├── Diagnosis codes → ✅ InferICD10CM
│   ├── Medication codes → ✅ InferRxNorm
│   └── Clinical terminology → ✅ InferSNOMEDCT
├── Sentiment analysis on patient feedback?
│   └── Use standard Amazon Comprehend (not Medical)
├── Summarize or generate medical text?
│   └── Use Amazon Bedrock
└── Non-English medical text?
    └── Comprehend Medical NOT supported — use SageMaker custom model
```

## 6. Best Practices

### Security
- **HIPAA Compliance**: Comprehend Medical is HIPAA-eligible — sign a BAA (Business Associate Agreement) with AWS
- **Encryption in Transit**: All connections use HTTPS/TLS by default — required for PHI
- **No Persistent Storage**: Service does not store customer content — no at-rest encryption configuration needed within the service
- **IAM**: Use least-privilege IAM policies for API access
- **VPC Endpoints**: Use PrivateLink for private connectivity (no internet traversal)
- **CloudTrail**: Enable audit logging for all Comprehend Medical API calls
- **PHI De-identification**: Use DetectPHI to identify and remove PHI before sharing data for research or analytics

### Cost Optimization
- **Pricing**: Pay per character of text processed — no minimum fees, no upfront commitments
- **Free Tier**: 25,000 characters/month for Medical NERe and PHI detection for first 12 months
- **Batch Processing**: Use asynchronous batch APIs for large document sets (more cost-effective than individual API calls)
- **Pre-filter Text**: Remove non-clinical content (headers, footers, boilerplate) before processing to reduce character count
- **Confidence Thresholds**: Filter low-confidence results programmatically to reduce downstream processing costs
- **Right API Selection**: Use DetectPHI (cheaper) if you only need PHI — don't use DetectEntitiesV2 for PHI-only use cases

### Performance
- **Synchronous**: Use for real-time, single-document analysis (interactive applications)
- **Asynchronous**: Use for batch processing of large document collections (up to 10 GB)
- **Parallel Jobs**: Run multiple batch jobs concurrently for higher throughput
- **Text Preprocessing**: Clean and normalize text before analysis for better accuracy
- **Transcribe Integration**: Use Amazon Transcribe Medical to convert audio to text first, then analyze with Comprehend Medical

### Operations
- **Monitoring**: Use CloudWatch to monitor API call metrics, errors, and latency
- **Batch Job Management**: Use DescribeEntitiesDetectionV2Job / DescribePHIDetectionJob to monitor job progress
- **Error Handling**: Implement retry logic with exponential backoff for throttled requests
- **Manifest Files**: Review batch job manifest files (summary, success, unprocessed) for job status and error details
- **Human Review**: Always have trained medical professionals review results in patient care scenarios

## 7. Hands-On Labs

### Quick Setup: Detect Medical Entities
```python
import boto3
import json

client = boto3.client('comprehendmedical', region_name='us-east-1')

text = """Mr. Smith is a 63-year-old gentleman with coronary artery disease 
and hypertension. CURRENT MEDICATIONS: taking LIPITOR 20 mg once daily."""

response = client.detect_entities_v2(Text=text)

for entity in response['Entities']:
    print(f"{entity['Text']}: {entity['Category']} / {entity['Type']} "
          f"(confidence: {entity['Score']:.2f})")
    for trait in entity.get('Traits', []):
        print(f"  Trait: {trait['Name']} ({trait['Score']:.2f})")
    for attr in entity.get('Attributes', []):
        print(f"  Attribute: {attr['Type']} = {attr['Text']} ({attr['Score']:.2f})")
```

### Practical Exercise 1: Detect PHI
```python
text = """Patient John Smith, DOB 03/15/1960, MRN 123456. 
Address: 123 Main St, Seattle, WA. Phone: 555-0123. 
Email: john.smith@example.com"""

response = client.detect_phi(Text=text)

print("PHI Entities Found:")
for entity in response['Entities']:
    print(f"  {entity['Type']}: '{entity['Text']}' "
          f"(offset: {entity['BeginOffset']}-{entity['EndOffset']}, "
          f"confidence: {entity['Score']:.2f})")
```

### Practical Exercise 2: Ontology Linking (ICD-10-CM)
```python
text = "The patient was diagnosed with type 2 diabetes mellitus and essential hypertension."

response = client.infer_icd10_cm(Text=text)

for entity in response['Entities']:
    print(f"\nCondition: {entity['Text']}")
    for concept in entity.get('ICD10CMConcepts', [])[:3]:
        print(f"  ICD-10-CM: {concept['Code']} - {concept['Description']} "
              f"(score: {concept['Score']:.2f})")
```

### Practical Exercise 3: Medication Linking (RxNorm)
```python
text = "Patient is taking Metformin 500mg twice daily and Lisinopril 10mg once daily."

response = client.infer_rx_norm(Text=text)

for entity in response['Entities']:
    print(f"\nMedication: {entity['Text']}")
    for concept in entity.get('RxNormConcepts', [])[:3]:
        print(f"  RxCUI: {concept['Code']} - {concept['Description']} "
              f"(score: {concept['Score']:.2f})")
```

### CLI Commands
```bash
# Detect medical entities
aws comprehendmedical detect-entities-v2 \
  --text "Patient has type 2 diabetes and is taking Metformin 500mg daily."

# Detect PHI
aws comprehendmedical detect-phi \
  --text "Patient John Smith, age 63, MRN 12345."

# Infer ICD-10-CM codes
aws comprehendmedical infer-icd10-cm \
  --text "Patient diagnosed with essential hypertension."

# Infer RxNorm codes
aws comprehendmedical infer-rx-norm \
  --text "Patient is taking Lipitor 20mg once daily."

# Infer SNOMED CT codes
aws comprehendmedical infer-snomedct \
  --text "Patient underwent appendectomy for acute appendicitis."

# Start batch entity detection job
aws comprehendmedical start-entities-detection-v2-job \
  --input-data-config S3Bucket=my-input-bucket,S3Key=clinical-notes/ \
  --output-data-config S3Bucket=my-output-bucket,S3Key=results/ \
  --data-access-role-arn arn:aws:iam::<account-id>:role/ComprehendMedicalRole \
  --language-code en

# Describe batch job status
aws comprehendmedical describe-entities-detection-v2-job \
  --job-id <job-id>
```

## 8. Sample Questions

### Question 1
A hospital wants to automatically extract diagnosis codes from physician discharge summaries to streamline their medical billing process. The summaries are written in English. Which approach should they use?

A) Amazon Comprehend Custom Entity Recognition trained on billing codes
B) Amazon Comprehend Medical InferICD10CM API
C) Amazon Bedrock with a prompt to extract ICD-10 codes
D) Amazon SageMaker with a custom NER model

**Correct Answer: B**
- ✅ B: InferICD10CM is purpose-built to detect medical conditions in clinical text and link them directly to ICD-10-CM billing codes. No training required, HIPAA-eligible, and designed exactly for this use case.
- ❌ A: Standard Comprehend Custom Entity Recognition is for general text, not medical-specific. It would require training data and wouldn't link to ICD-10-CM codes.
- ❌ C: Bedrock could attempt this but is not purpose-built for medical coding, not HIPAA-eligible by default, and would be less accurate and more expensive.
- ❌ D: SageMaker custom model is overkill — requires ML expertise, training data, and infrastructure management for a task that Comprehend Medical handles out of the box.

---

### Question 2
A healthcare research organization needs to de-identify thousands of clinical trial documents stored in Amazon S3 before sharing them with external researchers. The documents contain patient names, dates of birth, medical record numbers, and addresses. Which is the MOST efficient approach?

A) Use Amazon Comprehend PII Detection in batch mode
B) Use Amazon Comprehend Medical DetectPHI synchronous API on each document
C) Use Amazon Comprehend Medical StartPHIDetectionJob for batch processing
D) Use Amazon Textract to extract text and then manually redact PHI

**Correct Answer: C**
- ✅ C: StartPHIDetectionJob is the batch API specifically designed for detecting PHI across large document collections in S3. It's HIPAA-eligible and purpose-built for medical PHI detection using Safe Harbor guidelines.
- ❌ A: Standard Comprehend PII Detection handles general PII but is not HIPAA-eligible and not optimized for medical-specific PHI types (MRN, clinical trial numbers).
- ❌ B: Synchronous DetectPHI works on single documents — processing thousands individually is inefficient compared to a batch job.
- ❌ D: Manual redaction is not scalable for thousands of documents and defeats the purpose of automation.

---

### Question 3
A pharmaceutical company wants to analyze clinical notes to identify all medications patients are taking, including dosage, frequency, and route of administration, and map them to standardized drug identifiers. Which combination of Amazon Comprehend Medical APIs should they use?

A) DetectEntitiesV2 only
B) InferRxNorm only
C) DetectPHI followed by InferICD10CM
D) InferRxNorm for medication linking, with DetectEntitiesV2 for detailed attributes

**Correct Answer: B**
- ✅ B: InferRxNorm detects medications as entities (including attributes like dosage, frequency, route) AND links them to standardized RxNorm concept identifiers (RxCUI). It provides both entity extraction and ontology linking in a single API call.
- ❌ A: DetectEntitiesV2 extracts medication entities with attributes but does NOT link them to RxNorm codes.
- ❌ C: DetectPHI is for patient identifiers, and InferICD10CM is for medical conditions — neither addresses medication standardization.
- ❌ D: While this would work, it's unnecessarily complex. InferRxNorm alone provides medication entities with attributes AND RxNorm codes.

---

**Exam Tip**: Remember the three ontology linking APIs and their specific knowledge bases — this is a high-frequency exam topic:
- **InferICD10CM** = Medical conditions → diagnosis/billing codes
- **InferRxNorm** = Medications → standardized drug identifiers
- **InferSNOMEDCT** = Broad medical concepts → clinical terminology codes

Also remember: Comprehend Medical is **pre-trained only** (no custom models), **US English only**, and **HIPAA-eligible**. It does NOT do sentiment analysis — that's standard Comprehend.
