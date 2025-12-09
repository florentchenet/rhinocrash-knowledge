# Archive & Search Skill - Knowledge Management

## Core Principle

**Knowledge is only useful if it's findable. Document immediately, search before solving, archive permanently.**

## The Problem

Without systematic archiving and search:
- Solve the same problem multiple times
- Forget lessons learned
- Can't find past solutions
- Waste time re-researching
- New instances start from zero

## The Solution: Structured Knowledge Base

### Archive Structure

```
knowledge/
├── learnings/          # Session learnings, errors, solutions
│   ├── session-errors-and-fixes.md
│   ├── todo-archive-YYYY-MM-DD.md
│   └── user-preferences.md
│
├── research/           # Technical research, capabilities
│   ├── claude-capabilities-dec-2025.md
│   ├── youtube-claude-insights-dec2025.md
│   └── api-comparisons.md
│
├── architecture/       # System designs, specs
│   ├── meta-archivist-agent.md
│   ├── bare-metal-configuration.md
│   └── multi-agent-orchestration.md
│
├── templates/          # Reusable patterns, skills
│   ├── delegation-skill.md
│   ├── efficiency-skill.md
│   └── archive-search-skill.md  (this file)
│
├── errors/             # Indexed by error type
│   ├── oauth-failures.md
│   ├── docker-errors.md
│   └── api-errors.md
│
├── solutions/          # Indexed by problem domain
│   ├── authentication.md
│   ├── deployment.md
│   └── performance.md
│
└── brainstorm-log.md   # Real-time thinking/decisions
```

### Tagging System

Every document should have frontmatter tags:

```markdown
---
tags: [error, oauth, authentication, solved]
date: 2025-12-09
category: learnings
relevance: high
---

# Document Title
...
```

**Tag categories:**
- **Type:** error, solution, capability, pattern, decision
- **Domain:** auth, docker, git, api, deployment
- **Status:** solved, unsolved, in-progress, deprecated
- **Relevance:** high, medium, low

## Search Strategies

### 1. Grep Search (Fast, Local)

**By error message:**
```bash
cd ~/Documents/Documents\ -\ Mac/Dev/rhinocrash-knowledge
grep -r "AttributeError" learnings/ errors/
```

**By solution:**
```bash
grep -r "OAuth" solutions/ learnings/
```

**By capability:**
```bash
grep -r "extended thinking" research/
```

**By tag:**
```bash
grep -r "tags:.*docker" .
```

### 2. Semantic Search (Future: Vector Embeddings)

```python
# When implemented:
from knowledge_search import SemanticSearch

search = SemanticSearch("~/Documents/Documents - Mac/Dev/rhinocrash-knowledge")
results = search.find_similar("OAuth timeout error")

# Returns:
# 1. learnings/session-errors-and-fixes.md (95% similar)
# 2. errors/oauth-failures.md (87% similar)
# 3. solutions/authentication.md (72% similar)
```

### 3. Full-Text Search (find command)

```bash
# Find files modified in last 7 days
find knowledge/ -name "*.md" -mtime -7

# Find files containing "gemini"
find knowledge/ -name "*.md" -exec grep -l "gemini" {} \;
```

## Archiving Workflow

### When to Archive

Archive IMMEDIATELY when you:
1. Solve an error
2. Learn something new
3. Make a decision
4. Complete a task
5. Receive user feedback

**Don't wait. Don't batch. Archive NOW.**

### What to Archive

**Errors & Solutions:**
```markdown
## Error: [Short description]

**Context:** When did this happen, what was I doing?
**Error Message:** Exact error text
**Root Cause:** Why did this happen?
**Solution:** What fixed it
**Lesson:** Key takeaway
**Related:** Links to similar issues

**Tags:** `error`, `[domain]`, `solved`
```

**Capabilities & Research:**
```markdown
## [Feature/Capability Name]

**What it is:** Brief description
**How it works:** Technical details
**Use cases:** When to use it
**Cost:** If applicable
**Examples:** Code samples
**References:** Links to docs

**Tags:** `capability`, `[technology]`, `[relevance]`
```

**Decisions:**
```markdown
## Decision: [What was decided]

**Date:** YYYY-MM-DD
**Context:** Why was this decision needed?
**Options Considered:**
1. Option A - Pros/Cons
2. Option B - Pros/Cons

**Decision:** What we chose and why
**Impact:** What this affects
**Reversible:** Yes/No

**Tags:** `decision`, `[domain]`, `[status]`
```

### How to Archive

#### Option 1: Append to Existing File
```bash
cat >> knowledge/learnings/session-errors-and-fixes.md <<'EOF'

## Error 7: [New error]
**Solution:** [What worked]
**Lesson:** [Takeaway]
EOF
```

#### Option 2: Create New File
```bash
cat > knowledge/errors/gemini-api-error.md <<'EOF'
---
tags: [error, gemini, api, solved]
date: 2025-12-09
---

## Gemini API Invalid Key Error
...
EOF
```

#### Option 3: Update Brainstorm Log
```bash
# For real-time decisions/thinking
vim knowledge/brainstorm-log.md
# Add entry with timestamp
```

## Search Before Solving

**ALWAYS search before solving a problem:**

```bash
# Hit an error? Search first!
function solve_error() {
    local error_msg="$1"

    # Search knowledge base
    results=$(grep -r "$error_msg" ~/Documents/Documents\ -\ Mac/Dev/rhinocrash-knowledge/)

    if [[ -n "$results" ]]; then
        echo "Found similar error previously solved:"
        echo "$results"
        echo "Try that solution first"
    else
        echo "Novel error. Solve and document."
    fi
}
```

**Process:**
1. Encounter problem
2. Search knowledge base
3. If found → Apply known solution
4. If not found → Solve → Archive solution
5. Update knowledge base

## Indexing & Organization

### Auto-Indexing (Future Feature)

```python
# Meta archivist will automatically:
# 1. Index all documents on commit
# 2. Generate table of contents
# 3. Create cross-references
# 4. Update search index
# 5. Tag documents automatically
```

### Manual Indexing (Current)

Update index files manually:

```markdown
# knowledge/INDEX.md

## Errors
- [OAuth Failures](errors/oauth-failures.md)
- [Docker Errors](errors/docker-errors.md)

## Solutions
- [Authentication](solutions/authentication.md)
- [Deployment](solutions/deployment.md)

## Last Updated
2025-12-09
```

## Git Integration

**Every archive operation should be committed:**

```bash
# After archiving new knowledge
git add knowledge/
git commit -m "docs: Add [error/solution/capability]

- Document [what was learned]
- Solve [what problem]
- Impact: [who benefits]

🤖 Generated with Claude Code"

git push origin master
```

## Knowledge Maintenance

### Weekly Cleanup
1. Review old documents
2. Mark deprecated knowledge
3. Consolidate duplicates
4. Update references
5. Re-tag if needed

### Monthly Archive
1. Move old todos to archive/
2. Summarize month's learnings
3. Create "best of" compilation
4. Update INDEX.md

### Version Control
- Use git tags for major milestones
- Branch for experimental knowledge
- Main branch = stable, proven knowledge

## Search Patterns Cheat Sheet

```bash
# By error type
grep -r "Error:" knowledge/learnings/

# By solution domain
grep -r "tags:.*authentication" knowledge/

# By date
find knowledge/ -name "*.md" -newer knowledge/learnings/session-errors-and-fixes.md

# By author (if tracked)
grep -r "Claude Sonnet 4" knowledge/

# By relevance
grep -r "relevance: high" knowledge/

# Full-text search
ag "extended thinking" knowledge/  # If ag (silver searcher) installed

# Case-insensitive
grep -ri "oauth" knowledge/
```

## Integration With Other Tools

### With Archivist Agent
```python
# Archivist automatically:
archivist.capture_error(error, solution)
archivist.index_document(path, tags)
archivist.search_similar(query)
```

### With Delegation
```bash
# Delegate archiving to Gemini:
~/scripts/venv/bin/python ~/scripts/gemini-api.py \
    "Read this error log and create an archive document: ..."
```

### With Efficiency Skill
```
Before solving → Search knowledge base
After solving → Archive solution
Weekly review → Update efficiency metrics
```

## Examples

### Example 1: Error Search
```bash
# Hit OAuth error
grep -r "OAuth" knowledge/

# Found: learnings/session-errors-and-fixes.md
# Solution: "Use API key instead of OAuth"
# Apply solution → Success!
# Time saved: 30 minutes
```

### Example 2: Capability Lookup
```bash
# User asks: "Can Claude do extended thinking?"
grep -r "extended thinking" knowledge/research/

# Found: claude-capabilities-dec-2025.md
# Answer immediately with accurate info
# No web search needed
```

### Example 3: Decision Reference
```bash
# User asks: "Why did we choose Open Interpreter?"
grep -r "Open Interpreter" knowledge/learnings/

# Found: Decision documented in session-errors-and-fixes.md
# Context: OAuth failed, Open Interpreter simpler
# Provide informed answer
```

## Metrics

Track search effectiveness:
- **Hit rate:** % of searches that find answer (Goal: >80%)
- **Time to find:** Average seconds to find solution (Goal: <30s)
- **Reuse rate:** % of solutions reused (Goal: >50%)
- **Archive latency:** Time from learning to archiving (Goal: <5min)

## Best Practices

1. **Archive immediately** - Don't wait, don't batch
2. **Use consistent structure** - Follow templates
3. **Tag everything** - Make it searchable
4. **Link related docs** - Create knowledge graph
5. **Git commit often** - Never lose knowledge
6. **Search before solving** - Don't repeat work
7. **Update brainstorm log** - Real-time thinking
8. **Review weekly** - Keep knowledge fresh

## Anti-Patterns

❌ **Don't:**
- Archive "later" (you'll forget)
- Create duplicate documents
- Use vague titles ("notes.md")
- Skip tagging
- Forget to commit
- Solve without searching first
- Let knowledge base go stale

✅ **Do:**
- Archive NOW
- Check for existing docs first
- Use descriptive titles
- Tag comprehensively
- Commit after every archive
- Search knowledge base first
- Review and update regularly

---

**Created:** December 9, 2025
**Purpose:** Prevent knowledge loss, enable fast search, build institutional memory
**Key Lesson:** Knowledge archived immediately is 10x more valuable than knowledge "documented later"
