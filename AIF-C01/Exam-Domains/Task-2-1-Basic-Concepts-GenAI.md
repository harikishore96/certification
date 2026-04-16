# Task Statement 2.1: Explain the Basic Concepts of Generative AI

## 1. Topic Overview
- **Purpose**: Understand the foundational concepts, terminology, and mechanisms behind generative AI
- **Exam Domain**: Domain 2 — Fundamentals of Generative AI (24%)
- **Exam Weight**: HIGH — This is the conceptual backbone of the highest-weighted domain
- **Task Statements**: 
  - Define generative AI and distinguish it from traditional AI/ML
  - Explain foundation models, LLMs, and their architectures
  - Understand tokenization, embeddings, context windows, and inference parameters
  - Identify model types: transformers, diffusion models, GANs, VAEs
  - Recognize hallucinations, limitations, and the generative AI lifecycle

---

## 2. Exam Keyword Mapping

| Trigger Words | Points To |
|---|---|
| "generate new content", "create text/images/code" | Generative AI |
| "pre-trained on vast data", "general-purpose model" | Foundation Model (FM) |
| "attention mechanism", "self-attention" | Transformer architecture |
| "tokens", "subwords", "tokenizer" | Tokenization |
| "vector representation", "semantic meaning" | Embeddings |
| "maximum input length", "token limit" | Context Window |
| "temperature", "top-p", "top-k" | Inference Parameters |
| "factually incorrect output", "made-up information" | Hallucination |
| "noise to image", "denoising" | Diffusion Models |
| "generator vs discriminator" | GANs |
| "latent space", "encoder-decoder" | VAEs |
| "billions of parameters", "weights and biases" | Model Parameters |

**Anti-patterns** (when it's NOT GenAI basics):
- "Train a custom model from scratch" → Traditional ML / SageMaker
- "Classify images into categories" → Supervised learning / Rekognition
- "Detect anomalies in data" → Unsupervised learning
- "Reward-based learning" → Reinforcement learning

---

## 3. Core Concepts

### 3.1 What is Generative AI?

Generative AI refers to AI systems capable of **generating new content** — text, images, code, audio, video — based on input data or prompts. Unlike traditional ML that classifies or predicts, GenAI **creates**.

| Aspect | Traditional ML | Generative AI |
|---|---|---|
| Output | Classification, prediction, score | New content (text, images, code) |
| Training | Task-specific datasets | Massive, diverse datasets |
| Model Size | Thousands–millions of parameters | Billions–trillions of parameters |
| Approach | Train per task | Pre-train once, adapt to many tasks |
| Example | Spam detection | Write an email |

> **Exam Tip**: Generative AI is a *subset* of deep learning, which is a *subset* of ML, which is a *subset* of AI.

### 3.2 Foundation Models (FMs)

Foundation models are **large models pre-trained on vast, diverse datasets** that serve as a base for many downstream tasks.

**Key Characteristics**:
- Pre-trained on massive unlabeled data (internet-scale)
- General-purpose — adaptable to many tasks without retraining
- Can be customized via prompt engineering, fine-tuning, RAG, or continued pre-training
- Contain billions to trillions of **parameters** (learned weights and biases)

**Types of Foundation Models**:

| Type | Input/Output | Examples | AWS Service |
|---|---|---|---|
| Large Language Models (LLMs) | Text → Text | Claude, Titan Text, Llama | Bedrock |
| Image Generation Models | Text → Image | Stable Diffusion, Titan Image | Bedrock |
| Multimodal Models | Text + Image → Text/Image | Amazon Nova, Claude 3 | Bedrock |
| Embedding Models | Text → Vectors | Titan Embeddings, Cohere Embed | Bedrock |
| Code Models | Text → Code | CodeWhisperer | Amazon Q Developer |

**Model Families on Amazon Bedrock**:
- **Anthropic Claude** — Advanced reasoning, long context (200K tokens)
- **Amazon Titan** — AWS-built, cost-effective, text + image + embeddings
- **Amazon Nova** — Multimodal (Micro, Lite, Pro, Premier)
- **Meta Llama** — Open-source, customizable
- **AI21 Labs Jurassic** — Multilingual text
- **Cohere** — Enterprise search and embeddings
- **Stability AI** — Image generation (Stable Diffusion)
- **Mistral** — Efficient, open-weight models

### 3.3 Model Parameters

**Parameters** = the internal weights and biases learned during training.

- **NOT** set by humans (those are *hyperparameters*)
- More parameters generally = more capable model, but also more compute/cost
- Measured in billions (e.g., Llama 2 has 7B, 13B, 70B variants)

| Term | Definition | Set By |
|---|---|---|
| Parameters | Weights/biases learned during training | Model (during training) |
| Hyperparameters | Training configuration (learning rate, batch size) | Human (before training) |
| Inference Parameters | Output control (temperature, top-p) | Human (at inference time) |

### 3.4 Tokenization

**Tokenization** is the process of breaking input text into smaller units called **tokens** — words, subwords, or characters — as a preprocessing step.

**How it works**:
1. Input text → Tokenizer → Sequence of token IDs
2. Token IDs → Model processing → Output token IDs
3. Output token IDs → Detokenizer → Output text

**Key Facts**:
- 1 token ≈ 4 characters or ≈ ¾ of a word (English approximation)
- "Chatbot" might be 1 token; "unbelievable" might be 3 tokens ("un", "believ", "able")
- Tokenization varies by model — each model has its own tokenizer/vocabulary
- Tokens are the **billing unit** for most FM APIs (input tokens + output tokens)

**Common Tokenization Methods**:
| Method | Description |
|---|---|
| Byte-Pair Encoding (BPE) | Merges frequent character pairs iteratively; used by GPT models |
| WordPiece | Similar to BPE but uses likelihood; used by BERT |
| SentencePiece | Language-agnostic; works on raw text without pre-tokenization |

> **Exam Tip**: When a question mentions "cost of using a foundation model," think tokens — both input and output tokens count toward cost.

### 3.5 Embeddings

**Embeddings** transform data (text, images) into **dense numerical vectors** that capture semantic meaning.

**Key Properties**:
- Similar concepts → vectors close together in vector space
- Dissimilar concepts → vectors far apart
- Enable semantic search, similarity comparison, clustering
- Dimensionality: typically 256–1536 dimensions

**How Embeddings Work**:
```
"King" → [0.2, 0.8, 0.1, ...]    (1536-dim vector)
"Queen" → [0.21, 0.79, 0.12, ...]  (close to "King")
"Car" → [0.9, 0.1, 0.7, ...]      (far from "King")
```

**AWS Implementation**:
- **Amazon Titan Embeddings** — Text embeddings via Bedrock
- **Cohere Embed** — Multilingual embeddings via Bedrock
- **Amazon OpenSearch Serverless** — Vector store for embeddings
- **Amazon Bedrock Knowledge Bases** — Manages embedding + indexing for RAG

**Use Cases**:
- Semantic search (find similar documents)
- RAG (retrieve relevant context for FM)
- Recommendation systems
- Clustering and classification

> **Exam Tip**: Embeddings are critical for RAG. The process is: Chunk documents → Embed chunks → Store in vector DB → At query time, embed query → Retrieve similar chunks → Pass to FM.

### 3.6 Context Window

The **context window** is the maximum number of tokens a model can process in a single request (input + output combined).

**Key Facts**:
- Measured in tokens (e.g., 4K, 8K, 32K, 128K, 200K)
- Includes BOTH the input prompt AND the generated output
- Larger context window = can process longer documents, but costs more
- If input exceeds context window → truncation or error

**Context Window Sizes (Examples)**:
| Model | Context Window |
|---|---|
| Claude 3.5 Sonnet | 200K tokens |
| Amazon Titan Text | 8K tokens |
| Amazon Nova Pro | 300K tokens |
| Llama 3 | 8K–128K tokens |

**Why Context Window Matters**:
- Determines how much information the model can "see" at once
- Affects ability to summarize long documents
- Impacts RAG design — how many retrieved chunks can fit
- Larger windows enable multi-turn conversations with full history

> **Exam Tip**: If a question says "the model can't process the entire document," the issue is likely the context window size. Solutions: chunking, summarization, or choosing a model with a larger context window.

### 3.7 Inference Parameters

Inference parameters control **how the model generates output** at inference time. They do NOT change the model itself.

**Randomness & Diversity Parameters**:

| Parameter | What It Controls | Low Value | High Value |
|---|---|---|---|
| **Temperature** | Shape of probability distribution | More deterministic, focused | More random, creative |
| **Top K** | Number of candidate tokens considered | Fewer options, more predictable | More options, more diverse |
| **Top P** (Nucleus Sampling) | Cumulative probability threshold | Fewer options, more focused | More options, more diverse |

**How Temperature Works** (from [AWS Bedrock Docs](https://docs.aws.amazon.com/bedrock/latest/userguide/inference-parameters.html)):
- Lower temperature → steepens the probability distribution → model picks high-probability tokens → deterministic
- Higher temperature → flattens the distribution → model may pick lower-probability tokens → creative/random

**Example**: Prompt: `"I hear the hoof beats of"`
```json
{ "horses": 0.7, "zebras": 0.2, "unicorns": 0.1 }
```
- **Low temperature** → Almost always picks "horses"
- **High temperature** → Might pick "zebras" or "unicorns"
- **Top K = 2** → Only considers "horses" and "zebras"
- **Top P = 0.7** → Only considers "horses" (top 70% of distribution)
- **Top P = 0.9** → Considers "horses" and "zebras" (top 90%)

**Length Parameters**:
| Parameter | Purpose |
|---|---|
| **Max Tokens** | Maximum number of tokens in the response |
| **Stop Sequences** | Character sequences that stop generation |
| **Penalties** | Penalize repeated tokens, frequency, or length |

> **Exam Tip**: Temperature = 0 → most deterministic (factual tasks). Temperature = 1 → most creative (brainstorming). If a question asks "reduce randomness" → lower the temperature.

### 3.8 Hallucinations

**Hallucination** = when a model generates output that is **factually incorrect, fabricated, or inconsistent** with the input.

**Types**:
| Type | Description | Example |
|---|---|---|
| Factual | Generates false facts | "The Eiffel Tower is in London" |
| Fabrication | Invents non-existent things | Cites a paper that doesn't exist |
| Inconsistency | Contradicts itself | Says "yes" then "no" to the same question |
| Irrelevance | Output unrelated to prompt | Asked about Python, responds about snakes |

**Causes**:
- Training data gaps or inaccuracies
- Model overgeneralization
- Ambiguous prompts
- Knowledge cutoff (no awareness of recent events)

**Mitigation Strategies**:
| Strategy | How It Helps |
|---|---|
| **RAG** | Grounds responses in retrieved factual documents |
| **Guardrails** | Filter/block harmful or inaccurate outputs |
| **Prompt Engineering** | Clear instructions, ask model to cite sources |
| **Lower Temperature** | Reduces randomness, sticks to high-probability outputs |
| **Human Review** | Manual verification of critical outputs |
| **Fine-Tuning** | Train on domain-specific accurate data |

**AWS Implementation**:
- **Bedrock Knowledge Bases** (RAG) — Ground responses in your data
- **Bedrock Guardrails** — Content filtering, topic denial, grounding checks
- **Amazon A2I** — Human review workflows

> **Exam Tip**: RAG is the #1 answer for "how to reduce hallucinations" on the exam. It grounds the model's responses in actual retrieved documents.

---

## 4. Generative AI Model Architectures

### 4.1 Transformer Architecture

The **transformer** is the dominant architecture behind modern foundation models. Introduced in the 2017 paper "Attention Is All You Need."

**Key Innovation**: **Self-Attention Mechanism** — allows the model to weigh the importance of different parts of the input when generating each output token.

**How It Works**:
1. Input tokens → Embeddings + Positional Encoding
2. Self-attention layers compute relationships between ALL tokens
3. Feed-forward layers process the attention output
4. Stack multiple layers (e.g., 96 layers in GPT-4)
5. Output layer generates probability distribution over vocabulary

**Transformer Variants**:
| Variant | Architecture | Use Case | Examples |
|---|---|---|---|
| Encoder-Only | Processes input bidirectionally | Classification, NER, sentiment | BERT |
| Decoder-Only | Generates output autoregressively | Text generation | GPT, Claude, Llama |
| Encoder-Decoder | Encodes input, decodes output | Translation, summarization | T5, BART |

**Why Transformers Dominate**:
- Parallelizable (unlike RNNs) → faster training
- Handle long-range dependencies via attention
- Scale well with more data and parameters
- Transfer learning — pre-train once, fine-tune for many tasks

> **Exam Tip**: Most LLMs (Claude, GPT, Llama, Titan) are **decoder-only** transformers. BERT is **encoder-only**. T5 is **encoder-decoder**.

### 4.2 Diffusion Models

Diffusion models generate content (typically images) by **learning to reverse a noise-adding process**.

**How It Works**:
1. **Forward Process**: Gradually add noise to an image until it becomes pure noise
2. **Reverse Process (Learned)**: Model learns to remove noise step-by-step, reconstructing the image
3. **Generation**: Start from random noise → iteratively denoise → produce new image

**Key Characteristics**:
- Excellent image quality
- Slower generation than GANs (multiple denoising steps)
- Highly controllable with text prompts (text-to-image)
- Used in: Stable Diffusion, DALL-E, Amazon Titan Image Generator

**AWS Implementation**: Amazon Bedrock → Stability AI (Stable Diffusion), Amazon Titan Image Generator

### 4.3 Generative Adversarial Networks (GANs)

GANs use **two competing neural networks** to generate realistic content.

**Components**:
- **Generator**: Creates fake data (tries to fool the discriminator)
- **Discriminator**: Distinguishes real data from fake (tries to catch the generator)
- They train adversarially — each improves the other

**Key Characteristics**:
- Fast generation (single forward pass)
- Can produce very realistic images
- Training can be unstable (mode collapse)
- Less controllable than diffusion models

**Use Cases**: Image generation, style transfer, data augmentation, super-resolution

### 4.4 Variational Autoencoders (VAEs)

VAEs learn a **compressed latent representation** of data and generate new samples from that space.

**Components**:
- **Encoder**: Compresses input into a latent space distribution
- **Latent Space**: Compressed representation (mean + variance)
- **Decoder**: Reconstructs data from latent space samples

**Key Characteristics**:
- Smooth latent space enables interpolation between samples
- Faster training than GANs
- Output quality typically lower than GANs/diffusion
- Good for anomaly detection and data generation

### 4.5 Architecture Comparison

| Feature | Transformers | Diffusion Models | GANs | VAEs |
|---|---|---|---|---|
| Primary Use | Text, code, multimodal | Images, video | Images | Images, anomaly detection |
| Quality | Excellent (text) | Excellent (images) | High (images) | Moderate |
| Speed | Fast (autoregressive) | Slow (iterative) | Fast | Fast |
| Training | Stable | Stable | Unstable | Stable |
| Controllability | High (prompts) | High (text guidance) | Low | Moderate |
| AWS Service | Bedrock (LLMs) | Bedrock (Stable Diffusion, Titan Image) | SageMaker | SageMaker |

> **Exam Tip**: If a question mentions "text-to-image" → think Diffusion Models. "Text generation" → Transformers. "Two competing networks" → GANs. "Latent space / encoder-decoder for generation" → VAEs.

---

## 5. Generative AI Lifecycle

Per the [AWS Well-Architected Generative AI Lens](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/generative-ai-lifecycle.html), the GenAI lifecycle has **6 phases**:

| Phase | Focus | Key Activities |
|---|---|---|
| **1. Scoping** | Define the problem | Identify use case, assess feasibility, define success metrics, evaluate costs |
| **2. Model Selection** | Choose the right FM | Evaluate modality, size, accuracy, pricing, context window, latency |
| **3. Model Customization** | Adapt the model | Prompt engineering, RAG, fine-tuning, continued pre-training, distillation |
| **4. Development & Integration** | Build the application | APIs, UIs, agents, knowledge bases, guardrails, orchestration |
| **5. Deployment** | Go to production | CI/CD pipelines, IaC, monitoring, security validation |
| **6. Continuous Improvement** | Iterate and refine | Monitor metrics, collect feedback, retrain, mitigate biases |

**Model Selection Criteria**:
- **Modality**: Text, image, multimodal, code
- **Context Window**: How much input can it handle?
- **Latency**: Real-time vs. batch requirements
- **Cost**: Per-token pricing, provisioned throughput
- **Accuracy**: Benchmark performance for your task
- **Customizability**: Can it be fine-tuned?
- **Data Privacy**: Where is data processed? Retention policies?

---

## 6. Key Definitions (AWS Official)

From the [AWS Well-Architected Generative AI Lens — Definitions](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/definitions.html):

| Term | Definition |
|---|---|
| **Generative AI** | AI systems capable of generating new content (text, images, code) based on input data or prompts |
| **Foundation Models** | Large models pre-trained on vast amounts of data, serving as a foundation for downstream tasks |
| **Tokenization** | Breaking input text into smaller units (tokens) as preprocessing for NLP tasks |
| **Embedding** | Transforms chunks of data into vectors that represent semantic meaning |
| **Chunking** | Breaking large data files into small chunks to fit into a context window |
| **Hallucination** | Model produces outputs that are inconsistent, factually incorrect, or unrelated to the input |
| **Fine-Tuning** | Adapting a pre-trained model to a specific task using a smaller, task-specific dataset |
| **Continuous Pre-Training** | Continuously updating a pre-trained model with new data to adapt to evolving domains |
| **RAG** | Augmenting a model's output with relevant information retrieved from a document corpus |
| **Prompt Engineering** | Carefully crafting prompts to guide models to produce desired outputs |
| **Agent** | An AI system that performs tasks autonomously and interacts with its environment |
| **Model Distillation** | Creating a smaller, efficient model that mimics a larger model's behavior |
| **Quantization** | Reducing precision of model parameters to decrease memory and compute requirements |
| **Zero-Shot Learning** | Model performs a task without any task-specific training examples |
| **Vector Store** | Specialized data store for efficient storage/retrieval of high-dimensional vector embeddings |
| **Indexing** | Process of inserting embedded chunks into a vector data store |
| **Knowledge Graph** | Structured representation of entities and relationships for enhanced AI reasoning |
| **Model Card** | Document providing key info about a model: intended use, training data, performance, limitations |
| **Provisioned Throughput** | Amazon Bedrock feature for reserved, higher throughput at fixed cost |

---

## 7. Exam Focus Areas

### Common Question Types
1. **Definition questions**: "What is a foundation model?" / "What is tokenization?"
2. **Comparison questions**: "How does GenAI differ from traditional ML?"
3. **Parameter questions**: "What does lowering temperature do?"
4. **Architecture questions**: "Which model type uses a denoising process?"
5. **Limitation questions**: "What causes hallucinations and how to mitigate?"
6. **Lifecycle questions**: "What is the first step in a GenAI project?"

### Gotchas & Common Misconceptions
- ❌ "Parameters" and "hyperparameters" are the same → They are NOT. Parameters are learned; hyperparameters are set by humans.
- ❌ "Inference parameters change the model" → They do NOT. They only affect the current generation.
- ❌ "Larger context window is always better" → Larger windows cost more and may introduce noise.
- ❌ "Temperature = 0 means no output" → It means most deterministic (highest-probability token always chosen).
- ❌ "Foundation models don't hallucinate with RAG" → RAG *reduces* hallucinations but doesn't eliminate them entirely.
- ❌ "All foundation models are LLMs" → LLMs are a *type* of FM. Image models and embedding models are also FMs.
- ❌ "Tokens = words" → Tokens can be subwords, characters, or whole words depending on the tokenizer.

### Decision Tree: Choosing the Right Concept

```
Question about generating NEW content?
├── YES → Generative AI
│   ├── Text/code generation? → Transformer (Decoder-only LLM)
│   ├── Image from text? → Diffusion Model
│   ├── Two competing networks? → GAN
│   └── Compressed representation? → VAE
└── NO → Traditional ML (classification, regression, clustering)

Question about controlling output?
├── "More deterministic/factual" → Lower temperature
├── "More creative/diverse" → Higher temperature
├── "Limit candidate tokens" → Top K
├── "Probability threshold" → Top P
└── "Shorter response" → Max tokens / Stop sequences

Question about reducing hallucinations?
├── "Ground in external data" → RAG
├── "Filter harmful content" → Guardrails
├── "Better instructions" → Prompt Engineering
└── "Human verification" → A2I / Human Review
```

---

## 8. Sample Questions

### Question 1
**A company wants to use generative AI to create marketing copy. The marketing team wants the output to be creative and varied each time. Which inference parameter should they adjust?**

A) Decrease the temperature to 0  
B) Increase the temperature closer to 1  
C) Set max tokens to a very low value  
D) Use a smaller context window  

**Correct Answer: B**  
**Explanation**: Higher temperature flattens the probability distribution, making the model more likely to select lower-probability (more creative/unexpected) tokens. Option A would make output deterministic and repetitive. Options C and D control length and input capacity, not creativity.

---

### Question 2
**A data scientist notices that a foundation model sometimes generates factually incorrect information about company products. Which approach would MOST effectively reduce this issue?**

A) Increase the model's temperature parameter  
B) Use a model with more parameters  
C) Implement Retrieval-Augmented Generation (RAG) with a product knowledge base  
D) Switch from a transformer model to a diffusion model  

**Correct Answer: C**  
**Explanation**: RAG grounds the model's responses in actual retrieved documents from a knowledge base, directly addressing hallucinations about company-specific information. Option A would increase randomness (worse). Option B doesn't guarantee accuracy on specific domain knowledge. Option D is for image generation, not text accuracy.

---

### Question 3
**Which of the following BEST describes the relationship between tokens and the context window of a foundation model?**

A) Tokens are the billing unit; the context window determines the model's accuracy  
B) The context window defines the maximum number of tokens (input + output) the model can process in a single request  
C) Tokens are only used for input; the context window only applies to output  
D) The context window is the number of training examples the model was trained on  

**Correct Answer: B**  
**Explanation**: The context window is measured in tokens and represents the maximum combined length of input prompt + generated output. Option A is partially true (tokens are billing units) but the context window is about capacity, not accuracy. Option C is wrong — tokens apply to both input and output. Option D confuses context window with training data.

---

## 9. Cross-References

| Related Topic | File |
|---|---|
| Amazon Bedrock | [Amazon-Bedrock.md](../Generative-AI-Services/Amazon-Bedrock.md) |
| Amazon Nova | [Amazon-Nova.md](../Generative-AI-Services/Amazon-Nova.md) |
| Tokenization Deep Dive | [Tokenization.md](../Fundamentals/Tokenization.md) |
| Embeddings Deep Dive | [Embeddings.md](../Fundamentals/Embeddings.md) |
| Transformer Architecture | [Transformer-Based-Models.md](../Fundamentals/Transformer-Based-Models.md) |
| Diffusion Models | [Diffusion-Models.md](../Fundamentals/Diffusion-Models.md) |
| Foundation Models | [Foundation-Models.md](../Fundamentals/Foundation-Models.md) |
| LLMs | [Large-Language-Models.md](../Fundamentals/Large-Language-Models.md) |
| Inference Parameters | [Inference-Parameters-Sampling.md](../Fundamentals/Inference-Parameters-Sampling.md) |
| Model Customization | [Model-Customization.md](../Fundamentals/Model-Customization.md) |
| Model Distillation | [Model-Distillation.md](../Fundamentals/Model-Distillation.md) |
| Prompt Engineering | [Prompt-Engineering-Techniques.md](../Fundamentals/Prompt-Engineering-Techniques.md) |
| Domain 2 Overview | [Domain-2-Fundamentals-Generative-AI.md](Domain-2-Fundamentals-Generative-AI.md) |

---

**Citations**:
- [AWS Well-Architected Generative AI Lens — Definitions](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/definitions.html)
- [AWS Well-Architected Generative AI Lens — Lifecycle](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/generative-ai-lifecycle.html)
- [Amazon Bedrock — Inference Parameters](https://docs.aws.amazon.com/bedrock/latest/userguide/inference-parameters.html)

---

**Exam Tip**: Task 2.1 is about *understanding* GenAI — what it is, how it works, and the key terminology. Focus on being able to define every term in Section 6 and knowing when to apply each concept from the decision tree in Section 7.
