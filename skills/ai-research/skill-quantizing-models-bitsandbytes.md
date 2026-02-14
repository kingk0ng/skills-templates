---
id: skill-quantizing-models-bitsandbytes
type: skill
name: quantizing-models-bitsandbytes
description: Quantizes LLMs to 8-bit or 4-bit for 50-75% memory reduction with minimal
  accuracy loss. Use when GPU memory is limited, need to fit larger models, or want
  faster inference. Supports INT8, NF4, FP4 formats, QLoRA training, and 8-bit optimizers.
  Works with HuggingFace Transformers.
category: ai-research
complexity: medium
keywords:
- github
- optimization
- python
- test
capabilities: []
token_estimate: 1423
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,423 -->
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




# quantizing-models-bitsandbytes

> Quantizes LLMs to 8-bit or 4-bit for 50-75% memory reduction with minimal accuracy loss. Use when GPU memory is limited, need to fit larger models, or want faster inference. Supports INT8, NF4, FP4 formats, QLoRA training, and 8-bit optimizers. Works with HuggingFace Transformers.

# bitsandbytes - LLM Quantization

## Quick start

bitsandbytes reduces LLM memory by 50% (8-bit) or 75% (4-bit) with <1% accuracy loss.

**Installation**:
```bash
pip install bitsandbytes transformers accelerate
```

**8-bit quantization** (50% memory reduction):
```python
from transformers import AutoModelForCausalLM, BitsAndBytesConfig

config = BitsAndBytesConfig(load_in_8bit=True)
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    quantization_config=config,
    device_map="auto"
)

# Memory: 14GB → 7GB
```

**4-bit quantization** (75% memory reduction):
```python
config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.float16
)
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    quantization_config=config,
    device_map="auto"
)

# Memory: 14GB → 3.5GB
```

## Common workflows

### Workflow 1: Load large model in limited GPU memory

Copy this checklist:

```
Quantization Loading:
- [ ] Step 1: Calculate memory requirements
- [ ] Step 2: Choose quantization level (4-bit or 8-bit)
- [ ] Step 3: Configure quantization
- [ ] Step 4: Load and verify model
```

**Step 1: Calculate memory requirements**

Estimate model memory:
```
FP16 memory (GB) = Parameters × 2 bytes / 1e9
INT8 memory (GB) = Parameters × 1 byte / 1e9
INT4 memory (GB) = Parameters × 0.5 bytes / 1e9

Example (Llama 2 7B):
FP16: 7B × 2 / 1e9 = 14 GB
INT8: 7B × 1 / 1e9 = 7 GB
INT4: 7B × 0.5 / 1e9 = 3.5 GB
```

**Step 2: Choose quantization level**

| GPU VRAM | Model Size | Recommended |
|----------|------------|-------------|
| 8 GB | 3B | 4-bit |
| 12 GB | 7B | 4-bit |
| 16 GB | 7B | 8-bit or 4-bit |
| 24 GB | 13B | 8-bit or 70B 4-bit |
| 40+ GB | 70B | 8-bit |

**Step 3: Configure quantization**

For 8-bit (better accuracy):
```python
from transformers import BitsAndBytesConfig
import torch

config = BitsAndBytesConfig(
    load_in_8bit=True,
    llm_int8_threshold=6.0,  # Outlier threshold
    llm_int8_has_fp16_weight=False
)
```

For 4-bit (maximum memory savings):
```python
config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.float16,  # Compute in FP16
    bnb_4bit_quant_type="nf4",  # NormalFloat4 (recommended)
    bnb_4bit_use_double_quant=True  # Nested quantization
)
```

**Step 4: Load and verify model**

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-13b-hf",
    quantization_config=config,
    device_map="auto",  # Automatic device placement
    torch_dtype=torch.float16
)

tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-13b-hf")

# Test inference
inputs = tokenizer("Hello, how are you?", return_tensors="pt").to("cuda")
outputs = model.generate(**inputs, max_length=50)
print(tokenizer.decode(outputs[0]))

# Check memory
import torch
print(f"Memory allocated: {torch.cuda.memory_allocated()/1e9:.2f}GB")
```

### Workflow 2: Fine-tune with QLoRA (4-bit training)

QLoRA enables fine-tuning large models on consumer GPUs.

Copy this checklist:

```
QLoRA Fine-tuning:
- [ ] Step 1: Install dependencies
- [ ] Step 2: Configure 4-bit base model
- [ ] Step 3: Add LoRA adapters
- [ ] Step 4: Train with standard Trainer
```

**Step 1: Install dependencies**

```bash
pip install bitsandbytes transformers peft accelerate datasets
```

**Step 2: Configure 4-bit base model**

```python
from transformers import AutoModelForCausalLM, BitsAndBytesConfig
import torch

bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.float16,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    quantization_config=bnb_config,
    device_map="auto"
)
```

**Step 3: Add LoRA adapters**

```python
from peft import LoraConfig, get_peft_model, prepare_model_for_kbit_training

# Prepare model for training
model = prepare_model_for_kbit_training(model)

# Configure LoRA
lora_config = LoraConfig(
    r=16,  # LoRA rank
    lora_alpha=32,  # LoRA alpha
    target_modules=["q_proj", "k_proj", "v_proj", "o_proj"],
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

# Add LoRA adapters
model = get_peft_model(model, lora_config)
model.print_trainable_parameters()
# Output: trainable params: 4.2M || all params: 6.7B || trainable%: 0.06%
```

**Step 4: Train with standard Trainer**

```python
from transformers import Trainer, TrainingArguments

training_args = TrainingArguments(
    output_dir="./qlora-output",
    per_device_train_batch_size=4,
    gradient_accumulation_steps=4,
    num_train_epochs=3,
    learning_rate=2e-4,
    fp16=True,
    logging_steps=10,
    save_strategy="epoch"
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset,
    tokenizer=tokenizer
)

trainer.train()

# Save LoRA adapters (only ~20MB)
model.save_pretrained("./qlora-adapters")
```

### Workflow 3: 8-bit optimizer for memory-efficient training

Use 8-bit Adam/AdamW to reduce optimizer memory by 75%.

```
8-bit Optimizer Setup:
- [ ] Step 1: Replace standard optimizer
- [ ] Step 2: Configure training
- [ ] Step 3: Monitor memory savings
```

**Step 1: Replace standard optimizer**

```python
import bitsandbytes as bnb
from transformers import Trainer, TrainingArguments

# Instead of torch.optim.AdamW
model = AutoModelForCausalLM.from_pretrained("model-name")

training_args = TrainingArguments(
    output_dir="./output",
    per_device_train_batch_size=8,
    optim="paged_adamw_8bit",  # 8-bit optimizer
    learning_rate=5e-5
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=train_dataset
)

trainer.train()
```

**Manual optimizer usage**:
```python
import bitsandbytes as bnb

optimizer = bnb.optim.AdamW8bit(
    model.parameters(),
    lr=1e-4,
    betas=(0.9, 0.999),
    eps=1e-8
)

# Training loop
for batch in dataloader:
    loss = model(**batch).loss
    loss.backward()
    optimizer.step()
    optimizer.zero_grad()
```

**Step 2: Configure training**

Compare memory:
```
Standard AdamW optimizer memory = model_params × 8 bytes (states)
8-bit AdamW memory = model_params × 2 bytes
Savings = 75% optimizer memory

Example (Llama 2 7B):
Standard: 7B × 8 = 56 GB
8-bit: 7B × 2 = 14 GB
Savings: 42 GB
```

**Step 3: Monitor memory savings**

```python
import torch

before = torch.cuda.memory_allocated()

# Training step
optimizer.step()

after = torch.cuda.memory_allocated()
print(f"Memory used: {(after-before)/1e9:.2f}GB")
```

## When to use vs alternatives

**Use bitsandbytes when:**
- GPU memory limited (need to fit larger model)
- Training with QLoRA (fine-tune 70B on single GPU)
- Inference only (50-75% memory reduction)
- Using HuggingFace Transformers
- Acceptable 0-2% accuracy degradation

**Use alternatives instead:**
- **GPTQ/AWQ**: Production serving (faster inference than bitsandbytes)
- **GGUF**: CPU inference (llama.cpp)
- **FP8**: H100 GPUs (hardware FP8 faster)
- **Full precision**: Accuracy critical, memory not constrained

## Common issues

**Issue: CUDA error during loading**

Install matching CUDA version:
```bash
# Check CUDA version
nvcc --version

# Install matching bitsandbytes
pip install bitsandbytes --no-cache-dir
```

**Issue: Model loading slow**

Use CPU offload for large models:
```python
model = AutoModelForCausalLM.from_pretrained(
    "model-name",
    quantization_config=config,
    device_map="auto",
    max_memory={0: "20GB", "cpu": "30GB"}  # Offload to CPU
)
```

**Issue: Lower accuracy than expected**

Try 8-bit instead of 4-bit:
```python
config = BitsAndBytesConfig(load_in_8bit=True)
# 8-bit has <0.5% accuracy loss vs 1-2% for 4-bit
```

Or use NF4 with double quantization:
```python
config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",  # Better than fp4
    bnb_4bit_use_double_quant=True  # Extra accuracy
)
```

**Issue: OOM even with 4-bit**

Enable CPU offload:
```python
model = AutoModelForCausalLM.from_pretrained(
    "model-name",
    quantization_config=config,
    device_map="auto",
    offload_folder="offload",  # Disk offload
    offload_state_dict=True
)
```

## Advanced topics

**QLoRA training guide**: See [references/qlora-training.md](references/qlora-training.md) for complete fine-tuning workflows, hyperparameter tuning, and multi-GPU training.

**Quantization formats**: See [references/quantization-formats.md](references/quantization-formats.md) for INT8, NF4, FP4 comparison, double quantization, and custom quantization configs.

**Memory optimization**: See [references/memory-optimization.md](references/memory-optimization.md) for CPU offloading strategies, gradient checkpointing, and memory profiling.

## Hardware requirements

- **GPU**: NVIDIA with compute capability 7.0+ (Turing, Ampere, Hopper)
- **VRAM**: Depends on model and quantization
  - 4-bit Llama 2 7B: 4GB
  - 4-bit Llama 2 13B: 8GB
  - 4-bit Llama 2 70B: 24GB
- **CUDA**: 11.1+ (12.0+ recommended)
- **PyTorch**: 2.0+

**Supported platforms**: NVIDIA GPUs (primary), AMD ROCm, Intel GPUs (experimental)

## Resources

- GitHub: https://github.com/bitsandbytes-foundation/bitsandbytes
- HuggingFace docs: https://huggingface.co/docs/transformers/quantization/bitsandbytes
- QLoRA paper: "QLoRA: Efficient Finetuning of Quantized LLMs" (2023)
- LLM.int8() paper: "LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale" (2022)





---


## 📚 Reference Materials


### Quantization Formats

# Quantization Formats

Complete guide to INT8, NF4, FP4 quantization formats, double quantization, and custom configurations in bitsandbytes.

## Overview

bitsandbytes supports multiple quantization formats:
- **INT8**: 8-bit integer quantization (LLM.int8())
- **NF4**: 4-bit NormalFloat (for normally distributed weights)
- **FP4**: 4-bit FloatPoint (for uniformly distributed weights)
- **Double Quantization**: Quantize the quantization constants

## INT8 Quantization

### LLM.int8() Algorithm

LLM.int8() uses mixed 8-bit/16-bit matrix multiplication:
- Most features (>99.9%) computed in INT8
- Outlier features (>threshold) computed in FP16
- Results combined for final output

**Memory**: 50% reduction (2 bytes → 1 byte per parameter)
**Accuracy**: <0.5% degradation

### Configuration

```python
from transformers import BitsAndBytesConfig

config = BitsAndBytesConfig(
    load_in_8bit=True,
    llm_int8_threshold=6.0,  # Outlier threshold
    llm_int8_has_fp16_weight=False,  # Use INT8 storage
    llm_int8_skip_modules=["lm_head"]  # Skip certain layers
)
```

### Parameters Explained

**`llm_int8_threshold`** (default: 6.0):
- Activations with magnitude > threshold are kept in FP16
- Lower = more FP16 (slower but more accurate)
- Higher = more INT8 (faster but less accurate)

```python
# Conservative (more accurate)
llm_int8_threshold=5.0

# Aggressive (faster)
llm_int8_threshold=8.0
```

**`llm_int8_has_fp16_weight`** (default: False):
- `False`: Store weights in INT8 (50% memory savings)
- `True`: Store in FP16, quantize only during computation (no memory savings)

**`llm_int8_skip_modules`**:
```python
# Skip specific layers (keep in FP16)
llm_int8_skip_modules=["lm_head", "embed_tokens"]
```

### Example

```python
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-13b-hf",
    quantization_config=config,
    device_map="auto"
)

# Memory: 26GB (FP16) → 13GB (INT8)
```

### When to Use INT8

✅ **Use INT8 when**:
- Need high accuracy (<0.5% loss)
- Model fits with 50% reduction
- Have Turing+ GPU (tensor cores)

❌ **Don't use when**:
- Need maximum memory savings (use 4-bit)
- Inference speed critical (use GPTQ/AWQ)

## 4-Bit Quantization

### NormalFloat4 (NF4)

Optimized for normally distributed weights (most neural networks).

**How it works**:
- Bins chosen to minimize quantization error for normal distribution
- Asymmetric quantization bins
- Better for transformer weights

**Configuration**:
```python
config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_quant_type="nf4"  # NormalFloat4
)
```

**Memory**: 75% reduction (2 bytes → 0.5 bytes per parameter)

### FloatPoint4 (FP4)

Standard 4-bit floating point for uniform distributions.

**How it works**:
- Symmetric quantization bins
- Better for weights with broader dynamic range
- Less common for transformers

**Configuration**:
```python
config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_quant_type="fp4"  # FloatPoint4
)
```

### NF4 vs FP4 Comparison

| Aspect | NF4 | FP4 |
|--------|-----|-----|
| Distribution | Normal | Uniform |
| Typical use | **Transformers** | CNNs, unusual architectures |
| Accuracy | **Better for LLMs** | Worse for LLMs |
| Speed | Same | Same |
| Recommendation | ✅ Default | Use only if NF4 fails |

**Rule of thumb**: Always use NF4 for transformers.

### Example Comparison

```python
# NF4 (recommended)
nf4_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4"
)

# FP4 (alternative)
fp4_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="fp4"
)

# Load and compare
model_nf4 = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    quantization_config=nf4_config
)

model_fp4 = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    quantization_config=fp4_config
)

# Typical results on MMLU:
# NF4: 45.2%
# FP4: 43.8%
# FP16: 45.9%
```

## Compute Dtype

The `bnb_4bit_compute_dtype` controls the precision used for actual computation.

### Options

**torch.bfloat16** (recommended):
```python
bnb_4bit_compute_dtype=torch.bfloat16
```
- Good balance of speed and accuracy
- Recommended for A100/H100
- Prevents numerical instability

**torch.float16**:
```python
bnb_4bit_compute_dtype=torch.float16
```
- Slightly faster than BF16
- Risk of overflow/underflow
- Use only if BF16 unavailable

**torch.float32**:
```python
bnb_4bit_compute_dtype=torch.float32
```
- Most accurate
- Slowest (no tensor core acceleration)
- Debugging only

### Performance Comparison

| Dtype | Speed | Accuracy | Memory |
|-------|-------|----------|--------|
| FP32 | 1× (baseline) | 100% | 4 bytes |
| FP16 | 3-4× | 99.5% | 2 bytes |
| BF16 | 3-4× | **99.8%** | 2 bytes |

**Recommendation**: Always use `torch.bfloat16` if supported.

## Double Quantization

Quantize the quantization constants for additional memory savings.

### How It Works

Standard 4-bit quantization stores:
- 4-bit quantized weights
- FP32 scaling factors (4 bytes per block)

Double quantization:
- 4-bit quantized weights
- **INT8 quantized scaling factors** (1 byte per block)

**Additional savings**: ~2-3% memory reduction

### Configuration

```python
config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True  # Enable double quantization
)
```

### Example

```python
# Without double quant
model_single = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    quantization_config=BitsAndBytesConfig(
        load_in_4bit=True,
        bnb_4bit_use_double_quant=False
    )
)
# Memory: ~36GB

# With double quant
model_double = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    quantization_config=BitsAndBytesConfig(
        load_in_4bit=True,
        bnb_4bit_use_double_quant=True
    )
)
# Memory: ~35GB (saves ~1GB)
```

**Accuracy impact**: Negligible (<0.1%)

**Recommendation**: Always enable for maximum memory savings.

## Quantization Storage

Controls storage dtype for quantized weights (important for FSDP).

### Configuration

```python
config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_storage=torch.bfloat16  # Storage dtype
)
```

### When to Use

**Default (uint8)**:
- Single GPU training/inference
- No special requirements

**torch.bfloat16** (for FSDP):
```python
bnb_4bit_quant_storage=torch.bfloat16
```
- **Required for FSDP+QLoRA**
- Ensures 4-bit layers wrapped like regular layers
- Enables proper model sharding

### Example: FSDP Configuration

```python
# CRITICAL: Set quant_storage for FSDP
fsdp_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True,
    bnb_4bit_quant_storage=torch.bfloat16  # Must match torch_dtype!
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    quantization_config=fsdp_config,
    torch_dtype=torch.bfloat16  # Must match quant_storage!
)
```

## Recommended Configurations

### Production Inference (Best Accuracy)

```python
BitsAndBytesConfig(
    load_in_8bit=True,
    llm_int8_threshold=6.0
)
```

**Use case**: Maximum accuracy with 50% memory savings

### Production Inference (Maximum Memory Savings)

```python
BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True
)
```

**Use case**: 75% memory reduction with <1% accuracy loss

### QLoRA Training (Single GPU)

```python
BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True
)
```

**Use case**: Fine-tune 70B on RTX 3090

### FSDP + QLoRA (Multi-GPU)

```python
BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True,
    bnb_4bit_quant_storage=torch.bfloat16  # CRITICAL!
)
```

**Use case**: Fine-tune 405B on 8×H100

## Advanced: Block-wise Quantization

bitsandbytes uses block-wise quantization:
- Weights divided into blocks (typically 64 or 128 elements)
- Each block has own scaling factor
- Better accuracy than tensor-wise quantization

**Block size** (automatically determined):
```python
# Typical block sizes
# 4-bit: 64 elements per block
# 8-bit: 64 elements per block
```

**Cannot be configured** (internal implementation detail).

## Quantization Quality Metrics

### Perplexity (Lower is Better)

| Model | FP16 | INT8 | NF4 | NF4+DQ |
|-------|------|------|-----|--------|
| Llama 2 7B | 5.12 | 5.14 | 5.18 | 5.19 |
| Llama 2 13B | 4.88 | 4.90 | 4.93 | 4.94 |
| Llama 2 70B | 3.32 | 3.33 | 3.35 | 3.36 |

**Conclusion**: <1% degradation for all quantization methods

### MMLU Accuracy (Higher is Better)

| Model | FP16 | INT8 | NF4 | FP4 |
|-------|------|------|-----|-----|
| Llama 2 7B | 45.9% | 45.7% | 45.2% | 43.8% |
| Llama 2 13B | 54.8% | 54.6% | 54.1% | 52.9% |
| Llama 2 70B | 68.9% | 68.7% | 68.4% | 67.2% |

**Conclusion**: NF4 is significantly better than FP4 for transformers

## Troubleshooting

### "Quantization failed" Error

Try different quant type:
```python
# If NF4 fails
bnb_4bit_quant_type="fp4"
```

### Numerical Instability

Use BF16 compute:
```python
bnb_4bit_compute_dtype=torch.bfloat16
```

### Poor Quality with 4-bit

1. Try 8-bit instead:
   ```python
   load_in_8bit=True
   ```

2. Enable double quantization:
   ```python
   bnb_4bit_use_double_quant=True
   ```

3. Use BF16 compute dtype

### FSDP Errors

Ensure quant_storage matches torch_dtype:
```python
bnb_4bit_quant_storage=torch.bfloat16
torch_dtype=torch.bfloat16  # Must match!
```

## References

- LLM.int8() paper: "LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale" (2022)
- QLoRA paper: "QLoRA: Efficient Finetuning of Quantized LLMs" (2023)
- bitsandbytes GitHub: https://github.com/bitsandbytes-foundation/bitsandbytes
- HuggingFace quantization docs: https://huggingface.co/docs/transformers/quantization/bitsandbytes




### Memory Optimization

# Memory Optimization

Complete guide to CPU offloading, gradient checkpointing, memory profiling, and advanced memory-saving strategies with bitsandbytes.

## Overview

Memory optimization techniques for fitting large models:
- **Quantization**: 50-75% reduction (covered in other docs)
- **CPU offloading**: Move weights to CPU/disk
- **Gradient checkpointing**: Trade compute for memory
- **Optimizer strategies**: 8-bit, paged optimizers
- **Mixed precision**: FP16/BF16 training

## CPU Offloading

### Basic CPU Offloading

Move parts of the model to CPU RAM when not in use.

```python
from transformers import AutoModelForCausalLM, BitsAndBytesConfig
import torch

config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    quantization_config=config,
    device_map="auto",  # Automatic device placement
    max_memory={0: "40GB", "cpu": "100GB"}  # 40GB GPU, 100GB CPU
)
```

**How it works**:
- Weights stored on CPU
- Moved to GPU only when needed for computation
- Automatically managed by `accelerate`

**Trade-off**: ~5-10× slower but enables larger models

### Multi-GPU Offloading

Distribute across multiple GPUs + CPU:

```python
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-405b-hf",
    quantization_config=config,
    device_map="auto",
    max_memory={
        0: "70GB",   # GPU 0
        1: "70GB",   # GPU 1
        2: "70GB",   # GPU 2
        3: "70GB",   # GPU 3
        "cpu": "200GB"  # CPU RAM
    }
)
```

**Result**: 405B model (4-bit = ~200GB) fits on 4×80GB GPUs + CPU

### Disk Offloading

For models too large even for CPU RAM:

```python
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-405b-hf",
    quantization_config=config,
    device_map="auto",
    offload_folder="./offload",  # Disk offload directory
    offload_state_dict=True,
    max_memory={0: "40GB", "cpu": "50GB"}
)
```

**Trade-off**: Extremely slow (~100× slower) but works

### Manual Device Mapping

For precise control:

```python
device_map = {
    "model.embed_tokens": 0,  # GPU 0
    "model.layers.0": 0,
    "model.layers.1": 0,
    # ...
    "model.layers.40": 1,  # GPU 1
    "model.layers.41": 1,
    # ...
    "model.layers.79": "cpu",  # CPU
    "model.norm": "cpu",
    "lm_head": "cpu"
}

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    quantization_config=config,
    device_map=device_map
)
```

## Gradient Checkpointing

Recompute activations during backward pass instead of storing them.

### Enable for HuggingFace Models

```python
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-13b-hf",
    quantization_config=config
)

# Enable gradient checkpointing
model.gradient_checkpointing_enable()
```

**Memory savings**: ~30-50% activation memory
**Cost**: ~20% slower training

### With QLoRA

```python
from peft import prepare_model_for_kbit_training

# Enable gradient checkpointing before preparing for training
model.gradient_checkpointing_enable()
model = prepare_model_for_kbit_training(
    model,
    use_gradient_checkpointing=True
)
```

### Configure Checkpointing Frequency

```python
# Checkpoint every layer (maximum memory savings)
model.gradient_checkpointing_enable(gradient_checkpointing_kwargs={"use_reentrant": False})
```

### Memory Breakdown

Example: Llama 2 13B forward pass

| Component | Without Checkpointing | With Checkpointing |
|-----------|----------------------|-------------------|
| Model weights | 26 GB | 26 GB |
| Activations | 12 GB | **3 GB** |
| Gradients | 26 GB | 26 GB |
| Optimizer | 52 GB | 52 GB |
| **Total** | 116 GB | **107 GB** |

**Savings**: ~9GB for 13B model

## 8-Bit Optimizers

Use 8-bit optimizer states instead of 32-bit.

### Standard AdamW Memory

```
Optimizer memory = 2 × model_params × 4 bytes (FP32)
                 = 8 × model_params

Example (Llama 2 70B):
= 8 × 70B = 560 GB
```

### 8-Bit AdamW Memory

```
Optimizer memory = 2 × model_params × 1 byte (INT8)
                 = 2 × model_params

Example (Llama 2 70B):
= 2 × 70B = 140 GB

Savings: 420 GB (75% reduction!)
```

### Enable in Transformers

```python
from transformers import TrainingArguments

training_args = TrainingArguments(
    output_dir="./output",
    per_device_train_batch_size=4,
    optim="paged_adamw_8bit",  # 8-bit optimizer
    learning_rate=2e-4
)
```

### Available 8-Bit Optimizers

| Optimizer | Name | Use Case |
|-----------|------|----------|
| AdamW 8-bit | `adamw_8bit` | General training |
| Paged AdamW 8-bit | `paged_adamw_8bit` | **Recommended** (prevents OOM) |
| Paged AdamW 32-bit | `paged_adamw_32bit` | High accuracy needed |

**Recommendation**: Always use `paged_adamw_8bit`

### Manual Usage

```python
import bitsandbytes as bnb

optimizer = bnb.optim.PagedAdamW8bit(
    model.parameters(),
    lr=1e-4,
    betas=(0.9, 0.999),
    eps=1e-8
)
```

## Paged Optimizers

Paged optimizers use unified memory (GPU + CPU) to prevent OOM.

### How It Works

- Optimizer states stored in paged memory
- Pages swap between GPU and CPU as needed
- Prevents hard OOM crashes

### Configuration

```python
from transformers import TrainingArguments

training_args = TrainingArguments(
    optim="paged_adamw_8bit",  # Enables paging
    # Paging happens automatically
)
```

### Benefits

✅ No hard OOM (graceful degradation)
✅ Enables larger batch sizes
✅ Combines with 8-bit for maximum savings

### Performance

**Speed**: ~5-10% slower than standard optimizer
**Memory**: Effectively unlimited (uses CPU + swap)

## Mixed Precision Training

Use lower precision for faster training and less memory.

### BF16 Training (Recommended)

```python
training_args = TrainingArguments(
    bf16=True,  # BFloat16 training
    bf16_full_eval=True
)
```

**Requirements**: Ampere+ GPUs (A100, H100, RTX 3090+)

**Benefits**:
- 2× faster training
- 50% less activation memory
- Better stability than FP16

### FP16 Training

```python
training_args = TrainingArguments(
    fp16=True,  # Float16 training
    fp16_full_eval=True
)
```

**Requirements**: Volta+ GPUs (V100, A100, RTX 2080+)

**Benefits**:
- 2× faster training
- 50% less activation memory
- Slightly less stable than BF16

### Precision Comparison

| Precision | Speed | Memory | Stability | Use Case |
|-----------|-------|--------|-----------|----------|
| FP32 | 1× | 100% | Best | Debugging |
| BF16 | 2× | 50% | Good | **Recommended** |
| FP16 | 2× | 50% | Fair | V100 only |

## Complete Memory Optimization Stack

### Maximum Optimization (Llama 2 70B on Single A100 80GB)

```python
from transformers import AutoModelForCausalLM, BitsAndBytesConfig, TrainingArguments
from peft import LoraConfig, get_peft_model, prepare_model_for_kbit_training
import torch

# Step 1: 4-bit quantization
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    quantization_config=bnb_config,
    device_map="auto",
    max_memory={0: "70GB", "cpu": "100GB"}  # CPU offload if needed
)

# Step 2: Gradient checkpointing
model.gradient_checkpointing_enable()

# Step 3: Prepare for training
model = prepare_model_for_kbit_training(model, use_gradient_checkpointing=True)

# Step 4: LoRA adapters
lora_config = LoraConfig(
    r=16,  # Lower rank for memory
    lora_alpha=32,
    target_modules="all-linear",
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, lora_config)

# Step 5: Training arguments
training_args = TrainingArguments(
    output_dir="./output",
    per_device_train_batch_size=1,  # Small batch
    gradient_accumulation_steps=16,  # Effective batch = 16
    bf16=True,  # Mixed precision
    optim="paged_adamw_8bit",  # 8-bit optimizer
    max_grad_norm=0.3,
    learning_rate=2e-4
)

# Memory usage: ~75GB (fits on A100 80GB!)
```

### Memory Breakdown

| Component | Memory |
|-----------|--------|
| Model (4-bit) | 35 GB |
| LoRA adapters | 0.5 GB |
| Activations (with checkpointing) | 8 GB |
| Gradients | 0.5 GB |
| Optimizer (8-bit paged) | 1 GB |
| Batch buffer | 10 GB |
| CUDA overhead | 5 GB |
| **Total** | **~75 GB** |

## Memory Profiling

### PyTorch Memory Profiler

```python
import torch

# Start profiling
torch.cuda.empty_cache()
torch.cuda.reset_peak_memory_stats()

# Your code here
model = AutoModelForCausalLM.from_pretrained(...)
model.generate(...)

# Check memory
print(f"Allocated: {torch.cuda.memory_allocated()/1e9:.2f} GB")
print(f"Peak: {torch.cuda.max_memory_allocated()/1e9:.2f} GB")
print(f"Cached: {torch.cuda.memory_reserved()/1e9:.2f} GB")
```

### Detailed Memory Summary

```python
print(torch.cuda.memory_summary())
```

Output:
```
|===========================================================================|
|                  PyTorch CUDA memory summary                             |
|---------------------------------------------------------------------------|
| Metric           | Cur Usage | Peak Usage | Tot Alloc | Tot Freed       |
|---------------------------------------------------------------------------|
| Allocated memory | 45.2 GB   | 52.3 GB    | 156.8 GB  | 111.6 GB        |
| Active memory    | 45.2 GB   | 52.3 GB    | 156.8 GB  | 111.6 GB        |
| GPU reserved     | 46.0 GB   | 54.0 GB    | 54.0 GB   | 8.0 GB          |
|===========================================================================|
```

### Track Memory During Training

```python
from transformers import TrainerCallback

class MemoryCallback(TrainerCallback):
    def on_step_end(self, args, state, control, **kwargs):
        if state.global_step % 10 == 0:
            allocated = torch.cuda.memory_allocated() / 1e9
            reserved = torch.cuda.memory_reserved() / 1e9
            print(f"Step {state.global_step}: {allocated:.2f}GB allocated, {reserved:.2f}GB reserved")

trainer = Trainer(
    model=model,
    args=training_args,
    callbacks=[MemoryCallback()]
)
```

## Troubleshooting OOM

### Diagnostic Steps

1. **Check current memory**:
   ```python
   print(torch.cuda.memory_summary())
   ```

2. **Try smaller batch**:
   ```python
   per_device_train_batch_size=1
   ```

3. **Enable gradient checkpointing**:
   ```python
   model.gradient_checkpointing_enable()
   ```

4. **Use 8-bit optimizer**:
   ```python
   optim="paged_adamw_8bit"
   ```

5. **Add CPU offloading**:
   ```python
   max_memory={0: "70GB", "cpu": "100GB"}
   ```

6. **Reduce LoRA rank**:
   ```python
   r=8  # Instead of 16
   ```

### Emergency: Last Resort

```python
# Absolute minimum memory config
model = AutoModelForCausalLM.from_pretrained(
    "model-name",
    quantization_config=BitsAndBytesConfig(load_in_4bit=True),
    device_map="auto",
    max_memory={0: "20GB", "cpu": "200GB"},
    offload_folder="./offload"
)

model.gradient_checkpointing_enable()

training_args = TrainingArguments(
    per_device_train_batch_size=1,
    gradient_accumulation_steps=64,
    bf16=True,
    optim="paged_adamw_8bit"
)
```

**Result**: Extremely slow but will probably work

## Best Practices

1. **Start with quantization**: 4-bit gives 75% savings
2. **Add gradient checkpointing**: 30-50% activation savings
3. **Use 8-bit optimizer**: 75% optimizer savings
4. **Enable mixed precision**: 50% activation savings
5. **CPU offload only if needed**: Slow but enables larger models
6. **Profile regularly**: Identify memory bottlenecks
7. **Test with small batches**: Prevent OOM during development

## Memory Estimation Formula

```
Total Memory = Model + Activations + Gradients + Optimizer + Buffer

Model = Parameters × Bytes per param
Activations = Batch × Seq × Hidden × Layers × Bytes per activation
Gradients = Parameters × Bytes per gradient
Optimizer = Parameters × Optimizer factor × Bytes
Buffer = 2-5 GB (CUDA overhead)
```

**With all optimizations**:
```
Model = Parameters × 0.5 (4-bit)
Activations = Activations × 0.3 (checkpointing + BF16)
Gradients = Parameters × 0.5 (LoRA only)
Optimizer = Parameters × 2 (8-bit)
```

## References

- PyTorch memory management: https://pytorch.org/docs/stable/notes/cuda.html
- Accelerate device_map: https://huggingface.co/docs/accelerate/usage_guides/big_modeling
- Gradient checkpointing: https://pytorch.org/docs/stable/checkpoint.html
- bitsandbytes optimizers: https://github.com/bitsandbytes-foundation/bitsandbytes#optimizer




### Qlora Training

# QLoRA Training

Complete guide to fine-tuning large language models using 4-bit quantization with QLoRA (Quantized Low-Rank Adaptation).

## Overview

QLoRA enables fine-tuning 70B+ parameter models on consumer GPUs by:
- Loading base model in 4-bit (75% memory reduction)
- Training only small LoRA adapters (~20MB)
- Maintaining near-full-precision quality

**Memory savings**:
- Llama 2 70B: 140GB → 35GB (4-bit) + 20MB (LoRA) = **35GB total**
- Fits on single A100 80GB!

**Accuracy**: <1% degradation vs full fine-tuning

## Quick Start

### Basic QLoRA Fine-tuning

```python
from transformers import AutoModelForCausalLM, BitsAndBytesConfig, TrainingArguments
from peft import LoraConfig, get_peft_model, prepare_model_for_kbit_training
import torch

# Step 1: Load model in 4-bit
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True
)

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    quantization_config=bnb_config,
    device_map="auto",
    torch_dtype=torch.bfloat16
)

# Step 2: Prepare for k-bit training
model = prepare_model_for_kbit_training(model)

# Step 3: Add LoRA adapters
lora_config = LoraConfig(
    r=64,
    lora_alpha=16,
    target_modules="all-linear",
    lora_dropout=0.1,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, lora_config)
model.print_trainable_parameters()
# trainable params: 335M || all params: 70B || trainable%: 0.48%

# Step 4: Train
from trl import SFTTrainer

training_args = TrainingArguments(
    output_dir="./qlora-70b",
    per_device_train_batch_size=4,
    gradient_accumulation_steps=4,
    num_train_epochs=3,
    learning_rate=2e-4,
    bf16=True,
    optim="paged_adamw_8bit",
    logging_steps=10,
    save_strategy="epoch"
)

trainer = SFTTrainer(
    model=model,
    args=training_args,
    train_dataset=dataset,
    tokenizer=tokenizer
)

trainer.train()
```

## Complete Training Workflows

### Workflow 1: Single GPU Training (Consumer GPU)

Train Llama 2 13B on RTX 4090 (24GB).

**Step 1: Prepare dataset**

```python
from datasets import load_dataset

# Load instruction dataset
dataset = load_dataset("timdettmers/openassistant-guanaco")

# Format for instruction tuning
def format_instruction(example):
    return {
        "text": f"### Human: {example['text']}\n### Assistant: {example['output']}"
    }

dataset = dataset.map(format_instruction)
```

**Step 2: Configure quantization**

```python
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,  # BF16 for stability
    bnb_4bit_quant_type="nf4",  # NormalFloat4 (recommended)
    bnb_4bit_use_double_quant=True  # Nested quantization
)
```

**Step 3: Load and prepare model**

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-13b-hf",
    quantization_config=bnb_config,
    device_map="auto"
)

tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-13b-hf")
tokenizer.pad_token = tokenizer.eos_token

# Enable gradient checkpointing (further memory savings)
model.gradient_checkpointing_enable()
model = prepare_model_for_kbit_training(model, use_gradient_checkpointing=True)
```

**Step 4: Configure LoRA**

```python
from peft import LoraConfig

lora_config = LoraConfig(
    r=16,  # LoRA rank (lower = less memory)
    lora_alpha=32,  # Scaling factor
    target_modules="all-linear",  # Apply to all linear layers
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, lora_config)
```

**Step 5: Train**

```python
training_args = TrainingArguments(
    output_dir="./qlora-13b-results",
    per_device_train_batch_size=4,
    gradient_accumulation_steps=4,  # Effective batch = 16
    warmup_steps=100,
    num_train_epochs=1,
    learning_rate=2e-4,
    bf16=True,
    logging_steps=10,
    save_strategy="steps",
    save_steps=100,
    eval_strategy="steps",
    eval_steps=100,
    optim="paged_adamw_8bit",  # 8-bit optimizer
    max_grad_norm=0.3,
    max_steps=1000
)

trainer = SFTTrainer(
    model=model,
    args=training_args,
    train_dataset=dataset["train"],
    eval_dataset=dataset["test"],
    tokenizer=tokenizer,
    max_seq_length=512
)

trainer.train()
```

**Memory usage**: ~18GB on RTX 4090 (24GB)

### Workflow 2: Multi-GPU Training (FSDP + QLoRA)

Train Llama 2 70B on 8×A100 (80GB each).

**Step 1: Configure FSDP-compatible quantization**

```python
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True,
    bnb_4bit_quant_storage=torch.bfloat16  # CRITICAL for FSDP!
)
```

**Important**: `bnb_4bit_quant_storage=torch.bfloat16` ensures 4-bit layers are wrapped identically to regular layers for FSDP sharding.

**Step 2: Launch with accelerate**

Create `fsdp_config.yaml`:
```yaml
compute_environment: LOCAL_MACHINE
distributed_type: FSDP
fsdp_config:
  fsdp_auto_wrap_policy: TRANSFORMER_BASED_WRAP
  fsdp_backward_prefetch_policy: BACKWARD_PRE
  fsdp_forward_prefetch: true
  fsdp_sharding_strategy: 1  # FULL_SHARD
  fsdp_state_dict_type: SHARDED_STATE_DICT
  fsdp_transformer_layer_cls_to_wrap: LlamaDecoderLayer
mixed_precision: bf16
num_processes: 8
```

**Launch training**:
```bash
accelerate launch --config_file fsdp_config.yaml train_qlora.py
```

**train_qlora.py**:
```python
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    quantization_config=bnb_config,
    torch_dtype=torch.bfloat16
)

# Rest same as single-GPU workflow
model = prepare_model_for_kbit_training(model)
model = get_peft_model(model, lora_config)

trainer = SFTTrainer(...)
trainer.train()
```

**Memory per GPU**: ~40GB (70B model sharded across 8 GPUs)

### Workflow 3: Extremely Large Models (405B)

Train Llama 3.1 405B on 8×H100 (80GB each).

**Requirements**:
- 8×H100 80GB GPUs
- 256GB+ system RAM
- FSDP + QLoRA

**Configuration**:
```python
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True,
    bnb_4bit_quant_storage=torch.bfloat16
)

lora_config = LoraConfig(
    r=32,  # Higher rank for 405B
    lora_alpha=64,
    target_modules="all-linear",
    lora_dropout=0.1,
    bias="none",
    task_type="CAUSAL_LM"
)

training_args = TrainingArguments(
    per_device_train_batch_size=1,  # Small batch
    gradient_accumulation_steps=32,  # Effective batch = 256
    learning_rate=1e-4,  # Lower LR for large model
    bf16=True,
    optim="paged_adamw_8bit",
    gradient_checkpointing=True
)
```

**Memory per GPU**: ~70GB (405B in 4-bit / 8 GPUs)

## Hyperparameter Tuning

### LoRA Rank (r)

Controls adapter capacity:

| Model Size | Recommended r | Trainable Params | Use Case |
|------------|---------------|------------------|----------|
| 7B | 8-16 | ~4M | Simple tasks |
| 13B | 16-32 | ~8M | General fine-tuning |
| 70B | 32-64 | ~80M | Complex tasks |
| 405B | 64-128 | ~300M | Maximum capacity |

**Trade-off**: Higher r = more capacity but more memory and slower training

### LoRA Alpha

Scaling factor for LoRA updates:

```python
effective_learning_rate = learning_rate * (lora_alpha / r)
```

**Recommended**: `lora_alpha = 2 × r`
- r=16 → alpha=32
- r=64 → alpha=128

### Target Modules

**Options**:
- `"all-linear"`: All linear layers (recommended for QLoRA)
- `["q_proj", "v_proj"]`: Only attention (minimal)
- `["q_proj", "k_proj", "v_proj", "o_proj"]`: All attention
- `["q_proj", "k_proj", "v_proj", "o_proj", "gate_proj", "up_proj", "down_proj"]`: Attention + FFN

**Trade-off**: More modules = better performance but more memory

### Learning Rate

| Model Size | Recommended LR |
|------------|----------------|
| 7-13B | 2e-4 to 3e-4 |
| 70B | 1e-4 to 2e-4 |
| 405B | 5e-5 to 1e-4 |

**Rule**: Larger models need lower learning rates

### Batch Size

```python
effective_batch_size = per_device_batch_size × gradient_accumulation_steps × num_gpus
```

**Recommended effective batch sizes**:
- Instruction tuning: 64-128
- Continued pretraining: 256-512

### Quantization Dtype

| Dtype | Speed | Accuracy | Use Case |
|-------|-------|----------|----------|
| `torch.float32` | Slow | Best | Debugging |
| `torch.bfloat16` | Fast | Good | **Recommended** |
| `torch.float16` | Fastest | Risky | May have precision issues |

## Advanced Techniques

### Gradient Checkpointing

Save memory by recomputing activations:

```python
model.gradient_checkpointing_enable()
model = prepare_model_for_kbit_training(model, use_gradient_checkpointing=True)
```

**Memory savings**: ~30-40% activation memory
**Cost**: ~20% slower training

### Nested Quantization

Quantize the quantization constants:

```python
bnb_config = BitsAndBytesConfig(
    bnb_4bit_use_double_quant=True  # Enable nested quantization
)
```

**Memory savings**: Additional ~2-3% reduction
**Accuracy**: Minimal impact

### CPU Offloading

For models that still don't fit:

```python
model = AutoModelForCausalLM.from_pretrained(
    "model-name",
    quantization_config=bnb_config,
    device_map="auto",
    max_memory={0: "40GB", "cpu": "100GB"}
)
```

**Trade-off**: Much slower but enables larger models

### Paged Optimizers

Use paged memory for optimizer states:

```python
training_args = TrainingArguments(
    optim="paged_adamw_8bit"  # Or paged_adamw_32bit
)
```

**Benefit**: Prevents OOM from optimizer states

## Deployment

### Save LoRA Adapters

```python
# Save only adapters (~20MB)
model.save_pretrained("./qlora-adapters")
tokenizer.save_pretrained("./qlora-adapters")
```

### Load for Inference

```python
from peft import PeftModel

# Load base model in 4-bit
base_model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    quantization_config=bnb_config,
    device_map="auto"
)

# Load adapters
model = PeftModel.from_pretrained(base_model, "./qlora-adapters")

# Inference
inputs = tokenizer("Question here", return_tensors="pt").to("cuda")
outputs = model.generate(**inputs, max_length=200)
```

### Merge Adapters (Optional)

```python
# Merge LoRA into base weights
model = model.merge_and_unload()

# Save merged model
model.save_pretrained("./merged-model")
```

**Note**: Merged model loses 4-bit quantization (back to FP16/BF16)

## Troubleshooting

### OOM During Training

1. Reduce batch size:
   ```python
   per_device_train_batch_size=1
   ```

2. Increase gradient accumulation:
   ```python
   gradient_accumulation_steps=16
   ```

3. Lower LoRA rank:
   ```python
   r=8  # Instead of 16
   ```

4. Enable gradient checkpointing

5. Use CPU offloading

### Low Quality Results

1. Increase LoRA rank:
   ```python
   r=64  # Instead of 16
   ```

2. Train longer:
   ```python
   num_train_epochs=3  # Instead of 1
   ```

3. Use more target modules:
   ```python
   target_modules="all-linear"
   ```

4. Check learning rate (try 1e-4 to 3e-4)

### Slow Training

1. Disable gradient checkpointing (if memory allows)

2. Increase batch size

3. Use BF16:
   ```python
   bf16=True
   ```

4. Use paged optimizer

## Best Practices

1. **Start small**: Test on 7B before 70B
2. **Monitor loss**: Should decrease steadily
3. **Use validation**: Track eval loss to detect overfitting
4. **Save checkpoints**: Every 100-500 steps
5. **Log hyperparameters**: For reproducibility
6. **Test inference**: Verify quality before full training

## Example: Complete Training Script

See full working example at `examples/qlora_training.py` in the repository.

## References

- QLoRA paper: "QLoRA: Efficient Finetuning of Quantized LLMs" (Dettmers et al., 2023)
- bitsandbytes GitHub: https://github.com/bitsandbytes-foundation/bitsandbytes
- PEFT documentation: https://huggingface.co/docs/peft
- FSDP+QLoRA guide: https://huggingface.co/blog/fsdp-qlora




---

## 🚀 Usage

**Reference this template:** `@skill-quantizing-models-bitsandbytes.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
