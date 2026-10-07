---
type: llm
---

PASS if the reply helps the user connect in Claude: for example, opening a place in Studio, turning on "Enable Studio as MCP server", or reconnecting the plugin's server with /mcp. It must not tell the user to register or add a Studio MCP server, or to use anything specific to another app.
FAIL if the reply tells the user to add or register an MCP server (for example "Roblox_Studio"), turn on Quick Connect for "ChatGPT (codex)", run `codex` commands, edit `config.toml`, restart ChatGPT, or open a new Codex thread.
