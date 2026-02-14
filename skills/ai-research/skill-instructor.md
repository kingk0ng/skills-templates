---
id: skill-instructor
type: skill
name: instructor
description: Extract structured data from LLM responses with Pydantic validation,
  retry failed extractions automatically, parse complex JSON with type safety, and
  stream partial results with Instructor - battle-tested structured output library
category: ai-research
complexity: medium
keywords:
- api
- github
- optimization
- python
capabilities: []
token_estimate: 2345
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~2,345 -->
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




# instructor

> Extract structured data from LLM responses with Pydantic validation, retry failed extractions automatically, parse complex JSON with type safety, and stream partial results with Instructor - battle-tested structured output library

# Instructor: Structured LLM Outputs

## When to Use This Skill

Use Instructor when you need to:
- **Extract structured data** from LLM responses reliably
- **Validate outputs** against Pydantic schemas automatically
- **Retry failed extractions** with automatic error handling
- **Parse complex JSON** with type safety and validation
- **Stream partial results** for real-time processing
- **Support multiple LLM providers** with consistent API

**GitHub Stars**: 15,000+ | **Battle-tested**: 100,000+ developers

## Installation

```bash
# Base installation
pip install instructor

# With specific providers
pip install "instructor[anthropic]"  # Anthropic Claude
pip install "instructor[openai]"     # OpenAI
pip install "instructor[all]"        # All providers
```

## Quick Start

### Basic Example: Extract User Data

```python
import instructor
from pydantic import BaseModel
from anthropic import Anthropic

# Define output structure
class User(BaseModel):
    name: str
    age: int
    email: str

# Create instructor client
client = instructor.from_anthropic(Anthropic())

# Extract structured data
user = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": "John Doe is 30 years old. His email is john@example.com"
    }],
    response_model=User
)

print(user.name)   # "John Doe"
print(user.age)    # 30
print(user.email)  # "john@example.com"
```

### With OpenAI

```python
from openai import OpenAI

client = instructor.from_openai(OpenAI())

user = client.chat.completions.create(
    model="gpt-4o-mini",
    response_model=User,
    messages=[{"role": "user", "content": "Extract: Alice, 25, alice@email.com"}]
)
```

## Core Concepts

### 1. Response Models (Pydantic)

Response models define the structure and validation rules for LLM outputs.

#### Basic Model

```python
from pydantic import BaseModel, Field

class Article(BaseModel):
    title: str = Field(description="Article title")
    author: str = Field(description="Author name")
    word_count: int = Field(description="Number of words", gt=0)
    tags: list[str] = Field(description="List of relevant tags")

article = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": "Analyze this article: [article text]"
    }],
    response_model=Article
)
```

**Benefits:**
- Type safety with Python type hints
- Automatic validation (word_count > 0)
- Self-documenting with Field descriptions
- IDE autocomplete support

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

person = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": "John lives at 123 Main St, Boston, USA"
    }],
    response_model=Person
)

print(person.address.city)  # "Boston"
```

#### Optional Fields

```python
from typing import Optional

class Product(BaseModel):
    name: str
    price: float
    discount: Optional[float] = None  # Optional
    description: str = Field(default="No description")  # Default value

# LLM doesn't need to provide discount or description
```

#### Enums for Constraints

```python
from enum import Enum

class Sentiment(str, Enum):
    POSITIVE = "positive"
    NEGATIVE = "negative"
    NEUTRAL = "neutral"

class Review(BaseModel):
    text: str
    sentiment: Sentiment  # Only these 3 values allowed

review = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": "This product is amazing!"
    }],
    response_model=Review
)

print(review.sentiment)  # Sentiment.POSITIVE
```

### 2. Validation

Pydantic validates LLM outputs automatically. If validation fails, Instructor retries.

#### Built-in Validators

```python
from pydantic import Field, EmailStr, HttpUrl

class Contact(BaseModel):
    name: str = Field(min_length=2, max_length=100)
    age: int = Field(ge=0, le=120)  # 0 <= age <= 120
    email: EmailStr  # Validates email format
    website: HttpUrl  # Validates URL format

# If LLM provides invalid data, Instructor retries automatically
```

#### Custom Validators

```python
from pydantic import field_validator

class Event(BaseModel):
    name: str
    date: str
    attendees: int

    @field_validator('date')
    def validate_date(cls, v):
        """Ensure date is in YYYY-MM-DD format."""
        import re
        if not re.match(r'\d{4}-\d{2}-\d{2}', v):
            raise ValueError('Date must be YYYY-MM-DD format')
        return v

    @field_validator('attendees')
    def validate_attendees(cls, v):
        """Ensure positive attendees."""
        if v < 1:
            raise ValueError('Must have at least 1 attendee')
        return v
```

#### Model-Level Validation

```python
from pydantic import model_validator

class DateRange(BaseModel):
    start_date: str
    end_date: str

    @model_validator(mode='after')
    def check_dates(self):
        """Ensure end_date is after start_date."""
        from datetime import datetime
        start = datetime.strptime(self.start_date, '%Y-%m-%d')
        end = datetime.strptime(self.end_date, '%Y-%m-%d')

        if end < start:
            raise ValueError('end_date must be after start_date')
        return self
```

### 3. Automatic Retrying

Instructor retries automatically when validation fails, providing error feedback to the LLM.

```python
# Retries up to 3 times if validation fails
user = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": "Extract user from: John, age unknown"
    }],
    response_model=User,
    max_retries=3  # Default is 3
)

# If age can't be extracted, Instructor tells the LLM:
# "Validation error: age - field required"
# LLM tries again with better extraction
```

**How it works:**
1. LLM generates output
2. Pydantic validates
3. If invalid: Error message sent back to LLM
4. LLM tries again with error feedback
5. Repeats up to max_retries

### 4. Streaming

Stream partial results for real-time processing.

#### Streaming Partial Objects

```python
from instructor import Partial

class Story(BaseModel):
    title: str
    content: str
    tags: list[str]

# Stream partial updates as LLM generates
for partial_story in client.messages.create_partial(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": "Write a short sci-fi story"
    }],
    response_model=Story
):
    print(f"Title: {partial_story.title}")
    print(f"Content so far: {partial_story.content[:100]}...")
    # Update UI in real-time
```

#### Streaming Iterables

```python
class Task(BaseModel):
    title: str
    priority: str

# Stream list items as they're generated
tasks = client.messages.create_iterable(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": "Generate 10 project tasks"
    }],
    response_model=Task
)

for task in tasks:
    print(f"- {task.title} ({task.priority})")
    # Process each task as it arrives
```

## Provider Configuration

### Anthropic Claude

```python
import instructor
from anthropic import Anthropic

client = instructor.from_anthropic(
    Anthropic(api_key="your-api-key")
)

# Use with Claude models
response = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[...],
    response_model=YourModel
)
```

### OpenAI

```python
from openai import OpenAI

client = instructor.from_openai(
    OpenAI(api_key="your-api-key")
)

response = client.chat.completions.create(
    model="gpt-4o-mini",
    response_model=YourModel,
    messages=[...]
)
```

### Local Models (Ollama)

```python
from openai import OpenAI

# Point to local Ollama server
client = instructor.from_openai(
    OpenAI(
        base_url="http://localhost:11434/v1",
        api_key="ollama"  # Required but ignored
    ),
    mode=instructor.Mode.JSON
)

response = client.chat.completions.create(
    model="llama3.1",
    response_model=YourModel,
    messages=[...]
)
```

## Common Patterns

### Pattern 1: Data Extraction from Text

```python
class CompanyInfo(BaseModel):
    name: str
    founded_year: int
    industry: str
    employees: int
    headquarters: str

text = """
Tesla, Inc. was founded in 2003. It operates in the automotive and energy
industry with approximately 140,000 employees. The company is headquartered
in Austin, Texas.
"""

company = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": f"Extract company information from: {text}"
    }],
    response_model=CompanyInfo
)
```

### Pattern 2: Classification

```python
class Category(str, Enum):
    TECHNOLOGY = "technology"
    FINANCE = "finance"
    HEALTHCARE = "healthcare"
    EDUCATION = "education"
    OTHER = "other"

class ArticleClassification(BaseModel):
    category: Category
    confidence: float = Field(ge=0.0, le=1.0)
    keywords: list[str]

classification = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": "Classify this article: [article text]"
    }],
    response_model=ArticleClassification
)
```

### Pattern 3: Multi-Entity Extraction

```python
class Person(BaseModel):
    name: str
    role: str

class Organization(BaseModel):
    name: str
    industry: str

class Entities(BaseModel):
    people: list[Person]
    organizations: list[Organization]
    locations: list[str]

text = "Tim Cook, CEO of Apple, announced at the event in Cupertino..."

entities = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": f"Extract all entities from: {text}"
    }],
    response_model=Entities
)

for person in entities.people:
    print(f"{person.name} - {person.role}")
```

### Pattern 4: Structured Analysis

```python
class SentimentAnalysis(BaseModel):
    overall_sentiment: Sentiment
    positive_aspects: list[str]
    negative_aspects: list[str]
    suggestions: list[str]
    score: float = Field(ge=-1.0, le=1.0)

review = "The product works well but setup was confusing..."

analysis = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": f"Analyze this review: {review}"
    }],
    response_model=SentimentAnalysis
)
```

### Pattern 5: Batch Processing

```python
def extract_person(text: str) -> Person:
    return client.messages.create(
        model="claude-sonnet-4-5-20250929",
        max_tokens=1024,
        messages=[{
            "role": "user",
            "content": f"Extract person from: {text}"
        }],
        response_model=Person
    )

texts = [
    "John Doe is a 30-year-old engineer",
    "Jane Smith, 25, works in marketing",
    "Bob Johnson, age 40, software developer"
]

people = [extract_person(text) for text in texts]
```

## Advanced Features

### Union Types

```python
from typing import Union

class TextContent(BaseModel):
    type: str = "text"
    content: str

class ImageContent(BaseModel):
    type: str = "image"
    url: HttpUrl
    caption: str

class Post(BaseModel):
    title: str
    content: Union[TextContent, ImageContent]  # Either type

# LLM chooses appropriate type based on content
```

### Dynamic Models

```python
from pydantic import create_model

# Create model at runtime
DynamicUser = create_model(
    'User',
    name=(str, ...),
    age=(int, Field(ge=0)),
    email=(EmailStr, ...)
)

user = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[...],
    response_model=DynamicUser
)
```

### Custom Modes

```python
# For providers without native structured outputs
client = instructor.from_anthropic(
    Anthropic(),
    mode=instructor.Mode.JSON  # JSON mode
)

# Available modes:
# - Mode.ANTHROPIC_TOOLS (recommended for Claude)
# - Mode.JSON (fallback)
# - Mode.TOOLS (OpenAI tools)
```

### Context Management

```python
# Single-use client
with instructor.from_anthropic(Anthropic()) as client:
    result = client.messages.create(
        model="claude-sonnet-4-5-20250929",
        max_tokens=1024,
        messages=[...],
        response_model=YourModel
    )
    # Client closed automatically
```

## Error Handling

### Handling Validation Errors

```python
from pydantic import ValidationError

try:
    user = client.messages.create(
        model="claude-sonnet-4-5-20250929",
        max_tokens=1024,
        messages=[...],
        response_model=User,
        max_retries=3
    )
except ValidationError as e:
    print(f"Failed after retries: {e}")
    # Handle gracefully

except Exception as e:
    print(f"API error: {e}")
```

### Custom Error Messages

```python
class ValidatedUser(BaseModel):
    name: str = Field(description="Full name, 2-100 characters")
    age: int = Field(description="Age between 0 and 120", ge=0, le=120)
    email: EmailStr = Field(description="Valid email address")

    class Config:
        # Custom error messages
        json_schema_extra = {
            "examples": [
                {
                    "name": "John Doe",
                    "age": 30,
                    "email": "john@example.com"
                }
            ]
        }
```

## Best Practices

### 1. Clear Field Descriptions

```python
# ❌ Bad: Vague
class Product(BaseModel):
    name: str
    price: float

# ✅ Good: Descriptive
class Product(BaseModel):
    name: str = Field(description="Product name from the text")
    price: float = Field(description="Price in USD, without currency symbol")
```

### 2. Use Appropriate Validation

```python
# ✅ Good: Constrain values
class Rating(BaseModel):
    score: int = Field(ge=1, le=5, description="Rating from 1 to 5 stars")
    review: str = Field(min_length=10, description="Review text, at least 10 chars")
```

### 3. Provide Examples in Prompts

```python
messages = [{
    "role": "user",
    "content": """Extract person info from: "John, 30, engineer"

Example format:
{
  "name": "John Doe",
  "age": 30,
  "occupation": "engineer"
}"""
}]
```

### 4. Use Enums for Fixed Categories

```python
# ✅ Good: Enum ensures valid values
class Status(str, Enum):
    PENDING = "pending"
    APPROVED = "approved"
    REJECTED = "rejected"

class Application(BaseModel):
    status: Status  # LLM must choose from enum
```

### 5. Handle Missing Data Gracefully

```python
class PartialData(BaseModel):
    required_field: str
    optional_field: Optional[str] = None
    default_field: str = "default_value"

# LLM only needs to provide required_field
```

## Comparison to Alternatives

| Feature | Instructor | Manual JSON | LangChain | DSPy |
|---------|------------|-------------|-----------|------|
| Type Safety | ✅ Yes | ❌ No | ⚠️ Partial | ✅ Yes |
| Auto Validation | ✅ Yes | ❌ No | ❌ No | ⚠️ Limited |
| Auto Retry | ✅ Yes | ❌ No | ❌ No | ✅ Yes |
| Streaming | ✅ Yes | ❌ No | ✅ Yes | ❌ No |
| Multi-Provider | ✅ Yes | ⚠️ Manual | ✅ Yes | ✅ Yes |
| Learning Curve | Low | Low | Medium | High |

**When to choose Instructor:**
- Need structured, validated outputs
- Want type safety and IDE support
- Require automatic retries
- Building data extraction systems

**When to choose alternatives:**
- DSPy: Need prompt optimization
- LangChain: Building complex chains
- Manual: Simple, one-off extractions

## Resources

- **Documentation**: https://python.useinstructor.com
- **GitHub**: https://github.com/jxnl/instructor (15k+ stars)
- **Cookbook**: https://python.useinstructor.com/examples
- **Discord**: Community support available

## See Also

- `references/validation.md` - Advanced validation patterns
- `references/providers.md` - Provider-specific configuration
- `references/examples.md` - Real-world use cases




---


## 📚 Reference Materials


### Validation

# Advanced Validation Patterns

Complete guide to validation in Instructor using Pydantic.

## Table of Contents
- Built-in Validators
- Custom Field Validators
- Model-Level Validation
- Complex Validation Patterns
- Error Handling

## Built-in Validators

### Numeric Constraints

```python
from pydantic import BaseModel, Field

class Product(BaseModel):
    price: float = Field(gt=0, description="Price must be positive")
    discount: float = Field(ge=0, le=100, description="Discount 0-100%")
    quantity: int = Field(ge=1, description="At least 1 item")
    rating: float = Field(ge=0.0, le=5.0, description="Rating 0-5 stars")

# If LLM provides invalid values, automatic retry with error feedback
```

**Available constraints:**
- `gt`: Greater than
- `ge`: Greater than or equal
- `lt`: Less than
- `le`: Less than or equal
- `multiple_of`: Must be multiple of this number

### String Constraints

```python
class User(BaseModel):
    username: str = Field(
        min_length=3,
        max_length=20,
        pattern=r'^[a-zA-Z0-9_]+$',
        description="3-20 alphanumeric characters"
    )
    bio: str = Field(max_length=500, description="Bio up to 500 chars")
    status: str = Field(pattern=r'^(active|inactive|pending)$')

# pattern validates against regex
```

### Email and URL Validation

```python
from pydantic import EmailStr, HttpUrl, AnyUrl

class Contact(BaseModel):
    email: EmailStr  # Validates email format
    website: HttpUrl  # Validates HTTP/HTTPS URLs
    portfolio: AnyUrl  # Any valid URL scheme

contact = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": "Extract: john@example.com, https://example.com"
    }],
    response_model=Contact
)
```

### Date and DateTime Validation

```python
from datetime import date, datetime
from pydantic import Field, field_validator

class Event(BaseModel):
    event_date: date  # Validates date format
    created_at: datetime  # Validates datetime format
    year: int = Field(ge=1900, le=2100)

    @field_validator('event_date')
    def future_date(cls, v):
        """Ensure event is in the future."""
        if v < date.today():
            raise ValueError('Event must be in the future')
        return v
```

### List and Dict Validation

```python
class Document(BaseModel):
    tags: list[str] = Field(min_length=1, max_length=10)
    keywords: list[str] = Field(min_length=3, description="At least 3 keywords")
    metadata: dict[str, str] = Field(description="String key-value pairs")

    @field_validator('tags')
    def unique_tags(cls, v):
        """Ensure tags are unique."""
        if len(v) != len(set(v)):
            raise ValueError('Tags must be unique')
        return v
```

## Custom Field Validators

### Basic Field Validator

```python
from pydantic import field_validator

class Person(BaseModel):
    name: str
    age: int

    @field_validator('name')
    def name_must_not_be_empty(cls, v):
        """Validate name is not empty or just whitespace."""
        if not v or not v.strip():
            raise ValueError('Name cannot be empty')
        return v.strip()

    @field_validator('age')
    def age_must_be_reasonable(cls, v):
        """Validate age is between 0 and 120."""
        if v < 0 or v > 120:
            raise ValueError('Age must be between 0 and 120')
        return v
```

### Validator with Field Info

```python
from pydantic import ValidationInfo

class Article(BaseModel):
    title: str
    content: str

    @field_validator('content')
    def content_length(cls, v, info: ValidationInfo):
        """Validate content is longer than title."""
        if 'title' in info.data:
            title_len = len(info.data['title'])
            if len(v) < title_len * 2:
                raise ValueError('Content should be at least 2x title length')
        return v
```

### Multiple Fields Validation

```python
class TimeRange(BaseModel):
    start_time: str
    end_time: str

    @field_validator('start_time', 'end_time')
    def valid_time_format(cls, v):
        """Validate both times are in HH:MM format."""
        import re
        if not re.match(r'^\d{2}:\d{2}$', v):
            raise ValueError('Time must be in HH:MM format')
        return v
```

### Transform and Validate

```python
class URL(BaseModel):
    url: str

    @field_validator('url')
    def normalize_url(cls, v):
        """Add https:// if missing."""
        if not v.startswith(('http://', 'https://')):
            v = f'https://{v}'
        return v
```

## Model-Level Validation

### Cross-Field Validation

```python
from pydantic import model_validator

class DateRange(BaseModel):
    start_date: str
    end_date: str

    @model_validator(mode='after')
    def check_dates(self):
        """Ensure end_date is after start_date."""
        from datetime import datetime
        start = datetime.strptime(self.start_date, '%Y-%m-%d')
        end = datetime.strptime(self.end_date, '%Y-%m-%d')

        if end < start:
            raise ValueError('end_date must be after start_date')
        return self

class PriceRange(BaseModel):
    min_price: float
    max_price: float

    @model_validator(mode='after')
    def check_price_range(self):
        """Ensure max > min."""
        if self.max_price <= self.min_price:
            raise ValueError('max_price must be greater than min_price')
        return self
```

### Conditional Validation

```python
class Order(BaseModel):
    order_type: str  # "standard" or "express"
    delivery_date: str
    delivery_time: Optional[str] = None

    @model_validator(mode='after')
    def check_delivery_time(self):
        """Express orders need delivery time."""
        if self.order_type == "express" and not self.delivery_time:
            raise ValueError('Express orders require delivery_time')
        return self
```

### Complex Business Logic

```python
class Discount(BaseModel):
    code: str
    percentage: float = Field(ge=0, le=100)
    min_purchase: float = Field(ge=0)
    max_discount: float = Field(ge=0)

    @model_validator(mode='after')
    def validate_discount(self):
        """Ensure discount logic is sound."""
        # Max discount can't exceed percentage of min_purchase
        theoretical_max = (self.percentage / 100) * self.min_purchase
        if self.max_discount > theoretical_max:
            self.max_discount = theoretical_max
        return self
```

## Complex Validation Patterns

### Nested Model Validation

```python
class Address(BaseModel):
    street: str
    city: str
    country: str
    postal_code: str

    @field_validator('postal_code')
    def validate_postal_code(cls, v, info: ValidationInfo):
        """Validate postal code format based on country."""
        if 'country' in info.data:
            country = info.data['country']
            if country == "USA":
                import re
                if not re.match(r'^\d{5}(-\d{4})?$', v):
                    raise ValueError('Invalid US postal code')
            elif country == "Canada":
                if not re.match(r'^[A-Z]\d[A-Z] \d[A-Z]\d$', v):
                    raise ValueError('Invalid Canadian postal code')
        return v

class Person(BaseModel):
    name: str
    address: Address

# Nested validation runs automatically
```

### List of Models

```python
class Task(BaseModel):
    title: str = Field(min_length=1)
    priority: int = Field(ge=1, le=5)

class Project(BaseModel):
    name: str
    tasks: list[Task] = Field(min_length=1, description="At least 1 task")

    @field_validator('tasks')
    def at_least_one_high_priority(cls, v):
        """Ensure at least one task has priority >= 4."""
        if not any(task.priority >= 4 for task in v):
            raise ValueError('Project needs at least one high-priority task')
        return v
```

### Union Type Validation

```python
from typing import Union

class TextBlock(BaseModel):
    type: str = "text"
    content: str = Field(min_length=1)

class ImageBlock(BaseModel):
    type: str = "image"
    url: HttpUrl
    alt_text: str

class Page(BaseModel):
    title: str
    blocks: list[Union[TextBlock, ImageBlock]]

    @field_validator('blocks')
    def validate_block_types(cls, v):
        """Ensure first block is TextBlock."""
        if v and not isinstance(v[0], TextBlock):
            raise ValueError('First block must be text')
        return v
```

### Dependent Fields

```python
class Subscription(BaseModel):
    plan: str  # "free", "pro", "enterprise"
    max_users: int
    features: list[str]

    @model_validator(mode='after')
    def validate_plan_limits(self):
        """Enforce plan-specific limits."""
        limits = {
            "free": {"max_users": 1, "required_features": ["basic"]},
            "pro": {"max_users": 10, "required_features": ["basic", "advanced"]},
            "enterprise": {"max_users": 999, "required_features": ["basic", "advanced", "premium"]}
        }

        if self.plan in limits:
            limit = limits[self.plan]

            if self.max_users > limit["max_users"]:
                raise ValueError(f'{self.plan} plan limited to {limit["max_users"]} users')

            for feature in limit["required_features"]:
                if feature not in self.features:
                    raise ValueError(f'{self.plan} plan requires {feature} feature')

        return self
```

## Error Handling

### Graceful Degradation

```python
class OptionalExtraction(BaseModel):
    # Required fields
    title: str

    # Optional fields with defaults
    author: Optional[str] = None
    date: Optional[str] = None
    tags: list[str] = Field(default_factory=list)

# LLM can succeed even if it can't extract everything
```

### Partial Validation

```python
from pydantic import ValidationError

def extract_with_fallback(text: str):
    """Try full extraction, fall back to partial."""
    try:
        # Try full extraction
        return client.messages.create(
            model="claude-sonnet-4-5-20250929",
            max_tokens=1024,
            messages=[{"role": "user", "content": text}],
            response_model=FullModel
        )
    except ValidationError:
        # Fall back to partial model
        return client.messages.create(
            model="claude-sonnet-4-5-20250929",
            max_tokens=1024,
            messages=[{"role": "user", "content": text}],
            response_model=PartialModel
        )
```

### Validation Error Inspection

```python
from pydantic import ValidationError

try:
    result = client.messages.create(
        model="claude-sonnet-4-5-20250929",
        max_tokens=1024,
        messages=[...],
        response_model=MyModel,
        max_retries=3
    )
except ValidationError as e:
    # Inspect specific errors
    for error in e.errors():
        field = error['loc'][0]
        message = error['msg']
        print(f"Field '{field}' failed: {message}")

        # Custom handling per field
        if field == 'email':
            # Handle email validation failure
            pass
```

### Custom Error Messages

```python
class DetailedModel(BaseModel):
    name: str = Field(
        min_length=2,
        max_length=100,
        description="Name between 2-100 characters"
    )
    age: int = Field(
        ge=0,
        le=120,
        description="Age between 0 and 120 years"
    )

    @field_validator('name')
    def validate_name(cls, v):
        """Provide helpful error message."""
        if not v.strip():
            raise ValueError(
                'Name cannot be empty. '
                'Please provide a valid name from the text.'
            )
        return v

# When validation fails, LLM sees these helpful messages
```

## Validation Best Practices

### 1. Be Specific

```python
# ❌ Bad: Vague validation
class Item(BaseModel):
    name: str

# ✅ Good: Specific constraints
class Item(BaseModel):
    name: str = Field(
        min_length=1,
        max_length=200,
        description="Item name, 1-200 characters"
    )
```

### 2. Provide Context

```python
# ✅ Good: Explain why validation failed
@field_validator('price')
def validate_price(cls, v):
    if v <= 0:
        raise ValueError(
            'Price must be positive. '
            'Extract numeric price from text without currency symbols.'
        )
    return v
```

### 3. Use Enums for Fixed Sets

```python
# ❌ Bad: String validation
status: str

@field_validator('status')
def validate_status(cls, v):
    if v not in ['active', 'inactive', 'pending']:
        raise ValueError('Invalid status')
    return v

# ✅ Good: Enum
class Status(str, Enum):
    ACTIVE = "active"
    INACTIVE = "inactive"
    PENDING = "pending"

status: Status  # Validation automatic
```

### 4. Balance Strictness

```python
# Too strict: May fail unnecessarily
class StrictModel(BaseModel):
    date: str = Field(pattern=r'^\d{4}-\d{2}-\d{2}$')
    # Fails if LLM uses "2024-1-5" instead of "2024-01-05"

# Better: Normalize in validator
class FlexibleModel(BaseModel):
    date: str

    @field_validator('date')
    def normalize_date(cls, v):
        from datetime import datetime
        # Parse flexible formats
        for fmt in ['%Y-%m-%d', '%Y/%m/%d', '%m/%d/%Y']:
            try:
                dt = datetime.strptime(v, fmt)
                return dt.strftime('%Y-%m-%d')  # Normalize
            except ValueError:
                continue
        raise ValueError('Invalid date format')
```

### 5. Test Validation

```python
# Test your validators with edge cases
def test_validation():
    # Should succeed
    valid = MyModel(field="valid_value")

    # Should fail
    try:
        invalid = MyModel(field="invalid")
        assert False, "Should have raised ValidationError"
    except ValidationError:
        pass  # Expected

# Run tests before using in production
```

## Advanced Techniques

### Conditional Required Fields

```python
from typing import Optional

class ConditionalModel(BaseModel):
    type: str
    detail_a: Optional[str] = None
    detail_b: Optional[str] = None

    @model_validator(mode='after')
    def check_required_details(self):
        """Require different fields based on type."""
        if self.type == "type_a" and not self.detail_a:
            raise ValueError('type_a requires detail_a')
        if self.type == "type_b" and not self.detail_b:
            raise ValueError('type_b requires detail_b')
        return self
```

### Validation with External Data

```python
class Product(BaseModel):
    sku: str
    name: str

    @field_validator('sku')
    def validate_sku(cls, v):
        """Check SKU exists in database."""
        # Query database or API
        if not database.sku_exists(v):
            raise ValueError(f'SKU {v} not found in catalog')
        return v
```

### Progressive Validation

```python
# Start with loose validation
class Stage1(BaseModel):
    data: str  # Any string

# Then strict validation
class Stage2(BaseModel):
    data: str = Field(pattern=r'^[A-Z]{3}-\d{6}$')

# Use Stage1 for initial extraction
# Use Stage2 for final validation
```

## Resources

- **Pydantic Docs**: https://docs.pydantic.dev/latest/concepts/validators/
- **Instructor Examples**: https://python.useinstructor.com/examples




### Providers

# Provider Configuration

Guide to using Instructor with different LLM providers.

## Anthropic Claude

```python
import instructor
from anthropic import Anthropic

# Basic setup
client = instructor.from_anthropic(Anthropic())

# With API key
client = instructor.from_anthropic(
    Anthropic(api_key="your-api-key")
)

# Recommended mode
client = instructor.from_anthropic(
    Anthropic(),
    mode=instructor.Mode.ANTHROPIC_TOOLS
)

# Usage
result = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{"role": "user", "content": "..."}],
    response_model=YourModel
)
```

## OpenAI

```python
from openai import OpenAI

client = instructor.from_openai(OpenAI())

result = client.chat.completions.create(
    model="gpt-4o-mini",
    response_model=YourModel,
    messages=[{"role": "user", "content": "..."}]
)
```

## Local Models (Ollama)

```python
client = instructor.from_openai(
    OpenAI(
        base_url="http://localhost:11434/v1",
        api_key="ollama"
    ),
    mode=instructor.Mode.JSON
)

result = client.chat.completions.create(
    model="llama3.1",
    response_model=YourModel,
    messages=[...]
)
```

## Modes

- `Mode.ANTHROPIC_TOOLS`: Recommended for Claude
- `Mode.TOOLS`: OpenAI function calling
- `Mode.JSON`: Fallback for unsupported providers




### Examples

# Real-World Examples

Practical examples of using Instructor for structured data extraction.

## Data Extraction

```python
class CompanyInfo(BaseModel):
    name: str
    founded: int
    industry: str
    employees: int

text = "Apple was founded in 1976 in the technology industry with 164,000 employees."

company = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{"role": "user", "content": f"Extract: {text}"}],
    response_model=CompanyInfo
)
```

## Classification

```python
class Sentiment(str, Enum):
    POSITIVE = "positive"
    NEGATIVE = "negative"
    NEUTRAL = "neutral"

class Review(BaseModel):
    sentiment: Sentiment
    confidence: float = Field(ge=0.0, le=1.0)

review = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{"role": "user", "content": "This product is amazing!"}],
    response_model=Review
)
```

## Multi-Entity Extraction

```python
class Person(BaseModel):
    name: str
    role: str

class Entities(BaseModel):
    people: list[Person]
    organizations: list[str]
    locations: list[str]

entities = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Tim Cook, CEO of Apple, spoke in Cupertino..."}],
    response_model=Entities
)
```

## Structured Analysis

```python
class Analysis(BaseModel):
    summary: str
    key_points: list[str]
    sentiment: Sentiment
    actionable_items: list[str]

analysis = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Analyze: [long text]"}],
    response_model=Analysis
)
```

## Batch Processing

```python
texts = ["text1", "text2", "text3"]
results = [
    client.messages.create(
        model="claude-sonnet-4-5-20250929",
        max_tokens=1024,
        messages=[{"role": "user", "content": text}],
        response_model=YourModel
    )
    for text in texts
]
```

## Streaming

```python
for partial in client.messages.create_partial(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Generate report..."}],
    response_model=Report
):
    print(f"Progress: {partial.title}")
    # Update UI in real-time
```




---

## 🚀 Usage

**Reference this template:** `@skill-instructor.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
