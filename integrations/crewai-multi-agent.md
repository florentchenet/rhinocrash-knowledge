---
tags: [integration, crewai, multi-agent, orchestration]
date: 2025-12-09
researched-by: Gemini 2.0 Flash
status: GUIDE
priority: HIGH
---

# CrewAI Multi-Agent Orchestration

**Purpose**: Use CrewAI for orchestrating multiple AI agents (Claude + Gemini) in collaborative workflows.

**Researched by**: Gemini 2.0 Flash (delegated)

## What is CrewAI?

CrewAI is a framework for orchestrating **role-playing, autonomous AI agents** that work together to achieve complex objectives.

### Key Concepts

- **Agent**: Individual AI with specific role, goal, backstory, and tools
- **Task**: Specific objective assigned to an agent
- **Crew**: Collection of agents working together
- **Tools**: Functions agents can use (web search, code execution, file I/O)
- **Process**: Defines collaboration pattern (sequential, hierarchical)

### How It Works

```python
1. Define Agents → Specify roles, goals, tools, LLM
2. Define Tasks → Create objectives for each agent
3. Create Crew → Assemble agents + define workflow
4. Run Crew → Execute autonomous collaboration
```

---

## Integration with Claude API

### Setup

```bash
# Install dependencies
pip install crewai anthropic

# Set API key
export ANTHROPIC_API_KEY="your-key-here"
```

### Create Claude-Powered Agent

```python
from crewai import Agent
from anthropic import Anthropic

# Initialize Anthropic client
anthropic_client = Anthropic()

# Create agent using Claude
coding_agent = Agent(
    role='Software Engineer',
    goal='Write efficient and well-documented Python code',
    backstory='Experienced Python developer focusing on data science',
    llm=anthropic_client,  # Use Claude
    verbose=True
)
```

### Important Considerations

- **Cost**: Claude API usage incurs costs - monitor usage
- **Context Window**: Break complex tasks into smaller steps
- **Error Handling**: Handle API errors and rate limits gracefully

---

## Example: Web Scraper with Multi-Agent Collaboration

### Agents

1. **Research Agent** - Researches target website, identifies data
2. **Coding Agent** - Writes Python scraper code
3. **Testing Agent** - Tests scraper, reports issues

### Complete Implementation

```python
from crewai import Crew, Agent, Task
from anthropic import Anthropic

# Initialize Anthropic client
anthropic_client = Anthropic()

# Define Agents
research_agent = Agent(
    role='Research Agent',
    goal='Thoroughly research websites and identify data for scraping',
    backstory='Expert in web research and data extraction techniques',
    llm=anthropic_client,
    verbose=True
)

coding_agent = Agent(
    role='Coding Agent',
    goal='Write efficient Python code for web scraping',
    backstory='Experienced Python developer focused on web scraping',
    llm=anthropic_client,
    verbose=True,
    tools=[scrape_website_tool]  # Custom tool
)

testing_agent = Agent(
    role='Testing Agent',
    goal='Test web scraping code and identify issues',
    backstory='QA engineer expert in testing web applications',
    llm=anthropic_client,
    verbose=True
)

# Define Tasks
research_task = Task(
    description='''
    Research https://www.example.com and identify specific data to scrape
    (product names, prices, descriptions). Provide detailed instructions
    to the coding agent.
    ''',
    agent=research_agent
)

coding_task = Task(
    description='''
    Write Python code using Beautiful Soup and Requests to scrape data
    from https://www.example.com based on research agent's instructions.
    Ensure code is well-documented and handles errors.
    ''',
    agent=coding_agent
)

testing_task = Task(
    description='''
    Test the web scraping code and identify any issues or bugs.
    Provide detailed feedback to the coding agent.
    ''',
    agent=testing_agent
)

# Create Crew
crew = Crew(
    agents=[research_agent, coding_agent, testing_agent],
    tasks=[research_task, coding_task, testing_task],
    verbose=2  # Detailed logging
)

# Run Crew
result = crew.kickoff()

print("Final Result:", result)
```

### Workflow

```
1. Research Agent researches target website
    ↓
2. Provides findings to Coding Agent
    ↓
3. Coding Agent writes scraper code
    ↓
4. Testing Agent tests scraper
    ↓
5. Reports issues to Coding Agent
    ↓
6. Coding Agent fixes issues
    ↓
7. Testing Agent retests
    ↓
8. Final code delivered
```

---

## Installation & Setup

### Step-by-Step

```bash
# 1. Install CrewAI
pip install crewai

# 2. Install Anthropic SDK (for Claude)
pip install anthropic

# 3. Set Anthropic API Key
export ANTHROPIC_API_KEY="your-api-key"

# 4. Install tool dependencies (example)
pip install beautifulsoup4 requests

# 5. Create your agents, tasks, and crew
python your_crew.py
```

---

## CrewAI vs AutoGen

| Feature | CrewAI | AutoGen |
|---------|--------|---------|
| **Focus** | Role-playing agent orchestration | General multi-agent framework |
| **Agent Design** | Strong emphasis on roles/goals | More flexible agent definition |
| **Workflow** | Structured collaboration | Dynamic, emergent behavior |
| **Complexity** | Simpler to set up | Steeper learning curve |
| **Use Cases** | Tasks requiring specific expertise | Wider range of tasks |
| **Tooling** | Relies on external tools | Built-in tools + function calling |
| **Claude Support** | Anthropic SDK integration | Anthropic SDK integration |

### Detailed Comparison

**Abstraction Level**:
- CrewAI: Higher level, focuses on roles and collaboration patterns
- AutoGen: More flexible but requires manual configuration

**Agent Roles**:
- CrewAI: Emphasizes roles and backstories for quality output
- AutoGen: More generic agents

**Workflow Management**:
- CrewAI: Structured workflow definition
- AutoGen: Dynamic behavior, harder to control

**Ease of Use**:
- CrewAI: Easier to set up for simple scenarios
- AutoGen: Requires more configuration and understanding

### When to Choose What

**Choose CrewAI** if you need:
- Simple, structured role-playing agent orchestration
- Specific expertise collaboration (research + code + test)
- Quick setup for defined workflows

**Choose AutoGen** if you need:
- Flexible, powerful complex multi-agent systems
- Research, coding, problem-solving with emergent behavior
- More control over agent interactions

---

## rhinocrash Use Cases

### 1. Feature Development Crew

```python
# Agents:
# - Architect: Designs feature architecture
# - Developer: Implements code
# - Tester: Writes and runs tests
# - Reviewer: Reviews code quality

# Workflow:
spec_task → design_task → code_task → test_task → review_task
```

### 2. Knowledge Base Maintenance Crew

```python
# Agents:
# - Researcher (Gemini): Finds new content
# - Summarizer (Claude): Creates summaries
# - Organizer (Claude): Files content properly
# - Committer (Script): Git operations

# Workflow:
research_task → summarize_task → organize_task → commit_task
```

### 3. Deployment Crew

```python
# Agents:
# - Preparer: Backs up current state
# - Builder: Builds Docker images
# - Deployer: Deploys to server
# - Monitor: Checks health post-deploy

# Workflow:
backup_task → build_task → deploy_task → monitor_task
```

---

## Integration with rhinocrash Architecture

### with Open Interpreter

```python
# Run CrewAI crew from Open Interpreter at ai.rhncrs.com
from crewai import Crew
import interpreter

# Coding agent can use Open Interpreter for execution
coding_agent = Agent(
    role='Coding Agent',
    tools=[interpreter.run],  # Access to full system
    llm=anthropic_client
)
```

### with n8n Workflows

```javascript
// n8n workflow node
{
  "type": "Execute Python",
  "code": "python /opt/rhinocrash/crews/knowledge-sync.py",
  "trigger": "Schedule: Weekly"
}
```

### with Existing Daemons

```python
# Enhance existing claude daemon with CrewAI
# /opt/rhinocrash/daemons/claude/main.py

from crewai import Crew, Agent

orchestrator_agent = Agent(
    role='Orchestrator',
    goal='Coordinate tasks between Claude and Gemini',
    llm=anthropic_client
)

# Delegate subtasks to other daemons
```

---

## Example: rhinocrash Feature Implementation Crew

```python
from crewai import Crew, Agent, Task
from anthropic import Anthropic

anthropic_client = Anthropic()

# Architect Agent (Claude Sonnet)
architect = Agent(
    role='Software Architect',
    goal='Design robust, scalable feature architecture',
    backstory='20+ years designing complex systems',
    llm=anthropic_client,
    verbose=True
)

# Developer Agent (Claude Sonnet)
developer = Agent(
    role='Senior Developer',
    goal='Implement features following best practices',
    backstory='Expert in Python, TypeScript, Docker',
    llm=anthropic_client,
    verbose=True,
    tools=[read_file, write_file, run_tests]
)

# Reviewer Agent (Claude Sonnet)
reviewer = Agent(
    role='Code Reviewer',
    goal='Ensure code quality and security',
    backstory='Security expert, perfectionist',
    llm=anthropic_client,
    verbose=True
)

# Tasks
design_task = Task(
    description='Design authentication system using JWT',
    agent=architect
)

implement_task = Task(
    description='Implement authentication based on architecture spec',
    agent=developer
)

review_task = Task(
    description='Review authentication implementation for security',
    agent=reviewer
)

# Crew
feature_crew = Crew(
    agents=[architect, developer, reviewer],
    tasks=[design_task, implement_task, review_task],
    verbose=2
)

# Run
result = feature_crew.kickoff()
```

---

## Cost Optimization

### Use Mixed Models

```python
# Expensive reasoning: Claude Sonnet
architect = Agent(llm=anthropic_client_sonnet)

# Cheap execution: Claude Haiku
executor = Agent(llm=anthropic_client_haiku)

# Free research: Gemini
researcher = Agent(llm=gemini_client)
```

### Parallel vs Sequential

```python
# Parallel: Run simultaneously (faster, more tokens)
crew = Crew(agents=[a, b, c], process="parallel")

# Sequential: One after another (slower, fewer tokens)
crew = Crew(agents=[a, b, c], process="sequential")
```

---

## Next Steps

1. **Install CrewAI**: `pip install crewai anthropic`
2. **Test Simple Crew**: Run 2-agent collaboration locally
3. **Integrate with Open Interpreter**: Enable full system access
4. **Create Knowledge Sync Crew**: Automate weekly research
5. **Deploy to Server**: Run crews on VPS for 24/7 operation

---

**Status**: Research complete. Ready for implementation.

**Dependencies**:
- CrewAI framework
- Anthropic SDK (Claude)
- Google Generative AI SDK (Gemini)
- Open Interpreter (for system access)

**Recommendation**: Start with simple 2-agent crew (Research + Summarize) to validate before complex workflows.
