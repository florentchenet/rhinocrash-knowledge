---
tags: [architecture, automation, sequential-workflow]
date: 2025-12-09
status: DESIGN
priority: FUTURE
---

# Sequential Auto-Updating Workflow

**Purpose**: Automate repetitive knowledge base maintenance tasks with sequential agent workflows.

## Concept

Create workflows where completion of Task A automatically triggers Task B, forming a chain of autonomous updates.

## Use Cases

### 1. Weekly Knowledge Sync
```
Trigger: Friday 18:00
    ↓
Research Agent: Find new Claude/AI content
    ↓
Summarize Agent: Create summaries
    ↓
Integration Agent: Update knowledge base
    ↓
Git Agent: Commit and push
    ↓
Notification: n8n → Telegram
```

### 2. Error Learning Loop
```
Trigger: Session ends
    ↓
Analysis Agent: Extract errors from logs
    ↓
Pattern Agent: Identify patterns
    ↓
Documentation Agent: Update session-errors-and-fixes.md
    ↓
Git Agent: Commit changes
```

### 3. Documentation Auto-Update
```
Trigger: Code changes detected
    ↓
Code Agent: Analyze changes
    ↓
Doc Agent: Update relevant docs
    ↓
Review Agent: Check accuracy
    ↓
Git Agent: Create PR with docs
```

## Implementation Approaches

### Option A: n8n Workflow (Recommended)
**Pros**: Visual, no-code, already running on server
**Cons**: Limited AI integration currently

```javascript
// n8n workflow nodes
1. Schedule Trigger (cron)
2. Execute Command (delegate to Gemini)
3. Parse Response
4. HTTP Request (call Claude API)
5. File Write
6. Git Operations
7. Telegram Notification
```

### Option B: Claude Agent SDK Sequential
**Pros**: Native Claude integration, powerful
**Cons**: Requires coding, more complex

```python
# Example with Agent SDK
from anthropic import Anthropic

def sequential_workflow():
    client = Anthropic()

    # Task 1: Research
    research_result = client.messages.create(
        model="claude-sonnet-4-5",
        messages=[{"role": "user", "content": "Research new MCP servers"}]
    )

    # Task 2: Summarize (uses Task 1 output)
    summary = client.messages.create(
        model="claude-haiku",
        messages=[{
            "role": "user",
            "content": f"Summarize: {research_result.content}"
        }]
    )

    # Task 3: Update docs (uses Task 2 output)
    update_docs(summary.content)

    # Task 4: Git commit
    commit_and_push()
```

### Option C: Cron + Shell Scripts
**Pros**: Simple, Unix standard
**Cons**: Limited intelligence, brittle

```bash
#!/bin/bash
# ~/scripts/auto-update-knowledge.sh

# Weekly knowledge sync
0 18 * * 5 /Users/hoe/scripts/weekly-knowledge-sync.sh
```

## Recommended Implementation

**Hybrid: n8n + API calls**

### n8n Workflow Design

**Workflow 1: Weekly Knowledge Sync**
```
[Schedule Trigger: Fri 18:00]
    ↓
[Python Script: Gemini API]
  Input: "Research YouTube videos about Claude from this week"
    ↓
[Save to File: /tmp/research-weekly.md]
    ↓
[HTTP: Claude API]
  Input: "Summarize and integrate: {file}"
    ↓
[Write File: knowledge/research/weekly-{date}.md]
    ↓
[Exec: git add && commit && push]
    ↓
[Telegram: Notify user]
  "Weekly knowledge updated: 5 new findings"
```

**Workflow 2: Session Learning**
```
[Webhook Trigger: Session end]
    ↓
[Read File: ~/.claude/logs/latest-session.json]
    ↓
[Python: Extract errors]
    ↓
[HTTP: Claude API]
  "Analyze errors and create lessons learned"
    ↓
[Append to: learnings/session-errors-and-fixes.md]
    ↓
[Exec: git commit]
```

**Workflow 3: Documentation Sync**
```
[File Watcher: rhinocrash-knowledge/**/*.md]
    ↓
[On Change Detected]
    ↓
[HTTP: Claude API]
  "Review doc change for completeness"
    ↓
[If Issues Found]
    ↓
[Create TODO in TodoWrite]
```

## Setup Steps

### 1. n8n Configuration

```bash
# On server (already running at n8n.rhncrs.com)
ssh root@188.245.183.171

# Create workflows directory
mkdir -p /opt/rhinocrash/n8n-workflows

# Create workflow JSON files
cat > /opt/rhinocrash/n8n-workflows/weekly-knowledge-sync.json <<'EOF'
{
  "name": "Weekly Knowledge Sync",
  "nodes": [
    {
      "type": "n8n-nodes-base.scheduleTrigger",
      "parameters": {
        "rule": {
          "interval": [
            {
              "field": "cronExpression",
              "expression": "0 18 * * 5"
            }
          ]
        }
      }
    },
    {
      "type": "n8n-nodes-base.executeCommand",
      "parameters": {
        "command": "python3 /home/scripts/gemini-research.py"
      }
    }
  ]
}
EOF
```

### 2. API Integration Scripts

```bash
# Create scripts for n8n to call
cat > ~/scripts/gemini-research.py <<'EOF'
#!/usr/bin/env python3
import sys
from gemini_api import query_gemini

result = query_gemini("Research new Claude developments this week")
with open('/tmp/research-weekly.md', 'w') as f:
    f.write(result)
print(result)
EOF

chmod +x ~/scripts/gemini-research.py
```

### 3. Git Automation

```bash
# Create git commit helper
cat > ~/scripts/auto-commit-knowledge.sh <<'EOF'
#!/bin/bash
cd ~/Documents/Dev/rhinocrash-knowledge

if [ -n "$(git status --porcelain)" ]; then
    git add -A
    git commit -m "auto: Weekly knowledge sync $(date +%Y-%m-%d)"
    git push
    echo "Committed and pushed changes"
else
    echo "No changes to commit"
fi
EOF

chmod +x ~/scripts/auto-commit-knowledge.sh
```

## Security Considerations

### API Keys
```bash
# Store in n8n credentials
# Never commit API keys to git
# Use environment variables

# In n8n workflow:
$credentials.anthropic.apiKey
$credentials.gemini.apiKey
```

### File Permissions
```bash
# Scripts should be user-executable only
chmod 700 ~/scripts/auto-*.sh

# Knowledge base world-readable
chmod 755 ~/Documents/Dev/rhinocrash-knowledge
```

## Monitoring & Alerts

### Success Metrics
- Commits per week (target: 1 auto-commit)
- Research findings (target: 3-5 new items)
- Error patterns identified (measure: reduction over time)

### Failure Alerts
```javascript
// n8n error handling node
if (workflowFailed) {
    sendTelegram({
        message: `⚠️ Auto-update workflow failed: ${error}`,
        chat_id: process.env.TELEGRAM_CHAT_ID
    });
}
```

## Testing Workflow

### Manual Test
```bash
# Test each component individually
~/scripts/gemini-research.py
~/scripts/auto-commit-knowledge.sh

# Test n8n workflow with "Execute Workflow" button
```

### Dry Run
```bash
# Add --dry-run flag to git commands
git commit --dry-run -m "test"
git push --dry-run
```

## Rollback Plan

### Disable Workflows
```bash
# In n8n UI: Toggle workflow "Active" to OFF
# Or via API:
curl -X POST http://n8n.rhncrs.com/api/v1/workflows/123/deactivate \
  -H "X-N8N-API-KEY: ${API_KEY}"
```

### Revert Changes
```bash
cd ~/Documents/Dev/rhinocrash-knowledge
git log --oneline | grep "auto:"
git revert <commit-hash>
```

## Future Enhancements

1. **Intelligent Scheduling**: Trigger based on events, not just time
2. **Confidence Scoring**: Agent reviews other agents' work
3. **Multi-stage Approval**: Human-in-loop for critical changes
4. **Performance Metrics**: Track token usage, response quality
5. **Self-Improvement**: Workflows that improve their own prompts

## Example: Complete Weekly Sync

### Friday 18:00
```
1. n8n triggers workflow
2. Gemini researches: YouTube, GitHub, Anthropic docs
3. Claude summarizes findings
4. Script writes to knowledge/research/weekly-2025-12-13.md
5. Git commits and pushes
6. Telegram: "✅ 4 new findings added to knowledge base"
7. User reviews on Saturday morning
```

### Time Savings
- Manual research: 2 hours/week
- Automated: 15 min API calls
- **Savings: 1.75 hours/week = 91 hours/year**

---

**Status**: Design complete. Ready for implementation when prioritized.

**Dependencies**:
- n8n running (✅ already at n8n.rhncrs.com)
- API keys in environment (✅ have both Claude & Gemini)
- Git access (✅ configured)

**Next Steps**:
1. Create first n8n workflow (weekly sync)
2. Test manually
3. Enable automation
4. Monitor for 2 weeks
5. Iterate based on results
