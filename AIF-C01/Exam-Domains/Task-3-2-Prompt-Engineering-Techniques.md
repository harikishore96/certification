# Task 3.2: Choose Effective Prompt Engineering Techniques

**Domain**: 3 — Applications of Foundation Models (28%)

## Objectives
1. Define the concepts and constructs of prompt engineering (context, instruction, negative prompts, model latent space, prompt routing)
2. Define techniques for prompt engineering (chain-of-thought, zero-shot, single-shot, few-shot, prompt templates)
3. Identify and describe the benefits and best practices (response quality improvement, experimentation, guardrails, discovery, specificity and concision, using multiple comments)
4. Define potential risks and limitations (exposure, poisoning, hijacking, jailbreaking)

## Study Notes
- [Prompt Engineering Techniques (Comprehensive)](../Fundamentals/Prompt-Engineering-Techniques.md) — Covers all 4 objectives
- [Chain-of-Thought Prompting (Deep Dive)](../Fundamentals/Chain-of-Thought-Prompting.md) — CoT techniques in detail
- [Inference Parameters: Temperature, Top P, Top K](../Fundamentals/Inference-Parameters-Sampling.md) — Related inference controls
- [Prompt Injection Attacks](../Responsible-AI/Prompt-Injection-Attacks.md) — Security risks in detail

## Quick Reference

### Techniques Spectrum
```
Zero-Shot → Single-Shot → Few-Shot → Chain-of-Thought → Few-Shot CoT
(no examples)  (1 example)  (2+ examples)  (step-by-step)   (examples + reasoning)
```

### Prompt Components
**Instruction** + **Context** + **Input** + **Examples** + **Output Format**

### Five Prompt Risks
1. **Injection** — Malicious input overrides instructions
2. **Exposure** — System prompt leaks to user
3. **Poisoning** — Malicious data in RAG sources
4. **Hijacking** — Redirecting model to different task
5. **Jailbreaking** — Bypassing safety guardrails

### Key AWS Tools
- **Bedrock Prompt Management** — Create, version, test prompts
- **Bedrock Prompt Optimization** — Auto-rewrite prompts for better results
- **Bedrock Prompt Routing** — Route to optimal model (quality + cost)
- **Bedrock Guardrails** — Prompt attack detection and content filtering

### Exam Reminders
- ✅ Try prompt engineering BEFORE fine-tuning
- ✅ Few-shot = in-context learning (same thing)
- ✅ Prompt security = customer responsibility (Shared Responsibility Model)
- ✅ Negative prompts = primarily for image generation
