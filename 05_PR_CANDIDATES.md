# 05_PR_CANDIDATES.md — MiroThinker PR Candidates

## Overview
Repository has 1 open issue and recent merged PRs show active development. Ruff found 1 lint error and 1 format issue. Large modules (orchestrator.py: 1202 lines) and missing tests present opportunities.

---

## Issue-Derived Candidates

### CANDIDATE 1: MCP Server Connection Reuse (from Issue #137)
**Priority**: P0 — Performance bottleneck
**Source**: Issue #137 "Evaluation pipeline performance bottlenecks"
**Problem**: Every tool call (~400 per BC task) spawns a new MCP server subprocess with stdio handshake, executes, then destroys. This is extremely wasteful.
**Location**: `libs/miroflow-tools/src/miroflow_tools/manager.py` → `execute_tool_call()`
**Expected Impact**: 2-5 min saved per task, eliminate ~400 process spawns per task
**Status**: Issue says "🔴 To do"
**Reference PR**: #139 already fixed parallel MCP init with `asyncio.gather()` — connection reuse is the natural follow-up

### CANDIDATE 2: Concurrency Backpressure Control (from Issue #137)
**Priority**: P2 — Performance
**Source**: Issue #137
**Problem**: `NUM_RUNS=2, MAX_CONCURRENT=60` → peak 120 processes simultaneously. E2B sandbox init spikes from 33s to 631s under contention.
**Proposed Fix**: Shared semaphore across runs, or adaptive concurrency
**Status**: Issue says "⬚ To do"

---

## Lint/Format Fixes

### CANDIDATE 3: Fix E402 in gradio-demo/main.py
**Priority**: Low — CI enforces formatting
**Source**: `ruff check` output
**Problem**: `import atexit` at line 28 is not at top of file (E402)
**Location**: `apps/gradio-demo/main.py:28`
**Fix**: Move `import ateat` to top of file (after existing imports)
**Note**: ruff format also wants a blank line after the import before `atexit.register(...)`

---

## Code Quality Candidates

### CANDIDATE 4: Add Missing Test Suite
**Priority**: Medium — Quality gap
**Source**: Both `libs/miroflow-tools/pyproject.toml` and `apps/miroflow-agent/pyproject.toml` have full pytest configuration but no `tests/` directory exists
**Problem**: No unit tests for:
- `orchestrator.py` (1202 lines — critical path)
- `tool_executor.py`
- `parsing_utils.py`
- MCP server modules
**Impact**: No regression protection, hard to validate changes

### CANDIDATE 5: Break Up Large orchestrator.py
**Priority**: Medium — Maintainability
**Source**: Code review
**Problem**: `orchestrator.py` is 1202 lines — too large for single module. Should be split into:
- `orchestrator_core.py` (main loop)
- `context_manager.py` (context management)
- `retry_manager.py` (retry logic)
**Note**: Similar pattern to how `stream_handler.py` was already split off

### CANDIDATE 6: Starlog Badge in README (from Issue #160)
**Priority**: Low — Docs enhancement
**Source**: Issue #160 (open, external)
**Problem**: Starlog published a deep-dive article offering a badge for the README
**Effort**: Very low (markdown badge addition)

---

## PR History Reference (Recent Merged)

| PR # | Title | Type | Status |
|------|-------|------|--------|
| 162 | feat: add LiteLLM as AI gateway provider | feature | closed |
| 159 | fix: disable tool definitions during final answer/summary generation | bugfix | closed |
| 157 | Add report badge to README | docs | merged |
| 156 | feat: add Exa AI-powered search tool | feature | closed |
| 154 | perf: reuse persistent MCP server sessions across tool calls | perf | closed |
| 152 | docs: add WSL uv sync memory allocation troubleshooting | docs | merged |
| 150/149/146/145 | Tavily search additions | feature | closed |
| 139 | perf: parallelize tool server init and reduce LLM retry overhead | perf | merged |
| 138 | perf: parallelize MCP server initialization | perf | merged |
| 134 | feat: add MiniMax as LLM provider with M2.7 default | feature | closed |

**Observations**:
- Recent perf work (PRs 154, 139, 138) focused on MCP initialization — Candidate 1 is the natural continuation
- Recent feature additions (Tavily, Exa, LiteLLM, MiniMax) show active expansion
- Documentation PRs get merged quickly
- The repo has a pattern of small, focused PRs

---

## Last Updated
- 2026-05-22 16:22 UTC