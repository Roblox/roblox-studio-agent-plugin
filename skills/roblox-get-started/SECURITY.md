# Security notes: roblox-get-started

This skill sets up Roblox Studio on the user's own computer. Two of its steps can look risky to automated scanners, so here is exactly what they do and how they're limited.

| ID | What the skill does | Where |
| --- | --- | --- |
| `SEC_INSTALLER_DOWNLOAD_RUN` | Downloads the Roblox Studio installer and runs it | `references/install.md` section B |
| `SEC_RUN_OUTSIDE_SANDBOX` | Runs the install and open commands outside the assistant's sandbox | `SKILL.md` rules, `references/connect.md` |

Both run only on the user's own computer, and only after the user approves a list that names them. One approval covers the actions on that list and nothing else.

## Downloading and running the Studio installer

- **Source:** only Roblox's official download host: `https://setup.rbxcdn.com/mac/RobloxStudio.dmg` (macOS) or `https://setup.rbxcdn.com/RobloxStudioInstaller.exe` (Windows). These are the files `https://www.roblox.com/download/studio` redirects to. No mirrors, package managers, or third-party tools.
- **Signature check before running:**
  - **macOS:** `codesign --verify --deep --strict` must pass, and the Team ID must be Roblox's, `2CFABCH843`.
  - **Windows:** the Authenticode signature must be valid, with a signer subject containing `O=Roblox Corporation`.
  - If either check fails, the skill stops and deletes the download (Windows) or detaches the disk image (macOS) without running anything.
- **No elevation:** the skill never uses `sudo` or asks for an administrator password. The Roblox installer handles its own prompts.
- **Alternative:** if the user prefers, the skill sends them to https://create.roblox.com/docs/studio/setup to install Studio themselves.

## Running commands outside the sandbox

- **Why:** a sandboxed shell can point `$HOME` or `%LOCALAPPDATA%` at its own folders, or run as a separate account. The installer could then put Studio somewhere its MCP server can't find, and Studio may not start.
- **What:** only the install and open commands (`references/install.md` sections B and C). The read-only check in section A runs inside the sandbox.
- **Approval:** the skill names this step in the up-front list and asks before doing it. It uses the app's own permission prompt (for example, Codex's escalation request) and never changes the sandbox's settings.
- **If the user says no:** the skill walks them through installing and opening Studio themselves.

## Turning on Studio's MCP server

The skill doesn't do this itself. It asks whether the user is signed in, then has them turn on **Enable Studio as MCP server** in Studio: **Assistant → … → Settings → MCP Servers**. It doesn't read or edit Studio's settings or local-storage files.

## What the skill never does

- Ask for, read, type, or store Roblox passwords or sign-in tokens.
- Close, force-quit, or restart Studio itself.
- Check whether the user is signed in by reading files. It asks them.
- Read or edit Studio's settings or local-storage files, or change system security settings.
- Install third-party MCP servers, proxies, or `npx` packages.
- Register a second Studio MCP server.
- Run commands outside the sandbox without the user's approval, or change the sandbox's settings.
