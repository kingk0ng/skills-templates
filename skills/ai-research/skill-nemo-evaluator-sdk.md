---
id: skill-nemo-evaluator-sdk
type: skill
name: nemo-evaluator-sdk
description: Evaluates LLMs across 100+ benchmarks from 18+ harnesses (MMLU, HumanEval,
  GSM8K, safety, VLM) with multi-backend execution. Use when needing scalable evaluation
  on local Docker, Slurm HPC, or cloud platforms. NVIDIA's enterprise-grade platform
  with container-first architecture for reproducible benchmarking.
category: ai-research
complexity: medium
keywords:
- api
- benchmark
- deployment
- docker
- github
- python
- qa
- security
- testing
capabilities: []
token_estimate: 1719
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,719 -->
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




# nemo-evaluator-sdk

> Evaluates LLMs across 100+ benchmarks from 18+ harnesses (MMLU, HumanEval, GSM8K, safety, VLM) with multi-backend execution. Use when needing scalable evaluation on local Docker, Slurm HPC, or cloud platforms. NVIDIA's enterprise-grade platform with container-first architecture for reproducible benchmarking.

# NeMo Evaluator SDK - Enterprise LLM Benchmarking

## Quick Start

NeMo Evaluator SDK evaluates LLMs across 100+ benchmarks from 18+ harnesses using containerized, reproducible evaluation with multi-backend execution (local Docker, Slurm HPC, Lepton cloud).

**Installation**:
```bash
pip install nemo-evaluator-launcher
```

**Set API key and run evaluation**:
```bash
export NGC_API_KEY=nvapi-your-key-here

# Create minimal config
cat > config.yaml << 'EOF'
defaults:
  - execution: local
  - deployment: none
  - _self_

execution:
  output_dir: ./results

target:
  api_endpoint:
    model_id: meta/llama-3.1-8b-instruct
    url: https://integrate.api.nvidia.com/v1/chat/completions
    api_key_name: NGC_API_KEY

evaluation:
  tasks:
    - name: ifeval
EOF

# Run evaluation
nemo-evaluator-launcher run --config-dir . --config-name config
```

**View available tasks**:
```bash
nemo-evaluator-launcher ls tasks
```

## Common Workflows

### Workflow 1: Evaluate Model on Standard Benchmarks

Run core academic benchmarks (MMLU, GSM8K, IFEval) on any OpenAI-compatible endpoint.

**Checklist**:
```
Standard Evaluation:
- [ ] Step 1: Configure API endpoint
- [ ] Step 2: Select benchmarks
- [ ] Step 3: Run evaluation
- [ ] Step 4: Check results
```

**Step 1: Configure API endpoint**

```yaml
# config.yaml
defaults:
  - execution: local
  - deployment: none
  - _self_

execution:
  output_dir: ./results

target:
  api_endpoint:
    model_id: meta/llama-3.1-8b-instruct
    url: https://integrate.api.nvidia.com/v1/chat/completions
    api_key_name: NGC_API_KEY
```

For self-hosted endpoints (vLLM, TRT-LLM):
```yaml
target:
  api_endpoint:
    model_id: my-model
    url: http://localhost:8000/v1/chat/completions
    api_key_name: ""  # No key needed for local
```

**Step 2: Select benchmarks**

Add tasks to your config:
```yaml
evaluation:
  tasks:
    - name: ifeval           # Instruction following
    - name: gpqa_diamond     # Graduate-level QA
      env_vars:
        HF_TOKEN: HF_TOKEN   # Some tasks need HF token
    - name: gsm8k_cot_instruct  # Math reasoning
    - name: humaneval        # Code generation
```

**Step 3: Run evaluation**

```bash
# Run with config file
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name config

# Override output directory
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name config \
  -o execution.output_dir=./my_results

# Limit samples for quick testing
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name config \
  -o +evaluation.nemo_evaluator_config.config.params.limit_samples=10
```

**Step 4: Check results**

```bash
# Check job status
nemo-evaluator-launcher status <invocation_id>

# List all runs
nemo-evaluator-launcher ls runs

# View results
cat results/<invocation_id>/<task>/artifacts/results.yml
```

### Workflow 2: Run Evaluation on Slurm HPC Cluster

Execute large-scale evaluation on HPC infrastructure.

**Checklist**:
```
Slurm Evaluation:
- [ ] Step 1: Configure Slurm settings
- [ ] Step 2: Set up model deployment
- [ ] Step 3: Launch evaluation
- [ ] Step 4: Monitor job status
```

**Step 1: Configure Slurm settings**

```yaml
# slurm_config.yaml
defaults:
  - execution: slurm
  - deployment: vllm
  - _self_

execution:
  hostname: cluster.example.com
  account: my_slurm_account
  partition: gpu
  output_dir: /shared/results
  walltime: "04:00:00"
  nodes: 1
  gpus_per_node: 8
```

**Step 2: Set up model deployment**

```yaml
deployment:
  checkpoint_path: /shared/models/llama-3.1-8b
  tensor_parallel_size: 2
  data_parallel_size: 4
  max_model_len: 4096

target:
  api_endpoint:
    model_id: llama-3.1-8b
    # URL auto-generated by deployment
```

**Step 3: Launch evaluation**

```bash
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name slurm_config
```

**Step 4: Monitor job status**

```bash
# Check status (queries sacct)
nemo-evaluator-launcher status <invocation_id>

# View detailed info
nemo-evaluator-launcher info <invocation_id>

# Kill if needed
nemo-evaluator-launcher kill <invocation_id>
```

### Workflow 3: Compare Multiple Models

Benchmark multiple models on the same tasks for comparison.

**Checklist**:
```
Model Comparison:
- [ ] Step 1: Create base config
- [ ] Step 2: Run evaluations with overrides
- [ ] Step 3: Export and compare results
```

**Step 1: Create base config**

```yaml
# base_eval.yaml
defaults:
  - execution: local
  - deployment: none
  - _self_

execution:
  output_dir: ./comparison_results

evaluation:
  nemo_evaluator_config:
    config:
      params:
        temperature: 0.01
        parallelism: 4
  tasks:
    - name: mmlu_pro
    - name: gsm8k_cot_instruct
    - name: ifeval
```

**Step 2: Run evaluations with model overrides**

```bash
# Evaluate Llama 3.1 8B
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name base_eval \
  -o target.api_endpoint.model_id=meta/llama-3.1-8b-instruct \
  -o target.api_endpoint.url=https://integrate.api.nvidia.com/v1/chat/completions

# Evaluate Mistral 7B
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name base_eval \
  -o target.api_endpoint.model_id=mistralai/mistral-7b-instruct-v0.3 \
  -o target.api_endpoint.url=https://integrate.api.nvidia.com/v1/chat/completions
```

**Step 3: Export and compare**

```bash
# Export to MLflow
nemo-evaluator-launcher export <invocation_id_1> --dest mlflow
nemo-evaluator-launcher export <invocation_id_2> --dest mlflow

# Export to local JSON
nemo-evaluator-launcher export <invocation_id> --dest local --format json

# Export to Weights & Biases
nemo-evaluator-launcher export <invocation_id> --dest wandb
```

### Workflow 4: Safety and Vision-Language Evaluation

Evaluate models on safety benchmarks and VLM tasks.

**Checklist**:
```
Safety/VLM Evaluation:
- [ ] Step 1: Configure safety tasks
- [ ] Step 2: Set up VLM tasks (if applicable)
- [ ] Step 3: Run evaluation
```

**Step 1: Configure safety tasks**

```yaml
evaluation:
  tasks:
    - name: aegis              # Safety harness
    - name: wildguard          # Safety classification
    - name: garak              # Security probing
```

**Step 2: Configure VLM tasks**

```yaml
# For vision-language models
target:
  api_endpoint:
    type: vlm  # Vision-language endpoint
    model_id: nvidia/llama-3.2-90b-vision-instruct
    url: https://integrate.api.nvidia.com/v1/chat/completions

evaluation:
  tasks:
    - name: ocrbench           # OCR evaluation
    - name: chartqa            # Chart understanding
    - name: mmmu               # Multimodal understanding
```

## When to Use vs Alternatives

**Use NeMo Evaluator when:**
- Need **100+ benchmarks** from 18+ harnesses in one platform
- Running evaluations on **Slurm HPC clusters** or cloud
- Requiring **reproducible** containerized evaluation
- Evaluating against **OpenAI-compatible APIs** (vLLM, TRT-LLM, NIMs)
- Need **enterprise-grade** evaluation with result export (MLflow, W&B)

**Use alternatives instead:**
- **lm-evaluation-harness**: Simpler setup for quick local evaluation
- **bigcode-evaluation-harness**: Focused only on code benchmarks
- **HELM**: Stanford's broader evaluation (fairness, efficiency)
- **Custom scripts**: Highly specialized domain evaluation

## Supported Harnesses and Tasks

| Harness | Task Count | Categories |
|---------|-----------|------------|
| `lm-evaluation-harness` | 60+ | MMLU, GSM8K, HellaSwag, ARC |
| `simple-evals` | 20+ | GPQA, MATH, AIME |
| `bigcode-evaluation-harness` | 25+ | HumanEval, MBPP, MultiPL-E |
| `safety-harness` | 3 | Aegis, WildGuard |
| `garak` | 1 | Security probing |
| `vlmevalkit` | 6+ | OCRBench, ChartQA, MMMU |
| `bfcl` | 6 | Function calling v2/v3 |
| `mtbench` | 2 | Multi-turn conversation |
| `livecodebench` | 10+ | Live coding evaluation |
| `helm` | 15 | Medical domain |
| `nemo-skills` | 8 | Math, science, agentic |

## Common Issues

**Issue: Container pull fails**

Ensure NGC credentials are configured:
```bash
docker login nvcr.io -u '$oauthtoken' -p $NGC_API_KEY
```

**Issue: Task requires environment variable**

Some tasks need HF_TOKEN or JUDGE_API_KEY:
```yaml
evaluation:
  tasks:
    - name: gpqa_diamond
      env_vars:
        HF_TOKEN: HF_TOKEN  # Maps env var name to env var
```

**Issue: Evaluation timeout**

Increase parallelism or reduce samples:
```bash
-o +evaluation.nemo_evaluator_config.config.params.parallelism=8
-o +evaluation.nemo_evaluator_config.config.params.limit_samples=100
```

**Issue: Slurm job not starting**

Check Slurm account and partition:
```yaml
execution:
  account: correct_account
  partition: gpu
  qos: normal  # May need specific QOS
```

**Issue: Different results than expected**

Verify configuration matches reported settings:
```yaml
evaluation:
  nemo_evaluator_config:
    config:
      params:
        temperature: 0.0  # Deterministic
        num_fewshot: 5    # Check paper's fewshot count
```

## CLI Reference

| Command | Description |
|---------|-------------|
| `run` | Execute evaluation with config |
| `status <id>` | Check job status |
| `info <id>` | View detailed job info |
| `ls tasks` | List available benchmarks |
| `ls runs` | List all invocations |
| `export <id>` | Export results (mlflow/wandb/local) |
| `kill <id>` | Terminate running job |

## Configuration Override Examples

```bash
# Override model endpoint
-o target.api_endpoint.model_id=my-model
-o target.api_endpoint.url=http://localhost:8000/v1/chat/completions

# Add evaluation parameters
-o +evaluation.nemo_evaluator_config.config.params.temperature=0.5
-o +evaluation.nemo_evaluator_config.config.params.parallelism=8
-o +evaluation.nemo_evaluator_config.config.params.limit_samples=50

# Change execution settings
-o execution.output_dir=/custom/path
-o execution.mode=parallel

# Dynamically set tasks
-o 'evaluation.tasks=[{name: ifeval}, {name: gsm8k}]'
```

## Python API Usage

For programmatic evaluation without the CLI:

```python
from nemo_evaluator.core.evaluate import evaluate
from nemo_evaluator.api.api_dataclasses import (
    EvaluationConfig,
    EvaluationTarget,
    ApiEndpoint,
    EndpointType,
    ConfigParams
)

# Configure evaluation
eval_config = EvaluationConfig(
    type="mmlu_pro",
    output_dir="./results",
    params=ConfigParams(
        limit_samples=10,
        temperature=0.0,
        max_new_tokens=1024,
        parallelism=4
    )
)

# Configure target endpoint
target_config = EvaluationTarget(
    api_endpoint=ApiEndpoint(
        model_id="meta/llama-3.1-8b-instruct",
        url="https://integrate.api.nvidia.com/v1/chat/completions",
        type=EndpointType.CHAT,
        api_key="nvapi-your-key-here"
    )
)

# Run evaluation
result = evaluate(eval_cfg=eval_config, target_cfg=target_config)
```

## Advanced Topics

**Multi-backend execution**: See [references/execution-backends.md](references/execution-backends.md)
**Configuration deep-dive**: See [references/configuration.md](references/configuration.md)
**Adapter and interceptor system**: See [references/adapter-system.md](references/adapter-system.md)
**Custom benchmark integration**: See [references/custom-benchmarks.md](references/custom-benchmarks.md)

## Requirements

- **Python**: 3.10-3.13
- **Docker**: Required for local execution
- **NGC API Key**: For pulling containers and using NVIDIA Build
- **HF_TOKEN**: Required for some benchmarks (GPQA, MMLU)

## Resources

- **GitHub**: https://github.com/NVIDIA-NeMo/Evaluator
- **NGC Containers**: nvcr.io/nvidia/eval-factory/
- **NVIDIA Build**: https://build.nvidia.com (free hosted models)
- **Documentation**: https://github.com/NVIDIA-NeMo/Evaluator/tree/main/docs


---


## 📚 Reference Materials


### Custom Benchmarks

# Custom Benchmark Integration

NeMo Evaluator supports adding custom benchmarks through Framework Definition Files (FDFs) and custom containers.

## Overview

Custom benchmarks are added by:

1. **Framework Definition Files (FDFs)**: YAML files that define evaluation tasks, commands, and output parsing
2. **Custom Containers**: Package your framework with nemo-evaluator for reproducible execution

> **Note**: NeMo Evaluator does not currently support programmatic harness APIs or custom metric implementations via Python classes. Customization is done through FDFs and containers.

## Framework Definition Files (FDFs)

FDFs are the primary way to add custom evaluations. An FDF declares framework metadata, default commands, and evaluation tasks.

### FDF Structure

```yaml
# framework_def.yaml
framework:
  name: my-custom-framework
  package_name: my_custom_eval

defaults:
  command: "python -m my_custom_eval.run --model-id {model_id} --task {task} --output-dir {output_dir}"

evaluations:
  - name: custom_task_1
    defaults:
      temperature: 0.0
      max_new_tokens: 512
      extra:
        custom_param: value

  - name: custom_task_2
    defaults:
      temperature: 0.7
      max_new_tokens: 1024
```

### Key FDF Components

**Framework section**:
- `name`: Human-readable name for your framework
- `package_name`: Python package name

**Defaults section**:
- `command`: The command template to execute your evaluation
- Placeholders: `{model_id}`, `{task}`, `{output_dir}` are substituted at runtime

**Evaluations section**:
- List of tasks with their default parameters
- Each task can override the framework defaults

### Output Parser

When creating a custom FDF, you need an output parser function that translates your framework's results into NeMo Evaluator's standard schema:

```python
# my_custom_eval/parser.py
def parse_output(output_dir: str) -> dict:
    """
    Parse evaluation results from output_dir.

    Returns dict with metrics in NeMo Evaluator format.
    """
    # Read your framework's output files
    results_file = Path(output_dir) / "results.json"
    with open(results_file) as f:
        raw_results = json.load(f)

    # Transform to standard schema
    return {
        "metrics": {
            "accuracy": raw_results["score"],
            "total_samples": raw_results["num_samples"]
        }
    }
```

## Custom Container Creation

Package your custom framework as a container for reproducibility.

### Dockerfile Example

```dockerfile
# Dockerfile
FROM python:3.10-slim

# Install nemo-evaluator
RUN pip install nemo-evaluator

# Install your custom framework
COPY my_custom_eval/ /opt/my_custom_eval/
RUN pip install /opt/my_custom_eval/

# Copy framework definition
COPY framework_def.yaml /opt/framework_def.yaml

# Set working directory
WORKDIR /opt

ENTRYPOINT ["python", "-m", "nemo_evaluator"]
```

### Build and Push

```bash
docker build -t my-registry/custom-eval:1.0 .
docker push my-registry/custom-eval:1.0
```

### Register in mapping.toml

Add your custom container to the task registry:

```toml
# Add to mapping.toml
[my-custom-framework]
container = "my-registry/custom-eval:1.0"

[my-custom-framework.tasks.chat.custom_task_1]
required_env_vars = []

[my-custom-framework.tasks.chat.custom_task_2]
required_env_vars = ["CUSTOM_API_KEY"]
```

## Using Custom Datasets

### Dataset Mounting

Mount proprietary datasets at runtime rather than baking them into containers:

```yaml
# config.yaml
defaults:
  - execution: local
  - deployment: none
  - _self_

execution:
  output_dir: ./results

evaluation:
  tasks:
    - name: custom_task_1
      dataset_dir: /path/to/local/data
      dataset_mount_path: /data  # Optional, defaults to /datasets
```

The launcher will mount the dataset directory into the container and set `NEMO_EVALUATOR_DATASET_DIR` environment variable.

### Task-Specific Environment Variables

Pass environment variables to specific tasks:

```yaml
evaluation:
  tasks:
    - name: gpqa_diamond
      env_vars:
        HF_TOKEN: HF_TOKEN  # Maps to $HF_TOKEN from host

    - name: custom_task
      env_vars:
        CUSTOM_API_KEY: MY_CUSTOM_KEY
        DATA_PATH: /data/custom.jsonl
```

## Parameter Overrides

Override evaluation parameters at multiple levels:

### Global Overrides

Apply to all tasks:

```yaml
evaluation:
  nemo_evaluator_config:
    config:
      params:
        temperature: 0.0
        max_new_tokens: 512
        parallelism: 4
        request_timeout: 300
```

### Task-Specific Overrides

Override for individual tasks:

```yaml
evaluation:
  tasks:
    - name: humaneval
      nemo_evaluator_config:
        config:
          params:
            temperature: 0.8
            max_new_tokens: 1024
            n_samples: 200  # Task-specific parameter
```

### CLI Overrides

Override at runtime:

```bash
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name config \
  -o +evaluation.nemo_evaluator_config.config.params.limit_samples=10
```

## Testing Custom Benchmarks

### Dry Run

Validate configuration without execution:

```bash
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name custom_config \
  --dry-run
```

### Limited Sample Testing

Test with a small subset first:

```bash
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name custom_config \
  -o +evaluation.nemo_evaluator_config.config.params.limit_samples=5
```

### Check Results

```bash
# View results
cat results/<invocation_id>/<task>/artifacts/results.json

# Check logs
cat results/<invocation_id>/<task>/artifacts/logs/eval.log
```

## Best Practices

1. **Use FDFs**: Define custom benchmarks via Framework Definition Files
2. **Containerize**: Package frameworks as containers for reproducibility
3. **Mount data**: Use volume mounts for datasets instead of baking into images
4. **Test incrementally**: Use `limit_samples` for quick validation
5. **Version containers**: Tag containers with semantic versions
6. **Document parameters**: Include clear documentation in your FDF

## Limitations

Currently **not supported**:
- Custom Python metric classes via plugin system
- Programmatic harness registration via Python API
- Runtime metric injection via configuration

Custom scoring logic must be implemented within your evaluation framework and exposed through the FDF's output parser.

## Example: Complete Custom Setup

```yaml
# custom_eval_config.yaml
defaults:
  - execution: local
  - deployment: none
  - _self_

execution:
  output_dir: ./custom_results

target:
  api_endpoint:
    model_id: my-model
    url: http://localhost:8000/v1/chat/completions
    api_key_name: ""

evaluation:
  nemo_evaluator_config:
    config:
      params:
        parallelism: 4
        request_timeout: 300

  tasks:
    - name: custom_task_1
      dataset_dir: /data/benchmarks
      env_vars:
        DATA_VERSION: v2
      nemo_evaluator_config:
        config:
          params:
            temperature: 0.0
            max_new_tokens: 256
```

Run with:

```bash
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name custom_eval_config
```




### Execution Backends

# Execution Backends

NeMo Evaluator supports three execution backends: Local (Docker), Slurm (HPC), and Lepton (Cloud). Each backend implements the same interface but has different configuration requirements.

## Backend Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    nemo-evaluator-launcher                   │
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │ LocalExecutor │  │ SlurmExecutor │  │ LeptonExecutor│     │
│  │   (Docker)    │  │   (SSH+sbatch)│  │  (Cloud API)  │     │
│  └──────────────┘  └──────────────┘  └──────────────┘       │
│           │                │                 │               │
└───────────┼────────────────┼─────────────────┼───────────────┘
            │                │                 │
            ▼                ▼                 ▼
       ┌─────────┐    ┌───────────┐    ┌────────────┐
       │ Docker  │    │  Slurm    │    │  Lepton AI │
       │ Engine  │    │  Cluster  │    │  Platform  │
       └─────────┘    └───────────┘    └────────────┘
```

## Local Executor (Docker)

The local executor runs evaluation containers on your local machine using Docker.

### Prerequisites

- Docker installed and running
- `docker` command available in PATH
- GPU drivers and nvidia-container-toolkit for GPU tasks

### Configuration

```yaml
defaults:
  - execution: local
  - deployment: none
  - _self_

execution:
  output_dir: ./results
  mode: sequential  # or parallel

  # Docker-specific options
  docker_args:
    - "--gpus=all"
    - "--shm-size=16g"

  # Container resource limits
  memory_limit: "64g"
  cpus: 8
```

### How It Works

1. Launcher reads `mapping.toml` to find container image for task
2. Creates run configuration and mounts volumes
3. Executes `docker run` via subprocess
4. Monitors stage files (`stage.pre-start`, `stage.running`, `stage.exit`)
5. Collects results from mounted output directory

### Example Usage

```bash
# Simple local evaluation
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name local_config

# With GPU allocation
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name local_config \
  -o 'execution.docker_args=["--gpus=all"]'
```

### Status Tracking

Status is tracked via file markers in the output directory:

| File | Meaning |
|------|---------|
| `stage.pre-start` | Container starting |
| `stage.running` | Evaluation in progress |
| `stage.exit` | Evaluation complete |

## Slurm Executor

The Slurm executor submits evaluation jobs to HPC clusters via SSH.

### Prerequisites

- SSH access to cluster head node
- Slurm commands available (`sbatch`, `squeue`, `sacct`)
- NGC containers accessible from compute nodes
- Shared filesystem for results

### Configuration

```yaml
defaults:
  - execution: slurm
  - deployment: vllm  # or sglang, nim, none
  - _self_

execution:
  # SSH connection settings
  hostname: cluster.example.com
  username: myuser  # Optional, uses SSH config
  ssh_key_path: ~/.ssh/id_rsa

  # Slurm job settings
  account: my_account
  partition: gpu
  qos: normal
  nodes: 1
  gpus_per_node: 8
  cpus_per_task: 32
  memory: "256G"
  walltime: "04:00:00"

  # Output settings
  output_dir: /shared/nfs/results

  # Container settings
  container_mounts:
    - "/shared/data:/data:ro"
    - "/shared/models:/models:ro"
```

### Deployment Options

When running on Slurm, you can deploy models alongside evaluation:

```yaml
# vLLM deployment
deployment:
  type: vllm
  checkpoint_path: /models/llama-3.1-8b
  tensor_parallel_size: 4
  max_model_len: 8192
  gpu_memory_utilization: 0.9

# SGLang deployment
deployment:
  type: sglang
  checkpoint_path: /models/llama-3.1-8b
  tensor_parallel_size: 4

# NVIDIA NIM deployment
deployment:
  type: nim
  nim_model_name: meta/llama-3.1-8b-instruct
```

### Job Submission Flow

```
┌─────────────────┐
│ Launcher CLI    │
└────────┬────────┘
         │ SSH
         ▼
┌─────────────────┐
│ Cluster Head    │
│    Node         │
└────────┬────────┘
         │ sbatch
         ▼
┌─────────────────┐
│ Compute Node    │
│                 │
│ ┌─────────────┐ │
│ │ Deployment  │ │
│ │ Container   │ │
│ └─────────────┘ │
│        │        │
│        ▼        │
│ ┌─────────────┐ │
│ │ Evaluation  │ │
│ │ Container   │ │
│ └─────────────┘ │
└─────────────────┘
```

### Status Queries

The Slurm executor queries job status via `sacct`:

```bash
# Status command checks these Slurm states
sacct -j <job_id> --format=JobID,State,ExitCode

# Mapped to ExecutionState:
# PENDING -> pending
# RUNNING -> running
# COMPLETED -> completed
# FAILED -> failed
# CANCELLED -> cancelled
```

### Long-Running Jobs

For long-running evaluations on Slurm, consider:

```yaml
execution:
  walltime: "24:00:00"  # Extended walltime
  # Use caching to resume from interruptions

target:
  api_endpoint:
    adapter_config:
      interceptors:
        - name: caching
          config:
            cache_dir: "/shared/cache"
            reuse_cached_responses: true
```

The caching interceptor helps resume interrupted evaluations by reusing previous API responses.

## Lepton Executor

The Lepton executor runs evaluations on Lepton AI's cloud platform.

### Prerequisites

- Lepton AI account
- `LEPTON_API_TOKEN` environment variable set
- `leptonai` Python package (auto-installed)

### Configuration

```yaml
defaults:
  - execution: lepton
  - deployment: none
  - _self_

execution:
  # Lepton job settings
  resource_shape: gpu.a100-80g
  num_replicas: 1

  # Environment
  env_vars:
    NGC_API_KEY: NGC_API_KEY
    HF_TOKEN: HF_TOKEN
```

### How It Works

1. Launcher creates Lepton job specification
2. Submits job via Lepton API
3. Optionally creates endpoint for model serving
4. Polls job status via API
5. Retrieves results when complete

### Endpoint Management

For evaluating Lepton-hosted models:

```yaml
target:
  api_endpoint:
    type: lepton
    deployment_name: my-llama-deployment
    # URL auto-generated from deployment
```

## Backend Selection Guide

| Use Case | Recommended Backend |
|----------|-------------------|
| Quick local testing | Local |
| Large-scale batch evaluation | Slurm |
| CI/CD pipeline | Local or Lepton |
| Multi-model comparison | Slurm (parallel jobs) |
| Cloud-native workflow | Lepton |
| Self-hosted model evaluation | Local or Slurm |

## Execution Database

All backends share the `ExecutionDB` for tracking jobs:

```
┌─────────────────────────────────────────────┐
│               ExecutionDB (SQLite)           │
│                                              │
│  invocation_id │ job_id │ status │ backend  │
│  ─────────────────────────────────────────  │
│  inv_abc123    │ 12345  │ running │ slurm   │
│  inv_def456    │ cont_1 │ done    │ local   │
└─────────────────────────────────────────────┘
```

Query via CLI:

```bash
# List all invocations
nemo-evaluator-launcher ls runs

# Get specific invocation
nemo-evaluator-launcher info <invocation_id>
```

## Troubleshooting

### Local Executor

**Issue: Docker permission denied**
```bash
sudo usermod -aG docker $USER
newgrp docker
```

**Issue: GPU not available in container**
```bash
# Install nvidia-container-toolkit
sudo apt-get install nvidia-container-toolkit
sudo systemctl restart docker
```

### Slurm Executor

**Issue: SSH connection fails**
```bash
# Test SSH connection
ssh -v cluster.example.com

# Check SSH key permissions
chmod 600 ~/.ssh/id_rsa
```

**Issue: Job stuck in pending**
```bash
# Check queue status
squeue -u $USER

# Check account limits
sacctmgr show associations user=$USER
```

### Lepton Executor

**Issue: API token invalid**
```bash
# Verify token
curl -H "Authorization: Bearer $LEPTON_API_TOKEN" \
  https://api.lepton.ai/v1/jobs
```

**Issue: Resource shape unavailable**
```bash
# List available shapes
lepton shape list
```




### Configuration

# Configuration Reference

NeMo Evaluator uses Hydra for configuration management with a hierarchical override system.

## Configuration Structure

```yaml
# Complete configuration structure
defaults:
  - execution: local      # Execution backend
  - deployment: none      # Model deployment method
  - _self_

execution:
  # Executor-specific settings
  output_dir: ./results
  mode: sequential

target:
  # Model endpoint settings
  api_endpoint:
    model_id: model-name
    url: http://endpoint/v1/chat/completions
    api_key_name: API_KEY
    type: chat  # chat, completions, vlm, embedding
    adapter_config:
      interceptors: []

evaluation:
  # Global evaluation settings
  nemo_evaluator_config:
    config:
      params:
        temperature: 0.0
        parallelism: 4

  # Task list
  tasks:
    - name: task_name
      env_vars: {}
      nemo_evaluator_config: {}  # Per-task overrides
```

## Configuration Sections

### Defaults Section

Selects base configurations for execution and deployment:

```yaml
defaults:
  - execution: local    # Options: local, slurm, lepton
  - deployment: none    # Options: none, vllm, sglang, nim
  - _self_
```

Available execution configs:
- `local` - Docker-based local execution
- `slurm` - HPC cluster via SSH/sbatch
- `lepton` - Lepton AI cloud platform

Available deployment configs:
- `none` - Evaluate existing endpoint
- `vllm` - Deploy model with vLLM
- `sglang` - Deploy model with SGLang
- `nim` - Deploy model with NVIDIA NIM

### Execution Section

Controls how and where evaluations run:

```yaml
execution:
  # Common settings
  output_dir: ./results     # Where to write results
  mode: sequential          # sequential or parallel

  # Local executor specific
  docker_args:
    - "--gpus=all"
    - "--shm-size=16g"
  memory_limit: "64g"
  cpus: 8

  # Slurm executor specific
  hostname: cluster.example.com
  account: my_account
  partition: gpu
  qos: normal
  nodes: 1
  gpus_per_node: 8
  walltime: "04:00:00"

  # Lepton executor specific
  resource_shape: gpu.a100-80g
  num_replicas: 1
```

### Target Section

Specifies the model endpoint to evaluate:

```yaml
target:
  api_endpoint:
    # Required fields
    model_id: meta/llama-3.1-8b-instruct
    url: https://integrate.api.nvidia.com/v1/chat/completions
    api_key_name: NGC_API_KEY  # Environment variable name

    # Optional fields
    type: chat           # chat, completions, vlm, embedding
    timeout: 300         # Request timeout in seconds
    max_retries: 3       # Retry count for failed requests

    # Adapter configuration
    adapter_config:
      interceptors:
        - name: system_message
          config:
            system_message: "You are a helpful assistant."
        - name: caching
          config:
            cache_dir: "./cache"
        - name: reasoning
          config:
            start_reasoning_token: "<think>"
            end_reasoning_token: "</think>"
```

### Evaluation Section

Configures tasks and evaluation parameters:

```yaml
evaluation:
  # Global parameters (apply to all tasks)
  nemo_evaluator_config:
    config:
      params:
        temperature: 0.0          # Sampling temperature
        max_new_tokens: 512       # Max generation length
        parallelism: 4            # Concurrent requests
        limit_samples: null       # Limit samples (null = all)
        num_fewshot: 5            # Few-shot examples
        random_seed: 42           # Random seed

  # Task list
  tasks:
    - name: ifeval

    - name: gpqa_diamond
      env_vars:
        HF_TOKEN: HF_TOKEN  # Task-specific env vars

    - name: gsm8k_cot_instruct
      nemo_evaluator_config:  # Task-specific overrides
        config:
          params:
            temperature: 0.0
            max_new_tokens: 1024
```

## Configuration Override Precedence

Configurations are resolved in this order (highest to lowest):

1. **CLI overrides**: `-o key=value`
2. **Task-specific** `nemo_evaluator_config`
3. **Global** `evaluation.nemo_evaluator_config`
4. **Framework defaults** (in container)
5. **System defaults**

## CLI Override Syntax

### Basic Overrides

```bash
# Override simple values
-o execution.output_dir=/custom/path
-o target.api_endpoint.model_id=my-model

# Override nested values
-o target.api_endpoint.adapter_config.interceptors[0].name=logging
```

### Adding New Values

Use `+` prefix to add values not in base config:

```bash
# Add evaluation parameter
-o +evaluation.nemo_evaluator_config.config.params.limit_samples=100

# Add environment variable
-o +target.api_endpoint.env_vars.CUSTOM_VAR=value
```

### Complex Values

```bash
# Override list/array
-o 'evaluation.tasks=[{name: ifeval}, {name: gsm8k}]'

# Override with special characters (use quotes)
-o 'target.api_endpoint.url="http://localhost:8000/v1/chat/completions"'
```

### Multi-Override

```bash
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name config \
  -o execution.output_dir=/results \
  -o target.api_endpoint.model_id=my-model \
  -o +evaluation.nemo_evaluator_config.config.params.parallelism=8 \
  -o +evaluation.nemo_evaluator_config.config.params.limit_samples=10
```

## Task Configuration

### Task Discovery

List available tasks and their requirements:

```bash
# List all tasks
nemo-evaluator-launcher ls tasks

# Output includes:
# - Task name
# - Container image
# - Endpoint type (chat/completions/vlm)
# - Required environment variables
```

### Task Environment Variables

Some tasks require specific environment variables:

| Task | Required Env Vars |
|------|------------------|
| `gpqa_diamond` | `HF_TOKEN` |
| `mmlu` | `HF_TOKEN` |
| `math_test_500_nemo` | `JUDGE_API_KEY` |
| `aime` | `JUDGE_API_KEY` |
| `slidevqa` | `OPENAI_CLIENT_ID`, `OPENAI_CLIENT_SECRET` |

Configure in task definition:

```yaml
evaluation:
  tasks:
    - name: gpqa_diamond
      env_vars:
        HF_TOKEN: HF_TOKEN  # Maps to $HF_TOKEN from environment

    - name: math_test_500_nemo
      env_vars:
        JUDGE_API_KEY: MY_JUDGE_KEY  # Maps to $MY_JUDGE_KEY
```

### Task-Specific Parameters

Override parameters for specific tasks:

```yaml
evaluation:
  nemo_evaluator_config:
    config:
      params:
        temperature: 0.0  # Global default

  tasks:
    - name: ifeval
      # Uses global temperature: 0.0

    - name: humaneval
      nemo_evaluator_config:
        config:
          params:
            temperature: 0.8    # Override for code generation
            max_new_tokens: 1024
            n_samples: 200      # Multiple samples for pass@k
```

## Adapter Configuration

Adapters intercept and process requests/responses:

```yaml
target:
  api_endpoint:
    adapter_config:
      # Request interceptors (before API call)
      interceptors:
        - name: system_message
          config:
            system_message: "You are a helpful assistant."

        - name: request_logging
          config:
            max_logged_requests: 100
            log_path: "./logs/requests.jsonl"

        - name: caching
          config:
            cache_dir: "./cache"
            cache_ttl: 3600

      # Response interceptors (after API call)
        - name: reasoning
          config:
            start_reasoning_token: "<think>"
            end_reasoning_token: "</think>"
            strip_reasoning: true

        - name: response_logging
          config:
            max_logged_responses: 100

      # Error handling
      log_failed_requests: true
      retry_on_failure: true
      max_retries: 3
```

## Example Configurations

### Minimal Local Evaluation

```yaml
defaults:
  - execution: local
  - deployment: none
  - _self_

execution:
  output_dir: ./results

target:
  api_endpoint:
    model_id: meta/llama-3.1-8b-instruct
    url: https://integrate.api.nvidia.com/v1/chat/completions
    api_key_name: NGC_API_KEY

evaluation:
  tasks:
    - name: ifeval
```

### Production Slurm Evaluation

```yaml
defaults:
  - execution: slurm
  - deployment: vllm
  - _self_

execution:
  hostname: cluster.example.com
  account: research_account
  partition: gpu
  nodes: 2
  gpus_per_node: 8
  walltime: "08:00:00"
  output_dir: /shared/results/$(date +%Y%m%d)

deployment:
  checkpoint_path: /models/llama-3.1-70b
  tensor_parallel_size: 8
  data_parallel_size: 2
  max_model_len: 8192

evaluation:
  nemo_evaluator_config:
    config:
      params:
        parallelism: 16
        temperature: 0.0
  tasks:
    - name: mmlu_pro
    - name: gsm8k_cot_instruct
    - name: ifeval
    - name: gpqa_diamond
      env_vars:
        HF_TOKEN: HF_TOKEN
```

### Quick Testing Configuration

```yaml
defaults:
  - execution: local
  - deployment: none
  - _self_

execution:
  output_dir: ./test_results

target:
  api_endpoint:
    model_id: meta/llama-3.1-8b-instruct
    url: https://integrate.api.nvidia.com/v1/chat/completions
    api_key_name: NGC_API_KEY

evaluation:
  nemo_evaluator_config:
    config:
      params:
        limit_samples: 10  # Only 10 samples per task
        parallelism: 2
  tasks:
    - name: ifeval
    - name: gsm8k_cot_instruct
```

## Validation

### Dry Run

Validate configuration without execution:

```bash
nemo-evaluator-launcher run \
  --config-dir . \
  --config-name config \
  --dry-run
```

### Common Validation Errors

**Missing required field**:
```
ValidationError: target.api_endpoint.model_id is required
```

**Invalid task name**:
```
TaskNotFoundError: Task 'invalid_task' not found in mapping.toml
```

**Missing environment variable**:
```
EnvVarError: Task 'gpqa_diamond' requires HF_TOKEN but it is not set
```




### Adapter System

# Adapter and Interceptor System

NeMo Evaluator uses an adapter system to process requests and responses between the evaluation engine and model endpoints. The `nemo-evaluator` core library provides built-in interceptors for common use cases.

## Architecture Overview

```
┌───────────────────────────────────────────────────────────────┐
│                     Adapter Pipeline                           │
│                                                                │
│  Request  ───►  [Interceptor 1]  ───►  [Interceptor 2]  ───►  │
│                                                                │
│                              │                                 │
│                              ▼                                 │
│                  ┌───────────────────────────────────┐         │
│                  │      Endpoint Interceptor          │        │
│                  │   (HTTP call to Model API)         │        │
│                  └───────────────────────────────────┘         │
│                              │                                 │
│                              ▼                                 │
│  Response  ◄───  [Interceptor 3]  ◄───  [Interceptor 4]  ◄─── │
│                                                                │
└───────────────────────────────────────────────────────────────┘
```

Interceptors execute in order for requests, and in reverse order for responses.

## Configuring Adapters

The adapter configuration is specified in the `target.api_endpoint.adapter_config` section:

```yaml
target:
  api_endpoint:
    model_id: meta/llama-3.1-8b-instruct
    url: https://integrate.api.nvidia.com/v1/chat/completions
    api_key_name: NGC_API_KEY
    adapter_config:
      interceptors:
        - name: system_message
          config:
            system_message: "You are a helpful assistant."
        - name: caching
          config:
            cache_dir: "./cache"
        - name: endpoint
        - name: reasoning
          config:
            start_reasoning_token: "<think>"
            end_reasoning_token: "</think>"
```

## Available Interceptors

### System Message Interceptor

Injects a system prompt into chat requests.

```yaml
- name: system_message
  config:
    system_message: "You are a helpful AI assistant. Think step by step."
```

**Effect**: Prepends a system message to the messages array.

### Request Logging Interceptor

Logs outbound API requests for debugging and analysis.

```yaml
- name: request_logging
  config:
    max_requests: 1000
```

### Caching Interceptor

Caches responses to avoid repeated API calls for identical requests.

```yaml
- name: caching
  config:
    cache_dir: "./evaluation_cache"
    reuse_cached_responses: true
    save_requests: true
    save_responses: true
    max_saved_requests: 1000
    max_saved_responses: 1000
```

### Endpoint Interceptor

Performs the actual HTTP communication with the model endpoint. This is typically added automatically and has no configuration parameters.

```yaml
- name: endpoint
```

### Reasoning Interceptor

Extracts and removes reasoning tokens (e.g., `<think>` tags) from model responses.

```yaml
- name: reasoning
  config:
    start_reasoning_token: "<think>"
    end_reasoning_token: "</think>"
    enable_reasoning_tracking: true
```

**Effect**: Strips reasoning content from the response and tracks it separately.

### Response Logging Interceptor

Logs API responses.

```yaml
- name: response_logging
  config:
    max_responses: 1000
```

### Progress Tracking Interceptor

Reports evaluation progress to an external URL.

```yaml
- name: progress_tracking
  config:
    progress_tracking_url: "http://localhost:3828/progress"
    progress_tracking_interval: 10
```

### Additional Interceptors

Other available interceptors include:
- `payload_modifier`: Transforms request parameters
- `response_stats`: Collects aggregated statistics from responses
- `raise_client_errors`: Handles and raises exceptions for client errors (4xx)

## Interceptor Chain Example

A typical interceptor chain for evaluation:

```yaml
adapter_config:
  interceptors:
    # Pre-endpoint (request processing)
    - name: system_message
      config:
        system_message: "You are a helpful AI assistant."
    - name: request_logging
      config:
        max_requests: 50
    - name: caching
      config:
        cache_dir: "./evaluation_cache"
        reuse_cached_responses: true

    # Endpoint (HTTP call)
    - name: endpoint

    # Post-endpoint (response processing)
    - name: response_logging
      config:
        max_responses: 50
    - name: reasoning
      config:
        start_reasoning_token: "<think>"
        end_reasoning_token: "</think>"
```

## Python API Usage

You can also configure adapters programmatically:

```python
from nemo_evaluator.adapters.adapter_config import AdapterConfig, InterceptorConfig
from nemo_evaluator.api.api_dataclasses import ApiEndpoint, EndpointType

adapter_config = AdapterConfig(
    interceptors=[
        InterceptorConfig(
            name="system_message",
            config={"system_message": "You are a helpful assistant."}
        ),
        InterceptorConfig(
            name="caching",
            config={
                "cache_dir": "./cache",
                "reuse_cached_responses": True
            }
        ),
        InterceptorConfig(name="endpoint"),
        InterceptorConfig(
            name="reasoning",
            config={
                "start_reasoning_token": "<think>",
                "end_reasoning_token": "</think>"
            }
        )
    ]
)

api_endpoint = ApiEndpoint(
    url="http://localhost:8080/v1/chat/completions",
    type=EndpointType.CHAT,
    model_id="my_model",
    adapter_config=adapter_config
)
```

## OpenAI API Compatibility

NeMo Evaluator supports OpenAI-compatible endpoints with different endpoint types:

### Chat Completions

```yaml
target:
  api_endpoint:
    type: chat  # or omit, chat is default
    url: http://endpoint/v1/chat/completions
```

### Text Completions

```yaml
target:
  api_endpoint:
    type: completions
    url: http://endpoint/v1/completions
```

### Vision-Language Models

```yaml
target:
  api_endpoint:
    type: vlm
    url: http://endpoint/v1/chat/completions
```

## Error Handling

Configure error handling via the `log_failed_requests` option:

```yaml
adapter_config:
  log_failed_requests: true
  interceptors:
    - name: raise_client_errors
    # ... other interceptors
```

## Debugging

### Enable Logging Interceptors

Add request and response logging to debug issues:

```yaml
adapter_config:
  interceptors:
    - name: request_logging
      config:
        max_requests: 100
    - name: endpoint
    - name: response_logging
      config:
        max_responses: 100
```

### Common Issues

**Issue: System message not applied**

Ensure the `system_message` interceptor is listed before the `endpoint` interceptor.

**Issue: Cache not being used**

Check that `reuse_cached_responses: true` is set and the cache directory exists:
```yaml
- name: caching
  config:
    cache_dir: "./cache"
    reuse_cached_responses: true
```

**Issue: Reasoning tokens not extracted**

Verify the token patterns match your model's output format:
```yaml
- name: reasoning
  config:
    start_reasoning_token: "<think>"  # Must match model output exactly
    end_reasoning_token: "</think>"
```

## Custom Interceptor Discovery

NeMo Evaluator supports discovering custom interceptors via the `DiscoveryConfig` within `AdapterConfig`. You can specify modules or directories where your custom interceptors are located:

```yaml
adapter_config:
  discovery:
    modules:
      - "my_custom.interceptors"
      - "my_package.adapters"
    dirs:
      - "/path/to/custom/interceptors"
  interceptors:
    - name: my_custom_interceptor
      config:
        custom_option: value
```

Custom interceptors must implement the standard interceptor interface expected by `nemo-evaluator`.

## Additional AdapterConfig Options

Beyond interceptors, `AdapterConfig` supports these additional fields:

| Field | Description |
|-------|-------------|
| `discovery` | Configure custom interceptor discovery |
| `post_eval_hooks` | List of hooks to run after evaluation |
| `endpoint_type` | Default endpoint type (e.g., "chat") |
| `caching_dir` | Legacy option for response caching |
| `generate_html_report` | Generate HTML report of results |
| `log_failed_requests` | Log requests that fail |
| `tracking_requests_stats` | Enable request statistics |
| `html_report_size` | Number of request-response pairs in report |

## Notes

- The interceptor chain order matters - request interceptors run in order, response interceptors run in reverse
- Interceptors can be enabled/disabled via the `enabled` field in `InterceptorConfig`
- For complex custom logic, consider packaging as a custom container with your interceptors pre-installed




---

## 🚀 Usage

**Reference this template:** `@skill-nemo-evaluator-sdk.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
