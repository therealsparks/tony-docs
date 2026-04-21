# 2. Publish loop — how dashboards stay fresh

[← architecture index](README.md) · [← docs home](../README.md)

Every ~15 minutes a scheduled task on the server fires a **heartbeat**. Separately, whenever Tony actually does something (gets an email, runs a sync, processes an upload), a `deploy_*.py` script regenerates the affected HTML/JSON and pushes it. This is why the `tony` repo has accumulated 7,000+ commits since it started.

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

**Heartbeat (blue).** A "Tony is alive" signal. Every ~15 minutes a scheduled task on the server runs a small job that writes the current timestamp and known issues into `status.json`, commits it, and pushes. If heartbeats stop arriving in the repo, something is wrong with Tony (or with the server he runs on). This is also what makes [therealsparks.github.io/tony/status-page.html](https://therealsparks.github.io/tony/status-page.html) show a "last seen" time.

**Content updates (green).** Happen whenever Tony does real work. The QuickBooks syncer pulls invoices → regenerates `invoices.html` → pushes. A GA4 pull → regenerates `analytics.html` → pushes. A new project email → updates `projects.json` → regenerates `project-status.html` → pushes. Each of these is a separate `deploy_*.py` script (there are about half a dozen of them).

## Observations about this pattern

- The publish flow is simple and self-contained: there's no CI/CD on the GitHub side (no Actions, no hooks). Everything is driven from the server that pushes.
- The `github-token.txt` PAT is part of the server's `secrets/` directory (not visible to the contractor).
- Because the repo receives a push roughly every 15 minutes, anyone editing the repo directly on GitHub would likely collide with the server's next push.

---

**Prev:** [← Components](01-components.md) · **Next:** [Command loop →](03-command-loop.md)
