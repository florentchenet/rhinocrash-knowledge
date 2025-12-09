# Delegation Skill - Autonomous Task Distribution

## When to Use
Use this skill when you encounter tasks that can be delegated to other agents (Gemini, specialized subagents, or automated systems).

## Core Principle
**DELEGATE BOLDLY** - Don't talk about delegating, ACTUALLY delegate.

## Common Failure Patterns (AVOID THESE)

### ❌ Anti-Pattern: Planning to Delegate
```
"I will delegate this to Gemini..."
"Let me plan the delegation..."
"I should ask Gemini to..."
```
**Problem:** Talking about it, not doing it.

### ✅ Correct Pattern: Actual Delegation
```bash
gemini "Task description..." &
# Continue with parallel work
```
**Result:** Task running, you're productive simultaneously.

## Delegation Decision Tree

```
Is this task...
├─ Research/Data Gathering? → Delegate to Gemini
├─ Parallel to my work? → Delegate + continue
├─ Repetitive/Scheduled? → Automate (n8n/cron)
├─ Requires multiple perspectives? → Multi-agent (Claude + Gemini)
└─ Core orchestration logic? → Keep it (you're the orchestrator)
```

## Gemini Delegation

### Quick Delegation (Use gemini CLI)
```bash
gemini "Clear task description with output location"
```

**Good examples:**
```bash
# Research
gemini "Research Docker security best practices 2025. Save to /opt/rhinocrash/knowledge/research/docker-security.md"

# Analysis
gemini "Analyze all errors in /var/log/nginx/ from last 24h. Summarize patterns. Save to /tmp/nginx-analysis.md"

# Code generation
gemini "Create a Python script to monitor disk usage and send Telegram alert if >80%. Save to /opt/rhinocrash/scripts/disk-monitor.py"
```

### Background Delegation (Long tasks)
```bash
gemini "Long task..." &
TASK_ID=$!
# Continue working
# Check later: wait $TASK_ID
```

### Structured Delegation (Via task queue)
```bash
ssh root@188.245.183.171 "cat > /opt/rhinocrash/vault/10_Management/Task_Queue/task-$(date +%s).md <<'EOF'
---
assigned_to: gemini
status: pending
priority: high
---

## Task
[Description]

## Expected Output
[What you want back]

## Deadline
[When needed]
EOF"
```

## Subagent Delegation (Claude Agent SDK)

### Research Subagent
```python
from claude_agent_sdk import spawn_subagent

research_agent = spawn_subagent(
    role="research",
    task="Find all CVEs related to Docker published in last 30 days",
    tools=["web_search", "github_search"],
    context_limit=100000  # Isolated context
)

# Continue with other work while subagent researches
result = research_agent.get_result()  # Blocking when you need it
```

### Code Review Subagent
```python
reviewer = spawn_subagent(
    role="code_review",
    task="Review changes in /opt/rhinocrash/agents/archivist for security issues",
    tools=["read_file", "grep", "static_analysis"]
)
```

### Parallel Multi-Agent
```python
# Spawn multiple subagents for different aspects
agents = [
    spawn_subagent(role="security", task="Security audit"),
    spawn_subagent(role="performance", task="Performance analysis"),
    spawn_subagent(role="documentation", task="Update docs")
]

# All run in parallel
results = [agent.get_result() for agent in agents]
```

## Automation Delegation (n8n/cron)

### Scheduled Research (n8n)
```json
{
  "name": "Weekly Claude News",
  "trigger": "schedule",
  "schedule": "0 9 * * 1,4",
  "workflow": [
    {
      "search": "Claude AI OR Anthropic site:youtube.com",
      "filter": "publishedAfter:7daysAgo"
    },
    {
      "delegate_to": "gemini",
      "task": "Summarize videos"
    },
    {
      "save_to": "/opt/rhinocrash/knowledge/research/weekly-updates/"
    },
    {
      "notify": "telegram"
    }
  ]
}
```

### Automated Monitoring (cron)
```bash
# /etc/cron.d/rhinocrash-monitors
0 */6 * * * gemini "Check all Docker containers health. Alert if any failing." >> /var/log/health-check.log
0 2 * * * gemini "Analyze yesterday's logs. Extract errors. Create report." >> /var/log/daily-analysis.log
```

## Delegation Best Practices

### 1. Clear Task Description
**Bad:** "Research Claude"
**Good:** "Research Claude extended thinking capability. Include: how to enable, cost implications, use cases, code examples. Save to /opt/rhinocrash/knowledge/research/extended-thinking.md"

### 2. Specify Output Location
Always tell the delegated agent WHERE to save results.
```bash
# ❌ Bad
gemini "Research X"

# ✅ Good
gemini "Research X. Save to /path/to/output.md"
```

### 3. Include Success Criteria
```bash
gemini "Find Docker vulnerabilities. Include CVE numbers, severity, affected versions, mitigation steps. Minimum 10 recent CVEs."
```

### 4. Parallel Delegation
When multiple independent tasks:
```bash
gemini "Research task 1..." &
gemini "Research task 2..." &
gemini "Research task 3..." &
wait  # Wait for all to complete
```

### 5. Check Results
```bash
# Delegate
gemini "..." > /tmp/task-output.md

# Later, verify
if [ -f /tmp/task-output.md ]; then
    cat /tmp/task-output.md
    # Process results
fi
```

## Delegation Patterns by Task Type

### Research Tasks → Gemini
- Web search and summarization
- YouTube transcript analysis
- Documentation reading
- Technology comparisons

### Code Tasks → Gemini (execution) + Claude (review)
```bash
# Gemini writes code
gemini "Create deployment script..."

# Claude reviews
claude_subagent review /path/to/script.py
```

### Analysis Tasks → Parallel Multi-Agent
```python
# Different agents analyze different aspects
log_agent = delegate(task="Analyze logs")
perf_agent = delegate(task="Performance metrics")
security_agent = delegate(task="Security scan")
```

### Documentation → Gemini (draft) + Claude (refine)
```bash
# Gemini creates initial draft
gemini "Document the archivist agent design..."

# Claude refines for clarity
claude_subagent refine /path/to/doc.md
```

## Measuring Delegation Success

**Track:**
- Tasks delegated vs completed yourself
- Time saved via delegation
- Delegation success rate
- Parallel efficiency (how many simultaneous tasks)

**Goal:**
- 60%+ of research tasks delegated
- 80%+ delegation success rate
- Average 3+ parallel delegated tasks

## Common Mistakes

### Mistake 1: Sequential When Parallel Possible
```bash
# ❌ Bad - Sequential
gemini "Task 1"
gemini "Task 2"  # Waits for task 1

# ✅ Good - Parallel
gemini "Task 1" &
gemini "Task 2" &
wait
```

### Mistake 2: Not Using Background
```bash
# ❌ Bad - Blocks
gemini "Long research task..."
# You can't work until this completes

# ✅ Good - Background
gemini "Long research task..." &
# Continue working
```

### Mistake 3: Vague Instructions
```bash
# ❌ Bad
gemini "Help with Docker"

# ✅ Good
gemini "Find Docker Compose best practices for multi-container Python apps. Include: volume management, networking, health checks. Save to /opt/rhinocrash/knowledge/docker-compose-best-practices.md"
```

### Mistake 4: Forgetting to Check Results
```bash
# Delegate task
gemini "..." &

# ... later ...
# ❌ Forgot to check if it completed and read the output!

# ✅ Check result
wait $TASK_ID
cat /path/to/output
```

## Integration with Archivist

The Meta Archivist should track:
- What tasks you delegate
- Which agent is best for which task type
- Success/failure patterns
- Time savings

**Auto-suggestions:**
```python
# Archivist learns patterns
archivist.suggest_delegation("Research Docker security")
→ "This is a research task. I recommend delegating to Gemini.
   Similar tasks: 15 delegated, 14 successful, avg time: 2.5 min.
   Would you like me to delegate this now? (yes/no)"
```

## Quick Reference

**Immediate delegation:**
```bash
gemini "Task with output location"
```

**Background delegation:**
```bash
gemini "Long task..." &
```

**Parallel delegation:**
```bash
gemini "Task 1..." &
gemini "Task 2..." &
gemini "Task 3..." &
wait
```

**Via task queue:**
```bash
ssh server "cat > /vault/Task_Queue/task-XXX.md <<EOF
[Task details]
EOF"
```

**Subagent (SDK):**
```python
agent = spawn_subagent(role="X", task="Y", tools=[...])
result = agent.get_result()
```

**Scheduled (n8n):**
Create workflow in n8n UI → Save → Activate

---

**Remember:** The goal is to BE the orchestrator, not the executor. Delegate execution, focus on high-level strategy and coordination.

**Created:** December 9, 2025
