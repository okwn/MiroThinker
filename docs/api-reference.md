# MiroThinker API Reference

This document provides comprehensive API reference documentation for MiroThinker, a deep research agent optimized for research and prediction.

## Table of Contents

- [ToolManager](#toolmanager)
- [Orchestrator](#orchestrator)
- [BaseClient](#baseclient)
- [ToolExecutor](#toolexecutor)
- [MCP Servers](#mcp-servers)
- [OutputFormatter](#outputformatter)

---

## ToolManager

**Location:** `libs/miroflow-tools/src/miroflow_tools/manager.py`

The `ToolManager` class manages tool execution and discovery across multiple MCP servers.

### Class: `ToolManager`

```python
from miroflow_tools.manager import ToolManager
```

#### Constructor

```python
def __init__(self, server_configs, tool_blacklist=None)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `server_configs` | `list` | List of server configurations returned by `create_server_parameters()` |
| `tool_blacklist` | `set`, optional | Set of `(server_name, tool_name)` tuples to exclude |

**Example:**
```python
tool_manager = ToolManager(server_configs=[
    {"name": "google_search", "params": server_params},
    {"name": "playwright", "params": playwright_params},
])
```

#### Methods

##### `get_all_tool_definitions()`

Connects to all configured servers and retrieves their tool definitions.

```python
async def get_all_tool_definitions() -> List[Dict[str, Any]]
```

**Returns:** List of dictionaries, each containing:
- `name`: Server name
- `tools`: List of tool definitions, each with:
  - `name`: Tool name
  - `description`: Tool description
  - `schema`: Input JSON schema

**Example:**
```python
tool_defs = await tool_manager.get_all_tool_definitions()
```

##### `execute_tool_call()`

Executes a single tool call on a specified server.

```python
@with_timeout(1200)
async def execute_tool_call(
    self,
    server_name: str,
    tool_name: str,
    arguments: Dict[str, Any]
) -> Dict[str, Any]
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `server_name` | `str` | Name of the server hosting the tool |
| `tool_name` | `str` | Name of the tool to execute |
| `arguments` | `dict` | Tool arguments dictionary |

**Returns:** Dictionary containing:
- `server_name`: Server name
- `tool_name`: Tool name
- `result`: Result content (on success)
- `error`: Error message (on failure)

**Example:**
```python
result = await tool_manager.execute_tool_call(
    server_name="google_search",
    tool_name="google_search",
    arguments={"query": "machine learning latest research"}
)
```

##### `get_server_params()`

Retrieves parameters for a specific server.

```python
def get_server_params(self, server_name: str) -> Any
```

##### `set_task_log()`

Sets the task logger for structured logging.

```python
def set_task_log(self, task_log: TaskLog)
```

---

## Orchestrator

**Location:** `apps/miroflow-agent/src/core/orchestrator.py`

The `Orchestrator` class coordinates agent task execution, managing the execution loop for main and sub-agents.

### Class: `Orchestrator`

```python
from miroflow_agent.src.core.orchestrator import Orchestrator
```

#### Constructor

```python
def __init__(
    self,
    main_agent_tool_manager: ToolManager,
    sub_agent_tool_managers: Dict[str, ToolManager],
    llm_client: BaseClient,
    output_formatter: OutputFormatter,
    cfg: DictConfig,
    task_log: Optional[TaskLog] = None,
    stream_queue: Optional[Any] = None,
    tool_definitions: Optional[List[Dict[str, Any]]] = None,
    sub_agent_tool_definitions: Optional[Dict[str, List[Dict[str, Any]]]] = None,
)
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `main_agent_tool_manager` | `ToolManager` | Tool manager for main agent |
| `sub_agent_tool_managers` | `Dict[str, ToolManager]` | Dictionary of tool managers for sub-agents |
| `llm_client` | `BaseClient` | LLM client for API calls |
| `output_formatter` | `OutputFormatter` | Formatter for output processing |
| `cfg` | `DictConfig` | Configuration object |
| `task_log` | `TaskLog`, optional | Logger for task execution |
| `stream_queue` | `Any`, optional | Async queue for streaming events |
| `tool_definitions` | `List[Dict]`, optional | Pre-fetched tool definitions |
| `sub_agent_tool_definitions` | `Dict[str, List]`, optional | Pre-fetched sub-agent tool definitions |

#### Methods

##### `run_sub_agent()`

Runs a sub-agent to handle a subtask.

```python
async def run_sub_agent(
    self,
    sub_agent_name: str,
    task_description: str,
) -> str
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `sub_agent_name` | `str` | Name of the sub-agent to run |
| `task_description` | `str` | Description of the subtask |

**Returns:** The final answer text from the sub-agent

---

## BaseClient

**Location:** `apps/miroflow-agent/src/llm/base_client.py`

Abstract base class for LLM provider clients.

### Class: `BaseClient`

```python
from miroflow_agent.src.llm.base_client import BaseClient
```

**Inheritance:** `ABC` (Abstract Base Class)

#### Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| `task_id` | `str` | Unique identifier for the current task |
| `provider` | `str` | LLM provider name (e.g., "openai", "anthropic") |
| `model_name` | `str` | Model name |
| `temperature` | `float` | Sampling temperature |
| `top_p` | `float` | Top-p sampling parameter |
| `max_tokens` | `int` | Maximum output tokens |
| `token_usage` | `TokenUsage` | Token usage tracking |

### TypedDict: `TokenUsage`

```python
class TokenUsage(TypedDict, total=True):
    total_input_tokens: int
    total_output_tokens: int
    total_cache_read_input_tokens: int
    total_cache_write_input_tokens: int
```

---

## ToolExecutor

**Location:** `apps/miroflow-agent/src/core/tool_executor.py`

Manages tool execution within the agent loop.

### Class: `ToolExecutor`

```python
from miroflow_agent.src.core.tool_executor import ToolExecutor
```

#### Constructor

```python
def __init__(
    self,
    main_agent_tool_manager: ToolManager,
    sub_agent_tool_managers: Dict[str, ToolManager],
    output_formatter: OutputFormatter,
    task_log: Optional[TaskLog] = None,
    stream_handler: Optional[StreamHandler] = None,
    max_consecutive_rollbacks: int = 5,
)
```

#### Methods

##### `execute_tool_calls()`

Executes multiple tool calls.

```python
async def execute_tool_calls(
    self,
    tool_calls: List[Dict[str, Any]],
    message_history: List[Dict[str, Any]],
    turn_count: int,
    agent_name: str,
) -> Tuple[List[Dict[str, Any]], List[Dict[str, Any]], bool]
```

---

## MCP Servers

MiroThinker uses MCP (Model Context Protocol) servers for tool execution. All servers are located in `libs/miroflow-tools/src/miroflow_tools/mcp_servers/`.

### Google Search Server

**File:** `searching_google_mcp_server.py`

| Tool Name | Description |
|-----------|-------------|
| `google_search` | Search Google for information |
| `google_image_search` | Search Google Images |

### Serper Search Server

**File:** `serper_mcp_server.py`

| Tool Name | Description |
|-----------|-------------|
| `serper_search` | Search using Serper API |
| `serper_news` | Search news via Serper |

### Browser Session (Playwright)

**File:** `browser_session.py`

**Class:** `PlaywrightSession`

```python
class PlaywrightSession:
    def __init__(self, server_params)
    async def connect(self)
    async def call_tool(self, tool_name: str, arguments: dict = None) -> Any
    async def close(self)
```

### Audio Server

**File:** `audio_mcp_server.py`

| Tool Name | Description |
|-----------|-------------|
| `audio_transcription` | Transcribe audio file to text |
| `audio_question_answering` | Answer questions about audio content |

### Vision Server

**File:** `vision_mcp_server.py`

| Tool Name | Description |
|-----------|-------------|
| `visual_question_answering` | Answer questions about images |

### Reading Server

**File:** `reading_mcp_server.py`

| Tool Name | Description |
|-----------|-------------|
| `scrape` | Scrape web page content |
| `read_file` | Read local file content |

### Python Server

**File:** `python_mcp_server.py`

| Tool Name | Description |
|-----------|-------------|
| `python_executor` | Execute Python code safely |
| `stateless_python` | Execute stateless Python operations |

---

## OutputFormatter

**Location:** `apps/miroflow-agent/src/io/output_formatter.py`

Handles formatting of agent outputs.

### Class: `OutputFormatter`

```python
from miroflow_agent.src.io.output_formatter import OutputFormatter
```

#### Constructor

```python
def __init__(
    self,
    cfg: DictConfig,
    task_log: Optional[TaskLog] = None,
)
```

#### Methods

##### `format_output()`

Formats agent output for display.

```python
def format_output(self, content: Any, format_type: str = "text") -> str
```

##### `format_tool_result()`

Formats tool execution results.

```python
def format_tool_result(self, result: Dict[str, Any]) -> str
```

---

## Configuration

MiroThinker uses Hydra for configuration management. Default configs are in `apps/miroflow-agent/config/`.

### Example Configuration (YAML)

```yaml
llm:
  provider: openai
  model_name: gpt-4o
  temperature: 0.7
  top_p: 0.9
  max_tokens: 8192
  max_context_length: 256000

agent:
  max_turns: 300
  context_compress_limit: 0
  keep_tool_result: 10
  sub_agents:
    search_agent:
      max_turns: 50
    reasoning_agent:
      max_turns: 30
```

---

## Usage Examples

### Basic Agent Execution

```python
from miroflow_tools.manager import ToolManager
from miroflow_agent.src.core.orchestrator import Orchestrator
from miroflow_agent.src.llm.factory import create_llm_client
from miroflow_agent.src.io.output_formatter import OutputFormatter

# Initialize tool manager
tool_manager = ToolManager(server_configs=configs)

# Create LLM client
llm_client = create_llm_client(task_id="task-001", cfg=config)

# Initialize orchestrator
orchestrator = Orchestrator(
    main_agent_tool_manager=tool_manager,
    sub_agent_tool_managers={},
    llm_client=llm_client,
    output_formatter=OutputFormatter(cfg=config),
    cfg=config,
)

# Run a sub-agent task
result = await orchestrator.run_sub_agent(
    sub_agent_name="search_agent",
    task_description="Find recent advances in quantum computing"
)
```

### Direct Tool Execution

```python
from miroflow_tools.manager import ToolManager

tool_manager = ToolManager(server_configs=[...])

# Execute a search
result = await tool_manager.execute_tool_call(
    server_name="google_search",
    tool_name="google_search",
    arguments={"query": "latest AI research 2026"}
)

if "error" in result:
    print(f"Search failed: {result['error']}")
else:
    print(f"Result: {result['result']}")
```

---

## Error Handling

All async methods return error information in the response dictionary:

```python
result = {
    "server_name": "google_search",
    "tool_name": "google_search",
    "error": "Error message here"  # Present on failure
}
```

Check for the presence of `error` key before processing results.

---

## Constants

| Constant | Value | Description |
|----------|-------|-------------|
| `DEFAULT_LLM_TIMEOUT` | 600 | Default timeout for LLM calls (seconds) |
| `DEFAULT_MAX_CONSECUTIVE_ROLLBACKS` | 5 | Max rollback attempts before breaking loop |
| `EXTRA_ATTEMPTS_BUFFER` | 200 | Safety buffer beyond max_turns |