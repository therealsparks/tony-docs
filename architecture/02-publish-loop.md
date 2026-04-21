# 2. Publish loop — how dashboards stay fresh

[← architecture index](README.md) · [← docs home](../README.md)

Every ~15 minutes a scheduled task on the server fires a **heartbeat** commit. Separately, whenever Tony processes an event (inbound email, scheduled sync, upload), a `deploy_*.py` script regenerates the affected HTML and JSON and pushes them. These two mechanisms account for the 7,000+ commits on the repo since 2026-03-21.

```mermaid
sequenceDiagram
    participant C as ⏱️ Scheduled task
    participant T as 🤖 Tony<br/>(OpenClaw runtime)
    participant W as 📁 Workspace<br/>scripts + data
    participant G as 🐙 GitHub<br/>therealsparks/tony
    participant P as 🌐 Pages<br/>therealsparks.github.io/tony
    participant M as 👤 Matt

    rect rgb(240, 248, 255)
    Note over C,G: Heartbeat loop — every ~15 min
    C->>T: trigger heartbeat
    T->>W: write status.json (last_seen, known_issues)
    W->>G: git commit -m "Status heartbeat <ts>Z" & push
    end

    rect rgb(245, 255, 245)
    Note over T,P: Content update loop — on demand
    T->>W: run deploy_analytics.py<br/>(or deploy_status_page.py, deploy_other_pages.py...)
    W->>W: regenerate *.html + *.json from sources<br/>(GA4 pull, projects.json, QBO snapshot)
    W->>G: git commit -m "Update X" & push
    G->>P: GitHub Pages redeploys
    P->>M: Matt sees fresh dashboard
    end
```

## Two independent rhythms

**Heartbeat (blue).** Liveness signal. Every ~15 minutes a scheduled task writes the current timestamp and known issues into `status.json`, commits, and pushes. Missing heartbeats indicate a stalled runtime or host. [therealsparks.github.io/tony/status-page.html](https://therealsparks.github.io/tony/status-page.html) renders the most recent heartbeat as a "last seen" time.

**Content updates (green).** Emitted when a handler or scheduled job changes state. QuickBooks syncs regenerate `invoices.html`; GA4 pulls regenerate `analytics.html`; new-project emails update `projects.json` and regenerate `project-status.html`. Each path has its own `deploy_*.py` script (roughly half a dozen total).

## Notes

- No CI/CD on the GitHub side (no Actions, no hooks). All publish activity is driven by the server's push.
- The `github-token.txt` PAT is part of the server's `secrets/` directory (not visible in the delivered material).
- Because the repo receives a push roughly every 15 minutes, direct edits on GitHub will collide with the server's next push.

---

**Prev:** [← Components](01-components.md) · **Next:** [Command loop →](03-command-loop.md)
