---
id: skill-torchforge-rl-training
type: skill
name: torchforge-rl-training
description: Provides guidance for PyTorch-native agentic RL using torchforge, Meta's
  library separating infra from algorithms. Use when you want clean RL abstractions,
  easy algorithm experimentation, or scalable training with Monarch and TorchTitan.
category: ai-research
complexity: medium
keywords:
- api
- github
- python
capabilities: []
token_estimate: 1423
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,423 -->
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




# torchforge-rl-training

> Provides guidance for PyTorch-native agentic RL using torchforge, Meta's library separating infra from algorithms. Use when you want clean RL abstractions, easy algorithm experimentation, or scalable training with Monarch and TorchTitan.

# torchforge: PyTorch-Native Agentic RL Library

torchforge is Meta's PyTorch-native RL library that separates infrastructure concerns from algorithm concerns. It enables rapid RL research by letting you focus on algorithms while handling distributed training, inference, and weight sync automatically.

## When to Use torchforge

**Choose torchforge when you need:**
- Clean separation between RL algorithms and infrastructure
- PyTorch-native abstractions (no Ray dependency)
- Easy algorithm experimentation (GRPO, DAPO, SAPO in ~100 lines)
- Scalable training with Monarch actor system
- Integration with TorchTitan for model parallelism

**Consider alternatives when:**
- You need production-ready stability → use **miles** or **verl**
- You want Megatron-native training → use **slime**
- torchforge is experimental and APIs may change

## Key Features

- **Algorithm isolation**: Implement RL algorithms without touching infrastructure
- **Scalability**: From single GPU to thousands via Monarch
- **Modern stack**: TorchTitan (training), vLLM (inference), TorchStore (sync)
- **Loss functions**: GRPO, DAPO, CISPO, GSPO, SAPO built-in

## Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│ Application Layer (Your Code)                           │
│ - Define reward models, loss functions, sampling        │
└─────────────────────┬───────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────┐
│ Forge API Layer                                         │
│ - Episode, Group dataclasses                           │
│ - Service interfaces (async/await)                      │
└─────────────────────┬───────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────┐
│ Distributed Services (Monarch)                          │
│ ├── Trainer (TorchTitan FSDP)                          │
│ ├── Generator (vLLM inference)                          │
│ ├── Reference Model (frozen KL baseline)               │
│ └── Reward Actors (compute rewards)                    │
└─────────────────────────────────────────────────────────┘
```

## Installation

```bash
# Create environment
conda create -n forge python=3.12
conda activate forge

# Install (handles PyTorch nightly + dependencies)
./scripts/install.sh

# Verify
python -c "import torch, forge, vllm; print('OK')"
```

### ROCm Installation

```bash
./scripts/install_rocm.sh
```

## Quick Start

### SFT Training (2+ GPUs)

```bash
python -m apps.sft.main --config apps/sft/llama3_8b.yaml
```

### GRPO Training (3+ GPUs)

```bash
python -m apps.grpo.main --config apps/grpo/qwen3_1_7b.yaml
```

---

## Workflow 1: GRPO Training for Math Reasoning

Use this workflow for training reasoning models with group-relative advantages.

### Prerequisites Checklist
- [ ] 3+ GPUs (GPU0: trainer, GPU1: ref_model, GPU2: generator)
- [ ] Model from HuggingFace Hub
- [ ] Training dataset (GSM8K, MATH, etc.)

### Step 1: Create Configuration

```yaml
# config/grpo_math.yaml
model: "Qwen/Qwen2.5-7B-Instruct"

dataset:
  path: "openai/gsm8k"
  split: "train"
  streaming: true

training:
  batch_size: 4
  learning_rate: 1e-6
  seq_len: 4096
  dtype: bfloat16
  gradient_accumulation_steps: 4

grpo:
  n_samples: 8           # Responses per prompt
  clip_low: 0.2
  clip_high: 0.28
  beta: 0.1              # KL penalty coefficient
  temperature: 0.7

services:
  generator:
    procs: 1
    num_replicas: 1
    with_gpus: true
  trainer:
    procs: 1
    num_replicas: 1
    with_gpus: true
  ref_model:
    procs: 1
    num_replicas: 1
    with_gpus: true
```

### Step 2: Define Reward Function

```python
# rewards.py
# Reward functions are in forge.data.rewards
from forge.data.rewards import MathReward, ThinkingReward
import re

# Or define your own reward function
class CustomMathReward:
    def __call__(self, prompt: str, response: str, target: str) -> float:
        # Extract answer from response
        match = re.search(r'\\boxed{([^}]+)}', response)
        if not match:
            return 0.0

        answer = match.group(1).strip()
        return 1.0 if answer == target else 0.0
```

### Step 3: Launch Training

```bash
python -m apps.grpo.main --config config/grpo_math.yaml
```

### Step 4: Monitor Progress
- [ ] Check W&B dashboard for loss curves
- [ ] Verify entropy is decreasing (policy becoming more deterministic)
- [ ] Monitor KL divergence (should stay bounded)

---

## Workflow 2: Custom Loss Function

Use this workflow to implement new RL algorithms.

### Step 1: Create Loss Class

```python
# src/forge/losses/custom_loss.py
import torch
import torch.nn as nn

class CustomLoss(nn.Module):
    def __init__(self, clip_range: float = 0.2, beta: float = 0.1):
        super().__init__()
        self.clip_range = clip_range
        self.beta = beta

    def forward(
        self,
        logprobs: torch.Tensor,
        ref_logprobs: torch.Tensor,
        advantages: torch.Tensor,
        padding_mask: torch.Tensor,
    ) -> torch.Tensor:
        # Compute importance ratio
        ratio = torch.exp(logprobs - ref_logprobs)

        # Clipped policy gradient
        clipped_ratio = torch.clamp(
            ratio,
            1 - self.clip_range,
            1 + self.clip_range
        )
        pg_loss = -torch.min(ratio * advantages, clipped_ratio * advantages)

        # KL penalty
        kl = ref_logprobs - logprobs

        # Apply mask and aggregate
        masked_loss = (pg_loss + self.beta * kl) * padding_mask
        loss = masked_loss.sum() / padding_mask.sum()

        return loss
```

### Step 2: Integrate into Application

```python
# apps/custom/main.py
from forge.losses.custom_loss import CustomLoss

loss_fn = CustomLoss(clip_range=0.2, beta=0.1)

# In training loop
loss = loss_fn(
    logprobs=logprobs,
    ref_logprobs=ref_logprobs,
    advantages=advantages,
    padding_mask=padding_mask,
)
```

---

## Workflow 3: Multi-GPU Distributed Training

Use this workflow for scaling to multiple GPUs or nodes.

### Configuration for Distributed

```yaml
# config/distributed.yaml
model: "meta-llama/Meta-Llama-3.1-8B-Instruct"

parallelism:
  tensor_parallel_degree: 2    # Split model across GPUs
  pipeline_parallel_degree: 1
  data_parallel_shard_degree: 2

services:
  generator:
    procs: 2                   # 2 processes for TP=2
    num_replicas: 1
    with_gpus: true
  trainer:
    procs: 2
    num_replicas: 1
    with_gpus: true
```

### Launch with SLURM

```bash
# Submit job
sbatch --nodes=2 --gpus-per-node=8 run_grpo.sh
```

### Launch Locally (Multi-GPU)

```bash
# 8 GPU setup
python -m apps.grpo.main \
    --config config/distributed.yaml \
    --trainer.procs 4 \
    --generator.procs 4
```

---

## Core API Reference

### Training Batch Format

torchforge uses dictionary-based batches for training:

```python
# inputs: list of dicts with torch.Tensor values
inputs = [{"tokens": torch.Tensor}]

# targets: list of dicts with training signals
targets = [{
    "response": torch.Tensor,
    "ref_logprobs": torch.Tensor,
    "advantages": torch.Tensor,
    "padding_mask": torch.Tensor
}]

# train_step returns loss as float
loss = trainer.train_step(inputs, targets)
```

### Completion

Generated output from vLLM:

```python
@dataclass
class Completion:
    text: str              # Generated text
    token_ids: list[int]   # Token IDs
    logprobs: list[float]  # Log probabilities
    metadata: dict         # Custom metadata
```

---

## Built-in Loss Functions

### Loss Functions

Loss functions are in the `forge.losses` module:

```python
from forge.losses import SimpleGRPOLoss, ReinforceLoss

# SimpleGRPOLoss for GRPO training
loss_fn = SimpleGRPOLoss(beta=0.1)

# Forward pass
loss = loss_fn(
    logprobs=logprobs,
    ref_logprobs=ref_logprobs,
    advantages=advantages,
    padding_mask=padding_mask
)
```

### ReinforceLoss

```python
from forge.losses.reinforce_loss import ReinforceLoss

# With optional importance ratio clipping
loss_fn = ReinforceLoss(clip_ratio=0.2)
```

---

## Common Issues and Solutions

### Issue: Not Enough GPUs

**Symptoms**: "Insufficient GPU resources" error

**Solutions**:
```yaml
# Reduce service requirements
services:
  generator:
    procs: 1
    with_gpus: true
  trainer:
    procs: 1
    with_gpus: true
  # Remove ref_model (uses generator weights)
```

Or use CPU for reference model:
```yaml
ref_model:
  with_gpus: false
```

### Issue: OOM During Generation

**Symptoms**: CUDA OOM in vLLM

**Solutions**:
```yaml
# Reduce batch size
grpo:
  n_samples: 4  # Reduce from 8

# Or reduce sequence length
training:
  seq_len: 2048
```

### Issue: Slow Weight Sync

**Symptoms**: Long pauses between training and generation

**Solutions**:
```bash
# Enable RDMA (if available)
export TORCHSTORE_USE_RDMA=1

# Or reduce sync frequency
training:
  sync_interval: 10  # Sync every 10 steps
```

### Issue: Policy Collapse

**Symptoms**: Entropy drops to zero, reward stops improving

**Solutions**:
```yaml
# Increase KL penalty
grpo:
  beta: 0.2  # Increase from 0.1

# Or add entropy bonus
training:
  entropy_coef: 0.01
```

---

## Resources

- **Documentation**: https://meta-pytorch.org/torchforge
- **GitHub**: https://github.com/meta-pytorch/torchforge
- **Discord**: https://discord.gg/YsTYBh6PD9
- **TorchTitan**: https://github.com/pytorch/torchtitan
- **Monarch**: https://github.com/meta-pytorch/monarch



---


## 📚 Reference Materials


### Api Reference

# torchforge API Reference

## Architecture Overview

torchforge implements a fully asynchronous RL system built on:

- **Monarch**: PyTorch-native distributed coordination framework
- **TorchTitan**: Meta's production LLM training platform
- **vLLM**: High-throughput inference engine

```
┌─────────────────────────────────────────────────────────┐
│ Application Layer (Your Code)                           │
│ - Define reward models, loss functions, sampling        │
└─────────────────────┬───────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────┐
│ Forge API Layer                                         │
│ - ForgeActor, Service                                   │
│ - Async service interfaces                              │
└─────────────────────┬───────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────┐
│ Distributed Services (Monarch)                          │
│ ├── TitanTrainer (TorchTitan FSDP)                     │
│ ├── Generator (vLLM inference)                          │
│ └── ReferenceModel (frozen KL baseline)                │
└─────────────────────────────────────────────────────────┘
```

## Core Classes

### ForgeActor

Base class for Forge actors with configurable resource attributes.

**Location**: `forge.controller.actor.ForgeActor`

```python
from forge.controller.actor import ForgeActor

class MyActor(ForgeActor):
    procs = 1           # Number of processes
    hosts = None        # Host distribution
    with_gpus = True    # GPU allocation flag
    num_replicas = 1    # Service replica count
    mesh_name = None    # Process mesh identifier
```

**Class Methods**:
- `as_actor(*args, **actor_kwargs)` → Spawns single actor using .options() configuration
- `launch(*args, **kwargs)` → Provisions and deploys new actor replica
- `options(*, procs=1, hosts=None, with_gpus=False, num_replicas=1, mesh_name=None, **kwargs)` → Pre-configures actor class
- `shutdown(actor)` → Terminates actor instance

### TitanTrainer

Generic trainer actor built on TorchTitan's training engine.

**Location**: `forge.actors.trainer.TitanTrainer`

**Key Methods**:
- `forward_backward(batch)` → Forward and backward pass
- `train_step()` → Complete training step
- `setup()` / `cleanup()` → Lifecycle methods
- `clear_gradients()` → Reset gradients
- `save()` / `load()` → Checkpoint operations
- `push_weights()` → Sync weights to inference
- `get_config()` / `get_status()` → Introspection

**Properties**: `job`, `model`, `optimizer`, `lr_scheduler`, `training`, `parallelism`, `checkpoint`, `activation_checkpoint`, `compile`, `quantize`, `comm`, `memory_estimation`, `state_dict_key`

### Generator

vLLM-based generator for inference.

**Location**: `forge.actors.generator.Generator`

```python
from forge.actors.generator import Generator

generator = Generator(
    engine_args=<factory>,
    sampling_params=<factory>,
    prefetch_weights_to_shm=True,
    n_fetcher_procs=8
)
```

**Key Methods**:
- `generate()` → Generate completions
- `run()` → Async generation loop
- `update_weights()` → Receive new weights from trainer
- `get_version()` / `get_vllm_config()` → Introspection

**Returns**: `Completion` dataclass with fields: `prompt`, `text`, `token_ids`, `logprobs`

### ReferenceModel

Frozen policy copy for computing KL divergence.

**Location**: `forge.actors.reference_model.ReferenceModel`

Maintains a frozen copy of the policy for computing advantages without gradient computation.

**Key Methods**:
- `forward()` → Inference without gradients
- `setup()` → Initialize from checkpoint

### Service

Actor-less service implementation for managing replicas.

**Location**: `forge.controller.service.service.Service`

```python
Service(cfg, actor_def, actor_args, actor_kwargs)
```

**Methods**:
- `call_all(function, *args, **kwargs)` → Call function on all healthy replicas
- `get_metrics()` → Returns ServiceMetrics object
- `start_session()` / `terminate_session(sess_id)` → Session management
- `stop()` → Stop service and all replicas

## Configuration (TorchTitan)

torchforge uses TorchTitan's configuration system:

### Job Configuration

```python
from torchtitan.config.job_config import Job

@dataclass
class Job:
    config_file: str
    dump_folder: str
    description: str
    print_config: bool
    custom_config_module: str
```

### Model Configuration

```python
from torchtitan.config.job_config import Model

@dataclass
class Model:
    name: str
    flavor: str
    hf_assets_path: str
    tokenizer_path: str
    converters: list
    print_after_conversion: bool
```

### Training Configuration

```python
from torchtitan.config.job_config import Training

@dataclass
class Training:
    dataset: str
    dataset_path: str
    local_batch_size: int
    global_batch_size: int
    seq_len: int
    max_norm: float
    steps: int
    dtype: str
    mixed_precision_param: str
    mixed_precision_reduce: str
    gc_freq: int
    seed: int
    deterministic: bool
    enable_cpu_offload: bool
    # ... additional fields
```

### Parallelism Configuration

```python
from torchtitan.config.job_config import Parallelism

@dataclass
class Parallelism:
    # Parallelism degrees
    data_parallel_shard_degree: int
    data_parallel_replicate_degree: int
    tensor_parallel_degree: int
    pipeline_parallel_degree: int
    context_parallel_degree: int
    expert_parallel_degree: int
    # FSDP configuration options
    # ... additional fields
```

### Optimizer Configuration

```python
from torchtitan.config.job_config import Optimizer

@dataclass
class Optimizer:
    name: str
    lr: float
    beta1: float
    beta2: float
    eps: float
    weight_decay: float
    implementation: str
    early_step_in_backward: bool
```

## YAML Configuration Example

```yaml
# config/grpo_math.yaml
model: "Qwen/Qwen2.5-7B-Instruct"

dataset:
  path: "openai/gsm8k"
  split: "train"
  streaming: true

training:
  batch_size: 4
  learning_rate: 1e-6
  seq_len: 4096
  dtype: bfloat16
  gradient_accumulation_steps: 4

grpo:
  n_samples: 8
  clip_low: 0.2
  clip_high: 0.28
  beta: 0.1
  temperature: 0.7

services:
  generator:
    procs: 1
    num_replicas: 1
    with_gpus: true
  trainer:
    procs: 1
    num_replicas: 1
    with_gpus: true
  ref_model:
    procs: 1
    num_replicas: 1
    with_gpus: true
```

## Launch Commands

### SFT Training (2+ GPUs)

```bash
python -m apps.sft.main --config apps/sft/llama3_8b.yaml
```

### GRPO Training (3+ GPUs)

```bash
python -m apps.grpo.main --config apps/grpo/qwen3_1_7b.yaml
```

### Multi-GPU Distributed

```bash
python -m apps.grpo.main \
    --config config/distributed.yaml \
    --trainer.procs 4 \
    --generator.procs 4
```

## Async Communication Pattern

torchforge uses async/await patterns for service communication:

```python
# Route: async point-to-point
response = await service.method.route(arg1, arg2)

# Fanout: broadcast to all replicas
await service.update_weights.fanout(training_step)
```

## Installation

```bash
# Create environment
conda create -n forge python=3.12
conda activate forge

# Install (handles PyTorch nightly + dependencies)
./scripts/install.sh

# ROCm (AMD GPUs)
./scripts/install_rocm.sh

# Verify
python -c "import torch, forge, vllm; print('OK')"
```

**Requirements**:
- PyTorch >= 2.9.0 (nightly)
- Monarch
- TorchTitan
- vLLM

## Experimental Warning

Both Monarch and torchforge are experimental. APIs may change as the project learns from early adopters.

## Resources

- Documentation: https://meta-pytorch.org/torchforge
- GitHub: https://github.com/meta-pytorch/torchforge
- Discord: https://discord.gg/YsTYBh6PD9
- TorchTitan: https://github.com/pytorch/torchtitan
- Monarch: https://github.com/meta-pytorch/monarch
- Blog: https://pytorch.org/blog/introducing-torchforge/




### Troubleshooting

# torchforge Troubleshooting Guide

## GPU Resource Issues

### Issue: Not Enough GPUs

**Symptoms**: "Insufficient GPU resources" error

**Solutions**:

1. **Reduce service requirements**:
```yaml
services:
  generator:
    procs: 1
    with_gpus: true
  trainer:
    procs: 1
    with_gpus: true
  # Remove ref_model or use CPU
```

2. **Use CPU for reference model**:
```yaml
ref_model:
  with_gpus: false  # Run on CPU
```

3. **Share resources between services**:
```yaml
services:
  generator:
    procs: 1
    num_replicas: 1
    colocate_with: trainer  # Share GPU with trainer
```

### Issue: Minimum GPU Requirements

**Reference**:
- SFT: 2+ GPUs (trainer + generator)
- GRPO: 3+ GPUs (trainer + generator + ref_model)
- Large models: 8+ GPUs with tensor parallelism

## Memory Issues

### Issue: OOM During Generation

**Symptoms**: CUDA OOM in vLLM

**Solutions**:

1. **Reduce batch size**:
```yaml
grpo:
  n_samples: 4  # Reduce from 8
```

2. **Reduce sequence length**:
```yaml
training:
  seq_len: 2048  # Reduce from 4096
```

3. **Reduce vLLM memory**:
```yaml
generator:
  gpu_memory_utilization: 0.7  # Reduce from 0.9
```

### Issue: OOM During Training

**Symptoms**: CUDA OOM in backward pass

**Solutions**:

1. **Enable gradient checkpointing**:
```yaml
training:
  gradient_checkpointing: true
```

2. **Increase gradient accumulation**:
```yaml
training:
  gradient_accumulation_steps: 8  # Increase from 4
```

3. **Reduce batch size**:
```yaml
training:
  batch_size: 2  # Reduce from 4
```

## Weight Synchronization Issues

### Issue: Slow Weight Sync

**Symptoms**: Long pauses between training and generation

**Solutions**:

1. **Enable RDMA** (if available):
```bash
export TORCHSTORE_USE_RDMA=1
```

2. **Reduce sync frequency**:
```yaml
training:
  sync_interval: 10  # Sync every 10 steps
```

3. **Use colocated services**:
```yaml
services:
  generator:
    colocate_with: trainer
```

### Issue: Weight Sync Failures

**Symptoms**: Errors in weight transfer, stale weights

**Solutions**:

1. **Check network connectivity**:
```bash
ping other_node
```

2. **Increase timeout**:
```yaml
services:
  weight_sync_timeout: 600  # 10 minutes
```

3. **Enable sync verification**:
```yaml
training:
  verify_weight_sync: true
```

## Training Stability Issues

### Issue: Policy Collapse

**Symptoms**: Entropy drops to zero, reward stops improving

**Solutions**:

1. **Increase KL penalty**:
```yaml
grpo:
  beta: 0.2  # Increase from 0.1
```

2. **Add entropy bonus**:
```yaml
training:
  entropy_coef: 0.01
```

3. **Reduce learning rate**:
```yaml
training:
  learning_rate: 5e-7  # Reduce from 1e-6
```

### Issue: Loss Spikes

**Symptoms**: Sudden loss increases, training instability

**Solutions**:

1. **Enable gradient clipping**:
```yaml
training:
  max_grad_norm: 1.0
```

2. **Reduce clip range**:
```yaml
grpo:
  clip_low: 0.1   # Reduce from 0.2
  clip_high: 0.18 # Reduce from 0.28
```

3. **Use learning rate warmup**:
```yaml
training:
  warmup_steps: 100
```

### Issue: Divergent Training

**Symptoms**: Loss becomes NaN, model outputs garbage

**Solutions**:

1. **Check for data issues**:
```python
# Verify no empty sequences
for batch in dataset:
    assert batch.input_ids.numel() > 0
```

2. **Use BF16 instead of FP16**:
```yaml
training:
  dtype: bfloat16
```

3. **Reduce learning rate significantly**:
```yaml
training:
  learning_rate: 1e-7
```

## Service Issues

### Issue: Service Startup Failures

**Symptoms**: Services fail to initialize

**Solutions**:

1. **Check resource availability**:
```bash
nvidia-smi  # Verify GPU availability
```

2. **Increase startup timeout**:
```yaml
services:
  startup_timeout: 600
```

3. **Check model path**:
```python
from transformers import AutoModelForCausalLM
model = AutoModelForCausalLM.from_pretrained("model_path")  # Verify accessible
```

### Issue: Generator Not Responding

**Symptoms**: Generation hangs, timeouts

**Solutions**:

1. **Check vLLM status**:
```python
# Add health check
await generator.health_check.route()
```

2. **Restart service**:
```python
await generator.restart.fanout()
```

3. **Reduce concurrent requests**:
```yaml
generator:
  max_concurrent_requests: 10
```

## Monarch Issues

### Issue: Monarch Actor Failures

**Symptoms**: Actor crashes, communication errors

**Solutions**:

1. **Enable fault tolerance**:
```yaml
monarch:
  fault_tolerance: true
  max_restarts: 3
```

2. **Increase actor memory**:
```yaml
services:
  actor_memory_mb: 4096
```

3. **Check Monarch logs**:
```bash
export MONARCH_LOG_LEVEL=DEBUG
```

### Issue: Deadlock in Distributed Communication

**Symptoms**: Training hangs, no progress

**Solutions**:

1. **Check for blocking calls**:
```python
# Use async/await correctly
result = await service.method.route(args)  # Correct
# result = service.method.route(args).wait()  # May deadlock
```

2. **Add timeouts**:
```python
result = await asyncio.wait_for(
    service.method.route(args),
    timeout=60.0
)
```

## Installation Issues

### Issue: PyTorch Version Mismatch

**Symptoms**: Import errors, CUDA errors

**Solutions**:

1. **Use provided install script**:
```bash
./scripts/install.sh
```

2. **Verify versions**:
```python
import torch
print(torch.__version__)  # Should be 2.9.0+
```

3. **Clean reinstall**:
```bash
pip uninstall torch torchvision torchaudio
./scripts/install.sh
```

### Issue: Monarch Installation Fails

**Symptoms**: Cannot import monarch

**Solutions**:

1. **Install from source**:
```bash
git clone https://github.com/meta-pytorch/monarch
cd monarch && pip install -e .
```

2. **Check CUDA compatibility**:
```bash
nvcc --version  # Should match PyTorch CUDA
```

## Debugging Tips

### Enable Verbose Logging

```bash
export FORGE_DEBUG=1
export MONARCH_LOG_LEVEL=DEBUG
```

### Profile Services

```python
# Add profiling
with torch.profiler.profile() as prof:
    result = await trainer.train_step.route(batch)
prof.export_chrome_trace("trace.json")
```

### Monitor GPU Utilization

```bash
watch -n 1 nvidia-smi
```

### Test Services Individually

```python
# Test generator
completions = await generator.generate.route(
    prompts=["Hello"],
    max_tokens=10,
)
print(completions[0].text)

# Test trainer
result = await trainer.train_step.route(dummy_batch)
print(result.loss)
```

## Experimental Warning

Both Monarch and torchforge are experimental. Expect:
- API changes between versions
- Incomplete features
- Bugs in edge cases

Check Discord for latest updates and workarounds.

## Resources

- GitHub Issues: https://github.com/meta-pytorch/torchforge/issues
- Discord: https://discord.gg/YsTYBh6PD9
- Monarch Issues: https://github.com/meta-pytorch/monarch/issues




---

## 🚀 Usage

**Reference this template:** `@skill-torchforge-rl-training.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
