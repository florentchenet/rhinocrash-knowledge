---
tags: [integration, notebooklm, google, ai-research]
date: 2025-12-09
status: GUIDE
priority: FUTURE
---

# NotebookLLM Integration Guide

**Purpose**: Use Google's NotebookLLM for research analysis and knowledge synthesis with rhinocrash knowledge base.

## What is NotebookLLM

**NotebookLLM** is Google's experimental AI research assistant that:
- Analyzes uploaded documents (PDFs, text, markdown)
- Creates summaries and insights
- Generates Audio Overviews (podcast-style discussions)
- Answers questions grounded in your sources
- Creates study guides and outlines

**Key Advantage**: Grounded in YOUR documents only (no hallucination from general knowledge).

## Use Cases for rhinocrash

### 1. Knowledge Base Analysis
Upload `/research/*.md` files to NotebookLLM to:
- Find patterns across research
- Generate executive summaries
- Create study guides for methodology
- Identify knowledge gaps

### 2. Session Learning
Upload all session reports to:
- Identify recurring error patterns
- Extract meta-learnings
- Create training material for new instances

### 3. Audio Overviews
Convert documentation to podcasts:
- Listen to methodology while commuting
- Review session learnings hands-free
- Share knowledge with team

### 4. Cross-Reference Research
Upload multiple sources:
- IndyDevDan content + Anthropic docs
- Compare perspectives
- Find contradictions
- Synthesize best practices

## Setup & Access

### Access NotebookLLM
1. Visit: https://notebooklm.google.com
2. Sign in with Google account
3. Create new notebook

### Upload Knowledge Base
```bash
# Export knowledge base as single file
cd ~/Documents/Dev/rhinocrash-knowledge

# Create combined markdown
cat research/*.md > /tmp/research-combined.md
cat learnings/*.md > /tmp/learnings-combined.md
cat templates/*.md > /tmp/templates-combined.md

# Upload to NotebookLLM via web interface
```

## Workflow Integration

### Weekly Research Synthesis

```
Friday:
1. Gemini researches new content (automated)
2. Summaries saved to /research/weekly-*.md
3. Upload weekly summaries to NotebookLLM
4. Generate Audio Overview
5. Listen during weekend
6. Extract action items Monday
```

### Session Review

```
After complex session:
1. Upload SESSION-*-REPORT.md to NotebookLLM
2. Ask: "What were the key learnings?"
3. Ask: "What patterns indicate future problems?"
4. Save insights to /learnings/
```

### Methodology Training

```
For new Claude instance:
1. Upload all methodology docs to NotebookLLM
2. Generate study guide
3. Create FAQ from common questions
4. Add FAQ to knowledge base
```

## Example Prompts for NotebookLLM

### Analysis Prompts
```
"Analyze all error patterns and suggest preventive measures"

"Compare the approaches in parallel pattern vs spec prompts -
which is better for what use case?"

"Create a decision tree for when to use which priming command"

"What are the recurring themes across all research files?"
```

### Generation Prompts
```
"Create a 1-page quick reference guide for the R&D Framework"

"Generate a checklist for implementing new features using our methodology"

"Write a README for the knowledge base repo explaining how to use it"

"Create examples showing the evolution from vibe coding to spec prompts"
```

### Audio Overview Prompts
```
"Create an audio overview of the Agentic Engineering methodology"

"Generate a podcast discussing our error learnings"

"Explain the parallel agentic coding pattern as a conversation"
```

## Integration with Workflow

### Option A: Manual Weekly Review
1. Friday: Auto-update knowledge base (n8n workflow)
2. Saturday: Upload new files to NotebookLLM
3. Sunday: Listen to Audio Overview
4. Monday: Implement insights

### Option B: API Integration (Future)
```python
# Hypothetical - NotebookLLM API not public yet
import notebooklm

# Upload documents
notebook = notebooklm.create_notebook("Weekly Review")
notebook.add_source("/research/weekly-2025-12-13.md")

# Generate analysis
analysis = notebook.query("Summarize key findings")

# Generate audio
audio = notebook.generate_audio_overview()

# Save locally
with open("/tmp/weekly-overview.mp3", "wb") as f:
    f.write(audio)
```

### Option C: MCP Server (If Available)
```json
{
  "mcpServers": {
    "notebooklm": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "notebooklm-mcp@latest"],
      "env": {}
    }
  }
}
```

## Best Practices

### Document Preparation
✅ Use clear headings and structure
✅ Tag files consistently
✅ Keep file sizes reasonable (< 5MB each)
✅ Use markdown format for best results

❌ Don't upload binaries or images alone
❌ Avoid extremely long files (split them)
❌ Don't mix unrelated topics in one upload

### Question Formulation
✅ Be specific: "What errors occurred most frequently?"
✅ Ask for evidence: "Quote the relevant sections"
✅ Request structure: "Create a table comparing..."

❌ Avoid vague: "What's in this document?"
❌ Don't ask outside sources: "What does Google say?"

### Audio Overviews
✅ Best for: Conceptual content, methodology, discussions
✅ Request specific perspectives: "Explain as expert to beginner"

❌ Not ideal for: Code, tables, detailed specs

## Example: Weekly Knowledge Synthesis

### Monday Morning Routine

**1. Upload Last Week's Content**
```
Files:
- research/weekly-2025-12-06.md
- research/indydevdan-claude-content.md
- learnings/session-errors-and-fixes.md (updated)
```

**2. Ask NotebookLLM**
```
"Create a bulleted summary of:
1. New capabilities discovered
2. Errors encountered and fixed
3. Methodology improvements
4. Action items for this week"
```

**3. Generate Audio Overview**
- Click "Generate Audio Overview"
- Listen during commute (15 min)
- Take mental notes

**4. Update Knowledge Base**
```bash
# Create weekly synthesis
cat > ~/Documents/Dev/rhinocrash-knowledge/synthesis/week-2025-12-06.md <<EOF
# Week of Dec 6, 2025 - Synthesis

## Key Learnings
[From NotebookLLM analysis]

## Action Items
[From NotebookLLM recommendations]

## Patterns Observed
[From cross-referencing multiple sources]
EOF
```

## Limitations & Workarounds

### Limitations
- No public API yet (manual upload only)
- Max sources per notebook (~50 documents)
- Audio overviews are auto-generated (no customization)
- Cannot execute code or make changes

### Workarounds
- **API limit**: Create multiple notebooks by topic
- **Manual upload**: Use n8n to prepare combined files
- **Code execution**: Use Claude/Gemini for code, NotebookLLM for analysis
- **Changes**: NotebookLLM analyzes, you implement

## Comparison: NotebookLLM vs Other Tools

| Feature | NotebookLLM | Obsidian | Claude | Gemini |
|---------|-------------|----------|---------|---------|
| Grounded responses | ✅ Only sources | ⚠️ With plugins | ❌ May hallucinate | ❌ May hallucinate |
| Audio generation | ✅ Excellent | ❌ No | ❌ No | ✅ Text-to-speech |
| Code execution | ❌ No | ✅ With plugins | ✅ Yes | ✅ Yes |
| Multi-doc analysis | ✅ Excellent | ⚠️ Manual | ✅ Good | ✅ Good |
| Offline access | ❌ No | ✅ Yes | ❌ No | ❌ No |
| Cost | 🆓 Free | 🆓 Free | 💰 Paid | 💰 Paid |

### When to Use What
- **NotebookLLM**: Research synthesis, audio learning, multi-doc patterns
- **Obsidian**: Daily notes, knowledge graph, offline access
- **Claude**: Coding, complex reasoning, planning
- **Gemini**: Quick research, parallel tasks, image analysis

## Future Possibilities

### If NotebookLLM API Releases
1. **Auto-upload on knowledge update**
   - n8n triggers on git push
   - Uploads changed files to NotebookLLM
   - Generates weekly synthesis automatically

2. **Chat interface integration**
   - Ask NotebookLLM questions from Claude Code
   - Grounded responses from knowledge base
   - Best of both worlds

3. **Continuous learning loop**
   - Session ends → Upload logs
   - NotebookLLM extracts patterns
   - Auto-updates methodology docs
   - Next session uses improved methodology

---

## Quick Start

**5-Minute Test**:
1. Go to https://notebooklm.google.com
2. Create notebook "rhinocrash Test"
3. Upload `research/agentic-engineering-indydevdan.md`
4. Ask: "Summarize the R&D Framework"
5. Generate Audio Overview
6. Listen and evaluate

---

**Status**: Manual process ready. Waiting for API for full automation.

**Next Steps**:
1. Try weekly synthesis manually
2. Evaluate audio quality
3. Create synthesis template
4. Monitor for API release
