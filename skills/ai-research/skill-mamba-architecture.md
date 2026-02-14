---
id: skill-mamba-architecture
type: skill
name: mamba-architecture
description: State-space model with O(n) complexity vs Transformers' O(n²). 5× faster
  inference, million-token sequences, no KV cache. Selective SSM with hardware-aware
  design. Mamba-1 (d_state=16) and Mamba-2 (d_state=128, multi-head). Models 130M-2.8B
  on HuggingFace.
category: ai-research
complexity: medium
keywords:
- benchmark
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 962
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~962 -->
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




# mamba-architecture

> State-space model with O(n) complexity vs Transformers' O(n²). 5× faster inference, million-token sequences, no KV cache. Selective SSM with hardware-aware design. Mamba-1 (d_state=16) and Mamba-2 (d_state=128, multi-head). Models 130M-2.8B on HuggingFace.

# Mamba - Selective State Space Models

## Quick start

Mamba is a state-space model architecture achieving O(n) linear complexity for sequence modeling.

**Installation**:
```bash
# Install causal-conv1d (optional, for efficiency)
pip install causal-conv1d>=1.4.0

# Install Mamba
pip install mamba-ssm
# Or both together
pip install mamba-ssm[causal-conv1d]
```

**Prerequisites**: Linux, NVIDIA GPU, PyTorch 1.12+, CUDA 11.6+

**Basic usage** (Mamba block):
```python
import torch
from mamba_ssm import Mamba

batch, length, dim = 2, 64, 16
x = torch.randn(batch, length, dim).to("cuda")

model = Mamba(
    d_model=dim,      # Model dimension
    d_state=16,       # SSM state dimension
    d_conv=4,         # Conv1d kernel size
    expand=2          # Expansion factor
).to("cuda")

y = model(x)  # O(n) complexity!
assert y.shape == x.shape
```

## Common workflows

### Workflow 1: Language model with Mamba-2

**Complete LM with generation**:
```python
from mamba_ssm.models.mixer_seq_simple import MambaLMHeadModel
from mamba_ssm.models.config_mamba import MambaConfig
import torch

# Configure Mamba-2 LM
config = MambaConfig(
    d_model=1024,           # Hidden dimension
    n_layer=24,             # Number of layers
    vocab_size=50277,       # Vocabulary size
    ssm_cfg=dict(
        layer="Mamba2",     # Use Mamba-2
        d_state=128,        # Larger state for Mamba-2
        headdim=64,         # Head dimension
        ngroups=1           # Number of groups
    )
)

model = MambaLMHeadModel(config, device="cuda", dtype=torch.float16)

# Generate text
input_ids = torch.randint(0, 1000, (1, 20), device="cuda", dtype=torch.long)
output = model.generate(
    input_ids=input_ids,
    max_length=100,
    temperature=0.7,
    top_p=0.9
)
```

### Workflow 2: Use pretrained Mamba models

**Load from HuggingFace**:
```python
from transformers import AutoTokenizer
from mamba_ssm.models.mixer_seq_simple import MambaLMHeadModel

# Load pretrained model
model_name = "state-spaces/mamba-2.8b"
tokenizer = AutoTokenizer.from_pretrained("EleutherAI/gpt-neox-20b")  # Use compatible tokenizer
model = MambaLMHeadModel.from_pretrained(model_name, device="cuda", dtype=torch.float16)

# Generate
prompt = "The future of AI is"
input_ids = tokenizer(prompt, return_tensors="pt").input_ids.to("cuda")
output_ids = model.generate(
    input_ids=input_ids,
    max_length=200,
    temperature=0.7,
    top_p=0.9,
    repetition_penalty=1.2
)
generated_text = tokenizer.decode(output_ids[0])
print(generated_text)
```

**Available models**:
- `state-spaces/mamba-130m`
- `state-spaces/mamba-370m`
- `state-spaces/mamba-790m`
- `state-spaces/mamba-1.4b`
- `state-spaces/mamba-2.8b`

### Workflow 3: Mamba-1 vs Mamba-2

**Mamba-1** (smaller state):
```python
from mamba_ssm import Mamba

model = Mamba(
    d_model=256,
    d_state=16,      # Smaller state dimension
    d_conv=4,
    expand=2
).to("cuda")
```

**Mamba-2** (multi-head, larger state):
```python
from mamba_ssm import Mamba2

model = Mamba2(
    d_model=256,
    d_state=128,     # Larger state dimension
    d_conv=4,
    expand=2,
    headdim=64,      # Head dimension for multi-head
    ngroups=1        # Parallel groups
).to("cuda")
```

**Key differences**:
- **State size**: Mamba-1 (d_state=16) vs Mamba-2 (d_state=128)
- **Architecture**: Mamba-2 has multi-head structure
- **Normalization**: Mamba-2 uses RMSNorm
- **Distributed**: Mamba-2 supports tensor parallelism

### Workflow 4: Benchmark vs Transformers

**Generation speed comparison**:
```bash
# Benchmark Mamba
python benchmarks/benchmark_generation_mamba_simple.py \
  --model-name "state-spaces/mamba-2.8b" \
  --prompt "The future of machine learning is" \
  --topp 0.9 --temperature 0.7 --repetition-penalty 1.2

# Benchmark Transformer
python benchmarks/benchmark_generation_mamba_simple.py \
  --model-name "EleutherAI/pythia-2.8b" \
  --prompt "The future of machine learning is" \
  --topp 0.9 --temperature 0.7 --repetition-penalty 1.2
```

**Expected results**:
- **Mamba**: 5× faster inference
- **Memory**: No KV cache needed
- **Scaling**: Linear with sequence length

## When to use vs alternatives

**Use Mamba when**:
- Need long sequences (100K+ tokens)
- Want faster inference than Transformers
- Memory-constrained (no KV cache)
- Building streaming applications
- Linear scaling important

**Advantages**:
- **O(n) complexity**: Linear vs quadratic
- **5× faster inference**: No attention overhead
- **No KV cache**: Lower memory usage
- **Million-token sequences**: Hardware-efficient
- **Streaming**: Constant memory per token

**Use alternatives instead**:
- **Transformers**: Need best-in-class performance, have compute
- **RWKV**: Want RNN+Transformer hybrid
- **RetNet**: Need retention-based architecture
- **Hyena**: Want convolution-based approach

## Common issues

**Issue: CUDA out of memory**

Reduce batch size or use gradient checkpointing:
```python
model = MambaLMHeadModel(config, device="cuda", dtype=torch.float16)
model.gradient_checkpointing_enable()  # Enable checkpointing
```

**Issue: Slow installation**

Install binary wheels (not source):
```bash
pip install mamba-ssm --no-build-isolation
```

**Issue: Missing causal-conv1d**

Install separately:
```bash
pip install causal-conv1d>=1.4.0
```

**Issue: Model not loading from HuggingFace**

Use `MambaLMHeadModel.from_pretrained` (not `AutoModel`):
```python
from mamba_ssm.models.mixer_seq_simple import MambaLMHeadModel
model = MambaLMHeadModel.from_pretrained("state-spaces/mamba-2.8b")
```

## Advanced topics

**Selective SSM**: See [references/selective-ssm.md](references/selective-ssm.md) for mathematical formulation, state-space equations, and how selectivity enables O(n) complexity.

**Mamba-2 architecture**: See [references/mamba2-details.md](references/mamba2-details.md) for multi-head structure, tensor parallelism, and distributed training setup.

**Performance optimization**: See [references/performance.md](references/performance.md) for hardware-aware design, CUDA kernels, and memory efficiency techniques.

## Hardware requirements

- **GPU**: NVIDIA with CUDA 11.6+
- **VRAM**:
  - 130M model: 2GB
  - 370M model: 4GB
  - 790M model: 8GB
  - 1.4B model: 14GB
  - 2.8B model: 28GB (FP16)
- **Inference**: 5× faster than Transformers
- **Memory**: No KV cache (lower than Transformers)

**Performance** (vs Transformers):
- **Speed**: 5× faster inference
- **Memory**: 50% less (no KV cache)
- **Scaling**: Linear vs quadratic

## Resources

- Paper (Mamba-1): https://arxiv.org/abs/2312.00752 (Dec 2023)
- Paper (Mamba-2): https://arxiv.org/abs/2405.21060 (May 2024)
- GitHub: https://github.com/state-spaces/mamba ⭐ 13,000+
- Models: https://huggingface.co/state-spaces
- Docs: Repository README and wiki




---


## 📚 Reference Materials


### Architecture Details

# Mamba Architecture Details

## Selective State Space Mechanism

Mamba's core innovation is the **Selective SSM (S6)** layer that makes state space model parameters input-dependent.

### How S6 Works

**Traditional SSMs** (non-selective):
```python
# Fixed A, B, C matrices for all inputs
h(t) = A * h(t-1) + B * x(t)  # State update
y(t) = C * h(t)                # Output
```

**Mamba's Selective SSM**:
```python
# Input-dependent parameters
B(t) = Linear_B(x(t))  # Selection mechanism
C(t) = Linear_C(x(t))  # Output projection
Δ(t) = Linear_Δ(x(t))  # Discretization step

# Selective state update
h(t) = discretize(A, Δ(t)) * h(t-1) + Δ(t) * B(t) * x(t)
y(t) = C(t) * h(t)
```

### Key Advantages

**1. Content-based reasoning**:
- Can selectively remember or forget based on input
- Addresses discrete modality weakness of traditional SSMs
- Example: Remembers important tokens, forgets padding

**2. Input-dependent selection**:
```python
# Mamba decides per token what to remember
if is_important(x(t)):
    Δ(t) = large_value   # Keep in state
else:
    Δ(t) = small_value   # Forget quickly
```

**3. No attention required**:
- Replaces O(n²) attention with O(n) state updates
- State dimension is constant (typically 16)

## Model Configuration

### Core Parameters

```python
from mamba_ssm import Mamba

model = Mamba(
    d_model=256,      # Hidden dimension (256, 512, 768, 1024, 2048)
    d_state=16,       # SSM state dimension (fixed at 16 is optimal)
    d_conv=4,         # Local convolution width (4 is standard)
    expand=2,         # Expansion factor (1.5-2.0)
    dt_rank="auto",   # Rank of Δ projection (auto = d_model / 16)
    dt_min=0.001,     # Min Δ init (controls forgetting rate)
    dt_max=0.1,       # Max Δ init
    dt_init="random", # Δ initialization (random, constant)
    dt_scale=1.0,     # Δ scaling factor
    conv_bias=True,   # Use bias in convolution
    bias=False        # Use bias in linear projections
)
```

### Parameter Impact

**d_state** (SSM state dimension):
- Standard: 16 (optimal from ablations)
- Smaller (8): Faster but less capacity
- Larger (32, 64): Minimal improvement, 2× slower

**expand** (block expansion):
- Standard: 2.0
- Range: 1.5-2.0
- Controls inner dimension = expand * d_model

**d_conv** (convolution width):
- Standard: 4
- Local context window before SSM
- Helps with positional information

**dt_rank** (Δ projection rank):
- Auto: d_model / 16 (recommended)
- Controls Δ parameter efficiency
- Lower rank = more efficient but less expressive

## Mamba Block Structure

```python
# Mamba block (replaces Transformer block)
class MambaBlock(nn.Module):
    def __init__(self, d_model):
        self.norm = RMSNorm(d_model)
        self.mamba = Mamba(d_model, d_state=16, d_conv=4, expand=2)

    def forward(self, x):
        return x + self.mamba(self.norm(x))  # Residual

# Full model (stack of Mamba blocks)
model = nn.Sequential(
    Embedding(...),
    *[MambaBlock(d_model) for _ in range(n_layers)],
    RMSNorm(d_model),
    LMHead(...)
)
```

**Key differences from Transformers**:
- No multi-head attention (MHA)
- No feedforward network (FFN)
- Single Mamba layer per block
- 2× more layers than equivalent Transformer

## Hardware-Aware Implementation

### Parallel Algorithm

Mamba uses a **scan-based parallel algorithm** for training:

```python
# Parallel mode (training)
# GPU kernel fuses operations
y = parallel_scan(A, B, C, x)  # O(n log n) parallel

# Sequential mode (inference)
# Constant memory RNN-style
h = 0
for x_t in sequence:
    h = A*h + B*x_t
    y_t = C*h
```

### Memory Efficiency

**Training**:
- Recomputes activations in backward pass
- Similar to FlashAttention strategy
- Memory: O(batch_size * seq_len * d_model)

**Inference**:
- RNN-style sequential processing
- State size: O(d_model * d_state) = constant
- No KV cache needed (huge advantage!)

### CUDA Kernel Optimizations

```python
# Fused kernel operations
- Discretization (continuous → discrete A, B)
- SSM recurrence (parallel scan)
- Convolution (efficient 1D conv)
- All in single GPU kernel
```

## Layer Count Scaling

Mamba models use **2× layers** compared to Transformers:

| Model | d_model | n_layers | Params |
|-------|---------|----------|--------|
| Mamba-130M | 768 | 24 | 130M |
| Mamba-370M | 1024 | 48 | 370M |
| Mamba-790M | 1536 | 48 | 790M |
| Mamba-1.4B | 2048 | 48 | 1.4B |
| Mamba-2.8B | 2560 | 64 | 2.8B |

**Why 2× layers?**
- Mamba blocks are simpler (no MHA, no FFN)
- ~50% fewer parameters per layer
- Doubling layers matches compute budget

## Initialization Strategy

```python
# Δ (discretization step) initialization
dt_init_floor = 1e-4
dt = torch.exp(
    torch.rand(d_inner) * (math.log(dt_max) - math.log(dt_min))
    + math.log(dt_min)
).clamp(min=dt_init_floor)

# A (state transition) initialization
A = -torch.exp(torch.rand(d_inner, d_state))  # Negative for stability

# B, C (input/output) initialization
B = torch.randn(d_inner, d_state)
C = torch.randn(d_inner, d_state)
```

**Critical for stability**:
- A must be negative (exponential decay)
- Δ in range [dt_min, dt_max]
- Random initialization helps diversity

## Resources

- Paper: https://arxiv.org/abs/2312.00752 (Mamba-1)
- Paper: https://arxiv.org/abs/2405.21060 (Mamba-2)
- GitHub: https://github.com/state-spaces/mamba
- Models: https://huggingface.co/state-spaces
- CUDA kernels: https://github.com/state-spaces/mamba/tree/main/csrc




### Training Guide

# Mamba Training Guide

## Training from Scratch

### Setup Environment

```bash
# Install dependencies
pip install torch>=1.12.0 --extra-index-url https://download.pytorch.org/whl/cu116
pip install packaging ninja
pip install causal-conv1d>=1.1.0
pip install mamba-ssm

# Verify CUDA
python -c "import torch; print(torch.cuda.is_available())"
```

### Basic Training Loop

```python
import torch
from mamba_ssm import Mamba
from torch.utils.data import DataLoader

# Model setup
model = Mamba(
    d_model=512,
    d_state=16,
    d_conv=4,
    expand=2
).cuda()

# Optimizer (same as GPT)
optimizer = torch.optim.AdamW(
    model.parameters(),
    lr=6e-4,
    betas=(0.9, 0.95),
    weight_decay=0.1
)

# Training loop
for batch in dataloader:
    inputs, targets = batch
    inputs, targets = inputs.cuda(), targets.cuda()

    # Forward
    logits = model(inputs)
    loss = F.cross_entropy(logits.view(-1, vocab_size), targets.view(-1))

    # Backward
    optimizer.zero_grad()
    loss.backward()
    torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
    optimizer.step()
```

## Distributed Training

### Single-Node Multi-GPU (DDP)

```python
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP

# Initialize process group
dist.init_process_group("nccl")
local_rank = int(os.environ["LOCAL_RANK"])
torch.cuda.set_device(local_rank)

# Wrap model
model = Mamba(...).cuda()
model = DDP(model, device_ids=[local_rank])

# Train
optimizer = torch.optim.AdamW(model.parameters(), lr=6e-4)
for batch in dataloader:
    loss = compute_loss(model, batch)
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
```

**Launch**:
```bash
torchrun --nproc_per_node=8 train.py
```

### Multi-Node Training

```bash
# Node 0 (master)
torchrun --nproc_per_node=8 \
  --nnodes=4 --node_rank=0 \
  --master_addr=$MASTER_ADDR --master_port=29500 \
  train.py

# Node 1-3 (workers)
torchrun --nproc_per_node=8 \
  --nnodes=4 --node_rank=$NODE_RANK \
  --master_addr=$MASTER_ADDR --master_port=29500 \
  train.py
```

## Mixed Precision Training

### BF16 (Recommended)

```python
from torch.cuda.amp import autocast, GradScaler

# BF16 (no scaler needed on A100/H100)
for batch in dataloader:
    with autocast(dtype=torch.bfloat16):
        logits = model(inputs)
        loss = F.cross_entropy(logits.view(-1, vocab_size), targets.view(-1))

    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
```

### FP16 (with gradient scaling)

```python
scaler = GradScaler()

for batch in dataloader:
    with autocast(dtype=torch.float16):
        logits = model(inputs)
        loss = F.cross_entropy(logits.view(-1, vocab_size), targets.view(-1))

    optimizer.zero_grad()
    scaler.scale(loss).backward()
    scaler.unscale_(optimizer)
    torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
    scaler.step(optimizer)
    scaler.update()
```

## Hyperparameter Recommendations

### Learning Rate Schedule

```python
# Cosine decay with warmup (GPT-3 style)
def get_lr(it, warmup_iters=2000, lr_decay_iters=600000):
    max_lr = 6e-4
    min_lr = 6e-5

    # Warmup
    if it < warmup_iters:
        return max_lr * it / warmup_iters

    # Decay
    if it > lr_decay_iters:
        return min_lr

    # Cosine
    decay_ratio = (it - warmup_iters) / (lr_decay_iters - warmup_iters)
    coeff = 0.5 * (1.0 + math.cos(math.pi * decay_ratio))
    return min_lr + coeff * (max_lr - min_lr)

# Apply in training loop
for it, batch in enumerate(dataloader):
    lr = get_lr(it)
    for param_group in optimizer.param_groups:
        param_group['lr'] = lr
```

### Batch Size Recommendations

| Model Size | Per-GPU Batch | Gradient Accum | Effective Batch | GPUs |
|------------|---------------|----------------|-----------------|------|
| 130M | 32 | 4 | 1024 | 8 |
| 370M | 16 | 8 | 1024 | 8 |
| 790M | 8 | 8 | 512 | 8 |
| 1.4B | 4 | 16 | 512 | 8 |
| 2.8B | 2 | 16 | 256 | 8 |

```python
# Gradient accumulation
accumulation_steps = 8
optimizer.zero_grad()

for i, batch in enumerate(dataloader):
    loss = compute_loss(model, batch) / accumulation_steps
    loss.backward()

    if (i + 1) % accumulation_steps == 0:
        torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
        optimizer.step()
        optimizer.zero_grad()
```

### Optimizer Configuration

```python
# AdamW (recommended)
optimizer = torch.optim.AdamW(
    model.parameters(),
    lr=6e-4,           # Peak learning rate
    betas=(0.9, 0.95), # Standard for LLMs
    eps=1e-8,
    weight_decay=0.1   # Important for generalization
)

# Weight decay exemptions (optional)
decay = set()
no_decay = set()
for name, param in model.named_parameters():
    if 'norm' in name or 'bias' in name:
        no_decay.add(param)
    else:
        decay.add(param)

optimizer = torch.optim.AdamW([
    {'params': list(decay), 'weight_decay': 0.1},
    {'params': list(no_decay), 'weight_decay': 0.0}
], lr=6e-4, betas=(0.9, 0.95))
```

## Memory Optimization

### Gradient Checkpointing

```python
from torch.utils.checkpoint import checkpoint

class MambaBlock(nn.Module):
    def __init__(self, d_model, use_checkpoint=False):
        super().__init__()
        self.use_checkpoint = use_checkpoint
        self.norm = RMSNorm(d_model)
        self.mamba = Mamba(d_model)

    def forward(self, x):
        if self.use_checkpoint and self.training:
            return x + checkpoint(self._forward, x, use_reentrant=False)
        return x + self._forward(x)

    def _forward(self, x):
        return self.mamba(self.norm(x))

# Enable for training
model = MambaLM(use_checkpoint=True)
```

**Memory savings**: ~30-40% with minimal speed impact

### Flash Attention Integration

Mamba's CUDA kernels already use flash-attention-style optimizations:
- Fused operations in single kernel
- Recomputation in backward pass
- No intermediate activation storage

## Long Context Training

### Sequence Length Progression

```python
# Start short, increase gradually
training_stages = [
    {'seq_len': 512,  'iters': 50000},
    {'seq_len': 1024, 'iters': 100000},
    {'seq_len': 2048, 'iters': 150000},
    {'seq_len': 4096, 'iters': 200000},
]

for stage in training_stages:
    dataloader = create_dataloader(seq_len=stage['seq_len'])
    train(model, dataloader, max_iters=stage['iters'])
```

### Memory Requirements (Batch Size 1)

| Sequence Length | 130M Model | 370M Model | 1.4B Model |
|----------------|------------|------------|------------|
| 2K | 4 GB | 8 GB | 24 GB |
| 4K | 5 GB | 10 GB | 32 GB |
| 8K | 6 GB | 14 GB | 48 GB |
| 16K | 8 GB | 20 GB | 64 GB |
| 32K | 12 GB | 32 GB | 96 GB |

**Mamba advantage**: Memory grows **linearly**, Transformers grow **quadratically**

## Common Training Issues

### Issue: OOM during training

**Solution 1**: Reduce batch size
```python
per_gpu_batch = 8  # Reduce from 16
gradient_accumulation = 8  # Increase from 4
```

**Solution 2**: Enable gradient checkpointing
```python
model = MambaLM(use_checkpoint=True)
```

**Solution 3**: Use smaller sequence length
```python
seq_len = 1024  # Reduce from 2048
```

### Issue: Training unstable (loss spikes)

**Solution 1**: Check gradient norm
```python
grad_norm = torch.nn.utils.clip_grad_norm_(model.parameters(), 1.0)
print(f"Grad norm: {grad_norm}")  # Should be < 10
```

**Solution 2**: Lower learning rate
```python
max_lr = 3e-4  # Reduce from 6e-4
```

**Solution 3**: Check Δ initialization
```python
# Ensure dt_min, dt_max are reasonable
model = Mamba(
    d_model=512,
    dt_min=0.001,  # Not too small
    dt_max=0.1     # Not too large
)
```

### Issue: Slow training speed

**Solution 1**: Verify CUDA kernels installed
```python
import mamba_ssm
print(mamba_ssm.__version__)  # Should have CUDA kernels
```

**Solution 2**: Use BF16 on A100/H100
```python
with autocast(dtype=torch.bfloat16):  # Faster than FP16
    loss = model(inputs)
```

**Solution 3**: Increase batch size if possible
```python
per_gpu_batch = 16  # Increase from 8 (better GPU utilization)
```

## Checkpointing

### Save/Load Model

```python
# Save
checkpoint = {
    'model': model.state_dict(),
    'optimizer': optimizer.state_dict(),
    'iter': iteration,
    'config': model_config
}
torch.save(checkpoint, f'checkpoint_{iteration}.pt')

# Load
checkpoint = torch.load('checkpoint_100000.pt')
model.load_state_dict(checkpoint['model'])
optimizer.load_state_dict(checkpoint['optimizer'])
iteration = checkpoint['iter']
```

### Best Practices

```python
# Save every N iterations
if iteration % save_interval == 0:
    save_checkpoint(model, optimizer, iteration)

# Keep only last K checkpoints
checkpoints = sorted(glob.glob('checkpoint_*.pt'))
if len(checkpoints) > keep_last:
    for ckpt in checkpoints[:-keep_last]:
        os.remove(ckpt)
```

## Resources

- Training code: https://github.com/state-spaces/mamba/tree/main/benchmarks
- Pretrained models: https://huggingface.co/state-spaces
- CUDA installation: https://github.com/state-spaces/mamba#installation




### Benchmarks

# Mamba Performance Benchmarks

## Inference Speed Comparison

### Throughput (tokens/sec)

**Mamba-1.4B vs Transformer-1.3B** on single A100 80GB:

| Sequence Length | Mamba-1.4B | Transformer-1.3B | Speedup |
|----------------|------------|------------------|---------|
| 512 | 8,300 | 6,200 | 1.3× |
| 1024 | 7,800 | 4,100 | 1.9× |
| 2048 | 7,200 | 2,300 | 3.1× |
| 4096 | 6,800 | 1,200 | 5.7× |
| 8192 | 6,400 | 600 | **10.7×** |
| 16384 | 6,100 | OOM | ∞ |

**Key insight**: Speedup grows with sequence length (Mamba O(n) vs Transformer O(n²))

### Latency (ms per token)

**Generation latency** (batch size 1, autoregressive):

| Model | First Token | Per Token | 100 Tokens Total |
|-------|-------------|-----------|------------------|
| Mamba-130M | 3 ms | 0.8 ms | 83 ms |
| Transformer-130M | 5 ms | 1.2 ms | 125 ms |
| Mamba-1.4B | 12 ms | 3.2 ms | 332 ms |
| Transformer-1.3B | 18 ms | 8.5 ms | 868 ms |
| Mamba-2.8B | 20 ms | 6.1 ms | 631 ms |
| Transformer-2.7B | 35 ms | 18.2 ms | 1855 ms |

**Mamba advantage**: Constant per-token latency regardless of context length

## Memory Usage

### Training Memory (BF16, per GPU)

**Mamba-1.4B** training memory breakdown:

| Sequence Length | Activations | Gradients | Optimizer | Total | vs Transformer |
|----------------|-------------|-----------|-----------|-------|----------------|
| 512 | 2.1 GB | 3.2 GB | 11.2 GB | 16.5 GB | 0.9× |
| 1024 | 3.8 GB | 3.2 GB | 11.2 GB | 18.2 GB | 0.6× |
| 2048 | 7.2 GB | 3.2 GB | 11.2 GB | 21.6 GB | 0.4× |
| 4096 | 14.1 GB | 3.2 GB | 11.2 GB | 28.5 GB | 0.25× |
| 8192 | 28.0 GB | 3.2 GB | 11.2 GB | 42.4 GB | 0.15× |

**Note**: Transformer OOMs at 8K sequence length on 40GB A100

### Inference Memory (FP16, batch size 1)

| Model | KV Cache (8K ctx) | State (Mamba) | Ratio |
|-------|------------------|---------------|-------|
| 130M | 2.1 GB | 0 MB | ∞ |
| 370M | 5.2 GB | 0 MB | ∞ |
| 1.4B | 19.7 GB | 0 MB | ∞ |
| 2.8B | 38.4 GB | 0 MB | ∞ |

**Mamba stores no KV cache** - constant memory per token!

Actual Mamba state size:
- 130M: ~3 MB (d_model × d_state × n_layers = 768 × 16 × 24)
- 2.8B: ~13 MB (2560 × 16 × 64)

## Language Modeling Benchmarks

### Perplexity on Common Datasets

**Models trained on The Pile (300B tokens)**:

| Model | Params | Pile (val) | WikiText-103 | C4 | Lambada |
|-------|--------|------------|--------------|-----|---------|
| Pythia | 160M | 29.6 | 28.4 | 23.1 | 51.2 |
| **Mamba** | **130M** | **28.1** | **26.7** | **21.8** | **48.3** |
| Pythia | 410M | 18.3 | 17.6 | 16.2 | 32.1 |
| **Mamba** | **370M** | **16.7** | **16.2** | **15.1** | **28.4** |
| Pythia | 1.4B | 10.8 | 10.2 | 11.3 | 15.2 |
| **Mamba** | **1.4B** | **9.1** | **9.6** | **10.1** | **12.8** |
| Pythia | 2.8B | 8.3 | 7.9 | 9.2 | 10.6 |
| **Mamba** | **2.8B** | **7.4** | **7.2** | **8.3** | **9.1** |

**Mamba consistently outperforms** Transformers of similar size by 10-20%

### Zero-Shot Task Performance

**Mamba-2.8B vs Transformer-2.7B** on common benchmarks:

| Task | Mamba-2.8B | Transformer-2.7B | Delta |
|------|------------|------------------|-------|
| HellaSwag | 61.3 | 58.7 | +2.6 |
| PIQA | 78.1 | 76.4 | +1.7 |
| ARC-Easy | 68.2 | 65.9 | +2.3 |
| ARC-Challenge | 42.7 | 40.1 | +2.6 |
| WinoGrande | 64.8 | 62.3 | +2.5 |
| OpenBookQA | 43.2 | 41.8 | +1.4 |
| BoolQ | 71.4 | 68.2 | +3.2 |
| MMLU (5-shot) | 35.2 | 33.8 | +1.4 |

**Average improvement**: +2.2 points across benchmarks

## Audio Modeling Benchmarks

### SC09 (Speech Commands)

**Task**: Audio classification (10 classes)

| Model | Params | Accuracy | Inference (ms) |
|-------|--------|----------|----------------|
| Transformer | 8.2M | 96.2% | 18 ms |
| S4 | 6.1M | 97.1% | 8 ms |
| **Mamba** | **6.3M** | **98.4%** | **6 ms** |

### LJSpeech (Speech Generation)

**Task**: Text-to-speech quality (MOS score)

| Model | Params | MOS ↑ | RTF ↓ |
|-------|--------|-------|-------|
| Transformer | 12M | 3.82 | 0.45 |
| Conformer | 11M | 3.91 | 0.38 |
| **Mamba** | **10M** | **4.03** | **0.21** |

**RTF** (Real-Time Factor): Lower is better (0.21 = 5× faster than real-time)

## Genomics Benchmarks

### Human Reference Genome (HG38)

**Task**: Next nucleotide prediction

| Model | Context Length | Perplexity | Throughput |
|-------|----------------|------------|------------|
| Transformer | 1024 | 3.21 | 1,200 bp/s |
| Hyena | 32768 | 2.87 | 8,500 bp/s |
| **Mamba** | **1M** | **2.14** | **45,000 bp/s** |

**Mamba handles million-length sequences** efficiently

## Scaling Laws

### Compute-Optimal Training

**FLOPs vs perplexity** (The Pile validation):

| Model Size | Training FLOPs | Mamba Perplexity | Transformer Perplexity |
|------------|----------------|------------------|------------------------|
| 130M | 6e19 | 28.1 | 29.6 |
| 370M | 3e20 | 16.7 | 18.3 |
| 790M | 8e20 | 12.3 | 13.9 |
| 1.4B | 2e21 | 9.1 | 10.8 |
| 2.8B | 6e21 | 7.4 | 8.3 |

**Scaling coefficient**: Mamba achieves same perplexity as Transformer with **0.8×** compute

### Parameter Efficiency

**Perplexity 10.0 target** on The Pile:

| Model Type | Parameters Needed | Memory (inference) |
|------------|-------------------|-------------------|
| Transformer | 1.6B | 3.2 GB |
| **Mamba** | **1.1B** | **2.2 GB** |

**Mamba needs ~30% fewer parameters** for same performance

## Long-Range Arena (LRA)

**Task**: Long-context understanding benchmarks

| Task | Length | Transformer | S4 | Mamba |
|------|--------|-------------|-----|-------|
| ListOps | 2K | 36.4% | 59.6% | **61.2%** |
| Text | 4K | 64.3% | 86.8% | **88.1%** |
| Retrieval | 4K | 57.5% | 90.9% | **92.3%** |
| Image | 1K | 42.4% | 88.7% | **89.4%** |
| PathFinder | 1K | 71.4% | 86.1% | **87.8%** |
| Path-X | 16K | OOM | 88.3% | **91.2%** |

**Average**: Mamba 85.0%, S4 83.4%, Transformer 54.4%

## Training Throughput

### Tokens/sec During Training

**8× A100 80GB** cluster, BF16, different sequence lengths:

| Model | Seq Len 512 | Seq Len 2K | Seq Len 8K | Seq Len 32K |
|-------|-------------|------------|------------|-------------|
| Transformer-1.3B | 180K | 52K | OOM | OOM |
| **Mamba-1.4B** | **195K** | **158K** | **121K** | **89K** |
| Transformer-2.7B | 92K | 26K | OOM | OOM |
| **Mamba-2.8B** | **98K** | **81K** | **62K** | **45K** |

**Mamba scales to longer sequences** without OOM

## Hardware Utilization

### GPU Memory Bandwidth

**Mamba-1.4B** inference on different GPUs:

| GPU | Memory BW | Tokens/sec | Efficiency |
|-----|-----------|------------|------------|
| A100 80GB | 2.0 TB/s | 6,800 | 85% |
| A100 40GB | 1.6 TB/s | 5,400 | 84% |
| V100 32GB | 900 GB/s | 3,100 | 86% |
| RTX 4090 | 1.0 TB/s | 3,600 | 90% |

**High efficiency**: Mamba is memory-bandwidth bound (good!)

### Multi-GPU Scaling

**Mamba-2.8B** training throughput:

| GPUs | Tokens/sec | Scaling Efficiency |
|------|------------|-------------------|
| 1× A100 | 12,300 | 100% |
| 2× A100 | 23,800 | 97% |
| 4× A100 | 46,100 | 94% |
| 8× A100 | 89,400 | 91% |
| 16× A100 | 172,000 | 88% |

**Near-linear scaling** up to 16 GPUs

## Cost Analysis

### Training Cost (USD)

**Training to The Pile perplexity 10.0** on cloud GPUs:

| Model | Cloud GPUs | Hours | Cost (A100) | Cost (H100) |
|-------|------------|-------|-------------|-------------|
| Transformer-1.6B | 8× A100 | 280 | $8,400 | $4,200 |
| **Mamba-1.1B** | **8× A100** | **180** | **$5,400** | **$2,700** |

**Savings**: 36% cost reduction vs Transformer

### Inference Cost (USD/million tokens)

**API-style inference** (batch size 1, 2K context):

| Model | Latency | Cost/M tokens | Quality (perplexity) |
|-------|---------|---------------|---------------------|
| Transformer-1.3B | 8.5 ms/tok | $0.42 | 10.8 |
| **Mamba-1.4B** | **3.2 ms/tok** | **$0.18** | **9.1** |

**Mamba provides**: 2.6× faster, 57% cheaper, better quality

## Resources

- Benchmarks code: https://github.com/state-spaces/mamba/tree/main/benchmarks
- Paper (Mamba-1): https://arxiv.org/abs/2312.00752 (Section 4: Experiments)
- Paper (Mamba-2): https://arxiv.org/abs/2405.21060 (Section 5: Experiments)
- Pretrained models: https://huggingface.co/state-spaces




---

## 🚀 Usage

**Reference this template:** `@skill-mamba-architecture.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
