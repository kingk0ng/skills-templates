---
id: skill-fine-tuning-with-trl
type: skill
name: fine-tuning-with-trl
description: Fine-tune LLMs using reinforcement learning with TRL - SFT for instruction
  tuning, DPO for preference alignment, PPO/GRPO for reward optimization, and reward
  model training. Use when need RLHF, align model with preferences, or train from
  human feedback. Works with HuggingFace Transformers.
category: ai-research
complexity: medium
keywords:
- github
- optimization
- python
- test
- testing
capabilities: []
token_estimate: 1574
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,574 -->
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




# fine-tuning-with-trl

> Fine-tune LLMs using reinforcement learning with TRL - SFT for instruction tuning, DPO for preference alignment, PPO/GRPO for reward optimization, and reward model training. Use when need RLHF, align model with preferences, or train from human feedback. Works with HuggingFace Transformers.

# TRL - Transformer Reinforcement Learning

## Quick start

TRL provides post-training methods for aligning language models with human preferences.

**Installation**:
```bash
pip install trl transformers datasets peft accelerate
```

**Supervised Fine-Tuning** (instruction tuning):
```python
from trl import SFTTrainer

trainer = SFTTrainer(
    model="Qwen/Qwen2.5-0.5B",
    train_dataset=dataset,  # Prompt-completion pairs
)
trainer.train()
```

**DPO** (align with preferences):
```python
from trl import DPOTrainer, DPOConfig

config = DPOConfig(output_dir="model-dpo", beta=0.1)
trainer = DPOTrainer(
    model=model,
    args=config,
    train_dataset=preference_dataset,  # chosen/rejected pairs
    processing_class=tokenizer
)
trainer.train()
```

## Common workflows

### Workflow 1: Full RLHF pipeline (SFT → Reward Model → PPO)

Complete pipeline from base model to human-aligned model.

Copy this checklist:

```
RLHF Training:
- [ ] Step 1: Supervised fine-tuning (SFT)
- [ ] Step 2: Train reward model
- [ ] Step 3: PPO reinforcement learning
- [ ] Step 4: Evaluate aligned model
```

**Step 1: Supervised fine-tuning**

Train base model on instruction-following data:

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
from trl import SFTTrainer, SFTConfig
from datasets import load_dataset

# Load model
model = AutoModelForCausalLM.from_pretrained("Qwen/Qwen2.5-0.5B")
tokenizer = AutoTokenizer.from_pretrained("Qwen/Qwen2.5-0.5B")

# Load instruction dataset
dataset = load_dataset("trl-lib/Capybara", split="train")

# Configure training
training_args = SFTConfig(
    output_dir="Qwen2.5-0.5B-SFT",
    per_device_train_batch_size=4,
    num_train_epochs=1,
    learning_rate=2e-5,
    logging_steps=10,
    save_strategy="epoch"
)

# Train
trainer = SFTTrainer(
    model=model,
    args=training_args,
    train_dataset=dataset,
    tokenizer=tokenizer
)
trainer.train()
trainer.save_model()
```

**Step 2: Train reward model**

Train model to predict human preferences:

```python
from transformers import AutoModelForSequenceClassification
from trl import RewardTrainer, RewardConfig

# Load SFT model as base
model = AutoModelForSequenceClassification.from_pretrained(
    "Qwen2.5-0.5B-SFT",
    num_labels=1  # Single reward score
)
tokenizer = AutoTokenizer.from_pretrained("Qwen2.5-0.5B-SFT")

# Load preference data (chosen/rejected pairs)
dataset = load_dataset("trl-lib/ultrafeedback_binarized", split="train")

# Configure training
training_args = RewardConfig(
    output_dir="Qwen2.5-0.5B-Reward",
    per_device_train_batch_size=2,
    num_train_epochs=1,
    learning_rate=1e-5
)

# Train reward model
trainer = RewardTrainer(
    model=model,
    args=training_args,
    processing_class=tokenizer,
    train_dataset=dataset
)
trainer.train()
trainer.save_model()
```

**Step 3: PPO reinforcement learning**

Optimize policy using reward model:

```bash
python -m trl.scripts.ppo \
    --model_name_or_path Qwen2.5-0.5B-SFT \
    --reward_model_path Qwen2.5-0.5B-Reward \
    --dataset_name trl-internal-testing/descriptiveness-sentiment-trl-style \
    --output_dir Qwen2.5-0.5B-PPO \
    --learning_rate 3e-6 \
    --per_device_train_batch_size 64 \
    --total_episodes 10000
```

**Step 4: Evaluate**

```python
from transformers import pipeline

# Load aligned model
generator = pipeline("text-generation", model="Qwen2.5-0.5B-PPO")

# Test
prompt = "Explain quantum computing to a 10-year-old"
output = generator(prompt, max_length=200)[0]["generated_text"]
print(output)
```

### Workflow 2: Simple preference alignment with DPO

Align model with preferences without reward model.

Copy this checklist:

```
DPO Training:
- [ ] Step 1: Prepare preference dataset
- [ ] Step 2: Configure DPO
- [ ] Step 3: Train with DPOTrainer
- [ ] Step 4: Evaluate alignment
```

**Step 1: Prepare preference dataset**

Dataset format:
```json
{
  "prompt": "What is the capital of France?",
  "chosen": "The capital of France is Paris.",
  "rejected": "I don't know."
}
```

Load dataset:
```python
from datasets import load_dataset

dataset = load_dataset("trl-lib/ultrafeedback_binarized", split="train")
# Or load your own
# dataset = load_dataset("json", data_files="preferences.json")
```

**Step 2: Configure DPO**

```python
from trl import DPOConfig

config = DPOConfig(
    output_dir="Qwen2.5-0.5B-DPO",
    per_device_train_batch_size=4,
    num_train_epochs=1,
    learning_rate=5e-7,
    beta=0.1,  # KL penalty strength
    max_prompt_length=512,
    max_length=1024,
    logging_steps=10
)
```

**Step 3: Train with DPOTrainer**

```python
from transformers import AutoModelForCausalLM, AutoTokenizer
from trl import DPOTrainer

model = AutoModelForCausalLM.from_pretrained("Qwen/Qwen2.5-0.5B-Instruct")
tokenizer = AutoTokenizer.from_pretrained("Qwen/Qwen2.5-0.5B-Instruct")

trainer = DPOTrainer(
    model=model,
    args=config,
    train_dataset=dataset,
    processing_class=tokenizer
)

trainer.train()
trainer.save_model()
```

**CLI alternative**:
```bash
trl dpo \
    --model_name_or_path Qwen/Qwen2.5-0.5B-Instruct \
    --dataset_name argilla/Capybara-Preferences \
    --output_dir Qwen2.5-0.5B-DPO \
    --per_device_train_batch_size 4 \
    --learning_rate 5e-7 \
    --beta 0.1
```

### Workflow 3: Memory-efficient online RL with GRPO

Train with reinforcement learning using minimal memory.

Copy this checklist:

```
GRPO Training:
- [ ] Step 1: Define reward function
- [ ] Step 2: Configure GRPO
- [ ] Step 3: Train with GRPOTrainer
```

**Step 1: Define reward function**

```python
def reward_function(completions, **kwargs):
    """
    Compute rewards for completions.

    Args:
        completions: List of generated texts

    Returns:
        List of reward scores (floats)
    """
    rewards = []
    for completion in completions:
        # Example: reward based on length and unique words
        score = len(completion.split())  # Favor longer responses
        score += len(set(completion.lower().split()))  # Reward unique words
        rewards.append(score)
    return rewards
```

Or use a reward model:
```python
from transformers import pipeline

reward_model = pipeline("text-classification", model="reward-model-path")

def reward_from_model(completions, prompts, **kwargs):
    # Combine prompt + completion
    full_texts = [p + c for p, c in zip(prompts, completions)]
    # Get reward scores
    results = reward_model(full_texts)
    return [r["score"] for r in results]
```

**Step 2: Configure GRPO**

```python
from trl import GRPOConfig

config = GRPOConfig(
    output_dir="Qwen2-GRPO",
    per_device_train_batch_size=4,
    num_train_epochs=1,
    learning_rate=1e-5,
    num_generations=4,  # Generate 4 completions per prompt
    max_new_tokens=128
)
```

**Step 3: Train with GRPOTrainer**

```python
from datasets import load_dataset
from trl import GRPOTrainer

# Load prompt-only dataset
dataset = load_dataset("trl-lib/tldr", split="train")

trainer = GRPOTrainer(
    model="Qwen/Qwen2-0.5B-Instruct",
    reward_funcs=reward_function,  # Your reward function
    args=config,
    train_dataset=dataset
)

trainer.train()
```

**CLI**:
```bash
trl grpo \
    --model_name_or_path Qwen/Qwen2-0.5B-Instruct \
    --dataset_name trl-lib/tldr \
    --output_dir Qwen2-GRPO \
    --num_generations 4
```

## When to use vs alternatives

**Use TRL when:**
- Need to align model with human preferences
- Have preference data (chosen/rejected pairs)
- Want to use reinforcement learning (PPO, GRPO)
- Need reward model training
- Doing RLHF (full pipeline)

**Method selection**:
- **SFT**: Have prompt-completion pairs, want basic instruction following
- **DPO**: Have preferences, want simple alignment (no reward model needed)
- **PPO**: Have reward model, need maximum control over RL
- **GRPO**: Memory-constrained, want online RL
- **Reward Model**: Building RLHF pipeline, need to score generations

**Use alternatives instead:**
- **HuggingFace Trainer**: Basic fine-tuning without RL
- **Axolotl**: YAML-based training configuration
- **LitGPT**: Educational, minimal fine-tuning
- **Unsloth**: Fast LoRA training

## Common issues

**Issue: OOM during DPO training**

Reduce batch size and sequence length:
```python
config = DPOConfig(
    per_device_train_batch_size=1,  # Reduce from 4
    max_length=512,  # Reduce from 1024
    gradient_accumulation_steps=8  # Maintain effective batch
)
```

Or use gradient checkpointing:
```python
model.gradient_checkpointing_enable()
```

**Issue: Poor alignment quality**

Tune beta parameter:
```python
# Higher beta = more conservative (stays closer to reference)
config = DPOConfig(beta=0.5)  # Default 0.1

# Lower beta = more aggressive alignment
config = DPOConfig(beta=0.01)
```

**Issue: Reward model not learning**

Check loss type and learning rate:
```python
config = RewardConfig(
    learning_rate=1e-5,  # Try different LR
    num_train_epochs=3  # Train longer
)
```

Ensure preference dataset has clear winners:
```python
# Verify dataset
print(dataset[0])
# Should have clear chosen > rejected
```

**Issue: PPO training unstable**

Adjust KL coefficient:
```python
config = PPOConfig(
    kl_coef=0.1,  # Increase from 0.05
    cliprange=0.1  # Reduce from 0.2
)
```

## Advanced topics

**SFT training guide**: See [references/sft-training.md](references/sft-training.md) for dataset formats, chat templates, packing strategies, and multi-GPU training.

**DPO variants**: See [references/dpo-variants.md](references/dpo-variants.md) for IPO, cDPO, RPO, and other DPO loss functions with recommended hyperparameters.

**Reward modeling**: See [references/reward-modeling.md](references/reward-modeling.md) for outcome vs process rewards, Bradley-Terry loss, and reward model evaluation.

**Online RL methods**: See [references/online-rl.md](references/online-rl.md) for PPO, GRPO, RLOO, and OnlineDPO with detailed configurations.

## Hardware requirements

- **GPU**: NVIDIA (CUDA required)
- **VRAM**: Depends on model and method
  - SFT 7B: 16GB (with LoRA)
  - DPO 7B: 24GB (stores reference model)
  - PPO 7B: 40GB (policy + reward model)
  - GRPO 7B: 24GB (more memory efficient)
- **Multi-GPU**: Supported via `accelerate`
- **Mixed precision**: BF16 recommended (A100/H100)

**Memory optimization**:
- Use LoRA/QLoRA for all methods
- Enable gradient checkpointing
- Use smaller batch sizes with gradient accumulation

## Resources

- Docs: https://huggingface.co/docs/trl/
- GitHub: https://github.com/huggingface/trl
- Papers:
  - "Training language models to follow instructions with human feedback" (InstructGPT, 2022)
  - "Direct Preference Optimization: Your Language Model is Secretly a Reward Model" (DPO, 2023)
  - "Group Relative Policy Optimization" (GRPO, 2024)
- Examples: https://github.com/huggingface/trl/tree/main/examples/scripts





---


## 📚 Reference Materials


### Reward Modeling

# Reward Modeling

Guide to training reward models with TRL for RLHF pipelines.

## Overview

Reward models score completions based on human preferences. Used in:
- PPO training (RL feedback)
- GRPO online RL
- Completion ranking

## Basic Training

```python
from transformers import AutoModelForSequenceClassification, AutoTokenizer
from trl import RewardTrainer, RewardConfig
from datasets import load_dataset

# Load model (num_labels=1 for single reward score)
model = AutoModelForSequenceClassification.from_pretrained(
    "Qwen/Qwen2.5-0.5B-Instruct",
    num_labels=1
)
tokenizer = AutoTokenizer.from_pretrained("Qwen/Qwen2.5-0.5B-Instruct")

# Load preference dataset (chosen/rejected pairs)
dataset = load_dataset("trl-lib/ultrafeedback_binarized", split="train")

# Configure
config = RewardConfig(
    output_dir="Qwen2.5-Reward",
    per_device_train_batch_size=2,
    num_train_epochs=1,
    learning_rate=1e-5
)

# Train
trainer = RewardTrainer(
    model=model,
    args=config,
    processing_class=tokenizer,
    train_dataset=dataset
)
trainer.train()
```

## Dataset Format

Required fields:
```json
{
  "prompt": "Question or instruction",
  "chosen": "Better response",
  "rejected": "Worse response"
}
```

## Bradley-Terry Loss

Default loss function:
```
loss = -log(sigmoid(reward_chosen - reward_rejected))
```

Learns to score chosen > rejected.

## Using Reward Models

### Inference

```python
from transformers import pipeline

# Load trained reward model
reward_pipe = pipeline("text-classification", model="Qwen2.5-Reward")

# Score completions
texts = ["Good answer", "Bad answer"]
scores = reward_pipe(texts)
print(scores)  # Higher score = better
```

### In PPO

```python
from trl import PPOTrainer, PPOConfig

config = PPOConfig(
    reward_model_path="Qwen2.5-Reward"  # Use trained reward model
)

trainer = PPOTrainer(
    model=policy_model,
    config=config,
    # Reward model loaded automatically
)
```

## Hyperparameters

| Model Size | Learning Rate | Batch Size | Epochs |
|------------|---------------|------------|--------|
| <1B | 2e-5 | 4-8 | 1-2 |
| 1-7B | 1e-5 | 2-4 | 1 |
| 7-13B | 5e-6 | 1-2 | 1 |

## Evaluation

Check reward separation:
```python
# Chosen should score higher than rejected
chosen_rewards = model(**chosen_inputs).logits
rejected_rewards = model(**rejected_inputs).logits

accuracy = (chosen_rewards > rejected_rewards).float().mean()
print(f"Accuracy: {accuracy:.2%}")  # Target: >80%
```

## References

- InstructGPT paper: https://arxiv.org/abs/2203.02155
- TRL docs: https://huggingface.co/docs/trl/reward_trainer




### Dpo Variants

# DPO Variants

Complete guide to Direct Preference Optimization loss variants in TRL.

## Overview

DPO optimizes models using preference data (chosen/rejected pairs). TRL supports 10+ loss variants for different scenarios.

## Loss Types

### 1. Sigmoid (Standard DPO)

**Formula**: `-log(sigmoid(β * logits))`

**When to use**: Default choice, general preference alignment

**Config**:
```python
DPOConfig(
    loss_type="sigmoid",
    beta=0.1,  # KL penalty
    per_device_train_batch_size=64,
    learning_rate=1e-6
)
```

### 2. IPO (Identity Policy Optimization)

**Formula**: `(logits - 1/(2β))²`

**When to use**: Better theoretical foundation, reduce overfitting

**Config**:
```python
DPOConfig(
    loss_type="ipo",
    beta=0.1,
    per_device_train_batch_size=90,
    learning_rate=1e-2
)
```

### 3. Hinge (SLiC)

**Formula**: `ReLU(1 - β * logits)`

**When to use**: Margin-based objective

**Config**:
```python
DPOConfig(
    loss_type="hinge",
    beta=0.1,
    per_device_train_batch_size=512,
    learning_rate=1e-4
)
```

### 4. Robust DPO

**Formula**: Sigmoid with label smoothing for noise robustness

**When to use**: Noisy preference labels

**Config**:
```python
DPOConfig(
    loss_type="robust",
    beta=0.01,
    label_smoothing=0.1,  # Noise probability
    per_device_train_batch_size=16,
    learning_rate=1e-3,
    max_prompt_length=128,
    max_length=512
)
```

### 5. BCO Pair (Binary Classification)

**Formula**: Train binary classifier (chosen=1, rejected=0)

**When to use**: Pairwise preference data

**Config**:
```python
DPOConfig(
    loss_type="bco_pair",
    beta=0.01,
    per_device_train_batch_size=128,
    learning_rate=5e-7,
    max_prompt_length=1536,
    max_completion_length=512
)
```

### 6. SPPO Hard

**Formula**: Push chosen→0.5, rejected→-0.5

**When to use**: Nash equilibrium, sparse data

**Config**:
```python
DPOConfig(
    loss_type="sppo_hard",
    beta=0.1
)
```

### 7. DiscoPOP

**Formula**: Log-Ratio Modulated Loss

**When to use**: Automated loss discovery

**Config**:
```python
DPOConfig(
    loss_type="discopop",
    beta=0.05,
    discopop_tau=0.05,
    per_device_train_batch_size=64,
    learning_rate=5e-7
)
```

### 8. APO Zero

**Formula**: Increase chosen, decrease rejected likelihood

**When to use**: Model worse than winning outputs

**Config**:
```python
DPOConfig(
    loss_type="apo_zero",
    beta=0.1,
    per_device_train_batch_size=64,
    learning_rate=2e-7,
    max_prompt_length=512,
    max_completion_length=512
)
```

### 9. APO Down

**Formula**: Decrease both, emphasize rejected reduction

**When to use**: Model better than winning outputs

**Config**:
```python
DPOConfig(
    loss_type="apo_down",
    beta=0.1,
    # Same hyperparameters as apo_zero
)
```

### 10. AOT & AOT Pair

**Formula**: Distributional alignment via stochastic dominance

**When to use**:
- `aot_pair`: Paired preference data
- `aot`: Unpaired data

**Config**:
```python
DPOConfig(
    loss_type="aot_pair",  # or "aot"
    beta=0.1,
    label_smoothing=0.0
)
```

## Multi-Loss Training

Combine multiple losses:

```python
DPOConfig(
    loss_type=["sigmoid", "ipo"],
    loss_weights=[0.7, 0.3],  # Weighted combination
    beta=0.1
)
```

## Key Parameters

### Beta (β)

Controls deviation from reference model:
- **Higher** (0.5): More conservative, stays close to reference
- **Lower** (0.01): More aggressive alignment
- **Default**: 0.1

### Label Smoothing

For robust DPO:
- **0.0**: No smoothing (default)
- **0.1-0.3**: Moderate noise robustness
- **0.5**: Maximum noise tolerance

### Max Lengths

- `max_prompt_length`: 128-1536
- `max_completion_length`: 128-512
- `max_length`: Total sequence (1024-2048)

## Comparison Table

| Loss | Speed | Stability | Best For |
|------|-------|-----------|----------|
| Sigmoid | Fast | Good | **General use** |
| IPO | Fast | Better | Overfitting issues |
| Hinge | Fast | Good | Margin objectives |
| Robust | Fast | Best | Noisy data |
| BCO | Medium | Good | Binary classification |
| DiscoPOP | Fast | Good | New architectures |
| APO | Fast | Good | Model quality matching |

## References

- DPO paper: https://arxiv.org/abs/2305.18290
- IPO paper: https://arxiv.org/abs/2310.12036
- TRL docs: https://huggingface.co/docs/trl/dpo_trainer




### Online Rl

# Online RL Methods

Guide to online reinforcement learning with PPO, GRPO, RLOO, and OnlineDPO.

## Overview

Online RL generates completions during training and optimizes based on rewards.

## PPO (Proximal Policy Optimization)

Classic RL algorithm for LLM alignment.

### Basic Usage

```bash
python -m trl.scripts.ppo \
    --model_name_or_path Qwen/Qwen2.5-0.5B-Instruct \
    --reward_model_path reward-model \
    --dataset_name trl-internal-testing/descriptiveness-sentiment-trl-style \
    --output_dir model-ppo \
    --learning_rate 3e-6 \
    --per_device_train_batch_size 64 \
    --total_episodes 10000 \
    --num_ppo_epochs 4 \
    --kl_coef 0.05
```

### Key Parameters

- `kl_coef`: KL penalty (0.05-0.2)
- `num_ppo_epochs`: Epochs per batch (2-4)
- `cliprange`: PPO clip (0.1-0.3)
- `vf_coef`: Value function coef (0.1)

## GRPO (Group Relative Policy Optimization)

Memory-efficient online RL.

### Basic Usage

```python
from trl import GRPOTrainer, GRPOConfig
from datasets import load_dataset

# Define reward function
def reward_func(completions, **kwargs):
    return [len(set(c.split())) for c in completions]

config = GRPOConfig(
    output_dir="model-grpo",
    num_generations=4,  # Completions per prompt
    max_new_tokens=128
)

trainer = GRPOTrainer(
    model="Qwen/Qwen2-0.5B-Instruct",
    reward_funcs=reward_func,
    args=config,
    train_dataset=load_dataset("trl-lib/tldr", split="train")
)
trainer.train()
```

### Key Parameters

- `num_generations`: 2-8 completions
- `max_new_tokens`: 64-256
- Learning rate: 1e-5 to 1e-4

## Memory Comparison

| Method | Memory (7B) | Speed | Use Case |
|--------|-------------|-------|----------|
| PPO | 40GB | Medium | Maximum control |
| GRPO | 24GB | Fast | **Memory-constrained** |
| OnlineDPO | 28GB | Fast | No reward model |

## References

- PPO paper: https://arxiv.org/abs/1707.06347
- GRPO paper: https://arxiv.org/abs/2402.03300
- TRL docs: https://huggingface.co/docs/trl/




### Sft Training

# SFT Training Guide

Complete guide to Supervised Fine-Tuning (SFT) with TRL for instruction tuning and task-specific fine-tuning.

## Overview

SFT trains models on input-output pairs to minimize cross-entropy loss. Use for:
- Instruction following
- Task-specific fine-tuning
- Chatbot training
- Domain adaptation

## Dataset Formats

### Format 1: Prompt-Completion

```json
[
  {
    "prompt": "What is the capital of France?",
    "completion": "The capital of France is Paris."
  }
]
```

### Format 2: Conversational (ChatML)

```json
[
  {
    "messages": [
      {"role": "user", "content": "What is Python?"},
      {"role": "assistant", "content": "Python is a programming language."}
    ]
  }
]
```

### Format 3: Text-only

```json
[
  {"text": "User: Hello\nAssistant: Hi! How can I help?"}
]
```

## Basic Training

```python
from trl import SFTTrainer, SFTConfig
from transformers import AutoModelForCausalLM, AutoTokenizer
from datasets import load_dataset

# Load model
model = AutoModelForCausalLM.from_pretrained("Qwen/Qwen2.5-0.5B")
tokenizer = AutoTokenizer.from_pretrained("Qwen/Qwen2.5-0.5B")

# Load dataset
dataset = load_dataset("trl-lib/Capybara", split="train")

# Configure
config = SFTConfig(
    output_dir="Qwen2.5-SFT",
    per_device_train_batch_size=4,
    num_train_epochs=1,
    learning_rate=2e-5,
    save_strategy="epoch"
)

# Train
trainer = SFTTrainer(
    model=model,
    args=config,
    train_dataset=dataset,
    tokenizer=tokenizer
)
trainer.train()
```

## Chat Templates

Apply chat templates automatically:

```python
trainer = SFTTrainer(
    model=model,
    args=config,
    train_dataset=dataset,  # Messages format
    tokenizer=tokenizer
    # Chat template applied automatically
)
```

Or manually:
```python
def format_chat(example):
    messages = example["messages"]
    text = tokenizer.apply_chat_template(messages, tokenize=False)
    return {"text": text}

dataset = dataset.map(format_chat)
```

## Packing for Efficiency

Pack multiple sequences into one to maximize GPU utilization:

```python
config = SFTConfig(
    packing=True,  # Enable packing
    max_seq_length=2048,
    dataset_text_field="text"
)
```

**Benefits**: 2-3× faster training
**Trade-off**: Slightly more complex batching

## Multi-GPU Training

```bash
accelerate launch --num_processes 4 train_sft.py
```

Or with config:
```python
config = SFTConfig(
    output_dir="model-sft",
    per_device_train_batch_size=4,
    gradient_accumulation_steps=4,
    num_train_epochs=1
)
```

## LoRA Fine-Tuning

```python
from peft import LoraConfig

lora_config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules="all-linear",
    lora_dropout=0.05,
    task_type="CAUSAL_LM"
)

trainer = SFTTrainer(
    model=model,
    args=config,
    train_dataset=dataset,
    peft_config=lora_config  # Add LoRA
)
```

## Hyperparameters

| Model Size | Learning Rate | Batch Size | Epochs |
|------------|---------------|------------|--------|
| <1B | 5e-5 | 8-16 | 1-3 |
| 1-7B | 2e-5 | 4-8 | 1-2 |
| 7-13B | 1e-5 | 2-4 | 1 |
| 13B+ | 5e-6 | 1-2 | 1 |

## References

- TRL docs: https://huggingface.co/docs/trl/sft_trainer
- Examples: https://github.com/huggingface/trl/tree/main/examples/scripts




---

## 🚀 Usage

**Reference this template:** `@skill-fine-tuning-with-trl.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
