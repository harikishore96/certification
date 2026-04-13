# Task Statement 1.1: Explain Basic AI Concepts and Terminologies

> **Domain**: 1 — Fundamentals of AI and ML (20%)
> **Weight**: High — foundational knowledge tested throughout all domains
> **Source**: [AWS Certified AI Practitioner (AIF-C01) Exam Guide — Domain 1](https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain1.html)

---

## Official Exam Objectives

| # | Objective | Status |
|---|-----------|--------|
| 1 | Define basic AI terms (AI, ML, deep learning, neural networks, computer vision, NLP, model, algorithm, training, inferencing, bias, fairness, fit, LLMs) | ✅ Covered |
| 2 | Describe similarities and differences between AI, ML, GenAI, and deep learning | ✅ Covered |
| 3 | Describe various types of inferencing (batch, real-time) | ✅ Covered |
| 4 | Describe different types of data in AI models (labeled/unlabeled, tabular, time-series, image, text, structured/unstructured) | ✅ Covered |
| 5 | Describe supervised learning, unsupervised learning, and reinforcement learning | ✅ Covered |

---

## Objective 1: Define Basic AI Terms

### The AI Hierarchy

```
Artificial Intelligence (AI)
  └── Machine Learning (ML)
        └── Deep Learning (DL)
              └── Generative AI (GenAI)
```

### Term Definitions

| Term | Definition | Key Point |
|------|-----------|-----------|
| **Artificial Intelligence (AI)** | Computer systems performing tasks that typically require human intelligence | Broadest term; includes rule-based systems that don't learn |
| **Machine Learning (ML)** | Subset of AI where systems learn patterns from data without explicit programming | Always data-driven; improves with more data |
| **Deep Learning (DL)** | Subset of ML using multi-layered neural networks | Automatically discovers features; needs large data + GPUs |
| **Neural Network** | Computing system of interconnected nodes (neurons) in layers: input → hidden → output | Inspired by biological neurons; learns by adjusting weights |
| **Computer Vision** | AI field enabling computers to interpret visual information (images/video) | AWS: Amazon Rekognition |
| **Natural Language Processing (NLP)** | AI field focused on understanding and generating human language | AWS: Amazon Comprehend, Translate, Lex, Bedrock |
| **Model** | Mathematical representation of patterns learned from data | Has architecture, parameters (learned), and hyperparameters (set before training) |
| **Algorithm** | Step-by-step procedure for learning from data or solving a problem | Examples: XGBoost, K-Means, Random Forest, Transformers |
| **Training** | Teaching a model by feeding it data so it learns patterns | Uses historical data; adjusts parameters to minimize errors |
| **Inference (Inferencing)** | Using a trained model to make predictions on new, unseen data | Also called prediction/scoring; the deployment phase |
| **Large Language Model (LLM)** | Foundation model trained on massive text data to understand and generate language | Billions of parameters; Transformer-based; examples: Claude, Titan, Llama |

### Training vs. Inference

| Aspect | Training | Inference |
|--------|----------|-----------|
| **Purpose** | Learn patterns from data | Make predictions on new data |
| **Data** | Historical, labeled (supervised) | New, unseen |
| **Compute** | High (hours/days, GPUs) | Low (milliseconds) |
| **Frequency** | Periodic (retrain as needed) | Continuous (production) |
| **AWS Cost** | Per training hour | Per request or provisioned capacity |
| **AWS Service** | SageMaker Training Jobs, Bedrock fine-tuning | SageMaker Endpoints, Bedrock API, AI Services |

### Bias (Three Meanings — Context Matters!)

| Type | Definition | Example |
|------|-----------|---------|
| **Statistical Bias** (Model Performance) | Systematic error where model consistently under/over-predicts; high bias = underfitting | Linear model for non-linear data |
| **Societal Bias** (Fairness) | Unfair discrimination against certain groups in predictions | Loan model rejecting qualified applicants from certain demographics |
| **Neural Network Bias** (Technical) | Learnable parameter added to neuron inputs (y = wx + **b**) | Technical term in neural network math |

> **Exam Tip**: Read the question context carefully to determine which type of bias is being discussed.

### Fairness

- Ensuring AI systems treat all individuals and groups equitably
- Metrics: Demographic Parity, Equal Opportunity, Predictive Parity
- AWS Tool: **SageMaker Clarify** detects bias; **Model Cards** document fairness considerations
- Different fairness definitions can conflict with each other

### Fit (Model Fit)

| Fit Type | Training Performance | Test Performance | Cause | Fix |
|----------|---------------------|------------------|-------|-----|
| **Underfitting** (High Bias) | Poor | Poor | Model too simple | Increase complexity, add features, train longer |
| **Good Fit** (Optimal) | Good | Good | Balanced complexity | Goal state |
| **Overfitting** (High Variance) | Excellent | Poor | Model too complex, memorizes noise | Regularization, more data, dropout, early stopping |

---

## Objective 2: Similarities and Differences Between AI, ML, GenAI, and Deep Learning

### Visual Hierarchy

```
┌─────────────────────────────────────────────────────────────┐
│                    ARTIFICIAL INTELLIGENCE (AI)              │
│                  (Broadest - All intelligent systems)        │
│  ┌────────────────────────────────────────────────────────┐ │
│  │              MACHINE LEARNING (ML)                     │ │
│  │         (Learns patterns from data)                    │ │
│  │  ┌──────────────────────────────────────────────────┐ │ │
│  │  │           DEEP LEARNING (DL)                     │ │ │
│  │  │      (Multi-layer neural networks)               │ │ │
│  │  │  ┌────────────────────────────────────────────┐ │ │ │
│  │  │  │      GENERATIVE AI (GenAI)                 │ │ │ │
│  │  │  │  (Creates new content using DL)            │ │ │ │
│  │  │  └────────────────────────────────────────────┘ │ │ │
│  │  └──────────────────────────────────────────────────┘ │ │
│  └────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### Comparison Table

| Aspect | AI | ML | Deep Learning | Generative AI |
|--------|----|----|---------------|---------------|
| **Scope** | Broadest | Subset of AI | Subset of ML | Subset of DL |
| **Learning** | May or may not learn | Always learns from data | Learns hierarchical features | Learns to generate new data |
| **Data Needs** | Varies | Moderate (100s-1000s) | Large (1000s-millions) | Very large (millions-billions) |
| **Compute** | Low to high | Moderate | High (GPUs) | Very high (GPUs/TPUs) |
| **Output** | Decisions, actions | Predictions, classifications | Predictions, classifications | **New content** (text, images, code) |
| **Example** | Chess AI, expert systems | Spam filter, recommendations | Image recognition, speech | ChatGPT, DALL-E, Claude |
| **AWS** | All AI services | SageMaker, Forecast, Personalize | Rekognition, Comprehend, Transcribe | Bedrock, Nova, Q |

### Similarities

- All aim to solve problems requiring human-like intelligence
- All provide business value through automation, insights, and efficiency
- All available through AWS services
- ML, DL, and GenAI all learn from data and have training + inference phases

### Key Differences to Remember

| Distinction | Key Differentiator |
|-------------|-------------------|
| AI vs. ML | AI can be rule-based (no learning); ML always learns from data |
| ML vs. DL | ML can use simple algorithms (decision trees); DL always uses neural networks |
| DL vs. GenAI | DL primarily classifies/predicts; GenAI **creates new content** |
| Traditional ML vs. DL | ML needs manual feature engineering; DL auto-extracts features |

### Exam Decision Matrix

```
"Generate new content"           → Generative AI
"Create text/images/code"        → Generative AI
"Foundation models / prompts"    → Generative AI

"Complex patterns in images"     → Deep Learning
"Neural networks"                → Deep Learning
"Automatic feature extraction"   → Deep Learning

"Predict from historical data"   → Machine Learning
"Classification / regression"    → Machine Learning
"Smaller structured datasets"    → Machine Learning

"Intelligent automation"         → AI (general)
"Rule-based decisions"           → AI (not ML)
```

> **Exam Tip**: When multiple answers are technically correct, choose the **most specific** term. DL is more specific than ML, which is more specific than AI.

---

## Objective 3: Types of Inferencing

### Inference Types at a Glance

| Type | Latency | Best For | AWS Implementation |
|------|---------|----------|-------------------|
| **Real-Time** | < 1 second | Interactive apps, fraud detection, chatbots | SageMaker Endpoints, Bedrock On-Demand, AI Services |
| **Batch** | Hours | Bulk processing, scheduled jobs, millions of records | SageMaker Batch Transform |
| **Asynchronous** | Seconds-minutes | Large payloads, variable processing, unpredictable traffic | SageMaker Async Inference |
| **Serverless** | Seconds (cold start) | Intermittent traffic, dev/test, cost-sensitive | SageMaker Serverless, Bedrock On-Demand |
| **Streaming** | Milliseconds | IoT, clickstream, continuous data | Kinesis + SageMaker/Lambda |
| **Edge** | Milliseconds | Offline, IoT, low connectivity | SageMaker Edge Manager, IoT Greengrass |

### Decision Tree

```
Need predictions?
├── Immediate response? (< 1 sec)
│   ├── Consistent traffic → Real-Time (provisioned endpoint)
│   └── Sporadic traffic → Serverless
├── Can wait minutes/hours?
│   ├── Large dataset, scheduled → Batch
│   └── Variable size, queue-based → Asynchronous
├── Continuous data stream → Streaming
└── No internet / local device → Edge
```

### Key Exam Scenarios

| Scenario | Inference Type |
|----------|---------------|
| "Fraud detection during checkout" | Real-Time |
| "Process 10M images overnight" | Batch |
| "Analyze uploaded 500MB PDFs" | Asynchronous |
| "10 predictions per day, cost-sensitive" | Serverless |
| "IoT sensor data, continuous" | Streaming |
| "Autonomous vehicle, no connectivity" | Edge |

### Gotchas

- **Serverless has cold starts** (10-30 sec) — not for strict latency
- **Batch has no idle costs** — most cost-effective for large volumes
- **Asynchronous ≠ Batch** — async is near-real-time with queuing; batch is scheduled bulk
- **Real-time endpoints charge even when idle** — use auto-scaling or serverless for low traffic

---

## Objective 4: Types of Data in AI Models

### Data Classification Framework

```
By Label Status:
├── Labeled (has target/output) → Supervised Learning
└── Unlabeled (no target/output) → Unsupervised Learning

By Structure:
├── Structured (organized, schema-based)
│   ├── Tabular (rows/columns)
│   └── Time-Series (temporal sequence)
└── Unstructured (no predefined format) — ~80% of enterprise data
    ├── Text
    ├── Image
    ├── Audio
    └── Video
```

### Labeled vs. Unlabeled

| Aspect | Labeled | Unlabeled |
|--------|---------|-----------|
| **Definition** | Data with known outputs (ground truth) | Data without target variable |
| **Cost** | High (manual annotation) | Low (readily available) |
| **Learning Type** | Supervised | Unsupervised |
| **Availability** | Limited | Abundant |
| **Use Case** | Prediction, classification | Clustering, pattern discovery |
| **AWS Labeling** | SageMaker Ground Truth | N/A |

### Structured vs. Unstructured

| Aspect | Structured | Unstructured |
|--------|-----------|--------------|
| **Format** | Fixed schema (rows/columns) | No predefined format |
| **Storage** | Relational databases | S3, data lakes |
| **Volume** | ~20% of enterprise data | ~80% of enterprise data |
| **Processing** | Traditional ML algorithms | Deep learning |
| **Examples** | CSV, databases, spreadsheets | Text, images, audio, video |

### Data Types Detail

| Data Type | Structure | Format | Best Algorithm | AWS Service |
|-----------|-----------|--------|---------------|-------------|
| **Tabular** | Structured | Rows/columns (CSV, DB) | XGBoost, Random Forest | SageMaker, Fraud Detector |
| **Time-Series** | Structured | Temporal sequence | DeepAR, ARIMA | Amazon Forecast |
| **Image** | Unstructured | Pixels (JPG, PNG) | CNN (ResNet, YOLO) | Rekognition |
| **Text** | Unstructured | Natural language | Transformers, LLMs | Comprehend, Bedrock |
| **Audio** | Unstructured | Waveforms | RNN, spectrogram-based | Transcribe |
| **Video** | Unstructured | Sequential frames | CNN + RNN | Rekognition Video |

### Key Rules

- **Time-series must maintain temporal order** — never shuffle!
- **Structured ≠ always labeled** — you can cluster structured data (unsupervised)
- **Unstructured ≠ always unlabeled** — labeled images are unstructured + labeled
- **Text is unstructured** — even though it has grammar, it has no fixed schema
- **Images need deep learning** — traditional ML struggles with raw pixels

---

## Objective 5: Supervised, Unsupervised, and Reinforcement Learning

### Comparison Table

| Aspect | Supervised | Unsupervised | Reinforcement |
|--------|-----------|--------------|---------------|
| **Data** | Labeled (input + output) | Unlabeled (input only) | Environment interactions |
| **Goal** | Predict known outputs | Discover hidden patterns | Maximize cumulative reward |
| **Feedback** | Immediate (correct answer) | None | Delayed (rewards/penalties) |
| **Output** | Predictions, classifications | Clusters, patterns, anomalies | Actions, policies |
| **Evaluation** | Accuracy, Precision, Recall, F1 | Silhouette score, elbow method | Cumulative reward |
| **Prevalence** | ~80% of ML applications | Exploratory analysis | Specialized applications |

### Supervised Learning

**Definition**: Learning from labeled data (input-output pairs)

**Two Task Types**:

| Task | Output | Example | Metric |
|------|--------|---------|--------|
| **Classification** | Discrete category | Spam/Not Spam, Cat/Dog | Accuracy, F1, AUC-ROC |
| **Regression** | Continuous number | House price, temperature | RMSE, MAE, R² |

**Common Algorithms**: XGBoost, Random Forest, Linear/Logistic Regression, Neural Networks, SVM

**AWS Services**: SageMaker (XGBoost, Linear Learner), Rekognition Custom Labels, Comprehend Custom, Fraud Detector, Personalize

**When to Use**: ✅ Have labeled data, need predictions, clear input-output relationship
**When NOT to Use**: ❌ No labeled data, want to discover unknown patterns

### Unsupervised Learning

**Definition**: Learning from unlabeled data to find hidden patterns

**Three Task Types**:

| Task | Purpose | Example | Algorithm |
|------|---------|---------|-----------|
| **Clustering** | Group similar items | Customer segmentation | K-Means, DBSCAN |
| **Dimensionality Reduction** | Reduce features | Data compression, visualization | PCA, t-SNE |
| **Anomaly Detection** | Find outliers | Fraud detection, defect detection | Random Cut Forest, Isolation Forest |

**AWS Services**: SageMaker (K-Means, PCA, Random Cut Forest), Lookout for Metrics, Lookout for Vision

**When to Use**: ✅ No labels, want to explore data, find natural groupings, detect anomalies
**When NOT to Use**: ❌ Need specific predictions, have labeled data with clear targets

### Reinforcement Learning (RL)

**Definition**: Agent learns by interacting with an environment, receiving rewards/penalties

**Key Components**: Agent, Environment, State, Action, Reward, Policy

**Use Cases**: Game playing, robotics, autonomous vehicles, resource optimization, dynamic pricing

**AWS Services**: SageMaker RL, AWS DeepRacer, AWS RoboMaker

**RLHF (Reinforcement Learning from Human Feedback)**:
- Technique to fine-tune LLMs using human preferences
- Humans rank outputs → reward model trained → LLM optimized via RL
- Used to align models with human values (helpfulness, harmlessness)
- How Claude, ChatGPT, etc. are aligned

**When to Use**: ✅ Sequential decisions, long-term optimization, can simulate environment
**When NOT to Use**: ❌ Simple prediction, have labeled data, can't simulate safely

### Exam Decision Matrix

```
"Predict customer churn from historical data"        → Supervised (Classification)
"Predict house prices"                                → Supervised (Regression)
"Group customers into segments (no predefined groups)" → Unsupervised (Clustering)
"Find anomalies in network traffic"                   → Unsupervised (Anomaly Detection)
"Train robot to navigate warehouse"                   → Reinforcement Learning
"Optimize ad placement for maximum clicks"            → Reinforcement Learning
```

### Gotchas

1. **Anomaly detection can be both** — unsupervised (no labeled anomalies) or supervised (labeled anomalies)
2. **Clustering ≠ Classification** — clustering has no predefined groups; classification has predefined categories
3. **RL ≠ Supervised** — RL has delayed rewards, not immediate labels; learns through interaction
4. **Self-supervised learning** — creates labels from data itself (e.g., predict next word); used to pre-train foundation models
5. **Semi-supervised** — small labeled + large unlabeled data; useful when labeling is expensive

---

## Cross-Reference: Related Study Notes

| Topic | File |
|-------|------|
| AI/ML Basic Terminology (detailed) | [AI-ML-Basic-Terminology.md](../Fundamentals/AI-ML-Basic-Terminology.md) |
| AI vs ML vs GenAI vs DL (detailed comparison) | [AI-ML-GenAI-DeepLearning-Comparison.md](../Fundamentals/AI-ML-GenAI-DeepLearning-Comparison.md) |
| Learning Paradigms (detailed) | [Learning-Paradigms.md](../Fundamentals/Learning-Paradigms.md) |
| Types of Inferencing (detailed) | [Inference-Types.md](../Fundamentals/Inference-Types.md) |
| Data Types in AI (detailed) | [Data-Types-in-AI.md](../Fundamentals/Data-Types-in-AI.md) |
| AI/ML Applications and Value | [AI-ML-Applications-Value.md](../Fundamentals/AI-ML-Applications-Value.md) |
| Hyperparameters | [Hyperparameters.md](../Fundamentals/Hyperparameters.md) |
| ML Evaluation Metrics | [ML-Evaluation-Metrics.md](../ML-Fundamentals/ML-Evaluation-Metrics.md) |
| Flashcards (Domain 1 section) | [Flashcards.md](../Quick-Reference/Flashcards.md) |
| Cheat Sheet (Domain 1 section) | [Cheat-Sheet.md](../Quick-Reference/Cheat-Sheet.md) |

---

## Sample Questions

### Question 1: AI Term Definition

**Scenario**: A data scientist trains a model on 5 million historical transactions over 48 hours. The model is then deployed to check each new transaction in real-time (within 100ms) to determine if it's fraudulent.

**Question**: Which phase of the ML lifecycle is the real-time fraud check?

A) Training
B) Inference
C) Data preparation
D) Model evaluation

**Correct Answer**: B) Inference

**Why**: Using a trained model to make predictions on new data = inference. The 48-hour period was training. Real-time prediction on new transactions = inference.

**Exam Tip**: "Real-time predictions" + "new data" + "deployed model" → Inference.

---

### Question 2: AI Hierarchy

**Scenario**: A company uses a multi-layer neural network with millions of parameters trained on a large image dataset.

**Question**: Which term most accurately describes this approach?

A) Artificial Intelligence
B) Machine Learning
C) Deep Learning
D) Computer Vision

**Correct Answer**: C) Deep Learning

**Why**: Multi-layer neural networks + millions of parameters + large dataset = deep learning. While A and B are technically correct (DL is a subset of both), C is the most specific and accurate. D is the application domain, not the technique.

---

### Question 3: Similarities and Differences

**Scenario**: Match each scenario to the most specific applicable term:
1. Chess program using minimax algorithm (no learning)
2. Spam filter learning from labeled emails
3. System generating personalized email responses
4. Facial recognition in security cameras

**Question**: Which mapping is correct?

A) 1-AI, 2-ML, 3-GenAI, 4-DL
B) 1-ML, 2-ML, 3-DL, 4-DL
C) 1-AI, 2-DL, 3-GenAI, 4-ML
D) 1-GenAI, 2-ML, 3-AI, 4-DL

**Correct Answer**: A

**Why**: Chess with no learning = AI (rule-based). Spam filter with labeled data = ML (supervised). Generating email text = GenAI (creates new content). Facial recognition in images = DL (CNNs for complex visual patterns).

---

### Question 4: Bias and Fairness

**Scenario**: A healthcare model has 85% accuracy for patients over 65 but only 65% for patients under 40. It was trained primarily on elderly patient data.

**Question**: What issue is this, and which AWS service detects it?

A) Overfitting; SageMaker Model Monitor
B) Underfitting; SageMaker Autopilot
C) Bias; SageMaker Clarify
D) High variance; SageMaker Debugger

**Correct Answer**: C) Bias; SageMaker Clarify

**Why**: Systematic performance differences across demographic groups = bias (fairness issue). Unrepresentative training data (mostly elderly) caused biased predictions. SageMaker Clarify is specifically designed for bias detection.

---

### Question 5: Inference Types

**Scenario**: An e-commerce company needs:
1. Website recommendations for 5M daily visitors (immediate)
2. Weekly email campaign recommendations for 50M customers (scheduled)

**Question**: Which inference approach for each?

A) Real-time for both
B) Batch for both
C) Real-time for website, Batch for emails
D) Batch for website, Real-time for emails

**Correct Answer**: C

**Why**: Website = immediate response needed = real-time. Weekly emails = scheduled, bulk, non-urgent = batch (most cost-effective for 50M records).

---

### Question 6: Data Types

**Scenario**: A retail company has: (1) Customer database with age/income/purchases, (2) Product images, (3) Customer reviews, (4) Daily sales for 5 years.

**Question**: Correct classification?

A) 1-Tabular/Structured, 2-Image/Unstructured, 3-Text/Unstructured, 4-Time-Series/Structured
B) 1-Time-Series, 2-Structured, 3-Tabular, 4-Unstructured
C) All structured
D) All unstructured

**Correct Answer**: A

**Why**: Customer DB (rows/columns) = Tabular/Structured. Images = Unstructured. Reviews (natural language) = Text/Unstructured. Daily sales over time = Time-Series/Structured.

---

### Question 7: Learning Paradigms

**Scenario**: A company wants to group customers into segments based on purchasing behavior without predefined categories.

**Question**: Which approach?

A) Supervised learning with classification
B) Unsupervised learning with clustering
C) Reinforcement learning
D) Supervised learning with regression

**Correct Answer**: B

**Why**: "Without predefined categories" = no labels = unsupervised. "Group into segments" = clustering. K-Means on SageMaker would be appropriate.

---

## Exam Day Tips for Task 1.1

1. **Know the hierarchy**: AI ⊃ ML ⊃ DL ⊃ GenAI — choose the most specific term
2. **Training vs. Inference**: Training = learning phase; Inference = prediction phase
3. **Three types of bias**: Statistical (underfitting), Societal (fairness), Technical (neural network parameter)
4. **Overfitting vs. Underfitting**: Overfitting = too complex; Underfitting = too simple
5. **GenAI creates new content** — this is the key differentiator from traditional ML/DL
6. **Real-time vs. Batch**: Interactive = real-time; Scheduled bulk = batch
7. **Labeled → Supervised; Unlabeled → Unsupervised** (but structure and labels are independent)
8. **Time-series must maintain temporal order** — never shuffle
9. **~80% of enterprise data is unstructured** — text, images, audio, video
10. **SageMaker Clarify** = bias detection; **Model Cards** = documentation; **Model Monitor** = drift detection

---

**Sources**:
- [AIF-C01 Exam Guide — Domain 1](https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain1.html)
- [SageMaker Built-in Algorithms](https://docs.aws.amazon.com/sagemaker/latest/dg/algos.html)
- [SageMaker Clarify — Bias Detection](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-configure-processing-jobs.html)
- [Types of Algorithms — SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/algorithms-choose.html)
