---
tags: [research, multi-model, collaboration, memory-sharing, crewai, autogen]
date: 2025-12-09
researched-by: Gemini 2.0 Flash
relevance: high
---

# Multi-Model Collaboration & Memory Sharing Tools

**Research Question:** How to share memory and context between different AI models (Claude, Gemini, DeepSeek, ChatGPT, etc.)?

## Key Challenges

**Heterogeneous Architectures:** Different models have different internal representations, making direct memory transfer impossible.

**API-Driven Interaction:** Most interaction happens through APIs, limiting access to internal states.

**Context Length Limits:** Each model has different context windows - efficient summarization required.

**Semantic Alignment:** Ensuring different models interpret shared information consistently.

**Security/Privacy:** Sharing information between models raises concerns.

## Solution: Agent Orchestration Frameworks

## 1. CrewAI ⭐

**What it is:** Framework for orchestrating role-playing, autonomous AI agents.

**Key Features:**
- **Role-Based Agents:** Define agents with specific roles, goals, backstories
- **Task Management:** Assign and manage task execution
- **Collaboration:** Agents work together toward common goals
- **Tool Use:** Agents can use external tools
- **Flexible Execution:** Sequential, hierarchical task strategies

**Supported Models:** Any LLM via Langchain:
- OpenAI (GPT-3.5, GPT-4)
- Anthropic Claude
- Google Gemini
- Open-source models

**Memory/Context Sharing:**
- Task results passed between agents
- Contextual awareness of conversation history
- Tool-based information sharing

**Installation:**
```bash
pip install crewai
```

**Example:**
```python
from crewai import Crew, Agent, Task

# Different models for different agents
researcher = Agent(
    role='Researcher',
    goal='Find information',
    llm=Claude(model="claude-opus-4-5")
)

writer = Agent(
    role='Writer',
    goal='Write report',
    llm=Gemini(model="gemini-3-pro-preview")
)

research_task = Task(
    description='Research AI impact on society',
    agent=researcher
)

write_task = Task(
    description='Write report from research',
    agent=writer
)

crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, write_task]
)

result = crew.kickoff()
```

**GitHub:** https://github.com/joaomdmoura/crewAI

**Use Cases:**
- Research and reporting
- Software development
- Customer service
- Content creation

## 2. AutoGen (Microsoft) ⭐⭐

**What it is:** Framework for conversational AI agents that collaborate.

**Key Features:**
- **Conversational Agents:** Communicate via messages
- **Multiple Agent Types:** User proxies, assistants, specialized tool-users
- **Communication Patterns:** Peer-to-peer, group chat
- **Shared Memory:** Built-in memory mechanisms
- **Knowledge Graphs:** Represent and share knowledge

**Supported Models:**
- OpenAI
- Azure OpenAI
- Anthropic Claude
- Google PaLM
- Other LLMs + tools/APIs

**Memory/Context Sharing:**
- Message passing between agents
- Shared memory mechanism
- Knowledge graphs

**Installation:**
```bash
pip install pyautogen
```

**Example:**
```python
import autogen

config_list = [
    {"model": "gpt-4", "api_key": "..."},
    {"model": "claude-opus-4-5", "api_key": "..."},
    {"model": "gemini-3-pro-preview", "api_key": "..."}
]

user_proxy = autogen.UserProxyAgent(
    name="User",
    human_input_mode="TERMINATE",
    llm_config={"config_list": config_list}
)

assistant = autogen.AssistantAgent(
    name="Assistant",
    llm_config={"config_list": config_list}
)

user_proxy.initiate_chat(assistant, message="Your task...")
```

**GitHub:** https://github.com/microsoft/autogen

**Use Cases:**
- Code generation and debugging
- Data analysis
- Question answering
- Task automation

## 3. Camel (Calibrated Agent Modeling)

**What it is:** Framework for simulating conversations between AI agents to generate training data.

**Approach:** Role-playing with "task specialist" and "task requester" agents.

**Supported Models:** Primarily OpenAI (GPT-3, GPT-4), adaptable to others.

**Memory Sharing:**
- Full conversation history
- Role awareness

**Installation:**
```bash
pip install camel-ai
```

**GitHub:** https://github.com/lightaime/camel

**Use Cases:**
- Generating LLM training data
- Simulating user interactions
- Evaluating AI agent performance

## 4. LMQL (Language Model Query Language)

**What it is:** SQL-like programming language for controlling LLMs.

**Key Features:**
- Define constraints
- Specify sampling strategies
- Orchestrate multiple LLM calls
- Variables for context sharing

**Supported Models:**
- OpenAI
- Hugging Face Transformers
- Other LLMs

**Memory Sharing:**
- Variables store/share information between calls
- Constraints ensure consistency

**Installation:**
```bash
pip install lmql
```

**Example:**
```lmql
argmax
    "Research by {researcher_model}: [research]"
    "Report by {writer_model}: [report]"
    WHERE
        researcher_model = "claude-opus-4-5"
        writer_model = "gemini-3-pro-preview"
        report CONTAINS research
```

**GitHub:** https://github.com/eth-sri/lmql

**Use Cases:**
- Multi-step reasoning
- Data extraction
- Orchestrating LLM calls

## 5. LangChain (with Memory Modules)

**What it is:** Framework for building LLM applications with built-in memory.

**Memory Types:**
- **ConversationBufferMemory:** Full conversation history
- **ConversationSummaryMemory:** Summarized history
- **ConversationBufferWindowMemory:** Fixed-size window
- **ConversationKGMemory:** Knowledge graph representation

**Supported Models:** Wide range (OpenAI, Claude, Gemini, PaLM, open-source)

**Installation:**
```bash
pip install langchain
```

**Example:**
```python
from langchain.llms import OpenAI
from langchain.chains import ConversationChain
from langchain.memory import ConversationBufferMemory

llm = OpenAI(temperature=0)
memory = ConversationBufferMemory()
conversation = ConversationChain(llm=llm, memory=memory)

conversation.predict(input="Hi, I'm John.")
conversation.predict(input="What's my name?")
# Memory allows: "Your name is John."
```

**GitHub:** https://github.com/langchain-ai/langchain

**Use Cases:**
- Chatbots with memory
- Question answering
- Text summarization
- Code generation

## Comparison Matrix

| Feature | CrewAI | AutoGen | Camel | LMQL | LangChain |
|---------|--------|---------|-------|------|-----------|
| **Ease of Use** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ |
| **Flexibility** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| **Multi-Model** | ✅ | ✅ | ⚠️ | ✅ | ✅ |
| **Memory Sharing** | Task-based | Shared memory | Conversation | Variables | Memory modules |
| **Best For** | Role-based tasks | Complex collaboration | Training data | Query orchestration | Single-chain apps |

## Recommendations for RhinoCrash System

### Option A: CrewAI (Easiest)
**Use when:** You want role-based agents (researcher, executor, reviewer)

**Setup:**
```python
# Claude for planning
planner = Agent(role='Planner', llm=Claude(...))

# Gemini for execution
executor = Agent(role='Executor', llm=Gemini(...))

# Claude for review
reviewer = Agent(role='Reviewer', llm=Claude(...))

crew = Crew(agents=[planner, executor, reviewer], tasks=[...])
```

### Option B: AutoGen (Most Powerful)
**Use when:** You need sophisticated multi-agent collaboration

**Setup:**
```python
# Different models for different strengths
config_list = [
    {"model": "claude-opus-4-5"},  # Complex reasoning
    {"model": "gemini-3-pro-preview"},  # Fast execution
    {"model": "deepseek-chat"}  # Code specialization
]

# AutoGen automatically routes to best model
```

### Option C: Hybrid (Recommended)
**Use:** CrewAI for high-level orchestration + LangChain for memory

**Why:**
- CrewAI manages agent roles and tasks
- LangChain provides persistent memory
- Best of both worlds

## Implementation for RhinoCrash

### Phase 1: Basic Setup
```bash
pip install crewai langchain google-generativeai anthropic
```

### Phase 2: Define Agents
```python
# research-agent (Gemini for speed)
research_agent = Agent(
    role='Researcher',
    llm=Gemini(model='gemini-3-pro-preview'),
    memory=ConversationBufferMemory()
)

# architect-agent (Claude for reasoning)
architect_agent = Agent(
    role='Architect',
    llm=Claude(model='claude-opus-4-5'),
    memory=ConversationBufferMemory()
)

# executor-agent (Gemini for speed)
executor_agent = Agent(
    role='Executor',
    llm=Gemini(model='gemini-3-pro-preview'),
    memory=ConversationBufferMemory()
)
```

### Phase 3: Create Crew
```python
crew = Crew(
    agents=[research_agent, architect_agent, executor_agent],
    tasks=[research, plan, execute],
    memory=SharedMemory()  # Cross-agent memory
)
```

### Phase 4: Integration with Existing System
- CrewAI crew → Calls ~/scripts/gemini-api.py
- Memory → Stored in MCP memory server
- Results → Archived in knowledge base

## Cost Considerations

**API Costs per 1M tokens:**
- GPT-4: $30 input, $60 output
- Claude Opus: $15 input, $75 output
- Gemini Pro: $7 input, $21 output
- DeepSeek: $0.27 input, $1.10 output

**Strategy:** Use expensive models (Claude Opus) for planning, cheaper models (Gemini, DeepSeek) for execution.

## Security Considerations

1. **API Keys:** Store securely, never in code
2. **Data Privacy:** Don't share sensitive data between models
3. **Rate Limiting:** Implement to avoid excessive costs
4. **Validation:** Verify outputs before acting

## Next Steps

1. **Install CrewAI:** `pip install crewai`
2. **Test with 2 models:** Claude + Gemini
3. **Create simple crew:** Researcher + Writer
4. **Add memory:** LangChain ConversationBufferMemory
5. **Integrate with knowledge base:** Save results
6. **Scale:** Add more agents and models

## Resources

- **CrewAI Docs:** https://github.com/joaomdmoura/crewAI
- **AutoGen Docs:** https://github.com/microsoft/autogen
- **LangChain Docs:** https://python.langchain.com/docs/
- **LMQL Docs:** https://lmql.ai/

---

**Researched:** December 9, 2025
**Researcher:** Gemini 2.0 Flash (delegated)
**Key Finding:** CrewAI + LangChain hybrid approach best for RhinoCrash system
**Next Action:** Implement basic CrewAI setup with Claude + Gemini
