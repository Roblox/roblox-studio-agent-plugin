---
name: roblox-get-started
description: Get Roblox Studio connected so you can build Roblox games with the user. Installs Studio if it's missing, opens it, guides the user through signing in and turning on its MCP server, and confirms the connection, automating every step the user doesn't have to do by hand. Use when the user wants to make or work on a Roblox game and Studio isn't connected yet, says "continue Roblox setup", or Studio tools are missing, failing, or list_roblox_studios returns nothing. Don't use it once a Studio instance is listed and the user is just building.
---

# Get started with Roblox Studio

This skill gets you to a working Studio connection, then stops. After that, use the Studio tools and Studio's own `skill` tool for the actual work.

It's safe to re-run. Each time, start at step 1 and skip whatever's already done. In a new chat, "continue Roblox setup" picks up where the user left off.

Do as much as you can yourself. The user should only have to approve once, sign in, open a place, turn on Studio's MCP setting, and reconnect if needed.

## References

- `references/install.md`: commands to check for, install, and open Studio, and where the user turns on its MCP setting.
- `references/connect.md`: how this app registers and loads Studio's MCP server, and where it can run. It has one section per app. Follow only yours.
- `references/troubleshoot.md`: symptoms and fixes.

## Rules

- **One approval, up front.** Before you change anything, list what you'll do: for example, download Studio from Roblox, install it, and open it. Ask once. That covers everything on the list. Ask again only for something that wasn't on it.
- Download only from `setup.rbxcdn.com`, and check the installer's Roblox signature (`install.md`). Don't use mirrors, package managers, third-party MCP servers, or `npx` packages.
- Never ask for, read, or type the user's Roblox password. The user signs in to Studio themselves.
- **Ask whether the user is signed in.** Ask "Are you signed in to Roblox Studio?" and believe the answer. Don't try to check it yourself.
- Never close or force-quit Studio yourself, because the user could lose unsaved work. Ask them to quit it.
- Don't edit Studio's settings files or change system security settings. The user turns on the MCP setting in Studio.
- If this app can't run commands on the user's computer (see `connect.md`), don't run them. Walk the user through each step instead.
- If a step fails, say which step and what you saw, then check `troubleshoot.md`.

## 1. Can this app reach Studio?

Check `connect.md` for where this app can run. If it can't reach a local Studio, say what the user needs, link https://create.roblox.com/docs/studio/setup, and keep helping in the meantime: plan the game, explain Roblox concepts, or write Luau they can paste in later. Don't leave the user at a dead end.

## 2. Already connected?

If `list_roblox_studios` is available, call it.

- **One or more instances:** go to step 7.
- **Empty list:** the server is running, but Studio is closed, no place is open, or the MCP setting is off. Go to step 3.
- **Tool not available, or the call fails:** the server isn't loaded, usually because Studio wasn't installed when this chat started. Go to step 3, and plan on step 6.

If the tools appear twice, follow the duplicates section in `connect.md` first.

## 3. Check what's there

Without asking, run the read-only check in `install.md` section A.

## 4. Ask once

Tell the user, in one short list, what's missing and what you'll do about it. Mention that Studio is free and comes from Roblox's official download server. Then ask for approval.

If the user would rather install Studio themselves, send them to https://create.roblox.com/docs/studio/setup. Wait until they say it's done, then continue from step 5.

## 5. Install and open

In this order, skipping whatever's already done:

1. **Install** (`install.md` section B).
2. **Open Studio** (`install.md` section C).

Then give the user what's left as **one** short list, and wait for them to say it's done:

- **Sign in:** ask "Are you signed in to Roblox Studio?" If not, ask them to sign in.
- **Open a place:** an existing place, or **Baseplate** on the start page.
- **Turn on the MCP setting**, if it isn't on yet: **Assistant → … → Settings → MCP Servers** (**… → Manage MCP Servers** in older versions), then turn on **Enable Studio as MCP server** (`install.md` section D). The Assistant panel only appears with a place open.

## 6. Register and load the tools

If the Studio tools weren't available at the start of this chat, follow `connect.md`: register Studio's server if this app needs it, then load the tools. Include any registration in the step 4 approval.

## 7. Handshake

Call `list_roblox_studios`:

- **One or more instances:** you're connected. If several are listed, match the user's words to a `name`, or ask which one they mean before changing anything. Keep that `studio_id` for the rest of the conversation.
- **Empty list:** no place is open, or the MCP setting is off. Go back to step 5.
- **Tool unavailable or failing:** go to step 6, then check `troubleshoot.md`.

Once you're connected, tell the user in one line, then get on with what they asked for. For Roblox-specific guidance, call Studio's `skill` tool, which lists the skills available in their Studio version. Don't rerun this skill, and don't add another MCP server.
