# tony repo — live GitHub Pages publish target

[← docs home](../README.md)

- **Remote:** `https://github.com/therealsparks/tony.git` (private, pushed to `origin/main`)
- **Live site:** **tony.austinvisuals.com** (confirmed from `directory.html`)
- **Owner on GitHub:** `therealsparks` (Matt's GitHub handle)
- **Status:** actively in use. The server pushes a heartbeat commit roughly every 15 minutes, plus content updates whenever dashboards change.

## Description

Publish target for the `deploy_github.py`, `deploy_analytics.py`, `deploy_status_page.py`, and related `deploy_*.py` scripts in the workspace. The server regenerates HTML and JSON dashboards, commits them to this repo, and pushes. GitHub Pages serves them at the URL above. This is not a source repository; it contains only generated artifacts.

The `site/` subfolder inside the TonyWorkspace snapshot is a git clone of this same repo, frozen at commit `e30cbb6` from 2026-03-27 (484 commits in). The live repo is now at 7,400+ commits.

## Commit pattern (7,400+ total, starting 2026-03-21)

| Count | Message pattern | What it is |
|------:|-----------------|------------|
| 6,600+ | `Status heartbeat <timestamp>Z` | Every ~15 min, rewrites `status.json` and commits. Liveness signal. |
| ~190  | `Update X` / `Sync X` | Dashboard and data refreshes (analytics, projects, invoices, competitor brief, intel) |
|    63 | `Update status page with known bugs section` | — |
|    63 | `Sync project status page` | — |
|    58 | `Sync QuickBooks invoice snapshot` | QBO pull pushed here |
|    40 | `Update tasks personal workspace` | — |
|     9 | _commits authored by "Tony (OpenClaw)"_ | Edits made directly by the runtime (all on 2026-03-25 and 2026-03-26 — e.g. *"Convert AV process page into training modules"*, *"Fix analytics suggestions encoding"*). All other commits are authored as `therealsparks`. |

## Contents (flat layout — it's a GitHub Pages root)

- **Dashboards:** `index.html`, `analytics.html`, `project-status.html`, `invoices.html`, `tasks.html`, `tasks-personal.html`, `status-page.html`, `competitor-brief.html`, `crm.html`, `directory.html`, `av-process.html`, `tony-commands.html`, `storyboards.html`, `chat-nancy.html`
- **Data:** `analytics.json`, `projects.json`, `status.json`, `competitor-brief.json`, `intel-brief.json`, `data/quickbooks/invoices.json`, `data/quickbooks/purchases.json`
- **`scripts/`** → a single script: `qbo_fetch_purchases.py`. The script expects `.openclaw/secrets/quickbooks.json` at the repo root, indicating it runs against a local checkout on the server.
- **`site/` and `site-deploy/`** → both contain the same 4 files (`index.html`, `invoices.html`, `chat-nancy.html`, `storyboards.html`). Appear to be separate per-page publish bundles — md5-identical. Purpose undetermined. See [Questions for Matt #6](../README.md#-questions-for-matt).
- **`docs/feature-backlog.md`** — a skill backlog Tony writes to. Last entry: 2026-04-02, mentions "upcoming VPS build" and "Matt's seven current focus items."
- **`qbo-callback.html`** — landing page for the QuickBooks OAuth flow. Tony displays this URL to the user so they can copy the post-auth URL back.

## Notes

- `feature-backlog.md` references "upcoming VPS build", a "Priority Stack", and "Matt's seven current focus items" — the closest in-repo indicator of current priorities. See [Questions for Matt #5](../README.md#-questions-for-matt).
- No `.gitignore`, no `.github/workflows/`, no git hooks — nothing automated on the GitHub side. All activity is driven by the server's push.

## Authors on this repo

```
therealsparks      7,400+ commits   (the server's machine user pushing Tony's output)
Tony (OpenClaw)        9 commits   (the AI itself making edits, late March only)
```
