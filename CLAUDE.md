# CLAUDE.md

This repo documents the hosted Seclai MCP server and holds one example config per
client. The server itself lives in `seclai/seclai`
(`backend/api/src/api/routers/mcp_agents.py`).

- **`.claude/skills/**` is vendored from `seclai/sdk-tools` with `git subtree`.**
  Do not edit or review it here. Fixes and review findings on it belong in
  sdk-tools, whose CLAUDE.md has the rules for that code; they arrive here with
  the next `./sync.sh`.
- **The README's tool tables mirror the server's registered tools.** Check tool
  names against the server's `@mcp.tool(name=...)` list, and each one-line
  description against that tool's own description.
- **Each client spells its config differently** (`url`, `httpUrl` or `serverUrl`;
  `mcpServers` or `servers`; transport type names). A client's section and its
  `examples/` file are the source of truth for that client. Check the client's
  current docs before changing one, and cite them in the review thread.
- **Review findings** need a concrete failure: the client, the config a reader
  copies, and what goes wrong. "This might not work on an older version" is not
  a finding without a version and a source.
