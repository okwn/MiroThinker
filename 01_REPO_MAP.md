# 01_REPO_MAP.md — MiroThinker Repository Map

## Directory Tree

```
mirothinker/
├── .github/
│   └── workflows/
│       └── run-ruff.yml          # Lint workflow (org-gated)
├── .gitignore
├── LICENSE                       # Apache 2.0
├── README.md                     # 1001 lines, multi-version docs
├── justfile
├── assets/                       # Images, docs (QA.md, LOCAL-TOOL-DEPLOYMENT.md, etc.)
├── libs/
│   └── miroflow-tools/           # Tool management & MCP servers
│       ├── pyproject.toml
│       └── src/
│           └── miroflow_tools/
│               ├── __init__.py
│               ├── manager.py
│               ├── mcp_servers/   # Search, scraping, python, vision, audio, reasoning
│               │   ├── __init__.py
│               │   ├── audio_mcp_server.py
│               │   ├── audio_mcp_server_os.py
│               │   ├── browser_session.py
│               │   ├── reading_mcp_server.py
│               │   ├── reasoning_mcp_server.py
│               │   ├── reasoning_mcp_server_os.py
│               │   ├── search_and_scrape_webpage.py
│               │   ├── searching_google_mcp_server.py
│               │   ├── searching_sogou_mcp_server.py
│               │   ├── serper_mcp_server.py
│               │   ├── stateless_python_server.py
│               │   ├── vision_mcp_server.py
│               │   ├── vision_mcp_server_os.py
│               │   ├── python_mcp_server.py
│               │   └── utils/
│               │       ├── __init__.py
│               │       └── url_unquote.py
│               └── dev_mcp_servers/
│                   ├── jina_scrape_llm_summary.py
│                   ├── search_and_scrape_webpage.py
│                   ├── stateless_python_server.py
│                   └── task_planner.py
│
├── apps/
│   ├── miroflow-agent/           # Main agent framework
│   │   ├── pyproject.toml
│   │   ├── conf/
│   │   │   ├── config.yaml
│   │   │   ├── agent/           # Agent configs (mirothinker_1.7, v1.5, v1.0, etc.)
│   │   │   │   ├── default.yaml
│   │   │   │   ├── demo.yaml
│   │   │   │   ├── mirothinker_1.7_keep5_max200.yaml
│   │   │   │   ├── mirothinker_1.7_keep5_max300.yaml
│   │   │   │   ├── mirothinker_v1.0.yaml
│   │   │   │   ├── mirothinker_v1.0_keep5.yaml
│   │   │   │   ├── mirothinker_v1.5.yaml
│   │   │   │   ├── mirothinker_v1.5_keep5_max200.yaml
│   │   │   │   ├── mirothinker_v1.5_keep5_max400.yaml
│   │   │   │   ├── multi_agent.yaml
│   │   │   │   ├── multi_agent_os.yaml
│   │   │   │   ├── single_agent.yaml
│   │   │   │   └── single_agent_keep5.yaml
│   │   │   ├── benchmark/       # Benchmark configs (gaia, browsecomp, hle, etc.)
│   │   │   │   ├── aime2025.yaml
│   │   │   │   ├── browsecomp.yaml
│   │   │   │   ├── browsecomp_zh.yaml
│   │   │   │   ├── collect_trace.yaml
│   │   │   │   ├── debug.yaml
│   │   │   │   ├── default.yaml
│   │   │   │   ├── deepsearchqa.yaml
│   │   │   │   ├── frames.yaml
│   │   │   │   ├── futurex.yaml
│   │   │   │   ├── gaia-validation-text-103.yaml
│   │   │   │   ├── gaia-validation.yaml
│   │   │   │   ├── hle-text-2158.yaml
│   │   │   │   ├── hle-text-500.yaml
│   │   │   │   ├── hle.yaml
│   │   │   │   ├── seal-0.yaml
│   │   │   │   ├── webwalkerqa.yaml
│   │   │   │   └── xbench_deepsearch.yaml
│   │   │   └── llm/
│   │   │       ├── claude-3-7.yaml
│   │   │       ├── default.yaml
│   │   │       ├── gpt-5.yaml
│   │   │       └── qwen-3.yaml
│   │   └── src/
│   │       ├── __init__.py
│   │       ├── config/
│   │       │   ├── __init__.py
│   │       │   └── settings.py
│   │       ├── core/
│   │       │   ├── __init__.py
│   │       │   ├── answer_generator.py
│   │       │   ├── orchestrator.py     # 1202 lines - main execution loop
│   │       │   ├── pipeline.py
│   │       │   ├── stream_handler.py
│   │       │   └── tool_executor.py
│   │       ├── io/
│   │       │   ├── __init__.py
│   │       │   ├── input_handler.py
│   │       │   └── output_formatter.py
│   │       ├── llm/
│   │       │   ├── __init__.py
│   │       │   ├── base_client.py
│   │       │   ├── factory.py
│   │       │   ├── providers/
│   │       │   │   ├── __init__.py
│   │       │   │   ├── anthropic_client.py
│   │       │   │   └── openai_client.py
│   │       │   └── util.py
│   │       ├── logging/
│   │       │   ├── __init__.py
│   │       │   ├── summary_time_cost.py
│   │       │   └── task_logger.py
│   │       └── utils/
│   │           ├── __init__.py
│   │           ├── parsing_utils.py
│   │           ├── prompt_utils.py
│   │           └── wrapper_utils.py
│   │
│   ├── gradio-demo/
│   │   ├── pyproject.toml
│   │   ├── main.py
│   │   ├── prompt_patch.py
│   │   └── utils.py
│   │
│   ├── collect-trace/
│   │   └── pyproject.toml
│   │
│   ├── visualize-trace/
│   │   ├── pyproject.toml
│   │   ├── requirements.txt
│   │   ├── app.py
│   │   ├── run.py
│   │   └── trace_analyzer.py
│   │
│   └── lobehub-compatibility/
│       ├── pyproject.toml
│       ├── requirements.txt
│       ├── MiroThinkerToolParser.py
│       ├── test_tool_parser.py
│       └── unit_test.py
│
└── assets/
    ├── 17_table.png
    ├── LOCAL-TOOL-DEPLOYMENT.md
    ├── QA.md
    ├── miroflow_logo.png
    ├── miro_thinker.png
    ├── mirothinker_logo.png
    ├── gaia_text_103.png
    ├── futurex-09-12.png
    └── qwen3_nonthinking.jinja
```

## File Counts
- Python files: ~30+
- YAML configs: ~30+
- No test files present (tests/ directories missing despite pytest config)
- Assets: images + markdown docs

## Languages & Tools
- **Language**: Python (3.12+)
- **Build**: hatchling (pyproject.toml based)
- **Linting**: ruff (v0.8.0)
- **Testing**: pytest (configured but no tests present)
- **Environment**: uv package manager

## Dependencies Summary
- `miroflow-tools` (local editable): Tool/MCP management
- LLM: anthropic, openai, google-genai, transformers
- Agents: mcp, fastmcp
- Code execution: e2b-code-interpreter==1.2.1
- Search: duckduckgo-search, Tavily, Serper, Sogou, Google, Exa
- Web: playwright, requests, aiohttp
- Docs: pdfminer-six, python-pptx, mammoth, markitdown
- Config: omegaconf, hydra-core, jinja2
- Utilities: redis, tiktoken, tenacity, regex, youtube_transcript_api, mutagen