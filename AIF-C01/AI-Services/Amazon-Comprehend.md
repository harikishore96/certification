# Amazon Comprehend

## 1. Service/Topic Overview
- **Purpose**: Fully managed NLP service that uses ML to extract insights and relationships from text
- **Service Level**: Regional
- **Key Use Cases**: 
  - Sentiment analysis for customer feedback
  - Entity extraction from documents
  - Topic modeling for document organization
  - Language detection for multilingual content
  - PII detection and redaction for compliance
- **Exam Weight**: HIGH
- **Exam Domain**: 
  - Domain 1: Fundamentals of AI and ML (20%)
  - Domain 3: Applications of Foundation Models (28%)
  - Domain 5: Security, Compliance, and Governance (14%)
- **Task Statements**: 
  - Task 1.2: Identify practical use cases for AI
  - Task 3.1: Describe the capabilities of foundation models and AI services
  - Task 5.2: Recognize security and compliance requirements for AI systems
  - Task 1.1: Explain basic AI concepts and terminology
  - Task 3.2: Select appropriate AI services for specific use cases

## 2. Exam Keyword Mapping
- **Trigger Words**: 
  - "Analyze text" or "Text analysis"
  - "Sentiment analysis"
  - "Extract entities" or "Named entity recognition"
  - "Detect PII" or "Personally identifiable information"
  - "Topic modeling"
  - "Language detection"
  - "Custom classification"
  - "Medical text" → Comprehend Medical
  - "Toxic content" or "Content moderation" → Toxicity Detection
  - "Prompt safety" or "Harmful prompts" → Prompt Safety Classification
  - "Events" or "Who-what-when-where" → Events Detection
  - "Manage custom model versions" → Flywheels
- **Scenario Indicators**:
  - "Analyze customer reviews" → Sentiment Analysis
  - "Extract names, dates, locations" → Entity Recognition
  - "Organize documents by theme" → Topic Modeling
  - "Redact sensitive information" → PII Detection
  - "Classify support tickets" → Custom Classification
  - "Analyze medical records" → Comprehend Medical
  - "Sentiment toward specific entities in text" → Targeted Sentiment
  - "Detect toxic or harmful text" → Toxicity Detection
  - "Filter unsafe LLM prompts" → Prompt Safety Classification
  - "Extract event details from news articles" → Events Detection
  - "Continuously retrain custom models" → Flywheels
  - "Analyze PDFs or Word documents" → Document Processing (Custom models)
- **Anti-patterns**:
  - ❌ Don't use for text generation (use Bedrock instead)
  - ❌ Don't use for translation (use Amazon Translate)
  - ❌ Don't use for speech-to-text (use Amazon Transcribe)
  - ❌ Don't use for document search/retrieval (use Amazon Kendra)
  - ❌ Don't use for image/video analysis (use Amazon Rekognition)

## 3. Core Concepts

### Service Architecture
```
Text Input
    ↓
Amazon Comprehend
├── Pre-built APIs (No training)
│   ├── Sentiment Analysis
│   ├── Entity Recognition
│   ├── Key Phrase Extraction
│   ├── Language Detection
│   ├── Syntax Analysis
│   └── PII Detection
│
└── Custom Models (Training required)
    ├── Custom Classification
    └── Custom Entity Recognition
    ↓
Structured Insights (JSON)
```

### Integration Points
- **S3**: Input/output for batch processing
- **Lambda**: Real-time text analysis triggers
- **Kinesis / Firehose**: Stream processing for real-time analysis
- **CloudWatch**: Monitoring and logging
- **IAM**: Access control and permissions
- **KMS**: Encryption for data at rest AND storage volume + output results
- **VPC**: Private endpoint access (PrivateLink)
- **Textract**: Used internally for text extraction from images/scanned PDFs
- **CloudTrail**: Audit logging for all API calls

### Data Flow
```
Synchronous (Real-time):
Text → Comprehend API → Insights (< 1 second)

Asynchronous (Batch):
S3 Input → Comprehend Job → S3 Output (minutes to hours)
```

## 4. Key Features & Components

### Document Processing
- **Supported Input Types**:
  - Plain text (UTF-8 encoded)
  - Semi-structured: PDF, Microsoft Word (.docx), images (JPG, PNG, TIFF)
- **Text Extraction**:
  - Native PDFs/Word: Internal parsers
  - Images/scanned documents: Uses Amazon Textract APIs internally
- **Applies To**: Custom classification and custom entity recognition
- **Note**: Pre-built APIs only accept plain text; custom models accept all formats

### Pre-built APIs (No Training Required)

#### Sentiment Analysis
- **Purpose**: Determine emotional tone (Positive, Negative, Neutral, Mixed)
- **Output**: Sentiment label + confidence scores for each sentiment
- **Use Cases**:
  - Customer review analysis
  - Social media monitoring
  - Brand perception tracking
  - Support ticket prioritization
- **Languages**: 100+ languages supported
- **Example Output**:
```json
{
  "Sentiment": "POSITIVE",
  "SentimentScore": {
    "Positive": 0.95,
    "Negative": 0.01,
    "Neutral": 0.03,
    "Mixed": 0.01
  }
}
```

#### Entity Recognition
- **Purpose**: Identify and categorize entities in text
- **Entity Types**:
  - PERSON: People names
  - LOCATION: Places, cities, countries
  - ORGANIZATION: Companies, agencies
  - DATE: Dates and times
  - QUANTITY: Measurements, amounts
  - EVENT: Named events
  - COMMERCIAL_ITEM: Products, brands
  - TITLE: Job titles, honorifics
  - OTHER: Miscellaneous entities
- **Use Cases**:
  - Document indexing
  - Information extraction
  - Content categorization
  - Knowledge graph building

#### Key Phrase Extraction
- **Purpose**: Extract important phrases that describe the content
- **Output**: List of key phrases with confidence scores
- **Use Cases**:
  - Document summarization
  - Search indexing
  - Content tagging
  - SEO optimization

#### Language Detection
- **Purpose**: Identify the dominant language in text
- **Output**: Language code + confidence score
- **Languages**: 100+ languages
- **Use Cases**:
  - Routing multilingual content
  - Content filtering
  - Translation preparation

#### Syntax Analysis (Part-of-Speech Tagging)
- **Purpose**: Analyze grammatical structure
- **Output**: Token, part of speech, confidence
- **Use Cases**:
  - Grammar checking
  - Text normalization
  - Linguistic analysis

#### Targeted Sentiment
- **Purpose**: Determine sentiment toward SPECIFIC entities within text (more granular than document-level sentiment)
- **Output**: Entity mentions + per-entity sentiment (Positive, Negative, Neutral, Mixed) + confidence scores
- **Co-reference Groups**: Groups multiple mentions of the same entity (e.g., "the spa" and "it" → same entity)
- **Entity Types**: PERSON, LOCATION, ORGANIZATION, FACILITY, BRAND, COMMERCIAL_ITEM, MOVIE, MUSIC, BOOK, SOFTWARE, GAME, PERSONAL_TITLE, EVENT, DATE, QUANTITY, ATTRIBUTE, OTHER
- **Language**: English only
- **Use Cases**:
  - Restaurant reviews: "Loved the tacos but service was slow" → tacos=Positive, service=Negative
  - Product comparisons: Identify which features customers like/dislike
  - Competitive analysis: Sentiment per brand/product mentioned
- **Key Difference from Sentiment Analysis**: Document-level sentiment returns ONE sentiment for the whole text; targeted sentiment returns sentiment PER ENTITY

#### Events Detection
- **Purpose**: Extract event structures from documents (who-what-when-where)
- **Output**: Event type, participants, timestamps, locations
- **Use Cases**:
  - News article analysis
  - Financial event extraction
  - Timeline construction from large document sets
  - AI application pipelines and graph visualization

#### PII Detection and Redaction
- **Purpose**: Identify and optionally redact personally identifiable information
- **PII Types Detected**:
  - Names
  - Addresses
  - Email addresses
  - Phone numbers
  - SSN, credit card numbers
  - Bank account numbers
  - Driver's license numbers
  - Passport numbers
  - IP addresses
  - MAC addresses
- **Modes**:
  - **Offsets** (Detection): Identify PII locations in text
  - **Labels**: Return PII type labels
  - **Redaction** (Async): Replace PII with [PII_TYPE] in output
- **Use Cases**:
  - Compliance (GDPR, HIPAA, PCI-DSS)
  - Data anonymization
  - Log sanitization
  - Document sharing
  - Pre-processing before indexing in search solutions

#### Toxicity Detection
- **Purpose**: Detect toxic content in text-based documents
- **Output**: Toxicity score and labels
- **Use Cases**:
  - Moderate peer-to-peer conversations on online platforms
  - Filter generative AI inputs and outputs
  - Content moderation for user-generated content
- **API**: DetectToxicContent
- **Note**: Out-of-the-box, no training required

#### Prompt Safety Classification
- **Purpose**: Pre-trained binary classifier to detect harmful prompts
- **Output**: Safe / Unsafe classification
- **Use Cases**:
  - Filter harmful prompts before sending to LLMs
  - Add safety layer to generative AI applications
  - Complement Bedrock Guardrails for prompt filtering
- **API**: ClassifyDocument (with pre-trained prompt safety model)
- **Note**: Can be integrated as a pre-processing step for LLM pipelines

### Custom Models (Training Required)

#### Custom Classification
- **Purpose**: Train model to categorize documents into custom classes
- **Uses AutoML**: No ML expertise required — Comprehend builds the model automatically
- **Types**:
  - **Multi-class**: Single label per document (e.g., Support category)
  - **Multi-label**: Multiple labels per document (e.g., Article topics)
- **Input Formats**:
  - Plain text (CSV: Label, Text)
  - Semi-structured: PDF, Word, images (native document models)
- **Requirements**:
  - Training data: Minimum 50 documents per class (250+ recommended)
  - Maximum 1000 classes
- **Deployment**:
  - **Real-time**: Create endpoint with inference units (IUs) for throughput
  - **Asynchronous**: Batch classification jobs
- **Use Cases**:
  - Support ticket routing
  - Content moderation
  - Document classification
  - Email categorization

#### Custom Entity Recognition
- **Purpose**: Train model to identify domain-specific entities
- **Uses AutoML**: Learns from small set of examples, no ML expertise required
- **Training Options**:
  - **Annotations**: Manually annotated entities with locations (more accurate)
  - **Entity Lists**: Plaintext list of entities with type labels (easier to create)
- **Input Formats**: Plain text, PDF, images (JPG, PNG, TIFF)
- **Requirements**:
  - Training data: Minimum 250 documents (1000+ recommended)
  - Up to 25 custom entity types per model
  - English only for annotated PDF files
- **Advantages over String Matching**:
  - Learns context (not just exact matches)
  - Adapts to typos and naming variations
  - More robust than regex patterns
- **Use Cases**:
  - Policy number extraction (insurance)
  - Legal document analysis
  - Industry-specific terminology
  - Product catalog extraction

### Flywheels
- **Purpose**: Simplify training and managing custom model versions over time
- **How It Works**:
  - Orchestrates tasks for training and evaluating new model versions
  - Manages the lifecycle of custom models automatically
  - Supports iterative improvement with new data
- **Supports**: Plain-text custom models for classification and entity recognition
- **Cost**: No additional charge for flywheels; standard training/storage charges apply when running iterations
- **Use Cases**:
  - Continuous model improvement as new labeled data becomes available
  - Automated retraining pipelines
  - Model version management

### Amazon Comprehend Medical
- **Purpose**: Specialized NLP for medical/clinical text (HIPAA-eligible)
- **Separate Service**: Has its own API endpoint and documentation
- **Input**: Unstructured clinical text (physician notes, discharge summaries, test results, case notes)

#### Text Analysis APIs
| API | Purpose |
|-----|--------|
| DetectEntitiesV2 | Extract medical entities (medications, conditions, procedures, anatomy) |
| DetectPHI | Identify Protected Health Information (names, addresses, dates) |
| Batch APIs | Process large document sets stored in S3 |

#### Ontology Linking APIs
| API | Ontology | Use Case |
|-----|----------|----------|
| InferRxNorm | RxNorm | Link medications to standardized drug codes |
| InferICD10CM | ICD-10-CM | Link conditions to medical diagnosis codes |
| InferSNOMEDCT | SNOMED CT | Link clinical terms to standardized medical concepts |

- **Entity Types**:
  - MEDICATION (dosage, frequency, route, strength)
  - MEDICAL_CONDITION (diagnosis, signs, symptoms)
  - TEST_TREATMENT_PROCEDURE
  - ANATOMY (body parts, organ systems)
  - PROTECTED_HEALTH_INFORMATION
- **Output**: Entities with confidence scores, relationships between entities, linked ontology codes
- **Use Cases**:
  - Medical coding in patient records
  - Clinical documentation analysis
  - PHI detection and compliance
  - Medication validation
  - Pharmacovigilance
  - Clinical trial matching
- **Limitations**:
  - US English only
  - May not suit organizations with custom entity definitions
  - Not for custom NLP tasks beyond medical domain
- **Access**: Console, CLI, SDKs; supports VPC endpoints (PrivateLink)

## 5. Exam Focus Areas

### Common Question Types

#### Scenario-Based Selection
**Question Pattern**: "A company needs to [analyze text task]. Which AWS service should they use?"

**Decision Framework**:
```
Text Analysis Need?
│
├─ Sentiment/Entities/Topics → Comprehend
├─ Translation → Translate
├─ Speech-to-text → Transcribe
├─ Text-to-speech → Polly
├─ Conversational interface → Lex
├─ Text generation → Bedrock
└─ Document search → Kendra
```

#### Feature Comparison Questions
| Task | Comprehend Feature | Training Required | Use Case |
|------|-------------------|-------------------|----------|
| Determine if review is positive | Sentiment Analysis | No | Customer feedback |
| Extract person names | Entity Recognition | No | Document indexing |
| Classify support tickets | Custom Classification | Yes | Ticket routing |
| Find medical conditions | Comprehend Medical | No | Clinical analysis |
| Detect credit card numbers | PII Detection | No | Compliance |
| Extract company-specific terms | Custom Entity Recognition | Yes | Domain-specific NLP |

### Gotchas
1. **Real-time vs. Batch**: Synchronous API for <100KB, Asynchronous for large datasets
2. **Custom Models Need Training**: Can't use custom classification without training data
3. **Language Support**: Not all features support all languages — Targeted Sentiment is English only
4. **PII Redaction**: Separate API call from detection (Offsets vs. Labels modes)
5. **Comprehend Medical**: Separate service, HIPAA-eligible, US English only
6. **Toxicity Detection vs. Bedrock Guardrails**: Comprehend Toxicity is for standalone text moderation; Bedrock Guardrails is for FM input/output filtering
7. **Prompt Safety**: Binary classifier (safe/unsafe) — simpler than Bedrock Guardrails but can complement it
8. **Document Formats**: Pre-built APIs = plain text only; Custom models = plain text + PDF + Word + images
9. **Flywheels**: Only support plain-text custom models (not semi-structured document models)
10. **Custom Endpoints**: Real-time custom model inference requires creating an endpoint — you pay from endpoint creation until deletion
11. **Targeted Sentiment vs. Sentiment**: Targeted = per-entity; Sentiment = per-document. Common exam distractor

### Comparison Points

#### Comprehend vs. Bedrock
```
Comprehend
├─ Purpose: Text analysis and insights
├─ Output: Structured data (sentiment, entities)
├─ Training: Pre-built or custom models
└─ Use: Understanding existing text

Bedrock
├─ Purpose: Text generation and understanding
├─ Output: Generated text
├─ Training: Foundation models (fine-tuning optional)
└─ Use: Creating new content
```

#### Comprehend vs. Comprehend Medical
```
Comprehend
├─ Domain: General text
├─ Entities: General (person, location, organization)
├─ Compliance: Standard AWS compliance
└─ Languages: 100+

Comprehend Medical
├─ Domain: Medical/clinical text
├─ Entities: Medical-specific (medications, conditions)
├─ Compliance: HIPAA-eligible
└─ Languages: US English only
```

#### Custom Classification vs. Custom Entity Recognition
```
Custom Classification
├─ Task: Categorize entire document
├─ Output: Document-level labels
├─ Training: 50+ docs per class
├─ Uses: AutoML
└─ Example: "This is a billing inquiry"

Custom Entity Recognition
├─ Task: Identify specific terms/phrases
├─ Output: Entity locations and types
├─ Training: 250+ annotated docs OR entity lists
├─ Uses: AutoML
└─ Example: "Extract policy number 456-YQT from text"
```

#### Sentiment vs. Targeted Sentiment
```
Sentiment Analysis
├─ Scope: Entire document
├─ Output: One sentiment (Positive/Negative/Neutral/Mixed)
├─ Languages: 12+ languages
└─ Example: "Overall this review is MIXED"

Targeted Sentiment
├─ Scope: Per entity within document
├─ Output: Sentiment per entity mention + co-reference groups
├─ Languages: English only
└─ Example: "burger=Positive, service=Negative"
```

#### Toxicity Detection vs. Bedrock Guardrails
```
Comprehend Toxicity Detection
├─ Scope: Standalone text moderation API
├─ Output: Toxicity score and labels
├─ Integration: Any application
└─ Use: General content moderation

Bedrock Guardrails
├─ Scope: FM input/output filtering
├─ Output: Blocked/allowed + policy violations
├─ Integration: Bedrock FM invocations
└─ Use: Generative AI safety controls
```

### Decision Trees

#### Customer Feedback Analysis
```
✅ Use Comprehend Sentiment Analysis
- Pre-built, no training required
- Supports 100+ languages
- Real-time or batch processing
- Provides confidence scores

❌ Don't use Bedrock
- Bedrock is for generation, not analysis
- More expensive for simple sentiment
```

#### Medical Record Analysis
```
✅ Use Comprehend Medical
- HIPAA-eligible
- Pre-trained on medical text
- Extracts medical entities
- Links to medical ontologies

❌ Don't use Standard Comprehend
- Not specialized for medical terminology
- Not HIPAA-eligible
- Won't recognize medical entities accurately
```

#### Support Ticket Routing
```
✅ Use Comprehend Custom Classification
- Train on historical tickets
- Multi-class classification
- Automated routing
- Improves over time

❌ Don't use Pre-built Entity Recognition
- Doesn't categorize documents
- Only extracts entities, doesn't classify
```

## 6. Best Practices

### Security
- **IAM Policies**: Grant least privilege access
- **VPC Endpoints**: Use PrivateLink for private connectivity (no internet traversal)
- **Encryption**: 
  - Data in transit: TLS 1.2 or 1.3 with PFS (DHE/ECDHE cipher suites)
  - Data at rest: KMS encryption for S3 input documents
  - **Volume encryption**: KMS encrypts storage volume attached to compute instance processing the job
  - **Output encryption**: KMS encrypts output results of analysis jobs
- **Authentication**: Access key ID + secret access key (IAM principal) or temporary STS credentials
- **PII Handling**: Use PII detection before storing/sharing
- **Audit Logging**: Enable CloudTrail for all API calls

### Cost Optimization
- **Pricing Model**: Pay per request based on number of API calls + size of input data
- **Free Tier**: Available for new AWS customers
- **Batch Processing**: Use asynchronous jobs for large datasets (cheaper)
- **Right-Sizing**: Use synchronous API only for real-time needs
- **Custom Model Endpoints**: Pay from endpoint creation to deletion — delete when not in use
- **Flywheels**: No additional charge; only standard training/storage costs per iteration
- **Custom Models**: Reuse trained models across applications
- **Text Preprocessing**: Remove unnecessary content before analysis
- **Caching**: Cache results for frequently analyzed text

### Performance
- **Batch Size**: Optimal 5000-10000 documents per batch job
- **Text Length**: Maximum 100KB per document (synchronous), 5000 documents (async)
- **Parallel Processing**: Use multiple API calls for high throughput
- **Regional Deployment**: Deploy in region closest to data source

### Operations
- **Monitoring**: 
  - CloudWatch metrics: API calls, errors, latency
  - Set alarms for error rates
- **Error Handling**: Implement retry logic with exponential backoff
- **Model Versioning**: Track custom model versions
- **Testing**: Validate custom models before production deployment

## 7. Hands-On Labs

### Quick Setup: Sentiment Analysis
```python
import boto3

# Initialize Comprehend client
comprehend = boto3.client('comprehend', region_name='us-east-1')

# Analyze sentiment
text = "I love this product! It exceeded my expectations."
response = comprehend.detect_sentiment(
    Text=text,
    LanguageCode='en'
)

print(f"Sentiment: {response['Sentiment']}")
print(f"Scores: {response['SentimentScore']}")
```

### Practical Exercise 1: Entity Recognition
```python
# Detect entities
text = "Amazon Web Services was founded by Jeff Bezos in Seattle in 2006."
response = comprehend.detect_entities(
    Text=text,
    LanguageCode='en'
)

for entity in response['Entities']:
    print(f"{entity['Text']}: {entity['Type']} (confidence: {entity['Score']:.2f})")
```

### Practical Exercise 2: PII Detection and Redaction
```python
# Detect PII
text = "My name is John Doe and my email is john.doe@example.com. Call me at 555-1234."

# Detection
detect_response = comprehend.detect_pii_entities(
    Text=text,
    LanguageCode='en'
)

print("PII Entities Found:")
for entity in detect_response['Entities']:
    print(f"  {entity['Type']}: {text[entity['BeginOffset']:entity['EndOffset']]}")

# Redaction
redact_response = comprehend.contain_pii_entities(
    Text=text,
    LanguageCode='en'
)

print(f"\nRedacted Text: {redact_response['RedactedText']}")
```

### Practical Exercise 3: Custom Classification (Training)
```python
# Start custom classification training job
response = comprehend.create_document_classifier(
    DocumentClassifierName='support-ticket-classifier',
    DataAccessRoleArn='arn:aws:iam::123456789012:role/ComprehendRole',
    InputDataConfig={
        'S3Uri': 's3://my-bucket/training-data.csv',
        'DataFormat': 'COMPREHEND_CSV'
    },
    OutputDataConfig={
        'S3Uri': 's3://my-bucket/output/'
    },
    LanguageCode='en',
    Mode='MULTI_CLASS'
)

classifier_arn = response['DocumentClassifierArn']
print(f"Training job started: {classifier_arn}")
```

### CLI Commands
```bash
# Detect sentiment
aws comprehend detect-sentiment \
  --text "This is a great product!" \
  --language-code en

# Batch sentiment analysis
aws comprehend start-sentiment-detection-job \
  --input-data-config S3Uri=s3://my-bucket/input/ \
  --output-data-config S3Uri=s3://my-bucket/output/ \
  --data-access-role-arn arn:aws:iam::123456789012:role/ComprehendRole \
  --language-code en

# Detect PII
aws comprehend detect-pii-entities \
  --text "My SSN is 123-45-6789" \
  --language-code en

# List custom classifiers
aws comprehend list-document-classifiers
```

## 8. Sample Questions

### Question 1: Service Selection
**Scenario**: A customer service team receives thousands of support emails daily. They want to automatically categorize emails into predefined categories (Billing, Technical, Account, General) to route them to the appropriate team.

Which Amazon Comprehend feature should they use?

A) Pre-built Entity Recognition  
B) Custom Classification with multi-class mode  
C) Sentiment Analysis  
D) Topic Modeling

**Correct Answer**: B) Custom Classification with multi-class mode

**Explanation**:
- ✅ **B is correct**: Custom Classification allows training a model on historical emails labeled with categories. Multi-class mode assigns exactly one category per email, perfect for routing to specific teams. Requires training data with at least 50 emails per category.

- ❌ **A is wrong**: Entity Recognition extracts specific information (names, dates) but doesn't categorize entire documents into predefined classes.

- ❌ **C is wrong**: Sentiment Analysis determines emotional tone (positive/negative), not functional categories like Billing or Technical.

- ❌ **D is wrong**: Topic Modeling discovers themes in unlabeled data but doesn't classify into predefined categories. It's unsupervised, not suitable for routing to specific teams.

### Question 2: Compliance Requirement
**Scenario**: A healthcare provider wants to analyze patient feedback surveys to improve services. The surveys contain patient names, medical record numbers, and other PHI. They need to comply with HIPAA regulations while performing sentiment analysis.

What is the MOST secure approach?

A) Use Amazon Comprehend Sentiment Analysis directly on the raw survey text  
B) Use Amazon Comprehend Medical to analyze the surveys  
C) Use Comprehend PII Detection to redact PHI, then perform Sentiment Analysis  
D) Use Amazon Bedrock to summarize and analyze the surveys

**Correct Answer**: C) Use Comprehend PII Detection to redact PHI, then perform Sentiment Analysis

**Explanation**:
- ✅ **C is correct**: First use PII Detection to identify and redact PHI (names, MRNs, etc.), then perform sentiment analysis on the redacted text. This ensures compliance while still extracting sentiment insights. Comprehend PII Detection can identify medical PHI types.

- ❌ **A is wrong**: Analyzing raw text with PHI without redaction violates HIPAA compliance requirements. Must remove or redact PHI first.

- ❌ **B is wrong**: Comprehend Medical is for extracting medical entities (conditions, medications), not for sentiment analysis. It doesn't perform sentiment analysis on patient feedback.

- ❌ **D is wrong**: Bedrock isn't specifically designed for PII redaction or HIPAA compliance. Comprehend PII Detection is the purpose-built service for this task.

### Question 3: Real-time vs. Batch
**Scenario**: An e-commerce company wants to analyze product reviews. They have 500,000 existing reviews to analyze initially, and receive about 1,000 new reviews per day that need analysis within minutes of submission.

Which approach is MOST cost-effective?

A) Use synchronous Comprehend API for all reviews  
B) Use asynchronous batch jobs for existing reviews, synchronous API for new reviews  
C) Use asynchronous batch jobs for all reviews  
D) Use Amazon Bedrock to analyze all reviews

**Correct Answer**: B) Use asynchronous batch jobs for existing reviews, synchronous API for new reviews

**Explanation**:
- ✅ **B is correct**: Asynchronous batch processing is more cost-effective for large datasets (500K existing reviews) and doesn't require real-time results. Synchronous API is appropriate for new reviews that need near-real-time analysis (within minutes). This hybrid approach optimizes both cost and latency.

- ❌ **A is wrong**: Synchronous API for 500K reviews would be significantly more expensive and unnecessary since historical reviews don't need real-time processing.

- ❌ **C is wrong**: Asynchronous batch jobs have higher latency (minutes to hours), which doesn't meet the requirement for new reviews to be analyzed "within minutes."

- ❌ **D is wrong**: Bedrock is for text generation, not analysis. Comprehend is purpose-built and more cost-effective for sentiment analysis at scale.

---

**Exam Tips**:
- Comprehend = text ANALYSIS (understanding existing text), NOT generation
- Pre-built APIs = no training required; Custom models = training required but uses AutoML (no ML expertise needed)
- Sentiment = document-level; Targeted Sentiment = per-entity (English only) — know the difference
- PII Detection/Redaction = compliance scenarios (GDPR, HIPAA, PCI-DSS)
- Custom Classification = categorize documents; Custom Entity Recognition = extract domain-specific terms
- Comprehend Medical = HIPAA-eligible, US English only, links to RxNorm/ICD-10-CM/SNOMED CT
- Toxicity Detection = content moderation; Prompt Safety = LLM input filtering
- Flywheels = manage custom model versions over time (continuous improvement)
- Document Processing: Pre-built APIs accept plain text only; Custom models accept PDF/Word/images too
- Topic Modeling = unsupervised clustering of documents by keyword similarity (stored in S3)
