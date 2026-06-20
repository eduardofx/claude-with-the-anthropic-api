# Document Tools

A Python package implementing a variety of document-related tools for converting and processing document formats. These tools are exposed through an MCP server interface for seamless integration with AI assistants.

## Overview

**Claude Code** has an MCP (Model Context Protocol) client built right into it, allowing you to connect MCP servers to dramatically expand what Claude can do. This project is an example of such a server — a **Document Tools MCP Server** that gives Claude the ability to read and convert PDF and Word documents.

### How MCP Extends Claude

MCP allows Claude Code to connect to external services and tools through custom servers. Instead of being limited to Claude's built-in capabilities, you can add functionality by connecting servers that expose:

- **Tools** — actions Claude can take (e.g., converting a `.docx` file to Markdown)
- **Prompts** — reusable templates for common tasks
- **Resources** — data sources Claude can read from

### Registering This Server with Claude Code

```bash
claude mcp add documents uv run main.py
```

Once registered, Claude Code will automatically connect to this server on startup. You can then ask Claude things like:

> *"Convert the tests/fixtures/mcp_docs.docx file to Markdown"*

…and it will use the `document_path_to_markdown` tool provided by this server.

### Popular MCP Integrations

This project is part of a broader MCP ecosystem. Other popular integrations include:

| Server | Capability |
|---|---|
| `sentry-mcp` | Automatically discover and fix bugs logged in Sentry |
| `playwright-mcp` | Browser automation for testing and troubleshooting |
| `figma-context-mcp` | Expose Figma designs to Claude |
| `mcp-atlassian` | Access Confluence and Jira |
| `firecrawl-mcp-server` | Web scraping capabilities |
| `slack-mcp` | Post messages or reply to Slack threads |

By combining multiple MCP servers, you can build a development environment where Claude seamlessly works with all the tools and services you already use.

## Setup

```bash
# Create a virtual env and activate it
uv venv
source .venv/bin/activate

# Install the package in development mode
uv pip install -e .
```

## Running

```bash
# Start the MCP server
uv run main.py
```

## Testing

```bash
# Run all tests
uv run pytest
```

## Development

### Tool Definitions

Tools are defined as Python functions and registered with the MCP server:

```python
mcp.tool()(my_function)
```

Tool descriptions should:

- Begin with a one-line summary
- Provide detailed explanation of functionality
- Explain when to use (and not use) the tool
- Include usage examples with expected input/output

Use `Field` from pydantic for parameter descriptions:

```python
from pydantic import Field

def my_tool(
    param1: str = Field(description="Detailed description of this parameter"),
    param2: int = Field(description="Explain what this parameter does")
) -> ReturnType:
    """Comprehensive docstring here"""
    # Implementation
```
