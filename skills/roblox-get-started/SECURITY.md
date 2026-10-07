# Security notes: roblox-get-started

This skill sets up Roblox Studio on the user's own computer. One of its steps can look risky to automated scanners, so here is exactly what it does and how it's limited.

| ID | What the skill does | Where |
| --- | --- | --- |
| `SEC_INSTALLER_DOWNLOAD_RUN` | Downloads the Roblox Studio installer and runs it | `references/install.md` section B |

It runs only on the user's own computer, and only after the user approves a list that names it. One approval covers the actions on that list and nothing else.

## Downloading and running the Studio installer

- **Source:** only Roblox's official download host: `https://setup.rbxcdn.com/mac/RobloxStudio.dmg` (macOS) or `https://setup.rbxcdn.com/RobloxStudioInstaller.exe` (Windows). These are the files `https://www.roblox.com/download/studio` redirects to. No mirrors, package managers, or third-party tools.
- **Signature check before running:**
  - **macOS:** `codesign --verify --deep --strict` must pass, and the Team ID must be Roblox's, `2CFABCH843`.
  - **Windows:** the Authenticode signature must be valid, with a signer subject containing `O=Roblox Corporation`.
  - If either check fails, the skill stops and deletes the download (Windows) or detaches the disk image (macOS) without running anything.
- **No elevation:** the skill never uses `sudo` or asks for an administrator password. The Roblox installer handles its own prompts.
- **Alternative:** if the user prefers, the skill sends them to https://create.roblox.com/docs/studio/setup to install Studio themselves.

## Turning on Studio's MCP server

The skill doesn't do this itself. It asks whether the user is signed in, then has them turn on **Enable Studio as MCP server** in Studio: **Assistant → … → Settings → MCP Servers**. It doesn't read or edit Studio's settings or local-storage files.

## What the skill never does

- Ask for, read, type, or store Roblox passwords or sign-in tokens.
- Close, force-quit, or restart Studio itself.
- Check whether the user is signed in by reading files. It asks them.
- Read or edit Studio's settings or local-storage files, or change system security settings.
- Install third-party MCP servers, proxies, or `npx` packages.
- Register a second Studio MCP server.
