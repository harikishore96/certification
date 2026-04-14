# Transformer-Based Models

## 1. Topic Overview
- **Purpose**: The Transformer is a deep learning architecture that uses self-attention mechanisms to process sequential data in parallel, forming the backbone of all modern foundation models (LLMs, vision models, multimodal models)
- **Exam Weight**: HIGH
- **Exam Domain**:
  - Domain 1: Fundamentals of AI and ML (20%) — Task 1.1: Basic AI concepts and terminologies (deep learning, neural networks, LLMs)
  - Domain 2: Fundamentals of Generative AI (24%) — Understanding how foundation models work
- **Key Use Cases**:
  - Text generation (GPT, Claude, Llama, Amazon Titan)
  - Machine translation (encoder-decoder models)
  - Text summarization and question answering
  - Image generation and understanding (Vision Transformers)
  - Multimodal tasks (text + image + audio)

## 2. Exam Keyword Mapping
- **Trigger Words**: "transformer", "self-attention", "attention mechanism", "encoder-decoder", "foundation model architecture", "parallel processing of sequences", "positional encoding"
- **Scenario Indicators**:
  - "Model that processes all tokens simultaneously" → Transformer (vs. RNN/LSTM which process sequentially)
  - "Architecture behind LLMs" → Transformer
  - "Model that captures long-range dependencies in text" → Self-attention in Transformers
- **Anti-patterns**:
  - Sequential token processing → RNN/LSTM, not Transformer
  - Convolutional filters for feature extraction → CNN, not Transformer
  - Simple statistical pattern matching → Traditional ML, not Transformer

## 3. Core Concepts

### The "Attention Is All You Need" Paper (2017)
- Introduced by Vaswani et al. at Google
- Replaced recurrent (RNN/LSTM) and convolutional approaches for sequence tasks
- Key innovation: **Self-Attention Mechanism** — allows the model to weigh the importance of every token relative to every other token in a sequence

### Transformer Architecture Components

```
Input Text → Tokenization → Embedding + Positional Encoding
                                    ↓
                            ┌───────────────┐
                            │    ENCODER     │  (processes input)
                            │  Multi-Head    │
                            │  Self-Attention│
                            │  + Feed Forward│
                            └───────┬───────┘
                                    ↓
                            ┌───────────────┐
                            │    DECODER     │  (generates output)
                            │  Masked Self-  │
                            │  Attention +   │
                            │  Cross-Attention│
                            │  + Feed Forward│
                            └───────┬───────┘
                                    ↓
                              Output Tokens
```

### Key Building Blocks

| Component | Purpose | Exam Relevance |
|-----------|---------|----------------|
| **Self-Attention** | Computes relationships between all tokens in a sequence simultaneously | HIGH — Core differentiator from RNNs |
| **Multi-Head Attention** | Runs multiple attention operations in parallel to capture different relationship patterns | MEDIUM |
| **Positional Encoding** | Injects sequence order information since transformers process tokens in parallel (no inherent order) | HIGH — See [Positional Encoding](Positional-Encoding.md) |
| **Feed-Forward Network** | Applies non-linear transformations to each position independently | LOW |
| **Layer Normalization** | Stabilizes training by normalizing activations | LOW |
| **Residual Connections** | Skip connections that help with gradient flow in deep networks | LOW |

### Self-Attention Mechanism (Query-Key-Value)

The core of the transformer — for each token:
1. **Query (Q)**: "What am I looking for?"
2. **Key (K)**: "What do I contain?"
3. **Value (V)**: "What information do I provide?"

```
Attention(Q, K, V) = softmax(QK^T / √d_k) × V
```

- The dot product of Q and K determines how much attention each token pays to every other token
- Division by √d_k (scaling factor) prevents extremely large values
- Softmax converts scores to probabilities (attention weights)
- Multiply by V to get the weighted output

**Why it matters for the exam**: Self-attention allows the model to understand context across the entire input — e.g., in "The cat sat on the mat because **it** was tired", self-attention helps the model understand "it" refers to "the cat"

### Multi-Head Attention
- Instead of one attention function, runs **h** parallel attention heads
- Each head learns different types of relationships (syntactic, semantic, positional)
- Outputs are concatenated and linearly projected
- Enables the model to attend to information from different representation subspaces

## 4. Three Types of Transformer Architectures

### Encoder-Only Models
- **Architecture**: Only the encoder stack
- **How it works**: Processes the entire input bidirectionally (sees all tokens at once)
- **Best for**: Understanding/classification tasks
- **Examples**: BERT, RoBERTa, DistilBERT
- **AWS Relevance**: Amazon Comprehend uses encoder-based models for sentiment analysis, entity recognition, text classification
- **Use Cases**: Sentiment analysis, named entity recognition, text classification, semantic search

### Decoder-Only Models
- **Architecture**: Only the decoder stack
- **How it works**: Autoregressive — generates one token at a time, left-to-right, using masked self-attention (can only see previous tokens)
- **Best for**: Text generation tasks
- **Examples**: GPT family, Claude (Anthropic), Llama (Meta), Amazon Titan Text, Amazon Nova
- **AWS Relevance**: Most foundation models on Amazon Bedrock are decoder-only
- **Use Cases**: Text generation, code generation, chatbots, creative writing, summarization

### Encoder-Decoder Models
- **Architecture**: Full encoder + decoder stack with cross-attention
- **How it works**: Encoder processes input, decoder generates output while attending to encoder's representations
- **Best for**: Sequence-to-sequence tasks (input → different output)
- **Examples**: T5, BART, Flan-T5, mBART
- **AWS Relevance**: Flan-T5 is a supported architecture for [Bedrock Custom Model Import](https://docs.aws.amazon.com/bedrock/latest/userguide/model-customization-import-model.html)
- **Use Cases**: Translation, summarization, question answering

### Architecture Comparison Table

| Feature | Encoder-Only | Decoder-Only | Encoder-Decoder |
|---------|-------------|-------------|-----------------|
| **Attention** | Bidirectional | Unidirectional (causal/masked) | Bidirectional (encoder) + Causal (decoder) |
| **Primary Task** | Understanding | Generation | Sequence-to-sequence |
| **Example** | BERT | GPT, Claude, Llama, Titan | T5, BART, Flan-T5 |
| **Token Processing** | All at once | Left-to-right | Input all at once → Output left-to-right |
| **AWS Service** | Comprehend (behind the scenes) | Bedrock FMs | Bedrock Custom Import (Flan) |

## 5. Exam Focus Areas

### Common Question Types
- **"What architecture powers modern LLMs?"** → Transformer
- **"What enables transformers to process sequences in parallel?"** → Self-attention mechanism (no recurrence needed)
- **"Why do transformers need positional encoding?"** → Because self-attention has no inherent notion of token order/position
- **"Which type of model is best for text generation?"** → Decoder-only transformer
- **"Which type of model is best for text classification?"** → Encoder-only transformer

### Gotchas
- ❌ Transformers do NOT process tokens sequentially like RNNs — they process all tokens in parallel
- ❌ "Attention" is not the same as "memory" — attention computes relationships dynamically for each input, it doesn't store information across inputs
- ❌ Encoder-only models (BERT) are NOT good at text generation — they're designed for understanding
- ❌ Decoder-only models generate tokens one at a time during inference, even though training is parallelized
- ❌ Transformers don't inherently know word order — that's why positional encoding is essential

### Comparison: Transformer vs. Previous Architectures

| Feature | RNN/LSTM | CNN | Transformer |
|---------|----------|-----|-------------|
| **Sequence Processing** | Sequential (slow) | Parallel (local) | Parallel (global) |
| **Long-Range Dependencies** | Struggles (vanishing gradient) | Limited (fixed receptive field) | Excellent (self-attention) |
| **Training Speed** | Slow (can't parallelize) | Fast | Fast |
| **Context Window** | Limited by memory | Fixed kernel size | Configurable (can be very large) |
| **Scalability** | Poor | Good | Excellent |

### Decision Tree
- ✅ Use Transformer-based models when:
  - Processing natural language (text generation, understanding, translation)
  - Building conversational AI / chatbots
  - Working with foundation models on Bedrock
  - Need to capture long-range dependencies in sequences
  - Need parallel processing for faster training
- ❌ NOT ideal when:
  - Simple tabular data classification → Use traditional ML (XGBoost, Linear Regression)
  - Small dataset with limited compute → Simpler models may suffice
  - Real-time edge inference with strict latency → Consider smaller/distilled models

## 6. Advanced Attention Variants (Exam Awareness)

These appear in Bedrock's supported architectures for custom model import:

| Variant | Description | Used By |
|---------|-------------|---------|
| **Grouped Query Attention (GQA)** | Groups query heads to share key-value heads, reducing memory and improving inference speed | Llama 2/3, Mistral |
| **Sliding Window Attention (SWA)** | Limits attention to a fixed window around each token, reducing compute for long sequences | Mistral |
| **Multi-Query Attention (MQA)** | All query heads share a single key-value head — fastest but less expressive | GPTBigCode |
| **Mixture of Experts (MoE)** | Routes tokens to specialized "expert" sub-networks, activating only a subset per token | Mixtral |
| **Flash Attention** | Memory-efficient attention computation (hardware optimization, not architectural change) | SageMaker distributed training |

## 7. AWS Service Connections

| AWS Service | Transformer Relevance |
|-------------|----------------------|
| **Amazon Bedrock** | Hosts transformer-based FMs (Claude, Titan, Llama, Mistral, etc.) |
| **Amazon Bedrock Custom Model Import** | Supports importing transformer architectures: Mistral, Mixtral, Flan-T5, Llama, GPTBigCode, Qwen |
| **Amazon SageMaker** | Train/deploy custom transformer models; supports FlashAttention for distributed training |
| **Amazon SageMaker JumpStart** | Pre-trained transformer models ready to deploy |
| **Amazon Comprehend** | Uses encoder-based transformers for NLP tasks |
| **Amazon Translate** | Uses encoder-decoder transformers for translation |
| **Amazon Transcribe** | Uses transformer-based models for speech-to-text |
| **Amazon Titan** | AWS's own family of transformer-based FMs (Text, Embeddings, Image) |

## 8. Best Practices
- **Security**: Use VPC endpoints and IAM policies when deploying transformer models; encrypt model artifacts with KMS
- **Cost Optimization**: Use smaller/distilled models when full-size isn't needed; leverage Provisioned Throughput on Bedrock for predictable workloads; use SageMaker Managed Spot Training for fine-tuning
- **Performance**: Choose appropriate context window size; use quantization (reducing precision) to speed up inference; select attention variants (GQA, MQA) for faster inference
- **Operations**: Monitor token usage and latency via CloudWatch; set up Bedrock Guardrails for content safety

## 9. Sample Questions

### Question 1
**A company wants to build a text classification system to categorize customer support tickets. Which type of transformer architecture is MOST suitable?**

A) Decoder-only transformer (like GPT)
B) Encoder-only transformer (like BERT)
C) Encoder-decoder transformer (like T5)
D) Recurrent Neural Network (RNN)

**Correct Answer: B**
- ✅ B: Encoder-only models like BERT are designed for understanding and classification tasks. They process the entire input bidirectionally, making them ideal for text classification.
- ❌ A: Decoder-only models are optimized for text generation, not classification.
- ❌ C: Encoder-decoder models are best for sequence-to-sequence tasks like translation. Overkill for classification.
- ❌ D: RNNs are older architectures that struggle with long sequences and are slower to train.

### Question 2
**What is the PRIMARY advantage of the self-attention mechanism in transformers compared to RNNs?**

A) It uses less memory during training
B) It processes tokens sequentially for better accuracy
C) It captures relationships between all tokens in parallel, regardless of distance
D) It eliminates the need for training data

**Correct Answer: C**
- ✅ C: Self-attention computes relationships between every pair of tokens simultaneously, capturing long-range dependencies that RNNs struggle with due to vanishing gradients.
- ❌ A: Transformers actually use MORE memory than RNNs (quadratic attention complexity).
- ❌ B: The opposite — transformers process tokens in parallel, not sequentially.
- ❌ D: All ML models require training data.

### Question 3
**A machine learning team wants to import a custom fine-tuned open-source model into Amazon Bedrock. Which of the following is a supported transformer architecture for Bedrock Custom Model Import?**

A) BERT
B) ResNet
C) Llama
D) LSTM

**Correct Answer: C**
- ✅ C: Llama (2, 3, 3.1, 3.2, 3.3) is a supported decoder-only transformer architecture for Bedrock Custom Model Import.
- ❌ A: BERT is an encoder-only model and is not listed as a supported architecture for Bedrock Custom Model Import.
- ❌ B: ResNet is a CNN architecture for image classification, not a transformer.
- ❌ D: LSTM is a recurrent architecture, not a transformer.

---

**Exam Tip**: For the AIF-C01, you don't need to know the math behind attention — focus on understanding WHY transformers are better than RNNs (parallel processing, long-range dependencies), the THREE architecture types (encoder-only, decoder-only, encoder-decoder), and which AWS services use transformer-based models. Know that virtually all foundation models on Bedrock are transformer-based.

---

**Citations**:
- [Amazon Bedrock Custom Model Import — Supported Architectures](https://docs.aws.amazon.com/bedrock/latest/userguide/model-customization-import-model.html)
- [SageMaker FlashAttention Support](https://docs.aws.amazon.com/sagemaker/latest/dg/model-parallel-attention-head-size-for-flash-attention.html)
- [AIF-C01 Domain 1: Fundamentals of AI and ML](https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain1.html)
