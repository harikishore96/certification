# Amazon Nova

## 1. Service/Topic Overview
- **Purpose**: AWS's family of state-of-the-art foundation models available through Amazon Bedrock, offering multimodal capabilities for text, image, and video understanding and generation
- **Service Level**: Regional (accessed via Amazon Bedrock)
- **Key Use Cases**: 
  - Multimodal content understanding (text, image, video analysis)
  - Video search and summarization
  - Document processing with visual elements
  - Cost-effective text generation and reasoning
  - Creative content generation
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
  - Task 5.1: Explain methods to secure AI systems

## 2. Exam Keyword Mapping
- **Trigger Words**: 
  - "Multimodal AI", "video understanding", "video analysis"
  - "Cost-effective foundation models", "AWS native models"
  - "Image and text understanding", "visual reasoning"
  - "Document understanding with images"
  - "Video search", "video summarization"
  - "Low-cost inference", "budget-friendly AI"
  - "Amazon's own foundation models"
- **Scenario Indicators**: 
  - "Need to analyze video content for insights"
  - "Process documents with charts, diagrams, and images"
  - "Want cost-effective alternative to third-party models"
  - "Require multimodal understanding (text + images + video)"
  - "Building video search or content moderation systems"
  - "Need to extract information from visual content"
  - "Looking for AWS-native foundation models"
- **Anti-patterns**: 
  - Real-time video streaming analysis (use Rekognition Video)
  - Only text generation without multimodal needs (consider other Bedrock models)
  - Training custom models from scratch (use SageMaker)
  - Simple image classification (use Rekognition)
  - When you need the absolute highest accuracy regardless of cost

## 3. Core Concepts
- **Architectural Patterns**: 
  - **Multimodal Analysis**: Application → Nova Model → Process Text/Image/Video → Structured Output
  - **Video Understanding**: Video File (S3) → Nova → Scene Detection/Summarization → Results
  - **Document Intelligence**: PDF/Image → Nova → Extract Text + Visual Elements → Insights
  - **Cost-Optimized Pipeline**: Simple Tasks → Nova Micro/Lite → Complex Tasks → Nova Pro
- **Service Limits**: 
  - Model-specific context windows (varies by Nova variant)
  - Video length limits for processing
  - Image resolution constraints
  - Token limits per request
  - Accessed through Bedrock quotas and limits
- **Data Flow**: 
  - **Input**: Text prompts, images (JPEG, PNG), videos (MP4), or combinations
  - **Processing**: Multimodal understanding, reasoning, and generation
  - **Output**: Text responses, structured data, embeddings, or generated content
- **Integration Points**: 
  - **Amazon Bedrock**: Primary access point for all Nova models
  - **S3**: Store input videos, images, and output results
  - **Lambda**: Build serverless multimodal applications
  - **Step Functions**: Orchestrate complex video processing workflows
  - **CloudWatch**: Monitor usage, latency, and costs
  - **IAM**: Control access to Nova models
  - **API Gateway**: Expose Nova capabilities via REST APIs

## 4. Key Features & Components

### Amazon Nova Model Family

**Nova Micro**:
- **Purpose**: Text-only, ultra-low-cost model for simple tasks
- **Use Cases**: Classification, summarization, basic Q&A
- **Cost**: Lowest cost option in Nova family
- **Context Window**: Optimized for efficiency

**Nova Lite**:
- **Purpose**: Multimodal (text and image), low-cost, fast responses
- **Use Cases**: Image understanding, document processing, visual Q&A
- **Cost**: Very cost-effective for multimodal tasks
- **Context Window**: Supports text and image inputs

**Nova Pro**:
- **Purpose**: Multimodal (text, image, video), highest capability in Nova family
- **Use Cases**: Complex reasoning, video analysis, advanced document understanding
- **Cost**: Higher cost but still competitive with third-party models
- **Context Window**: Largest context window, supports video inputs
- **Video Capabilities**: Process up to 30 minutes of video content

**Nova Canvas** (Image Generation):
- **Purpose**: Generate high-quality images from text prompts
- **Use Cases**: Creative content, marketing materials, product visualization
- **Features**: Style control, aspect ratio options, quality settings

**Nova Reel** (Video Generation):
- **Purpose**: Generate short video clips from text or image prompts
- **Use Cases**: Video content creation, animations, visual storytelling
- **Features**: Camera motion control, scene transitions

### Exam-Tested Features
- **Multimodal Understanding**: 
  - Process text, images, and videos in single request
  - Cross-modal reasoning (e.g., answer questions about video content)
  - Visual element extraction from documents
- **Video Analysis Capabilities**: 
  - Scene detection and segmentation
  - Object and activity recognition in videos
  - Video summarization and key frame extraction
  - Temporal understanding (sequence of events)
- **Cost Optimization**: 
  - Tiered pricing across Micro/Lite/Pro
  - Choose appropriate model for task complexity
  - Significantly lower cost than comparable third-party models
- **Document Intelligence**: 
  - Extract text from images and PDFs
  - Understand charts, graphs, and diagrams
  - Maintain spatial relationships in documents
- **AWS Integration**: 
  - Native AWS service with full security features
  - Seamless integration with other AWS services
  - No data sharing with third parties

### Configuration Options
- **Inference Parameters**: 
  - **Temperature**: Control randomness (0-1)
  - **Top P**: Nucleus sampling
  - **Max Tokens**: Response length limit
  - **Stop Sequences**: Define completion triggers
- **Input Formats**: 
  - Text: Plain text, markdown
  - Images: JPEG, PNG, WebP
  - Videos: MP4, MOV (Nova Pro only)
- **Output Controls**: 
  - Response format (text, JSON)
  - Streaming vs. synchronous responses
  - Quality settings for generation models

### API/SDK Highlights
- **InvokeModel**: Standard inference with multimodal inputs
- **InvokeModelWithResponseStream**: Streaming for real-time applications
- **Converse API**: Simplified conversational interface with multimodal support
- All accessed through Amazon Bedrock APIs

## 5. Exam Focus Areas

### Common Question Types

**Scenario-based selection questions**:
- "A company needs to analyze hours of security camera footage to detect specific events. Which AWS service provides the most cost-effective solution?"
  - Answer: Amazon Nova Pro via Bedrock (video understanding capability)

**Feature comparison questions**:
- "When should you choose Nova Lite vs. Nova Pro?"
  - Nova Lite: Simple multimodal tasks, cost-sensitive, faster responses
  - Nova Pro: Complex reasoning, video analysis, advanced understanding

**Cost optimization questions**:
- "How to minimize costs for a document processing application that handles both simple forms and complex reports?"
  - Answer: Use Nova Micro for simple forms, Nova Lite/Pro for complex documents

**Best practice questions**:
- "What's the recommended approach for processing long videos with Nova?"
  - Answer: Use Nova Pro with video segmentation, process in chunks if needed

### Gotchas
- **Model Selection**: Nova Micro is text-only; Lite and Pro support multimodal
- **Video Length**: Nova Pro has maximum video duration limits (check current limits)
- **Regional Availability**: Nova models available in specific AWS regions
- **Access via Bedrock**: Cannot access Nova directly; must use Bedrock
- **Cost Calculation**: Charged per input/output token, video charged by duration
- **Video Format**: Not all video codecs supported; use standard formats (MP4)
- **Context Window**: Different models have different maximum context lengths
- **Model Updates**: AWS may release new Nova variants; stay updated

### Comparison Points

| Feature | Nova Micro | Nova Lite | Nova Pro | Third-Party Models |
|---------|-----------|-----------|----------|-------------------|
| **Modalities** | Text only | Text + Image | Text + Image + Video | Varies |
| **Cost** | Lowest | Low | Moderate | Higher |
| **Speed** | Fastest | Fast | Moderate | Varies |
| **Complexity** | Simple tasks | Moderate tasks | Complex tasks | Varies |
| **Video Support** | ❌ | ❌ | ✅ | Limited |
| **AWS Native** | ✅ | ✅ | ✅ | ❌ |

**Nova vs. Other Bedrock Models**:
- **Nova vs. Claude**: Nova more cost-effective, Claude better for complex reasoning
- **Nova vs. Titan**: Nova has video capabilities, Titan focused on embeddings/text
- **Nova vs. Stable Diffusion**: Nova Canvas for images, Stable Diffusion alternative

### Decision Trees

**When to choose Amazon Nova**:
- ✅ Need multimodal understanding (text + images + video)
- ✅ Video analysis and summarization required
- ✅ Cost optimization is a priority
- ✅ Processing documents with visual elements
- ✅ Want AWS-native foundation models
- ✅ Building video search or content moderation
- ✅ Need to balance cost and capability

**Which Nova Model to Choose**:
- **Nova Micro**: Simple text tasks, classification, basic summarization
- **Nova Lite**: Image understanding, document processing, visual Q&A, cost-sensitive
- **Nova Pro**: Video analysis, complex reasoning, advanced multimodal tasks

**When NOT to choose Amazon Nova**:
- ❌ Real-time video streaming (use Rekognition Video)
- ❌ Need absolute highest accuracy (consider Claude, GPT-4)
- ❌ Simple image classification (use Rekognition)
- ❌ Speech-to-text (use Transcribe)
- ❌ Training custom models (use SageMaker)

## 6. Best Practices

### Security
- **Access Control**: 
  - Use IAM policies to control access to specific Nova models
  - Implement least privilege access
  - Use resource-based policies for cross-account access
- **Data Protection**: 
  - All data encrypted in transit (TLS) and at rest (KMS)
  - Your data not used to train models
  - No data sharing with third parties
  - Implement Bedrock Guardrails for content filtering
- **Compliance**: 
  - Leverage AWS compliance certifications
  - Enable CloudTrail for audit logging
  - Use VPC endpoints for private connectivity
- **Content Safety**: 
  - Apply Guardrails to filter inappropriate content
  - Implement PII detection and redaction
  - Monitor outputs for quality and safety

### Cost Optimization
- **Pricing Model**: 
  - **Input Tokens**: Charged per 1,000 tokens (text and image)
  - **Output Tokens**: Charged per 1,000 tokens generated
  - **Video**: Charged per minute of video processed
  - **Images Generated**: Charged per image (Canvas)
  - **Videos Generated**: Charged per second (Reel)
- **Cost-Saving Strategies**: 
  - **Right-size Model Selection**: Use Micro for simple tasks, Lite for moderate, Pro for complex
  - **Optimize Prompts**: Reduce unnecessary input tokens
  - **Batch Processing**: Process multiple items in single requests when possible
  - **Cache Results**: Store and reuse results for repeated queries
  - **Video Preprocessing**: Compress videos, extract key frames before processing
  - **Monitor Usage**: Track costs with CloudWatch and Cost Explorer
  - **Set Budgets**: Use AWS Budgets to alert on spending thresholds

### Performance
- **Optimization Techniques**: 
  - **Model Selection**: Choose fastest model that meets accuracy requirements
  - **Streaming Responses**: Use streaming for better user experience
  - **Parallel Processing**: Process multiple videos/images concurrently
  - **Video Optimization**: Reduce resolution/frame rate if acceptable
  - **Prompt Engineering**: Clear, concise prompts improve response quality
  - **Caching**: Implement caching layer for repeated queries
- **Scaling Considerations**: 
  - On-demand inference scales automatically
  - Request quota increases for high-volume workloads
  - Use provisioned throughput for predictable performance (if available)
  - Implement retry logic with exponential backoff
  - Distribute load across multiple models if needed

### Operations
- **Monitoring**: 
  - CloudWatch metrics: Invocations, latency, errors, token usage
  - Track video processing duration and costs
  - Set alarms for throttling and error rates
  - Monitor model performance and accuracy
- **Logging**: 
  - Enable CloudTrail for API call auditing
  - Log model inputs/outputs for debugging (mind PII)
  - Use model invocation logging for compliance
  - Track video processing results
- **Maintenance**: 
  - Test applications when new Nova versions release
  - Update prompts based on model improvements
  - Review and optimize model selection periodically
  - Monitor AWS announcements for new capabilities
- **Troubleshooting**: 
  - Verify video format compatibility
  - Check service quotas if throttled
  - Review IAM permissions for access issues
  - Test with smaller inputs to isolate problems
  - Validate input file sizes and formats

## 7. Hands-On Labs

### Quick Setup
1. Open AWS Console → Amazon Bedrock
2. Navigate to Model Access → Request Access
3. Enable Amazon Nova models (Micro, Lite, Pro, Canvas, Reel)
4. Wait for approval (typically instant)
5. Go to Playgrounds → Text/Image/Video to experiment

### Practical Exercises

**Exercise 1: Document Understanding with Nova Lite**
```python
import boto3
import json
import base64

bedrock = boto3.client('bedrock-runtime', region_name='us-east-1')

# Read image file
with open('invoice.png', 'rb') as f:
    image_bytes = f.read()
    image_base64 = base64.b64encode(image_bytes).decode('utf-8')

# Prepare multimodal request
request_body = {
    "messages": [
        {
            "role": "user",
            "content": [
                {
                    "image": {
                        "format": "png",
                        "source": {"bytes": image_base64}
                    }
                },
                {
                    "text": "Extract the invoice number, date, total amount, and vendor name from this invoice."
                }
            ]
        }
    ],
    "inferenceConfig": {
        "temperature": 0,
        "maxTokens": 500
    }
}

response = bedrock.invoke_model(
    modelId='amazon.nova-lite-v1:0',
    body=json.dumps(request_body)
)

result = json.loads(response['body'].read())
print(result['output']['message']['content'][0]['text'])
```

**Exercise 2: Video Analysis with Nova Pro**
```python
import boto3
import json

bedrock = boto3.client('bedrock-runtime', region_name='us-east-1')

# Video stored in S3
request_body = {
    "messages": [
        {
            "role": "user",
            "content": [
                {
                    "video": {
                        "format": "mp4",
                        "source": {
                            "s3Location": {
                                "uri": "s3://my-bucket/security-footage.mp4"
                            }
                        }
                    }
                },
                {
                    "text": "Analyze this security footage and identify: 1) Number of people, 2) Any suspicious activities, 3) Timestamp of key events"
                }
            ]
        }
    ],
    "inferenceConfig": {
        "temperature": 0.3,
        "maxTokens": 1000
    }
}

response = bedrock.invoke_model(
    modelId='amazon.nova-pro-v1:0',
    body=json.dumps(request_body)
)

result = json.loads(response['body'].read())
print(result['output']['message']['content'][0]['text'])
```

**Exercise 3: Cost-Optimized Text Processing with Nova Micro**
```python
import boto3
import json

bedrock = boto3.client('bedrock-runtime', region_name='us-east-1')

# Simple classification task
request_body = {
    "messages": [
        {
            "role": "user",
            "content": [
                {
                    "text": "Classify the following customer feedback as Positive, Negative, or Neutral: 'The product arrived on time but the packaging was damaged.'"
                }
            ]
        }
    ],
    "inferenceConfig": {
        "temperature": 0,
        "maxTokens": 10
    }
}

response = bedrock.invoke_model(
    modelId='amazon.nova-micro-v1:0',
    body=json.dumps(request_body)
)

result = json.loads(response['body'].read())
print(f"Classification: {result['output']['message']['content'][0]['text']}")
```

### CLI Commands
```bash
# List available Nova models
aws bedrock list-foundation-models --by-provider amazon --region us-east-1

# Invoke Nova Lite with text
aws bedrock-runtime invoke-model \
    --model-id amazon.nova-lite-v1:0 \
    --body '{"messages":[{"role":"user","content":[{"text":"What is AI?"}]}],"inferenceConfig":{"maxTokens":200}}' \
    --region us-east-1 \
    output.json

# Check model access status
aws bedrock get-foundation-model --model-identifier amazon.nova-pro-v1:0 --region us-east-1
```

### Console Walkthrough
1. **Bedrock Console** → Playgrounds → Chat
2. Select **Amazon Nova Lite** or **Nova Pro**
3. Upload an image or video (Pro only)
4. Enter prompt: "Describe what you see in this image/video"
5. Adjust temperature and max tokens
6. Click **Run** and review results
7. Compare costs across Nova Micro/Lite/Pro
8. Test with different prompt engineering techniques

## 8. Sample Questions

**Question 1**: A media company needs to build a video search system that allows users to find specific scenes within thousands of hours of archived footage. The solution must be cost-effective and provide accurate results. Which AWS service and model combination is MOST appropriate?

A) Amazon Rekognition Video with custom labels  
B) Amazon SageMaker with a custom-trained video model  
C) Amazon Bedrock with Amazon Nova Pro  
D) Amazon Transcribe with Amazon Comprehend

**Correct Answer: C**

**Explanation**:
- **C is correct**: Amazon Nova Pro via Bedrock provides native video understanding capabilities, can process and analyze video content, supports video search use cases, and is cost-effective compared to building custom solutions. Nova Pro can understand temporal sequences, detect scenes, and answer questions about video content.
- **A is incorrect**: Rekognition Video is excellent for real-time video analysis and specific object/face detection but is not optimized for semantic video search and understanding complex queries about video content.
- **B is incorrect**: While SageMaker could be used to train a custom model, this would require significant ML expertise, training data, time, and cost. It's not the most appropriate solution when a pre-trained foundation model like Nova Pro can handle the task.
- **D is incorrect**: Transcribe converts speech to text and Comprehend analyzes text, but this combination cannot understand visual content in videos, only audio. It would miss all visual information.

---

**Question 2**: A financial services company processes thousands of documents daily, including simple forms and complex reports with charts and graphs. They want to minimize costs while maintaining accuracy. What is the BEST approach using Amazon Nova models?

A) Use Amazon Nova Pro for all documents to ensure highest accuracy  
B) Use Amazon Nova Micro for all documents to minimize costs  
C) Use Amazon Nova Lite for simple forms and Nova Pro for complex reports with visual elements  
D) Use Amazon Titan for text extraction and Nova Pro for charts

**Correct Answer: C**

**Explanation**:
- **C is correct**: This approach optimizes both cost and accuracy by right-sizing the model to the task complexity. Nova Lite is cost-effective and sufficient for simple forms with basic visual elements, while Nova Pro provides advanced understanding for complex reports with intricate charts and graphs. This tiered approach minimizes overall costs.
- **A is incorrect**: Using Nova Pro for all documents would be unnecessarily expensive for simple forms that don't require advanced reasoning capabilities. This violates cost optimization best practices.
- **B is incorrect**: Nova Micro is text-only and cannot process visual elements like charts and graphs. It would fail to extract information from documents with visual components.
- **D is incorrect**: While Titan can handle text, this approach adds unnecessary complexity by using two different models. Nova Lite/Pro can handle both text and visual elements in a single request, simplifying the architecture.

---

**Question 3**: A startup is building a content moderation system that needs to analyze user-uploaded images and videos for inappropriate content. They have a limited budget and need to process content quickly. Which combination of AWS services provides the MOST cost-effective solution?

A) Amazon Rekognition for images and videos with custom moderation labels  
B) Amazon Bedrock with Nova Lite for images and Nova Pro for videos, plus Bedrock Guardrails  
C) Amazon SageMaker with custom-trained moderation models  
D) Amazon Comprehend for text analysis with Lambda for image processing

**Correct Answer: B**

**Explanation**:
- **B is correct**: Nova Lite provides cost-effective image understanding, Nova Pro handles video analysis, and Bedrock Guardrails offers built-in content filtering capabilities. This combination is specifically designed for cost-conscious startups and provides comprehensive multimodal content moderation. The integrated Guardrails feature simplifies implementation.
- **A is incorrect**: While Rekognition is excellent for content moderation, it can be more expensive than Nova models for this use case, especially for video processing. Custom labels also require training data and additional setup time.
- **C is incorrect**: Training custom models with SageMaker requires ML expertise, labeled training data, significant time investment, and ongoing infrastructure costs. This is not cost-effective for a startup with limited budget.
- **D is incorrect**: Comprehend only analyzes text and cannot process images or videos. Lambda would require custom code for image processing, which is complex and not as effective as purpose-built foundation models.

---

## Exam Tips

**Key Takeaways for AIF-C01**:
1. **Nova is AWS's native foundation model family** - emphasize cost-effectiveness and AWS integration
2. **Understand the three tiers**: Micro (text-only, cheapest), Lite (multimodal, balanced), Pro (video, most capable)
3. **Video understanding is Nova Pro's unique capability** - key differentiator from other models
4. **Cost optimization through model selection** - right-sizing is a common exam theme
5. **Multimodal = text + images + video** - Nova Lite and Pro support multiple input types
6. **Accessed via Amazon Bedrock** - not a standalone service
7. **Compare with other Bedrock models** - know when to choose Nova vs. Claude vs. Titan
8. **Security and compliance** - same as Bedrock (encryption, IAM, no data sharing)

**Common Exam Scenarios**:
- Video analysis/search → Nova Pro
- Cost-effective document processing → Nova Lite
- Simple text classification → Nova Micro
- Multimodal understanding → Nova Lite or Pro
- Budget constraints mentioned → Consider Nova over third-party models
