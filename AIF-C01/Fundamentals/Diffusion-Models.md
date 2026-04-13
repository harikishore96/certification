# Diffusion Models

## 1. Topic Overview
- **Purpose**: Diffusion models are a class of generative AI models that create new data (primarily images) by learning to reverse a gradual noising process — starting from pure random noise and iteratively denoising it into a coherent output guided by a conditioning signal (e.g., text prompt)
- **Category**: Generative AI Architecture / Deep Learning
- **Key Use Cases**:
  - Text-to-image generation (e.g., Amazon Titan Image Generator, Stable Diffusion, Amazon Nova Canvas)
  - Image editing (inpainting, outpainting, background removal)
  - Image-to-image transformation (style transfer, variations)
  - Video generation (e.g., Amazon Nova Reel)
  - Image conditioning and subject consistency
- **Exam Weight**: Medium-High
- **Exam Domain**:
  - Domain 2: Fundamentals of Generative AI (24%) — understanding how generative models work
  - Domain 3: Applications of Foundation Models (28%) — image generation use cases on AWS
- **Task Statements**:
  - Task 2.1: Explain the basic concepts of generative AI
  - Task 2.2: Understand the capabilities and limitations of generative AI for solving business problems
  - Task 3.2: Choose effective prompt engineering techniques

## 2. Exam Keyword Mapping
- **Trigger Words**: "image generation", "text-to-image", "denoising", "noise-to-image", "iterative refinement", "Stable Diffusion", "Titan Image Generator", "Nova Canvas", "CFG scale", "seed value", "negative prompt", "inpainting", "outpainting"
- **Scenario Indicators**:
  - "Generate realistic images from text descriptions" → Diffusion model (Titan Image Generator / Nova Canvas / Stable Diffusion on Bedrock)
  - "Edit specific parts of an image" → Diffusion model with inpainting/outpainting
  - "Control image style while preserving composition" → Image conditioning with diffusion
  - "Reproducible image generation" → Seed values in diffusion models
  - "Exclude unwanted elements from generated images" → Negative prompts
- **Anti-patterns**:
  - ❌ Text generation → Use LLMs (Transformers), not diffusion models
  - ❌ Image classification/detection → Use Amazon Rekognition, not diffusion models
  - ❌ Extracting text from images → Use Amazon Textract
  - ❌ Real-time object detection in video → Use Rekognition Video

## 3. Core Concepts

### How Diffusion Models Work

```
Forward Process (Training):
Clean Image → Add Noise Step 1 → Add Noise Step 2 → ... → Pure Random Noise

Reverse Process (Inference/Generation):
Pure Random Noise → Denoise Step 1 → Denoise Step 2 → ... → Generated Image
                         ↑                  ↑
                    Text Prompt          Text Prompt
                   (conditioning)       (conditioning)
```

**Two-Phase Process**:

1. **Forward Diffusion (Training Phase)**:
   - Gradually adds Gaussian noise to training images over many steps
   - At each step, a small amount of noise is added until the image becomes pure noise
   - The model learns the amount of noise added at each step

2. **Reverse Diffusion (Inference/Generation Phase)**:
   - Starts with pure random noise (static image)
   - Iteratively removes noise in learned steps (denoising)
   - Text prompt is encoded as a numerical vector and used as conditioning signal
   - The prompt vector is matched against similar vectors in a joint text/image embedding space
   - These vectors guide the transformation from noise to a coherent image
   - Final output is upscaled to full resolution

### Key Technical Components

| Component | Role |
|-----------|------|
| **Text Encoder** | Converts text prompt into numerical vector representation |
| **Noise Predictor (U-Net / Transformer)** | Predicts and removes noise at each denoising step |
| **Scheduler** | Controls the denoising step schedule and noise levels |
| **VAE Decoder** | Converts latent representation to full-resolution pixel image |
| **CLIP / Embedding Model** | Aligns text and image in a shared embedding space |

### Latent Diffusion Models (LDMs)
- Modern diffusion models (Stable Diffusion, Titan Image Generator) operate in **latent space** rather than pixel space
- Images are compressed to a lower-dimensional latent representation → noise is added/removed in this space → decoded back to pixel space
- **Benefit**: Dramatically reduces computational cost while maintaining quality
- This is why they're called "Latent Diffusion Models"

### Diffusion Models vs Other Generative Architectures

| Feature | Diffusion Models | GANs | VAEs | Transformers (LLMs) |
|---------|-----------------|------|------|---------------------|
| **Primary Output** | Images, video | Images | Images | Text, code |
| **Training Stability** | Very stable | Unstable (mode collapse) | Stable | Stable |
| **Output Quality** | Excellent | Good-Excellent | Moderate | N/A (text) |
| **Diversity** | High | Can suffer mode collapse | High | High |
| **Speed** | Slower (iterative) | Fast (single pass) | Fast | Varies |
| **Controllability** | Excellent (prompts, CFG) | Limited | Limited | Excellent (prompts) |
| **AWS Example** | Titan Image Generator, Nova Canvas | — | — | Titan Text, Claude |

### Data Flow
```
Input:
  Text Prompt → Text Encoder → Embedding Vector
  (Optional) Reference Image → Image Encoder → Conditioning Signal
  (Optional) Negative Prompt → Text Encoder → Negative Embedding
  Seed Value → Random Noise Generator → Initial Noise

Processing:
  Initial Noise + Embedding + Conditioning → Iterative Denoising (N steps)
  → Classifier-Free Guidance (CFG) applied at each step
  → Latent Representation

Output:
  Latent Representation → VAE Decoder → Full Resolution Image
  → Safety Filters → Watermark (Titan) → Final Image
```

## 4. Key Features & Components

### Critical Parameters for Exam

| Parameter | Description | Impact |
|-----------|-------------|--------|
| **Prompt** | Text description of desired image | Guides what the model generates |
| **Negative Prompt** | Elements to exclude from output | Avoids unwanted artifacts (e.g., "blurry", "distorted", "extra limbs") |
| **CFG Scale (Classifier-Free Guidance)** | Controls prompt adherence (typically 1-30) | Low = more creative freedom; High = strict prompt following |
| **Seed** | Random number controlling initial noise | Same seed + same prompt = reproducible output |
| **Steps / Inference Steps** | Number of denoising iterations | More steps = higher quality but slower |
| **Strength** | How much to transform reference image (image-to-image) | 0 = no change; 1 = complete regeneration |
| **Aspect Ratio** | Output image dimensions | Controls composition layout |

### Classifier-Free Guidance (CFG) Scale — Exam Important
- **Low CFG (1-5)**: Model has more creative freedom, may deviate from prompt
- **Medium CFG (7-12)**: Balanced — good prompt adherence with some creativity
- **High CFG (15-30)**: Strict adherence to prompt, may reduce image quality/naturalness
- **Exam Tip**: CFG scale is a key parameter that controls the trade-off between creativity and prompt fidelity

### Seed Values — Exam Important
- Deterministic: Same seed + same prompt + same parameters = same image
- Essential for **reproducibility** in professional/production workflows
- Different seeds produce different variations of the same prompt

### Image Generation Capabilities on AWS

| Capability | Description |
|------------|-------------|
| **Text-to-Image** | Generate new image from text prompt |
| **Image-to-Image** | Transform existing image guided by text prompt |
| **Inpainting** | Replace/remove specific regions within an image using a mask |
| **Outpainting** | Extend image borders with new generated content |
| **Background Removal** | Automatically segment and remove backgrounds |
| **Image Conditioning** | Use reference image to guide composition/structure |
| **Color Palette Control** | Specify hex color values to control output palette |
| **Style Transfer** | Apply artistic style from reference images |
| **Subject Consistency** | Preserve specific objects across generated images (via fine-tuning) |

### AWS Services Using Diffusion Models

| Service/Model | Type | Key Details |
|---------------|------|-------------|
| **Amazon Titan Image Generator** | AWS proprietary | Text-conditioned diffusion model; includes watermarking, IP indemnity; available via Bedrock |
| **Amazon Nova Canvas** | AWS proprietary | Diffusion-based; part of Nova creative suite; text-to-image, editing, conditioning |
| **Amazon Nova Reel** | AWS proprietary | Video generation from text/image prompts |
| **Stable Diffusion 3.5 Large** | Stability AI (3rd party) | 8B parameters; 1MP resolution; available via Bedrock |
| **Stable Image Ultra** | Stability AI (3rd party) | Powered by SD 3.5; best quality; available via Bedrock |
| **Stable Image Core** | Stability AI (3rd party) | Enhanced SDXL; fast and cost-effective; available via Bedrock |

### Prompting Best Practices for Diffusion Models
- Structure prompts as **image captions**, not commands (e.g., "a sunset over mountains" not "generate a sunset")
- Include specific details: subject, style, lighting, perspective, mood, composition
- Use **positive prompts** for what to include
- Use **negative prompts** for what to exclude (avoid negation words like "no", "not" in positive prompts)
- Be specific: "a majestic snow-capped mountain peak at sunset with dramatic lighting" > "a mountain"

## 5. Exam Focus Areas

### Common Question Types
- **Scenario-based**: "A company wants to generate product images from text descriptions. Which AWS service should they use?" → Amazon Titan Image Generator / Nova Canvas via Bedrock
- **Parameter understanding**: "What does increasing the CFG scale do?" → Increases prompt adherence, reduces creative freedom
- **Architecture understanding**: "How do diffusion models generate images?" → Iterative denoising from random noise, guided by text conditioning
- **Comparison**: "How do diffusion models differ from GANs?" → More stable training, iterative process, better controllability

### Gotchas
- ⚠️ Diffusion models are **not** the same as GANs — they use iterative denoising, not generator-discriminator adversarial training
- ⚠️ Diffusion models operate in **latent space** (compressed), not directly on pixels
- ⚠️ Negative prompts work differently than negation in text — don't use "no" or "not" in positive prompts; use the dedicated negative prompt field
- ⚠️ Higher CFG scale doesn't always mean better — too high can produce artifacts and reduce quality
- ⚠️ Titan Image Generator includes **invisible watermarking** for content provenance — this is a responsible AI feature
- ⚠️ Diffusion models are trained on image-caption pairs, not 3D models or real-world physics — they may produce physically impossible scenes
- ⚠️ More inference steps = higher quality but slower and more expensive

### Comparison Points

| Criteria | Diffusion Models | GANs | Transformers (for images) |
|----------|-----------------|------|--------------------------|
| Training | Stable, well-defined loss | Unstable, adversarial | Stable, attention-based |
| Generation | Iterative (slow) | Single forward pass (fast) | Autoregressive or parallel |
| Quality | State-of-the-art | High but inconsistent | Emerging |
| Controllability | Excellent (CFG, prompts) | Limited | Good |
| Mode Collapse | No | Yes (common problem) | No |

### Decision Tree
```
Need to GENERATE images from text?
  ├── Yes → Diffusion Model
  │   ├── Want AWS-managed with IP indemnity? → Amazon Titan Image Generator
  │   ├── Want latest AWS creative suite? → Amazon Nova Canvas
  │   ├── Want open-source model on AWS? → Stable Diffusion on Bedrock
  │   └── Want full control over model? → Deploy on SageMaker
  └── No
      ├── Need to CLASSIFY/DETECT objects in images? → Amazon Rekognition
      ├── Need to EXTRACT text from images? → Amazon Textract
      └── Need to GENERATE text? → LLM (Transformer-based)
```

## 6. Best Practices

### Security
- **Content filtering**: Titan Image Generator applies pre-processing (prompt) and post-processing (output) safety filters
- **Abuse detection**: Amazon Bedrock implements automated abuse detection — no human review of inputs/outputs
- **CSAM protection**: Hash matching and classifiers detect potential CSAM; reports filed with NCMEC
- **Famous figure safeguards**: Titan blocks generation of identifiable public figures
- **Watermarking**: Titan Image Generator embeds invisible watermarks for content provenance and authenticity
- **IAM access control**: Use Bedrock model access policies to control who can invoke image generation models

### Cost Optimization
- Use **Stable Image Core** for cost-sensitive workloads (half the price of SDXL)
- Use **fewer inference steps** when quality requirements are lower
- Choose appropriate **image resolution** — higher resolution costs more
- Use **batch processing** for bulk image generation
- Consider **Provisioned Throughput** for consistent high-volume workloads
- Stop EC2/SageMaker instances when not in use for custom deployments

### Performance
- **Prompt engineering** is the #1 lever for output quality — invest time in crafting effective prompts
- Use **image conditioning** with reference images to guide composition and reduce iterations
- Adjust **CFG scale** to balance creativity vs. adherence
- Use **seed values** for reproducible A/B testing of prompt variations
- **Fine-tuning** Titan Image Generator on domain-specific data improves effectiveness for specific use cases
- **Instant customization** (up to 5 reference images) is faster than fine-tuning for style transfer

### Operations
- Monitor usage via **Amazon CloudWatch** metrics
- Track costs via **AWS Cost Explorer** with Bedrock service filters
- Use **Amazon Bedrock Guardrails** for additional content filtering layers
- Implement **human-in-the-loop** review for production content pipelines
- Test with representative prompts before production deployment

## 7. Hands-On Labs

### Quick Setup: Text-to-Image with Titan Image Generator
```python
import boto3
import json
import base64

bedrock_runtime = boto3.client("bedrock-runtime", region_name="us-east-1")

body = json.dumps({
    "taskType": "TEXT_IMAGE",
    "textToImageParams": {
        "text": "A modern office space with natural lighting and indoor plants, photorealistic"
    },
    "imageGenerationConfig": {
        "numberOfImages": 1,
        "height": 1024,
        "width": 1024,
        "cfgScale": 8.0,
        "seed": 42
    }
})

response = bedrock_runtime.invoke_model(
    modelId="amazon.titan-image-generator-v2:0",
    body=body
)

result = json.loads(response["body"].read())
image_data = base64.b64decode(result["images"][0])

with open("generated_image.png", "wb") as f:
    f.write(image_data)
```

### Text-to-Image with Stable Diffusion 3.5 Large
```python
body = json.dumps({
    "prompt": "A futuristic cityscape at night with neon lights, cyberpunk style",
    "mode": "text-to-image",
    "aspect_ratio": "16:9",
    "seed": 42,
    "negative_prompt": "blurry, low quality, distorted"
})

response = bedrock_runtime.invoke_model(
    modelId="stability.sd3-5-large-v1:0",
    body=body
)

result = json.loads(response["body"].read())
image_data = base64.b64decode(result["images"][0])
```

### Text-to-Image with Nova Canvas
```python
body = json.dumps({
    "taskType": "TEXT_IMAGE",
    "textToImageParams": {
        "text": "A serene Japanese garden with cherry blossoms and a koi pond, watercolor style",
        "negativeText": "blurry, distorted, low quality"
    },
    "imageGenerationConfig": {
        "numberOfImages": 1,
        "height": 1024,
        "width": 1024,
        "cfgScale": 7.0,
        "seed": 123
    }
})

response = bedrock_runtime.invoke_model(
    modelId="amazon.nova-canvas-v1:0",
    body=body
)
```

### CLI: Invoke Titan Image Generator
```bash
aws bedrock-runtime invoke-model \
    --model-id amazon.titan-image-generator-v2:0 \
    --body '{"taskType":"TEXT_IMAGE","textToImageParams":{"text":"A golden retriever in a park"},"imageGenerationConfig":{"numberOfImages":1,"height":512,"width":512,"cfgScale":8.0}}' \
    --region us-east-1 \
    output.json
```

## 8. Sample Questions

### Question 1
**A marketing team wants to generate product images from text descriptions. They need reproducible results so that the same prompt always generates the same image for approval workflows. Which parameter should they configure?**

A. Increase the CFG scale to maximum  
B. Set a fixed seed value  
C. Use a negative prompt  
D. Increase the number of inference steps  

**Correct Answer: B**

**Explanation**: A fixed seed value ensures that the same prompt with the same parameters produces the same image every time, enabling reproducible results for approval workflows. CFG scale (A) controls prompt adherence, not reproducibility. Negative prompts (C) exclude unwanted elements. More inference steps (D) improve quality but don't ensure reproducibility.

---

### Question 2
**A company is using Amazon Titan Image Generator to create advertising content. They notice the generated images are too creative and don't closely match their detailed text descriptions. What should they adjust?**

A. Decrease the seed value  
B. Add more negative prompts  
C. Increase the classifier-free guidance (CFG) scale  
D. Reduce the number of inference steps  

**Correct Answer: C**

**Explanation**: Increasing the CFG scale makes the model adhere more strictly to the text prompt, reducing creative deviation. A higher CFG value means the generated image will more closely match the text description. Decreasing the seed (A) just changes the random starting point. Negative prompts (B) exclude elements but don't increase adherence. Reducing steps (D) would lower quality.

---

### Question 3
**Which statement correctly describes how diffusion models generate images?**

A. A generator network creates images while a discriminator network evaluates them in an adversarial process  
B. The model starts with random noise and iteratively removes noise guided by text conditioning until a coherent image emerges  
C. The model retrieves and combines existing image fragments from a database to compose new images  
D. An autoregressive transformer predicts image pixels one at a time from left to right  

**Correct Answer: B**

**Explanation**: Diffusion models work by starting with pure random noise and iteratively denoising it, guided by a text prompt encoded as a conditioning signal. Option A describes GANs (Generative Adversarial Networks), not diffusion models. Option C describes a retrieval-based approach. Option D describes autoregressive image generation (like some transformer-based models).

---

**Exam Tip**: For the AIF-C01 exam, focus on understanding the *conceptual* process of diffusion (noise → iterative denoising → image), the key parameters (CFG scale, seed, negative prompts), and which AWS services use diffusion models (Titan Image Generator, Nova Canvas, Stable Diffusion on Bedrock). You don't need to know the mathematical details of the noise schedule or loss functions.
