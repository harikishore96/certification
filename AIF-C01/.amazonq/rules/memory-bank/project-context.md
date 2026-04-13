# AIF-C01 Certification Study Project - Memory Bank

## Project Overview
- **Purpose**: Study notes and preparation materials for AWS Certified AI Practitioner (AIF-C01) exam
- **Owner**: Delta Air Lines employee preparing for AWS AI certification
- **Structure**: Organized by service categories (Generative AI, ML Services)
- **Status**: Active development with 6 documented services and growing revision backlog

## Project Structure
```
Notes/
├── .amazonq/rules/          # Amazon Q configuration and rules
├── Generative-AI-Services/  # Foundation models and generative AI
│   ├── Amazon-Bedrock.md
│   └── Amazon-Nova.md
├── ML-Services/             # Traditional ML services
│   ├── Amazon-SageMaker.md
│   ├── Amazon-SageMaker-Assets.md
│   ├── Amazon-SageMaker-Model-Cards.md
│   └── Amazon-SageMaker-Studio.md
├── INDEX.md                 # Master index of all notes
├── ML_Hyperparameters.md    # Core ML concepts
└── revision-backlog.md      # Topics requiring additional study
```

## Documentation Standards

### File Template Structure
Each service note follows this 8-section format:
1. **Service/Topic Overview** - Purpose, use cases, exam weight, domains
2. **Exam Keyword Mapping** - Trigger words, scenarios, anti-patterns
3. **Core Concepts** - Architecture, limits, data flow, integrations
4. **Key Features & Components** - Detailed feature breakdown
5. **Exam Focus Areas** - Question types, gotchas, comparisons, decision trees
6. **Best Practices** - Security, cost, performance, operations
7. **Hands-On Labs** - Setup, exercises, CLI commands
8. **Sample Questions** - Practice questions with detailed explanations

### Content Guidelines
- Focus on AWS AI/ML services and exam-relevant concepts
- Include practical examples and code snippets
- Emphasize responsible AI principles (fairness, explainability, privacy)
- Cover generative AI concepts (foundation models, prompt engineering, RAG, fine-tuning)
- Highlight service comparisons and decision criteria
- Provide cost optimization and security considerations

## Exam Context

### AIF-C01 Exam Domains
1. **Fundamentals of AI and ML** (20%)
2. **Fundamentals of Generative AI** (24%)
3. **Applications of Foundation Models** (28%)
4. **Guidelines for Responsible AI** (14%)
5. **Security, Compliance, and Governance** (14%)

### Key AWS Services in Scope
- **Generative AI**: Bedrock, Nova, Q
- **ML Platform**: SageMaker (Studio, Training, Inference, Autopilot, Clarify, Model Monitor)
- **AI Services**: Rekognition, Comprehend, Translate, Transcribe, Polly, Lex, Kendra, Personalize, Forecast, Fraud Detector
- **Developer Tools**: CodeWhisperer

## Current Coverage

### Completed (6 files)
- Amazon Bedrock (comprehensive)
- Amazon Nova
- Amazon SageMaker (comprehensive)
- Amazon SageMaker Assets
- Amazon SageMaker Model Cards
- Amazon SageMaker Studio

### Revision Backlog Topics
Core ML concepts requiring documentation:
- Model Fit, Bias, and Variance
- Learning paradigms (Supervised, Unsupervised, Self-Supervised, Reinforcement, RLHF)
- Evaluation metrics (Confusion Matrix, Precision, Recall, F1, Accuracy, AUC-ROC)
- Regression metrics (MAE, MAPE, RMSE, R²)
- Data/Feature Augmentation
- Hyperparameters (Learning Rate, Batch Size, Epochs, Regularization)

## Key Patterns and Conventions

### Service Comparison Framework
When comparing services, use this structure:
- Use case differentiation
- Expertise level required
- Customization capabilities
- Time to deploy
- Cost model
- Infrastructure management

### Decision Trees
Format: ✅ When to use / ❌ When NOT to use
- Clear, actionable criteria
- Specific scenarios
- Alternative service recommendations

### Code Examples
- Include imports and dependencies
- Use boto3 for AWS SDK examples
- Provide both Python and CLI examples where applicable
- Keep examples minimal and focused

## Integration Points

### Cross-Service Relationships
- **Bedrock ↔ SageMaker**: Fine-tuning foundation models
- **S3**: Primary data storage for all ML services
- **IAM**: Access control across all services
- **CloudWatch**: Monitoring and logging
- **Lambda**: Serverless integration and triggers
- **Step Functions**: ML workflow orchestration

### Common Architecture Patterns
1. **RAG Pattern**: Query → Knowledge Base → Retrieve → FM → Response
2. **ML Pipeline**: Data (S3) → Processing → Training → Model → Deployment
3. **Inference**: Real-time endpoints, Batch transform, Serverless
4. **MLOps**: Pipelines → Model Registry → Monitoring → Retraining

## Study Priorities

### High Priority (Exam Weight 28%)
- Foundation model applications (Bedrock)
- Prompt engineering techniques
- RAG implementations
- Model deployment strategies

### Medium-High Priority (Exam Weight 24%)
- Generative AI fundamentals
- Foundation model capabilities
- Model customization (fine-tuning vs. continued pre-training)

### Medium Priority (Exam Weight 20%)
- ML fundamentals and terminology
- ML development lifecycle
- Evaluation metrics and model performance

### Important Throughout (14% each)
- Responsible AI (bias detection, explainability, fairness)
- Security, compliance, and governance

## Terminology Standards

### Consistent Naming
- **Foundation Models (FMs)** - not "large language models" unless specific
- **Inference** - not "prediction" in generative AI context
- **Fine-tuning** - specific customization with labeled data
- **Continued Pre-training** - domain adaptation with unlabeled data
- **RAG** - Retrieval Augmented Generation (always spell out first use)
- **Hyperparameters** - configuration set before training
- **Model Parameters** - learned during training (weights, biases)

### AWS-Specific Terms
- **Provisioned Throughput** - reserved capacity (Bedrock)
- **Managed Spot Training** - cost-optimized training (SageMaker)
- **Guardrails** - content filtering and safety (Bedrock)
- **Knowledge Bases** - managed RAG implementation (Bedrock)
- **Agents** - autonomous task execution (Bedrock)

## File Naming Conventions
- Use PascalCase with hyphens: `Amazon-Service-Name.md`
- Descriptive names for concept files: `ML_Hyperparameters.md`
- Special files: `INDEX.md`, `revision-backlog.md`

## Next Steps
1. Document revision backlog topics (ML fundamentals)
2. Add remaining AWS AI services (Rekognition, Comprehend, etc.)
3. Create domain-specific study guides for each exam domain
4. Develop practice question sets
5. Build quick reference cheat sheets
