# Practice Question: Continued Pre-Training for Domain Adaptation

**Domain**: Domain 3 — Applications of Foundation Models (28%)  
**Topic**: Model Customization, Continued Pre-Training, Amazon Bedrock  
**Difficulty**: Easy

---

## Question

A company wants to use Amazon Bedrock to build a generative AI application for product advertising. The company wants to customize a foundation model (FM) by using unlabeled data to make the model more specialized.

Which model customization technique will meet these requirements?

- A. Fine-tuning
- B. Continued pre-training
- C. Retrieval Augmented Generation (RAG)
- D. Prompt engineering

---

## Answer: B. Continued pre-training.

---

## Explanation

The key signals are:

- **"Unlabeled data"** → not fine-tuning (which requires labeled input-output pairs)
- **"More specialized"** → domain adaptation, not task-specific optimization
- **"Amazon Bedrock"** → supports both continued pre-training and fine-tuning

Continued pre-training feeds the FM large amounts of unlabeled, domain-specific text (e.g., product catalogs, advertising copy, brand guidelines) so it learns the terminology, style, and context of that domain.

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. Fine-tuning** | ❌ | Requires **labeled** data (input → expected output pairs) for task-specific improvement. |
| **B. Continued pre-training** | ✅ | Uses unlabeled domain data to adapt the model's knowledge to a specific domain. |
| **C. RAG** | ❌ | Retrieves external documents at inference time — doesn't customize the model itself. |
| **D. Prompt engineering** | ❌ | Guides model behavior through instructions — no model customization or training involved. |

### Quick Comparison: Continued Pre-training vs. Fine-tuning

| Aspect | Continued Pre-training | Fine-tuning |
|--------|----------------------|-------------|
| **Data type** | Unlabeled | Labeled (input-output pairs) |
| **Goal** | Domain adaptation | Task-specific performance |
| **Example data** | Product descriptions, industry docs | "Summarize this text" → "Expected summary" |
| **Result** | Model understands domain better | Model performs a specific task better |
| **Bedrock support** | ✅ Yes | ✅ Yes |

---

## Exam Tip

> The word **"unlabeled"** is the strongest signal pointing to **continued pre-training**.  
> If the question says **"labeled"**, it's **fine-tuning**.  
> This distinction appears frequently on the AIF-C01 exam.
