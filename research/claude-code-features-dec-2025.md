---
tags: [research, claude-code, features, documentation]
date: 2025-12-09
source: Official Anthropic Claude Code Documentation
relevance: critical
---

# Claude Code Features - Official Documentation (December 2025)

## What is Claude Code

Claude Code is a **command line tool for agentic coding** developed by Anthropic as a research project. It gives engineers and researchers a native way to integrate Claude into their coding workflows through a CLI interface.

## Core Features

### 1. Hooks

**What they are:**
User-defined shell commands or scripts that execute automatically at predetermined points in Claude Code's workflow.

**Hook Types:**
- **PreToolUse** - Runs before tool calls, can block them and provide Claude feedback
- **Pre-Command Execution** - Environment setup or validation
- **Post-Command Execution** - Formatting or logging
- **Error Handling** - Custom recovery procedures

**How to use:**
```bash
/hooks  # Opens hooks configuration
# Select PreToolUse event to configure
```

**Use cases:**
- Block dangerous commands
- Validate environment before execution
- Format output automatically
- Custom error recovery
- Log all tool usage

### 2. Slash Commands

**What they are:**
Prompt templates stored as Markdown files in `.claude/commands/` folder that become available through the slash commands menu.

**How to create:**
```bash
# Create command file
cat > .claude/commands/my-command.md <<'EOF'
# My Custom Command

Execute this specific workflow...
$ARGUMENTS
EOF

# Use it
/my-command arg1 arg2
```

**Special features:**
- `$ARGUMENTS` keyword - Pass parameters from command invocation
- Auto-discovered from `.claude/commands/` folder
- Support Markdown formatting
- Can include complex prompts

**Built-in commands:**
- `/hooks` - Configure hooks
- `/help` - Get help
- (Plus custom commands you create)

### 3. MCP Servers (Model Context Protocol)

**What MCP does:**
Connects Claude to external data sources and tools. MCP provides connectivity, while Skills (below) provide procedural knowledge.

**Key MCP Commands:**
```bash
# Add MCP server
claude mcp add [name] --scope user

# List all MCP servers
claude mcp list

# Remove MCP server
claude mcp remove [name]

# Test MCP server
claude mcp get [name]
```

**Configuration Scopes:**
1. **Project** - `.mcp.json` in project root (preferred)
2. **Local** - `~/.config/claude/mcp.json`
3. **User** - `~/.claude/mcp.json`

**Configuration Locations:**
- Project-scoped: `.mcp.json` in project directory
- Project settings: `.claude/settings.local.json`
- User settings: `~/.claude/settings.local.json`

**Environment Variables:**
- `MCP_TIMEOUT` - Configure MCP server startup timeout

**Popular MCP Servers:**
- Filesystem access
- Git operations
- Database connections
- API integrations
- Custom tools

### 4. Skills

**What Skills do:**
Teach Claude what to do with data. While MCP connects Claude to data, Skills provide procedural knowledge.

**Difference: Skills vs MCP:**
- **MCP:** "Connect to this database"
- **Skills:** "When querying the database, always filter by date range first"

**Using together:**
```
MCP (connectivity) + Skills (procedures) = Powerful workflows
```

**Example:**
```markdown
# Skill: Database Query Best Practices

When querying our production database:
1. Always filter by date range
2. Use read replicas
3. Limit results to 1000
4. Log all queries
```

### 5. Plugins

**What they are:**
Custom collections of slash commands, agents, MCP servers, and hooks that install with a single command.

**How to install:**
```bash
claude plugin marketplace add <plugin-name>
```

**Plugin Components:**
- Slash commands
- Agents
- MCP servers
- Hooks
- Skills

**Benefit:**
Bundle related functionality together for easy distribution and installation.

### 6. Settings & Configuration

**Configuration Files:**
- `~/.claude/settings.json` - Global settings
- `.claude/settings.local.json` - Project-specific settings
- `.mcp.json` - MCP server configuration

**Important Settings:**
- API keys (ANTHROPIC_API_KEY)
- MCP server configurations
- Hook definitions
- Custom slash commands

## Best Practices

### From Official Documentation

1. **Use hooks for validation** - Prevent dangerous operations
2. **Organize slash commands** - Keep `.claude/commands/` tidy
3. **Prefer project-scoped MCP** - Better for team collaboration
4. **Combine MCP + Skills** - MCP for data, Skills for procedures
5. **Use plugins** - Bundle related functionality
6. **Test immediately** - Verify hooks and MCP servers work

### Workflow Optimization

```bash
# Setup project
claude mcp add filesystem --scope project
claude mcp add git --scope project

# Create custom command
cat > .claude/commands/deploy.md <<'EOF'
Deploy to production with safety checks:
1. Run tests
2. Check branch is main
3. Deploy
EOF

# Configure hook
/hooks
# Select PreToolUse → Add validation logic
```

## Advanced Features

### Hook Blocking
PreToolUse hooks can **block tool execution** and provide feedback to Claude about what to do differently.

**Example use case:**
```bash
# Block git push to main without PR
if [[ $TOOL == "git push" ]] && [[ $BRANCH == "main" ]]; then
    echo "BLOCKED: Create PR instead of direct push to main"
    exit 1
fi
```

### Parameterized Slash Commands
```markdown
# .claude/commands/create-component.md

Create React component named $ARGUMENTS with:
- TypeScript
- Tests
- Storybook story
```

**Usage:**
```bash
/create-component Button
# Expands with "Button" replacing $ARGUMENTS
```

### MCP Server Debugging
```bash
# Test MCP server connectivity
claude mcp get [name]

# Check MCP logs
# (Location varies by platform)
```

## Integration Examples

### Example 1: Git Workflow with Hooks
```bash
# PreToolUse hook
# Prevent force push
if [[ $COMMAND == *"--force"* ]]; then
    echo "BLOCKED: Force push not allowed"
    exit 1
fi
```

### Example 2: Database Access via MCP + Skill
```json
// .mcp.json
{
  "mcpServers": {
    "database": {
      "command": "mcp-server-postgres",
      "args": ["--connection", "prod-db"],
      "env": {
        "DB_PASSWORD": "${DB_PASSWORD}"
      }
    }
  }
}
```

```markdown
<!-- .claude/skills/database-queries.md -->
# Database Query Skill

When querying production:
1. Always use read replica
2. Filter by date (last 30 days max)
3. LIMIT 1000
4. Log query to audit log
```

### Example 3: Custom Deployment Command
```markdown
<!-- .claude/commands/deploy.md -->
# Deploy to Production

Execute deployment with these steps:
1. Verify on correct branch (main)
2. Run full test suite
3. Build production assets
4. Run database migrations
5. Deploy to staging first
6. Smoke test staging
7. Deploy to production
8. Verify production health

Use $ARGUMENTS for deployment target.
```

## Official Resources

- **Main Documentation:** https://docs.claude.com/en/docs/claude-code/
- **Hooks Guide:** https://docs.claude.com/en/docs/claude-code/hooks-guide
- **Slash Commands:** https://docs.anthropic.com/en/docs/claude-code/slash-commands
- **MCP Documentation:** https://docs.anthropic.com/en/docs/claude-code/mcp
- **Best Practices:** https://www.anthropic.com/engineering/claude-code-best-practices
- **Plugins:** https://www.anthropic.com/news/claude-code-plugins
- **Skills Explained:** https://www.claude.com/blog/skills-explained

## Community Resources

- **Awesome Claude Code:** https://github.com/hesreallyhim/awesome-claude-code
- **Complete Guide:** https://www.siddharthbharath.com/claude-code-the-complete-guide/
- **Cheat Sheet:** https://shipyard.build/blog/claude-code-cheat-sheet/
- **Claude Log:** https://claudelog.com/

## Quick Reference

### Essential Commands
```bash
# MCP Management
claude mcp list
claude mcp add [name] --scope [user|project]
claude mcp remove [name]

# Hooks
/hooks

# Help
/help

# Custom slash commands
/[command-name] [args]
```

### File Structure
```
project/
├── .claude/
│   ├── commands/          # Slash commands
│   │   └── *.md
│   ├── settings.local.json # Project settings
│   └── hooks/             # Hook scripts
├── .mcp.json             # MCP configuration
└── skills/               # Skills (if using)
    └── *.md
```

### Configuration Priority
1. Project settings (`.claude/settings.local.json`)
2. User settings (`~/.claude/settings.local.json`)
3. Global settings (`~/.claude/settings.json`)

---

**Researched:** December 9, 2025
**Source:** Official Anthropic Claude Code Documentation
**Purpose:** Complete reference for Claude Code CLI features
**Status:** Current as of Dec 2025

**Key Takeaway:** Claude Code = Hooks + Slash Commands + MCP + Skills + Plugins for agentic coding workflows
