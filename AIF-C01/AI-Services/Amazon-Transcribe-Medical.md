# Amazon Transcribe Medical

## 1. Service/Topic Overview
- **Purpose**: HIPAA-eligible automatic speech recognition (ASR) service that converts medical speech to text, accurately transcribing medical terminologies for clinical documentation and healthcare applications
- **Service Level**: Regional
- **Key Use Cases**:
  - Conversational voice scribes (physician-patient conversation capture)
  - Medical dictation applications (physician-dictated notes)
  - Drug safety monitoring / pharmacovigilance (transcribing adverse event calls)
  - Telehealth consultation subtitling
  - Clinical documentation workflow automation
- **Exam Weight**: Medium
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — pre-built AI service for healthcare
  - Domain 3: Applications of Foundation Models (28%) — speech-to-text in medical AI pipelines
  - Domain 5: Security, Compliance, and Governance (14%) — HIPAA eligibility, PHI handling, shared responsibility
- **Task Statements**: Identify AWS AI services for healthcare speech processing; understand HIPAA-eligible AI services; apply data privacy controls for protected health information (PHI)

## 2. Exam Keyword Mapping
- **Trigger Words**: "medical transcription", "clinical documentation", "physician dictation", "medical speech-to-text", "HIPAA transcription", "PHI identification", "medical specialties", "clinician-patient conversation", "pharmacovigilance", "drug safety monitoring", "EHR integration"
- **Scenario Indicators**:
  - "Transcribe physician-patient conversations" → Transcribe Medical (Conversation mode)
  - "Physician dictating clinical notes" → Transcribe Medical (Dictation mode)
  - "HIPAA-compliant speech-to-text for healthcare" → Transcribe Medical
  - "Identify protected health information in audio" → Transcribe Medical
  - "Transcribe drug safety monitoring calls" → Transcribe Medical
  - "Medical specialty-specific transcription (cardiology, oncology)" → Transcribe Medical
  - "Generate clinical notes from conversations with generative AI" → **AWS HealthScribe** (not Transcribe Medical)
- **Anti-patterns**:
  - General-purpose speech-to-text (non-medical) → **Amazon Transcribe** (standard)
  - Contact center call analytics with sentiment → **Transcribe Call Analytics**
  - Text-to-speech → **Amazon Polly**
  - Extract medical entities from existing text → **Amazon Comprehend Medical**
  - AI-generated clinical notes from conversations → **AWS HealthScribe**
  - Non-English medical transcription → Not supported (US English only)
  - Custom-trained ASR model → **Amazon SageMaker**

## 3. Core Concepts

### Architectural Patterns
```
Audio Input (S3 file / Real-time stream)
        ↓
Amazon Transcribe Medical
  - Specify: Language (en-US), Specialty, Audio Type (dictation/conversation)
  - Optional: Custom Vocabulary, Speaker Diarization
        ↓
JSON Transcript Output (with timestamps, confidence scores)
        ↓
Downstream Processing:
  - Amazon Comprehend Medical → Extract medical entities (medications, conditions, dosages)
  - Amazon S3 → Store transcripts
  - EHR Systems → Clinical documentation entry
```

### Two Audio Types

| Audio Type | Description | Use Case |
|-----------|-------------|----------|
| **Dictation** | Single speaker (physician) reporting on a patient visit or procedure | Post-visit clinical notes, radiology reports, procedure summaries |
| **Conversation** | Multi-speaker dialogue between physician and patient, or between physicians | Real-time visit capture, telehealth consultations, clinical interviews |

### Supported Medical Specialties

| Specialty | Sub-specialty | Batch | Streaming |
|-----------|--------------|-------|-----------|
| **Primary Care** | Family Medicine | ✅ | ✅ |
| **Primary Care** | Internal Medicine | ✅ | ✅ |
| **Primary Care** | Obstetrics & Gynecology (OB-GYN) | ✅ | ✅ |
| **Primary Care** | Pediatrics | ✅ | ✅ |
| **Cardiology** | — | ❌ | ✅ |
| **Neurology** | — | ❌ | ✅ |
| **Oncology** | — | ❌ | ✅ |
| **Radiology** | — | ❌ | ✅ |
| **Urology** | — | ❌ | ✅ |

> ⚠️ **Exam Gotcha**: Only **Primary Care** specialties support both batch and streaming. All other specialties (Cardiology, Neurology, Oncology, Radiology, Urology) are **streaming only**.

### Service Limits
- **Language**: US English (en-US) only
- **Max audio file length**: 4 hours (batch)
- **Max file size**: 2 GB (batch)
- **Supported batch formats**: FLAC, MP3, MP4, WAV
- **Supported streaming formats**: PCM signed 16-bit little-endian, FLAC, OPUS in Ogg
- **Recommended sample rate**: 16,000 Hz or higher
- **Recommended encoding**: PCM 16-bit (lossless)
- **Custom vocabularies**: Up to 100 per account, max 50 KB each
- **Batch specialty**: `PRIMARYCARE` only
- **Streaming specialties**: All supported specialties

### Data Flow
```
Input: Audio file (S3) or audio stream (HTTP/2, WebSocket)
  ↓
Processing: Medical ASR engine → medical terminology recognition → specialty-specific models
  ↓
Output: JSON transcript with:
  - Word-level timestamps
  - Confidence scores
  - Speaker labels (if diarization enabled)
  - Medical term abbreviations (e.g., "blood pressure" → "BP")
  - Alternative transcriptions (if enabled)
```

### Integration Points
- **Amazon S3**: Input audio storage + output transcript storage (batch)
- **Amazon Comprehend Medical**: Extract medical entities (medications, conditions, dosages, procedures) from transcripts
- **AWS HealthScribe**: Next-generation service combining Transcribe Medical + generative AI for clinical notes
- **AWS Lambda**: Trigger transcription jobs, process results
- **Amazon CloudWatch**: Monitoring and logging
- **AWS KMS**: Encryption of data at rest
- **EHR Systems**: Downstream clinical documentation entry

## 4. Key Features & Components

### Exam-Tested Features

| Feature | Batch | Streaming | Description |
|---------|-------|-----------|-------------|
| **Medical Custom Vocabulary** | ✅ | ✅ | Add domain-specific medical terms for improved accuracy |
| **Speaker Diarization** | ✅ | ✅ | Identify and label different speakers in conversations |
| **Multi-Channel Audio** | ✅ | ❌ | Separate transcription per audio channel |
| **Alternative Transcriptions** | ✅ | ✅ | Return multiple transcript candidates with confidence scores |
| **Medical Term Abbreviations** | ✅ | ✅ | Auto-abbreviate medical terms (e.g., "Fahrenheit" → "F", "milligrams" → "mg") |
| **Partial Results** | ❌ | ✅ | Progressive transcription during streaming (IsPartial flag) |
| **PHI Identification** | ✅ | ✅ | Identify Protected Health Information in transcripts |

### Medical Term Abbreviation Examples

| Spoken Term | Transcribed As |
|-------------|---------------|
| Blood pressure | BP |
| Beats per minute | BPM |
| Oxygen saturation | O2 |
| CT Scan | CT |
| Centigrade / Celsius | C |
| Fahrenheit | F |
| Milligrams | mg |
| Milliliters | mL |
| Kilograms | kg |
| Post-operation | POSTOP |
| In and out | I/O |

### Batch API Operations
- `StartMedicalTranscriptionJob` — Start a batch transcription job
- `GetMedicalTranscriptionJob` — Check job status and get results
- `ListMedicalTranscriptionJobs` — List all medical transcription jobs
- `DeleteMedicalTranscriptionJob` — Delete a completed job

### Streaming API Operations
- `StartMedicalStreamTranscription` — Start a real-time streaming session (HTTP/2 or WebSocket)

### Minimum Batch Request Parameters
```json
{
  "MedicalTranscriptionJobName": "my-medical-job",
  "LanguageCode": "en-US",
  "Media": {
    "MediaFileUri": "s3://my-bucket/medical-audio.flac"
  },
  "OutputBucketName": "my-output-bucket",
  "Specialty": "PRIMARYCARE",
  "Type": "CONVERSATION"
}
```

### Streaming Request Parameters
- `LanguageCode`: en-US
- `MediaEncoding`: pcm (recommended)
- `MediaSampleRateHertz`: 16000+
- `Specialty`: PRIMARYCARE, CARDIOLOGY, NEUROLOGY, ONCOLOGY, RADIOLOGY, UROLOGY
- `Type`: DICTATION or CONVERSATION
- Optional: `VocabularyName`, `SessionId`, `EnableChannelIdentification`, `NumberOfChannels`

### Key Security Properties
- **HIPAA Eligible**: Covered under AWS Business Associate Agreement (BAA)
- **Stateless Service**: Does NOT store inbound audio or output text — users control all data storage
- **Shared Responsibility Model**: AWS protects infrastructure; customer manages data
- **Encryption in Transit**: TLS 1.2
- **Encryption at Rest**: SSE-S3 or AWS KMS for S3-stored outputs

## 5. Exam Focus Areas

### Common Question Types
- **Scenario-based**: "A hospital needs to transcribe physician-patient conversations for clinical documentation..." → Transcribe Medical (Conversation mode)
- **Feature selection**: "Which service identifies PHI in medical audio?" → Transcribe Medical
- **Compliance**: "HIPAA-eligible speech-to-text for healthcare" → Transcribe Medical
- **Service comparison**: "Transcribe Medical vs. HealthScribe vs. Comprehend Medical"
- **Limitation awareness**: "Medical transcription in French" → Not supported (US English only)

### Gotchas
- **US English ONLY** — Transcribe Medical does not support any other language
- **Batch supports only PRIMARYCARE specialty** — all other specialties require streaming
- **Stateless service** — Transcribe Medical does NOT store audio or transcripts; you must manage storage
- **Not a substitute for medical professionals** — transcriptions must be reviewed by trained medical professionals before use in patient care
- **Custom vocabularies must NOT contain PHI/PII** — you are responsible for data integrity
- **No PII Redaction** — unlike standard Transcribe, Transcribe Medical does NOT have PII redaction (it has PHI identification instead)
- **No Custom Language Models** — only custom vocabularies are supported (not CLMs)
- **No Audio Redaction** — audio redaction is a Call Analytics feature only
- **HealthScribe ≠ Transcribe Medical** — HealthScribe adds generative AI for clinical note generation; Transcribe Medical only transcribes

### Comparison Points

| Feature | Transcribe Medical | AWS HealthScribe | Comprehend Medical |
|---------|-------------------|-----------------|-------------------|
| **Input** | Audio (speech) | Audio (speech) | Text |
| **Output** | Text transcript | Transcript + AI clinical notes | Medical entities/relationships |
| **Generative AI** | ❌ | ✅ (clinical note generation) | ❌ |
| **HIPAA Eligible** | ✅ | ✅ | ✅ |
| **Language** | US English | US English | English |
| **Specialties** | 6 (9 sub-specialties) | 22+ specialties | N/A |
| **Region Availability** | Multiple regions | us-east-1 only | Multiple regions |
| **Speaker Role ID** | Speaker diarization | Automatic role identification | N/A |
| **Use Case** | Medical speech-to-text | End-to-end clinical documentation | Text entity extraction |

### Decision Trees

**✅ Use Amazon Transcribe Medical when:**
- You need medical speech-to-text transcription
- You need PHI identification in audio transcripts
- You need medical specialty-specific accuracy
- You need HIPAA-eligible transcription
- Content is in US English
- You need to integrate with downstream NLP (Comprehend Medical)

**✅ Use AWS HealthScribe instead when:**
- You need AI-generated clinical notes (not just transcripts)
- You need automatic speaker role identification (clinician vs. patient)
- You need dialogue classification and medical entity extraction in one API
- You need broader specialty coverage (22+ specialties)

**✅ Use Standard Amazon Transcribe when:**
- Content is non-medical
- You need multi-language support
- You need PII redaction (not PHI)
- You need custom language models
- You need subtitle generation

**❌ Do NOT use Transcribe Medical when:**
- Audio is not in US English
- You need text analysis on existing text → **Comprehend Medical**
- You need text-to-speech → **Amazon Polly**
- You need AI-generated clinical notes → **AWS HealthScribe**
- You need contact center analytics → **Transcribe Call Analytics**

## 6. Best Practices

### Security
- Use **AWS KMS customer-managed keys** for encrypting output transcripts in S3
- Enable **VPC endpoints** for private connectivity to the service
- Apply **least-privilege IAM policies** — restrict access to specific S3 buckets and Transcribe Medical APIs
- Sign a **Business Associate Agreement (BAA)** with AWS for HIPAA compliance
- **Never include PHI/PII in custom vocabularies** — you are responsible for data integrity
- Use **TLS 1.2** for all API communications (enforced by default)
- Implement **CloudTrail logging** for audit trails of all API calls
- Remember the service is **stateless** — audio and transcripts are not stored by the service

### Cost Optimization
- **Pay-per-use**: Billed per second of transcribed audio
- **No upfront costs**: No fixed fees, commitments, or long-term licenses
- **Batch non-urgent work**: Use batch transcription for recorded audio (same pricing, more efficient)
- **Optimize audio quality**: Better audio = fewer retranscriptions needed
- **Small custom vocabularies**: Create targeted vocabularies per specialty rather than one large one
- **Free Tier**: 60 minutes/month for the first 12 months (shared with standard Transcribe)

### Performance
- Use **lossless audio formats** (FLAC or WAV) with PCM 16-bit encoding for best accuracy
- Record at **16,000 Hz or higher** sample rate
- **Specify the correct medical specialty** — this significantly impacts transcription accuracy
- **Choose the correct audio type** (dictation vs. conversation) — affects the ASR model used
- Create **small, targeted custom vocabularies** per recording type rather than one large vocabulary
- Use **high confidence thresholds** for patient care scenarios
- For streaming, send **uniform chunk sizes** and encode silence when no speech is present

### Operations
- **Always review transcriptions** with trained medical professionals before clinical use
- Monitor transcription jobs via **Amazon CloudWatch**
- Use **alternative transcriptions** for human-in-the-loop review workflows
- Pipe transcripts to **Amazon Comprehend Medical** for entity extraction (medications, conditions, dosages)
- Implement **event-driven workflows** with Lambda for automated processing pipelines
- Periodically evaluate accuracy and update custom vocabularies with new medical terms

## 7. Hands-On Labs

### Quick Setup
1. Navigate to Amazon Transcribe console → select "Medical transcription"
2. Upload medical audio file to an S3 bucket
3. Create medical transcription job → specify language (en-US), specialty (PRIMARYCARE), type (DICTATION or CONVERSATION)
4. Review transcript output in console or download JSON from S3

### Practical Exercises

**Exercise 1: Batch Medical Transcription**
1. Upload a medical dictation audio file to S3
2. Create a medical transcription job with `PRIMARYCARE` specialty and `DICTATION` type
3. Review the output JSON for medical term abbreviations and confidence scores

**Exercise 2: Medical Custom Vocabulary**
1. Create a text file with specialty-specific medical terms
2. Create a medical custom vocabulary using the text file
3. Run a transcription job with and without the custom vocabulary — compare accuracy

**Exercise 3: Real-Time Medical Streaming**
1. Open the Transcribe console → Real-time transcription → Medical
2. Select a medical specialty (e.g., CARDIOLOGY)
3. Speak medical terms into your microphone and observe real-time transcription

### CLI Commands

```bash
# Start a medical batch transcription job
aws transcribe start-medical-transcription-job \
    --medical-transcription-job-name my-medical-job \
    --language-code en-US \
    --media MediaFileUri=s3://my-bucket/medical-audio.flac \
    --output-bucket-name my-output-bucket \
    --specialty PRIMARYCARE \
    --type CONVERSATION

# Start a medical dictation job with custom vocabulary
aws transcribe start-medical-transcription-job \
    --medical-transcription-job-name my-dictation-job \
    --language-code en-US \
    --media MediaFileUri=s3://my-bucket/dictation.wav \
    --output-bucket-name my-output-bucket \
    --specialty PRIMARYCARE \
    --type DICTATION \
    --settings VocabularyName=my-medical-vocab

# Check medical transcription job status
aws transcribe get-medical-transcription-job \
    --medical-transcription-job-name my-medical-job

# List medical transcription jobs
aws transcribe list-medical-transcription-jobs --status COMPLETED

# Create a medical custom vocabulary
aws transcribe create-medical-vocabulary \
    --vocabulary-name my-medical-vocab \
    --language-code en-US \
    --vocabulary-file-uri s3://my-bucket/medical-terms.txt

# Delete a medical transcription job
aws transcribe delete-medical-transcription-job \
    --medical-transcription-job-name my-medical-job
```

### Console Walkthrough
1. **Amazon Transcribe Console** → Transcription jobs → Create job → Select "Medical"
2. Specify job name, language (en-US), S3 input URI
3. Select specialty (PRIMARYCARE) and type (DICTATION or CONVERSATION)
4. Optionally attach a medical custom vocabulary
5. Specify output S3 bucket
6. Create job → monitor status → review JSON results

## 8. Sample Questions

### Question 1
**A healthcare organization wants to transcribe real-time cardiology consultations between physicians and patients. The audio is in US English. Which configuration should they use?**

A. Amazon Transcribe Medical batch transcription with specialty set to CARDIOLOGY and type set to CONVERSATION
B. Amazon Transcribe Medical streaming transcription with specialty set to CARDIOLOGY and type set to CONVERSATION
C. Amazon Transcribe Medical batch transcription with specialty set to PRIMARYCARE and type set to DICTATION
D. AWS HealthScribe with specialty set to CARDIOLOGY

**Correct Answer: B**

**Explanation**: Cardiology is a **streaming-only** specialty in Transcribe Medical — it does not support batch transcription. Since the scenario involves real-time physician-patient conversations, streaming with CONVERSATION type is correct. Option A is wrong because Cardiology doesn't support batch. Option C uses the wrong specialty and type. Option D (HealthScribe) could work but the question specifically asks about transcription, and HealthScribe is a broader service that also generates clinical notes — Transcribe Medical is the more targeted answer for pure transcription.

---

### Question 2
**A pharmaceutical company needs to transcribe recorded phone calls reporting drug side effects for pharmacovigilance. The calls are stored as audio files in Amazon S3 and are in US English. They need to extract medication names and adverse events from the transcripts. Which combination of services should they use?**

A. Amazon Transcribe with PII redaction → Amazon Comprehend
B. Amazon Transcribe Medical (batch, PRIMARYCARE, CONVERSATION) → Amazon Comprehend Medical
C. Amazon Transcribe Call Analytics → Amazon Comprehend Medical
D. AWS HealthScribe → Amazon Textract

**Correct Answer: B**

**Explanation**: This is a medical use case (drug safety monitoring) with recorded audio files in S3, making Transcribe Medical batch transcription the right choice. The PRIMARYCARE specialty supports batch mode, and CONVERSATION type is appropriate for phone calls. Amazon Comprehend Medical then extracts medical entities (medications, adverse events) from the transcript text. Option A uses standard Transcribe which lacks medical terminology optimization. Option C is for contact center analytics, not medical transcription. Option D uses HealthScribe (for clinical notes) and Textract (for document images), neither of which fits this scenario.

---

### Question 3
**A hospital is evaluating AWS services for clinical documentation. They need a solution that automatically transcribes physician-patient conversations AND generates preliminary clinical notes using AI. Which service should they choose?**

A. Amazon Transcribe Medical with custom vocabulary
B. Amazon Transcribe Medical → Amazon Comprehend Medical → manual note creation
C. AWS HealthScribe
D. Amazon Bedrock with a foundation model

**Correct Answer: C**

**Explanation**: AWS HealthScribe is specifically designed to combine speech recognition with generative AI to both transcribe conversations AND generate preliminary clinical notes. It identifies speaker roles, classifies dialogues, extracts medical terms, and produces AI-generated clinical documentation. Option A only transcribes (no note generation). Option B requires manual note creation. Option D could theoretically work but is not purpose-built for clinical documentation like HealthScribe.

---

**Exam Tip**: Know the key differences between Transcribe Medical, AWS HealthScribe, and Comprehend Medical. Transcribe Medical = medical speech→text. HealthScribe = speech→text + AI clinical notes. Comprehend Medical = text→medical entities. Also remember: batch mode only supports PRIMARYCARE specialty; all other specialties require streaming. The service is US English only and HIPAA eligible.
