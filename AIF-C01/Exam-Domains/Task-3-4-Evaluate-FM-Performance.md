# Task Statement 3.4: Describe Methods to Evaluate FM Performance

## 1. Topic Overview
- **Purpose**: Understand the approaches, metrics, and AWS services used to evaluate foundation model (FM) performance across different tasks and business objectives
- **Service Level**: Regional (Amazon Bedrock Model Evaluation)
- **Key Use Cases**:
  - Comparing multiple FMs to select the best one for a use case
  - Measuring text summarization, translation, Q&A, and classification quality
  - Evaluating safety (toxicity, bias, stereotyping) of FM outputs
  - Assessing RAG pipeline effectiveness (retrieval + generation)
  - Validating that FM applications meet business KPIs
- **Exam Weight**: HIGH
- **Exam Domain**: Domain 3: Applications of Foundation Models (28%)
- **Task Statements**:
  - Determine approaches to evaluate FM performance (human evaluation, benchmark datasets, Amazon Bedrock Model Evaluation)
  - Identify relevant metrics to assess FM performance (ROUGE, BLEU, BERTScore)
  - Determine whether an FM effectively meets business objectives (productivity, user engagement, task engineering)
  - Identify approaches to evaluate the performance of applications built with FMs (RAG, agents, workflows)

---

## 2. Exam Keyword Mapping

### Trigger Words
- "evaluate foundation model", "model performance", "compare models"
- "ROUGE", "BLEU", "BERTScore", "perplexity"
- "benchmark dataset", "human evaluation", "LLM-as-a-judge"
- "model evaluation job", "FMEval", "Bedrock evaluation"
- "accuracy", "robustness", "toxicity" (in FM context)

### Scenario Indicators
- "Which metric for summarization quality?" → **ROUGE**
- "Which metric for translation quality?" → **BLEU**
- "Which metric captures semantic meaning/paraphrasing?" → **BERTScore**
- "Need subjective quality assessment at scale?" → **LLM-as-a-Judge**
- "Need domain expert review of outputs?" → **Human evaluation**
- "Compare two FMs on Bedrock?" → **Amazon Bedrock Model Evaluation**
- "Evaluate RAG retrieval quality?" → **Bedrock RAG Evaluation**
- "Measure if model is robust to typos?" → **Semantic Robustness**
- "Check for harmful content in outputs?" → **Toxicity evaluation**

### Anti-patterns
- Don't confuse FM evaluation metrics (ROUGE, BLEU) with traditional ML classification metrics (AUC-ROC, confusion matrix) — different contexts
- Don't use BLEU for summarization (use ROUGE) or ROUGE for translation (use BLEU)
- Don't assume human evaluation is always best — it's expensive and slow; use automatic/LLM-as-a-judge first
- Don't confuse Bedrock Model Evaluation (FM evaluation) with SageMaker Model Monitor (production drift detection)

---

## 3. Core Concepts

### Three Approaches to Evaluate FM Performance

```
FM Evaluation Approaches
├── 1. Automatic (Programmatic) Evaluation
│   ├── Built-in benchmark datasets
│   ├── Custom prompt datasets
│   └── Computed metrics (ROUGE, BERTScore, F1, etc.)
├── 2. Human Evaluation
│   ├── Work teams (employees, SMEs)
│   ├── Rating methods (Likert scale, thumbs up/down, ordinal rank)
│   └── Subjective quality assessment
└── 3. LLM-as-a-Judge
    ├── Evaluator model scores generator model
    ├── Built-in metrics (correctness, faithfulness, etc.)
    └── Custom metrics (your own evaluation prompts)
```

### Approach 1: Automatic (Programmatic) Evaluation

Uses algorithms and benchmark datasets to compute quantitative scores. Fast, scalable, reproducible.

**How it works**:
1. Select model(s) to evaluate
2. Choose task type (text generation, summarization, Q&A, classification)
3. Select evaluation dimensions (accuracy, robustness, toxicity)
4. Use built-in or custom prompt dataset
5. System computes metrics automatically

**Evaluation Dimensions**:

| Dimension | What It Measures | Good Score | Algorithm |
|-----------|-----------------|------------|-----------|
| **Accuracy** | Quality of model output vs. reference | HIGH ↑ | ROUGE, BERTScore, F1, RWK |
| **Robustness** | Output stability under input perturbations | LOW ↓ | Word Error Rate, Delta metrics |
| **Toxicity** | Harmful/offensive content in outputs | LOW ↓ | Detoxify algorithm |

> ⚠️ **Key Insight**: Accuracy = HIGH is good. Robustness = LOW is good. Toxicity = LOW is good.

**Perturbation Types for Robustness Testing**:
- Keyboard typos
- Random uppercase changes
- Whitespace additions/deletions
- Number-to-word conversions
- Each prompt perturbed ~5 times

### Approach 2: Human Evaluation

Uses human reviewers (employees, subject-matter experts) to rate model outputs. Gold standard for subjective quality.

**Rating Methods in Amazon Bedrock**:

| Rating Method | Description | Use Case |
|--------------|-------------|----------|
| **Likert Scale (Comparison)** | 5-point scale comparing two model outputs | Side-by-side model comparison |
| **Likert Scale (Single)** | 5-point scale rating one model's response | Individual model quality assessment |
| **Choice Buttons (Radio)** | Select preferred response between two models | Binary preference |
| **Ordinal Rank** | Rank responses in order of preference | Ranking multiple outputs |
| **Thumbs Up/Down** | Accept or reject a response | Quick pass/fail assessment |

**When to Use Human Evaluation**:
- Subjective quality (creativity, tone, style)
- Domain-specific accuracy requiring expertise
- Nuanced judgment that metrics can't capture
- Validating automatic evaluation results
- Regulatory/compliance requirements

**Limitations**:
- Expensive (requires human labor)
- Slow (days to weeks)
- Not scalable for large datasets
- Subject to inter-annotator disagreement
- Difficult to reproduce exactly

### Approach 3: LLM-as-a-Judge

Uses a second LLM (evaluator model) to score the first LLM's (generator model) responses. Scalable alternative to human evaluation.

**How it works**:
1. Generator model produces responses to prompts
2. Evaluator model scores each response using built-in or custom metrics
3. Scores and explanations are generated for each prompt-response pair
4. Results available in Bedrock console and S3

**Built-in Metrics (LLM-as-a-Judge)**:

| Metric | What It Measures |
|--------|-----------------|
| **Correctness** | Is the response factually correct? (considers ground truth if provided) |
| **Completeness** | Does the response fully answer every part of the prompt? |
| **Faithfulness** | Does the response stay faithful to context? (no hallucination) |
| **Helpfulness** | Is the response useful, coherent, and anticipates user needs? |
| **Logical Coherence** | Are there logical gaps, inconsistencies, or contradictions? |
| **Relevance** | Is the response relevant to the prompt? |
| **Following Instructions** | Does the response respect exact directions in the prompt? |
| **Professional Style & Tone** | Is the style appropriate for professional settings? |
| **Harmfulness** | Does the response contain harmful content? |
| **Stereotyping** | Does the response contain stereotypes (positive or negative)? |
| **Refusal** | Does the response decline or reject the request? |

**Supported Evaluator Models**: Amazon Nova Pro, Claude 3.5 Sonnet, Claude 3.7 Sonnet, Claude 3 Haiku, Meta Llama 3.1 70B, Mistral Large

> You can also define **custom metrics** with your own evaluation prompt templates.

---

## 4. Key Metrics to Assess FM Performance

### Text Summarization Metrics

#### ROUGE (Recall-Oriented Understudy for Gisting Evaluation)
- **Primary metric for text summarization**
- Measures **recall** — how much of the reference summary is captured in the generated summary
- Score Range: 0 to 1 (higher = better)

| Variant | What It Measures |
|---------|-----------------|
| **ROUGE-1** | Unigram (single word) overlap |
| **ROUGE-2** | Bigram (two-word phrase) overlap |
| **ROUGE-L** | Longest Common Subsequence (sentence structure) |

**Formula**: ROUGE-N = (Count of matching n-grams) / (Count of n-grams in reference)

#### METEOR (Metric for Evaluation of Translation with Explicit ORdering)
- Considers synonyms, stemming, and paraphrasing
- Better correlation with human judgment than BLEU alone
- Balances precision and recall with harmonic mean
- Used for both summarization AND translation

### Machine Translation Metrics

#### BLEU (Bilingual Evaluation Understudy)
- **Primary metric for machine translation**
- Measures **precision** — how much of the generated text appears in the reference
- Score Range: 0 to 1 (higher = better)
- Includes **brevity penalty** to discourage overly short translations

**Key Difference**: BLEU = precision-focused | ROUGE = recall-focused

### Semantic Similarity Metrics

#### BERTScore
- Uses **contextual embeddings** from pre-trained BERT models
- Measures **semantic similarity** rather than exact word overlap
- Captures meaning even when different words are used (paraphrasing)
- Score Range: 0 to 1 (higher = better)
- The ONLY exam-listed metric that captures semantic meaning

### Question Answering Metrics
| Metric | Description |
|--------|-------------|
| **Exact Match (EM)** | Binary — does the answer exactly match the reference? |
| **Quasi-Exact Match** | Match after removing punctuation and articles (normalization) |
| **F1 over Words** | Token-level F1 between predicted and reference answer |

### Other Important Metrics

#### Perplexity
- Measures how well a language model predicts the next token
- **Lower perplexity = better model** (more confident predictions)
- Used during pre-training and fine-tuning evaluation
- Not directly available in Bedrock Model Evaluation, but important conceptually

#### Cosine Similarity
- Measures angle between two embedding vectors
- Used in RAG evaluation to measure retrieval relevance
- Score Range: -1 to 1 (higher = more similar)

### Metric Selection Decision Tree
```
What task are you evaluating?
├── Text Summarization → ROUGE (primary), METEOR, BERTScore
├── Machine Translation → BLEU (primary), METEOR
├── Question Answering → Exact Match, F1 over Words
├── Classification → Accuracy, Precision, Recall, F1
├── Open-ended Generation → Human Eval, LLM-as-a-Judge, Toxicity
├── Semantic Similarity → BERTScore, Cosine Similarity
└── Any task (paraphrasing matters) → BERTScore
```

### Quick Comparison Table

| Metric | Best For | Focus | Captures Semantic Meaning? |
|--------|----------|-------|---------------------------|
| **ROUGE** | Summarization | Recall (reference coverage) | ❌ No (word overlap only) |
| **BLEU** | Translation | Precision (generated accuracy) | ❌ No (word overlap only) |
| **BERTScore** | Any text generation | Semantic similarity | ✅ Yes |
| **METEOR** | Summarization/Translation | Balanced (P+R + synonyms) | Partially (synonyms) |
| **Perplexity** | Language modeling | Prediction confidence | ❌ No |
| **Exact Match** | Q&A | Binary correctness | ❌ No |
| **F1 over Words** | Q&A | Precision + Recall balance | ❌ No |

---

## 5. Evaluating FM Applications (RAG, Agents, Workflows)

### RAG Evaluation

Amazon Bedrock supports two types of RAG evaluation jobs:

#### Retrieve-Only Evaluation
- Evaluates the **retrieval** component only
- Measures: Did the knowledge base retrieve the right documents?
- Compares retrieved passages against ground truth expected passages
- Metrics: Relevance, context precision, context recall

#### Retrieve-and-Generate Evaluation
- Evaluates the **full RAG pipeline** (retrieval + generation)
- Measures: Did the system retrieve relevant info AND generate a good response?
- Compares both retrieved context and generated response against ground truth
- Metrics: Correctness, completeness, faithfulness, relevance, helpfulness

**RAG Evaluation Metrics**:

| Metric | What It Measures | Evaluation Type |
|--------|-----------------|-----------------|
| **Correctness** | Is the generated response factually correct? | Retrieve & Generate |
| **Completeness** | Does the response fully answer the query? | Retrieve & Generate |
| **Faithfulness** | Is the response grounded in retrieved context (no hallucination)? | Retrieve & Generate |
| **Relevance** | Is the retrieved context relevant to the query? | Both |
| **Helpfulness** | Is the response useful to the user? | Retrieve & Generate |

> **Exam Tip**: Faithfulness is critical for RAG — it measures whether the model hallucinated beyond the retrieved context.

### Agent Evaluation
- Evaluate end-to-end task completion
- Measure tool selection accuracy (did the agent pick the right tools?)
- Assess multi-step reasoning quality
- Amazon Bedrock AgentCore Evaluations uses LLM-as-a-Judge with built-in/custom evaluators
- Track: task success rate, number of steps, latency, cost per task

### Workflow Evaluation
- Evaluate each step independently AND the end-to-end pipeline
- Measure intermediate outputs at each stage
- Track cumulative error propagation
- Use A/B testing to compare workflow variants

---

## 6. Meeting Business Objectives

### Mapping FM Performance to Business KPIs

| Business Objective | FM Metrics to Track | How to Measure |
|-------------------|--------------------|-----------------| 
| **Productivity** | Task completion rate, response latency, tokens per task | Time saved vs. manual process |
| **User Engagement** | User satisfaction scores, session length, return rate | Human evaluation, feedback surveys |
| **Task Engineering** | Task success rate, accuracy on domain tasks | Automatic metrics + human review |
| **Cost Efficiency** | Cost per inference, tokens consumed, model size | CloudWatch metrics, billing data |
| **Quality** | ROUGE/BLEU/BERTScore, correctness, faithfulness | Bedrock Model Evaluation |
| **Safety** | Toxicity score, bias detection, refusal rate | Automatic toxicity + Guardrails |

### Evaluation Strategy Best Practices
1. **Start with automatic evaluation** — fast, cheap, reproducible
2. **Use LLM-as-a-Judge for subjective quality** — scalable alternative to humans
3. **Reserve human evaluation for final validation** — gold standard but expensive
4. **Combine multiple metrics** — no single metric captures all quality dimensions
5. **Evaluate on domain-specific data** — generic benchmarks may not reflect your use case
6. **Monitor continuously** — model performance can degrade over time
7. **A/B test in production** — compare models with real user traffic

---

## 7. AWS Services for FM Evaluation

### Amazon Bedrock Model Evaluation
- **Three evaluation types**: Automatic, Human-based, LLM-as-a-Judge
- **Task types**: Text generation, summarization, Q&A, classification
- **Dimensions**: Accuracy, robustness, toxicity
- **Datasets**: Built-in benchmarks (TREX, Gigaword, BoolQ, TriviaQA, etc.) or custom (JSONL in S3)
- **Compare**: Up to 2 models side-by-side
- **RAG evaluation**: Retrieve-only and retrieve-and-generate jobs
- **Output**: Reports in S3 + console visualization

### SageMaker FMEval
- Foundation Model Evaluations library (open-source)
- Supports same task types as Bedrock
- Can evaluate models outside of Bedrock
- Programmatic API (Python SDK)
- Integrates with SageMaker Studio

### Amazon Bedrock AgentCore Evaluations
- Evaluate agent performance using LLM-as-a-Judge
- Built-in and custom evaluators
- Supports Strands, LangGraph frameworks
- OpenTelemetry integration for tracing

### Built-in Benchmark Datasets

| Dataset | Task Type | Description |
|---------|-----------|-------------|
| **TREX** | Text Generation | Knowledge base triples from Wikipedia |
| **BOLD** | Text Generation | Bias in Open-ended Language Generation |
| **RealToxicityPrompts** | Text Generation | 100K prompts designed to elicit toxic text |
| **Gigaword** | Summarization | News headline summarization |
| **BoolQ** | Q&A | Boolean (yes/no) questions |
| **TriviaQA** | Q&A | Trivia questions with evidence documents |
| **NaturalQuestions** | Q&A | Real Google search questions |
| **Women's Ecommerce Reviews** | Classification | Product review classification |
| **CrowS-Pairs** | Stereotyping | Bias challenge dataset |

---

## 8. Exam Focus Areas

### Common Question Types
- **"Which metric for summarization?"** → ROUGE
- **"Which metric for translation?"** → BLEU
- **"Which metric captures semantic meaning?"** → BERTScore
- **"Which metric is recall-focused?"** → ROUGE
- **"Which metric is precision-focused?"** → BLEU
- **"How to evaluate FM on custom data?"** → Bedrock Model Evaluation
- **"Scalable alternative to human evaluation?"** → LLM-as-a-Judge
- **"Evaluate RAG retrieval quality?"** → Bedrock RAG Evaluation (retrieve-only)
- **"Check if model hallucinates beyond context?"** → Faithfulness metric
- **"High robustness score — good or bad?"** → BAD (means output changes a lot)

### Gotchas
- **Robustness**: LOW score = GOOD (model is stable). HIGH score = BAD (model is unstable)
- **Toxicity**: LOW score = GOOD (safe). HIGH score = BAD (harmful)
- **Text classification does NOT support toxicity evaluation** in Bedrock automated evaluation
- ROUGE measures **recall** (not precision). BLEU measures **precision** (not recall)
- BERTScore is the ONLY exam-listed metric that captures **semantic similarity** without exact word matches
- Human evaluation is the gold standard but NOT always the best choice (expensive, slow, not scalable)
- LLM-as-a-Judge requires TWO models: a generator model and an evaluator model
- Perplexity is important conceptually but not a built-in Bedrock evaluation metric

### Comparison: Evaluation Approaches

| Approach | Speed | Cost | Scalability | Subjectivity | Best For |
|----------|-------|------|-------------|-------------|----------|
| **Automatic** | Fast | Low | High | None | Quantitative metrics, benchmarking |
| **Human** | Slow | High | Low | High | Subjective quality, domain expertise |
| **LLM-as-a-Judge** | Fast | Medium | High | Medium | Scalable quality assessment |

### Decision Tree: Choosing an Evaluation Approach
```
What do you need to evaluate?
├── Quantitative metrics (ROUGE, BLEU, F1)?
│   └── Automatic evaluation (Bedrock programmatic)
├── Subjective quality (creativity, tone, domain accuracy)?
│   ├── Need scale? → LLM-as-a-Judge
│   └── Need highest accuracy? → Human evaluation
├── Safety (toxicity, bias, stereotyping)?
│   └── Automatic evaluation (toxicity dimension)
├── RAG pipeline quality?
│   ├── Retrieval only? → Retrieve-only RAG evaluation
│   └── Full pipeline? → Retrieve-and-generate RAG evaluation
└── Business KPIs (productivity, engagement)?
    └── A/B testing + user feedback + automatic metrics
```

---

## 9. Sample Questions

### Question 1
**A company wants to evaluate the quality of machine translations produced by a foundation model. Which metric is MOST appropriate?**

A) ROUGE-L
B) BLEU
C) BERTScore
D) F1 over Words

**Correct Answer: B) BLEU**

- ✅ **B) BLEU** — Bilingual Evaluation Understudy is specifically designed for machine translation evaluation, measuring precision of n-gram overlap with a brevity penalty
- ❌ A) ROUGE-L — Designed for summarization evaluation (recall-focused), not translation
- ❌ C) BERTScore — Measures semantic similarity but is not the primary/standard metric for translation
- ❌ D) F1 over Words — Used for question answering evaluation, not translation

---

### Question 2
**A team needs to evaluate foundation model outputs at scale for subjective quality dimensions like helpfulness and coherence, but cannot afford to hire a large team of human reviewers. Which approach should they use?**

A) Automatic evaluation with built-in benchmark datasets
B) Human evaluation with a small work team
C) LLM-as-a-Judge evaluation in Amazon Bedrock
D) SageMaker Model Monitor

**Correct Answer: C) LLM-as-a-Judge evaluation in Amazon Bedrock**

- ✅ **C)** — LLM-as-a-Judge uses an evaluator LLM to score responses on subjective dimensions (helpfulness, coherence, correctness) at scale, without requiring human reviewers
- ❌ A) — Automatic evaluation computes quantitative metrics (ROUGE, F1) but cannot assess subjective quality like helpfulness
- ❌ B) — Human evaluation provides subjective assessment but doesn't scale and is expensive (contradicts the constraint)
- ❌ D) — SageMaker Model Monitor is for detecting data/model drift in production ML models, not FM evaluation

---

### Question 3
**A company runs an automated model evaluation job in Amazon Bedrock for a text summarization task. The robustness score is 45%. What does this indicate?**

A) The model produces accurate summaries 45% of the time
B) The model's summary quality drops by 45% when inputs are slightly perturbed
C) 45% of the model's summaries contain toxic content
D) The model captures 45% of the reference summary content

**Correct Answer: B) The model's summary quality drops by 45% when inputs are slightly perturbed**

- ✅ **B)** — Robustness in Bedrock is measured as the percentage change in accuracy (e.g., Delta BERTScore / BERTScore × 100) when inputs are perturbed with typos, case changes, and whitespace. A 45% score means significant quality degradation — the model is NOT robust
- ❌ A) — Accuracy is a separate dimension, not robustness
- ❌ C) — Toxicity is a separate dimension measured by the detoxify algorithm
- ❌ D) — This describes recall (ROUGE), not robustness

---

### Question 4
**A company has deployed a RAG-based application using Amazon Bedrock Knowledge Bases. They want to verify that the generated responses are grounded in the retrieved documents and do not contain hallucinated information. Which evaluation metric should they prioritize?**

A) Relevance
B) Completeness
C) Faithfulness
D) Correctness

**Correct Answer: C) Faithfulness**

- ✅ **C) Faithfulness** — Specifically measures whether the response contains only information found in the retrieved context, detecting hallucination beyond the provided documents
- ❌ A) Relevance — Measures if the retrieved context is relevant to the query, not whether the response is grounded in it
- ❌ B) Completeness — Measures if the response fully answers the question, not whether it's grounded in context
- ❌ D) Correctness — Measures if the response is factually correct, but doesn't specifically check grounding in retrieved context

---

### Question 5
**Which of the following evaluation dimensions is NOT available for text classification tasks in Amazon Bedrock automated model evaluation?**

A) Accuracy
B) Semantic Robustness
C) Toxicity
D) All three are available

**Correct Answer: C) Toxicity**

- ✅ **C) Toxicity** — Text classification in Bedrock automated evaluation only supports Accuracy and Robustness. Toxicity evaluation is NOT available for classification tasks
- ❌ A) — Accuracy IS available (classification_accuracy_score)
- ❌ B) — Semantic Robustness IS available (delta_classification_accuracy_score)
- ❌ D) — Incorrect; toxicity is not available for classification

---

**Exam Tip**: The AIF-C01 exam specifically lists ROUGE, BLEU, and BERTScore as the three key metrics in Task 3.4. Remember: ROUGE = summarization (recall), BLEU = translation (precision), BERTScore = semantic similarity. For Bedrock evaluation: accuracy HIGH = good, robustness LOW = good, toxicity LOW = good. Know all three evaluation approaches (automatic, human, LLM-as-a-Judge) and when to use each.

---

## Citations
- [Task Statement 3.4 — AIF-C01 Exam Guide](https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/ai-practitioner-01-domain3.html)
- [Evaluate the performance of Amazon Bedrock resources](https://docs.aws.amazon.com/bedrock/latest/userguide/evaluation.html)
- [Use metrics to understand model performance (LLM-as-a-Judge)](https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation-metrics.html)
- [Evaluate model performance using another LLM as a judge](https://docs.aws.amazon.com/bedrock/latest/userguide/evaluation-judge.html)
- [Review a human-based model evaluation job](https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation-report-human-customer.html)
- [Evaluate RAG sources using Amazon Bedrock evaluations](https://docs.aws.amazon.com/bedrock/latest/userguide/evaluation-kb.html)
- [FMEval evaluation dimensions and tasks](https://docs.aws.amazon.com/sagemaker/latest/dg/clarify-foundation-model-evaluate-overview.html)
