---
id: skill-knowledge-distillation
type: skill
name: knowledge-distillation
description: Compress large language models using knowledge distillation from teacher
  to student models. Use when deploying smaller models with retained performance,
  transferring GPT-4 capabilities to open-source models, or reducing inference costs.
  Covers temperature scaling, soft targets, reverse KLD, logit distillation, and MiniLLM
  training strategies.
category: ai-research
complexity: medium
keywords:
- deployment
- git
- github
- performance
- python
capabilities: []
token_estimate: 1739
has_references: true
reference_count: 1
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,739 -->
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




# knowledge-distillation

> Compress large language models using knowledge distillation from teacher to student models. Use when deploying smaller models with retained performance, transferring GPT-4 capabilities to open-source models, or reducing inference costs. Covers temperature scaling, soft targets, reverse KLD, logit distillation, and MiniLLM training strategies.

# Knowledge Distillation: Compressing LLMs

## When to Use This Skill

Use Knowledge Distillation when you need to:
- **Compress models** from 70B → 7B while retaining 90%+ performance
- **Transfer capabilities** from proprietary models (GPT-4) to open-source (LLaMA, Mistral)
- **Reduce inference costs** by deploying smaller student models
- **Create specialized models** by distilling domain-specific knowledge
- **Improve small models** using synthetic data from large teachers

**Key Techniques**: Temperature scaling, soft targets, reverse KLD (MiniLLM), logit distillation, response distillation

**Papers**: Hinton et al. 2015 (arXiv 1503.02531), MiniLLM (arXiv 2306.08543), KD Survey (arXiv 2402.13116)

## Installation

```bash
# Standard transformers
pip install transformers datasets accelerate

# For training
pip install torch deepspeed wandb

# Optional: MiniLLM implementation
git clone https://github.com/microsoft/LMOps
cd LMOps/minillm
pip install -e .
```

## Quick Start

### Basic Knowledge Distillation

```python
import torch
import torch.nn.functional as F
from transformers import AutoModelForCausalLM, AutoTokenizer, Trainer, TrainingArguments

# 1. Load teacher (large) and student (small) models
teacher = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-70b-hf",  # Large teacher
    torch_dtype=torch.float16,
    device_map="auto"
)

student = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-hf",  # Small student
    torch_dtype=torch.float16,
    device_map="cuda:0"
)

tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-2-70b-hf")

# 2. Define distillation loss
def distillation_loss(student_logits, teacher_logits, labels, temperature=2.0, alpha=0.5):
    """
    Combine hard loss (cross-entropy) with soft loss (KL divergence).

    Args:
        temperature: Softens probability distributions (higher = softer)
        alpha: Weight for distillation loss (1-alpha for hard loss)
    """
    # Hard loss: Standard cross-entropy with true labels
    hard_loss = F.cross_entropy(student_logits.view(-1, student_logits.size(-1)), labels.view(-1))

    # Soft loss: KL divergence between student and teacher
    soft_targets = F.softmax(teacher_logits / temperature, dim=-1)
    soft_student = F.log_softmax(student_logits / temperature, dim=-1)
    soft_loss = F.kl_div(soft_student, soft_targets, reduction='batchmean') * (temperature ** 2)

    # Combined loss
    return alpha * soft_loss + (1 - alpha) * hard_loss

# 3. Training loop
for batch in dataloader:
    # Teacher forward (no grad)
    with torch.no_grad():
        teacher_outputs = teacher(**batch)
        teacher_logits = teacher_outputs.logits

    # Student forward
    student_outputs = student(**batch)
    student_logits = student_outputs.logits

    # Compute distillation loss
    loss = distillation_loss(
        student_logits,
        teacher_logits,
        batch['labels'],
        temperature=2.0,
        alpha=0.7  # 70% soft, 30% hard
    )

    # Backward and optimize
    loss.backward()
    optimizer.step()
    optimizer.zero_grad()
```

### MiniLLM (Reverse KLD)

**Source**: arXiv 2306.08543 (2024)

**Innovation**: Use reverse KLD instead of forward KLD for better generative model distillation.

```python
def reverse_kl_loss(student_logits, teacher_logits, temperature=1.0):
    """
    Reverse KL divergence: KL(Teacher || Student)
    Better for generative models than forward KL.
    """
    # Teacher distribution (target)
    p_teacher = F.softmax(teacher_logits / temperature, dim=-1)

    # Student distribution (model)
    log_p_student = F.log_softmax(student_logits / temperature, dim=-1)

    # Reverse KL: Sum over teacher, student learns to cover teacher's modes
    reverse_kl = -(p_teacher * log_p_student).sum(dim=-1).mean()

    return reverse_kl * (temperature ** 2)

# Training with MiniLLM
for batch in dataloader:
    with torch.no_grad():
        teacher_logits = teacher(**batch).logits

    student_logits = student(**batch).logits

    # Reverse KLD (better for generation)
    loss = reverse_kl_loss(student_logits, teacher_logits, temperature=1.0)

    loss.backward()
    optimizer.step()
```

**Why reverse KL?**
- **Forward KL** (standard): Student learns to match teacher's *mean*
- **Reverse KL** (MiniLLM): Student learns to *cover* all teacher's modes
- Better for diverse text generation

### Response Distillation

```python
# Generate synthetic data from teacher, train student to imitate

# 1. Generate synthetic responses from teacher
prompts = ["Explain AI:", "What is ML?", "Define NLP:"]

teacher_responses = []
for prompt in prompts:
    inputs = tokenizer(prompt, return_tensors='pt').to(teacher.device)
    outputs = teacher.generate(**inputs, max_new_tokens=256, do_sample=True, temperature=0.7)
    response = tokenizer.decode(outputs[0], skip_special_tokens=True)
    teacher_responses.append(response)

# 2. Train student on teacher's responses (standard fine-tuning)
train_dataset = [
    {"text": f"{prompt}\n{response}"}
    for prompt, response in zip(prompts, teacher_responses)
]

# 3. Fine-tune student
trainer = Trainer(
    model=student,
    args=TrainingArguments(output_dir="./student", num_train_epochs=3, learning_rate=2e-5),
    train_dataset=train_dataset,
)
trainer.train()
```

## Core Concepts

### 1. Temperature Scaling

**Purpose**: Soften probability distributions to expose teacher's uncertainty.

```python
# Low temperature (T=1): Sharp distribution
logits = [3.0, 2.0, 1.0]
probs_T1 = softmax(logits / 1.0)  # [0.67, 0.24, 0.09]

# High temperature (T=4): Soft distribution
probs_T4 = softmax(logits / 4.0)  # [0.42, 0.34, 0.24]

# Higher T reveals more information about relative rankings
```

**Rule**: Use T=2-5 for distillation (2 is common default).

### 2. Loss Function Components

```python
# Total loss = alpha * soft_loss + (1 - alpha) * hard_loss

# Soft loss: Learn from teacher's knowledge
soft_loss = KL(student || teacher)

# Hard loss: Learn from ground truth labels
hard_loss = CrossEntropy(student_output, true_labels)

# Typical values:
alpha = 0.5  # Balanced
alpha = 0.7  # More emphasis on teacher
alpha = 0.3  # More emphasis on labels
```

### 3. Forward vs Reverse KLD

```python
# Forward KL: KL(Student || Teacher)
# - Student matches teacher's average behavior
# - Mode-seeking: Student focuses on teacher's highest probability modes
# - Good for classification

# Reverse KL: KL(Teacher || Student)
# - Student covers all of teacher's behaviors
# - Mode-covering: Student learns diverse behaviors
# - Good for generation (MiniLLM)
```

## Training Strategies

### Strategy 1: Logit Distillation

```python
# Train student to match teacher's logits directly

def logit_distillation_trainer(student, teacher, dataloader, temperature=2.0):
    optimizer = torch.optim.AdamW(student.parameters(), lr=2e-5)

    for epoch in range(3):
        for batch in dataloader:
            # Get logits
            with torch.no_grad():
                teacher_logits = teacher(**batch).logits

            student_logits = student(**batch).logits

            # MSE on logits (alternative to KLD)
            loss = F.mse_loss(student_logits, teacher_logits)

            # Or use KLD
            # loss = F.kl_div(
            #     F.log_softmax(student_logits/temperature, dim=-1),
            #     F.softmax(teacher_logits/temperature, dim=-1),
            #     reduction='batchmean'
            # ) * (temperature ** 2)

            loss.backward()
            optimizer.step()
            optimizer.zero_grad()

    return student
```

### Strategy 2: Two-Stage Distillation

```python
# Stage 1: Distill from teacher
student = distill(teacher, student, epochs=5)

# Stage 2: Fine-tune on task-specific data
student = fine_tune(student, task_data, epochs=3)

# Results in better task performance than single-stage
```

### Strategy 3: Multi-Teacher Distillation

```python
# Learn from multiple expert teachers

def multi_teacher_distillation(student, teachers, batch):
    """Distill from ensemble of teachers."""
    teacher_logits_list = []

    # Get logits from all teachers
    with torch.no_grad():
        for teacher in teachers:
            logits = teacher(**batch).logits
            teacher_logits_list.append(logits)

    # Average teacher predictions
    avg_teacher_logits = torch.stack(teacher_logits_list).mean(dim=0)

    # Student learns from ensemble
    student_logits = student(**batch).logits
    loss = F.kl_div(
        F.log_softmax(student_logits, dim=-1),
        F.softmax(avg_teacher_logits, dim=-1),
        reduction='batchmean'
    )

    return loss
```

## Production Deployment

### Complete Training Script

```python
from transformers import Trainer, TrainingArguments, DataCollatorForLanguageModeling

def train_distilled_model(
    teacher_name="meta-llama/Llama-2-70b-hf",
    student_name="meta-llama/Llama-2-7b-hf",
    output_dir="./distilled-llama-7b",
    temperature=2.0,
    alpha=0.7,
):
    # Load models
    teacher = AutoModelForCausalLM.from_pretrained(teacher_name, torch_dtype=torch.float16, device_map="auto")
    student = AutoModelForCausalLM.from_pretrained(student_name, torch_dtype=torch.float16)
    tokenizer = AutoTokenizer.from_pretrained(teacher_name)

    # Custom trainer with distillation
    class DistillationTrainer(Trainer):
        def compute_loss(self, model, inputs, return_outputs=False):
            # Student forward
            outputs_student = model(**inputs)
            student_logits = outputs_student.logits

            # Teacher forward (no grad)
            with torch.no_grad():
                outputs_teacher = teacher(**inputs)
                teacher_logits = outputs_teacher.logits

            # Distillation loss
            soft_targets = F.softmax(teacher_logits / temperature, dim=-1)
            soft_student = F.log_softmax(student_logits / temperature, dim=-1)
            soft_loss = F.kl_div(soft_student, soft_targets, reduction='batchmean') * (temperature ** 2)

            # Hard loss
            hard_loss = outputs_student.loss

            # Combined
            loss = alpha * soft_loss + (1 - alpha) * hard_loss

            return (loss, outputs_student) if return_outputs else loss

    # Training arguments
    training_args = TrainingArguments(
        output_dir=output_dir,
        num_train_epochs=3,
        per_device_train_batch_size=4,
        gradient_accumulation_steps=8,
        learning_rate=2e-5,
        warmup_steps=500,
        logging_steps=100,
        save_steps=1000,
        bf16=True,
        gradient_checkpointing=True,
    )

    # Train
    trainer = DistillationTrainer(
        model=student,
        args=training_args,
        train_dataset=train_dataset,
        data_collator=DataCollatorForLanguageModeling(tokenizer, mlm=False),
    )

    trainer.train()
    student.save_pretrained(output_dir)
    tokenizer.save_pretrained(output_dir)

# Usage
train_distilled_model(
    teacher_name="meta-llama/Llama-2-70b-hf",
    student_name="meta-llama/Llama-2-7b-hf",
    temperature=2.0,
    alpha=0.7
)
```

## Best Practices

### 1. Hyperparameter Selection

```python
# Temperature
T = 1.0  # Sharp (less knowledge transfer)
T = 2.0  # Standard (good balance)
T = 5.0  # Soft (more knowledge transfer)

# Alpha (weight)
alpha = 0.5  # Balanced
alpha = 0.7  # Emphasize teacher knowledge
alpha = 0.9  # Strong distillation

# Rule: Higher T + higher alpha = stronger distillation
```

### 2. Model Size Ratio

```python
# Good ratios (teacher/student)
70B / 7B = 10×    # Excellent
13B / 1B = 13×    # Good
7B / 1B = 7×      # Acceptable

# Avoid too large gap
70B / 1B = 70×    # Too large, ineffective
```

### 3. Data Quality

```python
# Best: Use teacher-generated data + real data
train_data = {
    "teacher_generated": 70%,  # Diverse, high-quality
    "real_data": 30%            # Ground truth
}

# Avoid: Only real data (doesn't utilize teacher fully)
```

## Evaluation

```python
from transformers import pipeline

# Compare student vs teacher
teacher_pipe = pipeline("text-generation", model=teacher)
student_pipe = pipeline("text-generation", model=student)

prompts = ["Explain quantum computing:", "What is AI?"]

for prompt in prompts:
    teacher_out = teacher_pipe(prompt, max_new_tokens=100)
    student_out = student_pipe(prompt, max_new_tokens=100)

    print(f"Prompt: {prompt}")
    print(f"Teacher: {teacher_out[0]['generated_text']}")
    print(f"Student: {student_out[0]['generated_text']}")
    print(f"Match quality: {calculate_similarity(teacher_out, student_out):.2f}")
```

## Resources

- **Hinton et al. 2015 (Foundational)**: https://arxiv.org/abs/1503.02531
- **MiniLLM (Reverse KLD)**: https://arxiv.org/abs/2306.08543
- **KD Survey for LLMs (2024)**: https://arxiv.org/abs/2402.13116
- **MiniLLM GitHub**: https://github.com/microsoft/LMOps/tree/main/minillm




---


## 📚 Reference Materials


### Minillm

# MiniLLM: Reverse KL Divergence for LLM Distillation

Based on arXiv 2306.08543 (2024) - MiniLLM: Knowledge Distillation of Large Language Models

## Overview

**Source**: https://arxiv.org/abs/2306.08543
**GitHub**: https://github.com/microsoft/LMOps/tree/main/minillm

MiniLLM replaces forward KLD with reverse KLD for knowledge distillation, achieving better performance on generative language models.

## Problem with Standard KLD

### Forward KL Divergence (Standard)

**Formula**: `KL(Student || Teacher)`

**Minimization behavior**: Mode-seeking
```
Student tries to match teacher's MEAN behavior
→ Student focuses on teacher's highest probability regions
→ Student ignores low-probability but valid generations
```

**Issue for generative models**: Limits diversity, student generates safe but boring outputs.

### Why Forward KL Fails for Generation

```python
# Teacher distribution (diverse)
teacher_probs = [0.3, 0.3, 0.2, 0.1, 0.1]  # Multiple valid options

# Forward KL minimization
# Student learns: [0.6, 0.3, 0.1, 0.0, 0.0]
# Problem: Ignores options 4-5 entirely (mode-seeking)
```

## MiniLLM Solution: Reverse KLD

### Reverse KL Divergence

**Formula**: `KL(Teacher || Student)`

**Minimization behavior**: Mode-covering
```
Student tries to COVER all teacher's modes
→ Student learns diverse generation
→ Student doesn't ignore any valid teacher outputs
```

### Mathematical Formulation

**Forward KL** (standard distillation):
```
L_forward = Σ p_student(x) log(p_student(x) / p_teacher(x))
          = E_{x~student} [log p_student(x) - log p_teacher(x)]
```

**Reverse KL** (MiniLLM):
```
L_reverse = Σ p_teacher(x) log(p_teacher(x) / p_student(x))
          = E_{x~teacher} [log p_teacher(x) - log p_student(x)]
```

**Key difference**: Expectation over teacher distribution vs student distribution.

## Implementation

### Reverse KLD Loss

```python
import torch
import torch.nn.functional as F

def reverse_kl_loss(student_logits, teacher_logits, temperature=1.0):
    """
    Reverse KL divergence: KL(Teacher || Student).

    Args:
        student_logits: Model predictions (batch, seq_len, vocab_size)
        teacher_logits: Teacher predictions (batch, seq_len, vocab_size)
        temperature: Softening parameter

    Returns:
        Reverse KL divergence loss
    """
    # Teacher distribution (target, detached)
    p_teacher = F.softmax(teacher_logits / temperature, dim=-1)
    p_teacher = p_teacher.detach()  # Don't backprop through teacher

    # Student distribution (learnable)
    log_p_student = F.log_softmax(student_logits / temperature, dim=-1)

    # Reverse KL: -Σ p_teacher * log p_student
    reverse_kl = -(p_teacher * log_p_student).sum(dim=-1).mean()

    # Temperature correction
    return reverse_kl * (temperature ** 2)
```

### Policy Gradient Optimization

**Challenge**: Reverse KL requires sampling from teacher.

**Solution**: Use policy gradient with teacher samples.

```python
def minillm_policy_gradient(student_model, teacher_model, prompt_batch):
    """
    MiniLLM training with policy gradient.

    Steps:
    1. Sample responses from teacher
    2. Compute reverse KL using those samples
    3. Optimize student to cover teacher's distribution
    """
    # 1. Generate from teacher (detached)
    with torch.no_grad():
        teacher_outputs = teacher_model.generate(
            prompt_batch,
            max_new_tokens=256,
            do_sample=True,
            temperature=1.0,
            return_dict_in_generate=True,
            output_scores=True
        )

        teacher_sequences = teacher_outputs.sequences
        teacher_scores = teacher_outputs.scores

    # 2. Student evaluates teacher's samples
    student_outputs = student_model(
        input_ids=teacher_sequences,
        labels=teacher_sequences
    )

    # 3. Policy gradient loss
    # Maximize student's likelihood on teacher's samples
    loss = -student_outputs.logits.mean()

    return loss
```

## Training Procedure

### Two-Stage MiniLLM

**Stage 1**: Imitation learning (reverse KLD)
```python
# Learn to generate like teacher
for epoch in range(num_imitation_epochs):
    for batch in dataloader:
        # Sample from teacher
        teacher_samples = teacher.generate(batch['prompts'])

        # Student imitates
        loss = reverse_kl_loss(
            student(teacher_samples).logits,
            teacher(teacher_samples).logits
        )

        loss.backward()
        optimizer.step()
```

**Stage 2**: Self-training (optional)
```python
# Fine-tune on student's own generations
for epoch in range(num_self_train_epochs):
    for batch in dataloader:
        # Student generates
        student_samples = student.generate(batch['prompts'])

        # Self-training loss
        loss = student(student_samples).loss

        loss.backward()
        optimizer.step()
```

### Complete Training Script

```python
from transformers import AutoModelForCausalLM, Trainer, TrainingArguments

def train_minillm(
    teacher_name="meta-llama/Llama-2-70b-hf",
    student_name="meta-llama/Llama-2-7b-hf",
    output_dir="./minillm-7b",
):
    # Load models
    teacher = AutoModelForCausalLM.from_pretrained(teacher_name, torch_dtype=torch.float16, device_map="auto")
    student = AutoModelForCausalLM.from_pretrained(student_name, torch_dtype=torch.float16)

    # Custom trainer with reverse KLD
    class MiniLLMTrainer(Trainer):
        def compute_loss(self, model, inputs, return_outputs=False):
            # Generate from teacher
            with torch.no_grad():
                teacher_outputs = teacher.generate(
                    inputs['input_ids'],
                    max_new_tokens=256,
                    do_sample=True,
                    return_dict_in_generate=True,
                    output_scores=True
                )

                teacher_sequences = teacher_outputs.sequences
                teacher_logits = torch.stack(teacher_outputs.scores, dim=1)

            # Student evaluates teacher samples
            student_outputs = model(
                input_ids=teacher_sequences,
                labels=teacher_sequences
            )

            student_logits = student_outputs.logits

            # Reverse KL loss
            loss = reverse_kl_loss(student_logits, teacher_logits)

            return (loss, student_outputs) if return_outputs else loss

    # Training arguments
    training_args = TrainingArguments(
        output_dir=output_dir,
        num_train_epochs=5,
        per_device_train_batch_size=2,
        gradient_accumulation_steps=16,
        learning_rate=5e-5,
        warmup_steps=1000,
        logging_steps=100,
        save_steps=1000,
        bf16=True,
    )

    # Train
    trainer = MiniLLMTrainer(
        model=student,
        args=training_args,
        train_dataset=train_dataset,
    )

    trainer.train()
    student.save_pretrained(output_dir)

# Usage
train_minillm(
    teacher_name="meta-llama/Llama-2-70b-hf",
    student_name="meta-llama/Llama-2-7b-hf",
)
```

## Performance Results

**From paper (LLaMA models)**:

| Student | Teacher | Method | MT-Bench Score | AlpacaEval |
|---------|---------|--------|----------------|------------|
| LLaMA-7B | - | Baseline | 5.2 | 55% |
| LLaMA-7B | LLaMA-70B | Forward KL | 5.8 | 62% |
| LLaMA-7B | LLaMA-70B | **MiniLLM (Reverse KL)** | **6.4** | **71%** |

**Key findings**:
- Reverse KL outperforms forward KL by ~10%
- Distilled 7B model approaches 70B performance
- Better diversity and generation quality

## Comparison: Forward vs Reverse KL

### Generation Quality

```python
# Prompt: "Explain quantum computing"

# Forward KL (mode-seeking)
# Student output: "Quantum computing uses quantum bits..."
# → Safe, generic, one mode

# Reverse KL (mode-covering)
# Student output: Multiple diverse valid explanations
# → Covers different valid explanations
# → More creative, diverse
```

### When to Use Each

**Forward KL**:
- Classification tasks
- Single correct answer
- Need deterministic output

**Reverse KL (MiniLLM)**:
- Generative tasks
- Multiple valid outputs
- Need diversity
- Open-ended generation

## Hyperparameters

### Temperature

```python
# Temperature for both teacher and student

T = 1.0  # Standard (from paper)
T = 0.8  # Sharper (less diversity)
T = 1.2  # Softer (more diversity)

# Rule: Use T=1.0 for MiniLLM (higher temps help mode-covering)
```

### Learning Rate

```python
# MiniLLM uses higher LR than standard distillation

lr_forward_kl = 2e-5   # Standard distillation
lr_minillm = 5e-5      # MiniLLM (can handle higher LR)

# Reason: Reverse KL has better gradient properties
```

## Limitations

1. **Computational cost**: Requires sampling from teacher during training
2. **Implementation complexity**: More complex than standard distillation
3. **Memory**: Need to store teacher samples

## Resources

- **Paper**: https://arxiv.org/abs/2306.08543
- **GitHub**: https://github.com/microsoft/LMOps/tree/main/minillm
- **Blog**: https://www.microsoft.com/en-us/research/blog/minillm-small-language-models-via-large-language-model-distillation/




---

## 🚀 Usage

**Reference this template:** `@skill-knowledge-distillation.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
