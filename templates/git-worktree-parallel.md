---
tags: [template, git-worktree, parallel-agentic-coding]
date: 2025-12-09
source: Agentic Engineering - Parallel pattern
usage: Test multiple solution approaches simultaneously
---

# Git Worktree Parallel Pattern

**Purpose**: Hedge engineering risk by exploring multiple solutions in parallel.

## Concept

Instead of sequential "try A, if fails try B", run multiple agents simultaneously with different approaches. Cherry-pick best solutions.

## When to Use

- Complex problems with multiple valid approaches
- High-stakes features where getting it right matters
- When you want to compare trade-offs empirically
- Exploring new territory without clear best practice

## Pattern Overview

```
main branch
    ↓
Feature X spec (1000+ token prompt)
    ↓
    ├─→ worktree-A (approach 1: Redux)
    ├─→ worktree-B (approach 2: Context API)
    └─→ worktree-C (approach 3: Zustand)
         ↓
    Run agents in parallel
         ↓
    Compare results
         ↓
    Cherry-pick best approach → main
```

## Step-by-Step Guide

### 1. Create Spec Prompt

Use `templates/spec-prompt-template.md` to create comprehensive specification.

**Critical**: Same spec for all approaches. Only approach differs.

```bash
# Create spec
cat > /tmp/feature-spec.md <<'EOF'
# State Management for Dashboard

## Context
[Full context here - 1000+ tokens]

## Objectives
[Clear objectives]

## Approach A: Redux
[Details for Redux approach]

## Approach B: Context API
[Details for Context approach]

## Approach C: Zustand
[Details for Zustand approach]
EOF
```

### 2. Create Worktrees

```bash
# From your main repo
cd ~/Documents/Dev/my-project

# Create base branch
git checkout -b feature/dashboard-state

# Create worktrees for parallel exploration
git worktree add ../my-project-worktree-A feature/dashboard-state
git worktree add ../my-project-worktree-B feature/dashboard-state
git worktree add ../my-project-worktree-C feature/dashboard-state

# Verify
git worktree list
# main                  /Users/hoe/Documents/Dev/my-project
# feature/dashboard     /Users/hoe/Documents/Dev/my-project-worktree-A
# feature/dashboard     /Users/hoe/Documents/Dev/my-project-worktree-B
# feature/dashboard     /Users/hoe/Documents/Dev/my-project-worktree-C
```

### 3. Launch Parallel Agents

**Option A: Manual (3 terminal windows)**

```bash
# Terminal 1
cd ~/Documents/Dev/my-project-worktree-A
claude

# Terminal 2
cd ~/Documents/Dev/my-project-worktree-B
claude

# Terminal 3
cd ~/Documents/Dev/my-project-worktree-C
claude
```

Give each agent the same spec, specify approach:
- Agent A: "Implement using Redux approach from spec"
- Agent B: "Implement using Context API approach from spec"
- Agent C: "Implement using Zustand approach from spec"

**Option B: Automated (using screen/tmux)**

```bash
#!/bin/bash
# parallel-agents.sh

SPEC=$(cat /tmp/feature-spec.md)

screen -dmS agent-A bash -c "cd ~/Documents/Dev/my-project-worktree-A && claude -p 'Implement using Redux: $SPEC'"
screen -dmS agent-B bash -c "cd ~/Documents/Dev/my-project-worktree-B && claude -p 'Implement using Context: $SPEC'"
screen -dmS agent-C bash -c "cd ~/Documents/Dev/my-project-worktree-C && claude -p 'Implement using Zustand: $SPEC'"

echo "Agents running in screen sessions: agent-A, agent-B, agent-C"
echo "Attach with: screen -r agent-A"
```

### 4. Monitor Progress

```bash
# Check each worktree
cd ~/Documents/Dev/my-project-worktree-A && git log --oneline -5
cd ~/Documents/Dev/my-project-worktree-B && git log --oneline -5
cd ~/Documents/Dev/my-project-worktree-C && git log --oneline -5

# Compare file changes
diff -r my-project-worktree-A/src my-project-worktree-B/src
```

### 5. Evaluate Results

Create evaluation matrix:

```markdown
| Criteria | Redux (A) | Context (B) | Zustand (C) |
|----------|-----------|-------------|-------------|
| Tests pass | ✅ | ✅ | ❌ |
| Bundle size | 45KB | 12KB | 8KB |
| Code complexity | High | Medium | Low |
| TypeScript support | Excellent | Good | Excellent |
| DevTools | ✅ | ❌ | ✅ |
| Learning curve | Steep | Gentle | Gentle |
| Performance | Excellent | Good | Excellent |
| **WINNER** | | | ✅ |
```

### 6. Cherry-Pick Winner

```bash
# Go back to main working directory
cd ~/Documents/Dev/my-project

# Merge winner's work
git merge feature/dashboard-state --no-ff

# Or cherry-pick specific commits
git cherry-pick <commit-hash-from-worktree-C>

# Or manually copy best parts
cp -r ../my-project-worktree-C/src/store ./src/
```

### 7. Cleanup Worktrees

```bash
# Remove worktrees
git worktree remove ../my-project-worktree-A
git worktree remove ../my-project-worktree-B
git worktree remove ../my-project-worktree-C

# Delete feature branch if not needed
git branch -d feature/dashboard-state
```

---

## Example: API Client Implementation

### Spec (Same for all)

```markdown
# API Client with Error Handling

## Objective
Robust HTTP client with retry, caching, error handling

## Requirements
- TypeScript
- 100% test coverage
- < 10KB bundle size
- Works in Node & browser
```

### Approaches

**Worktree A**: axios + axios-retry + axios-cache-adapter
**Worktree B**: fetch + custom retry + in-memory cache
**Worktree C**: ky (fetch wrapper) + custom cache

### Results

```
A: Works great, but 45KB bundle (axios is heavy)
B: Minimal bundle, but complex retry logic
C: Best balance - 8KB, clean code, good DX

Winner: C (Ky approach)
```

---

## Cost Analysis

### Without Parallel Pattern
- Try Redux → 2 hours
- Doesn't meet performance goals
- Try Context → 2 hours
- Still not ideal
- Try Zustand → 2 hours
- **Total: 6 hours sequential**

### With Parallel Pattern
- All 3 approaches simultaneously → 2 hours
- Immediate comparison
- **Total: 2 hours parallel + pick winner**

**Savings**: 4 hours + confidence in decision

---

## Advanced: Hybrid Approaches

Sometimes best solution combines elements:

```bash
# Cherry-pick specific commits from each
git cherry-pick worktree-A:<store-structure-commit>
git cherry-pick worktree-B:<hooks-commit>
git cherry-pick worktree-C:<middleware-commit>

# Result: Hybrid approach with best of each
```

---

## Tools for Comparison

### Code Metrics
```bash
# Lines of code
cloc my-project-worktree-A/src
cloc my-project-worktree-B/src
cloc my-project-worktree-C/src

# Bundle size
cd my-project-worktree-A && npm run build && du -sh dist/
cd my-project-worktree-B && npm run build && du -sh dist/
cd my-project-worktree-C && npm run build && du -sh dist/

# Test coverage
npm test -- --coverage
```

### Performance Benchmarks
```bash
# Run performance tests in each worktree
cd my-project-worktree-A && npm run perf
cd my-project-worktree-B && npm run perf
cd my-project-worktree-C && npm run perf
```

---

## Guardrails

### Do's
✅ Same detailed spec for all approaches
✅ Clear success criteria upfront
✅ Realistic time budget (don't wait forever)
✅ Automated tests in spec
✅ Measure objectively

### Don'ts
❌ Vague specs (leads to incomparable results)
❌ Moving goalposts mid-experiment
❌ Letting personal bias override data
❌ Parallelizing too many approaches (3 max)
❌ Forgetting to clean up worktrees

---

## Economic Model

**Cost**: 3x compute (running 3 agents)
**Benefit**:
- 3x confidence (empirical comparison)
- 50% time savings (vs sequential)
- Risk reduction (wrong choice expensive later)

**ROI**: High for important decisions, overkill for trivial tasks

---

## Integration with Other Patterns

### With R&D Framework
- **Reduce**: Each worktree has .claudeignore
- **Delegate**: Different agents per worktree

### With TDD
Each approach must have same passing tests:
```bash
# Test suite is the contract
# All worktrees must pass identical tests
npm test
```

### With Spec Prompts
Parallel pattern requires excellent spec:
- Spec defines success criteria
- Approaches are variations on implementation
- Objective comparison possible

---

## Real-World Use Cases

1. **Database choice**: Postgres vs MongoDB vs Redis for caching layer
2. **Framework migration**: React → Vue vs React → Svelte vs Stay React
3. **Algorithm optimization**: Approach A vs B vs C for performance
4. **UI component library**: Material-UI vs Chakra vs Tailwind+Headless
5. **State management**: (as shown above)

---

**Remember**: Parallel agentic coding transforms compute cost into engineering confidence. Use for high-stakes decisions, skip for obvious choices.
