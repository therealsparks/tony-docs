# Emailing Tony

[← docs home](../README.md)

Tony accepts commands via email. There is no web UI. A whitelisted sender emails `tony@austinvisuals.com` with a specific subject line. Tony executes the command and replies with a confirmation.

This guide lists the currently supported commands and their expected behavior.

---

## Who can email Tony

Only the following addresses are recognized as command senders (per `data/bot_whitelist.json`):

- `nancyschirm@austinvisuals.com`
- `david@austinvisuals.com`
- `tom@austinvisuals.com`
- `adam@austinvisuals.com`
- `peter@austinvisuals.com`
- `che@austinvisuals.com`
- `tony@austinvisuals.com`

Emails from anyone else will be ignored (or, for some commands, trigger an access-request flow that Matt or Nancy has to approve).

### Two mailboxes, one Tony

- **`tony@austinvisuals.com`** — the main command inbox. Nearly everything in this guide goes here.
- **`bot@austinvisuals.com`** — a secondary inbox Tony also watches. Currently used only for the Task Intake Dashboard flow (not yet fully live).

For general use, send to `tony@austinvisuals.com`.

---

## Commands that work today

Each row below is confirmed working (✅ Pass) in the internal test matrix.

### Dropbox filing

| Subject line | What to put in the body | What Tony does |
|---|---|---|
| `Upload <Project>` | Attach one or more files. Body can be blank or short. | Files the attachments into the project's Dropbox folder. Replies with a confirmation email containing a Dropbox link to the destination. |
| `New Project <Name>` | Optional short description. | Creates `/Client/<Name>/` in Dropbox with the standard 7 subfolders. Adds a row to the "Best Of" Google Sheet. Confirms by email. |
| `New Prospect Project <Name>` | Optional short description. | Same as above but under `/Client/Z_Proposals and Prospects/<Name>/`. |
| _(no subject — just CC/BCC Tony)_ | Reply-all to a client thread with an attachment, and CC or BCC `tony@austinvisuals.com`. | Passively files any attachments from the thread into the correct project's `Client Reference/` folder. Deduplicates repeats. Replies with a summary. |

### Project tracking

| Subject line | Body / format | What Tony does |
|---|---|---|
| `Status update <Project>` | Free-text update in the body. | Appends the update to the project's registry entry. Pings Matt and Nancy. Confirms by email. |
| `Status check <Project>` | Empty body. | Replies with the project's current status summary, assignees, progress %, and last updates. If the sender isn't assigned, triggers an access-request flow. |
| `Assign <Name> to <Project>` | Empty body. | Adds Name to the project's assignee list. **Matt or Nancy only.** Notifies the assignee. |
| `Set due date <Project> <Date>` | Empty body. Date format: flexible (e.g. `April 30`, `2026-04-30`). | Updates the project's due date. Confirms by email. |
| `Approve <Name> for <Project>` | Empty body. | Grants a pending access request. Adds the requester to the project. **Matt or Nancy only.** |

### AI video generation

| Subject line | Body | What Tony does |
|---|---|---|
| `Generate video <Project>` | Free-text prompt describing the shot. | Submits the prompt to Google Veo 2 via Vertex AI. Replies with the generated MP4 as an attachment. Typically returns in a few minutes. |

### Viewing dashboards (no email needed)

These aren't email commands — they're just URLs you can bookmark:

- [**Project status**](https://therealsparks.github.io/tony/project-status.html) — every active and prospect project with assignees and progress
- [**Analytics**](https://therealsparks.github.io/tony/analytics.html) — website traffic from GA4
- [**Invoices**](https://therealsparks.github.io/tony/invoices.html) — QuickBooks invoice snapshot
- [**Competitor brief**](https://therealsparks.github.io/tony/competitor-brief.html) — daily competitor intel
- [**Status page**](https://therealsparks.github.io/tony/status-page.html) — Tony's own health and known issues
- [**Tony commands**](https://therealsparks.github.io/tony/tony-commands.html) — Tony's own command summary

---

## ⚠️ Commands that do NOT work yet

The following commands may have been advertised or discussed but **are not currently implemented** — don't send these expecting a response. They're on the roadmap; status tracked in the workspace's test matrix.

- `Dropbox transfer <Project> → <Owner>` — no handler implemented
- `List client folders` / `List prospect folders` — no handler implemented
- `Voiceover request` — no email pipeline (ElevenLabs integration exists but isn't wired to Gmail yet)
- `Sound effect request` — same as above
- `Generate music` — same as above
- `Kling video — <Shot>` — Kling CLI exists, no email trigger
- `MAKE FORMAL` / `PERSONALIZE NOTES` email-drafting prefixes — no drafting pipeline implemented

For unsupported commands, contact Matt.

---

## What to expect back

For almost every command, Tony replies by email:

- **Success:** confirmation message, plus a relevant link (Dropbox URL, project-status URL, generated file).
- **Needs clarification:** Tony replies with a follow-up question rather than guessing.
- **Access required:** if you try something you're not authorized for, you'll get an "access request sent" reply, and Matt or Nancy can approve with `Approve <you> for <Project>`.
- **Failure:** if Tony encounters an error (API outage, folder not found, etc.), the reply contains the error text. Forward the reply to Matt if the message isn't self-explanatory.

Expected turnaround: most email commands complete within 1–5 minutes (Gmail poll interval + action time). AI video generation is the slow one — typically a few minutes per shot.

---

## Tips

- **Use clear project names.** Tony matches on the `<Project>` token against names in the project registry. Fuzzy matches work, but exact names are safest.
- **One command per email.** Tony dispatches on the subject line; don't stack multiple instructions.
- **Attachments stay in the email.** Tony will pull them out and file them — you don't need to zip or share a link.
- **For file uploads, the `Upload <Project>` subject beats forwarding.** Forwarding works (it's how CC/BCC passive filing runs), but an explicit `Upload` subject gives you a cleaner confirmation.

---

_Sourced from `data/bot_whitelist.json` (whitelist) and `reports/bot_skill_test_matrix.md` (command list + pass/blocked status) in the TonyWorkspace snapshot. Updated 2026-04-20._
