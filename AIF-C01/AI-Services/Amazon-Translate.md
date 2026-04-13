# Amazon Translate

## 1. Service/Topic Overview
- **Purpose**: Neural machine translation service that delivers fast, high-quality, affordable, and customizable language translation using deep learning models
- **Service Level**: Regional
- **Key Use Cases**:
  - Translate company-authored content (reports, knowledge bases, posts)
  - Enable multilingual user experiences in applications
  - Translate interpersonal communications (email, chat, customer service)
  - Batch translate document repositories stored in S3
  - Provide subtitles and live captioning in multiple languages
- **Exam Weight**: Medium
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%)
  - Domain 3: Applications of Foundation Models (28%)
- **Task Statements**:
  - Task 1.2: Identify practical use cases for AI
  - Task 3.1: Describe the capabilities of AI services
  - Task 3.2: Select appropriate AI services for specific use cases

## 2. Exam Keyword Mapping
- **Trigger Words**:
  - "Translate text" or "Language translation"
  - "Multilingual" or "Multiple languages"
  - "Localization"
  - "Neural machine translation"
  - "Custom terminology"
  - "Parallel data"
  - "Brevity" or "Shorter translations"
  - "Formality" or "Formal/informal tone"
- **Scenario Indicators**:
  - "Translate customer reviews to English" → Real-time TranslateText API
  - "Translate thousands of documents" → Batch translation job
  - "Keep brand names untranslated" → Custom Terminology
  - "Match translation style to company tone" → Active Custom Translation (Parallel Data)
  - "Translate subtitles with shorter output" → Brevity setting
  - "Formal vs. informal translation" → Formality setting
  - "Mask profanity in translations" → Profanity masking
  - "Don't translate certain HTML tags" → Do-not-translate tags
  - "Auto-detect source language" → Auto language detection (uses Comprehend)
- **Anti-patterns**:
  - ❌ Don't use for text analysis/NLP (use Amazon Comprehend)
  - ❌ Don't use for speech-to-text (use Amazon Transcribe)
  - ❌ Don't use for text-to-speech (use Amazon Polly)
  - ❌ Don't use for text generation (use Amazon Bedrock)
  - ❌ Don't use for building chatbots (use Amazon Lex)

## 3. Core Concepts

### How It Works
```
Source Text (Language A)
    ↓
Amazon Translate (Neural MT Engine)
├── Deep learning models
├── Considers full sentence context
├── Optional: Custom Terminology
├── Optional: Parallel Data (ACT)
├── Optional: Brevity / Formality / Profanity settings
    ↓
Translated Text (Language B)
```

### Neural Machine Translation vs. Traditional
| Aspect | Neural MT (Amazon Translate) | Traditional (Statistical/Rule-based) |
|--------|------------------------------|--------------------------------------|
| Context | Considers entire sentence | Only a few words around target |
| Quality | More accurate and natural | Literal, less fluent |
| Approach | Deep learning models | Statistical patterns / grammar rules |

### Translation Modes
```
Real-time (Synchronous):
├── TranslateText API → Plain text input (up to 10,000 bytes)
└── TranslateDocument API → File input (.txt, .html, .docx) up to 100KB
    └── Preserves original document format

Batch (Asynchronous):
└── StartTextTranslationJob API
    ├── Input: Files in S3 (.txt, .html, .docx, .xlsx, .pptx, .xlf)
    ├── Output: Translated files in S3 (same format)
    ├── Supports multiple source → multiple target languages
    └── Up to 1000 queued jobs
```

### Language Support
- **75 languages** supported with **5,550+ language pair combinations**
- Supports direct translation between any two supported languages (not just English-to-X)
- Uses ISO 639-1 two-digit language codes (e.g., `en`, `fr`, `de`)
- Country variants use RFC 5646 format (e.g., `es-MX` for Mexican Spanish, `fr-CA` for Canadian French)

### Automatic Language Detection
- Set source language to `auto` to auto-detect
- **Uses Amazon Comprehend** behind the scenes (you agree to Comprehend service terms)
- **Additional charges** from Comprehend apply for auto-detection
- Returns `DetectedLanguageLowConfidenceException` if confidence is low
- Supported for both real-time and batch translations

### Integration Points
- **S3**: Input/output for batch translation jobs
- **Amazon Comprehend**: Auto language detection; also use Translate to feed non-English text into Comprehend
- **Amazon Transcribe**: Translate speech-to-text output for multilingual subtitles/captions
- **Amazon Polly**: Speak translated content (text-to-speech)
- **Lambda / AWS Glue**: Serverless integration for translation workflows
- **DynamoDB / Aurora / Redshift**: Translate text stored in databases
- **EventBridge**: Notifications for translation job status changes and parallel data updates
- **IAM**: Access control (TranslateFullAccess managed policy, or custom policies)
- **KMS**: Encrypt custom terminology with customer managed keys
- **PrivateLink**: VPC endpoint support for private connectivity

## 4. Key Features & Components

### Customization Options (6 Settings)

#### 1. Custom Terminology
- **Purpose**: Define how specific terms should be translated (e.g., brand names, product names)
- **How It Works**: Upload a terminology file → Translate uses exact match from file when found in source text
- **File Formats**: CSV, TSV, or TMX (Translation Memory eXchange)
- **Directionality**:
  - **Uni-directional**: One source language → one or more target languages
  - **Multi-directional**: Any language in file can be source or target
- **Matching**: Case-sensitive exact match on source text
- **Encryption**: Supports KMS customer managed keys for terminology encryption
- **Note**: Translate does NOT guarantee use of target term in every case — it considers context
- **Example**: "Amazon Family" → always translate to "Amazon Famille" in French (not "Famille Amazon")

#### 2. Active Custom Translation (Parallel Data)
- **Purpose**: Customize batch translation output to match your style, tone, and word choices
- **How It Works**: Provide parallel data (source + desired translation examples) → Translate adapts output at runtime
- **Input Formats**: TMX, CSV, or TSV files (max 5 MB)
- **Key Benefits**:
  - No custom model training required (unlike traditional custom MT)
  - Update parallel data anytime at no charge
  - Still produces good results for text outside your parallel data domain
- **Pricing**: Higher rate than standard batch jobs
- **Region Availability**: US East (N. Virginia), US West (Oregon), Europe (Ireland) only
- **Use Cases**: Domain-specific translations (legal, finance, life sciences)

#### 3. Brevity
- **Purpose**: Reduce translation output length (useful for subtitles, captions, headlines, form fields)
- **Availability**: Real-time text translations ONLY (not document translation, not batch)
- **Supported Target Languages**: French, German, Italian, Portuguese (Brazil), Spanish (+ any language → English)
- **Behavior**: If unsupported language pair, translation proceeds WITHOUT brevity (no error)

#### 4. Formality
- **Purpose**: Control formal vs. informal language (honorifics, pronouns, verb agreement)
- **Values**: `FORMAL` or `INFORMAL`
- **Availability**: Real-time AND batch translations
- **Supported Target Languages**: Dutch, French, French (Canada), German, Hindi, Italian, Japanese, Korean, Portuguese (Portugal), Spanish, Spanish (Mexico)
- **Example**: "Are you sure?" → German formal: "Sind Sie sicher?" vs. informal: "Bist du sicher?"
- **Behavior**: If unsupported target language, formality setting is ignored (no error)

#### 5. Profanity Masking
- **Purpose**: Replace profane words/phrases with a grawlix string (`?$#@$`) in translation output
- **Availability**: Real-time and batch translations
- **Supported**: Select languages (check docs for current list)

#### 6. Do-Not-Translate Tags
- **Purpose**: Exclude specific content from translation in HTML input
- **How**: Wrap content in `<span translate="no">` tags
- **Use Cases**: Code snippets, proper nouns, technical terms within HTML content

### Supported Input Formats

| Mode | Formats | Size Limit |
|------|---------|------------|
| Real-time text | Plain text (UTF-8) | 10,000 bytes |
| Real-time document | .txt, .html, .docx | 100 KB |
| Batch | .txt, .html, .docx, .xlsx, .pptx, .xlf (XLIFF 1.2) | S3-based |

### Real-time Document Translation
- Translates entire documents while preserving original formatting
- Supports: English ↔ any supported language
- File types: Plain text, HTML, Word (.docx)
- API: TranslateDocument

## 5. Exam Focus Areas

### Common Question Types

#### Scenario-Based Selection
**Question Pattern**: "A company needs to translate [content type] in [scenario]. Which service/feature should they use?"

**Decision Framework**:
```
Translation Need?
│
├─ Text translation → Amazon Translate
├─ Text analysis (sentiment, entities) → Amazon Comprehend
├─ Speech-to-text → Amazon Transcribe
├─ Text-to-speech → Amazon Polly
├─ Conversational interface → Amazon Lex
└─ Text generation → Amazon Bedrock
```

**Customization Decision Tree**:
```
Need to customize translation?
│
├─ Keep brand names consistent → Custom Terminology
├─ Match company style/tone → Active Custom Translation (Parallel Data)
├─ Shorter output for subtitles → Brevity
├─ Formal/informal tone → Formality
├─ Hide profanity → Profanity Masking
└─ Skip certain HTML content → Do-Not-Translate Tags
```

#### Feature Comparison Questions
| Need | Feature | Mode | Training Required |
|------|---------|------|-------------------|
| Translate text on demand | TranslateText API | Real-time | No |
| Translate documents preserving format | TranslateDocument API | Real-time | No |
| Translate large document sets | Batch translation job | Async | No |
| Consistent brand name translation | Custom Terminology | Both | No (upload file) |
| Domain-specific style adaptation | Active Custom Translation | Batch only | No (provide parallel data) |
| Shorter translations | Brevity | Real-time text only | No |
| Formal/informal tone | Formality | Both | No |

### Gotchas
1. **Auto-detect uses Comprehend**: Setting source to `auto` calls Comprehend behind the scenes — incurs additional Comprehend charges
2. **Brevity is real-time text ONLY**: Not available for document translation or batch jobs
3. **Active Custom Translation (ACT) is batch ONLY**: Cannot use parallel data with real-time translations
4. **ACT region-limited**: Only available in us-east-1, us-west-2, eu-west-1
5. **ACT costs more**: Higher pricing than standard batch jobs
6. **Custom Terminology is case-sensitive**: "Amazon" ≠ "amazon" — exact match required
7. **Custom Terminology not guaranteed**: Translate may skip the custom term if context doesn't fit
8. **Formality ignored silently**: If target language doesn't support formality, no error — just ignored
9. **Brevity ignored silently**: Same behavior — unsupported language pair proceeds without brevity
10. **Real-time document translation**: Only English ↔ other languages (not between two non-English languages)
11. **No same-language charges**: Translating from a language to itself incurs no charge (but auto-detect may still charge)
12. **Parallel data formats**: TMX, CSV, TSV only — max 5 MB per file

### Comparison Points

#### Amazon Translate vs. Amazon Comprehend
```
Amazon Translate
├─ Purpose: Convert text from one language to another
├─ Output: Translated text
├─ ML Type: Neural machine translation
└─ Use: Localization, multilingual apps

Amazon Comprehend
├─ Purpose: Analyze and extract insights from text
├─ Output: Entities, sentiment, key phrases, PII
├─ ML Type: NLP analysis
└─ Use: Text understanding, compliance
```

#### Custom Terminology vs. Active Custom Translation (Parallel Data)
```
Custom Terminology
├─ Scope: Individual terms/phrases
├─ Mode: Real-time AND batch
├─ Input: Term list (CSV/TSV/TMX)
├─ Cost: No extra charge
├─ Regions: All Translate regions
└─ Example: Brand names, product names

Active Custom Translation (Parallel Data)
├─ Scope: Full sentence style/tone adaptation
├─ Mode: Batch ONLY
├─ Input: Parallel sentence pairs (CSV/TSV/TMX)
├─ Cost: Higher rate than standard batch
├─ Regions: 3 regions only (us-east-1, us-west-2, eu-west-1)
└─ Example: Domain-specific legal/medical/financial translations
```

#### Translate in a Multi-Service Pipeline
```
Common Patterns:
1. Transcribe → Translate → Polly (speech-to-speech translation)
2. Translate → Comprehend (analyze non-English text in English)
3. S3 → Translate Batch → S3 (document repository translation)
4. Lambda → Translate → DynamoDB (real-time chat translation)
5. Transcribe → Translate → Subtitles (multilingual captioning)
```

### Decision Trees

#### Real-time vs. Batch Translation
```
✅ Use Real-time (TranslateText / TranslateDocument)
- Interactive applications (chat, customer service)
- Single document translation
- Low-latency requirements
- Up to 10,000 bytes text / 100 KB document

✅ Use Batch (StartTextTranslationJob)
- Large document repositories
- Multiple files in S3
- Multiple target languages at once
- Office formats (.xlsx, .pptx, .xlf)
- When using Active Custom Translation (parallel data)

❌ Don't use Translate for
- Text analysis → Comprehend
- Speech conversion → Transcribe / Polly
- Content generation → Bedrock
```

## 6. Best Practices

### Security
- **IAM**: Use least privilege; TranslateFullAccess for console, specific actions for API-only users
- **KMS**: Encrypt custom terminology with customer managed keys
- **PrivateLink**: Use VPC endpoints for private connectivity
- **SSL/TLS**: All communication encrypted in transit
- **Data at rest**: Content encrypted and stored in the AWS Region where you use the service
- **Compliance**: HIPAA-eligible, FedRAMP, SOC compliant
- **Data privacy**: Text inputs are not stored by AWS (check FAQ for opt-out of model improvement)

### Cost Optimization
- **Pricing**: Pay-per-character (total characters sent to API)
- **Free Tier**: 2 million characters/month for 12 months (new customers)
- **Avoid auto-detect charges**: Specify source language when known (avoids Comprehend charges)
- **Batch for bulk**: Use batch jobs for large volumes (more efficient)
- **ACT costs more**: Only use parallel data when domain customization is truly needed
- **Same-language no charge**: No charge for same source/target language
- **Preprocessing**: Remove unnecessary content before translation to reduce character count

### Performance
- **Real-time limits**: 10,000 bytes per TranslateText request
- **Batch throughput**: Up to 1,000 queued jobs
- **Regional deployment**: Deploy in region closest to users for lowest latency
- **Caching**: Cache frequently translated content to reduce API calls

### Operations
- **EventBridge**: Set up notifications for batch job completion and parallel data updates
- **CloudWatch**: Monitor API calls, errors, and latency
- **Error handling**:
  - `UnsupportedLanguagePairException` — unsupported source or target language
  - `DetectedLanguageLowConfidenceException` — auto-detect low confidence (can still use the detected language)
- **Parallel data updates**: Update parallel data as often as needed at no charge

## 7. Hands-On Labs

### Quick Setup: Real-time Translation
```python
import boto3

translate = boto3.client('translate', region_name='us-east-1')

# Translate text
response = translate.translate_text(
    Text="Hello, how are you today?",
    SourceLanguageCode='en',
    TargetLanguageCode='fr'
)

print(f"Translated: {response['TranslatedText']}")
print(f"Source Language: {response['SourceLanguageCode']}")
```

### Practical Exercise 1: Auto Language Detection
```python
# Auto-detect source language (uses Comprehend behind the scenes)
response = translate.translate_text(
    Text="Bonjour, comment allez-vous?",
    SourceLanguageCode='auto',
    TargetLanguageCode='en'
)

print(f"Detected: {response['SourceLanguageCode']}")
print(f"Translation: {response['TranslatedText']}")
```

### Practical Exercise 2: Translation with Brevity and Formality
```python
# Translate with brevity and formality settings
response = translate.translate_text(
    Text="Could you please confirm your attendance at the meeting?",
    SourceLanguageCode='en',
    TargetLanguageCode='de',
    Settings={
        'Formality': 'INFORMAL',
        'Brevity': 'ON'
    }
)

print(f"Translated: {response['TranslatedText']}")
```

### Practical Exercise 3: Batch Translation Job
```python
response = translate.start_text_translation_job(
    JobName='my-batch-job',
    InputDataConfig={
        'S3Uri': 's3://my-bucket/input/',
        'ContentType': 'text/plain'
    },
    OutputDataConfig={
        'S3Uri': 's3://my-bucket/output/'
    },
    DataAccessRoleArn='arn:aws:iam::123456789012:role/TranslateRole',
    SourceLanguageCode='en',
    TargetLanguageCodes=['fr', 'de', 'es']
)

print(f"Job ID: {response['JobId']}")
```

### CLI Commands
```bash
# Real-time text translation
aws translate translate-text \
  --text "Hello, how are you?" \
  --source-language-code en \
  --target-language-code fr

# Translation with brevity
aws translate translate-text \
  --text "Please confirm your attendance." \
  --source-language-code en \
  --target-language-code de \
  --settings Brevity=ON

# Translation with formality
aws translate translate-text \
  --text "Are you sure?" \
  --source-language-code en \
  --target-language-code de \
  --settings Formality=FORMAL

# Start batch translation job
aws translate start-text-translation-job \
  --job-name my-batch-job \
  --input-data-config S3Uri=s3://my-bucket/input/,ContentType=text/plain \
  --output-data-config S3Uri=s3://my-bucket/output/ \
  --source-language-code en \
  --target-language-codes fr de es \
  --data-access-role-arn arn:aws:iam::123456789012:role/TranslateRole

# Import custom terminology
aws translate import-terminology \
  --name my-terminology \
  --merge-strategy OVERWRITE \
  --terminology-data file://my-terminology.csv,Format=CSV

# List custom terminologies
aws translate list-terminologies
```

## 8. Sample Questions

### Question 1: Feature Selection
**Scenario**: A media company produces subtitles for streaming content. They need to translate English subtitles into French, German, and Spanish. The translated subtitles must be shorter than the original to fit on screen. Which Amazon Translate feature should they use?

A) Active Custom Translation with parallel data
B) Custom Terminology
C) Brevity setting with real-time text translation
D) Formality setting

**Correct Answer**: C) Brevity setting with real-time text translation

**Explanation**:
- ✅ **C is correct**: Brevity reduces translation output length, specifically designed for scenarios like subtitles, captions, and headlines. French, German, and Spanish are all supported target languages for brevity. Works with real-time text translation.
- ❌ **A is wrong**: Active Custom Translation adapts style/tone using parallel data but doesn't specifically reduce output length. Also only available for batch jobs, not real-time.
- ❌ **B is wrong**: Custom Terminology controls how specific terms are translated, not output length.
- ❌ **D is wrong**: Formality controls formal/informal tone, not translation length.

### Question 2: Customization Approach
**Scenario**: A legal firm needs to translate contracts from English to French. The translations must use specific legal terminology and match the formal tone used in their existing translated contracts. They have a collection of previously translated contracts. Which combination of features should they use?

A) Custom Terminology only
B) Active Custom Translation (Parallel Data) with batch translation
C) Custom Terminology + Formality setting with real-time translation
D) Active Custom Translation (Parallel Data) + Custom Terminology with batch translation

**Correct Answer**: D) Active Custom Translation (Parallel Data) + Custom Terminology with batch translation

**Explanation**:
- ✅ **D is correct**: Custom Terminology ensures specific legal terms are translated consistently. Active Custom Translation uses their existing translated contracts as parallel data to match the firm's style and tone. Both can be combined in a batch translation job.
- ❌ **A is wrong**: Custom Terminology alone handles specific terms but doesn't adapt overall style/tone to match existing translations.
- ❌ **B is wrong**: Parallel data adapts style but may not handle specific legal terms as precisely as Custom Terminology.
- ❌ **C is wrong**: Formality only controls formal/informal pronouns — it doesn't adapt to the firm's specific legal writing style. Also, Active Custom Translation is only available for batch, not real-time.

### Question 3: Service Integration
**Scenario**: A company receives customer support emails in multiple languages. They want to automatically detect the language, translate emails to English, and then analyze the sentiment. Which combination of AWS services achieves this with the LEAST operational overhead?

A) Amazon Translate (auto-detect) → Amazon Comprehend (sentiment analysis)
B) Amazon Comprehend (language detection) → Amazon Translate → Amazon Comprehend (sentiment analysis)
C) Amazon Lex (language detection) → Amazon Translate → Amazon Bedrock (sentiment analysis)
D) Amazon Translate (auto-detect) → Amazon Bedrock (sentiment analysis)

**Correct Answer**: A) Amazon Translate (auto-detect) → Amazon Comprehend (sentiment analysis)

**Explanation**:
- ✅ **A is correct**: Amazon Translate's auto-detect feature automatically identifies the source language (using Comprehend behind the scenes) and translates to English in a single API call. Then Comprehend performs sentiment analysis on the English text. This is the simplest two-step pipeline with least operational overhead.
- ❌ **B is wrong**: Adds an unnecessary explicit language detection step. Translate's auto-detect already handles this, making option A simpler.
- ❌ **C is wrong**: Amazon Lex is for conversational interfaces, not language detection. Bedrock is for text generation, not purpose-built sentiment analysis.
- ❌ **D is wrong**: While Translate auto-detect works, Bedrock is not the purpose-built service for sentiment analysis — Comprehend is more cost-effective and purpose-built for this task.

---

**Exam Tips**:
- Translate = neural machine translation (deep learning, full sentence context) — NOT statistical/rule-based
- 75 languages, 5,550+ language pairs — supports direct translation between any two (not just English-centric)
- Auto-detect uses Comprehend behind the scenes → additional Comprehend charges
- Custom Terminology = specific terms (brand names); Parallel Data (ACT) = style/tone adaptation — know the difference
- Brevity = real-time text ONLY; ACT = batch ONLY — common exam distractor
- Formality = formal/informal pronouns and verb agreement (not overall writing style)
- Common pipeline: Transcribe → Translate → Polly (speech-to-speech) or Translate → Comprehend (analyze non-English text)
- Supported file formats differ: Real-time (.txt, .html, .docx) vs. Batch (adds .xlsx, .pptx, .xlf)
- No contracts, no minimums — pay per character translated
