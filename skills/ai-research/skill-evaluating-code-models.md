---
id: skill-evaluating-code-models
type: skill
name: evaluating-code-models
description: Evaluates code generation models across HumanEval, MBPP, MultiPL-E, and
  15+ benchmarks with pass@k metrics. Use when benchmarking code models, comparing
  coding abilities, testing multi-language support, or measuring code generation quality.
  Industry standard from BigCode Project used by HuggingFace leaderboards.
category: ai-research
complexity: medium
keywords:
- benchmark
- docker
- git
- github
- go
- java
- javascript
- python
- rust
- test
- testing
- typescript
capabilities: []
token_estimate: 1748
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,748 -->
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




# evaluating-code-models

> Evaluates code generation models across HumanEval, MBPP, MultiPL-E, and 15+ benchmarks with pass@k metrics. Use when benchmarking code models, comparing coding abilities, testing multi-language support, or measuring code generation quality. Industry standard from BigCode Project used by HuggingFace leaderboards.

# BigCode Evaluation Harness - Code Model Benchmarking

## Quick Start

BigCode Evaluation Harness evaluates code generation models across 15+ benchmarks including HumanEval, MBPP, and MultiPL-E (18 languages).

**Installation**:
```bash
git clone https://github.com/bigcode-project/bigcode-evaluation-harness.git
cd bigcode-evaluation-harness
pip install -e .
accelerate config
```

**Evaluate on HumanEval**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks humaneval \
  --max_length_generation 512 \
  --temperature 0.2 \
  --n_samples 20 \
  --batch_size 10 \
  --allow_code_execution \
  --save_generations
```

**View available tasks**:
```bash
python -c "from bigcode_eval.tasks import ALL_TASKS; print(ALL_TASKS)"
```

## Common Workflows

### Workflow 1: Standard Code Benchmark Evaluation

Evaluate model on core code benchmarks (HumanEval, MBPP, HumanEval+).

**Checklist**:
```
Code Benchmark Evaluation:
- [ ] Step 1: Choose benchmark suite
- [ ] Step 2: Configure model and generation
- [ ] Step 3: Run evaluation with code execution
- [ ] Step 4: Analyze pass@k results
```

**Step 1: Choose benchmark suite**

**Python code generation** (most common):
- **HumanEval**: 164 handwritten problems, function completion
- **HumanEval+**: Same 164 problems with 80× more tests (stricter)
- **MBPP**: 500 crowd-sourced problems, entry-level difficulty
- **MBPP+**: 399 curated problems with 35× more tests

**Multi-language** (18 languages):
- **MultiPL-E**: HumanEval/MBPP translated to C++, Java, JavaScript, Go, Rust, etc.

**Advanced**:
- **APPS**: 10,000 problems (introductory/interview/competition)
- **DS-1000**: 1,000 data science problems across 7 libraries

**Step 2: Configure model and generation**

```bash
# Standard HuggingFace model
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks humaneval \
  --max_length_generation 512 \
  --temperature 0.2 \
  --do_sample True \
  --n_samples 200 \
  --batch_size 50 \
  --allow_code_execution

# Quantized model (4-bit)
accelerate launch main.py \
  --model codellama/CodeLlama-34b-hf \
  --tasks humaneval \
  --load_in_4bit \
  --max_length_generation 512 \
  --allow_code_execution

# Custom/private model
accelerate launch main.py \
  --model /path/to/my-code-model \
  --tasks humaneval \
  --trust_remote_code \
  --use_auth_token \
  --allow_code_execution
```

**Step 3: Run evaluation**

```bash
# Full evaluation with pass@k estimation (k=1,10,100)
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks humaneval \
  --temperature 0.8 \
  --n_samples 200 \
  --batch_size 50 \
  --allow_code_execution \
  --save_generations \
  --metric_output_path results/starcoder2-humaneval.json
```

**Step 4: Analyze results**

Results in `results/starcoder2-humaneval.json`:
```json
{
  "humaneval": {
    "pass@1": 0.354,
    "pass@10": 0.521,
    "pass@100": 0.689
  },
  "config": {
    "model": "bigcode/starcoder2-7b",
    "temperature": 0.8,
    "n_samples": 200
  }
}
```

### Workflow 2: Multi-Language Evaluation (MultiPL-E)

Evaluate code generation across 18 programming languages.

**Checklist**:
```
Multi-Language Evaluation:
- [ ] Step 1: Generate solutions (host machine)
- [ ] Step 2: Run evaluation in Docker (safe execution)
- [ ] Step 3: Compare across languages
```

**Step 1: Generate solutions on host**

```bash
# Generate without execution (safe)
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks multiple-py,multiple-js,multiple-java,multiple-cpp \
  --max_length_generation 650 \
  --temperature 0.8 \
  --n_samples 50 \
  --batch_size 50 \
  --generation_only \
  --save_generations \
  --save_generations_path generations_multi.json
```

**Step 2: Evaluate in Docker container**

```bash
# Pull the MultiPL-E Docker image
docker pull ghcr.io/bigcode-project/evaluation-harness-multiple

# Run evaluation inside container
docker run -v $(pwd)/generations_multi.json:/app/generations.json:ro \
  -it evaluation-harness-multiple python3 main.py \
  --model bigcode/starcoder2-7b \
  --tasks multiple-py,multiple-js,multiple-java,multiple-cpp \
  --load_generations_path /app/generations.json \
  --allow_code_execution \
  --n_samples 50
```

**Supported languages**: Python, JavaScript, Java, C++, Go, Rust, TypeScript, C#, PHP, Ruby, Swift, Kotlin, Scala, Perl, Julia, Lua, R, Racket

### Workflow 3: Instruction-Tuned Model Evaluation

Evaluate chat/instruction models with proper formatting.

**Checklist**:
```
Instruction Model Evaluation:
- [ ] Step 1: Use instruction-tuned tasks
- [ ] Step 2: Configure instruction tokens
- [ ] Step 3: Run evaluation
```

**Step 1: Choose instruction tasks**

- **instruct-humaneval**: HumanEval with instruction prompts
- **humanevalsynthesize-{lang}**: HumanEvalPack synthesis tasks

**Step 2: Configure instruction tokens**

```bash
# For models with chat templates (e.g., CodeLlama-Instruct)
accelerate launch main.py \
  --model codellama/CodeLlama-7b-Instruct-hf \
  --tasks instruct-humaneval \
  --instruction_tokens "<s>[INST],</s>,[/INST]" \
  --max_length_generation 512 \
  --allow_code_execution
```

**Step 3: HumanEvalPack for instruction models**

```bash
# Test code synthesis across 6 languages
accelerate launch main.py \
  --model codellama/CodeLlama-7b-Instruct-hf \
  --tasks humanevalsynthesize-python,humanevalsynthesize-js \
  --prompt instruct \
  --max_length_generation 512 \
  --allow_code_execution
```

### Workflow 4: Compare Multiple Models

Benchmark suite for model comparison.

**Step 1: Create evaluation script**

```bash
#!/bin/bash
# eval_models.sh

MODELS=(
  "bigcode/starcoder2-7b"
  "codellama/CodeLlama-7b-hf"
  "deepseek-ai/deepseek-coder-6.7b-base"
)
TASKS="humaneval,mbpp"

for model in "${MODELS[@]}"; do
  model_name=$(echo $model | tr '/' '-')
  echo "Evaluating $model"

  accelerate launch main.py \
    --model $model \
    --tasks $TASKS \
    --temperature 0.2 \
    --n_samples 20 \
    --batch_size 20 \
    --allow_code_execution \
    --metric_output_path results/${model_name}.json
done
```

**Step 2: Generate comparison table**

```python
import json
import pandas as pd

models = ["bigcode-starcoder2-7b", "codellama-CodeLlama-7b-hf", "deepseek-ai-deepseek-coder-6.7b-base"]
results = []

for model in models:
    with open(f"results/{model}.json") as f:
        data = json.load(f)
        results.append({
            "Model": model,
            "HumanEval pass@1": f"{data['humaneval']['pass@1']:.3f}",
            "MBPP pass@1": f"{data['mbpp']['pass@1']:.3f}"
        })

df = pd.DataFrame(results)
print(df.to_markdown(index=False))
```

## When to Use vs Alternatives

**Use BigCode Evaluation Harness when:**
- Evaluating **code generation** models specifically
- Need **multi-language** evaluation (18 languages via MultiPL-E)
- Testing **functional correctness** with unit tests (pass@k)
- Benchmarking for **BigCode/HuggingFace leaderboards**
- Evaluating **fill-in-the-middle** (FIM) capabilities

**Use alternatives instead:**
- **lm-evaluation-harness**: General LLM benchmarks (MMLU, GSM8K, HellaSwag)
- **EvalPlus**: Stricter HumanEval+/MBPP+ with more test cases
- **SWE-bench**: Real-world GitHub issue resolution
- **LiveCodeBench**: Contamination-free, continuously updated problems
- **CodeXGLUE**: Code understanding tasks (clone detection, defect prediction)

## Supported Benchmarks

| Benchmark | Problems | Languages | Metric | Use Case |
|-----------|----------|-----------|--------|----------|
| HumanEval | 164 | Python | pass@k | Standard code completion |
| HumanEval+ | 164 | Python | pass@k | Stricter evaluation (80× tests) |
| MBPP | 500 | Python | pass@k | Entry-level problems |
| MBPP+ | 399 | Python | pass@k | Stricter evaluation (35× tests) |
| MultiPL-E | 164×18 | 18 languages | pass@k | Multi-language evaluation |
| APPS | 10,000 | Python | pass@k | Competition-level |
| DS-1000 | 1,000 | Python | pass@k | Data science (pandas, numpy, etc.) |
| HumanEvalPack | 164×3×6 | 6 languages | pass@k | Synthesis/fix/explain |
| Mercury | 1,889 | Python | Efficiency | Computational efficiency |

## Common Issues

**Issue: Different results than reported in papers**

Check these factors:
```bash
# 1. Verify n_samples (need 200 for accurate pass@k)
--n_samples 200

# 2. Check temperature (0.2 for greedy-ish, 0.8 for sampling)
--temperature 0.8

# 3. Verify task name matches exactly
--tasks humaneval  # Not "human_eval" or "HumanEval"

# 4. Check max_length_generation
--max_length_generation 512  # Increase for longer problems
```

**Issue: CUDA out of memory**

```bash
# Use quantization
--load_in_8bit
# OR
--load_in_4bit

# Reduce batch size
--batch_size 1

# Set memory limit
--max_memory_per_gpu "20GiB"
```

**Issue: Code execution hangs or times out**

Use Docker for safe execution:
```bash
# Generate on host (no execution)
--generation_only --save_generations

# Evaluate in Docker
docker run ... --allow_code_execution --load_generations_path ...
```

**Issue: Low scores on instruction models**

Ensure proper instruction formatting:
```bash
# Use instruction-specific tasks
--tasks instruct-humaneval

# Set instruction tokens for your model
--instruction_tokens "<s>[INST],</s>,[/INST]"
```

**Issue: MultiPL-E language failures**

Use the dedicated Docker image:
```bash
docker pull ghcr.io/bigcode-project/evaluation-harness-multiple
```

## Command Reference

| Argument | Default | Description |
|----------|---------|-------------|
| `--model` | - | HuggingFace model ID or local path |
| `--tasks` | - | Comma-separated task names |
| `--n_samples` | 1 | Samples per problem (200 for pass@k) |
| `--temperature` | 0.2 | Sampling temperature |
| `--max_length_generation` | 512 | Max tokens (prompt + generation) |
| `--batch_size` | 1 | Batch size per GPU |
| `--allow_code_execution` | False | Enable code execution (required) |
| `--generation_only` | False | Generate without evaluation |
| `--load_generations_path` | - | Load pre-generated solutions |
| `--save_generations` | False | Save generated code |
| `--metric_output_path` | results.json | Output file for metrics |
| `--load_in_8bit` | False | 8-bit quantization |
| `--load_in_4bit` | False | 4-bit quantization |
| `--trust_remote_code` | False | Allow custom model code |
| `--precision` | fp32 | Model precision (fp32/fp16/bf16) |

## Hardware Requirements

| Model Size | VRAM (fp16) | VRAM (4-bit) | Time (HumanEval, n=200) |
|------------|-------------|--------------|-------------------------|
| 7B | 14GB | 6GB | ~30 min (A100) |
| 13B | 26GB | 10GB | ~1 hour (A100) |
| 34B | 68GB | 20GB | ~2 hours (A100) |

## Resources

- **GitHub**: https://github.com/bigcode-project/bigcode-evaluation-harness
- **Documentation**: https://github.com/bigcode-project/bigcode-evaluation-harness/tree/main/docs
- **BigCode Leaderboard**: https://huggingface.co/spaces/bigcode/bigcode-models-leaderboard
- **HumanEval Dataset**: https://huggingface.co/datasets/openai/openai_humaneval
- **MultiPL-E**: https://github.com/nuprl/MultiPL-E


---


## 📚 Reference Materials


### Issues

# Common Issues and Troubleshooting

Solutions to frequently encountered problems with BigCode Evaluation Harness.

## Installation Issues

### Issue: PyTorch Version Conflicts

**Symptom**: Import errors or CUDA incompatibility after installation.

**Solution**: Install PyTorch separately BEFORE installing the harness:
```bash
# Check your CUDA version
nvidia-smi

# Install matching PyTorch (example for CUDA 11.8)
pip install torch --index-url https://download.pytorch.org/whl/cu118

# Then install harness
pip install -e .
```

### Issue: DS-1000 Specific Requirements

**Symptom**: Errors when running DS-1000 benchmark.

**Solution**: DS-1000 requires Python 3.7.10 specifically:
```bash
# Create conda environment
conda create -n ds1000 python=3.7.10
conda activate ds1000

# Install specific dependencies
pip install -e ".[ds1000]"
pip install torch==1.12.1+cu116 --extra-index-url https://download.pytorch.org/whl/cu116

# Set environment variables
export TF_CPP_MIN_LOG_LEVEL=3
export TF_FORCE_GPU_ALLOW_GROWTH=true
```

### Issue: HuggingFace Authentication

**Symptom**: `401 Unauthorized` when accessing gated models/datasets.

**Solution**:
```bash
# Login to HuggingFace
huggingface-cli login

# Use auth token in command
accelerate launch main.py \
  --model meta-llama/CodeLlama-7b-hf \
  --use_auth_token \
  ...
```

## Memory Issues

### Issue: CUDA Out of Memory

**Symptom**: `torch.cuda.OutOfMemoryError: CUDA out of memory`

**Solutions**:

1. **Use quantization**:
```bash
# 8-bit quantization (saves ~50% memory)
accelerate launch main.py \
  --model bigcode/starcoder2-15b \
  --load_in_8bit \
  ...

# 4-bit quantization (saves ~75% memory)
accelerate launch main.py \
  --model bigcode/starcoder2-15b \
  --load_in_4bit \
  ...
```

2. **Reduce batch size**:
```bash
--batch_size 1
```

3. **Set memory limits**:
```bash
--max_memory_per_gpu "20GiB"
# OR
--max_memory_per_gpu auto
```

4. **Use half precision**:
```bash
--precision fp16
# OR
--precision bf16
```

### Issue: Running Out of RAM During Evaluation

**Symptom**: Process killed, system becomes unresponsive.

**Solution**: Reduce number of samples being held in memory:
```bash
# Save intermediate results
--save_every_k_tasks 10

# Evaluate subset at a time
--limit 50 --limit_start 0
# Then
--limit 50 --limit_start 50
```

## Execution Issues

### Issue: Code Execution Not Allowed

**Symptom**: Error about code execution being disabled.

**Solution**: Add the execution flag:
```bash
accelerate launch main.py \
  --model ... \
  --tasks humaneval \
  --allow_code_execution  # Required for unit test benchmarks
```

### Issue: Execution Timeout/Hang

**Symptom**: Evaluation hangs indefinitely or times out.

**Solutions**:

1. **Use Docker for isolation**:
```bash
# Generate without execution
accelerate launch main.py \
  --model ... \
  --tasks humaneval \
  --generation_only \
  --save_generations \
  --save_generations_path generations.json

# Evaluate in Docker
docker run -v $(pwd)/generations.json:/app/generations.json:ro \
  -it evaluation-harness python3 main.py \
  --tasks humaneval \
  --load_generations_path /app/generations.json \
  --allow_code_execution
```

2. **Use subsets for debugging**:
```bash
--limit 10  # Only evaluate first 10 problems
```

### Issue: MultiPL-E Language Runtime Errors

**Symptom**: Errors executing code in non-Python languages.

**Solution**: Use the MultiPL-E specific Docker image:
```bash
docker pull ghcr.io/bigcode-project/evaluation-harness-multiple
docker run -it evaluation-harness-multiple ...
```

## Result Discrepancies

### Issue: Results Don't Match Paper/Leaderboard

**Symptom**: Your pass@k scores differ from reported values.

**Common causes and fixes**:

1. **Wrong n_samples**:
```bash
# For accurate pass@k estimation, use n_samples >= 200
--n_samples 200
```

2. **Wrong temperature**:
```bash
# Papers often use different temperatures
# For pass@1: temperature 0.2 (near-greedy)
# For pass@10, pass@100: temperature 0.8 (more sampling)
--temperature 0.8
```

3. **Task name mismatch**:
```bash
# Use exact task names
--tasks humaneval      # Correct
--tasks human_eval     # Wrong
--tasks HumanEval      # Wrong
```

4. **Prompting differences**:
```bash
# Some models need instruction formatting
--instruction_tokens "<s>[INST],</s>,[/INST]"

# Or specific prompt types for HumanEvalPack
--prompt instruct
```

5. **Postprocessing differences**:
```bash
# Enable/disable postprocessing
--postprocess True  # Default
```

### Issue: Inconsistent Results Across Runs

**Symptom**: Different scores each time you run.

**Solution**: For reproducibility:
```bash
# Use greedy decoding for deterministic results
--do_sample False
--temperature 0.0

# OR set seeds (if using sampling)
# Note: Sampling inherently has variance
# Use high n_samples to reduce noise
--n_samples 200
```

## Model Loading Issues

### Issue: Model with Custom Code

**Symptom**: `ValueError: ... requires you to execute the configuration file`

**Solution**:
```bash
--trust_remote_code
```

### Issue: Private/Gated Model Access

**Symptom**: `401 Unauthorized` or `403 Forbidden`

**Solution**:
```bash
# First login
huggingface-cli login

# Then use auth token
--use_auth_token
```

### Issue: PEFT/LoRA Adapter Loading

**Symptom**: Can't load fine-tuned adapter.

**Solution**:
```bash
--model base-model-name \
--peft_model path/to/adapter
```

### Issue: Seq2Seq Model Not Generating

**Symptom**: Empty or truncated outputs with encoder-decoder models.

**Solution**:
```bash
--modeltype seq2seq
```

## Task-Specific Issues

### Issue: Low MBPP Scores with Instruction Models

**Symptom**: Instruction-tuned models score poorly on MBPP.

**Solution**: MBPP prompts are plain text, not instruction format. Consider:
1. Using `instruct-humaneval` for instruction models
2. Creating custom instruction-formatted prompts

### Issue: APPS Taking Too Long

**Symptom**: APPS evaluation runs for hours.

**Solutions**:
```bash
# Use subset
--limit 100

# Reduce samples
--n_samples 10

# Use introductory level only
--tasks apps-introductory
```

### Issue: GSM8K Wrong max_length

**Symptom**: Truncated outputs, low scores on math tasks.

**Solution**: GSM8K needs longer context for 8-shot prompts:
```bash
--max_length_generation 2048  # Not default 512
```

## Docker Issues

### Issue: Docker Image Pull Fails

**Symptom**: `Error response from daemon: manifest unknown`

**Solution**: Build locally:
```bash
# Clone repo
git clone https://github.com/bigcode-project/bigcode-evaluation-harness.git
cd bigcode-evaluation-harness

# Build image
sudo make DOCKERFILE=Dockerfile all

# For MultiPL-E
sudo make DOCKERFILE=Dockerfile-multiple all
```

### Issue: Docker Can't Access GPU

**Symptom**: No GPU available inside container.

**Solution**: Use nvidia-docker:
```bash
docker run --gpus all -it evaluation-harness ...
```

## Debugging Tips

### Enable Verbose Output

```bash
# Check what's being generated
--save_generations
--save_references

# Inspect a few samples
--limit 5
```

### Test Reference Solutions

```bash
# Verify test cases pass with ground truth
--check_references
```

### Inspect Intermediate Results

```bash
# Save progress periodically
--save_every_k_tasks 10
--save_generations_path intermediate_generations.json
```

### Common Debug Workflow

```bash
# 1. Test with tiny subset
accelerate launch main.py \
  --model your-model \
  --tasks humaneval \
  --limit 3 \
  --n_samples 1 \
  --save_generations \
  --allow_code_execution

# 2. Inspect generations
cat generations.json | python -m json.tool | head -100

# 3. If looks good, scale up
accelerate launch main.py \
  --model your-model \
  --tasks humaneval \
  --n_samples 200 \
  --allow_code_execution
```

## Getting Help

1. **Check existing issues**: https://github.com/bigcode-project/bigcode-evaluation-harness/issues
2. **Search closed issues**: Often contains solutions
3. **Open new issue** with:
   - Full command used
   - Error message
   - Environment details (Python version, PyTorch version, GPU)
   - Model being evaluated




### Custom Tasks

# Creating Custom Tasks in BigCode Evaluation Harness

Guide to implementing custom evaluation tasks for code generation models.

## Task Architecture

All tasks inherit from a base `Task` class and implement standard methods:

```python
class Task:
    DATASET_PATH: str  # HuggingFace dataset ID
    DATASET_NAME: str  # Dataset configuration (or None)

    def __init__(self, stop_words, requires_execution):
        """Initialize task with stop words and execution flag."""

    def get_dataset(self):
        """Return the evaluation dataset."""

    def get_prompt(self, doc):
        """Format document into model prompt."""

    def get_reference(self, doc):
        """Extract reference solution from document."""

    def postprocess_generation(self, generation, idx):
        """Clean up model output."""

    def process_results(self, generations, references):
        """Evaluate and return metrics."""
```

## Step-by-Step Implementation

### Step 1: Create Task File

Copy template to `bigcode_eval/tasks/<task_name>.py`:

```python
"""
<Paper Title>
<Paper URL>

<Task Description>

Homepage: <Homepage URL>
"""

import json
from evaluate import load
from bigcode_eval.base import Task

class MyCustomTask(Task):
    """Custom code evaluation task."""

    DATASET_PATH = "username/dataset-name"  # HuggingFace dataset
    DATASET_NAME = None  # or specific config name

    def __init__(self):
        super().__init__(
            stop_words=["\nclass", "\ndef", "\n#", "\nif", "\nprint"],
            requires_execution=True,  # Set True if running unit tests
        )

    def get_dataset(self):
        """Load evaluation split."""
        from datasets import load_dataset
        return load_dataset(
            self.DATASET_PATH,
            self.DATASET_NAME,
            split="test"
        )

    def get_prompt(self, doc):
        """Format problem into prompt for model."""
        return doc["prompt"]

    def get_reference(self, doc):
        """Return test cases or reference solution."""
        return doc["test"]

    def postprocess_generation(self, generation, idx):
        """Clean model output (remove extra text after solution)."""
        # Common: stop at first occurrence of stop words
        for stop_word in self.stop_words:
            if stop_word in generation:
                generation = generation[:generation.index(stop_word)]
        return generation

    def process_results(self, generations, references):
        """Execute tests and compute pass@k."""
        code_metric = load("code_eval")
        results, _ = code_metric.compute(
            references=references,
            predictions=generations,
            k=[1, 10, 100]
        )
        return results
```

### Step 2: Register Task

Add to `bigcode_eval/tasks/__init__.py`:

```python
from bigcode_eval.tasks import my_custom_task

TASK_REGISTRY = {
    # ... existing tasks ...
    "my-custom-task": my_custom_task.MyCustomTask,
}
```

### Step 3: Test Task

```bash
# Verify task loads correctly
python -c "from bigcode_eval.tasks import get_task; t = get_task('my-custom-task'); print(t)"

# Run small evaluation
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks my-custom-task \
  --limit 5 \
  --allow_code_execution
```

## Implementation Patterns

### Pattern 1: Code Execution with Unit Tests

For benchmarks that verify functional correctness:

```python
class CodeExecutionTask(Task):
    def __init__(self):
        super().__init__(
            stop_words=["\nclass", "\ndef", "\n#"],
            requires_execution=True,  # CRITICAL: Enable execution
        )

    def get_reference(self, doc):
        """Return test code to execute."""
        return f"\n{doc['test']}\ncheck({doc['entry_point']})"

    def process_results(self, generations, references):
        code_metric = load("code_eval")
        results, details = code_metric.compute(
            references=references,
            predictions=generations,
            k=[1, 10, 100],
            timeout=10.0,  # Seconds per test
        )
        return results
```

### Pattern 2: BLEU Score Evaluation

For benchmarks without executable tests:

```python
class BLEUTask(Task):
    def __init__(self):
        super().__init__(
            stop_words=["\n\n"],
            requires_execution=False,  # No code execution
        )

    def get_reference(self, doc):
        """Return reference code string."""
        return doc["canonical_solution"]

    def process_results(self, generations, references):
        from evaluate import load
        bleu = load("bleu")

        # Flatten generations (one per problem for BLEU)
        predictions = [g[0] for g in generations]

        results = bleu.compute(
            predictions=predictions,
            references=[[r] for r in references]
        )
        return {"bleu": results["bleu"]}
```

### Pattern 3: Few-Shot Prompting

For tasks requiring in-context examples:

```python
class FewShotTask(Task):
    def __init__(self):
        super().__init__(stop_words=["\n\n"], requires_execution=True)
        self.examples = self._load_examples()

    def _load_examples(self):
        """Load few-shot examples from JSON."""
        import os
        path = os.path.join(
            os.path.dirname(__file__),
            "few_shot_examples",
            "my_task_examples.json"
        )
        with open(path) as f:
            return json.load(f)

    def get_prompt(self, doc):
        """Build few-shot prompt."""
        prompt = ""
        for ex in self.examples[:3]:  # 3-shot
            prompt += f"Problem: {ex['problem']}\nSolution: {ex['solution']}\n\n"
        prompt += f"Problem: {doc['problem']}\nSolution:"
        return prompt
```

### Pattern 4: Fill-in-the-Middle (FIM)

For infilling tasks:

```python
class FIMTask(Task):
    FIM_PREFIX = "<fim_prefix>"
    FIM_MIDDLE = "<fim_middle>"
    FIM_SUFFIX = "<fim_suffix>"

    def __init__(self):
        super().__init__(
            stop_words=["<|endoftext|>", self.FIM_MIDDLE],
            requires_execution=False,
        )

    def get_prompt(self, doc):
        """Format as FIM prompt."""
        prefix = doc["prefix"]
        suffix = doc["suffix"]
        return f"{self.FIM_PREFIX}{prefix}{self.FIM_SUFFIX}{suffix}{self.FIM_MIDDLE}"

    def postprocess_generation(self, generation, idx):
        """Extract middle portion."""
        if self.FIM_MIDDLE in generation:
            generation = generation.split(self.FIM_MIDDLE)[0]
        return generation.strip()
```

### Pattern 5: Instruction-Tuned Models

For chat/instruction models:

```python
class InstructTask(Task):
    def __init__(self):
        super().__init__(
            stop_words=["</s>", "[/INST]", "```\n"],
            requires_execution=True,
        )

    def get_prompt(self, doc):
        """Format as instruction prompt."""
        instruction = f"""Write a Python function that {doc['description']}.

Function signature: {doc['signature']}

Examples:
{doc['examples']}

Write only the function implementation:"""
        return instruction
```

## Dataset Format Requirements

### For HuggingFace Datasets

Your dataset should include:

```python
{
    "prompt": "def function_name(args):\n    '''Docstring'''",
    "canonical_solution": "    return result",
    "test": "assert function_name(input) == expected",
    "entry_point": "function_name"
}
```

### Creating Dataset Factories

For tasks with multiple configurations:

```python
def create_all_tasks():
    """Create task variants for all languages."""
    tasks = {}
    for lang in ["python", "javascript", "java", "cpp"]:
        tasks[f"my-task-{lang}"] = create_task_class(lang)
    return tasks

def create_task_class(language):
    class LanguageTask(Task):
        DATASET_PATH = "username/dataset"
        DATASET_NAME = language
        # ... implementation
    return LanguageTask

# In __init__.py:
TASK_REGISTRY = {
    **my_module.create_all_tasks(),
}
```

## Testing Your Task

### Unit Tests

Create `tests/test_my_task.py`:

```python
import pytest
from bigcode_eval.tasks import get_task

def test_task_loads():
    task = get_task("my-custom-task")
    assert task is not None

def test_dataset_loads():
    task = get_task("my-custom-task")
    dataset = task.get_dataset()
    assert len(dataset) > 0

def test_prompt_format():
    task = get_task("my-custom-task")
    dataset = task.get_dataset()
    prompt = task.get_prompt(dataset[0])
    assert isinstance(prompt, str)
    assert len(prompt) > 0

def test_postprocess():
    task = get_task("my-custom-task")
    raw = "def foo():\n    return 1\n\nclass Bar:"
    processed = task.postprocess_generation(raw, 0)
    assert "class Bar" not in processed
```

Run tests:
```bash
pytest tests/test_my_task.py -v
```

### Integration Test

```bash
# Small-scale evaluation
accelerate launch main.py \
  --model bigcode/santacoder \
  --tasks my-custom-task \
  --limit 10 \
  --n_samples 5 \
  --allow_code_execution \
  --save_generations
```

## Common Pitfalls

### 1. Missing `requires_execution=True`

If your task uses unit tests, you MUST set:
```python
super().__init__(requires_execution=True, ...)
```

### 2. Incorrect Stop Words

Stop words should match your programming language:

```python
# Python
stop_words=["\nclass", "\ndef", "\n#", "\nif __name__"]

# JavaScript
stop_words=["\nfunction", "\nconst", "\nlet", "\n//"]

# Java
stop_words=["\npublic", "\nprivate", "\nclass", "\n//"]
```

### 3. Not Handling Edge Cases in Postprocessing

```python
def postprocess_generation(self, generation, idx):
    # Handle empty generation
    if not generation or not generation.strip():
        return ""

    # Handle multiple stop words
    for sw in self.stop_words:
        if sw in generation:
            generation = generation[:generation.index(sw)]

    # Remove trailing whitespace
    return generation.rstrip()
```

### 4. Timeout Issues

For complex tests, increase timeout:
```python
results, _ = code_metric.compute(
    references=references,
    predictions=generations,
    timeout=30.0,  # Increase from default
)
```

## Contributing Your Task

1. Fork the repository
2. Create feature branch
3. Implement task following patterns above
4. Add tests
5. Update documentation
6. Submit PR with:
   - Task description
   - Example usage
   - Expected results range




### Benchmarks

# BigCode Evaluation Harness - Benchmark Guide

Comprehensive guide to all benchmarks supported by BigCode Evaluation Harness.

## Code Generation with Unit Tests

These benchmarks test functional correctness by executing generated code against unit tests.

### HumanEval

**Overview**: 164 handwritten Python programming problems created by OpenAI.

**Dataset**: `openai_humaneval` on HuggingFace
**Metric**: pass@k (k=1, 10, 100)
**Problems**: Function completion with docstrings

**Example problem structure**:
```python
def has_close_elements(numbers: List[float], threshold: float) -> bool:
    """Check if in given list of numbers, are any two numbers closer to each other than given threshold.
    >>> has_close_elements([1.0, 2.0, 3.0], 0.5)
    False
    >>> has_close_elements([1.0, 2.8, 3.0, 4.0, 5.0, 2.0], 0.3)
    True
    """
```

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks humaneval \
  --temperature 0.2 \
  --n_samples 200 \
  --batch_size 50 \
  --allow_code_execution
```

**Recommended settings**:
- `temperature`: 0.8 for pass@k with large n_samples, 0.2 for greedy
- `n_samples`: 200 for accurate pass@k estimation
- `max_length_generation`: 512 (sufficient for most problems)

### HumanEval+

**Overview**: Extended HumanEval with 80× more test cases per problem.

**Dataset**: `evalplus/humanevalplus` on HuggingFace
**Why use it**: Catches solutions that pass original tests but fail on edge cases

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks humanevalplus \
  --temperature 0.2 \
  --n_samples 200 \
  --allow_code_execution
```

**Note**: Execution takes longer due to additional tests. Timeout may need adjustment.

### MBPP (Mostly Basic Python Problems)

**Overview**: 1,000 crowd-sourced Python problems designed for entry-level programmers.

**Dataset**: `mbpp` on HuggingFace
**Test split**: 500 problems (indices 11-511)
**Metric**: pass@k

**Problem structure**:
- Task description in English
- 3 automated test cases per problem
- Code solution (ground truth)

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks mbpp \
  --temperature 0.2 \
  --n_samples 200 \
  --allow_code_execution
```

### MBPP+

**Overview**: 399 curated MBPP problems with 35× more test cases.

**Dataset**: `evalplus/mbppplus` on HuggingFace

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks mbppplus \
  --allow_code_execution
```

### MultiPL-E (18 Languages)

**Overview**: HumanEval and MBPP translated to 18 programming languages.

**Languages**: Python, JavaScript, Java, C++, Go, Rust, TypeScript, C#, PHP, Ruby, Swift, Kotlin, Scala, Perl, Julia, Lua, R, Racket

**Task naming**: `multiple-{lang}` where lang is file extension:
- `multiple-py` (Python)
- `multiple-js` (JavaScript)
- `multiple-java` (Java)
- `multiple-cpp` (C++)
- `multiple-go` (Go)
- `multiple-rs` (Rust)
- `multiple-ts` (TypeScript)
- `multiple-cs` (C#)
- `multiple-php` (PHP)
- `multiple-rb` (Ruby)
- `multiple-swift` (Swift)
- `multiple-kt` (Kotlin)
- `multiple-scala` (Scala)
- `multiple-pl` (Perl)
- `multiple-jl` (Julia)
- `multiple-lua` (Lua)
- `multiple-r` (R)
- `multiple-rkt` (Racket)

**Usage with Docker** (recommended for safe execution):
```bash
# Step 1: Generate on host
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks multiple-js,multiple-java,multiple-cpp \
  --generation_only \
  --save_generations \
  --save_generations_path generations.json

# Step 2: Evaluate in Docker
docker pull ghcr.io/bigcode-project/evaluation-harness-multiple
docker run -v $(pwd)/generations.json:/app/generations.json:ro \
  -it evaluation-harness-multiple python3 main.py \
  --tasks multiple-js,multiple-java,multiple-cpp \
  --load_generations_path /app/generations.json \
  --allow_code_execution
```

### APPS

**Overview**: 10,000 Python problems across three difficulty levels.

**Difficulty levels**:
- Introductory: Basic programming
- Interview: Technical interview level
- Competition: Competitive programming

**Tasks**:
- `apps-introductory`
- `apps-interview`
- `apps-competition`

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks apps-introductory \
  --max_length_generation 1024 \
  --allow_code_execution
```

### DS-1000

**Overview**: 1,000 data science problems across 7 Python libraries.

**Libraries**: NumPy, Pandas, SciPy, Scikit-learn, PyTorch, TensorFlow, Matplotlib

**Requirements**:
- Python 3.7.10 specifically
- `pip install -e ".[ds1000]"`
- PyTorch 1.12.1

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks ds1000-all-completion \
  --allow_code_execution
```

### Mercury

**Overview**: 1,889 tasks for evaluating computational efficiency of generated code.

**Requirements**: `pip install lctk sortedcontainers`

**Metric**: Beyond@k (efficiency-based)

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks mercury \
  --allow_code_execution
```

## Code Generation Without Unit Tests

These benchmarks use text-based metrics (BLEU, Exact Match).

### SantaCoder-FIM (Fill-in-the-Middle)

**Overview**: 4,792 fill-in-the-middle tasks for Python, JavaScript, Java.

**Metric**: Exact Match
**Use case**: Evaluating FIM/infilling capabilities

**Tasks**:
- `santacoder_fim`
- `starcoder_fim`

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks santacoder_fim \
  --n_samples 1 \
  --batch_size 1
```

### CoNaLa

**Overview**: Natural language to Python code generation.

**Metric**: BLEU score
**Setting**: Two-shot

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks conala \
  --do_sample False \
  --n_samples 1
```

### Concode

**Overview**: Natural language to Java code generation.

**Metric**: BLEU score

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks concode \
  --do_sample False \
  --n_samples 1
```

## Instruction-Tuned Model Evaluation

### InstructHumanEval

**Overview**: HumanEval reformatted for instruction-following models.

**Usage**:
```bash
accelerate launch main.py \
  --model codellama/CodeLlama-7b-Instruct-hf \
  --tasks instruct-humaneval \
  --instruction_tokens "<s>[INST],</s>,[/INST]" \
  --allow_code_execution
```

### HumanEvalPack

**Overview**: Extends HumanEval to 3 scenarios across 6 languages.

**Scenarios**:
- **Synthesize**: Generate code from docstring
- **Fix**: Fix buggy code
- **Explain**: Generate docstring from code

**Languages**: Python, JavaScript, Java, Go, C++, Rust

**Tasks**:
- `humanevalsynthesize-{lang}`
- `humanevalfix-{lang}`
- `humanevalexplain-{lang}`

**Usage**:
```bash
accelerate launch main.py \
  --model codellama/CodeLlama-7b-Instruct-hf \
  --tasks humanevalsynthesize-python,humanevalfix-python \
  --prompt instruct \
  --allow_code_execution
```

## Math and Reasoning

### PAL (Program-Aided Language Models)

**Overview**: Solve math problems by generating Python code.

**Datasets**: GSM8K, GSM-HARD

**Tasks**:
- `pal-gsm8k-greedy`: Greedy decoding
- `pal-gsm8k-majority_voting`: k=40 majority voting
- `pal-gsmhard-greedy`
- `pal-gsmhard-majority_voting`

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks pal-gsm8k-greedy \
  --max_length_generation 2048 \
  --do_sample False \
  --allow_code_execution
```

**Note**: Requires `max_length_generation >= 2048` due to 8-shot prompts (~1500 tokens).

## Documentation Generation

### CodeXGLUE Code-to-Text

**Overview**: Generate documentation from code.

**Languages**: Python, Go, Ruby, Java, JavaScript, PHP

**Tasks**: `codexglue_code_to_text-{lang}`

**Usage**:
```bash
accelerate launch main.py \
  --model bigcode/starcoder2-7b \
  --tasks codexglue_code_to_text-python \
  --do_sample False \
  --n_samples 1 \
  --batch_size 1
```

## Classification Tasks

### Java Complexity Prediction

**Task**: `java-complexity`

### Code Equivalence Detection

**Task**: `java-clone-detection`

### C Defect Prediction

**Task**: `c-defect-detection`

## Benchmark Selection Guide

| Goal | Recommended Benchmarks |
|------|------------------------|
| Quick sanity check | HumanEval (n_samples=20) |
| Standard evaluation | HumanEval + MBPP |
| Rigorous evaluation | HumanEval+ + MBPP+ |
| Multi-language | MultiPL-E |
| Instruction models | InstructHumanEval, HumanEvalPack |
| FIM/Infilling | SantaCoder-FIM, StarCoder-FIM |
| Data science | DS-1000 |
| Competition-level | APPS |
| Efficiency | Mercury |
| Math reasoning | PAL-GSM8K |

## pass@k Calculation

pass@k estimates probability that at least one of k samples passes all tests:

```
pass@k = E[1 - C(n-c, k) / C(n, k)]
```

Where:
- n = total samples generated
- c = samples that pass all tests
- k = number of samples allowed

**Recommended n_samples by k**:
- pass@1: n >= 20
- pass@10: n >= 100
- pass@100: n >= 200

**Temperature recommendations**:
- pass@1: temperature = 0.2 (near-greedy)
- pass@10, pass@100: temperature = 0.8 (more diverse sampling)




---

## 🚀 Usage

**Reference this template:** `@skill-evaluating-code-models.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
