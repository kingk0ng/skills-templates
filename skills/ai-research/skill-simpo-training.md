---
id: skill-simpo-training
type: skill
name: simpo-training
description: Simple Preference Optimization for LLM alignment. Reference-free alternative
  to DPO with better performance (+6.4 points on AlpacaEval 2.0). No reference model
  needed, more efficient than DPO. Use for preference alignment when want simpler,
  faster training than DPO/PPO.
category: ai-research
complexity: medium
keywords:
- git
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 739
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~739 -->
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




# simpo-training

> Simple Preference Optimization for LLM alignment. Reference-free alternative to DPO with better performance (+6.4 points on AlpacaEval 2.0). No reference model needed, more efficient than DPO. Use for preference alignment when want simpler, faster training than DPO/PPO.

# SimPO - Simple Preference Optimization

## Quick start

SimPO is a reference-free preference optimization method that outperforms DPO without needing a reference model.

**Installation**:
```bash
# Create environment
conda create -n simpo python=3.10 && conda activate simpo

# Install PyTorch 2.2.2
# Visit: https://pytorch.org/get-started/locally/

# Install alignment-handbook
git clone https://github.com/huggingface/alignment-handbook.git
cd alignment-handbook
python -m pip install .

# Install Flash Attention 2
python -m pip install flash-attn --no-build-isolation
```

**Training** (Mistral 7B):
```bash
ACCELERATE_LOG_LEVEL=info accelerate launch \
  --config_file accelerate_configs/deepspeed_zero3.yaml \
  scripts/run_simpo.py \
  training_configs/mistral-7b-base-simpo.yaml
```

## Common workflows

### Workflow 1: Train from base model (Mistral 7B)

**Config** (`mistral-7b-base-simpo.yaml`):
```yaml
# Model
model_name_or_path: mistralai/Mistral-7B-v0.1
torch_dtype: bfloat16

# Dataset
dataset_mixer:
  HuggingFaceH4/ultrafeedback_binarized: 1.0
dataset_splits:
  - train_prefs
  - test_prefs

# SimPO hyperparameters
beta: 2.0                  # Reward scaling (2.0-10.0)
gamma_beta_ratio: 0.5       # Target margin (0-1)
loss_type: sigmoid          # sigmoid or hinge
sft_weight: 0.0             # Optional SFT regularization

# Training
learning_rate: 5e-7         # Critical: 3e-7 to 1e-6
num_train_epochs: 1
per_device_train_batch_size: 1
gradient_accumulation_steps: 8

# Output
output_dir: ./outputs/mistral-7b-simpo
```

**Launch training**:
```bash
accelerate launch --config_file accelerate_configs/deepspeed_zero3.yaml \
  scripts/run_simpo.py training_configs/mistral-7b-base-simpo.yaml
```

### Workflow 2: Fine-tune instruct model (Llama 3 8B)

**Config** (`llama3-8b-instruct-simpo.yaml`):
```yaml
model_name_or_path: meta-llama/Meta-Llama-3-8B-Instruct

dataset_mixer:
  argilla/ultrafeedback-binarized-preferences-cleaned: 1.0

beta: 2.5
gamma_beta_ratio: 0.5
learning_rate: 5e-7
sft_weight: 0.1             # Add SFT loss to preserve capabilities

num_train_epochs: 1
per_device_train_batch_size: 2
gradient_accumulation_steps: 4
output_dir: ./outputs/llama3-8b-simpo
```

**Launch**:
```bash
accelerate launch --config_file accelerate_configs/deepspeed_zero3.yaml \
  scripts/run_simpo.py training_configs/llama3-8b-instruct-simpo.yaml
```

### Workflow 3: Reasoning-intensive tasks (lower LR)

**For math/code tasks**:
```yaml
model_name_or_path: deepseek-ai/deepseek-math-7b-base

dataset_mixer:
  argilla/distilabel-math-preference-dpo: 1.0

beta: 5.0                   # Higher for stronger signal
gamma_beta_ratio: 0.7       # Larger margin
learning_rate: 3e-7         # Lower LR for reasoning
sft_weight: 0.0

num_train_epochs: 1
per_device_train_batch_size: 1
gradient_accumulation_steps: 16
```

## When to use vs alternatives

**Use SimPO when**:
- Want simpler training than DPO (no reference model)
- Have preference data (chosen/rejected pairs)
- Need better performance than DPO
- Limited compute resources
- Single-node training sufficient

**Algorithm selection**:
- **SimPO**: Simplest, best performance, no reference model
- **DPO**: Need reference model baseline, more conservative
- **PPO**: Maximum control, need reward model, complex setup
- **GRPO**: Memory-efficient RL, no critic

**Use alternatives instead**:
- **OpenRLHF**: Multi-node distributed training, PPO/GRPO
- **TRL**: Need multiple methods in one framework
- **DPO**: Established baseline comparison

## Common issues

**Issue: Loss divergence**

Reduce learning rate:
```yaml
learning_rate: 3e-7  # Reduce from 5e-7
```

Reduce beta:
```yaml
beta: 1.0  # Reduce from 2.0
```

**Issue: Model forgets capabilities**

Add SFT regularization:
```yaml
sft_weight: 0.1  # Add SFT loss component
```

**Issue: Poor preference separation**

Increase beta and margin:
```yaml
beta: 5.0            # Increase from 2.0
gamma_beta_ratio: 0.8  # Increase from 0.5
```

**Issue: OOM during training**

Reduce batch size:
```yaml
per_device_train_batch_size: 1
gradient_accumulation_steps: 16  # Maintain effective batch
```

Enable gradient checkpointing:
```yaml
gradient_checkpointing: true
```

## Advanced topics

**Loss functions**: See [references/loss-functions.md](references/loss-functions.md) for sigmoid vs hinge loss, mathematical formulations, and when to use each.

**Hyperparameter tuning**: See [references/hyperparameters.md](references/hyperparameters.md) for beta, gamma, learning rate selection guide, and model-size-specific recommendations.

**Dataset preparation**: See [references/datasets.md](references/datasets.md) for preference data formats, quality filtering, and custom dataset creation.

## Hardware requirements

- **GPU**: NVIDIA A100/H100 recommended
- **VRAM**:
  - 7B model: 1× A100 40GB (DeepSpeed ZeRO-3)
  - 8B model: 2× A100 40GB
  - 70B model: 8× A100 80GB
- **Single-node**: DeepSpeed ZeRO-3 sufficient
- **Mixed precision**: BF16 recommended

**Memory optimization**:
- DeepSpeed ZeRO-3 (default config)
- Gradient checkpointing
- Flash Attention 2

## Resources

- Paper: https://arxiv.org/abs/2405.14734 (NeurIPS 2024)
- GitHub: https://github.com/princeton-nlp/SimPO
- Models: https://huggingface.co/princeton-nlp
- Alignment Handbook: https://github.com/huggingface/alignment-handbook





---


## 📚 Reference Materials


### Loss Functions

# Loss Functions

Complete guide to SimPO loss functions and mathematical formulations.

## Overview

SimPO supports two loss types:
- **Sigmoid** (default) - Smooth, differentiable loss
- **Hinge** - Margin-based, sparse loss

Both are reference-free (no reference model needed).

## SimPO Loss Formula

### Core Calculation

**Step 1: Log probability ratio**:
```
pi_logratios = log P_θ(y_chosen|x) - log P_θ(y_rejected|x)
```

**Step 2: Apply target margin**:
```
logits = pi_logratios - γ/β
```
Where:
- γ/β = `gamma_beta_ratio` (target margin)

**Step 3: Compute loss** (depends on loss type)

### Sigmoid Loss (Default)

**Formula**:
```
L = -log σ(β * logits) * (1 - ε) - log σ(-β * logits) * ε
```

Where:
- β = `beta` (reward scaling)
- σ = sigmoid function
- ε = `label_smoothing` (default 0.0)

**Implementation**:
```python
losses = (
    -F.logsigmoid(self.beta * logits) * (1 - self.label_smoothing)
    - F.logsigmoid(-self.beta * logits) * self.label_smoothing
)
```

**Characteristics**:
- Smooth, continuous gradients
- Probabilistic interpretation
- Standard choice for most tasks
- Works well with higher beta values

### Hinge Loss

**Formula**:
```
L = max(0, 1 - β * logits)
```

**Implementation**:
```python
losses = torch.relu(1 - self.beta * logits)
```

**Characteristics**:
- Non-smooth (has kink at logits = 1/β)
- Margin-based (SVM-style)
- Can lead to sparser solutions
- Less commonly used

## Comparison to DPO

### DPO Loss (Reference Model Required)

**Formula**:
```
L_DPO = -E[log σ(β * log(π_θ(y_w|x)/π_ref(y_w|x)) - β * log(π_θ(y_l|x)/π_ref(y_l|x)))]
```

**Key features**:
- Requires reference model π_ref
- Normalizes by reference log probabilities
- More conservative (stays close to reference)

### SimPO Loss (Reference-Free)

**Formula**:
```
L_SimPO = -log σ(β * (log π_θ(y_w|x) - log π_θ(y_l|x) - γ/β))
```

**Key features**:
- No reference model needed
- Direct preference optimization
- Target margin γ/β controls preference strength
- More efficient (fewer model forward passes)

**Visual comparison**:
```
DPO:    [Policy] - [Reference] → Loss
SimPO:  [Policy]               → Loss
```

## Average Log Probability Reward

### Calculation

**Per-token log probabilities**:
```python
# Get log probs for each token
per_token_logps = log_softmax(logits).gather(dim=-1, index=labels)

# Create mask to ignore padding
loss_mask = (labels != label_pad_token_id)
```

**Average log probability** (if `average_log_prob=True`):
```python
avg_logp = (per_token_logps * loss_mask).sum(-1) / loss_mask.sum(-1)
```

**Sum log probability** (if `average_log_prob=False`):
```python
sum_logp = (per_token_logps * loss_mask).sum(-1)
```

**Why average?**
- Normalizes for sequence length
- Prevents bias toward shorter/longer responses
- Standard practice in SimPO

### Reward Metrics

**Chosen reward**:
```python
chosen_rewards = beta * policy_chosen_logps.detach()
```

**Rejected reward**:
```python
rejected_rewards = beta * policy_rejected_logps.detach()
```

**Reward margin**:
```python
reward_margin = chosen_rewards.mean() - rejected_rewards.mean()
```

## Label Smoothing

### Formula with Smoothing

**Sigmoid loss**:
```
L = -log σ(β * logits) * (1 - ε) - log σ(-β * logits) * ε
```

**Effect**:
- ε = 0.0: No smoothing (default)
- ε = 0.1: 10% smoothing (soft labels)
- ε = 0.5: Maximum smoothing

**When to use**:
- Noisy preference labels
- Uncertain preferences
- Prevent overconfidence

**Config**:
```yaml
label_smoothing: 0.1  # 10% smoothing
```

## SFT Regularization

### Combined Loss

**With SFT component**:
```
L_total = L_SimPO + λ * L_SFT
```

Where:
- L_SFT = cross-entropy loss on chosen responses
- λ = `sft_weight` (0.0 to 1.0)

**Implementation**:
```python
if self.sft_weight > 0:
    sft_loss = -policy_chosen_logps
    total_loss = simpo_loss + self.sft_weight * sft_loss
```

**When to use**:
- Preserve model capabilities
- Prevent catastrophic forgetting
- Fine-tuning instruct models

**Trade-off**:
- Higher sft_weight: Preserve capabilities, less alignment
- Lower sft_weight: Stronger alignment, may forget capabilities

**Config**:
```yaml
sft_weight: 0.1  # 10% SFT regularization
```

## Loss Type Selection

### Sigmoid vs Hinge

| Aspect | Sigmoid | Hinge |
|--------|---------|-------|
| Smoothness | Smooth | Non-smooth |
| Gradients | Continuous | Discontinuous at margin |
| Sparsity | Dense solutions | Sparse solutions |
| Interpretability | Probabilistic | Geometric margin |
| Use case | **General purpose** | Margin-based tasks |
| Recommendation | **Default choice** | Experimental |

**Config**:
```yaml
# Sigmoid (default)
loss_type: sigmoid

# Hinge (alternative)
loss_type: hinge
```

## Mathematical Properties

### Gradient Analysis

**Sigmoid loss gradient**:
```
∂L/∂logits = -β * σ(-β * logits) * (1 - ε) + β * σ(β * logits) * ε
```

**Hinge loss gradient**:
```
∂L/∂logits = -β   if logits < 1/β
             0     otherwise
```

**Implications**:
- Sigmoid: Always provides gradient signal
- Hinge: No gradient when margin satisfied

### Convergence Behavior

**Sigmoid**:
- Asymptotically approaches zero loss
- Continues optimizing even with large margins
- Smoother training curves

**Hinge**:
- Reaches zero loss at margin
- Stops optimizing once margin satisfied
- May have training plateaus

## Complete Loss Examples

### Example 1: Basic SimPO (Sigmoid)

**Config**:
```yaml
beta: 2.0
gamma_beta_ratio: 0.5
loss_type: sigmoid
label_smoothing: 0.0
sft_weight: 0.0
```

**Loss calculation**:
```python
# Step 1: Compute log probs
chosen_logps = avg_log_prob(policy(chosen))    # e.g., -1.2
rejected_logps = avg_log_prob(policy(rejected)) # e.g., -2.5

# Step 2: Log ratio and margin
pi_logratios = -1.2 - (-2.5) = 1.3
logits = 1.3 - 0.5 = 0.8

# Step 3: Sigmoid loss
loss = -log(sigmoid(2.0 * 0.8))
     = -log(sigmoid(1.6))
     = -log(0.832)
     = 0.184
```

### Example 2: SimPO with SFT

**Config**:
```yaml
beta: 2.5
gamma_beta_ratio: 0.5
loss_type: sigmoid
sft_weight: 0.1
```

**Loss calculation**:
```python
# SimPO loss (as above)
simpo_loss = 0.184

# SFT loss
sft_loss = -chosen_logps = -(-1.2) = 1.2

# Total loss
total_loss = simpo_loss + 0.1 * sft_loss
           = 0.184 + 0.12
           = 0.304
```

## Debugging

### Check Reward Margins

**Low margin (< 0.5)**:
- Preferences not being learned
- Increase beta or gamma_beta_ratio

**High margin (> 5.0)**:
- May be overfitting
- Reduce beta or learning rate

**Monitor**:
```python
reward_margin = chosen_rewards.mean() - rejected_rewards.mean()
print(f"Reward margin: {reward_margin:.2f}")
```

### Check Log Probabilities

**Typical values**:
- Chosen: -1.0 to -2.0 (higher is better)
- Rejected: -2.0 to -4.0 (lower is worse)

**Warning signs**:
- Both very negative (< -10): Model not learning
- Both very positive (> 0): Numerical instability

## References

- SimPO paper: https://arxiv.org/abs/2405.14734
- DPO paper: https://arxiv.org/abs/2305.18290
- Implementation: https://github.com/princeton-nlp/SimPO




### Hyperparameters

# Hyperparameters

Complete guide to SimPO hyperparameter selection and tuning.

## Overview

Key hyperparameters in SimPO:
1. **Learning Rate** - Most critical
2. **Beta (β)** - Reward scaling
3. **Gamma-Beta Ratio (γ/β)** - Target margin
4. **SFT Weight** - Regularization strength

## Learning Rate

### Recommended Ranges

**By model size**:
| Model Size | Learning Rate | Notes |
|------------|---------------|-------|
| 1B-3B | 5e-7 to 1e-6 | Higher end safe |
| 7B-8B | 3e-7 to 5e-7 | **Standard** |
| 13B-30B | 1e-7 to 3e-7 | Lower for stability |
| 70B+ | 5e-8 to 1e-7 | Very conservative |

**By task type**:
| Task | Learning Rate | Reason |
|------|---------------|--------|
| General chat | 5e-7 | Standard |
| Code generation | 3e-7 | **Precise reasoning** |
| Math reasoning | 3e-7 | **Careful optimization** |
| Creative writing | 1e-6 | More aggressive OK |

### Why Learning Rate Matters

**Too high** (> 1e-6 for 7B):
- Loss divergence
- Catastrophic forgetting
- Unstable training

**Too low** (< 1e-7 for 7B):
- Very slow convergence
- May not finish in time
- Undertraining

**Optimal** (3e-7 to 5e-7 for 7B):
- Stable convergence
- Good final performance
- Efficient training

### Config Examples

**Mistral 7B (general)**:
```yaml
learning_rate: 5e-7
num_train_epochs: 1
warmup_ratio: 0.1
lr_scheduler_type: cosine
```

**Llama 3 8B (reasoning)**:
```yaml
learning_rate: 3e-7
num_train_epochs: 1
warmup_ratio: 0.1
lr_scheduler_type: cosine
```

**Gemma 2 9B (creative)**:
```yaml
learning_rate: 1e-6
num_train_epochs: 1
warmup_ratio: 0.1
lr_scheduler_type: linear
```

## Beta (β)

### Recommended Values

**Range**: 2.0 to 10.0 (much higher than DPO's 0.01-0.1)

**By preference strength**:
| Beta | Preference Strength | Use Case |
|------|-------------------|----------|
| 1.0-2.0 | Weak | Subtle preferences |
| 2.0-5.0 | **Standard** | General alignment |
| 5.0-10.0 | Strong | Clear preferences |

**Default**: 2.0 to 2.5

### Why Beta Matters

**Low beta** (< 2.0):
- Weak reward signal
- Slow preference learning
- May underfit

**High beta** (> 10.0):
- Very strong reward signal
- Risk of overfitting
- May ignore weak preferences

**Optimal** (2.0-5.0):
- Balanced reward scaling
- Stable training
- Good generalization

### Interaction with Gamma

**Beta and gamma together**:
```
Target margin in reward space = gamma
Target margin in logit space = gamma / beta
```

**Example**:
```yaml
beta: 2.0
gamma_beta_ratio: 0.5
# Effective gamma = 2.0 * 0.5 = 1.0
```

### Config Examples

**Weak preferences**:
```yaml
beta: 2.0
gamma_beta_ratio: 0.3  # Small margin
```

**Standard**:
```yaml
beta: 2.5
gamma_beta_ratio: 0.5  # Default
```

**Strong preferences**:
```yaml
beta: 5.0
gamma_beta_ratio: 0.7  # Larger margin
```

## Gamma-Beta Ratio (γ/β)

### Recommended Values

**Range**: 0.0 to 1.0

**By scenario**:
| Ratio | Margin | Use Case |
|-------|--------|----------|
| 0.0-0.3 | Small | Weak preference data |
| 0.4-0.6 | **Standard** | General use |
| 0.7-1.0 | Large | Very clear preferences |

**Default**: 0.5

### Why Gamma Matters

**Low gamma** (< 0.3):
- Small target margin
- Less aggressive alignment
- More conservative

**High gamma** (> 0.7):
- Large target margin
- Stronger alignment
- More aggressive

**Optimal** (0.4-0.6):
- Balanced margin
- Stable training
- Good alignment

### Mathematical Meaning

**In loss function**:
```python
logits = pi_logratios - gamma_beta_ratio
loss = -log(sigmoid(beta * logits))
```

**Interpretation**:
- gamma_beta_ratio shifts the decision boundary
- Higher ratio = requires larger log prob difference
- Controls how "clear" preferences must be

### Config Examples

**Noisy preferences**:
```yaml
gamma_beta_ratio: 0.3  # Smaller margin, more tolerant
```

**Standard**:
```yaml
gamma_beta_ratio: 0.5  # Default
```

**High-quality preferences**:
```yaml
gamma_beta_ratio: 0.8  # Larger margin, stricter
```

## SFT Weight

### Recommended Values

**Range**: 0.0 to 1.0

**By model type**:
| Model Type | SFT Weight | Reason |
|------------|-----------|--------|
| Base model | 0.0 | No prior capabilities |
| **Instruct model** | 0.05-0.1 | Preserve instruction following |
| Chat model | 0.1-0.2 | Preserve conversational skills |

**Default**: 0.0 (no SFT regularization)

### Why SFT Weight Matters

**Zero SFT** (0.0):
- Pure preference optimization
- May forget capabilities
- Standard for base models

**Low SFT** (0.05-0.1):
- Balanced approach
- **Recommended for instruct models**
- Slight capability preservation

**High SFT** (> 0.2):
- Strong capability preservation
- Weaker preference alignment
- May reduce alignment gains

### Trade-off

```
Total Loss = SimPO Loss + (sft_weight * SFT Loss)
```

**Example**:
```yaml
sft_weight: 0.1
# 90% preference optimization + 10% capability preservation
```

### Config Examples

**Base model (no SFT)**:
```yaml
model_name_or_path: mistralai/Mistral-7B-v0.1
sft_weight: 0.0
```

**Instruct model (light SFT)**:
```yaml
model_name_or_path: meta-llama/Meta-Llama-3-8B-Instruct
sft_weight: 0.1
```

**Chat model (moderate SFT)**:
```yaml
model_name_or_path: HuggingFaceH4/zephyr-7b-beta
sft_weight: 0.2
```

## Model-Size-Specific Recommendations

### 7B Models (Mistral, Llama 3)

**Standard config**:
```yaml
learning_rate: 5e-7
beta: 2.0
gamma_beta_ratio: 0.5
sft_weight: 0.0  # 0.1 if instruct model
num_train_epochs: 1
per_device_train_batch_size: 2
gradient_accumulation_steps: 4
```

### 8B-13B Models

**Standard config**:
```yaml
learning_rate: 3e-7
beta: 2.5
gamma_beta_ratio: 0.5
sft_weight: 0.1  # If instruct
num_train_epochs: 1
per_device_train_batch_size: 1
gradient_accumulation_steps: 8
```

### 70B Models

**Standard config**:
```yaml
learning_rate: 1e-7
beta: 2.0
gamma_beta_ratio: 0.5
sft_weight: 0.05
num_train_epochs: 1
per_device_train_batch_size: 1
gradient_accumulation_steps: 16
```

## Batch Size & Gradient Accumulation

### Effective Batch Size

```
Effective Batch Size = per_device_batch_size * num_gpus * grad_accum_steps
```

**Recommended effective batch sizes**:
- 7B: 128-256
- 13B: 64-128
- 70B: 32-64

### Config Examples

**Single GPU (A100 40GB)**:
```yaml
per_device_train_batch_size: 1
gradient_accumulation_steps: 128  # Effective batch = 128
```

**4 GPUs (A100 40GB)**:
```yaml
per_device_train_batch_size: 2
gradient_accumulation_steps: 16  # Effective batch = 2*4*16 = 128
```

**8 GPUs (A100 80GB)**:
```yaml
per_device_train_batch_size: 2
gradient_accumulation_steps: 8  # Effective batch = 2*8*8 = 128
```

## Loss Type

### Sigmoid vs Hinge

**Sigmoid** (default, recommended):
```yaml
loss_type: sigmoid
label_smoothing: 0.0
```

**Hinge** (experimental):
```yaml
loss_type: hinge
# No label smoothing for hinge
```

**When to use hinge**:
- Margin-based tasks
- SVM-style optimization
- Experimental purposes

**Generally**: Stick with sigmoid

## Tuning Guide

### Step 1: Start with Defaults

```yaml
learning_rate: 5e-7  # For 7B
beta: 2.0
gamma_beta_ratio: 0.5
sft_weight: 0.0  # 0.1 if instruct
loss_type: sigmoid
```

### Step 2: Monitor Training

**Check every 100 steps**:
- Loss curve (should decrease smoothly)
- Reward margin (should increase)
- Chosen/rejected logps (should separate)

### Step 3: Adjust if Needed

**If loss diverges**:
```yaml
learning_rate: 3e-7  # Reduce from 5e-7
beta: 1.0           # Reduce from 2.0
```

**If loss plateaus early**:
```yaml
learning_rate: 1e-6  # Increase from 5e-7
beta: 5.0           # Increase from 2.0
```

**If model forgets**:
```yaml
sft_weight: 0.2  # Increase from 0.0
```

## Complete Example Configs

### Mistral 7B Base (Standard)

```yaml
model_name_or_path: mistralai/Mistral-7B-v0.1
dataset_mixer:
  HuggingFaceH4/ultrafeedback_binarized: 1.0

learning_rate: 5e-7
beta: 2.0
gamma_beta_ratio: 0.5
loss_type: sigmoid
sft_weight: 0.0

num_train_epochs: 1
per_device_train_batch_size: 2
gradient_accumulation_steps: 4
warmup_ratio: 0.1
lr_scheduler_type: cosine

bf16: true
gradient_checkpointing: true
```

### Llama 3 8B Instruct (Reasoning)

```yaml
model_name_or_path: meta-llama/Meta-Llama-3-8B-Instruct
dataset_mixer:
  argilla/distilabel-math-preference-dpo: 1.0

learning_rate: 3e-7
beta: 5.0
gamma_beta_ratio: 0.7
loss_type: sigmoid
sft_weight: 0.1

num_train_epochs: 1
per_device_train_batch_size: 1
gradient_accumulation_steps: 16
warmup_ratio: 0.1
lr_scheduler_type: cosine
```

## References

- SimPO paper: https://arxiv.org/abs/2405.14734
- Alignment Handbook: https://github.com/huggingface/alignment-handbook




### Datasets

# Datasets

Complete guide to preference datasets for SimPO training.

## Dataset Format

### Required Fields

Preference datasets must contain:
```json
{
  "prompt": "User question or instruction",
  "chosen": "Better/preferred response",
  "rejected": "Worse/rejected response"
}
```

**Alternative field names** (auto-detected):
- `prompt` → `question`, `instruction`, `input`
- `chosen` → `response_chosen`, `winner`, `preferred`
- `rejected` → `response_rejected`, `loser`

### Example Entry

```json
{
  "prompt": "Explain quantum computing in simple terms.",
  "chosen": "Quantum computing uses quantum bits (qubits) that can exist in multiple states simultaneously through superposition. This allows quantum computers to process many possibilities at once, making them potentially much faster than classical computers for specific tasks like cryptography and optimization.",
  "rejected": "It's like regular computing but quantum."
}
```

## Popular Datasets

### 1. UltraFeedback (Recommended)

**HuggingFaceH4/ultrafeedback_binarized**:
- **Size**: 60K preference pairs
- **Quality**: High (GPT-4 annotations)
- **Domain**: General instruction following
- **Format**: Clean, ready-to-use

**Config**:
```yaml
dataset_mixer:
  HuggingFaceH4/ultrafeedback_binarized: 1.0
dataset_splits:
  - train_prefs
  - test_prefs
```

### 2. Argilla UltraFeedback (Cleaned)

**argilla/ultrafeedback-binarized-preferences-cleaned**:
- **Size**: 50K pairs (filtered)
- **Quality**: Very high (deduped, cleaned)
- **Domain**: General
- **Format**: Clean

**Config**:
```yaml
dataset_mixer:
  argilla/ultrafeedback-binarized-preferences-cleaned: 1.0
```

### 3. Distilabel Math

**argilla/distilabel-math-preference-dpo**:
- **Size**: 30K pairs
- **Quality**: High (GSM8K, MATH)
- **Domain**: Math reasoning
- **Format**: Math-specific

**Config**:
```yaml
dataset_mixer:
  argilla/distilabel-math-preference-dpo: 1.0
```

### 4. HelpSteer

**nvidia/HelpSteer**:
- **Size**: 38K samples
- **Quality**: High (human ratings)
- **Domain**: Helpfulness alignment
- **Format**: Multi-attribute ratings

**Config**:
```yaml
dataset_mixer:
  nvidia/HelpSteer: 1.0
```

### 5. Anthropic HH-RLHF

**Anthropic/hh-rlhf**:
- **Size**: 161K samples
- **Quality**: High (human preferences)
- **Domain**: Harmless + helpful
- **Format**: Conversational

**Config**:
```yaml
dataset_mixer:
  Anthropic/hh-rlhf: 1.0
```

## Dataset Mixing

### Multiple Datasets

**Equal mix**:
```yaml
dataset_mixer:
  HuggingFaceH4/ultrafeedback_binarized: 0.5
  Anthropic/hh-rlhf: 0.5
```

**Weighted mix**:
```yaml
dataset_mixer:
  HuggingFaceH4/ultrafeedback_binarized: 0.7
  argilla/distilabel-math-preference-dpo: 0.2
  nvidia/HelpSteer: 0.1
```

**Domain-specific emphasis**:
```yaml
# 80% general + 20% math
dataset_mixer:
  HuggingFaceH4/ultrafeedback_binarized: 0.8
  argilla/distilabel-math-preference-dpo: 0.2
```

## Data Quality

### Quality Indicators

**Good preference data**:
- ✅ Clear quality difference between chosen/rejected
- ✅ Diverse prompts
- ✅ Minimal noise/annotation errors
- ✅ Appropriate difficulty level

**Poor preference data**:
- ❌ Ambiguous preferences
- ❌ Repetitive prompts
- ❌ Annotation noise
- ❌ Too easy/hard prompts

### Quality Filtering

**Filter by length difference**:
```python
def filter_by_length(example):
    chosen_len = len(example['chosen'].split())
    rejected_len = len(example['rejected'].split())
    # Reject if chosen is much shorter (potential low-effort)
    return chosen_len >= rejected_len * 0.5

dataset = dataset.filter(filter_by_length)
```

**Filter by diversity**:
```python
seen_prompts = set()

def filter_duplicates(example):
    prompt = example['prompt']
    if prompt in seen_prompts:
        return False
    seen_prompts.add(prompt)
    return True

dataset = dataset.filter(filter_duplicates)
```

## Custom Dataset Creation

### Format 1: JSON Lines

**File** (`preferences.jsonl`):
```jsonl
{"prompt": "What is Python?", "chosen": "Python is a high-level programming language...", "rejected": "It's a snake."}
{"prompt": "Explain AI.", "chosen": "AI refers to systems that can...", "rejected": "It's computers that think."}
```

**Load**:
```yaml
dataset_mixer:
  json:
    data_files: preferences.jsonl
```

### Format 2: HuggingFace Dataset

**Create from dict**:
```python
from datasets import Dataset

data = {
    "prompt": ["What is Python?", "Explain AI."],
    "chosen": ["Python is...", "AI refers to..."],
    "rejected": ["It's a snake.", "It's computers..."]
}

dataset = Dataset.from_dict(data)
dataset.push_to_hub("username/my-preferences")
```

**Use in config**:
```yaml
dataset_mixer:
  username/my-preferences: 1.0
```

### Format 3: ChatML

**For conversational data**:
```json
{
  "prompt": [
    {"role": "user", "content": "What is quantum computing?"}
  ],
  "chosen": [
    {"role": "assistant", "content": "Quantum computing uses qubits..."}
  ],
  "rejected": [
    {"role": "assistant", "content": "It's like regular computing but quantum."}
  ]
}
```

**Apply chat template**:
```yaml
dataset_text_field: null  # Will apply chat template
```

## Synthetic Data Generation

### Using GPT-4

**Prompt template**:
```
Given the following question:
{prompt}

Generate two responses:
1. A high-quality, detailed response (chosen)
2. A low-quality, brief response (rejected)

Format as JSON with "chosen" and "rejected" fields.
```

**Example code**:
```python
import openai

def generate_pair(prompt):
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[{
            "role": "user",
            "content": f"Given: {prompt}\n\nGenerate chosen/rejected pair in JSON."
        }]
    )
    return json.loads(response.choices[0].message.content)

# Generate dataset
prompts = load_prompts()
dataset = [generate_pair(p) for p in prompts]
```

### Using Local Model

**With vLLM**:
```python
from vllm import LLM

llm = LLM(model="meta-llama/Meta-Llama-3-70B-Instruct")

def generate_variations(prompt):
    # Generate multiple completions
    outputs = llm.generate(
        [prompt] * 4,
        sampling_params={
            "temperature": 0.8,
            "top_p": 0.9,
            "max_tokens": 512
        }
    )

    # Select best/worst
    chosen = max(outputs, key=lambda x: len(x.outputs[0].text))
    rejected = min(outputs, key=lambda x: len(x.outputs[0].text))

    return {
        "prompt": prompt,
        "chosen": chosen.outputs[0].text,
        "rejected": rejected.outputs[0].text
    }
```

## Data Preprocessing

### Truncation

**Limit sequence length**:
```yaml
max_prompt_length: 512
max_completion_length: 512
max_length: 1024  # Total
```

**Implementation**:
```python
def truncate_example(example):
    tokenizer.truncation_side = "left"  # For prompts
    prompt_tokens = tokenizer(
        example['prompt'],
        max_length=512,
        truncation=True
    )

    tokenizer.truncation_side = "right"  # For completions
    chosen_tokens = tokenizer(
        example['chosen'],
        max_length=512,
        truncation=True
    )

    return {
        "prompt": tokenizer.decode(prompt_tokens['input_ids']),
        "chosen": tokenizer.decode(chosen_tokens['input_ids'])
    }

dataset = dataset.map(truncate_example)
```

### Deduplication

**Remove exact duplicates**:
```python
dataset = dataset.unique('prompt')
```

**Remove near-duplicates** (MinHash):
```python
from datasketch import MinHash, MinHashLSH

def deduplicate_lsh(dataset, threshold=0.8):
    lsh = MinHashLSH(threshold=threshold, num_perm=128)
    seen = []

    for i, example in enumerate(dataset):
        m = MinHash(num_perm=128)
        for word in example['prompt'].split():
            m.update(word.encode('utf8'))

        if not lsh.query(m):
            lsh.insert(i, m)
            seen.append(example)

    return Dataset.from_list(seen)

dataset = deduplicate_lsh(dataset)
```

## Data Augmentation

### Paraphrasing Prompts

```python
def paraphrase_prompt(example):
    # Use paraphrasing model
    paraphrased = paraphrase_model(example['prompt'])

    return [
        example,  # Original
        {
            "prompt": paraphrased,
            "chosen": example['chosen'],
            "rejected": example['rejected']
        }
    ]

dataset = dataset.map(paraphrase_prompt, batched=False, remove_columns=[])
```

### Difficulty Balancing

**Mix easy/medium/hard**:
```python
def categorize_difficulty(example):
    prompt_len = len(example['prompt'].split())
    if prompt_len < 20:
        return "easy"
    elif prompt_len < 50:
        return "medium"
    else:
        return "hard"

dataset = dataset.map(lambda x: {"difficulty": categorize_difficulty(x)})

# Sample balanced dataset
easy = dataset.filter(lambda x: x['difficulty'] == 'easy').shuffle().select(range(1000))
medium = dataset.filter(lambda x: x['difficulty'] == 'medium').shuffle().select(range(1000))
hard = dataset.filter(lambda x: x['difficulty'] == 'hard').shuffle().select(range(1000))

balanced = concatenate_datasets([easy, medium, hard]).shuffle()
```

## Dataset Statistics

### Compute Stats

```python
def compute_stats(dataset):
    prompt_lens = [len(x['prompt'].split()) for x in dataset]
    chosen_lens = [len(x['chosen'].split()) for x in dataset]
    rejected_lens = [len(x['rejected'].split()) for x in dataset]

    print(f"Dataset size: {len(dataset)}")
    print(f"Avg prompt length: {np.mean(prompt_lens):.1f} words")
    print(f"Avg chosen length: {np.mean(chosen_lens):.1f} words")
    print(f"Avg rejected length: {np.mean(rejected_lens):.1f} words")
    print(f"Chosen > Rejected: {sum(c > r for c, r in zip(chosen_lens, rejected_lens)) / len(dataset):.1%}")

compute_stats(dataset)
```

**Expected output**:
```
Dataset size: 50000
Avg prompt length: 45.2 words
Avg chosen length: 180.5 words
Avg rejected length: 120.3 words
Chosen > Rejected: 85.2%
```

## Best Practices

### 1. Data Quality Over Quantity

- **Prefer**: 10K high-quality pairs
- **Over**: 100K noisy pairs

### 2. Clear Preference Signals

- Chosen should be noticeably better
- Avoid marginal differences
- Remove ambiguous pairs

### 3. Domain Matching

- Match dataset domain to target use case
- Mix datasets for broader coverage
- Include safety-filtered data

### 4. Validate Before Training

```python
# Sample 10 random examples
samples = dataset.shuffle().select(range(10))

for ex in samples:
    print(f"Prompt: {ex['prompt']}")
    print(f"Chosen: {ex['chosen'][:100]}...")
    print(f"Rejected: {ex['rejected'][:100]}...")
    print(f"Preference clear: {'✓' if len(ex['chosen']) > len(ex['rejected']) else '?'}")
    print()
```

## References

- HuggingFace Datasets: https://huggingface.co/datasets
- Alignment Handbook: https://github.com/huggingface/alignment-handbook
- UltraFeedback: https://huggingface.co/datasets/HuggingFaceH4/ultrafeedback_binarized




---

## 🚀 Usage

**Reference this template:** `@skill-simpo-training.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
