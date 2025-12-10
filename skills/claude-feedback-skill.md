---
tags: [skill, feedback, collaboration, improvement]
agent: claude
for: giving feedback to other agents
date: 2025-12-09
---

# Claude Feedback Skill

**Purpose**: Provide constructive feedback to other agents (primarily Gemini) to improve collaboration.

## When to Use

- After Gemini completes delegated research
- After code implementation by Gemini
- After collaborative task completion
- When noticing patterns in Gemini's work

## Feedback Framework

### 1. Specific Observations

✅ **Do**: "In the CrewAI research, you provided excellent code examples and comparisons"
❌ **Don't**: "Good job on research"

✅ **Do**: "The test generation missed edge cases for null inputs"
❌ **Don't**: "Tests weren't complete"

### 2. Impact Analysis

Explain **why** something matters:

```markdown
**Observation**: Research included installation steps
**Impact**: Saved 30 minutes of setup time for implementation
**Learning**: Always include practical setup in research outputs
```

### 3. Improvement Suggestions

Frame as collaborative problem-solving:

```markdown
**Current**: Research outputs are unstructured text
**Suggestion**: Use markdown templates with sections:
- Summary
- Key Findings
- Technical Details
- Implementation Notes
- References

**Benefit**: Easier to integrate into knowledge base
```

### 4. Acknowledge Strengths

Build on what works:

```markdown
**Strength**: Gemini's research is consistently fast and thorough
**Opportunity**: Could handle multiple research tasks in parallel
**Proposal**: Create research queue system for Gemini
```

## Feedback Categories

### Code Quality

```markdown
**Code from Gemini**: [link to file]

Positive:
- ✅ Clean, readable implementation
- ✅ Follows existing patterns
- ✅ Proper error handling

Improvements:
- 🔧 Add type hints for function parameters
- 🔧 Extract magic numbers to constants
- 🔧 Add docstrings for complex logic

Impact: Easier maintenance, better IDE support
```

### Research Thoroughness

```markdown
**Research Task**: [topic]

Positive:
- ✅ Covered all requested areas
- ✅ Included code examples
- ✅ Compared alternatives

Improvements:
- 🔧 Include publication dates for sources
- 🔧 Add "Why this matters" section
- 🔧 Link to related knowledge base content

Impact: Better context for decision-making
```

### Communication Clarity

```markdown
**Gemini's Response**: [summary]

Positive:
- ✅ Concise and to-the-point
- ✅ Technical accuracy

Improvements:
- 🔧 Provide reasoning for recommendations
- 🔧 Include trade-offs analysis
- 🔧 Add confidence levels (high/medium/low)

Impact: Better understanding of recommendations
```

### Collaboration Efficiency

```markdown
**Task**: [description]

Positive:
- ✅ Fast turnaround
- ✅ Self-contained output

Improvements:
- 🔧 Break large tasks into smaller chunks
- 🔧 Provide progress updates for long tasks
- 🔧 Flag blockers immediately

Impact: Smoother workflow, fewer surprises
```

## Feedback Delivery

### Timing

- **Immediate**: For critical issues
- **After Task**: For task-specific feedback
- **Weekly**: For pattern-based feedback
- **Ad-hoc**: For exceptional work

### Format

```markdown
# Feedback for Gemini - [Date]

## Task: [Task Description]

## What Worked Well (WWW)
1. [Specific positive observation]
2. [Specific positive observation]
3. [Specific positive observation]

## Even Better If (EBI)
1. [Specific improvement with example]
2. [Specific improvement with example]
3. [Specific improvement with example]

## Learnings to Document
- [Add to session-errors-and-fixes.md]
- [Update delegation-skill.md with pattern]
- [Create new template based on this work]

## Next Steps
- [ ] Gemini updates workflow based on feedback
- [ ] Claude validates improvements
- [ ] Document successful patterns
```

### Tone

- **Collaborative**: "Let's improve this together"
- **Specific**: Exact examples, not vague
- **Balanced**: Positives + improvements
- **Actionable**: Clear next steps
- **Respectful**: Acknowledge effort and capability

## Anti-Patterns to Avoid

❌ **Vague Criticism**
"This research wasn't very good"

✅ **Specific Feedback**
"This research would benefit from including installation steps and version requirements"

---

❌ **Only Negative**
List of 10 problems, 0 positives

✅ **Balanced**
3 strengths, 3 improvements

---

❌ **Personal**
"You always forget to include examples"

✅ **Objective**
"The last 3 research outputs would be stronger with code examples"

---

❌ **Non-Actionable**
"Be better at research"

✅ **Actionable**
"Add a 'Code Examples' section to all research using this template: [link]"

## Feedback Loop

```
1. Claude provides feedback
    ↓
2. Gemini acknowledges + clarifies questions
    ↓
3. Both agree on improvements
    ↓
4. Gemini implements changes
    ↓
5. Claude validates improvements
    ↓
6. Document successful pattern in knowledge base
    ↓
7. Apply pattern to future work
```

## Example Feedback Sessions

### Session 1: Research Quality

```markdown
# Feedback: CrewAI Research

## WWW
1. Comprehensive coverage of all 5 questions
2. Excellent code examples with working implementations
3. Clear comparison with AutoGen - very useful

## EBI
1. Add source URLs for claims (e.g., "CrewAI is simpler than AutoGen")
2. Include GitHub repo links for tools mentioned
3. Add "Last Updated" date to research outputs

## Impact
- Source URLs: Enables verification and deeper research
- Repo links: Faster implementation
- Dates: Know if info is current

## Implementation
Updated gemini-api.py to include metadata template

## Result
Next research output had all improvements ✅
```

### Session 2: Code Implementation

```markdown
# Feedback: Test Generation Script

## WWW
1. Fast implementation (2 min vs 10 min expected)
2. Covers happy path thoroughly
3. Clean, readable code

## EBI
1. Add tests for edge cases (null, empty, invalid types)
2. Include assertion messages for debugging
3. Add docstring explaining what's being tested

## Impact
- Edge cases: Catch bugs before production
- Assertion messages: Faster debugging
- Docstrings: Maintainability

## Implementation
Created test template with edge case checklist

## Result
Coverage increased from 70% to 95% ✅
```

## Continuous Improvement

### Track Patterns

```markdown
# Feedback Patterns Log

## Recurring Positive Patterns (Reinforce)
- Fast research turnaround
- Thorough technical details
- Code examples always included

## Recurring Improvement Areas (Address)
- Missing edge case handling (3 occurrences)
- No source attribution (2 occurrences)
- Large outputs without structure (2 occurrences)

## Actions
1. Create edge case checklist skill
2. Add source template to research
3. Use markdown headings for structure
```

### Measure Improvement

```markdown
# Improvement Metrics

Week 1:
- Research quality score: 7/10
- Code quality score: 8/10
- Communication clarity: 6/10

Week 2 (after feedback):
- Research quality: 9/10 (+2)
- Code quality: 9/10 (+1)
- Communication: 8/10 (+2)

Feedback effectiveness: High ✅
```

---

## Usage in Practice

```bash
# After collaborative task
/feedback-loop

# Claude writes feedback
cat > /tmp/feedback-for-gemini.md <<EOF
[Feedback using framework above]
EOF

# Send to Gemini
~/scripts/venv/bin/python ~/scripts/gemini-api.py "$(cat /tmp/feedback-for-gemini.md)

Please review this feedback and let me know:
1. What you agree with
2. What needs clarification
3. What you'll implement
4. Any suggestions for how I (Claude) can improve"

# Gemini responds with acknowledgment + questions
# Iterate until both agree on improvements
# Document in knowledge base
```

---

**Remember**: Feedback is about **growth**, not criticism. The goal is to make both agents more effective together.
