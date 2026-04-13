# AIF-C01 Flashcards

> 100+ flashcards organized by exam domain. Use for quick review and self-testing.
> Format: **Q** (front) → **A** (back)

---

## Domain 1: Fundamentals of AI and ML (20%)

### AI/ML Terminology

**Q1**: What is the difference between AI, ML, and Deep Learning?
**A**: AI is the broadest concept (machines mimicking human intelligence). ML is a subset of AI (learning from data without explicit programming). Deep Learning is a subset of ML (using neural networks with multiple layers).

---

**Q2**: What is the difference between a model parameter and a hyperparameter?
**A**: **Parameters** are learned during training (weights, biases). **Hyperparameters** are set before training and control the learning process (learning rate, batch size, epochs, regularization).

---

**Q3**: What is the difference between training and inference?
**A**: **Training** = teaching a model by feeding it data so it learns patterns. **Inference** = using a trained model to make predictions on new, unseen data.

---

**Q4**: What is a feature in ML?
**A**: An individual measurable property or characteristic of the data used as input to a model. Example: in a house price model, features include square footage, number of bedrooms, and location.

---

**Q5**: What is a label in ML?
**A**: The target variable or output that a supervised learning model is trained to predict. Example: the actual house price in a house price prediction model.

---

**Q6**: What is overfitting?
**A**: When a model learns the training data too well (including noise), performing great on training data but poorly on unseen data. Signs: high training accuracy, low validation accuracy. Fix: regularization, more data, dropout, early stopping.

---

**Q7**: What is underfitting?
**A**: When a model is too simple to capture patterns in the data, performing poorly on both training and test data. Fix: increase model complexity, add features, train longer, reduce regularization.

---

**Q8**: What is the bias-variance tradeoff?
**A**: **High bias** = underfitting (model too simple). **High variance** = overfitting (model too complex). The goal is to find the sweet spot that minimizes total error.

---

### Learning Paradigms

**Q9**: What is supervised learning?
**A**: Learning from labeled data (input-output pairs). The model learns to map inputs to known outputs. Examples: classification (spam detection), regression (price prediction).

---

**Q10**: What is unsupervised learning?
**A**: Learning from unlabeled data to find hidden patterns. No target variable. Examples: clustering (customer segmentation), dimensionality reduction (PCA), anomaly detection.

---

**Q11**: What is reinforcement learning (RL)?
**A**: An agent learns by interacting with an environment, receiving rewards or penalties for actions. Goal: maximize cumulative reward. Examples: game playing, robotics, recommendation systems.

---

**Q12**: What is RLHF (Reinforcement Learning from Human Feedback)?
**A**: A technique to fine-tune LLMs using human preferences. Humans rank model outputs → a reward model is trained on rankings → the LLM is fine-tuned using RL to maximize the reward model's score. Used to align models with human values.

---

**Q13**: What is self-supervised learning?
**A**: A form of unsupervised learning where the model generates its own labels from the data. Example: masking words in a sentence and predicting them (BERT), predicting the next token (GPT). Foundation models are typically trained this way.

---

**Q14**: What is semi-supervised learning?
**A**: Uses a small amount of labeled data combined with a large amount of unlabeled data. Useful when labeling is expensive. The model leverages unlabeled data to improve learning.

---

### Evaluation Metrics

**Q15**: What is a confusion matrix?
**A**: A table showing: **True Positives (TP)**, **True Negatives (TN)**, **False Positives (FP)**, **False Negatives (FN)**. Used to evaluate classification models.

---

**Q16**: What is Precision?
**A**: Of all predicted positives, how many were actually positive? **Precision = TP / (TP + FP)**. High precision = few false alarms. Important when false positives are costly (e.g., spam filtering).

---

**Q17**: What is Recall (Sensitivity)?
**A**: Of all actual positives, how many did the model correctly identify? **Recall = TP / (TP + FN)**. High recall = few missed positives. Important when false negatives are costly (e.g., disease detection).

---

**Q18**: What is the F1 Score?
**A**: Harmonic mean of Precision and Recall. **F1 = 2 × (Precision × Recall) / (Precision + Recall)**. Best when you need a balance between precision and recall, especially with imbalanced classes.

---

**Q19**: What is AUC-ROC?
**A**: **Area Under the Receiver Operating Characteristic Curve**. Measures a classifier's ability to distinguish between classes across all thresholds. AUC = 1.0 is perfect, AUC = 0.5 is random guessing.

---

**Q20**: What is RMSE?
**A**: **Root Mean Squared Error**. Measures average magnitude of prediction errors for regression. Penalizes large errors more than MAE. Lower is better. Formula: √(mean of squared differences between predicted and actual values).

---

**Q21**: What is R² (R-Squared)?
**A**: Proportion of variance in the target variable explained by the model. Range: 0 to 1 (can be negative for very bad models). R² = 1 means perfect prediction. Used for regression evaluation.

---

**Q22**: When should you prioritize Recall over Precision?
**A**: When **missing a positive case is very costly** — e.g., cancer detection, fraud detection, security threats. You'd rather have false alarms than miss real cases.

---

**Q23**: When should you prioritize Precision over Recall?
**A**: When **false positives are very costly** — e.g., email spam filtering (don't want to block legitimate emails), legal document classification, recommendation systems.

---

### Data Concepts

**Q24**: What is data augmentation?
**A**: Techniques to artificially increase training data size by creating modified versions of existing data. Examples: rotating/flipping images, adding noise, synonym replacement in text. Helps reduce overfitting.

---

**Q25**: What is feature engineering?
**A**: The process of creating, selecting, and transforming input features to improve model performance. Includes: normalization, encoding categorical variables, creating interaction features, handling missing values.

---

**Q26**: What are the main types of data in AI?
**A**: **Structured** (tabular, databases), **Unstructured** (text, images, audio, video), **Semi-structured** (JSON, XML). Most real-world data is unstructured. Deep learning excels at unstructured data.

---

**Q27**: What is data wrangling?
**A**: The process of cleaning, transforming, and preparing raw data for analysis/modeling. Includes: handling missing values, removing duplicates, correcting errors, standardizing formats, feature scaling.

---

### Neural Networks

**Q28**: What is a neural network?
**A**: A computing system inspired by biological neurons. Consists of layers: **input layer** → **hidden layers** → **output layer**. Each connection has a weight. Learns by adjusting weights through backpropagation.

---

**Q29**: What is an activation function?
**A**: A mathematical function applied to a neuron's output to introduce non-linearity. Common types: **ReLU** (most popular for hidden layers), **Sigmoid** (binary output), **Softmax** (multi-class output), **Tanh** (centered output).

---

**Q30**: What is a CNN (Convolutional Neural Network)?
**A**: Neural network specialized for grid-like data (images). Uses convolutional layers to detect features (edges, textures, objects). Key for: image classification, object detection, facial recognition.

---

**Q31**: What is an RNN (Recurrent Neural Network)?
**A**: Neural network designed for sequential data. Has memory of previous inputs. Used for: time series, text, speech. Variants: **LSTM** and **GRU** solve the vanishing gradient problem.

---

**Q32**: What is a Transformer?
**A**: Architecture using **self-attention mechanisms** to process all input tokens in parallel (not sequentially like RNNs). Foundation of modern LLMs (GPT, BERT, Claude). Key innovation: attention mechanism captures long-range dependencies efficiently.

---

---

## Domain 2: Fundamentals of Generative AI (24%)

### Foundation Models

**Q33**: What is a Foundation Model (FM)?
**A**: A large AI model pre-trained on broad data that can be adapted to many downstream tasks. Characteristics: trained on massive datasets, general-purpose, can be fine-tuned or used via prompting. Examples: Claude, Titan, Llama, Stable Diffusion.

---

**Q34**: What is the difference between fine-tuning and continued pre-training?
**A**: **Fine-tuning** = adapting a model with labeled, task-specific data (e.g., sentiment analysis examples). **Continued pre-training** = further training with unlabeled, domain-specific data (e.g., medical literature) to teach domain knowledge.

---

**Q35**: What is transfer learning?
**A**: Using a model trained on one task as the starting point for a different but related task. Foundation models are the ultimate example — pre-trained on general data, then adapted to specific tasks via fine-tuning or prompting.

---

**Q36**: What are embeddings?
**A**: Dense vector representations of data (text, images) in a continuous vector space. Similar items have similar embeddings (close in vector space). Used for: semantic search, RAG, recommendations, clustering.

---

**Q37**: What is tokenization?
**A**: Breaking text into smaller units (tokens) that a model can process. Types: **word-level**, **subword** (BPE — most common in LLMs), **character-level**. Token count affects cost and context window usage.

---

**Q38**: What is a diffusion model?
**A**: A generative model that learns to create data by reversing a noise-adding process. Starts with random noise → gradually denoises to produce output. Used for: image generation (Stable Diffusion, DALL-E), video generation.

---

**Q39**: What is positional encoding?
**A**: A technique in Transformers to inject information about token position in a sequence, since self-attention has no inherent notion of order. Without it, "The cat sat on the mat" and "The mat sat on the cat" would be identical.

---

**Q40**: What are the main FM modalities?
**A**: **Text** (generation, summarization, Q&A), **Image** (generation, editing), **Audio** (speech synthesis, music), **Video** (generation, editing), **Multimodal** (combining text + image + audio). Amazon Nova supports text, image, and video.

---

### Prompt Engineering

**Q41**: What is prompt engineering?
**A**: The practice of designing and optimizing input prompts to get desired outputs from foundation models. Techniques include: zero-shot, few-shot, chain-of-thought, system prompts, and structured formatting.

---

**Q42**: What is zero-shot prompting?
**A**: Asking a model to perform a task without providing any examples. Relies entirely on the model's pre-trained knowledge. Example: "Classify this review as positive or negative: [review text]"

---

**Q43**: What is few-shot prompting?
**A**: Providing a few examples (typically 2-5) in the prompt before the actual task. Helps the model understand the expected format and behavior. More effective than zero-shot for complex or specific tasks.

---

**Q44**: What is chain-of-thought (CoT) prompting?
**A**: Instructing the model to show its reasoning step-by-step before giving a final answer. Improves accuracy on complex reasoning tasks (math, logic). Example: "Think step by step..."

---

**Q45**: What is a system prompt?
**A**: Instructions that define the model's behavior, persona, constraints, and output format. Set at the beginning of a conversation. Example: "You are a helpful customer service agent. Always respond in formal English. Never discuss competitor products."

---

**Q46**: What is prompt injection?
**A**: A security attack where malicious instructions are embedded in user input to override the model's system prompt or intended behavior. Mitigation: input validation, Bedrock Guardrails, output filtering, prompt design best practices.

---

**Q47**: What is temperature in LLM inference?
**A**: Controls randomness of output. **Low temperature (0-0.3)** = more deterministic, focused, factual. **High temperature (0.7-1.0)** = more creative, diverse, random. Use low for factual tasks, high for creative tasks.

---

**Q48**: What is Top-P (nucleus sampling)?
**A**: Controls diversity by sampling from the smallest set of tokens whose cumulative probability exceeds P. **Top-P = 0.1** = only consider top 10% probability tokens (focused). **Top-P = 0.9** = consider top 90% (diverse).

---

**Q49**: What are hallucinations in generative AI?
**A**: When a model generates plausible-sounding but factually incorrect or fabricated information. Mitigation: RAG (ground responses in real data), lower temperature, explicit instructions, human review, Bedrock Guardrails.

---

**Q50**: What is the context window?
**A**: The maximum number of tokens a model can process in a single request (input + output combined). Larger context windows allow processing longer documents. Affects cost (more tokens = higher cost).

---

---

## Domain 3: Applications of Foundation Models (28%)

### Amazon Bedrock

**Q51**: What is Amazon Bedrock?
**A**: A fully managed AWS service to build generative AI applications using foundation models from multiple providers (Anthropic, Meta, Amazon, Cohere, Stability AI, Mistral). No infrastructure management. Access via API.

---

**Q52**: What are Bedrock Knowledge Bases?
**A**: Managed RAG implementation. You connect data sources (S3, web crawlers, etc.) → Bedrock automatically chunks, embeds, and stores in a vector database → at query time, retrieves relevant context and augments the FM prompt.

---

**Q53**: What are Bedrock Agents?
**A**: Enable FMs to autonomously execute multi-step tasks by connecting to APIs, databases, and knowledge bases. Agents plan, orchestrate, and execute actions. Example: a travel agent that searches flights, books hotels, and sends confirmations.

---

**Q54**: What are Bedrock Guardrails?
**A**: Configurable safeguards to filter harmful content, block sensitive topics, redact PII, and enforce responsible AI policies. Applied to both input and output. Can define denied topics, content filters, word filters, and sensitive info filters.

---

**Q55**: What is RAG (Retrieval Augmented Generation)?
**A**: A pattern that enhances FM responses by retrieving relevant information from external knowledge sources before generating a response. Flow: **Query → Retrieve from knowledge base → Augment prompt with context → Generate response**. Reduces hallucinations.

---

**Q56**: When should you use RAG vs. fine-tuning?
**A**: **RAG**: when you need up-to-date information, have frequently changing data, want to cite sources, or need factual grounding. **Fine-tuning**: when you need to change model behavior/style, teach domain-specific patterns, or optimize for a specific task format.

---

**Q57**: What is Bedrock Model Evaluation?
**A**: A feature to evaluate and compare FM performance using automatic metrics (accuracy, robustness, toxicity) or human evaluation. Helps select the best model for your use case. Supports built-in and custom evaluation datasets.

---

**Q58**: What is Provisioned Throughput in Bedrock?
**A**: Reserved model capacity for consistent, high-volume inference. Guarantees a specific number of model units. Required for custom/fine-tuned models. More cost-effective than on-demand for sustained high-throughput workloads.

---

**Q59**: What FM providers are available in Bedrock?
**A**: **Anthropic** (Claude), **Amazon** (Titan, Nova), **Meta** (Llama), **Cohere** (Command, Embed), **Stability AI** (Stable Diffusion), **Mistral** (Mistral, Mixtral), **AI21 Labs** (Jamba). Access varies by region.

---

**Q60**: What is Amazon Titan?
**A**: Amazon's family of foundation models available in Bedrock. Includes: **Titan Text** (text generation), **Titan Embeddings** (vector embeddings), **Titan Image Generator** (image creation), **Titan Multimodal Embeddings**.

---

### Amazon SageMaker

**Q61**: What is Amazon SageMaker?
**A**: A fully managed ML platform to build, train, and deploy ML models at scale. Covers the entire ML lifecycle: data preparation, training, tuning, deployment, monitoring. For teams that need full control over the ML pipeline.

---

**Q62**: What is SageMaker Autopilot?
**A**: AutoML capability that automatically explores data, selects algorithms, tunes hyperparameters, and trains models. Provides full visibility into the process. Best for: tabular data, users who want automated ML with transparency.

---

**Q63**: What is SageMaker Canvas?
**A**: No-code ML tool with a visual interface. Business analysts can build ML models without writing code. Supports: tabular, text, image, time-series data. Uses AutoML under the hood.

---

**Q64**: What is SageMaker Clarify?
**A**: Detects bias in data and models, and provides model explainability. Uses **SHAP values** for feature attribution. Can detect pre-training bias (data) and post-training bias (model predictions). Integrates with Model Monitor for ongoing monitoring.

---

**Q65**: What is SageMaker JumpStart?
**A**: A hub of pre-trained models, solution templates, and example notebooks. One-click deployment of foundation models (similar to Bedrock but with more infrastructure control). Supports fine-tuning on your own data.

---

**Q66**: What is SageMaker Model Monitor?
**A**: Monitors deployed models for data quality, model quality, bias drift, and feature attribution drift. Sends alerts when model performance degrades. Helps detect when retraining is needed.

---

**Q67**: When to use Bedrock vs. SageMaker?
**A**: **Bedrock**: managed FMs, no ML expertise needed, API-based, quick to deploy, generative AI focus. **SageMaker**: full ML lifecycle control, custom model training, any ML algorithm, need infrastructure control, traditional ML + deep learning.

---

**Q68**: What are SageMaker inference options?
**A**: **Real-time endpoints** (low-latency, always-on), **Serverless inference** (auto-scaling, pay-per-use), **Batch transform** (large datasets, offline), **Async inference** (large payloads, queued processing).

---

### AI Services (Pre-built)

**Q69**: What is Amazon Rekognition?
**A**: Pre-built computer vision service. Capabilities: object/scene detection, facial analysis, face comparison, celebrity recognition, text in images, content moderation, custom labels, PPE detection, video analysis.

---

**Q70**: What is Amazon Comprehend?
**A**: NLP service for text analysis. Capabilities: sentiment analysis, entity recognition, key phrase extraction, language detection, topic modeling, PII detection, custom classification, custom entity recognition.

---

**Q71**: What is Amazon Textract?
**A**: Document intelligence service. Extracts text, handwriting, tables, forms, and structured data from scanned documents. Supports: invoices, receipts, ID documents, lending packages. Goes beyond simple OCR with layout understanding.

---

**Q72**: What is Amazon Transcribe?
**A**: Automatic speech-to-text service. Features: real-time and batch transcription, custom vocabulary, speaker identification, automatic language detection, content redaction, subtitle generation. Variant: **Transcribe Medical** for clinical speech.

---

**Q73**: What is Amazon Polly?
**A**: Text-to-speech service. Converts text into lifelike speech. Supports: multiple languages, neural voices (more natural), SSML for speech control, lexicons for custom pronunciation. Use cases: accessibility, IVR systems, content narration.

---

**Q74**: What is Amazon Translate?
**A**: Neural machine translation service. Real-time and batch translation across 75+ languages. Features: custom terminology, formality settings, profanity masking, automatic language detection. Integrates with Comprehend for language detection.

---

**Q75**: What is Amazon Lex?
**A**: Conversational AI service for building chatbots and voice bots. Uses ASR (speech-to-text) and NLU (natural language understanding). Same technology as Alexa. Components: intents, utterances, slots, fulfillment (Lambda).

---

**Q76**: What is Amazon Kendra?
**A**: Intelligent enterprise search service powered by ML. Understands natural language queries. Connectors for 40+ data sources (S3, SharePoint, databases, etc.). Returns precise answers, not just document links. Supports incremental learning from user feedback.

---

**Q77**: What is Amazon Personalize?
**A**: Real-time personalization and recommendation service. No ML expertise required. Use cases: product recommendations, personalized search, customized marketing. Uses your interaction data (clicks, purchases, views) to train models.

---

**Q78**: What is Amazon Forecast?
**A**: Time-series forecasting service. Uses ML to generate accurate forecasts. No ML expertise needed. Use cases: demand planning, inventory management, resource planning, financial forecasting. Automatically selects best algorithm.

---

**Q79**: What is Amazon Fraud Detector?
**A**: Fully managed fraud detection service. Build custom fraud detection models using your data. Use cases: online payment fraud, fake account creation, account takeover, loyalty program abuse. Integrates with SageMaker for custom models.

---

**Q80**: What is Amazon Augmented AI (A2I)?
**A**: Human review workflow service. Routes low-confidence ML predictions to human reviewers. Integrates with Rekognition, Textract, and custom models. Use cases: content moderation review, document processing verification, compliance checks.

---

### Service Selection

**Q81**: "I need to extract text from scanned documents" → Which service?
**A**: **Amazon Textract**. It goes beyond OCR to understand document structure (tables, forms, key-value pairs). For simple text in images, Rekognition DetectText also works but Textract is purpose-built for documents.

---

**Q82**: "I need to build a chatbot" → Which service?
**A**: **Amazon Lex** for traditional intent-based chatbots. **Amazon Bedrock** (with Agents) for advanced conversational AI powered by foundation models. Lex = structured conversations; Bedrock = open-ended, generative conversations.

---

**Q83**: "I need to detect sentiment in customer reviews" → Which service?
**A**: **Amazon Comprehend**. It provides sentiment analysis (positive, negative, neutral, mixed) along with confidence scores. For real-time streaming, use Comprehend with Kinesis.

---

**Q84**: "I need to search internal company documents using natural language" → Which service?
**A**: **Amazon Kendra** for enterprise search. **Bedrock Knowledge Bases** for RAG-powered search with generative responses. Kendra = returns documents/answers; Bedrock KB = generates synthesized answers from documents.

---

---

## Domain 4: Guidelines for Responsible AI (14%)

**Q85**: What are the core principles of Responsible AI on AWS?
**A**: **Fairness** (no discrimination), **Explainability** (understand decisions), **Privacy & Security** (protect data), **Transparency** (disclose AI use), **Robustness** (reliable performance), **Governance** (oversight and accountability), **Safety** (prevent harm).

---

**Q86**: What is bias in AI/ML?
**A**: Systematic errors that produce unfair outcomes for certain groups. Types: **Selection bias** (non-representative training data), **Measurement bias** (inconsistent data collection), **Confirmation bias** (reinforcing existing beliefs), **Algorithmic bias** (model amplifies data biases).

---

**Q87**: How does SageMaker Clarify detect bias?
**A**: **Pre-training bias metrics**: analyze training data before model training (e.g., Class Imbalance, Difference in Proportions of Labels). **Post-training bias metrics**: analyze model predictions (e.g., Disparate Impact, Demographic Parity). Uses **SHAP values** for explainability.

---

**Q88**: What are Bedrock Guardrails content filters?
**A**: Configurable filters for: **Hate** (discrimination), **Insults** (demeaning language), **Sexual** (explicit content), **Violence** (violent content), **Misconduct** (illegal activities), **Prompt attacks** (injection attempts). Each has adjustable strength levels (None, Low, Medium, High).

---

**Q89**: What is model explainability?
**A**: The ability to understand why a model made a specific prediction. Methods: **SHAP values** (feature importance), **attention visualization** (what the model focuses on), **model cards** (documentation). AWS tools: SageMaker Clarify, Model Cards.

---

**Q90**: What are SageMaker Model Cards?
**A**: Documentation framework for ML models. Records: model purpose, training details, evaluation results, intended use, limitations, ethical considerations, bias metrics. Supports responsible AI governance and model transparency.

---

**Q91**: What is the AWS AI Service Cards program?
**A**: AWS-published transparency documents for AI services (Rekognition, Textract, etc.). Each card describes: intended use cases, design choices, performance across conditions, limitations, responsible deployment guidance.

---

**Q92**: What is PII detection and redaction in AWS AI?
**A**: **Comprehend**: detects PII entities in text (names, SSN, addresses). **Bedrock Guardrails**: can redact PII in FM inputs/outputs. **Textract**: can detect PII in documents. **Transcribe**: can redact PII from transcriptions. **Macie**: detects PII in S3.

---

---

## Domain 5: Security, Compliance, and Governance (14%)

**Q93**: How is data encrypted in Bedrock?
**A**: **At rest**: encrypted with AWS KMS (customer-managed or AWS-managed keys). **In transit**: TLS 1.2+. **Model customization data**: encrypted and isolated per customer. **No customer data is used to train base models** (unless explicitly opted in).

---

**Q94**: What IAM controls apply to Bedrock?
**A**: IAM policies control: model access (which FMs users can invoke), feature access (fine-tuning, knowledge bases, agents), resource-level permissions, service-linked roles, VPC endpoints for private connectivity, SCPs for organizational controls.

---

**Q95**: How do you secure SageMaker?
**A**: **Network**: VPC, private subnets, VPC endpoints, security groups. **Data**: KMS encryption at rest and in transit, S3 bucket policies. **Access**: IAM roles, resource policies. **Audit**: CloudTrail logging, CloudWatch monitoring. **Isolation**: inter-container traffic encryption.

---

**Q96**: What is the AWS Shared Responsibility Model for AI?
**A**: **AWS responsibility**: security OF the cloud (infrastructure, managed service security, model hosting). **Customer responsibility**: security IN the cloud (data, IAM, prompt design, content filtering, compliance, responsible AI implementation).

---

**Q97**: What is AWS CloudTrail's role in AI governance?
**A**: Logs all API calls to AWS AI services. Provides audit trail for: who accessed which models, when inference was called, what configurations were changed. Essential for compliance, security monitoring, and incident investigation.

---

**Q98**: What is the AI services opt-out policy?
**A**: An AWS Organizations policy that prevents AWS from using your content processed by AI services to improve those services. Applied at the organization level via SCPs. Ensures data privacy compliance.

---

**Q99**: How do you implement model governance on AWS?
**A**: **SageMaker Model Registry** (version control, approval workflows), **Model Cards** (documentation), **Model Monitor** (drift detection), **CloudTrail** (audit logging), **IAM** (access control), **SageMaker Pipelines** (reproducible ML workflows).

---

**Q100**: What compliance frameworks apply to AWS AI services?
**A**: SOC 1/2/3, ISO 27001, HIPAA (BAA required), PCI DSS, FedRAMP, GDPR, CCPA. Specific services may have additional certifications. Check AWS Compliance Programs page and service-specific documentation for current status.

---

### Bonus: Quick Decision Cards

**Q101**: RAG vs. Fine-tuning vs. Continued Pre-training — when to use each?
**A**:
- **RAG**: Need current/dynamic data, want source citations, data changes frequently
- **Fine-tuning**: Need to change model behavior/style, have labeled task-specific data
- **Continued Pre-training**: Need domain knowledge (medical, legal), have large unlabeled domain corpus

---

**Q102**: Bedrock vs. SageMaker vs. AI Services — how to choose?
**A**:
- **AI Services** (Rekognition, Comprehend, etc.): Pre-built, no ML knowledge needed, specific tasks, API call
- **Bedrock**: Generative AI, foundation models, managed, moderate customization
- **SageMaker**: Full ML lifecycle, custom models, maximum control, ML expertise required

---

**Q103**: Real-time vs. Batch vs. Serverless vs. Async inference?
**A**:
- **Real-time**: Low latency, always-on, consistent traffic
- **Serverless**: Intermittent traffic, auto-scaling, cost-sensitive
- **Batch**: Large datasets, offline processing, no latency requirement
- **Async**: Large payloads (>6MB), long processing time, queued

---

**Q104**: When to use Kendra vs. Bedrock Knowledge Bases?
**A**:
- **Kendra**: Enterprise search, return ranked documents/passages, FAQ matching, 40+ connectors
- **Bedrock Knowledge Bases**: RAG, generate synthesized answers from documents, conversational, integrated with FMs

---

**Q105**: Temperature 0 vs. Temperature 1?
**A**:
- **Temperature 0**: Most deterministic, factual, consistent. Use for: code generation, data extraction, factual Q&A
- **Temperature 1**: Most creative, diverse, variable. Use for: creative writing, brainstorming, storytelling

---

> **Exam Tip**: The AIF-C01 focuses heavily on knowing WHEN to use each service and understanding the WHY behind architectural decisions. Memorize the service selection decision tree!

---

**Sources**:
- [Amazon Bedrock Foundation Models](https://docs.aws.amazon.com/bedrock/latest/userguide/foundation-models-reference.html)
- [SageMaker Clarify - Bias Detection & Explainability](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-configure-processing-jobs.html)
- [AWS AI Services Overview](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/machine-learning.html)
