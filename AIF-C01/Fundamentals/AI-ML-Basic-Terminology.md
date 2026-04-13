# AI/ML Basic Terminology

## 1. Service/Topic Overview
- **Purpose**: Foundational AI/ML terminology and concepts essential for understanding AWS AI services and the AIF-C01 exam
- **Service Level**: Conceptual (applies across all AWS AI/ML services)
- **Key Use Cases**: 
  - Understanding AI/ML service documentation
  - Selecting appropriate AWS services for business problems
  - Communicating with data scientists and ML engineers
  - Evaluating model performance and capabilities
  - Making informed decisions about AI/ML implementations
- **Exam Weight**: HIGH - Foundational knowledge tested throughout all domains
- **Exam Domain**: Primarily Domain 1 (Fundamentals of AI and ML - 20%), but applies to all domains
- **Task Statements**: 
  - Task 1.1: Explain basic AI concepts and terminologies
  - Task 1.2: Identify practical use cases for AI
  - Task 2.1: Explain basic concepts of generative AI
  - Task 3.1: Describe design considerations for applications that use foundation models

## 2. Exam Keyword Mapping

### Trigger Words
- **AI/ML Basics**: "artificial intelligence", "machine learning", "deep learning", "neural network"
- **Model Lifecycle**: "training", "inference", "prediction", "deployment"
- **Model Quality**: "bias", "fairness", "overfitting", "underfitting", "accuracy"
- **Generative AI**: "LLM", "foundation model", "generative AI", "prompt"
- **Specialized AI**: "computer vision", "NLP", "natural language processing"

### Scenario Indicators
- "Identify patterns in data" → Machine Learning
- "Analyze images or videos" → Computer Vision
- "Understand or generate text" → NLP or LLMs
- "Model performs poorly on new data" → Overfitting/Bias issues
- "Automated decision-making" → AI/ML with inference

### Anti-patterns
- Don't confuse AI (broad) with ML (subset) with Deep Learning (subset of ML)
- Don't use "training" and "inference" interchangeably - they're distinct phases
- Don't assume all AI requires deep learning - many problems use simpler ML algorithms

## 3. Core Concepts

### The AI Hierarchy
```
Artificial Intelligence (AI)
└── Machine Learning (ML)
    └── Deep Learning (DL)
        └── Neural Networks
```

### ML Workflow
```
Data Collection → Data Preparation → Training → Evaluation → Deployment → Inference → Monitoring
```

### Model Lifecycle Phases
1. **Training Phase**: Model learns patterns from historical data
2. **Inference Phase**: Model makes predictions on new data
3. **Monitoring Phase**: Track performance and detect drift

## 4. Key Features & Components

### Core Terminology Definitions

#### **Artificial Intelligence (AI)**
- **Definition**: Computer systems that perform tasks typically requiring human intelligence
- **Capabilities**: Reasoning, learning, problem-solving, perception, language understanding
- **Types**: 
  - **Narrow AI**: Specialized for specific tasks (most current AI)
  - **General AI**: Human-level intelligence across domains (theoretical)
- **AWS Context**: Umbrella term for all AWS AI/ML services
- **Example**: Amazon Rekognition identifying objects in images, Alexa understanding voice commands

#### **Machine Learning (ML)**
- **Definition**: Subset of AI where systems learn patterns from data without explicit programming
- **Key Characteristic**: Improves performance through experience (data)
- **Learning Process**: Algorithm + Data → Model → Predictions
- **AWS Context**: SageMaker for custom ML, pre-built AI services use ML internally
- **Example**: Training a model to predict customer churn based on historical behavior data

#### **Deep Learning**
- **Definition**: Subset of ML using multi-layered neural networks to learn hierarchical representations
- **Key Characteristic**: Automatically discovers features from raw data
- **Requirements**: Large datasets, significant compute power (GPUs)
- **Best For**: Complex patterns in images, text, speech, video
- **AWS Context**: SageMaker with deep learning frameworks (TensorFlow, PyTorch), Bedrock foundation models
- **Example**: Image classification with convolutional neural networks, LLMs like Claude

#### **Neural Networks**
- **Definition**: Computing systems inspired by biological neural networks, composed of interconnected nodes (neurons)
- **Structure**: 
  - **Input Layer**: Receives raw data
  - **Hidden Layers**: Process and transform data
  - **Output Layer**: Produces predictions
- **Types**:
  - **Feedforward**: Data flows one direction (basic classification)
  - **Convolutional (CNN)**: Specialized for images
  - **Recurrent (RNN)**: Handles sequential data (time series, text)
  - **Transformer**: Modern architecture for NLP (basis of LLMs)
- **AWS Context**: Built into deep learning frameworks on SageMaker
- **Example**: A 3-layer network classifying emails as spam/not spam

#### **Computer Vision**
- **Definition**: AI field enabling computers to interpret and understand visual information from images/videos
- **Common Tasks**:
  - Object detection and recognition
  - Image classification
  - Facial recognition
  - Scene understanding
  - Image segmentation
- **AWS Services**: Amazon Rekognition, Lookout for Vision
- **Example**: Detecting defects in manufacturing products, identifying celebrities in photos

#### **Natural Language Processing (NLP)**
- **Definition**: AI field focused on interaction between computers and human language
- **Common Tasks**:
  - Sentiment analysis
  - Named entity recognition (NER)
  - Language translation
  - Text summarization
  - Question answering
- **AWS Services**: Amazon Comprehend, Translate, Lex, Bedrock
- **Example**: Analyzing customer reviews to determine sentiment, extracting key entities from medical records

#### **Model**
- **Definition**: Mathematical representation of patterns learned from training data
- **Components**: 
  - **Architecture**: Structure (e.g., neural network design)
  - **Parameters**: Learned values (weights, biases) - adjusted during training
  - **Hyperparameters**: Configuration settings set before training
- **Types**:
  - **Custom Models**: Trained on your specific data (SageMaker)
  - **Pre-trained Models**: Ready-to-use (AI services, SageMaker JumpStart)
  - **Foundation Models**: Large pre-trained models (Bedrock)
- **AWS Context**: Stored in SageMaker Model Registry, deployed to endpoints
- **Example**: A trained XGBoost model predicting house prices, Claude 3 Sonnet foundation model

#### **Algorithm**
- **Definition**: Step-by-step procedure or formula for solving a problem or learning from data
- **Categories**:
  - **Supervised Learning**: Linear Regression, Decision Trees, Random Forest, XGBoost, Neural Networks
  - **Unsupervised Learning**: K-Means Clustering, PCA, Anomaly Detection
  - **Deep Learning**: CNNs, RNNs, Transformers
- **AWS Context**: SageMaker built-in algorithms, custom algorithms via containers
- **Example**: Random Forest algorithm for classification, K-Means for customer segmentation

#### **Training**
- **Definition**: Process of teaching a model to recognize patterns by exposing it to labeled data
- **Process**:
  1. Feed training data to algorithm
  2. Algorithm adjusts model parameters to minimize errors
  3. Iterate until performance goals met
- **Key Concepts**:
  - **Training Data**: Historical data with known outcomes
  - **Epochs**: Complete passes through training dataset
  - **Loss Function**: Measures prediction errors
  - **Optimization**: Adjusting parameters to reduce loss
- **AWS Context**: SageMaker Training Jobs, Bedrock custom model fine-tuning
- **Cost**: Charged per compute hour (instance type × duration)
- **Example**: Training a fraud detection model on 1 million historical transactions

#### **Inference (Inferencing)**
- **Definition**: Using a trained model to make predictions on new, unseen data
- **Also Called**: Prediction, scoring
- **Types**:
  - **Real-time**: Immediate predictions (milliseconds) - SageMaker endpoints, Bedrock on-demand
  - **Batch**: Process large datasets offline - SageMaker Batch Transform
  - **Asynchronous**: Queue-based for variable workloads
- **AWS Context**: SageMaker endpoints, Bedrock API calls, Lambda with models
- **Cost**: Charged per inference request or provisioned capacity
- **Example**: Deployed model predicting if a credit card transaction is fraudulent in real-time

#### **Bias (Multiple Meanings)**

**1. Statistical Bias (Model Performance)**
- **Definition**: Systematic error where model consistently over/under-predicts
- **Cause**: Oversimplified model, insufficient features
- **Impact**: Poor accuracy on both training and test data
- **Related**: High bias = underfitting
- **Example**: Using linear model for non-linear relationship

**2. Societal Bias (Fairness)**
- **Definition**: Unfair discrimination against certain groups in model predictions
- **Causes**: 
  - Biased training data (historical discrimination)
  - Unrepresentative samples
  - Proxy features (correlated with protected attributes)
- **Protected Attributes**: Race, gender, age, religion, disability
- **AWS Tool**: SageMaker Clarify for bias detection
- **Example**: Loan approval model rejecting qualified applicants from certain demographics

**3. Neural Network Bias**
- **Definition**: Learnable parameter added to neuron inputs (technical term)
- **Purpose**: Shifts activation function for better fit
- **Example**: y = wx + b (b is the bias term)

#### **Fairness**
- **Definition**: Ensuring AI systems treat all individuals and groups equitably
- **Fairness Metrics**:
  - **Demographic Parity**: Equal positive prediction rates across groups
  - **Equal Opportunity**: Equal true positive rates across groups
  - **Predictive Parity**: Equal precision across groups
- **Challenges**: Different fairness definitions can conflict
- **AWS Context**: SageMaker Clarify detects bias, Model Cards document fairness considerations
- **Responsible AI**: Core principle (Domain 4)
- **Example**: Ensuring hiring model evaluates candidates fairly regardless of gender

#### **Fit (Model Fit)**

**Underfitting (High Bias)**
- **Definition**: Model too simple to capture data patterns
- **Symptoms**: Poor performance on both training and test data
- **Causes**: Insufficient model complexity, too few features, over-regularization
- **Solution**: Increase model complexity, add features, reduce regularization
- **Example**: Using linear regression for complex non-linear relationships

**Good Fit (Optimal)**
- **Definition**: Model captures true patterns without memorizing noise
- **Symptoms**: Good performance on both training and test data
- **Goal**: Balance between bias and variance
- **Example**: Model generalizes well to new data

**Overfitting (High Variance)**
- **Definition**: Model memorizes training data including noise, fails on new data
- **Symptoms**: Excellent training performance, poor test performance
- **Causes**: Model too complex, too many features, insufficient training data
- **Solutions**: 
  - More training data
  - Regularization (L1, L2, dropout)
  - Reduce model complexity
  - Cross-validation
  - Early stopping
- **AWS Context**: SageMaker Model Monitor detects performance degradation
- **Example**: Decision tree with unlimited depth memorizing every training example

#### **Large Language Models (LLMs)**
- **Definition**: Foundation models trained on massive text datasets to understand and generate human language
- **Characteristics**:
  - **Scale**: Billions to trillions of parameters
  - **Pre-training**: Trained on diverse internet text
  - **Generalization**: Perform multiple tasks without task-specific training
  - **Few-shot Learning**: Learn from examples in prompts
- **Capabilities**:
  - Text generation and completion
  - Question answering
  - Summarization
  - Translation
  - Code generation
  - Reasoning
- **Architecture**: Typically based on Transformer neural networks
- **AWS Context**: Available via Amazon Bedrock (Claude, Titan Text, Llama, Mistral)
- **Customization**: Prompt engineering, RAG, fine-tuning
- **Example**: Claude 3 Sonnet generating marketing copy, answering customer questions, analyzing documents

## 5. Exam Focus Areas

### Common Question Types

**Terminology Matching**
- "Which term describes a model that performs well on training data but poorly on new data?" → Overfitting
- "What phase uses a trained model to make predictions?" → Inference

**Scenario-Based Selection**
- "Company needs to analyze customer sentiment from reviews" → NLP (Amazon Comprehend)
- "Detect defects in manufacturing images" → Computer Vision (Amazon Rekognition)
- "Model shows different accuracy rates across demographic groups" → Bias/Fairness issue

**Concept Relationships**
- "What is the relationship between AI, ML, and Deep Learning?" → Hierarchy (AI ⊃ ML ⊃ DL)
- "Difference between training and inference?" → Training learns, inference predicts

### Gotchas

1. **Training vs. Inference Confusion**
   - Training = learning phase (historical data)
   - Inference = prediction phase (new data)
   - Different costs, compute requirements, latency needs

2. **Bias Multiple Meanings**
   - Statistical bias (underfitting)
   - Societal bias (fairness)
   - Neural network bias (technical parameter)
   - Context determines meaning

3. **AI ≠ ML ≠ Deep Learning**
   - AI is broadest (includes rule-based systems)
   - ML is subset (learns from data)
   - Deep Learning is subset of ML (neural networks)

4. **Overfitting vs. Underfitting**
   - Overfitting: Too complex, memorizes training data
   - Underfitting: Too simple, misses patterns
   - Both result in poor real-world performance

5. **LLMs are Foundation Models**
   - All LLMs are foundation models
   - Not all foundation models are LLMs (some are multimodal)
   - Foundation models = large, pre-trained, general-purpose

### Comparison Points

| Concept | Training | Inference |
|---------|----------|-----------|
| **Purpose** | Learn patterns | Make predictions |
| **Data** | Historical (labeled) | New (unlabeled) |
| **Compute** | High (hours/days) | Low (milliseconds) |
| **Frequency** | Periodic | Continuous |
| **AWS Cost** | Per training hour | Per request/hour |

| Model Fit | Training Performance | Test Performance | Solution |
|-----------|---------------------|------------------|----------|
| **Underfitting** | Poor | Poor | Increase complexity |
| **Good Fit** | Good | Good | Optimal |
| **Overfitting** | Excellent | Poor | Regularization, more data |

| AI Type | Scope | Learning | Examples |
|---------|-------|----------|----------|
| **Traditional AI** | Rule-based | No learning | Expert systems |
| **Machine Learning** | Pattern recognition | Learns from data | Regression, classification |
| **Deep Learning** | Complex patterns | Multi-layer learning | Image recognition, LLMs |

### Decision Trees

**✅ Use Machine Learning When:**
- Pattern recognition from data
- Predictions based on historical examples
- Continuous improvement with more data
- Complex relationships difficult to code manually

**❌ Don't Use ML When:**
- Simple rule-based logic suffices
- Insufficient training data
- Explainability is critical and model is black-box
- Deterministic outcomes required

**✅ Use Deep Learning When:**
- Large datasets available (thousands to millions of examples)
- Complex patterns (images, text, speech)
- High-dimensional data
- Computational resources available

**❌ Don't Use Deep Learning When:**
- Small datasets (< 1000 examples)
- Simple patterns (linear relationships)
- Limited compute budget
- Fast training required

## 6. Best Practices

### Understanding AI/ML Concepts

**For Exam Preparation**
1. **Master the hierarchy**: AI → ML → Deep Learning → Neural Networks
2. **Distinguish phases**: Training (learning) vs. Inference (predicting)
3. **Understand fit**: Underfitting (too simple) vs. Overfitting (too complex)
4. **Know bias types**: Statistical, societal, technical - context matters
5. **LLM fundamentals**: Large, pre-trained, general-purpose language models

**For Real-World Application**
1. **Start simple**: Try traditional ML before deep learning
2. **Data quality**: Better data > complex algorithms
3. **Monitor bias**: Use SageMaker Clarify for fairness checks
4. **Document models**: Use Model Cards for transparency
5. **Continuous monitoring**: Track model performance post-deployment

### Responsible AI Considerations

**Fairness**
- Test models across demographic groups
- Use SageMaker Clarify for bias detection
- Document fairness metrics in Model Cards
- Implement human review for high-stakes decisions

**Transparency**
- Document model purpose, limitations, performance
- Explain predictions when possible (SHAP values)
- Maintain audit trails (CloudTrail)
- Use Model Cards for stakeholder communication

**Privacy**
- Minimize PII in training data
- Use encryption (at rest and in transit)
- Implement data retention policies
- Consider federated learning for sensitive data

## 7. Hands-On Labs

### Lab 1: Understanding Model Fit with SageMaker

**Objective**: Observe underfitting and overfitting behavior

```python
import boto3
import sagemaker
from sagemaker import get_execution_role
from sagemaker.sklearn.estimator import SKLearn

# Setup
role = get_execution_role()
session = sagemaker.Session()

# Train models with different complexity
# Underfit: Simple linear model
underfit_estimator = SKLearn(
    entry_point='train_linear.py',
    role=role,
    instance_type='ml.m5.large',
    framework_version='1.0-1',
    hyperparameters={'max_depth': 1}  # Too simple
)

# Good fit: Balanced model
goodfit_estimator = SKLearn(
    entry_point='train_tree.py',
    role=role,
    instance_type='ml.m5.large',
    framework_version='1.0-1',
    hyperparameters={'max_depth': 5}  # Balanced
)

# Overfit: Complex model
overfit_estimator = SKLearn(
    entry_point='train_tree.py',
    role=role,
    instance_type='ml.m5.large',
    framework_version='1.0-1',
    hyperparameters={'max_depth': 50}  # Too complex
)

# Compare training vs. test accuracy
```

### Lab 2: Bias Detection with SageMaker Clarify

**Objective**: Detect bias in model predictions

```python
from sagemaker import clarify

# Configure bias analysis
bias_config = clarify.BiasConfig(
    label_values_or_threshold=[1],
    facet_name='gender',  # Protected attribute
    facet_values_or_threshold=[0]  # Reference group
)

# Create Clarify processor
clarify_processor = clarify.SageMakerClarifyProcessor(
    role=role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    sagemaker_session=session
)

# Run bias analysis
clarify_processor.run_bias(
    data_config=data_config,
    bias_config=bias_config,
    model_config=model_config,
    model_predicted_label_config=predictions_config
)

# Review bias metrics in output
```

### Lab 3: Training vs. Inference with Bedrock

**Objective**: Understand the difference between training and inference

```python
import boto3

bedrock_runtime = boto3.client('bedrock-runtime', region_name='us-east-1')

# INFERENCE: Using pre-trained model (no training needed)
prompt = "Explain the difference between training and inference in machine learning."

response = bedrock_runtime.invoke_model(
    modelId='anthropic.claude-3-sonnet-20240229-v1:0',
    body=json.dumps({
        "anthropic_version": "bedrock-2023-05-31",
        "max_tokens": 500,
        "messages": [
            {"role": "user", "content": prompt}
        ]
    })
)

# This is INFERENCE - model is already trained
# Training happened at Amazon/Anthropic before deployment
# You're just using the trained model to generate predictions

print(json.loads(response['body'].read())['content'][0]['text'])
```

### CLI Commands

```bash
# List SageMaker training jobs (training phase)
aws sagemaker list-training-jobs --max-results 10

# List SageMaker endpoints (inference phase)
aws sagemaker list-endpoints

# Describe model for details
aws sagemaker describe-model --model-name my-model

# List Bedrock foundation models (pre-trained, ready for inference)
aws bedrock list-foundation-models --region us-east-1

# Invoke Bedrock model (inference)
aws bedrock-runtime invoke-model \
    --model-id anthropic.claude-3-sonnet-20240229-v1:0 \
    --body '{"anthropic_version":"bedrock-2023-05-31","max_tokens":100,"messages":[{"role":"user","content":"Hello"}]}' \
    --region us-east-1 \
    output.json
```

## 8. Sample Questions

### Question 1: AI Hierarchy

**Scenario**: A data scientist is explaining different approaches to solve a business problem. They mention using a multi-layer neural network with millions of parameters trained on a large dataset of images.

**Question**: Which term most accurately describes this approach?

A) Artificial Intelligence  
B) Machine Learning  
C) Deep Learning  
D) Computer Vision

**Correct Answer**: C) Deep Learning

**Explanation**:
- **Why C is correct**: Multi-layer neural networks with millions of parameters trained on large datasets are the defining characteristics of deep learning. This is the most specific and accurate term.
- **Why A is wrong**: While technically correct (deep learning is a form of AI), this is too broad. The question asks for the "most accurate" term.
- **Why B is wrong**: Also technically correct but not specific enough. Deep learning is a subset of ML, and the question provides specific details pointing to deep learning.
- **Why D is wrong**: Computer vision is the application domain (working with images), not the approach/technique. You could use deep learning for computer vision, but the question asks about the approach itself.

**Exam Tip**: When multiple answers are technically correct, choose the most specific term that matches all the details provided.

---

### Question 2: Training vs. Inference

**Scenario**: A company has developed a fraud detection model using Amazon SageMaker. The model was trained on 5 million historical transactions over 48 hours. Now they need to check each new transaction in real-time (within 100ms) to determine if it's fraudulent.

**Question**: Which phase of the ML lifecycle is the company implementing for real-time fraud detection?

A) Training  
B) Inference  
C) Data preparation  
D) Model evaluation

**Correct Answer**: B) Inference

**Explanation**:
- **Why B is correct**: Using a trained model to make predictions on new data (new transactions) is inference. The real-time requirement (100ms) is a classic inference scenario. The model is already trained; now it's being used for predictions.
- **Why A is wrong**: Training already happened (the 48-hour period with 5 million historical transactions). Training is the learning phase, not the prediction phase.
- **Why C is wrong**: Data preparation happens before training. The scenario describes using the model after it's already trained.
- **Why D is wrong**: Model evaluation happens during/after training to assess performance. The scenario describes production use for real-time predictions.

**Exam Tip**: Keywords like "real-time predictions," "new data," "deployed model" indicate inference. Keywords like "historical data," "learning," "training dataset" indicate training.

---

### Question 3: Bias and Fairness

**Scenario**: A healthcare company trained a model to predict patient readmission risk. During testing, they discovered the model has 85% accuracy for patients over 65 but only 65% accuracy for patients under 40. The model was trained primarily on data from elderly patients.

**Question**: What issue is the company experiencing, and which AWS service can help detect it?

A) Overfitting; use Amazon SageMaker Model Monitor  
B) Underfitting; use Amazon SageMaker Autopilot  
C) Bias; use Amazon SageMaker Clarify  
D) High variance; use Amazon SageMaker Debugger

**Correct Answer**: C) Bias; use Amazon SageMaker Clarify

**Explanation**:
- **Why C is correct**: The model shows systematic performance differences across demographic groups (age), which is bias. The training data was unrepresentative (mostly elderly patients), leading to biased predictions. SageMaker Clarify is specifically designed to detect bias and fairness issues across different groups.
- **Why A is wrong**: Overfitting means good training performance but poor test performance overall. This scenario shows differential performance across groups, which is bias, not overfitting. Model Monitor tracks drift over time, not bias detection.
- **Why B is wrong**: Underfitting means poor performance across all data due to model simplicity. The model performs well on one group (85%), so it's not underfitting. Autopilot automates model building, not bias detection.
- **Why D is wrong**: High variance is another term for overfitting. Debugger helps with training issues like vanishing gradients, not fairness/bias detection.

**Exam Tip**: Performance differences across demographic groups = bias/fairness issue. SageMaker Clarify is the AWS tool for bias detection. Model Monitor is for drift detection over time.

---

**Exam Tip**: Master these foundational terms - they appear throughout all exam domains. Understanding the precise definitions and relationships between concepts is critical for selecting correct answers, especially when multiple options seem plausible.
