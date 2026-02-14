---
id: skill-peft-fine-tuning
type: skill
name: peft-fine-tuning
description: Parameter-efficient fine-tuning for LLMs using LoRA, QLoRA, and 25+ methods.
  Use when fine-tuning large models (7B-70B) with limited GPU memory, when you need
  to train <1% of parameters with minimal accuracy loss, or for multi-adapter serving.
  HuggingFace's official library integrated with transformers ecosystem.
category: ai-research
complexity: medium
keywords:
- benchmark
- deployment
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 1751
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,751 -->
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




# peft-fine-tuning

> Parameter-efficient fine-tuning for LLMs using LoRA, QLoRA, and 25+ methods. Use when fine-tuning large models (7B-70B) with limited GPU memory, when you need to train <1% of parameters with minimal accuracy loss, or for multi-adapter serving. HuggingFace's official library integrated with transformers ecosystem.

# PEFT (Parameter-Efficient Fine-Tuning)

Fine-tune LLMs by training <1% of parameters using LoRA, QLoRA, and 25+ adapter methods.

## When to use PEFT

**Use PEFT/LoRA when:**
- Fine-tuning 7B-70B models on consumer GPUs (RTX 4090, A100)
- Need to train <1% parameters (6MB adapters vs 14GB full model)
- Want fast iteration with multiple task-specific adapters
- Deploying multiple fine-tuned variants from one base model

**Use QLoRA (PEFT + quantization) when:**
- Fine-tuning 70B models on single 24GB GPU
- Memory is the primary constraint
- Can accept ~5% quality trade-off vs full fine-tuning

**Use full fine-tuning instead when:**
- Training small models (<1B parameters)
- Need maximum quality and have compute budget
- Significant domain shift requires updating all weights

## Quick start

### Installation

```bash
# Basic installation
pip install peft

# With quantization support (recommended)
pip install peft bitsandbytes

# Full stack
pip install peft transformers accelerate bitsandbytes datasets
```

### LoRA fine-tuning (standard)

```python
from transformers import AutoModelForCausalLM, AutoTokenizer, TrainingArguments, Trainer
from peft import get_peft_model, LoraConfig, TaskType
from datasets import load_dataset

# Load base model
model_name = "meta-llama/Llama-3.1-8B"
model = AutoModelForCausalLM.from_pretrained(model_name, torch_dtype="auto", device_map="auto")
tokenizer = AutoTokenizer.from_pretrained(model_name)
tokenizer.pad_token = tokenizer.eos_token

# LoRA configuration
lora_config = LoraConfig(
    task_type=TaskType.CAUSAL_LM,
    r=16,                          # Rank (8-64, higher = more capacity)
    lora_alpha=32,                 # Scaling factor (typically 2*r)
    lora_dropout=0.05,             # Dropout for regularization
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj"],  # Attention layers
    bias="none"                    # Don't train biases
)

# Apply LoRA
model = get_peft_model(model, lora_config)
model.print_trainable_parameters()
# Output: trainable params: 13,631,488 || all params: 8,043,307,008 || trainable%: 0.17%

# Prepare dataset
dataset = load_dataset("databricks/databricks-dolly-15k", split="train")

def tokenize(example):
    text = f"### Instruction:\n{example['instruction']}\n\n### Response:\n{example['response']}"
    return tokenizer(text, truncation=True, max_length=512, padding="max_length")

tokenized = dataset.map(tokenize, remove_columns=dataset.column_names)

# Training
training_args = TrainingArguments(
    output_dir="./lora-llama",
    num_train_epochs=3,
    per_device_train_batch_size=4,
    gradient_accumulation_steps=4,
    learning_rate=2e-4,
    fp16=True,
    logging_steps=10,
    save_strategy="epoch"
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized,
    data_collator=lambda data: {"input_ids": torch.stack([f["input_ids"] for f in data]),
                                 "attention_mask": torch.stack([f["attention_mask"] for f in data]),
                                 "labels": torch.stack([f["input_ids"] for f in data])}
)

trainer.train()

# Save adapter only (6MB vs 16GB)
model.save_pretrained("./lora-llama-adapter")
```

### QLoRA fine-tuning (memory-efficient)

```python
from transformers import AutoModelForCausalLM, BitsAndBytesConfig
from peft import get_peft_model, LoraConfig, prepare_model_for_kbit_training

# 4-bit quantization config
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",           # NormalFloat4 (best for LLMs)
    bnb_4bit_compute_dtype="bfloat16",   # Compute in bf16
    bnb_4bit_use_double_quant=True       # Nested quantization
)

# Load quantized model
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-70B",
    quantization_config=bnb_config,
    device_map="auto"
)

# Prepare for training (enables gradient checkpointing)
model = prepare_model_for_kbit_training(model)

# LoRA config for QLoRA
lora_config = LoraConfig(
    r=64,                              # Higher rank for 70B
    lora_alpha=128,
    lora_dropout=0.1,
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj", "gate_proj", "up_proj", "down_proj"],
    bias="none",
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, lora_config)
# 70B model now fits on single 24GB GPU!
```

## LoRA parameter selection

### Rank (r) - capacity vs efficiency

| Rank | Trainable Params | Memory | Quality | Use Case |
|------|-----------------|--------|---------|----------|
| 4 | ~3M | Minimal | Lower | Simple tasks, prototyping |
| **8** | ~7M | Low | Good | **Recommended starting point** |
| **16** | ~14M | Medium | Better | **General fine-tuning** |
| 32 | ~27M | Higher | High | Complex tasks |
| 64 | ~54M | High | Highest | Domain adaptation, 70B models |

### Alpha (lora_alpha) - scaling factor

```python
# Rule of thumb: alpha = 2 * rank
LoraConfig(r=16, lora_alpha=32)  # Standard
LoraConfig(r=16, lora_alpha=16)  # Conservative (lower learning rate effect)
LoraConfig(r=16, lora_alpha=64)  # Aggressive (higher learning rate effect)
```

### Target modules by architecture

```python
# Llama / Mistral / Qwen
target_modules = ["q_proj", "v_proj", "k_proj", "o_proj", "gate_proj", "up_proj", "down_proj"]

# GPT-2 / GPT-Neo
target_modules = ["c_attn", "c_proj", "c_fc"]

# Falcon
target_modules = ["query_key_value", "dense", "dense_h_to_4h", "dense_4h_to_h"]

# BLOOM
target_modules = ["query_key_value", "dense", "dense_h_to_4h", "dense_4h_to_h"]

# Auto-detect all linear layers
target_modules = "all-linear"  # PEFT 0.6.0+
```

## Loading and merging adapters

### Load trained adapter

```python
from peft import PeftModel, AutoPeftModelForCausalLM
from transformers import AutoModelForCausalLM

# Option 1: Load with PeftModel
base_model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-3.1-8B")
model = PeftModel.from_pretrained(base_model, "./lora-llama-adapter")

# Option 2: Load directly (recommended)
model = AutoPeftModelForCausalLM.from_pretrained(
    "./lora-llama-adapter",
    device_map="auto"
)
```

### Merge adapter into base model

```python
# Merge for deployment (no adapter overhead)
merged_model = model.merge_and_unload()

# Save merged model
merged_model.save_pretrained("./llama-merged")
tokenizer.save_pretrained("./llama-merged")

# Push to Hub
merged_model.push_to_hub("username/llama-finetuned")
```

### Multi-adapter serving

```python
from peft import PeftModel

# Load base with first adapter
model = AutoPeftModelForCausalLM.from_pretrained("./adapter-task1")

# Load additional adapters
model.load_adapter("./adapter-task2", adapter_name="task2")
model.load_adapter("./adapter-task3", adapter_name="task3")

# Switch between adapters at runtime
model.set_adapter("task1")  # Use task1 adapter
output1 = model.generate(**inputs)

model.set_adapter("task2")  # Switch to task2
output2 = model.generate(**inputs)

# Disable adapters (use base model)
with model.disable_adapter():
    base_output = model.generate(**inputs)
```

## PEFT methods comparison

| Method | Trainable % | Memory | Speed | Best For |
|--------|------------|--------|-------|----------|
| **LoRA** | 0.1-1% | Low | Fast | General fine-tuning |
| **QLoRA** | 0.1-1% | Very Low | Medium | Memory-constrained |
| AdaLoRA | 0.1-1% | Low | Medium | Automatic rank selection |
| IA3 | 0.01% | Minimal | Fastest | Few-shot adaptation |
| Prefix Tuning | 0.1% | Low | Medium | Generation control |
| Prompt Tuning | 0.001% | Minimal | Fast | Simple task adaptation |
| P-Tuning v2 | 0.1% | Low | Medium | NLU tasks |

### IA3 (minimal parameters)

```python
from peft import IA3Config

ia3_config = IA3Config(
    target_modules=["q_proj", "v_proj", "k_proj", "down_proj"],
    feedforward_modules=["down_proj"]
)
model = get_peft_model(model, ia3_config)
# Trains only 0.01% of parameters!
```

### Prefix Tuning

```python
from peft import PrefixTuningConfig

prefix_config = PrefixTuningConfig(
    task_type="CAUSAL_LM",
    num_virtual_tokens=20,      # Prepended tokens
    prefix_projection=True       # Use MLP projection
)
model = get_peft_model(model, prefix_config)
```

## Integration patterns

### With TRL (SFTTrainer)

```python
from trl import SFTTrainer, SFTConfig
from peft import LoraConfig

lora_config = LoraConfig(r=16, lora_alpha=32, target_modules="all-linear")

trainer = SFTTrainer(
    model=model,
    args=SFTConfig(output_dir="./output", max_seq_length=512),
    train_dataset=dataset,
    peft_config=lora_config,  # Pass LoRA config directly
)
trainer.train()
```

### With Axolotl (YAML config)

```yaml
# axolotl config.yaml
adapter: lora
lora_r: 16
lora_alpha: 32
lora_dropout: 0.05
lora_target_modules:
  - q_proj
  - v_proj
  - k_proj
  - o_proj
lora_target_linear: true  # Target all linear layers
```

### With vLLM (inference)

```python
from vllm import LLM
from vllm.lora.request import LoRARequest

# Load base model with LoRA support
llm = LLM(model="meta-llama/Llama-3.1-8B", enable_lora=True)

# Serve with adapter
outputs = llm.generate(
    prompts,
    lora_request=LoRARequest("adapter1", 1, "./lora-adapter")
)
```

## Performance benchmarks

### Memory usage (Llama 3.1 8B)

| Method | GPU Memory | Trainable Params |
|--------|-----------|------------------|
| Full fine-tuning | 60+ GB | 8B (100%) |
| LoRA r=16 | 18 GB | 14M (0.17%) |
| QLoRA r=16 | 6 GB | 14M (0.17%) |
| IA3 | 16 GB | 800K (0.01%) |

### Training speed (A100 80GB)

| Method | Tokens/sec | vs Full FT |
|--------|-----------|------------|
| Full FT | 2,500 | 1x |
| LoRA | 3,200 | 1.3x |
| QLoRA | 2,100 | 0.84x |

### Quality (MMLU benchmark)

| Model | Full FT | LoRA | QLoRA |
|-------|---------|------|-------|
| Llama 2-7B | 45.3 | 44.8 | 44.1 |
| Llama 2-13B | 54.8 | 54.2 | 53.5 |

## Common issues

### CUDA OOM during training

```python
# Solution 1: Enable gradient checkpointing
model.gradient_checkpointing_enable()

# Solution 2: Reduce batch size + increase accumulation
TrainingArguments(
    per_device_train_batch_size=1,
    gradient_accumulation_steps=16
)

# Solution 3: Use QLoRA
from transformers import BitsAndBytesConfig
bnb_config = BitsAndBytesConfig(load_in_4bit=True, bnb_4bit_quant_type="nf4")
```

### Adapter not applying

```python
# Verify adapter is active
print(model.active_adapters)  # Should show adapter name

# Check trainable parameters
model.print_trainable_parameters()

# Ensure model in training mode
model.train()
```

### Quality degradation

```python
# Increase rank
LoraConfig(r=32, lora_alpha=64)

# Target more modules
target_modules = "all-linear"

# Use more training data and epochs
TrainingArguments(num_train_epochs=5)

# Lower learning rate
TrainingArguments(learning_rate=1e-4)
```

## Best practices

1. **Start with r=8-16**, increase if quality insufficient
2. **Use alpha = 2 * rank** as starting point
3. **Target attention + MLP layers** for best quality/efficiency
4. **Enable gradient checkpointing** for memory savings
5. **Save adapters frequently** (small files, easy rollback)
6. **Evaluate on held-out data** before merging
7. **Use QLoRA for 70B+ models** on consumer hardware

## References

- **[Advanced Usage](references/advanced-usage.md)** - DoRA, LoftQ, rank stabilization, custom modules
- **[Troubleshooting](references/troubleshooting.md)** - Common errors, debugging, optimization

## Resources

- **GitHub**: https://github.com/huggingface/peft
- **Docs**: https://huggingface.co/docs/peft
- **LoRA Paper**: arXiv:2106.09685
- **QLoRA Paper**: arXiv:2305.14314
- **Models**: https://huggingface.co/models?library=peft


---


## 📚 Reference Materials


### Advanced Usage

# PEFT Advanced Usage Guide

## Advanced LoRA Variants

### DoRA (Weight-Decomposed Low-Rank Adaptation)

DoRA decomposes weights into magnitude and direction components, often achieving better results than standard LoRA:

```python
from peft import LoraConfig

dora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj"],
    use_dora=True,  # Enable DoRA
    task_type="CAUSAL_LM"
)

model = get_peft_model(model, dora_config)
```

**When to use DoRA**:
- Consistently outperforms LoRA on instruction-following tasks
- Slightly higher memory (~10%) due to magnitude vectors
- Best for quality-critical fine-tuning

### AdaLoRA (Adaptive Rank)

Automatically adjusts rank per layer based on importance:

```python
from peft import AdaLoraConfig

adalora_config = AdaLoraConfig(
    init_r=64,              # Initial rank
    target_r=16,            # Target average rank
    tinit=200,              # Warmup steps
    tfinal=1000,            # Final pruning step
    deltaT=10,              # Rank update frequency
    beta1=0.85,
    beta2=0.85,
    orth_reg_weight=0.5,    # Orthogonality regularization
    target_modules=["q_proj", "v_proj"],
    task_type="CAUSAL_LM"
)
```

**Benefits**:
- Allocates more rank to important layers
- Can reduce total parameters while maintaining quality
- Good for exploring optimal rank distribution

### LoRA+ (Asymmetric Learning Rates)

Different learning rates for A and B matrices:

```python
from peft import LoraConfig

# LoRA+ uses higher LR for B matrix
lora_plus_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules="all-linear",
    use_rslora=True,  # Rank-stabilized LoRA (related technique)
)

# Manual implementation of LoRA+
from torch.optim import AdamW

# Group parameters
lora_A_params = [p for n, p in model.named_parameters() if "lora_A" in n]
lora_B_params = [p for n, p in model.named_parameters() if "lora_B" in n]

optimizer = AdamW([
    {"params": lora_A_params, "lr": 1e-4},
    {"params": lora_B_params, "lr": 1e-3},  # 10x higher for B
])
```

### rsLoRA (Rank-Stabilized LoRA)

Scales LoRA outputs to stabilize training with different ranks:

```python
lora_config = LoraConfig(
    r=64,
    lora_alpha=64,
    use_rslora=True,  # Enables rank-stabilized scaling
    target_modules="all-linear"
)
```

**When to use**:
- When experimenting with different ranks
- Helps maintain consistent behavior across rank values
- Recommended for r > 32

## LoftQ (LoRA-Fine-Tuning-aware Quantization)

Initializes LoRA weights to compensate for quantization error:

```python
from peft import LoftQConfig, LoraConfig, get_peft_model
from transformers import AutoModelForCausalLM, BitsAndBytesConfig

# LoftQ configuration
loftq_config = LoftQConfig(
    loftq_bits=4,              # Quantization bits
    loftq_iter=5,              # Alternating optimization iterations
)

# LoRA config with LoftQ initialization
lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules="all-linear",
    init_lora_weights="loftq",
    loftq_config=loftq_config,
    task_type="CAUSAL_LM"
)

# Load quantized model
bnb_config = BitsAndBytesConfig(load_in_4bit=True, bnb_4bit_quant_type="nf4")
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    quantization_config=bnb_config
)

model = get_peft_model(model, lora_config)
```

**Benefits over standard QLoRA**:
- Better initial quality after quantization
- Faster convergence
- ~1-2% better final accuracy on benchmarks

## Custom Module Targeting

### Target specific layers

```python
# Target only first and last transformer layers
lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["model.layers.0.self_attn.q_proj",
                    "model.layers.0.self_attn.v_proj",
                    "model.layers.31.self_attn.q_proj",
                    "model.layers.31.self_attn.v_proj"],
    layers_to_transform=[0, 31]  # Alternative approach
)
```

### Layer pattern matching

```python
# Target layers 0-10 only
lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules="all-linear",
    layers_to_transform=list(range(11)),  # Layers 0-10
    layers_pattern="model.layers"
)
```

### Exclude specific layers

```python
lora_config = LoraConfig(
    r=16,
    target_modules="all-linear",
    modules_to_save=["lm_head"],  # Train these fully (not LoRA)
)
```

## Embedding and LM Head Training

### Train embeddings with LoRA

```python
from peft import LoraConfig

# Include embeddings
lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["q_proj", "v_proj", "embed_tokens"],  # Include embeddings
    modules_to_save=["lm_head"],  # Train lm_head fully
)
```

### Extending vocabulary with LoRA

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
from peft import get_peft_model, LoraConfig

# Add new tokens
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-3.1-8B")
new_tokens = ["<custom_token_1>", "<custom_token_2>"]
tokenizer.add_tokens(new_tokens)

# Resize model embeddings
model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-3.1-8B")
model.resize_token_embeddings(len(tokenizer))

# Configure LoRA to train new embeddings
lora_config = LoraConfig(
    r=16,
    target_modules="all-linear",
    modules_to_save=["embed_tokens", "lm_head"],  # Train these fully
)

model = get_peft_model(model, lora_config)
```

## Multi-Adapter Patterns

### Adapter composition

```python
from peft import PeftModel

# Load model with multiple adapters
model = AutoPeftModelForCausalLM.from_pretrained("./base-adapter")
model.load_adapter("./style-adapter", adapter_name="style")
model.load_adapter("./task-adapter", adapter_name="task")

# Combine adapters (weighted sum)
model.add_weighted_adapter(
    adapters=["style", "task"],
    weights=[0.7, 0.3],
    adapter_name="combined",
    combination_type="linear"  # or "cat", "svd"
)

model.set_adapter("combined")
```

### Adapter stacking

```python
# Stack adapters (apply sequentially)
model.add_weighted_adapter(
    adapters=["base", "domain", "task"],
    weights=[1.0, 1.0, 1.0],
    adapter_name="stacked",
    combination_type="cat"  # Concatenate adapter outputs
)
```

### Dynamic adapter switching

```python
import torch

class MultiAdapterModel:
    def __init__(self, base_model_path, adapter_paths):
        self.model = AutoPeftModelForCausalLM.from_pretrained(adapter_paths[0])
        for name, path in adapter_paths[1:].items():
            self.model.load_adapter(path, adapter_name=name)

    def generate(self, prompt, adapter_name="default"):
        self.model.set_adapter(adapter_name)
        return self.model.generate(**self.tokenize(prompt))

    def generate_ensemble(self, prompt, adapters, weights):
        """Generate with weighted adapter ensemble"""
        outputs = []
        for adapter, weight in zip(adapters, weights):
            self.model.set_adapter(adapter)
            logits = self.model(**self.tokenize(prompt)).logits
            outputs.append(weight * logits)
        return torch.stack(outputs).sum(dim=0)
```

## Memory Optimization

### Gradient checkpointing with LoRA

```python
from peft import prepare_model_for_kbit_training

# Enable gradient checkpointing
model = prepare_model_for_kbit_training(
    model,
    use_gradient_checkpointing=True,
    gradient_checkpointing_kwargs={"use_reentrant": False}
)
```

### CPU offloading for training

```python
from accelerate import Accelerator

accelerator = Accelerator(
    mixed_precision="bf16",
    gradient_accumulation_steps=8,
    cpu_offload=True  # Offload optimizer states to CPU
)

model, optimizer, dataloader = accelerator.prepare(model, optimizer, dataloader)
```

### Memory-efficient attention with LoRA

```python
from transformers import AutoModelForCausalLM

# Combine Flash Attention 2 with LoRA
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-3.1-8B",
    attn_implementation="flash_attention_2",
    torch_dtype=torch.bfloat16
)

# Apply LoRA
model = get_peft_model(model, lora_config)
```

## Inference Optimization

### Merge for deployment

```python
# Merge adapter weights into base model
merged_model = model.merge_and_unload()

# Quantize merged model for inference
from transformers import BitsAndBytesConfig

bnb_config = BitsAndBytesConfig(load_in_4bit=True)
quantized_model = AutoModelForCausalLM.from_pretrained(
    "./merged-model",
    quantization_config=bnb_config
)
```

### Export to different formats

```python
# Export to GGUF (llama.cpp)
# First merge, then convert
merged_model.save_pretrained("./merged-model")

# Use llama.cpp converter
# python convert-hf-to-gguf.py ./merged-model --outfile model.gguf

# Export to ONNX
from optimum.onnxruntime import ORTModelForCausalLM

ort_model = ORTModelForCausalLM.from_pretrained(
    "./merged-model",
    export=True
)
ort_model.save_pretrained("./onnx-model")
```

### Batch adapter inference

```python
from vllm import LLM
from vllm.lora.request import LoRARequest

# Initialize with LoRA support
llm = LLM(
    model="meta-llama/Llama-3.1-8B",
    enable_lora=True,
    max_lora_rank=64,
    max_loras=4  # Max concurrent adapters
)

# Batch with different adapters
requests = [
    ("prompt1", LoRARequest("adapter1", 1, "./adapter1")),
    ("prompt2", LoRARequest("adapter2", 2, "./adapter2")),
    ("prompt3", LoRARequest("adapter1", 1, "./adapter1")),
]

outputs = llm.generate(
    [r[0] for r in requests],
    lora_request=[r[1] for r in requests]
)
```

## Training Recipes

### Instruction tuning recipe

```python
lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    lora_dropout=0.05,
    target_modules="all-linear",
    bias="none",
    task_type="CAUSAL_LM"
)

training_args = TrainingArguments(
    output_dir="./output",
    num_train_epochs=3,
    per_device_train_batch_size=4,
    gradient_accumulation_steps=4,
    learning_rate=2e-4,
    lr_scheduler_type="cosine",
    warmup_ratio=0.03,
    bf16=True,
    logging_steps=10,
    save_strategy="steps",
    save_steps=100,
    eval_strategy="steps",
    eval_steps=100,
)
```

### Code generation recipe

```python
lora_config = LoraConfig(
    r=32,              # Higher rank for code
    lora_alpha=64,
    lora_dropout=0.1,
    target_modules=["q_proj", "v_proj", "k_proj", "o_proj", "gate_proj", "up_proj", "down_proj"],
    bias="none",
    task_type="CAUSAL_LM"
)

training_args = TrainingArguments(
    learning_rate=1e-4,        # Lower LR for code
    num_train_epochs=2,
    max_seq_length=2048,       # Longer sequences
)
```

### Conversational/Chat recipe

```python
from trl import SFTTrainer

lora_config = LoraConfig(
    r=16,
    lora_alpha=16,  # alpha = r for chat
    lora_dropout=0.05,
    target_modules="all-linear"
)

# Use chat template
def format_chat(example):
    messages = [
        {"role": "user", "content": example["instruction"]},
        {"role": "assistant", "content": example["response"]}
    ]
    return tokenizer.apply_chat_template(messages, tokenize=False)

trainer = SFTTrainer(
    model=model,
    peft_config=lora_config,
    train_dataset=dataset.map(format_chat),
    max_seq_length=1024,
)
```

## Debugging and Validation

### Verify adapter application

```python
# Check which modules have LoRA
for name, module in model.named_modules():
    if hasattr(module, "lora_A"):
        print(f"LoRA applied to: {name}")

# Print detailed config
print(model.peft_config)

# Check adapter state
print(f"Active adapters: {model.active_adapters}")
print(f"Trainable: {sum(p.numel() for p in model.parameters() if p.requires_grad)}")
```

### Compare with base model

```python
# Generate with adapter
model.set_adapter("default")
adapter_output = model.generate(**inputs)

# Generate without adapter
with model.disable_adapter():
    base_output = model.generate(**inputs)

print(f"Adapter: {tokenizer.decode(adapter_output[0])}")
print(f"Base: {tokenizer.decode(base_output[0])}")
```

### Monitor training metrics

```python
from transformers import TrainerCallback

class LoRACallback(TrainerCallback):
    def on_log(self, args, state, control, logs=None, **kwargs):
        if "loss" in logs:
            # Log adapter-specific metrics
            model = kwargs["model"]
            lora_params = sum(p.numel() for n, p in model.named_parameters()
                            if "lora" in n and p.requires_grad)
            print(f"Step {state.global_step}: loss={logs['loss']:.4f}, lora_params={lora_params}")
```




### Troubleshooting

# PEFT Troubleshooting Guide

## Installation Issues

### bitsandbytes CUDA Error

**Error**: `CUDA Setup failed despite GPU being available`

**Fix**:
```bash
# Check CUDA version
nvcc --version

# Install matching bitsandbytes
pip uninstall bitsandbytes
pip install bitsandbytes --no-cache-dir

# Or compile from source for specific CUDA
git clone https://github.com/TimDettmers/bitsandbytes.git
cd bitsandbytes
CUDA_VERSION=118 make cuda11x  # Adjust for your CUDA
pip install .
```

### Triton Import Error

**Error**: `ModuleNotFoundError: No module named 'triton'`

**Fix**:
```bash
# Install triton (Linux only)
pip install triton

# Windows: Triton not supported, use CUDA backend
# Set environment variable to disable triton
export CUDA_VISIBLE_DEVICES=0
```

### PEFT Version Conflicts

**Error**: `AttributeError: 'LoraConfig' object has no attribute 'use_dora'`

**Fix**:
```bash
# Upgrade to latest PEFT
pip install peft>=0.13.0 --upgrade

# Check version
python -c "import peft; print(peft.__version__)"
```

## Training Issues

### CUDA Out of Memory

**Error**: `torch.cuda.OutOfMemoryError: CUDA out of memory`

**Solutions**:

1. **Enable gradient checkpointing**:
```python
from peft import prepare_model_for_kbit_training
model = prepare_model_for_kbit_training(model, use_gradient_checkpointing=True)
```

2. **Reduce batch size**:
```python
TrainingArguments(
    per_device_train_batch_size=1,
    gradient_accumulation_steps=16  # Maintain effective batch size
)
```

3. **Use QLoRA**:
```python
from transformers import BitsAndBytesConfig

bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_quant_type="nf4",
    bnb_4bit_use_double_quant=True
)
model = AutoModelForCausalLM.from_pretrained(model_name, quantization_config=bnb_config)
```

4. **Lower LoRA rank**:
```python
LoraConfig(r=8)  # Instead of r=16 or higher
```

5. **Target fewer modules**:
```python
target_modules=["q_proj", "v_proj"]  # Instead of all-linear
```

### Loss Not Decreasing

**Problem**: Training loss stays flat or increases.

**Solutions**:

1. **Check learning rate**:
```python
# Start lower
TrainingArguments(learning_rate=1e-4)  # Not 2e-4 or higher
```

2. **Verify adapter is active**:
```python
model.print_trainable_parameters()
# Should show >0 trainable params

# Check adapter applied
print(model.peft_config)
```

3. **Check data formatting**:
```python
# Verify tokenization
sample = dataset[0]
decoded = tokenizer.decode(sample["input_ids"])
print(decoded)  # Should look correct
```

4. **Increase rank**:
```python
LoraConfig(r=32, lora_alpha=64)  # More capacity
```

### NaN Loss

**Error**: `Loss is NaN`

**Fix**:
```python
# Use bf16 instead of fp16
TrainingArguments(bf16=True, fp16=False)

# Or enable loss scaling
TrainingArguments(fp16=True, fp16_full_eval=True)

# Lower learning rate
TrainingArguments(learning_rate=5e-5)

# Check for data issues
for batch in dataloader:
    if torch.isnan(batch["input_ids"].float()).any():
        print("NaN in input!")
```

### Adapter Not Training

**Problem**: `trainable params: 0` or model not updating.

**Fix**:
```python
# Verify LoRA applied to correct modules
for name, module in model.named_modules():
    if "lora" in name.lower():
        print(f"Found LoRA: {name}")

# Check target_modules match model architecture
from peft.utils import TRANSFORMERS_MODELS_TO_LORA_TARGET_MODULES_MAPPING
print(TRANSFORMERS_MODELS_TO_LORA_TARGET_MODULES_MAPPING.get(model.config.model_type))

# Ensure model in training mode
model.train()

# Check requires_grad
for name, param in model.named_parameters():
    if param.requires_grad:
        print(f"Trainable: {name}")
```

## Loading Issues

### Adapter Loading Fails

**Error**: `ValueError: Can't find adapter weights`

**Fix**:
```python
# Check adapter files exist
import os
print(os.listdir("./adapter-path"))
# Should contain: adapter_config.json, adapter_model.safetensors

# Load with correct structure
from peft import PeftModel, PeftConfig

# Check config
config = PeftConfig.from_pretrained("./adapter-path")
print(config)

# Load base model first
base_model = AutoModelForCausalLM.from_pretrained(config.base_model_name_or_path)
model = PeftModel.from_pretrained(base_model, "./adapter-path")
```

### Base Model Mismatch

**Error**: `RuntimeError: size mismatch`

**Fix**:
```python
# Ensure base model matches adapter
from peft import PeftConfig

config = PeftConfig.from_pretrained("./adapter-path")
print(f"Base model: {config.base_model_name_or_path}")

# Load exact same base model
base_model = AutoModelForCausalLM.from_pretrained(config.base_model_name_or_path)
```

### Safetensors vs PyTorch Format

**Error**: `ValueError: We couldn't connect to 'https://huggingface.co'`

**Fix**:
```python
# Force local loading
model = PeftModel.from_pretrained(
    base_model,
    "./adapter-path",
    local_files_only=True
)

# Or specify format
model.save_pretrained("./adapter", safe_serialization=True)  # safetensors
model.save_pretrained("./adapter", safe_serialization=False)  # pytorch
```

## Inference Issues

### Slow Generation

**Problem**: Inference much slower than expected.

**Solutions**:

1. **Merge adapter for deployment**:
```python
merged_model = model.merge_and_unload()
# No adapter overhead during inference
```

2. **Use optimized inference engine**:
```python
from vllm import LLM
llm = LLM(model="./merged-model", dtype="half")
```

3. **Enable Flash Attention**:
```python
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    attn_implementation="flash_attention_2"
)
```

### Output Quality Issues

**Problem**: Fine-tuned model produces worse outputs.

**Solutions**:

1. **Check evaluation without adapter**:
```python
with model.disable_adapter():
    base_output = model.generate(**inputs)
# Compare with adapter output
```

2. **Lower temperature during eval**:
```python
model.generate(**inputs, temperature=0.1, do_sample=False)
```

3. **Retrain with more data**:
```python
# Increase training samples
# Use higher quality data
# Train for more epochs
```

### Wrong Adapter Active

**Problem**: Model using wrong adapter or no adapter.

**Fix**:
```python
# Check active adapters
print(model.active_adapters)

# Explicitly set adapter
model.set_adapter("your-adapter-name")

# List all adapters
print(model.peft_config.keys())
```

## QLoRA Specific Issues

### Quantization Errors

**Error**: `RuntimeError: mat1 and mat2 shapes cannot be multiplied`

**Fix**:
```python
# Ensure compute dtype matches
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.bfloat16,  # Match model dtype
    bnb_4bit_quant_type="nf4"
)

# Load with correct dtype
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    quantization_config=bnb_config,
    torch_dtype=torch.bfloat16
)
```

### QLoRA OOM

**Error**: OOM even with 4-bit quantization.

**Fix**:
```python
# Enable double quantization
bnb_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_use_double_quant=True  # Further memory reduction
)

# Use offloading
model = AutoModelForCausalLM.from_pretrained(
    model_name,
    quantization_config=bnb_config,
    device_map="auto",
    max_memory={0: "20GB", "cpu": "100GB"}
)
```

### QLoRA Merge Fails

**Error**: `RuntimeError: expected scalar type BFloat16 but found Float`

**Fix**:
```python
# Dequantize before merging
from peft import PeftModel

# Load in higher precision for merging
base_model = AutoModelForCausalLM.from_pretrained(
    base_model_name,
    torch_dtype=torch.float16,  # Not quantized
    device_map="auto"
)

# Load adapter
model = PeftModel.from_pretrained(base_model, "./qlora-adapter")

# Now merge
merged = model.merge_and_unload()
```

## Multi-Adapter Issues

### Adapter Conflict

**Error**: `ValueError: Adapter with name 'default' already exists`

**Fix**:
```python
# Use unique names
model.load_adapter("./adapter1", adapter_name="task1")
model.load_adapter("./adapter2", adapter_name="task2")

# Or delete existing
model.delete_adapter("default")
```

### Mixed Precision Adapters

**Error**: Adapters trained with different dtypes.

**Fix**:
```python
# Convert adapter precision
model = PeftModel.from_pretrained(base_model, "./adapter")
model = model.to(torch.bfloat16)

# Or load with specific dtype
model = PeftModel.from_pretrained(
    base_model,
    "./adapter",
    torch_dtype=torch.bfloat16
)
```

## Performance Optimization

### Memory Profiling

```python
import torch

def print_memory():
    if torch.cuda.is_available():
        allocated = torch.cuda.memory_allocated() / 1e9
        reserved = torch.cuda.memory_reserved() / 1e9
        print(f"Allocated: {allocated:.2f}GB, Reserved: {reserved:.2f}GB")

# Profile during training
print_memory()  # Before
model.train()
loss = model(**batch).loss
loss.backward()
print_memory()  # After
```

### Speed Profiling

```python
import time
import torch

def benchmark_generation(model, tokenizer, prompt, n_runs=5):
    inputs = tokenizer(prompt, return_tensors="pt").to(model.device)

    # Warmup
    model.generate(**inputs, max_new_tokens=10)
    torch.cuda.synchronize()

    # Benchmark
    times = []
    for _ in range(n_runs):
        start = time.perf_counter()
        outputs = model.generate(**inputs, max_new_tokens=100)
        torch.cuda.synchronize()
        times.append(time.perf_counter() - start)

    tokens = outputs.shape[1] - inputs.input_ids.shape[1]
    avg_time = sum(times) / len(times)
    print(f"Speed: {tokens/avg_time:.2f} tokens/sec")

# Compare adapter vs merged
benchmark_generation(adapter_model, tokenizer, "Hello")
benchmark_generation(merged_model, tokenizer, "Hello")
```

## Getting Help

1. **Check PEFT GitHub Issues**: https://github.com/huggingface/peft/issues
2. **HuggingFace Forums**: https://discuss.huggingface.co/
3. **PEFT Documentation**: https://huggingface.co/docs/peft

### Debugging Template

When reporting issues, include:

```python
# System info
import peft
import transformers
import torch

print(f"PEFT: {peft.__version__}")
print(f"Transformers: {transformers.__version__}")
print(f"PyTorch: {torch.__version__}")
print(f"CUDA: {torch.version.cuda}")
print(f"GPU: {torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'N/A'}")

# Config
print(model.peft_config)
model.print_trainable_parameters()
```




---

## 🚀 Usage

**Reference this template:** `@skill-peft-fine-tuning.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
