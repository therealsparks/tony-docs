# Missing pieces (need from Matt)

[← docs home](../README.md)

Things we know we need before we can stand Tony up on a VPS. Called out in Matt's original handoff `README.txt`, and confirmed by inspection of the delivered bundles.

## 1. `.openclaw/secrets/` — not shipped

The whole `secrets/` directory was intentionally stripped from the workspace snapshot. Matt expects the VPS operator to recreate these with fresh tokens. We'll need:

- Dropbox access token (`dropbox-access-token.txt`)
- Gmail OAuth for `bot@austinvisuals.com`, `tony@austinvisuals.com`, and Matt's account
- Google service account JSON (`av-automation.json` — referenced by Sheets + QBO tooling)
- QuickBooks OAuth (`quickbooks.json`)
- GitHub Personal Access Token for the publish repo (`github-token.txt`)
- ElevenLabs API key
- Replicate API key
- Vertex AI / Google Cloud service account for Veo
- GA4 Analytics API credentials

**Handoff method matters** — these are production credentials. Don't just email them. See [Questions for Matt #10](../README.md#-questions-for-matt).

## 2. `memory/` — not shipped

Runtime memory store where Tony writes his daily notes and long-term recollections. Per Matt's handoff: generate fresh on the VPS. We don't need to migrate the existing memory.

## 3. Windows Task Scheduler job definitions

The `.ps1` and `.vbs` trigger scripts are present in `scripts/` (e.g. `register_tony_cron.ps1`, `run_watchdog_hidden.vbs`), but the *registered schedule* (TonyQuickBooksSync, heartbeat watchdog, Dropbox alerts) lives in Matt's laptop's Task Scheduler database. These need to be re-created as systemd timers (Linux) or Task Scheduler jobs (Windows) on the VPS.

**Good news:** we can reverse-engineer the schedule by reading the `.ps1` scripts and the commit cadence in the `tony` repo (heartbeats every 15 min, etc.).

## 4. Production hosting target

Matt mentioned "VPS (Windows or Linux + Wine), 16 GB / 4 vCPU min." Decision not yet made. See [Questions for Matt #1](../README.md#-questions-for-matt) — strong recommendation is Linux, native, no Wine.

## 5. Full 42-document SOP corpus

`data/openclaw_av_processes.md` and `data/av_processes_playbook.md` are built from **only 15 of 42** business process DOCX files in Matt's Google Drive. The other 27 are missing from what we've seen. Without them, Tony's understanding of Austin Visuals' operations is partial — which will bite us when we try to grow his skills.

See [Questions for Matt #7](../README.md#-questions-for-matt).

## 6. Fresh workspace snapshot

Not "missing" in the strict sense — we have *a* snapshot, but it's 3 weeks old and the publish repo has moved ~7,000 commits since. Before we migrate, we need a fresh cut from Matt's laptop with the same exclusions (no secrets, no memory).

See [Questions for Matt #5](../README.md#-questions-for-matt).
