# Practice Question: Diffusion Models for Text-to-Image Generation

**Domain**: Domain 2 — Fundamentals of Generative AI (24%)  
**Topic**: Generative AI Model Types, Diffusion Models, Text-to-Image  
**Difficulty**: Easy

---

## Question

A company wants to deploy a generative AI application to help employees create images from text inputs.

Which type of generative AI model will meet this requirement?

- A. Large Language Model (LLM)
- B. Generative Adversarial Network (GAN)
- C. Diffusion model
- D. Transformer (text-only)

---

## Answer: C. Diffusion model

---

## Explanation

The key signal is **"create images from text inputs"** → text-to-image generation → diffusion models.

### How Diffusion Models Work

1. **Forward process** — gradually adds noise to an image until it becomes pure noise
2. **Reverse process** — learns to denoise step-by-step, generating a clean image from random noise
3. **Text conditioning** — uses the text prompt to guide the denoising process toward the desired image

### Why Each Option:

| Option | Verdict | Reasoning |
|--------|---------|-----------|
| **A. Large Language Model (LLM)** | ❌ | Generates **text** output, not images. Processes and produces sequential text tokens. |
| **B. Generative Adversarial Network (GAN)** | ❌ | Can generate images but not well-suited for text-to-image. Uses generator vs. discriminator approach. Largely superseded by diffusion models. |
| **C. Diffusion model** | ✅ | Purpose-built for image generation from text. Used by Stable Diffusion, DALL-E, Amazon Titan Image Generator. |
| **D. Transformer (text-only)** | ❌ | Core architecture for LLMs — generates text, not images. |

### Generative AI Model Types by Output

| Output Type | Model Type | Example |
|-------------|-----------|---------|
| **Text → Image** | Diffusion model | Stable Diffusion, DALL-E, Titan Image Generator |
| **Text → Text** | LLM / Transformer | Claude, Titan Text, GPT |
| **Text → Video** | Diffusion model (video) | Amazon Nova Reel |
| **Text → Audio** | Audio generation model | Amazon Polly (TTS) |

### AWS Context

- **Amazon Titan Image Generator** — Bedrock's native text-to-image diffusion model
- **Stable Diffusion** — available on Bedrock via Stability AI
- **Amazon Nova Canvas** — Amazon's image generation model

---

## Exam Tip

> **Text-to-image → diffusion model**. This is a direct association the exam tests.  
> Don't confuse with GANs — while GANs can generate images, diffusion models are the modern standard for text-to-image generation and the expected answer on the AIF-C01.
