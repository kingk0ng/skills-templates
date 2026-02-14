---
id: skill-phoenix-observability
type: skill
name: phoenix-observability
description: Open-source AI observability platform for LLM tracing, evaluation, and
  monitoring. Use when debugging LLM applications with detailed traces, running evaluations
  on datasets, or monitoring production AI systems with real-time insights.
category: ai-research
complexity: medium
keywords:
- api
- ci/cd
- database
- deployment
- docker
- github
- grpc
- performance
- python
- qa
- rest
- test
- testing
capabilities: []
token_estimate: 1458
has_references: true
reference_count: 2
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,458 -->
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




# phoenix-observability

> Open-source AI observability platform for LLM tracing, evaluation, and monitoring. Use when debugging LLM applications with detailed traces, running evaluations on datasets, or monitoring production AI systems with real-time insights.

# Phoenix - AI Observability Platform

Open-source AI observability and evaluation platform for LLM applications with tracing, evaluation, datasets, experiments, and real-time monitoring.

## When to use Phoenix

**Use Phoenix when:**
- Debugging LLM application issues with detailed traces
- Running systematic evaluations on datasets
- Monitoring production LLM systems in real-time
- Building experiment pipelines for prompt/model comparison
- Self-hosted observability without vendor lock-in

**Key features:**
- **Tracing**: OpenTelemetry-based trace collection for any LLM framework
- **Evaluation**: LLM-as-judge evaluators for quality assessment
- **Datasets**: Versioned test sets for regression testing
- **Experiments**: Compare prompts, models, and configurations
- **Playground**: Interactive prompt testing with multiple models
- **Open-source**: Self-hosted with PostgreSQL or SQLite

**Use alternatives instead:**
- **LangSmith**: Managed platform with LangChain-first integration
- **Weights & Biases**: Deep learning experiment tracking focus
- **Arize Cloud**: Managed Phoenix with enterprise features
- **MLflow**: General ML lifecycle, model registry focus

## Quick start

### Installation

```bash
pip install arize-phoenix

# With specific backends
pip install arize-phoenix[embeddings]  # Embedding analysis
pip install arize-phoenix-otel         # OpenTelemetry config
pip install arize-phoenix-evals        # Evaluation framework
pip install arize-phoenix-client       # Lightweight REST client
```

### Launch Phoenix server

```python
import phoenix as px

# Launch in notebook (ThreadServer mode)
session = px.launch_app()

# View UI
session.view()  # Embedded iframe
print(session.url)  # http://localhost:6006
```

### Command-line server (production)

```bash
# Start Phoenix server
phoenix serve

# With PostgreSQL
export PHOENIX_SQL_DATABASE_URL="postgresql://user:pass@host/db"
phoenix serve --port 6006
```

### Basic tracing

```python
from phoenix.otel import register
from openinference.instrumentation.openai import OpenAIInstrumentor

# Configure OpenTelemetry with Phoenix
tracer_provider = register(
    project_name="my-llm-app",
    endpoint="http://localhost:6006/v1/traces"
)

# Instrument OpenAI SDK
OpenAIInstrumentor().instrument(tracer_provider=tracer_provider)

# All OpenAI calls are now traced
from openai import OpenAI
client = OpenAI()
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Hello!"}]
)
```

## Core concepts

### Traces and spans

A **trace** represents a complete execution flow, while **spans** are individual operations within that trace.

```python
from phoenix.otel import register
from opentelemetry import trace

# Setup tracing
tracer_provider = register(project_name="my-app")
tracer = trace.get_tracer(__name__)

# Create custom spans
with tracer.start_as_current_span("process_query") as span:
    span.set_attribute("input.value", query)

    # Child spans are automatically nested
    with tracer.start_as_current_span("retrieve_context"):
        context = retriever.search(query)

    with tracer.start_as_current_span("generate_response"):
        response = llm.generate(query, context)

    span.set_attribute("output.value", response)
```

### Projects

Projects organize related traces:

```python
import os
os.environ["PHOENIX_PROJECT_NAME"] = "production-chatbot"

# Or per-trace
from phoenix.otel import register
tracer_provider = register(project_name="experiment-v2")
```

## Framework instrumentation

### OpenAI

```python
from phoenix.otel import register
from openinference.instrumentation.openai import OpenAIInstrumentor

tracer_provider = register()
OpenAIInstrumentor().instrument(tracer_provider=tracer_provider)
```

### LangChain

```python
from phoenix.otel import register
from openinference.instrumentation.langchain import LangChainInstrumentor

tracer_provider = register()
LangChainInstrumentor().instrument(tracer_provider=tracer_provider)

# All LangChain operations traced
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o")
response = llm.invoke("Hello!")
```

### LlamaIndex

```python
from phoenix.otel import register
from openinference.instrumentation.llama_index import LlamaIndexInstrumentor

tracer_provider = register()
LlamaIndexInstrumentor().instrument(tracer_provider=tracer_provider)
```

### Anthropic

```python
from phoenix.otel import register
from openinference.instrumentation.anthropic import AnthropicInstrumentor

tracer_provider = register()
AnthropicInstrumentor().instrument(tracer_provider=tracer_provider)
```

## Evaluation framework

### Built-in evaluators

```python
from phoenix.evals import (
    OpenAIModel,
    HallucinationEvaluator,
    RelevanceEvaluator,
    ToxicityEvaluator,
    llm_classify
)

# Setup model for evaluation
eval_model = OpenAIModel(model="gpt-4o")

# Evaluate hallucination
hallucination_eval = HallucinationEvaluator(eval_model)
results = hallucination_eval.evaluate(
    input="What is the capital of France?",
    output="The capital of France is Paris.",
    reference="Paris is the capital of France."
)
```

### Custom evaluators

```python
from phoenix.evals import llm_classify

# Define custom evaluation
def evaluate_helpfulness(input_text, output_text):
    template = """
    Evaluate if the response is helpful for the given question.

    Question: {input}
    Response: {output}

    Is this response helpful? Answer 'helpful' or 'not_helpful'.
    """

    result = llm_classify(
        model=eval_model,
        template=template,
        input=input_text,
        output=output_text,
        rails=["helpful", "not_helpful"]
    )
    return result
```

### Run evaluations on dataset

```python
from phoenix import Client
from phoenix.evals import run_evals

client = Client()

# Get spans to evaluate
spans_df = client.get_spans_dataframe(
    project_name="my-app",
    filter_condition="span_kind == 'LLM'"
)

# Run evaluations
eval_results = run_evals(
    dataframe=spans_df,
    evaluators=[
        HallucinationEvaluator(eval_model),
        RelevanceEvaluator(eval_model)
    ],
    provide_explanation=True
)

# Log results back to Phoenix
client.log_evaluations(eval_results)
```

## Datasets and experiments

### Create dataset

```python
from phoenix import Client

client = Client()

# Create dataset
dataset = client.create_dataset(
    name="qa-test-set",
    description="QA evaluation dataset"
)

# Add examples
client.add_examples_to_dataset(
    dataset_name="qa-test-set",
    examples=[
        {
            "input": {"question": "What is Python?"},
            "output": {"answer": "A programming language"}
        },
        {
            "input": {"question": "What is ML?"},
            "output": {"answer": "Machine learning"}
        }
    ]
)
```

### Run experiment

```python
from phoenix import Client
from phoenix.experiments import run_experiment

client = Client()

def my_model(input_data):
    """Your model function."""
    question = input_data["question"]
    return {"answer": generate_answer(question)}

def accuracy_evaluator(input_data, output, expected):
    """Custom evaluator."""
    return {
        "score": 1.0 if expected["answer"].lower() in output["answer"].lower() else 0.0,
        "label": "correct" if expected["answer"].lower() in output["answer"].lower() else "incorrect"
    }

# Run experiment
results = run_experiment(
    dataset_name="qa-test-set",
    task=my_model,
    evaluators=[accuracy_evaluator],
    experiment_name="baseline-v1"
)

print(f"Average accuracy: {results.aggregate_metrics['accuracy']}")
```

## Client API

### Query traces and spans

```python
from phoenix import Client

client = Client(endpoint="http://localhost:6006")

# Get spans as DataFrame
spans_df = client.get_spans_dataframe(
    project_name="my-app",
    filter_condition="span_kind == 'LLM'",
    limit=1000
)

# Get specific span
span = client.get_span(span_id="abc123")

# Get trace
trace = client.get_trace(trace_id="xyz789")
```

### Log feedback

```python
from phoenix import Client

client = Client()

# Log user feedback
client.log_annotation(
    span_id="abc123",
    name="user_rating",
    annotator_kind="HUMAN",
    score=0.8,
    label="helpful",
    metadata={"comment": "Good response"}
)
```

### Export data

```python
# Export to pandas
df = client.get_spans_dataframe(project_name="my-app")

# Export traces
traces = client.list_traces(project_name="my-app")
```

## Production deployment

### Docker

```bash
docker run -p 6006:6006 arizephoenix/phoenix:latest
```

### With PostgreSQL

```bash
# Set database URL
export PHOENIX_SQL_DATABASE_URL="postgresql://user:pass@host:5432/phoenix"

# Start server
phoenix serve --host 0.0.0.0 --port 6006
```

### Environment variables

| Variable | Description | Default |
|----------|-------------|---------|
| `PHOENIX_PORT` | HTTP server port | `6006` |
| `PHOENIX_HOST` | Server bind address | `127.0.0.1` |
| `PHOENIX_GRPC_PORT` | gRPC/OTLP port | `4317` |
| `PHOENIX_SQL_DATABASE_URL` | Database connection | SQLite temp |
| `PHOENIX_WORKING_DIR` | Data storage directory | OS temp |
| `PHOENIX_ENABLE_AUTH` | Enable authentication | `false` |
| `PHOENIX_SECRET` | JWT signing secret | Required if auth enabled |

### With authentication

```bash
export PHOENIX_ENABLE_AUTH=true
export PHOENIX_SECRET="your-secret-key-min-32-chars"
export PHOENIX_ADMIN_SECRET="admin-bootstrap-token"

phoenix serve
```

## Best practices

1. **Use projects**: Separate traces by environment (dev/staging/prod)
2. **Add metadata**: Include user IDs, session IDs for debugging
3. **Evaluate regularly**: Run automated evaluations in CI/CD
4. **Version datasets**: Track test set changes over time
5. **Monitor costs**: Track token usage via Phoenix dashboards
6. **Self-host**: Use PostgreSQL for production deployments

## Common issues

**Traces not appearing:**
```python
from phoenix.otel import register

# Verify endpoint
tracer_provider = register(
    project_name="my-app",
    endpoint="http://localhost:6006/v1/traces"  # Correct endpoint
)

# Force flush
from opentelemetry import trace
trace.get_tracer_provider().force_flush()
```

**High memory in notebook:**
```python
# Close session when done
session = px.launch_app()
# ... do work ...
session.close()
px.close_app()
```

**Database connection issues:**
```bash
# Verify PostgreSQL connection
psql $PHOENIX_SQL_DATABASE_URL -c "SELECT 1"

# Check Phoenix logs
phoenix serve --log-level debug
```

## References

- **[Advanced Usage](references/advanced-usage.md)** - Custom evaluators, experiments, production setup
- **[Troubleshooting](references/troubleshooting.md)** - Common issues, debugging, performance

## Resources

- **Documentation**: https://docs.arize.com/phoenix
- **Repository**: https://github.com/Arize-ai/phoenix
- **Docker Hub**: https://hub.docker.com/r/arizephoenix/phoenix
- **Version**: 12.0.0+
- **License**: Apache 2.0


---


## 📚 Reference Materials


### Advanced Usage

# Phoenix Advanced Usage Guide

## Custom Evaluators

### Template-Based Evaluators

```python
from phoenix.evals import OpenAIModel, llm_classify

eval_model = OpenAIModel(model="gpt-4o")

# Custom template for specific evaluation
CUSTOM_EVAL_TEMPLATE = """
You are evaluating an AI assistant's response.

User Query: {input}
AI Response: {output}
Reference Answer: {reference}

Evaluate the response on these criteria:
1. Accuracy: Is the information correct?
2. Completeness: Does it fully answer the question?
3. Clarity: Is it easy to understand?

Provide a score from 1-5 and explain your reasoning.
Format: SCORE: [1-5]\nREASONING: [explanation]
"""

def custom_evaluator(input_text, output_text, reference_text):
    result = llm_classify(
        model=eval_model,
        template=CUSTOM_EVAL_TEMPLATE,
        input=input_text,
        output=output_text,
        reference=reference_text,
        rails=["1", "2", "3", "4", "5"]
    )
    return {
        "score": float(result.label) / 5.0,
        "label": result.label,
        "explanation": result.explanation
    }
```

### Multi-Criteria Evaluator

```python
from phoenix.evals import OpenAIModel, llm_classify
from dataclasses import dataclass
from typing import List

@dataclass
class EvaluationResult:
    criteria: str
    score: float
    label: str
    explanation: str

def multi_criteria_evaluator(input_text, output_text, criteria: List[str]):
    """Evaluate output against multiple criteria."""
    results = []

    for criterion in criteria:
        template = f"""
        Evaluate the following response for {criterion}.

        Input: {{input}}
        Output: {{output}}

        Is this response good in terms of {criterion}?
        Answer 'good', 'acceptable', or 'poor'.
        """

        result = llm_classify(
            model=eval_model,
            template=template,
            input=input_text,
            output=output_text,
            rails=["good", "acceptable", "poor"]
        )

        score_map = {"good": 1.0, "acceptable": 0.5, "poor": 0.0}
        results.append(EvaluationResult(
            criteria=criterion,
            score=score_map.get(result.label, 0.5),
            label=result.label,
            explanation=result.explanation
        ))

    return results

# Usage
results = multi_criteria_evaluator(
    input_text="What is Python?",
    output_text="Python is a programming language...",
    criteria=["accuracy", "completeness", "helpfulness"]
)
```

### Batch Evaluation with Concurrency

```python
from phoenix.evals import run_evals, OpenAIModel
from phoenix import Client
import asyncio

client = Client()
eval_model = OpenAIModel(model="gpt-4o")

# Get spans to evaluate
spans_df = client.get_spans_dataframe(
    project_name="production",
    filter_condition="span_kind == 'LLM'",
    limit=1000
)

# Run evaluations with concurrency control
eval_results = run_evals(
    dataframe=spans_df,
    evaluators=[
        HallucinationEvaluator(eval_model),
        RelevanceEvaluator(eval_model),
        ToxicityEvaluator(eval_model)
    ],
    provide_explanation=True,
    concurrency=10  # Control parallel evaluations
)

# Log results back to Phoenix
client.log_evaluations(eval_results)
```

## Advanced Experiments

### A/B Testing Prompts

```python
from phoenix import Client
from phoenix.experiments import run_experiment

client = Client()

# Define prompt variants
PROMPT_A = """
Answer the following question concisely:
{question}
"""

PROMPT_B = """
You are a helpful assistant. Please provide a detailed answer to:
{question}

Include relevant examples if applicable.
"""

def create_model_with_prompt(prompt_template):
    def model_fn(input_data):
        from openai import OpenAI
        client = OpenAI()

        response = client.chat.completions.create(
            model="gpt-4o",
            messages=[{
                "role": "user",
                "content": prompt_template.format(**input_data)
            }]
        )
        return {"answer": response.choices[0].message.content}
    return model_fn

# Run experiments for each variant
results_a = run_experiment(
    dataset_name="qa-test-set",
    task=create_model_with_prompt(PROMPT_A),
    evaluators=[accuracy_evaluator, helpfulness_evaluator],
    experiment_name="prompt-variant-a"
)

results_b = run_experiment(
    dataset_name="qa-test-set",
    task=create_model_with_prompt(PROMPT_B),
    evaluators=[accuracy_evaluator, helpfulness_evaluator],
    experiment_name="prompt-variant-b"
)

# Compare results
print(f"Variant A accuracy: {results_a.aggregate_metrics['accuracy']}")
print(f"Variant B accuracy: {results_b.aggregate_metrics['accuracy']}")
```

### Model Comparison Experiment

```python
from phoenix.experiments import run_experiment

MODELS = ["gpt-4o", "gpt-4o-mini", "claude-3-sonnet"]

def create_model_fn(model_name):
    def model_fn(input_data):
        if "gpt" in model_name:
            from openai import OpenAI
            client = OpenAI()
            response = client.chat.completions.create(
                model=model_name,
                messages=[{"role": "user", "content": input_data["question"]}]
            )
            return {"answer": response.choices[0].message.content}
        elif "claude" in model_name:
            from anthropic import Anthropic
            client = Anthropic()
            response = client.messages.create(
                model=model_name,
                max_tokens=1024,
                messages=[{"role": "user", "content": input_data["question"]}]
            )
            return {"answer": response.content[0].text}
    return model_fn

# Run experiments for each model
all_results = {}
for model in MODELS:
    results = run_experiment(
        dataset_name="qa-test-set",
        task=create_model_fn(model),
        evaluators=[quality_evaluator, latency_evaluator],
        experiment_name=f"model-comparison-{model}"
    )
    all_results[model] = results

# Summary comparison
for model, results in all_results.items():
    print(f"{model}: quality={results.aggregate_metrics['quality']:.2f}")
```

## Production Deployment

### Kubernetes Deployment

```yaml
# phoenix-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: phoenix
spec:
  replicas: 1
  selector:
    matchLabels:
      app: phoenix
  template:
    metadata:
      labels:
        app: phoenix
    spec:
      containers:
      - name: phoenix
        image: arizephoenix/phoenix:latest
        ports:
        - containerPort: 6006
        - containerPort: 4317
        env:
        - name: PHOENIX_SQL_DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: phoenix-secrets
              key: database-url
        - name: PHOENIX_ENABLE_AUTH
          value: "true"
        - name: PHOENIX_SECRET
          valueFrom:
            secretKeyRef:
              name: phoenix-secrets
              key: jwt-secret
        resources:
          requests:
            memory: "1Gi"
            cpu: "500m"
          limits:
            memory: "4Gi"
            cpu: "2000m"
        livenessProbe:
          httpGet:
            path: /healthz
            port: 6006
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /readyz
            port: 6006
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: phoenix
spec:
  selector:
    app: phoenix
  ports:
  - name: http
    port: 6006
    targetPort: 6006
  - name: grpc
    port: 4317
    targetPort: 4317
```

### Docker Compose Setup

```yaml
# docker-compose.yml
version: '3.8'

services:
  phoenix:
    image: arizephoenix/phoenix:latest
    ports:
      - "6006:6006"
      - "4317:4317"
    environment:
      - PHOENIX_SQL_DATABASE_URL=postgresql://phoenix:phoenix@postgres:5432/phoenix
      - PHOENIX_ENABLE_AUTH=true
      - PHOENIX_SECRET=${PHOENIX_SECRET}
      - PHOENIX_HOST=0.0.0.0
    depends_on:
      postgres:
        condition: service_healthy
    restart: unless-stopped

  postgres:
    image: postgres:15
    environment:
      - POSTGRES_USER=phoenix
      - POSTGRES_PASSWORD=phoenix
      - POSTGRES_DB=phoenix
    volumes:
      - phoenix_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U phoenix"]
      interval: 5s
      timeout: 5s
      retries: 5

volumes:
  phoenix_data:
```

### High Availability Setup

```yaml
# phoenix-ha.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: phoenix
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  selector:
    matchLabels:
      app: phoenix
  template:
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - phoenix
              topologyKey: kubernetes.io/hostname
      containers:
      - name: phoenix
        image: arizephoenix/phoenix:latest
        env:
        - name: PHOENIX_SQL_DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: phoenix-secrets
              key: database-url
```

## Advanced Tracing

### Custom Span Attributes

```python
from opentelemetry import trace
from phoenix.otel import register

tracer_provider = register(project_name="my-app")
tracer = trace.get_tracer(__name__)

def process_request(user_id: str, query: str):
    with tracer.start_as_current_span("process_request") as span:
        # Add custom attributes
        span.set_attribute("user.id", user_id)
        span.set_attribute("input.value", query)
        span.set_attribute("custom.priority", "high")

        # Process and add output
        result = do_processing(query)
        span.set_attribute("output.value", result)
        span.set_attribute("output.tokens", count_tokens(result))

        return result
```

### Distributed Tracing

```python
from opentelemetry import trace
from opentelemetry.propagate import inject, extract

# Service A: Inject trace context
def call_service_b(request_data):
    headers = {}
    inject(headers)  # Inject trace context into headers

    response = requests.post(
        "http://service-b/process",
        json=request_data,
        headers=headers
    )
    return response.json()

# Service B: Extract trace context
from flask import Flask, request

app = Flask(__name__)

@app.route("/process", methods=["POST"])
def process():
    # Extract trace context from incoming request
    context = extract(request.headers)

    with tracer.start_as_current_span("service_b_process", context=context):
        # Continue the trace
        result = process_data(request.json)
        return {"result": result}
```

### Session Tracking

```python
from phoenix.otel import register
from opentelemetry import trace

tracer_provider = register(project_name="chatbot")
tracer = trace.get_tracer(__name__)

def handle_conversation(session_id: str, user_message: str):
    with tracer.start_as_current_span("conversation_turn") as span:
        # Add session context
        span.set_attribute("session.id", session_id)
        span.set_attribute("input.value", user_message)

        # Get conversation history
        history = get_session_history(session_id)
        span.set_attribute("conversation.turn_count", len(history))

        # Generate response
        response = generate_response(history + [user_message])
        span.set_attribute("output.value", response)

        # Save to history
        save_to_history(session_id, user_message, response)

        return response
```

## Data Management

### Export and Backup

```python
from phoenix import Client
import pandas as pd
from datetime import datetime, timedelta

client = Client()

def export_project_data(project_name: str, days: int = 30):
    """Export project data for backup."""
    # Get spans
    spans_df = client.get_spans_dataframe(
        project_name=project_name,
        start_time=datetime.now() - timedelta(days=days)
    )

    # Get evaluations
    evals_df = client.get_evaluations(project_name=project_name)

    # Save to files
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    spans_df.to_parquet(f"backup/{project_name}_spans_{timestamp}.parquet")
    evals_df.to_parquet(f"backup/{project_name}_evals_{timestamp}.parquet")

    return spans_df, evals_df

# Export data
export_project_data("production", days=7)
```

### Data Retention Policy

```python
from phoenix import Client
from datetime import datetime, timedelta

client = Client()

def cleanup_old_data(project_name: str, retention_days: int = 90):
    """Delete data older than retention period."""
    cutoff_date = datetime.now() - timedelta(days=retention_days)

    # Get old traces
    old_spans = client.get_spans_dataframe(
        project_name=project_name,
        end_time=cutoff_date
    )

    # Delete old traces
    trace_ids = old_spans["trace_id"].unique()
    for trace_id in trace_ids:
        client.delete_trace(trace_id=trace_id)

    print(f"Deleted {len(trace_ids)} traces older than {retention_days} days")

# Run cleanup
cleanup_old_data("production", retention_days=90)
```

## Integration Patterns

### CI/CD Evaluation Pipeline

```python
# evaluate_in_ci.py
import sys
from phoenix import Client
from phoenix.experiments import run_experiment

def run_ci_evaluation():
    client = Client(endpoint="https://phoenix.company.com")

    results = run_experiment(
        dataset_name="regression-test-set",
        task=my_model,
        evaluators=[
            accuracy_evaluator,
            hallucination_evaluator,
            latency_evaluator
        ],
        experiment_name=f"ci-{os.environ['CI_COMMIT_SHA'][:8]}"
    )

    # Check thresholds
    if results.aggregate_metrics['accuracy'] < 0.9:
        print(f"FAIL: Accuracy {results.aggregate_metrics['accuracy']:.2f} < 0.9")
        sys.exit(1)

    if results.aggregate_metrics['hallucination_rate'] > 0.05:
        print(f"FAIL: Hallucination rate too high")
        sys.exit(1)

    print("PASS: All evaluation thresholds met")
    sys.exit(0)

if __name__ == "__main__":
    run_ci_evaluation()
```

### Alerting Integration

```python
from phoenix import Client
import requests

def check_and_alert():
    client = Client()

    # Get recent error rate
    spans_df = client.get_spans_dataframe(
        project_name="production",
        filter_condition="status_code == 'ERROR'",
        start_time=datetime.now() - timedelta(hours=1)
    )

    total_spans = client.get_spans_dataframe(
        project_name="production",
        start_time=datetime.now() - timedelta(hours=1)
    )

    error_rate = len(spans_df) / max(len(total_spans), 1)

    if error_rate > 0.05:  # 5% threshold
        # Send Slack alert
        requests.post(
            os.environ["SLACK_WEBHOOK_URL"],
            json={
                "text": f"🚨 High error rate in production: {error_rate:.1%}",
                "channel": "#alerts"
            }
        )

# Run periodically
check_and_alert()
```




### Troubleshooting

# Phoenix Troubleshooting Guide

## Installation Issues

### Package Not Found

**Error**: `ModuleNotFoundError: No module named 'phoenix'`

**Fix**:
```bash
pip install arize-phoenix

# Verify installation
python -c "import phoenix as px; print(px.__version__)"
```

### Dependency Conflicts

**Error**: `ImportError: cannot import name 'X' from 'Y'`

**Fix**:
```bash
# Create clean environment
python -m venv venv
source venv/bin/activate

# Install Phoenix
pip install arize-phoenix

# If using specific features
pip install arize-phoenix[embeddings]
pip install arize-phoenix-otel
pip install arize-phoenix-evals
```

### Version Conflicts with OpenTelemetry

**Error**: `ImportError: cannot import name 'TracerProvider'`

**Fix**:
```bash
# Ensure compatible versions
pip install opentelemetry-api>=1.20.0
pip install opentelemetry-sdk>=1.20.0
pip install arize-phoenix-otel
```

## Server Issues

### Port Already in Use

**Error**: `OSError: [Errno 48] Address already in use`

**Fix**:
```bash
# Find process using port
lsof -i :6006

# Kill the process
kill -9 <PID>

# Or use different port
phoenix serve --port 6007
```

### Database Connection Failed

**Error**: `sqlalchemy.exc.OperationalError: could not connect to server`

**Fix**:
```bash
# For PostgreSQL, verify connection
psql $PHOENIX_SQL_DATABASE_URL -c "SELECT 1"

# Check environment variable
echo $PHOENIX_SQL_DATABASE_URL

# For SQLite, check permissions
ls -la $PHOENIX_WORKING_DIR
```

### Server Crashes on Startup

**Error**: `RuntimeError: Event loop is closed`

**Fix**:
```python
# In notebooks, ensure proper async handling
import nest_asyncio
nest_asyncio.apply()

import phoenix as px
session = px.launch_app()
```

### Memory Issues

**Error**: `MemoryError` or server becomes slow

**Fix**:
```bash
# Increase available memory in Docker
docker run -m 4g arizephoenix/phoenix:latest

# Or clean up old data
from phoenix import Client
client = Client()
# Delete old traces (see advanced-usage.md for cleanup script)
```

## Tracing Issues

### Traces Not Appearing

**Problem**: Instrumented code runs but no traces in Phoenix

**Solutions**:

1. **Verify endpoint**:
```python
from phoenix.otel import register

# Ensure correct endpoint
tracer_provider = register(
    project_name="my-app",
    endpoint="http://localhost:6006/v1/traces"  # Include /v1/traces
)
```

2. **Force flush traces**:
```python
from opentelemetry import trace

# Force send pending traces
trace.get_tracer_provider().force_flush()
```

3. **Check Phoenix is running**:
```bash
curl http://localhost:6006/healthz
# Should return 200 OK
```

4. **Enable debug logging**:
```python
import logging
logging.basicConfig(level=logging.DEBUG)

from phoenix.otel import register
tracer_provider = register(project_name="debug-test")
```

### Missing Spans in Trace

**Problem**: Parent trace exists but child spans missing

**Fix**:
```python
from opentelemetry import trace

tracer = trace.get_tracer(__name__)

# Ensure spans are properly nested
with tracer.start_as_current_span("parent") as parent_span:
    # Child spans must be created within parent context
    with tracer.start_as_current_span("child"):
        do_something()
```

### Instrumentation Not Working

**Problem**: Framework calls not being traced

**Fix**:
```python
from phoenix.otel import register
from openinference.instrumentation.openai import OpenAIInstrumentor

# Must register BEFORE instrumenting
tracer_provider = register(project_name="my-app")

# Pass tracer_provider to instrumentor
OpenAIInstrumentor().instrument(tracer_provider=tracer_provider)

# Now import and use the SDK
from openai import OpenAI
client = OpenAI()
```

### Duplicate Traces

**Problem**: Same trace appearing multiple times

**Fix**:
```python
# Ensure instrumentor only called once
from openinference.instrumentation.openai import OpenAIInstrumentor

# Check if already instrumented
if not OpenAIInstrumentor().is_instrumented:
    OpenAIInstrumentor().instrument(tracer_provider=tracer_provider)
```

## Evaluation Issues

### Evaluator Returns None

**Error**: `AttributeError: 'NoneType' object has no attribute`

**Fix**:
```python
from phoenix.evals import OpenAIModel, llm_classify

# Ensure model is properly configured
eval_model = OpenAIModel(
    model="gpt-4o",
    api_key=os.environ.get("OPENAI_API_KEY")  # Explicit key
)

# Add error handling
try:
    result = llm_classify(
        model=eval_model,
        template=template,
        input=input_text,
        output=output_text,
        rails=["good", "bad"]
    )
except Exception as e:
    print(f"Evaluation failed: {e}")
    result = None
```

### Rate Limiting During Evaluation

**Error**: `RateLimitError: Rate limit exceeded`

**Fix**:
```python
from phoenix.evals import run_evals
import time

# Reduce concurrency
eval_results = run_evals(
    dataframe=spans_df,
    evaluators=[evaluator],
    concurrency=2  # Lower concurrency
)

# Or add retry logic
from tenacity import retry, wait_exponential

@retry(wait=wait_exponential(multiplier=1, min=4, max=60))
def evaluate_with_retry(input_text, output_text):
    return evaluator.evaluate(input_text, output_text)
```

### Evaluation Results Not Logging

**Problem**: Evaluations complete but don't appear in Phoenix

**Fix**:
```python
from phoenix import Client

client = Client()

# Ensure results are logged correctly
eval_results = run_evals(
    dataframe=spans_df,
    evaluators=[evaluator]
)

# Explicitly log evaluations
client.log_evaluations(
    project_name="my-app",
    evaluations=eval_results
)
```

## Client Issues

### Connection Refused

**Error**: `ConnectionRefusedError: [Errno 111] Connection refused`

**Fix**:
```python
from phoenix import Client

# Verify Phoenix is running
import requests
try:
    response = requests.get("http://localhost:6006/healthz")
    print(f"Phoenix status: {response.status_code}")
except:
    print("Phoenix not running")

# Use correct endpoint
client = Client(endpoint="http://localhost:6006")  # No /v1 for client
```

### Authentication Failed

**Error**: `401 Unauthorized`

**Fix**:
```python
from phoenix import Client

# If auth is enabled, provide API key
client = Client(
    endpoint="http://localhost:6006",
    api_key="your-api-key"  # Or use headers
)

# Or set environment variable
import os
os.environ["PHOENIX_API_KEY"] = "your-api-key"
client = Client()
```

### Timeout Errors

**Error**: `TimeoutError: Connection timed out`

**Fix**:
```python
from phoenix import Client

# Increase timeout
client = Client(
    endpoint="http://localhost:6006",
    timeout=60  # Seconds
)

# For large queries, use pagination
spans_df = client.get_spans_dataframe(
    project_name="my-app",
    limit=100,  # Smaller batches
    offset=0
)
```

## Database Issues

### PostgreSQL Connection Issues

**Error**: `psycopg2.OperationalError: FATAL: password authentication failed`

**Fix**:
```bash
# Verify credentials
psql "postgresql://user:pass@host:5432/phoenix"

# Check database exists
psql -h host -U user -c "SELECT datname FROM pg_database"

# Ensure correct URL format
export PHOENIX_SQL_DATABASE_URL="postgresql://user:pass@host:5432/phoenix"
```

### Migration Errors

**Error**: `alembic.util.exc.CommandError: Can't locate revision`

**Fix**:
```bash
# Reset migrations (WARNING: data loss)
# For development only
rm -rf $PHOENIX_WORKING_DIR/phoenix.db

# Restart Phoenix - will create fresh database
phoenix serve
```

### SQLite Lock Errors

**Error**: `sqlite3.OperationalError: database is locked`

**Fix**:
```python
# Ensure only one Phoenix instance
# Kill other Phoenix processes
pkill -f "phoenix serve"

# Or use PostgreSQL for concurrent access
export PHOENIX_SQL_DATABASE_URL="postgresql://..."
```

## UI Issues

### UI Not Loading

**Problem**: Phoenix server running but UI blank

**Fix**:
```bash
# Check if static files are served
curl http://localhost:6006/

# Verify server logs
phoenix serve --log-level debug

# Clear browser cache and try incognito mode
```

### Graphs Not Rendering

**Problem**: Dashboard shows but charts are empty

**Fix**:
```python
# Verify data exists
from phoenix import Client
client = Client()

spans = client.get_spans_dataframe(project_name="my-app")
print(f"Found {len(spans)} spans")

# Check project name matches
projects = client.list_projects()
print(f"Available projects: {[p.name for p in projects]}")
```

## Performance Issues

### Slow Query Performance

**Problem**: Getting spans takes too long

**Fix**:
```python
# Use filters to reduce data
spans_df = client.get_spans_dataframe(
    project_name="my-app",
    filter_condition="span_kind == 'LLM'",  # Filter
    limit=1000,  # Limit results
    start_time=datetime.now() - timedelta(days=1)  # Time range
)
```

### High Memory Usage

**Problem**: Phoenix using too much memory

**Fix**:
```bash
# For production, use PostgreSQL instead of SQLite
export PHOENIX_SQL_DATABASE_URL="postgresql://..."

# Set data retention
export PHOENIX_TRACE_RETENTION_DAYS=30

# Or manually clean old data
```

### Slow Trace Ingestion

**Problem**: Traces taking long to appear

**Fix**:
```python
# Check if bulk inserter is backing up
# Look for warnings in Phoenix logs

# Reduce trace volume
from phoenix.otel import register

tracer_provider = register(
    project_name="my-app",
    # Sample traces
    sampler=TraceIdRatioBased(0.1)  # 10% sampling
)
```

## Debugging Tips

### Enable Debug Logging

```python
import logging

# Phoenix debug logging
logging.getLogger("phoenix").setLevel(logging.DEBUG)

# OpenTelemetry debug logging
logging.getLogger("opentelemetry").setLevel(logging.DEBUG)
```

### Verify Configuration

```python
import os

print("Phoenix Configuration:")
print(f"  PHOENIX_PORT: {os.environ.get('PHOENIX_PORT', '6006')}")
print(f"  PHOENIX_HOST: {os.environ.get('PHOENIX_HOST', '127.0.0.1')}")
print(f"  PHOENIX_SQL_DATABASE_URL: {'SET' if os.environ.get('PHOENIX_SQL_DATABASE_URL') else 'NOT SET'}")
print(f"  PHOENIX_ENABLE_AUTH: {os.environ.get('PHOENIX_ENABLE_AUTH', 'false')}")
```

### Test Basic Connectivity

```python
import requests

# Test Phoenix server
try:
    r = requests.get("http://localhost:6006/healthz")
    print(f"Health check: {r.status_code}")
except Exception as e:
    print(f"Failed to connect: {e}")

# Test OTLP endpoint
try:
    r = requests.post("http://localhost:6006/v1/traces", json={})
    print(f"OTLP endpoint: {r.status_code}")
except Exception as e:
    print(f"OTLP failed: {e}")
```

## Getting Help

1. **Documentation**: https://docs.arize.com/phoenix
2. **GitHub Issues**: https://github.com/Arize-ai/phoenix/issues
3. **Discord**: https://discord.gg/arize
4. **Stack Overflow**: Tag `arize-phoenix`

### Reporting Issues

Include:
- Phoenix version: `pip show arize-phoenix`
- Python version: `python --version`
- Full error traceback
- Minimal reproducible code
- Environment (local, Docker, Kubernetes)
- Database type (SQLite/PostgreSQL)




---

## 🚀 Usage

**Reference this template:** `@skill-phoenix-observability.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
