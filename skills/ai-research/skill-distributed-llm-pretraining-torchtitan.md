---
id: skill-distributed-llm-pretraining-torchtitan
type: skill
name: distributed-llm-pretraining-torchtitan
description: Provides PyTorch-native distributed LLM pretraining using torchtitan
  with 4D parallelism (FSDP2, TP, PP, CP). Use when pretraining Llama 3.1, DeepSeek
  V3, or custom models at scale from 8 to 512+ GPUs with Float8, torch.compile, and
  distributed checkpointing.
category: ai-research
complexity: medium
keywords:
- git
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 1387
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,387 -->
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




# distributed-llm-pretraining-torchtitan

> Provides PyTorch-native distributed LLM pretraining using torchtitan with 4D parallelism (FSDP2, TP, PP, CP). Use when pretraining Llama 3.1, DeepSeek V3, or custom models at scale from 8 to 512+ GPUs with Float8, torch.compile, and distributed checkpointing.

# TorchTitan - PyTorch Native Distributed LLM Pretraining

## Quick start

TorchTitan is PyTorch's official platform for large-scale LLM pretraining with composable 4D parallelism (FSDP2, TP, PP, CP), achieving 65%+ speedups over baselines on H100 GPUs.

**Installation**:
```bash
# From PyPI (stable)
pip install torchtitan

# From source (latest features, requires PyTorch nightly)
git clone https://github.com/pytorch/torchtitan
cd torchtitan
pip install -r requirements.txt
```

**Download tokenizer**:
```bash
# Get HF token from https://huggingface.co/settings/tokens
python scripts/download_hf_assets.py --repo_id meta-llama/Llama-3.1-8B --assets tokenizer --hf_token=...
```

**Start training on 8 GPUs**:
```bash
CONFIG_FILE="./torchtitan/models/llama3/train_configs/llama3_8b.toml" ./run_train.sh
```

## Common workflows

### Workflow 1: Pretrain Llama 3.1 8B on single node

Copy this checklist:

```
Single Node Pretraining:
- [ ] Step 1: Download tokenizer
- [ ] Step 2: Configure training
- [ ] Step 3: Launch training
- [ ] Step 4: Monitor and checkpoint
```

**Step 1: Download tokenizer**

```bash
python scripts/download_hf_assets.py \
  --repo_id meta-llama/Llama-3.1-8B \
  --assets tokenizer \
  --hf_token=YOUR_HF_TOKEN
```

**Step 2: Configure training**

Edit or create a TOML config file:

```toml
# llama3_8b_custom.toml
[job]
dump_folder = "./outputs"
description = "Llama 3.1 8B training"

[model]
name = "llama3"
flavor = "8B"
hf_assets_path = "./assets/hf/Llama-3.1-8B"

[optimizer]
name = "AdamW"
lr = 3e-4

[lr_scheduler]
warmup_steps = 200

[training]
local_batch_size = 2
seq_len = 8192
max_norm = 1.0
steps = 1000
dataset = "c4"

[parallelism]
data_parallel_shard_degree = -1  # Use all GPUs for FSDP

[activation_checkpoint]
mode = "selective"
selective_ac_option = "op"

[checkpoint]
enable = true
folder = "checkpoint"
interval = 500
```

**Step 3: Launch training**

```bash
# 8 GPUs on single node
CONFIG_FILE="./llama3_8b_custom.toml" ./run_train.sh

# Or explicitly with torchrun
torchrun --nproc_per_node=8 \
  -m torchtitan.train \
  --job.config_file ./llama3_8b_custom.toml
```

**Step 4: Monitor and checkpoint**

TensorBoard logs are saved to `./outputs/tb/`:
```bash
tensorboard --logdir ./outputs/tb
```

### Workflow 2: Multi-node training with SLURM

```
Multi-Node Training:
- [ ] Step 1: Configure parallelism for scale
- [ ] Step 2: Set up SLURM script
- [ ] Step 3: Submit job
- [ ] Step 4: Resume from checkpoint
```

**Step 1: Configure parallelism for scale**

For 70B model on 256 GPUs (32 nodes):
```toml
[parallelism]
data_parallel_shard_degree = 32  # FSDP across 32 ranks
tensor_parallel_degree = 8        # TP within node
pipeline_parallel_degree = 1      # No PP for 70B
context_parallel_degree = 1       # Increase for long sequences
```

**Step 2: Set up SLURM script**

```bash
#!/bin/bash
#SBATCH --job-name=llama70b
#SBATCH --nodes=32
#SBATCH --ntasks-per-node=8
#SBATCH --gpus-per-node=8

srun torchrun \
  --nnodes=32 \
  --nproc_per_node=8 \
  --rdzv_backend=c10d \
  --rdzv_endpoint=$MASTER_ADDR:$MASTER_PORT \
  -m torchtitan.train \
  --job.config_file ./llama3_70b.toml
```

**Step 3: Submit job**

```bash
sbatch multinode_trainer.slurm
```

**Step 4: Resume from checkpoint**

Training auto-resumes if checkpoint exists in configured folder.

### Workflow 3: Enable Float8 training for H100s

Float8 provides 30-50% speedup on H100 GPUs.

```
Float8 Training:
- [ ] Step 1: Install torchao
- [ ] Step 2: Configure Float8
- [ ] Step 3: Launch with compile
```

**Step 1: Install torchao**

```bash
USE_CPP=0 pip install git+https://github.com/pytorch/ao.git
```

**Step 2: Configure Float8**

Add to your TOML config:
```toml
[model]
converters = ["quantize.linear.float8"]

[quantize.linear.float8]
enable_fsdp_float8_all_gather = true
precompute_float8_dynamic_scale_for_fsdp = true
filter_fqns = ["output"]  # Exclude output layer

[compile]
enable = true
components = ["model", "loss"]
```

**Step 3: Launch with compile**

```bash
CONFIG_FILE="./llama3_8b.toml" ./run_train.sh \
  --model.converters="quantize.linear.float8" \
  --quantize.linear.float8.enable_fsdp_float8_all_gather \
  --compile.enable
```

### Workflow 4: 4D parallelism for 405B models

```
4D Parallelism (FSDP + TP + PP + CP):
- [ ] Step 1: Create seed checkpoint
- [ ] Step 2: Configure 4D parallelism
- [ ] Step 3: Launch on 512 GPUs
```

**Step 1: Create seed checkpoint**

Required for consistent initialization across PP stages:
```bash
NGPU=1 CONFIG_FILE=./llama3_405b.toml ./run_train.sh \
  --checkpoint.enable \
  --checkpoint.create_seed_checkpoint \
  --parallelism.data_parallel_shard_degree 1 \
  --parallelism.tensor_parallel_degree 1 \
  --parallelism.pipeline_parallel_degree 1
```

**Step 2: Configure 4D parallelism**

```toml
[parallelism]
data_parallel_shard_degree = 8   # FSDP
tensor_parallel_degree = 8       # TP within node
pipeline_parallel_degree = 8     # PP across nodes
context_parallel_degree = 1      # CP for long sequences

[training]
local_batch_size = 32
seq_len = 8192
```

**Step 3: Launch on 512 GPUs**

```bash
# 64 nodes x 8 GPUs = 512 GPUs
srun torchrun --nnodes=64 --nproc_per_node=8 \
  -m torchtitan.train \
  --job.config_file ./llama3_405b.toml
```

## When to use vs alternatives

**Use TorchTitan when:**
- Pretraining LLMs from scratch (8B to 405B+)
- Need PyTorch-native solution without third-party dependencies
- Require composable 4D parallelism (FSDP2, TP, PP, CP)
- Training on H100s with Float8 support
- Want interoperable checkpoints with torchtune/HuggingFace

**Use alternatives instead:**
- **Megatron-LM**: Maximum performance for NVIDIA-only deployments
- **DeepSpeed**: Broader ZeRO optimization ecosystem, inference support
- **Axolotl/TRL**: Fine-tuning rather than pretraining
- **LitGPT**: Educational, smaller-scale training

## Common issues

**Issue: Out of memory on large models**

Enable activation checkpointing and reduce batch size:
```toml
[activation_checkpoint]
mode = "full"  # Instead of "selective"

[training]
local_batch_size = 1
```

Or use gradient accumulation:
```toml
[training]
local_batch_size = 1
global_batch_size = 32  # Accumulates gradients
```

**Issue: TP causes high memory with async collectives**

Set environment variable:
```bash
export TORCH_NCCL_AVOID_RECORD_STREAMS=1
```

**Issue: Float8 training not faster**

Float8 only benefits large GEMMs. Filter small layers:
```toml
[quantize.linear.float8]
filter_fqns = ["attention.wk", "attention.wv", "output", "auto_filter_small_kn"]
```

**Issue: Checkpoint loading fails after parallelism change**

Use DCP's resharding capability:
```bash
# Convert sharded checkpoint to single file
python -m torch.distributed.checkpoint.format_utils \
  dcp_to_torch checkpoint/step-1000 checkpoint.pt
```

**Issue: Pipeline parallelism initialization**

Create seed checkpoint first (see Workflow 4, Step 1).

## Supported models

| Model | Sizes | Status |
|-------|-------|--------|
| Llama 3.1 | 8B, 70B, 405B | Production |
| Llama 4 | Various | Experimental |
| DeepSeek V3 | 16B, 236B, 671B (MoE) | Experimental |
| GPT-OSS | 20B, 120B (MoE) | Experimental |
| Qwen 3 | Various | Experimental |
| Flux | Diffusion | Experimental |

## Performance benchmarks (H100)

| Model | GPUs | Parallelism | TPS/GPU | Techniques |
|-------|------|-------------|---------|------------|
| Llama 8B | 8 | FSDP | 5,762 | Baseline |
| Llama 8B | 8 | FSDP+compile+FP8 | 8,532 | +48% |
| Llama 70B | 256 | FSDP+TP+AsyncTP | 876 | 2D parallel |
| Llama 405B | 512 | FSDP+TP+PP | 128 | 3D parallel |

## Advanced topics

**FSDP2 configuration**: See [references/fsdp.md](references/fsdp.md) for detailed FSDP2 vs FSDP1 comparison and ZeRO equivalents.

**Float8 training**: See [references/float8.md](references/float8.md) for tensorwise vs rowwise scaling recipes.

**Checkpointing**: See [references/checkpoint.md](references/checkpoint.md) for HuggingFace conversion and async checkpointing.

**Adding custom models**: See [references/custom-models.md](references/custom-models.md) for TrainSpec protocol.

## Resources

- GitHub: https://github.com/pytorch/torchtitan
- Paper: https://arxiv.org/abs/2410.06511
- ICLR 2025: https://iclr.cc/virtual/2025/poster/29620
- PyTorch Forum: https://discuss.pytorch.org/c/distributed/torchtitan/44



---


## 📚 Reference Materials


### Custom Models

# Adding Custom Models to TorchTitan

This guide explains how to add a new model to TorchTitan following the established patterns.

## Directory Structure

```
torchtitan/models/your_model/
├── model/
│   ├── __init__.py
│   ├── args.py          # Model arguments
│   ├── model.py         # Model definition
│   └── state_dict_adapter.py  # HF conversion (optional)
├── infra/
│   ├── __init__.py
│   ├── parallelize.py   # TP, FSDP, compile application
│   └── pipeline.py      # PP application (optional)
├── train_configs/
│   ├── debug_model.toml
│   └── your_model_XB.toml
├── __init__.py          # TrainSpec registration
└── README.md
```

## Step 1: Define Model Arguments

Inherit from `BaseModelArgs`:

```python
# model/args.py
from torchtitan.protocols.model import BaseModelArgs
from dataclasses import dataclass

@dataclass
class YourModelArgs(BaseModelArgs):
    dim: int = 4096
    n_layers: int = 32
    n_heads: int = 32
    vocab_size: int = 128256

    def get_nparams_and_flops(self, seq_len: int) -> tuple[int, int]:
        """Return (num_params, flops_per_token) for throughput calculation."""
        nparams = self.vocab_size * self.dim + ...  # Calculate params
        flops = 6 * nparams  # Approximate: 6 * params for forward+backward
        return nparams, flops

    def update_from_config(self, job_config) -> "YourModelArgs":
        """Update args from training config."""
        # Override specific args from job_config if needed
        return self
```

## Step 2: Define Model

Inherit from `ModelProtocol`:

```python
# model/model.py
import torch.nn as nn
from torchtitan.protocols.model import ModelProtocol
from .args import YourModelArgs

class YourModel(ModelProtocol):
    def __init__(self, args: YourModelArgs):
        super().__init__()
        self.args = args
        self.tok_embeddings = nn.Embedding(args.vocab_size, args.dim)
        self.layers = nn.ModuleDict({
            str(i): TransformerBlock(args) for i in range(args.n_layers)
        })
        self.norm = RMSNorm(args.dim)
        self.output = nn.Linear(args.dim, args.vocab_size, bias=False)

    def forward(self, tokens: torch.Tensor) -> torch.Tensor:
        h = self.tok_embeddings(tokens)
        for layer in self.layers.values():
            h = layer(h)
        h = self.norm(h)
        return self.output(h)

    def init_weights(self):
        """Initialize weights recursively."""
        for module in self.modules():
            if hasattr(module, 'init_weights') and module is not self:
                module.init_weights()
            elif isinstance(module, nn.Linear):
                nn.init.normal_(module.weight, std=0.02)
```

**Important guidelines**:
- Write single-device model code (parallelism applied externally)
- Use `nn.ModuleDict` for layers (preserves FQNs when deleting for PP)
- Make input/output layers optional for PP compatibility
- Define `init_weights()` recursively

## Step 3: Parallelize Function

```python
# infra/parallelize.py
from torch.distributed._composable.fsdp import fully_shard
from torch.distributed.tensor.parallel import parallelize_module

def parallelize_your_model(
    model: YourModel,
    world_mesh: DeviceMesh,
    parallel_dims: ParallelDims,
    job_config: JobConfig,
):
    # Apply in this order: TP -> AC -> compile -> FSDP

    # 1. Tensor Parallelism
    if parallel_dims.tp_enabled:
        apply_tp(model, world_mesh["tp"], job_config)

    # 2. Activation Checkpointing
    if job_config.activation_checkpoint.mode == "full":
        apply_ac(model, job_config)

    # 3. torch.compile
    if job_config.compile.enable:
        model = torch.compile(model)

    # 4. FSDP
    if parallel_dims.dp_enabled:
        apply_fsdp(model, world_mesh["dp"], job_config)

    return model
```

## Step 4: Create TrainSpec

```python
# __init__.py
from torchtitan.protocols.train_spec import TrainSpec, register_train_spec
from .model.model import YourModel
from .model.args import YourModelArgs
from .infra.parallelize import parallelize_your_model

MODEL_CONFIGS = {
    "8B": YourModelArgs(dim=4096, n_layers=32, n_heads=32),
    "70B": YourModelArgs(dim=8192, n_layers=80, n_heads=64),
}

def get_train_spec(flavor: str) -> TrainSpec:
    return TrainSpec(
        model_cls=YourModel,
        model_args=MODEL_CONFIGS[flavor],
        parallelize_fn=parallelize_your_model,
        pipeline_fn=None,  # Or your_pipeline_fn for PP
        build_optimizer_fn=build_optimizer,  # Reuse existing
        build_lr_scheduler_fn=build_lr_scheduler,  # Reuse existing
        build_dataloader_fn=build_dataloader,  # Reuse existing
        build_tokenizer_fn=build_tokenizer,  # Reuse existing
        build_loss_fn=build_loss,  # Reuse existing
        state_dict_adapter=None,  # Or YourStateDictAdapter
    )

# Register so train.py can find it
register_train_spec("your_model", get_train_spec)
```

## Step 5: State Dict Adapter (Optional)

For HuggingFace checkpoint conversion:

```python
# model/state_dict_adapter.py
from torchtitan.protocols.state_dict_adapter import BaseStateDictAdapter

class YourStateDictAdapter(BaseStateDictAdapter):
    def to_hf(self, state_dict: dict) -> dict:
        """Convert torchtitan state dict to HF format."""
        hf_state_dict = {}
        for key, value in state_dict.items():
            hf_key = self._convert_key_to_hf(key)
            hf_state_dict[hf_key] = value
        return hf_state_dict

    def from_hf(self, state_dict: dict) -> dict:
        """Convert HF state dict to torchtitan format."""
        tt_state_dict = {}
        for key, value in state_dict.items():
            tt_key = self._convert_key_from_hf(key)
            tt_state_dict[tt_key] = value
        return tt_state_dict
```

## Step 6: Training Config

```toml
# train_configs/your_model_8b.toml
[job]
dump_folder = "./outputs"
description = "Your Model 8B training"

[model]
name = "your_model"
flavor = "8B"

[optimizer]
name = "AdamW"
lr = 3e-4

[training]
local_batch_size = 2
seq_len = 8192
steps = 1000
dataset = "c4"

[parallelism]
data_parallel_shard_degree = -1
tensor_parallel_degree = 1
```

## Step 7: Register Model

Add to `torchtitan/models/__init__.py`:

```python
from .your_model import get_train_spec as get_your_model_train_spec

MODEL_REGISTRY["your_model"] = get_your_model_train_spec
```

## Testing

### Numerics Test

Compare output with HuggingFace implementation:

```python
def test_numerics():
    # Load same checkpoint into both implementations
    tt_model = YourModel(args).load_checkpoint(...)
    hf_model = HFYourModel.from_pretrained(...)

    # Compare outputs
    input_ids = torch.randint(0, vocab_size, (1, 128))
    tt_output = tt_model(input_ids)
    hf_output = hf_model(input_ids).logits

    torch.testing.assert_close(tt_output, hf_output, atol=1e-4, rtol=1e-4)
```

### Loss Convergence

Compare loss curves with verified baseline (see `docs/converging.md`).

### Performance Benchmark

Add benchmark config to `benchmarks/` folder.

## Guiding Principles

1. **Readability over flexibility**: Don't over-abstract
2. **Minimal model changes**: Parallelism applied externally
3. **Clean, minimal codebase**: Reuse existing components where possible
4. **Single-device semantics**: Model code should work on single GPU




### Checkpoint

# Checkpointing in TorchTitan

TorchTitan uses PyTorch Distributed Checkpoint (DCP) for fault-tolerant, interoperable checkpointing.

## Basic Configuration

```toml
[checkpoint]
enable = true
folder = "checkpoint"
interval = 500
```

## Save Model Only (Smaller Checkpoints)

Exclude optimizer state and training metadata:

```toml
[checkpoint]
enable = true
last_save_model_only = true
export_dtype = "bfloat16"  # Optional: export in lower precision
```

## Excluding Keys from Loading

Partial checkpoint loading for modified settings:

```toml
[checkpoint]
enable = true
exclude_from_loading = ["data_loader", "lr_scheduler"]
```

CLI equivalent:
```bash
--checkpoint.exclude_from_loading data_loader,lr_scheduler
```

## Creating Seed Checkpoints

Required for Pipeline Parallelism to ensure consistent initialization:

```bash
NGPU=1 CONFIG_FILE=<path_to_config> ./run_train.sh \
  --checkpoint.enable \
  --checkpoint.create_seed_checkpoint \
  --parallelism.data_parallel_replicate_degree 1 \
  --parallelism.data_parallel_shard_degree 1 \
  --parallelism.tensor_parallel_degree 1 \
  --parallelism.pipeline_parallel_degree 1 \
  --parallelism.context_parallel_degree 1 \
  --parallelism.expert_parallel_degree 1
```

This initializes on single CPU for reproducible initialization across any GPU count.

## Async Checkpointing

Reduce checkpoint overhead with async writes:

```toml
[checkpoint]
enable = true
async_mode = "async"  # Options: "disabled", "async", "async_with_pinned_mem"
```

## HuggingFace Conversion

### During Training

Save directly in HuggingFace format:

```toml
[checkpoint]
last_save_in_hf = true
last_save_model_only = true
```

Load from HuggingFace:

```toml
[checkpoint]
initial_load_in_hf = true

[model]
hf_assets_path = "./path/to/hf/checkpoint"
```

### Offline Conversion

Convert without running training:

```bash
# HuggingFace -> TorchTitan
python ./scripts/checkpoint_conversion/convert_from_hf.py \
  <input_dir> <output_dir> \
  --model_name llama3 \
  --model_flavor 8B

# TorchTitan -> HuggingFace
python ./scripts/checkpoint_conversion/convert_to_hf.py \
  <input_dir> <output_dir> \
  --hf_assets_path ./assets/hf/Llama3.1-8B \
  --model_name llama3 \
  --model_flavor 8B
```

### Example

```bash
python ./scripts/convert_from_hf.py \
  ~/.cache/huggingface/hub/models--meta-llama--Meta-Llama-3-8B/snapshots/8cde5ca8380496c9a6cc7ef3a8b46a0372a1d920/ \
  ./initial_load_path/ \
  --model_name llama3 \
  --model_flavor 8B
```

## Converting to Single .pt File

Convert DCP sharded checkpoint to single PyTorch file:

```bash
python -m torch.distributed.checkpoint.format_utils \
  dcp_to_torch \
  torchtitan/outputs/checkpoint/step-1000 \
  checkpoint.pt
```

## Checkpoint Structure

DCP saves sharded checkpoints that can be resharded for different parallelism configurations:

```
checkpoint/
├── step-500/
│   ├── .metadata
│   ├── __0_0.distcp
│   ├── __0_1.distcp
│   └── ...
└── step-1000/
    └── ...
```

## Resume Training

Training auto-resumes from the latest checkpoint in the configured folder. To resume from a specific step:

```toml
[checkpoint]
load_step = 500  # Resume from step 500
```

## Interoperability with TorchTune

Checkpoints saved with `last_save_model_only = true` can be loaded directly into [torchtune](https://github.com/pytorch/torchtune) for fine-tuning.

## Full Configuration Example

```toml
[checkpoint]
enable = true
folder = "checkpoint"
interval = 500
load_step = -1  # -1 = latest, or specify step number
last_save_model_only = true
export_dtype = "bfloat16"
async_mode = "async"
exclude_from_loading = []
last_save_in_hf = false
initial_load_in_hf = false
create_seed_checkpoint = false
```

## Best Practices

1. **Large models**: Use `async_mode = "async"` to overlap checkpoint saves with training
2. **Fine-tuning export**: Enable `last_save_model_only` and `export_dtype = "bfloat16"` for smaller files
3. **Pipeline parallelism**: Always create seed checkpoint first
4. **Debugging**: Save frequent checkpoints during development, reduce for production
5. **HF interop**: Use conversion scripts for offline conversion, direct save/load for training workflows




### Fsdp

# FSDP2 in TorchTitan

## Why FSDP2?

FSDP2 is a rewrite of PyTorch's Fully Sharded Data Parallel (FSDP) API, removing the `FlatParameter` abstraction for better composability and simpler implementation.

### Key improvements over FSDP1

- **DTensor-based sharding**: Sharded parameters are `DTensor`s on dim-0, enabling easy manipulation and communication-free sharded state dicts
- **Better memory management**: Deterministic and lower GPU memory (7% reduction) by avoiding `recordStream`
- **Simplified API**: Fewer arguments, no wrapper class

### Performance

On Llama-7B with 8x H100s, FSDP2 achieves higher MFU with 7% lower peak memory than FSDP1, matching the same loss curve.

## API Reference

```python
from torch.distributed._composable.fsdp import fully_shard, MixedPrecisionPolicy, OffloadPolicy

@contract(state_cls=FSDPState)
def fully_shard(
    module: nn.Module,
    *,
    mesh: Optional[DeviceMesh] = None,
    reshard_after_forward: Union[bool, int] = True,
    mp_policy: MixedPrecisionPolicy = MixedPrecisionPolicy(),
    offload_policy: OffloadPolicy = OffloadPolicy(),
) -> nn.Module:
```

## Sharding Strategies (ZeRO Equivalents)

| FSDP2 Configuration | FSDP1 Equivalent | DeepSpeed |
|---------------------|------------------|-----------|
| 1D mesh + `reshard_after_forward=True` | FULL_SHARD | ZeRO-3 |
| 1D mesh + `reshard_after_forward=False` | SHARD_GRAD_OP | ZeRO-2 |
| 2D mesh + `reshard_after_forward=True` | HYBRID_SHARD | MiCS |
| 1D/2D mesh + `reshard_after_forward=8` (int) | - | ZeRO++ hpZ |

## Meta-Device Initialization

FSDP2 supports materializing tensors onto GPU _after_ sharding:

```python
# Initialize on meta device (no memory)
with torch.device("meta"):
    model = Transformer()

# Apply FSDP2 sharding
for module in model.modules():
    if isinstance(module, TransformerBlock):
        fully_shard(module)
fully_shard(model)

# Parameters still on meta device
for tensor in itertools.chain(model.parameters(), model.buffers()):
    assert tensor.device == torch.device("meta")

# Allocate sharded parameters on GPU
model.to_empty(device="cuda")

# Initialize weights
model.init_weights()
```

## State Dict Differences

| Operation | FSDP1 | FSDP2 |
|-----------|-------|-------|
| `model.state_dict()` | Full state dict | Sharded state dict (no communication) |
| `optim.state_dict()` | Local state dict | Sharded state dict (no communication) |
| `summon_full_params()` | Supported | Use `DTensor` APIs like `full_tensor()` |
| Gradient clipping | `FSDP.clip_grad_norm_()` | `nn.utils.clip_grad_norm_()` |

## Mixed Precision

```python
from torch.distributed._composable.fsdp import MixedPrecisionPolicy

mp_policy = MixedPrecisionPolicy(
    param_dtype=torch.bfloat16,
    reduce_dtype=torch.float32,
    output_dtype=torch.bfloat16,
    cast_forward_inputs=True,
)

fully_shard(model, mp_policy=mp_policy)
```

## HSDP (Hybrid Sharded Data Parallel)

For 2D parallelism with replication + sharding:

```python
from torch.distributed.device_mesh import init_device_mesh

# Replicate across 4 groups, shard within 8 GPUs each
mesh = init_device_mesh("cuda", (4, 8), mesh_dim_names=("replicate", "shard"))

fully_shard(model, mesh=mesh)
```

## Configuration in TorchTitan

```toml
[parallelism]
# FSDP sharding degree (-1 = auto, use all available GPUs)
data_parallel_shard_degree = -1

# HSDP replication degree (1 = pure FSDP, >1 = HSDP)
data_parallel_replicate_degree = 1
```

## Removed Arguments from FSDP1

These FSDP1 arguments are no longer needed:

- `auto_wrap_policy`: Apply `fully_shard` directly to modules
- `backward_prefetch`: Always uses BACKWARD_PRE
- `param_init_fn`: Use meta-device initialization
- `device_id`: Uses mesh's device automatically
- `sync_module_states`: Not needed with DTensor
- `limit_all_gathers`: New memory management doesn't need it
- `use_orig_params`: Always true (no FlatParameter)




### Float8

# Float8 Training in TorchTitan

Float8 training provides substantial speedups for models where GEMMs are large enough that the FP8 tensorcore speedup outweighs dynamic quantization overhead.

## Hardware Requirements

- NVIDIA H100 or newer GPUs (FP8 Tensor Cores)
- Blackwell GPUs for MXFP8 training

## Installation

```bash
USE_CPP=0 pip install git+https://github.com/pytorch/ao.git
```

## Usage: Tensorwise Scaling

Standard Float8 with tensorwise dynamic scaling:

```bash
CONFIG_FILE="./torchtitan/models/llama3/train_configs/llama3_8b.toml" ./run_train.sh \
  --model.converters="quantize.linear.float8" \
  --quantize.linear.float8.enable_fsdp_float8_all_gather \
  --quantize.linear.float8.precompute_float8_dynamic_scale_for_fsdp \
  --compile.enable
```

### Key Arguments

| Argument | Description |
|----------|-------------|
| `--model.converters="quantize.linear.float8"` | Swap `nn.Linear` with `Float8Linear` |
| `--quantize.linear.float8.enable_fsdp_float8_all_gather` | Communicate in float8 to save bandwidth |
| `--quantize.linear.float8.precompute_float8_dynamic_scale_for_fsdp` | Single all-reduce for all AMAX/scales |
| `--compile.enable` | Required - fuses float8 scaling/casting kernels |

## Usage: Rowwise Scaling

Higher accuracy than tensorwise scaling:

```bash
CONFIG_FILE="./torchtitan/models/llama3/train_configs/llama3_8b.toml" ./run_train.sh \
  --model.converters="quantize.linear.float8" \
  --quantize.linear.float8.recipe_name rowwise \
  --compile.enable
```

## Filtering Layers

Not all layers benefit from Float8. Filter small layers:

```bash
--quantize.linear.float8.filter_fqns="attention.wk,attention.wv,output"
```

### Auto-filtering

Automatically skip layers too small to benefit:

```bash
--quantize.linear.float8.filter_fqns="auto_filter_small_kn"
```

Thresholds based on H100 microbenchmarks where speedup > overhead.

## TOML Configuration

```toml
[model]
converters = ["quantize.linear.float8"]

[quantize.linear.float8]
enable_fsdp_float8_all_gather = true
precompute_float8_dynamic_scale_for_fsdp = true
filter_fqns = ["output", "auto_filter_small_kn"]

[compile]
enable = true
components = ["model", "loss"]
```

## How Float8 Works with Distributed Training

### Single Device

Cast input and weight to float8 inside forward before calling `torch._scaled_mm`:

```python
# Float8 matmul requires scales
torch._scaled_mm(input_fp8, weight_fp8, scale_a=scale_input, scale_b=scale_weight)
```

### FSDP + Float8

1. Cast sharded high-precision weights (1/N per rank) to float8
2. Perform float8 all-gather (saves bandwidth vs bf16/fp32)
3. Communicate `max(abs)` across ranks for scale computation
4. At forward start, have unsharded float8 weights ready

**Net benefit**: Float8 all-gather + amax communication can beat bf16/fp32 all-gather, depending on world size and message size.

### TP + Float8

- **Input**: Cast sharded input to float8, all-gather in float8
- **Weights**: Communicate `max(abs)` for sharded weights
- **Matmul**: Float8 input (unsharded) x float8 weight (sharded) with global scales

## Scaling Strategies

| Strategy | Status | Description |
|----------|--------|-------------|
| Tensorwise dynamic | Stable | Single scale per tensor |
| Rowwise dynamic | Alpha | Scale per row, higher accuracy |

## Performance Gains

From benchmarks on H100:

| Configuration | TPS/GPU | vs Baseline |
|---------------|---------|-------------|
| FSDP only | 5,762 | - |
| FSDP + compile | 6,667 | +16% |
| FSDP + compile + Float8 | 8,532 | +48% |

## Determining Float8 Benefit

Check [torchao microbenchmarks](https://github.com/pytorch/ao/tree/main/torchao/float8#performance) for forward+backward pass speedups on "layer norm => linear => sigmoid" for different M,N,K sizes.

Rule of thumb: GEMMs with K,N > 4096 typically benefit from Float8.

## MXFP8 Training (Blackwell)

For NVIDIA Blackwell GPUs, TorchTitan supports MXFP8 (Microscaling FP8) for both dense and MoE models. See [docs/mxfp8.md](https://github.com/pytorch/torchtitan/blob/main/docs/mxfp8.md) for details.




---

## 🚀 Usage

**Reference this template:** `@skill-distributed-llm-pretraining-torchtitan.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
