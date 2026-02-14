---
id: skill-guidance
type: skill
name: guidance
description: Control LLM output with regex and grammars, guarantee valid JSON/XML/code
  generation, enforce structured formats, and build multi-step workflows with Guidance
  - Microsoft Research's constrained generation framework
category: ai-research
complexity: medium
keywords:
- api
- github
- performance
- python
- react
- sql
capabilities: []
token_estimate: 2360
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~2,360 -->
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




# guidance

> Control LLM output with regex and grammars, guarantee valid JSON/XML/code generation, enforce structured formats, and build multi-step workflows with Guidance - Microsoft Research's constrained generation framework

# Guidance: Constrained LLM Generation

## When to Use This Skill

Use Guidance when you need to:
- **Control LLM output syntax** with regex or grammars
- **Guarantee valid JSON/XML/code** generation
- **Reduce latency** vs traditional prompting approaches
- **Enforce structured formats** (dates, emails, IDs, etc.)
- **Build multi-step workflows** with Pythonic control flow
- **Prevent invalid outputs** through grammatical constraints

**GitHub Stars**: 18,000+ | **From**: Microsoft Research

## Installation

```bash
# Base installation
pip install guidance

# With specific backends
pip install guidance[transformers]  # Hugging Face models
pip install guidance[llama_cpp]     # llama.cpp models
```

## Quick Start

### Basic Example: Structured Generation

```python
from guidance import models, gen

# Load model (supports OpenAI, Transformers, llama.cpp)
lm = models.OpenAI("gpt-4")

# Generate with constraints
result = lm + "The capital of France is " + gen("capital", max_tokens=5)

print(result["capital"])  # "Paris"
```

### With Anthropic Claude

```python
from guidance import models, gen, system, user, assistant

# Configure Claude
lm = models.Anthropic("claude-sonnet-4-5-20250929")

# Use context managers for chat format
with system():
    lm += "You are a helpful assistant."

with user():
    lm += "What is the capital of France?"

with assistant():
    lm += gen(max_tokens=20)
```

## Core Concepts

### 1. Context Managers

Guidance uses Pythonic context managers for chat-style interactions.

```python
from guidance import system, user, assistant, gen

lm = models.Anthropic("claude-sonnet-4-5-20250929")

# System message
with system():
    lm += "You are a JSON generation expert."

# User message
with user():
    lm += "Generate a person object with name and age."

# Assistant response
with assistant():
    lm += gen("response", max_tokens=100)

print(lm["response"])
```

**Benefits:**
- Natural chat flow
- Clear role separation
- Easy to read and maintain

### 2. Constrained Generation

Guidance ensures outputs match specified patterns using regex or grammars.

#### Regex Constraints

```python
from guidance import models, gen

lm = models.Anthropic("claude-sonnet-4-5-20250929")

# Constrain to valid email format
lm += "Email: " + gen("email", regex=r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}")

# Constrain to date format (YYYY-MM-DD)
lm += "Date: " + gen("date", regex=r"\d{4}-\d{2}-\d{2}")

# Constrain to phone number
lm += "Phone: " + gen("phone", regex=r"\d{3}-\d{3}-\d{4}")

print(lm["email"])  # Guaranteed valid email
print(lm["date"])   # Guaranteed YYYY-MM-DD format
```

**How it works:**
- Regex converted to grammar at token level
- Invalid tokens filtered during generation
- Model can only produce matching outputs

#### Selection Constraints

```python
from guidance import models, gen, select

lm = models.Anthropic("claude-sonnet-4-5-20250929")

# Constrain to specific choices
lm += "Sentiment: " + select(["positive", "negative", "neutral"], name="sentiment")

# Multiple-choice selection
lm += "Best answer: " + select(
    ["A) Paris", "B) London", "C) Berlin", "D) Madrid"],
    name="answer"
)

print(lm["sentiment"])  # One of: positive, negative, neutral
print(lm["answer"])     # One of: A, B, C, or D
```

### 3. Token Healing

Guidance automatically "heals" token boundaries between prompt and generation.

**Problem:** Tokenization creates unnatural boundaries.

```python
# Without token healing
prompt = "The capital of France is "
# Last token: " is "
# First generated token might be " Par" (with leading space)
# Result: "The capital of France is  Paris" (double space!)
```

**Solution:** Guidance backs up one token and regenerates.

```python
from guidance import models, gen

lm = models.Anthropic("claude-sonnet-4-5-20250929")

# Token healing enabled by default
lm += "The capital of France is " + gen("capital", max_tokens=5)
# Result: "The capital of France is Paris" (correct spacing)
```

**Benefits:**
- Natural text boundaries
- No awkward spacing issues
- Better model performance (sees natural token sequences)

### 4. Grammar-Based Generation

Define complex structures using context-free grammars.

```python
from guidance import models, gen

lm = models.Anthropic("claude-sonnet-4-5-20250929")

# JSON grammar (simplified)
json_grammar = """
{
    "name": <gen name regex="[A-Za-z ]+" max_tokens=20>,
    "age": <gen age regex="[0-9]+" max_tokens=3>,
    "email": <gen email regex="[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}" max_tokens=50>
}
"""

# Generate valid JSON
lm += gen("person", grammar=json_grammar)

print(lm["person"])  # Guaranteed valid JSON structure
```

**Use cases:**
- Complex structured outputs
- Nested data structures
- Programming language syntax
- Domain-specific languages

### 5. Guidance Functions

Create reusable generation patterns with the `@guidance` decorator.

```python
from guidance import guidance, gen, models

@guidance
def generate_person(lm):
    """Generate a person with name and age."""
    lm += "Name: " + gen("name", max_tokens=20, stop="\n")
    lm += "\nAge: " + gen("age", regex=r"[0-9]+", max_tokens=3)
    return lm

# Use the function
lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = generate_person(lm)

print(lm["name"])
print(lm["age"])
```

**Stateful Functions:**

```python
@guidance(stateless=False)
def react_agent(lm, question, tools, max_rounds=5):
    """ReAct agent with tool use."""
    lm += f"Question: {question}\n\n"

    for i in range(max_rounds):
        # Thought
        lm += f"Thought {i+1}: " + gen("thought", stop="\n")

        # Action
        lm += "\nAction: " + select(list(tools.keys()), name="action")

        # Execute tool
        tool_result = tools[lm["action"]]()
        lm += f"\nObservation: {tool_result}\n\n"

        # Check if done
        lm += "Done? " + select(["Yes", "No"], name="done")
        if lm["done"] == "Yes":
            break

    # Final answer
    lm += "\nFinal Answer: " + gen("answer", max_tokens=100)
    return lm
```

## Backend Configuration

### Anthropic Claude

```python
from guidance import models

lm = models.Anthropic(
    model="claude-sonnet-4-5-20250929",
    api_key="your-api-key"  # Or set ANTHROPIC_API_KEY env var
)
```

### OpenAI

```python
lm = models.OpenAI(
    model="gpt-4o-mini",
    api_key="your-api-key"  # Or set OPENAI_API_KEY env var
)
```

### Local Models (Transformers)

```python
from guidance.models import Transformers

lm = Transformers(
    "microsoft/Phi-4-mini-instruct",
    device="cuda"  # Or "cpu"
)
```

### Local Models (llama.cpp)

```python
from guidance.models import LlamaCpp

lm = LlamaCpp(
    model_path="/path/to/model.gguf",
    n_ctx=4096,
    n_gpu_layers=35
)
```

## Common Patterns

### Pattern 1: JSON Generation

```python
from guidance import models, gen, system, user, assistant

lm = models.Anthropic("claude-sonnet-4-5-20250929")

with system():
    lm += "You generate valid JSON."

with user():
    lm += "Generate a user profile with name, age, and email."

with assistant():
    lm += """{
    "name": """ + gen("name", regex=r'"[A-Za-z ]+"', max_tokens=30) + """,
    "age": """ + gen("age", regex=r"[0-9]+", max_tokens=3) + """,
    "email": """ + gen("email", regex=r'"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"', max_tokens=50) + """
}"""

print(lm)  # Valid JSON guaranteed
```

### Pattern 2: Classification

```python
from guidance import models, gen, select

lm = models.Anthropic("claude-sonnet-4-5-20250929")

text = "This product is amazing! I love it."

lm += f"Text: {text}\n"
lm += "Sentiment: " + select(["positive", "negative", "neutral"], name="sentiment")
lm += "\nConfidence: " + gen("confidence", regex=r"[0-9]+", max_tokens=3) + "%"

print(f"Sentiment: {lm['sentiment']}")
print(f"Confidence: {lm['confidence']}%")
```

### Pattern 3: Multi-Step Reasoning

```python
from guidance import models, gen, guidance

@guidance
def chain_of_thought(lm, question):
    """Generate answer with step-by-step reasoning."""
    lm += f"Question: {question}\n\n"

    # Generate multiple reasoning steps
    for i in range(3):
        lm += f"Step {i+1}: " + gen(f"step_{i+1}", stop="\n", max_tokens=100) + "\n"

    # Final answer
    lm += "\nTherefore, the answer is: " + gen("answer", max_tokens=50)

    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = chain_of_thought(lm, "What is 15% of 200?")

print(lm["answer"])
```

### Pattern 4: ReAct Agent

```python
from guidance import models, gen, select, guidance

@guidance(stateless=False)
def react_agent(lm, question):
    """ReAct agent with tool use."""
    tools = {
        "calculator": lambda expr: eval(expr),
        "search": lambda query: f"Search results for: {query}",
    }

    lm += f"Question: {question}\n\n"

    for round in range(5):
        # Thought
        lm += f"Thought: " + gen("thought", stop="\n") + "\n"

        # Action selection
        lm += "Action: " + select(["calculator", "search", "answer"], name="action")

        if lm["action"] == "answer":
            lm += "\nFinal Answer: " + gen("answer", max_tokens=100)
            break

        # Action input
        lm += "\nAction Input: " + gen("action_input", stop="\n") + "\n"

        # Execute tool
        if lm["action"] in tools:
            result = tools[lm["action"]](lm["action_input"])
            lm += f"Observation: {result}\n\n"

    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = react_agent(lm, "What is 25 * 4 + 10?")
print(lm["answer"])
```

### Pattern 5: Data Extraction

```python
from guidance import models, gen, guidance

@guidance
def extract_entities(lm, text):
    """Extract structured entities from text."""
    lm += f"Text: {text}\n\n"

    # Extract person
    lm += "Person: " + gen("person", stop="\n", max_tokens=30) + "\n"

    # Extract organization
    lm += "Organization: " + gen("organization", stop="\n", max_tokens=30) + "\n"

    # Extract date
    lm += "Date: " + gen("date", regex=r"\d{4}-\d{2}-\d{2}", max_tokens=10) + "\n"

    # Extract location
    lm += "Location: " + gen("location", stop="\n", max_tokens=30) + "\n"

    return lm

text = "Tim Cook announced at Apple Park on 2024-09-15 in Cupertino."

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = extract_entities(lm, text)

print(f"Person: {lm['person']}")
print(f"Organization: {lm['organization']}")
print(f"Date: {lm['date']}")
print(f"Location: {lm['location']}")
```

## Best Practices

### 1. Use Regex for Format Validation

```python
# ✅ Good: Regex ensures valid format
lm += "Email: " + gen("email", regex=r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}")

# ❌ Bad: Free generation may produce invalid emails
lm += "Email: " + gen("email", max_tokens=50)
```

### 2. Use select() for Fixed Categories

```python
# ✅ Good: Guaranteed valid category
lm += "Status: " + select(["pending", "approved", "rejected"], name="status")

# ❌ Bad: May generate typos or invalid values
lm += "Status: " + gen("status", max_tokens=20)
```

### 3. Leverage Token Healing

```python
# Token healing is enabled by default
# No special action needed - just concatenate naturally
lm += "The capital is " + gen("capital")  # Automatic healing
```

### 4. Use stop Sequences

```python
# ✅ Good: Stop at newline for single-line outputs
lm += "Name: " + gen("name", stop="\n")

# ❌ Bad: May generate multiple lines
lm += "Name: " + gen("name", max_tokens=50)
```

### 5. Create Reusable Functions

```python
# ✅ Good: Reusable pattern
@guidance
def generate_person(lm):
    lm += "Name: " + gen("name", stop="\n")
    lm += "\nAge: " + gen("age", regex=r"[0-9]+")
    return lm

# Use multiple times
lm = generate_person(lm)
lm += "\n\n"
lm = generate_person(lm)
```

### 6. Balance Constraints

```python
# ✅ Good: Reasonable constraints
lm += gen("name", regex=r"[A-Za-z ]+", max_tokens=30)

# ❌ Too strict: May fail or be very slow
lm += gen("name", regex=r"^(John|Jane)$", max_tokens=10)
```

## Comparison to Alternatives

| Feature | Guidance | Instructor | Outlines | LMQL |
|---------|----------|------------|----------|------|
| Regex Constraints | ✅ Yes | ❌ No | ✅ Yes | ✅ Yes |
| Grammar Support | ✅ CFG | ❌ No | ✅ CFG | ✅ CFG |
| Pydantic Validation | ❌ No | ✅ Yes | ✅ Yes | ❌ No |
| Token Healing | ✅ Yes | ❌ No | ✅ Yes | ❌ No |
| Local Models | ✅ Yes | ⚠️ Limited | ✅ Yes | ✅ Yes |
| API Models | ✅ Yes | ✅ Yes | ⚠️ Limited | ✅ Yes |
| Pythonic Syntax | ✅ Yes | ✅ Yes | ✅ Yes | ❌ SQL-like |
| Learning Curve | Low | Low | Medium | High |

**When to choose Guidance:**
- Need regex/grammar constraints
- Want token healing
- Building complex workflows with control flow
- Using local models (Transformers, llama.cpp)
- Prefer Pythonic syntax

**When to choose alternatives:**
- Instructor: Need Pydantic validation with automatic retrying
- Outlines: Need JSON schema validation
- LMQL: Prefer declarative query syntax

## Performance Characteristics

**Latency Reduction:**
- 30-50% faster than traditional prompting for constrained outputs
- Token healing reduces unnecessary regeneration
- Grammar constraints prevent invalid token generation

**Memory Usage:**
- Minimal overhead vs unconstrained generation
- Grammar compilation cached after first use
- Efficient token filtering at inference time

**Token Efficiency:**
- Prevents wasted tokens on invalid outputs
- No need for retry loops
- Direct path to valid outputs

## Resources

- **Documentation**: https://guidance.readthedocs.io
- **GitHub**: https://github.com/guidance-ai/guidance (18k+ stars)
- **Notebooks**: https://github.com/guidance-ai/guidance/tree/main/notebooks
- **Discord**: Community support available

## See Also

- `references/constraints.md` - Comprehensive regex and grammar patterns
- `references/backends.md` - Backend-specific configuration
- `references/examples.md` - Production-ready examples




---


## 📚 Reference Materials


### Constraints

# Comprehensive Constraint Patterns

Guide to regex constraints, grammar-based generation, and token healing in Guidance.

## Table of Contents
- Regex Constraints
- Grammar-Based Generation
- Token Healing
- Selection Constraints
- Complex Patterns
- Performance Optimization

## Regex Constraints

### Basic Patterns

#### Numeric Constraints

```python
from guidance import models, gen

lm = models.Anthropic("claude-sonnet-4-5-20250929")

# Integer (positive)
lm += "Age: " + gen("age", regex=r"[0-9]+")

# Integer (with negatives)
lm += "Temperature: " + gen("temp", regex=r"-?[0-9]+")

# Float (positive)
lm += "Price: $" + gen("price", regex=r"[0-9]+\.[0-9]{2}")

# Float (with negatives and optional decimals)
lm += "Value: " + gen("value", regex=r"-?[0-9]+(\.[0-9]+)?")

# Percentage (0-100)
lm += "Progress: " + gen("progress", regex=r"(100|[0-9]{1,2})")

# Range (1-5 stars)
lm += "Rating: " + gen("rating", regex=r"[1-5]") + " stars"
```

#### Text Constraints

```python
# Alphabetic only
lm += "Name: " + gen("name", regex=r"[A-Za-z]+")

# Alphabetic with spaces
lm += "Full Name: " + gen("full_name", regex=r"[A-Za-z ]+")

# Alphanumeric
lm += "Username: " + gen("username", regex=r"[A-Za-z0-9_]+")

# Capitalized words
lm += "Title: " + gen("title", regex=r"[A-Z][a-z]+( [A-Z][a-z]+)*")

# Lowercase only
lm += "Code: " + gen("code", regex=r"[a-z0-9-]+")

# Specific length
lm += "ID: " + gen("id", regex=r"[A-Z]{3}-[0-9]{6}")  # e.g., "ABC-123456"
```

#### Date and Time Constraints

```python
# Date (YYYY-MM-DD)
lm += "Date: " + gen("date", regex=r"\d{4}-\d{2}-\d{2}")

# Date (MM/DD/YYYY)
lm += "Date: " + gen("date_us", regex=r"\d{2}/\d{2}/\d{4}")

# Time (HH:MM)
lm += "Time: " + gen("time", regex=r"\d{2}:\d{2}")

# Time (HH:MM:SS)
lm += "Time: " + gen("time_full", regex=r"\d{2}:\d{2}:\d{2}")

# ISO 8601 datetime
lm += "Timestamp: " + gen(
    "timestamp",
    regex=r"\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}Z"
)

# Year (YYYY)
lm += "Year: " + gen("year", regex=r"(19|20)\d{2}")

# Month name
lm += "Month: " + gen(
    "month",
    regex=r"(January|February|March|April|May|June|July|August|September|October|November|December)"
)
```

#### Contact Information

```python
# Email
lm += "Email: " + gen(
    "email",
    regex=r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"
)

# Phone (US format)
lm += "Phone: " + gen("phone", regex=r"\d{3}-\d{3}-\d{4}")

# Phone (international format)
lm += "Phone: " + gen("phone_intl", regex=r"\+[0-9]{1,3}-[0-9]{1,14}")

# ZIP code (US)
lm += "ZIP: " + gen("zip", regex=r"\d{5}(-\d{4})?")

# Postal code (Canada)
lm += "Postal: " + gen("postal", regex=r"[A-Z]\d[A-Z] \d[A-Z]\d")

# URL
lm += "URL: " + gen(
    "url",
    regex=r"https?://[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}(/[a-zA-Z0-9._~:/?#\[\]@!$&'()*+,;=-]*)?"
)
```

### Advanced Patterns

#### JSON Field Constraints

```python
from guidance import models, gen

lm = models.Anthropic("claude-sonnet-4-5-20250929")

# String field with quotes
lm += '"name": ' + gen("name", regex=r'"[A-Za-z ]+"')

# Numeric field (no quotes)
lm += '"age": ' + gen("age", regex=r"[0-9]+")

# Boolean field
lm += '"active": ' + gen("active", regex=r"(true|false)")

# Null field
lm += '"optional": ' + gen("optional", regex=r"(null|[0-9]+)")

# Array of strings
lm += '"tags": [' + gen(
    "tags",
    regex=r'"[a-z]+"(, "[a-z]+")*'
) + ']'

# Complete JSON object
lm += """{
    "name": """ + gen("name", regex=r'"[A-Za-z ]+"') + """,
    "age": """ + gen("age", regex=r"[0-9]+") + """,
    "email": """ + gen(
        "email",
        regex=r'"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"'
    ) + """
}"""
```

#### Code Patterns

```python
# Python variable name
lm += "Variable: " + gen("var", regex=r"[a-z_][a-z0-9_]*")

# Python function name
lm += "Function: " + gen("func", regex=r"[a-z_][a-z0-9_]*")

# Hex color code
lm += "Color: #" + gen("color", regex=r"[0-9A-Fa-f]{6}")

# UUID
lm += "UUID: " + gen(
    "uuid",
    regex=r"[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}"
)

# Git commit hash (short)
lm += "Commit: " + gen("commit", regex=r"[0-9a-f]{7}")

# Semantic version
lm += "Version: " + gen("version", regex=r"[0-9]+\.[0-9]+\.[0-9]+")

# IP address (IPv4)
lm += "IP: " + gen(
    "ip",
    regex=r"((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)"
)
```

#### Domain-Specific Patterns

```python
# Credit card number
lm += "Card: " + gen("card", regex=r"\d{4}-\d{4}-\d{4}-\d{4}")

# Social Security Number (US)
lm += "SSN: " + gen("ssn", regex=r"\d{3}-\d{2}-\d{4}")

# ISBN-13
lm += "ISBN: " + gen("isbn", regex=r"978-\d{1,5}-\d{1,7}-\d{1,7}-\d")

# License plate (US)
lm += "Plate: " + gen("plate", regex=r"[A-Z]{3}-\d{4}")

# Currency amount
lm += "Amount: $" + gen("amount", regex=r"[0-9]{1,3}(,[0-9]{3})*\.[0-9]{2}")

# Percentage with decimal
lm += "Rate: " + gen("rate", regex=r"[0-9]+\.[0-9]{1,2}%")
```

## Grammar-Based Generation

### JSON Grammar

```python
from guidance import models, gen, guidance

@guidance
def json_object(lm):
    """Generate valid JSON object."""
    lm += "{\n"

    # Name field (required)
    lm += '    "name": ' + gen("name", regex=r'"[A-Za-z ]+"') + ",\n"

    # Age field (required)
    lm += '    "age": ' + gen("age", regex=r"[0-9]+") + ",\n"

    # Email field (required)
    lm += '    "email": ' + gen(
        "email",
        regex=r'"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"'
    ) + ",\n"

    # Active field (required, boolean)
    lm += '    "active": ' + gen("active", regex=r"(true|false)") + "\n"

    lm += "}"
    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = json_object(lm)
print(lm)  # Valid JSON guaranteed
```

### Nested JSON Grammar

```python
@guidance
def nested_json(lm):
    """Generate nested JSON structure."""
    lm += "{\n"

    # User object
    lm += '    "user": {\n'
    lm += '        "name": ' + gen("name", regex=r'"[A-Za-z ]+"') + ",\n"
    lm += '        "age": ' + gen("age", regex=r"[0-9]+") + "\n"
    lm += "    },\n"

    # Address object
    lm += '    "address": {\n'
    lm += '        "street": ' + gen("street", regex=r'"[A-Za-z0-9 ]+"') + ",\n"
    lm += '        "city": ' + gen("city", regex=r'"[A-Za-z ]+"') + ",\n"
    lm += '        "zip": ' + gen("zip", regex=r'"\d{5}"') + "\n"
    lm += "    }\n"

    lm += "}"
    return lm
```

### Array Grammar

```python
@guidance
def json_array(lm, count=3):
    """Generate JSON array with fixed count."""
    lm += "[\n"

    for i in range(count):
        lm += "    {\n"
        lm += '        "id": ' + gen(f"id_{i}", regex=r"[0-9]+") + ",\n"
        lm += '        "name": ' + gen(f"name_{i}", regex=r'"[A-Za-z ]+"') + "\n"
        lm += "    }"
        if i < count - 1:
            lm += ","
        lm += "\n"

    lm += "]"
    return lm
```

### XML Grammar

```python
@guidance
def xml_document(lm):
    """Generate valid XML document."""
    lm += '<?xml version="1.0"?>\n'
    lm += "<person>\n"

    # Name element
    lm += "    <name>" + gen("name", regex=r"[A-Za-z ]+") + "</name>\n"

    # Age element
    lm += "    <age>" + gen("age", regex=r"[0-9]+") + "</age>\n"

    # Email element
    lm += "    <email>" + gen(
        "email",
        regex=r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"
    ) + "</email>\n"

    lm += "</person>"
    return lm
```

### CSV Grammar

```python
@guidance
def csv_row(lm):
    """Generate CSV row."""
    lm += gen("name", regex=r"[A-Za-z ]+") + ","
    lm += gen("age", regex=r"[0-9]+") + ","
    lm += gen("email", regex=r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}")
    return lm

@guidance
def csv_document(lm, rows=5):
    """Generate complete CSV."""
    # Header
    lm += "Name,Age,Email\n"

    # Rows
    for i in range(rows):
        lm = csv_row(lm)
        if i < rows - 1:
            lm += "\n"

    return lm
```

## Token Healing

### How Token Healing Works

**Problem:** Tokenization creates unnatural boundaries.

```python
# Example without token healing
prompt = "The capital of France is "
# Tokenization: ["The", " capital", " of", " France", " is", " "]
# Model sees last token: " "
# First generated token might include leading space: " Paris"
# Result: "The capital of France is  Paris" (double space)
```

**Solution:** Guidance backs up and regenerates the last token.

```python
from guidance import models, gen

lm = models.Anthropic("claude-sonnet-4-5-20250929")

# Token healing enabled by default
lm += "The capital of France is " + gen("capital", max_tokens=5)

# Process:
# 1. Back up to token before " is "
# 2. Regenerate " is" + "capital" together
# 3. Result: "The capital of France is Paris" (correct)
```

### Token Healing Examples

#### Natural Continuations

```python
# Before token healing
lm += "The function name is get" + gen("rest")
# Might generate: "The function name is get User" (space before User)

# With token healing
lm += "The function name is get" + gen("rest")
# Generates: "The function name is getUser" (correct camelCase)
```

#### Code Generation

```python
# Function name completion
lm += "def calculate_" + gen("rest", stop="(")
# Token healing ensures smooth connection: "calculate_total"

# Variable name completion
lm += "my_" + gen("var_name", regex=r"[a-z_]+")
# Token healing ensures: "my_variable_name" (not "my_ variable_name")
```

#### Domain-Specific Terms

```python
# Medical terms
lm += "The patient has hyper" + gen("condition")
# Token healing helps: "hypertension" (not "hyper tension")

# Technical terms
lm += "Using micro" + gen("tech")
# Token healing helps: "microservices" (not "micro services")
```

### Disabling Token Healing

```python
# Disable token healing if needed (rare)
lm += gen("text", token_healing=False)
```

## Selection Constraints

### Basic Selection

```python
from guidance import models, select

lm = models.Anthropic("claude-sonnet-4-5-20250929")

# Simple selection
lm += "Status: " + select(["active", "inactive", "pending"], name="status")

# Boolean selection
lm += "Approved: " + select(["Yes", "No"], name="approved")

# Multiple choice
lm += "Answer: " + select(
    ["A) Paris", "B) London", "C) Berlin", "D) Madrid"],
    name="answer"
)
```

### Conditional Selection

```python
from guidance import models, select, gen, guidance

@guidance
def conditional_fields(lm):
    """Generate fields conditionally based on type."""
    lm += "Type: " + select(["person", "company"], name="type")

    if lm["type"] == "person":
        lm += "\nName: " + gen("name", regex=r"[A-Za-z ]+")
        lm += "\nAge: " + gen("age", regex=r"[0-9]+")
    else:
        lm += "\nCompany Name: " + gen("company", regex=r"[A-Za-z ]+")
        lm += "\nEmployees: " + gen("employees", regex=r"[0-9]+")

    return lm
```

### Repeated Selection

```python
@guidance
def multiple_selections(lm):
    """Select multiple items."""
    lm += "Select 3 colors:\n"

    colors = ["red", "blue", "green", "yellow", "purple"]

    for i in range(3):
        lm += f"{i+1}. " + select(colors, name=f"color_{i}") + "\n"

    return lm
```

## Complex Patterns

### Pattern 1: Structured Forms

```python
@guidance
def user_form(lm):
    """Generate structured user form."""
    lm += "=== User Registration ===\n\n"

    # Name (alphabetic only)
    lm += "Full Name: " + gen("name", regex=r"[A-Za-z ]+", stop="\n") + "\n"

    # Age (numeric)
    lm += "Age: " + gen("age", regex=r"[0-9]+", max_tokens=3) + "\n"

    # Email (validated format)
    lm += "Email: " + gen(
        "email",
        regex=r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}",
        stop="\n"
    ) + "\n"

    # Phone (US format)
    lm += "Phone: " + gen("phone", regex=r"\d{3}-\d{3}-\d{4}") + "\n"

    # Account type (selection)
    lm += "Account Type: " + select(
        ["Standard", "Premium", "Enterprise"],
        name="account_type"
    ) + "\n"

    # Active status (boolean)
    lm += "Active: " + select(["Yes", "No"], name="active") + "\n"

    return lm
```

### Pattern 2: Multi-Entity Extraction

```python
@guidance
def extract_entities(lm, text):
    """Extract multiple entities with constraints."""
    lm += f"Text: {text}\n\n"

    # Person name (alphabetic)
    lm += "Person: " + gen("person", regex=r"[A-Za-z ]+", stop="\n") + "\n"

    # Organization (alphanumeric with spaces)
    lm += "Organization: " + gen(
        "organization",
        regex=r"[A-Za-z0-9 ]+",
        stop="\n"
    ) + "\n"

    # Date (YYYY-MM-DD format)
    lm += "Date: " + gen("date", regex=r"\d{4}-\d{2}-\d{2}") + "\n"

    # Location (alphabetic with spaces)
    lm += "Location: " + gen("location", regex=r"[A-Za-z ]+", stop="\n") + "\n"

    # Amount (currency)
    lm += "Amount: $" + gen("amount", regex=r"[0-9,]+\.[0-9]{2}") + "\n"

    return lm
```

### Pattern 3: Code Generation

```python
@guidance
def generate_python_function(lm):
    """Generate Python function with constraints."""
    # Function name (valid Python identifier)
    lm += "def " + gen("func_name", regex=r"[a-z_][a-z0-9_]*") + "("

    # Parameter name
    lm += gen("param", regex=r"[a-z_][a-z0-9_]*") + "):\n"

    # Docstring
    lm += '    """' + gen("docstring", stop='"""', max_tokens=50) + '"""\n'

    # Function body (constrained to valid Python)
    lm += "    return " + gen("return_value", stop="\n") + "\n"

    return lm
```

### Pattern 4: Hierarchical Data

```python
@guidance
def org_chart(lm):
    """Generate organizational chart."""
    lm += "Company: " + gen("company", regex=r"[A-Za-z ]+") + "\n\n"

    # CEO
    lm += "CEO: " + gen("ceo", regex=r"[A-Za-z ]+") + "\n"

    # Departments
    for dept in ["Engineering", "Sales", "Marketing"]:
        lm += f"\n{dept} Department:\n"
        lm += "  Head: " + gen(f"{dept.lower()}_head", regex=r"[A-Za-z ]+") + "\n"
        lm += "  Size: " + gen(f"{dept.lower()}_size", regex=r"[0-9]+") + " employees\n"

    return lm
```

## Performance Optimization

### Best Practices

#### 1. Use Specific Patterns

```python
# ✅ Good: Specific pattern
lm += gen("age", regex=r"[0-9]{1,3}")  # Fast

# ❌ Bad: Overly broad pattern
lm += gen("age", regex=r"[0-9]+")  # Slower
```

#### 2. Limit Max Tokens

```python
# ✅ Good: Reasonable limit
lm += gen("name", max_tokens=30)

# ❌ Bad: No limit
lm += gen("name")  # May generate forever
```

#### 3. Use stop Sequences

```python
# ✅ Good: Stop at newline
lm += gen("line", stop="\n")

# ❌ Bad: Rely on max_tokens
lm += gen("line", max_tokens=100)
```

#### 4. Cache Compiled Grammars

```python
# Grammars are cached automatically after first use
# No manual caching needed
@guidance
def reusable_pattern(lm):
    """This grammar is compiled once and cached."""
    lm += gen("email", regex=r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}")
    return lm

# First call: compiles grammar
lm = reusable_pattern(lm)

# Subsequent calls: uses cached grammar (fast)
lm = reusable_pattern(lm)
```

#### 5. Avoid Overlapping Constraints

```python
# ✅ Good: Clear constraints
lm += gen("age", regex=r"[0-9]+", max_tokens=3)

# ❌ Bad: Conflicting constraints
lm += gen("age", regex=r"[0-9]{2}", max_tokens=10)  # max_tokens unnecessary
```

### Performance Benchmarks

**Regex vs Free Generation:**
- Simple regex (digits): ~1.2x slower than free gen
- Complex regex (email): ~1.5x slower than free gen
- Grammar-based: ~2x slower than free gen

**But:**
- 100% valid outputs (vs ~70% with free gen + validation)
- No retry loops needed
- Overall faster end-to-end for structured outputs

**Optimization Tips:**
- Use regex for critical fields only
- Use `select()` for small fixed sets (fastest)
- Use `stop` sequences when possible (faster than max_tokens)
- Cache compiled grammars by reusing functions

## Resources

- **Token Healing Paper**: https://arxiv.org/abs/2306.17648
- **Guidance Docs**: https://guidance.readthedocs.io
- **GitHub**: https://github.com/guidance-ai/guidance




### Backends

# Backend Configuration Guide

Complete guide to configuring Guidance with different LLM backends.

## Table of Contents
- API-Based Models (Anthropic, OpenAI)
- Local Models (Transformers, llama.cpp)
- Backend Comparison
- Performance Tuning
- Advanced Configuration

## API-Based Models

### Anthropic Claude

#### Basic Setup

```python
from guidance import models

# Using environment variable
lm = models.Anthropic("claude-sonnet-4-5-20250929")
# Reads ANTHROPIC_API_KEY from environment

# Explicit API key
lm = models.Anthropic(
    model="claude-sonnet-4-5-20250929",
    api_key="your-api-key-here"
)
```

#### Available Models

```python
# Claude 3.5 Sonnet (Latest, recommended)
lm = models.Anthropic("claude-sonnet-4-5-20250929")

# Claude 3.7 Sonnet (Fast, cost-effective)
lm = models.Anthropic("claude-sonnet-3.7-20250219")

# Claude 3 Opus (Most capable)
lm = models.Anthropic("claude-3-opus-20240229")

# Claude 3.5 Haiku (Fastest, cheapest)
lm = models.Anthropic("claude-3-5-haiku-20241022")
```

#### Configuration Options

```python
lm = models.Anthropic(
    model="claude-sonnet-4-5-20250929",
    api_key="your-api-key",
    max_tokens=4096,           # Max tokens to generate
    temperature=0.7,            # Sampling temperature (0-1)
    top_p=0.9,                  # Nucleus sampling
    timeout=30,                 # Request timeout (seconds)
    max_retries=3              # Retry failed requests
)
```

#### With Context Managers

```python
from guidance import models, system, user, assistant, gen

lm = models.Anthropic("claude-sonnet-4-5-20250929")

with system():
    lm += "You are a helpful assistant."

with user():
    lm += "What is the capital of France?"

with assistant():
    lm += gen(max_tokens=50)

print(lm)
```

### OpenAI

#### Basic Setup

```python
from guidance import models

# Using environment variable
lm = models.OpenAI("gpt-4o")
# Reads OPENAI_API_KEY from environment

# Explicit API key
lm = models.OpenAI(
    model="gpt-4o",
    api_key="your-api-key-here"
)
```

#### Available Models

```python
# GPT-4o (Latest, multimodal)
lm = models.OpenAI("gpt-4o")

# GPT-4o Mini (Fast, cost-effective)
lm = models.OpenAI("gpt-4o-mini")

# GPT-4 Turbo
lm = models.OpenAI("gpt-4-turbo")

# GPT-3.5 Turbo (Cheapest)
lm = models.OpenAI("gpt-3.5-turbo")
```

#### Configuration Options

```python
lm = models.OpenAI(
    model="gpt-4o-mini",
    api_key="your-api-key",
    max_tokens=2048,
    temperature=0.7,
    top_p=1.0,
    frequency_penalty=0.0,
    presence_penalty=0.0,
    timeout=30
)
```

#### Chat Format

```python
from guidance import models, gen

lm = models.OpenAI("gpt-4o-mini")

# OpenAI uses chat format
lm += [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "What is 2+2?"}
]

# Generate response
lm += gen(max_tokens=50)
```

### Azure OpenAI

```python
from guidance import models

lm = models.AzureOpenAI(
    model="gpt-4o",
    azure_endpoint="https://your-resource.openai.azure.com/",
    api_key="your-azure-api-key",
    api_version="2024-02-15-preview",
    deployment_name="your-deployment-name"
)
```

## Local Models

### Transformers (Hugging Face)

#### Basic Setup

```python
from guidance.models import Transformers

# Load model from Hugging Face
lm = Transformers("microsoft/Phi-4-mini-instruct")
```

#### GPU Configuration

```python
# Use GPU
lm = Transformers(
    "microsoft/Phi-4-mini-instruct",
    device="cuda"
)

# Use specific GPU
lm = Transformers(
    "microsoft/Phi-4-mini-instruct",
    device="cuda:0"  # GPU 0
)

# Use CPU
lm = Transformers(
    "microsoft/Phi-4-mini-instruct",
    device="cpu"
)
```

#### Advanced Configuration

```python
lm = Transformers(
    "microsoft/Phi-4-mini-instruct",
    device="cuda",
    torch_dtype="float16",      # Use FP16 (faster, less memory)
    load_in_8bit=True,          # 8-bit quantization
    max_memory={0: "20GB"},     # GPU memory limit
    offload_folder="./offload"  # Offload to disk if needed
)
```

#### Popular Models

```python
# Phi-4 (Microsoft)
lm = Transformers("microsoft/Phi-4-mini-instruct")
lm = Transformers("microsoft/Phi-3-medium-4k-instruct")

# Llama 3 (Meta)
lm = Transformers("meta-llama/Llama-3.1-8B-Instruct")
lm = Transformers("meta-llama/Llama-3.1-70B-Instruct")

# Mistral (Mistral AI)
lm = Transformers("mistralai/Mistral-7B-Instruct-v0.3")
lm = Transformers("mistralai/Mixtral-8x7B-Instruct-v0.1")

# Qwen (Alibaba)
lm = Transformers("Qwen/Qwen2.5-7B-Instruct")

# Gemma (Google)
lm = Transformers("google/gemma-2-9b-it")
```

#### Generation Configuration

```python
lm = Transformers(
    "microsoft/Phi-4-mini-instruct",
    device="cuda"
)

# Configure generation
from guidance import gen

result = lm + gen(
    max_tokens=100,
    temperature=0.7,
    top_p=0.9,
    top_k=50,
    repetition_penalty=1.1
)
```

### llama.cpp

#### Basic Setup

```python
from guidance.models import LlamaCpp

# Load GGUF model
lm = LlamaCpp(
    model_path="/path/to/model.gguf",
    n_ctx=4096  # Context window
)
```

#### GPU Configuration

```python
# Use GPU acceleration
lm = LlamaCpp(
    model_path="/path/to/model.gguf",
    n_ctx=4096,
    n_gpu_layers=35,  # Offload 35 layers to GPU
    n_threads=8       # CPU threads for remaining layers
)

# Full GPU offload
lm = LlamaCpp(
    model_path="/path/to/model.gguf",
    n_ctx=4096,
    n_gpu_layers=-1  # Offload all layers
)
```

#### Advanced Configuration

```python
lm = LlamaCpp(
    model_path="/path/to/llama-3.1-8b-instruct.Q4_K_M.gguf",
    n_ctx=8192,          # Context window (tokens)
    n_gpu_layers=35,     # GPU layers
    n_threads=8,         # CPU threads
    n_batch=512,         # Batch size for prompt processing
    use_mmap=True,       # Memory-map the model file
    use_mlock=False,     # Lock model in RAM
    seed=42,             # Random seed
    verbose=False        # Suppress verbose output
)
```

#### Quantized Models

```python
# Q4_K_M (4-bit, recommended for most cases)
lm = LlamaCpp("/path/to/model.Q4_K_M.gguf")

# Q5_K_M (5-bit, better quality)
lm = LlamaCpp("/path/to/model.Q5_K_M.gguf")

# Q8_0 (8-bit, high quality)
lm = LlamaCpp("/path/to/model.Q8_0.gguf")

# F16 (16-bit float, highest quality)
lm = LlamaCpp("/path/to/model.F16.gguf")
```

#### Popular GGUF Models

```python
# Llama 3.1
lm = LlamaCpp("llama-3.1-8b-instruct.Q4_K_M.gguf")

# Mistral
lm = LlamaCpp("mistral-7b-instruct-v0.3.Q4_K_M.gguf")

# Phi-4
lm = LlamaCpp("phi-4-mini-instruct.Q4_K_M.gguf")
```

## Backend Comparison

### Feature Matrix

| Feature | Anthropic | OpenAI | Transformers | llama.cpp |
|---------|-----------|--------|--------------|-----------|
| Constrained Generation | ✅ Full | ✅ Full | ✅ Full | ✅ Full |
| Token Healing | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |
| Streaming | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |
| GPU Support | N/A | N/A | ✅ Yes | ✅ Yes |
| Quantization | N/A | N/A | ✅ Yes | ✅ Yes |
| Cost | $$$ | $$$ | Free | Free |
| Latency | Low | Low | Medium | Low |
| Setup Difficulty | Easy | Easy | Medium | Medium |

### Performance Characteristics

**Anthropic Claude:**
- **Latency**: 200-500ms (API call)
- **Throughput**: Limited by API rate limits
- **Cost**: $3-15 per 1M input tokens
- **Best for**: Production systems, high-quality outputs

**OpenAI:**
- **Latency**: 200-400ms (API call)
- **Throughput**: Limited by API rate limits
- **Cost**: $0.15-30 per 1M input tokens
- **Best for**: Cost-sensitive production, gpt-4o-mini

**Transformers:**
- **Latency**: 50-200ms (local inference)
- **Throughput**: GPU-dependent (10-100 tokens/sec)
- **Cost**: Hardware cost only
- **Best for**: Privacy-sensitive, high-volume, experimentation

**llama.cpp:**
- **Latency**: 30-150ms (local inference)
- **Throughput**: Hardware-dependent (20-150 tokens/sec)
- **Cost**: Hardware cost only
- **Best for**: Edge deployment, Apple Silicon, CPU inference

### Memory Requirements

**Transformers (FP16):**
- 7B model: ~14GB GPU VRAM
- 13B model: ~26GB GPU VRAM
- 70B model: ~140GB GPU VRAM (multi-GPU)

**llama.cpp (Q4_K_M):**
- 7B model: ~4.5GB RAM
- 13B model: ~8GB RAM
- 70B model: ~40GB RAM

**Optimization Tips:**
- Use quantized models (Q4_K_M) for lower memory
- Use GPU offloading for faster inference
- Use CPU inference for smaller models (<7B)

## Performance Tuning

### API Models (Anthropic, OpenAI)

#### Reduce Latency

```python
from guidance import models, gen

lm = models.Anthropic("claude-sonnet-4-5-20250929")

# Use lower max_tokens (faster response)
lm += gen(max_tokens=100)  # Instead of 1000

# Use streaming (perceived latency reduction)
for chunk in lm.stream(gen(max_tokens=500)):
    print(chunk, end="", flush=True)
```

#### Reduce Cost

```python
# Use cheaper models
lm = models.Anthropic("claude-3-5-haiku-20241022")  # vs Sonnet
lm = models.OpenAI("gpt-4o-mini")  # vs gpt-4o

# Reduce context size
# - Keep prompts concise
# - Avoid large few-shot examples
# - Use max_tokens limits
```

### Local Models (Transformers, llama.cpp)

#### Optimize GPU Usage

```python
from guidance.models import Transformers

# Use FP16 for 2x speedup
lm = Transformers(
    "meta-llama/Llama-3.1-8B-Instruct",
    device="cuda",
    torch_dtype="float16"
)

# Use 8-bit quantization for 4x memory reduction
lm = Transformers(
    "meta-llama/Llama-3.1-8B-Instruct",
    device="cuda",
    load_in_8bit=True
)

# Use flash attention (requires flash-attn package)
lm = Transformers(
    "meta-llama/Llama-3.1-8B-Instruct",
    device="cuda",
    use_flash_attention_2=True
)
```

#### Optimize llama.cpp

```python
from guidance.models import LlamaCpp

# Maximize GPU layers
lm = LlamaCpp(
    model_path="/path/to/model.Q4_K_M.gguf",
    n_gpu_layers=-1  # All layers on GPU
)

# Optimize batch size
lm = LlamaCpp(
    model_path="/path/to/model.Q4_K_M.gguf",
    n_batch=512,     # Larger batch = faster prompt processing
    n_gpu_layers=-1
)

# Use Metal (Apple Silicon)
lm = LlamaCpp(
    model_path="/path/to/model.Q4_K_M.gguf",
    n_gpu_layers=-1,  # Use Metal GPU acceleration
    use_mmap=True
)
```

#### Batch Processing

```python
# Process multiple requests efficiently
requests = [
    "What is 2+2?",
    "What is the capital of France?",
    "What is photosynthesis?"
]

# Bad: Sequential processing
for req in requests:
    lm = Transformers("microsoft/Phi-4-mini-instruct")
    lm += req + gen(max_tokens=50)

# Good: Reuse loaded model
lm = Transformers("microsoft/Phi-4-mini-instruct")
for req in requests:
    lm += req + gen(max_tokens=50)
```

## Advanced Configuration

### Custom Model Configurations

```python
from transformers import AutoTokenizer, AutoModelForCausalLM
from guidance.models import Transformers

# Load custom model
tokenizer = AutoTokenizer.from_pretrained("your-model")
model = AutoModelForCausalLM.from_pretrained(
    "your-model",
    device_map="auto",
    torch_dtype="float16"
)

# Use with Guidance
lm = Transformers(model=model, tokenizer=tokenizer)
```

### Environment Variables

```bash
# API keys
export ANTHROPIC_API_KEY="sk-ant-..."
export OPENAI_API_KEY="sk-..."

# Transformers cache
export HF_HOME="/path/to/cache"
export TRANSFORMERS_CACHE="/path/to/cache"

# GPU selection
export CUDA_VISIBLE_DEVICES=0,1  # Use GPU 0 and 1
```

### Debugging

```python
# Enable verbose logging
import logging
logging.basicConfig(level=logging.DEBUG)

# Check backend info
lm = models.Anthropic("claude-sonnet-4-5-20250929")
print(f"Model: {lm.model_name}")
print(f"Backend: {lm.backend}")

# Check GPU usage (Transformers)
lm = Transformers("microsoft/Phi-4-mini-instruct", device="cuda")
print(f"Device: {lm.device}")
print(f"Memory allocated: {torch.cuda.memory_allocated() / 1e9:.2f} GB")
```

## Resources

- **Anthropic Docs**: https://docs.anthropic.com
- **OpenAI Docs**: https://platform.openai.com/docs
- **Hugging Face Models**: https://huggingface.co/models
- **llama.cpp**: https://github.com/ggerganov/llama.cpp
- **GGUF Models**: https://huggingface.co/models?library=gguf




### Examples

# Production-Ready Examples

Real-world examples of using Guidance for structured generation, agents, and workflows.

## Table of Contents
- JSON Generation
- Data Extraction
- Classification Systems
- Agent Systems
- Multi-Step Workflows
- Code Generation
- Production Tips

## JSON Generation

### Basic JSON

```python
from guidance import models, gen, guidance

@guidance
def generate_user(lm):
    """Generate valid user JSON."""
    lm += "{\n"
    lm += '  "name": ' + gen("name", regex=r'"[A-Za-z ]+"') + ",\n"
    lm += '  "age": ' + gen("age", regex=r"[0-9]+") + ",\n"
    lm += '  "email": ' + gen(
        "email",
        regex=r'"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"'
    ) + "\n"
    lm += "}"
    return lm

# Use it
lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm += "Generate a user profile:\n"
lm = generate_user(lm)

print(lm)
# Output: Valid JSON guaranteed
```

### Nested JSON

```python
@guidance
def generate_order(lm):
    """Generate nested order JSON."""
    lm += "{\n"

    # Customer info
    lm += '  "customer": {\n'
    lm += '    "name": ' + gen("customer_name", regex=r'"[A-Za-z ]+"') + ",\n"
    lm += '    "email": ' + gen(
        "customer_email",
        regex=r'"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"'
    ) + "\n"
    lm += "  },\n"

    # Order details
    lm += '  "order": {\n'
    lm += '    "id": ' + gen("order_id", regex=r'"ORD-[0-9]{6}"') + ",\n"
    lm += '    "date": ' + gen("order_date", regex=r'"\d{4}-\d{2}-\d{2}"') + ",\n"
    lm += '    "total": ' + gen("order_total", regex=r"[0-9]+\.[0-9]{2}") + "\n"
    lm += "  },\n"

    # Status
    lm += '  "status": ' + gen(
        "status",
        regex=r'"(pending|processing|shipped|delivered)"'
    ) + "\n"

    lm += "}"
    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = generate_order(lm)
```

### JSON Array

```python
@guidance
def generate_user_list(lm, count=3):
    """Generate JSON array of users."""
    lm += "[\n"

    for i in range(count):
        lm += "  {\n"
        lm += '    "id": ' + gen(f"id_{i}", regex=r"[0-9]+") + ",\n"
        lm += '    "name": ' + gen(f"name_{i}", regex=r'"[A-Za-z ]+"') + ",\n"
        lm += '    "active": ' + gen(f"active_{i}", regex=r"(true|false)") + "\n"
        lm += "  }"
        if i < count - 1:
            lm += ","
        lm += "\n"

    lm += "]"
    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = generate_user_list(lm, count=5)
```

### Dynamic JSON Schema

```python
import json
from guidance import models, gen, guidance

@guidance
def json_from_schema(lm, schema):
    """Generate JSON matching a schema."""
    lm += "{\n"

    fields = list(schema["properties"].items())
    for i, (field_name, field_schema) in enumerate(fields):
        lm += f'  "{field_name}": '

        # Handle different types
        if field_schema["type"] == "string":
            if "pattern" in field_schema:
                lm += gen(field_name, regex=f'"{field_schema["pattern"]}"')
            else:
                lm += gen(field_name, regex=r'"[^"]+"')
        elif field_schema["type"] == "number":
            lm += gen(field_name, regex=r"[0-9]+(\.[0-9]+)?")
        elif field_schema["type"] == "integer":
            lm += gen(field_name, regex=r"[0-9]+")
        elif field_schema["type"] == "boolean":
            lm += gen(field_name, regex=r"(true|false)")

        if i < len(fields) - 1:
            lm += ","
        lm += "\n"

    lm += "}"
    return lm

# Define schema
schema = {
    "type": "object",
    "properties": {
        "name": {"type": "string"},
        "age": {"type": "integer"},
        "score": {"type": "number"},
        "active": {"type": "boolean"}
    }
}

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = json_from_schema(lm, schema)
```

## Data Extraction

### Extract from Text

```python
from guidance import models, gen, guidance, system, user, assistant

@guidance
def extract_person_info(lm, text):
    """Extract structured info from text."""
    lm += f"Text: {text}\n\n"

    with assistant():
        lm += "Name: " + gen("name", regex=r"[A-Za-z ]+", stop="\n") + "\n"
        lm += "Age: " + gen("age", regex=r"[0-9]+", max_tokens=3) + "\n"
        lm += "Occupation: " + gen("occupation", regex=r"[A-Za-z ]+", stop="\n") + "\n"
        lm += "Email: " + gen(
            "email",
            regex=r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}",
            stop="\n"
        ) + "\n"

    return lm

text = "John Smith is a 35-year-old software engineer. Contact: john@example.com"

lm = models.Anthropic("claude-sonnet-4-5-20250929")

with system():
    lm += "You extract structured information from text."

with user():
    lm = extract_person_info(lm, text)

print(f"Name: {lm['name']}")
print(f"Age: {lm['age']}")
print(f"Occupation: {lm['occupation']}")
print(f"Email: {lm['email']}")
```

### Multi-Entity Extraction

```python
@guidance
def extract_entities(lm, text):
    """Extract multiple entity types."""
    lm += f"Analyze: {text}\n\n"

    # Person entities
    lm += "People:\n"
    for i in range(3):  # Up to 3 people
        lm += f"- " + gen(f"person_{i}", regex=r"[A-Za-z ]+", stop="\n") + "\n"

    # Organization entities
    lm += "\nOrganizations:\n"
    for i in range(2):  # Up to 2 orgs
        lm += f"- " + gen(f"org_{i}", regex=r"[A-Za-z0-9 ]+", stop="\n") + "\n"

    # Dates
    lm += "\nDates:\n"
    for i in range(2):  # Up to 2 dates
        lm += f"- " + gen(f"date_{i}", regex=r"\d{4}-\d{2}-\d{2}", stop="\n") + "\n"

    # Locations
    lm += "\nLocations:\n"
    for i in range(2):  # Up to 2 locations
        lm += f"- " + gen(f"location_{i}", regex=r"[A-Za-z ]+", stop="\n") + "\n"

    return lm

text = """
Tim Cook and Satya Nadella met at Microsoft headquarters in Redmond on 2024-09-15
to discuss the collaboration between Apple and Microsoft. The meeting continued
in Cupertino on 2024-09-20.
"""

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = extract_entities(lm, text)
```

### Batch Extraction

```python
@guidance
def batch_extract(lm, texts):
    """Extract from multiple texts."""
    lm += "Batch Extraction Results:\n\n"

    for i, text in enumerate(texts):
        lm += f"=== Item {i+1} ===\n"
        lm += f"Text: {text}\n"
        lm += "Name: " + gen(f"name_{i}", regex=r"[A-Za-z ]+", stop="\n") + "\n"
        lm += "Sentiment: " + gen(
            f"sentiment_{i}",
            regex=r"(positive|negative|neutral)",
            stop="\n"
        ) + "\n\n"

    return lm

texts = [
    "Alice is happy with the product",
    "Bob is disappointed with the service",
    "Carol has no strong feelings either way"
]

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = batch_extract(lm, texts)
```

## Classification Systems

### Sentiment Analysis

```python
from guidance import models, select, gen

lm = models.Anthropic("claude-sonnet-4-5-20250929")

text = "This product is absolutely amazing! Best purchase ever."

lm += f"Text: {text}\n\n"
lm += "Sentiment: " + select(
    ["positive", "negative", "neutral"],
    name="sentiment"
)
lm += "\nConfidence: " + gen("confidence", regex=r"[0-9]{1,3}") + "%\n"
lm += "Reasoning: " + gen("reasoning", stop="\n", max_tokens=50)

print(f"Sentiment: {lm['sentiment']}")
print(f"Confidence: {lm['confidence']}%")
print(f"Reasoning: {lm['reasoning']}")
```

### Multi-Label Classification

```python
@guidance
def classify_article(lm, text):
    """Classify article with multiple labels."""
    lm += f"Article: {text}\n\n"

    # Primary category
    lm += "Primary Category: " + select(
        ["Technology", "Business", "Science", "Politics", "Entertainment"],
        name="primary_category"
    ) + "\n"

    # Secondary categories (up to 3)
    lm += "\nSecondary Categories:\n"
    categories = ["Technology", "Business", "Science", "Politics", "Entertainment"]
    for i in range(3):
        lm += f"{i+1}. " + select(categories, name=f"secondary_{i}") + "\n"

    # Tags
    lm += "\nTags: " + gen("tags", stop="\n", max_tokens=50) + "\n"

    # Target audience
    lm += "Target Audience: " + select(
        ["General", "Expert", "Beginner"],
        name="audience"
    )

    return lm

article = """
Apple announced new AI features in iOS 18, leveraging machine learning to improve
battery life and performance. The company's stock rose 5% following the announcement.
"""

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = classify_article(lm, article)
```

### Intent Classification

```python
@guidance
def classify_intent(lm, message):
    """Classify user intent."""
    lm += f"User Message: {message}\n\n"

    # Intent
    lm += "Intent: " + select(
        ["question", "complaint", "request", "feedback", "other"],
        name="intent"
    ) + "\n"

    # Urgency
    lm += "Urgency: " + select(
        ["low", "medium", "high", "critical"],
        name="urgency"
    ) + "\n"

    # Department
    lm += "Route To: " + select(
        ["support", "sales", "billing", "technical"],
        name="department"
    ) + "\n"

    # Sentiment
    lm += "Sentiment: " + select(
        ["positive", "neutral", "negative"],
        name="sentiment"
    )

    return lm

message = "My account was charged twice for the same order. Need help ASAP!"

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = classify_intent(lm, message)

print(f"Intent: {lm['intent']}")
print(f"Urgency: {lm['urgency']}")
print(f"Department: {lm['department']}")
```

## Agent Systems

### ReAct Agent

```python
from guidance import models, gen, select, guidance

@guidance(stateless=False)
def react_agent(lm, question, tools, max_rounds=5):
    """ReAct agent with tool use."""
    lm += f"Question: {question}\n\n"

    for round in range(max_rounds):
        # Thought
        lm += f"Thought {round+1}: " + gen("thought", stop="\n", max_tokens=100) + "\n"

        # Action selection
        lm += "Action: " + select(
            list(tools.keys()) + ["answer"],
            name="action"
        )

        if lm["action"] == "answer":
            lm += "\n\nFinal Answer: " + gen("answer", max_tokens=200)
            break

        # Action input
        lm += "\nAction Input: " + gen("action_input", stop="\n", max_tokens=100) + "\n"

        # Execute tool
        if lm["action"] in tools:
            try:
                result = tools[lm["action"]](lm["action_input"])
                lm += f"Observation: {result}\n\n"
            except Exception as e:
                lm += f"Observation: Error - {str(e)}\n\n"

    return lm

# Define tools
tools = {
    "calculator": lambda expr: eval(expr),
    "search": lambda query: f"Search results for '{query}': [Mock results]",
    "weather": lambda city: f"Weather in {city}: Sunny, 72°F"
}

# Use agent
lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = react_agent(lm, "What is (25 * 4) + 10?", tools)

print(lm["answer"])
```

### Multi-Agent System

```python
@guidance
def coordinator_agent(lm, task):
    """Coordinator that delegates to specialists."""
    lm += f"Task: {task}\n\n"

    # Determine which specialist to use
    lm += "Specialist: " + select(
        ["researcher", "writer", "coder", "analyst"],
        name="specialist"
    ) + "\n"

    lm += "Reasoning: " + gen("reasoning", stop="\n", max_tokens=100) + "\n"

    return lm

@guidance
def researcher_agent(lm, query):
    """Research specialist."""
    lm += f"Research Query: {query}\n\n"
    lm += "Findings:\n"
    for i in range(3):
        lm += f"{i+1}. " + gen(f"finding_{i}", stop="\n", max_tokens=100) + "\n"
    return lm

@guidance
def writer_agent(lm, topic):
    """Writing specialist."""
    lm += f"Topic: {topic}\n\n"
    lm += "Title: " + gen("title", stop="\n", max_tokens=50) + "\n"
    lm += "Content:\n" + gen("content", max_tokens=500)
    return lm

# Coordination workflow
task = "Write an article about AI safety"

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = coordinator_agent(lm, task)

specialist = lm["specialist"]
if specialist == "researcher":
    lm = researcher_agent(lm, task)
elif specialist == "writer":
    lm = writer_agent(lm, task)
```

### Tool Use with Validation

```python
@guidance(stateless=False)
def validated_tool_agent(lm, question):
    """Agent with validated tool calls."""
    tools = {
        "add": lambda a, b: float(a) + float(b),
        "multiply": lambda a, b: float(a) * float(b),
        "divide": lambda a, b: float(a) / float(b) if float(b) != 0 else "Error: Division by zero"
    }

    lm += f"Question: {question}\n\n"

    for i in range(5):
        # Select tool
        lm += "capabilities: File operations, code editing, terminal access, search" + select(list(tools.keys()) + ["done"], name="tool")

        if lm["tool"] == "done":
            lm += "\nAnswer: " + gen("answer", max_tokens=100)
            break

        # Get validated numeric arguments
        lm += "\nArg1: " + gen("arg1", regex=r"-?[0-9]+(\.[0-9]+)?") + "\n"
        lm += "Arg2: " + gen("arg2", regex=r"-?[0-9]+(\.[0-9]+)?") + "\n"

        # Execute
        result = tools[lm["tool"]](lm["arg1"], lm["arg2"])
        lm += f"Result: {result}\n\n"

    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = validated_tool_agent(lm, "What is (10 + 5) * 3?")
```

## Multi-Step Workflows

### Chain of Thought

```python
@guidance
def chain_of_thought(lm, question):
    """Multi-step reasoning with CoT."""
    lm += f"Question: {question}\n\n"

    # Generate reasoning steps
    lm += "Let me think step by step:\n\n"
    for i in range(4):
        lm += f"Step {i+1}: " + gen(f"step_{i+1}", stop="\n", max_tokens=100) + "\n"

    # Final answer
    lm += "\nTherefore, the answer is: " + gen("answer", stop="\n", max_tokens=50)

    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = chain_of_thought(lm, "If a train travels 60 mph for 2.5 hours, how far does it go?")

print(lm["answer"])
```

### Self-Consistency

```python
@guidance
def self_consistency(lm, question, num_samples=3):
    """Generate multiple reasoning paths and aggregate."""
    lm += f"Question: {question}\n\n"

    answers = []
    for i in range(num_samples):
        lm += f"=== Attempt {i+1} ===\n"
        lm += "Reasoning: " + gen(f"reasoning_{i}", stop="\n", max_tokens=100) + "\n"
        lm += "Answer: " + gen(f"answer_{i}", stop="\n", max_tokens=50) + "\n\n"
        answers.append(lm[f"answer_{i}"])

    # Aggregate (simple majority vote)
    from collections import Counter
    most_common = Counter(answers).most_common(1)[0][0]

    lm += f"Final Answer (by majority): {most_common}\n"
    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = self_consistency(lm, "What is 15% of 200?")
```

### Planning and Execution

```python
@guidance
def plan_and_execute(lm, goal):
    """Plan tasks then execute them."""
    lm += f"Goal: {goal}\n\n"

    # Planning phase
    lm += "Plan:\n"
    num_steps = 4
    for i in range(num_steps):
        lm += f"{i+1}. " + gen(f"plan_step_{i}", stop="\n", max_tokens=100) + "\n"

    # Execution phase
    lm += "\nExecution:\n\n"
    for i in range(num_steps):
        lm += f"Step {i+1}: {lm[f'plan_step_{i}']}\n"
        lm += "Status: " + select(["completed", "in-progress", "blocked"], name=f"status_{i}") + "\n"
        lm += "Result: " + gen(f"result_{i}", stop="\n", max_tokens=150) + "\n\n"

    # Summary
    lm += "Summary: " + gen("summary", max_tokens=200)

    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = plan_and_execute(lm, "Build a REST API for a blog platform")
```

## Code Generation

### Python Function

```python
@guidance
def generate_python_function(lm, description):
    """Generate Python function from description."""
    lm += f"Description: {description}\n\n"

    # Function signature
    lm += "def " + gen("func_name", regex=r"[a-z_][a-z0-9_]*") + "("
    lm += gen("params", regex=r"[a-z_][a-z0-9_]*(, [a-z_][a-z0-9_]*)*") + "):\n"

    # Docstring
    lm += '    """' + gen("docstring", stop='"""', max_tokens=100) + '"""\n'

    # Function body
    lm += "    " + gen("body", stop="\n", max_tokens=200) + "\n"

    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = generate_python_function(lm, "Check if a number is prime")

print(lm)
```

### SQL Query

```python
@guidance
def generate_sql(lm, description):
    """Generate SQL query from description."""
    lm += f"Description: {description}\n\n"
    lm += "SQL Query:\n"

    # SELECT clause
    lm += "SELECT " + gen("select_clause", stop=" FROM", max_tokens=100)

    # FROM clause
    lm += " FROM " + gen("from_clause", stop=" WHERE", max_tokens=50)

    # WHERE clause (optional)
    lm += " WHERE " + gen("where_clause", stop=";", max_tokens=100) + ";"

    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = generate_sql(lm, "Get all users who signed up in the last 30 days")
```

### API Endpoint

```python
@guidance
def generate_api_endpoint(lm, description):
    """Generate REST API endpoint."""
    lm += f"Description: {description}\n\n"

    # HTTP method
    lm += "Method: " + select(["GET", "POST", "PUT", "DELETE"], name="method") + "\n"

    # Path
    lm += "Path: /" + gen("path", regex=r"[a-z0-9/-]+", stop="\n") + "\n"

    # Request body (if POST/PUT)
    if lm["method"] in ["POST", "PUT"]:
        lm += "\nRequest Body:\n"
        lm += "{\n"
        lm += '  "field1": ' + gen("field1", regex=r'"[a-z_]+"') + ",\n"
        lm += '  "field2": ' + gen("field2", regex=r'"[a-z_]+"') + "\n"
        lm += "}\n"

    # Response
    lm += "\nResponse (200 OK):\n"
    lm += "{\n"
    lm += '  "status": "success",\n'
    lm += '  "data": ' + gen("response_data", max_tokens=100) + "\n"
    lm += "}\n"

    return lm

lm = models.Anthropic("claude-sonnet-4-5-20250929")
lm = generate_api_endpoint(lm, "Create a new blog post")
```

## Production Tips

### Error Handling

```python
@guidance
def safe_extraction(lm, text):
    """Extract with fallback handling."""
    try:
        lm += f"Text: {text}\n"
        lm += "Name: " + gen("name", regex=r"[A-Za-z ]+", stop="\n", max_tokens=30)
        return lm
    except Exception as e:
        # Fallback to less strict extraction
        lm += f"Text: {text}\n"
        lm += "Name: " + gen("name", stop="\n", max_tokens=30)
        return lm
```

### Caching

```python
from functools import lru_cache

@lru_cache(maxsize=100)
def cached_generation(text):
    """Cache LLM generations."""
    lm = models.Anthropic("claude-sonnet-4-5-20250929")
    lm += f"Analyze: {text}\n"
    lm += "Sentiment: " + select(["positive", "negative", "neutral"], name="sentiment")
    return lm["sentiment"]

# First call: hits LLM
result1 = cached_generation("This is great!")

# Second call: returns cached result
result2 = cached_generation("This is great!")  # Instant!
```

### Monitoring

```python
import time

@guidance
def monitored_generation(lm, text):
    """Track generation metrics."""
    start_time = time.time()

    lm += f"Text: {text}\n"
    lm += "Analysis: " + gen("analysis", max_tokens=100)

    elapsed = time.time() - start_time

    # Log metrics
    print(f"Generation time: {elapsed:.2f}s")
    print(f"Output length: {len(lm['analysis'])} chars")

    return lm
```

### Batch Processing

```python
def batch_process(texts, batch_size=10):
    """Process texts in batches."""
    lm = models.Anthropic("claude-sonnet-4-5-20250929")
    results = []

    for i in range(0, len(texts), batch_size):
        batch = texts[i:i+batch_size]

        for text in batch:
            lm += f"Text: {text}\n"
            lm += "Sentiment: " + select(
                ["positive", "negative", "neutral"],
                name=f"sentiment_{i}"
            ) + "\n\n"

        results.extend([lm[f"sentiment_{i}"] for i in range(len(batch))])

    return results
```

## Resources

- **Guidance Notebooks**: https://github.com/guidance-ai/guidance/tree/main/notebooks
- **Guidance Docs**: https://guidance.readthedocs.io
- **Community Examples**: https://github.com/guidance-ai/guidance/discussions




---

## 🚀 Usage

**Reference this template:** `@skill-guidance.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
