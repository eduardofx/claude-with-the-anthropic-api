# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Setup
uv venv && source .venv/bin/activate
uv pip install -e .

# Run MCP server
uv run main.py

# Run tests
uv run pytest

# Run a single test
uv run pytest tests/test_document.py::TestBinaryDocumentToMarkdown::test_binary_document_to_markdown_with_docx
```

## Architecture

This is a **Document Tools MCP Server** — a Python package that exposes document-processing tools via the [Model Context Protocol](https://modelcontextprotocol.io/) using the FastMCP framework.

**Flow:**

```
main.py  →  FastMCP("docs")  →  mcp.run()
              ↑
         @mcp.tool() decorators register functions from tools/
```

**Adding a new tool:** create a function in `tools/`, import it in `main.py`, and decorate it with `@mcp.tool()`. Tool parameters use Pydantic `Field` with descriptions that surface directly in the MCP interface — these descriptions are important for usability.

Tool docstrings follow a specific convention: one-line summary, detailed explanation, when-to-use / when-not-to-use guidance, and usage examples with expected outputs. Follow this pattern when adding new tools.

Test fixtures (`.docx`, `.pdf`) live in `tests/fixtures/`.
