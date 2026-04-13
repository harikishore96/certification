# ML Hyperparameters Overview

## What Are Hyperparameters?

**Hyperparameters** are configuration settings that control the learning process and model structure. They are set *before* training begins and remain fixed during training.

**Model Parameters** are learned from data during training (e.g., neural network weights, linear regression coefficients).

## Key Differences

| Aspect | Hyperparameters | Model Parameters |
|--------|----------------|------------------|
| **Set by** | You (the practitioner) | Learning algorithm |
| **When** | Before training | During training |
| **Examples** | Learning rate, batch size, number of layers | Weights, biases, coefficients |
| **Optimization** | Manual tuning or hyperparameter search | Gradient descent, backpropagation |

## Common Hyperparameters by Category

**Model Architecture:**
- Number of layers/trees
- Number of neurons/units per layer
- Kernel size (CNNs)
- Hidden dimensions

**Training Process:**
- Learning rate
- Batch size
- Number of epochs
- Optimizer type (Adam, SGD, RMSprop)

**Regularization:**
- Dropout rate
- L1/L2 regularization strength
- Early stopping patience

**Algorithm-Specific:**
- Tree depth (Random Forest, XGBoost)
- Number of neighbors (KNN)
- Kernel type (SVM)
- Momentum, weight decay

## Why They Matter

- **Performance**: Wrong hyperparameters → poor accuracy or failure to converge
- **Efficiency**: Affect training time and computational cost
- **Generalization**: Control overfitting/underfitting balance

## Tuning Approaches

1. **Manual tuning**: Trial and error based on experience
2. **Grid search**: Exhaustive search over specified values
3. **Random search**: Random sampling from distributions
4. **Bayesian optimization**: Smart search using probabilistic models
5. **Automated ML**: Tools like AWS SageMaker Automatic Model Tuning
