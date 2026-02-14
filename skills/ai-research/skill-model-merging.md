---
id: skill-model-merging
type: skill
name: model-merging
description: Merge multiple fine-tuned models using mergekit to combine capabilities
  without retraining. Use when creating specialized models by blending domain-specific
  expertise (math + coding + chat), improving performance beyond single models, or
  experimenting rapidly with model variants. Covers SLERP, TIES-Merging, DARE, Task
  Arithmetic, linear merging, and production deployment strategies.
category: ai-research
complexity: medium
keywords:
- benchmark
- deploy
- deployment
- git
- github
- performance
- python
- test
- testing
capabilities: []
token_estimate: 1849
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,849 -->
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




# model-merging

> Merge multiple fine-tuned models using mergekit to combine capabilities without retraining. Use when creating specialized models by blending domain-specific expertise (math + coding + chat), improving performance beyond single models, or experimenting rapidly with model variants. Covers SLERP, TIES-Merging, DARE, Task Arithmetic, linear merging, and production deployment strategies.

# Model Merging: Combining Pre-trained Models

## When to Use This Skill

Use Model Merging when you need to:
- **Combine capabilities** from multiple fine-tuned models without retraining
- **Create specialized models** by blending domain-specific expertise (math + coding + chat)
- **Improve performance** beyond single models (often +5-10% on benchmarks)
- **Reduce training costs** - no GPUs needed, merges run on CPU
- **Experiment rapidly** - create new model variants in minutes, not days
- **Preserve multiple skills** - merge without catastrophic forgetting

**Success Stories**: Marcoro14-7B-slerp (best on Open LLM Leaderboard 02/2024), many top HuggingFace models use merging

**Tools**: mergekit (Arcee AI), LazyMergekit, Model Soup

## Installation

```bash
# Install mergekit
git clone https://github.com/arcee-ai/mergekit.git
cd mergekit
pip install -e .

# Or via pip
pip install mergekit

# Optional: Transformer library
pip install transformers torch
```

## Quick Start

### Simple Linear Merge

```yaml
# config.yml - Merge two models with equal weights
merge_method: linear
models:
  - model: mistralai/Mistral-7B-v0.1
    parameters:
      weight: 0.5
  - model: teknium/OpenHermes-2.5-Mistral-7B
    parameters:
      weight: 0.5
dtype: bfloat16
```

```bash
# Run merge
mergekit-yaml config.yml ./merged-model --cuda

# Use merged model
python -m transformers.models.auto --model_name_or_path ./merged-model
```

### SLERP Merge (Best for 2 Models)

```yaml
# config.yml - Spherical interpolation
merge_method: slerp
slices:
  - sources:
      - model: mistralai/Mistral-7B-v0.1
        layer_range: [0, 32]
      - model: teknium/OpenHermes-2.5-Mistral-7B
        layer_range: [0, 32]
parameters:
  t: 0.5  # Interpolation factor (0=model1, 1=model2)
dtype: bfloat16
```

## Core Concepts

### 1. Merge Methods

**Linear (Model Soup)**
- Simple weighted average of parameters
- Fast, works well for similar models
- Can merge 2+ models

```python
merged_weights = w1 * model1_weights + w2 * model2_weights + w3 * model3_weights
# where w1 + w2 + w3 = 1
```

**SLERP (Spherical Linear Interpolation)**
- Interpolates along sphere in weight space
- Preserves magnitude of weight vectors
- Best for merging 2 models
- Smoother than linear

```python
# SLERP formula
merged = (sin((1-t)*θ) / sin(θ)) * model1 + (sin(t*θ) / sin(θ)) * model2
# where θ = arccos(dot(model1, model2))
# t ∈ [0, 1]
```

**Task Arithmetic**
- Extract "task vectors" (fine-tuned - base)
- Combine task vectors, add to base
- Good for merging multiple specialized models

```python
# Task vector
task_vector = finetuned_model - base_model

# Merge multiple task vectors
merged = base_model + α₁*task_vector₁ + α₂*task_vector₂
```

**TIES-Merging**
- Task arithmetic + sparsification
- Resolves sign conflicts in parameters
- Best for merging many task-specific models

**DARE (Drop And REscale)**
- Randomly drops fine-tuned parameters
- Rescales remaining parameters
- Reduces redundancy, maintains performance

### 2. Configuration Structure

```yaml
# Basic structure
merge_method: <method>  # linear, slerp, ties, dare_ties, task_arithmetic
base_model: <path>      # Optional: base model for task arithmetic

models:
  - model: <path/to/model1>
    parameters:
      weight: <float>   # Merge weight
      density: <float>  # For TIES/DARE

  - model: <path/to/model2>
    parameters:
      weight: <float>

parameters:
  # Method-specific parameters

dtype: <dtype>  # bfloat16, float16, float32

# Optional
slices:  # Layer-wise merging
tokenizer:  # Tokenizer configuration
```

## Merge Methods Guide

### Linear Merge

**Best for**: Simple model combinations, equal weighting

```yaml
merge_method: linear
models:
  - model: WizardLM/WizardMath-7B-V1.1
    parameters:
      weight: 0.4
  - model: teknium/OpenHermes-2.5-Mistral-7B
    parameters:
      weight: 0.3
  - model: NousResearch/Nous-Hermes-2-Mistral-7B-DPO
    parameters:
      weight: 0.3
dtype: bfloat16
```

### SLERP Merge

**Best for**: Two models, smooth interpolation

```yaml
merge_method: slerp
slices:
  - sources:
      - model: mistralai/Mistral-7B-v0.1
        layer_range: [0, 32]
      - model: teknium/OpenHermes-2.5-Mistral-7B
        layer_range: [0, 32]
parameters:
  t: 0.5  # 0.0 = first model, 1.0 = second model
dtype: bfloat16
```

**Layer-specific SLERP:**

```yaml
merge_method: slerp
slices:
  - sources:
      - model: model_a
        layer_range: [0, 32]
      - model: model_b
        layer_range: [0, 32]
parameters:
  t:
    - filter: self_attn    # Attention layers
      value: 0.3
    - filter: mlp          # MLP layers
      value: 0.7
    - value: 0.5           # Default for other layers
dtype: bfloat16
```

### Task Arithmetic

**Best for**: Combining specialized skills

```yaml
merge_method: task_arithmetic
base_model: mistralai/Mistral-7B-v0.1
models:
  - model: WizardLM/WizardMath-7B-V1.1  # Math
    parameters:
      weight: 0.5
  - model: teknium/OpenHermes-2.5-Mistral-7B  # Chat
    parameters:
      weight: 0.3
  - model: ajibawa-2023/Code-Mistral-7B  # Code
    parameters:
      weight: 0.2
dtype: bfloat16
```

### TIES-Merging

**Best for**: Many models, resolving conflicts

```yaml
merge_method: ties
base_model: mistralai/Mistral-7B-v0.1
models:
  - model: WizardLM/WizardMath-7B-V1.1
    parameters:
      density: 0.5  # Keep top 50% of parameters
      weight: 1.0
  - model: teknium/OpenHermes-2.5-Mistral-7B
    parameters:
      density: 0.5
      weight: 1.0
  - model: NousResearch/Nous-Hermes-2-Mistral-7B-DPO
    parameters:
      density: 0.5
      weight: 1.0
parameters:
  normalize: true
dtype: bfloat16
```

### DARE Merge

**Best for**: Reducing redundancy

```yaml
merge_method: dare_ties
base_model: mistralai/Mistral-7B-v0.1
models:
  - model: WizardLM/WizardMath-7B-V1.1
    parameters:
      density: 0.5    # Drop 50% of deltas
      weight: 0.6
  - model: teknium/OpenHermes-2.5-Mistral-7B
    parameters:
      density: 0.5
      weight: 0.4
parameters:
  int8_mask: true  # Use int8 for masks (saves memory)
dtype: bfloat16
```

## Advanced Patterns

### Layer-wise Merging

```yaml
# Different models for different layers
merge_method: passthrough
slices:
  - sources:
      - model: mistralai/Mistral-7B-v0.1
        layer_range: [0, 16]   # First half
  - sources:
      - model: teknium/OpenHermes-2.5-Mistral-7B
        layer_range: [16, 32]  # Second half
dtype: bfloat16
```

### MoE from Merged Models

```yaml
# Create Mixture of Experts
merge_method: moe
base_model: mistralai/Mistral-7B-v0.1
experts:
  - source_model: WizardLM/WizardMath-7B-V1.1
    positive_prompts:
      - "math"
      - "calculate"
  - source_model: teknium/OpenHermes-2.5-Mistral-7B
    positive_prompts:
      - "chat"
      - "conversation"
  - source_model: ajibawa-2023/Code-Mistral-7B
    positive_prompts:
      - "code"
      - "python"
dtype: bfloat16
```

### Tokenizer Merging

```yaml
merge_method: linear
models:
  - model: mistralai/Mistral-7B-v0.1
  - model: custom/specialized-model

tokenizer:
  source: "union"  # Combine vocabularies from both models
  tokens:
    <|special_token|>:
      source: "custom/specialized-model"
```

## Best Practices

### 1. Model Compatibility

```python
# ✅ Good: Same architecture
models = [
    "mistralai/Mistral-7B-v0.1",
    "teknium/OpenHermes-2.5-Mistral-7B",  # Both Mistral 7B
]

# ❌ Bad: Different architectures
models = [
    "meta-llama/Llama-2-7b-hf",  # Llama
    "mistralai/Mistral-7B-v0.1",  # Mistral (incompatible!)
]
```

### 2. Weight Selection

```yaml
# ✅ Good: Weights sum to 1.0
models:
  - model: model_a
    parameters:
      weight: 0.6
  - model: model_b
    parameters:
      weight: 0.4  # 0.6 + 0.4 = 1.0

# ⚠️  Acceptable: Weights don't sum to 1 (for task arithmetic)
models:
  - model: model_a
    parameters:
      weight: 0.8
  - model: model_b
    parameters:
      weight: 0.8  # May boost performance
```

### 3. Method Selection

```python
# Choose merge method based on use case:

# 2 models, smooth blend → SLERP
merge_method = "slerp"

# 3+ models, simple average → Linear
merge_method = "linear"

# Multiple task-specific models → Task Arithmetic or TIES
merge_method = "ties"

# Want to reduce redundancy → DARE
merge_method = "dare_ties"
```

### 4. Density Tuning (TIES/DARE)

```yaml
# Start conservative (keep more parameters)
parameters:
  density: 0.8  # Keep 80%

# If performance good, increase sparsity
parameters:
  density: 0.5  # Keep 50%

# If performance degrades, reduce sparsity
parameters:
  density: 0.9  # Keep 90%
```

### 5. Layer-specific Merging

```yaml
# Preserve base model's beginning and end
merge_method: passthrough
slices:
  - sources:
      - model: base_model
        layer_range: [0, 2]     # Keep first layers
  - sources:
      - model: merged_middle    # Merge middle layers
        layer_range: [2, 30]
  - sources:
      - model: base_model
        layer_range: [30, 32]   # Keep last layers
```

## Evaluation & Testing

### Benchmark Merged Models

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load merged model
model = AutoModelForCausalLM.from_pretrained("./merged-model")
tokenizer = AutoTokenizer.from_pretrained("./merged-model")

# Test on various tasks
test_prompts = {
    "math": "Calculate: 25 * 17 =",
    "code": "Write a Python function to reverse a string:",
    "chat": "What is the capital of France?",
}

for task, prompt in test_prompts.items():
    inputs = tokenizer(prompt, return_tensors="pt")
    outputs = model.generate(**inputs, max_length=100)
    print(f"{task}: {tokenizer.decode(outputs[0])}")
```

### Common Benchmarks

- **Open LLM Leaderboard**: General capabilities
- **MT-Bench**: Multi-turn conversation
- **MMLU**: Multitask accuracy
- **HumanEval**: Code generation
- **GSM8K**: Math reasoning

## Production Deployment

### Save and Upload

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

# Load merged model
model = AutoModelForCausalLM.from_pretrained("./merged-model")
tokenizer = AutoTokenizer.from_pretrained("./merged-model")

# Upload to HuggingFace Hub
model.push_to_hub("username/my-merged-model")
tokenizer.push_to_hub("username/my-merged-model")
```

### Quantize Merged Model

```bash
# Quantize with GGUF
python convert.py ./merged-model --outtype f16 --outfile merged-model.gguf

# Quantize with GPTQ
python quantize_gptq.py ./merged-model --bits 4 --group_size 128
```

## Common Pitfalls

### ❌ Pitfall 1: Merging Incompatible Models

```yaml
# Wrong: Different architectures
models:
  - model: meta-llama/Llama-2-7b  # Llama architecture
  - model: mistralai/Mistral-7B   # Mistral architecture
```

**Fix**: Only merge models with same architecture

### ❌ Pitfall 2: Over-weighting One Model

```yaml
# Suboptimal: One model dominates
models:
  - model: model_a
    parameters:
      weight: 0.95  # Too high
  - model: model_b
    parameters:
      weight: 0.05  # Too low
```

**Fix**: Use more balanced weights (0.3-0.7 range)

### ❌ Pitfall 3: Not Evaluating

```bash
# Wrong: Merge and deploy without testing
mergekit-yaml config.yml ./merged-model
# Deploy immediately (risky!)
```

**Fix**: Always benchmark before deploying

## Resources

- **mergekit GitHub**: https://github.com/arcee-ai/mergekit
- **HuggingFace Tutorial**: https://huggingface.co/blog/mlabonne/merge-models
- **LazyMergekit**: Automated merging notebook
- **TIES Paper**: https://arxiv.org/abs/2306.01708
- **DARE Paper**: https://arxiv.org/abs/2311.03099

## See Also

- `references/methods.md` - Deep dive into merge algorithms
- `references/examples.md` - Real-world merge configurations
- `references/evaluation.md` - Benchmarking and testing strategies




---


## 📚 Reference Materials


### Evaluation

# Model Merging Evaluation

Complete guide to benchmarking and testing merged models based on research best practices.

## Table of Contents
- Benchmark Suites
- Evaluation Metrics
- Testing Methodology
- Comparison Framework
- Quality Assurance

## Benchmark Suites

### Open LLM Leaderboard

**URL**: https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard

**Tasks** (6 benchmarks):
1. **ARC** (AI2 Reasoning Challenge): 25-shot, science questions
2. **HellaSwag**: 10-shot, commonsense reasoning
3. **MMLU** (Massive Multitask Language Understanding): 5-shot, 57 subjects
4. **TruthfulQA**: 0-shot, factual accuracy
5. **Winogrande**: 5-shot, commonsense reasoning
6. **GSM8K**: 5-shot, grade-school math

**Running Evaluation**:

```python
from lm_eval import evaluator

model = "path/to/merged/model"

results = evaluator.simple_evaluate(
    model="hf",
    model_args=f"pretrained={model},dtype=float16",
    tasks=[
        "arc_challenge",
        "hellaswag",
        "hendrycksTest-*",  # MMLU
        "truthfulqa_mc",
        "winogrande",
        "gsm8k"
    ],
    num_fewshot=5,
    batch_size=8
)

# Average score
avg_score = sum(results['results'].values()) / len(results['results'])
print(f"Average: {avg_score:.2f}")
```

### MT-Bench

**Focus**: Multi-turn conversation quality

**Installation**:

```bash
git clone https://github.com/lm-sys/FastChat
cd FastChat
pip install -e .
```

**Running**:

```bash
# Generate responses
python gen_model_answer.py \
  --model-path path/to/merged/model \
  --model-id merged_model

# Judge with GPT-4
python gen_judgment.py \
  --model-list merged_model \
  --judge-model gpt-4

# View scores
python show_result.py
```

**Metrics**:
- Turn 1 score (1-10)
- Turn 2 score (1-10)
- Average score

### MMLU (Detailed)

**Subjects** (57 total):
- STEM: Math, Physics, Chemistry, Biology, Computer Science
- Humanities: History, Philosophy, Law
- Social Sciences: Economics, Psychology, Sociology
- Other: Professional subjects (Medicine, Accounting, etc.)

```python
from lm_eval import evaluator

# Run all MMLU subjects
results = evaluator.simple_evaluate(
    model="hf",
    model_args=f"pretrained={model}",
    tasks="hendrycksTest-*",  # All MMLU tasks
    num_fewshot=5
)

# Subject breakdown
for task, score in results['results'].items():
    subject = task.replace('hendrycksTest-', '')
    print(f"{subject}: {score['acc']:.2%}")
```

### HumanEval (Code)

**Focus**: Python code generation

```python
from human_eval.data import write_jsonl, read_problems
from human_eval.evaluation import evaluate_functional_correctness

# Generate completions
problems = read_problems()
samples = []

for task_id, problem in problems.items():
    prompt = problem['prompt']
    completion = model.generate(prompt)
    samples.append({
        'task_id': task_id,
        'completion': completion
    })

write_jsonl("samples.jsonl", samples)

# Evaluate
results = evaluate_functional_correctness("samples.jsonl")
print(f"Pass@1: {results['pass@1']:.2%}")
```

## Evaluation Metrics

### Performance Metrics

**Accuracy**: Correct predictions / total predictions
```python
def accuracy(predictions, labels):
    correct = sum(p == l for p, l in zip(predictions, labels))
    return correct / len(predictions)
```

**Perplexity**: Language modeling quality (lower is better)
```python
import torch

def perplexity(model, text):
    tokens = tokenizer(text, return_tensors='pt')
    with torch.no_grad():
        loss = model(**tokens).loss
    return torch.exp(loss).item()
```

**BLEU Score**: Translation/generation quality
```python
from nltk.translate.bleu_score import sentence_bleu

reference = [["the", "cat", "sat", "on", "the", "mat"]]
candidate = ["the", "cat", "is", "on", "the", "mat"]

score = sentence_bleu(reference, candidate)
```

### Capability Retention

**Test**: Does merged model retain parent capabilities?

```python
def test_capability_retention(merged_model, parent_models, test_suite):
    """Check if merged model maintains parent capabilities."""
    results = {}

    # Baseline: Test parent models
    for i, parent in enumerate(parent_models):
        parent_score = evaluate(parent, test_suite)
        results[f'parent_{i}'] = parent_score

    # Test merged model
    merged_score = evaluate(merged_model, test_suite)
    results['merged'] = merged_score

    # Retention percentage
    avg_parent_score = sum(s for k, s in results.items() if k.startswith('parent')) / len(parent_models)
    retention = merged_score / avg_parent_score

    print(f"Capability Retention: {retention:.1%}")
    return retention >= 0.95  # 95% retention threshold
```

### Conflict Detection

**Test**: Does model show conflicting behaviors?

```python
def test_conflicts(model, test_pairs):
    """Test for contradictory outputs."""
    conflicts = []

    for question_a, question_b, expected_consistency in test_pairs:
        answer_a = model.generate(question_a)
        answer_b = model.generate(question_b)

        # Check consistency
        is_consistent = check_semantic_similarity(answer_a, answer_b)

        if is_consistent != expected_consistency:
            conflicts.append((question_a, question_b, answer_a, answer_b))

    conflict_rate = len(conflicts) / len(test_pairs)
    print(f"Conflict Rate: {conflict_rate:.1%}")

    return conflict_rate < 0.05  # <5% conflicts acceptable
```

## Testing Methodology

### Pre-Merge Testing

**Before merging**, establish baselines:

```python
# Test parent models
parent_1_scores = evaluate(parent_1, benchmark_suite)
parent_2_scores = evaluate(parent_2, benchmark_suite)

# Expected range for merged model
min_expected = min(parent_1_scores, parent_2_scores)
max_expected = max(parent_1_scores, parent_2_scores)

print(f"Expected merged score: {min_expected:.2f} - {max_expected:.2f}")
```

### Post-Merge Testing

**Comprehensive evaluation**:

```python
def comprehensive_eval(merged_model):
    """Full evaluation suite."""
    results = {}

    # 1. General capabilities
    results['open_llm'] = evaluate_open_llm(merged_model)

    # 2. Conversation
    results['mt_bench'] = evaluate_mt_bench(merged_model)

    # 3. Domain-specific
    results['math'] = evaluate_math(merged_model)  # GSM8K, MATH
    results['code'] = evaluate_code(merged_model)  # HumanEval
    results['reasoning'] = evaluate_reasoning(merged_model)  # ARC, HellaSwag

    # 4. Safety
    results['safety'] = evaluate_safety(merged_model)  # TruthfulQA

    return results
```

### A/B Testing

**Compare merged model vs parents**:

```python
def ab_test(model_a, model_b, test_prompts, n_users=100):
    """User preference testing."""
    preferences = {'a': 0, 'b': 0, 'tie': 0}

    for prompt in test_prompts:
        response_a = model_a.generate(prompt)
        response_b = model_b.generate(prompt)

        # Simulated user preference (or use GPT-4 as judge)
        preference = judge_responses(prompt, response_a, response_b)
        preferences[preference] += 1

    a_win_rate = preferences['a'] / (preferences['a'] + preferences['b'] + preferences['tie'])

    print(f"Model A Win Rate: {a_win_rate:.1%}")
    print(f"Tie Rate: {preferences['tie'] / len(test_prompts):.1%}")

    return a_win_rate
```

## Comparison Framework

### Score Comparison Table

```python
import pandas as pd

def compare_models(models, benchmarks):
    """Create comparison table."""
    results = {}

    for model_name, model_path in models.items():
        results[model_name] = {}

        for benchmark_name, benchmark_fn in benchmarks.items():
            score = benchmark_fn(model_path)
            results[model_name][benchmark_name] = score

    # Create DataFrame
    df = pd.DataFrame(results).T

    # Add average column
    df['Average'] = df.mean(axis=1)

    # Highlight best
    print(df.to_markdown())

    return df

# Usage
models = {
    'Parent 1': 'path/to/parent1',
    'Parent 2': 'path/to/parent2',
    'Merged (SLERP t=0.5)': 'path/to/merged_0.5',
    'Merged (TIES)': 'path/to/merged_ties'
}

benchmarks = {
    'MMLU': evaluate_mmlu,
    'ARC': evaluate_arc,
    'GSM8K': evaluate_gsm8k
}

df = compare_models(models, benchmarks)
```

### Statistical Significance

```python
from scipy import stats

def is_improvement_significant(scores_a, scores_b, alpha=0.05):
    """Test if improvement is statistically significant."""
    # Paired t-test
    t_stat, p_value = stats.ttest_rel(scores_a, scores_b)

    is_significant = p_value < alpha
    improvement = (sum(scores_b) - sum(scores_a)) / len(scores_a)

    print(f"Mean improvement: {improvement:.2f}")
    print(f"P-value: {p_value:.4f}")
    print(f"Significant: {is_significant}")

    return is_significant
```

## Quality Assurance

### Regression Testing

**Ensure no capability loss**:

```python
def regression_test(merged_model, parent_models, critical_tests):
    """Check for performance regressions."""
    regressions = []

    for test_name, test_fn in critical_tests.items():
        # Parent scores
        parent_scores = [test_fn(p) for p in parent_models]
        min_parent_score = min(parent_scores)

        # Merged score
        merged_score = test_fn(merged_model)

        # Regression if merged < min parent
        if merged_score < min_parent_score * 0.95:  # 5% tolerance
            regressions.append({
                'test': test_name,
                'parents': parent_scores,
                'merged': merged_score,
                'delta': merged_score - min_parent_score
            })

    if regressions:
        print(f"⚠️  {len(regressions)} regressions detected:")
        for r in regressions:
            print(f"  - {r['test']}: {r['delta']:.2%} drop")

    return len(regressions) == 0
```

### Sanity Checks

```python
def sanity_checks(model):
    """Basic functionality tests."""
    tests = {
        'generates': lambda: model.generate("Hello") != "",
        'coherent': lambda: len(model.generate("The capital of France is")) > 5,
        'follows_instruction': lambda: "paris" in model.generate("What is the capital of France?").lower(),
        'no_repetition': lambda: not has_repetition(model.generate("Tell me about AI", max_length=100))
    }

    results = {name: test() for name, test in tests.items()}

    passed = sum(results.values())
    total = len(results)

    print(f"Sanity Checks: {passed}/{total} passed")

    for name, result in results.items():
        status = "✓" if result else "✗"
        print(f"  {status} {name}")

    return passed == total
```

### Deployment Checklist

Before deploying merged model:

- [ ] Open LLM Leaderboard score >= min(parent scores)
- [ ] MT-Bench score >= avg(parent scores)
- [ ] Domain-specific benchmarks pass
- [ ] No regressions in critical tests
- [ ] Sanity checks all pass
- [ ] A/B test win rate >= 45%
- [ ] Safety checks pass (TruthfulQA)
- [ ] Manual testing with diverse prompts
- [ ] Model size acceptable for deployment
- [ ] Inference speed acceptable

## Benchmark Interpretation

### Open LLM Leaderboard Ranges

| Score | Quality |
|-------|---------|
| <60 | Poor - likely broken |
| 60-65 | Below average |
| 65-70 | Average |
| 70-75 | Good |
| 75-80 | Excellent |
| >80 | State-of-art |

### MT-Bench Ranges

| Score | Quality |
|-------|---------|
| <6.0 | Poor conversation |
| 6.0-7.0 | Acceptable |
| 7.0-8.0 | Good |
| 8.0-9.0 | Excellent |
| >9.0 | Near human-level |

## Resources

- **lm-evaluation-harness**: https://github.com/EleutherAI/lm-evaluation-harness
- **MT-Bench**: https://github.com/lm-sys/FastChat
- **HumanEval**: https://github.com/openai/human-eval
- **Open LLM Leaderboard**: https://huggingface.co/spaces/HuggingFaceH4/open_llm_leaderboard




### Methods

# Model Merging Methods: Deep Dive

Complete technical guide to model merging algorithms based on research papers.

## Table of Contents
- TIES-Merging Algorithm
- DARE (Drop And REscale)
- Linear Merging
- SLERP
- Task Arithmetic
- Comparison

## TIES-Merging: Resolving Interference

**Paper**: "TIES-Merging: Resolving Interference When Merging Models" (NeurIPS 2023)
**Authors**: Prateek Yadav et al.
**Code**: https://github.com/prateeky2806/ties-merging

### Algorithm Overview

TIES-Merging addresses two major sources of interference:
1. Redundant parameter values
2. Sign disagreement across models

**Three-Step Process**: TRIM, ELECT, MERGE

### Step 1: TRIM (Reset Small Changes)

Remove parameters that changed minimally during fine-tuning.

```python
def trim(task_vector, density=0.2):
    """Keep top-k% parameters by magnitude, reset rest to 0."""
    # Calculate magnitude
    magnitudes = torch.abs(task_vector)

    # Get threshold for top-k%
    k = int(density * task_vector.numel())
    threshold = torch.topk(magnitudes.flatten(), k).values.min()

    # Create mask: keep parameters above threshold
    mask = magnitudes >= threshold

    # Apply mask
    trimmed_vector = task_vector * mask

    return trimmed_vector

# Example
task_vector_1 = finetuned_model_1 - base_model
task_vector_2 = finetuned_model_2 - base_model

trimmed_1 = trim(task_vector_1, density=0.2)  # Keep top 20%
trimmed_2 = trim(task_vector_2, density=0.2)
```

### Step 2: ELECT SIGN (Resolve Conflicts)

When parameters have conflicting signs, elect the dominant sign.

```python
def elect_sign(task_vectors):
    """Resolve sign conflicts across multiple task vectors."""
    # Stack all task vectors
    stacked = torch.stack(task_vectors)  # (num_models, num_params)

    # Count positive vs negative for each parameter
    positive_count = (stacked > 0).sum(dim=0)
    negative_count = (stacked < 0).sum(dim=0)

    # Elect majority sign
    final_sign = torch.where(
        positive_count > negative_count,
        torch.ones_like(stacked[0]),
        -torch.ones_like(stacked[0])
    )

    # Where tie, keep sign from first model
    tie_mask = (positive_count == negative_count)
    final_sign[tie_mask] = torch.sign(stacked[0][tie_mask])

    return final_sign

# Example
task_vectors = [trimmed_1, trimmed_2, trimmed_3]
elected_sign = elect_sign(task_vectors)
```

### Step 3: MERGE (Disjoint Merging)

Merge only parameters that agree with elected sign.

```python
def ties_merge(base_model, task_vectors, density=0.2, lambda_param=1.0):
    """Complete TIES-Merging algorithm."""
    # Step 1: Trim each task vector
    trimmed_vectors = [trim(tv, density) for tv in task_vectors]

    # Step 2: Elect sign
    elected_sign = elect_sign(trimmed_vectors)

    # Step 3: Merge aligned parameters
    merged_task_vector = torch.zeros_like(task_vectors[0])

    for tv in trimmed_vectors:
        # Keep only parameters aligned with elected sign
        aligned_mask = (torch.sign(tv) == elected_sign) | (tv == 0)
        aligned_params = tv * aligned_mask

        # Accumulate
        merged_task_vector += aligned_params

    # Average
    num_models = len(task_vectors)
    merged_task_vector /= num_models

    # Add back to base model
    final_model = base_model + lambda_param * merged_task_vector

    return final_model

# Usage
base = load_model("mistralai/Mistral-7B-v0.1")
model_1 = load_model("WizardLM/WizardMath-7B-V1.1")
model_2 = load_model("teknium/OpenHermes-2.5-Mistral-7B")
model_3 = load_model("NousResearch/Nous-Hermes-2-Mistral-7B-DPO")

task_vectors = [
    model_1 - base,
    model_2 - base,
    model_3 - base
]

merged = ties_merge(base, task_vectors, density=0.5, lambda_param=1.0)
```

### Hyperparameters

**density** (ρ): Fraction of parameters to keep (default: 0.2)
- Lower (0.1-0.3): More aggressive pruning, higher sparsity
- Higher (0.5-0.8): Conservative pruning, denser result

**lambda** (λ): Scaling factor for merged task vector (default: 1.0)
- Lower (<1.0): Less influence from fine-tuned models
- Higher (>1.0): More influence from fine-tuned models

## DARE: Drop And REscale

**Paper**: "Language Models are Super Mario: Absorbing Abilities from Homologous Models as a Free Lunch" (arXiv 2311.03099, 2023)
**Authors**: Le Yu, Bowen Yu, Haiyang Yu, Fei Huang, Yongbin Li

### Algorithm

DARE randomly drops delta parameters and rescales remaining ones.

### Mathematical Formulation

Given:
- Base model parameters: θ₀
- Fine-tuned model parameters: θₜ
- Delta parameters: δₜ = θₜ - θ₀

**Step 1: Random Drop**

```
m_t ~ Bernoulli(p)  # Drop mask
δ̃_t = (1 - m_t) ⊙ δ_t  # Element-wise product
```

**Step 2: Rescale**

```
δ̂_t = δ̃_t / (1 - p)  # Rescale to preserve expectation
```

**Final Model**

```
θ̂_t = θ₀ + δ̂_t
```

### Implementation

```python
def dare(base_model, finetuned_model, drop_rate=0.9):
    """DARE: Drop And REscale delta parameters."""
    # Compute delta
    delta = finetuned_model - base_model

    # Random drop mask (Bernoulli)
    drop_mask = torch.bernoulli(torch.full_like(delta, drop_rate))

    # Apply mask (keep 1-p, drop p)
    dropped_delta = delta * (1 - drop_mask)

    # Rescale to preserve expectation
    rescaled_delta = dropped_delta / (1 - drop_rate)

    # Reconstruct model
    result = base_model + rescaled_delta

    return result

# Example
base = load_model("mistralai/Mistral-7B-v0.1")
finetuned = load_model("WizardLM/WizardMath-7B-V1.1")

# Drop 90% of delta parameters
result = dare(base, finetuned, drop_rate=0.9)
```

### DARE + TIES (DARE-TIES)

Combine both methods for best results.

```python
def dare_ties(base_model, finetuned_models, drop_rate=0.9, density=0.5):
    """DARE + TIES-Merging."""
    # Step 1: Apply DARE to each model
    dare_deltas = []
    for model in finetuned_models:
        delta = model - base_model

        # DARE drop
        drop_mask = torch.bernoulli(torch.full_like(delta, drop_rate))
        dropped = delta * (1 - drop_mask)
        rescaled = dropped / (1 - drop_rate)

        dare_deltas.append(rescaled)

    # Step 2: Apply TIES to DARE-processed deltas
    merged = ties_merge(base_model, dare_deltas, density=density)

    return merged
```

### Hyperparameters

**drop_rate** (p): Probability of dropping each parameter (default: 0.9)
- Lower (0.5-0.7): Conservative, keeps more parameters
- Higher (0.9-0.99): Aggressive, maximum sparsity
- Works well even at 0.99 for large models

**Observations**:
- Larger models tolerate higher drop rates
- Delta parameters with small absolute values (<0.002) can be safely dropped
- Performance improves with model size

## Linear Merging (Model Soup)

Simple weighted average.

```python
def linear_merge(models, weights):
    """Weighted average of model parameters."""
    assert len(models) == len(weights)
    assert sum(weights) == 1.0, "Weights should sum to 1"

    merged = sum(w * model for w, model in zip(weights, models))

    return merged

# Example
models = [model_1, model_2, model_3]
weights = [0.4, 0.3, 0.3]
merged = linear_merge(models, weights)
```

## SLERP: Spherical Linear Interpolation

Interpolate along sphere in weight space.

```python
def slerp(model_1, model_2, t=0.5):
    """SLERP between two models."""
    # Flatten parameters
    p1 = torch.cat([p.flatten() for p in model_1.parameters()])
    p2 = torch.cat([p.flatten() for p in model_2.parameters()])

    # Normalize
    p1_norm = p1 / p1.norm()
    p2_norm = p2 / p2.norm()

    # Compute angle
    dot = (p1_norm * p2_norm).sum()
    theta = torch.acos(torch.clamp(dot, -1.0, 1.0))

    # SLERP formula
    if theta < 1e-6:
        # Vectors nearly parallel, use linear interpolation
        result = (1 - t) * p1 + t * p2
    else:
        # Spherical interpolation
        sin_theta = torch.sin(theta)
        result = (torch.sin((1 - t) * theta) / sin_theta) * p1 + \
                 (torch.sin(t * theta) / sin_theta) * p2

    # Reshape back to model
    merged_model = reshape_to_model(result, model_1)

    return merged_model

# Example
merged = slerp(model_1, model_2, t=0.5)  # 50-50 blend
```

## Task Arithmetic

Add task vectors to base model.

```python
def task_arithmetic(base_model, finetuned_models, lambdas):
    """Task arithmetic merging."""
    # Extract task vectors
    task_vectors = [model - base_model for model in finetuned_models]

    # Weighted sum
    combined_vector = sum(λ * tv for λ, tv in zip(lambdas, task_vectors))

    # Add to base
    merged = base_model + combined_vector

    return merged

# Example
base = load_model("mistralai/Mistral-7B-v0.1")
math_model = load_model("WizardLM/WizardMath-7B-V1.1")
code_model = load_model("ajibawa-2023/Code-Mistral-7B")

merged = task_arithmetic(
    base,
    [math_model, code_model],
    lambdas=[0.6, 0.4]
)
```

## Method Comparison

| Method | Pros | Cons | Best For |
|--------|------|------|----------|
| **Linear** | Simple, fast | Basic averaging | 2-3 similar models |
| **SLERP** | Preserves magnitude | Only 2 models | Smooth blending |
| **Task Arithmetic** | Intuitive, flexible | Sign conflicts | Multiple specialized models |
| **TIES** | Resolves conflicts | More complex | Many task-specific models |
| **DARE** | High sparsity | Random variance | Reducing redundancy |
| **DARE-TIES** | Best performance | Most complex | Production (state-of-art) |

## Resources

- **TIES Paper**: https://arxiv.org/abs/2306.01708
- **DARE Paper**: https://arxiv.org/abs/2311.03099
- **mergekit**: https://github.com/arcee-ai/mergekit




### Examples

# Model Merging Examples

Real-world merge configurations from successful models on HuggingFace and research papers.

## Table of Contents
- Successful Merges
- Mixtral-based Merges
- Llama-based Merges
- Task-Specific Merges
- Production Examples

## Successful Merges

### Marcoro14-7B-slerp

**Achievement**: #1 on Open LLM Leaderboard (February 2024)
**Method**: SLERP
**Source**: HuggingFace

```yaml
# marcoro14-7b-slerp.yml
merge_method: slerp
slices:
  - sources:
      - model: AIDC-ai-business/Marcoroni-7B-v3
        layer_range: [0, 32]
      - model: EmbeddedLLM/Mistral-7B-Merge-14-v0.1
        layer_range: [0, 32]
parameters:
  t: 0.5  # Equal blend
dtype: bfloat16
```

**Results**:
- Average: 74.32 on Open LLM Leaderboard
- Strong across all tasks
- Smooth capability combination

### goliath-120b (Mixtral MoE)

**Method**: Linear + SLERP
**Achievement**: Top-performing 120B model

```yaml
# goliath-120b.yml
merge_method: slerp
slices:
  - sources:
      - model: alpindale/c4ai-command-r-plus-GPTQ
        layer_range: [0, 40]
      - model: CohereForAI/c4ai-command-r-v01
        layer_range: [0, 40]
parameters:
  t:
    - filter: self_attn
      value: [0, 0.5, 0.3, 0.7, 1]  # Layer-specific blending
    - filter: mlp
      value: [1, 0.5, 0.7, 0.3, 0]
    - value: 0.5  # Default
dtype: float16
```

## Mixtral-based Merges

### Math + Code Specialist

**Goal**: Combine mathematical reasoning with code generation

```yaml
# math-code-mixtral.yml
merge_method: task_arithmetic
base_model: mistralai/Mixtral-8x7B-v0.1
models:
  - model: WizardLM/WizardMath-7B-V1.1
    parameters:
      weight: 0.6  # Emphasize math
  - model: ajibawa-2023/Code-Mixtral-8x7B
    parameters:
      weight: 0.4  # Add code
dtype: bfloat16
```

**Expected capabilities**:
- Strong mathematical reasoning
- Code generation and understanding
- Technical problem-solving

### Chat + Roleplay Merge

```yaml
# chat-roleplay.yml
merge_method: slerp
slices:
  - sources:
      - model: teknium/OpenHermes-2.5-Mistral-7B
        layer_range: [0, 32]
      - model: Undi95/MLewd-ReMM-L2-Chat-20B-Part1
        layer_range: [0, 32]
parameters:
  t: 0.5
dtype: bfloat16
```

### Multi-Task TIES Merge

```yaml
# multi-task-mixtral.yml
merge_method: ties
base_model: mistralai/Mixtral-8x7B-v0.1
models:
  - model: WizardLM/WizardMath-7B-V1.1
    parameters:
      density: 0.5
      weight: 1.0
  - model: teknium/OpenHermes-2.5-Mistral-7B
    parameters:
      density: 0.5
      weight: 1.0
  - model: ajibawa-2023/Code-Mixtral-8x7B
    parameters:
      density: 0.5
      weight: 1.0
parameters:
  normalize: true
dtype: bfloat16
```

## Llama-based Merges

### Platypus-Hermes Merge

**Models**: Garage-bAInd/Platypus2-13B + WizardLM/WizardLM-13B-V1.2

```yaml
# platypus-hermes-13b.yml
merge_method: linear
models:
  - model: garage-bAInd/Platypus2-13B
    parameters:
      weight: 0.5
  - model: WizardLM/WizardLM-13B-V1.2
    parameters:
      weight: 0.3
  - model: psmathur/orca_mini_v3_13b
    parameters:
      weight: 0.2
dtype: float16
```

### DARE-TIES Llama Merge

**Source**: DARE paper (arXiv 2311.03099)

```yaml
# dare-ties-llama.yml
merge_method: dare_ties
base_model: meta-llama/Llama-2-7b-hf
models:
  - model: WizardLM/WizardLM-7B-V1.0
    parameters:
      density: 0.5   # Keep top 50%
      weight: 0.6
      dare:
        drop_rate: 0.9  # Drop 90% of deltas
  - model: garage-bAInd/Platypus-7B
    parameters:
      density: 0.5
      weight: 0.4
      dare:
        drop_rate: 0.9
parameters:
  int8_mask: true
dtype: bfloat16
```

## Task-Specific Merges

### Medical Domain

**Goal**: Create medical specialist model

```yaml
# medical-specialist.yml
merge_method: task_arithmetic
base_model: mistralai/Mistral-7B-v0.1
models:
  - model: medalpaca/medalpaca-7b
    parameters:
      weight: 0.7  # Strong medical knowledge
  - model: teknium/OpenHermes-2.5-Mistral-7B
    parameters:
      weight: 0.3  # Add general chat ability
dtype: bfloat16
```

### Legal Assistant

```yaml
# legal-assistant.yml
merge_method: slerp
slices:
  - sources:
      - model: law-ai/legal-bert-7b
        layer_range: [0, 32]
      - model: teknium/OpenHermes-2.5-Mistral-7B
        layer_range: [0, 32]
parameters:
  t:
    - filter: self_attn
      value: 0.7  # Emphasize legal model in attention
    - filter: mlp
      value: 0.3  # More general chat in MLPs
    - value: 0.5
dtype: bfloat16
```

### Multilingual Merge

```yaml
# multilingual-merge.yml
merge_method: linear
models:
  - model: mistralai/Mistral-7B-v0.1
    parameters:
      weight: 0.4  # English
  - model: CohereForAI/aya-23-7B
    parameters:
      weight: 0.3  # Multilingual
  - model: Qwen/Qwen3-7B
    parameters:
      weight: 0.3  # Asian languages
dtype: bfloat16
```

## Production Examples

### Gradual Merge (Safer)

**Strategy**: Merge incrementally, test at each step

```yaml
# Step 1: Merge two models
# step1.yml
merge_method: slerp
slices:
  - sources:
      - model: base_model
        layer_range: [0, 32]
      - model: specialist_1
        layer_range: [0, 32]
parameters:
  t: 0.3  # Conservative blend
dtype: bfloat16
```

```yaml
# Step 2: Add third model to result
# step2.yml
merge_method: slerp
slices:
  - sources:
      - model: ./merged_step1  # Previous merge
        layer_range: [0, 32]
      - model: specialist_2
        layer_range: [0, 32]
parameters:
  t: 0.3  # Conservative
dtype: bfloat16
```

**Benefits**:
- Test after each merge
- Easier to debug
- Can stop if quality degrades

### A/B Testing Setup

```yaml
# variant_a.yml - Conservative
merge_method: slerp
slices:
  - sources:
      - model: base_model
        layer_range: [0, 32]
      - model: specialist
        layer_range: [0, 32]
parameters:
  t: 0.3  # 30% specialist
dtype: bfloat16
```

```yaml
# variant_b.yml - Aggressive
merge_method: slerp
slices:
  - sources:
      - model: base_model
        layer_range: [0, 32]
      - model: specialist
        layer_range: [0, 32]
parameters:
  t: 0.7  # 70% specialist
dtype: bfloat16
```

**Test both**, choose best performer

### Frankenmerge (Experimental)

**Warning**: Experimental, may not work

```yaml
# frankenmerge.yml
merge_method: passthrough
slices:
  # First 8 layers from model A
  - sources:
      - model: model_a
        layer_range: [0, 8]

  # Middle 16 layers from model B
  - sources:
      - model: model_b
        layer_range: [8, 24]

  # Last 8 layers from model C
  - sources:
      - model: model_c
        layer_range: [24, 32]
dtype: bfloat16
```

**Use case**: Create models with non-standard layer counts

### MoE from Merges

```yaml
# moe-from-merges.yml
merge_method: moe
base_model: mistralai/Mistral-7B-v0.1
experts:
  - source_model: WizardLM/WizardMath-7B-V1.1
    positive_prompts:
      - "math"
      - "calculate"
      - "solve"
      - "equation"

  - source_model: ajibawa-2023/Code-Mistral-7B
    positive_prompts:
      - "code"
      - "python"
      - "function"
      - "programming"

  - source_model: teknium/OpenHermes-2.5-Mistral-7B
    positive_prompts:
      - "chat"
      - "conversation"
      - "help"
      - "question"
dtype: bfloat16
```

**Result**: Dynamic expert selection based on prompt

## Command-Line Examples

### Basic Merge

```bash
# Simple two-model SLERP
mergekit-yaml config.yml ./output-model \
  --cuda \
  --lazy-unpickle
```

### Large Model Merge (Low VRAM)

```bash
# Merge on CPU (slow but works with 8GB VRAM)
mergekit-yaml config.yml ./output-model \
  --allow-crimes \  # Enable CPU offloading
  --low-cpu-memory
```

### Merge and Upload

```bash
# Merge and push to HuggingFace
mergekit-yaml config.yml ./merged-model --cuda

cd merged-model
python << EOF
from transformers import AutoModel, AutoTokenizer

model = AutoModel.from_pretrained("./")
tokenizer = AutoTokenizer.from_pretrained("./")

model.push_to_hub("username/my-merged-model")
tokenizer.push_to_hub("username/my-merged-model")
EOF
```

### Batch Merging

```bash
# Merge multiple configs
for config in configs/*.yml; do
  output="./output/$(basename $config .yml)"
  mergekit-yaml $config $output --cuda
done
```

## Tips from Successful Merges

1. **Start Conservative**: Use t=0.3-0.5 for SLERP, test before going higher
2. **Match Architectures**: Only merge models with same base architecture
3. **Test Extensively**: Benchmark on multiple tasks before deploying
4. **Layer-Specific Merging**: Different t values for attention vs MLP often works better
5. **DARE for Many Models**: When merging 3+ models, DARE-TIES often best
6. **Gradual Merging**: For production, merge incrementally and test

## Resources

- **HuggingFace Models**: Browse merged models for inspiration
- **Open LLM Leaderboard**: See top-performing merges
- **mergekit Examples**: https://github.com/arcee-ai/mergekit/tree/main/examples




---

## 🚀 Usage

**Reference this template:** `@skill-model-merging.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
