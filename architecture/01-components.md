# 1. Components — what talks to what

[← architecture index](README.md) · [← docs home](../README.md)

This is the big-picture map of the Tony system as it runs today. The server is the brain; everything else is either an external service Tony talks to or an output Tony produces.

```mermaid
flowchart LR
    subgraph host["🖥️ Where Tony runs (server)"]
        direction TB
        RT["OpenClaw runtime<br/>Node 24 + llama.cpp"]
        WS["📁 workspace/<br/>AGENTS · SOUL · USER ·<br/>scripts · skills · data"]
        SEC[🔑 secrets/<br/>Dropbox · Gmail · QBO<br/>GitHub PAT · GA4 · AI keys]
        CRON["⏱️ scheduled tasks<br/>heartbeat every 15 min<br/>QBO sync · watchdogs"]
        MEM[(🧠 memory/<br/>runtime state)]

        RT --> WS
        WS <-.reads.-> SEC
        WS <--> MEM
        CRON -->|triggers scripts| WS
    end

    subgraph ext["☁️ External services Tony integrates with"]
        direction TB
        GM["📧 Gmail<br/>tony@ · bot@ · matt@"]
        DB["📦 Dropbox<br/>info@austinvisuals.com"]
        GOOG["📄 Google Drive / Docs / Sheets"]
        QBO["💰 QuickBooks Online"]
        GA[📊 GA4 Analytics]
        AI["🤖 AI APIs<br/>Anthropic · Vertex Veo<br/>Replicate · ElevenLabs"]
        WP["🌐 WordPress<br/>austinvisuals.com"]
    end

    WS <-->|OAuth| GM
    WS <-->|API| DB
    WS <-->|service acct| GOOG
    WS <-->|OAuth| QBO
    WS -->|pull| GA
    WS -->|generate| AI
    WS -->|REST| WP

    WS -->|"git push<br/>(deploy_*.py)"| REPO

    subgraph github["🐙 GitHub"]
        REPO["therealsparks/tony repo<br/>(7,473 commits)"]
        PAGES["GitHub Pages →<br/>therealsparks.github.io/tony/"]
        REPO --> PAGES
    end

    subgraph humans["👥 Consumers"]
        MATT["Matt<br/>views dashboards"]
        TEAM["AV team<br/>nancy · david · tom<br/>adam · peter · che"]
    end

    PAGES -->|dashboards| MATT
    TEAM -->|email commands| GM
    GM -->|confirmations| TEAM

    classDef missing fill:#fee,stroke:#c33,stroke-dasharray:5 5
    class SEC,MEM missing
```

> **🔴 Red-dashed boxes = not visible to the contractor.** `secrets/` and `memory/` live on the server and weren't part of the delivered bundles.

## What each piece is, in plain terms

- **OpenClaw runtime** — The actual Tony engine. A Node.js install that includes the `openclaw` package, `node-llama-cpp`, and SDKs for every service Tony talks to.
- **Workspace** — The folder Tony reads from every session: identity files (who he is), automation scripts (what he does), skills (specialized capabilities), and data (projects, processes, reference).
- **Secrets** — Credentials Tony needs. Stored separately from the workspace so sharing the workspace doesn't leak keys. Not part of what the contractor has been given.
- **Memory** — Tony's notes to himself between sessions. Regenerates on its own.
- **Scheduled tasks** — The host's job scheduler (could be `cron`, `systemd` timers, or Windows Task Scheduler — the contractor hasn't seen the server directly). Fires Tony's periodic jobs: the 15-minute heartbeat, QuickBooks sync, watchdogs.
- **External services** — The seven buckets Tony talks to. He's got OAuth tokens or API keys for each.
- **GitHub repo + Pages** — The publish target. Tony generates HTML/JSON dashboards, pushes them to `therealsparks/tony` on GitHub, and GitHub Pages serves them as a website.

---

**Next:** [Publish loop →](02-publish-loop.md)
