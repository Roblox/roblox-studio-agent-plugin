# Troubleshooting the Studio connection

Match the symptom, give the user the single fix, then re-run setup from the top. For anything about registering the server or loading its tools in this app, see `connect.md`.

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| No Studio tools in this chat | The server isn't registered or loaded in this app, or Studio wasn't installed when the chat started | Follow `connect.md` |
| Tools appear twice | Studio's server is registered twice | Follow the duplicates section in `connect.md` |
| Tools present, but `list_roblox_studios` is empty | Studio isn't running, no place is open, or the MCP setting is off | Open Studio, open a place, and turn on the setting (`install.md` sections C and D) |
| "Unable to reach Roblox Studio" | Same as above | Same as above |
| The MCP setting was on, but now it's off | The setting is per Roblox account, and the user switched accounts | Turn it on again for this account |
| Studio is installed, but there's no `StudioMCP` (macOS) or `mcp.bat` (Windows) | Studio is out of date | Update Studio, then reopen it |
| macOS: "Roblox Studio MCP was not found" | Studio isn't in `/Applications` or `~/Applications` | Install Studio, or move `RobloxStudio.app` into one of those folders |
| Connection fails right after the setting is turned on | Another app is using port 13469 | Close the other app, then restart Studio |
| Windows: the server fails to start | `mcp.bat` is missing, or the path is wrong | Check that `%LOCALAPPDATA%\Roblox\mcp.bat` exists. If it doesn't, update Studio |
| Tool calls go to the wrong place | Several Studios are open | Call `list_roblox_studios`, confirm the place by name, and use that `studio_id` |
| A tool mentioned in the docs is missing | Studio's tool set varies with its version and feature flags | Use the tools that are there, and suggest updating Studio |
| A download, install, or launch command is blocked | The app's sandbox can't reach the user's computer, or blocks network or GUI apps | See `connect.md` for this app. If it can't be unblocked, walk the user through the step instead |

If nothing matches, say which setup step failed and what you saw. Leave the user's places and files unchanged, and don't install third-party tools to work around it.
