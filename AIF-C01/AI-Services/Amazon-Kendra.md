# Amazon Kendra

## 1. Service/Topic Overview
- **Purpose**: Fully managed, ML-powered intelligent enterprise search service that uses NLP and deep learning for semantic and contextual search across multiple data repositories
- **Service Level**: Regional (GenAI Enterprise Edition available in limited regions: us-east-1, us-west-2, eu-west-1, ap-southeast-2)
- **Key Use Cases**:
  - Enterprise-wide intelligent search across scattered content repositories
  - Retrieval Augmented Generation (RAG) retriever for generative AI applications
  - FAQ matching and knowledge base search
  - Internal employee self-service portals (HR, IT, legal)
  - Customer-facing search applications
- **Exam Weight**: High (Domain 2, Domain 3)
- **Exam Domain**:
  - Domain 2: Fundamentals of Generative AI (24%) — RAG retrieval layer
  - Domain 3: Applications of Foundation Models (28%) — enterprise search, RAG pipelines with Bedrock
  - Domain 5: Security, Compliance, and Governance (14%) — ACL filtering, encryption
- **Task Statements**: Select appropriate retrieval mechanisms for RAG; identify enterprise search solutions; understand how Kendra integrates with generative AI services

## 2. Exam Keyword Mapping
- **Trigger Words**: "intelligent search", "enterprise search", "find information across repositories", "semantic search", "natural language search", "search across documents", "RAG retriever", "knowledge retrieval", "FAQ search", "document search"
- **Scenario Indicators**:
  - "Employees need to search across SharePoint, S3, Confluence, and Salesforce" → Kendra
  - "Build a RAG pipeline with high-accuracy retrieval" → Kendra GenAI Index + Bedrock
  - "Users ask natural language questions and expect precise answers from company docs" → Kendra
  - "Need to respect document-level access permissions in search results" → Kendra (ACL filtering)
  - "Deploy a search experience without writing code" → Kendra Experience Builder
- **Anti-patterns**:
  - Extracting text/data from scanned documents → Amazon Textract (not Kendra)
  - Building a chatbot/conversational interface → Amazon Lex (Kendra can be a backend data source)
  - Product recommendations based on user behavior → Amazon Personalize
  - Full-text search on structured database data → Amazon OpenSearch Service
  - Simple keyword search on a single data source → OpenSearch (Kendra is for multi-source semantic search)

## 3. Core Concepts

### Architectural Patterns
- **Index-based Architecture**: Documents are ingested into a Kendra index → ML models build semantic understanding → queries are matched using NLP and deep learning
- **Connector-based Ingestion**: Pre-built connectors sync data from 40+ sources (S3, SharePoint, Confluence, Salesforce, ServiceNow, Google Drive, OneDrive, RDS, etc.)
- **RAG Retriever Pattern**: User query → Kendra Retrieve API → returns most relevant passages with optimal granularity → passages sent to LLM (Bedrock) → generative response
- **Semantic Search**: Goes beyond keyword matching — understands context, meaning, and intent behind queries
- **Incremental Learning**: Automatically improves search results over time based on user interactions and feedback

### Three Index Types

| Feature | GenAI Enterprise Edition | Basic Enterprise Edition | Basic Developer Edition |
|---------|------------------------|------------------------|------------------------|
| **Accuracy** | Highest (vector search + semantic models) | Semantic search | Semantic search |
| **Production Ready** | ✅ Yes (HA) | ✅ Yes (HA) | ❌ No (PoC only) |
| **Base Documents** | 20,000 or 200MB text | 100,000 or 30GB text | 10,000 or 3GB text |
| **Base QPS** | 0.1 (~8,000 queries/day) | 0.1 (~8,000 queries/day) | 0.05 (~4,000 queries/day) |
| **Scalable** | ✅ Add storage/query units | ✅ Add storage/query units | ❌ Fixed capacity |
| **Data Sources** | 50 per index | 50 per index | 5 per index |
| **Bedrock KB Integration** | ✅ Yes | ❌ No | ❌ No |
| **Q Business Integration** | ✅ Yes | ❌ No | ❌ No |

### Query Types
1. **Factoid Questions**: Who, what, when, where → returns precise single-word/phrase answers (e.g., "Who is the CEO?" → "Andy Jassy")
2. **Descriptive Questions**: How/why → returns relevant passages or documents (e.g., "How do I configure VPN?")
3. **Keyword/Natural Language**: Ambiguous queries → Kendra infers context and returns ranked documents (e.g., "keynote address" — understands "address" means speech, not location)

### Data Flow
```
Data Sources (S3, SharePoint, Confluence, Salesforce, etc.)
    → Connectors (scheduled sync / BatchPutDocument API)
        → Kendra Index (NLP + deep learning models)
            → Query/Retrieve API
                → Ranked Results (answers, passages, documents with confidence scores)
                    → Application / RAG Pipeline (Bedrock)
```

### Integration Points
- **Amazon Bedrock Knowledge Bases**: GenAI Index as managed retriever for RAG
- **Amazon Q Business**: GenAI Index as retriever for fully managed AI assistant
- **Amazon S3**: Primary document storage and data source
- **Amazon Lex**: Kendra as fallback answer source for chatbots
- **Amazon Comprehend**: Pre-process documents to extract entities/metadata for enriched search
- **Amazon Textract**: Extract text from scanned documents before indexing
- **Amazon Transcribe**: Convert audio/video to searchable text (MediaSearch solution)
- **AWS Lambda**: Custom Document Enrichment during ingestion pipeline
- **AWS IAM Identity Center**: User authentication for Experience Builder
- **AWS CloudTrail**: Audit logging for API calls
- **AWS KMS**: Encryption key management (AWS-owned, AWS-managed, or customer-managed)

## 4. Key Features & Components

### APIs

| API | Purpose |
|-----|---------|
| **Query** | Search the index — returns answers, document excerpts, ranked results |
| **Retrieve** | Optimized for RAG — returns relevant passages with optimal granularity for LLM input |
| **BatchPutDocument** | Add documents directly to index (max 5 MB per document) |
| **CreateIndex** | Create a new Kendra index |
| **CreateDataSource** | Connect a data source connector |
| **StartDataSourceSyncJob** | Trigger data source sync |
| **GetQuerySuggestions** | Autocomplete suggestions based on query history |
| **Rescore** | Re-rank search results from another search service (Intelligent Ranking) |

### Connectors (40+ Pre-built)
- **AWS**: Amazon S3, Amazon RDS, Amazon WorkDocs
- **Microsoft**: SharePoint, OneDrive, Teams, Exchange
- **Atlassian**: Confluence, Jira
- **Salesforce**, **ServiceNow**, **Zendesk**
- **Google**: Google Drive, Gmail
- **Slack**, **GitHub**, **Quip**, **Box**, **Dropbox**
- **Databases**: JDBC-compatible databases
- **Web Crawler**: Index web pages
- **Custom Data Source API**: Build your own connector

### Supported File Types
- HTML, PDF, MS Office (Word, PowerPoint, Excel), plain text, CSV, JSON
- Audio/video (via Amazon Transcribe integration — MediaSearch solution)

### Key Features

#### Generative AI / RAG Support
- **Retrieve API**: Purpose-built for RAG — returns optimally chunked passages for LLM consumption
- **Smart Chunking**: Automatically determines best passage granularity for LLM accuracy
- **ACL Filtering in RAG**: Only returns passages the end-user is authorized to see
- **Relevance Boosting**: Boost specific content based on metadata (date, source, author) to improve LLM answers

#### Experience Builder (No-Code)
- Visual drag-and-drop interface to build search applications
- Customize with filters, sorting, suggested answers, FAQs, language selection
- Integrates with AWS IAM Identity Center (SSO) for secure user access
- Supports Azure AD, Okta, and other identity providers

#### Custom Document Enrichment (CDE)
- Pre-process documents during ingestion before indexing
- Apply rules or invoke AWS Lambda functions
- Call other AI services (Comprehend, Textract, Transcribe) during enrichment
- Add/modify metadata, classify documents, extract entities

#### Relevance Tuning
- Boost results based on document metadata fields (freshness, source, author)
- Custom synonyms — upload synonym lists to expand query understanding (e.g., "HSA" ↔ "Health Savings Account")
- Featured Results — pin specific documents to the top for certain queries

#### FAQ Support
- Upload FAQ files (question-answer pairs) in CSV/JSON format
- Specialized model matches user questions to closest FAQ
- Up to 30 FAQs per index, max 5 MB each

#### Query Suggestions / Autocompletion
- Autocomplete queries based on search history and patterns
- Reduces typing by ~25% and guides users toward better questions
- Block lists to prevent inappropriate suggestions

#### Search Analytics Dashboard
- Visualize search quality and usability metrics
- Understand user behavior, search trends, and content gaps
- Available in console or via API for custom dashboards

#### Intelligent Ranking
- Re-rank results from external search services (e.g., self-managed OpenSearch)
- Uses Kendra's semantic models to improve relevance of third-party search results
- Plugin available for OpenSearch 2.4.0+

#### Domain Optimization
- Pre-trained for 14+ industry domains: IT, pharma, insurance, energy, financial services, legal, media, travel, healthcare, HR, news, telecom, mining, automotive

## 5. Exam Focus Areas

### Common Question Types
- **RAG Architecture**: "Which service provides the retrieval layer for a RAG pipeline?" → Kendra (Retrieve API)
- **Service Selection**: "Employees need to search across multiple content repositories using natural language" → Kendra
- **Integration**: "How to build a generative AI assistant on enterprise data?" → Kendra GenAI Index + Bedrock Knowledge Bases or Q Business
- **Access Control**: "Search results must respect document-level permissions" → Kendra ACL filtering

### Gotchas
- Kendra is **not free** — you pay for provisioned index hours even if no queries are run or no documents are indexed
- **Developer Edition is NOT for production** — no HA, fixed capacity, max 5 data sources
- GenAI Enterprise Edition is only available in **4 regions** (us-east-1, us-west-2, eu-west-1, ap-southeast-2)
- Kendra does **NOT** support cross-document aggregation or calculation queries
- **Query and Retrieve API share the same QPS quota** — plan capacity accordingly
- Free tier is only **30 days** (750 hours), not 12 months like many other AWS services
- Connector usage does **NOT** qualify for free tier
- BatchPutDocument has a **5 MB** limit per document (vs 50 MB for connector-ingested documents)
- Max extracted text per document: **5 MB** (regardless of file size)
- Kendra understands **context** — "address" in "keynote address" is interpreted as speech, not location

### Comparison Points

| Feature | Amazon Kendra | Amazon OpenSearch | Amazon Bedrock KB |
|---------|--------------|-------------------|-------------------|
| **Primary Use** | Intelligent enterprise search + RAG retriever | Full-text search, log analytics | RAG with foundation models |
| **Search Type** | Semantic + NLP | Keyword + vector (k-NN) | Vector search |
| **ML Expertise** | None required | Some (for vector search) | None required |
| **Data Sources** | 40+ pre-built connectors | Custom ingestion | S3, Web Crawler, Confluence, SharePoint, Salesforce |
| **ACL Support** | ✅ Native | Manual implementation | Limited |
| **Managed** | Fully managed | Managed (but more config) | Fully managed |
| **RAG Optimized** | ✅ Retrieve API | Manual chunking needed | ✅ Native |
| **Cost Model** | Hourly (provisioned) | Instance-based | Per-query + storage |

### Decision Trees
- ✅ **Use Kendra when**: Enterprise search across multiple repositories with semantic understanding and ACL filtering
- ✅ **Use Kendra GenAI Index when**: Building RAG applications with Bedrock KB or Q Business — need highest retrieval accuracy
- ✅ **Use Kendra Retrieve API when**: Need optimally chunked passages for LLM input in RAG pipelines
- ✅ **Use Kendra + Lex when**: Chatbot needs to answer questions from enterprise knowledge base
- ✅ **Use Kendra Experience Builder when**: Need to deploy search UI quickly without coding
- ❌ **Don't use Kendra when**: Simple keyword search on a single database (use OpenSearch)
- ❌ **Don't use Kendra when**: Log analytics or real-time streaming data search (use OpenSearch)
- ❌ **Don't use Kendra when**: Need to extract text from scanned documents (use Textract first, then index in Kendra)
- ❌ **Don't use Kendra when**: Product recommendations based on user behavior (use Personalize)

## 6. Best Practices

### Security
- **Encryption at rest**: Three options — AWS-owned KMS key, AWS-managed KMS key, or customer-managed KMS key
- **Encryption in transit**: HTTPS/TLS for all API calls
- **ACL filtering**: Kendra respects document-level access permissions — search results filtered per user/group
- **IAM**: Apply least privilege for Kendra API access; audit IAM roles regularly
- **VPC endpoints**: Use AWS PrivateLink for private API access
- **IAM Identity Center**: Secure user authentication for Experience Builder (supports Azure AD, Okta)
- **CloudTrail**: Enable for auditing all Kendra API calls

### Cost Optimization
- **Free Tier**: 750 hours for first 30 days (GenAI Enterprise or Developer Edition) — connector usage excluded
- **GenAI Enterprise Edition** is significantly cheaper than Basic Enterprise ($0.32/hr vs $1.40/hr base)
- Use **Developer Edition** for PoC/testing only — don't provision Enterprise for experiments
- Right-size storage and query capacity units — monitor actual usage
- GenAI connectors are flat-rate ($30/index/month) vs Basic Enterprise per-hour + per-document scanning
- Delete unused indexes — charges accrue even when idle
- Use scheduled connector syncs (not continuous) to minimize sync costs

### Pricing Summary

| Component | GenAI Enterprise | Basic Enterprise | Basic Developer |
|-----------|-----------------|-----------------|-----------------|
| Base Index | $0.32/hr (~$230/mo) | $1.40/hr (~$1,008/mo) | $1.125/hr (~$810/mo) |
| Storage Unit | $0.25/hr | $0.70/hr | N/A |
| Query Unit | $0.07/hr | $0.70/hr | N/A |
| Connectors | $30/index/month (flat) | $0.35/hr sync + $1/1M docs | $0.35/hr sync + $1/1M docs |

### Performance
- Use **relevance tuning** to boost authoritative sources and fresh content
- Upload **custom synonyms** for domain-specific vocabulary
- Use **Featured Results** to pin critical documents for common queries
- Leverage **Custom Document Enrichment** to add metadata during ingestion for better filtering
- Monitor search quality with **Search Analytics Dashboard**
- Use **incremental learning** — Kendra automatically improves from user feedback

### Operations
- Schedule **connector syncs** to keep index up-to-date (full sync + incremental syncs)
- Monitor with **CloudWatch** metrics and **Search Analytics Dashboard**
- Use **CloudTrail** for audit logging
- Review query suggestions and maintain **block lists** to prevent inappropriate autocomplete
- Test with Developer Edition before deploying Enterprise Edition

## 7. Hands-On Labs

### Quick Setup
1. Open Amazon Kendra console → Create Index
2. Choose edition (GenAI Enterprise for best accuracy, Developer for testing)
3. Configure IAM role for Kendra
4. Add a data source (e.g., S3 bucket with documents)
5. Sync the data source
6. Test queries in the console search interface

### Practical Exercises
1. **Basic Search**: Create an index → Add S3 data source with PDF/HTML documents → Run natural language queries → Review answers and confidence scores
2. **RAG Pipeline**: Create GenAI Index → Integrate with Bedrock Knowledge Base → Ask questions and get generative responses grounded in your documents
3. **Experience Builder**: Create a search experience → Customize with filters and sorting → Share with users via IAM Identity Center

### CLI Commands
```bash
# Create a Kendra index
aws kendra create-index \
    --name "my-search-index" \
    --edition "ENTERPRISE_EDITION" \
    --role-arn "arn:aws:iam::123456789012:role/KendraRole"

# Create an S3 data source
aws kendra create-data-source \
    --index-id "index-id" \
    --name "my-s3-datasource" \
    --type "S3" \
    --configuration '{"S3Configuration":{"BucketName":"my-docs-bucket"}}' \
    --role-arn "arn:aws:iam::123456789012:role/KendraS3Role"

# Start a data source sync
aws kendra start-data-source-sync-job \
    --id "datasource-id" \
    --index-id "index-id"

# Query the index
aws kendra query \
    --index-id "index-id" \
    --query-text "What is the company vacation policy?"

# Retrieve passages for RAG
aws kendra retrieve \
    --index-id "index-id" \
    --query-text "How do I reset my password?"

# Add FAQ
aws kendra create-faq \
    --index-id "index-id" \
    --name "company-faqs" \
    --s3-path '{"Bucket":"my-bucket","Key":"faqs.csv"}' \
    --role-arn "arn:aws:iam::123456789012:role/KendraRole"
```

### Python (boto3) Example
```python
import boto3

kendra = boto3.client('kendra')

# Query the index
response = kendra.query(
    IndexId='your-index-id',
    QueryText='What is the maternity leave policy?'
)

for result in response['ResultItems']:
    print(f"Type: {result['Type']}")
    print(f"Score: {result['ScoreAttributes']['ScoreConfidence']}")
    if 'DocumentExcerpt' in result:
        print(f"Excerpt: {result['DocumentExcerpt']['Text']}")
    print("---")

# Retrieve passages for RAG
response = kendra.retrieve(
    IndexId='your-index-id',
    QueryText='How do I configure VPN access?'
)

for item in response['ResultItems']:
    print(f"Passage: {item['Content']}")
    print(f"Document: {item['DocumentTitle']}")
```

## 8. Sample Questions

### Question 1
**A company wants to build a generative AI assistant that answers employee questions using internal documents stored in SharePoint, Confluence, and S3. The solution must respect document-level access permissions so employees only see information they're authorized to access. Which architecture should they use?**

A) Amazon Bedrock with S3 data source and IAM policies
B) Amazon Kendra GenAI Index integrated with Amazon Bedrock Knowledge Bases
C) Amazon OpenSearch Service with vector search and Lambda authorizer
D) Amazon Comprehend for document analysis with custom classification

**Correct Answer: B**

**Explanation**: Amazon Kendra GenAI Index provides the highest accuracy retrieval for RAG, has pre-built connectors for SharePoint, Confluence, and S3, and natively supports ACL-based filtering to respect document-level permissions. Integrating with Bedrock Knowledge Bases enables the generative AI response layer.
- A is wrong: Bedrock with S3 alone doesn't provide connectors for SharePoint/Confluence or native ACL filtering.
- C is wrong: OpenSearch requires manual implementation of vector search, document ingestion, and access control — significantly more effort with no pre-built connectors.
- D is wrong: Comprehend is for NLP text analysis (sentiment, entities), not search or generative AI assistants.

---

### Question 2
**A developer is building a RAG application and needs to send the most relevant document passages to a foundation model in Amazon Bedrock. The passages must be optimally sized for LLM consumption. Which Amazon Kendra API should they use?**

A) Query API
B) BatchPutDocument API
C) Retrieve API
D) GetQuerySuggestions API

**Correct Answer: C**

**Explanation**: The Retrieve API is purpose-built for RAG workflows. It returns the most semantically relevant passages with optimal granularity for LLM input — performing smart chunking automatically. It also supports ACL filtering and relevance boosting.
- A is wrong: The Query API is designed for enterprise search and returns answers, document excerpts, and ranked results — not optimized for RAG passage retrieval.
- B is wrong: BatchPutDocument is for ingesting documents into the index, not querying.
- D is wrong: GetQuerySuggestions provides autocomplete suggestions, not document passages.

---

### Question 3
**A company has deployed an Amazon Kendra index for internal search. They notice that when employees search for "HSA", they don't get results about "Health Savings Account" documents. How should they improve search accuracy?**

A) Retrain the Kendra ML model with custom training data
B) Upload a custom synonym list mapping "HSA" to "Health Savings Account"
C) Use Amazon Comprehend to pre-process all queries before sending to Kendra
D) Switch from Developer Edition to Enterprise Edition

**Correct Answer: B**

**Explanation**: Amazon Kendra supports custom synonym lists that expand query understanding. By uploading a synonym file mapping "HSA" to "Health Savings Account", Kendra will automatically include both terms when searching, returning relevant results for either term.
- A is wrong: Kendra is a managed service — you cannot retrain its ML models directly. You customize through synonyms, relevance tuning, and metadata.
- C is wrong: While Comprehend can extract entities, it doesn't solve the synonym mapping problem. Kendra's built-in synonym feature is the correct approach.
- D is wrong: Changing editions doesn't address vocabulary gaps — synonyms are available in all editions.

---

**Exam Tip**: Remember the key differentiator — Kendra is the **intelligent enterprise search** service with **semantic understanding** and **40+ pre-built connectors**. For RAG, the **Retrieve API** (not Query API) is optimized for LLM consumption. The **GenAI Enterprise Edition** is the recommended index for both RAG and production search. Kendra + Bedrock KB = RAG pipeline. Kendra + Q Business = fully managed AI assistant.
