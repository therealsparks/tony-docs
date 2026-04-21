# 3. Command loop — how a team member triggers an action

[← architecture index](README.md) · [← docs home](../README.md)

This is the primary way Tony is *used*. The AV team doesn't log into anything — they just email `tony@austinvisuals.com` (or CC him on a client thread). A Gmail-polling script picks it up, dispatches to a handler, the handler does the work against external services, and replies.

```mermaid
sequenceDiagram
    actor U as 👤 AV team member<br/>(whitelisted only)
    participant GM as 📧 Gmail<br/>tony@austinvisuals.com
    participant P as 🔄 Polling script<br/>gmail_read_tony.py
    participant H as 🛠️ Handler<br/>email_upload_handler.py · cc_scan_handler.py ·<br/>project_registry.py · video_request_handler.py
    participant X as ☁️ External<br/>Dropbox · Sheets · AI APIs
    participant W as 📁 Workspace<br/>projects.json · memory/
    participant G as 🐙 GitHub repo<br/>(dashboards)

    U->>GM: Email<br/>"Upload <Project>" + attachment
    Note right of GM: Subject-line routing:<br/>Upload · New Project · Status update ·<br/>Assign · Generate video · Voiceover...
    P->>GM: POP/IMAP poll
    P->>H: dispatch based on subject
    H->>H: check bot_whitelist.json<br/>(nancy/david/tom/adam/peter/che/tony only)
    H->>X: perform action<br/>(e.g. upload to Dropbox folder)
    X-->>H: success + link
    H->>W: update projects.json / memory
    H->>GM: send confirmation email + Dropbox link
    GM->>U: ✅ confirmation
    W->>G: deploy_*.py commits dashboard changes
```

## What this unlocks for the team

Anyone whitelisted can do the following with just an email:

- **File an attachment to a project's Dropbox folder** (forward + subject line `Upload <Project>`)
- **Create a new Dropbox project folder with the standard 7 subfolders** (subject `New Project <Name>`)
- **Passively file anything CC'd to Tony** on a client thread
- **Log status updates, check project status, assign staff, set due dates**
- **Generate an AI video via Google Veo**

Full list of working commands and what they do is in the [Emailing Tony guide](../guides/emailing-tony.md).

## What's blocked today

Per `reports/bot_skill_test_matrix.md` in the workspace snapshot, several commands that the team *might* expect to work are currently **not implemented**:

- `Dropbox transfer <Project> → <Owner>` — no handler written
- `List client folders` — no handler written
- `Voiceover request`, `Sound effect request`, `Generate music` — manual test scripts exist but no email pipelines
- `Kling video — <Shot>` — CLI exists, no email glue
- `MAKE FORMAL` / `PERSONALIZE NOTES` prefixes — advertised but no drafting pipeline

These are either scope to close during migration, or things we should tell the team not to try yet.

## Why this matters for the migration

- All of this glue is in the **workspace** (the 175 Python scripts in `scripts/`), not in the repo we can currently see on GitHub. Without the workspace, we have no command loop.
- The whitelist lives in `data/bot_whitelist.json` — easy to extend, but a person has to edit the file and Tony has to restart (or reload) to see the change. Worth revisiting post-migration.
- Gmail uses OAuth — we'll need fresh tokens on the VPS.

---

**Prev:** [← Publish loop](02-publish-loop.md) · **Next:** [VPS migration →](04-vps-migration.md)
