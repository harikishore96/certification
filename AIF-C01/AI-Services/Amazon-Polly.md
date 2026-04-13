# Amazon Polly

## 1. Service/Topic Overview
- **Purpose**: Cloud-based text-to-speech (TTS) service that converts text into lifelike speech using deep learning technologies
- **Service Level**: Regional
- **Key Use Cases**:
  - Voice-enabled applications (newsreaders, games, eLearning)
  - Accessibility for visually impaired users
  - Contact center IVR prompts (Amazon Connect integration)
  - IoT device voice output
  - Content narration (articles, training materials, marketing videos)
  - Multilingual content localization (with Amazon Translate)
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
  - "Text-to-speech" or "TTS"
  - "Convert text to audio"
  - "Speech synthesis"
  - "Lifelike voice" or "Natural-sounding speech"
  - "SSML" or "Speech Synthesis Markup Language"
  - "Newscaster style"
  - "Custom lexicon"
  - "Speech marks"
  - "Brand Voice"
- **Scenario Indicators**:
  - "Read news articles aloud" → Newscaster speaking style or Long-form engine
  - "Pronounce company names correctly" → Custom Lexicons
  - "Synchronize lip movements with audio" → Speech Marks (visemes)
  - "Highlight words as they're spoken" → Speech Marks (word timing)
  - "Most human-like voice" → Generative engine
  - "Narrate long articles or audiobooks" → Long-form engine
  - "Contact center voice prompts" → Polly + Amazon Connect
  - "Unique voice for brand" → Brand Voice (custom engagement)
  - "Translate then speak" → Amazon Translate → Amazon Polly pipeline
- **Anti-patterns**:
  - ❌ Don't use for speech-to-text (use Amazon Transcribe)
  - ❌ Don't use for language translation (use Amazon Translate)
  - ❌ Don't use for text analysis/NLP (use Amazon Comprehend)
  - ❌ Don't use for conversational interfaces (use Amazon Lex — which uses Polly internally)
  - ❌ Polly is NOT a translation service — it speaks text in the language of the selected voice

## 3. Core Concepts

### How It Works
```
Input Text (Plain text or SSML)
    ↓
Select Voice Engine (Generative / Long-form / Neural / Standard)
    ↓
Select Voice ID (language + gender)
    ↓
Amazon Polly TTS Engine
├── Optional: Custom Lexicons (pronunciation overrides)
├── Optional: SSML tags (pitch, rate, volume, style)
    ↓
Output Audio Stream (MP3, OGG Vorbis, PCM)
+ Optional: Speech Marks metadata (JSON)
```

### Four Voice Engines

| Engine | Quality | Best For | Key Characteristics |
|--------|---------|----------|---------------------|
| **Generative** | Highest | Conversational, assistants, ads | Billion-parameter transformer + convolution decoder; most human-like; 43 voices; emotionally engaged, assertive, colloquial |
| **Long-form** | Very High | Articles, training, audiobooks | Optimized for longer content; interprets text meaning via embeddings; correct emphasis, pauses, tone; 6 voices (4 en-US, 2 es-ES) |
| **Neural (NTTS)** | High | General purpose, news | Neural network → spectrograms → vocoder → waveforms; Newscaster style supported; 36+ languages |
| **Standard** | Good | Legacy, widest language coverage | Concatenative TTS; largest voice selection; lowest cost |

### Generative Engine Details
- **Architecture**: Billion-parameter transformer + convolution-based decoder
- **Streaming**: Bidirectional Streaming API (stream input AND output simultaneously)
- **Voices**: 43 voices across 23 language variants
- **Hallucination Safety**: Built-in emergency stop mechanism prevents inappropriate output
- **Limitation**: Speech marks NOT supported; Newscaster style NOT supported
- **Note**: Voice may sound slightly different across model updates (generative AI side effect)
- **Regions**: us-east-1, eu-central-1, us-west-2, ap-northeast-1, ap-northeast-2, ap-southeast-1, eu-west-2, ca-central-1

### Long-form Engine Details
- **Purpose**: Human-like, expressive voices for longer content
- **Technology**: Deep learning TTS with text embeddings for meaning interpretation
- **Voices**: Danielle, Gregory, Ruth, Patrick (en-US); Alba, Raúl (es-ES)
- **Region**: US East (N. Virginia) only
- **Supports**: Speech marks, many SSML tags, real-time and async synthesis

### Neural Engine Details
- **Technology**: Neural network converts phonemes → spectrograms; vocoder converts → waveforms
- **Voices**: 36+ languages and variants
- **Newscaster Style**: Supported (Matthew, Joanna for en-US; Amy for en-GB; Lupe for es-US)
- **Regions**: 15 AWS regions
- **Supports**: Speech marks, many SSML tags

### Standard Engine Details
- **Technology**: Concatenative TTS (traditional approach)
- **Advantage**: Widest language and voice selection; lowest cost
- **Supports**: All SSML tags, all speech marks

### Audio Output Formats
| Format | Use Case |
|--------|----------|
| MP3 | Web and mobile applications |
| OGG (Vorbis) | Web streaming, gaming |
| PCM (raw) | IoT devices, telephony solutions |

### Sampling Rates
- 8 kHz, 16 kHz, 22 kHz, 24 kHz
- Default: 22 kHz (Standard), 24 kHz (Neural, Long-form, Generative)
- Lower rates = smaller file size; higher rates = better quality

### Integration Points
- **Amazon Connect**: Native integration for contact center IVR voice prompts
- **Amazon Lex**: Uses Polly internally for chatbot voice responses
- **Amazon Translate**: Translate text → Polly speaks translated content
- **Amazon Transcribe**: Transcribe → Translate → Polly (speech-to-speech pipeline)
- **S3**: Store synthesized audio files (async synthesis)
- **Lambda**: Serverless TTS workflows
- **Chime SDK**: Real-time communications with TTS
- **Genesys Cloud CX**: Contact center voice bots
- **AWS CCI Partners**: Vonage, Accenture, Genesys

## 4. Key Features & Components

### Speech Synthesis APIs
| API | Mode | Description |
|-----|------|-------------|
| SynthesizeSpeech | Real-time (synchronous) | Returns audio stream immediately; for interactive apps |
| StartSpeechSynthesisTask | Asynchronous | Stores output in S3; for longer text or batch processing |
| StartSpeechSynthesisStream | Bidirectional streaming | Stream input AND output simultaneously (Generative engine only) |

### SSML (Speech Synthesis Markup Language)
- **Purpose**: Fine-grained control over speech output using XML-based markup
- **Wrap text in**: `<speak>` tags
- **Key SSML Tags**:

| Tag | Purpose | Example |
|-----|---------|---------|
| `<break>` | Add pause | `<break time="1s"/>` |
| `<prosody>` | Control rate, pitch, volume | `<prosody rate="slow" pitch="high">` |
| `<emphasis>` | Stress a word | `<emphasis level="strong">important</emphasis>` |
| `<phoneme>` | Specify pronunciation | `<phoneme alphabet="ipa" ph="...">` |
| `<say-as>` | Interpret as (date, number, etc.) | `<say-as interpret-as="date">` |
| `<sub>` | Substitute text | `<sub alias="World Wide Web Consortium">W3C</sub>` |
| `<amazon:domain>` | Speaking style | `<amazon:domain name="news">` (Newscaster) |
| `<amazon:effect>` | Special effects | `<amazon:effect name="whispered">` |

- **Note**: Not all SSML tags supported by all engines — Neural/Generative support a subset

### Newscaster Speaking Style
- **Purpose**: Synthesize speech as if spoken by a TV/radio newscaster
- **SSML**: `<amazon:domain name="news">text</amazon:domain>`
- **Available Voices**: Matthew, Joanna (en-US); Amy (en-GB); Lupe (es-US) — Neural engine only
- **NOT supported**: Generative engine, Standard engine
- **Use Cases**: News articles, flash briefings, podcast intros

### Speech Marks
- **Purpose**: Metadata describing when sentences, words, and sounds are pronounced in the audio
- **Output**: Line-delimited JSON stream (separate from audio)
- **Types**:
  - **Sentence**: Start/end of each sentence
  - **Word**: Start/end of each word
  - **Viseme**: Visual mouth positions for facial animation / lip-sync
  - **SSML**: Custom marks placed in SSML input
- **Use Cases**:
  - Karaoke-style word highlighting
  - Speech-synchronized facial animation (visemes)
  - Subtitle timing
- **Availability**: Neural, Long-form, Standard engines (NOT Generative)

### Custom Lexicons
- **Purpose**: Override pronunciation of specific words (company names, acronyms, foreign words)
- **Format**: XML files following W3C Pronunciation Lexicon Specification (PLS)
- **How It Works**: Upload lexicon → apply during SynthesizeSpeech call
- **Features**:
  - Define aliases (e.g., "W3C" → "World Wide Web Consortium")
  - Specify phonetic pronunciation using IPA or X-SAMPA
  - Multiple lexicons can be applied simultaneously
  - Region-specific storage
- **Example**: Customize "Nguyen" pronunciation with phoneme entry

### Brand Voice
- **Purpose**: Build a unique, exclusive NTTS voice for your organization
- **Process**: Custom engagement with Amazon Polly team
  1. Identify persona
  2. Select and record actor/actress
  3. Train custom model
  4. Deploy to your AWS account ID(s)
- **Use Cases**: Differentiated brand identity, Amazon Connect integration, Alexa Skills
- **Note**: Not self-service — requires working with AWS team

### Time-Driven Prosody
- **Purpose**: Automatically adjust speech rate to fit a maximum time duration
- **Use Case**: Localization — ensure translated speech fits within original video frame timing
- **Example**: English video → translate to German → Polly speaks German within same time constraints

## 5. Exam Focus Areas

### Common Question Types

#### Service Selection
**Question Pattern**: "A company needs to [convert text to audio / add voice to application]. Which service?"

**Decision Framework**:
```
Voice/Speech Need?
│
├─ Text → Speech → Amazon Polly
├─ Speech → Text → Amazon Transcribe
├─ Text → Translation → Amazon Translate
├─ Text → Analysis → Amazon Comprehend
├─ Build chatbot → Amazon Lex (uses Polly internally)
└─ Text generation → Amazon Bedrock
```

**Engine Selection Decision Tree**:
```
Which Polly engine?
│
├─ Most human-like, conversational → Generative
├─ Long articles, audiobooks, training → Long-form
├─ News narration (Newscaster style) → Neural
├─ Widest language support, lowest cost → Standard
└─ Need speech marks + high quality → Neural or Long-form
```

### Gotchas
1. **NOT a translation service**: Polly speaks text in the voice's language — numbers like "53" are spoken in the voice's language, not the text's language
2. **Generative ≠ Speech Marks**: Generative engine does NOT support speech marks
3. **Generative ≠ Newscaster**: Newscaster style only works with Neural engine
4. **Long-form region limited**: Only available in US East (N. Virginia)
5. **Newscaster limited voices**: Only Matthew, Joanna (en-US), Amy (en-GB), Lupe (es-US)
6. **SSML tag support varies**: Not all tags work with all engines — Neural/Generative support a subset
7. **Generative hallucination**: Emergency stop mechanism may cut words short at sentence boundaries
8. **Brand Voice is NOT self-service**: Requires custom engagement with AWS team
9. **Caching is free**: You can cache and replay Polly's generated speech at no additional cost
10. **Bilingual voice**: Polly offers a bilingual voice (English + Hindi) — Kajal
11. **Bidirectional streaming**: Only available with Generative engine in select regions

### Comparison Points

#### Four Voice Engines
```
Generative (Newest, Highest Quality)
├─ Billion-parameter transformer
├─ Most human-like, emotionally engaged
├─ 43 voices, 23 language variants
├─ Bidirectional streaming
├─ ❌ No speech marks, no Newscaster
└─ Use: Conversational assistants, ads

Long-form
├─ Text embeddings for meaning interpretation
├─ Optimized for longer content
├─ 6 voices (en-US, es-ES only)
├─ ✅ Speech marks, SSML
├─ ❌ US East (N. Virginia) only
└─ Use: Articles, audiobooks, training

Neural (NTTS)
├─ Neural network + vocoder pipeline
├─ Newscaster speaking style
├─ 36+ languages
├─ ✅ Speech marks, SSML
└─ Use: General purpose, news narration

Standard
├─ Concatenative TTS (traditional)
├─ Widest language/voice selection
├─ Lowest cost
├─ ✅ All SSML tags, all speech marks
└─ Use: Legacy apps, budget-conscious
```

#### Polly vs. Transcribe
```
Amazon Polly
├─ Direction: Text → Speech
├─ Input: Plain text or SSML
├─ Output: Audio stream (MP3, OGG, PCM)
└─ Use: Voice-enable applications

Amazon Transcribe
├─ Direction: Speech → Text
├─ Input: Audio stream or file
├─ Output: Text transcript (JSON)
└─ Use: Transcription, subtitles
```

#### Custom Lexicons vs. SSML
```
Custom Lexicons
├─ Scope: Account-wide pronunciation overrides
├─ Format: XML (PLS standard)
├─ Persistence: Stored in AWS, reusable across requests
├─ Use: Company names, acronyms, foreign words
└─ Applied: Via SynthesizeSpeech parameter

SSML Tags
├─ Scope: Per-request speech customization
├─ Format: XML tags within <speak> block
├─ Persistence: Inline with each request
├─ Use: Pitch, rate, volume, pauses, emphasis, styles
└─ Applied: Directly in input text
```

### Multi-Service Pipelines
```
Common Patterns:
1. Translate → Polly (speak translated content)
2. Transcribe → Translate → Polly (speech-to-speech translation)
3. Comprehend → Polly (analyze text, then narrate summary)
4. Lex → Polly (chatbot voice responses — built-in)
5. Connect → Polly (contact center IVR prompts — native integration)
6. S3 → Polly (async) → S3 (batch audio generation)
```

## 6. Best Practices

### Security
- **IAM**: Least privilege access to Polly APIs
- **Compliance**: HIPAA-eligible, PCI DSS certified
- **Encryption**: SSL/TLS for data in transit; S3 encryption for stored audio
- **No data storage**: Polly does not store input text after processing

### Cost Optimization
- **Pricing**: Pay-per-character (total characters sent for synthesis)
  - Standard voices: Lowest rate
  - Neural voices: Higher rate
  - Long-form voices: Higher rate
  - Generative voices: Highest rate
- **Free Tier**: 5 million characters/month Standard; 1 million characters/month Neural (12 months)
- **Caching**: Cache and replay generated speech at NO additional cost — major cost saver
- **Engine selection**: Use Standard for non-critical audio; Neural/Generative only when quality matters
- **Async for long text**: Use StartSpeechSynthesisTask for longer content (stores in S3)

### Performance
- **Real-time streaming**: SynthesizeSpeech returns audio immediately for low-latency use cases
- **Bidirectional streaming**: Generative engine streams input/output simultaneously
- **Sampling rate**: Choose appropriate rate — 8 kHz for telephony, 24 kHz for high-quality
- **Regional deployment**: Deploy in region closest to users

### Operations
- **CloudWatch**: Monitor API calls, character count, errors
- **Lexicon management**: Store lexicons per region; apply up to 5 lexicons per request
- **Voice updates**: Neural/Generative voices may change slightly with model updates — test after updates

## 7. Hands-On Labs

### Quick Setup: Basic Text-to-Speech
```python
import boto3

polly = boto3.client('polly', region_name='us-east-1')

# Synthesize speech
response = polly.synthesize_speech(
    Text='Hello, my name is Joanna. Welcome to Amazon Polly.',
    OutputFormat='mp3',
    VoiceId='Joanna',
    Engine='neural'
)

# Save audio to file
with open('output.mp3', 'wb') as f:
    f.write(response['AudioStream'].read())
```

### Practical Exercise 1: SSML with Newscaster Style
```python
ssml_text = """
<speak>
    <amazon:domain name="news">
        In today's top story, Amazon Web Services announced new features
        for their machine learning services, including improvements to
        Amazon Polly's voice synthesis capabilities.
    </amazon:domain>
</speak>
"""

response = polly.synthesize_speech(
    TextType='ssml',
    Text=ssml_text,
    OutputFormat='mp3',
    VoiceId='Joanna',
    Engine='neural'
)
```

### Practical Exercise 2: Speech Marks for Word Highlighting
```python
response = polly.synthesize_speech(
    Text='Hello, welcome to Amazon Polly.',
    OutputFormat='json',
    VoiceId='Joanna',
    Engine='neural',
    SpeechMarkTypes=['word', 'sentence']
)

# Speech marks returned as line-delimited JSON
marks = response['AudioStream'].read().decode('utf-8')
print(marks)
```

### Practical Exercise 3: Generative Voice
```python
response = polly.synthesize_speech(
    Text='Hi there! I am Ruth, and I sound incredibly natural and conversational.',
    OutputFormat='mp3',
    VoiceId='Ruth',
    Engine='generative'
)
```

### CLI Commands
```bash
# Basic synthesis with Neural engine
aws polly synthesize-speech \
  --text "Hello, this is Amazon Polly." \
  --output-format mp3 \
  --voice-id Joanna \
  --engine neural \
  output.mp3

# SSML synthesis with Newscaster style
aws polly synthesize-speech \
  --text-type ssml \
  --text '<speak><amazon:domain name="news">Breaking news today.</amazon:domain></speak>' \
  --output-format mp3 \
  --voice-id Joanna \
  --engine neural \
  news.mp3

# Async synthesis (stores in S3)
aws polly start-speech-synthesis-task \
  --text "Long article text here..." \
  --output-format mp3 \
  --voice-id Ruth \
  --engine long-form \
  --output-s3-bucket-name my-audio-bucket

# List available voices
aws polly describe-voices --engine neural

# Upload custom lexicon
aws polly put-lexicon \
  --name my-lexicon \
  --content file://my-lexicon.xml

# Synthesize with lexicon
aws polly synthesize-speech \
  --text "Hello from Nguyen." \
  --output-format mp3 \
  --voice-id Joanna \
  --engine neural \
  --lexicon-names my-lexicon \
  output.mp3
```

## 8. Sample Questions

### Question 1: Engine Selection
**Scenario**: A media company wants to generate audio versions of their long-form news articles and blog posts. The audio must sound natural with correct emphasis, pauses, and emotional tone appropriate to the content. Which Amazon Polly engine should they use?

A) Standard engine with SSML tags for emphasis
B) Neural engine with Newscaster speaking style
C) Long-form engine
D) Generative engine

**Correct Answer**: C) Long-form engine

**Explanation**:
- ✅ **C is correct**: The Long-form engine is specifically designed for longer content like news articles, training materials, and marketing videos. It uses text embeddings to interpret meaning and generate correct emphasis, pauses, and tone automatically — no manual SSML tuning needed.
- ❌ **A is wrong**: Standard engine produces lower quality speech and would require extensive manual SSML markup to achieve natural emphasis and pauses for long content.
- ❌ **B is wrong**: Newscaster style is designed for a specific news anchor persona, not general long-form content. It's also limited to 4 specific voices.
- ❌ **D is wrong**: Generative engine produces the most human-like speech but is optimized for conversational/interactive use cases, not specifically for long-form content narration. It also doesn't support speech marks if needed for synchronization.

### Question 2: Feature Selection
**Scenario**: A gaming company is building an animated character that needs to move its lips in sync with spoken dialogue. They need metadata about mouth positions during speech synthesis. Which Amazon Polly feature should they use?

A) SSML prosody tags
B) Custom Lexicons
C) Speech Marks with viseme type
D) Newscaster speaking style

**Correct Answer**: C) Speech Marks with viseme type

**Explanation**:
- ✅ **C is correct**: Speech Marks provide metadata including visemes — the visual mouth positions corresponding to speech sounds. This enables lip-sync animation. Request speech marks with type "viseme" alongside the audio stream.
- ❌ **A is wrong**: SSML prosody tags control speech rate, pitch, and volume — they don't provide lip-sync metadata.
- ❌ **B is wrong**: Custom Lexicons control pronunciation of specific words, not visual animation data.
- ❌ **D is wrong**: Newscaster style changes the speaking persona but doesn't provide animation metadata.

### Question 3: Multi-Service Pipeline
**Scenario**: A company has training videos in English and needs to create localized versions in German. The German audio must fit within the same time frames as the original English audio. Which combination of AWS services and features achieves this?

A) Amazon Transcribe → Amazon Translate → Amazon Polly
B) Amazon Translate → Amazon Polly with time-driven prosody
C) Amazon Bedrock for translation → Amazon Polly with Long-form engine
D) Amazon Translate → Amazon Polly with Newscaster style

**Correct Answer**: B) Amazon Translate → Amazon Polly with time-driven prosody

**Explanation**:
- ✅ **B is correct**: Amazon Translate converts English text to German. Amazon Polly's time-driven prosody feature automatically adjusts speech rate to fit within a maximum time duration — exactly designed for localization dubbing where translated speech must match original video timing.
- ❌ **A is wrong**: Transcribe converts speech-to-text (unnecessary if they already have the English text). Also doesn't address the time-fitting requirement.
- ❌ **C is wrong**: Bedrock is not the purpose-built translation service. Long-form engine is for content quality, not time-constrained output.
- ❌ **D is wrong**: Newscaster style changes the speaking persona to a news anchor — it doesn't control timing to fit video frames.

---

**Exam Tips**:
- Polly = text-to-speech (TTS); Transcribe = speech-to-text (STT) — know the direction
- 4 engines: Generative (highest quality, newest) > Long-form (long content) > Neural (general + Newscaster) > Standard (widest coverage, cheapest)
- Generative engine: NO speech marks, NO Newscaster — common exam distractor
- Long-form engine: US East (N. Virginia) ONLY — region-limited
- Newscaster style: Neural engine ONLY, 4 specific voices (Matthew, Joanna, Amy, Lupe)
- Speech Marks = metadata for lip-sync (visemes), word highlighting, subtitle timing
- Custom Lexicons = pronunciation overrides (PLS XML); SSML = per-request speech control
- Brand Voice = custom engagement with AWS team (NOT self-service)
- Caching is FREE — cache and replay generated speech at no additional cost
- Polly is NOT a translation service — it speaks in the voice's language
- Common pipeline: Translate → Polly (multilingual TTS); Transcribe → Translate → Polly (speech-to-speech)
- Compliance: HIPAA-eligible + PCI DSS certified
