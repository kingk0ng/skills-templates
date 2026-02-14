---
id: skill-sparse-autoencoder-training
type: skill
name: sparse-autoencoder-training
description: Provides guidance for training and analyzing Sparse Autoencoders (SAEs)
  using SAELens to decompose neural network activations into interpretable features.
  Use when discovering interpretable features, analyzing superposition, or studying
  monosemantic representations in language models.
category: ai-research
complexity: medium
keywords:
- api
- github
- python
capabilities: []
token_estimate: 1829
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,829 -->
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




# sparse-autoencoder-training

> Provides guidance for training and analyzing Sparse Autoencoders (SAEs) using SAELens to decompose neural network activations into interpretable features. Use when discovering interpretable features, analyzing superposition, or studying monosemantic representations in language models.

# SAELens: Sparse Autoencoders for Mechanistic Interpretability

SAELens is the primary library for training and analyzing Sparse Autoencoders (SAEs) - a technique for decomposing polysemantic neural network activations into sparse, interpretable features. Based on Anthropic's groundbreaking research on monosemanticity.

**GitHub**: [jbloomAus/SAELens](https://github.com/jbloomAus/SAELens) (1,100+ stars)

## The Problem: Polysemanticity & Superposition

Individual neurons in neural networks are **polysemantic** - they activate in multiple, semantically distinct contexts. This happens because models use **superposition** to represent more features than they have neurons, making interpretability difficult.

**SAEs solve this** by decomposing dense activations into sparse, monosemantic features - typically only a small number of features activate for any given input, and each feature corresponds to an interpretable concept.

## When to Use SAELens

**Use SAELens when you need to:**
- Discover interpretable features in model activations
- Understand what concepts a model has learned
- Study superposition and feature geometry
- Perform feature-based steering or ablation
- Analyze safety-relevant features (deception, bias, harmful content)

**Consider alternatives when:**
- You need basic activation analysis → Use **TransformerLens** directly
- You want causal intervention experiments → Use **pyvene** or **TransformerLens**
- You need production steering → Consider direct activation engineering

## Installation

```bash
pip install sae-lens
```

Requirements: Python 3.10+, transformer-lens>=2.0.0

## Core Concepts

### What SAEs Learn

SAEs are trained to reconstruct model activations through a sparse bottleneck:

```
Input Activation → Encoder → Sparse Features → Decoder → Reconstructed Activation
    (d_model)       ↓        (d_sae >> d_model)    ↓         (d_model)
                 sparsity                      reconstruction
                 penalty                          loss
```

**Loss Function**: `MSE(original, reconstructed) + L1_coefficient × L1(features)`

### Key Validation (Anthropic Research)

In "Towards Monosemanticity", human evaluators found **70% of SAE features genuinely interpretable**. Features discovered include:
- DNA sequences, legal language, HTTP requests
- Hebrew text, nutrition statements, code syntax
- Sentiment, named entities, grammatical structures

## Workflow 1: Loading and Analyzing Pre-trained SAEs

### Step-by-Step

```python
from transformer_lens import HookedTransformer
from sae_lens import SAE

# 1. Load model and pre-trained SAE
model = HookedTransformer.from_pretrained("gpt2-small", device="cuda")
sae, cfg_dict, sparsity = SAE.from_pretrained(
    release="gpt2-small-res-jb",
    sae_id="blocks.8.hook_resid_pre",
    device="cuda"
)

# 2. Get model activations
tokens = model.to_tokens("The capital of France is Paris")
_, cache = model.run_with_cache(tokens)
activations = cache["resid_pre", 8]  # [batch, pos, d_model]

# 3. Encode to SAE features
sae_features = sae.encode(activations)  # [batch, pos, d_sae]
print(f"Active features: {(sae_features > 0).sum()}")

# 4. Find top features for each position
for pos in range(tokens.shape[1]):
    top_features = sae_features[0, pos].topk(5)
    token = model.to_str_tokens(tokens[0, pos:pos+1])[0]
    print(f"Token '{token}': features {top_features.indices.tolist()}")

# 5. Reconstruct activations
reconstructed = sae.decode(sae_features)
reconstruction_error = (activations - reconstructed).norm()
```

### Available Pre-trained SAEs

| Release | Model | Layers |
|---------|-------|--------|
| `gpt2-small-res-jb` | GPT-2 Small | Multiple residual streams |
| `gemma-2b-res` | Gemma 2B | Residual streams |
| Various on HuggingFace | Search tag `saelens` | Various |

### Checklist
- [ ] Load model with TransformerLens
- [ ] Load matching SAE for target layer
- [ ] Encode activations to sparse features
- [ ] Identify top-activating features per token
- [ ] Validate reconstruction quality

## Workflow 2: Training a Custom SAE

### Step-by-Step

```python
from sae_lens import SAE, LanguageModelSAERunnerConfig, SAETrainingRunner

# 1. Configure training
cfg = LanguageModelSAERunnerConfig(
    # Model
    model_name="gpt2-small",
    hook_name="blocks.8.hook_resid_pre",
    hook_layer=8,
    d_in=768,  # Model dimension

    # SAE architecture
    architecture="standard",  # or "gated", "topk"
    d_sae=768 * 8,  # Expansion factor of 8
    activation_fn="relu",

    # Training
    lr=4e-4,
    l1_coefficient=8e-5,  # Sparsity penalty
    l1_warm_up_steps=1000,
    train_batch_size_tokens=4096,
    training_tokens=100_000_000,

    # Data
    dataset_path="monology/pile-uncopyrighted",
    context_size=128,

    # Logging
    log_to_wandb=True,
    wandb_project="sae-training",

    # Checkpointing
    checkpoint_path="checkpoints",
    n_checkpoints=5,
)

# 2. Train
trainer = SAETrainingRunner(cfg)
sae = trainer.run()

# 3. Evaluate
print(f"L0 (avg active features): {trainer.metrics['l0']}")
print(f"CE Loss Recovered: {trainer.metrics['ce_loss_score']}")
```

### Key Hyperparameters

| Parameter | Typical Value | Effect |
|-----------|---------------|--------|
| `d_sae` | 4-16× d_model | More features, higher capacity |
| `l1_coefficient` | 5e-5 to 1e-4 | Higher = sparser, less accurate |
| `lr` | 1e-4 to 1e-3 | Standard optimizer LR |
| `l1_warm_up_steps` | 500-2000 | Prevents early feature death |

### Evaluation Metrics

| Metric | Target | Meaning |
|--------|--------|---------|
| **L0** | 50-200 | Average active features per token |
| **CE Loss Score** | 80-95% | Cross-entropy recovered vs original |
| **Dead Features** | <5% | Features that never activate |
| **Explained Variance** | >90% | Reconstruction quality |

### Checklist
- [ ] Choose target layer and hook point
- [ ] Set expansion factor (d_sae = 4-16× d_model)
- [ ] Tune L1 coefficient for desired sparsity
- [ ] Enable L1 warm-up to prevent dead features
- [ ] Monitor metrics during training (W&B)
- [ ] Validate L0 and CE loss recovery
- [ ] Check dead feature ratio

## Workflow 3: Feature Analysis and Steering

### Analyzing Individual Features

```python
from transformer_lens import HookedTransformer
from sae_lens import SAE
import torch

model = HookedTransformer.from_pretrained("gpt2-small", device="cuda")
sae, _, _ = SAE.from_pretrained(
    release="gpt2-small-res-jb",
    sae_id="blocks.8.hook_resid_pre",
    device="cuda"
)

# Find what activates a specific feature
feature_idx = 1234
test_texts = [
    "The scientist conducted an experiment",
    "I love chocolate cake",
    "The code compiles successfully",
    "Paris is beautiful in spring",
]

for text in test_texts:
    tokens = model.to_tokens(text)
    _, cache = model.run_with_cache(tokens)
    features = sae.encode(cache["resid_pre", 8])
    activation = features[0, :, feature_idx].max().item()
    print(f"{activation:.3f}: {text}")
```

### Feature Steering

```python
def steer_with_feature(model, sae, prompt, feature_idx, strength=5.0):
    """Add SAE feature direction to residual stream."""
    tokens = model.to_tokens(prompt)

    # Get feature direction from decoder
    feature_direction = sae.W_dec[feature_idx]  # [d_model]

    def steering_hook(activation, hook):
        # Add scaled feature direction at all positions
        activation += strength * feature_direction
        return activation

    # Generate with steering
    output = model.generate(
        tokens,
        max_new_tokens=50,
        fwd_hooks=[("blocks.8.hook_resid_pre", steering_hook)]
    )
    return model.to_string(output[0])
```

### Feature Attribution

```python
# Which features most affect a specific output?
tokens = model.to_tokens("The capital of France is")
_, cache = model.run_with_cache(tokens)

# Get features at final position
features = sae.encode(cache["resid_pre", 8])[0, -1]  # [d_sae]

# Get logit attribution per feature
# Feature contribution = feature_activation × decoder_weight × unembedding
W_dec = sae.W_dec  # [d_sae, d_model]
W_U = model.W_U    # [d_model, vocab]

# Contribution to "Paris" logit
paris_token = model.to_single_token(" Paris")
feature_contributions = features * (W_dec @ W_U[:, paris_token])

top_features = feature_contributions.topk(10)
print("Top features for 'Paris' prediction:")
for idx, val in zip(top_features.indices, top_features.values):
    print(f"  Feature {idx.item()}: {val.item():.3f}")
```

## Common Issues & Solutions

### Issue: High dead feature ratio
```python
# WRONG: No warm-up, features die early
cfg = LanguageModelSAERunnerConfig(
    l1_coefficient=1e-4,
    l1_warm_up_steps=0,  # Bad!
)

# RIGHT: Warm-up L1 penalty
cfg = LanguageModelSAERunnerConfig(
    l1_coefficient=8e-5,
    l1_warm_up_steps=1000,  # Gradually increase
    use_ghost_grads=True,   # Revive dead features
)
```

### Issue: Poor reconstruction (low CE recovery)
```python
# Reduce sparsity penalty
cfg = LanguageModelSAERunnerConfig(
    l1_coefficient=5e-5,  # Lower = better reconstruction
    d_sae=768 * 16,       # More capacity
)
```

### Issue: Features not interpretable
```python
# Increase sparsity (higher L1)
cfg = LanguageModelSAERunnerConfig(
    l1_coefficient=1e-4,  # Higher = sparser, more interpretable
)
# Or use TopK architecture
cfg = LanguageModelSAERunnerConfig(
    architecture="topk",
    activation_fn_kwargs={"k": 50},  # Exactly 50 active features
)
```

### Issue: Memory errors during training
```python
cfg = LanguageModelSAERunnerConfig(
    train_batch_size_tokens=2048,  # Reduce batch size
    store_batch_size_prompts=4,    # Fewer prompts in buffer
    n_batches_in_buffer=8,         # Smaller activation buffer
)
```

## Integration with Neuronpedia

Browse pre-trained SAE features at [neuronpedia.org](https://neuronpedia.org):

```python
# Features are indexed by SAE ID
# Example: gpt2-small layer 8 feature 1234
# → neuronpedia.org/gpt2-small/8-res-jb/1234
```

## Key Classes Reference

| Class | Purpose |
|-------|---------|
| `SAE` | Sparse Autoencoder model |
| `LanguageModelSAERunnerConfig` | Training configuration |
| `SAETrainingRunner` | Training loop manager |
| `ActivationsStore` | Activation collection and batching |
| `HookedSAETransformer` | TransformerLens + SAE integration |

## Reference Documentation

For detailed API documentation, tutorials, and advanced usage, see the `references/` folder:

| File | Contents |
|------|----------|
| [references/README.md](references/README.md) | Overview and quick start guide |
| [references/api.md](references/api.md) | Complete API reference for SAE, TrainingSAE, configurations |
| [references/tutorials.md](references/tutorials.md) | Step-by-step tutorials for training, analysis, steering |

## External Resources

### Tutorials
- [Basic Loading & Analysis](https://github.com/jbloomAus/SAELens/blob/main/tutorials/basic_loading_and_analysing.ipynb)
- [Training a Sparse Autoencoder](https://github.com/jbloomAus/SAELens/blob/main/tutorials/training_a_sparse_autoencoder.ipynb)
- [ARENA SAE Curriculum](https://www.lesswrong.com/posts/LnHowHgmrMbWtpkxx/intro-to-superposition-and-sparse-autoencoders-colab)

### Papers
- [Towards Monosemanticity](https://transformer-circuits.pub/2023/monosemantic-features) - Anthropic (2023)
- [Scaling Monosemanticity](https://transformer-circuits.pub/2024/scaling-monosemanticity/) - Anthropic (2024)
- [Sparse Autoencoders Find Highly Interpretable Features](https://arxiv.org/abs/2309.08600) - Cunningham et al. (ICLR 2024)

### Official Documentation
- [SAELens Docs](https://jbloomaus.github.io/SAELens/)
- [Neuronpedia](https://neuronpedia.org) - Feature browser

## SAE Architectures

| Architecture | Description | Use Case |
|--------------|-------------|----------|
| **Standard** | ReLU + L1 penalty | General purpose |
| **Gated** | Learned gating mechanism | Better sparsity control |
| **TopK** | Exactly K active features | Consistent sparsity |

```python
# TopK SAE (exactly 50 features active)
cfg = LanguageModelSAERunnerConfig(
    architecture="topk",
    activation_fn="topk",
    activation_fn_kwargs={"k": 50},
)
```


---


## 📚 Reference Materials


### Readme

# SAELens Reference Documentation

This directory contains comprehensive reference materials for SAELens.

## Contents

- [api.md](api.md) - Complete API reference for SAE, TrainingSAE, and configuration classes
- [tutorials.md](tutorials.md) - Step-by-step tutorials for training and analyzing SAEs
- [papers.md](papers.md) - Key research papers on sparse autoencoders

## Quick Links

- **GitHub Repository**: https://github.com/jbloomAus/SAELens
- **Neuronpedia**: https://neuronpedia.org (browse pre-trained SAE features)
- **HuggingFace SAEs**: Search for tag `saelens`

## Installation

```bash
pip install sae-lens
```

Requirements: Python 3.10+, transformer-lens>=2.0.0

## Basic Usage

```python
from transformer_lens import HookedTransformer
from sae_lens import SAE

# Load model and SAE
model = HookedTransformer.from_pretrained("gpt2-small", device="cuda")
sae, cfg_dict, sparsity = SAE.from_pretrained(
    release="gpt2-small-res-jb",
    sae_id="blocks.8.hook_resid_pre",
    device="cuda"
)

# Encode activations to sparse features
tokens = model.to_tokens("Hello world")
_, cache = model.run_with_cache(tokens)
activations = cache["resid_pre", 8]

features = sae.encode(activations)  # Sparse feature activations
reconstructed = sae.decode(features)  # Reconstructed activations
```

## Key Concepts

### Sparse Autoencoders
SAEs decompose dense neural activations into sparse, interpretable features:
- **Encoder**: Maps d_model → d_sae (typically 4-16x expansion)
- **ReLU/TopK**: Enforces sparsity
- **Decoder**: Reconstructs original activations

### Training Loss
`Loss = MSE(original, reconstructed) + L1_coefficient × L1(features)`

### Key Metrics
- **L0**: Average number of active features (target: 50-200)
- **CE Loss Score**: Cross-entropy recovered vs original model (target: 80-95%)
- **Dead Features**: Features that never activate (target: <5%)

## Available Pre-trained SAEs

| Release | Model | Description |
|---------|-------|-------------|
| `gpt2-small-res-jb` | GPT-2 Small | Residual stream SAEs |
| `gemma-2b-res` | Gemma 2B | Residual stream SAEs |
| Various | Search HuggingFace | Community-trained SAEs |




### Api

# SAELens API Reference

## SAE Class

The core class representing a Sparse Autoencoder.

### Loading Pre-trained SAEs

```python
from sae_lens import SAE

# From official releases
sae, cfg_dict, sparsity = SAE.from_pretrained(
    release="gpt2-small-res-jb",
    sae_id="blocks.8.hook_resid_pre",
    device="cuda"
)

# From HuggingFace
sae, cfg_dict, sparsity = SAE.from_pretrained(
    release="username/repo-name",
    sae_id="path/to/sae",
    device="cuda"
)

# From local disk
sae = SAE.load_from_disk("/path/to/sae", device="cuda")
```

### SAE Attributes

| Attribute | Shape | Description |
|-----------|-------|-------------|
| `W_enc` | [d_in, d_sae] | Encoder weights |
| `W_dec` | [d_sae, d_in] | Decoder weights |
| `b_enc` | [d_sae] | Encoder bias |
| `b_dec` | [d_in] | Decoder bias |
| `cfg` | SAEConfig | Configuration object |

### Core Methods

#### encode()

```python
# Encode activations to sparse features
features = sae.encode(activations)
# Input: [batch, pos, d_in]
# Output: [batch, pos, d_sae]
```

#### decode()

```python
# Reconstruct activations from features
reconstructed = sae.decode(features)
# Input: [batch, pos, d_sae]
# Output: [batch, pos, d_in]
```

#### forward()

```python
# Full forward pass (encode + decode)
reconstructed = sae(activations)
# Returns reconstructed activations
```

#### save_model()

```python
sae.save_model("/path/to/save")
```

---

## SAEConfig

Configuration class for SAE architecture and training context.

### Key Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `d_in` | int | Input dimension (model's d_model) |
| `d_sae` | int | SAE hidden dimension |
| `architecture` | str | "standard", "gated", "jumprelu", "topk" |
| `activation_fn_str` | str | Activation function name |
| `model_name` | str | Source model name |
| `hook_name` | str | Hook point in model |
| `normalize_activations` | str | Normalization method |
| `dtype` | str | Data type |
| `device` | str | Device |

### Accessing Config

```python
print(sae.cfg.d_in)      # 768 for GPT-2 small
print(sae.cfg.d_sae)     # e.g., 24576 (32x expansion)
print(sae.cfg.hook_name) # e.g., "blocks.8.hook_resid_pre"
```

---

## LanguageModelSAERunnerConfig

Comprehensive configuration for training SAEs.

### Example Configuration

```python
from sae_lens import LanguageModelSAERunnerConfig

cfg = LanguageModelSAERunnerConfig(
    # Model and hook
    model_name="gpt2-small",
    hook_name="blocks.8.hook_resid_pre",
    hook_layer=8,
    d_in=768,

    # SAE architecture
    architecture="standard",  # "standard", "gated", "jumprelu", "topk"
    d_sae=768 * 8,           # Expansion factor
    activation_fn="relu",

    # Training hyperparameters
    lr=4e-4,
    l1_coefficient=8e-5,
    lp_norm=1.0,
    lr_scheduler_name="constant",
    lr_warm_up_steps=500,

    # Sparsity control
    l1_warm_up_steps=1000,
    use_ghost_grads=True,
    feature_sampling_window=1000,
    dead_feature_window=5000,
    dead_feature_threshold=1e-8,

    # Data
    dataset_path="monology/pile-uncopyrighted",
    streaming=True,
    context_size=128,

    # Batch sizes
    train_batch_size_tokens=4096,
    store_batch_size_prompts=16,
    n_batches_in_buffer=64,

    # Training duration
    training_tokens=100_000_000,

    # Logging
    log_to_wandb=True,
    wandb_project="sae-training",
    wandb_log_frequency=100,

    # Checkpointing
    checkpoint_path="checkpoints",
    n_checkpoints=5,

    # Hardware
    device="cuda",
    dtype="float32",
)
```

### Key Parameters Explained

#### Architecture Parameters

| Parameter | Description |
|-----------|-------------|
| `architecture` | SAE type: "standard", "gated", "jumprelu", "topk" |
| `d_sae` | Hidden dimension (or use `expansion_factor`) |
| `expansion_factor` | Alternative to d_sae: d_sae = d_in × expansion_factor |
| `activation_fn` | "relu", "topk", etc. |
| `activation_fn_kwargs` | Dict for activation params (e.g., {"k": 50} for topk) |

#### Sparsity Parameters

| Parameter | Description |
|-----------|-------------|
| `l1_coefficient` | L1 penalty weight (higher = sparser) |
| `l1_warm_up_steps` | Steps to ramp up L1 penalty |
| `use_ghost_grads` | Apply gradients to dead features |
| `dead_feature_threshold` | Activation threshold for "dead" |
| `dead_feature_window` | Steps to check for dead features |

#### Learning Rate Parameters

| Parameter | Description |
|-----------|-------------|
| `lr` | Base learning rate |
| `lr_scheduler_name` | "constant", "cosineannealing", etc. |
| `lr_warm_up_steps` | LR warmup steps |
| `lr_decay_steps` | Steps for LR decay |

---

## SAETrainingRunner

Main class for executing training.

### Basic Training

```python
from sae_lens import SAETrainingRunner, LanguageModelSAERunnerConfig

cfg = LanguageModelSAERunnerConfig(...)
runner = SAETrainingRunner(cfg)
sae = runner.run()
```

### Accessing Training Metrics

```python
# During training, metrics logged to W&B include:
# - l0: Average active features
# - ce_loss_score: Cross-entropy recovery
# - mse_loss: Reconstruction loss
# - l1_loss: Sparsity loss
# - dead_features: Count of dead features
```

---

## ActivationsStore

Manages activation collection and batching.

### Basic Usage

```python
from sae_lens import ActivationsStore

store = ActivationsStore.from_sae(
    model=model,
    sae=sae,
    store_batch_size_prompts=8,
    train_batch_size_tokens=4096,
    n_batches_in_buffer=32,
    device="cuda",
)

# Get batch of activations
activations = store.get_batch_tokens()
```

---

## HookedSAETransformer

Integration of SAEs with TransformerLens models.

### Basic Usage

```python
from sae_lens import HookedSAETransformer

# Load model with SAE
model = HookedSAETransformer.from_pretrained("gpt2-small")
model.add_sae(sae)

# Run with SAE in the loop
output = model.run_with_saes(tokens, saes=[sae])

# Cache with SAE activations
output, cache = model.run_with_cache_with_saes(tokens, saes=[sae])
```

---

## SAE Architectures

### Standard (ReLU + L1)

```python
cfg = LanguageModelSAERunnerConfig(
    architecture="standard",
    activation_fn="relu",
    l1_coefficient=8e-5,
)
```

### Gated

```python
cfg = LanguageModelSAERunnerConfig(
    architecture="gated",
)
```

### TopK

```python
cfg = LanguageModelSAERunnerConfig(
    architecture="topk",
    activation_fn="topk",
    activation_fn_kwargs={"k": 50},  # Exactly 50 active features
)
```

### JumpReLU (State-of-the-art)

```python
cfg = LanguageModelSAERunnerConfig(
    architecture="jumprelu",
)
```

---

## Utility Functions

### Upload to HuggingFace

```python
from sae_lens import upload_saes_to_huggingface

upload_saes_to_huggingface(
    saes=[sae],
    repo_id="username/my-saes",
    token="hf_token",
)
```

### Neuronpedia Integration

```python
# Features can be viewed on Neuronpedia
# URL format: neuronpedia.org/{model}/{layer}-{sae_type}/{feature_id}
# Example: neuronpedia.org/gpt2-small/8-res-jb/1234
```




### Tutorials

# SAELens Tutorials

## Tutorial 1: Loading and Analyzing Pre-trained SAEs

### Goal
Load a pre-trained SAE and analyze which features activate on specific inputs.

### Step-by-Step

```python
from transformer_lens import HookedTransformer
from sae_lens import SAE
import torch

# 1. Load model and SAE
model = HookedTransformer.from_pretrained("gpt2-small", device="cuda")
sae, cfg_dict, sparsity = SAE.from_pretrained(
    release="gpt2-small-res-jb",
    sae_id="blocks.8.hook_resid_pre",
    device="cuda"
)

print(f"SAE input dim: {sae.cfg.d_in}")
print(f"SAE hidden dim: {sae.cfg.d_sae}")
print(f"Expansion factor: {sae.cfg.d_sae / sae.cfg.d_in:.1f}x")

# 2. Get model activations
prompt = "The capital of France is Paris"
tokens = model.to_tokens(prompt)
_, cache = model.run_with_cache(tokens)
activations = cache["resid_pre", 8]  # [1, seq_len, 768]

# 3. Encode to SAE features
features = sae.encode(activations)  # [1, seq_len, d_sae]

# 4. Analyze sparsity
active_per_token = (features > 0).sum(dim=-1)
print(f"Average active features per token: {active_per_token.float().mean():.1f}")

# 5. Find top features for each token
str_tokens = model.to_str_tokens(prompt)
for pos in range(len(str_tokens)):
    top_features = features[0, pos].topk(5)
    print(f"\nToken '{str_tokens[pos]}':")
    for feat_idx, feat_val in zip(top_features.indices, top_features.values):
        print(f"  Feature {feat_idx.item()}: {feat_val.item():.3f}")

# 6. Check reconstruction quality
reconstructed = sae.decode(features)
mse = ((activations - reconstructed) ** 2).mean()
print(f"\nReconstruction MSE: {mse.item():.6f}")
```

---

## Tutorial 2: Training a Custom SAE

### Goal
Train a Sparse Autoencoder on GPT-2 activations.

### Step-by-Step

```python
from sae_lens import LanguageModelSAERunnerConfig, SAETrainingRunner

# 1. Configure training
cfg = LanguageModelSAERunnerConfig(
    # Model
    model_name="gpt2-small",
    hook_name="blocks.6.hook_resid_pre",
    hook_layer=6,
    d_in=768,

    # SAE architecture
    architecture="standard",
    d_sae=768 * 8,  # 8x expansion
    activation_fn="relu",

    # Training
    lr=4e-4,
    l1_coefficient=8e-5,
    l1_warm_up_steps=1000,
    train_batch_size_tokens=4096,
    training_tokens=10_000_000,  # Small run for demo

    # Data
    dataset_path="monology/pile-uncopyrighted",
    streaming=True,
    context_size=128,

    # Dead feature prevention
    use_ghost_grads=True,
    dead_feature_window=5000,

    # Logging
    log_to_wandb=True,
    wandb_project="sae-training-demo",

    # Hardware
    device="cuda",
    dtype="float32",
)

# 2. Train
runner = SAETrainingRunner(cfg)
sae = runner.run()

# 3. Save
sae.save_model("./my_trained_sae")
```

### Hyperparameter Tuning Guide

| If you see... | Try... |
|---------------|--------|
| High L0 (>200) | Increase `l1_coefficient` |
| Low CE recovery (<80%) | Decrease `l1_coefficient`, increase `d_sae` |
| Many dead features (>5%) | Enable `use_ghost_grads`, increase `l1_warm_up_steps` |
| Training instability | Lower `lr`, increase `lr_warm_up_steps` |

---

## Tutorial 3: Feature Attribution and Steering

### Goal
Identify which SAE features contribute to specific predictions and use them for steering.

### Step-by-Step

```python
from transformer_lens import HookedTransformer
from sae_lens import SAE
import torch

model = HookedTransformer.from_pretrained("gpt2-small", device="cuda")
sae, _, _ = SAE.from_pretrained(
    release="gpt2-small-res-jb",
    sae_id="blocks.8.hook_resid_pre",
    device="cuda"
)

# 1. Feature attribution for a specific prediction
prompt = "The capital of France is"
tokens = model.to_tokens(prompt)
_, cache = model.run_with_cache(tokens)
activations = cache["resid_pre", 8]
features = sae.encode(activations)

# Target token
target_token = model.to_single_token(" Paris")

# Compute feature contributions to target logit
# contribution = feature_activation * decoder_weight * unembedding
W_dec = sae.W_dec  # [d_sae, d_model]
W_U = model.W_U    # [d_model, d_vocab]

# Feature direction projected to vocabulary
feature_to_logit = W_dec @ W_U  # [d_sae, d_vocab]

# Contribution of each feature to "Paris" at final position
feature_acts = features[0, -1]  # [d_sae]
contributions = feature_acts * feature_to_logit[:, target_token]

# Top contributing features
top_features = contributions.topk(10)
print("Top features contributing to 'Paris':")
for idx, val in zip(top_features.indices, top_features.values):
    print(f"  Feature {idx.item()}: {val.item():.3f}")

# 2. Feature steering
def steer_with_feature(feature_idx, strength=5.0):
    """Add a feature direction to the residual stream."""
    feature_direction = sae.W_dec[feature_idx]  # [d_model]

    def hook(activation, hook_obj):
        activation[:, -1, :] += strength * feature_direction
        return activation

    output = model.generate(
        tokens,
        max_new_tokens=10,
        fwd_hooks=[("blocks.8.hook_resid_pre", hook)]
    )
    return model.to_string(output[0])

# Try steering with top feature
top_feature_idx = top_features.indices[0].item()
print(f"\nSteering with feature {top_feature_idx}:")
print(steer_with_feature(top_feature_idx, strength=10.0))
```

---

## Tutorial 4: Feature Ablation

### Goal
Test the causal importance of features by ablating them.

### Step-by-Step

```python
from transformer_lens import HookedTransformer
from sae_lens import SAE
import torch

model = HookedTransformer.from_pretrained("gpt2-small", device="cuda")
sae, _, _ = SAE.from_pretrained(
    release="gpt2-small-res-jb",
    sae_id="blocks.8.hook_resid_pre",
    device="cuda"
)

prompt = "The capital of France is"
tokens = model.to_tokens(prompt)

# Baseline prediction
baseline_logits = model(tokens)
target_token = model.to_single_token(" Paris")
baseline_prob = torch.softmax(baseline_logits[0, -1], dim=-1)[target_token].item()
print(f"Baseline P(Paris): {baseline_prob:.4f}")

# Get features to ablate
_, cache = model.run_with_cache(tokens)
activations = cache["resid_pre", 8]
features = sae.encode(activations)
top_features = features[0, -1].topk(10).indices

# Ablate top features one by one
for feat_idx in top_features:
    def ablation_hook(activation, hook, feat_idx=feat_idx):
        # Encode → zero feature → decode
        feats = sae.encode(activation)
        feats[:, :, feat_idx] = 0
        return sae.decode(feats)

    ablated_logits = model.run_with_hooks(
        tokens,
        fwd_hooks=[("blocks.8.hook_resid_pre", ablation_hook)]
    )
    ablated_prob = torch.softmax(ablated_logits[0, -1], dim=-1)[target_token].item()
    change = (ablated_prob - baseline_prob) / baseline_prob * 100
    print(f"Ablate feature {feat_idx.item()}: P(Paris)={ablated_prob:.4f} ({change:+.1f}%)")
```

---

## Tutorial 5: Comparing Features Across Prompts

### Goal
Find which features activate consistently for a concept.

### Step-by-Step

```python
from transformer_lens import HookedTransformer
from sae_lens import SAE
import torch

model = HookedTransformer.from_pretrained("gpt2-small", device="cuda")
sae, _, _ = SAE.from_pretrained(
    release="gpt2-small-res-jb",
    sae_id="blocks.8.hook_resid_pre",
    device="cuda"
)

# Test prompts about the same concept
prompts = [
    "The Eiffel Tower is located in",
    "Paris is the capital of",
    "France's largest city is",
    "The Louvre museum is in",
]

# Collect feature activations
all_features = []
for prompt in prompts:
    tokens = model.to_tokens(prompt)
    _, cache = model.run_with_cache(tokens)
    activations = cache["resid_pre", 8]
    features = sae.encode(activations)
    # Take max activation across positions
    max_features = features[0].max(dim=0).values
    all_features.append(max_features)

all_features = torch.stack(all_features)  # [n_prompts, d_sae]

# Find features that activate consistently
mean_activation = all_features.mean(dim=0)
min_activation = all_features.min(dim=0).values

# Features active in ALL prompts
consistent_features = (min_activation > 0.5).nonzero().squeeze(-1)
print(f"Features active in all prompts: {len(consistent_features)}")

# Top consistent features
top_consistent = mean_activation[consistent_features].topk(min(10, len(consistent_features)))
print("\nTop consistent features (possibly 'France/Paris' related):")
for idx, val in zip(top_consistent.indices, top_consistent.values):
    feat_idx = consistent_features[idx].item()
    print(f"  Feature {feat_idx}: mean activation {val.item():.3f}")
```

---

## External Resources

### Official Tutorials
- [Basic Loading & Analysis](https://github.com/jbloomAus/SAELens/blob/main/tutorials/basic_loading_and_analysing.ipynb)
- [Training SAEs](https://github.com/jbloomAus/SAELens/blob/main/tutorials/training_a_sparse_autoencoder.ipynb)
- [Logits Lens with Features](https://github.com/jbloomAus/SAELens/blob/main/tutorials/logits_lens_with_features.ipynb)

### ARENA Curriculum
Comprehensive SAE course: https://www.lesswrong.com/posts/LnHowHgmrMbWtpkxx/intro-to-superposition-and-sparse-autoencoders-colab

### Key Papers
- [Towards Monosemanticity](https://transformer-circuits.pub/2023/monosemantic-features) - Anthropic (2023)
- [Scaling Monosemanticity](https://transformer-circuits.pub/2024/scaling-monosemanticity/) - Anthropic (2024)
- [Sparse Autoencoders Find Interpretable Features](https://arxiv.org/abs/2309.08600) - ICLR 2024




---

## 🚀 Usage

**Reference this template:** `@skill-sparse-autoencoder-training.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
