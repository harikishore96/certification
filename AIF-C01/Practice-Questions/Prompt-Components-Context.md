# Practice Question: Prompt Components — Context

**Domain**: Domain 2 — Fundamentals of Generative AI (24%)  
**Topic**: Prompt Engineering, Prompt Components, Context  
**Difficulty**: Easy

---

## Question

Which part of a prompt needs the user to provide information to set up the situation for the model?

- A. Instruction
- B. Context
- C. Input data
- D. Output format

---

## Answer: B. Context

---

## Explanation

The key signal is **"set up the situation for the model"** → providing background information = context.

### Anatomy of a Prompt

| Component | Purpose | Example |
|-----------|---------|---------|
| **Context** | ✅ Sets up the situation, provides background info | "You are a senior AWS solutions architect. The company uses a microservices architecture." |
| **Instruction** | Tells the model what to do | "Summarize the following document." |
| **Input data** | The actual data to process | The document text itself |
| **Output format** | Specifies how to structure the response | "Respond in bullet points with max 5 items." |

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. Instruction** | ❌ | Tells the model **what action** to perform (summarize, translate, classify) — not the situation. |
| **B. Context** | ✅ | Provides the background, situation, role, or constraints the model should consider. Sets the stage. |
| **C. Input data** | ❌ | The raw content to be processed — not background information. |
| **D. Output format** | ❌ | Defines **how** the response should look — not the situation. |

### Example Prompt Breakdown

```
[Context]     You are a customer support agent for an e-commerce company 
              that sells electronics. The customer has a 30-day return policy.

[Instruction] Respond to the following customer complaint politely and 
              offer a resolution.

[Input data]  "I bought a laptop 2 weeks ago and the screen is flickering."

[Output]      Format your response as: Greeting, Acknowledgment, Resolution, Closing.
```

---

## Exam Tip

> **"Set up the situation"** or **"background information"** → **Context**.  
> **"What to do"** → **Instruction**.  
> **"What to process"** → **Input data**.  
> **"How to respond"** → **Output format**.  
> These four components of a prompt are tested frequently on the AIF-C01.
