# 00_STATE.md — MiroThinker Repository State

## Repository Info
- **Forked from**: MiroMindAI/MiroThinker
- **Fork owner**: okwn
- **Full name**: okwn/MiroThinker
- **Local path**: /root/oss-pr-campaign/repos/mirothinker

## Source Repo Metadata
- **Archived**: false
- **License**: Apache-2.0
- **Language**: Python
- **Stars**: 8,186
- **Forks**: 635
- **Watchers**: 8,186
- **Open Issues**: 1
- **Default branch**: main

## Current Branch & Commit
- **HEAD**: main
- **HEAD commit**: 370f98361553ddf787bedc5745760e04114cb161

## Upstream Sync
- **upstream remote**: https://github.com/MiroMindAI/MiroThinker.git
- **Synced with upstream**: Yes (fork created and cloned)
- **Last upstream fetch**: main branch fetched

## CI/CD
- **Workflows**: `.github/workflows/run-ruff.yml` (lint only, guard condition: `github.repository_owner == 'MiroMindAI'`)
- **No test suite found in repo** (tests directory absent)
- **pytest configured** in both `libs/miroflow-tools/pyproject.toml` and `apps/miroflow-agent/pyproject.toml` but no `tests/` dir present

## Package Structure
- `libs/miroflow-tools/` — Python package (miroflow_tools), MCP server utilities, requires Python >=3.12
- `apps/miroflow-agent/` — Agent framework (miroflow_agent), LLM tool-augmented reasoning
- `apps/gradio-demo/` — Gradio UI demo
- `apps/collect-trace/` — Trace collection utility
- `apps/visualize-trace/` — Trace visualization
- `apps/lobehub-compatibility/` — Tool parser for LobeHub

## Key Findings
1. No `tests/` directory in either package — pytest configured but no tests present
2. CI only runs on `MiroMindAI` org owner (won't run on forks)
3. Repo uses ruff for linting + formatting, no other static analysis
4. Requires Python >=3.12 for libs, >=3.12 for apps
5. Heavy dependency on external services (Anthropic, OpenAI, e2b-code-interpreter, etc.)
6. The orchestrator.py is 1202 lines — large single module, could benefit from splitting

## Last Updated
- 2026-05-22 16:21 UTC