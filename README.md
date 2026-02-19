# Seclai MCP Server

[![MCP](https://img.shields.io/badge/MCP-Compatible-blue)](https://modelcontextprotocol.io)
[![Transport](https://img.shields.io/badge/Transport-Streamable%20HTTP-green)](https://modelcontextprotocol.io/specification/2025-03-26/basic/transports#streamable-http)

Connect AI-powered coding tools to [Seclai](https://seclai.com) using the [Model Context Protocol](https://modelcontextprotocol.io). Manage your agents, knowledge bases, and content sources directly from your editor or terminal.

## Quick Start

**Endpoint:** `https://api.seclai.com/mcp`
**Transport:** Streamable HTTP
**Auth:** `X-API-Key` header

You need a Seclai account with API access and a user-scoped API key. Create one at [seclai.com](https://seclai.com) → Account Settings → API Keys.

### Claude Desktop

Add to your config file:

- **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "seclai": {
      "type": "streamable-http",
      "url": "https://api.seclai.com/mcp",
      "headers": {
        "X-API-Key": "YOUR_API_KEY"
      }
    }
  }
}
```

Restart Claude Desktop after saving.

### Claude Code (CLI)

```bash
claude mcp add seclai \
  --transport streamable-http \
  "https://api.seclai.com/mcp" \
  --header "X-API-Key: YOUR_API_KEY"
```

Or copy [`examples/claude-code.json`](examples/claude-code.json) to `.mcp.json` in your project root.

### Cursor

Copy [`examples/cursor.json`](examples/cursor.json) to `.cursor/mcp.json` in your project root.

Or add manually: Settings → MCP → Add new MCP server → set type to `streamable-http` and URL to `https://api.seclai.com/mcp`.

### Windsurf

Copy [`examples/windsurf.json`](examples/windsurf.json) to `~/.codeium/windsurf/mcp_config.json`.

### Other Clients

Any client supporting Streamable HTTP MCP transport works. Configure:

| Setting     | Value                          |
| ----------- | ------------------------------ |
| URL         | `https://api.seclai.com/mcp`  |
| Transport   | Streamable HTTP                |
| Header      | `X-API-Key: YOUR_API_KEY`      |

## Available Tools

### Agents

| Tool                       | Description                                                   |
| -------------------------- | ------------------------------------------------------------- |
| `list_agents`              | List all agents in your account with pagination               |
| `get_agent`                | Get an agent's metadata (name, description, trigger type)     |
| `get_agent_definition`     | Get the full agent definition including steps and `change_id` |
| `create_agent`             | Create a new agent with an optional template                  |
| `update_agent`             | Update an agent's name and/or description                     |
| `update_agent_definition`  | Update an agent's definition with optimistic locking          |
| `delete_agent`             | Delete an agent (cannot be undone)                            |

### Knowledge Bases

| Tool                       | Description                                                  |
| -------------------------- | ------------------------------------------------------------ |
| `list_knowledge_bases`     | List knowledge bases in your account with pagination         |
| `get_knowledge_base`       | Get a knowledge base's details including its linked sources  |
| `create_knowledge_base`    | Create a new knowledge base with at least one source         |
| `update_knowledge_base`    | Update a knowledge base's name, description, or sources      |
| `delete_knowledge_base`    | Delete a knowledge base (fails if referenced by agents)      |

### Content Sources

| Tool                       | Description                                                  |
| -------------------------- | ------------------------------------------------------------ |
| `list_sources`             | List all content sources in your account with pagination     |
| `get_source`               | Get detailed information about a content source              |
| `update_source`            | Update a source's name, polling interval, or retention       |

### Resources

| Resource                 | URI                                    | Description                                  |
| ------------------------ | -------------------------------------- | -------------------------------------------- |
| Agent Definition Schema  | `seclai://schema/agent-definition`     | Full schema reference for agent definitions  |
| String Substitutions     | `seclai://schema/string-substitutions` | All supported string substitution expressions|

## Usage Examples

Once connected, interact with Seclai through natural language:

> _"Show me all my Seclai agents"_

> _"Create a new agent called 'Daily News Digest' with a schedule trigger and the summarizer template"_

> _"Add a retrieval step before the display step in my Daily News Digest agent"_

> _"Create a knowledge base called 'Podcast Archive' using my podcast sources"_

> _"Show me all my content sources and change the news feed to poll hourly"_

## Agent Templates

When creating agents, specify a template for a pre-configured starting point:

| Template                   | Description                                |
| -------------------------- | ------------------------------------------ |
| `blank`                    | Empty agent with no steps                  |
| `retrieval_example`        | Demonstrates knowledge base retrieval      |
| `simple_qa`               | Basic question-answering agent             |
| `summarizer`              | Summarizes content from sources            |
| `json_extractor`          | Extracts structured data from content      |
| `content_change_notifier` | Monitors sources for changes and notifies  |
| `scheduled_report`        | Generates reports on a schedule            |
| `webhook_pipeline`        | Processes incoming webhook data            |

## Rate Limits

MCP calls are rate-limited per account based on your plan:

| Plan       | Calls / Minute |
| ---------- | -------------- |
| Free       | No MCP access  |
| Starter    | 0              |
| Pro        | 30             |
| Team       | 75             |
| Enterprise | 300            |

Exceeded calls return a `retry_after` value. Most AI assistants handle this automatically.

## Troubleshooting

| Error | Fix |
| ----- | --- |
| Invalid API key | Verify key, check account standing, confirm API access on plan |
| MCP requires a user-scoped API key | Re-create your key — new keys are user-scoped automatically |
| Rate limit exceeded | Wait the indicated seconds, or upgrade your plan |
| Tools not appearing | Restart client, verify URL and `streamable-http` transport |
| Missing X-API-Key header | Check header config in your MCP client settings |
| Conflict on definition update | Retry — your assistant will re-read and re-apply |

## Links

- [Full MCP Documentation](https://seclai.com/docs/mcp)
- [API Keys](https://seclai.com/docs/api-keys)
- [Agent Concepts](https://seclai.com/docs/agents)
- [Agent Steps Reference](https://seclai.com/docs/agent-steps)
- [REST API](https://seclai.com/docs/api-introduction)
- [seclai.com](https://seclai.com)

## License

MIT
