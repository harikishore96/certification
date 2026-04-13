# Foundation Model (FM) Evaluation Metrics

## 1. Topic Overview
- **Purpose**: Metrics and methods used to assess the performance, quality, and safety of foundation model outputs across different tasks (summarization, translation, Q&A, classification)
- **Service Level**: N/A (Conceptual topic; implemented via Amazon Bedrock Model Evaluation & SageMaker FMEval)
- **Key Use Cases**:
  - Evaluating text summarization quality (ROUGE)
  - Assessing machine translation accuracy (BLEU)
  - Measuring semantic similarity of generated text (BERTScore)
  - Comparing foundation models for selection decisions
  - Monitoring model quality over time
- **Exam Weight**: HIGH
- **Exam Domain**: Domain 3: Applications of Foundation Models (28%)
- **Task Statements**: Task 3.4 — Describe methods to evaluate FM performance

---

## 2. Exam Keyword Mapping

### Trigger Words
- "evaluate foundation model", "model performance", "summarization quality"
- "ROUGE", "BLEU", "BERTScore", "METEOR"
- "model comparison", "benchmark", "accuracy metric"
- "FMEval", "model evaluation job"

### Scenario Indicators
- "Which metric to assess summarization?" → **ROUGE**
- "Which metric for translation quality?" → **BLEU**
- "Which metric captures semantic meaning?" → **BERTScore**
- "How to evaluate FM on your own data?" → **Amazon Bedrock Model Evaluation** or **SageMaker FMEval**
- "Human review of model outputs?" → **Human evaluation**

### Anti-patterns
- Don't confuse with classification metrics (Precision, Recall, F1) — those are for traditional ML classification tasks
- Don't confuse ROUGE (summarization/recall-focused) with BLEU (translation/precision-focused)
- Don't use accuracy/F1 for open-ended text generation tasks

---

## 3. Core Concepts

### Text Summarization Metrics

#### ROUGE (Recall-Oriented Understudy for Gisting Evaluation)
The **primary metric for text summarization**. Measures overlap between generated summary and reference summary.

| Variant | What It Measures | Example |
|---------|-----------------|---------|
| **ROUGE-1** | Unigram (single word) overlap | "the cat sat" vs "the cat" → overlap of "the", "cat" |
| **ROUGE-2** | Bigram (two-word) overlap | Captures phrase-level similarity |
| **ROUGE-L** | Longest Common Subsequence (LCS) | Captures sentence-level structure |
| **ROUGE-S** | Skip-bigram co-occurrence | Allows gaps between matched words |

- **Focus**: Recall — how much of the reference summary is captured in the generated summary
- **Score Range**: 0 to 1 (higher = better)
- **Formula**: ROUGE-N = (Count of matching n-grams) / (Count of n-grams in reference)

#### METEOR (Metric for Evaluation of Translation with Explicit ORdering)
- Considers synonyms, stemming, and paraphrasing
- Better correlation with human judgment than BLEU alone
- Used in both summarization and translation evaluation
- Balances precision and recall with a harmonic mean

### Translation Metrics

#### BLEU (Bilingual Evaluation Understudy)
- **Primary metric for machine translation**
- Measures **precision** of n-gram overlap (how much of the generated text appears in the reference)
- Score Range: 0 to 1 (higher = better)
- Includes a brevity penalty to discourage overly short translations
- **Key difference from ROUGE**: BLEU = precision-focused; ROUGE = recall-focused

### Semantic Metrics

#### BERTScore
- Uses **contextual embeddings** from pre-trained BERT models
- Measures **semantic similarity** rather than exact word overlap
- Captures meaning even when different words are used (paraphrasing)
- Better at evaluating fluency and meaning preservation
- Score Range: 0 to 1 (higher = better)

#### Cosine Similarity
- Measures angle between two embedding vectors
- Used for comparing semantic similarity of text passages
- Often used in RAG evaluation to measure retrieval relevance

### Classification Metrics (for FM classification tasks)
| Metric | Formula | Use Case |
|--------|---------|----------|
| **Accuracy** | (TP + TN) / Total | Overall correctness |
| **Precision** | TP / (TP + FP) | When false positives are costly |
| **Recall** | TP / (TP + FN) | When false negatives are costly |
| **F1 Score** | 2 × (P × R) / (P + R) | Balance of precision and recall |

### Question Answering Metrics
- **Exact Match (EM)**: Binary — does the answer exactly match the reference?
- **Quasi-Exact Match**: Allows minor variations (whitespace, articles)
- **F1 over Words**: Token-level F1 between predicted and reference answer

### Safety & Responsibility Metrics
- **Toxicity Score**: Measures harmful/offensive content using toxicity detector models
- **Semantic Robustness**: How much output changes with small, meaning-preserving input changes
- **Prompt Stereotyping**: Measures bias in model responses
- **Factual Knowledge**: Evaluates model's encoding of factual information

---

## 4. Key Features & Components

### AWS Evaluation Tools

#### Amazon Bedrock Model Evaluation
- Built-in automatic evaluation for FM comparison
- Supports human evaluation workflows
- Evaluates across accuracy, toxicity, and robustness dimensions
- Can use built-in benchmark datasets or custom datasets

#### SageMaker FMEval
- Foundation Model Evaluations in SageMaker Studio
- Supports: open-ended generation, text summarization, Q&A, classification
- Compare one model, two models, or different versions of the same model
- Automatic + human evaluation support

### Evaluation Dimensions by Task Type

| Task | Accuracy Metrics | Safety Metrics |
|------|-----------------|----------------|
| **Text Summarization** | ROUGE-N, METEOR, BERTScore | Toxicity, Semantic Robustness |
| **Translation** | BLEU, METEOR | Toxicity |
| **Q&A** | Exact Match, F1 over Words | Toxicity, Semantic Robustness |
| **Classification** | Accuracy, Precision, Recall, F1 | Semantic Robustness |
| **Open-ended Generation** | Factual Knowledge | Toxicity, Prompt Stereotyping, Semantic Robustness |

### LLM-as-a-Judge
- Uses another LLM to evaluate model outputs when reference data is unavailable
- Evaluates on dimensions: correctness, completeness, readability, coherence, factuality
- Useful for subjective quality assessment
- Available in Amazon Bedrock Model Evaluation

---

## 5. Exam Focus Areas

### Common Question Types
- **"Which metric for summarization?"** → ROUGE
- **"Which metric for translation?"** → BLEU
- **"Which metric captures semantic meaning?"** → BERTScore
- **"How to evaluate FM on custom data?"** → Bedrock Model Evaluation / SageMaker FMEval
- **"Which metric is recall-focused?"** → ROUGE
- **"Which metric is precision-focused?"** → BLEU

### Gotchas
- ROUGE measures **recall** (reference coverage), not precision
- BLEU measures **precision** (generated text accuracy), not recall
- BERTScore is the only metric that captures **semantic similarity** without requiring exact word matches
- METEOR is used for both summarization AND translation
- Classification metrics (Precision/Recall/F1) are NOT used for open-ended text generation
- Human evaluation is still considered the gold standard for subjective quality

### Comparison Points

| Metric | Task | Focus | Captures Meaning? |
|--------|------|-------|-------------------|
| **ROUGE** | Summarization | Recall | No (word overlap only) |
| **BLEU** | Translation | Precision | No (word overlap only) |
| **BERTScore** | Any text generation | Semantic similarity | ✅ Yes |
| **METEOR** | Summarization/Translation | Balanced (P+R) | Partially (synonyms) |
| **Exact Match** | Q&A | Binary correctness | No |

### Decision Tree
```
Need to evaluate FM output?
├── Text Summarization → ROUGE (primary), METEOR, BERTScore
├── Machine Translation → BLEU (primary), METEOR
├── Question Answering → Exact Match, F1 over Words
├── Classification → Accuracy, Precision, Recall, F1
├── Open-ended Generation → Human Evaluation, Toxicity, Factual Knowledge
└── Semantic Similarity → BERTScore, Cosine Similarity
```

---

## 6. Best Practices

### Evaluation Strategy
- **Combine multiple metrics** — no single metric captures all quality dimensions
- **Use human evaluation** alongside automatic metrics for subjective tasks
- **Evaluate on domain-specific data** — generic benchmarks may not reflect your use case
- **Monitor over time** — model performance can degrade with data drift

### Security
- Ensure evaluation datasets don't contain PII or sensitive data
- Use IAM roles to control access to evaluation jobs
- Encrypt evaluation data at rest and in transit

### Cost Optimization
- Use automatic evaluation first (cheaper) before human evaluation
- Leverage built-in benchmark datasets to avoid data preparation costs
- Use SageMaker FMEval for batch evaluation instead of real-time inference

---

## 7. Hands-On Labs

### Quick Setup: Bedrock Model Evaluation
1. Open Amazon Bedrock Console → Model Evaluation
2. Select models to evaluate
3. Choose task type (e.g., Text Summarization)
4. Select evaluation dimensions (Accuracy, Toxicity, Robustness)
5. Choose built-in dataset or upload custom dataset
6. Run evaluation and compare results

### CLI: Run FMEval
```bash
# Install fmeval library
pip install fmeval

# Example: Evaluate summarization with ROUGE
from fmeval.eval_algorithms.summarization_accuracy import SummarizationAccuracy
eval_algo = SummarizationAccuracy()
eval_output = eval_algo.evaluate(
    model=model_runner,
    dataset_config=dataset_config,
    prompt_template="Summarize the following text: $feature",
    save=True
)
```

---

## 8. Sample Questions

### Question 1
**A company wants to evaluate the quality of text summaries generated by a foundation model. Which metric should they use?**

A) BLEU  
B) F1 Score  
C) ROUGE  
D) AUC-ROC  

**Correct Answer: C) ROUGE**

- ✅ **C) ROUGE** — Specifically designed for evaluating text summarization by measuring recall-based n-gram overlap between generated and reference summaries
- ❌ A) BLEU — Primarily used for machine translation evaluation, focuses on precision
- ❌ B) F1 Score — Used for classification tasks, not text generation
- ❌ D) AUC-ROC — Used for binary classification model evaluation

---

### Question 2
**A data scientist needs a metric that can evaluate the semantic meaning of generated text, even when different words are used compared to the reference. Which metric should they choose?**

A) ROUGE-1  
B) BLEU  
C) Exact Match  
D) BERTScore  

**Correct Answer: D) BERTScore**

- ✅ **D) BERTScore** — Uses contextual embeddings to measure semantic similarity, capturing meaning even with different wording (paraphrasing)
- ❌ A) ROUGE-1 — Only measures unigram word overlap, doesn't capture semantic meaning
- ❌ B) BLEU — Measures n-gram precision overlap, doesn't capture semantic meaning
- ❌ C) Exact Match — Binary metric requiring identical text, no semantic understanding

---

### Question 3
**A team is using Amazon Bedrock to compare two foundation models for a text summarization use case. They want to evaluate accuracy, toxicity, and robustness. Which AWS feature should they use?**

A) SageMaker Clarify  
B) Amazon Bedrock Model Evaluation  
C) Amazon Comprehend  
D) AWS Trusted Advisor  

**Correct Answer: B) Amazon Bedrock Model Evaluation**

- ✅ **B) Amazon Bedrock Model Evaluation** — Provides built-in automatic evaluation for FM comparison across accuracy (ROUGE, BERTScore), toxicity, and semantic robustness dimensions
- ❌ A) SageMaker Clarify — Focuses on bias detection and explainability for traditional ML models
- ❌ C) Amazon Comprehend — NLP service for text analysis (sentiment, entities), not FM evaluation
- ❌ D) AWS Trusted Advisor — Provides infrastructure optimization recommendations, not ML evaluation

---

**Exam Tip**: When you see "summarization" in a question → think **ROUGE**. When you see "translation" → think **BLEU**. When you see "semantic meaning" or "paraphrasing" → think **BERTScore**. The AIF-C01 exam specifically lists ROUGE, BLEU, and BERTScore as relevant metrics in Task 3.4.
