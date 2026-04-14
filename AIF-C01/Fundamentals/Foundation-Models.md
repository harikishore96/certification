# Foundation Models (FMs)

## 1. Topic Overview
- **Purpose**: Foundation models are large-scale, pre-trained AI models trained on broad datasets that can be adapted to a wide range of downstream tasks without task-specific training from scratch
- **Exam Weight**: HIGH
- **Exam Domains**:
  - Domain 2: Fundamentals of Generative AI (24%) — Task 2.1, 2.2
  - Domain 3: Applications of Foundation Models (28%) — Task 3.1, 3.3, 3.4
- **Task Statements**:
  - 2.1: Explain basic concepts of GenAI (FMs, transformer-based LLMs, multimodal models, diffusion models)
  - 2.2: Understand capabilities and limitations of GenAI
  - 3.1: Describe design considerations for FM applications (selection criteria, inference parameters, RAG, cost tradeoffs)
  - 3.3: Describe training and fine-tuning process for FMs
  - 3.4: Describe methods to evaluate FM performance

---

## 2. Exam Keyword Mapping

### Trigger Words
- "Foundation model", "pre-trained model", "large language model (LLM)"
- "Transformer", "self-attention", "tokens", "embeddings"
- "Multimodal", "diffusion model", "generative AI"
- "Model selection", "model customization", "model lifecycle"

### Scenario Indicators
- "Need a general-purpose model adaptable to multiple tasks" → Foundation Model
- "Want to generate text/images/code without training from scratch" → FM via Bedrock or JumpStart
- "Need to customize a pre-trained model for a specific domain" → Fine-tuning / Continued Pre-training
- "Need to select the right model for cost, latency, and accuracy" → FM selection criteria

### Anti-patterns
- "Need to train a model entirely from scratch on proprietary data" → Custom ML model (SageMaker)
- "Need a rule-based system with deterministic outputs" → Not an FM use case
- "Need real-time structured data predictions (tabular)" → Traditional ML, not generative FM

---

## 3. Core Concepts

### What is a Foundation Model?
- A **large AI model** pre-trained on massive, diverse datasets (text, images, code, etc.)
- Learns general patterns and representations that transfer to many tasks
- Serves as a **starting point** — adapted via prompt engineering, fine-tuning, or RAG
- Typically built on the **Transformer architecture** (self-attention mechanism)

### Key Characteristics
| Characteristic | Description |
|---|---|
| **Scale** | Billions of parameters (weights learned during training) |
| **Pre-training** | Trained on vast unlabeled data (self-supervised learning) |
| **Adaptability** | Single model → many tasks (text generation, summarization, Q&A, code, images) |
| **Emergent Abilities** | Capabilities that appear at scale (reasoning, in-context learning) |
| **Multimodal** | Some FMs handle text + images + audio + video |

### Foundation Model Lifecycle
```
Data Selection → Model Architecture → Pre-training → Evaluation → 
Fine-tuning/Customization → Deployment → Feedback → Iteration
```

1. **Data Selection** — Curate large, diverse, representative training data
2. **Model Selection** — Choose architecture (Transformer, diffusion, etc.)
3. **Pre-training** — Train on massive data using self-supervised objectives (next-token prediction, masked language modeling)
4. **Evaluation** — Benchmark performance (ROUGE, BLEU, BERTScore, human eval)
5. **Fine-tuning** — Adapt with labeled, task-specific data
6. **Deployment** — Serve via API endpoints for inference
7. **Feedback** — Collect user feedback, RLHF, monitor performance
8. **Iteration** — Retrain, update, or switch models as needed

### Types of Foundation Models

| Type | Description | Examples |
|---|---|---|
| **Large Language Models (LLMs)** | Text-in, text-out; trained on text corpora | Claude, Llama, Titan Text, Mistral, DeepSeek, Qwen |
| **Multimodal Models** | Handle multiple modalities (text + image + audio + video) | Claude (vision), Nova Pro/Lite, Llama 4, Pixtral |
| **Diffusion Models** | Generate images/video from noise via iterative denoising | Stable Diffusion, Titan Image Generator, Nova Canvas |
| **Embedding Models** | Convert inputs to vector representations for search/similarity | Titan Text Embeddings, Cohere Embed, Nova Multimodal Embeddings, Marengo Embed |
| **Speech Models** | Process or generate speech/audio | Nova Sonic, Voxtral |
| **Video Models** | Generate video from text/image prompts | Nova Reel, Luma Ray |
| **Reranking Models** | Re-score and reorder search results for relevance | Amazon Rerank, Cohere Rerank |

### Transformer Architecture (Exam Essentials)
- **Self-Attention Mechanism** — Allows model to weigh importance of different parts of input
- **Tokens** — Text broken into sub-word units; models process tokens, not words
- **Context Window** — Maximum number of tokens a model can process at once
- **Parameters** — Learned weights; more parameters generally = more capable but more expensive
- **Encoder-Decoder** — Original Transformer; encoder processes input, decoder generates output
  - Encoder-only: BERT (classification, embeddings)
  - Decoder-only: GPT, Claude, Llama (text generation)
  - Encoder-Decoder: T5, BART (translation, summarization)

### Inference Parameters
| Parameter | Effect | Exam Tip |
|---|---|---|
| **Temperature** | Controls randomness; 0 = deterministic, 1+ = creative | Lower for factual tasks, higher for creative |
| **Top-P (Nucleus Sampling)** | Limits token selection to cumulative probability P | Alternative to temperature for controlling diversity |
| **Top-K** | Limits selection to top K most probable tokens | Reduces unlikely outputs |
| **Max Tokens** | Maximum output length | Controls response length and cost |
| **Stop Sequences** | Tokens that signal the model to stop generating | Prevents runaway generation |

### Data Flow
```
Input (Prompt/Text/Image) → Tokenization → FM Processing (Transformer layers) → 
Token Generation → De-tokenization → Output (Text/Image/Embeddings)
```

### Integration Points
- **Amazon Bedrock** — Managed access to FMs from 17+ providers (see Model Providers table below)
- **Amazon SageMaker JumpStart** — Deploy and fine-tune open-source FMs
- **Amazon SageMaker Canvas** — No-code FM access for business users
- **Amazon Bedrock Knowledge Bases** — RAG with FMs
- **Amazon Bedrock Agents** — Multi-step task execution with FMs
- **Amazon Bedrock Guardrails** — Content filtering and safety for FM outputs
- **Cross-Region Inference Profiles** — Route inference across multiple regions for availability and throughput

### Amazon Bedrock Model Providers (as of 2025)

| Provider | Key Models | Modalities |
|---|---|---|
| **Amazon** | Nova Micro/Lite/Pro/Premier, Nova Canvas, Nova Reel, Nova Sonic, Titan Text/Embeddings/Image | Text, Image, Video, Speech, Embedding |
| **Anthropic** | Claude Haiku, Sonnet, Opus (4.x series) | Text, Image → Text |
| **Meta** | Llama 3.x, Llama 4 Scout/Maverick | Text, Image → Text |
| **Mistral AI** | Mistral Large 3, Mixtral, Ministral, Pixtral, Devstral, Voxtral | Text, Image, Speech → Text |
| **Cohere** | Command R/R+, Embed v4, Rerank 3.5 | Text, Image → Text, Embedding |
| **AI21 Labs** | Jamba 1.5 Large/Mini | Text → Text |
| **Stability AI** | Stable Diffusion 3.5, Stable Image (Core, Ultra, Inpaint, etc.) | Text, Image → Image |
| **DeepSeek** | DeepSeek-R1 (reasoning), DeepSeek-V3.x | Text → Text |
| **Google** | Gemma 3 (4B, 12B, 27B) | Text, Image → Text |
| **Qwen** | Qwen3 (32B, 235B), Qwen3-Coder, Qwen3-VL | Text, Image → Text |
| **NVIDIA** | Nemotron Nano (9B, 12B, 30B) | Text, Image → Text |
| **OpenAI** | GPT-OSS (20B, 120B), GPT-OSS Safeguard | Text → Text |
| **Luma AI** | Ray v2 | Text → Video |
| **MiniMax** | MiniMax M2/M2.1 | Text → Text |
| **Moonshot AI** | Kimi K2 Thinking, Kimi K2.5 | Text, Image → Text |
| **TwelveLabs** | Marengo Embed, Pegasus | Text, Image, Speech, Video → Text, Embedding |
| **Writer** | Palmyra X4/X5 | Text → Text |
| **Z.AI** | GLM 4.7/4.7 Flash | Text → Text |

> **Exam Tip**: You don't need to memorize every model. Know that Bedrock provides access to multiple third-party and first-party FMs, and understand the modality categories (text, image, video, speech, embedding).

---

## 4. Key Features & Components

### FM Selection Criteria (Task 3.1)
When choosing a foundation model, consider:

| Criteria | Description |
|---|---|
| **Task Performance** | Accuracy for your specific use case |
| **Modality** | Text-only, multimodal (text + image + audio + video) |
| **Latency** | Response time requirements |
| **Cost** | Token-based pricing, provisioned throughput |
| **Context Window** | Max input/output token length |
| **Model Size** | Parameter count affects capability and cost |
| **Customization** | Fine-tuning support, continued pre-training |
| **Multi-lingual** | Language support requirements |
| **Compliance** | Data residency, licensing, responsible AI |
| **Prompt Caching** | Reduce latency/cost for repeated context |

### FM Customization Spectrum (Cost vs. Effort)

```
Least Effort/Cost ←————————————————————→ Most Effort/Cost

Prompt Engineering → RAG → Fine-tuning → Continued Pre-training → Pre-training from Scratch
```

| Approach | Description | When to Use | Cost |
|---|---|---|---|
| **Prompt Engineering** | Craft better prompts; no model changes | First approach; quick iteration | Lowest |
| **In-Context Learning** | Provide examples in the prompt (few-shot) | When examples improve output quality | Low |
| **RAG** | Retrieve external knowledge, inject into prompt | Need current/proprietary data without retraining | Medium |
| **Fine-tuning** | Train on labeled data; updates model weights | Need domain-specific behavior, tone, format | High |
| **Continued Pre-training** | Train on unlabeled domain data | Need domain vocabulary/knowledge | Higher |
| **Distillation** | Transfer knowledge from large → small model | Need smaller, faster, cheaper model | Medium-High |
| **Pre-training** | Train from scratch on massive data | Almost never needed; use existing FMs | Highest |

### Fine-tuning Methods (Task 3.3)
- **Supervised Fine-tuning** — Labeled input-output pairs; model learns task-specific patterns
- **Instruction Tuning** — Fine-tune to follow instructions better
- **Reinforcement Fine-tuning** — Use reward functions to evaluate response quality; iterative feedback
- **Transfer Learning** — Apply knowledge from one domain/task to another
- **Continued Pre-training** — Unlabeled domain data to adapt vocabulary and knowledge
- **Distillation** — Teacher model (large) trains student model (small); up to 500% faster, 75% cheaper, <2% accuracy loss

### Data Preparation for Fine-tuning
- **Data Curation** — Clean, relevant, high-quality examples
- **Data Governance** — Ensure compliance, privacy, licensing
- **Data Size** — Sufficient examples for the task (varies by model)
- **Data Labeling** — Accurate labels for supervised fine-tuning
- **Representativeness** — Diverse data to avoid bias
- **RLHF** — Human feedback to align model with preferences

### FM Evaluation Methods (Task 3.4)

| Method | Description |
|---|---|
| **Human Evaluation** | Subject matter experts rate outputs for quality, relevance, safety |
| **Benchmark Datasets** | Standardized test sets (MMLU, HellaSwag, HumanEval) |
| **Amazon Bedrock Model Evaluation** | Automatic and human evaluation workflows in Bedrock |
| **A/B Testing** | Compare models with production traffic |
| **Adversarial Testing** | Test edge cases, jailbreaks, harmful inputs |

### Advanced FM Concepts (from AWS Well-Architected GenAI Lens)

| Concept | Definition | Exam Relevance |
|---|---|---|
| **Quantization** | Reducing precision of model parameters (e.g., FP32 → INT8) to decrease memory and compute requirements | Cost optimization, edge deployment |
| **Model Gateway** | Interaction layer offering secure access to a model hub through standardized APIs | Enterprise architecture |
| **Model Hub** | Central repository providing access to FMs from first-party, third-party, and open-source providers (e.g., Bedrock) | Service selection |
| **LLMOps / GenAIOps** | Operational practices for managing FM lifecycle: selection, data prep, deployment, monitoring, governance | Operations, MLOps |
| **Prompt Catalog** | Centralized repository for storing, managing, and versioning prompts | Governance, reusability |
| **Knowledge Graph** | Structured representation of entities and relationships; enhances FM contextual understanding | RAG enhancement |
| **Zero-shot Learning** | Model performs tasks it was never explicitly trained on, without task-specific examples | FM capability |
| **Model Card** | Document with key info about a model: intended use, training data, performance, limitations, biases | Responsible AI, governance |

### FM Evaluation Metrics

| Metric | Use Case | Description |
|---|---|---|
| **ROUGE** | Summarization | Measures overlap between generated and reference summaries |
| **BLEU** | Translation | Measures n-gram precision between generated and reference text |
| **BERTScore** | General text | Semantic similarity using BERT embeddings |
| **Perplexity** | Language modeling | How well model predicts next token (lower = better) |
| **Human Evaluation** | Subjective quality | Expert ratings on relevance, coherence, safety |
| **Task-specific accuracy** | Classification, Q&A | Standard accuracy, F1, exact match |

### Capabilities and Limitations (Task 2.2)

**Advantages:**
- Adaptability — One model, many tasks
- Responsiveness — Fast inference via APIs
- Simplicity — No ML expertise needed for basic use
- Few-shot learning — Learn from examples in the prompt
- Emergent abilities — Reasoning, chain-of-thought at scale

**Limitations:**
- **Hallucinations** — Generate plausible but incorrect information
- **Nondeterminism** — Same input may produce different outputs
- **Interpretability** — Difficult to explain why a specific output was generated
- **Inaccuracy** — May produce factually wrong content
- **Knowledge Cutoff** — Training data has a date boundary
- **Bias** — Reflects biases in training data
- **Cost** — Large models are expensive to run at scale
- **Context Window Limits** — Cannot process unlimited input
- **Toxicity** — May generate harmful, offensive, or inappropriate content
- **Data Privacy** — Risk of memorizing and reproducing training data (PII leakage)

---

## 5. Exam Focus Areas

### Common Question Types
- **Scenario-based selection**: "Which model/approach should you use for [use case]?"
- **Customization comparison**: "When to use fine-tuning vs. RAG vs. prompt engineering?"
- **Evaluation metrics**: "Which metric is best for evaluating [summarization/translation]?"
- **Inference parameters**: "What does adjusting temperature do?"
- **Lifecycle questions**: "What are the steps in the FM lifecycle?"

### Gotchas
- **Fine-tuning ≠ Prompt Engineering** — Fine-tuning changes model weights; prompt engineering does not
- **RAG ≠ Fine-tuning** — RAG retrieves external data at inference time; fine-tuning trains the model
- **Continued Pre-training uses UNLABELED data** — Fine-tuning uses LABELED data
- **Temperature = 0 is NOT truly deterministic** in all models, but produces most consistent outputs
- **More parameters ≠ always better** — Smaller models can outperform larger ones for specific tasks
- **Distillation** — Creates a smaller model from a larger one; NOT the same as fine-tuning
- **Quantization ≠ Distillation** — Quantization reduces numerical precision; distillation trains a new smaller model
- **ROUGE is for summarization, BLEU is for translation** — Don't mix them up
- **Embeddings are NOT generative** — They produce vector representations, not text/images
- **Cross-Region Inference ≠ Multi-Region Deployment** — Cross-region inference profiles route requests across regions automatically for availability
- **Reranking models** improve search relevance but do NOT generate text — they re-score existing results

### Comparison Points

| | Prompt Engineering | RAG | Fine-tuning | Continued Pre-training |
|---|---|---|---|---|
| **Changes model weights?** | ❌ No | ❌ No | ✅ Yes | ✅ Yes |
| **Needs training data?** | ❌ No | ❌ No (needs knowledge base) | ✅ Yes (labeled) | ✅ Yes (unlabeled) |
| **Adds external knowledge?** | Limited (in-context) | ✅ Yes (retrieval) | Indirectly | ✅ Yes (domain) |
| **Cost** | Lowest | Medium | High | Higher |
| **Time to implement** | Minutes | Hours-Days | Days-Weeks | Weeks |
| **Best for** | Quick iteration | Current/proprietary data | Task-specific behavior | Domain adaptation |

### Decision Tree: Choosing a Customization Approach

```
Need to customize FM output?
├── Is prompt engineering sufficient?
│   └── YES → Use prompt engineering (zero-shot, few-shot, chain-of-thought)
│   └── NO ↓
├── Do you need current/external/proprietary data in responses?
│   └── YES → Use RAG (Bedrock Knowledge Bases)
│   └── NO ↓
├── Do you need the model to learn specific behavior/format/tone?
│   └── YES → Fine-tune with labeled data (Bedrock or SageMaker)
│   └── NO ↓
├── Do you need the model to understand domain-specific vocabulary?
│   └── YES → Continued Pre-training with unlabeled domain data
│   └── NO ↓
└── Do you need a smaller, faster, cheaper model?
    └── YES → Model Distillation (Bedrock)
```

### Decision Tree: Choosing an FM

```
What is your primary need?
├── Text generation/chat/summarization → LLM (Claude, Llama, Mistral, DeepSeek, Qwen)
├── Image generation → Diffusion model (Stable Diffusion, Titan Image Generator, Nova Canvas)
├── Text + Image understanding → Multimodal model (Claude, Nova Pro/Lite, Llama 4, Pixtral)
├── Semantic search/similarity → Embedding model (Titan Embeddings, Cohere Embed v4, Nova Multimodal Embeddings)
├── Code generation → Code-optimized LLM (Claude, Devstral, Qwen3-Coder)
├── Video generation → Video model (Nova Reel, Luma Ray)
├── Speech-to-speech/text → Speech model (Nova Sonic, Voxtral)
├── Video understanding → Video model (Pegasus, Nova Pro/Lite)
├── Advanced reasoning → Reasoning model (DeepSeek-R1, Kimi K2 Thinking)
└── Reranking search results → Reranking model (Amazon Rerank, Cohere Rerank)
```

### Model Selection: Functional Requirements Mapping (AWS Prescriptive Guidance)

When selecting an FM, map requirements across three dimensions:
1. **Functional Requirements** — Non-negotiable capabilities (multimodal support, function-calling, domain-specific pre-training, language support)
2. **Performance vs. Cost Trade-offs** — Balance model intelligence against operational costs; test smaller variants to understand quality degradation curve
3. **Latency Considerations** — Real-time apps need sub-second responses; analytical tasks can tolerate multi-second latencies

**Common Pitfalls to Avoid:**
- Don't over-optimize prematurely — PoC phase is about proving feasibility
- Don't ignore specialized models — Test domain-specific models early
- Don't assume bigger is better — Smaller models with well-crafted prompts can outperform larger ones

---

## 6. Best Practices

### Security
- Use **Amazon Bedrock** for managed, secure FM access — data never used to train base models
- Enable **VPC endpoints** for private connectivity to FM APIs
- Use **IAM policies** to control model access per user/role
- Encrypt data in transit (TLS) and at rest (KMS)
- Apply **Bedrock Guardrails** for content filtering, PII redaction, topic blocking
- Fine-tuning data transferred securely through Amazon VPC

### Cost Optimization
- Start with **prompt engineering** before fine-tuning (lowest cost)
- Use **RAG** instead of fine-tuning when possible (no training cost)
- Choose **smaller models** when task doesn't require large model capabilities
- Use **on-demand pricing** for variable workloads, **provisioned throughput** for steady traffic
- Leverage **model distillation** — up to 75% cheaper than original models
- Use **prompt caching** for repeated context to reduce token costs
- Monitor token usage with **CloudWatch** and set **AWS Budgets** alerts

### Performance
- Select model based on **task-specific benchmarks**, not just parameter count
- Tune **inference parameters** (temperature, top-p, max tokens) for your use case
- Use **Bedrock Model Evaluation** to compare models before production deployment
- Implement **RAG** to reduce hallucinations and improve factual accuracy
- Use **provisioned throughput** for consistent low-latency responses
- Consider **model distillation** for latency-sensitive applications (up to 500% faster)

### Operations
- Use **Amazon Bedrock Model Evaluation** for automated and human evaluation workflows
- Monitor model performance with **CloudWatch metrics** (latency, token usage, errors)
- Implement **Guardrails** to enforce content policies in production
- Version control prompts and fine-tuning datasets
- Establish feedback loops for continuous improvement (RLHF)
- Use **CloudTrail** for audit logging of all Bedrock API calls

---

## 7. Hands-On Labs

### Quick Setup: Access FMs via Amazon Bedrock
1. Open **Amazon Bedrock Console** → Model access → Request access to desired models
2. Navigate to **Playgrounds** → Text/Chat/Image playground
3. Select a model (e.g., Claude 3 Haiku) and experiment with prompts
4. Adjust inference parameters (temperature, max tokens) and observe output changes

### Exercise 1: Compare FM Outputs
1. In Bedrock Chat Playground, select **Claude 3 Haiku**
2. Ask: "Explain quantum computing in 3 sentences"
3. Switch to **Titan Text Express** and ask the same question
4. Compare quality, style, and length of responses
5. Try adjusting **temperature** from 0 to 1 and observe differences

### Exercise 2: Model Evaluation
1. Navigate to **Bedrock → Model Evaluation**
2. Create an automatic evaluation job
3. Select 2-3 models to compare
4. Choose evaluation metrics (e.g., ROUGE for summarization)
5. Review results and select the best model for your use case

### CLI Commands
```bash
# List available foundation models in Bedrock
aws bedrock list-foundation-models --region us-east-1

# List models by provider
aws bedrock list-foundation-models --by-provider "amazon" --region us-east-1

# Invoke a model (Claude 3 Haiku example)
aws bedrock-runtime invoke-model \
  --model-id anthropic.claude-3-haiku-20240307-v1:0 \
  --content-type application/json \
  --accept application/json \
  --body '{"anthropic_version":"bedrock-2023-05-31","max_tokens":256,"messages":[{"role":"user","content":"What is a foundation model?"}]}' \
  output.json

# List fine-tuning jobs
aws bedrock list-model-customization-jobs --region us-east-1

# Get model evaluation job details
aws bedrock get-evaluation-job --job-identifier <job-id> --region us-east-1
```

---

## 8. Sample Questions

### Question 1
**A company wants to build a customer support chatbot that can answer questions about their proprietary product documentation. The documentation is updated weekly. Which approach should they use to customize a foundation model?**

A) Fine-tune the model weekly with the updated documentation
B) Use Retrieval Augmented Generation (RAG) with Amazon Bedrock Knowledge Bases
C) Continuously pre-train the model with the documentation
D) Increase the model's temperature parameter to generate more creative responses

**Correct Answer: B**

**Explanation:**
- ✅ **B** — RAG is ideal because it retrieves current information from an external knowledge base at inference time. Weekly documentation updates are automatically reflected without retraining. Bedrock Knowledge Bases provides managed RAG.
- ❌ **A** — Fine-tuning weekly is expensive, time-consuming, and unnecessary for this use case. Fine-tuning changes model behavior, not knowledge.
- ❌ **C** — Continued pre-training is for domain vocabulary adaptation, not for keeping up with weekly content changes.
- ❌ **D** — Temperature controls randomness/creativity, not knowledge. This would make responses less accurate.

---

### Question 2
**A data scientist is evaluating two foundation models for a text summarization task. Which metric is MOST appropriate to compare their summarization quality?**

A) BLEU
B) ROUGE
C) AUC-ROC
D) RMSE

**Correct Answer: B**

**Explanation:**
- ✅ **B** — ROUGE (Recall-Oriented Understudy for Gisting Evaluation) measures overlap between generated summaries and reference summaries. It is the standard metric for summarization tasks.
- ❌ **A** — BLEU is designed for machine translation evaluation, measuring n-gram precision.
- ❌ **C** — AUC-ROC is for binary classification model evaluation, not text generation.
- ❌ **D** — RMSE is a regression metric for numerical predictions, not text quality.

---

### Question 3
**A startup wants to deploy a foundation model for their application. They need low latency, minimal cost, and the model only needs to handle simple text classification. Which approach is MOST cost-effective?**

A) Use the largest available model with provisioned throughput
B) Fine-tune a large model and deploy it on a dedicated endpoint
C) Use a smaller foundation model with on-demand pricing
D) Pre-train a custom foundation model from scratch

**Correct Answer: C**

**Explanation:**
- ✅ **C** — For simple text classification, a smaller model is sufficient and more cost-effective. On-demand pricing avoids paying for unused capacity. Smaller models also have lower latency.
- ❌ **A** — The largest model is overkill for simple classification and provisioned throughput adds unnecessary cost.
- ❌ **B** — Fine-tuning adds training cost, and a dedicated endpoint has ongoing compute charges. Unnecessary for simple classification.
- ❌ **D** — Pre-training from scratch is the most expensive option and completely unnecessary when pre-trained models exist.

---

## Quick Reference Card

| Concept | Key Point |
|---|---|
| **Foundation Model** | Large pre-trained model adaptable to many tasks |
| **Transformer** | Architecture using self-attention; basis for most FMs |
| **Tokens** | Sub-word units processed by the model |
| **Context Window** | Max tokens the model can process at once |
| **Temperature** | Controls output randomness (0=focused, 1+=creative) |
| **ROUGE** | Summarization evaluation metric |
| **BLEU** | Translation evaluation metric |
| **BERTScore** | Semantic similarity metric |
| **RAG** | Retrieve external knowledge → inject into prompt → generate |
| **Fine-tuning** | Train with labeled data → changes model weights |
| **Continued Pre-training** | Train with unlabeled domain data → domain adaptation |
| **Distillation** | Large model teaches small model → faster, cheaper |
| **Quantization** | Reduce parameter precision → smaller, faster model |
| **RLHF** | Human feedback to align model behavior |
| **Hallucination** | Model generates plausible but incorrect information |
| **Embeddings** | Vector representations for search/similarity (NOT generative) |
| **GenAIOps/LLMOps** | Operational practices for FM lifecycle management |
| **Model Gateway** | Secure API layer for accessing model hub |
| **Cross-Region Inference** | Route requests across regions for availability |
| **Bedrock Providers** | 17+ providers: Amazon, Anthropic, Meta, Mistral, Cohere, AI21, Stability, DeepSeek, Google, Qwen, NVIDIA, OpenAI, Luma, MiniMax, Moonshot, TwelveLabs, Writer, Z.AI |

---

**Exam Tip**: The AIF-C01 heavily tests your ability to choose the RIGHT customization approach (prompt engineering vs. RAG vs. fine-tuning vs. continued pre-training) based on the scenario. Always start with the simplest, cheapest approach and escalate only when needed. Remember: RAG for current/external data, fine-tuning for behavior changes, continued pre-training for domain vocabulary.

---

## Citations
- [Supported foundation models in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/models-supported.html)
- [Definitions - AWS Well-Architected Generative AI Lens](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/definitions.html)
- [Choosing models for generative AI applications - AWS Prescriptive Guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/gen-ai-lifecycle-operational-excellence/dev-experimenting-model-selection.html)
- [Layer 2: Approved set of foundation models and tools - AWS Prescriptive Guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/strategy-enterprise-ready-gen-ai-platform/model.html)
