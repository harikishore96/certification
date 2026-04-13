# AWS AI/ML Services Catalog - Coverage Tracker

## Generative AI Services

### Amazon Bedrock ✅ COMPLETE
- **Status**: Comprehensive documentation
- **File**: `Generative-AI-Services/Amazon-Bedrock.md`
- **Coverage**: All 8 sections complete with examples
- **Key Topics**: Foundation models, RAG, Agents, Guardrails, Fine-tuning, Knowledge Bases
- **Exam Relevance**: HIGH (Domains 2, 3, 5)

### Amazon Nova ✅ DOCUMENTED
- **Status**: Documented
- **File**: `Generative-AI-Services/Amazon-Nova.md`
- **Exam Relevance**: MEDIUM (Domain 2)

### Amazon Q ⏳ PENDING
- **Status**: Not yet documented
- **Key Topics**: AI assistant, code generation, business intelligence
- **Exam Relevance**: MEDIUM

## ML Platform Services

### Amazon SageMaker ✅ COMPLETE
- **Status**: Comprehensive documentation
- **File**: `ML-Services/Amazon-SageMaker.md`
- **Coverage**: All 8 sections with code examples
- **Key Topics**: Training, Inference, Autopilot, Clarify, Model Monitor, Pipelines
- **Exam Relevance**: HIGH (Domains 1, 3, 5)

### SageMaker Studio ✅ DOCUMENTED
- **File**: `ML-Services/Amazon-SageMaker-Studio.md`
- **Exam Relevance**: MEDIUM

### SageMaker Assets ✅ DOCUMENTED
- **File**: `ML-Services/Amazon-SageMaker-Assets.md`
- **Exam Relevance**: LOW-MEDIUM

### SageMaker Model Cards ✅ DOCUMENTED
- **File**: `ML-Services/Amazon-SageMaker-Model-Cards.md`
- **Exam Relevance**: MEDIUM (Responsible AI)

### SageMaker Components Pending
- **SageMaker Canvas** ⏳ - No-code ML
- **SageMaker Data Wrangler** ⏳ - Data preparation
- **SageMaker JumpStart** ⏳ - Pre-trained models
- **SageMaker Edge Manager** ⏳ - Edge deployment

## AI Services (Pre-built)

### Computer Vision
- **Amazon Rekognition** ⏳ - Image/video analysis, facial recognition
- **Amazon Lookout for Vision** ⏳ - Defect detection

### Natural Language Processing
- **Amazon Comprehend** ⏳ - Text analysis, sentiment, entities
- **Amazon Comprehend Medical** ⏳ - Medical text extraction
- **Amazon Translate** ⏳ - Language translation
- **Amazon Lex** ⏳ - Conversational interfaces

### Speech
- **Amazon Transcribe** ⏳ - Speech-to-text
- **Amazon Polly** ⏳ - Text-to-speech

### Search & Recommendations
- **Amazon Kendra** ⏳ - Intelligent search
- **Amazon Personalize** ⏳ - Recommendation engine

### Forecasting & Fraud
- **Amazon Forecast** ⏳ - Time-series forecasting
- **Amazon Fraud Detector** ⏳ - Fraud detection

### Document Processing
- **Amazon Textract** ⏳ - Document text/data extraction
- **Amazon Augmented AI (A2I)** ⏳ - Human review workflows

### Developer Tools
- **Amazon CodeWhisperer** ⏳ - AI code companion
- **Amazon DevOps Guru** ⏳ - Operational insights

## Supporting Services

### Data & Storage
- **Amazon S3** - Primary data storage (referenced throughout)
- **Amazon EFS** - File storage for ML
- **Amazon FSx for Lustre** - High-performance storage

### Compute & Orchestration
- **AWS Lambda** - Serverless compute (referenced)
- **AWS Step Functions** - Workflow orchestration (referenced)
- **Amazon EC2** - Training/inference instances

### Security & Governance
- **AWS IAM** - Access control (referenced)
- **AWS KMS** - Encryption (referenced)
- **AWS CloudTrail** - Audit logging (referenced)

### Monitoring & Analytics
- **Amazon CloudWatch** - Monitoring (referenced)
- **AWS Cost Explorer** - Cost analysis
- **AWS Budgets** - Cost management

## ML Fundamentals Topics

### Core Concepts ⏳ REVISION BACKLOG
- Model Fit, Bias, Variance
- Supervised Learning
- Unsupervised Learning
- Self-Supervised Learning
- Reinforcement Learning
- RLHF (Reinforcement Learning from Human Feedback)

### Evaluation Metrics ⏳ REVISION BACKLOG
- Confusion Matrix
- Precision, Recall, F1 Score
- Accuracy
- AUC-ROC
- MAE, MAPE, RMSE
- R² (R-Squared)
- Classification vs. Regression metrics

### Training Concepts
- **Hyperparameters** ✅ DOCUMENTED (`ML_Hyperparameters.md`)
  - Learning Rate
  - Batch Size
  - Number of Epochs
  - Regularization
- **Data Augmentation** ⏳
- **Feature Engineering** ⏳
- **Transfer Learning** ⏳
- **Overfitting/Underfitting** ⏳

### Responsible AI ⏳ NEEDS EXPANSION
- Bias Detection (covered in SageMaker Clarify)
- Explainability (covered in SageMaker Clarify)
- Fairness Principles
- Privacy & Data Protection
- Transparency & Governance
- Model Cards & Documentation

## Generative AI Concepts

### Foundation Models ✅ COVERED
- Pre-trained models (Bedrock documentation)
- Model families (Claude, Titan, Llama, etc.)
- Multimodal capabilities

### Prompt Engineering ✅ COVERED
- Prompt design techniques (Bedrock documentation)
- Few-shot learning
- Chain-of-thought prompting
- System prompts

### Model Customization ✅ COVERED
- Fine-tuning (Bedrock, SageMaker)
- Continued Pre-training (Bedrock)
- RLHF concepts

### Advanced Patterns ✅ COVERED
- RAG (Retrieval Augmented Generation)
- Agents and tool use
- Knowledge Bases
- Guardrails and safety

## Documentation Priority Matrix

### Priority 1 (High Exam Weight + Not Documented)
1. Amazon Comprehend (NLP - Domain 1)
2. Amazon Rekognition (Vision - Domain 1)
3. ML Evaluation Metrics (Domain 1)
4. Learning Paradigms (Domain 1)
5. Responsible AI Principles (Domain 4)

### Priority 2 (Medium Exam Weight)
1. Amazon Transcribe (Speech)
2. Amazon Lex (Conversational AI)
3. Amazon Kendra (Search)
4. Amazon Personalize (Recommendations)
5. Model Fit, Bias, Variance

### Priority 3 (Lower Exam Weight but Important)
1. Amazon Translate
2. Amazon Polly
3. Amazon Forecast
4. Amazon Fraud Detector
5. Data/Feature Augmentation

### Priority 4 (Nice to Have)
1. SageMaker Canvas
2. SageMaker JumpStart
3. Amazon Textract
4. Amazon A2I
5. Supporting AWS services details

## Coverage Statistics
- **Total Services Identified**: 30+
- **Fully Documented**: 6 (20%)
- **Partially Documented**: 0
- **Pending**: 24+ (80%)
- **Core ML Concepts**: 40% complete

## Quick Reference: Service Selection

### "I need to..." → Service Mapping
- **Generate text/images** → Bedrock
- **Train custom ML model** → SageMaker
- **Analyze images/videos** → Rekognition
- **Extract text from documents** → Textract
- **Understand text sentiment** → Comprehend
- **Translate languages** → Translate
- **Convert speech to text** → Transcribe
- **Convert text to speech** → Polly
- **Build chatbot** → Lex (simple) or Bedrock (advanced)
- **Search documents** → Kendra
- **Recommend products** → Personalize
- **Forecast time-series** → Forecast
- **Detect fraud** → Fraud Detector
- **No-code ML** → SageMaker Canvas
- **AutoML** → SageMaker Autopilot
