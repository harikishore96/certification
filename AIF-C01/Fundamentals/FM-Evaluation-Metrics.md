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

### Amazon Bedrock Automated Model Evaluation — Accuracy, Robustness & Toxicity

Amazon Bedrock provides **three evaluation dimensions** for automated model evaluation jobs: **Accuracy**, **Robustness**, and **Toxicity**. Each dimension uses different computed metrics depending on the task type.

> **Exam Tip**: The exam may ask which evaluation dimensions are available in Bedrock automated evaluation. The answer is always **Accuracy + Robustness + Toxicity** (not all task types support all three — text classification does NOT have toxicity).

#### Evaluation Types in Bedrock

| Type | How It Works | When to Use |
|------|-------------|-------------|
| **Automatic** | Uses built-in algorithms & benchmark datasets | Quick comparison, no human reviewers needed |
| **Human-based** | Human reviewers score model outputs | Subjective quality, nuanced evaluation |
| **LLM-as-a-Judge** | Another FM evaluates the model's outputs | Scalable alternative to human evaluation |

#### Accuracy Metrics by Task Type

| Task Type | Computed Metric | Built-in Dataset(s) | What It Measures | Good Score |
|-----------|----------------|---------------------|------------------|------------|
| **General Text Generation** | Real World Knowledge (RWK) Score | TREX | Model's ability to encode factual knowledge about the real world | **High** = accurate |
| **Text Summarization** | BERTScore | Gigaword | Semantic similarity between generated and reference summaries using BERT embeddings | **High** = accurate |
| **Question & Answer** | F1 Score (NLP-F1) | BoolQ, NaturalQuestions, TriviaQA | Balance of precision (correct predictions / all predictions) and recall (correct predictions / relevant predictions). Range: 0–1 | **High** = accurate |
| **Text Classification** | Classification Accuracy Score | Women's Ecommerce Clothing Reviews | Compares predicted class to ground truth label | **High** = accurate |

#### Robustness Metrics by Task Type

Semantic robustness measures **how much the model output changes when the input is slightly perturbed** in meaning-preserving ways.

**Perturbation Types Applied** (all task types):
- Convert text to all lowercase
- Keyboard typos
- Converting numbers to words
- Random changes to uppercase
- Random addition/deletion of whitespaces
- Each prompt is perturbed **~5 times**, then each perturbed prompt is sent for inference

| Task Type | Computed Metric | Built-in Dataset(s) | Calculation | Good Score |
|-----------|----------------|---------------------|-------------|------------|
| **General Text Generation** | Word Error Rate (WER) | BOLD, WikiText2, TREX | Measures word-level differences between original and perturbed outputs | **Low** = robust |
| **Text Summarization** | (Delta BERTScore / BERTScore) × 100 | Gigaword | Percentage change in BERTScore between perturbed and original prompts | **Low** = robust |
| **Question & Answer** | (Delta F1 / F1) × 100 | BoolQ, NaturalQuestions, TriviaQA | Percentage change in F1 score between perturbed and original prompts | **Low** = robust |
| **Text Classification** | (Delta Accuracy / Accuracy) × 100 | Women's Ecommerce Clothing Reviews | Percentage change in classification accuracy between perturbed and original prompts | **Low** = robust |

> ⚠️ **Key Insight**: For robustness, **LOW scores = GOOD** (model is stable). For accuracy, **HIGH scores = GOOD**.

#### Toxicity Metrics by Task Type

Toxicity is calculated using the **detoxify algorithm** (open-source, by Unitary AI). It measures whether the model generates racist, sexist, or otherwise harmful/offensive content.

| Task Type | Built-in Dataset(s) | Good Score |
|-----------|---------------------|------------|
| **General Text Generation** | RealToxicityPrompts (100K prompts), BOLD (23,679 prompts) | **Low** = safe |
| **Text Summarization** | Gigaword | **Low** = safe |
| **Question & Answer** | BoolQ, NaturalQuestions, TriviaQA | **Low** = safe |
| **Text Classification** | ❌ **Not available** | N/A |

> ⚠️ **Gotcha**: Text classification does NOT support toxicity evaluation in Bedrock automated evaluation.

#### Built-in Datasets Quick Reference

| Dataset | Purpose | Size | Used For |
|---------|---------|------|----------|
| **TREX** | Knowledge Base Triples from Wikipedia (subject-predicate-object) | Large | Accuracy (RWK), Robustness (text gen) |
| **BOLD** | Bias in Open-ended Language Generation — covers profession, gender, race, religion, politics | 23,679 prompts | Robustness, Toxicity (text gen) |
| **RealToxicityPrompts** | Prompts designed to elicit toxic language | 100,000 prompts | Toxicity (text gen) |
| **WikiText2** | General text generation prompts from HuggingFace | Medium | Robustness (text gen) |
| **Gigaword** | News headline summarization | Large | Accuracy, Robustness, Toxicity (summarization) |
| **BoolQ** | Boolean (yes/no) questions | Medium | Accuracy, Robustness, Toxicity (Q&A) |
| **NaturalQuestions** | Real Google search questions with Wikipedia answers | Large | Accuracy, Robustness, Toxicity (Q&A) |
| **TriviaQA** | Trivia questions with evidence documents | Large | Accuracy, Robustness, Toxicity (Q&A) |
| **Women's Ecommerce Clothing Reviews** | Product review classification | Medium | Accuracy, Robustness (classification) |

#### LLM-as-a-Judge Built-in Metrics (Human-like Evaluation)

When using an evaluator LLM instead of automatic algorithms:

| Metric | What It Measures |
|--------|------------------|
| **Correctness** | Is the response correct? (considers ground truth if provided) |
| **Completeness** | Does the response fully answer the prompt? |
| **Faithfulness** | Does the response stay faithful to the provided context (no hallucination)? |
| **Helpfulness** | Is the response useful, coherent, and anticipates user needs? |
| **Logical Coherence** | Are there logical gaps, inconsistencies, or contradictions? |
| **Relevance** | Is the response relevant to the prompt? |
| **Following Instructions** | Does the response respect exact directions in the prompt? |
| **Professional Style & Tone** | Is the style appropriate for professional settings? |
| **Harmfulness** | Does the response contain harmful content? |
| **Stereotyping** | Does the response contain stereotypes (positive or negative)? |
| **Refusal** | Does the response decline or reject the request? |

> You can also define **custom metrics** with your own evaluation prompts.

#### Summary: Score Interpretation Cheat Sheet

| Dimension | Good Score | Bad Score | Algorithm/Method |
|-----------|-----------|-----------|------------------|
| **Accuracy** | HIGH ↑ | LOW | RWK, BERTScore, F1, Classification Accuracy |
| **Robustness** | LOW ↓ | HIGH | Word Error Rate, Delta metrics with perturbation |
| **Toxicity** | LOW ↓ | HIGH | Detoxify algorithm |

---

## 4. Key Features & Components

### AWS Evaluation Tools

#### Amazon Bedrock Model Evaluation
- Built-in automatic evaluation for FM comparison
- Supports human evaluation workflows and LLM-as-a-Judge
- Evaluates across **accuracy, toxicity, and robustness** dimensions
- Can use built-in benchmark datasets or custom datasets (JSON Lines in S3)
- One task type per evaluation job
- Compare up to 2 models side-by-side

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
| **Classification** | Accuracy, Precision, Recall, F1 | Semantic Robustness (no toxicity) |
| **Open-ended Generation** | Factual Knowledge (RWK) | Toxicity, Prompt Stereotyping, Semantic Robustness |

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

### Question 4
**A company runs an automated model evaluation job in Amazon Bedrock for a general text generation task. The robustness score is HIGH. What does this indicate?**

A) The model is very robust and handles input variations well  
B) The model output changes significantly with minor input perturbations  
C) The model generates highly toxic content  
D) The model has high factual accuracy  

**Correct Answer: B) The model output changes significantly with minor input perturbations**

- ✅ **B)** — In Bedrock automated evaluation, robustness is measured by perturbing inputs (typos, case changes, whitespace). A HIGH robustness score means large output changes = the model is NOT robust
- ❌ A) — This is the opposite. LOW robustness score = model is robust
- ❌ C) — Toxicity is a separate dimension measured by the detoxify algorithm
- ❌ D) — Accuracy is a separate dimension (RWK score for text generation)

---

### Question 5
**Which evaluation dimension is NOT available for text classification tasks in Amazon Bedrock automated model evaluation?**

A) Accuracy  
B) Robustness  
C) Toxicity  
D) All three are available  

**Correct Answer: C) Toxicity**

- ✅ **C) Toxicity** — Text classification in Bedrock automated evaluation only supports Accuracy and Robustness, NOT Toxicity
- ❌ A) — Accuracy IS available (classification_accuracy_score)
- ❌ B) — Robustness IS available (delta_classification_accuracy_score)
- ❌ D) — Incorrect, toxicity is not available for classification

---

**Exam Tip**: When you see "summarization" in a question → think **ROUGE**. When you see "translation" → think **BLEU**. When you see "semantic meaning" or "paraphrasing" → think **BERTScore**. Remember: for Bedrock automated evaluation, **accuracy = HIGH is good**, **robustness = LOW is good**, **toxicity = LOW is good**. The AIF-C01 exam specifically lists ROUGE, BLEU, and BERTScore as relevant metrics in Task 3.4.

---

## Citations
- [Model evaluation task types in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation-tasks.html)
- [Review metrics for automated model evaluation in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation-report-programmatic.html)
- [General text generation for model evaluation in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation-tasks-general-text.html)
- [Use metrics to understand model performance (LLM-as-a-Judge)](https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation-metrics.html)
