# Practice Question: Overfitting — Customer Turnover Prediction

**Domain**: Domain 1 — Fundamentals of AI and ML (20%)  
**Topic**: Model Fit, Bias-Variance Tradeoff, Regularization  
**Difficulty**: Easy

---

## Question

A company is developing an ML model to predict customer turnover. The model performs well on the training dataset but does not accurately predict turnover for new data.

Which solution will resolve this issue?

- A. Increase the number of training epochs.
- B. Increase the complexity of the model.
- C. Apply regularization techniques.
- D. Reduce the size of the training dataset.

---

## Answer: C. Apply regularization techniques.

---

## Explanation

This scenario describes **overfitting** — the model learned the training data too well (including noise) and fails to generalize to new data.

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. Increase training epochs** | ❌ | More training iterations would make the model memorize the training data even more, worsening overfitting. |
| **B. Increase model complexity** | ❌ | A more complex model has more capacity to memorize noise, making overfitting worse. |
| **C. Apply regularization** | ✅ | Regularization (L1/L2, dropout, early stopping) penalizes overly complex learned patterns, forcing the model to learn simpler, more generalizable representations. |
| **D. Reduce training data** | ❌ | Less data means less diversity for the model to learn from, which increases overfitting. |

### Key Concept: Overfitting vs. Underfitting

| Scenario | Training Performance | New Data Performance | Problem | Solution |
|----------|---------------------|---------------------|---------|----------|
| Good on training, bad on new data | ✅ High | ❌ Low | **Overfitting** | Regularization, more data, simpler model |
| Bad on both training and new data | ❌ Low | ❌ Low | **Underfitting** | More complex model, more features, train longer |
| Good on both | ✅ High | ✅ High | **Good fit** | — |

### Common Regularization Techniques
- **L1 (Lasso)** — drives some weights to zero (feature selection)
- **L2 (Ridge)** — shrinks weights toward zero (prevents large weights)
- **Dropout** — randomly disables neurons during training (neural networks)
- **Early stopping** — stops training before the model starts memorizing noise
- **Cross-validation** — k-fold validation to better evaluate generalization

---

## Exam Tip

> **Key Signal**: "performs well on training data but not on new/unseen data" = **overfitting** → regularization is the fix.  
> The opposite (poor on both) = **underfitting** → increase complexity or train longer.
