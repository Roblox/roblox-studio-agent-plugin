# Connect Studio to this app

This file has one section per app. **Follow only the section for the app you're running in, and ignore the others.** Commands and settings from another app's section don't work here and can break the connection, for example by adding a duplicate server.

- Claude Code, Cowork, or claude.ai: read **Claude**.
- ChatGPT or Codex: read **ChatGPT and Codex**.

---

## Claude

### Registration

This plugin registers Studio's MCP server itself, so there's nothing to add by hand. **Never register or add another Studio MCP server in Claude.** The plugin has two entries, and only the one for the user's OS connects. The other always shows as failed, and that's expected:

- `plugin:roblox-studio:macos`
- `plugin:roblox-studio:windows`

### Loading the tools

The server starts when the session starts. If Studio wasn't installed at that point, the tools aren't loaded yet:

- **Claude Code:** have the user run `/mcp` and reconnect the server for their OS. If that doesn't work, have them start a new session and say **continue Roblox setup**.
- **Cowork:** have the user start a new task and say **continue Roblox setup**.

If the user asked for more than setup, such as making a game, and has to start a new session or task, ask them to repeat that request there. Don't say setup is finished while the tools are still missing.

### Where this can run

- **Claude Code** on the user's Mac or PC can run the commands in `install.md` directly.
- **Cowork** may run commands in a sandbox that can't reach the user's computer. If a check shows a Linux machine (`uname` prints `Linux`), or a command can't see the user's files, don't run `install.md`. Walk the user through each step instead.
- **claude.ai chat** can't run Studio's local server. Say so, and keep helping without Studio.

### Duplicates

If the Studio tools appear twice, the user probably also added Studio's server by hand, for example as `Roblox_Studio`. In Claude Code, check with `claude mcp list`. If there's a Studio entry that isn't from this plugin, offer to remove it, with approval:

```sh
claude mcp remove Roblox_Studio
```

Then have the user start a new session.

---

## ChatGPT and Codex

Studio ships its own MCP server. Registering it once covers both the ChatGPT desktop app and the Codex CLI, because they share `~/.codex/config.toml` (`$CODEX_HOME/config.toml` if set; on Windows `%USERPROFILE%\.codex\config.toml`).

Always name the server `Roblox_Studio`, the name Studio's Quick Connect writes. Check before adding, and never add a second one.

### Registration

First check whether it's already registered. It is if Studio tools are in this chat, if `codex mcp list` shows `Roblox_Studio`, or if `config.toml` has `[mcp_servers.Roblox_Studio]`.

If it isn't registered, use the first option that works. Include it in the skill's one approval.

**Option A: Quick Connect (main path).** This is in the same Studio pane as the MCP setting, so the user can do both in one visit:

1. With a place open, open **Assistant**, select **⋯ → Settings**, then **MCP Servers**.
2. Make sure **Enable Studio as MCP server** is on.
3. Under **Quick Connect**, turn on **ChatGPT (codex)**.

If **ChatGPT (codex)** isn't listed, use option B or C. Don't create `~/.codex/tmp` to make it appear.

**Option B: ChatGPT desktop, manual add.**

1. Open ChatGPT **Settings → MCP servers → Add server**.
2. Name: `Roblox_Studio`.
3. Command, macOS: `/Applications/RobloxStudio.app/Contents/MacOS/StudioMCP`, or `~/Applications/RobloxStudio.app/...` if Studio is installed there.
4. Command, Windows: `cmd.exe`, with arguments `/c` and `cd /d %LOCALAPPDATA%\Roblox && .\mcp.bat`.
5. Save, restart the app, and open a new chat.

**Option C: Codex CLI.**

macOS:

```zsh
APP=/Applications/RobloxStudio.app; [ -d "$APP" ] || APP="$HOME/Applications/RobloxStudio.app"
codex mcp add Roblox_Studio -- "$APP/Contents/MacOS/StudioMCP"
```

Windows (PowerShell):

```powershell
codex mcp add Roblox_Studio '--' cmd.exe /c 'cd /d %LOCALAPPDATA%\Roblox && .\mcp.bat'
```

Keep the quotes around `'--'`. If `codex` is installed as a PowerShell script shim, PowerShell drops a bare `--` before Codex sees it. `mcp.bat` must run from its own folder, which is why the command changes into `%LOCALAPPDATA%\Roblox` first.

Run `codex mcp list` again and confirm the entry.

**Equivalent `config.toml`.** Use this only if Quick Connect and the CLI are both unavailable. Edit the existing file and keep every other entry.

```toml
# macOS
[mcp_servers.Roblox_Studio]
command = "/Applications/RobloxStudio.app/Contents/MacOS/StudioMCP"

# Windows
[mcp_servers.Roblox_Studio]
command = "cmd.exe"
args = ["/c", "cd /d %LOCALAPPDATA%\\Roblox && .\\mcp.bat"]
```

### Loading the tools

Tools only load at the start of a chat. Once the server is registered, if there are no Studio tools in this chat, tell the user:

> Restart ChatGPT (or open a new chat / new Codex thread) and say **continue Roblox setup**.

If the user asked for more than setup, ask them to repeat that request there. Don't say setup is finished while the tools are still missing.

### Where this can run

- **ChatGPT desktop (Mac or Windows) and the Codex CLI** can reach a local Studio. Use the commands in `install.md` if this surface has a shell. Without one, ask the user instead of checking, and walk them through each step.
- **Codex's sandbox** can block network access, writes outside the workspace, and GUI apps, and programs you launch inherit it. Request escalated permissions for the Studio download and installer, launching Studio, and registering the server. If escalation is denied, walk the user through that step. A sandbox block doesn't mean the computer has no network.
- **ChatGPT web or mobile, a cloud sandbox, or Linux** (`uname` prints `Linux`) can't reach Studio. Say the user needs the ChatGPT desktop app or Codex, plus Roblox Studio on Mac or Windows, and keep helping without Studio.

### Duplicates

If more than one entry runs `StudioMCP` or `mcp.bat` (for example `Roblox_Studio` plus an older `roblox-studio`), the tools show up twice. Keep `Roblox_Studio` and, with approval, remove the other:

```sh
codex mcp remove <other-name>
```

In ChatGPT desktop, remove it under **Settings → MCP servers**. Then restart or open a new chat.
