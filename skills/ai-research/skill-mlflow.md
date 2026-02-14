---
id: skill-mlflow
type: skill
name: mlflow
description: Track ML experiments, manage model registry with versioning, deploy models
  to production, and reproduce experiments with MLflow - framework-agnostic ML lifecycle
  platform
category: ai-research
complexity: medium
keywords:
- deploy
- deployment
- github
- performance
- python
- test
capabilities: []
token_estimate: 1855
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,855 -->
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




# mlflow

> Track ML experiments, manage model registry with versioning, deploy models to production, and reproduce experiments with MLflow - framework-agnostic ML lifecycle platform

# MLflow: ML Lifecycle Management Platform

## When to Use This Skill

Use MLflow when you need to:
- **Track ML experiments** with parameters, metrics, and artifacts
- **Manage model registry** with versioning and stage transitions
- **Deploy models** to various platforms (local, cloud, serving)
- **Reproduce experiments** with project configurations
- **Compare model versions** and performance metrics
- **Collaborate** on ML projects with team workflows
- **Integrate** with any ML framework (framework-agnostic)

**Users**: 20,000+ organizations | **GitHub Stars**: 23k+ | **License**: Apache 2.0

## Installation

```bash
# Install MLflow
pip install mlflow

# Install with extras
pip install mlflow[extras]  # Includes SQLAlchemy, boto3, etc.

# Start MLflow UI
mlflow ui

# Access at http://localhost:5000
```

## Quick Start

### Basic Tracking

```python
import mlflow

# Start a run
with mlflow.start_run():
    # Log parameters
    mlflow.log_param("learning_rate", 0.001)
    mlflow.log_param("batch_size", 32)

    # Your training code
    model = train_model()

    # Log metrics
    mlflow.log_metric("train_loss", 0.15)
    mlflow.log_metric("val_accuracy", 0.92)

    # Log model
    mlflow.sklearn.log_model(model, "model")
```

### Autologging (Automatic Tracking)

```python
import mlflow
from sklearn.ensemble import RandomForestClassifier

# Enable autologging
mlflow.autolog()

# Train (automatically logged)
model = RandomForestClassifier(n_estimators=100, max_depth=5)
model.fit(X_train, y_train)

# Metrics, parameters, and model logged automatically!
```

## Core Concepts

### 1. Experiments and Runs

**Experiment**: Logical container for related runs
**Run**: Single execution of ML code (parameters, metrics, artifacts)

```python
import mlflow

# Create/set experiment
mlflow.set_experiment("my-experiment")

# Start a run
with mlflow.start_run(run_name="baseline-model"):
    # Log params
    mlflow.log_param("model", "ResNet50")
    mlflow.log_param("epochs", 10)

    # Train
    model = train()

    # Log metrics
    mlflow.log_metric("accuracy", 0.95)

    # Log model
    mlflow.pytorch.log_model(model, "model")

# Run ID is automatically generated
print(f"Run ID: {mlflow.active_run().info.run_id}")
```

### 2. Logging Parameters

```python
with mlflow.start_run():
    # Single parameter
    mlflow.log_param("learning_rate", 0.001)

    # Multiple parameters
    mlflow.log_params({
        "batch_size": 32,
        "epochs": 50,
        "optimizer": "Adam",
        "dropout": 0.2
    })

    # Nested parameters (as dict)
    config = {
        "model": {
            "architecture": "ResNet50",
            "pretrained": True
        },
        "training": {
            "lr": 0.001,
            "weight_decay": 1e-4
        }
    }

    # Log as JSON string or individual params
    for key, value in config.items():
        mlflow.log_param(key, str(value))
```

### 3. Logging Metrics

```python
with mlflow.start_run():
    # Training loop
    for epoch in range(NUM_EPOCHS):
        train_loss = train_epoch()
        val_loss = validate()

        # Log metrics at each step
        mlflow.log_metric("train_loss", train_loss, step=epoch)
        mlflow.log_metric("val_loss", val_loss, step=epoch)

        # Log multiple metrics
        mlflow.log_metrics({
            "train_accuracy": train_acc,
            "val_accuracy": val_acc
        }, step=epoch)

    # Log final metrics (no step)
    mlflow.log_metric("final_accuracy", final_acc)
```

### 4. Logging Artifacts

```python
with mlflow.start_run():
    # Log file
    model.save('model.pkl')
    mlflow.log_artifact('model.pkl')

    # Log directory
    os.makedirs('plots', exist_ok=True)
    plt.savefig('plots/loss_curve.png')
    mlflow.log_artifacts('plots')

    # Log text
    with open('config.txt', 'w') as f:
        f.write(str(config))
    mlflow.log_artifact('config.txt')

    # Log dict as JSON
    mlflow.log_dict({'config': config}, 'config.json')
```

### 5. Logging Models

```python
# PyTorch
import mlflow.pytorch

with mlflow.start_run():
    model = train_pytorch_model()
    mlflow.pytorch.log_model(model, "model")

# Scikit-learn
import mlflow.sklearn

with mlflow.start_run():
    model = train_sklearn_model()
    mlflow.sklearn.log_model(model, "model")

# Keras/TensorFlow
import mlflow.keras

with mlflow.start_run():
    model = train_keras_model()
    mlflow.keras.log_model(model, "model")

# HuggingFace Transformers
import mlflow.transformers

with mlflow.start_run():
    mlflow.transformers.log_model(
        transformers_model={
            "model": model,
            "tokenizer": tokenizer
        },
        artifact_path="model"
    )
```

## Autologging

Automatically log metrics, parameters, and models for popular frameworks.

### Enable Autologging

```python
import mlflow

# Enable for all supported frameworks
mlflow.autolog()

# Or enable for specific framework
mlflow.sklearn.autolog()
mlflow.pytorch.autolog()
mlflow.keras.autolog()
mlflow.xgboost.autolog()
```

### Autologging with Scikit-learn

```python
import mlflow
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split

# Enable autologging
mlflow.sklearn.autolog()

# Split data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# Train (automatically logs params, metrics, model)
with mlflow.start_run():
    model = RandomForestClassifier(n_estimators=100, max_depth=5, random_state=42)
    model.fit(X_train, y_train)

    # Metrics like accuracy, f1_score logged automatically
    # Model logged automatically
    # Training duration logged
```

### Autologging with PyTorch Lightning

```python
import mlflow
import pytorch_lightning as pl

# Enable autologging
mlflow.pytorch.autolog()

# Train
with mlflow.start_run():
    trainer = pl.Trainer(max_epochs=10)
    trainer.fit(model, datamodule=dm)

    # Hyperparameters logged
    # Training metrics logged
    # Best model checkpoint logged
```

## Model Registry

Manage model lifecycle with versioning and stage transitions.

### Register Model

```python
import mlflow

# Log and register model
with mlflow.start_run():
    model = train_model()

    # Log model
    mlflow.sklearn.log_model(
        model,
        "model",
        registered_model_name="my-classifier"  # Register immediately
    )

# Or register later
run_id = "abc123"
model_uri = f"runs:/{run_id}/model"
mlflow.register_model(model_uri, "my-classifier")
```

### Model Stages

Transition models between stages: **None** → **Staging** → **Production** → **Archived**

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()

# Promote to staging
client.transition_model_version_stage(
    name="my-classifier",
    version=3,
    stage="Staging"
)

# Promote to production
client.transition_model_version_stage(
    name="my-classifier",
    version=3,
    stage="Production",
    archive_existing_versions=True  # Archive old production versions
)

# Archive model
client.transition_model_version_stage(
    name="my-classifier",
    version=2,
    stage="Archived"
)
```

### Load Model from Registry

```python
import mlflow.pyfunc

# Load latest production model
model = mlflow.pyfunc.load_model("models:/my-classifier/Production")

# Load specific version
model = mlflow.pyfunc.load_model("models:/my-classifier/3")

# Load from staging
model = mlflow.pyfunc.load_model("models:/my-classifier/Staging")

# Use model
predictions = model.predict(X_test)
```

### Model Versioning

```python
client = MlflowClient()

# List all versions
versions = client.search_model_versions("name='my-classifier'")

for v in versions:
    print(f"Version {v.version}: {v.current_stage}")

# Get latest version by stage
latest_prod = client.get_latest_versions("my-classifier", stages=["Production"])
latest_staging = client.get_latest_versions("my-classifier", stages=["Staging"])

# Get model version details
version_info = client.get_model_version(name="my-classifier", version="3")
print(f"Run ID: {version_info.run_id}")
print(f"Stage: {version_info.current_stage}")
print(f"Tags: {version_info.tags}")
```

### Model Annotations

```python
client = MlflowClient()

# Add description
client.update_model_version(
    name="my-classifier",
    version="3",
    description="ResNet50 classifier trained on 1M images with 95% accuracy"
)

# Add tags
client.set_model_version_tag(
    name="my-classifier",
    version="3",
    key="validation_status",
    value="approved"
)

client.set_model_version_tag(
    name="my-classifier",
    version="3",
    key="deployed_date",
    value="2025-01-15"
)
```

## Searching Runs

Find runs programmatically.

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()

# Search all runs in experiment
experiment_id = client.get_experiment_by_name("my-experiment").experiment_id
runs = client.search_runs(
    experiment_ids=[experiment_id],
    filter_string="metrics.accuracy > 0.9",
    order_by=["metrics.accuracy DESC"],
    max_results=10
)

for run in runs:
    print(f"Run ID: {run.info.run_id}")
    print(f"Accuracy: {run.data.metrics['accuracy']}")
    print(f"Params: {run.data.params}")

# Search with complex filters
runs = client.search_runs(
    experiment_ids=[experiment_id],
    filter_string="""
        metrics.accuracy > 0.9 AND
        params.model = 'ResNet50' AND
        tags.dataset = 'ImageNet'
    """,
    order_by=["metrics.f1_score DESC"]
)
```

## Integration Examples

### PyTorch

```python
import mlflow
import torch
import torch.nn as nn

# Enable autologging
mlflow.pytorch.autolog()

with mlflow.start_run():
    # Log config
    config = {
        "lr": 0.001,
        "epochs": 10,
        "batch_size": 32
    }
    mlflow.log_params(config)

    # Train
    model = create_model()
    optimizer = torch.optim.Adam(model.parameters(), lr=config["lr"])

    for epoch in range(config["epochs"]):
        train_loss = train_epoch(model, optimizer, train_loader)
        val_loss, val_acc = validate(model, val_loader)

        # Log metrics
        mlflow.log_metrics({
            "train_loss": train_loss,
            "val_loss": val_loss,
            "val_accuracy": val_acc
        }, step=epoch)

    # Log model
    mlflow.pytorch.log_model(model, "model")
```

### HuggingFace Transformers

```python
import mlflow
from transformers import Trainer, TrainingArguments

# Enable autologging
mlflow.transformers.autolog()

training_args = TrainingArguments(
    output_dir="./results",
    num_train_epochs=3,
    per_device_train_batch_size=16,
    evaluation_strategy="epoch",
    save_strategy="epoch",
    load_best_model_at_end=True
)

# Start MLflow run
with mlflow.start_run():
    trainer = Trainer(
        model=model,
        args=training_args,
        train_dataset=train_dataset,
        eval_dataset=eval_dataset
    )

    # Train (automatically logged)
    trainer.train()

    # Log final model to registry
    mlflow.transformers.log_model(
        transformers_model={
            "model": trainer.model,
            "tokenizer": tokenizer
        },
        artifact_path="model",
        registered_model_name="hf-classifier"
    )
```

### XGBoost

```python
import mlflow
import xgboost as xgb

# Enable autologging
mlflow.xgboost.autolog()

with mlflow.start_run():
    dtrain = xgb.DMatrix(X_train, label=y_train)
    dval = xgb.DMatrix(X_val, label=y_val)

    params = {
        'max_depth': 6,
        'learning_rate': 0.1,
        'objective': 'binary:logistic',
        'eval_metric': ['logloss', 'auc']
    }

    # Train (automatically logged)
    model = xgb.train(
        params,
        dtrain,
        num_boost_round=100,
        evals=[(dtrain, 'train'), (dval, 'val')],
        early_stopping_rounds=10
    )

    # Model and metrics logged automatically
```

## Best Practices

### 1. Organize with Experiments

```python
# ✅ Good: Separate experiments for different tasks
mlflow.set_experiment("sentiment-analysis")
mlflow.set_experiment("image-classification")
mlflow.set_experiment("recommendation-system")

# ❌ Bad: Everything in one experiment
mlflow.set_experiment("all-models")
```

### 2. Use Descriptive Run Names

```python
# ✅ Good: Descriptive names
with mlflow.start_run(run_name="resnet50-imagenet-lr0.001-bs32"):
    train()

# ❌ Bad: No name (auto-generated UUID)
with mlflow.start_run():
    train()
```

### 3. Log Comprehensive Metadata

```python
with mlflow.start_run():
    # Log hyperparameters
    mlflow.log_params({
        "learning_rate": 0.001,
        "batch_size": 32,
        "epochs": 50
    })

    # Log system info
    mlflow.set_tags({
        "dataset": "ImageNet",
        "framework": "PyTorch 2.0",
        "gpu": "A100",
        "git_commit": get_git_commit()
    })

    # Log data info
    mlflow.log_param("train_samples", len(train_dataset))
    mlflow.log_param("val_samples", len(val_dataset))
```

### 4. Track Model Lineage

```python
# Link runs to understand lineage
with mlflow.start_run(run_name="preprocessing"):
    data = preprocess()
    mlflow.log_artifact("data.csv")
    preprocessing_run_id = mlflow.active_run().info.run_id

with mlflow.start_run(run_name="training"):
    # Reference parent run
    mlflow.set_tag("preprocessing_run_id", preprocessing_run_id)
    model = train(data)
```

### 5. Use Model Registry for Deployment

```python
# ✅ Good: Use registry for production
model_uri = "models:/my-classifier/Production"
model = mlflow.pyfunc.load_model(model_uri)

# ❌ Bad: Hard-code run IDs
model_uri = "runs:/abc123/model"
model = mlflow.pyfunc.load_model(model_uri)
```

## Deployment

### Serve Model Locally

```bash
# Serve registered model
mlflow models serve -m "models:/my-classifier/Production" -p 5001

# Serve from run
mlflow models serve -m "runs:/<RUN_ID>/model" -p 5001

# Test endpoint
curl http://127.0.0.1:5001/invocations -H 'Content-Type: application/json' -d '{
  "inputs": [[1.0, 2.0, 3.0, 4.0]]
}'
```

### Deploy to Cloud

```bash
# Deploy to AWS SageMaker
mlflow sagemaker deploy -m "models:/my-classifier/Production" --region-name us-west-2

# Deploy to Azure ML
mlflow azureml deploy -m "models:/my-classifier/Production"
```

## Configuration

### Tracking Server

```bash
# Start tracking server with backend store
mlflow server \
  --backend-store-uri postgresql://user:password@localhost/mlflow \
  --default-artifact-root s3://my-bucket/mlflow \
  --host 0.0.0.0 \
  --port 5000
```

### Client Configuration

```python
import mlflow

# Set tracking URI
mlflow.set_tracking_uri("http://localhost:5000")

# Or use environment variable
# export MLFLOW_TRACKING_URI=http://localhost:5000
```

## Resources

- **Documentation**: https://mlflow.org/docs/latest
- **GitHub**: https://github.com/mlflow/mlflow (23k+ stars)
- **Examples**: https://github.com/mlflow/mlflow/tree/master/examples
- **Community**: https://mlflow.org/community

## See Also

- `references/tracking.md` - Comprehensive tracking guide
- `references/model-registry.md` - Model lifecycle management
- `references/deployment.md` - Production deployment patterns




---


## 📚 Reference Materials


### Deployment

# Deployment Guide

Complete guide to deploying MLflow models to production environments.

## Table of Contents
- Deployment Options
- Local Serving
- REST API Serving
- Docker Deployment
- Cloud Deployment
- Batch Inference
- Production Patterns
- Monitoring

## Deployment Options

MLflow supports multiple deployment targets:

| Target | Use Case | Complexity |
|--------|----------|------------|
| **Local Server** | Development, testing | Low |
| **REST API** | Production serving | Medium |
| **Docker** | Containerized deployment | Medium |
| **AWS SageMaker** | Managed AWS deployment | High |
| **Azure ML** | Managed Azure deployment | High |
| **Kubernetes** | Scalable orchestration | High |
| **Batch** | Offline predictions | Low |

## Local Serving

### Serve Model Locally

```bash
# Serve registered model
mlflow models serve -m "models:/product-classifier/Production" -p 5001

# Serve from run
mlflow models serve -m "runs:/abc123/model" -p 5001

# Serve with custom host
mlflow models serve -m "models:/my-model/Production" -h 0.0.0.0 -p 8080

# Serve with workers (for scalability)
mlflow models serve -m "models:/my-model/Production" -p 5001 --workers 4
```

**Output:**
```
Serving model on http://127.0.0.1:5001
```

### Test Local Server

```bash
# Single prediction
curl http://127.0.0.1:5001/invocations \
  -H 'Content-Type: application/json' \
  -d '{
    "inputs": [[1.0, 2.0, 3.0, 4.0]]
  }'

# Batch predictions
curl http://127.0.0.1:5001/invocations \
  -H 'Content-Type: application/json' \
  -d '{
    "inputs": [
      [1.0, 2.0, 3.0, 4.0],
      [5.0, 6.0, 7.0, 8.0]
    ]
  }'

# CSV input
curl http://127.0.0.1:5001/invocations \
  -H 'Content-Type: text/csv' \
  --data-binary @data.csv
```

### Python Client

```python
import requests
import json

url = "http://127.0.0.1:5001/invocations"

data = {
    "inputs": [[1.0, 2.0, 3.0, 4.0]]
}

headers = {"Content-Type": "application/json"}

response = requests.post(url, json=data, headers=headers)
predictions = response.json()

print(predictions)
```

## REST API Serving

### Build Custom Serving API

```python
from flask import Flask, request, jsonify
import mlflow.pyfunc

app = Flask(__name__)

# Load model on startup
model = mlflow.pyfunc.load_model("models:/product-classifier/Production")

@app.route('/predict', methods=['POST'])
def predict():
    """Prediction endpoint."""
    data = request.get_json()
    inputs = data.get('inputs')

    # Make predictions
    predictions = model.predict(inputs)

    return jsonify({
        'predictions': predictions.tolist()
    })

@app.route('/health', methods=['GET'])
def health():
    """Health check endpoint."""
    return jsonify({'status': 'healthy'})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5001)
```

### FastAPI Serving

```python
from fastapi import FastAPI
from pydantic import BaseModel
import mlflow.pyfunc
import numpy as np

app = FastAPI()

# Load model
model = mlflow.pyfunc.load_model("models:/product-classifier/Production")

class PredictionRequest(BaseModel):
    inputs: list

class PredictionResponse(BaseModel):
    predictions: list

@app.post("/predict", response_model=PredictionResponse)
async def predict(request: PredictionRequest):
    """Make predictions."""
    inputs = np.array(request.inputs)
    predictions = model.predict(inputs)

    return PredictionResponse(predictions=predictions.tolist())

@app.get("/health")
async def health():
    """Health check."""
    return {"status": "healthy"}

# Run with: uvicorn main:app --host 0.0.0.0 --port 5001
```

## Docker Deployment

### Build Docker Image

```bash
# Build Docker image with MLflow
mlflow models build-docker \
  -m "models:/product-classifier/Production" \
  -n product-classifier:v1

# Build with custom image name
mlflow models build-docker \
  -m "runs:/abc123/model" \
  -n my-registry/my-model:latest

# Build and enable MLServer (for KServe/Seldon)
mlflow models build-docker \
  -m "models:/my-model/Production" \
  -n my-model:v1 \
  --enable-mlserver
```

### Run Docker Container

```bash
# Run container
docker run -p 5001:8080 product-classifier:v1

# Run with environment variables
docker run \
  -p 5001:8080 \
  -e MLFLOW_TRACKING_URI=http://mlflow-server:5000 \
  product-classifier:v1

# Run with GPU support
docker run --gpus all -p 5001:8080 product-classifier:v1
```

### Test Docker Container

```bash
# Test endpoint
curl http://localhost:5001/invocations \
  -H 'Content-Type: application/json' \
  -d '{"inputs": [[1.0, 2.0, 3.0, 4.0]]}'
```

### Custom Dockerfile

```dockerfile
FROM python:3.9-slim

# Install MLflow
RUN pip install mlflow boto3

# Set working directory
WORKDIR /app

# Copy model (alternative to downloading from tracking server)
COPY model/ /app/model/

# Expose port
EXPOSE 8080

# Set environment variables
ENV MLFLOW_TRACKING_URI=http://mlflow-server:5000

# Serve model
CMD ["mlflow", "models", "serve", "-m", "/app/model", "-h", "0.0.0.0", "-p", "8080"]
```

## Cloud Deployment

### AWS SageMaker

#### Deploy to SageMaker

```bash
# Build and push Docker image to ECR
mlflow sagemaker build-and-push-container

# Deploy model to SageMaker endpoint
mlflow deployments create \
  -t sagemaker \
  -m "models:/product-classifier/Production" \
  --name product-classifier-endpoint \
  --region-name us-west-2 \
  --config instance_type=ml.m5.xlarge \
  --config instance_count=1
```

#### Python API

```python
import mlflow.sagemaker

# Deploy to SageMaker
mlflow.sagemaker.deploy(
    app_name="product-classifier",
    model_uri="models:/product-classifier/Production",
    region_name="us-west-2",
    mode="create",
    instance_type="ml.m5.xlarge",
    instance_count=1,
    vpc_config={
        "SecurityGroupIds": ["sg-123456"],
        "Subnets": ["subnet-123456", "subnet-789012"]
    }
)
```

#### Invoke SageMaker Endpoint

```python
import boto3
import json

runtime = boto3.client('sagemaker-runtime', region_name='us-west-2')

# Prepare input
data = {
    "inputs": [[1.0, 2.0, 3.0, 4.0]]
}

# Invoke endpoint
response = runtime.invoke_endpoint(
    EndpointName='product-classifier',
    ContentType='application/json',
    Body=json.dumps(data)
)

# Parse response
predictions = json.loads(response['Body'].read())
print(predictions)
```

#### Update SageMaker Endpoint

```bash
# Update endpoint with new model version
mlflow deployments update \
  -t sagemaker \
  -m "models:/product-classifier/Production" \
  --name product-classifier-endpoint
```

#### Delete SageMaker Endpoint

```bash
# Delete endpoint
mlflow deployments delete -t sagemaker --name product-classifier-endpoint
```

### Azure ML

#### Deploy to Azure

```bash
# Deploy to Azure ML
mlflow deployments create \
  -t azureml \
  -m "models:/product-classifier/Production" \
  --name product-classifier-azure \
  --config workspace_name=my-workspace \
  --config resource_group=my-resource-group
```

#### Python API

```python
import mlflow.azureml

# Deploy to Azure ML
mlflow.azureml.deploy(
    model_uri="models:/product-classifier/Production",
    workspace=workspace,
    deployment_config=deployment_config,
    service_name="product-classifier"
)
```

### Kubernetes (KServe)

#### Deploy to Kubernetes

```yaml
# kserve-inference.yaml
apiVersion: serving.kserve.io/v1beta1
kind: InferenceService
metadata:
  name: product-classifier
spec:
  predictor:
    mlflow:
      storageUri: "models:/product-classifier/Production"
      protocolVersion: v2
      runtimeVersion: 1.0.0
```

```bash
# Apply to cluster
kubectl apply -f kserve-inference.yaml

# Check status
kubectl get inferenceservice product-classifier

# Get endpoint URL
kubectl get inferenceservice product-classifier -o jsonpath='{.status.url}'
```

## Batch Inference

### Batch Prediction with Spark

```python
import mlflow.pyfunc
from pyspark.sql import SparkSession

# Load model as Spark UDF
model_uri = "models:/product-classifier/Production"
predict_udf = mlflow.pyfunc.spark_udf(spark, model_uri)

# Load data
df = spark.read.parquet("s3://bucket/data/")

# Apply predictions
predictions_df = df.withColumn(
    "prediction",
    predict_udf(*df.columns)
)

# Save results
predictions_df.write.parquet("s3://bucket/predictions/")
```

### Batch Prediction with Pandas

```python
import mlflow.pyfunc
import pandas as pd

# Load model
model = mlflow.pyfunc.load_model("models:/product-classifier/Production")

# Load data in batches
batch_size = 10000

for chunk in pd.read_csv("large_data.csv", chunksize=batch_size):
    # Make predictions
    predictions = model.predict(chunk)

    # Save results
    chunk['prediction'] = predictions
    chunk.to_csv("predictions.csv", mode='a', header=False, index=False)
```

### Scheduled Batch Job

```python
import mlflow.pyfunc
import pandas as pd
from datetime import datetime

def batch_predict():
    """Daily batch prediction job."""
    # Load model
    model = mlflow.pyfunc.load_model("models:/product-classifier/Production")

    # Load today's data
    today = datetime.now().strftime("%Y-%m-%d")
    df = pd.read_parquet(f"s3://bucket/data/{today}/")

    # Predict
    predictions = model.predict(df)

    # Save results
    df['prediction'] = predictions
    df['prediction_date'] = today
    df.to_parquet(f"s3://bucket/predictions/{today}/")

    print(f"✅ Batch prediction complete for {today}")

# Run with scheduler (e.g., Airflow, cron)
batch_predict()
```

## Production Patterns

### Blue-Green Deployment

```python
import mlflow.pyfunc

# Load both models
blue_model = mlflow.pyfunc.load_model("models:/product-classifier@blue")
green_model = mlflow.pyfunc.load_model("models:/product-classifier@green")

# Switch traffic (controlled by feature flag)
def get_model():
    if feature_flag.is_enabled("use_green_model"):
        return green_model
    else:
        return blue_model

# Serve predictions
def predict(inputs):
    model = get_model()
    return model.predict(inputs)
```

### Canary Deployment

```python
import random
import mlflow.pyfunc

# Load models
stable_model = mlflow.pyfunc.load_model("models:/product-classifier@stable")
canary_model = mlflow.pyfunc.load_model("models:/product-classifier@canary")

def predict_with_canary(inputs, canary_percentage=10):
    """Route traffic: 90% stable, 10% canary."""
    if random.random() * 100 < canary_percentage:
        model = canary_model
        version = "canary"
    else:
        model = stable_model
        version = "stable"

    predictions = model.predict(inputs)

    # Log which version was used
    log_prediction_metrics(version, predictions)

    return predictions
```

### Shadow Deployment

```python
import mlflow.pyfunc
import asyncio

# Load models
production_model = mlflow.pyfunc.load_model("models:/product-classifier@production")
shadow_model = mlflow.pyfunc.load_model("models:/product-classifier@shadow")

async def predict_with_shadow(inputs):
    """Run shadow model in parallel, return production results."""
    # Production prediction (synchronous)
    production_preds = production_model.predict(inputs)

    # Shadow prediction (async, don't block)
    asyncio.create_task(shadow_predict(inputs))

    return production_preds

async def shadow_predict(inputs):
    """Run shadow model and log results."""
    shadow_preds = shadow_model.predict(inputs)

    # Compare predictions
    log_shadow_comparison(shadow_preds)
```

### Model Fallback

```python
import mlflow.pyfunc

class FallbackModel:
    """Model with fallback on error."""

    def __init__(self, primary_uri, fallback_uri):
        self.primary = mlflow.pyfunc.load_model(primary_uri)
        self.fallback = mlflow.pyfunc.load_model(fallback_uri)

    def predict(self, inputs):
        try:
            return self.primary.predict(inputs)
        except Exception as e:
            print(f"Primary model failed: {e}, using fallback")
            return self.fallback.predict(inputs)

# Use it
model = FallbackModel(
    primary_uri="models:/product-classifier@latest",
    fallback_uri="models:/product-classifier@stable"
)

predictions = model.predict(inputs)
```

## Monitoring

### Log Predictions

```python
import mlflow

def predict_and_log(model, inputs):
    """Make predictions and log to MLflow."""
    with mlflow.start_run(run_name="inference"):
        # Predict
        predictions = model.predict(inputs)

        # Log inputs
        mlflow.log_param("num_inputs", len(inputs))

        # Log predictions
        mlflow.log_metric("avg_prediction", predictions.mean())
        mlflow.log_metric("max_prediction", predictions.max())
        mlflow.log_metric("min_prediction", predictions.min())

        # Log timestamp
        import time
        mlflow.log_param("timestamp", time.time())

    return predictions
```

### Model Performance Monitoring

```python
import mlflow
from sklearn.metrics import accuracy_score

def monitor_model_performance(model, X_test, y_test):
    """Monitor production model performance."""
    with mlflow.start_run(run_name="production-monitoring"):
        # Predict
        predictions = model.predict(X_test)

        # Calculate metrics
        accuracy = accuracy_score(y_test, predictions)

        # Log metrics
        mlflow.log_metric("production_accuracy", accuracy)
        mlflow.log_param("test_samples", len(X_test))

        # Alert if performance drops
        if accuracy < 0.85:
            print(f"⚠️  Alert: Production accuracy dropped to {accuracy}")
            # Send alert (e.g., Slack, PagerDuty)

# Run periodically (e.g., daily)
monitor_model_performance(model, X_test, y_test)
```

### Request Logging

```python
from flask import Flask, request, jsonify
import mlflow.pyfunc
import time

app = Flask(__name__)
model = mlflow.pyfunc.load_model("models:/product-classifier/Production")

@app.route('/predict', methods=['POST'])
def predict():
    start_time = time.time()

    data = request.get_json()
    inputs = data.get('inputs')

    # Predict
    predictions = model.predict(inputs)

    # Calculate latency
    latency = (time.time() - start_time) * 1000  # ms

    # Log request
    with mlflow.start_run(run_name="inference"):
        mlflow.log_metric("latency_ms", latency)
        mlflow.log_param("num_inputs", len(inputs))

    return jsonify({
        'predictions': predictions.tolist(),
        'latency_ms': latency
    })
```

## Best Practices

### 1. Use Model Registry URIs

```python
# ✅ Good: Load from registry
model = mlflow.pyfunc.load_model("models:/product-classifier/Production")

# ❌ Bad: Hard-code run IDs
model = mlflow.pyfunc.load_model("runs:/abc123/model")
```

### 2. Implement Health Checks

```python
@app.route('/health', methods=['GET'])
def health():
    """Comprehensive health check."""
    try:
        # Check model loaded
        if model is None:
            return jsonify({'status': 'unhealthy', 'reason': 'model not loaded'}), 503

        # Check model can predict
        test_input = [[1.0, 2.0, 3.0, 4.0]]
        _ = model.predict(test_input)

        return jsonify({'status': 'healthy'}), 200

    except Exception as e:
        return jsonify({'status': 'unhealthy', 'reason': str(e)}), 503
```

### 3. Version Your Deployment

```python
# Tag Docker images with model version
mlflow models build-docker \
  -m "models:/product-classifier/Production" \
  -n product-classifier:v5

# Track deployment version
client.set_model_version_tag(
    name="product-classifier",
    version="5",
    key="deployed_as",
    value="product-classifier:v5"
)
```

### 4. Use Environment Variables

```python
import os
import mlflow.pyfunc

# Configuration via environment
TRACKING_URI = os.getenv("MLFLOW_TRACKING_URI", "http://localhost:5000")
MODEL_NAME = os.getenv("MODEL_NAME", "product-classifier")
MODEL_STAGE = os.getenv("MODEL_STAGE", "Production")

mlflow.set_tracking_uri(TRACKING_URI)

# Load model
model_uri = f"models:/{MODEL_NAME}/{MODEL_STAGE}"
model = mlflow.pyfunc.load_model(model_uri)
```

### 5. Implement Graceful Shutdown

```python
import signal
import sys

def signal_handler(sig, frame):
    """Handle shutdown gracefully."""
    print("Shutting down gracefully...")

    # Close connections
    # Save state
    # Finish pending requests

    sys.exit(0)

signal.signal(signal.SIGINT, signal_handler)
signal.signal(signal.SIGTERM, signal_handler)
```

## Resources

- **MLflow Deployment**: https://mlflow.org/docs/latest/deployment/
- **SageMaker Integration**: https://mlflow.org/docs/latest/python_api/mlflow.sagemaker.html
- **Azure ML Integration**: https://mlflow.org/docs/latest/python_api/mlflow.azureml.html
- **KServe Integration**: https://kserve.github.io/website/latest/modelserving/v1beta1/mlflow/v2/




### Tracking

# Comprehensive Tracking Guide

Complete guide to experiment tracking with MLflow.

## Table of Contents
- Logging Parameters
- Logging Metrics
- Logging Artifacts
- Logging Models
- Autologging
- Runs and Experiments
- Searching and Comparing

## Logging Parameters

### Basic Parameter Logging

```python
import mlflow

with mlflow.start_run():
    # Single parameter
    mlflow.log_param("learning_rate", 0.001)
    mlflow.log_param("batch_size", 32)
    mlflow.log_param("optimizer", "Adam")

    # Multiple parameters at once
    mlflow.log_params({
        "epochs": 50,
        "dropout": 0.2,
        "weight_decay": 1e-4,
        "momentum": 0.9
    })
```

### Structured Parameters

```python
# Nested configuration
config = {
    "model": {
        "architecture": "ResNet50",
        "pretrained": True,
        "num_classes": 10
    },
    "training": {
        "lr": 0.001,
        "batch_size": 32,
        "epochs": 50
    },
    "data": {
        "dataset": "ImageNet",
        "augmentation": True
    }
}

with mlflow.start_run():
    # Log as flattened params
    for section, params in config.items():
        for key, value in params.items():
            mlflow.log_param(f"{section}.{key}", value)

    # Or log entire config as artifact
    mlflow.log_dict(config, "config.json")
```

### Parameter Best Practices

```python
with mlflow.start_run():
    # ✅ Good: Log all hyperparameters
    mlflow.log_params({
        "learning_rate": 0.001,
        "batch_size": 32,
        "optimizer": "Adam",
        "scheduler": "CosineAnnealing",
        "weight_decay": 1e-4
    })

    # ✅ Good: Log data info
    mlflow.log_params({
        "dataset": "ImageNet",
        "train_samples": len(train_dataset),
        "val_samples": len(val_dataset),
        "num_classes": 1000
    })

    # ✅ Good: Log environment info
    mlflow.log_params({
        "framework": "PyTorch 2.0",
        "cuda_version": torch.version.cuda,
        "gpu": torch.cuda.get_device_name(0)
    })
```

## Logging Metrics

### Time-Series Metrics

```python
with mlflow.start_run():
    for epoch in range(num_epochs):
        # Train
        train_loss, train_acc = train_epoch()

        # Validate
        val_loss, val_acc = validate()

        # Log metrics with step
        mlflow.log_metric("train_loss", train_loss, step=epoch)
        mlflow.log_metric("train_accuracy", train_acc, step=epoch)
        mlflow.log_metric("val_loss", val_loss, step=epoch)
        mlflow.log_metric("val_accuracy", val_acc, step=epoch)

        # Log learning rate
        current_lr = optimizer.param_groups[0]['lr']
        mlflow.log_metric("learning_rate", current_lr, step=epoch)
```

### Batch-Level Metrics

```python
with mlflow.start_run():
    global_step = 0

    for epoch in range(num_epochs):
        for batch_idx, (data, target) in enumerate(train_loader):
            loss = train_batch(data, target)

            # Log every 100 batches
            if global_step % 100 == 0:
                mlflow.log_metric("batch_loss", loss, step=global_step)

            global_step += 1

        # Log epoch metrics
        val_loss = validate()
        mlflow.log_metric("epoch_val_loss", val_loss, step=epoch)
```

### Multiple Metrics at Once

```python
with mlflow.start_run():
    metrics = {
        "train_loss": 0.15,
        "val_loss": 0.18,
        "train_accuracy": 0.95,
        "val_accuracy": 0.92,
        "f1_score": 0.93,
        "precision": 0.94,
        "recall": 0.92
    }

    mlflow.log_metrics(metrics, step=epoch)
```

### Custom Metrics

```python
def compute_custom_metrics(y_true, y_pred):
    """Compute custom evaluation metrics."""
    from sklearn.metrics import accuracy_score, f1_score, precision_score, recall_score

    return {
        "accuracy": accuracy_score(y_true, y_pred),
        "f1_macro": f1_score(y_true, y_pred, average='macro'),
        "f1_weighted": f1_score(y_true, y_pred, average='weighted'),
        "precision": precision_score(y_true, y_pred, average='weighted'),
        "recall": recall_score(y_true, y_pred, average='weighted')
    }

with mlflow.start_run():
    predictions = model.predict(X_test)
    metrics = compute_custom_metrics(y_test, predictions)

    # Log all metrics
    mlflow.log_metrics(metrics)
```

## Logging Artifacts

### Files and Directories

```python
with mlflow.start_run():
    # Log single file
    plt.savefig('loss_curve.png')
    mlflow.log_artifact('loss_curve.png')

    # Log directory
    os.makedirs('plots', exist_ok=True)
    plt.savefig('plots/train_loss.png')
    plt.savefig('plots/val_loss.png')
    mlflow.log_artifacts('plots')  # Logs entire directory

    # Log to specific artifact path
    mlflow.log_artifact('model.pkl', artifact_path='models')
    # Stored at: artifacts/models/model.pkl
```

### JSON and YAML

```python
import json
import yaml

with mlflow.start_run():
    # Log dict as JSON
    config = {"lr": 0.001, "batch_size": 32}
    mlflow.log_dict(config, "config.json")

    # Log as YAML
    with open('config.yaml', 'w') as f:
        yaml.dump(config, f)
    mlflow.log_artifact('config.yaml')
```

### Text Files

```python
with mlflow.start_run():
    # Log training summary
    summary = f"""
    Training Summary:
    - Epochs: {num_epochs}
    - Final train loss: {final_train_loss:.4f}
    - Final val loss: {final_val_loss:.4f}
    - Best accuracy: {best_acc:.4f}
    - Training time: {training_time:.2f}s
    """

    with open('summary.txt', 'w') as f:
        f.write(summary)

    mlflow.log_artifact('summary.txt')
```

### Model Checkpoints

```python
import torch

with mlflow.start_run():
    # Save checkpoint
    checkpoint = {
        'epoch': epoch,
        'model_state_dict': model.state_dict(),
        'optimizer_state_dict': optimizer.state_dict(),
        'loss': loss,
        'accuracy': accuracy
    }

    torch.save(checkpoint, f'checkpoint_epoch_{epoch}.pth')
    mlflow.log_artifact(f'checkpoint_epoch_{epoch}.pth', artifact_path='checkpoints')
```

## Logging Models

### Framework-Specific Logging

```python
# Scikit-learn
import mlflow.sklearn

with mlflow.start_run():
    model = train_sklearn_model()
    mlflow.sklearn.log_model(model, "model")

# PyTorch
import mlflow.pytorch

with mlflow.start_run():
    model = train_pytorch_model()
    mlflow.pytorch.log_model(model, "model")

# TensorFlow/Keras
import mlflow.keras

with mlflow.start_run():
    model = train_keras_model()
    mlflow.keras.log_model(model, "model")

# XGBoost
import mlflow.xgboost

with mlflow.start_run():
    model = train_xgboost_model()
    mlflow.xgboost.log_model(model, "model")
```

### Log Model with Signature

```python
from mlflow.models.signature import infer_signature
import mlflow.sklearn

with mlflow.start_run():
    model = train_model()

    # Infer signature from training data
    signature = infer_signature(X_train, model.predict(X_train))

    # Log with signature
    mlflow.sklearn.log_model(
        model,
        "model",
        signature=signature
    )
```

### Log Model with Input Example

```python
with mlflow.start_run():
    model = train_model()

    # Log with input example
    input_example = X_train[:5]

    mlflow.sklearn.log_model(
        model,
        "model",
        signature=signature,
        input_example=input_example
    )
```

### Log Model to Registry

```python
with mlflow.start_run():
    model = train_model()

    # Log and register in one step
    mlflow.sklearn.log_model(
        model,
        "model",
        registered_model_name="my-classifier"  # Register immediately
    )
```

## Autologging

### Enable Autologging

```python
import mlflow

# Enable for all frameworks
mlflow.autolog()

# Or framework-specific
mlflow.sklearn.autolog()
mlflow.pytorch.autolog()
mlflow.keras.autolog()
mlflow.xgboost.autolog()
mlflow.lightgbm.autolog()
```

### Autologging with Scikit-learn

```python
import mlflow
from sklearn.ensemble import RandomForestClassifier

mlflow.sklearn.autolog()

with mlflow.start_run():
    model = RandomForestClassifier(n_estimators=100, max_depth=5)
    model.fit(X_train, y_train)

    # Automatically logs:
    # - Parameters: n_estimators, max_depth, etc.
    # - Metrics: training score, test score
    # - Model: pickled model
    # - Training time
```

### Autologging with PyTorch Lightning

```python
import mlflow
import pytorch_lightning as pl

mlflow.pytorch.autolog()

with mlflow.start_run():
    trainer = pl.Trainer(max_epochs=10)
    trainer.fit(model, datamodule=dm)

    # Automatically logs:
    # - Hyperparameters from model and trainer
    # - Training and validation metrics
    # - Model checkpoints
```

### Disable Autologging

```python
# Disable for specific framework
mlflow.sklearn.autolog(disable=True)

# Disable all
mlflow.autolog(disable=True)
```

### Configure Autologging

```python
mlflow.sklearn.autolog(
    log_input_examples=True,  # Log input examples
    log_model_signatures=True,  # Log model signatures
    log_models=True,  # Log models
    disable=False,
    exclusive=False,
    disable_for_unsupported_versions=False,
    silent=False
)
```

## Runs and Experiments

### Create Experiment

```python
# Create experiment
experiment_id = mlflow.create_experiment(
    "my-experiment",
    artifact_location="s3://my-bucket/mlflow",
    tags={"project": "classification", "team": "ml-team"}
)

# Set active experiment
mlflow.set_experiment("my-experiment")

# Get experiment
experiment = mlflow.get_experiment_by_name("my-experiment")
print(f"Experiment ID: {experiment.experiment_id}")
```

### Nested Runs

```python
# Parent run
with mlflow.start_run(run_name="hyperparameter-tuning"):
    parent_run_id = mlflow.active_run().info.run_id

    # Child runs
    for lr in [0.001, 0.01, 0.1]:
        with mlflow.start_run(run_name=f"lr-{lr}", nested=True):
            mlflow.log_param("learning_rate", lr)
            model = train(lr)
            accuracy = evaluate(model)
            mlflow.log_metric("accuracy", accuracy)
```

### Run Tags

```python
with mlflow.start_run():
    # Set tags
    mlflow.set_tags({
        "model_type": "ResNet50",
        "dataset": "ImageNet",
        "git_commit": get_git_commit(),
        "developer": "alice@company.com"
    })

    # Single tag
    mlflow.set_tag("production_ready", "true")
```

### Run Notes

```python
with mlflow.start_run():
    # Add notes
    mlflow.set_tag("mlflow.note.content", """
    ## Experiment Notes

    - Using pretrained ResNet50
    - Fine-tuning last 2 layers
    - Data augmentation: random flip, crop, rotation
    - Learning rate schedule: cosine annealing

    ## Results
    - Best validation accuracy: 95.2%
    - Converged after 35 epochs
    """)
```

## Searching and Comparing

### Search Runs

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()

# Get experiment
experiment = mlflow.get_experiment_by_name("my-experiment")
experiment_id = experiment.experiment_id

# Search all runs
runs = client.search_runs(
    experiment_ids=[experiment_id],
    filter_string="",
    order_by=["metrics.accuracy DESC"],
    max_results=10
)

for run in runs:
    print(f"Run ID: {run.info.run_id}")
    print(f"Accuracy: {run.data.metrics.get('accuracy', 'N/A')}")
    print(f"Params: {run.data.params}")
    print("---")
```

### Filter Runs

```python
# Filter by metric
runs = client.search_runs(
    experiment_ids=[experiment_id],
    filter_string="metrics.accuracy > 0.9"
)

# Filter by parameter
runs = client.search_runs(
    experiment_ids=[experiment_id],
    filter_string="params.model = 'ResNet50'"
)

# Complex filter
runs = client.search_runs(
    experiment_ids=[experiment_id],
    filter_string="""
        metrics.accuracy > 0.9 AND
        params.learning_rate < 0.01 AND
        tags.dataset = 'ImageNet'
    """
)
```

### Compare Best Runs

```python
def compare_best_runs(experiment_name, metric="accuracy", top_n=5):
    """Compare top N runs by metric."""
    experiment = mlflow.get_experiment_by_name(experiment_name)
    client = MlflowClient()

    runs = client.search_runs(
        experiment_ids=[experiment.experiment_id],
        filter_string=f"metrics.{metric} > 0",
        order_by=[f"metrics.{metric} DESC"],
        max_results=top_n
    )

    print(f"Top {top_n} runs by {metric}:")
    print("-" * 80)

    for i, run in enumerate(runs, 1):
        print(f"{i}. Run ID: {run.info.run_id}")
        print(f"   {metric}: {run.data.metrics.get(metric, 'N/A')}")
        print(f"   Params: {run.data.params}")
        print()

compare_best_runs("my-experiment", metric="accuracy", top_n=5)
```

### Download Artifacts

```python
client = MlflowClient()

# Download artifact
run_id = "abc123"
local_path = client.download_artifacts(run_id, "model")
print(f"Downloaded to: {local_path}")

# Download specific file
local_file = client.download_artifacts(run_id, "plots/loss_curve.png")
```

## Best Practices

### 1. Use Descriptive Names

```python
# ✅ Good: Descriptive experiment and run names
mlflow.set_experiment("sentiment-analysis-bert")

with mlflow.start_run(run_name="bert-base-lr1e-5-bs32-epochs10"):
    train()

# ❌ Bad: Generic names
mlflow.set_experiment("experiment1")
with mlflow.start_run():
    train()
```

### 2. Log Comprehensive Metadata

```python
with mlflow.start_run():
    # Hyperparameters
    mlflow.log_params(config)

    # System info
    mlflow.set_tags({
        "git_commit": get_git_commit(),
        "framework": f"PyTorch {torch.__version__}",
        "cuda": torch.version.cuda,
        "gpu": torch.cuda.get_device_name(0)
    })

    # Data info
    mlflow.log_params({
        "train_samples": len(train_dataset),
        "val_samples": len(val_dataset),
        "num_classes": num_classes
    })
```

### 3. Track Time

```python
import time

with mlflow.start_run():
    start_time = time.time()

    # Training
    model = train()

    # Log training time
    training_time = time.time() - start_time
    mlflow.log_metric("training_time_seconds", training_time)
```

### 4. Version Control Integration

```python
import subprocess

def get_git_commit():
    """Get current git commit hash."""
    try:
        return subprocess.check_output(
            ['git', 'rev-parse', 'HEAD']
        ).decode('ascii').strip()
    except:
        return "unknown"

with mlflow.start_run():
    mlflow.set_tag("git_commit", get_git_commit())
    mlflow.set_tag("git_branch", get_git_branch())
```

### 5. Error Handling

```python
with mlflow.start_run():
    try:
        model = train()
        mlflow.set_tag("status", "completed")
    except Exception as e:
        mlflow.set_tag("status", "failed")
        mlflow.set_tag("error", str(e))
        raise
```

## Resources

- **Tracking API**: https://mlflow.org/docs/latest/tracking.html
- **Python API**: https://mlflow.org/docs/latest/python_api/mlflow.html
- **Examples**: https://github.com/mlflow/mlflow/tree/master/examples




### Model Registry

# Model Registry Guide

Complete guide to MLflow Model Registry for versioning, lifecycle management, and collaboration.

## Table of Contents
- What is Model Registry
- Registering Models
- Model Versions
- Stage Transitions
- Model Aliases (Modern Approach)
- Searching Models
- Model Annotations
- Collaborative Workflows
- Best Practices

## What is Model Registry

The Model Registry is a centralized model store for managing the full lifecycle of MLflow Models.

**Key Features:**
- **Versioning**: Automatic version increments (v1, v2, v3...)
- **Stages**: None, Staging, Production, Archived (legacy)
- **Aliases**: champion, challenger, latest (modern approach)
- **Annotations**: Descriptions, tags, metadata
- **Lineage**: Track which runs produced models
- **Collaboration**: Team-wide model governance
- **Deployment**: Single source of truth for production models

**Use Cases:**
- Model approval workflows
- A/B testing (champion vs challenger)
- Production deployment tracking
- Model performance monitoring
- Regulatory compliance

## Registering Models

### Register During Training

```python
import mlflow
import mlflow.sklearn

with mlflow.start_run():
    model = train_model()

    # Log and register in one step
    mlflow.sklearn.log_model(
        model,
        "model",
        registered_model_name="product-classifier"  # Creates or updates
    )
```

### Register After Training

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()

# Get run ID from experiment
run_id = "abc123"

# Register model from run
model_uri = f"runs:/{run_id}/model"
result = mlflow.register_model(
    model_uri,
    "product-classifier"
)

print(f"Model name: {result.name}")
print(f"Version: {result.version}")
```

### Register with Signature

```python
from mlflow.models.signature import infer_signature

with mlflow.start_run():
    model = train_model()

    # Infer signature
    signature = infer_signature(X_train, model.predict(X_train))

    # Register with signature
    mlflow.sklearn.log_model(
        model,
        "model",
        signature=signature,
        registered_model_name="product-classifier"
    )
```

## Model Versions

### Automatic Versioning

```python
# First registration: creates version 1
with mlflow.start_run():
    model_v1 = train_model()
    mlflow.sklearn.log_model(model_v1, "model", registered_model_name="my-model")
    # Result: my-model version 1

# Second registration: creates version 2
with mlflow.start_run():
    model_v2 = train_improved_model()
    mlflow.sklearn.log_model(model_v2, "model", registered_model_name="my-model")
    # Result: my-model version 2

# Third registration: creates version 3
with mlflow.start_run():
    model_v3 = train_best_model()
    mlflow.sklearn.log_model(model_v3, "model", registered_model_name="my-model")
    # Result: my-model version 3
```

### List Model Versions

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()

# Get all versions
versions = client.search_model_versions("name='product-classifier'")

for v in versions:
    print(f"Version {v.version}:")
    print(f"  Stage: {v.current_stage}")
    print(f"  Run ID: {v.run_id}")
    print(f"  Created: {v.creation_timestamp}")
    print(f"  Status: {v.status}")
    print()
```

### Get Specific Version

```python
client = MlflowClient()

# Get version details
version_info = client.get_model_version(
    name="product-classifier",
    version="3"
)

print(f"Version: {version_info.version}")
print(f"Stage: {version_info.current_stage}")
print(f"Run ID: {version_info.run_id}")
print(f"Description: {version_info.description}")
print(f"Tags: {version_info.tags}")
```

### Get Latest Version

```python
# Get latest version in Production stage
latest_prod = client.get_latest_versions(
    "product-classifier",
    stages=["Production"]
)

# Get latest version in Staging
latest_staging = client.get_latest_versions(
    "product-classifier",
    stages=["Staging"]
)

# Get all latest versions (one per stage)
all_latest = client.get_latest_versions("product-classifier")
```

## Stage Transitions

**Note**: Stages are deprecated in MLflow 2.9+. Use aliases instead (see next section).

### Available Stages

- **None**: Initial state, not yet tested
- **Staging**: Under testing/validation
- **Production**: Deployed in production
- **Archived**: Retired/deprecated

### Transition Model

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()

# Promote to Staging
client.transition_model_version_stage(
    name="product-classifier",
    version=3,
    stage="Staging"
)

# Promote to Production (archive old production versions)
client.transition_model_version_stage(
    name="product-classifier",
    version=3,
    stage="Production",
    archive_existing_versions=True  # Archive old production models
)

# Archive old version
client.transition_model_version_stage(
    name="product-classifier",
    version=2,
    stage="Archived"
)
```

### Load Model by Stage

```python
import mlflow.pyfunc

# Load production model
model = mlflow.pyfunc.load_model("models:/product-classifier/Production")

# Load staging model
staging_model = mlflow.pyfunc.load_model("models:/product-classifier/Staging")

# Load specific version
model_v3 = mlflow.pyfunc.load_model("models:/product-classifier/3")

# Use model
predictions = model.predict(X_test)
```

## Model Aliases (Modern Approach)

**Introduced in MLflow 2.8** - Flexible alternative to stages.

### Set Aliases

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()

# Set champion alias (current production model)
client.set_registered_model_alias(
    name="product-classifier",
    alias="champion",
    version="5"
)

# Set challenger alias (candidate for production)
client.set_registered_model_alias(
    name="product-classifier",
    alias="challenger",
    version="6"
)

# Set latest alias
client.set_registered_model_alias(
    name="product-classifier",
    alias="latest",
    version="7"
)
```

### Load Model by Alias

```python
import mlflow.pyfunc

# Load champion model
champion = mlflow.pyfunc.load_model("models:/product-classifier@champion")

# Load challenger model
challenger = mlflow.pyfunc.load_model("models:/product-classifier@challenger")

# Load latest model
latest = mlflow.pyfunc.load_model("models:/product-classifier@latest")

# Use for A/B testing
champion_preds = champion.predict(X_test)
challenger_preds = challenger.predict(X_test)
```

### Get Model by Alias

```python
client = MlflowClient()

# Get version info by alias
version_info = client.get_model_version_by_alias(
    name="product-classifier",
    alias="champion"
)

print(f"Champion is version: {version_info.version}")
print(f"Run ID: {version_info.run_id}")
```

### Delete Alias

```python
# Remove alias
client.delete_registered_model_alias(
    name="product-classifier",
    alias="challenger"
)
```

## Searching Models

### Search All Models

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()

# List all registered models
models = client.search_registered_models()

for model in models:
    print(f"Name: {model.name}")
    print(f"Description: {model.description}")
    print(f"Latest versions: {model.latest_versions}")
    print()
```

### Search by Name

```python
# Search by name pattern
models = client.search_registered_models(
    filter_string="name LIKE 'product-%'"
)

# Search exact name
models = client.search_registered_models(
    filter_string="name='product-classifier'"
)
```

### Search Model Versions

```python
# Find all versions of a model
versions = client.search_model_versions("name='product-classifier'")

# Find production versions
versions = client.search_model_versions(
    "name='product-classifier' AND current_stage='Production'"
)

# Find versions from specific run
versions = client.search_model_versions(
    f"run_id='{run_id}'"
)
```

## Model Annotations

### Add Description

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()

# Update model description
client.update_registered_model(
    name="product-classifier",
    description="ResNet50 classifier for product categorization. Trained on 1M images with 95% accuracy."
)

# Update version description
client.update_model_version(
    name="product-classifier",
    version="3",
    description="Best performing model. Validation accuracy: 95.2%. Tested on 50K images."
)
```

### Add Tags

```python
client = MlflowClient()

# Add tags to model
client.set_registered_model_tag(
    name="product-classifier",
    key="task",
    value="classification"
)

client.set_registered_model_tag(
    name="product-classifier",
    key="domain",
    value="e-commerce"
)

# Add tags to specific version
client.set_model_version_tag(
    name="product-classifier",
    version="3",
    key="validation_status",
    value="approved"
)

client.set_model_version_tag(
    name="product-classifier",
    version="3",
    key="deployed_date",
    value="2025-01-15"
)

client.set_model_version_tag(
    name="product-classifier",
    version="3",
    key="approved_by",
    value="ml-team-lead"
)
```

### Delete Tags

```python
# Delete model tag
client.delete_registered_model_tag(
    name="product-classifier",
    key="old_tag"
)

# Delete version tag
client.delete_model_version_tag(
    name="product-classifier",
    version="3",
    key="old_version_tag"
)
```

## Collaborative Workflows

### Model Approval Workflow

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()

# 1. Data scientist trains and registers model
with mlflow.start_run():
    model = train_model()
    mlflow.sklearn.log_model(
        model,
        "model",
        registered_model_name="product-classifier"
    )
    run_id = mlflow.active_run().info.run_id

# 2. Add metadata for review
version = client.get_latest_versions("product-classifier")[0].version
client.update_model_version(
    name="product-classifier",
    version=version,
    description=f"Accuracy: 95%, F1: 0.93, Run: {run_id}"
)

client.set_model_version_tag(
    name="product-classifier",
    version=version,
    key="status",
    value="awaiting_review"
)

# 3. ML engineer reviews and tests
test_accuracy = evaluate_model(model)

if test_accuracy > 0.9:
    # Approve and promote to staging
    client.set_model_version_tag(
        name="product-classifier",
        version=version,
        key="status",
        value="approved"
    )

    client.transition_model_version_stage(
        name="product-classifier",
        version=version,
        stage="Staging"
    )

# 4. After staging validation, promote to production
if staging_tests_pass():
    client.transition_model_version_stage(
        name="product-classifier",
        version=version,
        stage="Production",
        archive_existing_versions=True
    )

    client.set_model_version_tag(
        name="product-classifier",
        version=version,
        key="deployed_by",
        value="ml-ops-team"
    )
```

### A/B Testing Workflow

```python
# Set up champion vs challenger
client = MlflowClient()

# Champion: Current production model
client.set_registered_model_alias(
    name="product-classifier",
    alias="champion",
    version="5"
)

# Challenger: New candidate model
client.set_registered_model_alias(
    name="product-classifier",
    alias="challenger",
    version="6"
)

# In production code
import random

def get_model_for_request():
    """Route 90% to champion, 10% to challenger."""
    if random.random() < 0.9:
        return mlflow.pyfunc.load_model("models:/product-classifier@champion")
    else:
        return mlflow.pyfunc.load_model("models:/product-classifier@challenger")

# After A/B test completes
if challenger_performs_better():
    # Promote challenger to champion
    client.set_registered_model_alias(
        name="product-classifier",
        alias="champion",
        version="6"
    )

    # Archive old champion
    client.delete_registered_model_alias(
        name="product-classifier",
        alias="challenger"
    )
```

### Model Rollback

```python
client = MlflowClient()

# Emergency rollback to previous production version
previous_version = "4"

client.transition_model_version_stage(
    name="product-classifier",
    version=previous_version,
    stage="Production",
    archive_existing_versions=True
)

# Add rollback metadata
client.set_model_version_tag(
    name="product-classifier",
    version=previous_version,
    key="rollback_reason",
    value="Performance degradation in production"
)

client.set_model_version_tag(
    name="product-classifier",
    version=previous_version,
    key="rollback_date",
    value="2025-01-15"
)
```

## Best Practices

### 1. Use Descriptive Names

```python
# ✅ Good: Descriptive, domain-specific names
mlflow.sklearn.log_model(model, "model", registered_model_name="ecommerce-product-classifier")
mlflow.sklearn.log_model(model, "model", registered_model_name="fraud-detection-xgboost")

# ❌ Bad: Generic names
mlflow.sklearn.log_model(model, "model", registered_model_name="model1")
mlflow.sklearn.log_model(model, "model", registered_model_name="classifier")
```

### 2. Always Add Descriptions

```python
client = MlflowClient()

# Add detailed version description
client.update_model_version(
    name="product-classifier",
    version="5",
    description="""
    ResNet50 classifier for product categorization

    Performance:
    - Validation Accuracy: 95.2%
    - F1 Score: 0.93
    - Inference Time: 15ms

    Training:
    - Dataset: ImageNet subset (1.2M images)
    - Augmentation: Random flip, crop, rotation
    - Epochs: 50
    - Batch Size: 32

    Notes:
    - Pretrained on ImageNet
    - Fine-tuned last 2 layers
    - Handles 1000 product categories
    """
)
```

### 3. Use Tags for Metadata

```python
# Add comprehensive tags
tags = {
    # Performance
    "accuracy": "0.952",
    "f1_score": "0.93",
    "inference_time_ms": "15",

    # Training
    "dataset": "imagenet-subset",
    "num_samples": "1200000",
    "epochs": "50",

    # Validation
    "validation_status": "approved",
    "tested_by": "ml-team",
    "test_date": "2025-01-10",

    # Deployment
    "deployed_date": "2025-01-15",
    "deployed_by": "mlops-team",
    "environment": "production",

    # Business
    "use_case": "product-categorization",
    "owner": "data-science-team",
    "stakeholder": "ecommerce-team"
}

for key, value in tags.items():
    client.set_model_version_tag(
        name="product-classifier",
        version="5",
        key=key,
        value=value
    )
```

### 4. Use Aliases Instead of Stages

```python
# ✅ Modern: Use aliases (MLflow 2.8+)
client.set_registered_model_alias(name="my-model", alias="champion", version="5")
client.set_registered_model_alias(name="my-model", alias="challenger", version="6")
model = mlflow.pyfunc.load_model("models:/my-model@champion")

# ⚠️ Legacy: Stages (deprecated in MLflow 2.9+)
client.transition_model_version_stage(name="my-model", version=5, stage="Production")
model = mlflow.pyfunc.load_model("models:/my-model/Production")
```

### 5. Track Model Lineage

```python
# Link model version to training run
with mlflow.start_run(run_name="product-classifier-training") as run:
    # Log training metrics
    mlflow.log_params(config)
    mlflow.log_metrics(metrics)

    # Register model
    mlflow.sklearn.log_model(
        model,
        "model",
        registered_model_name="product-classifier"
    )

    run_id = run.info.run_id

# Add lineage metadata
version = client.get_latest_versions("product-classifier")[0].version
client.set_model_version_tag(
    name="product-classifier",
    version=version,
    key="training_run_id",
    value=run_id
)

# Add data lineage
client.set_model_version_tag(
    name="product-classifier",
    version=version,
    key="dataset_version",
    value="imagenet-v2-2025-01"
)
```

### 6. Implement Approval Gates

```python
def promote_to_production(model_name, version, min_accuracy=0.9):
    """Promote model to production with validation checks."""
    client = MlflowClient()

    # 1. Validate performance
    version_info = client.get_model_version(name=model_name, version=version)

    # Check if approved
    tags = version_info.tags
    if tags.get("validation_status") != "approved":
        raise ValueError("Model not approved for production")

    # Check accuracy threshold
    accuracy = float(tags.get("accuracy", 0))
    if accuracy < min_accuracy:
        raise ValueError(f"Accuracy {accuracy} below threshold {min_accuracy}")

    # 2. Promote to production
    client.transition_model_version_stage(
        name=model_name,
        version=version,
        stage="Production",
        archive_existing_versions=True
    )

    # 3. Add deployment metadata
    from datetime import datetime
    client.set_model_version_tag(
        name=model_name,
        version=version,
        key="deployed_date",
        value=datetime.now().isoformat()
    )

    print(f"✅ Promoted {model_name} v{version} to production")

# Use it
promote_to_production("product-classifier", "5", min_accuracy=0.9)
```

## Resources

- **Model Registry**: https://mlflow.org/docs/latest/model-registry.html
- **Model Aliases**: https://mlflow.org/docs/latest/model-registry.html#using-model-aliases
- **Python API**: https://mlflow.org/docs/latest/python_api/mlflow.tracking.html#mlflow.tracking.MlflowClient




---

## 🚀 Usage

**Reference this template:** `@skill-mlflow.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
