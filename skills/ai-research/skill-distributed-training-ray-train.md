---
id: skill-distributed-training-ray-train
type: skill
name: distributed-training-ray-train
description: ''
category: ai-research
complexity: medium
keywords:
- api
- deployment
- github
- kubernetes
- performance
- python
capabilities: []
token_estimate: 1435
has_references: true
reference_count: 1
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,435 -->
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




# distributed-training-ray-train

---
name: ray-train
description: Distributed training orchestration across clusters. Scales PyTorch/TensorFlow/HuggingFace from laptop to 1000s of nodes. Built-in hyperparameter tuning with Ray Tune, fault tolerance, elastic scaling. Use when training massive models across multiple machines or running distributed hyperparameter sweeps.
version: 1.0.0
author: Orchestra Research
license: MIT
tags: [Ray Train, Distributed Training, Orchestration, Ray, Hyperparameter Tuning, Fault Tolerance, Elastic Scaling, Multi-Node, PyTorch, TensorFlow]
dependencies: [ray[train], torch, transformers]
---

# Ray Train - Distributed Training Orchestration

## Quick start

Ray Train scales machine learning training from single GPU to multi-node clusters with minimal code changes.

**Installation**:
```bash
pip install -U "ray[train]"
```

**Basic PyTorch training** (single node):

```python
import ray
from ray import train
from ray.train import ScalingConfig
from ray.train.torch import TorchTrainer
import torch
import torch.nn as nn

# Define training function
def train_func(config):
    # Your normal PyTorch code
    model = nn.Linear(10, 1)
    optimizer = torch.optim.SGD(model.parameters(), lr=0.01)

    # Prepare for distributed (Ray handles device placement)
    model = train.torch.prepare_model(model)

    for epoch in range(10):
        # Your training loop
        output = model(torch.randn(32, 10))
        loss = output.sum()
        loss.backward()
        optimizer.step()
        optimizer.zero_grad()

        # Report metrics (logged automatically)
        train.report({"loss": loss.item(), "epoch": epoch})

# Run distributed training
trainer = TorchTrainer(
    train_func,
    scaling_config=ScalingConfig(
        num_workers=4,  # 4 GPUs/workers
        use_gpu=True
    )
)

result = trainer.fit()
print(f"Final loss: {result.metrics['loss']}")
```

**That's it!** Ray handles:
- Distributed coordination
- GPU allocation
- Fault tolerance
- Checkpointing
- Metric aggregation

## Common workflows

### Workflow 1: Scale existing PyTorch code

**Original single-GPU code**:
```python
model = MyModel().cuda()
optimizer = torch.optim.Adam(model.parameters())

for epoch in range(epochs):
    for batch in dataloader:
        loss = model(batch)
        loss.backward()
        optimizer.step()
```

**Ray Train version** (scales to multi-GPU/multi-node):
```python
from ray.train.torch import TorchTrainer
from ray import train

def train_func(config):
    model = MyModel()
    optimizer = torch.optim.Adam(model.parameters())

    # Prepare for distributed (automatic device placement)
    model = train.torch.prepare_model(model)
    dataloader = train.torch.prepare_data_loader(dataloader)

    for epoch in range(epochs):
        for batch in dataloader:
            loss = model(batch)
            loss.backward()
            optimizer.step()

            # Report metrics
            train.report({"loss": loss.item()})

# Scale to 8 GPUs
trainer = TorchTrainer(
    train_func,
    scaling_config=ScalingConfig(num_workers=8, use_gpu=True)
)
trainer.fit()
```

**Benefits**: Same code runs on 1 GPU or 1000 GPUs

### Workflow 2: HuggingFace Transformers integration

```python
from ray.train.huggingface import TransformersTrainer
from transformers import AutoModelForCausalLM, AutoTokenizer, TrainingArguments

def train_func(config):
    # Load model and tokenizer
    model = AutoModelForCausalLM.from_pretrained("gpt2")
    tokenizer = AutoTokenizer.from_pretrained("gpt2")

    # Training arguments (HuggingFace API)
    training_args = TrainingArguments(
        output_dir="./output",
        num_train_epochs=3,
        per_device_train_batch_size=8,
        learning_rate=2e-5,
    )

    # Ray automatically handles distributed training
    from transformers import Trainer
    trainer = Trainer(
        model=model,
        args=training_args,
        train_dataset=train_dataset,
    )

    trainer.train()

# Scale to multi-node (2 nodes × 8 GPUs = 16 workers)
trainer = TransformersTrainer(
    train_func,
    scaling_config=ScalingConfig(
        num_workers=16,
        use_gpu=True,
        resources_per_worker={"GPU": 1}
    )
)

result = trainer.fit()
```

### Workflow 3: Hyperparameter tuning with Ray Tune

```python
from ray import tune
from ray.train.torch import TorchTrainer
from ray.tune.schedulers import ASHAScheduler

def train_func(config):
    # Use hyperparameters from config
    lr = config["lr"]
    batch_size = config["batch_size"]

    model = MyModel()
    optimizer = torch.optim.Adam(model.parameters(), lr=lr)

    model = train.torch.prepare_model(model)

    for epoch in range(10):
        # Training loop
        loss = train_epoch(model, optimizer, batch_size)
        train.report({"loss": loss, "epoch": epoch})

# Define search space
param_space = {
    "lr": tune.loguniform(1e-5, 1e-2),
    "batch_size": tune.choice([16, 32, 64, 128])
}

# Run 20 trials with early stopping
tuner = tune.Tuner(
    TorchTrainer(
        train_func,
        scaling_config=ScalingConfig(num_workers=4, use_gpu=True)
    ),
    param_space=param_space,
    tune_config=tune.TuneConfig(
        num_samples=20,
        scheduler=ASHAScheduler(metric="loss", mode="min")
    )
)

results = tuner.fit()
best = results.get_best_result(metric="loss", mode="min")
print(f"Best hyperparameters: {best.config}")
```

**Result**: Distributed hyperparameter search across cluster

### Workflow 4: Checkpointing and fault tolerance

```python
from ray import train
from ray.train import Checkpoint

def train_func(config):
    model = MyModel()
    optimizer = torch.optim.Adam(model.parameters())

    # Try to resume from checkpoint
    checkpoint = train.get_checkpoint()
    if checkpoint:
        with checkpoint.as_directory() as checkpoint_dir:
            state = torch.load(f"{checkpoint_dir}/model.pt")
            model.load_state_dict(state["model"])
            optimizer.load_state_dict(state["optimizer"])
            start_epoch = state["epoch"]
    else:
        start_epoch = 0

    model = train.torch.prepare_model(model)

    for epoch in range(start_epoch, 100):
        loss = train_epoch(model, optimizer)

        # Save checkpoint every 10 epochs
        if epoch % 10 == 0:
            checkpoint = Checkpoint.from_directory(
                train.get_context().get_trial_dir()
            )
            torch.save({
                "model": model.state_dict(),
                "optimizer": optimizer.state_dict(),
                "epoch": epoch
            }, checkpoint.path / "model.pt")

            train.report({"loss": loss}, checkpoint=checkpoint)

trainer = TorchTrainer(
    train_func,
    scaling_config=ScalingConfig(num_workers=8, use_gpu=True)
)

# Automatically resumes from checkpoint if training fails
result = trainer.fit()
```

### Workflow 5: Multi-node training

```python
from ray.train import ScalingConfig

# Connect to Ray cluster
ray.init(address="auto")  # Or ray.init("ray://head-node:10001")

# Train across 4 nodes × 8 GPUs = 32 workers
trainer = TorchTrainer(
    train_func,
    scaling_config=ScalingConfig(
        num_workers=32,
        use_gpu=True,
        resources_per_worker={"GPU": 1, "CPU": 4},
        placement_strategy="SPREAD"  # Spread across nodes
    )
)

result = trainer.fit()
```

**Launch Ray cluster**:
```bash
# On head node
ray start --head --port=6379

# On worker nodes
ray start --address=<head-node-ip>:6379
```

## When to use vs alternatives

**Use Ray Train when**:
- Training across multiple machines (multi-node)
- Need hyperparameter tuning at scale
- Want fault tolerance (auto-restart failed workers)
- Elastic scaling (add/remove nodes during training)
- Unified framework (same code for PyTorch/TF/HF)

**Key advantages**:
- **Multi-node orchestration**: Easiest multi-node setup
- **Ray Tune integration**: Best-in-class hyperparameter tuning
- **Fault tolerance**: Automatic recovery from failures
- **Elastic**: Add/remove nodes without restarting
- **Framework agnostic**: PyTorch, TensorFlow, HuggingFace, XGBoost

**Use alternatives instead**:
- **Accelerate**: Single-node multi-GPU, simpler
- **PyTorch Lightning**: High-level abstractions, callbacks
- **DeepSpeed**: Maximum performance, complex setup
- **Raw DDP**: Maximum control, minimal overhead

## Common issues

**Issue: Ray cluster not connecting**

Check ray status:
```bash
ray status

# Should show:
# - Nodes: 4
# - GPUs: 32
# - Workers: Ready
```

If not connected:
```bash
# Restart head node
ray stop
ray start --head --port=6379 --dashboard-host=0.0.0.0

# Restart worker nodes
ray stop
ray start --address=<head-ip>:6379
```

**Issue: Out of memory**

Reduce workers or use gradient accumulation:
```python
scaling_config=ScalingConfig(
    num_workers=4,  # Reduce from 8
    use_gpu=True
)

# In train_func, accumulate gradients
for i, batch in enumerate(dataloader):
    loss = model(batch) / accumulation_steps
    loss.backward()

    if (i + 1) % accumulation_steps == 0:
        optimizer.step()
        optimizer.zero_grad()
```

**Issue: Slow training**

Check if data loading is bottleneck:
```python
import time

def train_func(config):
    for epoch in range(epochs):
        start = time.time()
        for batch in dataloader:
            data_time = time.time() - start
            # Train...
            start = time.time()
            print(f"Data loading: {data_time:.3f}s")
```

If data loading is slow, increase workers:
```python
dataloader = DataLoader(dataset, num_workers=8)
```

## Advanced topics

**Multi-node setup**: See [references/multi-node.md](references/multi-node.md) for Ray cluster deployment on AWS, GCP, Kubernetes, and SLURM.

**Hyperparameter tuning**: See [references/hyperparameter-tuning.md](references/hyperparameter-tuning.md) for Ray Tune integration, search algorithms (Optuna, HyperOpt), and population-based training.

**Custom training loops**: See [references/custom-loops.md](references/custom-loops.md) for advanced Ray Train usage, custom backends, and integration with other frameworks.

## Hardware requirements

- **Single node**: 1+ GPUs (or CPUs)
- **Multi-node**: 2+ machines with network connectivity
- **Cloud**: AWS, GCP, Azure (Ray autoscaling)
- **On-prem**: Kubernetes, SLURM clusters

**Supported accelerators**:
- NVIDIA GPUs (CUDA)
- AMD GPUs (ROCm)
- TPUs (Google Cloud)
- CPUs

## Resources

- Docs: https://docs.ray.io/en/latest/train/train.html
- GitHub: https://github.com/ray-project/ray ⭐ 36,000+
- Version: 2.40.0+
- Examples: https://docs.ray.io/en/latest/train/examples.html
- Slack: https://forms.gle/9TSdDYUgxYs8SA9e8
- Used by: OpenAI, Uber, Spotify, Shopify, Instacart




---


## 📚 Reference Materials


### Multi Node

# Ray Train Multi-Node Setup

## Ray Cluster Architecture

Ray Train runs on a **Ray cluster** with one head node and multiple worker nodes.

**Components**:
- **Head node**: Coordinates workers, runs scheduling
- **Worker nodes**: Execute training tasks
- **Object store**: Shared memory across nodes (using Apache Arrow/Plasma)

## Local Multi-Node Setup

### Manual Cluster Setup

**Head node**:
```bash
# Start Ray head
ray start --head --port=6379 --dashboard-host=0.0.0.0

# Output:
# Started Ray on this node with:
#   - Head node IP: 192.168.1.100
#   - Dashboard: http://192.168.1.100:8265
```

**Worker nodes**:
```bash
# Connect to head node
ray start --address=192.168.1.100:6379

# Output:
# Started Ray on this node.
# Connected to Ray cluster.
```

**Training script**:
```python
import ray
from ray.train.torch import TorchTrainer
from ray.train import ScalingConfig

# Connect to cluster
ray.init(address='auto')  # Auto-detects cluster

# Train across all nodes
trainer = TorchTrainer(
    train_func,
    scaling_config=ScalingConfig(
        num_workers=16,  # Total workers across all nodes
        use_gpu=True,
        placement_strategy="SPREAD"  # Spread across nodes
    )
)

result = trainer.fit()
```

### Check Cluster Status

```bash
# View cluster status
ray status

# Output:
# ======== Cluster Status ========
# Nodes: 4
# Total CPUs: 128
# Total GPUs: 32
# Total memory: 512 GB
```

**Python API**:
```python
import ray

ray.init(address='auto')

# Get cluster resources
print(ray.cluster_resources())
# {'CPU': 128.0, 'GPU': 32.0, 'memory': 549755813888, 'node:192.168.1.100': 1.0, ...}

# Get available resources
print(ray.available_resources())
```

## Cloud Deployments

### AWS EC2 Cluster

**Cluster config** (`cluster.yaml`):
```yaml
cluster_name: ray-train-cluster

max_workers: 3  # 3 worker nodes

provider:
  type: aws
  region: us-west-2
  availability_zone: us-west-2a

auth:
  ssh_user: ubuntu

head_node_type: head_node
available_node_types:
  head_node:
    node_config:
      InstanceType: p3.2xlarge  # V100 GPU
      ImageId: ami-0a2363a9cff180a64  # Deep Learning AMI
    resources: {"CPU": 8, "GPU": 1}
    min_workers: 0
    max_workers: 0

  worker_node:
    node_config:
      InstanceType: p3.8xlarge  # 4× V100
      ImageId: ami-0a2363a9cff180a64
    resources: {"CPU": 32, "GPU": 4}
    min_workers: 3
    max_workers: 3

setup_commands:
  - pip install -U ray[train] torch transformers

head_setup_commands:
  - pip install -U "ray[default]"
```

**Launch cluster**:
```bash
# Start cluster
ray up cluster.yaml

# SSH to head node
ray attach cluster.yaml

# Run training
python train.py

# Teardown
ray down cluster.yaml
```

**Auto-submit job**:
```bash
# Submit job from local machine
ray job submit \
  --address http://<head-node-ip>:8265 \
  --working-dir . \
  -- python train.py
```

### GCP Cluster

**Cluster config** (`gcp-cluster.yaml`):
```yaml
cluster_name: ray-train-gcp

provider:
  type: gcp
  region: us-central1
  availability_zone: us-central1-a
  project_id: my-project-id

auth:
  ssh_user: ubuntu

head_node_type: head_node
available_node_types:
  head_node:
    node_config:
      machineType: n1-standard-8
      disks:
        - boot: true
          autoDelete: true
          type: PERSISTENT
          initializeParams:
            diskSizeGb: 50
            sourceImage: projects/deeplearning-platform-release/global/images/family/pytorch-latest-gpu
      guestAccelerators:
        - acceleratorType: nvidia-tesla-v100
          acceleratorCount: 1
    resources: {"CPU": 8, "GPU": 1}

  worker_node:
    node_config:
      machineType: n1-highmem-16
      disks:
        - boot: true
          autoDelete: true
          type: PERSISTENT
          initializeParams:
            diskSizeGb: 100
            sourceImage: projects/deeplearning-platform-release/global/images/family/pytorch-latest-gpu
      guestAccelerators:
        - acceleratorType: nvidia-tesla-v100
          acceleratorCount: 4
    resources: {"CPU": 16, "GPU": 4}
    min_workers: 2
    max_workers: 10

setup_commands:
  - pip install -U ray[train] torch transformers
```

**Launch**:
```bash
ray up gcp-cluster.yaml --yes
```

### Azure Cluster

**Cluster config** (`azure-cluster.yaml`):
```yaml
cluster_name: ray-train-azure

provider:
  type: azure
  location: eastus
  resource_group: ray-cluster-rg
  subscription_id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

auth:
  ssh_user: ubuntu
  ssh_private_key: ~/.ssh/id_rsa

head_node_type: head_node
available_node_types:
  head_node:
    node_config:
      azure_arm_parameters:
        vmSize: Standard_NC6  # K80 GPU
        imagePublisher: microsoft-dsvm
        imageOffer: ubuntu-1804
        imageSku: 1804-gen2
        imageVersion: latest
    resources: {"CPU": 6, "GPU": 1}

  worker_node:
    node_config:
      azure_arm_parameters:
        vmSize: Standard_NC24  # 4× K80
        imagePublisher: microsoft-dsvm
        imageOffer: ubuntu-1804
        imageSku: 1804-gen2
        imageVersion: latest
    resources: {"CPU": 24, "GPU": 4}
    min_workers: 2
    max_workers: 10
```

## Kubernetes Deployment

### KubeRay Operator

**Install KubeRay**:
```bash
# Add Helm repo
helm repo add kuberay https://ray-project.github.io/kuberay-helm/

# Install operator
helm install kuberay-operator kuberay/kuberay-operator --version 0.6.0
```

**RayCluster manifest** (`ray-cluster.yaml`):
```yaml
apiVersion: ray.io/v1alpha1
kind: RayCluster
metadata:
  name: ray-train-cluster
spec:
  rayVersion: '2.40.0'
  headGroupSpec:
    rayStartParams:
      dashboard-host: '0.0.0.0'
    template:
      spec:
        containers:
        - name: ray-head
          image: rayproject/ray:2.40.0-py310-gpu
          resources:
            limits:
              cpu: "8"
              memory: "32Gi"
              nvidia.com/gpu: "1"
            requests:
              cpu: "8"
              memory: "32Gi"
              nvidia.com/gpu: "1"
          ports:
          - containerPort: 6379
            name: gcs-server
          - containerPort: 8265
            name: dashboard
          - containerPort: 10001
            name: client

  workerGroupSpecs:
  - replicas: 4
    minReplicas: 2
    maxReplicas: 10
    groupName: gpu-workers
    rayStartParams: {}
    template:
      spec:
        containers:
        - name: ray-worker
          image: rayproject/ray:2.40.0-py310-gpu
          resources:
            limits:
              cpu: "16"
              memory: "64Gi"
              nvidia.com/gpu: "4"
            requests:
              cpu: "16"
              memory: "64Gi"
              nvidia.com/gpu: "4"
```

**Deploy**:
```bash
kubectl apply -f ray-cluster.yaml

# Check status
kubectl get rayclusters

# Access dashboard
kubectl port-forward service/ray-train-cluster-head-svc 8265:8265
# Open http://localhost:8265
```

**Submit training job**:
```bash
# Port-forward Ray client port
kubectl port-forward service/ray-train-cluster-head-svc 10001:10001

# Submit from local machine
RAY_ADDRESS="ray://localhost:10001" python train.py
```

## SLURM Integration

### SLURM Job Script

**Launch Ray cluster** (`ray_cluster.sh`):
```bash
#!/bin/bash
#SBATCH --job-name=ray-train
#SBATCH --nodes=4
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=32
#SBATCH --gres=gpu:8
#SBATCH --time=24:00:00
#SBATCH --output=ray_train_%j.out

# Load modules
module load cuda/11.8
module load python/3.10

# Activate environment
source ~/venv/bin/activate

# Get head node
head_node=$(hostname)
head_node_ip=$(hostname -I | awk '{print $1}')

# Start Ray head on first node
if [ "$SLURM_NODEID" -eq 0 ]; then
    echo "Starting Ray head node at $head_node_ip"
    ray start --head --node-ip-address=$head_node_ip \
      --port=6379 \
      --dashboard-host=0.0.0.0 \
      --num-cpus=$SLURM_CPUS_PER_TASK \
      --num-gpus=$SLURM_GPUS_ON_NODE \
      --block &
    sleep 10
fi

# Start Ray workers on other nodes
if [ "$SLURM_NODEID" -ne 0 ]; then
    echo "Starting Ray worker node"
    ray start --address=$head_node_ip:6379 \
      --num-cpus=$SLURM_CPUS_PER_TASK \
      --num-gpus=$SLURM_GPUS_ON_NODE \
      --block &
fi

sleep 5

# Run training on head node only
if [ "$SLURM_NODEID" -eq 0 ]; then
    echo "Running training..."
    python train.py --address=$head_node_ip:6379
fi

# Wait for all processes
wait
```

**Submit job**:
```bash
sbatch ray_cluster.sh
```

**Training script** (`train.py`):
```python
import argparse
import ray
from ray.train.torch import TorchTrainer
from ray.train import ScalingConfig

def main(args):
    # Connect to Ray cluster
    ray.init(address=args.address)

    # Train across all SLURM nodes
    trainer = TorchTrainer(
        train_func,
        scaling_config=ScalingConfig(
            num_workers=32,  # 4 nodes × 8 GPUs
            use_gpu=True,
            placement_strategy="SPREAD"
        )
    )

    result = trainer.fit()
    print(f"Training complete: {result.metrics}")

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('--address', required=True)
    args = parser.parse_args()
    main(args)
```

## Autoscaling

### Enable Autoscaling

**Cluster config with autoscaling**:
```yaml
cluster_name: ray-autoscale

max_workers: 10  # Maximum worker nodes

idle_timeout_minutes: 5  # Shutdown idle workers after 5 min

provider:
  type: aws
  region: us-west-2

available_node_types:
  worker_node:
    min_workers: 2  # Always keep 2 workers
    max_workers: 10  # Scale up to 10
    resources: {"CPU": 32, "GPU": 4}
    node_config:
      InstanceType: p3.8xlarge
```

**Training with autoscaling**:
```python
from ray.train.torch import TorchTrainer
from ray.train import ScalingConfig, RunConfig

# Request resources, Ray autoscaler adds nodes as needed
trainer = TorchTrainer(
    train_func,
    scaling_config=ScalingConfig(
        num_workers=40,  # Ray will autoscale to 10 nodes (40 GPUs)
        use_gpu=True,
        trainer_resources={"CPU": 0}  # Trainer doesn't need resources
    ),
    run_config=RunConfig(
        name="autoscale-training",
        storage_path="s3://my-bucket/ray-results"
    )
)

result = trainer.fit()
```

## Network Configuration

### Firewall Rules

**Required ports**:
- **6379**: Ray GCS (Global Control Store)
- **8265**: Ray Dashboard
- **10001**: Ray Client
- **8000-9000**: Worker communication (configurable)

**AWS Security Group**:
```bash
# Allow Ray ports within cluster
aws ec2 authorize-security-group-ingress \
  --group-id sg-xxxxx \
  --source-group sg-xxxxx \
  --protocol tcp \
  --port 6379

aws ec2 authorize-security-group-ingress \
  --group-id sg-xxxxx \
  --source-group sg-xxxxx \
  --protocol tcp \
  --port 8000-9000
```

### High-Performance Networking

**Enable InfiniBand/RDMA** (on-prem):
```bash
# Set Ray to use specific network interface
export RAY_BACKEND_LOG_LEVEL=debug
export NCCL_SOCKET_IFNAME=ib0  # InfiniBand interface
export NCCL_IB_DISABLE=0       # Enable InfiniBand

ray start --head --node-ip-address=$(ip addr show ib0 | grep 'inet ' | awk '{print $2}' | cut -d/ -f1)
```

**AWS Enhanced Networking**:
```yaml
# Use ENA (Elastic Network Adapter)
worker_node:
  node_config:
    InstanceType: p3dn.24xlarge  # 100 Gbps networking
    EbsOptimized: true
    NetworkInterfaces:
      - DeviceIndex: 0
        DeleteOnTermination: true
        InterfaceType: ena  # Enhanced networking
```

## Monitoring and Debugging

### Ray Dashboard

**Access dashboard**:
```bash
# Local: http://localhost:8265
# Remote: http://<head-node-ip>:8265

# SSH tunnel for secure access
ssh -L 8265:localhost:8265 user@<head-node-ip>
```

**Dashboard features**:
- Cluster utilization (CPU, GPU, memory)
- Running tasks and actors
- Object store usage
- Logs and errors

### Cluster Logs

**View logs**:
```bash
# Head node logs
tail -f /tmp/ray/session_latest/logs/monitor.log

# Worker node logs
tail -f /tmp/ray/session_latest/logs/raylet.log

# All logs
ray logs
```

**Python logging**:
```python
import logging

logger = logging.getLogger("ray")
logger.setLevel(logging.DEBUG)

# In training function
def train_func(config):
    logger.info(f"Worker {ray.get_runtime_context().get_worker_id()} starting")
    # Training...
```

## Best Practices

### 1. Placement Strategies

```python
# PACK: Pack workers on fewer nodes (better for communication)
ScalingConfig(num_workers=16, placement_strategy="PACK")

# SPREAD: Spread across nodes (better for fault tolerance)
ScalingConfig(num_workers=16, placement_strategy="SPREAD")

# STRICT_SPREAD: Exactly one worker per node
ScalingConfig(num_workers=4, placement_strategy="STRICT_SPREAD")
```

### 2. Resource Allocation

```python
# Reserve resources per worker
ScalingConfig(
    num_workers=8,
    use_gpu=True,
    resources_per_worker={"CPU": 8, "GPU": 1},  # Explicit allocation
    trainer_resources={"CPU": 2}  # Reserve for trainer
)
```

### 3. Fault Tolerance

```python
from ray.train import RunConfig, FailureConfig

trainer = TorchTrainer(
    train_func,
    run_config=RunConfig(
        failure_config=FailureConfig(
            max_failures=3  # Retry up to 3 times on worker failure
        )
    )
)
```

## Resources

- Ray Cluster Launcher: https://docs.ray.io/en/latest/cluster/getting-started.html
- KubeRay: https://docs.ray.io/en/latest/cluster/kubernetes/index.html
- SLURM: https://docs.ray.io/en/latest/cluster/vms/user-guides/launching-clusters/slurm.html
- Autoscaling: https://docs.ray.io/en/latest/cluster/vms/user-guides/configuring-autoscaling.html




---

## 🚀 Usage

**Reference this template:** `@skill-distributed-training-ray-train.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
