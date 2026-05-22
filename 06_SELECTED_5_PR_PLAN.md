# 06_SELECTED_5_PR_PLAN.md — MiroThinker 5-PR Plan

## Selected 5 PRs (ordered by priority)

| # | PR | Priority | Type | Impact |
|---|-----|----------|------|--------|
| 1 | MCP Server Connection Reuse | P0 | Performance | High |
| 2 | Concurrency Backpressure Control | P2 | Performance | Medium |
| 3 | Fix E402 + Format in gradio-demo/main.py | Low | Lint | Low |
| 4 | Add pytest Test Suite (structure + basic tests) | Medium | Quality | High |
| 5 | Starlog Badge in README | Low | Docs | Low |

---

## PR 1: MCP Server Connection Reuse

### Summary
Modify `libs/miroflow-tools/src/miroflow_tools/manager.py` to reuse MCP server sessions across tool calls instead of spawning a new process per call.

### Problem (from Issue #137)
Every tool call (~400 per BC task) spawns new MCP server subprocess with stdio handshake, executes, then destroys:
```python
# Called ~400 times per BC task!
async with stdio_client(server_params) as (read, write):
    async with ClientSession(read, write) as session:
        await session.initialize()  # Handshake every time
        tool_result = await session.call_tool(tool_name, arguments)
# Process destroyed here
```
Already done for `playwright` (lines 247-252 of manager.py).

### Files to Modify
- `libs/miroflow-tools/src/miroflow_tools/manager.py`

### Approach
Add a session cache (dict of tool_name → persistent session) that persists for the lifetime of a task/tool manager, similar to how playwright browser is reused.

### Testing
- Run existing benchmark (if applicable)
- Verify no regression in tool calls
- Check that session is properly cleaned up on task completion

---

## PR 2: Concurrency Backpressure Control

### Summary
Add a shared semaphore mechanism to prevent E2B sandbox overload when running multiple concurrent evaluation tasks.

### Problem (from Issue #137)
`NUM_RUNS=2, MAX_CONCURRENT=60` → peak 120 processes simultaneously. E2B sandbox init spikes from 33s to 631s under contention.

### Files to Modify
- `apps/miroflow-agent/benchmarks/common_benchmark.py`
- Possibly `apps/miroflow-agent/src/core/pipeline.py`

### Approach
Implement a shared semaphore that limits total concurrent E2B sandbox initializations across all runs. Could use `asyncio.Semaphore` or a file-based lock.

### Testing
- Run benchmark with high concurrency and verify E2B init stays reasonable

---

## PR 3: Fix E402 + Format in gradio-demo/main.py

### Summary
Fix lint error (E402) and format issue in gradio-demo/main.py.

### Changes
1. Move `import atexit` to top of file with other imports
2. Add blank line after `import atexit` before `atexit.register(...)`

### Files to Modify
- `apps/gradio-demo/main.py`

### Testing
- Run `ruff check .` and `ruff format --diff .` to verify

---

## PR 4: Add pytest Test Suite

### Summary
Add basic test structure and unit tests for core modules. Both packages have pytest configured but no tests exist.

### Approach
Add minimal test coverage:
1. Create `libs/miroflow-tools/src/test/` with basic tests for `manager.py`
2. Create `apps/miroflow-agent/tests/` with basic tests for `parsing_utils.py` and `prompt_utils.py`

### Files to Create/Modify
- `libs/miroflow-tools/src/test/test_manager.py` (basic unit tests)
- `apps/miroflow-agent/tests/test_parsing_utils.py` (basic unit tests)
- Both packages have `testpaths = ["src/test"]` and `["tests"]` configured respectively

### Testing
- Run `pytest` to verify tests pass

---

## PR 5: Starlog Badge in README

### Summary
Add the Starlog deep-dive badge to the README as suggested in Issue #160.

### Badge Code (from issue)
```markdown
[![Starlog](https://img.shields.io/badge/Starlog-Deep_Dive-00e5ff?style=flat&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0id2hpdGUiPjxwYXRoIGQ9Ik0xMiAyTDkgOUgyTDcgMTRMNSAyMkwxMiAxN0wxOSAyMkwxNyAxNEwyMiA5SDE1TDEyIDJaIi8+PC9zdmc+)](https://starlog.is/articles/ai-agents/miromindai-mirothinker)
```

### Files to Modify
- `README.md` (add badge near other social badges at top)

### Testing
- Verify badge renders correctly

---

## Execution Order
1. **PR 3** (format fix) — Quick win, validates CI pipeline works on fork
2. **PR 4** (tests) — Establishes testing infrastructure for subsequent PRs
3. **PR 1** (MCP reuse) — High impact performance fix
4. **PR 2** (backpressure) — Medium impact, depends on PR1
5. **PR 5** (badge) — Low effort docs improvement

---

## CI Notes
- Workflow is org-gated (`if: github.repository_owner == 'MiroMindAI'`) so CI won't run on fork
- All PRs must pass `ruff check` and `ruff format` locally before submission
- Python 3.12+ required (uv handles this via pyproject.toml)

## Last Updated
- 2026-05-22 16:22 UTC