# Amazon Bedrock

## 1. Service/Topic Overview
- **Purpose**: Fully managed service that provides access to foundation models (FMs) from leading AI companies through a single API
- **Service Level**: Regional
- **Key Use Cases**: 
  - Text generation and conversational AI applications
  - Content creation and summarization
  - Code generation and analysis
  - Image generation from text prompts
  - Building RAG (Retrieval Augmented Generation) applications
- **Exam Weight**: High
- **Exam Domain**: 
  - Domain 2: Fundamentals of Generative AI (24%) - Primary
  - Domain 3: Applications of Foundation Models (28%) - Primary
  - Domain 5: Security, Compliance, and Governance for AI Solutions (14%)
- **Task Statements**: 
  - Task 2.1: Explain the basic concepts of generative AI
  - Task 2.2: Understand the capabilities of foundation models
  - Task 3.1: Describe design considerations for applications that use foundation models
  - Task 3.2: Choose effective prompt engineering techniques
  - Task 3.3: Describe the training and fine-tuning process for foundation models
  - Task 5.2: Recognize governance and compliance regulations for AI systems

## 2. Exam Keyword Mapping
- **Trigger Words**: 
  - "Foundation models", "FMs", "pre-trained models"
  - "Generative AI", "text generation", "image generation"
  - "Prompt engineering", "inference"
  - "Claude", "Titan", "Jurassic", "Stable Diffusion"
  - "Serverless", "fully managed AI"
  - "Model customization", "fine-tuning"
  - "RAG", "Knowledge Bases"
  - "Agents", "autonomous AI"
- **Scenario Indicators**: 
  - "Need to quickly deploy generative AI without managing infrastructure"
  - "Want to experiment with multiple foundation models"
  - "Require enterprise-grade security and privacy for AI workloads"
  - "Need to customize models with proprietary data"
  - "Building chatbots or conversational interfaces"
  - "Generating content, summaries, or creative text"
  - "Integrating external data sources with LLMs"
- **Anti-patterns**: 
  - Training models from scratch (use SageMaker instead)
  - Real-time computer vision tasks like object detection (use Rekognition)
  - Speech-to-text conversion (use Transcribe)
  - Traditional ML with structured data (use SageMaker)
  - When you need full control over model architecture

## 3. Core Concepts
- **Architectural Patterns**: 
  - **Direct Inference**: Application → Bedrock API → Foundation Model → Response
  - **RAG Pattern**: Query → Knowledge Base → Retrieve Context → FM with Context → Response
  - **Agent Pattern**: User Request → Agent → Orchestration → Action Groups/Knowledge Bases → Response
  - **Custom Model**: Base FM → Fine-tuning with Custom Data → Custom Model → Inference
- **Service Limits**: 
  - Model-specific token limits (varies by model: 8K-200K tokens)
  - Requests per minute quotas (adjustable via Service Quotas)
  - Maximum training data size for fine-tuning
  - Provisioned throughput available for consistent performance
- **Data Flow**: 
  - **Input**: Text prompts, images (for multimodal models), parameters (temperature, top_p, max_tokens)
  - **Processing**: Model inference on AWS infrastructure, no data used for model training
  - **Output**: Generated text, embeddings, or images based on model type
- **Integration Points**: 
  - **S3**: Store training data, knowledge base documents, model outputs
  - **Lambda**: Trigger inference, build serverless AI applications
  - **API Gateway**: Expose Bedrock capabilities via REST APIs
  - **CloudWatch**: Monitor API calls, latency, errors
  - **IAM**: Control access to models and features
  - **KMS**: Encrypt data at rest and in transit
  - **OpenSearch/Vector Databases**: Store embeddings for RAG applications
  - **Step Functions**: Orchestrate complex AI workflows

## 4. Key Features & Components

### Foundation Models Available
- **Amazon Titan**: AWS's own models for text generation, embeddings, and image generation
- **Anthropic Claude**: Advanced reasoning, long context windows (100K+ tokens), constitutional AI
- **AI21 Labs Jurassic**: Multilingual text generation
- **Cohere**: Text generation, embeddings, classification
- **Meta Llama**: Open-source models for various tasks
- **Stability AI**: Image generation (Stable Diffusion)

### Exam-Tested Features
- **Model Inference**: 
  - On-demand inference (pay per token)
  - Provisioned throughput (reserved capacity)
  - Batch inference for large-scale processing
- **Knowledge Bases for Amazon Bedrock**: 
  - Fully managed RAG implementation
  - Automatic chunking, embedding, and vector storage
  - Supports S3 as data source, OpenSearch/Aurora as vector store
- **Agents for Amazon Bedrock**: 
  - Autonomous agents that can plan and execute tasks
  - Action groups (Lambda functions agents can call)
  - Automatic prompt engineering and orchestration
- **Model Customization**: 
  - **Fine-tuning**: Adapt models with labeled data (supervised learning)
  - **Continued Pre-training**: Expose models to unlabeled domain-specific data
  - Custom models remain private to your account
- **Guardrails for Amazon Bedrock**: 
  - Content filtering (hate, violence, sexual content, etc.)
  - Denied topics configuration
  - Word and phrase filters
  - PII redaction
  - Apply to both inputs and outputs
- **Model Evaluation**: 
  - Automatic evaluation with built-in metrics
  - Human-based evaluation workflows
  - Compare models side-by-side

### Configuration Options
- **Inference Parameters**: 
  - **Temperature**: Controls randomness (0=deterministic, 1=creative)
  - **Top P**: Nucleus sampling threshold
  - **Top K**: Limits token selection pool
  - **Max Tokens**: Maximum length of generated response
  - **Stop Sequences**: Tokens that halt generation
- **Security Settings**: 
  - VPC endpoints for private connectivity
  - Encryption with AWS KMS
  - IAM policies for fine-grained access control
  - CloudTrail logging for audit trails

### API/SDK Highlights
- **InvokeModel**: Synchronous inference for single requests
- **InvokeModelWithResponseStream**: Streaming responses for real-time applications
- **CreateModelCustomizationJob**: Start fine-tuning process
- **RetrieveAndGenerate**: RAG inference using Knowledge Bases
- **InvokeAgent**: Execute agent tasks

## 5. Exam Focus Areas

### Common Question Types

**Scenario-based selection questions**:
- "A company wants to build a chatbot that can answer questions about their product documentation. Which AWS service should they use?"
  - Answer: Amazon Bedrock with Knowledge Bases for RAG

**Feature comparison questions**:
- "What's the difference between fine-tuning and continued pre-training in Bedrock?"
  - Fine-tuning: Uses labeled data, supervised learning, task-specific
  - Continued pre-training: Uses unlabeled data, domain adaptation

**Troubleshooting questions**:
- "Generated responses contain inappropriate content. What Bedrock feature should be implemented?"
  - Answer: Guardrails for Amazon Bedrock

**Best practice questions**:
- "How to ensure consistent performance for a production generative AI application?"
  - Answer: Use Provisioned Throughput instead of on-demand inference

### Gotchas
- **Data Privacy**: Your data is NOT used to train base models; it stays within your account
- **Model Availability**: Not all models available in all regions
- **Context Windows**: Different models have different maximum context lengths
- **Cost Structure**: Charged per input/output token, not per request
- **Fine-tuning Persistence**: Custom models are separate billable resources
- **Guardrails Latency**: Adding guardrails increases response time
- **Knowledge Base Sync**: Changes to S3 data require re-syncing the knowledge base

### Comparison Points

| Feature | Amazon Bedrock | Amazon SageMaker | AWS AI Services |
|---------|---------------|------------------|-----------------|
| **Use Case** | Generative AI with FMs | Custom ML models | Specific AI tasks |
| **Model Training** | Fine-tuning only | Full training from scratch | Pre-trained, no training |
| **Infrastructure** | Fully managed, serverless | Managed instances | Fully managed |
| **Customization** | Limited (fine-tuning) | Complete control | None |
| **Time to Deploy** | Minutes | Hours to days | Minutes |
| **Expertise Required** | Prompt engineering | ML/data science | API integration |

### Decision Trees

**When to choose Amazon Bedrock**:
- ✅ Need generative AI capabilities (text, image generation)
- ✅ Want to use pre-trained foundation models
- ✅ Require quick deployment without ML expertise
- ✅ Need to experiment with multiple models
- ✅ Building chatbots, content generation, summarization
- ✅ Implementing RAG patterns

**When NOT to choose Amazon Bedrock**:
- ❌ Training custom models from scratch → Use SageMaker
- ❌ Computer vision tasks (object detection, facial recognition) → Use Rekognition
- ❌ Speech services → Use Transcribe/Polly
- ❌ Traditional ML with tabular data → Use SageMaker
- ❌ Need complete control over model architecture → Use SageMaker

## 6. Best Practices

### Security
- **IAM Policies**: 
  - Grant least privilege access to specific models
  - Use resource-based policies for cross-account access
  - Separate permissions for inference vs. customization
- **Encryption**: 
  - Data encrypted in transit (TLS)
  - Data encrypted at rest with KMS (customer or AWS managed keys)
  - Custom models encrypted with your KMS keys
- **Network Isolation**: 
  - Use VPC endpoints for private connectivity
  - Avoid exposing Bedrock APIs to public internet
- **Data Protection**: 
  - Implement Guardrails to filter sensitive content
  - Use PII redaction features
  - Enable CloudTrail for audit logging
  - Your prompts and completions are not shared with model providers

### Cost Optimization
- **Pricing Model — 4 Service Tiers** (as of 2025):
  - **Standard Tier** (default): Pay per 1M input/output tokens, consistent performance for everyday AI tasks
  - **Priority Tier**: Standard rate + 75% premium; preferential processing, up to 25% better OTPS latency; for mission-critical, latency-sensitive apps
  - **Flex Tier**: 50% discount off Standard; higher latency tolerated; for non-urgent workloads (evaluations, summarizations, agentic workflows)
  - **Reserved Tier**: Fixed price per 1K tokens-per-minute; 1-month or 3-month commitments; guaranteed capacity for consistent throughput needs
- **Additional Pricing Options**:
  - **Batch Inference**: Up to 50% lower price vs. on-demand; asynchronous processing of large datasets via S3
  - **Provisioned Throughput**: Dedicated Model Units (MUs) billed hourly; no-commitment, 1-month, or 6-month terms (longer = cheaper); for consistent high-volume workloads
  - **Model Customization**: Training cost (per token or per image trained) + custom model storage ($1.95/month) + inference cost
  - **Knowledge Bases**: Indexing fees (per object or OCU-hour) + query costs ($0.002/GenerateQuery for structured data)
  - **Guardrails**: Per text unit (1 text unit = up to 1,000 characters); content filters $0.15/1K units, PII redaction $0.10/1K units, Automated Reasoning $0.17/1K units
  - **Custom Model Import**: Per-minute metering in 5-min windows (only active windows billed) + storage per Custom Model Unit
  - **Prompt Caching**: Cache write premium + discounted cache read rate (model-specific)
- **Exam-Critical Pricing Formulas**:
  - On-Demand: `(input tokens / 1M) × input price + (output tokens / 1M) × output price`
  - Provisioned Throughput monthly: `# MUs × price/hour/MU × 24 hours × 31 days`
  - Guardrails: `# text units × price per 1K text units / 1000`
- **Cost-Saving Strategies**:
  - Use smaller/cheaper models for simpler tasks (Titan Lite vs. Claude, Ministral 3B vs. Mistral Large)
  - **Model Distillation**: Transfer knowledge from large "teacher" to small "student" model — up to 500% faster, 75% cheaper, <2% accuracy loss
  - **Intelligent Prompt Routing**: Automatically route to optimal model based on complexity
  - Optimize prompt length to reduce input tokens
  - **Prompt Caching**: Eliminate re-processing identical context; reduces cost and latency
  - Use Batch inference for non-real-time workloads (50% savings)
  - Use Flex tier for non-urgent workloads (50% savings)
  - Monitor with inference-level Cost Allocation Tags and Application Inference Profiles
  - Track costs with CloudWatch, Cost Explorer, and AWS Budgets
  - Only include relevant data in Knowledge Bases to minimize indexing costs
  - Avoid unnecessary re-indexing (modified files get re-indexed)
  - Choose appropriate commitment term for Provisioned Throughput (6-month = cheapest)

### Performance
- **Optimization Techniques**: 
  - Use streaming responses for better user experience
  - Implement prompt caching for repeated contexts
  - Choose models with appropriate context windows
  - Use embeddings models (Titan Embeddings) for semantic search
  - Optimize chunk size in Knowledge Bases (default: 300 tokens)
- **Scaling Considerations**: 
  - On-demand scales automatically but has rate limits
  - Provisioned throughput for predictable performance
  - Request quota increases proactively for production
  - Use multiple models for load distribution
  - Implement retry logic with exponential backoff

### Operations
- **Monitoring**: 
  - CloudWatch metrics: Invocations, latency, errors, token usage
  - Set alarms for throttling and error rates
  - Track cost with Cost Explorer and Budgets
- **Logging**: 
  - Enable CloudTrail for API call logging
  - Log prompts and responses for debugging (be mindful of PII)
  - Use model invocation logging for compliance
- **Maintenance**: 
  - Test applications when new model versions release
  - Regularly update Knowledge Bases with new data
  - Review and update Guardrails policies
  - Monitor model performance and accuracy
- **Troubleshooting**: 
  - Check service quotas if experiencing throttling
  - Verify IAM permissions for access issues
  - Review prompt structure for poor outputs
  - Test different inference parameters
  - Check model availability in your region

## 7. Hands-On Labs

### Quick Setup
1. Open AWS Console → Amazon Bedrock
2. Request model access (one-time setup per model)
3. Wait for approval (instant for most models)
4. Navigate to Playgrounds → Text or Image
5. Select a model and start experimenting

### Practical Exercises

**Exercise 1: Text Generation with Prompt Engineering**
```python
import boto3
import json

bedrock = boto3.client('bedrock-runtime', region_name='us-east-1')

prompt = """Human: Write a professional email to a customer explaining a 2-day shipping delay.