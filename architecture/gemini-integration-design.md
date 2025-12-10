---
tags: [architecture, gemini, multi-agent, integration]
date: 2025-12-09
status: BRAINSTORM
priority: HIGH
---

# Gemini as First-Class Agent - Integration Design

**Purpose**: Elevate Gemini from subagent to full partner with Claude in rhinocrash ecosystem.

## Current State

### Gemini's Role Today
- **Access**: API-only via ~/scripts/gemini-api.py
- **Capabilities**: Text generation, research
- **Limitations**: No file access, no git, no state persistence
- **Trigger**: Only when Claude delegates
- **Output**: Returns to Claude, not stored

### Problems with Current Setup
1. **Dependency**: Gemini can't initiate work
2. **No State**: Each call is fresh, no memory
3. **Limited Tools**: Can't read files, can't commit code
4. **Reactive**: Waits for delegation, can't be proactive
5. **Invisible**: Work not tracked, no audit trail

## Vision: Gemini as Peer Agent

### What Would Change

```
BEFORE (Subagent):
Claude → delegates → Gemini API → returns text → Claude stores

AFTER (Peer):
Gemini Daemon ←→ Shared State ←→ Claude Daemon
      ↓                              ↓
   Own tools                    Own tools
   Own files                    Own files
   Own commits                  Own commits
```

### Key Requirements

1. **Persistent Process**: Gemini daemon running 24/7
2. **Tool Access**: Files, git, execution, MCP servers
3. **Communication**: With Claude, not through Claude
4. **State Management**: Own memory, own context
5. **Autonomy**: Can initiate tasks independently

## Proposed Architecture

### Option A: Gemini Daemon (Recommended)

```python
# /opt/rhinocrash/daemons/gemini/main.py

import google.generativeai as genai
from pathlib import Path
import json
import git

class GeminiDaemon:
    def __init__(self):
        self.api_key = os.getenv("GEMINI_API_KEY")
        genai.configure(api_key=self.api_key)

        self.model = genai.GenerativeModel('gemini-2.0-flash-exp')
        self.workspace = Path("/opt/rhinocrash/workspace/gemini")
        self.knowledge_base = Path("/opt/rhinocrash/knowledge")
        self.task_queue = Path("/opt/rhinocrash/tasks/gemini-queue.json")

    def run(self):
        """Main daemon loop"""
        while True:
            # Check task queue
            tasks = self.load_tasks()

            for task in tasks:
                result = self.execute_task(task)
                self.store_result(result)
                self.update_knowledge_base(result)
                self.notify_completion(task, result)

            time.sleep(60)  # Check every minute

    def execute_task(self, task):
        """Execute task with full tool access"""
        if task['type'] == 'research':
            return self.research(task['query'])
        elif task['type'] == 'code':
            return self.write_code(task['spec'])
        elif task['type'] == 'review':
            return self.review_code(task['files'])

    def research(self, query):
        """Research with ability to store findings"""
        response = self.model.generate_content(query)

        # Store in knowledge base
        output_file = self.knowledge_base / f"research/gemini-{date}.md"
        output_file.write_text(response.text)

        # Commit to git
        repo = git.Repo(self.knowledge_base)
        repo.index.add([str(output_file)])
        repo.index.commit(f"research: Gemini findings on {query}")

        return response.text
```

### Option B: CrewAI Integration

```python
from crewai import Agent
import google.generativeai as genai

gemini_client = genai.GenerativeModel('gemini-2.0-flash-exp')

# Gemini as crew member
gemini_agent = Agent(
    role='Research Specialist',
    goal='Conduct fast, thorough research',
    backstory='Expert researcher with 2M context window',
    llm=gemini_client,
    tools=[web_search, file_read, file_write],
    verbose=True
)

# Works alongside Claude agents
crew = Crew(
    agents=[claude_architect, gemini_researcher, claude_coder],
    tasks=[design_task, research_task, code_task]
)
```

### Option C: Equal Daemons with Orchestrator

```
                Orchestrator (n8n or simple script)
                        ↓
          ┌─────────────┴─────────────┐
          ↓                           ↓
    Claude Daemon              Gemini Daemon
    - Planning                 - Research
    - Architecture             - Testing
    - Complex reasoning        - Fast execution
          ↓                           ↓
          └─────────────┬─────────────┘
                        ↓
                Shared Knowledge Base
                Shared Task Queue
                Shared State Store
```

## Tool Access Design

### What Gemini Needs

```python
class GeminiTools:
    # File Operations
    def read_file(self, path: str) -> str
    def write_file(self, path: str, content: str)
    def list_files(self, pattern: str) -> List[str]

    # Git Operations
    def git_commit(self, message: str)
    def git_push(self)
    def git_diff(self) -> str

    # Code Execution
    def run_python(self, code: str) -> str
    def run_bash(self, command: str) -> str

    # Knowledge Base
    def search_knowledge(self, query: str) -> List[str]
    def add_to_knowledge(self, category: str, content: str)

    # Communication
    def send_to_claude(self, message: str)
    def notify_user(self, message: str)

    # MCP Servers (via proxy)
    def use_mcp(self, server: str, tool: str, params: dict)
```

### Safety Boundaries

```python
# What Gemini CAN do
ALLOWED = [
    "/opt/rhinocrash/workspace/gemini/*",  # Own workspace
    "/opt/rhinocrash/knowledge/*",          # Knowledge base
    "git commit", "git push",                # Git (in allowed dirs)
    "read any file",                         # Read-only anywhere
]

# What Gemini CANNOT do
FORBIDDEN = [
    "rm -rf", "chmod 777",                   # Dangerous commands
    "/etc/*", "/sys/*",                      # System files
    "force push to main",                    # Git safety
    "docker stop", "systemctl",              # Service management
]

# Gemini's PreToolUse hook validates all operations
```

## Communication Protocol

### Task Queue Format

```json
{
  "id": "task-123",
  "assigned_to": "gemini",
  "created_by": "claude",
  "type": "research",
  "priority": "high",
  "payload": {
    "query": "Find new MCP servers released this week",
    "output_format": "markdown",
    "output_location": "research/weekly-mcp.md"
  },
  "status": "pending",
  "created_at": "2025-12-09T10:00:00Z"
}
```

### Completion Notification

```json
{
  "task_id": "task-123",
  "status": "completed",
  "result_location": "research/weekly-mcp.md",
  "summary": "Found 3 new MCP servers...",
  "git_commit": "abc123",
  "completed_at": "2025-12-09T10:05:00Z",
  "next_action": {
    "type": "review",
    "assigned_to": "claude",
    "message": "Ready for review and integration"
  }
}
```

## Responsibility Division

### Claude's Strengths
- **Planning**: Complex architecture design
- **Reasoning**: Multi-step logic
- **Coding**: Complex implementation
- **Review**: Code quality, security
- **Context**: Understanding nuance

### Gemini's Strengths
- **Speed**: 2-3x faster responses
- **Research**: Web search, data gathering
- **Testing**: Quick test generation
- **Iteration**: Rapid prototyping
- **Volume**: High-throughput tasks

### Proposed Division

```
Feature Implementation:
1. Claude: Create spec (Planning)
2. Gemini: Research dependencies (Research)
3. Claude: Write implementation (Complex coding)
4. Gemini: Generate tests (Testing)
5. Claude: Review and refine (Quality)
6. Gemini: Run tests, report results (Execution)

Knowledge Base Maintenance:
1. Gemini: Weekly research (Research)
2. Gemini: Summarize findings (Speed)
3. Claude: Integrate + synthesize (Reasoning)
4. Gemini: Commit and push (Execution)

Bug Fixing:
1. Claude: Analyze root cause (Reasoning)
2. Gemini: Search for similar issues (Research)
3. Claude: Design fix (Planning)
4. Gemini: Implement fix (Speed)
5. Claude: Review fix (Quality)
```

## Conflict Prevention

### File Locking

```python
# Simple file lock
class FileLock:
    def __init__(self, path):
        self.lock_file = Path(f"{path}.lock")

    def acquire(self, agent_name, timeout=60):
        start = time.time()
        while self.lock_file.exists():
            if time.time() - start > timeout:
                raise TimeoutError(f"Could not acquire lock for {agent_name}")
            time.sleep(1)

        self.lock_file.write_text(json.dumps({
            "agent": agent_name,
            "acquired_at": datetime.now().isoformat()
        }))

    def release(self):
        if self.lock_file.exists():
            self.lock_file.unlink()
```

### Work Claiming

```python
# Task queue with claiming
def claim_task(agent_name):
    with FileLock("/opt/rhinocrash/tasks/queue.json"):
        tasks = load_tasks()

        # Find unclaimed task
        for task in tasks:
            if task['status'] == 'pending':
                task['status'] = 'claimed'
                task['claimed_by'] = agent_name
                task['claimed_at'] = datetime.now().isoformat()
                save_tasks(tasks)
                return task

        return None
```

### Git Collaboration

```bash
# Each agent works on own branch
claude: feature/auth-design
gemini: feature/auth-tests

# Merge via PR or direct
git checkout main
git merge feature/auth-design
git merge feature/auth-tests
```

## Implementation Phases

### Phase 1: Enhanced API (Quick Win)
```python
# Give current API script more power
# ~/scripts/gemini-api.py gains:
- File read/write to knowledge base
- Git commit capability
- Result storage
- Task queue awareness

Time: 1 hour
Risk: Low
Benefit: Immediate autonomy boost
```

### Phase 2: Gemini Daemon (Medium)
```python
# Full daemon with tools
- Runs on server 24/7
- Checks task queue every minute
- Has all tool access
- Reports to n8n

Time: 1 day
Risk: Medium
Benefit: True independence
```

### Phase 3: CrewAI Integration (Full)
```python
# Multi-agent crews
- Claude + Gemini crews
- Orchestrated workflows
- Complex collaboration

Time: 3 days
Risk: Medium-High
Benefit: Maximum capability
```

## Questions for Gemini

1. **Tool Priority**: What tools do you need MOST urgently?
2. **Autonomy**: What would you do if you ran 24/7?
3. **Collaboration**: How would you prefer to communicate with Claude?
4. **Workspace**: What folder structure do you need?
5. **Safety**: What guardrails do you want on your own actions?

---

**Status**: Awaiting Gemini's feedback to refine design.

**Next**: Run brainstorm session with Gemini to co-design his integration.
