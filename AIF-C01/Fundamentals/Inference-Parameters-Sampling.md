# Inference Parameters: Temperature vs Top P vs Top K

## 1. Topic Overview
- **Purpose**: Control the randomness, diversity, and creativity of foundation model text generation outputs
- **Category**: Generative AI Inference Configuration
- **Key Use Cases**:
  - Tuning creative writing outputs (high randomness)
  - Ensuring deterministic factual responses (low randomness)
  - Balancing diversity vs. accuracy in chatbot responses
  - Controlling token selection during autoregressive generation
- **Exam Weight**: HIGH
- **Exam Domain**:
  - Domain 2: Fundamentals of Generative AI (24%)
  - Domain 3: Applications of Foundation Models (28%)
- **Task Statements**: Understanding how inference parameters influence FM output quality and behavior

## 2. Exam Keyword Mapping
- **Trigger Words**: "randomness", "creativity", "diversity", "deterministic", "sampling", "probability distribution", "token selection", "inference parameters", "model output control"
- **Scenario Indicators**:
  - "The model responses are too random/repetitive" → adjust Temperature
  - "Need more creative/diverse outputs" → increase Temperature, Top P, or Top K
  - "Need consistent, factual answers" → lower Temperature
  - "Limit the candidate token pool" → Top K or Top P
  - "Control the probability distribution shape" → Temperature
- **Anti-patterns**:
  - These are NOT training hyperparameters — they are inference-time parameters
  - These do NOT change the model weights
  - These are NOT the same as learning rate, batch size, or epochs

## 3. Core Concepts

### How Token Generation Works
For any given sequence, a foundation model determines a **probability distribution** of options for the next token. The model samples from this distribution to generate each token. Temperature, Top K, and Top P all control *how* the model samples from that distribution.

### The Three Parameters Explained

#### 🌡️ Temperature
- **What it does**: Modulates the **shape** of the probability distribution
- **Range**: Typically 0.0 – 1.0 (some models allow up to 2.0)
- **Mechanism**: Adjusts the softmax function output before sampling

| Setting | Effect | Use Case |
|---------|--------|----------|
| **Low (→ 0)** | Steepens distribution → model picks highest-probability tokens → **deterministic** | Factual Q&A, classification, code generation, summarization |
| **High (→ 1)** | Flattens distribution → lower-probability tokens become more likely → **creative/random** | Creative writing, brainstorming, poetry, storytelling |

#### 🔝 Top K (K most-likely tokens)
- **What it does**: Limits the candidate pool to the **K most probable** tokens
- **Range**: Integer (e.g., 1 – 500)
- **Mechanism**: Hard cutoff — only the top K tokens are considered, rest are discarded

| Setting | Effect | Use Case |
|---------|--------|----------|
| **Low K (e.g., 1–10)** | Very small pool → more focused, predictable output | Factual responses, constrained generation |
| **High K (e.g., 200–500)** | Large pool → more diverse, creative output | Open-ended generation, creative tasks |

#### 📊 Top P (Nucleus Sampling)
- **What it does**: Limits the candidate pool to tokens whose **cumulative probability** reaches P%
- **Range**: 0.0 – 1.0
- **Mechanism**: Dynamic cutoff — pool size varies based on the probability distribution

| Setting | Effect | Use Case |
|---------|--------|----------|
| **Low P (e.g., 0.1–0.3)** | Only highest-probability tokens → deterministic | Factual, precise outputs |
| **High P (e.g., 0.8–1.0)** | Most of the distribution included → diverse | Creative, exploratory outputs |

### 🐴 AWS Documentation Example

Given the prompt: `"I hear the hoof beats of"`

Model probability distribution:
```json
{
    "horses": 0.7,
    "zebras": 0.2,
    "unicorns": 0.1
}
```

| Parameter | Setting | Result |
|-----------|---------|--------|
| **High Temperature** | — | Distribution flattens → "unicorns" becomes more likely, "horses" less dominant |
| **Top K = 2** | K=2 | Only "horses" and "zebras" considered (top 2 candidates) |
| **Top P = 0.7** | P=0.7 | Only "horses" considered (it alone covers 70% of probability) |
| **Top P = 0.9** | P=0.9 | "horses" + "zebras" considered (together they cover 90%) |

## 4. Key Differences: Temperature vs Top K vs Top P

| Aspect | Temperature | Top K | Top P |
|--------|-------------|-------|-------|
| **Controls** | Shape of probability distribution | Fixed number of candidates | Cumulative probability threshold |
| **Cutoff Type** | No cutoff — reshapes all probabilities | Hard cutoff (fixed count) | Dynamic cutoff (probability-based) |
| **Pool Size** | All tokens remain (probabilities change) | Always exactly K tokens | Varies per prediction step |
| **Adaptiveness** | Static effect on distribution | Static pool size | **Adaptive** — pool shrinks/grows based on model confidence |
| **Technical Name** | Softmax temperature scaling | Top-K sampling | Nucleus sampling |
| **Best For** | Overall randomness control | Simple diversity control | Intelligent diversity control |

### Why Top P is Often Preferred Over Top K
- Top P is **adaptive**: when the model is confident (one token has high probability), the pool is small; when uncertain, the pool grows automatically
- Top K is **fixed**: always considers exactly K tokens regardless of the distribution shape, which can include irrelevant tokens or exclude relevant ones

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "A developer wants more deterministic responses from a foundation model" → Lower temperature
- **Comparison**: "What is the difference between Top K and Top P?" → Fixed count vs. cumulative probability
- **Best Practice**: "Which parameter should be adjusted first to reduce randomness?" → Temperature
- **Troubleshooting**: "Model outputs are too repetitive" → Increase temperature/Top P/Top K

### ⚠️ Gotchas
- Temperature = 0 makes output **nearly deterministic** (greedy decoding)
- Top K and Top P can be used **together** — they stack (intersection of both filters)
- These parameters are applied **at inference time**, not during training
- Different models support different subsets of these parameters
- Setting all three to their most restrictive values can make output extremely repetitive
- **Temperature reshapes probabilities; Top K/Top P filter the candidate pool** — they work at different stages

### Decision Tree
```
Need to control FM output randomness?
├── Want to reshape the entire probability distribution?
│   └── ✅ Use Temperature
├── Want to limit candidates to a fixed number?
│   └── ✅ Use Top K
├── Want to limit candidates based on cumulative probability?
│   └── ✅ Use Top P (Nucleus Sampling)
└── Want maximum control?
    └── ✅ Combine Temperature + Top P (most common combo)
```

### Recommended Settings by Use Case

| Use Case | Temperature | Top P | Top K |
|----------|-------------|-------|-------|
| Factual Q&A / Classification | 0.0 – 0.3 | 0.1 – 0.3 | 1 – 10 |
| Summarization | 0.0 – 0.3 | 0.5 – 0.7 | — |
| Code Generation | 0.0 – 0.2 | 0.1 – 0.3 | — |
| Conversational Chatbot | 0.3 – 0.7 | 0.7 – 0.9 | 40 – 100 |
| Creative Writing | 0.7 – 1.0 | 0.9 – 1.0 | 100 – 500 |
| Brainstorming | 0.8 – 1.0 | 0.95 – 1.0 | 200+ |

## 6. Best Practices

### AWS Well-Architected Guidance (Generative AI Lens)
Per [GENPERF02-BP02](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/genperf02-bp02.html):
- Optimize inference hyperparameters **per use case** to maintain consistent response quality
- Use a structured approach: test highest/lowest values, compare against ground truth data
- Consider the **LLM-as-a-judge** pattern to automate hyperparameter tuning at scale
- Document recommended hyperparameter ranges by task type in your organization's AI policy

### Tuning Strategy
1. **Start with Temperature** — it has the broadest impact on output behavior
2. **Add Top P** for finer control over candidate diversity
3. **Use Top K** when you need a hard limit on candidate count
4. **Test iteratively** against ground truth / golden data
5. **Don't set all parameters to extremes** simultaneously

### Amazon Bedrock Implementation
- Set via `inferenceConfig` in the Converse API or model-specific parameters in InvokeModel
- Parameters vary by model provider (Anthropic, Amazon Titan, Meta, Cohere, etc.)
- Use Amazon Bedrock **Model Evaluation** to compare parameter configurations

## 7. Other Inference Parameters (Related)

| Parameter | Purpose |
|-----------|---------|
| **Max Tokens** | Maximum number of tokens in the response |
| **Stop Sequences** | Character sequences that stop generation |
| **Penalties** (frequency, presence, repetition) | Penalize repeated tokens to increase diversity |
| **Response Length** | Min/max token count constraints |

## 8. Sample Questions

### Question 1
**A company is using Amazon Bedrock to build a customer support chatbot. The chatbot sometimes provides creative but inaccurate answers. Which inference parameter should the developer adjust FIRST to make responses more factual and consistent?**

A) Increase Top K to 500
B) Decrease the temperature to a lower value
C) Increase Top P to 1.0
D) Increase the maximum token length

**Answer: B** — Lowering the temperature steepens the probability distribution, making the model favor high-probability (more factual) tokens. This is the most direct way to reduce randomness. Options A and C would increase randomness. Option D controls length, not accuracy.

---

### Question 2
**A data scientist is configuring inference parameters for a foundation model. They want the model to dynamically adjust the number of candidate tokens based on the model's confidence level for each prediction. Which parameter should they use?**

A) Temperature
B) Top K
C) Top P
D) Max Tokens

**Answer: C** — Top P (nucleus sampling) uses cumulative probability to determine the candidate pool, which means the pool size **dynamically adjusts** based on the distribution. When the model is confident, fewer tokens are considered; when uncertain, more tokens are included. Top K uses a fixed count regardless of confidence.

---

### Question 3
**An ML engineer sets Temperature = 0.1, Top K = 5, and Top P = 0.3 for a text generation model. What is the expected behavior of the model's output?**

A) Highly creative and diverse responses
B) Highly deterministic and focused responses
C) Random responses with many rare tokens
D) Responses that ignore the prompt context

**Answer: B** — All three parameters are set to restrictive values: low temperature (steep distribution), low Top K (only 5 candidates), and low Top P (only top 30% of probability mass). Together, these produce highly deterministic, focused output.

---

**Exam Tip**: Remember the mental model — **Temperature reshapes the distribution, Top K/Top P filter the candidates**. They operate at different stages of the sampling process and can be combined. For the AIF-C01 exam, know that lowering any of these parameters makes output more deterministic, and raising them makes output more creative/random.

---

## Citations
- [Influence response generation with inference parameters - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/inference-parameters.html)
- [GENPERF02-BP02 Optimize inference parameters - Generative AI Lens](https://docs.aws.amazon.com/wellarchitected/latest/generative-ai-lens/genperf02-bp02.html)
- [Inference request parameters for foundation models - Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-parameters.html)
