# TonyWorkspace snapshot (2026-04-01)

[← docs home](../README.md)

Purpose: a sanitized copy of `C:\Users\matthewwinters\.openclaw\workspace` as of **2026-04-01**. This is the actual content Tony operates on — identity files, dashboards, automation scripts, skills, and data. Secrets were intentionally stripped.

**Delivered as:** `TonyWorkspace-2026-04-01.zip` (~62 MB compressed, ~246 MB unzipped) via a Dropbox link in the onboarding spreadsheet.

> **⚠️ Already stale.** Frozen 2026-04-01; today is 2026-04-20. The publish repo (see [tony-repo.md](tony-repo.md)) has moved ~7,000 commits since. Useful reference, not ground truth. Plan to request a fresh snapshot before doing real work. See [Questions for Matt #5](../README.md#-questions-for-matt).

## Top-level identity + policy files

These define *who* Tony is and how he behaves. OpenClaw reads them every session.

| File | Purpose |
|------|---------|
| `AGENTS.md` | Main system prompt / operator manual. Covers memory, session hygiene, group-chat etiquette, heartbeats. |
| `IDENTITY.md` | Name (Tony), vibe, emoji, avatar. |
| `SOUL.md` | Personality/values guardrails. |
| `USER.md` | Who Matt is + his cost-control and email-review rules (pause above $25, draft outbound emails for review). |
| `TOOLS.md` | Local infra notes (e.g. Dropbox account scoped to `info@austinvisuals.com`, delete-forbidden policy). |
| `SESSION_POLICY.md` | Session lifecycle rules + `scripts/session_housekeeper.py` docs. |
| `README.txt` | Matt's restore instructions. |

## Dashboards + state (root HTML/JSON)

Static dashboards served via GitHub Pages (or any static host). Each HTML has a companion JSON with the live data.

- `analytics.html` + `analytics.json` — GA4 site analytics view
- `project-status.html` + `projects.json` — **24 active/prospect client projects** with assignees, progress %, notes (at snapshot time)
- `status-page.html` + `status.json` — Tony's own health page (currently flags *Landon Dropbox alerts paused* and *GA4 pull failing*)
- `invoices.html` — QuickBooks invoices view (data in `data/quickbooks/invoices.json`)
- `tasks.html`, `tasks-personal.html` — task boards
- `package.json` — only dep is `localtunnel` (for the `start_av_chat_tunnel.cmd` flow)
- `cron-email-watchdog.ps1` — Windows scheduled-task watchdog

## `scripts/` — 175 files of automation glue

The operational heart of Tony. Roughly organized by integration:

- **Dropbox:** `dropbox_*.py`, `upload_dropbox.py`, `email_upload_handler.py`, `cc_scan_handler.py` (CC/BCC passive filing)
- **Gmail:** `gmail_*.py` (OAuth, POP, send/read for `bot@` and `tony@` mailboxes), `gmail_bot_utils.py`
- **Google Drive / Docs / Sheets:** `drive_*.py`, `backup_to_drive.py`, `share_sheet.py`, `inspect_sheet*.py`
- **QuickBooks:** `qbo_fetch_invoices.py`, `run_qbo_sync.ps1` (registered as `TonyQuickBooksSync` Task Scheduler job on the laptop)
- **Deploy (GitHub Pages):** `deploy_github.py`, `deploy_analytics.py`, `deploy_status_page.py`, `deploy_other_pages.py`, `deploy_page.py`, `deploy_tasks_personal.py`
- **AI media:** `veo_*.py` / `test_vertex_veo.py` (Google Veo video gen), `replicate_*.py`, `test_elevenlabs*.py`, `tts_send.py`, `voice_sampler.py`
- **Project registry:** `project_registry.py`, `seed_projects.py`, client onboarding scripts (`add_*.py`, `onboard_*.py`)
- **Competitor / intel:** `competitor_feed.py`, `intel_feed.py`, `brave_search.py`, `deploy_competitor_brief.py`
- **WordPress:** `wp_rest.py`, `wp_theme_info.py` (austinvisuals.com runs WP)
- **Ops:** `session_housekeeper.py`, `gateway_watchdog.py`, `register_tony_cron.ps1`, `run_watchdog_hidden.vbs`, `tunnel.js`, `start_av_chat_tunnel.cmd`

Mix of Python, PowerShell, Node/TypeScript, and a VBScript. No formal `requirements.txt` — deps are implicit per script.

## `site/` — static dashboard bundle

Same HTML dashboards as root (plus extras: `competitor-brief.html`, `crm.html`, `directory.html`, `tony-commands.html`, `intel-brief.json`, `av-process.html`). This is the GitHub Pages publish root.

## `skills/` — OpenClaw "AgentSkills"

Modular capability definitions Tony loads on demand. Each has a `SKILL.md` frontmatter describing trigger conditions.

- **browser-automation** — Stagehand CLI wrapper for natural-language browser actions. Includes a full `my-stagehand-app/` TypeScript project (WordPress admin/chat automations: `wp_chat_workflow.ts`, `wp-admin-check.ts`, etc.).
- **storyboard-video** — pipeline to turn Word-doc storyboard frames into stitched MP4 previews (python-docx + Pillow + moviepy/ffmpeg).
- **video-watermark** — ffmpeg-based "Austin Visuals Sample" text-grid watermark for external preview clips. Ships with `scripts/apply_text_watermark.py`.

## `data/`

Reference data — mostly research outputs and registries, not live.

- `openclaw_av_processes.md`, `av_processes_playbook.md` — large synthesized SOP/knowledge packs built from 15 of 42 DOCX files in Matt's Drive (marketing, sales, production workflows). Note: self-described as **partial ingest**.
- `austinvisuals-videos.md`, `portfolio-urls.txt` — scraped portfolio catalog from austinvisuals.com
- `bot_whitelist.json` — email addresses Tony accepts commands from (nancy, david, tom, adam, peter, che, tony @austinvisuals.com)
- `contact-form-locations.md`, `contact-form-tests.csv` — site audit
- `ambiguous-av-web.csv/txt`, `av_docs_inventory.csv`, `av_docs_pending.csv` — doc inventory / triage
- `quickbooks/invoices.json` — QBO invoice snapshot (~2000 lines)
- `talent/motion-senior.csv`, `talent/motion-sheet.json` — motion graphics freelancer roster

## `docs/business/`

- `best-of-sheet.md` — policy for the "AV Best Work Samples" master Google Sheet. Notable: sales-critical; edits require explicit approval from Matt.

## `reports/`

- `bot_skill_test_matrix.md` — **the single most useful onboarding doc in the bundle.** Skill-by-skill test matrix from the 2026-03-28 Gmail migration: 20+ capabilities scored Pass / Blocked / Pending, with specific gaps called out (Dropbox transfer handler missing, voice/music email handlers missing, MAKE FORMAL pipeline not implemented, etc.). The [Emailing Tony guide](../guides/emailing-tony.md) is distilled from this file.
- `shared_docs_*.csv`, `shared_doc_copies_for_cam.csv` — Drive sharing audits.
