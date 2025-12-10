---
tags: [implementation, gemini, phase1, tools]
date: 2025-12-10
status: IN-PROGRESS
priority: HIGH
---

# Gemini Phase 1 Implementation: Read-Only File Access

**Started**: 2025-12-10
**Status**: Running in background
**Goal**: Give Gemini read-only file access with safety boundaries

---

## Implementation

### Files Created

**1. ~/scripts/gemini-tools.py**
```python
class GeminiTools:
    # Read-only tools with safety boundaries
    - read_file(path, max_lines=500)
    - list_files(pattern, max_results=100)
    - git_show(file, commit='HEAD')
    - git_diff(file=None)
    - search_content(pattern, path=None)
```

**Safety Features**:
- Allowed paths whitelist (knowledge base only)
- No write capabilities
- No system access
- Timeouts on all operations
- Result size limits

---

## Allowed Directories

Gemini can ONLY read from:
```python
ALLOWED_PATHS = [
    "/Users/hoe/Documents/Dev/rhinocrash-knowledge",
    "/Users/hoe/Documents/Documents - Mac/Dev/rhinocrash-knowledge",
    "/opt/rhinocrash/knowledge",
    "/tmp/gemini-",  # Temp files only
]
```

**Everything else**: Access denied

---

## Tool Capabilities

### read_file(path)
- Reads file contents
- Max 500 lines (configurable)
- UTF-8 with error handling
- Returns: {success, path, content} or {error}

### list_files(pattern)
- Glob pattern matching
- Max 100 results
- Files only (no dirs)
- Returns: {success, files, count} or {error}

### git_show(file, commit)
- Shows file at specific commit
- Default: HEAD
- Timeout: 10 seconds
- Returns: {success, content} or {error}

### git_diff(file)
- Shows uncommitted changes
- Optional: specific file
- Returns: {success, diff} or {error}

### search_content(pattern, path)
- Grep-based search
- Fast, recursive
- Max 100 matches returned
- Timeout: 30 seconds

---

## Testing

### Test 1: Read Knowledge Base File
```python
result = tools.read_file('/path/to/knowledge/file.md')
assert result['success'] == True
assert 'content' in result
```

### Test 2: Access Denied
```python
result = tools.read_file('/etc/passwd')
assert result['success'] == False
assert 'Access denied' in result['error']
```

### Test 3: List Files
```python
result = tools.list_files('/path/to/knowledge/*.md')
assert result['success'] == True
assert len(result['files']) > 0
```

### Test 4: Git Operations
```python
result = tools.git_diff()
assert result['success'] == True
```

---

## Next Steps (Phase 2)

**Not in this phase**:
- ❌ Write access (Phase 3)
- ❌ Code execution (Phase 2)
- ❌ Git commit (Phase 4)
- ❌ Network access (never)

**Coming in Phase 2**:
- ✅ Sandboxed code execution
- ✅ Test generation
- ✅ Validation scripts

---

## Integration Test

Once tools are ready, test with:
```bash
# Test that Gemini can read knowledge base
~/scripts/venv/bin/python ~/scripts/gemini-api.py \
  "Read the file /path/to/knowledge/README.md and summarize it"

# Should return: Summary based on actual file contents
```

---

## Rollback Plan

If issues arise:
```bash
# Remove tool file
rm ~/scripts/gemini-tools.py

# Gemini reverts to text-only mode
# No functionality lost, just no file access
```

---

## Success Criteria

- [  ] Gemini can read knowledge base files
- [  ] Access denied for paths outside whitelist
- [  ] No performance impact on system
- [  ] No security issues
- [  ] Gemini provides better research with file context

---

**Status**: Implementation running in background
**ETA**: 30 minutes
**Next**: Test and validate
