# Seclai MCP Server

[![MCP](https://img.shields.io/badge/MCP-Compatible-blue)](https://modelcontextprotocol.io)
[![Transport](https://img.shields.io/badge/Transport-Streamable%20HTTP-green)](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports#streamable-http)

Connect AI-powered coding tools to [Seclai](https://seclai.com) using the [Model Context Protocol](https://modelcontextprotocol.io). Manage your agents, knowledge bases, content sources, memory banks, evaluations, solutions, governance, alerts, models, and more directly from your editor or terminal.

## Quick Start

**Endpoint:** `https://mcp.seclai.com/mcp`
**Transport:** Streamable HTTP
**Auth:** browser sign-in (OAuth), or an `X-API-Key` header

You need a Seclai account with API access.

### Sign in from your client (recommended)

The server is an OAuth 2.1 authorization server with [dynamic client
registration](https://datatracker.ietf.org/doc/html/rfc7591) and PKCE, so any
client implementing the MCP authorization spec can register itself and open a
browser sign-in. **No API key to create, paste, or rotate, and no secret sitting
in a config file.**

Add the URL with no `headers` block at all and the client handles the rest:

```json
{
  "mcpServers": {
    "seclai": {
      "type": "streamable-http",
      "url": "https://mcp.seclai.com/mcp"
    }
  }
}
```

Claude Code, Claude Desktop, VS Code, Cursor and Codex CLI all support this
today. If a client reports an expired or invalid token, clear its stored
credentials for the server and reconnect — it will re-register automatically.

<details>
<summary>What the server advertises</summary>

```
GET /.well-known/oauth-protected-resource/mcp
  resource:              https://mcp.seclai.com/mcp
  authorization_servers: https://mcp.seclai.com/

GET /.well-known/oauth-authorization-server
  authorization_endpoint:          /authorize
  token_endpoint:                  /token
  registration_endpoint:           /register     (dynamic client registration)
  grant_types_supported:           authorization_code, refresh_token
  code_challenge_methods_supported: S256
  scopes_supported:                openid
```

</details>

### API key

For clients without OAuth support, or for CI and scripted use, create a key at
[seclai.com](https://seclai.com) → Account Settings → API Keys and send it as
`X-API-Key`:

```json
{
  "mcpServers": {
    "seclai": {
      "type": "streamable-http",
      "url": "https://mcp.seclai.com/mcp",
      "headers": {
        "X-API-Key": "YOUR_API_KEY"
      }
    }
  }
}
```

Keys must be user-scoped; keys created recently are user-scoped automatically.

To target a specific organization, include the `X-Account-Id` header.

> **On the URL.** `https://mcp.seclai.com/mcp` is the canonical resource the
> server advertises in its protected-resource metadata. The bare host also
> routes, but prefer the `/mcp` form — OAuth clients derive the token audience
> from the resource identifier.

### Claude Desktop

Add to your config file:

- **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "seclai": {
      "type": "streamable-http",
      "url": "https://mcp.seclai.com/mcp"
    }
  }
}
```

To pin the connection to one organization, add the `X-Account-Id` header — sign-in
still happens in the browser, so this is the only header you ever need:

```json
{
  "mcpServers": {
    "seclai": {
      "type": "streamable-http",
      "url": "https://mcp.seclai.com/mcp",
      "headers": {
        "X-Account-Id": "YOUR_ACCOUNT_ID"
      }
    }
  }
}
```

Restart Claude Desktop after saving.

### Claude Code (CLI)

```bash
claude mcp add seclai \
  --transport http \
  "https://mcp.seclai.com/mcp"
```

Claude Code opens the browser sign-in on first use. Run `/mcp` inside Claude
Code to check the connection or re-authenticate.

Or copy [`examples/claude-code.json`](examples/claude-code.json) to `.mcp.json` in your project root.

### Cursor

Copy [`examples/cursor.json`](examples/cursor.json) to `.cursor/mcp.json` in your project root.

Or add manually: Settings → MCP → Add new MCP server → set type to `streamable-http` and URL to `https://mcp.seclai.com/mcp`.

### Windsurf

Copy [`examples/windsurf.json`](examples/windsurf.json) to `~/.codeium/windsurf/mcp_config.json`.

### VS Code (GitHub Copilot)

Copy [`examples/vscode.json`](examples/vscode.json) to `.vscode/mcp.json` in your
project, or run **MCP: Open User Configuration** for a user-level install. VS
Code uses `servers` rather than `mcpServers`, and `"type": "http"`:

```json
{
  "servers": {
    "seclai": {
      "type": "http",
      "url": "https://mcp.seclai.com/mcp"
    }
  }
}
```

### Kiro

Copy [`examples/kiro.json`](examples/kiro.json) to `.kiro/settings/mcp.json` in
your project, or `~/.kiro/settings/mcp.json` for all projects.

### Roo Code

Copy [`examples/roo.json`](examples/roo.json) to `.roo/mcp.json` in your project
root. For a global install, use Roo's **MCP Servers → Edit Global MCP** panel.

### Cline

Cline stores MCP settings in `cline_mcp_settings.json` inside its VS Code
extension storage, so edit it through the UI rather than by path: open the Cline
panel → **MCP Servers** → **Configure MCP Servers**, then add the same
`mcpServers` block shown above.

### Gemini CLI

Add to `.gemini/settings.json` in your project, or `~/.gemini/settings.json`
globally. Gemini CLI names the field **`httpUrl`**, not `url`:

```json
{
  "mcpServers": {
    "seclai": {
      "httpUrl": "https://mcp.seclai.com/mcp"
    }
  }
}
```

### Codex CLI

Codex uses TOML at `~/.codex/config.toml`, and can hold the OAuth credentials
itself — see [`examples/codex.toml`](examples/codex.toml):

```toml
[mcp_servers.seclai]
url = "https://mcp.seclai.com/mcp"
auth = "oauth"
```

Then run `codex mcp login seclai` to complete sign-in. On older Codex builds
that only discover stdio servers, add `experimental_use_rmcp_client = true`
above the server block.

### Antigravity

Copy [`examples/antigravity.json`](examples/antigravity.json) to
`~/.gemini/config/mcp_config.json`, or `.agents/mcp_config.json` in your
workspace. Antigravity names the field **`serverUrl`**:

```json
{
  "mcpServers": {
    "seclai": {
      "serverUrl": "https://mcp.seclai.com/mcp"
    }
  }
}
```

### Other Clients

Any client supporting Streamable HTTP MCP transport works. Configure:

| Setting       | Value                                                   |
| ------------- | ------------------------------------------------------- |
| URL           | `https://mcp.seclai.com/mcp`                            |
| Transport     | Streamable HTTP                                         |
| Auth (OAuth)  | none in config — the client registers and signs in      |
| Auth (API key)| `X-API-Key: YOUR_API_KEY`                               |
| Org targeting | `X-Account-Id: YOUR_ACCOUNT_ID` _(optional)_            |

The URL field is not spelled the same everywhere — `url` in most clients,
`httpUrl` in Gemini CLI, `serverUrl` in Antigravity — and VS Code uses `servers`
where everyone else uses `mcpServers`.

## Available Tools

### Agents

| Tool                         | Description                                                   |
| ---------------------------- | ------------------------------------------------------------- |
| `list_agents`                | List all agents in your account with pagination               |
| `get_agent`                  | Get an agent's metadata (name, description, trigger type)     |
| `get_agent_definition`       | Get the full agent definition including steps and `change_id` |
| `create_agent`               | Create a new agent with an optional template                  |
| `update_agent`               | Update an agent's name and/or description                     |
| `update_agent_definition`    | Update an agent's definition with optimistic locking          |
| `delete_agent`               | Delete an agent (cannot be undone)                            |
| `export_agent`               | Export an agent's portable JSON snapshot                       |
| `preview_agent_import`       | Validate an `agent_definition` payload without creating anything |
| `disable_agent`              | Pause an agent so it stops running from every trigger          |
| `enable_agent`               | Re-enable a paused agent                                       |
| `get_agents_calling_agent`   | List live agents that call this one via a `call_agent` step    |

### Agent Runs

| Tool                         | Description                                                   |
| ---------------------------- | ------------------------------------------------------------- |
| `run_agent`                  | Start an agent run                                            |
| `get_agent_run_status`       | Get the status and result of an agent run                     |
| `list_agent_runs`            | List runs for an agent with optional status filter            |
| `cancel_agent_run`           | Cancel an in-flight or queued run by run id                   |

### Agent Inputs

| Tool                             | Description                                               |
| -------------------------------- | --------------------------------------------------------- |
| `get_agent_attachment_references`| What files an agent's definition expects on a run         |
| `upload_agent_input`             | Upload a file to use as input for a subsequent `run_agent` |
| `get_agent_input_upload_status`  | Poll the processing status of an `upload_agent_input`     |

Call `get_agent_attachment_references` before staging uploads — it reports
whether the agent accepts files and the names or patterns a run-time batch must
satisfy.

### Agent AI Assistant

| Tool                         | Description                                                   |
| ---------------------------- | ------------------------------------------------------------- |
| `generate_agent_steps`       | Auto-generate agent steps from a natural-language description |
| `generate_step_config`       | Generate configuration for a specific step type               |
| `get_ai_conversation_history`| Get the AI assistant conversation history for an agent        |
| `mark_ai_suggestion`         | Accept or decline an AI-generated suggestion                  |

### Evaluations

| Tool                              | Description                                                  |
| --------------------------------- | ------------------------------------------------------------ |
| `list_evaluation_criteria`        | List evaluation criteria for an agent                        |
| `get_evaluation_criteria`         | Get details of a specific evaluation criteria                |
| `create_evaluation_criteria`      | Create a new evaluation criteria                             |
| `update_evaluation_criteria`      | Update an existing evaluation criteria                       |
| `delete_evaluation_criteria`      | Delete an evaluation criteria                                |
| `get_evaluation_summary`          | Get summary statistics for an evaluation criteria            |
| `get_non_manual_evaluation_summary` | Get summary of non-manual evaluations for an agent         |
| `list_evaluation_results`         | List results for an evaluation criteria                      |
| `list_agent_evaluation_results`   | List all evaluation results for an agent                     |
| `list_run_evaluation_results`     | List evaluation results for a specific run                   |
| `create_evaluation_result`        | Create a manual evaluation result                            |
| `run_evaluation`                  | Run automated evaluation on a criteria                       |
| `test_draft_evaluation`           | Test an evaluation criteria without persisting               |
| `opt_evaluation_into_review`      | Move an evaluation result into the review queue              |

### Knowledge Bases

| Tool                         | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| `list_knowledge_bases`       | List knowledge bases in your account with pagination         |
| `get_knowledge_base`         | Get a knowledge base's details including its linked sources  |
| `create_knowledge_base`      | Create a new knowledge base with at least one source         |
| `update_knowledge_base`      | Update a knowledge base's name, description, or sources      |
| `delete_knowledge_base`      | Delete a knowledge base (fails if referenced by agents)      |
| `generate_kb_plan`           | AI-generate a knowledge base configuration plan              |

### Knowledge Base Chat

| Tool                         | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| `chat_with_knowledge_base`   | Chat with a knowledge base using natural language            |
| `list_kb_conversations`      | List conversations for a knowledge base                      |
| `load_kb_chat_messages`      | Load messages for a knowledge base conversation              |
| `rename_kb_conversation`     | Rename a knowledge base conversation                         |
| `delete_kb_conversation`     | Delete a knowledge base conversation                         |

### Content Sources

| Tool                              | Description                                                  |
| --------------------------------- | ------------------------------------------------------------ |
| `list_sources`                    | List all content sources in your account with pagination     |
| `get_source`                      | Get detailed information about a content source              |
| `create_source`                   | Create a new content source                                  |
| `update_source`                   | Update a source's name, polling interval, or retention       |
| `delete_source`                   | Delete a content source                                      |
| `get_source_content_text`         | Get the text content of a source item                        |
| `list_account_source_content`     | List all source content in the account                       |
| `list_source_pulls`               | List pull (sync) operations for a source                     |
| `get_source_pull_errors`          | Get errors from a source pull operation                      |
| `generate_source_plan`            | AI-generate a source configuration plan                      |
| `upload_source_content`           | Upload an image/audio/video/PDF/text file to a custom index  |

### Source Exports

| Tool                         | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| `list_source_exports`        | List exports for a source                                    |
| `get_source_export`          | Get details of a source export                               |
| `create_source_export`       | Create a new source export                                   |
| `cancel_source_export`       | Cancel a running source export                               |
| `download_source_export`     | Download a completed source export                           |
| `estimate_source_export`     | Estimate the size/cost of a source export                    |

### Source Embedding Migrations

| Tool                              | Description                                                  |
| --------------------------------- | ------------------------------------------------------------ |
| `get_source_embedding_migration`  | Get status of an embedding migration                         |
| `start_source_embedding_migration`| Start a new embedding migration                              |
| `cancel_source_embedding_migration`| Cancel a running embedding migration                        |

### Memory Banks

| Tool                              | Description                                                  |
| --------------------------------- | ------------------------------------------------------------ |
| `list_memory_banks`               | List memory banks with pagination                            |
| `get_memory_bank`                 | Get a memory bank's details                                  |
| `create_memory_bank`              | Create a new memory bank (conversation or general)           |
| `update_memory_bank`              | Update a memory bank's name or configuration                 |
| `delete_memory_bank`              | Delete a memory bank                                         |
| `get_memory_bank_stats`           | Get usage statistics for a memory bank                       |
| `get_agents_using_memory_bank`    | List agents that reference a memory bank                     |
| `compact_memory_bank_now`         | Trigger compaction of a memory bank                          |
| `delete_memory_bank_source`       | Delete the source backing a memory bank                      |
| `list_memory_bank_templates`      | List available memory bank templates                         |
| `test_compaction_prompt`          | Test a compaction prompt without applying                    |

### Memory Bank AI

| Tool                              | Description                                                  |
| --------------------------------- | ------------------------------------------------------------ |
| `generate_memory_bank_config`     | AI-generate a memory bank configuration                      |
| `get_memory_bank_ai_conversation` | Get the last AI conversation for a memory bank               |
| `accept_memory_bank_suggestion`   | Accept an AI-generated memory bank suggestion                |

### Solutions

| Tool                         | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| `list_solutions`             | List solutions with pagination                               |
| `get_solution`               | Get a solution's details                                     |
| `create_solution`            | Create a new solution                                        |
| `update_solution`            | Update a solution's name or description                      |
| `delete_solution`            | Delete a solution                                            |
| `link_solution_resources`    | Link agents, KBs, or sources to a solution                   |
| `unlink_solution_resources`  | Unlink resources from a solution                             |
| `generate_solution_plan`     | AI-generate a solution configuration plan                    |
| `accept_solution_plan`       | Accept an AI-generated solution plan                         |
| `decline_solution_plan`      | Decline an AI-generated solution plan                        |

### Governance

| Tool                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `get_governance_settings`             | Get current governance settings                              |
| `update_governance_settings`          | Enable/disable governance or update settings                 |
| `list_governance_settings`            | List all governance settings                                 |
| `list_governance_policies`            | List all governance policies                                 |
| `get_governance_policy`               | Get a specific governance policy                             |
| `create_governance_policy`            | Create a new governance policy                               |
| `update_governance_policy`            | Update a governance policy                                   |
| `list_governance_policy_documents`    | Browse available sample policy documents                     |
| `get_governance_policy_document`      | Get a specific sample policy document                        |
| `get_governance_policy_usage`         | Get usage stats for a policy                                 |
| `list_governance_policy_changes`      | List change history for a policy                             |
| `list_governance_evaluations`         | List governance evaluations (filter by verdict)              |
| `resolve_governance_evaluation`       | Resolve a flagged governance evaluation                      |
| `bulk_resolve_governance_evaluations` | Resolve multiple evaluations at once                         |
| `test_draft_governance_policy`        | Test a policy draft without persisting                       |
| `test_governance_policy`              | Test a policy and persist the result                         |
| `set_policy_knowledge_bases`          | Link knowledge bases to a policy for RAG evidence            |
| `list_policy_knowledge_bases`         | List knowledge bases linked to a policy                      |
| `get_circular_knowledge_bases`        | Check for circular KB references before linking              |
| `get_governance_stats`                | Get aggregate governance metrics                             |
| `get_governance_usage`                | Get governance usage details                                 |
| `get_governance_credit_estimates`     | Estimate governance credit costs                             |
| `list_governance_audit_trail`         | List governance audit trail entries                          |
| `list_governance_conversations`       | List governance AI conversations                             |
| `generate_governance_plan`            | AI-generate a governance plan from natural language           |
| `accept_governance_plan`              | Accept an AI-generated governance plan                       |
| `decline_governance_plan`             | Decline an AI-generated governance plan                      |
| `delete_governance_policy`            | Soft-delete a governance policy                              |
| `review_governance_coverage`          | Generate a structured policy-coverage review for a scope     |
| `list_governance_redactor_chunks`     | Per-chunk audit records under a parent REDACT evaluation     |

### Alerts

| Tool                                    | Description                                                |
| --------------------------------------- | ---------------------------------------------------------- |
| `list_alerts`                           | List alerts, filterable by status, agent or source connection |
| `get_alert`                             | Get details of a specific alert                            |
| `change_alert_status`                   | Change an alert's status (e.g. resolve)                    |
| `add_alert_comment`                     | Add a comment to an alert                                  |
| `list_alert_configs`                    | List alert configurations                                  |
| `get_alert_config`                      | Get a specific alert configuration                         |
| `create_alert_config`                   | Create a new alert configuration                           |
| `update_alert_config`                   | Update an alert configuration                              |
| `delete_alert_config`                   | Delete an alert configuration                              |
| `list_organization_alert_preferences`   | List org-level alert preferences                           |
| `update_organization_alert_preference`  | Update an org-level alert preference                       |

### Models

| Tool                         | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| `list_models`                | List models grouped by provider with optional filters        |
| `get_model`                  | Get full details for a specific model                        |
| `get_model_recommendations`  | Get recommendations for a model                              |
| `list_model_alerts`          | List model-related alerts                                    |
| `dismiss_model_alert`        | Dismiss a model alert                                        |
| `dismiss_all_model_alerts`   | Dismiss all model alerts                                     |
| `list_generation_tiers`      | Media-generation quality tiers per modality, with cost       |
| `list_managed_providers`     | Seclai-managed OAuth providers configured on this deployment |

### Playground Experiments

| Tool                         | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| `list_experiments`           | List model playground experiments                            |
| `get_experiment`             | Get a specific experiment                                    |
| `run_experiment`             | Run a model playground experiment                            |
| `cancel_experiment`          | Cancel a running experiment                                  |

### Account & Organization

| Tool                              | Description                                                  |
| --------------------------------- | ------------------------------------------------------------ |
| `get_account_credit_usage`        | Get credit usage for the account                             |
| `get_account_llm_calls_summary`   | Get LLM call summary for the account                        |
| `list_organizations`              | List organizations the user belongs to                       |
| `switch_organization`             | Switch to a different organization                           |

### Resource Exports

| Tool                         | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| `list_resource_exports`      | List resource exports                                        |
| `get_resource_export`        | Get a specific resource export                               |
| `create_resource_export`     | Create a resource export                                     |
| `cancel_resource_export`     | Cancel a resource export                                     |
| `download_resource_export`   | Download a completed resource export                         |

### Transfers

| Tool                         | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| `list_transfer_targets`      | List available transfer targets                              |
| `preview_transfer`           | Preview a resource transfer between accounts                 |
| `execute_transfer`           | Execute a resource transfer                                  |

### Agent Email — Domains

| Tool                        | Description                                                    |
| --------------------------- | -------------------------------------------------------------- |
| `list_email_domains`        | The account's vanity and custom agent-email domains            |
| `add_email_domain`          | Add and provision a domain; returns the DNS records to publish |
| `verify_email_domain`       | Re-check SES and DNS verification right now                    |
| `set_primary_email_domain`  | Make a verified domain the account's primary sending domain    |
| `use_shared_email_domain`   | Revert to the shared `agent.seclai.com` domain                 |
| `send_email_domain_test`    | Send a test message from a verified domain to the account owner|
| `get_email_domain_dmarc`    | DMARC aggregate-report summary for a domain                    |
| `remove_email_domain`       | Remove a domain and tear down its SES identity and DNS         |

### Agent Email — Inbound

| Tool                          | Description                                                  |
| ----------------------------- | ------------------------------------------------------------ |
| `set_email_trigger_config`    | Set an `EMAIL_RECEIVED` trigger's alias and sender allowlist  |
| `list_blocked_email_senders`  | Senders blocked from triggering `EMAIL_RECEIVED` agents       |
| `block_email_sender`          | Block a sender or domain (idempotent)                         |
| `unblock_email_sender`        | Remove a sender from the blocklist                            |
| `set_email_auto_block_mode`   | How governance BLOCK verdicts feed the blocklist              |
| `list_inbound_email_rejections`| Inbound mail discarded without running an agent, and why     |
| `get_inbound_email_status`    | Quota usage and whether the account-wide breaker has tripped  |
| `cancel_queued_email_runs`    | Fail all over-quota parked inbound-email runs at once         |
| `resume_inbound_email`        | Lift the account-wide inbound-email pause                     |
| `list_agent_email_optouts`    | Recipients who opted out of this account's agent email        |
| `remove_agent_email_optout`   | Revoke an opt-out, letting the recipient receive mail again   |

### MCP Clients

Seclai agents can call *other* MCP servers. These tools manage those saved
connections — the Seclai server acting as an MCP client itself.

| Tool                        | Description                                                    |
| --------------------------- | -------------------------------------------------------------- |
| `list_mcp_catalog`          | Browse popular external MCP servers you can add                |
| `list_mcp_clients`          | The account's saved external-MCP configurations                |
| `get_mcp_client`            | Get a single MCP client by id                                  |
| `create_mcp_client`         | Save a configuration for an external MCP server                |
| `update_mcp_client`         | Update an existing MCP client                                  |
| `delete_mcp_client`         | Soft-delete an MCP client                                      |
| `test_mcp_client_connection`| Connect and list the external server's tools                   |
| `refresh_mcp_client_tools`  | Re-list and persist the external server's tool catalog         |
| `describe_mcp_client_tools` | The cached tool catalog for one client, in compact form        |
| `disconnect_mcp_client`     | Revoke a connected OAuth client's stored tokens, keeping it    |
| `list_mcp_client_calls`     | Tool calls your agents made to this client during runs         |
| `get_agents_using_mcp_client`| Agents whose workflow references this MCP client               |

### Human-in-the-Loop

| Tool                        | Description                                                    |
| --------------------------- | -------------------------------------------------------------- |
| `list_pending_hitl_requests`| HITL requests in the account, paginated                        |
| `get_hitl_request`          | One request with its status, recipients and votes              |
| `vote_on_hitl_request`      | Submit the calling user's vote on a pending request            |
| `cancel_hitl_request`       | Cancel a pending request                                       |

### Cloud Drives

| Tool                          | Description                                                  |
| ----------------------------- | ------------------------------------------------------------ |
| `list_drive_providers`        | Providers whose OAuth app is configured on this deployment   |
| `list_cloud_drives`           | The account's cloud-drive connections                        |
| `get_agents_using_cloud_drive`| Agents whose workflow references this connection             |

### Search

| Tool                         | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
| `search`                     | Search across all resource types in the account              |
| `search_docs`                | Search the Seclai product documentation by content           |

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
| Rate limit exceeded | Wait the indicated seconds, or upgrade your plan |
| Tools not appearing | Restart client, verify URL and `streamable-http` transport |
| `invalid_token` | For OAuth clients, clear stored credentials for the server and reconnect. For API keys, confirm the key is current and user-scoped |
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
