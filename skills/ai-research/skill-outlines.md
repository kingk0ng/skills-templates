---
id: skill-outlines
type: skill
name: outlines
description: Guarantee valid JSON/XML/code structure during generation, use Pydantic
  models for type-safe outputs, support local models (Transformers, vLLM), and maximize
  inference speed with Outlines - dottxt.ai's structured generation library
category: ai-research
complexity: medium
keywords:
- api
- github
- optimization
- performance
- python
capabilities: []
token_estimate: 2355
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~2,355 -->
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




# outlines

> Guarantee valid JSON/XML/code structure during generation, use Pydantic models for type-safe outputs, support local models (Transformers, vLLM), and maximize inference speed with Outlines - dottxt.ai's structured generation library

# Outlines: Structured Text Generation

## When to Use This Skill

Use Outlines when you need to:
- **Guarantee valid JSON/XML/code** structure during generation
- **Use Pydantic models** for type-safe outputs
- **Support local models** (Transformers, llama.cpp, vLLM)
- **Maximize inference speed** with zero-overhead structured generation
- **Generate against JSON schemas** automatically
- **Control token sampling** at the grammar level

**GitHub Stars**: 8,000+ | **From**: dottxt.ai (formerly .txt)

## Installation

```bash
# Base installation
pip install outlines

# With specific backends
pip install outlines transformers  # Hugging Face models
pip install outlines llama-cpp-python  # llama.cpp
pip install outlines vllm  # vLLM for high-throughput
```

## Quick Start

### Basic Example: Classification

```python
import outlines
from typing import Literal

# Load model
model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")

# Generate with type constraint
prompt = "Sentiment of 'This product is amazing!': "
generator = outlines.generate.choice(model, ["positive", "negative", "neutral"])
sentiment = generator(prompt)

print(sentiment)  # "positive" (guaranteed one of these)
```

### With Pydantic Models

```python
from pydantic import BaseModel
import outlines

class User(BaseModel):
    name: str
    age: int
    email: str

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")

# Generate structured output
prompt = "Extract user: John Doe, 30 years old, john@example.com"
generator = outlines.generate.json(model, User)
user = generator(prompt)

print(user.name)   # "John Doe"
print(user.age)    # 30
print(user.email)  # "john@example.com"
```

## Core Concepts

### 1. Constrained Token Sampling

Outlines uses Finite State Machines (FSM) to constrain token generation at the logit level.

**How it works:**
1. Convert schema (JSON/Pydantic/regex) to context-free grammar (CFG)
2. Transform CFG into Finite State Machine (FSM)
3. Filter invalid tokens at each step during generation
4. Fast-forward when only one valid token exists

**Benefits:**
- **Zero overhead**: Filtering happens at token level
- **Speed improvement**: Fast-forward through deterministic paths
- **Guaranteed validity**: Invalid outputs impossible

```python
import outlines

# Pydantic model -> JSON schema -> CFG -> FSM
class Person(BaseModel):
    name: str
    age: int

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")

# Behind the scenes:
# 1. Person -> JSON schema
# 2. JSON schema -> CFG
# 3. CFG -> FSM
# 4. FSM filters tokens during generation

generator = outlines.generate.json(model, Person)
result = generator("Generate person: Alice, 25")
```

### 2. Structured Generators

Outlines provides specialized generators for different output types.

#### Choice Generator

```python
# Multiple choice selection
generator = outlines.generate.choice(
    model,
    ["positive", "negative", "neutral"]
)

sentiment = generator("Review: This is great!")
# Result: One of the three choices
```

#### JSON Generator

```python
from pydantic import BaseModel

class Product(BaseModel):
    name: str
    price: float
    in_stock: bool

# Generate valid JSON matching schema
generator = outlines.generate.json(model, Product)
product = generator("Extract: iPhone 15, $999, available")

# Guaranteed valid Product instance
print(type(product))  # <class '__main__.Product'>
```

#### Regex Generator

```python
# Generate text matching regex
generator = outlines.generate.regex(
    model,
    r"[0-9]{3}-[0-9]{3}-[0-9]{4}"  # Phone number pattern
)

phone = generator("Generate phone number:")
# Result: "555-123-4567" (guaranteed to match pattern)
```

#### Integer/Float Generators

```python
# Generate specific numeric types
int_generator = outlines.generate.integer(model)
age = int_generator("Person's age:")  # Guaranteed integer

float_generator = outlines.generate.float(model)
price = float_generator("Product price:")  # Guaranteed float
```

### 3. Model Backends

Outlines supports multiple local and API-based backends.

#### Transformers (Hugging Face)

```python
import outlines

# Load from Hugging Face
model = outlines.models.transformers(
    "microsoft/Phi-3-mini-4k-instruct",
    device="cuda"  # Or "cpu"
)

# Use with any generator
generator = outlines.generate.json(model, YourModel)
```

#### llama.cpp

```python
# Load GGUF model
model = outlines.models.llamacpp(
    "./models/llama-3.1-8b-instruct.Q4_K_M.gguf",
    n_gpu_layers=35
)

generator = outlines.generate.json(model, YourModel)
```

#### vLLM (High Throughput)

```python
# For production deployments
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-8B-Instruct",
    tensor_parallel_size=2  # Multi-GPU
)

generator = outlines.generate.json(model, YourModel)
```

#### OpenAI (Limited Support)

```python
# Basic OpenAI support
model = outlines.models.openai(
    "gpt-4o-mini",
    api_key="your-api-key"
)

# Note: Some features limited with API models
generator = outlines.generate.json(model, YourModel)
```

### 4. Pydantic Integration

Outlines has first-class Pydantic support with automatic schema translation.

#### Basic Models

```python
from pydantic import BaseModel, Field

class Article(BaseModel):
    title: str = Field(description="Article title")
    author: str = Field(description="Author name")
    word_count: int = Field(description="Number of words", gt=0)
    tags: list[str] = Field(description="List of tags")

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, Article)

article = generator("Generate article about AI")
print(article.title)
print(article.word_count)  # Guaranteed > 0
```

#### Nested Models

```python
class Address(BaseModel):
    street: str
    city: str
    country: str

class Person(BaseModel):
    name: str
    age: int
    address: Address  # Nested model

generator = outlines.generate.json(model, Person)
person = generator("Generate person in New York")

print(person.address.city)  # "New York"
```

#### Enums and Literals

```python
from enum import Enum
from typing import Literal

class Status(str, Enum):
    PENDING = "pending"
    APPROVED = "approved"
    REJECTED = "rejected"

class Application(BaseModel):
    applicant: str
    status: Status  # Must be one of enum values
    priority: Literal["low", "medium", "high"]  # Must be one of literals

generator = outlines.generate.json(model, Application)
app = generator("Generate application")

print(app.status)  # Status.PENDING (or APPROVED/REJECTED)
```

## Common Patterns

### Pattern 1: Data Extraction

```python
from pydantic import BaseModel
import outlines

class CompanyInfo(BaseModel):
    name: str
    founded_year: int
    industry: str
    employees: int

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, CompanyInfo)

text = """
Apple Inc. was founded in 1976 in the technology industry.
The company employs approximately 164,000 people worldwide.
"""

prompt = f"Extract company information:\n{text}\n\nCompany:"
company = generator(prompt)

print(f"Name: {company.name}")
print(f"Founded: {company.founded_year}")
print(f"Industry: {company.industry}")
print(f"Employees: {company.employees}")
```

### Pattern 2: Classification

```python
from typing import Literal
import outlines

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")

# Binary classification
generator = outlines.generate.choice(model, ["spam", "not_spam"])
result = generator("Email: Buy now! 50% off!")

# Multi-class classification
categories = ["technology", "business", "sports", "entertainment"]
category_gen = outlines.generate.choice(model, categories)
category = category_gen("Article: Apple announces new iPhone...")

# With confidence
class Classification(BaseModel):
    label: Literal["positive", "negative", "neutral"]
    confidence: float

classifier = outlines.generate.json(model, Classification)
result = classifier("Review: This product is okay, nothing special")
```

### Pattern 3: Structured Forms

```python
class UserProfile(BaseModel):
    full_name: str
    age: int
    email: str
    phone: str
    country: str
    interests: list[str]

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, UserProfile)

prompt = """
Extract user profile from:
Name: Alice Johnson
Age: 28
Email: alice@example.com
Phone: 555-0123
Country: USA
Interests: hiking, photography, cooking
"""

profile = generator(prompt)
print(profile.full_name)
print(profile.interests)  # ["hiking", "photography", "cooking"]
```

### Pattern 4: Multi-Entity Extraction

```python
class Entity(BaseModel):
    name: str
    type: Literal["PERSON", "ORGANIZATION", "LOCATION"]

class DocumentEntities(BaseModel):
    entities: list[Entity]

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, DocumentEntities)

text = "Tim Cook met with Satya Nadella at Microsoft headquarters in Redmond."
prompt = f"Extract entities from: {text}"

result = generator(prompt)
for entity in result.entities:
    print(f"{entity.name} ({entity.type})")
```

### Pattern 5: Code Generation

```python
class PythonFunction(BaseModel):
    function_name: str
    parameters: list[str]
    docstring: str
    body: str

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, PythonFunction)

prompt = "Generate a Python function to calculate factorial"
func = generator(prompt)

print(f"def {func.function_name}({', '.join(func.parameters)}):")
print(f'    """{func.docstring}"""')
print(f"    {func.body}")
```

### Pattern 6: Batch Processing

```python
def batch_extract(texts: list[str], schema: type[BaseModel]):
    """Extract structured data from multiple texts."""
    model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
    generator = outlines.generate.json(model, schema)

    results = []
    for text in texts:
        result = generator(f"Extract from: {text}")
        results.append(result)

    return results

class Person(BaseModel):
    name: str
    age: int

texts = [
    "John is 30 years old",
    "Alice is 25 years old",
    "Bob is 40 years old"
]

people = batch_extract(texts, Person)
for person in people:
    print(f"{person.name}: {person.age}")
```

## Backend Configuration

### Transformers

```python
import outlines

# Basic usage
model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")

# GPU configuration
model = outlines.models.transformers(
    "microsoft/Phi-3-mini-4k-instruct",
    device="cuda",
    model_kwargs={"torch_dtype": "float16"}
)

# Popular models
model = outlines.models.transformers("meta-llama/Llama-3.1-8B-Instruct")
model = outlines.models.transformers("mistralai/Mistral-7B-Instruct-v0.3")
model = outlines.models.transformers("Qwen/Qwen2.5-7B-Instruct")
```

### llama.cpp

```python
# Load GGUF model
model = outlines.models.llamacpp(
    "./models/llama-3.1-8b.Q4_K_M.gguf",
    n_ctx=4096,         # Context window
    n_gpu_layers=35,    # GPU layers
    n_threads=8         # CPU threads
)

# Full GPU offload
model = outlines.models.llamacpp(
    "./models/model.gguf",
    n_gpu_layers=-1  # All layers on GPU
)
```

### vLLM (Production)

```python
# Single GPU
model = outlines.models.vllm("meta-llama/Llama-3.1-8B-Instruct")

# Multi-GPU
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-70B-Instruct",
    tensor_parallel_size=4  # 4 GPUs
)

# With quantization
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-8B-Instruct",
    quantization="awq"  # Or "gptq"
)
```

## Best Practices

### 1. Use Specific Types

```python
# ✅ Good: Specific types
class Product(BaseModel):
    name: str
    price: float  # Not str
    quantity: int  # Not str
    in_stock: bool  # Not str

# ❌ Bad: Everything as string
class Product(BaseModel):
    name: str
    price: str  # Should be float
    quantity: str  # Should be int
```

### 2. Add Constraints

```python
from pydantic import Field

# ✅ Good: With constraints
class User(BaseModel):
    name: str = Field(min_length=1, max_length=100)
    age: int = Field(ge=0, le=120)
    email: str = Field(pattern=r"^[\w\.-]+@[\w\.-]+\.\w+$")

# ❌ Bad: No constraints
class User(BaseModel):
    name: str
    age: int
    email: str
```

### 3. Use Enums for Categories

```python
# ✅ Good: Enum for fixed set
class Priority(str, Enum):
    LOW = "low"
    MEDIUM = "medium"
    HIGH = "high"

class Task(BaseModel):
    title: str
    priority: Priority

# ❌ Bad: Free-form string
class Task(BaseModel):
    title: str
    priority: str  # Can be anything
```

### 4. Provide Context in Prompts

```python
# ✅ Good: Clear context
prompt = """
Extract product information from the following text.
Text: iPhone 15 Pro costs $999 and is currently in stock.
Product:
"""

# ❌ Bad: Minimal context
prompt = "iPhone 15 Pro costs $999 and is currently in stock."
```

### 5. Handle Optional Fields

```python
from typing import Optional

# ✅ Good: Optional fields for incomplete data
class Article(BaseModel):
    title: str  # Required
    author: Optional[str] = None  # Optional
    date: Optional[str] = None  # Optional
    tags: list[str] = []  # Default empty list

# Can succeed even if author/date missing
```

## Comparison to Alternatives

| Feature | Outlines | Instructor | Guidance | LMQL |
|---------|----------|------------|----------|------|
| Pydantic Support | ✅ Native | ✅ Native | ❌ No | ❌ No |
| JSON Schema | ✅ Yes | ✅ Yes | ⚠️ Limited | ✅ Yes |
| Regex Constraints | ✅ Yes | ❌ No | ✅ Yes | ✅ Yes |
| Local Models | ✅ Full | ⚠️ Limited | ✅ Full | ✅ Full |
| API Models | ⚠️ Limited | ✅ Full | ✅ Full | ✅ Full |
| Zero Overhead | ✅ Yes | ❌ No | ⚠️ Partial | ✅ Yes |
| Automatic Retrying | ❌ No | ✅ Yes | ❌ No | ❌ No |
| Learning Curve | Low | Low | Low | High |

**When to choose Outlines:**
- Using local models (Transformers, llama.cpp, vLLM)
- Need maximum inference speed
- Want Pydantic model support
- Require zero-overhead structured generation
- Control token sampling process

**When to choose alternatives:**
- Instructor: Need API models with automatic retrying
- Guidance: Need token healing and complex workflows
- LMQL: Prefer declarative query syntax

## Performance Characteristics

**Speed:**
- **Zero overhead**: Structured generation as fast as unconstrained
- **Fast-forward optimization**: Skips deterministic tokens
- **1.2-2x faster** than post-generation validation approaches

**Memory:**
- FSM compiled once per schema (cached)
- Minimal runtime overhead
- Efficient with vLLM for high throughput

**Accuracy:**
- **100% valid outputs** (guaranteed by FSM)
- No retry loops needed
- Deterministic token filtering

## Resources

- **Documentation**: https://outlines-dev.github.io/outlines
- **GitHub**: https://github.com/outlines-dev/outlines (8k+ stars)
- **Discord**: https://discord.gg/R9DSu34mGd
- **Blog**: https://blog.dottxt.co

## See Also

- `references/json_generation.md` - Comprehensive JSON and Pydantic patterns
- `references/backends.md` - Backend-specific configuration
- `references/examples.md` - Production-ready examples




---


## 📚 Reference Materials


### Json_Generation

# Comprehensive JSON Generation Guide

Complete guide to JSON generation with Outlines using Pydantic models and JSON schemas.

## Table of Contents
- Pydantic Models
- JSON Schema Support
- Advanced Patterns
- Nested Structures
- Complex Types
- Validation
- Performance Optimization

## Pydantic Models

### Basic Models

```python
from pydantic import BaseModel
import outlines

class User(BaseModel):
    name: str
    age: int
    email: str

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, User)

user = generator("Generate user: Alice, 25, alice@example.com")
print(user.name)   # "Alice"
print(user.age)    # 25
print(user.email)  # "alice@example.com"
```

###

 Field Constraints

```python
from pydantic import BaseModel, Field

class Product(BaseModel):
    name: str = Field(min_length=1, max_length=100)
    price: float = Field(gt=0, description="Price in USD")
    discount: float = Field(ge=0, le=100, description="Discount percentage")
    quantity: int = Field(ge=0, description="Available quantity")
    sku: str = Field(pattern=r"^[A-Z]{3}-\d{6}$")

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, Product)

product = generator("Generate product: iPhone 15, $999")
# All fields guaranteed to meet constraints
```

**Available Constraints:**
- `min_length`, `max_length`: String length
- `gt`, `ge`, `lt`, `le`: Numeric comparisons
- `multiple_of`: Number must be multiple of value
- `pattern`: Regex pattern for strings
- `min_items`, `max_items`: List length

### Optional Fields

```python
from typing import Optional

class Article(BaseModel):
    title: str  # Required
    author: Optional[str] = None  # Optional
    published_date: Optional[str] = None  # Optional
    tags: list[str] = []  # Default empty list
    view_count: int = 0  # Default value

generator = outlines.generate.json(model, Article)

# Can generate even if optional fields missing
article = generator("Title: Introduction to AI")
print(article.author)  # None (not provided)
print(article.tags)    # [] (default)
```

### Default Values

```python
class Config(BaseModel):
    debug: bool = False
    max_retries: int = 3
    timeout: float = 30.0
    log_level: str = "INFO"

# Generator uses defaults when not specified
generator = outlines.generate.json(model, Config)
config = generator("Generate config with debug enabled")
print(config.debug)  # True (from prompt)
print(config.timeout)  # 30.0 (default)
```

## Enums and Literals

### Enum Fields

```python
from enum import Enum

class Status(str, Enum):
    PENDING = "pending"
    APPROVED = "approved"
    REJECTED = "rejected"
    CANCELLED = "cancelled"

class Application(BaseModel):
    applicant_name: str
    status: Status  # Must be one of enum values
    submitted_date: str

generator = outlines.generate.json(model, Application)
app = generator("Generate application for John Doe")

print(app.status)  # Status.PENDING (or one of the enum values)
print(type(app.status))  # <enum 'Status'>
```

### Literal Types

```python
from typing import Literal

class Task(BaseModel):
    title: str
    priority: Literal["low", "medium", "high", "critical"]
    status: Literal["todo", "in_progress", "done"]
    assigned_to: str

generator = outlines.generate.json(model, Task)
task = generator("Create high priority task: Fix bug")

print(task.priority)  # One of: "low", "medium", "high", "critical"
```

### Multiple Choice Fields

```python
class Survey(BaseModel):
    question: str
    answer: Literal["strongly_disagree", "disagree", "neutral", "agree", "strongly_agree"]
    confidence: Literal["low", "medium", "high"]

generator = outlines.generate.json(model, Survey)
survey = generator("Rate: 'I enjoy using this product'")
```

## Nested Structures

### Nested Models

```python
class Address(BaseModel):
    street: str
    city: str
    state: str
    zip_code: str
    country: str = "USA"

class Person(BaseModel):
    name: str
    age: int
    email: str
    address: Address  # Nested model

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, Person)

prompt = """
Extract person:
Name: Alice Johnson
Age: 28
Email: alice@example.com
Address: 123 Main St, Boston, MA, 02101
"""

person = generator(prompt)
print(person.name)  # "Alice Johnson"
print(person.address.city)  # "Boston"
print(person.address.state)  # "MA"
```

### Deep Nesting

```python
class Coordinates(BaseModel):
    latitude: float
    longitude: float

class Location(BaseModel):
    name: str
    coordinates: Coordinates

class Event(BaseModel):
    title: str
    date: str
    location: Location

generator = outlines.generate.json(model, Event)
event = generator("Generate event: Tech Conference in San Francisco")

print(event.title)  # "Tech Conference"
print(event.location.name)  # "San Francisco"
print(event.location.coordinates.latitude)  # 37.7749
```

### Lists of Nested Models

```python
class Item(BaseModel):
    name: str
    quantity: int
    price: float

class Order(BaseModel):
    order_id: str
    customer: str
    items: list[Item]  # List of nested models
    total: float

generator = outlines.generate.json(model, Order)

prompt = """
Generate order for John:
- 2x Widget ($10 each)
- 3x Gadget ($15 each)
Order ID: ORD-001
"""

order = generator(prompt)
print(f"Order ID: {order.order_id}")
for item in order.items:
    print(f"- {item.quantity}x {item.name} @ ${item.price}")
print(f"Total: ${order.total}")
```

## Complex Types

### Union Types

```python
from typing import Union

class TextContent(BaseModel):
    type: Literal["text"]
    content: str

class ImageContent(BaseModel):
    type: Literal["image"]
    url: str
    caption: str

class Post(BaseModel):
    title: str
    content: Union[TextContent, ImageContent]  # Either type

generator = outlines.generate.json(model, Post)

# Can generate either text or image content
post = generator("Generate blog post with image")
if post.content.type == "text":
    print(post.content.content)
elif post.content.type == "image":
    print(post.content.url)
```

### Lists and Arrays

```python
class Article(BaseModel):
    title: str
    authors: list[str]  # List of strings
    tags: list[str]
    sections: list[dict[str, str]]  # List of dicts
    related_ids: list[int]

generator = outlines.generate.json(model, Article)
article = generator("Generate article about AI")

print(article.authors)  # ["Alice", "Bob"]
print(article.tags)  # ["AI", "Machine Learning", "Technology"]
```

### Dictionaries

```python
class Metadata(BaseModel):
    title: str
    properties: dict[str, str]  # String keys and values
    counts: dict[str, int]  # String keys, int values
    settings: dict[str, Union[str, int, bool]]  # Mixed value types

generator = outlines.generate.json(model, Metadata)
meta = generator("Generate metadata")

print(meta.properties)  # {"author": "Alice", "version": "1.0"}
print(meta.counts)  # {"views": 1000, "likes": 50}
```

### Any Type (Use Sparingly)

```python
from typing import Any

class FlexibleData(BaseModel):
    name: str
    structured_field: str
    flexible_field: Any  # Can be anything

# Note: Any reduces type safety, use only when necessary
generator = outlines.generate.json(model, FlexibleData)
```

## JSON Schema Support

### Direct Schema Usage

```python
import outlines

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")

# Define JSON schema
schema = {
    "type": "object",
    "properties": {
        "name": {"type": "string"},
        "age": {"type": "integer", "minimum": 0, "maximum": 120},
        "email": {"type": "string", "format": "email"}
    },
    "required": ["name", "age", "email"]
}

# Generate from schema
generator = outlines.generate.json(model, schema)
result = generator("Generate person: Alice, 25, alice@example.com")

print(result)  # Valid JSON matching schema
```

### Schema from Pydantic

```python
class User(BaseModel):
    name: str
    age: int
    email: str

# Get JSON schema from Pydantic model
schema = User.model_json_schema()
print(schema)
# {
#   "type": "object",
#   "properties": {
#     "name": {"type": "string"},
#     "age": {"type": "integer"},
#     "email": {"type": "string"}
#   },
#   "required": ["name", "age", "email"]
# }

# Both approaches equivalent:
generator1 = outlines.generate.json(model, User)
generator2 = outlines.generate.json(model, schema)
```

## Advanced Patterns

### Conditional Fields

```python
class Order(BaseModel):
    order_type: Literal["standard", "express"]
    delivery_date: str
    express_fee: Optional[float] = None  # Only for express orders

generator = outlines.generate.json(model, Order)

# Express order
order1 = generator("Create express order for tomorrow")
print(order1.express_fee)  # 25.0

# Standard order
order2 = generator("Create standard order")
print(order2.express_fee)  # None
```

### Recursive Models

```python
from typing import Optional, List

class TreeNode(BaseModel):
    value: str
    children: Optional[List['TreeNode']] = None

# Enable forward references
TreeNode.model_rebuild()

generator = outlines.generate.json(model, TreeNode)
tree = generator("Generate file tree with subdirectories")

print(tree.value)  # "root"
print(tree.children[0].value)  # "subdir1"
```

### Model with Validation

```python
from pydantic import field_validator

class DateRange(BaseModel):
    start_date: str
    end_date: str

    @field_validator('end_date')
    def end_after_start(cls, v, info):
        """Ensure end_date is after start_date."""
        if 'start_date' in info.data:
            from datetime import datetime
            start = datetime.strptime(info.data['start_date'], '%Y-%m-%d')
            end = datetime.strptime(v, '%Y-%m-%d')
            if end < start:
                raise ValueError('end_date must be after start_date')
        return v

generator = outlines.generate.json(model, DateRange)
# Validation happens after generation
```

## Multiple Objects

### Generate List of Objects

```python
class Person(BaseModel):
    name: str
    age: int

class Team(BaseModel):
    team_name: str
    members: list[Person]

generator = outlines.generate.json(model, Team)

team = generator("Generate engineering team with 5 members")
print(f"Team: {team.team_name}")
for member in team.members:
    print(f"- {member.name}, {member.age}")
```

### Batch Generation

```python
def generate_batch(prompts: list[str], schema: type[BaseModel]):
    """Generate structured outputs for multiple prompts."""
    model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
    generator = outlines.generate.json(model, schema)

    results = []
    for prompt in prompts:
        result = generator(prompt)
        results.append(result)

    return results

class Product(BaseModel):
    name: str
    price: float

prompts = [
    "Product: iPhone 15, $999",
    "Product: MacBook Pro, $2499",
    "Product: AirPods, $179"
]

products = generate_batch(prompts, Product)
for product in products:
    print(f"{product.name}: ${product.price}")
```

## Performance Optimization

### Caching Generators

```python
from functools import lru_cache

@lru_cache(maxsize=10)
def get_generator(model_name: str, schema_hash: int):
    """Cache generators for reuse."""
    model = outlines.models.transformers(model_name)
    return outlines.generate.json(model, schema)

# First call: creates generator
gen1 = get_generator("microsoft/Phi-3-mini-4k-instruct", hash(User))

# Second call: returns cached generator (fast!)
gen2 = get_generator("microsoft/Phi-3-mini-4k-instruct", hash(User))
```

### Batch Processing

```python
# Process multiple items efficiently
model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, User)

texts = ["User: Alice, 25", "User: Bob, 30", "User: Carol, 35"]

# Reuse generator (model stays loaded)
users = [generator(text) for text in texts]
```

### Minimize Schema Complexity

```python
# ✅ Good: Simple, flat structure (faster)
class SimplePerson(BaseModel):
    name: str
    age: int
    city: str

# ⚠️ Slower: Deep nesting
class ComplexPerson(BaseModel):
    personal_info: PersonalInfo
    address: Address
    employment: Employment
    # ... many nested levels
```

## Error Handling

### Handle Missing Fields

```python
from pydantic import ValidationError

class User(BaseModel):
    name: str
    age: int
    email: str

try:
    user = generator("Generate user")  # May not include all fields
except ValidationError as e:
    print(f"Validation error: {e}")
    # Handle gracefully
```

### Fallback with Optional Fields

```python
class RobustUser(BaseModel):
    name: str  # Required
    age: Optional[int] = None  # Optional
    email: Optional[str] = None  # Optional

# More likely to succeed even with incomplete data
user = generator("Generate user: Alice")
print(user.name)  # "Alice"
print(user.age)  # None (not provided)
```

## Best Practices

### 1. Use Specific Types

```python
# ✅ Good: Specific types
class Product(BaseModel):
    name: str
    price: float  # Not Any or str
    quantity: int  # Not str
    in_stock: bool  # Not int

# ❌ Bad: Generic types
class Product(BaseModel):
    name: Any
    price: str  # Should be float
    quantity: str  # Should be int
```

### 2. Add Descriptions

```python
# ✅ Good: Clear descriptions
class Article(BaseModel):
    title: str = Field(description="Article title, 10-100 characters")
    content: str = Field(description="Main article content in paragraphs")
    tags: list[str] = Field(description="List of relevant topic tags")

# Descriptions help the model understand expected output
```

### 3. Use Constraints

```python
# ✅ Good: With constraints
class Age(BaseModel):
    value: int = Field(ge=0, le=120, description="Age in years")

# ❌ Bad: No constraints
class Age(BaseModel):
    value: int  # Could be negative or > 120
```

### 4. Prefer Enums Over Strings

```python
# ✅ Good: Enum for fixed set
class Priority(str, Enum):
    LOW = "low"
    MEDIUM = "medium"
    HIGH = "high"

class Task(BaseModel):
    priority: Priority  # Guaranteed valid

# ❌ Bad: Free-form string
class Task(BaseModel):
    priority: str  # Could be "urgent", "ASAP", "!!", etc.
```

### 5. Test Your Models

```python
# Test models work as expected
def test_product_model():
    product = Product(
        name="Test Product",
        price=19.99,
        quantity=10,
        in_stock=True
    )
    assert product.price == 19.99
    assert isinstance(product, Product)

# Run tests before using in production
```

## Resources

- **Pydantic Docs**: https://docs.pydantic.dev
- **JSON Schema**: https://json-schema.org
- **Outlines GitHub**: https://github.com/outlines-dev/outlines




### Backends

# Backend Configuration Guide

Complete guide to configuring Outlines with different model backends.

## Table of Contents
- Local Models (Transformers, llama.cpp, vLLM)
- API Models (OpenAI)
- Performance Comparison
- Configuration Examples
- Production Deployment

## Transformers (Hugging Face)

### Basic Setup

```python
import outlines

# Load model from Hugging Face
model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")

# Use with generator
generator = outlines.generate.json(model, YourModel)
result = generator("Your prompt")
```

### GPU Configuration

```python
# Use CUDA GPU
model = outlines.models.transformers(
    "microsoft/Phi-3-mini-4k-instruct",
    device="cuda"
)

# Use specific GPU
model = outlines.models.transformers(
    "microsoft/Phi-3-mini-4k-instruct",
    device="cuda:0"  # GPU 0
)

# Use CPU
model = outlines.models.transformers(
    "microsoft/Phi-3-mini-4k-instruct",
    device="cpu"
)

# Use Apple Silicon MPS
model = outlines.models.transformers(
    "microsoft/Phi-3-mini-4k-instruct",
    device="mps"
)
```

### Advanced Configuration

```python
# FP16 for faster inference
model = outlines.models.transformers(
    "microsoft/Phi-3-mini-4k-instruct",
    device="cuda",
    model_kwargs={
        "torch_dtype": "float16"
    }
)

# 8-bit quantization (less memory)
model = outlines.models.transformers(
    "microsoft/Phi-3-mini-4k-instruct",
    device="cuda",
    model_kwargs={
        "load_in_8bit": True,
        "device_map": "auto"
    }
)

# 4-bit quantization (even less memory)
model = outlines.models.transformers(
    "meta-llama/Llama-3.1-70B-Instruct",
    device="cuda",
    model_kwargs={
        "load_in_4bit": True,
        "device_map": "auto",
        "bnb_4bit_compute_dtype": "float16"
    }
)

# Multi-GPU
model = outlines.models.transformers(
    "meta-llama/Llama-3.1-70B-Instruct",
    device="cuda",
    model_kwargs={
        "device_map": "auto",  # Automatic GPU distribution
        "max_memory": {0: "40GB", 1: "40GB"}  # Per-GPU limits
    }
)
```

### Popular Models

```python
# Phi-4 (Microsoft)
model = outlines.models.transformers("microsoft/Phi-4-mini-instruct")
model = outlines.models.transformers("microsoft/Phi-3-medium-4k-instruct")

# Llama 3.1 (Meta)
model = outlines.models.transformers("meta-llama/Llama-3.1-8B-Instruct")
model = outlines.models.transformers("meta-llama/Llama-3.1-70B-Instruct")
model = outlines.models.transformers("meta-llama/Llama-3.1-405B-Instruct")

# Mistral (Mistral AI)
model = outlines.models.transformers("mistralai/Mistral-7B-Instruct-v0.3")
model = outlines.models.transformers("mistralai/Mixtral-8x7B-Instruct-v0.1")
model = outlines.models.transformers("mistralai/Mixtral-8x22B-Instruct-v0.1")

# Qwen (Alibaba)
model = outlines.models.transformers("Qwen/Qwen2.5-7B-Instruct")
model = outlines.models.transformers("Qwen/Qwen2.5-14B-Instruct")
model = outlines.models.transformers("Qwen/Qwen2.5-72B-Instruct")

# Gemma (Google)
model = outlines.models.transformers("google/gemma-2-9b-it")
model = outlines.models.transformers("google/gemma-2-27b-it")

# Llava (Vision)
model = outlines.models.transformers("llava-hf/llava-v1.6-mistral-7b-hf")
```

### Custom Model Loading

```python
from transformers import AutoTokenizer, AutoModelForCausalLM
import outlines

# Load model manually
tokenizer = AutoTokenizer.from_pretrained("your-model")
model_hf = AutoModelForCausalLM.from_pretrained(
    "your-model",
    device_map="auto",
    torch_dtype="float16"
)

# Use with Outlines
model = outlines.models.transformers(
    model=model_hf,
    tokenizer=tokenizer
)
```

## llama.cpp

### Basic Setup

```python
import outlines

# Load GGUF model
model = outlines.models.llamacpp(
    "./models/llama-3.1-8b-instruct.Q4_K_M.gguf",
    n_ctx=4096  # Context window
)

# Use with generator
generator = outlines.generate.json(model, YourModel)
```

### GPU Configuration

```python
# CPU only
model = outlines.models.llamacpp(
    "./models/model.gguf",
    n_ctx=4096,
    n_threads=8  # Use 8 CPU threads
)

# GPU offload (partial)
model = outlines.models.llamacpp(
    "./models/model.gguf",
    n_ctx=4096,
    n_gpu_layers=35,  # Offload 35 layers to GPU
    n_threads=4       # CPU threads for remaining layers
)

# Full GPU offload
model = outlines.models.llamacpp(
    "./models/model.gguf",
    n_ctx=8192,
    n_gpu_layers=-1  # All layers on GPU
)
```

### Advanced Configuration

```python
model = outlines.models.llamacpp(
    "./models/llama-3.1-8b.Q4_K_M.gguf",
    n_ctx=8192,          # Context window (tokens)
    n_gpu_layers=35,     # GPU layers
    n_threads=8,         # CPU threads
    n_batch=512,         # Batch size for prompt processing
    use_mmap=True,       # Memory-map model file (faster loading)
    use_mlock=False,     # Lock model in RAM (prevents swapping)
    seed=42,             # Random seed for reproducibility
    verbose=False        # Suppress verbose output
)
```

### Quantization Formats

```python
# Q4_K_M (4-bit, recommended for most cases)
# - Size: ~4.5GB for 7B model
# - Quality: Good
# - Speed: Fast
model = outlines.models.llamacpp("./models/model.Q4_K_M.gguf")

# Q5_K_M (5-bit, better quality)
# - Size: ~5.5GB for 7B model
# - Quality: Very good
# - Speed: Slightly slower than Q4
model = outlines.models.llamacpp("./models/model.Q5_K_M.gguf")

# Q6_K (6-bit, high quality)
# - Size: ~6.5GB for 7B model
# - Quality: Excellent
# - Speed: Slower than Q5
model = outlines.models.llamacpp("./models/model.Q6_K.gguf")

# Q8_0 (8-bit, near-original quality)
# - Size: ~8GB for 7B model
# - Quality: Near FP16
# - Speed: Slower than Q6
model = outlines.models.llamacpp("./models/model.Q8_0.gguf")

# F16 (16-bit float, original quality)
# - Size: ~14GB for 7B model
# - Quality: Original
# - Speed: Slowest
model = outlines.models.llamacpp("./models/model.F16.gguf")
```

### Popular GGUF Models

```python
# Llama 3.1
model = outlines.models.llamacpp("llama-3.1-8b-instruct.Q4_K_M.gguf")
model = outlines.models.llamacpp("llama-3.1-70b-instruct.Q4_K_M.gguf")

# Mistral
model = outlines.models.llamacpp("mistral-7b-instruct-v0.3.Q4_K_M.gguf")

# Phi-4
model = outlines.models.llamacpp("phi-4-mini-instruct.Q4_K_M.gguf")

# Qwen
model = outlines.models.llamacpp("qwen2.5-7b-instruct.Q4_K_M.gguf")
```

### Apple Silicon Optimization

```python
# Optimized for M1/M2/M3 Macs
model = outlines.models.llamacpp(
    "./models/llama-3.1-8b.Q4_K_M.gguf",
    n_ctx=4096,
    n_gpu_layers=-1,  # Use Metal GPU acceleration
    use_mmap=True,    # Efficient memory mapping
    n_threads=8       # Use performance cores
)
```

## vLLM (Production)

### Basic Setup

```python
import outlines

# Load model with vLLM
model = outlines.models.vllm("meta-llama/Llama-3.1-8B-Instruct")

# Use with generator
generator = outlines.generate.json(model, YourModel)
```

### Single GPU

```python
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-8B-Instruct",
    gpu_memory_utilization=0.9,  # Use 90% of GPU memory
    max_model_len=4096          # Max sequence length
)
```

### Multi-GPU

```python
# Tensor parallelism (split model across GPUs)
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-70B-Instruct",
    tensor_parallel_size=4,  # Use 4 GPUs
    gpu_memory_utilization=0.9
)

# Pipeline parallelism (rare, for very large models)
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-405B-Instruct",
    pipeline_parallel_size=8,  # 8-GPU pipeline
    tensor_parallel_size=4     # 4-GPU tensor split
    # Total: 32 GPUs
)
```

### Quantization

```python
# AWQ quantization (4-bit)
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-8B-Instruct",
    quantization="awq",
    dtype="float16"
)

# GPTQ quantization (4-bit)
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-8B-Instruct",
    quantization="gptq"
)

# SqueezeLLM quantization
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-8B-Instruct",
    quantization="squeezellm"
)
```

### Advanced Configuration

```python
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-8B-Instruct",
    tensor_parallel_size=1,
    gpu_memory_utilization=0.9,
    max_model_len=8192,
    max_num_seqs=256,           # Max concurrent sequences
    max_num_batched_tokens=8192, # Max tokens per batch
    dtype="float16",
    trust_remote_code=True,
    enforce_eager=False,        # Use CUDA graphs (faster)
    swap_space=4                # CPU swap space (GB)
)
```

### Batch Processing

```python
# vLLM optimized for high-throughput batch processing
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-8B-Instruct",
    max_num_seqs=128  # Process 128 sequences in parallel
)

generator = outlines.generate.json(model, YourModel)

# Process many prompts efficiently
prompts = ["prompt1", "prompt2", ..., "prompt100"]
results = [generator(p) for p in prompts]
# vLLM automatically batches and optimizes
```

## OpenAI (Limited Support)

### Basic Setup

```python
import outlines

# Basic OpenAI support
model = outlines.models.openai("gpt-4o-mini", api_key="your-api-key")

# Use with generator
generator = outlines.generate.json(model, YourModel)
result = generator("Your prompt")
```

### Configuration

```python
model = outlines.models.openai(
    "gpt-4o-mini",
    api_key="your-api-key",  # Or set OPENAI_API_KEY env var
    max_tokens=2048,
    temperature=0.7
)
```

### Available Models

```python
# GPT-4o (latest)
model = outlines.models.openai("gpt-4o")

# GPT-4o Mini (cost-effective)
model = outlines.models.openai("gpt-4o-mini")

# GPT-4 Turbo
model = outlines.models.openai("gpt-4-turbo")

# GPT-3.5 Turbo
model = outlines.models.openai("gpt-3.5-turbo")
```

**Note**: OpenAI support is limited compared to local models. Some advanced features may not work.

## Backend Comparison

### Feature Matrix

| Feature | Transformers | llama.cpp | vLLM | OpenAI |
|---------|-------------|-----------|------|--------|
| Structured Generation | ✅ Full | ✅ Full | ✅ Full | ⚠️ Limited |
| FSM Optimization | ✅ Yes | ✅ Yes | ✅ Yes | ❌ No |
| GPU Support | ✅ Yes | ✅ Yes | ✅ Yes | N/A |
| Multi-GPU | ✅ Yes | ✅ Yes | ✅ Yes | N/A |
| Quantization | ✅ Yes | ✅ Yes | ✅ Yes | N/A |
| High Throughput | ⚠️ Medium | ⚠️ Medium | ✅ Excellent | ⚠️ API-limited |
| Setup Difficulty | Easy | Medium | Medium | Easy |
| Cost | Hardware | Hardware | Hardware | API usage |

### Performance Characteristics

**Transformers:**
- **Latency**: 50-200ms (single request, GPU)
- **Throughput**: 10-50 tokens/sec (depends on hardware)
- **Memory**: 2-4GB per 1B parameters (FP16)
- **Best for**: Development, small-scale deployment, flexibility

**llama.cpp:**
- **Latency**: 30-150ms (single request)
- **Throughput**: 20-150 tokens/sec (depends on quantization)
- **Memory**: 0.5-2GB per 1B parameters (Q4-Q8)
- **Best for**: CPU inference, Apple Silicon, edge deployment, low memory

**vLLM:**
- **Latency**: 30-100ms (single request)
- **Throughput**: 100-1000+ tokens/sec (batch processing)
- **Memory**: 2-4GB per 1B parameters (FP16)
- **Best for**: Production, high-throughput, batch processing, serving

**OpenAI:**
- **Latency**: 200-500ms (API call)
- **Throughput**: API rate limits
- **Memory**: N/A (cloud-based)
- **Best for**: Quick prototyping, no infrastructure

### Memory Requirements

**7B Model:**
- FP16: ~14GB
- 8-bit: ~7GB
- 4-bit: ~4GB
- Q4_K_M (GGUF): ~4.5GB

**13B Model:**
- FP16: ~26GB
- 8-bit: ~13GB
- 4-bit: ~7GB
- Q4_K_M (GGUF): ~8GB

**70B Model:**
- FP16: ~140GB (multi-GPU)
- 8-bit: ~70GB (multi-GPU)
- 4-bit: ~35GB (single A100/H100)
- Q4_K_M (GGUF): ~40GB

## Performance Tuning

### Transformers Optimization

```python
# Use FP16
model = outlines.models.transformers(
    "meta-llama/Llama-3.1-8B-Instruct",
    device="cuda",
    model_kwargs={"torch_dtype": "float16"}
)

# Use flash attention (2-4x faster)
model = outlines.models.transformers(
    "meta-llama/Llama-3.1-8B-Instruct",
    device="cuda",
    model_kwargs={
        "torch_dtype": "float16",
        "use_flash_attention_2": True
    }
)

# Use 8-bit quantization (2x less memory)
model = outlines.models.transformers(
    "meta-llama/Llama-3.1-8B-Instruct",
    device="cuda",
    model_kwargs={
        "load_in_8bit": True,
        "device_map": "auto"
    }
)
```

### llama.cpp Optimization

```python
# Maximize GPU usage
model = outlines.models.llamacpp(
    "./models/model.Q4_K_M.gguf",
    n_gpu_layers=-1,  # All layers on GPU
    n_ctx=8192,
    n_batch=512       # Larger batch = faster
)

# Optimize for CPU (Apple Silicon)
model = outlines.models.llamacpp(
    "./models/model.Q4_K_M.gguf",
    n_ctx=4096,
    n_threads=8,      # Use all performance cores
    use_mmap=True
)
```

### vLLM Optimization

```python
# High throughput
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-8B-Instruct",
    gpu_memory_utilization=0.95,  # Use 95% of GPU
    max_num_seqs=256,             # High concurrency
    enforce_eager=False           # Use CUDA graphs
)

# Multi-GPU
model = outlines.models.vllm(
    "meta-llama/Llama-3.1-70B-Instruct",
    tensor_parallel_size=4,  # 4 GPUs
    gpu_memory_utilization=0.9
)
```

## Production Deployment

### Docker with vLLM

```dockerfile
FROM vllm/vllm-openai:latest

# Install outlines
RUN pip install outlines

# Copy your code
COPY app.py /app/

# Run
CMD ["python", "/app/app.py"]
```

### Environment Variables

```bash
# Transformers cache
export HF_HOME="/path/to/cache"
export TRANSFORMERS_CACHE="/path/to/cache"

# GPU selection
export CUDA_VISIBLE_DEVICES=0,1,2,3

# OpenAI API key
export OPENAI_API_KEY="sk-..."

# Disable tokenizers parallelism warning
export TOKENIZERS_PARALLELISM=false
```

### Model Serving

```python
# Simple HTTP server with vLLM
import outlines
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

# Load model once at startup
model = outlines.models.vllm("meta-llama/Llama-3.1-8B-Instruct")

class User(BaseModel):
    name: str
    age: int
    email: str

generator = outlines.generate.json(model, User)

@app.post("/extract")
def extract(text: str):
    result = generator(f"Extract user from: {text}")
    return result.model_dump()
```

## Resources

- **Transformers**: https://huggingface.co/docs/transformers
- **llama.cpp**: https://github.com/ggerganov/llama.cpp
- **vLLM**: https://docs.vllm.ai
- **Outlines**: https://github.com/outlines-dev/outlines




### Examples

# Production-Ready Examples

Real-world examples of using Outlines for structured generation in production systems.

## Table of Contents
- Data Extraction
- Classification Systems
- Form Processing
- Multi-Entity Extraction
- Code Generation
- Batch Processing
- Production Patterns

## Data Extraction

### Basic Information Extraction

```python
from pydantic import BaseModel, Field
import outlines

class PersonInfo(BaseModel):
    name: str = Field(description="Full name")
    age: int = Field(ge=0, le=120)
    occupation: str
    email: str = Field(pattern=r"^[\w\.-]+@[\w\.-]+\.\w+$")
    location: str

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, PersonInfo)

text = """
Dr. Sarah Johnson is a 42-year-old research scientist at MIT.
She can be reached at sarah.j@mit.edu and currently lives in Cambridge, MA.
"""

prompt = f"Extract person information from:\n{text}\n\nPerson:"
person = generator(prompt)

print(f"Name: {person.name}")
print(f"Age: {person.age}")
print(f"Occupation: {person.occupation}")
print(f"Email: {person.email}")
print(f"Location: {person.location}")
```

### Company Information

```python
class CompanyInfo(BaseModel):
    name: str
    founded_year: int = Field(ge=1800, le=2025)
    industry: str
    headquarters: str
    employees: int = Field(gt=0)
    revenue: Optional[str] = None

model = outlines.models.transformers("meta-llama/Llama-3.1-8B-Instruct")
generator = outlines.generate.json(model, CompanyInfo)

text = """
Tesla, Inc. was founded in 2003 and operates primarily in the automotive
and energy industries. The company is headquartered in Austin, Texas,
and employs approximately 140,000 people worldwide.
"""

company = generator(f"Extract company information:\n{text}\n\nCompany:")

print(f"Company: {company.name}")
print(f"Founded: {company.founded_year}")
print(f"Industry: {company.industry}")
print(f"HQ: {company.headquarters}")
print(f"Employees: {company.employees:,}")
```

### Product Specifications

```python
class ProductSpec(BaseModel):
    name: str
    brand: str
    price: float = Field(gt=0)
    dimensions: str
    weight: str
    features: list[str]
    rating: Optional[float] = Field(None, ge=0, le=5)

generator = outlines.generate.json(model, ProductSpec)

text = """
The Apple iPhone 15 Pro is priced at $999. It measures 146.6 x 70.6 x 8.25 mm
and weighs 187 grams. Key features include the A17 Pro chip, titanium design,
action button, and USB-C port. It has an average customer rating of 4.5 stars.
"""

product = generator(f"Extract product specifications:\n{text}\n\nProduct:")

print(f"Product: {product.brand} {product.name}")
print(f"Price: ${product.price}")
print(f"Features: {', '.join(product.features)}")
```

## Classification Systems

### Sentiment Analysis

```python
from typing import Literal
from enum import Enum

class Sentiment(str, Enum):
    VERY_POSITIVE = "very_positive"
    POSITIVE = "positive"
    NEUTRAL = "neutral"
    NEGATIVE = "negative"
    VERY_NEGATIVE = "very_negative"

class SentimentAnalysis(BaseModel):
    text: str
    sentiment: Sentiment
    confidence: float = Field(ge=0.0, le=1.0)
    aspects: list[str]  # What aspects were mentioned
    reasoning: str

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, SentimentAnalysis)

review = """
This product completely exceeded my expectations! The build quality is
outstanding, and customer service was incredibly helpful. My only minor
complaint is the packaging could be better.
"""

result = generator(f"Analyze sentiment:\n{review}\n\nAnalysis:")

print(f"Sentiment: {result.sentiment.value}")
print(f"Confidence: {result.confidence:.2%}")
print(f"Aspects: {', '.join(result.aspects)}")
print(f"Reasoning: {result.reasoning}")
```

### Content Classification

```python
class Category(str, Enum):
    TECHNOLOGY = "technology"
    BUSINESS = "business"
    SCIENCE = "science"
    POLITICS = "politics"
    ENTERTAINMENT = "entertainment"
    SPORTS = "sports"
    HEALTH = "health"

class ArticleClassification(BaseModel):
    primary_category: Category
    secondary_categories: list[Category]
    keywords: list[str] = Field(min_items=3, max_items=10)
    target_audience: Literal["general", "expert", "beginner"]
    reading_level: Literal["elementary", "intermediate", "advanced"]

generator = outlines.generate.json(model, ArticleClassification)

article = """
Apple announced groundbreaking advancements in its AI capabilities with the
release of iOS 18. The new features leverage machine learning to significantly
improve battery life and overall device performance. Industry analysts predict
this will strengthen Apple's position in the competitive smartphone market.
"""

classification = generator(f"Classify article:\n{article}\n\nClassification:")

print(f"Primary: {classification.primary_category.value}")
print(f"Secondary: {[c.value for c in classification.secondary_categories]}")
print(f"Keywords: {classification.keywords}")
print(f"Audience: {classification.target_audience}")
```

### Intent Recognition

```python
class Intent(str, Enum):
    QUESTION = "question"
    COMPLAINT = "complaint"
    REQUEST = "request"
    FEEDBACK = "feedback"
    CANCEL = "cancel"
    UPGRADE = "upgrade"

class UserMessage(BaseModel):
    original_message: str
    intent: Intent
    urgency: Literal["low", "medium", "high", "critical"]
    department: Literal["support", "sales", "billing", "technical"]
    sentiment: Literal["positive", "neutral", "negative"]
    action_required: bool
    summary: str

generator = outlines.generate.json(model, UserMessage)

message = """
I've been charged twice for my subscription this month! This is the third
time this has happened. I need someone to fix this immediately and refund
the extra charge. Very disappointed with this service.
"""

result = generator(f"Analyze message:\n{message}\n\nAnalysis:")

print(f"Intent: {result.intent.value}")
print(f"Urgency: {result.urgency}")
print(f"Route to: {result.department}")
print(f"Action required: {result.action_required}")
print(f"Summary: {result.summary}")
```

## Form Processing

### Job Application

```python
class Education(BaseModel):
    degree: str
    field: str
    institution: str
    year: int

class Experience(BaseModel):
    title: str
    company: str
    duration: str
    responsibilities: list[str]

class JobApplication(BaseModel):
    full_name: str
    email: str
    phone: str
    education: list[Education]
    experience: list[Experience]
    skills: list[str]
    availability: str

model = outlines.models.transformers("meta-llama/Llama-3.1-8B-Instruct")
generator = outlines.generate.json(model, JobApplication)

resume_text = """
John Smith
Email: john.smith@email.com | Phone: 555-0123

EDUCATION
- BS in Computer Science, MIT, 2018
- MS in Artificial Intelligence, Stanford, 2020

EXPERIENCE
Software Engineer, Google (2020-2023)
- Developed ML pipelines for search ranking
- Led team of 5 engineers
- Improved search quality by 15%

SKILLS: Python, Machine Learning, TensorFlow, System Design

AVAILABILITY: Immediate
"""

application = generator(f"Extract job application:\n{resume_text}\n\nApplication:")

print(f"Applicant: {application.full_name}")
print(f"Email: {application.email}")
print(f"Education: {len(application.education)} degrees")
for edu in application.education:
    print(f"  - {edu.degree} in {edu.field}, {edu.institution} ({edu.year})")
print(f"Experience: {len(application.experience)} positions")
```

### Invoice Processing

```python
class InvoiceItem(BaseModel):
    description: str
    quantity: int = Field(gt=0)
    unit_price: float = Field(gt=0)
    total: float = Field(gt=0)

class Invoice(BaseModel):
    invoice_number: str
    date: str = Field(pattern=r"\d{4}-\d{2}-\d{2}")
    vendor: str
    customer: str
    items: list[InvoiceItem]
    subtotal: float = Field(gt=0)
    tax: float = Field(ge=0)
    total: float = Field(gt=0)

generator = outlines.generate.json(model, Invoice)

invoice_text = """
INVOICE #INV-2024-001
Date: 2024-01-15

From: Acme Corp
To: Smith & Co

Items:
- Widget A: 10 units @ $50.00 = $500.00
- Widget B: 5 units @ $75.00 = $375.00
- Service Fee: 1 @ $100.00 = $100.00

Subtotal: $975.00
Tax (8%): $78.00
TOTAL: $1,053.00
"""

invoice = generator(f"Extract invoice:\n{invoice_text}\n\nInvoice:")

print(f"Invoice: {invoice.invoice_number}")
print(f"From: {invoice.vendor} → To: {invoice.customer}")
print(f"Items: {len(invoice.items)}")
for item in invoice.items:
    print(f"  - {item.description}: {item.quantity} × ${item.unit_price} = ${item.total}")
print(f"Total: ${invoice.total}")
```

### Survey Responses

```python
class SurveyResponse(BaseModel):
    respondent_id: str
    completion_date: str
    satisfaction: Literal[1, 2, 3, 4, 5]
    would_recommend: bool
    favorite_features: list[str]
    improvement_areas: list[str]
    additional_comments: Optional[str] = None

generator = outlines.generate.json(model, SurveyResponse)

survey_text = """
Survey ID: RESP-12345
Completed: 2024-01-20

How satisfied are you with our product? 4 out of 5

Would you recommend to a friend? Yes

What features do you like most?
- Fast performance
- Easy to use
- Great customer support

What could we improve?
- Better documentation
- More integrations

Additional feedback: Overall great product, keep up the good work!
"""

response = generator(f"Extract survey response:\n{survey_text}\n\nResponse:")

print(f"Respondent: {response.respondent_id}")
print(f"Satisfaction: {response.satisfaction}/5")
print(f"Would recommend: {response.would_recommend}")
print(f"Favorite features: {response.favorite_features}")
print(f"Improvement areas: {response.improvement_areas}")
```

## Multi-Entity Extraction

### News Article Entities

```python
class Person(BaseModel):
    name: str
    role: Optional[str] = None
    affiliation: Optional[str] = None

class Organization(BaseModel):
    name: str
    type: Optional[str] = None

class Location(BaseModel):
    name: str
    type: Literal["city", "state", "country", "region"]

class Event(BaseModel):
    name: str
    date: Optional[str] = None
    location: Optional[str] = None

class ArticleEntities(BaseModel):
    people: list[Person]
    organizations: list[Organization]
    locations: list[Location]
    events: list[Event]
    dates: list[str]

model = outlines.models.transformers("meta-llama/Llama-3.1-8B-Instruct")
generator = outlines.generate.json(model, ArticleEntities)

article = """
Apple CEO Tim Cook met with Microsoft CEO Satya Nadella at Microsoft
headquarters in Redmond, Washington on September 15, 2024, to discuss
potential collaboration opportunities. The meeting was attended by executives
from both companies and focused on AI integration strategies. Apple's
Cupertino offices will host a follow-up meeting on October 20, 2024.
"""

entities = generator(f"Extract all entities:\n{article}\n\nEntities:")

print("People:")
for person in entities.people:
    print(f"  - {person.name} ({person.role}) @ {person.affiliation}")

print("\nOrganizations:")
for org in entities.organizations:
    print(f"  - {org.name} ({org.type})")

print("\nLocations:")
for loc in entities.locations:
    print(f"  - {loc.name} ({loc.type})")

print("\nEvents:")
for event in entities.events:
    print(f"  - {event.name} on {event.date}")
```

### Document Metadata

```python
class Author(BaseModel):
    name: str
    email: Optional[str] = None
    affiliation: Optional[str] = None

class Reference(BaseModel):
    title: str
    authors: list[str]
    year: int
    source: str

class DocumentMetadata(BaseModel):
    title: str
    authors: list[Author]
    abstract: str
    keywords: list[str]
    publication_date: str
    journal: str
    doi: Optional[str] = None
    references: list[Reference]

generator = outlines.generate.json(model, DocumentMetadata)

paper = """
Title: Advances in Neural Machine Translation

Authors:
- Dr. Jane Smith (jane@university.edu), MIT
- Prof. John Doe (jdoe@stanford.edu), Stanford University

Abstract: This paper presents novel approaches to neural machine translation
using transformer architectures. We demonstrate significant improvements in
translation quality across multiple language pairs.

Keywords: Neural Networks, Machine Translation, Transformers, NLP

Published: Journal of AI Research, 2024-03-15
DOI: 10.1234/jair.2024.001

References:
1. "Attention Is All You Need" by Vaswani et al., 2017, NeurIPS
2. "BERT: Pre-training of Deep Bidirectional Transformers" by Devlin et al., 2019, NAACL
"""

metadata = generator(f"Extract document metadata:\n{paper}\n\nMetadata:")

print(f"Title: {metadata.title}")
print(f"Authors: {', '.join(a.name for a in metadata.authors)}")
print(f"Keywords: {', '.join(metadata.keywords)}")
print(f"References: {len(metadata.references)}")
```

## Code Generation

### Python Function Generation

```python
class Parameter(BaseModel):
    name: str = Field(pattern=r"^[a-z_][a-z0-9_]*$")
    type_hint: str
    default: Optional[str] = None

class PythonFunction(BaseModel):
    function_name: str = Field(pattern=r"^[a-z_][a-z0-9_]*$")
    parameters: list[Parameter]
    return_type: str
    docstring: str
    body: list[str]  # Lines of code

model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
generator = outlines.generate.json(model, PythonFunction)

spec = "Create a function to calculate the factorial of a number"

func = generator(f"Generate Python function:\n{spec}\n\nFunction:")

print(f"def {func.function_name}(", end="")
print(", ".join(f"{p.name}: {p.type_hint}" for p in func.parameters), end="")
print(f") -> {func.return_type}:")
print(f'    """{func.docstring}"""')
for line in func.body:
    print(f"    {line}")
```

### SQL Query Generation

```python
class SQLQuery(BaseModel):
    query_type: Literal["SELECT", "INSERT", "UPDATE", "DELETE"]
    select_columns: Optional[list[str]] = None
    from_tables: list[str]
    joins: Optional[list[str]] = None
    where_conditions: Optional[list[str]] = None
    group_by: Optional[list[str]] = None
    order_by: Optional[list[str]] = None
    limit: Optional[int] = None

generator = outlines.generate.json(model, SQLQuery)

request = "Get top 10 users who made purchases in the last 30 days, ordered by total spent"

sql = generator(f"Generate SQL query:\n{request}\n\nQuery:")

print(f"Query type: {sql.query_type}")
print(f"SELECT {', '.join(sql.select_columns)}")
print(f"FROM {', '.join(sql.from_tables)}")
if sql.joins:
    for join in sql.joins:
        print(f"  {join}")
if sql.where_conditions:
    print(f"WHERE {' AND '.join(sql.where_conditions)}")
if sql.order_by:
    print(f"ORDER BY {', '.join(sql.order_by)}")
if sql.limit:
    print(f"LIMIT {sql.limit}")
```

### API Endpoint Spec

```python
class Parameter(BaseModel):
    name: str
    type: str
    required: bool
    description: str

class APIEndpoint(BaseModel):
    method: Literal["GET", "POST", "PUT", "DELETE", "PATCH"]
    path: str
    description: str
    parameters: list[Parameter]
    request_body: Optional[dict] = None
    response_schema: dict
    status_codes: dict[int, str]

generator = outlines.generate.json(model, APIEndpoint)

spec = "Create user endpoint"

endpoint = generator(f"Generate API endpoint:\n{spec}\n\nEndpoint:")

print(f"{endpoint.method} {endpoint.path}")
print(f"Description: {endpoint.description}")
print("\nParameters:")
for param in endpoint.parameters:
    req = "required" if param.required else "optional"
    print(f"  - {param.name} ({param.type}, {req}): {param.description}")
```

## Batch Processing

### Parallel Extraction

```python
def batch_extract(texts: list[str], schema: type[BaseModel], model_name: str):
    """Extract structured data from multiple texts."""
    model = outlines.models.transformers(model_name)
    generator = outlines.generate.json(model, schema)

    results = []
    for i, text in enumerate(texts):
        print(f"Processing {i+1}/{len(texts)}...", end="\r")
        result = generator(f"Extract:\n{text}\n\nData:")
        results.append(result)

    return results

class Product(BaseModel):
    name: str
    price: float
    category: str

texts = [
    "iPhone 15 Pro costs $999 in Electronics",
    "Running Shoes are $89.99 in Sports",
    "Coffee Maker priced at $49.99 in Home & Kitchen"
]

products = batch_extract(texts, Product, "microsoft/Phi-3-mini-4k-instruct")

for product in products:
    print(f"{product.name}: ${product.price} ({product.category})")
```

### CSV Processing

```python
import csv

def process_csv(csv_file: str, schema: type[BaseModel]):
    """Process CSV file and extract structured data."""
    model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
    generator = outlines.generate.json(model, schema)

    results = []
    with open(csv_file, 'r') as f:
        reader = csv.DictReader(f)
        for row in reader:
            text = " | ".join(f"{k}: {v}" for k, v in row.items())
            result = generator(f"Extract:\n{text}\n\nData:")
            results.append(result)

    return results

class Customer(BaseModel):
    name: str
    email: str
    tier: Literal["basic", "premium", "enterprise"]
    mrr: float

# customers = process_csv("customers.csv", Customer)
```

## Production Patterns

### Error Handling

```python
from pydantic import ValidationError

def safe_extract(text: str, schema: type[BaseModel], retries: int = 3):
    """Extract with error handling and retries."""
    model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
    generator = outlines.generate.json(model, schema)

    for attempt in range(retries):
        try:
            result = generator(f"Extract:\n{text}\n\nData:")
            return result
        except ValidationError as e:
            print(f"Attempt {attempt + 1} failed: {e}")
            if attempt == retries - 1:
                raise
        except Exception as e:
            print(f"Unexpected error: {e}")
            if attempt == retries - 1:
                raise

    return None
```

### Caching

```python
from functools import lru_cache
import hashlib

@lru_cache(maxsize=1000)
def cached_extract(text_hash: str, schema_name: str):
    """Cache extraction results."""
    # This would be called with actual extraction logic
    pass

def extract_with_cache(text: str, schema: type[BaseModel]):
    """Extract with caching."""
    text_hash = hashlib.md5(text.encode()).hexdigest()
    schema_name = schema.__name__

    cached_result = cached_extract(text_hash, schema_name)
    if cached_result:
        return cached_result

    # Perform actual extraction
    model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
    generator = outlines.generate.json(model, schema)
    result = generator(f"Extract:\n{text}\n\nData:")

    return result
```

### Monitoring

```python
import time
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def monitored_extract(text: str, schema: type[BaseModel]):
    """Extract with monitoring and logging."""
    start_time = time.time()

    try:
        model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
        generator = outlines.generate.json(model, schema)

        result = generator(f"Extract:\n{text}\n\nData:")

        elapsed = time.time() - start_time
        logger.info(f"Extraction succeeded in {elapsed:.2f}s")
        logger.info(f"Input length: {len(text)} chars")

        return result

    except Exception as e:
        elapsed = time.time() - start_time
        logger.error(f"Extraction failed after {elapsed:.2f}s: {e}")
        raise
```

### Rate Limiting

```python
import time
from threading import Lock

class RateLimiter:
    def __init__(self, max_requests: int, time_window: int):
        self.max_requests = max_requests
        self.time_window = time_window
        self.requests = []
        self.lock = Lock()

    def wait_if_needed(self):
        with self.lock:
            now = time.time()
            # Remove old requests
            self.requests = [r for r in self.requests if now - r < self.time_window]

            if len(self.requests) >= self.max_requests:
                sleep_time = self.time_window - (now - self.requests[0])
                time.sleep(sleep_time)
                self.requests = []

            self.requests.append(now)

def rate_limited_extract(texts: list[str], schema: type[BaseModel]):
    """Extract with rate limiting."""
    limiter = RateLimiter(max_requests=10, time_window=60)  # 10 req/min
    model = outlines.models.transformers("microsoft/Phi-3-mini-4k-instruct")
    generator = outlines.generate.json(model, schema)

    results = []
    for text in texts:
        limiter.wait_if_needed()
        result = generator(f"Extract:\n{text}\n\nData:")
        results.append(result)

    return results
```

## Resources

- **Outlines Documentation**: https://outlines-dev.github.io/outlines
- **Pydantic Documentation**: https://docs.pydantic.dev
- **GitHub Examples**: https://github.com/outlines-dev/outlines/tree/main/examples




---

## 🚀 Usage

**Reference this template:** `@skill-outlines.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
