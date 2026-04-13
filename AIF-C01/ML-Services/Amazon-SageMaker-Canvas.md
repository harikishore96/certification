# Amazon SageMaker Canvas

## 1. Service/Topic Overview
- **Purpose**: No-code machine learning service that enables business users to build, train, evaluate, and deploy ML models using a visual point-and-click interface without writing any code
- **Service Level**: Regional (available in 16 AWS Regions)
- **Key Use Cases**:
  - Predict customer churn and lifetime value
  - Plan inventory and demand forecasting
  - Classify text or images based on custom categories
  - Extract information from documents
  - Generate content using foundation models (LLMs)
- **Exam Weight**: Medium
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — no-code ML development lifecycle
  - Domain 3: Applications of Foundation Models (28%) — FM access and fine-tuning
  - Domain 5: Security, Compliance, and Governance (14%) — MLOps governance
- **Task Statements**:
  - Task 1.3: Describe the ML development lifecycle
  - Task 3.2: Select appropriate FM deployment strategies
  - Task 5.2: Recognize governance and compliance requirements for AI systems

## 2. Exam Keyword Mapping
- **Trigger Words**: "no-code ML", "business analyst", "visual interface", "non-technical users", "point-and-click", "AutoML without code", "business users build ML models"
- **Scenario Indicators**:
  - "A marketing team wants to predict customer churn without writing code"
  - "Business analysts need to build ML models without data science expertise"
  - "Non-technical users need to generate predictions from tabular data"
  - "A team wants to use foundation models through a visual interface"
- **Anti-patterns**:
  - ❌ Need full control over training algorithms → Use SageMaker Studio/Training
  - ❌ Need custom deep learning architectures → Use SageMaker with custom containers
  - ❌ Need to process millions of records in real-time → Use SageMaker endpoints directly
  - ❌ Need programmatic API access for ML pipeline → Use SageMaker Pipelines
  - ❌ Need only pre-built AI capabilities (no custom model) → Use AWS AI Services directly (Rekognition, Comprehend, etc.)

## 3. Core Concepts

### Architectural Patterns
```
Business User → SageMaker Canvas (Visual UI)
                    ├── Chat with Amazon Q Developer (guided ML)
                    ├── Import Data (50+ sources)
                    ├── Prepare Data (300+ no-code transforms)
                    ├── Build Model (AutoML)
                    │     ├── Custom Models (regression, classification, forecasting, CV, NLP)
                    │     ├── Ready-to-Use Models (Rekognition, Textract, Comprehend)
                    │     └── Foundation Models (Bedrock + JumpStart)
                    ├── Evaluate Model
                    ├── Generate Predictions (batch, real-time, what-if)
                    └── MLOps (Model Registry, share with Studio)
```

### Service Limits
- Workspace instance: Charged per hour ($1.90/hr base)
- Free tier: 160 hours/month workspace usage
- Supports tabular, image, text, and document data types
- File formats: CSV, Parquet, ORC, JSON
- Data sources: 50+ connectors (S3, Athena, Redshift, Snowflake, Databricks, local files)

### Data Flow
```
Data Sources (S3, Redshift, Snowflake, etc.)
    → Import & Prepare (300+ PySpark transforms, Data Quality Report)
        → Build Model (AutoML tests hundreds of algorithms)
            → Evaluate (accuracy metrics, feature importance)
                → Predict (batch, real-time, what-if)
                    → Share (QuickSight dashboards, Model Registry, Studio)
```

### Integration Points
| Service | Integration |
|---------|------------|
| **SageMaker Studio** | Share models bidirectionally with data scientists |
| **SageMaker Model Registry** | Register models for CI/CD deployment pipelines |
| **SageMaker Pipelines** | Export data prep flows for pipeline integration |
| **Amazon Bedrock** | Access foundation models (Claude, Titan, Llama, etc.) |
| **SageMaker JumpStart** | Access open-source FMs (Falcon, Mistral, Flan-T5) |
| **Amazon Rekognition** | Powers ready-to-use CV models (object detection) |
| **Amazon Textract** | Powers ready-to-use document extraction models |
| **Amazon Comprehend** | Powers ready-to-use NLP models (sentiment, entities) |
| **Amazon QuickSight** | Build predictive dashboards from model predictions |
| **Amazon S3** | Primary data storage and import source |
| **Amazon Redshift** | Data warehouse connector |
| **Amazon Athena** | Query data via SQL |
| **Amazon Q Developer** | Chat-guided ML development assistance |

## 4. Key Features & Components

### Chat-Driven ML Development (Amazon Q Developer)
- Translate business problems into ML workflows using natural language
- Guided step-by-step model development (data prep → build → evaluate → deploy)
- Explains ML terms in non-technical language
- Deterministic pipeline builder ensures reproducibility
- Maintains artifacts: original/transformed datasets, data prep pipelines
- Export model notebooks for further customization

### Data Preparation
- **50+ data source connectors**: S3, Redshift, Snowflake, Databricks, Athena, local files
- **300+ prebuilt PySpark-based transforms**: No-code data transformation
- **Data Quality & Insight Report**: One-click report detecting missing values, duplicates, outliers, class imbalance, data leakage
- **Data Pipelines**: Schedule/automate data preparation workflows
- **SQL support**: Write queries to access data sources directly
- **FM-powered chat**: Use natural language to prepare data

### Model Building
- **Custom Models (AutoML)**:
  - Regression (numeric prediction)
  - Binary classification (2 categories)
  - Multi-class classification (3+ categories)
  - Time series forecasting
  - Image classification (single-label)
  - Multi-class text classification
  - Tests hundreds of ML algorithms automatically (linear regression, logistic regression, deep learning, gradient boosting)
- **Ready-to-Use Models** (no training required):
  - Sentiment analysis (Comprehend)
  - Object detection in images (Rekognition)
  - Text detection in images (Rekognition)
  - Entity extraction (Comprehend)
  - Document text extraction (Textract)
- **Foundation Models**:
  - Via Bedrock: Claude, Titan, Llama, Jurassic, Command
  - Via JumpStart: Falcon, Flan-T5, Mistral, Dolly, MPT
  - Fine-tune FMs with your own data
  - Content generation, text extraction, summarization

### Predictions & Deployment
- **What-if analysis**: Change inputs interactively, see prediction impact
- **Batch predictions**: Automated predictions on entire datasets with scheduling
- **Real-time inference**: Deploy to SageMaker endpoints
- **QuickSight integration**: Predictive dashboards combining BI + ML predictions

### MLOps & Governance
- **SageMaker Model Registry**: One-click model registration for CI/CD integration
- **Model sharing**: Bidirectional sharing between Canvas ↔ SageMaker Studio
- **Granular permissions**: User-level access controls
- **Model versioning**: Track model iterations
- **SSO support**: AWS SSO, Active Directory, Okta, SAML, OpenID Connect
- **Notebook export**: Export model code for data scientist review

## 5. Exam Focus Areas

### Common Question Types
- **Scenario-based**: "A business analyst needs to predict customer churn without coding" → Canvas
- **Feature comparison**: Canvas vs. SageMaker Studio vs. SageMaker Autopilot
- **Best practice**: How to enable collaboration between business users and data scientists
- **Governance**: How Canvas models integrate into MLOps pipelines

### Gotchas
- ⚠️ Canvas uses **AutoML under the hood** — it's not just a simple UI, it tests hundreds of algorithms
- ⚠️ Ready-to-use models are **powered by AWS AI Services** (Rekognition, Textract, Comprehend) — not custom-trained
- ⚠️ Canvas can access **foundation models from both Bedrock AND JumpStart**
- ⚠️ Canvas is **not free** — workspace instance charges apply ($1.90/hr), but has a 160 hr/month free tier
- ⚠️ Canvas models **can be shared with SageMaker Studio** — enabling collaboration between business users and data scientists
- ⚠️ Canvas supports **fine-tuning foundation models** — not just inference

### Comparison Points

| Feature | SageMaker Canvas | SageMaker Autopilot | SageMaker Studio |
|---------|-----------------|--------------------|--------------------|
| **Target User** | Business analysts | Data scientists (AutoML) | Data scientists/ML engineers |
| **Code Required** | None | Minimal (notebooks) | Full coding |
| **Interface** | Visual point-and-click | Notebook + API | Full IDE |
| **AutoML** | ✅ Built-in | ✅ Core feature | Manual or Autopilot |
| **Foundation Models** | ✅ Chat + fine-tune | ❌ | ✅ Via JumpStart |
| **Ready-to-Use Models** | ✅ (Rekognition, etc.) | ❌ | ❌ |
| **Custom Algorithms** | ❌ | Limited | ✅ Full control |
| **MLOps Integration** | Model Registry + sharing | Model Registry | Full pipeline control |
| **Data Prep** | 300+ no-code transforms | Basic | Full (Data Wrangler) |

### Decision Trees
- ✅ **Use Canvas when**:
  - Business users/analysts need to build ML models
  - No coding expertise available
  - Need quick predictions from tabular, image, or text data
  - Want to use foundation models through a visual interface
  - Need to collaborate with data scientists (share models to Studio)
- ❌ **Don't use Canvas when**:
  - Need custom deep learning architectures
  - Need full programmatic control over training
  - Need to process data at massive scale with custom infrastructure
  - Building complex ML pipelines with custom steps

## 6. Best Practices

### Security
- Use **IAM policies** to control Canvas access per user
- Enable **SSO** (AWS SSO, Active Directory, Okta) for enterprise access
- Encrypt data at rest using **AWS KMS**
- Store data in **S3 with encryption** before importing
- Use **VPC configurations** to restrict network access
- Enable **CloudTrail** for audit logging of Canvas activities

### Cost Optimization
- **Free tier**: 160 hours/month workspace usage — monitor usage to stay within limits
- **Log out when not in use**: Workspace charges accrue while logged in ($1.90/hr)
- Use **Quick Build** for rapid prototyping (faster, less accurate) vs. **Standard Build** for production (slower, more accurate)
- Use **ready-to-use models** when possible — no training cost, only inference charges
- Monitor costs via **AWS Cost Explorer** and set **AWS Budgets** alerts
- Use **batch predictions** instead of real-time endpoints when latency isn't critical

### Performance
- Ensure **clean, high-quality data** — use Data Quality & Insight Report
- Remove **duplicate rows** and handle **missing values** before training
- Use **Standard Build** for production models (tests more algorithms)
- Leverage **300+ built-in transforms** for feature engineering
- For time-series: ensure sufficient historical data points

### Operations
- **Register models** in SageMaker Model Registry for version control
- **Share models** with data scientists in Studio for review and improvement
- **Schedule batch predictions** for automated recurring forecasts
- **Export notebooks** for code transparency and reproducibility
- Use **Amazon Q Developer** for guided troubleshooting

## 7. Hands-On Labs

### Quick Setup
1. Open AWS Console → Amazon SageMaker → Canvas
2. Create a Canvas application (or access via SageMaker Studio)
3. Upload a CSV dataset (e.g., customer churn data)
4. Select target column → Canvas auto-detects problem type
5. Click **Quick Build** or **Standard Build**
6. Review model accuracy → Generate predictions

### Practical Exercises
1. **Customer Churn Prediction**: Upload customer data CSV → select "Churn" as target → build classification model → analyze feature importance → generate batch predictions
2. **Time Series Forecasting**: Import sales data → select revenue column → configure forecast horizon → build forecasting model → visualize predictions
3. **Foundation Model Chat**: Access FM chat → prompt Claude/Titan for text summarization → compare model responses → fine-tune with custom data

### CLI Commands
```bash
# Canvas is primarily a visual service, but you can manage it via CLI

# List Canvas apps
aws sagemaker list-apps --domain-id <domain-id> --app-type Canvas

# Describe Canvas app
aws sagemaker describe-app --domain-id <domain-id> \
  --user-profile-name <profile> --app-type Canvas --app-name default

# Create a Canvas-compatible SageMaker domain
aws sagemaker create-domain --domain-name my-canvas-domain \
  --auth-mode IAM --default-user-settings '{"ExecutionRole":"arn:aws:iam::<account>:role/SageMakerRole"}' \
  --subnet-ids <subnet-id> --vpc-id <vpc-id>
```

### Console Walkthrough
1. **Navigate**: AWS Console → SageMaker → Canvas (left sidebar)
2. **Import Data**: Datasets tab → Import → Select source (S3, local, Redshift, etc.)
3. **Prepare Data**: Select dataset → Data Quality Report → Apply transforms
4. **Build Model**: My Models → New Model → Select dataset → Choose target → Build
5. **Evaluate**: Review accuracy, column impact (feature importance), advanced metrics
6. **Predict**: Single prediction (what-if) or Batch prediction → Download results
7. **Share**: Model Registry → Register | Share with Studio users

## 8. Sample Questions

### Question 1
A retail company wants its business analysts to predict which products will have high demand next quarter. The analysts have no coding experience but have access to historical sales data in Amazon S3. Which AWS service should they use?

A) Amazon SageMaker Studio with custom notebooks  
B) Amazon SageMaker Canvas  
C) Amazon Forecast  
D) Amazon Comprehend  

**Correct Answer: B**

**Explanation**: SageMaker Canvas is designed for non-technical business users to build ML models using a visual, no-code interface. It supports time series forecasting and can import data directly from S3. Option A requires coding expertise. Option C (Amazon Forecast) is a dedicated forecasting service but requires API/SDK usage. Option D is for NLP, not forecasting.

---

### Question 2
A data science team wants to review and improve ML models built by business analysts in Amazon SageMaker Canvas. How can they collaborate?

A) Export the model as a pickle file and share via email  
B) Share the Canvas model with SageMaker Studio for data scientist review  
C) Recreate the model from scratch in SageMaker Studio  
D) Download the training data and retrain in a Jupyter notebook  

**Correct Answer: B**

**Explanation**: SageMaker Canvas supports bidirectional model sharing with SageMaker Studio. Business analysts can share their Canvas models directly with data scientists in Studio, who can then review, update, and improve the models. Models can also be registered in the SageMaker Model Registry for CI/CD integration. The other options are unnecessarily complex and don't leverage the built-in collaboration features.

---

### Question 3
A company is using Amazon SageMaker Canvas and wants to use pre-trained models for sentiment analysis and object detection without building custom models. Which statement is correct?

A) Canvas ready-to-use models require training on company data before use  
B) Canvas ready-to-use models are powered by AWS AI services like Amazon Comprehend and Amazon Rekognition  
C) Canvas ready-to-use models are only available for tabular data  
D) Canvas ready-to-use models require a SageMaker endpoint for inference  

**Correct Answer: B**

**Explanation**: SageMaker Canvas provides ready-to-use models powered by AWS AI services — Amazon Comprehend (sentiment analysis, entity extraction), Amazon Rekognition (object detection, text detection in images), and Amazon Textract (document extraction). These models do not require any training; users simply import their data and start generating predictions. Option A is incorrect because ready-to-use models are pre-trained. Option C is wrong because they support CV and NLP use cases. Option D is incorrect because predictions can be generated directly in Canvas.

---

**Exam Tip**: When you see "no-code", "business analyst", "visual interface", or "non-technical users" building ML models in an exam question, think **SageMaker Canvas**. Remember that Canvas is distinct from SageMaker Autopilot (which targets data scientists wanting AutoML) and SageMaker Studio (which is a full IDE for ML engineers). The key differentiator is the **target audience** — Canvas is for business users who cannot write code.
