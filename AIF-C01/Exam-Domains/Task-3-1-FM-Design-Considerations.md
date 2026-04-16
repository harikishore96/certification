# Task Statement 3.1: Design Considerations for Applications That Use Foundation Models (FMs)

> **Exam Domain**: Domain 3 — Applications of Foundation Models (28%)
> **Exam Weight**: HIGH — This is the highest-weighted domain on the exam
> **Task Focus**: Selecting models, tuning inference, RAG, vector databases, cost tradeoffs, and agents

---

## 1. Exam Keyword Mapping

| Trigger Words | Points To |
|---|---|
| "cost-effective", "lowest latency", "model selection" | Pre-trained model selection criteria |
| "temperature", "randomness", "creative output", "deterministic" | Inference parameters |
| "up-to-date data", "private data", "knowledge base", "grounding" | RAG / Bedrock Knowledge Bases |
| "vector store", "embeddings", "semantic search" | Vector database services |
| "customize model", "domain-specific", "cost tradeoff" | FM customization approaches |
| "multi-step", "orchestrate", "API calls", "autonomous" | Agents / Agentic AI |
| "prompt caching", "repeated context", "reduce latency" | Prompt caching |
| "cross-region", "throughput", "traffic bursts" | Cross-Region Inference |

---

## 2. Objective 1: Selection Criteria for Pre-Trained Models

When choosing a foundation model, evaluate these key criteria:

### Selection Criteria Matrix

| Criteria | What to Consider | Example Scenario |
|---|---|---|
| **Cost** | Token pricing (input vs. output), hosting paradigm (managed vs. self-hosted) | Budget-constrained app → smaller model (Nova Micro, Haiku) |
| **Modality** | Text, image, video, audio, multimodal | Image generation → Stable Diffusion, Titan Image Generator |
| **Latency** | Time-to-first-token, total response time | Real-time chatbot → smaller/faster model; batch reports → larger model OK |
| **Multi-lingual** | Language support breadth | Global customer support → model with broad language coverage |
| **Model Size** | Parameter count (7B, 70B, 175B+) | Simple classification → small model; complex reasoning → large model |
| **Model Complexity** | Architecture sophistication | Simple Q&A → lightweight model; multi-step reasoning → advanced model |
| **Customization** | Fine-tuning support, continued pre-training | Domain-specific terminology → model that supports fine-tuning on Bedrock |
| **Input/Output Length** | Context window size (tokens) | Summarizing long documents → model with large context window (200K+) |
| **Prompt Caching** | Support for caching repeated contexts | Chatbot with document uploads → model supporting prompt caching |

### Model Selection Decision Tree

```
Need to generate content?
├── Text only?
│   ├── Simple tasks (classification, extraction) → Small model (Nova Micro, Haiku)
│   ├── Complex reasoning/coding → Large model (Claude Sonnet/Opus, Nova Pro)
│   └── Domain-specific → Fine-tuned model
├── Images?
│   ├── Generation → Stable Diffusion, Titan Image Generator, Nova Canvas
│   └── Understanding → Multimodal model (Claude, Nova Lite/Pro)
├── Video?
│   └── Nova Reel
└── Multi-modal (text + image + video)?
    └── Nova Pro, Claude (vision)
```

### Cost Optimization: Start Small, Scale Up

Per the [AWS Well-Architected Generative AI Lens](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/gencost01-bp01.html):
- **Start with the smallest model** that meets minimum performance requirements
- Gradually increase model size/capability until acceptable quality is reached
- Consider **intelligent prompt routing** — route simple queries to smaller models, complex queries to larger models
- Use **batch inference** (50% cost savings) for non-time-sensitive workloads
- Continuously re-evaluate as newer, more cost-effective models become available

### Bedrock Capacity Options

| Capacity Type | Use Case | Cost | Throttling Risk |
|---|---|---|---|
| **On-Demand: Flex** | Dev/test, sporadic workloads | Lowest per token | High (no SLA) |
| **On-Demand: Standard** | Regular production | Balanced | Medium |
| **On-Demand: Priority** | Latency-sensitive, critical apps | Highest on-demand | Low |
| **Reserved Tier** | Consistent high-volume (1 or 6 month commitment) | 30-50% savings | None (guaranteed) |
| **Batch** | Bulk processing, non-urgent | 50% savings vs on-demand | N/A (24-hr window) |
| **Cross-Region Inference** | High availability, traffic bursting | Standard pricing | Auto-routed |

> **📝 Exam Tip**: If a question mentions "consistent, predictable workload" → Reserved Tier. "Sporadic, dev/test" → Flex. "Latency-sensitive, user-facing" → Priority. "Large-scale, non-urgent" → Batch.

Source: [Bedrock Capacity, Limits, and Cost Optimization](https://docs.aws.amazon.com/bedrock/latest/userguide/capacity-limits-cost-optimization.html)

---

## 3. Objective 2: Effect of Inference Parameters on Model Responses

### Key Inference Parameters

| Parameter | Range | Effect | Use Case |
|---|---|---|---|
| **Temperature** | 0.0 – 1.0 | Controls randomness. Low = deterministic/focused. High = creative/diverse | Low (0.0-0.3): factual Q&A, code. High (0.7-1.0): creative writing, brainstorming |
| **Top P (Nucleus Sampling)** | 0.0 – 1.0 | Limits token selection to cumulative probability threshold | 0.1 = very focused. 0.9 = more diverse. Alternative to temperature |
| **Top K** | 1 – 500+ | Limits selection to top K most probable tokens | Small K = focused. Large K = diverse |
| **Max Tokens (Output Length)** | Model-dependent | Maximum number of tokens in the response | Short answers → low value. Long documents → high value |
| **Stop Sequences** | Strings | Stops generation when sequence is encountered | Control output format and length |

### Temperature vs. Top P — When to Use Which

```
Temperature = 0, Top P = 1  → Most deterministic (always picks highest probability token)
Temperature = 1, Top P = 0.1 → Creative but constrained vocabulary
Temperature = 0.7, Top P = 0.9 → Balanced creativity (common default)
```

> **⚠️ Gotcha**: Temperature and Top P both control randomness but work differently. Temperature adjusts the probability distribution; Top P truncates it. Typically adjust one, not both simultaneously.

### Input/Output Length Considerations

- **Input tokens** = your prompt (system prompt + user message + context)
- **Output tokens** = model's response
- **Context window** = maximum input + output tokens combined
- Longer inputs → higher cost (charged per token)
- Longer outputs → higher cost AND higher latency
- **Prompt caching** reduces cost for repeated long inputs

> **📝 Exam Tip**: "Reduce randomness" → lower temperature. "More creative" → higher temperature. "Limit response length" → reduce max tokens. "Reduce cost" → optimize prompt length, use prompt caching.

---

## 4. Objective 3: Retrieval Augmented Generation (RAG)

### What is RAG?

RAG is a technique that **augments FM responses with external data** retrieved at query time, without modifying the model's weights. It combines:
1. **Retrieval** — Search relevant information from a knowledge source
2. **Augmentation** — Add retrieved context to the prompt
3. **Generation** — FM generates a response grounded in the retrieved context

### RAG Data Flow

```
User Query
    ↓
Embedding Model converts query → vector
    ↓
Vector Database performs similarity search
    ↓
Top-K relevant chunks retrieved
    ↓
Chunks + Original Query → Augmented Prompt
    ↓
Foundation Model generates grounded response
    ↓
Response (with source citations)
```

### RAG Pre-Processing Pipeline

```
Data Sources (S3, web, DB, Confluence, SharePoint, etc.)
    ↓
Text extraction & splitting into chunks
    ↓
Embedding model converts chunks → vectors
    ↓
Vectors stored in Vector Database
    (maintains mapping to original documents)
```

### Amazon Bedrock Knowledge Bases

The managed RAG solution on AWS that automates the entire pipeline:

- **Data Sources**: S3, web crawlers, Confluence, SharePoint, Salesforce, custom connectors
- **Chunking Strategies**: Fixed-size, semantic, hierarchical, custom
- **Embedding Models**: Amazon Titan Embeddings, Cohere Embed
- **Vector Stores**: Amazon OpenSearch Serverless, Aurora, Neptune, Pinecone, Redis, MongoDB
- **Query Types**: Retrieve only, Retrieve and Generate

### Business Applications of RAG

| Use Case | Why RAG? |
|---|---|
| Customer support chatbot | Ground answers in company documentation, FAQs, policies |
| Legal document analysis | Search across case law, contracts, regulations |
| Internal knowledge assistant | Query company wikis, Confluence, SharePoint |
| Medical information retrieval | Access up-to-date clinical guidelines |
| Financial research | Query earnings reports, market data, filings |

### Why RAG Over Fine-Tuning?

| Factor | RAG | Fine-Tuning |
|---|---|---|
| Data freshness | ✅ Always current (data updated independently) | ❌ Stale (requires retraining) |
| Cost | ✅ Lower (no training compute) | ❌ Higher (GPU training costs) |
| Setup time | ✅ Fast (hours) | ❌ Slow (days/weeks) |
| Source attribution | ✅ Can cite sources | ❌ No source tracking |
| Model weights | Unchanged | Modified |
| Best for | Factual Q&A, knowledge retrieval | Style/tone adaptation, specialized tasks |

> **📝 Exam Tip**: "Access private/proprietary data without retraining" → RAG. "Up-to-date information" → RAG. "Cite sources" → RAG. "Change model behavior/style" → Fine-tuning.

Source: [How Amazon Bedrock Knowledge Bases Work](https://docs.aws.amazon.com/bedrock/latest/userguide/kb-how-it-works.html)

---

## 5. Objective 4: Vector Database Services for Storing Embeddings

### What Are Vector Embeddings?

Embeddings are numerical representations (arrays of numbers) of text/data that capture semantic meaning. Similar concepts have vectors that are close together in vector space.

| Representation | Example |
|---|---|
| Text | "Amazon Bedrock uses foundation models from leading AI companies" |
| Float32 Vector | `[0.041, 0.056, -0.018, -0.012, -0.020, ...]` |
| Binary Vector | `[1, 1, 0, 0, 0, ...]` |

- **Float32 vectors**: 32 bits/dimension — more precise, higher storage cost
- **Binary vectors**: 1 bit/dimension — less precise, much lower storage cost

### AWS Vector Database Options

| Service | Type | Best For | Key Features |
|---|---|---|---|
| **Amazon OpenSearch Service** | Managed search & analytics | Full-text + vector search combined | k-NN plugin, hybrid search, serverless option |
| **Amazon Aurora** (PostgreSQL) | Relational DB with pgvector | Existing Aurora users, structured + vector data | pgvector extension, familiar SQL interface |
| **Amazon Neptune** | Graph database | Knowledge graphs + vector search | Graph relationships + semantic similarity |
| **Amazon RDS for PostgreSQL** | Relational DB with pgvector | Simple vector storage needs | pgvector extension, cost-effective |

### Additional Supported Vector Stores (via Bedrock Knowledge Bases)

- **Pinecone** — Purpose-built vector database
- **Redis Enterprise Cloud** — In-memory vector search
- **MongoDB Atlas** — Document DB with vector search

### Vector Store Selection Decision Tree

```
Need combined full-text + vector search? → OpenSearch Service
Already using Aurora PostgreSQL? → Aurora with pgvector
Need graph relationships + vectors? → Neptune
Simple, cost-effective vector storage? → RDS for PostgreSQL
Need serverless, fully managed? → OpenSearch Serverless
```

> **📝 Exam Tip**: The exam specifically calls out OpenSearch Service, Aurora, Neptune, and RDS for PostgreSQL as vector database options. Know that they all support storing embeddings for RAG use cases.

---

## 6. Objective 5: Cost Tradeoffs of FM Customization Approaches

### Customization Spectrum (Cost & Effort)

```
Low Cost/Effort ◄──────────────────────────────────► High Cost/Effort

In-Context Learning → RAG → Fine-Tuning → Continued Pre-Training → Pre-Training from Scratch
  (prompt only)     (retrieval)  (labeled data)   (unlabeled domain data)    (massive dataset)
```

### Detailed Comparison

| Approach | Cost | Time | Data Needed | Model Weights | Best For |
|---|---|---|---|---|---|
| **In-Context Learning** (few-shot prompting) | 💰 Lowest | Minutes | Examples in prompt | Unchanged | Quick adaptation, format guidance, simple tasks |
| **RAG** | 💰💰 Low | Hours | Knowledge base documents | Unchanged | Factual grounding, private data access, up-to-date info |
| **Fine-Tuning** | 💰💰💰 Medium | Days | Labeled training data (100s-1000s examples) | Modified (task-specific layers) | Domain-specific behavior, style/tone, specialized tasks |
| **Continued Pre-Training** | 💰💰💰💰 High | Days-Weeks | Large unlabeled domain corpus | Modified (all layers) | Domain adaptation (legal, medical, financial terminology) |
| **Pre-Training from Scratch** | 💰💰💰💰💰 Highest | Weeks-Months | Massive dataset (TB+) | Created from scratch | Entirely new model for unique requirements |

### When to Use Each Approach

| Scenario | Recommended Approach | Why |
|---|---|---|
| "Answer questions about our company docs" | RAG | Access private data without training |
| "Model should respond in our brand voice" | Fine-Tuning | Behavioral/style change requires weight updates |
| "Model doesn't understand medical terminology" | Continued Pre-Training | Domain vocabulary requires broad weight updates |
| "Show the model the output format I want" | In-Context Learning | Few-shot examples in prompt are sufficient |
| "Need up-to-date product catalog info" | RAG | Data changes frequently, no retraining needed |
| "Combine domain knowledge + private data" | Continued Pre-Training + RAG | Pre-train for domain, RAG for specific data |

### Cost Optimization Decision Framework

```
Can prompt engineering solve it?
├── YES → In-Context Learning (cheapest)
└── NO → Does the model need access to external/private data?
    ├── YES → RAG (no training cost, data stays current)
    └── NO → Does the model need to change its behavior/style?
        ├── YES → Fine-Tuning (moderate cost, labeled data)
        └── NO → Does the model lack domain understanding?
            ├── YES → Continued Pre-Training (high cost, unlabeled data)
            └── NO → Pre-Training from Scratch (highest cost, rare)
```

> **📝 Exam Tip**: The exam loves asking "most cost-effective" approach. Always start with the cheapest option (prompt engineering) and only escalate if needed. RAG is almost always preferred over fine-tuning when the goal is accessing external data.

Source: [Comparing RAG and Fine-Tuning](https://docs.aws.amazon.com/prescriptive-guidance/latest/retrieval-augmented-generation-options/rag-vs-fine-tuning.html)

---

## 7. Objective 6: Role of Agents in Multi-Step Tasks

### What Are AI Agents?

Agents are autonomous systems that use FMs to **break down complex tasks into steps**, make decisions, call APIs, and retrieve information — all without human intervention at each step.

### Amazon Bedrock Agents

Bedrock Agents orchestrate interactions between:
- **Foundation Models** — reasoning and decision-making
- **Action Groups** — API calls to external systems (Lambda functions, APIs)
- **Knowledge Bases** — RAG for information retrieval
- **User Conversations** — collecting additional input

### How Agents Work (Orchestration Loop)

```
User Request: "Book me a flight from NYC to LAX next Friday"
    ↓
1. PRE-PROCESSING: Agent understands intent, validates input
    ↓
2. ORCHESTRATION: FM breaks task into steps:
   Step 1: Search available flights (Action Group → Flight API)
   Step 2: Check user preferences (Knowledge Base → User profile)
   Step 3: Compare options and select best match
   Step 4: Book the flight (Action Group → Booking API)
   Step 5: Send confirmation (Action Group → Notification API)
    ↓
3. POST-PROCESSING: Format and return response to user
```

### Agent Components

| Component | Purpose | Example |
|---|---|---|
| **Foundation Model** | Reasoning, planning, decision-making | Claude, Nova Pro |
| **Instructions** | Define agent's role and behavior | "You are a travel booking assistant..." |
| **Action Groups** | Define available actions (APIs) | SearchFlights, BookFlight, CancelBooking |
| **Knowledge Bases** | Provide context and information | Company policies, FAQs, product catalog |
| **Guardrails** | Safety and content filtering | Block PII, enforce topic boundaries |
| **Memory** | Maintain conversation context | Remember user preferences across turns |

### Agentic AI Patterns

| Pattern | Description | AWS Implementation |
|---|---|---|
| **Single Agent** | One agent handles all tasks | Bedrock Agent with multiple action groups |
| **Multi-Agent Collaboration** | Supervisor agent routes to specialized agents | Bedrock Multi-Agent Collaboration |
| **Agent + RAG** | Agent retrieves knowledge before acting | Agent with associated Knowledge Base |
| **Agent + Tools** | Agent calls external tools/APIs | Action Groups with Lambda functions |
| **Agent Chaining** | Output of one agent feeds into another | Step Functions + Bedrock Agents |

### Model Context Protocol (MCP)

MCP is an **open standard** for connecting AI agents to external data sources and tools:
- Provides a **standardized interface** for agents to interact with tools
- Enables agents to access **real-time data** from various sources
- Supports **interoperability** between different AI systems
- Reduces custom integration code

### Agent Use Cases

| Use Case | Actions | Knowledge |
|---|---|---|
| Insurance claims processing | Submit claim, check status, approve/deny | Policy documents, claim history |
| IT helpdesk | Create ticket, reset password, check status | Runbooks, FAQs, system docs |
| E-commerce assistant | Search products, place order, track shipment | Product catalog, shipping policies |
| HR assistant | Submit PTO, check benefits, update info | Employee handbook, benefits guide |

> **📝 Exam Tip**: "Multi-step task", "orchestrate", "call APIs", "autonomous" → Agents. "Simple Q&A from documents" → RAG (no agent needed). "Complex workflow with multiple API calls" → Agent with Action Groups.

Source: [Amazon Bedrock Agents](https://docs.aws.amazon.com/bedrock/latest/userguide/agents.html)

---

## 8. Prompt Caching

### What Is Prompt Caching?

An optional Bedrock feature that **caches repeated prompt contexts** to reduce latency and input token costs.

### How It Works

- You define **cache checkpoints** — markers in your prompt that define what to cache
- Cached content (prompt prefix) must be **static between requests**
- Cache has a **TTL** (Time To Live): 5 minutes (default) or 1 hour (select models)
- TTL resets with each successful cache hit
- Tokens read from cache are charged at a **reduced rate**

### When to Use Prompt Caching

| Scenario | Benefit |
|---|---|
| Chatbot with uploaded documents | Don't reprocess document on every user message |
| System prompts reused across requests | Cache system instructions |
| Agentic workflows with long contexts | Maintain cache across agent steps |
| Repeated analysis of same dataset | Cache the dataset context |

### Supported Models

- **Anthropic Claude** models (Opus, Sonnet, Haiku) — cache system, messages, tools
- **Amazon Nova** models (Micro, Lite, Pro, Premier) — cache system and messages
- Minimum tokens per checkpoint: 1,024 – 4,096 (model-dependent)
- Maximum cache checkpoints per request: 4

> **📝 Exam Tip**: "Reduce latency for repeated contexts" → Prompt Caching. "Chatbot reprocessing same document" → Prompt Caching. This is a cost AND performance optimization.

Source: [Prompt Caching for Faster Model Inference](https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-caching.html)

---

## 9. Cross-Region Inference

### What Is It?

Bedrock automatically routes inference requests to the **optimal AWS Region** to maximize throughput and manage traffic bursts.

### Two Types

| Feature | Geographic | Global |
|---|---|---|
| Data residency | Within geographic boundaries (US, EU, APAC) | Any supported commercial Region |
| Throughput | Higher than single-region | Highest available |
| Cost | Standard pricing | ~10% savings |
| Best for | Compliance requirements | Maximum performance + cost savings |

### Key Facts

- No additional routing cost — priced based on source Region
- All data stays on AWS network (encrypted in transit)
- Logged in CloudTrail in source Region
- Does NOT support Provisioned Throughput
- Does NOT require manual Region enablement

> **📝 Exam Tip**: "Maximize throughput" + "no data residency concerns" → Global Cross-Region Inference. "Data must stay in EU" → Geographic Cross-Region Inference.

Source: [Cross-Region Inference](https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html)

---

## 10. Sample Exam Questions

### Question 1

A company wants to build a customer support chatbot that answers questions using their internal knowledge base of 10,000 product documents. The documents are updated weekly. Which approach is MOST cost-effective?

A) Fine-tune a foundation model on the product documents weekly
B) Use Amazon Bedrock Knowledge Bases with RAG to retrieve relevant documents at query time
C) Pre-train a custom foundation model on the product documents
D) Use in-context learning by including all 10,000 documents in the prompt

**Answer: B**
- ✅ B: RAG is ideal — no retraining needed when documents update, cost-effective, provides source citations
- ❌ A: Fine-tuning weekly is expensive and unnecessary for factual retrieval
- ❌ C: Pre-training from scratch is the most expensive option and overkill
- ❌ D: 10,000 documents would exceed any model's context window

---

### Question 2

A developer is building a real-time translation application that requires the lowest possible latency. The application handles millions of requests daily with consistent traffic. Which Amazon Bedrock capacity option should they choose?

A) On-Demand Flex tier
B) On-Demand Priority tier
C) Reserved Tier with Provisioned Throughput
D) Batch processing

**Answer: C**
- ✅ C: Consistent high-volume + lowest latency = Reserved Tier with guaranteed capacity and no throttling
- ❌ A: Flex has highest throttling risk, no SLA — unsuitable for production
- ❌ B: Priority is good for latency but more expensive than Reserved for consistent workloads
- ❌ D: Batch has 24-hour processing window — not suitable for real-time

---

### Question 3

A company needs their AI assistant to process insurance claims by looking up policy details, calculating coverage, and submitting the claim to their backend system. Which Amazon Bedrock feature should they use?

A) Amazon Bedrock Knowledge Bases only
B) Amazon Bedrock Guardrails
C) Amazon Bedrock Agents with Action Groups and Knowledge Bases
D) Amazon Bedrock Model Evaluation

**Answer: C**
- ✅ C: Multi-step task requiring API calls (submit claim) + knowledge retrieval (policy details) = Agent with Action Groups + Knowledge Bases
- ❌ A: Knowledge Bases alone can retrieve info but cannot call APIs to submit claims
- ❌ B: Guardrails filter content but don't orchestrate tasks
- ❌ D: Model Evaluation assesses model performance, not task orchestration

---

## 11. Key Takeaways for the Exam

1. **Model Selection**: Start small, scale up. Cost, modality, latency, context window, and customization support are the key criteria
2. **Inference Parameters**: Temperature controls randomness (low = deterministic, high = creative). Max tokens controls output length. Both affect cost
3. **RAG**: The go-to approach for grounding FM responses in private/current data without retraining. Implemented via Bedrock Knowledge Bases
4. **Vector Databases**: OpenSearch Service, Aurora, Neptune, RDS for PostgreSQL — all support storing embeddings for RAG
5. **Cost Tradeoffs**: In-context learning < RAG < Fine-tuning < Continued Pre-training < Pre-training (cost order)
6. **Agents**: For multi-step tasks requiring API calls, decision-making, and knowledge retrieval. Use Action Groups for APIs, Knowledge Bases for data
7. **Prompt Caching**: Reduces latency and cost for repeated contexts (5-min or 1-hr TTL)
8. **Cross-Region Inference**: Maximizes throughput by routing across Regions (Geographic for compliance, Global for performance)

---

## 12. Citations

- [AIF-C01 Domain 3: Applications of Foundation Models](https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain3.html)
- [Bedrock Capacity, Limits, and Cost Optimization](https://docs.aws.amazon.com/bedrock/latest/userguide/capacity-limits-cost-optimization.html)
- [Cross-Region Inference](https://docs.aws.amazon.com/bedrock/latest/userguide/cross-region-inference.html)
- [How Amazon Bedrock Knowledge Bases Work](https://docs.aws.amazon.com/bedrock/latest/userguide/kb-how-it-works.html)
- [Amazon Bedrock Agents](https://docs.aws.amazon.com/bedrock/latest/userguide/agents.html)
- [Prompt Caching for Faster Model Inference](https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-caching.html)
- [GENCOST01-BP01: Right-Size Model Selection](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/gencost01-bp01.html)
- [GENCOST02-BP01: Balance Cost and Performance for Inference](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/gencost02-bp01.html)
- [Comparing RAG and Fine-Tuning](https://docs.aws.amazon.com/prescriptive-guidance/latest/retrieval-augmented-generation-options/rag-vs-fine-tuning.html)
