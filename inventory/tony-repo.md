# tony repo — live GitHub Pages publish target

[← docs home](../README.md)

- **Remote:** `https://github.com/therealsparks/tony.git` (private, pushed to `origin/main`)
- **Live site:** **https://therealsparks.github.io/tony/** (confirmed from `directory.html`)
- **Owner on GitHub:** `therealsparks` (Matt's GitHub handle)
- **Status:** **Actively in production use right now.** (Matt's "not in use" statement is not accurate — the `cron-email-watchdog.ps1` / Tony heartbeat loop on his laptop is committing to `main` every ~15 min.)

## What it actually is

This is the **publish target** for the `deploy_github.py` + `deploy_analytics.py` + `deploy_status_page.py` + `deploy_*.py` scripts in the workspace. Tony runs on Matt's laptop, regenerates HTML/JSON dashboards, then checks them into this repo and pushes. GitHub Pages serves them at the URL above. **It is not a second codebase — it is the runtime output of the workspace.**

Confirmed by: the `site/` subfolder inside the TonyWorkspace snapshot is itself a git clone of this same repo, frozen at commit `e30cbb6` from 2026-03-27 (484 commits in at that point). The live repo is now at 7,473 commits — roughly **7,000 commits of activity have happened since the snapshot** Matt gave us was cut.

## Commit pattern (7,473 total, starting 2026-03-21)

| Count | Message pattern | What it is |
|------:|-----------------|------------|
| 6,648 | `Status heartbeat <timestamp>Z` | Every ~15 min, rewrites `status.json` and commits. This is Tony's "I'm alive" signal. |
| ~190  | `Update X` / `Sync X` | Dashboard and data refreshes (analytics, projects, invoices, competitor brief, intel) |
|    63 | `Update status page with known bugs section` | — |
|    63 | `Sync project status page` | — |
|    58 | `Sync QuickBooks invoice snapshot` | QBO pull pushed here |
|    40 | `Update tasks personal workspace` | — |
|     9 | _commits authored by "Tony (OpenClaw)"_ | Tony itself making edits to the site (all on 2026-03-25 and 2026-03-26 — e.g. *"Convert AV process page into training modules"*, *"Fix analytics suggestions encoding"*). Everything else is authored as `therealsparks`. |

## Contents (flat layout — it's a GitHub Pages root)

- **Dashboards:** `index.html`, `analytics.html`, `project-status.html`, `invoices.html`, `tasks.html`, `tasks-personal.html`, `status-page.html`, `competitor-brief.html`, `crm.html`, `directory.html`, `av-process.html`, `tony-commands.html`, `storyboards.html` *(new vs snapshot)*, `chat-nancy.html` *(new)*
- **Data:** `analytics.json`, `projects.json` (**25 projects, updated 2026-04-10** — snapshot had 24 projects from 2026-03-26), `status.json`, `competitor-brief.json`, `intel-brief.json`, `data/quickbooks/invoices.json`, `data/quickbooks/purchases.json` *(new)*
- **`scripts/`** → a single script: `qbo_fetch_purchases.py` *(new — not in the workspace snapshot)*. Note it expects `.openclaw/secrets/quickbooks.json` at the repo root, which confirms Tony runs this script against a local checkout.
- **`site/` and `site-deploy/`** → both contain the same 4 files (`index.html`, `invoices.html`, `chat-nancy.html`, `storyboards.html`). Appears to be a separate per-page publish bundle — md5-identical to each other. Purpose unclear; could be a staging flow. See [Questions for Matt #9](../README.md#-questions-for-matt).
- **`docs/feature-backlog.md`** *(new)* — a skill backlog Tony writes to. Last entry: 2026-04-02, mentions "upcoming VPS build" and "Matt's seven current focus items."
- **`qbo-callback.html`** *(new)* — landing page for the QuickBooks OAuth flow. Tony displays this URL to the user so they can copy the post-auth URL back.

## What this means for the migration

- **Don't push to this repo without coordination.** If we commit while Tony is mid-heartbeat, we cause merge conflicts on Matt's laptop and break the deploy flow.
- **The snapshot is already stale.** 3 weeks and ~7,000 commits of drift. Before doing anything on the VPS, `git pull` this repo to see current state, and ideally pull a fresh sanitized workspace from Matt too — the 2026-04-01 bundle is no longer ground truth.
- **GitHub Pages + a PAT is the deploy mechanism.** The `github-token.txt` secret Matt needs to give us is the PAT for this `therealsparks/tony` repo. On the VPS, the cutover sequence is: (1) get a fresh clone of this repo in the new workspace, (2) flip the heartbeat cron from the laptop to the VPS, (3) ideally only one should be pushing at a time to avoid collisions.
- **`feature-backlog.md` says "upcoming VPS build" + "Priority Stack" + "Matt's seven current focus items."** This is the best in-repo hint we have about scope. See [Questions for Matt #8](../README.md#-questions-for-matt).

## Authors on this repo

```
therealsparks      7,464 commits   (cron/heartbeat machine user — Tony's deploy push)
Tony (OpenClaw)        9 commits   (the AI itself making edits, late March only)
```

No `.gitignore`, no `.github/workflows/`, no git hooks — nothing automated on the GitHub side. Everything is driven from the laptop push.
