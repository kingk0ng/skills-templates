---
id: skill-langsmith-observability
type: skill
name: langsmith-observability
description: LLM observability platform for tracing, evaluation, and monitoring. Use
  when debugging LLM applications, evaluating model outputs against datasets, monitoring
  production systems, or building systematic testing pipelines for AI applications.
category: ai-research
complexity: medium
keywords:
- api
- ci/cd
- github
- performance
- python
- qa
- test
- testing
capabilities: []
token_estimate: 1301
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,301 -->
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




# langsmith-observability

> LLM observability platform for tracing, evaluation, and monitoring. Use when debugging LLM applications, evaluating model outputs against datasets, monitoring production systems, or building systematic testing pipelines for AI applications.

# LangSmith - LLM Observability Platform

Development platform for debugging, evaluating, and monitoring language models and AI applications.

## When to use LangSmith

**Use LangSmith when:**
- Debugging LLM application issues (prompts, chains, agents)
- Evaluating model outputs systematically against datasets
- Monitoring production LLM systems
- Building regression testing for AI features
- Analyzing latency, token usage, and costs
- Collaborating on prompt engineering

**Key features:**
- **Tracing**: Capture inputs, outputs, latency for all LLM calls
- **Evaluation**: Systematic testing with built-in and custom evaluators
- **Datasets**: Create test sets from production traces or manually
- **Monitoring**: Track metrics, errors, and costs in production
- **Integrations**: Works with OpenAI, Anthropic, LangChain, LlamaIndex

**Use alternatives instead:**
- **Weights & Biases**: Deep learning experiment tracking, model training
- **MLflow**: General ML lifecycle, model registry focus
- **Arize/WhyLabs**: ML monitoring, data drift detection

## Quick start

### Installation

```bash
pip install langsmith

# Set environment variables
export LANGSMITH_API_KEY="your-api-key"
export LANGSMITH_TRACING=true
```

### Basic tracing with @traceable

```python
from langsmith import traceable
from openai import OpenAI

client = OpenAI()

@traceable
def generate_response(prompt: str) -> str:
    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": prompt}]
    )
    return response.choices[0].message.content

# Automatically traced to LangSmith
result = generate_response("What is machine learning?")
```

### OpenAI wrapper (automatic tracing)

```python
from langsmith.wrappers import wrap_openai
from openai import OpenAI

# Wrap client for automatic tracing
client = wrap_openai(OpenAI())

# All calls automatically traced
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Hello!"}]
)
```

## Core concepts

### Runs and traces

A **run** is a single execution unit (LLM call, chain, tool). Runs form hierarchical **traces** showing the full execution flow.

```python
from langsmith import traceable

@traceable(run_type="chain")
def process_query(query: str) -> str:
    # Parent run
    context = retrieve_context(query)  # Child run
    response = generate_answer(query, context)  # Child run
    return response

@traceable(run_type="retriever")
def retrieve_context(query: str) -> list:
    return vector_store.search(query)

@traceable(run_type="llm")
def generate_answer(query: str, context: list) -> str:
    return llm.invoke(f"Context: {context}\n\nQuestion: {query}")
```

### Projects

Projects organize related runs. Set via environment or code:

```python
import os
os.environ["LANGSMITH_PROJECT"] = "my-project"

# Or per-function
@traceable(project_name="my-project")
def my_function():
    pass
```

## Client API

```python
from langsmith import Client

client = Client()

# List runs
runs = list(client.list_runs(
    project_name="my-project",
    filter='eq(status, "success")',
    limit=100
))

# Get run details
run = client.read_run(run_id="...")

# Create feedback
client.create_feedback(
    run_id="...",
    key="correctness",
    score=0.9,
    comment="Good answer"
)
```

## Datasets and evaluation

### Create dataset

```python
from langsmith import Client

client = Client()

# Create dataset
dataset = client.create_dataset("qa-test-set", description="QA evaluation")

# Add examples
client.create_examples(
    inputs=[
        {"question": "What is Python?"},
        {"question": "What is ML?"}
    ],
    outputs=[
        {"answer": "A programming language"},
        {"answer": "Machine learning"}
    ],
    dataset_id=dataset.id
)
```

### Run evaluation

```python
from langsmith import evaluate

def my_model(inputs: dict) -> dict:
    # Your model logic
    return {"answer": generate_answer(inputs["question"])}

def correctness_evaluator(run, example):
    prediction = run.outputs["answer"]
    reference = example.outputs["answer"]
    score = 1.0 if reference.lower() in prediction.lower() else 0.0
    return {"key": "correctness", "score": score}

results = evaluate(
    my_model,
    data="qa-test-set",
    evaluators=[correctness_evaluator],
    experiment_prefix="v1"
)

print(f"Average score: {results.aggregate_metrics['correctness']}")
```

### Built-in evaluators

```python
from langsmith.evaluation import LangChainStringEvaluator

# Use LangChain evaluators
results = evaluate(
    my_model,
    data="qa-test-set",
    evaluators=[
        LangChainStringEvaluator("qa"),
        LangChainStringEvaluator("cot_qa")
    ]
)
```

## Advanced tracing

### Tracing context

```python
from langsmith import tracing_context

with tracing_context(
    project_name="experiment-1",
    tags=["production", "v2"],
    metadata={"version": "2.0"}
):
    # All traceable calls inherit context
    result = my_function()
```

### Manual runs

```python
from langsmith import trace

with trace(
    name="custom_operation",
    run_type="tool",
    inputs={"query": "test"}
) as run:
    result = do_something()
    run.end(outputs={"result": result})
```

### Process inputs/outputs

```python
def sanitize_inputs(inputs: dict) -> dict:
    if "password" in inputs:
        inputs["password"] = "***"
    return inputs

@traceable(process_inputs=sanitize_inputs)
def login(username: str, password: str):
    return authenticate(username, password)
```

### Sampling

```python
import os
os.environ["LANGSMITH_TRACING_SAMPLING_RATE"] = "0.1"  # 10% sampling
```

## LangChain integration

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate

# Tracing enabled automatically with LANGSMITH_TRACING=true
llm = ChatOpenAI(model="gpt-4o")
prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant."),
    ("user", "{input}")
])

chain = prompt | llm

# All chain runs traced automatically
response = chain.invoke({"input": "Hello!"})
```

## Production monitoring

### Hub prompts

```python
from langsmith import Client

client = Client()

# Pull prompt from hub
prompt = client.pull_prompt("my-org/qa-prompt")

# Use in application
result = prompt.invoke({"question": "What is AI?"})
```

### Async client

```python
from langsmith import AsyncClient

async def main():
    client = AsyncClient()

    runs = []
    async for run in client.list_runs(project_name="my-project"):
        runs.append(run)

    return runs
```

### Feedback collection

```python
from langsmith import Client

client = Client()

# Collect user feedback
def record_feedback(run_id: str, user_rating: int, comment: str = None):
    client.create_feedback(
        run_id=run_id,
        key="user_rating",
        score=user_rating / 5.0,  # Normalize to 0-1
        comment=comment
    )

# In your application
record_feedback(run_id="...", user_rating=4, comment="Helpful response")
```

## Testing integration

### Pytest integration

```python
from langsmith import test

@test
def test_qa_accuracy():
    result = my_qa_function("What is Python?")
    assert "programming" in result.lower()
```

### Evaluation in CI/CD

```python
from langsmith import evaluate

def run_evaluation():
    results = evaluate(
        my_model,
        data="regression-test-set",
        evaluators=[accuracy_evaluator]
    )

    # Fail CI if accuracy drops
    assert results.aggregate_metrics["accuracy"] >= 0.9, \
        f"Accuracy {results.aggregate_metrics['accuracy']} below threshold"
```

## Best practices

1. **Structured naming** - Use consistent project/run naming conventions
2. **Add metadata** - Include version, environment, user info
3. **Sample in production** - Use sampling rate to control volume
4. **Create datasets** - Build test sets from interesting production cases
5. **Automate evaluation** - Run evaluations in CI/CD pipelines
6. **Monitor costs** - Track token usage and latency trends

## Common issues

**Traces not appearing:**
```python
import os
# Ensure tracing is enabled
os.environ["LANGSMITH_TRACING"] = "true"
os.environ["LANGSMITH_API_KEY"] = "your-key"

# Verify connection
from langsmith import Client
client = Client()
print(client.list_projects())  # Should work
```

**High latency from tracing:**
```python
# Enable background batching (default)
from langsmith import Client
client = Client(auto_batch_tracing=True)

# Or use sampling
os.environ["LANGSMITH_TRACING_SAMPLING_RATE"] = "0.1"
```

**Large payloads:**
```python
# Hide sensitive/large fields
@traceable(
    process_inputs=lambda x: {k: v for k, v in x.items() if k != "large_field"}
)
def my_function(data):
    pass
```

## References

- **[Advanced Usage](references/advanced-usage.md)** - Custom evaluators, distributed tracing, hub prompts
- **[Troubleshooting](references/troubleshooting.md)** - Common issues, debugging, performance

## Resources

- **Documentation**: https://docs.smith.langchain.com
- **Python SDK**: https://github.com/langchain-ai/langsmith-sdk
- **Web App**: https://smith.langchain.com
- **Version**: 0.2.0+
- **License**: MIT


---


## 📚 Reference Materials


### Advanced Usage

# LangSmith Advanced Usage Guide

## Custom Evaluators

### Simple Custom Evaluator

```python
from langsmith import evaluate

def accuracy_evaluator(run, example):
    """Check if prediction matches reference."""
    prediction = run.outputs.get("answer", "")
    reference = example.outputs.get("answer", "")

    score = 1.0 if prediction.strip().lower() == reference.strip().lower() else 0.0

    return {
        "key": "accuracy",
        "score": score,
        "comment": f"Predicted: {prediction[:50]}..."
    }

results = evaluate(
    my_model,
    data="test-dataset",
    evaluators=[accuracy_evaluator]
)
```

### LLM-as-Judge Evaluator

```python
from langsmith import evaluate
from openai import OpenAI

client = OpenAI()

def llm_judge_evaluator(run, example):
    """Use LLM to evaluate response quality."""
    prediction = run.outputs.get("answer", "")
    question = example.inputs.get("question", "")
    reference = example.outputs.get("answer", "")

    prompt = f"""Evaluate the following response for accuracy and helpfulness.

Question: {question}
Reference Answer: {reference}
Model Response: {prediction}

Rate on a scale of 1-5:
1 = Completely wrong
5 = Perfect answer

Respond with just the number."""

    response = client.chat.completions.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": prompt}],
        max_tokens=10
    )

    try:
        score = int(response.choices[0].message.content.strip()) / 5.0
    except ValueError:
        score = 0.5

    return {
        "key": "llm_judge",
        "score": score,
        "comment": response.choices[0].message.content
    }

results = evaluate(
    my_model,
    data="test-dataset",
    evaluators=[llm_judge_evaluator]
)
```

### Async Evaluator

```python
from langsmith import aevaluate
import asyncio

async def async_evaluator(run, example):
    """Async evaluator for concurrent evaluation."""
    prediction = run.outputs.get("answer", "")

    # Async operation (e.g., API call)
    score = await compute_similarity_async(prediction, example.outputs["answer"])

    return {"key": "similarity", "score": score}

async def run_async_eval():
    results = await aevaluate(
        async_model,
        data="test-dataset",
        evaluators=[async_evaluator],
        max_concurrency=10
    )
    return results

results = asyncio.run(run_async_eval())
```

### Multiple Return Values

```python
def comprehensive_evaluator(run, example):
    """Return multiple evaluation results."""
    prediction = run.outputs.get("answer", "")
    reference = example.outputs.get("answer", "")

    return [
        {"key": "exact_match", "score": 1.0 if prediction == reference else 0.0},
        {"key": "length_ratio", "score": min(len(prediction) / max(len(reference), 1), 1.0)},
        {"key": "contains_reference", "score": 1.0 if reference.lower() in prediction.lower() else 0.0}
    ]
```

## Summary Evaluators

```python
def summary_evaluator(runs, examples):
    """Compute aggregate metrics across all runs."""
    total_latency = sum(
        (run.end_time - run.start_time).total_seconds()
        for run in runs if run.end_time and run.start_time
    )

    avg_latency = total_latency / len(runs) if runs else 0

    return {
        "key": "avg_latency",
        "score": avg_latency
    }

results = evaluate(
    my_model,
    data="test-dataset",
    evaluators=[accuracy_evaluator],
    summary_evaluators=[summary_evaluator]
)
```

## Comparative Evaluation

```python
from langsmith import evaluate_comparative

def pairwise_judge(runs, example):
    """Compare two model outputs."""
    output_a = runs[0].outputs.get("answer", "")
    output_b = runs[1].outputs.get("answer", "")
    reference = example.outputs.get("answer", "")

    # Use LLM to compare
    prompt = f"""Compare these two answers to the question.

Question: {example.inputs['question']}
Reference: {reference}

Answer A: {output_a}
Answer B: {output_b}

Which is better? Respond with 'A', 'B', or 'TIE'."""

    response = llm.invoke(prompt)

    if "A" in response:
        return {"key": "preference", "scores": {"model_a": 1.0, "model_b": 0.0}}
    elif "B" in response:
        return {"key": "preference", "scores": {"model_a": 0.0, "model_b": 1.0}}
    else:
        return {"key": "preference", "scores": {"model_a": 0.5, "model_b": 0.5}}

results = evaluate_comparative(
    ["experiment-a-id", "experiment-b-id"],
    evaluators=[pairwise_judge]
)
```

## Advanced Tracing

### Run Trees

```python
from langsmith import RunTree

# Create root run
root = RunTree(
    name="complex_pipeline",
    run_type="chain",
    inputs={"query": "What is AI?"},
    project_name="my-project"
)

# Create child run
child = root.create_child(
    name="retrieval_step",
    run_type="retriever",
    inputs={"query": "What is AI?"}
)

# Execute and record
docs = retriever.invoke("What is AI?")
child.end(outputs={"documents": docs})

# Another child
llm_child = root.create_child(
    name="llm_call",
    run_type="llm",
    inputs={"prompt": f"Context: {docs}\n\nQuestion: What is AI?"}
)

response = llm.invoke(...)
llm_child.end(outputs={"response": response})

# End root
root.end(outputs={"answer": response})
```

### Distributed Tracing

```python
from langsmith import get_current_run_tree
from langsmith.run_helpers import get_tracing_context

# Get current trace context
context = get_tracing_context()
run_tree = get_current_run_tree()

# Pass to another service
trace_headers = {
    "langsmith-trace": run_tree.trace_id,
    "langsmith-parent": run_tree.id
}

# In receiving service
from langsmith import RunTree

child_run = RunTree(
    name="remote_operation",
    run_type="tool",
    parent_run_id=headers["langsmith-parent"],
    trace_id=headers["langsmith-trace"]
)
```

### Attachments

```python
from langsmith import Client

client = Client()

# Attach files to examples
client.create_example(
    inputs={"query": "Describe this image"},
    outputs={"description": "A sunset over mountains"},
    attachments={
        "image": ("image/jpeg", image_bytes)
    },
    dataset_id=dataset.id
)

# Attach to runs
from langsmith import traceable

@traceable(dangerously_allow_filesystem=True)
def process_file(file_path: str):
    with open(file_path, "rb") as f:
        return {"result": analyze(f.read())}
```

## Hub Prompts

### Pull and Use Prompts

```python
from langsmith import Client

client = Client()

# Pull prompt from hub
prompt = client.pull_prompt("langchain-ai/rag-prompt")

# Use prompt
response = prompt.invoke({
    "context": "Python is a programming language...",
    "question": "What is Python?"
})
```

### Push Prompts

```python
from langchain_core.prompts import ChatPromptTemplate

# Create prompt
prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful {role}."),
    ("user", "{question}")
])

# Push to hub
client.push_prompt("my-org/my-prompt", object=prompt)

# Push with tags
client.push_prompt(
    "my-org/my-prompt",
    object=prompt,
    tags=["production", "v2"]
)
```

### Versioned Prompts

```python
# Pull specific version
prompt_v1 = client.pull_prompt("my-org/my-prompt", commit_hash="abc123")

# Pull latest
prompt_latest = client.pull_prompt("my-org/my-prompt")

# Compare versions
print(f"V1 template: {prompt_v1}")
print(f"Latest template: {prompt_latest}")
```

## Dataset Management

### Create from Runs

```python
from langsmith import Client

client = Client()

# Create dataset from existing runs
runs = client.list_runs(
    project_name="production",
    filter='and(eq(feedback_key, "user_rating"), gt(feedback_score, 0.8))'
)

# Convert to examples
examples = []
for run in runs:
    examples.append({
        "inputs": run.inputs,
        "outputs": run.outputs
    })

# Create dataset
dataset = client.create_dataset("high-quality-examples")
client.create_examples(
    inputs=[e["inputs"] for e in examples],
    outputs=[e["outputs"] for e in examples],
    dataset_id=dataset.id
)
```

### Dataset Splits

```python
from langsmith import Client
import random

client = Client()

# Get all examples
examples = list(client.list_examples(dataset_name="my-dataset"))
random.shuffle(examples)

# Split
train_size = int(0.8 * len(examples))
train_examples = examples[:train_size]
test_examples = examples[train_size:]

# Create split datasets
train_dataset = client.create_dataset("my-dataset-train")
test_dataset = client.create_dataset("my-dataset-test")

for ex in train_examples:
    client.create_example(inputs=ex.inputs, outputs=ex.outputs, dataset_id=train_dataset.id)

for ex in test_examples:
    client.create_example(inputs=ex.inputs, outputs=ex.outputs, dataset_id=test_dataset.id)
```

### Upload from CSV

```python
from langsmith import Client

client = Client()

# Upload CSV directly
dataset = client.upload_csv(
    csv_file="./qa_data.csv",
    input_keys=["question"],
    output_keys=["answer"],
    name="qa-dataset",
    description="QA pairs from CSV"
)
```

## Filtering and Querying

### Run Filters

```python
from langsmith import Client

client = Client()

# Complex filters
runs = client.list_runs(
    project_name="production",
    filter='and(eq(status, "success"), gt(latency, 2.0))',
    execution_order=1,  # Only root runs
    start_time="2024-01-01T00:00:00Z",
    end_time="2024-12-31T23:59:59Z"
)

# Filter by tags
runs = client.list_runs(
    project_name="production",
    filter='has(tags, "production")'
)

# Filter by error
runs = client.list_runs(
    project_name="production",
    filter='eq(status, "error")'
)
```

### Feedback Queries

```python
# Get runs with specific feedback
runs = client.list_runs(
    project_name="production",
    filter='and(eq(feedback_key, "user_rating"), lt(feedback_score, 0.5))'
)

# Aggregate feedback
from collections import defaultdict

feedback_by_key = defaultdict(list)
for feedback in client.list_feedback(project_name="production"):
    feedback_by_key[feedback.key].append(feedback.score)

for key, scores in feedback_by_key.items():
    print(f"{key}: avg={sum(scores)/len(scores):.2f}, count={len(scores)}")
```

## OpenTelemetry Integration

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from langsmith import Client

# Set up OTel
provider = TracerProvider()
trace.set_tracer_provider(provider)

# Create client with OTel integration
client = Client(otel_tracer_provider=provider)

# Traces will be exported to both LangSmith and OTel backends
```

## Multi-Tenant Setup

```python
from langsmith import Client

# Configure multiple endpoints
api_urls = {
    "https://api-team1.langsmith.com": "api_key_1",
    "https://api-team2.langsmith.com": "api_key_2"
}

# Client writes to all endpoints
client = Client(api_urls=api_urls)

# All operations replicated
client.create_run(
    name="shared_operation",
    run_type="chain",
    inputs={"query": "test"}
)
```

## Batch Operations

```python
from langsmith import Client

client = Client()

# Batch create examples
inputs = [{"q": f"Question {i}"} for i in range(1000)]
outputs = [{"a": f"Answer {i}"} for i in range(1000)]

client.create_examples(
    inputs=inputs,
    outputs=outputs,
    dataset_id=dataset.id
)

# Batch update examples
example_ids = [ex.id for ex in client.list_examples(dataset_id=dataset.id)]
client.update_examples(
    example_ids=example_ids,
    metadata=[{"updated": True} for _ in example_ids]
)

# Batch delete
client.delete_examples(example_ids=example_ids[:100])
```

## Caching and Performance

```python
from langsmith import Client
from functools import lru_cache

client = Client()

# Cache dataset lookups
@lru_cache(maxsize=100)
def get_dataset_id(name: str) -> str:
    dataset = client.read_dataset(dataset_name=name)
    return str(dataset.id)

# Batch tracing for high throughput
client = Client(auto_batch_tracing=True)

# Control batch size
import os
os.environ["LANGSMITH_BATCH_SIZE"] = "100"
os.environ["LANGSMITH_BATCH_INTERVAL_MS"] = "1000"
```




### Troubleshooting

# LangSmith Troubleshooting Guide

## Installation Issues

### Package Not Found

**Error**: `ModuleNotFoundError: No module named 'langsmith'`

**Fix**:
```bash
pip install langsmith

# Verify installation
python -c "import langsmith; print(langsmith.__version__)"
```

### Version Conflicts

**Error**: `ImportError: cannot import name 'traceable' from 'langsmith'`

**Fix**:
```bash
# Upgrade to latest version
pip install -U langsmith

# Check for conflicts
pip check

# If conflicts exist, create clean environment
python -m venv venv
source venv/bin/activate
pip install langsmith
```

## Authentication Issues

### API Key Not Found

**Error**: `LangSmithAuthError: Authentication failed`

**Solutions**:

1. **Set environment variable**:
```bash
export LANGSMITH_API_KEY="your-api-key"

# Or in .env file
LANGSMITH_API_KEY=your-api-key
```

2. **Pass directly to client**:
```python
from langsmith import Client

client = Client(api_key="your-api-key")
```

3. **Verify key is set**:
```python
import os
print(os.environ.get("LANGSMITH_API_KEY", "NOT SET"))
```

### Invalid API Key

**Error**: `LangSmithAuthError: 401 Unauthorized`

**Fix**:
```bash
# Verify key at https://smith.langchain.com/settings

# Test connection
python -c "from langsmith import Client; c = Client(); print(list(c.list_projects()))"
```

### Wrong Endpoint

**Error**: `LangSmithConnectionError: Connection refused`

**Fix**:
```python
import os

# Default endpoint
os.environ["LANGSMITH_ENDPOINT"] = "https://api.smith.langchain.com"

# Or for self-hosted
os.environ["LANGSMITH_ENDPOINT"] = "https://your-langsmith-instance.com"
```

## Tracing Issues

### Traces Not Appearing

**Problem**: Traced functions don't appear in LangSmith.

**Solutions**:

1. **Enable tracing**:
```python
import os
os.environ["LANGSMITH_TRACING"] = "true"

# Verify
print(os.environ.get("LANGSMITH_TRACING"))
```

2. **Check project name**:
```python
import os
os.environ["LANGSMITH_PROJECT"] = "my-project"

# Or in decorator
from langsmith import traceable

@traceable(project_name="my-project")
def my_function():
    pass
```

3. **Flush pending traces**:
```python
from langsmith import Client

client = Client()
client.flush()  # Wait for all pending traces to be sent
```

4. **Verify connection**:
```python
from langsmith import Client

client = Client()
try:
    projects = list(client.list_projects())
    print(f"Connected! Found {len(projects)} projects")
except Exception as e:
    print(f"Connection failed: {e}")
```

### Missing Child Runs

**Problem**: Nested function calls don't appear as child runs.

**Fix**:
```python
from langsmith import traceable

# All nested functions must be decorated
@traceable
def parent_function():
    child_function()  # This will be a child run

@traceable
def child_function():
    pass

# Or use tracing context
from langsmith import trace

with trace("parent", run_type="chain") as parent:
    with trace("child", run_type="tool") as child:
        # Child automatically nested under parent
        pass
```

### Async Tracing Issues

**Problem**: Async functions not traced correctly.

**Fix**:
```python
from langsmith import traceable
import asyncio

# Decorator works with async functions
@traceable
async def async_function():
    await asyncio.sleep(1)
    return "done"

# For async context
from langsmith import AsyncClient

async def main():
    client = AsyncClient()
    async for run in client.list_runs(project_name="my-project"):
        print(run.name)

asyncio.run(main())
```

## Evaluation Issues

### Dataset Not Found

**Error**: `LangSmithNotFoundError: Dataset 'xyz' not found`

**Fix**:
```python
from langsmith import Client

client = Client()

# List available datasets
for dataset in client.list_datasets():
    print(f"Dataset: {dataset.name}, ID: {dataset.id}")

# Use correct name or ID
results = evaluate(
    my_model,
    data="correct-dataset-name",  # Or use dataset ID
    evaluators=[my_evaluator]
)
```

### Evaluator Errors

**Problem**: Custom evaluator fails silently.

**Fix**:
```python
def safe_evaluator(run, example):
    try:
        prediction = run.outputs.get("answer", "")
        reference = example.outputs.get("answer", "")

        if not prediction or not reference:
            return {"key": "accuracy", "score": 0.0, "comment": "Missing data"}

        score = compute_score(prediction, reference)
        return {"key": "accuracy", "score": score}

    except Exception as e:
        # Return error as comment instead of crashing
        return {
            "key": "accuracy",
            "score": 0.0,
            "comment": f"Evaluator error: {str(e)}"
        }
```

### Evaluation Timeout

**Problem**: Evaluation hangs or times out.

**Fix**:
```python
from langsmith import evaluate
import asyncio

# Use async evaluation with timeout
async def run_with_timeout():
    try:
        results = await asyncio.wait_for(
            aevaluate(my_model, data="test-set", evaluators=[my_evaluator]),
            timeout=300  # 5 minutes
        )
        return results
    except asyncio.TimeoutError:
        print("Evaluation timed out")
        return None

# Or reduce concurrency
results = evaluate(
    my_model,
    data="test-set",
    evaluators=[my_evaluator],
    max_concurrency=5  # Reduce from default
)
```

## Performance Issues

### High Latency from Tracing

**Problem**: Tracing adds significant latency.

**Solutions**:

1. **Enable background batching** (default):
```python
from langsmith import Client

client = Client(auto_batch_tracing=True)
```

2. **Use sampling**:
```python
import os
os.environ["LANGSMITH_TRACING_SAMPLING_RATE"] = "0.1"  # 10% of traces
```

3. **Reduce payload size**:
```python
from langsmith import traceable

def truncate_inputs(inputs):
    return {k: str(v)[:1000] for k, v in inputs.items()}

@traceable(process_inputs=truncate_inputs)
def my_function(large_input):
    pass
```

### Memory Issues

**Problem**: High memory usage during evaluation.

**Fix**:
```python
from langsmith import evaluate

# Process in smaller batches
def evaluate_in_batches(model, dataset_name, batch_size=100):
    from langsmith import Client
    client = Client()

    examples = list(client.list_examples(dataset_name=dataset_name))

    all_results = []
    for i in range(0, len(examples), batch_size):
        batch = examples[i:i + batch_size]
        results = evaluate(
            model,
            data=batch,
            evaluators=[my_evaluator]
        )
        all_results.extend(results)

        # Clear memory
        import gc
        gc.collect()

    return all_results
```

### Rate Limiting

**Error**: `LangSmithRateLimitError: 429 Too Many Requests`

**Fix**:
```python
import time
from langsmith import Client

client = Client()

def retry_with_backoff(func, max_retries=5):
    for attempt in range(max_retries):
        try:
            return func()
        except Exception as e:
            if "429" in str(e):
                wait_time = 2 ** attempt
                print(f"Rate limited, waiting {wait_time}s...")
                time.sleep(wait_time)
            else:
                raise
    raise Exception("Max retries exceeded")

# Use with operations
retry_with_backoff(lambda: client.create_run(...))
```

## Data Issues

### Large Payload Errors

**Error**: `PayloadTooLarge: Request payload exceeds maximum size`

**Fix**:
```python
from langsmith import traceable

def limit_size(data, max_chars=10000):
    if isinstance(data, str):
        return data[:max_chars]
    elif isinstance(data, dict):
        return {k: limit_size(v, max_chars // len(data)) for k, v in data.items()}
    elif isinstance(data, list):
        return [limit_size(item, max_chars // len(data)) for item in data[:100]]
    return data

@traceable(
    process_inputs=limit_size,
    process_outputs=limit_size
)
def process_large_data(data):
    return large_result
```

### Serialization Errors

**Error**: `TypeError: Object of type X is not JSON serializable`

**Fix**:
```python
import json
from datetime import datetime
import numpy as np

def serialize_value(obj):
    if isinstance(obj, datetime):
        return obj.isoformat()
    elif isinstance(obj, np.ndarray):
        return obj.tolist()
    elif hasattr(obj, "__dict__"):
        return obj.__dict__
    return str(obj)

def safe_serialize(data):
    return json.loads(json.dumps(data, default=serialize_value))

@traceable(
    process_inputs=safe_serialize,
    process_outputs=safe_serialize
)
def my_function(complex_input):
    return complex_output
```

## Network Issues

### Connection Timeout

**Error**: `LangSmithRequestTimeout: Connection timed out`

**Fix**:
```python
from langsmith import Client

# Increase timeout
client = Client(timeout_ms=60000)  # 60 seconds

# Or set via environment
import os
os.environ["LANGSMITH_TIMEOUT_MS"] = "60000"
```

### SSL Certificate Errors

**Error**: `SSLCertVerificationError`

**Fix**:
```python
# For self-signed certificates (not recommended for production)
import os
os.environ["LANGSMITH_VERIFY_SSL"] = "false"

# Better: Add certificate to trusted store
# Or use proper CA-signed certificates
```

### Proxy Configuration

**Problem**: Behind corporate proxy.

**Fix**:
```python
import os

# Set proxy environment variables
os.environ["HTTP_PROXY"] = "http://proxy.company.com:8080"
os.environ["HTTPS_PROXY"] = "http://proxy.company.com:8080"

# Then use client normally
from langsmith import Client
client = Client()
```

## Debugging Tips

### Enable Debug Logging

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logging.getLogger("langsmith").setLevel(logging.DEBUG)
```

### Verify Configuration

```python
from langsmith import Client
import os

print("Configuration:")
print(f"  API Key: {'SET' if os.environ.get('LANGSMITH_API_KEY') else 'NOT SET'}")
print(f"  Endpoint: {os.environ.get('LANGSMITH_ENDPOINT', 'default')}")
print(f"  Project: {os.environ.get('LANGSMITH_PROJECT', 'default')}")
print(f"  Tracing: {os.environ.get('LANGSMITH_TRACING', 'not set')}")

# Test connection
client = Client()
try:
    info = client.info
    print(f"  Connected: Yes")
    print(f"  Version: {info}")
except Exception as e:
    print(f"  Connected: No ({e})")
```

### Test Simple Trace

```python
from langsmith import traceable
import os

os.environ["LANGSMITH_TRACING"] = "true"

@traceable
def test_trace():
    return "Hello, LangSmith!"

# Run and check LangSmith UI
result = test_trace()
print(f"Result: {result}")
print("Check LangSmith UI for trace")
```

## Getting Help

1. **Documentation**: https://docs.smith.langchain.com
2. **GitHub Issues**: https://github.com/langchain-ai/langsmith-sdk/issues
3. **Discord**: https://discord.gg/langchain
4. **Stack Overflow**: Tag `langsmith`

### Reporting Issues

Include:
- LangSmith SDK version: `pip show langsmith`
- Python version: `python --version`
- Full error traceback
- Minimal reproducible code
- Environment (local, cloud, etc.)




---

## 🚀 Usage

**Reference this template:** `@skill-langsmith-observability.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
