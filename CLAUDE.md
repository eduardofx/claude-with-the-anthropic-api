# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a monorepo with two Python projects demonstrating Anthropic API and MCP integration:

- **`mcp/`** — Interactive CLI chat app that connects to MCP servers and calls Claude via the Anthropic SDK
- **`claude_code/`** — Document Tools MCP server (FastMCP-based); has its own `CLAUDE.md`

## Environment

Copy `.env.example` to `.env` and fill in credentials:

```
ANTHROPIC_API_KEY=sk-ant-...
CLAUDE_MODEL=claude-sonnet-4-6      # or another model ID
VOYAGE_API_KEY=...                  # optional, for embeddings
```

## Commands

Each subproject has its own virtualenv and `pyproject.toml`.

```bash
# MCP Chat CLI (mcp/)
cd mcp/
uv venv && source .venv/bin/activate
uv pip install -e .
uv run main.py                         # start interactive chat
uv run mcp dev mcp_server.py           # inspect/debug MCP server

# Document Tools MCP Server (claude_code/)
cd claude_code/
uv venv && source .venv/bin/activate
uv pip install -e .
uv run main.py                         # run server
uv run pytest                          # all tests
uv run pytest tests/test_document.py::TestBinaryDocumentToMarkdown::test_binary_document_to_markdown_with_docx  # single test
```

## Architecture

### `mcp/` — MCP Chat CLI

```
CliApp (prompt_toolkit)
  └─ CliChat          resource/command extraction, /cmd and @doc processing
       └─ Chat.run()  agentic message loop, tool-call/result cycle
            ├─ Claude (core/claude.py)      thin Anthropic SDK wrapper, manages message history
            └─ ToolManager (core/tools.py)  aggregates tools from all connected MCP clients
                  └─ MCPClient (mcp_client.py)  stdio transport to each MCP server
```

- `MCPClient` is an async context manager; connects via stdio and exposes tools, resources, and prompts from a server process.
- `ToolManager` maps tool names to the correct `MCPClient` instance and dispatches `call_tool`.
- `Chat.run()` drives the agentic loop: sends messages, detects `tool_use` blocks, invokes `ToolManager`, appends `tool_result` blocks, and loops until a `end_turn` stop reason.
- `CliChat` extends `Chat` with resource injection (`@doc` syntax) and command handling.
- `CliApp` (prompt_toolkit) adds autocomplete for `/commands` and `@resources`, key bindings (`/` and `@` trigger suggestions), and history.

### `claude_code/` — Document Tools MCP Server

FastMCP server exposing document-processing tools. See `claude_code/CLAUDE.md` for details on adding tools, docstring conventions, and test fixture location.
