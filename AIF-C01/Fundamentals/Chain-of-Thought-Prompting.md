# Chain-of-Thought (CoT) Prompting

## 1. Topic Overview
- **Purpose**: A prompt engineering technique that guides foundation models to break down complex problems into intermediate reasoning steps before arriving at a final answer
- **Category**: Prompt Engineering Technique
- **Key Use Cases**:
  - Multi-step mathematical reasoning and word problems
  - Complex logical deductions and analysis
  - Decision-making with multiple factors
  - Debugging and troubleshooting workflows
  - Financial analysis and comparisons
- **Exam Weight**: HIGH
- **Exam Domain**:
  - Domain 2: Fundamentals of Generative AI (24%)
  - Domain 3: Applications of Foundation Models (28%)
- **Task Statements**: Understanding prompt engineering techniques, optimizing FM outputs, applying best practices for generative AI applications

## 2. Exam Keyword Mapping
- **Trigger Words**: "step-by-step reasoning", "think through the problem", "show your work", "explain reasoning", "break down the problem", "intermediate steps", "chain-of-thought", "CoT prompting"
- **Scenario Indicators**:
  - "The model is giving incorrect answers to math problems" → Add CoT prompting
  - "Need to improve accuracy of complex reasoning tasks" → Use CoT
  - "Want to understand how the model arrived at an answer" → CoT makes reasoning transparent
  - "Model jumps to conclusions without analysis" → CoT forces step-by-step thinking
- **Anti-patterns**:
  - ❌ Simple factual lookups ("What is the capital of France?") — CoT adds unnecessary overhead
  - ❌ Latency-sensitive applications — CoT increases output tokens and response time
  - ❌ Cost-sensitive high-volume inference — more tokens = higher cost
  - ❌ Basic classification or extraction tasks — zero/few-shot is sufficient

## 3. Core Concepts

### What is Chain-of-Thought Prompting?
- Guides the model to **think step-by-step** and make attempts at reasoning **before** arriving at an answer
- Breaks complex issues into simpler, more manageable intermediate thoughts
- Makes the reasoning process **transparent and easier to follow**
- Minimizes errors by ensuring clearer logic and improved coherence

### How It Works
```
Input → Model identifies sub-problems → Reasons through each step → Arrives at final answer
```

### Three Styles of CoT Prompting

#### 1. Open-Ended CoT (Zero-Shot CoT)
- Simply append **"Think step-by-step"** to the end of your query
- No examples needed — the model generates its own reasoning chain
- Easiest to implement
```
{{User query}} Think step-by-step.
```
- Stronger variant: **"DO NOT provide answer without thinking step by step"**

#### 2. Guided CoT (Structured Steps)
- Explicitly specify the reasoning steps the model should follow
- Gives you more control over the reasoning direction
```
{{User query}} Please follow these steps:
1. {{Step 1}}
2. {{Step 2}}
3. {{Step 3}}
...
```
- Example: *"First, think through the financial goals. Then think through the risk tolerance. Finally, provide your recommendation."*

#### 3. System-Level CoT (Thinking Tags)
- Use system prompts to instruct the model to separate reasoning from the answer
- Recommended for **Amazon Nova Premier** and advanced reasoning tasks
```json
"system": [{
    "text": "The Bot first thinks about the reasoning process and then provides 
the User with the answer. The reasoning process is enclosed with 
<thinking> </thinking> and answer enclosed with <output> </output>"
}]
```
- Separates the **thinking process** from the **final output**
- Best for debugging — you can inspect the model's reasoning

### CoT vs. Other Prompting Techniques

| Technique | Description | When to Use |
|-----------|-------------|-------------|
| **Zero-Shot** | No examples, direct instruction | Simple tasks, factual questions |
| **Few-Shot** | Provide input-output examples | Pattern matching, classification |
| **Chain-of-Thought** | Force step-by-step reasoning | Complex reasoning, math, multi-step analysis |
| **Few-Shot CoT** | Examples WITH reasoning steps shown | Most effective for complex tasks |
| **Prompt Chaining** | Sequential LLM calls, output feeds next input | Multi-stage workflows |

### CoT for Speech (Amazon Nova)
When using CoT with speech-to-speech models:
- Keep reasoning chains **shorter** than text interactions
- Break complex explanations into **smaller conversational chunks**
- Use **verbal signposting** ("First point... Second point...") more explicitly

## 4. Key Features & Components

### Benefits of CoT
- **Improves accuracy**: Model lands at better answers by thinking before responding
- **Improves debugging**: Reveals where and why the model made errors — essential for iterating on prompts
- **Transparency**: Makes the reasoning process visible and auditable
- **Reduces hallucinations**: Step-by-step reasoning grounds the model in logical progression

### Trade-offs
| Benefit | Cost |
|---------|------|
| Higher accuracy | Increased latency |
| Better debugging | More output tokens (higher cost) |
| Transparent reasoning | Not needed for simple tasks |
| Fewer errors on complex tasks | May over-complicate straightforward queries |

### AWS Implementation

#### Amazon Bedrock
- CoT is listed as a core prompt engineering task: **"Chain-of-thought reasoning: Give step-by-step reasoning on how an answer is derived based on your prompt"**
- Works with all text models on Bedrock (Titan, Claude, Llama, Mistral, etc.)
- Use **Bedrock Prompt Management** to version and optimize CoT prompts
- Use **Bedrock Prompt Optimization** to automatically refine CoT prompts

#### Amazon Nova Models
- Amazon Nova models **work better in reasoning** when given time to think
- **Nova Premier** is recommended for advanced chain-of-thought tasks
- Supports `<thinking>` / `<output>` tag separation via system prompts

## 5. Exam Focus Areas

### Common Question Types
- **Scenario**: "A developer's FM is producing incorrect answers to multi-step math problems. What technique should they use?" → **Chain-of-thought prompting**
- **Comparison**: "What is the difference between few-shot prompting and chain-of-thought prompting?" → Few-shot provides examples; CoT forces step-by-step reasoning
- **Best Practice**: "When should you NOT use chain-of-thought prompting?" → Simple tasks, latency-sensitive apps, cost-sensitive high-volume inference
- **Implementation**: "How do you implement CoT in Amazon Nova?" → Add "Think step-by-step" or use system-level `<thinking>` tags

### Gotchas
- ⚠️ CoT is NOT the same as **prompt chaining** — CoT is within a single prompt; prompt chaining is sequential LLM calls
- ⚠️ CoT increases **both latency AND cost** due to additional output tokens
- ⚠️ Not all tasks benefit from CoT — simple factual questions may get worse results
- ⚠️ Zero-shot CoT ("Think step-by-step") works surprisingly well without needing examples
- ⚠️ Few-shot CoT (examples with reasoning) is the **most effective** variant but requires more prompt engineering effort

### Decision Tree
```
Is the task complex (math, multi-step reasoning, analysis)?
├── YES → Use Chain-of-Thought
│   ├── Need maximum accuracy? → Few-Shot CoT (examples with reasoning)
│   ├── Need quick implementation? → Zero-Shot CoT ("Think step-by-step")
│   └── Need to inspect reasoning? → System-Level CoT (<thinking> tags)
└── NO → Use Zero-Shot or Few-Shot prompting (simpler, faster, cheaper)
```

## 6. Best Practices

### Prompt Design
- Start with **"Think step-by-step"** — simplest and often sufficient
- Use **stronger wording** if the model still skips reasoning: "DO NOT provide answer without thinking step by step"
- For maximum control, **explicitly define the steps**: "First... Then... Finally..."
- Use **`<thinking>` / `<output>` tags** in system prompts to separate reasoning from answers

### Cost Optimization
- Only use CoT for tasks that **actually require reasoning** (math, logic, multi-step analysis)
- Avoid CoT for simple classification, extraction, or factual lookups
- Monitor output token counts — CoT can 2-5x your token usage
- Consider using **Bedrock Prompt Optimization** to find the most efficient CoT format

### Security
- CoT reasoning may expose internal logic — be cautious in user-facing applications
- Use **Bedrock Guardrails** to filter CoT output if reasoning contains sensitive information
- Consider stripping `<thinking>` tags before returning responses to end users

## 7. Hands-On Examples

### Zero-Shot CoT (Simplest)
```
User: A store has 45 apples. They sell 12 in the morning and receive 
a shipment of 30 in the afternoon. Then they sell 18 more. 
How many apples do they have? Think step-by-step.
```

### Guided CoT
```
User: Evaluate whether to use Amazon Bedrock or Amazon SageMaker 
for deploying a text generation model. 

Please follow these steps:
1. First, consider the team's ML expertise level
2. Then, evaluate customization requirements
3. Next, compare infrastructure management needs
4. Finally, provide your recommendation with reasoning
```

### System-Level CoT (Bedrock API)
```python
import boto3, json

client = boto3.client("bedrock-runtime")

response = client.converse(
    modelId="amazon.nova-pro-v1:0",
    system=[{
        "text": """Think through problems step-by-step. 
Enclose reasoning in <thinking></thinking> tags 
and final answer in <output></output> tags."""
    }],
    messages=[{
        "role": "user",
        "content": [{"text": "If a train travels 120 km in 1.5 hours, "
                              "then stops for 30 minutes, then travels "
                              "80 km in 1 hour, what is the average speed "
                              "for the entire journey?"}]
    }]
)

print(response["output"]["message"]["content"][0]["text"])
```

## 8. Sample Questions

### Question 1
**A machine learning engineer is using Amazon Bedrock to build a financial analysis application. The foundation model frequently provides incorrect calculations when analyzing multi-step investment scenarios. Which prompt engineering technique should the engineer implement to improve accuracy?**

A) Increase the temperature parameter to generate more diverse responses  
B) Use chain-of-thought prompting to guide the model through step-by-step reasoning  
C) Switch to a smaller model to reduce hallucinations  
D) Add more few-shot examples of correct final answers without showing reasoning steps  

**Correct Answer: B**

*Explanation*: Chain-of-thought prompting improves accuracy on complex reasoning tasks like multi-step calculations by forcing the model to think through intermediate steps before arriving at an answer. (A) Higher temperature increases randomness, making calculations worse. (C) Smaller models typically have weaker reasoning. (D) Few-shot examples without reasoning steps don't teach the model HOW to reason — showing the reasoning process (few-shot CoT) would be better, but standard CoT (B) is the most direct improvement.

---

### Question 2
**A developer is building a high-throughput customer support chatbot on Amazon Bedrock that handles simple FAQ-style questions. A colleague suggests adding "Think step-by-step" to every prompt. What is the MOST accurate concern about this approach?**

A) Chain-of-thought prompting is not supported on Amazon Bedrock  
B) It will increase latency and token costs without meaningful accuracy improvement for simple tasks  
C) It will cause the model to hallucinate more frequently  
D) Chain-of-thought only works with Amazon Nova models  

**Correct Answer: B**

*Explanation*: CoT adds overhead (more output tokens → higher latency and cost). For simple FAQ questions, the model can answer directly without step-by-step reasoning, so CoT provides no accuracy benefit while increasing costs. (A) CoT is fully supported on Bedrock. (C) CoT generally reduces hallucinations, not increases them. (D) CoT works with all LLMs on Bedrock, not just Nova.

---

### Question 3
**A data scientist wants to debug why an Amazon Nova model is arriving at incorrect conclusions in a complex analysis task. They want to see the model's intermediate reasoning separately from the final answer. Which approach should they use?**

A) Enable Amazon CloudWatch logging for the Bedrock endpoint  
B) Use system-level chain-of-thought with `<thinking>` and `<output>` tags to separate reasoning from the answer  
C) Use Amazon SageMaker Clarify to explain the model's predictions  
D) Increase the max tokens parameter to get longer responses  

**Correct Answer: B**

*Explanation*: System-level CoT with `<thinking>` and `<output>` tags explicitly separates the model's reasoning process from the final answer, making it easy to inspect where the model's logic went wrong. This is the recommended approach for Amazon Nova Premier. (A) CloudWatch logs API calls, not reasoning steps. (C) SageMaker Clarify explains ML model features, not FM reasoning. (D) More tokens doesn't structure the output for debugging.

---

**Exam Tip**: The AIF-C01 exam tests whether you know WHEN to use CoT (complex reasoning tasks) and WHEN NOT to (simple tasks where it adds unnecessary cost/latency). Remember: CoT improves accuracy but increases tokens and latency. The simplest implementation is just appending "Think step-by-step" to your prompt.

---

## Citations
- [Give Amazon Nova time to think (chain-of-thought) — Amazon Nova User Guide](https://docs.aws.amazon.com/nova/latest/userguide/prompting-chain-of-thought.html)
- [What is prompt engineering? — Amazon Bedrock User Guide](https://docs.aws.amazon.com/bedrock/latest/userguide/what-is-prompt-engineering.html)
- [Prompt engineering concepts — Amazon Bedrock User Guide](https://docs.aws.amazon.com/bedrock/latest/userguide/prompt-engineering-guidelines.html)
- [Chain-of-thought for speech — Amazon Nova User Guide](https://docs.aws.amazon.com/nova/latest/userguide/prompting-speech-bp-reasoning.html)
