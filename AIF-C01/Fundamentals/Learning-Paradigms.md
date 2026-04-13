# Learning Paradigms - Supervised, Unsupervised, and Reinforcement Learning

## 1. Service/Topic Overview
- **Purpose**: Understanding the three main machine learning paradigms and when to apply each
- **Service Level**: Conceptual framework applicable across all AWS AI/ML services
- **Key Use Cases**: 
  - Selecting appropriate learning approach for business problems
  - Choosing correct AWS services and algorithms
  - Understanding data requirements for each paradigm
  - Designing ML solutions based on available data
- **Exam Weight**: HIGH - Fundamental concept tested across all domains
- **Exam Domain**: 
  - Domain 1: Fundamentals of AI and ML (20%)
  - Domain 3: Applications of Foundation Models (28%)
- **Task Statements**: 
  - Task 1.1: Explain basic AI concepts and terminologies
  - Task 1.2: Identify practical use cases for AI
  - Task 1.3: Describe the ML development lifecycle

## 2. Exam Keyword Mapping

### Trigger Words
- **Supervised**: "labeled data", "predict", "classify", "ground truth", "target variable", "historical outcomes"
- **Unsupervised**: "unlabeled data", "patterns", "clusters", "segments", "no labels", "discover groups"
- **Reinforcement**: "rewards", "actions", "environment", "trial and error", "sequential decisions", "optimize behavior"

### Scenario Indicators
- "Predict customer churn using historical data" → Supervised
- "Group customers into segments" → Unsupervised
- "Train robot to navigate maze" → Reinforcement
- "Classify emails as spam/not spam" → Supervised
- "Detect anomalies in network traffic" → Unsupervised
- "Optimize ad placement for clicks" → Reinforcement
- "Find natural groupings in data" → Unsupervised

### Anti-patterns
- Don't use supervised when you have no labels
- Don't use unsupervised when you need specific predictions
- Don't use reinforcement for simple classification tasks
- Don't confuse clustering (unsupervised) with classification (supervised)

## 3. Core Concepts

### Learning Paradigm Hierarchy

```
Machine Learning
├── Supervised Learning (Labeled Data)
│   ├── Classification (Discrete outputs)
│   └── Regression (Continuous outputs)
├── Unsupervised Learning (Unlabeled Data)
│   ├── Clustering (Group similar items)
│   ├── Dimensionality Reduction (Reduce features)
│   └── Anomaly Detection (Find outliers)
└── Reinforcement Learning (Reward-based)
    ├── Model-Free (Q-Learning, Policy Gradient)
    └── Model-Based (Plan with environment model)
```

### Key Differences at a Glance

| Aspect | Supervised | Unsupervised | Reinforcement |
|--------|------------|--------------|---------------|
| **Data** | Labeled (input + output) | Unlabeled (input only) | Environment interactions |
| **Goal** | Predict outputs | Discover patterns | Maximize rewards |
| **Feedback** | Correct answers provided | No feedback | Delayed rewards |
| **Example** | Spam detection | Customer segmentation | Game playing |
| **Evaluation** | Accuracy, precision | Silhouette score | Cumulative reward |

## 4. Key Features & Components

### 1. Supervised Learning

#### Definition
Learning from labeled data where each input has a corresponding correct output (ground truth).

#### How It Works
```
Training Phase:
Input (Features) + Label (Target) → Algorithm → Model

Inference Phase:
New Input → Trained Model → Predicted Output
```

#### Characteristics
- **Data Requirement**: Labeled dataset (input-output pairs)
- **Learning Process**: Minimize prediction error on training data
- **Feedback**: Immediate (correct answer known)
- **Goal**: Learn mapping from inputs to outputs
- **Evaluation**: Compare predictions to actual labels

#### Types of Supervised Learning

**1. Classification (Discrete Outputs)**
- Predict categorical labels
- Binary: Yes/No, Spam/Not Spam, Fraud/Legitimate
- Multi-class: Cat/Dog/Bird, Product categories
- Multi-label: Multiple tags per item

**Examples**:
- Email spam detection
- Image classification
- Sentiment analysis (positive/negative/neutral)
- Disease diagnosis
- Credit approval

**2. Regression (Continuous Outputs)**
- Predict numerical values
- Output is a real number

**Examples**:
- House price prediction
- Sales forecasting
- Temperature prediction
- Stock price estimation
- Customer lifetime value

#### Common Algorithms

**Classification**:
- Logistic Regression
- Decision Trees
- Random Forest
- XGBoost
- Support Vector Machines (SVM)
- Neural Networks

**Regression**:
- Linear Regression
- Polynomial Regression
- Ridge/Lasso Regression
- XGBoost
- Neural Networks

#### AWS Services & Algorithms

**SageMaker Built-in Algorithms**:
- **XGBoost**: Classification and regression (most popular)
- **Linear Learner**: Linear models for classification/regression
- **Factorization Machines**: Sparse data classification
- **K-Nearest Neighbors (KNN)**: Classification and regression
- **Image Classification**: CNN-based image classifier
- **Object Detection**: Detect objects in images

**AI Services (Pre-built Supervised Models)**:
- **Rekognition Custom Labels**: Custom image classification
- **Comprehend Custom**: Custom text classification
- **Fraud Detector**: Fraud detection models
- **Personalize**: Recommendation systems

#### When to Use Supervised Learning

✅ **Use When**:
- You have labeled historical data
- Clear input-output relationship exists
- Need to predict specific outcomes
- Can define success metrics (accuracy, precision)
- Examples: fraud detection, price prediction, image classification

❌ **Don't Use When**:
- No labeled data available (expensive to create)
- Don't know what to predict
- Want to discover unknown patterns
- Need exploratory analysis

#### Example Workflow

```python
# Supervised Learning: Fraud Detection
import boto3
import sagemaker
from sagemaker import image_uris

# 1. Prepare labeled data
# Format: features + label (fraud/legitimate)
train_data = """
amount,location,time,merchant,label
100,US,10am,Amazon,0
5000,Unknown,3am,Unknown,1
50,US,2pm,Walmart,0
"""

# 2. Train model with XGBoost
container = image_uris.retrieve('xgboost', region, '1.5-1')

xgb = sagemaker.estimator.Estimator(
    container,
    role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    hyperparameters={
        'objective': 'binary:logistic',  # Classification
        'num_round': 100
    }
)

xgb.fit({'train': 's3://bucket/train.csv'})

# 3. Deploy and predict
predictor = xgb.deploy(initial_instance_count=1, instance_type='ml.m5.xlarge')

# 4. Make predictions on new transactions
new_transaction = [[5500, 'Unknown', '2am', 'Unknown']]
prediction = predictor.predict(new_transaction)  # 0 or 1
```

#### Advantages
- High accuracy when sufficient labeled data available
- Clear evaluation metrics
- Well-understood algorithms
- Interpretable results

#### Challenges
- Requires labeled data (expensive, time-consuming)
- May not generalize to unseen patterns
- Prone to overfitting
- Biased if training data is biased

---

### 2. Unsupervised Learning

#### Definition
Learning from unlabeled data to discover hidden patterns, structures, or relationships without predefined outputs.

#### How It Works
```
Input (Features only, no labels) → Algorithm → Discovered Patterns/Groups
```

#### Characteristics
- **Data Requirement**: Unlabeled dataset (inputs only)
- **Learning Process**: Find inherent structure in data
- **Feedback**: No feedback (no correct answers)
- **Goal**: Discover hidden patterns, groupings, or representations
- **Evaluation**: Domain knowledge, visualization, internal metrics

#### Types of Unsupervised Learning

**1. Clustering**
- Group similar data points together
- Points in same cluster are similar
- Points in different clusters are dissimilar

**Examples**:
- Customer segmentation (group by behavior)
- Document categorization
- Image segmentation
- Anomaly detection (outliers don't fit clusters)

**Algorithms**:
- K-Means (most common)
- Hierarchical Clustering
- DBSCAN
- Gaussian Mixture Models

**2. Dimensionality Reduction**
- Reduce number of features while preserving information
- Compress data, remove noise
- Visualization (reduce to 2D/3D)

**Examples**:
- Feature extraction
- Data compression
- Visualization of high-dimensional data
- Noise reduction

**Algorithms**:
- Principal Component Analysis (PCA)
- t-SNE
- Autoencoders

**3. Anomaly Detection**
- Identify unusual patterns that don't conform to expected behavior
- Outlier detection

**Examples**:
- Fraud detection (unusual transactions)
- Network intrusion detection
- Manufacturing defect detection
- System health monitoring

**Algorithms**:
- Isolation Forest
- Random Cut Forest (RCF)
- One-Class SVM
- Autoencoders

#### AWS Services & Algorithms

**SageMaker Built-in Algorithms**:
- **K-Means**: Clustering algorithm
- **PCA**: Dimensionality reduction
- **Random Cut Forest (RCF)**: Anomaly detection

**AI Services**:
- **Lookout for Metrics**: Anomaly detection in metrics
- **Lookout for Equipment**: Anomaly detection in sensor data
- **Lookout for Vision**: Defect detection in images

**Foundation Models**:
- **Bedrock**: Pre-trained on unlabeled text (self-supervised)

#### When to Use Unsupervised Learning

✅ **Use When**:
- No labeled data available
- Want to explore data structure
- Need to find natural groupings
- Discover hidden patterns
- Reduce data dimensionality
- Detect anomalies without known examples
- Examples: customer segmentation, anomaly detection

❌ **Don't Use When**:
- Need specific predictions
- Have labeled data and clear targets
- Require precise accuracy metrics
- Need interpretable results for specific outcomes

#### Example Workflow

```python
# Unsupervised Learning: Customer Segmentation
import boto3
import sagemaker
from sagemaker import image_uris

# 1. Prepare unlabeled data (no target variable)
# Format: features only (age, income, purchases, etc.)
customer_data = """
age,income,purchases,avg_order_value
25,50000,10,75
45,80000,25,120
30,60000,15,90
"""

# 2. Train K-Means clustering
kmeans_image = image_uris.retrieve('kmeans', region)

kmeans = sagemaker.estimator.Estimator(
    kmeans_image,
    role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    hyperparameters={
        'k': 3,  # Number of clusters
        'feature_dim': 4  # Number of features
    }
)

kmeans.fit({'train': 's3://bucket/customers.csv'})

# 3. Assign customers to clusters
predictor = kmeans.deploy(initial_instance_count=1, instance_type='ml.m5.xlarge')

# 4. Get cluster assignment for new customer
new_customer = [[35, 70000, 20, 100]]
cluster = predictor.predict(new_customer)  # Returns cluster ID (0, 1, or 2)

# 5. Analyze clusters to understand segments
# Cluster 0: Young, low income, few purchases
# Cluster 1: Middle-aged, high income, many purchases
# Cluster 2: Mixed demographics, moderate activity
```

#### Advantages
- No labeling required (cost-effective)
- Discover unknown patterns
- Exploratory data analysis
- Works with abundant unlabeled data

#### Challenges
- No clear success metric
- Results can be subjective
- Difficult to validate
- Requires domain expertise to interpret

---

### 3. Reinforcement Learning (RL)

#### Definition
Learning through interaction with an environment by taking actions and receiving rewards or penalties, optimizing long-term cumulative reward.

#### How It Works
```
Agent observes State → Takes Action → Receives Reward → Environment transitions to new State
                         ↑                                              ↓
                         └──────────────── Learn Policy ───────────────┘
```

#### Key Components
- **Agent**: The learner/decision maker
- **Environment**: The world the agent interacts with
- **State**: Current situation of the agent
- **Action**: Choices available to the agent
- **Reward**: Feedback signal (positive or negative)
- **Policy**: Strategy for choosing actions (what to do in each state)
- **Value Function**: Expected long-term reward from a state

#### Characteristics
- **Data Requirement**: Environment to interact with
- **Learning Process**: Trial and error, exploration vs exploitation
- **Feedback**: Delayed rewards (actions have long-term consequences)
- **Goal**: Learn optimal policy to maximize cumulative reward
- **Evaluation**: Total reward over time

#### Types of Reinforcement Learning

**1. Model-Free RL**
- Learn directly from experience
- Don't build model of environment
- **Q-Learning**: Learn action-value function
- **Policy Gradient**: Directly optimize policy

**2. Model-Based RL**
- Build model of environment
- Plan using the model
- More sample efficient

#### Common Algorithms
- Q-Learning
- Deep Q-Networks (DQN)
- Policy Gradient
- Actor-Critic
- Proximal Policy Optimization (PPO)
- REINFORCE

#### Use Cases

**Game Playing**:
- Chess, Go, video games
- AlphaGo, OpenAI Five

**Robotics**:
- Robot navigation
- Manipulation tasks
- Autonomous vehicles

**Resource Management**:
- Traffic light optimization
- Energy management
- Inventory optimization

**Personalization**:
- Ad placement optimization
- Content recommendation
- Dynamic pricing

**Finance**:
- Trading strategies
- Portfolio management

#### AWS Services

**Amazon SageMaker RL**:
- Managed reinforcement learning
- Pre-built RL algorithms
- Integration with simulation environments
- Supports TensorFlow, PyTorch, Ray RLlib

**AWS RoboMaker**:
- Robot simulation and testing
- RL for robotics applications

**AWS DeepRacer**:
- Autonomous racing car
- Learn RL concepts hands-on
- Uses PPO algorithm

#### When to Use Reinforcement Learning

✅ **Use When**:
- Sequential decision-making required
- Actions have long-term consequences
- Can simulate environment
- Need to optimize behavior over time
- Trial and error is acceptable
- Examples: game AI, robotics, resource optimization

❌ **Don't Use When**:
- Simple prediction task (use supervised)
- No clear reward signal
- Can't simulate environment safely
- Need immediate results
- Have labeled data for supervised learning

#### Example Workflow

```python
# Reinforcement Learning: AWS DeepRacer
import boto3
import sagemaker

# 1. Define environment (race track)
# 2. Define reward function
def reward_function(params):
    """
    Reward function for DeepRacer
    Encourages staying on track and going fast
    """
    # Read input parameters
    track_width = params['track_width']
    distance_from_center = params['distance_from_center']
    speed = params['speed']
    
    # Calculate reward
    # Reward for staying on track
    marker_1 = 0.1 * track_width
    marker_2 = 0.25 * track_width
    
    if distance_from_center <= marker_1:
        reward = 1.0
    elif distance_from_center <= marker_2:
        reward = 0.5
    else:
        reward = 0.01  # Likely off track
    
    # Bonus for speed
    if speed > 2.0:
        reward *= 1.5
    
    return float(reward)

# 3. Train agent using PPO algorithm
# Agent learns to drive by trial and error
# Maximizes cumulative reward over episodes

# 4. Agent learns optimal policy:
# - Stay near center of track (higher reward)
# - Go fast (bonus reward)
# - Avoid going off track (low reward)
```

#### Reinforcement Learning from Human Feedback (RLHF)

**Definition**: Special RL technique used to fine-tune foundation models based on human preferences.

**How It Works**:
1. Generate multiple outputs from model
2. Humans rank outputs by quality
3. Train reward model from human preferences
4. Use RL to optimize model based on reward model

**Use Case**: Fine-tuning LLMs (ChatGPT, Claude)
- Align model outputs with human values
- Improve helpfulness, harmlessness, honesty
- Reduce harmful or biased outputs

**AWS Context**: Used in foundation model training (Bedrock models)

#### Advantages
- Learns optimal behavior through experience
- Handles sequential decision-making
- Adapts to changing environments
- No labeled data required

#### Challenges
- Requires simulation environment
- Sample inefficient (needs many interactions)
- Reward function design is critical
- Can be unstable during training
- Computationally expensive

---

## 5. Exam Focus Areas

### Comprehensive Comparison Table

| Aspect | Supervised | Unsupervised | Reinforcement |
|--------|------------|--------------|---------------|
| **Data Type** | Labeled (input + output) | Unlabeled (input only) | Environment interactions |
| **Learning Goal** | Predict outputs | Discover patterns | Maximize rewards |
| **Feedback** | Immediate (correct answer) | None | Delayed (rewards) |
| **Output** | Predictions/classifications | Clusters/patterns | Actions/policies |
| **Evaluation** | Accuracy, precision, recall | Silhouette, elbow method | Cumulative reward |
| **Data Volume** | Moderate (labeled) | Large (unlabeled) | Many interactions |
| **Cost** | High (labeling) | Low (no labeling) | Medium (simulation) |
| **Examples** | Spam detection, price prediction | Customer segmentation, anomaly detection | Game playing, robotics |
| **AWS Algorithms** | XGBoost, Linear Learner | K-Means, PCA, RCF | SageMaker RL, DeepRacer |
| **Common Use** | Most common (80% of ML) | Exploratory analysis | Specialized applications |

### Decision Matrix for Exam Questions

```
Question Scenario → Learning Paradigm
─────────────────────────────────────────────────────────────
"Predict customer churn from historical data"        → Supervised
"Classify images as cat/dog"                         → Supervised
"Forecast sales based on past sales"                 → Supervised
"Detect spam emails"                                 → Supervised

"Group customers into segments (no predefined groups)" → Unsupervised
"Find anomalies in network traffic"                   → Unsupervised
"Discover patterns in purchase behavior"              → Unsupervised
"Reduce 100 features to 10 principal components"      → Unsupervised

"Train robot to navigate warehouse"                   → Reinforcement
"Optimize ad placement for maximum clicks"            → Reinforcement
"Game-playing AI"                                     → Reinforcement
"Dynamic pricing optimization"                        → Reinforcement
```

### Common Question Types

**Paradigm Identification**:
- "Has labeled data, need predictions" → Supervised
- "No labels, find natural groups" → Unsupervised
- "Sequential decisions, maximize reward" → Reinforcement

**Algorithm Selection**:
- "Classify transactions as fraud/legitimate" → Supervised (XGBoost)
- "Segment customers into 5 groups" → Unsupervised (K-Means)
- "Train autonomous vehicle" → Reinforcement (RL)

**Data Requirements**:
- "Have input-output pairs" → Supervised
- "Only have inputs, no outputs" → Unsupervised
- "Can simulate environment" → Reinforcement

### Gotchas

1. **Anomaly Detection Can Be Both**
   - Unsupervised: No labeled anomalies (find outliers)
   - Supervised: Have labeled anomalies (classify normal/anomaly)
   - Context determines which

2. **Clustering ≠ Classification**
   - Clustering: Unsupervised (no predefined groups)
   - Classification: Supervised (predefined categories)
   - Don't confuse the two

3. **Reinforcement ≠ Supervised**
   - RL has delayed rewards, not immediate labels
   - RL learns through interaction, not from dataset
   - RL optimizes sequence of actions, not single predictions

4. **Semi-Supervised Learning**
   - Combination: Small labeled + large unlabeled data
   - Not commonly tested but good to know
   - Example: Label few images, use rest unlabeled

5. **Self-Supervised Learning**
   - Creates labels from data itself
   - Used in foundation model pre-training
   - Example: Predict next word (label comes from text)

## 6. Best Practices

### Selecting Learning Paradigm

**Start with These Questions**:
1. **Do you have labeled data?**
   - Yes → Consider Supervised
   - No → Consider Unsupervised or Reinforcement

2. **What's your goal?**
   - Predict specific outcome → Supervised
   - Discover patterns → Unsupervised
   - Optimize sequential decisions → Reinforcement

3. **What's your data situation?**
   - Labeled dataset → Supervised
   - Unlabeled dataset → Unsupervised
   - Can simulate environment → Reinforcement

### AWS Service Selection by Paradigm

**Supervised Learning**:
- Tabular data: SageMaker XGBoost, Linear Learner
- Images: Rekognition Custom Labels
- Text: Comprehend Custom
- Time-series: Forecast
- Recommendations: Personalize
- Fraud: Fraud Detector

**Unsupervised Learning**:
- Clustering: SageMaker K-Means
- Dimensionality reduction: SageMaker PCA
- Anomaly detection: SageMaker RCF, Lookout services
- Pattern discovery: SageMaker

**Reinforcement Learning**:
- Custom RL: SageMaker RL
- Robotics: RoboMaker
- Learning RL: DeepRacer

### Cost Optimization

**Supervised**:
- Labeling is expensive: Use SageMaker Ground Truth
- Consider active learning (label most informative samples)
- Use pre-trained models (transfer learning)

**Unsupervised**:
- Most cost-effective (no labeling)
- Use for initial exploration
- Can inform supervised learning strategy

**Reinforcement**:
- Simulation costs can be high
- Use managed services (SageMaker RL)
- Start with simple environments

## 7. Hands-On Labs

### Lab 1: Supervised Learning - Classification

```python
# Binary classification: Fraud detection
import sagemaker
from sagemaker import image_uris

# Labeled data: transactions with fraud labels
container = image_uris.retrieve('xgboost', region, '1.5-1')

xgb = sagemaker.estimator.Estimator(
    container,
    role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    hyperparameters={
        'objective': 'binary:logistic',
        'num_round': 100,
        'eval_metric': 'auc'
    }
)

# Train on labeled data
xgb.fit({'train': 's3://bucket/labeled_transactions.csv'})

# Predict on new transactions
predictor = xgb.deploy(initial_instance_count=1, instance_type='ml.m5.xlarge')
prediction = predictor.predict(new_transaction)
```

### Lab 2: Unsupervised Learning - Clustering

```python
# Customer segmentation with K-Means
from sagemaker import image_uris

# Unlabeled data: customer features only
kmeans_image = image_uris.retrieve('kmeans', region)

kmeans = sagemaker.estimator.Estimator(
    kmeans_image,
    role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    hyperparameters={
        'k': 5,  # 5 customer segments
        'feature_dim': 10
    }
)

# Train on unlabeled data
kmeans.fit({'train': 's3://bucket/customer_features.csv'})

# Assign customers to segments
predictor = kmeans.deploy(initial_instance_count=1, instance_type='ml.m5.xlarge')
segment = predictor.predict(customer_data)
```

### Lab 3: Unsupervised Learning - Anomaly Detection

```python
# Anomaly detection with Random Cut Forest
from sagemaker import image_uris

rcf_image = image_uris.retrieve('randomcutforest', region)

rcf = sagemaker.estimator.Estimator(
    rcf_image,
    role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    hyperparameters={
        'num_trees': 100,
        'num_samples_per_tree': 256
    }
)

# Train on normal data (unlabeled)
rcf.fit({'train': 's3://bucket/normal_transactions.csv'})

# Detect anomalies
predictor = rcf.deploy(initial_instance_count=1, instance_type='ml.m5.xlarge')
anomaly_score = predictor.predict(transaction)  # High score = anomaly
```

### Lab 4: Reinforcement Learning - DeepRacer

```python
# AWS DeepRacer reward function
def reward_function(params):
    """
    Reinforcement learning reward function
    Agent learns to maximize this reward
    """
    # Extract parameters
    track_width = params['track_width']
    distance_from_center = params['distance_from_center']
    all_wheels_on_track = params['all_wheels_on_track']
    speed = params['speed']
    
    # Initialize reward
    reward = 1.0
    
    # Penalize if off track
    if not all_wheels_on_track:
        reward = 0.001
    else:
        # Reward staying near center
        marker_1 = 0.1 * track_width
        if distance_from_center <= marker_1:
            reward = 1.0
        else:
            reward = 0.5
        
        # Bonus for speed
        if speed > 2.5:
            reward *= 1.5
    
    return float(reward)

# Agent learns through trial and error:
# - Try different actions (steering, speed)
# - Receive rewards based on performance
# - Learn policy that maximizes cumulative reward
```

## 8. Sample Questions

### Question 1: Supervised vs Unsupervised

**Scenario**: A retail company has a database of customer transactions including purchase history, demographics, and total spending. They want to identify distinct customer groups to tailor marketing campaigns, but they don't have predefined categories.

**Question**: Which learning approach should they use?

A) Supervised learning with classification  
B) Unsupervised learning with clustering  
C) Reinforcement learning with rewards  
D) Supervised learning with regression

**Correct Answer**: B) Unsupervised learning with clustering

**Explanation**:
- **Why B is correct**: "Don't have predefined categories" means no labels. "Identify distinct groups" = clustering = unsupervised learning. K-Means would be appropriate.
- **Why A is wrong**: Classification requires predefined categories (labels). They don't have these.
- **Why C is wrong**: Reinforcement learning is for sequential decision-making with rewards, not customer segmentation.
- **Why D is wrong**: Regression predicts continuous values, not groups. Also requires labels.

**Exam Tip**: No predefined categories/labels + find groups = Unsupervised clustering.

---

### Question 2: Supervised Learning Selection

**Scenario**: A bank wants to predict whether loan applications will be approved or denied based on historical data of 100,000 applications with known outcomes (approved/denied).

**Question**: Which learning approach and task type is most appropriate?

A) Unsupervised learning - clustering  
B) Supervised learning - regression  
C) Supervised learning - classification  
D) Reinforcement learning - policy optimization

**Correct Answer**: C) Supervised learning - classification

**Explanation**:
- **Why C is correct**: Historical data with "known outcomes" = labeled data = supervised learning. "Approved/denied" = discrete categories = classification (not regression).
- **Why A is wrong**: They have labeled data (known outcomes), so supervised learning is appropriate, not unsupervised.
- **Why B is wrong**: Regression predicts continuous values (like loan amount). Approved/denied is categorical, requiring classification.
- **Why D is wrong**: Reinforcement learning is for sequential decision-making, not one-time predictions from historical data.

**Exam Tip**: Historical data + known outcomes + categorical prediction = Supervised classification.

---

### Question 3: Reinforcement Learning

**Scenario**: A logistics company wants to optimize delivery routes for their fleet of trucks. Routes change daily based on orders, traffic, and weather. The goal is to minimize delivery time and fuel costs over time through adaptive decision-making.

**Question**: Which learning approach is most suitable?

A) Supervised learning with historical route data  
B) Unsupervised learning to cluster delivery locations  
C) Reinforcement learning to learn optimal routing policy  
D) Supervised regression to predict delivery times

**Correct Answer**: C) Reinforcement learning to learn optimal routing policy

**Explanation**:
- **Why C is correct**: "Optimize over time", "adaptive decision-making", and "sequential decisions" (route choices) indicate reinforcement learning. Agent learns policy to maximize reward (minimize time/cost) through trial and error.
- **Why A is wrong**: Historical routes may not be optimal. RL can discover better strategies through exploration. Supervised learning would just mimic past (possibly suboptimal) decisions.
- **Why B is wrong**: Clustering locations doesn't optimize routes or make sequential decisions.
- **Why D is wrong**: Predicting delivery times doesn't optimize routes. Need decision-making, not just prediction.

**Exam Tip**: Sequential decisions + optimize over time + adaptive = Reinforcement learning.

---

### Question 4: Anomaly Detection Paradigm

**Scenario**: A cybersecurity team wants to detect unusual network traffic patterns that might indicate security breaches. They have large amounts of normal network traffic data but very few examples of actual attacks.

**Question**: Which approach is most appropriate?

A) Supervised learning with labeled attack data  
B) Unsupervised anomaly detection on normal traffic  
C) Reinforcement learning to learn attack patterns  
D) Supervised classification with balanced dataset

**Correct Answer**: B) Unsupervised anomaly detection on normal traffic

**Explanation**:
- **Why B is correct**: "Very few examples of attacks" means insufficient labeled data for supervised learning. Unsupervised anomaly detection (like Random Cut Forest) learns normal patterns and flags deviations as anomalies.
- **Why A is wrong**: "Very few examples of attacks" means insufficient labeled data for effective supervised learning.
- **Why C is wrong**: Reinforcement learning is for sequential decision-making, not pattern detection.
- **Why D is wrong**: Can't create balanced dataset with very few attack examples. Supervised learning requires sufficient labeled examples of both classes.

**Exam Tip**: Few/no labeled anomalies + abundant normal data = Unsupervised anomaly detection.

---

**Exam Tip**: Match learning paradigm to data availability and goal. Labeled data + prediction = Supervised. Unlabeled + patterns = Unsupervised. Sequential decisions + rewards = Reinforcement.
