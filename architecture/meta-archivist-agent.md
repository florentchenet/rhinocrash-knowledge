# Meta Archivist Agent - Design Spec

## Purpose
Autonomous agent responsible for knowledge management, git operations, credential management, data organization, and institutional memory.

## The Problem
Current issues observed:
1. **Git chaos** - Wrong branches (master vs main), manual commits, no automation
2. **Credential scatter** - API keys in .env, some hard-coded, repeated requests
3. **Knowledge loss** - Learnings not automatically captured and stored
4. **Disorganized storage** - Files in local vs server, no clear structure
5. **No search** - Can't find previous solutions to same problems
6. **Token waste** - Re-reading same files, no caching strategy

## Solution: Meta Archivist Agent

### Core Responsibilities

#### 1. Knowledge Management
**Auto-capture learnings:**
- Watch for errors in logs → Extract pattern → Document solution
- Detect repeated questions → Create FAQ entry
- Notice new capabilities → Update capability docs
- Track decisions → Create decision log

**Smart storage:**
```
/opt/rhinocrash/knowledge/
├── errors/          # Indexed by error type, searchable
├── solutions/       # Indexed by problem domain
├── decisions/       # ADRs (Architecture Decision Records)
├── capabilities/    # What each tool/model can do
├── patterns/        # Code patterns, workflows that work
└── meta/           # How the archivist works
```

#### 2. Git Operations
**Automatic git management:**
- Auto-detect correct default branch (master/main)
- Create meaningful commits from work context
- Push to remote automatically
- Tag important milestones
- Create branches for experimental work
- Merge when stable

**Smart commit messages:**
Instead of generic "Update files", generate:
```
feat(knowledge): Add session errors and Claude capabilities docs

- Documented 6 error patterns from Dec 9 setup session
- Comprehensive Claude Agent SDK research
- Extended thinking and tool search capabilities
- Ready for new agent instances

Affected areas: Open Interpreter, Docker, API integration
Impact: Future instances can learn from these errors
```

#### 3. Credential Management
**Unified secrets store:**
```
/opt/rhinocrash/.secrets/
├── api-keys.age        # age-encrypted
├── ssh-keys.age
├── tokens.age
└── credentials.age
```

**Auto-detection:**
- Before asking for API key → Search .env, .secrets, environment
- Cache credentials in memory during session
- Securely pass to subagents
- Never commit secrets to git (gitignore automation)

**Key features:**
- Age encryption (user has master key)
- Auto-rotation detection
- Usage logging (which agent used which key when)
- Revocation handling

#### 4. Data Organization & Search

**Semantic search:**
```python
# When Claude hits an error:
archivist.search_similar_errors(error_message)
# Returns: "Similar error solved on Dec 5: OAuth timeout → Solution: Use API key instead"
```

**Auto-organization:**
- Tag documents by topic, date, relevance
- Create indexes
- Generate TOCs
- Link related documents
- Archive old/obsolete info

**Vector embeddings:**
- All knowledge embedded
- Semantic search via MCP Memory Server
- Find relevant info even with different wording

#### 5. Token Optimization

**Caching strategy:**
- Hash file contents → Cache in Redis
- Detect unchanged files → Use cached analysis
- Share embeddings across sessions
- Compress knowledge for context

**Context management:**
- Track what's in context
- Suggest removing irrelevant info
- Load only needed knowledge
- Summarize lengthy docs

#### 6. Institutional Memory

**Session logs:**
Every session auto-documented:
```
sessions/2025-12-09-open-interpreter-setup/
├── session-log.md          # What happened
├── errors.md              # Errors encountered
├── solutions.md           # How we solved them
├── learnings.md           # Key takeaways
├── files-changed.json     # Git diff summary
└── delegation-log.md      # Tasks delegated to Gemini
```

**Cross-session learning:**
- "We tried X before, it failed because Y"
- "User prefers Z approach for W type of tasks"
- "This error always happens when..."

## Implementation

### Phase 1: Core Infrastructure
**Files:**
```
/opt/rhinocrash/agents/archivist/
├── agent.py                    # Main archivist agent
├── knowledge_manager.py        # Learning capture & storage
├── git_ops.py                  # Smart git operations
├── credential_vault.py         # Unified secrets
├── search_engine.py            # Semantic search
├── token_optimizer.py          # Context management
└── session_logger.py           # Auto-documentation
```

**Dependencies:**
- age (encryption)
- gitpython (git automation)
- chromadb or similar (vector store)
- MCP Memory Server
- Redis (caching)

### Phase 2: Automation Hooks

**Git hooks:**
```bash
# pre-commit
- Scan for secrets → Block if found
- Check commit message quality
- Update changelog

# post-commit
- Push to remote if on main branch
- Notify Telegram
- Update knowledge index

# post-merge
- Check for conflicts in knowledge base
- Re-generate indices
```

**Session hooks:**
```python
# On session start
- Load relevant knowledge
- Check for updates to tools
- Restore context from last session

# On error
- Capture error details
- Search for similar errors
- Suggest solution if found
- Document new error if novel

# On session end
- Summarize what was accomplished
- Extract learnings
- Commit changes
- Update indices
```

### Phase 3: Smart Search

**Search capabilities:**
```python
# By error message
archivist.search("AttributeError: module 'interpreter'")
→ Returns: "Solved in session Dec 9: API changed, use interpreter.model not interpreter.llm.model"

# By task
archivist.search("deploy to VPS with Docker")
→ Returns: "Standard workflow: docker-compose.yml → build → push → deploy. See template: ..."

# By capability
archivist.search("can Claude do computer use?")
→ Returns: "Yes, Claude Opus 4.5 has computer use. See capabilities doc..."

# By decision
archivist.search("why did we choose Open Interpreter?")
→ Returns: "Dec 9 decision: OAuth failed on Claude Code extension, Open Interpreter simpler..."
```

### Phase 4: Delegation Intelligence

**Learn delegation patterns:**
- Track what tasks work well for Gemini
- Which tasks better for Claude
- Success rate of delegated tasks
- Time savings from delegation

**Auto-suggest delegation:**
```python
# When user asks: "Research latest Kubernetes features"
archivist.suggest_delegation()
→ "This is a research task. Recommend delegating to Gemini.
   Similar tasks succeeded 8/10 times. Average completion: 3min."
```

## API Design

```python
from archivist import MetaArchivist

archivist = MetaArchivist()

# Knowledge operations
archivist.capture_learning(error, solution, context)
archivist.search_knowledge(query)
archivist.get_similar_errors(error_message)

# Git operations
archivist.smart_commit(message_context)
archivist.auto_push()
archivist.create_branch_for_experiment(description)

# Credentials
api_key = archivist.get_credential("ANTHROPIC_API_KEY")
archivist.store_credential("NEW_API", key, encrypted=True)

# Session management
archivist.start_session(topic="multi-agent setup")
archivist.log_delegation(task, agent="gemini")
archivist.end_session()  # Auto-generates summary

# Search
results = archivist.semantic_search("how to fix Docker permission errors")
archivist.index_document(path, tags=["docker", "errors"])
```

## Integration with Other Agents

### With Orchestrator
```python
# Orchestrator delegates to archivist:
orchestrator.delegate(
    agent="archivist",
    task="Find all previous Docker deployment errors and solutions"
)
```

### With Execution Agents
```python
# When Gemini hits an error:
solution = archivist.search_similar_errors(error)
if solution:
    gemini.apply_solution(solution)
else:
    # Novel error, solve and document
    gemini.solve()
    archivist.capture_learning(error, gemini.solution)
```

## Metrics & Monitoring

**Track:**
- Knowledge base growth (docs/week)
- Search usage (queries/day)
- Hit rate (found solution %)
- Time saved (avg time to find solution)
- Credential access patterns
- Git operations (commits/day, push failures)
- Session learnings captured

**Dashboard:**
```
Meta Archivist Status
├─ Knowledge Base: 127 documents, 2.3MB
├─ Search Index: 1,453 entries, 94% hit rate
├─ Credentials: 12 keys, 0 expired
├─ Git Health: Clean, 3 commits today, auto-pushed
├─ Sessions: 5 this week, 23 learnings captured
└─ Delegation: 8 tasks delegated, 87% success rate
```

## Security Considerations

**Encryption:**
- All credentials age-encrypted
- Master key on user's device only
- Agents get time-limited tokens, not master key

**Access control:**
- Read-only knowledge base for most agents
- Only archivist can write/index
- Audit log of all credential access

**Secrets detection:**
- Pre-commit hook scans for secrets
- Regex patterns for API keys, tokens
- Block commit if secrets found

## Future Enhancements

**AI-powered features:**
- Auto-generate documentation from code changes
- Predict which knowledge will be needed based on task
- Suggest refactoring based on patterns
- Detect duplicate/conflicting knowledge

**Cross-instance sync:**
- Share knowledge across multiple machines
- Federated search across team's knowledge bases
- Conflict resolution for concurrent edits

**Learning optimization:**
- A/B test different solution approaches
- Track which solutions work best
- Evolve documentation based on usage
- Deprecate obsolete knowledge

## Success Criteria

**The archivist is successful when:**
1. ✅ No API key asked twice in same session
2. ✅ Errors solved in <30s via search
3. ✅ Git operations never manual
4. ✅ New instances productive immediately (loaded knowledge)
5. ✅ Zero secrets in git history
6. ✅ 90%+ search hit rate
7. ✅ Session summaries auto-generated
8. ✅ Delegation success rate >80%

## Quick Start Implementation

**MVP (v0.1):**
1. Basic credential vault (age-encrypted .env)
2. Error search (simple grep)
3. Auto git commit/push
4. Session logging

**v0.2:**
- Semantic search (vector embeddings)
- Smart commit messages
- Knowledge indexing

**v0.3:**
- Delegation intelligence
- Cross-session learning
- Token optimization

**v1.0:**
- Full feature set
- Dashboard
- Multi-agent integration

---

**Created:** December 9, 2025
**Status:** Design spec - ready for implementation
**Priority:** HIGH - Solves multiple pain points observed in current session
