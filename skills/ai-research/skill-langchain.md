---
id: skill-langchain
type: skill
name: langchain
description: Framework for building LLM-powered applications with agents, chains,
  and RAG. Supports multiple providers (OpenAI, Anthropic, Google), 500+ integrations,
  ReAct agents, tool calling, memory management, and vector store retrieval. Use for
  building chatbots, question-answering systems, autonomous agents, or RAG applications.
  Best for rapid prototyping and production deployments.
category: ai-research
complexity: medium
keywords:
- api
- deployment
- github
- performance
- python
- qa
- react
capabilities: []
token_estimate: 1732
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,732 -->
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




# langchain

> Framework for building LLM-powered applications with agents, chains, and RAG. Supports multiple providers (OpenAI, Anthropic, Google), 500+ integrations, ReAct agents, tool calling, memory management, and vector store retrieval. Use for building chatbots, question-answering systems, autonomous agents, or RAG applications. Best for rapid prototyping and production deployments.

# LangChain - Build LLM Applications with Agents & RAG

The most popular framework for building LLM-powered applications.

## When to use LangChain

**Use LangChain when:**
- Building agents with tool calling and reasoning (ReAct pattern)
- Implementing RAG (retrieval-augmented generation) pipelines
- Need to swap LLM providers easily (OpenAI, Anthropic, Google)
- Creating chatbots with conversation memory
- Rapid prototyping of LLM applications
- Production deployments with LangSmith observability

**Metrics**:
- **119,000+ GitHub stars**
- **272,000+ repositories** use LangChain
- **500+ integrations** (models, vector stores, tools)
- **3,800+ contributors**

**Use alternatives instead**:
- **LlamaIndex**: RAG-focused, better for document Q&A
- **LangGraph**: Complex stateful workflows, more control
- **Haystack**: Production search pipelines
- **Semantic Kernel**: Microsoft ecosystem

## Quick start

### Installation

```bash
# Core library (Python 3.10+)
pip install -U langchain

# With OpenAI
pip install langchain-openai

# With Anthropic
pip install langchain-anthropic

# Common extras
pip install langchain-community  # 500+ integrations
pip install langchain-chroma     # Vector store
```

### Basic LLM usage

```python
from langchain_anthropic import ChatAnthropic

# Initialize model
llm = ChatAnthropic(model="claude-sonnet-4-5-20250929")

# Simple completion
response = llm.invoke("Explain quantum computing in 2 sentences")
print(response.content)
```

### Create an agent (ReAct pattern)

```python
from langchain.agents import create_agent
from langchain_anthropic import ChatAnthropic

# Define tools
def get_weather(city: str) -> str:
    """Get current weather for a city."""
    return f"It's sunny in {city}, 72°F"

def search_web(query: str) -> str:
    """Search the web for information."""
    return f"Search results for: {query}"

# Create agent (<10 lines!)
agent = create_agent(
    model=ChatAnthropic(model="claude-sonnet-4-5-20250929"),
    tools=[get_weather, search_web],
    system_prompt="You are a helpful assistant. Use tools when needed."
)

# Run agent
result = agent.invoke({"messages": [{"role": "user", "content": "What's the weather in Paris?"}]})
print(result["messages"][-1].content)
```

## Core concepts

### 1. Models - LLM abstraction

```python
from langchain_openai import ChatOpenAI
from langchain_anthropic import ChatAnthropic
from langchain_google_genai import ChatGoogleGenerativeAI

# Swap providers easily
llm = ChatOpenAI(model="gpt-4o")
llm = ChatAnthropic(model="claude-sonnet-4-5-20250929")
llm = ChatGoogleGenerativeAI(model="gemini-2.0-flash-exp")

# Streaming
for chunk in llm.stream("Write a poem"):
    print(chunk.content, end="", flush=True)
```

### 2. Chains - Sequential operations

```python
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate

# Define prompt template
prompt = PromptTemplate(
    input_variables=["topic"],
    template="Write a 3-sentence summary about {topic}"
)

# Create chain
chain = LLMChain(llm=llm, prompt=prompt)

# Run chain
result = chain.run(topic="machine learning")
```

### 3. Agents - Tool-using reasoning

**ReAct (Reasoning + Acting) pattern:**

```python
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain.tools import Tool

# Define custom tool
calculator = Tool(
    name="Calculator",
    func=lambda x: eval(x),
    description="Useful for math calculations. Input: valid Python expression."
)

# Create agent with tools
agent = create_tool_calling_agent(
    llm=llm,
    tools=[calculator, search_web],
    prompt="Answer questions using available tools"
)

# Create executor
agent_executor = AgentExecutor(agent=agent, tools=[calculator], verbose=True)

# Run with reasoning
result = agent_executor.invoke({"input": "What is 25 * 17 + 142?"})
```

### 4. Memory - Conversation history

```python
from langchain.memory import ConversationBufferMemory
from langchain.chains import ConversationChain

# Add memory to track conversation
memory = ConversationBufferMemory()

conversation = ConversationChain(
    llm=llm,
    memory=memory,
    verbose=True
)

# Multi-turn conversation
conversation.predict(input="Hi, I'm Alice")
conversation.predict(input="What's my name?")  # Remembers "Alice"
```

## RAG (Retrieval-Augmented Generation)

### Basic RAG pipeline

```python
from langchain_community.document_loaders import WebBaseLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_chroma import Chroma
from langchain.chains import RetrievalQA

# 1. Load documents
loader = WebBaseLoader("https://docs.python.org/3/tutorial/")
docs = loader.load()

# 2. Split into chunks
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200
)
splits = text_splitter.split_documents(docs)

# 3. Create embeddings and vector store
vectorstore = Chroma.from_documents(
    documents=splits,
    embedding=OpenAIEmbeddings()
)

# 4. Create retriever
retriever = vectorstore.as_retriever(search_kwargs={"k": 4})

# 5. Create QA chain
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    return_source_documents=True
)

# 6. Query
result = qa_chain({"query": "What are Python decorators?"})
print(result["result"])
print(f"Sources: {result['source_documents']}")
```

### Conversational RAG with memory

```python
from langchain.chains import ConversationalRetrievalChain

# RAG with conversation memory
qa = ConversationalRetrievalChain.from_llm(
    llm=llm,
    retriever=retriever,
    memory=ConversationBufferMemory(
        memory_key="chat_history",
        return_messages=True
    )
)

# Multi-turn RAG
qa({"question": "What is Python used for?"})
qa({"question": "Can you elaborate on web development?"})  # Remembers context
```

## Advanced agent patterns

### Structured output

```python
from langchain_core.pydantic_v1 import BaseModel, Field

# Define schema
class WeatherReport(BaseModel):
    city: str = Field(description="City name")
    temperature: float = Field(description="Temperature in Fahrenheit")
    condition: str = Field(description="Weather condition")

# Get structured response
structured_llm = llm.with_structured_output(WeatherReport)
result = structured_llm.invoke("What's the weather in SF? It's 65F and sunny")
print(result.city, result.temperature, result.condition)
```

### Parallel tool execution

```python
from langchain.agents import create_tool_calling_agent

# Agent automatically parallelizes independent tool calls
agent = create_tool_calling_agent(
    llm=llm,
    tools=[get_weather, search_web, calculator]
)

# This will call get_weather("Paris") and get_weather("London") in parallel
result = agent.invoke({
    "messages": [{"role": "user", "content": "Compare weather in Paris and London"}]
})
```

### Streaming agent execution

```python
# Stream agent steps
for step in agent_executor.stream({"input": "Research AI trends"}):
    if "actions" in step:
        print(f"capabilities: File operations, code editing, terminal access, search{step['actions'][0].tool}")
    if "output" in step:
        print(f"Output: {step['output']}")
```

## Common patterns

### Multi-document QA

```python
from langchain.chains.qa_with_sources import load_qa_with_sources_chain

# Load multiple documents
docs = [
    loader.load("https://docs.python.org"),
    loader.load("https://docs.numpy.org")
]

# QA with source citations
chain = load_qa_with_sources_chain(llm, chain_type="stuff")
result = chain({"input_documents": docs, "question": "How to use numpy arrays?"})
print(result["output_text"])  # Includes source citations
```

### Custom tools with error handling

```python
from langchain.tools import tool

@tool
def risky_operation(query: str) -> str:
    """Perform a risky operation that might fail."""
    try:
        # Your operation here
        result = perform_operation(query)
        return f"Success: {result}"
    except Exception as e:
        return f"Error: {str(e)}"

# Agent handles errors gracefully
agent = create_agent(model=llm, tools=[risky_operation])
```

### LangSmith observability

```python
import os

# Enable tracing
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["LANGCHAIN_API_KEY"] = "your-api-key"
os.environ["LANGCHAIN_PROJECT"] = "my-project"

# All chains/agents automatically traced
agent = create_agent(model=llm, tools=[calculator])
result = agent.invoke({"input": "Calculate 123 * 456"})

# View traces at smith.langchain.com
```

## Vector stores

### Chroma (local)

```python
from langchain_chroma import Chroma

vectorstore = Chroma.from_documents(
    documents=docs,
    embedding=OpenAIEmbeddings(),
    persist_directory="./chroma_db"
)
```

### Pinecone (cloud)

```python
from langchain_pinecone import PineconeVectorStore

vectorstore = PineconeVectorStore.from_documents(
    documents=docs,
    embedding=OpenAIEmbeddings(),
    index_name="my-index"
)
```

### FAISS (similarity search)

```python
from langchain_community.vectorstores import FAISS

vectorstore = FAISS.from_documents(docs, OpenAIEmbeddings())
vectorstore.save_local("faiss_index")

# Load later
vectorstore = FAISS.load_local("faiss_index", OpenAIEmbeddings())
```

## Document loaders

```python
# Web pages
from langchain_community.document_loaders import WebBaseLoader
loader = WebBaseLoader("https://example.com")

# PDFs
from langchain_community.document_loaders import PyPDFLoader
loader = PyPDFLoader("paper.pdf")

# GitHub
from langchain_community.document_loaders import GithubFileLoader
loader = GithubFileLoader(repo="user/repo", file_filter=lambda x: x.endswith(".py"))

# CSV
from langchain_community.document_loaders import CSVLoader
loader = CSVLoader("data.csv")
```

## Text splitters

```python
# Recursive (recommended for general text)
from langchain.text_splitter import RecursiveCharacterTextSplitter
splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    separators=["\n\n", "\n", " ", ""]
)

# Code-aware
from langchain.text_splitter import PythonCodeTextSplitter
splitter = PythonCodeTextSplitter(chunk_size=500)

# Semantic (by meaning)
from langchain_experimental.text_splitter import SemanticChunker
splitter = SemanticChunker(OpenAIEmbeddings())
```

## Best practices

1. **Start simple** - Use `create_agent()` for most cases
2. **Enable streaming** - Better UX for long responses
3. **Add error handling** - Tools can fail, handle gracefully
4. **Use LangSmith** - Essential for debugging agents
5. **Optimize chunk size** - 500-1000 chars for RAG
6. **Version prompts** - Track changes in production
7. **Cache embeddings** - Expensive, cache when possible
8. **Monitor costs** - Track token usage with LangSmith

## Performance benchmarks

| Operation | Latency | Notes |
|-----------|---------|-------|
| Simple LLM call | ~1-2s | Depends on provider |
| Agent with 1 tool | ~3-5s | ReAct reasoning overhead |
| RAG retrieval | ~0.5-1s | Vector search + LLM |
| Embedding 1000 docs | ~10-30s | Depends on model |

## LangChain vs LangGraph

| Feature | LangChain | LangGraph |
|---------|-----------|-----------|
| **Best for** | Quick agents, RAG | Complex workflows |
| **Abstraction level** | High | Low |
| **Code to start** | <10 lines | ~30 lines |
| **Control** | Simple | Full control |
| **Stateful workflows** | Limited | Native |
| **Cyclic graphs** | No | Yes |
| **Human-in-loop** | Basic | Advanced |

**Use LangGraph when:**
- Need stateful workflows with cycles
- Require fine-grained control
- Building multi-agent systems
- Production apps with complex logic

## References

- **[Agents Guide](references/agents.md)** - ReAct, tool calling, streaming
- **[RAG Guide](references/rag.md)** - Document loaders, retrievers, QA chains
- **[Integration Guide](references/integration.md)** - Vector stores, LangSmith, deployment

## Resources

- **GitHub**: https://github.com/langchain-ai/langchain ⭐ 119,000+
- **Docs**: https://docs.langchain.com
- **API Reference**: https://reference.langchain.com/python
- **LangSmith**: https://smith.langchain.com (observability)
- **Version**: 0.3+ (stable)
- **License**: MIT




---


## 📚 Reference Materials


### Agents

# LangChain Agents Guide

Complete guide to building agents with ReAct, tool calling, and streaming.

## What are agents?

Agents combine language models with tools to solve complex tasks through reasoning and action:

1. **Reasoning**: LLM decides what to do
2. **Acting**: Execute tools based on reasoning
3. **Observation**: Receive tool results
4. **Loop**: Repeat until task complete

This is the **ReAct pattern** (Reasoning + Acting).

## Basic agent creation

```python
from langchain.agents import create_agent
from langchain_anthropic import ChatAnthropic

# Define tools
def calculator(expression: str) -> str:
    """Evaluate a math expression."""
    return str(eval(expression))

def search(query: str) -> str:
    """Search for information."""
    return f"Results for: {query}"

# Create agent
agent = create_agent(
    model=ChatAnthropic(model="claude-sonnet-4-5-20250929"),
    tools=[calculator, search],
    system_prompt="You are a helpful assistant. Use tools when needed."
)

# Run agent
result = agent.invoke({
    "messages": [{"role": "user", "content": "What is 25 * 17?"}]
})
print(result["messages"][-1].content)
```

## Agent components

### 1. Model - The reasoning engine

```python
from langchain_openai import ChatOpenAI
from langchain_anthropic import ChatAnthropic

# OpenAI
model = ChatOpenAI(model="gpt-4o", temperature=0)

# Anthropic (better for complex reasoning)
model = ChatAnthropic(model="claude-sonnet-4-5-20250929", temperature=0)

# Dynamic model selection
def select_model(task_complexity: str):
    if task_complexity == "high":
        return ChatAnthropic(model="claude-sonnet-4-5-20250929")
    else:
        return ChatOpenAI(model="gpt-4o-mini")
```

### 2. Tools - Actions the agent can take

```python
from langchain.tools import tool

# Simple function tool
@tool
def get_current_time() -> str:
    """Get the current time."""
    from datetime import datetime
    return datetime.now().strftime("%H:%M:%S")

# Tool with parameters
@tool
def fetch_weather(city: str, units: str = "fahrenheit") -> str:
    """Fetch weather for a city.

    Args:
        city: City name
        units: Temperature units (fahrenheit or celsius)
    """
    # Your weather API call here
    return f"Weather in {city}: 72°{units[0].upper()}"

# Tool with error handling
@tool
def risky_api_call(endpoint: str) -> str:
    """Call an external API that might fail."""
    try:
        response = requests.get(endpoint, timeout=5)
        return response.text
    except Exception as e:
        return f"Error calling API: {str(e)}"
```

### 3. System prompt - Agent behavior

```python
# General assistant
system_prompt = "You are a helpful assistant. Use tools when needed."

# Domain expert
system_prompt = """You are a financial analyst assistant.
- Use the calculator for precise calculations
- Search for recent financial data
- Provide data-driven recommendations
- Always cite your sources"""

# Constrained agent
system_prompt = """You are a customer support agent.
- Only use search_kb tool to find answers
- If answer not found, escalate to human
- Be concise and professional
- Never make up information"""
```

## Agent types

### 1. Tool-calling agent (recommended)

Uses native function calling for best performance:

```python
from langchain.agents import create_tool_calling_agent, AgentExecutor
from langchain.prompts import ChatPromptTemplate

# Create prompt
prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant"),
    ("human", "{input}"),
    ("placeholder", "{agent_scratchpad}"),
])

# Create agent
agent = create_tool_calling_agent(
    llm=model,
    tools=[calculator, search],
    prompt=prompt
)

# Wrap in executor
agent_executor = AgentExecutor(
    agent=agent,
    tools=[calculator, search],
    verbose=True,
    max_iterations=5,
    handle_parsing_errors=True
)

# Run
result = agent_executor.invoke({"input": "What is the weather in Paris?"})
```

### 2. ReAct agent (reasoning trace)

Shows step-by-step reasoning:

```python
from langchain.agents import create_react_agent

# ReAct prompt shows thought process
react_prompt = """Answer the following questions as best you can. You have access to the following tools:

{tools}

Use the following format:

Question: the input question you must answer
Thought: you should always think about what to do
Action: the action to take, should be one of [{tool_names}]
Action Input: the input to the action
Observation: the result of the action
... (this Thought/Action/Action Input/Observation can repeat N times)
Thought: I now know the final answer
Final Answer: the final answer to the original input question

Begin!

Question: {input}
Thought: {agent_scratchpad}"""

agent = create_react_agent(
    llm=model,
    tools=[calculator, search],
    prompt=ChatPromptTemplate.from_template(react_prompt)
)

# Run with visible reasoning
result = agent_executor.invoke({"input": "What is 25 * 17 + 142?"})
```

### 3. Conversational agent (with memory)

Remembers conversation history:

```python
from langchain.agents import create_conversational_retrieval_agent
from langchain.memory import ConversationBufferMemory

# Add memory
memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)

# Conversational agent
agent_executor = AgentExecutor(
    agent=agent,
    tools=[calculator, search],
    memory=memory,
    verbose=True
)

# Multi-turn conversation
agent_executor.invoke({"input": "My name is Alice"})
agent_executor.invoke({"input": "What's my name?"})  # Remembers "Alice"
agent_executor.invoke({"input": "What is 25 * 17?"})
```

## Tool execution patterns

### Parallel tool execution

```python
# Agent automatically parallelizes independent calls
agent = create_tool_calling_agent(llm=model, tools=[get_weather, search])

# This calls get_weather("Paris") and get_weather("London") in parallel
result = agent_executor.invoke({
    "input": "Compare weather in Paris and London"
})
```

### Sequential tool chaining

```python
# Agent chains tools automatically
@tool
def search_company(name: str) -> str:
    """Search for company information."""
    return f"Company ID: 12345, Industry: Tech"

@tool
def get_stock_price(company_id: str) -> str:
    """Get stock price for a company."""
    return f"${150.00}"

# Agent will: search_company → get_stock_price
result = agent_executor.invoke({
    "input": "What is Apple's current stock price?"
})
```

### Conditional tool usage

```python
# Agent decides when to use tools
@tool
def expensive_tool(query: str) -> str:
    """Use only when necessary - costs $0.10 per call."""
    return perform_expensive_operation(query)

# Agent uses tool only if needed
result = agent_executor.invoke({
    "input": "What is 2+2?"  # Won't use expensive_tool
})
```

## Streaming

### Stream agent steps

```python
# Stream intermediate steps
for step in agent_executor.stream({"input": "Research quantum computing"}):
    if "actions" in step:
        action = step["actions"][0]
        print(f"capabilities: File operations, code editing, terminal access, search{action.tool}, Input: {action.tool_input}")
    if "steps" in step:
        print(f"Observation: {step['steps'][0].observation}")
    if "output" in step:
        print(f"Final: {step['output']}")
```

### Stream LLM tokens

```python
from langchain.callbacks import StreamingStdOutCallbackHandler

# Stream model responses
agent_executor = AgentExecutor(
    agent=agent,
    tools=[calculator],
    callbacks=[StreamingStdOutCallbackHandler()],
    verbose=True
)

result = agent_executor.invoke({"input": "Explain quantum computing"})
```

## Error handling

### Tool error handling

```python
@tool
def fallible_tool(query: str) -> str:
    """A tool that might fail."""
    try:
        result = risky_operation(query)
        return f"Success: {result}"
    except Exception as e:
        return f"Error: {str(e)}. Please try a different approach."

# Agent adapts to errors
agent_executor = AgentExecutor(
    agent=agent,
    tools=[fallible_tool],
    handle_parsing_errors=True,  # Handle malformed tool calls
    max_iterations=5
)
```

### Timeout handling

```python
from langchain.callbacks import TimeoutCallback

# Set timeout
agent_executor = AgentExecutor(
    agent=agent,
    tools=[slow_tool],
    callbacks=[TimeoutCallback(timeout=30)],  # 30 second timeout
    max_iterations=10
)
```

### Retry logic

```python
from langchain.callbacks import RetryCallback

# Retry on failure
agent_executor = AgentExecutor(
    agent=agent,
    tools=[unreliable_tool],
    callbacks=[RetryCallback(max_retries=3)],
    max_execution_time=60
)
```

## Advanced patterns

### Dynamic tool selection

```python
# Select tools based on context
def get_tools_for_user(user_role: str):
    if user_role == "admin":
        return [search, calculator, database_query, delete_data]
    elif user_role == "analyst":
        return [search, calculator, database_query]
    else:
        return [search, calculator]

# Create agent with role-based tools
tools = get_tools_for_user(current_user.role)
agent = create_agent(model=model, tools=tools)
```

### Multi-step reasoning

```python
# Agent plans multiple steps
system_prompt = """Break down complex tasks into steps:
1. Analyze the question
2. Determine required information
3. Use tools to gather data
4. Synthesize findings
5. Provide final answer"""

agent = create_agent(
    model=model,
    tools=[search, calculator, database],
    system_prompt=system_prompt
)

result = agent.invoke({
    "input": "Compare revenue growth of top 3 tech companies over 5 years"
})
```

### Structured output from agents

```python
from langchain_core.pydantic_v1 import BaseModel, Field

class ResearchReport(BaseModel):
    summary: str = Field(description="Executive summary")
    findings: list[str] = Field(description="Key findings")
    sources: list[str] = Field(description="Source URLs")

# Agent returns structured output
structured_agent = agent.with_structured_output(ResearchReport)
report = structured_agent.invoke({"input": "Research AI safety"})
print(report.summary, report.findings)
```

## Middleware & customization

### Custom agent middleware

```python
from langchain.agents import AgentExecutor

def logging_middleware(agent_executor):
    """Log all agent actions."""
    original_invoke = agent_executor.invoke

    def wrapped_invoke(*args, **kwargs):
        print(f"Agent invoked with: {args[0]}")
        result = original_invoke(*args, **kwargs)
        print(f"Agent result: {result}")
        return result

    agent_executor.invoke = wrapped_invoke
    return agent_executor

# Apply middleware
agent_executor = logging_middleware(agent_executor)
```

### Custom stopping conditions

```python
from langchain.agents import EarlyStoppingMethod

# Stop early if confident
agent_executor = AgentExecutor(
    agent=agent,
    tools=[search],
    early_stopping_method=EarlyStoppingMethod.GENERATE,  # or FORCE
    max_iterations=10
)
```

## Best practices

1. **Use tool-calling agents** - Fastest and most reliable
2. **Keep tool descriptions clear** - Agent needs to understand when to use each tool
3. **Add error handling** - Tools will fail, handle gracefully
4. **Set max_iterations** - Prevent infinite loops (default: 15)
5. **Enable streaming** - Better UX for long tasks
6. **Use verbose=True during dev** - See agent reasoning
7. **Test tool combinations** - Ensure tools work together
8. **Monitor with LangSmith** - Essential for production
9. **Cache tool results** - Avoid redundant API calls
10. **Version system prompts** - Track changes in behavior

## Common pitfalls

1. **Vague tool descriptions** - Agent won't know when to use tool
2. **Too many tools** - Agent gets confused (limit to 5-10)
3. **Tools without error handling** - One failure crashes agent
4. **Circular tool dependencies** - Agent gets stuck in loops
5. **Missing max_iterations** - Agent runs forever
6. **Poor system prompts** - Agent doesn't follow instructions

## Debugging agents

```python
# Enable verbose logging
agent_executor = AgentExecutor(
    agent=agent,
    tools=[calculator],
    verbose=True,  # See all steps
    return_intermediate_steps=True  # Get full trace
)

result = agent_executor.invoke({"input": "Calculate 25 * 17"})

# Inspect intermediate steps
for step in result["intermediate_steps"]:
    print(f"Action: {step[0].tool}")
    print(f"Input: {step[0].tool_input}")
    print(f"Output: {step[1]}")
```

## Resources

- **ReAct Paper**: https://arxiv.org/abs/2210.03629
- **LangChain Agents Docs**: https://docs.langchain.com/oss/python/langchain/agents
- **LangSmith Debugging**: https://smith.langchain.com




### Integration

# LangChain Integration Guide

Integration with vector stores, LangSmith observability, and deployment.

## Vector store integrations

### Chroma (local, open-source)

```python
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings

# Create vector store
vectorstore = Chroma.from_documents(
    documents=docs,
    embedding=OpenAIEmbeddings(),
    persist_directory="./chroma_db"
)

# Load existing store
vectorstore = Chroma(
    persist_directory="./chroma_db",
    embedding_function=OpenAIEmbeddings()
)

# Add documents incrementally
vectorstore.add_documents([new_doc1, new_doc2])

# Delete documents
vectorstore.delete(ids=["doc1", "doc2"])
```

### Pinecone (cloud, scalable)

```python
from langchain_pinecone import PineconeVectorStore
import pinecone

# Initialize Pinecone
pinecone.init(api_key="your-api-key", environment="us-west1-gcp")

# Create index (one-time)
pinecone.create_index("my-index", dimension=1536, metric="cosine")

# Create vector store
vectorstore = PineconeVectorStore.from_documents(
    documents=docs,
    embedding=OpenAIEmbeddings(),
    index_name="my-index"
)

# Query with metadata filters
results = vectorstore.similarity_search(
    "Python tutorials",
    k=4,
    filter={"category": "beginner"}
)
```

### FAISS (fast similarity search)

```python
from langchain_community.vectorstores import FAISS

# Create FAISS index
vectorstore = FAISS.from_documents(docs, OpenAIEmbeddings())

# Save to disk
vectorstore.save_local("./faiss_index")

# Load from disk
vectorstore = FAISS.load_local(
    "./faiss_index",
    OpenAIEmbeddings(),
    allow_dangerous_deserialization=True
)

# Merge multiple indices
vectorstore1 = FAISS.load_local("./index1", embeddings)
vectorstore2 = FAISS.load_local("./index2", embeddings)
vectorstore1.merge_from(vectorstore2)
```

### Weaviate (production, ML-native)

```python
from langchain_weaviate import WeaviateVectorStore
import weaviate

# Connect to Weaviate
client = weaviate.Client("http://localhost:8080")

# Create vector store
vectorstore = WeaviateVectorStore.from_documents(
    documents=docs,
    embedding=OpenAIEmbeddings(),
    client=client,
    index_name="LangChain"
)

# Hybrid search (vector + keyword)
results = vectorstore.similarity_search(
    "Python async",
    k=4,
    alpha=0.5  # 0=keyword, 1=vector, 0.5=hybrid
)
```

### Qdrant (fast, open-source)

```python
from langchain_qdrant import QdrantVectorStore
from qdrant_client import QdrantClient

# Connect to Qdrant
client = QdrantClient(host="localhost", port=6333)

# Create vector store
vectorstore = QdrantVectorStore.from_documents(
    documents=docs,
    embedding=OpenAIEmbeddings(),
    collection_name="my_documents",
    client=client
)
```

## LangSmith observability

### Enable tracing

```python
import os

# Set environment variables
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["LANGCHAIN_API_KEY"] = "your-langsmith-api-key"
os.environ["LANGCHAIN_PROJECT"] = "my-project"

# All chains/agents automatically traced
from langchain.agents import create_agent
from langchain_anthropic import ChatAnthropic

agent = create_agent(
    model=ChatAnthropic(model="claude-sonnet-4-5-20250929"),
    tools=[calculator, search]
)

# Run - automatically logged to LangSmith
result = agent.invoke({"input": "What is 25 * 17?"})

# View traces at https://smith.langchain.com
```

### Custom metadata

```python
from langchain.callbacks import tracing_v2_enabled

# Add custom metadata to traces
with tracing_v2_enabled(
    project_name="my-project",
    tags=["production", "customer-support"],
    metadata={"user_id": "12345", "session_id": "abc"}
):
    result = agent.invoke({"input": "Help me with Python"})
```

### Evaluate runs

```python
from langsmith import Client

client = Client()

# Create dataset
dataset = client.create_dataset("qa-eval")
client.create_example(
    dataset_id=dataset.id,
    inputs={"question": "What is Python?"},
    outputs={"answer": "Python is a programming language"}
)

# Evaluate
from langchain.evaluation import load_evaluator

evaluator = load_evaluator("qa")
results = client.evaluate(
    lambda x: qa_chain(x),
    data=dataset,
    evaluators=[evaluator]
)
```

## Deployment patterns

### FastAPI server

```python
from fastapi import FastAPI
from pydantic import BaseModel
from langchain.agents import create_agent

app = FastAPI()

# Initialize agent once
agent = create_agent(
    model=llm,
    tools=[search, calculator]
)

class Query(BaseModel):
    input: str

@app.post("/chat")
async def chat(query: Query):
    result = agent.invoke({"input": query.input})
    return {"response": result["output"]}

# Run: uvicorn main:app --reload
```

### Streaming responses

```python
from fastapi.responses import StreamingResponse
from langchain.callbacks import AsyncIteratorCallbackHandler

@app.post("/chat/stream")
async def chat_stream(query: Query):
    callback = AsyncIteratorCallbackHandler()

    async def generate():
        async for token in agent.astream({"input": query.input}):
            if "output" in token:
                yield token["output"]

    return StreamingResponse(generate(), media_type="text/plain")
```

### Docker deployment

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

```bash
# Build and run
docker build -t langchain-app .
docker run -p 8000:8000 \
  -e OPENAI_API_KEY=your-key \
  -e LANGCHAIN_API_KEY=your-key \
  langchain-app
```

### Kubernetes deployment

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: langchain-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: langchain
  template:
    metadata:
      labels:
        app: langchain
    spec:
      containers:
      - name: langchain
        image: your-registry/langchain-app:latest
        ports:
        - containerPort: 8000
        env:
        - name: OPENAI_API_KEY
          valueFrom:
            secretKeyRef:
              name: langchain-secrets
              key: openai-api-key
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "2Gi"
            cpu: "2000m"
```

## Model integrations

### OpenAI

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    model="gpt-4o",
    temperature=0,
    max_tokens=1000,
    timeout=30,
    max_retries=2
)
```

### Anthropic

```python
from langchain_anthropic import ChatAnthropic

llm = ChatAnthropic(
    model="claude-sonnet-4-5-20250929",
    temperature=0,
    max_tokens=4096,
    timeout=60
)
```

### Google

```python
from langchain_google_genai import ChatGoogleGenerativeAI

llm = ChatGoogleGenerativeAI(
    model="gemini-2.0-flash-exp",
    temperature=0
)
```

### Local models (Ollama)

```python
from langchain_community.llms import Ollama

llm = Ollama(
    model="llama3",
    base_url="http://localhost:11434"
)
```

### Azure OpenAI

```python
from langchain_openai import AzureChatOpenAI

llm = AzureChatOpenAI(
    azure_endpoint="https://your-endpoint.openai.azure.com/",
    azure_deployment="gpt-4",
    api_version="2024-02-15-preview"
)
```

## Tool integrations

### Web search

```python
from langchain_community.tools import DuckDuckGoSearchRun, TavilySearchResults

# DuckDuckGo (free)
search = DuckDuckGoSearchRun()

# Tavily (best quality)
search = TavilySearchResults(api_key="your-key")
```

### Wikipedia

```python
from langchain_community.tools import WikipediaQueryRun
from langchain_community.utilities import WikipediaAPIWrapper

wikipedia = WikipediaQueryRun(api_wrapper=WikipediaAPIWrapper())
```

### Python REPL

```python
from langchain_experimental.tools import PythonREPLTool

python_repl = PythonREPLTool()

# Agent can execute Python code
agent = create_agent(model=llm, tools=[python_repl])
result = agent.invoke({"input": "Calculate the 10th Fibonacci number"})
```

### Shell commands

```python
from langchain_community.tools import ShellTool

shell = ShellTool()

# Agent can run shell commands
agent = create_agent(model=llm, tools=[shell])
```

### SQL databases

```python
from langchain_community.utilities import SQLDatabase
from langchain_community.agent_toolkits import create_sql_agent

db = SQLDatabase.from_uri("sqlite:///mydatabase.db")

agent = create_sql_agent(
    llm=llm,
    db=db,
    agent_type="openai-tools",
    verbose=True
)

result = agent.run("How many users are in the database?")
```

## Memory integrations

### Redis

```python
from langchain.memory import RedisChatMessageHistory
from langchain.memory import ConversationBufferMemory

# Redis-backed memory
message_history = RedisChatMessageHistory(
    url="redis://localhost:6379",
    session_id="user-123"
)

memory = ConversationBufferMemory(
    chat_memory=message_history,
    return_messages=True
)
```

### PostgreSQL

```python
from langchain_postgres import PostgresChatMessageHistory

message_history = PostgresChatMessageHistory(
    connection_string="postgresql://user:pass@localhost/db",
    session_id="user-123"
)
```

### MongoDB

```python
from langchain_mongodb import MongoDBChatMessageHistory

message_history = MongoDBChatMessageHistory(
    connection_string="mongodb://localhost:27017/",
    session_id="user-123"
)
```

## Caching

### In-memory cache

```python
from langchain.cache import InMemoryCache
from langchain.globals import set_llm_cache

set_llm_cache(InMemoryCache())

# Same query uses cache
response1 = llm.invoke("What is Python?")  # API call
response2 = llm.invoke("What is Python?")  # Cached
```

### SQLite cache

```python
from langchain.cache import SQLiteCache

set_llm_cache(SQLiteCache(database_path=".langchain.db"))
```

### Redis cache

```python
from langchain.cache import RedisCache
from redis import Redis

set_llm_cache(RedisCache(redis_=Redis(host="localhost", port=6379)))
```

## Monitoring & logging

### Custom callbacks

```python
from langchain.callbacks.base import BaseCallbackHandler

class CustomCallback(BaseCallbackHandler):
    def on_llm_start(self, serialized, prompts, **kwargs):
        print(f"LLM started with prompts: {prompts}")

    def on_llm_end(self, response, **kwargs):
        print(f"LLM finished with: {response}")

    def on_tool_start(self, serialized, input_str, **kwargs):
        print(f"Tool {serialized['name']} started with: {input_str}")

    def on_tool_end(self, output, **kwargs):
        print(f"Tool finished with: {output}")

# Use callback
agent = create_agent(
    model=llm,
    tools=[calculator],
    callbacks=[CustomCallback()]
)
```

### Token counting

```python
from langchain.callbacks import get_openai_callback

with get_openai_callback() as cb:
    result = llm.invoke("Write a long story")
    print(f"Tokens used: {cb.total_tokens}")
    print(f"Cost: ${cb.total_cost:.4f}")
```

## Best practices

1. **Use LangSmith in production** - Essential for debugging
2. **Cache aggressively** - LLM calls are expensive
3. **Set timeouts** - Prevent hanging requests
4. **Add retries** - Handle transient failures
5. **Monitor costs** - Track token usage
6. **Version your prompts** - Track changes
7. **Use async** - Better performance for I/O
8. **Persistent memory** - Don't lose conversation history
9. **Secure API keys** - Use environment variables
10. **Test integrations** - Verify connections before production

## Resources

- **LangSmith**: https://smith.langchain.com
- **Vector Stores**: https://python.langchain.com/docs/integrations/vectorstores
- **Model Providers**: https://python.langchain.com/docs/integrations/llms
- **Tools**: https://python.langchain.com/docs/integrations/tools
- **Deployment Guide**: https://docs.langchain.com/deploy




### Rag

# LangChain RAG Guide

Complete guide to Retrieval-Augmented Generation with LangChain.

## What is RAG?

**RAG (Retrieval-Augmented Generation)** combines:
1. **Retrieval**: Find relevant documents from knowledge base
2. **Generation**: LLM generates answer using retrieved context

**Benefits**:
- Reduce hallucinations
- Up-to-date information
- Domain-specific knowledge
- Source citations

## RAG pipeline components

### 1. Document loading

```python
from langchain_community.document_loaders import (
    WebBaseLoader,
    PyPDFLoader,
    TextLoader,
    DirectoryLoader,
    CSVLoader,
    UnstructuredMarkdownLoader
)

# Web pages
loader = WebBaseLoader("https://docs.python.org/3/tutorial/")
docs = loader.load()

# PDF files
loader = PyPDFLoader("paper.pdf")
docs = loader.load()

# Multiple PDFs
loader = DirectoryLoader("./papers/", glob="**/*.pdf", loader_cls=PyPDFLoader)
docs = loader.load()

# Text files
loader = TextLoader("data.txt")
docs = loader.load()

# CSV
loader = CSVLoader("data.csv")
docs = loader.load()

# Markdown
loader = UnstructuredMarkdownLoader("README.md")
docs = loader.load()
```

### 2. Text splitting

```python
from langchain.text_splitter import (
    RecursiveCharacterTextSplitter,
    CharacterTextSplitter,
    TokenTextSplitter
)

# Recommended: Recursive (tries multiple separators)
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,        # Characters per chunk
    chunk_overlap=200,      # Overlap between chunks
    length_function=len,
    separators=["\n\n", "\n", " ", ""]
)

splits = text_splitter.split_documents(docs)

# Token-based (for precise token limits)
text_splitter = TokenTextSplitter(
    chunk_size=512,         # Tokens per chunk
    chunk_overlap=50
)

# Character-based (simple)
text_splitter = CharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    separator="\n\n"
)
```

**Chunk size recommendations**:
- **Short answers**: 256-512 tokens
- **General Q&A**: 512-1024 tokens (recommended)
- **Long context**: 1024-2048 tokens
- **Overlap**: 10-20% of chunk_size

### 3. Embeddings

```python
from langchain_openai import OpenAIEmbeddings
from langchain_community.embeddings import (
    HuggingFaceEmbeddings,
    CohereEmbeddings
)

# OpenAI (fast, high quality)
embeddings = OpenAIEmbeddings(model="text-embedding-3-small")

# HuggingFace (free, local)
embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-mpnet-base-v2"
)

# Cohere
embeddings = CohereEmbeddings(model="embed-english-v3.0")
```

### 4. Vector stores

```python
from langchain_chroma import Chroma
from langchain_community.vectorstores import FAISS
from langchain_pinecone import PineconeVectorStore

# Chroma (local, persistent)
vectorstore = Chroma.from_documents(
    documents=splits,
    embedding=embeddings,
    persist_directory="./chroma_db"
)

# FAISS (fast similarity search)
vectorstore = FAISS.from_documents(splits, embeddings)
vectorstore.save_local("./faiss_index")

# Pinecone (cloud, scalable)
vectorstore = PineconeVectorStore.from_documents(
    documents=splits,
    embedding=embeddings,
    index_name="my-index"
)
```

### 5. Retrieval

```python
# Basic retriever (top-k similarity)
retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 4}  # Return top 4 documents
)

# MMR (Maximal Marginal Relevance) - diverse results
retriever = vectorstore.as_retriever(
    search_type="mmr",
    search_kwargs={
        "k": 4,
        "fetch_k": 20,      # Fetch 20, return diverse 4
        "lambda_mult": 0.5  # Diversity (0=diverse, 1=similar)
    }
)

# Similarity score threshold
retriever = vectorstore.as_retriever(
    search_type="similarity_score_threshold",
    search_kwargs={
        "score_threshold": 0.5  # Minimum similarity score
    }
)

# Query documents directly
docs = retriever.get_relevant_documents("What is Python?")
```

### 6. QA chain

```python
from langchain.chains import RetrievalQA
from langchain_anthropic import ChatAnthropic

llm = ChatAnthropic(model="claude-sonnet-4-5-20250929")

# Basic QA chain
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    return_source_documents=True
)

# Query
result = qa_chain({"query": "What are Python decorators?"})
print(result["result"])
print(f"Sources: {len(result['source_documents'])}")
```

## Advanced RAG patterns

### Conversational RAG

```python
from langchain.chains import ConversationalRetrievalChain
from langchain.memory import ConversationBufferMemory

# Add memory
memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True,
    output_key="answer"
)

# Conversational RAG chain
qa = ConversationalRetrievalChain.from_llm(
    llm=llm,
    retriever=retriever,
    memory=memory,
    return_source_documents=True
)

# Multi-turn conversation
result1 = qa({"question": "What is Python used for?"})
result2 = qa({"question": "Can you give examples?"})  # Remembers context
result3 = qa({"question": "What about web development?"})
```

### Custom prompt template

```python
from langchain.prompts import PromptTemplate

# Custom QA prompt
template = """Use the following pieces of context to answer the question.
If you don't know the answer, say so - don't make it up.
Always cite your sources using [Source N] notation.

Context: {context}

Question: {question}

Helpful Answer:"""

prompt = PromptTemplate(
    template=template,
    input_variables=["context", "question"]
)

qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    chain_type_kwargs={"prompt": prompt}
)
```

### Chain types

```python
# 1. Stuff (default) - Put all docs in context
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    chain_type="stuff"  # Fast, works if docs fit in context
)

# 2. Map-reduce - Summarize each doc, then combine
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    chain_type="map_reduce"  # For many documents
)

# 3. Refine - Iteratively refine answer
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    chain_type="refine"  # Most thorough, slowest
)

# 4. Map-rerank - Score answers, return best
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    chain_type="map_rerank"  # Good for multiple perspectives
)
```

### Multi-query retrieval

```python
from langchain.retrievers import MultiQueryRetriever

# Generate multiple queries for better recall
retriever = MultiQueryRetriever.from_llm(
    retriever=vectorstore.as_retriever(),
    llm=llm
)

# "What is Python?" becomes:
# - "What is Python programming language?"
# - "Python language definition"
# - "Overview of Python"
docs = retriever.get_relevant_documents("What is Python?")
```

### Contextual compression

```python
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import LLMChainExtractor

# Compress retrieved docs to relevant parts only
compressor = LLMChainExtractor.from_llm(llm)

compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=vectorstore.as_retriever()
)

# Returns only relevant excerpts
compressed_docs = compression_retriever.get_relevant_documents("Python decorators")
```

### Ensemble retrieval (hybrid search)

```python
from langchain.retrievers import EnsembleRetriever
from langchain.retrievers import BM25Retriever

# Vector search (semantic)
vector_retriever = vectorstore.as_retriever(search_kwargs={"k": 5})

# Keyword search (BM25)
keyword_retriever = BM25Retriever.from_documents(splits)
keyword_retriever.k = 5

# Combine both
ensemble_retriever = EnsembleRetriever(
    retrievers=[vector_retriever, keyword_retriever],
    weights=[0.5, 0.5]  # Equal weight
)

docs = ensemble_retriever.get_relevant_documents("Python async")
```

## RAG with agents

### Agent-based RAG

```python
from langchain.agents import create_tool_calling_agent
from langchain.tools.retriever import create_retriever_tool

# Create retriever tool
retriever_tool = create_retriever_tool(
    retriever=retriever,
    name="python_docs",
    description="Searches Python documentation for answers about Python programming"
)

# Create agent with retriever tool
agent = create_tool_calling_agent(
    llm=llm,
    tools=[retriever_tool, calculator, search],
    system_prompt="Use python_docs tool for Python questions"
)

# Agent decides when to retrieve
from langchain.agents import AgentExecutor
agent_executor = AgentExecutor(agent=agent, tools=[retriever_tool])

result = agent_executor.invoke({"input": "What are Python generators?"})
```

### Multi-document agents

```python
# Multiple knowledge bases
python_retriever = create_retriever_tool(
    retriever=python_vectorstore.as_retriever(),
    name="python_docs",
    description="Python programming documentation"
)

numpy_retriever = create_retriever_tool(
    retriever=numpy_vectorstore.as_retriever(),
    name="numpy_docs",
    description="NumPy library documentation"
)

# Agent chooses which knowledge base to query
agent = create_agent(
    model=llm,
    tools=[python_retriever, numpy_retriever, search]
)

result = agent.invoke({"input": "How do I create numpy arrays?"})
```

## Metadata filtering

### Add metadata to documents

```python
from langchain.schema import Document

# Documents with metadata
docs = [
    Document(
        page_content="Python is a programming language",
        metadata={"source": "tutorial.pdf", "page": 1, "category": "intro"}
    ),
    Document(
        page_content="Python decorators modify functions",
        metadata={"source": "advanced.pdf", "page": 42, "category": "advanced"}
    )
]

vectorstore = Chroma.from_documents(docs, embeddings)
```

### Filter by metadata

```python
# Retrieve only from specific source
retriever = vectorstore.as_retriever(
    search_kwargs={
        "k": 4,
        "filter": {"category": "intro"}  # Only intro documents
    }
)

# Multiple filters
retriever = vectorstore.as_retriever(
    search_kwargs={
        "k": 4,
        "filter": {
            "category": "advanced",
            "source": "advanced.pdf"
        }
    }
)
```

## Document preprocessing

### Clean documents

```python
def preprocess_doc(doc):
    """Clean and normalize document."""
    # Remove extra whitespace
    doc.page_content = " ".join(doc.page_content.split())

    # Remove special characters
    doc.page_content = re.sub(r'[^\w\s]', '', doc.page_content)

    # Lowercase (optional)
    doc.page_content = doc.page_content.lower()

    return doc

# Apply preprocessing
clean_docs = [preprocess_doc(doc) for doc in docs]
```

### Extract structured data

```python
from langchain.document_transformers import Html2TextTransformer

# HTML to clean text
transformer = Html2TextTransformer()
clean_docs = transformer.transform_documents(html_docs)

# Extract tables
from langchain.document_loaders import UnstructuredHTMLLoader

loader = UnstructuredHTMLLoader("data.html")
docs = loader.load()  # Extracts tables as structured data
```

## Evaluation & monitoring

### Evaluate retrieval quality

```python
from langchain.evaluation import load_evaluator

# Relevance evaluator
evaluator = load_evaluator("relevance", llm=llm)

# Test retrieval
query = "What are Python decorators?"
retrieved_docs = retriever.get_relevant_documents(query)

for doc in retrieved_docs:
    result = evaluator.evaluate_strings(
        input=query,
        prediction=doc.page_content
    )
    print(f"Relevance score: {result['score']}")
```

### Track sources

```python
# Always return sources
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    return_source_documents=True
)

result = qa_chain({"query": "What is Python?"})

# Show sources to user
print(result["result"])
print("\nSources:")
for i, doc in enumerate(result["source_documents"]):
    print(f"[{i+1}] {doc.metadata.get('source', 'Unknown')}")
    print(f"    {doc.page_content[:100]}...")
```

## Best practices

1. **Chunk size matters** - 512-1024 tokens is usually optimal
2. **Add overlap** - 10-20% overlap prevents context loss
3. **Use metadata** - Track sources for citations
4. **Test retrieval quality** - Evaluate before using in production
5. **Hybrid search** - Combine vector + keyword for best results
6. **Compress context** - Remove irrelevant parts before LLM
7. **Cache embeddings** - Expensive, cache when possible
8. **Version your index** - Track changes to knowledge base
9. **Monitor failures** - Log when retrieval doesn't find answers
10. **Update regularly** - Keep knowledge base current

## Common pitfalls

1. **Chunks too large** - Won't fit in context
2. **No overlap** - Important context lost at boundaries
3. **No metadata** - Can't cite sources
4. **Poor splitting** - Breaks mid-sentence or mid-paragraph
5. **Wrong embedding model** - Domain mismatch hurts retrieval
6. **No reranking** - Lower quality results
7. **Ignoring failures** - No handling when retrieval fails

## Performance optimization

### Caching

```python
from langchain.cache import InMemoryCache, SQLiteCache
from langchain.globals import set_llm_cache

# In-memory cache
set_llm_cache(InMemoryCache())

# Persistent cache
set_llm_cache(SQLiteCache(database_path=".langchain.db"))

# Same query uses cache (faster + cheaper)
result1 = qa_chain({"query": "What is Python?"})
result2 = qa_chain({"query": "What is Python?"})  # Cached
```

### Batch processing

```python
# Process multiple queries efficiently
queries = [
    "What is Python?",
    "What are decorators?",
    "How do I use async?"
]

# Batch retrieval
all_docs = vectorstore.similarity_search_batch(queries)

# Batch QA
results = qa_chain.batch([{"query": q} for q in queries])
```

### Async operations

```python
# Async RAG for concurrent queries
import asyncio

async def async_qa(query):
    return await qa_chain.ainvoke({"query": query})

# Run multiple queries concurrently
results = await asyncio.gather(
    async_qa("What is Python?"),
    async_qa("What are decorators?")
)
```

## Resources

- **LangChain RAG Docs**: https://docs.langchain.com/oss/python/langchain/rag
- **Vector Stores**: https://python.langchain.com/docs/integrations/vectorstores
- **Document Loaders**: https://python.langchain.com/docs/integrations/document_loaders
- **Retrievers**: https://python.langchain.com/docs/modules/data_connection/retrievers




---

## 🚀 Usage

**Reference this template:** `@skill-langchain.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
