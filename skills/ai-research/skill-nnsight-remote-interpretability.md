---
id: skill-nnsight-remote-interpretability
type: skill
name: nnsight-remote-interpretability
description: Provides guidance for interpreting and manipulating neural network internals
  using nnsight with optional NDIF remote execution. Use when needing to run interpretability
  experiments on massive models (70B+) without local GPU resources, or when working
  with any PyTorch architecture.
category: ai-research
complexity: medium
keywords:
- api
- github
- python
capabilities: []
token_estimate: 1934
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,934 -->
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




# nnsight-remote-interpretability

> Provides guidance for interpreting and manipulating neural network internals using nnsight with optional NDIF remote execution. Use when needing to run interpretability experiments on massive models (70B+) without local GPU resources, or when working with any PyTorch architecture.

# nnsight: Transparent Access to Neural Network Internals

nnsight (/ɛn.saɪt/) enables researchers to interpret and manipulate the internals of any PyTorch model, with the unique capability of running the same code locally on small models or remotely on massive models (70B+) via NDIF.

**GitHub**: [ndif-team/nnsight](https://github.com/ndif-team/nnsight) (730+ stars)
**Paper**: [NNsight and NDIF: Democratizing Access to Foundation Model Internals](https://arxiv.org/abs/2407.14561) (ICLR 2025)

## Key Value Proposition

**Write once, run anywhere**: The same interpretability code works on GPT-2 locally or Llama-3.1-405B remotely. Just toggle `remote=True`.

```python
# Local execution (small model)
with model.trace("Hello world"):
    hidden = model.transformer.h[5].output[0].save()

# Remote execution (massive model) - same code!
with model.trace("Hello world", remote=True):
    hidden = model.model.layers[40].output[0].save()
```

## When to Use nnsight

**Use nnsight when you need to:**
- Run interpretability experiments on models too large for local GPUs (70B, 405B)
- Work with any PyTorch architecture (transformers, Mamba, custom models)
- Perform multi-token generation interventions
- Share activations between different prompts
- Access full model internals without reimplementation

**Consider alternatives when:**
- You want consistent API across models → Use **TransformerLens**
- You need declarative, shareable interventions → Use **pyvene**
- You're training SAEs → Use **SAELens**
- You only work with small models locally → **TransformerLens** may be simpler

## Installation

```bash
# Basic installation
pip install nnsight

# For vLLM support
pip install "nnsight[vllm]"
```

For remote NDIF execution, sign up at [login.ndif.us](https://login.ndif.us) for an API key.

## Core Concepts

### LanguageModel Wrapper

```python
from nnsight import LanguageModel

# Load model (uses HuggingFace under the hood)
model = LanguageModel("openai-community/gpt2", device_map="auto")

# For larger models
model = LanguageModel("meta-llama/Llama-3.1-8B", device_map="auto")
```

### Tracing Context

The `trace` context manager enables deferred execution - operations are collected into a computation graph:

```python
from nnsight import LanguageModel

model = LanguageModel("gpt2", device_map="auto")

with model.trace("The Eiffel Tower is in") as tracer:
    # Access any module's output
    hidden_states = model.transformer.h[5].output[0].save()

    # Access attention patterns
    attn = model.transformer.h[5].attn.attn_dropout.input[0][0].save()

    # Modify activations
    model.transformer.h[8].output[0][:] = 0  # Zero out layer 8

    # Get final output
    logits = model.output.save()

# After context exits, access saved values
print(hidden_states.shape)  # [batch, seq, hidden]
```

### Proxy Objects

Inside `trace`, module accesses return Proxy objects that record operations:

```python
with model.trace("Hello"):
    # These are all Proxy objects - operations are deferred
    h5_out = model.transformer.h[5].output[0]  # Proxy
    h5_mean = h5_out.mean(dim=-1)              # Proxy
    h5_saved = h5_mean.save()                   # Save for later access
```

## Workflow 1: Activation Analysis

### Step-by-Step

```python
from nnsight import LanguageModel
import torch

model = LanguageModel("gpt2", device_map="auto")

prompt = "The capital of France is"

with model.trace(prompt) as tracer:
    # 1. Collect activations from multiple layers
    layer_outputs = []
    for i in range(12):  # GPT-2 has 12 layers
        layer_out = model.transformer.h[i].output[0].save()
        layer_outputs.append(layer_out)

    # 2. Get attention patterns
    attn_patterns = []
    for i in range(12):
        # Access attention weights (after softmax)
        attn = model.transformer.h[i].attn.attn_dropout.input[0][0].save()
        attn_patterns.append(attn)

    # 3. Get final logits
    logits = model.output.save()

# 4. Analyze outside context
for i, layer_out in enumerate(layer_outputs):
    print(f"Layer {i} output shape: {layer_out.shape}")
    print(f"Layer {i} norm: {layer_out.norm().item():.3f}")

# 5. Find top predictions
probs = torch.softmax(logits[0, -1], dim=-1)
top_tokens = probs.topk(5)
for token, prob in zip(top_tokens.indices, top_tokens.values):
    print(f"{model.tokenizer.decode(token)}: {prob.item():.3f}")
```

### Checklist
- [ ] Load model with LanguageModel wrapper
- [ ] Use trace context for operations
- [ ] Call `.save()` on values you need after context
- [ ] Access saved values outside context
- [ ] Use `.shape`, `.norm()`, etc. for analysis

## Workflow 2: Activation Patching

### Step-by-Step

```python
from nnsight import LanguageModel
import torch

model = LanguageModel("gpt2", device_map="auto")

clean_prompt = "The Eiffel Tower is in"
corrupted_prompt = "The Colosseum is in"

# 1. Get clean activations
with model.trace(clean_prompt) as tracer:
    clean_hidden = model.transformer.h[8].output[0].save()

# 2. Patch clean into corrupted run
with model.trace(corrupted_prompt) as tracer:
    # Replace layer 8 output with clean activations
    model.transformer.h[8].output[0][:] = clean_hidden

    patched_logits = model.output.save()

# 3. Compare predictions
paris_token = model.tokenizer.encode(" Paris")[0]
rome_token = model.tokenizer.encode(" Rome")[0]

patched_probs = torch.softmax(patched_logits[0, -1], dim=-1)
print(f"Paris prob: {patched_probs[paris_token].item():.3f}")
print(f"Rome prob: {patched_probs[rome_token].item():.3f}")
```

### Systematic Patching Sweep

```python
def patch_layer_position(layer, position, clean_cache, corrupted_prompt):
    """Patch single layer/position from clean to corrupted."""
    with model.trace(corrupted_prompt) as tracer:
        # Get current activation
        current = model.transformer.h[layer].output[0]

        # Patch only specific position
        current[:, position, :] = clean_cache[layer][:, position, :]

        logits = model.output.save()

    return logits

# Sweep over all layers and positions
results = torch.zeros(12, seq_len)
for layer in range(12):
    for pos in range(seq_len):
        logits = patch_layer_position(layer, pos, clean_hidden, corrupted)
        results[layer, pos] = compute_metric(logits)
```

## Workflow 3: Remote Execution with NDIF

Run the same experiments on massive models without local GPUs.

### Step-by-Step

```python
from nnsight import LanguageModel

# 1. Load large model (will run remotely)
model = LanguageModel("meta-llama/Llama-3.1-70B")

# 2. Same code, just add remote=True
with model.trace("The meaning of life is", remote=True) as tracer:
    # Access internals of 70B model!
    layer_40_out = model.model.layers[40].output[0].save()
    logits = model.output.save()

# 3. Results returned from NDIF
print(f"Layer 40 shape: {layer_40_out.shape}")

# 4. Generation with interventions
with model.trace(remote=True) as tracer:
    with tracer.invoke("What is 2+2?"):
        # Intervene during generation
        model.model.layers[20].output[0][:, -1, :] *= 1.5

    output = model.generate(max_new_tokens=50)
```

### NDIF Setup

1. Sign up at [login.ndif.us](https://login.ndif.us)
2. Get API key
3. Set environment variable or pass to nnsight:

```python
import os
os.environ["NDIF_API_KEY"] = "your_key"

# Or configure directly
from nnsight import CONFIG
CONFIG.API_KEY = "your_key"
```

### Available Models on NDIF

- Llama-3.1-8B, 70B, 405B
- DeepSeek-R1 models
- Various open-weight models (check [ndif.us](https://ndif.us) for current list)

## Workflow 4: Cross-Prompt Activation Sharing

Share activations between different inputs in a single trace.

```python
from nnsight import LanguageModel

model = LanguageModel("gpt2", device_map="auto")

with model.trace() as tracer:
    # First prompt
    with tracer.invoke("The cat sat on the"):
        cat_hidden = model.transformer.h[6].output[0].save()

    # Second prompt - inject cat's activations
    with tracer.invoke("The dog ran through the"):
        # Replace with cat's activations at layer 6
        model.transformer.h[6].output[0][:] = cat_hidden
        dog_with_cat = model.output.save()

# The dog prompt now has cat's internal representations
```

## Workflow 5: Gradient-Based Analysis

Access gradients during backward pass.

```python
from nnsight import LanguageModel
import torch

model = LanguageModel("gpt2", device_map="auto")

with model.trace("The quick brown fox") as tracer:
    # Save activations and enable gradient
    hidden = model.transformer.h[5].output[0].save()
    hidden.retain_grad()

    logits = model.output

    # Compute loss on specific token
    target_token = model.tokenizer.encode(" jumps")[0]
    loss = -logits[0, -1, target_token]

    # Backward pass
    loss.backward()

# Access gradients
grad = hidden.grad
print(f"Gradient shape: {grad.shape}")
print(f"Gradient norm: {grad.norm().item():.3f}")
```

**Note**: Gradient access not supported for vLLM or remote execution.

## Common Issues & Solutions

### Issue: Module path differs between models
```python
# GPT-2 structure
model.transformer.h[5].output[0]

# LLaMA structure
model.model.layers[5].output[0]

# Solution: Check model structure
print(model._model)  # See actual module names
```

### Issue: Forgetting to save
```python
# WRONG: Value not accessible outside trace
with model.trace("Hello"):
    hidden = model.transformer.h[5].output[0]  # Not saved!

print(hidden)  # Error or wrong value

# RIGHT: Call .save()
with model.trace("Hello"):
    hidden = model.transformer.h[5].output[0].save()

print(hidden)  # Works!
```

### Issue: Remote timeout
```python
# For long operations, increase timeout
with model.trace("prompt", remote=True, timeout=300) as tracer:
    # Long operation...
```

### Issue: Memory with many saved activations
```python
# Only save what you need
with model.trace("prompt"):
    # Don't save everything
    for i in range(100):
        model.transformer.h[i].output[0].save()  # Memory heavy!

    # Better: save specific layers
    key_layers = [0, 5, 11]
    for i in key_layers:
        model.transformer.h[i].output[0].save()
```

### Issue: vLLM gradient limitation
```python
# vLLM doesn't support gradients
# Use standard execution for gradient analysis
model = LanguageModel("gpt2", device_map="auto")  # Not vLLM
```

## Key API Reference

| Method/Property | Purpose |
|-----------------|---------|
| `model.trace(prompt, remote=False)` | Start tracing context |
| `proxy.save()` | Save value for access after trace |
| `proxy[:]` | Slice/index proxy (assignment patches) |
| `tracer.invoke(prompt)` | Add prompt within trace |
| `model.generate(...)` | Generate with interventions |
| `model.output` | Final model output logits |
| `model._model` | Underlying HuggingFace model |

## Comparison with Other Tools

| Feature | nnsight | TransformerLens | pyvene |
|---------|---------|-----------------|--------|
| Any architecture | Yes | Transformers only | Yes |
| Remote execution | Yes (NDIF) | No | No |
| Consistent API | No | Yes | Yes |
| Deferred execution | Yes | No | No |
| HuggingFace native | Yes | Reimplemented | Yes |
| Shareable configs | No | No | Yes |

## Reference Documentation

For detailed API documentation, tutorials, and advanced usage, see the `references/` folder:

| File | Contents |
|------|----------|
| [references/README.md](references/README.md) | Overview and quick start guide |
| [references/api.md](references/api.md) | Complete API reference for LanguageModel, tracing, proxy objects |
| [references/tutorials.md](references/tutorials.md) | Step-by-step tutorials for local and remote interpretability |

## External Resources

### Tutorials
- [Getting Started](https://nnsight.net/start/)
- [Features Overview](https://nnsight.net/features/)
- [Remote Execution](https://nnsight.net/notebooks/features/remote_execution/)
- [Applied Tutorials](https://nnsight.net/applied_tutorials/)

### Official Documentation
- [Official Docs](https://nnsight.net/documentation/)
- [NDIF Info](https://ndif.us/)
- [Community Forum](https://discuss.ndif.us/)

### Papers
- [NNsight and NDIF Paper](https://arxiv.org/abs/2407.14561) - Fiotto-Kaufman et al. (ICLR 2025)

## Architecture Support

nnsight works with any PyTorch model:
- **Transformers**: GPT-2, LLaMA, Mistral, etc.
- **State Space Models**: Mamba
- **Vision Models**: ViT, CLIP
- **Custom architectures**: Any nn.Module

The key is knowing the module structure to access the right components.


---


## 📚 Reference Materials


### Readme

# nnsight Reference Documentation

This directory contains comprehensive reference materials for nnsight.

## Contents

- [api.md](api.md) - Complete API reference for LanguageModel, tracing, and proxy objects
- [tutorials.md](tutorials.md) - Step-by-step tutorials for local and remote interpretability

## Quick Links

- **Official Documentation**: https://nnsight.net/
- **GitHub Repository**: https://github.com/ndif-team/nnsight
- **NDIF (Remote Execution)**: https://ndif.us/
- **Community Forum**: https://discuss.ndif.us/
- **Paper**: https://arxiv.org/abs/2407.14561 (ICLR 2025)

## Installation

```bash
# Basic installation
pip install nnsight

# For vLLM support
pip install "nnsight[vllm]"
```

## Basic Usage

```python
from nnsight import LanguageModel

# Load model
model = LanguageModel("openai-community/gpt2", device_map="auto")

# Trace and access internals
with model.trace("The Eiffel Tower is in") as tracer:
    # Access layer output
    hidden = model.transformer.h[5].output[0].save()

    # Modify activations
    model.transformer.h[8].output[0][:] *= 0.5

    # Get final output
    logits = model.output.save()

# Access saved values outside context
print(hidden.shape)
```

## Key Concepts

### Tracing
The `trace()` context enables deferred execution - operations are recorded and executed together.

### Proxy Objects
Inside trace, module accesses return Proxies. Call `.save()` to retrieve values after execution.

### Remote Execution (NDIF)
Run the same code on massive models (70B+) without local GPUs:

```python
# Same code, just add remote=True
with model.trace("Hello", remote=True):
    hidden = model.model.layers[40].output[0].save()
```

## NDIF Setup

1. Sign up at https://login.ndif.us/
2. Get API key
3. Set environment variable: `export NDIF_API_KEY=your_key`

## Available Remote Models

- Llama-3.1-8B, 70B, 405B
- DeepSeek-R1 models
- More at https://ndif.us/




### Api

# nnsight API Reference

## LanguageModel

Main class for wrapping language models with intervention capabilities.

### Loading Models

```python
from nnsight import LanguageModel

# Basic loading
model = LanguageModel("openai-community/gpt2", device_map="auto")

# Larger models
model = LanguageModel("meta-llama/Llama-3.1-8B", device_map="auto")

# With custom tokenizer settings
model = LanguageModel(
    "gpt2",
    device_map="auto",
    torch_dtype=torch.float16,
)
```

### Model Attributes

```python
# Access underlying HuggingFace model
model._model

# Access tokenizer
model.tokenizer

# Model config
model._model.config
```

---

## Tracing Context

The `trace()` method creates a context for deferred execution.

### Basic Tracing

```python
with model.trace("Hello world") as tracer:
    # Operations are recorded, not executed immediately
    hidden = model.transformer.h[5].output[0].save()
    logits = model.output.save()

# After context, operations execute and saved values are available
print(hidden.shape)
```

### Tracing Parameters

```python
with model.trace(
    prompt,                    # Input text or tokens
    remote=False,              # Use NDIF remote execution
    validate=True,             # Validate tensor shapes
    scan=True,                 # Scan for shape info
) as tracer:
    ...
```

### Remote Execution

```python
# Same code works remotely
with model.trace("Hello", remote=True) as tracer:
    hidden = model.transformer.h[5].output[0].save()
```

---

## Proxy Objects

Inside tracing context, accessing modules returns Proxy objects.

### Accessing Values

```python
with model.trace("Hello") as tracer:
    # These are Proxy objects
    layer_output = model.transformer.h[5].output[0]
    attention = model.transformer.h[5].attn.output

    # Operations create new Proxies
    mean = layer_output.mean(dim=-1)
    normed = layer_output / layer_output.norm()
```

### Saving Values

```python
with model.trace("Hello") as tracer:
    # Must call .save() to access after context
    hidden = model.transformer.h[5].output[0].save()

# Now hidden contains actual tensor
print(hidden.shape)
```

### Modifying Values

```python
with model.trace("Hello") as tracer:
    # In-place modification
    model.transformer.h[5].output[0][:] = 0

    # Replace with computed value
    model.transformer.h[5].output[0][:] = some_tensor

    # Arithmetic modification
    model.transformer.h[5].output[0][:] *= 0.5
    model.transformer.h[5].output[0][:] += steering_vector
```

### Proxy Operations

```python
with model.trace("Hello") as tracer:
    h = model.transformer.h[5].output[0]

    # Indexing
    first_token = h[:, 0, :]
    last_token = h[:, -1, :]

    # PyTorch operations
    mean = h.mean(dim=-1)
    norm = h.norm()
    transposed = h.transpose(1, 2)

    # Save results
    mean.save()
```

---

## Module Access Patterns

### GPT-2 Structure

```python
with model.trace("Hello") as tracer:
    # Embeddings
    embed = model.transformer.wte.output.save()
    pos_embed = model.transformer.wpe.output.save()

    # Layer outputs
    layer_out = model.transformer.h[5].output[0].save()

    # Attention
    attn_out = model.transformer.h[5].attn.output.save()

    # MLP
    mlp_out = model.transformer.h[5].mlp.output.save()

    # Final output
    logits = model.output.save()
```

### LLaMA Structure

```python
with model.trace("Hello") as tracer:
    # Embeddings
    embed = model.model.embed_tokens.output.save()

    # Layer outputs
    layer_out = model.model.layers[10].output[0].save()

    # Attention
    attn_out = model.model.layers[10].self_attn.output.save()

    # MLP
    mlp_out = model.model.layers[10].mlp.output.save()

    # Final output
    logits = model.output.save()
```

### Finding Module Names

```python
# Print model structure
print(model._model)

# Or iterate
for name, module in model._model.named_modules():
    print(name)
```

---

## Multiple Prompts (invoke)

Process multiple prompts in a single trace.

### Basic Usage

```python
with model.trace() as tracer:
    with tracer.invoke("First prompt"):
        hidden1 = model.transformer.h[5].output[0].save()

    with tracer.invoke("Second prompt"):
        hidden2 = model.transformer.h[5].output[0].save()
```

### Cross-Prompt Intervention

```python
with model.trace() as tracer:
    # Get activations from first prompt
    with tracer.invoke("The cat sat on the"):
        cat_hidden = model.transformer.h[6].output[0].save()

    # Inject into second prompt
    with tracer.invoke("The dog ran through the"):
        model.transformer.h[6].output[0][:] = cat_hidden
        output = model.output.save()
```

---

## Generation

Generate text with interventions.

### Basic Generation

```python
with model.trace() as tracer:
    with tracer.invoke("Once upon a time"):
        # Intervention during generation
        model.transformer.h[5].output[0][:] *= 1.2

    output = model.generate(max_new_tokens=50)

print(model.tokenizer.decode(output[0]))
```

---

## Gradients

Access gradients for analysis (not supported with remote/vLLM).

```python
with model.trace("The quick brown fox") as tracer:
    hidden = model.transformer.h[5].output[0].save()
    hidden.retain_grad()

    logits = model.output
    target_token = model.tokenizer.encode(" jumps")[0]
    loss = -logits[0, -1, target_token]
    loss.backward()

# Access gradient
grad = hidden.grad
```

---

## NDIF Remote Execution

### Setup

```python
import os
os.environ["NDIF_API_KEY"] = "your_key"

# Or configure directly
from nnsight import CONFIG
CONFIG.set_default_api_key("your_key")
```

### Using Remote

```python
model = LanguageModel("meta-llama/Llama-3.1-70B")

with model.trace("Hello", remote=True) as tracer:
    hidden = model.model.layers[40].output[0].save()
    logits = model.output.save()

# Results returned from NDIF
print(hidden.shape)
```

### Sessions (Batching Requests)

```python
with model.session(remote=True) as session:
    with model.trace("First prompt"):
        h1 = model.model.layers[20].output[0].save()

    with model.trace("Second prompt"):
        h2 = model.model.layers[20].output[0].save()

# Both run in single NDIF request
```

---

## Utility Methods

### Early Stopping

```python
with model.trace("Hello") as tracer:
    hidden = model.transformer.h[5].output[0].save()
    tracer.stop()  # Don't run remaining layers
```

### Validation

```python
# Validate shapes before execution
with model.trace("Hello", validate=True) as tracer:
    hidden = model.transformer.h[5].output[0].save()
```

### Module Access Result

```python
with model.trace("Hello") as tracer:
    # Access result of a method call
    result = tracer.result
```

---

## Common Module Paths

| Model | Embeddings | Layers | Attention | MLP |
|-------|------------|--------|-----------|-----|
| GPT-2 | `transformer.wte` | `transformer.h[i]` | `transformer.h[i].attn` | `transformer.h[i].mlp` |
| LLaMA | `model.embed_tokens` | `model.layers[i]` | `model.layers[i].self_attn` | `model.layers[i].mlp` |
| Mistral | `model.embed_tokens` | `model.layers[i]` | `model.layers[i].self_attn` | `model.layers[i].mlp` |




### Tutorials

# nnsight Tutorials

## Tutorial 1: Basic Activation Analysis

### Goal
Load a model, access internal activations, and analyze them.

### Step-by-Step

```python
from nnsight import LanguageModel
import torch

# 1. Load model
model = LanguageModel("openai-community/gpt2", device_map="auto")

# 2. Trace and collect activations
prompt = "The capital of France is"

with model.trace(prompt) as tracer:
    # Collect from multiple layers
    activations = {}
    for i in range(12):  # GPT-2 has 12 layers
        activations[i] = model.transformer.h[i].output[0].save()

    # Get final logits
    logits = model.output.save()

# 3. Analyze (outside context)
print("Layer-wise activation norms:")
for layer, act in activations.items():
    print(f"  Layer {layer}: {act.norm().item():.2f}")

# 4. Check predictions
probs = torch.softmax(logits[0, -1], dim=-1)
top_tokens = probs.topk(5)
print("\nTop predictions:")
for token_id, prob in zip(top_tokens.indices, top_tokens.values):
    token_str = model.tokenizer.decode(token_id)
    print(f"  {token_str!r}: {prob.item():.3f}")
```

---

## Tutorial 2: Activation Patching

### Goal
Patch activations from one prompt into another to test causal relationships.

### Step-by-Step

```python
from nnsight import LanguageModel
import torch

model = LanguageModel("gpt2", device_map="auto")

clean_prompt = "The Eiffel Tower is in the city of"
corrupted_prompt = "The Colosseum is in the city of"

# 1. Get clean activations
with model.trace(clean_prompt) as tracer:
    clean_hidden = model.transformer.h[8].output[0].save()
    clean_logits = model.output.save()

# 2. Define metric
paris_token = model.tokenizer.encode(" Paris")[0]
rome_token = model.tokenizer.encode(" Rome")[0]

def logit_diff(logits):
    return (logits[0, -1, paris_token] - logits[0, -1, rome_token]).item()

print(f"Clean logit diff: {logit_diff(clean_logits):.3f}")

# 3. Patch clean into corrupted
with model.trace(corrupted_prompt) as tracer:
    # Replace layer 8 output with clean activations
    model.transformer.h[8].output[0][:] = clean_hidden
    patched_logits = model.output.save()

print(f"Patched logit diff: {logit_diff(patched_logits):.3f}")

# 4. Systematic patching sweep
results = torch.zeros(12)  # 12 layers

for layer in range(12):
    # Get clean activation for this layer
    with model.trace(clean_prompt) as tracer:
        clean_act = model.transformer.h[layer].output[0].save()

    # Patch into corrupted
    with model.trace(corrupted_prompt) as tracer:
        model.transformer.h[layer].output[0][:] = clean_act
        logits = model.output.save()

    results[layer] = logit_diff(logits)
    print(f"Layer {layer}: {results[layer]:.3f}")

print(f"\nMost important layer: {results.argmax().item()}")
```

---

## Tutorial 3: Cross-Prompt Activation Sharing

### Goal
Transfer activations between different prompts in a single trace.

### Step-by-Step

```python
from nnsight import LanguageModel

model = LanguageModel("gpt2", device_map="auto")

with model.trace() as tracer:
    # First prompt - get "cat" representations
    with tracer.invoke("The cat sat on the mat"):
        cat_hidden = model.transformer.h[6].output[0].save()

    # Second prompt - inject "cat" into "dog"
    with tracer.invoke("The dog ran through the park"):
        # Replace with cat's activations
        model.transformer.h[6].output[0][:] = cat_hidden
        modified_logits = model.output.save()

# The dog prompt now has cat's internal representations
print(f"Modified logits shape: {modified_logits.shape}")
```

---

## Tutorial 4: Remote Execution with NDIF

### Goal
Run the same interpretability code on massive models (70B+).

### Step-by-Step

```python
from nnsight import LanguageModel
import os

# 1. Setup API key
os.environ["NDIF_API_KEY"] = "your_key_here"

# 2. Load large model (runs remotely)
model = LanguageModel("meta-llama/Llama-3.1-70B")

# 3. Same code, just remote=True
prompt = "The meaning of life is"

with model.trace(prompt, remote=True) as tracer:
    # Access layer 40 of 70B model!
    hidden = model.model.layers[40].output[0].save()
    logits = model.output.save()

# 4. Results returned from NDIF
print(f"Hidden shape: {hidden.shape}")
print(f"Logits shape: {logits.shape}")

# 5. Check predictions
import torch
probs = torch.softmax(logits[0, -1], dim=-1)
top_tokens = probs.topk(5)
print("\nTop predictions from Llama-70B:")
for token_id, prob in zip(top_tokens.indices, top_tokens.values):
    print(f"  {model.tokenizer.decode(token_id)!r}: {prob.item():.3f}")
```

### Batching with Sessions

```python
# Run multiple experiments in one NDIF request
with model.session(remote=True) as session:
    with model.trace("What is 2+2?"):
        math_hidden = model.model.layers[30].output[0].save()

    with model.trace("The capital of France is"):
        fact_hidden = model.model.layers[30].output[0].save()

# Compare representations
similarity = torch.cosine_similarity(
    math_hidden.mean(dim=1),
    fact_hidden.mean(dim=1),
    dim=-1
)
print(f"Similarity: {similarity.item():.3f}")
```

---

## Tutorial 5: Steering with Activation Addition

### Goal
Add a steering vector to change model behavior.

### Step-by-Step

```python
from nnsight import LanguageModel
import torch

model = LanguageModel("gpt2", device_map="auto")

# 1. Get contrasting activations
with model.trace("I love this movie, it's wonderful") as tracer:
    positive_hidden = model.transformer.h[6].output[0].save()

with model.trace("I hate this movie, it's terrible") as tracer:
    negative_hidden = model.transformer.h[6].output[0].save()

# 2. Compute steering direction
steering_vector = positive_hidden.mean(dim=1) - negative_hidden.mean(dim=1)

# 3. Generate without steering
test_prompt = "This restaurant is"
with model.trace(test_prompt) as tracer:
    normal_logits = model.output.save()

# 4. Generate with steering
with model.trace(test_prompt) as tracer:
    # Add steering at layer 6
    model.transformer.h[6].output[0][:] += 3.0 * steering_vector
    steered_logits = model.output.save()

# 5. Compare predictions
def top_prediction(logits):
    token = logits[0, -1].argmax()
    return model.tokenizer.decode(token)

print(f"Normal: {top_prediction(normal_logits)}")
print(f"Steered (positive): {top_prediction(steered_logits)}")
```

---

## Tutorial 6: Logit Lens

### Goal
See what the model "believes" at each layer.

### Step-by-Step

```python
from nnsight import LanguageModel
import torch

model = LanguageModel("gpt2", device_map="auto")

prompt = "The quick brown fox jumps over the lazy"

with model.trace(prompt) as tracer:
    # Collect residual stream at each layer
    residuals = []
    for i in range(12):
        resid = model.transformer.h[i].output[0].save()
        residuals.append(resid)

# Access model's unembedding and final layernorm
W_U = model._model.lm_head.weight.T  # [d_model, vocab]
ln_f = model._model.transformer.ln_f

print("Layer-by-layer predictions for final token:")
for i, resid in enumerate(residuals):
    # Apply final layernorm
    normed = ln_f(resid)

    # Project to vocabulary
    layer_logits = normed @ W_U

    # Get prediction
    probs = torch.softmax(layer_logits[0, -1], dim=-1)
    top_token = probs.argmax()
    top_prob = probs[top_token].item()

    print(f"Layer {i}: {model.tokenizer.decode(top_token)!r} ({top_prob:.3f})")
```

---

## External Resources

### Official Resources
- [Getting Started](https://nnsight.net/start/)
- [Features Overview](https://nnsight.net/features/)
- [Documentation](https://nnsight.net/documentation/)
- [Tutorials](https://nnsight.net/tutorials/)

### NDIF Resources
- [NDIF Homepage](https://ndif.us/)
- [Available Models](https://ndif.us/models)
- [API Key Signup](https://login.ndif.us/)

### Paper
- [NNsight and NDIF](https://arxiv.org/abs/2407.14561) - ICLR 2025

### Community
- [Discussion Forum](https://discuss.ndif.us/)
- [GitHub Issues](https://github.com/ndif-team/nnsight/issues)




---

## 🚀 Usage

**Reference this template:** `@skill-nnsight-remote-interpretability.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
