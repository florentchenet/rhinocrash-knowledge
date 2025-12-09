---
tags: [architecture, memory-sharing, configuration, mcp]
date: 2025-12-09
relevance: critical
---

# Sharing Memory & Prompts Between Claude Instances

## The Challenge

Every new Claude instance starts with zero context. We need ways to:
1. Share knowledge across instances
2. Transfer prompts and workflows
3. Maintain consistent configuration
4. Enable local and online collaboration

## Solution Architecture

### 1. GitHub Knowledge Repository (Implemented ✅)

**What:** Central knowledge base accessible to all instances
**Location:** https://github.com/florentchenet/rhinocrash-knowledge

**How to use:**
```bash
# New instance onboarding (25 minutes)
git clone https://github.com/florentchenet/rhinocrash-knowledge.git
cd rhinocrash-knowledge

# Read key files
cat learnings/session-errors-and-fixes.md
cat research/claude-capabilities-dec-2025.md
cat templates/delegation-skill.md
cat templates/efficiency-skill.md

# You're now caught up with all previous learnings
```

**What it contains:**
- All errors and solutions
- Research findings
- Skills and workflows
- Architecture decisions
- Session logs
- Brainstorm notes

**Update frequency:**
Every session, commit new learnings immediately

### 2. MCP Memory Server (Already Connected ✅)

**What:** Persistent memory across sessions using Model Context Protocol
**Status:** Already configured at ~/.claude/

**How it works:**
```bash
# Check connection
claude mcp list
# Shows: memory: npx -y @modelcontextprotocol/server-memory - ✓ Connected
```

**Usage:**
MCP memory automatically stores:
- Important facts
- Userpreferences
- Project context
- Decisions made

**Access from any instance:**
Memory is stored centrally and accessible via MCP protocol

### 3. Shared Slash Commands

**Location:** `~/.claude/commands/` (local) or project `.claude/commands/` (shared)

**How to share:**

#### Option A: Symlinks (Current Setup)
```bash
# Link to central skills repository
ln -s /path/to/rhinocrash/skills/delegation ~/.claude/commands/delegate
```

#### Option B: Git-Tracked Commands (Recommended)
```bash
# In project directory
mkdir -p .claude/commands
cat > .claude/commands/search-knowledge.md <<'EOF'
# Search Knowledge Base

Search the rhinocrash-knowledge repository for:
$ARGUMENTS

Look in:
- learnings/ for errors and solutions
- research/ for capabilities
- architecture/ for designs
- templates/ for skills
EOF

# Commit to git
git add .claude/commands/
git commit -m "Add knowledge search command"
```

**Any instance that clones the repo gets the commands automatically**

#### Option C: Package as Plugin
```bash
# Create plugin with shared commands
# Others can install with:
claude plugin marketplace add your-plugin-name
```

### 4. Shared Settings & Configuration

**Files to share:**
- `settings.json` - Global preferences
- `.mcp.json` - MCP server configuration
- Slash commands
- Skills

**Method 1: Git Repository**
```bash
# Create dotfiles repo
mkdir ~/claude-dotfiles
cd ~/claude-dotfiles

# Copy configs
cp ~/.claude/settings.json ./settings.json
cp ~/.mcp.json ./mcp.json
cp -r ~/.claude/commands/ ./commands/

# Commit
git init
git add .
git commit -m "Claude configuration"
git push origin main

# On new machine/instance
git clone <dotfiles-repo>
cp settings.json ~/.claude/
cp mcp.json ~/.
cp -r commands/* ~/.claude/commands/
```

**Method 2: Project-Scoped Config**
```bash
# In project directory
cat > .mcp.json <<'EOF'
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/workspace"]
    }
  }
}
EOF

# Commit
git add .mcp.json
git commit -m "Add MCP configuration"
```

**Anyone who clones the project gets the MCP servers**

### 5. Skills Sharing

**Via GitHub:**
```bash
# In rhinocrash-knowledge repo
cat > skills/efficiency.md <<'EOF'
---
name: efficiency
description: Task prioritization and big picture thinking
---

[Skill content from templates/efficiency-skill.md]
EOF

# Other instances import
claude skill install https://github.com/florentchenet/rhinocrash-knowledge/skills/efficiency.md
```

**Via Plugin:**
```bash
# Package skills as plugin
claude plugin create rhinocrash-skills
# Add skills
# Publish
# Others install
claude plugin marketplace add rhinocrash-skills
```

### 6. Session Context Export/Import

**Export session context:**
```bash
# Save current session
cat > session-context-$(date +%Y-%m-%d).md <<EOF
# Session Context $(date)

## Active Project
$(pwd)

## Recent Files
$(git diff --name-only HEAD~5)

## Current Focus
[Manual description]

## Key Decisions
[Document decisions made]
EOF

git add session-context-*.md
git commit -m "Save session context"
```

**Import on new instance:**
```bash
# Read session context
cat session-context-2025-12-09.md
# You now know what previous instance was working on
```

### 7. Automated Sync with n8n

**Workflow:**
```
Every commit to rhinocrash-knowledge
  ↓
n8n webhook triggered
  ↓
Notify all instances (Telegram/Slack)
  ↓
Instances pull latest knowledge
  ↓
Knowledge synchronized
```

**n8n Workflow:**
```json
{
  "name": "Knowledge Sync",
  "nodes": [
    {
      "type": "Webhook",
      "url": "github.com webhooks"
    },
    {
      "type": "Telegram",
      "message": "New knowledge committed: {{commit.message}}"
    }
  ]
}
```

## Implementation Checklist

### For Knowledge Sharing
- [x] Create GitHub repository
- [x] Document all learnings
- [x] Commit frequently
- [ ] Setup git hooks for auto-commit
- [ ] Configure n8n for notifications

### For Prompt Sharing
- [ ] Create shared slash commands in .claude/commands/
- [ ] Package as plugin for easy distribution
- [ ] Document all prompts in knowledge base
- [ ] Export effective prompts to templates/

### For Configuration Sharing
- [ ] Create dotfiles repository
- [ ] Add .mcp.json to all projects
- [ ] Document configuration in knowledge base
- [ ] Script for easy setup on new machines

### For Memory Sharing
- [x] MCP memory server connected
- [ ] Add more MCP servers (filesystem, git)
- [ ] Document memory structure
- [ ] Create memory backup/restore scripts

## Quick Start: New Instance Onboarding

```bash
#!/bin/bash
# onboard-new-instance.sh

# 1. Clone knowledge base
git clone https://github.com/florentchenet/rhinocrash-knowledge.git ~/knowledge
cd ~/knowledge

# 2. Read essential files (25 min)
echo "Reading essential knowledge..."
cat README.md
cat learnings/session-errors-and-fixes.md
cat research/claude-capabilities-dec-2025.md
cat templates/efficiency-skill.md
cat templates/delegation-skill.md
cat templates/archive-search-skill.md

# 3. Install shared configuration
echo "Installing shared configuration..."
cp configs/settings.json ~/.claude/
cp configs/mcp.json ~/.

# 4. Link slash commands
echo "Linking slash commands..."
ln -s ~/knowledge/commands/* ~/.claude/commands/

# 5. Connect to MCP servers
echo "Connecting MCP servers..."
claude mcp add memory
claude mcp add filesystem --scope user

# 6. Verify setup
echo "Verifying setup..."
claude mcp list

echo "✅ Onboarding complete! You now have access to all shared knowledge."
```

## Usage Examples

### Example 1: Share Error Solution
```bash
# Instance A encounters error, solves it
echo "## Error: OAuth Timeout
Solution: Use direct API instead
" >> ~/knowledge/learnings/session-errors-and-fixes.md

git add learnings/session-errors-and-fixes.md
git commit -m "Add OAuth timeout solution"
git push

# Instance B (later) encounters same error
cd ~/knowledge
git pull
grep "OAuth" learnings/session-errors-and-fixes.md
# Finds solution immediately, saves 30 minutes
```

### Example 2: Share Workflow
```bash
# Instance A creates useful workflow
cat > ~/.claude/commands/deploy.md <<'EOF'
# Deploy Workflow
1. Run tests
2. Build
3. Deploy to staging
4. Smoke test
5. Deploy to production
EOF

git add .claude/commands/deploy.md
git commit -m "Add deploy workflow"
git push

# Instance B gets it automatically
git pull
/deploy  # Command now available
```

### Example 3: Share Configuration
```bash
# Instance A configures MCP servers perfectly
cat > .mcp.json <<'EOF'
{
  "mcpServers": {
    "memory": {...},
    "filesystem": {...},
    "git": {...}
  }
}
EOF

git add .mcp.json
git commit -m "Add MCP configuration"
git push

# Instance B clones repo
git clone <repo>
# Gets MCP configuration automatically
claude mcp list  # All servers connected
```

## Advanced: Real-Time Collaboration

**Use MCP Memory for Live Sharing:**
```
Instance A writes to memory: "Working on feature X, approach Y"
Instance B reads from memory: Sees Instance A's progress
Instance B writes: "I'll handle feature Z while you do X"
Parallel work without conflicts
```

**Shared Project Directory:**
```bash
# Use cloud-synced directory
cd ~/Dropbox/claude-projects/rhinocrash
# Multiple instances can work on same project
# Changes sync in real-time
```

## Summary

**Local Sharing:**
1. Git repository for knowledge
2. Symlinked slash commands
3. MCP memory server
4. Shared .mcp.json per project

**Online Sharing:**
1. GitHub for knowledge base
2. Plugin marketplace for commands
3. Cloud storage for real-time sync
4. n8n for notifications

**Best Practice:**
- Commit knowledge immediately after learning
- Use project-scoped .mcp.json for team sharing
- Package reusable workflows as plugins
- Document everything in knowledge base

---

**Created:** December 9, 2025
**Purpose:** Enable seamless knowledge transfer between Claude instances
**Key Insight:** Knowledge shared = Knowledge multiplied
