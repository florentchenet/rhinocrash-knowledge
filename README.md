# RhinoCrash Knowledge Base

**Living knowledge base for AI agents - learnings, capabilities, architecture, and institutional memory.**

🔗 **Repository:** https://github.com/florentchenet/rhinocrash-knowledge

## Quick Start for New Instances

If you're a new Claude/Gemini instance joining the RhinoCrash system, **read these files first:**

### 1. Start Here (5 min)
- **`learnings/session-errors-and-fixes.md`** - Common errors and solutions
  - Learn from past mistakes
  - Don't repeat solved problems
  - Quick reference for error patterns

### 2. Understand Capabilities (10 min)
- **`research/claude-capabilities-dec-2025.md`** - What you can do
  - Extended thinking
  - Tool search
  - Agent SDK features
  - Programmatic tool calling

### 3. Learn to Delegate (5 min)
- **`templates/delegation-skill.md`** - How to delegate effectively
  - **Key lesson:** ACTUALLY delegate, don't just talk about it
  - Use Gemini for research/execution
  - Parallel task execution
  - Anti-patterns to avoid

### 4. Configuration (5 min)
- **`architecture/bare-metal-configuration.md`** - Direct API access
  - API keys location: `/opt/rhinocrash/infrastructure/.env`
  - No OAuth loops
  - Bare metal mode for unfiltered work

### Total onboarding time: ~25 minutes

## What's In This Repo

```
rhinocrash-knowledge/
├── learnings/              # Lessons from sessions
│   ├── session-errors-and-fixes.md
│   └── todo-archive-*.md
│
├── research/               # Technical research
│   ├── claude-capabilities-dec-2025.md
│   ├── youtube-claude-insights-dec2025.md (in progress)
│   └── indydevdan-claude-analysis.md (in progress)
│
├── architecture/           # System designs
│   ├── meta-archivist-agent.md
│   └── bare-metal-configuration.md
│
└── templates/              # Reusable patterns
    └── delegation-skill.md
```

## Current Infrastructure

### Running Systems
- **ai.rhncrs.com** - Open Interpreter with Claude Sonnet 4 & Gemini 3 Pro
- **ide.rhncrs.com** - code-server (currently stopped)
- **n8n** - Workflow automation
- **Obsidian Vault** - Shared memory at `/opt/rhinocrash/vault`

### Daemons (Currently Stopped)
- **Claude Daemon** - Orchestrator role
- **Gemini Daemon** - Executor role

### Agents (Partially Implemented)
- Profile, Meta, Infrastructure, Docs, Brew, Manager

### Keys & Secrets
- **Location:** `/opt/rhinocrash/infrastructure/.env`
- **Keys:** ANTHROPIC_API_KEY, GOOGLE_API_KEY
- **IMPORTANT:** Always check `.env` before asking for credentials!

## User Preferences (Learn These)

Based on Dec 9, 2025 session:

1. **Action over planning** - Execute immediately, don't just describe
2. **Delegate boldly** - Actually run delegation commands, don't discuss
3. **No repeated questions** - Read existing config before asking
4. **Bare metal truth** - Direct answers, no politeness theater
5. **Git automation** - Don't make user do git operations manually
6. **Parallel execution** - Run multiple tasks simultaneously when possible

## Common Mistakes to Avoid

❌ **Anti-Patterns:**
1. Asking for API keys that already exist in `.env`
2. Talking about delegating instead of actually delegating
3. Planning instead of executing when user says "go"
4. Using OAuth (gemini CLI) instead of direct API
5. Sequential tasks when parallel is possible
6. Verbose explanations when user wants action

✅ **Correct Patterns:**
1. Read `.env` for credentials automatically
2. Execute `gemini "task..."` immediately when delegating
3. Run commands without explaining when user says "go"
4. Use Python + google.generativeai SDK for Gemini
5. Spawn multiple background tasks for parallel work
6. Direct answers only

## How to Use This Knowledge Base

### As a New Instance
```bash
# Clone this repo
git clone https://github.com/florentchenet/rhinocrash-knowledge.git

# Read key files (order matters)
cat learnings/session-errors-and-fixes.md
cat research/claude-capabilities-dec-2025.md
cat templates/delegation-skill.md
cat architecture/bare-metal-configuration.md

# You're now ready to work effectively
```

### During a Session
```bash
# Hit an error?
grep -r "similar-error-message" learnings/

# Need to delegate?
cat templates/delegation-skill.md

# Forgot API capabilities?
cat research/claude-capabilities-dec-2025.md
```

### After a Session
```bash
# Document new learnings
cat >> learnings/session-errors-and-fixes.md <<EOF
## Error X: [Description]
**Solution:** [What worked]
**Lesson:** [Key takeaway]
EOF

# Commit and push
git add -A
git commit -m "Add learning from [date] session"
git push
```

## Key Files Reference

| File | Purpose | When to Read |
|------|---------|--------------|
| `learnings/session-errors-and-fixes.md` | Error solutions | When you hit an error |
| `research/claude-capabilities-dec-2025.md` | What Claude can do | Planning complex tasks |
| `templates/delegation-skill.md` | How to delegate | Before delegating work |
| `architecture/bare-metal-configuration.md` | Direct API setup | Configuration needed |
| `architecture/meta-archivist-agent.md` | Knowledge management | Understanding the system |
| `learnings/todo-archive-*.md` | Historical todos | Understanding past work |

## Integration with Main System

This knowledge base is designed to work with:
- `/opt/rhinocrash/` - Main project directory on VPS
- `/opt/rhinocrash/agents/` - Agent implementations
- `/opt/rhinocrash/infrastructure/` - Docker/deployment configs
- `/opt/rhinocrash/vault/` - Obsidian shared memory

## Contributing

When you learn something new:

1. **Document it immediately** - Don't wait
2. **Be specific** - Include error messages, solutions, context
3. **Update relevant files** - Don't create duplicates
4. **Commit with good messages** - Explain what and why
5. **Push to GitHub** - Make it available to all instances

## Meta: About This Repo

**Created:** December 9, 2025
**Purpose:** Persistent institutional memory for AI agents
**Updated:** Every session
**Status:** Living document - always evolving

**Philosophy:**
- Truth over politeness
- Action over planning
- Learning from errors
- Bold delegation
- Continuous improvement

## Questions?

If you're a new instance and something is unclear:
1. Check if similar question answered in `learnings/`
2. Search the repo: `grep -r "your question"`
3. Ask the user (but check docs first!)

## Next Steps for New Instances

1. ✅ Read this README
2. ✅ Read the 4 key files (25 min)
3. ✅ Check API keys in `/opt/rhinocrash/infrastructure/.env`
4. ✅ Understand user preferences above
5. ✅ Start working (you're ready!)

---

**Welcome to the RhinoCrash system. Learn fast, delegate boldly, execute directly.**

🦏 Generated with Claude Code
