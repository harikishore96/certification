# AWS Custom Hardware for AI/ML

## 1. Service/Topic Overview
- **Purpose**: AWS designs custom silicon (Trainium, Inferentia, Graviton) and offers GPU instances to provide high-performance, cost-efficient compute for AI/ML training and inference workloads
- **Service Level**: Regional (EC2 instance availability varies by region)
- **Key Use Cases**:
  - Training large foundation models and LLMs (Trainium, P-series GPUs)
  - Cost-efficient inference at scale (Inferentia)
  - Lightweight ML inference on CPU (Graviton)
  - Multi-modal and generative AI workloads (Trainium2/3, P6)
- **Exam Weight**: Medium
- **Exam Domain**: 
  - Domain 1: Fundamentals of AI and ML (20%)
  - Domain 3: Applications of Foundation Models (28%)
- **Task Statements**: Understanding compute infrastructure for ML training and inference; selecting appropriate hardware for AI workloads

---

## 2. Exam Keyword Mapping

### Trigger Words
| Keyword | Points To |
|---------|-----------|
| "cost-effective training" | AWS Trainium / Trn instances |
| "lowest cost inference" | AWS Inferentia / Inf instances |
| "custom AI chip" / "purpose-built chip" | Trainium or Inferentia |
| "GPU training" / "NVIDIA" | P-series instances (P5, P6) |
| "GPU inference" | G-series instances (G5, G6, G7e) |
| "Neuron SDK" | Trainium + Inferentia software stack |
| "lightweight inference" / "CPU inference" | Graviton |
| "UltraServer" | Multi-instance Trainium clusters |
| "NeuronLink" | Chip-to-chip interconnect (Trn2/Trn3) |
| "Elastic Fabric Adapter (EFA)" | High-speed networking for distributed training |

### Scenario Indicators
- "Reduce training costs for deep learning models" → Trainium (Trn1/Trn2)
- "Deploy inference at lowest cost" → Inferentia (Inf2)
- "Train trillion-parameter models" → Trn2 UltraServers or P6 instances
- "Run small language models cost-efficiently on CPU" → Graviton
- "Need NVIDIA CUDA compatibility" → P-series or G-series GPU instances

### Anti-patterns
- ❌ Don't choose Trainium/Inferentia when CUDA-specific libraries are required
- ❌ Don't choose GPU instances when cost is the primary concern and workload is compatible with Neuron SDK
- ❌ Don't choose Inferentia for training (it's inference-optimized; Inferentia2 supports some training but Trainium is the primary training chip)
- ❌ Don't choose Graviton for large model training

---

## 3. Core Concepts

### AWS Custom AI Chip Family

```
AWS AI Silicon
├── Trainium (Training + Inference)
│   ├── Trainium1 → EC2 Trn1 instances
│   ├── Trainium2 → EC2 Trn2 instances + Trn2 UltraServers
│   └── Trainium3 → EC2 Trn3 UltraServers (3nm chip)
├── Inferentia (Inference-optimized)
│   ├── Inferentia1 → EC2 Inf1 instances
│   └── Inferentia2 → EC2 Inf2 instances
├── Graviton (General-purpose ARM CPU)
│   ├── Graviton3 → ML inference on CPU
│   └── Graviton4 → Latest generation
└── Neuron SDK (Software stack for Trainium + Inferentia)
```

### GPU Instance Families (NVIDIA)

| Family | Purpose | GPU | Use Case |
|--------|---------|-----|----------|
| **P6** | Training + Inference | NVIDIA Blackwell B200 | Largest FM training, distributed AI |
| **P5/P5en** | Training + Inference | NVIDIA H100/H200 | Large-scale training |
| **P4d** | Training + Inference | NVIDIA A100 | General ML training |
| **G7e** | Inference + Graphics | NVIDIA RTX PRO 6000 Blackwell | LLM inference, spatial computing |
| **G6/G6e** | Inference + Graphics | NVIDIA L4/L40S | Cost-efficient inference |
| **G5** | Inference + Graphics | NVIDIA A10G | General inference |

### Data Flow

```
Training:
  Data (S3) → EC2 Trn/P instances → Model Artifacts (S3)
                    ↑
              Neuron SDK / CUDA
              
Inference:
  Request → EC2 Inf/G instances → Response
                  ↑
            Neuron SDK / CUDA
            Compiled Model
```

### Integration Points
- **Amazon SageMaker**: Managed training/inference with Trn, Inf, P, G instances
- **SageMaker HyperPod**: Resilient large-scale training clusters
- **Amazon EKS/ECS**: Container-based ML workloads with Neuron device plugin
- **AWS ParallelCluster**: HPC cluster management for training
- **AWS Batch**: Batch ML job scheduling
- **EC2 Capacity Blocks for ML**: Reserved GPU/accelerator capacity
- **EC2 UltraClusters**: Scale to tens of thousands of GPUs/chips

---

## 4. Key Features & Components

### AWS Trainium — Purpose-Built for Training

#### Trainium1 (Trn1 Instances)
- Up to 16 Trainium chips per instance
- Up to 3 PFLOPs FP8 compute
- 512 GB HBM, 9.8 TB/s memory bandwidth
- Up to 1.6 Tbps EFA networking
- **Up to 50% lower training costs** vs comparable GPU instances

#### Trainium2 (Trn2 Instances)
- 16 Trainium2 chips per instance
- **20.8 PFLOPs FP8** compute (4x over Trn1)
- 1.5 TB HBM, 46 TB/s memory bandwidth
- 3.2 Tbps EFA v3 networking
- **30-40% better price-performance** vs GPU P5e/P5en
- **Trn2 UltraServers**: Up to 64 Trainium2 chips connected via NeuronLink

#### Trainium3 (Trn3 UltraServers)
- AWS's first **3nm AI chip**
- 2.52 PFLOPs FP8 per chip (2x over Trainium2)
- 144 GB HBM3e per chip, 4.9 TB/s memory bandwidth
- Trn3 UltraServer: up to 144 chips, 20.7 TB HBM3e, 362 MXFP8 PFLOPs
- **4.4x higher performance** and **4x better energy efficiency** vs Trn2 UltraServers
- Advanced data types: MXFP8, MXFP4
- Hardware optimizations for 4x sparsity (16:4)
- Designed for agentic, reasoning, and video generation workloads

### AWS Inferentia — Purpose-Built for Inference

#### Inferentia1 (Inf1 Instances)
- 4 NeuronCores per chip, up to 16 chips per instance
- 8 GB DDR4 memory per chip
- Supports FP16, BF16, INT8
- **Up to 2.3x higher throughput**, **up to 70% lower cost** vs comparable EC2

#### Inferentia2 (Inf2 Instances)
- 2 NeuronCores-v2 per chip, up to 12 chips per instance
- 32 GB HBM per chip (4x more memory than Inf1)
- 190 TFLOPS FP16 per chip
- **4x higher throughput**, **10x lower latency** vs Inf1
- Supports FP32, TF32, BF16, FP16, FP8 (cFP8), INT8
- **First inference instances with scale-out distributed inference** (chip-to-chip connectivity)
- Supports LLMs and latent diffusion models
- **50% better performance/watt** vs comparable instances

### AWS Neuron SDK

The unified software stack for Trainium and Inferentia:

| Component | Purpose |
|-----------|---------|
| **Neuron Compiler** | Compiles models for Trainium/Inferentia hardware |
| **Neuron Runtime** | Executes compiled models on chips |
| **Neuron Kernel Interface (NKI)** | Low-level kernel programming (direct ISA access) |
| **Neuron Kernel Library** | Pre-optimized open-source kernels |
| **Neuron Explorer** | Profiling, debugging, AI-powered optimization insights |
| **Framework Integrations** | PyTorch, JAX, TensorFlow, vLLM, HuggingFace |

Key Neuron SDK features:
- **Native PyTorch/JAX integration** — run existing code unchanged
- **torch.compile support** for Trainium
- **vLLM V1 APIs** for inference serving
- **Distributed training**: FSDP, DDP, DTensor
- **Open-source**: NKI Compiler (MLIR-based), Kernel Library, Explorer
- **Automatic mixed-precision casting** (FP32 → lower precision)

### AWS Graviton for ML Inference
- ARM-based processors designed by AWS
- Best for **lightweight/small model inference** (XGBoost, small NLP, SLMs)
- Up to **60% cost savings** for ML inference (Warner Bros. Discovery case study)
- Supports SageMaker inference endpoints
- Energy-efficient alternative to GPU for suitable workloads

### GPU Instances — NVIDIA Partnership

#### P6-B200 (Latest — 2025)
- 8 NVIDIA Blackwell B200 GPUs
- 1,440 GB high-bandwidth GPU memory
- 5th Gen Intel Xeon (Emerald Rapids)
- 3.2 Tbps EFA v4 networking
- **2x performance of P5en**
- Available via EC2 Capacity Blocks for ML

#### EC2 UltraClusters
- Scale to **tens of thousands of GPUs**
- Tightly coupled with high-bandwidth, low-latency interconnect
- For the most demanding distributed training workloads

---

## 5. Exam Focus Areas

### Common Question Types

**Scenario: "Which instance type for cost-effective training?"**
→ AWS Trainium (Trn1/Trn2) — purpose-built, lowest cost for training

**Scenario: "Which instance type for lowest cost inference?"**
→ AWS Inferentia (Inf2) — purpose-built, lowest cost for inference

**Scenario: "Need NVIDIA GPU compatibility?"**
→ P-series (training) or G-series (inference)

**Scenario: "What SDK for Trainium/Inferentia?"**
→ AWS Neuron SDK

### Gotchas
- ⚠️ Trainium is for **both training AND inference** (not just training despite the name)
- ⚠️ Inferentia is **inference-only** (despite Inf2 having some training capability, exam focuses on inference)
- ⚠️ Neuron SDK is **not** for GPU instances — GPUs use CUDA/cuDNN
- ⚠️ Trainium/Inferentia require model compilation via Neuron — not all models/ops are supported out of the box
- ⚠️ EC2 Capacity Blocks for ML are for **reserving** GPU/accelerator capacity (not on-demand)
- ⚠️ UltraServers connect multiple instances via NeuronLink (Trainium) — different from UltraClusters (GPU)

### Comparison Points

| Feature | Trainium | Inferentia | NVIDIA GPU (P/G) | Graviton |
|---------|----------|------------|-------------------|----------|
| **Primary Use** | Training + Inference | Inference | Training + Inference | CPU Inference |
| **Cost** | Lowest for training | Lowest for inference | Higher | Lowest for CPU workloads |
| **SDK** | Neuron | Neuron | CUDA/cuDNN | Standard CPU |
| **Framework Lock-in** | Neuron compilation needed | Neuron compilation needed | CUDA ecosystem | None |
| **Model Size** | Up to 1T+ params | LLMs, diffusion models | Unlimited | Small models |
| **Custom by** | AWS | AWS | NVIDIA | AWS |

### Decision Tree

```
Need ML Compute?
├── Training workload?
│   ├── Cost is priority + Neuron compatible → Trainium (Trn1/Trn2)
│   ├── Need CUDA/NVIDIA ecosystem → P-series GPU
│   └── Largest scale (1T+ params) → Trn2 UltraServer or P6
├── Inference workload?
│   ├── Cost is priority + Neuron compatible → Inferentia (Inf2)
│   ├── Need CUDA/NVIDIA → G-series GPU
│   ├── Small model / CPU sufficient → Graviton
│   └── LLM serving at scale → Inf2 or Trn2
└── Both training + inference?
    ├── Want single chip family → Trainium
    └── Want NVIDIA ecosystem → P-series + G-series
```

---

## 6. Best Practices

### Security
- Use **VPC** for network isolation of training/inference instances
- **IAM roles** for EC2 instances accessing S3 data and model artifacts
- **Encryption at rest** (EBS encryption) and **in transit** (TLS)
- **Security groups** to restrict access to inference endpoints
- Use **AWS PrivateLink** for private inference endpoints

### Cost Optimization
- **Trainium over GPU** for compatible training workloads (30-50% savings)
- **Inferentia over GPU** for inference (up to 70% lower cost)
- **EC2 Capacity Blocks for ML** to reserve accelerator capacity
- **Spot Instances** for fault-tolerant training (SageMaker Managed Spot Training)
- **Graviton** for lightweight inference (up to 60% savings)
- **Right-size instances** — use SageMaker Inference Recommender
- **Savings Plans** for sustained workloads

### Performance
- Use **EFA** for distributed training (high-bandwidth, low-latency networking)
- **UltraServers** for multi-node training with NeuronLink
- **Mixed precision** (FP8, BF16) for faster training with minimal accuracy loss
- **Neuron Kernel Interface (NKI)** for custom kernel optimization
- **vLLM** on Neuron for optimized LLM serving
- **Speculative decoding** and **Expert Parallelism** for inference throughput

### Operations
- **CloudWatch** for monitoring accelerator utilization
- **Neuron Explorer** for profiling and debugging on Trainium/Inferentia
- **SageMaker HyperPod** for resilient training clusters (auto-recovery)
- **Deep Learning AMIs (DLAMI)** for pre-configured environments
- **Deep Learning Containers (DLC)** for containerized workloads

---

## 7. Hands-On Labs

### Quick Setup: Launch an Inf2 Instance
```bash
# Launch an Inf2 instance with Neuron DLAMI
aws ec2 run-instances \
  --instance-type inf2.xlarge \
  --image-id <neuron-dlami-ami-id> \
  --key-name <your-key-pair> \
  --security-group-ids <sg-id> \
  --subnet-id <subnet-id>
```

### Practical Exercises
1. **Deploy a model on Inferentia2**: Compile a HuggingFace model with Neuron SDK and deploy on Inf2
2. **Compare costs**: Run the same inference workload on Inf2 vs G5 and compare throughput/cost
3. **SageMaker with Trainium**: Launch a SageMaker training job using Trn1 instance type

### Key CLI Commands
```bash
# Check Neuron devices on an instance
neuron-ls

# Monitor Neuron device utilization
neuron-top

# Compile a model for Inferentia2
neuron_cc compile --target inf2

# List available Capacity Blocks
aws ec2 describe-capacity-block-offerings \
  --instance-type p5.48xlarge \
  --capacity-duration-hours 24
```

---

## 8. Sample Questions

### Question 1
A company wants to deploy a large language model for real-time inference at the lowest possible cost. The model is compatible with PyTorch. Which EC2 instance type should they choose?

A) Amazon EC2 P5 instances with NVIDIA H100 GPUs  
B) Amazon EC2 Inf2 instances powered by AWS Inferentia2  
C) Amazon EC2 G5 instances with NVIDIA A10G GPUs  
D) Amazon EC2 C7g instances powered by AWS Graviton3  

**Correct Answer: B**

**Explanation**: Inf2 instances powered by AWS Inferentia2 are purpose-built for inference and deliver the lowest cost per inference for deep learning and generative AI workloads. They support LLMs with scale-out distributed inference. The Neuron SDK integrates natively with PyTorch, so no code changes are needed.
- A is wrong: P5 instances are high-performance but significantly more expensive; they're optimized for training.
- C is wrong: G5 instances are GPU-based and cost more than Inferentia for inference workloads.
- D is wrong: Graviton is CPU-based and not suitable for large LLM inference.

---

### Question 2
A machine learning team needs to train a 500-billion parameter foundation model with the best price-performance. They don't require NVIDIA CUDA compatibility. Which AWS solution is most appropriate?

A) Amazon EC2 P6-B200 instances  
B) Amazon EC2 Trn2 UltraServers with AWS Trainium2  
C) Amazon EC2 Inf2 instances with AWS Inferentia2  
D) Amazon SageMaker with Graviton-based instances  

**Correct Answer: B**

**Explanation**: Trn2 UltraServers connect up to 64 Trainium2 chips via NeuronLink, providing massive compute for training models up to 1T+ parameters. They offer 30-40% better price-performance than GPU-based P5e/P5en instances. Since CUDA is not required, Trainium is the best cost-performance choice.
- A is wrong: P6 instances are powerful but more expensive; the team doesn't need CUDA.
- C is wrong: Inferentia2 is optimized for inference, not large-scale training.
- D is wrong: Graviton is a CPU processor, not suitable for training large foundation models.

---

### Question 3
What is the AWS Neuron SDK used for?

A) Managing NVIDIA GPU drivers on EC2 instances  
B) Compiling and deploying models on AWS Trainium and Inferentia chips  
C) Automating SageMaker training pipelines  
D) Monitoring EC2 instance health and performance  

**Correct Answer: B**

**Explanation**: AWS Neuron SDK is the developer stack specifically designed for running deep learning and generative AI workloads on AWS Trainium and Inferentia chips. It includes a compiler, runtime, training/inference libraries, and developer tools. It integrates natively with PyTorch, JAX, and TensorFlow.
- A is wrong: NVIDIA GPU drivers are managed separately (CUDA/cuDNN); Neuron is for AWS custom chips only.
- C is wrong: SageMaker Pipelines handles ML workflow automation, not Neuron.
- D is wrong: CloudWatch and Neuron Monitor handle monitoring; the SDK's primary purpose is model compilation and deployment.

---

## Quick Reference Card

| Chip | Instance | Optimized For | Key Metric |
|------|----------|---------------|------------|
| **Trainium1** | Trn1 | Training | 50% lower training cost |
| **Trainium2** | Trn2 | Training + Inference | 30-40% better price-perf vs GPU |
| **Trainium3** | Trn3 | Training + Inference | 4.4x perf over Trn2 UltraServer |
| **Inferentia1** | Inf1 | Inference | 70% lower cost, 2.3x throughput |
| **Inferentia2** | Inf2 | Inference | 4x throughput, 10x lower latency vs Inf1 |
| **Graviton3/4** | C7g/M7g | CPU Inference | Up to 60% cost savings |
| **NVIDIA B200** | P6 | Training + Inference | 2x perf over P5en |
| **NVIDIA H100** | P5 | Training + Inference | Large-scale GPU training |

**Exam Tip**: The exam tests whether you know WHEN to use AWS custom chips (Trainium/Inferentia) vs NVIDIA GPUs. The key differentiator is **cost** — AWS custom chips are cheaper for compatible workloads, while NVIDIA GPUs offer broader ecosystem compatibility (CUDA). Remember: Trainium = Training focus, Inferentia = Inference focus, Neuron SDK = the software that makes them work.
