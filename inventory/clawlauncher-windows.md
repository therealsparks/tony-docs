# ClawLauncher-Windows bundle

[← docs home](../README.md)

Purpose: the full contents of `C:\openclaw\ClawLauncher-Windows` from Matt's laptop. This is the launcher GUI plus everything needed to install its Windows dependencies. Treat it as "how Tony is currently run on the laptop we're replacing."

**Delivered as:** `ClawLauncher-Windows.zip` (~550 MB compressed, ~999 MB unzipped) via a Dropbox link in the onboarding spreadsheet.

## What's inside

| Item | What it is |
|------|------------|
| `ClawLauncher.exe` | 11 MB Go-based launcher GUI. Serves a local web UI on `localhost:54321` that bootstraps deps and runs `openclaw` commands. |
| `myclaw/` | npm install root. `package.json` pins `openclaw@^2026.3.23` and `node-llama-cpp@^3.16.2`. `node_modules/` already populated (373 packages) — includes Anthropic/OpenAI/Google/AWS SDKs, MCP, Hono, sharp, llama.cpp bindings, etc. This is effectively the OpenClaw runtime. |
| `Git-2.53.0-arm64.exe` | Git installer (63 MB, ARM64). |
| `node-v24.14.0-arm64.msi` (×3 duplicates) | Node.js 24 installer for ARM64. Three copies — likely repeat downloads, can dedupe. |
| `Microsoft.DesktopAppInstaller_*.msixbundle` | 216 MB Windows installer package. |
| `launcher.log` | Runtime log from Mar 7, 2026 — shows VC++ redist install, failed Node.js MSI (exit 1603), Chocolatey bootstrap, and `openclaw doctor` invocations. Good trace of what the launcher actually does on startup. |
| `logo.png` | Launcher icon. |
| `restore points/restorepointsdb.json` | Empty array — restore-point DB, never populated. |
| `skill-backups/skillbackupsdb.json` | Empty array — skill backup DB, never populated. |

## Architecture note

This specific bundle is **ARM64 Windows** — the installers target Matt's personal laptop (likely a Surface Pro X or similar ARM device). The installers themselves aren't directly usable on a typical x64 VPS, but they're useful as a reference for which dependencies the runtime actually needs (Node 24, Git, VC++ redist, Chocolatey).

## Linux is a real option (despite how the bundle looks)

`ClawLauncher.exe` is a Go-based GUI wrapper, not the runtime itself. The actual runtime is the `openclaw` npm package (Node.js), which is cross-platform. Matt's own notes mention "switch to the Linux launcher if preferred" and reference a `/opt/openclaw/workspace` install path — so a Linux-native path exists.

**Don't plan around Wine emulation** — that path adds complexity and bugs for no real benefit. See [Questions for Matt #1](../README.md#-questions-for-matt) for the conversation to have with the client.
