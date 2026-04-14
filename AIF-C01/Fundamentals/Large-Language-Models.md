# Large Language Models (LLMs)

## 1. Topic Overview
- **Purpose**: Large Language Models are transformer-based neural networks trained on massive text corpora that generate, understand, and reason about natural language — they are the core engine behind modern generative AI
- **Service Level**: N/A (concept, not a service — accessed via Amazon Bedrock, SageMaker JumpStart)
- **Key Use Cases**:
  - Text generation (articles, emails, marketing copy)
  - Conversational AI (chatbots, virtual assistants)
  - Code generation and debugging
  - Summarization and translation
  - Question answering over documents (with RAG)
- **Exam Weight**: HIGH
- **Exam Domains**:
  - Domain 2: Fundamentals of Generative AI (24%) — Task 2.1, 2.2
  - Domain 3: Applications of Foundation Models (28%) — Task 3.1, 3.3, 3.4
- **Task Statements**:
  - 2.1: Explain basic concepts of GenAI (transformer-based LLMs, tokens, context window, inference parameters)
  - 2.2: Understand capabilities and limitations of GenAI (hallucinations, nondeterminism, knowledge cutoff)
  - 3.1: Describe design considerations for FM applications (model selection, inference parameters, RAG)
  - 3.3: Describe training and fine-tuning process for FMs
  - 3.4: Describe methods to evaluate FM performance

---

## 2. Exam Keyword Mapping

### Trigger Words
- "Large language model", "LLM", "text generation", "next-token prediction"
- "Transformer", "self-attention", "context window", "tokens"
- "Autoregressive", "decoder-only", "GPT-style"
- "Inference parameters", "temperature", "top-p", "top-k"
- "Hallucination", "nondeterministic", "knowledge cutoff"

### Scenario Indicators
- "Need to generate human-like text responses" → LLM
- "Need a conversational AI assistant" → LLM (via Bedrock)
- "Model produces plausible but incorrect information" → LLM hallucination
- "Want more creative/diverse outputs" → Increase temperature
- "Want more consistent/factual outputs" → Decrease temperature
- "Model doesn't know about recent events" → Knowledge cutoff → Use RAG

### Anti-patterns
- "Need to generate images from text" → NOT an LLM → Diffusion model (Stable Diffusion, Nova Canvas)
- "Need to classify images or detect objects" → NOT an LLM → Amazon Rekognition or vision model
- "Need structured tabular predictions" → NOT an LLM → Traditional ML (SageMaker)
- "Need vector representations for search" → NOT an LLM → Embedding model

---

## 3. Core Concepts

### What is a Large Language Model?
- A **deep neural network** built on the **Transformer architecture** (specifically the decoder portion)
- Trained via **self-supervised learning** on massive text datasets (books, web pages, code, etc.)
- Primary training objective: **next-token prediction** — given a sequence of tokens, predict the most probable next token
- Scale: billions to trillions of **parameters** (learned weights during training)
- The "large" in LLM refers to both the **model size** (parameter count) and the **training data** volume

### How LLMs Work — Step by Step

```
Input Text → Tokenization → Positional Encoding → Transformer Layers (Self-Attention + Feed-Forward) 
→ Probability Distribution over Vocabulary → Token Sampling → De-tokenization → Output Text
```

1. **Tokenization** — Input text is split into sub-word tokens (e.g., "unhappiness" → ["un", "happiness"])
2. **Positional Encoding** — Position information is added so the model knows token order
3. **Self-Attention** — Each token attends to every other token to understand context and relationships
4. **Feed-Forward Layers** — Transform attention outputs through neural network layers
5. **Output Distribution** — Model produces a probability distribution over the entire vocabulary for the next token
6. **Token Sampling** — A token is selected based on the distribution (controlled by temperature, top-p, top-k)
7. **Autoregressive Generation** — The selected token is appended to the input, and the process repeats until a stop condition

### Key Terminology

| Term | Definition | Exam Relevance |
|---|---|---|
| **Token** | Sub-word unit; the atomic unit LLMs process (not whole words) | HIGH — understand tokenization |
| **Context Window** | Maximum number of tokens the model can process in a single request (input + output) | HIGH — limits on input size |
| **Parameters** | Learned weights in the neural network (billions); determine model capability | HIGH — bigger ≠ always better |
| **Self-Attention** | Mechanism allowing each token to "attend to" all other tokens in the sequence | MEDIUM — core of Transformer |
| **Autoregressive** | Generates one token at a time, using previous tokens as context | MEDIUM — explains sequential generation |
| **Pre-training** | Initial training on massive unlabeled data using self-supervised objectives | HIGH — understand training phases |
| **Next-Token Prediction** | The core training objective — predict the next token given preceding tokens | HIGH — fundamental concept |
| **Perplexity** | Measures how well the model predicts the next token; lower = better | MEDIUM — evaluation metric |
| **Latent Space** | Internal representation space where the model encodes meaning | LOW — conceptual understanding |

### Transformer Architecture Variants

| Variant | Description | Examples | Best For |
|---|---|---|---|
| **Encoder-only** | Processes input bidirectionally; understands context from both directions | BERT | Classification, embeddings, NER |
| **Decoder-only** | Processes input left-to-right; generates text autoregressively | GPT, Claude, Llama, Mistral | Text generation, chat, code |
| **Encoder-Decoder** | Encoder processes input, decoder generates output | T5, BART | Translation, summarization |

> **Exam Tip**: Most modern LLMs (Claude, GPT, Llama, Mistral) are **decoder-only** transformers. Know that "LLM" in the exam context almost always means a decoder-only text generation model.

### LLM Training Phases

```
Phase 1: Pre-training          Phase 2: Fine-tuning           Phase 3: Alignment
(Self-supervised)              (Supervised)                    (RLHF / Reinforcement)
─────────────────────          ──────────────────              ─────────────────────
• Massive unlabeled data       • Labeled task-specific data    • Human preference data
• Next-token prediction        • Instruction following         • Reward model training
• Learns language patterns     • Domain adaptation             • Policy optimization
• Most expensive phase         • Moderate cost                 • Aligns with human values
• Weeks/months on GPU clusters • Hours/days                    • Reduces harmful outputs
```

1. **Pre-training**: Model learns language structure, grammar, facts, and reasoning from vast text corpora. This is the most compute-intensive phase.
2. **Fine-tuning**: Model is adapted to specific tasks or domains using labeled examples (e.g., instruction-following, medical Q&A).
3. **Alignment (RLHF)**: Reinforcement Learning from Human Feedback aligns the model with human preferences — makes it helpful, harmless, and honest.

### LLM Inference Parameters (Exam Critical)

Per [Amazon Bedrock documentation](https://docs.aws.amazon.com/bedrock/latest/userguide/inference-parameters.html), these parameters control how LLMs generate responses:

#### Randomness and Diversity

| Parameter | What It Does | Lower Value | Higher Value | Use Case |
|---|---|---|---|---|
| **Temperature** | Modulates the probability distribution for the next token | Steepens distribution → more deterministic, higher-probability tokens favored | Flattens distribution → more random, lower-probability tokens allowed | Low for factual Q&A; High for creative writing |
| **Top-K** | Limits selection to the K most probable tokens | Fewer candidates → more focused | More candidates → more diverse | K=1 is greedy decoding |
| **Top-P** (Nucleus Sampling) | Limits selection to tokens within cumulative probability P | Smaller pool → more predictable | Larger pool → more varied | P=0.9 considers top 90% of probability mass |

**Example** (from AWS docs): For the prompt `"I hear the hoof beats of"` with candidates `{horses: 0.7, zebras: 0.2, unicorns: 0.1}`:
- High **temperature** → flattens probabilities → "unicorns" becomes more likely
- **Top-K = 2** → only considers "horses" and "zebras"
- **Top-P = 0.7** → only considers "horses" (top 70% of distribution)
- **Top-P = 0.9** → considers "horses" and "zebras" (top 90%)

#### Length Controls

| Parameter | Description |
|---|---|
| **Max Tokens** | Maximum number of tokens to generate in the response |
| **Stop Sequences** | Character sequences that signal the model to stop generating |
| **Penalties** | Penalize repeated tokens, frequency of tokens, or response length |

### Data Flow

```
User Prompt → [Tokenizer] → Token IDs → [LLM Transformer Layers] → 
Next-Token Probabilities → [Sampling (temp/top-p/top-k)] → 
Selected Token → [Append to sequence] → [Repeat until stop] → 
[De-tokenizer] → Generated Text Response
```

### Integration Points (AWS Services)
- **Amazon Bedrock** — Primary managed service for accessing LLMs (Claude, Llama, Mistral, Titan, etc.)
- **Amazon Bedrock Knowledge Bases** — RAG to augment LLMs with external knowledge
- **Amazon Bedrock Agents** — LLMs that can plan and execute multi-step tasks with tool use
- **Amazon Bedrock Guardrails** — Content filtering, PII redaction, topic blocking for LLM outputs
- **Amazon SageMaker JumpStart** — Deploy and fine-tune open-source LLMs
- **Amazon SageMaker** — Custom training and hosting of LLMs
- **AWS Trainium / Inferentia** — Custom chips optimized for LLM training and inference

---

## 4. Key Features & Components

### LLM Capabilities

| Capability | Description | Example |
|---|---|---|
| **Text Generation** | Produce coherent, contextual text | Write an email, generate a report |
| **Summarization** | Condense long text into key points | Summarize a 50-page document |
| **Translation** | Convert text between languages | English → French translation |
| **Question Answering** | Answer questions based on context | "What is the capital of France?" |
| **Code Generation** | Write, debug, and explain code | Generate a Python function |
| **Reasoning** | Multi-step logical reasoning | Chain-of-thought problem solving |
| **In-Context Learning** | Learn from examples provided in the prompt (few-shot) | Provide 3 examples → model follows pattern |
| **Instruction Following** | Follow natural language instructions | "Rewrite this in a formal tone" |
| **Sentiment Analysis** | Determine emotional tone of text | Classify review as positive/negative |
| **Entity Extraction** | Identify named entities in text | Extract names, dates, locations |

### LLM Limitations (Exam Critical)

| Limitation | Description | Mitigation |
|---|---|---|
| **Hallucinations** | Generates plausible but factually incorrect information | RAG, grounding, human review, Guardrails |
| **Knowledge Cutoff** | Training data has a date boundary; no awareness of recent events | RAG with current data sources |
| **Nondeterminism** | Same prompt can produce different outputs across invocations | Lower temperature, set seed (if supported) |
| **Context Window Limits** | Cannot process unlimited input; truncates beyond limit | Chunking, summarization, RAG |
| **Bias** | Reflects biases present in training data | Bias detection (SageMaker Clarify), diverse training data |
| **Toxicity** | May generate harmful, offensive, or inappropriate content | Bedrock Guardrails, content filtering |
| **PII Leakage** | Risk of memorizing and reproducing personal data from training | Guardrails PII redaction, data governance |
| **Lack of Explainability** | Difficult to explain why a specific output was generated | Model cards, human review |
| **Cost at Scale** | Large models are expensive for high-throughput applications | Smaller models, distillation, quantization, prompt caching |
| **Mathematical Reasoning** | Can struggle with complex math and logic | Chain-of-thought prompting, tool use |

### Emergent Abilities
Capabilities that appear only at sufficient model scale:
- **Chain-of-thought reasoning** — Step-by-step problem solving
- **Zero-shot generalization** — Performing tasks never explicitly trained on
- **In-context learning** — Learning from examples in the prompt without weight updates
- **Instruction following** — Understanding and executing complex instructions
- **Code understanding** — Reading, writing, and debugging code

### LLM Optimization Techniques

| Technique | Description | Trade-off |
|---|---|---|
| **Quantization** | Reduce numerical precision of weights (FP32 → INT8/INT4) | Smaller, faster; slight accuracy loss |
| **Distillation** | Train a smaller "student" model from a larger "teacher" model | Much smaller/faster; some capability loss |
| **Pruning** | Remove unnecessary weights/connections from the model | Smaller model; may lose some capability |
| **Prompt Caching** | Cache repeated prompt prefixes to avoid reprocessing | Reduces latency and cost for repeated contexts |
| **Speculative Decoding** | Use a small model to draft tokens, large model to verify | Faster generation with same quality |
| **Batching** | Process multiple requests together | Higher throughput; slightly higher latency |

### LLMs Available on Amazon Bedrock

| Model Family | Provider | Key Strengths |
|---|---|---|
| **Claude** (Haiku, Sonnet, Opus) | Anthropic | Reasoning, safety, long context, vision |
| **Llama** (3.x, 4) | Meta | Open-source, multilingual, multimodal |
| **Mistral** (Large, Mixtral) | Mistral AI | Efficient, multilingual, code |
| **Titan Text** | Amazon | General purpose, embeddings |
| **Nova** (Micro, Lite, Pro, Premier) | Amazon | Cost-effective, multimodal, speed tiers |
| **DeepSeek-R1** | DeepSeek | Advanced reasoning |
| **Qwen3** | Qwen | Multilingual, code, vision |
| **Command R/R+** | Cohere | RAG-optimized, enterprise search |
| **Jamba** | AI21 Labs | Long context, efficiency |

---

## 5. Exam Focus Areas

### Common Question Types
- **"What causes hallucinations?"** → LLM generates text based on patterns, not factual verification
- **"How to reduce hallucinations?"** → RAG, grounding, lower temperature, Guardrails
- **"What does temperature control?"** → Randomness/diversity of output
- **"Difference between LLM and traditional ML?"** → LLM generates text; traditional ML makes structured predictions
- **"When to use RAG vs. fine-tuning?"** → RAG for external/current data; fine-tuning for behavior/format changes
- **"What is the context window?"** → Maximum tokens the model can process (input + output)

### Gotchas
- **LLMs are NOT databases** — They don't "store" facts; they learn statistical patterns. This is why they hallucinate
- **Temperature = 0 does NOT guarantee identical outputs** — It makes outputs more consistent but not truly deterministic in all implementations
- **More parameters ≠ always better** — A well-prompted smaller model can outperform a poorly-prompted larger model
- **LLMs don't "understand" in the human sense** — They predict statistically likely next tokens
- **Context window includes BOTH input AND output tokens** — A 128K context window doesn't mean 128K input + 128K output
- **Fine-tuning changes weights; RAG does NOT** — RAG only provides context at inference time
- **LLMs are autoregressive** — They generate one token at a time, sequentially (not all at once)
- **Tokens ≠ words** — A single word may be multiple tokens; a common word may be one token
- **Top-P and Top-K are alternatives to temperature** — They all control output diversity but through different mechanisms
- **Perplexity is for LLM evaluation, not classification** — Don't confuse with accuracy/F1/AUC-ROC

### Comparison: LLM vs. Other Model Types

| | LLM | Embedding Model | Diffusion Model | Traditional ML |
|---|---|---|---|---|
| **Input** | Text (+ images for multimodal) | Text/Image | Text prompt + noise | Structured/tabular data |
| **Output** | Generated text | Vector representation | Generated image/video | Prediction (class/value) |
| **Architecture** | Transformer (decoder) | Transformer (encoder) | U-Net + noise scheduler | Various (trees, SVM, NN) |
| **Training** | Next-token prediction | Contrastive learning | Denoising | Supervised/unsupervised |
| **Use Case** | Generation, chat, reasoning | Search, similarity, RAG | Image/video generation | Classification, regression |
| **AWS Service** | Bedrock, SageMaker | Bedrock (Titan Embeddings) | Bedrock (Stable Diffusion) | SageMaker |

### Decision Tree: Is an LLM the Right Choice?

```
Does your task involve generating or understanding natural language?
├── NO → Consider traditional ML, computer vision, or other AI services
└── YES ↓
    Does the task require creative/open-ended text generation?
    ├── YES → LLM (via Bedrock)
    │   ├── Need current/proprietary knowledge? → Add RAG
    │   ├── Need specific behavior/format? → Fine-tune
    │   └── Need safety controls? → Add Guardrails
    └── NO ↓
        Is it a specific NLP task (sentiment, entities, translation)?
        ├── YES → Consider pre-built AI services first:
        │   ├── Sentiment → Amazon Comprehend
        │   ├── Translation → Amazon Translate
        │   ├── Speech-to-text → Amazon Transcribe
        │   └── Entities → Amazon Comprehend
        └── NO → Evaluate LLM vs. traditional ML based on data type
```

---

## 6. Best Practices

### Security
- Access LLMs through **Amazon Bedrock** — data is encrypted in transit and at rest, never used to train base models
- Use **Bedrock Guardrails** to filter harmful content, redact PII, and block restricted topics
- Apply **IAM policies** to control which users/roles can invoke specific models
- Enable **VPC endpoints** for private connectivity (no internet traversal)
- Use **CloudTrail** to audit all LLM API invocations
- Implement **prompt injection defenses** — validate and sanitize user inputs

### Cost Optimization
- Start with **prompt engineering** before considering fine-tuning (zero additional cost)
- Use **smaller models** (e.g., Nova Micro, Claude Haiku) for simple tasks — don't use a large model when a small one suffices
- Leverage **prompt caching** for repeated system prompts or context
- Use **on-demand pricing** for variable workloads; **provisioned throughput** for steady, high-volume traffic
- Monitor **token usage** with CloudWatch — set budget alerts
- Consider **model distillation** for production workloads (up to 75% cost reduction per AWS)
- Minimize **max tokens** parameter to avoid generating unnecessarily long responses
- Use **batch inference** for non-real-time workloads

### Performance
- Tune **inference parameters** systematically — use ground truth data to find optimal temperature/top-p ranges ([AWS Well-Architected GenAI Lens](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/genperf02-bp02.html))
- Use the **LLM-as-a-judge** pattern to automate evaluation of response quality
- Implement **RAG** to reduce hallucinations and improve factual accuracy
- Use **cross-region inference profiles** for high availability and throughput
- Consider **latency-optimized inference** in Bedrock for real-time applications
- Use **streaming responses** for better user experience in chat applications

### Operations
- Use **Amazon Bedrock Model Evaluation** to benchmark models before production
- Monitor with **CloudWatch** — track latency, token usage, error rates, throttling
- Version control prompts and maintain a **prompt catalog**
- Implement **feedback loops** — collect user feedback to improve prompts and evaluate model performance
- Use **Model Cards** to document model capabilities, limitations, and intended use
- Establish **GenAIOps/LLMOps** practices for lifecycle management

---

## 7. Hands-On Labs

### Quick Setup: Chat with an LLM via Bedrock
1. Open **Amazon Bedrock Console** → Model access → Enable Claude 3 Haiku
2. Navigate to **Chat Playground**
3. Select **Claude 3 Haiku** → Type a prompt → Observe response
4. Adjust **temperature** from 0 to 1 → Re-run same prompt → Compare outputs
5. Change **max tokens** → Observe how response length changes

### Exercise 1: Explore Inference Parameters
1. In Bedrock Chat Playground, use this prompt: `"Write a short poem about clouds"`
2. Set **temperature = 0** → Generate → Note the output
3. Set **temperature = 1** → Generate 3 times → Note the variation
4. Set **Top-P = 0.1** → Generate → Note how focused the output becomes
5. Set **Top-K = 5** → Generate → Compare with Top-P results

### Exercise 2: Observe Hallucinations and RAG
1. Ask an LLM: `"What were the top news stories from last week?"`
2. Observe the hallucinated response (model doesn't have current data)
3. Set up a **Bedrock Knowledge Base** with recent news articles
4. Ask the same question with RAG enabled → Compare accuracy

### CLI Commands
```bash
# List available LLMs in Bedrock
aws bedrock list-foundation-models --by-output-modality TEXT --region us-east-1

# Invoke Claude 3 Haiku
aws bedrock-runtime invoke-model \
  --model-id anthropic.claude-3-haiku-20240307-v1:0 \
  --content-type application/json \
  --accept application/json \
  --body '{
    "anthropic_version": "bedrock-2023-05-31",
    "max_tokens": 256,
    "temperature": 0.7,
    "top_p": 0.9,
    "messages": [{"role": "user", "content": "Explain LLMs in 3 sentences."}]
  }' \
  output.json

# Invoke with streaming (for chat applications)
aws bedrock-runtime invoke-model-with-response-stream \
  --model-id anthropic.claude-3-haiku-20240307-v1:0 \
  --content-type application/json \
  --body '{
    "anthropic_version": "bedrock-2023-05-31",
    "max_tokens": 512,
    "messages": [{"role": "user", "content": "What is self-attention?"}]
  }' \
  output.json
```

### Python (boto3) Example
```python
import boto3
import json

bedrock = boto3.client("bedrock-runtime", region_name="us-east-1")

response = bedrock.invoke_model(
    modelId="anthropic.claude-3-haiku-20240307-v1:0",
    contentType="application/json",
    accept="application/json",
    body=json.dumps({
        "anthropic_version": "bedrock-2023-05-31",
        "max_tokens": 256,
        "temperature": 0.5,
        "top_p": 0.9,
        "messages": [
            {"role": "user", "content": "What is a large language model?"}
        ]
    })
)

result = json.loads(response["body"].read())
print(result["content"][0]["text"])
```

---

## 8. Sample Questions

### Question 1
**A company is using an LLM via Amazon Bedrock to generate customer support responses. Users report that the model sometimes provides incorrect product specifications. What is the MOST effective way to reduce these inaccuracies?**

A) Increase the temperature parameter to generate more diverse responses
B) Implement Retrieval Augmented Generation (RAG) with the company's product database
C) Switch to a larger LLM with more parameters
D) Decrease the Top-K parameter to 1

**Correct Answer: B**

**Explanation:**
- ✅ **B** — RAG retrieves accurate product information from the company's database and provides it as context to the LLM. This grounds the response in factual data, directly addressing the hallucination problem.
- ❌ **A** — Increasing temperature makes outputs MORE random and diverse, which would likely INCREASE inaccuracies.
- ❌ **C** — A larger model may still hallucinate. Model size doesn't solve the fundamental issue of lacking access to proprietary product data.
- ❌ **D** — Top-K=1 (greedy decoding) makes output more deterministic but doesn't add factual knowledge. The model would still hallucinate, just more consistently.

---

### Question 2
**A developer wants their LLM application to produce highly consistent, factual responses for a financial reporting use case. Which combination of inference parameters should they configure?**

A) High temperature, high Top-P, high max tokens
B) Low temperature, low Top-P, appropriate max tokens
C) High temperature, low Top-K, low max tokens
D) Low temperature, high Top-K, high Top-P

**Correct Answer: B**

**Explanation:**
- ✅ **B** — Low temperature steepens the probability distribution, favoring high-probability (more likely correct) tokens. Low Top-P restricts the candidate pool to the most probable tokens. Together, they produce consistent, focused outputs ideal for factual reporting.
- ❌ **A** — High temperature and high Top-P increase randomness and diversity — the opposite of what's needed for factual consistency.
- ❌ **C** — High temperature introduces randomness, which contradicts the need for consistency.
- ❌ **D** — While low temperature is correct, high Top-K and high Top-P allow more diverse token selection, partially undermining the consistency goal.

---

### Question 3
**An ML engineer is explaining LLM architecture to their team. Which statement CORRECTLY describes how a decoder-only transformer LLM generates text?**

A) The model processes the entire input bidirectionally and outputs all tokens simultaneously
B) The model generates tokens one at a time, using previously generated tokens as additional context for each subsequent token
C) The model retrieves pre-written responses from a database and combines them into a coherent answer
D) The model uses an encoder to understand the input and a separate decoder to generate the output

**Correct Answer: B**

**Explanation:**
- ✅ **B** — Decoder-only LLMs are autoregressive: they generate one token at a time, appending each generated token to the input sequence before predicting the next. This is the fundamental mechanism of models like Claude, GPT, and Llama.
- ❌ **A** — Bidirectional processing describes encoder-only models (like BERT), not decoder-only LLMs. LLMs also generate tokens sequentially, not simultaneously.
- ❌ **C** — LLMs generate novel text through next-token prediction, not by retrieving pre-written responses. (RAG adds retrieval, but the LLM itself generates.)
- ❌ **D** — This describes an encoder-decoder architecture (like T5 or BART), not a decoder-only LLM.

---

## Quick Reference Card

| Concept | Key Point |
|---|---|
| **LLM** | Transformer-based model that generates text via next-token prediction |
| **Decoder-only** | Architecture of most modern LLMs (Claude, GPT, Llama) — left-to-right generation |
| **Autoregressive** | Generates one token at a time, each conditioned on all previous tokens |
| **Self-Attention** | Mechanism that lets each token attend to all other tokens in the sequence |
| **Next-Token Prediction** | Core training objective — predict the most probable next token |
| **Temperature** | Controls randomness: low = focused/deterministic, high = creative/random |
| **Top-P** | Nucleus sampling — limits tokens to cumulative probability P |
| **Top-K** | Limits selection to K most probable tokens |
| **Context Window** | Max tokens (input + output) the model can process at once |
| **Hallucination** | Model generates plausible but incorrect information → mitigate with RAG |
| **Knowledge Cutoff** | Model only knows data up to its training date → mitigate with RAG |
| **RLHF** | Reinforcement Learning from Human Feedback — aligns model with human preferences |
| **Perplexity** | LLM evaluation metric — how well model predicts next token (lower = better) |
| **Quantization** | Reduce weight precision → smaller, faster, cheaper model |
| **Distillation** | Large model teaches small model → maintains quality at lower cost |
| **LLM-as-a-Judge** | Use one LLM to evaluate another LLM's outputs |

---

**Exam Tip**: The AIF-C01 exam frequently tests your understanding of LLM limitations (especially hallucinations) and how to mitigate them. Remember: hallucinations happen because LLMs predict statistically likely tokens, NOT because they verify facts. RAG is the primary mitigation for factual accuracy. Temperature/Top-P/Top-K control output diversity, NOT factual correctness.

---

## Citations
- [Influence response generation with inference parameters - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/inference-parameters.html)
- [Understanding LLMs and RAG - AWS Prescriptive Guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/writing-best-practices-rag/understanding.html)
- [GENPERF02-BP02 Optimize inference parameters - Generative AI Lens](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/genperf02-bp02.html)
- [A parallel timeline: the rise of large language models - AWS Prescriptive Guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-foundations/llm.html)
- [Configuring a Large Language Model (LLM) - Generative AI Application Builder](https://docs.aws.amazon.com/solutions/latest/generative-ai-application-builder-on-aws/configuring-an-llm.html)
