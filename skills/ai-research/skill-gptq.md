---
id: skill-gptq
type: skill
name: gptq
description: Post-training 4-bit quantization for LLMs with minimal accuracy loss.
  Use for deploying large models (70B, 405B) on consumer GPUs, when you need 4× memory
  reduction with <2% perplexity degradation, or for faster inference (3-4× speedup)
  vs FP16. Integrates with transformers and PEFT for QLoRA fine-tuning.
category: ai-research
complexity: medium
keywords:
- deployment
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 1748
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,748 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# gptq

> Post-training 4-bit quantization for LLMs with minimal accuracy loss. Use for deploying large models (70B, 405B) on consumer GPUs, when you need 4× memory reduction with <2% perplexity degradation, or for faster inference (3-4× speedup) vs FP16. Integrates with transformers and PEFT for QLoRA fine-tuning.

# GPTQ (Generative Pre-trained Transformer Quantization)

Post-training quantization method that compresses LLMs to 4-bit with minimal accuracy loss using group-wise quantization.

## When to use GPTQ

**Use GPTQ when:**
- Need to fit large models (70B+) on limited GPU memory
- Want 4× memory reduction with <2% accuracy loss
- Deploying on consumer GPUs (RTX 4090, 3090)
- Need faster inference (3-4× speedup vs FP16)

**Use AWQ instead when:**
- Need slightly better accuracy (<1% loss)
- Have newer GPUs (Ampere, Ada)
- Want Marlin kernel support (2× faster on some GPUs)

**Use bitsandbytes instead when:**
- Need simple integration with transformers
- Want 8-bit quantization (less compression, better quality)
- Don't need pre-quantized model files

## Quick start

### Installation

```bash
# Install AutoGPTQ
pip install auto-gptq

# With Triton (Linux only, faster)
pip install auto-gptq[triton]

# With CUDA extensions (faster)
pip install auto-gptq --no-build-isolation

# Full installation
pip install auto-gptq transformers accelerate
```

### Load pre-quantized model

```python
from transformers import AutoTokenizer
from auto_gptq import AutoGPTQForCausalLM

# Load quantized model from HuggingFace
model_name = "TheBloke/Llama-2-7B-Chat-GPTQ"

model = AutoGPTQForCausalLM.from_quantized(
    model_name,
    device="cuda:0",
    use_triton=False  # Set True on Linux for speed
)

tokenizer = AutoTokenizer.from_pretrained(model_name)

# Generate
prompt = "Explain quantum computing"
inputs = tokenizer(prompt, return_tensors="pt").to("cuda:0")
outputs = model.generate(**inputs, max_new_tokens=200)
print(tokenizer.decode(outputs[0]))
```

### Quantize your own model

```python
from transformers import AutoTokenizer
from auto_gptq import AutoGPTQForCausalLM, BaseQuantizeConfig
from datasets import load_dataset

# Load model
model_name = "meta-llama/Llama-2-7b-chat-hf"
tokenizer = AutoTokenizer.from_pretrained(model_name)

# Quantization config
quantize_config = BaseQuantizeConfig(
    bits=4,              # 4-bit quantization
    group_size=128,      # Group size (recommended: 128)
    desc_act=False,      # Activation order (False for CUDA kernel)
    damp_percent=0.01    # Dampening factor
)

# Load model for quantization
model = AutoGPTQForCausalLM.from_pretrained(
    model_name,
    quantize_config=quantize_config
)

# Prepare calibration data
dataset = load_dataset("c4", split="train", streaming=True)
calibration_data = [
    tokenizer(example["text"])["input_ids"][:512]
    for example in dataset.take(128)
]

# Quantize
model.quantize(calibration_data)

# Save quantized model
model.save_quantized("llama-2-7b-gptq")
tokenizer.save_pretrained("llama-2-7b-gptq")

# Push to HuggingFace
model.push_to_hub("username/llama-2-7b-gptq")
```

## Group-wise quantization

**How GPTQ works**:
1. **Group weights**: Divide each weight matrix into groups (typically 128 elements)
2. **Quantize per-group**: Each group has its own scale/zero-point
3. **Minimize error**: Uses Hessian information to minimize quantization error
4. **Result**: 4-bit weights with near-FP16 accuracy

**Group size trade-off**:

| Group Size | Model Size | Accuracy | Speed | Recommendation |
|------------|------------|----------|-------|----------------|
| -1 (per-column) | Smallest | Best | Slowest | Research only |
| 32 | Smaller | Better | Slower | High accuracy needed |
| **128** | Medium | Good | **Fast** | **Recommended default** |
| 256 | Larger | Lower | Faster | Speed critical |
| 1024 | Largest | Lowest | Fastest | Not recommended |

**Example**:
```
Weight matrix: [1024, 4096] = 4.2M elements

Group size = 128:
- Groups: 4.2M / 128 = 32,768 groups
- Each group: own 4-bit scale + zero-point
- Result: Better granularity → better accuracy
```

## Quantization configurations

### Standard 4-bit (recommended)

```python
from auto_gptq import BaseQuantizeConfig

config = BaseQuantizeConfig(
    bits=4,              # 4-bit quantization
    group_size=128,      # Standard group size
    desc_act=False,      # Faster CUDA kernel
    damp_percent=0.01    # Dampening factor
)
```

**Performance**:
- Memory: 4× reduction (70B model: 140GB → 35GB)
- Accuracy: ~1.5% perplexity increase
- Speed: 3-4× faster than FP16

### High accuracy (3-bit with larger groups)

```python
config = BaseQuantizeConfig(
    bits=3,              # 3-bit (more compression)
    group_size=128,      # Keep standard group size
    desc_act=True,       # Better accuracy (slower)
    damp_percent=0.01
)
```

**Trade-off**:
- Memory: 5× reduction
- Accuracy: ~3% perplexity increase
- Speed: 5× faster (but less accurate)

### Maximum accuracy (4-bit with small groups)

```python
config = BaseQuantizeConfig(
    bits=4,
    group_size=32,       # Smaller groups (better accuracy)
    desc_act=True,       # Activation reordering
    damp_percent=0.005   # Lower dampening
)
```

**Trade-off**:
- Memory: 3.5× reduction (slightly larger)
- Accuracy: ~0.8% perplexity increase (best)
- Speed: 2-3× faster (kernel overhead)

## Kernel backends

### ExLlamaV2 (default, fastest)

```python
model = AutoGPTQForCausalLM.from_quantized(
    model_name,
    device="cuda:0",
    use_exllama=True,      # Use ExLlamaV2
    exllama_config={"version": 2}
)
```

**Performance**: 1.5-2× faster than Triton

### Marlin (Ampere+ GPUs)

```python
# Quantize with Marlin format
config = BaseQuantizeConfig(
    bits=4,
    group_size=128,
    desc_act=False  # Required for Marlin
)

model.quantize(calibration_data, use_marlin=True)

# Load with Marlin
model = AutoGPTQForCausalLM.from_quantized(
    model_name,
    device="cuda:0",
    use_marlin=True  # 2× faster on A100/H100
)
```

**Requirements**:
- NVIDIA Ampere or newer (A100, H100, RTX 40xx)
- Compute capability ≥ 8.0

### Triton (Linux only)

```python
model = AutoGPTQForCausalLM.from_quantized(
    model_name,
    device="cuda:0",
    use_triton=True  # Linux only
)
```

**Performance**: 1.2-1.5× faster than CUDA backend

## Integration with transformers

### Direct transformers usage

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load quantized model (transformers auto-detects GPTQ)
model = AutoModelForCausalLM.from_pretrained(
    "TheBloke/Llama-2-13B-Chat-GPTQ",
    device_map="auto",
    trust_remote_code=False
)

tokenizer = AutoTokenizer.from_pretrained("TheBloke/Llama-2-13B-Chat-GPTQ")

# Use like any transformers model
inputs = tokenizer("Hello", return_tensors="pt").to("cuda")
outputs = model.generate(**inputs, max_new_tokens=100)
```

### QLoRA fine-tuning (GPTQ + LoRA)

```python
from transformers import AutoModelForCausalLM
from peft import prepare_model_for_kbit_training, LoraConfig, get_peft_model

# Load GPTQ model
model = AutoModelForCausalLM.from_pretrained(
    "TheBloke/Llama-2-7B-GPTQ",
    device_map="auto"
)

# Prepare for LoRA training
model = prepare_model_for_kbit_training(model)

# LoRA config
lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["q_proj", "v_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

# Add LoRA adapters
model = get_peft_model(model, lora_config)

# Fine-tune (memory efficient!)
# 70B model trainable on single A100 80GB
```

## Performance benchmarks

### Memory reduction

| Model | FP16 | GPTQ 4-bit | Reduction |
|-------|------|------------|-----------|
| Llama 2-7B | 14 GB | 3.5 GB | 4× |
| Llama 2-13B | 26 GB | 6.5 GB | 4× |
| Llama 2-70B | 140 GB | 35 GB | 4× |
| Llama 3-405B | 810 GB | 203 GB | 4× |

**Enables**:
- 70B on single A100 80GB (vs 2× A100 needed for FP16)
- 405B on 3× A100 80GB (vs 11× A100 needed for FP16)
- 13B on RTX 4090 24GB (vs OOM with FP16)

### Inference speed (Llama 2-7B, A100)

| Precision | Tokens/sec | vs FP16 |
|-----------|------------|---------|
| FP16 | 25 tok/s | 1× |
| GPTQ 4-bit (CUDA) | 85 tok/s | 3.4× |
| GPTQ 4-bit (ExLlama) | 105 tok/s | 4.2× |
| GPTQ 4-bit (Marlin) | 120 tok/s | 4.8× |

### Accuracy (perplexity on WikiText-2)

| Model | FP16 | GPTQ 4-bit (g=128) | Degradation |
|-------|------|---------------------|-------------|
| Llama 2-7B | 5.47 | 5.55 | +1.5% |
| Llama 2-13B | 4.88 | 4.95 | +1.4% |
| Llama 2-70B | 3.32 | 3.38 | +1.8% |

**Excellent quality preservation** - less than 2% degradation!

## Common patterns

### Multi-GPU deployment

```python
# Automatic device mapping
model = AutoGPTQForCausalLM.from_quantized(
    "TheBloke/Llama-2-70B-GPTQ",
    device_map="auto",  # Automatically split across GPUs
    max_memory={0: "40GB", 1: "40GB"}  # Limit per GPU
)

# Manual device mapping
device_map = {
    "model.embed_tokens": 0,
    "model.layers.0-39": 0,  # First 40 layers on GPU 0
    "model.layers.40-79": 1,  # Last 40 layers on GPU 1
    "model.norm": 1,
    "lm_head": 1
}

model = AutoGPTQForCausalLM.from_quantized(
    model_name,
    device_map=device_map
)
```

### CPU offloading

```python
# Offload some layers to CPU (for very large models)
model = AutoGPTQForCausalLM.from_quantized(
    "TheBloke/Llama-2-405B-GPTQ",
    device_map="auto",
    max_memory={
        0: "80GB",  # GPU 0
        1: "80GB",  # GPU 1
        2: "80GB",  # GPU 2
        "cpu": "200GB"  # Offload overflow to CPU
    }
)
```

### Batch inference

```python
# Process multiple prompts efficiently
prompts = [
    "Explain AI",
    "Explain ML",
    "Explain DL"
]

inputs = tokenizer(prompts, return_tensors="pt", padding=True).to("cuda")

outputs = model.generate(
    **inputs,
    max_new_tokens=100,
    pad_token_id=tokenizer.eos_token_id
)

for i, output in enumerate(outputs):
    print(f"Prompt {i}: {tokenizer.decode(output)}")
```

## Finding pre-quantized models

**TheBloke on HuggingFace**:
- https://huggingface.co/TheBloke
- 1000+ models in GPTQ format
- Multiple group sizes (32, 128)
- Both CUDA and Marlin formats

**Search**:
```bash
# Find GPTQ models on HuggingFace
https://huggingface.co/models?library=gptq
```

**Download**:
```python
from auto_gptq import AutoGPTQForCausalLM

# Automatically downloads from HuggingFace
model = AutoGPTQForCausalLM.from_quantized(
    "TheBloke/Llama-2-70B-Chat-GPTQ",
    device="cuda:0"
)
```

## Supported models

- **LLaMA family**: Llama 2, Llama 3, Code Llama
- **Mistral**: Mistral 7B, Mixtral 8x7B, 8x22B
- **Qwen**: Qwen, Qwen2, QwQ
- **DeepSeek**: V2, V3
- **Phi**: Phi-2, Phi-3
- **Yi, Falcon, BLOOM, OPT**
- **100+ models** on HuggingFace

## References

- **[Calibration Guide](references/calibration.md)** - Dataset selection, quantization process, quality optimization
- **[Integration Guide](references/integration.md)** - Transformers, PEFT, vLLM, TensorRT-LLM
- **[Troubleshooting](references/troubleshooting.md)** - Common issues, performance optimization

## Resources

- **GitHub**: https://github.com/AutoGPTQ/AutoGPTQ
- **Paper**: GPTQ: Accurate Post-Training Quantization (arXiv:2210.17323)
- **Models**: https://huggingface.co/models?library=gptq
- **Discord**: https://discord.gg/autogptq




---


## 📚 Reference Materials


### Calibration

# GPTQ Calibration Guide

Complete guide to calibration data selection and quantization process.

## Calibration Data Selection

### Why calibration matters

Calibration data is used to:
1. **Compute weight importance** (Hessian matrix)
2. **Minimize quantization error** for important weights
3. **Preserve model accuracy** after quantization

**Impact**:
- Good calibration: <1.5% perplexity increase
- Poor calibration: 5-10% perplexity increase
- No calibration: Model may output gibberish

### Dataset size

**Recommended**:
- **128-256 samples** of 512 tokens each
- Total: 65K-131K tokens

**More is not always better**:
- <64 samples: Underfitting (poor quality)
- 128-256 samples: Sweet spot
- >512 samples: Diminishing returns, slower quantization

### Dataset selection by domain

**General purpose models (GPT, Llama)**:
```python
from datasets import load_dataset

# C4 dataset (recommended for general models)
dataset = load_dataset("c4", split="train", streaming=True)
calibration_data = [
    tokenizer(example["text"])["input_ids"][:512]
    for example in dataset.take(128)
]
```

**Code models (CodeLlama, StarCoder)**:
```python
# The Stack dataset
dataset = load_dataset("bigcode/the-stack", split="train", streaming=True)
calibration_data = [
    tokenizer(example["content"])["input_ids"][:512]
    for example in dataset.take(128)
    if example["lang"] == "Python"  # Or your target language
]
```

**Chat models**:
```python
# ShareGPT or Alpaca format
dataset = load_dataset("anon8231489123/ShareGPT_Vicuna_unfiltered", split="train")

calibration_data = []
for example in dataset.select(range(128)):
    # Format as conversation
    conversation = tokenizer.apply_chat_template(
        example["conversations"],
        tokenize=True,
        max_length=512
    )
    calibration_data.append(conversation)
```

**Domain-specific (medical, legal)**:
```python
# Use domain-specific text
dataset = load_dataset("medical_dataset", split="train")
calibration_data = [
    tokenizer(example["text"])["input_ids"][:512]
    for example in dataset.take(256)  # More samples for niche domains
]
```

## Quantization Process

### Basic quantization

```python
from auto_gptq import AutoGPTQForCausalLM, BaseQuantizeConfig
from transformers import AutoTokenizer
from datasets import load_dataset

# 1. Load model
model_name = "meta-llama/Llama-2-7b-hf"
model = AutoGPTQForCausalLM.from_pretrained(
    model_name,
    quantize_config=BaseQuantizeConfig(
        bits=4,
        group_size=128,
        desc_act=False
    )
)
tokenizer = AutoTokenizer.from_pretrained(model_name)

# 2. Prepare calibration data
dataset = load_dataset("c4", split="train", streaming=True)
calibration_data = [
    tokenizer(example["text"])["input_ids"][:512]
    for example in dataset.take(128)
]

# 3. Quantize
model.quantize(calibration_data)

# 4. Save
model.save_quantized("llama-2-7b-gptq")
```

**Time**: ~10-30 minutes for 7B model on A100

### Advanced configuration

```python
config = BaseQuantizeConfig(
    bits=4,                    # 3, 4, or 8 bits
    group_size=128,            # 32, 64, 128, or -1 (per-column)
    desc_act=False,            # Activation order (True = better accuracy, slower)
    damp_percent=0.01,         # Dampening (0.001-0.1, default 0.01)
    static_groups=False,       # Static quantization
    sym=True,                  # Symmetric quantization
    true_sequential=True,      # Sequential quantization (more accurate)
    model_seqlen=2048          # Model sequence length
)
```

**Parameter tuning**:
- `damp_percent`: Lower = more accurate, slower. Try 0.005-0.02.
- `desc_act=True`: 0.5-1% better accuracy, 20-30% slower inference
- `group_size=32`: Better accuracy, slightly larger model

### Multi-GPU quantization

```python
# Quantize on multiple GPUs (faster)
model = AutoGPTQForCausalLM.from_pretrained(
    model_name,
    quantize_config=config,
    device_map="auto",         # Distribute across GPUs
    max_memory={0: "40GB", 1: "40GB"}
)

model.quantize(calibration_data)
```

## Quality Evaluation

### Perplexity testing

```python
from datasets import load_dataset
import torch

# Load test dataset
test_dataset = load_dataset("wikitext", "wikitext-2-raw-v1", split="test")
test_text = "\n\n".join(test_dataset["text"])

# Tokenize
encodings = tokenizer(test_text, return_tensors="pt")
max_length = model.seqlen

# Calculate perplexity
nlls = []
for i in range(0, encodings.input_ids.size(1), max_length):
    begin_loc = i
    end_loc = min(i + max_length, encodings.input_ids.size(1))
    input_ids = encodings.input_ids[:, begin_loc:end_loc].to("cuda")

    with torch.no_grad():
        outputs = model(input_ids, labels=input_ids)
        nll = outputs.loss

    nlls.append(nll)

ppl = torch.exp(torch.stack(nlls).mean())
print(f"Perplexity: {ppl.item():.2f}")
```

**Quality targets**:
- <1.5% increase: Excellent
- 1.5-3% increase: Good
- 3-5% increase: Acceptable for some use cases
- >5% increase: Poor, redo calibration

### Benchmark evaluation

```python
from lm_eval import evaluator

# Evaluate on standard benchmarks
results = evaluator.simple_evaluate(
    model=model,
    tasks=["hellaswag", "mmlu", "arc_challenge"],
    num_fewshot=5
)

print(results["results"])

# Compare to baseline FP16 scores
```

## Optimization Tips

### Improving accuracy

**1. Use more calibration samples**:
```python
# Try 256 or 512 samples
calibration_data = [... for example in dataset.take(256)]
```

**2. Use domain-specific data**:
```python
# Match your use case
if code_model:
    dataset = load_dataset("bigcode/the-stack")
elif chat_model:
    dataset = load_dataset("ShareGPT")
```

**3. Enable activation reordering**:
```python
config = BaseQuantizeConfig(
    bits=4,
    group_size=128,
    desc_act=True  # Better accuracy, slower inference
)
```

**4. Use smaller group size**:
```python
config = BaseQuantizeConfig(
    bits=4,
    group_size=32,  # vs 128
    desc_act=False
)
```

### Reducing quantization time

**1. Use fewer samples**:
```python
# 64-128 samples usually sufficient
calibration_data = [... for example in dataset.take(64)]
```

**2. Disable activation ordering**:
```python
config = BaseQuantizeConfig(
    desc_act=False  # Faster quantization
)
```

**3. Use multi-GPU**:
```python
model = AutoGPTQForCausalLM.from_pretrained(
    model_name,
    device_map="auto"  # Parallelize across GPUs
)
```

## Troubleshooting

### Poor quality after quantization

**Symptom**: >5% perplexity increase or gibberish output

**Solutions**:
1. **Check calibration data**:
   ```python
   # Verify data is representative
   for sample in calibration_data[:5]:
       print(tokenizer.decode(sample))
   ```

2. **Try more samples**:
   ```python
   calibration_data = [... for example in dataset.take(256)]
   ```

3. **Use domain-specific data**:
   ```python
   # Match your model's use case
   dataset = load_dataset("domain_specific_dataset")
   ```

4. **Adjust dampening**:
   ```python
   config = BaseQuantizeConfig(damp_percent=0.005)  # Lower dampening
   ```

### Quantization OOM

**Solutions**:
1. **Reduce batch size**:
   ```python
   model.quantize(calibration_data, batch_size=1)  # Default: auto
   ```

2. **Use CPU offloading**:
   ```python
   model = AutoGPTQForCausalLM.from_pretrained(
       model_name,
       device_map="auto",
       max_memory={"cpu": "100GB"}
   )
   ```

3. **Quantize on larger GPU** or use multi-GPU

### Slow quantization

**Typical times** (7B model):
- Single A100: 10-15 minutes
- Single RTX 4090: 20-30 minutes
- CPU: 2-4 hours (not recommended)

**Speedup**:
- Use fewer samples (64 vs 256)
- Disable `desc_act`
- Use multi-GPU

## Best Practices

1. **Use C4 dataset for general models** - well-balanced, diverse
2. **Match domain** - code models need code data, chat needs conversations
3. **Start with 128 samples** - good balance of speed and quality
4. **Test perplexity** - always verify quality before deployment
5. **Compare kernels** - try ExLlama, Marlin, Triton for speed
6. **Save multiple versions** - try group_size 32, 128, 256
7. **Document settings** - save quantize_config.json for reproducibility




### Integration

# GPTQ Integration Guide

Integration with transformers, PEFT, vLLM, and other frameworks.

## Transformers Integration

### Auto-detection
```python
from transformers import AutoModelForCausalLM

# Automatically detects and loads GPTQ model
model = AutoModelForCausalLM.from_pretrained(
    "TheBloke/Llama-2-13B-GPTQ",
    device_map="auto"
)
```

### Manual loading
```python
from auto_gptq import AutoGPTQForCausalLM

model = AutoGPTQForCausalLM.from_quantized(
    "TheBloke/Llama-2-13B-GPTQ",
    device="cuda:0",
    use_exllama=True
)
```

## QLoRA Fine-Tuning

```python
from transformers import AutoModelForCausalLM, TrainingArguments
from peft import prepare_model_for_kbit_training, LoraConfig, get_peft_model
from trl import SFTTrainer

# Load GPTQ model
model = AutoModelForCausalLM.from_pretrained(
    "TheBloke/Llama-2-70B-GPTQ",
    device_map="auto"
)

# Prepare for training
model = prepare_model_for_kbit_training(model)

# LoRA config
lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["q_proj", "v_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, lora_config)

# Train (70B model on single A100!)
trainer = SFTTrainer(
    model=model,
    train_dataset=dataset,
    max_seq_length=2048,
    args=TrainingArguments(
        per_device_train_batch_size=1,
        gradient_accumulation_steps=16,
        learning_rate=2e-4,
        num_train_epochs=3,
        output_dir="./results"
    )
)

trainer.train()
```

## vLLM Integration

```python
from vllm import LLM, SamplingParams

# Load GPTQ model in vLLM
llm = LLM(
    model="TheBloke/Llama-2-70B-GPTQ",
    quantization="gptq",
    dtype="float16",
    gpu_memory_utilization=0.95
)

# Generate
sampling_params = SamplingParams(
    temperature=0.7,
    top_p=0.9,
    max_tokens=200
)

outputs = llm.generate(["Explain AI"], sampling_params)
```

## Text Generation Inference (TGI)

```bash
# Docker with GPTQ support
docker run --gpus all -p 8080:80 \
    -v $PWD/data:/data \
    ghcr.io/huggingface/text-generation-inference:latest \
    --model-id TheBloke/Llama-2-70B-GPTQ \
    --quantize gptq
```

## LangChain Integration

```python
from langchain.llms import HuggingFacePipeline
from transformers import AutoTokenizer, pipeline

tokenizer = AutoTokenizer.from_pretrained("TheBloke/Llama-2-13B-GPTQ")
model = AutoModelForCausalLM.from_pretrained(
    "TheBloke/Llama-2-13B-GPTQ",
    device_map="auto"
)

pipe = pipeline("text-generation", model=model, tokenizer=tokenizer, max_new_tokens=200)
llm = HuggingFacePipeline(pipeline=pipe)

# Use in LangChain
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate

chain = LLMChain(llm=llm, prompt=PromptTemplate(...))
result = chain.run(input="...")
```




### Troubleshooting

# GPTQ Troubleshooting Guide

Common issues and solutions for GPTQ quantization and inference.

## Installation Issues

### CUDA mismatch
```bash
# Check CUDA version
nvcc --version
python -c "import torch; print(torch.version.cuda)"

# Install matching version
pip install auto-gptq --extra-index-url https://huggingface.github.io/autogptq-index/whl/cu118/  # CUDA 11.8
```

### Build errors
```bash
# Install build dependencies
pip install auto-gptq --no-build-isolation

# On Ubuntu
sudo apt-get install python3-dev
```

## Runtime Issues

### Slow inference
```python
# Try different backends
model = AutoGPTQForCausalLM.from_quantized(
    model_name,
    use_exllama=True  # Fastest (try v1 or v2)
)

# Or Marlin (Ampere+ GPUs)
model = AutoGPTQForCausalLM.from_quantized(
    model_name,
    use_marlin=True
)
```

### OOM during inference
```python
# Reduce batch size
outputs = model.generate(**inputs, batch_size=1)

# Use CPU offloading
model = AutoGPTQForCausalLM.from_quantized(
    model_name,
    device_map="auto",
    max_memory={"cpu": "100GB"}
)

# Reduce context
model.seqlen = 1024  # Instead of 2048
```

### Poor quality outputs
```python
# Requantize with better calibration
# 1. Use more samples (256 instead of 128)
# 2. Use domain-specific data
# 3. Lower dampening: damp_percent=0.005
# 4. Enable desc_act=True
```

## Quantization Issues

### Very slow quantization
```bash
# Expected times (7B model):
# - A100: 10-15 min
# - RTX 4090: 20-30 min
# - CPU: 2-4 hours

# Speed up:
# 1. Use GPU
# 2. Reduce samples (64 instead of 256)
# 3. Disable desc_act
# 4. Use multi-GPU
```

### Quantization crashes
```python
# Reduce memory usage
model = AutoGPTQForCausalLM.from_pretrained(
    model_name,
    device_map="auto",
    max_memory={"cpu": "100GB"}  # Offload to CPU
)

# Or quantize layer-by-layer (slower but works)
model.quantize(calibration_data, batch_size=1)
```




---

## 🚀 Usage

**Reference this template:** `@skill-gptq.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
