# Amazon Rekognition

## 1. Service/Topic Overview
- **Purpose**: Fully managed computer vision service that analyzes images and videos using deep learning
- **Service Level**: Regional
- **Key Use Cases**: 
  - Face detection and recognition
  - Object and scene detection
  - Content moderation
  - Celebrity recognition
  - Text detection in images (OCR)
  - Video analysis and tracking
- **Exam Weight**: HIGH
- **Exam Domain**: 
  - Domain 1: Fundamentals of AI and ML (20%)
  - Domain 3: Applications of Foundation Models (28%)
  - Domain 4: Guidelines for Responsible AI (14%)
  - Domain 5: Security, Compliance, and Governance (14%)
- **HIPAA Eligible**: Yes
- **Task Statements**: 
  - Task 1.2: Identify practical use cases for AI
  - Task 3.1: Describe capabilities of AI services
  - Task 4.1: Explain responsible AI principles
  - Task 5.2: Recognize security and compliance requirements

## 2. Exam Keyword Mapping
- **Trigger Words**: 
  - "Analyze images" or "Image analysis"
  - "Face detection" or "Facial recognition"
  - "Object detection"
  - "Content moderation"
  - "Video analysis"
  - "Celebrity recognition"
  - "Text in images" or "OCR"
  - "PPE detection" (Personal Protective Equipment)
- **Scenario Indicators**:
  - "Identify faces in photos" → Face Detection/Recognition
  - "Detect inappropriate content" → Content Moderation
  - "Find objects in images" → Object/Scene Detection
  - "Verify user identity with selfie" → Face Comparison
  - "Track people in video" → Video Analysis
  - "Read text from signs" → Text Detection
  - "Ensure workers wear helmets" → PPE Detection
  - "Verify user is live, not a photo" → Face Liveness
  - "Detect people/pets/packages on camera" → Streaming Video Events
  - "Find black frames or end credits in video" → Video Segmentation
  - "Analyze image quality, color, sharpness" → Image Properties
  - "Process large batch of images for moderation" → Bulk Analysis (StartMediaAnalysisJob)
- **Anti-patterns**:
  - ❌ Don't use for document text extraction (use Textract)
  - ❌ Don't use for medical image analysis (use custom SageMaker models)
  - ❌ Don't use for text generation (use Bedrock)
  - ❌ Don't use for structured document forms/tables (use Textract)
  - ❌ Don't use for speech-to-text (use Transcribe)

## 3. Core Concepts

### Service Architecture
```
Image/Video Input
    ↓
Amazon Rekognition
├── Image Analysis
│   ├── Object & Scene Detection
│   ├── Face Detection & Analysis
│   ├── Face Comparison
│   ├── Face Search (Collections)
│   ├── Celebrity Recognition
│   ├── Text Detection (OCR)
│   ├── Content Moderation
│   ├── PPE Detection
│   └── Custom Labels
│
└── Video Analysis
    ├── Object & Activity Detection
    ├── Face Detection & Tracking
    ├── Person Tracking
    ├── Celebrity Recognition
    ├── Content Moderation
    └── Text Detection
    ↓
Structured Insights (JSON)
```

### API Operation Types

| Type | Description | Persistence | Examples |
|------|-------------|-------------|----------|
| **Non-storage** | No data persisted by Rekognition | None | DetectLabels, DetectFaces, CompareFaces, DetectModerationLabels, DetectText, RecognizeCelebrities, DetectProtectiveEquipment |
| **Storage-based** | Face vectors stored in collections | Face metadata in collections | IndexFaces, SearchFaces, SearchFacesByImage, CreateCollection, CreateUser, AssociateFaces |

> **Key**: Collections store face **vectors** (mathematical representations), NOT actual images.

### Integration Points
- **S3**: Primary storage for images/videos
- **Lambda**: Trigger analysis on upload
- **Kinesis Video Streams**: Real-time streaming video analysis
- **Kinesis Data Streams**: Output for streaming face search results
- **CloudWatch**: Monitoring and logging
- **IAM**: Access control
- **SNS**: Notifications for async video analysis completion
- **SQS**: Decouple video analysis job processing
- **DynamoDB**: Store face collection metadata
- **Step Functions**: Orchestrate complex workflows
- **Amazon A2I (Augmented AI)**: Human review for content moderation
- **AWS PrivateLink**: VPC endpoint support for private connectivity

### Data Flow
```
Image Analysis (Synchronous):
S3/Base64 Bytes → Rekognition Image API → JSON Results (< 1 second)

Stored Video Analysis (Asynchronous):
S3 Video → Start* API → SNS Notification → Get* API → JSON Results

Streaming Video Analysis (Real-time):
Kinesis Video Streams → Stream Processor → SNS/S3 (labels) or Kinesis Data Stream (faces)

Bulk Image Analysis (Asynchronous):
Manifest File (S3) → StartMediaAnalysisJob → Results in S3
```

## 4. Key Features & Components

### Image Analysis Features

#### Object and Scene Detection
- **Purpose**: Identify objects, scenes, activities, and concepts
- **Detects**: 
  - Objects: Car, person, dog, phone (1000+ labels)
  - Scenes: Beach, office, sunset
  - Activities: Running, eating, playing
  - Concepts: Outdoors, celebration
- **Output**: Labels with confidence scores and bounding boxes
- **Use Cases**:
  - Image cataloging
  - Content-based search
  - Automated tagging
  - Scene understanding

#### Face Detection and Analysis
- **Purpose**: Detect faces and analyze facial attributes
- **Detects**:
  - Face location (bounding box)
  - Facial landmarks (eyes, nose, mouth)
  - Attributes: Age range, gender, emotions, glasses, beard, eyes open/closed
  - Face quality (brightness, sharpness)
- **Does NOT**: Identify who the person is (use Face Search for that)
- **Use Cases**:
  - Demographic analysis
  - Emotion detection
  - Photo organization
  - User experience personalization

#### Face Comparison
- **Purpose**: Compare two faces to determine if they're the same person
- **Input**: Source face + target face
- **Output**: Similarity score (0-100) and confidence
- **Threshold**: Typically 80+ for high confidence match
- **Use Cases**:
  - Identity verification
  - User authentication
  - Duplicate detection
  - Access control

#### Face Search (Face Collections)
- **Purpose**: Search for known faces in a collection
- **How It Works**:
  1. Create face collection
  2. Index faces with metadata (user ID, name)
  3. Search new images against collection
  4. Get matches with similarity scores
- **Limits**: 
  - Up to 20 million faces per collection
  - 100 faces per image
- **Use Cases**:
  - Employee identification
  - VIP recognition
  - Missing person search
  - Photo organization by person

#### Celebrity Recognition
- **Purpose**: Identify celebrities in images/videos
- **Database**: Pre-built database of celebrities
- **Output**: Celebrity name, confidence, face location, URLs
- **Use Cases**:
  - Media monitoring
  - Content tagging
  - Social media analysis
  - Event coverage

#### Text Detection (OCR)
- **Purpose**: Detect and extract text from images
- **Detects**:
  - Printed text
  - Scene text (signs, labels, billboards)
  - Skewed or rotated text
- **Output**: Detected text, confidence, bounding box, text type (LINE/WORD)
- **Languages**: Latin script, Arabic, Russian, etc.
- **Use Cases**:
  - License plate reading
  - Sign translation
  - Document digitization (simple)
  - Street view text extraction
- **Note**: For complex documents, use Textract instead

#### Content Moderation
- **Purpose**: Detect inappropriate, unwanted, or offensive content
- **Categories**:
  - Explicit Nudity
  - Suggestive content
  - Violence
  - Visually Disturbing content
  - Rude Gestures
  - Drugs, Tobacco, Alcohol
  - Hate Symbols
  - Gambling
- **Output**: Labels with confidence scores
- **Confidence Thresholds**: Adjustable (e.g., 75% for auto-block, 50-75% for review)
- **Use Cases**:
  - Social media moderation
  - User-generated content filtering
  - Compliance enforcement
  - Brand safety

#### PPE Detection (Personal Protective Equipment)
- **Purpose**: Detect if people are wearing required safety equipment
- **Detects**:
  - Face covers (masks)
  - Head covers (hard hats, helmets)
  - Hand covers (gloves)
- **Output**: Person location, body parts, PPE coverage status
- **Use Cases**:
  - Workplace safety compliance
  - Construction site monitoring
  - Healthcare facility compliance
  - Manufacturing safety

#### Custom Labels
- **Purpose**: Train custom models for domain-specific image classification and object detection
- **Types**:
  - Image classification: Categorize entire image
  - Object detection: Locate and classify objects
- **Requirements**:
  - Minimum 10 images per label (50+ recommended)
  - Labeled training data
  - SageMaker Ground Truth for labeling
- **Training**: Automated (no ML expertise required)
- **Pricing**: Per training hour + per inference hour
- **Use Cases**:
  - Manufacturing defect detection
  - Brand logo detection
  - Custom product recognition
  - Industry-specific classification

#### Custom Moderation (Adapters)
- **Purpose**: Enhance accuracy of the base content moderation model with your own images
- **How It Works**: Train a custom adapter on your annotated images → adapter enhances base model predictions
- **Difference from Custom Labels**: Adapters fine-tune the existing moderation model; Custom Labels trains entirely new models
- **Use Cases**:
  - Domain-specific moderation (e.g., gaming, fashion)
  - Reducing false positives for your content type
  - Industry-specific content policies

#### Image Properties
- **Purpose**: Analyze image quality and visual characteristics
- **Detects**: Sharpness, brightness, contrast, dominant colors
- **API**: DetectLabels with `IMAGE_PROPERTIES` parameter
- **Availability**: Image only (not video)
- **Use Cases**: Image quality filtering, color-based search, photo organization

#### Face Liveness Detection
- **Purpose**: Verify a live person is present during face-based identity verification
- **Detects Spoofs**:
  - Printed photos
  - Digital photos/videos on screens
  - 3D masks
  - Pre-recorded or deepfake videos injected into video capture
- **How It Works**: User takes a short video selfie → returns Liveness score (0-100)
- **Higher score** = greater confidence the person is live
- **APIs**: CreateFaceLivenessSession → StartFaceLivenessSession → GetFaceLivenessSessionResults
- **Use Cases**:
  - Online identity verification (banking, fintech)
  - Remote onboarding
  - Preventing fraud in face-based authentication

#### Bulk Analysis (StartMediaAnalysisJob)
- **Purpose**: Process large collections of images asynchronously
- **Input**: Manifest file in S3 listing all images to process
- **Operations**: Content moderation at scale
- **Use Cases**: Moderating large image libraries, batch processing user-generated content

### Video Analysis Features

#### Video Object and Activity Detection
- **Purpose**: Detect objects, activities, and scenes in videos
- **Output**: Timestamps, labels, confidence scores
- **Use Cases**:
  - Video cataloging
  - Content-based search
  - Highlight generation
  - Automated tagging

#### Face Detection and Tracking
- **Purpose**: Detect and track faces throughout video
- **Output**: Face locations, attributes, timestamps
- **Tracking**: Maintains face ID across frames
- **Use Cases**:
  - Video analytics
  - Audience measurement
  - Security monitoring

#### Person Tracking
- **Purpose**: Track people's movement through video
- **Output**: Person ID, bounding box, timestamps
- **Maintains**: Consistent person ID even when occluded
- **Use Cases**:
  - Retail analytics
  - Crowd monitoring
  - Security applications

#### Video Content Moderation
- **Purpose**: Detect inappropriate content in videos
- **Output**: Timestamps of inappropriate content
- **Use Cases**:
  - Video platform moderation
  - Live stream monitoring
  - Compliance enforcement

#### Video Segmentation
- **Purpose**: Detect useful segments in video content
- **Detects**:
  - Black frames
  - End credits
  - Color bars/test patterns
  - Shot changes
  - Opening credits
- **Output**: Frame-accurate SMPTE timecodes and timestamps
- **API**: StartSegmentDetection / GetSegmentDetection
- **Use Cases**:
  - Automated ad insertion points
  - Content preparation for streaming
  - Binge marker placement
  - Media asset management

### Streaming Video Analysis
- **Purpose**: Real-time analysis of video streams from connected cameras
- **Integration**: Amazon Kinesis Video Streams → Stream Processor
- **Capabilities**:
  - **Label detection**: Detect people, pets, packages in real-time
  - **Face search**: Match faces against collections in real-time
- **Output**: Detected object, bounding box, zoomed-in image, timestamp
- **Connected Home**: Low-cost, low-latency detection for security cameras
- **Use Cases**:
  - Smart home security (person/pet/package detection)
  - Live face recognition at entry points
  - Real-time security monitoring
  - Retail analytics

## 5. Exam Focus Areas

### Common Question Types

#### Scenario-Based Selection
**Question Pattern**: "A company needs to [vision task]. Which AWS service should they use?"

**Decision Framework**:
```
Vision/Image Task?
│
├─ General image/video analysis → Rekognition
├─ Document text extraction → Textract
├─ Medical image analysis → SageMaker (custom)
├─ Satellite imagery → SageMaker (custom)
├─ Manufacturing defects → Rekognition Custom Labels or Lookout for Vision
└─ 3D object detection → Custom SageMaker model
```

#### Feature Comparison Questions
| Task | Rekognition Feature | Training Required | Use Case |
|------|---------------------|-------------------|----------|
| Detect faces | Face Detection | No | Photo organization |
| Identify specific person | Face Search | Yes (index faces) | Employee ID |
| Verify identity | Face Comparison | No | Authentication |
| Find inappropriate content | Content Moderation | No | Social media |
| Detect custom objects | Custom Labels | Yes | Brand logo detection |
| Read text from signs | Text Detection | No | Street view |
| Detect safety equipment | PPE Detection | No | Workplace safety |

### Gotchas
1. **Face Detection ≠ Face Recognition**: Detection finds faces; recognition identifies who they are
2. **Text Detection ≠ Textract**: Rekognition for scene text (signs, labels); Textract for structured documents (forms, tables)
3. **Video Analysis is Asynchronous**: Stored video uses Start/Get pattern; only streaming is real-time
4. **Custom Labels Needs Training**: Minimum 10 images per label (50+ recommended)
5. **Custom Moderation ≠ Custom Labels**: Adapters enhance the base moderation model; Custom Labels trains new models
6. **Confidence Thresholds**: Adjust based on use case (high for auto-action, lower for human review)
7. **Face Collection Limits**: 20M faces per collection
8. **Collections store vectors, NOT images**: Face collections contain mathematical representations, not actual photos
9. **Image operations are synchronous; stored video operations are asynchronous**
10. **Face Liveness requires video selfie**: Not just a static image — detects spoofs including deepfakes
11. **Image Properties only for images**: Sharpness, brightness, contrast, dominant colors not available for video
12. **Gender predictions are binary**: Based on physical appearance, not gender identity — use responsibly
13. **Non-storage APIs don't persist data**: Only storage-based APIs (face collections) retain information

### Comparison Points

#### Rekognition vs. Textract
```
Rekognition Text Detection
├─ Purpose: Scene text (signs, labels)
├─ Format: Simple text in images
├─ Output: Text strings with locations
└─ Use: Street signs, product labels

Textract
├─ Purpose: Document text extraction
├─ Format: Forms, tables, documents
├─ Output: Structured data with relationships
└─ Use: Invoices, forms, receipts
```

#### Face Detection vs. Face Comparison vs. Face Search
```
Face Detection
├─ Task: Find faces in image
├─ Output: Face locations + attributes
├─ Training: None required
└─ Use: "Are there faces?"

Face Comparison
├─ Task: Compare two faces
├─ Output: Similarity score
├─ Training: None required
└─ Use: "Are these the same person?"

Face Search
├─ Task: Find known faces
├─ Output: Matched identities
├─ Training: Index faces in collection
└─ Use: "Who is this person?"
```

#### Rekognition Custom Labels vs. SageMaker vs. Lookout for Vision
```
Rekognition Custom Labels
├─ Expertise: No ML knowledge required
├─ Training: Automated
├─ Data: 10+ images per label
├─ Use Case: Simple custom vision tasks (logos, products)
└─ Cost: Pay per training hour + inference hour

Amazon Lookout for Vision
├─ Expertise: No ML knowledge required
├─ Training: Automated
├─ Data: As few as 30 images
├─ Use Case: Industrial defect detection (anomaly detection)
└─ Cost: Pay per training hour + inference

SageMaker
├─ Expertise: ML knowledge required
├─ Training: Full control over algorithms
├─ Data: Flexible requirements
├─ Use Case: Complex custom models, full flexibility
└─ Cost: Full control over resources
```

#### Content Moderation vs. Custom Moderation
```
Content Moderation (Base)
├─ Pre-built, no training needed
├─ 3-tier label taxonomy (L1/L2/L3)
├─ Categories: Explicit, Violence, Drugs, Hate Symbols, etc.
└─ Use: General-purpose moderation

Custom Moderation (Adapters)
├─ Requires training with your annotated images
├─ Enhances base model accuracy for your domain
├─ Reduces false positives/negatives
└─ Use: Domain-specific moderation policies
```

### Decision Trees

#### Identity Verification
```
✅ Use Face Comparison
- Compare selfie with ID photo
- Get similarity score
- No need to store faces
- Simple 1:1 comparison

❌ Don't use Face Search
- Overkill for 1:1 comparison
- Requires creating collection
- More complex setup
```

#### Employee Access Control
```
✅ Use Face Search with Collections
- Index all employee faces
- Search in real-time
- Maintain employee database
- 1:N comparison

❌ Don't use Face Comparison
- Can't scale to many employees
- Would need to compare against each employee
```

#### Content Moderation
```
✅ Use Rekognition Content Moderation
- Pre-built, no training
- Covers multiple inappropriate categories
- Adjustable confidence thresholds
- Fast and cost-effective

❌ Don't use Custom Labels
- Content Moderation already handles this
- No need for custom training
```

## 6. Best Practices

### Security
- **IAM Policies**: Least privilege access
- **Face Collections**: Encrypt with KMS
- **Data Retention**: Delete faces when no longer needed
- **Privacy**: Inform users of face recognition use
- **Compliance**: Follow GDPR, BIPA, and local regulations
- **Audit**: Enable CloudTrail logging

### Cost Optimization
- **Pricing Model**: Pay-per-use, tiered pricing based on volume
- **Free Tier**: 1,000 images/month (Image), 1,000 face/user vectors (storage), 2 training hours/month (Custom Labels/Moderation), 1 inference hour/month
- **Image Size**: Resize images before analysis (max 15MB)
- **Batch Processing**: Use Bulk Analysis (StartMediaAnalysisJob) for large image collections
- **Confidence Thresholds**: Filter low-confidence results to reduce downstream processing
- **Custom Labels**: Stop training when accuracy plateaus; stop inference endpoints when not in use
- **Face Collections**: Remove unused faces; face metadata storage charged monthly ($0.00001/face/month)
- **Video Sampling**: Analyze key frames instead of entire video when possible
- **Streaming Video**: Only pay for video processed from Kinesis Video Streams

### Performance
- **Image Formats**: .jpg or .png for Image API; Base64 bytes or S3 for input
- **Image Quality**: Higher quality = better accuracy
- **Face Size**: Faces should be at least 40x40 pixels
- **Lighting**: Good lighting improves accuracy
- **Angle**: Front-facing images work best
- **Resolution**: Minimum 80 pixels for smallest dimension
- **Video Frame Rate**: 5-30 fps optimal
- **Text Detection**: Up to 100 words per image
- **Custom Vocabularies**: Use Amazon Translate to convert English labels to other languages

### Operations
- **Monitoring**: 
  - CloudWatch metrics: API calls, errors, latency
  - Set alarms for error rates
- **Error Handling**: Retry with exponential backoff
- **Testing**: Validate accuracy with test dataset
- **Thresholds**: Tune confidence thresholds for use case
- **Face Collection Management**: Regular cleanup and optimization

## 7. Hands-On Labs

### Quick Setup: Object Detection
```python
import boto3

# Initialize Rekognition client
rekognition = boto3.client('rekognition', region_name='us-east-1')

# Detect labels in image
response = rekognition.detect_labels(
    Image={'S3Object': {'Bucket': 'my-bucket', 'Name': 'photo.jpg'}},
    MaxLabels=10,
    MinConfidence=75
)

print("Detected labels:")
for label in response['Labels']:
    print(f"  {label['Name']}: {label['Confidence']:.2f}%")
```

### Practical Exercise 1: Face Detection
```python
# Detect faces and attributes
response = rekognition.detect_faces(
    Image={'S3Object': {'Bucket': 'my-bucket', 'Name': 'group-photo.jpg'}},
    Attributes=['ALL']
)

print(f"Found {len(response['FaceDetails'])} faces")
for i, face in enumerate(response['FaceDetails'], 1):
    print(f"\nFace {i}:")
    print(f"  Age range: {face['AgeRange']['Low']}-{face['AgeRange']['High']}")
    print(f"  Gender: {face['Gender']['Value']} ({face['Gender']['Confidence']:.1f}%)")
    print(f"  Emotions: {face['Emotions'][0]['Type']} ({face['Emotions'][0]['Confidence']:.1f}%)")
```

### Practical Exercise 2: Face Search with Collections
```python
# Create face collection
rekognition.create_collection(CollectionId='employee-faces')

# Index a face
response = rekognition.index_faces(
    CollectionId='employee-faces',
    Image={'S3Object': {'Bucket': 'my-bucket', 'Name': 'employee-123.jpg'}},
    ExternalImageId='employee-123',
    DetectionAttributes=['ALL']
)

print(f"Indexed {len(response['FaceRecords'])} faces")

# Search for face
search_response = rekognition.search_faces_by_image(
    CollectionId='employee-faces',
    Image={'S3Object': {'Bucket': 'my-bucket', 'Name': 'unknown-person.jpg'}},
    MaxFaces=5,
    FaceMatchThreshold=80
)

print("\nMatches found:")
for match in search_response['FaceMatches']:
    print(f"  {match['Face']['ExternalImageId']}: {match['Similarity']:.2f}% similar")
```

### Practical Exercise 3: Content Moderation
```python
# Moderate image content
response = rekognition.detect_moderation_labels(
    Image={'S3Object': {'Bucket': 'my-bucket', 'Name': 'user-upload.jpg'}},
    MinConfidence=50
)

if response['ModerationLabels']:
    print("Inappropriate content detected:")
    for label in response['ModerationLabels']:
        print(f"  {label['Name']}: {label['Confidence']:.2f}%")
        print(f"    Parent: {label['ParentName']}")
else:
    print("No inappropriate content detected")
```

### CLI Commands
```bash
# Detect labels
aws rekognition detect-labels \
  --image '{"S3Object":{"Bucket":"my-bucket","Name":"photo.jpg"}}' \
  --max-labels 10 \
  --min-confidence 75

# Detect faces
aws rekognition detect-faces \
  --image '{"S3Object":{"Bucket":"my-bucket","Name":"photo.jpg"}}' \
  --attributes "ALL"

# Compare faces
aws rekognition compare-faces \
  --source-image '{"S3Object":{"Bucket":"my-bucket","Name":"source.jpg"}}' \
  --target-image '{"S3Object":{"Bucket":"my-bucket","Name":"target.jpg"}}' \
  --similarity-threshold 80

# Start video analysis
aws rekognition start-label-detection \
  --video '{"S3Object":{"Bucket":"my-bucket","Name":"video.mp4"}}' \
  --notification-channel "SNSTopicArn=arn:aws:sns:us-east-1:123456789012:MyTopic,RoleArn=arn:aws:iam::123456789012:role/RekognitionRole"
```

## 8. Sample Questions

### Question 1: Feature Selection
**Scenario**: A social media platform wants to automatically detect and flag user-uploaded images that contain inappropriate content before they're published. The system should identify explicit nudity, violence, and hate symbols.

Which Amazon Rekognition feature is MOST appropriate?

A) Custom Labels trained on inappropriate content  
B) Content Moderation API  
C) Object and Scene Detection  
D) Face Detection with emotion analysis

**Correct Answer**: B) Content Moderation API

**Explanation**:
- ✅ **B is correct**: Content Moderation API is pre-built specifically for detecting inappropriate content including explicit nudity, violence, hate symbols, and other categories. No training required, and it provides confidence scores for each category, allowing adjustable thresholds for auto-blocking vs. human review.

- ❌ **A is wrong**: Custom Labels would require training data of inappropriate content (difficult and potentially problematic to collect) and unnecessary since Content Moderation already handles this use case.

- ❌ **C is wrong**: Object and Scene Detection identifies general objects and scenes but isn't specialized for detecting inappropriate content categories.

- ❌ **D is wrong**: Face Detection analyzes facial attributes but doesn't detect inappropriate content types like nudity or violence.

### Question 2: Face Recognition Approach
**Scenario**: An airport wants to implement a system where passengers can use facial recognition for boarding. Each passenger's face should be compared against their passport photo stored in the system. There are approximately 50,000 passengers per day.

Which approach is MOST appropriate?

A) Use Face Detection to find faces in both images  
B) Use Face Comparison for 1:1 verification  
C) Use Face Search with a collection of all passenger faces  
D) Use Celebrity Recognition to identify passengers

**Correct Answer**: C) Use Face Search with a collection of all passenger faces

**Explanation**:
- ✅ **C is correct**: Face Search with collections is designed for 1:N matching scenarios. Index all passenger passport photos in a collection with their booking reference, then search the live camera image against the collection to find the matching passenger. This scales to millions of faces and provides fast lookups.

- ❌ **B is wrong**: Face Comparison is for 1:1 verification (comparing two specific images). With 50K passengers, you'd need to know which passport photo to compare against first, making this impractical for the boarding scenario.

- ❌ **A is wrong**: Face Detection only finds faces and analyzes attributes; it doesn't identify or match people.

- ❌ **D is wrong**: Celebrity Recognition uses a pre-built database of celebrities, not custom passenger photos.

### Question 3: Responsible AI
**Scenario**: A retail company wants to use Amazon Rekognition for analyzing customer demographics in stores to optimize product placement. They're concerned about privacy and responsible AI practices.

Which practices should they implement? (Choose TWO)

A) Use Face Detection to analyze age range and gender without storing face images  
B) Create a Face Collection to track individual customers across visits  
C) Display clear signage informing customers about the facial analysis  
D) Use the data to make hiring decisions based on customer demographics  
E) Share individual customer face data with third-party advertisers

**Correct Answers**: A and C

**Explanation**:
- ✅ **A is correct**: Face Detection can analyze demographic attributes (age range, gender) without identifying or storing individual faces. This provides useful aggregate data while respecting privacy.

- ✅ **C is correct**: Transparency is a key responsible AI principle. Customers should be informed about facial analysis being performed, even if individuals aren't being identified.

- ❌ **B is wrong**: Tracking individual customers without consent raises significant privacy concerns and may violate regulations like GDPR or BIPA.

- ❌ **D is wrong**: Using facial analysis data for employment decisions could introduce bias and discrimination, violating responsible AI principles and potentially laws.

- ❌ **E is wrong**: Sharing individual biometric data with third parties without explicit consent violates privacy principles and regulations.

---

**Exam Tip**: Remember the three face features: Detection (find faces + attributes), Comparison (1:1 matching), Search (1:N identification with collections). Face Liveness verifies a live person (not a photo/deepfake). Use Content Moderation for inappropriate content (pre-built) and Custom Moderation adapters to enhance it for your domain. For custom vision tasks, use Custom Labels (simple) or SageMaker (complex). Image operations are synchronous; stored video operations are asynchronous (Start/Get pattern). Collections store face vectors, NOT images. Always consider responsible AI principles: transparency, privacy, bias mitigation, and compliance with regulations when using facial recognition.
