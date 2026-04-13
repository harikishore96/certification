# Data Types in AI Models

## 1. Service/Topic Overview
- **Purpose**: Understanding different data types used in AI/ML models and their characteristics
- **Service Level**: Conceptual framework applicable across all AWS AI/ML services
- **Key Use Cases**: 
  - Selecting appropriate AWS services based on data type
  - Choosing correct ML algorithms for data structure
  - Data preprocessing and preparation strategies
  - Understanding service capabilities and limitations
- **Exam Weight**: HIGH - Fundamental to all AI/ML implementations
- **Exam Domain**: 
  - Domain 1: Fundamentals of AI and ML (20%)
  - Domain 3: Applications of Foundation Models (28%)
- **Task Statements**: 
  - Task 1.1: Explain basic AI concepts and terminologies
  - Task 1.3: Describe the ML development lifecycle
  - Task 3.1: Describe design considerations for applications

## 2. Exam Keyword Mapping

### Trigger Words
- **Labeled**: "supervised", "ground truth", "annotations", "training labels"
- **Unlabeled**: "unsupervised", "clustering", "no labels", "raw data"
- **Tabular**: "rows and columns", "CSV", "database", "structured"
- **Time-Series**: "temporal", "sequential", "forecasting", "trends over time"
- **Image**: "photos", "visual", "computer vision", "pixels"
- **Text**: "documents", "NLP", "language", "unstructured text"
- **Structured**: "schema", "relational", "organized format"
- **Unstructured**: "no predefined format", "raw", "free-form"

### Scenario Indicators
- "Customer database with purchase history" → Tabular, Structured, Labeled
- "Social media posts" → Text, Unstructured, Unlabeled
- "Product images for classification" → Image, Unstructured, Labeled
- "Stock prices over time" → Time-Series, Structured
- "Raw sensor readings" → Time-Series, Unlabeled
- "Medical records with diagnoses" → Text/Structured, Labeled

## 3. Core Concepts

### Data Classification Framework

```
By Learning Type:
├─ Labeled Data (has target/output)
└─ Unlabeled Data (no target/output)

By Structure:
├─ Structured (organized, schema-based)
│  ├─ Tabular
│  └─ Time-Series
└─ Unstructured (no predefined format)
   ├─ Text
   ├─ Image
   ├─ Audio
   └─ Video

By Format:
├─ Tabular (rows/columns)
├─ Time-Series (temporal sequence)
├─ Image (pixels/visual)
├─ Text (natural language)
├─ Audio (sound waves)
└─ Video (sequential images + audio)
```

## 4. Key Features & Components

### 1. Labeled vs Unlabeled Data

#### Labeled Data

**Definition**: Data with known outputs/targets (ground truth annotations)

**Characteristics**:
- Each input has corresponding correct answer
- Required for supervised learning
- Expensive and time-consuming to create
- Enables model evaluation with accuracy metrics

**Structure**:
```
Input (Features) → Label (Target)
Email text → Spam/Not Spam
Image → Cat/Dog
Transaction → Fraudulent/Legitimate
```

**Use Cases**:
✅ Classification (spam detection, image recognition)
✅ Regression (price prediction, demand forecasting)
✅ Object detection (bounding boxes)
✅ Sentiment analysis (positive/negative/neutral)

**AWS Services**:
- SageMaker Ground Truth (data labeling)
- SageMaker (supervised learning algorithms)
- Rekognition Custom Labels (image classification)
- Comprehend Custom (text classification)

**Example**:
```python
# Labeled data for fraud detection
labeled_data = [
    {"amount": 100, "location": "US", "time": "10am", "label": "legitimate"},
    {"amount": 5000, "location": "Unknown", "time": "3am", "label": "fraud"},
    {"amount": 50, "location": "US", "time": "2pm", "label": "legitimate"}
]
```

#### Unlabeled Data

**Definition**: Data without known outputs or annotations

**Characteristics**:
- No target variable or ground truth
- Used for unsupervised learning
- Abundant and inexpensive to collect
- Requires different evaluation approaches

**Use Cases**:
✅ Clustering (customer segmentation)
✅ Anomaly detection (fraud, defects)
✅ Dimensionality reduction (PCA)
✅ Pattern discovery
✅ Pre-training foundation models

**AWS Services**:
- SageMaker (K-Means, PCA, Random Cut Forest)
- Bedrock (foundation models pre-trained on unlabeled text)
- Lookout for Metrics (anomaly detection)

**Example**:
```python
# Unlabeled data for customer segmentation
unlabeled_data = [
    {"age": 25, "income": 50000, "purchases": 10},
    {"age": 45, "income": 80000, "purchases": 25},
    {"age": 30, "income": 60000, "purchases": 15}
]
# No labels - algorithm finds natural groupings
```

**Comparison**:

| Aspect | Labeled | Unlabeled |
|--------|---------|-----------|
| **Cost** | High (manual annotation) | Low (readily available) |
| **Learning Type** | Supervised | Unsupervised |
| **Availability** | Limited | Abundant |
| **Use Case** | Prediction, classification | Clustering, discovery |
| **Evaluation** | Accuracy, precision, recall | Silhouette score, elbow method |
| **AWS Labeling** | Ground Truth | N/A |

---

### 2. Structured vs Unstructured Data

#### Structured Data

**Definition**: Organized data with predefined schema, easily searchable

**Characteristics**:
- Fixed format and schema
- Organized in rows and columns
- Easily stored in relational databases
- Queryable with SQL
- Represents ~20% of enterprise data

**Types**:
- Tabular data (CSV, databases)
- Time-series data (with timestamps)

**Use Cases**:
✅ Business analytics
✅ Financial modeling
✅ Customer relationship management
✅ Inventory management

**AWS Services**:
- SageMaker (XGBoost, Linear Learner)
- Forecast (time-series)
- Personalize (recommendations)
- Fraud Detector (tabular fraud detection)

**Example**:
```
CustomerID | Age | Income | Purchases | Churn
1001       | 35  | 75000  | 12        | No
1002       | 42  | 90000  | 8         | Yes
```

#### Unstructured Data

**Definition**: Data without predefined format or organization

**Characteristics**:
- No fixed schema
- Free-form content
- Requires preprocessing/feature extraction
- Represents ~80% of enterprise data
- More complex to analyze

**Types**:
- Text (documents, emails, social media)
- Images (photos, scans)
- Audio (speech, music)
- Video (recordings, streams)

**Use Cases**:
✅ Document analysis
✅ Image recognition
✅ Speech transcription
✅ Sentiment analysis

**AWS Services**:
- Bedrock (text, images)
- Rekognition (images/video)
- Comprehend (text)
- Transcribe (audio)
- Textract (documents)

**Comparison**:

| Aspect | Structured | Unstructured |
|--------|------------|--------------|
| **Format** | Fixed schema | No schema |
| **Storage** | Relational DB | S3, data lakes |
| **Query** | SQL | Search, ML |
| **Volume** | 20% of data | 80% of data |
| **Processing** | Traditional analytics | Deep learning |
| **Examples** | CSV, databases | Text, images, video |

---

### 3. Tabular Data

**Definition**: Data organized in rows (records) and columns (features)

**Characteristics**:
- Most common structured format
- Each row is an observation
- Each column is a feature/variable
- Easily processed by traditional ML algorithms

**Format Examples**:
- CSV files
- Excel spreadsheets
- Database tables
- Parquet files

**Feature Types**:
- **Numerical**: Continuous (age, price) or discrete (count)
- **Categorical**: Nominal (color, category) or ordinal (rating)
- **Binary**: Yes/No, True/False

**Use Cases**:
✅ Customer churn prediction
✅ Credit risk assessment
✅ Sales forecasting
✅ Fraud detection
✅ Recommendation systems

**AWS Services**:
- SageMaker (XGBoost, Linear Learner, Factorization Machines)
- Forecast (with additional features)
- Personalize (user-item interactions)
- Fraud Detector

**Best Algorithms**:
- XGBoost (gradient boosting)
- Random Forest
- Linear/Logistic Regression
- Neural Networks

**Example**:
```python
import pandas as pd

# Tabular data
df = pd.DataFrame({
    'customer_id': [1, 2, 3],
    'age': [25, 45, 35],
    'income': [50000, 80000, 65000],
    'credit_score': [720, 680, 750],
    'loan_approved': ['Yes', 'No', 'Yes']  # Label
})
```

**Preprocessing Needs**:
- Handle missing values
- Encode categorical variables
- Normalize/standardize numerical features
- Feature engineering

---

### 4. Time-Series Data

**Definition**: Sequential data points indexed by time, ordered chronologically

**Characteristics**:
- Temporal ordering is critical
- Often exhibits trends, seasonality, cycles
- Past values influence future values
- Requires specialized algorithms

**Components**:
- **Trend**: Long-term increase/decrease
- **Seasonality**: Regular periodic patterns
- **Cyclical**: Non-fixed periodic patterns
- **Noise**: Random variation

**Use Cases**:
✅ Stock price prediction
✅ Demand forecasting
✅ Weather prediction
✅ IoT sensor monitoring
✅ Website traffic analysis
✅ Energy consumption forecasting

**AWS Services**:
- Amazon Forecast (specialized time-series)
- SageMaker DeepAR (time-series forecasting)
- Lookout for Metrics (anomaly detection)
- Lookout for Equipment (predictive maintenance)

**Best Algorithms**:
- DeepAR (deep learning for time-series)
- ARIMA (statistical)
- Prophet (Facebook's algorithm)
- LSTM/RNN (neural networks)

**Example**:
```python
# Time-series data
time_series = pd.DataFrame({
    'timestamp': ['2024-01-01', '2024-01-02', '2024-01-03'],
    'sales': [1000, 1200, 950],
    'temperature': [72, 75, 70],
    'day_of_week': ['Mon', 'Tue', 'Wed']
})
```

**Key Considerations**:
- Maintain temporal order (don't shuffle)
- Use time-based train/test splits
- Handle missing timestamps
- Account for seasonality and trends

---

### 5. Image Data

**Definition**: Visual data represented as pixels in 2D or 3D arrays

**Characteristics**:
- Unstructured format
- High dimensionality (millions of pixels)
- Requires deep learning (CNNs)
- Spatial relationships matter

**Format Types**:
- **Grayscale**: Single channel (0-255)
- **RGB**: Three channels (Red, Green, Blue)
- **RGBA**: RGB + Alpha (transparency)

**Dimensions**:
```
Grayscale: Height × Width × 1
RGB: Height × Width × 3
Batch: Batch_Size × Height × Width × Channels
```

**Use Cases**:
✅ Image classification (cat vs dog)
✅ Object detection (bounding boxes)
✅ Facial recognition
✅ Medical image analysis
✅ Defect detection
✅ Optical character recognition (OCR)

**AWS Services**:
- Amazon Rekognition (pre-built computer vision)
- Rekognition Custom Labels (custom image classification)
- Textract (document/image text extraction)
- SageMaker (custom CNN models)
- Bedrock (multimodal models like Nova)

**Best Algorithms**:
- Convolutional Neural Networks (CNNs)
- ResNet, VGG, Inception (architectures)
- YOLO, R-CNN (object detection)
- Vision Transformers (ViT)

**Example**:
```python
import boto3

rekognition = boto3.client('rekognition')

# Analyze image
response = rekognition.detect_labels(
    Image={'S3Object': {'Bucket': 'my-bucket', 'Name': 'image.jpg'}},
    MaxLabels=10
)

# Image data: pixels → CNN → predictions
```

**Preprocessing Needs**:
- Resize to consistent dimensions
- Normalize pixel values (0-1 or -1 to 1)
- Data augmentation (rotation, flip, crop)
- Color space conversion

---

### 6. Text Data

**Definition**: Natural language data in written form

**Characteristics**:
- Unstructured format
- Variable length
- Requires tokenization and encoding
- Context and semantics matter

**Types**:
- **Short text**: Tweets, reviews, messages
- **Long text**: Articles, documents, books
- **Structured text**: Forms, tables
- **Conversational**: Chat, dialogue

**Use Cases**:
✅ Sentiment analysis
✅ Text classification (spam, topic)
✅ Named entity recognition (NER)
✅ Machine translation
✅ Text summarization
✅ Question answering
✅ Chatbots

**AWS Services**:
- Amazon Bedrock (LLMs for text generation)
- Amazon Comprehend (text analysis)
- Amazon Translate (language translation)
- Amazon Lex (conversational interfaces)
- SageMaker (custom NLP models)

**Best Algorithms**:
- Transformers (BERT, GPT, Claude)
- RNNs, LSTMs (sequential models)
- Word embeddings (Word2Vec, GloVe)
- Attention mechanisms

**Example**:
```python
import boto3

comprehend = boto3.client('comprehend')

# Analyze text sentiment
response = comprehend.detect_sentiment(
    Text="I love this product! It exceeded my expectations.",
    LanguageCode='en'
)

# Text → Tokenization → Embeddings → Model → Predictions
```

**Preprocessing Needs**:
- Tokenization (split into words/subwords)
- Lowercasing
- Remove punctuation/special characters
- Stop word removal (optional)
- Stemming/Lemmatization
- Encoding (convert to numbers)

---

### 7. Audio Data

**Definition**: Sound waves represented as digital signals

**Characteristics**:
- Sequential/temporal data
- Represented as waveforms or spectrograms
- Sampling rate and duration matter
- Requires specialized processing

**Use Cases**:
✅ Speech-to-text transcription
✅ Voice commands
✅ Music classification
✅ Speaker identification
✅ Audio anomaly detection

**AWS Services**:
- Amazon Transcribe (speech-to-text)
- Amazon Polly (text-to-speech)
- SageMaker (custom audio models)

**Preprocessing**:
- Convert to spectrograms
- Normalize amplitude
- Remove noise
- Feature extraction (MFCC)

---

### 8. Video Data

**Definition**: Sequential images (frames) with optional audio

**Characteristics**:
- Combination of image + time-series
- Very high dimensionality
- Computationally expensive
- Temporal relationships between frames

**Use Cases**:
✅ Action recognition
✅ Video surveillance
✅ Content moderation
✅ Sports analytics

**AWS Services**:
- Amazon Rekognition Video
- SageMaker (custom video models)

---

## 5. Exam Focus Areas

### Data Type Selection Matrix

| Business Need | Data Type | Structure | Labels | AWS Service |
|---------------|-----------|-----------|--------|-------------|
| Customer churn prediction | Tabular | Structured | Labeled | SageMaker |
| Sales forecasting | Time-Series | Structured | Labeled | Forecast |
| Image classification | Image | Unstructured | Labeled | Rekognition |
| Sentiment analysis | Text | Unstructured | Labeled | Comprehend |
| Customer segmentation | Tabular | Structured | Unlabeled | SageMaker K-Means |
| Anomaly detection | Time-Series | Structured | Unlabeled | Lookout |
| Document extraction | Image/Text | Unstructured | N/A | Textract |
| Speech transcription | Audio | Unstructured | N/A | Transcribe |

### Common Question Patterns

**Data Type Identification**:
- "Database with customer records" → Tabular, Structured
- "Social media posts" → Text, Unstructured
- "Security camera footage" → Video, Unstructured
- "Daily sales numbers" → Time-Series, Structured

**Service Selection Based on Data**:
- Tabular + Labeled → SageMaker (XGBoost)
- Time-Series → Forecast or DeepAR
- Images → Rekognition or SageMaker CNN
- Text → Comprehend or Bedrock
- Audio → Transcribe

**Labeling Requirements**:
- "Need to classify images" → Labeled data required
- "Find customer groups" → Unlabeled (clustering)
- "Predict prices" → Labeled (supervised)

### Gotchas

1. **Time-Series ≠ Just Tabular**
   - Time-series has temporal dependencies
   - Requires specialized algorithms (DeepAR, ARIMA)
   - Can't shuffle data randomly

2. **Structured ≠ Always Labeled**
   - Structured data can be unlabeled (clustering)
   - Unstructured data can be labeled (image classification)
   - Structure and labels are independent concepts

3. **Text is Unstructured**
   - Even though text has grammar/syntax
   - No fixed schema like tabular data
   - Requires NLP preprocessing

4. **Images Need Deep Learning**
   - Traditional ML struggles with raw pixels
   - CNNs automatically extract features
   - Pre-trained models (transfer learning) recommended

5. **Labeling is Expensive**
   - Use SageMaker Ground Truth for efficiency
   - Consider semi-supervised or unsupervised approaches
   - Pre-trained models reduce labeling needs

## 6. Best Practices

### Data Preparation by Type

**Tabular Data**:
- Handle missing values (imputation)
- Encode categorical variables (one-hot, label encoding)
- Normalize/standardize numerical features
- Feature engineering (create new features)
- Split: train/validation/test

**Time-Series Data**:
- Maintain temporal order
- Use time-based splits (not random)
- Handle missing timestamps
- Feature engineering (lags, rolling averages)
- Account for seasonality

**Image Data**:
- Resize to consistent dimensions
- Normalize pixel values
- Data augmentation (rotation, flip, zoom)
- Use pre-trained models (transfer learning)
- Balance classes

**Text Data**:
- Tokenization (word, subword, character)
- Remove noise (HTML, special characters)
- Handle variable length (padding, truncation)
- Use pre-trained embeddings or LLMs
- Consider domain-specific vocabulary

### AWS Service Selection

**Structured Data**:
- Small datasets (< 10K rows): SageMaker built-in algorithms
- Time-series: Amazon Forecast
- Recommendations: Amazon Personalize
- Fraud: Amazon Fraud Detector

**Unstructured Data**:
- Images: Amazon Rekognition
- Text: Amazon Comprehend or Bedrock
- Audio: Amazon Transcribe
- Video: Amazon Rekognition Video
- Custom models: SageMaker with deep learning

## 7. Hands-On Labs

### Lab 1: Tabular Data with SageMaker

```python
import sagemaker
from sagemaker import image_uris

# Tabular data for XGBoost
container = image_uris.retrieve('xgboost', region, '1.5-1')

xgb = sagemaker.estimator.Estimator(
    container,
    role,
    instance_count=1,
    instance_type='ml.m5.xlarge',
    hyperparameters={
        'objective': 'binary:logistic',
        'num_round': 100
    }
)

# Train on tabular CSV data
xgb.fit({'train': 's3://bucket/train.csv'})
```

### Lab 2: Time-Series with Forecast

```python
import boto3

forecast = boto3.client('forecast')

# Create dataset for time-series
response = forecast.create_dataset(
    DatasetName='sales-forecast',
    Domain='RETAIL',
    DatasetType='TARGET_TIME_SERIES',
    Schema={
        'Attributes': [
            {'AttributeName': 'timestamp', 'AttributeType': 'timestamp'},
            {'AttributeName': 'item_id', 'AttributeType': 'string'},
            {'AttributeName': 'demand', 'AttributeType': 'float'}
        ]
    }
)
```

### Lab 3: Image Data with Rekognition

```python
import boto3

rekognition = boto3.client('rekognition')

# Analyze image (unstructured data)
response = rekognition.detect_labels(
    Image={'S3Object': {'Bucket': 'bucket', 'Name': 'image.jpg'}},
    MaxLabels=10,
    MinConfidence=80
)

for label in response['Labels']:
    print(f"{label['Name']}: {label['Confidence']:.2f}%")
```

### Lab 4: Text Data with Comprehend

```python
import boto3

comprehend = boto3.client('comprehend')

# Analyze text (unstructured data)
text = "I love this product! Best purchase ever."

# Sentiment
sentiment = comprehend.detect_sentiment(Text=text, LanguageCode='en')

# Entities
entities = comprehend.detect_entities(Text=text, LanguageCode='en')

print(f"Sentiment: {sentiment['Sentiment']}")
```

## 8. Sample Questions

### Question 1: Data Type Identification

**Scenario**: A retail company has the following data sources:
1. Customer database with age, income, purchase history
2. Product images for catalog
3. Customer reviews and feedback
4. Daily sales numbers for the past 5 years

**Question**: Match each data source to its correct classification:

A) 1-Tabular/Structured, 2-Image/Unstructured, 3-Text/Unstructured, 4-Time-Series/Structured  
B) 1-Time-Series, 2-Structured, 3-Tabular, 4-Unstructured  
C) 1-Unstructured, 2-Tabular, 3-Image, 4-Text  
D) All are structured data

**Correct Answer**: A

**Explanation**:
- **Why A is correct**: Customer database (rows/columns) = Tabular/Structured. Product images = Image/Unstructured. Reviews (natural language) = Text/Unstructured. Daily sales over time = Time-Series/Structured.
- **Why B is wrong**: Mismatches all categories.
- **Why C is wrong**: Customer database is structured tabular, not unstructured.
- **Why D is wrong**: Images and text are unstructured.

---

### Question 2: Labeled vs Unlabeled

**Scenario**: A company wants to group customers into segments based on purchasing behavior without predefined categories.

**Question**: What type of data and learning approach is needed?

A) Labeled data with supervised learning  
B) Unlabeled data with unsupervised learning  
C) Time-series data with forecasting  
D) Labeled data with reinforcement learning

**Correct Answer**: B

**Explanation**:
- **Why B is correct**: "Without predefined categories" means no labels. Grouping/segmentation = clustering = unsupervised learning with unlabeled data.
- **Why A is wrong**: Supervised learning requires labeled data with known categories.
- **Why C is wrong**: This is about customer segmentation, not time-based forecasting.
- **Why D is wrong**: Reinforcement learning is for sequential decision-making, not clustering.

---

### Question 3: Service Selection by Data Type

**Scenario**: A healthcare provider needs to extract patient information (names, dates, medications) from handwritten medical forms.

**Question**: Which AWS service is most appropriate?

A) Amazon Comprehend (text analysis)  
B) Amazon Textract (document extraction)  
C) Amazon Forecast (time-series)  
D) Amazon SageMaker XGBoost (tabular)

**Correct Answer**: B

**Explanation**:
- **Why B is correct**: Textract extracts text and structured data from documents/images, including handwritten forms. Perfect for this use case.
- **Why A is wrong**: Comprehend analyzes text but doesn't extract from images/handwriting.
- **Why C is wrong**: Forecast is for time-series predictions, not document extraction.
- **Why D is wrong**: XGBoost works with tabular data, not image/document extraction.

---

### Question 4: Time-Series Characteristics

**Scenario**: A data scientist is building a model to predict website traffic. They randomly shuffle the data before splitting into train/test sets. The model performs poorly in production.

**Question**: What is the likely issue?

A) Insufficient training data  
B) Overfitting to training data  
C) Violated temporal ordering of time-series data  
D) Need more features

**Correct Answer**: C

**Explanation**:
- **Why C is correct**: Website traffic over time is time-series data. Shuffling destroys temporal relationships. Time-series requires chronological train/test splits.
- **Why A is wrong**: The issue is methodology (shuffling), not data quantity.
- **Why B is wrong**: Overfitting would show good training performance but poor test performance. The issue here is improper data handling.
- **Why D is wrong**: More features won't fix the fundamental problem of shuffled time-series data.

**Exam Tip**: Time-series data must maintain temporal order. Never shuffle time-series data.
