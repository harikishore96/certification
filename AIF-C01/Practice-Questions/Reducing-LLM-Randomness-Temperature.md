# Practice Question: Reducing LLM Response Randomness with Temperature

**Domain**: Domain 2 — Fundamentals of Generative AI (24%)  
**Topic**: Inference Parameters, Temperature, LLM Configuration  
**Difficulty**: Easy

---

## Question

An ML specialist incorporated a large language model (LLM) in a proof of concept. The ML specialist wants to reduce the response randomness of the model.

Which change in inference parameter can reduce random responses?

- A. Increase the temperature.
- B. Decrease the temperature.
- C. Increase Top K.
- D. Increase Max Tokens.

---

## Answer: B. Decrease the temperature.

---

## Explanation

The key signal is **"reduce response randomness"** → temperature directly controls randomness in LLM outputs.

### How Temperature Works

| Temperature | Behavior | Use Case |
|-------------|----------|----------|
| **Low (e.g., 0.1)** | More deterministic, focused, predictable | Factual Q&A, code generation, classification |
| **Medium (e.g., 0.5)** | Balanced creativity and consistency | General conversation |
| **High (e.g., 1.0)** | More random, creative, diverse | Creative writing, brainstorming |

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. Increase temperature** | ❌ | Makes output *more* random — opposite of what's needed. |
| **B. Decrease temperature** | ✅ | Directly controls randomness. Lower = less random, more deterministic. |
| **C. Increase Top K** | ❌ | Expands the pool of candidate tokens, increasing diversity/randomness. |
| **D. Increase Max Tokens** | ❌ | Controls response *length*, not randomness. |

### Related Inference Parameters

| Parameter | What It Controls | Lower Value = |
|-----------|-----------------|---------------|
| **Temperature** | Overall randomness of token selection | Less random |
| **Top P (nucleus sampling)** | Cumulative probability threshold for token pool | Less random |
| **Top K** | Number of top tokens considered | Less random |
| **Max Tokens** | Maximum length of the response | Shorter response |
| **Stop Sequences** | When to stop generating | N/A |

---

## Exam Tip

> **Temperature** is the go-to answer for any question about controlling randomness/creativity.  
> **Low temperature = deterministic**. **High temperature = creative/random**.  
> If the question mentions reducing randomness via probability threshold, think **Top P**.
