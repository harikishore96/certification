# Practice Question: Bedrock Model Evaluation for FM Assessment

**Domain**: Domain 3 — Applications of Foundation Models (28%)  
**Topic**: Model Evaluation, Amazon Bedrock, FM Assessment  
**Difficulty**: Easy

---

## Question

A company uses a foundation model (FM) on Amazon Bedrock for text summarization. The company wants to check the accuracy, robustness, and toxicity of the FM.

Which Amazon Bedrock feature or resource can assess the FM to meet these requirements?

- A. Guardrails
- B. Model Evaluation
- C. Knowledge Bases
- D. Model Invocation Logging

---

## Answer: B. Model Evaluation

---

## Explanation

The key signals are:

- **"Check accuracy, robustness, and toxicity"** → model evaluation/assessment
- **"Amazon Bedrock"** → Bedrock's built-in model evaluation feature
- **Multiple evaluation dimensions** → comprehensive model assessment

Bedrock Model Evaluation allows you to evaluate FMs across multiple dimensions including accuracy, robustness, and toxicity using either automatic evaluation (built-in metrics) or human evaluation (human reviewers).

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. Guardrails** | ❌ | Filters harmful content at inference time — it's a runtime safety control, not an evaluation/assessment tool. |
| **B. Model Evaluation** | ✅ | Purpose-built to assess FM quality across accuracy, robustness, and toxicity metrics. |
| **C. Knowledge Bases** | ❌ | Implements RAG for grounding responses in external data — not related to model evaluation. |
| **D. Model Invocation Logging** | ❌ | Logs API calls and inputs/outputs for auditing — doesn't assess model quality. |

### Bedrock Model Evaluation Types

| Type | How It Works | Best For |
|------|-------------|----------|
| **Automatic** | Uses built-in algorithms and benchmark datasets | Quick, scalable evaluation of accuracy, robustness, toxicity |
| **Human** | Uses human reviewers (your team or AWS-managed) | Subjective quality, tone, relevance, helpfulness |

### Key Evaluation Dimensions

| Dimension | What It Measures |
|-----------|-----------------|
| **Accuracy** | Correctness of model outputs |
| **Robustness** | Consistency under varied/adversarial inputs |
| **Toxicity** | Harmful, offensive, or inappropriate content |

### Guardrails vs. Model Evaluation — Common Distractor

| Aspect | Model Evaluation | Guardrails |
|--------|-----------------|------------|
| **When** | Before/during model selection | At inference time (runtime) |
| **Purpose** | Assess model quality | Filter/block harmful content |
| **Action** | Reports metrics | Blocks or modifies responses |

---

## Exam Tip

> When the question asks to **"evaluate"**, **"assess"**, or **"check"** a model's quality → **Model Evaluation**.  
> When the question asks to **"prevent"**, **"filter"**, or **"block"** harmful content → **Guardrails**.
