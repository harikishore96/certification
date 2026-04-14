# Practice Question: Fine-Tuning FM on Bedrock for Summarization

**Domain**: Domain 3 — Applications of Foundation Models (28%)  
**Topic**: Model Customization, Fine-Tuning, Amazon Bedrock  
**Difficulty**: Easy

---

## Question

A company wants to improve the performance of a foundation model (FM) on Amazon Bedrock for frequent summarization tasks. The company has a labeled dataset with sample texts specific to the summarization tasks.

Which solution will meet these requirements?

- A. Use continued pre-training with the labeled dataset.
- B. Fine-tune the FM using the labeled dataset.
- C. Implement prompt engineering with few-shot examples.
- D. Set up RAG with a knowledge base containing the sample texts.

---

## Answer: B. Fine-tune the FM using the labeled dataset.

---

## Explanation

This is a textbook fine-tuning scenario. The key signals are:

- **"Improve performance"** on a specific task → customization needed
- **"Labeled dataset"** → supervised fine-tuning (input-output pairs)
- **"Specific to the summarization tasks"** → task-specific optimization
- **"Amazon Bedrock"** → use Bedrock's custom model training

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. Continued pre-training** | ❌ | Uses *unlabeled* domain data to teach the model new domain knowledge, not task-specific performance. |
| **B. Fine-tuning with labeled data** | ✅ | Labeled data + specific task improvement = fine-tuning. Bedrock supports this natively. |
| **C. Prompt engineering** | ❌ | Can help but won't fundamentally improve model performance for repeated tasks at scale. Doesn't use the labeled dataset. |
| **D. RAG** | ❌ | Retrieves external knowledge to augment responses. Useful for factual grounding, not for improving summarization quality on a specific style/format. |

### Decision Framework: When to Use What

| Approach | You Have | You Need |
|----------|----------|----------|
| **Fine-tuning** | Labeled dataset (input → output pairs) | Better performance on a specific task |
| **Continued pre-training** | Unlabeled domain-specific corpus | Model to understand domain terminology/context |
| **Prompt engineering** | No training data | Quick improvements without customization |
| **RAG** | Knowledge base / documents | Factual, up-to-date, grounded responses |

---

## Exam Tip

> **Key Signal**: "labeled dataset" + "improve performance on specific task" → **fine-tuning**.  
> "Unlabeled domain data" + "domain adaptation" → **continued pre-training**.  
> "No data, quick improvement" → **prompt engineering**.  
> "External knowledge, factual grounding" → **RAG**.
