# Practice Question: Real-Time Inference Deployment

**Domain**: Domain 3 — Applications of Foundation Models (28%)  
**Topic**: Model Deployment, Inference Types, Amazon SageMaker  
**Difficulty**: Easy

---

## Question

An AI practitioner wants to deploy a trained model that is used for consistent, live, interactive, and low-latency predictions.

Which solution will meet these requirements?

- A. Batch transform
- B. Real-time inference
- C. Serverless inference
- D. Asynchronous inference

---

## Answer: B. Real-time inference

---

## Explanation

The key signals are:

- **"Live"** → needs to be always available, not batch
- **"Interactive"** → request-response pattern
- **"Low-latency"** → fast responses required
- **"Consistent"** → persistent endpoint, not serverless cold starts

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. Batch transform** | ❌ | Processes large datasets offline — not live or interactive. No real-time responses. |
| **B. Real-time inference** | ✅ | Persistent endpoint that's always running, provides consistent low-latency responses for interactive use cases. |
| **C. Serverless inference** | ❌ | Has **cold start** latency when idle, so it's not consistent or low-latency for live traffic. Best for intermittent/unpredictable traffic. |
| **D. Asynchronous inference** | ❌ | Queues requests and processes them later — not interactive or low-latency. Best for large payloads with long processing times. |

### SageMaker Inference Types Decision Tree

| Requirement | Best Inference Type |
|-------------|-------------------|
| Live, low-latency, consistent traffic | **Real-time** |
| Large dataset, no urgency, offline | **Batch transform** |
| Intermittent traffic, cost-sensitive | **Serverless** |
| Large payloads, long processing (up to 1 hour) | **Asynchronous** |

---

## Exam Tip

> When you see **"live" + "interactive" + "low-latency"** together, it's always **real-time inference**.  
> The word **"consistent"** rules out serverless (cold starts).  
> The word **"interactive"** rules out batch and async.
