# Claude with the Anthropic API

> **Course repository** for [Claude with the Anthropic API](https://anthropic.skilljar.com/claude-with-the-anthropic-api) — a comprehensive course covering the Claude API from basic usage through advanced agent architectures.

---

## 📖 About the Course

This course provides end-to-end coverage of building production-ready applications with the Anthropic API. Topics range from making your first API request all the way to designing multi-agent systems with MCP (Model Context Protocol) and RAG pipelines.

**Learning objectives:**
- Make API requests to Claude models and handle responses
- Implement multi-turn conversations, streaming, and structured output generation
- Build and evaluate prompts systematically using automated testing pipelines
- Create custom tools and integrate Claude with external services
- Design and implement RAG systems with hybrid search and reranking
- Use MCP (Model Context Protocol) to connect Claude to various data sources
- Understand common workflows and agent architectures

**Prerequisites:** Python proficiency, basic JSON knowledge, and an Anthropic API key.

---

## 📁 Repository Structure

```
.
├── notebooks/          # Jupyter notebooks — course exercises & completed examples
├── data/               # Sample datasets, PDFs, and images used in notebooks
├── docs/               # Generated reports (e.g. RAG chunking analysis)
├── outputs/            # Runtime outputs produced by notebook experiments
├── experiments/        # Sandbox copies of mcp/ and claude_code/ for free exploration
└── CLAUDE.md           # Guidance file for Claude Code (AI assistant context)
```

---

## 🧪 Experiments (`experiments/`)

A **sandbox area** for free exploration — safe to break things without affecting the main projects. It mirrors the structure of the two primary subprojects:

| Folder | Based on | Purpose |
|---|---|---|
| `experiments/mcp/` | `mcp/` | Experiment with the MCP Chat CLI freely |
| `experiments/claude_code/` | `claude_code/` | Experiment with the Document Tools MCP Server freely |

Use this folder to try out ideas, test modifications, or follow along with course exercises without worrying about breaking the reference implementations in `mcp/` and `claude_code/`.

---

## 📓 Notebooks (`notebooks/`)

The core learning material lives here as Jupyter notebooks. They are numbered by topic area:

| Notebook | Topic |
|---|---|
| `request.ipynb` | First API request — basic `messages.create` usage |
| `stream.ipynb` | Streaming responses with the Anthropic SDK |
| `001_prompting.ipynb` / `002_prompting_completed.ipynb` | Prompt engineering fundamentals |
| `001_thinking_complete.ipynb` | Extended thinking / chain-of-thought |
| `001_tools.ipynb` / `001_tools_007–009.ipynb` | Tool calling (function calling) |
| `003_tool_streaming.ipynb` / `_completed.ipynb` | Streaming with tool use |
| `001_prompt_evals*.ipynb` | Prompt evaluation pipelines & automated graders |
| `002_images.ipynb` | Vision — sending images to Claude |
| `002_embeddings.ipynb` | Generating and using text embeddings (Voyage AI) |
| `002_citations_complete.ipynb` | Citation extraction from documents |
| `003_caching.ipynb` | Prompt caching for cost & latency reduction |
| `003_vectordb.ipynb` | Setting up a vector database |
| `004_bm25.ipynb` | BM25 sparse retrieval |
| `005_hybrid.ipynb` | Hybrid search (BM25 + vector) with reranking |
| `001_chunking.ipynb` | Document chunking strategies for RAG |
| `005_code_execution.ipynb` | Code execution tool |
| `005_text_editor_tool.ipynb` | Text editor tool |
| `006_web_search.ipynb` / `_complete.ipynb` | Web search integration |
| `step-by-step-to-learn.ipynb` | Guided learning path overview |

---

## 🗂️ Data (`data/`)

Sample files used across the notebooks:

| File | Description |
|---|---|
| `earth.pdf` | PDF document used for RAG / citation exercises |
| `dataset.json` | Small JSON dataset for evaluation exercises |
| `streaming.csv` | CSV data for streaming-related experiments |
| `VoyageAI_API_Key_Directions.pdf` | Guide for obtaining a Voyage AI API key (embeddings) |
| `images/` | Images used in the vision notebook |

---

## 🤖 MCP Chat CLI (`experiments/mcp/`)

An interactive **terminal chat application** that connects to MCP servers and drives a full agentic loop using the Anthropic SDK.

### Architecture

```
CliApp (prompt_toolkit)
  └─ CliChat          — @doc references, /command handling
       └─ Chat.run()  — agentic message loop, tool-call/result cycle
            ├─ Claude (core/claude.py)      — thin Anthropic SDK wrapper
            └─ ToolManager (core/tools.py)  — aggregates tools from MCP servers
                  └─ MCPClient (mcp_client.py)  — stdio transport to each MCP server
```

### Key files

| File | Purpose |
|---|---|
| `main.py` | Entry point — launches the CLI |
| `mcp_server.py` | MCP server exposing documents and commands |
| `mcp_client.py` | Async MCP client (stdio transport) |
| `core/claude.py` | Anthropic SDK wrapper — manages message history |
| `core/chat.py` | Agentic loop logic |
| `core/cli_chat.py` | CLI-specific extensions (`@doc`, `/cmd`) |
| `core/cli.py` | `prompt_toolkit` UI with tab-completion |
| `core/tools.py` | Tool dispatcher — routes calls to the right MCP client |

### Quick start

```bash
cd mcp/
uv venv && source .venv/bin/activate
uv pip install -e .

# Start the interactive chat
uv run main.py

# Debug the MCP server with the MCP Inspector
uv run mcp dev mcp_server.py
```

### CLI features

- **`@document`** — inject document content inline (e.g. `Tell me about @earth.pdf`)
- **`/command`** — run MCP prompt templates (e.g. `/summarize earth.pdf`)
- Tab-completion for both `@` resources and `/` commands

---

## 🛠️ Document Tools MCP Server (`experiments/claude_code/`)

A **FastMCP server** that exposes document-processing tools to Claude Code (or any MCP client). It allows Claude to read and convert PDF and Word (`.docx`) files to Markdown.

### Tools exposed

| Tool | Description |
|---|---|
| `document_path_to_markdown` | Convert a `.pdf` or `.docx` file to Markdown text |

### Key files

| File | Purpose |
|---|---|
| `main.py` | Entry point — starts the FastMCP server |
| `tools/document.py` | Document conversion tool implementation |
| `tools/math.py` | Math helper tools |
| `tests/` | Pytest test suite with fixtures |

### Quick start

```bash
cd claude_code/
uv venv && source .venv/bin/activate
uv pip install -e .

# Run the server
uv run main.py

# Run tests
uv run pytest
```

### Registering with Claude Code

```bash
claude mcp add documents uv run main.py
```

Once registered, Claude Code auto-connects on startup. You can then ask:
> *"Convert the tests/fixtures/mcp_docs.docx file to Markdown"*

---

## ⚙️ Environment Setup

Copy the example file and fill in your credentials:

```bash
cp .env.example .env
```

```env
ANTHROPIC_API_KEY=sk-ant-...     # Required — your Anthropic API key
VOYAGE_API_KEY=pa-...            # Optional — needed for embedding notebooks
CLAUDE_MODEL=claude-sonnet-4-6   # Optional — override the default model
```

> **Note:** Each subproject (`mcp/` and `claude_code/`) has its own `.env` and virtual environment.

---

## 🚀 Getting Started

1. **Clone the repo** and navigate into it
2. **Get an Anthropic API key** at [console.anthropic.com](https://console.anthropic.com)
3. **Copy `.env.example` → `.env`** and add your key
4. **Open the notebooks** — start with `request.ipynb` for your first API call, then follow `step-by-step-to-learn.ipynb` for a guided path
5. **Explore the projects** — run the MCP Chat CLI (`mcp/`) or register the Document Tools server (`claude_code/`) with Claude Code

---

## 📚 Resources

- [Anthropic Documentation](https://docs.anthropic.com)
- [Anthropic Python SDK](https://github.com/anthropic/anthropic-sdk-python)
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io)
- [Voyage AI Embeddings](https://www.voyageai.com)
- [Course page](https://anthropic.skilljar.com/claude-with-the-anthropic-api)
