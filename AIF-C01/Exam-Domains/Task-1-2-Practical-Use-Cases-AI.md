# Task Statement 1.2: Identify Practical Use Cases for AI

## 1. Topic Overview
- **Purpose**: Recognize where AI/ML provides value, when it's inappropriate, which ML techniques fit which problems, identify real-world applications, and understand AWS managed AI/ML service capabilities
- **Exam Weight**: HIGH
- **Exam Domain**: Domain 1: Fundamentals of AI and ML (20%)
- **Task Statement**: Task 1.2 — Identify practical use cases for AI

### Exam Objectives (5)
1. Recognize applications where AI/ML can provide value (decision support, scalability, automation)
2. Determine when AI/ML solutions are **not** appropriate (cost-benefit, deterministic outcomes)
3. Select the appropriate ML technique for specific use cases (regression, classification, clustering)
4. Identify examples of real-world AI applications (computer vision, NLP, speech, recommendations, fraud, forecasting)
5. Explain the capabilities of AWS managed AI/ML services (SageMaker, Transcribe, Translate, Comprehend, Lex, Polly)

---

## 2. Exam Keyword Mapping

### Trigger Words
| Keyword / Phrase | Points To |
|---|---|
| "assist human decision", "augment", "recommend" | Value: Decision Support |
| "millions of records", "scale", "high throughput" | Value: Scalability |
| "eliminate manual", "repetitive", "automate" | Value: Automation |
| "predict a number", "how much", "continuous value" | Technique: Regression |
| "categorize", "spam or not", "which category" | Technique: Classification |
| "group similar", "segment customers", "find patterns" | Technique: Clustering |
| "simple rules", "deterministic", "exact answer needed" | Anti-pattern: Don't use AI/ML |

### Anti-patterns (When NOT to Use AI/ML)
- "We need an exact, deterministic answer every time"
- "We only have 50 data points"
- "A simple if/else rule handles this"
- "Cost of ML exceeds the benefit"

---

## 3. Objective 1 — Where AI/ML Provides Value

Three primary value drivers tested on the exam:

### A. Assist Human Decision-Making
- AI augments human expertise with data-driven insights
- Human-in-the-loop: AI recommends, human decides
- Examples: medical diagnosis support, credit risk scoring, legal contract review
- **AWS**: Comprehend Medical, SageMaker, Bedrock

### B. Solution Scalability
- Process millions/billions of records cost-effectively
- Real-time or near-real-time at any volume
- Examples: content moderation for millions of posts, fraud detection across all transactions
- **AWS**: Rekognition, Comprehend, Personalize, Fraud Detector

### C. Automation
- Eliminate repetitive manual tasks
- Consistent quality 24/7
- Examples: invoice data extraction, chatbot responses, resume screening
- **AWS**: Textract, Lex, Comprehend

> **Exam Tip**: Identify the value driver from the scenario keywords — "millions" → scalability, "repetitive" → automation, "assist analysts" → decision support.

📖 Deep dive: [AI/ML Applications and Value](../Fundamentals/AI-ML-Applications-Value.md)

---

## 4. Objective 2 — When AI/ML Is NOT Appropriate

This is a frequently tested area. Know when to say "don't use AI/ML."

### ❌ When NOT to Use AI/ML

| Situation | Why Not | Better Alternative |
|---|---|---|
| **Deterministic outcome required** | ML produces probabilistic predictions, not guaranteed results | Rule-based logic, lookup tables |
| **Insufficient data** | Models can't learn patterns from tiny datasets | Manual rules, expert systems |
| **Simple rule-based logic suffices** | Over-engineering adds cost and complexity | If/else rules, decision trees |
| **Cost exceeds benefit** | ML infrastructure, training, maintenance costs outweigh gains | Manual process, simpler automation |
| **Constantly changing requirements** | Model retraining can't keep up with daily rule changes | Configurable business rules engine |
| **Full explainability legally required** | Complex models (deep learning) are hard to explain | Simpler interpretable models or manual process |
| **One-time task** | ROI doesn't justify model development | Manual effort |
| **No clear success metric** | Can't evaluate if model is working | Define metrics first |

### Cost-Benefit Analysis Framework

```
Should you use AI/ML?

1. Is there sufficient data?          → No  → ❌ Don't use AI/ML
2. Is the problem repetitive/ongoing? → No  → ❌ Probably not worth it
3. Can a simple rule solve it?        → Yes → ❌ Use the simple rule
4. Does benefit > cost?               → No  → ❌ Don't use AI/ML
5. Is a prediction (not exact answer) acceptable? → No → ❌ Don't use AI/ML
6. All yes?                           → ✅ AI/ML is appropriate
```

### Exam Question Pattern
> "A company needs to calculate sales tax based on state codes. Should they use ML?"
> **Answer**: No — this is deterministic rule-based logic. A lookup table is simpler, cheaper, and 100% accurate.

---

## 5. Objective 3 — Select the Appropriate ML Technique

### Three Core Techniques

| Technique | Type | Output | Use When... |
|---|---|---|---|
| **Regression** | Supervised | Continuous number | Predicting a numeric value |
| **Classification** | Supervised | Discrete category/label | Assigning items to categories |
| **Clustering** | Unsupervised | Groups/segments | Finding natural groupings in data (no labels) |

### Regression — Predict a Number

**Output**: Continuous numeric value

| Use Case | What's Predicted | AWS Service |
|---|---|---|
| House price prediction | Price ($) | SageMaker |
| Demand forecasting | Units to sell | Amazon Forecast |
| Revenue forecasting | Revenue ($) | Amazon Forecast |
| Temperature prediction | Degrees | SageMaker |
| Customer lifetime value | Dollar amount | SageMaker |
| Stock price prediction | Price ($) | SageMaker |

**Algorithms**: Linear Regression, Decision Tree Regression, XGBoost (regression mode)

**Exam Keywords**: "how much", "predict a number", "forecast", "estimate", "continuous value"

### Classification — Assign a Category

**Output**: Discrete label (binary or multi-class)

| Use Case | Categories | AWS Service |
|---|---|---|
| Spam detection | Spam / Not Spam (binary) | SageMaker, Comprehend |
| Sentiment analysis | Positive / Negative / Neutral (multi-class) | Comprehend |
| Image recognition | Cat / Dog / Bird (multi-class) | Rekognition |
| Fraud detection | Fraud / Not Fraud (binary) | Fraud Detector |
| Medical diagnosis | Disease A / B / C (multi-class) | SageMaker |
| Document classification | Invoice / Receipt / Contract (multi-class) | Comprehend |
| Content moderation | Safe / Unsafe (binary) | Rekognition |

**Algorithms**: Logistic Regression, Random Forest, XGBoost (classification mode), Neural Networks

**Exam Keywords**: "categorize", "classify", "which type", "is it X or Y", "label", "detect"

### Clustering — Find Natural Groups

**Output**: Groups/segments (no predefined labels)

| Use Case | What's Grouped | AWS Service |
|---|---|---|
| Customer segmentation | Customers by behavior | SageMaker |
| Anomaly detection | Normal vs. outlier patterns | SageMaker |
| Document grouping | Similar documents | SageMaker |
| Market segmentation | Markets by characteristics | SageMaker |
| Gene expression analysis | Similar gene patterns | SageMaker |

**Algorithms**: K-Means, DBSCAN, Hierarchical Clustering

**Exam Keywords**: "group similar", "segment", "find patterns", "no labels", "discover structure", "unlabeled data"

### Decision Tree: Which Technique?

```
Do you have labeled data?
├── YES (Supervised Learning)
│   ├── Is the target a number? → REGRESSION
│   └── Is the target a category? → CLASSIFICATION
└── NO (Unsupervised Learning)
    └── Want to find natural groups? → CLUSTERING
```

### Quick Comparison

| Aspect | Regression | Classification | Clustering |
|---|---|---|---|
| Learning Type | Supervised | Supervised | Unsupervised |
| Requires Labels? | Yes (numeric) | Yes (categories) | No |
| Output | Continuous number | Discrete category | Group assignments |
| Evaluation | MAE, RMSE, R² | Accuracy, F1, AUC-ROC | Silhouette score |
| Example | "Price = $350K" | "Spam = Yes" | "Customer → Group A" |

---

## 6. Objective 4 — Real-World AI Application Categories

### Computer Vision
Analyzing and understanding images and video.

| Application | Description | AWS Service |
|---|---|---|
| Object detection | Identify objects in images/video | Amazon Rekognition |
| Facial analysis | Detect faces, emotions, attributes | Amazon Rekognition |
| Content moderation | Flag inappropriate images/video | Amazon Rekognition |
| OCR / Document extraction | Extract text from documents | Amazon Textract |
| Defect detection | Spot manufacturing defects | Amazon Lookout for Vision |
| Custom image classification | Classify domain-specific images | Rekognition Custom Labels |

**Industries**: Manufacturing (quality inspection), Retail (visual search), Security (surveillance), Healthcare (medical imaging)

### Natural Language Processing (NLP)
Understanding and processing human language text.

| Application | Description | AWS Service |
|---|---|---|
| Sentiment analysis | Determine positive/negative/neutral | Amazon Comprehend |
| Entity extraction | Extract names, dates, places | Amazon Comprehend |
| Language detection | Identify text language | Amazon Comprehend |
| Topic modeling | Discover topics in documents | Amazon Comprehend |
| Text classification | Categorize documents | Amazon Comprehend |
| Medical text extraction | Extract medical entities | Amazon Comprehend Medical |
| Translation | Translate between languages | Amazon Translate |
| Intelligent search | Find answers in documents | Amazon Kendra |

**Industries**: Healthcare (clinical notes), Legal (contract analysis), Customer Service (ticket routing), Media (content analysis)

### Speech Recognition & Synthesis
Converting between speech and text.

| Application | Description | AWS Service |
|---|---|---|
| Speech-to-text | Transcribe audio to text | Amazon Transcribe |
| Text-to-speech | Generate natural speech from text | Amazon Polly |
| Call analytics | Analyze customer service calls | Transcribe Call Analytics |
| Medical transcription | Transcribe clinical conversations | Transcribe Medical |
| Conversational AI | Voice/text chatbots | Amazon Lex |
| Clinical documentation | Auto-generate clinical notes | AWS HealthScribe |

**Industries**: Contact Centers (call transcription), Healthcare (clinical documentation), Media (subtitles), Accessibility (screen readers)

### Recommendation Systems
Personalized suggestions based on user behavior.

| Application | Description | AWS Service |
|---|---|---|
| Product recommendations | "Customers also bought" | Amazon Personalize |
| Content recommendations | "You might also like" | Amazon Personalize |
| User segmentation | Group users by interests | Amazon Personalize |
| Search ranking | Personalized search results | Amazon Personalize + Kendra |

**Industries**: E-commerce (product suggestions), Media/Streaming (content curation), Marketing (targeted campaigns)

### Fraud Detection
Identifying fraudulent or anomalous activity.

| Application | Description | AWS Service |
|---|---|---|
| Transaction fraud | Flag suspicious payments | Amazon Fraud Detector |
| Account fraud | Detect fake account creation | Amazon Fraud Detector |
| Identity fraud | Verify identity claims | Amazon Fraud Detector |
| Anomaly detection | Detect unusual patterns | SageMaker |

**Industries**: Banking (payment fraud), Insurance (claims fraud), E-commerce (account takeover)

### Forecasting
Predicting future values from time-series data.

| Application | Description | AWS Service |
|---|---|---|
| Demand forecasting | Predict product demand | Amazon Forecast |
| Revenue forecasting | Predict future revenue | Amazon Forecast |
| Resource planning | Predict capacity needs | Amazon Forecast |
| Inventory optimization | Predict stock requirements | Amazon Forecast |

**Industries**: Retail (inventory), Supply Chain (logistics), Finance (budgeting), Energy (demand planning)

### Summary: Application → AWS Service Quick Map

```
"I need to..."
├── Analyze images/video         → Amazon Rekognition
├── Extract text from documents  → Amazon Textract
├── Understand text sentiment    → Amazon Comprehend
├── Extract medical text         → Amazon Comprehend Medical
├── Translate languages          → Amazon Translate
├── Convert speech to text       → Amazon Transcribe
├── Convert text to speech       → Amazon Polly
├── Build a chatbot              → Amazon Lex
├── Search enterprise documents  → Amazon Kendra
├── Recommend products/content   → Amazon Personalize
├── Detect fraud                 → Amazon Fraud Detector
├── Forecast time-series data    → Amazon Forecast
├── Generate text/images (GenAI) → Amazon Bedrock
├── Build custom ML models       → Amazon SageMaker
└── No-code ML predictions       → SageMaker Canvas
```

---

## 7. Objective 5 — AWS Managed AI/ML Service Capabilities

### Amazon SageMaker AI
- **Purpose**: Build, train, and deploy custom ML models at any scale
- **Key Capabilities**: End-to-end ML platform — data labeling, training, tuning, deployment, monitoring
- **Use When**: You need a custom model, full control over algorithms, or MLOps pipelines
- **Sub-services**: Autopilot (AutoML), Canvas (no-code), Clarify (bias/explainability), JumpStart (pre-trained models), Pipelines (CI/CD for ML)
- 📖 [Amazon SageMaker Notes](../ML-Services/Amazon-SageMaker.md)

### Amazon Transcribe
- **Purpose**: Automatic speech recognition (ASR) — convert speech to text
- **Key Capabilities**: Real-time and batch transcription, speaker diarization, PII redaction, custom vocabulary, language identification
- **Variants**: Transcribe Medical (HIPAA-eligible clinical speech), Transcribe Call Analytics (contact center insights)
- **Use When**: Transcribe audio/video, generate subtitles, analyze call recordings
- 📖 [Amazon Transcribe Notes](../AI-Services/Amazon-Transcribe.md)

### Amazon Translate
- **Purpose**: Neural machine translation between languages
- **Key Capabilities**: Real-time and batch translation, custom terminology, automatic language detection, 75+ languages
- **Use When**: Translate text content, localize applications, enable multilingual communication
- 📖 [Amazon Translate Notes](../AI-Services/Amazon-Translate.md)

### Amazon Comprehend
- **Purpose**: NLP service to extract insights from text
- **Key Capabilities**: Sentiment analysis, entity extraction, key phrase extraction, language detection, topic modeling, PII detection, custom classification and entity recognition
- **Variant**: Comprehend Medical (extract medical entities — conditions, medications, dosages)
- **Use When**: Analyze customer feedback, classify documents, extract entities from text
- 📖 [Amazon Comprehend Notes](../AI-Services/Amazon-Comprehend.md)

### Amazon Lex
- **Purpose**: Build conversational interfaces (chatbots) using voice and text
- **Key Capabilities**: Automatic speech recognition (ASR), natural language understanding (NLU), multi-turn dialog, slot filling, integration with Lambda for fulfillment
- **Powered by**: Same technology as Amazon Alexa
- **Use When**: Build chatbots, IVR systems, virtual assistants
- 📖 [Amazon Lex Notes](../AI-Services/Amazon-Lex.md)

### Amazon Polly
- **Purpose**: Text-to-speech (TTS) — convert text into lifelike speech
- **Key Capabilities**: Standard and Neural TTS voices, SSML support, multiple languages, Newscaster style, Brand Voice (custom), real-time streaming
- **Use When**: Add speech to applications, create audiobooks, build accessibility features, voice-enable products
- 📖 [Amazon Polly Notes](../AI-Services/Amazon-Polly.md)

### Additional Exam-Relevant Services

| Service | Purpose | Key Differentiator |
|---|---|---|
| **Amazon Rekognition** | Image and video analysis | Pre-trained CV models, no ML expertise needed |
| **Amazon Textract** | Extract text/data from documents | Goes beyond OCR — understands forms and tables |
| **Amazon Personalize** | Real-time personalization and recommendations | Same tech as Amazon.com recommendations |
| **Amazon Forecast** | Time-series forecasting | Combines time-series with related variables |
| **Amazon Fraud Detector** | Online fraud detection | Built on 20+ years of Amazon fraud expertise |
| **Amazon Kendra** | Intelligent enterprise search | ML-powered, understands natural language queries |
| **Amazon Bedrock** | Access foundation models via API | Serverless, multiple FM providers, fine-tuning, RAG |
| **Amazon A2I** | Human review workflows | Add human oversight to ML predictions |

### Service Selection Decision Tree

```
What type of data are you working with?

TEXT:
├── Understand sentiment/entities?     → Comprehend
├── Translate to another language?     → Translate
├── Search across documents?           → Kendra
├── Extract from scanned documents?    → Textract
├── Medical text?                      → Comprehend Medical
└── Generate new text?                 → Bedrock

SPEECH/AUDIO:
├── Speech → Text?                     → Transcribe
├── Text → Speech?                     → Polly
└── Conversational bot?                → Lex

IMAGES/VIDEO:
├── Detect objects/faces/text?         → Rekognition
├── Manufacturing defect detection?    → Lookout for Vision
└── Custom image classification?       → Rekognition Custom Labels

STRUCTURED/TABULAR DATA:
├── Predict a number?                  → SageMaker (regression) or Forecast
├── Classify into categories?          → SageMaker (classification)
├── Detect fraud?                      → Fraud Detector
├── Recommend items?                   → Personalize
└── No-code ML?                        → SageMaker Canvas

NEED FULL CONTROL?                     → SageMaker (custom models)
```

---

## 8. Sample Questions

### Question 1: When NOT to Use AI/ML

**Scenario**: A retail company calculates shipping costs based on package weight and destination zip code using a fixed rate table. A developer suggests using ML to predict shipping costs.

**Question**: Is ML appropriate for this use case?

A) Yes, ML can predict shipping costs more accurately than a rate table  
B) Yes, ML can scale to handle millions of shipments  
C) No, the shipping cost is deterministic and a lookup table is simpler and more accurate  
D) No, because ML cannot work with numeric data

**Correct Answer**: C

**Explanation**:
- **Why C**: Shipping cost from a fixed rate table is deterministic — same inputs always produce the same output. A simple lookup is 100% accurate, cheaper, and faster. ML adds unnecessary complexity.
- **Why A is wrong**: ML predictions are probabilistic. A lookup table is already 100% accurate for deterministic calculations.
- **Why B is wrong**: Scalability isn't the issue — a lookup table also scales easily.
- **Why D is wrong**: ML works fine with numeric data; the issue is that ML is unnecessary here.

---

### Question 2: Selecting the Right ML Technique

**Scenario**: An e-commerce company wants to group its customers into segments based on purchasing behavior to create targeted marketing campaigns. They have no predefined customer categories.

**Question**: Which ML technique is most appropriate?

A) Linear regression  
B) Binary classification  
C) Clustering  
D) Reinforcement learning

**Correct Answer**: C

**Explanation**:
- **Why C**: No predefined labels ("no predefined customer categories") + "group customers" = unsupervised learning → clustering. K-Means or similar algorithms discover natural segments.
- **Why A is wrong**: Regression predicts a continuous number, not groups.
- **Why B is wrong**: Classification requires predefined labels/categories, which don't exist here.
- **Why D is wrong**: Reinforcement learning is for sequential decision-making with rewards, not customer segmentation.

---

### Question 3: Matching Use Case to AWS Service

**Scenario**: A media company needs to automatically generate subtitles for thousands of video files in multiple languages.

**Question**: Which combination of AWS services should they use?

A) Amazon Polly and Amazon Translate  
B) Amazon Transcribe and Amazon Translate  
C) Amazon Comprehend and Amazon Polly  
D) Amazon Lex and Amazon Translate

**Correct Answer**: B

**Explanation**:
- **Why B**: Transcribe converts speech → text (subtitles in original language), then Translate converts text to other languages. This is the standard subtitle generation pipeline.
- **Why A is wrong**: Polly converts text → speech, which is the opposite of what's needed for subtitles.
- **Why C is wrong**: Comprehend analyzes text (sentiment, entities) — it doesn't transcribe audio. Polly generates speech, not subtitles.
- **Why D is wrong**: Lex builds chatbots — it doesn't transcribe audio from video files.

---

### Question 4: Identifying Value Proposition

**Scenario**: A bank processes 5 million credit card transactions daily. Currently, a team of 10 analysts manually reviews flagged transactions, but they can only review 500 per day, missing potential fraud.

**Question**: Which AI/ML value propositions does implementing Amazon Fraud Detector address? (Select TWO)

A) Assist human decision-making by providing risk scores  
B) Scalability to analyze all 5 million transactions  
C) Automation of the entire fraud investigation process  
D) Personalization of fraud rules per customer

**Correct Answer**: A and B

**Explanation**:
- **Why A**: Fraud Detector provides risk scores and recommendations, helping analysts prioritize which transactions to investigate — this is decision support.
- **Why B**: Analyzing all 5 million transactions (vs. 500 manually) is a massive scalability improvement.
- **Why C is wrong**: Fraud detection still requires human investigation for confirmed fraud cases. It automates detection, not the full investigation.
- **Why D is wrong**: While rules can be customized, "personalization per customer" isn't the primary value here.

---

## Citations

- [AIF-C01 Domain 1: Fundamentals of AI and ML](https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain1.html)
- [AWS Overview: Machine Learning and AI Services](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/machine-learning.html)
- [Responsible AI Lens — Identify the type of AI required](https://docs.aws.amazon.com/wellarchitected/latest/responsible-ai-lens/raiuc03-bp03.html)

---

**Exam Tip**: Task 1.2 is about practical judgment — WHEN to use AI/ML, WHICH technique fits, and WHICH AWS service to pick. Focus on matching business problems to solutions, not memorizing service features.
