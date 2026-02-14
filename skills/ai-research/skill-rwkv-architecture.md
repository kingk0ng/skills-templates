---
id: skill-rwkv-architecture
type: skill
name: rwkv-architecture
description: RNN+Transformer hybrid with O(n) inference. Linear time, infinite context,
  no KV cache. Train like GPT (parallel), infer like RNN (sequential). Linux Foundation
  AI project. Production at Windows, Office, NeMo. RWKV-7 (March 2025). Models up
  to 14B parameters.
category: ai-research
complexity: medium
keywords:
- deployment
- github
- performance
- python
capabilities: []
token_estimate: 1076
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,076 -->
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




# rwkv-architecture

> RNN+Transformer hybrid with O(n) inference. Linear time, infinite context, no KV cache. Train like GPT (parallel), infer like RNN (sequential). Linux Foundation AI project. Production at Windows, Office, NeMo. RWKV-7 (March 2025). Models up to 14B parameters.

# RWKV - Receptance Weighted Key Value

## Quick start

RWKV (RwaKuv) combines Transformer parallelization (training) with RNN efficiency (inference).

**Installation**:
```bash
# Install PyTorch
pip install torch --upgrade --extra-index-url https://download.pytorch.org/whl/cu121

# Install dependencies
pip install pytorch-lightning==1.9.5 deepspeed wandb ninja --upgrade

# Install RWKV
pip install rwkv
```

**Basic usage** (GPT mode + RNN mode):
```python
import os
from rwkv.model import RWKV

os.environ["RWKV_JIT_ON"] = '1'
os.environ["RWKV_CUDA_ON"] = '1'  # Use CUDA kernel for speed

# Load model
model = RWKV(
    model='/path/to/RWKV-4-Pile-1B5-20220903-8040',
    strategy='cuda fp16'
)

# GPT mode (parallel processing)
out, state = model.forward([187, 510, 1563, 310, 247], None)
print(out.detach().cpu().numpy())  # Logits

# RNN mode (sequential processing, same result)
out, state = model.forward([187, 510], None)  # First 2 tokens
out, state = model.forward([1563], state)      # Next token
out, state = model.forward([310, 247], state)  # Last tokens
print(out.detach().cpu().numpy())  # Same logits as above!
```

## Common workflows

### Workflow 1: Text generation (streaming)

**Efficient token-by-token generation**:
```python
from rwkv.model import RWKV
from rwkv.utils import PIPELINE

model = RWKV(model='RWKV-4-Pile-14B-20230313-ctx8192-test1050', strategy='cuda fp16')
pipeline = PIPELINE(model, "20B_tokenizer.json")

# Initial prompt
prompt = "The future of AI is"
state = None

# Generate token by token
for token in prompt:
    out, state = pipeline.model.forward(pipeline.encode(token), state)

# Continue generation
for _ in range(100):
    out, state = pipeline.model.forward(None, state)
    token = pipeline.sample_logits(out)
    print(pipeline.decode(token), end='', flush=True)
```

**Key advantage**: Constant memory per token (no growing KV cache)

### Workflow 2: Long context processing (infinite context)

**Process million-token sequences**:
```python
model = RWKV(model='RWKV-4-Pile-14B', strategy='cuda fp16')

# Process very long document
state = None
long_document = load_document()  # e.g., 1M tokens

# Stream through entire document
for chunk in chunks(long_document, chunk_size=1024):
    out, state = model.forward(chunk, state)

# State now contains information from entire 1M token document
# Memory usage: O(1) (constant, not O(n)!)
```

### Workflow 3: Fine-tuning RWKV

**Standard fine-tuning workflow**:
```python
# Training script
import pytorch_lightning as pl
from rwkv.model import RWKV
from rwkv.trainer import RWKVTrainer

# Configure model
config = {
    'n_layer': 24,
    'n_embd': 1024,
    'vocab_size': 50277,
    'ctx_len': 1024
}

# Setup trainer
trainer = pl.Trainer(
    accelerator='gpu',
    devices=8,
    precision='bf16',
    strategy='deepspeed_stage_2',
    max_epochs=1
)

# Train
model = RWKV(config)
trainer.fit(model, train_dataloader)
```

### Workflow 4: RWKV vs Transformer comparison

**Memory comparison** (1M token sequence):
```python
# Transformer (GPT)
# Memory: O(n²) for attention
# KV cache: 1M × hidden_dim × n_layers × 2 (keys + values)
# Example: 1M × 4096 × 24 × 2 = ~400GB (impractical!)

# RWKV
# Memory: O(1) per token
# State: hidden_dim × n_layers = 4096 × 24 = ~400KB
# 1,000,000× more efficient!
```

**Speed comparison** (inference):
```python
# Transformer: O(n) per token (quadratic overall)
# First token: 1 computation
# Second token: 2 computations
# ...
# 1000th token: 1000 computations

# RWKV: O(1) per token (linear overall)
# Every token: 1 computation
# 1000th token: 1 computation (same as first!)
```

## When to use vs alternatives

**Use RWKV when**:
- Need very long context (100K+ tokens)
- Want constant memory usage
- Building streaming applications
- Need RNN efficiency with Transformer performance
- Memory-constrained deployment

**Key advantages**:
- **Linear time**: O(n) vs O(n²) for Transformers
- **No KV cache**: Constant memory per token
- **Infinite context**: No fixed window limit
- **Parallelizable training**: Like GPT
- **Sequential inference**: Like RNN

**Use alternatives instead**:
- **Transformers**: Need absolute best performance, have compute
- **Mamba**: Want state-space models
- **RetNet**: Need retention mechanism
- **Hyena**: Want convolution-based approach

## Common issues

**Issue: Out of memory during training**

Use gradient checkpointing and DeepSpeed:
```python
trainer = pl.Trainer(
    strategy='deepspeed_stage_3',  # Full ZeRO-3
    precision='bf16'
)
```

**Issue: Slow inference**

Enable CUDA kernel:
```python
os.environ["RWKV_CUDA_ON"] = '1'
```

**Issue: Model not loading**

Check model path and strategy:
```python
model = RWKV(
    model='/absolute/path/to/model.pth',
    strategy='cuda fp16'  # Or 'cpu fp32' for CPU
)
```

**Issue: State management in RNN mode**

Always pass state between forward calls:
```python
# WRONG: State lost
out1, _ = model.forward(tokens1, None)
out2, _ = model.forward(tokens2, None)  # No context from tokens1!

# CORRECT: State preserved
out1, state = model.forward(tokens1, None)
out2, state = model.forward(tokens2, state)  # Has context from tokens1
```

## Advanced topics

**Time-mixing and channel-mixing**: See [references/architecture-details.md](references/architecture-details.md) for WKV operation, time-decay mechanism, and receptance gates.

**State management**: See [references/state-management.md](references/state-management.md) for att_x_prev, att_kv, ffn_x_prev states, and numerical stability considerations.

**RWKV-7 improvements**: See [references/rwkv7.md](references/rwkv7.md) for latest architectural improvements (March 2025) and multimodal capabilities.

## Hardware requirements

- **GPU**: NVIDIA (CUDA 11.6+) or CPU
- **VRAM** (FP16):
  - 169M model: 1GB
  - 430M model: 2GB
  - 1.5B model: 4GB
  - 3B model: 8GB
  - 7B model: 16GB
  - 14B model: 32GB
- **Inference**: O(1) memory per token
- **Training**: Parallelizable like GPT

**Performance** (vs Transformers):
- **Speed**: Similar training, faster inference
- **Memory**: 1000× less for long sequences
- **Scaling**: Linear vs quadratic

## Resources

- Paper (RWKV): https://arxiv.org/abs/2305.13048 (May 2023)
- Paper (RWKV-7): https://arxiv.org/abs/2503.14456 (March 2025)
- GitHub: https://github.com/BlinkDL/RWKV-LM ⭐ 12,000+
- Docs: https://wiki.rwkv.com/
- Models: https://huggingface.co/BlinkDL
- Linux Foundation AI: Official project
- Production: Microsoft Windows, Office integration, NeMo support




---


## 📚 Reference Materials


### Architecture Details

# RWKV Architecture Details

## Time-Mixing and Channel-Mixing Blocks

RWKV alternates between **Time-Mixing** (sequence processing) and **Channel-Mixing** (feature processing) blocks.

### Time-Mixing Block (WKV Operation)

The core innovation is the **WKV (Weighted Key-Value)** mechanism:

```python
# Traditional Attention (O(n²))
scores = Q @ K.T / sqrt(d)  # n×n matrix
attention = softmax(scores)
output = attention @ V

# RWKV Time-Mixing (O(n))
# Compute WKV in linear time using recurrence
for t in range(T):
    wkv[t] = (exp(w) * k[t] @ v[t] + a[t] * aa[t]) / (exp(w) * k[t] + a[t] * ab[t])
    aa[t+1] = exp(w) * k[t] @ v[t] + exp(-u) * aa[t]
    ab[t+1] = exp(w) * k[t] + exp(-u) * ab[t]
```

**Full Time-Mixing implementation**:

```python
class RWKV_TimeMix(nn.Module):
    def __init__(self, d_model, n_layer):
        super().__init__()
        self.d_model = d_model

        # Linear projections
        self.key = nn.Linear(d_model, d_model, bias=False)
        self.value = nn.Linear(d_model, d_model, bias=False)
        self.receptance = nn.Linear(d_model, d_model, bias=False)
        self.output = nn.Linear(d_model, d_model, bias=False)

        # Time-mixing parameters
        self.time_mix_k = nn.Parameter(torch.ones(1, 1, d_model))
        self.time_mix_v = nn.Parameter(torch.ones(1, 1, d_model))
        self.time_mix_r = nn.Parameter(torch.ones(1, 1, d_model))

        # Time-decay and bonus
        self.time_decay = nn.Parameter(torch.ones(d_model))  # w
        self.time_first = nn.Parameter(torch.ones(d_model))  # u

    def forward(self, x, state=None):
        B, T, C = x.shape

        # Time-shift mixing (interpolate with previous token)
        if state is None:
            state = torch.zeros(B, C, 3, device=x.device)  # [aa, ab, x_prev]

        x_prev = state[:, :, 2].unsqueeze(1)  # Previous x
        xk = x * self.time_mix_k + x_prev * (1 - self.time_mix_k)
        xv = x * self.time_mix_v + x_prev * (1 - self.time_mix_v)
        xr = x * self.time_mix_r + x_prev * (1 - self.time_mix_r)

        # Compute k, v, r
        k = self.key(xk)
        v = self.value(xv)
        r = self.receptance(xr)

        # WKV computation (parallelizable or sequential)
        wkv = self.wkv(k, v, state[:, :, :2])

        # Apply receptance gate and output projection
        out = self.output(torch.sigmoid(r) * wkv)

        # Update state
        new_state = torch.stack([state_aa, state_ab, x[:, -1]], dim=2)

        return out, new_state

    def wkv(self, k, v, state):
        # Parallel implementation (training)
        # Sequential implementation (inference) - see below
        ...
```

### WKV Parallel Algorithm (Training)

```python
def wkv_forward(w, u, k, v):
    """
    Parallel WKV computation for training.
    w: time_decay (d_model,)
    u: time_first (d_model,)
    k: keys (batch, seq_len, d_model)
    v: values (batch, seq_len, d_model)
    """
    B, T, C = k.shape

    # Compute cumulative sums with exponential decay
    # This is the key to O(n) parallel computation
    w = -torch.exp(w)  # Negative for decay

    # Associative scan operation
    wkv = torch.zeros(B, T, C, device=k.device)
    state = torch.zeros(B, C, device=k.device)

    for t in range(T):
        kv = k[:, t] * v[:, t]
        wkv[:, t] = (u * kv + state) / (u * k[:, t] + torch.exp(state_count))
        state = w * state + kv

    return wkv
```

### WKV Sequential Algorithm (Inference)

```python
def wkv_inference(w, u, k, v, state):
    """
    Sequential WKV for O(1) per-token inference.
    state: (aa, ab) from previous step
    """
    w = -torch.exp(w)  # time_decay
    u = torch.exp(u)   # time_first

    # Unpack state
    aa, ab = state  # aa = numerator, ab = denominator

    # Compute WKV for current token
    kv = k * v
    wkv = (u * kv + aa) / (u * k + ab)

    # Update state for next token
    new_aa = w * aa + kv
    new_ab = w * ab + k

    return wkv, (new_aa, new_ab)
```

### Channel-Mixing Block

Replaces Transformer FFN with time-shifted variant:

```python
class RWKV_ChannelMix(nn.Module):
    def __init__(self, d_model, hidden_ratio=4):
        super().__init__()
        self.d_model = d_model
        self.hidden = d_model * hidden_ratio

        # Time-mixing for channel
        self.time_mix_k = nn.Parameter(torch.ones(1, 1, d_model))
        self.time_mix_r = nn.Parameter(torch.ones(1, 1, d_model))

        # FFN layers
        self.key = nn.Linear(d_model, self.hidden, bias=False)
        self.receptance = nn.Linear(d_model, d_model, bias=False)
        self.value = nn.Linear(self.hidden, d_model, bias=False)

    def forward(self, x, x_prev):
        # Time-shift mixing
        xk = x * self.time_mix_k + x_prev * (1 - self.time_mix_k)
        xr = x * self.time_mix_r + x_prev * (1 - self.time_mix_r)

        # Channel mixing
        k = self.key(xk)
        k = torch.square(torch.relu(k))  # Squared ReLU activation
        kv = self.value(k)

        # Receptance gate
        r = torch.sigmoid(self.receptance(xr))

        return r * kv
```

## RWKV Block Structure

```python
class RWKV_Block(nn.Module):
    def __init__(self, d_model, n_layer):
        super().__init__()
        self.ln1 = nn.LayerNorm(d_model)
        self.ln2 = nn.LayerNorm(d_model)
        self.att = RWKV_TimeMix(d_model, n_layer)
        self.ffn = RWKV_ChannelMix(d_model)

    def forward(self, x, state):
        # Time-mixing with residual
        att_out, new_state = self.att(self.ln1(x), state)
        x = x + att_out

        # Channel-mixing with residual
        ffn_out = self.ffn(self.ln2(x), state[:, :, 2])  # Use x_prev from state
        x = x + ffn_out

        return x, new_state

# Full RWKV model
model = nn.Sequential(
    Embedding(...),
    *[RWKV_Block(d_model, i) for i in range(n_layers)],
    LayerNorm(d_model),
    LMHead(...)
)
```

## Time-Decay Mechanism

The **time_decay** parameter `w` controls how fast information decays:

```python
# Initialization (RWKV-4)
time_decay = torch.ones(n_layers, d_model)
for i in range(n_layers):
    for j in range(d_model):
        # Logarithmic spacing
        ratio = (i + 1) / n_layers
        time_decay[i, j] = -5.0 + 8.0 * ratio + 0.3 * (j / d_model)

# Effect on memory
w = -exp(time_decay)  # Range: [-exp(-5), -exp(3)] ≈ [-0.007, -20]
# Smaller w = slower decay = longer memory
# Larger w = faster decay = shorter memory
```

**Layer-wise decay pattern**:
- Early layers (shallow): Fast decay, capture local patterns
- Later layers (deep): Slow decay, capture long-range dependencies

## Receptance Gate

The **receptance** mechanism controls information flow:

```python
r = sigmoid(receptance(x))  # Range [0, 1]
output = r * wkv  # Gate the WKV output

# High receptance (r ≈ 1): Pass information through
# Low receptance (r ≈ 0): Block information
```

**Purpose**: Similar to LSTM forget gate, but learned per-token

## RWKV-4 vs RWKV-5 vs RWKV-6 vs RWKV-7

### RWKV-4 (Original)
```python
# Time-shift with previous token
xx = x * time_mix + x_prev * (1 - time_mix)
k, v, r = key(xx), value(xx), receptance(xx)
```

### RWKV-5 (2023)
```python
# Separate time-mix for k, v, r
xk = x * time_mix_k + x_prev * (1 - time_mix_k)
xv = x * time_mix_v + x_prev * (1 - time_mix_v)
xr = x * time_mix_r + x_prev * (1 - time_mix_r)
k, v, r = key(xk), value(xk), receptance(xr)
```

### RWKV-6 (2024)
- Added **multi-head time-mixing** (like multi-head attention)
- Separate time-decay per head
- Improved stability for large models

```python
# Per-head processing
for h in range(n_heads):
    k_h = key[h](x)  # Separate projection per head
    w_h = time_decay[h]  # Separate decay per head
    wkv_h = wkv(k_h, v_h, w_h)
output = concat(wkv_0, wkv_1, ..., wkv_H)
```

### RWKV-7 (March 2025)
- **Multimodal support** (vision + language)
- Improved numerical stability
- Better scaling to 14B+ parameters

## Numerical Stability

### Issue: Exponential Overflow

```python
# Problem: exp(wkv) can overflow
wkv = exp(u * kv) / exp(u * k)  # Can overflow!
```

### Solution: Log-space Computation

```python
# Stable implementation
log_wkv_num = u + log(kv) + log(aa)
log_wkv_den = u + log(k) + log(ab)
wkv = exp(log_wkv_num - log_wkv_den)  # Numerically stable
```

### Gradient Clipping

```python
# Recommended for training stability
torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
```

## State Management

### State Shape

```python
# For batch inference
state = torch.zeros(
    batch_size,
    n_layers,
    4,  # (att_aa, att_ab, att_x_prev, ffn_x_prev)
    d_model
)
```

### State Initialization

```python
# Zero initialization (standard)
state = None  # Model creates zero state

# Warm state (from previous conversation)
_, state = model.forward(previous_context, None)
# Use `state` for next turn
```

### State Serialization

```python
# Save conversation state
torch.save(state, 'conversation_state.pt')

# Resume conversation
state = torch.load('conversation_state.pt')
out, state = model.forward(new_tokens, state)
```

## Resources

- Paper (RWKV): https://arxiv.org/abs/2305.13048 (May 2023)
- Paper (RWKV-7): https://arxiv.org/abs/2503.14456 (March 2025)
- GitHub: https://github.com/BlinkDL/RWKV-LM
- Math derivation: https://wiki.rwkv.com/
- CUDA kernels: https://github.com/BlinkDL/RWKV-CUDA




### State Management

# RWKV State Management

## Understanding RWKV State

Unlike Transformers with KV cache, RWKV maintains a **fixed-size recurrent state** that summarizes all previous context.

### State Components

```python
state = {
    'att_aa': torch.zeros(n_layers, d_model),  # Attention numerator accumulator
    'att_ab': torch.zeros(n_layers, d_model),  # Attention denominator accumulator
    'att_x_prev': torch.zeros(n_layers, d_model),  # Previous x for time-mixing
    'ffn_x_prev': torch.zeros(n_layers, d_model)   # Previous x for channel-mixing
}
```

**Total state size**: `4 × n_layers × d_model` parameters

| Model | Layers | d_model | State Size |
|-------|--------|---------|------------|
| RWKV-169M | 12 | 768 | 37 KB |
| RWKV-430M | 24 | 1024 | 98 KB |
| RWKV-1.5B | 24 | 2048 | 196 KB |
| RWKV-3B | 32 | 2560 | 327 KB |
| RWKV-7B | 32 | 4096 | 524 KB |
| RWKV-14B | 40 | 5120 | 819 KB |

**Constant memory** regardless of context length!

## State Initialization

### Zero State (Default)

```python
from rwkv.model import RWKV

model = RWKV(model='/path/to/RWKV-4-Pile-1B5', strategy='cuda fp16')

# Start with zero state (no context)
state = None
out, state = model.forward(tokens, state)
```

### Warm State (Preloaded Context)

```python
# Load context once
context = "The capital of France is Paris. The capital of Germany is Berlin."
context_tokens = tokenizer.encode(context)

# Process context to build state
state = None
for token in context_tokens:
    _, state = model.forward([token], state)

# Now use warm state for queries
query = " The capital of Italy is"
query_tokens = tokenizer.encode(query)
out, state = model.forward(query_tokens, state)
# Model "remembers" Paris and Berlin examples!
```

### Shared State (Multi-turn Conversations)

```python
# Conversation with persistent state
state = None

# Turn 1
user1 = "My name is Alice."
tokens1 = tokenizer.encode(user1)
_, state = model.forward(tokens1, state)

# Turn 2
user2 = "What is my name?"
tokens2 = tokenizer.encode(user2)
response, state = model.forward(tokens2, state)
# Response: "Alice" (state remembers!)
```

## State Update Rules

### Time-Mixing State Update

```python
# Before processing token t
att_aa_t = att_aa_{t-1}  # Previous numerator
att_ab_t = att_ab_{t-1}  # Previous denominator

# Compute WKV
wkv_t = (exp(u) * k_t * v_t + att_aa_t) / (exp(u) * k_t + att_ab_t)

# Update state for token t+1
w = -exp(time_decay)  # Decay factor
att_aa_{t+1} = exp(w) * att_aa_t + k_t * v_t
att_ab_{t+1} = exp(w) * att_ab_t + k_t
att_x_prev_{t+1} = x_t
```

**Effect of time_decay**:
- **w = -0.01** (small decay): State decays slowly → long memory
- **w = -5.0** (large decay): State decays quickly → short memory

### Channel-Mixing State Update

```python
# Simply store previous x for next token
ffn_x_prev_{t+1} = x_t
```

## State Serialization

### Save/Load State (PyTorch)

```python
import torch

# Save conversation state
state_dict = {
    'att_aa': state[0],
    'att_ab': state[1],
    'att_x_prev': state[2],
    'ffn_x_prev': state[3]
}
torch.save(state_dict, 'conversation_123.pt')

# Load state
loaded = torch.load('conversation_123.pt')
state = (loaded['att_aa'], loaded['att_ab'], loaded['att_x_prev'], loaded['ffn_x_prev'])

# Continue conversation
out, state = model.forward(new_tokens, state)
```

### State Compression (Optional)

```python
# FP16 state (half size)
state_fp16 = tuple(s.half() for s in state)
torch.save(state_fp16, 'state_compressed.pt')

# Restore
state = tuple(s.float() for s in torch.load('state_compressed.pt'))
```

## Multi-Session State Management

### Session State Store

```python
class StateManager:
    def __init__(self):
        self.sessions = {}  # session_id -> state

    def get_state(self, session_id):
        return self.sessions.get(session_id, None)

    def save_state(self, session_id, state):
        self.sessions[session_id] = state

    def clear_session(self, session_id):
        if session_id in self.sessions:
            del self.sessions[session_id]

# Usage
manager = StateManager()

# User 1 conversation
state1 = manager.get_state('user_1')
out1, state1 = model.forward(tokens1, state1)
manager.save_state('user_1', state1)

# User 2 conversation (independent state)
state2 = manager.get_state('user_2')
out2, state2 = model.forward(tokens2, state2)
manager.save_state('user_2', state2)
```

### State Expiration

```python
import time

class StateManagerWithExpiry:
    def __init__(self, expiry_seconds=3600):
        self.sessions = {}  # session_id -> (state, timestamp)
        self.expiry = expiry_seconds

    def get_state(self, session_id):
        if session_id in self.sessions:
            state, timestamp = self.sessions[session_id]
            if time.time() - timestamp < self.expiry:
                return state
            else:
                del self.sessions[session_id]  # Expired
        return None

    def save_state(self, session_id, state):
        self.sessions[session_id] = (state, time.time())
```

## State Interpolation

### Blending States

```python
# Average two states (e.g., merging conversations)
def blend_states(state1, state2, alpha=0.5):
    """Blend state1 and state2 with weight alpha."""
    return tuple(
        alpha * s1 + (1 - alpha) * s2
        for s1, s2 in zip(state1, state2)
    )

# Example: Blend Alice and Bob conversation contexts
state_blended = blend_states(state_alice, state_bob, alpha=0.7)
# 70% Alice context, 30% Bob context
```

### State Editing

```python
# Manually edit state (advanced)
# Example: Reduce long-term memory influence

def decay_state(state, decay_factor=0.5):
    """Reduce state magnitude (forget older context)."""
    att_aa, att_ab, att_x_prev, ffn_x_prev = state
    return (
        att_aa * decay_factor,
        att_ab * decay_factor,
        att_x_prev,  # Keep recent x
        ffn_x_prev   # Keep recent x
    )

# Usage
state = decay_state(state, decay_factor=0.3)  # Forget 70% of history
```

## Batch Inference with States

### Independent Batch States

```python
# Each sequence in batch has separate state
batch_size = 4
states = [None] * batch_size

for i, tokens in enumerate(batch_sequences):
    out, states[i] = model.forward(tokens, states[i])
```

### Shared Prefix Optimization

```python
# All sequences share common prefix (e.g., system prompt)
prefix = "You are a helpful assistant."
prefix_tokens = tokenizer.encode(prefix)

# Compute prefix state once
prefix_state = None
_, prefix_state = model.forward(prefix_tokens, None)

# Clone prefix state for each sequence
states = [prefix_state] * batch_size

# Process user queries (independent)
for i, user_query in enumerate(user_queries):
    tokens = tokenizer.encode(user_query)
    out, states[i] = model.forward(tokens, states[i])
```

## State Debugging

### Inspect State Magnitudes

```python
def inspect_state(state):
    """Print state statistics for debugging."""
    att_aa, att_ab, att_x_prev, ffn_x_prev = state

    print("State magnitudes:")
    print(f"  att_aa: mean={att_aa.abs().mean():.4f}, max={att_aa.abs().max():.4f}")
    print(f"  att_ab: mean={att_ab.abs().mean():.4f}, max={att_ab.abs().max():.4f}")
    print(f"  att_x_prev: mean={att_x_prev.abs().mean():.4f}, max={att_x_prev.abs().max():.4f}")
    print(f"  ffn_x_prev: mean={ffn_x_prev.abs().mean():.4f}, max={ffn_x_prev.abs().max():.4f}")

# Usage
inspect_state(state)
```

**Healthy ranges**:
- `att_aa`, `att_ab`: 0.1 - 10.0 (if much larger, may overflow)
- `att_x_prev`, `ffn_x_prev`: Similar to input embedding magnitude

### State Divergence Check

```python
def state_distance(state1, state2):
    """Compute L2 distance between two states."""
    return sum(
        torch.dist(s1, s2).item()
        for s1, s2 in zip(state1, state2)
    )

# Example: Check if states diverged
distance = state_distance(state_alice, state_bob)
print(f"State distance: {distance:.2f}")
# Large distance → very different contexts
```

## Numerical Stability Considerations

### Overflow Prevention

```python
# Issue: att_aa, att_ab can grow unbounded
# If att_aa > 1e10, numerical precision issues

# Solution 1: Periodic normalization
if att_aa.abs().max() > 1e6:
    scale = att_aa.abs().max()
    att_aa = att_aa / scale
    att_ab = att_ab / scale
```

### Underflow Prevention

```python
# Issue: With large negative time_decay, state can underflow to 0

# Solution: Clip time_decay
time_decay = torch.clamp(time_decay, min=-8.0, max=-0.1)
# Ensures state doesn't decay too fast
```

## State vs KV Cache Comparison

### Memory Usage (8K context)

| Model Type | Model Size | KV Cache Size | RWKV State Size |
|------------|------------|---------------|-----------------|
| Transformer | 1.3B | 4.1 GB | - |
| **RWKV** | **1.5B** | **-** | **196 KB** |
| Transformer | 7B | 21.3 GB | - |
| **RWKV** | **7B** | **-** | **524 KB** |

**RWKV advantage**: 10,000× smaller than KV cache!

### Information Retention

**KV Cache (Transformer)**:
- Perfect: Stores all previous keys and values
- Retrieval: Exact attention to any previous token
- Cost: O(n) memory growth

**RWKV State**:
- Lossy: Compressed representation of history
- Retrieval: Weighted blend of previous tokens (decay-based)
- Cost: O(1) constant memory

**Trade-off**: RWKV sacrifices perfect recall for constant memory

## Resources

- State management examples: https://github.com/BlinkDL/ChatRWKV
- Wiki: https://wiki.rwkv.com/state-management
- Discord: https://discord.gg/bDSBUMeFpc (RWKV community)




### Rwkv7

# RWKV-7: Latest Improvements (March 2025)

## Overview

RWKV-7 is the latest version released in March 2025, introducing multimodal capabilities and improved scaling to 14B+ parameters.

**Paper**: https://arxiv.org/abs/2503.14456 (March 2025)

## Key Improvements Over RWKV-6

### 1. Enhanced Numerical Stability

**Problem in RWKV-6**:
```python
# Exponential operations could overflow for large models
att_aa = exp(w) * att_aa + k * v  # Overflow risk!
```

**RWKV-7 Solution**:
```python
# Log-space computation with safe exponentiation
log_att_aa = log_softmax([log(k * v), log_w + log(att_aa)])
att_aa = exp(log_att_aa)
```

**Result**: Stable training up to 14B parameters (RWKV-6 struggled beyond 7B)

### 2. Improved Time-Decay Initialization

**RWKV-6**:
```python
# Simple logarithmic spacing
time_decay[i] = -5.0 + 8.0 * (i / n_layers)
```

**RWKV-7**:
```python
# Adaptive per-head decay with better range
for layer in range(n_layers):
    for head in range(n_heads):
        # Different heads specialize in different timescales
        alpha = (layer / n_layers) ** 0.7  # Non-linear progression
        beta = (head / n_heads) * 0.5
        time_decay[layer, head] = -6.0 + 9.0 * alpha + beta

# Result: Better long/short-term memory balance
```

**Impact**: 15-20% perplexity improvement on long-context tasks

### 3. Multi-Head Time-Mixing Refinements

**RWKV-6 Multi-Head**:
```python
# Simple concatenation
heads = [head_i(x) for head_i in heads]
output = concat(heads)
```

**RWKV-7 Multi-Head**:
```python
# Attention-style output projection
heads = [head_i(x) for head_i in heads]
concat_heads = concat(heads)
output = output_proj(concat_heads)  # Learnable mixing

# Plus: Per-head layer norm
for i, head in enumerate(heads):
    heads[i] = head_norm[i](head)  # Separate norm per head
```

**Result**: Better head specialization, 8-12% quality improvement

### 4. Rotary Position Encoding (RoPE) Integration

**New in RWKV-7**:
```python
class RWKV7_TimeMix(nn.Module):
    def __init__(self, d_model, n_heads):
        super().__init__()
        self.rope = RotaryEmbedding(d_model // n_heads)

    def forward(self, x):
        k = self.key(x)  # (B, T, d_model)
        v = self.value(x)

        # Apply RoPE to keys
        k = self.rope.rotate_queries_or_keys(k)

        # WKV with position-aware keys
        wkv = self.wkv(k, v)
        return wkv
```

**Why useful**: Improves positional awareness without breaking O(n) complexity

### 5. RWKV-7 Block Structure

```python
class RWKV7_Block(nn.Module):
    def __init__(self, d_model, n_heads):
        super().__init__()
        self.ln1 = nn.LayerNorm(d_model)
        self.ln2 = nn.LayerNorm(d_model)

        # Multi-head time-mixing with RoPE
        self.att = RWKV7_MultiHeadTimeMix(d_model, n_heads)

        # Enhanced channel-mixing
        self.ffn = RWKV7_ChannelMix(d_model, hidden_ratio=3.5)  # Larger FFN

    def forward(self, x, state):
        # Pre-norm (like GPT)
        att_out, new_state = self.att(self.ln1(x), state)
        x = x + att_out

        # FFN with gating
        ffn_out = self.ffn(self.ln2(x))
        x = x + ffn_out

        return x, new_state
```

## Multimodal Capabilities

### Vision Encoder Integration

**Architecture**:
```python
class RWKV7_Multimodal(nn.Module):
    def __init__(self):
        super().__init__()
        # Vision encoder (CLIP-style)
        self.vision_encoder = VisionTransformer(
            patch_size=14,
            d_model=1024,
            n_layers=24
        )

        # Projection to RWKV space
        self.vision_proj = nn.Linear(1024, d_model)

        # RWKV language model
        self.rwkv = RWKV7_LanguageModel(d_model=2560, n_layers=40)

    def forward(self, image, text, state=None):
        # Encode image to patches
        vision_tokens = self.vision_encoder(image)  # (B, 256, 1024)
        vision_tokens = self.vision_proj(vision_tokens)  # (B, 256, 2560)

        # Concatenate vision and text tokens
        combined = torch.cat([vision_tokens, text], dim=1)

        # Process with RWKV
        out, state = self.rwkv(combined, state)

        return out, state
```

### Vision-Language Tasks

**Image Captioning**:
```python
model = RWKV7_Multimodal()

# Encode image
image = load_image('cat.jpg')
vision_tokens = model.vision_encoder(image)

# Generate caption
state = None
_, state = model.rwkv(vision_tokens, state)  # Process image

# Autoregressive caption generation
caption = []
for _ in range(max_length):
    logits, state = model.rwkv(prev_token, state)
    next_token = sample(logits)
    caption.append(next_token)
```

**VQA (Visual Question Answering)**:
```python
# Question: "What color is the cat?"
question_tokens = tokenizer.encode("What color is the cat?")

# Process image + question
combined = torch.cat([vision_tokens, question_tokens], dim=1)
answer_logits, state = model.rwkv(combined, state)

# Answer: "orange"
```

### Training Multimodal RWKV-7

```python
# Pretrain vision encoder (CLIP-style)
train_vision_encoder(image_text_pairs)

# Freeze vision encoder
model.vision_encoder.requires_grad_(False)

# Train projection + RWKV
for batch in multimodal_dataloader:
    images, captions = batch

    # Forward
    vision_tokens = model.vision_encoder(images)
    vision_tokens = model.vision_proj(vision_tokens)

    logits, _ = model.rwkv(
        torch.cat([vision_tokens, captions[:, :-1]], dim=1),
        state=None
    )

    # Loss (next token prediction)
    loss = F.cross_entropy(
        logits[:, vision_tokens.shape[1]:].reshape(-1, vocab_size),
        captions.reshape(-1)
    )

    loss.backward()
    optimizer.step()
```

## Scaling to 14B Parameters

### Model Configuration

| Model | Layers | d_model | n_heads | Params | Context | VRAM (FP16) |
|-------|--------|---------|---------|--------|---------|-------------|
| RWKV-7-1.5B | 24 | 2048 | 16 | 1.5B | Infinite | 3 GB |
| RWKV-7-3B | 32 | 2560 | 20 | 3B | Infinite | 6 GB |
| RWKV-7-7B | 32 | 4096 | 32 | 7B | Infinite | 14 GB |
| RWKV-7-14B | 40 | 5120 | 40 | 14B | Infinite | 28 GB |

### Training Efficiency Improvements

**RWKV-6 Training (7B)**:
- Speed: 45K tokens/sec (8× A100)
- Memory: 38 GB per GPU (4K sequence)
- Stability: Occasional loss spikes

**RWKV-7 Training (14B)**:
- Speed: 52K tokens/sec (8× A100) - **15% faster**
- Memory: 42 GB per GPU (4K sequence) - **Better utilization**
- Stability: No loss spikes - **Improved stability**

**Key optimization**: Fused CUDA kernels for multi-head WKV

### RWKV-7 vs GPT-3 (14B)

| Metric | RWKV-7-14B | GPT-3-13B | Advantage |
|--------|------------|-----------|-----------|
| Training Speed | 52K tok/s | 28K tok/s | 1.9× |
| Inference (2K ctx) | 6,100 tok/s | 1,800 tok/s | 3.4× |
| Inference (8K ctx) | 5,800 tok/s | 450 tok/s | **12.9×** |
| Memory (inference) | 28 GB | 52 GB | 1.9× |
| Perplexity (Pile) | 6.8 | 7.2 | +6% |

## Production Use Cases

### Microsoft Integration

**Windows Copilot** (Limited Release):
- Uses RWKV-7-3B for on-device inference
- 5-8× faster than GPT-2 with better quality
- Constant memory for infinite context

**Office 365** (Experimental):
- Document summarization with RWKV-7-7B
- Handles 100K+ token documents efficiently
- No KV cache storage needed

### NVIDIA NeMo Support

**NeMo Guardrails with RWKV-7**:
```python
from nemoguardrails import RailsConfig
from nemoguardrails.llm.providers import register_llm_provider

# Register RWKV-7 as LLM backend
register_llm_provider("rwkv7", RWKV7Provider)

config = RailsConfig.from_path("config/")
rails = LLMRails(config, llm_provider="rwkv7")

# Use for content moderation
response = rails.generate(user_input="...")
```

## Benchmarks (RWKV-7 vs RWKV-6)

### Language Modeling

| Dataset | RWKV-6-7B | RWKV-7-7B | Improvement |
|---------|-----------|-----------|-------------|
| Pile (val) | 7.8 | 7.1 | +9% |
| C4 | 9.3 | 8.6 | +8% |
| WikiText-103 | 8.4 | 7.7 | +8% |
| Lambada | 11.2 | 9.8 | +13% |

### Long-Context Tasks (32K context)

| Task | RWKV-6-7B | RWKV-7-7B | Improvement |
|------|-----------|-----------|-------------|
| QuALITY | 52.3 | 61.8 | +18% |
| Qasper | 38.1 | 46.7 | +23% |
| NarrativeQA | 41.2 | 49.5 | +20% |

**RWKV-7's improved time-decay** significantly helps long-context understanding

### Multimodal Benchmarks

| Task | RWKV-7-7B | LLaVA-7B | BLIP-2-7B |
|------|-----------|----------|-----------|
| VQAv2 | 74.2 | 78.5 | 82.1 |
| GQA | 58.3 | 62.1 | 65.4 |
| TextVQA | 51.2 | 58.2 | 60.8 |
| COCO Caption | 118.3 | 125.7 | 132.4 |

**Note**: RWKV-7 competitive but not SOTA on vision (vision-focused models still better)

## Migration from RWKV-6 to RWKV-7

### Model Conversion

```python
# Load RWKV-6 checkpoint
rwkv6_state = torch.load('rwkv6-7b.pth')

# Initialize RWKV-7 model
rwkv7_model = RWKV7_Model(d_model=4096, n_layers=32, n_heads=32)

# Convert weights (mostly compatible)
for key in rwkv6_state:
    if 'time_mixing' in key:
        # RWKV-7 uses multi-head, need to split
        rwkv7_key = convert_key_to_multihead(key)
        rwkv7_model.state_dict()[rwkv7_key].copy_(rwkv6_state[key])
    else:
        # Direct copy
        rwkv7_model.state_dict()[key].copy_(rwkv6_state[key])

# Fine-tune on small dataset to adapt
finetune(rwkv7_model, small_dataset, epochs=1)
```

### State Compatibility

**RWKV-6 State**:
```python
state_v6 = (att_aa, att_ab, att_x_prev, ffn_x_prev)  # 4 components
```

**RWKV-7 State** (Multi-head):
```python
state_v7 = (
    att_aa_heads,  # (n_heads, d_model//n_heads)
    att_ab_heads,  # (n_heads, d_model//n_heads)
    att_x_prev,
    ffn_x_prev
)  # 4 components, but att_* are multi-head
```

**Conversion**:
```python
# Split RWKV-6 state into RWKV-7 multi-head state
def convert_state_v6_to_v7(state_v6, n_heads):
    att_aa, att_ab, att_x_prev, ffn_x_prev = state_v6
    d_head = att_aa.shape[-1] // n_heads

    att_aa_heads = att_aa.view(-1, n_heads, d_head).transpose(0, 1)
    att_ab_heads = att_ab.view(-1, n_heads, d_head).transpose(0, 1)

    return (att_aa_heads, att_ab_heads, att_x_prev, ffn_x_prev)
```

## Resources

- **Paper**: https://arxiv.org/abs/2503.14456 (RWKV-7, March 2025)
- **GitHub**: https://github.com/BlinkDL/RWKV-LM (v7 branch)
- **Models**: https://huggingface.co/BlinkDL/rwkv-7-world
- **Multimodal Demo**: https://huggingface.co/spaces/BlinkDL/RWKV-7-Multimodal
- **Discord**: https://discord.gg/bDSBUMeFpc
- **Wiki**: https://wiki.rwkv.com/rwkv7




---

## 🚀 Usage

**Reference this template:** `@skill-rwkv-architecture.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
