---
id: skill-moe-training
type: skill
name: moe-training
description: Train Mixture of Experts (MoE) models using DeepSpeed or HuggingFace.
  Use when training large-scale models with limited compute (5× cost reduction vs
  dense models), implementing sparse architectures like Mixtral 8x7B or DeepSeek-V3,
  or scaling model capacity without proportional compute increase. Covers MoE architectures,
  routing mechanisms, load balancing, expert parallelism, and inference optimization.
category: ai-research
complexity: medium
keywords:
- deployment
- git
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 2042
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~2,042 -->
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




# moe-training

> Train Mixture of Experts (MoE) models using DeepSpeed or HuggingFace. Use when training large-scale models with limited compute (5× cost reduction vs dense models), implementing sparse architectures like Mixtral 8x7B or DeepSeek-V3, or scaling model capacity without proportional compute increase. Covers MoE architectures, routing mechanisms, load balancing, expert parallelism, and inference optimization.

# MoE Training: Mixture of Experts

## When to Use This Skill

Use MoE Training when you need to:
- **Train larger models** with limited compute (5× cost reduction vs dense models)
- **Scale model capacity** without proportional compute increase
- **Achieve better performance** per compute budget than dense models
- **Specialize experts** for different domains/tasks/languages
- **Reduce inference latency** with sparse activation (only 13B/47B params active in Mixtral)
- **Implement SOTA models** like Mixtral 8x7B, DeepSeek-V3, Switch Transformers

**Notable MoE Models**: Mixtral 8x7B (Mistral AI), DeepSeek-V3, Switch Transformers (Google), GLaM (Google), NLLB-MoE (Meta)

## Installation

```bash
# DeepSpeed with MoE support
pip install deepspeed>=0.6.0

# Megatron-DeepSpeed for large-scale training
git clone https://github.com/microsoft/Megatron-DeepSpeed
cd Megatron-DeepSpeed
pip install -r requirements.txt

# Alternative: HuggingFace Transformers
pip install transformers accelerate
```

## Quick Start

### Basic MoE Architecture

```python
import torch
import torch.nn as nn

class MoELayer(nn.Module):
    """Sparse Mixture of Experts layer."""

    def __init__(self, hidden_size, num_experts=8, top_k=2):
        super().__init__()
        self.num_experts = num_experts
        self.top_k = top_k

        # Expert networks (FFN)
        self.experts = nn.ModuleList([
            nn.Sequential(
                nn.Linear(hidden_size, 4 * hidden_size),
                nn.GELU(),
                nn.Linear(4 * hidden_size, hidden_size)
            )
            for _ in range(num_experts)
        ])

        # Gating network (router)
        self.gate = nn.Linear(hidden_size, num_experts)

    def forward(self, x):
        # x shape: (batch_size, seq_len, hidden_size)
        batch_size, seq_len, hidden_size = x.shape

        # Flatten for routing
        x_flat = x.view(-1, hidden_size)  # (batch_size * seq_len, hidden_size)

        # Compute gate scores
        gate_logits = self.gate(x_flat)  # (batch_size * seq_len, num_experts)

        # Top-k routing
        gate_scores = torch.softmax(gate_logits, dim=-1)
        topk_scores, topk_indices = torch.topk(gate_scores, self.top_k, dim=-1)

        # Normalize top-k scores
        topk_scores = topk_scores / topk_scores.sum(dim=-1, keepdim=True)

        # Dispatch and combine expert outputs
        output = torch.zeros_like(x_flat)

        for i in range(self.top_k):
            expert_idx = topk_indices[:, i]
            expert_scores = topk_scores[:, i].unsqueeze(-1)

            # Route tokens to experts
            for expert_id in range(self.num_experts):
                mask = (expert_idx == expert_id)
                if mask.any():
                    expert_input = x_flat[mask]
                    expert_output = self.experts[expert_id](expert_input)
                    output[mask] += expert_scores[mask] * expert_output

        # Reshape back
        return output.view(batch_size, seq_len, hidden_size)
```

### DeepSpeed MoE Training

```bash
# Training script with MoE
deepspeed pretrain_gpt_moe.py \
  --num-layers 24 \
  --hidden-size 1024 \
  --num-attention-heads 16 \
  --seq-length 2048 \
  --max-position-embeddings 2048 \
  --micro-batch-size 4 \
  --global-batch-size 256 \
  --train-iters 500000 \
  --lr 0.0001 \
  --min-lr 0.00001 \
  --lr-decay-style cosine \
  --num-experts 128 \
  --moe-expert-parallel-size 4 \
  --moe-loss-coeff 0.01 \
  --moe-train-capacity-factor 1.25 \
  --moe-eval-capacity-factor 2.0 \
  --fp16 \
  --deepspeed_config ds_config.json
```

## Core Concepts

### 1. MoE Architecture

**Key Components:**
- **Experts**: Multiple specialized FFN networks (typically 8-128)
- **Router/Gate**: Learned network that selects which experts to use
- **Top-k Routing**: Activate only k experts per token (k=1 or k=2)
- **Load Balancing**: Ensure even expert utilization

```
Input Token
    ↓
Router (Gate Network)
    ↓
Top-k Expert Selection (e.g., 2 out of 8)
    ↓
Expert 1 (weight: 0.6) + Expert 5 (weight: 0.4)
    ↓
Weighted Combination
    ↓
Output
```

### 2. Routing Mechanisms

**Top-1 Routing (Switch Transformer):**
```python
# Simplest routing: one expert per token
gate_logits = router(x)  # (batch, seq_len, num_experts)
expert_idx = torch.argmax(gate_logits, dim=-1)  # Hard routing
```

**Top-2 Routing (Mixtral):**
```python
# Top-2: two experts per token
gate_scores = torch.softmax(router(x), dim=-1)
top2_scores, top2_indices = torch.topk(gate_scores, k=2, dim=-1)

# Normalize scores
top2_scores = top2_scores / top2_scores.sum(dim=-1, keepdim=True)

# Combine expert outputs
output = (top2_scores[:, :, 0:1] * expert_outputs[top2_indices[:, :, 0]] +
          top2_scores[:, :, 1:2] * expert_outputs[top2_indices[:, :, 1]])
```

**Expert Choice Routing:**
```python
# Experts choose top-k tokens (instead of tokens choosing experts)
# Guarantees perfect load balancing
expert_scores = router(x).transpose(-1, -2)  # (batch, num_experts, seq_len)
topk_tokens = torch.topk(expert_scores, k=capacity_per_expert, dim=-1)
```

### 3. Load Balancing

**Auxiliary Loss:**
```python
def load_balancing_loss(gate_logits, expert_indices, num_experts):
    """Encourage uniform expert usage."""
    # Fraction of tokens routed to each expert
    expert_counts = torch.bincount(expert_indices.flatten(), minlength=num_experts)
    expert_fraction = expert_counts.float() / expert_indices.numel()

    # Gate probability for each expert (average across tokens)
    gate_probs = torch.softmax(gate_logits, dim=-1).mean(dim=0)

    # Auxiliary loss: encourage alignment
    aux_loss = num_experts * (expert_fraction * gate_probs).sum()

    return aux_loss

# Add to main loss
total_loss = language_model_loss + 0.01 * load_balancing_loss(...)
```

**Router Z-Loss (Stability):**
```python
def router_z_loss(logits):
    """Encourage router to have lower entropy (more decisive)."""
    z_loss = torch.logsumexp(logits, dim=-1).pow(2).mean()
    return z_loss

total_loss = lm_loss + 0.01 * aux_loss + 0.001 * router_z_loss(gate_logits)
```

### 4. Expert Parallelism

```python
# DeepSpeed configuration
{
  "train_batch_size": 256,
  "fp16": {"enabled": true},
  "moe": {
    "enabled": true,
    "num_experts": 128,
    "expert_parallel_size": 8,  # Distribute 128 experts across 8 GPUs
    "capacity_factor": 1.25,    # Expert capacity = tokens_per_batch * capacity_factor / num_experts
    "drop_tokens": true,        # Drop tokens exceeding capacity
    "use_residual": false
  }
}
```

## Training Configuration

### DeepSpeed MoE Config

```json
{
  "train_batch_size": 256,
  "gradient_accumulation_steps": 1,
  "optimizer": {
    "type": "Adam",
    "params": {
      "lr": 0.0001,
      "betas": [0.9, 0.999],
      "eps": 1e-8
    }
  },
  "fp16": {
    "enabled": true,
    "loss_scale": 0,
    "initial_scale_power": 16
  },
  "moe": {
    "enabled": true,
    "num_experts": 128,
    "expert_parallel_size": 8,
    "moe_loss_coeff": 0.01,
    "train_capacity_factor": 1.25,
    "eval_capacity_factor": 2.0,
    "min_capacity": 4,
    "drop_tokens": true,
    "use_residual": false,
    "use_tutel": false
  },
  "zero_optimization": {
    "stage": 1
  }
}
```

### Training Script

```bash
#!/bin/bash

# Mixtral-style MoE training
deepspeed --num_gpus 8 pretrain_moe.py \
  --model-parallel-size 1 \
  --num-layers 32 \
  --hidden-size 4096 \
  --num-attention-heads 32 \
  --seq-length 2048 \
  --max-position-embeddings 4096 \
  --micro-batch-size 2 \
  --global-batch-size 256 \
  --train-iters 500000 \
  --save-interval 5000 \
  --eval-interval 1000 \
  --eval-iters 100 \
  --lr 0.0001 \
  --min-lr 0.00001 \
  --lr-decay-style cosine \
  --lr-warmup-iters 2000 \
  --clip-grad 1.0 \
  --weight-decay 0.1 \
  --num-experts 8 \
  --moe-expert-parallel-size 4 \
  --moe-loss-coeff 0.01 \
  --moe-train-capacity-factor 1.25 \
  --moe-eval-capacity-factor 2.0 \
  --disable-moe-token-dropping \
  --fp16 \
  --deepspeed \
  --deepspeed_config ds_config_moe.json \
  --data-path /path/to/data \
  --vocab-file /path/to/vocab.json \
  --merge-file /path/to/merges.txt
```

## Advanced Patterns

### Mixtral 8x7B Architecture

```python
class MixtralMoEBlock(nn.Module):
    """Mixtral-style MoE block with 8 experts, top-2 routing."""

    def __init__(self, config):
        super().__init__()
        self.hidden_dim = config.hidden_size
        self.ffn_dim = config.intermediate_size
        self.num_experts = config.num_local_experts  # 8
        self.top_k = config.num_experts_per_tok       # 2

        # 8 expert FFNs
        self.experts = nn.ModuleList([
            nn.Sequential(
                nn.Linear(self.hidden_dim, self.ffn_dim, bias=False),
                nn.SiLU(),
                nn.Linear(self.ffn_dim, self.hidden_dim, bias=False)
            )
            for _ in range(self.num_experts)
        ])

        # Router
        self.gate = nn.Linear(self.hidden_dim, self.num_experts, bias=False)

    def forward(self, hidden_states):
        batch_size, sequence_length, hidden_dim = hidden_states.shape

        # Flatten
        hidden_states = hidden_states.view(-1, hidden_dim)

        # Router logits
        router_logits = self.gate(hidden_states)  # (batch * seq_len, num_experts)

        # Softmax and top-2
        routing_weights = torch.softmax(router_logits, dim=1)
        routing_weights, selected_experts = torch.topk(routing_weights, self.top_k, dim=-1)

        # Normalize routing weights
        routing_weights /= routing_weights.sum(dim=-1, keepdim=True)

        # Initialize output
        final_hidden_states = torch.zeros_like(hidden_states)

        # Route to experts
        for expert_idx in range(self.num_experts):
            expert_layer = self.experts[expert_idx]
            idx, top_x = torch.where(selected_experts == expert_idx)

            if idx.shape[0] == 0:
                continue

            # Current expert tokens
            current_hidden_states = hidden_states[idx]

            # Expert forward
            current_hidden_states = expert_layer(current_hidden_states)

            # Weighted by routing scores
            current_hidden_states *= routing_weights[idx, top_x, None]

            # Accumulate
            final_hidden_states.index_add_(0, idx, current_hidden_states)

        # Reshape
        return final_hidden_states.view(batch_size, sequence_length, hidden_dim)
```

### PR-MoE (Pyramid-Residual-MoE)

```bash
# DeepSpeed PR-MoE: 3x better parameter efficiency
deepspeed pretrain_gpt_moe.py \
  --num-layers 24 \
  --hidden-size 1024 \
  --num-attention-heads 16 \
  --num-experts "[128, 64, 32, 16]" \
  --mlp-type residual \
  --moe-expert-parallel-size 4 \
  --moe-loss-coeff 0.01 \
  --fp16
```

## Best Practices

### 1. Expert Count Selection

```python
# Rule of thumb: More experts = more capacity, but diminishing returns
# Typical configurations:
# - Small models (1B-7B): 8-16 experts
# - Medium models (7B-30B): 8-64 experts
# - Large models (30B+): 64-256 experts

# Example: Mixtral 8x7B
# Total params: 47B (8 experts × 7B each)
# Active params: 13B (2 experts × 7B, top-2 routing)
# Efficiency: 47B capacity with 13B compute
```

### 2. Capacity Factor Tuning

```python
# Capacity = (tokens_per_batch / num_experts) * capacity_factor

# Training: Lower capacity (faster, drops some tokens)
train_capacity_factor = 1.25  # 25% buffer

# Evaluation: Higher capacity (no dropping)
eval_capacity_factor = 2.0    # 100% buffer

# Formula:
expert_capacity = int((seq_len * batch_size / num_experts) * capacity_factor)
```

### 3. Learning Rate Guidelines

```python
# MoE models need lower LR than dense models
# - Dense model: lr = 6e-4
# - MoE model: lr = 1e-4 (3-6× lower)

# Also extend decay schedule
dense_lr_decay_iters = 300000
moe_lr_decay_iters = 500000  # 1.5-2× longer
```

### 4. Loss Coefficient Tuning

```python
# Start with standard values
moe_loss_coeff = 0.01    # Auxiliary loss (load balancing)
router_z_loss_coeff = 0.001  # Router entropy (stability)

# If load imbalance persists, increase aux loss
if max_expert_usage / min_expert_usage > 2.0:
    moe_loss_coeff = 0.1  # Stronger load balancing

# If training unstable, increase z-loss
if grad_norm > 10.0:
    router_z_loss_coeff = 0.01
```

### 5. Avoid Common Pitfalls

```python
# ❌ Bad: Using same LR as dense model
optimizer = Adam(model.parameters(), lr=6e-4)

# ✅ Good: Lower LR for MoE
optimizer = Adam([
    {'params': model.non_moe_params, 'lr': 6e-4},
    {'params': model.moe_params, 'lr': 1e-4}
])

# ❌ Bad: No load balancing
loss = lm_loss

# ✅ Good: Add auxiliary loss
loss = lm_loss + 0.01 * aux_loss + 0.001 * z_loss

# ❌ Bad: Too many experts for small dataset
num_experts = 128  # Overfitting risk

# ✅ Good: Match experts to data diversity
num_experts = 8  # Better for small datasets
```

## Inference Optimization

### Sparse Inference

```python
# Only activate top-k experts (huge memory savings)
@torch.no_grad()
def moe_inference(x, model, top_k=2):
    """Sparse MoE inference: only load k experts."""
    # Router
    gate_logits = model.gate(x)
    topk_scores, topk_indices = torch.topk(
        torch.softmax(gate_logits, dim=-1),
        k=top_k,
        dim=-1
    )

    # Load and run only top-k experts
    output = torch.zeros_like(x)
    for i in range(top_k):
        expert_idx = topk_indices[:, i]
        # Load expert from disk/offload if needed
        expert = model.load_expert(expert_idx)
        output += topk_scores[:, i:i+1] * expert(x)

    return output
```

## Resources

- **DeepSpeed MoE Tutorial**: https://www.deepspeed.ai/tutorials/mixture-of-experts-nlg/
- **Mixtral Paper**: https://arxiv.org/abs/2401.04088
- **Switch Transformers**: https://arxiv.org/abs/2101.03961
- **HuggingFace MoE Guide**: https://huggingface.co/blog/moe
- **NVIDIA MoE Blog**: https://developer.nvidia.com/blog/applying-mixture-of-experts-in-llm-architectures/

## See Also

- `references/architectures.md` - MoE model architectures (Mixtral, Switch, DeepSeek-V3)
- `references/training.md` - Advanced training techniques and optimization
- `references/inference.md` - Production deployment and serving patterns




---


## 📚 Reference Materials


### Inference

# MoE Inference Optimization

Complete guide to optimizing MoE inference based on MoE-Inference-Bench research (arXiv 2508.17467, 2024).

## Table of Contents
- Performance Metrics
- vLLM Optimizations
- Quantization
- Expert Parallelism
- Optimization Techniques
- Production Deployment

## Performance Metrics

**Source**: MoE-Inference-Bench (arXiv 2508.17467)

### Key Metrics

1. **Time to First Token (TTFT)**
   - Latency until first token generated
   - Critical for user experience

2. **Inter-Token Latency (ITL)**
   - Time between consecutive tokens
   - Affects streaming experience

3. **Throughput**
   - Formula: `(Batch Size × (Input + Output Tokens)) / Total Latency`
   - Higher is better

### Benchmark Results (H100 GPU)

**LLM Performance**:
- **OLMoE-1B-7B**: Highest throughput
- **Mixtral-8x7B**: Highest accuracy, lower throughput
- **Qwen3-30B**: High accuracy, moderate throughput

**VLM Performance**:
- **DeepSeek-VL2-Tiny**: Fastest, lowest accuracy
- **DeepSeek-VL2**: Highest accuracy, lowest throughput

## vLLM Optimizations

**Source**: MoE-Inference-Bench 2024, vLLM documentation

### Expert Parallelism

Distribute experts across GPUs for parallel execution.

```python
from vllm import LLM, SamplingParams

# Enable expert parallelism
llm = LLM(
    model="mistralai/Mixtral-8x7B-v0.1",
    tensor_parallel_size=2,      # Tensor parallelism
    enable_expert_parallel=True,  # Expert parallelism
    gpu_memory_utilization=0.9
)

# Generate
outputs = llm.generate(
    prompts=["What is mixture of experts?"],
    sampling_params=SamplingParams(temperature=0.7, max_tokens=256)
)
```

### Parallelism Strategies

**From MoE-Inference-Bench**:

| Strategy | Throughput Gain | Best For |
|----------|----------------|----------|
| **Tensor Parallelism** | High | Large models, multi-GPU |
| **Expert Parallelism** | Moderate | MoE-specific, many experts |
| **Pipeline Parallelism** | Low | Very large models |

**Recommendation**: Tensor parallelism most effective for MoE models

### Fused MoE Kernels

**Performance Gain**: 12-18% throughput improvement

```python
# vLLM automatically uses fused kernels when available
llm = LLM(
    model="mistralai/Mixtral-8x7B-v0.1",
    use_v2_block_manager=True  # Enable fused MoE kernels
)
```

**What it does**:
- Reduces kernel launch overhead
- Combines multiple operations into single kernel
- Better GPU utilization

## Quantization

**Source**: MoE-Inference-Bench quantization analysis

### FP8 Quantization

**Performance**: 20-30% throughput improvement over FP16

```python
from vllm import LLM

# FP8 quantization
llm = LLM(
    model="mistralai/Mixtral-8x7B-v0.1",
    quantization="fp8"  # FP8 quantization
)
```

**Trade-offs**:
- Throughput: +20-30%
- Memory: -40-50%
- Accuracy: Minimal degradation (<1%)

### INT8 Quantization

```python
# INT8 weight-only quantization
llm = LLM(
    model="mistralai/Mixtral-8x7B-v0.1",
    quantization="awq"  # or "gptq"
)
```

**Performance**:
- Throughput: +15-20%
- Memory: -50-60%
- Quality: Slight degradation (1-2%)

## Expert Configuration

**Source**: MoE-Inference-Bench hyperparameter analysis

### Active Experts

**Key Finding**: Single-expert activation → 50-80% higher throughput

```python
# Top-1 routing (best throughput)
# Mixtral default is top-2, but top-1 can be enforced at inference

# Model architecture determines this
# Cannot change at runtime, but affects deployment planning
```

**Performance vs Experts**:
- 1 expert/token: +50-80% throughput vs top-2
- 2 experts/token: Balanced (Mixtral default)
- 3+ experts/token: Lower throughput, higher quality

### Total Expert Count

**Scaling**: Non-linear, diminishing returns at high counts

| Total Experts | Throughput | Memory |
|--------------|------------|--------|
| 8 | Baseline | Baseline |
| 16 | +15% | +20% |
| 32 | +25% | +45% |
| 64 | +30% | +90% |
| 128 | +32% | +180% |

**Recommendation**: 8-32 experts for optimal throughput/memory

### FFN Dimension

**Key Finding**: Performance degrades with increasing FFN size

```python
# Smaller FFN = better throughput
# Trade-off: model capacity vs inference speed
```

| FFN Dimension | Throughput | Quality |
|---------------|------------|---------|
| 2048 | High | Moderate |
| 4096 | Moderate | High |
| 8192 | Low | Very High |

## Optimization Techniques

**Source**: MoE-Inference-Bench optimization experiments

### 1. Speculative Decoding

**Performance**: 1.5-2.5× speedup

```python
from vllm import LLM, SamplingParams

# Main model (large MoE)
main_model = LLM(model="mistralai/Mixtral-8x7B-v0.1")

# Draft model (small, fast)
draft_model = LLM(model="Qwen/Qwen3-1.7B")

# Speculative decoding with draft model
# vLLM handles automatically if draft model specified
```

**Best draft models** (from research):
- Medium-sized (1.7B-3B parameters)
- Qwen3-1.7B most effective
- Too small (<1B): low acceptance rate
- Too large (>7B): overhead dominates

### 2. Expert Pruning

**Performance**: 50% pruning → significant throughput gain

```python
# Prune least-used experts (offline)
# Example: Keep top-50% experts by usage

# Requires profiling on representative data:
# 1. Track expert utilization
# 2. Prune unused/rarely-used experts
# 3. Fine-tune pruned model (optional)
```

**Trade-off**:
- 50% pruning: +40-60% throughput, -2-5% accuracy
- 75% pruning: +80-120% throughput, -5-15% accuracy

### 3. Batch Size Tuning

```python
# Larger batches = better throughput (until OOM)
llm = LLM(
    model="mistralai/Mixtral-8x7B-v0.1",
    max_num_seqs=256,        # Maximum batch size
    max_num_batched_tokens=8192  # Total tokens in batch
)
```

**Optimal batch sizes** (H100):
- Mixtral-8x7B: 64-128
- Smaller MoE (8 experts): 128-256
- Larger MoE (>16 experts): 32-64

## Production Deployment

### Single GPU (Consumer Hardware)

```python
from vllm import LLM

# Optimize for single GPU
llm = LLM(
    model="mistralai/Mixtral-8x7B-v0.1",
    gpu_memory_utilization=0.95,  # Use 95% of VRAM
    max_num_seqs=32,              # Smaller batches
    quantization="awq"            # Quantize to fit
)
```

**Minimum requirements**:
- Mixtral-8x7B: 48GB VRAM (FP16) or 24GB (INT8)
- Expert parallelism not needed

### Multi-GPU (Data Center)

```python
# Tensor parallelism + Expert parallelism
llm = LLM(
    model="mistralai/Mixtral-8x7B-v0.1",
    tensor_parallel_size=2,       # Split across 2 GPUs
    enable_expert_parallel=True,  # Distribute experts
    gpu_memory_utilization=0.9
)
```

**Scaling strategy**:
- 2 GPUs: Tensor parallelism
- 4+ GPUs: Tensor + expert parallelism
- 8+ GPUs: Consider pipeline parallelism

### Production Configuration

```python
# Optimized for production
llm = LLM(
    model="mistralai/Mixtral-8x7B-v0.1",

    # Parallelism
    tensor_parallel_size=2,
    enable_expert_parallel=True,

    # Memory
    gpu_memory_utilization=0.9,
    swap_space=4,  # 4GB CPU swap

    # Performance
    use_v2_block_manager=True,  # Fused kernels
    max_num_seqs=64,
    max_num_batched_tokens=4096,

    # Optional: Quantization
    quantization="fp8"
)
```

### Monitoring

```python
import time

# Track metrics
def monitor_inference(llm, prompts):
    start = time.time()
    outputs = llm.generate(prompts)
    end = time.time()

    total_time = end - start
    total_tokens = sum(len(o.outputs[0].token_ids) for o in outputs)

    print(f"Throughput: {total_tokens / total_time:.2f} tokens/sec")
    print(f"Latency: {total_time / len(prompts):.2f} sec/request")

    return outputs

# Usage
outputs = monitor_inference(llm, ["Prompt 1", "Prompt 2"])
```

## Optimization Checklist

**From MoE-Inference-Bench best practices:**

- [ ] Use FP8 quantization (20-30% speedup)
- [ ] Enable fused MoE kernels (12-18% speedup)
- [ ] Tune batch size for your hardware
- [ ] Use tensor parallelism for multi-GPU
- [ ] Consider speculative decoding (1.5-2.5× speedup)
- [ ] Profile expert utilization, prune if needed
- [ ] Optimize active expert count (top-1 vs top-2)
- [ ] Monitor and tune GPU memory utilization

## Resources

- **MoE-Inference-Bench**: https://arxiv.org/abs/2508.17467
- **vLLM Documentation**: https://docs.vllm.ai
- **PyTorch MoE Optimization**: https://pytorch.org/blog/accelerating-moe-model/




### Training

# MoE Training Guide

Complete training guide based on DeepSpeed official documentation and production practices.

## Table of Contents
- DeepSpeed MoE Setup
- Training Configuration
- PR-MoE (Pyramid-Residual-MoE)
- Mixture-of-Students (MoS)
- Hyperparameter Tuning
- Production Training

## DeepSpeed MoE Setup

**Source**: DeepSpeed MoE Tutorial (https://www.deepspeed.ai/tutorials/mixture-of-experts-nlg/)

### Requirements

```bash
# Install DeepSpeed v0.6.0 or higher
pip install deepspeed>=0.6.0

# Clone Megatron-DeepSpeed
git clone https://github.com/microsoft/Megatron-DeepSpeed
cd Megatron-DeepSpeed
pip install -r requirements.txt
```

### Basic MoE Configuration

```json
{
  "train_batch_size": 256,
  "gradient_accumulation_steps": 1,
  "fp16": {
    "enabled": true,
    "loss_scale": 0,
    "initial_scale_power": 16
  },
  "moe": {
    "enabled": true,
    "num_experts": 128,
    "expert_parallel_size": 8,
    "moe_loss_coeff": 0.01,
    "train_capacity_factor": 1.25,
    "eval_capacity_factor": 2.0,
    "min_capacity": 4,
    "drop_tokens": true
  },
  "zero_optimization": {
    "stage": 1
  }
}
```

## Training Parameters

### Core MoE Parameters

**From DeepSpeed documentation:**

1. **`--num-experts`**
   - Number of experts per MoE layer
   - Recommended: 128 experts
   - Range: 8-256 depending on scale

2. **`--moe-expert-parallel-size`**
   - Degree of expert parallelism
   - Distributes experts across GPUs
   - Example: 128 experts / 8 GPUs = 16 experts per GPU

3. **`--moe-loss-coeff`**
   - MoE auxiliary loss coefficient
   - Recommended: 0.01
   - Controls load balancing strength

4. **`--moe-train-capacity-factor`**
   - Training capacity multiplier
   - Default: 1.25
   - Formula: capacity = (tokens/num_experts) × capacity_factor

5. **`--moe-eval-capacity-factor`**
   - Evaluation capacity multiplier
   - Default: 2.0 (no token dropping during eval)

6. **`--moe-min-capacity`**
   - Minimum expert capacity
   - Default: 4
   - Ensures each expert processes minimum tokens

7. **`--disable-moe-token-dropping`**
   - Remove expert capacity limits
   - All tokens processed (no dropping)
   - May increase memory usage

### Example Training Script

```bash
#!/bin/bash

deepspeed --num_gpus 8 pretrain_gpt_moe.py \
  --tensor-model-parallel-size 1 \
  --pipeline-model-parallel-size 1 \
  --num-layers 24 \
  --hidden-size 1024 \
  --num-attention-heads 16 \
  --seq-length 2048 \
  --max-position-embeddings 2048 \
  --micro-batch-size 4 \
  --global-batch-size 256 \
  --train-iters 500000 \
  --lr 0.0001 \
  --min-lr 0.00001 \
  --lr-decay-style cosine \
  --lr-warmup-iters 2000 \
  --clip-grad 1.0 \
  --weight-decay 0.1 \
  --num-experts 128 \
  --moe-expert-parallel-size 8 \
  --moe-loss-coeff 0.01 \
  --moe-train-capacity-factor 1.25 \
  --moe-eval-capacity-factor 2.0 \
  --moe-min-capacity 4 \
  --fp16 \
  --deepspeed \
  --deepspeed_config ds_config_moe.json \
  --data-path /path/to/data \
  --vocab-file /path/to/vocab.json \
  --merge-file /path/to/merges.txt \
  --save-interval 5000 \
  --eval-interval 1000 \
  --eval-iters 100
```

## PR-MoE: Pyramid-Residual-MoE

**Source**: DeepSpeed documentation - improves parameter efficiency 3× over standard MoE

### Architecture

PR-MoE uses:
- Varying number of experts per layer (pyramid structure)
- Residual connections between expert layers
- Better parameter efficiency

### Configuration

```bash
# PR-MoE specific parameters
--num-experts "[128, 64, 32, 16]" \  # Pyramid: different experts per layer
--mlp-type residual \                # Use residual connections
--moe-expert-parallel-size 4 \
--moe-loss-coeff 0.01
```

### Full PR-MoE Training

```bash
deepspeed --num_gpus 8 pretrain_gpt_moe.py \
  --num-layers 24 \
  --hidden-size 1024 \
  --num-attention-heads 16 \
  --seq-length 2048 \
  --max-position-embeddings 2048 \
  --micro-batch-size 4 \
  --global-batch-size 256 \
  --num-experts "[128, 64, 32, 16]" \  # Pyramid structure
  --mlp-type residual \                # Residual MoE
  --moe-expert-parallel-size 4 \
  --moe-loss-coeff 0.01 \
  --moe-train-capacity-factor 1.25 \
  --fp16 \
  --deepspeed \
  --deepspeed_config ds_config_moe.json \
  --data-path /path/to/data \
  --save-interval 5000
```

**Benefits**:
- 3× better parameter efficiency vs standard MoE
- Fewer total parameters for same performance
- Better gradient flow with residual connections

## Mixture-of-Students (MoS)

**Source**: DeepSpeed documentation - knowledge distillation for MoE

### Overview

MoS = MoE + Knowledge Distillation
- Student: MoE model (being trained)
- Teacher: Dense model (pre-trained)
- Transfers knowledge from dense teacher to sparse MoE student

### Configuration

```bash
# MoS parameters
--mos \                              # Enable MoS distillation
--load-teacher /path/to/teacher \    # Teacher model checkpoint
--teacher-forward \                  # Enable teacher forward pass
--teacher-model-parallel-size 1
```

### Full MoS Training

```bash
deepspeed --num_gpus 8 pretrain_gpt_moe.py \
  --num-layers 24 \
  --hidden-size 1024 \
  --num-attention-heads 16 \
  --num-experts 128 \
  --moe-expert-parallel-size 8 \
  --moe-loss-coeff 0.01 \
  --mos \                                    # Enable MoS
  --load-teacher /path/to/dense/teacher \    # Teacher checkpoint
  --teacher-forward \
  --teacher-model-parallel-size 1 \
  --fp16 \
  --deepspeed \
  --deepspeed_config ds_config_moe.json \
  --data-path /path/to/data
```

### Staged Distillation

**Recommended**: Stop distillation early

```python
# In training loop
if iteration < 400000:
    # Use MoS (distillation)
    loss = moe_loss + distillation_loss
else:
    # Stop distillation, train MoE only
    loss = moe_loss
```

**Benefits**:
- Faster convergence
- Better final performance
- Preserves teacher knowledge while allowing MoE specialization

## Hyperparameter Tuning

### Learning Rate

**Key insight**: MoE needs lower LR than dense models

```bash
# Dense model
--lr 0.0006 \
--min-lr 0.00006

# MoE model (3-6× lower)
--lr 0.0001 \        # Lower!
--min-lr 0.00001
```

### LR Decay

**Extend decay schedule** for MoE:

```bash
# Dense model
--lr-decay-iters 300000 \
--lr-warmup-iters 2000

# MoE model (1.5-2× longer)
--lr-decay-iters 500000 \   # Extended!
--lr-warmup-iters 2000
```

### Capacity Factor

**Tune based on memory/speed tradeoff**:

```json
{
  "moe": {
    // Training: Lower capacity (faster, drops tokens)
    "train_capacity_factor": 1.0,   // Aggressive
    "train_capacity_factor": 1.25,  // Balanced (recommended)
    "train_capacity_factor": 1.5,   // Conservative

    // Evaluation: Higher capacity (no dropping)
    "eval_capacity_factor": 2.0     // Standard
  }
}
```

### Load Balancing Coefficient

```json
{
  "moe": {
    "moe_loss_coeff": 0.001,  // Weak balancing
    "moe_loss_coeff": 0.01,   // Standard (recommended)
    "moe_loss_coeff": 0.1     // Strong balancing
  }
}
```

**Rule**: If load imbalance persists, increase coefficient

## Production Training

### Performance Benchmarks

**From DeepSpeed documentation:**

Standard MoE:
- **5× training cost reduction** vs dense model
- **3× model size reduction** with PR-MoE

Example:
- Dense 13B model: 100% cost
- MoE 13B (128 experts): 20% cost (5× faster)
- PR-MoE 13B: 15% cost + 3× fewer params

### Recommended Dataset

**The Pile** - publicly available training dataset
- 800GB of diverse text
- Standard benchmark for MoE training
- Used in DeepSpeed examples

### Example Configs

**Small MoE (8 experts)**:

```bash
deepspeed --num_gpus 4 pretrain_gpt_moe.py \
  --num-layers 12 \
  --hidden-size 768 \
  --num-attention-heads 12 \
  --num-experts 8 \
  --moe-expert-parallel-size 2 \
  --global-batch-size 128 \
  --fp16
```

**Medium MoE (64 experts)**:

```bash
deepspeed --num_gpus 16 pretrain_gpt_moe.py \
  --num-layers 24 \
  --hidden-size 1024 \
  --num-attention-heads 16 \
  --num-experts 64 \
  --moe-expert-parallel-size 8 \
  --global-batch-size 256 \
  --fp16
```

**Large MoE (128 experts)**:

```bash
deepspeed --num_gpus 32 pretrain_gpt_moe.py \
  --num-layers 32 \
  --hidden-size 2048 \
  --num-attention-heads 32 \
  --num-experts 128 \
  --moe-expert-parallel-size 16 \
  --global-batch-size 512 \
  --fp16
```

### Monitoring

Key metrics to track:

```python
# Expert load balance
expert_counts = [expert.token_count for expert in experts]
load_imbalance = max(expert_counts) / min(expert_counts)

# Should be close to 1.0 (perfectly balanced)
# If > 2.0, increase moe_loss_coeff

# Expert utilization
utilized_experts = sum(count > 0 for count in expert_counts)
utilization_rate = utilized_experts / num_experts

# Should be close to 1.0 (all experts used)

# Token dropping rate
dropped_tokens = total_tokens - processed_tokens
drop_rate = dropped_tokens / total_tokens

# Should be low (<5%) during training
```

## Troubleshooting

### Issue: Load Imbalance

**Symptoms**: Some experts get most tokens

**Solutions**:
1. Increase `moe_loss_coeff` (0.01 → 0.1)
2. Reduce `train_capacity_factor` (forces redistribution)
3. Add noise to router logits (gating network)

### Issue: High Memory Usage

**Solutions**:
1. Enable ZeRO Stage 1 or 2
2. Reduce `train_capacity_factor`
3. Enable `drop_tokens`
4. Increase `moe_expert_parallel_size`

### Issue: Unstable Training

**Solutions**:
1. Lower learning rate
2. Increase warmup steps
3. Use gradient clipping (`--clip-grad 1.0`)
4. Reduce router z-loss coefficient

## Resources

- **DeepSpeed MoE Tutorial**: https://www.deepspeed.ai/tutorials/mixture-of-experts-nlg/
- **Megatron-DeepSpeed**: https://github.com/microsoft/Megatron-DeepSpeed
- **Example Scripts**: `examples_deepspeed/MoE/`




### Architectures

# MoE Model Architectures

Comprehensive guide to different Mixture of Experts architectures and their design patterns.

## Table of Contents
- Mixtral 8x7B (Mistral AI)
- DeepSeek-V3 (DeepSeek AI)
- Switch Transformers (Google)
- GLaM (Google)
- Comparison Table

## Mixtral 8x7B (Mistral AI - 2024)

### Architecture Overview

**Parameters:**
- Total: 47B parameters
- Active per token: 13B (2 experts out of 8)
- Each expert: ~7B parameters

**Key Features:**
- **Top-2 routing**: Each token routed to 2 experts
- **8 experts per layer**: Sparse activation
- **SMoE architecture**: Sparse Mixture of Experts
- **Grouped-Query Attention (GQA)**: Efficient attention mechanism

### Layer Structure

```python
# Mixtral Transformer Block
class MixtralDecoderLayer(nn.Module):
    def __init__(self, config):
        super().__init__()
        self.hidden_size = config.hidden_size

        # Self-attention
        self.self_attn = MixtralAttention(config)

        # MoE Feed-Forward
        self.block_sparse_moe = MixtralSparseMoeBlock(config)

        # Layer norms
        self.input_layernorm = MixtralRMSNorm(config.hidden_size)
        self.post_attention_layernorm = MixtralRMSNorm(config.hidden_size)

    def forward(self, hidden_states, attention_mask=None):
        residual = hidden_states

        # Self-attention
        hidden_states = self.input_layernorm(hidden_states)
        hidden_states = self.self_attn(hidden_states, attention_mask)
        hidden_states = residual + hidden_states

        # MoE FFN
        residual = hidden_states
        hidden_states = self.post_attention_layernorm(hidden_states)
        hidden_states = self.block_sparse_moe(hidden_states)
        hidden_states = residual + hidden_states

        return hidden_states
```

### Sparse MoE Block

```python
class MixtralSparseMoeBlock(nn.Module):
    def __init__(self, config):
        super().__init__()
        self.hidden_dim = config.hidden_size
        self.ffn_dim = config.intermediate_size
        self.num_experts = config.num_local_experts  # 8
        self.top_k = config.num_experts_per_tok       # 2

        # Router (gating network)
        self.gate = nn.Linear(self.hidden_dim, self.num_experts, bias=False)

        # 8 expert FFNs
        self.experts = nn.ModuleList([
            MixtralBlockSparseTop2MLP(config)
            for _ in range(self.num_experts)
        ])

    def forward(self, hidden_states):
        batch_size, sequence_length, hidden_dim = hidden_states.shape
        hidden_states = hidden_states.view(-1, hidden_dim)

        # Router logits (batch * seq_len, num_experts)
        router_logits = self.gate(hidden_states)

        # Top-2 routing
        routing_weights = F.softmax(router_logits, dim=1)
        routing_weights, selected_experts = torch.topk(
            routing_weights, self.top_k, dim=-1
        )

        # Normalize top-2 weights to sum to 1
        routing_weights /= routing_weights.sum(dim=-1, keepdim=True)

        # Route to experts
        final_hidden_states = torch.zeros(
            (batch_size * sequence_length, hidden_dim),
            dtype=hidden_states.dtype,
            device=hidden_states.device
        )

        # Process each expert
        for expert_idx in range(self.num_experts):
            expert_layer = self.experts[expert_idx]
            idx, top_x = torch.where(selected_experts == expert_idx)

            if idx.shape[0] == 0:
                continue

            # Tokens routed to this expert
            top_x_list = top_x.tolist()
            idx_list = idx.tolist()

            # Current expert input
            current_state = hidden_states[None, idx_list].reshape(-1, hidden_dim)
            current_hidden_states = expert_layer(current_state)

            # Weight by routing scores
            current_hidden_states *= routing_weights[idx_list, top_x_list, None]

            # Accumulate
            final_hidden_states.index_add_(0, idx, current_hidden_states.to(hidden_states.dtype))

        final_hidden_states = final_hidden_states.reshape(batch_size, sequence_length, hidden_dim)
        return final_hidden_states
```

### Expert FFN

```python
class MixtralBlockSparseTop2MLP(nn.Module):
    def __init__(self, config):
        super().__init__()
        self.ffn_dim = config.intermediate_size
        self.hidden_dim = config.hidden_size

        self.w1 = nn.Linear(self.hidden_dim, self.ffn_dim, bias=False)
        self.w2 = nn.Linear(self.ffn_dim, self.hidden_dim, bias=False)
        self.w3 = nn.Linear(self.hidden_dim, self.ffn_dim, bias=False)

        self.act_fn = nn.SiLU()

    def forward(self, hidden_states):
        # SwiGLU activation
        current_hidden_states = self.act_fn(self.w1(hidden_states)) * self.w3(hidden_states)
        current_hidden_states = self.w2(current_hidden_states)
        return current_hidden_states
```

### Configuration

```json
{
  "architectures": ["MixtralForCausalLM"],
  "hidden_size": 4096,
  "intermediate_size": 14336,
  "num_attention_heads": 32,
  "num_hidden_layers": 32,
  "num_key_value_heads": 8,
  "num_local_experts": 8,
  "num_experts_per_tok": 2,
  "vocab_size": 32000,
  "max_position_embeddings": 32768,
  "rms_norm_eps": 1e-5,
  "rope_theta": 1000000.0
}
```

## DeepSeek-V3 (DeepSeek AI - December 2024)

### Architecture Overview

**Parameters:**
- Total: 671B parameters
- Active per token: 37B
- Model size: Massive-scale MoE

**Key Innovations:**
1. **DeepSeekMoE**: Finer-grained experts with shared experts
2. **Multi-Head Latent Attention (MLA)**: Reduced KV cache memory
3. **Auxiliary-Loss-Free Load Balancing**: No auxiliary loss needed
4. **Multi-Token Prediction (MTP)**: Predict multiple tokens simultaneously

### DeepSeekMoE Architecture

```python
class DeepSeekMoE(nn.Module):
    """Finer-grained experts with shared experts."""

    def __init__(self, config):
        super().__init__()
        self.num_experts = config.num_experts  # More fine-grained
        self.num_shared_experts = config.num_shared_experts  # e.g., 2
        self.num_routed_experts = self.num_experts - self.num_shared_experts
        self.top_k = config.top_k

        # Shared experts (always activated)
        self.shared_experts = nn.ModuleList([
            FFN(config) for _ in range(self.num_shared_experts)
        ])

        # Routed experts (top-k activated)
        self.routed_experts = nn.ModuleList([
            FFN(config) for _ in range(self.num_routed_experts)
        ])

        # Router for routed experts only
        self.gate = nn.Linear(config.hidden_size, self.num_routed_experts, bias=False)

    def forward(self, x):
        # Shared experts (always computed)
        shared_output = sum(expert(x) for expert in self.shared_experts)

        # Router for top-k routed experts
        router_logits = self.gate(x)
        routing_weights = F.softmax(router_logits, dim=-1)
        routing_weights, selected_experts = torch.topk(routing_weights, self.top_k, dim=-1)
        routing_weights /= routing_weights.sum(dim=-1, keepdim=True)

        # Routed experts output
        routed_output = torch.zeros_like(x)
        for i in range(self.top_k):
            expert_idx = selected_experts[:, :, i]
            expert_weight = routing_weights[:, :, i:i+1]
            for eidx in range(self.num_routed_experts):
                mask = (expert_idx == eidx)
                if mask.any():
                    routed_output[mask] += expert_weight[mask] * self.routed_experts[eidx](x[mask])

        # Combine shared and routed
        return shared_output + routed_output
```

### Multi-Head Latent Attention (MLA)

```python
class MultiHeadLatentAttention(nn.Module):
    """Compress KV cache with latent vectors."""

    def __init__(self, config):
        super().__init__()
        self.hidden_size = config.hidden_size
        self.num_heads = config.num_attention_heads
        self.head_dim = self.hidden_size // self.num_heads
        self.latent_dim = config.latent_dim  # Compressed dimension

        # Project to latent space
        self.q_proj = nn.Linear(self.hidden_size, self.num_heads * self.head_dim)
        self.kv_proj = nn.Linear(self.hidden_size, self.latent_dim)  # Compress!

        # Decompress for attention
        self.k_decompress = nn.Linear(self.latent_dim, self.num_heads * self.head_dim)
        self.v_decompress = nn.Linear(self.latent_dim, self.num_heads * self.head_dim)

        self.o_proj = nn.Linear(self.num_heads * self.head_dim, self.hidden_size)

    def forward(self, hidden_states, past_key_value=None):
        batch_size, seq_len, _ = hidden_states.shape

        # Query
        q = self.q_proj(hidden_states)
        q = q.view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)

        # Compress KV to latent
        kv_latent = self.kv_proj(hidden_states)  # (batch, seq, latent_dim)

        # Store compressed KV in cache (huge memory savings!)
        if past_key_value is not None:
            kv_latent = torch.cat([past_key_value, kv_latent], dim=1)

        # Decompress for attention
        k = self.k_decompress(kv_latent)
        v = self.v_decompress(kv_latent)
        k = k.view(batch_size, -1, self.num_heads, self.head_dim).transpose(1, 2)
        v = v.view(batch_size, -1, self.num_heads, self.head_dim).transpose(1, 2)

        # Attention
        attn_output = F.scaled_dot_product_attention(q, k, v)
        attn_output = attn_output.transpose(1, 2).contiguous()
        attn_output = attn_output.view(batch_size, seq_len, -1)

        return self.o_proj(attn_output), kv_latent
```

### Auxiliary-Loss-Free Load Balancing

```python
# DeepSeek-V3 uses bias terms instead of auxiliary loss
class DeepSeekRouter(nn.Module):
    def __init__(self, hidden_size, num_experts):
        super().__init__()
        self.weight = nn.Parameter(torch.empty(num_experts, hidden_size))
        self.bias = nn.Parameter(torch.zeros(num_experts))  # Load balancing bias!

        # Initialize
        nn.init.kaiming_uniform_(self.weight, a=math.sqrt(5))

    def forward(self, x):
        # Router with bias for load balancing
        logits = F.linear(x, self.weight, self.bias)
        return logits
```

## Switch Transformers (Google - 2021)

### Architecture Overview

**Key Innovation**: Simplest MoE - Top-1 routing

**Parameters:**
- Switch-C: 1.6T parameters
- Active per token: ~10B

### Top-1 Routing

```python
class SwitchTransformersTop1Router(nn.Module):
    """Simplest routing: one expert per token."""

    def __init__(self, config):
        super().__init__()
        self.num_experts = config.num_experts
        self.expert_capacity = config.expert_capacity

        # Router
        self.classifier = nn.Linear(config.d_model, config.num_experts)

    def forward(self, hidden_states):
        # Router logits
        router_logits = self.classifier(hidden_states)

        # Add noise for load balancing (during training)
        if self.training:
            router_logits += torch.randn_like(router_logits) * config.router_jitter_noise

        # Top-1: Argmax (hard routing)
        router_probs = F.softmax(router_logits, dim=-1)
        expert_index = torch.argmax(router_probs, dim=-1)

        # Expert capacity: drop tokens if expert is full
        expert_mask = F.one_hot(expert_index, self.num_experts)
        expert_capacity_mask = self._get_capacity_mask(expert_mask)

        return expert_index, expert_mask, expert_capacity_mask

    def _get_capacity_mask(self, expert_mask):
        """Enforce expert capacity limits."""
        # Count tokens per expert
        tokens_per_expert = expert_mask.sum(dim=0)

        # Mark tokens exceeding capacity
        capacity_mask = tokens_per_expert < self.expert_capacity
        return capacity_mask
```

### Load Balancing Loss

```python
def switch_load_balancing_loss(router_probs, expert_indices, num_experts):
    """Auxiliary loss to encourage uniform expert usage."""
    # Fraction of probability mass assigned to each expert
    router_prob_per_expert = router_probs.mean(dim=0)  # (num_experts,)

    # Fraction of tokens routed to each expert
    expert_counts = F.one_hot(expert_indices, num_experts).float().mean(dim=0)

    # Loss: num_experts * sum(prob_mass * token_fraction)
    # Minimized when both are uniform (1/num_experts)
    loss = num_experts * (router_prob_per_expert * expert_counts).sum()

    return loss
```

## Architecture Comparison Table

| Model | Total Params | Active Params | Routing | Experts/Layer | Top-K | Key Innovation |
|-------|-------------|---------------|---------|---------------|-------|----------------|
| **Mixtral 8x7B** | 47B | 13B | Top-2 | 8 | 2 | Balanced top-2, GQA |
| **DeepSeek-V3** | 671B | 37B | Top-K | Many | Variable | MLA, shared experts, no aux loss |
| **Switch-C** | 1.6T | ~10B | Top-1 | 2048 | 1 | Simplest routing |
| **GLaM** | 1.2T | ~97B | Top-2 | 64 | 2 | Capacity factor tuning |

## Design Patterns

### Pattern 1: Shared + Routed Experts (DeepSeek)

```python
# Best for: Ensuring some experts always activated
output = shared_experts(x) + routed_experts(x)
```

**Pros:**
- Guarantees minimum computation
- Shared experts learn common patterns
- Routed experts specialize

### Pattern 2: Pure Sparse Routing (Mixtral, Switch)

```python
# Best for: Maximum sparsity and efficiency
output = sum(weight_i * expert_i(x) for i in top_k)
```

**Pros:**
- Simplest implementation
- Maximum parameter efficiency
- Clear expert specialization

### Pattern 3: Expert Choice Routing

```python
# Experts choose tokens (instead of tokens choosing experts)
for expert in experts:
    top_k_tokens = expert.select_top_k_tokens(all_tokens)
    expert.process(top_k_tokens)
```

**Pros:**
- Perfect load balancing
- No token dropping
- Variable tokens per expert

## Resources

- **Mixtral Paper**: https://arxiv.org/abs/2401.04088
- **DeepSeek-V3**: https://arxiv.org/abs/2412.19437
- **Switch Transformers**: https://arxiv.org/abs/2101.03961
- **GLaM**: https://arxiv.org/abs/2112.06905




---

## 🚀 Usage

**Reference this template:** `@skill-moe-training.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
