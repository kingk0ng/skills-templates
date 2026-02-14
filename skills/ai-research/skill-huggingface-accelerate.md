---
id: skill-huggingface-accelerate
type: skill
name: huggingface-accelerate
description: Simplest distributed training API. 4 lines to add distributed support
  to any PyTorch script. Unified API for DeepSpeed/FSDP/Megatron/DDP. Automatic device
  placement, mixed precision (FP16/BF16/FP8). Interactive config, single launch command.
  HuggingFace ecosystem standard.
category: ai-research
complexity: medium
keywords:
- api
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 1056
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,056 -->
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




# huggingface-accelerate

> Simplest distributed training API. 4 lines to add distributed support to any PyTorch script. Unified API for DeepSpeed/FSDP/Megatron/DDP. Automatic device placement, mixed precision (FP16/BF16/FP8). Interactive config, single launch command. HuggingFace ecosystem standard.

# HuggingFace Accelerate - Unified Distributed Training

## Quick start

Accelerate simplifies distributed training to 4 lines of code.

**Installation**:
```bash
pip install accelerate
```

**Convert PyTorch script** (4 lines):
```python
import torch
+ from accelerate import Accelerator

+ accelerator = Accelerator()

  model = torch.nn.Transformer()
  optimizer = torch.optim.Adam(model.parameters())
  dataloader = torch.utils.data.DataLoader(dataset)

+ model, optimizer, dataloader = accelerator.prepare(model, optimizer, dataloader)

  for batch in dataloader:
      optimizer.zero_grad()
      loss = model(batch)
-     loss.backward()
+     accelerator.backward(loss)
      optimizer.step()
```

**Run** (single command):
```bash
accelerate launch train.py
```

## Common workflows

### Workflow 1: From single GPU to multi-GPU

**Original script**:
```python
# train.py
import torch

model = torch.nn.Linear(10, 2).to('cuda')
optimizer = torch.optim.Adam(model.parameters())
dataloader = torch.utils.data.DataLoader(dataset, batch_size=32)

for epoch in range(10):
    for batch in dataloader:
        batch = batch.to('cuda')
        optimizer.zero_grad()
        loss = model(batch).mean()
        loss.backward()
        optimizer.step()
```

**With Accelerate** (4 lines added):
```python
# train.py
import torch
from accelerate import Accelerator  # +1

accelerator = Accelerator()  # +2

model = torch.nn.Linear(10, 2)
optimizer = torch.optim.Adam(model.parameters())
dataloader = torch.utils.data.DataLoader(dataset, batch_size=32)

model, optimizer, dataloader = accelerator.prepare(model, optimizer, dataloader)  # +3

for epoch in range(10):
    for batch in dataloader:
        # No .to('cuda') needed - automatic!
        optimizer.zero_grad()
        loss = model(batch).mean()
        accelerator.backward(loss)  # +4
        optimizer.step()
```

**Configure** (interactive):
```bash
accelerate config
```

**Questions**:
- Which machine? (single/multi GPU/TPU/CPU)
- How many machines? (1)
- Mixed precision? (no/fp16/bf16/fp8)
- DeepSpeed? (no/yes)

**Launch** (works on any setup):
```bash
# Single GPU
accelerate launch train.py

# Multi-GPU (8 GPUs)
accelerate launch --multi_gpu --num_processes 8 train.py

# Multi-node
accelerate launch --multi_gpu --num_processes 16 \
  --num_machines 2 --machine_rank 0 \
  --main_process_ip $MASTER_ADDR \
  train.py
```

### Workflow 2: Mixed precision training

**Enable FP16/BF16**:
```python
from accelerate import Accelerator

# FP16 (with gradient scaling)
accelerator = Accelerator(mixed_precision='fp16')

# BF16 (no scaling, more stable)
accelerator = Accelerator(mixed_precision='bf16')

# FP8 (H100+)
accelerator = Accelerator(mixed_precision='fp8')

model, optimizer, dataloader = accelerator.prepare(model, optimizer, dataloader)

# Everything else is automatic!
for batch in dataloader:
    with accelerator.autocast():  # Optional, done automatically
        loss = model(batch)
    accelerator.backward(loss)
```

### Workflow 3: DeepSpeed ZeRO integration

**Enable DeepSpeed ZeRO-2**:
```python
from accelerate import Accelerator

accelerator = Accelerator(
    mixed_precision='bf16',
    deepspeed_plugin={
        "zero_stage": 2,  # ZeRO-2
        "offload_optimizer": False,
        "gradient_accumulation_steps": 4
    }
)

# Same code as before!
model, optimizer, dataloader = accelerator.prepare(model, optimizer, dataloader)
```

**Or via config**:
```bash
accelerate config
# Select: DeepSpeed → ZeRO-2
```

**deepspeed_config.json**:
```json
{
    "fp16": {"enabled": false},
    "bf16": {"enabled": true},
    "zero_optimization": {
        "stage": 2,
        "offload_optimizer": {"device": "cpu"},
        "allgather_bucket_size": 5e8,
        "reduce_bucket_size": 5e8
    }
}
```

**Launch**:
```bash
accelerate launch --config_file deepspeed_config.json train.py
```

### Workflow 4: FSDP (Fully Sharded Data Parallel)

**Enable FSDP**:
```python
from accelerate import Accelerator, FullyShardedDataParallelPlugin

fsdp_plugin = FullyShardedDataParallelPlugin(
    sharding_strategy="FULL_SHARD",  # ZeRO-3 equivalent
    auto_wrap_policy="TRANSFORMER_AUTO_WRAP",
    cpu_offload=False
)

accelerator = Accelerator(
    mixed_precision='bf16',
    fsdp_plugin=fsdp_plugin
)

model, optimizer, dataloader = accelerator.prepare(model, optimizer, dataloader)
```

**Or via config**:
```bash
accelerate config
# Select: FSDP → Full Shard → No CPU Offload
```

### Workflow 5: Gradient accumulation

**Accumulate gradients**:
```python
from accelerate import Accelerator

accelerator = Accelerator(gradient_accumulation_steps=4)

model, optimizer, dataloader = accelerator.prepare(model, optimizer, dataloader)

for batch in dataloader:
    with accelerator.accumulate(model):  # Handles accumulation
        optimizer.zero_grad()
        loss = model(batch)
        accelerator.backward(loss)
        optimizer.step()
```

**Effective batch size**: `batch_size * num_gpus * gradient_accumulation_steps`

## When to use vs alternatives

**Use Accelerate when**:
- Want simplest distributed training
- Need single script for any hardware
- Use HuggingFace ecosystem
- Want flexibility (DDP/DeepSpeed/FSDP/Megatron)
- Need quick prototyping

**Key advantages**:
- **4 lines**: Minimal code changes
- **Unified API**: Same code for DDP, DeepSpeed, FSDP, Megatron
- **Automatic**: Device placement, mixed precision, sharding
- **Interactive config**: No manual launcher setup
- **Single launch**: Works everywhere

**Use alternatives instead**:
- **PyTorch Lightning**: Need callbacks, high-level abstractions
- **Ray Train**: Multi-node orchestration, hyperparameter tuning
- **DeepSpeed**: Direct API control, advanced features
- **Raw DDP**: Maximum control, minimal abstraction

## Common issues

**Issue: Wrong device placement**

Don't manually move to device:
```python
# WRONG
batch = batch.to('cuda')

# CORRECT
# Accelerate handles it automatically after prepare()
```

**Issue: Gradient accumulation not working**

Use context manager:
```python
# CORRECT
with accelerator.accumulate(model):
    optimizer.zero_grad()
    accelerator.backward(loss)
    optimizer.step()
```

**Issue: Checkpointing in distributed**

Use accelerator methods:
```python
# Save only on main process
if accelerator.is_main_process:
    accelerator.save_state('checkpoint/')

# Load on all processes
accelerator.load_state('checkpoint/')
```

**Issue: Different results with FSDP**

Ensure same random seed:
```python
from accelerate.utils import set_seed
set_seed(42)
```

## Advanced topics

**Megatron integration**: See [references/megatron-integration.md](references/megatron-integration.md) for tensor parallelism, pipeline parallelism, and sequence parallelism setup.

**Custom plugins**: See [references/custom-plugins.md](references/custom-plugins.md) for creating custom distributed plugins and advanced configuration.

**Performance tuning**: See [references/performance.md](references/performance.md) for profiling, memory optimization, and best practices.

## Hardware requirements

- **CPU**: Works (slow)
- **Single GPU**: Works
- **Multi-GPU**: DDP (default), DeepSpeed, or FSDP
- **Multi-node**: DDP, DeepSpeed, FSDP, Megatron
- **TPU**: Supported
- **Apple MPS**: Supported

**Launcher requirements**:
- **DDP**: `torch.distributed.run` (built-in)
- **DeepSpeed**: `deepspeed` (pip install deepspeed)
- **FSDP**: PyTorch 1.12+ (built-in)
- **Megatron**: Custom setup

## Resources

- Docs: https://huggingface.co/docs/accelerate
- GitHub: https://github.com/huggingface/accelerate
- Version: 1.11.0+
- Tutorial: "Accelerate your scripts"
- Examples: https://github.com/huggingface/accelerate/tree/main/examples
- Used by: HuggingFace Transformers, TRL, PEFT, all HF libraries





---


## 📚 Reference Materials


### Performance

# Accelerate Performance Tuning

## Profiling

### Basic Profiling

```python
from accelerate import Accelerator
import time

accelerator = Accelerator()

# Warmup
for _ in range(10):
    batch = next(iter(dataloader))
    outputs = model(**batch)
    loss = outputs.loss
    accelerator.backward(loss)
    optimizer.step()
    optimizer.zero_grad()

# Profile training loop
start = time.time()
total_batches = 100

for i, batch in enumerate(dataloader):
    if i >= total_batches:
        break

    outputs = model(**batch)
    loss = outputs.loss
    accelerator.backward(loss)
    optimizer.step()
    optimizer.zero_grad()

accelerator.wait_for_everyone()  # Sync all processes
elapsed = time.time() - start

# Metrics
batches_per_sec = total_batches / elapsed
samples_per_sec = (total_batches * batch_size * accelerator.num_processes) / elapsed

print(f"Throughput: {samples_per_sec:.2f} samples/sec")
print(f"Batches/sec: {batches_per_sec:.2f}")
```

### PyTorch Profiler Integration

```python
from torch.profiler import profile, ProfilerActivity

with profile(
    activities=[ProfilerActivity.CPU, ProfilerActivity.CUDA],
    record_shapes=True,
    profile_memory=True,
    with_stack=True
) as prof:
    for i, batch in enumerate(dataloader):
        if i >= 10:  # Profile first 10 batches
            break

        outputs = model(**batch)
        loss = outputs.loss
        accelerator.backward(loss)
        optimizer.step()
        optimizer.zero_grad()

# Print profiling results
print(prof.key_averages().table(
    sort_by="cuda_time_total", row_limit=20
))

# Export to Chrome tracing
prof.export_chrome_trace("trace.json")
# View at chrome://tracing
```

## Memory Optimization

### 1. Gradient Accumulation

**Problem**: Large batch size causes OOM

**Solution**: Accumulate gradients across micro-batches

```python
accelerator = Accelerator(gradient_accumulation_steps=8)

# Effective batch = batch_size × accumulation_steps × num_gpus
# Example: 4 × 8 × 8 = 256

for batch in dataloader:
    with accelerator.accumulate(model):  # Handles accumulation logic
        outputs = model(**batch)
        loss = outputs.loss
        accelerator.backward(loss)
        optimizer.step()
        optimizer.zero_grad()
```

**Memory savings**: 8× less activation memory (with 8 accumulation steps)

### 2. Gradient Checkpointing

**Enable in model**:

```python
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained(
    "gpt2",
    use_cache=False  # Required for gradient checkpointing
)

# Enable checkpointing
model.gradient_checkpointing_enable()

# Prepare with Accelerate
model = accelerator.prepare(model)
```

**Memory savings**: 30-50% with 10-15% slowdown

### 3. Mixed Precision

**BF16 (A100/H100)**:
```python
accelerator = Accelerator(mixed_precision='bf16')

# Automatic mixed precision
for batch in dataloader:
    outputs = model(**batch)  # Forward in BF16
    loss = outputs.loss
    accelerator.backward(loss)  # Backward in FP32
    optimizer.step()
```

**FP16 (V100, older GPUs)**:
```python
from accelerate.utils import GradScalerKwargs

scaler_kwargs = GradScalerKwargs(
    init_scale=2.**16,
    growth_interval=2000
)

accelerator = Accelerator(
    mixed_precision='fp16',
    kwargs_handlers=[scaler_kwargs]
)
```

**Memory savings**: 50% compared to FP32

### 4. CPU Offloading (DeepSpeed)

```python
from accelerate.utils import DeepSpeedPlugin

ds_plugin = DeepSpeedPlugin(
    zero_stage=3,
    offload_optimizer_device="cpu",  # Offload optimizer to CPU
    offload_param_device="cpu",      # Offload parameters to CPU
)

accelerator = Accelerator(
    deepspeed_plugin=ds_plugin,
    mixed_precision='bf16'
)
```

**Memory savings**: 10-20× for optimizer state, 5-10× for parameters

**Trade-off**: 20-30% slower due to CPU-GPU transfers

### 5. Flash Attention

```python
# Install flash-attn
# pip install flash-attn

from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained(
    "gpt2",
    attn_implementation="flash_attention_2"  # Enable Flash Attention 2
)

model = accelerator.prepare(model)
```

**Memory savings**: 50% for attention, 2× faster

**Requirements**: A100/H100, sequence length must be multiple of 128

## Communication Optimization

### 1. Gradient Bucketing (DDP)

```python
from accelerate.utils import DistributedDataParallelKwargs

ddp_kwargs = DistributedDataParallelKwargs(
    bucket_cap_mb=25,  # Bucket size for gradient reduction
    gradient_as_bucket_view=True,  # Reduce memory copies
    static_graph=False  # Set True if model doesn't change
)

accelerator = Accelerator(kwargs_handlers=[ddp_kwargs])
```

**Recommended bucket sizes**:
- Small models (<1B): 25 MB
- Medium models (1-10B): 50-100 MB
- Large models (>10B): 100-200 MB

### 2. Find Unused Parameters

```python
# Only enable if model has unused parameters (slower!)
ddp_kwargs = DistributedDataParallelKwargs(
    find_unused_parameters=True
)
```

**Use case**: Models with conditional branches (e.g., mixture of experts)

**Cost**: 10-20% slower

### 3. NCCL Tuning

```bash
# Set environment variables before launch
export NCCL_DEBUG=INFO           # Debug info
export NCCL_IB_DISABLE=0         # Enable InfiniBand
export NCCL_SOCKET_IFNAME=eth0   # Network interface
export NCCL_P2P_LEVEL=NVL        # Use NVLink

accelerate launch train.py
```

**NCCL_P2P_LEVEL options**:
- `NVL`: NVLink (fastest, within node)
- `PIX`: PCIe (fast, within node)
- `PHB`: PCIe host bridge (slow, cross-node)

## Data Loading Optimization

### 1. DataLoader Workers

```python
from torch.utils.data import DataLoader

train_loader = DataLoader(
    dataset,
    batch_size=32,
    num_workers=4,      # Parallel data loading
    pin_memory=True,    # Pin memory for faster GPU transfer
    prefetch_factor=2,  # Prefetch batches per worker
    persistent_workers=True  # Keep workers alive between epochs
)

train_loader = accelerator.prepare(train_loader)
```

**Recommendations**:
- `num_workers`: 2-4 per GPU (8 GPUs → 16-32 workers)
- `pin_memory`: Always True for GPU training
- `prefetch_factor`: 2-4 (higher for slow data loading)

### 2. Data Preprocessing

```python
from datasets import load_dataset

# Bad: Preprocess during training (slow)
dataset = load_dataset("openwebtext")

for batch in dataset:
    tokens = tokenizer(batch['text'])  # Slow!
    ...

# Good: Preprocess once, save
dataset = load_dataset("openwebtext")
tokenized = dataset.map(
    lambda x: tokenizer(x['text']),
    batched=True,
    num_proc=8,  # Parallel preprocessing
    remove_columns=['text']
)
tokenized.save_to_disk("preprocessed_data")

# Load preprocessed
dataset = load_from_disk("preprocessed_data")
```

### 3. Faster Tokenization

```python
import os

# Enable Rust-based tokenizers (10× faster)
os.environ["TOKENIZERS_PARALLELISM"] = "true"

from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained(
    "gpt2",
    use_fast=True  # Use fast Rust tokenizer
)
```

## Compilation (PyTorch 2.0+)

### Compile Model

```python
import torch

# Compile model for faster execution
model = torch.compile(
    model,
    mode="reduce-overhead",  # Options: default, reduce-overhead, max-autotune
    fullgraph=False,         # Compile entire graph (stricter)
    dynamic=True             # Support dynamic shapes
)

model = accelerator.prepare(model)
```

**Speedup**: 10-50% depending on model

**Compilation modes**:
- `default`: Balanced (best for most cases)
- `reduce-overhead`: Min overhead (best for small batches)
- `max-autotune`: Max performance (slow compile, best for production)

### Compilation Best Practices

```python
# Bad: Compile after prepare (won't work)
model = accelerator.prepare(model)
model = torch.compile(model)  # Error!

# Good: Compile before prepare
model = torch.compile(model)
model = accelerator.prepare(model)

# Training loop
for batch in dataloader:
    # First iteration: slow (compilation)
    # Subsequent iterations: fast (compiled)
    outputs = model(**batch)
    ...
```

## Benchmarking Different Strategies

### Script Template

```python
import time
import torch
from accelerate import Accelerator

def benchmark_strategy(strategy_name, accelerator_kwargs):
    """Benchmark a specific training strategy."""
    accelerator = Accelerator(**accelerator_kwargs)

    # Setup
    model = create_model()
    optimizer = torch.optim.AdamW(model.parameters(), lr=1e-4)
    dataloader = create_dataloader()

    model, optimizer, dataloader = accelerator.prepare(
        model, optimizer, dataloader
    )

    # Warmup
    for i, batch in enumerate(dataloader):
        if i >= 10:
            break
        outputs = model(**batch)
        loss = outputs.loss
        accelerator.backward(loss)
        optimizer.step()
        optimizer.zero_grad()

    # Benchmark
    accelerator.wait_for_everyone()
    torch.cuda.synchronize()
    start = time.time()

    num_batches = 100
    for i, batch in enumerate(dataloader):
        if i >= num_batches:
            break

        outputs = model(**batch)
        loss = outputs.loss
        accelerator.backward(loss)
        optimizer.step()
        optimizer.zero_grad()

    accelerator.wait_for_everyone()
    torch.cuda.synchronize()
    elapsed = time.time() - start

    # Metrics
    throughput = (num_batches * batch_size * accelerator.num_processes) / elapsed
    memory_used = torch.cuda.max_memory_allocated() / 1e9  # GB

    if accelerator.is_main_process:
        print(f"\n{strategy_name}:")
        print(f"  Throughput: {throughput:.2f} samples/sec")
        print(f"  Memory: {memory_used:.2f} GB")
        print(f"  Time: {elapsed:.2f} sec")

    torch.cuda.reset_peak_memory_stats()

# Benchmark different strategies
strategies = [
    ("DDP + FP32", {}),
    ("DDP + BF16", {"mixed_precision": "bf16"}),
    ("DDP + BF16 + GradAccum", {"mixed_precision": "bf16", "gradient_accumulation_steps": 4}),
    ("FSDP", {"fsdp_plugin": fsdp_plugin}),
    ("DeepSpeed ZeRO-2", {"deepspeed_plugin": ds_plugin_stage2}),
    ("DeepSpeed ZeRO-3", {"deepspeed_plugin": ds_plugin_stage3}),
]

for name, kwargs in strategies:
    benchmark_strategy(name, kwargs)
```

## Performance Checklist

**Before training**:
- [ ] Use BF16/FP16 mixed precision
- [ ] Enable gradient checkpointing (if OOM)
- [ ] Set appropriate `num_workers` (2-4 per GPU)
- [ ] Enable `pin_memory=True`
- [ ] Preprocess data once, not during training
- [ ] Compile model with `torch.compile` (PyTorch 2.0+)

**For large models**:
- [ ] Use FSDP or DeepSpeed ZeRO-3
- [ ] Enable CPU offloading (if still OOM)
- [ ] Use Flash Attention
- [ ] Increase gradient accumulation

**For multi-node**:
- [ ] Check network topology (InfiniBand > Ethernet)
- [ ] Tune NCCL settings
- [ ] Use larger bucket sizes for DDP
- [ ] Verify NVLink for tensor parallelism

**Profiling**:
- [ ] Profile first 10-100 batches
- [ ] Check GPU utilization (`nvidia-smi dmon`)
- [ ] Check data loading time (should be <5% of iteration)
- [ ] Identify communication bottlenecks

## Common Performance Issues

### Issue: Low GPU Utilization (<80%)

**Cause 1**: Data loading bottleneck
```python
# Solution: Increase workers and prefetch
num_workers=8
prefetch_factor=4
```

**Cause 2**: Small batch size
```python
# Solution: Increase batch size or use gradient accumulation
batch_size=32  # Increase
gradient_accumulation_steps=4  # Or accumulate
```

### Issue: High Memory Usage

**Solution 1**: Gradient checkpointing
```python
model.gradient_checkpointing_enable()
```

**Solution 2**: Reduce batch size, increase accumulation
```python
batch_size=8  # Reduce from 32
gradient_accumulation_steps=16  # Maintain effective batch
```

**Solution 3**: Use FSDP or DeepSpeed ZeRO-3
```python
accelerator = Accelerator(fsdp_plugin=fsdp_plugin)
```

### Issue: Slow Multi-GPU Training

**Cause**: Communication bottleneck

**Check 1**: Gradient bucket size
```python
ddp_kwargs = DistributedDataParallelKwargs(bucket_cap_mb=100)
```

**Check 2**: NCCL settings
```bash
export NCCL_DEBUG=INFO
# Check for "Using NVLS" (good) vs "Using PHB" (bad)
```

**Check 3**: Network bandwidth
```bash
# Test inter-GPU bandwidth
nvidia-smi nvlink -s
```

## Resources

- Accelerate Performance: https://huggingface.co/docs/accelerate/usage_guides/performance
- PyTorch Profiler: https://pytorch.org/tutorials/recipes/recipes/profiler_recipe.html
- NCCL Tuning: https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/env.html
- Flash Attention: https://github.com/Dao-AILab/flash-attention




### Megatron Integration

# Megatron Integration with Accelerate

## Overview

Accelerate supports Megatron-LM for massive model training with tensor parallelism and pipeline parallelism.

**Megatron capabilities**:
- **Tensor Parallelism (TP)**: Split layers across GPUs
- **Pipeline Parallelism (PP)**: Split model depth across GPUs
- **Data Parallelism (DP)**: Replicate model across GPU groups
- **Sequence Parallelism**: Split sequences for long contexts

## Setup

### Install Megatron-LM

```bash
# Clone Megatron-LM repository
git clone https://github.com/NVIDIA/Megatron-LM.git
cd Megatron-LM
pip install -e .

# Install Apex (NVIDIA optimizations)
git clone https://github.com/NVIDIA/apex
cd apex
pip install -v --disable-pip-version-check --no-cache-dir --no-build-isolation \
  --config-settings "--build-option=--cpp_ext" --config-settings "--build-option=--cuda_ext" ./
```

### Accelerate Configuration

```bash
accelerate config
```

**Questions**:
```
In which compute environment are you running?
> This machine

Which type of machine are you using?
> Multi-GPU

How many different machines will you use?
> 1

Do you want to use DeepSpeed/FSDP?
> No

Do you want to use Megatron-LM?
> Yes

What is the Tensor Parallelism degree? [1-8]
> 2

Do you want to enable Sequence Parallelism?
> No

What is the Pipeline Parallelism degree? [1-8]
> 2

What is the Data Parallelism degree? [1-8]
> 2

Where to perform activation checkpointing? ['SELECTIVE', 'FULL', 'NONE']
> SELECTIVE

Where to perform activation partitioning? ['SEQUENTIAL', 'UNIFORM']
> SEQUENTIAL
```

**Generated config** (`~/.cache/huggingface/accelerate/default_config.yaml`):
```yaml
compute_environment: LOCAL_MACHINE
distributed_type: MEGATRON_LM
downcast_bf16: 'no'
machine_rank: 0
main_training_function: main
megatron_lm_config:
  megatron_lm_gradient_clipping: 1.0
  megatron_lm_learning_rate_decay_iters: 320000
  megatron_lm_num_micro_batches: 1
  megatron_lm_pp_degree: 2
  megatron_lm_recompute_activations: true
  megatron_lm_sequence_parallelism: false
  megatron_lm_tp_degree: 2
mixed_precision: bf16
num_machines: 1
num_processes: 8
rdzv_backend: static
same_network: true
tpu_env: []
tpu_use_cluster: false
tpu_use_sudo: false
use_cpu: false
```

## Parallelism Strategies

### Tensor Parallelism (TP)

**Splits each transformer layer across GPUs**:

```python
# Layer split across 2 GPUs
# GPU 0: First half of attention heads
# GPU 1: Second half of attention heads

# Each GPU computes partial outputs
# All-reduce combines results
```

**TP degree recommendations**:
- **TP=1**: No tensor parallelism (single GPU per layer)
- **TP=2**: 2 GPUs per layer (good for 7-13B models)
- **TP=4**: 4 GPUs per layer (good for 20-40B models)
- **TP=8**: 8 GPUs per layer (good for 70B+ models)

**Benefits**:
- Reduces memory per GPU
- All-reduce communication (fast)

**Drawbacks**:
- Requires fast inter-GPU bandwidth (NVLink)
- Communication overhead per layer

### Pipeline Parallelism (PP)

**Splits model depth across GPUs**:

```python
# 12-layer model, PP=4
# GPU 0: Layers 0-2
# GPU 1: Layers 3-5
# GPU 2: Layers 6-8
# GPU 3: Layers 9-11
```

**PP degree recommendations**:
- **PP=1**: No pipeline parallelism
- **PP=2**: 2 pipeline stages (good for 20-40B models)
- **PP=4**: 4 pipeline stages (good for 70B+ models)
- **PP=8**: 8 pipeline stages (good for 175B+ models)

**Benefits**:
- Linear memory reduction (4× PP = 4× less memory)
- Works across nodes (slower interconnect OK)

**Drawbacks**:
- Pipeline bubbles (idle time)
- Requires micro-batching

### Data Parallelism (DP)

**Replicates model across GPU groups**:

```python
# 8 GPUs, TP=2, PP=2, DP=2
# Group 0 (GPUs 0-3): Full model replica
# Group 1 (GPUs 4-7): Full model replica
```

**DP degree**:
- `DP = total_gpus / (TP × PP)`
- Example: 8 GPUs, TP=2, PP=2 → DP=2

**Benefits**:
- Increases throughput
- Scales batch size

### Sequence Parallelism

**Splits long sequences across GPUs** (extends TP):

```python
# 8K sequence, TP=2, Sequence Parallel=True
# GPU 0: Tokens 0-4095
# GPU 1: Tokens 4096-8191
```

**Benefits**:
- Enables very long sequences (100K+ tokens)
- Reduces activation memory

**Requirements**:
- Must use with TP > 1
- RoPE/ALiBi position encodings work best

## Accelerate Code Example

### Basic Setup

```python
from accelerate import Accelerator
from accelerate.utils import MegatronLMPlugin

# Configure Megatron
megatron_plugin = MegatronLMPlugin(
    tp_degree=2,              # Tensor parallelism degree
    pp_degree=2,              # Pipeline parallelism degree
    num_micro_batches=4,      # Micro-batches for pipeline
    gradient_clipping=1.0,    # Gradient clipping value
    sequence_parallelism=False,  # Enable sequence parallelism
    recompute_activations=True,  # Activation checkpointing
    use_distributed_optimizer=True,  # Distributed optimizer
    custom_prepare_model_function=None,  # Custom model prep
)

# Initialize accelerator
accelerator = Accelerator(
    mixed_precision='bf16',
    megatron_lm_plugin=megatron_plugin
)

# Prepare model and optimizer
model, optimizer, train_dataloader = accelerator.prepare(
    model, optimizer, train_dataloader
)

# Training loop (same as DDP!)
for batch in train_dataloader:
    optimizer.zero_grad()
    outputs = model(**batch)
    loss = outputs.loss
    accelerator.backward(loss)
    optimizer.step()
```

### Full Training Script

```python
import torch
from accelerate import Accelerator
from accelerate.utils import MegatronLMPlugin
from transformers import GPT2Config, GPT2LMHeadModel

def main():
    # Megatron configuration
    megatron_plugin = MegatronLMPlugin(
        tp_degree=2,
        pp_degree=2,
        num_micro_batches=4,
        gradient_clipping=1.0,
    )

    accelerator = Accelerator(
        mixed_precision='bf16',
        gradient_accumulation_steps=8,
        megatron_lm_plugin=megatron_plugin
    )

    # Model
    config = GPT2Config(
        n_layer=24,
        n_head=16,
        n_embd=1024,
    )
    model = GPT2LMHeadModel(config)

    # Optimizer
    optimizer = torch.optim.AdamW(model.parameters(), lr=6e-4)

    # Prepare
    model, optimizer, train_loader = accelerator.prepare(
        model, optimizer, train_loader
    )

    # Training loop
    for epoch in range(num_epochs):
        for batch in train_loader:
            with accelerator.accumulate(model):
                outputs = model(**batch)
                loss = outputs.loss
                accelerator.backward(loss)
                optimizer.step()
                optimizer.zero_grad()

        # Save checkpoint
        accelerator.wait_for_everyone()
        accelerator.save_state(f'checkpoint-epoch-{epoch}')

if __name__ == '__main__':
    main()
```

### Launch Command

```bash
# 8 GPUs, TP=2, PP=2, DP=2
accelerate launch --multi_gpu --num_processes 8 train.py

# Multi-node (2 nodes, 8 GPUs each)
# Node 0
accelerate launch --multi_gpu --num_processes 16 \
  --num_machines 2 --machine_rank 0 \
  --main_process_ip $MASTER_ADDR \
  --main_process_port 29500 \
  train.py

# Node 1
accelerate launch --multi_gpu --num_processes 16 \
  --num_machines 2 --machine_rank 1 \
  --main_process_ip $MASTER_ADDR \
  --main_process_port 29500 \
  train.py
```

## Activation Checkpointing

**Reduces memory by recomputing activations**:

```python
megatron_plugin = MegatronLMPlugin(
    recompute_activations=True,      # Enable checkpointing
    checkpoint_num_layers=1,         # Checkpoint every N layers
    distribute_checkpointed_activations=True,  # Distribute across TP
    partition_activations=True,      # Partition in PP
    check_for_nan_in_loss_and_grad=True,  # Stability check
)
```

**Strategies**:
- `SELECTIVE`: Checkpoint transformer blocks only
- `FULL`: Checkpoint all layers
- `NONE`: No checkpointing

**Memory savings**: 30-50% with 10-15% slowdown

## Distributed Optimizer

**Shards optimizer state across DP ranks**:

```python
megatron_plugin = MegatronLMPlugin(
    use_distributed_optimizer=True,  # Enable sharded optimizer
)
```

**Benefits**:
- Reduces optimizer memory by DP degree
- Example: DP=4 → 4× less optimizer memory per GPU

**Compatible with**:
- AdamW, Adam, SGD
- Mixed precision training

## Performance Tuning

### Micro-Batch Size

```python
# Pipeline parallelism requires micro-batching
megatron_plugin = MegatronLMPlugin(
    pp_degree=4,
    num_micro_batches=16,  # 16 micro-batches per pipeline
)

# Effective batch = num_micro_batches × micro_batch_size × DP
# Example: 16 × 2 × 4 = 128
```

**Recommendations**:
- More micro-batches → less pipeline bubble
- Typical: 4-16 micro-batches

### Sequence Length

```python
# For long sequences, enable sequence parallelism
megatron_plugin = MegatronLMPlugin(
    tp_degree=4,
    sequence_parallelism=True,  # Required: TP > 1
)

# Enables sequences up to TP × normal limit
# Example: TP=4, 8K normal → 32K with sequence parallel
```

### GPU Topology

**NVLink required for TP**:
```bash
# Check NVLink topology
nvidia-smi topo -m

# Good topology (NVLink between all GPUs)
# GPU0 - GPU1: NV12 (fast)
# GPU0 - GPU2: NV12 (fast)

# Bad topology (PCIe only)
# GPU0 - GPU4: PHB (slow, avoid TP across these)
```

**Recommendations**:
- **TP**: Within same node (NVLink)
- **PP**: Across nodes (slower interconnect OK)
- **DP**: Any topology

## Model Size Guidelines

| Model Size | GPUs | TP | PP | DP | Micro-Batches |
|------------|------|----|----|----|--------------|
| 7B | 8 | 1 | 1 | 8 | 1 |
| 13B | 8 | 2 | 1 | 4 | 1 |
| 20B | 16 | 4 | 1 | 4 | 1 |
| 40B | 32 | 4 | 2 | 4 | 4 |
| 70B | 64 | 8 | 2 | 4 | 8 |
| 175B | 128 | 8 | 4 | 4 | 16 |

**Assumptions**: BF16, 2K sequence length, A100 80GB

## Checkpointing

### Save Checkpoint

```python
# Save full model state
accelerator.save_state('checkpoint-1000')

# Megatron saves separate files per rank
# checkpoint-1000/
#   pytorch_model_tp_0_pp_0.bin
#   pytorch_model_tp_0_pp_1.bin
#   pytorch_model_tp_1_pp_0.bin
#   pytorch_model_tp_1_pp_1.bin
#   optimizer_tp_0_pp_0.bin
#   ...
```

### Load Checkpoint

```python
# Resume training
accelerator.load_state('checkpoint-1000')

# Automatically loads correct shard per rank
```

### Convert to Standard PyTorch

```bash
# Merge Megatron checkpoint to single file
python merge_megatron_checkpoint.py \
  --checkpoint-dir checkpoint-1000 \
  --output pytorch_model.bin
```

## Common Issues

### Issue: OOM with Pipeline Parallelism

**Solution**: Increase micro-batches
```python
megatron_plugin = MegatronLMPlugin(
    pp_degree=4,
    num_micro_batches=16,  # Increase from 4
)
```

### Issue: Slow Training

**Check 1**: Pipeline bubbles (PP too high)
```python
# Reduce PP, increase TP
tp_degree=4  # Increase
pp_degree=2  # Decrease
```

**Check 2**: Micro-batch size too small
```python
num_micro_batches=8  # Increase
```

### Issue: NVLink Not Detected

```bash
# Verify NVLink
nvidia-smi nvlink -s

# If no NVLink, avoid TP > 1
# Use PP or DP instead
```

## Resources

- Megatron-LM: https://github.com/NVIDIA/Megatron-LM
- Accelerate Megatron docs: https://huggingface.co/docs/accelerate/usage_guides/megatron_lm
- Paper: "Megatron-LM: Training Multi-Billion Parameter Language Models Using Model Parallelism"
- NVIDIA Apex: https://github.com/NVIDIA/apex




### Custom Plugins

# Custom Plugins for Accelerate

## Overview

Accelerate allows creating **custom plugins** to extend distributed training strategies beyond built-in options (DDP, FSDP, DeepSpeed).

## Plugin Architecture

### Base Plugin Structure

```python
from accelerate.utils import DistributedDataParallelKwargs
from dataclasses import dataclass

@dataclass
class CustomPlugin:
    """Custom training plugin."""

    # Plugin configuration
    param1: int = 1
    param2: str = "default"

    def __post_init__(self):
        # Validation logic
        if self.param1 < 1:
            raise ValueError("param1 must be >= 1")
```

### Using Custom Plugin

```python
from accelerate import Accelerator

# Create plugin
custom_plugin = CustomPlugin(param1=4, param2="value")

# Pass to Accelerator
accelerator = Accelerator(
    custom_plugin=custom_plugin  # Not a real parameter, example only
)
```

## Built-In Plugin Examples

### 1. GradScalerKwargs (FP16 Configuration)

```python
from accelerate.utils import GradScalerKwargs

# Configure gradient scaler for FP16
scaler_kwargs = GradScalerKwargs(
    init_scale=2.**16,        # Initial loss scale
    growth_factor=2.0,        # Scale growth rate
    backoff_factor=0.5,       # Scale backoff rate
    growth_interval=2000,     # Steps between scale increases
    enabled=True              # Enable scaler
)

accelerator = Accelerator(
    mixed_precision='fp16',
    kwargs_handlers=[scaler_kwargs]  # Pass as kwargs handler
)
```

**Use case**: Fine-tune FP16 gradient scaling behavior

### 2. DistributedDataParallelKwargs

```python
from accelerate.utils import DistributedDataParallelKwargs

# Configure DDP behavior
ddp_kwargs = DistributedDataParallelKwargs(
    bucket_cap_mb=25,                 # Gradient bucketing size
    find_unused_parameters=False,     # Find unused params (slower)
    check_reduction=False,            # Check gradient reduction
    gradient_as_bucket_view=True,     # Memory optimization
    static_graph=False                # Static computation graph
)

accelerator = Accelerator(
    kwargs_handlers=[ddp_kwargs]
)
```

**Use case**: Optimize DDP performance for specific models

### 3. FP8RecipeKwargs (H100 FP8)

```python
from accelerate.utils import FP8RecipeKwargs

# Configure FP8 training (H100)
fp8_recipe = FP8RecipeKwargs(
    backend="te",              # TransformerEngine backend
    margin=0,                  # Scaling margin
    interval=1,                # Scaling interval
    fp8_format="HYBRID",       # E4M3 + E5M2 hybrid
    amax_history_len=1024,     # AMAX history length
    amax_compute_algo="max"    # AMAX computation algorithm
)

accelerator = Accelerator(
    mixed_precision='fp8',
    kwargs_handlers=[fp8_recipe]
)
```

**Use case**: Ultra-fast training on H100 GPUs

## Custom DeepSpeed Configuration

### ZeRO-3 with CPU Offload

```python
from accelerate import Accelerator
from accelerate.utils import DeepSpeedPlugin

# Custom DeepSpeed config
ds_plugin = DeepSpeedPlugin(
    zero_stage=3,                     # ZeRO-3
    offload_optimizer_device="cpu",   # CPU offload optimizer
    offload_param_device="cpu",       # CPU offload parameters
    zero3_init_flag=True,             # ZeRO-3 initialization
    zero3_save_16bit_model=True,      # Save FP16 weights
)

accelerator = Accelerator(
    deepspeed_plugin=ds_plugin,
    mixed_precision='bf16'
)
```

### ZeRO-2 with NVMe Offload

```python
ds_plugin = DeepSpeedPlugin(
    zero_stage=2,
    offload_optimizer_device="nvme",  # NVMe offload
    offload_param_device="nvme",
    nvme_path="/local_nvme",          # NVMe mount path
)
```

### Custom JSON Config

```python
import json

# Load custom DeepSpeed config
with open('deepspeed_config.json', 'r') as f:
    ds_config = json.load(f)

ds_plugin = DeepSpeedPlugin(hf_ds_config=ds_config)

accelerator = Accelerator(deepspeed_plugin=ds_plugin)
```

**Example config** (`deepspeed_config.json`):
```json
{
  "train_batch_size": "auto",
  "train_micro_batch_size_per_gpu": "auto",
  "gradient_accumulation_steps": "auto",
  "gradient_clipping": 1.0,
  "zero_optimization": {
    "stage": 3,
    "offload_optimizer": {
      "device": "cpu",
      "pin_memory": true
    },
    "offload_param": {
      "device": "cpu",
      "pin_memory": true
    },
    "overlap_comm": true,
    "contiguous_gradients": true,
    "sub_group_size": 1e9,
    "reduce_bucket_size": 5e8,
    "stage3_prefetch_bucket_size": 5e8,
    "stage3_param_persistence_threshold": 1e6,
    "stage3_max_live_parameters": 1e9,
    "stage3_max_reuse_distance": 1e9,
    "stage3_gather_16bit_weights_on_model_save": true
  },
  "bf16": {
    "enabled": true
  },
  "steps_per_print": 100,
  "wall_clock_breakdown": false
}
```

## Custom FSDP Configuration

### FSDP with Custom Auto-Wrap Policy

```python
from accelerate.utils import FullyShardedDataParallelPlugin
from torch.distributed.fsdp import BackwardPrefetch, ShardingStrategy
from torch.distributed.fsdp.wrap import size_based_auto_wrap_policy
import functools

# Custom wrap policy (size-based)
wrap_policy = functools.partial(
    size_based_auto_wrap_policy,
    min_num_params=1e6  # Wrap layers with 1M+ params
)

fsdp_plugin = FullyShardedDataParallelPlugin(
    sharding_strategy=ShardingStrategy.FULL_SHARD,  # ZeRO-3 equivalent
    backward_prefetch=BackwardPrefetch.BACKWARD_PRE,  # Prefetch strategy
    mixed_precision_policy=None,  # Use Accelerator's mixed precision
    auto_wrap_policy=wrap_policy,  # Custom wrapping
    cpu_offload=False,
    ignored_modules=None,  # Modules to not wrap
    state_dict_type="FULL_STATE_DICT",  # Save format
    optim_state_dict_config=None,
    limit_all_gathers=False,
    use_orig_params=True,  # Use original param shapes
)

accelerator = Accelerator(
    fsdp_plugin=fsdp_plugin,
    mixed_precision='bf16'
)
```

### FSDP with Transformer Auto-Wrap

```python
from torch.distributed.fsdp.wrap import transformer_auto_wrap_policy
from transformers.models.gpt2.modeling_gpt2 import GPT2Block

# Wrap at transformer block level
wrap_policy = functools.partial(
    transformer_auto_wrap_policy,
    transformer_layer_cls={GPT2Block}  # Wrap GPT2Block layers
)

fsdp_plugin = FullyShardedDataParallelPlugin(
    auto_wrap_policy=wrap_policy
)
```

## Creating Custom Training Strategy

### Example: Custom Gradient Accumulation

```python
from accelerate import Accelerator

class CustomGradientAccumulation:
    def __init__(self, steps=4, adaptive=False):
        self.steps = steps
        self.adaptive = adaptive
        self.current_step = 0

    def should_sync(self, loss):
        """Decide whether to sync gradients."""
        self.current_step += 1

        # Adaptive: sync on high loss
        if self.adaptive and loss > threshold:
            self.current_step = 0
            return True

        # Regular: sync every N steps
        if self.current_step >= self.steps:
            self.current_step = 0
            return True

        return False

# Usage
custom_accum = CustomGradientAccumulation(steps=8, adaptive=True)
accelerator = Accelerator()

for batch in dataloader:
    outputs = model(**batch)
    loss = outputs.loss

    # Scale loss
    loss = loss / custom_accum.steps
    accelerator.backward(loss)

    # Conditional sync
    if custom_accum.should_sync(loss.item()):
        optimizer.step()
        optimizer.zero_grad()
```

### Example: Custom Mixed Precision

```python
import torch

class CustomMixedPrecision:
    """Custom mixed precision with dynamic loss scaling."""

    def __init__(self, init_scale=2**16, scale_window=2000):
        self.scaler = torch.cuda.amp.GradScaler(
            init_scale=init_scale,
            growth_interval=scale_window
        )
        self.scale_history = []

    def scale_loss(self, loss):
        """Scale loss for backward."""
        return self.scaler.scale(loss)

    def unscale_and_clip(self, optimizer, max_norm=1.0):
        """Unscale gradients and clip."""
        self.scaler.unscale_(optimizer)
        torch.nn.utils.clip_grad_norm_(
            optimizer.param_groups[0]['params'],
            max_norm
        )

    def step(self, optimizer):
        """Optimizer step with scaler update."""
        scale_before = self.scaler.get_scale()
        self.scaler.step(optimizer)
        self.scaler.update()
        scale_after = self.scaler.get_scale()

        # Track scale changes
        if scale_before != scale_after:
            self.scale_history.append(scale_after)

# Usage
custom_mp = CustomMixedPrecision()

for batch in dataloader:
    with torch.cuda.amp.autocast(dtype=torch.float16):
        loss = model(**batch).loss

    scaled_loss = custom_mp.scale_loss(loss)
    scaled_loss.backward()

    custom_mp.unscale_and_clip(optimizer, max_norm=1.0)
    custom_mp.step(optimizer)
    optimizer.zero_grad()
```

## Advanced: Custom Distributed Backend

### Custom AllReduce Strategy

```python
import torch.distributed as dist

class CustomAllReduce:
    """Custom all-reduce with compression."""

    def __init__(self, compression_ratio=0.1):
        self.compression_ratio = compression_ratio

    def compress_gradients(self, tensor):
        """Top-k gradient compression."""
        k = int(tensor.numel() * self.compression_ratio)
        values, indices = torch.topk(tensor.abs().view(-1), k)
        return values, indices

    def all_reduce_compressed(self, tensor):
        """All-reduce with gradient compression."""
        # Compress
        values, indices = self.compress_gradients(tensor)

        # All-reduce compressed gradients
        dist.all_reduce(values, op=dist.ReduceOp.SUM)

        # Decompress
        tensor_compressed = torch.zeros_like(tensor).view(-1)
        tensor_compressed[indices] = values / dist.get_world_size()

        return tensor_compressed.view_as(tensor)

# Usage in training loop
custom_ar = CustomAllReduce(compression_ratio=0.1)

for batch in dataloader:
    loss = model(**batch).loss
    loss.backward()

    # Custom all-reduce
    for param in model.parameters():
        if param.grad is not None:
            param.grad.data = custom_ar.all_reduce_compressed(param.grad.data)

    optimizer.step()
    optimizer.zero_grad()
```

## Plugin Best Practices

### 1. Validation in `__post_init__`

```python
@dataclass
class CustomPlugin:
    learning_rate: float = 1e-3
    warmup_steps: int = 1000

    def __post_init__(self):
        # Validate parameters
        if self.learning_rate <= 0:
            raise ValueError("learning_rate must be positive")
        if self.warmup_steps < 0:
            raise ValueError("warmup_steps must be non-negative")

        # Compute derived values
        self.min_lr = self.learning_rate * 0.1
```

### 2. Compatibility Checks

```python
@dataclass
class CustomPlugin:
    feature_enabled: bool = True

    def is_compatible(self, accelerator):
        """Check if plugin is compatible with accelerator config."""
        if self.feature_enabled and accelerator.mixed_precision == 'fp8':
            raise ValueError("Custom plugin not compatible with FP8")
        return True
```

### 3. State Management

```python
@dataclass
class CustomPlugin:
    counter: int = 0
    history: list = None

    def __post_init__(self):
        if self.history is None:
            self.history = []

    def update_state(self, value):
        """Update plugin state during training."""
        self.counter += 1
        self.history.append(value)
```

## Resources

- Accelerate Plugins: https://huggingface.co/docs/accelerate/package_reference/kwargs
- DeepSpeed Config: https://www.deepspeed.ai/docs/config-json/
- FSDP Guide: https://pytorch.org/docs/stable/fsdp.html
- Custom Training Loops: https://huggingface.co/docs/accelerate/usage_guides/training_tpu




---

## 🚀 Usage

**Reference this template:** `@skill-huggingface-accelerate.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
