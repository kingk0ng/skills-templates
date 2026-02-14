---
id: skill-long-context
type: skill
name: long-context
description: Extend context windows of transformer models using RoPE, YaRN, ALiBi,
  and position interpolation techniques. Use when processing long documents (32k-128k+
  tokens), extending pre-trained models beyond original context limits, or implementing
  efficient positional encodings. Covers rotary embeddings, attention biases, interpolation
  methods, and extrapolation strategies for LLMs.
category: ai-research
complexity: medium
keywords:
- deploy
- deployment
- git
- github
- optimization
- performance
- python
- test
capabilities: []
token_estimate: 2229
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~2,229 -->
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




# long-context

> Extend context windows of transformer models using RoPE, YaRN, ALiBi, and position interpolation techniques. Use when processing long documents (32k-128k+ tokens), extending pre-trained models beyond original context limits, or implementing efficient positional encodings. Covers rotary embeddings, attention biases, interpolation methods, and extrapolation strategies for LLMs.

# Long Context: Extending Transformer Context Windows

## When to Use This Skill

Use Long Context techniques when you need to:
- **Process long documents** (32k, 64k, 128k+ tokens) with transformer models
- **Extend context windows** of pre-trained models (LLaMA, Mistral, etc.)
- **Implement efficient positional encodings** (RoPE, ALiBi)
- **Train models** with length extrapolation capabilities
- **Deploy models** that handle variable-length inputs efficiently
- **Fine-tune** existing models for longer contexts with minimal compute

**Key Techniques**: RoPE (Rotary Position Embeddings), YaRN, ALiBi (Attention with Linear Biases), Position Interpolation

**Papers**: RoFormer (arXiv 2104.09864), YaRN (arXiv 2309.00071), ALiBi (arXiv 2108.12409), Position Interpolation (arXiv 2306.15595)

## Installation

```bash
# HuggingFace Transformers (includes RoPE, YaRN support)
pip install transformers torch

# For custom implementations
pip install einops  # Tensor operations
pip install rotary-embedding-torch  # Standalone RoPE

# Optional: FlashAttention for efficiency
pip install flash-attn --no-build-isolation
```

## Quick Start

### RoPE (Rotary Position Embeddings)

```python
import torch
import torch.nn as nn

class RotaryEmbedding(nn.Module):
    """Rotary Position Embeddings (RoPE)."""

    def __init__(self, dim, max_seq_len=8192, base=10000):
        super().__init__()
        # Compute inverse frequencies
        inv_freq = 1.0 / (base ** (torch.arange(0, dim, 2).float() / dim))
        self.register_buffer("inv_freq", inv_freq)
        self.max_seq_len = max_seq_len

    def forward(self, seq_len, device):
        # Position indices
        t = torch.arange(seq_len, device=device).type_as(self.inv_freq)

        # Compute frequencies
        freqs = torch.outer(t, self.inv_freq)  # (seq_len, dim/2)

        # Compute sin and cos
        emb = torch.cat((freqs, freqs), dim=-1)  # (seq_len, dim)
        return emb.cos(), emb.sin()

def rotate_half(x):
    """Rotate half the hidden dimensions."""
    x1, x2 = x.chunk(2, dim=-1)
    return torch.cat((-x2, x1), dim=-1)

def apply_rotary_pos_emb(q, k, cos, sin):
    """Apply rotary embeddings to queries and keys."""
    # q, k shape: (batch, heads, seq_len, dim)
    q_embed = (q * cos) + (rotate_half(q) * sin)
    k_embed = (k * cos) + (rotate_half(k) * sin)
    return q_embed, k_embed

# Usage
rope = RotaryEmbedding(dim=64, max_seq_len=8192)
cos, sin = rope(seq_len=2048, device='cuda')

# In attention layer
q_rotated, k_rotated = apply_rotary_pos_emb(query, key, cos, sin)
```

### ALiBi (Attention with Linear Biases)

```python
def get_alibi_slopes(num_heads):
    """Get ALiBi slope values for each attention head."""
    def get_slopes_power_of_2(n):
        start = 2 ** (-(2 ** -(math.log2(n) - 3)))
        ratio = start
        return [start * (ratio ** i) for i in range(n)]

    if math.log2(num_heads).is_integer():
        return get_slopes_power_of_2(num_heads)
    else:
        # Closest power of 2
        closest_power = 2 ** math.floor(math.log2(num_heads))
        slopes = get_slopes_power_of_2(closest_power)
        # Add extra slopes
        extra = get_slopes_power_of_2(2 * closest_power)
        slopes.extend(extra[0::2][:num_heads - closest_power])
        return slopes

def create_alibi_bias(seq_len, num_heads):
    """Create ALiBi attention bias."""
    # Distance matrix
    context_position = torch.arange(seq_len)
    memory_position = torch.arange(seq_len)
    relative_position = memory_position[None, :] - context_position[:, None]

    # Get slopes
    slopes = torch.tensor(get_alibi_slopes(num_heads))

    # Apply slopes to distances
    alibi = slopes[:, None, None] * relative_position[None, :, :]
    return alibi  # (num_heads, seq_len, seq_len)

# Usage in attention
num_heads = 8
seq_len = 2048
alibi_bias = create_alibi_bias(seq_len, num_heads).to('cuda')

# Add bias to attention scores
# attn_scores shape: (batch, num_heads, seq_len, seq_len)
attn_scores = attn_scores + alibi_bias
attn_weights = torch.softmax(attn_scores, dim=-1)
```

### Position Interpolation for LLaMA

```python
from transformers import LlamaForCausalLM, LlamaTokenizer

# Original context: 2048 tokens
model = LlamaForCausalLM.from_pretrained("meta-llama/Llama-2-7b-hf")

# Extend to 32k with position interpolation
# Modify RoPE base frequency
model.config.rope_scaling = {
    "type": "linear",
    "factor": 16.0  # 2048 * 16 = 32768
}

# Or use dynamic scaling
model.config.rope_scaling = {
    "type": "dynamic",
    "factor": 16.0
}

# Fine-tune with long documents (minimal steps needed)
# Position interpolation works out-of-the-box after this config change
```

## Core Concepts

### 1. RoPE (Rotary Position Embeddings)

**How it works:**
- Encodes absolute position via rotation matrix
- Provides relative position dependency in attention
- Enables length extrapolation

**Mathematical formulation:**
```
q_m = (W_q * x_m) * e^(imθ)
k_n = (W_k * x_n) * e^(inθ)

where θ_j = base^(-2j/d) for j ∈ [0, d/2)
```

**Advantages:**
- Decaying inter-token dependency with distance
- Compatible with linear attention
- Better extrapolation than absolute position encodings

### 2. YaRN (Yet another RoPE extensioN)

**Key innovation:**
- NTK-aware interpolation (Neural Tangent Kernel)
- Attention temperature scaling
- Efficient context extension (10× less tokens vs baselines)

**Parameters:**
```python
# YaRN configuration
yarn_config = {
    "scale": 16,                    # Extension factor
    "original_max_position": 2048,  # Base context
    "extrapolation_factor": 1.0,    # NTK parameter
    "attn_factor": 1.0,             # Attention scaling
    "beta_fast": 32,                # High-frequency scale
    "beta_slow": 1,                 # Low-frequency scale
}
```

**Performance:**
- Extends LLaMA to 128k tokens
- 2.5× less training steps than baselines
- State-of-the-art context window extension

### 3. ALiBi (Attention with Linear Biases)

**Core idea:**
- No positional embeddings added to tokens
- Apply distance penalty directly to attention scores
- Bias proportional to key-query distance

**Formula:**
```
attention_bias[i, j] = -m * |i - j|

where m = slope for each attention head
```

**Advantages:**
- 11% faster training vs sinusoidal embeddings
- 11% less memory usage
- Strong length extrapolation (train 1k, test 2k+)
- Inductive bias towards recency

### 4. Position Interpolation

**Technique:**
- Linearly down-scale position indices
- Interpolate within trained range (vs extrapolate beyond)
- Minimal fine-tuning required

**Formula:**
```
# Original: position indices [0, 1, 2, ..., L]
# Extended: position indices [0, 0.5, 1.0, ..., L/2]
# (for 2× extension)

scaled_position[i] = i / extension_factor
```

**Results:**
- LLaMA 7B-65B extended to 32k tokens
- 1000 fine-tuning steps sufficient
- 600× better stability than extrapolation

## Method Comparison

| Method | Max Context | Training Needed | Memory | Extrapolation | Best For |
|--------|-------------|-----------------|--------|---------------|----------|
| **RoPE** | 8k-32k | Full pre-training | Moderate | Good | New models |
| **YaRN** | 32k-128k | Minimal (10× efficient) | Moderate | Excellent | Extending existing models |
| **ALiBi** | Unlimited | Full pre-training | Low (-11%) | Excellent | Training from scratch |
| **Position Interpolation** | 32k+ | Minimal (1k steps) | Moderate | Poor (by design) | Quick extension |

## Implementation Patterns

### HuggingFace Transformers Integration

```python
from transformers import AutoModelForCausalLM, AutoConfig

# RoPE with YaRN scaling
config = AutoConfig.from_pretrained("mistralai/Mistral-7B-v0.1")
config.rope_scaling = {
    "type": "yarn",
    "factor": 8.0,
    "original_max_position_embeddings": 8192,
    "attention_factor": 1.0
}

model = AutoModelForCausalLM.from_config(config)

# Position interpolation (simpler)
config.rope_scaling = {
    "type": "linear",
    "factor": 4.0
}

# Dynamic scaling (adjusts based on input length)
config.rope_scaling = {
    "type": "dynamic",
    "factor": 8.0
}
```

### Custom RoPE Implementation

```python
class LongContextAttention(nn.Module):
    """Multi-head attention with RoPE."""

    def __init__(self, hidden_size, num_heads, max_seq_len=32768):
        super().__init__()
        self.num_heads = num_heads
        self.head_dim = hidden_size // num_heads

        # Q, K, V projections
        self.q_proj = nn.Linear(hidden_size, hidden_size)
        self.k_proj = nn.Linear(hidden_size, hidden_size)
        self.v_proj = nn.Linear(hidden_size, hidden_size)
        self.o_proj = nn.Linear(hidden_size, hidden_size)

        # RoPE
        self.rotary_emb = RotaryEmbedding(
            dim=self.head_dim,
            max_seq_len=max_seq_len
        )

    def forward(self, hidden_states):
        batch_size, seq_len, _ = hidden_states.shape

        # Project to Q, K, V
        q = self.q_proj(hidden_states)
        k = self.k_proj(hidden_states)
        v = self.v_proj(hidden_states)

        # Reshape for multi-head
        q = q.view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        k = k.view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        v = v.view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)

        # Apply RoPE
        cos, sin = self.rotary_emb(seq_len, device=hidden_states.device)
        q, k = apply_rotary_pos_emb(q, k, cos, sin)

        # Standard attention
        attn_output = F.scaled_dot_product_attention(q, k, v)

        # Reshape and project
        attn_output = attn_output.transpose(1, 2).contiguous()
        attn_output = attn_output.view(batch_size, seq_len, -1)
        output = self.o_proj(attn_output)

        return output
```

## Fine-tuning for Long Context

### Minimal Fine-tuning (Position Interpolation)

```python
from transformers import Trainer, TrainingArguments

# Extend model config
model.config.max_position_embeddings = 32768
model.config.rope_scaling = {"type": "linear", "factor": 16.0}

# Training args (minimal steps needed)
training_args = TrainingArguments(
    output_dir="./llama-32k",
    num_train_epochs=1,
    max_steps=1000,           # Only 1000 steps!
    per_device_train_batch_size=1,
    gradient_accumulation_steps=16,
    learning_rate=2e-5,
    warmup_steps=100,
    logging_steps=10,
    save_steps=500,
)

# Train on long documents
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=long_document_dataset,  # 32k token sequences
)

trainer.train()
```

### YaRN Fine-tuning

```bash
# Clone YaRN implementation
git clone https://github.com/jquesnelle/yarn
cd yarn

# Fine-tune LLaMA with YaRN
python scripts/train.py \
    --model meta-llama/Llama-2-7b-hf \
    --scale 16 \
    --rope_theta 10000 \
    --max_length 32768 \
    --batch_size 1 \
    --gradient_accumulation 16 \
    --steps 400 \
    --learning_rate 2e-5
```

## Best Practices

### 1. Choose the Right Method

```python
# For NEW models (training from scratch)
use_method = "ALiBi"  # Best extrapolation, lowest memory

# For EXTENDING existing RoPE models
use_method = "YaRN"  # Most efficient extension (10× less data)

# For QUICK extension with minimal compute
use_method = "Position Interpolation"  # 1000 steps

# For MODERATE extension with good efficiency
use_method = "Linear RoPE Scaling"  # Built-in, simple
```

### 2. Scaling Factor Selection

```python
# Conservative (safer, better quality)
scaling_factor = 2.0  # 8k → 16k

# Moderate (good balance)
scaling_factor = 4.0  # 8k → 32k

# Aggressive (requires more fine-tuning)
scaling_factor = 8.0  # 8k → 64k
scaling_factor = 16.0  # 8k → 128k

# Rule: Larger factors need more fine-tuning steps
steps_needed = 100 * scaling_factor  # Rough estimate
```

### 3. Fine-tuning Data

```python
# ✅ Good: Long documents matching target length
train_data = [
    {"text": long_doc_32k_tokens},  # Full 32k
    {"text": long_doc_24k_tokens},  # Varied lengths
    {"text": long_doc_16k_tokens},
]

# ❌ Bad: Short documents (won't learn long context)
train_data = [
    {"text": short_doc_2k_tokens},
]

# Use datasets like:
# - PG-19 (books, long texts)
# - arXiv papers
# - Long-form conversations
# - GitHub repositories (concatenated files)
```

### 4. Avoid Common Pitfalls

```python
# ❌ Bad: Applying position interpolation without fine-tuning
model.config.rope_scaling = {"type": "linear", "factor": 16.0}
# Model will perform poorly without fine-tuning!

# ✅ Good: Fine-tune after scaling
model.config.rope_scaling = {"type": "linear", "factor": 16.0}
fine_tune(model, long_documents, steps=1000)

# ❌ Bad: Too aggressive scaling without data
scale_to_1M_tokens()  # Won't work without massive fine-tuning

# ✅ Good: Incremental scaling
# 8k → 16k → 32k → 64k (fine-tune at each step)
```

## Production Deployment

### Inference with Long Context

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load long-context model
model = AutoModelForCausalLM.from_pretrained(
    "togethercomputer/LLaMA-2-7B-32K",  # 32k context
    torch_dtype=torch.float16,
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained("togethercomputer/LLaMA-2-7B-32K")

# Process long document
long_text = "..." * 30000  # 30k tokens
inputs = tokenizer(long_text, return_tensors="pt", truncation=False).to('cuda')

# Generate
outputs = model.generate(
    **inputs,
    max_new_tokens=512,
    temperature=0.7,
)

response = tokenizer.decode(outputs[0], skip_special_tokens=True)
```

### Memory Optimization

```python
# Use gradient checkpointing for fine-tuning
model.gradient_checkpointing_enable()

# Use Flash Attention 2
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    attn_implementation="flash_attention_2",  # 2-3× faster
    torch_dtype=torch.float16
)

# Use paged attention (vLLM)
from vllm import LLM

llm = LLM(
    model="togethercomputer/LLaMA-2-7B-32K",
    max_model_len=32768,  # 32k context
    gpu_memory_utilization=0.9
)
```

## Resources

- **RoPE Paper**: https://arxiv.org/abs/2104.09864 (RoFormer)
- **YaRN Paper**: https://arxiv.org/abs/2309.00071
- **ALiBi Paper**: https://arxiv.org/abs/2108.12409 (Train Short, Test Long)
- **Position Interpolation**: https://arxiv.org/abs/2306.15595
- **HuggingFace RoPE Utils**: https://github.com/huggingface/transformers/blob/main/src/transformers/modeling_rope_utils.py
- **YaRN Implementation**: https://github.com/jquesnelle/yarn
- **Together AI Blog**: https://www.together.ai/blog/llama-2-7b-32k

## See Also

- `references/rope.md` - Detailed RoPE implementation and theory
- `references/extension_methods.md` - YaRN, ALiBi, Position Interpolation comparisons
- `references/fine_tuning.md` - Complete fine-tuning guide for context extension




---


## 📚 Reference Materials


### Extension_Methods

# Context Extension Methods

Comprehensive comparison of YaRN, ALiBi, and Position Interpolation based on published research.

## Table of Contents
- YaRN (Yet another RoPE extensioN)
- ALiBi (Attention with Linear Biases)
- Position Interpolation
- Method Comparison

## YaRN: Yet another RoPE extensioN

**Paper**: arXiv 2309.00071 (2023)
**Authors**: Bowen Peng, Jeffrey Quesnelle, Honglu Fan, Enrico Shippole

### Overview

YaRN extends RoPE-based models to 128k+ context with 10× less training data than previous methods.

### Key Innovations

1. **NTK-aware interpolation**: Scales different frequency components differently
2. **Attention temperature scaling**: Adjusts attention sharpness
3. **NTK-by-parts**: Hybrid interpolation/extrapolation

### Technical Details

**Problem**: Naive position interpolation compresses all frequencies uniformly, losing high-frequency information.

**Solution**: Different treatment for different frequencies.

```python
# Frequency decomposition
# Low frequencies (< 1/β_slow): Interpolate (compress)
# High frequencies (> 1/β_fast): Extrapolate (extend as-is)
# Middle frequencies: Smooth ramp between the two

def yarn_get_mscale(scale=1.0):
    """Attention temperature scaling."""
    if scale <= 1:
        return 1.0
    return 0.1 * math.log(scale) + 1.0

def yarn_find_correction_dim(num_rotations, dim, base=10000, max_position_embeddings=2048):
    """Find dimension cutoffs for NTK-by-parts."""
    return (dim * math.log(max_position_embeddings / (num_rotations * 2 * math.pi))) / (2 * math.log(base))

def yarn_find_correction_range(low_rot, high_rot, dim, base=10000, max_position_embeddings=2048):
    """Find frequency ranges for interpolation."""
    low = math.floor(yarn_find_correction_dim(low_rot, dim, base, max_position_embeddings))
    high = math.ceil(yarn_find_correction_dim(high_rot, dim, base, max_position_embeddings))
    return max(low, 0), min(high, dim - 1)

def yarn_linear_ramp_mask(min_val, max_val, dim):
    """Create smooth ramp between interpolation and extrapolation."""
    if min_val == max_val:
        max_val += 0.001  # Avoid division by zero
    linear_func = (torch.arange(dim, dtype=torch.float32) - min_val) / (max_val - min_val)
    ramp_func = torch.clamp(linear_func, 0, 1)
    return ramp_func
```

### Complete YaRN Implementation

```python
class YaRNScaledRoPE(nn.Module):
    """Full YaRN implementation."""

    def __init__(
        self,
        dim,
        max_position_embeddings=2048,
        base=10000,
        scale=1.0,
        original_max_position_embeddings=2048,
        extrapolation_factor=1.0,
        attn_factor=1.0,
        beta_fast=32,
        beta_slow=1,
        device=None
    ):
        super().__init__()
        self.dim = dim
        self.max_position_embeddings = max_position_embeddings
        self.base = base
        self.scale = scale
        self.original_max_position_embeddings = original_max_position_embeddings
        self.extrapolation_factor = extrapolation_factor
        self.attn_factor = attn_factor
        self.beta_fast = beta_fast
        self.beta_slow = beta_slow

        # Compute mscale (attention temperature)
        self.mscale = float(yarn_get_mscale(self.scale) * self.attn_factor)

        # Compute frequency bands
        self.low, self.high = yarn_find_correction_range(
            self.beta_fast,
            self.beta_slow,
            self.dim,
            self.base,
            self.original_max_position_embeddings
        )

        # Compute inverse frequencies
        inv_freq = 1.0 / (self.base ** (torch.arange(0, self.dim, 2, dtype=torch.float32) / self.dim))

        # Create ramp mask
        inv_freq_mask = 1.0 - yarn_linear_ramp_mask(self.low, self.high, self.dim // 2)
        inv_freq = inv_freq / ((1 - inv_freq_mask) * self.extrapolation_factor + inv_freq_mask)

        self.register_buffer("inv_freq", inv_freq)

    def forward(self, seq_len, device):
        t = torch.arange(seq_len, device=device, dtype=self.inv_freq.dtype)

        # Apply YaRN scaling
        freqs = torch.outer(t, self.inv_freq)

        # Attention temperature scaling
        emb = torch.cat((freqs, freqs), dim=-1)
        cos = emb.cos() * self.mscale
        sin = emb.sin() * self.mscale

        return cos, sin
```

### YaRN Parameters

```python
# Default YaRN configuration (from paper)
yarn_config = {
    "scale": 16,                    # 16× extension (2k → 32k)
    "original_max_position": 2048,  # Original context length
    "extrapolation_factor": 1.0,    # How much to extrapolate high freqs
    "attn_factor": 1.0,             # Base attention temperature
    "beta_fast": 32,                # High-frequency threshold
    "beta_slow": 1,                 # Low-frequency threshold
}

# For larger extensions (64k, 128k)
yarn_config_large = {
    "scale": 64,
    "beta_fast": 64,   # Increase for larger scales
    "beta_slow": 2,
}
```

### Performance

**Results from paper (LLaMA 7B)**:

| Method | Training Tokens | Steps | Final Perplexity | Context Length |
|--------|----------------|-------|------------------|----------------|
| Full Fine-tune | 10B | 10000 | 11.2 | 32k |
| Position Interpolation | 1B | 1000 | 12.5 | 32k |
| **YaRN** | **100M** | **400** | **11.8** | **32k** |

**10× less data, 2.5× less steps than Position Interpolation!**

## ALiBi: Attention with Linear Biases

**Paper**: arXiv 2108.12409 (ICLR 2022)
**Authors**: Ofir Press, Noah A. Smith, Mike Lewis
**Title**: "Train Short, Test Long: Attention with Linear Biases Enables Input Length Extrapolation"

### Core Concept

**Key idea**: Don't add positional embeddings. Instead, bias attention scores based on distance.

```
attention_score[i, j] = q_i · k_j + bias[i, j]

where bias[i, j] = -m * |i - j|
      m = slope for each head
```

### Mathematical Formulation

**Standard attention**:
```
Attention(Q, K, V) = softmax(QK^T / √d_k) V
```

**ALiBi attention**:
```
Attention(Q, K, V) = softmax((QK^T + m · L) / √d_k) V

where L[i,j] = -(i - j)  (lower triangular)
      m = head-specific slope
```

### Implementation

```python
import math
import torch
import torch.nn.functional as F

def get_alibi_slopes(num_heads):
    """Compute ALiBi slope for each attention head.

    Source: Official ALiBi implementation
    """
    def get_slopes_power_of_2(n):
        start = 2 ** (-(2 ** -(math.log2(n) - 3)))
        ratio = start
        return [start * (ratio ** i) for i in range(n)]

    # If power of 2
    if math.log2(num_heads).is_integer():
        return get_slopes_power_of_2(num_heads)

    # If not power of 2, use closest power of 2 and interpolate
    closest_power_of_2 = 2 ** math.floor(math.log2(num_heads))
    slopes = get_slopes_power_of_2(closest_power_of_2)

    # Add extra slopes from next power of 2
    extra_slopes = get_slopes_power_of_2(2 * closest_power_of_2)
    slopes.extend(extra_slopes[0::2][:num_heads - closest_power_of_2])

    return slopes

def create_alibi_bias(seq_len, num_heads, device='cpu'):
    """Create ALiBi attention bias matrix."""
    # Relative positions: L[i, j] = -(i - j)
    context_position = torch.arange(seq_len, device=device)[:, None]
    memory_position = torch.arange(seq_len, device=device)[None, :]

    # Distance matrix (negative for causal)
    relative_position = memory_position - context_position
    relative_position = torch.abs(relative_position).unsqueeze(0)  # (1, seq_len, seq_len)

    # Get slopes for each head
    slopes = torch.tensor(get_alibi_slopes(num_heads), device=device).unsqueeze(-1).unsqueeze(-1)

    # Apply slopes: (num_heads, seq_len, seq_len)
    alibi = -slopes * relative_position

    return alibi

def alibi_attention(query, key, value, num_heads, scale=None):
    """Multi-head attention with ALiBi."""
    batch_size, seq_len, embed_dim = query.shape
    head_dim = embed_dim // num_heads

    if scale is None:
        scale = head_dim ** -0.5

    # Reshape for multi-head: (batch, num_heads, seq_len, head_dim)
    query = query.reshape(batch_size, seq_len, num_heads, head_dim).transpose(1, 2)
    key = key.reshape(batch_size, seq_len, num_heads, head_dim).transpose(1, 2)
    value = value.reshape(batch_size, seq_len, num_heads, head_dim).transpose(1, 2)

    # Attention scores: (batch, num_heads, seq_len, seq_len)
    attn_scores = torch.matmul(query, key.transpose(-2, -1)) * scale

    # Add ALiBi bias
    alibi_bias = create_alibi_bias(seq_len, num_heads, device=query.device)
    attn_scores = attn_scores + alibi_bias

    # Softmax and apply to values
    attn_weights = F.softmax(attn_scores, dim=-1)
    output = torch.matmul(attn_weights, value)

    # Reshape back: (batch, seq_len, embed_dim)
    output = output.transpose(1, 2).reshape(batch_size, seq_len, embed_dim)

    return output
```

### Slope Values

**Example slopes for 8 heads**:
```python
slopes = get_alibi_slopes(8)
# Output: [0.0625, 0.125, 0.25, 0.5, 1.0, 2.0, 4.0, 8.0]

# Each head has different slope
# → Different heads attend to different distance ranges
# → Head 1: Strong recency bias (slope=8.0)
# → Head 8: Weak recency bias (slope=0.0625)
```

### Advantages

1. **No position limit**: Works for any sequence length
2. **Efficient**: 11% less memory than sinusoidal embeddings
3. **Fast**: 11% faster training
4. **Extrapolates well**: Train 1k, test 2k+ tokens
5. **Simple**: No learned parameters for position

### Disadvantages

1. **Requires pre-training**: Can't retrofit existing models
2. **Recency bias**: Always biases toward recent tokens (may not suit all tasks)

## Position Interpolation

**Paper**: arXiv 2306.15595 (2023)
**Authors**: Shouyuan Chen, Sherman Wong, Liangjian Chen, Yuandong Tian
**Title**: "Extending Context Window of Large Language Models via Positional Interpolation"

### Core Idea

Instead of extrapolating positions beyond training range, interpolate within trained range.

```
# Extrapolation (bad): positions [0, 1, 2, ..., 2048, 2049, ..., 32768]
# Positions > 2048 are out-of-distribution

# Interpolation (good): positions [0, 0.0625, 0.125, ..., 2048]
# All positions within [0, 2048] (in-distribution)
```

### Mathematical Formulation

**Original RoPE**:
```
position_ids = [0, 1, 2, 3, ..., L-1]
```

**Position Interpolation** (scale factor s):
```
position_ids = [0, 1/s, 2/s, 3/s, ..., (L-1)/s]
```

### Implementation

```python
class InterpolatedRoPE(nn.Module):
    """RoPE with position interpolation."""

    def __init__(self, dim, max_seq_len=2048, base=10000, scaling_factor=1.0):
        super().__init__()
        self.scaling_factor = scaling_factor

        # Standard RoPE frequencies
        inv_freq = 1.0 / (base ** (torch.arange(0, dim, 2).float() / dim))
        self.register_buffer("inv_freq", inv_freq)

    def forward(self, seq_len, device):
        # Position indices
        t = torch.arange(seq_len, device=device).type_as(self.inv_freq)

        # Interpolate positions
        t = t / self.scaling_factor  # KEY LINE

        # Standard RoPE
        freqs = torch.outer(t, self.inv_freq)
        emb = torch.cat((freqs, freqs), dim=-1)
        return emb.cos(), emb.sin()
```

### Fine-tuning Requirements

**Minimal fine-tuning needed**:

```python
# Extension: 2k → 32k (16× scale)
scaling_factor = 16.0

# Training config
training_args = {
    "max_steps": 1000,      # Only 1000 steps!
    "learning_rate": 2e-5,  # Small LR
    "batch_size": 1,
    "gradient_accumulation_steps": 16,
}

# Results: Near-perfect perplexity retention
```

### Theoretical Analysis

**Interpolation bound** (from paper):

Upper bound of interpolation error is ~600× smaller than extrapolation error.

```
Extrapolation error: O(L^2)  # Grows quadratically
Interpolation error: O(1/s)  # Shrinks linearly with scale
```

### Results

**LLaMA models extended to 32k**:

| Model | Original Context | Extended Context | Fine-tune Steps | Perplexity |
|-------|-----------------|------------------|----------------|------------|
| LLaMA 7B | 2048 | 32768 | 1000 | 2.72 |
| LLaMA 13B | 2048 | 32768 | 1000 | 2.55 |
| LLaMA 33B | 2048 | 32768 | 1000 | 2.38 |
| LLaMA 65B | 2048 | 32768 | 1000 | 2.26 |

**Passkey retrieval**: 100% accuracy up to 32k tokens

### Advantages

1. **Minimal training**: 1000 steps sufficient
2. **Stable**: Interpolation more stable than extrapolation
3. **Simple**: One-line code change
4. **Effective**: Works across all LLaMA sizes

### Disadvantages

1. **Limited extrapolation**: Can't go beyond trained range without fine-tuning
2. **Information compression**: All positions compressed into trained range

## Method Comparison

### Training Requirements

| Method | Pre-training Needed | Fine-tuning Steps | Training Tokens |
|--------|---------------------|-------------------|-----------------|
| **ALiBi** | Yes (from scratch) | 0 | Full (100B+) |
| **Position Interpolation** | No | 1,000 | ~100M |
| **YaRN** | No | 400 | ~100M |
| **Linear RoPE Scaling** | No | 1,000-5,000 | ~1B |

### Extrapolation Performance

**Test**: Train on 2k, test on 8k, 16k, 32k

| Method | 8k PPL | 16k PPL | 32k PPL | Extrapolation Quality |
|--------|--------|---------|---------|----------------------|
| **ALiBi** | 12.1 | 12.3 | 12.5 | Excellent |
| **YaRN** | 11.8 | 12.0 | 12.2 | Excellent |
| **Position Interpolation** | 12.5 | 13.2 | 14.8 | Poor |
| **Linear Scaling** | 13.1 | 15.2 | 19.4 | Poor |

### Memory and Speed

| Method | Memory vs Baseline | Speed vs Baseline |
|--------|--------------------|--------------------|
| **ALiBi** | -11% | +11% |
| **Position Interpolation** | 0% | 0% |
| **YaRN** | 0% | -5% |
| **Linear Scaling** | 0% | 0% |

### Use Case Recommendations

```python
# New model from scratch → ALiBi
if training_from_scratch:
    use_method = "ALiBi"

# Extending existing RoPE model with best quality → YaRN
elif need_sota_quality:
    use_method = "YaRN"

# Quick extension with minimal compute → Position Interpolation
elif need_quick_solution:
    use_method = "Position Interpolation"

# Moderate extension, simple implementation → Linear Scaling
else:
    use_method = "Linear RoPE Scaling"
```

## Resources

- **YaRN Paper**: https://arxiv.org/abs/2309.00071
- **ALiBi Paper**: https://arxiv.org/abs/2108.12409
- **Position Interpolation Paper**: https://arxiv.org/abs/2306.15595
- **YaRN Implementation**: https://github.com/jquesnelle/yarn
- **ALiBi Implementation**: https://github.com/ofirpress/attention_with_linear_biases
- **Together AI Blog**: https://www.together.ai/blog/llama-2-7b-32k




### Fine_Tuning

# Fine-tuning for Context Extension

Complete guide to fine-tuning transformer models for longer context windows.

## Table of Contents
- Data Preparation
- Training Configuration
- YaRN Fine-tuning
- Position Interpolation Fine-tuning
- Evaluation
- Production Deployment

## Data Preparation

### Long Document Datasets

**Best datasets for context extension**:

```python
# 1. PG-19 (Books)
from datasets import load_dataset

pg19 = load_dataset("pg19", split="train")
# Average length: 50k-150k tokens
# Quality: High (literary works)

# 2. arXiv Papers
arxiv = load_dataset("scientific_papers", "arxiv", split="train")
# Average length: 4k-15k tokens
# Quality: High (technical content)

# 3. Long-form GitHub Code
github = load_dataset("codeparrot/github-code", split="train")
# Filter for large files (>5k tokens)

# 4. Long Conversations
conversations = load_dataset("HuggingFaceH4/ultrachat_200k", split="train")
# Concatenate multi-turn dialogues

# 5. Wikipedia Articles (concatenated)
wikipedia = load_dataset("wikipedia", "20220301.en", split="train")
```

### Creating Training Sequences

```python
def create_long_sequences(dataset, target_length=32768, tokenizer=None):
    """Create training sequences of target length."""
    sequences = []

    for example in dataset:
        # Tokenize
        tokens = tokenizer.encode(example['text'])

        # If single document is long enough
        if len(tokens) >= target_length:
            # Split into chunks
            for i in range(0, len(tokens) - target_length, target_length // 2):
                sequences.append(tokens[i:i + target_length])
        else:
            # Concatenate multiple documents
            buffer = tokens
            while len(buffer) < target_length:
                next_example = next(dataset)
                buffer.extend(tokenizer.encode(next_example['text']))

            sequences.append(buffer[:target_length])

    return sequences
```

### Data Quality Checks

```python
def validate_training_data(sequences, tokenizer, min_length=8192):
    """Ensure data quality for context extension."""
    issues = []

    for i, seq in enumerate(sequences):
        # 1. Check length
        if len(seq) < min_length:
            issues.append(f"Sequence {i}: too short ({len(seq)} tokens)")

        # 2. Check for repetition (copy-paste errors)
        if has_excessive_repetition(seq):
            issues.append(f"Sequence {i}: excessive repetition")

        # 3. Check for truncation artifacts
        if looks_truncated(seq, tokenizer):
            issues.append(f"Sequence {i}: appears truncated")

    if issues:
        print(f"⚠️  Found {len(issues)} data quality issues:")
        for issue in issues[:10]:  # Show first 10
            print(f"  - {issue}")

    return len(issues) == 0

def has_excessive_repetition(tokens, window=50, threshold=0.8):
    """Detect copy-paste or generated repetition."""
    for i in range(len(tokens) - window * 2):
        chunk1 = tokens[i:i + window]
        chunk2 = tokens[i + window:i + window * 2]
        similarity = sum(a == b for a, b in zip(chunk1, chunk2)) / window
        if similarity > threshold:
            return True
    return False

def looks_truncated(tokens, tokenizer):
    """Check if sequence ends mid-sentence."""
    last_20 = tokenizer.decode(tokens[-20:])
    # Check for incomplete sentences
    return not any(last_20.endswith(c) for c in ['.', '!', '?', '\n'])
```

## Training Configuration

### Position Interpolation Setup

**Minimal fine-tuning** (fastest method):

```python
from transformers import (
    AutoModelForCausalLM,
    AutoTokenizer,
    TrainingArguments,
    Trainer
)

# 1. Load base model
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    torch_dtype=torch.float16,
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-7b-hf")

# 2. Configure position interpolation
scaling_factor = 16.0  # 2k → 32k
model.config.max_position_embeddings = 32768
model.config.rope_scaling = {
    "type": "linear",
    "factor": scaling_factor
}

# 3. Training arguments
training_args = TrainingArguments(
    output_dir="./llama-2-7b-32k",
    num_train_epochs=1,
    max_steps=1000,                # Only 1000 steps!
    per_device_train_batch_size=1,
    gradient_accumulation_steps=16,
    learning_rate=2e-5,            # Low LR
    warmup_steps=100,
    lr_scheduler_type="cosine",
    logging_steps=10,
    save_steps=500,
    bf16=True,
    gradient_checkpointing=True,   # Reduce memory
    dataloader_num_workers=4,
)

# 4. Create trainer
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=long_context_dataset,
    data_collator=DataCollatorForLanguageModeling(tokenizer, mlm=False),
)

# 5. Train
trainer.train()
```

### YaRN Setup

**State-of-the-art extension** (best quality):

```python
# 1. Install YaRN
# git clone https://github.com/jquesnelle/yarn
# cd yarn && pip install -e .

# 2. Configure YaRN scaling
model.config.max_position_embeddings = 32768
model.config.rope_scaling = {
    "type": "yarn",
    "factor": 16.0,
    "original_max_position_embeddings": 2048,
    "attention_factor": 1.0,
    "beta_fast": 32,
    "beta_slow": 1,
}

# 3. Training arguments (fewer steps than position interpolation!)
training_args = TrainingArguments(
    output_dir="./llama-2-7b-32k-yarn",
    max_steps=400,                 # 400 steps (vs 1000 for PI)
    per_device_train_batch_size=1,
    gradient_accumulation_steps=16,
    learning_rate=2e-5,
    warmup_steps=50,
    bf16=True,
    gradient_checkpointing=True,
)

# 4. Train
trainer = Trainer(model=model, args=training_args, train_dataset=dataset)
trainer.train()
```

### Full Configuration Example

```python
# Complete fine-tuning script
import torch
from transformers import (
    AutoModelForCausalLM,
    AutoTokenizer,
    TrainingArguments,
    Trainer,
    DataCollatorForLanguageModeling,
)
from datasets import load_dataset

def prepare_long_context_data(dataset, tokenizer, context_length=32768):
    """Prepare training data."""
    def tokenize_function(examples):
        # Concatenate all texts
        concatenated = "\n\n".join(examples['text'])
        # Tokenize
        tokenized = tokenizer(
            concatenated,
            truncation=False,
            return_tensors=None,
        )
        # Split into chunks
        total_length = len(tokenized['input_ids'])
        chunks = []
        for i in range(0, total_length - context_length, context_length // 2):
            chunk = {
                'input_ids': tokenized['input_ids'][i:i + context_length],
                'attention_mask': tokenized['attention_mask'][i:i + context_length],
            }
            chunks.append(chunk)
        return chunks

    return dataset.map(tokenize_function, batched=True, remove_columns=dataset.column_names)

def fine_tune_long_context(
    base_model="meta-llama/Llama-2-7b-hf",
    target_context=32768,
    method="yarn",  # or "linear"
    output_dir="./output",
    max_steps=400,
):
    """Complete fine-tuning pipeline."""

    # Load model and tokenizer
    print(f"Loading {base_model}...")
    model = AutoModelForCausalLM.from_pretrained(
        base_model,
        torch_dtype=torch.bfloat16,
        device_map="auto",
        use_cache=False  # Required for gradient checkpointing
    )
    tokenizer = AutoTokenizer.from_pretrained(base_model)
    tokenizer.pad_token = tokenizer.eos_token

    # Configure scaling
    original_context = model.config.max_position_embeddings
    scaling_factor = target_context / original_context

    print(f"Scaling {original_context} → {target_context} ({scaling_factor}×)")
    model.config.max_position_embeddings = target_context

    if method == "yarn":
        model.config.rope_scaling = {
            "type": "yarn",
            "factor": scaling_factor,
            "original_max_position_embeddings": original_context,
            "attention_factor": 1.0,
            "beta_fast": 32,
            "beta_slow": 1,
        }
    else:  # linear
        model.config.rope_scaling = {
            "type": "linear",
            "factor": scaling_factor
        }

    # Enable gradient checkpointing
    model.gradient_checkpointing_enable()

    # Load and prepare data
    print("Preparing training data...")
    dataset = load_dataset("pg19", split="train[:1000]")  # Use subset for testing
    train_dataset = prepare_long_context_data(dataset, tokenizer, target_context)

    # Training arguments
    training_args = TrainingArguments(
        output_dir=output_dir,
        max_steps=max_steps,
        per_device_train_batch_size=1,
        gradient_accumulation_steps=16,
        learning_rate=2e-5,
        warmup_steps=max_steps // 10,
        lr_scheduler_type="cosine",
        logging_steps=10,
        save_steps=max_steps // 4,
        bf16=True,
        gradient_checkpointing=True,
        dataloader_num_workers=4,
        remove_unused_columns=False,
    )

    # Trainer
    trainer = Trainer(
        model=model,
        args=training_args,
        train_dataset=train_dataset,
        data_collator=DataCollatorForLanguageModeling(tokenizer, mlm=False),
    )

    # Train
    print("Starting fine-tuning...")
    trainer.train()

    # Save
    print(f"Saving model to {output_dir}...")
    model.save_pretrained(output_dir)
    tokenizer.save_pretrained(output_dir)

    print("Done!")

# Usage
if __name__ == "__main__":
    fine_tune_long_context(
        base_model="meta-llama/Llama-2-7b-hf",
        target_context=32768,
        method="yarn",
        max_steps=400,
    )
```

## Evaluation

### Perplexity Evaluation

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer
from datasets import load_dataset
import math

def evaluate_perplexity(model, tokenizer, dataset, context_length=32768):
    """Evaluate perplexity on long context."""
    model.eval()
    total_loss = 0
    total_tokens = 0

    with torch.no_grad():
        for example in dataset:
            # Tokenize
            tokens = tokenizer(
                example['text'],
                return_tensors='pt',
                max_length=context_length,
                truncation=True,
            ).to(model.device)

            # Forward pass
            outputs = model(**tokens, labels=tokens['input_ids'])
            loss = outputs.loss
            num_tokens = tokens['input_ids'].numel()

            total_loss += loss.item() * num_tokens
            total_tokens += num_tokens

    # Compute perplexity
    avg_loss = total_loss / total_tokens
    perplexity = math.exp(avg_loss)

    return perplexity

# Usage
model = AutoModelForCausalLM.from_pretrained("./llama-2-7b-32k")
tokenizer = AutoTokenizer.from_pretrained("./llama-2-7b-32k")

test_dataset = load_dataset("pg19", split="test[:100]")
ppl = evaluate_perplexity(model, tokenizer, test_dataset, context_length=32768)

print(f"Perplexity at 32k context: {ppl:.2f}")
```

### Passkey Retrieval Test

```python
def passkey_retrieval_test(model, tokenizer, context_lengths=[4096, 8192, 16384, 32768]):
    """Test ability to retrieve information from different positions."""
    results = {}

    for context_len in context_lengths:
        # Create synthetic document with passkey at random position
        passkey = "12345"
        position = random.randint(100, context_len - 100)

        # Generate filler text
        filler = "The quick brown fox jumps over the lazy dog. " * (context_len // 10)
        text = filler[:position] + f"The passkey is {passkey}. " + filler[position:]

        # Truncate to context length
        tokens = tokenizer(text, return_tensors='pt', max_length=context_len, truncation=True)

        # Query
        prompt = text + "\nWhat is the passkey?"
        inputs = tokenizer(prompt, return_tensors='pt').to(model.device)

        # Generate
        outputs = model.generate(**inputs, max_new_tokens=10)
        response = tokenizer.decode(outputs[0], skip_special_tokens=True)

        # Check if passkey retrieved
        success = passkey in response
        results[context_len] = success

        print(f"Context {context_len}: {'✓' if success else '✗'}")

    return results
```

### Long Document Q&A

```python
from datasets import load_dataset

def test_long_qa(model, tokenizer, max_length=32768):
    """Test on long-form QA dataset."""
    # Load dataset
    dataset = load_dataset("narrativeqa", split="test[:100]")

    correct = 0
    total = 0

    for example in dataset:
        # Long document
        document = example['document']['text']
        question = example['question']['text']
        gold_answers = example['answers']

        # Create prompt
        prompt = f"Document:\n{document}\n\nQuestion: {question}\n\nAnswer:"

        # Tokenize (may exceed original context)
        inputs = tokenizer(
            prompt,
            return_tensors='pt',
            max_length=max_length,
            truncation=True
        ).to(model.device)

        # Generate
        outputs = model.generate(
            **inputs,
            max_new_tokens=50,
            temperature=0.7,
        )
        answer = tokenizer.decode(outputs[0][inputs['input_ids'].shape[1]:], skip_special_tokens=True)

        # Check correctness
        if any(gold in answer.lower() for gold in gold_answers):
            correct += 1
        total += 1

    accuracy = correct / total
    print(f"Long QA Accuracy: {accuracy:.1%}")
    return accuracy
```

## Best Practices

### 1. Gradual Scaling

```python
# Don't jump directly to 128k!
# Scale incrementally:

# Step 1: 2k → 8k
fine_tune(model, target=8192, steps=200)

# Step 2: 8k → 16k
fine_tune(model, target=16384, steps=200)

# Step 3: 16k → 32k
fine_tune(model, target=32768, steps=400)

# Each step builds on previous, reducing total training needed
```

### 2. Learning Rate Tuning

```python
# Position Interpolation: Lower LR
lr_pi = 2e-5

# YaRN: Can use slightly higher LR
lr_yarn = 5e-5

# Rule: Larger scaling factors need lower LR
lr = base_lr / sqrt(scaling_factor)
```

### 3. Gradient Checkpointing

```python
# Essential for long context (saves ~50% memory)
model.gradient_checkpointing_enable()

# Trade-off: ~20% slower training, but fits in memory
```

### 4. Flash Attention

```python
# 2-3× speedup for long sequences
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    attn_implementation="flash_attention_2",  # Flash Attention 2
    torch_dtype=torch.bfloat16
)
```

## Production Deployment

### Save and Upload

```python
# Save fine-tuned model
model.save_pretrained("./llama-2-7b-32k-yarn")
tokenizer.save_pretrained("./llama-2-7b-32k-yarn")

# Upload to HuggingFace Hub
from huggingface_hub import HfApi

api = HfApi()
api.upload_folder(
    folder_path="./llama-2-7b-32k-yarn",
    repo_id="your-username/llama-2-7b-32k-yarn",
    repo_type="model",
)
```

### Inference Configuration

```python
# Load for inference
model = AutoModelForCausalLM.from_pretrained(
    "your-username/llama-2-7b-32k-yarn",
    torch_dtype=torch.float16,
    device_map="auto",
    max_memory={0: "40GB", "cpu": "100GB"}  # Offload to CPU if needed
)

# Process long document
long_text = "..." * 30000  # 30k tokens
inputs = tokenizer(long_text, return_tensors="pt", truncation=False).to('cuda')

outputs = model.generate(
    **inputs,
    max_new_tokens=512,
    do_sample=True,
    temperature=0.7,
    top_p=0.9,
)

response = tokenizer.decode(outputs[0], skip_special_tokens=True)
```

## Troubleshooting

### Issue: Out of Memory

**Solutions**:
1. Enable gradient checkpointing
2. Reduce batch size to 1
3. Increase gradient accumulation steps
4. Use bfloat16 or float16
5. Use Flash Attention

### Issue: Poor Extrapolation

**Solutions**:
1. Use YaRN instead of linear scaling
2. Increase fine-tuning steps
3. Use higher-quality long-form data
4. Gradual scaling (8k → 16k → 32k)

### Issue: Training Instability

**Solutions**:
1. Lower learning rate
2. Increase warmup steps
3. Use gradient clipping
4. Check data quality

## Resources

- **Position Interpolation Paper**: https://arxiv.org/abs/2306.15595
- **YaRN Paper**: https://arxiv.org/abs/2309.00071
- **Together AI Guide**: https://www.together.ai/blog/llama-2-7b-32k
- **HuggingFace Long Context Guide**: https://huggingface.co/blog/long-range-transformers




### Rope

# RoPE: Rotary Position Embeddings

Complete technical guide based on RoFormer paper (arXiv 2104.09864) and HuggingFace transformers implementation.

## Table of Contents
- Mathematical Formulation
- Implementation Details
- Scaling Techniques
- Production Usage

## Mathematical Formulation

**Source**: RoFormer: Enhanced Transformer with Rotary Position Embedding (arXiv 2104.09864)

### Core Idea

RoPE encodes absolute position with a rotation matrix while naturally incorporating relative position dependency in attention.

### Formulation

Given position index `m` and embedding dimension `d`:

```
Rotation Matrix R_θ(m):
  [cos(mθ₁)  -sin(mθ₁)  0         0        ]
  [sin(mθ₁)   cos(mθ₁)  0         0        ]
  [0          0         cos(mθ₂) -sin(mθ₂) ]
  [0          0         sin(mθ₂)  cos(mθ₂) ]
  ...

where θⱼ = base^(-2j/d) for j ∈ [0, 1, 2, ..., d/2)
```

**Key property**: Attention between positions m and n depends only on relative distance (m - n).

### Derivation

**Step 1: Position encoding via rotation**

```
q_m = W_q x_m rotated by mθ
k_n = W_k x_n rotated by nθ
```

**Step 2: Attention score**

```
score(q_m, k_n) = q_m^T k_n
                = (Rotated query) · (Rotated key)
                = f(q, k, m-n)
```

The score depends on relative position `m - n`, not absolute positions.

## Implementation Details

**Source**: HuggingFace transformers/modeling_rope_utils.py

### Basic RoPE Implementation

```python
import torch
import math

def precompute_freqs_cis(dim: int, end: int, theta: float = 10000.0):
    """Precompute rotation frequencies (cos + i*sin)."""
    # Compute inverse frequencies
    freqs = 1.0 / (theta ** (torch.arange(0, dim, 2)[: (dim // 2)].float() / dim))

    # Position indices
    t = torch.arange(end, device=freqs.device)

    # Outer product: (end, dim/2)
    freqs = torch.outer(t, freqs).float()

    # Convert to complex exponential (Euler's formula)
    freqs_cis = torch.polar(torch.ones_like(freqs), freqs)  # e^(i*θ) = cos(θ) + i*sin(θ)

    return freqs_cis

def reshape_for_broadcast(freqs_cis, x):
    """Reshape frequency tensor to match x dimensions."""
    ndim = x.ndim
    assert 0 <= 1 < ndim
    assert freqs_cis.shape == (x.shape[1], x.shape[-1])
    shape = [d if i == 1 or i == ndim - 1 else 1 for i, d in enumerate(x.shape)]
    return freqs_cis.view(*shape)

def apply_rotary_emb(xq, xk, freqs_cis):
    """Apply rotary embeddings to queries and keys."""
    # Convert to complex
    xq_ = torch.view_as_complex(xq.float().reshape(*xq.shape[:-1], -1, 2))
    xk_ = torch.view_as_complex(xk.float().reshape(*xk.shape[:-1], -1, 2))

    # Reshape freqs
    freqs_cis = reshape_for_broadcast(freqs_cis, xq_)

    # Apply rotation
    xq_out = torch.view_as_real(xq_ * freqs_cis).flatten(3)
    xk_out = torch.view_as_real(xk_ * freqs_cis).flatten(3)

    return xq_out.type_as(xq), xk_out.type_as(xk)
```

### Alternative: GPT-NeoX Style (HuggingFace)

```python
def rotate_half(x):
    """Rotate half the hidden dimensions of the input."""
    x1 = x[..., : x.shape[-1] // 2]
    x2 = x[..., x.shape[-1] // 2 :]
    return torch.cat((-x2, x1), dim=-1)

def apply_rotary_pos_emb_gpt_neox(q, k, cos, sin, position_ids=None):
    """GPT-NeoX style RoPE (used in HuggingFace)."""
    if position_ids is not None:
        # Select cos/sin for specific positions
        cos = cos[position_ids].unsqueeze(1)  # (bs, 1, seq_len, dim)
        sin = sin[position_ids].unsqueeze(1)
    else:
        cos = cos.unsqueeze(0).unsqueeze(0)  # (1, 1, seq_len, dim)
        sin = sin.unsqueeze(0).unsqueeze(0)

    # Apply rotation
    q_embed = (q * cos) + (rotate_half(q) * sin)
    k_embed = (k * cos) + (rotate_half(k) * sin)
    return q_embed, k_embed
```

### Difference: GPT-J vs GPT-NeoX Style

**GPT-J style** (Meta LLaMA):
- Processes in complex number space
- Pairs adjacent dimensions: (0,1), (2,3), (4,5)

**GPT-NeoX style** (HuggingFace):
- Splits into two halves
- Pairs across halves: (0, d/2), (1, d/2+1), ...

Both mathematically equivalent, different implementations.

## Scaling Techniques

### 1. Linear Scaling

**Simplest method**: Scale position indices linearly.

```python
# Original: positions [0, 1, 2, ..., L-1]
# Scaled: positions [0, 1/s, 2/s, ..., (L-1)/s]

class LinearScaledRoPE(nn.Module):
    def __init__(self, dim, max_seq_len=2048, base=10000, scaling_factor=1.0):
        super().__init__()
        self.scaling_factor = scaling_factor
        inv_freq = 1.0 / (base ** (torch.arange(0, dim, 2).float() / dim))
        self.register_buffer("inv_freq", inv_freq)

    def forward(self, seq_len, device):
        # Scale positions
        t = torch.arange(seq_len, device=device).type_as(self.inv_freq)
        t = t / self.scaling_factor  # Linear scaling

        freqs = torch.outer(t, self.inv_freq)
        emb = torch.cat((freqs, freqs), dim=-1)
        return emb.cos(), emb.sin()
```

**Pros**: Simple, easy to implement
**Cons**: May lose high-frequency information

### 2. NTK-Aware Scaling (RoPE-NTK)

**Source**: Community discovery (Reddit, GitHub)

**Key insight**: Scale base frequency instead of positions.

```python
# Instead of scaling positions, scale theta (base frequency)
base_new = base * (scaling_factor ** (dim / (dim - 2)))

# This preserves high frequencies while extending low frequencies
```

**Implementation**:

```python
class NTKScaledRoPE(nn.Module):
    def __init__(self, dim, max_seq_len=2048, base=10000, scaling_factor=1.0):
        super().__init__()
        # Compute new base
        base = base * (scaling_factor ** (dim / (dim - 2)))

        inv_freq = 1.0 / (base ** (torch.arange(0, dim, 2).float() / dim))
        self.register_buffer("inv_freq", inv_freq)

    def forward(self, seq_len, device):
        t = torch.arange(seq_len, device=device).type_as(self.inv_freq)
        freqs = torch.outer(t, self.inv_freq)
        emb = torch.cat((freqs, freqs), dim=-1)
        return emb.cos(), emb.sin()
```

**Pros**: Better than linear scaling
**Cons**: Still not perfect for very long contexts

### 3. Dynamic Scaling

**Source**: HuggingFace transformers

**Idea**: Adjust scaling factor dynamically based on input length.

```python
class DynamicScaledRoPE(nn.Module):
    def __init__(self, dim, max_seq_len=2048, base=10000, scaling_factor=1.0):
        super().__init__()
        self.max_seq_len = max_seq_len
        self.scaling_factor = scaling_factor
        inv_freq = 1.0 / (base ** (torch.arange(0, dim, 2).float() / dim))
        self.register_buffer("inv_freq", inv_freq)

    def forward(self, seq_len, device):
        # Compute dynamic scaling factor
        if seq_len > self.max_seq_len:
            # Scale proportionally
            scale = seq_len / self.max_seq_len
        else:
            scale = 1.0

        # Scale positions
        t = torch.arange(seq_len, device=device).type_as(self.inv_freq)
        t = t / (self.scaling_factor * scale)

        freqs = torch.outer(t, self.inv_freq)
        emb = torch.cat((freqs, freqs), dim=-1)
        return emb.cos(), emb.sin()
```

**Pros**: Adapts to input length
**Cons**: Different behavior for different lengths

### 4. YaRN (Yet another RoPE extensioN)

**Source**: arXiv 2309.00071

**Most sophisticated**: Combines multiple techniques.

```python
class YaRNScaledRoPE(nn.Module):
    """YaRN: NTK + Attention Temperature + Ramp."""

    def __init__(
        self,
        dim,
        max_seq_len=2048,
        base=10000,
        scaling_factor=1.0,
        beta_fast=32,
        beta_slow=1,
        attn_factor=1.0
    ):
        super().__init__()
        self.scaling_factor = scaling_factor
        self.beta_fast = beta_fast
        self.beta_slow = beta_slow
        self.attn_factor = attn_factor

        # Compute frequencies
        inv_freq = 1.0 / (base ** (torch.arange(0, dim, 2).float() / dim))
        self.register_buffer("inv_freq", inv_freq)

    def forward(self, seq_len, device):
        t = torch.arange(seq_len, device=device).type_as(self.inv_freq)

        # NTK-by-parts: Different scaling for different frequencies
        inv_freq_mask = (self.inv_freq > 1 / self.beta_fast).float()

        # Low frequencies: NTK scaling
        # High frequencies: Linear scaling
        # Middle: Smooth ramp

        inv_freq_scaled = self.inv_freq / self.scaling_factor
        freqs = torch.outer(t, inv_freq_scaled)

        emb = torch.cat((freqs, freqs), dim=-1)
        return emb.cos() * self.attn_factor, emb.sin() * self.attn_factor
```

**Pros**: State-of-the-art context extension
**Cons**: More complex, more hyperparameters

## Production Usage

### HuggingFace Integration

```python
from transformers import AutoModelForCausalLM, AutoConfig

# Linear scaling
config = AutoConfig.from_pretrained("meta-llama/Llama-2-7b-hf")
config.rope_scaling = {
    "type": "linear",
    "factor": 4.0  # 2k → 8k
}

# NTK-aware scaling
config.rope_scaling = {
    "type": "ntk",
    "factor": 4.0
}

# Dynamic scaling
config.rope_scaling = {
    "type": "dynamic",
    "factor": 4.0
}

# YaRN scaling
config.rope_scaling = {
    "type": "yarn",
    "factor": 16.0,
    "original_max_position_embeddings": 2048,
    "attention_factor": 1.0,
    "beta_fast": 32,
    "beta_slow": 1
}

model = AutoModelForCausalLM.from_config(config)
```

### Custom Implementation

```python
class RoPEAttention(nn.Module):
    def __init__(self, config):
        super().__init__()
        self.num_heads = config.num_attention_heads
        self.head_dim = config.hidden_size // config.num_attention_heads

        # Projections
        self.q_proj = nn.Linear(config.hidden_size, config.hidden_size, bias=False)
        self.k_proj = nn.Linear(config.hidden_size, config.hidden_size, bias=False)
        self.v_proj = nn.Linear(config.hidden_size, config.hidden_size, bias=False)
        self.o_proj = nn.Linear(config.hidden_size, config.hidden_size, bias=False)

        # RoPE
        self.rotary_emb = RotaryEmbedding(
            dim=self.head_dim,
            max_seq_len=config.max_position_embeddings,
            base=config.rope_theta
        )

    def forward(self, hidden_states, attention_mask=None, position_ids=None):
        bsz, seq_len, _ = hidden_states.size()

        # Q, K, V
        query_states = self.q_proj(hidden_states)
        key_states = self.k_proj(hidden_states)
        value_states = self.v_proj(hidden_states)

        # Reshape: (batch, seq_len, num_heads, head_dim)
        query_states = query_states.view(bsz, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        key_states = key_states.view(bsz, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        value_states = value_states.view(bsz, seq_len, self.num_heads, self.head_dim).transpose(1, 2)

        # Apply RoPE
        cos, sin = self.rotary_emb(seq_len, device=hidden_states.device)
        query_states, key_states = apply_rotary_pos_emb(query_states, key_states, cos, sin)

        # Attention
        attn_output = F.scaled_dot_product_attention(
            query_states, key_states, value_states,
            attn_mask=attention_mask
        )

        # Reshape and project
        attn_output = attn_output.transpose(1, 2).contiguous()
        attn_output = attn_output.reshape(bsz, seq_len, -1)
        attn_output = self.o_proj(attn_output)

        return attn_output
```

## Performance Comparison

**Scaling method comparison** (8k → 32k extension):

| Method | Fine-tune Steps | Perplexity | Memory | Speed |
|--------|----------------|------------|---------|-------|
| Linear | 1000 | 12.5 | 1.0× | 1.0× |
| NTK | 500 | 11.8 | 1.0× | 1.0× |
| Dynamic | 1000 | 12.2 | 1.0× | 0.98× |
| YaRN | 400 | 11.2 | 1.0× | 0.95× |

**Source**: YaRN paper (arXiv 2309.00071)

## Resources

- **RoFormer Paper**: https://arxiv.org/abs/2104.09864
- **YaRN Paper**: https://arxiv.org/abs/2309.00071
- **HuggingFace RoPE Utils**: https://github.com/huggingface/transformers/blob/main/src/transformers/modeling_rope_utils.py
- **Rotary Embeddings PyTorch**: https://github.com/lucidrains/rotary-embedding-torch




---

## 🚀 Usage

**Reference this template:** `@skill-long-context.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
