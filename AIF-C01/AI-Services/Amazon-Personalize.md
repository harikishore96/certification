# Amazon Personalize

## 1. Service/Topic Overview
- **Purpose**: Fully managed ML service that generates personalized item recommendations and user segments based on user interaction data — no ML expertise required
- **Service Level**: Regional
- **Key Use Cases**:
  - Personalized product recommendations (ecommerce)
  - Personalized video/content recommendations (streaming)
  - Next best action recommendations (loyalty programs, app downloads)
  - Personalized search result re-ranking
  - Targeted marketing campaigns via user segmentation
  - Personalized email content
- **Exam Weight**: Medium-High
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%)
  - Domain 3: Applications of Foundation Models (28%)
- **Task Statements**:
  - Task 1.2: Identify practical use cases for AI
  - Task 3.1: Describe the capabilities of AI services
  - Task 3.2: Select appropriate AI services for specific use cases

## 2. Exam Keyword Mapping
- **Trigger Words**:
  - "Recommendation engine" or "Personalized recommendations"
  - "Recommend products/items/content"
  - "Users who bought X also bought Y"
  - "Top picks for you"
  - "Similar items" or "Related items"
  - "User segmentation"
  - "Next best action"
  - "Re-rank search results"
  - "Trending" or "Popular items"
- **Scenario Indicators**:
  - "Recommend products based on purchase history" → USER_PERSONALIZATION recipe
  - "Show similar items on product page" → RELATED_ITEMS recipe (Similar-Items / SIMS)
  - "Re-order search results for each user" → PERSONALIZED_RANKING recipe
  - "Show trending content" → Trending-Now recipe
  - "Recommend next action (sign up, download)" → Next-Best-Action recipe
  - "Segment users for marketing campaign" → USER_SEGMENTATION recipe
  - "Streaming video app recommendations" → VIDEO_ON_DEMAND domain
  - "Ecommerce product recommendations" → ECOMMERCE domain
- **Anti-patterns**:
  - ❌ Don't use for text analysis (use Amazon Comprehend)
  - ❌ Don't use for time-series forecasting (use Amazon Forecast)
  - ❌ Don't use for fraud detection (use Amazon Fraud Detector)
  - ❌ Don't use for image/video analysis (use Amazon Rekognition)
  - ❌ Don't use for search (use Amazon Kendra) — but CAN re-rank search results

## 3. Core Concepts

### Architecture & Workflow
```
1. PREPARE DATA
   ├── Item Interactions dataset (REQUIRED — clicks, purchases, views)
   ├── Items dataset (optional — price, genre, description)
   ├── Users dataset (optional — age, gender, membership)
   ├── Actions dataset (Next-Best-Action only)
   └── Action Interactions dataset (Next-Best-Action only)
       ↓
2. CREATE DATASET GROUP
   ├── Domain dataset group (VIDEO_ON_DEMAND or ECOMMERCE)
   │   └── Create Recommenders (preconfigured use cases)
   └── Custom dataset group
       └── Create Solution → Solution Version (trained model)
           ↓
3. DEPLOY & GET RECOMMENDATIONS
   ├── Real-time: Campaign (provisioned capacity) → GetRecommendations API
   ├── Batch: Batch Inference Job → S3 output
   └── User Segments: Batch Segment Job → S3 output
       ↓
4. RECORD EVENTS & RETRAIN
   ├── Event Tracker → PutEvents API (real-time interactions)
   └── Retrain models periodically (auto or manual)
```

### Key Terminology

| Term | Definition |
|------|-----------|
| **Dataset Group** | Container for all Personalize resources (datasets, recommenders, solutions) |
| **Domain Dataset Group** | Preconfigured for VIDEO_ON_DEMAND or ECOMMERCE with managed lifecycle |
| **Custom Dataset Group** | Fully configurable; you choose recipes and manage training/deployment |
| **Recipe** | Preconfigured ML algorithm for a specific use case |
| **Solution** | Recipe + customized parameters + trained models |
| **Solution Version** | A trained model created from a solution |
| **Campaign** | Deployed solution version with provisioned capacity for real-time recommendations |
| **Recommender** | Domain dataset group resource that generates recommendations for a use case |
| **Event Tracker** | Records real-time user interactions to influence recommendations |
| **Impressions** | Items shown to a user when they interacted with a particular item |

### Five Dataset Types

| Dataset | Required? | Contains | Example Fields |
|---------|-----------|----------|----------------|
| **Item Interactions** | ✅ Required | User-item events (clicks, purchases, views) | userId, itemId, timestamp, eventType, eventValue |
| **Items** | Optional | Item metadata | itemId, genre, price, description, creationTimestamp |
| **Users** | Optional | User metadata | userId, age, gender, membershipStatus |
| **Actions** | Next-Best-Action only | Action metadata | actionId, value, expirationTimestamp, repeatFrequency |
| **Action Interactions** | Next-Best-Action only | User-action events | userId, actionId, timestamp, eventType |

### Minimum Data Requirements
- **Minimum**: 1,000 item interactions from at least 25 unique users (2+ interactions each)
- **Recommended**: 50,000+ item interactions from 1,000+ users (2+ interactions each)
- **Schema**: JSON in Apache Avro format
- **Import**: CSV files from S3 (bulk) or real-time via PutEvents API

### Domain vs. Custom Dataset Groups

| Aspect | Domain Dataset Group | Custom Dataset Group |
|--------|---------------------|---------------------|
| Domains | VIDEO_ON_DEMAND, ECOMMERCE | Any use case |
| Setup | Preconfigured use cases | Choose recipe manually |
| Resource | Recommenders | Solutions + Solution Versions |
| Training | Managed (auto-retrain every 7 days) | Manual or automatic |
| Deployment | Automatic | Create Campaign manually |
| Flexibility | Can add custom resources too | Full control |
| Next-Best-Action | ❌ Not supported | ✅ Supported |

## 4. Key Features & Components

### Recipe Types & Recipes

#### USER_PERSONALIZATION — "Recommended for you"
| Recipe | Architecture | Max Items | Key Feature |
|--------|-------------|-----------|-------------|
| **User-Personalization-v2** | Transformer-based | 5M | Latest; real-time personalization, exploration, auto-updates, metadata in recommendations |
| **User-Personalization** | HRNN-based | — | Legacy; exploration + impressions data |
| **Popularity-Count** | — | — | Recommends items with most unique user interactions |
| **Trending-Now** | — | — | Recommends currently trending items |

#### RELATED_ITEMS — "More like this"
| Recipe | How It Works |
|--------|-------------|
| **Similar-Items** | Co-occurrence in interactions + item metadata |
| **SIMS** | Similarity based purely on user interaction patterns (not metadata) |
| **Semantic-Similarity** | Uses unstructured text metadata (descriptions) for similarity |

#### PERSONALIZED_RANKING — "Re-rank for you"
| Recipe | Architecture | Use Case |
|--------|-------------|----------|
| **Personalized-Ranking-v2** | Transformer-based, up to 5M items | Re-rank search results or curated lists per user |
| **Personalized-Ranking** | Legacy | Same use case, older architecture |

#### PERSONALIZED_ACTIONS — "Next best action"
| Recipe | Use Case |
|--------|----------|
| **Next-Best-Action** | Recommend actions users will most likely take (loyalty signup, app download) |

- Action optimization period: 7-28 days (default 14)
- ❌ Cannot be created in Domain dataset groups

#### USER_SEGMENTATION — "Target these users"
| Recipe | Segments By |
|--------|------------|
| **Item-Affinity** | Users likely to interact with specific items |
| **Item-Attribute-Affinity** | Users likely to interact with items having specific attributes |

- Batch only (CreateBatchSegmentJob)
- Use for targeted marketing campaigns

### Domain Use Cases

#### VIDEO_ON_DEMAND
- Top picks for you
- More like X
- Most popular
- Because you watched X

#### ECOMMERCE
- Recommended for you
- Frequently bought together
- Customers who viewed X also viewed
- Best sellers / Most popular

### Key Features

#### Real-time Personalization
- Recommendations update based on user's evolving interests
- Record interactions via PutEvents API → recommendations adapt immediately
- Supported by User-Personalization-v2, User-Personalization, Next-Best-Action

#### Exploration
- Includes items with fewer interactions or less relevance to improve discovery
- Prevents "filter bubble" — users see new/diverse items
- Configurable: exploration weight + exploration item age cutoff
- User-Personalization-v2 handles exploration automatically

#### Automatic Updates
- Models auto-update every 2 hours to include new items (via exploration)
- Recommenders auto-retrain every 7 days
- Manual UPDATE training mode available for more frequent updates
- FULL training mode creates completely new solution version

#### Filters
- Apply custom criteria to recommendation results
- Filter by item metadata (e.g., only show items in stock, exclude already purchased)
- Dynamic filters with runtime parameters

#### Promotions
- Include specific items in recommendations (e.g., sponsored products)
- Promoted items marked with reason in response

#### Batch Workflows
- **Batch Inference Job**: Get recommendations for all users → S3 output
- **Batch Segment Job**: Get user segments → S3 output
- No campaign needed for batch workflows

#### Event Tracking
- **PutEvents API**: Record item interactions in real-time
- **PutActionInteractions API**: Record action interactions
- Supports anonymous users via session ID
- Third-party integrations: Amplitude, Segment

#### Metric Attribution
- Measure impact of recommendations on business metrics
- Track recommendation effectiveness over time

### Integration Points
- **S3**: Bulk data import/export, batch job input/output
- **SageMaker Data Wrangler**: Import from 40+ sources and prepare data
- **AWS Amplify**: Record events from web/mobile apps
- **Lambda**: Serverless recommendation workflows
- **Step Functions**: Orchestrate Personalize workflows (MLOps)
- **EventBridge**: Notifications for job status changes
- **OpenSearch**: Re-rank search results with Personalized-Ranking
- **CloudWatch**: Monitor recommendation requests, latency
- **KMS**: Encrypt dataset group data
- **IAM**: Access control
- **Third-party**: Amplitude, Braze (email), Optimizely (A/B testing), Segment (event data)

## 5. Exam Focus Areas

### Common Question Types

#### Service Selection
```
Recommendation Need?
│
├─ Personalized item recommendations → Amazon Personalize
├─ Time-series forecasting → Amazon Forecast
├─ Fraud detection → Amazon Fraud Detector
├─ Intelligent search → Amazon Kendra
├─ Text generation/chat → Amazon Bedrock
└─ Custom ML model → Amazon SageMaker
```

#### Recipe Selection Decision Tree
```
What do you want to recommend?
│
├─ Items for a specific user → USER_PERSONALIZATION
│   ├─ Personalized picks → User-Personalization-v2
│   ├─ Trending items → Trending-Now
│   └─ Most popular → Popularity-Count
│
├─ Similar/related items → RELATED_ITEMS
│   ├─ Based on interactions → SIMS
│   ├─ Based on interactions + metadata → Similar-Items
│   └─ Based on text descriptions → Semantic-Similarity
│
├─ Re-rank a list for a user → PERSONALIZED_RANKING
│   └─ Personalized-Ranking-v2
│
├─ Next best action → PERSONALIZED_ACTIONS
│   └─ Next-Best-Action
│
└─ User segments for marketing → USER_SEGMENTATION
    ├─ By specific items → Item-Affinity
    └─ By item attributes → Item-Attribute-Affinity
```

### Gotchas
1. **Interactions data is REQUIRED**: Items and Users datasets are optional but improve quality
2. **Minimum 1,000 interactions**: Won't train without sufficient data
3. **Not a search engine**: Personalize re-ranks results but doesn't perform search (use Kendra/OpenSearch for search)
4. **Next-Best-Action only in Custom dataset groups**: Cannot use in Domain dataset groups
5. **User-Personalization-v2 vs v1**: v2 uses transformer architecture, handles up to 5M items, lower latency — prefer v2
6. **Campaigns cost money while active**: Delete campaigns when not in use
7. **Recommender billing**: Billed on greater of minimum requests/hour OR actual requests
8. **Auto-updates ≠ full retrain**: Auto-updates every 2 hours only add new items via exploration; full retrain needed for learning from new interaction patterns
9. **Exploration is important**: Without it, new items never get recommended (cold start problem)
10. **Schema is Avro JSON**: Not regular JSON — must follow Apache Avro format
11. **Impressions data**: Helps Personalize understand what users ignored, improving relevance
12. **Anonymous users**: Can track via session ID, then link to user ID later
13. **Up to 15 recommenders per region**: Limit for Domain dataset groups

### Comparison Points

#### Amazon Personalize vs. Amazon SageMaker
```
Amazon Personalize
├─ Purpose: Managed recommendation engine
├─ ML Expertise: None required
├─ Data: Interaction data + optional metadata
├─ Output: Item recommendations, user segments
├─ Training: Managed (recipes, auto-retrain)
└─ Use: Product/content recommendations

Amazon SageMaker
├─ Purpose: Build any custom ML model
├─ ML Expertise: Required
├─ Data: Any structured/unstructured data
├─ Output: Any prediction type
├─ Training: Full control (algorithms, hyperparameters)
└─ Use: Custom ML solutions beyond recommendations
```

#### Amazon Personalize vs. Amazon Kendra
```
Amazon Personalize
├─ Purpose: Recommend items based on user behavior
├─ Input: User interaction history
├─ Output: Ranked list of recommended items
└─ Use: "You might also like..."

Amazon Kendra
├─ Purpose: Intelligent document search
├─ Input: Search query
├─ Output: Relevant documents/answers
└─ Use: "Find documents about..."

Combined: Kendra search → Personalize re-ranks results per user
```

#### Domain vs. Custom Dataset Groups
```
Domain Dataset Group
├─ Best for: VIDEO_ON_DEMAND or ECOMMERCE
├─ Setup: Preconfigured use cases
├─ Management: AWS manages training lifecycle
├─ Flexibility: Can still add custom resources
└─ Limitation: No Next-Best-Action

Custom Dataset Group
├─ Best for: Any other use case
├─ Setup: Choose recipe, configure manually
├─ Management: You manage training/deployment
├─ Flexibility: Full control over everything
└─ Advantage: All recipes available including Next-Best-Action
```

#### SIMS vs. Similar-Items vs. Semantic-Similarity
```
SIMS
├─ Similarity based on: User interaction patterns only
├─ Metadata: Not used
└─ Best for: "Users who interacted with X also interacted with Y"

Similar-Items
├─ Similarity based on: Interactions + item metadata
├─ Metadata: Used (genre, category, etc.)
└─ Best for: Combined behavioral + attribute similarity

Semantic-Similarity
├─ Similarity based on: Unstructured text metadata
├─ Metadata: Text descriptions, synopses
└─ Best for: Content-based similarity from descriptions
```

## 6. Best Practices

### Security
- **IAM**: Least privilege for Personalize API access
- **KMS**: Encrypt dataset group data with customer managed keys
- **S3 Encryption**: Encrypt training data at rest in S3
- **Data Deletion**: Use CreateDataDeletionJob to remove user data (GDPR compliance)
- **VPC**: No VPC endpoint needed — Personalize is a managed service

### Cost Optimization
- **Pricing Components**: Data processing + training hours + recommendation requests
- **Free Tier**: 20 GB data processing, 100 hours training, 180,000 requests/month (first 2 months)
- **Campaigns**: Delete when not in use — you pay while active
- **Minimum TPS**: Start with 1 TPS and let auto-scaling handle spikes
- **Batch for bulk**: Use batch inference jobs instead of real-time for non-interactive use cases
- **Automatic training**: Turn off if not needed to avoid unnecessary training costs
- **User-Personalization-v2**: Different pricing model — check pricing page

### Performance
- **Data quality**: More interaction data = better recommendations
- **Optional datasets**: Adding Items and Users metadata improves recommendation quality
- **Contextual metadata**: Include device, location for context-aware recommendations
- **Impressions data**: Record what users saw but didn't click for better relevance
- **Retrain regularly**: Models degrade over time without fresh training data
- **Event tracking**: Record real-time events for immediate recommendation updates

### Operations
- **CloudWatch**: Monitor recommendation request count, latency, errors
- **EventBridge**: Set up notifications for training completion, job status
- **Step Functions**: Automate retrain → deploy → update campaign workflows
- **Metric Attribution**: Measure business impact of recommendations
- **A/B Testing**: Use Optimizely integration to test recommendation strategies

## 7. Hands-On Labs

### Quick Setup: Create Dataset Group and Import Data
```python
import boto3

personalize = boto3.client('personalize', region_name='us-east-1')

# Create dataset group
response = personalize.create_dataset_group(
    name='my-ecommerce-recommendations',
    domain='ECOMMERCE'
)
dataset_group_arn = response['datasetGroupArn']

# Create schema for interactions
schema = {
    "type": "record",
    "name": "Interactions",
    "namespace": "com.amazonaws.personalize.schema",
    "fields": [
        {"name": "USER_ID", "type": "string"},
        {"name": "ITEM_ID", "type": "string"},
        {"name": "TIMESTAMP", "type": "long"},
        {"name": "EVENT_TYPE", "type": "string"}
    ],
    "version": "1.0"
}

schema_response = personalize.create_schema(
    name='my-interactions-schema',
    schema=json.dumps(schema)
)
```

### Practical Exercise 1: Create Recommender (Domain)
```python
# Create recommender for "Recommended for you" use case
response = personalize.create_recommender(
    name='recommended-for-you',
    datasetGroupArn=dataset_group_arn,
    recipeArn='arn:aws:personalize:::recipe/aws-ecomm-recommended-for-you'
)
recommender_arn = response['recommenderArn']
```

### Practical Exercise 2: Get Real-time Recommendations
```python
personalize_runtime = boto3.client('personalize-runtime', region_name='us-east-1')

# Get recommendations for a user
response = personalize_runtime.get_recommendations(
    recommenderArn=recommender_arn,
    userId='user123',
    numResults=10
)

for item in response['itemList']:
    print(f"Item: {item['itemId']}, Score: {item.get('score', 'N/A')}")
```

### Practical Exercise 3: Record Real-time Event
```python
personalize_events = boto3.client('personalize-events', region_name='us-east-1')

# Record a click event
personalize_events.put_events(
    trackingId='your-tracking-id',
    userId='user123',
    sessionId='session-abc',
    eventList=[{
        'sentAt': datetime.now().timestamp(),
        'eventType': 'click',
        'itemId': 'item456'
    }]
)
```

### CLI Commands
```bash
# Create dataset group
aws personalize create-dataset-group \
  --name my-recommendations \
  --domain ECOMMERCE

# Create recommender
aws personalize create-recommender \
  --name top-picks \
  --dataset-group-arn arn:aws:personalize:us-east-1:123456789012:dataset-group/my-recommendations \
  --recipe-arn arn:aws:personalize:::recipe/aws-ecomm-recommended-for-you

# List recipes
aws personalize list-recipes

# Get recommendations
aws personalize-runtime get-recommendations \
  --recommender-arn arn:aws:personalize:us-east-1:123456789012:recommender/top-picks \
  --user-id user123 \
  --num-results 10

# Start batch inference job
aws personalize create-batch-inference-job \
  --job-name my-batch-job \
  --solution-version-arn arn:aws:personalize:... \
  --job-input s3DataSource={path=s3://input-bucket/} \
  --job-output s3DataDestination={path=s3://output-bucket/} \
  --role-arn arn:aws:iam::123456789012:role/PersonalizeRole
```

## 8. Sample Questions

### Question 1: Service & Recipe Selection
**Scenario**: An ecommerce company wants to show "Customers who viewed this product also viewed" recommendations on each product detail page. They have 2 years of clickstream data including product views, add-to-cart, and purchase events. Which Amazon Personalize recipe should they use?

A) User-Personalization-v2
B) Similar-Items
C) Personalized-Ranking-v2
D) Trending-Now

**Correct Answer**: B) Similar-Items

**Explanation**:
- ✅ **B is correct**: Similar-Items is a RELATED_ITEMS recipe that recommends items similar to a given item based on user interaction patterns and item metadata. "Customers who viewed X also viewed Y" is the classic related items use case. It requires an itemId as input and returns similar items.
- ❌ **A is wrong**: User-Personalization-v2 generates personalized recommendations for a specific user ("Recommended for you"), not items related to a specific product.
- ❌ **C is wrong**: Personalized-Ranking-v2 re-ranks a provided list of items for a user. It doesn't discover related items — it reorders an existing list.
- ❌ **D is wrong**: Trending-Now recommends currently popular items across all users, not items related to a specific product.

### Question 2: Architecture Decision
**Scenario**: A streaming video company wants to add personalized recommendations to their app. They need "Top picks for you" and "Because you watched X" features. They want the fastest setup with minimal ML expertise. Which approach should they use?

A) Custom dataset group with User-Personalization-v2 and Similar-Items recipes
B) Domain dataset group with VIDEO_ON_DEMAND domain and preconfigured recommenders
C) Amazon SageMaker to build a custom recommendation model
D) Amazon Bedrock to generate personalized content suggestions

**Correct Answer**: B) Domain dataset group with VIDEO_ON_DEMAND domain and preconfigured recommenders

**Explanation**:
- ✅ **B is correct**: VIDEO_ON_DEMAND Domain dataset group provides preconfigured use cases including "Top picks for you" and "Because you watched X." AWS manages the training lifecycle, auto-retrains every 7 days, and requires no ML expertise. This is the fastest setup for streaming video apps.
- ❌ **A is wrong**: Custom dataset group requires manually choosing recipes, creating solutions, training solution versions, and deploying campaigns. More setup effort than Domain dataset group for a standard streaming video use case.
- ❌ **C is wrong**: SageMaker requires significant ML expertise to build, train, and deploy a custom recommendation model. Much more complex than Personalize.
- ❌ **D is wrong**: Bedrock is for generative AI (text generation, chat), not for building recommendation engines based on user interaction data.

### Question 3: Real-time Personalization
**Scenario**: A news app uses Amazon Personalize to recommend articles. They notice that when a user reads several sports articles in a row, the recommendations don't immediately reflect this interest shift. What should they implement to fix this?

A) Retrain the model more frequently
B) Use the PutEvents API to record real-time reading events with an Event Tracker
C) Switch from User-Personalization-v2 to Trending-Now recipe
D) Increase the exploration weight parameter

**Correct Answer**: B) Use the PutEvents API to record real-time reading events with an Event Tracker

**Explanation**:
- ✅ **B is correct**: The PutEvents API records real-time user interactions. When using User-Personalization-v2 (which supports real-time personalization), recording events immediately influences the next set of recommendations for that user. The Event Tracker enables this real-time feedback loop.
- ❌ **A is wrong**: Retraining creates a new solution version from all historical data — it doesn't provide immediate, session-level personalization. Retraining takes time and doesn't solve the real-time responsiveness issue.
- ❌ **C is wrong**: Trending-Now shows globally trending items, not personalized recommendations based on individual user behavior.
- ❌ **D is wrong**: Exploration introduces less-relevant items for discovery purposes. It would actually make recommendations LESS aligned with the user's current interest, not more.

---

**Exam Tips**:
- Personalize = recommendation engine using interaction data; no ML expertise required
- 6 recipe types: USER_PERSONALIZATION, RELATED_ITEMS, PERSONALIZED_RANKING, POPULAR_ITEMS, PERSONALIZED_ACTIONS, USER_SEGMENTATION
- Domain dataset groups (VIDEO_ON_DEMAND, ECOMMERCE) = fastest setup, managed lifecycle; Custom = full control
- Interactions dataset is REQUIRED (minimum 1,000 interactions, 25 users); Items/Users datasets are optional but improve quality
- Real-time personalization: PutEvents API + Event Tracker → immediate recommendation updates
- Exploration prevents cold start problem for new items
- Auto-updates every 2 hours (new items); auto-retrain every 7 days (recommenders)
- Next-Best-Action = Custom dataset groups ONLY (not Domain)
- USER_SEGMENTATION = batch only (for marketing campaigns)
- Personalize re-ranks search results but is NOT a search engine (use Kendra/OpenSearch for search)
- Campaigns cost money while active — delete when done
- Schema format = Apache Avro JSON (not regular JSON)
- User-Personalization-v2 = transformer-based, up to 5M items, preferred over v1
