---
id: skill-miles-rl-training
type: skill
name: miles-rl-training
description: Provides guidance for enterprise-grade RL training using miles, a production-ready
  fork of slime. Use when training large MoE models with FP8/INT4, needing train-inference
  alignment, or requiring speculative RL for maximum throughput.
category: ai-research
complexity: medium
keywords:
- api
- deployment
- docker
- git
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 1374
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,374 -->
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




# miles-rl-training

> Provides guidance for enterprise-grade RL training using miles, a production-ready fork of slime. Use when training large MoE models with FP8/INT4, needing train-inference alignment, or requiring speculative RL for maximum throughput.

# miles: Enterprise-Grade RL for Large-Scale Model Training

miles is a high-performance, enterprise-ready RL framework optimized for large-scale model post-training. Built as a production fork of slime, it addresses critical challenges in MoE training stability, low-precision training, and train-inference alignment.

## When to Use miles

**Choose miles when you need:**
- Training 1TB+ MoE models (DeepSeek V3, Qwen3-MoE)
- FP8 or INT4 quantization-aware training
- Bit-wise identical train-inference alignment
- Speculative RL for maximum throughput
- Production stability with enterprise support

**Consider alternatives when:**
- You want the research-grade original → use **slime**
- You need flexible backend swapping → use **verl**
- You want PyTorch-native abstractions → use **torchforge**

## Key Features

### Low-Precision Training
- **Unified FP8**: End-to-end FP8 for both inference and training
- **INT4 QAT**: 1TB models on single-machine VRAM (H200)
- **Rollout Routing Replay (R3)**: Bit-wise expert alignment for MoE

### Performance Optimizations
- **Speculative RL**: 25%+ rollout speedup with online SFT draft models
- **Zero-Copy Weight Sync**: CUDA IPC zero-copy mapping
- **Partial Rollout**: Recycle half-finished trajectories

### Train-Inference Alignment
- **TIS/MIS**: Truncated/Masked Importance Sampling for off-policy correction
- **Kernel-level optimization**: FlashAttention-3, DeepGEMM integration

## Installation

```bash
# Recommended: Docker
docker pull radixark/miles:latest
docker run --rm --gpus all --ipc=host --shm-size=16g \
  -it radixark/miles:latest /bin/bash

# From source
git clone https://github.com/radixark/miles.git
cd miles
pip install -r requirements.txt
pip install -e .
```

## Quick Start

miles inherits slime's configuration system. Basic training:

```bash
python train.py \
    --advantage-estimator grpo \
    --model-name qwen3-30b-a3b \
    --hf-checkpoint /path/to/qwen3-30b-a3b-hf \
    --rollout-batch-size 512 \
    --n-samples-per-prompt 8
```

---

## Workflow 1: Large MoE Training

Use this workflow for training large MoE models like DeepSeek V3 or Qwen3-MoE.

### Prerequisites Checklist
- [ ] H100/H200 GPUs with FP8 support
- [ ] MoE model (DeepSeek V3, Qwen3-MoE)
- [ ] Docker environment with miles

### Step 1: Environment Setup

```bash
# FP8 block scaling (recommended for stability)
export NVTE_FP8_BLOCK_SCALING_FP32_SCALES=1
export CUDA_DEVICE_MAX_CONNECTIONS=1
```

### Step 2: Configure Training

```bash
python train.py \
    --actor-num-gpus-per-node 8 \
    --rollout-num-gpus 8 \
    --hf-checkpoint /path/to/deepseek-v3 \
    --advantage-estimator grpo \
    --tensor-model-parallel-size 8 \
    --expert-model-parallel-size 4 \
    --prompt-data /path/to/data.jsonl \
    --num-rollout 3000
```

### Verification Checklist
- [ ] Model loads without errors
- [ ] Routing decisions are consistent
- [ ] No NaN/Inf in loss values

---

## Workflow 2: Speculative RL Training

Use this workflow for maximum rollout throughput with EAGLE speculative decoding.

### How Speculative RL Works

1. Small draft model generates candidate tokens
2. Target model verifies in parallel
3. Draft model updated via online SFT to track policy

### Step 1: Enable Speculative Decoding

miles supports EAGLE speculative decoding via SGLang:

```bash
python train.py \
    --actor-num-gpus-per-node 8 \
    --hf-checkpoint /path/to/target-model \
    --sglang-speculative-algorithm EAGLE \
    --sglang-speculative-num-steps 3 \
    --sglang-speculative-eagle-topk 1 \
    --sglang-speculative-num-draft-tokens 4 \
    --sglang-speculative-draft-model-path /path/to/draft-model \
    --advantage-estimator grpo \
    --prompt-data /path/to/data.jsonl
```

### Step 2: Enable Online MTP Training (Optional)

For online SFT of draft model during training:

```bash
--mtp-num-layers 1 \
--enable-mtp-training \
--mtp-loss-scaling-factor 0.2
```

**Note**: Online MTP training requires a torch dist checkpoint with MTP weights. Add `--mtp-num-layers 1` during checkpoint conversion from HuggingFace.

### Expected Speedup

- **Standard rollout**: Baseline
- **Speculative RL**: 25-40% faster rollout
- **With partial rollout**: Additional 10-15% throughput

---

## Configuration Reference

miles inherits all slime arguments. See [slime API Reference](../slime/references/api-reference.md) for the complete list.

### Cluster Resources (from slime)

```bash
--actor-num-nodes 1
--actor-num-gpus-per-node 8
--rollout-num-gpus 8
--rollout-num-gpus-per-engine 2
--colocate
```

### Megatron Parallelism (from slime)

```bash
--tensor-model-parallel-size 8
--pipeline-model-parallel-size 2
--expert-model-parallel-size 4    # MoE expert parallelism
```

### Speculative Decoding (miles-specific)

```bash
--sglang-speculative-algorithm EAGLE
--sglang-speculative-num-steps 3
--sglang-speculative-eagle-topk 1
--sglang-speculative-num-draft-tokens 4
--sglang-enable-draft-weights-cpu-backup
--sglang-speculative-draft-model-path /your/draft/model/path
```

### Online MTP Training (miles-specific)

```bash
--mtp-num-layers 1
--enable-mtp-training
--mtp-loss-scaling-factor 0.2
```

---

## Key Features (Conceptual)

The following features are documented in miles but specific CLI flags may vary. Consult the miles repository for latest configuration.

### Unified FP8 Pipeline

End-to-end FP8 sampling and training that eliminates quantization-induced discrepancy causing RL collapse in MoE models.

### Rollout Routing Replay (R3)

Records expert routing decisions during SGLang inference and replays them during Megatron training for bit-wise expert alignment.

**How R3 Works**:
1. During SGLang inference, expert routing decisions are recorded
2. Routing decisions stored in `sample.rollout_routed_experts`
3. During Megatron training, routing is replayed instead of recomputed
4. Ensures identical expert selection between train and inference

### INT4 Quantization-Aware Training

Enables single-machine deployment of 1TB+ models (e.g., on H200).

**Memory Savings with INT4**:

| Model Size | BF16 VRAM | INT4 VRAM | Reduction |
|------------|-----------|-----------|-----------|
| 70B | 140GB | 45GB | 3.1x |
| 235B | 470GB | 150GB | 3.1x |
| 671B | 1.3TB | 420GB | 3.1x |

### Train-Inference Alignment

miles achieves "exactly 0 KL divergence" between training and inference through:
- Flash Attention 3
- DeepGEMM
- Batch-invariant kernels from Thinking Machines Lab
- `torch.compile` integration

---

## Sample Data Structure

miles uses the same `Sample` dataclass as slime with the `rollout_routed_experts` field for MoE routing replay:

```python
@dataclass
class Sample:
    prompt: str | list[dict]
    tokens: list[int]
    response: str
    reward: float | dict
    loss_mask: list[int]
    status: Status
    metadata: dict
    rollout_log_probs: list[float]
    rollout_routed_experts: list[list[int]]  # MoE routing for R3
```

See [slime API Reference](../slime/references/api-reference.md) for the complete Sample definition.

---

## Common Issues and Solutions

### Issue: FP8 Training Collapse

**Symptoms**: Loss explodes, NaN values

**Solutions**:
- Use block scaling: `export NVTE_FP8_BLOCK_SCALING_FP32_SCALES=1`
- Reduce learning rate: `--lr 5e-7`
- Ensure MoE routing is consistent between train/inference

### Issue: Speculative Draft Drift

**Symptoms**: Low acceptance rate over time

**Solutions**:
- Enable online MTP training to keep draft model aligned
- Reduce speculative steps: `--sglang-speculative-num-steps 2`
- Use CPU backup: `--sglang-enable-draft-weights-cpu-backup`

### Issue: Train-Inference Mismatch

**Symptoms**: Policy divergence, reward collapse

**Solutions**:
- Use TIS for off-policy correction: `--use-tis --tis-threshold 0.9`
- Verify log probs match between SGLang and Megatron
- Enable R3 for MoE models

---

## Supported Models

| Family | Models | MoE Support |
|--------|--------|-------------|
| DeepSeek | R1, V3, V3.2 | Full |
| Qwen | 2, 2.5, 3 (including MoE) | Full |
| Llama | 3, 3.1, 3.3, 4 | Dense only |
| Gemma | 2, 3, 3N | Dense only |
| GLM | 4.5, 4.6, 4.7 | Dense only |
| MiniMax | M2, M2.1 | Full |

---

## Resources

- **GitHub**: https://github.com/radixark/miles
- **Introduction Blog**: https://lmsys.org/blog/2025-11-19-miles/
- **Slime (upstream)**: https://github.com/THUDM/slime
- **SGLang**: https://github.com/sgl-project/sglang



---


## 📚 Reference Materials


### Api Reference

# miles API Reference

## Overview

miles is an enterprise-grade RL framework built on slime, adding advanced features for large-scale MoE training:

- Unified FP8 training and inference
- INT4 Quantization-Aware Training
- Rollout Routing Replay (R3)
- Speculative RL training

**Note**: miles inherits slime's configuration system. See [slime API Reference](../../slime/references/api-reference.md) for base arguments.

## Core Data Structures

miles uses the same `Sample` dataclass as slime with the `rollout_routed_experts` field for MoE routing replay.

## Quick Start

```bash
python train.py \
    --advantage-estimator grpo \
    --model-name qwen3-30b-a3b \
    --hf-checkpoint /path/to/qwen3-30b-a3b-hf \
    --rollout-batch-size 512 \
    --n-samples-per-prompt 8
```

## Configuration Options

miles inherits slime's three argument categories (Megatron, SGLang with `--sglang-` prefix, and slime-specific). Key additions:

### Cluster Resources (inherited from slime)

```bash
--actor-num-nodes 1
--actor-num-gpus-per-node 8
--rollout-num-gpus 8
--rollout-num-gpus-per-engine 2
--colocate
```

### Megatron Parallelism (inherited from slime)

```bash
--tensor-model-parallel-size 8
--pipeline-model-parallel-size 2
--expert-model-parallel-size 4    # MoE expert parallelism
```

### Speculative Decoding

Verified flags from miles documentation:

```bash
# Basic speculative decoding
--sglang-speculative-algorithm EAGLE
--sglang-speculative-num-steps 3
--sglang-speculative-eagle-topk 1
--sglang-speculative-num-draft-tokens 4
--sglang-enable-draft-weights-cpu-backup

# Draft model path
--sglang-speculative-draft-model-path /your/draft/model/path

# Online SFT for draft model (MTP)
--mtp-num-layers 1
--enable-mtp-training
--mtp-loss-scaling-factor 0.2
```

**Note**: Online MTP training requires a torch dist checkpoint with MTP weights. Add `--mtp-num-layers 1` during checkpoint conversion from HuggingFace to torch dist format.

## Key Features (Conceptual)

The following features are documented in miles but specific CLI flags are not publicly documented. Consult the miles repository for latest configuration options.

### Unified FP8 Pipeline

End-to-end FP8 sampling and training that eliminates quantization-induced discrepancy causing RL collapse in MoE models.

### Rollout Routing Replay (R3)

Records expert routing decisions during SGLang inference and replays them during Megatron training for bit-wise expert alignment.

**How R3 Works**:
1. During SGLang inference, expert routing decisions are recorded
2. Routing decisions stored in `sample.rollout_routed_experts`
3. During Megatron training, routing is replayed instead of recomputed
4. Ensures identical expert selection between train and inference

### INT4 Quantization-Aware Training

Enables single-machine deployment of 1TB+ models (e.g., on H200).

**Memory Savings with INT4**:

| Model Size | BF16 VRAM | INT4 VRAM | Reduction |
|------------|-----------|-----------|-----------|
| 70B | 140GB | 45GB | 3.1x |
| 235B | 470GB | 150GB | 3.1x |
| 671B | 1.3TB | 420GB | 3.1x |

### Train-Inference Alignment

miles achieves "exactly 0 KL divergence" between training and inference through infrastructure optimizations:
- Flash Attention 3
- DeepGEMM
- Batch-invariant kernels from Thinking Machines Lab
- `torch.compile` integration

### Truncated/Masked Importance Sampling (TIS/MIS)

Algorithmic corrections for off-policy training. See slime documentation for `--use-tis` flag.

## Custom Functions

Same interface as slime:

```bash
--custom-generate-function-path generate.py
--custom-rm-path reward.py
```

## Supported Models

| Family | Models | MoE Support |
|--------|--------|-------------|
| DeepSeek | R1, V3, V3.2 | Full |
| Qwen | 2, 2.5, 3 (including MoE) | Full |
| Llama | 3, 3.1, 3.3, 4 | Dense only |
| Gemma | 2, 3, 3N | Dense only |
| GLM | 4.5, 4.6, 4.7 | Dense only |
| MiniMax | M2, M2.1 | Full |

## Resources

- GitHub: https://github.com/radixark/miles
- Introduction Blog: https://lmsys.org/blog/2025-11-19-miles/
- Slime (upstream): https://github.com/THUDM/slime
- SGLang: https://github.com/sgl-project/sglang




### Troubleshooting

# miles Troubleshooting Guide

## FP8 Training Issues

### Issue: FP8 Training Collapse

**Symptoms**: Loss explodes, NaN values, reward collapses

**Solutions**:

1. **Use block scaling**:
```bash
--fp8-recipe blockwise
export NVTE_FP8_BLOCK_SCALING_FP32_SCALES=1
```

2. **Enable R3 for MoE models**:
```bash
--use-r3
```

3. **Reduce learning rate**:
```bash
--lr 5e-7  # Reduce from 1e-6
```

4. **Warm up from BF16**:
```bash
--warmup-steps 100
--warmup-precision bf16
```

### Issue: FP8 vs BF16 Accuracy Gap

**Symptoms**: FP8 model underperforms BF16 baseline

**Solutions**:

1. **Use E4M3 format for activations**:
```bash
--fp8-format e4m3
```

2. **Enable dynamic scaling**:
```bash
--fp8-dynamic-scaling
```

3. **Skip sensitive layers**:
```bash
--fp8-skip-layers "lm_head,embed"
```

## Train-Inference Mismatch Issues

### Issue: Policy Divergence

**Symptoms**: Model behavior differs between training and inference

**Solutions**:

1. **Enable Rollout Routing Replay**:
```bash
--use-r3
```

2. **Use importance sampling correction**:
```bash
--use-tis --tis-threshold 0.9
```

3. **Verify log probs match**:
```bash
--verify-logprobs
```

### Issue: Expert Routing Mismatch (MoE)

**Symptoms**: Different experts activated during train vs inference

**Solutions**:

1. **Enable R3**:
```bash
--use-r3
--r3-buffer-size 1000
```

2. **Use deterministic routing**:
```bash
--deterministic-expert-routing
```

## INT4 Training Issues

### Issue: INT4 Accuracy Degradation

**Symptoms**: Worse performance than BF16 or FP8

**Solutions**:

1. **Increase group size**:
```bash
--int4-group-size 256  # Increase from 128
```

2. **Use mixed precision for sensitive layers**:
```bash
--int4-skip-layers "lm_head,embed,layer_norm"
```

3. **Warm start from BF16**:
```bash
--warmup-steps 100
--warmup-precision bf16
```

4. **Increase learning rate** (INT4 often needs higher LR):
```bash
--lr 2e-6  # Increase from 1e-6
```

### Issue: INT4 OOM Despite Expected Savings

**Symptoms**: Still running out of memory with INT4

**Solutions**:

1. **Verify environment variable**:
```bash
export OPEN_TRAINING_INT4_FAKE_QAT_FLAG=1
```

2. **Check group size alignment**:
```bash
# Group size must divide hidden dimension evenly
--int4-group-size 128  # Must divide hidden_size
```

## Speculative RL Issues

### Issue: Low Acceptance Rate

**Symptoms**: Draft model tokens frequently rejected

**Solutions**:

1. **Reduce lookahead**:
```bash
--spec-lookahead 3  # Reduce from 5
```

2. **Update draft more frequently**:
```bash
--online-sft-interval 5  # Reduce from 10
```

3. **Increase draft learning rate**:
```bash
--draft-lr 1e-5  # Increase
```

### Issue: Draft Model Drift

**Symptoms**: Acceptance rate drops over time

**Solutions**:

1. **Enable online SFT**:
```bash
--online-sft-interval 5
```

2. **Use EMA for draft updates**:
```bash
--draft-ema-decay 0.99
```

3. **Reinitialize draft periodically**:
```bash
--reinit-draft-interval 1000
```

### Issue: Speculative Training Slower Than Expected

**Symptoms**: Not achieving expected 25%+ speedup

**Solutions**:

1. **Verify draft model is small enough**:
```bash
# Draft should be 1/4 to 1/10 size of target
```

2. **Check lookahead is optimal**:
```bash
--spec-lookahead 5  # Sweet spot for most models
```

3. **Profile to find bottleneck**:
```bash
--profile-speculative
```

## Weight Synchronization Issues

### Issue: Zero-Copy Sync Failures

**Symptoms**: Errors with CUDA IPC, weight corruption

**Solutions**:

1. **Verify CUDA IPC support**:
```bash
nvidia-smi topo -m  # Check GPU topology
```

2. **Fall back to standard sync**:
```bash
# Remove --use-zero-copy-sync
```

3. **Increase bucket size**:
```bash
--sync-bucket-size 2147483648  # 2GB
```

### Issue: Slow Weight Sync Despite Zero-Copy

**Symptoms**: Weight sync still slow

**Solutions**:

1. **Use colocated mode**:
```bash
--colocate
```

2. **Enable async weight transfer**:
```bash
--async-weight-sync
```

## MoE-Specific Issues

### Issue: Expert Load Imbalance

**Symptoms**: Some experts heavily loaded, others unused

**Solutions**:

1. **Enable load balancing loss**:
```bash
--aux-loss-coef 0.01
```

2. **Use capacity factor**:
```bash
--moe-capacity-factor 1.25
```

### Issue: Expert Parallelism OOM

**Symptoms**: OOM with large MoE models

**Solutions**:

1. **Increase expert parallelism**:
```bash
--expert-model-parallel-size 8  # Increase from 4
```

2. **Reduce batch size per GPU**:
```bash
--micro-batch-size 1
```

3. **Enable expert offloading**:
```bash
--offload-experts
```

## Multi-Agent Issues

### Issue: Co-Evolution Instability

**Symptoms**: Agents oscillate or one dominates

**Solutions**:

1. **Use alternating updates**:
```yaml
co_evolution:
  strategy: alternating
```

2. **Reduce co-evolution frequency**:
```bash
--co-evolution-interval 20  # Increase from 10
```

3. **Add population diversity**:
```yaml
co_evolution:
  population_size: 4
```

## Debugging Tips

### Enable Verbose Logging

```bash
--log-level DEBUG
export MILES_DEBUG=1
```

### Check FP8 Tensors

```python
# Verify FP8 is active
for name, param in model.named_parameters():
    print(f"{name}: {param.dtype}")
```

### Profile Training

```bash
--profile
--profile-dir /path/to/profile
```

### Verify R3 Is Working

```python
# Check routing is being recorded
sample = samples[0]
assert sample.rollout_routed_experts is not None
assert len(sample.rollout_routed_experts) > 0
```

### Monitor GPU Memory

```bash
watch -n 1 nvidia-smi
```

## Resources

- GitHub Issues: https://github.com/radixark/miles/issues
- Unified FP8 Blog: https://lmsys.org/blog/2025-11-25-fp8-rl/
- Train-Inference Mismatch Tutorial: https://github.com/zhaochenyang20/Awesome-ML-SYS-Tutorial/blob/main/rlhf/slime/mismatch/blog-en.md
- SGLang Discord: Community support




---

## 🚀 Usage

**Reference this template:** `@skill-miles-rl-training.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
