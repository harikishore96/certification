# Domain 2: Fundamentals of Generative AI (24%)

## Domain Overview
- **Weight**: 24% of exam (HIGHEST weight)
- **Focus**: Foundation models, generative AI concepts, prompt engineering, model capabilities
- **Key Topics**: LLMs, foundation models, prompt engineering, model customization, generative AI applications

## Task Statements

### Task 2.1: Explain basic concepts of generative AI
**Weight**: Very High  
**Skills**:
- Define generative AI and foundation models
- Explain how LLMs work
- Understand pre-training and fine-tuning
- Describe model parameters and context windows
- Explain tokens and tokenization

**Key Concepts**:
- **Generative AI**: Creates new content (text, images, code, audio)
- **Foundation Models**: Large pre-trained models, general-purpose
- **LLMs**: Language-focused foundation models
- **Pre-training**: Initial training on massive datasets
- **Tokens**: Units of text (words, subwords, characters)
- **Context Window**: Maximum input length model can process
- **Parameters**: Learned weights (billions to trillions)

**AWS Services**:
- Amazon Bedrock (foundation models)
- Amazon Nova (multimodal models)
- Amazon Titan (AWS foundation models)

**Study Resources**:
- [Amazon Bedrock](../Generative-AI-Services/Amazon-Bedrock.md)
- [Amazon Nova](../Generative-AI-Services/Amazon-Nova.md)

---

### Task 2.2: Understand capabilities of foundation models
**Weight**: Very High  
**Skills**:
- Identify what foundation models can do
- Understand multimodal capabilities
- Recognize limitations and constraints
- Understand model selection criteria
- Know different model families (Claude, Titan, Llama, etc.)

**Key Concepts**:
- **Text Generation**: Content creation, summarization, Q&A
- **Code Generation**: Write, explain, debug code
- **Multimodal**: Process text + images together
- **Reasoning**: Logical thinking, problem-solving
- **Translation**: Language conversion
- **Extraction**: Pull information from documents
- **Limitations**: Hallucinations, knowledge cutoff, context limits

**Model Families**:
- **Anthropic Claude**: Advanced reasoning, long context
- **Amazon Titan**: AWS-built, cost-effective
- **Meta Llama**: Open-source, customizable
- **AI21 Jurassic**: Multilingual
- **Cohere**: Enterprise-focused
- **Stability AI**: Image generation

**AWS Services**:
- Amazon Bedrock (all foundation models)
- Amazon Nova (multimodal)

**Study Resources**:
- [Amazon Bedrock](../Generative-AI-Services/Amazon-Bedrock.md)
- [Amazon Nova](../Generative-AI-Services/Amazon-Nova.md)

---

### Task 2.3: Understand prompt engineering techniques
**Weight**: Very High  
**Skills**:
- Design effective prompts
- Use few-shot learning
- Apply chain-of-thought prompting
- Understand system prompts
- Optimize prompt structure

**Key Concepts**:
- **Zero-Shot**: No examples, just instruction
- **Few-Shot**: Include examples in prompt
- **Chain-of-Thought**: Step-by-step reasoning
- **System Prompts**: Set behavior and context
- **Prompt Structure**: Clear instructions, context, examples, constraints
- **Temperature**: Control randomness (0=deterministic, 1=creative)
- **Top-P**: Nucleus sampling for diversity

**Prompt Engineering Techniques**:
1. Be specific and clear
2. Provide context
3. Use examples (few-shot)
4. Request step-by-step reasoning
5. Set constraints and format
6. Iterate and refine

**AWS Services**:
- Amazon Bedrock (prompt engineering)
- Bedrock Prompt Management (store/version prompts)

**Study Resources**:
- [Amazon Bedrock](../Generative-AI-Services/Amazon-Bedrock.md) - Section on Prompt Engineering

---

### Task 2.4: Understand foundation model customization
**Weight**: High  
**Skills**:
- Differentiate customization approaches
- Understand fine-tuning vs. continued pre-training
- Know when to use RAG vs. fine-tuning
- Understand prompt engineering as customization
- Recognize trade-offs between approaches

**Key Concepts**:

**Customization Hierarchy** (Least to Most Effort):
1. **Prompt Engineering**: Modify prompts (no training)
2. **RAG**: Add external knowledge (no training)
3. **Fine-Tuning**: Train on labeled task-specific data
4. **Continued Pre-Training**: Train on domain-specific unlabeled data

**Comparison**:
| Approach | Training | Data Needed | Cost | Use Case |
|----------|----------|-------------|------|----------|
| Prompt Engineering | No | None | Lowest | Task adaptation |
| RAG | No | Documents | Low | Add knowledge |
| Fine-Tuning | Yes | Labeled | Medium | Task specialization |
| Continued Pre-Training | Yes | Unlabeled | High | Domain adaptation |

**AWS Services**:
- Bedrock (fine-tuning, continued pre-training)
- Bedrock Knowledge Bases (RAG)
- Bedrock Agents (RAG + tool use)

**Study Resources**:
- [Amazon Bedrock](../Generative-AI-Services/Amazon-Bedrock.md) - Customization section

---

## Domain Summary

### Key Focus Areas
1. **Foundation Model Basics**: What they are, how they work, capabilities
2. **Prompt Engineering**: Designing effective prompts, few-shot learning
3. **Model Customization**: RAG, fine-tuning, continued pre-training
4. **Model Selection**: Choosing appropriate models for use cases
5. **Limitations**: Hallucinations, biases, context limits

### Common Question Types
- Define generative AI concepts
- Identify appropriate foundation models for use cases
- Select correct prompt engineering technique
- Choose customization approach (RAG vs. fine-tuning)
- Recognize model capabilities and limitations

### Study Strategy
1. Understand what foundation models are and how they differ from traditional ML
2. Master prompt engineering techniques
3. Know when to use each customization approach
4. Learn model families and their strengths
5. Understand limitations (hallucinations, biases)

### AWS Services to Know
- **Amazon Bedrock**: Primary service (foundation models)
- **Amazon Nova**: AWS multimodal models
- **Amazon Titan**: AWS foundation models
- **Bedrock Knowledge Bases**: RAG implementation
- **Bedrock Agents**: Autonomous task execution
- **Bedrock Guardrails**: Content filtering

### Exam Tips
- This domain has HIGHEST weight (24%) - prioritize study time
- Focus on practical applications, not just theory
- Understand prompt engineering deeply
- Know when to use RAG vs. fine-tuning
- Recognize model selection criteria
- Understand limitations and responsible use
