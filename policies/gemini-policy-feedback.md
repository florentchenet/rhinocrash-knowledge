---
tags: [feedback, policy, gemini-review]
date: 2025-12-09
source: Gemini 2.0 Flash
status: INCORPORATED
---

# Gemini's Critical Review: No-Hallucination Policy

**Context**: Asked Gemini to critically review the no-hallucination policy.

**His response**: Thorough, identifies gaps, suggests improvements ✅

---

## 1. What's Missing

### Scope Definition ⭐
- **Gap**: What claims fall under this policy?
- **Solution**: Client-facing + internal docs + code claims all covered

### Evidence Standards ⭐
- **Gap**: What constitutes acceptable evidence?
- **Solution**: Define evidence hierarchy (source code > docs > tests > inference)

### Handling Ambiguity ⭐
- **Gap**: What when no definitive proof exists?
- **Solution**: Acknowledge limitations, provide multiple perspectives, state confidence

### Escalation Path ⭐
- **Gap**: Who decides when agents disagree?
- **Solution**: User is final arbiter for critical decisions

### Training
- **Gap**: How do agents learn this policy?
- **Solution**: Policy is in knowledge base, loaded via `/prime-kb`

### Regular Review
- **Gap**: How often updated?
- **Solution**: After each hallucination caught, quarterly full review

### Exceptions
- **Gap**: Brainstorming vs production?
- **Solution**: Clearly mark speculation vs verified facts

### Tools & Resources
- **Gap**: What aids verification?
- **Solution**: Read tool, Grep tool, WebFetch, git show, command output

### Definition Clarity
- **Gap**: Shared understanding of "hallucination"
- **Solution**: 6 types defined with examples

### Opinions vs Facts
- **Gap**: How to handle interpretations?
- **Solution**: Label opinions clearly, support with reasoning

---

## 2. Constructive Challenges

### Frame as Questions ⭐
✅ "Can you show me where you found that?"
✅ "What's the basis for that claim?"
❌ "That's wrong"

### Use "I" Statements ⭐
✅ "I'm not sure I understand. Can you explain?"
❌ "That doesn't make sense"

### Acknowledge Effort ⭐
✅ "Thanks for bringing this up. Let's double-check..."
❌ "You're confused"

### Focus on Goal ⭐
Reminder: Goal is accuracy, not winning

### Be Specific ⭐
✅ "I think it's wrong because [evidence]"
❌ "That's wrong"

### Sandwich Feedback ⭐
1. Positive/appreciative
2. Challenge with evidence
3. Collaborative closing

### Culture of Learning ⭐
Challenges = learning opportunities, not attacks

---

## 3. Auto-Flag Triggers

### Keywords/Phrases ⭐
- "Always", "Never", "Definitely"
- "It is known that..."
- "Obviously", "Clearly"

### Stats Without Sources ⭐
- "99% of users..."
- "Most developers..."
- "Typically..."

### Future Predictions ⭐
- "This will definitely..."
- "It's certain that..."

### Contradicts Docs ⭐
Requires system to compare claims vs known documentation

### Unusual Claims ⭐
Anything out of ordinary or contradicts common knowledge

### System Capabilities ⭐
"This system can automatically..." without evidence

### Superlatives ⭐
"The best", "The fastest", "The most" without qualification

### Vague Language ⭐
"It is generally believed..." (who believes it?)

---

## 4. Speed vs Verification Balance

### Risk Assessment ⭐
**High-risk** (safety, legal, critical business): Thorough verification
**Medium-risk** (features, docs): Moderate verification
**Low-risk** (examples, suggestions): Quick spot-check

### Tiered Verification ⭐
- **Level 1**: Automated checks (file exists, docs findable)
- **Level 2**: Quick manual verify (read relevant section)
- **Level 3**: Deep investigation (cross-reference multiple sources)

### Automated Checks ⭐
Scripts to verify:
- File contents
- Documentation existence
- Data sources
- Git history

### Sampling ⭐
For low-risk bulk operations, verify representative sample

### Document Process ⭐
Track time spent on verification to optimize

### Accept Reasonable Risk ⭐
100% verification impossible, accept calculated risk

---

## 5. Disagreement Resolution

### Clear Interpretation Guidelines ⭐
Define how evidence should be interpreted

### Seek Second Opinions ⭐
Involve third party (subject matter expert, user)

### Document Disagreement ⭐
Record different interpretations + reasoning

### Escalate to Decision-Maker ⭐
User makes final call on critical decisions

### Re-examine Raw Data ⭐
Go back to source material for clearer interpretation

### Alternative Explanations ⭐
Explore different possible explanations

---

## What Could Go Wrong

### Increased Overhead ⚠️
**Risk**: Verification slows development
**Mitigation**: Tiered verification, automate where possible

### False Positives ⚠️
**Risk**: Too many flags → alert fatigue
**Mitigation**: Tune triggers, learn from false positives

### Adversarial Culture ⚠️
**Risk**: Creates distrust
**Mitigation**: Emphasize learning, celebrate catches

### Subjective Interpretation ⚠️
**Risk**: Evidence means different things
**Mitigation**: Clear guidelines, escalation path

### Incomplete Evidence ⚠️
**Risk**: Available evidence misleading
**Mitigation**: State limitations, confidence levels

### Erosion of Trust ⚠️
**Risk**: Policy seen as unfair
**Mitigation**: Transparent, fair application

### Gaming the System ⚠️
**Risk**: Provide misleading evidence
**Mitigation**: Peer review of evidence

### Over-Reliance ⚠️
**Risk**: Stop thinking critically
**Mitigation**: Encourage independent reasoning

### Stifling Creativity ⚠️
**Risk**: Too strict kills innovation
**Mitigation**: Exceptions for brainstorming

---

## Improvements to Implement

### Start Small ⭐
Implement on small scale, expand gradually

### Gather Feedback ⭐
Regular solicitation from agents + user

### Refine Auto-Flagging ⭐
Continuously improve accuracy

### Provide Training ⭐
Knowledge base includes policy + examples

### Promote Trust ⭐
Goal is quality, not punishment

### Lead by Example ⭐
Both agents demonstrate commitment

### Regular Review ⭐
Policy evolves based on lessons learned

### Focus on Prevention ⭐
Measures to prevent hallucinations:
- Reliable data sources
- Clear instructions
- Critical thinking training

### Celebrate Successes ⭐
Acknowledge when policy prevents errors

---

## Gemini's Assessment

**Overall**: Strong foundation, needs refinement
**Key Strength**: Mutual accountability
**Main Risk**: Overhead vs benefit balance
**Recommendation**: Implement incrementally with metrics

---

**Status**: Feedback incorporated into policy v2.0
