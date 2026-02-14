---
id: skill-transformer-lens-interpretability
type: skill
name: transformer-lens-interpretability
description: Provides guidance for mechanistic interpretability research using TransformerLens
  to inspect and manipulate transformer internals via HookPoints and activation caching.
  Use when reverse-engineering model algorithms, studying attention patterns, or performing
  activation patching experiments.
category: ai-research
complexity: medium
keywords:
- api
- git
- github
- python
capabilities: []
token_estimate: 1771
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,771 -->
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




# transformer-lens-interpretability

> Provides guidance for mechanistic interpretability research using TransformerLens to inspect and manipulate transformer internals via HookPoints and activation caching. Use when reverse-engineering model algorithms, studying attention patterns, or performing activation patching experiments.

# TransformerLens: Mechanistic Interpretability for Transformers

TransformerLens is the de facto standard library for mechanistic interpretability research on GPT-style language models. Created by Neel Nanda and maintained by Bryce Meyer, it provides clean interfaces to inspect and manipulate model internals via HookPoints on every activation.

**GitHub**: [TransformerLensOrg/TransformerLens](https://github.com/TransformerLensOrg/TransformerLens) (2,900+ stars)

## When to Use TransformerLens

**Use TransformerLens when you need to:**
- Reverse-engineer algorithms learned during training
- Perform activation patching / causal tracing experiments
- Study attention patterns and information flow
- Analyze circuits (e.g., induction heads, IOI circuit)
- Cache and inspect intermediate activations
- Apply direct logit attribution

**Consider alternatives when:**
- You need to work with non-transformer architectures → Use **nnsight** or **pyvene**
- You want to train/analyze Sparse Autoencoders → Use **SAELens**
- You need remote execution on massive models → Use **nnsight** with NDIF
- You want higher-level causal intervention abstractions → Use **pyvene**

## Installation

```bash
pip install transformer-lens
```

For development version:
```bash
pip install git+https://github.com/TransformerLensOrg/TransformerLens
```

## Core Concepts

### HookedTransformer

The main class that wraps transformer models with HookPoints on every activation:

```python
from transformer_lens import HookedTransformer

# Load a model
model = HookedTransformer.from_pretrained("gpt2-small")

# For gated models (LLaMA, Mistral)
import os
os.environ["HF_TOKEN"] = "your_token"
model = HookedTransformer.from_pretrained("meta-llama/Llama-2-7b-hf")
```

### Supported Models (50+)

| Family | Models |
|--------|--------|
| GPT-2 | gpt2, gpt2-medium, gpt2-large, gpt2-xl |
| LLaMA | llama-7b, llama-13b, llama-2-7b, llama-2-13b |
| EleutherAI | pythia-70m to pythia-12b, gpt-neo, gpt-j-6b |
| Mistral | mistral-7b, mixtral-8x7b |
| Others | phi, qwen, opt, gemma |

### Activation Caching

Run the model and cache all intermediate activations:

```python
# Get all activations
tokens = model.to_tokens("The Eiffel Tower is in")
logits, cache = model.run_with_cache(tokens)

# Access specific activations
residual = cache["resid_post", 5]  # Layer 5 residual stream
attn_pattern = cache["pattern", 3]  # Layer 3 attention pattern
mlp_out = cache["mlp_out", 7]  # Layer 7 MLP output

# Filter which activations to cache (saves memory)
logits, cache = model.run_with_cache(
    tokens,
    names_filter=lambda name: "resid_post" in name
)
```

### ActivationCache Keys

| Key Pattern | Shape | Description |
|-------------|-------|-------------|
| `resid_pre, layer` | [batch, pos, d_model] | Residual before attention |
| `resid_mid, layer` | [batch, pos, d_model] | Residual after attention |
| `resid_post, layer` | [batch, pos, d_model] | Residual after MLP |
| `attn_out, layer` | [batch, pos, d_model] | Attention output |
| `mlp_out, layer` | [batch, pos, d_model] | MLP output |
| `pattern, layer` | [batch, head, q_pos, k_pos] | Attention pattern (post-softmax) |
| `q, layer` | [batch, pos, head, d_head] | Query vectors |
| `k, layer` | [batch, pos, head, d_head] | Key vectors |
| `v, layer` | [batch, pos, head, d_head] | Value vectors |

## Workflow 1: Activation Patching (Causal Tracing)

Identify which activations causally affect model output by patching clean activations into corrupted runs.

### Step-by-Step

```python
from transformer_lens import HookedTransformer, patching
import torch

model = HookedTransformer.from_pretrained("gpt2-small")

# 1. Define clean and corrupted prompts
clean_prompt = "The Eiffel Tower is in the city of"
corrupted_prompt = "The Colosseum is in the city of"

clean_tokens = model.to_tokens(clean_prompt)
corrupted_tokens = model.to_tokens(corrupted_prompt)

# 2. Get clean activations
_, clean_cache = model.run_with_cache(clean_tokens)

# 3. Define metric (e.g., logit difference)
paris_token = model.to_single_token(" Paris")
rome_token = model.to_single_token(" Rome")

def metric(logits):
    return logits[0, -1, paris_token] - logits[0, -1, rome_token]

# 4. Patch each position and layer
results = torch.zeros(model.cfg.n_layers, clean_tokens.shape[1])

for layer in range(model.cfg.n_layers):
    for pos in range(clean_tokens.shape[1]):
        def patch_hook(activation, hook):
            activation[0, pos] = clean_cache[hook.name][0, pos]
            return activation

        patched_logits = model.run_with_hooks(
            corrupted_tokens,
            fwd_hooks=[(f"blocks.{layer}.hook_resid_post", patch_hook)]
        )
        results[layer, pos] = metric(patched_logits)

# 5. Visualize results (layer x position heatmap)
```

### Checklist
- [ ] Define clean and corrupted inputs that differ minimally
- [ ] Choose metric that captures behavior difference
- [ ] Cache clean activations
- [ ] Systematically patch each (layer, position) combination
- [ ] Visualize results as heatmap
- [ ] Identify causal hotspots

## Workflow 2: Circuit Analysis (Indirect Object Identification)

Replicate the IOI circuit discovery from "Interpretability in the Wild".

### Step-by-Step

```python
from transformer_lens import HookedTransformer
import torch

model = HookedTransformer.from_pretrained("gpt2-small")

# IOI task: "When John and Mary went to the store, Mary gave a bottle to"
# Model should predict "John" (indirect object)

prompt = "When John and Mary went to the store, Mary gave a bottle to"
tokens = model.to_tokens(prompt)

# 1. Get baseline logits
logits, cache = model.run_with_cache(tokens)

john_token = model.to_single_token(" John")
mary_token = model.to_single_token(" Mary")

# 2. Compute logit difference (IO - S)
logit_diff = logits[0, -1, john_token] - logits[0, -1, mary_token]
print(f"Logit difference: {logit_diff.item():.3f}")

# 3. Direct logit attribution by head
def get_head_contribution(layer, head):
    # Project head output to logits
    head_out = cache["z", layer][0, :, head, :]  # [pos, d_head]
    W_O = model.W_O[layer, head]  # [d_head, d_model]
    W_U = model.W_U  # [d_model, vocab]

    # Head contribution to logits at final position
    contribution = head_out[-1] @ W_O @ W_U
    return contribution[john_token] - contribution[mary_token]

# 4. Map all heads
head_contributions = torch.zeros(model.cfg.n_layers, model.cfg.n_heads)
for layer in range(model.cfg.n_layers):
    for head in range(model.cfg.n_heads):
        head_contributions[layer, head] = get_head_contribution(layer, head)

# 5. Identify top contributing heads (name movers, backup name movers)
```

### Checklist
- [ ] Set up task with clear IO/S tokens
- [ ] Compute baseline logit difference
- [ ] Decompose by attention head contributions
- [ ] Identify key circuit components (name movers, S-inhibition, induction)
- [ ] Validate with ablation experiments

## Workflow 3: Induction Head Detection

Find induction heads that implement [A][B]...[A] → [B] pattern.

```python
from transformer_lens import HookedTransformer
import torch

model = HookedTransformer.from_pretrained("gpt2-small")

# Create repeated sequence: [A][B][A] should predict [B]
repeated_tokens = torch.tensor([[1000, 2000, 1000]])  # Arbitrary tokens

_, cache = model.run_with_cache(repeated_tokens)

# Induction heads attend from final [A] back to first [B]
# Check attention from position 2 to position 1
induction_scores = torch.zeros(model.cfg.n_layers, model.cfg.n_heads)

for layer in range(model.cfg.n_layers):
    pattern = cache["pattern", layer][0]  # [head, q_pos, k_pos]
    # Attention from pos 2 to pos 1
    induction_scores[layer] = pattern[:, 2, 1]

# Heads with high scores are induction heads
top_heads = torch.topk(induction_scores.flatten(), k=5)
```

## Common Issues & Solutions

### Issue: Hooks persist after debugging
```python
# WRONG: Old hooks remain active
model.run_with_hooks(tokens, fwd_hooks=[...])  # Debug, add new hooks
model.run_with_hooks(tokens, fwd_hooks=[...])  # Old hooks still there!

# RIGHT: Always reset hooks
model.reset_hooks()
model.run_with_hooks(tokens, fwd_hooks=[...])
```

### Issue: Tokenization gotchas
```python
# WRONG: Assuming consistent tokenization
model.to_tokens("Tim")  # Single token
model.to_tokens("Neel")  # Becomes "Ne" + "el" (two tokens!)

# RIGHT: Check tokenization explicitly
tokens = model.to_tokens("Neel", prepend_bos=False)
print(model.to_str_tokens(tokens))  # ['Ne', 'el']
```

### Issue: LayerNorm ignored in analysis
```python
# WRONG: Ignoring LayerNorm
pre_activation = residual @ model.W_in[layer]

# RIGHT: Include LayerNorm
ln_scale = model.blocks[layer].ln2.w
ln_out = model.blocks[layer].ln2(residual)
pre_activation = ln_out @ model.W_in[layer]
```

### Issue: Memory explosion with large models
```python
# Use selective caching
logits, cache = model.run_with_cache(
    tokens,
    names_filter=lambda n: "resid_post" in n or "pattern" in n,
    device="cpu"  # Cache on CPU
)
```

## Key Classes Reference

| Class | Purpose |
|-------|---------|
| `HookedTransformer` | Main model wrapper with hooks |
| `ActivationCache` | Dictionary-like cache of activations |
| `HookedTransformerConfig` | Model configuration |
| `FactoredMatrix` | Efficient factored matrix operations |

## Integration with SAELens

TransformerLens integrates with SAELens for Sparse Autoencoder analysis:

```python
from transformer_lens import HookedTransformer
from sae_lens import SAE

model = HookedTransformer.from_pretrained("gpt2-small")
sae = SAE.from_pretrained("gpt2-small-res-jb", "blocks.8.hook_resid_pre")

# Run with SAE
tokens = model.to_tokens("Hello world")
_, cache = model.run_with_cache(tokens)
sae_acts = sae.encode(cache["resid_pre", 8])
```

## Reference Documentation

For detailed API documentation, tutorials, and advanced usage, see the `references/` folder:

| File | Contents |
|------|----------|
| [references/README.md](references/README.md) | Overview and quick start guide |
| [references/api.md](references/api.md) | Complete API reference for HookedTransformer, ActivationCache, HookPoints |
| [references/tutorials.md](references/tutorials.md) | Step-by-step tutorials for activation patching, circuit analysis, logit lens |

## External Resources

### Tutorials
- [Main Demo Notebook](https://transformerlensorg.github.io/TransformerLens/generated/demos/Main_Demo.html)
- [Activation Patching Demo](https://colab.research.google.com/github/TransformerLensOrg/TransformerLens/blob/main/demos/Activation_Patching_in_TL_Demo.ipynb)
- [ARENA Mech Interp Course](https://arena-foundation.github.io/ARENA/) - 200+ hours of tutorials

### Papers
- [A Mathematical Framework for Transformer Circuits](https://transformer-circuits.pub/2021/framework/index.html)
- [In-context Learning and Induction Heads](https://transformer-circuits.pub/2022/in-context-learning-and-induction-heads/index.html)
- [Interpretability in the Wild (IOI)](https://arxiv.org/abs/2211.00593)

### Official Documentation
- [Official Docs](https://transformerlensorg.github.io/TransformerLens/)
- [Model Properties Table](https://transformerlensorg.github.io/TransformerLens/generated/model_properties_table.html)
- [Neel Nanda's Glossary](https://www.neelnanda.io/mechanistic-interpretability/glossary)

## Version Notes

- **v2.0**: Removed HookedSAE (moved to SAELens)
- **v3.0 (alpha)**: TransformerBridge for loading any nn.Module


---


## 📚 Reference Materials


### Readme

# TransformerLens Reference Documentation

This directory contains comprehensive reference materials for TransformerLens.

## Contents

- [api.md](api.md) - Complete API reference for HookedTransformer, ActivationCache, and HookPoints
- [tutorials.md](tutorials.md) - Step-by-step tutorials for common interpretability workflows
- [papers.md](papers.md) - Key research papers and foundational concepts

## Quick Links

- **Official Documentation**: https://transformerlensorg.github.io/TransformerLens/
- **GitHub Repository**: https://github.com/TransformerLensOrg/TransformerLens
- **Model Properties Table**: https://transformerlensorg.github.io/TransformerLens/generated/model_properties_table.html

## Installation

```bash
pip install transformer-lens
```

## Basic Usage

```python
from transformer_lens import HookedTransformer

# Load model
model = HookedTransformer.from_pretrained("gpt2-small")

# Run with activation caching
tokens = model.to_tokens("Hello world")
logits, cache = model.run_with_cache(tokens)

# Access activations
residual = cache["resid_post", 5]  # Layer 5 residual stream
attention = cache["pattern", 3]    # Layer 3 attention patterns
```

## Key Concepts

### HookPoints
Every activation in the transformer has a HookPoint wrapper, enabling:
- Reading activations via `run_with_cache()`
- Modifying activations via `run_with_hooks()`

### Activation Cache
The `ActivationCache` stores all intermediate activations with helper methods for:
- Residual stream decomposition
- Logit attribution
- Layer-wise analysis

### Supported Models (50+)
GPT-2, LLaMA, Mistral, Pythia, GPT-Neo, OPT, Gemma, Phi, and more.




### Api

# TransformerLens API Reference

## HookedTransformer

The core class for mechanistic interpretability, wrapping transformer models with hooks on every activation.

### Loading Models

```python
from transformer_lens import HookedTransformer

# Basic loading
model = HookedTransformer.from_pretrained("gpt2-small")

# With specific device/dtype
model = HookedTransformer.from_pretrained(
    "gpt2-medium",
    device="cuda",
    dtype=torch.float16
)

# Gated models (LLaMA, Mistral)
import os
os.environ["HF_TOKEN"] = "your_token"
model = HookedTransformer.from_pretrained("meta-llama/Llama-2-7b-hf")
```

### from_pretrained() Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `model_name` | str | required | Model name from OFFICIAL_MODEL_NAMES |
| `fold_ln` | bool | True | Fold LayerNorm weights into subsequent layers |
| `center_writing_weights` | bool | True | Center residual stream writer means |
| `center_unembed` | bool | True | Center unembedding weights |
| `dtype` | torch.dtype | None | Model precision |
| `device` | str | None | Target device |
| `n_devices` | int | 1 | Number of devices for model parallelism |

### Weight Matrices

| Property | Shape | Description |
|----------|-------|-------------|
| `W_E` | [d_vocab, d_model] | Token embedding matrix |
| `W_U` | [d_model, d_vocab] | Unembedding matrix |
| `W_pos` | [n_ctx, d_model] | Positional embedding |
| `W_Q` | [n_layers, n_heads, d_model, d_head] | Query weights |
| `W_K` | [n_layers, n_heads, d_model, d_head] | Key weights |
| `W_V` | [n_layers, n_heads, d_model, d_head] | Value weights |
| `W_O` | [n_layers, n_heads, d_head, d_model] | Output weights |
| `W_in` | [n_layers, d_model, d_mlp] | MLP input weights |
| `W_out` | [n_layers, d_mlp, d_model] | MLP output weights |

### Core Methods

#### forward()

```python
logits = model(tokens)
logits = model(tokens, return_type="logits")
loss = model(tokens, return_type="loss")
logits, loss = model(tokens, return_type="both")
```

Parameters:
- `input`: Token tensor or string
- `return_type`: "logits", "loss", "both", or None
- `prepend_bos`: Whether to prepend BOS token
- `start_at_layer`: Start execution from specific layer
- `stop_at_layer`: Stop execution at specific layer

#### run_with_cache()

```python
logits, cache = model.run_with_cache(tokens)

# Selective caching (saves memory)
logits, cache = model.run_with_cache(
    tokens,
    names_filter=lambda name: "resid_post" in name
)

# Cache on CPU
logits, cache = model.run_with_cache(tokens, device="cpu")
```

#### run_with_hooks()

```python
def my_hook(activation, hook):
    # Modify activation
    activation[:, :, 0] = 0
    return activation

logits = model.run_with_hooks(
    tokens,
    fwd_hooks=[("blocks.5.hook_resid_post", my_hook)]
)
```

#### generate()

```python
output = model.generate(
    tokens,
    max_new_tokens=50,
    temperature=0.7,
    top_k=40,
    top_p=0.9,
    freq_penalty=1.0,
    use_past_kv_cache=True
)
```

### Tokenization Methods

```python
# String to tokens
tokens = model.to_tokens("Hello world")  # [1, seq_len]
tokens = model.to_tokens("Hello", prepend_bos=False)

# Tokens to string
text = model.to_string(tokens)

# Get string tokens (for debugging)
str_tokens = model.to_str_tokens("Hello world")
# ['<|endoftext|>', 'Hello', ' world']

# Single token validation
token_id = model.to_single_token(" Paris")  # Returns int or raises error
```

### Hook Management

```python
# Clear all hooks
model.reset_hooks()

# Add permanent hook
model.add_hook("blocks.0.hook_resid_post", my_hook)

# Remove specific hook
model.remove_hook("blocks.0.hook_resid_post")
```

---

## ActivationCache

Stores and provides access to all activations from a forward pass.

### Accessing Activations

```python
logits, cache = model.run_with_cache(tokens)

# By name and layer
residual = cache["resid_post", 5]
attention = cache["pattern", 3]
mlp_out = cache["mlp_out", 7]

# Full name string
residual = cache["blocks.5.hook_resid_post"]
```

### Cache Keys

| Key Pattern | Shape | Description |
|-------------|-------|-------------|
| `hook_embed` | [batch, pos, d_model] | Token embeddings |
| `hook_pos_embed` | [batch, pos, d_model] | Positional embeddings |
| `resid_pre, layer` | [batch, pos, d_model] | Residual before attention |
| `resid_mid, layer` | [batch, pos, d_model] | Residual after attention |
| `resid_post, layer` | [batch, pos, d_model] | Residual after MLP |
| `attn_out, layer` | [batch, pos, d_model] | Attention output |
| `mlp_out, layer` | [batch, pos, d_model] | MLP output |
| `pattern, layer` | [batch, head, q_pos, k_pos] | Attention pattern (post-softmax) |
| `attn_scores, layer` | [batch, head, q_pos, k_pos] | Attention scores (pre-softmax) |
| `q, layer` | [batch, pos, head, d_head] | Query vectors |
| `k, layer` | [batch, pos, head, d_head] | Key vectors |
| `v, layer` | [batch, pos, head, d_head] | Value vectors |
| `z, layer` | [batch, pos, head, d_head] | Attention output per head |

### Analysis Methods

#### decompose_resid()

Decomposes residual stream into component contributions:

```python
components, labels = cache.decompose_resid(
    layer=5,
    return_labels=True,
    mode="attn"  # or "mlp" or "full"
)
```

#### accumulated_resid()

Get accumulated residual at each layer (for Logit Lens):

```python
accumulated = cache.accumulated_resid(
    layer=None,  # All layers
    incl_mid=False,
    apply_ln=True  # Apply final LayerNorm
)
```

#### logit_attrs()

Calculate logit attribution for components:

```python
attrs = cache.logit_attrs(
    residual_stack,
    tokens=target_tokens,
    incorrect_tokens=incorrect_tokens
)
```

#### stack_head_results()

Stack attention head outputs:

```python
head_results = cache.stack_head_results(
    layer=-1,  # All layers
    pos_slice=None  # All positions
)
# Shape: [n_layers, n_heads, batch, pos, d_model]
```

### Utility Methods

```python
# Move cache to device
cache = cache.to("cpu")

# Remove batch dimension (for batch_size=1)
cache = cache.remove_batch_dim()

# Get all keys
keys = cache.keys()

# Iterate
for name, activation in cache.items():
    print(name, activation.shape)
```

---

## HookPoint

The fundamental hook mechanism wrapping every activation.

### Hook Function Signature

```python
def hook_fn(activation: torch.Tensor, hook: HookPoint) -> torch.Tensor:
    """
    Args:
        activation: Current activation value
        hook: The HookPoint object (has .name attribute)

    Returns:
        Modified activation (or None to keep original)
    """
    # Modify activation
    return activation
```

### Common Hook Patterns

```python
# Zero ablation
def zero_hook(act, hook):
    act[:, :, :] = 0
    return act

# Mean ablation
def mean_hook(act, hook):
    act[:, :, :] = act.mean(dim=0, keepdim=True)
    return act

# Patch from cache
def patch_hook(act, hook):
    act[:, 5, :] = clean_cache[hook.name][:, 5, :]
    return act

# Add steering vector
def steer_hook(act, hook):
    act += 0.5 * steering_vector
    return act
```

---

## Utility Functions

### patching module

```python
from transformer_lens import patching

# Generic activation patching
results = patching.generic_activation_patch(
    model=model,
    corrupted_tokens=corrupted,
    clean_cache=clean_cache,
    patching_metric=metric_fn,
    patch_setter=patch_fn,
    activation_name="resid_post",
    index_axis_names=("layer", "pos")
)
```

### FactoredMatrix

Efficient operations on factored weight matrices:

```python
from transformer_lens import FactoredMatrix

# QK circuit
QK = FactoredMatrix(model.W_Q[layer], model.W_K[layer].T)

# OV circuit
OV = FactoredMatrix(model.W_V[layer], model.W_O[layer])

# Get full matrix
full = QK.AB

# SVD decomposition
U, S, V = QK.svd()
```

---

## Configuration

### HookedTransformerConfig

Key configuration attributes:

| Attribute | Description |
|-----------|-------------|
| `n_layers` | Number of transformer layers |
| `n_heads` | Number of attention heads |
| `d_model` | Model dimension |
| `d_head` | Head dimension |
| `d_mlp` | MLP hidden dimension |
| `d_vocab` | Vocabulary size |
| `n_ctx` | Maximum context length |
| `act_fn` | Activation function name |
| `normalization_type` | "LN" or "LNPre" |

Access via:
```python
model.cfg.n_layers
model.cfg.d_model
```




### Tutorials

# TransformerLens Tutorials

## Tutorial 1: Basic Activation Analysis

### Goal
Understand how to load models, cache activations, and inspect model internals.

### Step-by-Step

```python
from transformer_lens import HookedTransformer
import torch

# 1. Load model
model = HookedTransformer.from_pretrained("gpt2-small")
print(f"Model has {model.cfg.n_layers} layers, {model.cfg.n_heads} heads")

# 2. Tokenize input
prompt = "The capital of France is"
tokens = model.to_tokens(prompt)
print(f"Tokens shape: {tokens.shape}")
print(f"String tokens: {model.to_str_tokens(prompt)}")

# 3. Run with cache
logits, cache = model.run_with_cache(tokens)
print(f"Logits shape: {logits.shape}")
print(f"Cache keys: {len(cache.keys())}")

# 4. Inspect activations
for layer in range(model.cfg.n_layers):
    resid = cache["resid_post", layer]
    print(f"Layer {layer} residual norm: {resid.norm().item():.2f}")

# 5. Look at attention patterns
attn = cache["pattern", 0]  # Layer 0
print(f"Attention shape: {attn.shape}")  # [batch, heads, q_pos, k_pos]

# 6. Get top predictions
probs = torch.softmax(logits[0, -1], dim=-1)
top_tokens = probs.topk(5)
for token_id, prob in zip(top_tokens.indices, top_tokens.values):
    print(f"{model.to_string(token_id.unsqueeze(0))}: {prob.item():.3f}")
```

---

## Tutorial 2: Activation Patching

### Goal
Identify which activations causally affect model output.

### Concept
1. Run model on "clean" input, cache activations
2. Run model on "corrupted" input
3. Patch clean activations into corrupted run
4. Measure effect on output

### Step-by-Step

```python
from transformer_lens import HookedTransformer
import torch

model = HookedTransformer.from_pretrained("gpt2-small")

# Define clean and corrupted prompts
clean_prompt = "The Eiffel Tower is in the city of"
corrupted_prompt = "The Colosseum is in the city of"

clean_tokens = model.to_tokens(clean_prompt)
corrupted_tokens = model.to_tokens(corrupted_prompt)

# Get clean activations
_, clean_cache = model.run_with_cache(clean_tokens)

# Define metric
paris_token = model.to_single_token(" Paris")
rome_token = model.to_single_token(" Rome")

def logit_diff(logits):
    """Positive = model prefers Paris over Rome"""
    return (logits[0, -1, paris_token] - logits[0, -1, rome_token]).item()

# Baseline measurements
clean_logits = model(clean_tokens)
corrupted_logits = model(corrupted_tokens)
print(f"Clean logit diff: {logit_diff(clean_logits):.3f}")
print(f"Corrupted logit diff: {logit_diff(corrupted_logits):.3f}")

# Patch each layer
results = []
for layer in range(model.cfg.n_layers):
    def patch_hook(activation, hook, layer=layer):
        activation[:] = clean_cache["resid_post", layer]
        return activation

    patched_logits = model.run_with_hooks(
        corrupted_tokens,
        fwd_hooks=[(f"blocks.{layer}.hook_resid_post", patch_hook)]
    )
    results.append(logit_diff(patched_logits))
    print(f"Layer {layer}: {results[-1]:.3f}")

# Find most important layer
best_layer = max(range(len(results)), key=lambda i: results[i])
print(f"\nMost important layer: {best_layer}")
```

### Position-Specific Patching

```python
import torch

seq_len = clean_tokens.shape[1]
results = torch.zeros(model.cfg.n_layers, seq_len)

for layer in range(model.cfg.n_layers):
    for pos in range(seq_len):
        def patch_hook(activation, hook, layer=layer, pos=pos):
            activation[:, pos, :] = clean_cache["resid_post", layer][:, pos, :]
            return activation

        patched_logits = model.run_with_hooks(
            corrupted_tokens,
            fwd_hooks=[(f"blocks.{layer}.hook_resid_post", patch_hook)]
        )
        results[layer, pos] = logit_diff(patched_logits)

# Visualize as heatmap
import matplotlib.pyplot as plt
plt.figure(figsize=(12, 8))
plt.imshow(results.numpy(), aspect='auto', cmap='RdBu')
plt.xlabel('Position')
plt.ylabel('Layer')
plt.colorbar(label='Logit Difference')
plt.title('Activation Patching Results')
```

---

## Tutorial 3: Direct Logit Attribution

### Goal
Identify which components (heads, neurons) contribute to specific predictions.

### Step-by-Step

```python
from transformer_lens import HookedTransformer
import torch

model = HookedTransformer.from_pretrained("gpt2-small")

prompt = "The capital of France is"
tokens = model.to_tokens(prompt)
logits, cache = model.run_with_cache(tokens)

# Target token
target_token = model.to_single_token(" Paris")

# Get unembedding direction for target
target_direction = model.W_U[:, target_token]  # [d_model]

# Attribution per attention head
head_contributions = torch.zeros(model.cfg.n_layers, model.cfg.n_heads)

for layer in range(model.cfg.n_layers):
    # Get per-head output at final position
    z = cache["z", layer][0, -1]  # [n_heads, d_head]

    for head in range(model.cfg.n_heads):
        # Project through W_O to get contribution to residual
        head_out = z[head] @ model.W_O[layer, head]  # [d_model]

        # Dot with target direction
        contribution = (head_out @ target_direction).item()
        head_contributions[layer, head] = contribution

# Find top contributing heads
flat_idx = head_contributions.flatten().topk(10)
print("Top 10 heads for predicting 'Paris':")
for idx, val in zip(flat_idx.indices, flat_idx.values):
    layer = idx.item() // model.cfg.n_heads
    head = idx.item() % model.cfg.n_heads
    print(f"  L{layer}H{head}: {val.item():.3f}")
```

---

## Tutorial 4: Induction Head Detection

### Goal
Find attention heads that implement the [A][B]...[A] → [B] pattern.

### Step-by-Step

```python
from transformer_lens import HookedTransformer
import torch

model = HookedTransformer.from_pretrained("gpt2-small")

# Create repeated sequence pattern
# Pattern: [A][B][C][A] - model should attend from last A to B
seq = torch.randint(1000, 5000, (1, 20))
# Repeat first half
seq[0, 10:] = seq[0, :10]

_, cache = model.run_with_cache(seq)

# For induction heads: position i should attend to position (i - seq_len/2 + 1)
# At position 10 (second A), should attend to position 1 (first B)

induction_scores = torch.zeros(model.cfg.n_layers, model.cfg.n_heads)

for layer in range(model.cfg.n_layers):
    pattern = cache["pattern", layer][0]  # [heads, q_pos, k_pos]

    # Check attention from repeated positions to position after first occurrence
    for offset in range(1, 10):
        q_pos = 10 + offset  # Position in second half
        k_pos = offset       # Should attend to corresponding position in first half

        # Average attention to the "correct" position
        induction_scores[layer] += pattern[:, q_pos, k_pos]

    induction_scores[layer] /= 9  # Average over offsets

# Find top induction heads
print("Top induction heads:")
for layer in range(model.cfg.n_layers):
    for head in range(model.cfg.n_heads):
        score = induction_scores[layer, head].item()
        if score > 0.3:
            print(f"  L{layer}H{head}: {score:.3f}")
```

---

## Tutorial 5: Logit Lens

### Goal
See what the model "believes" at each layer before final unembedding.

### Step-by-Step

```python
from transformer_lens import HookedTransformer
import torch

model = HookedTransformer.from_pretrained("gpt2-small")

prompt = "The quick brown fox jumps over the lazy"
tokens = model.to_tokens(prompt)
logits, cache = model.run_with_cache(tokens)

# Get accumulated residual at each layer
# Apply LayerNorm to match what unembedding sees
accumulated = cache.accumulated_resid(layer=None, incl_mid=False, apply_ln=True)
# Shape: [n_layers + 1, batch, pos, d_model]

# Project to vocabulary
layer_logits = accumulated @ model.W_U  # [n_layers + 1, batch, pos, d_vocab]

# Look at predictions for final position
print("Layer-by-layer predictions for final token:")
for layer in range(model.cfg.n_layers + 1):
    probs = torch.softmax(layer_logits[layer, 0, -1], dim=-1)
    top_token = probs.argmax()
    top_prob = probs[top_token].item()
    print(f"Layer {layer}: {model.to_string(top_token.unsqueeze(0))!r} ({top_prob:.3f})")
```

---

## Tutorial 6: Steering with Activation Addition

### Goal
Add a steering vector to change model behavior.

### Step-by-Step

```python
from transformer_lens import HookedTransformer
import torch

model = HookedTransformer.from_pretrained("gpt2-small")

# Get activations for contrasting prompts
positive_prompt = "I love this! It's absolutely wonderful and"
negative_prompt = "I hate this! It's absolutely terrible and"

_, pos_cache = model.run_with_cache(model.to_tokens(positive_prompt))
_, neg_cache = model.run_with_cache(model.to_tokens(negative_prompt))

# Compute steering vector (positive - negative direction)
layer = 6
steering_vector = (
    pos_cache["resid_post", layer].mean(dim=1) -
    neg_cache["resid_post", layer].mean(dim=1)
)

# Generate with steering
test_prompt = "The movie was"
test_tokens = model.to_tokens(test_prompt)

def steer_hook(activation, hook):
    activation += 2.0 * steering_vector
    return activation

# Without steering
normal_output = model.generate(test_tokens, max_new_tokens=20)
print(f"Normal: {model.to_string(normal_output[0])}")

# With positive steering
steered_output = model.generate(
    test_tokens,
    max_new_tokens=20,
    fwd_hooks=[(f"blocks.{layer}.hook_resid_post", steer_hook)]
)
print(f"Steered: {model.to_string(steered_output[0])}")
```

---

## External Resources

### Official Tutorials
- [Main Demo](https://transformerlensorg.github.io/TransformerLens/generated/demos/Main_Demo.html)
- [Exploratory Analysis](https://transformerlensorg.github.io/TransformerLens/generated/demos/Exploratory_Analysis_Demo.html)
- [Activation Patching Demo](https://colab.research.google.com/github/TransformerLensOrg/TransformerLens/blob/main/demos/Activation_Patching_in_TL_Demo.ipynb)

### ARENA Course
Comprehensive 200+ hour curriculum: https://arena-foundation.github.io/ARENA/

### Neel Nanda's Resources
- [Getting Started in Mech Interp](https://www.neelnanda.io/mechanistic-interpretability/getting-started)
- [Mech Interp Glossary](https://www.neelnanda.io/mechanistic-interpretability/glossary)
- [YouTube Channel](https://www.youtube.com/@neelnanda)




---

## 🚀 Usage

**Reference this template:** `@skill-transformer-lens-interpretability.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
