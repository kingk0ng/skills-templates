---
id: skill-openrlhf-training
type: skill
name: openrlhf-training
description: High-performance RLHF framework with Ray+vLLM acceleration. Use for PPO,
  GRPO, RLOO, DPO training of large models (7B-70B+). Built on Ray, vLLM, ZeRO-3.
  2× faster than DeepSpeedChat with distributed architecture and GPU resource sharing.
category: ai-research
complexity: medium
keywords:
- api
- docker
- github
- optimization
- performance
capabilities: []
token_estimate: 1085
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,085 -->
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




# openrlhf-training

> High-performance RLHF framework with Ray+vLLM acceleration. Use for PPO, GRPO, RLOO, DPO training of large models (7B-70B+). Built on Ray, vLLM, ZeRO-3. 2× faster than DeepSpeedChat with distributed architecture and GPU resource sharing.

# OpenRLHF - High-Performance RLHF Training

## Quick start

OpenRLHF is a Ray-based RLHF framework optimized for distributed training with vLLM inference acceleration.

**Installation**:
```bash
# Launch Docker container
docker run --runtime=nvidia -it --rm --shm-size="10g" --cap-add=SYS_ADMIN \
  -v $PWD:/openrlhf nvcr.io/nvidia/pytorch:25.02-py3 bash

# Uninstall conflicts
sudo pip uninstall xgboost transformer_engine flash_attn pynvml -y

# Install OpenRLHF with vLLM
pip install openrlhf[vllm]
```

**PPO Training** (Hybrid Engine):
```bash
ray start --head --node-ip-address 0.0.0.0 --num-gpus 8

ray job submit --address="http://127.0.0.1:8265" \
  --runtime-env-json='{"working_dir": "/openrlhf"}' \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --ref_num_nodes 1 --ref_num_gpus_per_node 8 \
  --reward_num_nodes 1 --reward_num_gpus_per_node 8 \
  --critic_num_nodes 1 --critic_num_gpus_per_node 8 \
  --actor_num_nodes 1 --actor_num_gpus_per_node 8 \
  --vllm_num_engines 4 --vllm_tensor_parallel_size 2 \
  --colocate_all_models \
  --vllm_gpu_memory_utilization 0.5 \
  --pretrain OpenRLHF/Llama-3-8b-sft-mixture \
  --reward_pretrain OpenRLHF/Llama-3-8b-rm-700k \
  --save_path ./output/llama3-8b-rlhf \
  --micro_train_batch_size 8 --train_batch_size 128 \
  --micro_rollout_batch_size 16 --rollout_batch_size 1024 \
  --max_epochs 1 --prompt_max_len 1024 --generate_max_len 1024 \
  --zero_stage 3 --bf16 \
  --actor_learning_rate 5e-7 --critic_learning_rate 9e-6 \
  --init_kl_coef 0.01 --normalize_reward \
  --gradient_checkpointing --packing_samples \
  --vllm_enable_sleep --deepspeed_enable_sleep
```

**GRPO Training** (Group Normalized Policy Optimization):
```bash
# Same command as PPO, but add:
--advantage_estimator group_norm
```

## Common workflows

### Workflow 1: Full RLHF pipeline (SFT → Reward Model → PPO)

**Step 1: Train reward model** (DPO):
```bash
deepspeed --module openrlhf.cli.train_rm \
  --save_path ./output/llama3-8b-rm \
  --save_steps -1 --logging_steps 1 \
  --eval_steps -1 --train_batch_size 256 \
  --micro_train_batch_size 1 --pretrain meta-llama/Meta-Llama-3-8B \
  --bf16 --max_epochs 1 --max_len 8192 \
  --zero_stage 3 --learning_rate 9e-6 \
  --dataset OpenRLHF/preference_dataset_mixture2_and_safe_pku \
  --apply_chat_template --chosen_key chosen \
  --rejected_key rejected --flash_attn --gradient_checkpointing
```

**Step 2: PPO training**:
```bash
ray start --head --node-ip-address 0.0.0.0 --num-gpus 8

ray job submit --address="http://127.0.0.1:8265" \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --ref_num_nodes 1 --ref_num_gpus_per_node 8 \
  --reward_num_nodes 1 --reward_num_gpus_per_node 8 \
  --critic_num_nodes 1 --critic_num_gpus_per_node 8 \
  --actor_num_nodes 1 --actor_num_gpus_per_node 8 \
  --vllm_num_engines 4 --vllm_tensor_parallel_size 2 \
  --colocate_all_models \
  --pretrain OpenRLHF/Llama-3-8b-sft-mixture \
  --reward_pretrain ./output/llama3-8b-rm \
  --save_path ./output/llama3-8b-ppo \
  --micro_train_batch_size 8 --train_batch_size 128 \
  --micro_rollout_batch_size 16 --rollout_batch_size 1024 \
  --max_epochs 1 --prompt_max_len 1024 --generate_max_len 1024 \
  --zero_stage 3 --bf16 \
  --actor_learning_rate 5e-7 --critic_learning_rate 9e-6 \
  --init_kl_coef 0.01 --normalize_reward \
  --vllm_enable_sleep --deepspeed_enable_sleep
```

### Workflow 2: GRPO training (no critic model needed)

Memory-efficient alternative to PPO:

```bash
ray job submit --address="http://127.0.0.1:8265" \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --advantage_estimator group_norm \
  --ref_num_nodes 1 --ref_num_gpus_per_node 8 \
  --reward_num_nodes 1 --reward_num_gpus_per_node 8 \
  --actor_num_nodes 1 --actor_num_gpus_per_node 8 \
  --vllm_num_engines 4 --vllm_tensor_parallel_size 2 \
  --colocate_all_models \
  --pretrain OpenRLHF/Llama-3-8b-sft-mixture \
  --reward_pretrain OpenRLHF/Llama-3-8b-rm-700k \
  --save_path ./output/llama3-8b-grpo \
  --micro_train_batch_size 8 --train_batch_size 128 \
  --micro_rollout_batch_size 16 --rollout_batch_size 1024 \
  --max_epochs 1 --bf16 \
  --actor_learning_rate 5e-7 \
  --init_kl_coef 0.01 --use_kl_loss --kl_estimator k3 \
  --normalize_reward --no_advantage_std_norm
```

**Key GRPO parameters**:
- `--advantage_estimator group_norm` - Enables GRPO
- `--use_kl_loss` - KL loss from GRPO paper
- `--kl_estimator k3` - Loss function (k2 ≈ k1)
- `--no_advantage_std_norm` - Disables std normalization

### Workflow 3: DPO training (preference optimization)

Simpler alternative without reward model:

```bash
deepspeed --module openrlhf.cli.train_dpo \
  --save_path ./output/llama3-8b-dpo \
  --save_steps -1 --logging_steps 1 \
  --eval_steps -1 --train_batch_size 256 \
  --micro_train_batch_size 2 --pretrain meta-llama/Meta-Llama-3-8B \
  --bf16 --max_epochs 1 --max_len 8192 \
  --zero_stage 3 --learning_rate 5e-7 --beta 0.1 \
  --dataset OpenRLHF/preference_dataset_mixture2_and_safe_pku \
  --apply_chat_template --chosen_key chosen \
  --rejected_key rejected --flash_attn --gradient_checkpointing
```

## When to use vs alternatives

**Use OpenRLHF when**:
- Training large models (7B-70B+) with RL
- Need vLLM inference acceleration
- Want distributed architecture with Ray
- Have multi-node GPU cluster
- Need PPO/GRPO/RLOO/DPO in one framework

**Algorithm selection**:
- **PPO**: Maximum control, best for complex rewards
- **GRPO**: Memory-efficient, no critic needed
- **RLOO**: Modified PPO with per-token KL
- **REINFORCE++**: More stable than GRPO, faster than PPO
- **DPO**: Simplest, no reward model needed

**Use alternatives instead**:
- **TRL**: Single-node training, simpler API
- **veRL**: ByteDance's framework for 671B models
- **DeepSpeedChat**: Integrated with DeepSpeed ecosystem

## Common issues

**Issue: GPU OOM with large models**

Disable model colocation:
```bash
# Remove --colocate_all_models flag
# Allocate separate GPUs for each model
--actor_num_gpus_per_node 8 \
--critic_num_gpus_per_node 8 \
--reward_num_gpus_per_node 8 \
--ref_num_gpus_per_node 8
```

**Issue: DeepSpeed GPU index out of range**

Set environment variable:
```bash
export RAY_EXPERIMENTAL_NOSET_CUDA_VISIBLE_DEVICES=1
```

**Issue: Training instability**

Use Hybrid Engine instead of async:
```bash
--colocate_all_models \
--vllm_enable_sleep \
--deepspeed_enable_sleep
```

Adjust KL coefficient:
```bash
--init_kl_coef 0.05  # Increase from 0.01
```

**Issue: Slow generation during PPO**

Enable vLLM acceleration:
```bash
--vllm_num_engines 4 \
--vllm_tensor_parallel_size 2 \
--vllm_gpu_memory_utilization 0.5
```

## Advanced topics

**Hybrid Engine GPU sharing**: See [references/hybrid-engine.md](references/hybrid-engine.md) for vLLM sleep mode, DeepSpeed sleep mode, and optimal node allocation.

**Algorithm comparison**: See [references/algorithm-comparison.md](references/algorithm-comparison.md) for PPO vs GRPO vs RLOO vs REINFORCE++ benchmarks and hyperparameters.

**Multi-node setup**: See [references/multi-node-training.md](references/multi-node-training.md) for Ray cluster configuration and fault tolerance.

**Custom reward functions**: See [references/custom-rewards.md](references/custom-rewards.md) for reinforced fine-tuning and agent RLHF.

## Hardware requirements

- **GPU**: NVIDIA A100/H100 recommended
- **VRAM**:
  - 7B model: 8× A100 40GB (Hybrid Engine)
  - 70B model: 48× A100 80GB (vLLM:Actor:Critic = 1:1:1)
- **Multi-node**: Ray cluster with InfiniBand recommended
- **Docker**: NVIDIA PyTorch container 25.02+

**Performance**:
- 2× faster than DeepSpeedChat
- vLLM inference acceleration
- Hybrid Engine minimizes GPU idle time

## Resources

- Docs: https://github.com/OpenRLHF/OpenRLHF
- Paper: https://arxiv.org/abs/2405.11143
- Examples: https://github.com/OpenRLHF/OpenRLHF/tree/main/examples
- Discord: Community support





---


## 📚 Reference Materials


### Hybrid Engine

# Hybrid Engine Architecture

Complete guide to OpenRLHF's GPU resource sharing system for maximizing utilization during RLHF training.

## Overview

The Hybrid Engine allows Actor, Critic, Reward, Reference models and vLLM engines to share GPU resources, minimizing idle time and maximizing GPU utilization through dynamic sleep/wake cycles.

## Architecture

### Core Components

**Enable Hybrid Engine**:
```bash
--colocate_all_models  # Enable GPU sharing across all models
```

**Components that share GPUs**:
1. **Actor Model** - Policy being trained
2. **Critic Model** - Value function for PPO
3. **Reward Model** - Scores completions
4. **Reference Model** - KL penalty baseline
5. **vLLM Engines** - Fast inference generation

### GPU Allocation Strategy

**Optimal ratio** (vLLM : Actor : Critic = 1:1:1):
```bash
# 70B model on 48× A100 GPUs
--vllm_num_engines 4          # 16 GPUs total
--vllm_tensor_parallel_size 4  # 4 GPUs per engine
--actor_num_nodes 1            # 16 GPUs
--actor_num_gpus_per_node 16
--critic_num_nodes 1           # 16 GPUs
--critic_num_gpus_per_node 16
```

**Constraint**: `actor_num_nodes * actor_num_gpus_per_node == vllm_num_engines * vllm_tensor_parallel_size`

## vLLM Sleep Mode

### How It Works

**Enable vLLM sleep**:
```bash
--vllm_enable_sleep
```

**Sleep/wake cycle**:
1. **Wake up** before generation: Load vLLM engines to GPU
2. **Generate** samples: vLLM performs inference
3. **Sleep** after generation: Offload vLLM engines to CPU

**Implementation**:
```python
# In SamplesGenerator.generate_samples()
batch_vllm_engine_call(self.vllm_engines, "wake_up")  # GPU ← CPU
# ... generate samples ...
batch_vllm_engine_call(self.vllm_engines, "sleep")    # CPU ← GPU
```

**When used**:
- Sample generation during PPO rollout
- Initial weight sync from actor to vLLM
- Evaluation phase

### Memory Management

**Control GPU memory**:
```bash
--vllm_gpu_memory_utilization 0.5  # Use 50% of GPU for vLLM
```

**Example**:
- A100 80GB × 0.5 = 40GB for vLLM
- Remaining 40GB for other models when colocated

## DeepSpeed Sleep Mode

### How It Works

**Enable DeepSpeed sleep**:
```bash
--deepspeed_enable_sleep
```

**Sleep/wake cycle**:
1. **Reload states** before training: Move model CPU → GPU
2. **Train** model: DeepSpeed performs optimization
3. **Offload states** after training: Move model GPU → CPU

**Implementation**:
```python
# In PPOTrainer.ppo_train()
# For actor model
self.actor.reload_states()      # GPU ← CPU
# ... training loop ...
self.actor.offload_states()     # CPU ← GPU

# For critic model
self.critic.reload_states()     # GPU ← CPU
# ... training loop ...
self.critic.offload_states()    # CPU ← GPU
```

**Synchronization**:
- Ray barriers ensure models don't reload simultaneously
- Prevents OOM from concurrent GPU memory usage

### Initial Offload

**Actor offload** (after initialization):
```python
if args.deepspeed_enable_sleep:
    self.actor.offload_states()  # Start in CPU
```

## OOM Prevention Strategies

### 1. Memory Utilization Control

**Limit vLLM memory**:
```bash
--vllm_gpu_memory_utilization 0.5  # Conservative
--vllm_gpu_memory_utilization 0.7  # Aggressive
```

### 2. Ray Barriers for Synchronization

**Prevent simultaneous loading**:
- vLLM wakes → generates → sleeps
- Then DeepSpeed reloads → trains → offloads
- Never both in GPU memory simultaneously

### 3. Disable Colocation for Large Models

**If OOM occurs**:
```bash
# Remove --colocate_all_models
# Allocate separate GPUs for each model
--actor_num_nodes 1 --actor_num_gpus_per_node 16
--critic_num_nodes 1 --critic_num_gpus_per_node 16
--reward_num_nodes 1 --reward_num_gpus_per_node 16
--ref_num_nodes 1 --ref_num_gpus_per_node 16
```

### 4. ZeRO-3 Sharding

**Memory efficiency**:
```bash
--zero_stage 3  # Shard parameters, gradients, optimizer states
```

Combined with Hybrid Engine for maximum efficiency.

## Complete Example (70B Model)

### With Hybrid Engine (48 GPUs)

```bash
ray job submit --address="http://127.0.0.1:8265" \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --colocate_all_models \
  --vllm_enable_sleep \
  --deepspeed_enable_sleep \
  --vllm_num_engines 4 \
  --vllm_tensor_parallel_size 4 \
  --vllm_gpu_memory_utilization 0.5 \
  --actor_num_nodes 1 --actor_num_gpus_per_node 16 \
  --critic_num_nodes 1 --critic_num_gpus_per_node 16 \
  --reward_num_nodes 1 --reward_num_gpus_per_node 8 \
  --ref_num_nodes 1 --ref_num_gpus_per_node 8 \
  --pretrain meta-llama/Llama-2-70b-hf \
  --reward_pretrain ./reward-model-70b \
  --zero_stage 3 --bf16
```

**GPU allocation**:
- vLLM: 4 engines × 4 GPUs = 16 GPUs
- Actor: 16 GPUs (shares with vLLM via sleep)
- Critic: 16 GPUs
- Reward: 8 GPUs
- Reference: 8 GPUs
- **Total**: 48 GPUs (16 shared efficiently)

### Without Hybrid Engine (64 GPUs)

```bash
ray job submit --address="http://127.0.0.1:8265" \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --vllm_num_engines 4 \
  --vllm_tensor_parallel_size 4 \
  --actor_num_nodes 1 --actor_num_gpus_per_node 16 \
  --critic_num_nodes 1 --critic_num_gpus_per_node 16 \
  --reward_num_nodes 1 --reward_num_gpus_per_node 16 \
  --ref_num_nodes 1 --ref_num_gpus_per_node 16 \
  --pretrain meta-llama/Llama-2-70b-hf \
  --zero_stage 3 --bf16
```

**GPU allocation**:
- vLLM: 16 GPUs (dedicated)
- Actor: 16 GPUs (dedicated)
- Critic: 16 GPUs (dedicated)
- Reward: 16 GPUs (dedicated)
- **Total**: 64 GPUs (no sharing)

**Savings**: Hybrid Engine saves 25% GPUs (48 vs 64)

## Ray Placement Groups

### Automatic Creation

**When `--colocate_all_models` is enabled**:
```python
# Placement group created for GPU sharing
placement_group = {
    "bundle": [{"GPU": actor_num_gpus_per_node}],  # Shared GPUs
    "strategy": "PACK"  # Colocate on same nodes
}
```

**Resource constraints**:
- vLLM engines scheduled on actor node GPUs
- DeepSpeed models scheduled on same GPUs
- Ray ensures proper scheduling

## Performance Benefits

**GPU utilization**:
- **Without Hybrid**: ~60-70% (idle during generation or training)
- **With Hybrid**: ~90-95% (constant utilization)

**Cost savings**:
- 25-33% fewer GPUs needed
- Same throughput with Hybrid Engine

**Stability**:
- More stable than async training
- Ray barriers prevent race conditions

## Troubleshooting

### OOM During Sleep/Wake

**Symptom**: OOM when model wakes up

**Solution 1** - Lower vLLM memory:
```bash
--vllm_gpu_memory_utilization 0.4  # Reduce from 0.5
```

**Solution 2** - Disable colocation:
```bash
# Remove --colocate_all_models
```

### DeepSpeed GPU Index Error

**Symptom**: `RuntimeError: Index out of range`

**Solution**:
```bash
export RAY_EXPERIMENTAL_NOSET_CUDA_VISIBLE_DEVICES=1
```

### vLLM Engines Don't Share GPUs

**Symptom**: vLLM uses separate GPUs despite `--colocate_all_models`

**Check constraint**:
```bash
# This must be true:
actor_num_nodes * actor_num_gpus_per_node == vllm_num_engines * vllm_tensor_parallel_size

# Example (valid):
# Actor: 1 node × 16 GPUs = 16
# vLLM: 4 engines × 4 TP = 16
# ✓ Equal
```

## References

- OpenRLHF: https://github.com/OpenRLHF/OpenRLHF
- Ray: https://docs.ray.io/en/latest/ray-core/scheduling/placement-group.html
- vLLM: https://docs.vllm.ai/
- DeepSpeed ZeRO: https://www.deepspeed.ai/tutorials/zero/




### Custom Rewards

# Custom Reward Functions

Complete guide to implementing custom reward functions and agent RLHF in OpenRLHF.

## Overview

OpenRLHF supports two paradigms for custom rewards:
1. **Reinforced Fine-Tuning (RFT)** - Custom reward function for single-step generation
2. **Agent RLHF** - Multi-step environment interaction with feedback loops

## Reinforced Fine-Tuning (RFT)

### Basic Concept

Instead of using a pre-trained reward model, define your own reward logic to evaluate model outputs.

**Enable RFT**:
```bash
--remote_rm_url ./reward_func.py  # Path to custom reward function
--label_key answers                # Pass additional info (e.g., ground truth)
```

### Reward Function API

**Template** (`reward_func.py`):
```python
import torch

def reward_func(queries, prompts, labels):
    """
    Args:
        queries: List[str] - Full prompts + generated responses
        prompts: List[str] - Original prompts only
        labels: List[str] - Ground truth answers (from --label_key)

    Returns:
        dict with:
            "rewards": torch.Tensor - Rewards for advantage calculation
            "scores": torch.Tensor - Scores (0-1) for dynamic filtering
            "extra_logs": dict - Additional metrics for W&B logging
    """
    # Your reward calculation logic here
    rewards = torch.tensor([...])

    return {
        "rewards": rewards,
        "scores": rewards,
        "extra_logs": {"custom_metric": rewards}
    }
```

### Example 1: Code Generation Rewards

**Evaluate code correctness via execution**:
```python
# reward_func_code_gen.py
import torch
import subprocess
import tempfile
import os

def reward_func(queries, prompts, labels):
    """Reward based on code execution and test passing."""
    rewards = []

    for query, prompt, label in zip(queries, prompts, labels):
        # Extract generated code (after prompt)
        generated_code = query.split(prompt)[-1].strip()

        try:
            # Write code to temporary file
            with tempfile.NamedTemporaryFile(mode='w', suffix='.py', delete=False) as f:
                f.write(generated_code)
                temp_file = f.name

            # Execute code and run tests
            result = subprocess.run(
                ["python", "-m", "pytest", temp_file],
                capture_output=True,
                text=True,
                timeout=5
            )

            # Reward based on test results
            if "passed" in result.stdout:
                rewards.append(1.0)  # All tests passed
            elif "failed" in result.stdout:
                rewards.append(0.3)  # Some tests failed
            else:
                rewards.append(0.0)  # No tests passed

        except subprocess.TimeoutExpired:
            rewards.append(-0.5)  # Code execution timeout
        except Exception as e:
            rewards.append(-1.0)  # Syntax error or crash
        finally:
            if os.path.exists(temp_file):
                os.remove(temp_file)

    rewards_tensor = torch.tensor(rewards).float()
    return {
        "rewards": rewards_tensor,
        "scores": (rewards_tensor + 1.0) / 2.0,  # Normalize to [0, 1]
        "extra_logs": {
            "code_correctness": rewards_tensor,
            "avg_correctness": rewards_tensor.mean()
        }
    }
```

**Training command**:
```bash
ray job submit --address="http://127.0.0.1:8265" \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --remote_rm_url ./reward_func_code_gen.py \
  --label_key test_cases \
  --pretrain codellama/CodeLlama-7b-Instruct-hf \
  --prompt_data code-generation-dataset \
  --advantage_estimator reinforce \
  # ... other args
```

### Example 2: Math Reasoning Rewards

**Check final answer correctness**:
```python
# reward_func_math.py
import torch
import re

def reward_func(queries, prompts, labels):
    """Reward based on mathematical correctness."""
    rewards = []

    for query, prompt, label in zip(queries, prompts, labels):
        generated_answer = query.split(prompt)[-1].strip()
        expected_answer = label  # Ground truth answer

        # Extract numerical answer from various formats
        # Format 1: "The answer is: 42"
        match1 = re.search(r"(?:answer is:?|=)\s*(-?\d+\.?\d*)", generated_answer, re.IGNORECASE)
        # Format 2: "#### 42" (GSM8K format)
        match2 = re.search(r"####\s*(-?\d+\.?\d*)", generated_answer)

        extracted_answer = None
        if match1:
            extracted_answer = match1.group(1)
        elif match2:
            extracted_answer = match2.group(1)

        # Calculate reward
        if extracted_answer is None:
            rewards.append(-0.5)  # No answer found
        else:
            try:
                if abs(float(extracted_answer) - float(expected_answer)) < 1e-6:
                    rewards.append(1.0)  # Correct answer
                else:
                    rewards.append(0.0)  # Incorrect answer
            except ValueError:
                rewards.append(-0.5)  # Malformed answer

    rewards_tensor = torch.tensor(rewards).float()
    return {
        "rewards": rewards_tensor,
        "scores": (rewards_tensor + 0.5) / 1.5,  # Normalize to [0, 1]
        "extra_logs": {
            "math_accuracy": (rewards_tensor == 1.0).float().mean(),
            "answer_formatted": (rewards_tensor >= 0.0).float().mean()
        }
    }
```

**Training command**:
```bash
ray job submit --address="http://127.0.0.1:8265" \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --remote_rm_url ./reward_func_math.py \
  --label_key answers \
  --pretrain deepseek-ai/deepseek-math-7b-base \
  --prompt_data gsm8k \
  --advantage_estimator reinforce_baseline \
  --n_samples_per_prompt 4 \
  # ... other args
```

### Example 3: Conversation Quality Rewards

**Use sentiment/quality model**:
```python
# reward_func_conversation.py
import torch
from transformers import pipeline

# Load quality evaluation model (once, outside reward_func if possible)
quality_scorer = pipeline("text-classification", model="OpenAssistant/reward-model-deberta-v3-large")

def reward_func(queries, prompts, labels):
    """Reward based on conversation quality (helpfulness, safety)."""
    rewards = []

    for query, prompt, label in zip(queries, prompts, labels):
        conversation = query  # Full conversation up to this point

        # Score conversation quality using reward model
        result = quality_scorer(conversation)[0]
        score = result['score'] if result['label'] == 'LABEL_1' else 1 - result['score']

        # Optional: Additional heuristics
        # - Check for harmful content
        # - Verify answer relevance
        # - Measure coherence

        # Penalize very short responses
        response = query.split(prompt)[-1].strip()
        if len(response.split()) < 10:
            score *= 0.5

        rewards.append(score)

    rewards_tensor = torch.tensor(rewards).float()
    return {
        "rewards": rewards_tensor,
        "scores": rewards_tensor,  # Already in [0, 1]
        "extra_logs": {
            "avg_quality": rewards_tensor.mean(),
            "min_quality": rewards_tensor.min(),
            "max_quality": rewards_tensor.max()
        }
    }
```

**Training command**:
```bash
ray job submit --address="http://127.0.0.1:8265" \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --remote_rm_url ./reward_func_conversation.py \
  --pretrain meta-llama/Llama-3-8b-Instruct \
  --prompt_data OpenAssistant/oasst1 \
  --advantage_estimator gae \
  # ... other args
```

### Dynamic Filtering

**Use `scores` for sample filtering**:
```python
def reward_func(queries, prompts, labels):
    rewards = calculate_rewards(...)  # Your reward logic

    # Filter: Keep only samples with score > 0.5
    scores = (rewards > 0.0).float()

    return {
        "rewards": rewards,      # For advantage calculation
        "scores": scores,        # For dynamic filtering (0 or 1)
        "extra_logs": {"filtered_ratio": scores.mean()}
    }
```

## Agent RLHF (Multi-Step)

### Basic Concept

Train language models as agents that interact with environments over multiple steps, receiving feedback after each action.

**Enable Agent RLHF**:
```bash
--async_train                      # Enable async mode
--agent_func_path ./agent_func.py  # Path to agent definition
```

### Agent API

**Template** (`agent_func.py`):
```python
from openrlhf.utils.agent import AgentExecutorBase, AgentInstanceBase
import torch
from typing import Dict, Any

class AgentInstance(AgentInstanceBase):
    """Manages state for a single agent episode."""

    async def __init__(self, *args, **kwargs):
        self.step_idx = 0
        self.max_steps = 5  # Maximum environment steps

    async def reset(self, states: dict, **kwargs):
        """Reset environment for new episode."""
        return {"observation": states["observation"]}

    async def step(self, states: dict, **kwargs) -> Dict[str, Any]:
        """Execute one environment step."""
        observation_text = states["observation_text"]
        action_text = states["action_text"]
        label = states["label"]

        # Your environment logic here
        done = self.step_idx >= self.max_steps
        reward = calculate_reward(action_text, label) if done else 0.0

        # Environment feedback for next step
        if done:
            environment_feedback = "\n\n[EPISODE COMPLETE]\n</s>"
        else:
            environment_feedback = "\n\nNext step:\n</s>\n\nAssistant: "

        self.step_idx += 1

        return {
            "rewards": torch.tensor([reward]),
            "scores": torch.tensor([reward]),
            "environment_feedback": environment_feedback,
            "done": done,
            "sampling_params": states.get("sampling_params", None),
            "extra_logs": {"step": self.step_idx}
        }

class AgentExecutor(AgentExecutorBase):
    """Orchestrates agent execution."""

    def __init__(self, max_steps, max_length, llm_engine, hf_tokenizer, result_queue):
        super().__init__(AgentInstance, max_steps, max_length, llm_engine, hf_tokenizer, result_queue)

    async def execute(self, prompt, label, sampling_params):
        # Override for custom execution logic
        return await super().execute(prompt, label, sampling_params)
```

### Example: Math Problem Solving Agent

**Multi-step reasoning with verification**:
```python
# agent_func_math.py
from openrlhf.utils.agent import AgentExecutorBase, AgentInstanceBase
import torch
import re

class AgentInstance(AgentInstanceBase):
    async def __init__(self, *args, **kwargs):
        self.step_idx = 0
        self.max_steps = 3  # Allow 3 attempts
        self.steps_taken = []

    async def reset(self, states: dict, **kwargs):
        self.step_idx = 0
        self.steps_taken = []
        return {"observation": states["observation"]}

    async def step(self, states: dict, **kwargs):
        observation_text = states["observation_text"]
        action_text = states["action_text"]
        label = states["label"]  # Correct answer

        self.steps_taken.append(action_text)

        # Extract answer from current step
        match = re.search(r"(?:answer is:?|=)\s*(-?\d+\.?\d*)", action_text, re.IGNORECASE)

        if match:
            try:
                answer = float(match.group(1))
                correct = abs(answer - float(label)) < 1e-6

                if correct:
                    # Correct answer - episode done
                    done = True
                    reward = 1.0
                    feedback = "\n\n[CORRECT! Episode complete]\n</s>"
                else:
                    # Incorrect but attempt made
                    done = self.step_idx >= self.max_steps - 1
                    reward = 0.0 if not done else -0.3  # Penalty if max steps reached
                    feedback = "\n\n[INCORRECT] Try again. Think step-by-step:\n</s>\n\nAssistant: "
            except ValueError:
                # Malformed answer
                done = self.step_idx >= self.max_steps - 1
                reward = -0.5 if done else 0.0
                feedback = "\n\n[INVALID FORMAT] Provide numerical answer:\n</s>\n\nAssistant: "
        else:
            # No answer found
            done = self.step_idx >= self.max_steps - 1
            reward = -0.5 if done else 0.0
            feedback = "\n\n[NO ANSWER FOUND] Please state the final answer:\n</s>\n\nAssistant: "

        self.step_idx += 1

        return {
            "rewards": torch.tensor([reward]),
            "scores": torch.tensor([max(0.0, reward + 0.5)]),  # Normalize to [0, 1]
            "environment_feedback": feedback,
            "done": done,
            "sampling_params": states.get("sampling_params", None),
            "extra_logs": {
                "step": self.step_idx,
                "correct": reward == 1.0,
                "attempts": len(self.steps_taken)
            }
        }

class AgentExecutor(AgentExecutorBase):
    def __init__(self, max_steps, max_length, llm_engine, hf_tokenizer, result_queue):
        super().__init__(AgentInstance, max_steps, max_length, llm_engine, hf_tokenizer, result_queue)
```

**Training command**:
```bash
ray job submit --address="http://127.0.0.1:8265" \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --async_train \
  --agent_func_path ./agent_func_math.py \
  --label_key answers \
  --pretrain deepseek-ai/deepseek-math-7b-base \
  --prompt_data gsm8k \
  --advantage_estimator reinforce \
  --max_steps 3 \
  # ... other args
```

### Token-in-Token-out Principle

**Important**: Agent RLHF uses token-level processing to ensure consistency between sampling and training.

**Why**: Text-level processing can cause mismatches between generated tokens and training samples.

**Implementation**:
- `environment_feedback` is tokenized and concatenated
- Maintains alignment throughout multi-step episode
- Prevents token/text inconsistencies

## Best Practices

### Reward Function Design

**1. Normalize rewards**:
```python
# Keep rewards in reasonable range [-1, 1] or [0, 1]
rewards = (raw_rewards - raw_rewards.mean()) / (raw_rewards.std() + 1e-9)
```

**2. Handle errors gracefully**:
```python
try:
    reward = calculate_reward(output)
except Exception as e:
    reward = 0.0  # Neutral reward for errors
    print(f"Error in reward calculation: {e}")
```

**3. Log extensively**:
```python
return {
    "rewards": rewards,
    "scores": scores,
    "extra_logs": {
        "avg_reward": rewards.mean(),
        "max_reward": rewards.max(),
        "error_rate": error_count / len(queries),
        "custom_metric": ...
    }
}
```

### Agent Design

**1. Limit max steps**:
```python
self.max_steps = 5  # Prevent infinite loops
```

**2. Provide informative feedback**:
```python
if error:
    feedback = f"\n\n[ERROR: {error_msg}] Try again:\n</s>\n\nAssistant: "
else:
    feedback = "\n\nContinue:\n</s>\n\nAssistant: "
```

**3. Sparse rewards**:
```python
# Only reward at episode end
reward = final_score if done else 0.0
```

## Debugging

### Print Queries

```python
def reward_func(queries, prompts, labels):
    print(f"Query sample: {queries[0][:200]}")  # First 200 chars
    print(f"Prompt sample: {prompts[0]}")
    print(f"Label sample: {labels[0]}")
    # ... reward logic
```

### Test Locally

```python
# test_reward.py
from reward_func import reward_func
import torch

queries = ["Question: 2+2?\nAnswer: 4"]
prompts = ["Question: 2+2?\n"]
labels = ["4"]

result = reward_func(queries, prompts, labels)
print(result)
```

```bash
python test_reward.py
```

### Monitor W&B

Enable detailed logging:
```bash
--use_wandb {token}
--wandb_project custom-rewards-debug
```

Check `extra_logs` in W&B dashboard.

## References

- OpenRLHF: https://github.com/OpenRLHF/OpenRLHF
- Agent API: `openrlhf/utils/agent.py`
- Remote RM: `openrlhf/utils/remote_rm_utils.py`




### Algorithm Comparison

# Algorithm Comparison

Complete guide to RL algorithms in OpenRLHF: PPO, REINFORCE++, GRPO, RLOO, and their variants.

## Overview

OpenRLHF supports 6 RL algorithms selectable via `--advantage_estimator`:
- **gae** - PPO with Generalized Advantage Estimation
- **reinforce** - REINFORCE++ (PPO optimizations without critic)
- **reinforce_baseline** - REINFORCE++ with baseline
- **group_norm** - GRPO (Group Normalized Policy Optimization)
- **dr_grpo** - Dr. GRPO (GRPO without std normalization)
- **rloo** - Reinforcement Learning with Online Off-policy Correction

## Algorithm Details

### PPO (Proximal Policy Optimization)

**Formula**:
```
loss = -min(ratio * advantages, clip(ratio, 1-ε, 1+ε) * advantages)
ratio = π_new(a|s) / π_old(a|s)
```

**Characteristics**:
- **Stability**: High (clipped objective prevents large updates)
- **Memory**: High (stores actor + critic experiences)
- **Speed**: Medium (critic training overhead)
- **Requires**: Critic network for value estimation

**Implementation**:
```python
surr1 = ratio * advantages
surr2 = ratio.clamp(1 - clip_eps_low, 1 + clip_eps_high) * advantages
loss = -torch.min(surr1, surr2)
```

**When to use**:
- General-purpose RLHF
- Complex reward functions
- Need stable training

**Hyperparameters**:
```bash
--advantage_estimator gae  # Enable PPO
--clip_eps_low 0.2         # Clipping lower bound
--clip_eps_high 0.2        # Clipping upper bound
--actor_learning_rate 1e-6
--critic_learning_rate 9e-6
--init_kl_coef 0.01
```

### REINFORCE++

**Formula**:
```
loss = -ratio * advantages  (with PPO-clip)
advantages = cumulative_returns - baseline
```

**Characteristics**:
- **Stability**: Higher than GRPO
- **Memory**: Lower (no critic network)
- **Speed**: Faster than PPO
- **Requires**: No critic network

**Key innovation**: Integrates PPO optimizations (advantage normalization, PPO-clip loss) into REINFORCE while eliminating critic network overhead.

**When to use**:
- Want PPO stability without critic
- Limited memory budget
- Fast training priority

**Hyperparameters**:
```bash
--advantage_estimator reinforce
--critic_pretrain None  # No critic needed
--init_kl_coef 0.01
--actor_learning_rate 1e-6
```

### REINFORCE++-baseline

**Formula**:
```
rewards = rewards - mean(rewards_same_prompt)
```

**Characteristics**:
- **Stability**: Very high
- **Memory**: Lower (no critic)
- **Speed**: Faster than PPO
- **Requires**: Multiple samples per prompt

**Key innovation**: Uses mean reward of multiple samples from same prompt as baseline to reshape rewards.

**When to use**:
- RLVR (Reinforcement Learning via Verifier Rewards) settings
- Reward patterns vary (0/1/-0.5)
- Multiple samples per prompt available

**Hyperparameters**:
```bash
--advantage_estimator reinforce_baseline
--n_samples_per_prompt 4  # Must be > 1
--init_kl_coef 0.01
```

### GRPO (Group Normalized Policy Optimization)

**Formula**:
```
rewards = (rewards - mean(rewards)) / (std(rewards) + 1e-9)
loss = -ratio * normalized_advantages
KL loss (optional): k1, k2, or k3 estimator
```

**Characteristics**:
- **Stability**: Lower than REINFORCE++
- **Memory**: Lower (no critic)
- **Speed**: Fast
- **Requires**: Group reward normalization

**Key innovation**: Group-based advantage normalization with optional KL loss.

**When to use**:
- Exploring policy optimization variants
- Need reward normalization
- Memory-constrained

**Hyperparameters**:
```bash
--advantage_estimator group_norm
--use_kl_loss                # Enable KL loss
--kl_estimator k3            # k3 for loss, k2 ≈ k1
--init_kl_coef 0.01
--no_advantage_std_norm      # Optional: disable std norm
```

**KL estimator variance**:
- **k3**: Larger variance under categorical distribution
- **k1, k2**: Similar variance, k2 ≈ k1 for loss

### Dr. GRPO

**Formula**:
```
rewards = rewards - mean(rewards)  # No std normalization
```

**Characteristics**:
- **Stability**: Similar to GRPO
- **Memory**: Lower (no critic)
- **Speed**: Fast
- **Requires**: Group mean normalization only

**Key innovation**: Removes local group normalization `/std` from GRPO (not needed in RL variance reduction theory).

**When to use**:
- GRPO variant experimentation
- Avoid std normalization issues

**Hyperparameters**:
```bash
--advantage_estimator dr_grpo
--init_kl_coef 0.01
```

### RLOO (RL with Online Off-policy Correction)

**Formula**:
```
baseline = (sum(rewards) - rewards) / (n_samples - 1)
rewards = rewards - baseline
loss = -ratio * advantages  (with PPO-clip)
```

**Characteristics**:
- **Stability**: High (PPO-clip)
- **Memory**: Lower (no critic)
- **Speed**: Fast
- **Requires**: Multiple samples per prompt, per-token KL

**Key innovation**: Incorporates per-token KL reward and PPO-clip loss.

**When to use**:
- Need per-token KL rewards
- Want PPO stability without critic
- Multiple samples per prompt

**Hyperparameters**:
```bash
--advantage_estimator rloo
--n_samples_per_prompt 4  # Must be > 1
--init_kl_coef 0.01
```

## Comparison Table

| Algorithm | Critic | Stability | Memory | Speed | Best For |
|-----------|--------|-----------|--------|-------|----------|
| PPO | ✅ Yes | ⭐⭐⭐⭐⭐ | High | Medium | General purpose |
| REINFORCE++ | ❌ No | ⭐⭐⭐⭐ | Low | **Fast** | Critic-free PPO |
| REINFORCE++-baseline | ❌ No | ⭐⭐⭐⭐⭐ | Low | **Fast** | RLVR settings |
| GRPO | ❌ No | ⭐⭐⭐ | Low | Fast | Reward normalization |
| Dr. GRPO | ❌ No | ⭐⭐⭐ | Low | Fast | GRPO variant |
| RLOO | ❌ No | ⭐⭐⭐⭐ | Low | Fast | Per-token KL |

## Experience Data Structure

**PPO (with critic)**:
```python
@dataclass
class Experience:
    sequences: torch.Tensor       # Token sequences
    attention_mask: torch.Tensor  # Attention masks
    action_mask: torch.Tensor     # Action masks
    action_log_probs: torch.Tensor # Log π(a|s)
    values: torch.Tensor          # Critic value estimates
    returns: torch.Tensor         # Cumulative returns
    advantages: torch.Tensor      # GAE advantages
    reward: float                 # Total reward
    kl: torch.Tensor             # KL divergence
```

**REINFORCE++ (no critic)**:
```python
# No values, returns, or advantages stored
# Only sequences, log_probs, and rewards
```

## Memory Comparison (7B Model)

| Algorithm | Components | Memory (8× A100) |
|-----------|-----------|------------------|
| PPO | Actor + Critic + Reward + Ref | ~40GB |
| REINFORCE++ | Actor + Reward + Ref | ~28GB |
| GRPO | Actor + Reward + Ref | ~28GB |
| RLOO | Actor + Reward + Ref | ~28GB |

**Savings**: ~30% memory reduction without critic

## Speed Comparison

**Relative training time** (7B model, 1000 steps):
- PPO: 1.0× baseline
- REINFORCE++: **0.75×** (25% faster)
- GRPO: 0.80×
- RLOO: 0.80×

**Why REINFORCE++ is faster**:
- No critic training
- No value function updates
- Fewer backward passes

## Choosing an Algorithm

### Decision Tree

```
Need maximum stability?
  ├─ Yes → PPO (with critic)
  └─ No ↓

Have multiple samples per prompt?
  ├─ Yes ↓
  │   └─ RLVR setting with varying rewards?
  │       ├─ Yes → REINFORCE++-baseline
  │       └─ No → RLOO (if need per-token KL)
  └─ No ↓

Want faster than PPO?
  └─ Yes → REINFORCE++ (most stable critic-free)

Experimenting with normalization?
  └─ Yes → GRPO or Dr. GRPO
```

### By Use Case

**Production deployment**:
```bash
# Maximum stability
--advantage_estimator gae  # PPO
--clip_eps_low 0.2
--init_kl_coef 0.01
```

**Memory-constrained**:
```bash
# No critic, stable
--advantage_estimator reinforce  # REINFORCE++
--critic_pretrain None
```

**RLVR / Verification rewards**:
```bash
# Baseline reward shaping
--advantage_estimator reinforce_baseline
--n_samples_per_prompt 4
```

**Research / Experimentation**:
```bash
# Explore GRPO variants
--advantage_estimator group_norm
--use_kl_loss --kl_estimator k3
```

## Advanced Configuration

### Reward Normalization

**PPO (no manual normalization)**:
```bash
--advantage_estimator gae
# GAE handles advantage normalization
```

**GRPO (group normalization)**:
```bash
--advantage_estimator group_norm
--normalize_reward  # Optional additional normalization
```

**Disable std normalization**:
```bash
--no_advantage_std_norm  # Keep mean norm only
```

### KL Penalty Configuration

**All algorithms support**:
```bash
--init_kl_coef 0.01    # Initial KL coefficient
--kl_target 0.1        # Target KL divergence
--kl_horizon 10000     # Steps to reach target
```

**GRPO-specific**:
```bash
--use_kl_loss          # Enable KL loss term
--kl_estimator k3      # Loss function choice
```

### Clipping Configuration

**PPO clipping**:
```bash
--clip_eps_low 0.2     # Lower bound
--clip_eps_high 0.2    # Upper bound
```

**Reward clipping**:
```bash
--reward_clip_range 10.0  # Clip rewards to [-10, 10]
```

## Common Issues

### PPO Instability

**Symptom**: Large policy updates, divergence

**Solution**: Reduce clipping range
```bash
--clip_eps_low 0.1     # Reduce from 0.2
--clip_eps_high 0.1
```

### GRPO High Variance

**Symptom**: Unstable training with GRPO

**Solution**: Switch to REINFORCE++
```bash
--advantage_estimator reinforce  # More stable
```

### Memory OOM with PPO

**Symptom**: OOM during critic training

**Solution**: Switch to critic-free
```bash
--advantage_estimator reinforce  # No critic
--critic_pretrain None
```

### RLOO/Baseline Requires Multiple Samples

**Symptom**: `AssertionError: n_samples_per_prompt must be > 1`

**Solution**:
```bash
--n_samples_per_prompt 4  # Minimum 2, recommended 4-8
```

## References

- PPO paper: https://arxiv.org/abs/1707.06347
- GRPO paper: https://arxiv.org/abs/2402.03300
- OpenRLHF: https://github.com/OpenRLHF/OpenRLHF
- OpenRLHF paper: https://arxiv.org/abs/2405.11143




### Multi Node Training

# Multi-Node Training

Complete guide to distributed Ray cluster training with OpenRLHF across multiple machines.

## Overview

OpenRLHF uses Ray for distributed scheduling, allowing Actor, Critic, Reward, and Reference models to span multiple nodes. Supports fault tolerance through checkpointing and automatic task rescheduling.

## Ray Cluster Setup

### 1. Start Head Node (Master Machine)

**In Docker container**:
```bash
# Launch container on master node
docker run --runtime=nvidia -it --rm --shm-size="10g" \
  --cap-add=SYS_ADMIN -v $PWD:/openrlhf \
  nvcr.io/nvidia/pytorch:25.02-py3 bash

# Start Ray head node
ray start --head --node-ip-address 0.0.0.0 --num-gpus 8
```

**Output**:
```
Ray runtime started.
Dashboard: http://0.0.0.0:8265
```

### 2. Connect Worker Nodes

**On each worker machine**:
```bash
# Launch container
docker run --runtime=nvidia -it --rm --shm-size="10g" \
  --cap-add=SYS_ADMIN -v $PWD:/openrlhf \
  nvcr.io/nvidia/pytorch:25.02-py3 bash

# Connect to head node
ray start --address {MASTER-NODE-IP}:6379 --num-gpus 8
```

**Replace `{MASTER-NODE-IP}`** with head node's IP address.

### 3. Verify Cluster

```bash
# On head node
ray status
```

**Output**:
```
Nodes: 4
  - 1 head node (8 GPUs)
  - 3 worker nodes (8 GPUs each)
Total GPUs: 32
```

## Distributed Training Configuration

### Multi-Node PPO Training

**4-node cluster (32 GPUs)** - 70B model:
```bash
ray job submit --address="http://127.0.0.1:8265" \
  --runtime-env-json='{"working_dir": "/openrlhf"}' \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --ref_num_nodes 1 --ref_num_gpus_per_node 8 \
  --reward_num_nodes 1 --reward_num_gpus_per_node 8 \
  --critic_num_nodes 1 --critic_num_gpus_per_node 8 \
  --actor_num_nodes 1 --actor_num_gpus_per_node 8 \
  --vllm_num_engines 2 --vllm_tensor_parallel_size 4 \
  --pretrain meta-llama/Llama-2-70b-hf \
  --reward_pretrain ./reward-model-70b \
  --save_path ./output/llama-70b-ppo \
  --ckpt_path ./checkpoints/llama-70b-ppo \
  --save_steps 100 --logging_steps 1 \
  --micro_train_batch_size 2 --train_batch_size 128 \
  --micro_rollout_batch_size 4 --rollout_batch_size 1024 \
  --max_epochs 1 --prompt_max_len 1024 --generate_max_len 1024 \
  --zero_stage 3 --bf16 \
  --actor_learning_rate 5e-7 --critic_learning_rate 9e-6 \
  --init_kl_coef 0.01 --normalize_reward \
  --gradient_checkpointing --flash_attn
```

**GPU allocation**:
- **Node 1**: Reference model (8 GPUs)
- **Node 2**: Reward model (8 GPUs)
- **Node 3**: Critic model (8 GPUs)
- **Node 4**: Actor model (8 GPUs)

### Model Distribution Arguments

**Per-model configuration**:
```bash
# Actor model
--actor_num_nodes 2           # 2 nodes for actor
--actor_num_gpus_per_node 8   # 8 GPUs per node = 16 GPUs total

# Critic model
--critic_num_nodes 1
--critic_num_gpus_per_node 8

# Reward model
--reward_num_nodes 1
--reward_num_gpus_per_node 8

# Reference model
--ref_num_nodes 1
--ref_num_gpus_per_node 8
```

### Hybrid Engine (Colocated Models)

**Share GPUs across models**:
```bash
# Colocate all models on same GPUs
--colocate_all_models

# Or colocate specific pairs
--colocate_actor_ref       # Actor + Reference
--colocate_critic_reward   # Critic + Reward
```

**Example (2-node, 16 GPUs)**:
```bash
ray job submit --address="http://127.0.0.1:8265" \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --colocate_all_models \
  --vllm_enable_sleep --deepspeed_enable_sleep \
  --actor_num_nodes 2 --actor_num_gpus_per_node 8 \
  --critic_num_nodes 0 --critic_num_gpus_per_node 0 \
  --reward_num_nodes 0 --reward_num_gpus_per_node 0 \
  --ref_num_nodes 0 --ref_num_gpus_per_node 0 \
  --vllm_num_engines 4 --vllm_tensor_parallel_size 4 \
  # ... other args
```

**Result**: All models share 16 GPUs via sleep/wake cycles.

## vLLM Configuration

### Tensor Parallelism

**Multi-GPU per engine**:
```bash
--vllm_num_engines 4           # 4 engines
--vllm_tensor_parallel_size 4  # 4 GPUs each = 16 GPUs total
```

### GPU Memory Management

```bash
--vllm_gpu_memory_utilization 0.5  # Use 50% GPU for vLLM
```

**Calculation**:
- A100 80GB × 0.5 = 40GB for vLLM
- Remaining 40GB for other models (if colocated)

## Checkpointing

### Enable Checkpointing

**Basic checkpointing**:
```bash
--save_path ./output/model           # Final save path
--ckpt_path ./checkpoints/model      # Checkpoint directory
--save_steps 100                     # Save every 100 steps
--save_value_network                 # Also save critic
```

**HuggingFace format**:
```bash
--save_hf_ckpt  # Save as HuggingFace model (easier loading)
```

**DeepSpeed universal checkpoint**:
```bash
--use_ds_universal_ckpt  # Compatible across ZeRO stages
```

### Checkpoint Content

**Saved state**:
```python
{
    "global_step": 1000,
    "episode": 10,
    "data_loader_state_dict": {...},
    "actor_model": {...},        # DeepSpeed checkpoint
    "critic_model": {...}        # If --save_value_network
}
```

**Files created**:
```
checkpoints/llama-70b-ppo/
├── global_step_1000/
│   ├── actor/
│   │   ├── mp_rank_00_model_states.pt
│   │   ├── zero_pp_rank_0_mp_rank_00optim_states.pt
│   │   └── ...
│   └── critic/ (if --save_value_network)
│       └── ...
└── hf_ckpt/ (if --save_hf_ckpt)
    ├── config.json
    ├── pytorch_model.bin
    └── ...
```

### Resume Training

**From checkpoint**:
```bash
ray job submit --address="http://127.0.0.1:8265" \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --load_checkpoint                         # Enable resume
  --ckpt_path ./checkpoints/llama-70b-ppo   # Checkpoint dir
  # ... other args (must match original)
```

**Resume logic**:
1. `PPOTrainer.fit()` checks for existing checkpoints
2. Loads latest checkpoint from `ckpt_path`
3. Restores `global_step`, `episode`, dataloader state
4. Continues training from that point

## Fault Tolerance

### Automatic Task Rescheduling

**Ray's built-in fault tolerance**:
- If worker node fails → Ray reschedules tasks on available nodes
- Requires sufficient resources on remaining nodes
- May need to reinitialize some components

### DeepSpeed Sleep Mode Protection

**Prevents OOM-related failures**:
```bash
--deepspeed_enable_sleep  # Offload to CPU when not training
```

**Sleep/wake cycle**:
1. Model offloaded to CPU after training
2. Frees GPU memory for other components
3. Reloaded from CPU before next training step
4. Synchronized via Ray barriers

**OOM prevention**:
- Models don't compete for GPU memory
- Sequential loading prevents concurrent OOM
- Barriers ensure synchronization

### Checkpoint-Based Recovery

**Recover from catastrophic failure**:
1. Training interrupted (node crash, OOM, etc.)
2. Restart Ray cluster
3. Resume with `--load_checkpoint`
4. Training continues from last saved step

**Best practice**:
```bash
--save_steps 100  # Frequent checkpointing (every 100 steps)
```

## Monitoring

### Ray Dashboard

**Access dashboard**:
```
http://{HEAD-NODE-IP}:8265
```

**Monitor**:
- Node status (active, idle, failed)
- GPU utilization per node
- Task scheduling (which models on which nodes)
- Resource usage (memory, CPU, GPU)

### Weights & Biases Integration

**Enable W&B logging**:
```bash
--use_wandb {your-wandb-token}
--wandb_org your-org
--wandb_project llama-70b-ppo
```

**Metrics logged**:
- Training loss per step
- Reward scores
- KL divergence
- GPU utilization per node

## Performance Optimization

### InfiniBand for Multi-Node

**For nodes with InfiniBand**:
```bash
# Set environment variable before starting Ray
export NCCL_IB_HCA=mlx5_0  # InfiniBand device
export NCCL_SOCKET_IFNAME=ib0
export NCCL_IB_DISABLE=0

ray start --head --node-ip-address 0.0.0.0 --num-gpus 8
```

**Performance gain**: 2-3× faster multi-node communication

### Gradient Checkpointing

**Reduce memory, enable larger models**:
```bash
--gradient_checkpointing  # Trade compute for memory
```

### Flash Attention 2

**Faster attention, lower memory**:
```bash
--flash_attn  # Requires FlashAttention installed
```

### Packing Samples

**Improve GPU utilization**:
```bash
--packing_samples  # Pack multiple samples per batch
```

## Troubleshooting

### Ray Connection Issues

**Symptom**: Worker nodes can't connect to head

**Solution**: Check firewall/network
```bash
# On head node, ensure ports open
# Default ports: 6379 (Redis), 8265 (Dashboard), 10001-10100 (workers)

# Test connection from worker
telnet {HEAD-NODE-IP} 6379
```

### Node Failures During Training

**Symptom**: Ray reports node failure

**Solution 1** - Resume from checkpoint:
```bash
# Fix failed node or remove from cluster
ray stop  # On failed node
# Then resume training with --load_checkpoint
```

**Solution 2** - Adjust resources:
```bash
# Reduce nodes if some failed
--actor_num_nodes 1  # Instead of 2
```

### OOM on Multi-Node

**Symptom**: OOM despite multi-node setup

**Solution 1** - Reduce batch sizes:
```bash
--micro_train_batch_size 1  # Reduce from 2
--micro_rollout_batch_size 2  # Reduce from 4
```

**Solution 2** - Enable sleep modes:
```bash
--vllm_enable_sleep
--deepspeed_enable_sleep
```

**Solution 3** - Increase ZeRO stage:
```bash
--zero_stage 3  # Maximum sharding
```

### Checkpoint Loading Fails

**Symptom**: `FileNotFoundError` when resuming

**Check checkpoint path**:
```bash
ls -la ./checkpoints/llama-70b-ppo/
# Verify global_step_* directories exist
```

**Solution**: Ensure `--ckpt_path` matches save location
```bash
--ckpt_path ./checkpoints/llama-70b-ppo  # Same as during save
```

## Complete Multi-Node Example

### 8-node cluster (64 GPUs) - 70B model

**Head node (Node 1)**:
```bash
ray start --head --node-ip-address 10.0.0.1 --num-gpus 8
```

**Worker nodes (Nodes 2-8)**:
```bash
ray start --address 10.0.0.1:6379 --num-gpus 8
```

**Submit job**:
```bash
ray job submit --address="http://10.0.0.1:8265" \
  --runtime-env-json='{"working_dir": "/openrlhf"}' \
  -- python3 -m openrlhf.cli.train_ppo_ray \
  --ref_num_nodes 2 --ref_num_gpus_per_node 8 \
  --reward_num_nodes 2 --reward_num_gpus_per_node 8 \
  --critic_num_nodes 2 --critic_num_gpus_per_node 8 \
  --actor_num_nodes 2 --actor_num_gpus_per_node 8 \
  --vllm_num_engines 4 --vllm_tensor_parallel_size 4 \
  --pretrain meta-llama/Llama-2-70b-hf \
  --reward_pretrain ./reward-70b \
  --save_path ./output/llama-70b-ppo \
  --ckpt_path ./checkpoints/llama-70b-ppo \
  --save_steps 100 --save_hf_ckpt \
  --micro_train_batch_size 1 --train_batch_size 128 \
  --micro_rollout_batch_size 2 --rollout_batch_size 1024 \
  --max_epochs 1 --bf16 --zero_stage 3 \
  --actor_learning_rate 5e-7 --critic_learning_rate 9e-6 \
  --gradient_checkpointing --flash_attn --packing_samples \
  --use_wandb {token} --wandb_project llama-70b-ppo
```

**GPU allocation**:
- Reference: 16 GPUs (2 nodes × 8)
- Reward: 16 GPUs (2 nodes × 8)
- Critic: 16 GPUs (2 nodes × 8)
- Actor: 16 GPUs (2 nodes × 8)
- **Total**: 64 GPUs

## References

- Ray Docs: https://docs.ray.io/
- OpenRLHF: https://github.com/OpenRLHF/OpenRLHF
- DeepSpeed ZeRO: https://www.deepspeed.ai/tutorials/zero/




---

## 🚀 Usage

**Reference this template:** `@skill-openrlhf-training.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
