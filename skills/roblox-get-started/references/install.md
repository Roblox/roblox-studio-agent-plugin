# Install, open, and enable Studio

Commands for the `roblox-get-started` skill. Run them only on the user's own computer, and only after the user has approved the actions they're part of. Each command is self-contained, so don't rely on variables from an earlier one.

## A. Check what's there

This only reads. Run it without asking.

**macOS:**

```zsh
for APP in /Applications/RobloxStudio.app "$HOME/Applications/RobloxStudio.app"; do [ -d "$APP" ] && { echo "INSTALLED $APP"; break; }; done
pgrep -x RobloxStudio >/dev/null && echo RUNNING || echo NOT_RUNNING
```

**Windows (PowerShell):**

```powershell
$studio = Get-ChildItem "$env:LOCALAPPDATA\Roblox\Versions\*\RobloxStudioBeta.exe", "${env:ProgramFiles(x86)}\Roblox\Versions\*\RobloxStudioBeta.exe" -ErrorAction SilentlyContinue | Sort-Object LastWriteTime -Descending | Select-Object -First 1
if ($studio) { "INSTALLED $($studio.FullName)" } else { 'NOT_INSTALLED' }
if (Get-Process RobloxStudioBeta -ErrorAction SilentlyContinue) { 'RUNNING' } else { 'NOT_RUNNING' }
```

If nothing printed `INSTALLED`, Studio isn't installed.

## B. Install Studio

**macOS:** download the installer, check that Roblox signed it, run it, and wait for Studio to appear. Give the command a timeout of at least 6 minutes.

```zsh
set -e
DMG="$(mktemp -d)/RobloxStudio.dmg"
curl -fsSL --max-time 300 -o "$DMG" https://setup.rbxcdn.com/mac/RobloxStudio.dmg
VOL="$(mktemp -d)"
hdiutil attach -nobrowse -readonly -mountpoint "$VOL" "$DMG" >/dev/null
INSTALLER="$(find "$VOL" -maxdepth 1 -name '*.app' | head -1)"
[ -n "$INSTALLER" ] || { hdiutil detach "$VOL" >/dev/null; echo "No installer app found in the disk image." >&2; exit 1; }
codesign --verify --deep --strict "$INSTALLER"
codesign -dv "$INSTALLER" 2>&1 | grep -q '^TeamIdentifier=2CFABCH843$' || { hdiutil detach "$VOL" >/dev/null; echo "Installer is not signed by Roblox. Stopping." >&2; exit 1; }
open -W "$INSTALLER" &
for _ in $(seq 1 60); do
  for APP in /Applications/RobloxStudio.app "$HOME/Applications/RobloxStudio.app"; do
    [ -x "$APP/Contents/MacOS/StudioMCP" ] && { echo "INSTALLED $APP"; break 2; }
  done
  sleep 5
done
hdiutil detach "$VOL" >/dev/null 2>&1 || true
```

If it doesn't print `INSTALLED` within 5 minutes, ask the user whether the installer is still running or showed an error. Don't download it again unless they ask.

**Windows (PowerShell):** download the installer, check that Roblox signed it, and start it. The installer opens its own window and launches Studio when it's done. Give the command a timeout of at least 6 minutes.

```powershell
$ProgressPreference = 'SilentlyContinue'
$installer = Join-Path $env:TEMP 'RobloxStudioInstaller.exe'
Invoke-WebRequest -Uri 'https://setup.rbxcdn.com/RobloxStudioInstaller.exe' -OutFile $installer -UseBasicParsing
$sig = Get-AuthenticodeSignature -FilePath $installer
if ($sig.Status -ne 'Valid' -or $sig.SignerCertificate.Subject -notlike '*O=Roblox Corporation*') {
    Remove-Item $installer -Force
    throw "Installer signature check failed: status=$($sig.Status) signer=$($sig.SignerCertificate.Subject)"
}
Start-Process -FilePath $installer
$deadline = (Get-Date).AddMinutes(5)
do {
    Start-Sleep -Seconds 5
    $studio = Get-ChildItem "$env:LOCALAPPDATA\Roblox\Versions\*\RobloxStudioBeta.exe" -ErrorAction SilentlyContinue | Select-Object -First 1
} until ($studio -or (Get-Date) -gt $deadline)
if ($studio) { "INSTALLED $($studio.FullName)" } else { 'INSTALL_NOT_DETECTED' }
```

Don't add `-Wait` to `Start-Process`. It would wait for Studio to close, not for the install to finish.

## C. Open Studio

**macOS:**

```zsh
for APP in /Applications/RobloxStudio.app "$HOME/Applications/RobloxStudio.app"; do [ -d "$APP" ] && { pgrep -x RobloxStudio >/dev/null || open "$APP"; break; }; done
```

**Windows (PowerShell):**

```powershell
if (-not (Get-Process RobloxStudioBeta -ErrorAction SilentlyContinue)) {
    $studio = Get-ChildItem "$env:LOCALAPPDATA\Roblox\Versions\*\RobloxStudioBeta.exe", "${env:ProgramFiles(x86)}\Roblox\Versions\*\RobloxStudioBeta.exe" -ErrorAction SilentlyContinue | Sort-Object LastWriteTime -Descending | Select-Object -First 1
    if ($studio) { Start-Process -FilePath $studio.FullName } else { throw 'Studio is not installed.' }
}
```

If Studio shows an update prompt, let it finish.

## D. Turn on the MCP setting

The user does this in Studio. Don't edit Studio's settings files. It needs a place open, because the Assistant panel only appears then:

1. Open **Assistant** and select **… → Settings → MCP Servers** (**… → Manage MCP Servers** in older versions).
2. Turn on **Enable Studio as MCP server**.

The setting is per Roblox account, so after switching accounts it may need turning on again.
