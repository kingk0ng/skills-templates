---
id: skill-slime-rl-training
type: skill
name: slime-rl-training
description: Provides guidance for LLM post-training with RL using slime, a Megatron+SGLang
  framework. Use when training GLM models, implementing custom data generation workflows,
  or needing tight Megatron-LM integration for RL scaling.
category: ai-research
complexity: medium
keywords:
- docker
- git
- github
- python
capabilities: []
token_estimate: 1608
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,608 -->
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




# slime-rl-training

> Provides guidance for LLM post-training with RL using slime, a Megatron+SGLang framework. Use when training GLM models, implementing custom data generation workflows, or needing tight Megatron-LM integration for RL scaling.

# slime: LLM Post-Training Framework for RL Scaling

slime is an LLM post-training framework from Tsinghua's THUDM team, powering GLM-4.5, GLM-4.6, and GLM-4.7. It connects Megatron-LM for training with SGLang for high-throughput rollout generation.

## When to Use slime

**Choose slime when you need:**
- Megatron-LM native training with SGLang inference
- Custom data generation workflows with flexible data buffers
- Training GLM, Qwen3, DeepSeek V3, or Llama 3 models
- Research-grade framework with production backing (Z.ai)

**Consider alternatives when:**
- You need enterprise-grade stability features → use **miles**
- You want flexible backend swapping → use **verl**
- You need PyTorch-native abstractions → use **torchforge**

## Key Features

- **Training**: Megatron-LM with full parallelism support (TP, PP, DP, SP)
- **Rollout**: SGLang-based high-throughput generation with router
- **Data Buffer**: Flexible prompt management and sample storage
- **Models**: GLM-4.x, Qwen3, DeepSeek V3/R1, Llama 3

## Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    Data Buffer                          │
│ - Prompt initialization and management                  │
│ - Custom data generation and filtering                  │
│ - Rollout sample storage                                │
└─────────────┬───────────────────────────┬───────────────┘
              │                           │
┌─────────────▼───────────┐ ┌─────────────▼───────────────┐
│ Training (Megatron-LM)  │ │ Rollout (SGLang + Router)   │
│ - Actor model training  │ │ - Response generation       │
│ - Critic (optional)     │ │ - Reward/verifier output    │
│ - Weight sync to rollout│ │ - Multi-turn support        │
└─────────────────────────┘ └─────────────────────────────┘
```

## Installation

```bash
# Recommended: Docker
docker pull slimerl/slime:latest
docker run --rm --gpus all --ipc=host --shm-size=16g \
  -it slimerl/slime:latest /bin/bash

# Inside container
cd /root/slime && pip install -e . --no-deps
```

### From Source

```bash
git clone https://github.com/THUDM/slime.git
cd slime
pip install -r requirements.txt
pip install -e .
```

## Quick Start: GRPO Training

```bash
# Source model configuration
source scripts/models/qwen3-4B.sh

# Launch training
python train.py \
    --actor-num-nodes 1 \
    --actor-num-gpus-per-node 4 \
    --rollout-num-gpus 4 \
    --advantage-estimator grpo \
    --use-kl-loss --kl-loss-coef 0.001 \
    --rollout-batch-size 32 \
    --n-samples-per-prompt 8 \
    --global-batch-size 256 \
    --num-rollout 3000 \
    --prompt-data /path/to/data.jsonl \
    ${MODEL_ARGS[@]} ${CKPT_ARGS[@]}
```

---

## Workflow 1: Standard GRPO Training

Use this workflow for training reasoning models with group-relative advantages.

### Prerequisites Checklist
- [ ] Docker environment or Megatron-LM + SGLang installed
- [ ] Model checkpoint (HuggingFace or Megatron format)
- [ ] Training data in JSONL format

### Step 1: Prepare Data

```python
# data.jsonl format
{"prompt": "What is 2 + 2?", "label": "4"}
{"prompt": "Solve: 3x = 12", "label": "x = 4"}
```

Or with chat format:
```python
{
    "prompt": [
        {"role": "system", "content": "You are a math tutor."},
        {"role": "user", "content": "What is 15 + 27?"}
    ],
    "label": "42"
}
```

### Step 2: Configure Model

Choose a pre-configured model script:

```bash
# List available models
ls scripts/models/
# glm4-9B.sh, qwen3-4B.sh, qwen3-30B-A3B.sh, deepseek-v3.sh, llama3-8B.sh, ...

# Source your model
source scripts/models/qwen3-4B.sh
```

### Step 3: Launch Training

```bash
python train.py \
    --actor-num-nodes 1 \
    --actor-num-gpus-per-node 8 \
    --rollout-num-gpus 8 \
    --advantage-estimator grpo \
    --use-kl-loss \
    --kl-loss-coef 0.001 \
    --prompt-data /path/to/train.jsonl \
    --input-key prompt \
    --label-key label \
    --apply-chat-template \
    --rollout-batch-size 32 \
    --n-samples-per-prompt 8 \
    --global-batch-size 256 \
    --num-rollout 3000 \
    --save-interval 100 \
    --eval-interval 50 \
    ${MODEL_ARGS[@]}
```

### Step 4: Monitor Training
- [ ] Check TensorBoard: `tensorboard --logdir outputs/`
- [ ] Verify reward curves are increasing
- [ ] Monitor GPU utilization across nodes

---

## Workflow 2: Asynchronous Training

Use async mode for higher throughput by overlapping rollout and training.

### When to Use Async
- Large models with long generation times
- High GPU idle time in synchronous mode
- Sufficient memory for buffering

### Launch Async Training

```bash
python train_async.py \
    --actor-num-nodes 1 \
    --actor-num-gpus-per-node 8 \
    --rollout-num-gpus 8 \
    --advantage-estimator grpo \
    --async-buffer-size 4 \
    --prompt-data /path/to/train.jsonl \
    ${MODEL_ARGS[@]}
```

### Async-Specific Parameters

```bash
--async-buffer-size 4        # Number of rollouts to buffer
--update-weights-interval 2  # Sync weights every N rollouts
```

---

## Workflow 3: Multi-Turn Agentic Training

Use this workflow for training agents with tool use or multi-step reasoning.

### Prerequisites
- [ ] Custom generate function for multi-turn logic
- [ ] Tool/environment interface

### Step 1: Define Custom Generate Function

```python
# custom_generate.py
async def custom_generate(args, samples, evaluation=False):
    """Multi-turn generation with tool calling."""
    for sample in samples:
        conversation = sample.prompt

        for turn in range(args.max_turns):
            # Generate response
            response = await generate_single(conversation)

            # Check for tool call
            tool_call = extract_tool_call(response)
            if tool_call:
                tool_result = execute_tool(tool_call)
                conversation.append({"role": "assistant", "content": response})
                conversation.append({"role": "tool", "content": tool_result})
            else:
                break

        sample.response = response
        sample.reward = compute_reward(sample)

    return samples
```

### Step 2: Launch with Custom Function

```bash
python train.py \
    --custom-generate-function-path custom_generate.py \
    --max-turns 5 \
    --prompt-data /path/to/agent_data.jsonl \
    ${MODEL_ARGS[@]}
```

See `examples/search-r1/` for a complete multi-turn search example.

---

## Configuration Reference

### Three Argument Categories

slime uses three types of arguments:

**1. Megatron Arguments** (passed directly):
```bash
--tensor-model-parallel-size 2
--pipeline-model-parallel-size 1
--num-layers 32
--hidden-size 4096
```

**2. SGLang Arguments** (prefixed with `--sglang-`):
```bash
--sglang-mem-fraction-static 0.8
--sglang-context-length 8192
--sglang-log-level INFO
```

**3. slime Arguments**:
```bash
# Resource allocation
--actor-num-nodes 1
--actor-num-gpus-per-node 8
--rollout-num-gpus 8
--colocate  # Share GPUs between training/inference

# Data
--prompt-data /path/to/data.jsonl
--input-key prompt
--label-key label

# Training loop
--num-rollout 3000
--rollout-batch-size 32
--n-samples-per-prompt 8
--global-batch-size 256

# Algorithm
--advantage-estimator grpo  # or: gspo, ppo, reinforce_plus_plus
--use-kl-loss
--kl-loss-coef 0.001
```

### Key Constraints

```
rollout_batch_size × n_samples_per_prompt = global_batch_size × num_steps_per_rollout
```

Example: 32 × 8 = 256 × 1

---

## Data Buffer System

slime's data buffer enables flexible data management:

### Basic Data Source

```python
class RolloutDataSource:
    def get_samples(self, num_samples):
        """Fetch prompts from dataset."""
        return self.dataset.sample(num_samples)

    def add_samples(self, samples):
        """Called after generation (no-op by default)."""
        pass
```

### Buffered Data Source (Off-Policy)

```python
class RolloutDataSourceWithBuffer(RolloutDataSource):
    def __init__(self):
        self.buffer = []

    def add_samples(self, samples):
        """Store generated samples for reuse."""
        self.buffer.extend(samples)

    def buffer_filter(self, args, buffer, num_samples):
        """Custom selection logic (prioritized, stratified, etc.)."""
        return select_best(buffer, num_samples)
```

---

## Common Issues and Solutions

### Issue: SGLang Engine Crash

**Symptoms**: Inference engine dies mid-training

**Solutions**:
```bash
# Enable fault tolerance
--use-fault-tolerance

# Increase memory allocation
--sglang-mem-fraction-static 0.85

# Reduce batch size
--rollout-batch-size 16
```

### Issue: Weight Sync Timeout

**Symptoms**: Training hangs after rollout

**Solutions**:
```bash
# Increase sync interval
--update-weights-interval 5

# Use colocated mode (no network transfer)
--colocate
```

### Issue: OOM During Training

**Symptoms**: CUDA OOM in backward pass

**Solutions**:
```bash
# Enable gradient checkpointing
--recompute-activations

# Reduce micro-batch size
--micro-batch-size 1

# Enable sequence parallelism
--sequence-parallel
```

### Issue: Slow Data Loading

**Symptoms**: GPU idle during data fetch

**Solutions**:
```bash
# Increase data workers
--num-data-workers 4

# Use streaming dataset
--streaming-data
```

---

## Supported Models

| Model Family | Configurations |
|--------------|----------------|
| GLM | GLM-4.5, GLM-4.6, GLM-4.7, GLM-Z1-9B |
| Qwen | Qwen3 (4B, 8B, 30B-A3B), Qwen3-MoE, Qwen2.5 |
| DeepSeek | V3, V3.1, R1 |
| Llama | Llama 3 (8B, 70B) |
| Others | Kimi K2, Moonlight-16B |

Each model has pre-configured scripts in `scripts/models/`.

---

## Advanced Topics

### Co-location Mode

Share GPUs between training and inference to reduce memory:

```bash
python train.py \
    --colocate \
    --actor-num-gpus-per-node 8 \
    --sglang-mem-fraction-static 0.4 \
    ${MODEL_ARGS[@]}
```

### Custom Reward Model

```python
# custom_rm.py
class CustomRewardModel:
    def __init__(self, model_path):
        self.model = load_model(model_path)

    def compute_reward(self, prompts, responses):
        inputs = self.tokenize(prompts, responses)
        scores = self.model(inputs)
        return scores.tolist()
```

```bash
--custom-rm-path custom_rm.py
```

### Evaluation Multi-Task

```bash
--eval-prompt-data aime /path/to/aime.jsonl \
--eval-prompt-data gsm8k /path/to/gsm8k.jsonl \
--n-samples-per-eval-prompt 16
```

---

## Resources

- **Documentation**: https://thudm.github.io/slime/
- **GitHub**: https://github.com/THUDM/slime
- **Blog**: https://lmsys.org/blog/2025-07-09-slime/
- **Examples**: See `examples/` directory for 14+ worked examples



---


## 📚 Reference Materials


### Api Reference

# slime API Reference

## Architecture Overview

slime operates with a three-module architecture orchestrated by Ray:

```
┌─────────────────────────────────────────────────────────┐
│                    Data Buffer                          │
│ - Prompt initialization and management                  │
│ - Custom data generation and filtering                  │
│ - Rollout sample storage                                │
└─────────────┬───────────────────────────┬───────────────┘
              │                           │
┌─────────────▼───────────┐ ┌─────────────▼───────────────┐
│ Training (Megatron-LM)  │ │ Rollout (SGLang + Router)   │
│ - Actor model training  │ │ - Response generation       │
│ - Critic (optional)     │ │ - Reward/verifier output    │
│ - Weight sync to rollout│ │ - Multi-turn support        │
└─────────────────────────┘ └─────────────────────────────┘
```

## Core Data Structures

### Sample Object

The `Sample` object is the core data structure defined in `slime/utils/types.py`:

```python
from slime.utils.types import Sample

@dataclass
class Sample:
    # Core fields
    group_index: Optional[int]              # Group index for batching
    index: Optional[int]                    # Sample index
    prompt: str | list[dict] = ""           # Input prompt or chat history
    tokens: list[int] = field(default_factory=list)  # Token IDs
    response: str = ""                      # Generated response
    response_length: int = 0                # Response length in tokens
    label: Optional[str] = None             # Ground truth label
    reward: Optional[float | dict] = None   # RL reward signal
    loss_mask: Optional[list[int]] = None   # 1=compute loss, 0=mask
    status: Status = Status.PENDING         # Sample status
    metadata: dict = field(default_factory=dict)  # Custom data

    # Multimodal support
    multimodal_inputs: Optional[Any] = None       # Raw multimodal data (images, videos)
    multimodal_train_inputs: Optional[Any] = None # Processed multimodal data (pixel_values)

    # Rollout tracking
    weight_versions: list[str] = field(default_factory=list)
    rollout_log_probs: Optional[list[float]] = None    # Log probs from SGLang
    rollout_routed_experts: Optional[list[list[int]]] = None  # Expert routing (MoE)

    # Control fields
    remove_sample: bool = False
    generate_function_path: Optional[str] = None
    train_metadata: Optional[dict] = None
    non_generation_time: float = 0.0

    # Speculative decoding info (nested dataclass)
    @dataclass
    class SpecInfo:
        spec_accept_token_num: int = 0
        spec_draft_token_num: int = 0
        spec_verify_ct: int = 0
        completion_token_num: int = 0
```

### Status Enum

```python
class Status(Enum):
    PENDING = "pending"           # Not yet processed
    COMPLETED = "completed"       # Successfully generated
    TRUNCATED = "truncated"       # Hit max length
    ABORTED = "aborted"           # Failed generation
    FAILED = "failed"             # Generation failed
```

## Configuration System

slime uses three categories of command-line arguments:

### 1. Megatron Arguments

All Megatron-LM arguments are supported directly:

```bash
--tensor-model-parallel-size 2
--pipeline-model-parallel-size 1
--num-layers 32
--hidden-size 4096
--num-attention-heads 32
--seq-length 4096
--micro-batch-size 1
--global-batch-size 256
```

### 2. SGLang Arguments

SGLang arguments are prefixed with `--sglang-`:

```bash
--sglang-mem-fraction-static 0.8   # GPU memory for KV cache
--sglang-context-length 8192       # Maximum context length
--sglang-log-level INFO            # Logging verbosity
--sglang-tp-size 2                 # Tensor parallelism
--sglang-disable-cuda-graph        # Disable CUDA graphs
```

### 3. slime-Specific Arguments

Defined in `slime/utils/arguments.py`:

```bash
# Resource Allocation
--actor-num-nodes 1                # Training nodes
--actor-num-gpus-per-node 8        # GPUs per training node
--rollout-num-gpus 8               # Total rollout GPUs
--rollout-num-gpus-per-engine 2    # GPUs per SGLang engine
--colocate                         # Share GPUs for train/inference

# Data Configuration
--prompt-data /path/to/data.jsonl  # Training data path
--input-key prompt                 # Key for prompts in JSON
--label-key label                  # Key for labels in JSON
--apply-chat-template              # Apply chat formatting

# Training Loop
--num-rollout 3000                 # Total rollout iterations
--rollout-batch-size 32            # Prompts per rollout
--n-samples-per-prompt 8           # Responses per prompt
--global-batch-size 256            # Training batch size
--num-steps-per-rollout 1          # Training steps per rollout

# RL Algorithm
--advantage-estimator grpo         # grpo, gspo, ppo, reinforce_plus_plus
--use-kl-loss                      # Enable KL loss
--kl-loss-coef 0.001               # KL coefficient
--calculate-per-token-loss         # Token-level loss

# Off-Policy Options
--use-tis                          # Truncated Importance Sampling
--tis-threshold 0.9                # TIS threshold
--true-on-policy-mode              # Force on-policy training
```

## Data Buffer System

### RolloutDataSource (Base Class)

```python
from slime.data import RolloutDataSource

class RolloutDataSource:
    def __init__(self, dataset, args):
        self.dataset = dataset
        self.args = args

    def get_samples(self, num_samples: int) -> list[Sample]:
        """Fetch prompts from dataset."""
        return [Sample(prompt=p) for p in self.dataset.sample(num_samples)]

    def add_samples(self, samples: list[Sample]) -> None:
        """Called after generation (no-op by default)."""
        pass
```

### Buffered Data Source (Off-Policy)

```python
from slime.data import RolloutDataSourceWithBuffer

class RolloutDataSourceWithBuffer(RolloutDataSource):
    def __init__(self, dataset, args):
        super().__init__(dataset, args)
        self.buffer = []

    def add_samples(self, samples: list[Sample]) -> None:
        """Store generated samples for reuse."""
        self.buffer.extend(samples)

    def buffer_filter(self, args, buffer, num_samples) -> list[Sample]:
        """Custom selection logic."""
        # Example: prioritized sampling based on reward
        sorted_buffer = sorted(buffer, key=lambda s: s.reward, reverse=True)
        return sorted_buffer[:num_samples]
```

## Custom Functions

### Custom Generate Function

For multi-turn or tool-calling scenarios:

```python
# custom_generate.py
from slime.data import Sample

async def custom_generate(args, samples: list[Sample], evaluation: bool = False) -> list[Sample]:
    """
    Custom generation function for multi-turn interactions.

    Args:
        args: Training arguments
        samples: List of Sample objects with prompts
        evaluation: Whether this is an evaluation run

    Returns:
        List of Sample objects with responses and rewards
    """
    for sample in samples:
        conversation = sample.prompt if isinstance(sample.prompt, list) else [
            {"role": "user", "content": sample.prompt}
        ]

        for turn in range(args.max_turns):
            # Generate response
            response = await generate_single(conversation)

            # Check for tool call
            tool_call = extract_tool_call(response)
            if tool_call:
                # Execute tool
                tool_result = await execute_tool(tool_call)
                conversation.append({"role": "assistant", "content": response})
                conversation.append({"role": "tool", "content": tool_result})
            else:
                # Final response
                sample.response = response
                break

        # Compute reward
        sample.reward = compute_reward(sample)

        # Set loss mask (1 for model tokens, 0 for tool responses)
        sample.loss_mask = build_loss_mask(sample)

    return samples
```

Usage:
```bash
python train.py \
    --custom-generate-function-path custom_generate.py \
    --max-turns 5
```

### Custom Reward Function

```python
# custom_rm.py
from slime.data import Sample

async def reward_func(args, sample: Sample, **kwargs) -> float:
    """
    Compute reward for a single sample.

    Args:
        args: Training arguments
        sample: Sample object with response

    Returns:
        Reward score (float)
    """
    response = sample.response
    ground_truth = sample.label or sample.metadata.get("answer", "")

    # Example: exact match reward
    if response.strip() == ground_truth.strip():
        return 1.0
    return 0.0

# For batched processing (more efficient)
async def batched_custom_rm(args, samples: list[Sample]) -> list[float]:
    """Batch reward computation."""
    rewards = []
    for sample in samples:
        reward = await reward_func(args, sample)
        rewards.append(reward)
    return rewards
```

Usage:
```bash
python train.py \
    --custom-rm-path custom_rm.py \
    --group-rm  # Enable batched processing
```

## Model Configuration

### Pre-configured Model Scripts

Located in `scripts/models/`:

```bash
# List available models
ls scripts/models/
# glm4-9B.sh, qwen3-4B.sh, qwen3-30B-A3B.sh, deepseek-v3.sh, llama3-8B.sh

# Source model configuration
source scripts/models/qwen3-4B.sh
# This sets MODEL_ARGS and CKPT_ARGS arrays
```

### Example Model Script

```bash
# scripts/models/qwen3-4B.sh
export MODEL_ARGS=(
    --num-layers 36
    --hidden-size 2560
    --num-attention-heads 20
    --num-query-groups 4
    --ffn-hidden-size 6912
    --max-position-embeddings 32768
    --rotary-percent 1.0
    --rotary-base 1000000
    --swiglu
    --untie-embeddings-and-output-weights
    --no-position-embedding
    --normalization RMSNorm
    --tokenizer-type HuggingFaceTokenizer
    --bf16
)

export CKPT_ARGS=(
    --hf-checkpoint /path/to/qwen3-4b-hf
    --initial-megatron-checkpoint /path/to/megatron/ckpt
)
```

## Async Training

### Enabling Async Mode

```bash
python train_async.py \
    --actor-num-gpus-per-node 8 \
    --rollout-num-gpus 8 \
    --async-buffer-size 4 \
    --update-weights-interval 2 \
    ${MODEL_ARGS[@]}
```

### Async-Specific Parameters

```bash
--async-buffer-size 4            # Number of rollouts to buffer
--update-weights-interval 2      # Sync weights every N rollouts
```

**Note**: Colocated mode (`--colocate`) is NOT supported with async training.

## Evaluation

### Multi-Task Evaluation

```bash
--eval-prompt-data aime /path/to/aime.jsonl \
--eval-prompt-data gsm8k /path/to/gsm8k.jsonl \
--n-samples-per-eval-prompt 16 \
--eval-interval 50
```

### Evaluation Configuration

```bash
--eval-interval 50               # Evaluate every N rollouts
--n-samples-per-eval-prompt 16   # Samples for evaluation
--eval-temperature 0.0           # Greedy decoding for eval
```

## Supported Models

| Model Family | Configurations |
|--------------|----------------|
| GLM | GLM-4.5, GLM-4.6, GLM-4.7, GLM-Z1-9B |
| Qwen | Qwen3 (4B, 8B, 30B-A3B), Qwen3-MoE, Qwen2.5 |
| DeepSeek | V3, V3.1, R1 |
| Llama | Llama 3 (8B, 70B) |
| Others | Kimi K2, Moonlight-16B |

## Resources

- Documentation: https://thudm.github.io/slime/
- GitHub: https://github.com/THUDM/slime
- Blog: https://lmsys.org/blog/2025-07-09-slime/
- Examples: `examples/` directory (14+ worked examples)




### Troubleshooting

# slime Troubleshooting Guide

## Common Issues and Solutions

### SGLang Issues

#### Issue: SGLang Engine Crash

**Symptoms**: Inference engine dies mid-training, connection errors

**Solutions**:

1. **Enable fault tolerance**:
```bash
--use-fault-tolerance
```

2. **Increase memory allocation**:
```bash
--sglang-mem-fraction-static 0.85  # Increase from 0.8
```

3. **Reduce batch size**:
```bash
--rollout-batch-size 16  # Reduce from 32
```

4. **Disable CUDA graphs** (for debugging):
```bash
--sglang-disable-cuda-graph
```

#### Issue: SGLang Router Load Imbalance

**Symptoms**: Some SGLang engines overloaded while others idle

**Solutions**:

1. **Adjust routing strategy**:
```bash
--sglang-router-strategy round_robin
```

2. **Increase number of engines**:
```bash
--rollout-num-gpus-per-engine 1  # More engines, less GPUs each
```

### Weight Synchronization Issues

#### Issue: Weight Sync Timeout

**Symptoms**: Training hangs after rollout, timeout errors

**Solutions**:

1. **Increase sync interval** (async mode):
```bash
--update-weights-interval 5  # Increase from 2
```

2. **Use colocated mode** (eliminates network transfer):
```bash
--colocate
```

3. **Check network bandwidth**:
```bash
# Verify InfiniBand is enabled
ibstat
```

#### Issue: Weight Sync Failures in Multi-Node

**Symptoms**: Nodes fail to receive updated weights

**Solutions**:

1. **Set NCCL environment**:
```bash
export NCCL_DEBUG=INFO
export NCCL_SOCKET_IFNAME=eth0
export NCCL_IB_DISABLE=0
```

2. **Increase timeout**:
```bash
export NCCL_TIMEOUT=1800
```

### Memory Issues

#### Issue: OOM During Training

**Symptoms**: CUDA OOM in backward pass

**Solutions**:

1. **Enable gradient checkpointing**:
```bash
--recompute-activations
```

2. **Reduce micro-batch size**:
```bash
--micro-batch-size 1
```

3. **Enable sequence parallelism**:
```bash
--sequence-parallel
```

4. **Reduce global batch size**:
```bash
--global-batch-size 128  # Reduce from 256
```

#### Issue: OOM in Colocated Mode

**Symptoms**: OOM when both training and inference run on same GPUs

**Solutions**:

1. **Reduce SGLang memory**:
```bash
--sglang-mem-fraction-static 0.4  # Reduce from 0.8
```

2. **Enable offloading**:
```bash
--offload-optimizer-states
```

3. **Use smaller sequence length**:
```bash
--seq-length 2048  # Reduce from 4096
```

### Data Loading Issues

#### Issue: Slow Data Loading

**Symptoms**: GPU idle during data fetch, low GPU utilization

**Solutions**:

1. **Increase data workers**:
```bash
--num-data-workers 4
```

2. **Use streaming dataset**:
```bash
--streaming-data
```

3. **Pre-tokenize data**:
```python
# Pre-process data offline
from transformers import AutoTokenizer
tokenizer = AutoTokenizer.from_pretrained("model_path")
# Save tokenized data
```

#### Issue: Data Format Errors

**Symptoms**: KeyError, missing fields, parsing failures

**Solutions**:

1. **Verify data format**:
```python
import json
with open("data.jsonl") as f:
    for line in f:
        data = json.loads(line)
        assert "prompt" in data, "Missing prompt field"
        assert "label" in data, "Missing label field"
```

2. **Check key names**:
```bash
--input-key prompt  # Must match your data
--label-key label   # Must match your data
```

### Training Stability Issues

#### Issue: Loss Explosion / NaN

**Symptoms**: Loss becomes NaN or explodes

**Solutions**:

1. **Reduce learning rate**:
```bash
--lr 1e-6  # Reduce from 5e-6
```

2. **Enable gradient clipping**:
```bash
--clip-grad 1.0
```

3. **Check for data issues**:
```python
# Verify no empty prompts or responses
for sample in dataset:
    assert len(sample["prompt"]) > 0
```

4. **Use BF16 instead of FP16**:
```bash
--bf16  # More numerically stable
```

#### Issue: Reward Collapse

**Symptoms**: Reward drops to zero, model outputs garbage

**Solutions**:

1. **Increase KL penalty**:
```bash
--kl-loss-coef 0.01  # Increase from 0.001
```

2. **Reduce number of samples**:
```bash
--n-samples-per-prompt 4  # Reduce from 8
```

3. **Verify reward function**:
```python
# Test reward function independently
from custom_rm import reward_func
sample = Sample(prompt="test", response="test response")
reward = reward_func(args, sample)
print(f"Reward: {reward}")  # Should be reasonable
```

### Async Training Issues

#### Issue: Async Training Not Supported with Colocate

**Symptoms**: Error when using `--colocate` with `train_async.py`

**Solution**: Colocated mode is NOT supported for async training. Use separate GPUs:
```bash
# Remove --colocate flag
python train_async.py \
    --actor-num-gpus-per-node 4 \
    --rollout-num-gpus 4 \
    # No --colocate
```

#### Issue: Stale Weights in Async Mode

**Symptoms**: Policy divergence, inconsistent behavior

**Solutions**:

1. **Reduce async buffer size**:
```bash
--async-buffer-size 2  # Reduce from 4
```

2. **Increase weight update frequency**:
```bash
--update-weights-interval 1  # Sync every rollout
```

### Multi-Turn Training Issues

#### Issue: Tool Responses Included in Loss

**Symptoms**: Model learns to output tool responses verbatim

**Solution**: Properly set loss mask in custom generate function:
```python
def build_loss_mask(sample):
    """Create loss mask that excludes tool responses."""
    mask = []
    for i, token in enumerate(sample.tokens):
        if is_tool_response(token, sample.metadata):
            mask.append(0)  # Don't compute loss
        else:
            mask.append(1)  # Compute loss
    return mask
```

#### Issue: Multi-Turn Context Too Long

**Symptoms**: OOM or truncation in multi-turn conversations

**Solutions**:

1. **Limit conversation history**:
```python
# In custom generate function
conversation = sample.prompt[-10:]  # Keep last 10 turns
```

2. **Increase context length**:
```bash
--sglang-context-length 16384
```

### Checkpoint Issues

#### Issue: Checkpoint Loading Fails

**Symptoms**: Cannot load saved checkpoint

**Solutions**:

1. **Verify checkpoint path**:
```bash
ls -la /path/to/checkpoint/
```

2. **Check parallelism matches**:
```bash
# Checkpoint was saved with TP=2, must load with TP=2
--tensor-model-parallel-size 2
```

3. **Convert HuggingFace to Megatron** (if needed):
```bash
python tools/convert_hf_to_megatron.py \
    --hf_model_path /path/to/hf/model \
    --save_path /path/to/megatron/checkpoint
```

### Debugging Tips

#### Enable Verbose Logging

```bash
--log-level DEBUG
export SLIME_DEBUG=1
```

#### Check GPU Utilization

```bash
watch -n 1 nvidia-smi
```

#### Monitor Training

```bash
tensorboard --logdir outputs/
```

#### Test Custom Functions Independently

```python
# Test reward function
import asyncio
from custom_rm import reward_func

async def test():
    sample = Sample(prompt="test", response="test", label="expected")
    reward = await reward_func(args, sample)
    print(f"Reward: {reward}")

asyncio.run(test())
```

## Constraint Reference

Key constraint to remember:

```
rollout_batch_size × n_samples_per_prompt = global_batch_size × num_steps_per_rollout
```

Example: `32 × 8 = 256 × 1`

## Resources

- GitHub Issues: https://github.com/THUDM/slime/issues
- Documentation: https://thudm.github.io/slime/
- Examples: `examples/` directory




---

## 🚀 Usage

**Reference this template:** `@skill-slime-rl-training.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
