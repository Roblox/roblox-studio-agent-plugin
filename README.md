# Roblox Studio

Roblox's official plugin for AI assistants. Build, script, playtest, and debug your Roblox experiences by talking to your assistant, connected live to Roblox Studio.

Available for **Claude** (Claude Code and Cowork) and **ChatGPT** (the ChatGPT desktop app and Codex).

## Install

**Claude Code:** run `/plugin`, search for **Roblox Studio**, and install it.

**Claude desktop app (Cowork):** go to **Customize → Plugins**, find **Roblox Studio**, and install it.

**ChatGPT desktop app:** open **Plugins**, find **Roblox Studio**, and install it. Then start a new chat.

**Codex CLI:**

```bash
codex plugin marketplace add Roblox/roblox-studio-agent-plugin
```

Then open `/plugins`, install **Roblox Studio**, and start a new thread with `/new`.

### Get connected

Ask your assistant:

> "Get me set up with Roblox Studio."

It checks what's missing and asks you once. Then it installs and opens Studio if needed. You sign in, open a place, and turn on Studio's MCP server with one switch, and it tells you exactly where. If you need to start a new chat partway through, say **"continue Roblox setup"** to pick up where you left off.

### Verify it worked

Ask "list my open Roblox Studio windows". Your assistant should name the place you have open.

- **Claude Code:** `/mcp` lists `plugin:roblox-studio:macos` and `plugin:roblox-studio:windows`. Only the one for your OS connects, so the other showing as failed is expected.
- **Codex:** `codex mcp list` shows `Roblox_Studio`.

### Manual setup

If you'd rather set up Studio yourself:

1. [Install Roblox Studio](https://create.roblox.com/docs/studio/setup), sign in, and open a place.
2. Open **Assistant**, select **… → Settings → MCP Servers** (**Manage MCP Servers** in older versions), and turn on **Enable Studio as MCP server**.
3. **ChatGPT and Codex only:** in the same pane, under **Quick Connect**, turn on **ChatGPT (codex)**. Then restart ChatGPT or open a new Codex thread.

## Usage

> **Before you start:** your assistant changes your place directly. It edits scripts, adds and removes objects, and runs Luau in Studio. Not every change can be undone from Studio, so save or publish a version of your place before asking for bigger changes.

Once you're connected, just describe what you want:

> "I've never used Roblox Studio. Get me set up so you can help me build a game."
>
> "Look at my game and tell me how the round system works."
>
> "My coin pickup doesn't add to the leaderboard. Playtest it, find the bug, and fix it."
>
> "Add a lobby with four spawn pads and a sign that shows the player count, then playtest it."

For Roblox-specific know-how, your assistant uses the skills built into your version of Studio.

<details>
<summary>Studio tools your assistant can use</summary>

**Read-only:** `list_roblox_studios`, `get_studio_state`, `get_console_output`, `script_read`, `script_search`, `script_grep`, `search_game_tree`, `inspect_instance`, `screen_capture`, `http_get` (Roblox documentation only), `skill`, `search_asset`, `wait_job_finished`, `store_image`.

**Change your place or account:** `execute_luau`, `multi_edit`, `insert_asset`, `generate_mesh`, `generate_material`, `generate_procedural_model`, `generate_texture`, `segment_mesh`, `upload_image`, `start_stop_play`, `character_navigation`, `user_keyboard_input`, `user_mouse_input`, `subagent`.

</details>

## Works with

- Roblox Studio on macOS or Windows
- Claude Code, or Cowork in the Claude desktop app
- The ChatGPT desktop app (macOS or Windows), or the Codex CLI

Studio's MCP server runs on your computer, so claude.ai on the web and ChatGPT web and mobile can help you plan and write Luau, but can't connect to Studio.

## What this plugin runs and sends

The plugin contains no code of its own, only configuration and skill instructions.

- **Runs** Roblox Studio's own MCP server, installed with Studio: `StudioMCP` in `RobloxStudio.app` on macOS (in `/Applications` or `~/Applications`), or `%LOCALAPPDATA%\Roblox\mcp.bat` on Windows. Claude starts it through `/bin/sh` or `cmd.exe`. In ChatGPT and Codex, setup registers it as `Roblox_Studio`.
- **During setup, after one approval from you,** it may:
  - download the Studio installer from `setup.rbxcdn.com`, check Roblox's signature, and run it
  - open Studio
  - in ChatGPT and Codex, register the `Roblox_Studio` server

  You turn on Studio's MCP server yourself; it doesn't edit Studio's settings files. It asks whether you're signed in rather than checking, never closes Studio itself, and never handles your password. [Security notes](skills/roblox-get-started/SECURITY.md) explain each step in detail.
- **Sends to Roblox,** under your Studio account, only when your assistant uses these tools: Creator Store search and insert, AI generation, image uploads, and Roblox documentation fetches.
- **Sends to your AI provider** whatever tool results your assistant reads, such as scripts, properties, console output, and screenshots, as part of your conversation.
- **Collects nothing itself:** no telemetry, and no stored data.

## Privacy Policy

This plugin is published by Roblox Corporation. Data handled by Roblox Studio and Roblox services is covered by the [Roblox Privacy and Cookie Policy](https://www.roblox.com/info/privacy). Conversation data is covered by your AI provider's policy: [Anthropic's Privacy Policy](https://www.anthropic.com/legal/privacy) for Claude, or [OpenAI's Privacy Policy](https://openai.com/policies/privacy-policy) for ChatGPT and Codex.

## Issues and feedback

- Docs: [Roblox Studio MCP server](https://create.roblox.com/docs/studio/mcp)
- Bugs and feedback: [Roblox Developer Forum](https://devforum.roblox.com)

## License

MIT. See [LICENSE](LICENSE).
