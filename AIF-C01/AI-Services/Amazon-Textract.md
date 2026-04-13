# Amazon Textract

## 1. Service/Topic Overview
- **Purpose**: ML-powered document analysis service that automatically extracts text, handwriting, layout elements, structured data (forms, tables), and specific information from scanned documents — going beyond simple OCR
- **Service Level**: Regional
- **Key Use Cases**:
  - Extracting text and structured data from financial reports, tax forms, medical records
  - Automating data capture from forms (key-value pairs)
  - Processing invoices and receipts at scale
  - Identity document verification (US passports, driver's licenses)
  - Mortgage/lending document classification and extraction
- **Exam Weight**: Medium (Domain 1, Domain 3)
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — understanding pre-built AI services
  - Domain 3: Applications of Foundation Models (28%) — document processing pipelines
  - Domain 5: Security, Compliance, and Governance (14%) — data privacy, HIPAA
- **Task Statements**: Identify AWS AI services for document processing; select appropriate AI service for text/data extraction use cases

## 2. Exam Keyword Mapping
- **Trigger Words**: "extract text from documents", "OCR", "form extraction", "table extraction", "invoice processing", "receipt processing", "identity document", "handwriting recognition", "document analysis", "key-value pairs from forms", "structured data from scanned documents"
- **Scenario Indicators**:
  - "Company needs to digitize paper forms and extract field values" → Textract
  - "Automate processing of invoices/receipts" → Textract AnalyzeExpense
  - "Extract data from driver's licenses or passports" → Textract AnalyzeID
  - "Process mortgage loan packages automatically" → Textract Analyze Lending
  - "Extract specific information from documents using natural language questions" → Textract Queries
- **Anti-patterns**:
  - Text detection in live scenes/photos (not documents) → Amazon Rekognition DetectText
  - Understanding sentiment or entities in extracted text → Amazon Comprehend (use Textract + Comprehend together)
  - Translating extracted text → Amazon Translate (downstream from Textract)
  - Searching across extracted document content → Amazon Kendra
  - General image classification or object detection → Amazon Rekognition

## 3. Core Concepts

### Architectural Patterns
- **Synchronous Processing**: Single-page documents, low-latency use cases — pass document as S3 object or byte array
- **Asynchronous Processing**: Multi-page documents (PDF/TIFF up to 3,000 pages) — uses SNS notifications for completion
- **Block-based Response**: All results returned as `Block` objects with types (PAGE, LINE, WORD, KEY_VALUE_SET, TABLE, CELL, QUERY, SIGNATURE, LAYOUT)
- **Confidence Scores**: Every extracted element includes a confidence score (0-1) for downstream decision-making
- **Bounding Boxes**: All extracted data includes geometry/coordinates for precise location on the page

### Service Limits (Set Quotas — Cannot Be Changed)

| Limit | Synchronous | Asynchronous |
|-------|------------|--------------|
| File Formats | JPEG, PNG, PDF, TIFF | JPEG, PNG, PDF, TIFF |
| Max File Size | 10 MB | 10 MB (JPEG/PNG), 500 MB (PDF/TIFF) |
| Max Pages | 1 page | 3,000 pages |
| Queries per Page | 15 | 30 |

- **Additional Limits**:
  - Max image resolution: 10,000 pixels on all sides
  - Min text height: 15 pixels (≈ 8pt font at 150 DPI)
  - PDF max dimensions: 40 inches / 9,000 points
  - PDFs cannot be password protected
  - No XFA-based PDF support
  - Languages: English, French, German, Italian, Portuguese, Spanish (Queries and handwriting: English only)
  - AnalyzeID: US passports and US driver's licenses only

### Data Flow
```
Input Document (S3 / Byte Array)
    → Amazon Textract API (DetectText / AnalyzeDocument / AnalyzeExpense / AnalyzeID / AnalyzeLending)
        → ML Models (deep learning, pre-trained)
            → JSON Response (Block objects with text, geometry, confidence, relationships)
                → Downstream Processing (database, NLP, search index)
```

### Integration Points
- **Amazon S3**: Primary document storage and input source
- **Amazon Comprehend**: NLP analysis (sentiment, entities, key phrases) on extracted text
- **Amazon Kendra**: Build intelligent search indexes from extracted content
- **AWS Lambda**: Trigger Textract processing on S3 upload events
- **Amazon SNS/SQS**: Async job completion notifications
- **Amazon A2I (Augmented AI)**: Human review of low-confidence extractions
- **AWS Step Functions**: Orchestrate document processing workflows
- **Amazon CloudWatch**: Monitor API usage and performance
- **AWS PrivateLink**: VPC endpoints for private API access

## 4. Key Features & Components

### APIs Overview

| API | Purpose | Key Capability |
|-----|---------|---------------|
| **DetectDocumentText** | Basic OCR | Extract printed and handwritten text (lines, words) |
| **AnalyzeDocument** | Advanced analysis | Forms, Tables, Queries, Signatures, Layout extraction |
| **AnalyzeExpense** | Invoice/receipt processing | Normalized vendor names, line items, totals, payment terms |
| **AnalyzeID** | Identity documents | US passports and driver's licenses — names, DOB, expiry |
| **AnalyzeLending** | Mortgage documents | Auto-classify and route mortgage loan package pages |

### AnalyzeDocument FeatureTypes
- **FORMS**: Extracts key-value pairs (e.g., "First Name" → "Jane Doe") and selection elements (checkboxes, radio buttons)
- **TABLES**: Extracts tables preserving row/column structure, titles, footers, and cell contents
- **QUERIES**: Natural language questions to extract specific data (e.g., "What is the customer SSN?")
- **SIGNATURES**: Detects signature locations with bounding boxes and confidence scores
- **LAYOUT**: Identifies paragraphs, titles, lists, headers, footers, page numbers, figures, section headers — returns in reading order

### Custom Queries (Adapters)
- Customize the pre-trained Queries feature for business-specific documents
- Train with as few as 10 sample documents via AWS Console
- Adapter training: 5–2,500 training samples, 5–1,000 test samples
- Adapters plug into the pre-trained model — provide AdapterId + AdapterVersion to API calls
- Training data encrypted at rest and in transit, deleted after training completes

### AnalyzeExpense Highlights
- Automatically finds vendor names (even from logos without explicit labels)
- Extracts line items, quantities, prices without labeled column headers
- Returns normalized key names for consistent downstream processing
- Works with diverse invoice/receipt layouts

### AnalyzeLending Highlights
- Managed, preconfigured intelligent document processing for mortgage packages
- Auto-classifies document pages by type (W-2, pay stub, bank statement, etc.)
- Routes pages to appropriate extraction operations
- Returns per-page and summarized results

### Block Object Structure
- **BlockType**: PAGE, LINE, WORD, KEY_VALUE_SET, TABLE, CELL, QUERY, QUERY_RESULT, SIGNATURE, LAYOUT_*
- **Text**: Detected text content
- **Confidence**: 0–100 confidence score
- **Geometry**: BoundingBox (coordinates) and Polygon
- **Relationships**: Parent-child links between blocks (PAGE → LINE → WORD)
- **TextType**: HANDWRITING or PRINTED

## 5. Exam Focus Areas

### Common Question Types
- **Scenario-based**: "A company needs to extract form data from scanned documents..." → Textract AnalyzeDocument with FORMS
- **Service selection**: "Which service extracts tables from PDF documents?" → Textract
- **Integration**: "How to analyze sentiment of text extracted from documents?" → Textract + Comprehend
- **Feature selection**: "Need to ask specific questions about document content" → Textract Queries

### Gotchas
- Textract ≠ Rekognition: Textract is for **documents**, Rekognition DetectText is for **photos/scenes**
- Synchronous = 1 page max; Asynchronous = up to 3,000 pages
- Queries are English-only; text extraction supports 6 languages
- AnalyzeID only supports **US** passports and driver's licenses
- Handwriting recognition is English-only
- Confidence scores are critical — use thresholds to route low-confidence results to human review (A2I)
- Custom Queries (Adapters) need only 10 sample documents to get started
- Content may be stored by AWS for service improvement — opt out via AWS Organizations policy

### Comparison Points

| Feature | Amazon Textract | Amazon Rekognition | Amazon Comprehend |
|---------|----------------|-------------------|-------------------|
| **Primary Use** | Document text/data extraction | Image/video analysis | NLP text analysis |
| **Text Detection** | Documents (printed + handwriting) | Text in photos/scenes | N/A (needs text input) |
| **Structured Data** | Forms, tables, key-value pairs | No | Entities, key phrases |
| **ML Expertise Needed** | None | None | None |
| **Input** | Documents (PDF, JPEG, PNG, TIFF) | Images/video | Text |

### Decision Trees
- ✅ **Use Textract when**: Extracting text/data from scanned documents, forms, invoices, receipts, IDs, mortgage packages
- ✅ **Use Textract Queries when**: Need specific answers from documents without knowing the structure
- ✅ **Use Textract + Comprehend when**: Need to understand meaning/sentiment of extracted text
- ✅ **Use Textract + A2I when**: Low-confidence extractions need human review
- ❌ **Don't use Textract when**: Detecting text in photos/live scenes (use Rekognition)
- ❌ **Don't use Textract when**: Need to search across many documents (use Kendra with Textract as data source)
- ❌ **Don't use Textract when**: Need to classify images or detect objects (use Rekognition)

## 6. Best Practices

### Security
- Use **IAM policies** to control access to Textract APIs and S3 document buckets
- Enable **encryption at rest** (AWS KMS) for documents in S3
- Use **VPC endpoints via AWS PrivateLink** to keep API calls within your VPC
- Enable **AWS CloudTrail** for auditing all Textract API calls
- **Opt out of data usage** for ML improvement via AWS Organizations AI services opt-out policy
- Textract is **HIPAA eligible** — requires executed BAA with AWS for PHI processing
- Compliant with **PCI DSS, ISO, SOC**

### Cost Optimization
- **Pay-per-page** pricing — no minimum fees or upfront commitments
- **Free Tier**: 3 months of free usage (limited pages per API)
- Use **DetectDocumentText** ($0.0015/page) for simple OCR instead of AnalyzeDocument ($0.05/page for forms) when you only need raw text
- Combine FeatureTypes in a single AnalyzeDocument call rather than making separate calls
- Use **asynchronous APIs** for batch processing to manage throughput
- Volume discounts available after 1 million pages
- Request custom pricing for high-volume use cases

### Pricing Summary (US West Oregon)

| API | Price per Page (First 1M) |
|-----|--------------------------|
| DetectDocumentText | $0.0015 |
| AnalyzeDocument - Forms | $0.05 |
| AnalyzeDocument - Tables | $0.015 |
| AnalyzeDocument - Queries | $0.015 |
| AnalyzeDocument - Signatures | $0.0035 |
| AnalyzeExpense | $0.01 |
| AnalyzeID | $0.025 (first 100K) |
| AnalyzeLending | $0.07 |

### Performance
- Provide high-quality images (≥150 DPI recommended)
- Don't convert or downsample documents already in supported formats
- Tables work best when visually separated from surrounding elements
- Use confidence score thresholds to filter results (e.g., flag scores < 95%)
- Use async operations for multi-page documents to avoid timeouts
- Monitor and request quota increases via Service Quotas console if hitting TPS limits

### Operations
- Monitor with **CloudWatch** metrics for API call counts, errors, latency
- Use **CloudTrail** for audit logging
- Implement retry logic with exponential backoff for throttling (ProvisionedThroughputExceededException)
- Use the **Textract Service Quotas Calculator** in the console to estimate quota needs

## 7. Hands-On Labs

### Quick Setup
1. Navigate to Amazon Textract console
2. Upload a sample document (PDF, JPEG, PNG, or TIFF)
3. Choose analysis type (Text, Forms, Tables, Queries)
4. Review extracted results with confidence scores and bounding boxes

### Practical Exercises
1. **Basic OCR**: Upload a scanned document → Use DetectDocumentText → Review extracted lines and words
2. **Form Extraction**: Upload a form with fields → Use AnalyzeDocument with FORMS → Extract key-value pairs
3. **Query-based Extraction**: Upload an invoice → Use Queries ("What is the total amount?") → Get specific answers

### CLI Commands
```bash
# Detect text from S3 document
aws textract detect-document-text \
    --document '{"S3Object":{"Bucket":"my-bucket","Name":"document.png"}}'

# Analyze document for forms and tables
aws textract analyze-document \
    --document '{"S3Object":{"Bucket":"my-bucket","Name":"form.png"}}' \
    --feature-types '["FORMS","TABLES"]'

# Analyze document with queries
aws textract analyze-document \
    --document '{"S3Object":{"Bucket":"my-bucket","Name":"invoice.png"}}' \
    --feature-types '["QUERIES"]' \
    --queries-config '{"Queries":[{"Text":"What is the total amount?"}]}'

# Start async text detection for multi-page PDF
aws textract start-document-text-detection \
    --document-location '{"S3Object":{"Bucket":"my-bucket","Name":"multipage.pdf"}}' \
    --notification-channel '{"SNSTopicArn":"arn:aws:sns:us-east-1:123456789012:TextractTopic","RoleArn":"arn:aws:iam::123456789012:role/TextractRole"}'

# Analyze expense (invoice/receipt)
aws textract analyze-expense \
    --document '{"S3Object":{"Bucket":"my-bucket","Name":"receipt.jpg"}}'

# Analyze identity document
aws textract analyze-id \
    --document-pages '[{"S3Object":{"Bucket":"my-bucket","Name":"drivers-license.jpg"}}]'
```

### Python (boto3) Example
```python
import boto3

textract = boto3.client('textract')

# Detect text
response = textract.detect_document_text(
    Document={'S3Object': {'Bucket': 'my-bucket', 'Name': 'document.png'}}
)

for block in response['Blocks']:
    if block['BlockType'] == 'LINE':
        print(f"Text: {block['Text']} | Confidence: {block['Confidence']:.1f}%")

# Analyze with Queries
response = textract.analyze_document(
    Document={'S3Object': {'Bucket': 'my-bucket', 'Name': 'form.pdf'}},
    FeatureTypes=['QUERIES'],
    QueriesConfig={'Queries': [{'Text': 'What is the patient name?'}]}
)
```

## 8. Sample Questions

### Question 1
**A healthcare company needs to digitize thousands of patient intake forms and extract field values like patient name, date of birth, and insurance ID into a database. The forms have varying layouts across different clinics. Which AWS service and feature should they use?**

A) Amazon Rekognition with custom labels
B) Amazon Textract AnalyzeDocument with FORMS feature
C) Amazon Comprehend entity detection
D) Amazon Textract DetectDocumentText

**Correct Answer: B**

**Explanation**: Amazon Textract AnalyzeDocument with the FORMS feature extracts key-value pairs (e.g., "Patient Name" → "John Doe") from documents regardless of layout variations. It understands form structure and preserves the relationship between fields and values.
- A is wrong: Rekognition is for image/video analysis (objects, faces), not document form extraction.
- C is wrong: Comprehend detects entities in text but doesn't extract structured form data from scanned documents — it needs text input, not document images.
- D is wrong: DetectDocumentText only extracts raw text (lines and words) without understanding form structure or key-value relationships.

---

### Question 2
**A financial services company processes thousands of invoices monthly from hundreds of vendors. Each vendor uses a different invoice format. They need to automatically extract vendor name, invoice number, line items, and total amount. Which Amazon Textract API is most appropriate?**

A) AnalyzeDocument with TABLES feature
B) DetectDocumentText
C) AnalyzeExpense
D) AnalyzeDocument with QUERIES feature

**Correct Answer: C**

**Explanation**: AnalyzeExpense is purpose-built for invoices and receipts. It automatically identifies vendor names (even from logos), extracts line items, quantities, prices, and totals, and returns normalized key names for consistent downstream processing — all without templates or configuration.
- A is wrong: While TABLES can extract tabular data, it doesn't understand invoice-specific semantics like vendor names from logos or normalized field names.
- B is wrong: DetectDocumentText only extracts raw text without any structural understanding.
- D is wrong: QUERIES could extract specific fields but would require crafting individual questions and doesn't provide the invoice-specific normalization that AnalyzeExpense offers.

---

### Question 3
**A company uses Amazon Textract to process sensitive medical documents. They want to ensure that their document content is NOT used by AWS to improve machine learning models. What should they do?**

A) Enable server-side encryption on the S3 bucket
B) Use VPC endpoints for Textract API calls
C) Create an AWS Organizations AI services opt-out policy
D) Process documents using only synchronous APIs

**Correct Answer: C**

**Explanation**: AWS Organizations AI services opt-out policy prevents AWS from using your content to improve Amazon Textract and other AI/ML services. When opted out, content is not stored outside your AWS region and is not used for model training.
- A is wrong: Encryption protects data at rest but doesn't prevent AWS from using content for service improvement.
- B is wrong: VPC endpoints provide private network access but don't control data usage policies.
- D is wrong: The processing mode (sync vs async) has no impact on data usage policies.

---

**Exam Tip**: Remember the API hierarchy — DetectDocumentText (basic OCR) < AnalyzeDocument (forms, tables, queries, signatures, layout) < specialized APIs (AnalyzeExpense, AnalyzeID, AnalyzeLending). Choose the most specific API for the use case. Also remember: Textract = documents, Rekognition = photos/scenes.
