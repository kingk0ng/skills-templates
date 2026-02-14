---
id: skill-lambda-labs-gpu-cloud
type: skill
name: lambda-labs-gpu-cloud
description: Reserved and on-demand GPU cloud instances for ML training and inference.
  Use when you need dedicated GPU instances with simple SSH access, persistent filesystems,
  or high-performance multi-node clusters for large-scale training.
category: ai-research
complexity: medium
keywords:
- api
- git
- github
- go
- optimization
- performance
- python
capabilities: []
token_estimate: 1944
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,944 -->
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




# lambda-labs-gpu-cloud

> Reserved and on-demand GPU cloud instances for ML training and inference. Use when you need dedicated GPU instances with simple SSH access, persistent filesystems, or high-performance multi-node clusters for large-scale training.

# Lambda Labs GPU Cloud

Comprehensive guide to running ML workloads on Lambda Labs GPU cloud with on-demand instances and 1-Click Clusters.

## When to use Lambda Labs

**Use Lambda Labs when:**
- Need dedicated GPU instances with full SSH access
- Running long training jobs (hours to days)
- Want simple pricing with no egress fees
- Need persistent storage across sessions
- Require high-performance multi-node clusters (16-512 GPUs)
- Want pre-installed ML stack (Lambda Stack with PyTorch, CUDA, NCCL)

**Key features:**
- **GPU variety**: B200, H100, GH200, A100, A10, A6000, V100
- **Lambda Stack**: Pre-installed PyTorch, TensorFlow, CUDA, cuDNN, NCCL
- **Persistent filesystems**: Keep data across instance restarts
- **1-Click Clusters**: 16-512 GPU Slurm clusters with InfiniBand
- **Simple pricing**: Pay-per-minute, no egress fees
- **Global regions**: 12+ regions worldwide

**Use alternatives instead:**
- **Modal**: For serverless, auto-scaling workloads
- **SkyPilot**: For multi-cloud orchestration and cost optimization
- **RunPod**: For cheaper spot instances and serverless endpoints
- **Vast.ai**: For GPU marketplace with lowest prices

## Quick start

### Account setup

1. Create account at https://lambda.ai
2. Add payment method
3. Generate API key from dashboard
4. Add SSH key (required before launching instances)

### Launch via console

1. Go to https://cloud.lambda.ai/instances
2. Click "Launch instance"
3. Select GPU type and region
4. Choose SSH key
5. Optionally attach filesystem
6. Launch and wait 3-15 minutes

### Connect via SSH

```bash
# Get instance IP from console
ssh ubuntu@<INSTANCE-IP>

# Or with specific key
ssh -i ~/.ssh/lambda_key ubuntu@<INSTANCE-IP>
```

## GPU instances

### Available GPUs

| GPU | VRAM | Price/GPU/hr | Best For |
|-----|------|--------------|----------|
| B200 SXM6 | 180 GB | $4.99 | Largest models, fastest training |
| H100 SXM | 80 GB | $2.99-3.29 | Large model training |
| H100 PCIe | 80 GB | $2.49 | Cost-effective H100 |
| GH200 | 96 GB | $1.49 | Single-GPU large models |
| A100 80GB | 80 GB | $1.79 | Production training |
| A100 40GB | 40 GB | $1.29 | Standard training |
| A10 | 24 GB | $0.75 | Inference, fine-tuning |
| A6000 | 48 GB | $0.80 | Good VRAM/price ratio |
| V100 | 16 GB | $0.55 | Budget training |

### Instance configurations

```
8x GPU: Best for distributed training (DDP, FSDP)
4x GPU: Large models, multi-GPU training
2x GPU: Medium workloads
1x GPU: Fine-tuning, inference, development
```

### Launch times

- Single-GPU: 3-5 minutes
- Multi-GPU: 10-15 minutes

## Lambda Stack

All instances come with Lambda Stack pre-installed:

```bash
# Included software
- Ubuntu 22.04 LTS
- NVIDIA drivers (latest)
- CUDA 12.x
- cuDNN 8.x
- NCCL (for multi-GPU)
- PyTorch (latest)
- TensorFlow (latest)
- JAX
- JupyterLab
```

### Verify installation

```bash
# Check GPU
nvidia-smi

# Check PyTorch
python -c "import torch; print(torch.cuda.is_available())"

# Check CUDA version
nvcc --version
```

## Python API

### Installation

```bash
pip install lambda-cloud-client
```

### Authentication

```python
import os
import lambda_cloud_client

# Configure with API key
configuration = lambda_cloud_client.Configuration(
    host="https://cloud.lambdalabs.com/api/v1",
    access_token=os.environ["LAMBDA_API_KEY"]
)
```

### List available instances

```python
with lambda_cloud_client.ApiClient(configuration) as api_client:
    api = lambda_cloud_client.DefaultApi(api_client)

    # Get available instance types
    types = api.instance_types()
    for name, info in types.data.items():
        print(f"{name}: {info.instance_type.description}")
```

### Launch instance

```python
from lambda_cloud_client.models import LaunchInstanceRequest

request = LaunchInstanceRequest(
    region_name="us-west-1",
    instance_type_name="gpu_1x_h100_sxm5",
    ssh_key_names=["my-ssh-key"],
    file_system_names=["my-filesystem"],  # Optional
    name="training-job"
)

response = api.launch_instance(request)
instance_id = response.data.instance_ids[0]
print(f"Launched: {instance_id}")
```

### List running instances

```python
instances = api.list_instances()
for instance in instances.data:
    print(f"{instance.name}: {instance.ip} ({instance.status})")
```

### Terminate instance

```python
from lambda_cloud_client.models import TerminateInstanceRequest

request = TerminateInstanceRequest(
    instance_ids=[instance_id]
)
api.terminate_instance(request)
```

### SSH key management

```python
from lambda_cloud_client.models import AddSshKeyRequest

# Add SSH key
request = AddSshKeyRequest(
    name="my-key",
    public_key="ssh-rsa AAAA..."
)
api.add_ssh_key(request)

# List keys
keys = api.list_ssh_keys()

# Delete key
api.delete_ssh_key(key_id)
```

## CLI with curl

### List instance types

```bash
curl -u $LAMBDA_API_KEY: \
  https://cloud.lambdalabs.com/api/v1/instance-types | jq
```

### Launch instance

```bash
curl -u $LAMBDA_API_KEY: \
  -X POST https://cloud.lambdalabs.com/api/v1/instance-operations/launch \
  -H "Content-Type: application/json" \
  -d '{
    "region_name": "us-west-1",
    "instance_type_name": "gpu_1x_h100_sxm5",
    "ssh_key_names": ["my-key"]
  }' | jq
```

### Terminate instance

```bash
curl -u $LAMBDA_API_KEY: \
  -X POST https://cloud.lambdalabs.com/api/v1/instance-operations/terminate \
  -H "Content-Type: application/json" \
  -d '{"instance_ids": ["<INSTANCE-ID>"]}' | jq
```

## Persistent storage

### Filesystems

Filesystems persist data across instance restarts:

```bash
# Mount location
/lambda/nfs/<FILESYSTEM_NAME>

# Example: save checkpoints
python train.py --checkpoint-dir /lambda/nfs/my-storage/checkpoints
```

### Create filesystem

1. Go to Storage in Lambda console
2. Click "Create filesystem"
3. Select region (must match instance region)
4. Name and create

### Attach to instance

Filesystems must be attached at instance launch time:
- Via console: Select filesystem when launching
- Via API: Include `file_system_names` in launch request

### Best practices

```bash
# Store on filesystem (persists)
/lambda/nfs/storage/
  ├── datasets/
  ├── checkpoints/
  ├── models/
  └── outputs/

# Local SSD (faster, ephemeral)
/home/ubuntu/
  └── working/  # Temporary files
```

## SSH configuration

### Add SSH key

```bash
# Generate key locally
ssh-keygen -t ed25519 -f ~/.ssh/lambda_key

# Add public key to Lambda console
# Or via API
```

### Multiple keys

```bash
# On instance, add more keys
echo 'ssh-rsa AAAA...' >> ~/.ssh/authorized_keys
```

### Import from GitHub

```bash
# On instance
ssh-import-id gh:username
```

### SSH tunneling

```bash
# Forward Jupyter
ssh -L 8888:localhost:8888 ubuntu@<IP>

# Forward TensorBoard
ssh -L 6006:localhost:6006 ubuntu@<IP>

# Multiple ports
ssh -L 8888:localhost:8888 -L 6006:localhost:6006 ubuntu@<IP>
```

## JupyterLab

### Launch from console

1. Go to Instances page
2. Click "Launch" in Cloud IDE column
3. JupyterLab opens in browser

### Manual access

```bash
# On instance
jupyter lab --ip=0.0.0.0 --port=8888

# From local machine with tunnel
ssh -L 8888:localhost:8888 ubuntu@<IP>
# Open http://localhost:8888
```

## Training workflows

### Single-GPU training

```bash
# SSH to instance
ssh ubuntu@<IP>

# Clone repo
git clone https://github.com/user/project
cd project

# Install dependencies
pip install -r requirements.txt

# Train
python train.py --epochs 100 --checkpoint-dir /lambda/nfs/storage/checkpoints
```

### Multi-GPU training (single node)

```python
# train_ddp.py
import torch
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP

def main():
    dist.init_process_group("nccl")
    rank = dist.get_rank()
    device = rank % torch.cuda.device_count()

    model = MyModel().to(device)
    model = DDP(model, device_ids=[device])

    # Training loop...

if __name__ == "__main__":
    main()
```

```bash
# Launch with torchrun (8 GPUs)
torchrun --nproc_per_node=8 train_ddp.py
```

### Checkpoint to filesystem

```python
import os

checkpoint_dir = "/lambda/nfs/my-storage/checkpoints"
os.makedirs(checkpoint_dir, exist_ok=True)

# Save checkpoint
torch.save({
    'epoch': epoch,
    'model_state_dict': model.state_dict(),
    'optimizer_state_dict': optimizer.state_dict(),
    'loss': loss,
}, f"{checkpoint_dir}/checkpoint_{epoch}.pt")
```

## 1-Click Clusters

### Overview

High-performance Slurm clusters with:
- 16-512 NVIDIA H100 or B200 GPUs
- NVIDIA Quantum-2 400 Gb/s InfiniBand
- GPUDirect RDMA at 3200 Gb/s
- Pre-installed distributed ML stack

### Included software

- Ubuntu 22.04 LTS + Lambda Stack
- NCCL, Open MPI
- PyTorch with DDP and FSDP
- TensorFlow
- OFED drivers

### Storage

- 24 TB NVMe per compute node (ephemeral)
- Lambda filesystems for persistent data

### Multi-node training

```bash
# On Slurm cluster
srun --nodes=4 --ntasks-per-node=8 --gpus-per-node=8 \
  torchrun --nnodes=4 --nproc_per_node=8 \
  --rdzv_backend=c10d --rdzv_endpoint=$MASTER_ADDR:29500 \
  train.py
```

## Networking

### Bandwidth

- Inter-instance (same region): up to 200 Gbps
- Internet outbound: 20 Gbps max

### Firewall

- Default: Only port 22 (SSH) open
- Configure additional ports in Lambda console
- ICMP traffic allowed by default

### Private IPs

```bash
# Find private IP
ip addr show | grep 'inet '
```

## Common workflows

### Workflow 1: Fine-tuning LLM

```bash
# 1. Launch 8x H100 instance with filesystem

# 2. SSH and setup
ssh ubuntu@<IP>
pip install transformers accelerate peft

# 3. Download model to filesystem
python -c "
from transformers import AutoModelForCausalLM
model = AutoModelForCausalLM.from_pretrained('meta-llama/Llama-2-7b-hf')
model.save_pretrained('/lambda/nfs/storage/models/llama-2-7b')
"

# 4. Fine-tune with checkpoints on filesystem
accelerate launch --num_processes 8 train.py \
  --model_path /lambda/nfs/storage/models/llama-2-7b \
  --output_dir /lambda/nfs/storage/outputs \
  --checkpoint_dir /lambda/nfs/storage/checkpoints
```

### Workflow 2: Batch inference

```bash
# 1. Launch A10 instance (cost-effective for inference)

# 2. Run inference
python inference.py \
  --model /lambda/nfs/storage/models/fine-tuned \
  --input /lambda/nfs/storage/data/inputs.jsonl \
  --output /lambda/nfs/storage/data/outputs.jsonl
```

## Cost optimization

### Choose right GPU

| Task | Recommended GPU |
|------|-----------------|
| LLM fine-tuning (7B) | A100 40GB |
| LLM fine-tuning (70B) | 8x H100 |
| Inference | A10, A6000 |
| Development | V100, A10 |
| Maximum performance | B200 |

### Reduce costs

1. **Use filesystems**: Avoid re-downloading data
2. **Checkpoint frequently**: Resume interrupted training
3. **Right-size**: Don't over-provision GPUs
4. **Terminate idle**: No auto-stop, manually terminate

### Monitor usage

- Dashboard shows real-time GPU utilization
- API for programmatic monitoring

## Common issues

| Issue | Solution |
|-------|----------|
| Instance won't launch | Check region availability, try different GPU |
| SSH connection refused | Wait for instance to initialize (3-15 min) |
| Data lost after terminate | Use persistent filesystems |
| Slow data transfer | Use filesystem in same region |
| GPU not detected | Reboot instance, check drivers |

## References

- **[Advanced Usage](references/advanced-usage.md)** - Multi-node training, API automation
- **[Troubleshooting](references/troubleshooting.md)** - Common issues and solutions

## Resources

- **Documentation**: https://docs.lambda.ai
- **Console**: https://cloud.lambda.ai
- **Pricing**: https://lambda.ai/instances
- **Support**: https://support.lambdalabs.com
- **Blog**: https://lambda.ai/blog


---


## 📚 Reference Materials


### Advanced Usage

# Lambda Labs Advanced Usage Guide

## Multi-Node Distributed Training

### PyTorch DDP across nodes

```python
# train_multi_node.py
import os
import torch
import torch.distributed as dist
from torch.nn.parallel import DistributedDataParallel as DDP

def setup_distributed():
    # Environment variables set by launcher
    rank = int(os.environ["RANK"])
    world_size = int(os.environ["WORLD_SIZE"])
    local_rank = int(os.environ["LOCAL_RANK"])

    dist.init_process_group(
        backend="nccl",
        rank=rank,
        world_size=world_size
    )

    torch.cuda.set_device(local_rank)
    return rank, world_size, local_rank

def main():
    rank, world_size, local_rank = setup_distributed()

    model = MyModel().cuda(local_rank)
    model = DDP(model, device_ids=[local_rank])

    # Training loop with synchronized gradients
    for epoch in range(num_epochs):
        train_one_epoch(model, dataloader)

        # Save checkpoint on rank 0 only
        if rank == 0:
            torch.save(model.module.state_dict(), f"checkpoint_{epoch}.pt")

    dist.destroy_process_group()

if __name__ == "__main__":
    main()
```

### Launch on multiple instances

```bash
# On Node 0 (master)
export MASTER_ADDR=<NODE0_PRIVATE_IP>
export MASTER_PORT=29500

torchrun \
    --nnodes=2 \
    --nproc_per_node=8 \
    --node_rank=0 \
    --master_addr=$MASTER_ADDR \
    --master_port=$MASTER_PORT \
    train_multi_node.py

# On Node 1
export MASTER_ADDR=<NODE0_PRIVATE_IP>
export MASTER_PORT=29500

torchrun \
    --nnodes=2 \
    --nproc_per_node=8 \
    --node_rank=1 \
    --master_addr=$MASTER_ADDR \
    --master_port=$MASTER_PORT \
    train_multi_node.py
```

### FSDP for large models

```python
from torch.distributed.fsdp import FullyShardedDataParallel as FSDP
from torch.distributed.fsdp.wrap import transformer_auto_wrap_policy
from transformers.models.llama.modeling_llama import LlamaDecoderLayer

# Wrap policy for transformer models
auto_wrap_policy = functools.partial(
    transformer_auto_wrap_policy,
    transformer_layer_cls={LlamaDecoderLayer}
)

model = FSDP(
    model,
    auto_wrap_policy=auto_wrap_policy,
    mixed_precision=MixedPrecision(
        param_dtype=torch.bfloat16,
        reduce_dtype=torch.bfloat16,
        buffer_dtype=torch.bfloat16,
    ),
    device_id=local_rank,
)
```

### DeepSpeed ZeRO

```python
# ds_config.json
{
    "train_batch_size": 64,
    "gradient_accumulation_steps": 4,
    "fp16": {"enabled": true},
    "zero_optimization": {
        "stage": 3,
        "offload_optimizer": {"device": "cpu"},
        "offload_param": {"device": "cpu"}
    }
}
```

```bash
# Launch with DeepSpeed
deepspeed --num_nodes=2 \
    --num_gpus=8 \
    --hostfile=hostfile.txt \
    train.py --deepspeed ds_config.json
```

### Hostfile for multi-node

```bash
# hostfile.txt
node0_ip slots=8
node1_ip slots=8
```

## API Automation

### Auto-launch training jobs

```python
import os
import time
import lambda_cloud_client
from lambda_cloud_client.models import LaunchInstanceRequest

class LambdaJobManager:
    def __init__(self, api_key: str):
        self.config = lambda_cloud_client.Configuration(
            host="https://cloud.lambdalabs.com/api/v1",
            access_token=api_key
        )

    def find_available_gpu(self, gpu_types: list[str], regions: list[str] = None):
        """Find first available GPU type across regions."""
        with lambda_cloud_client.ApiClient(self.config) as client:
            api = lambda_cloud_client.DefaultApi(client)
            types = api.instance_types()

            for gpu_type in gpu_types:
                if gpu_type in types.data:
                    info = types.data[gpu_type]
                    for region in info.regions_with_capacity_available:
                        if regions is None or region.name in regions:
                            return gpu_type, region.name

        return None, None

    def launch_and_wait(self, instance_type: str, region: str,
                        ssh_key: str, filesystem: str = None,
                        timeout: int = 900) -> dict:
        """Launch instance and wait for it to be ready."""
        with lambda_cloud_client.ApiClient(self.config) as client:
            api = lambda_cloud_client.DefaultApi(client)

            request = LaunchInstanceRequest(
                region_name=region,
                instance_type_name=instance_type,
                ssh_key_names=[ssh_key],
                file_system_names=[filesystem] if filesystem else [],
            )

            response = api.launch_instance(request)
            instance_id = response.data.instance_ids[0]

            # Poll until ready
            start = time.time()
            while time.time() - start < timeout:
                instance = api.get_instance(instance_id)
                if instance.data.status == "active":
                    return {
                        "id": instance_id,
                        "ip": instance.data.ip,
                        "status": "active"
                    }
                time.sleep(30)

            raise TimeoutError(f"Instance {instance_id} not ready after {timeout}s")

    def terminate(self, instance_ids: list[str]):
        """Terminate instances."""
        from lambda_cloud_client.models import TerminateInstanceRequest

        with lambda_cloud_client.ApiClient(self.config) as client:
            api = lambda_cloud_client.DefaultApi(client)
            request = TerminateInstanceRequest(instance_ids=instance_ids)
            api.terminate_instance(request)


# Usage
manager = LambdaJobManager(os.environ["LAMBDA_API_KEY"])

# Find available H100 or A100
gpu_type, region = manager.find_available_gpu(
    ["gpu_8x_h100_sxm5", "gpu_8x_a100_80gb_sxm4"],
    regions=["us-west-1", "us-east-1"]
)

if gpu_type:
    instance = manager.launch_and_wait(
        gpu_type, region,
        ssh_key="my-key",
        filesystem="training-data"
    )
    print(f"Ready: ssh ubuntu@{instance['ip']}")
```

### Batch job submission

```python
import subprocess
import paramiko

def run_remote_job(ip: str, ssh_key_path: str, commands: list[str]):
    """Execute commands on remote instance."""
    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    client.connect(ip, username="ubuntu", key_filename=ssh_key_path)

    for cmd in commands:
        stdin, stdout, stderr = client.exec_command(cmd)
        print(stdout.read().decode())
        if stderr.read():
            print(f"Error: {stderr.read().decode()}")

    client.close()

# Submit training job
commands = [
    "cd /lambda/nfs/storage/project",
    "git pull",
    "pip install -r requirements.txt",
    "nohup torchrun --nproc_per_node=8 train.py > train.log 2>&1 &"
]

run_remote_job(instance["ip"], "~/.ssh/lambda_key", commands)
```

### Monitor training progress

```python
def monitor_job(ip: str, ssh_key_path: str, log_file: str = "train.log"):
    """Stream training logs from remote instance."""
    import time

    client = paramiko.SSHClient()
    client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    client.connect(ip, username="ubuntu", key_filename=ssh_key_path)

    # Tail log file
    stdin, stdout, stderr = client.exec_command(f"tail -f {log_file}")

    try:
        for line in stdout:
            print(line.strip())
    except KeyboardInterrupt:
        pass
    finally:
        client.close()
```

## 1-Click Cluster Workflows

### Slurm job submission

```bash
#!/bin/bash
#SBATCH --job-name=llm-training
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=8
#SBATCH --gpus-per-node=8
#SBATCH --time=24:00:00
#SBATCH --output=logs/%j.out
#SBATCH --error=logs/%j.err

# Set up distributed environment
export MASTER_ADDR=$(scontrol show hostnames $SLURM_JOB_NODELIST | head -n 1)
export MASTER_PORT=29500

# Launch training
srun torchrun \
    --nnodes=$SLURM_NNODES \
    --nproc_per_node=$SLURM_GPUS_PER_NODE \
    --rdzv_backend=c10d \
    --rdzv_endpoint=$MASTER_ADDR:$MASTER_PORT \
    train.py \
    --config config.yaml
```

### Interactive cluster session

```bash
# Request interactive session
srun --nodes=1 --ntasks=1 --gpus=8 --time=4:00:00 --pty bash

# Now on compute node with 8 GPUs
nvidia-smi
python train.py
```

### Monitoring cluster jobs

```bash
# View job queue
squeue

# View job details
scontrol show job <JOB_ID>

# Cancel job
scancel <JOB_ID>

# View node status
sinfo

# View GPU usage across cluster
srun --nodes=4 nvidia-smi --query-gpu=name,utilization.gpu --format=csv
```

## Advanced Filesystem Usage

### Data staging workflow

```bash
# Stage data from S3 to filesystem (one-time)
aws s3 sync s3://my-bucket/dataset /lambda/nfs/storage/datasets/

# Or use rclone
rclone sync s3:my-bucket/dataset /lambda/nfs/storage/datasets/
```

### Shared filesystem across instances

```python
# Instance 1: Write checkpoints
checkpoint_path = "/lambda/nfs/shared/checkpoints/model_step_1000.pt"
torch.save(model.state_dict(), checkpoint_path)

# Instance 2: Read checkpoints
model.load_state_dict(torch.load(checkpoint_path))
```

### Filesystem best practices

```bash
# Organize for ML workflows
/lambda/nfs/storage/
├── datasets/
│   ├── raw/           # Original data
│   └── processed/     # Preprocessed data
├── models/
│   ├── pretrained/    # Base models
│   └── fine-tuned/    # Your trained models
├── checkpoints/
│   └── experiment_1/  # Per-experiment checkpoints
├── logs/
│   └── tensorboard/   # Training logs
└── outputs/
    └── inference/     # Inference results
```

## Environment Management

### Custom Python environments

```bash
# Don't modify system Python, create venv
python -m venv ~/myenv
source ~/myenv/bin/activate

# Install packages
pip install torch transformers accelerate

# Save to filesystem for reuse
cp -r ~/myenv /lambda/nfs/storage/envs/myenv
```

### Conda environments

```bash
# Install miniconda (if not present)
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh -b -p ~/miniconda3

# Create environment
~/miniconda3/bin/conda create -n ml python=3.10 pytorch pytorch-cuda=12.1 -c pytorch -c nvidia -y

# Activate
source ~/miniconda3/bin/activate ml
```

### Docker containers

```bash
# Pull and run NVIDIA container
docker run --gpus all -it --rm \
    -v /lambda/nfs/storage:/data \
    nvcr.io/nvidia/pytorch:24.01-py3

# Run training in container
docker run --gpus all -d \
    -v /lambda/nfs/storage:/data \
    -v $(pwd):/workspace \
    nvcr.io/nvidia/pytorch:24.01-py3 \
    python /workspace/train.py
```

## Monitoring and Observability

### GPU monitoring

```bash
# Real-time GPU stats
watch -n 1 nvidia-smi

# GPU utilization over time
nvidia-smi dmon -s u -d 1

# Detailed GPU info
nvidia-smi -q
```

### System monitoring

```bash
# CPU and memory
htop

# Disk I/O
iostat -x 1

# Network
iftop

# All resources
glances
```

### TensorBoard integration

```bash
# Start TensorBoard
tensorboard --logdir /lambda/nfs/storage/logs --port 6006 --bind_all

# SSH tunnel from local machine
ssh -L 6006:localhost:6006 ubuntu@<IP>

# Access at http://localhost:6006
```

### Weights & Biases integration

```python
import wandb

# Initialize with API key
wandb.login(key=os.environ["WANDB_API_KEY"])

# Start run
wandb.init(
    project="lambda-training",
    config={"learning_rate": 1e-4, "epochs": 100}
)

# Log metrics
wandb.log({"loss": loss, "accuracy": acc})

# Save artifacts to filesystem + W&B
wandb.save("/lambda/nfs/storage/checkpoints/best_model.pt")
```

## Cost Optimization Strategies

### Checkpointing for interruption recovery

```python
import os

def save_checkpoint(model, optimizer, epoch, loss, path):
    torch.save({
        'epoch': epoch,
        'model_state_dict': model.state_dict(),
        'optimizer_state_dict': optimizer.state_dict(),
        'loss': loss,
    }, path)

def load_checkpoint(path, model, optimizer):
    if os.path.exists(path):
        checkpoint = torch.load(path)
        model.load_state_dict(checkpoint['model_state_dict'])
        optimizer.load_state_dict(checkpoint['optimizer_state_dict'])
        return checkpoint['epoch'], checkpoint['loss']
    return 0, float('inf')

# Save every N steps to filesystem
checkpoint_path = "/lambda/nfs/storage/checkpoints/latest.pt"
if step % 1000 == 0:
    save_checkpoint(model, optimizer, epoch, loss, checkpoint_path)
```

### Instance selection by workload

```python
def recommend_instance(model_params: int, batch_size: int, task: str) -> str:
    """Recommend Lambda instance based on workload."""

    if task == "inference":
        if model_params < 7e9:
            return "gpu_1x_a10"  # $0.75/hr
        elif model_params < 13e9:
            return "gpu_1x_a6000"  # $0.80/hr
        else:
            return "gpu_1x_h100_pcie"  # $2.49/hr

    elif task == "fine-tuning":
        if model_params < 7e9:
            return "gpu_1x_a100"  # $1.29/hr
        elif model_params < 13e9:
            return "gpu_4x_a100"  # $5.16/hr
        else:
            return "gpu_8x_h100_sxm5"  # $23.92/hr

    elif task == "pretraining":
        return "gpu_8x_h100_sxm5"  # Maximum performance

    return "gpu_1x_a100"  # Default
```

### Auto-terminate idle instances

```python
import time
from datetime import datetime, timedelta

def auto_terminate_idle(api_key: str, idle_threshold_hours: float = 2):
    """Terminate instances idle for too long."""
    manager = LambdaJobManager(api_key)

    with lambda_cloud_client.ApiClient(manager.config) as client:
        api = lambda_cloud_client.DefaultApi(client)
        instances = api.list_instances()

        for instance in instances.data:
            # Check if instance has been running without activity
            # (You'd need to track this separately)
            launch_time = instance.launched_at
            if datetime.now() - launch_time > timedelta(hours=idle_threshold_hours):
                print(f"Terminating idle instance: {instance.id}")
                manager.terminate([instance.id])
```

## Security Best Practices

### SSH key rotation

```bash
# Generate new key pair
ssh-keygen -t ed25519 -f ~/.ssh/lambda_key_new -C "lambda-$(date +%Y%m)"

# Add new key via Lambda console or API
# Update authorized_keys on running instances
ssh ubuntu@<IP> "echo '$(cat ~/.ssh/lambda_key_new.pub)' >> ~/.ssh/authorized_keys"

# Test new key
ssh -i ~/.ssh/lambda_key_new ubuntu@<IP>

# Remove old key from Lambda console
```

### Firewall configuration

```bash
# Lambda console: Only open necessary ports
# Recommended:
# - 22 (SSH) - Always needed
# - 6006 (TensorBoard) - If using
# - 8888 (Jupyter) - If using
# - 29500 (PyTorch distributed) - For multi-node only
```

### Secrets management

```bash
# Don't hardcode API keys in code
# Use environment variables
export HF_TOKEN="hf_..."
export WANDB_API_KEY="..."

# Or use .env file (add to .gitignore)
source .env

# On instance, store in ~/.bashrc
echo 'export HF_TOKEN="..."' >> ~/.bashrc
```




### Troubleshooting

# Lambda Labs Troubleshooting Guide

## Instance Launch Issues

### No instances available

**Error**: "No capacity available" or instance type not listed

**Solutions**:
```bash
# Check availability via API
curl -u $LAMBDA_API_KEY: \
  https://cloud.lambdalabs.com/api/v1/instance-types | jq '.data | to_entries[] | select(.value.regions_with_capacity_available | length > 0) | .key'

# Try different regions
# US regions: us-west-1, us-east-1, us-south-1
# International: eu-west-1, asia-northeast-1, etc.

# Try alternative GPU types
# H100 not available? Try A100
# A100 not available? Try A10 or A6000
```

### Instance stuck launching

**Problem**: Instance shows "booting" for over 20 minutes

**Solutions**:
```bash
# Single-GPU: Should be ready in 3-5 minutes
# Multi-GPU (8x): May take 10-15 minutes

# If stuck longer:
# 1. Terminate the instance
# 2. Try a different region
# 3. Try a different instance type
# 4. Contact Lambda support if persistent
```

### API authentication fails

**Error**: `401 Unauthorized` or `403 Forbidden`

**Solutions**:
```bash
# Verify API key format (should start with specific prefix)
echo $LAMBDA_API_KEY

# Test API key
curl -u $LAMBDA_API_KEY: \
  https://cloud.lambdalabs.com/api/v1/instance-types

# Generate new API key from Lambda console if needed
# Settings > API keys > Generate
```

### Quota limits reached

**Error**: "Instance limit reached" or "Quota exceeded"

**Solutions**:
- Check current running instances in console
- Terminate unused instances
- Contact Lambda support to request quota increase
- Use 1-Click Clusters for large-scale needs

## SSH Connection Issues

### Connection refused

**Error**: `ssh: connect to host <IP> port 22: Connection refused`

**Solutions**:
```bash
# Wait for instance to fully initialize
# Single-GPU: 3-5 minutes
# Multi-GPU: 10-15 minutes

# Check instance status in console (should be "active")

# Verify correct IP address
curl -u $LAMBDA_API_KEY: \
  https://cloud.lambdalabs.com/api/v1/instances | jq '.data[].ip'
```

### Permission denied

**Error**: `Permission denied (publickey)`

**Solutions**:
```bash
# Verify SSH key matches
ssh -v -i ~/.ssh/lambda_key ubuntu@<IP>

# Check key permissions
chmod 600 ~/.ssh/lambda_key
chmod 644 ~/.ssh/lambda_key.pub

# Verify key was added to Lambda console before launch
# Keys must be added BEFORE launching instance

# Check authorized_keys on instance (if you have another way in)
cat ~/.ssh/authorized_keys
```

### Host key verification failed

**Error**: `WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!`

**Solutions**:
```bash
# This happens when IP is reused by different instance
# Remove old key
ssh-keygen -R <IP>

# Then connect again
ssh ubuntu@<IP>
```

### Timeout during SSH

**Error**: `ssh: connect to host <IP> port 22: Operation timed out`

**Solutions**:
```bash
# Check if instance is in "active" state

# Verify firewall allows SSH (port 22)
# Lambda console > Firewall

# Check your local network allows outbound SSH

# Try from different network/VPN
```

## GPU Issues

### GPU not detected

**Error**: `nvidia-smi: command not found` or no GPUs shown

**Solutions**:
```bash
# Reboot instance
sudo reboot

# Reinstall NVIDIA drivers (if needed)
wget -nv -O- https://lambdalabs.com/install-lambda-stack.sh | sh -
sudo reboot

# Check driver status
nvidia-smi
lsmod | grep nvidia
```

### CUDA out of memory

**Error**: `torch.cuda.OutOfMemoryError: CUDA out of memory`

**Solutions**:
```python
# Check GPU memory
import torch
print(torch.cuda.get_device_properties(0).total_memory / 1e9, "GB")

# Clear cache
torch.cuda.empty_cache()

# Reduce batch size
batch_size = batch_size // 2

# Enable gradient checkpointing
model.gradient_checkpointing_enable()

# Use mixed precision
from torch.cuda.amp import autocast
with autocast():
    outputs = model(**inputs)

# Use larger GPU instance
# A100-40GB → A100-80GB → H100
```

### CUDA version mismatch

**Error**: `CUDA driver version is insufficient for CUDA runtime version`

**Solutions**:
```bash
# Check versions
nvidia-smi  # Shows driver CUDA version
nvcc --version  # Shows toolkit version

# Lambda Stack should have compatible versions
# If mismatch, reinstall Lambda Stack
wget -nv -O- https://lambdalabs.com/install-lambda-stack.sh | sh -
sudo reboot

# Or install specific PyTorch version
pip install torch==2.1.0+cu121 -f https://download.pytorch.org/whl/torch_stable.html
```

### Multi-GPU not working

**Error**: Only one GPU being used

**Solutions**:
```python
# Check all GPUs visible
import torch
print(f"GPUs available: {torch.cuda.device_count()}")

# Verify CUDA_VISIBLE_DEVICES not set restrictively
import os
print(os.environ.get("CUDA_VISIBLE_DEVICES", "not set"))

# Use DataParallel or DistributedDataParallel
model = torch.nn.DataParallel(model)
# or
model = torch.nn.parallel.DistributedDataParallel(model)
```

## Filesystem Issues

### Filesystem not mounted

**Error**: `/lambda/nfs/<name>` doesn't exist

**Solutions**:
```bash
# Filesystem must be attached at launch time
# Cannot attach to running instance

# Verify filesystem was selected during launch

# Check mount points
df -h | grep lambda

# If missing, terminate and relaunch with filesystem
```

### Slow filesystem performance

**Problem**: Reading/writing to filesystem is slow

**Solutions**:
```bash
# Use local SSD for temporary/intermediate files
# /home/ubuntu has fast NVMe storage

# Copy frequently accessed data to local storage
cp -r /lambda/nfs/storage/dataset /home/ubuntu/dataset

# Use filesystem for checkpoints and final outputs only

# Check network bandwidth
iperf3 -c <filesystem_server>
```

### Data lost after termination

**Problem**: Files disappeared after instance terminated

**Solutions**:
```bash
# Root volume (/home/ubuntu) is EPHEMERAL
# Data there is lost on termination

# ALWAYS use filesystem for persistent data
/lambda/nfs/<filesystem_name>/

# Sync important local files before terminating
rsync -av /home/ubuntu/outputs/ /lambda/nfs/storage/outputs/
```

### Filesystem full

**Error**: `No space left on device`

**Solutions**:
```bash
# Check filesystem usage
df -h /lambda/nfs/storage

# Find large files
du -sh /lambda/nfs/storage/* | sort -h

# Clean up old checkpoints
find /lambda/nfs/storage/checkpoints -mtime +7 -delete

# Increase filesystem size in Lambda console
# (may require support request)
```

## Network Issues

### Port not accessible

**Error**: Cannot connect to service (TensorBoard, Jupyter, etc.)

**Solutions**:
```bash
# Lambda default: Only port 22 is open
# Configure firewall in Lambda console

# Or use SSH tunneling (recommended)
ssh -L 6006:localhost:6006 ubuntu@<IP>
# Access at http://localhost:6006

# For Jupyter
ssh -L 8888:localhost:8888 ubuntu@<IP>
```

### Slow data download

**Problem**: Downloading datasets is slow

**Solutions**:
```bash
# Check available bandwidth
speedtest-cli

# Use multi-threaded download
aria2c -x 16 <URL>

# For HuggingFace models
export HF_HUB_ENABLE_HF_TRANSFER=1
pip install hf_transfer

# For S3, use parallel transfer
aws s3 sync s3://bucket/data /local/data --quiet
```

### Inter-node communication fails

**Error**: Distributed training can't connect between nodes

**Solutions**:
```bash
# Verify nodes in same region (required)

# Check private IPs can communicate
ping <other_node_private_ip>

# Verify NCCL settings
export NCCL_DEBUG=INFO
export NCCL_IB_DISABLE=0  # Enable InfiniBand if available

# Check firewall allows distributed ports
# Need: 29500 (PyTorch), or configured MASTER_PORT
```

## Software Issues

### Package installation fails

**Error**: `pip install` errors

**Solutions**:
```bash
# Use virtual environment (don't modify system Python)
python -m venv ~/myenv
source ~/myenv/bin/activate
pip install <package>

# For CUDA packages, match CUDA version
pip install torch --index-url https://download.pytorch.org/whl/cu121

# Clear pip cache if corrupted
pip cache purge
```

### Python version issues

**Error**: Package requires different Python version

**Solutions**:
```bash
# Install alternate Python (don't replace system Python)
sudo apt install python3.11 python3.11-venv python3.11-dev

# Create venv with specific Python
python3.11 -m venv ~/py311env
source ~/py311env/bin/activate
```

### ImportError or ModuleNotFoundError

**Error**: Module not found despite installation

**Solutions**:
```bash
# Verify correct Python environment
which python
pip list | grep <module>

# Ensure virtual environment is activated
source ~/myenv/bin/activate

# Reinstall in correct environment
pip uninstall <package>
pip install <package>
```

## Training Issues

### Training hangs

**Problem**: Training stops progressing, no output

**Solutions**:
```bash
# Check GPU utilization
watch -n 1 nvidia-smi

# If GPUs at 0%, likely data loading bottleneck
# Increase num_workers in DataLoader

# Check for deadlocks in distributed training
export NCCL_DEBUG=INFO

# Add timeouts
dist.init_process_group(..., timeout=timedelta(minutes=30))
```

### Checkpoint corruption

**Error**: `RuntimeError: storage has wrong size` or similar

**Solutions**:
```python
# Use safe saving pattern
checkpoint_path = "/lambda/nfs/storage/checkpoint.pt"
temp_path = checkpoint_path + ".tmp"

# Save to temp first
torch.save(state_dict, temp_path)
# Then atomic rename
os.rename(temp_path, checkpoint_path)

# For loading corrupted checkpoint
try:
    state = torch.load(checkpoint_path)
except:
    # Fall back to previous checkpoint
    state = torch.load(checkpoint_path + ".backup")
```

### Memory leak

**Problem**: Memory usage grows over time

**Solutions**:
```python
# Clear CUDA cache periodically
torch.cuda.empty_cache()

# Detach tensors when logging
loss_value = loss.detach().cpu().item()

# Don't accumulate gradients unintentionally
optimizer.zero_grad(set_to_none=True)

# Use gradient accumulation properly
if (step + 1) % accumulation_steps == 0:
    optimizer.step()
    optimizer.zero_grad()
```

## Billing Issues

### Unexpected charges

**Problem**: Bill higher than expected

**Solutions**:
```bash
# Check for forgotten running instances
curl -u $LAMBDA_API_KEY: \
  https://cloud.lambdalabs.com/api/v1/instances | jq '.data[].id'

# Terminate all instances
# Lambda console > Instances > Terminate all

# Lambda charges by the minute
# No charge for stopped instances (but no "stop" feature - only terminate)
```

### Instance terminated unexpectedly

**Problem**: Instance disappeared without manual termination

**Possible causes**:
- Payment issue (card declined)
- Account suspension
- Instance health check failure

**Solutions**:
- Check email for Lambda notifications
- Verify payment method in console
- Contact Lambda support
- Always checkpoint to filesystem

## Common Error Messages

| Error | Cause | Solution |
|-------|-------|----------|
| `No capacity available` | Region/GPU sold out | Try different region or GPU type |
| `Permission denied (publickey)` | SSH key mismatch | Re-add key, check permissions |
| `CUDA out of memory` | Model too large | Reduce batch size, use larger GPU |
| `No space left on device` | Disk full | Clean up or use filesystem |
| `Connection refused` | Instance not ready | Wait 3-15 minutes for boot |
| `Module not found` | Wrong Python env | Activate correct virtualenv |

## Getting Help

1. **Documentation**: https://docs.lambda.ai
2. **Support**: https://support.lambdalabs.com
3. **Email**: support@lambdalabs.com
4. **Status**: Check Lambda status page for outages

### Information to Include

When contacting support, include:
- Instance ID
- Region
- Instance type
- Error message (full traceback)
- Steps to reproduce
- Time of occurrence




---

## 🚀 Usage

**Reference this template:** `@skill-lambda-labs-gpu-cloud.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
