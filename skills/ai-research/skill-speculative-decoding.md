---
id: skill-speculative-decoding
type: skill
name: speculative-decoding
description: Accelerate LLM inference using speculative decoding, Medusa multiple
  heads, and lookahead decoding techniques. Use when optimizing inference speed (1.5-3.6×
  speedup), reducing latency for real-time applications, or deploying models with
  limited compute. Covers draft models, tree-based attention, Jacobi iteration, parallel
  token generation, and production deployment strategies.
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
token_estimate: 2008
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~2,008 -->
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




# speculative-decoding

> Accelerate LLM inference using speculative decoding, Medusa multiple heads, and lookahead decoding techniques. Use when optimizing inference speed (1.5-3.6× speedup), reducing latency for real-time applications, or deploying models with limited compute. Covers draft models, tree-based attention, Jacobi iteration, parallel token generation, and production deployment strategies.

# Speculative Decoding: Accelerating LLM Inference

## When to Use This Skill

Use Speculative Decoding when you need to:
- **Speed up inference** by 1.5-3.6× without quality loss
- **Reduce latency** for real-time applications (chatbots, code generation)
- **Optimize throughput** for high-volume serving
- **Deploy efficiently** on limited hardware
- **Generate faster** without changing model architecture

**Key Techniques**: Draft model speculative decoding, Medusa (multiple heads), Lookahead Decoding (Jacobi iteration)

**Papers**: Medusa (arXiv 2401.10774), Lookahead Decoding (ICML 2024), Speculative Decoding Survey (ACL 2024)

## Installation

```bash
# Standard speculative decoding (transformers)
pip install transformers accelerate

# Medusa (multiple decoding heads)
git clone https://github.com/FasterDecoding/Medusa
cd Medusa
pip install -e .

# Lookahead Decoding
git clone https://github.com/hao-ai-lab/LookaheadDecoding
cd LookaheadDecoding
pip install -e .

# Optional: vLLM with speculative decoding
pip install vllm
```

## Quick Start

### Basic Speculative Decoding (Draft Model)

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load target model (large, slow)
target_model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",
    device_map="auto",
    torch_dtype=torch.float16
)

# Load draft model (small, fast)
draft_model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    device_map="auto",
    torch_dtype=torch.float16
)

tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-70b-hf")

# Generate with speculative decoding
prompt = "Explain quantum computing in simple terms:"
inputs = tokenizer(prompt, return_tensors="pt").to("cuda")

# Transformers 4.36+ supports assisted generation
outputs = target_model.generate(
    **inputs,
    assistant_model=draft_model,  # Enable speculative decoding
    max_new_tokens=256,
    do_sample=True,
    temperature=0.7,
)

response = tokenizer.decode(outputs[0], skip_special_tokens=True)
print(response)
```

### Medusa (Multiple Decoding Heads)

```python
from medusa.model.medusa_model import MedusaModel

# Load Medusa-enhanced model
model = MedusaModel.from_pretrained(
    "FasterDecoding/medusa-vicuna-7b-v1.3",  # Pre-trained with Medusa heads
    torch_dtype=torch.float16,
    device_map="auto"
)

tokenizer = AutoTokenizer.from_pretrained("FasterDecoding/medusa-vicuna-7b-v1.3")

# Generate with Medusa (2-3× speedup)
prompt = "Write a Python function to calculate fibonacci numbers:"
inputs = tokenizer(prompt, return_tensors="pt").to("cuda")

outputs = model.medusa_generate(
    **inputs,
    max_new_tokens=256,
    temperature=0.7,
    posterior_threshold=0.09,  # Acceptance threshold
    posterior_alpha=0.3,       # Tree construction parameter
)

response = tokenizer.decode(outputs[0], skip_special_tokens=True)
```

### Lookahead Decoding (Jacobi Iteration)

```python
from lookahead.lookahead_decoding import LookaheadDecoding

# Load model
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",
    torch_dtype=torch.float16,
    device_map="auto"
)
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-7b-hf")

# Initialize lookahead decoding
lookahead = LookaheadDecoding(
    model=model,
    tokenizer=tokenizer,
    window_size=15,    # Lookahead window (W)
    ngram_size=5,      # N-gram size (N)
    guess_size=5       # Number of parallel guesses
)

# Generate (1.5-2.3× speedup)
prompt = "Implement quicksort in Python:"
output = lookahead.generate(prompt, max_new_tokens=256)
print(output)
```

## Core Concepts

### 1. Speculative Decoding (Draft Model)

**Idea**: Use small draft model to generate candidates, large target model to verify in parallel.

**Algorithm**:
1. Draft model generates K tokens speculatively
2. Target model evaluates all K tokens in parallel (single forward pass)
3. Accept tokens where draft and target agree
4. Reject first disagreement, continue from there

```python
def speculative_decode(target_model, draft_model, prompt, K=4):
    """Speculative decoding algorithm."""
    # 1. Generate K draft tokens
    draft_tokens = draft_model.generate(prompt, max_new_tokens=K)

    # 2. Target model evaluates all K tokens in one forward pass
    target_logits = target_model(draft_tokens)  # Parallel!

    # 3. Accept/reject based on probability match
    accepted = []
    for i in range(K):
        p_draft = softmax(draft_model.logits[i])
        p_target = softmax(target_logits[i])

        # Acceptance probability
        if random.random() < min(1, p_target[draft_tokens[i]] / p_draft[draft_tokens[i]]):
            accepted.append(draft_tokens[i])
        else:
            break  # Reject, resample from target

    return accepted
```

**Performance**:
- Speedup: 1.5-2× with good draft model
- Zero quality loss (mathematically equivalent to target model)
- Best when draft model is 5-10× smaller than target

### 2. Medusa (Multiple Decoding Heads)

**Source**: arXiv 2401.10774 (2024)

**Innovation**: Add multiple prediction heads to existing model, predict future tokens without separate draft model.

**Architecture**:
```
Input → Base LLM (frozen) → Hidden State
                                ├→ Head 1 (predicts token t+1)
                                ├→ Head 2 (predicts token t+2)
                                ├→ Head 3 (predicts token t+3)
                                └→ Head 4 (predicts token t+4)
```

**Training**:
- **Medusa-1**: Freeze base LLM, train only heads
  - 2.2× speedup, lossless
- **Medusa-2**: Fine-tune base LLM + heads together
  - 2.3-3.6× speedup, better quality

**Tree-based Attention**:
```python
# Medusa constructs tree of candidates
# Example: Predict 2 steps ahead with top-2 per step

#         Root
#        /    \
#      T1a    T1b  (Step 1: 2 candidates)
#     /  \    / \
#  T2a  T2b T2c T2d  (Step 2: 4 candidates total)

# Single forward pass evaluates entire tree!
```

**Advantages**:
- No separate draft model needed
- Minimal training (only heads)
- Compatible with any LLM

### 3. Lookahead Decoding (Jacobi Iteration)

**Source**: ICML 2024

**Core idea**: Reformulate autoregressive decoding as solving system of equations, solve in parallel using Jacobi iteration.

**Mathematical formulation**:
```
Traditional:  y_t = f(x, y_1, ..., y_{t-1})  (sequential)
Jacobi:       y_t^{(k+1)} = f(x, y_1^{(k)}, ..., y_{t-1}^{(k)})  (parallel)
```

**Two branches**:

1. **Lookahead Branch**: Generate n-grams in parallel
   - Window size W: How many steps to look ahead
   - N-gram size N: How many past tokens to use

2. **Verification Branch**: Verify promising n-grams
   - Match n-grams with generated tokens
   - Accept if first token matches

```python
class LookaheadDecoding:
    def __init__(self, model, window_size=15, ngram_size=5):
        self.model = model
        self.W = window_size  # Lookahead window
        self.N = ngram_size   # N-gram size

    def generate_step(self, tokens):
        # Lookahead branch: Generate W × N candidates
        candidates = {}
        for w in range(1, self.W + 1):
            for n in range(1, self.N + 1):
                # Generate n-gram starting at position w
                ngram = self.generate_ngram(tokens, start=w, length=n)
                candidates[(w, n)] = ngram

        # Verification branch: Find matching n-grams
        verified = []
        for ngram in candidates.values():
            if ngram[0] == tokens[-1]:  # First token matches last input
                if self.verify(tokens, ngram):
                    verified.append(ngram)

        # Accept longest verified n-gram
        return max(verified, key=len) if verified else [self.model.generate_next(tokens)]
```

**Performance**:
- Speedup: 1.5-2.3× (up to 3.6× for code generation)
- No draft model or training needed
- Works out-of-the-box with any model

## Method Comparison

| Method | Speedup | Training Needed | Draft Model | Quality Loss |
|--------|---------|-----------------|-------------|--------------|
| **Draft Model Speculative** | 1.5-2× | No | Yes (external) | None |
| **Medusa** | 2-3.6× | Minimal (heads only) | No (built-in heads) | None |
| **Lookahead** | 1.5-2.3× | None | No | None |
| **Naive Batching** | 1.2-1.5× | No | No | None |

## Advanced Patterns

### Training Medusa Heads

```python
from medusa.model.medusa_model import MedusaModel
from medusa.model.kv_cache import initialize_past_key_values
import torch.nn as nn

# 1. Load base model
base_model = AutoModelForCausalLM.from_pretrained(
    "lmsys/vicuna-7b-v1.3",
    torch_dtype=torch.float16
)

# 2. Add Medusa heads
num_heads = 4
medusa_heads = nn.ModuleList([
    nn.Linear(base_model.config.hidden_size, base_model.config.vocab_size, bias=False)
    for _ in range(num_heads)
])

# 3. Training loop (freeze base model for Medusa-1)
for param in base_model.parameters():
    param.requires_grad = False  # Freeze base

optimizer = torch.optim.Adam(medusa_heads.parameters(), lr=1e-3)

for batch in dataloader:
    # Forward pass
    hidden_states = base_model(**batch, output_hidden_states=True).hidden_states[-1]

    # Predict future tokens with each head
    loss = 0
    for i, head in enumerate(medusa_heads):
        logits = head(hidden_states)
        # Target: tokens shifted by (i+1) positions
        target = batch['input_ids'][:, i+1:]
        loss += F.cross_entropy(logits[:, :-i-1], target)

    # Backward
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
```

### Hybrid: Speculative + Medusa

```python
# Use Medusa as draft model for speculative decoding
draft_medusa = MedusaModel.from_pretrained("medusa-vicuna-7b")
target_model = AutoModelForCausalLM.from_pretrained("vicuna-33b")

# Draft generates multiple candidates with Medusa
draft_tokens = draft_medusa.medusa_generate(prompt, max_new_tokens=5)

# Target verifies in single forward pass
outputs = target_model.generate(
    prompt,
    assistant_model=draft_medusa,  # Use Medusa as draft
    max_new_tokens=256
)

# Combines benefits: Medusa speed + large model quality
```

### Optimal Draft Model Selection

```python
def select_draft_model(target_model_size, target):
    """Select optimal draft model for speculative decoding."""
    # Rule: Draft should be 5-10× smaller
    if target_model_size == "70B":
        return "7B"  # 10× smaller
    elif target_model_size == "33B":
        return "7B"  # 5× smaller
    elif target_model_size == "13B":
        return "1B"  # 13× smaller
    else:
        return None  # Target too small, use Medusa/Lookahead instead

# Example
draft = select_draft_model("70B", target_model)
# Returns "7B" → Use Llama-2-7b as draft for Llama-2-70b
```

## Best Practices

### 1. Choose the Right Method

```python
# New deployment → Medusa (best overall speedup, no draft model)
if deploying_new_model:
    use_method = "Medusa"

# Existing deployment with small model available → Draft speculative
elif have_small_version_of_model:
    use_method = "Draft Model Speculative"

# Want zero training/setup → Lookahead
elif want_plug_and_play:
    use_method = "Lookahead Decoding"
```

### 2. Hyperparameter Tuning

**Draft Model Speculative**:
```python
# K = number of speculative tokens
K = 4  # Good default
K = 2  # Conservative (higher acceptance)
K = 8  # Aggressive (lower acceptance, but more when accepted)

# Rule: Larger K → more speedup IF draft model is good
```

**Medusa**:
```python
# Posterior threshold (acceptance confidence)
posterior_threshold = 0.09  # Standard (from paper)
posterior_threshold = 0.05  # More conservative (slower, higher quality)
posterior_threshold = 0.15  # More aggressive (faster, may degrade quality)

# Tree depth (how many steps ahead)
medusa_choices = [[0], [0, 0], [0, 1], [0, 0, 0]]  # Depth 3 (standard)
```

**Lookahead**:
```python
# Window size W (lookahead distance)
# N-gram size N (context for generation)

# 7B model (more resources)
W, N = 15, 5

# 13B model (moderate)
W, N = 10, 5

# 33B+ model (limited resources)
W, N = 7, 5
```

### 3. Production Deployment

```python
# vLLM with speculative decoding
from vllm import LLM, SamplingParams

# Initialize with draft model
llm = LLM(
    model="meta-llama/Llama-2-70b-hf",
    speculative_model="meta-llama/Llama-2-7b-hf",  # Draft model
    num_speculative_tokens=5,
    use_v2_block_manager=True,
)

# Generate
prompts = ["Tell me about AI:", "Explain quantum physics:"]
sampling_params = SamplingParams(temperature=0.7, max_tokens=256)

outputs = llm.generate(prompts, sampling_params)
for output in outputs:
    print(output.outputs[0].text)
```

## Resources

- **Medusa Paper**: https://arxiv.org/abs/2401.10774
- **Medusa GitHub**: https://github.com/FasterDecoding/Medusa
- **Lookahead Decoding (ICML 2024)**: https://lmsys.org/blog/2023-11-21-lookahead-decoding/
- **Lookahead GitHub**: https://github.com/hao-ai-lab/LookaheadDecoding
- **Speculative Decoding Survey (ACL 2024)**: https://aclanthology.org/2024.findings-acl.456.pdf
- **Comprehensive Survey**: https://arxiv.org/abs/2401.07851

## See Also

- `references/draft_model.md` - Draft model selection and training
- `references/medusa.md` - Medusa architecture and training
- `references/lookahead.md` - Lookahead decoding implementation details




---


## 📚 Reference Materials


### Medusa

# Medusa: Multiple Decoding Heads

Based on arXiv 2401.10774 (2024) - MEDUSA: Simple LLM Inference Acceleration Framework with Multiple Decoding Heads

## Overview

**Source**: https://arxiv.org/abs/2401.10774
**GitHub**: https://github.com/FasterDecoding/Medusa

Medusa augments LLM inference by adding extra decoding heads to predict multiple subsequent tokens in parallel, achieving 2.2-3.6× speedup without quality loss.

## Architecture

### Core Innovation

Instead of separate draft model, add multiple prediction heads to existing LLM:

```
Input → Base LLM (frozen or fine-tuned) → Hidden State
                                             ├→ Head 0 (original, predicts t+1)
                                             ├→ Head 1 (predicts t+2)
                                             ├→ Head 2 (predicts t+3)
                                             └→ Head 3 (predicts t+4)
```

### Tree-Based Attention

**Key mechanism**: Construct candidate tree, verify all paths in single forward pass.

Example with 2 heads, top-2 candidates per head:

```
                Root (current token)
                /                  \
           Candidate 1a         Candidate 1b    (Head 1: 2 options)
           /        \           /        \
        C2a        C2b       C2c        C2d     (Head 2: 4 total paths)
```

Single forward pass evaluates entire tree (4 candidates) in parallel!

## Training Methods

### Medusa-1: Frozen Backbone

**Approach**: Keep base LLM frozen, train only Medusa heads.

**Advantages**:
- Lossless (base model unchanged)
- Fast training (~few hours on 8 GPUs)
- Minimal data needed (~10M tokens)

**Performance**: 2.2× speedup

```python
# Training loop for Medusa-1
for batch in dataloader:
    # Frozen base model
    with torch.no_grad():
        hidden_states = base_model(**batch, output_hidden_states=True).hidden_states[-1]

    # Train Medusa heads
    for i, head in enumerate(medusa_heads):
        logits = head(hidden_states)
        # Target: tokens shifted by (i+1) positions
        targets = batch['input_ids'][:, i+1:]
        loss += F.cross_entropy(logits[:, :-i-1], targets)

    loss.backward()
    optimizer.step()
```

**Training Data**: Any text corpus (Wikipedia, C4, etc.)

### Medusa-2: Joint Fine-Tuning

**Approach**: Fine-tune base LLM + Medusa heads together.

**Advantages**:
- Better prediction accuracy (heads aligned with base)
- Higher speedup (2.3-3.6×)

**Challenge**: Must preserve base model capabilities

**Solution**: Special training recipe:
1. Start with pre-trained base model
2. Add Medusa heads
3. Fine-tune both together with careful LR scheduling
4. Use high-quality data to avoid degradation

```python
# Medusa-2 training
# All parameters trainable
for param in base_model.parameters():
    param.requires_grad = True  # Unfreeze base

for param in medusa_heads.parameters():
    param.requires_grad = True

# Different learning rates
optimizer = torch.optim.AdamW([
    {'params': base_model.parameters(), 'lr': 1e-5},      # Lower for base
    {'params': medusa_heads.parameters(), 'lr': 1e-3},    # Higher for heads
])
```

**Performance**: 2.3-3.6× speedup

## Inference Algorithm

### Candidate Generation

```python
def medusa_generate_candidates(base_logits, medusa_head_logits, top_k=10):
    """Generate candidate sequences using tree structure."""
    candidates = []

    # Base token (original LLM output)
    base_token = torch.argmax(base_logits, dim=-1)

    # For each Medusa head, get top-k predictions
    medusa_candidates = []
    for head_logits in medusa_head_logits:
        top_k_tokens = torch.topk(head_logits, k=top_k, dim=-1).indices
        medusa_candidates.append(top_k_tokens)

    # Build candidate tree (all combinations)
    # With 4 heads, top-2 each: 2^4 = 16 candidates
    for combo in itertools.product(*medusa_candidates):
        candidate = [base_token] + list(combo)
        candidates.append(candidate)

    return candidates  # Shape: (num_candidates, seq_len)
```

### Tree Verification

```python
def medusa_verify_candidates(model, candidates, past_key_values):
    """Verify all candidates in single forward pass using tree attention."""
    # Construct tree attention mask
    # All candidates share prefix, diverge at different points
    attention_mask = build_tree_attention_mask(candidates)

    # Single forward pass for all candidates
    outputs = model(
        input_ids=candidates,
        attention_mask=attention_mask,
        past_key_values=past_key_values,
        use_cache=True
    )

    # Score each candidate
    scores = compute_acceptance_scores(outputs.logits, candidates)

    # Accept longest valid candidate
    best_candidate = select_best(candidates, scores)

    return best_candidate
```

### Acceptance Criterion

**Posterior threshold**: Accept token if probability exceeds threshold.

```python
def should_accept(token, token_prob, threshold=0.09):
    """Medusa acceptance criterion."""
    return token_prob >= threshold

# Typical thresholds:
# - 0.09: Standard (from paper)
# - 0.05: Conservative (fewer rejections, slower)
# - 0.15: Aggressive (more rejections, faster when works)
```

## Performance Results

**From paper (Vicuna-7B, MT-Bench):**

| Configuration | Speedup | Quality (MT-Bench score) |
|---------------|---------|--------------------------|
| Baseline | 1.0× | 6.57 |
| Medusa-1 (frozen) | 2.2× | 6.57 (lossless) |
| Medusa-2 (joint) | 2.3× | 6.60 (+0.03) |
| Medusa-2 (optimized) | 3.6× | 6.55 (-0.02) |

**Key findings**:
- Medusa-1: No quality degradation (frozen base)
- Medusa-2: Slight quality improvement possible
- Trade-off: More aggressive = faster but may reduce quality

## Hyperparameter Tuning

### Number of Heads

```python
# Typical configurations:
num_heads = 2  # Conservative (2× speedup)
num_heads = 3  # Balanced (2.5× speedup)
num_heads = 4  # Standard (3× speedup, from paper)
num_heads = 5  # Aggressive (3.5×+ speedup)

# Rule: More heads = more candidates but also more computation
# Optimal: 3-4 heads for most models
```

### Top-K per Head

```python
# Candidates per head
top_k = 2   # Standard (2^num_heads total candidates)
top_k = 3   # More candidates (3^num_heads)
top_k = 5   # Many candidates (5^num_heads)

# Example with 4 heads:
# top_k=2: 16 candidates (fast)
# top_k=3: 81 candidates (slower verification)
```

### Tree Construction

**Medusa Choices** (which candidate paths to explore):

```python
# Standard configuration (from paper)
medusa_choices = [
    [0],        # Only head 0
    [0, 0],     # Head 0, then head 1 (first candidate)
    [0, 1],     # Head 0, then head 1 (second candidate)
    [0, 0, 0],  # All heads (first path)
]

# Aggressive configuration (more paths)
medusa_choices = [
    [0],
    [0, 0], [0, 1],
    [0, 0, 0], [0, 0, 1], [0, 1, 0], [0, 1, 1],
]
```

## Training Recipe

### Data Requirements

**Medusa-1**:
- Amount: 10M-100M tokens
- Quality: Any text corpus works
- Time: 2-8 hours on 8× A100

**Medusa-2**:
- Amount: 100M-1B tokens
- Quality: High-quality (same domain as target use case)
- Time: 1-3 days on 8× A100

### Training Script

```bash
# Clone Medusa repo
git clone https://github.com/FasterDecoding/Medusa
cd Medusa

# Train Medusa-1 (frozen base)
python medusa/train/train.py \
    --model_name_or_path lmsys/vicuna-7b-v1.3 \
    --data_path ShareGPT_Vicuna_unfiltered/ShareGPT_V4.3_unfiltered_cleaned_split.json \
    --bf16 True \
    --output_dir medusa-vicuna-7b-v1.3 \
    --num_train_epochs 3 \
    --per_device_train_batch_size 4 \
    --gradient_accumulation_steps 8 \
    --learning_rate 1e-3 \
    --medusa_num_heads 4 \
    --medusa_num_layers 1 \
    --freeze_base_model True  # Medusa-1

# Train Medusa-2 (joint fine-tuning)
python medusa/train/train.py \
    --model_name_or_path lmsys/vicuna-7b-v1.3 \
    --data_path high_quality_data.json \
    --bf16 True \
    --output_dir medusa-vicuna-7b-v1.3-joint \
    --num_train_epochs 1 \
    --per_device_train_batch_size 4 \
    --gradient_accumulation_steps 8 \
    --learning_rate 1e-5 \  # Lower LR for base model
    --medusa_num_heads 4 \
    --freeze_base_model False  # Medusa-2 (joint)
```

## Deployment

### Loading Medusa Model

```python
from medusa.model.medusa_model import MedusaModel

# Load pre-trained Medusa model
model = MedusaModel.from_pretrained(
    "FasterDecoding/medusa-vicuna-7b-v1.3",
    torch_dtype=torch.float16,
    device_map="auto"
)

# Or load base + Medusa heads separately
base_model = AutoModelForCausalLM.from_pretrained("lmsys/vicuna-7b-v1.3")
medusa_heads = torch.load("medusa_heads.pt")
model = MedusaModel(base_model, medusa_heads)
```

### Generation

```python
# Generate with Medusa
outputs = model.medusa_generate(
    input_ids,
    max_new_tokens=256,
    temperature=0.7,
    posterior_threshold=0.09,    # Acceptance threshold
    posterior_alpha=0.3,         # Tree construction parameter
    medusa_choices=medusa_choices,  # Candidate paths
)
```

## Comparison with Speculative Decoding

| Aspect | Medusa | Speculative Decoding |
|--------|--------|----------------------|
| **Draft Model** | Built-in (heads) | External (separate model) |
| **Training** | Minimal (heads only) | None (use existing small model) |
| **Memory** | Base + heads (~1-2% overhead) | Base + draft (can be large) |
| **Speedup** | 2-3.6× | 1.5-2× |
| **Deployment** | Single model | Two models |

**When to use Medusa**:
- Want single model deployment
- Can afford minimal training
- Need best speedup (3×+)

**When to use Speculative**:
- Have existing small model
- Zero training budget
- Simpler setup

## Resources

- **Paper**: https://arxiv.org/abs/2401.10774
- **GitHub**: https://github.com/FasterDecoding/Medusa
- **Blog**: https://www.together.ai/blog/medusa
- **Demo**: https://sites.google.com/view/medusa-llm




### Lookahead

# Lookahead Decoding: Jacobi Iteration

Based on ICML 2024 paper and LMSYS blog post

## Overview

**Source**: https://lmsys.org/blog/2023-11-21-lookahead-decoding/
**Paper**: ICML 2024
**GitHub**: https://github.com/hao-ai-lab/LookaheadDecoding

Lookahead Decoding breaks sequential dependency in autoregressive decoding using Jacobi iteration, achieving 1.5-2.3× speedup without draft models or training.

## Core Concept

### Reformulation as Equation Solving

**Traditional autoregressive**:
```
y_t = f(x, y_1, y_2, ..., y_{t-1})  # Sequential
```

**Jacobi iteration**:
```
y_t^{(k+1)} = f(x, y_1^{(k)}, y_2^{(k)}, ..., y_{t-1}^{(k)})  # Parallel
```

**Key insight**: Although exact parallel decoding is impossible, we can generate multiple disjoint n-grams in parallel that may fit into the final sequence.

## Two-Branch Architecture

### Lookahead Branch

**Purpose**: Generate potential token sequences (n-grams) in parallel.

**Parameters**:
- `W` (window size): How many steps ahead to look
- `N` (n-gram size): How many past tokens to use for generation

```python
# Example: W=5, N=3
# Generate n-grams at positions 1-5 using past 1-3 tokens

def lookahead_branch(model, tokens, W=5, N=3):
    """Generate n-grams using Jacobi iteration."""
    candidates = {}

    for w in range(1, W + 1):         # Position offset
        for n in range(1, N + 1):     # N-gram length
            # Use n past tokens to predict at position w
            past_tokens = tokens[-n:]
            future_position = len(tokens) + w

            # Generate n-gram
            ngram = model.generate_ngram(
                context=past_tokens,
                position=future_position,
                length=n
            )

            candidates[(w, n)] = ngram

    return candidates
```

**Output**: Pool of candidate n-grams that might match future sequence.

### Verification Branch

**Purpose**: Identify and confirm promising n-grams.

```python
def verification_branch(model, tokens, candidates):
    """Verify which candidates match actual sequence."""
    verified = []

    for ngram in candidates:
        # Check if ngram's first token matches last generated token
        if ngram[0] == tokens[-1]:
            # Verify full n-gram with model
            is_valid = model.verify_sequence(tokens + ngram)

            if is_valid:
                verified.append(ngram)

    # Return longest verified n-gram
    return max(verified, key=len) if verified else None
```

**Acceptance**: N-gram accepted if its first token matches the last input token and model confirms the sequence.

## Algorithm

### Complete Lookahead Decoding

```python
class LookaheadDecoding:
    def __init__(self, model, W=15, N=5, G=5):
        """
        Args:
            W: Window size (lookahead distance)
            N: N-gram size (context length)
            G: Guess size (parallel candidates)
        """
        self.model = model
        self.W = W
        self.N = N
        self.G = G

    def generate(self, input_ids, max_new_tokens=256):
        tokens = input_ids.clone()

        while len(tokens) < max_new_tokens:
            # 1. Lookahead: Generate candidates
            candidates = self._lookahead_step(tokens)

            # 2. Verification: Find matching n-grams
            accepted_ngram = self._verification_step(tokens, candidates)

            if accepted_ngram is not None:
                # Accept multiple tokens
                tokens = torch.cat([tokens, accepted_ngram])
            else:
                # Fallback: Generate single token
                next_token = self.model.generate_next(tokens)
                tokens = torch.cat([tokens, next_token])

        return tokens

    def _lookahead_step(self, tokens):
        """Generate candidate n-grams in parallel."""
        candidates = []

        for w in range(1, self.W + 1):
            for n in range(1, self.N + 1):
                # Sample n-gram from model
                ngram = self.model.sample_ngram(
                    tokens=tokens,
                    offset=w,
                    context_size=n,
                    num_samples=self.G
                )
                candidates.extend(ngram)

        return candidates

    def _verification_step(self, tokens, candidates):
        """Verify candidates and select best."""
        valid_ngrams = []

        for ngram in candidates:
            # Must match continuation
            if ngram[0] == self._get_next_token_prediction(tokens):
                # Verify full sequence
                if self._verify_ngram(tokens, ngram):
                    valid_ngrams.append(ngram)

        # Return longest valid n-gram
        return max(valid_ngrams, key=len) if valid_ngrams else None
```

## Performance Analysis

### Speedup vs Parameters

**From paper (7B model on HumanEval)**:

| Window (W) | N-gram (N) | Speedup | Throughput |
|------------|------------|---------|------------|
| 5 | 3 | 1.5× | 45 tokens/sec |
| 10 | 5 | 1.8× | 54 tokens/sec |
| 15 | 5 | 2.2× | 66 tokens/sec |
| 20 | 7 | 2.3× | 69 tokens/sec |

**Hardware configurations (A100 GPU)**:

| Model Size | Recommended W | Recommended N |
|------------|---------------|---------------|
| 7B | 15 | 5 |
| 13B | 10 | 5 |
| 33B | 7 | 5 |
| 70B | 5 | 3 |

**Rule**: Larger models → smaller W, N (more expensive to verify)

### Scaling Law

**Key finding from paper**:

"When n-gram size is sufficiently large, exponentially increasing future token guesses can linearly reduce decoding steps."

```
Speedup ≈ 1 + (W × acceptance_rate)

where acceptance_rate depends on:
- Model quality (better models = higher acceptance)
- Task type (code generation > chat)
- N-gram size (larger N = higher acceptance but more compute)
```

## Hyperparameter Tuning

### Window Size (W)

```python
# Trade-off: Larger W = more candidates but more verification cost

W = 5   # Conservative (low overhead, moderate speedup)
W = 10  # Balanced
W = 15  # Standard (from paper, 7B models)
W = 20  # Aggressive (diminishing returns)

# Rule: W should be ~2-3× average token acceptance length
```

### N-gram Size (N)

```python
# Trade-off: Larger N = better context but slower generation

N = 3   # Fast generation, less context
N = 5   # Standard (from paper)
N = 7   # Better context, slower

# Rule: N should be large enough to capture local patterns
```

### Guess Size (G)

```python
# Number of parallel n-gram candidates per position

G = 1   # Deterministic (fastest, lower acceptance)
G = 5   # Standard (good balance)
G = 10  # More exploration (higher acceptance, more compute)
```

## Comparison with Other Methods

| Method | Speedup | Training | Draft Model | Memory |
|--------|---------|----------|-------------|---------|
| **Lookahead** | 1.5-2.3× | None | No | Base only |
| Draft Speculative | 1.5-2× | None | Yes | Base + draft |
| Medusa | 2-3.6× | Minimal | No | Base + heads |

**Advantages of Lookahead**:
- Zero training required
- No draft model needed
- Works out-of-the-box with any model
- No model modification

**Disadvantages**:
- Lower speedup than Medusa
- More complex implementation
- Verification overhead

## Task-Specific Performance

**From paper**:

| Task | Baseline | Lookahead | Speedup |
|------|----------|-----------|---------|
| **HumanEval (code)** | 30 tok/s | 69 tok/s | 2.3× |
| **MT-Bench (chat)** | 35 tok/s | 56 tok/s | 1.6× |
| **GSM8K (math)** | 32 tok/s | 54 tok/s | 1.7× |

**Why code is faster**: Higher n-gram predictability (syntax, patterns).

## Production Deployment

### Integration Example

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load model
model = AutoModelForCausalLM.from_pretrained("meta-llama/Llama-2-7b-hf")
tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-7b-hf")

# Initialize Lookahead
lookahead = LookaheadDecoding(
    model=model,
    W=15,  # Window size
    N=5,   # N-gram size
    G=5    # Guess size
)

# Generate
prompt = "Write a Python function to calculate fibonacci:"
input_ids = tokenizer.encode(prompt, return_tensors="pt")

output = lookahead.generate(input_ids, max_new_tokens=256)
response = tokenizer.decode(output[0], skip_special_tokens=True)

print(response)
```

### Optimization Tips

1. **Batch processing**: Verify multiple n-grams in single forward pass
2. **Caching**: Reuse KV cache across verification steps
3. **Early stopping**: Stop generation when no candidates match
4. **Adaptive parameters**: Adjust W, N based on acceptance rate

## Resources

- **Blog Post**: https://lmsys.org/blog/2023-11-21-lookahead-decoding/
- **GitHub**: https://github.com/hao-ai-lab/LookaheadDecoding
- **Paper**: ICML 2024 (Break the Sequential Dependency of LLM Inference Using Lookahead Decoding)
- **NVIDIA Blog**: https://developer.nvidia.com/blog/optimizing-qwen2-5-coder-throughput-with-nvidia-tensorrt-llm-lookahead-decoding/




---

## 🚀 Usage

**Reference this template:** `@skill-speculative-decoding.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
