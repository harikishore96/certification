# Practice Question: Limiting FM Output with Max Tokens

**Domain**: Domain 3 — Applications of Foundation Models (28%)  
**Topic**: Inference Parameters, Max Tokens, Amazon Bedrock  
**Difficulty**: Easy

---

## Question

An AI practitioner is testing a model that is hosted on Amazon Bedrock. The AI practitioner has prompted the model to require only the first token that the model returns. The rest of the response is always unneeded.

What should the AI practitioner do to return only the first token?

- A. Set the Temperature to 0.
- B. Set the Max Tokens to 1.
- C. Add a Stop Sequence after the first token.
- D. Use prompt engineering to instruct the model to return one token.

---

## Answer: B. Set the Max Tokens to 1.

---

## Explanation

Max Tokens controls the maximum number of tokens the model generates in its response. Setting it to 1 ensures only the first token is returned.

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. Set Temperature to 0** | ❌ | Controls randomness, not output length. Model still generates a full response. |
| **B. Set Max Tokens to 1** | ✅ | Directly limits output to exactly 1 token. Simple, effective, and reduces cost. |
| **C. Use a Stop Sequence** | ❌ | Stops generation when a specific string is encountered — can't guarantee stopping after exactly 1 token since you'd need to know what the first token will be. |
| **D. Use prompt engineering** | ❌ | Instructing the model to "return only one word" is unreliable — models don't always follow length instructions precisely. |

### Inference Parameters Quick Reference

| Parameter | Controls | Use When |
|-----------|----------|----------|
| **Max Tokens** | Response length (number of tokens) | You need to limit output size |
| **Temperature** | Randomness/creativity | You need more/less creative responses |
| **Top P** | Token probability threshold | You need to control diversity |
| **Top K** | Number of candidate tokens | You need to narrow token selection |
| **Stop Sequences** | When to stop generating | You need to stop at a specific delimiter |

---

## Exam Tip

> When the question is about controlling **how much** the model outputs → **Max Tokens**.  
> When it's about **how random/creative** → **Temperature**.  
> Reducing Max Tokens also reduces cost since you pay per output token on Bedrock.
