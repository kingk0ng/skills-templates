---
id: skill-pyvene-interventions
type: skill
name: pyvene-interventions
description: Provides guidance for performing causal interventions on PyTorch models
  using pyvene's declarative intervention framework. Use when conducting causal tracing,
  activation patching, interchange intervention training, or testing causal hypotheses
  about model behavior.
category: ai-research
complexity: medium
keywords:
- api
- github
- python
- test
capabilities: []
token_estimate: 1838
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,838 -->
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




# pyvene-interventions

> Provides guidance for performing causal interventions on PyTorch models using pyvene's declarative intervention framework. Use when conducting causal tracing, activation patching, interchange intervention training, or testing causal hypotheses about model behavior.

# pyvene: Causal Interventions for Neural Networks

pyvene is Stanford NLP's library for performing causal interventions on PyTorch models. It provides a declarative, dict-based framework for activation patching, causal tracing, and interchange intervention training - making intervention experiments reproducible and shareable.

**GitHub**: [stanfordnlp/pyvene](https://github.com/stanfordnlp/pyvene) (840+ stars)
**Paper**: [pyvene: A Library for Understanding and Improving PyTorch Models via Interventions](https://aclanthology.org/2024.naacl-demo.16) (NAACL 2024)

## When to Use pyvene

**Use pyvene when you need to:**
- Perform causal tracing (ROME-style localization)
- Run activation patching experiments
- Conduct interchange intervention training (IIT)
- Test causal hypotheses about model components
- Share/reproduce intervention experiments via HuggingFace
- Work with any PyTorch architecture (not just transformers)

**Consider alternatives when:**
- You need exploratory activation analysis → Use **TransformerLens**
- You want to train/analyze SAEs → Use **SAELens**
- You need remote execution on massive models → Use **nnsight**
- You want lower-level control → Use **nnsight**

## Installation

```bash
pip install pyvene
```

Standard import:
```python
import pyvene as pv
```

## Core Concepts

### IntervenableModel

The main class that wraps any PyTorch model with intervention capabilities:

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load base model
model = AutoModelForCausalLM.from_pretrained("gpt2")
tokenizer = AutoTokenizer.from_pretrained("gpt2")

# Define intervention configuration
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=8,
            component="block_output",
            intervention_type=pv.VanillaIntervention,
        )
    ]
)

# Create intervenable model
intervenable = pv.IntervenableModel(config, model)
```

### Intervention Types

| Type | Description | Use Case |
|------|-------------|----------|
| `VanillaIntervention` | Swap activations between runs | Activation patching |
| `AdditionIntervention` | Add activations to base run | Steering, ablation |
| `SubtractionIntervention` | Subtract activations | Ablation |
| `ZeroIntervention` | Zero out activations | Component knockout |
| `RotatedSpaceIntervention` | DAS trainable intervention | Causal discovery |
| `CollectIntervention` | Collect activations | Probing, analysis |

### Component Targets

```python
# Available components to intervene on
components = [
    "block_input",      # Input to transformer block
    "block_output",     # Output of transformer block
    "mlp_input",        # Input to MLP
    "mlp_output",       # Output of MLP
    "mlp_activation",   # MLP hidden activations
    "attention_input",  # Input to attention
    "attention_output", # Output of attention
    "attention_value_output",  # Attention value vectors
    "query_output",     # Query vectors
    "key_output",       # Key vectors
    "value_output",     # Value vectors
    "head_attention_value_output",  # Per-head values
]
```

## Workflow 1: Causal Tracing (ROME-style)

Locate where factual associations are stored by corrupting inputs and restoring activations.

### Step-by-Step

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch

model = AutoModelForCausalLM.from_pretrained("gpt2-xl")
tokenizer = AutoTokenizer.from_pretrained("gpt2-xl")

# 1. Define clean and corrupted inputs
clean_prompt = "The Space Needle is in downtown"
corrupted_prompt = "The ##### ###### ## ## ########"  # Noise

clean_tokens = tokenizer(clean_prompt, return_tensors="pt")
corrupted_tokens = tokenizer(corrupted_prompt, return_tensors="pt")

# 2. Get clean activations (source)
with torch.no_grad():
    clean_outputs = model(**clean_tokens, output_hidden_states=True)
    clean_states = clean_outputs.hidden_states

# 3. Define restoration intervention
def run_causal_trace(layer, position):
    """Restore clean activation at specific layer and position."""
    config = pv.IntervenableConfig(
        representations=[
            pv.RepresentationConfig(
                layer=layer,
                component="block_output",
                intervention_type=pv.VanillaIntervention,
                unit="pos",
                max_number_of_units=1,
            )
        ]
    )

    intervenable = pv.IntervenableModel(config, model)

    # Run with intervention
    _, patched_outputs = intervenable(
        base=corrupted_tokens,
        sources=[clean_tokens],
        unit_locations={"sources->base": ([[[position]]], [[[position]]])},
        output_original_output=True,
    )

    # Return probability of correct token
    probs = torch.softmax(patched_outputs.logits[0, -1], dim=-1)
    seattle_token = tokenizer.encode(" Seattle")[0]
    return probs[seattle_token].item()

# 4. Sweep over layers and positions
n_layers = model.config.n_layer
seq_len = clean_tokens["input_ids"].shape[1]

results = torch.zeros(n_layers, seq_len)
for layer in range(n_layers):
    for pos in range(seq_len):
        results[layer, pos] = run_causal_trace(layer, pos)

# 5. Visualize (layer x position heatmap)
# High values indicate causal importance
```

### Checklist
- [ ] Prepare clean prompt with target factual association
- [ ] Create corrupted version (noise or counterfactual)
- [ ] Define intervention config for each (layer, position)
- [ ] Run patching sweep
- [ ] Identify causal hotspots in heatmap

## Workflow 2: Activation Patching for Circuit Analysis

Test which components are necessary for a specific behavior.

### Step-by-Step

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch

model = AutoModelForCausalLM.from_pretrained("gpt2")
tokenizer = AutoTokenizer.from_pretrained("gpt2")

# IOI task setup
clean_prompt = "When John and Mary went to the store, Mary gave a bottle to"
corrupted_prompt = "When John and Mary went to the store, John gave a bottle to"

clean_tokens = tokenizer(clean_prompt, return_tensors="pt")
corrupted_tokens = tokenizer(corrupted_prompt, return_tensors="pt")

john_token = tokenizer.encode(" John")[0]
mary_token = tokenizer.encode(" Mary")[0]

def logit_diff(logits):
    """IO - S logit difference."""
    return logits[0, -1, john_token] - logits[0, -1, mary_token]

# Patch attention output at each layer
def patch_attention(layer):
    config = pv.IntervenableConfig(
        representations=[
            pv.RepresentationConfig(
                layer=layer,
                component="attention_output",
                intervention_type=pv.VanillaIntervention,
            )
        ]
    )

    intervenable = pv.IntervenableModel(config, model)

    _, patched_outputs = intervenable(
        base=corrupted_tokens,
        sources=[clean_tokens],
    )

    return logit_diff(patched_outputs.logits).item()

# Find which layers matter
results = []
for layer in range(model.config.n_layer):
    diff = patch_attention(layer)
    results.append(diff)
    print(f"Layer {layer}: logit diff = {diff:.3f}")
```

## Workflow 3: Interchange Intervention Training (IIT)

Train interventions to discover causal structure.

### Step-by-Step

```python
import pyvene as pv
from transformers import AutoModelForCausalLM
import torch

model = AutoModelForCausalLM.from_pretrained("gpt2")

# 1. Define trainable intervention
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=6,
            component="block_output",
            intervention_type=pv.RotatedSpaceIntervention,  # Trainable
            low_rank_dimension=64,  # Learn 64-dim subspace
        )
    ]
)

intervenable = pv.IntervenableModel(config, model)

# 2. Set up training
optimizer = torch.optim.Adam(
    intervenable.get_trainable_parameters(),
    lr=1e-4
)

# 3. Training loop (simplified)
for base_input, source_input, target_output in dataloader:
    optimizer.zero_grad()

    _, outputs = intervenable(
        base=base_input,
        sources=[source_input],
    )

    loss = criterion(outputs.logits, target_output)
    loss.backward()
    optimizer.step()

# 4. Analyze learned intervention
# The rotation matrix reveals causal subspace
rotation = intervenable.interventions["layer.6.block_output"][0].rotate_layer
```

### DAS (Distributed Alignment Search)

```python
# Low-rank rotation finds interpretable subspaces
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=8,
            component="block_output",
            intervention_type=pv.LowRankRotatedSpaceIntervention,
            low_rank_dimension=1,  # Find 1D causal direction
        )
    ]
)
```

## Workflow 4: Model Steering (Honest LLaMA)

Steer model behavior during generation.

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-2-7b-hf")
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-7b-hf")

# Load pre-trained steering intervention
intervenable = pv.IntervenableModel.load(
    "zhengxuanzenwu/intervenable_honest_llama2_chat_7B",
    model=model,
)

# Generate with steering
prompt = "Is the earth flat?"
inputs = tokenizer(prompt, return_tensors="pt")

# Intervention applied during generation
outputs = intervenable.generate(
    inputs,
    max_new_tokens=100,
    do_sample=False,
)

print(tokenizer.decode(outputs[0]))
```

## Saving and Sharing Interventions

```python
# Save locally
intervenable.save("./my_intervention")

# Load from local
intervenable = pv.IntervenableModel.load(
    "./my_intervention",
    model=model,
)

# Share on HuggingFace
intervenable.save_intervention("username/my-intervention")

# Load from HuggingFace
intervenable = pv.IntervenableModel.load(
    "username/my-intervention",
    model=model,
)
```

## Common Issues & Solutions

### Issue: Wrong intervention location
```python
# WRONG: Incorrect component name
config = pv.RepresentationConfig(
    component="mlp",  # Not valid!
)

# RIGHT: Use exact component name
config = pv.RepresentationConfig(
    component="mlp_output",  # Valid
)
```

### Issue: Dimension mismatch
```python
# Ensure source and base have compatible shapes
# For position-specific interventions:
config = pv.RepresentationConfig(
    unit="pos",
    max_number_of_units=1,  # Intervene on single position
)

# Specify locations explicitly
intervenable(
    base=base_tokens,
    sources=[source_tokens],
    unit_locations={"sources->base": ([[[5]]], [[[5]]])},  # Position 5
)
```

### Issue: Memory with large models
```python
# Use gradient checkpointing
model.gradient_checkpointing_enable()

# Or intervene on fewer components
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=8,  # Single layer instead of all
            component="block_output",
        )
    ]
)
```

### Issue: LoRA integration
```python
# pyvene v0.1.8+ supports LoRAs as interventions
config = pv.RepresentationConfig(
    intervention_type=pv.LoRAIntervention,
    low_rank_dimension=16,
)
```

## Key Classes Reference

| Class | Purpose |
|-------|---------|
| `IntervenableModel` | Main wrapper for interventions |
| `IntervenableConfig` | Configuration container |
| `RepresentationConfig` | Single intervention specification |
| `VanillaIntervention` | Activation swapping |
| `RotatedSpaceIntervention` | Trainable DAS intervention |
| `CollectIntervention` | Activation collection |

## Supported Models

pyvene works with any PyTorch model. Tested on:
- GPT-2 (all sizes)
- LLaMA / LLaMA-2
- Pythia
- Mistral / Mixtral
- OPT
- BLIP (vision-language)
- ESM (protein models)
- Mamba (state space)

## Reference Documentation

For detailed API documentation, tutorials, and advanced usage, see the `references/` folder:

| File | Contents |
|------|----------|
| [references/README.md](references/README.md) | Overview and quick start guide |
| [references/api.md](references/api.md) | Complete API reference for IntervenableModel, intervention types, configurations |
| [references/tutorials.md](references/tutorials.md) | Step-by-step tutorials for causal tracing, activation patching, DAS |

## External Resources

### Tutorials
- [pyvene 101](https://stanfordnlp.github.io/pyvene/tutorials/pyvene_101.html)
- [Causal Tracing Tutorial](https://stanfordnlp.github.io/pyvene/tutorials/advanced_tutorials/Causal_Tracing.html)
- [IOI Circuit Replication](https://stanfordnlp.github.io/pyvene/tutorials/advanced_tutorials/IOI_Replication.html)
- [DAS Introduction](https://stanfordnlp.github.io/pyvene/tutorials/advanced_tutorials/DAS_Main_Introduction.html)

### Papers
- [Locating and Editing Factual Associations in GPT](https://arxiv.org/abs/2202.05262) - Meng et al. (2022)
- [Inference-Time Intervention](https://arxiv.org/abs/2306.03341) - Li et al. (2023)
- [Interpretability in the Wild](https://arxiv.org/abs/2211.00593) - Wang et al. (2022)

### Official Documentation
- [Official Docs](https://stanfordnlp.github.io/pyvene/)
- [API Reference](https://stanfordnlp.github.io/pyvene/api/)

## Comparison with Other Tools

| Feature | pyvene | TransformerLens | nnsight |
|---------|--------|-----------------|---------|
| Declarative config | Yes | No | No |
| HuggingFace sharing | Yes | No | No |
| Trainable interventions | Yes | Limited | Yes |
| Any PyTorch model | Yes | Transformers only | Yes |
| Remote execution | No | No | Yes (NDIF) |


---


## 📚 Reference Materials


### Readme

# pyvene Reference Documentation

This directory contains comprehensive reference materials for pyvene.

## Contents

- [api.md](api.md) - Complete API reference for IntervenableModel, intervention types, and configurations
- [tutorials.md](tutorials.md) - Step-by-step tutorials for causal tracing, activation patching, and trainable interventions

## Quick Links

- **Official Documentation**: https://stanfordnlp.github.io/pyvene/
- **GitHub Repository**: https://github.com/stanfordnlp/pyvene
- **Paper**: https://arxiv.org/abs/2403.07809 (NAACL 2024)

## Installation

```bash
pip install pyvene
```

## Basic Usage

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load model
model = AutoModelForCausalLM.from_pretrained("gpt2")
tokenizer = AutoTokenizer.from_pretrained("gpt2")

# Define intervention
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=5,
            component="block_output",
            intervention_type=pv.VanillaIntervention,
        )
    ]
)

# Create intervenable model
intervenable = pv.IntervenableModel(config, model)

# Run intervention (swap activations from source to base)
base_inputs = tokenizer("The cat sat on the", return_tensors="pt")
source_inputs = tokenizer("The dog ran through the", return_tensors="pt")

_, outputs = intervenable(
    base=base_inputs,
    sources=[source_inputs],
)
```

## Key Concepts

### Intervention Types
- **VanillaIntervention**: Swap activations between runs
- **AdditionIntervention**: Add source to base activations
- **ZeroIntervention**: Zero out activations (ablation)
- **CollectIntervention**: Collect activations without modifying
- **RotatedSpaceIntervention**: Trainable intervention for causal discovery

### Components
Target specific parts of the model:
- `block_input`, `block_output`
- `mlp_input`, `mlp_output`, `mlp_activation`
- `attention_input`, `attention_output`
- `query_output`, `key_output`, `value_output`

### HuggingFace Integration
Save and load interventions via HuggingFace Hub for reproducibility.




### Api

# pyvene API Reference

## IntervenableModel

The core class that wraps PyTorch models for intervention.

### Basic Usage

```python
import pyvene as pv
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained("gpt2")

config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=5,
            component="block_output",
            intervention_type=pv.VanillaIntervention,
        )
    ]
)

intervenable = pv.IntervenableModel(config, model)
```

### Forward Pass

```python
# Basic intervention
original_output, intervened_output = intervenable(
    base=base_inputs,
    sources=[source_inputs],
)

# With unit locations (position-specific)
_, outputs = intervenable(
    base=base_inputs,
    sources=[source_inputs],
    unit_locations={"sources->base": ([[[5]]], [[[5]]])},  # Position 5
)

# Return original output too
original, intervened = intervenable(
    base=base_inputs,
    sources=[source_inputs],
    output_original_output=True,
)
```

### Generation

```python
# Generate with interventions
outputs = intervenable.generate(
    base_inputs,
    sources=[source_inputs],
    max_new_tokens=50,
    do_sample=False,
)
```

### Saving and Loading

```python
# Save locally
intervenable.save("./my_intervention")

# Load
intervenable = pv.IntervenableModel.load("./my_intervention", model=model)

# Save to HuggingFace
intervenable.save_intervention("username/my-intervention")

# Load from HuggingFace
intervenable = pv.IntervenableModel.load(
    "username/my-intervention",
    model=model
)
```

### Getting Trainable Parameters

```python
# For trainable interventions
params = intervenable.get_trainable_parameters()
optimizer = torch.optim.Adam(params, lr=1e-4)
```

---

## IntervenableConfig

Configuration container for interventions.

### Basic Config

```python
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(...)
    ]
)
```

### Multiple Interventions

```python
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(layer=3, component="block_output", ...),
        pv.RepresentationConfig(layer=5, component="mlp_output", ...),
        pv.RepresentationConfig(layer=7, component="attention_output", ...),
    ]
)
```

---

## RepresentationConfig

Specifies a single intervention target.

### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `layer` | int | Layer index |
| `component` | str | Component to intervene on |
| `intervention_type` | type | Intervention class |
| `unit` | str | Intervention unit ("pos", "h", etc.) |
| `max_number_of_units` | int | Max units to intervene |
| `low_rank_dimension` | int | For trainable interventions |
| `subspace_partition` | list | Dimension ranges |

### Components

| Component | Description |
|-----------|-------------|
| `block_input` | Input to transformer block |
| `block_output` | Output of transformer block |
| `mlp_input` | Input to MLP |
| `mlp_output` | Output of MLP |
| `mlp_activation` | MLP hidden activations |
| `attention_input` | Input to attention |
| `attention_output` | Output of attention |
| `attention_value_output` | Attention values |
| `query_output` | Query vectors |
| `key_output` | Key vectors |
| `value_output` | Value vectors |
| `head_attention_value_output` | Per-head values |

### Example Configs

```python
# Position-specific intervention
pv.RepresentationConfig(
    layer=5,
    component="block_output",
    intervention_type=pv.VanillaIntervention,
    unit="pos",
    max_number_of_units=1,
)

# Trainable low-rank intervention
pv.RepresentationConfig(
    layer=5,
    component="block_output",
    intervention_type=pv.LowRankRotatedSpaceIntervention,
    low_rank_dimension=64,
)

# Subspace intervention
pv.RepresentationConfig(
    layer=5,
    component="block_output",
    intervention_type=pv.VanillaIntervention,
    subspace_partition=[[0, 256], [256, 512]],  # First 512 dims split
)
```

---

## Intervention Types

### Basic Interventions

#### VanillaIntervention
Replaces base activations with source activations.

```python
pv.RepresentationConfig(
    intervention_type=pv.VanillaIntervention,
    ...
)
```

#### AdditionIntervention
Adds source activations to base.

```python
pv.RepresentationConfig(
    intervention_type=pv.AdditionIntervention,
    ...
)
```

#### SubtractionIntervention
Subtracts source from base.

```python
pv.RepresentationConfig(
    intervention_type=pv.SubtractionIntervention,
    ...
)
```

#### ZeroIntervention
Sets activations to zero (ablation).

```python
pv.RepresentationConfig(
    intervention_type=pv.ZeroIntervention,
    ...
)
```

#### CollectIntervention
Collects activations without modification.

```python
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=5,
            component="block_output",
            intervention_type=pv.CollectIntervention,
        )
    ]
)

intervenable = pv.IntervenableModel(config, model)
_, collected = intervenable(base=inputs)
# collected contains the activations
```

### Trainable Interventions

#### RotatedSpaceIntervention
Full-rank trainable rotation.

```python
pv.RepresentationConfig(
    intervention_type=pv.RotatedSpaceIntervention,
    ...
)
```

#### LowRankRotatedSpaceIntervention
Low-rank trainable intervention (DAS).

```python
pv.RepresentationConfig(
    intervention_type=pv.LowRankRotatedSpaceIntervention,
    low_rank_dimension=64,
    ...
)
```

#### BoundlessRotatedSpaceIntervention
Boundless DAS variant.

```python
pv.RepresentationConfig(
    intervention_type=pv.BoundlessRotatedSpaceIntervention,
    ...
)
```

#### SigmoidMaskIntervention
Learnable binary mask.

```python
pv.RepresentationConfig(
    intervention_type=pv.SigmoidMaskIntervention,
    ...
)
```

---

## Unit Locations

Specify exactly where to intervene.

### Format

```python
unit_locations = {
    "sources->base": (source_locations, base_locations)
}
```

### Examples

```python
# Single position
unit_locations = {"sources->base": ([[[5]]], [[[5]]])}

# Multiple positions
unit_locations = {"sources->base": ([[[3, 5, 7]]], [[[3, 5, 7]]])}

# Different source and base positions
unit_locations = {"sources->base": ([[[5]]], [[[10]]])}
```

---

## Supported Models

pyvene works with any PyTorch model. Officially tested:

| Family | Models |
|--------|--------|
| GPT-2 | gpt2, gpt2-medium, gpt2-large, gpt2-xl |
| LLaMA | llama-7b, llama-2-7b, llama-2-13b |
| Pythia | pythia-70m to pythia-12b |
| Mistral | mistral-7b, mixtral-8x7b |
| Gemma | gemma-2b, gemma-7b |
| Vision | BLIP, LLaVA |
| Other | OPT, Phi, Qwen, ESM, Mamba |

---

## Quick Reference: Common Patterns

### Activation Patching
```python
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=layer,
            component="block_output",
            intervention_type=pv.VanillaIntervention,
        )
    ]
)
```

### Causal Tracing (ROME-style)
```python
config = pv.IntervenableConfig(
    representations=[
        # First corrupt with noise
        pv.RepresentationConfig(
            layer=0,
            component="block_input",
            intervention_type=pv.NoiseIntervention,
        ),
        # Then restore at target layer
        pv.RepresentationConfig(
            layer=target_layer,
            component="block_output",
            intervention_type=pv.VanillaIntervention,
        ),
    ]
)
```

### DAS (Distributed Alignment Search)
```python
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=layer,
            component="block_output",
            intervention_type=pv.LowRankRotatedSpaceIntervention,
            low_rank_dimension=1,  # Find 1D causal direction
        )
    ]
)
```




### Tutorials

# pyvene Tutorials

## Tutorial 1: Basic Activation Patching

### Goal
Swap activations between two prompts to test causal relationships.

### Step-by-Step

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch

# 1. Load model
model = AutoModelForCausalLM.from_pretrained("gpt2")
tokenizer = AutoTokenizer.from_pretrained("gpt2")

# 2. Prepare inputs
base_prompt = "The Colosseum is in the city of"
source_prompt = "The Eiffel Tower is in the city of"

base_inputs = tokenizer(base_prompt, return_tensors="pt")
source_inputs = tokenizer(source_prompt, return_tensors="pt")

# 3. Define intervention (patch layer 8)
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=8,
            component="block_output",
            intervention_type=pv.VanillaIntervention,
        )
    ]
)

intervenable = pv.IntervenableModel(config, model)

# 4. Run intervention
_, patched_outputs = intervenable(
    base=base_inputs,
    sources=[source_inputs],
)

# 5. Check predictions
patched_logits = patched_outputs.logits
probs = torch.softmax(patched_logits[0, -1], dim=-1)

rome_token = tokenizer.encode(" Rome")[0]
paris_token = tokenizer.encode(" Paris")[0]

print(f"P(Rome): {probs[rome_token].item():.4f}")
print(f"P(Paris): {probs[paris_token].item():.4f}")
```

---

## Tutorial 2: Causal Tracing (ROME-style)

### Goal
Locate where factual associations are stored by corrupting inputs and restoring activations.

### Step-by-Step

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch

model = AutoModelForCausalLM.from_pretrained("gpt2-xl")
tokenizer = AutoTokenizer.from_pretrained("gpt2-xl")

# 1. Define prompts
clean_prompt = "The Space Needle is in downtown"
# We'll corrupt by adding noise to embeddings

clean_inputs = tokenizer(clean_prompt, return_tensors="pt")
seattle_token = tokenizer.encode(" Seattle")[0]

# 2. Get clean baseline
with torch.no_grad():
    clean_outputs = model(**clean_inputs)
    clean_prob = torch.softmax(clean_outputs.logits[0, -1], dim=-1)[seattle_token].item()

print(f"Clean P(Seattle): {clean_prob:.4f}")

# 3. Sweep over layers - corrupt input, restore at each layer
results = []

for restore_layer in range(model.config.n_layer):
    # Config: add noise at input, restore at target layer
    config = pv.IntervenableConfig(
        representations=[
            # Noise intervention at embedding
            pv.RepresentationConfig(
                layer=0,
                component="block_input",
                intervention_type=pv.NoiseIntervention,
            ),
            # Restore clean at target layer
            pv.RepresentationConfig(
                layer=restore_layer,
                component="block_output",
                intervention_type=pv.VanillaIntervention,
            ),
        ]
    )

    intervenable = pv.IntervenableModel(config, model)

    # Source is clean (for restoration), base gets noise
    _, outputs = intervenable(
        base=clean_inputs,
        sources=[clean_inputs],  # Restore from clean
    )

    prob = torch.softmax(outputs.logits[0, -1], dim=-1)[seattle_token].item()
    results.append(prob)
    print(f"Restore at layer {restore_layer}: P(Seattle) = {prob:.4f}")

# 4. Find critical layers (where restoration helps most)
import numpy as np
results = np.array(results)
critical_layers = np.argsort(results)[-5:]
print(f"\nMost critical layers: {critical_layers}")
```

---

## Tutorial 3: Trainable Interventions (DAS)

### Goal
Learn a low-rank intervention that achieves a target counterfactual behavior.

### Step-by-Step

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer
import torch

model = AutoModelForCausalLM.from_pretrained("gpt2")
tokenizer = AutoTokenizer.from_pretrained("gpt2")

# 1. Define trainable intervention
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=6,
            component="block_output",
            intervention_type=pv.LowRankRotatedSpaceIntervention,
            low_rank_dimension=64,  # Learn 64-dim subspace
        )
    ]
)

intervenable = pv.IntervenableModel(config, model)

# 2. Setup optimizer
optimizer = torch.optim.Adam(
    intervenable.get_trainable_parameters(),
    lr=1e-3
)

# 3. Training data (simplified example)
# Goal: Make model predict "Paris" instead of "Rome"
base_prompt = "The capital of Italy is"
target_token = tokenizer.encode(" Paris")[0]

base_inputs = tokenizer(base_prompt, return_tensors="pt")

# 4. Training loop
for step in range(100):
    optimizer.zero_grad()

    _, outputs = intervenable(
        base=base_inputs,
        sources=[base_inputs],  # Self-intervention
    )

    # Loss: maximize probability of target token
    logits = outputs.logits[0, -1]
    loss = -torch.log_softmax(logits, dim=-1)[target_token]

    loss.backward()
    optimizer.step()

    if step % 20 == 0:
        prob = torch.softmax(logits.detach(), dim=-1)[target_token].item()
        print(f"Step {step}: loss={loss.item():.4f}, P(Paris)={prob:.4f}")

# 5. Analyze learned rotation
rotation = intervenable.interventions["layer.6.comp.block_output.unit.pos.nunit.1#0"][0]
print(f"Learned rotation shape: {rotation.rotate_layer.weight.shape}")
```

---

## Tutorial 4: Position-Specific Intervention

### Goal
Intervene at specific token positions only.

### Step-by-Step

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained("gpt2")
tokenizer = AutoTokenizer.from_pretrained("gpt2")

# 1. Setup
base_prompt = "John and Mary went to the store"
source_prompt = "Alice and Bob went to the store"

base_inputs = tokenizer(base_prompt, return_tensors="pt")
source_inputs = tokenizer(source_prompt, return_tensors="pt")

# 2. Position-specific config
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=5,
            component="block_output",
            intervention_type=pv.VanillaIntervention,
            unit="pos",
            max_number_of_units=1,  # Single position
        )
    ]
)

intervenable = pv.IntervenableModel(config, model)

# 3. Intervene at position 0 only (first name)
_, outputs = intervenable(
    base=base_inputs,
    sources=[source_inputs],
    unit_locations={"sources->base": ([[[0]]], [[[0]]])},
)

# 4. Intervene at multiple positions
_, outputs = intervenable(
    base=base_inputs,
    sources=[source_inputs],
    unit_locations={"sources->base": ([[[0, 2]]], [[[0, 2]]])},
)
```

---

## Tutorial 5: Collecting Activations

### Goal
Extract activations without modifying them.

### Step-by-Step

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained("gpt2")
tokenizer = AutoTokenizer.from_pretrained("gpt2")

# 1. Config with CollectIntervention
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=5,
            component="block_output",
            intervention_type=pv.CollectIntervention,
        ),
        pv.RepresentationConfig(
            layer=10,
            component="attention_output",
            intervention_type=pv.CollectIntervention,
        ),
    ]
)

intervenable = pv.IntervenableModel(config, model)

# 2. Run and collect
inputs = tokenizer("Hello world", return_tensors="pt")
_, collected = intervenable(base=inputs)

# 3. Access collected activations
layer5_output = collected[0]
layer10_attn = collected[1]

print(f"Layer 5 block output shape: {layer5_output.shape}")
print(f"Layer 10 attention output shape: {layer10_attn.shape}")
```

---

## Tutorial 6: Generation with Interventions

### Goal
Apply interventions during text generation.

### Step-by-Step

```python
import pyvene as pv
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained("gpt2")
tokenizer = AutoTokenizer.from_pretrained("gpt2")
tokenizer.pad_token = tokenizer.eos_token

# 1. Get steering direction (happy vs sad)
happy_inputs = tokenizer("I am very happy and", return_tensors="pt")
sad_inputs = tokenizer("I am very sad and", return_tensors="pt")

# Collect activations
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=6,
            component="mlp_output",
            intervention_type=pv.CollectIntervention,
        )
    ]
)
collector = pv.IntervenableModel(config, model)

_, happy_acts = collector(base=happy_inputs)
_, sad_acts = collector(base=sad_inputs)

steering_direction = happy_acts[0].mean(dim=1) - sad_acts[0].mean(dim=1)

# 2. Config for steering during generation
config = pv.IntervenableConfig(
    representations=[
        pv.RepresentationConfig(
            layer=6,
            component="mlp_output",
            intervention_type=pv.AdditionIntervention,
        )
    ]
)

intervenable = pv.IntervenableModel(config, model)

# 3. Generate with steering
prompt = "Today I feel"
inputs = tokenizer(prompt, return_tensors="pt")

# Create source with steering direction
# (This is simplified - actual implementation varies)
output = intervenable.generate(
    inputs,
    max_new_tokens=20,
    do_sample=True,
    temperature=0.7,
)

print(tokenizer.decode(output[0]))
```

---

## External Resources

### Official Tutorials
- [pyvene 101](https://stanfordnlp.github.io/pyvene/tutorials/pyvene_101.html)
- [Causal Tracing](https://stanfordnlp.github.io/pyvene/tutorials/advanced_tutorials/Causal_Tracing.html)
- [DAS Introduction](https://stanfordnlp.github.io/pyvene/tutorials/advanced_tutorials/DAS_Main_Introduction.html)
- [IOI Replication](https://stanfordnlp.github.io/pyvene/tutorials/advanced_tutorials/IOI_Replication.html)

### Papers
- [pyvene Paper](https://arxiv.org/abs/2403.07809) - NAACL 2024
- [ROME](https://arxiv.org/abs/2202.05262) - Meng et al. (2022)
- [Inference-Time Intervention](https://arxiv.org/abs/2306.03341) - Li et al. (2023)




---

## 🚀 Usage

**Reference this template:** `@skill-pyvene-interventions.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
