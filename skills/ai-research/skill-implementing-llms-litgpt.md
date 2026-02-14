---
id: skill-implementing-llms-litgpt
type: skill
name: implementing-llms-litgpt
description: Implements and trains LLMs using Lightning AI's LitGPT with 20+ pretrained
  architectures (Llama, Gemma, Phi, Qwen, Mistral). Use when need clean model implementations,
  educational understanding of architectures, or production fine-tuning with LoRA/QLoRA.
  Single-file implementations, no abstraction layers.
category: ai-research
complexity: medium
keywords:
- api
- deploy
- deployment
- github
- performance
- python
- test
capabilities: []
token_estimate: 1734
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,734 -->
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




# implementing-llms-litgpt

> Implements and trains LLMs using Lightning AI's LitGPT with 20+ pretrained architectures (Llama, Gemma, Phi, Qwen, Mistral). Use when need clean model implementations, educational understanding of architectures, or production fine-tuning with LoRA/QLoRA. Single-file implementations, no abstraction layers.

# LitGPT - Clean LLM Implementations

## Quick start

LitGPT provides 20+ pretrained LLM implementations with clean, readable code and production-ready training workflows.

**Installation**:
```bash
pip install 'litgpt[extra]'
```

**Load and use any model**:
```python
from litgpt import LLM

# Load pretrained model
llm = LLM.load("microsoft/phi-2")

# Generate text
result = llm.generate(
    "What is the capital of France?",
    max_new_tokens=50,
    temperature=0.7
)
print(result)
```

**List available models**:
```bash
litgpt download list
```

## Common workflows

### Workflow 1: Fine-tune on custom dataset

Copy this checklist:

```
Fine-Tuning Setup:
- [ ] Step 1: Download pretrained model
- [ ] Step 2: Prepare dataset
- [ ] Step 3: Configure training
- [ ] Step 4: Run fine-tuning
```

**Step 1: Download pretrained model**

```bash
# Download Llama 3 8B
litgpt download meta-llama/Meta-Llama-3-8B

# Download Phi-2 (smaller, faster)
litgpt download microsoft/phi-2

# Download Gemma 2B
litgpt download google/gemma-2b
```

Models are saved to `checkpoints/` directory.

**Step 2: Prepare dataset**

LitGPT supports multiple formats:

**Alpaca format** (instruction-response):
```json
[
  {
    "instruction": "What is the capital of France?",
    "input": "",
    "output": "The capital of France is Paris."
  },
  {
    "instruction": "Translate to Spanish: Hello, how are you?",
    "input": "",
    "output": "Hola, ¿cómo estás?"
  }
]
```

Save as `data/my_dataset.json`.

**Step 3: Configure training**

```bash
# Full fine-tuning (requires 40GB+ GPU for 7B models)
litgpt finetune \
  meta-llama/Meta-Llama-3-8B \
  --data JSON \
  --data.json_path data/my_dataset.json \
  --train.max_steps 1000 \
  --train.learning_rate 2e-5 \
  --train.micro_batch_size 1 \
  --train.global_batch_size 16

# LoRA fine-tuning (efficient, 16GB GPU)
litgpt finetune_lora \
  microsoft/phi-2 \
  --data JSON \
  --data.json_path data/my_dataset.json \
  --lora_r 16 \
  --lora_alpha 32 \
  --lora_dropout 0.05 \
  --train.max_steps 1000 \
  --train.learning_rate 1e-4
```

**Step 4: Run fine-tuning**

Training saves checkpoints to `out/finetune/` automatically.

Monitor training:
```bash
# View logs
tail -f out/finetune/logs.txt

# TensorBoard (if using --train.logger_name tensorboard)
tensorboard --logdir out/finetune/lightning_logs
```

### Workflow 2: LoRA fine-tuning on single GPU

Most memory-efficient option.

```
LoRA Training:
- [ ] Step 1: Choose base model
- [ ] Step 2: Configure LoRA parameters
- [ ] Step 3: Train with LoRA
- [ ] Step 4: Merge LoRA weights (optional)
```

**Step 1: Choose base model**

For limited GPU memory (12-16GB):
- **Phi-2** (2.7B) - Best quality/size tradeoff
- **Llama 3 1B** - Smallest, fastest
- **Gemma 2B** - Good reasoning

**Step 2: Configure LoRA parameters**

```bash
litgpt finetune_lora \
  microsoft/phi-2 \
  --data JSON \
  --data.json_path data/my_dataset.json \
  --lora_r 16 \          # LoRA rank (8-64, higher=more capacity)
  --lora_alpha 32 \      # LoRA scaling (typically 2×r)
  --lora_dropout 0.05 \  # Prevent overfitting
  --lora_query true \    # Apply LoRA to query projection
  --lora_key false \     # Usually not needed
  --lora_value true \    # Apply LoRA to value projection
  --lora_projection true \  # Apply LoRA to output projection
  --lora_mlp false \     # Usually not needed
  --lora_head false      # Usually not needed
```

LoRA rank guide:
- `r=8`: Lightweight, 2-4MB adapters
- `r=16`: Standard, good quality
- `r=32`: High capacity, use for complex tasks
- `r=64`: Maximum quality, 4× larger adapters

**Step 3: Train with LoRA**

```bash
litgpt finetune_lora \
  microsoft/phi-2 \
  --data JSON \
  --data.json_path data/my_dataset.json \
  --lora_r 16 \
  --train.epochs 3 \
  --train.learning_rate 1e-4 \
  --train.micro_batch_size 4 \
  --train.global_batch_size 32 \
  --out_dir out/phi2-lora

# Memory usage: ~8-12GB for Phi-2 with LoRA
```

**Step 4: Merge LoRA weights** (optional)

Merge LoRA adapters into base model for deployment:

```bash
litgpt merge_lora \
  out/phi2-lora/final \
  --out_dir out/phi2-merged
```

Now use merged model:
```python
from litgpt import LLM
llm = LLM.load("out/phi2-merged")
```

### Workflow 3: Pretrain from scratch

Train new model on your domain data.

```
Pretraining:
- [ ] Step 1: Prepare pretraining dataset
- [ ] Step 2: Configure model architecture
- [ ] Step 3: Set up multi-GPU training
- [ ] Step 4: Launch pretraining
```

**Step 1: Prepare pretraining dataset**

LitGPT expects tokenized data. Use `prepare_dataset.py`:

```bash
python scripts/prepare_dataset.py \
  --source_path data/my_corpus.txt \
  --checkpoint_dir checkpoints/tokenizer \
  --destination_path data/pretrain \
  --split train,val
```

**Step 2: Configure model architecture**

Edit config file or use existing:

```python
# config/pythia-160m.yaml
model_name: pythia-160m
block_size: 2048
vocab_size: 50304
n_layer: 12
n_head: 12
n_embd: 768
rotary_percentage: 0.25
parallel_residual: true
bias: true
```

**Step 3: Set up multi-GPU training**

```bash
# Single GPU
litgpt pretrain \
  --config config/pythia-160m.yaml \
  --data.data_dir data/pretrain \
  --train.max_tokens 10_000_000_000

# Multi-GPU with FSDP
litgpt pretrain \
  --config config/pythia-1b.yaml \
  --data.data_dir data/pretrain \
  --devices 8 \
  --train.max_tokens 100_000_000_000
```

**Step 4: Launch pretraining**

For large-scale pretraining on cluster:

```bash
# Using SLURM
sbatch --nodes=8 --gpus-per-node=8 \
  pretrain_script.sh

# pretrain_script.sh content:
litgpt pretrain \
  --config config/pythia-1b.yaml \
  --data.data_dir /shared/data/pretrain \
  --devices 8 \
  --num_nodes 8 \
  --train.global_batch_size 512 \
  --train.max_tokens 300_000_000_000
```

### Workflow 4: Convert and deploy model

Export LitGPT models for production.

```
Model Deployment:
- [ ] Step 1: Test inference locally
- [ ] Step 2: Quantize model (optional)
- [ ] Step 3: Convert to GGUF (for llama.cpp)
- [ ] Step 4: Deploy with API
```

**Step 1: Test inference locally**

```python
from litgpt import LLM

llm = LLM.load("out/phi2-lora/final")

# Single generation
print(llm.generate("What is machine learning?"))

# Streaming
for token in llm.generate("Explain quantum computing", stream=True):
    print(token, end="", flush=True)

# Batch inference
prompts = ["Hello", "Goodbye", "Thank you"]
results = [llm.generate(p) for p in prompts]
```

**Step 2: Quantize model** (optional)

Reduce model size with minimal quality loss:

```bash
# 8-bit quantization (50% size reduction)
litgpt convert_lit_checkpoint \
  out/phi2-lora/final \
  --dtype bfloat16 \
  --quantize bnb.nf4

# 4-bit quantization (75% size reduction)
litgpt convert_lit_checkpoint \
  out/phi2-lora/final \
  --quantize bnb.nf4-dq  # Double quantization
```

**Step 3: Convert to GGUF** (for llama.cpp)

```bash
python scripts/convert_lit_checkpoint.py \
  --checkpoint_path out/phi2-lora/final \
  --output_path models/phi2.gguf \
  --model_name microsoft/phi-2
```

**Step 4: Deploy with API**

```python
from fastapi import FastAPI
from litgpt import LLM

app = FastAPI()
llm = LLM.load("out/phi2-lora/final")

@app.post("/generate")
def generate(prompt: str, max_tokens: int = 100):
    result = llm.generate(
        prompt,
        max_new_tokens=max_tokens,
        temperature=0.7
    )
    return {"response": result}

# Run: uvicorn api:app --host 0.0.0.0 --port 8000
```

## When to use vs alternatives

**Use LitGPT when:**
- Want to understand LLM architectures (clean, readable code)
- Need production-ready training recipes
- Educational purposes or research
- Prototyping new model ideas
- Lightning ecosystem user

**Use alternatives instead:**
- **Axolotl/TRL**: More fine-tuning features, YAML configs
- **Megatron-Core**: Maximum performance for >70B models
- **HuggingFace Transformers**: Broadest model support
- **vLLM**: Inference-only (no training)

## Common issues

**Issue: Out of memory during fine-tuning**

Use LoRA instead of full fine-tuning:
```bash
# Instead of litgpt finetune (requires 40GB+)
litgpt finetune_lora  # Only needs 12-16GB
```

Or enable gradient checkpointing:
```bash
litgpt finetune_lora \
  ... \
  --train.gradient_accumulation_iters 4  # Accumulate gradients
```

**Issue: Training too slow**

Enable Flash Attention (built-in, automatic on compatible hardware):
```python
# Already enabled by default on Ampere+ GPUs (A100, RTX 30/40 series)
# No configuration needed
```

Use smaller micro-batch and accumulate:
```bash
--train.micro_batch_size 1 \
--train.global_batch_size 32 \
--train.gradient_accumulation_iters 32  # Effective batch=32
```

**Issue: Model not loading**

Check model name:
```bash
# List all available models
litgpt download list

# Download if not exists
litgpt download meta-llama/Meta-Llama-3-8B
```

Verify checkpoints directory:
```bash
ls checkpoints/
# Should see: meta-llama/Meta-Llama-3-8B/
```

**Issue: LoRA adapters too large**

Reduce LoRA rank:
```bash
--lora_r 8  # Instead of 16 or 32
```

Apply LoRA to fewer layers:
```bash
--lora_query true \
--lora_value true \
--lora_projection false \  # Disable this
--lora_mlp false  # And this
```

## Advanced topics

**Supported architectures**: See [references/supported-models.md](references/supported-models.md) for complete list of 20+ model families with sizes and capabilities.

**Training recipes**: See [references/training-recipes.md](references/training-recipes.md) for proven hyperparameter configurations for pretraining and fine-tuning.

**FSDP configuration**: See [references/distributed-training.md](references/distributed-training.md) for multi-GPU training with Fully Sharded Data Parallel.

**Custom architectures**: See [references/custom-models.md](references/custom-models.md) for implementing new model architectures in LitGPT style.

## Hardware requirements

- **GPU**: NVIDIA (CUDA 11.8+), AMD (ROCm), Apple Silicon (MPS)
- **Memory**:
  - Inference (Phi-2): 6GB
  - LoRA fine-tuning (7B): 16GB
  - Full fine-tuning (7B): 40GB+
  - Pretraining (1B): 24GB
- **Storage**: 5-50GB per model (depending on size)

## Resources

- GitHub: https://github.com/Lightning-AI/litgpt
- Docs: https://lightning.ai/docs/litgpt
- Tutorials: https://lightning.ai/docs/litgpt/tutorials
- Model zoo: 20+ pretrained architectures (Llama, Gemma, Phi, Qwen, Mistral, Mixtral, Falcon, etc.)




---


## 📚 Reference Materials


### Custom Models

# Custom Models

Guide to implementing custom model architectures in LitGPT.

## Overview

LitGPT's clean, single-file implementations make it easy to create custom architectures. You can extend the base `GPT` class or create entirely new models.

**Use cases**:
- Implementing new research architectures
- Adapting models for specific domains
- Experimenting with attention mechanisms
- Adding custom layers or components

## Key Files and Classes

### Core Architecture (`litgpt/model.py`)

**Main classes**:
- `GPT`: Top-level model class
- `Block`: Transformer block (attention + MLP)
- `CausalSelfAttention`: Attention mechanism
- `MLP`: Feed-forward network
- `RMSNorm` / `LayerNorm`: Normalization layers

**Configuration** (`litgpt/config.py`):
- `Config`: Base configuration dataclass
- Model-specific configs: `LlamaConfig`, `MistralConfig`, `PhiConfig`, etc.

## Custom Architecture Workflow

### Step 1: Define Configuration

Create a `Config` dataclass with your model's hyperparameters:

```python
from dataclasses import dataclass
from litgpt.config import Config

@dataclass
class MyModelConfig(Config):
    """Configuration for my custom model."""
    # Standard parameters
    name: str = "my-model-7b"
    block_size: int = 4096
    vocab_size: int = 32000
    n_layer: int = 32
    n_head: int = 32
    n_embd: int = 4096

    # Custom parameters
    custom_param: float = 0.1
    use_custom_attention: bool = True

    # Optional: override defaults
    rope_base: int = 10000
    intermediate_size: int = 11008
```

### Step 2: Implement Custom Components

#### Option A: Custom Attention

```python
from litgpt.model import CausalSelfAttention
import torch
import torch.nn as nn

class CustomAttention(CausalSelfAttention):
    """Custom attention mechanism."""

    def __init__(self, config):
        super().__init__(config)
        # Add custom components
        self.custom_proj = nn.Linear(config.n_embd, config.n_embd)
        self.custom_param = config.custom_param

    def forward(self, x, mask=None, input_pos=None):
        B, T, C = x.size()

        # Standard Q, K, V projections
        q = self.attn(x)
        k = self.attn(x)
        v = self.attn(x)

        # Custom modification
        q = q + self.custom_proj(x) * self.custom_param

        # Rest of attention computation
        q = q.view(B, T, self.n_head, self.head_size)
        k = k.view(B, T, self.n_query_groups, self.head_size)
        v = v.view(B, T, self.n_query_groups, self.head_size)

        # Scaled dot-product attention
        y = self.scaled_dot_product_attention(q, k, v, mask=mask)

        y = y.reshape(B, T, C)
        return self.proj(y)
```

#### Option B: Custom MLP

```python
from litgpt.model import MLP

class CustomMLP(MLP):
    """Custom feed-forward network."""

    def __init__(self, config):
        super().__init__(config)
        # Add custom layers
        self.custom_layer = nn.Linear(config.intermediate_size, config.intermediate_size)

    def forward(self, x):
        x = self.fc_1(x)
        x = self.act(x)
        x = self.custom_layer(x)  # Custom modification
        x = self.fc_2(x)
        return x
```

#### Option C: Custom Block

```python
from litgpt.model import Block

class CustomBlock(Block):
    """Custom transformer block."""

    def __init__(self, config):
        super().__init__(config)
        # Replace attention or MLP
        self.attn = CustomAttention(config)
        # Or: self.mlp = CustomMLP(config)

        # Add custom components
        self.custom_norm = nn.LayerNorm(config.n_embd)

    def forward(self, x, input_pos=None, mask=None):
        # Custom forward pass
        h = self.norm_1(x)
        h = self.attn(h, mask=mask, input_pos=input_pos)
        x = x + h

        # Custom normalization
        x = x + self.custom_norm(x)

        x = x + self.mlp(self.norm_2(x))
        return x
```

### Step 3: Create Custom GPT Model

```python
from litgpt.model import GPT
import torch.nn as nn

class CustomGPT(GPT):
    """Custom GPT model."""

    def __init__(self, config: MyModelConfig):
        # Don't call super().__init__() - we reimplement
        nn.Module.__init__(self)
        self.config = config

        # Standard components
        self.lm_head = nn.Linear(config.n_embd, config.vocab_size, bias=False)
        self.transformer = nn.ModuleDict(
            dict(
                wte=nn.Embedding(config.vocab_size, config.n_embd),
                h=nn.ModuleList(CustomBlock(config) for _ in range(config.n_layer)),
                ln_f=nn.LayerNorm(config.n_embd),
            )
        )

        # Custom components
        if config.use_custom_attention:
            self.custom_embedding = nn.Linear(config.n_embd, config.n_embd)

        # Initialize weights
        self.apply(self._init_weights)

    def _init_weights(self, module):
        """Initialize weights (required)."""
        if isinstance(module, nn.Linear):
            torch.nn.init.normal_(module.weight, mean=0.0, std=0.02)
            if module.bias is not None:
                torch.nn.init.zeros_(module.bias)
        elif isinstance(module, nn.Embedding):
            torch.nn.init.normal_(module.weight, mean=0.0, std=0.02)

    def forward(self, idx, input_pos=None):
        """Forward pass (must match base signature)."""
        B, T = idx.size()

        # Token embeddings
        x = self.transformer.wte(idx)

        # Custom embedding modification
        if self.config.use_custom_attention:
            x = x + self.custom_embedding(x)

        # Transformer blocks
        for block in self.transformer.h:
            x = block(x, input_pos=input_pos)

        # Final norm + LM head
        x = self.transformer.ln_f(x)
        return self.lm_head(x)
```

### Step 4: Register Configuration

Add your config to `litgpt/config.py`:

```python
# In litgpt/config.py
configs = [
    # ... existing configs ...

    # My custom model
    dict(
        name="my-model-7b",
        hf_config=dict(org="myorg", name="my-model-7b"),
        block_size=4096,
        vocab_size=32000,
        n_layer=32,
        n_head=32,
        n_embd=4096,
        custom_param=0.1,
    ),
]
```

### Step 5: Use Your Custom Model

```python
from litgpt.api import LLM
from my_model import CustomGPT, MyModelConfig

# Initialize
config = MyModelConfig()
model = CustomGPT(config)

# Wrap with LLM API
llm = LLM(model=model, tokenizer_dir="path/to/tokenizer")

# Generate
result = llm.generate("Once upon a time", max_new_tokens=100)
print(result)
```

## Real Example: Adapter Fine-tuning

LitGPT's `Adapter` implementation shows a complete custom architecture:

### Adapter Configuration

```python
@dataclass
class Config(BaseConfig):
    """Adds adapter-specific parameters."""
    adapter_prompt_length: int = 10
    adapter_start_layer: int = 2
```

### Adapter GPT Model

```python
class GPT(BaseModel):
    """GPT model with adapter layers."""

    def __init__(self, config: Config):
        nn.Module.__init__(self)
        self.config = config

        # Standard components
        self.lm_head = nn.Linear(config.n_embd, config.padded_vocab_size, bias=False)
        self.transformer = nn.ModuleDict(
            dict(
                wte=nn.Embedding(config.padded_vocab_size, config.n_embd),
                h=nn.ModuleList(Block(config, i) for i in range(config.n_layer)),
                ln_f=config.norm_class(config.n_embd, eps=config.norm_eps),
            )
        )

        # Adapter-specific: gating factor
        self.gating_factor = torch.nn.Parameter(torch.zeros(1))
```

### Adapter Block

```python
class Block(BaseBlock):
    """Transformer block with adapter."""

    def __init__(self, config: Config, block_idx: int):
        super().__init__()
        self.norm_1 = config.norm_class(config.n_embd, eps=config.norm_eps)
        self.attn = CausalSelfAttention(config, block_idx)
        self.norm_2 = config.norm_class(config.n_embd, eps=config.norm_eps)
        self.mlp = config.mlp_class(config)

        # Adapter: add prefix for certain layers
        self.adapter_wte = (
            nn.Embedding(config.adapter_prompt_length, config.n_embd)
            if block_idx >= config.adapter_start_layer
            else None
        )
```

### Adapter Attention

```python
class CausalSelfAttention(BaseCausalSelfAttention):
    """Attention with adapter prompts."""

    def forward(self, x: torch.Tensor, ...) -> torch.Tensor:
        B, T, C = x.size()

        # Add adapter prefix if enabled
        if self.adapter_wte is not None:
            adapter_prompts = self.adapter_wte(
                torch.arange(self.adapter_prompt_length, device=x.device)
            )
            adapter_prompts = adapter_prompts.unsqueeze(0).expand(B, -1, -1)
            x = torch.cat([adapter_prompts, x], dim=1)

        # Standard attention with gating
        q, k, v = self.attn(x).split(self.n_embd, dim=2)
        y = self.scaled_dot_product_attention(q, k, v, mask=mask)

        # Apply gating factor
        y = y * self.gating_factor

        return self.proj(y)
```

See full implementation: `litgpt/finetune/adapter.py`

## Real Example: AdapterV2

AdapterV2 shows custom linear layers:

### AdapterV2Linear

```python
class AdapterV2Linear(torch.nn.Module):
    """Linear layer with low-rank adapter."""

    def __init__(self, in_features, out_features, adapter_rank=8, **kwargs):
        super().__init__()
        self.linear = torch.nn.Linear(in_features, out_features, **kwargs)

        # Adapter: low-rank bottleneck
        self.adapter_down = torch.nn.Linear(in_features, adapter_rank, bias=False)
        self.adapter_up = torch.nn.Linear(adapter_rank, out_features, bias=False)

        # Initialize adapter to identity
        torch.nn.init.zeros_(self.adapter_up.weight)

    def forward(self, x):
        # Original linear transformation
        out = self.linear(x)

        # Add adapter contribution
        adapter_out = self.adapter_up(self.adapter_down(x))
        return out + adapter_out
```

See full implementation: `litgpt/finetune/adapter_v2.py`

## Custom Model Checklist

- [ ] Define `Config` dataclass with all hyperparameters
- [ ] Implement custom components (Attention, MLP, Block)
- [ ] Create custom `GPT` class
- [ ] Implement `_init_weights()` for proper initialization
- [ ] Implement `forward()` matching base signature
- [ ] Register configuration in `litgpt/config.py`
- [ ] Test with small model (100M params) first
- [ ] Verify training convergence
- [ ] Profile memory usage

## Testing Your Custom Model

### Unit Test

```python
import torch
from my_model import CustomGPT, MyModelConfig

def test_custom_model():
    """Test custom model forward pass."""
    config = MyModelConfig(
        n_layer=2,
        n_head=4,
        n_embd=128,
        vocab_size=1000,
        block_size=256,
    )

    model = CustomGPT(config)
    model.eval()

    # Test forward pass
    batch_size = 2
    seq_length = 16
    idx = torch.randint(0, config.vocab_size, (batch_size, seq_length))

    with torch.no_grad():
        logits = model(idx)

    assert logits.shape == (batch_size, seq_length, config.vocab_size)
    print("✓ Forward pass works")

if __name__ == "__main__":
    test_custom_model()
```

### Training Test

```python
from litgpt.api import LLM

def test_training():
    """Test custom model training."""
    config = MyModelConfig(n_layer=2, n_head=4, n_embd=128)
    model = CustomGPT(config)

    # Small dataset for testing
    data = [
        {"instruction": "Test", "input": "", "output": "OK"}
    ]

    # Should run without errors
    llm = LLM(model=model)
    # ... training code ...
    print("✓ Training works")
```

## Common Patterns

### Adding New Attention Mechanism

```python
class MyAttention(nn.Module):
    """Template for custom attention."""

    def __init__(self, config):
        super().__init__()
        self.n_head = config.n_head
        self.n_embd = config.n_embd
        self.head_size = self.n_embd // self.n_head

        # Q, K, V projections
        self.q_proj = nn.Linear(config.n_embd, config.n_embd, bias=False)
        self.k_proj = nn.Linear(config.n_embd, config.n_embd, bias=False)
        self.v_proj = nn.Linear(config.n_embd, config.n_embd, bias=False)

        # Output projection
        self.out_proj = nn.Linear(config.n_embd, config.n_embd, bias=False)

    def forward(self, x, mask=None):
        B, T, C = x.size()

        # Project Q, K, V
        q = self.q_proj(x).view(B, T, self.n_head, self.head_size)
        k = self.k_proj(x).view(B, T, self.n_head, self.head_size)
        v = self.v_proj(x).view(B, T, self.n_head, self.head_size)

        # Custom attention computation here
        # attn = custom_attention_function(q, k, v, mask)

        # Output projection
        out = self.out_proj(attn.reshape(B, T, C))
        return out
```

### Adding Mixture of Experts

```python
class MoELayer(nn.Module):
    """Mixture of Experts layer."""

    def __init__(self, config):
        super().__init__()
        self.num_experts = config.num_experts
        self.top_k = config.moe_top_k

        # Router
        self.router = nn.Linear(config.n_embd, self.num_experts)

        # Experts
        self.experts = nn.ModuleList([
            MLP(config) for _ in range(self.num_experts)
        ])

    def forward(self, x):
        B, T, C = x.size()

        # Route tokens to experts
        router_logits = self.router(x)  # (B, T, num_experts)
        router_probs = torch.softmax(router_logits, dim=-1)

        # Select top-k experts
        top_k_probs, top_k_indices = torch.topk(router_probs, self.top_k, dim=-1)

        # Process through selected experts
        output = torch.zeros_like(x)
        for i in range(self.top_k):
            expert_idx = top_k_indices[:, :, i]
            expert_prob = top_k_probs[:, :, i:i+1]

            # Route to expert
            for expert_id in range(self.num_experts):
                mask = (expert_idx == expert_id)
                if mask.any():
                    expert_out = self.experts[expert_id](x[mask])
                    output[mask] += expert_out * expert_prob[mask]

        return output
```

### Adding Positional Encoding

```python
class CustomPositionalEncoding(nn.Module):
    """Custom positional encoding."""

    def __init__(self, config):
        super().__init__()
        self.n_embd = config.n_embd
        self.register_buffer(
            "pos_encoding",
            self._create_encoding(config.block_size, config.n_embd)
        )

    def _create_encoding(self, max_len, d_model):
        """Create positional encoding matrix."""
        pos = torch.arange(max_len).unsqueeze(1)
        div = torch.exp(torch.arange(0, d_model, 2) * -(torch.log(torch.tensor(10000.0)) / d_model))

        encoding = torch.zeros(max_len, d_model)
        encoding[:, 0::2] = torch.sin(pos * div)
        encoding[:, 1::2] = torch.cos(pos * div)
        return encoding

    def forward(self, x):
        """Add positional encoding."""
        return x + self.pos_encoding[:x.size(1), :]
```

## Debugging Tips

1. **Start small**: Test with 2 layers, 128 hidden size
2. **Check shapes**: Print tensor shapes at each step
3. **Verify gradients**: Ensure all parameters have gradients
4. **Compare to base**: Run same config with base `GPT` model
5. **Profile memory**: Use `torch.cuda.memory_summary()`

## References

- Base model: `litgpt/model.py`
- Configuration: `litgpt/config.py`
- Adapter example: `litgpt/finetune/adapter.py`
- AdapterV2 example: `litgpt/finetune/adapter_v2.py`
- LoRA example: `litgpt/finetune/lora.py`




### Training Recipes

# Training Recipes

Complete hyperparameter configurations for LoRA, QLoRA, and full fine-tuning across different model sizes.

## Overview

LitGPT provides optimized training configurations in `config_hub/finetune/` for various model architectures and fine-tuning methods.

**Key Configuration Files**:
- `config_hub/finetune/*/lora.yaml` - LoRA fine-tuning
- `config_hub/finetune/*/qlora.yaml` - 4-bit quantized LoRA
- `config_hub/finetune/*/full.yaml` - Full fine-tuning

## LoRA Fine-tuning Recipes

### TinyLlama 1.1B LoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 8
lr_warmup_steps: 10
epochs: 3
max_seq_length: 512

# LoRA specific
lora_r: 8
lora_alpha: 16
lora_dropout: 0.05
```

**Command**:
```bash
litgpt finetune_lora TinyLlama/TinyLlama-1.1B-intermediate-step-1431k-3T \
  --data JSON \
  --data.json_path data/alpaca_sample.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 8 \
  --train.lr_warmup_steps 10 \
  --train.epochs 3 \
  --train.max_seq_length 512 \
  --lora_r 8 \
  --lora_alpha 16
```

**Memory**: ~4GB VRAM
**Time**: ~30 minutes on RTX 3090

### Llama 2 7B LoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 2
lr_warmup_steps: 10
epochs: 4
max_seq_length: 512

# LoRA specific
lora_r: 8
lora_alpha: 16
lora_dropout: 0.05
```

**Command**:
```bash
litgpt finetune_lora meta-llama/Llama-2-7b-hf \
  --data JSON \
  --data.json_path data/alpaca.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 2 \
  --train.lr_warmup_steps 10 \
  --train.epochs 4 \
  --lora_r 8 \
  --lora_alpha 16
```

**Memory**: ~16GB VRAM
**Gradient Accumulation**: 4 steps (8 / 2)
**Time**: ~6 hours on A100

### Llama 3 8B LoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 1
lr_warmup_steps: 10
epochs: 2
max_seq_length: 512

# LoRA specific
lora_r: 8
lora_alpha: 16
lora_dropout: 0.05
```

**Command**:
```bash
litgpt finetune_lora meta-llama/Llama-3.2-8B \
  --data JSON \
  --data.json_path data/custom_dataset.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 1 \
  --train.lr_warmup_steps 10 \
  --train.epochs 2 \
  --lora_r 8
```

**Memory**: ~20GB VRAM
**Gradient Accumulation**: 8 steps
**Time**: ~8 hours on A100

### Mistral 7B LoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 2
lr_warmup_steps: 10
epochs: 4
max_seq_length: 512

lora_r: 8
lora_alpha: 16
```

**Command**:
```bash
litgpt finetune_lora mistralai/Mistral-7B-v0.1 \
  --data JSON \
  --data.json_path data/alpaca.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 2 \
  --train.epochs 4 \
  --lora_r 8
```

**Memory**: ~16GB VRAM

### Phi-2 LoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 4
lr_warmup_steps: 10
epochs: 1
max_seq_length: 512

lora_r: 8
lora_alpha: 16
```

**Command**:
```bash
litgpt finetune_lora microsoft/phi-2 \
  --data JSON \
  --data.json_path data/alpaca_sample.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 4 \
  --train.epochs 1 \
  --lora_r 8
```

**Memory**: ~8GB VRAM
**Time**: ~20 minutes on RTX 3090

### Falcon 7B LoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 1
lr_warmup_steps: 10
epochs: 4
max_seq_length: 512

lora_r: 8
lora_alpha: 16
```

**Command**:
```bash
litgpt finetune_lora tiiuae/falcon-7b \
  --data JSON \
  --data.json_path data/alpaca.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 1 \
  --train.epochs 4 \
  --lora_r 8
```

**Memory**: ~18GB VRAM

### Gemma 7B LoRA

**Configuration**:
```yaml
global_batch_size: 6
micro_batch_size: 1
lr_warmup_steps: 200
epochs: 2
max_seq_length: 512

lora_r: 8
lora_alpha: 16
```

**Command**:
```bash
litgpt finetune_lora google/gemma-7b \
  --data JSON \
  --data.json_path data/alpaca.json \
  --train.global_batch_size 6 \
  --train.micro_batch_size 1 \
  --train.lr_warmup_steps 200 \
  --train.epochs 2 \
  --lora_r 8
```

**Memory**: ~18GB VRAM
**Note**: Longer warmup (200 steps) for stability

## QLoRA Fine-tuning Recipes

QLoRA uses 4-bit quantization to reduce memory by ~75%.

### TinyLlama 1.1B QLoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 8
lr_warmup_steps: 10
epochs: 3
max_seq_length: 512

lora_r: 8
lora_alpha: 16
quantize: "bnb.nf4"
```

**Command**:
```bash
litgpt finetune_lora TinyLlama/TinyLlama-1.1B-intermediate-step-1431k-3T \
  --quantize bnb.nf4 \
  --data JSON \
  --data.json_path data/alpaca_sample.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 8 \
  --train.epochs 3 \
  --lora_r 8
```

**Memory**: ~2GB VRAM (75% reduction)

### Llama 2 7B QLoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 2
lr_warmup_steps: 10
epochs: 4
max_seq_length: 512
min_lr: 6.0e-5

lora_r: 8
lora_alpha: 16
quantize: "bnb.nf4"
```

**Command**:
```bash
litgpt finetune_lora meta-llama/Llama-2-7b-hf \
  --quantize bnb.nf4 \
  --data JSON \
  --data.json_path data/alpaca.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 2 \
  --train.epochs 4 \
  --lora_r 8
```

**Memory**: ~6GB VRAM (consumer GPU friendly)

### Llama 3 8B QLoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 2
lr_warmup_steps: 10
epochs: 2
max_seq_length: 512

lora_r: 8
lora_alpha: 16
quantize: "bnb.nf4"
```

**Command**:
```bash
litgpt finetune_lora meta-llama/Llama-3.2-8B \
  --quantize bnb.nf4 \
  --data JSON \
  --data.json_path data/custom_dataset.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 2 \
  --train.epochs 2 \
  --lora_r 8
```

**Memory**: ~8GB VRAM

### Mistral 7B QLoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 2
lr_warmup_steps: 10
epochs: 4
max_seq_length: 512

lora_r: 8
lora_alpha: 16
quantize: "bnb.nf4"
```

**Memory**: ~6GB VRAM

### Phi-2 QLoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 4
lr_warmup_steps: 10
epochs: 1
max_seq_length: 512

lora_r: 8
lora_alpha: 16
quantize: "bnb.nf4"
```

**Memory**: ~3GB VRAM

### Falcon 7B QLoRA

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 1
lr_warmup_steps: 10
epochs: 4
max_seq_length: 512

lora_r: 8
lora_alpha: 16
quantize: "bnb.nf4"
```

**Memory**: ~6GB VRAM

### Gemma 2B QLoRA

**Configuration**:
```yaml
global_batch_size: 6
micro_batch_size: 2
lr_warmup_steps: 200
epochs: 2
max_seq_length: 512

lora_r: 8
lora_alpha: 16
quantize: "bnb.nf4"
```

**Memory**: ~3GB VRAM

### Gemma 7B QLoRA

**Configuration**:
```yaml
global_batch_size: 6
micro_batch_size: 1
lr_warmup_steps: 200
epochs: 2
max_seq_length: 512

lora_r: 8
lora_alpha: 16
quantize: "bnb.nf4"
```

**Memory**: ~6GB VRAM

## Full Fine-tuning Recipes

Full fine-tuning updates all model parameters (requires more memory).

### TinyLlama 1.1B Full

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 2
lr_warmup_steps: 100
epochs: 3
max_seq_length: 512
learning_rate: 5e-5
```

**Command**:
```bash
litgpt finetune_full TinyLlama/TinyLlama-1.1B-intermediate-step-1431k-3T \
  --data JSON \
  --data.json_path data/alpaca.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 2 \
  --train.lr_warmup_steps 100 \
  --train.epochs 3 \
  --train.learning_rate 5e-5
```

**Memory**: ~12GB VRAM
**Time**: ~4 hours on A100

### Phi-2 Full

**Configuration**:
```yaml
global_batch_size: 8
micro_batch_size: 1
lr_warmup_steps: 100
epochs: 2
max_seq_length: 512
learning_rate: 3e-5
```

**Command**:
```bash
litgpt finetune_full microsoft/phi-2 \
  --data JSON \
  --data.json_path data/alpaca.json \
  --train.global_batch_size 8 \
  --train.micro_batch_size 1 \
  --train.epochs 2 \
  --train.learning_rate 3e-5
```

**Memory**: ~24GB VRAM

## Common Hyperparameter Patterns

### Learning Rates

| Model Size | LoRA LR | Full Fine-tune LR |
|------------|---------|-------------------|
| <2B | 3e-4 | 5e-5 |
| 2-10B | 1e-4 | 3e-5 |
| 10-70B | 5e-5 | 1e-5 |

### LoRA Rank (r)

- **r=8**: Default, good balance (recommended)
- **r=16**: More capacity, 2× trainable params
- **r=32**: Maximum capacity, slower training
- **r=4**: Minimal, fastest training

**Rule of thumb**: Start with r=8, increase if underfitting.

### Batch Sizes

| GPU VRAM | Micro Batch | Global Batch |
|----------|-------------|--------------|
| 8GB | 1 | 8 |
| 16GB | 2 | 8-16 |
| 40GB | 4 | 16-32 |
| 80GB | 8 | 32-64 |

### Warmup Steps

- **Small models (<2B)**: 10-50 steps
- **Medium models (2-10B)**: 100-200 steps
- **Large models (>10B)**: 200-500 steps

### Epochs

- **Instruction tuning**: 1-3 epochs
- **Domain adaptation**: 3-5 epochs
- **Small datasets (<10K)**: 5-10 epochs

## Advanced Configurations

### Custom Learning Rate Schedule

```bash
litgpt finetune_lora meta-llama/Llama-2-7b-hf \
  --train.learning_rate 3e-4 \
  --train.lr_warmup_steps 100 \
  --train.min_lr 3e-6 \
  --train.lr_decay_iters 10000
```

### Gradient Accumulation

```bash
# Simulate global_batch_size=128 with 16GB GPU
litgpt finetune_lora meta-llama/Llama-2-7b-hf \
  --train.global_batch_size 128 \
  --train.micro_batch_size 2
# Accumulates over 64 steps (128 / 2)
```

### Mixed Precision

```bash
litgpt finetune_lora meta-llama/Llama-2-7b-hf \
  --precision bf16-mixed  # BF16 mixed precision
# or
  --precision 16-mixed  # FP16 mixed precision
```

### Longer Context

```bash
litgpt finetune_lora meta-llama/Llama-3.1-8B \
  --train.max_seq_length 8192 \
  --train.micro_batch_size 1  # Reduce batch for memory
```

## Memory Optimization

### Out of Memory? Try These

1. **Enable quantization**:
   ```bash
   --quantize bnb.nf4  # 4-bit QLoRA
   ```

2. **Reduce batch size**:
   ```bash
   --train.micro_batch_size 1
   ```

3. **Lower LoRA rank**:
   ```bash
   --lora_r 4  # Instead of 8
   ```

4. **Use FSDP** (multi-GPU):
   ```bash
   litgpt finetune_lora meta-llama/Llama-2-7b-hf \
     --devices 4  # Use 4 GPUs with FSDP
   ```

5. **Gradient checkpointing**:
   ```bash
   --train.gradient_accumulation_iters 16
   ```

## Data Format

LitGPT expects JSON data in instruction format:

```json
[
  {
    "instruction": "What is the capital of France?",
    "input": "",
    "output": "The capital of France is Paris."
  },
  {
    "instruction": "Translate to Spanish:",
    "input": "Hello world",
    "output": "Hola mundo"
  }
]
```

**Load custom data**:
```bash
litgpt finetune_lora meta-llama/Llama-2-7b-hf \
  --data JSON \
  --data.json_path data/my_dataset.json \
  --data.val_split_fraction 0.1  # 10% validation
```

## Merge and Deploy

After fine-tuning, merge LoRA weights:

```bash
litgpt merge_lora checkpoints/meta-llama/Llama-2-7b-hf/final_lora.pth
```

Generate with merged model:

```bash
litgpt generate checkpoints/meta-llama/Llama-2-7b-hf-merged/ \
  --prompt "What is machine learning?"
```

Or serve via API:

```bash
litgpt serve checkpoints/meta-llama/Llama-2-7b-hf-merged/
```

## References

- Configuration hub: `config_hub/finetune/`
- Fine-tuning tutorial: `tutorials/finetune_*.md`
- Memory guide: `tutorials/oom.md`




### Supported Models

# Supported Models

Complete list of model architectures supported by LitGPT with parameter sizes and variants.

## Overview

LitGPT supports **20+ model families** with **100+ model variants** ranging from 135M to 405B parameters.

**List all models**:
```bash
litgpt download list
```

**List pretrain-capable models**:
```bash
litgpt pretrain list
```

## Model Families

### Llama Family

**Llama 3, 3.1, 3.2, 3.3**:
- **Sizes**: 1B, 3B, 8B, 70B, 405B
- **Use Cases**: General-purpose, long-context (128K), multimodal
- **Best For**: Production applications, research, instruction following

**Code Llama**:
- **Sizes**: 7B, 13B, 34B, 70B
- **Use Cases**: Code generation, completion, infilling
- **Best For**: Programming assistants, code analysis

**Function Calling Llama 2**:
- **Sizes**: 7B
- **Use Cases**: Tool use, API integration
- **Best For**: Agents, function execution

**Llama 2**:
- **Sizes**: 7B, 13B, 70B
- **Use Cases**: General-purpose (predecessor to Llama 3)
- **Best For**: Established baselines, research comparisons

**Llama 3.1 Nemotron**:
- **Sizes**: 70B
- **Use Cases**: NVIDIA-optimized variant
- **Best For**: Enterprise deployments

**TinyLlama**:
- **Sizes**: 1.1B
- **Use Cases**: Edge devices, resource-constrained environments
- **Best For**: Fast inference, mobile deployment

**OpenLLaMA**:
- **Sizes**: 3B, 7B, 13B
- **Use Cases**: Open-source Llama reproduction
- **Best For**: Research, education

**Vicuna**:
- **Sizes**: 7B, 13B, 33B
- **Use Cases**: Chatbot, instruction following
- **Best For**: Conversational AI

**R1 Distill Llama**:
- **Sizes**: 8B, 70B
- **Use Cases**: Distilled reasoning models
- **Best For**: Efficient reasoning tasks

**MicroLlama**:
- **Sizes**: 300M
- **Use Cases**: Extremely small Llama variant
- **Best For**: Prototyping, testing

**Platypus**:
- **Sizes**: 7B, 13B, 70B
- **Use Cases**: STEM-focused fine-tune
- **Best For**: Science, math, technical domains

### Mistral Family

**Mistral**:
- **Sizes**: 7B, 123B
- **Use Cases**: Efficient open models, long-context
- **Best For**: Cost-effective deployments

**Mathstral**:
- **Sizes**: 7B
- **Use Cases**: Math reasoning
- **Best For**: Mathematical problem solving

**Mixtral MoE**:
- **Sizes**: 8×7B (47B total, 13B active), 8×22B (141B total, 39B active)
- **Use Cases**: Sparse mixture of experts
- **Best For**: High capacity with lower compute

### Falcon Family

**Falcon**:
- **Sizes**: 7B, 40B, 180B
- **Use Cases**: Open-source models from TII
- **Best For**: Multilingual applications

**Falcon 3**:
- **Sizes**: 1B, 3B, 7B, 10B
- **Use Cases**: Newer Falcon generation
- **Best For**: Efficient multilingual models

### Phi Family (Microsoft)

**Phi 1.5 & 2**:
- **Sizes**: 1.3B, 2.7B
- **Use Cases**: Small language models with strong performance
- **Best For**: Edge deployment, low-resource environments

**Phi 3 & 3.5**:
- **Sizes**: 3.8B
- **Use Cases**: Improved small models
- **Best For**: Mobile, browser-based applications

**Phi 4**:
- **Sizes**: 14B
- **Use Cases**: Medium-size high-performance model
- **Best For**: Balance of size and capability

**Phi 4 Mini Instruct**:
- **Sizes**: 3.8B
- **Use Cases**: Instruction-tuned variant
- **Best For**: Chat, task completion

### Gemma Family (Google)

**Gemma**:
- **Sizes**: 2B, 7B
- **Use Cases**: Google's open models
- **Best For**: Research, education

**Gemma 2**:
- **Sizes**: 2B, 9B, 27B
- **Use Cases**: Second generation improvements
- **Best For**: Enhanced performance

**Gemma 3**:
- **Sizes**: 1B, 4B, 12B, 27B
- **Use Cases**: Latest Gemma generation
- **Best For**: State-of-the-art open models

**CodeGemma**:
- **Sizes**: 7B
- **Use Cases**: Code-specialized Gemma
- **Best For**: Code generation, analysis

### Qwen Family (Alibaba)

**Qwen2.5**:
- **Sizes**: 0.5B, 1.5B, 3B, 7B, 14B, 32B, 72B
- **Use Cases**: General-purpose multilingual models
- **Best For**: Chinese/English applications

**Qwen2.5 Coder**:
- **Sizes**: 0.5B, 1.5B, 3B, 7B, 14B, 32B
- **Use Cases**: Code-specialized variants
- **Best For**: Programming in multiple languages

**Qwen2.5 Math**:
- **Sizes**: 1.5B, 7B, 72B
- **Use Cases**: Mathematical reasoning
- **Best For**: Math problems, STEM education

**QwQ & QwQ-Preview**:
- **Sizes**: 32B
- **Use Cases**: Question-answering focus
- **Best For**: Reasoning tasks

### Pythia Family (EleutherAI)

**Pythia**:
- **Sizes**: 14M, 31M, 70M, 160M, 410M, 1B, 1.4B, 2.8B, 6.9B, 12B
- **Use Cases**: Research, interpretability
- **Best For**: Scientific studies, ablations

### StableLM Family (Stability AI)

**StableLM**:
- **Sizes**: 3B, 7B
- **Use Cases**: Open models from Stability AI
- **Best For**: Research, commercial use

**StableLM Zephyr**:
- **Sizes**: 3B
- **Use Cases**: Instruction-tuned variant
- **Best For**: Chat applications

**StableCode**:
- **Sizes**: 3B
- **Use Cases**: Code generation
- **Best For**: Programming tasks

**FreeWilly2 (Stable Beluga 2)**:
- **Sizes**: 70B
- **Use Cases**: Large Stability AI model
- **Best For**: High-capability tasks

### Other Models

**Danube2**:
- **Sizes**: 1.8B
- **Use Cases**: Efficient small model
- **Best For**: Resource-constrained environments

**Dolly**:
- **Sizes**: 3B, 7B, 12B
- **Use Cases**: Databricks' instruction-following model
- **Best For**: Enterprise applications

**LongChat**:
- **Sizes**: 7B, 13B
- **Use Cases**: Extended context windows
- **Best For**: Long-document understanding

**Nous-Hermes**:
- **Sizes**: 7B, 13B, 70B
- **Use Cases**: Instruction-following fine-tune
- **Best For**: Task completion, reasoning

**OLMo**:
- **Sizes**: 1B, 7B
- **Use Cases**: Allen AI's fully open model
- **Best For**: Research transparency

**RedPajama-INCITE**:
- **Sizes**: 3B, 7B
- **Use Cases**: Open reproduction project
- **Best For**: Research, education

**Salamandra**:
- **Sizes**: 2B, 7B
- **Use Cases**: Multilingual European model
- **Best For**: European language support

**SmolLM2**:
- **Sizes**: 135M, 360M, 1.7B
- **Use Cases**: Ultra-small models
- **Best For**: Edge devices, testing

## Download Examples

**Download specific model**:
```bash
litgpt download meta-llama/Llama-3.2-1B
litgpt download microsoft/phi-2
litgpt download google/gemma-2-9b
```

**Download with HuggingFace token** (for gated models):
```bash
export HF_TOKEN=hf_...
litgpt download meta-llama/Llama-3.1-405B
```

## Model Selection Guide

### By Use Case

**General Chat/Instruction Following**:
- Small: Phi-2 (2.7B), TinyLlama (1.1B)
- Medium: Llama-3.2-8B, Mistral-7B
- Large: Llama-3.1-70B, Mixtral-8x22B

**Code Generation**:
- Small: Qwen2.5-Coder-3B
- Medium: CodeLlama-13B, CodeGemma-7B
- Large: CodeLlama-70B, Qwen2.5-Coder-32B

**Math/Reasoning**:
- Small: Qwen2.5-Math-1.5B
- Medium: Mathstral-7B, Qwen2.5-Math-7B
- Large: QwQ-32B, Qwen2.5-Math-72B

**Multilingual**:
- Small: SmolLM2-1.7B
- Medium: Qwen2.5-7B, Falcon-7B
- Large: Qwen2.5-72B

**Research/Education**:
- Pythia family (14M-12B for ablations)
- OLMo (fully open)
- TinyLlama (fast iteration)

### By Hardware

**Consumer GPU (8-16GB VRAM)**:
- Phi-2 (2.7B)
- TinyLlama (1.1B)
- Gemma-2B
- SmolLM2 family

**Single A100 (40-80GB)**:
- Llama-3.2-8B
- Mistral-7B
- CodeLlama-13B
- Gemma-9B

**Multi-GPU (200GB+ total)**:
- Llama-3.1-70B (TP=4)
- Mixtral-8x22B (TP=2)
- Falcon-40B

**Large Cluster**:
- Llama-3.1-405B (FSDP)
- Falcon-180B

## Model Capabilities

### Context Lengths

| Model | Context Window |
|-------|----------------|
| Llama 3.1 | 128K |
| Llama 3.2/3.3 | 128K |
| Mistral-123B | 128K |
| Mixtral | 32K |
| Gemma 2 | 8K |
| Phi-3 | 128K |
| Qwen2.5 | 32K |

### Training Data

- **Llama 3**: 15T tokens (multilingual)
- **Mistral**: Web data, code
- **Qwen**: Multilingual (Chinese/English focus)
- **Pythia**: The Pile (controlled training)

## References

- LitGPT GitHub: https://github.com/Lightning-AI/litgpt
- Model configs: `litgpt/config.py`
- Download tutorial: `tutorials/download_model_weights.md`




### Distributed Training

# Distributed Training

Guide to FSDP (Fully Sharded Data Parallel) distributed training in LitGPT for scaling to multiple GPUs and nodes.

## Overview

LitGPT uses **Lightning Fabric** with **FSDP** to distribute training across multiple GPUs. FSDP shards model parameters, gradients, and optimizer states to enable training models larger than single-GPU memory.

**When to use FSDP**:
- Model doesn't fit on single GPU
- Want faster training with multi-GPU
- Training models >7B parameters
- Need to scale across multiple nodes

## Quick Start

### Single Node Multi-GPU

```bash
# Train Llama 2 7B on 4 GPUs
litgpt finetune_lora meta-llama/Llama-2-7b-hf \
  --devices 4 \
  --data JSON \
  --data.json_path data/alpaca.json
```

FSDP is **automatically enabled** when `devices > 1`.

### Multi-Node Training

```bash
# Train on 2 nodes with 8 GPUs each (16 total)
litgpt finetune_lora meta-llama/Llama-2-70b-hf \
  --devices 8 \
  --num_nodes 2 \
  --data JSON \
  --data.json_path data/alpaca.json
```

## FSDP Configuration

### Default FSDP Strategy

When multiple devices are used, LitGPT applies this FSDP configuration:

```python
from lightning.fabric.strategies import FSDPStrategy
from litgpt.model import Block

strategy = FSDPStrategy(
    auto_wrap_policy={Block},
    state_dict_type="full",
    sharding_strategy="HYBRID_SHARD"
)
```

**Parameters**:
- `auto_wrap_policy={Block}`: Automatically wraps each transformer `Block` with FSDP
- `state_dict_type="full"`: Saves full model (assembled on rank 0) for easy deployment
- `sharding_strategy="HYBRID_SHARD"`: Shards parameters, gradients, and optimizer states

### Sharding Strategies

| Strategy | Shards | Communication | Use Case |
|----------|--------|---------------|----------|
| `FULL_SHARD` (ZeRO-3) | Params + Grads + Optim | All-gather before forward/backward | Maximum memory savings |
| `SHARD_GRAD_OP` (ZeRO-2) | Grads + Optim only | Reduce-scatter after backward | Faster than FULL_SHARD |
| `HYBRID_SHARD` (default) | All (hybrid across nodes) | Optimized for multi-node | Best for clusters |
| `NO_SHARD` | None | Broadcast | Single GPU (no FSDP) |

**Recommendation**: Use default `HYBRID_SHARD` for multi-node, or `FULL_SHARD` for single-node multi-GPU.

### State Dict Types

| Type | Behavior | Use Case |
|------|----------|----------|
| `full` (default) | Gathers all shards on rank 0, saves single file | Easy deployment, inference |
| `sharded` | Each rank saves its shard separately | Faster checkpointing, resume training |

### Auto-Wrap Policy

FSDP wraps model components based on `auto_wrap_policy`:

```python
auto_wrap_policy={Block}  # Wrap each transformer block
```

This means each `Block` (transformer layer) is independently sharded across GPUs. For a 32-layer model on 4 GPUs, each GPU holds ~8 layer shards.

## Thunder FSDP (Advanced)

LitGPT includes an experimental **Thunder** extension with enhanced FSDP:

```bash
litgpt pretrain tiny-llama-1.1b \
  --devices 8 \
  --num_nodes 1 \
  --compiler thunder \
  --strategy fsdp
```

### Thunder FSDP Configuration

```python
from extensions.thunder.pretrain import ThunderFSDPStrategy

strategy = ThunderFSDPStrategy(
    sharding_strategy="ZERO3",
    bucketing_strategy="BLOCK",
    state_dict_type="full",
    jit=False,
)
```

**Additional Parameters**:
- `sharding_strategy`: `"ZERO3"` (full shard), `"ZERO2"` (grad/optim only)
- `bucketing_strategy`: `"BLOCK"` (combine ops per block), `"LAYER"` (per layer), `"NONE"` (no bucketing)
- `jit`: Whether to apply `thunder.jit(model)` for optimization
- `executors`: Tuple of Thunder executors to enable

**Bucketing Strategy**:
- `"BLOCK"` (default): Combines collective operations for layer blocks → fewer communication calls
- `"LAYER"`: Combines per layer class
- `"NONE"`: No bucketing → more fine-grained but more overhead

## Pretraining with FSDP

### Single Node

```bash
litgpt pretrain tiny-llama-1.1b \
  --devices 8 \
  --train.global_batch_size 512 \
  --train.micro_batch_size 8 \
  --data Alpaca2k
```

**Memory calculation**:
- TinyLlama 1.1B: ~4GB model + ~4GB gradients + ~8GB optimizer = 16GB per GPU without FSDP
- With FSDP on 8 GPUs: 16GB / 8 = 2GB per GPU ✅ Fits easily

### Multi-Node

```bash
# Launch on 4 nodes with 8 GPUs each (32 total)
litgpt pretrain llama-2-7b \
  --devices 8 \
  --num_nodes 4 \
  --train.global_batch_size 1024 \
  --train.micro_batch_size 2 \
  --data RedPajama
```

**Memory calculation**:
- Llama 2 7B: ~28GB model + ~28GB gradients + ~56GB optimizer = 112GB total
- With FSDP on 32 GPUs: 112GB / 32 = 3.5GB per GPU ✅

## Fine-tuning with FSDP

### LoRA Fine-tuning (Recommended)

LoRA fine-tuning with FSDP for >7B models:

```bash
# Llama 2 70B LoRA on 8 GPUs
litgpt finetune_lora meta-llama/Llama-2-70b-hf \
  --devices 8 \
  --data JSON \
  --data.json_path data/alpaca.json \
  --train.global_batch_size 16 \
  --train.micro_batch_size 1 \
  --lora_r 8
```

**Why LoRA with FSDP**:
- Base model sharded with FSDP (memory efficient)
- Only LoRA adapters trained (fast)
- Best of both worlds for large models

### Full Fine-tuning

Full fine-tuning with FSDP:

```bash
# Llama 2 7B full fine-tune on 4 GPUs
litgpt finetune_full meta-llama/Llama-2-7b-hf \
  --devices 4 \
  --data JSON \
  --data.json_path data/alpaca.json \
  --train.global_batch_size 16 \
  --train.micro_batch_size 1 \
  --train.learning_rate 3e-5
```

## Mixed Precision

FSDP works with mixed precision for memory savings and speedup:

```bash
# BF16 mixed precision (recommended for A100/H100)
litgpt pretrain tiny-llama-1.1b \
  --devices 8 \
  --precision bf16-mixed

# FP16 mixed precision (V100 compatible)
litgpt pretrain tiny-llama-1.1b \
  --devices 8 \
  --precision 16-mixed
```

**Precision options**:
- `bf16-mixed`: BF16 for computation, FP32 for master weights (best for Ampere+)
- `16-mixed`: FP16 for computation, FP32 for master weights (V100)
- `32-true`: Full FP32 (debugging only, slow)

## Gradient Accumulation

Simulate larger batch sizes with gradient accumulation:

```bash
# Simulate global_batch_size=512 with micro_batch_size=2
litgpt pretrain tiny-llama-1.1b \
  --devices 8 \
  --train.global_batch_size 512 \
  --train.micro_batch_size 2
# Accumulates over 512/(8*2) = 32 steps per optimizer update
```

**Formula**:
```
Gradient accumulation steps = global_batch_size / (devices × micro_batch_size)
```

## Memory Optimization

### Out of Memory? Try These

1. **Increase devices**:
   ```bash
   --devices 8  # Instead of 4
   ```

2. **Reduce micro batch size**:
   ```bash
   --train.micro_batch_size 1  # Instead of 2
   ```

3. **Lower precision**:
   ```bash
   --precision bf16-mixed  # Instead of 32-true
   ```

4. **Use FULL_SHARD**:
   ```python
   strategy = FSDPStrategy(
       sharding_strategy="FULL_SHARD"  # Maximum memory savings
   )
   ```

5. **Enable activation checkpointing** (implemented in model):
   ```python
   # Recomputes activations during backward pass
   # Trades compute for memory
   ```

6. **Use QLoRA**:
   ```bash
   litgpt finetune_lora meta-llama/Llama-2-7b-hf \
     --quantize bnb.nf4 \
     --devices 1  # May not need FSDP with quantization
   ```

## Checkpointing

### Save Checkpoints

FSDP automatically handles checkpoint saving:

```bash
litgpt pretrain tiny-llama-1.1b \
  --devices 8 \
  --out_dir checkpoints/tinyllama-pretrain
# Saves to: checkpoints/tinyllama-pretrain/final/lit_model.pth
```

With `state_dict_type="full"` (default), rank 0 assembles full model and saves single file.

### Resume Training

```bash
litgpt pretrain tiny-llama-1.1b \
  --devices 8 \
  --resume checkpoints/tinyllama-pretrain/
# Automatically loads latest checkpoint
```

### Convert to HuggingFace

```bash
python scripts/convert_lit_checkpoint.py \
  --checkpoint_path checkpoints/tinyllama-pretrain/final/lit_model.pth \
  --output_dir models/tinyllama-hf
```

## Performance Tuning

### Communication Backends

LitGPT uses NCCL for GPU communication:

```bash
# Default (NCCL auto-configured)
litgpt pretrain tiny-llama-1.1b --devices 8

# Explicit NCCL settings (advanced)
NCCL_DEBUG=INFO \
NCCL_IB_DISABLE=0 \
litgpt pretrain tiny-llama-1.1b --devices 8
```

**NCCL Environment Variables**:
- `NCCL_DEBUG=INFO`: Enable debug logging
- `NCCL_IB_DISABLE=0`: Use InfiniBand (if available)
- `NCCL_SOCKET_IFNAME=eth0`: Specify network interface

### Multi-Node Setup

**Option 1: SLURM**

```bash
#!/bin/bash
#SBATCH --nodes=4
#SBATCH --gpus-per-node=8
#SBATCH --ntasks-per-node=1

srun litgpt pretrain llama-2-7b \
  --devices 8 \
  --num_nodes 4 \
  --data RedPajama
```

**Option 2: torchrun**

```bash
# On each node, run:
torchrun \
  --nproc_per_node=8 \
  --nnodes=4 \
  --node_rank=$NODE_RANK \
  --master_addr=$MASTER_ADDR \
  --master_port=29500 \
  -m litgpt pretrain llama-2-7b
```

### Profiling

Enable profiling to identify bottlenecks:

```bash
litgpt pretrain tiny-llama-1.1b \
  --devices 8 \
  --train.max_steps 100 \
  --profile
# Generates profiling report
```

## Example Configurations

### Llama 2 7B on 4× A100 (40GB)

```bash
litgpt finetune_lora meta-llama/Llama-2-7b-hf \
  --devices 4 \
  --precision bf16-mixed \
  --train.global_batch_size 64 \
  --train.micro_batch_size 4 \
  --train.max_seq_length 2048 \
  --lora_r 8 \
  --data JSON \
  --data.json_path data/alpaca.json
```

**Memory per GPU**: ~20GB
**Throughput**: ~5 samples/sec

### Llama 2 70B on 8× A100 (80GB)

```bash
litgpt finetune_lora meta-llama/Llama-2-70b-hf \
  --devices 8 \
  --precision bf16-mixed \
  --train.global_batch_size 32 \
  --train.micro_batch_size 1 \
  --train.max_seq_length 2048 \
  --lora_r 8 \
  --data JSON \
  --data.json_path data/alpaca.json
```

**Memory per GPU**: ~70GB
**Throughput**: ~1 sample/sec

### Llama 3 405B on 64× H100 (80GB)

```bash
litgpt finetune_lora meta-llama/Llama-3.1-405B \
  --devices 8 \
  --num_nodes 8 \
  --precision bf16-mixed \
  --train.global_batch_size 128 \
  --train.micro_batch_size 1 \
  --train.max_seq_length 4096 \
  --lora_r 16 \
  --data JSON \
  --data.json_path data/alpaca.json
```

**Memory per GPU**: ~60GB
**Requires**: 64 H100 GPUs (8 nodes × 8 GPUs)

## Troubleshooting

### "CUDA out of memory"

1. Reduce `micro_batch_size`
2. Increase `devices` (more sharding)
3. Lower `max_seq_length`
4. Use `bf16-mixed` precision
5. Try QLoRA (`--quantize bnb.nf4`)

### "NCCL error" or Slow Communication

1. Check network connectivity between nodes
2. Enable InfiniBand: `NCCL_IB_DISABLE=0`
3. Verify NCCL version: `python -c "import torch; print(torch.cuda.nccl.version())"`
4. Test with NCCL tests: `$NCCL_HOME/build/all_reduce_perf -b 8 -e 128M`

### Training Slower Than Expected

1. Profile with `--profile`
2. Check GPU utilization: `nvidia-smi dmon`
3. Verify data loading isn't bottleneck
4. Increase `micro_batch_size` if memory allows
5. Use Thunder FSDP with bucketing

## References

- FSDP configuration: `litgpt/pretrain.py:setup()`
- Thunder FSDP: `extensions/thunder/pretrain.py`
- Memory optimization guide: `tutorials/oom.md`
- Lightning Fabric docs: https://lightning.ai/docs/fabric/




---

## 🚀 Usage

**Reference this template:** `@skill-implementing-llms-litgpt.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
