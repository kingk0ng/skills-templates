---
id: skill-crewai-multi-agent
type: skill
name: crewai-multi-agent
description: Multi-agent orchestration framework for autonomous AI collaboration.
  Use when building teams of specialized agents working together on complex tasks,
  when you need role-based agent collaboration with memory, or for production workflows
  requiring sequential/hierarchical execution. Built without LangChain dependencies
  for lean, fast execution.
category: ai-research
complexity: medium
keywords:
- api
- github
- python
capabilities: []
token_estimate: 1831
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,831 -->
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




# crewai-multi-agent

> Multi-agent orchestration framework for autonomous AI collaboration. Use when building teams of specialized agents working together on complex tasks, when you need role-based agent collaboration with memory, or for production workflows requiring sequential/hierarchical execution. Built without LangChain dependencies for lean, fast execution.

# CrewAI - Multi-Agent Orchestration Framework

Build teams of autonomous AI agents that collaborate to solve complex tasks.

## When to use CrewAI

**Use CrewAI when:**
- Building multi-agent systems with specialized roles
- Need autonomous collaboration between agents
- Want role-based task delegation (researcher, writer, analyst)
- Require sequential or hierarchical process execution
- Building production workflows with memory and observability
- Need simpler setup than LangChain/LangGraph

**Key features:**
- **Standalone**: No LangChain dependencies, lean footprint
- **Role-based**: Agents have roles, goals, and backstories
- **Dual paradigm**: Crews (autonomous) + Flows (event-driven)
- **50+ tools**: Web scraping, search, databases, AI services
- **Memory**: Short-term, long-term, and entity memory
- **Production-ready**: Tracing, enterprise features

**Use alternatives instead:**
- **LangChain**: General-purpose LLM apps, RAG pipelines
- **LangGraph**: Complex stateful workflows with cycles
- **AutoGen**: Microsoft ecosystem, multi-agent conversations
- **LlamaIndex**: Document Q&A, knowledge retrieval

## Quick start

### Installation

```bash
# Core framework
pip install crewai

# With 50+ built-in tools
pip install 'crewai[tools]'
```

### Create project with CLI

```bash
# Create new crew project
crewai create crew my_project
cd my_project

# Install dependencies
crewai install

# Run the crew
crewai run
```

### Simple crew (code-only)

```python
from crewai import Agent, Task, Crew, Process

# 1. Define agents
researcher = Agent(
    role="Senior Research Analyst",
    goal="Discover cutting-edge developments in AI",
    backstory="You are an expert analyst with a keen eye for emerging trends.",
    verbose=True
)

writer = Agent(
    role="Technical Writer",
    goal="Create clear, engaging content about technical topics",
    backstory="You excel at explaining complex concepts to general audiences.",
    verbose=True
)

# 2. Define tasks
research_task = Task(
    description="Research the latest developments in {topic}. Find 5 key trends.",
    expected_output="A detailed report with 5 bullet points on key trends.",
    agent=researcher
)

write_task = Task(
    description="Write a blog post based on the research findings.",
    expected_output="A 500-word blog post in markdown format.",
    agent=writer,
    context=[research_task]  # Uses research output
)

# 3. Create and run crew
crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, write_task],
    process=Process.sequential,  # Tasks run in order
    verbose=True
)

# 4. Execute
result = crew.kickoff(inputs={"topic": "AI Agents"})
print(result.raw)
```

## Core concepts

### Agents - Autonomous workers

```python
from crewai import Agent

agent = Agent(
    role="Data Scientist",                    # Job title/role
    goal="Analyze data to find insights",     # What they aim to achieve
    backstory="PhD in statistics...",         # Background context
    llm="gpt-4o",                             # LLM to use
    tools=[],                                 # Tools available
    memory=True,                              # Enable memory
    verbose=True,                             # Show reasoning
    allow_delegation=True,                    # Can delegate to others
    max_iter=15,                              # Max reasoning iterations
    max_rpm=10                                # Rate limit
)
```

### Tasks - Units of work

```python
from crewai import Task

task = Task(
    description="Analyze the sales data for Q4 2024. {context}",
    expected_output="A summary report with key metrics and trends.",
    agent=analyst,                            # Assigned agent
    context=[previous_task],                  # Input from other tasks
    output_file="report.md",                  # Save to file
    async_execution=False,                    # Run synchronously
    human_input=False                         # No human approval needed
)
```

### Crews - Teams of agents

```python
from crewai import Crew, Process

crew = Crew(
    agents=[researcher, writer, editor],      # Team members
    tasks=[research, write, edit],            # Tasks to complete
    process=Process.sequential,               # Or Process.hierarchical
    verbose=True,
    memory=True,                              # Enable crew memory
    cache=True,                               # Cache tool results
    max_rpm=10,                               # Rate limit
    share_crew=False                          # Opt-in telemetry
)

# Execute with inputs
result = crew.kickoff(inputs={"topic": "AI trends"})

# Access results
print(result.raw)                             # Final output
print(result.tasks_output)                    # All task outputs
print(result.token_usage)                     # Token consumption
```

## Process types

### Sequential (default)

Tasks execute in order, each agent completing their task before the next:

```python
crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, write_task],
    process=Process.sequential  # Task 1 → Task 2 → Task 3
)
```

### Hierarchical

Auto-creates a manager agent that delegates and coordinates:

```python
crew = Crew(
    agents=[researcher, writer, analyst],
    tasks=[research_task, write_task, analyze_task],
    process=Process.hierarchical,  # Manager delegates tasks
    manager_llm="gpt-4o"           # LLM for manager
)
```

## Using tools

### Built-in tools (50+)

```bash
pip install 'crewai[tools]'
```

```python
from crewai_tools import (
    SerperDevTool,           # Web search
    ScrapeWebsiteTool,       # Web scraping
    FileReadTool,            # Read files
    PDFSearchTool,           # Search PDFs
    WebsiteSearchTool,       # Search websites
    CodeDocsSearchTool,      # Search code docs
    YoutubeVideoSearchTool,  # Search YouTube
)

# Assign tools to agent
researcher = Agent(
    role="Researcher",
    goal="Find accurate information",
    backstory="Expert at finding data online.",
    tools=[SerperDevTool(), ScrapeWebsiteTool()]
)
```

### Custom tools

```python
from crewai.tools import BaseTool
from pydantic import Field

class CalculatorTool(BaseTool):
    name: str = "Calculator"
    description: str = "Performs mathematical calculations. Input: expression"

    def _run(self, expression: str) -> str:
        try:
            result = eval(expression)
            return f"Result: {result}"
        except Exception as e:
            return f"Error: {str(e)}"

# Use custom tool
agent = Agent(
    role="Analyst",
    goal="Perform calculations",
    tools=[CalculatorTool()]
)
```

## YAML configuration (recommended)

### Project structure

```
my_project/
├── src/my_project/
│   ├── config/
│   │   ├── agents.yaml    # Agent definitions
│   │   └── tasks.yaml     # Task definitions
│   ├── crew.py            # Crew assembly
│   └── main.py            # Entry point
└── pyproject.toml
```

### agents.yaml

```yaml
researcher:
  role: "{topic} Senior Data Researcher"
  goal: "Uncover cutting-edge developments in {topic}"
  backstory: >
    You're a seasoned researcher with a knack for uncovering
    the latest developments in {topic}. Known for your ability
    to find relevant information and present it clearly.

reporting_analyst:
  role: "Reporting Analyst"
  goal: "Create detailed reports based on research data"
  backstory: >
    You're a meticulous analyst who transforms raw data into
    actionable insights through well-structured reports.
```

### tasks.yaml

```yaml
research_task:
  description: >
    Conduct thorough research about {topic}.
    Find the most relevant information for {year}.
  expected_output: >
    A list with 10 bullet points of the most relevant
    information about {topic}.
  agent: researcher

reporting_task:
  description: >
    Review the research and create a comprehensive report.
    Focus on key findings and recommendations.
  expected_output: >
    A detailed report in markdown format with executive
    summary, findings, and recommendations.
  agent: reporting_analyst
  output_file: report.md
```

### crew.py

```python
from crewai import Agent, Crew, Process, Task
from crewai.project import CrewBase, agent, crew, task
from crewai_tools import SerperDevTool

@CrewBase
class MyProjectCrew:
    """My Project crew"""

    @agent
    def researcher(self) -> Agent:
        return Agent(
            config=self.agents_config['researcher'],
            tools=[SerperDevTool()],
            verbose=True
        )

    @agent
    def reporting_analyst(self) -> Agent:
        return Agent(
            config=self.agents_config['reporting_analyst'],
            verbose=True
        )

    @task
    def research_task(self) -> Task:
        return Task(config=self.tasks_config['research_task'])

    @task
    def reporting_task(self) -> Task:
        return Task(
            config=self.tasks_config['reporting_task'],
            output_file='report.md'
        )

    @crew
    def crew(self) -> Crew:
        return Crew(
            agents=self.agents,
            tasks=self.tasks,
            process=Process.sequential,
            verbose=True
        )
```

### main.py

```python
from my_project.crew import MyProjectCrew

def run():
    inputs = {
        'topic': 'AI Agents',
        'year': 2025
    }
    MyProjectCrew().crew().kickoff(inputs=inputs)

if __name__ == "__main__":
    run()
```

## Flows - Event-driven orchestration

For complex workflows with conditional logic, use Flows:

```python
from crewai.flow.flow import Flow, listen, start, router
from pydantic import BaseModel

class MyState(BaseModel):
    confidence: float = 0.0

class MyFlow(Flow[MyState]):
    @start()
    def gather_data(self):
        return {"data": "collected"}

    @listen(gather_data)
    def analyze(self, data):
        self.state.confidence = 0.85
        return analysis_crew.kickoff(inputs=data)

    @router(analyze)
    def decide(self):
        return "high" if self.state.confidence > 0.8 else "low"

    @listen("high")
    def generate_report(self):
        return report_crew.kickoff()

# Run flow
flow = MyFlow()
result = flow.kickoff()
```

See [Flows Guide](references/flows.md) for complete documentation.

## Memory system

```python
# Enable all memory types
crew = Crew(
    agents=[researcher],
    tasks=[research_task],
    memory=True,           # Enable memory
    embedder={             # Custom embeddings
        "provider": "openai",
        "config": {"model": "text-embedding-3-small"}
    }
)
```

**Memory types:** Short-term (ChromaDB), Long-term (SQLite), Entity (ChromaDB)

## LLM providers

```python
from crewai import LLM

llm = LLM(model="gpt-4o")                              # OpenAI (default)
llm = LLM(model="claude-sonnet-4-5-20250929")                       # Anthropic
llm = LLM(model="ollama/llama3.1", base_url="http://localhost:11434")  # Local
llm = LLM(model="azure/gpt-4o", base_url="https://...")              # Azure

agent = Agent(role="Analyst", goal="Analyze data", llm=llm)
```

## CrewAI vs alternatives

| Feature | CrewAI | LangChain | LangGraph |
|---------|--------|-----------|-----------|
| **Best for** | Multi-agent teams | General LLM apps | Stateful workflows |
| **Learning curve** | Low | Medium | Higher |
| **Agent paradigm** | Role-based | Tool-based | Graph-based |
| **Memory** | Built-in | Plugin-based | Custom |

## Best practices

1. **Clear roles** - Each agent should have a distinct specialty
2. **YAML config** - Better organization for larger projects
3. **Enable memory** - Improves context across tasks
4. **Set max_iter** - Prevent infinite loops (default 15)
5. **Limit tools** - 3-5 tools per agent max
6. **Rate limiting** - Set max_rpm to avoid API limits

## Common issues

**Agent stuck in loop:**
```python
agent = Agent(
    role="...",
    max_iter=10,           # Limit iterations
    max_rpm=5              # Rate limit
)
```

**Task not using context:**
```python
task2 = Task(
    description="...",
    context=[task1],       # Explicitly pass context
    agent=writer
)
```

**Memory errors:**
```python
# Use environment variable for storage
import os
os.environ["CREWAI_STORAGE_DIR"] = "./my_storage"
```

## References

- **[Flows Guide](references/flows.md)** - Event-driven workflows, state management
- **[Tools Guide](references/tools.md)** - Built-in tools, custom tools, MCP
- **[Troubleshooting](references/troubleshooting.md)** - Common issues, debugging

## Resources

- **GitHub**: https://github.com/crewAIInc/crewAI (25k+ stars)
- **Docs**: https://docs.crewai.com
- **Tools**: https://github.com/crewAIInc/crewAI-tools
- **Examples**: https://github.com/crewAIInc/crewAI-examples
- **Version**: 1.2.0+
- **License**: MIT


---


## 📚 Reference Materials


### Flows

# CrewAI Flows Guide

## Overview

Flows provide event-driven orchestration with precise control over execution paths, state management, and conditional branching. Use Flows when you need more control than Crews provide.

## When to Use Flows vs Crews

| Scenario | Use Crews | Use Flows |
|----------|-----------|-----------|
| Simple multi-agent collaboration | ✅ | |
| Sequential/hierarchical tasks | ✅ | |
| Conditional branching | | ✅ |
| Complex state management | | ✅ |
| Event-driven workflows | | ✅ |
| Hybrid (Crews inside Flow steps) | | ✅ |

## Flow Basics

### Creating a Flow

```python
from crewai.flow.flow import Flow, listen, start, router, or_, and_
from pydantic import BaseModel

# Define state model
class MyState(BaseModel):
    counter: int = 0
    data: str = ""
    results: list = []

# Create flow with typed state
class MyFlow(Flow[MyState]):

    @start()
    def initialize(self):
        """Entry point - runs first"""
        self.state.counter = 1
        return {"initialized": True}

    @listen(initialize)
    def process(self, data):
        """Runs after initialize completes"""
        self.state.counter += 1
        return f"Processed: {data}"

# Run flow
flow = MyFlow()
result = flow.kickoff()
print(flow.state.counter)  # Access final state
```

### Flow Decorators

#### @start() - Entry Point

```python
@start()
def begin(self):
    """First method(s) to execute"""
    return {"status": "started"}

# Multiple start points (run in parallel)
@start()
def start_a(self):
    return "A"

@start()
def start_b(self):
    return "B"
```

#### @listen() - Event Trigger

```python
# Listen to single method
@listen(initialize)
def after_init(self, result):
    """Runs when initialize completes"""
    return process(result)

# Listen to string name
@listen("high_confidence")
def handle_high(self):
    """Runs when router returns 'high_confidence'"""
    pass
```

#### @router() - Conditional Branching

```python
@router(analyze)
def decide_path(self):
    """Returns string to route to specific listener"""
    if self.state.confidence > 0.8:
        return "high_confidence"
    elif self.state.confidence > 0.5:
        return "medium_confidence"
    return "low_confidence"

@listen("high_confidence")
def handle_high(self):
    pass

@listen("medium_confidence")
def handle_medium(self):
    pass

@listen("low_confidence")
def handle_low(self):
    pass
```

#### or_() and and_() - Conditional Combinations

```python
from crewai.flow.flow import or_, and_

# Triggers when EITHER condition is met
@listen(or_("success", "partial_success"))
def handle_any_success(self):
    pass

# Triggers when BOTH conditions are met
@listen(and_(task_a, task_b))
def after_both_complete(self):
    pass
```

## State Management

### Pydantic State Model

```python
from pydantic import BaseModel, Field
from typing import Optional

class WorkflowState(BaseModel):
    # Required fields
    input_data: str

    # Optional with defaults
    processed: bool = False
    confidence: float = 0.0
    results: list = Field(default_factory=list)
    error: Optional[str] = None

    # Nested models
    metadata: dict = Field(default_factory=dict)

class MyFlow(Flow[WorkflowState]):
    @start()
    def init(self):
        # Access state
        print(self.state.input_data)

        # Modify state
        self.state.processed = True
        self.state.results.append("item")
        self.state.metadata["timestamp"] = "2025-01-01"
```

### State Initialization

```python
# Initialize with inputs
flow = MyFlow()
result = flow.kickoff(inputs={"input_data": "my data"})

# Or set state before kickoff
flow.state.input_data = "my data"
result = flow.kickoff()
```

## Integrating Crews in Flows

### Crew as Flow Step

```python
from crewai import Crew, Agent, Task, Process
from crewai.flow.flow import Flow, listen, start

class ResearchFlow(Flow[ResearchState]):

    @start()
    def gather_requirements(self):
        return {"topic": self.state.topic}

    @listen(gather_requirements)
    def run_research_crew(self, requirements):
        # Define crew
        researcher = Agent(
            role="Researcher",
            goal="Research {topic}",
            backstory="Expert researcher"
        )

        research_task = Task(
            description="Research {topic} thoroughly",
            expected_output="Detailed findings",
            agent=researcher
        )

        crew = Crew(
            agents=[researcher],
            tasks=[research_task],
            process=Process.sequential
        )

        # Execute crew within flow
        result = crew.kickoff(inputs=requirements)
        self.state.research_output = result.raw
        return result

    @listen(run_research_crew)
    def process_results(self, crew_result):
        # Process crew output
        return {"summary": self.state.research_output[:500]}
```

### Multiple Crews in Flow

```python
class MultiCrewFlow(Flow[MultiState]):

    @start()
    def init(self):
        return {"ready": True}

    @listen(init)
    def research_phase(self, data):
        return research_crew.kickoff(inputs={"topic": self.state.topic})

    @listen(research_phase)
    def writing_phase(self, research):
        return writing_crew.kickoff(inputs={"research": research.raw})

    @listen(writing_phase)
    def review_phase(self, draft):
        return review_crew.kickoff(inputs={"draft": draft.raw})
```

## Complex Flow Patterns

### Parallel Execution

```python
class ParallelFlow(Flow[ParallelState]):

    @start()
    def init(self):
        return {"ready": True}

    # These run in parallel after init
    @listen(init)
    def branch_a(self, data):
        return crew_a.kickoff()

    @listen(init)
    def branch_b(self, data):
        return crew_b.kickoff()

    @listen(init)
    def branch_c(self, data):
        return crew_c.kickoff()

    # Waits for all branches
    @listen(and_(branch_a, branch_b, branch_c))
    def merge_results(self):
        return {
            "a": self.state.result_a,
            "b": self.state.result_b,
            "c": self.state.result_c
        }
```

### Error Handling

```python
class RobustFlow(Flow[RobustState]):

    @start()
    def risky_operation(self):
        try:
            result = perform_operation()
            self.state.success = True
            return result
        except Exception as e:
            self.state.error = str(e)
            self.state.success = False
            return {"error": str(e)}

    @router(risky_operation)
    def handle_result(self):
        if self.state.success:
            return "success"
        return "failure"

    @listen("success")
    def continue_flow(self):
        pass

    @listen("failure")
    def handle_error(self):
        # Retry, alert, or graceful degradation
        pass
```

### Loops and Retries

```python
class RetryFlow(Flow[RetryState]):

    @start()
    def attempt_task(self):
        result = try_operation()
        self.state.attempts += 1
        self.state.last_result = result
        return result

    @router(attempt_task)
    def check_result(self):
        if self.state.last_result.get("success"):
            return "success"
        if self.state.attempts >= 3:
            return "max_retries"
        return "retry"

    @listen("retry")
    def retry_task(self):
        # Recursively call start
        return self.attempt_task()

    @listen("success")
    def finish(self):
        return {"completed": True}

    @listen("max_retries")
    def fail(self):
        return {"error": "Max retries exceeded"}
```

## Flow Visualization

```bash
# Create flow project
crewai create flow my_flow
cd my_flow

# Plot flow diagram
crewai flow plot
```

This generates a visual representation of your flow's execution paths.

## Best Practices

1. **Use typed state** - Pydantic models catch errors early
2. **Keep methods focused** - Single responsibility per method
3. **Clear routing logic** - Router decisions should be simple
4. **Handle errors** - Add error paths for robustness
5. **Test incrementally** - Test each path independently
6. **Use logging** - Add verbose output for debugging
7. **Manage state carefully** - Don't mutate state in unexpected ways

## Common Patterns

### Data Pipeline

```python
class DataPipeline(Flow[PipelineState]):
    @start()
    def extract(self):
        return extract_data()

    @listen(extract)
    def transform(self, data):
        return transform_data(data)

    @listen(transform)
    def load(self, data):
        return load_data(data)
```

### Approval Workflow

```python
class ApprovalFlow(Flow[ApprovalState]):
    @start()
    def create_request(self):
        return create_request()

    @listen(create_request)
    def review(self, request):
        return review_crew.kickoff(inputs=request)

    @router(review)
    def approval_decision(self):
        if self.state.approved:
            return "approved"
        return "rejected"

    @listen("approved")
    def execute(self):
        return execute_request()

    @listen("rejected")
    def notify_rejection(self):
        return send_notification()
```

### Multi-Stage Analysis

```python
class AnalysisFlow(Flow[AnalysisState]):
    @start()
    def collect_data(self):
        return data_collection_crew.kickoff()

    @listen(collect_data)
    def analyze(self, data):
        return analysis_crew.kickoff(inputs={"data": data})

    @router(analyze)
    def quality_check(self):
        if self.state.confidence > 0.8:
            return "high_quality"
        return "needs_review"

    @listen("high_quality")
    def generate_report(self):
        return report_crew.kickoff()

    @listen("needs_review")
    def request_human_review(self):
        self.state.needs_human = True
        return "Awaiting human review"
```




### Tools

# CrewAI Tools Guide

## Built-in Tools

Install the tools package:

```bash
pip install 'crewai[tools]'
```

### Search Tools

```python
from crewai_tools import (
    SerperDevTool,         # Google search via Serper
    TavilySearchTool,      # Tavily search API
    BraveSearchTool,       # Brave search
    EXASearchTool,         # EXA semantic search
)

# Serper (requires SERPER_API_KEY)
search = SerperDevTool()

# Tavily (requires TAVILY_API_KEY)
search = TavilySearchTool()

# Use in agent
researcher = Agent(
    role="Researcher",
    goal="Find information",
    tools=[SerperDevTool()]
)
```

### Web Scraping Tools

```python
from crewai_tools import (
    ScrapeWebsiteTool,           # Basic scraping
    FirecrawlScrapeWebsiteTool,  # Firecrawl API
    SeleniumScrapingTool,        # Browser automation
    SpiderTool,                  # Spider.cloud
)

# Basic scraping
scraper = ScrapeWebsiteTool()

# Firecrawl (requires FIRECRAWL_API_KEY)
scraper = FirecrawlScrapeWebsiteTool()

# Selenium (requires chromedriver)
scraper = SeleniumScrapingTool()

agent = Agent(
    role="Web Analyst",
    goal="Extract web content",
    tools=[ScrapeWebsiteTool()]
)
```

### File Tools

```python
from crewai_tools import (
    FileReadTool,           # Read any file
    FileWriterTool,         # Write files
    DirectoryReadTool,      # List directory contents
    DirectorySearchTool,    # Search in directory
)

# Read files
file_reader = FileReadTool(file_path="./data")  # Limit to directory

# Write files
file_writer = FileWriterTool()

agent = Agent(
    role="File Manager",
    tools=[FileReadTool(), FileWriterTool()]
)
```

### Document Tools

```python
from crewai_tools import (
    PDFSearchTool,          # Search PDF content
    DOCXSearchTool,         # Search Word docs
    TXTSearchTool,          # Search text files
    CSVSearchTool,          # Search CSV files
    JSONSearchTool,         # Search JSON files
    XMLSearchTool,          # Search XML files
    MDXSearchTool,          # Search MDX files
)

# PDF search (uses embeddings)
pdf_tool = PDFSearchTool(pdf="./documents/report.pdf")

# CSV search
csv_tool = CSVSearchTool(csv="./data/sales.csv")

agent = Agent(
    role="Document Analyst",
    tools=[PDFSearchTool(), CSVSearchTool()]
)
```

### Database Tools

```python
from crewai_tools import (
    MySQLSearchTool,              # MySQL queries
    PostgreSQLTool,               # PostgreSQL
    MongoDBVectorSearchTool,      # MongoDB vector search
    QdrantVectorSearchTool,       # Qdrant vector DB
    WeaviateVectorSearchTool,     # Weaviate
)

# MySQL
mysql_tool = MySQLSearchTool(
    host="localhost",
    port=3306,
    database="mydb",
    user="user",
    password="pass"
)

# Qdrant
qdrant_tool = QdrantVectorSearchTool(
    url="http://localhost:6333",
    collection_name="my_collection"
)
```

### AI Service Tools

```python
from crewai_tools import (
    DallETool,              # DALL-E image generation
    VisionTool,             # Image analysis
    OCRTool,                # Text extraction from images
)

# DALL-E (requires OPENAI_API_KEY)
dalle = DallETool()

# Vision (GPT-4V)
vision = VisionTool()

agent = Agent(
    role="Visual Designer",
    tools=[DallETool(), VisionTool()]
)
```

### Code Tools

```python
from crewai_tools import (
    CodeDocsSearchTool,     # Search code documentation
    GithubSearchTool,       # Search GitHub repos
    CodeInterpreterTool,    # Execute Python code
)

# Code docs search
code_docs = CodeDocsSearchTool(docs_url="https://docs.python.org")

# GitHub search (requires GITHUB_TOKEN)
github = GithubSearchTool(
    repo="owner/repo",
    content_types=["code", "issue"]
)

# Code interpreter (sandboxed)
interpreter = CodeInterpreterTool()
```

### Cloud Platform Tools

```python
from crewai_tools import (
    BedrockInvokeAgentTool,     # AWS Bedrock
    DatabricksQueryTool,        # Databricks
    S3ReaderTool,               # AWS S3
    SnowflakeTool,              # Snowflake
)

# AWS Bedrock
bedrock = BedrockInvokeAgentTool(
    agent_id="your-agent-id",
    agent_alias_id="alias-id"
)

# Databricks
databricks = DatabricksQueryTool(
    host="your-workspace.databricks.com",
    token="your-token"
)
```

### Integration Tools

```python
from crewai_tools import (
    MCPServerAdapter,       # MCP protocol
    ComposioTool,           # Composio integrations
    ZapierActionTool,       # Zapier automations
)

# MCP Server
mcp = MCPServerAdapter(
    server_url="http://localhost:8080",
    tool_names=["tool1", "tool2"]
)

# Composio (requires COMPOSIO_API_KEY)
composio = ComposioTool()
```

## Custom Tools

### Basic Custom Tool

```python
from crewai.tools import BaseTool
from pydantic import Field

class WeatherTool(BaseTool):
    name: str = "Weather Lookup"
    description: str = "Get current weather for a city. Input: city name"

    def _run(self, city: str) -> str:
        # Your implementation
        return f"Weather in {city}: 72°F, sunny"

# Use custom tool
agent = Agent(
    role="Weather Reporter",
    tools=[WeatherTool()]
)
```

### Tool with Parameters

```python
from crewai.tools import BaseTool
from pydantic import Field
from typing import Optional

class APITool(BaseTool):
    name: str = "API Client"
    description: str = "Make API requests"

    # Tool configuration
    api_key: str = Field(default="")
    base_url: str = Field(default="https://api.example.com")

    def _run(self, endpoint: str, method: str = "GET") -> str:
        import requests

        url = f"{self.base_url}/{endpoint}"
        headers = {"Authorization": f"Bearer {self.api_key}"}

        response = requests.request(method, url, headers=headers)
        return response.json()

# Configure tool
api_tool = APITool(api_key="your-key", base_url="https://api.example.com")
```

### Tool with Validation

```python
from crewai.tools import BaseTool
from pydantic import Field, field_validator

class CalculatorTool(BaseTool):
    name: str = "Calculator"
    description: str = "Perform math calculations. Input: expression (e.g., '2 + 2')"

    allowed_operators: list = Field(default=["+", "-", "*", "/", "**"])

    @field_validator("allowed_operators")
    def validate_operators(cls, v):
        valid = ["+", "-", "*", "/", "**", "%", "//"]
        for op in v:
            if op not in valid:
                raise ValueError(f"Invalid operator: {op}")
        return v

    def _run(self, expression: str) -> str:
        try:
            # Simple eval with safety checks
            for char in expression:
                if char.isalpha():
                    return "Error: Letters not allowed"
            result = eval(expression)
            return f"Result: {result}"
        except Exception as e:
            return f"Error: {str(e)}"
```

### Async Tool

```python
from crewai.tools import BaseTool
import aiohttp

class AsyncAPITool(BaseTool):
    name: str = "Async API"
    description: str = "Make async API requests"

    async def _arun(self, url: str) -> str:
        async with aiohttp.ClientSession() as session:
            async with session.get(url) as response:
                return await response.text()

    def _run(self, url: str) -> str:
        import asyncio
        return asyncio.run(self._arun(url))
```

## Tool Configuration

### Caching

```python
from crewai_tools import SerperDevTool

# Enable caching (default)
search = SerperDevTool(cache=True)

# Disable for real-time data
search = SerperDevTool(cache=False)
```

### Error Handling

```python
class RobustTool(BaseTool):
    name: str = "Robust Tool"
    description: str = "A tool with error handling"

    max_retries: int = 3

    def _run(self, query: str) -> str:
        for attempt in range(self.max_retries):
            try:
                return self._execute(query)
            except Exception as e:
                if attempt == self.max_retries - 1:
                    return f"Failed after {self.max_retries} attempts: {str(e)}"
                continue
```

### Tool Limits per Agent

```python
# Recommended: 3-5 tools per agent
researcher = Agent(
    role="Researcher",
    goal="Find information",
    tools=[
        SerperDevTool(),        # Search
        ScrapeWebsiteTool(),    # Scrape
        PDFSearchTool(),        # PDF search
    ],
    max_iter=15                 # Limit iterations
)
```

## MCP (Model Context Protocol)

### Using MCP Servers

```python
from crewai_tools import MCPServerAdapter

# Connect to MCP server
mcp_adapter = MCPServerAdapter(
    server_url="http://localhost:8080",
    tool_names=["search", "calculate", "translate"]
)

# Get tools from MCP
mcp_tools = mcp_adapter.get_tools()

agent = Agent(
    role="MCP User",
    tools=mcp_tools
)
```

### MCP Tool Discovery

```python
# List available tools
tools = mcp_adapter.list_tools()
for tool in tools:
    print(f"{tool.name}: {tool.description}")

# Get specific tools
selected_tools = mcp_adapter.get_tools(tool_names=["search", "translate"])
```

## Tool Best Practices

1. **Single responsibility** - Each tool should do one thing well
2. **Clear descriptions** - Agents use descriptions to choose tools
3. **Input validation** - Validate inputs before processing
4. **Error messages** - Return helpful error messages
5. **Limit per agent** - 3-5 tools max for focused agents
6. **Cache when appropriate** - Enable caching for expensive operations
7. **Timeout handling** - Add timeouts for external API calls
8. **Test thoroughly** - Unit test tools independently

## Tool Categories Reference

| Category | Tools | Use Case |
|----------|-------|----------|
| **Search** | Serper, Tavily, Brave, EXA | Web search, information retrieval |
| **Scraping** | ScrapeWebsite, Firecrawl, Selenium | Extract web content |
| **Files** | FileRead, FileWrite, DirectoryRead | Local file operations |
| **Documents** | PDF, DOCX, CSV, JSON, XML | Document parsing |
| **Databases** | MySQL, PostgreSQL, MongoDB, Qdrant | Data storage queries |
| **AI Services** | DALL-E, Vision, OCR | AI-powered tools |
| **Code** | CodeDocs, GitHub, CodeInterpreter | Development tools |
| **Cloud** | Bedrock, Databricks, S3, Snowflake | Cloud platform integration |
| **Integration** | MCP, Composio, Zapier | Third-party integrations |




### Troubleshooting

# CrewAI Troubleshooting Guide

## Installation Issues

### Missing Dependencies

**Error**: `ModuleNotFoundError: No module named 'crewai_tools'`

**Fix**:
```bash
pip install 'crewai[tools]'
```

### Python Version

**Error**: `Python version not supported`

**Fix**: CrewAI requires Python 3.10-3.13:
```bash
python --version  # Check current version

# Use pyenv to switch
pyenv install 3.11
pyenv local 3.11
```

### UV Package Manager

**Error**: Poetry-related errors

**Fix**: CrewAI migrated from Poetry to UV:
```bash
crewai update

# Or manually install UV
pip install uv
```

## Agent Issues

### Agent Stuck in Loop

**Problem**: Agent keeps iterating without completing.

**Solutions**:

1. **Set max iterations**:
```python
agent = Agent(
    role="...",
    max_iter=10,  # Limit iterations
    max_rpm=5     # Rate limit
)
```

2. **Clearer task description**:
```python
task = Task(
    description="Research AI trends. Return EXACTLY 5 bullet points.",
    expected_output="A list of 5 bullet points, nothing more."
)
```

3. **Enable verbose to debug**:
```python
agent = Agent(role="...", verbose=True)
```

### Agent Not Using Tools

**Problem**: Agent ignores available tools.

**Solutions**:

1. **Better tool descriptions**:
```python
class MyTool(BaseTool):
    name: str = "Calculator"
    description: str = "Use this to perform mathematical calculations. Input: math expression like '2+2'"
```

2. **Include tool in goal/backstory**:
```python
agent = Agent(
    role="Data Analyst",
    goal="Calculate metrics using the Calculator tool",
    backstory="You are skilled at using calculation tools."
)
```

3. **Limit tools** (3-5 max):
```python
agent = Agent(
    role="...",
    tools=[tool1, tool2, tool3]  # Don't overload with tools
)
```

### Agent Using Wrong Tool

**Problem**: Agent picks incorrect tool for task.

**Fix**: Make descriptions distinct:
```python
search_tool = SerperDevTool()
search_tool.description = "Search the web for current news and information. Use for recent events."

pdf_tool = PDFSearchTool()
pdf_tool.description = "Search within PDF documents. Use for document-specific queries."
```

## Task Issues

### Task Not Receiving Context

**Problem**: Task doesn't use output from previous task.

**Fix**: Explicitly pass context:
```python
task1 = Task(
    description="Research AI trends",
    expected_output="List of trends",
    agent=researcher
)

task2 = Task(
    description="Write about the research findings",
    expected_output="Blog post",
    agent=writer,
    context=[task1]  # Must explicitly reference
)
```

### Output Not Matching Expected

**Problem**: Task output doesn't match expected_output format.

**Solutions**:

1. **Be specific in expected_output**:
```python
task = Task(
    description="...",
    expected_output="""
    A JSON object with:
    - 'title': string
    - 'points': array of 5 strings
    - 'summary': string under 100 words
    """
)
```

2. **Use output_pydantic for structure**:
```python
from pydantic import BaseModel

class Report(BaseModel):
    title: str
    points: list[str]
    summary: str

task = Task(
    description="...",
    expected_output="Structured report",
    output_pydantic=Report
)
```

### Task Timeout

**Problem**: Task takes too long.

**Fix**: Set timeouts and limits:
```python
agent = Agent(
    role="...",
    max_iter=15,
    max_rpm=10
)

crew = Crew(
    agents=[agent],
    tasks=[task],
    max_rpm=20  # Crew-level limit
)
```

## Crew Issues

### CUDA/Memory Errors

**Problem**: Out of memory with local models.

**Fix**: Use cloud LLM or smaller model:
```python
from crewai import LLM

# Use cloud API instead of local
llm = LLM(model="gpt-4o")

# Or smaller local model
llm = LLM(model="ollama/llama3.1:7b")

agent = Agent(role="...", llm=llm)
```

### Rate Limiting

**Problem**: API rate limit errors.

**Fix**: Configure rate limits:
```python
agent = Agent(
    role="...",
    max_rpm=5  # 5 requests per minute
)

crew = Crew(
    agents=[agent1, agent2],
    max_rpm=10  # Total crew limit
)
```

### Memory Errors

**Problem**: Memory storage issues.

**Fix**: Set storage directory:
```python
import os
os.environ["CREWAI_STORAGE_DIR"] = "./my_storage"

# Or disable memory
crew = Crew(
    agents=[...],
    tasks=[...],
    memory=False
)
```

## Flow Issues

### State Not Persisting

**Problem**: Flow state resets between methods.

**Fix**: Use self.state correctly:
```python
class MyFlow(Flow[MyState]):
    @start()
    def init(self):
        self.state.data = "initialized"  # Correct
        return {}

    @listen(init)
    def process(self):
        print(self.state.data)  # "initialized"
```

### Router Not Triggering Listener

**Problem**: Router returns string but listener not triggered.

**Fix**: Match names exactly:
```python
@router(analyze)
def decide(self):
    return "high_confidence"  # Must match exactly

@listen("high_confidence")  # Match the router return value
def handle_high(self):
    pass
```

### Multiple Start Methods

**Problem**: Confusion with multiple @start methods.

**Note**: Multiple starts run in parallel:
```python
@start()
def start_a(self):
    return "A"

@start()
def start_b(self):  # Runs parallel with start_a
    return "B"

@listen(and_(start_a, start_b))
def after_both(self):  # Waits for both
    pass
```

## Tool Issues

### Tool Not Found

**Error**: `Tool 'X' not found`

**Fix**: Verify tool installation:
```python
# Check available tools
from crewai_tools import *

# Install specific tool
pip install 'crewai[tools]'

# Some tools need extra deps
pip install 'crewai-tools[selenium]'
pip install 'crewai-tools[firecrawl]'
```

### API Key Missing

**Error**: `API key not found`

**Fix**: Set environment variables:
```bash
# .env file
OPENAI_API_KEY=sk-...
SERPER_API_KEY=...
TAVILY_API_KEY=...
```

```python
# Or in code
import os
os.environ["SERPER_API_KEY"] = "your-key"

from crewai_tools import SerperDevTool
search = SerperDevTool()
```

### Tool Returns Error

**Problem**: Tool consistently fails.

**Fix**: Test tool independently:
```python
from crewai_tools import SerperDevTool

# Test tool directly
tool = SerperDevTool()
result = tool._run("test query")
print(result)  # Check output

# Add error handling
class SafeTool(BaseTool):
    def _run(self, query: str) -> str:
        try:
            return actual_operation(query)
        except Exception as e:
            return f"Error: {str(e)}"
```

## Performance Issues

### Slow Execution

**Problem**: Crew takes too long.

**Solutions**:

1. **Use faster model**:
```python
llm = LLM(model="gpt-4o-mini")  # Faster than gpt-4o
```

2. **Reduce iterations**:
```python
agent = Agent(role="...", max_iter=10)
```

3. **Enable caching**:
```python
crew = Crew(
    agents=[...],
    cache=True  # Cache tool results
)
```

4. **Parallel tasks** (where possible):
```python
task1 = Task(..., async_execution=True)
task2 = Task(..., async_execution=True)
```

### High Token Usage

**Problem**: Excessive API costs.

**Solutions**:

1. **Use smaller context**:
```python
task = Task(
    description="Brief research on X",  # Keep descriptions short
    expected_output="3 bullet points"    # Limit output
)
```

2. **Disable verbose in production**:
```python
agent = Agent(role="...", verbose=False)
crew = Crew(agents=[...], verbose=False)
```

3. **Use cheaper models**:
```python
llm = LLM(model="gpt-4o-mini")  # Cheaper than gpt-4o
```

## Debugging Tips

### Enable Verbose Output

```python
agent = Agent(role="...", verbose=True)
crew = Crew(agents=[...], verbose=True)
```

### Check Crew Output

```python
result = crew.kickoff(inputs={"topic": "AI"})

# Check all outputs
print(result.raw)            # Final output
print(result.tasks_output)   # All task outputs
print(result.token_usage)    # Token consumption

# Check individual tasks
for task_output in result.tasks_output:
    print(f"Task: {task_output.description}")
    print(f"Output: {task_output.raw}")
    print(f"Agent: {task_output.agent}")
```

### Test Agents Individually

```python
# Test single agent
agent = Agent(role="Researcher", goal="...", verbose=True)

task = Task(
    description="Simple test task",
    expected_output="Test output",
    agent=agent
)

crew = Crew(agents=[agent], tasks=[task], verbose=True)
result = crew.kickoff()
```

### Logging

```python
import logging

# Enable CrewAI logging
logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger("crewai")
logger.setLevel(logging.DEBUG)
```

## Getting Help

1. **Documentation**: https://docs.crewai.com
2. **GitHub Issues**: https://github.com/crewAIInc/crewAI/issues
3. **Discord**: https://discord.gg/crewai
4. **Examples**: https://github.com/crewAIInc/crewAI-examples

### Reporting Issues

Include:
- CrewAI version: `pip show crewai`
- Python version: `python --version`
- Full error traceback
- Minimal reproducible code
- Expected vs actual behavior




---

## 🚀 Usage

**Reference this template:** `@skill-crewai-multi-agent.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
