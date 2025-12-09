# Efficiency Skill - Task Prioritization & Big Picture Thinking

## Core Principle

**See the system, not just tasks. Fix tools before using them. Work local before remote.**

## The Problem

AI assistants (including me) tend to:
- Execute tasks sequentially without analyzing dependencies
- Use broken tools instead of fixing them first
- Work on remote systems before testing locally
- Miss the big picture by focusing on individual tasks
- Prioritize by order received, not by importance

**Result:** Wasted effort, repeated failures, user frustration.

## The Solution

### 1. Always Start With Dependency Analysis

Before executing ANY task, ask:
- What does this task depend on?
- What tools/systems must work for this to succeed?
- Are those tools/systems working?
- If not, fix them FIRST

**Example:**
```
Task: "Delegate research to Gemini"

Dependency Analysis:
├─ Requires: Gemini CLI/API
├─ Status: Gemini CLI broken (OAuth fails)
├─ Root cause: Network too slow for OAuth
├─ Solution: Create direct API script first
└─ Priority: Fix Gemini → THEN delegate

Correct order:
1. Create gemini-api.py (fix tool)
2. Test it works
3. THEN delegate research
```

**Not:**
```
❌ Try to delegate with broken CLI
❌ Fail
❌ Try again
❌ Fail again
❌ User frustrated
```

### 2. Priority Matrix (Eisenhower)

Categorize every task:

```
              URGENT       NOT URGENT
           ┌──────────┬──────────────┐
 IMPORTANT │    DO    │     PLAN     │
           │   NOW    │   (schedule) │
           ├──────────┼──────────────┤
NOT        │ DELEGATE │   ELIMINATE  │
IMPORTANT  │          │              │
           └──────────┴──────────────┘
```

**Examples:**
- Broken tool blocking work → DO NOW (important + urgent)
- Documentation → PLAN (important, not urgent)
- Nice-to-have features → DELEGATE or ELIMINATE
- Verbose commit messages → ELIMINATE (not important)

### 3. Local → Remote Flow

**Always work in this order:**
1. Fix LOCAL tools first
2. Test LOCALLY
3. Deploy to REMOTE
4. Test REMOTE

**Never:**
- ❌ Deploy broken code to remote
- ❌ Debug on production
- ❌ Fix tools on server before fixing locally

**Example (today's session):**
```
✅ CORRECT:
1. Create gemini-api.py locally
2. Test locally (works!)
3. Later: Deploy to server if needed

❌ WRONG (what I was doing):
1. Try to create on server
2. Permission denied
3. Try to fix on server
4. User stops me: "don't touch the server"
```

### 4. Test Immediately After Creating

Don't create tools and assume they work. TEST IMMEDIATELY.

**Pattern:**
```bash
# Create tool
create_tool()

# Test it RIGHT AWAY
test_tool()

# If it fails, fix before moving on
if [[ $? -ne 0 ]]; then
    fix_tool()
    test_again()
fi

# Only then proceed
use_tool()
```

**Anti-pattern:**
```bash
# Create 5 tools
create_tool_1()
create_tool_2()
create_tool_3()
create_tool_4()
create_tool_5()

# Try to use them (discover they're all broken)
use_tool_1()  # Fails!
use_tool_2()  # Fails!
# User: "why didn't you test them?"
```

### 5. Big Picture Before Details

Before diving into tasks, ask:
- What's the ultimate goal?
- What are we really trying to solve?
- Is this task necessary for that goal?
- Is there a simpler way?

**Example (from session):**
```
User requests: "Design agents and skills"

❌ WRONG (what I was doing):
- Immediately plan multiple agents
- Design complex architecture
- Try to delegate research
- Tools broken → everything blocked

✅ CORRECT (big picture):
- Goal: Enable efficient multi-agent work
- Blockers: Can't delegate (Gemini broken)
- Root issue: OAuth doesn't work
- Simple solution: Direct API script
- Priority: Fix delegation FIRST
- Then: Actually research/design
```

### 6. Batch Similar Tasks

Group similar tasks together for efficiency.

**Example:**
```
✅ EFFICIENT:
1. Create all documentation files at once
2. Commit all changes together
3. Push once

❌ INEFFICIENT:
1. Create file 1 → commit → push
2. Create file 2 → commit → push
3. Create file 3 → commit → push
(3x git operations instead of 1x)
```

### 7. Parallel When Possible

If tasks are independent, run them in parallel.

**Check:**
- Does task B depend on task A's output? → Sequential
- Are tasks independent? → Parallel

**Example:**
```bash
# Independent tasks → Parallel
research_task_1 &
research_task_2 &
research_task_3 &
wait

# Dependent tasks → Sequential
create_tool()
test_tool()
use_tool()
```

## Decision Framework

For every new task, go through this checklist:

### Pre-Flight Checklist
1. ☐ What tools does this need?
2. ☐ Are those tools working?
3. ☐ If not, can I fix them quickly (<5 min)?
4. ☐ If yes → fix them first
5. ☐ If no → delegate the fix or find alternative
6. ☐ What's the simplest way to do this?
7. ☐ Local or remote work?
8. ☐ Can this run in parallel with anything?

### Post-Task Checklist
1. ☐ Did it work?
2. ☐ If not, why? (document for next time)
3. ☐ Can this be automated?
4. ☐ Should this be documented?
5. ☐ Update todos/brainstorm log

## Common Mistakes & Fixes

### Mistake 1: Sequential By Default
**Symptom:** Waiting for task 1 to finish before starting task 2, when they're independent

**Fix:** Always ask "Can these run in parallel?"

### Mistake 2: Remote Before Local
**Symptom:** Modifying files on server, permission errors, user frustration

**Fix:** Work locally, test locally, deploy when stable

### Mistake 3: Using Broken Tools
**Symptom:** Repeated failures, OAuth timeouts, "this doesn't work"

**Fix:** Test tools before using. If broken, fix or replace immediately.

### Mistake 4: Missing Dependencies
**Symptom:** Task fails because prerequisite not done

**Fix:** Map dependencies BEFORE starting. Create dependency graph mentally.

### Mistake 5: Premature Optimization
**Symptom:** Planning complex architecture when simple script would work

**Fix:** Start simple. Add complexity only when needed.

## Efficiency Metrics

Track these to improve over time:
- **Tool failures:** How often do tools fail? (Goal: <5%)
- **Rework:** How often do I redo tasks? (Goal: <10%)
- **Local-first:** % of work done locally first (Goal: >90%)
- **Parallelization:** Tasks run in parallel vs sequential (Goal: >50% when possible)
- **User corrections:** How often does user stop me? (Goal: <3 per session)

## Real Examples From Today

### ❌ What I Did Wrong
1. Tried to delegate before fixing Gemini
2. Worked on server before testing locally
3. Didn't test tools immediately
4. Missed big picture (spent time planning instead of fixing blockers)
5. Sequential execution when parallel was possible

### ✅ What I Should Have Done
1. Identify: Gemini broken
2. Fix: Create gemini-api.py locally
3. Test: Verify it works
4. Then: Delegate research
5. Parallel: Create skills while Gemini researches

### 📊 Efficiency Comparison
```
WRONG APPROACH (what I did):
├─ Try delegate → Fail (5 min wasted)
├─ Try again → Fail (5 min wasted)
├─ Try server → Stopped by user (10 min wasted)
├─ Total: 20+ min, 0 progress

CORRECT APPROACH (what I should have done):
├─ Fix Gemini script → Works (10 min)
├─ Delegate research → Success (1 min)
├─ Create skills while waiting → Parallel (15 min)
├─ Total: 15 min, 3 tasks done
```

## Implementation

Use this skill by:
1. **Read this before starting ANY task**
2. **Go through pre-flight checklist**
3. **Map dependencies visually**
4. **Fix tools before using them**
5. **Work local → test → remote**
6. **Batch similar tasks**
7. **Parallelize when possible**
8. **Update brainstorm log with learnings**

## Integration With Other Skills

- **Delegation Skill:** Don't delegate until delegation tool works
- **Archive Skill:** Search for similar problems before solving
- **Bare Metal Config:** Know where tools/configs are
- **Brainstorm Log:** Document big-picture insights

## Quick Reference Card

```
EVERY TASK:
1. Dependencies? → List them
2. Tools working? → Test them
3. If broken → Fix FIRST
4. Simple solution? → Use it
5. Local or remote? → Local first
6. Parallel possible? → Do it
7. Test immediately? → Yes
8. Document? → Update logs
```

---

**Created:** December 9, 2025
**Purpose:** Prevent scattered execution, improve task prioritization
**Key Lesson:** See the system. Fix tools. Work local. Test first.
