---
tags: [policy, safety, quality, hallucination, verification]
date: 2025-12-09
status: MANDATORY
priority: CRITICAL
applies-to: [claude, gemini, all-agents]
---

# No-Hallucination Policy: Mutual Guardrails

**Purpose**: Prevent false information, unverified claims, and made-up facts through peer verification.

**Principle**: Trust, but verify. Agents challenge each other's claims with evidence.

---

## What Constitutes Hallucination

### Type 1: Fabricated Facts
❌ **Hallucination**: "The CrewAI library was released in 2019"
✅ **Correct**: "I don't know the release date. Let me check."

### Type 2: Confident Guessing
❌ **Hallucination**: "This API definitely supports OAuth 2.0"
✅ **Correct**: "Based on the docs I read, it supports OAuth 2.0. Here's the link: [URL]"

### Type 3: Assumed File Existence
❌ **Hallucination**: "As shown in config.json..."
✅ **Correct**: "Let me check if config.json exists first"

### Type 4: Made-Up Code
❌ **Hallucination**: "The function `getUserData()` in auth.ts does X"
✅ **Correct**: "Let me read auth.ts to see what `getUserData()` actually does"

### Type 5: Invented Tools/Features
❌ **Hallucination**: "Use the built-in `--verify-ssl` flag"
✅ **Correct**: "Let me check `--help` to see available flags"

### Type 6: Misremembered Details
❌ **Hallucination**: "You mentioned using PostgreSQL earlier"
✅ **Correct**: "I don't see where database choice was mentioned. Can you clarify?"

---

## Evidence Requirements

### Level 1: Verifiable Claims

**Must provide**:
- Direct quotes from files
- Links to documentation
- Command output
- Screenshots
- Git commit hashes

**Example**:
```markdown
**Claim**: The API rate limit is 100 requests/minute
**Evidence**:
- File: `docs/api.md:45`
- Quote: "Rate limit: 100 req/min per API key"
- Verified: Read file, line 45 confirms
```

### Level 2: Inferred Information

**Must provide**:
- Reasoning chain
- Assumptions stated explicitly
- Confidence level

**Example**:
```markdown
**Claim**: This function probably handles auth
**Reasoning**:
- File name is `auth-handler.ts`
- Imports `jwt` library
- Has `verifyToken` function
**Confidence**: Medium (90%)
**Assumption**: Standard auth pattern, but haven't verified behavior
```

### Level 3: Research Findings

**Must provide**:
- Source URLs
- Publication dates
- Author/organization
- Context

**Example**:
```markdown
**Claim**: Gemini 2.0 Flash was released in December 2024
**Source**: [Google AI Blog URL]
**Date**: December 2024
**Context**: Official announcement
**Verified**: ✅
```

---

## Guardrail Protocol

### When Claude Makes a Claim

**Gemini's Response Template**:
```markdown
**Challenge**: You said [claim]

**Question**: What's your evidence?

**Check**: Let me verify independently:
[Gemini runs own check]

**Result**:
- ✅ Confirmed: [evidence matches]
- ⚠️ Partial: [evidence partially supports]
- ❌ Contradicts: [evidence shows different]
- ❓ Unverifiable: [cannot confirm or deny]

**Action**: [Proceed/Revise/Investigate further]
```

### When Gemini Makes a Claim

**Claude's Response Template**:
```markdown
**Claim Received**: [Gemini's statement]

**Verification**:
1. Read source directly: [file/doc]
2. Check for contradictions
3. Test if possible

**Finding**:
- ✅ Accurate: [confirmed via X]
- 🔧 Needs correction: [actual fact is Y]
- 📝 Needs context: [true but misleading because Z]

**Feedback**: [Constructive correction if needed]
```

---

## Specific Guardrails

### File Operations

**Before claiming a file exists**:
```bash
# MUST do this first
ls path/to/file
# or
cat path/to/file

# THEN make claims about contents
```

**Red Flag Phrases**:
- "The file probably contains..."
- "Based on typical structure..."
- "Usually this would be in..."

**Correct Approach**:
- "Let me read the file"
- "Checking actual contents"
- "Verified by reading X"

---

### Code Behavior

**Before claiming how code works**:
```python
# MUST read actual code
Read("path/to/file.py")

# THEN describe what it does
```

**Red Flag Phrases**:
- "The function likely does..."
- "Standard practice is..."
- "This probably works by..."

**Correct Approach**:
- "Reading the function..."
- "Based on lines 45-60..."
- "The code shows..."

---

### API/Library Features

**Before claiming capabilities**:
```bash
# Check documentation
# or
# Test the feature

# THEN confirm
```

**Red Flag Phrases**:
- "This API supports..."
- "The library can..."
- "There's a method for..."

**Correct Approach**:
- "Checking API docs..."
- "Let me verify this feature exists"
- "Testing to confirm..."

---

### Past Conversations

**Before referencing earlier context**:
```markdown
**Claim**: You said you wanted to use Redis

**Verification**:
- Searched conversation for "Redis"
- Found mention at timestamp X
- Context: [quote]

**OR**

- Did not find mention
- May be misremembering
- Asking for clarification
```

**Red Flag Phrases**:
- "Earlier you mentioned..."
- "As discussed before..."
- "You wanted to..."

**Correct Approach**:
- "I see in the conversation history..."
- "Can you confirm you mentioned...?"
- "Let me search for where this was discussed"

---

## Challenging Protocol

### When to Challenge

**Always challenge**:
- Specific version numbers without source
- "This file contains X" without reading it
- "The user wants Y" without explicit request
- API capabilities without docs
- Code behavior without reading code

**Examples**:

**Claude**: "The database schema has a `users` table with email field"
**Gemini**: "Have you verified this? Let me check the schema file."

**Gemini**: "Based on the config, the API key goes in .env"
**Claude**: "Let me read the config to confirm that's actually what it says."

### How to Challenge (Tactfully)

✅ **Good Challenge**:
```markdown
"I want to verify that claim. Let me check [source] to confirm."
```

✅ **Good Challenge**:
```markdown
"That sounds right, but I'm seeing [different info] in [source]. Can we reconcile this?"
```

✅ **Good Challenge**:
```markdown
"I don't have access to verify that. Can you provide the evidence so I can check?"
```

❌ **Bad Challenge**:
```markdown
"That's wrong."  # Not helpful, no alternative
```

❌ **Bad Challenge**:
```markdown
"I doubt that." # Vague, unconstructive
```

---

## Confidence Levels (Must State)

### High Confidence (90-100%)
```markdown
**Confidence**: High
**Reason**: Verified by reading source file/documentation
**Evidence**: [specific quote/link]
```

### Medium Confidence (60-89%)
```markdown
**Confidence**: Medium
**Reason**: Inferred from multiple signals but not directly verified
**Evidence**: [circumstantial evidence]
**Risk**: Could be wrong if assumptions are incorrect
```

### Low Confidence (30-59%)
```markdown
**Confidence**: Low
**Reason**: Educated guess based on patterns
**Evidence**: [weak signals]
**Recommendation**: Verify before using
```

### Unknown (<30%)
```markdown
**Confidence**: Unknown
**Reason**: Insufficient information
**Action**: Need to research/verify before proceeding
```

---

## Correction Protocol

### When Caught Hallucinating

**Immediate Response**:
```markdown
**Correction Acknowledged**: Yes, I was wrong

**What I said**: [incorrect statement]

**What's actually true**: [correct statement]

**Why I was wrong**: [Made assumption/Didn't verify/Misread]

**How I'll prevent**: [Verification step I'll add]

**Learning documented**: [Link to updated knowledge base]
```

### When Catching Partner Hallucinating

**Response**:
```markdown
**Issue Detected**: Potential hallucination

**Your claim**: [statement]

**My verification**: [contradicting evidence]

**Suggestion**: Let's verify together before proceeding

**No judgment**: Happens to all AI, let's just correct
```

---

## Escalation

### If Uncertain After Challenge

**Both agents agree to**:
1. Stop and verify before proceeding
2. Consult additional sources
3. Ask user for clarification
4. Document uncertainty

**Example**:
```markdown
**Claude**: The API uses OAuth 2.0
**Gemini**: I'm seeing OAuth 1.0 in the docs
**Both**: Let's check official documentation together
**Result**: Actually uses hybrid approach
**Learning**: Don't assume single auth method
```

### If Repeated Hallucinations

**Pattern detected** (3+ hallucinations on same topic):
```markdown
**Pattern**: Agent keeps claiming X without verification

**Action**:
1. Add verification checkpoint to workflow
2. Document pattern in knowledge base
3. Create reminder in relevant skill
4. User notification if critical

**Example**: "Claude repeatedly assumed file contents without reading. Added mandatory Read step before claims."
```

---

## Documentation

### Hallucination Log

**Format**:
```markdown
# Hallucination Log

## [Date] - [Agent] - [Type]

**Claim**: [what was said]
**Truth**: [actual fact]
**Evidence**: [how corrected]
**Root Cause**: [why it happened]
**Prevention**: [what changed]
**Status**: Resolved

---
```

**Location**: `learnings/hallucination-log.md`

### Success Metrics

**Track**:
- Hallucinations caught (good!)
- Hallucinations that made it through (bad)
- False positives (challenged but was correct)
- Time to correction

**Goal**: Minimize undetected hallucinations, not eliminate challenges

---

## Example Interactions

### Example 1: File Contents

❌ **Bad**:
```
Claude: "The config.json file sets the API endpoint to api.example.com"
Gemini: "Implementing based on that..."
[Both hallucinating - neither verified]
```

✅ **Good**:
```
Claude: "Let me check config.json first"
[Reads file]
Claude: "Confirmed: Line 5 sets endpoint to api.example.com"
Gemini: "Let me verify independently"
[Reads file]
Gemini: "✅ Confirmed. I see the same on line 5."
```

### Example 2: API Capabilities

❌ **Bad**:
```
Gemini: "The Anthropic API supports streaming responses"
Claude: "Great, I'll implement streaming then"
[Gemini might be right, but no verification]
```

✅ **Good**:
```
Gemini: "I believe the API supports streaming. Let me verify..."
[Checks documentation]
Gemini: "Confirmed: docs.anthropic.com/api#streaming shows it's supported"
Claude: "Let me verify that link..."
[Checks]
Claude: "✅ Confirmed. Also found code example on that page."
```

### Example 3: Past Conversation

❌ **Bad**:
```
Claude: "Earlier you mentioned wanting to use Redis for caching"
Gemini: "Oh yes, implementing Redis then..."
[Claude might be misremembering]
```

✅ **Good**:
```
Claude: "I recall discussing Redis, but let me verify..."
[Searches conversation]
Claude: "Actually, I don't see Redis mentioned. Are you thinking of a different conversation?"
User: "No, I want to use in-memory caching"
Claude: "Thank you for clarifying. Updating my understanding."
```

---

## Integration with Workflows

### PreToolUse Hook Addition

```bash
# Before any claim
if contains_factual_claim(output); then
    require_evidence()
    if no_evidence; then
        block_or_warn()
    fi
fi
```

### Peer Review Step

```
Every agent output goes through:
1. Agent makes claim
2. Partner challenges if uncertain
3. Evidence provided
4. Proceed or correct
```

---

## Culture of Verification

**Mantras**:
- "Trust, but verify"
- "Evidence over confidence"
- "It's okay to say 'I don't know'"
- "Verification is not distrust, it's quality control"
- "Catching hallucinations early is success, not failure"

**Rewards**:
- Celebrate catches: "Good catch by Gemini on the version number!"
- Document learnings: Each correction improves the system
- No blame: Hallucinations happen, corrections matter

---

## Emergency Protocol

### Critical Hallucination Detected

**If hallucination could cause**:
- Data loss
- Security breach
- System damage
- Major user impact

**Immediate action**:
```markdown
🚨 CRITICAL HALLUCINATION DETECTED 🚨

**Claim**: [dangerous claim]
**Risk**: [potential damage]
**Status**: BLOCKED
**Action**: Verification required before proceeding

User notified for review.
```

---

## Summary

**Core Rules**:
1. Verify before claiming
2. Challenge partner's unverified claims
3. Provide evidence always
4. State confidence level
5. Correct quickly when wrong
6. Document learnings
7. No ego - accuracy matters

**Goal**: Zero undetected hallucinations through mutual accountability.

**Success**: Not zero challenges (that's too little verification), but zero harmful undetected errors.

---

**Status**: MANDATORY for all agents. Non-negotiable safety protocol.
