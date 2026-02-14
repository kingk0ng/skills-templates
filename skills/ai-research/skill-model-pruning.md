---
id: skill-model-pruning
type: skill
name: model-pruning
description: Reduce LLM size and accelerate inference using pruning techniques like
  Wanda and SparseGPT. Use when compressing models without retraining, achieving 50%
  sparsity with minimal accuracy loss, or enabling faster inference on hardware accelerators.
  Covers unstructured pruning, structured pruning, N:M sparsity, magnitude pruning,
  and one-shot methods.
category: ai-research
complexity: medium
keywords:
- deploy
- deployment
- git
- github
- performance
- python
capabilities: []
token_estimate: 1869
has_references: true
reference_count: 1
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,869 -->
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




# model-pruning

> Reduce LLM size and accelerate inference using pruning techniques like Wanda and SparseGPT. Use when compressing models without retraining, achieving 50% sparsity with minimal accuracy loss, or enabling faster inference on hardware accelerators. Covers unstructured pruning, structured pruning, N:M sparsity, magnitude pruning, and one-shot methods.

# Model Pruning: Compressing LLMs

## When to Use This Skill

Use Model Pruning when you need to:
- **Reduce model size** by 40-60% with <1% accuracy loss
- **Accelerate inference** using hardware-friendly sparsity (2-4× speedup)
- **Deploy on constrained hardware** (mobile, edge devices)
- **Compress without retraining** using one-shot methods
- **Enable efficient serving** with reduced memory footprint

**Key Techniques**: Wanda (weights × activations), SparseGPT (second-order), structured pruning, N:M sparsity

**Papers**: Wanda ICLR 2024 (arXiv 2306.11695), SparseGPT (arXiv 2301.00774)

## Installation

```bash
# Wanda implementation
git clone https://github.com/locuslab/wanda
cd wanda
pip install -r requirements.txt

# Optional: SparseGPT
git clone https://github.com/IST-DASLab/sparsegpt
cd sparsegpt
pip install -e .

# Dependencies
pip install torch transformers accelerate
```

## Quick Start

### Wanda Pruning (One-Shot, No Retraining)

**Source**: ICLR 2024 (arXiv 2306.11695)

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load model
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    torch_dtype=torch.float16,
    device_map="cuda"
)
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-7b-hf")

# Calibration data (small dataset for activation statistics)
calib_data = [
    "The quick brown fox jumps over the lazy dog.",
    "Machine learning is transforming the world.",
    "Artificial intelligence powers modern applications.",
]

# Wanda pruning function
def wanda_prune(model, calib_data, sparsity=0.5):
    """
    Wanda: Prune by weight magnitude × input activation.

    Args:
        sparsity: Fraction of weights to prune (0.5 = 50%)
    """
    # 1. Collect activation statistics
    activations = {}

    def hook_fn(name):
        def hook(module, input, output):
            # Store input activation norms
            activations[name] = input[0].detach().abs().mean(dim=0)
        return hook

    # Register hooks for all linear layers
    hooks = []
    for name, module in model.named_modules():
        if isinstance(module, torch.nn.Linear):
            hooks.append(module.register_forward_hook(hook_fn(name)))

    # Run calibration data
    model.eval()
    with torch.no_grad():
        for text in calib_data:
            inputs = tokenizer(text, return_tensors="pt").to(model.device)
            model(**inputs)

    # Remove hooks
    for hook in hooks:
        hook.remove()

    # 2. Prune weights based on |weight| × activation
    for name, module in model.named_modules():
        if isinstance(module, torch.nn.Linear) and name in activations:
            W = module.weight.data
            act = activations[name]

            # Compute importance: |weight| × activation
            importance = W.abs() * act.unsqueeze(0)

            # Flatten and find threshold
            threshold = torch.quantile(importance.flatten(), sparsity)

            # Create mask
            mask = importance >= threshold

            # Apply mask (prune)
            W *= mask.float()

    return model

# Apply Wanda pruning (50% sparsity, one-shot, no retraining)
pruned_model = wanda_prune(model, calib_data, sparsity=0.5)

# Save
pruned_model.save_pretrained("./llama-2-7b-wanda-50")
```

### SparseGPT (Second-Order Pruning)

**Source**: arXiv 2301.00774

```python
from sparsegpt import SparseGPT

# Load model
model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-2-7b-hf")

# Initialize SparseGPT
pruner = SparseGPT(model)

# Calibration data
calib_data = load_calibration_data()  # ~128 samples

# Prune (one-shot, layer-wise reconstruction)
pruned_model = pruner.prune(
    calib_data=calib_data,
    sparsity=0.5,           # 50% sparsity
    prunen=0,               # Unstructured (0) or N:M structured
    prunem=0,
    percdamp=0.01,          # Damping for Hessian inverse
)

# Results: Near-lossless pruning at 50% sparsity
```

### N:M Structured Pruning (Hardware Accelerator)

```python
def nm_prune(weight, n=2, m=4):
    """
    N:M pruning: Keep N weights per M consecutive weights.
    Example: 2:4 = keep 2 out of every 4 weights.

    Compatible with NVIDIA sparse tensor cores (2:4, 4:8).
    """
    # Reshape weight into groups of M
    shape = weight.shape
    weight_flat = weight.flatten()

    # Pad to multiple of M
    pad_size = (m - weight_flat.numel() % m) % m
    weight_padded = F.pad(weight_flat, (0, pad_size))

    # Reshape into (num_groups, m)
    weight_grouped = weight_padded.reshape(-1, m)

    # Find top-N in each group
    _, indices = torch.topk(weight_grouped.abs(), n, dim=-1)

    # Create mask
    mask = torch.zeros_like(weight_grouped)
    mask.scatter_(1, indices, 1.0)

    # Apply mask
    weight_pruned = weight_grouped * mask

    # Reshape back
    weight_pruned = weight_pruned.flatten()[:weight_flat.numel()]
    return weight_pruned.reshape(shape)

# Apply 2:4 sparsity (NVIDIA hardware)
for name, module in model.named_modules():
    if isinstance(module, torch.nn.Linear):
        module.weight.data = nm_prune(module.weight.data, n=2, m=4)

# 50% sparsity, 2× speedup on A100 with sparse tensor cores
```

## Core Concepts

### 1. Pruning Criteria

**Magnitude Pruning** (baseline):
```python
# Prune weights with smallest absolute values
importance = weight.abs()
threshold = torch.quantile(importance, sparsity)
mask = importance >= threshold
```

**Wanda** (weights × activations):
```python
# Importance = |weight| × input_activation
importance = weight.abs() * activation
# Better than magnitude alone (considers usage)
```

**SparseGPT** (second-order):
```python
# Uses Hessian (second derivative) for importance
# More accurate but computationally expensive
importance = weight^2 / diag(Hessian)
```

### 2. Structured vs Unstructured

**Unstructured** (fine-grained):
- Prune individual weights
- Higher quality (better accuracy)
- No hardware speedup (irregular sparsity)

**Structured** (coarse-grained):
- Prune entire neurons, heads, or layers
- Lower quality (more accuracy loss)
- Hardware speedup (regular sparsity)

**Semi-structured (N:M)**:
- Best of both worlds
- 50% sparsity (2:4) → 2× speedup on NVIDIA GPUs
- Minimal accuracy loss

### 3. Sparsity Patterns

```python
# Unstructured (random)
# [1, 0, 1, 0, 1, 1, 0, 0]
# Pros: Flexible, high quality
# Cons: No speedup

# Structured (block)
# [1, 1, 0, 0, 1, 1, 0, 0]
# Pros: Hardware friendly
# Cons: More accuracy loss

# N:M (semi-structured)
# [1, 0, 1, 0] [1, 1, 0, 0]  (2:4 pattern)
# Pros: Hardware speedup + good quality
# Cons: Requires specific hardware (NVIDIA)
```

## Pruning Strategies

### Strategy 1: Gradual Magnitude Pruning

```python
def gradual_prune(model, initial_sparsity=0.0, final_sparsity=0.5, num_steps=100):
    """Gradually increase sparsity during training."""
    for step in range(num_steps):
        # Current sparsity
        current_sparsity = initial_sparsity + (final_sparsity - initial_sparsity) * (step / num_steps)

        # Prune at current sparsity
        for module in model.modules():
            if isinstance(module, torch.nn.Linear):
                weight = module.weight.data
                threshold = torch.quantile(weight.abs().flatten(), current_sparsity)
                mask = weight.abs() >= threshold
                weight *= mask.float()

        # Train one step
        train_step(model)

    return model
```

### Strategy 2: Layer-wise Pruning

```python
def layer_wise_prune(model, sparsity_per_layer):
    """Different sparsity for different layers."""
    # Early layers: Less pruning (more important)
    # Late layers: More pruning (less critical)

    sparsity_schedule = {
        "layer.0": 0.3,   # 30% sparsity
        "layer.1": 0.4,
        "layer.2": 0.5,
        "layer.3": 0.6,   # 60% sparsity
    }

    for name, module in model.named_modules():
        if isinstance(module, torch.nn.Linear):
            # Find layer index
            for layer_name, sparsity in sparsity_schedule.items():
                if layer_name in name:
                    # Prune at layer-specific sparsity
                    prune_layer(module, sparsity)
                    break

    return model
```

### Strategy 3: Iterative Pruning + Fine-tuning

```python
def iterative_prune_finetune(model, target_sparsity=0.5, iterations=5):
    """Prune gradually with fine-tuning between iterations."""
    current_sparsity = 0.0
    sparsity_increment = target_sparsity / iterations

    for i in range(iterations):
        # Increase sparsity
        current_sparsity += sparsity_increment

        # Prune
        prune_model(model, sparsity=current_sparsity)

        # Fine-tune (recover accuracy)
        fine_tune(model, epochs=2, lr=1e-5)

    return model

# Results: Better accuracy than one-shot at high sparsity
```

## Production Deployment

### Complete Pruning Pipeline

```python
from transformers import Trainer, TrainingArguments

def production_pruning_pipeline(
    model_name="meta-llama/Llama-2-7b-hf",
    target_sparsity=0.5,
    method="wanda",  # or "sparsegpt"
):
    # 1. Load model
    model = AutoModelForCausalLM.from_pretrained(model_name, torch_dtype=torch.float16)
    tokenizer = AutoTokenizer.from_pretrained(model_name)

    # 2. Load calibration data
    calib_dataset = load_dataset("wikitext", "wikitext-2-raw-v1", split="train[:1000]")

    # 3. Apply pruning
    if method == "wanda":
        pruned_model = wanda_prune(model, calib_dataset, sparsity=target_sparsity)
    elif method == "sparsegpt":
        pruner = SparseGPT(model)
        pruned_model = pruner.prune(calib_dataset, sparsity=target_sparsity)

    # 4. (Optional) Fine-tune to recover accuracy
    training_args = TrainingArguments(
        output_dir="./pruned-model",
        num_train_epochs=1,
        per_device_train_batch_size=4,
        learning_rate=1e-5,
        bf16=True,
    )

    trainer = Trainer(
        model=pruned_model,
        args=training_args,
        train_dataset=finetune_dataset,
    )

    trainer.train()

    # 5. Save
    pruned_model.save_pretrained("./pruned-llama-7b-50")
    tokenizer.save_pretrained("./pruned-llama-7b-50")

    return pruned_model

# Usage
pruned_model = production_pruning_pipeline(
    model_name="meta-llama/Llama-2-7b-hf",
    target_sparsity=0.5,
    method="wanda"
)
```

### Evaluation

```python
from lm_eval import evaluator

# Evaluate pruned vs original model
original_results = evaluator.simple_evaluate(
    model="hf",
    model_args="pretrained=meta-llama/Llama-2-7b-hf",
    tasks=["arc_easy", "hellaswag", "winogrande"],
)

pruned_results = evaluator.simple_evaluate(
    model="hf",
    model_args="pretrained=./pruned-llama-7b-50",
    tasks=["arc_easy", "hellaswag", "winogrande"],
)

# Compare
print(f"Original: {original_results['results']['arc_easy']['acc']:.3f}")
print(f"Pruned:   {pruned_results['results']['arc_easy']['acc']:.3f}")
print(f"Degradation: {(original_results - pruned_results):.3f}")

# Typical results at 50% sparsity:
# - Wanda: <1% accuracy loss
# - SparseGPT: <0.5% accuracy loss
# - Magnitude: 2-3% accuracy loss
```

## Best Practices

### 1. Sparsity Selection

```python
# Conservative (safe)
sparsity = 0.3  # 30%, <0.5% loss

# Balanced (recommended)
sparsity = 0.5  # 50%, ~1% loss

# Aggressive (risky)
sparsity = 0.7  # 70%, 2-5% loss

# Extreme (model-dependent)
sparsity = 0.9  # 90%, significant degradation
```

### 2. Method Selection

```python
# One-shot, no retraining → Wanda or SparseGPT
if no_retraining_budget:
    use_method = "wanda"  # Faster

# Best quality → SparseGPT
if need_best_quality:
    use_method = "sparsegpt"  # More accurate

# Hardware speedup → N:M structured
if need_speedup:
    use_method = "nm_prune"  # 2:4 or 4:8
```

### 3. Avoid Common Pitfalls

```python
# ❌ Bad: Pruning without calibration data
prune_random(model)  # No activation statistics

# ✅ Good: Use calibration data
prune_wanda(model, calib_data)

# ❌ Bad: Too high sparsity in one shot
prune(model, sparsity=0.9)  # Massive accuracy loss

# ✅ Good: Gradual or iterative
iterative_prune(model, target=0.9, steps=10)
```

## Performance Comparison

**Pruning methods at 50% sparsity** (LLaMA-7B):

| Method | Accuracy Loss | Speed | Memory | Retraining Needed |
|--------|---------------|-------|---------|-------------------|
| **Magnitude** | -2.5% | 1.0× | -50% | No |
| **Wanda** | -0.8% | 1.0× | -50% | No |
| **SparseGPT** | -0.4% | 1.0× | -50% | No |
| **N:M (2:4)** | -1.0% | 2.0× | -50% | No |
| **Structured** | -3.0% | 2.0× | -50% | No |

**Source**: Wanda paper (ICLR 2024), SparseGPT paper

## Resources

- **Wanda Paper (ICLR 2024)**: https://arxiv.org/abs/2306.11695
- **Wanda GitHub**: https://github.com/locuslab/wanda
- **SparseGPT Paper**: https://arxiv.org/abs/2301.00774
- **SparseGPT GitHub**: https://github.com/IST-DASLab/sparsegpt
- **NVIDIA Sparse Tensor Cores**: https://developer.nvidia.com/blog/accelerating-inference-with-sparsity-using-ampere-and-tensorrt/




---


## 📚 Reference Materials


### Wanda

# Wanda: Pruning by Weights and Activations

Based on ICLR 2024 paper (arXiv 2306.11695) - A Simple and Effective Pruning Approach for Large Language Models

## Overview

**Source**: https://arxiv.org/abs/2306.11695
**Conference**: ICLR 2024
**GitHub**: https://github.com/locuslab/wanda

Wanda prunes LLMs by weight magnitude × input activation, achieving 50% sparsity with <1% accuracy loss, no retraining required.

## Core Innovation

### Pruning Criterion

**Key insight**: Weight importance = magnitude × usage

```python
importance(w_ij) = |w_ij| × ||X_i||

where:
- w_ij: Weight connecting input i to output j
- X_i: Input activation norm for dimension i
- ||·||: L2 norm
```

**Intuition**:
- Large weight magnitude → important parameter
- High activation → frequently used dimension
- Product captures both factors

### Comparison with Magnitude Pruning

**Magnitude pruning** (baseline):
```python
importance = |weight|  # Only considers weight size
```

**Wanda**:
```python
importance = |weight| × activation  # Considers usage too
```

**Example**:
```
Weight A: magnitude=0.5, activation=0.1 → importance=0.05
Weight B: magnitude=0.3, activation=0.8 → importance=0.24

Magnitude pruning: Keeps A (larger weight)
Wanda: Keeps B (more important overall) ✓
```

## Algorithm

### One-Shot Pruning

```python
import torch
from transformers import AutoModelForCausalLM

def wanda_prune(model, calib_data, sparsity=0.5):
    """
    Wanda pruning algorithm.

    Steps:
    1. Collect activation statistics on calibration data
    2. Compute importance = |weight| × activation
    3. Prune lowest importance weights
    4. Return pruned model (no retraining!)
    """

    # Step 1: Collect activations
    activations = {}

    def activation_hook(name):
        def hook(module, input, output):
            # Store input activation norms
            X = input[0].detach()
            # Per-input-dimension norm
            act_norm = X.abs().mean(dim=0)  # Average over batch/sequence
            if name in activations:
                activations[name] += act_norm
            else:
                activations[name] = act_norm
        return hook

    # Register hooks
    hooks = []
    for name, module in model.named_modules():
        if isinstance(module, torch.nn.Linear):
            hook = module.register_forward_hook(activation_hook(name))
            hooks.append(hook)

    # Run calibration
    model.eval()
    with torch.no_grad():
        for batch in calib_data:
            model(**batch)

    # Remove hooks
    for hook in hooks:
        hook.remove()

    # Step 2 & 3: Prune based on importance
    for name, module in model.named_modules():
        if isinstance(module, torch.nn.Linear) and name in activations:
            W = module.weight.data
            act = activations[name]

            # Compute importance (per output dimension)
            importance = W.abs() * act.unsqueeze(0)  # (out_features, in_features)

            # Find threshold for sparsity
            threshold = torch.quantile(importance.flatten(), sparsity)

            # Create mask
            mask = importance >= threshold

            # Apply pruning
            W.data *= mask.float()

    return model
```

### Per-Output Pruning

**Key detail**: Pruning is per-output dimension, not global.

```python
# For each output dimension, prune sparsity% of weights

for out_dim in range(out_features):
    # Importance for this output
    importance_out = |W[out_dim, :]| × activation

    # Prune sparsity% of this output's weights
    threshold = quantile(importance_out, sparsity)
    mask_out = importance_out >= threshold

    # Apply
    W[out_dim, :] *= mask_out
```

**Reason**: Ensures each output has similar capacity (balanced pruning).

## Calibration Data

### Requirements

**Amount**: 128 samples (from paper)
**Source**: Any text corpus (C4, WikiText, etc.)
**Length**: 2048 tokens per sample

```python
from datasets import load_dataset

# Load calibration dataset
calib_dataset = load_dataset("allenai/c4", "en", split="train", streaming=True)
calib_samples = []

for i, example in enumerate(calib_dataset):
    if i >= 128:
        break
    text = example['text'][:2048]  # First 2048 chars
    calib_samples.append(text)

# Tokenize
tokenized = tokenizer(
    calib_samples,
    return_tensors="pt",
    padding=True,
    truncation=True,
    max_length=2048
)
```

**Quality**: Higher-quality data → slightly better pruning (but not critical).

## Performance Results

**From ICLR 2024 paper** (LLaMA models on zero-shot tasks):

### Unstructured Sparsity

| Model | Sparsity | Method | Perplexity (WikiText2) | Average Accuracy |
|-------|----------|--------|------------------------|------------------|
| LLaMA-7B | 0% | Baseline | 5.68 | 60.2% |
| LLaMA-7B | 50% | Magnitude | 8.45 | 55.3% (-4.9%) |
| LLaMA-7B | 50% | SparseGPT | 6.32 | 59.1% (-1.1%) |
| LLaMA-7B | 50% | **Wanda** | **6.18** | **59.4% (-0.8%)** |

**Key finding**: Wanda achieves near-SparseGPT quality with much simpler algorithm (no Hessian).

### N:M Structured Sparsity (Hardware-Friendly)

| Model | Sparsity Pattern | Wanda PPL | Magnitude PPL | Speedup |
|-------|------------------|-----------|---------------|---------|
| LLaMA-7B | 2:4 (50%) | 6.42 | 9.12 | 2.0× (on A100) |
| LLaMA-7B | 4:8 (50%) | 6.38 | 8.95 | 2.0× (on A100) |

**N:M sparsity**: Compatible with NVIDIA sparse tensor cores.

### Scaling to Large Models

| Model Size | Sparsity | Wanda PPL | Degradation |
|------------|----------|-----------|-------------|
| LLaMA-7B | 50% | 6.18 | +0.50 |
| LLaMA-13B | 50% | 5.42 | +0.38 |
| LLaMA-30B | 50% | 4.77 | +0.21 |
| LLaMA-65B | 50% | 4.25 | +0.15 |

**Scaling behavior**: Larger models → better pruning (more redundancy).

## Extensions

### Wanda with N:M Sparsity

```python
def wanda_nm_prune(model, calib_data, n=2, m=4):
    """
    Wanda with N:M structured sparsity.

    Keeps top-N weights per M consecutive weights.
    Compatible with NVIDIA sparse tensor cores.
    """
    # Collect activations (same as standard Wanda)
    activations = collect_activations(model, calib_data)

    # Prune with N:M pattern
    for name, module in model.named_modules():
        if isinstance(module, torch.nn.Linear):
            W = module.weight.data
            act = activations[name]

            # Importance
            importance = W.abs() * act.unsqueeze(0)

            # Apply N:M pruning
            W.data = apply_nm_mask(W, importance, n=n, m=m)

    return model

def apply_nm_mask(weight, importance, n=2, m=4):
    """Apply N:M sparsity pattern."""
    shape = weight.shape

    # Flatten and pad to multiple of M
    importance_flat = importance.flatten()
    weight_flat = weight.flatten()

    pad_size = (m - len(importance_flat) % m) % m
    importance_padded = F.pad(importance_flat, (0, pad_size))
    weight_padded = F.pad(weight_flat, (0, pad_size))

    # Reshape into groups of M
    importance_grouped = importance_padded.reshape(-1, m)
    weight_grouped = weight_padded.reshape(-1, m)

    # Find top-N per group
    _, indices = torch.topk(importance_grouped, n, dim=-1)

    # Create mask
    mask = torch.zeros_like(importance_grouped)
    mask.scatter_(1, indices, 1.0)

    # Apply
    weight_pruned = weight_grouped * mask
    weight_pruned = weight_pruned.flatten()[:len(weight_flat)]

    return weight_pruned.reshape(shape)
```

## Comparison with SparseGPT

| Aspect | Wanda | SparseGPT |
|--------|-------|-----------|
| **Complexity** | O(n) per layer | O(n²) per layer (Hessian) |
| **Speed** | Fast (~minutes) | Slow (~hours) |
| **Memory** | Low (activations) | High (Hessian matrix) |
| **Quality (50%)** | -0.8% accuracy | -0.4% accuracy |
| **Implementation** | Simple (~100 lines) | Complex (matrix inverse) |

**Trade-off**:
- Wanda: Simpler, faster, slightly lower quality
- SparseGPT: More complex, slower, slightly higher quality

**Recommendation**: Use Wanda unless you need absolute best quality.

## Practical Deployment

### Complete Pruning Script

```bash
# Clone Wanda repo
git clone https://github.com/locuslab/wanda
cd wanda

# Install dependencies
pip install torch transformers datasets

# Prune LLaMA-7B to 50% sparsity
python main.py \
    --model meta-llama/Llama-2-7b-hf \
    --prune_method wanda \
    --sparsity_ratio 0.5 \
    --sparsity_type unstructured \
    --save ./pruned_models/llama-7b-wanda-50

# Prune with 2:4 structured sparsity (NVIDIA GPUs)
python main.py \
    --model meta-llama/Llama-2-7b-hf \
    --prune_method wanda \
    --sparsity_ratio 0.5 \
    --sparsity_type 2:4 \
    --save ./pruned_models/llama-7b-wanda-2-4
```

### Evaluation

```python
from lm_eval import evaluator

# Evaluate pruned model
results = evaluator.simple_evaluate(
    model="hf",
    model_args="pretrained=./pruned_models/llama-7b-wanda-50",
    tasks=["arc_easy", "arc_challenge", "hellaswag", "winogrande"],
    batch_size=8
)

print("Accuracy after 50% pruning:")
for task, score in results['results'].items():
    print(f"{task}: {score['acc']:.3f}")
```

## Limitations

1. **No retraining**: One-shot only (can't recover from bad pruning)
2. **Activation dependency**: Requires calibration data
3. **Unstructured sparsity**: No speedup without specialized hardware (unless using N:M)

## Resources

- **Paper**: https://arxiv.org/abs/2306.11695
- **GitHub**: https://github.com/locuslab/wanda
- **ICLR 2024**: https://openreview.net/forum?id=PxoFut3dWW




---

## 🚀 Usage

**Reference this template:** `@skill-model-pruning.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
