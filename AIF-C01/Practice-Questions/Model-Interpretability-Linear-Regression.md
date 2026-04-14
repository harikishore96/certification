# Practice Question: Model Interpretability — Linear Regression

**Domain**: Domain 1 — Fundamentals of AI and ML (20%)  
**Topic**: Model Types, Interpretability, Explainability  
**Difficulty**: Easy

---

## Question

A company wants to build an ML model to make predictions. The model's predictions must be interpretable.

Which type of model has the HIGHEST interpretability?

- A. Neural network
- B. Random forest
- C. Decision tree
- D. Linear regression

---

## Answer: D. Linear regression

---

## Explanation

Linear regression uses the formula: `y = β₀ + β₁x₁ + β₂x₂ + ... + βₙxₙ`

- Each coefficient (β) directly shows the **impact** of each feature on the prediction
- You can clearly explain: "For every 1 unit increase in X, the prediction changes by β"
- No hidden layers, no black-box behavior

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. Neural network** | ❌ | Classic "black box" — millions of weights with no clear explanation of how inputs map to outputs. |
| **B. Random forest** | ❌ | Ensemble of many decision trees — individual predictions are hard to explain. |
| **C. Decision tree** | ❌ | Interpretable but less so than linear regression, especially as depth increases. |
| **D. Linear regression** | ✅ | Most interpretable — clear, direct relationship between inputs and output. |

### Interpretability Ranking

| Model | Interpretability | Why |
|-------|-----------------|-----|
| **Linear Regression** | ✅ Highest | Clear coefficients, direct feature-to-output relationship |
| **Decision Tree** | High | Visual rules, easy to follow if-then logic |
| **Random Forest** | Medium | Ensemble of trees — harder to trace individual decisions |
| **Neural Network / Deep Learning** | ❌ Lowest | Millions of parameters, hidden layers — "black box" |

### Interpretability vs. Accuracy Tradeoff

```
High Interpretability ←————————————→ High Accuracy
Linear Regression → Decision Tree → Random Forest → Neural Network
(Simple)                                            (Complex)
```

Generally, simpler models are more interpretable but less accurate on complex tasks. Complex models are more accurate but harder to explain.

---

## Exam Tip

> When the question emphasizes **"interpretability"** or **"explainability"**, choose the **simplest model** — linear regression.  
> When it emphasizes **"accuracy on complex data"**, choose neural networks/deep learning.  
> This tradeoff is a core AIF-C01 concept.
