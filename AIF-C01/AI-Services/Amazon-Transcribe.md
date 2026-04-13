# Amazon Transcribe

## 1. Service/Topic Overview
- **Purpose**: Automatic speech recognition (ASR) service that converts audio to text using machine learning models
- **Service Level**: Regional
- **Key Use Cases**:
  - Live closed captioning and subtitling for video/audio content
  - Contact center call transcription and analytics
  - Medical dictation and clinical documentation
  - Meeting transcription with speaker identification
  - Search and analysis of audio/video archives
- **Exam Weight**: Medium
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — pre-built AI service
  - Domain 3: Applications of Foundation Models (28%) — speech-to-text in AI pipelines
  - Domain 5: Security, Compliance, and Governance (14%) — PII redaction, HIPAA
- **Task Statements**: Identify AWS AI services for speech processing; understand pre-built AI service capabilities; apply data privacy and compliance controls

## 2. Exam Keyword Mapping
- **Trigger Words**: "speech-to-text", "audio transcription", "convert speech", "ASR", "automatic speech recognition", "transcribe audio", "subtitles", "closed captioning", "speaker diarization", "call analytics"
- **Scenario Indicators**:
  - "Need to convert recorded calls to text" → Transcribe
  - "Real-time captioning for live events" → Transcribe Streaming
  - "Analyze contact center calls for sentiment" → Transcribe Call Analytics
  - "Transcribe medical dictation" → Transcribe Medical
  - "Remove PII from audio transcripts" → Transcribe PII Redaction
  - "Identify speakers in a conversation" → Speaker Diarization
  - "Detect toxic content in audio" → Transcribe Toxicity Detection
- **Anti-patterns**:
  - Text-to-speech → **Amazon Polly** (opposite direction)
  - Text analysis/NLP on existing text → **Amazon Comprehend**
  - Building a chatbot → **Amazon Lex** (though Lex uses Transcribe under the hood)
  - Translation of text → **Amazon Translate**
  - Custom-trained speech model from scratch → **Amazon SageMaker**

## 3. Core Concepts

### Architectural Patterns
```
Audio Input (S3 / Stream) → Amazon Transcribe → JSON Transcript Output
                                    ↓
                          Optional: PII Redaction, Speaker Labels,
                          Custom Vocabulary, Language ID
                                    ↓
                          Downstream: Comprehend (NLP), Translate,
                          S3 Storage, Analytics
```

### Three Transcribe Solutions

| Solution | Use Case | Key Differentiator |
|----------|----------|-------------------|
| **Amazon Transcribe** | General speech-to-text | Broadest language support, custom models |
| **Transcribe Medical** | Healthcare/clinical | HIPAA eligible, PHI identification, medical specialties |
| **Transcribe Call Analytics** | Contact centers | Sentiment, summarization, issue detection, categorization |

### Transcription Modes

| Mode | Description | Max Duration | Use Case |
|------|-------------|-------------|----------|
| **Batch** | Transcribe files stored in S3 | Up to 4 hours | Recorded audio/video processing |
| **Streaming** | Real-time transcription via HTTP/2 or WebSocket | Up to 4 hours | Live captioning, real-time monitoring |

### Service Limits
- Max audio file length: **4 hours** (batch)
- Max streaming session: **4 hours**
- Supported audio formats: FLAC, MP3, MP4, Ogg, WebM, AMR, WAV (batch); FLAC, OPUS in Ogg, PCM (streaming)
- Speaker diarization: up to **10 speakers**
- Custom vocabularies: up to **100 per account**, max **50 KB** each
- Minimum billing: **15 seconds** per request
- Sampling rate: **8 kHz to 48 kHz** (streaming)

### Data Flow
```
Input: Audio file (S3) or audio stream (HTTP/2, WebSocket)
  ↓
Processing: ASR engine → acoustic feature extraction → candidate generation → language modeling → ranking
  ↓
Output: JSON transcript with timestamps, confidence scores, speaker labels, alternatives
```

### Integration Points
- **Amazon S3**: Input audio storage and output transcript storage (batch)
- **Amazon Comprehend**: Downstream NLP analysis (sentiment, entities, key phrases)
- **Amazon Translate**: Translate transcripts to other languages
- **Amazon Lex**: Speech input for conversational bots
- **AWS Lambda**: Trigger transcription jobs, process results
- **Amazon CloudWatch**: Monitoring and logging
- **AWS KMS**: Encryption of data at rest
- **Amazon EventBridge**: Event-driven transcription workflows

## 4. Key Features & Components

### Exam-Tested Features

| Feature | Batch | Streaming | Description |
|---------|-------|-----------|-------------|
| **Custom Vocabulary** | ✅ | ✅ | Add domain-specific terms, proper nouns, acronyms |
| **Custom Language Models (CLM)** | ✅ | ✅ | Train models with your own text corpus for domain accuracy |
| **Speaker Diarization** | ✅ | ✅ | Identify and label different speakers (up to 10) |
| **Channel Identification** | ✅ | ✅ | Separate transcription per audio channel |
| **Automatic Language ID** | ✅ | ✅ | Detect dominant language without specifying |
| **Multi-Language ID** | ✅ | ✅ | Identify and transcribe multiple languages in one audio |
| **PII Redaction** | ✅ | ✅ | Identify and redact personally identifiable information |
| **Vocabulary Filtering** | ✅ | ✅ | Remove profane or unwanted words |
| **Subtitles** | ✅ | ❌ | Generate WebVTT or SubRip subtitle files |
| **Alternative Transcriptions** | ✅ | ✅ | Up to 10 alternative transcripts with confidence scores |
| **Toxicity Detection** | ✅ | ❌ | Flag toxic audio content into categories |
| **Punctuation & Normalization** | ✅ | ✅ | Auto-add punctuation, format numbers |
| **Timestamps** | ✅ | ✅ | Word-level and segment-level timestamps |

### Transcribe Call Analytics Features

| Feature | Post-Call | Real-Time | Description |
|---------|-----------|-----------|-------------|
| **Call Summarization** | ✅ | ❌ | Generative AI-powered call summaries (issues, outcomes, action items) |
| **Speaker Sentiment** | ✅ | ✅ | Positive/negative/neutral/mixed sentiment per turn |
| **Issue Detection** | ❌ | ✅ | Automatically detect customer issues in real-time |
| **Custom Categorization** | ✅ | ✅ | Label calls based on custom criteria (words, phrases, characteristics) |
| **Call Characteristics** | ✅ | ❌ | Non-talk time, interruptions, talk speed, loudness |
| **Audio Redaction** | ✅ | ✅ | Redact PII from the audio file itself |

### Transcribe Medical Features
- **Dictation Mode**: Single-speaker medical dictation
- **Conversational Mode**: Multi-speaker clinician-patient conversations
- **Medical Specialties**: Cardiology, oncology, neurology, primary care, radiology, urology
- **PHI Identification**: Automatically identify Protected Health Information
- **HIPAA Eligible**: Covered under AWS BAA
- **Language**: US English only
- **Custom Vocabulary**: Medical-specific terminology support

### Configuration Options
- **Domain-specific models**: Telephone vs. multimedia optimized
- **Output encryption**: SSE-S3 or AWS KMS customer-managed keys
- **Job queueing**: Automatic queue management for batch jobs
- **Tagging**: Resource tagging for cost allocation and organization

## 5. Exam Focus Areas

### Common Question Types
- **Scenario-based**: "A company needs to transcribe customer service calls and detect sentiment..." → Transcribe Call Analytics
- **Feature selection**: "Which feature removes profanity from transcripts?" → Vocabulary Filtering
- **Service comparison**: "Speech-to-text vs. text-to-speech" → Transcribe vs. Polly
- **Compliance**: "HIPAA-compliant medical transcription" → Transcribe Medical
- **Privacy**: "Remove SSN and credit card numbers from transcripts" → PII Redaction

### Gotchas
- **Transcribe ≠ Polly**: Transcribe is speech→text; Polly is text→speech (opposite directions)
- **Transcribe Medical is US English only** — don't select it for other languages
- **PII Redaction vs. Vocabulary Filtering**: PII Redaction removes sensitive data (SSN, credit cards); Vocabulary Filtering removes unwanted words (profanity)
- **Audio Redaction is Call Analytics only** — standard Transcribe can redact transcripts but NOT the audio itself
- **Custom Language Models ≠ Custom Vocabulary**: CLM trains on your text corpus for domain context; Custom Vocabulary adds specific words/phrases
- **Call Summarization uses generative AI** — it's a post-call only feature
- **Streaming supports fewer features** than batch (e.g., no subtitles, no toxicity detection)
- **Minimum billing is 15 seconds** per request regardless of actual audio length

### Comparison Points

| Feature | Transcribe | Transcribe Medical | Transcribe Call Analytics |
|---------|-----------|-------------------|--------------------------|
| **Primary Use** | General ASR | Healthcare | Contact centers |
| **Language Support** | 100+ languages | US English only | Multiple languages |
| **Sentiment Analysis** | ❌ | ❌ | ✅ |
| **Call Summarization** | ❌ | ❌ | ✅ (generative AI) |
| **PHI Identification** | ❌ | ✅ | ❌ |
| **PII Redaction** | ✅ | ❌ | ✅ |
| **Audio Redaction** | ❌ | ❌ | ✅ |
| **Custom Language Models** | ✅ | ❌ | ✅ |
| **HIPAA Eligible** | ✅ | ✅ | ✅ |

### Decision Trees

**✅ Use Amazon Transcribe when:**
- You need general-purpose speech-to-text
- You need subtitle generation (WebVTT/SubRip)
- You need multi-language transcription
- You need custom language models for domain accuracy
- You need toxicity detection in audio

**✅ Use Transcribe Call Analytics when:**
- You need contact center call analysis
- You need speaker sentiment detection
- You need call summarization (generative AI)
- You need to categorize calls automatically
- You need to redact PII from audio files (not just transcripts)

**✅ Use Transcribe Medical when:**
- You need medical dictation transcription
- You need PHI identification
- You need medical specialty-specific accuracy
- Content is in US English

**❌ Do NOT use Transcribe when:**
- You need text-to-speech → **Amazon Polly**
- You need to analyze existing text → **Amazon Comprehend**
- You need to build a conversational bot → **Amazon Lex**
- You need to translate text → **Amazon Translate**
- You need to train a custom ASR model from scratch → **Amazon SageMaker**

## 6. Best Practices

### Security
- Enable **encryption at rest** using AWS KMS customer-managed keys for sensitive transcripts
- Use **PII redaction** to automatically remove sensitive data from transcripts
- Use **vocabulary filtering** to remove inappropriate content
- Enable **audio redaction** (Call Analytics) to remove PII from audio files
- Use **VPC endpoints** for private connectivity
- Apply **least-privilege IAM policies** for Transcribe API access
- Transcribe is **HIPAA eligible** — use with BAA for healthcare workloads
- Data is encrypted **in transit** using TLS 1.2

### Cost Optimization
- **Pay-per-use**: Billed per second of transcribed audio (monthly)
- **Minimum charge**: 15 seconds per request — batch small files together when possible
- **Free Tier**: 60 minutes/month for the first 12 months
- **Two-channel audio**: Charged for total duration only (not per channel)
- **Volume discounts**: Available for large workloads (contact AWS)
- Use **batch transcription** for non-time-sensitive workloads (same pricing but more efficient)
- **Additional charges** apply for: PII redaction, custom language models, toxicity detection, Call Analytics features
- Use **job queueing** to manage batch processing efficiently

### Performance
- Use **PCM-encoded audio** for best streaming results
- Record at **16,000 Hz** sampling rate for optimal quality-to-data ratio
- Use **lossless audio formats** (FLAC, WAV) for best accuracy
- Create **custom vocabularies** for domain-specific terms to improve accuracy
- Build **custom language models** when you have domain-specific text data
- Specify correct **number of audio channels** for multi-channel audio
- Send **uniform chunk sizes** for streaming transcription
- Encode and send **silence** when no speech is present in streams

### Operations
- Monitor transcription jobs via **Amazon CloudWatch**
- Use **tagging** for cost allocation and resource organization
- Implement **event-driven architectures** with EventBridge for automated workflows
- Use **alternative transcriptions** for human-in-the-loop review workflows
- Test with representative audio samples before production deployment
- Periodically evaluate transcription accuracy and adjust custom vocabularies

## 7. Hands-On Labs

### Quick Setup
1. Navigate to Amazon Transcribe console
2. Upload audio file to S3 bucket
3. Create transcription job → specify language, S3 input URI
4. Review transcript output in console or download JSON

### Practical Exercises

**Exercise 1: Batch Transcription**
1. Upload an audio file to S3
2. Create a transcription job with speaker diarization enabled
3. Review the output with speaker labels

**Exercise 2: Custom Vocabulary**
1. Create a custom vocabulary with domain-specific terms
2. Run a transcription job using the custom vocabulary
3. Compare accuracy with and without custom vocabulary

**Exercise 3: Streaming Transcription**
1. Use the Transcribe console's real-time transcription feature
2. Speak into your microphone
3. Observe real-time transcript output with partial results

### CLI Commands

```bash
# Start a batch transcription job
aws transcribe start-transcription-job \
    --transcription-job-name my-job \
    --language-code en-US \
    --media MediaFileUri=s3://my-bucket/my-audio.mp3 \
    --output-bucket-name my-output-bucket

# Start a transcription job with speaker diarization
aws transcribe start-transcription-job \
    --transcription-job-name my-diarization-job \
    --language-code en-US \
    --media MediaFileUri=s3://my-bucket/my-audio.mp3 \
    --settings ShowSpeakerLabels=true,MaxSpeakerLabels=3

# Create a custom vocabulary
aws transcribe create-vocabulary \
    --vocabulary-name my-vocab \
    --language-code en-US \
    --vocabulary-file-uri s3://my-bucket/my-vocabulary.txt

# Start a transcription job with PII redaction
aws transcribe start-transcription-job \
    --transcription-job-name my-redacted-job \
    --language-code en-US \
    --media MediaFileUri=s3://my-bucket/my-audio.mp3 \
    --content-redaction RedactionType=PII,RedactionOutput=redacted

# Check job status
aws transcribe get-transcription-job \
    --transcription-job-name my-job

# List transcription jobs
aws transcribe list-transcription-jobs --status COMPLETED
```

### Console Walkthrough
1. **Amazon Transcribe Console** → Transcription jobs → Create job
2. Specify job name, language, S3 input location
3. Configure output (S3 bucket or service-managed)
4. Enable optional features: speaker diarization, PII redaction, custom vocabulary
5. Create job → monitor status → review results

## 8. Sample Questions

### Question 1
**A media company needs to automatically generate subtitles for their video content library stored in Amazon S3. The videos are in multiple languages. Which combination of Amazon Transcribe features should they use?**

A. Streaming transcription with custom vocabulary
B. Batch transcription with automatic language identification and subtitle generation
C. Transcribe Call Analytics with call summarization
D. Transcribe Medical with conversational mode

**Correct Answer: B**

**Explanation**: Batch transcription is appropriate for processing pre-recorded files in S3. Automatic language identification detects the language without manual specification, and the subtitle feature generates WebVTT/SubRip files directly. Option A uses streaming (for real-time, not stored files). Option C is for contact centers. Option D is for medical use cases only.

---

### Question 2
**A healthcare organization needs to transcribe physician-patient conversations and automatically identify any Protected Health Information (PHI) in the transcripts. The conversations are in US English. Which service should they use?**

A. Amazon Transcribe with PII redaction
B. Amazon Transcribe Medical with PHI identification
C. Amazon Transcribe Call Analytics with sentiment analysis
D. Amazon Comprehend Medical with entity detection

**Correct Answer: B**

**Explanation**: Amazon Transcribe Medical is specifically designed for healthcare use cases and includes PHI identification at no additional charge. It supports conversational mode for multi-speaker clinician-patient interactions. Option A identifies PII but not PHI specifically. Option C is for contact centers. Option D analyzes existing text, not audio.

---

### Question 3
**A contact center wants to automatically generate summaries of customer calls, detect customer sentiment in real-time, and redact credit card numbers from both the transcript and the audio recording. Which Amazon Transcribe solution provides ALL of these capabilities?**

A. Amazon Transcribe with custom language models and PII redaction
B. Amazon Transcribe Medical with PHI identification
C. Amazon Transcribe Call Analytics with generative call summarization
D. Amazon Transcribe with vocabulary filtering and streaming transcription

**Correct Answer: C**

**Explanation**: Only Transcribe Call Analytics provides all three capabilities: generative call summarization (post-call), real-time sentiment analysis, and audio redaction (removing PII from the audio file itself). Standard Transcribe (A, D) can redact transcripts but cannot redact audio or detect sentiment. Transcribe Medical (B) is for healthcare, not contact centers.

---

**Exam Tip**: Remember the three Transcribe solutions and their unique capabilities. Standard Transcribe = general ASR + subtitles + toxicity detection. Call Analytics = sentiment + summarization + audio redaction. Medical = PHI + medical specialties (US English only). The exam often tests which solution fits a specific scenario.
