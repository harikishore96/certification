# Embeddings

## 1. Topic Overview
- **Purpose**: Embeddings are dense numerical vector representations that capture the semantic meaning of data (text, images, audio, video), enabling machines to understand similarity and relationships between content
- **Scope**: Foundational concept underpinning RAG, semantic search, recommendations, clustering, and classification across AWS AI/ML services
- **Key Use Cases**:
  - Semantic search (find content by meaning, not keywords)
  - Retrieval Augmented Generation (RAG) — retrieve relevant context for foundation models
  - Recommendation systems (find similar products, content, users)
  - Clustering and classification of documents
  - Anomaly detection (identify outliers in vector space)
- **Exam Weight**: High
- **Exam Domain**:
  - Domain 2: Fundamentals of Generative AI (24%) — understanding embeddings as a core concept
  - Domain 3: Applications of Foundation Models (28%) — RAG, Knowledge Bases, semantic search
- **Task Statements**:
  - Task 2.1: Explain the basic concepts of generative AI
  - Task 3.1: Describe design considerations for applications that use foundation models
  - Task 3.2: Choose effective prompt engineering techniques

---

## 2. Exam Keyword Mapping

### Trigger Words
- "Embeddings", "vector representations", "semantic meaning"
- "Similarity search", "semantic search", "nearest neighbor"
- "Vector database", "vector store", "vector index"
- "Cosine similarity", "Euclidean distance", "dot product"
- "RAG", "retrieval", "knowledge base", "grounding"
- "Titan Embeddings", "Nova Embeddings", "Cohere Embed"
- "Dimensionality", "high-dimensional", "embedding space"

### Scenario Indicators
- "Find documents with similar meaning" → Embeddings + vector search
- "Search by concept, not keywords" → Semantic search with embeddings
- "Ground model responses in company data" → RAG with embeddings + Knowledge Bases
- "Recommend similar products/content" → Embeddings + similarity search
- "Reduce storage cost for embeddings" → Lower dimension embeddings (256/512 vs 1024)
- "Search across text AND images" → Multimodal embeddings (Titan Multimodal / Nova Multimodal)

### Anti-patterns
- Embeddings are NOT the same as encoding (one-hot, label encoding) — embeddings capture semantic relationships
- Embeddings do NOT generate text — they produce vectors; a separate FM generates text
- Keyword/lexical search does NOT use embeddings — it uses exact term matching
- Embeddings are NOT fine-tuning — fine-tuning changes model weights; embeddings are model outputs

---

## 3. Core Concepts

### What Are Embeddings?

Embeddings convert unstructured data (text, images, audio, video) into fixed-length numerical vectors in a high-dimensional space where **semantically similar items are closer together**.

```
"The cat sat on the mat"  →  [0.12, -0.45, 0.78, ..., 0.33]  (1024 dimensions)
"A kitten rested on a rug" →  [0.11, -0.44, 0.77, ..., 0.34]  (very similar vector!)
"Stock market crashed"     →  [0.89, 0.23, -0.56, ..., -0.71]  (very different vector)
```

### Key Properties
- **Dense**: Every dimension has a meaningful value (unlike sparse one-hot vectors)
- **Fixed-length**: Same number of dimensions regardless of input length
- **Semantic**: Similar meanings → similar vectors; different meanings → distant vectors
- **Learned**: Created by neural networks trained on large datasets
- **Not human-readable**: The individual dimensions don't have interpretable meaning

### How Embeddings Work in RAG

```
                    INDEXING PHASE (Offline)
┌──────────────┐    ┌──────────────┐    ┌──────────────────┐
│  Documents   │───→│  Embedding   │───→│  Vector Database  │
│  (chunks)    │    │  Model       │    │  (store vectors)  │
└──────────────┘    └──────────────┘    └──────────────────┘

                    QUERY PHASE (Online)
┌──────────────┐    ┌──────────────┐    ┌──────────────────┐    ┌─────────┐
│  User Query  │───→│  Embedding   │───→│  Vector Database  │───→│  Top-K  │
│              │    │  Model       │    │  Similarity Search │    │ Results │
└──────────────┘    └──────────────┘    └──────────────────┘    └────┬────┘
                                                                     │
                    ┌──────────────┐    ┌──────────────────┐         │
                    │  Generated   │←───│  Foundation      │←────────┘
                    │  Response    │    │  Model (FM)      │  (query + retrieved context)
                    └──────────────┘    └──────────────────┘
```

**Critical**: The **same embedding model** must be used for both indexing and querying. Mixing different embedding models produces incompatible vector spaces.

### Similarity/Distance Metrics

| Metric | Formula Concept | Range | Best For | AWS Support |
|--------|----------------|-------|----------|-------------|
| **Cosine Similarity** | Angle between vectors | -1 to 1 (1 = identical) | Text similarity, normalized vectors | OpenSearch, S3 Vectors, Knowledge Bases |
| **Euclidean Distance (L2)** | Straight-line distance | 0 to ∞ (0 = identical) | When magnitude matters | OpenSearch, Neptune |
| **Dot Product** | Sum of element-wise products | -∞ to ∞ (higher = more similar) | Normalized vectors, fast computation | OpenSearch |

> **Exam Tip**: Cosine similarity is the most commonly used metric for text embeddings because it measures directional similarity regardless of vector magnitude.

### Vector Search Algorithms

| Algorithm | Type | Speed | Accuracy | Use Case |
|-----------|------|-------|----------|----------|
| **k-NN (k-Nearest Neighbors)** | Exact | Slow (brute force) | 100% accurate | Small datasets |
| **ANN (Approximate Nearest Neighbors)** | Approximate | Fast | ~95-99% accurate | Large-scale production |
| **HNSW (Hierarchical Navigable Small World)** | ANN variant | Very fast | High accuracy | Most common in production |
| **IVF (Inverted File Index)** | ANN variant | Fast | Good accuracy | Large datasets with clustering |

### Embedding Dimensions

| Dimensions | Storage | Latency | Accuracy | When to Use |
|------------|---------|---------|----------|-------------|
| **256** | Lowest | Fastest | Good | Cost-sensitive, simple similarity |
| **384** | Low | Fast | Better | Balanced cost/accuracy |
| **512** | Medium | Medium | Good | General purpose |
| **1024** | Higher | Slower | Best | Maximum accuracy, complex retrieval |
| **1536** | Highest | Slowest | Best | Legacy (Titan V1) |

> **Exam Tip**: Smaller dimensions = less storage + lower latency but slightly reduced accuracy. Titan V2 defaults to 1024 but supports 256 and 512 for cost optimization.

---

## 4. Key Features & Components

### 4.1 Amazon Titan Text Embeddings V2

| Feature | Details |
|---------|---------|
| **Model ID** | `amazon.titan-embed-text-v2:0` |
| **Input** | Text only |
| **Max Tokens** | 8,192 tokens (~50,000 characters) |
| **Output Dimensions** | 1,024 (default), 512, 256 |
| **Languages** | Optimized for English; 100+ languages supported |
| **Normalization** | Supported (unit-length vectors for cosine similarity) |
| **Fine-tuning** | Not supported |
| **Inference Types** | On-Demand, Provisioned Throughput, Batch |
| **Use Cases** | RAG, document search, reranking, classification, clustering |

**Key Details**:
- Characters-to-token ratio: ~4.7 characters per token (English)
- For retrieval tasks, **segment documents into logical chunks** (paragraphs/sections) — don't embed entire documents
- Cross-language queries (e.g., Korean KB queried in German) return sub-optimal results
- Does NOT support inference parameters like `maxTokenCount` or `topP`

### 4.2 Amazon Titan Multimodal Embeddings G1

| Feature | Details |
|---------|---------|
| **Model ID** | `amazon.titan-embed-image-v1` |
| **Input** | Text AND/OR images |
| **Max Text Tokens** | 128 tokens |
| **Max Image Size** | 25 MB |
| **Output Dimensions** | 1,024 (default), 384, 256 |
| **Fine-tuning** | Supported |
| **Use Cases** | Multimodal search, image+text retrieval, recommendations |

**Key Details**:
- Accepts `inputText`, `inputImage` (base64-encoded), or **both**
- When both text and image are provided, output is the **average** of text and image embedding vectors
- Enables cross-modal search: search images with text queries and vice versa
- Much shorter text context (128 tokens) compared to Titan Text V2 (8,192 tokens)

### 4.3 Amazon Nova Multimodal Embeddings

| Feature | Details |
|---------|---------|
| **Modalities** | Text, images, document images, video, audio |
| **Max Context** | 8K tokens (text), 30s (video), 30s (audio) |
| **Dimension Sizes** | 4 options (trading accuracy vs. storage cost) |
| **API** | Synchronous (InvokeModel) and Asynchronous (StartAsyncInvoke) |
| **Use Cases** | Agentic RAG, semantic search, cross-modal retrieval |

**Key Differentiators from Titan**:
- **Unified semantic space** across ALL modalities (text, image, video, audio) — single model replaces multiple specialized models
- **Video and audio support** — Titan does not support these modalities
- **Async API** for large files — results saved to S3
- **Batch inference** via JSONL files for large-scale processing
- **Segmented embeddings** for video content

### 4.4 Third-Party Embedding Models on Bedrock

| Model | Provider | Dimensions | Modality |
|-------|----------|-----------|----------|
| **Cohere Embed English** | Cohere | 1,024 | Text |
| **Cohere Embed Multilingual** | Cohere | 1,024 | Text |

### 4.5 AWS Vector Database Options

| Service | Type | Key Advantage | Best For |
|---------|------|--------------|----------|
| **Amazon OpenSearch Service** | Managed search engine | k-NN/ANN with HNSW, IVF; hybrid search | Production semantic search at scale |
| **Amazon Aurora PostgreSQL (pgvector)** | Relational DB + vector extension | Use existing PostgreSQL; exact + ANN search | Teams already using Aurora/PostgreSQL |
| **Amazon Neptune Analytics** | Graph DB + vector | Vector search within graph traversals | Knowledge graphs + vector search |
| **Amazon MemoryDB** | In-memory DB | Ultra-low latency; HNSW + Flat indexing | Real-time applications, session memory |
| **Amazon DocumentDB** | Document DB | MongoDB-compatible; HNSW + IVFFlat | Teams using MongoDB patterns |
| **Amazon S3 Vectors** | Object storage + vector index | Cosine/Euclidean; up to 4,096 dimensions | Simple vector storage integrated with S3 |
| **Amazon Kendra** | Managed intelligent search | No vector DB management needed; built-in connectors | Enterprise document search |
| **Pinecone** (3rd party) | Managed vector DB | Fully managed; filtering, live updates | Dedicated vector workloads |

> **Exam Tip**: For Bedrock Knowledge Bases, the supported vector stores are OpenSearch Serverless, Aurora PostgreSQL, Pinecone, MongoDB Atlas, and Redis Enterprise Cloud. Know that Knowledge Bases handle chunking and embedding automatically.

### 4.6 Bedrock Knowledge Bases (Managed RAG)

Bedrock Knowledge Bases **automate the entire embedding pipeline**:
1. **Data ingestion**: Connect to S3 data sources
2. **Chunking**: Automatically split documents (fixed-size, semantic, or custom)
3. **Embedding**: Generate vectors using selected embedding model
4. **Storage**: Store in configured vector database
5. **Retrieval**: Query with automatic embedding + similarity search
6. **Reranking**: Optional reranking model to improve result relevance

Supported embedding models for Knowledge Bases:
- Amazon Titan Text Embeddings V2
- Amazon Titan Embeddings G1 - Text
- Cohere Embed models

---

## 5. Exam Focus Areas

### Common Question Types

**1. Scenario-Based Selection**:
- "Company wants to search product catalog by meaning, not keywords" → Embeddings + vector database + semantic search
- "Company needs to ground chatbot responses in internal documents" → RAG with Bedrock Knowledge Bases (embeddings + vector store + FM)
- "Company wants to search across both text and images" → Multimodal embeddings (Titan Multimodal or Nova Multimodal)
- "Company wants to reduce vector storage costs" → Use smaller embedding dimensions (256 instead of 1024)

**2. Feature Comparison**:
- Titan Text Embeddings V2 vs. Titan Multimodal Embeddings vs. Nova Multimodal Embeddings
- Keyword search vs. semantic search vs. hybrid search
- Different vector database options

**3. Architecture Questions**:
- "Which component converts documents to vectors?" → Embedding model
- "Where are embedding vectors stored?" → Vector database
- "What ensures query and document vectors are compatible?" → Same embedding model for both

### Gotchas
- **Same embedding model for indexing AND querying** — mixing models produces incompatible vectors
- **Embeddings ≠ text generation** — embedding models output vectors, NOT text
- **Titan Multimodal has only 128 token text limit** — much less than Titan Text V2's 8,192 tokens
- **Titan Text Embeddings V2 does NOT support fine-tuning** — Titan Multimodal G1 does
- **More dimensions ≠ always better** — higher dimensions increase storage/latency; 256-512 may suffice
- **Embeddings are NOT reversible** — you cannot reconstruct the original text from an embedding vector
- **Cross-language queries** with Titan V2 return sub-optimal results (optimized for same-language)
- **Nova Multimodal Embeddings** supports video and audio — Titan models do NOT

### Comparison Points

| Feature | Titan Text V2 | Titan Multimodal G1 | Nova Multimodal |
|---------|--------------|---------------------|-----------------|
| **Modalities** | Text only | Text + Image | Text + Image + Video + Audio |
| **Max Text Input** | 8,192 tokens | 128 tokens | 8K tokens |
| **Default Dimensions** | 1,024 | 1,024 | Configurable (4 sizes) |
| **Dimension Options** | 1024, 512, 256 | 1024, 384, 256 | 4 options |
| **Fine-tuning** | ❌ | ✅ | — |
| **Async API** | ❌ | ❌ | ✅ |
| **Video/Audio** | ❌ | ❌ | ✅ |
| **Languages** | 100+ | — | — |
| **Best For** | Text RAG, search | Image+text search | Universal multimodal search |

### Decision Tree

```
What type of content do you need to embed?

├── Text only?
│   └── Amazon Titan Text Embeddings V2
│       ├── Need cost optimization? → Use 256 or 512 dimensions
│       └── Need max accuracy? → Use 1024 dimensions (default)
│
├── Text + Images?
│   ├── Short text captions? → Titan Multimodal Embeddings G1 (128 token limit)
│   └── Long text + images? → Nova Multimodal Embeddings (8K tokens)
│
├── Video or Audio?
│   └── Amazon Nova Multimodal Embeddings (only option)
│
└── Where to store vectors?
    ├── Managed RAG (no infra)? → Bedrock Knowledge Bases
    ├── Full-text + vector search? → Amazon OpenSearch Service
    ├── Already using PostgreSQL? → Aurora PostgreSQL + pgvector
    ├── Ultra-low latency? → Amazon MemoryDB
    ├── Graph + vector? → Amazon Neptune Analytics
    └── Simple storage? → Amazon S3 Vectors
```

---

## 6. Best Practices

### Security
- Encrypt vector databases at rest (KMS) and in transit (TLS)
- Use VPC endpoints for private access to Bedrock embedding APIs
- Apply least-privilege IAM policies for embedding model invocation
- Ensure source documents in S3 are encrypted and access-controlled

### Cost Optimization
- Use **smaller embedding dimensions** (256/512) when full accuracy isn't required — reduces storage and compute
- Use **batch inference** for large-scale indexing instead of real-time API calls
- Choose **On-Demand** for variable workloads; **Provisioned Throughput** for steady high-volume
- **Chunk documents appropriately** — too many small chunks = more vectors = more storage cost
- Use **OpenSearch Serverless** for variable query patterns to avoid over-provisioning

### Performance
- **Chunk size matters**: Too small = loss of context; too large = diluted relevance. Typical: 200-500 tokens
- **Overlap chunks** by 10-20% to preserve context at boundaries
- Use **HNSW algorithm** for fast approximate search at scale
- **Normalize vectors** when using cosine similarity (Titan V2 supports this natively)
- **Segment long documents** into logical sections before embedding
- Use **reranking** after initial retrieval to improve result quality

### Operations
- Monitor embedding API latency and error rates via CloudWatch
- Track vector database index size and query performance
- Re-index when switching embedding models (vectors are incompatible across models)
- Version your embedding model choice — document which model generated which vectors
- Set up alerts for vector database capacity thresholds

---

## 7. Hands-On Labs

### Quick Setup: Generate Text Embeddings with Titan V2

```python
import json
import boto3

bedrock_runtime = boto3.client("bedrock-runtime", region_name="us-east-1")

# Generate text embedding
response = bedrock_runtime.invoke_model(
    modelId="amazon.titan-embed-text-v2:0",
    contentType="application/json",
    accept="application/json",
    body=json.dumps({
        "inputText": "What is retrieval augmented generation?",
        "dimensions": 512,       # 256, 512, or 1024
        "normalize": True        # Unit-length vectors for cosine similarity
    })
)

result = json.loads(response["body"].read())
embedding = result["embedding"]          # List of 512 floats
token_count = result["inputTextTokenCount"]

print(f"Dimensions: {len(embedding)}")
print(f"Token count: {token_count}")
print(f"First 5 values: {embedding[:5]}")
```

### Quick Setup: Generate Multimodal Embeddings with Titan

```python
import json
import base64
import boto3

bedrock_runtime = boto3.client("bedrock-runtime", region_name="us-east-1")

# Encode image
with open("product.jpg", "rb") as f:
    image_base64 = base64.b64encode(f.read()).decode("utf-8")

# Generate combined text + image embedding
response = bedrock_runtime.invoke_model(
    modelId="amazon.titan-embed-image-v1",
    contentType="application/json",
    accept="application/json",
    body=json.dumps({
        "inputText": "Red running shoes",
        "inputImage": image_base64,
        "embeddingConfig": {
            "outputEmbeddingLength": 256  # 256, 384, or 1024
        }
    })
)

result = json.loads(response["body"].read())
embedding = result["embedding"]  # Average of text + image vectors
print(f"Multimodal embedding dimensions: {len(embedding)}")
```

### CLI: Invoke Titan Text Embeddings

```bash
aws bedrock-runtime invoke-model \
    --model-id amazon.titan-embed-text-v2:0 \
    --content-type application/json \
    --accept application/json \
    --body '{"inputText": "Semantic search example", "dimensions": 256, "normalize": true}' \
    output.json

# View token count from response
python -c "import json; d=json.load(open('output.json')); print(f'Tokens: {d[\"inputTextTokenCount\"]}, Dims: {len(d[\"embedding\"])}')"
```

---

## 8. Sample Questions

### Question 1
**A company is building a customer support chatbot using Amazon Bedrock. They want the chatbot to answer questions based on their internal product documentation stored in Amazon S3. The solution should require minimal infrastructure management. Which approach should they use?**

A) Fine-tune a foundation model on the product documentation  
B) Use Amazon Bedrock Knowledge Bases with Amazon Titan Text Embeddings V2 and a vector store  
C) Use Amazon Comprehend to extract entities from the documentation  
D) Store the documentation in Amazon DynamoDB and query it with the foundation model  

**Correct Answer: B**

**Explanation**: Bedrock Knowledge Bases provides a fully managed RAG pipeline — it automatically chunks documents, generates embeddings with Titan Text Embeddings V2, stores vectors in a managed vector store, and retrieves relevant context at query time. This requires minimal infrastructure management.
- A is wrong: Fine-tuning changes model behavior but doesn't give it access to specific documents; it's also expensive and doesn't update when docs change.
- C is wrong: Comprehend extracts entities/sentiment but doesn't enable document-grounded Q&A.
- D is wrong: DynamoDB is a key-value/document database, not optimized for semantic similarity search.

---

### Question 2
**A media company wants to build a search system where users can find relevant video clips by typing text descriptions. Which Amazon embedding model should they use?**

A) Amazon Titan Text Embeddings V2  
B) Amazon Titan Multimodal Embeddings G1  
C) Amazon Nova Multimodal Embeddings  
D) Amazon Cohere Embed English  

**Correct Answer: C**

**Explanation**: Amazon Nova Multimodal Embeddings is the only embedding model that supports video (along with text, images, and audio) in a unified semantic space. This enables cross-modal retrieval — searching video content using text queries.
- A is wrong: Titan Text V2 only supports text input, not video.
- B is wrong: Titan Multimodal G1 supports text and images but NOT video.
- D is wrong: Cohere Embed is text-only.

---

### Question 3
**A company indexed 10 million product descriptions using Amazon Titan Text Embeddings V2 with 1,024 dimensions. They want to reduce vector storage costs by 75% while maintaining reasonable search accuracy. What should they do?**

A) Switch to Amazon Titan Multimodal Embeddings G1  
B) Re-index the documents using 256 dimensions with Titan Text Embeddings V2  
C) Compress the existing 1,024-dimension vectors using gzip  
D) Use keyword search instead of semantic search  

**Correct Answer: B**

**Explanation**: Titan Text Embeddings V2 supports configurable output dimensions (1024, 512, 256). Switching from 1,024 to 256 dimensions reduces vector storage by 75% (256/1024 = 0.25) while maintaining reasonable search accuracy. The documents must be re-indexed because the new vectors have different dimensions.
- A is wrong: Switching to Multimodal doesn't reduce dimensions and has a 128-token text limit — unsuitable for product descriptions.
- C is wrong: Compressing vectors with gzip doesn't help because vectors must be decompressed for similarity search; it doesn't reduce the effective storage in the vector database.
- D is wrong: Keyword search eliminates semantic understanding entirely — it doesn't reduce embedding storage costs.

---

**Exam Tip**: Embeddings are the bridge between unstructured data and machine-understandable representations. For the AIF-C01 exam, focus on: (1) WHY embeddings matter (semantic understanding), (2) WHICH embedding model to choose (text-only vs. multimodal), (3) WHERE to store vectors (vector database selection), and (4) HOW they power RAG (the end-to-end pipeline from documents → embeddings → vector store → retrieval → FM → response).
