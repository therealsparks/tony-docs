# ClawLauncher-Windows bundle

[← docs home](../README.md)

**Historical reference.** Contents of `C:\openclaw\ClawLauncher-Windows` from Matt's laptop, captured before the migration to the current server. Retained for architectural reference; not representative of the current deployment.

**Delivered as:** `ClawLauncher-Windows.zip` (~550 MB compressed, ~999 MB unzipped) via a Dropbox link in the onboarding spreadsheet.

## What's inside

| Item | What it is |
|------|------------|
| `ClawLauncher.exe` | 11 MB Go-based launcher GUI. Serves a local web UI on `localhost:54321` that bootstraps deps and runs `openclaw` commands. |
| `myclaw/` | npm install root. `package.json` pins `openclaw@^2026.3.23` and `node-llama-cpp@^3.16.2`. `node_modules/` already populated (373 packages) — includes Anthropic/OpenAI/Google/AWS SDKs, MCP, Hono, sharp, llama.cpp bindings, etc. This is effectively the OpenClaw runtime. |
| `Git-2.53.0-arm64.exe` | Git installer (63 MB, ARM64). |
| `node-v24.14.0-arm64.msi` (×3 duplicates) | Node.js 24 installer for ARM64. Three copies — likely repeat downloads. |
| `Microsoft.DesktopAppInstaller_*.msixbundle` | 216 MB Windows installer package. |
| `launcher.log` | Runtime log from Mar 7, 2026 — shows VC++ redist install, failed Node.js MSI (exit 1603), Chocolatey bootstrap, and `openclaw doctor` invocations. Records the launcher's startup sequence. |
| `logo.png` | Launcher icon. |
| `restore points/restorepointsdb.json` | Empty array — restore-point DB, never populated. |
| `skill-backups/skillbackupsdb.json` | Empty array — skill backup DB, never populated. |

## Architectural notes

- The bundle targets **ARM64 Windows** — that matches Matt's personal laptop (likely a Surface Pro X or similar ARM device). It's a point-in-time artifact of one user's install.
- `ClawLauncher.exe` is a Go-based GUI wrapper, not the runtime itself. The runtime is the `openclaw` npm package (Node.js), which is cross-platform (the OpenClaw README supports macOS, Linux, and Windows via WSL2).
