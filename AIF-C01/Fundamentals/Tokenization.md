# Tokenization

## 1. Topic Overview
- **Purpose**: The process of converting raw text into smaller units called tokens that machine learning models can process — the fundamental bridge between human language and model computation
- **Exam Weight**: High
- **Exam Domain**: 
  - Domain 2 — Fundamentals of Generative AI (24%)
  - Domain 3 — Applications of Foundation Models (28%)
- **Task Statements**: Task 2.1 (Explain the basic concepts of generative AI), Task 3.1 (Describe design considerations for applications that use foundation models)

## 2. Exam Keyword Mapping
- **Trigger Words**: "token", "tokenization", "context window", "input tokens", "output tokens", "token limit", "vocabulary", "subword", "embeddings"
- **Scenario Indicators**: Questions about model pricing, context window limits, prompt design, text processing for LLMs, cost optimization
- **Anti-patterns**: Tokenization is NOT the same as word splitting — modern tokenizers use subword algorithms, not simple whitespace splitting

## 3. Core Concepts

### What Is a Token?
- A sequence of characters that a model interprets as a **single unit of meaning**
- NOT always a full word — can be a word, part of a word, punctuation, or common phrase
- Examples:
  - `"Hello"` → 1 token
  - `"unhappiness"` → may become `"un"` + `"happiness"` → 2 tokens
  - `"?"` → 1 token
  - `"a lot"` → could be 1 or 2 tokens depending on tokenizer

### Rule of Thumb
- **1 token ≈ 4 characters** in English (or about ¾ of a word)
- **100 tokens ≈ 75 words** in English
- Non-English languages typically require more tokens per word
- Code typically requires more tokens than natural language

### Why Tokenization Matters
- Models don't understand raw text — they process **numerical representations** of tokens
- Token count determines **cost** (Bedrock charges per input/output token)
- Token count determines **context window** limits (how much text fits in one request)
- Tokenization quality affects model performance on different languages and domains

## 4. Types of Tokenization

### 4.1 Word-Level Tokenization
- **How**: Split text on whitespace and punctuation
- **Example**: `"I don't like cats"` → `["I", "don't", "like", "cats"]`
- **Pros**: Simple, intuitive
- **Cons**: 
  - ❌ Huge vocabulary size (every unique word needs an entry)
  - ❌ Can't handle unknown/rare words (out-of-vocabulary problem)
  - ❌ Doesn't capture word relationships (e.g., "run" vs. "running")

### 4.2 Character-Level Tokenization
- **How**: Split text into individual characters
- **Example**: `"cat"` → `["c", "a", "t"]`
- **Pros**: Very small vocabulary, no unknown words
- **Cons**: 
  - ❌ Very long sequences (slow processing)
  - ❌ Loses word-level meaning
  - ❌ Hard for model to learn meaningful patterns

### 4.3 Subword Tokenization ⭐ (Used by Modern LLMs)
- **How**: Split text into meaningful subword units — balances vocabulary size and sequence length
- **Example**: `"unhappiness"` → `["un", "happi", "ness"]`
- **Pros**: 
  - ✅ Handles rare/unknown words by breaking into known subwords
  - ✅ Manageable vocabulary size
  - ✅ Captures morphological patterns (prefixes, suffixes, roots)
- **Used by**: GPT, Claude, Llama, Titan, and virtually all modern LLMs

#### Common Subword Algorithms

| Algorithm | Used By | How It Works |
|-----------|---------|-------------|
| **BPE** (Byte Pair Encoding) | GPT, Llama | Iteratively merges most frequent character pairs |
| **WordPiece** | BERT, DistilBERT | Similar to BPE but uses likelihood-based merging |
| **SentencePiece** | T5, ALBERT, Llama | Language-agnostic, treats text as raw bytes (no pre-tokenization) |
| **Unigram** | Used with SentencePiece | Starts with large vocabulary, prunes least useful tokens |

### 4.4 Byte-Level Tokenization
- **How**: Operates on raw bytes (256 possible values)
- **Pros**: Truly language-agnostic, handles any text/code/emoji
- **Cons**: Longer sequences than subword tokenization
- **Used by**: Some multimodal models

## 5. Tokenization in the Generative AI Pipeline

```
User Input (text) → Tokenizer → Token IDs → Model (Transformer) → Output Token IDs → Detokenizer → Output Text
                     ^^^^^^^^                                                         ^^^^^^^^^^^^
                     Text → Numbers                                                   Numbers → Text
```

### Step-by-Step Process
1. **Tokenize**: Split input text into tokens using the model's vocabulary
2. **Encode**: Convert tokens to numerical IDs (integers)
3. **Embed**: Convert token IDs to dense vector representations (embeddings)
4. **Process**: Transformer model processes embeddings through attention layers
5. **Generate**: Model predicts next token ID(s)
6. **Decode**: Convert output token IDs back to text

### Vocabulary
- Each model has a fixed **vocabulary** — a mapping of tokens to numerical IDs
- Vocabulary size varies by model (typically 30K–100K+ tokens)
- Tokens not in vocabulary are broken into smaller subword pieces
- The vocabulary is determined during model training and cannot be changed at inference

## 6. Tokens in Amazon Bedrock

### Token Types

| Token Type | Description |
|-----------|-------------|
| **Input tokens** | Tokens in the request sent to the model (prompt, system instructions, context) |
| **Output tokens** | Tokens generated by the model in the response |
| **Total tokens** | Input tokens + Output tokens |
| **CacheReadInputTokens** | Tokens retrieved from prompt cache (not reprocessed) |
| **CacheWriteInputTokens** | Tokens written to prompt cache |

### Context Window
- **Definition**: Maximum number of tokens (input + output) a model can process in one request
- Includes: system prompt + user message + retrieved context (RAG) + model response
- Larger context window = can process more information per request
- Different models have different context windows:

| Model | Approximate Context Window |
|-------|---------------------------|
| Claude 3.5 Sonnet | 200K tokens |
| Claude 3 Haiku | 200K tokens |
| Amazon Titan Text Express | 8K tokens |
| Llama 3 | 8K-128K tokens |
| Amazon Nova | Up to 300K tokens |

### Pricing Model
- Bedrock charges **per token** (input and output priced separately)
- Output tokens are typically **more expensive** than input tokens
- **On-demand pricing**: Pay per token processed
- **Service tiers**: Priority (premium), Standard, Flex (discounted), Reserved (fixed capacity)
- **Cost formula**: `(Input tokens × input price) + (Output tokens × output price)`

### Token Quota Management
- **TPM** (Tokens Per Minute): Quota on tokens per minute per model
- **TPD** (Tokens Per Day): Quota on tokens per day (default = TPM × 24 × 60)
- **RPM** (Requests Per Minute): Quota on number of requests per minute
- **max_tokens**: Parameter you set to limit maximum output tokens generated
- **Burndown rate**: Rate at which tokens consume quota (Claude 3.7+ has 5x for output tokens)

### Optimizing Token Usage
- ✅ Set `max_tokens` close to expected output length (avoid over-provisioning)
- ✅ Use **prompt caching** to avoid reprocessing repeated context
- ✅ Use **CountTokens API** to estimate token usage before sending requests
- ✅ Monitor with **CloudWatch** metrics (InputTokenCount, OutputTokenCount)
- ✅ Write concise prompts — fewer input tokens = lower cost
- ✅ Use smaller models for simple tasks (fewer tokens needed, lower per-token cost)

## 7. Tokenization vs. Embeddings

| Aspect | Tokenization | Embeddings |
|--------|-------------|-----------|
| **What** | Splits text into tokens (discrete units) | Converts tokens into dense vectors (continuous numbers) |
| **Output** | Token IDs (integers) | Vectors (arrays of floats) |
| **Purpose** | Make text processable by model | Capture semantic meaning |
| **When** | First step in text processing | After tokenization |
| **Example** | `"cat"` → `[2134]` | `[2134]` → `[0.23, -0.45, 0.67, ...]` |

### Embeddings in AWS
- **Amazon Titan Text Embeddings**: Converts text to vector representations for search, similarity, clustering
- **Amazon Titan Multimodal Embeddings**: Converts text + images to vectors
- Used in **RAG** pipelines: query and documents are embedded, then compared by similarity

## 8. Tokenization for Different Data Types

### Text
- Standard subword tokenization (BPE, WordPiece, SentencePiece)
- Language-dependent: non-English may need more tokens

### Code
- Code tokenizers handle programming syntax, indentation, special characters
- Typically requires more tokens than natural language
- Models like CodeWhisperer/Amazon Q Developer use code-aware tokenizers

### Multimodal (Images + Text)
- Images are divided into **patches** (similar concept to tokens for text)
- Each patch is converted to a token-like representation
- Text and image tokens are processed together in multimodal models

## 9. Exam Focus Areas

### Common Question Types
- "What determines the cost of a Bedrock inference call?" → **Number of input and output tokens**
- "What is a context window?" → **Maximum tokens (input + output) a model can process per request**
- "How to reduce Bedrock costs?" → **Optimize prompts (fewer tokens), use prompt caching, set appropriate max_tokens**
- "What is a token?" → **A unit of text (word, subword, or character) that a model processes**
- "Why can't the model process my entire document?" → **Document exceeds context window token limit**

### Gotchas
- Tokens ≠ words (a word can be multiple tokens, especially rare/long words)
- Context window includes BOTH input AND output tokens (not just input)
- Output tokens are typically more expensive than input tokens
- Different models have different tokenizers — same text = different token counts across models
- `max_tokens` is deducted from quota at request start, then adjusted at completion
- Non-English text generally requires more tokens than English for the same content
- Prompt caching reduces cost by not reprocessing repeated context (CacheReadInputTokens don't count toward quota)
- You're billed for **actual** token usage, not the initial quota deduction

### Decision Tree
```
Working with text and LLMs?
│
├── Need to understand cost?
│   ├── Count tokens → Bedrock CountTokens API
│   ├── Monitor usage → CloudWatch (InputTokenCount, OutputTokenCount)
│   └── Optimize → Concise prompts, prompt caching, appropriate max_tokens
│
├── Text too long for context window?
│   ├── Summarize/chunk the text
│   ├── Use RAG (retrieve only relevant chunks)
│   └── Choose model with larger context window
│
├── Need semantic search/similarity?
│   └── Use Embeddings (Titan Text Embeddings) — tokenization + embedding
│
└── Need to convert text to vectors for RAG?
    └── Embedding model tokenizes internally → store in vector database
```

## 10. Best Practices
- **Estimate tokens before sending**: Use CountTokens API to avoid exceeding limits
- **Optimize prompts**: Remove unnecessary text, be concise — every token costs money
- **Set max_tokens wisely**: Too high wastes quota capacity, too low truncates responses
- **Use prompt caching**: For repeated system prompts or context across requests
- **Choose the right model**: Smaller models for simple tasks (lower cost per token)
- **Monitor token usage**: Use CloudWatch to track InputTokenCount and OutputTokenCount
- **Consider language**: Non-English content uses more tokens — factor into cost estimates
- **Chunk documents for RAG**: Break large documents into token-appropriate chunks for retrieval

## 11. Sample Questions

### Question 1
A company is building a chatbot using Amazon Bedrock and wants to understand their costs. The chatbot sends 1,000 tokens as input and receives 500 tokens as output per request. Which statement about pricing is correct?

A) The company is charged only for output tokens  
B) The company is charged for input and output tokens separately, with output tokens typically costing more  
C) The company is charged a flat rate per request regardless of token count  
D) The company is charged only for input tokens since the model generates output for free  

**Answer: B) The company is charged for input and output tokens separately, with output tokens typically costing more**  
Amazon Bedrock uses token-based pricing where input and output tokens are priced separately. Output tokens are typically more expensive because they require more computation (generation vs. processing). There is no flat rate — cost scales with token usage.

---

### Question 2
A developer is trying to send a 250,000-token document to a foundation model with a 200,000-token context window. The model returns an error. What is the most appropriate solution?

A) Increase the max_tokens parameter to 250,000  
B) Split the document into smaller chunks and use RAG to retrieve only relevant sections  
C) Convert the document to a different file format  
D) Increase the model's context window by fine-tuning  

**Answer: B) Split the document into smaller chunks and use RAG to retrieve only relevant sections**  
The document exceeds the model's context window limit. RAG allows you to chunk the document, store it in a knowledge base, and retrieve only the relevant sections for each query — keeping within the context window. max_tokens controls output length, not input capacity. Fine-tuning doesn't change context window size. File format doesn't affect token count.

---

### Question 3
Which statement about tokenization in large language models is correct?

A) One token always equals exactly one word in any language  
B) Modern LLMs use subword tokenization, where a single word may be split into multiple tokens  
C) Tokenization only applies to English text — other languages use character-level processing  
D) The number of tokens is always the same regardless of which model processes the text  

**Answer: B) Modern LLMs use subword tokenization, where a single word may be split into multiple tokens**  
Modern LLMs use subword tokenization (BPE, WordPiece, SentencePiece) that breaks text into meaningful subword units. A single word like "unhappiness" may become multiple tokens ("un" + "happi" + "ness"). Tokens ≠ words. Different models have different tokenizers, so the same text produces different token counts. Subword tokenization works across all languages.

---

**Exam Tip**: 🎯 For AIF-C01, focus on understanding that tokens are the billing unit for Bedrock (input + output priced separately), context windows limit total tokens per request, and subword tokenization is how modern LLMs process text. Know how to optimize costs through concise prompts, prompt caching, and appropriate max_tokens settings.
