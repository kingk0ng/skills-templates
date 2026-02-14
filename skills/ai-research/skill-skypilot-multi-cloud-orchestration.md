---
id: skill-skypilot-multi-cloud-orchestration
type: skill
name: skypilot-multi-cloud-orchestration
description: Multi-cloud orchestration for ML workloads with automatic cost optimization.
  Use when you need to run training or batch jobs across multiple clouds, leverage
  spot instances with auto-recovery, or optimize GPU costs across providers.
category: ai-research
complexity: medium
keywords:
- api
- deploy
- github
- k8s
- kubernetes
- optimization
- python
capabilities: []
token_estimate: 1582
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,582 -->
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




# skypilot-multi-cloud-orchestration

> Multi-cloud orchestration for ML workloads with automatic cost optimization. Use when you need to run training or batch jobs across multiple clouds, leverage spot instances with auto-recovery, or optimize GPU costs across providers.

# SkyPilot Multi-Cloud Orchestration

Comprehensive guide to running ML workloads across clouds with automatic cost optimization using SkyPilot.

## When to use SkyPilot

**Use SkyPilot when:**
- Running ML workloads across multiple clouds (AWS, GCP, Azure, etc.)
- Need cost optimization with automatic cloud/region selection
- Running long jobs on spot instances with auto-recovery
- Managing distributed multi-node training
- Want unified interface for 20+ cloud providers
- Need to avoid vendor lock-in

**Key features:**
- **Multi-cloud**: AWS, GCP, Azure, Kubernetes, Lambda, RunPod, 20+ providers
- **Cost optimization**: Automatic cheapest cloud/region selection
- **Spot instances**: 3-6x cost savings with automatic recovery
- **Distributed training**: Multi-node jobs with gang scheduling
- **Managed jobs**: Auto-recovery, checkpointing, fault tolerance
- **Sky Serve**: Model serving with autoscaling

**Use alternatives instead:**
- **Modal**: For simpler serverless GPU with Python-native API
- **RunPod**: For single-cloud persistent pods
- **Kubernetes**: For existing K8s infrastructure
- **Ray**: For pure Ray-based orchestration

## Quick start

### Installation

```bash
pip install "skypilot[aws,gcp,azure,kubernetes]"

# Verify cloud credentials
sky check
```

### Hello World

Create `hello.yaml`:
```yaml
resources:
  accelerators: T4:1

run: |
  nvidia-smi
  echo "Hello from SkyPilot!"
```

Launch:
```bash
sky launch -c hello hello.yaml

# SSH to cluster
ssh hello

# Terminate
sky down hello
```

## Core concepts

### Task YAML structure

```yaml
# Task name (optional)
name: my-task

# Resource requirements
resources:
  cloud: aws              # Optional: auto-select if omitted
  region: us-west-2       # Optional: auto-select if omitted
  accelerators: A100:4    # GPU type and count
  cpus: 8+                # Minimum CPUs
  memory: 32+             # Minimum memory (GB)
  use_spot: true          # Use spot instances
  disk_size: 256          # Disk size (GB)

# Number of nodes for distributed training
num_nodes: 2

# Working directory (synced to ~/sky_workdir)
workdir: .

# Setup commands (run once)
setup: |
  pip install -r requirements.txt

# Run commands
run: |
  python train.py
```

### Key commands

| Command | Purpose |
|---------|---------|
| `sky launch` | Launch cluster and run task |
| `sky exec` | Run task on existing cluster |
| `sky status` | Show cluster status |
| `sky stop` | Stop cluster (preserve state) |
| `sky down` | Terminate cluster |
| `sky logs` | View task logs |
| `sky queue` | Show job queue |
| `sky jobs launch` | Launch managed job |
| `sky serve up` | Deploy serving endpoint |

## GPU configuration

### Available accelerators

```yaml
# NVIDIA GPUs
accelerators: T4:1
accelerators: L4:1
accelerators: A10G:1
accelerators: L40S:1
accelerators: A100:4
accelerators: A100-80GB:8
accelerators: H100:8

# Cloud-specific
accelerators: V100:4         # AWS/GCP
accelerators: TPU-v4-8       # GCP TPUs
```

### GPU fallbacks

```yaml
resources:
  accelerators:
    H100: 8
    A100-80GB: 8
    A100: 8
  any_of:
    - cloud: gcp
    - cloud: aws
    - cloud: azure
```

### Spot instances

```yaml
resources:
  accelerators: A100:8
  use_spot: true
  spot_recovery: FAILOVER  # Auto-recover on preemption
```

## Cluster management

### Launch and execute

```bash
# Launch new cluster
sky launch -c mycluster task.yaml

# Run on existing cluster (skip setup)
sky exec mycluster another_task.yaml

# Interactive SSH
ssh mycluster

# Stream logs
sky logs mycluster
```

### Autostop

```yaml
resources:
  accelerators: A100:4
  autostop:
    idle_minutes: 30
    down: true  # Terminate instead of stop
```

```bash
# Set autostop via CLI
sky autostop mycluster -i 30 --down
```

### Cluster status

```bash
# All clusters
sky status

# Detailed view
sky status -a
```

## Distributed training

### Multi-node setup

```yaml
resources:
  accelerators: A100:8

num_nodes: 4  # 4 nodes × 8 GPUs = 32 GPUs total

setup: |
  pip install torch torchvision

run: |
  torchrun \
    --nnodes=$SKYPILOT_NUM_NODES \
    --nproc_per_node=$SKYPILOT_NUM_GPUS_PER_NODE \
    --node_rank=$SKYPILOT_NODE_RANK \
    --master_addr=$(echo "$SKYPILOT_NODE_IPS" | head -n1) \
    --master_port=12355 \
    train.py
```

### Environment variables

| Variable | Description |
|----------|-------------|
| `SKYPILOT_NODE_RANK` | Node index (0 to num_nodes-1) |
| `SKYPILOT_NODE_IPS` | Newline-separated IP addresses |
| `SKYPILOT_NUM_NODES` | Total number of nodes |
| `SKYPILOT_NUM_GPUS_PER_NODE` | GPUs per node |

### Head-node-only execution

```bash
run: |
  if [ "${SKYPILOT_NODE_RANK}" == "0" ]; then
    python orchestrate.py
  fi
```

## Managed jobs

### Spot recovery

```bash
# Launch managed job with spot recovery
sky jobs launch -n my-job train.yaml
```

### Checkpointing

```yaml
name: training-job

file_mounts:
  /checkpoints:
    name: my-checkpoints
    store: s3
    mode: MOUNT

resources:
  accelerators: A100:8
  use_spot: true

run: |
  python train.py \
    --checkpoint-dir /checkpoints \
    --resume-from-latest
```

### Job management

```bash
# List jobs
sky jobs queue

# View logs
sky jobs logs my-job

# Cancel job
sky jobs cancel my-job
```

## File mounts and storage

### Local file sync

```yaml
workdir: ./my-project  # Synced to ~/sky_workdir

file_mounts:
  /data/config.yaml: ./config.yaml
  ~/.vimrc: ~/.vimrc
```

### Cloud storage

```yaml
file_mounts:
  # Mount S3 bucket
  /datasets:
    source: s3://my-bucket/datasets
    mode: MOUNT  # Stream from S3

  # Copy GCS bucket
  /models:
    source: gs://my-bucket/models
    mode: COPY  # Pre-fetch to disk

  # Cached mount (fast writes)
  /outputs:
    name: my-outputs
    store: s3
    mode: MOUNT_CACHED
```

### Storage modes

| Mode | Description | Best For |
|------|-------------|----------|
| `MOUNT` | Stream from cloud | Large datasets, read-heavy |
| `COPY` | Pre-fetch to disk | Small files, random access |
| `MOUNT_CACHED` | Cache with async upload | Checkpoints, outputs |

## Sky Serve (Model Serving)

### Basic service

```yaml
# service.yaml
service:
  readiness_probe: /health
  replica_policy:
    min_replicas: 1
    max_replicas: 10
    target_qps_per_replica: 2.0

resources:
  accelerators: A100:1

run: |
  python -m vllm.entrypoints.openai.api_server \
    --model meta-llama/Llama-2-7b-chat-hf \
    --port 8000
```

```bash
# Deploy
sky serve up -n my-service service.yaml

# Check status
sky serve status

# Get endpoint
sky serve status my-service
```

### Autoscaling policies

```yaml
service:
  replica_policy:
    min_replicas: 1
    max_replicas: 10
    target_qps_per_replica: 2.0
    upscale_delay_seconds: 60
    downscale_delay_seconds: 300
  load_balancing_policy: round_robin
```

## Cost optimization

### Automatic cloud selection

```yaml
# SkyPilot finds cheapest option
resources:
  accelerators: A100:8
  # No cloud specified - auto-select cheapest
```

```bash
# Show optimizer decision
sky launch task.yaml --dryrun
```

### Cloud preferences

```yaml
resources:
  accelerators: A100:8
  any_of:
    - cloud: gcp
      region: us-central1
    - cloud: aws
      region: us-east-1
    - cloud: azure
```

### Environment variables

```yaml
envs:
  HF_TOKEN: $HF_TOKEN  # Inherited from local env
  WANDB_API_KEY: $WANDB_API_KEY

# Or use secrets
secrets:
  - HF_TOKEN
  - WANDB_API_KEY
```

## Common workflows

### Workflow 1: Fine-tuning with checkpoints

```yaml
name: llm-finetune

file_mounts:
  /checkpoints:
    name: finetune-checkpoints
    store: s3
    mode: MOUNT_CACHED

resources:
  accelerators: A100:8
  use_spot: true

setup: |
  pip install transformers accelerate

run: |
  python train.py \
    --checkpoint-dir /checkpoints \
    --resume
```

### Workflow 2: Hyperparameter sweep

```yaml
name: hp-sweep-${RUN_ID}

envs:
  RUN_ID: 0
  LEARNING_RATE: 1e-4
  BATCH_SIZE: 32

resources:
  accelerators: A100:1
  use_spot: true

run: |
  python train.py \
    --lr $LEARNING_RATE \
    --batch-size $BATCH_SIZE \
    --run-id $RUN_ID
```

```bash
# Launch multiple jobs
for i in {1..10}; do
  sky jobs launch sweep.yaml \
    --env RUN_ID=$i \
    --env LEARNING_RATE=$(python -c "import random; print(10**random.uniform(-5,-3))")
done
```

## Debugging

```bash
# SSH to cluster
ssh mycluster

# View logs
sky logs mycluster

# Check job queue
sky queue mycluster

# View managed job logs
sky jobs logs my-job
```

## Common issues

| Issue | Solution |
|-------|----------|
| Quota exceeded | Request quota increase, try different region |
| Spot preemption | Use `sky jobs launch` for auto-recovery |
| Slow file sync | Use `MOUNT_CACHED` mode for outputs |
| GPU not available | Use `any_of` for fallback clouds |

## References

- **[Advanced Usage](references/advanced-usage.md)** - Multi-cloud, optimization, production patterns
- **[Troubleshooting](references/troubleshooting.md)** - Common issues and solutions

## Resources

- **Documentation**: https://docs.skypilot.co
- **GitHub**: https://github.com/skypilot-org/skypilot
- **Slack**: https://slack.skypilot.co
- **Examples**: https://github.com/skypilot-org/skypilot/tree/master/examples


---


## 📚 Reference Materials


### Advanced Usage

# SkyPilot Advanced Usage Guide

## Multi-Cloud Strategies

### Cloud selection patterns

```yaml
# Prefer specific clouds in order
resources:
  accelerators: A100:8
  any_of:
    - cloud: gcp
      region: us-central1
    - cloud: aws
      region: us-west-2
    - cloud: azure
      region: westus2
```

### Wildcard regions

```yaml
resources:
  cloud: aws
  region: us-*  # Any US region
  accelerators: A100:8
```

### Kubernetes + Cloud fallback

```yaml
resources:
  accelerators: A100:8
  any_of:
    - cloud: kubernetes
    - cloud: aws
    - cloud: gcp
```

## Advanced Resource Configuration

### Instance type constraints

```yaml
resources:
  instance_type: p4d.24xlarge  # Specific instance
  # OR
  cpus: 32+
  memory: 128+
  accelerators: A100:8
```

### Disk configuration

```yaml
resources:
  disk_size: 500  # GB
  disk_tier: best  # low, medium, high, ultra, best
```

### Network tier

```yaml
resources:
  network_tier: best  # High-performance networking
```

## Production Managed Jobs

### Job configuration

```yaml
name: production-training

resources:
  accelerators: H100:8
  use_spot: true
  spot_recovery: FAILOVER

# Retry configuration
max_restarts_on_errors: 3
```

### Controller scaling

For large-scale deployments (hundreds of jobs):

```bash
# Increase controller memory
sky jobs launch --controller-resources memory=32
```

### Static credentials

Use non-expiring credentials for controllers:

```bash
# AWS: Use IAM role or long-lived access keys
# GCP: Use service account JSON key
# Azure: Use service principal
```

## Advanced File Mounts

### Git repository workdir

```yaml
workdir:
  url: https://github.com/user/repo.git
  ref: main
  # For private repos, set GIT_TOKEN env var
```

### Multiple storage backends

```yaml
file_mounts:
  /data/s3:
    source: s3://my-bucket/data
    mode: MOUNT

  /data/gcs:
    source: gs://my-bucket/data
    mode: MOUNT

  /outputs:
    name: training-outputs
    store: s3
    mode: MOUNT_CACHED
```

### Rsync exclude patterns

```yaml
workdir: .

# Use .skyignore or .gitignore for excludes
```

Create `.skyignore`:
```
__pycache__/
*.pyc
.git/
.env
node_modules/
```

## Distributed Training Patterns

### PyTorch DDP

```yaml
num_nodes: 4

resources:
  accelerators: A100:8

run: |
  torchrun \
    --nnodes=$SKYPILOT_NUM_NODES \
    --nproc_per_node=$SKYPILOT_NUM_GPUS_PER_NODE \
    --node_rank=$SKYPILOT_NODE_RANK \
    --master_addr=$(echo "$SKYPILOT_NODE_IPS" | head -n1) \
    --master_port=12355 \
    train.py
```

### DeepSpeed

```yaml
num_nodes: 4

resources:
  accelerators: A100:8

setup: |
  pip install deepspeed

run: |
  # Create hostfile
  echo "$SKYPILOT_NODE_IPS" | awk '{print $1 " slots=8"}' > /tmp/hostfile

  deepspeed --hostfile=/tmp/hostfile \
    --num_nodes=$SKYPILOT_NUM_NODES \
    --num_gpus=$SKYPILOT_NUM_GPUS_PER_NODE \
    train.py --deepspeed ds_config.json
```

### Ray Train

```yaml
num_nodes: 4

resources:
  accelerators: A100:8

run: |
  # Head node starts Ray head
  if [ "${SKYPILOT_NODE_RANK}" == "0" ]; then
    ray start --head --port=6379
    # Wait for workers
    sleep 30
    python train_ray.py
  else
    ray start --address=$(echo "$SKYPILOT_NODE_IPS" | head -n1):6379
  fi
```

## Sky Serve Advanced

### Multi-replica serving

```yaml
service:
  readiness_probe:
    path: /health
    initial_delay_seconds: 60
    period_seconds: 10

  replica_policy:
    min_replicas: 2
    max_replicas: 20
    target_qps_per_replica: 5.0
    upscale_delay_seconds: 60
    downscale_delay_seconds: 300

  load_balancing_policy: round_robin  # or least_connections
```

### Blue-green deployment

```bash
# Deploy new version
sky serve up -n my-service-v2 service_v2.yaml

# Test new version
curl https://my-service-v2.skypilot.cloud/health

# Switch traffic (update DNS/load balancer)
# Then terminate old version
sky serve down my-service-v1
```

### Service with multiple accelerator options

```yaml
service:
  replica_policy:
    min_replicas: 1
    max_replicas: 5

resources:
  accelerators:
    L40S: 1
    A100: 1
    A10G: 1
  any_of:
    - cloud: aws
    - cloud: gcp
```

## Cost Optimization

### Spot instance strategies

```yaml
resources:
  accelerators: A100:8
  use_spot: true
  spot_recovery: FAILOVER  # or FAILOVER_NO_WAIT

# Always checkpoint for spot jobs
file_mounts:
  /checkpoints:
    name: spot-checkpoints
    store: s3
    mode: MOUNT_CACHED
```

### Reserved instance hints

```yaml
resources:
  accelerators: A100:8
  # SkyPilot considers reserved instances in cost calculation
```

### Budget constraints

```bash
# Dry run to see cost estimate
sky launch task.yaml --dryrun

# Set max cluster cost (future feature)
# sky launch task.yaml --max-cost-per-hour 50
```

## Kubernetes Integration

### Using existing clusters

```bash
# Configure kubeconfig
export KUBECONFIG=~/.kube/config

# Verify
sky check kubernetes
```

### Pod configuration

```yaml
resources:
  cloud: kubernetes
  accelerators: A100:1

config:
  kubernetes:
    pod_config:
      spec:
        runtimeClassName: nvidia
        tolerations:
          - key: "nvidia.com/gpu"
            operator: "Exists"
            effect: "NoSchedule"
```

### Multi-cluster

```yaml
resources:
  any_of:
    - cloud: kubernetes
      infra: cluster1
    - cloud: kubernetes
      infra: cluster2
    - cloud: aws
```

## API Server Deployment

### Team setup

```bash
# Start API server
sky api serve --host 0.0.0.0 --port 8000

# Connect clients
sky api login --endpoint https://your-server:8000
```

### Authentication

```bash
# Create service account
sky api create-service-account my-service

# Use token in CI/CD
export SKYPILOT_API_TOKEN=...
sky launch task.yaml
```

## Advanced CLI Patterns

### Parallel cluster operations

```bash
# Launch multiple clusters in parallel
for i in {1..10}; do
  sky launch -c cluster-$i task.yaml --detach &
done
wait
```

### Batch job submission

```bash
# Submit many jobs
for config in configs/*.yaml; do
  name=$(basename $config .yaml)
  sky jobs launch -n $name $config
done

# Monitor all jobs
sky jobs queue
```

### Conditional execution

```yaml
run: |
  # Only run on head node
  if [ "${SKYPILOT_NODE_RANK}" == "0" ]; then
    python main.py
  else
    python worker.py
  fi
```

## Environment Management

### Environment variables

```yaml
envs:
  WANDB_PROJECT: my-project
  HF_TOKEN: $HF_TOKEN  # Inherit from local
  CUDA_VISIBLE_DEVICES: "0,1,2,3"

# Secrets (hidden in logs)
secrets:
  - WANDB_API_KEY
  - HF_TOKEN
```

### Config overrides

```yaml
config:
  # Override global config
  jobs:
    controller:
      resources:
        memory: 32
```

## Monitoring and Observability

### Log streaming

```bash
# Stream logs
sky logs mycluster

# Follow specific job
sky logs mycluster 1

# Managed job logs
sky jobs logs my-job --follow
```

### Integration with W&B/MLflow

```yaml
envs:
  WANDB_API_KEY: $WANDB_API_KEY
  WANDB_PROJECT: my-project

run: |
  wandb login $WANDB_API_KEY
  python train.py --wandb
```

## Debugging

### SSH access

```bash
# SSH to head node
ssh mycluster

# SSH to worker node
ssh mycluster-worker1

# Port forwarding
ssh -L 8080:localhost:8080 mycluster
```

### Interactive debugging

```bash
# Launch interactive cluster
sky launch -c debug --gpus A100:1

# SSH and debug
ssh debug
```

### Job inspection

```bash
# View job queue
sky queue mycluster

# Cancel specific job
sky cancel mycluster 1

# View job details
sky logs mycluster 1
```




### Troubleshooting

# SkyPilot Troubleshooting Guide

## Installation Issues

### Cloud credentials not found

**Error**: `sky check` shows clouds as disabled

**Solutions**:
```bash
# AWS
aws configure
# Verify: aws sts get-caller-identity

# GCP
gcloud auth application-default login
# Verify: gcloud auth list

# Azure
az login
az account set -s <subscription-id>

# Kubernetes
export KUBECONFIG=~/.kube/config
kubectl get nodes

# Re-check after configuration
sky check
```

### Permission errors

**Error**: `PermissionError` or `AccessDenied`

**Solutions**:
```bash
# AWS: Ensure IAM permissions include EC2, S3, IAM
# Required policies: AmazonEC2FullAccess, AmazonS3FullAccess, IAMFullAccess

# GCP: Ensure roles include Compute Admin, Storage Admin
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="user:email@example.com" \
  --role="roles/compute.admin"

# Azure: Ensure Contributor role on subscription
az role assignment create \
  --assignee email@example.com \
  --role Contributor \
  --scope /subscriptions/SUBSCRIPTION_ID
```

## Cluster Launch Issues

### Quota exceeded

**Error**: `Quota exceeded for resource`

**Solutions**:
```yaml
# Try different region
resources:
  accelerators: A100:8
  any_of:
    - cloud: gcp
      region: us-west1
    - cloud: gcp
      region: europe-west4
    - cloud: aws
      region: us-east-1

# Or request quota increase from cloud provider
```

```bash
# Check quota before launching
sky show-gpus --cloud gcp
```

### GPU not available

**Error**: `No resources available in region`

**Solutions**:
```yaml
# Use fallback accelerators
resources:
  accelerators:
    H100: 8
    A100-80GB: 8
    A100: 8
  any_of:
    - cloud: gcp
    - cloud: aws
    - cloud: azure
```

```bash
# Check GPU availability
sky show-gpus A100
sky show-gpus --cloud aws
```

### Instance type not found

**Error**: `Instance type 'xyz' not found`

**Solutions**:
```yaml
# Let SkyPilot choose instance automatically
resources:
  accelerators: A100:8
  cpus: 96+
  memory: 512+
  # Don't specify instance_type unless necessary
```

### Cluster stuck in INIT

**Error**: Cluster stays in INIT state

**Solutions**:
```bash
# Check cluster logs
sky logs mycluster --status

# SSH and check manually
ssh mycluster
journalctl -u sky-supervisor

# Terminate and retry
sky down mycluster
sky launch -c mycluster task.yaml
```

## Setup Command Issues

### Setup script fails

**Error**: Setup commands fail during provisioning

**Solutions**:
```yaml
# Add error handling and retries
setup: |
  set -e  # Exit on error

  # Retry pip installs
  for i in {1..3}; do
    pip install torch transformers && break
    echo "Retry $i..."
    sleep 10
  done

  # Verify installation
  python -c "import torch; print(torch.__version__)"
```

### Conda environment issues

**Error**: Conda not found or environment issues

**Solutions**:
```yaml
setup: |
  # Initialize conda for bash
  source ~/.bashrc

  # Or use full path
  ~/miniconda3/bin/conda create -n myenv python=3.10 -y
  ~/miniconda3/bin/conda activate myenv
```

### CUDA version mismatch

**Error**: `CUDA driver version is insufficient`

**Solutions**:
```yaml
setup: |
  # Install specific CUDA version
  pip install torch==2.1.0+cu121 -f https://download.pytorch.org/whl/torch_stable.html

  # Verify CUDA
  python -c "import torch; print(torch.cuda.is_available())"
```

## Distributed Training Issues

### Nodes can't communicate

**Error**: Connection refused between nodes

**Solutions**:
```yaml
run: |
  # Debug: Print all node IPs
  echo "All nodes: $SKYPILOT_NODE_IPS"
  echo "My rank: $SKYPILOT_NODE_RANK"

  # Wait for all nodes to be ready
  sleep 30

  # Use correct master address
  MASTER_ADDR=$(echo "$SKYPILOT_NODE_IPS" | head -n1)
  echo "Master: $MASTER_ADDR"
```

### torchrun fails

**Error**: `torch.distributed` errors

**Solutions**:
```yaml
run: |
  # Ensure correct environment variables
  export NCCL_DEBUG=INFO
  export NCCL_IB_DISABLE=1  # Try if InfiniBand issues

  torchrun \
    --nnodes=$SKYPILOT_NUM_NODES \
    --nproc_per_node=$SKYPILOT_NUM_GPUS_PER_NODE \
    --node_rank=$SKYPILOT_NODE_RANK \
    --master_addr=$(echo "$SKYPILOT_NODE_IPS" | head -n1) \
    --master_port=12355 \
    --rdzv_backend=c10d \
    train.py
```

### DeepSpeed hostfile errors

**Error**: `Invalid hostfile` or connection errors

**Solutions**:
```yaml
run: |
  # Create proper hostfile
  echo "$SKYPILOT_NODE_IPS" | while read ip; do
    echo "$ip slots=$SKYPILOT_NUM_GPUS_PER_NODE"
  done > /tmp/hostfile

  cat /tmp/hostfile  # Debug

  deepspeed --hostfile=/tmp/hostfile train.py
```

## File Mount Issues

### Mount fails

**Error**: `Failed to mount storage`

**Solutions**:
```yaml
# Verify bucket exists and credentials are valid
file_mounts:
  /data:
    source: s3://my-bucket/data
    mode: MOUNT

# Check bucket access
# aws s3 ls s3://my-bucket/
```

### Slow file access

**Problem**: Reading from mount is very slow

**Solutions**:
```yaml
# Use COPY mode for small datasets
file_mounts:
  /data:
    source: s3://bucket/data
    mode: COPY  # Pre-fetch to local disk

# Use MOUNT_CACHED for outputs
file_mounts:
  /outputs:
    name: outputs
    store: s3
    mode: MOUNT_CACHED  # Cached writes
```

### Storage not persisting

**Error**: Data lost after cluster restart

**Solutions**:
```yaml
# Use named storage (persists across clusters)
file_mounts:
  /persistent:
    name: my-persistent-storage
    store: s3
    mode: MOUNT

# Data in ~/sky_workdir is NOT persisted
# Always use file_mounts for persistent data
```

## Managed Job Issues

### Job keeps failing

**Error**: Job fails and doesn't recover

**Solutions**:
```yaml
# Enable spot recovery
resources:
  use_spot: true
  spot_recovery: FAILOVER

# Add retry logic
max_restarts_on_errors: 5

# Implement checkpointing
run: |
  python train.py \
    --checkpoint-dir /checkpoints \
    --resume-from-latest
```

### Job stuck in pending

**Error**: Job stays in PENDING state

**Solutions**:
```bash
# Check job controller status
sky jobs controller status

# View controller logs
sky jobs controller logs

# Restart controller if needed
sky jobs controller restart
```

### Checkpoint not resuming

**Error**: Training restarts from beginning

**Solutions**:
```yaml
file_mounts:
  /checkpoints:
    name: training-checkpoints
    store: s3
    mode: MOUNT_CACHED

run: |
  # Check for existing checkpoint
  if [ -d "/checkpoints/latest" ]; then
    RESUME_FLAG="--resume /checkpoints/latest"
  else
    RESUME_FLAG=""
  fi

  python train.py $RESUME_FLAG --checkpoint-dir /checkpoints
```

## Sky Serve Issues

### Service not accessible

**Error**: Cannot reach service endpoint

**Solutions**:
```bash
# Check service status
sky serve status my-service

# View replica logs
sky serve logs my-service

# Check readiness probe
sky serve status my-service --endpoint
```

### Replicas keep crashing

**Error**: Replicas fail health checks

**Solutions**:
```yaml
service:
  readiness_probe:
    path: /health
    initial_delay_seconds: 120  # Increase for slow model loading
    period_seconds: 30
    timeout_seconds: 10

run: |
  # Ensure health endpoint exists
  python -c "
  from fastapi import FastAPI
  app = FastAPI()

  @app.get('/health')
  def health():
      return {'status': 'ok'}
  "
```

### Autoscaling not working

**Problem**: Service doesn't scale up/down

**Solutions**:
```yaml
service:
  replica_policy:
    min_replicas: 1
    max_replicas: 10
    target_qps_per_replica: 2.0
    upscale_delay_seconds: 30   # Faster scale up
    downscale_delay_seconds: 60  # Faster scale down

# Monitor metrics
# sky serve status my-service
```

## SSH and Access Issues

### Cannot SSH to cluster

**Error**: `Connection refused` or timeout

**Solutions**:
```bash
# Verify cluster is running
sky status

# Try with verbose output
ssh -v mycluster

# Check SSH key
ls -la ~/.ssh/sky-key*

# Regenerate SSH key if needed
sky launch -c test --dryrun  # Regenerates key
```

### Port forwarding fails

**Error**: Cannot forward ports

**Solutions**:
```bash
# Correct syntax
ssh -L 8080:localhost:8080 mycluster

# For Jupyter
ssh -L 8888:localhost:8888 mycluster

# Multiple ports
ssh -L 8080:localhost:8080 -L 6006:localhost:6006 mycluster
```

## Cost and Billing Issues

### Unexpected charges

**Problem**: Higher than expected costs

**Solutions**:
```bash
# Always terminate unused clusters
sky down --all

# Set autostop
sky autostop mycluster -i 30 --down

# Use spot instances
resources:
  use_spot: true
```

### Spot instance preempted

**Error**: Instance terminated unexpectedly

**Solutions**:
```yaml
# Use managed jobs for automatic recovery
# sky jobs launch instead of sky launch

resources:
  use_spot: true
  spot_recovery: FAILOVER  # Auto-failover to another region/cloud

# Always checkpoint frequently when using spot
```

## Debugging Commands

### View cluster state

```bash
# Cluster status
sky status
sky status -a  # Show all details

# Cluster resources
sky show-gpus

# Cloud credentials
sky check
```

### View logs

```bash
# Task logs
sky logs mycluster
sky logs mycluster 1  # Specific job

# Managed job logs
sky jobs logs my-job
sky jobs logs my-job --follow

# Service logs
sky serve logs my-service
```

### Inspect cluster

```bash
# SSH to cluster
ssh mycluster

# Check GPU status
nvidia-smi

# Check processes
ps aux | grep python

# Check disk space
df -h
```

## Common Error Messages

| Error | Cause | Solution |
|-------|-------|----------|
| `No launchable resources` | No available instances | Try different region/cloud |
| `Quota exceeded` | Cloud quota limit | Request increase or use different cloud |
| `Setup failed` | Script error | Check logs, add error handling |
| `Connection refused` | Network/firewall | Check security groups, wait for init |
| `CUDA OOM` | Out of GPU memory | Use larger GPU or reduce batch size |
| `Spot preempted` | Spot instance reclaimed | Use managed jobs for auto-recovery |
| `Mount failed` | Storage access issue | Check credentials and bucket exists |

## Getting Help

1. **Documentation**: https://docs.skypilot.co
2. **GitHub Issues**: https://github.com/skypilot-org/skypilot/issues
3. **Slack**: https://slack.skypilot.co
4. **Examples**: https://github.com/skypilot-org/skypilot/tree/master/examples

### Reporting Issues

Include:
- SkyPilot version: `sky --version`
- Python version: `python --version`
- Cloud provider and region
- Full error traceback
- Task YAML (sanitized)
- Output of `sky check`




---

## 🚀 Usage

**Reference this template:** `@skill-skypilot-multi-cloud-orchestration.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
