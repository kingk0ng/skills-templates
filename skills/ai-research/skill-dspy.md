---
id: skill-dspy
type: skill
name: dspy
description: Build complex AI systems with declarative programming, optimize prompts
  automatically, create modular RAG systems and agents with DSPy - Stanford NLP's
  framework for systematic LM programming
category: ai-research
complexity: medium
keywords:
- api
- git
- github
- optimization
- python
- qa
- react
capabilities: []
token_estimate: 1998
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,998 -->
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




# dspy

> Build complex AI systems with declarative programming, optimize prompts automatically, create modular RAG systems and agents with DSPy - Stanford NLP's framework for systematic LM programming

# DSPy: Declarative Language Model Programming

## When to Use This Skill

Use DSPy when you need to:
- **Build complex AI systems** with multiple components and workflows
- **Program LMs declaratively** instead of manual prompt engineering
- **Optimize prompts automatically** using data-driven methods
- **Create modular AI pipelines** that are maintainable and portable
- **Improve model outputs systematically** with optimizers
- **Build RAG systems, agents, or classifiers** with better reliability

**GitHub Stars**: 22,000+ | **Created By**: Stanford NLP

## Installation

```bash
# Stable release
pip install dspy

# Latest development version
pip install git+https://github.com/stanfordnlp/dspy.git

# With specific LM providers
pip install dspy[openai]        # OpenAI
pip install dspy[anthropic]     # Anthropic Claude
pip install dspy[all]           # All providers
```

## Quick Start

### Basic Example: Question Answering

```python
import dspy

# Configure your language model
lm = dspy.Claude(model="claude-sonnet-4-5-20250929")
dspy.settings.configure(lm=lm)

# Define a signature (input → output)
class QA(dspy.Signature):
    """Answer questions with short factual answers."""
    question = dspy.InputField()
    answer = dspy.OutputField(desc="often between 1 and 5 words")

# Create a module
qa = dspy.Predict(QA)

# Use it
response = qa(question="What is the capital of France?")
print(response.answer)  # "Paris"
```

### Chain of Thought Reasoning

```python
import dspy

lm = dspy.Claude(model="claude-sonnet-4-5-20250929")
dspy.settings.configure(lm=lm)

# Use ChainOfThought for better reasoning
class MathProblem(dspy.Signature):
    """Solve math word problems."""
    problem = dspy.InputField()
    answer = dspy.OutputField(desc="numerical answer")

# ChainOfThought generates reasoning steps automatically
cot = dspy.ChainOfThought(MathProblem)

response = cot(problem="If John has 5 apples and gives 2 to Mary, how many does he have?")
print(response.rationale)  # Shows reasoning steps
print(response.answer)     # "3"
```

## Core Concepts

### 1. Signatures

Signatures define the structure of your AI task (inputs → outputs):

```python
# Inline signature (simple)
qa = dspy.Predict("question -> answer")

# Class signature (detailed)
class Summarize(dspy.Signature):
    """Summarize text into key points."""
    text = dspy.InputField()
    summary = dspy.OutputField(desc="bullet points, 3-5 items")

summarizer = dspy.ChainOfThought(Summarize)
```

**When to use each:**
- **Inline**: Quick prototyping, simple tasks
- **Class**: Complex tasks, type hints, better documentation

### 2. Modules

Modules are reusable components that transform inputs to outputs:

#### dspy.Predict
Basic prediction module:

```python
predictor = dspy.Predict("context, question -> answer")
result = predictor(context="Paris is the capital of France",
                   question="What is the capital?")
```

#### dspy.ChainOfThought
Generates reasoning steps before answering:

```python
cot = dspy.ChainOfThought("question -> answer")
result = cot(question="Why is the sky blue?")
print(result.rationale)  # Reasoning steps
print(result.answer)     # Final answer
```

#### dspy.ReAct
Agent-like reasoning with tools:

```python
from dspy.predict import ReAct

class SearchQA(dspy.Signature):
    """Answer questions using search."""
    question = dspy.InputField()
    answer = dspy.OutputField()

def search_tool(query: str) -> str:
    """Search Wikipedia."""
    # Your search implementation
    return results

react = ReAct(SearchQA, tools=[search_tool])
result = react(question="When was Python created?")
```

#### dspy.ProgramOfThought
Generates and executes code for reasoning:

```python
pot = dspy.ProgramOfThought("question -> answer")
result = pot(question="What is 15% of 240?")
# Generates: answer = 240 * 0.15
```

### 3. Optimizers

Optimizers improve your modules automatically using training data:

#### BootstrapFewShot
Learns from examples:

```python
from dspy.teleprompt import BootstrapFewShot

# Training data
trainset = [
    dspy.Example(question="What is 2+2?", answer="4").with_inputs("question"),
    dspy.Example(question="What is 3+5?", answer="8").with_inputs("question"),
]

# Define metric
def validate_answer(example, pred, trace=None):
    return example.answer == pred.answer

# Optimize
optimizer = BootstrapFewShot(metric=validate_answer, max_bootstrapped_demos=3)
optimized_qa = optimizer.compile(qa, trainset=trainset)

# Now optimized_qa performs better!
```

#### MIPRO (Most Important Prompt Optimization)
Iteratively improves prompts:

```python
from dspy.teleprompt import MIPRO

optimizer = MIPRO(
    metric=validate_answer,
    num_candidates=10,
    init_temperature=1.0
)

optimized_cot = optimizer.compile(
    cot,
    trainset=trainset,
    num_trials=100
)
```

#### BootstrapFinetune
Creates datasets for model fine-tuning:

```python
from dspy.teleprompt import BootstrapFinetune

optimizer = BootstrapFinetune(metric=validate_answer)
optimized_module = optimizer.compile(qa, trainset=trainset)

# Exports training data for fine-tuning
```

### 4. Building Complex Systems

#### Multi-Stage Pipeline

```python
import dspy

class MultiHopQA(dspy.Module):
    def __init__(self):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=3)
        self.generate_query = dspy.ChainOfThought("question -> search_query")
        self.generate_answer = dspy.ChainOfThought("context, question -> answer")

    def forward(self, question):
        # Stage 1: Generate search query
        search_query = self.generate_query(question=question).search_query

        # Stage 2: Retrieve context
        passages = self.retrieve(search_query).passages
        context = "\n".join(passages)

        # Stage 3: Generate answer
        answer = self.generate_answer(context=context, question=question).answer
        return dspy.Prediction(answer=answer, context=context)

# Use the pipeline
qa_system = MultiHopQA()
result = qa_system(question="Who wrote the book that inspired the movie Blade Runner?")
```

#### RAG System with Optimization

```python
import dspy
from dspy.retrieve.chromadb_rm import ChromadbRM

# Configure retriever
retriever = ChromadbRM(
    collection_name="documents",
    persist_directory="./chroma_db"
)

class RAG(dspy.Module):
    def __init__(self, num_passages=3):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=num_passages)
        self.generate = dspy.ChainOfThought("context, question -> answer")

    def forward(self, question):
        context = self.retrieve(question).passages
        return self.generate(context=context, question=question)

# Create and optimize
rag = RAG()

# Optimize with training data
from dspy.teleprompt import BootstrapFewShot

optimizer = BootstrapFewShot(metric=validate_answer)
optimized_rag = optimizer.compile(rag, trainset=trainset)
```

## LM Provider Configuration

### Anthropic Claude

```python
import dspy

lm = dspy.Claude(
    model="claude-sonnet-4-5-20250929",
    api_key="your-api-key",  # Or set ANTHROPIC_API_KEY env var
    max_tokens=1000,
    temperature=0.7
)
dspy.settings.configure(lm=lm)
```

### OpenAI

```python
lm = dspy.OpenAI(
    model="gpt-4",
    api_key="your-api-key",
    max_tokens=1000
)
dspy.settings.configure(lm=lm)
```

### Local Models (Ollama)

```python
lm = dspy.OllamaLocal(
    model="llama3.1",
    base_url="http://localhost:11434"
)
dspy.settings.configure(lm=lm)
```

### Multiple Models

```python
# Different models for different tasks
cheap_lm = dspy.OpenAI(model="gpt-3.5-turbo")
strong_lm = dspy.Claude(model="claude-sonnet-4-5-20250929")

# Use cheap model for retrieval, strong model for reasoning
with dspy.settings.context(lm=cheap_lm):
    context = retriever(question)

with dspy.settings.context(lm=strong_lm):
    answer = generator(context=context, question=question)
```

## Common Patterns

### Pattern 1: Structured Output

```python
from pydantic import BaseModel, Field

class PersonInfo(BaseModel):
    name: str = Field(description="Full name")
    age: int = Field(description="Age in years")
    occupation: str = Field(description="Current job")

class ExtractPerson(dspy.Signature):
    """Extract person information from text."""
    text = dspy.InputField()
    person: PersonInfo = dspy.OutputField()

extractor = dspy.TypedPredictor(ExtractPerson)
result = extractor(text="John Doe is a 35-year-old software engineer.")
print(result.person.name)  # "John Doe"
print(result.person.age)   # 35
```

### Pattern 2: Assertion-Driven Optimization

```python
import dspy
from dspy.primitives.assertions import assert_transform_module, backtrack_handler

class MathQA(dspy.Module):
    def __init__(self):
        super().__init__()
        self.solve = dspy.ChainOfThought("problem -> solution: float")

    def forward(self, problem):
        solution = self.solve(problem=problem).solution

        # Assert solution is numeric
        dspy.Assert(
            isinstance(float(solution), float),
            "Solution must be a number",
            backtrack=backtrack_handler
        )

        return dspy.Prediction(solution=solution)
```

### Pattern 3: Self-Consistency

```python
import dspy
from collections import Counter

class ConsistentQA(dspy.Module):
    def __init__(self, num_samples=5):
        super().__init__()
        self.qa = dspy.ChainOfThought("question -> answer")
        self.num_samples = num_samples

    def forward(self, question):
        # Generate multiple answers
        answers = []
        for _ in range(self.num_samples):
            result = self.qa(question=question)
            answers.append(result.answer)

        # Return most common answer
        most_common = Counter(answers).most_common(1)[0][0]
        return dspy.Prediction(answer=most_common)
```

### Pattern 4: Retrieval with Reranking

```python
class RerankedRAG(dspy.Module):
    def __init__(self):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=10)
        self.rerank = dspy.Predict("question, passage -> relevance_score: float")
        self.answer = dspy.ChainOfThought("context, question -> answer")

    def forward(self, question):
        # Retrieve candidates
        passages = self.retrieve(question).passages

        # Rerank passages
        scored = []
        for passage in passages:
            score = float(self.rerank(question=question, passage=passage).relevance_score)
            scored.append((score, passage))

        # Take top 3
        top_passages = [p for _, p in sorted(scored, reverse=True)[:3]]
        context = "\n\n".join(top_passages)

        # Generate answer
        return self.answer(context=context, question=question)
```

## Evaluation and Metrics

### Custom Metrics

```python
def exact_match(example, pred, trace=None):
    """Exact match metric."""
    return example.answer.lower() == pred.answer.lower()

def f1_score(example, pred, trace=None):
    """F1 score for text overlap."""
    pred_tokens = set(pred.answer.lower().split())
    gold_tokens = set(example.answer.lower().split())

    if not pred_tokens:
        return 0.0

    precision = len(pred_tokens & gold_tokens) / len(pred_tokens)
    recall = len(pred_tokens & gold_tokens) / len(gold_tokens)

    if precision + recall == 0:
        return 0.0

    return 2 * (precision * recall) / (precision + recall)
```

### Evaluation

```python
from dspy.evaluate import Evaluate

# Create evaluator
evaluator = Evaluate(
    devset=testset,
    metric=exact_match,
    num_threads=4,
    display_progress=True
)

# Evaluate model
score = evaluator(qa_system)
print(f"Accuracy: {score}")

# Compare optimized vs unoptimized
score_before = evaluator(qa)
score_after = evaluator(optimized_qa)
print(f"Improvement: {score_after - score_before:.2%}")
```

## Best Practices

### 1. Start Simple, Iterate

```python
# Start with Predict
qa = dspy.Predict("question -> answer")

# Add reasoning if needed
qa = dspy.ChainOfThought("question -> answer")

# Add optimization when you have data
optimized_qa = optimizer.compile(qa, trainset=data)
```

### 2. Use Descriptive Signatures

```python
# ❌ Bad: Vague
class Task(dspy.Signature):
    input = dspy.InputField()
    output = dspy.OutputField()

# ✅ Good: Descriptive
class SummarizeArticle(dspy.Signature):
    """Summarize news articles into 3-5 key points."""
    article = dspy.InputField(desc="full article text")
    summary = dspy.OutputField(desc="bullet points, 3-5 items")
```

### 3. Optimize with Representative Data

```python
# Create diverse training examples
trainset = [
    dspy.Example(question="factual", answer="...).with_inputs("question"),
    dspy.Example(question="reasoning", answer="...").with_inputs("question"),
    dspy.Example(question="calculation", answer="...").with_inputs("question"),
]

# Use validation set for metric
def metric(example, pred, trace=None):
    return example.answer in pred.answer
```

### 4. Save and Load Optimized Models

```python
# Save
optimized_qa.save("models/qa_v1.json")

# Load
loaded_qa = dspy.ChainOfThought("question -> answer")
loaded_qa.load("models/qa_v1.json")
```

### 5. Monitor and Debug

```python
# Enable tracing
dspy.settings.configure(lm=lm, trace=[])

# Run prediction
result = qa(question="...")

# Inspect trace
for call in dspy.settings.trace:
    print(f"Prompt: {call['prompt']}")
    print(f"Response: {call['response']}")
```

## Comparison to Other Approaches

| Feature | Manual Prompting | LangChain | DSPy |
|---------|-----------------|-----------|------|
| Prompt Engineering | Manual | Manual | Automatic |
| Optimization | Trial & error | None | Data-driven |
| Modularity | Low | Medium | High |
| Type Safety | No | Limited | Yes (Signatures) |
| Portability | Low | Medium | High |
| Learning Curve | Low | Medium | Medium-High |

**When to choose DSPy:**
- You have training data or can generate it
- You need systematic prompt improvement
- You're building complex multi-stage systems
- You want to optimize across different LMs

**When to choose alternatives:**
- Quick prototypes (manual prompting)
- Simple chains with existing tools (LangChain)
- Custom optimization logic needed

## Resources

- **Documentation**: https://dspy.ai
- **GitHub**: https://github.com/stanfordnlp/dspy (22k+ stars)
- **Discord**: https://discord.gg/XCGy2WDCQB
- **Twitter**: @DSPyOSS
- **Paper**: "DSPy: Compiling Declarative Language Model Calls into Self-Improving Pipelines"

## See Also

- `references/modules.md` - Detailed module guide (Predict, ChainOfThought, ReAct, ProgramOfThought)
- `references/optimizers.md` - Optimization algorithms (BootstrapFewShot, MIPRO, BootstrapFinetune)
- `references/examples.md` - Real-world examples (RAG, agents, classifiers)




---


## 📚 Reference Materials


### Modules

# DSPy Modules

Complete guide to DSPy's built-in modules for language model programming.

## Module Basics

DSPy modules are composable building blocks inspired by PyTorch's NN modules:
- Have learnable parameters (prompts, few-shot examples)
- Can be composed using Python control flow
- Generalized to handle any signature
- Optimizable with DSPy optimizers

### Base Module Pattern

```python
import dspy

class CustomModule(dspy.Module):
    def __init__(self):
        super().__init__()
        # Initialize sub-modules
        self.predictor = dspy.Predict("input -> output")

    def forward(self, input):
        # Module logic
        result = self.predictor(input=input)
        return result
```

## Core Modules

### dspy.Predict

**Basic prediction module** - Makes LM calls without reasoning steps.

```python
# Inline signature
qa = dspy.Predict("question -> answer")
result = qa(question="What is 2+2?")

# Class signature
class QA(dspy.Signature):
    """Answer questions concisely."""
    question = dspy.InputField()
    answer = dspy.OutputField(desc="short, factual answer")

qa = dspy.Predict(QA)
result = qa(question="What is the capital of France?")
print(result.answer)  # "Paris"
```

**When to use:**
- Simple, direct predictions
- No reasoning steps needed
- Fast responses required

### dspy.ChainOfThought

**Step-by-step reasoning** - Generates rationale before answer.

**Parameters:**
- `signature`: Task signature
- `rationale_field`: Custom reasoning field (optional)
- `rationale_field_type`: Type for rationale (default: `str`)

```python
# Basic usage
cot = dspy.ChainOfThought("question -> answer")
result = cot(question="If I have 5 apples and give away 2, how many remain?")
print(result.rationale)  # "Let's think step by step..."
print(result.answer)     # "3"

# Custom rationale field
cot = dspy.ChainOfThought(
    signature="problem -> solution",
    rationale_field=dspy.OutputField(
        prefix="Reasoning: Let's break this down step by step to"
    )
)
```

**When to use:**
- Complex reasoning tasks
- Math word problems
- Logical deduction
- Quality > speed

**Performance:**
- ~2x slower than Predict
- Significantly better accuracy on reasoning tasks

### dspy.ProgramOfThought

**Code-based reasoning** - Generates and executes Python code.

```python
pot = dspy.ProgramOfThought("question -> answer")

result = pot(question="What is 15% of 240?")
# Internally generates: answer = 240 * 0.15
# Executes code and returns result
print(result.answer)  # 36.0

result = pot(question="If a train travels 60 mph for 2.5 hours, how far does it go?")
# Generates: distance = 60 * 2.5
print(result.answer)  # 150.0
```

**When to use:**
- Arithmetic calculations
- Symbolic math
- Data transformations
- Deterministic computations

**Benefits:**
- More reliable than text-based math
- Handles complex calculations
- Transparent (shows generated code)

### dspy.ReAct

**Reasoning + Acting** - Agent that uses tools iteratively.

```python
from dspy.predict import ReAct

# Define tools
def search_wikipedia(query: str) -> str:
    """Search Wikipedia for information."""
    # Your search implementation
    return search_results

def calculate(expression: str) -> float:
    """Evaluate a mathematical expression."""
    return eval(expression)

# Create ReAct agent
class ResearchQA(dspy.Signature):
    """Answer questions using available tools."""
    question = dspy.InputField()
    answer = dspy.OutputField()

react = ReAct(ResearchQA, tools=[search_wikipedia, calculate])

# Agent decides which tools to use
result = react(question="How old was Einstein when he published special relativity?")
# Internally:
# 1. Thinks: "Need birth year and publication year"
# 2. Acts: search_wikipedia("Albert Einstein")
# 3. Acts: search_wikipedia("Special relativity 1905")
# 4. Acts: calculate("1905 - 1879")
# 5. Returns: "26 years old"
```

**When to use:**
- Multi-step research tasks
- Tool-using agents
- Complex information retrieval
- Tasks requiring multiple API calls

**Best practices:**
- Keep tool descriptions clear and specific
- Limit to 5-7 tools (too many = confusion)
- Provide tool usage examples in docstrings

### dspy.MultiChainComparison

**Generate multiple outputs and compare** - Self-consistency pattern.

```python
mcc = dspy.MultiChainComparison("question -> answer", M=5)

result = mcc(question="What is the capital of France?")
# Generates 5 candidate answers
# Compares and selects most consistent
print(result.answer)  # "Paris"
print(result.candidates)  # All 5 generated answers
```

**Parameters:**
- `M`: Number of candidates to generate (default: 5)
- `temperature`: Sampling temperature for diversity

**When to use:**
- High-stakes decisions
- Ambiguous questions
- When single answer may be unreliable

**Tradeoff:**
- M times slower (M parallel calls)
- Higher accuracy on ambiguous tasks

### dspy.majority

**Majority voting over multiple predictions.**

```python
from dspy.primitives import majority

# Generate multiple predictions
predictor = dspy.Predict("question -> answer")
predictions = [predictor(question="What is 2+2?") for _ in range(5)]

# Take majority vote
answer = majority([p.answer for p in predictions])
print(answer)  # "4"
```

**When to use:**
- Combining multiple model outputs
- Reducing variance in predictions
- Ensemble approaches

## Advanced Modules

### dspy.TypedPredictor

**Structured output with Pydantic models.**

```python
from pydantic import BaseModel, Field

class PersonInfo(BaseModel):
    name: str = Field(description="Full name")
    age: int = Field(description="Age in years")
    occupation: str = Field(description="Current job")

class ExtractPerson(dspy.Signature):
    """Extract person information from text."""
    text = dspy.InputField()
    person: PersonInfo = dspy.OutputField()

extractor = dspy.TypedPredictor(ExtractPerson)
result = extractor(text="John Doe is a 35-year-old software engineer.")

print(result.person.name)       # "John Doe"
print(result.person.age)        # 35
print(result.person.occupation) # "software engineer"
```

**Benefits:**
- Type safety
- Automatic validation
- JSON schema generation
- IDE autocomplete

### dspy.Retry

**Automatic retry with validation.**

```python
from dspy.primitives import Retry

def validate_number(example, pred, trace=None):
    """Validate output is a number."""
    try:
        float(pred.answer)
        return True
    except ValueError:
        return False

# Retry up to 3 times if validation fails
qa = Retry(
    dspy.ChainOfThought("question -> answer"),
    validate=validate_number,
    max_retries=3
)

result = qa(question="What is 15% of 80?")
# If first attempt returns non-numeric, retries automatically
```

### dspy.Assert

**Assertion-driven optimization.**

```python
import dspy
from dspy.primitives.assertions import assert_transform_module, backtrack_handler

class ValidatedQA(dspy.Module):
    def __init__(self):
        super().__init__()
        self.qa = dspy.ChainOfThought("question -> answer: float")

    def forward(self, question):
        answer = self.qa(question=question).answer

        # Assert answer is numeric
        dspy.Assert(
            isinstance(float(answer), float),
            "Answer must be a number",
            backtrack=backtrack_handler
        )

        return dspy.Prediction(answer=answer)
```

**Benefits:**
- Catches errors during optimization
- Guides LM toward valid outputs
- Better than post-hoc filtering

## Module Composition

### Sequential Pipeline

```python
class Pipeline(dspy.Module):
    def __init__(self):
        super().__init__()
        self.stage1 = dspy.Predict("input -> intermediate")
        self.stage2 = dspy.ChainOfThought("intermediate -> output")

    def forward(self, input):
        intermediate = self.stage1(input=input).intermediate
        output = self.stage2(intermediate=intermediate).output
        return dspy.Prediction(output=output)
```

### Conditional Logic

```python
class ConditionalModule(dspy.Module):
    def __init__(self):
        super().__init__()
        self.router = dspy.Predict("question -> category: str")
        self.simple_qa = dspy.Predict("question -> answer")
        self.complex_qa = dspy.ChainOfThought("question -> answer")

    def forward(self, question):
        category = self.router(question=question).category

        if category == "simple":
            return self.simple_qa(question=question)
        else:
            return self.complex_qa(question=question)
```

### Parallel Execution

```python
class ParallelModule(dspy.Module):
    def __init__(self):
        super().__init__()
        self.approach1 = dspy.ChainOfThought("question -> answer")
        self.approach2 = dspy.ProgramOfThought("question -> answer")

    def forward(self, question):
        # Run both approaches
        answer1 = self.approach1(question=question).answer
        answer2 = self.approach2(question=question).answer

        # Compare or combine results
        if answer1 == answer2:
            return dspy.Prediction(answer=answer1, confidence="high")
        else:
            return dspy.Prediction(answer=answer1, confidence="low")
```

## Batch Processing

All modules support batch processing for efficiency:

```python
cot = dspy.ChainOfThought("question -> answer")

questions = [
    "What is 2+2?",
    "What is 3+3?",
    "What is 4+4?"
]

# Process all at once
results = cot.batch([{"question": q} for q in questions])

for result in results:
    print(result.answer)
```

## Saving and Loading

```python
# Save module
qa = dspy.ChainOfThought("question -> answer")
qa.save("models/qa_v1.json")

# Load module
loaded_qa = dspy.ChainOfThought("question -> answer")
loaded_qa.load("models/qa_v1.json")
```

**What gets saved:**
- Few-shot examples
- Prompt instructions
- Module configuration

**What doesn't get saved:**
- Model weights (DSPy doesn't fine-tune by default)
- LM provider configuration

## Module Selection Guide

| Task | Module | Reason |
|------|--------|--------|
| Simple classification | Predict | Fast, direct |
| Math word problems | ProgramOfThought | Reliable calculations |
| Logical reasoning | ChainOfThought | Better with steps |
| Multi-step research | ReAct | Tool usage |
| High-stakes decisions | MultiChainComparison | Self-consistency |
| Structured extraction | TypedPredictor | Type safety |
| Ambiguous questions | MultiChainComparison | Multiple perspectives |

## Performance Tips

1. **Start with Predict**, add reasoning only if needed
2. **Use batch processing** for multiple inputs
3. **Cache predictions** for repeated queries
4. **Profile token usage** with `track_usage=True`
5. **Optimize after prototyping** with teleprompters

## Common Patterns

### Pattern: Retrieval + Generation

```python
class RAG(dspy.Module):
    def __init__(self, k=3):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=k)
        self.generate = dspy.ChainOfThought("context, question -> answer")

    def forward(self, question):
        context = self.retrieve(question).passages
        return self.generate(context=context, question=question)
```

### Pattern: Verification Loop

```python
class VerifiedQA(dspy.Module):
    def __init__(self):
        super().__init__()
        self.answer = dspy.ChainOfThought("question -> answer")
        self.verify = dspy.Predict("question, answer -> is_correct: bool")

    def forward(self, question, max_attempts=3):
        for _ in range(max_attempts):
            answer = self.answer(question=question).answer
            is_correct = self.verify(question=question, answer=answer).is_correct

            if is_correct:
                return dspy.Prediction(answer=answer)

        return dspy.Prediction(answer="Unable to verify answer")
```

### Pattern: Multi-Turn Dialog

```python
class DialogAgent(dspy.Module):
    def __init__(self):
        super().__init__()
        self.respond = dspy.Predict("history, user_message -> assistant_message")
        self.history = []

    def forward(self, user_message):
        history_str = "\n".join(self.history)
        response = self.respond(history=history_str, user_message=user_message)

        self.history.append(f"User: {user_message}")
        self.history.append(f"Assistant: {response.assistant_message}")

        return response
```




### Optimizers

# DSPy Optimizers (Teleprompters)

Complete guide to DSPy's optimization algorithms for improving prompts and model weights.

## What are Optimizers?

DSPy optimizers (called "teleprompters") automatically improve your modules by:
- **Synthesizing few-shot examples** from training data
- **Proposing better instructions** through search
- **Fine-tuning model weights** (optional)

**Key idea**: Instead of manually tuning prompts, define a metric and let DSPy optimize.

## Optimizer Selection Guide

| Optimizer | Best For | Speed | Quality | Data Needed |
|-----------|----------|-------|---------|-------------|
| BootstrapFewShot | General purpose | Fast | Good | 10-50 examples |
| MIPRO | Instruction tuning | Medium | Excellent | 50-200 examples |
| BootstrapFinetune | Fine-tuning | Slow | Excellent | 100+ examples |
| COPRO | Prompt optimization | Medium | Good | 20-100 examples |
| KNNFewShot | Quick baseline | Very fast | Fair | 10+ examples |

## Core Optimizers

### BootstrapFewShot

**Most popular optimizer** - Generates few-shot demonstrations from training data.

**How it works:**
1. Takes your training examples
2. Uses your module to generate predictions
3. Selects high-quality predictions (based on metric)
4. Uses these as few-shot examples in future prompts

**Parameters:**
- `metric`: Function that scores predictions (required)
- `max_bootstrapped_demos`: Max demonstrations to generate (default: 4)
- `max_labeled_demos`: Max labeled examples to use (default: 16)
- `max_rounds`: Optimization iterations (default: 1)
- `metric_threshold`: Minimum score to accept (optional)

```python
import dspy
from dspy.teleprompt import BootstrapFewShot

# Define metric
def validate_answer(example, pred, trace=None):
    """Return True if prediction matches gold answer."""
    return example.answer.lower() == pred.answer.lower()

# Training data
trainset = [
    dspy.Example(question="What is 2+2?", answer="4").with_inputs("question"),
    dspy.Example(question="What is 3+5?", answer="8").with_inputs("question"),
    dspy.Example(question="What is 10-3?", answer="7").with_inputs("question"),
]

# Create module
qa = dspy.ChainOfThought("question -> answer")

# Optimize
optimizer = BootstrapFewShot(
    metric=validate_answer,
    max_bootstrapped_demos=3,
    max_rounds=2
)

optimized_qa = optimizer.compile(qa, trainset=trainset)

# Now optimized_qa has learned few-shot examples!
result = optimized_qa(question="What is 5+7?")
```

**Best practices:**
- Start with 10-50 training examples
- Use diverse examples covering edge cases
- Set `max_bootstrapped_demos=3-5` for most tasks
- Increase `max_rounds=2-3` for better quality

**When to use:**
- First optimizer to try
- You have 10+ labeled examples
- Want quick improvements
- General-purpose tasks

### MIPRO (Most Important Prompt Optimization)

**State-of-the-art optimizer** - Iteratively searches for better instructions.

**How it works:**
1. Generates candidate instructions
2. Tests each on validation set
3. Selects best-performing instructions
4. Iterates to refine further

**Parameters:**
- `metric`: Evaluation metric (required)
- `num_candidates`: Instructions to try per iteration (default: 10)
- `init_temperature`: Sampling temperature (default: 1.0)
- `verbose`: Show progress (default: False)

```python
from dspy.teleprompt import MIPRO

# Define metric with more nuance
def answer_quality(example, pred, trace=None):
    """Score answer quality 0-1."""
    if example.answer.lower() in pred.answer.lower():
        return 1.0
    # Partial credit for similar answers
    return 0.5 if len(set(example.answer.split()) & set(pred.answer.split())) > 0 else 0.0

# Larger training set (MIPRO benefits from more data)
trainset = [...]  # 50-200 examples
valset = [...]    # 20-50 examples

# Create module
qa = dspy.ChainOfThought("question -> answer")

# Optimize with MIPRO
optimizer = MIPRO(
    metric=answer_quality,
    num_candidates=10,
    init_temperature=1.0,
    verbose=True
)

optimized_qa = optimizer.compile(
    student=qa,
    trainset=trainset,
    valset=valset,  # MIPRO uses separate validation set
    num_trials=100   # More trials = better quality
)
```

**Best practices:**
- Use 50-200 training examples
- Separate validation set (20-50 examples)
- Run 100-200 trials for best results
- Takes 10-30 minutes typically

**When to use:**
- You have 50+ labeled examples
- Want state-of-the-art performance
- Willing to wait for optimization
- Complex reasoning tasks

### BootstrapFinetune

**Fine-tune model weights** - Creates training dataset for fine-tuning.

**How it works:**
1. Generates synthetic training data
2. Exports data in fine-tuning format
3. You fine-tune model separately
4. Load fine-tuned model back

**Parameters:**
- `metric`: Evaluation metric (required)
- `max_bootstrapped_demos`: Demonstrations to generate (default: 4)
- `max_rounds`: Data generation rounds (default: 1)

```python
from dspy.teleprompt import BootstrapFinetune

# Training data
trainset = [...]  # 100+ examples recommended

# Define metric
def validate(example, pred, trace=None):
    return example.answer == pred.answer

# Create module
qa = dspy.ChainOfThought("question -> answer")

# Generate fine-tuning data
optimizer = BootstrapFinetune(metric=validate)
optimized_qa = optimizer.compile(qa, trainset=trainset)

# Exports training data to file
# You then fine-tune using your LM provider's API

# After fine-tuning, load your model:
finetuned_lm = dspy.OpenAI(model="ft:gpt-3.5-turbo:your-model-id")
dspy.settings.configure(lm=finetuned_lm)
```

**Best practices:**
- Use 100+ training examples
- Validate on held-out test set
- Monitor for overfitting
- Compare with prompt-based methods first

**When to use:**
- You have 100+ examples
- Latency is critical (fine-tuned models faster)
- Task is narrow and well-defined
- Prompt optimization isn't enough

### COPRO (Coordinate Prompt Optimization)

**Optimize prompts via gradient-free search.**

**How it works:**
1. Generates prompt variants
2. Evaluates each variant
3. Selects best prompts
4. Iterates to refine

```python
from dspy.teleprompt import COPRO

# Training data
trainset = [...]

# Define metric
def metric(example, pred, trace=None):
    return example.answer == pred.answer

# Create module
qa = dspy.ChainOfThought("question -> answer")

# Optimize with COPRO
optimizer = COPRO(
    metric=metric,
    breadth=10,  # Candidates per iteration
    depth=3      # Optimization rounds
)

optimized_qa = optimizer.compile(qa, trainset=trainset)
```

**When to use:**
- Want prompt optimization
- Have 20-100 examples
- MIPRO too slow

### KNNFewShot

**Simple k-nearest neighbors** - Selects similar examples for each query.

**How it works:**
1. Embeds all training examples
2. For each query, finds k most similar examples
3. Uses these as few-shot demonstrations

```python
from dspy.teleprompt import KNNFewShot

trainset = [...]

# No metric needed - just selects similar examples
optimizer = KNNFewShot(k=3)
optimized_qa = optimizer.compile(qa, trainset=trainset)

# For each query, uses 3 most similar examples from trainset
```

**When to use:**
- Quick baseline
- Have diverse training examples
- Similarity is good proxy for helpfulness

## Writing Metrics

Metrics are functions that score predictions. They're critical for optimization.

### Binary Metrics

```python
def exact_match(example, pred, trace=None):
    """Return True if prediction exactly matches gold."""
    return example.answer == pred.answer

def contains_answer(example, pred, trace=None):
    """Return True if prediction contains gold answer."""
    return example.answer.lower() in pred.answer.lower()
```

### Continuous Metrics

```python
def f1_score(example, pred, trace=None):
    """F1 score between prediction and gold."""
    pred_tokens = set(pred.answer.lower().split())
    gold_tokens = set(example.answer.lower().split())

    if not pred_tokens:
        return 0.0

    precision = len(pred_tokens & gold_tokens) / len(pred_tokens)
    recall = len(pred_tokens & gold_tokens) / len(gold_tokens)

    if precision + recall == 0:
        return 0.0

    return 2 * (precision * recall) / (precision + recall)

def semantic_similarity(example, pred, trace=None):
    """Embedding similarity between prediction and gold."""
    from sentence_transformers import SentenceTransformer
    model = SentenceTransformer('all-MiniLM-L6-v2')

    emb1 = model.encode(example.answer)
    emb2 = model.encode(pred.answer)

    similarity = cosine_similarity(emb1, emb2)
    return similarity
```

### Multi-Factor Metrics

```python
def comprehensive_metric(example, pred, trace=None):
    """Combine multiple factors."""
    score = 0.0

    # Correctness (50%)
    if example.answer.lower() in pred.answer.lower():
        score += 0.5

    # Conciseness (25%)
    if len(pred.answer.split()) <= 20:
        score += 0.25

    # Citation (25%)
    if "source:" in pred.answer.lower():
        score += 0.25

    return score
```

### Using Trace for Debugging

```python
def metric_with_trace(example, pred, trace=None):
    """Metric that uses trace for debugging."""
    is_correct = example.answer == pred.answer

    if trace is not None and not is_correct:
        # Log failures for analysis
        print(f"Failed on: {example.question}")
        print(f"Expected: {example.answer}")
        print(f"Got: {pred.answer}")

    return is_correct
```

## Evaluation Best Practices

### Train/Val/Test Split

```python
# Split data
trainset = data[:100]   # 70%
valset = data[100:120]  # 15%
testset = data[120:]    # 15%

# Optimize on train
optimized = optimizer.compile(module, trainset=trainset)

# Validate during optimization (for MIPRO)
optimized = optimizer.compile(module, trainset=trainset, valset=valset)

# Evaluate on test
from dspy.evaluate import Evaluate
evaluator = Evaluate(devset=testset, metric=metric)
score = evaluator(optimized)
```

### Cross-Validation

```python
from sklearn.model_selection import KFold

kfold = KFold(n_splits=5)
scores = []

for train_idx, val_idx in kfold.split(data):
    trainset = [data[i] for i in train_idx]
    valset = [data[i] for i in val_idx]

    optimized = optimizer.compile(module, trainset=trainset)
    score = evaluator(optimized, devset=valset)
    scores.append(score)

print(f"Average score: {sum(scores) / len(scores):.2f}")
```

### Comparing Optimizers

```python
results = {}

for opt_name, optimizer in [
    ("baseline", None),
    ("fewshot", BootstrapFewShot(metric=metric)),
    ("mipro", MIPRO(metric=metric)),
]:
    if optimizer is None:
        module_opt = module
    else:
        module_opt = optimizer.compile(module, trainset=trainset)

    score = evaluator(module_opt, devset=testset)
    results[opt_name] = score

print(results)
# {'baseline': 0.65, 'fewshot': 0.78, 'mipro': 0.85}
```

## Advanced Patterns

### Custom Optimizer

```python
from dspy.teleprompt import Teleprompter

class CustomOptimizer(Teleprompter):
    def __init__(self, metric):
        self.metric = metric

    def compile(self, student, trainset, **kwargs):
        # Your optimization logic here
        # Return optimized student module
        return student
```

### Multi-Stage Optimization

```python
# Stage 1: Bootstrap few-shot
stage1 = BootstrapFewShot(metric=metric, max_bootstrapped_demos=3)
optimized1 = stage1.compile(module, trainset=trainset)

# Stage 2: Instruction tuning
stage2 = MIPRO(metric=metric, num_candidates=10)
optimized2 = stage2.compile(optimized1, trainset=trainset, valset=valset)

# Final optimized module
final_module = optimized2
```

### Ensemble Optimization

```python
class EnsembleModule(dspy.Module):
    def __init__(self, modules):
        super().__init__()
        self.modules = modules

    def forward(self, question):
        predictions = [m(question=question).answer for m in self.modules]
        # Vote or average
        return dspy.Prediction(answer=max(set(predictions), key=predictions.count))

# Optimize multiple modules
opt1 = BootstrapFewShot(metric=metric).compile(module, trainset=trainset)
opt2 = MIPRO(metric=metric).compile(module, trainset=trainset)
opt3 = COPRO(metric=metric).compile(module, trainset=trainset)

# Ensemble
ensemble = EnsembleModule([opt1, opt2, opt3])
```

## Optimization Workflow

### 1. Start with Baseline

```python
# No optimization
baseline = dspy.ChainOfThought("question -> answer")
baseline_score = evaluator(baseline, devset=testset)
print(f"Baseline: {baseline_score}")
```

### 2. Try BootstrapFewShot

```python
# Quick optimization
fewshot = BootstrapFewShot(metric=metric, max_bootstrapped_demos=3)
optimized = fewshot.compile(baseline, trainset=trainset)
fewshot_score = evaluator(optimized, devset=testset)
print(f"Few-shot: {fewshot_score} (+{fewshot_score - baseline_score:.2f})")
```

### 3. If More Data Available, Try MIPRO

```python
# State-of-the-art optimization
mipro = MIPRO(metric=metric, num_candidates=10)
optimized_mipro = mipro.compile(baseline, trainset=trainset, valset=valset)
mipro_score = evaluator(optimized_mipro, devset=testset)
print(f"MIPRO: {mipro_score} (+{mipro_score - baseline_score:.2f})")
```

### 4. Save Best Model

```python
if mipro_score > fewshot_score:
    optimized_mipro.save("models/best_model.json")
else:
    optimized.save("models/best_model.json")
```

## Common Pitfalls

### 1. Overfitting to Training Data

```python
# ❌ Bad: Too many demos
optimizer = BootstrapFewShot(max_bootstrapped_demos=20)  # Overfits!

# ✅ Good: Moderate demos
optimizer = BootstrapFewShot(max_bootstrapped_demos=3-5)
```

### 2. Metric Doesn't Match Task

```python
# ❌ Bad: Binary metric for nuanced task
def bad_metric(example, pred, trace=None):
    return example.answer == pred.answer  # Too strict!

# ✅ Good: Graded metric
def good_metric(example, pred, trace=None):
    return f1_score(example.answer, pred.answer)  # Allows partial credit
```

### 3. Insufficient Training Data

```python
# ❌ Bad: Too little data
trainset = data[:5]  # Not enough!

# ✅ Good: Sufficient data
trainset = data[:50]  # Better
```

### 4. No Validation Set

```python
# ❌ Bad: Optimizing on test set
optimizer.compile(module, trainset=testset)  # Cheating!

# ✅ Good: Proper splits
optimizer.compile(module, trainset=trainset, valset=valset)
evaluator(optimized, devset=testset)
```

## Performance Tips

1. **Start simple**: BootstrapFewShot first
2. **Use representative data**: Cover edge cases
3. **Monitor overfitting**: Validate on held-out set
4. **Iterate metrics**: Refine based on failures
5. **Save checkpoints**: Don't lose progress
6. **Compare to baseline**: Measure improvement
7. **Test multiple optimizers**: Find best fit

## Resources

- **Paper**: "DSPy: Compiling Declarative Language Model Calls into Self-Improving Pipelines"
- **GitHub**: https://github.com/stanfordnlp/dspy
- **Discord**: https://discord.gg/XCGy2WDCQB




### Examples

# DSPy Real-World Examples

Practical examples of building production systems with DSPy.

## Table of Contents
- RAG Systems
- Agent Systems
- Classification
- Data Processing
- Multi-Stage Pipelines

## RAG Systems

### Basic RAG

```python
import dspy

class BasicRAG(dspy.Module):
    def __init__(self, num_passages=3):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=num_passages)
        self.generate = dspy.ChainOfThought("context, question -> answer")

    def forward(self, question):
        passages = self.retrieve(question).passages
        context = "\n\n".join(passages)
        return self.generate(context=context, question=question)

# Configure retriever (example with Chroma)
from dspy.retrieve.chromadb_rm import ChromadbRM

retriever = ChromadbRM(
    collection_name="my_docs",
    persist_directory="./chroma_db",
    k=3
)
dspy.settings.configure(rm=retriever)

# Use RAG
rag = BasicRAG()
result = rag(question="What is DSPy?")
print(result.answer)
```

### Optimized RAG

```python
from dspy.teleprompt import BootstrapFewShot

# Training data with question-answer pairs
trainset = [
    dspy.Example(
        question="What is retrieval augmented generation?",
        answer="RAG combines retrieval of relevant documents with generation..."
    ).with_inputs("question"),
    # ... more examples
]

# Define metric
def answer_correctness(example, pred, trace=None):
    # Check if answer contains key information
    return example.answer.lower() in pred.answer.lower()

# Optimize RAG
optimizer = BootstrapFewShot(metric=answer_correctness)
optimized_rag = optimizer.compile(rag, trainset=trainset)

# Optimized RAG performs better on similar questions
result = optimized_rag(question="Explain RAG systems")
```

### Multi-Hop RAG

```python
class MultiHopRAG(dspy.Module):
    """RAG that follows chains of reasoning across documents."""

    def __init__(self):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=3)
        self.generate_query = dspy.ChainOfThought("question -> search_query")
        self.generate_answer = dspy.ChainOfThought("context, question -> answer")

    def forward(self, question):
        # First retrieval
        query1 = self.generate_query(question=question).search_query
        passages1 = self.retrieve(query1).passages

        # Generate follow-up query based on first results
        context1 = "\n".join(passages1)
        query2 = self.generate_query(
            question=f"Based on: {context1}\nFollow-up: {question}"
        ).search_query

        # Second retrieval
        passages2 = self.retrieve(query2).passages

        # Combine all context
        all_context = "\n\n".join(passages1 + passages2)

        # Generate final answer
        return self.generate_answer(context=all_context, question=question)

# Use multi-hop RAG
multi_rag = MultiHopRAG()
result = multi_rag(question="Who wrote the book that inspired Blade Runner?")
# Hop 1: Find "Blade Runner was based on..."
# Hop 2: Find author of that book
```

### RAG with Reranking

```python
class RerankedRAG(dspy.Module):
    """RAG with learned reranking of retrieved passages."""

    def __init__(self):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=10)  # Get more candidates
        self.rerank = dspy.Predict("question, passage -> relevance_score: float")
        self.answer = dspy.ChainOfThought("context, question -> answer")

    def forward(self, question):
        # Retrieve candidates
        passages = self.retrieve(question).passages

        # Rerank passages
        scored_passages = []
        for passage in passages:
            score = float(self.rerank(
                question=question,
                passage=passage
            ).relevance_score)
            scored_passages.append((score, passage))

        # Take top 3 after reranking
        top_passages = [p for _, p in sorted(scored_passages, reverse=True)[:3]]
        context = "\n\n".join(top_passages)

        # Generate answer from reranked context
        return self.answer(context=context, question=question)
```

## Agent Systems

### ReAct Agent

```python
from dspy.predict import ReAct

# Define tools
def search_wikipedia(query: str) -> str:
    """Search Wikipedia for information."""
    import wikipedia
    try:
        return wikipedia.summary(query, sentences=3)
    except:
        return "No results found"

def calculate(expression: str) -> str:
    """Evaluate mathematical expression safely."""
    try:
        # Use safe eval
        result = eval(expression, {"__builtins__": {}}, {})
        return str(result)
    except:
        return "Invalid expression"

def search_web(query: str) -> str:
    """Search the web."""
    # Your web search implementation
    return results

# Create agent signature
class ResearchAgent(dspy.Signature):
    """Answer questions using available tools."""
    question = dspy.InputField()
    answer = dspy.OutputField()

# Create ReAct agent
agent = ReAct(ResearchAgent, tools=[search_wikipedia, calculate, search_web])

# Agent decides which tools to use
result = agent(question="What is the population of France divided by 10?")
# Agent:
# 1. Thinks: "Need population of France"
# 2. Acts: search_wikipedia("France population")
# 3. Thinks: "Got 67 million, need to divide"
# 4. Acts: calculate("67000000 / 10")
# 5. Returns: "6,700,000"
```

### Multi-Agent System

```python
class MultiAgentSystem(dspy.Module):
    """System with specialized agents for different tasks."""

    def __init__(self):
        super().__init__()

        # Router agent
        self.router = dspy.Predict("question -> agent_type: str")

        # Specialized agents
        self.research_agent = ReAct(
            ResearchAgent,
            tools=[search_wikipedia, search_web]
        )
        self.math_agent = dspy.ProgramOfThought("problem -> answer")
        self.reasoning_agent = dspy.ChainOfThought("question -> answer")

    def forward(self, question):
        # Route to appropriate agent
        agent_type = self.router(question=question).agent_type

        if agent_type == "research":
            return self.research_agent(question=question)
        elif agent_type == "math":
            return self.math_agent(problem=question)
        else:
            return self.reasoning_agent(question=question)

# Use multi-agent system
mas = MultiAgentSystem()
result = mas(question="What is 15% of the GDP of France?")
# Routes to research_agent for GDP, then to math_agent for calculation
```

## Classification

### Binary Classifier

```python
class SentimentClassifier(dspy.Module):
    def __init__(self):
        super().__init__()
        self.classify = dspy.Predict("text -> sentiment: str")

    def forward(self, text):
        return self.classify(text=text)

# Training data
trainset = [
    dspy.Example(text="I love this!", sentiment="positive").with_inputs("text"),
    dspy.Example(text="Terrible experience", sentiment="negative").with_inputs("text"),
    # ... more examples
]

# Optimize
def accuracy(example, pred, trace=None):
    return example.sentiment == pred.sentiment

optimizer = BootstrapFewShot(metric=accuracy, max_bootstrapped_demos=5)
classifier = SentimentClassifier()
optimized_classifier = optimizer.compile(classifier, trainset=trainset)

# Use classifier
result = optimized_classifier(text="This product is amazing!")
print(result.sentiment)  # "positive"
```

### Multi-Class Classifier

```python
class TopicClassifier(dspy.Module):
    def __init__(self):
        super().__init__()
        self.classify = dspy.ChainOfThought(
            "text -> category: str, confidence: float"
        )

    def forward(self, text):
        result = self.classify(text=text)
        return dspy.Prediction(
            category=result.category,
            confidence=float(result.confidence)
        )

# Define categories in signature
class TopicSignature(dspy.Signature):
    """Classify text into one of: technology, sports, politics, entertainment."""
    text = dspy.InputField()
    category = dspy.OutputField(desc="one of: technology, sports, politics, entertainment")
    confidence = dspy.OutputField(desc="0.0 to 1.0")

classifier = dspy.ChainOfThought(TopicSignature)
result = classifier(text="The Lakers won the championship")
print(result.category)  # "sports"
print(result.confidence)  # 0.95
```

### Hierarchical Classifier

```python
class HierarchicalClassifier(dspy.Module):
    """Two-stage classification: coarse then fine-grained."""

    def __init__(self):
        super().__init__()
        self.coarse = dspy.Predict("text -> broad_category: str")
        self.fine_tech = dspy.Predict("text -> tech_subcategory: str")
        self.fine_sports = dspy.Predict("text -> sports_subcategory: str")

    def forward(self, text):
        # Stage 1: Broad category
        broad = self.coarse(text=text).broad_category

        # Stage 2: Fine-grained based on broad
        if broad == "technology":
            fine = self.fine_tech(text=text).tech_subcategory
        elif broad == "sports":
            fine = self.fine_sports(text=text).sports_subcategory
        else:
            fine = "other"

        return dspy.Prediction(broad_category=broad, fine_category=fine)
```

## Data Processing

### Text Summarization

```python
class AdaptiveSummarizer(dspy.Module):
    """Summarizes text to target length."""

    def __init__(self):
        super().__init__()
        self.summarize = dspy.ChainOfThought("text, target_length -> summary")

    def forward(self, text, target_length="3 sentences"):
        return self.summarize(text=text, target_length=target_length)

# Use summarizer
summarizer = AdaptiveSummarizer()
long_text = "..." # Long article

short_summary = summarizer(long_text, target_length="1 sentence")
medium_summary = summarizer(long_text, target_length="3 sentences")
detailed_summary = summarizer(long_text, target_length="1 paragraph")
```

### Information Extraction

```python
from pydantic import BaseModel, Field

class PersonInfo(BaseModel):
    name: str = Field(description="Full name")
    age: int = Field(description="Age in years")
    occupation: str = Field(description="Job title")
    location: str = Field(description="City and country")

class ExtractPerson(dspy.Signature):
    """Extract person information from text."""
    text = dspy.InputField()
    person: PersonInfo = dspy.OutputField()

extractor = dspy.TypedPredictor(ExtractPerson)

text = "Dr. Jane Smith, 42, is a neuroscientist at Stanford University in Palo Alto, California."
result = extractor(text=text)

print(result.person.name)       # "Dr. Jane Smith"
print(result.person.age)        # 42
print(result.person.occupation) # "neuroscientist"
print(result.person.location)   # "Palo Alto, California"
```

### Batch Processing

```python
class BatchProcessor(dspy.Module):
    """Process large datasets efficiently."""

    def __init__(self):
        super().__init__()
        self.process = dspy.Predict("text -> processed_text")

    def forward(self, texts):
        # Batch processing for efficiency
        return self.process.batch([{"text": t} for t in texts])

# Process 1000 documents
processor = BatchProcessor()
results = processor(texts=large_dataset)

# Results are returned in order
for original, result in zip(large_dataset, results):
    print(f"{original} -> {result.processed_text}")
```

## Multi-Stage Pipelines

### Document Processing Pipeline

```python
class DocumentPipeline(dspy.Module):
    """Multi-stage document processing."""

    def __init__(self):
        super().__init__()
        self.extract = dspy.Predict("document -> key_points")
        self.classify = dspy.Predict("key_points -> category")
        self.summarize = dspy.ChainOfThought("key_points, category -> summary")
        self.tag = dspy.Predict("summary -> tags")

    def forward(self, document):
        # Stage 1: Extract key points
        key_points = self.extract(document=document).key_points

        # Stage 2: Classify
        category = self.classify(key_points=key_points).category

        # Stage 3: Summarize
        summary = self.summarize(
            key_points=key_points,
            category=category
        ).summary

        # Stage 4: Generate tags
        tags = self.tag(summary=summary).tags

        return dspy.Prediction(
            key_points=key_points,
            category=category,
            summary=summary,
            tags=tags
        )
```

### Quality Control Pipeline

```python
class QualityControlPipeline(dspy.Module):
    """Generate output and verify quality."""

    def __init__(self):
        super().__init__()
        self.generate = dspy.ChainOfThought("prompt -> output")
        self.verify = dspy.Predict("output -> is_valid: bool, issues: str")
        self.improve = dspy.ChainOfThought("output, issues -> improved_output")

    def forward(self, prompt, max_iterations=3):
        output = self.generate(prompt=prompt).output

        for _ in range(max_iterations):
            # Verify output
            verification = self.verify(output=output)

            if verification.is_valid:
                return dspy.Prediction(output=output, iterations=_ + 1)

            # Improve based on issues
            output = self.improve(
                output=output,
                issues=verification.issues
            ).improved_output

        return dspy.Prediction(output=output, iterations=max_iterations)
```

## Production Tips

### 1. Caching for Performance

```python
from functools import lru_cache

class CachedRAG(dspy.Module):
    def __init__(self):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=3)
        self.generate = dspy.ChainOfThought("context, question -> answer")

    @lru_cache(maxsize=1000)
    def forward(self, question):
        passages = self.retrieve(question).passages
        context = "\n".join(passages)
        return self.generate(context=context, question=question).answer
```

### 2. Error Handling

```python
class RobustModule(dspy.Module):
    def __init__(self):
        super().__init__()
        self.process = dspy.ChainOfThought("input -> output")

    def forward(self, input):
        try:
            result = self.process(input=input)
            return result
        except Exception as e:
            # Log error
            print(f"Error processing {input}: {e}")
            # Return fallback
            return dspy.Prediction(output="Error: could not process input")
```

### 3. Monitoring

```python
class MonitoredModule(dspy.Module):
    def __init__(self):
        super().__init__()
        self.process = dspy.ChainOfThought("input -> output")
        self.call_count = 0
        self.errors = 0

    def forward(self, input):
        self.call_count += 1

        try:
            result = self.process(input=input)
            return result
        except Exception as e:
            self.errors += 1
            raise

    def get_stats(self):
        return {
            "calls": self.call_count,
            "errors": self.errors,
            "error_rate": self.errors / max(self.call_count, 1)
        }
```

### 4. A/B Testing

```python
class ABTestModule(dspy.Module):
    """Run two variants and compare."""

    def __init__(self, variant_a, variant_b):
        super().__init__()
        self.variant_a = variant_a
        self.variant_b = variant_b
        self.a_calls = 0
        self.b_calls = 0

    def forward(self, input, variant="a"):
        if variant == "a":
            self.a_calls += 1
            return self.variant_a(input=input)
        else:
            self.b_calls += 1
            return self.variant_b(input=input)

# Compare two optimizers
baseline = dspy.ChainOfThought("question -> answer")
optimized = BootstrapFewShot(...).compile(baseline, trainset=trainset)

ab_test = ABTestModule(variant_a=baseline, variant_b=optimized)

# Route 50% to each
import random
variant = "a" if random.random() < 0.5 else "b"
result = ab_test(input=question, variant=variant)
```

## Complete Example: Customer Support Bot

```python
import dspy
from dspy.teleprompt import BootstrapFewShot

class CustomerSupportBot(dspy.Module):
    """Complete customer support system."""

    def __init__(self):
        super().__init__()

        # Classify intent
        self.classify_intent = dspy.Predict("message -> intent: str")

        # Specialized handlers
        self.technical_handler = dspy.ChainOfThought("message, history -> response")
        self.billing_handler = dspy.ChainOfThought("message, history -> response")
        self.general_handler = dspy.Predict("message, history -> response")

        # Retrieve relevant docs
        self.retrieve = dspy.Retrieve(k=3)

        # Conversation history
        self.history = []

    def forward(self, message):
        # Classify intent
        intent = self.classify_intent(message=message).intent

        # Retrieve relevant documentation
        docs = self.retrieve(message).passages
        context = "\n".join(docs)

        # Add context to history
        history_str = "\n".join(self.history)
        full_message = f"Context: {context}\n\nMessage: {message}"

        # Route to appropriate handler
        if intent == "technical":
            response = self.technical_handler(
                message=full_message,
                history=history_str
            ).response
        elif intent == "billing":
            response = self.billing_handler(
                message=full_message,
                history=history_str
            ).response
        else:
            response = self.general_handler(
                message=full_message,
                history=history_str
            ).response

        # Update history
        self.history.append(f"User: {message}")
        self.history.append(f"Bot: {response}")

        return dspy.Prediction(response=response, intent=intent)

# Training data
trainset = [
    dspy.Example(
        message="My account isn't working",
        intent="technical",
        response="I'd be happy to help. What error are you seeing?"
    ).with_inputs("message"),
    # ... more examples
]

# Define metric
def response_quality(example, pred, trace=None):
    # Check if response is helpful
    if len(pred.response) < 20:
        return 0.0
    if example.intent != pred.intent:
        return 0.3
    return 1.0

# Optimize
optimizer = BootstrapFewShot(metric=response_quality)
bot = CustomerSupportBot()
optimized_bot = optimizer.compile(bot, trainset=trainset)

# Use in production
optimized_bot.save("models/support_bot_v1.json")

# Later, load and use
loaded_bot = CustomerSupportBot()
loaded_bot.load("models/support_bot_v1.json")
response = loaded_bot(message="I can't log in")
```

## Resources

- **Documentation**: https://dspy.ai
- **Examples Repo**: https://github.com/stanfordnlp/dspy/tree/main/examples
- **Discord**: https://discord.gg/XCGy2WDCQB




---

## 🚀 Usage

**Reference this template:** `@skill-dspy.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
