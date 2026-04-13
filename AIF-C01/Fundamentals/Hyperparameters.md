# Hyperparameters

## 1. Topic Overview
- **Purpose**: Configuration settings set before training that control the learning process, model structure, and optimization behavior — they are NOT learned from data
- **Exam Weight**: Medium-High
- **Exam Domain**: Domain 1 — Fundamentals of AI and ML (20%)
- **Task Statements**: Task 1.2 (Identify practical use cases for AI), Task 1.3 (Describe the ML development lifecycle)

## 2. Exam Keyword Mapping
- **Trigger Words**: "hyperparameter", "learning rate", "batch size", "epochs", "regularization", "dropout", "tuning", "optimization"
- **Scenario Indicators**: Questions about model training configuration, improving model performance, overfitting/underfitting, fine-tuning foundation models
- **Anti-patterns**: Hyperparameters are NOT model parameters — weights and biases are learned during training, hyperparameters are set before training

## 3. Core Concepts

### Hyperparameters vs. Model Parameters

| Aspect | Hyperparameters | Model Parameters |
|--------|----------------|------------------|
| **Set by** | You (the practitioner) | Learning algorithm |
| **When** | Before training | During training |
| **Examples** | Learning rate, batch size, epochs | Weights, biases, coefficients |
| **How optimized** | Manual tuning or HPO | Gradient descent, backpropagation |
| **Stored where** | Training configuration | Model artifact |

### Why Hyperparameters Matter
- **Performance**: Wrong hyperparameters → poor accuracy or failure to converge
- **Efficiency**: Affect training time and computational cost
- **Generalization**: Control the overfitting/underfitting balance
- **Cost**: More epochs = more training time = higher cost (especially in Bedrock fine-tuning)

## 4. Key Hyperparameters by Category

### 4.1 Training Process Hyperparameters

#### Learning Rate ⭐
- **What**: Controls the step size at which model parameters are updated after each batch
- **Range**: Typically 1e-6 to 1.0 (varies by algorithm)
- **Impact**:
  - Too high → overshoots optimal values, training becomes unstable, may diverge
  - Too low → very slow convergence, may get stuck in local minima
  - Just right → stable convergence to optimal solution
- **Exam Tip**: 🎯 Most frequently tested hyperparameter

```
Learning Rate Too High:     ╱╲╱╲╱╲  (oscillates, never converges)
Learning Rate Too Low:      ──────── (barely moves, very slow)
Learning Rate Just Right:   ╲_______ (smooth convergence)
```

#### Learning Rate Warmup Steps
- **What**: Number of iterations where learning rate gradually increases from 0 to the target value
- **Why**: Helps model converge more effectively, avoids divergence early in training
- **AWS Context**: Used in Bedrock fine-tuning (Nova, Titan, Claude models)
- **Tip**: Avoid large warmup values with small datasets — learning rate may never reach target

#### Batch Size
- **What**: Number of training samples processed before updating model parameters
- **Impact**:
  - Larger batch → faster training, more memory, may generalize worse
  - Smaller batch → slower training, less memory, often better generalization
  - Too large → risk of out-of-memory (OOM) errors
- **Typical values**: 1, 8, 16, 32, 64, 128, 256

#### Number of Epochs
- **What**: Number of complete passes through the entire training dataset
- **Impact**:
  - Too few → underfitting (model hasn't learned enough)
  - Too many → overfitting (model memorizes training data)
- **Cost Impact**: Each epoch processes the entire dataset → more epochs = higher cost
- **AWS Context**: Bedrock fine-tuning defaults:
  - Nova models: 2 epochs (max 5)
  - Titan Text Lite/Express: 5 epochs (max 10)
  - Claude 3: configurable with early stopping

```
Epochs vs. Performance:

Accuracy │        ╭──── Overfitting zone
         │      ╱  ────────
         │    ╱
         │  ╱
         │╱
         └──────────────────
              Epochs →
         ↑
    Sweet spot (use early stopping to find it)
```

#### Optimizer Type
- **What**: Algorithm used to update model weights during training
- **Common optimizers**:
  - **SGD** (Stochastic Gradient Descent): Simple, requires careful learning rate tuning
  - **Adam**: Adaptive learning rate, most popular default choice
  - **AdaGrad**: Adapts learning rate per parameter, good for sparse data
  - **AdaDelta**: Extension of AdaGrad, no need to set initial learning rate
  - **RMSprop**: Adapts learning rate, good for RNNs

### 4.2 Regularization Hyperparameters
Regularization prevents **overfitting** by penalizing model complexity.

#### L1 Regularization (Lasso)
- Adds absolute value of weights as penalty
- Drives irrelevant feature weights to **exactly zero** → automatic feature selection
- Produces **sparse models** (fewer features)
- Use when: You suspect many features are irrelevant

#### L2 Regularization (Ridge)
- Adds squared value of weights as penalty
- Shrinks weights toward zero but **never exactly zero**
- Stabilizes weights when features are correlated
- Use when: All features may be relevant but you want to prevent large weights

#### Dropout Rate
- **What**: Fraction of neurons randomly "turned off" during each training step
- **Range**: 0.0 to 1.0 (commonly 0.2 to 0.5)
- **Why**: Forces network to not rely on any single neuron → better generalization
- **Only used during training**, not during inference

#### Early Stopping
- **What**: Stops training when validation performance stops improving
- **Patience**: Number of epochs to wait before stopping (e.g., patience=3 means stop if no improvement for 3 epochs)
- **AWS Context**: Supported in Bedrock fine-tuning (Claude, Cohere models) and SageMaker

| Regularization | Effect | Best For |
|---------------|--------|----------|
| **L1 (Lasso)** | Zeros out features | Feature selection, sparse models |
| **L2 (Ridge)** | Shrinks all weights | Correlated features, stability |
| **Dropout** | Randomly disables neurons | Deep neural networks |
| **Early Stopping** | Stops training early | Preventing overfitting |

### 4.3 Model Architecture Hyperparameters

| Hyperparameter | Description | Impact |
|---------------|-------------|--------|
| **Number of layers** | Depth of neural network | More layers = more complex patterns, but harder to train |
| **Neurons per layer** | Width of each layer | More neurons = more capacity, but risk of overfitting |
| **Kernel size** | Filter size in CNNs | Larger = captures broader patterns |
| **Hidden dimensions** | Size of hidden representations | Larger = more expressive, more compute |

### 4.4 Algorithm-Specific Hyperparameters

| Algorithm | Key Hyperparameters |
|-----------|-------------------|
| **XGBoost** | max_depth, n_estimators, learning_rate, subsample |
| **Random Forest** | n_estimators, max_depth, min_samples_split |
| **KNN** | k (number of neighbors), distance metric |
| **SVM** | C (regularization), kernel type, gamma |
| **K-Means** | k (number of clusters), max_iterations |

## 5. Hyperparameter Tuning (HPO)

### Tuning Strategies

| Strategy | How It Works | Pros | Cons |
|----------|-------------|------|------|
| **Manual Tuning** | Trial and error based on experience | Simple, intuitive | Slow, not scalable |
| **Grid Search** | Exhaustive search over all specified combinations | Thorough, guaranteed coverage | Expensive, only categorical params in SageMaker |
| **Random Search** | Random sampling from parameter distributions | Efficient, parallelizable | May miss optimal values |
| **Bayesian Optimization** ⭐ | Uses regression to predict best next combination | Smart, efficient, learns from results | Sequential (limited parallelism) |
| **Hyperband** | Dynamically reallocates resources, stops underperformers | Fast, resource-efficient | Only for iterative algorithms |

### SageMaker Automatic Model Tuning (AMT)
- **Purpose**: Automated hyperparameter optimization service
- **How it works**: Runs multiple training jobs with different hyperparameter combinations, finds the best
- **Strategies supported**: Grid Search, Random Search, Bayesian Optimization, Hyperband
- **Key features**:
  - Specify hyperparameter ranges (min/max)
  - Define objective metric to optimize (e.g., AUC, accuracy, RMSE)
  - Set max number of training jobs and parallel jobs
  - Warm start: Reuse results from previous tuning jobs
  - Supports EC2 Spot Instances for cost savings
  - Early stopping to save compute

### Bayesian Optimization (Deep Dive)
- Treats HPO as a **regression problem**
- Balances **exploration** (trying new areas) vs. **exploitation** (refining known good areas)
- Uses results from previous training jobs to predict next best combination
- More efficient than random/grid search for continuous hyperparameters
- **Limitation**: Sequential nature limits parallelism (each job depends on previous results)

### Hyperband (Deep Dive)
- **Multi-fidelity** strategy: evaluates configurations at different resource levels
- Dynamically reallocates epochs to well-performing configurations
- Automatically stops underperforming configurations early
- Best for **iterative algorithms** that publish intermediate results (e.g., neural networks publishing accuracy after each epoch)
- Can be combined with early stopping

## 6. Hyperparameters in AWS Services

### Amazon Bedrock Fine-Tuning
Common hyperparameters across Bedrock models:

| Hyperparameter | Description | Typical Default |
|---------------|-------------|-----------------|
| **epochCount** | Iterations through entire dataset | 2-5 |
| **learningRate** | Rate of parameter updates | 1e-5 |
| **batchSize** | Samples per update | 1-8 |
| **learningRateWarmupSteps** | Gradual LR increase steps | 5-10 |
| **earlyStoppingThreshold** | Min improvement to continue | Model-specific |
| **earlyStoppingPatience** | Epochs to wait before stopping | Model-specific |

**Key Bedrock Tips**:
- More epochs = higher cost (each epoch processes entire dataset)
- Start with defaults — AWS optimizes them across tasks and data sizes
- Larger datasets need fewer epochs; smaller datasets need more
- Avoid large warmup with small datasets (LR may never reach target)

### Amazon SageMaker
- **Built-in algorithms**: Each has documented tunable hyperparameters
- **Automatic Model Tuning**: Automated HPO with multiple strategies
- **Autopilot**: Automatically selects and tunes hyperparameters (AutoML)

### Amazon SageMaker Autopilot
- **Ensembling mode**: Uses AutoGluon, trains multiple models with stacking
- **HPO mode**: Traditional hyperparameter optimization
- **Auto mode**: Automatically selects best approach based on dataset size

## 7. Hyperparameters and Overfitting/Underfitting

```
                    Underfitting              Good Fit              Overfitting
                    ──────────              ────────              ──────────
Fix with:           Fix with:               ✅ Goal               Fix with:
• More epochs       • More complex model                         • Fewer epochs
• Higher LR         • More features                              • More regularization
• More layers       • Less regularization                        • Dropout
• Less regularization                                            • Early stopping
                                                                 • More training data
```

| Problem | Hyperparameter Adjustments |
|---------|---------------------------|
| **Underfitting** | ↑ epochs, ↑ learning rate, ↑ model complexity, ↓ regularization |
| **Overfitting** | ↓ epochs, ↑ regularization (L1/L2), ↑ dropout, enable early stopping |
| **Slow convergence** | ↑ learning rate, change optimizer (try Adam), ↑ batch size |
| **Unstable training** | ↓ learning rate, ↑ warmup steps, gradient clipping |

## 8. Exam Focus Areas

### Common Question Types
- "What controls how fast a model learns?" → **Learning rate**
- "How to prevent overfitting?" → **Regularization (L1/L2, dropout, early stopping)**
- "What is the difference between hyperparameters and model parameters?" → Set before vs. learned during training
- "Which tuning strategy learns from previous results?" → **Bayesian optimization**
- "How to automatically find best hyperparameters?" → **SageMaker Automatic Model Tuning**
- "What happens with too many epochs?" → **Overfitting**

### Gotchas
- Hyperparameters are set **before** training, model parameters are learned **during** training
- Learning rate is the **most impactful** hyperparameter in most cases
- Bayesian optimization is **smarter** than random/grid search but less parallelizable
- Grid search in SageMaker only supports **categorical** parameters
- Dropout is applied during **training only**, not inference
- L1 produces **sparse** models (feature selection), L2 does **not**
- More epochs ≠ better model (overfitting risk + higher cost)

### Decision Tree
```
Model performing poorly?
│
├── Underfitting (high training error)?
│   ├── Increase epochs
│   ├── Increase learning rate
│   ├── Add more layers/neurons
│   └── Reduce regularization
│
├── Overfitting (low training error, high validation error)?
│   ├── Add regularization (L1/L2)
│   ├── Increase dropout
│   ├── Enable early stopping
│   ├── Reduce epochs
│   └── Get more training data
│
└── Need automated tuning?
    ├── SageMaker AMT (Bayesian, Random, Grid, Hyperband)
    ├── SageMaker Autopilot (full AutoML)
    └── Bedrock (use defaults, adjust epochs/LR)
```

## 9. Best Practices
- **Start with defaults**: AWS services provide well-tested defaults — start there
- **Change one at a time**: When manually tuning, change one hyperparameter at a time to understand impact
- **Use Bayesian optimization**: More efficient than grid/random search for most problems
- **Monitor training curves**: Watch for diverging training/validation loss (overfitting signal)
- **Use early stopping**: Prevents overfitting and saves compute cost
- **Log everything**: Track hyperparameter values and results for reproducibility
- **Consider cost**: More epochs and larger batch sizes increase training cost

## 10. Sample Questions

### Question 1
A data scientist is training a deep learning model and notices the training accuracy is 99% but validation accuracy is only 72%. Which hyperparameter adjustments would most likely improve the model?

A) Increase the learning rate and add more epochs  
B) Increase dropout rate and enable early stopping  
C) Decrease regularization and increase batch size  
D) Add more layers and increase the number of neurons  

**Answer: B) Increase dropout rate and enable early stopping**  
The large gap between training (99%) and validation (72%) accuracy indicates overfitting. Dropout forces the network to generalize by randomly disabling neurons. Early stopping prevents further overfitting by halting training when validation performance stops improving. Options A, C, and D would likely worsen overfitting.

---

### Question 2
A machine learning team wants to automatically find the best hyperparameters for their SageMaker training job. They want the tuning process to learn from previous training job results to make smarter choices. Which tuning strategy should they use?

A) Grid search  
B) Random search  
C) Bayesian optimization  
D) Manual tuning  

**Answer: C) Bayesian optimization**  
Bayesian optimization treats HPO as a regression problem and uses results from previous training jobs to predict which hyperparameter combinations will yield the best results. Grid search exhaustively tests all combinations. Random search samples randomly without learning. Manual tuning relies on human intuition.

---

### Question 3
When fine-tuning a foundation model in Amazon Bedrock, a developer wants to minimize cost while achieving good model performance. Which approach is recommended?

A) Set epochs to the maximum value and use the lowest learning rate  
B) Start with default hyperparameters and adjust based on results  
C) Use the largest batch size possible with maximum epochs  
D) Set learning rate warmup steps to 0 for faster training  

**Answer: B) Start with default hyperparameters and adjust based on results**  
AWS recommends starting with default hyperparameters, which are optimized across tasks of different complexity and data sizes. More epochs directly increase cost (each epoch processes the entire dataset). Maximum epochs with lowest learning rate would be expensive and slow. Large batch sizes risk OOM errors.

---

**Exam Tip**: 🎯 For AIF-C01, focus on understanding what each hyperparameter controls, the difference between hyperparameters and model parameters, how to diagnose overfitting vs. underfitting through hyperparameter adjustments, and when to use SageMaker AMT vs. manual tuning. Know that Bedrock fine-tuning costs scale with epochs.
