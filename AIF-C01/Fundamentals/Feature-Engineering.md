# Feature Engineering

## 1. Topic Overview
- **Purpose**: The process of selecting, transforming, and creating variables (features) from raw data to improve machine learning model performance
- **Exam Weight**: Medium-High
- **Exam Domain**: Domain 1 — Fundamentals of AI and ML (20%)
- **Task Statements**: Task 1.2 (Identify practical use cases for AI), Task 1.3 (Describe the ML development lifecycle)

## 2. Exam Keyword Mapping
- **Trigger Words**: "feature engineering", "data preparation", "data preprocessing", "feature selection", "feature extraction", "dimensionality reduction", "one-hot encoding", "data transformation"
- **Scenario Indicators**: Questions about improving model accuracy, handling missing data, preparing data for training, reducing input dimensions
- **Anti-patterns**: Feature engineering is NOT the same as data collection — it transforms existing data, not gathers new data

## 3. Core Concepts

### What Is a Feature?
- A unique attribute/variable in the dataset used as input to an ML model
- Example: For customer churn prediction → customer location, age, income level, recent purchases are all **features**
- Also called **attributes**, **variables**, or **columns** in tabular data

### Why Feature Engineering Matters
- Raw data is rarely in a format suitable for ML models
- Good features can make a simple model outperform a complex model with poor features
- "Garbage in, garbage out" — model quality depends heavily on input data quality
- Deep learning **automates** much of feature engineering as part of algorithm learning

### Where It Fits in the ML Lifecycle
```
Data Collection → Data Preprocessing → Feature Engineering → Model Training → Evaluation → Deployment
                                        ^^^^^^^^^^^^^^^^
                                        YOU ARE HERE
```

## 4. Four Components of Feature Engineering

### 4.1 Feature Creation
Creating **new features** from existing data to improve predictions.

| Technique | Description | Example |
|-----------|-------------|---------|
| **One-Hot Encoding** | Convert categorical variables to binary columns | Color: [Red, Blue, Green] → 3 binary columns |
| **Binning** | Group continuous values into discrete buckets | Age: 25 → "18-30" bucket |
| **Splitting** | Break a single feature into multiple | Full Name → First Name + Last Name |
| **Calculated Features** | Derive new features from math operations | Revenue - Cost = Profit |
| **Date Extraction** | Extract components from timestamps | Date → Day of Week, Month, Year, Is Weekend |
| **Interaction Features** | Combine two features | Price × Quantity = Total Spend |

### 4.2 Feature Transformation & Imputation
Replacing missing or invalid features and transforming existing ones.

| Technique | Description | When to Use |
|-----------|-------------|-------------|
| **Imputation (Mean/Median/Mode)** | Replace missing values with statistical measures | Numerical data with few missing values |
| **Normalization (Min-Max Scaling)** | Scale values to [0, 1] range | When features have different scales |
| **Standardization (Z-Score)** | Scale to mean=0, std=1 | When data follows normal distribution |
| **Log Transformation** | Apply log to reduce skewness | Right-skewed data (e.g., income, prices) |
| **Non-linear Transformations** | Binning numeric variables into categories | When linear relationships don't exist |
| **Cartesian Products** | Combine features to create new ones | Capturing feature interactions |
| **Domain-Specific Features** | Create features using domain knowledge | Industry-specific patterns |

### 4.3 Feature Extraction
Reducing the amount of data using **dimensionality reduction** techniques.

| Technique | Description | Key Characteristic |
|-----------|-------------|-------------------|
| **PCA** (Principal Component Analysis) | Projects data onto directions of maximum variance | Unsupervised, linear, most common |
| **ICA** (Independent Component Analysis) | Separates mixed signals into independent sources | Assumes statistical independence |
| **LDA** (Linear Discriminant Analysis) | Maximizes class separability | Supervised (uses labels) |
| **t-SNE** | Non-linear dimensionality reduction for visualization | Good for 2D/3D visualization |

**Why reduce dimensions?**
- ✅ Reduces memory and computing power requirements
- ✅ Maintains original data characteristics
- ✅ Helps avoid the **curse of dimensionality** (too many features → poor generalization)
- ✅ Removes multicollinearity (correlated features)

### 4.4 Feature Selection
Choosing the **most relevant subset** of features that contribute to minimizing model error.

| Method | Description | Example |
|--------|-------------|---------|
| **Feature Importance Scores** | Rank features by their contribution to predictions | Tree-based models provide built-in importance |
| **Correlation Matrix** | Identify highly correlated features (remove redundant ones) | If Feature A and B are 0.95 correlated, drop one |
| **Wrapper Methods** | Train model with different feature subsets | Forward selection, backward elimination |
| **Filter Methods** | Statistical tests to score features | Chi-squared, mutual information |
| **L1 Regularization (Lasso)** | Automatically drives irrelevant feature weights to zero | Built-in feature selection during training |

**Why select features?**
- ✅ Reduces overfitting
- ✅ Improves model accuracy
- ✅ Reduces training time
- ✅ Improves model interpretability

## 5. AWS Services for Feature Engineering

### Amazon SageMaker Data Wrangler
- **Purpose**: Visual, low-code data preparation and feature engineering tool
- **Key Features**:
  - 300+ built-in PySpark transformations (no code required)
  - Natural language interface for data transformation
  - Import from S3, Athena, Redshift, Snowflake, Databricks
  - Data quality reports and visualizations
  - Quick Model analysis to estimate feature predictive power
  - Export to S3, SageMaker Pipelines, Feature Store, or Python scripts
- **Exam Tip**: 🎯 "No-code/low-code data preparation" → Data Wrangler

### Amazon SageMaker Feature Store
- **Purpose**: Centralized repository to store, share, and manage ML features
- **Two Store Types**:

| | Online Store | Offline Store |
|---|---|---|
| **Purpose** | Real-time inference | Training & batch inference |
| **Latency** | Low latency (milliseconds) | Higher latency (acceptable) |
| **Data** | Latest record only | Full historical records |
| **Use Case** | Serving features during prediction | Data exploration, model training |
| **Format** | Key-value lookup | AWS Glue or Apache Iceberg tables |

- **Key Concepts**:
  - **Feature Group**: Collection of features (like a table)
  - **Record Identifier**: Unique ID for each row
  - **Event Time**: Timestamp for each record
  - **Data Types**: String (default), Fractional (float64), Integral (int64)
  - Online + Offline stores sync automatically when both enabled
- **Exam Tip**: 🎯 "Centralized feature storage" or "share features across teams" → Feature Store

### Amazon SageMaker Processing
- **Purpose**: Run data preprocessing and feature engineering scripts at scale
- **Use Case**: Custom transformations using scikit-learn, Spark, or custom containers

### AWS Glue
- **Purpose**: Serverless ETL service for large-scale data transformation
- **Use Case**: Batch feature engineering on large datasets

### Amazon EMR
- **Purpose**: Big data processing with Spark/Hadoop
- **Use Case**: Feature engineering at petabyte scale

## 6. Feature Engineering for Different Data Types

### Tabular Data
- One-hot encoding for categorical variables
- Scaling/normalization for numerical variables
- Imputation for missing values
- Feature crosses for interactions

### Text Data (NLP)
- **Tokenization**: Split text into words/subwords
- **TF-IDF**: Term Frequency-Inverse Document Frequency
- **Word Embeddings**: Word2Vec, GloVe, BERT embeddings
- **Bag of Words**: Count word occurrences
- AWS Service: Amazon Comprehend (pre-built NLP features)

### Image Data (Computer Vision)
- **Pixel normalization**: Scale pixel values to [0, 1]
- **Data augmentation**: Rotation, flipping, cropping, color jittering
- **Feature extraction**: Use pre-trained CNN layers as feature extractors
- AWS Service: Amazon Rekognition (pre-built vision features)

### Time Series Data
- **Lag features**: Previous time step values
- **Rolling statistics**: Moving averages, rolling std
- **Date features**: Day of week, month, holiday flags
- AWS Service: Amazon Forecast (automated time series features)

## 7. Deep Learning & Feature Engineering
- Deep learning models (CNNs, Transformers) **automatically learn features** from raw data
- This is called **representation learning**
- Reduces the need for manual feature engineering
- Still need basic preprocessing (normalization, resizing, tokenization)
- **Exam Tip**: 🎯 "Automated feature engineering" or "learns features automatically" → Deep Learning

## 8. Exam Focus Areas

### Common Question Types
- "How to handle missing data?" → **Imputation** (mean, median, mode)
- "How to reduce number of features?" → **PCA** (dimensionality reduction) or **Feature Selection**
- "Where to store features for real-time inference?" → **SageMaker Feature Store (Online Store)**
- "Low-code data preparation tool?" → **SageMaker Data Wrangler**
- "How to convert categorical to numerical?" → **One-Hot Encoding**

### Gotchas
- Feature engineering ≠ Feature Store (engineering = creating/transforming, store = storing/serving)
- PCA is **unsupervised** (no labels needed), LDA is **supervised** (needs labels)
- One-hot encoding can create very high-dimensional data (curse of dimensionality)
- Normalization vs. Standardization: normalization scales to [0,1], standardization scales to mean=0, std=1
- Deep learning automates feature engineering but still needs data preprocessing

### Decision Tree
```
Need to prepare features for ML?
│
├── No-code/visual tool? → SageMaker Data Wrangler
├── Store & share features? → SageMaker Feature Store
│   ├── Real-time serving? → Online Store
│   └── Training/batch? → Offline Store
├── Custom processing scripts? → SageMaker Processing
├── Large-scale ETL? → AWS Glue or Amazon EMR
└── Deep learning? → Model learns features automatically
```

## 9. Best Practices
- **Start simple**: Begin with basic features before complex engineering
- **Use domain knowledge**: Domain-specific features often outperform generic ones
- **Handle missing data early**: Don't let NaN values propagate through the pipeline
- **Scale features consistently**: Use the same scaler for training and inference
- **Monitor feature drift**: Features can change over time (data drift)
- **Use Feature Store**: Centralize features to ensure consistency between training and serving
- **Automate pipelines**: Use SageMaker Pipelines to automate feature engineering workflows

## 10. Sample Questions

### Question 1
A data scientist has a dataset with 500 features and suspects many are redundant. Which technique should they use to reduce the number of features while preserving the most important information?

A) One-hot encoding  
B) Principal Component Analysis (PCA)  
C) Data augmentation  
D) Imputation  

**Answer: B) Principal Component Analysis (PCA)**  
PCA is a dimensionality reduction technique that projects data onto directions of maximum variance, reducing features while preserving important information. One-hot encoding increases dimensions. Data augmentation creates more samples, not fewer features. Imputation handles missing values, not feature reduction.

---

### Question 2
A machine learning team needs to serve features to their model during real-time inference with low latency, while also maintaining historical feature data for model retraining. Which AWS service and configuration should they use?

A) Amazon S3 with versioning enabled  
B) Amazon SageMaker Feature Store with both online and offline stores enabled  
C) Amazon DynamoDB for real-time and Amazon Redshift for historical  
D) Amazon SageMaker Data Wrangler with export to S3  

**Answer: B) Amazon SageMaker Feature Store with both online and offline stores enabled**  
SageMaker Feature Store's online store provides low-latency real-time feature lookup, while the offline store maintains historical records for training and batch inference. When both are enabled, they sync automatically. The other options require custom integration and don't provide the managed feature serving capabilities.

---

### Question 3
A company wants to prepare their tabular data for ML training without writing code. They need to handle missing values, encode categorical variables, and visualize data quality. Which AWS service is most appropriate?

A) Amazon SageMaker Autopilot  
B) Amazon SageMaker Data Wrangler  
C) AWS Glue DataBrew  
D) Amazon SageMaker Processing  

**Answer: B) Amazon SageMaker Data Wrangler**  
SageMaker Data Wrangler provides a visual, low-code interface with 300+ built-in transformations for data preparation and feature engineering. It includes data quality reports and visualizations. Autopilot automates the entire ML pipeline (not just data prep). Glue DataBrew is for general data preparation but not ML-specific. SageMaker Processing requires writing custom scripts.

---

**Exam Tip**: 🎯 For AIF-C01, focus on understanding the four components of feature engineering (creation, transformation, extraction, selection), when to use each AWS service (Data Wrangler vs. Feature Store vs. Processing), and how deep learning automates feature engineering. Know the difference between PCA (unsupervised) and LDA (supervised) for dimensionality reduction.
