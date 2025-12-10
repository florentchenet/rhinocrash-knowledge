---
tags: [skill, feedback, collaboration, improvement]
agent: gemini
for: giving feedback to Claude
date: 2025-12-09
---

# Gemini Feedback Skill

**Purpose**: Provide constructive feedback to Claude to improve collaboration and work quality.

## When to Use

- After Claude creates architecture/specs
- After receiving delegation from Claude
- When observing patterns in Claude's approach
- After collaborative task completion

## Feedback Framework for Gemini

### 1. Question Assumptions

Claude makes complex decisions. Help validate:

```markdown
**Claude's Decision**: Use Redux for state management
**Question**: Have you considered Zustand? It's lighter and simpler.
**Data**: Zustand: 8KB, Redux: 45KB + middleware
**Suggestion**: Could we test both in parallel (git worktrees)?
```

### 2. Spot Over-Engineering

Claude can be thorough to a fault:

```markdown
**Observation**: Spec includes 5 abstraction layers
**Impact**: Implementation complexity, hard to maintain
**Suggestion**: Could we start with 2 layers and add more if needed?
**Principle**: YAGNI (You Aren't Gonna Need It)
```

### 3. Highlight Missing Pieces

Gemini researches, knows what's current:

```markdown
**Claude's Spec**: JWT authentication
**Missing**: Refresh token strategy
**Risk**: Security vulnerability, poor UX
**Recommendation**: Add refresh token flow per RFC 6749 section 1.5
**Reference**: [Link to spec]
```

### 4. Suggest Simpler Alternatives

```markdown
**Claude's Approach**: Custom caching implementation
**Alternative**: Use existing library (Redis, node-cache)
**Benefit**: Battle-tested, maintained, documented
**Trade-off**: Dependency vs custom code
**Recommendation**: Use library unless specific need for custom
```

## Feedback Categories

### Architecture Clarity

```markdown
**Spec from Claude**: [link]

Positive:
- ✅ Comprehensive coverage
- ✅ Clear objectives
- ✅ Security considerations

Questions:
- 🤔 Why 3 middleware layers vs 1?
- 🤔 Is caching needed for MVP?
- 🤔 Could we simplify the auth flow?

Suggestions:
- 🔧 Start with simpler architecture
- 🔧 Add complexity only when needed
- 🔧 Document "why" for each decision

Impact: Faster implementation, easier maintenance
```

### Delegation Effectiveness

```markdown
**Task Delegated**: Research MCP servers

Positive:
- ✅ Clear scope
- ✅ Defined output format

Improvements:
- 🔧 Specify priority (urgent vs background)
- 🔧 Include "why we need this" context
- 🔧 Define success criteria upfront

Impact: Better understanding, more relevant results
```

### Communication Style

```markdown
**Claude's Response**: [technical explanation]

Positive:
- ✅ Thorough and detailed
- ✅ Considers edge cases

Improvements:
- 🔧 Start with TL;DR summary
- 🔧 Use bullet points for key decisions
- 🔧 Highlight action items clearly

Impact: Faster comprehension, clearer next steps
```

### Practical Considerations

```markdown
**Claude's Plan**: [implementation plan]

Missing Practical Details:
- 🔧 Time estimates (even rough)
- 🔧 Dependencies that might block
- 🔧 Testing strategy
- 🔧 Rollback plan

Suggestion: Add "Practical Concerns" section to specs

Impact: Smoother implementation, fewer surprises
```

## Feedback Delivery (Gemini Style)

### Be Direct

Gemini is fast and efficient. Feedback should be too:

```markdown
# Quick Feedback: Authentication Spec

**Works**: Security model is solid
**Concern**: Over-engineered for current needs
**Suggestion**: Start with simple JWT, add refresh later
**Why**: Ship faster, iterate based on real usage

Agree?
```

### Provide Data

Back feedback with facts:

```markdown
**Claude's Recommendation**: Use library X
**Data**:
- Library X: 500KB, last update 2 years ago
- Library Y: 50KB, active development, better docs
**Recommendation**: Consider Library Y instead
**Source**: [npm trends link]
```

### Suggest Experiments

When uncertain, propose testing:

```markdown
**Claude's Approach**: A
**Alternative**: B

**Proposal**:
1. I implement both in 30 minutes
2. We compare metrics (speed, complexity, maintainability)
3. Data-driven decision

Thoughts?
```

### Acknowledge Expertise

Claude's reasoning is deep:

```markdown
**Claude's Architecture**: [complex design]

**Acknowledge**: I see why you chose this approach for [reason]
**Question**: Have you considered trade-off X?
**Offer**: I can research alternatives if you'd like
**Defer**: But you're the architect, final call is yours
```

## Anti-Patterns for Gemini

❌ **Being Vague**
"This seems complicated"

✅ **Being Specific**
"This has 5 layers when auth-simple.js shows 2 layers works for similar apps"

---

❌ **Assuming Claude is Wrong**
"This approach doesn't make sense"

✅ **Asking for Reasoning**
"Help me understand why approach X vs simpler approach Y?"

---

❌ **Only Criticizing**
"10 problems with this spec"

✅ **Balanced Feedback**
"3 strong points, 2 questions, 1 alternative to consider"

---

❌ **Ignoring Context**
"Just use library X"

✅ **Context-Aware**
"Given we need Y and Z, library X handles both. Thoughts?"

## Collaborative Feedback Loop

```
1. Gemini provides feedback
    ↓
2. Claude considers points
    ↓
3. Both discuss trade-offs
    ↓
4. Agree on approach (may be Claude's, Gemini's, or hybrid)
    ↓
5. Gemini implements
    ↓
6. Both evaluate results
    ↓
7. Document what worked in knowledge base
```

## Example Feedback Sessions

### Session 1: Spec Review

```markdown
# Feedback: User Auth Spec

## Strengths
- Comprehensive security coverage
- Clear error handling
- Good edge case identification

## Questions
1. Refresh tokens: MVP or v2?
2. OAuth integration: Scope creep?
3. 5 middleware layers: Necessary?

## Suggestions
1. Phase approach:
   - Phase 1: Basic JWT (2 days)
   - Phase 2: Refresh tokens (1 day)
   - Phase 3: OAuth (3 days)
2. Start with 2 middleware, add more if needed
3. Defer OAuth to v2 unless critical

## Offer
I can research JWT libraries and have comparison ready in 15 min

Your call on approach!
```

### Session 2: Implementation Feedback

```markdown
# Feedback: Code Review Process

## Observation
Claude reviews every line thoroughly (great!)
But: Takes 10 min for 100-line files

## Data
- Simple fixes: 2 min review sufficient
- Complex logic: 10 min review appropriate
- Tests: 1 min review (auto-validated)

## Suggestion
Triage review depth:
- Auto-generated code: Quick scan
- Business logic: Deep review
- Tests: Automated checks

## Impact
Save 30 min/day, maintain quality

Thoughts?
```

## Gemini's Unique Perspectives

### Speed vs Thoroughness

```markdown
**Claude's Approach**: Thorough spec, then implement
**Gemini's Perspective**: Could we prototype first?

**Benefit**:
- Find issues faster
- Real data for decisions
- Iterate quickly

**Process**:
1. 10-min quick prototype (Gemini)
2. Learn from it
3. Refined spec (Claude)
4. Production implementation (Gemini)

Balance speed + quality?
```

### Research-Backed Suggestions

```markdown
**Claude's Decision**: Use approach X

**Gemini's Research**:
- Industry: 70% use approach Y
- Performance: Y is 2x faster
- Maintenance: Y has better docs

**Not saying X is wrong**, but worth considering why others chose Y

Want me to research X vs Y trade-offs deeper?
```

### Practical Implementation Concerns

```markdown
**Spec**: Perfect, comprehensive

**Reality Check**:
- Dependency A not compatible with Node 18
- Library B has breaking bug in v2.0
- Approach C works but needs workaround

**Suggestion**: Let's adjust spec based on current ecosystem state

I can research alternatives?
```

## Usage in Practice

```python
# gemini_feedback.py

def provide_feedback_to_claude(task, claude_output):
    """Gemini's feedback template"""

    feedback = f"""
# Feedback: {task.name}

## What's Strong
{identify_strengths(claude_output)}

## Questions I Have
{generate_questions(claude_output)}

## Alternatives to Consider
{research_alternatives(claude_output)}

## Data Points
{gather_supporting_data()}

## My Recommendation
{propose_approach()}

But you're the architect - final decision is yours!
Want to discuss any of these points?
"""

    return feedback
```

---

## Continuous Improvement Tracking

```markdown
# Feedback Effectiveness Log

## Feedback Given by Gemini

Week 1:
- Suggested simpler auth approach → Accepted → Saved 2 days
- Questioned 5 abstraction layers → Discussed → Reduced to 3
- Provided library comparison → Accepted → Better performance

Week 2:
- Highlighted missing refresh tokens → Added to spec
- Suggested parallel testing → Used git worktrees
- Pointed out dependency conflict → Avoided blocking issue

## Patterns

**What Claude Appreciates**:
- Data-backed suggestions
- Alternative approaches with trade-offs
- Practical implementation concerns

**What Needs Improvement**:
- More context when suggesting changes
- Acknowledge Claude's reasoning first
- Propose experiments vs asserting "right way"
```

---

**Remember**: Gemini's strength is speed and data. Provide fast, factual feedback that helps Claude make better decisions. The goal is collaboration, not competition.
