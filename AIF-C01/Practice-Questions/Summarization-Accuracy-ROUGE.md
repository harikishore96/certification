# Practice Question: Evaluating Summarization Accuracy with ROUGE

**Domain**: Domain 3 — Applications of Foundation Models (28%)  
**Topic**: FM Evaluation Metrics, ROUGE, Summarization  
**Difficulty**: Easy

---

## Question

A company wants to use Amazon Bedrock to summarize customer documents that are in the company's database.

Which metric will evaluate the accuracy of the summaries?

- A. BLEU
- B. ROUGE
- C. Perplexity
- D. AUC-ROC

---

## Answer: B. ROUGE

---

## Explanation

ROUGE (Recall-Oriented Understudy for Gisting Evaluation) is the standard metric for evaluating text summarization. It measures the overlap between the generated summary and a reference (human-written) summary.

### How ROUGE Works

| ROUGE Variant | What It Measures |
|---------------|-----------------|
| **ROUGE-1** | Overlap of individual words (unigrams) |
| **ROUGE-2** | Overlap of word pairs (bigrams) |
| **ROUGE-L** | Longest common subsequence between generated and reference text |

Higher ROUGE score = generated summary is closer to the reference summary.

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. BLEU** | ❌ | Designed for **machine translation**, not summarization. Measures precision of n-gram overlap. |
| **B. ROUGE** | ✅ | Purpose-built for summarization evaluation. Measures overlap between generated and reference summaries. |
| **C. Perplexity** | ❌ | Measures how well a model predicts the next token — evaluates language fluency, not summary accuracy. |
| **D. AUC-ROC** | ❌ | Classification metric — measures true positive vs. false positive rates. Not applicable to text generation. |

### Summarization vs. Translation Metrics

| Task | Go-To Metric | Focus |
|------|-------------|-------|
| **Summarization** | **ROUGE** | Recall — how much of the reference is captured |
| **Translation** | **BLEU** | Precision — how much of the generated text matches the reference |
| **General text quality** | **BERTScore** | Semantic similarity using embeddings |

---

## Exam Tip

> **Summarization → ROUGE**. **Translation → BLEU**.  
> This is one of the most frequently tested metric associations on the AIF-C01 exam.  
> ROUGE focuses on **recall** (did the summary capture the key content?).  
> BLEU focuses on **precision** (is the translation accurate word-by-word?).
