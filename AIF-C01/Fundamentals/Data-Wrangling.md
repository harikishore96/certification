# Data Wrangling

## 1. Topic Overview
- **Purpose**: The process of cleaning, transforming, structuring, and enriching raw data into a usable format for machine learning — encompasses data preprocessing, data cleaning, data labeling, and data augmentation
- **Exam Weight**: Medium-High
- **Exam Domain**: Domain 1 — Fundamentals of AI and ML (20%)
- **Task Statements**: Task 1.3 (Describe the ML development lifecycle)

## 2. Exam Keyword Mapping
- **Trigger Words**: "data wrangling", "data preprocessing", "data cleaning", "data preparation", "missing values", "outliers", "data quality", "data labeling", "data augmentation", "data partitioning"
- **Scenario Indicators**: Questions about preparing data before training, handling messy data, improving data quality, splitting datasets
- **Anti-patterns**: Data wrangling is NOT feature engineering — wrangling cleans/structures raw data, feature engineering creates/transforms features for model input

## 3. Core Concepts

### Where Data Wrangling Fits in the ML Lifecycle
```
Data Collection → Data Wrangling → Feature Engineering → Model Training → Evaluation → Deployment
                  ^^^^^^^^^^^^^^
                  YOU ARE HERE
```

### Why Data Wrangling Matters
- Data scientists spend **60-80%** of their time on data preparation
- "Garbage in, garbage out" — model quality depends on data quality
- Poor data leads to biased, inaccurate, or unreliable models
- Proper wrangling prevents data leakage, overfitting, and training failures

### Data Wrangling vs. Feature Engineering

| Aspect | Data Wrangling | Feature Engineering |
|--------|---------------|-------------------|
| **Goal** | Clean and structure raw data | Create/transform features for model input |
| **When** | Before feature engineering | After data wrangling |
| **Activities** | Remove duplicates, handle missing values, fix errors | One-hot encoding, PCA, feature creation |
| **Focus** | Data quality | Model performance |

## 4. Data Preprocessing Strategies

### 4.1 Data Cleaning
The foundation of data wrangling — fixing data quality issues.

#### Handling Missing Values

| Technique | Description | When to Use |
|-----------|-------------|-------------|
| **Remove rows** | Delete rows with missing values | Few missing values, large dataset |
| **Remove columns** | Drop entire feature column | >50% values missing in column |
| **Mean imputation** | Replace with column mean | Numerical, normally distributed |
| **Median imputation** | Replace with column median | Numerical, skewed data |
| **Mode imputation** | Replace with most frequent value | Categorical data |
| **Forward/backward fill** | Use previous/next value | Time series data |
| **KNN imputation** | Use K-nearest neighbors to estimate | Complex relationships between features |
| **Constant value** | Replace with a fixed value (e.g., 0, "Unknown") | When missing has meaning |

#### Handling Outliers

| Technique | Description |
|-----------|-------------|
| **Z-score method** | Remove data points > 3 standard deviations from mean |
| **IQR method** | Remove data outside 1.5 × interquartile range |
| **Capping/Winsorizing** | Cap extreme values at a percentile threshold |
| **Log transformation** | Reduce impact of extreme values |
| **Keep outliers** | When outliers are valid (e.g., fraud detection) |

⚠️ **Exam Tip**: Not all outliers should be removed — in fraud detection, outliers ARE the signal.

#### Removing Duplicates
- Duplicate rows can bias the model toward repeated patterns
- Remove duplicates **before** splitting into train/test sets to prevent data leakage

#### Fixing Data Types
- Ensure numerical columns are numeric (not strings)
- Parse dates into proper datetime format
- Standardize categorical values (e.g., "Yes"/"yes"/"Y" → "Yes")

### 4.2 Data Partitioning (Train/Validate/Test Split)
Splitting data to prevent overfitting and evaluate model accurately.

| Split | Purpose | Typical % |
|-------|---------|-----------|
| **Training set** | Train the model | 70-80% |
| **Validation set** | Tune hyperparameters, prevent overfitting | 10-15% |
| **Test set** | Final unbiased evaluation | 10-15% |

**Critical Rules**:
- ✅ Remove duplicates **before** splitting (prevents data leakage)
- ✅ Split **randomly** to ensure representative samples
- ✅ For time series: split **chronologically** (no random shuffle)
- ❌ Never use test data during training or hyperparameter tuning
- ❌ Never look at test data until final evaluation

**Data Leakage**: When information from the test/validation set "leaks" into training data, giving artificially high performance that won't generalize.

```
Common causes of data leakage:
• Duplicates across train/test sets
• Scaling/normalizing before splitting
• Using future data to predict past (time series)
• Target variable information in features
```

### 4.3 Data Scaling

| Technique | Formula | Output Range | When to Use |
|-----------|---------|-------------|-------------|
| **Normalization (Min-Max)** | (x - min) / (max - min) | [0, 1] | Features with different scales, no outliers |
| **Standardization (Z-Score)** | (x - mean) / std | mean=0, std=1 | Data with outliers, normally distributed |

**Key Points**:
- Fit scaler on **training data only**, then apply to validation/test
- Required for distance-based algorithms (KNN, SVM, K-Means)
- Not required for tree-based algorithms (Random Forest, XGBoost)
- Standardization handles outliers better than normalization

### 4.4 Data Balancing & Unbiasing
Addressing class imbalance and bias in datasets.

| Technique | Description | Approach |
|-----------|-------------|----------|
| **Oversampling** | Duplicate minority class samples | Increases minority representation |
| **SMOTE** | Synthesize new minority samples | Creates synthetic examples |
| **Undersampling** | Remove majority class samples | Reduces majority representation |
| **Class weights** | Assign higher weight to minority class | Algorithm-level adjustment |
| **Stratified splitting** | Maintain class proportions in splits | Ensures balanced train/test sets |

**Bias Detection**: Use Amazon SageMaker Clarify to detect pre-training bias in datasets.

### 4.5 Data Augmentation
Artificially increasing dataset size by creating modified versions of existing data.

#### Image Augmentation
| Technique | Description |
|-----------|-------------|
| **Rotation** | Rotate images by random angles |
| **Flipping** | Horizontal/vertical mirror |
| **Cropping** | Random crop sections |
| **Color jittering** | Adjust brightness, contrast, saturation |
| **Scaling/Zooming** | Resize images |
| **Noise injection** | Add random noise |

#### Text Augmentation
| Technique | Description |
|-----------|-------------|
| **Synonym replacement** | Replace words with synonyms |
| **Back translation** | Translate to another language and back |
| **Random insertion/deletion** | Add or remove random words |
| **Paraphrasing** | Rephrase sentences (can use LLMs) |

#### Tabular Data Augmentation
| Technique | Description |
|-----------|-------------|
| **SMOTE** | Synthetic Minority Over-sampling Technique |
| **Noise injection** | Add small random noise to numerical features |
| **Synthetic data generation** | Use generative models to create new samples |

**Why Augment?**
- ✅ Reduces overfitting (acts as regularization)
- ✅ Increases effective dataset size
- ✅ Improves model generalization
- ✅ Helps with class imbalance

### 4.6 Data Labeling
Assigning ground truth labels to data for supervised learning.

| Service | Description | Use Case |
|---------|-------------|----------|
| **SageMaker Ground Truth** | Managed data labeling with human workers | Custom labeling workflows |
| **Ground Truth Plus** | Turnkey managed labeling service with expert workforce | High-quality labels without managing workforce |
| **Amazon Mechanical Turk** | Crowdsourced human workforce | Large-scale simple labeling tasks |

**SageMaker Ground Truth Key Features**:
- **Automated data labeling**: Uses active learning to reduce manual labeling by up to 70%
- **Workforce options**: Amazon Mechanical Turk, private workforce, vendor companies
- **Built-in task types**: Image classification, object detection, semantic segmentation, text classification
- **Annotation consolidation**: Combines multiple workers' labels into one high-fidelity label
- **Custom labeling workflows**: Create custom UIs for specialized tasks
- **RLHF support**: Generate demonstration and preference data for foundation model alignment

**Active Learning Workflow**:
```
Random sample → Human labels → Train model → Model labels confident data
                                            → Send uncertain data back to humans
                                            → Repeat until done
```

## 5. AWS Services for Data Wrangling

### Amazon SageMaker Data Wrangler
- **Purpose**: Visual, low-code data preparation and feature engineering
- **Key Features**:
  - 300+ built-in PySpark transformations
  - Natural language interface (describe transformation in plain English)
  - Import from S3, Athena, Redshift, Snowflake, Databricks
  - Data quality and insights reports
  - Quick Model analysis (estimate feature predictive power)
  - Data flow: visual pipeline of transformation steps
  - Export to S3, SageMaker Pipelines, Feature Store, Python scripts
- **Available in**: SageMaker Studio Classic and SageMaker Canvas
- **Data types**: Tabular, image, and text data
- **Exam Tip**: 🎯 "No-code/low-code data preparation for ML" → Data Wrangler

### Amazon SageMaker Processing
- **Purpose**: Run custom data preprocessing scripts at scale
- **Use Case**: When you need custom transformations beyond Data Wrangler's built-in options
- **Supports**: scikit-learn, Spark, custom containers
- **Exam Tip**: 🎯 "Custom preprocessing scripts at scale" → SageMaker Processing

### Amazon SageMaker Clarify
- **Purpose**: Detect bias in data and explain model predictions
- **Pre-training bias detection**: Analyze dataset for imbalances before training
- **Post-training bias detection**: Analyze model predictions for unfairness
- **Exam Tip**: 🎯 "Detect bias in data" → SageMaker Clarify

### Amazon SageMaker Ground Truth
- **Purpose**: Data labeling service using human workers + ML
- **Exam Tip**: 🎯 "Label training data" or "human annotation" → Ground Truth

### Amazon SageMaker Autopilot
- **Purpose**: AutoML that automatically preprocesses data
- **Data exploration report**: Identifies data quality issues (missing values, outliers, duplicates, correlations)
- **Exam Tip**: 🎯 "Automatic data preprocessing + model training" → Autopilot

### AWS Glue / AWS Glue DataBrew
- **AWS Glue**: Serverless ETL for large-scale data transformation
- **AWS Glue DataBrew**: Visual data preparation tool (general purpose, not ML-specific)
- **Exam Tip**: 🎯 "General data preparation" → Glue DataBrew; "ML-specific data prep" → Data Wrangler

### Amazon EMR
- **Purpose**: Big data processing with Spark/Hadoop
- **Use Case**: Data wrangling at petabyte scale
- **Exam Tip**: 🎯 "Big data preprocessing at scale" → EMR

## 6. Service Comparison: Data Preparation Tools

| Service | Code Required? | ML-Specific? | Scale | Best For |
|---------|---------------|-------------|-------|----------|
| **SageMaker Data Wrangler** | No (visual/NL) | ✅ Yes | Medium-Large | ML data prep, feature engineering |
| **SageMaker Processing** | Yes (scripts) | ✅ Yes | Large | Custom transformations |
| **SageMaker Autopilot** | No (automated) | ✅ Yes | Medium | End-to-end AutoML |
| **AWS Glue DataBrew** | No (visual) | ❌ General | Large | General data cleaning |
| **AWS Glue** | Yes (ETL scripts) | ❌ General | Very Large | ETL pipelines |
| **Amazon EMR** | Yes (Spark/Hadoop) | ❌ General | Petabyte | Big data processing |

## 7. Exam Focus Areas

### Common Question Types
- "How to handle missing values?" → **Imputation** (mean, median, mode) or removal
- "How to prevent data leakage?" → Remove duplicates before splitting, scale after splitting
- "How to label training data?" → **SageMaker Ground Truth**
- "Low-code data preparation for ML?" → **SageMaker Data Wrangler**
- "How to detect bias in data?" → **SageMaker Clarify**
- "How to increase dataset size?" → **Data augmentation**
- "How to handle class imbalance?" → **Oversampling, SMOTE, undersampling, class weights**

### Gotchas
- Data Wrangler ≠ Glue DataBrew (Data Wrangler is ML-specific, DataBrew is general purpose)
- Remove duplicates **before** splitting, not after (prevents data leakage)
- Fit scaler on **training data only**, apply to all sets
- Standardization handles outliers better than normalization
- Not all outliers should be removed (fraud detection needs them)
- Oversampling before splitting causes data leakage (synthetic copies in both train and test)
- Ground Truth uses **active learning** to reduce labeling cost, not just human workers
- Data augmentation is a form of **regularization** (reduces overfitting)

### Decision Tree
```
Need to prepare data for ML?
│
├── Need to label data?
│   ├── Custom labeling workflows → SageMaker Ground Truth
│   ├── Turnkey managed labeling → Ground Truth Plus
│   └── Simple crowdsourced tasks → Amazon Mechanical Turk
│
├── Need to clean/transform data?
│   ├── No-code, ML-specific → SageMaker Data Wrangler
│   ├── No-code, general purpose → AWS Glue DataBrew
│   ├── Custom scripts at scale → SageMaker Processing
│   ├── Large-scale ETL → AWS Glue
│   └── Petabyte-scale → Amazon EMR
│
├── Need to detect bias?
│   └── SageMaker Clarify
│
├── Need automatic preprocessing + training?
│   └── SageMaker Autopilot
│
└── Need to increase dataset size?
    └── Data Augmentation (image: rotation/flip; text: back-translation; tabular: SMOTE)
```

## 8. Best Practices
- **Understand your data first**: Generate data quality reports before transforming (Data Wrangler, Autopilot)
- **Handle missing values intentionally**: Choose imputation strategy based on data distribution and missingness pattern
- **Split before scaling**: Always partition data before applying normalization/standardization
- **Remove duplicates early**: Before any splitting to prevent data leakage
- **Use stratified splitting**: For imbalanced datasets to maintain class proportions
- **Automate pipelines**: Use SageMaker Pipelines to create reproducible data wrangling workflows
- **Version your data**: Track data transformations for reproducibility and auditing
- **Check for bias**: Use SageMaker Clarify before and after training

## 9. Sample Questions

### Question 1
A data scientist discovers that their model performs exceptionally well during training but poorly on new data. Investigation reveals that duplicate records exist across both the training and test datasets. What is this problem called, and how should it be fixed?

A) Overfitting — add more regularization to the model  
B) Data leakage — remove duplicates before splitting the dataset  
C) Underfitting — increase model complexity  
D) Class imbalance — apply SMOTE to the minority class  

**Answer: B) Data leakage — remove duplicates before splitting the dataset**  
Duplicate records across train and test sets cause data leakage — the model has effectively "seen" test data during training, inflating performance metrics. The fix is to remove duplicates before partitioning. While the symptoms resemble overfitting, the root cause is data leakage, not model complexity.

---

### Question 2
A company needs to label 500,000 images for an object detection model. They want to minimize labeling costs while maintaining high quality. Which AWS service and feature should they use?

A) Amazon Rekognition with custom labels  
B) Amazon SageMaker Ground Truth with automated data labeling  
C) Amazon Mechanical Turk with manual labeling only  
D) Amazon SageMaker Data Wrangler with built-in transformations  

**Answer: B) Amazon SageMaker Ground Truth with automated data labeling**  
Ground Truth's automated data labeling uses active learning to train a model on human-labeled samples, then automatically labels confident predictions — reducing manual labeling by up to 70%. Mechanical Turk alone would be expensive at 500K images. Rekognition is for inference, not labeling. Data Wrangler is for data transformation, not labeling.

---

### Question 3
A machine learning team has a dataset where 95% of records belong to class A and 5% to class B. Which combination of techniques would best address this class imbalance?

A) Remove all class A records and train on class B only  
B) Apply SMOTE to oversample class B and use stratified train/test splitting  
C) Normalize all features and increase the number of epochs  
D) Use PCA to reduce dimensionality and apply L2 regularization  

**Answer: B) Apply SMOTE to oversample class B and use stratified train/test splitting**  
SMOTE creates synthetic minority class samples to balance the dataset, and stratified splitting ensures both classes are proportionally represented in train/test sets. Removing all class A records loses valuable data. Normalization and PCA address different problems (scaling and dimensionality, not imbalance).

---

**Exam Tip**: 🎯 For AIF-C01, focus on understanding the data preprocessing pipeline (clean → partition → scale → balance → augment), when to use each AWS service (Data Wrangler vs. Ground Truth vs. Clarify vs. Processing), and how to prevent data leakage. Know that Ground Truth uses active learning and that data augmentation acts as regularization.
