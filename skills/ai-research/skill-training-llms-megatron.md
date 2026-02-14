---
id: skill-training-llms-megatron
type: skill
name: training-llms-megatron
description: Trains large language models (2B-462B parameters) using NVIDIA Megatron-Core
  with advanced parallelism strategies. Use when training models >1B parameters, need
  maximum GPU efficiency (47% MFU on H100), or require tensor/pipeline/sequence/context/expert
  parallelism. Production-ready framework used for Nemotron, LLaMA, DeepSeek.
category: ai-research
complexity: medium
keywords:
- api
- docker
- github
- optimization
- performance
- testing
capabilities: []
token_estimate: 1549
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,549 -->
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




# training-llms-megatron

> Trains large language models (2B-462B parameters) using NVIDIA Megatron-Core with advanced parallelism strategies. Use when training models >1B parameters, need maximum GPU efficiency (47% MFU on H100), or require tensor/pipeline/sequence/context/expert parallelism. Production-ready framework used for Nemotron, LLaMA, DeepSeek.

# Megatron-Core - Large-Scale LLM Training

## Quick start

Megatron-Core trains LLMs from 2B to 462B parameters with up to 47% Model FLOP Utilization on H100 GPUs through advanced parallelism strategies.

**Installation**:
```bash
# Docker (recommended)
docker run --gpus all -it --rm nvcr.io/nvidia/pytorch:25.04-py3

# Or pip
pip install megatron-core
```

**Simple distributed training**:
```bash
# Train with 2 GPUs using data parallelism
torchrun --nproc_per_node=2 examples/run_simple_mcore_train_loop.py

# Or LLaMA-3 8B training
./examples/llama/train_llama3_8b_fp8.sh
```

## Common workflows

### Workflow 1: Train LLaMA-style model with 3D parallelism

Copy this checklist:

```
LLaMA Training Setup:
- [ ] Step 1: Choose parallelism configuration
- [ ] Step 2: Configure training hyperparameters
- [ ] Step 3: Launch distributed training
- [ ] Step 4: Monitor performance metrics
```

**Step 1: Choose parallelism configuration**

Model size determines parallelism strategy:

| Model Size | GPUs | Tensor Parallel | Pipeline Parallel | Data Parallel | Context Parallel |
|------------|------|-----------------|-------------------|---------------|------------------|
| 7B | 8 | 1 | 1 | 8 | 1 |
| 13B | 8 | 2 | 1 | 4 | 1 |
| 70B | 64 | 4 | 4 | 4 | 1 |
| 405B | 128 | 8 | 8 | 2 | 2 |

**Step 2: Configure training hyperparameters**

```bash
#!/bin/bash
# train_llama_70b.sh

GPUS_PER_NODE=8
NNODES=8  # 64 GPUs total
TP=4      # Tensor parallel
PP=4      # Pipeline parallel
CP=1      # Context parallel

# LLaMA 70B configuration
MODEL_SIZE=70  # Billion parameters
HIDDEN_SIZE=8192
NUM_LAYERS=80
NUM_HEADS=64
SEQ_LENGTH=4096

# Training hyperparameters
MICRO_BATCH=1
GLOBAL_BATCH=1024
LR=3e-4

torchrun \
  --nproc_per_node=$GPUS_PER_NODE \
  --nnodes=$NNODES \
  pretrain_gpt.py \
  --tensor-model-parallel-size $TP \
  --pipeline-model-parallel-size $PP \
  --context-parallel-size $CP \
  --sequence-parallel \
  --num-layers $NUM_LAYERS \
  --hidden-size $HIDDEN_SIZE \
  --num-attention-heads $NUM_HEADS \
  --seq-length $SEQ_LENGTH \
  --max-position-embeddings $SEQ_LENGTH \
  --micro-batch-size $MICRO_BATCH \
  --global-batch-size $GLOBAL_BATCH \
  --lr $LR \
  --train-iters 100000 \
  --lr-decay-style cosine \
  --lr-warmup-iters 2000 \
  --weight-decay 0.1 \
  --clip-grad 1.0 \
  --bf16 \
  --use-mcore-models \
  --transformer-impl transformer_engine \
  --data-path /path/to/data \
  --vocab-file /path/to/vocab.json \
  --merge-file /path/to/merges.txt
```

**Step 3: Launch distributed training**

```bash
# Single node (8 GPUs)
bash train_llama_70b.sh

# Multi-node with SLURM
sbatch --nodes=8 --gpus-per-node=8 train_llama_70b.sh
```

**Step 4: Monitor performance metrics**

Key metrics to track:
```
Model FLOP Utilization (MFU): Target >40% on H100
Throughput: Tokens/sec/GPU
Memory usage: <80GB per GPU for 70B model
Loss: Should decrease steadily
```

### Workflow 2: Configure Mixture of Experts (MoE) training

For sparse MoE models like Mixtral.

```
MoE Training:
- [ ] Step 1: Configure expert parallelism
- [ ] Step 2: Set MoE hyperparameters
- [ ] Step 3: Launch training with EP
```

**Step 1: Configure expert parallelism**

```bash
# Mixtral 8x7B example
TENSOR_PARALLEL=2
PIPELINE_PARALLEL=1
EXPERT_PARALLEL=4  # Split 8 experts across 4 GPUs
DATA_PARALLEL=4

TOTAL_GPUS=$((TENSOR_PARALLEL * PIPELINE_PARALLEL * EXPERT_PARALLEL * DATA_PARALLEL))
# = 2 * 1 * 4 * 4 = 32 GPUs
```

**Step 2: Set MoE hyperparameters**

```bash
torchrun \
  --nproc_per_node=8 \
  pretrain_gpt.py \
  --tensor-model-parallel-size 2 \
  --pipeline-model-parallel-size 1 \
  --expert-model-parallel-size 4 \
  --num-experts 8 \
  --moe-router-topk 2 \
  --moe-router-load-balancing-type aux_loss \
  --moe-aux-loss-coeff 0.01 \
  --hidden-size 4096 \
  --num-layers 32 \
  --num-attention-heads 32 \
  --seq-length 4096 \
  --max-position-embeddings 4096 \
  --bf16 \
  --use-mcore-models \
  --transformer-impl transformer_engine \
  --data-path /path/to/data \
  --vocab-file /path/to/vocab.json \
  --merge-file /path/to/merges.txt
```

**Step 3: Launch training with EP**

Expert parallelism distributes different experts across GPUs, reducing memory while maintaining capacity.

```
Memory without EP: 8 experts × 7B = 56GB per GPU
Memory with EP=4: 2 experts × 7B = 14GB per GPU
Savings: 75% memory reduction
```

### Workflow 3: Optimize for maximum throughput

Achieve 47% MFU on H100.

```
Performance Optimization:
- [ ] Step 1: Enable Flash Attention
- [ ] Step 2: Use FP8 precision (H100)
- [ ] Step 3: Optimize micro-batch size
- [ ] Step 4: Tune parallelism degrees
```

**Step 1: Enable optimizations**

```bash
--use-mcore-models  # Use Megatron Core models
--transformer-impl transformer_engine  # Use Transformer Engine
--sequence-parallel  # Reduce activation memory (use with TP)
```

**Step 2: Use FP8 precision (H100 only)**

```bash
--fp8-hybrid  # FP8 mixed precision training
# Transformer Engine handles FP8 automatically
```

Result: 1.5-2x speedup on H100 vs BF16.

**Step 3: Optimize micro-batch size**

Find largest micro-batch that fits in memory:

```bash
# Start with 1, increase until OOM
for MBS in 1 2 4 8; do
  echo "Testing micro-batch-size=$MBS"
  torchrun ... --micro-batch-size $MBS
done
```

Typical values:
- 7B model: 4-8
- 70B model: 1-2
- 405B model: 1

**Step 4: Tune parallelism degrees**

Rules of thumb:
```
Tensor Parallel: Use ≤8 (limited by NVLink within node)
Pipeline Parallel: Use for >70B models
Context Parallel: Use for sequences >8K tokens
Data Parallel: Fill remaining GPUs
```

Example 405B on 128 H100s:
```
TP=8 (1 node)
PP=8 (across nodes)
CP=2 (long sequences)
DP=1
Total = 8 × 8 × 2 × 1 = 128 GPUs
```

## When to use vs alternatives

**Use Megatron-Core when:**
- Training models >10B parameters
- Need maximum efficiency (target >40% MFU)
- Using NVIDIA GPUs (A100, H100)
- Production training at scale
- Want fine-grained parallelism control

**Use alternatives instead:**
- **PyTorch FSDP**: Models <70B, simpler API, PyTorch native
- **DeepSpeed**: Easier setup, good for <100B models
- **HuggingFace Accelerate**: Prototyping, simpler workflows
- **LitGPT**: Educational, single-file implementations

## Common issues

**Issue: Low GPU utilization (<30% MFU)**

Causes:
1. Micro-batch too small
2. Too much parallelism overhead
3. Not using Flash Attention

Fixes:
```bash
# Increase micro-batch
--micro-batch-size 4  # Was 1

# Enable optimizations
--use-flash-attn
--sequence-parallel

# Reduce TP if >8
--tensor-model-parallel-size 4  # Was 16
```

**Issue: Out of memory**

Reduce memory with:
```bash
--tensor-model-parallel-size 2  # Split model across GPUs
--recompute-granularity full  # Gradient checkpointing
--recompute-method block  # Checkpoint transformer blocks
--recompute-num-layers 1  # Checkpoint every layer
```

Or use CPU/NVMe offloading:
```bash
--cpu-optimizer  # Offload optimizer to CPU
--cpu-optimizer-type ADAM  # CPU Adam variant
```

**Issue: Training slower than expected**

Check:
1. **Network bottleneck**: Ensure InfiniBand/NVLink enabled
2. **Pipeline bubbles**: Use interleaved pipeline schedule
   ```bash
   --num-layers-per-virtual-pipeline-stage 2
   ```
3. **Data loading**: Use fast data loader
   ```bash
   --dataloader-type cyclic
   ```

**Issue: Diverging loss**

Stabilize training:
```bash
--lr-warmup-iters 2000  # Longer warmup
--clip-grad 1.0  # Gradient clipping
--init-method-std 0.006  # Smaller init
--attention-dropout 0.0  # No dropout in attention
--hidden-dropout 0.0  # No dropout in FFN
```

## Advanced topics

**Parallelism strategies**: See [references/parallelism-guide.md](references/parallelism-guide.md) for detailed comparison of TP/PP/DP/CP/EP with performance analysis and when to use each.

**Performance benchmarks**: See [references/benchmarks.md](references/benchmarks.md) for MFU numbers across different model sizes and GPU configurations.

**Production configurations**: See [references/production-examples.md](references/production-examples.md) for real-world setups from LLaMA 3 405B, Nemotron-4 340B, and DeepSeek-V3 671B.

**Training recipes**: See [references/training-recipes.md](references/training-recipes.md) for complete hyperparameter configurations for GPT/LLaMA/Mixtral architectures.

## Hardware requirements

- **GPU**: NVIDIA Ampere+ (A100, H100, B200)
  - Turing works but slower
  - FP8 requires Hopper/Ada/Blackwell
- **Network**: InfiniBand or 400Gb+ Ethernet for multi-node
- **Memory per GPU**:
  - 7B model: 40GB+
  - 70B model: 80GB (with TP=4)
  - 405B model: 80GB (with TP=8, PP=8)
- **Storage**: Fast NVMe for checkpoints (1TB+ for 70B+ models)

## Resources

- Docs: https://docs.nvidia.com/megatron-core/
- GitHub: https://github.com/NVIDIA/Megatron-LM
- Papers:
  - "Megatron-LM: Training Multi-Billion Parameter Language Models Using Model Parallelism" (2019)
  - "Efficient Large-Scale Language Model Training on GPU Clusters Using Megatron-LM" (2021)
- NeMo Framework: https://docs.nvidia.com/nemo-framework/ (built on Megatron-Core)




---


## 📚 Reference Materials


### Parallelism Guide

# Parallelism Strategies Guide

Complete guide to parallelism in Megatron-Core: when to use each strategy, performance characteristics, and configuration examples.

## Parallelism Types

### 1. Data Parallel (DP)

**How it works**: Replicate full model on each GPU, split data batches, synchronize gradients.

**Memory**: Low efficiency - full model replication
**Communication**: Low - only gradients (all-reduce)
**Scalability**: Good for small models

**When to use**:
- Models <1B parameters that fit on single GPU
- Plenty of GPUs available
- Limited model complexity

**Configuration**:
```bash
# 8 GPUs, no model parallelism
torchrun --nproc_per_node=8 train.py \
  --tensor-model-parallel-size 1 \
  --pipeline-model-parallel-size 1
# Implicit DP = 8
```

**Performance**:
- Near-linear scaling for small models
- 7B model on 8×A100: ~90% efficiency

### 2. Tensor Parallel (TP)

**How it works**: Split individual layers/tensors across GPUs (column/row partitioning of weight matrices).

**Memory**: Excellent - 1/N reduction per GPU
**Communication**: Very high - all-reduce after every layer
**Scalability**: Best ≤8 GPUs within single node (needs NVLink)

**When to use**:
- Models >10B parameters
- Have NVLink-connected GPUs
- Within single node (network latency kills performance across nodes)

**Configuration**:
```bash
# Split model across 4 GPUs with TP
torchrun --nproc_per_node=4 train.py \
  --tensor-model-parallel-size 4
```

**Performance**:
- **1 node (8 GPUs, NVLink)**: 85-95% efficiency
- **Across nodes**: <50% efficiency (avoid)

**Memory savings**:
```
LLaMA 70B without TP: 140GB (won't fit on 80GB GPU)
LLaMA 70B with TP=4: 35GB per GPU (fits easily)
```

**Communication volume** (70B model):
- Per layer: ~20GB all-reduce
- 80 layers × 20GB = 1.6TB total traffic
- With NVLink (600GB/s): Manageable
- With Ethernet (100Gb/s = 12.5GB/s): Too slow

### 3. Pipeline Parallel (PP)

**How it works**: Divide model layers into stages, assign stages to different GPUs, process microbatches in pipeline.

**Memory**: Very high - divide layers evenly
**Communication**: Low-medium - only activations between stages
**Scalability**: Good across nodes

**Pipeline Schedules**:

**GPipe** (simple but inefficient):
```
GPU0: F F F F ........ B B B B
GPU1: .... F F F F .... B B B B
GPU2: ........ F F F F B B B B
```
Bubble: 50% idle time

**1F1B** (one-forward-one-backward):
```
GPU0: F F F F B B B B B B B B
GPU1: .. F F F F B B B B B B B B
GPU2: .... F F F F B B B B B B B B
```
Bubble: ~25% idle time

**Interleaved 1F1B** (best):
```
GPU0: F1 F2 F3 F4 B1 B2 B3 B4 ...
GPU1: F1 F2 F3 F4 B1 B2 B3 B4 ...
```
Bubble: 5-10% idle time

**When to use**:
- Models >70B parameters
- Multi-node training
- Limited intra-node bandwidth

**Configuration**:
```bash
# 4-stage pipeline
torchrun --nproc_per_node=8 --nnodes=4 train.py \
  --pipeline-model-parallel-size 4 \
  --num-layers 80 \
  --num-layers-per-virtual-pipeline-stage 2  # Interleaved
```

**Performance**:
- Interleaved schedule: 90-95% efficiency
- Standard 1F1B: 75-85% efficiency

### 4. Sequence Parallel (SP)

**How it works**: Split sequence dimension across tensor-parallel GPUs, reduce activation memory.

**Memory**: Reduces activations by TP factor
**Communication**: Same as TP (already using all-reduce)
**Scalability**: Tied to TP

**When to use**:
- Long sequences (>4K tokens)
- Using TP already
- Activation memory is bottleneck

**Configuration**:
```bash
torchrun --nproc_per_node=8 train.py \
  --tensor-model-parallel-size 4 \
  --sequence-parallel  # Requires TP > 1
```

**Memory savings**:
```
70B model, 4K sequence, TP=4:
Without SP: 48GB activations per GPU
With SP: 12GB activations per GPU
Savings: 75%
```

### 5. Context Parallel (CP)

**How it works**: Split very long sequences across GPUs using Ring Attention.

**Memory**: Reduces KV cache and activations
**Communication**: Medium - ring communication pattern
**Scalability**: Good for >8K sequences

**When to use**:
- Sequences >8K tokens
- Long-context models (>32K)
- KV cache memory bottleneck

**Configuration**:
```bash
torchrun --nproc_per_node=8 train.py \
  --context-parallel-size 2 \
  --seq-length 32768  # 32K tokens
```

**Memory savings** (32K sequence):
```
Without CP: 64GB KV cache
With CP=4: 16GB KV cache per GPU
```

### 6. Expert Parallel (EP)

**How it works**: For MoE models, distribute different experts across GPUs.

**Memory**: Excellent - only store 1/N experts per GPU
**Communication**: Low - only route tokens to experts
**Scalability**: Matches number of experts

**When to use**:
- Mixture of Experts models
- Want model capacity without memory cost
- Have ≥8 GPUs

**Configuration**:
```bash
# Mixtral 8x7B: 8 experts
torchrun --nproc_per_node=8 train.py \
  --expert-model-parallel-size 4 \
  --num-experts 8 \
  --tensor-model-parallel-size 2
```

**Memory** (Mixtral 8×7B):
```
Without EP: 8 experts × 7B = 56GB
With EP=4: 2 experts × 7B = 14GB
Savings: 75%
```

## Combining Parallelism Strategies

### 3D Parallelism (TP + PP + DP)

Standard for large models.

**LLaMA 3 70B on 64 GPUs**:
```bash
TP=4  # Within each node
PP=4  # Across nodes
DP=4  # Remaining dimension
Total = 4 × 4 × 4 = 64 GPUs
```

**Memory per GPU**: 70B / 4 (TP) / 4 (PP) = 4.4B params ≈ 20GB

**Configuration**:
```bash
torchrun --nproc_per_node=8 --nnodes=8 train.py \
  --tensor-model-parallel-size 4 \
  --pipeline-model-parallel-size 4
  # DP is implicit: 64 / (4*4) = 4
```

### 4D Parallelism (TP + PP + DP + CP)

For very large models or long context.

**LLaMA 3 405B on 256 GPUs**:
```bash
TP=8   # Max NVLink
PP=8   # Across nodes
CP=2   # Long sequences
DP=2   # Remaining
Total = 8 × 8 × 2 × 2 = 256 GPUs
```

**Configuration**:
```bash
torchrun --nproc_per_node=8 --nnodes=32 train.py \
  --tensor-model-parallel-size 8 \
  --pipeline-model-parallel-size 8 \
  --context-parallel-size 2
```

### 4D + EP (5D Parallelism)

For sparse MoE models.

**DeepSeek-V3 671B (37B active) on 1024 GPUs**:
```bash
TP=2   # Limited by active params
PP=16  # Many stages
EP=64  # 256 experts / 4 experts per GPU
DP=2   # Small data parallel
Total = 2 × 16 × 64 × 2 = 4096 (uses 1024 in practice)
```

## Decision Guide

### By Model Size

| Model Size | GPUs | Recommended Strategy |
|------------|------|---------------------|
| <1B | 1-8 | DP only |
| 1-10B | 8-16 | TP=2-4 + DP |
| 10-70B | 16-64 | TP=4 + PP=2-4 + DP |
| 70-175B | 64-256 | TP=8 + PP=4-8 + DP |
| 175-500B | 256-1024 | TP=8 + PP=8-16 + CP=2 + DP |
| 500B+ | 1024+ | 4D or 5D (with EP) |

### By Hardware Topology

**Single node (8 GPUs with NVLink)**:
```bash
# Up to 70B
TP=8  # Use all NVLink bandwidth
```

**Multiple nodes (InfiniBand)**:
```bash
# Minimize cross-node communication
TP=8      # Within node only
PP=N      # Across nodes
DP=remaining
```

**Limited network (Ethernet)**:
```bash
# Avoid TP across nodes
TP=1-4    # Within node
PP=many   # PP has low communication
```

### By Sequence Length

| Sequence | Parallelism |
|----------|------------|
| <2K | Standard (TP + PP + DP) |
| 2K-8K | + SP (sequence parallel) |
| 8K-32K | + CP=2 (context parallel) |
| 32K+ | + CP=4-8 |

## Performance Characteristics

### Communication Volume (per iteration)

**Data Parallel**: O(model_size) - all-reduce gradients
**Tensor Parallel**: O(model_size × layers) - all-reduce per layer
**Pipeline Parallel**: O(batch × hidden × layers/stages) - activations only
**Context Parallel**: O(sequence × hidden) - ring communication

### Memory Breakdown (70B model example)

Without parallelism:
```
Model parameters: 140GB (FP16)
Gradients: 140GB
Optimizer states: 280GB (Adam)
Activations: 48GB (batch=1, seq=4K)
Total: 608GB (won't fit!)
```

With TP=4, PP=4, DP=4 (64 GPUs):
```
Parameters: 140GB / 4 / 4 = 8.75GB per GPU
Gradients: 8.75GB per GPU
Optimizer: 17.5GB per GPU
Activations: 48GB / 4 / 4 = 3GB per GPU
Total: ~38GB per GPU (fits on A100 80GB)
```

## Best Practices

1. **Start with TP within single node**
   ```bash
   --tensor-model-parallel-size 8  # Use all NVLink
   ```

2. **Add PP for cross-node scaling**
   ```bash
   --pipeline-model-parallel-size 4
   --num-layers-per-virtual-pipeline-stage 2  # Interleaved
   ```

3. **Enable SP when using TP**
   ```bash
   --sequence-parallel  # Free activation savings
   ```

4. **Use CP for long sequences**
   ```bash
   --context-parallel-size 2  # If seq_len > 8K
   ```

5. **Avoid TP across nodes** (network latency kills performance)

6. **Match TP to GPU topology** (TP=8 for 8-GPU nodes)

7. **Profile first iteration** to check memory and communication:
   ```bash
   --profile  # Enable profiling
   --profile-ranks 0  # Profile first rank only
   ```

## Troubleshooting

**High communication overhead (low MFU)**:
- Reduce TP degree (especially across nodes)
- Increase PP degree instead
- Enable interleaved pipeline schedule

**Out of memory**:
- Increase TP/PP (split model more)
- Enable gradient checkpointing:
  ```bash
  --recompute-granularity full
  --recompute-method block
  ```
- Reduce micro-batch size

**Pipeline bubbles (low GPU util)**:
- Use interleaved schedule:
  ```bash
  --num-layers-per-virtual-pipeline-stage 2
  ```
- Increase number of microbatches:
  ```bash
  --global-batch-size 1024
  --micro-batch-size 1  # More microbatches = smaller bubbles
  ```

**Load imbalance in MoE**:
- Tune load balancing:
  ```bash
  --moe-router-load-balancing-type aux_loss
  --moe-aux-loss-coeff 0.01
  ```
- Increase expert parallel degree:
  ```bash
  --expert-model-parallel-size 8  # More experts per GPU
  ```




### Production Examples

# Production Examples

Real-world production deployments using Megatron-Core and Megatron-LM.

## Meta LLaMA 3

### Overview
- **Models**: 8B, 70B, 405B parameters
- **Training Infrastructure**: Two 24,000 H100 GPU clusters
- **Total Investment**: Massive scale, months of training
- **Training Data**: 15 trillion tokens for 405B model
- **Status**: Production deployed (llama.meta.com)

### LLaMA 3.1 405B - Largest Open Model

**Architecture**:
```yaml
Parameters: 405 billion
Layers: 126
Hidden size: 16384
Attention heads: 128
Query groups: 8 (GQA)
FFN size: 53248
Vocabulary: 128,256 tokens
Max context: 128K tokens (supports up to)
Position encoding: RoPE
Activation: SwiGLU
Normalization: RMSNorm
```

**Training Configuration**:
```bash
# 1024 H100 GPUs (128 nodes × 8 GPUs)
Tensor Parallel (TP): 8     # Within node
Pipeline Parallel (PP): 8    # Across nodes
Context Parallel (CP): 2     # For long sequences
Data Parallel (DP): 8        # Remaining dimension

Total GPUs: 8 × 8 × 2 × 8 = 1024
Effective batch size: 2048
Micro-batch per GPU: 1
Sequence length: 4096 tokens
```

**Performance Metrics**:
- **Sustained throughput**: 400 TFlops/GPU
- **MFU**: ~46% on H100
- **Uptime**: 95%+ over months
- **Efficiency improvement**: 3× vs LLaMA 2 training

**Training Duration**:
- 15 trillion tokens total
- ~54 days on 16,384 H100 GPUs
- Or ~6 months on 1,024 H100 GPUs

**Key Optimizations Used**:
```bash
--use-mcore-models \
--transformer-impl transformer_engine \
--sequence-parallel \
--context-parallel-size 2 \
--use-distributed-optimizer \
--overlap-grad-reduce \
--overlap-param-gather \
--use-flash-attn-v2 \
--bf16
```

**Production Serving**:
- Deployed on llama.meta.com
- Available via API and download
- Used in Meta products (Instagram, Facebook, WhatsApp)

### LLaMA 3 70B

**Training Configuration**:
```bash
# 64 H100 GPUs (8 nodes × 8 GPUs)
TP=4, PP=4, CP=2, DP=2

torchrun --nproc_per_node=8 --nnodes=8 pretrain_gpt.py \
  --num-layers 80 \
  --hidden-size 8192 \
  --num-attention-heads 64 \
  --num-query-groups 8 \
  --seq-length 4096 \
  --micro-batch-size 1 \
  --global-batch-size 1024 \
  --tensor-model-parallel-size 4 \
  --pipeline-model-parallel-size 4 \
  --context-parallel-size 2 \
  --bf16 \
  --use-mcore-models
```

**Memory per GPU**:
- Model parameters: 140GB / 4 (TP) / 4 (PP) = 8.75GB
- Optimizer states: ~17.5GB
- Activations: ~3GB
- **Total**: ~30GB per H100 (fits in 80GB)

## NVIDIA Nemotron-4 340B

### Overview
- **Organization**: NVIDIA
- **Parameters**: 340 billion
- **Framework**: NeMo (built on Megatron-Core)
- **Purpose**: Enterprise AI foundation model
- **Status**: Commercial deployment

**Key Features**:
- Mixture of Experts architecture
- Optimized for enterprise use cases
- NeMo framework integration
- Production-ready deployment

**Architecture**:
```yaml
Type: Mixture of Experts (MoE)
Total parameters: 340B
Active parameters per token: ~40B
Experts: 8
Router: Top-2
Context length: 4096
```

**Training Infrastructure**:
- NVIDIA DGX H100 systems
- Megatron-Core + NeMo
- Multi-node training
- Enterprise-grade fault tolerance

**Production Features**:
- NeMo Guardrails integration
- Enterprise support
- Customization options
- On-premise deployment available

## Microsoft & NVIDIA Megatron-Turing NLG 530B

### Overview
- **Organization**: Microsoft + NVIDIA collaboration
- **Parameters**: 530 billion (largest dense model when released)
- **Year**: 2021
- **Framework**: DeepSpeed ZeRO-3 + Megatron tensor/pipeline parallelism
- **Hardware**: 560 NVIDIA A100 80GB GPUs

**Architecture**:
```yaml
Parameters: 530 billion
Layers: 105
Hidden size: 20480
Attention heads: 128
Vocabulary: 51,200 tokens
Sequence length: 2048
```

**Training Configuration**:
```bash
# 560 A100 80GB GPUs
Tensor Parallel: 8
Pipeline Parallel: 35
Data Parallel: 2
Total: 8 × 35 × 2 = 560

DeepSpeed ZeRO Stage 3:
- Full parameter sharding
- Gradient sharding
- Optimizer state sharding
```

**Innovations**:
- First to combine DeepSpeed ZeRO-3 with Megatron parallelism
- Demonstrated training at 500B+ scale
- Proved viability of extreme parallelism

**Performance**:
- Trained on 339 billion tokens
- Multiple months of training
- Achieved state-of-the-art results in 2021

## BigScience BLOOM 176B

### Overview
- **Organization**: BigScience (1000+ researchers)
- **Parameters**: 176 billion
- **Year**: 2022
- **Framework**: Megatron-DeepSpeed
- **Hardware**: 384 NVIDIA A100 80GB GPUs
- **Training Duration**: 46 days

**Architecture**:
```yaml
Parameters: 176 billion
Layers: 70
Hidden size: 14336
Attention heads: 112
Vocabulary: 250,680 tokens (multilingual)
Sequence length: 2048
Languages: 46 natural languages + 13 programming languages
```

**Training Configuration**:
```bash
# 384 A100 80GB GPUs on Jean Zay supercomputer
Tensor Parallel: 4
Pipeline Parallel: 12
Data Parallel: 8
Total: 4 × 12 × 8 = 384

Global batch size: 2048
Micro-batch size: 4
Learning rate: 6e-5
Optimizer: Adam (β1=0.9, β2=0.95)
```

**Training Data**:
- 366 billion tokens (1.6TB)
- ROOTS corpus (custom multilingual dataset)
- 46 natural languages
- 13 programming languages

**Key Achievements**:
- Largest multilingual open-source model at release
- Trained on public supercomputer (Jean Zay)
- Fully documented training process
- Open-source model and training code

**Public Impact**:
- Downloaded 100,000+ times
- Used in hundreds of research papers
- Enabled multilingual AI research
- Demonstrated open science at scale

## DeepSeek-V3

### Overview
- **Organization**: DeepSeek
- **Parameters**: 671 billion total, 37B active per token
- **Type**: Mixture of Experts (MoE)
- **Year**: 2024-2025
- **Framework**: Megatron-Core

**Architecture**:
```yaml
Type: Mixture of Experts
Total parameters: 671B
Active parameters per token: 37B
Layers: 61
Hidden size: 7168
Attention heads: 128
Query groups: 16
Experts: 256 (massive MoE)
Router top-k: 8 (Multi-head Latent Attention)
Shared expert size: 18432
```

**Training Configuration**:
```bash
# 1024 H100 GPUs
Tensor Parallel (TP): 2
Pipeline Parallel (PP): 16
Expert Parallel (EP): 64
Context Parallel (CP): 1

Total: 2 × 16 × 64 = 2048 slots
# Uses overlapping parallelism

Global batch size: 4096
Sequence length: 4096
Training tokens: 14.8 trillion
```

**Innovations**:
- Multi-head Latent Attention (MLA) router
- Shared experts + routed experts
- Ultra-large expert count (256)
- Advanced load balancing

**Performance**:
- Competitive with GPT-4
- 37B active params rivals 70B+ dense models
- Efficient inference (only 37B active)

## OpenAI GPT-3 175B (2020)

### Overview
- **Organization**: OpenAI
- **Parameters**: 175 billion
- **Year**: 2020
- **Framework**: Megatron-inspired custom implementation
- **Hardware**: Thousands of NVIDIA V100 GPUs

**Architecture**:
```yaml
Parameters: 175 billion
Layers: 96
Hidden size: 12288
Attention heads: 96
FFN size: 49152
Vocabulary: 50,257 tokens (GPT-2 BPE)
Sequence length: 2048
Context window: 2048 tokens
```

**Training Configuration**:
```bash
# Estimated configuration
Tensor Parallel: 4-8
Pipeline Parallel: 8-16
Data Parallel: Remaining GPUs

Global batch size: 1536
Learning rate: 6e-5
Training tokens: 300 billion
```

**Training Compute**:
- 3.14 × 10^23 FLOPs
- Equivalent to ~355 GPU-years on V100
- Estimated cost: $4-12 million

**Impact**:
- Launched modern era of large language models
- Demonstrated few-shot learning
- Foundation for ChatGPT

## Stability AI StableLM

### Overview
- **Organization**: Stability AI
- **Framework**: GPT-NeoX (Megatron + DeepSpeed)
- **Hardware**: Training on supercomputers
- **Status**: Open-source

**Models**:
- StableLM-Base-Alpha: 3B, 7B
- StableLM-Tuned-Alpha: Fine-tuned versions
- StableCode: Code-specialized

**Training Configuration**:
```yaml
Framework: GPT-NeoX
Parallelism: Megatron TP/PP + DeepSpeed ZeRO
GPUs: A100 clusters
Training data: 1.5 trillion tokens (The Pile)
```

**Key Features**:
- Fully open-source (Apache 2.0)
- GPT-NeoX framework
- Trained on The Pile dataset
- Multiple model sizes

## Common Production Patterns

### Fault Tolerance

**Checkpoint Strategy**:
```bash
--save-interval 500              # Save every 500 iterations
--save /checkpoints/model_name  # Checkpoint directory
--load /checkpoints/model_name  # Auto-resume from latest
```

**Monitoring**:
```python
# Check in progress.txt
Job throughput: 45.2 TFLOPs/GPU
Cumulative throughput: 44.8 TFLOPs/GPU
Memory usage: 68.2 GB / 80 GB
Loss: 2.143
```

### Data Pipeline

**Preprocessing**:
```bash
python tools/preprocess_data.py \
  --input data.jsonl \
  --output-prefix /data/processed \
  --vocab-file vocab.json \
  --merge-file merges.txt \
  --tokenizer-type GPT2BPETokenizer \
  --append-eod \
  --workers 64
```

**Training with Preprocessed Data**:
```bash
--data-path /data/processed_text_document \
--split 969,30,1  # Train/valid/test split
```

### Monitoring & Logging

**Key Metrics to Track**:
```bash
# Training metrics
- Loss (should steadily decrease)
- Learning rate (follows schedule)
- Gradient norm (watch for spikes)
- Throughput (TFlops/GPU)
- MFU percentage

# System metrics
- GPU utilization (>90%)
- Memory usage (<95% of capacity)
- Network bandwidth (saturated for TP)
- Data loading time (should be minimal)
```

**Production Monitoring Tools**:
- TensorBoard for loss curves
- Weights & Biases for experiment tracking
- Prometheus + Grafana for system metrics
- Custom scripts for MFU calculation

### Multi-Datacenter Training

**Challenges**:
- Higher latency between datacenters
- Network bandwidth limitations
- Fault isolation

**Solutions**:
```bash
# Keep TP within datacenter
--tensor-model-parallel-size 8  # Single node only

# Use PP across datacenters
--pipeline-model-parallel-size 16  # Across sites

# Data parallel across everything
# Automatic from remaining GPUs
```

## Lessons from Production

1. **Fault Tolerance is Critical**
   - Save checkpoints frequently (every 500-1000 steps)
   - Test checkpoint recovery regularly
   - Monitor for GPU failures

2. **Data Quality Matters More Than Quantity**
   - LLaMA 3: Carefully curated 15T tokens
   - Better than naive web scraping
   - Investment in data preprocessing pays off

3. **Parallelism Strategy Evolves with Scale**
   - <70B: TP + DP sufficient
   - 70-175B: Add PP
   - 175B+: 3D or 4D parallelism required
   - MoE: Add EP dimension

4. **Hardware Matters**
   - H100 vs A100: 2× speedup from better hardware
   - NVLink topology affects TP efficiency
   - InfiniBand essential for multi-node

5. **Monitoring is Essential**
   - Track MFU to catch performance issues
   - Monitor loss for training health
   - Watch memory usage to avoid OOM
   - Log everything for debugging

## References

- Meta LLaMA 3 technical report
- NVIDIA Nemotron blog posts
- Microsoft Megatron-Turing NLG paper
- BigScience BLOOM documentation
- DeepSeek-V3 technical report




### Training Recipes

# Training Recipes

Complete hyperparameter configurations and training recipes for GPT, LLaMA, and Mixtral models.

## GPT-3 Training Recipes

### GPT-3 15B Configuration

**Model Architecture**:
```yaml
num-layers: 32
hidden-size: 6144
num-attention-heads: 48
ffn-hidden-size: 24576  # 4 × hidden-size
seq-length: 4096
max-position-embeddings: 4096
position-embedding-type: rope
squared-relu: true
group-query-attention: true
num-query-groups: 8
```

**Training Hyperparameters**:
```yaml
# Batch Configuration
micro-batch-size: 4
global-batch-size: 1152
rampup-batch-size: [384, 384, 97656250]  # start, increment, total samples

# Learning Rate Schedule
lr: 4.5e-4
min-lr: 4.5e-5
lr-decay-style: cosine
lr-decay-samples: 1949218748
lr-warmup-samples: 3906252  # ~2B tokens with seq_len=4096

# Optimizer
optimizer: adam
adam-beta1: 0.9
adam-beta2: 0.95
weight-decay: 0.1
clip-grad: 1.0

# Precision
bf16: true

# Parallelism
tensor-model-parallel-size: 8
pipeline-model-parallel-size: 1
sequence-parallel: true
use-distributed-optimizer: true
overlap-grad-reduce: true
overlap-param-gather: true
```

**Command**:
```bash
torchrun --nproc_per_node=8 --nnodes=4 pretrain_gpt.py \
  --num-layers 32 \
  --hidden-size 6144 \
  --num-attention-heads 48 \
  --ffn-hidden-size 24576 \
  --seq-length 4096 \
  --max-position-embeddings 4096 \
  --micro-batch-size 4 \
  --global-batch-size 1152 \
  --lr 4.5e-4 \
  --min-lr 4.5e-5 \
  --lr-decay-style cosine \
  --lr-warmup-samples 3906252 \
  --train-samples 1953125000 \
  --adam-beta1 0.9 \
  --adam-beta2 0.95 \
  --weight-decay 0.1 \
  --clip-grad 1.0 \
  --bf16 \
  --tensor-model-parallel-size 8 \
  --pipeline-model-parallel-size 1 \
  --sequence-parallel \
  --use-distributed-optimizer \
  --overlap-grad-reduce \
  --overlap-param-gather \
  --data-path /path/to/data \
  --vocab-file /path/to/vocab.json \
  --merge-file /path/to/merges.txt \
  --save /checkpoints/gpt3-15b \
  --load /checkpoints/gpt3-15b \
  --save-interval 1000 \
  --eval-interval 100
```

### GPT-3 175B Configuration

**Model Architecture**:
```yaml
num-layers: 96
hidden-size: 12288
num-attention-heads: 96
ffn-hidden-size: 49152
seq-length: 2048
max-position-embeddings: 2048
```

**Training Hyperparameters**:
```yaml
micro-batch-size: 1
global-batch-size: 1536
lr: 6e-5
min-lr: 6e-6
lr-decay-style: cosine
lr-warmup-steps: 2000
train-iters: 150000
adam-beta1: 0.9
adam-beta2: 0.95
weight-decay: 0.1
clip-grad: 1.0
bf16: true

# Parallelism for 512 GPUs
tensor-model-parallel-size: 4
pipeline-model-parallel-size: 8
# Data parallel: 512 / (4 * 8) = 16
```

## LLaMA Training Recipes

### LLaMA-3 8B

**Model Architecture**:
```yaml
num-layers: 32
hidden-size: 4096
num-attention-heads: 32
num-query-groups: 8  # GQA
ffn-hidden-size: 14336
seq-length: 8192
max-position-embeddings: 8192
position-embedding-type: rope
rope-theta: 500000
normalization: RMSNorm
swiglu: true
untie-embeddings-and-output-weights: true
```

**Training Hyperparameters**:
```yaml
micro-batch-size: 4
global-batch-size: 128
lr: 3e-4
min-lr: 3e-5
lr-decay-style: cosine
lr-warmup-iters: 2000
train-iters: 100000
adam-beta1: 0.9
adam-beta2: 0.95
weight-decay: 0.1
clip-grad: 1.0
bf16: true

# Parallelism for 8 GPUs
tensor-model-parallel-size: 1
pipeline-model-parallel-size: 1
context-parallel-size: 2  # For 8K sequences
```

**FP8 Training** (H100):
```bash
./examples/llama/train_llama3_8b_fp8.sh
```

Contents:
```bash
#!/bin/bash
torchrun --nproc_per_node=8 pretrain_gpt.py \
  --num-layers 32 \
  --hidden-size 4096 \
  --num-attention-heads 32 \
  --num-query-groups 8 \
  --ffn-hidden-size 14336 \
  --seq-length 8192 \
  --max-position-embeddings 8192 \
  --micro-batch-size 2 \
  --global-batch-size 128 \
  --lr 3e-4 \
  --train-iters 100000 \
  --lr-decay-style cosine \
  --lr-warmup-iters 2000 \
  --weight-decay 0.1 \
  --clip-grad 1.0 \
  --fp8-hybrid \
  --fp8-amax-history-len 1024 \
  --fp8-amax-compute-algo max \
  --apply-query-key-layer-scaling \
  --attention-softmax-in-fp32 \
  --tensor-model-parallel-size 1 \
  --pipeline-model-parallel-size 1 \
  --context-parallel-size 2 \
  --sequence-parallel \
  --use-mcore-models \
  --transformer-impl transformer_engine \
  --data-path /data/llama_train \
  --vocab-file /data/tokenizer.model \
  --save-interval 1000
```

### LLaMA-3 70B

**Model Architecture**:
```yaml
num-layers: 80
hidden-size: 8192
num-attention-heads: 64
num-query-groups: 8
ffn-hidden-size: 28672
seq-length: 4096
max-position-embeddings: 4096
position-embedding-type: rope
rope-theta: 500000
normalization: RMSNorm
swiglu: true
```

**Training Hyperparameters**:
```yaml
micro-batch-size: 1
global-batch-size: 1024
lr: 1.5e-4
min-lr: 1.5e-5
lr-decay-style: cosine
lr-warmup-iters: 2000
adam-beta1: 0.9
adam-beta2: 0.95
weight-decay: 0.1
clip-grad: 1.0
bf16: true

# Parallelism for 64 GPUs
tensor-model-parallel-size: 4
pipeline-model-parallel-size: 4
context-parallel-size: 2
# Data parallel: 64 / (4 * 4 * 2) = 2
```

### LLaMA-3.1 405B

**Model Architecture**:
```yaml
num-layers: 126
hidden-size: 16384
num-attention-heads: 128
num-query-groups: 8
ffn-hidden-size: 53248
seq-length: 4096
max-position-embeddings: 131072  # Supports up to 128K
position-embedding-type: rope
rope-theta: 500000
```

**Training Hyperparameters**:
```yaml
micro-batch-size: 1
global-batch-size: 2048
lr: 8e-5
min-lr: 8e-6
lr-decay-style: cosine
lr-warmup-iters: 8000
train-samples: 15000000000000  # 15T tokens
adam-beta1: 0.9
adam-beta2: 0.95
weight-decay: 0.1
clip-grad: 1.0
bf16: true

# Parallelism for 1024 GPUs
tensor-model-parallel-size: 8
pipeline-model-parallel-size: 8
context-parallel-size: 2
# Data parallel: 1024 / (8 * 8 * 2) = 8
```

**Production Configuration** (Meta):
```bash
torchrun --nproc_per_node=8 --nnodes=128 pretrain_gpt.py \
  --num-layers 126 \
  --hidden-size 16384 \
  --num-attention-heads 128 \
  --num-query-groups 8 \
  --ffn-hidden-size 53248 \
  --seq-length 4096 \
  --max-position-embeddings 131072 \
  --micro-batch-size 1 \
  --global-batch-size 2048 \
  --lr 8e-5 \
  --min-lr 8e-6 \
  --lr-decay-style cosine \
  --lr-warmup-iters 8000 \
  --train-samples 3662109375 \
  --adam-beta1 0.9 \
  --adam-beta2 0.95 \
  --weight-decay 0.1 \
  --clip-grad 1.0 \
  --bf16 \
  --tensor-model-parallel-size 8 \
  --pipeline-model-parallel-size 8 \
  --context-parallel-size 2 \
  --sequence-parallel \
  --use-distributed-optimizer \
  --overlap-grad-reduce \
  --overlap-param-gather \
  --use-flash-attn-v2 \
  --position-embedding-type rope \
  --normalization RMSNorm \
  --swiglu \
  --untie-embeddings-and-output-weights \
  --use-mcore-models \
  --transformer-impl transformer_engine \
  --data-path /data/llama3_pretraining \
  --vocab-file /data/llama3_tokenizer.model \
  --save /checkpoints/llama3-405b \
  --save-interval 500 \
  --eval-interval 100
```

## Mixtral Training Recipes

### Mixtral 8×7B (56B Total, 13B Active)

**Model Architecture**:
```yaml
num-layers: 32
hidden-size: 4096
num-attention-heads: 32
num-query-groups: 8
ffn-hidden-size: 14336
seq-length: 4096
max-position-embeddings: 32768  # Sliding window
position-embedding-type: rope
normalization: RMSNorm
swiglu: true

# MoE Configuration
num-experts: 8
moe-router-topk: 2  # Activate 2 experts per token
moe-router-load-balancing-type: aux_loss
moe-aux-loss-coeff: 0.01
```

**Training Hyperparameters**:
```yaml
micro-batch-size: 2
global-batch-size: 512
lr: 1e-4
min-lr: 1e-5
lr-decay-style: cosine
lr-warmup-iters: 2000
adam-beta1: 0.9
adam-beta2: 0.95
weight-decay: 0.1
clip-grad: 1.0
bf16: true

# Parallelism for 64 GPUs
tensor-model-parallel-size: 1
pipeline-model-parallel-size: 4
expert-model-parallel-size: 8
context-parallel-size: 1
# Data parallel: 64 / (1 * 4 * 8 * 1) = 2
```

**Training Command**:
```bash
torchrun --nproc_per_node=8 --nnodes=8 pretrain_gpt.py \
  --num-layers 32 \
  --hidden-size 4096 \
  --num-attention-heads 32 \
  --num-query-groups 8 \
  --ffn-hidden-size 14336 \
  --seq-length 4096 \
  --max-position-embeddings 32768 \
  --micro-batch-size 2 \
  --global-batch-size 512 \
  --lr 1e-4 \
  --min-lr 1e-5 \
  --lr-decay-style cosine \
  --lr-warmup-iters 2000 \
  --train-iters 100000 \
  --adam-beta1 0.9 \
  --adam-beta2 0.95 \
  --weight-decay 0.1 \
  --clip-grad 1.0 \
  --bf16 \
  --tensor-model-parallel-size 1 \
  --pipeline-model-parallel-size 4 \
  --expert-model-parallel-size 8 \
  --num-experts 8 \
  --moe-router-topk 2 \
  --moe-router-load-balancing-type aux_loss \
  --moe-aux-loss-coeff 0.01 \
  --position-embedding-type rope \
  --normalization RMSNorm \
  --swiglu \
  --use-mcore-models \
  --transformer-impl transformer_engine \
  --data-path /data/mixtral_train \
  --vocab-file /data/mixtral_tokenizer.model \
  --save /checkpoints/mixtral-8x7b \
  --save-interval 1000
```

### Mixtral 8×22B (176B Total, 39B Active)

**Model Architecture**:
```yaml
num-layers: 56
hidden-size: 6144
num-attention-heads: 48
num-query-groups: 8
ffn-hidden-size: 16384
seq-length: 4096
max-position-embeddings: 65536

# MoE Configuration
num-experts: 8
moe-router-topk: 2
```

**Training Hyperparameters**:
```yaml
micro-batch-size: 1
global-batch-size: 1024
lr: 7e-5
min-lr: 7e-6
lr-decay-style: cosine
adam-beta1: 0.9
adam-beta2: 0.95
weight-decay: 0.1
clip-grad: 1.0
bf16: true

# Parallelism for 256 GPUs
tensor-model-parallel-size: 4
pipeline-model-parallel-size: 4
expert-model-parallel-size: 8
# Data parallel: 256 / (4 * 4 * 8) = 2
```

## DeepSeek-V3 (671B Total, 37B Active)

**Model Architecture**:
```yaml
num-layers: 61
hidden-size: 7168
num-attention-heads: 128
num-query-groups: 16
ffn-hidden-size: 18432

# MoE Configuration
num-experts: 256
moe-router-topk: 8  # Multi-head latent attention
shared-expert-intermediate-size: 18432
```

**Training Hyperparameters**:
```yaml
micro-batch-size: 1
global-batch-size: 4096
lr: 2.7e-4
min-lr: 2.7e-5
lr-decay-style: cosine
lr-warmup-tokens: 5B
train-tokens: 14.8T
adam-beta1: 0.9
adam-beta2: 0.95
weight-decay: 0.1
clip-grad: 1.0
bf16: true

# Parallelism for 1024 GPUs
tensor-model-parallel-size: 2
pipeline-model-parallel-size: 16
expert-model-parallel-size: 64
# Data parallel: 1024 / (2 * 16 * 64) = 0.5 (overlapping)
```

## Common Training Patterns

### Batch Size Ramp-Up

Many models use gradual batch size increase:

```yaml
rampup-batch-size: [start_batch, increment, total_samples]
# Example: [384, 384, 97656250]
# Start with 384, increase by 384 every step until total_samples
```

### Learning Rate Schedules

**Cosine Decay** (most common):
```python
lr(step) = min_lr + 0.5 * (max_lr - min_lr) * (1 + cos(π * step / total_steps))
```

**Linear Warmup + Cosine Decay**:
```python
if step < warmup_steps:
    lr(step) = max_lr * step / warmup_steps
else:
    lr(step) = cosine_decay(step - warmup_steps)
```

### Optimizer Settings

**Standard Adam**:
```yaml
optimizer: adam
adam-beta1: 0.9
adam-beta2: 0.95  # Lower than typical 0.999
weight-decay: 0.1
clip-grad: 1.0
```

**Why beta2=0.95?**
- More responsive to recent gradients
- Better for large-scale training
- Proven in GPT-3, LLaMA, Mixtral

### Data Configuration

**Vocabulary Sizes**:
- GPT-3: 50,257 tokens
- LLaMA-3: 128,256 tokens (expanded for multilingual)
- Mixtral: 32,000 tokens

**Typical Data Mix** (by tokens):
- Web pages: 60-70%
- Books: 10-15%
- GitHub code: 5-10%
- Academic papers: 5-10%
- Other (Wikipedia, etc.): 5-10%

## References

- Megatron-LM configurations: `tests/functional_tests/test_cases/`
- LLaMA-3 training: Meta AI technical report
- Mixtral training: Mistral AI blog
- DeepSeek-V3: DeepSeek technical report




### Benchmarks

# Performance Benchmarks

Performance metrics and benchmarks for Megatron-Core across different model sizes and hardware configurations.

## Model FLOP Utilization (MFU)

**H100 Clusters**: Up to 47% MFU achieved

MFU increases with larger model sizes due to higher arithmetic intensity in larger matrix multiplications (GEMMs).

## Throughput Metrics by Model Size

### GPT-3 175B
- **Hardware**: H100
- **Configuration**: TP=4, PP=8
- **GPUs**: 128-512
- **MFU**: 47% on H100
- **Throughput**: 390 TFlops/GPU on H100

### LLaMA Configurations

| Model | Size | GPUs | TP | PP | CP | Seq Length | Hardware | Notes |
|-------|------|------|----|----|----| -----------|----------|-------|
| LLaMA-3 | 8B | 8 | 1 | 1 | 2 | 8K | H100 | CP for long sequences |
| LLaMA-3 | 70B | 64 | 4 | 4 | 2 | 4K | H100 | TP+PP parallelism |
| LLaMA-3.1 | 405B | 1024 | 8 | 8 | 2 | 4K | H100 | 3D parallelism |

**LLaMA-3 405B Details**:
- 16K H100 GPUs (two 24K GPU clusters)
- TP=8, PP=8, CP=2
- 400 TFlops/GPU average
- 95%+ uptime
- 3× efficiency improvement vs LLaMA 2

### Mixtral (Mixture of Experts)

| Model | Active Params | Total Params | GPUs | TP | PP | EP | Experts | Hardware |
|-------|---------------|--------------|------|----|----|----|---------| ---------|
| Mixtral | 7B (active) | 8×7B (56B) | 64 | 1 | 4 | 8 | 8 | H100 |
| Mixtral | 22B (active) | 8×22B (176B) | 256 | 4 | 4 | 8 | 8 | H100 |

### DeepSeek-V3

- **Active Parameters**: 37B per token
- **Total Parameters**: 671B
- **GPUs**: 1024 H100
- **Configuration**: TP=2, PP=16, EP=64
- **Parallelism**: 4D with Expert Parallel

### GPT-462B (Largest Benchmark)

- **Parameters**: 462B
- **GPUs**: 6144 H100
- **MFU**: 47-48%
- **Throughput**: ~390 TFlops/GPU

## Hardware Performance Characteristics

### NVIDIA H100 (Hopper)
- **Peak Performance**:
  - FP16: 1979 TFlops
  - BF16: 1979 TFlops
  - FP8: 3958 TFlops
- **Memory**: 80GB HBM3
- **Memory Bandwidth**: 3.35 TB/s
- **NVLink**: 900 GB/s per GPU

**Achieved MFU**: 40-47% (typical range)

### NVIDIA A100 (Ampere)
- **Peak Performance**:
  - FP16: 312 TFlops (with sparsity)
  - BF16: 312 TFlops
- **Memory**: 40GB or 80GB HBM2e
- **Memory Bandwidth**: 2 TB/s
- **NVLink**: 600 GB/s per GPU

**Typical MFU**: 35-42%

## Weak Scaling (Fixed Per-GPU Workload)

As you add more GPUs while keeping per-GPU workload constant:

| GPUs | Model Size | MFU | Efficiency |
|------|------------|-----|------------|
| 8 | 7B | 42% | 100% (baseline) |
| 64 | 70B | 44% | 95% |
| 512 | 175B | 45% | 93% |
| 1024 | 405B | 46% | 90% |
| 6144 | 462B | 47% | 88% |

## Strong Scaling (Fixed Total Workload)

Distributing a fixed model across more GPUs:

| Model | GPUs | Time per Iteration | Speedup | Efficiency |
|-------|------|-------------------|---------|------------|
| 70B | 64 | 1.0× (baseline) | 1.0× | 100% |
| 70B | 128 | 0.52× | 1.92× | 96% |
| 70B | 256 | 0.27× | 3.70× | 93% |

## Throughput Calculations

**Formula**:
```
Throughput (TFlops/GPU) = Total FLOPs / (Time × Number of GPUs × 10^12)
```

**Example (GPT-3 175B)**:
- Forward + Backward pass: 3 × (model FLOPs)
- Per-token FLOPs: ~350 billion for 175B model
- Batch size: 1536 (global)
- Sequence length: 2048
- Time per iteration: ~5 seconds on 512 H100s
- Throughput: ~390 TFlops/GPU

## Memory Usage vs Model Size

| Model Size | Parameters | Memory (FP16) | Memory (BF16) | Memory (FP8) |
|------------|------------|---------------|---------------|--------------|
| 7B | 7 billion | 14 GB | 14 GB | 7 GB |
| 13B | 13 billion | 26 GB | 26 GB | 13 GB |
| 70B | 70 billion | 140 GB | 140 GB | 70 GB |
| 175B | 175 billion | 350 GB | 350 GB | 175 GB |
| 405B | 405 billion | 810 GB | 810 GB | 405 GB |

**Note**: These are model weights only. Add ~2× for gradients and optimizer states during training.

## Communication Overhead

### Tensor Parallelism (TP)
- **Bandwidth Required**: ~20 GB/GPU for LLaMA 70B with TP=4
- **Frequency**: Every layer (80+ layers)
- **Best Practice**: Use NVLink, keep TP ≤8 within single node

### Pipeline Parallelism (PP)
- **Bandwidth Required**: Activation size only (~100s of MB)
- **Frequency**: Between pipeline stages
- **Best Practice**: Use for cross-node scaling

### Data Parallelism (DP)
- **Bandwidth Required**: Full gradient size
- **Frequency**: Once per iteration
- **Best Practice**: Use for remaining parallelism after TP/PP

## Optimization Impact

### Flash Attention
- **Speedup**: 2-4× on attention layers
- **Memory**: 10-20× reduction
- **Overall Impact**: ~30% faster training

### Sequence Parallelism
- **Memory Savings**: Activation memory / TP degree
- **Example**: With TP=4, saves 75% of activation memory
- **No Performance Cost**: Communication already happening

### Context Parallelism
- **Use Case**: Sequences >8K tokens
- **Memory Savings**: KV cache / CP degree
- **Communication**: Ring all-to-all pattern

### FP8 Training (H100 Only)
- **Speedup**: 1.5-2× vs BF16
- **Memory**: 50% reduction vs BF16
- **Quality**: Minimal degradation with proper scaling

## Production Deployments

### Meta LLaMA 3 Training
- **Models**: 8B, 70B, 405B
- **Cluster**: Two 24K H100 clusters
- **Efficiency**: 400 TFlops/GPU sustained
- **Uptime**: 95%+
- **Total Tokens**: 15 trillion for 405B model

### Microsoft Megatron-Turing NLG 530B
- **GPUs**: 560 NVIDIA A100 (80GB)
- **Parallelism**: DeepSpeed ZeRO-3 + Megatron TP/PP
- **Duration**: Several months
- **Year**: 2021

### NVIDIA Nemotron-4 340B
- **Architecture**: Mixture of Experts
- **Framework**: NeMo (built on Megatron-Core)
- **Production**: Commercial deployment

## Benchmarking Best Practices

1. **Measure Sustained Performance**: Not peak, measure over 100+ iterations
2. **Include All Operations**: Forward, backward, optimizer step, communication
3. **Report MFU**: Use theoretical peak FLOPs of hardware
4. **Specify Configuration**: TP, PP, CP, EP degrees, batch sizes, sequence length
5. **Note Optimizations**: Flash Attention, FP8, sequence parallel, etc.

## How to Measure Your Own Performance

**Enable profiling**:
```bash
torchrun pretrain_gpt.py \
  --profile \
  --profile-step-start 10 \
  --profile-step-end 20
```

**Calculate MFU**:
```python
# Megatron logs this automatically
# Check logs for:
# - elapsed time per iteration (seconds)
# - samples per second
# - TFLOPs/s per GPU
# - MFU percentage
```

**Key Metrics to Track**:
- Elapsed time per iteration
- Throughput (TFlops/GPU)
- MFU (%)
- Memory usage (GB)
- Communication time (% of total)

## Troubleshooting Low Performance

**If MFU < 30%**:
1. Check micro-batch size (increase if possible)
2. Enable all optimizations (Flash Attention, sequence parallel, etc.)
3. Verify communication backend (NCCL properly configured)
4. Check for data loading bottlenecks
5. Ensure proper CPU-GPU pipeline

**If Communication Heavy** (>30% of time):
1. Reduce TP degree (especially across nodes)
2. Use interleaved pipeline schedule
3. Enable communication overlap flags
4. Check network topology (InfiniBand vs Ethernet)

**If Memory Bound**:
1. Enable gradient checkpointing
2. Use lower precision (BF16 or FP8)
3. Increase parallelism degrees
4. Reduce micro-batch size

## References

- NVIDIA Megatron-LM GitHub: https://github.com/NVIDIA/Megatron-LM
- Performance Docs: https://docs.nvidia.com/megatron-core/
- LLaMA 3 Paper: Meta AI
- DeepSeek-V3 Technical Report




---

## 🚀 Usage

**Reference this template:** `@skill-training-llms-megatron.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
