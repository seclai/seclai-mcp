# Copilot Instructions — seclai-mcp

## Overview

This repo contains documentation and client configuration examples for the hosted Seclai MCP server at `https://api.seclai.com/mcp`. It does not contain server source code — the MCP server runs as part of the main Seclai API.

## Key Rules

- Configuration examples live in `examples/` (Claude Desktop, Cursor, Windsurf, Claude Code).
- Transport is Streamable HTTP, not stdio.
- Auth uses `X-API-Key` header in all example configs.
- When the API adds new MCP tools, update the README tool inventory accordingly.
- `.github/copilot-instructions.md` shares common sections (git rules, editing rules, self-correction rules) across all SDK repos. When updating shared rules, apply the same change to all repos: `seclai-python`, `seclai-javascript`, `seclai-go`, `seclai-csharp`, `seclai-cli`, `seclai-mcp`.

## Git rules

- **NEVER use `git stash`.** Use `git diff`, `git log`, or `git show` instead.
- Do not run `git checkout` to switch branches, `git reset`, or any other destructive git operation without explicit user approval.

## Editing rules

- Do not use CLI text tools (sed/awk). Use the editor-based patch tool.

## Self-correction rules

- **NEVER promise to "do better" without updating these instruction files.** If a recurring mistake is identified, edit this file with a concrete rule that prevents the mistake. Do that FIRST, then continue work.
